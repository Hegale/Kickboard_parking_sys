## 🚲RTK를 이용한 전동킥보드 자동 주차관리 시스템

> cm 단위의 정확도를 얻을 수 있는 정밀측위를 통해 공용 킥보드의 주차 강제를 실현하는 프로그램. 속도와 이동 거리 계산, 대상 데이터의 오차수준 판별을 포함한다.  
> RTK 정밀측위 사용을 위해 RTKLIB의 결과 파일을 필요로 한다. 주차 가능 구역과 불가능 구역을 geojson 형식의 파일을 통해 지정할 수 있다.
  
#### 🚩RTK(Real Time Kinematic)란 무엇인가?
 >  높은 수준의 정확도를 확보하기 위해 후처리가 필요했던 기존 측량 방식과는 다르게, 네트워크를 통해 기준국으로부터 실시간으로 보정정보를 수신하여 cm 단위의 정확도를 확보하는 측량기술. RTK 수신기를 통하여 실시간으로 높은 정확도의 위치정보를 얻을 수 있다.
   
    
### 발단
> - 전동 킥보드 대여 시스템은 비교적 최근에 등장하여 젊은 층의 폭발적인 수요로 급성장했으나, 교통사고나 인도 위의 무분별한 주정차로 인한 통행방해와 같은 여러 문제점들을 낳았다.  
> - 이미 킥보드에 GPS가 내장되어 있으나, 낮은 정확도로 인하여 새로운 사용자가 킥보드를 찾는 데 어려움이 따르는 경우가 존재한다.  
> - 별도의 주차구역이 지정되어 있지 않은 경우가 대부분이며, 몇 자치구에서 적용하는 주차구역은 이용자에게 강제성을 부여하지 않는다.  
> 👉지정 주차구역 안에 킥보드가 있어야만 요금 정산이 가능한 프로그램 고안 : 목표는 3분 이내 주차  
  
