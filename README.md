# opencart-sale-delete
Opencart 3.x sale delete
Bugün sizlere bazı zamanlarda çok aradığımız işlemlerden biri olan Opencart Sipariş Silme nasıl yapılır bu konuda bir yazı ve kod bloğunu paylaşmak istedim.

Opencart sipariş silme işlemi neden gereklidir peki ?
Opencart sipariş silme işlemi genel olarak sipariş iptali sağlayan ve bunları panelde gereksiz görenler için yapılan işlemdir. Genelde gereksiz müşteriler veya sitedeki işleyiş düzenini görmek isteyen ziyaretçiler site üzerinden sipariş vererek, hangi aşamaların olduğunu, hangi yapıların barındığını görmek istemektedirler. Bu gibi durumlarda sipariş sonuçlanmadığı gibi panel üzerinde çok fazla sipariş bulunabiliyor. Bu durumların önüne geçmek için sipariş silme işlemini yapabiliyoruz.

Dikkat!
Bu opencart 3.x üzerinden anlatılmakta olup, işlemlere başlamadan önce mutlaka yedek alınması son derece önemlidir! Yazacağım kodların uygulama kısmındaki sorumluluk tamamen sizlere ait olacağını belirtmek isterim.

işlem adımları
Bu işlem iki adımdan oluşmaktadır.
1. admin üzerinde hiç bir işlem yapmadıysanız dosyaları atabilirsiniz
2. admin üzerinde kendi geliştirmeleriniz var ise düzenleme ile alt kısımda belirteceğim adımlar izleyerek ekleme yapabilirsiniz.

admin > controller > sale > order.php dosyasını açın ve en alt satıra gelin.
public function getOrderDelete(){
        $this->load->model('sale/order');
        if(isset($this->request->post['siparis']))
        {
//            print_r($this->request->post['siparis']);
            foreach ( $this->request->post['siparis'] as $key => $val )
            {
                $qry    =   $this->model_sale_order->getOrderDelete($val);
            }
            $this->response->setOutput(json_encode(['response'=>true]));
        }
    }
2. admin > controller > sale > order.php satır 211 gelin ve $data[‘add’] kısmının altına ekleyin.
$data['sil'] = $this->url->link('sale/order/getOrderDelete', 'user_token=' . $this->session->data['user_token'], true);
3. admin > model > sale > order.php
public function getOrderDelete($order_id){
        $sql    =   "DELETE oo FROM ". DB_PREFIX ."order as oo JOIN ". DB_PREFIX ."order_history as ooh JOIN ". DB_PREFIX ."order_product as oop ";
        $sql    .=  "ON oo.order_id = oop.order_id JOIN ". DB_PREFIX ."order_total as oot ON oo.order_id = oot.order_id";
        $sql    .=  " WHERE oo.order_id =".$order_id;
        if($this->db->query($sql)) {
            return true;
        }
    }
    
4. admin > view > template > sale > order_list.twig dosyasını açın ve 10. satıra ekleyin

<a href="{{ sil }}" id="siparisSil" data-toggle="tooltip" title="Seçili Siparişleri Sil" class="btn btn-danger"><i class="fa fa-trash-o"></i></a> </div>
5. admin > view > template > sale > order_list.twig dosyanın en son satırındaki script kodlarınızın arasında alttaki kodları ekleyin.

$('a#siparisSil').on('click',function(){
    if($('.table-responsive').find('input[type=checkbox]:checked').length == 0)
    {
        alert('Silme yapabilmek için seçim yapmalısınız!');
        return false;
    }
    var _sip    =   [];
    $('.table-responsive').find('input[type=checkbox]').each(function(index,val){
        // console.log(val)
        if($(val).prop('checked'))
        {
            if($(val).val() != "on")
                _sip.push(parseInt($(val).val()));
        }
    });
    console.warn(_sip);
    $.ajax({
        type:'post',
        url: $(this).attr('href'),
        data: { siparis:_sip },
        success: function(suc)
        {
            _parse = JSON.parse(suc);
            console.log(suc.response);
            if(_parse.response)
            {
                location.reload();
            }
        },
        error:function(err){
            console.warn(err);
        }
    });
    return false;
});
Opencart Sipariş Silme
Opencart Sipariş Silme
Yukarıdaki işlemleri sırası ile takip etmenizin ardından sorun yaşamadan silme işlemini tamamlamış olacaksınız.
projenin kaynak kodları ile beraber github sayfamda paylaşıyor olacağım. Değişiklik ve gelişltirmelerde ekleme yaparsanız tekrar kontrol edip gelişmeleride sizinle paylaşacağım.

Projenin web sayfası linkine ulaşmak için ise. http://dokuman.kurtulusoz.com.tr/2018/opencart-siparis-silme/ bakabilirsiniz.
