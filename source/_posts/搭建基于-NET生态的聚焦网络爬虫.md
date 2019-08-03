---
title: 搭建基于.NET生态的聚焦网络爬虫
date: 2019-07-29 09:06:07
categories:
- 随笔
tags:
- .NET
---

# 萌芽

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我曾经有段时间是很讨厌网络爬虫这种东西的。它们漫无目的地在互联网上东奔西走，窥探着一切想要被看到的和不想被看到的东西。回想起来，我初中二三年级的时候还会在网络上放一些初中生的无病呻吟和风花雪月的诗词。当时还和班里的才子和真·才女组成了一个诗词同好会之类的东西，每日浸淫于诗赋之间，互相切磋互相吹捧，颇有古时候举人的风采。但哪怕是中二的我也懂得，这些东西大多是见不得人的自娱自乐的产物（和现在如出一辙）。不是说有哪些会伤着人的语句，只是怕自己那稚嫩的文青风格被当作刻意的少年老成——也许我潜意识里确实有这样卖弄的心态——所以很怕被人看到吧。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;那时流行一种被我们称作“萌芽体”的作文，是一种肆意引经据典，滥用排比，要么如黛玉般无端伤怀、要么装成个飞马的武将故作豪迈的文风，其命名来源于一本叫做《萌芽》的青年杂志，每期千篇一律的都是这种风格的文章，乍一看根本瞧不出这篇和那篇之间的区别，全是虚假空洞的屁话。但不幸的是，这些屁话在教师之间很是流行。它们通过堆砌华丽的辞藻，就像是要蹦出纸面一样地朝读者嘶吼着它们是多么优雅的艺术品。时间不充裕的阅卷老师看到这溢出纸面的“文气”，不消细看文章内容，只凭文章首尾的结构就可以打分，实在是方便还挑不出错。诚然，萌芽体对于作者的文学底蕴还是有很高的要求的，能从唐诗三百首里随手抄出几篇适用的句子的人，其文学功底一定能吊打大部分学生了。且比起初中生贫弱的生活经验，教师们更认同既定成型的诗词和习语这件事虽然令人不爽却也不是不能理解。但这并不能改变萌芽文等同于屁话这一真理。连散文都要借景抒情，言之有物。你一个萌芽体算老几？

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我的作文虽说也会用些形容比喻，但终究没有跳出以叙事为主抒怀为辅的传统文章的路数。众所周知，叙事题材的质与量是和作者的阅历成正比的。应试教育的皮鞭逼着我每两三天就要写一篇作文出来，一个月一个月过去，我能写的内容越来越少，写出的内容也大同，成了只为了考试拿分的存在。后来我甚至开始在内容上胡编滥造，要按现在的话说就是失去了写作的灵魂。我明明把写作当成自我的诉求和与大人的对话方式，结果不论内容真假，反应在纸面上的评价并不会有什么区别。我也有想过不如放下自己的矜持，向萌芽体妥协。可惜做不到的事情就是做不到，在QQ空间上试水模仿的几篇文章都成了四不像，越看越恶心，被毕业后的我光速扫入了青春的垃圾堆。终于我醒悟了，明明用自己的方式写作就已经能拿学年第一了，为什么非要降维去学写不出实质内容的人用的旁门左道？既然自己这一路走来并无犯错，何必抛弃自己的风格呢？

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;顺带一提，如今的《萌芽》上萌芽体也不再泛滥，其内容回归《格言》之流的青少年文学，实在是令人欣慰。而不幸的是，我这几年的内心变化影响到了我的文字，写出来的东西像是变了质的萌芽体，成了旁门左道中的旁门左道。如果没有心境上的变化怕是很难回到当初大开大阖的文风了。那句话怎么说的来着：人终于会变成他们最讨厌的存在。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;唉怎么又讲了一堆屁话，我明明只是想表达我有一些不想被爬虫看到的内容的。总之，和一切科技一样，爬虫这一概念是不带有善恶的标签的。通用的爬虫，如Google搜索引擎的前端，是不会在乎你我的喜悲的。它们只会遵循着繁复的规则，日复一日地构建互联网的索引。而如果你像我一样多愁善感，你可以编写一个在乎你的感受的爬虫，意即聚焦型网络爬虫。而这这就是本文的目的了。

