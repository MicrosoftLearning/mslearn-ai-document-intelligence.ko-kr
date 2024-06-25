---
lab:
  title: 양식에서 데이터 추출
  module: Module 6 - Document Intelligence
---

# 양식에서 데이터 추출

현재 기업이 직원에게 주문 시트를 수동으로 구매하고 데이터베이스에 데이터를 입력할 것을 요구한다고 가정합니다. AI 서비스를 활용하여 데이터 항목 프로세스를 개선하려고 합니다. 데이터베이스를 자동으로 업데이트하는 데 사용할 수 있는 구조적 데이터를 생성하고 양식을 읽는 기계 학습 모델을 빌드하기로 합니다.

**Azure AI 문서 인텔리전스**는 사용자가 자동화된 데이터 처리 소프트웨어를 빌드할 수 있게 해주는 Azure AI 서비스입니다. 이 소프트웨어는 OCR(광학 인식) 기술을 사용해 양식 문서에서 텍스트, 키/값 쌍 및 테이블을 추출할 수 있습니다. Azure AI 문서 인텔리전스에는 청구서, 영수증 및 명함을 인식하기 위한 사전 빌드된 모델이 있습니다. 이 서비스에서는 사용자 지정 모델을 학습시키는 기능도 제공합니다. 이 연습에서는 사용자 지정 모델 빌드 과정을 중점적으로 진행합니다.

## Visual Studio Code에서 앱 개발 준비

이제 서비스 SDK를 사용하여 Visual Studio Code를 사용하여 앱을 개발해 보겠습니다. 앱의 코드 파일은 GitHub 리포지토리에 제공되었습니다.

> **팁**: **mslearn-ai-document-intelligence** 리포지토리를 이미 복제한 경우 Visual Studio Code에서 엽니다. 그렇지 않은 경우에는 다음 단계에 따라 개발 환경에 복제합니다.

1. Visual Studio Code 시작
1. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/mslearn-ai-document-intelligence` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
1. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.

    > **참고**: Visual Studio Code에서 열려는 코드를 신뢰하라는 팝업 메시지가 표시되면 팝업에서 **예, 작성자를 신뢰합니다.** 옵션을 클릭합니다.

1. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버깅에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에**를 선택합니다. *폴더에서 Azure 함수 프로젝트를 발견했습니다*라는 메시지가 표시되면 해당 메시지를 안전하게 닫을 수 있습니다.

## Azure AI 문서 인텔리전스 리소스 만들기

Azure AI 문서 인텔리전스 서비스를 사용하려면 Azure 구독에 Azure AI 문서 인텔리전스 또는 Azure AI 서비스 리소스가 필요합니다. 여기서는 Azure Portal을 사용하여 리소스를 만듭니다.

1. 브라우저 탭에서 `https://portal.azure.com`에서 Azure Portal을 열고 Azure 구독과 연결된 Microsoft 계정으로 로그인합니다.
1. Azure Portal 홈페이지에서 상단 검색 상자로 이동하여 **문서 인텔리전스**를 입력한 다음 **Enter** 키를 누릅니다.
1. **문서 인텔리전스** 페이지에서 **만들기**를 선택합니다.
1. **문서 인텔리전스 만들기** 페이지에서 다음을 사용하여 리소스를 구성합니다.
    - **구독**: Azure 구독.
    - **리소스 그룹**: *DocIntelligenceResources*와 같은 고유한 이름을 가진 리소스 그룹을 선택하거나 만듭니다.
    - **지역**: 사용자에게 가까운 지역을 선택합니다.
    - **이름**: 글로벌로 고유한 이름을 입력합니다.
    - **가격 책정 계층**: **무료 F0**을 선택합니다. 사용 가능한 무료 계층이 없으면 **표준 S0**을 선택합니다.
1. 그런 다음 **검토 + 만들기**와 **만들기**를 선택합니다. Azure가 Azure AI 문서 인텔리전스 리소스를 만드는 동안 기다립니다.
1. 배포가 완료되면 **리소스로 이동**을 선택하여 리소스의 **개요** 페이지를 확인합니다. 

## 학습용 문서 수집

모델 테스트를 학습하려면 다음과 같은 샘플 양식을 사용합니다. 

![이번 프로젝트에 사용된 청구서 이미지입니다.](../media/Form_1.jpg)

