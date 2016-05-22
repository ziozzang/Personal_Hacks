# TL;DR
샤오미 중국판 CCTV(IP캠)은 중국외 지역에서 사용이 불가능 한데, 이를 최신 버전의 펌웨어에 패치 하여 한국내에서 사용 하는 방법을 정리 한다.


# 기본 이론
샤오미 카메라는 한국에서 사용을 할수 없다. 이는

최신 버전의 펌웨어에서는 이를 사용 할수 없고, 펌웨어 다운그레이드도 제대로 동작 하지 않는다. 즉, 알려진 방법으로는 동작을 시킬수 있는 방법이 존재 하지 않는다.
예를 들어 이전 버전의 경우에는 중국이외의 지역에서는 사용 할수 없다는 메시지가 나오면서 중지 해버리게 된다.

# 최신 기기의 확인
최신 하드웨어 기기의 경우 다운그레이드시 wifi 연결시도시 패스워드 타임아웃등의 문제로 동작 자체가 불가능하게 된다.

기기가 언제 제조되었는지 확인하려면 카메라 뒤의 QR코드를 찍어 보면, 맨 마지막의 6자리가 제조일자이다. 이 제조일자가 특정 일자 이후면 다운그레이드시 와이파이 페어링 자체가 지속적인 오류가 발생하여 불가능 하다.
기기의 버전은 시리얼넘버의 맨앞 네자리인데, 장치 버전 (구버전은 11/신버전 12) 다음은 국가 코드이다. 국가 코드는 KR / TW /US /CN 등이 있고 이는 페어링 앱의 특성을 타게 된다.

* 이하 작성중
# 펌웨어 업그레이드 방법
Home 이라는 파일은 펌웨어 파일인데 이를 SD카드의 루트에 넣은후, 전원을 켜면 다음의 순서로 펌웨어 업그레이드를 알리게 된다.
```
깜빡깜빡(노랑) : 펌웨어 업데이트 준비
계속 켜짐(노랑) : 중간에 셔터 소리 && 중국어로 쏼라.
깜빡깜빡(노랑) : 페어링 레디.
```

# 하드웨어 리셋 방법
SD 카드 빼고, 전원을 연결후에 리셋핀홀을 6-8초간 눌러준다. 그리고 전원을 뽑으면 리셋 완료.

# 부트로딩시 Hack
펌웨어 해킹을 하는 도중에 특정 커맨드를 실행하는 방법이 존재 한다. SD카드의 test/equip_test.sh 를 작성하는데, 이는 쉘 파일의 형식이다.

아래의 내용을 넣어 주면, 부팅될때 텔넷 서버가 실행 된다.
```
#!/bin/sh
# /test/equip_test.sh on SDcard root.
# Telnet
if [ ! -f "/etc/init.d/S88telnet" ]; then
echo "#!/bin/sh" > /etc/init.d/S88telnet
echo "telnetd &" >> /etc/init.d/S88telnet
chmod 755 /etc/init.d/S88telnet
fi
dr=`dirname $0`
# fix bootcycle
mv $dr/equip_test.sh $dr/equip_test.sh.moved
reboot
```

# 시간 설정
한국 시간은 베이징 시간 기준에서 차이를 구해야 함. (이게 샤오미 장비임을 생각하자...)

/etc/TZ 에 GMT-1 을 설정함. (웃기지만 이렇게 세팅해야함...)

# 설정
텔넷으로 접근 한 뒤 콘솔에서 다음의 명령을 실행 하여 설정 한다.

* 텔넷 Id: root / PW: 1234qwer


```
# 아래는 프로세스 죽이기.
ps | grep /home/watch_process | grep -v "grep" | awk '{print $1}' | xargs kill -9
ps | grep /home/cloud | grep -v "grep" | awk '{print $1}' | xargs kill -9
# 체크 URL을 의미 없는 주소로 교체 하기.
sed -i  's|api.xiaoyi.com/v4/ipc/check_did|api.xiaoyi.cox/v4/ipc/check_did|g' /home/cloud
# 재부팅
reboot
```

# 타임존 해킹
작성중.

한국의 타임존과 관련된 수정

# 내부 구조의 분석

작성중.


# RTSP 프로토콜 활성화
1.8.6.1B 기준, 최신 버전의 경우에는 http://xiaoyi.querex.be/ 페이지 에서 1.8.5.1M_test-rtspfix-4.zip (RTSP enable script) 를 받아서 test/rtspsvrM 파일을 /home/rtspsvr 로 리네이밍후 덮어 쓰기 해준다. 재부팅후 잘 작동함.

테스트는 rtsp://IP_ADDR:554/ch0_0.h264 로 해 본다. 다음 팟플레이어에서 동작 잘됨

# 시놀로지 연동
* https://forum.synology.com/enu/viewtopic.php?t=95211 참고. 테스트 결과 아주 잘 작동한다.

# 타 사오미 환경과 동시 사용
작성중.

샤오미 홈 IoT 어플라이언스를 구축 한다면 Mi Home 에서 서버 위치를 중국으로 설정 하여야 한다. 즉 한국버전 캠을 사용하지 못한다는 것을 감안해야 한다.

# 참조
 * http://diy.2pmc.net/solved-xiaomi-xiao-yi-ant-home-camera-can-used-china/#more
 * http://xiaoyi.querex.be/
 * http://en.miui.com/thread-196713-1-1.html
 * https://disqus.com/home/discussion/twigstechtips/twigs_tech_tips_setting_up_xiaomi_yi_small_ants_intelligent_camera_home_ip_camera/
