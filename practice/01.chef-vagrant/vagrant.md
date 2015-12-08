(*) Vagrant 특정 버전이 지원하는 Hypervisor 버전을 맞추는것도 필요
--> 어디서 확인? :

Hypervisor
VMWare
Parallels
Virtual Box

반복 : Virtual Machine 생성 --> OS 설치 --> 각종 설정

Install a Hypervisor
Virtual Box (https://www.virtualbox.org) --> VirtualBox-5.0.10-104061-OSX.dmg (here)
Windows
OS X
Linux
Solaris

Install the Vagrant
http://www.vagrantup.com
MAC OS X
Windows
Debian
Cent OS


Box : VM 에 대한 기본 템플릿 ( 수백메가)
http://www.vagrantbox.es
https://atlas.hashicorp.com/boxes/search

Ubuntu 14.04 amd64 (Past Release : http://releases.ubuntu.com/ )
$ vagrant init ubuntu14.04amd64 https://github.com/kraksoft/vagrant-box-ubuntu/releases/download/14.04/ubuntu-14.04-amd64.box
--> 현재 폴더에 Vagrantfile 파일 다운로드

$ vagrant up
--> ubuntu14.04amd64 라는 box 이미지 정보를 찾아서 없으면 추가후, box 이미지를 다운로드 한다.
--> .vagrant/ 생성

(*) Box : VM 생성을 위한 기본 템플릿
(*) Vagrant file : 생성될 VM에 대한 세부 설정

box 를 기반으로 customise 가능
config.vm.provider --> vm.customize --> --memory, --name, --cpus, ..

Vagrant + Provisioning
--> 필요한 미들웨어 설치도 Vagrantfile 을 이용하여 설치
--> OS + 다양한 미들웨어 설치
--> 즉, Vagrantfile에 정의된 Provision Script/명령어를 수행하여 미들웨어(예, apache2) 를 설치
--> config.vm.provision :shell
--> vagrant up/reload/provision 시마다 항상 수행되므로 주의
--> (*) http://docs.vagrantup.com/v2/provisioning/index.html

결론 : 개발 환경 구축 방법
2개의 repository : Box & Vagrant
- Box repo : Box 이미지
- Vagrant repo : Vagrantfile --> 해당 box 이미지 url 을 포인팅하도록

예) ubuntu + mysql, ubuntu + node.js

multiple vm 을 단일 Vagrantfile 에

==> Vagrant는 Virtual Box, VMWare, AWS EC2 지원 가능하다
--> 상세 내용은 여기 : http://docs.vagrantup.com/


ubuntu 14.04 를 설치시 mount 에러 발생 (vboxsf 관련)
: 참고 블로그 : http://blog.mdnsolutions.com/index.php/vagrant-up-in-mac-os-vboxsf-file-system-is-not-available/
host에서 플러그인 설치 (vagrant-vaguest)
$ vagrant plugin install vagrant-vbguest
--> 설치하는 동중 ruby gem 에러가 발생 (nokogiri 관련)

ruby >= 1.9.2 이면 nokogiri 및 ruby 업데이트는 필요없다.
$ sudo gem install nokogiri -v '1.6.6.2'
--> nokogiri 는 루비의 특정 버전 필요로 한다. 즉, >= 1.9.2
--> 루비 업데이트는 링크 참조 : http://stackoverflow.com/questions/3696564/how-to-update-ruby-to-1-9-x-on-mac
