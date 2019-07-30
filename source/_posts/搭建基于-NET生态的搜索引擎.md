---
title: 搭建基于.NET生态的聚焦网络爬虫及内网搜索引擎
date: 2019-07-29 09:06:07
categories:
- 随笔
tags:
- .NET
---

# 萌芽

我曾经有段时间是很讨厌网络爬虫这种东西的。它们漫无目的地在互联网上东奔西走，窥探着一切想要被看到的和不想被看到的东西。回想起来，我初中二三年级的时候还会在网络上放一些初中生的无病呻吟和风花雪月的诗词。当时还和班里的才子和真·才女组成了一个诗词同好会之类的东西，每日浸淫于诗赋之间，互相切磋互相吹捧，颇有古时候举人的风采。但哪怕是中二的我也懂得，这些东西大多是见不得人的自娱自乐的产物（和现在如出一辙）。不是说有哪些会伤着人的语句，只是怕自己那稚嫩的文青风格被当作刻意的少年老成——也许我潜意识里确实有这样卖弄的心态——所以很怕被人看到吧。
那时流行一种被我们称作“萌芽体”的作文，是一种肆意引经据典，滥用排比，要么无端伤怀要么故作豪迈的文风，其命名来源于一本叫做《萌芽》的青年杂志，每期千篇一律的都是这种风格的文章，乍一看根本瞧不出这篇和那篇之间的区别，是一本本空洞的屁话。但不幸的是，这些屁话在教师之间很是流行。通过堆砌华丽的辞藻，它就像是要蹦出纸面一样地朝读者嘶吼着它是一件多么优雅的艺术品。时间不充裕的阅卷老师看到这溢出纸面的“文气”，不需要细看文章内容，只凭文章首尾的结构就可以打分，实在是方便。诚然，萌芽体对于作者的文学底蕴还是有很高的要求的，能从唐诗三百首里随手抄出几篇适用的句子的人，其文学功底一定能吊打大部分学生了。且比起初中生贫弱的生活经验，教师们更认同既定成型的诗词和习语这件事虽然令人不爽却也不是不能理解。但这并不能改变萌芽文等同于屁话这一真理。
我的作文虽说也会用些形容比喻，但终究没有跳出以叙事为主抒怀为辅的传统文章的路数。可叙事题材的质与量是和作者的阅历成正比的。应试教育的皮鞭逼着我每两三天就要写一篇作文出来，一个月一个月过去，能写的内容越来越少，写出的内容也大同，成了只为了考试拿分的存在，甚至开始在内容上坑蒙拐骗。要按现在的话说就是失去了写作的灵魂。我明明把写作当成自我的诉求和与大人的对话方式，结果不论内容真假，反应在纸面上的评价并不会有什么区别。我也有想过不如放下自己的矜持，向萌芽体妥协。可惜做不到的事情就是做不到，在QQ空间上试水模仿的几篇文章都成了四不像，被毕业后的我光速扫入了青春的垃圾堆。也的确怪我蠢，明明用自己的方式写作就已经能拿学年第一了，为什么非要降维去学写不出实质内容的人用的旁门左道呢？
顺带一提，令人欣慰的是，如今的萌芽体也不再泛滥，其内容回归了《格言》一样的青少年文学，实在是令人欣慰。而不幸的是，我这几年的内心变化也影响到了我的文字，写出来的东西像是变了质的萌芽体，如果没有心境上的变化怕是很难回到当初大开大阖的文风了。
唉怎么又讲了一堆屁话，我明明只是想表达我有一些不想被爬虫看到的内容的。总之，和一切科技一样，爬虫这一概念是不带有善恶的标签的。通用的爬虫，如Google搜索引擎的前端，是不会在乎你我的喜悲的。它们只会遵循着繁复的规则，日复一日地构建互联网的索引。而如果你像我一样多愁善感，你可以编写一个在乎你的感受的爬虫，意即聚焦型网络爬虫。这就是本文的目的了。

# 爬虫

出于实验目的，这次我使用了.NET Core 2.0而不是一些包所声明的.NET 4.6.1。

