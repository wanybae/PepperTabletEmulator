=================================
Pepper Tablet Emulator 셋업순서(Mac버전)
=================================

## NAOqi Python SDK를 인스톨
### 다운로드

- NAOqi Python SDK (Python 2.7 SDK 2.4.2 Mac 64) 를 다음 링크에서 다운로드한다.

    https://community.ald.softbankrobotics.com/en/resources/software/pepper-sdks-and-documentation-243

    (리소스에 접근하기 위해서는 Aldebara 디벨로퍼 프로그램에 등록해 두어야 한다. 우선은 [Create New Customer Account](https://store.aldebaran.com/default/customer/account/create/) 에서 유저등록을 진행하고, 다음으로는 [Developers | SoftBank Robotics Community](https://community.ald.softbankrobotics.com/en/developerprogram#section3) 에서 디벨로퍼 프로그램을 등록한다.）

- 다운로드한 파일 압축풀기
- 특정 폴더에 압축을 푼다. 예를들어 사용자 디렉토리의 naoqi 디렉토리 밑에 저장
    
    ```
예)
    mkdir -p ~/naoqi
    cp -r pynaoqi-python2.7-2.4.2.26-mac64  ~/naoqi
　```

## nginx 인스톨, 셋업
- 인스톨

    ```
    brew install nginx
    ```

    (※ brew를 설치하지 않았다면 먼저 [Homebrew — macOS 용 패키지 관리자](http://brew.sh/index_ko.html) 를 참고하여 homebrew를 인스톨한다.)

- 에뮬레이터용 셋업

    ```
    mkdir -p ~/.local/share/PackageManager/apps
    ln -s ~/.local/share/PackageManager/apps /usr/local/var/www/
    ```

## libqi-js 셋업
- https://github.com/aldebaran/libqi-js/ 에서  [Download ZIP] 버튼을 눌러 libqi-js아카이브를 얻는다.

- 압축을 풀고, 압축을 푼 폴더에서 다음을 실행한다.

   ```
    cp -r libs /usr/local/var/www/
   ```
- qimessaging-json을 /usr/local/bin에 저장

   ```
cp qimessaging-json /usr/local/bin
```

## tornado 인스톨
    sudo pip install tornado
- pip를 아직 설치하지 않은 상태라면 pip를 인스톨한다.

    ```
    sudo easy_install pip
```

## tornadio2 인스톨

- https://github.com/MrJoes/tornadio2 오른쪽 밑에  [Downlad ZIP]버튼을 눌러서 tornadio2 아카이브를 얻는다.

- 압축을 풀고, 압축을 푼 폴더에서 다음을 실행한다.

    ```
sudo ./setup.py install
```

## Pepper Tablet Emulator 셋업

- 작업용 디렉토리를 작성한뒤 이동하여 다음 명령들을 실행

    ```
git clone https://github.com/tkawata1025/PepperTabletEmulator.git
cd PepperTabletEmulator
cp install_files/PepperTabletEmulator.py /usr/local/bin/
cp install_files/nginx/nginx.conf /usr/local/etc/nginx/
```
- Choregraphe2.4_te.sh　을 텍스트에디터에서 열고 3번째 줄에  NAOqi python SDK를 저장한 장소로 변경해 준다.

    ```
NAOQISDK="$HOME/naoqi/pynaoqi-python2.7-2.4.2.26-mac64"
<--- NAOqi Python SDK 를 저장한 장소로 변경해 준다
```

## Mac OS X El Capitan의 경우 - SDK에 패치

- Mac OS X El Capitan을 사용하는 경우, 다음 문서를 참고하여 Python SDK에 패치를 해줘야 한다

    [Mac OS X El Capitan에서 NAOqi 2.4.2 Python SDK 사용하기 - 일본어](http://qiita.com/tkawata1025/items/31dd49bcef04c85b3047)

- **내용**
    
    Mac OS X El Capitan에서는 System Integrity Protection(SIP)라고 불리우는 새로운 시큐리티기능이 원인으로, **NAOqi Python SDK**를 사용할 수가 없다. 다음과 같은 에러가 나오면 SIP 때문에 발생한 것이라고 볼 수 있다.

    ```
>>> import naoqi
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/Users/tkawata/naoqi/pynaoqi-python2.7-2.4.2.26-mac64/naoqi.py", line 9, in <module>
    import qi
  File "/Users/tkawata/naoqi/pynaoqi-python2.7-2.4.2.26-mac64/qi/__init__.py", line 66, in <module>
    from _qi import Application as _Application
ImportError: dlopen(/Users/tkawata/naoqi/pynaoqi-python2.7-2.4.2.26-mac64/_qi.so, 2): Library not loaded: libboost_python.dylib
  Referenced from: /Users/tkawata/naoqi/pynaoqi-python2.7-2.4.2.26-mac64/_qi.so
  Reason: unsafe use of relative rpath libboost_python.dylib in /Users/tkawata/naoqi/pynaoqi-python2.7-2.4.2.26-mac64/_qi.so with restricted binary
>>> 
```

    SDK가 참조하고 있는 라이브러리 참조방법에 문제가 있는거 같은데, 사실 SDK가 수정되기만을 기다릴 수 밖에 없다.（각 NAOqi 2.4.2 SDK는 Mac Yosemite가 서포트하고 있는 OS 버전이다.）

    그러니 꼼수를 사용하여야 하는데, 패치를 적용하는 방식이다. 이 패치가 저 사이트에 적혀있다.

    ```
patch_naoqi_python_sdk_for_elcaptain.sh
#!/bin/bash
if [ $# -ne 1 ]; then
  echo "Parameter error" 1>&2
  echo "sh patch_naoqi_python_sdk_for_elcaptain.sh  naoqi_python_sdk의 path" 
  exit 1
fi
NAOQIDIR=$1 # "${HOME}/naoqi/pynaoqi-python2.7-2.4.2.26-mac64"
if [ ! -e ${NAOQIDIR}/naoqi.py ]; then
    echo "지정한 path는 naoqi python SDK 의 path가 아닌거 같네요."
    exit 1
fi
cd ${NAOQIDIR}
for file in `ls *.dylib *.so`
do
    # patch all library internal cross references
    echo "Patching " $file "..."
    for fileother in `ls  *.dylib *.so ;ls qi *.dylib *.so`
    do
        # library
        echo "  Patching " $fileother " with " $file "..."
        install_name_tool  -change $file $NAOQIDIR/$file $fileother
    done
    # patch Python reference for the library
    install_name_tool -change /Library/Frameworks/Python.framework/Versions/2.7/Python /System/Library/Frameworks/Python.framework/Versions/2.7/Python $file
done
for file in `ls *.dylib *.so`
do
    # patch all library internal cross references
    echo "Patching " $file "..."
    fileother="qi/plugins/libqimodule_python_plugin.dylib"
    # library
    echo "  Patching " $fileother " with " $file "..."
    install_name_tool -change $file $NAOQIDIR/$file $fileother
    # patch Python reference for the library
    install_name_tool -change /Library/Frameworks/Python.framework/Versions/2.7/Python /System/Library/Frameworks/Python.framework/Versions/2.7/Python $file
done
```

    파라메터로 NAOqi SDK의 path를 지정한다.

    ```
sh patch_naoqi_python_sdk_for_elcaptain.sh  PythonNAOqiSDK의 path`
```