### GUI
![캡처](https://user-images.githubusercontent.com/92227496/139965810-12273705-1ae9-4e4c-9b3d-7a9860f6d4ee.JPG)

### 입력 파일 관리  
> - 사용자 위치 파일 (필수)  
>   : RTKLIB의 결과 데이터로, 초 단위로 사용자의 위치가 기록된 pos 형식의 파일을 입력으로 받는다.  
> - 주차 가능 구역 폴리곤 선택 (필수)  
>   : geojson 형식의 폴리곤 파일을 입력으로 받는다.  
> - 주차 불가능 구역 폴리곤 선택 (옵션)  
>   : geojson 형식의 폴리곤 파일을 입력으로 받는다.  
  
## 기능
  
### 주차 관리 프로그램 
> Manager 클래스에서 관리한다.
>   - 사용자의 위치를 availCount 변수의 수치로 판단하여 출력한다. 위치 정보의 신뢰성은 Q값으로 판단한다.  
>   (Q = 1 : 해가 픽스되어 가장 정확도 높음, Q = 2 : float해로 신뢰도가 떨어짐, Q = 5 : 네트워크 연결 불안정으로 인해 single 로 전환하여 구한 결과)  
>   - Q = 1일때 availCount += 2, Q = 2일때 availCount -= 1, 그 외의 상황에서는 availCount의 값이 0으로 초기화된다. availCount 값이 10 이상이 되면 주차 완료로 간주하고 프로그램을 종료함.  
> 결과 예시  
    ![출력예시](https://user-images.githubusercontent.com/92227496/139967420-f8102fee-1d8d-49b5-8fa2-b5e47908ac1f.jpg)  
  
### 벌금 부과 프로그램
> FineManager 클래스에서 관리한다. ECEF 좌표계 기준.  
>   - 제한 속도를 입력하고, 프로그램 최소 실행 조건을 갖췄을 때 실행되며, 벌금 현황 textbox에 제한 속도 초과 여부가 표시된다.  
>   - 프로그램이 종료되면 부여된 총 벌금을 출력  
  
### RMS 측정 프로그램
> RMS 클래스에서 관리한다.
>   - 사용자 위치정보의 정확도를 수치로 판단하기 위해 RMS 계산을 시행한다. 별도의 rms 측정 GroupBox에서 ECEF 좌표계 기반 사용자 위치 파일을 선택하고, 기존 입력 파일 관리 GroupBox에서 경위도 좌표계 기반 사용자 위치 파일을 선택한 후 rms 확인 버튼을 눌러 rms 값을 확인할 수 있다. (아래 예시)
  ![2_RMS](https://user-images.githubusercontent.com/92227496/139968861-8e867d61-c52e-4455-8891-3aa205513a68.PNG)  

## 테스트 데이터  
> 1. 전동킥보드 전용 주차구역을 시범운영하고 있으며, 송파 관측소와 짧은 기선거리를 확보할 수 있는 송파구를 대상지역으로 선정하였다.  
> ![대상지역](https://user-images.githubusercontent.com/92227496/139969023-7228b0fd-c7e3-456f-969f-4b59f40e8a3a.png)  
> (위는 송파구에서 제공한 전동킥보드 주차장 위치)
>   
> 2. 예시로 쓰일 사용자 위치 정보는 주차장 한가운데와 멀리 떨어지지 않은 주변 거리 등 총 열두 곳에서 각 3분 간 측정하였으며, 붉은 색으로 표시된 포인트는 킥보드 주차장 안에서 측정하였다.  
> ![잠실새내_잠실](https://user-images.githubusercontent.com/92227496/139969354-08c82a7f-9fc5-4fdb-b66e-4d0d83650ae6.JPG)  
> (왼쪽 : 잠실새내역 부근, 오른쪽 : 잠실역 부근)  
>   
> 3. 주차장 폴리곤은 QGIS를 활용해 앞서 주차장에서 측정한 사용자 위치 데이터를 기반으로 사각형의 폴리곤을 생성해서 사용하였다.  
>   - 가로와 세로 중 더 큰 길이를 기준으로 8~10m 수준 크기의 폴리곤부터 시작하여, 네 번의 테스트 동안 2~3m 수준으로 크기를 줄임  
>   - 당연히 주차장 폴리곤의 크기가 줄어들수록 주차 가능 여부를 판단하는 시간이 늘어났으며, 네 번째 테스트에서 다섯 개 중 두 개 샘플이 1분을 초과함  
>   - test3까지는 주차장 안의 포인트 다섯 개 모두 45초 안에 주차를 완료하였다.  
>     
> 4. 테스트 데이터를 통한 결론    
> ![결론](https://user-images.githubusercontent.com/92227496/139969856-b626944d-f357-4ebe-97ce-427649f09d66.png)  
>   - RTKLIB을 통한 사용자 위치정보는 파장이 짧은 위성 정보를 이용한다는 한계 탓에 건물이 있는 방향에 의해 영향을 받으며, 가로수와 같은 구조물도 정확도 저해 요인이 된다. 
>   - 주차장은 이러한 요소들을 고려하여 설치되어야 하며, 도로와 수직한 방향으로 설치되는 것이 시간 단축에 용이해 보인다.
  
## 한계와 개선점  
> 1. 장치 고장
>   - 사용했던 ZED-F9P 모듈이 고장났었다는 것을 프로젝트를 마치고서야 교수님께서 알려주셔서 알게 되었다. 어쩐지... 멀쩡한 모듈을 사용했다면 더욱 작은 오차범위만을 상정한 주차 구역 폴리곤이 가능했을 듯 하다. 많이 아쉽다.  
> 2. 생성된 폴리곤의 정확성  
>   - 폴리곤은 기준 좌표계로 ESPG 5174를 사용하는 서울시 SHP 데이터를 기반으로 직접 그려서 생성하였다. 이 때 변환 과정에서 오차가 발생했는지 판단하지 못함.  
>   - 직접 그린 폴리곤은 신뢰성이 부족하다고 생각함. 시간이 충분했다면 주차장의 네 꼭짓점에서 충분한 시간 동안 데이터를 수집하여, 그 평균을 통해 폴리곤 파일을 생성하는 방법도 사용할 수 있었을 듯 하다.  
> 3. 포인트 판별 알고리즘의 문제  
>   - 이번 프로젝트에서 사용한 Point-in-poly 알고리즘은 convex hull(즉 볼록도형)에만 적용되기 때문에, 복잡한 형태의 폴리곤에는 적용할 수 없다. 도로 전체를 폴리곤으로 지정하여 주차 불가능 구역으로 지정하는 방안도 생각했으나, 지금의 방식을 그대로 쓴다면 불가능함  
> 4. 실현 가능성
>   - 프로젝트에서 사용한 ZED-F9P 모듈이 대략 40만원대, 저렴한 모델도 10만원을 훌쩍 넘어가는 가격을 보인다. 많이 저렴해진 거라지만 모든 킥보드에 당장 적용이 가능하다고 생각하긴 어렵다. 지정 구역을 벗어나 주차된 킥보드의 회수비용 등을 총체적으로 고려하여 생각해야 할 부분인 듯함 
> 5. 벌금 부과 프로그램  
>   : 최대한 많은 기능을 구현하고자 하는 욕심에 쓸데없는 기능을 추가한 것 같다. GNSS 정보 계산을 통해 과속 여부를 실시간으로 판단하는 건 현실성과 효용성이 떨어진다고 생각함. 더불어 한 개의 폼에서 주차 시스템과 동시에 구현되었기 때문에, 입력 파일 좌표계에 따른 혼동이 오기 쉽다.  
> 



### 참조
사용 모듈 : ZED-F9P  
RINEX 데이터 : <https://gnssdata.or.kr/>  
RTKLIB : <https://www.rtklib.com/>  