# 爬虫
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我选择使用.NET生态中人气最旺的Abot来完成这项任务。Abot爬虫由许多个小型的组件组成，拥有极高的可插拔性和可延伸性。它还使用了大量惰性初始化，最大化去掉了多余的运算，因此它的运行速度很快。可惜的是，由于维护人手的不足，Abot目前是不支持.NET Standard的。但如果不嫌麻烦的话稍微修改源代码再编译应该也不会很麻烦。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;首先创建一个空的.NET Framework控制台程序，然后在NuGet包管理器中安装Abot（[源代码](https://github.com/sjdirect/abot)）。注意不是AbotX，那是Abot的商业版本。
<center>![](/images/201907/1.png)</center>

## 配置
Abot提供了三种不同的配置方法：配置文件、配置对象、或是两者混用。

### 使用配置文件
此方式适用于.NET及ASP.NET环境下的程序。

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
创建一个`Abot.Poco.CrawlConfiguration`对象并修改其中的内容：

{% codeblock lang:csharp %}
CrawlConfiguration crawlConfig = new CrawlConfiguration
{
  CrawlTimeoutSeconds = 100,
  MaxConcurrentThreads = 10,
  MaxPagesToCrawl = 1000,
};
{% endcodeblock %}

### 配置文件与配置对象混用
{% codeblock lang:csharp %}
CrawlConfiguration crawlConfig = AbotConfigurationSectionHandler.LoadFromXml().Convert();
crawlConfig.MaxPagesToCrawl = 0; // 无抓取上限
{% endcodeblock %}

## 上手
新建一个Crawler类，并声明一个`PoliteWebCrawler`对象。在Abot框架中，`PoliteWebCrawler`是一切指令和组件的入口。是的，组件，Abot提供了一套高度可自定义的插件接口，它们都可以通过导入`PoliteWebCrawler`的构造函数来使用。这一点后面会详解。

{% codeblock lang:csharp %}
using Abot.Crawler;
using System;

namespace SampleSearchEngine
{
  public class Crawler
  {
    private readonly PoliteWebCrawler _crawler;
    public Uri RootUrl { get; set; } = new Uri("https://github.com/");
    // 储存结果的容器，SitePage包含标题、URL、内容等页面基本元素
    public Dictionary<string, SitePage> Pages { get; private set; } = new Dictionary<string, SitePage>();
    private int _totalPages; // 统计所有找到的页面
    private int _pagesCrawled; // 统计爬过的页面

    public Crawler()
    {
      _crawler = new PoliteWebCrawler(); // 简单构造
      // _crawler = new PoliteWebCrawler(crawlConfig, null, null, null, null, null, null, null, null); // 复杂构造
    }
  }

  [Serializable]
  public class SitePage
  {
    public string Url { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }
  }
}
{% endcodeblock %}

如代码所示，可以看出`PoliteWebCrawler`有简单和复杂的构造函数。在复杂函数的参数全为`null`时两者相同，都是使用Abot默认的构建方法。如果有自定义的插件则可以在调用复杂构造函数时引入，但不能在对象已被创建后替换。

## 自定义爬虫

Abot的爬虫由9个部件组成：配置、决策、线程管理、资源调度、页面请求、超链接解析、内存管理，还有robot.txt文件的处理机制。我认为Abot提供的几个默认组件已经能满足绝大多数用户的需求。出于不想跑题的理由，这里只详细谈一谈最重要的决策器。

### 决策器

这一定是每个写爬虫的人最关心的内容之一。开发者可以通过这个接口来决定爬哪些网页，抓取哪些内容，放弃哪些嵌套的链接以及何时需要重新抓取页面。

{% codeblock lang:csharp 自定义决策逻辑 %}
public class CustomDecisionMaker : CrawlDecisionMaker, ICrawlDecisionMaker
{
  public CrawlDecision ShouldCrawlPage(PageToCrawl pageToCrawl, CrawlContext crawlContext)
  {
    if (pageToCrawl.Uri.Authority == "google.com")
    {
        return new CrawlDecision { Allow = false, Reason = "不爬取没用的" };
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
  //  if (crawledPage.WebException == null)
  //  {
  //    return new CrawlDecision { Allow = false, Reason = "无异常" };
  //  }
  //  return new CrawlDecision { Allow = true };
  //}
}
{% endcodeblock %}

如果逻辑不复杂，开发者也可以通过植入的方式替换默认的决策逻辑。

{% codeblock lang:csharp 植入式修改决策逻辑 %}
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
`PoliteWebCrawler`会监听5种不同的事件，每种监听又有同步和异步两种模式。如何处理这些事件是业务逻辑中最关键的地方。因此，关于爬虫的绝大部分逻辑都会嵌套进这些监听器中。

{% codeblock lang:csharp 监听异步事件 %}
_crawler.PageCrawlStartingAsync += Crawler_ProcessPageCrawlStarting;
_crawler.PageCrawlCompletedAsync += Crawler_ProcessPageCrawlCompleted;
_crawler.PageCrawlDisallowedAsync += Crawler_PageCrawlDisallowed;
_crawler.PageLinksCrawlDisallowedAsync += Crawler_PageLinksCrawlDisallowed;
_crawler.RobotsDotTextParseCompletedAsync += Crawler_RobotsDotTextParseCompleted;
{% endcodeblock %}


### 处理页面前
这一事件触发于从资源仓库提取之后，处理页面之前。一些记录、页面的预处理可以在这一步进行。

{% codeblock lang:csharp %}
private void Crawler_ProcessPageCrawlStarting(object sender, PageCrawlStartingArgs e)
{
  _totalPages++;
  PageToCrawl pageToCrawl = e.PageToCrawl;
  _logger.Info($"Crawling {pageToCrawl.Uri.AbsoluteUri} which was found on page {pageToCrawl.ParentUri.AbsoluteUri}...");
}
{% endcodeblock %}

### 处理页面后
这一事件触发于处理页面之后，从仓库中提取下一页面之前。爬虫的主要逻辑都应该放在这里。

{% codeblock lang:csharp %}
private void Crawler_ProcessPageCrawlCompleted(object sender, PageCrawlCompletedArgs e)
{
  CrawledPage crawledPage = e.CrawledPage;

  if (crawledPage.WebException != null || crawledPage.HttpWebResponse.StatusCode != HttpStatusCode.OK)
  {
    _logger.Warn(crawledPage.WebException,
    $"Crawl failed for {crawledPage.Uri.AbsoluteUri}, the host returned {crawledPage.HttpWebResponse.StatusCode} status code.");
  }
  else
  {
    _logger.Info($"Crawl of page succeeded {crawledPage.Uri.AbsoluteUri}");
  }
  if (string.IsNullOrEmpty(crawledPage.Content.Text))
  {
    _logger.Info($"Page had no content {crawledPage.Uri.AbsoluteUri}");
  }

  // Use AngleSharp HTML parser.
  var document = crawledPage.AngleSharpHtmlDocument;
  if (!Pages.ContainsKey(crawledPage.Uri.AbsoluteUri))
  {
    // Store page information
    Pages.Add(crawledPage.Uri.AbsoluteUri, new SitePage
    {
      Url = crawledPage.Uri.AbsoluteUri,
      Title = document.Title,
      Content = document.TextContent, // 进行一些自定义的页面处理
    });
    _pagesCrawled++;
  }
}
{% endcodeblock %}

### 禁止爬取页面
这一事件触发于被禁止获取页面内容后，可以配合robot.txt处理机制和决定器一起使用。

{% codeblock lang:csharp %}
private void Crawler_PageCrawlDisallowed(object sender, PageCrawlDisallowedArgs e)
{
  PageToCrawl pageToCrawl = e.PageToCrawl;
  _logger.Info($"Did not crawl page {pageToCrawl.Uri.AbsoluteUri} due to {e.DisallowedReason}.");
}
{% endcodeblock %}

### 禁止爬取页面内超链接
这一事件触发于被禁止获取页面内某一超链接后，同样需要配合robot.txt处理机制和决定器一起使用。

{% codeblock lang:csharp %}
private void Crawler_PageLinksCrawlDisallowed(object sender, PageLinksCrawlDisallowedArgs e)
{
  CrawledPage crawledPage = e.CrawledPage;
  _logger.Info($"Did not crawl the links on page {crawledPage.Uri.AbsoluteUri} due to {e.DisallowedReason}");
}
{% endcodeblock %}

## 提取DOM内所有的文字信息
由于我的最终目的是要做一个全文字搜索引擎，我需要提取出HTML页面中所有的文字信息。然而我用到的`AngleSharp`HTML解析器不但会提取出有用的文字信息，还会提取出`<script>`、`<style>`标签中的JavaScript和CSS代码...这设计的也太蠢了。于是我只好自己写一个。逻辑不难懂，具体可以看代码，但想出这个方案前真是走了不少弯路。

{% codeblock lang:csharp %}
private readonly IEnumerable<string> filteredTags = new string[]
{
  "STYLE", "SCRIPT"
};
private readonly IEnumerable<string> filteredClassNames = new string[]
{
  "aspnethidden", "header", "footer", "modal"
};
private string TraverseDomGetContent(IElement node)
{
  // Filter unwanted tags and class names (e.g. footers)
  if (filteredTags.Contains(node.TagName)) return null;
  if (node.ClassName != null)
  {
    string lowerClassName = node.ClassName.ToLower();
    if (filteredClassNames.Any(n => lowerClassName.Contains(n))) return null;
  }

  // Get and join all text content from children
  string childrenJoin = string.Empty;
  foreach (IElement child in node.Children)
  {
    string childString = TraverseDomGetContent(child);
    if (childString != null) childrenJoin += childString + " ";
  }

  // If all children have no content, return text content of current node.
  // Else return the content of children.
  string content = childrenJoin == string.Empty ? node.TextContent.Trim(trimmedCharacters) : childrenJoin.Trim();
  return string.IsNullOrEmpty(content) ? null : content;
}
{% endcodeblock %}

## 运行
一切准备完毕后，运行爬虫并验证结果。最后将结果导出至文件或数据库。

{% codeblock lang:csharp %}
public void Run()
{
  CrawlResult result = _crawler.Crawl(RootUrl); // 注意此为同步任务，且必须同步

  if (result.ErrorOccurred)
  {
    _logger.Error($"Crawl of {result.RootUri.AbsoluteUri} completed with error: {result.ErrorException.Message}");
  }
  else
  {
    _logger.Info($"Crawl of {result.RootUri.AbsoluteUri} completed.");
    _logger.Info($"Total pages founded: {_totalPages}. Pages crawled:{_pagesCrawled}.");

#if DEBUG
    ExportToJson(Pages);
#else
    ExportToDatabase(Pages);
#endif
  }
}

private void ExportToJson(Dictionary<string, SitePage> pages)
{
  string fileName = "output.json";
  string content = JsonConvert.SerializeObject(Pages);
  File.WriteAllText(fileName, content);
  _logger.Info($"Wrote to {Path.GetFullPath(Directory.GetCurrentDirectory() + '\\' + fileName)}");
}

private void ExportToDatabase(Dictionary<string, SitePage> pages)
{
  try
  {
    _entities.SitePages.AddOrUpdate(pages.Values.ToArray());
    _entities.SaveChanges();
  }
  catch (DbEntityValidationException ex)
  {
    foreach (var err in ex.EntityValidationErrors)
    {
      foreach (var subErr in err.ValidationErrors)
      {
        _logger.Error($"Validation failed for property {subErr.PropertyName} because {subErr.ErrorMessage}.");
      }
    }
  }
  _logger.Info("Wrote to database.");
}
{% endcodeblock %}

# 小结
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;至此关于Abot爬虫的概览便总结完毕。下一步我想做的是基于SQL Server的简易全文字搜索引擎。具体怎么实现等下回再说。