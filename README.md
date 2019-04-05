# 图片预加载之无序加载
    效果如下：
![](images/GIF.gif)
    
    基于jquery写的预加载功能插件
```angularjs
/**
 * 图片预加载
 */
(function ($) {
    function Preload(imgs,options) {
        this.imgs = (typeof imgs === "string")?[imgs]:imgs;
        this.opts = $.extend({},Preload.DEFAULTS,options);
        this._unoredered();
    }
    Preload.DEFAULTS = {
        /**
         * 每张图片加载完毕后执行
         */
        each:null,
        /**
         * 所有图片加载完毕后执行
         */
        all:null,
    };
    Preload.prototype._unoredered = function () {
        /**
         * 无序加载
         */
        var imgs = this.imgs,
            opts = this.opts,
            count = 0,
            len = imgs.length;
        $.each(imgs,function (i,src) {
            if(typeof src != "string") return;
            var imgObj = new Image();
            $(imgObj).on("load error",function () {
                opts.each && opts.each(count);
                if(count >= len -1){
                    opts.all && opts.all();
                }
                count ++;
            });
            imgObj.src = src;
        });
    };
    $.extend({
        preload:function (imgs,opts) {
            new Preload(imgs,opts)
        }
    })
})(jQuery);
```       
    页面调用
```angularjs
    var imgs = [
        "images/1.jpg",
        "images/2.jpg",
        "images/3.jpg",
        "images/4.jpg",
        "images/5.jpg",
        "images/6.jpg",
        "images/7.jpg"
    ],
        index = 0,
        len = imgs.length,
        $progress = $(".progress");
    $.preload(imgs,{
        each:function (count) {
            $progress.html(Math.round((count + 1)/len * 100) + "%");
        },
        all:function () {
            $(".loading").hide();
            document.title = "1/"+len;
        }
    });
    $(".btn").click(function () {
        if($(this).data("control") === "prev"){
            //上一张
            index = Math.max(0,--index);
        }else {
            index = Math.min(len - 1,++index)
        }
        document.title = (index + 1) + "/" + len;
        $("#img").attr("src",imgs[index]);
    })
```    
    html+css
````angularjs
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>图片预加载之无序加载</title>
    <style>
        html,body{
            width: 100%;
            height: 100%;
        }
        a{
            text-decoration: none;
        }
        .box{
            text-align: center;
        }
        .btn{
            display: inline-block;
            height: 30px;
            line-height: 30px;
            border:1px solid #ccc;
            background-color:#fff;
            padding:0 10px;
            margin-right: 50px;
            color:#333;
        }
        .btn:hover{
            background: #eee;
        }
        img{
            width: 300px;
            height: 200px;
        }
        .loading{
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: #eeeeee;
            text-align: center;
            font-size: 30px;
        }
        .progress{
            margin-top: 30px;
        }
    </style>
</head>
<body>
<div class="box">
    <img src="images/1.jpg" alt="" id="img">
    <p>
        <a href="javascript:" class="btn" data-control="prev">上一页</a>
        <a href="javascript:" class="btn" data-control="next">下一页</a>
    </p>
</div>
<div class="loading">
    <p class="progress">0%</p>
</div>
<script src="js/jquery-3.3.1.js"></script>
<script src="js/preload.js"></script>
</body>
</html>
````    