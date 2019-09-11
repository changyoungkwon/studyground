# python package dependency 정리하기
파이썬 프로젝트들이 세 개정도 만들었다. 서로에 대한 의존성이 있다보니까 임시로 의존성있는 프로젝트의 복사본을 여기저기서 사용, 수정하였고, 정해놓은 규칙없이 수정하다보니 어떤 파일이 가장 최신 수정본인지 알 방법이 없었다(아마 버전 관리를 좀 더 말끔하게 했다면 나았겠지만, 그러지 못했다). 
본 문서는 이 문제를 해결하는 과정이다. 

## 1. terminology
### 1.1. pypi
Python Package Index, a repository of a software for the Python programming language. Python package들을 저장하는 repository. 특정해서 Python software foundation에서 제공하는 pypi를 나타내기도 한다. 

### 1.2. archive file
압축 파일. archive file format은 압축 파일 포맷. 

### 1.3. distribution package
파이썬 공식 문서에서는 package와 distribution package를 구분하여서 사용한다. Package는 module, 혹은 sub-package들을 구조화시키는 컨테이너. Distribution package는 python package, modules들을 배포(distribute)하기 위해 versioned archive file이다. Distribution package는 단순한 python package가 포함하지 않는 배포를 위한 정보(dependency 등)이 포함된다. 

## 2. workflow for upload package
자세한 내용은 [링크](https://packaging.python.org/tutorials/packaging-projects/)

순서는 1. source code를 archive화 한 뒤, 2. pypi에 업로드한다. 

### 2.1. archive



### 2.2. pypi에 업로드
`twine`을 이용한다. `twine`이나, `setup.py upload`나 내부를 보면 `http post`의 wrapper에 불과함을 할 수 있다. 다만, `twine`이 전송동안 security를 보증한다. (`setup.py upload`는 pypi가 https를 지원하더라고 하더라도, http를 사용하는 걸까? 이해가 되지 않는다). 
`pypi`에서 지원하는, 업로드 가능 포맷은 tarball, egg, wheel 등 archive 형태로 지원한다. `wheel`은 패키지 배포를 위한 포맷으로, 까놓고보면 정해진 파일 구조를 가지는 archive에 불과하다. 




## 3. workflow for install package
pip를 이용한 설치는 `man pip`에 총 4가지로 소개된다. 
- pypi
- vcs url
- local project directories
- local or remote source archives

그리고 여러 flag를 제공하는 데 `editable` flag가 가장 유용하다([링크](https://setuptools.readthedocs.io/en/latest/setuptools.html#development-mode)). editable flag가 없이는 `site-packages`에 설치하고자 하는 패키지의 코드가 복사되는 데에 반해, flag가 있으면 해당 패키지의 링크만이 복사된다! 짱이다!

정확한 동작은 링크에서 확인할 수 있는데, 의아했던 점은 설치하고자 하는 패키지의 링크를 두 번 복사한다. `.egg-link`, 그리고 `easy-install.pth` 내에 저장된다. 정확히는 deployment directory가 `site-packages`면 easy-install.pth를 업데이트 한다는데, 거기 말고 배포할 directory가 있나?

## 4. 결론
결론적으로 python 프로젝트 A가 B에 대해서 의존성을 가진다면, (1)B의 `setup.py`, 혹은 `pyproject.toml` 파일을 정의한다. (2) `pipenv install -e B`로 설치한다. `B` 대신에 `git+https://(git-url)`을 대입하여도 상관없다. 하여튼, B의 복사본을 만든다거나, B를 매번 설치했다 지웠다 할 필요는 없어졌다. 