首先创建一个空的.NET Core控制台程序，然后在NuGet包管理器中安装Abot（[源代码](https://github.com/sjdirect/abot)）。注意不是AbotX，那是Abot的商业版本。
<center>![](/images/201907/1.png)</center>

因为我使用的不是Abot所要求的.NET 4.6.1，我收到了如下的警示，无视。
<center>![](/images/201907/2.png)</center>

## 配置
Abot提供了三种不同的配置方法：配置文件、配置对象、或是两者混用。

### 使用配置文件
此方式适用于.NET及ASP.NET环境下的程序。如果使用.NET Core则必须使用配置类。

在`app.config`或是`web.config`文件中加入如下字段，并进行相应的调整。

{% codeblock lang:XML %}
<configuration>
  <configSections>
    <section name="abot" type="Abot.Core.AbotConfigurationSectionHandler, Abot"/>
  </configSections>

  <abot>
    <crawlBehavior 
      maxConcurrentThreads="10" 
      maxPagesToCrawl="1000" 
      maxPagesToCrawlPerDomain="0" 
      maxPageSizeInBytes="0"
      userAgentString="Mozilla/5.0 (Windows NT 6.3; Trident/7.0; rv:11.0) like Gecko" 
      crawlTimeoutSeconds="0" 
      downloadableContentTypes="text/html, text/plain" 
      isUriRecrawlingEnabled="false" 
      isExternalPageCrawlingEnabled="false" 
      isExternalPageLinksCrawlingEnabled="false"
      httpServicePointConnectionLimit="200"  
      httpRequestTimeoutInSeconds="15" 
      httpRequestMaxAutoRedirects="7" 
      isHttpRequestAutoRedirectsEnabled="true" 
      isHttpRequestAutomaticDecompressionEnabled="false"
      isSendingCookiesEnabled="false"
      isSslCertificateValidationEnabled="false"
      isRespectUrlNamedAnchorOrHashbangEnabled="false"
      minAvailableMemoryRequiredInMb="0"
      maxMemoryUsageInMb="0"
      maxMemoryUsageCacheTimeInSeconds="0"
      maxCrawlDepth="1000"
	  maxLinksPerPage="1000"
      isForcedLinkParsingEnabled="false"
      maxRetryCount="0"
      minRetryDelayInMilliseconds="0"
      />
    <authorization
      isAlwaysLogin="false"
      loginUser=""
      loginPassword="" />	  
    <politeness 
      isRespectRobotsDotTextEnabled="false"
      isRespectMetaRobotsNoFollowEnabled="false"
	  isRespectHttpXRobotsTagHeaderNoFollowEnabled="false"
      isRespectAnchorRelNoFollowEnabled="false"
      isIgnoreRobotsDotTextIfRootDisallowedEnabled="false"
      robotsDotTextUserAgentString="abot"
      maxRobotsDotTextCrawlDelayInSeconds="5" 
      minCrawlDelayPerDomainMilliSeconds="0"/>
    <extensionValues>
      <add key="key1" value="value1"/>
      <add key="key2" value="value2"/>
    </extensionValues>
  </abot>  
</configuration>    
{% endcodeblock %}

### 使用配置对象
创建一个`Abot.Poco.CrawlConfiguration`对象并修改其中的内容:

{% codeblock lang:C# %}
CrawlConfiguration crawlConfig = new CrawlConfiguration
{
    CrawlTimeoutSeconds = 100,
    MaxConcurrentThreads = 10,
    MaxPagesToCrawl = 1000,
};
{% endcodeblock %}

### 配置文件与配置对象混用

{% codeblock lang:C# %}
CrawlConfiguration crawlConfig = AbotConfigurationSectionHandler.LoadFromXml().Convert();
crawlConfig.MaxPagesToCrawl = 0; // 无抓取上限
{% endcodeblock %}

## 上手
新建一个Crawler类，并声明一个`PoliteWebCrawler`对象。在Abot框架中，`PoliteWebCrawler`是一切指令和组件的入口。是的，组件，Abot提供了一套高度可自定义的插件接口，它们都可以通过导入`PoliteWebCrawler`的构造函数来使用。这一点后面会详解。

{% codeblock lang:C# %}
using Abot.Crawler;
using System;

namespace SampleSearchEngine
{
    public class Crawler
    {
        private readonly PoliteWebCrawler _crawler;
        public Uri RootUrl { get; set; } = new Uri("https://github.com/");

        public Crawler()
        {
            _crawler = new PoliteWebCrawler(); // 简单构造
            // _crawler = new PoliteWebCrawler(crawlConfig, null, null, null, null, null, null, null, null); // 复杂构造
        }
    }
}
{% endcodeblock %}

如代码所示，可以看出`PoliteWebCrawler`有简单和复杂的构造函数。在复杂函数的参数全为`null`时两者相同，都是使用Abot默认的构建方法。如果有自定义的插件则可以在调用复杂构造函数时引入，但不能在对象已被创建后替换。

## 自定义爬虫

Abot的爬虫由9个部件组成：配置、决策、线程管理、资源调度、页面请求、超链接解析、内存管理，还有robot.txt文件的处理机制。我认为Abot提供的几个默认组件已经能满足绝大多数用户的需求。出于不想跑题的理由，这里只详细谈一谈最重要的决策器。

### 决策器

这一定是每个写爬虫的人最关心的内容。开发者可以通过这个接口来决定爬哪些网页，抓取哪些内容，放弃哪些嵌套的链接以及何时需要重新抓取页面。

{% codeblock lang:C# 自定义决策逻辑 %}
public class CustomDecisionMaker : CrawlDecisionMaker, ICrawlDecisionMaker
    {
        public CrawlDecision ShouldCrawlPage(PageToCrawl pageToCrawl, CrawlContext crawlContext)
        {
            if (pageToCrawl.Uri.Authority == "google.com")
            {
                return new CrawlDecision { Allow = false, Reason = "爬到Google上不就等于把互联网爬了吗" };
            }
            return new CrawlDecision { Allow = true }; // 默认爬取所有网页
        }

        public CrawlDecision ShouldCrawlPageLinks(CrawledPage crawledPage, CrawlContext crawlContext)
        {
            if (!crawlContext.CrawlConfiguration.IsExternalPageLinksCrawlingEnabled && !crawledPage.IsInternal)
            {
                return new CrawlDecision { Allow = false, Reason = "不爬取外部链接" };
            }
            return new CrawlDecision { Allow = true };
        }

        public CrawlDecision ShouldDownloadPageContent(CrawledPage crawledPage, CrawlContext crawlContext)
        {
            if (crawledPage.HttpWebResponse.StatusCode != HttpStatusCode.OK)
            {
                return new CrawlDecision { Allow = false, Reason = "只爬取返回OK的网页" };
            }
            return new CrawlDecision { Allow = true };
        }

        // 使用默认决策
        //public CrawlDecision ShouldRecrawlPage(CrawledPage crawledPage, CrawlContext crawlContext)
        //{
        //    if (crawledPage.WebException == null)
        //    {
        //        return new CrawlDecision { Allow = false, Reason = "无异常" };
        //    }
        //    return new CrawlDecision { Allow = true };
        //}
    }
{% endcodeblock %}

如果逻辑不复杂，开发者也可以通过植入的方式替换默认的决策逻辑。

{% codeblock lang:C# 植入式修改决策逻辑 %}
crawler.ShouldCrawlPage((pageToCrawl, crawlContext) => 
{
	if (pageToCrawl.Uri.Authority == "google.com")
    {
        return new CrawlDecision { Allow = false, Reason = "爬到Google上不就等于把互联网爬了吗" };
    }
    return new CrawlDecision { Allow = true }; // 默认爬取所有网页
});
{% endcodeblock %}

### 其他的组件
线程管理器负责多线程的逻辑，我认为使用配置参数来调整其行为足以满足大部分的需求了。

资源管理器负责调度已被爬取和将要被爬取的页面的仓库。除非使用分布式的架构否则也不需要调整。

页面请求器负责发送HTTP请求并下载其内容。如果有比较复杂的重定向或用户认证机制便需要重新编写这一类，但如果是常规逻辑则只需要调整配置参数（如使用明文登陆或系统证书等）。

超链接解析器负责获取页面上的所有超链接并进行筛选。和决策器所不同的是，决策器掌管整个页面的决定，而解析器负责页面内的细节。

内存管理负责统筹进程的内存占用，并在空间不够的情况下做出相应的对策。可以配合配置文件里的最大线程参数一起使用。

最后，robot.txt处理器决定是否听从文件的指示，听从哪些等。一般用户使用默认的就可以。

## 注册监听器
