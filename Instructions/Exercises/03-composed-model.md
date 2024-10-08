---
lab:
  title: 구성된 문서 인텔리전스 모델 만들기
  module: Module 6 - Document Intelligence
---

# 구성된 문서 인텔리전스 모델 만들기

이 연습에서는 서로 다른 세금 양식을 분석하는 두 개의 사용자 지정 모델을 만들고 학습합니다. 그런 다음 이러한 사용자 지정 모델을 모두 포함하는 구성된 모델을 만듭니다. 양식을 제출하여 모델을 테스트하고 문서 유형 및 레이블이 지정된 필드를 올바르게 인식하는지 확인합니다.

## 리소스 설정

스크립트를 사용하여 Azure AI 문서 인텔리전스 리소스, 샘플 양식이 포함된 스토리지 계정 및 리소스 그룹을 만듭니다.

1. Visual Studio Code 시작
1. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/mslearn-ai-document-intelligence` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
1. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.

    > **참고**: Visual Studio Code에서 열려는 코드를 신뢰하라는 팝업 메시지가 표시되면 팝업에서 **예, 작성자를 신뢰합니다.** 옵션을 클릭합니다.
    
    > **참고**: 빌드 및 디버깅에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에**를 선택합니다. Visual Studio Code에서 다른 팝업이 나타나면 안전하게 해제할 수 있습니다.

1. 왼쪽 창에서 **Labfiles** 폴더를 확장하고 **03-composed-model** 디렉터리를 마우스 오른쪽 단추로 클릭합니다. 통합 터미널에서 열 옵션을 선택하고 다음 스크립트를 실행합니다.

    ```powershell
    az login --output none
    ```

    > **참고**: 활성 구독이 없다는 오류가 발생하고 MFA가 사용하도록 설정된 경우 먼저 `https://portal.azure.com`에서 Azure Portal에 로그인한 다음 `az login`을 다시 실행해야 할 수 있습니다.

1. 메시지가 표시되면 Azure 구독에 로그인합니다. 그런 다음 Visual Studio Code로 돌아가서 로그인 프로세스가 완료될 때까지 기다립니다.
1. 통합 터미널에서 다음 명령을 실행하여 리소스를 설정합니다.

   ```powershell
   ./setup.ps1
   ```

   > **중요**: 스크립트로 만들어진 마지막 리소스는 Azure AI 문서 인텔리전스 서비스입니다. F0 계층 리소스가 이미 있어서 해당 명령이 실패하는 경우 이 랩에 해당 리소스를 사용하거나 Azure Portal에서 S0 계층을 사용하여 수동으로 리소스를 만듭니다.

## 1040 Forms 사용자 지정 모델 만들기

구성된 모델을 만들려면 먼저 두 개 이상의 사용자 지정 모델을 만들어야 합니다. 첫 번째 사용자 지정 모델을 만들려면 다음을 수행합니다.

1. 새 브라우저 탭의 `https://documentintelligence.ai.azure.com/studio`에서 **Azure AI 문서 인텔리전스 스튜디오**를 시작합니다.
1. 아래로 스크롤한 후 **사용자 지정 모델**에서 **사용자 지정 추출 모델**을 선택합니다.
1. 계정에 로그인하라는 메시지가 표시되면 Azure 자격 증명을 사용합니다.
1. 사용할 Azure AI 문서 인텔리전스 리소스를 묻는 메시지가 나타나면 Azure AI 문서 인텔리전스 리소스를 만들 때 사용한 구독 및 리소스 이름을 선택합니다.
1. **내 프로젝트** 아래에서 **프로젝트 만들기**를 선택합니다.
1. **프로젝트 이름** 텍스트 상자에서 **1040 Forms**를 입력한 다음, **계속**을 선택합니다.
1. **서비스 리소스 구성** 페이지의 **구독** 드롭다운 목록에서 Azure 구독을 선택합니다.
1. **리소스 그룹** 드롭다운 목록에서 자동으로 만들어진 **DocumentIntelligenceResources&lt;xxxx&gt;** 를 선택합니다.
1. **문서 인텔리전스 또는 인지 서비스 리소스** 드롭다운 목록에서 **DocumentIntelligence&lt;xxxx&gt;** 를 선택합니다.
1. **API 버전** 드롭다운 목록에서 **2024-07-31(미리보기)** 가 선택되어 있는지 확인한 다음 **계속**을 선택합니다.
1. **학습 데이터 원본 연결** 페이지의 **구독** 드롭다운 목록에서 Azure 구독을 선택합니다.
1. **리소스 그룹** 드롭다운 목록에서 **DocumentIntelligenceResources&lt;xxxx&gt;** 를 선택합니다.
1. **스토리지 계정** 드롭다운 목록에서 나열된 스토리지 계정만 선택합니다. 구독에 여러 스토리지 계정이 있는 경우 *docintelstorage*로 시작하는 계정을 선택합니다.
1. **BLOB 컨테이너** 드롭다운 목록에서 **1040examples**를 선택한 다음 **계속**을 선택합니다.
1. **검토 및 만들기** 페이지에서 **프로젝트 만들기**를 선택합니다.
1. *지금 레이블 지정 시작* 팝업의 *레이아웃 실행*에서 **지금 실행**을 선택하고 분석이 완료될 때까지 기다립니다.

