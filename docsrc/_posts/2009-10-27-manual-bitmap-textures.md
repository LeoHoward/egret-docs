---
layout: post
title:  "纹理集的使用"
permalink: post/manual/bitmap/textures.html
type: manual
element: manualtexture
version: Egret引擎 v1.x
---

纹理集听上去名称高大上，实则概念非常简单。纹理集实际上就是将一些零碎的小图放到一张大图当中。游戏中也经常使用到纹理集。

使用纹理集的好处很多，我们通过将大量的图片拼合为一张图片从而减少网络请求，原先加载数次的图片资源现在加载一次即可。
同时，在引擎渲染的时候也会较少IO读取，从而提高性能。

Egret内置了纹理集的支持，在编写代码之前，我们需要先制作一张纹理集，具体使用的工具可以选择业内比较流行的 TexturePacker。
具体使用方法请参考**<a href="{{site.baseurl}}/post/tools/othertools/texturepacker.html" target="_blank">TexturePacker</a>**。

我们首先制作一张纹理集，拼合后的效果如下：

![img]({{site.baseurl}}/assets/img/bitmaptextures1.png)

同时生成的对应的json文件如下：

{% highlight json linenos %}
{
    "file": "dogs.png",
    "frames": {
        "dog1": {
            "x": 322,
            "y": 2,
            "w": 184,
            "h": 222
        },
        "dog2": {
            "x": 307,
            "y": 226,
            "w": 147,
            "h": 154
        },
        "dog3": {
            "x": 2,
            "y": 2,
            "w": 318,
            "h": 217
        },
        "dog4": {
            "x": 2,
            "y": 393,
            "w": 298,
            "h": 201
        },
        "dog5": {
            "x": 2,
            "y": 221,
            "w": 303,
            "h": 170
        },
        "dog6": {
            "x": 2,
            "y": 596,
            "w": 245,
            "h": 125
        }
    }
}
{% endhighlight %}

然后我们将资源文件拷贝到项目文件夹中的 `resource/assets/` 目录下，同时我们修改资源配置文件 `resource.json` 。

资源配置文件内容如下：

{% highlight json linenos %}
{
"resources":
	[
		{"name":"dogs","type":"sheet","url":"assets/dogs.json"}
	],

"groups":
	[
        {"name":"preload","keys":"dogs"}
	]
}
{% endhighlight %}

然后我们来编写代码：


{% highlight java linenos %}
class BitmapTest extends egret.DisplayObjectContainer{
    public constructor()
    {
        super();
        this.addEventListener(egret.Event.ADDED_TO_STAGE,this.onAddToStage,this);
    }

    private onAddToStage(event:egret.Event) {
        RES.addEventListener(RES.ResourceEvent.GROUP_COMPLETE, this.onGroupComp, this);
        RES.loadConfig("resource/resource.json", "resource/");
        RES.loadGroup("preload");
    }
    private onGroupComp()
    {
        var imgs:egret.SpriteSheet = RES.getRes("dogs");

        var img:egret.Bitmap = new egret.Bitmap();
        img.texture = imgs.getTexture("dog2");
        this.addChild(img);
    }
}
{% endhighlight %}

我们注意其中一行

{% highlight java linenos %}
var imgs:egret.SpriteSheet = RES.getRes("dogs");
{% endhighlight %}

这一行代码中，我们创建一个 `SpriteSheet` 类型的对象，该对象中存放着我们的纹理集数据，也包含我们的图片数据。
当需要获取其中一张图片时候，我们可以使用 

{% highlight java linenos %}
SpriteSheet.getTexture("id");
{% endhighlight %}

通过设置其中的ID，我们就可以获取对应的图片资源。

编译后运行，效果如图：

![img]({{site.baseurl}}/assets/img/bitmaptextures2.png)