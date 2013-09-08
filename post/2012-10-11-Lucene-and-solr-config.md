## 一、搜索引擎创建和搜索索引过程
在非结构化数据中，文档包含字符串，从文档搜索可得到字符串；而要从已知字符串搜索包含该字符串的文件即是从字符串到文档的映射，是从文档到字符串的反向过程，保存这种信息的索引称为__反向索引__（倒排索引）。
倒排索引左边是词典，每个词都保存着包含该字符串的文档列表。
###1. 创建索引过程
	 文档(Document)
	       ↓
	 ------------------------------
	    分词组件(Tokenizer)
	    1. 文档分成一个个单词
	    2. 去除标点
	    3. 去除停词(stop word)
	 ------------------------------
	    词元(Token)
	       ↓
	 ------------------------------------------------
	    语言处理组件(Linguistic Processor)
	   英文：
	   1. 变小写
	   2. 缩减(stemming)为词根  [cars->car]
	   3. 变换(lemmatizatic)为词根[driven ->drive]
	 -------------------------------------------------
	     ↓
	   词(Term)
	     ↓
	 ---------------------------------------
	  索引组件(Indexer)
	  1. 利用Term创建词典
	  2. 排序
	  3. 合并相同的词 生成倒排链表
	 ---------------------------------------
	     ↓
	 倒排链表
###2. 搜索索引过程

(1). 用户输入查询语句 

		 A AND B NOT C

(2). 查询语句分析

- 词法分析( 识别关键字和单词 )
- 语法分析( 生成语法树 )

(3). 搜索索引,得到符合语法树的文档
在倒排表中取出包含A、B和C的文档链表

(4). 排序输出结果
根据得到的文档和查询语句的相关性，对结果进行排序。

- 词的权重(Term weight)

>计算词对文档的重要性，Term在文档的重要性有两个因素：

> *Term Frequency (ft)*: Term在Document出现的次数，越大越重要；

> *Document Frequency (df)*: 多少文档包含此Term，越大说明越不重要。

> *w = tf × log( n / df)*

- 文档相关性
> 文档是由一系列词组成的，通过词之间的关系来判断文档的相关性，利用向量空间模型算法(VSM)。把所有搜索出来的文档向量和查询向量放到一个N维空间，两个向量夹角越小相似度越高(余弦相似定理)。

## 二、Lucene代码模块
- analysis模块负责词法分析及语言处理得到Term；
- index模块负责创建索引；
- store模块负责索引读写；
- QueryParser主要负责语法分析；
- search模块负责索引搜索；
- similarity模块负责相关性打分。
## 三、Lucene创建、查找索引
索引过程：按照全文检索基本过程，将倒排表写成文件格式过程；

搜索过程：按照文件格式读取索引，计算每篇文本得分的过程。





## 四、Lucene索引格式
###索引层次结构：
- 索引(index)：同一个文件夹内所有文件构成一个Lucene索引
- 段(segment)：相同前缀的文件属同一个段
- 文档(document)：不同的文档保存在不同的段中，一个段包含多个文档；
- 域(field)：文档的不同列信息；
- 词(term)：索引最小单位。
### 正向信息：
Index→Segment(segments.gen, segments_N)→Field(fnm, fdx, fdt)→Term(tvx, tvd, tvf)
段的真正信息保存在域和词中，segment文件只是元数据信息。
> - segments_N保存了些索引包含多少个段，每个段包含了多少篇文档；
- XXX.fnm保存了此段包含了多少个域，每个域的名称及索引方式；
- XXX.fdx，XXX.fdt保存了此段包含的所有文档，每篇文档包含了多少域，每个域保存了哪些信息；
- XXX.tvx，XXX.tvd, XXX.tvf保存了此段包含多少文档，每篇文档包含多少域，每个域包含了多少词，每个词的字符串，位置等信息。
### 反向信息：
保存了词典到倒排表的映射：Term→Document
> - XXX.tis，XXX.tii保存了词典，此段包含所有的词按字典顺序排序；
- XXX.frq 保存了倒排表，每个词的文档ID列表
- XXX.prx保存了倒排表个每个词在包含此词的文档中的位置

