---
layout: post
title: 'Jekyll on GitHub Pagesの知見（2015年版）'
date: 2015-12-07 23:39
comment: 'false'
---


<!--- ## ページのルート --->
<!---  --->
<!--- GitHub Pagesは`ユーザ名.github.io`リポジトリの使用が一般的である。 --->
<!---  --->
<!--- それとは別に、任意のリポジトリで`gh-pages`ブランチを切り、`index.html`その他を配置しGitHubへプッシュすると、`https://ユーザ名.github.io/リポジトリ名/`でアクセスできる。 --->
<!---  --->
<!--- ### <i class="fa fa-sitemap fa-fw"></i> 844196.github.ioの例 --->
<!---  --->
<!--- ``` --->
<!--- 844196.github.io --->
<!---  | --->
<!---  |- /index.html --->
<!---  |      repository: https://github.com/844196/844196.github.io --->
<!---  |      branch:     master --->
<!---  | --->
<!---  `- /blog/index.html --->
<!---         repository: https://github.com/844196/blog --->
<!---         branch:     branch: gh-pages --->
<!--- ``` --->
<!---  --->
<!--- - `844196.github.io`リポジトリ --->
<!---     - `master`ブランチのルートにある`index.html`が<http://844196.github.io/index.html>に対応 --->
<!--- - `blog`リポジトリ --->
<!---     - `gh-pages`ブランチのルートにある`index.html`が<http://844196.github.io/blog/index.html>に対応 --->
<!---  --->
<!---  --->
<!--- ## Jekyll --->
<!---  --->
<!--- GitHub Pagesでは構成管理にJekyllを利用することができる。 --->

## <i class="fa fa-files-o fa-fw"></i> Data Files

Data Filesは、YAML等のデータを`site.data.ファイル名`配列変数でアクセスすることができるJekyllの機能である。これにより、HTML文書からデータを分離し、データドリブンなページレンダリングが可能となる。

### 例: ナビゲーションバー

通常はHTML内にハードコーディングされる、ナビゲーションバーがある:

```html
<nav id="navbar">
  <ul>
    <li><a href="/profile.html">Profile</a></li>
    <li><a href="/archive.html">Archive</a></li>
  </ul>
</nav>
```

このナビゲーションバーに`/tag.html`へのリンクを追加したい場合、HTMLを直接編集し`li`を追加する方法があるが、Data Filesを用いると簡潔に書ける。

まず、リポジトリルートに`_data`ディレクトリを作成し、`_data/navbar.yml`を作成・編集する:

```yaml
- name: 'Profile'
  href: '/profile.html'

- name: 'Archive'
  href: '/archive.html'

- name: 'Tags'
  href: '/tags.html'
```

この`_data/navbar.yml`はJekyllにより解釈され、`site.data`変数に以下のような配列として格納される:

```
{
  "navbar" => [
    {"name" => "Profile", "href"=>"/profile.html"},
    {"name" => "Archive", "href"=>"/archive.html"},
    {"name" => "Tags", "href"=>"/tags.html"}
  ]
}
```

あとはHTML内で`site.data.navbar`をforループすればよい:

```html
<nav id="navbar">
  <ul>
    {{"{% for item in site.data.navbar "}}%}
      <li><a href="{{"{{ item.href "}}}}">{{"{{ item.name "}}}}</a></li>
    {{"{% endfor "}}%}
  </ul>
</nav>
```

以後、項目を追加・削除する際は`_data/navbar.yml`を編集すればよい。以下では、`/profile.html`へのリンクを削除し、[Nuitter](http://keepoff07.github.io/nuitter/)へのリンクを追加している:

```yaml
- name: 'Archive'
  href: '/archive.html'

- name: 'Tags'
  href: '/tags.html'

- name: 'Nuitter'
  href: 'http://keepoff07.github.io/nuitter/'
```

## <i class="fa fa-archive fa-fw"></i> 月別アーカイブ

Jekyllではポストの本文や日付等のメタデータは、全て`site.posts`配列変数に格納されている。これをうまくforループすることで月別アーカイブが作成できる。

```html
{{"{% for post in site.posts "}}%}
  {{"{% assign current_year-month = post.date | date:'%Y/%m' "}}%}

  {{"{% if current_year-month != previous_year-month "}}%}
  {{"{% unless forloop.first "}}%}</ul>{{"{% endunless "}}%}
    <h2>{{"{{ current_year-month "}}}}</h2>
    <ul>
  {{"{% endif "}}%}

  <li><a href="{{"{{ site.baseurl "}}}}{{"{{ post.url "}}}}">{{"{{ post.title "}}}}</a></li>

  {{"{% if forloop.last "}}%}</ul>{{"{% endif "}}%}

  {{"{% assign previous_year-month = current_year-month "}}%}
{{"{% endfor "}}%}
```

[このサイトのarchive.html]({{ site.baseurl }}/archive.html)

## <i class="fa fa-github-alt fa-fw"></i> JekyllのバージョンをGitHub Pagesに合わせる

GitHubが公開している[pages-gem](https://rubygems.org/gems/github-pages)を`Gemfile`に指定・インストール[^1]することで、ローカルとGitHubとのバージョン間の違いのせいで面倒なヤクの毛刈りをする必要がなくなる。

[^1]: 実際にインストールされる各Gemのバージョンは[lib/github-pages.rb](https://github.com/github/pages-gem/blob/master/lib/github-pages.rb)で確認できる

```bash
$ cat << EOF > Gemfile
source 'https://rubygems.org'

gem 'github-pages'
EOF

$ bundle install --path vendor/bundle
```

## <i class="fa fa-refresh fa-fw fa-spin"></i> ライブリロード

guard-jekyll-plus、guard-livereloadを導入すると、ファイルを編集する度にページのビルドとブラウザリロードができる。

```ruby
source 'https://rubygems.org'

gem 'github-pages'

group :development do
  gem 'guard'
  gem 'guard-jekyll-plus'
  gem 'guard-livereload'
end
```

```ruby
guard 'jekyll-plus', :serve => true do
  watch(%r{.*})
  ignore(%r{^_site/})
end

guard :livereload do
  watch(%r{.*})
end
```

```bash
$ bundle exec guard start
```

guard-livereloadについては、[以前の記事]({{ site.baseurl }}{% post_url 2015-11-21-live-reload %})を参照せよ。


## <i class="fa fa-puzzle-piece fa-fw"></i> プラグイン

TODO: なんか増えてた https://github.com/github/pages-gem/blob/master/lib/github-pages.rb#L27

## <i class="fa fa-magic fa-fw"></i> SCSS (Sass)
