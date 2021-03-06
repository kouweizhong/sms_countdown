jQuery插件之短信发送倒计时功能
====

实现的主要功能如下：
----
1.点击按钮的时候，可以进行倒计时，倒计时自定义。

2.当接收短信失败后，倒计时停止，可点击重新发送短信。

3.点击的元素支持一般标签和input标签。

html代码
----
```
<input type="button" class="sameBtn btnCur" value="发送验证码"/>
<div class="sameBtn btnCur2">发送验证码</div>
```
js代码
---
```

//短信倒计时功能
/**使用方式如下：
 * $(".btnCur").CountDownF({
 *		time:120,
 * 		resetWords:'重新发送', //文字定义 
 *		cnSeconds:'s',//倒计时中显示中文的秒，还是s
 *		clickClass:'current', //点击后添加的class类
 *	    countState:true,
 *		callback:function(){
 *			setTimeout(function(){
 *              //当发送失败后，可以立即清除倒计时与其状态
 *				$(".btnCur").CountDownF('clearState');
 *			},3000);
 *		}
 *	});
 * 
 * */

;(function($,window,document,undefined){
	var pluginName = 'CountDownF',
	defaluts = {
		time:120,
		resetWords:'重新发送', //文字定义
		cnSeconds:'s',//倒计时中显示中文的秒，还是s
		clickClass:'current', //点击后添加的class类
		countState:true //是否可以倒计时，true可以倒计时，false不能进行倒计时
	}
	function Count(element,options){
		this.element = element;
		this.$element = $(this.element);
		this.state = true;
		this.settings = $.extend({},defaluts,options);
		this.number = this.settings.time;
		this.init();
	}

	Count.prototype = {
		init:function(){
			var self = this;
			self.$element.on('click',function(){
				if(self.state && self.settings.countState){
					self.state = false;
					if(self.settings.countState){
						self._count();
					}
					if(self.settings.callback){
						self.settings.callback();
					}
				}
				
			});
		},

		//倒计时函数
		_count:function(){
			var self = this;
			if(self.number == 0){
				self._clearInit();
			}else{
				if(self.number < 10){
					//如果当前元素是input，使用val赋值
					this.$element.attr('type') ? this.$element.val('0' + self.number + self.settings.cnSeconds) : this.$element.html('0' + self.number + self.settings.cnSeconds);	
				}else{
					this.$element.attr('type') ? this.$element.val(self.number + self.settings.cnSeconds) : this.$element.html(self.number + self.settings.cnSeconds);
				}
				self.number--;
				this.$element.addClass(self.settings.clickClass);
				self.clearCount = setTimeout(function(){
					self._count();
				},1000);
			}
		},
		
		//修改是否可发送短信验证码倒计时状态
		change:function(state){
			var self = this;
			self.settings.countState = state;
		},
		
		//置为初始状态
		_clearInit:function(){
			var self = this;
			self.$element.removeClass(self.settings.clickClass);
			self.$element.attr('type') ? self.$element.val(self.settings.resetWords) : self.$element.html(self.settings.resetWords); 
			clearTimeout(self.clearCount);
			self.number = self.settings.time;
			self.state = true;
		},

		//清除倒计时进行状态
		clearState:function(){
			var self = this;
			self._clearInit();
		}
	};

	$.fn.CountDownF = function(options){
		var args = arguments;
		if(options === undefined || typeof options ==='object' ){
			return this.each(function(){
				if(!$.data(this,'plugin' + pluginName)){
					$.data(this,'plugin' + pluginName,new Count(this,options));
				}
			});
		}
		else if(typeof options === 'string' && options !== 'init'){
			var returns;
			 this.each(function(){
				var data = $.data(this,'plugin' + pluginName);
				if(data instanceof Count && typeof data[options] === 'function'){
					returns = data[options].apply(data,Array.prototype.slice.call(args,1));
				}
				if(options === 'destory'){
					 $.data(this, 'plugin' + pluginName, null);
				}
			});
			 return returns !== undefined ? returns : this;
		}
		else{
			$.error('Method' + options + 'does not exist on jQuery.CountDownF');
		}
	}

})(jQuery,window,document);


```
调用方式
----
```
$(function(){
    $(".btnCur").CountDownF({
        time:120,
        countState:true,
        callback:function(){
            setTimeout(function(){
                $(".btnCur").CountDownF('clearState');
            },3000);
        }
    });

    $(".btnCur2").CountDownF({
        time:50,
        countState:true,
        cnSeconds:'秒',
        callback:function(){
            setTimeout(function(){
                $(".btnCur2").CountDownF('clearState');
            },5000);
        }
    });
})
```



