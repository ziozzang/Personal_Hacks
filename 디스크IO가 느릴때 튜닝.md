
# TL; DR
커널을 튜닝하고, 몽땅 램에 올려버려!

# 원리 설명
설명 대상은 클라우드앳코스트(Cloud@cost ; cloudatcost)

이게 하이퍼바이저로 VMWare 를 쓰는데 이경우 벌루닝이라고 오버 커밋을 하는 단계가 있습니다. 요거 사용률이 떨어지면 스스로 판단하기에 우선순위가 밀리는 것으로 보고 메모리나 뭐 이것저것 전부다 스왑아웃 해버립니다. 그러면? 강제로 스왑아웃 안하게 합니다. 그리고 왠만한건 메모리에서 가져 올수 있도록 죄다 커널 옵션을 튜닝합니다.

# 설정 방법
rc.local 에 다음과 같이 설정.

```
echo 10 > /proc/sys/vm/vfs_cache_pressure
echo 1 > /proc/sys/vm/swappiness
echo 99 > /proc/sys/vm/dirty_ratio
echo 50 > /proc/sys/vm/dirty_background_ratio
echo 60000 > /proc/sys/vm/dirty_expire_centisecs
echo 60000 > /proc/sys/vm/dirty_writeback_centisecs
bash /etc/loops > /dev/null 2> /dev/null &
```

/etc/loops 에는 다음과 같이 설정.
```
#!/bin/bash
for (( ; ; ))
do
(nice find / -type f -and -not -path '/sys/*' -and -not -path '/proc/*' -print0 2>/dev/null | nice ionice -c 3 wc -l --files0-from - > /dev/null)
sleep 10
done
```

# 결론
이건 그냥 시스템 튜닝 할때 하는 짓인데, 왠만하면 죄다 캐쉬로 돌도록 해버리는 겁니다 (....) 이걸 10초마다 계속 돌아가면서 해버리면 (사실 캐쉬빨이 어느정도 계속 살도록 리프레쉬 하는 것도 있고) CPU도 얼마 안먹고 디스크 IO도 그닥 안먹어요. 단지 메모리가 남아 돌면 이짓 하면 됩니다. 즉, 클라우드앳 코스트 같이 너무 느려서 못쓸 물건을 이렇게 쓰면 된다는 거죠... 이렇게 하면? 겁나 쾌적해짐....
