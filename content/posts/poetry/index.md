---
title: "[Python] Poetry를 통한 Python 개발 환경 구축"
date: 2024-07-05T18:31:44+09:00
draft: false
authors: ["JoonShik"]
description: "Poetry는 Python의 의존성 관리 및 패키징 도구입니다."
categories: ["python"]
tags: ["poetry", "package", "VirtualEnv"]
featuredImagePreview: "thumbnail.png"
---
<!--more-->
## Poetry란? 
Poetry는 Python의 의존성 관리 및 패키징 도구입니다.  

## 주요 기능
- `requirements.txt`, `setup.py`, `setup.cfg`, `MANIFEST.in` 등의 파일들을 대체하여 `pyproject.toml` 하나로 모든 설정을 관리합니다.
- `npm`의 `package-lock.json`과 비슷한 `poetry.lock` 파일을 제공하여 각 패키지의 해시값과 의존성 트리를 기록하여 의존성 충돌을 방지하고 일관된 환경을 유지할 수 있습니다.
- 손쉽게 개별 가상 환경을 구축하여 프로젝트 관리를 용이하게 합니다.
- 편리한 빌드 및 배포 기능을 제공합니다.

## 기존 패키지 관리 방법
Python의 기본 패키지 관리 도구는 `pip`를 사용하며, `requirements.txt` 파일을 통해 패키지 정보를 관리합니다.

```requirements.txt
Django==4.1.13
djangorestframework==3.15.1
```

```bash
pip install -r requirements.txt
```
{{< admonition type=info title="Info" open=false >}}
requirements.txt를 직접 작성하지 않고 pip로 설치한 후에 `pip freeze > requirements.txt`와 같이 생성할 수도 있지만 의존 패키지까지 모두 포함시켜 가독성이 떨어지고 관리가 어려워진다는 단점이 있습니다.
{{< /admonition >}}

## 기존 가상 환경 구축 방법
python에서는 기본적으로 venv 모듈을 통하여 가상 환경 기능을 제공합니다.

```bash
python -m venv venv
```

해당 명령어로 가상 환경 디렉토리가 생성되면 가상 환경을 활성화 할 수 있습니다.

```bash
source venv/bin/activate
```

deactivate를 명령어를 통해 가상 환경을 종료할 수 있습니다.

## Poetry의 패키지 관리 및 가상환경 구축 방법
poetry를 사용하기에 앞서 poetry를 설치하도록 하겠습니다.

```bash
pip install poetry
```
### Poetry 설정
poetry로 프로젝트를 생성하기 위해서는 `poetry new` 명령어를, 기존 프로젝트에 poetry를 추가하기 위해서는 `poetry init`을 사용하여 기본 설정을 할 수 있습니다.

```bash
poetry new myproject --src
```

poetry로 프로젝트를 생성부터 진행하도록 하겠습니다.  
여기서 `--src` 옵션은 `src` 하위에 패키지가 위치하도록 설정하는 옵션입니다.

그러면 아래와 같은 구조의 프로젝트가 생성됩니다.

```
├── README.md
├── pyproject.toml
├── src
│   └── myproject
│       └── __init__.py
└── tests
    └── __init__.py
```
### Poetry 패키지 관리
다음과 같이 원하는 패키지를 손쉽게 설치하고 제거할 수 있습니다.  

```bash
poetry add {PACKAGE}
poetry remove {PACKAGE}
```

패키지 설치 시에는 버전을 명시하는 형태로 원하는 버전을 설정할 수도 있습니다.  


```bash
poetry add django=4.2.3
```

| Requirement | Versions Allowed |
| ----------- | ---------------- |
| ^1.2        | >=1.2.0 < 2.0.0  |
| ~1.2        | >=1.2.0 < 1.3.0  |
| 1.2.*       | >=1.2.0 < 1.3.0  |

혹은 위와 같은 표현식으로 버전을 명시할 수도 있습니다.
```bash
poetry add django@~4.2
```

이후 `install` 명령을 통해 프로젝트의 `pyproject.toml` 파일을 읽고 의존성을 해결하고 새로운 가상환경에 필요한 패키지를 설치합니다.  

그러면 `poetry.lock` 파일이 작성되고 모든 패키지의 해시값과 의존성이 작성된 것을 확인할 수 있습니다.  

```bash
poetry install
```

### Poetry 가상환경 관리
`install` 명령을 통해 생성된 가상환경은 `env info` 명령을 통해 확인할 수 있습니다.  

```bash
poetry env info

>   Virtualenv
    Python:         3.12.4
    Implementation: CPython
    Path:           /Users/joonshik/Library/Caches/pypoetry/virtualenvs/myproject-6Uz1uM9E-py3.12
    Executable:     /Users/joonshik/Library/Caches/pypoetry/virtualenvs/myproject-6Uz1uM9E-py3.12/bin/python
    Valid:          True
```

이후 `poetry run` 명령어나 `poetry shell`을 통해 Poetry 가상 환경을 통해 명령을 실행시킬 수 있습니다.

```bash
which python
poetry run which python
```

`which python`은 기본 python으로 `poetry run which python`은 poetry 가상 환경으로 실행되어 출력되는 것을 확인할 수 있습니다.  

다음과 같이 python 명령어를 실행시킬 수 있습니다.  

```bash
poetry run pip list
poetry run python app.py
```

`poetry shell` 명령어를 통해서 현재 쉘에 가상 환경을 활성화할 수 있습니다.

```bash
poetry shell
which python
```

`which python`을 통해 poetry 가상 환경이 활성화된 것을 확인할 수 있습니다.  

```bash
exit
```
exit 명령을 통해 가상 환경을 종료할 수 있습니다.

만약 가상 환경을 사용하지 않고 기본 python 환경을 사용하고 싶다면 `poetry config` 설정을 할 수도 있습니다.
```bash
poetry config virtualenvs.create false --local
```

만약 이미 가상환경을 생성했다면 삭제한 후 다시 `poetry install`을 실행하면 가상 환경이 생성되지 않는 것을 확인할 수 있습니다.

```bash
poetry env list
```

위의 출력된 가상환경을 remove 명령어를 통해 삭제할 수 있습니다.

```bash
poetry env remove {ENV}
```

그러면 poetry install을 하더라도 가상환경이 생성되지 않는 것을 확인할 수 있습니다.

```bash
poetry install
poetry run which python

> Skipping virtualenv creation, as specified in config file.
```

## Poetry 추가 기능
### 그룹을 통한 의존성 관리
Poetry는 그룹을 통한 의존성 관리 기능을 제공합니다.  
즉, `개발`, `테스트`, `운영` 등의 환경을 구분하여 패키지를 관리할 수 있도록 해줍니다.

기존의 `requirements.txt`를 통해 환경을 분리할 때에는 개별 파일을 생성하는 방식으로 처리했습니다.

```
├── requirements
│   ├── common.txt
│   ├── dev.txt
│   └── prod.txt    
```
```dev.txt
-r common.txt
dev_req==1.0
```

하지만 Poetry는 복잡한 작업 없이 손쉽게 그룹을 구분할 수 있도록 합니다.

```bash
poetry add black --group dev
```
```bash
poetry install --without dev
poetry install --with dev
```

### 패키지 통합 관리
`black`이나 `pytest` 등 여러 라이브러리에서 Poetry를 통한 설정을 지원하여 하나의 파일에서 손쉽게 관리할 수 있습니다.

```pyproject.toml
[tool.pytest.ini_options]
minversion = "6.0"
addopts = "-ra -q"
testpaths = [
    "tests",
    "integration",
]
```