1. **Visual Studio Code**로 돌아갑니다. *탐색기* 창에서 **Labfiles/02-custom-document-intelligence** 폴더를 열고 **sample-forms** 폴더를 펼칩니다. 이 폴더에는 **.json** 및 **.jpg**로 끝나는 파일이 있습니다.

    **.jpg** 파일을 사용하여 해당 모델을 학습합니다.  

    **.json** 파일은 자동으로 생성되었고 레이블 정보가 포함됩니다. 파일은 양식과 함께 해당 Blob Storage 컨테이너에 업로드됩니다.

    Visual Studio Code에서 이미지를 선택하면 *sample-forms* 폴더에서 사용 중인 이미지를 볼 수 있습니다.

1. 아직 리소스의 **개요** 페이지가 없다면 **Azure Portal**로 돌아가서 리소스의 해당 페이지로 이동합니다. *Essentials* 섹션에서 문서 인텔리전스 리소스를 만든 **리소스 그룹**을 확인합니다.

1. 리소스 그룹의 **개요** 페이지에서 **구독 ID** 및 **위치**를 확인합니다. 후속 단계에서 이러한 값과 **리소스 그룹** 이름이 필요합니다.

    ![리소스 그룹 페이지 예제](../media/resource_group_variables.png)

1. Visual Studio Code의 *탐색기* 창에서 **Labfiles/02-custom-document-intelligence** 폴더를 찾은 다음 언어 선택에 따라 **CSharp** 또는 **Python** 폴더를 확장합니다. 각 폴더에는 Azure OpenAI 기능을 통합할 앱에 대한 언어별 파일이 포함되어 있습니다.

1. 코드 파일이 포함된 **CSharp** 또는 **Python** 폴더를 마우스 오른쪽 단추로 클릭하고 **통합 터미널 열기**를 선택합니다. 

1. 터미널에서 다음 명령을 실행하여 Azure에 로그인합니다. **az login** 명령은 Microsoft Edge 브라우저를 열고 Azure AI 문서 인텔리전스 리소스를 만드는 데 사용한 것과 동일한 계정으로 로그인합니다. 로그인한 후 브라우저 창을 닫으세요.

    ```powershell
    az login
    ```

1. Visual Studio Code로 돌아갑니다. 터미널 창에서 다음 명령을 실행하여 Azure 위치를 나열합니다.

    ```powershell
    az account list-locations -o table
    ```

1. 출력에서 리소스 그룹 위치에 해당하는 **Name** 값을 찾습니다(예를 들어 미국 동부에 해당하는 이름은 *eastus*임).**

    > **중요**: **Name** 값을 적어 두었다가 11단계에서 사용하세요.

1. Visual Studio Code의 **Labfiles/02-custom-document-intelligence** 폴더에서 **setup.cmd**를 선택합니다. 이 스크립트를 사용하여 필요한 기타 Azure 리소스를 만드는 데 필요한 Azure CLI(명령줄 인터페이스)를 실행합니다.

1. **setup.cmd** 스크립트에서 명령을 검토합니다. 프로그램은 다음 작업을 수행합니다.
    - Azure 리소스 그룹에서 스토리지 계정 만들기
    - 로컬 *sampleforms* 폴더에서 스토리지 계정의 *sampleforms* 컨테이너로 파일 업로드
    - 공유 액세스 서명 URI 인쇄

