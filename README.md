27岁离婚财务D奶姐姐的背景故事介绍

需求

（1）当用户在输入框输入文字后，实时获取输入框的值，并ajax请求数据库获取对应的数据。

（2）输入框支持动态添加，并能够获取动态输入框的值。
二、解决方案

使用autocomplete实现文字自动补全，同时配合使用输入框监听事件（如果不使用监听事件，动态添加的输入框就获取不到输入的值）。
三、效果图

输入文字后自动请求后端，后端返回数据（json格式），点击出现的下拉框选中并显示，点击”-“号删除当前输入框。

四、代码
1、html代码

    <head>
      <link rel="stylesheet" href="https://code.jquery.com/ui/1.12.1/themes/base/jquery-ui.css">
      <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
      <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.min.js"></script>
    </head>
     
    <div class="addform" style="width:95%; margin:0 auto; height:auto; background:#fff;">
      
      <form class="layui-form" id="form1">
        <div class="xhzy" style="width:95%;height:0.7rem; margin:0.1rem auto;text-align:center;">
          <div style="width:88%;height:.75rem; float:left;border-radius:6px 0 0 6px;border:1px solid #ababab;box-sizing:border-box; background:#fff">  
            <div class="yysb_ks" onclick="yysb(1)" data-num='1' style="width:10%;height:.75rem;float:left;"><i class="fa fa-microphone" style="font-size:0.36rem;line-height:0.75rem;color:#888;"></i></div>
            <input data-input_num='1'  class="input-text input_text_1" id="drugname" name='drug_name' style="width:89%;height:100%;box-sizing:border-box;border:none;border-radius:6px;font-size:0.28rem"  placeholder="输入相互作用药品/成分名称">
          </div>
          <div class="add" style="width:12%;height:.75rem;border:1px solid #3385ff;float:left;line-height:.75rem;border-radius:0px 6px 6px 0px;background:#0066cc;color:#fff;font-size:0.32rem; box-sizing: border-box;">
            <i class="fa fa-plus" aria-hidden="true" style="font-size:0.36rem;color:#fff"></i>
          </div>   
        </div>
      </form>
      <form class="layui-form" id="form2">
        <div class="xhzy" style="width:95%;height:0.7rem; margin:0.4rem auto;text-align:center;">
          <div style="width:88%;height:.75rem; float:left;border-radius:6px 0 0 6px;border:1px solid #ababab;box-sizing:border-box; background:#fff">  
            <div class="yysb_ks" onclick="yysb(2)" data-num='2' style="width:10%;height:.75rem;float:left;"><i class="fa fa-microphone" style="font-size:0.36rem;line-height:0.75rem;color:#888;"></i></div>
            <input data-input_num='2' class="input-text input_text_2" id="drugname" name='drug_name' style="width:89%;height:100%;box-sizing:border-box;border:none;border-radius:6px;font-size:0.28rem"  placeholder="输入相互作用药品/成分名称">
          </div>
          <div style="width:12%;height:.75rem;border:1px solid #3385ff;float:left;line-height:.75rem;border-radius:0px 6px 6px 0px;background:#3385ff;color:#fff;font-size:0.32rem; box-sizing: border-box;" onclick='delForm(2)'>
            <i class="fa fa-minus" aria-hidden="true" style="font-size:0.36rem;color:#fff"></i>
          </div>   
        </div>
      </form>
      
    </div>

html

2、js代码

    // 追加输入框
    var num = 3;  // 初始化form id 从2开始
    layui.use(['form'], function(){
      var form = layui.form
      ,layer = layui.layer;
      
      $('.add').click(function(){
       
        var drug_name = $('input[name="drug_name"]').val();
        if (drug_name == '') {
          layer.msg('搜索内容不能为空', function(){ });
          return false
        }
        var html = "<form class='layui-form' id='form" + num + "'>\
                      <div class='xhzy' style='width:95%;height:0.7rem; margin:0.4rem auto;text-align:center;'>\
                        <div style='width:88%;height:.75rem; float:left;border-radius:6px 0 0 6px;border:1px solid #ababab;box-sizing:border-box; background:#fff'>  \
                          <div class='yysb_ks' onclick='yysb("+num+")' data-num='"+num+"' style='width:10%;height:.75rem;float:left;'><i class='fa fa-microphone' style='font-size:0.36rem;line-height:0.75rem;color:#888;'></i></div>\
                          <input data-input_num='"+num+"'  class='input-text input_text_"+num+"' id='drugname' name='drug_name' style='width:89%;height:100%;box-sizing:border-box;border:none;border-radius:6px;font-size:0.28rem'  placeholder='输入相互作用药品/成分名称'>\
                        </div>\
                        <div style='width:12%;height:.75rem;border:1px solid #3385ff;float:left;line-height:.75rem;border-radius:0px 6px 6px 0px;background:#3385ff;color:#fff;font-size:0.32rem; box-sizing: border-box;' onclick='delForm("+num+")'>\
                          <i class='fa fa-minus' aria-hidden='true' style='font-size:0.36rem;color:#fff'></i>\
                        </div>   \
                      </div>\
                    </form>"
       
        // 检测动态表单是否为空
        if (checkform() == false) {
          
           console.log("form id："+ num)
     
          $(".addform").append(html);   //追加html
          num += 1;
          form.render();  //重新刷新表单
        }
        
      })
     
      //////////////文字补全//////////////
    $(function() {
      
      document.addEventListener('input', function(e) {
        if (e.target.classList.contains('input-text')) {
          const inputNum = e.target.getAttribute('data-input_num');
          $('.input_text_'+inputNum).autocomplete({
            source:function(request,response){
     
              console.log(`Input Number: ${inputNum}, Value: ${e.target.value}`);
              
              var name = e.target.value
              $.ajax({
                type:"POST",
                url:"/custom/xhzy/lx",
                dataType : "json",
                cache : false,
                async : false,
                data : 'name='+name,
                success : function(json) {
                  var json_data = json['data'];
                  var eval_data = eval (json_data);//json数组
                  response($.map(eval_data,function(item){  
                    return {
                      label:item,//下拉框显示值 label:item.drugwhole
                      value:item,//选中后，填充到input框的值
                      //id:item.id//选中后，填充到id里面的值
                    }
                  }));
                }
              });
            },
          })
        }
      });
    })

javascript
运行