## 五、solr配置文件参数
### 1. schema.xml
字段配置[schema.xml](http://wiki.apache.org/solr/SchemaXml)l定义了加入索引的数据的数据类型
 
	< fieldType name =" string " class =" solr.StrField " sortMissingLast =" true " omitNorms =" true " />

- *indexed* 需要被索引的field
- *stored* 需要被存储的field, 如果仅需要存储作为其它域查询结果的话, 则不需要设indexed, 仅需要stored即可; 
- *sortMissingLast="true"* 表示此filed没有数据时的文档排在有数据文档之后, 而不 管请求时的排序规则;
- *sortMissingFirst="true"* 类似;
- *omitNorms="true" *表示不关心字段长段(或不使用存储规范)和索引时不计算boost，可以节省内存空间. 只有文本字段需要关心，所以一般文本字段不设置为true;
- *compressed=true|flase* 使用gzip压缩field, 用于TextField和StrField;
- *multiValued=true|false* 该field在文档中出现多次时设多值为true, 即文档可以拥有相同名称的多个field;
- __Expert field options__ termVectors=false|true <?> Solr 1.1
>If set, include full term vector info.
If enabled, often also used with termPositions="true" and termOffsets="true".
To use interactively, requires TermVectorComponent
Corresponds to TV button in Luke, and V field attribute.

###2. solrconfig.xml
[solrconfig.xml](http://wiki.apache.org/solr/SolrConfigXml) 配置solr自身的一些参数
[solrconfig.xml示例+注释](http://svn.apache.org/repos/asf/lucene/dev/trunk/solr/example/solr/collection1/conf/solrconfig.xml)

####索引配置
- *useCompoundFile="true"*表示使用单文件索引格式(复合索引格式)，减少每次打开文件的数目, 会增加索引时间;
> __非复合索引格式__
<table>
    <tr>
        <td>segment</td>
        <td>field</td>
        <td>term</td>
    </tr>
    <tr>
         <td> segments.gen </td>
         <td>_0.fdx</td>
         <td>_0.tvx</td>
    </tr>
     <tr>
         <td> segments_N </td>
         <td>_0.fnm</td>
         <td>_0.tvd</td>
    </td>
         <tr>
         <td>   </td>
         <td>_0.fdt</td>
         <td>_0.tvx</td>
    </td>
</table>
> __复合索引格式__
<table>
    <tr>
        <td>segment</td>
        <td>field term</td>
        <td>  </td>
    </tr>
    <tr>
         <td> segments.gen </td>
         <td>_0.cfs</td>
         <td> </td>
    </tr>
     <tr>
         <td> segments_N </td>
         <td>_0.cfs</td>
         <td> </td>
</table>           
                        
         

- *mergeFactor* 控制一次合并多少个段(segment)，或合并频率，值小使用内存少, 索引时间变长; 值大使用内存加大, 索引时间变小. 默认值10.(当大小相当的段的数量达到此值时开始合并)
- *ramBufferSizeMB*和 *maxBufferedDocs*
> 段合并发生在添加完一篇文档后, 添加完一篇文档后, 如果内存达到用户设定的ramBufferSize或者maxBufferedDocs, 则写入文件系统, 形成一个新的段. 新段加入可能造成差不多大小的段个数达到mergeFactor, 则开始合并过程; 

>*ramBufferSizeMB*和 *maxBufferedDocs*可以同时使用, __不要__随意设置MaxbufferedDocs。MaxBufferedDocs和RAMBufferSize共同控制内存中文档的容量, MaxbufferedDocs本身是disabled，设置不合理将导致大规模的重建索引非常慢。
## 六、 minisearch接下来工作


