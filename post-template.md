---
title: Hexo Butterfly 主题 Post 变量模板
date: 2025-12-07 12:00:00
updated: 2025-12-07 14:00:00
description: 详细展示 Hexo Butterfly 主题中 Post 支持的所有变量
tags:
  - Hexo
  - Butterfly
  - 模板
categories:
  - 技术文档
comments: true
cover: /img/cover.jpg
toc: true
---

# Hexo Butterfly 主题 Post 变量模板

## 1. 核心 Post 变量

### 基本信息
```pug
// 文章标题
<h1>#{page.title}</h1>

// 文章创建日期
<p>创建日期: #{page.date}</p>

// 文章更新日期
<p>更新日期: #{page.updated}</p>

// 文章描述
<p>描述: #{page.description}</p>

// 文章路径
<p>路径: #{page.path}</p>

// 文章源文件路径
<p>源文件: #{page.source}</p>

// 是否启用评论
<p>评论: #{page.comments ? '启用' : '禁用'}</p>

// 是否加密
<p>加密: #{page.encrypt ? '是' : '否'}</p>
```

### 分类与标签
```pug
// 文章分类
if page.categories && page.categories.data.length > 0
  div
    span 分类: 
    each category in page.categories.data
      a(href=url_for(category.path)) #{category.name}

// 文章标签
if page.tags && page.tags.data.length > 0
  div
    span 标签: 
    each tag in page.tags.data
      a(href=url_for(tag.path)) #{tag.name}
```

### 文章内容
```pug
// 文章内容（HTML格式）
<div class="post-content">
  != page.content
</div>

// 原始内容（Markdown格式）
<div class="raw-content">
  != page._content
</div>
```

## 2. 主题扩展变量

### 文章摘要
```pug
// 文章摘要
if page.postDesc
  div.post-desc
    != page.postDesc
```

### 字数统计与阅读时间
```pug
// 字数统计
if hexo-config('wordcount.enable')
  span.word-count
    = wordcount(page.content)
    |  字

// 阅读时间
if hexo-config('wordcount.enable')
  span.read-time
    = min2read(page.content)
    |  分钟
```

### 文章封面
```pug
// 文章封面
if page.cover
  img.post-cover(src=page.cover, alt=page.title)
```

### 相关文章
```pug
// 相关文章
if hexo-config('related_post.enable')
  .related-posts
    h3 相关文章
    each post in related_posts(page, site.posts)
      a(href=url_for(post.path))
        h4 #{post.title}
```

## 3. 模板配置变量

### 目录配置
```pug
// 目录
if page.toc && hexo-config('toc.enable')
  .post-toc
    h3 文章目录
    != toc(page.content)
```

### 文章元信息
```pug
// 文章元信息
if hexo-config('post_meta')
  .post-meta
    // 作者
    if hexo-config('post_meta.author')
      span.author #{page.author || config.author}
    
    // 发布日期
    if hexo-config('post_meta.date_type') === 'created' || hexo-config('post_meta.date_type') === 'both'
      span.date #{date(page.date, hexo-config('post_meta.date_format'))}
    
    // 更新日期
    if hexo-config('post_meta.date_type') === 'updated' || hexo-config('post_meta.date_type') === 'both'
      span.updated #{date(page.updated, hexo-config('post_meta.date_format'))}
    
    // 分类
    if hexo-config('post_meta.categories') && page.categories.data.length > 0
      span.categories
        each category in page.categories.data
          a(href=url_for(category.path)) #{category.name}
    
    // 标签
    if hexo-config('post_meta.tags') && page.tags.data.length > 0
      span.tags
        each tag in page.tags.data
          a(href=url_for(tag.path)) #{tag.name}
    
    // 评论数
    if hexo-config('post_meta.comments')
      span.comments #{page.comments ? '有评论' : '无评论'}
    
    // 字数统计
    if hexo-config('post_meta.wordcount') && hexo-config('wordcount.enable')
      span.wordcount #{wordcount(page.content)} 字
    
    // 阅读时间
    if hexo-config('post_meta.min2read') && hexo-config('wordcount.enable')
      span.min2read #{min2read(page.content)} 分钟
```