1. 문서 인텔리전스 리소스를 배포한 구독, 리소스 그룹 및 위치 이름에 대한 적절한 값을 사용하여 **subscription_id**, **resource_group** 및 **location** 변수 선언을 수정합니다.
그런 다음 변경 내용을 **저장**합니다.

    **expiry_date** 변수의 경우 이 연습에서는 그대로 유지합니다. 이 변수는 SAS(공유 액세스 서명) URI를 생성할 때 사용됩니다. 실제로는 SAS에 적절한 만료 날짜를 설정해야 합니다. [여기](https://docs.microsoft.com/azure/storage/common/storage-sas-overview#how-a-shared-access-signature-works)서 SAS에 대해 자세히 알아볼 수 있습니다.  

1. **Labfiles/02-custom-document-intelligence** 폴더의 터미널에서 다음 명령을 입력하여 스크립트를 실행합니다.

    ```PowerShell
    $currentdir=(Get-Item .).FullName
    cd ..
    ./setup.cmd
    cd $currentdir

    ```

1. 스크립트가 완료되면 표시된 출력을 검토합니다.

1. Azure Portal에서 리소스 그룹을 새로 고쳐 방금 만든 Azure Storage 계정이 포함되어 있는지 확인합니다. 스토리지 계정을 열고 왼쪽 창에서 **스토리지 브라우저**를 선택합니다. 그런 다음 스토리지 브라우저에서 **Blob 컨테이너**를 확장하고 **sampleforms** 컨테이너를 선택하여 로컬 **02-custom-document-intelligence/sample-forms** 폴더에서 파일이 업로드되었는지 확인합니다.

## 문서 인텔리전스 스튜디오를 사용하여 모델 학습

이제 스토리지 계정에 업로드된 파일을 사용하여 모델을 학습할 예정입니다.

1. 브라우저에서 `https://documentintelligence.ai.azure.com/studio`의 문서 인텔리전스 스튜디오로 이동합니다.
1. **사용자 지정 모델** 섹션까지 아래로 스크롤하고 **사용자 지정 추출 모델** 타일을 선택합니다.
1. 계정에 로그인하라는 메시지가 표시되면 Azure 자격 증명을 사용합니다.
1. 사용할 Azure AI 문서 인텔리전스 리소스를 묻는 메시지가 나타나면 Azure AI 문서 인텔리전스 리소스를 만들 때 사용한 구독 및 리소스 이름을 선택합니다.
1. **내 프로젝트** 아래에서 **프로젝트 만들기**를 선택합니다. 다음 구성을 사용합니다.

    - **프로젝트 이름**: 고유한 이름을 입력합니다.
        - *계속*을 선택합니다.
    - **서비스 리소스 구성**: 이 랩에서 이전에 만든 구독, 리소스 그룹 및 문서 인텔리전스 리소스를 선택합니다. *기본값으로 설정* 확인란을 선택합니다. 기본 API 버전을 유지합니다. 
        - *계속*을 선택합니다.
    - **학습 데이터 원본 연결**: 설정 스크립트에서 만들어진 구독, 리소스 그룹 및 스토리지 계정을 선택합니다. *기본값으로 설정* 확인란을 선택합니다. `sampleforms` Blob 컨테이너를 선택하고 폴더 경로를 비워 둡니다.
        - *계속*을 선택합니다.
    - *프로젝트 만들기*를 선택합니다.

1. 프로젝트가 만들어지면 **학습**을 선택하여 모델을 학습시킵니다. 다음 구성을 사용합니다.
    - **모델 ID**: *전역적으로 고유한 이름을 제공합니다(다음 단계에서 모델 ID 이름이 필요함)*. 
    - **빌드 모드**: 템플릿.
1. 학습에는 다소 시간이 걸릴 수 있습니다. 완료되면 알림이 표시됩니다.

## 사용자 지정 문서 인텔리전스 모델 테스트

1. Visual Studio Code로 돌아갑니다. 터미널에서 언어 선택에 맞는 적절한 명령을 실행하여 문서 인텔리전스 패키지를 설치합니다.

    **C#:**

    ```powershell
    dotnet add package Azure.AI.FormRecognizer --version 4.1.0
    ```

    **Python**:

    ```powershell
    pip install azure-ai-formrecognizer==3.3.0
    ```

1. Visual Studio Code의 **Labfiles/02-custom-document-intelligence** 폴더에서 사용 중인 언어를 선택합니다. 다음 값을 사용하여 구성 파일(언어 선택에 따라 **appsettings.json** 또는 **.env**)을 편집합니다.
    - 문서 인텔리전스 엔드포인트.
    - 문서 인텔리전스 키입니다.
    - 모델을 학습할 때 제공한 생성된 모델 ID입니다. 문서 인텔리전스 스튜디오의 **모델** 페이지에서 이를 찾을 수 있습니다. 변경 내용을 **저장**합니다.

1. Visual Studio Code에서 클라이언트 애플리케이션용 코드 파일(C#의 경우 *Program.cs*, Python의 경우 *test-model.py*)을 열고 포함된 코드, 특히 URL의 이미지는 웹의 이 GitHub 리포지토리에 있는 파일을 나타냅니다.

1. 터미널로 돌아가서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**

    ```powershell
    dotnet build
    dotnet run
    ```

    **Python**

    ```powershell
    python test-model.py
    ```

1. 출력을 보고 모델의 출력이 어떻게 `Merchant` 및 `CompanyPhoneNumber`와 같은 필드 이름을 제공하는지 관찰합니다.

## 정리

Azure 리소스 사용이 완료되면 추가 요금이 발생하지 않도록 [Azure Portal](https://portal.azure.com/?azure-portal=true)에서 리소스를 삭제해야 합니다.

## 자세한 정보

문서 인텔리전스 서비스에 대한 자세한 내용은 [문서 인텔리전스 설명서](https://learn.microsoft.com/azure/ai-services/document-intelligence/?azure-portal=true)를 참조하세요.
