# [비전에이전트2] 6.5 파노라마 모델 구현
-------------
## 개발 목적

* 사진 한 장에 담을 수 없는 풍경이나 이미지를 이어붙여서 하나의 사진으로 만들어줄 수 있는 기술을 통해 멋진 경관이나 이미지 사진을 만들어보고 싶어서 이 모델을 선택했다.
-------------
## 구현 내용

1. 파노라마 클래스 선언과 ui 만들기
```
class Panorama(QMainWindow) :
    def __init__(self) :
        super().__init__()
        self.setWindowTitle('파노라마 영상')
        self.setGeometry(200,200,700,200)
        
        collectButton=QPushButton('영상 수집',self)
        self.showButton=QPushButton('영상 보기',self) 
        self.stitchButton=QPushButton('봉합',self) 
        self.saveButton=QPushButton('저장',self)
        quitButton=QPushButton('나가기',self)
        self.label=QLabel('환영합니다!',self)
        
        collectButton.setGeometry(10,25,100,30)
        self.showButton.setGeometry(110,25,100,30) 
        self.stitchButton.setGeometry(210,25,100,30) 
        self.saveButton.setGeometry(310,25,100,30)
        quitButton.setGeometry(450,25,100,30) 
        self.label.setGeometry(10,70,600,170)

        self.showButton.setEnabled(False) 
        self.stitchButton.setEnabled(False) 
        self.saveButton.setEnabled(False)
        
        collectButton.clicked.connect(self.collectFunction)
        self.showButton.clicked.connect(self.showFunction)       
        self.stitchButton.clicked.connect(self.stitchFunction) 
        self.saveButton.clicked.connect(self.saveFunction)   
        quitButton.clicked.connect(self.quitFunction)
```
"영상 수집" 버튼을 제외하고 비활성화를 시켜준다.

2. 영상 수집 버튼
```
def collectFunction(self):
        self.showButton.setEnabled(False) 
        self.stitchButton.setEnabled(False) 
        self.saveButton.setEnabled(False)
        self.label.setText('c를 여러 번 눌러 수집하고 끝나면 q를 눌러 비디오를 끕니다.')
        
        self.cap=cv.VideoCapture(0,cv.CAP_DSHOW)
        if not self.cap.isOpened(): sys.exit('카메라 연결 실패')
        
        self.imgs=[]   
        while True:
            ret,frame=self.cap.read()  
            if not ret: break
            
            cv.imshow('video display', frame)
            
            key=cv.waitKey(1)
            if key==ord('c'):
                self.imgs.append(frame)
            elif key==ord('q'):
                self.cap.release()
                cv.destroyWindow('video display')          
                break 
        
        if len(self.imgs)>=2:
            self.showButton.setEnabled(True) 
            self.stitchButton.setEnabled(True) 
            self.saveButton.setEnabled(True)
```
"c"를 눌러서 영상에서 수집하고 "q"를 눌러 저장을 한다.

3. 영상수집 후 봉합하기
```
def showFunction(self):
        self.label.setText('수집된 영상은 '+str(len(self.imgs))+'장 입니다.')
        stack=cv.resize(self.imgs[0],dsize=(0,0),fx=0.25,fy=0.25)
        for i in range(1,len(self.imgs)):
            stack=np.hstack((stack,cv.resize(self.imgs[i],dsize=(0,0),fx=0.25,fy=0.25))) 
        cv.imshow('Image collection',stack)  

    def stitchFunction(self):
        stitcher=cv.Stitcher_create()
        status,self.img_stitched=stitcher.stitch(self.imgs)
        if status==cv.STITCHER_OK:
            cv.imshow('Image stitched panorama',self.img_stitched)
        else:
            winsound.Beep(3000,500)      
            self.label.setText('파노라마 제작에 실패했습니다. 다시 시도하세요.')
```
수집한 것을 봉합을 눌러 하나로 이어준다.