## 1040 Forms 사용자 지정 모델 레이블 지정

이제 예제 양식의 필드에 레이블을 지정해 보겠습니다.

1. **레이블 데이터** 페이지의 오른쪽 상단에서 **+ 필드 추가**를 선택한 다음 **필드**를 선택합니다.
1. **FirstName**을 입력한 다음 *Enter* 키를 누릅니다.
1. 왼쪽 목록에서 **f1040_1.pdf**라는 문서를 선택하고 **John**을 선택한 다음 **FirstName**을 선택합니다.
1. 페이지 오른쪽 상단에서 **+ 필드 추가**를 선택한 다음 **필드**를 선택합니다.
1. **LastName**을 입력한 다음 *Enter* 키를 누릅니다.
1. 문서에서 **Doe**를 선택한 다음 **LastName**을 선택합니다.
1. 페이지 오른쪽 상단에서 **+ 필드 추가**를 선택한 다음 **필드**를 선택합니다.
1. **City**를 입력한 다음 *Enter* 키를 누릅니다.
1. 문서에서 **로스앤젤레스**를 선택한 다음, **City**를 선택합니다.
1. 페이지 오른쪽 상단에서 **+ 필드 추가**를 선택한 다음 **필드**를 선택합니다.
1. **State**를 입력한 다음 *Enter* 키를 누릅니다.
1. 문서에서 **CA**를 선택한 다음 **State**를 선택합니다.
1. 만든 레이블을 사용하여 왼쪽 목록의 나머지 양식에 대해 레이블 지정 프로세스를 반복합니다. 동일한 네 개의 필드인 *FirstName*, *LastName*, *City* 및 *State*에 레이블을 지정합니다. 문서 중 하나에 도시 또는 주 데이터가 없는 것을 확인할 수 있습니다.

> **중요** 이 연습에서는 5개의 예제 양식만 사용하고 4개의 필드에만 레이블을 지정합니다. 실세계 모델에서는 가능한 한 많은 샘플을 사용하여 예측의 정확도와 신뢰도를 최대화해야 합니다. 또한 4개의 필드가 아닌 양식에서 사용 가능한 모든 필드에 레이블을 지정해야 합니다.

## 1040 Forms 사용자 지정 모델 학습

이제 샘플 양식에 레이블이 지정되었으므로 첫 번째 사용자 지정 모델을 학습시킬 수 있습니다.

1. Azure AI 문서 인텔리전스 스튜디오의 화면 오른쪽 상단에서 **학습**을 선택합니다.
1. **새 모델 학습** 대화 상자의 **모델 ID** 텍스트 상자에 **1040FormsModel**을 입력합니다.
1. **빌드 모드** 드롭다운 목록에서 **템플릿**을 선택한 다음 **학습**을 선택합니다. 
1. **학습 진행 중** 대화 상자에서 **모델로 이동**을 선택합니다.

## 1099 Forms 사용자 지정 모델 만들기

이제 1099 세금 양식 예제에서 학습할 두 번째 모델을 만들어야 합니다.

1. Azure AI 문서 인텔리전스 스튜디오에서 **사용자 지정 추출 모델**을 선택합니다.
1. **내 프로젝트** 아래에서 **프로젝트 만들기**를 선택합니다.
1. **프로젝트 이름** 텍스트 상자에서 **1099 Forms**를 입력한 다음 **계속**을 선택합니다.
1. **서비스 리소스 구성** 페이지의 **구독** 드롭다운 목록에서 Azure 구독을 선택합니다.
1. **리소스 그룹** 드롭다운 목록에서 **DocumentIntelligenceResources&lt;xxxx&gt;** 를 선택합니다.
1. **문서 인텔리전스 또는 인지 서비스 리소스** 드롭다운 목록에서 **DocumentIntelligence&lt;xxxx&gt;** 를 선택합니다.
1. **API 버전** 드롭다운 목록에서 **2024-07-31(미리보기)** 가 선택되어 있는지 확인한 다음 **계속**을 선택합니다.
1. **학습 데이터 원본 연결** 페이지의 **구독** 드롭다운 목록에서 Azure 구독을 선택합니다.
1. **리소스 그룹** 드롭다운 목록에서 **DocumentIntelligenceResources&lt;xxxx&gt;** 를 선택합니다.
1. **스토리지 계정** 드롭다운 목록에서 나열된 스토리지 계정만 선택합니다.
1. **BLOB 컨테이너** 드롭다운 목록에서 **1099examples**를 선택한 다음 **계속**을 선택합니다.
1. **검토 및 만들기** 페이지에서 **프로젝트 만들기**를 선택합니다.
1. **레이아웃 실행**의 드롭다운 버튼을 선택하고 **분석되지 않은 문서**를 선택합니다.
1. 분석이 완료될 떄까지 기다립니다.

