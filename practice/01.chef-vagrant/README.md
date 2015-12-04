1.셰프와 베이그런트로 인프라의 코드화
==================================

# 1.1 인프라 구성의 자동화
- Infrastructure as Code
    - Chef
        - http://www.getchef.com/chef
        - 서버 구성(Provisioning)을 자동화하는 프레임워크
        - Ruby 로 개발한 OSS(Open Source Software)
        - 레시피를 루비로 작성
            - DSL(Domain Specific Language)에 정의된 언어를 사용하는 것 뿐
    - ServerSpec
        - http://serverspec.org

# 1.2 사용 환경

# 1.3 Vagrant 를 사용한 가상 서버 준비
- Vagrant
    - 서버 가상화 소프트웨어(e.g. VirtualBox)의 Frontend
    - Ruby 로 만들어진 OSS
    - (*) Chef 검증 환경을 구성 & 테스트 주도 서버 환경 구성할 때 편리
        - 즉, 한 번 만들고 지워도 상관없는 호스트를 구성하는데 매우 편리

## 1.3.1 Virtual Box 설치
- http://www.virtualbox.org

## 1.3.2 Vagrant 설치
- http://vagrantup.com
    - $ vagrant -v
    - (*) Box : Vagrant 용 OS 이미지
        - Box 를 얻는 방법은 여러 가지 --> Bento 프로젝트(Opscode 를 관리) box 를 사용
- 가상 이미지 받기
    - $ vagrant box add centos http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_centos-6.5_chef-provisionerless.box
        ```
        $ vagrant box add centos65 http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_centos-6.5_chef-provisionerless.box
        ==> box: Box file was not detected as metadata. Adding it directly...
        ==> box: Adding box 'centos65' (v0) for provider: 
            box: Downloading: http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_centos-6.5_chef-provisionerless.box
        ==> box: Successfully added box 'centos65' (v0) for 'virtualbox'!
        ```
    - ~/.vagrant.d
        - 메타 데이터와 이미지가 저장된다
        - (*) $ ll ~/.vagrant.d/boxes/centos65/0/virtualbox/
- 가상 서버 가동
    - (*) $ mkdir -p ~/workspace/vagrant/centos65 & $ cd ~/workspace/vagrant/centos65 
    - $ vagrant init centos65
        - (*) Vagrantfile 생성
        ```
        A `Vagrantfile` has been placed in this directory. You are now
        ready to `vagrant up` your first virtual environment! Please read
        the comments in the Vagrantfile as well as documentation on
        `vagrantup.com` for more information on using Vagrant.
        ```
    - $ vagrant up
        - (*) Failed to mount folders in Linux guest. This is usually because the "vboxsf" file system is not available.
            ```
            Failed to mount folders in Linux guest. This is usually because
            the "vboxsf" file system is not available. Please verify that
            the guest additions are properly installed in the guest and
            can work properly. The command attempted was:
            
            mount -t vboxsf -o uid=`id -u vagrant`,gid=`getent group vagrant | cut -d: -f3` vagrant /vagrant
            mount -t vboxsf -o uid=`id -u vagrant`,gid=`id -g vagrant` vagrant /vagrant
            
            The error output from the last command was:
            
            /sbin/mount.vboxsf: mounting failed with the error: No such device
            ```
            - $ vagrant plugin install vagrant-vbguest
                - (*) Reference : http://blog.mdnsolutions.com/index.php/vagrant-up-in-mac-os-vboxsf-file-system-is-not-available/ 
                - (*) if failed,
                    - $ sudo gem install nokogiri -v '1.6.6.2'
                        - $ ruby -v
                        - if <= 1.9.2, update Ruby
                            - (*) http://stackoverflow.com/questions/3696564/how-to-update-ruby-to-1-9-x-on-mac
    - $ vagrant ssh -C 'sudo /etc/init.d/vboxadd setup'
    - $ vagrant reload
    - $ vagrant ssh
- 가상 서버의 정지와 삭제
    - $ vagrant halt
    - $ vagrant destroy
- ssh 설정
    - $ vagrant ssh-config --host webdb >> ~/.ssh/config



# 1.4 셰프로 인프라의 코드화
- 2종류의 Chef
    - Chef Server
        - 어느 정도 이상의 서버 규모를 대상으로
        - 구성 관리 대상이 되는 각 호스트가 클라이언트 형태로 서버에서 명령을 받는 구조
    - Chef Solo
        - Standalone 을 대상으로
        - (*) 단 몇대의 서버라면 사용해도 문제 없이 구성하고 관리 가능
- node
    - Chef 에서 구성 관리 대상의 호스트/서버
- chef-sole
    - (*) Vagrant OS 이미지에 수동으로 Chef 설치 <-- 설치 스크립트 이용
    - $vagrant ssh
    - $ sudo bash -c "curl -L http://opscode.com/chef/install.sh | bash"
    - $ sudo chef-solo
- 레시피 작성
    - (*) 대상 노드에 httpd 를 설치 & 레시피 작성
        - knife : 레시피 양식을 만드는 프런트엔드 작업용 명령. chef와 함께 설치된다.
    - $ sudo knife cookbook create httpd
        - (*) cookbook : 레시피 파일이 있는 폴더
        - 쿡북 양식을 생성
    - $ sudo vi /var/chef/cookbooks/httpd/recipes/default.rb
    ```ruby
    package "httpd" do
        action :install
    end
    ```
    - $ sudo chef-solo -o httpd
        - (*) $ rpm -qa | grep httpd
