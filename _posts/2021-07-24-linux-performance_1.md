---
layout: post
title: "[Linux]성능튜닝, 툴"
date: 2021-07-24T00:35:55-05:00
author: Lee
categories: Linux-Performance
---

일반적인 두 가지 성능 측정값   
처리량  
	처리량은 주어진 시간에 리소스에서 전송하거나 처리할 수 있는 데이터 양을 측정한 값  

대기시간  
	대기 시간은 리소스에서 데이터 전송 또는 처리를 시작하기 위해 기다려야 하는 지연 시간  

리소스 종류  
  CPU 리소스:  
  CPU는 대기열과 우선 순위를 처리하는 처리 리소스입니다. CPU 성능 측정을 위한 일반적인 지표는 사용률, 부하 평균, 대기열 평균입니다.
  메모리 리소스:  
  메모리는 용량 리소스입니다. 성능 측정을 위한 일반적인 지표는 사용 가능한 용량, 처리량, 오류입니다.  
  스토리지 리소스:  
  스토리지 장치는 용량 및 I/O 리소스입니다. 스토리지 장치 성능을 측정하기 위한 일반적인 지표는 사용가능한 공간, IOPS(초당 I/O 작업 수), I/O 대기 시간, 처리량입니다.
  네트워크 리소스:  
  네트워크 장치는 I/O 리소스로 간주되며, 성능 측정을 위한 일반적인 지표는 처리량, 왕복 시간, 대기 시간, 패킷 손실, 오류, 충돌입니다.

시스템 성능분석  
  USE(Utilization Saturation and Errors) 방법  

<p>
<img src="/assets/linux-performance/20210724/usemethod_flow.png">
</p>
USE성능분석흐름  

{% include page_divider.html %}

측정단위  

SI(International System of Units) 10진수  
	kilo- (k) = 10^3 = 1,000
	mega- (M) = 10^6 = 1,000,000
	giga- (G) = 10^9 = 1,000,000,000
	tera- (T) = 10^12 = 1,000,000,000,000
	peta- (P) = 10^15 = 1,000,000,000,000,000
	exa- (E) = 10^18 = 1,000,000,000,000,000,00

IEC(International Electrotechnical Commission) 바이너리 (파일 시스템은 바이너리 구조로 사용함)  
	kibi- (Ki) = 2^10 = 1,024
	mebi- (Mi) = 2^20 = 1,048,576
	gibi- (Gi) = 2^30 = 1,073,741,824
	tebi- (Ti) = 2^40 = 1,099,511,627,776
	pebi- (Pi) = 2^50 = 1,125,899,906,842,624
	exbi- (Ei) = 2^60 = 1,152,921,504,606,846,976

<p>
1기가비트 네트워크 카드가 전송할 수 있는 데이터 양? 
1 Gb/s × 10^9  b/Gb ÷ 8 bits/1 Byte ÷ 2^20  Bytes/1 MiB = about 119.2 MiB/s
</p>


모니터링 툴  

sysstat(Linux 시스템 성능 모니터링 유틸리티)  
	mpstat(CPU 사용량 모니터링)
	iostat(디스크 I/O 사용량 모니터링)
	pidstat(프로세스 사용률 모니터링)
	sar(시스템 활동 보고서 생성)
	tapestat,cifsiostat


procps패키지  
	vmstat(프로세스 사용률 모니터링)
	free,top

Performance Co-Pilot(모니터링, 시각화, 저장, 분석하기 위한 툴)  


### Reference Site  
[https://www.brendangregg.com/usemethod.html](https://www.brendangregg.com/usemethod.html)

