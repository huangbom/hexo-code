---
title: Ruby on Rails 笔记
date: 2017-05-11 09:56:46
tags: ruby,rails
category: Ruby on Rails
---

学习rails时记下的笔记

## Rails 环境

```
rails console						# Loading development environment
rails console test					# Loading test environment
rails console production			# Loading production environment
rails console --sandbox				# sandbox

rails server --environment production			# server production
```

## controller [plural]

```
rails generate controller Sessions home help				# generate 命令可以接收一个可选的参数列表,创建首页、帮助页面
rails generate controller PasswordResets new edit --no-test-framework

rails destroy  controller StaticPages 						# 撤销staticpages controller
rails destroy  controller StaticPages home help
```

## model

```
rails generate model User name:string email:string
rails destroy model User	
```

## migration

```
rails generate migration add_index_to_users_email					# generate migration index
rails generate migration add_admin_to_users admin:boolean			# add filed
rails generate migration add_password_digest_to_users password_digest:string
rails generate migration add_reset_to_users reset_digest:string reset_sent_at:datetime
```

### 执行 migrate
```
rails db:migrate
rails db:rollback
rails db:migrate VERSION=0
rails db:migrate:reset			#reset db
```

## integration_test模版

```
rails generate integration_test site_layout
rails generate integration_test users_signup
```

### 测试

测试驱动开发使用“遇红-变绿-重构”循环

```
rails test
rails test:integration								# 只执行集成测试
rails test:models									# 执行model测试【待验证】
rails test test/integration/users_login_test.rb		# 执行单个文件测试
```

## mailer

```
rails generate mailer UserMailer account_activation password_reset
```

## uploader
```
rails generate uploader Picture
```

## 页面操作

## link_to

```
<%= link_to "(forgot password)", new_password_reset_path %>
<%= link_to "Reset password", edit_password_reset_url(@user.reset_token, email: @user.email) %>
```

## update_attribute
```
update_attribute(:name, "El Duderino")
update_columns(activated: true, activated_at: Time.zone.now)
```

## assert_*

```
assert_select "div"													<div>foobar</div>
assert_select "div", "foobar"										<div>foobar</div>
assert_select "div.nav"												<div class="nav">foobar</div>
assert_select "div#profile"											<div id="profile">foobar</div>
assert_select "div[name=yo]"										<div name="yo">hey</div>
assert_select "a[href=?]", '/', count: 1							<a href="/">foo</a>
assert_select "a[href=?]", '/', text: "foo"							<a href="/">foo</a>
assert_select "input[name=email][type=hidden][value=?]", user.email
	<input id="email" name="email" type="hidden" value="michael@example.com" />

assert_template 'static_pages/home'
assert_response :success
assert_equal full_title, "Ruby on Rails"		# 判断两个值相等，full_title是一个方法
```

## other

```
app/assets/stylesheets/application.css
*= require_tree .
会把 app/assets/stylesheets 目录中的所有 CSS 文件（包含子目录中的文件）都引入应用的 CSS 文件。
*= require_self
会把 application.css 这个文件中的 CSS 也加载进来。
```

下载一个图片

```
curl -o app/assets/images/rails.png -OL railstutorial-china.org/assets/images/rails.png
```
