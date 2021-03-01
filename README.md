# opencart-sale-delete
Opencart 3.x sale delete
Today, I wanted to share an article and a block of code on how to do Opencart Order Deletion, which is one of the operations that we are looking for at some times.

Why is Opencart order deletion necessary?
Opencart order deletion is the process that generally provides order cancellation and is performed for those who see them unnecessary in the panel. Generally, unnecessary customers or visitors who want to see the functioning of the site want to see what stages are and which structures are hosted by placing an order on the site. In such cases, the order is not finalized and there are too many orders on the panel. In order to avoid these situations, we can delete orders.

Attention!
This is explained on opencart 3.x and it is extremely important to make a backup before starting operations! I would like to state that the responsibility in the application part of the codes I will write will belong to you completely.

process steps
This process consists of two steps.
1. If you have not done any action on the admin, you can transfer the files
2. If you have your own improvements on admin, you can edit and add by following the steps I will specify at the bottom.

Open admin> controller> sale> order.php and go to the bottom line.
public function getOrderDelete () {
        $ this-> load-> model ('sale / order');
        if (isset ($ this-> request-> post ('order')))
        {
// print_r ($ this-> request-> post ['order']);
            foreach ($ this-> request-> post ('order') as $ key => $ val)
            {
                $ qry = $ this-> model_sale_order-> getOrderDelete ($ val);
            }
            $ this-> response-> setOutput (json_encode (['response' => true]));
        }
    }
2. Go to admin> controller> sale> order.php line 211 and add it under $ data ['add'].
$ data ['delete'] = $ this-> url-> link ('sale / order / getOrderDelete', 'user_token ='. $ this-> session-> data ['user_token'], true);
3. admin> model> sale> order.php
public function getOrderDelete ($ order_id) {
        $ sql = "DELETE oo FROM". DB_PREFIX. "Order as oo JOIN". DB_PREFIX. "Order_history as ooh JOIN". DB_PREFIX. "Order_product as oop";
        $ sql. = "ON oo.order_id = oop.order_id JOIN". DB_PREFIX. "Order_total as oot ON oo.order_id = oot.order_id";
        $ sql. = "WHERE oo.order_id =". $ order_id;
        if ($ this-> db-> query ($ sql)) (
            return true;
        }
    }
    
4. Open admin> view> template> sale> order_list.twig and add it to line 10

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
Opencart Order Deletion
Opencart Order Deletion
After you follow the above processes in order, you will have completed the deletion without any problems.
I will be sharing the source code of the project on my github page. If you add changes and improvements, I will check it again and share it with you.

To reach the web page link of the project. You can see http://dokuman.kurtulusoz.com.tr/2018/opencart-siparis-silme/. 
