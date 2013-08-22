windowAlert
===========

                                          jquery弹窗组件


 1 fileoverview WindowAlert。
 
 2 @desc jQuery弹窗组件。
 
 3 @param {clickElem} 点击元素。
 
 4 @param {container} 弹窗窗口最外层容器。
 
 5 默认配置参数如下：{
	 closed: '.closed', //关闭按钮
	 bgColor: '#000',   //默认颜色
	 opacity: '0.5',    // 默认透明度
	 position: {
		x: 0, //默认情况下居中 
		y: 0
	 },
	 isScroll: true, //默认情况下 窗口随着滚动而滚动 
	 isResize: true  // 默认情况下 随着窗口缩放而缩放
 }
 
 6 @callback 回调函数  
 
 
 var WindowAlert = (function(){
	function WindowAlert(clickElem,container,config,callback){
		var self = this,
			isIE6 = navigator.userAgent.match(/MSIE 6.0/)!= null;
		self.isIE6 = isIE6;
		if(!(self instanceof WindowAlert)){
			return new WindowAlert(clickElem,container,config,callback);
		}
		

		// 匹配传参数clickElem
		if($.isPlainObject(clickElem)){
			self.clickElem = clickElem;
		}else if(/^\./.test(clickElem)){
			self.clickElem = $(clickElem);
		}else if(/^#/.test(clickElem)){
			self.clickElem = $(clickElem);
		}else if($('.'+clickElem)){
			self.clickElem = $('.'+clickElem);
		}else {
			throw new Error('传递参数不符合!');
		}

		// 匹配传参数container
		if($.isPlainObject(container)){
			self.container = container;
		}else if(/^\./.test(container)){
			self.container = $(container);
		}else if(/^#/.test(container)){
			self.container = $(container);
		}else if($('.'+container)){
			self.container = $('.'+container);
		}else {
			throw new Error('传递参数不符合!');
		}
		config = $.extend(WindowAlert.Config,config || {});
		self.config = config || {};
		self._init();

		// 显示后 回调
		callback && callback();

		// 点击关闭按钮
		$(self.config.closed).bind('click',function(){
			self.hide();
			$('.kissy-mask').hide();

			if(true === isIE6) {
				$('body').css({'filter':'','opacity':'','background-color':'',
							'position':'','top':'','left':'','z-index':''});
			}
			
		});

		// 窗口缩放
		$(window).bind('resize',function(){
			t && clearTimeout(t);
			var t = setTimeout(function(){
				if(!$(self.container).hasClass('hidden') && self.config.isResize){
					self._showDialog(self.container);
				}
			},200);
		});

		// 窗口滚动
		$(window).bind('scroll',function(){
			t && clearTimeout(t);
			var t = setTimeout(function(){
				if(!$(self.container).hasClass('hidden') && self.config.isScroll){
					self._showDialog(self.container);
				}
			},200);
			
		});
    }
	// 默认配置参数
	
	WindowAlert.Config = {
		closed: '.closed', //关闭按钮
		bgColor: '#000',   //默认颜色
		opacity: '0.5',    // 默认透明度
		position: {
			x: 0, //默认情况下居中 
			y: 0
		},
		isScroll: true, //默认情况下 窗口随着滚动而滚动 
		isResize: true  // 默认情况下 随着窗口缩放而缩放
		
	};
	
	WindowAlert.prototype = {
		
		_init: function(){
			var self = this;
			
			$(self.clickElem).each(function(index,item){
				$(item).bind('click',function(){

					if(!self.flag){
						var div = $('<div class="kissy-mask"></div>');
						$('body').prepend(div);
						flag = true;
					}
					
					if(false === self.isIE6) {
						$('.kissy-mask').css({'opacity':self.config.opacity,'background-color':self.config.bgColor,
							'position':'fixed','top':0,'left':0,'width':'100%','height':'100%','z-index':1}); 
					}else {
						/**$('body').css({'opacity':self.config.opacity,'background-color':self.config.bgColor,
							'position':'absolute','top':0,'left':0,'z-index':1}); **/

						/* IE6不支持fixed 所以对IE6做特殊的处理下 就空白背景*/
						$('body').css({'position':'absolute','top':0,'left':0,'z-index':1});
					}

					self.show();
					self._showDialog(self.container);
				});
			})
			
		},

		flag: false,

		_showDialog: function(container){
			
			var self = this;
			$(container).css({'position':'absolute','z-index':99999});
			var offsetTop = Math.floor(($(window).height() - $(self.container).height())/2) + $(document).scrollTop(),
				offsetLeft = Math.floor(($(window).width() - $(self.container).width())/2) + $(document).scrollLeft();
			
			// 判断x,y位置默认是不是等于0 如是的话 居中 否则 根据传进来的位置重新定位
			if(0 === self.config.position.x && 0 === self.config.position.y){

				$(container).offset({'top':offsetTop, 'left':offsetLeft});
			}else{

				$(container).offset({'top':self.config.position.y,'left':self.config.position.x});
			}
			
		},
		hide: function(){
			var self = this;
			if(!$(self.container).hasClass('hidden')){
				$(self.container).addClass('hidden');
			}
		},
		show: function(){
			var self = this;
			if($(self.container).hasClass('hidden')){
				$(self.container).removeClass('hidden');
			}
		},
		
		// 销毁
		destory: function(){
			var self = this;
			$(self.clickElem).each(function(index,item){
				$(item).unbind('click');
			});
			self.clickElem = [];
			self.container = [];
		}
	};
	return WindowAlert;
})();

 
