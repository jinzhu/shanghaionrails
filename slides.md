!SLIDE

# 从 SuperList 谈谈我的工具箱

_Jinzhu / [ThePlant](http://theplant.jp) / May, 2011_


!SLIDE

## [SuperList](https://github.com/jinzhu/super_list) <span>为解决和其它公司进行API交互的问题而抽象出来的一个小插件.</span>
##### 以信用卡的发卡机构为例：

* 数据库保存值: ``V, M, X, C, J`` <br/>
* 页面对应显示值: ``VISA, Mastercard, AMEX, DINERS, JCB`` <br/>
* 公司A API: ``visa, master, american_express, diners_club, jcb`` <br/>
* 公司B API: ``1, 2, 3, 4, 5``

@@@ ruby
    
    # config/initializers/super_list.rb
    SuperList.new("CreditCardIssuer",ActiveSupport::OrderedHash[
	  "V", {:default => "VISA", :companyA => "visa", :companyB => "1"},
	  "M", {:default => "Mastercard", :companyA => "master", :companyB => "2"},
	  "X", {:default => "AMEX", :companyA => "american_express", :companyB => "3"},
	  "C", {:default => "DINERS", :companyA => "diners_club", :companyB => "4"},
	  "J", {:default => "JCB", :companyA => "jcb", :companyB => "5"}
    ])

    # app/models/credit_card.rb
    super_list :issuer, 'CreditCardIssuer'

    # Usage
    credit_card.issuer = 'V'
    credit_card.issuer #=> 'V'
    credit_card.issuer(:companyB) #=> '1'

    SuperList.options = { :format => :comanyA }
    credit_card.issuer #=> 'visa'
    credit_card.issuer = 'jcb'
    credit_card.issuer(:companyB) #=> '5'
    # I18n, View helper, Validations

@@@

!SLIDE

## 在日常项目中遇到的常见问题
* 服务器架设，配置
* 备份
* 监控
* JavaScript I18n
* 导入导出Excel,CSV数据
* DraftPublish系统
* Admin
* ....

!SLIDE

## 服务器配置  ---  ThePlantProvisioning
* 简单的配置一下，例如Amazon Key, 数据库密码
* ``cap provisioning:bootstrap`` 启动EC2
* ``cap provisioning:provision`` 根据你的配置安装各种软件、Gem，配置Nginx, Postfix, RVM等.


!SLIDE

## 备份中心  ---  [BackupIt](https://github.com/jinzhu/backupit)
* 把下面的配置扔到我们备份中心的配置文件里, 然后运行``rake deploy``.

@@@ ruby

    server 'app1' do
      host "app@example.com"
      port "234"
      rsync ['/media/data/production/shared/documents', {'/media/data/production/shared/system' => 'attachments'}]
      mysql 'database_for_production' do
	      user      'user'
	      password  'thePassword'
	      databases 'database_for_production'
      end
      mysql 'database_for_staging' do
	      user      'user'
	      password  'thePassword'
	      databases 'database_for_staging'
      end
    end

@@@

!SLIDE

## 监控中心  --- Nagios.rb
* 把下面的配置扔到我们的监控中心的配置文件里

@@@ ruby

    server 'server1', 'server2' do
      check_disk :warning => '15%'
      check_users
      check_load
      check_ssh
      check_mem :warning => '10%', :critical => '5%'
    end

    server 'server1' do
      host '111.222.33.44'
      username 'app'
      port '222'
    end

    server 'server2' do
      host '44.33.222.111'
      username 'app'
      port '222'

      check_mysql :user => 'app',:password => 'Mal2DsHaYVEoXlJ',:database => 'production'
      check_disk "/mnt/DATA" do
          warning do
                remote("rm -rf /mnt/DATA/tmp_files/")
          end
      end
      check_memcached
    end

@@@

* ``nagios.rb --install`` 安装客户端到被监控的服务器上
* ``nagios.rb --deploy`` 部署修改过的配置文件到相应机器上


!SLIDE

## JavaScript国际化  --- JsL10n

* 把下面的代码放到 locales 文件里，注意要以js.yml结尾.

@@@ yaml

    # #{Rails.root}/config/locales/en.js.yml
    en:
	  example:
		hello_world: "Hello World"
		hello_user: "Hello %{user}"

@@@

* layout里引用下  ``<%= raw include_qor_js_l10n %>``
* JavaScript中可使用 ``I18n.t("example.hello_user", {'user' : 'Jinzhu'})``  #=>  "Hello Jinzhu"


!SLIDE

## 在其它方面的一些工具<br/><br/>

<table>
  <tr>
    <th>网站</th>
    <td>Qor</td>
    <td>关于Admin的一站式服务</td>
  </tr>
  <tr>
    <th>服务器</th>
    <td>HealthCheck</td>
    <td>用于Load Balancing, 检查mysql, memcache, load, memory ...</td>
  </tr>
  <tr>
    <th>Shell</th>
    <td><a href="http://github.com/jinzhu/grb">Grb</a></td>
    <td>方便操作远程Git Branch的小工具</td>
  </tr>
  <tr>
    <th>测试</th>
    <td><a href="http://github.com/jinzhu/testingmachine">TestingMachine</a></td>
    <td>基于Keyword驱动的View测试框架<br/><span class='note'>理论上来说和Cucumber差不多类型，但是，在早期的开发阶段，遭到了同事的一致批评，已经停止开发好久了，当然我对它还是蛮有兴趣的。。。 ：(</span> </td>
  </tr>
  <tr>
    <th>浏览器</th>
    <td><a href="https://github.com/jinzhu/vrome">Vrome</a></td>
    <td>VIM for Chrome的插件</td>
  </tr>
  <tr>
    <th>编辑器</th>
    <td><a href="https://github.com/janx/vim-rubytest">Vim-RubyTest</a></td>
    <td>方便在VIM里运行单个测试</td>
  </tr>
  <tr>
    <th> </th>
    <td> ............ <td>
    <td></td>
  </tr>
</table>


!SLIDE

## 你的工具箱里有什么好玩的工具吗？ 欢迎交流 ！！！

!SLIDE

# THANKS!

## Jinzhu Zhang
#### [ThePlant](http://theplant.jp) / Hang Zhou
* wosmvp@gmail.com
* [github.com/jinzhu](http://github.com/jinzhu)
* [twitter.com/zhangjinzhu](http://twitter.com/zhangjinzhu)