## 4. 高级功能变量

### 系列文章
```pug
// 系列文章
if page.series
  .series
    h3 系列文章
    // 系列文章相关内容
```

### 文章分页
```pug
// 文章分页
if page.prev || page.next
  .pagination
    if page.prev
      a.prev(href=url_for(page.prev.path)) #{page.prev.title}
    if page.next
      a.next(href=url_for(page.next.path)) #{page.next.title}
```

### 文章分享
```pug
// 文章分享
if hexo-config('share.enable')
  .post-share
    h3 分享本文
    // 分享按钮相关内容
```

### 文章版权
```pug
// 文章版权
if hexo-config('post_copyright.enable')
  .post-copyright
    h3 版权声明
    p 本文作者：#{page.author || config.author}
    p 本文链接：#{url_for(page.path)}
    p 版权声明：本作品采用 #{hexo-config('post_copyright.license')} 许可协议进行许可。
```

## 5. 条件判断示例

### 基于配置的条件渲染
```pug
// 条件渲染示例
if hexo-config('post_pagination.enable')
  != partial('includes/pagination', {page: page})

if hexo-config('mathjax.enable') && page.mathjax
  != partial('includes/third-party/mathjax')

if hexo-config('fancybox')
  a.fancybox(href=page.cover)
    img(src=page.cover)
```

### 基于文章属性的条件渲染
```pug
// 基于文章属性的条件渲染
if page.top
  .top-post 置顶

if page.pin
  .pin-post 精华

if page.password
  .password-post 需要密码
```

## 6. 实用 Helper 函数

### 常用 Helper 函数
```pug
// URL 处理
= url_for(page.path)

// 日期格式化
= date(page.date, 'YYYY-MM-DD HH:mm:ss')

// 相对时间
= relative_date(page.date)

// 截断文本
= truncate(page.content, {length: 200, omission: '...'})

// 标签云
!= tagcloud(site.tags, {min_font: 12, max_font: 24, amount: 50})

// 分类云
!= categorycloud(site.categories, {min_font: 12, max_font: 24, amount: 50})
```

## 7. Front-matter 配置示例

```yaml
title: 文章标题
date: 2025-12-07 12:00:00
updated: 2025-12-07 14:00:00
description: 文章描述
author: 作者名
tags:
  - 标签1
  - 标签2
categories:
  - 分类1
  - 分类2/子分类
comments: true
cover: /img/cover.jpg
toc: true
top: true
pin: true
mathjax: true
password: 123456
```

## 8. 主题配置影响

以下主题配置会影响 Post 变量的使用：

- `post_meta`：控制文章元信息显示
- `toc`：控制目录显示
- `wordcount`：控制字数统计和阅读时间
- `related_post`：控制相关文章推荐
- `post_pagination`：控制文章分页
- `post_copyright`：控制版权声明
- `share`：控制分享功能
- `fancybox`：控制图片灯箱
- `mathjax`：控制数学公式渲染

## 9. 使用建议

1. **合理使用条件判断**：根据主题配置和文章属性进行条件渲染，提高模板的灵活性
2. **优化性能**：避免在模板中进行复杂计算，尽量使用 Helper 函数
3. **保持模板简洁**：将复杂逻辑封装到 Helper 函数或过滤器中
4. **遵循主题规范**：参考主题现有模板的写法，保持一致性
5. **测试不同配置**：测试在不同主题配置下模板的表现

# 模板使用说明

1. 将上述模板内容保存为 `post-template.pug`（或相应的模板文件）
2. 在主题布局中引用该模板
3. 根据需要调整模板内容，适配你的主题样式
4. 结合主题配置文件，自定义模板的显示效果
5. 在文章 Front-matter 中设置相应的属性，控制模板的渲染

该模板涵盖了 Hexo Butterfly 主题中 Post 支持的所有主要变量和使用场景，可以根据实际需求进行修改和扩展。
