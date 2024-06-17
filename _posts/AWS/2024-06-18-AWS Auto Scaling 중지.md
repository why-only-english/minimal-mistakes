---

layout: single
title: "AWS Auto Scaling 중지"
toc: true
toc_sticky: true
toc_label: " "
categories: AWS

---

돈이 너무 많이 나와서 인스턴스 중지하려고 했더니 인스턴스가 무한으로 증식한다. 명륜진사갈비급Auto Scaling 설정을 해놔서 그렇다. 

![image](https://github.com/why-only-english/why-only-english/assets/114092152/30018815-43cf-4952-b051-654122b8a9b8)

프로세스를 중지하는 방법은 다음과 같다

1. https://console.aws.amazon.com/ec2/에서 Amazon EC2 콘솔을 열고 탐색 창에서 **Auto Scaling Groups**(Auto Scaling 그룹)를 선택합니다.
2. Auto Scaling 그룹 옆의 확인란을 선택합니다.
    
    페이지 하단에 분할 창이 열립니다.
    
3. **세부 정보(Details)** 탭에서 **고급 구성(Advanced configurations)**, **편집(Edit)**을 선택합니다.
4. **Suspended processes(일시 중지된 프로세스)**에서 일시 중지할 프로세스를 선택합니다.
5. **업데이트**를 선택합니다.

일단 나머지 기능은 잘 몰라서 아래 두 프로세스만 중지시켰다. 

`ReplaceUnhealthy`, `HealthCheck` 

지금 노드 그룹이 3개인데 그룹별 용량을 3개로 해놔서 결국 최대 개수(3x3=9)까지 켜져버렸다. 다음엔 빨리 중지해야겠다. ~~이거 돈이 또 얼마나 나왔을지..~~

![image](https://github.com/why-only-english/why-only-english/assets/114092152/478b9973-4a18-4dd1-afbb-5ccf4ec4679c)

![image](https://github.com/why-only-english/why-only-english/assets/114092152/cb415a46-f8d8-43bd-baa2-87e80ff92e32)