- knife-sole 에서 셰프 실행
    - (*) knife-solo
        - knife 의 plugin
        - 로컬에서 편집한 쿡북을 rsync를 사용해 노드에 전송해서 chef-solo 를 실행
        - Ruby로 만들어진 도구 --> RubyGems.org 에 공개
    - 설치 (host OS)
        - (*) Bundler 사용하면 편리
        - Gemfile 생성
            - Vagrantfile 과 같은 폴더
            ```ruby
            source 'https://rubygems.org'

            gem 'knife-solo'
            ```
        - $ Bundler
    - Chef repository
        - knife-solo 를 사용하면 작업 폴더내에 초기화를 실행할 셰프용 폴더가 생성된다.
    - $ bundle exec knife solo init .
        - (*) $ tree
    - (*) $ echo ".vagrant/" > .gitignore
- knife-solo prepare 로 노드에 Chef Solo 설치
    - $ bundle exec knife solo prepare webdb
        - (*) webdb : 호스트명
        - (*) knife-solo 용 rsync 설정과 그 외 설정도 조정된다.
- 쿡북 작성과 적용
    - $ bundle exec knife cookbook create httpd -o site-cookbooks
    - $ emacs site-cookbooks/httpd/recipes/default.rb
    - $ bundle exec knife solo cook webdb -o httpd
    - (*) SSL validation of HTTPS request is disabled
        - $ vi .chef/knif.rb
        ```ruby
        ssl_verify_mode : verify_peer
        ```
- 노드 객체 편집
    - base_package <-- 자주 쓰는 패키지를 모아 쿡북을 생성
    - $ bundle exec knife cookbook create base_package -o site-cookbooks
    - $ vi site-cookbooks/base_package/recipes/default.rb
        ```ruby
        $ w{gcc make git readline readline-devel}.each do |pkg|
            package pkg do
                action :install
            end
        end
        ```
    - $ vi chef-repo/nodes/webdb.json
        ```json
        {
            "run_list": [
                "recipe[base_package]",
                "recipe[httpd]"
            ],
            "automatic": {
                "ipaddress": "webdb"
            }
        }
        ```
    - $ bundle exec knife solo cook webdb
        - webdb 노드 상태를 노드 객체에 작성해서 -o 옵션 없이 실행하면 실행될 쿡북을 노드 객체가 판단하게 된다.
- Chef Solo로 httpd 프로비저닝
    - httpd 쿡북 레시피 편집
        - $ sudo vi /var/chef/cookbooks/httpd/recipes/default.rb
        ```ruby
        package "httpd" do
            action :install
        end

        # httpd 시작
        service "httpd" do
            action [:enable, :start]
        end

        # 설정 파일 설치
        template "httpd.conf" do
            path "/etc/httpd/httpd.conf"
            owner "root"
            group "root"
            mode 0644
        end
        # 서비스 재로딩
        ```
        - package, service, template --> 리소스
            - service
                - 리눅스의 /etc/init.d/init 스크립트의 상태를 언급
                - (*) chkconfig or '/etc/init.d/httpd start'
            - template
                - httpd.conf 같은 설정 파일을 배포시 사용
                - site-cookbooks/httpd/templates/default/httpd.conf.erb에 있는 파일이 사용된다.
                    - 최소한의 설정이 기록되어 있다.
        - path, owner, group, mode --> 속성
        - 리소스의 집합 --> 노드 상태를 표현
        - 셰프를 적용하면 노드는 그 상태로 설치된다.
    - Attribute 정의 <-- 노드 객체로 가능
        - $ vi chef-repo/nodes/webdb.json
            ```json
            {
                "httpd": {
                    "port": 80
                },
                "run_list": [
                    "recipe[base_package]",
                    "recipe[httpd]"
                ],
                "automatic": {
                    "ipaddress": "webdb"
                }
            }
            ```
            - Chef 관점 : webdb 노드에 httpd 포트는 80으로 한다
- vagrant의 host-only 네트워크 유효화
    - (*) host OS - guest OS 사이에서만 유효한 네트워크 인터페이스를 생성 --> IP 주소를 직접 지정해서 통신가능하도록
    - $ vi Vagrantfile
        - ex) config.vm.network :private_network, ip: "192.168.33.10"
    - $ vagrant halt / up
        - host OS 에 가상 인터페이스가 추가됨
- 노드의 상태는 기본적으로 쿡북에 모두 정의되어 있어야 한다.
    - ex) ssh로 접속해 /etc/init.d/iptables stop 을 직접 하지 말아라.
- (*) Chef 는 서버 형상관리 프레임워크이다.
- 리소스 정의 방법
    - http://docs.getchef.com/resource.html
- 공개된 쿡북 사용
    - Chef Supermarket
        - (*) https://supermarket.getchef.com
    - ex) $ bundle exec knife cookbooks site vendor yum-epel
        - "run_list": [..., "recipe[yum-epel]", ...]
