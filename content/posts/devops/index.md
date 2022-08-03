---
title: "NL DevOps팀의 첫 AWS 인프라 설계기"
date: 2022-07-29T16:38:49+09:00
categories: ["DevOps"] #카테고리 설정
tags: ["devops","aws","ec2","iam","vpc","elb","route53"] #태그 달기
author: ["newdeal123","TProgramer","Hanalin0422","Moojun"] #본인 이름 넣기  
# author: ["Me", "You"] # 공동저자 일 경우
description: "NL 동아리의 홈페이지의 견고하고 안전한 클라우드 인프라를 위해 설계하고 개발한 내용들을 공유해드려요. " #설명 넣기

showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
disableHLJS: true 
disableShare: false
disableHLJS: false  
hideSummary: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowCodeCopyButtons : true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
ShareButtons: []
---

# NL DevOps팀의 첫 AWS 인프라 설계기

![hello-there-private-from-penguins-of-madagascar.gif](https://network-leader.s3.ap-northeast-2.amazonaws.com/blog/1/hello-there-private-from-penguins-of-madagascar.gif)

 안녕하세요. NL 동아리 홈페이지 제작 프로젝트에서 DevOps 및 AWS 인프라 설계, 개발을 맡고 있는 DevOps팀이에요. 저희 팀 블로그의 첫 포스팅으로 저희 DevOps팀의 첫 AWS 인프라 설계하는 과정을 설명해드리려 해요. 클라우드 인프라 설계는 간단하게 진행하면 별 것 아닐 수도 있지만, 클라우드 서비스 하나하나 깊게 파고 적용시킬수록 가용성, 보안성을 높이고 비용 효율적으로 인프라를 고도화 시킬 수도 있습니다. 저희 팀에서 AWS를 사용해 웹 서비스의 인프라를 어떻게 설계하고 개발했는지, 차근차근 설명해 드릴게요.

## AWS IAM 유저 생성 및 권한

 

 가장 먼저 AWS 인프라를 개발할 때 필요한 절차는 무엇일까요? EC2 인스턴스 만들기? VPC 만들기? 정답은 루트계정으로 부터 최소 권한이 담긴 AWS IAM 유저를 생성하고, MFA를 적용시키는 일이에요. 모두가 루트계정으로 접속해 모든 권한으로 동시에 작업하는 행동은 아주 위험하고, AWS에서도 권장하지 않는 방식이에요. 

![1_-oFXKPUWQAERp1BZ7rR-IQ.gif](https://network-leader.s3.ap-northeast-2.amazonaws.com/blog/1/1_-oFXKPUWQAERp1BZ7rR-IQ.gif)

이를 위해 가장 널리 사용되는 접근 방식은 [RBAC](https://en.wikipedia.org/wiki/Role-based_access_control) (역할 기반 액세스 제어, **[Role-Based Access Control](https://internaldeveloperplatform.org/core-components/role-based-access-control/)**) 입니다. DevOps팀에게는 팀 역할에 맞는 제어 권한을, Back-end팀에게는 그 팀 역할에 맞는 제어 권한을 줘야 하죠. AWS에는 권한이 담긴 사용자를 그룹별로 제어할 수 있도록 기능을 제어해 줍니다. 

![Untitled](https://network-leader.s3.ap-northeast-2.amazonaws.com/blog/1/Untitled.png)

그룹에 여러가지 권한을 할당시키고, 그 그룹에 IAM 유저를 배정시킨다면 편리하게 그룹 별로 최소권한을 관리할 수 있게 됩니다. 특히 이후에는 AWS X-Ray, CloudTrail을 활용해 각 사용자별 활동 로그를 확인할 수 있어 더 효율적인 관리가 가능하게 됩니다. 

 이제 DevOps 팀원 각각에게 IAM 유저를 할당해주었으니, 팀원 각각은 보안 강화를 위해 [MFA](https://docs.aws.amazon.com/ko_kr/IAM/latest/UserGuide/id_credentials_mfa.html)를 설정해 주어야 합니다. MFA는 멀티 팩터 인증으로, AWS 웹 사이트 콘솔에 로그인을 시도할 때 모바일 디바이스 등으로 2차 인증을 거쳐야 로그인이 가능하게 합니다. 

![Untitled](https://network-leader.s3.ap-northeast-2.amazonaws.com/blog/1/Untitled%201.png)

아무리 계정에 최소 권한을 설정하고 비용을 모니터링 하더라도 보안에 대한 대비는 가능한 한 최대로 하는 것이 좋기에, 인프라에 대한 보안 위협을 보호하기 위해 MFA는 꼭 활성화 해주는 것이 중요해요.

## AWS VPC CIDR 설계하기

다음으로 AWS 에서 큰 틀이라고 할 수 있는 VPC 를 설계해보려고 해요.

‘Virtual Private Cloud’ 라는 이름에서 보이듯이 VPC 는 AWS 에서 서버를 생성하여 사용하기 위해서는 일반 온프레미스 환경에서 사설망을 구축하는것과 동일하게 네트워크 설계가 필요해요.

이는 크게 어떤 IP대역을 선택할 것 인가와 어떻게 Subnet 을 나눌 것 인가에 대해 정의하는 단계로 나눠볼 수 있어요.

우선 첫 번째로 어떤 네트워크 IP대역을 사용할 것인지를 정해보았어요.

여기서 VPC 는 사설 네트워크 망이니 당연히 공인 IP 가 아닌 사설 IP 를 기준으로 정해야 해요.

통상적으로 사용가능한 사설 IP대역은 아래와 같아요.

![Untitled](https://network-leader.s3.ap-northeast-2.amazonaws.com/blog/1/Untitled%202.png)

이 중 저희 팀은 Class C를 선택했어요.

하지만, 여기서 현재 몇개의 서버가 필요하며 향후 서버가 얼마나 늘어날 것 인가를 추가로 고려해야해요.

이번 프로젝트의 경우, 동아리 홈페이지를 위한 환경이니 미래에도 많은 서버를 필요로 하지않을 것 같아  192.168.0.0/24 의 네트워크 대역을 설정했어요.

다음 두 번째로는 Subnet 을 어떻게 나눌 것인지, 즉 Subnetting 방식을 정해야 해요.

여기서 Subnetting 이란 IP블록을 더 작은 그룹으로 분할하는 것을 말해요. 이렇게 Subnet 을 나누는 이유는 용도 별로 네트워크의 관리적인 측면도 있지만 가장 큰 이유는 보안 이슈 때문입니다.

Subnetting 을 통해서 VPC안에서도 외부에서 접근가능한 Public Subnet, 외부에서 접근이 불가능한 Private Subnet 으로 구분할 수 있어요. 일반적으로 웹/앱 서비스를 구동하기위한 서버가 필요하다고 한다면 Public Subnet 2개, Private Subnet 4개로 총 6개의 Subnet을 사용하지만, 저희는 규모가 큰 서비스가 아니기 때문에  줄여서 2개 / 2개로 설정했어요.

 그러면 Public Subnet 과 Private Subnet 의 대역은 각각 ‘192.168.**10**.0/24’, ‘192.168.**11**.0/24’, ‘192.168.**12**.0/24’, ‘192.168.**13**.0/24’ 로, 각 Subnet 마다 251 개의 IP 를 할당해줄 수 있게돼요.

여기서 할당가능한 IP 의 개수가 256 개가 아닌 251 개인 이유는, 첫 4개의 IP 와 마지막 IP 주소가 AWS 에서 예약 해놓은 주소 이기 때문이에요. 예를 들어 192.168.10.0/24 CIDR 블록의 Subnet 에서는 다음 5개 IP 주소가 예약되어 있어요.

| IP | 기능 |
| --- | --- |
| 192.168.10.0 | 네트워크 주소 |
| 192.168.10.1 | AWS에서 VPC 라우터용으로 예약한 주소 |
| 192.168.10.2 | AWS에서 예약한 주소 DNS 서버의 IP 주소는 항상 VPC 네트워크 범위를 기초로 2를 더한 것 (하지만 AWS는 각 서브넷 범위의 기준 + 2도 예약) |
| 192.168.10.3 | AWS에서 앞으로 사용하려고 예약한 주소 |
| 192.168.10.255 | 네트워크 브로드캐스트 주소 (VPC에서는 브로드캐스트를 지원하지 않으므로, 이 주소를 예약) |

이렇게해서 각 서브넷에서 사용할 수 있는 IP 5개가 줄어 들게 되는 것이랍니다.

![AgitatedOblongBanteng-size_restricted.gif](https://network-leader.s3.ap-northeast-2.amazonaws.com/blog/1/AgitatedOblongBanteng-size_restricted.gif)

마지막으로 각 Subnet 묶음(Public, Private)을 서로다른 가용영역인 ap-northeast-2d 와 ap-northeast-2c 에 나누어서 프로젝트의 가용성을 확보해 줄거에요. 이렇게 해서 최종 결정한 VPC의 다이어그램을 아래와 같이 정리해보며 마무리 해봤어요! 

![NL-VPC.drawio.png](https://network-leader.s3.ap-northeast-2.amazonaws.com/blog/1/NL-VPC.drawio.png)

## AWS EC2 생성하기

 AWS는 Amazon Web Services의 약자인건 다들 아실거에요. AWS는 말 그대로 웹 서비스에 대한 여러가지 서비스를 제공해주는 확장 가능 컴퓨팅 용량을 제공해주는 클라우드 서비스인데 그 중 가장 익숙하고 널리 알려진 서비스가 바로 EC2일 것 같아요.

![Untitled](https://network-leader.s3.ap-northeast-2.amazonaws.com/blog/1/Untitled%203.png)

 AWS EC2는 Amazon Elastic Compute Cloud의 약자로 500개가 넘는 인스턴스, 그리고 최신 프로세서, 스토리지, 네트워킹, 운영 체제 및 구매 모델의 옵션과 함께 워크로드의 요구 사항에 가장 잘 부합할 수 있도록 가장 포괄적이고 심층적인 컴퓨팅 플랫폼을 제공하는 서비스에요. Amazon EC2를 사용하면 원하는 수의 가상 서버를 구축하고 하드웨어에 선투자할 필요없이 더 빠르게 애플리케이션을 배포하고 개발할 수 있답니다.

사실 이런 저런 설명을 해도 그냥 간단히 말하면 EC2는 가상의 컴퓨터이며 우리는 이 가상의 컴퓨터를 가지고 여러가지 프로그램을 설치해서 사용한다고 보시면 될 것 같아요 :) 그리고 우리는 그 컴퓨터 단위를 인스턴스라고 부른답니다.

EC2 인스턴스를 생성하는 법은 간단해요. 어떻게 생성하는지 가볍게 알아볼까요?

1. 우선 [https://console.aws.amazon.com/ec2/](https://console.aws.amazon.com/ec2/) 에서 Amazon EC2 콘솔을 열어요.
    
    ![스크린샷 2022-07-29 오후 3.24.23.png](https://network-leader.s3.ap-northeast-2.amazonaws.com/blog/1/_2022-07-29__3.24.23.png)
    

그러면 다음과 같이 사이드바에서 인스턴스 관련된 여러가지 목록들을 확인할 수 있는데 여기서 인스턴스를 클릭합니다. 

![스크린샷 2022-07-29 오후 3.26.54.png](https://network-leader.s3.ap-northeast-2.amazonaws.com/blog/1/_2022-07-29__3.26.54.png)

그러면 이렇게 생성한 인스턴스를 확인할 수 있는 화면과 오른쪽 상단 위에 인스턴스 시작버튼을 확인할 수 있어요. 여기서 인스턴스 시작을 누르시면 인스턴스를 생성할 준비가 절반은 다 이루어 졌답니다!

1. 인스턴스 시작 버튼을 누르면 먼저 어떤 기능을 수행할 컴퓨터를 생성할 것인지 사용목적에 맞게 이름을 지정해주세요. 저희는 Front-end, Back-end 두 팀이 진행하고 있기 때문에 두 개의 인스턴스를 생성하여 각각 NL_AWS_FE, NL_AWS_BE라고 이름을 지정했습니다.
    
    ![스크린샷 2022-07-29 오후 3.29.53.png](https://network-leader.s3.ap-northeast-2.amazonaws.com/blog/1/_2022-07-29__3.29.53.png)
    

1. 이름을 지정한 후에 인스턴스의 운영체제를 정해주세요. 저희는 처음에 Amazon Linux를 선택했다가 CI/CD작업중에 깃허브 액션의 가상 환경이 우분투를 지원해서 중간에 우분투로 운영체제를 바꿨습니다. 해당 내용은 이후에 CI/CD 관련 포스팅에서 자세하게 설명드릴게요.
    
    ![스크린샷 2022-07-29 오후 3.34.04.png](https://network-leader.s3.ap-northeast-2.amazonaws.com/blog/1/_2022-07-29__3.34.04.png)
    
2. 인스턴스 유형은 t3.medium을 선택했어요.
    
    ![스크린샷 2022-07-29 오후 3.55.39.png](https://network-leader.s3.ap-northeast-2.amazonaws.com/blog/1/_2022-07-29__3.55.39.png)
    
3. 키 페어는 새로 생성하여 꼭 저장해주세요! 키 페어가 없으면 인스턴스에 접근이 어려워요.
4. 나머지는 표준으로 하고 인스턴스 생성을 눌러주시면 인스턴스 생성이 완료 된답니다!
    
    ![스크린샷 2022-07-29 오후 3.58.41.png](https://network-leader.s3.ap-northeast-2.amazonaws.com/blog/1/_2022-07-29__3.58.41.png)
    

![Untitled](https://network-leader.s3.ap-northeast-2.amazonaws.com/blog/1/Untitled%204.png)

자, 그럼 인스턴스 생성을 완료했으니 접속을 해봐야겠죠!?

생성한 키 페어가 있는 파일 위치에서 터미널에 접속을 시도해주세요.

```bash
chmod 400 NL_AWS_BE.pem # Key 파일 읽기 권한 부여
ssh -i NL_AWS_BE.pem ec2-user@<public ip> # AWS EC2에 SSH로 접속
```

그런 다음 위와 같은 명령어를 입력하면 인스턴스에 접속할 수 있습니다.

이후 자유롭게 필요한 프로그램을 깔거나 파일을 올려 원하시는 대로 사용하면 되요. 저희는 이렇게 생성한 EC2 인스턴스에 각각 Front-End와 Back-End 팀의 깃허브를 clone하여 파일을 올린뒤 pm2를 설치하여 무중단 서비스 환경을 만들어 사용하고 있답니다 :)

## Route 53, ELB 그리고 대상 그룹 설정하기

 또 DevOps Team에서는, `[www.network-leader.com](http://www.network-leader.com)` 이라는 도메인 이름을 홈페이지 제작에 사용하기로 결정했어요. 이를 위해 AWS Route 53 서비스를 사용했는데, Route 53이 무엇인지 알아봅시다. 

 Route 53이란, 높은 가용성과 확장성이 뛰어난 클라우드 [Domain Name System (DNS)](https://aws.amazon.com/ko/route53/what-is-dns/) 웹 서비스입니다. 이 서비스에서는 www.network-leader.com과 같은 이름을 192.0.2.1과 같은 컴퓨터 간 연결에 사용되는 숫자 IP 주소로 변환해줍니다.

이를 위해, 먼저 `www.network-leader.com` 이름의 domain을 AWS에서 구매했어요. 

- AWS console > Route 53 > Domains > Register Domain 클릭
- 등록하고자 하는 domain 이름을 선택하고, 결제에 필요한 domain 구매자의 개인 정보(연락처, e-mail) 등을 입력 후 [Complete Purchase] 를 눌러서 도메인 등록 수수료를 결제
- 결제 이후, domain 구매시 등록한 e-mail로 구매 내역을 받을 수 있으며, AWS 내에서는 아래와 같이 확인이 가능해요.

![Screen Shot 2022-07-29 at 3.06.26 PM.png](https://network-leader.s3.ap-northeast-2.amazonaws.com/blog/1/Screen_Shot_2022-07-29_at_3.06.26_PM.png)

이후, AWS Route 53 서비스를 통해 내가 구매한 domain 이름을 domain name server로 등록했어요.

- AWS console > Route 53 > Hosted zones > Create hosted zone 클릭
- Domain name을 위에서 만든 domain name을 선택하고, AWS내에서가 아닌 Internet web에서 동작하도록 하기 위해 Type은 Public hosted zone으로 체크. 이후 하단의 Create 버튼을 누르면 아래 그림과 같이 Domain Name Server가 생성이 됩니다.
    
    ![Screen Shot 2022-07-29 at 3.06.26 PM.png](https://network-leader.s3.ap-northeast-2.amazonaws.com/blog/1/Screen_Shot_2022-07-29_at_3.06.26_PM%201.png)
    

 

다음으로, Route 53과 다른 AWS 서비스들을 연결해줘야 하는데요, 저희는 AWS 서비스 중 부하분산 등의 기능을 서비스 해주는 ELB(Elastic Load Balancer)를 Route 53과 연결시켜줬어요. ELB를 통해, 현재 도메인이 호스팅 하고 있는 2개의 EC2 인스턴스(NL_AWS_FE, NL_AWS_FE2)의 트래픽을 인스턴스 간에 분산시켜 줄 수 있어요.

- AWS console > EC2 > Load Balancers 에서 Load Balancer를 생성했고, 이후 Route 53 > Hosted zones에서 network-leader.com의 Record를 생성하여 ELB와 Route 53을 연결했어요
- 이후, ELB의 대상 그룹에 EC2 인스턴스 NL_AWS_FE 및 NL_AWS_FE2를 추가해줬어요. 그리고 ‘Healthy Check’ 기능을 통해, 10초 간격으로 이상 징후를 탐지하도록 설정했어요.

![1_0_AxDGA6rGF8ESMifA6j8w.gif](https://network-leader.s3.ap-northeast-2.amazonaws.com/blog/1/1_0_AxDGA6rGF8ESMifA6j8w.gif)

이 ELB를 통해 여러 AZ(가용 영역)에 배치된 인스턴스들이 정상적으로 작동하는지 지속적으로 체크를 하고, 작동하지 않는 인스턴스에는 트래픽을 보내지 않게 되면서 가용성을 증가시켜 줄 수 있습니다.

## ****실제 개발 후기와 앞으로 해야할 과제****

 

팀원 대부분이 안정적이고 정교한 클라우드 인프라 설계가 처음이라 새로 학습하고 적용해야 할 기능들이 많았지만, 동료들 끼리의 협업과 뜨거운 열정으로 성공적으로 개발을 마무리 지을 수 있었습니다. 

 

 DevOps라는 이름을 달고 시작한 이후로 UX를 넘어 프로젝트 팀원 전체의 DX를 목표로 해야 할 일들이 참 많은데요, 새로운 요구 사항이나 이슈가 발생해도 기존 인프라를 어떻게 리팩토링할까 어떤 방식으로 자동화를 할까 하고 고민하는 것이 즐거운 부분중 하나입니다. DevOps 팀원 전체도 AWS 인프라를 더 잘 이해하고, 더 효율적이게 사용하기 위해 열심히 공부하고 토의하고 있으니 앞으로의 포스팅들도 기대해주시길 바랍니다.

그러기에 앞으로 수행해야 할 과제들은 다음과 같다고 생각합니다.

- Front-end, Back-end 서버 CI/CD 파이프라인 구축
- Cloudwatch로 인스턴스 사용량 감시 및 알람, 위기 대응
- ELB에 SSL 인증서 할당으로 http→https 연결 활성화
- Back-end 인스턴스 및 DB 프라이빗 서브넷 이전, Internet Gateway, NAT Gateway 연결

![devops.gif](https://network-leader.s3.ap-northeast-2.amazonaws.com/blog/1/devops.gif)

궁금한 점이나 후기들을 댓글로 달아주시길 바랍니다.

부족한 글을 읽어주셔서 감사합니다!
