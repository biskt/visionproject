# [비전에이전트2] 6.4 교통약자 보호구역 알림 모델 구현

1. 개발환경 구축

```
mkdir vision_agent4
git clone "https://github.com/biskt/visionproject.git"
conda activate 이름
```
### 환경을 만든 다음 vscode로 폴더를 열어서 준비를 마친다.

2. 이 모델을 만드는 목적은 교통약자(어린이, 노인, 장애인 등)의 안전을 더욱 신경써야하는 구간을 나타내는 표지판들을 컴퓨터 비전 기술로 인식한 후 운전자에게 알려주는 기술을 활용해 교통약자의 사고율을 줄이는 것이다. 미디어에서 보이는 교통약자 표지판을 인식해주는 기술을 활용한다.

3. 구현 내용
```
class TrafficWeak(QMainWindow):
    def __init__(self):
        super().__init__()
        # 윈도우 제목,위치,크기 설정
        self.setWindowTitle('교통약자 보호')
        self.setGeometry(200,200,700,200)
       
        # 버튼 4개, 레이블 1개 생성 
        signButton=QPushButton('표지판 등록',self)
        roadButton=QPushButton('도로 영상 불러옴',self)
        recognitionButton=QPushButton('인식',self)
        quitButton=QPushButton('나가기',self)
        self.label=QLabel('환영합니다!',self)
        
        # 버튼 클릭 시 수행할 콜백 함수 지정
        signButton.setGeometry(10,10,100,30)
        roadButton.setGeometry(110,10,100,30)
        recognitionButton.setGeometry(210,10,100,30)
        quitButton.setGeometry(510,10,100,30)
        self.label.setGeometry(10,40,600,170)
        
        signButton.clicked.connect(self.signFunction)
        roadButton.clicked.connect(self.roadFunction) 
        recognitionButton.clicked.connect(self.recognitionFunction)        
        quitButton.clicked.connect(self.quitFunction)

        self.signFiles=[['child.png','어린이'],['elder.png','노인'],['disabled.png','장애인']]	# 표지판 모델 영상 이름 설정
        self.signImgs=[]
```
먼저 ui를 생성하고 준비된 표지판 이미지 파일들을 지정하고 영상에 저장할 객체를 생성한다.

