# javascript
## Object Prototypes (对象原型)
### 1.定义一个方法，传入一个string类型的参数，然后将string的每个字符间加个空格返回，例如：

    spacify('hello world') // => 'h e l l o  w o r l d'

下面是正确答案。

    function spacify(str) {
      return str.split('').join(' ');
    }
### 2.如何把这个方法放入String对象上面，例如：

    'hello world'.spacify();
答案可能会像这样：

    String.prototype.spacify = function(){
      return this.split('').join(' ');
    };
### 3.解释一下函数声明和函数表达式的区别。
## 参数 arguments

### 4.定义一个未定义的log方法。

    log('hello world')
定义log，然后它可以代理console.log的方法。

    function log(msg)　{
      console.log(msg);
    }
### 5.改变调用log的方式，传入多个参数。强调传入参数的个数是不定的，可不止两个。

    log('hello', 'world');
答案是直接使用apply。

    function log(){
      console.log.apply(console, arguments);
    };
### 6.每一个log消息添加一个"(app)"的前辍，比如：

    '(app) hello world'
需要知道arugments是一个伪数组，然后会将它转化成为标准数组。通常方法是使用Array.prototype.slice，像这样：

    function log(){
      var args = Array.prototype.slice.call(arguments);
      args.unshift('(app)');

      console.log.apply(console, args);
    };
## 上下文(context)

### 考察对上下文和this的理解。

var User = {
  count: 1,

  getCount: function() {
    return this.count;
  }
};
加上下面几行，然后问log输出的会是什么。

    console.log(User.getCount());

    var func = User.getCount;
    console.log(func());
这种情况下，正确的答案是1和undefined。
func是在winodw的上下文中被执行的，所以会访问不到count属性。
### 怎么样保证User总是能访问到func的上下文，即返回正即的值：1

正确的答案是使用Function.prototype.bind，例如：

    var func = User.getCount.bind(User);
    console.log(func());
### 这个方法对老版本的浏览器不起作用，如何解决

    Function.prototype.bind = Function.prototype.bind || function(context){
      var self = this;

      return function(){
        return self.apply(context, arguments);
      };
    }

# css

# html

# 综合

## 弹出窗口（Overlay library）

通过做一个‘弹出窗口’的库。

在遮罩中最好使用position中的fixed代替absolute属性，这样即使在滚动的时侯，
也能始终让遮罩始盖住整个窗口。

    .overlay {
      position: fixed;
      left: 0;
      right: 0;
      bottom: 0;
      top: 0;
      background: rgba(0,0,0,.8);
    }
如何让里面的内容居中也是需要考察的一点。
如果选择CSS和绝对定位，如果内容有固定的宽、高这是可行的。否则就要使用JavaScript.

    .overlay article {
      position: absolute;
      left: 50%;
      top: 50%;
      margin: -200px 0 0 -200px;
      width: 400px;
      height: 400px;
    }
确保当遮罩被点击时要自动关闭，这会很好地考查事件冒泡机制的机会。
通常会在overlay上面直接绑定一个点击关闭的方法。

    $('.overlay').click(closeOverlay);
这是个方法，不过直到你认识到点击窗口里面的东西也会关闭overlay的时侯——这明显是个BUG。
解决方法是检查事件的触发对象和绑定对象是否一致，从而确定事件不是从子元素里面冒上来的，就像这样：

    $('.overlay').click(function(e){
      if (e.target == e.currentTarget)
        closeOverlay();
    });