## 1099 Forms 사용자 지정 모델 레이블 지정

이제 몇 가지 필드가 있는 예제 양식에 레이블을 지정합니다.

1. **레이블 데이터** 페이지의 오른쪽 상단에서 **+ 필드 추가**를 선택한 다음 **필드**를 선택합니다.
1. **FirstName**을 입력한 다음 *Enter* 키를 누릅니다.
1. **f1099msc_payer.pdf**라는 문서를 선택하고 **John**을 선택한 다음 **FirstName**을 선택합니다.
1. 페이지 오른쪽 상단에서 **+ 필드 추가**를 선택한 다음 **필드**를 선택합니다.
1. **LastName**을 입력한 다음 *Enter* 키를 누릅니다.
1. 문서에서 **Doe**를 선택한 다음 **LastName**을 선택합니다.
1. 페이지 오른쪽 상단에서 **+ 필드 추가**를 선택한 다음 **필드**를 선택합니다.
1. **City**를 입력한 다음 *Enter* 키를 누릅니다.
1. 문서에서 **New Haven**을 선택한 다음 **City**를 선택합니다.
1. 페이지 오른쪽 상단에서 **+ 필드 추가**를 선택한 다음 **필드**를 선택합니다.
1. **State**를 입력한 다음 *Enter* 키를 누릅니다.
1. 문서에서 **CT**를 선택한 다음 **State**를 선택합니다.
1. 왼쪽 목록의 나머지 양식에 대해 레이블 지정 프로세스를 반복합니다. 동일한 네 개의 필드인 *FirstName*, *LastName*, *City* 및 *State*에 레이블을 지정합니다. 두 문서에 레이블을 지정할 이름 데이터가 없는 것을 확인할 수 있습니다.

## 1099 Forms 사용자 지정 모델 학습

이제 두 번째 사용자 지정 모델을 학습할 수 있습니다.

1. Azure AI 문서 인텔리전스 스튜디오의 오른쪽 상단에서 **학습**을 선택합니다.
1. **새 모델 학습** 대화 상자의 **모델 ID** 텍스트 상자에서 **1099FormsModel**을 입력합니다.
1. **빌드 모드** 드롭다운 목록에서 **템플릿**을 선택한 다음 **학습**을 선택합니다.
1. **학습 진행 중** 대화 상자에서 **모델로 이동**을 선택합니다.
1. 학습 프로세스는 몇 분 정도 걸릴 수 있습니다. 두 모델 모두 **성공** 상태가 표시될 때까지 가끔씩 브라우저를 새로 고칩니다.

## 모델 사용

이제 모델이 완료되었으므로 예제 형식으로 테스트하겠습니다.

1. Azure AI 문서 인텔리전스 스튜디오에서 **모델** 페이지를 선택하고 **1040FormsModel**을 선택합니다.
1. **테스트**를 선택합니다.
1. **파일 찾아보기**를 선택한 다음 리포지토리를 복제한 위치를 찾습니다.
1. **03-composed-model/trainingdata/TestDoc/f1040_7.pdf**를 선택한 다음 **열기**를 선택합니다.
1. **분석 실행**을 선택합니다. Azure AI 문서 인텔리전스는 구성된 모델을 사용하여 양식을 분석합니다.
1. 분석한 문서는 1040 세금 양식의 예입니다. **DocType** 속성을 확인하여 올바른 사용자 지정 모델이 사용되었는지 확인합니다. 또한 모델에서 식별된 **FirstName**, **LastName**, **City** 및 **State** 값을 확인합니다.

## 리소스 정리

이제 구성된 모델이 작동하는 방식을 살펴보았으므로 Azure 구독에서 만든 리소스를 제거하겠습니다.

1. `https://portal.azure.com/`의 **Azure Portal**에서 **리소스 그룹**을 선택합니다.
1. **리소스 그룹** 목록에서 만든 **DocumentIntelligenceResources&lt;xxxx&gt;** 를 선택한 다음 **리소스 그룹 삭제**를 선택합니다.
1. **리소스 그룹 이름 입력** 텍스트 상자에 리소스 그룹 이름을 입력한 다음 **삭제**를 선택하여 문서 인텔리전스 리소스와 스토리지 계정을 삭제합니다.

## 자세한 정보

- [사용자 지정 모델 작성](/azure/ai-services/document-intelligence/concept-composed-models)
