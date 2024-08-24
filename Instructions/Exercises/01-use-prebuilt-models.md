---
lab:
  title: 미리 빌드된 문서 인텔리전스 모델 사용
  module: Module 6 - Document Intelligence
---

# 미리 빌드된 문서 인텔리전스 모델 사용

이 연습에서는 Azure 구독에서 Azure AI 문서 인텔리전스 리소스를 설정합니다. Azure AI 문서 인텔리전스 및 C# 또는 Python을 모두 사용하여 분석을 위해 해당 리소스에 양식을 제출합니다.

## Azure AI 문서 인텔리전스 리소스를 만듭니다.

Azure AI 문서 인텔리전스 서비스를 호출하려면 먼저 Azure에서 해당 서비스를 호스트할 리소스를 만들어야 합니다.

1. 브라우저 탭의 [https://portal.azure.com](https://portal.azure.com?azure-portal=true)에서 Azure Portal을 열고 Azure 구독과 연결된 Microsoft 계정으로 로그인합니다.
1. Azure Portal 홈페이지에서 상단 검색 상자로 이동하여 **문서 인텔리전스**를 입력한 다음 **Enter** 키를 누릅니다.
1. **문서 인텔리전스** 페이지에서 **만들기**를 선택합니다.
1. **문서 인텔리전스 만들기** 페이지에서 다음을 사용하여 리소스를 구성합니다.
    - **구독**: Azure 구독.
    - **리소스 그룹**: *DocIntelligenceResources*와 같은 고유한 이름을 가진 리소스 그룹을 선택하거나 만듭니다.
    - **지역**: 사용자에게 가까운 지역을 선택합니다.
    - **이름**: 글로벌로 고유한 이름을 입력합니다.
    - **가격 책정 계층**: **무료 F0**을 선택합니다. 사용 가능한 무료 계층이 없으면 **표준 S0**을 선택합니다.
1. 그런 다음 **검토 + 만들기**와 **만들기**를 선택합니다. Azure에서 Azure AI 문서 인텔리전스 리소스를 만드는 동안 기다립니다.
1. 배포가 완료되면 **리소스로 이동**을 선택합니다. 이 연습의 나머지 부분에 대해 이 페이지를 열어 둡니다.

## 읽기 모델 사용

먼저 **Azure AI Document Intelligence Studio** 및 읽기 모델을 사용하여 여러 언어로 문서를 분석해 보겠습니다. 분석을 수행하기 위해 방금 만든 리소스에 Azure AI Document Intelligence Studio를 연결합니다.

1. 새 브라우저 탭을 열고 [https://documentintelligence.ai.azure.com/studio](https://documentintelligence.ai.azure.com/studio)에서 **Azure AI Document Intelligence Studio**로 이동합니다.
1. **문서 분석**에서 **읽기** 타일을 선택합니다.
1. 계정에 로그인하라는 메시지가 표시되면 Azure 자격 증명을 사용합니다.
1. 사용할 Azure AI 문서 인텔리전스 리소스를 묻는 메시지가 나타나면 Azure AI 문서 인텔리전스 리소스를 만들 때 사용한 구독 및 리소스 이름을 선택합니다.
1. 왼쪽의 문서 목록에서 **read-german.pdf**를 선택합니다.

    ![Azure AI Document Intelligence Studio의 읽기 페이지를 보여 주는 스크린샷입니다.](../media/read-german-sample.png#lightbox)

1. 왼쪽 상단에서 **분석 옵션**을 선택한 다음 **분석 옵션** 창에서 **언어** 확인란(**선택적 검색** 아래)을 선택하고 **저장**을 클릭합니다. 
1. 왼쪽 위에서 **분석 실행**을 선택합니다.
1. 분석이 완료되면 이미지에서 추출된 텍스트가 **콘텐츠** 탭의 오른쪽에 표시됩니다. 이 텍스트를 검토하고 정확도를 위해 원본 이미지의 텍스트와 비교합니다.
1. **결과** 탭을 선택합니다. 이 탭에는 추출된 JSON 코드가 표시됩니다. 
1. **결과** 탭에서 JSON 코드의 아래쪽으로 스크롤합니다. 읽기 모델이 `locale`로 표시된 각 범위의 언어를 감지했음을 알 수 있습니다. 대부분의 범위는 독일어(언어 코드 `de`)로 되어 있지만 범위에서 다른 언어 코드를 찾을 수 있습니다(예: 영어 - 언어 코드 `en` - 마지막 범위 중 하나).

    ![Azure AI Document Intelligence Studio의 읽기 모델 결과에서 두 범위에 대한 언어 검색을 보여 주는 스크린샷입니다.](../media/language-detection.png#lightbox)

## Visual Studio Code에서 앱 개발 준비

이제 Azure 문서 인텔리전스 서비스 SDK를 사용하는 앱을 살펴보겠습니다. 여기서는 Visual Studio Code를 사용하여 앱을 개발합니다. 앱의 코드 파일은 GitHub 리포지토리에 제공되었습니다.

> **팁**: **mslearn-ai-document-intelligence** 리포지토리를 이미 복제한 경우 Visual Studio Code에서 엽니다. 그렇지 않은 경우에는 다음 단계에 따라 개발 환경에 복제합니다.

1. Visual Studio Code 시작
1. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/mslearn-ai-document-intelligence` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
1. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.

    > **참고**: Visual Studio Code에서 열려는 코드를 신뢰하라는 팝업 메시지가 표시되면 팝업에서 **예, 작성자를 신뢰합니다.** 옵션을 클릭합니다.

1. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버깅에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에**를 선택합니다. *폴더에서 Azure 함수 프로젝트를 발견했습니다*라는 메시지가 표시되면 해당 메시지를 안전하게 닫을 수 있습니다.

## 애플리케이션 사용

C# 및 Python용 애플리케이션과 문서 인텔리전스를 테스트하는 데 사용할 샘플 pdf 파일이 제공되었습니다. 두 앱 모두 동일한 기능을 제공합니다. 먼저, Azure 문서 인텔리전스 리소스를 사용할 수 있도록 애플리케이션의 일부 주요 부분을 완료합니다.

1. 다음 청구서를 검사하고 해당 필드와 값 중 일부를 적어 둡니다. 이 청구서를 코드로 분석하게 됩니다.

    ![샘플 청구서 문서를 보여 주는 스크린샷.](../media/sample-invoice.png#lightbox)

1. Visual Studio Code의 **탐색기** 창에서 **Labfiles/01-prebuild-models** 폴더를 찾은 다음 언어 선택에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다. 각 폴더에는 Azure 문서 인텔리전스 기능을 통합할 앱에 대한 언어별 파일이 포함되어 있습니다.

1. 코드 파일이 포함된 **CSharp** 또는 **Python** 폴더를 마우스 오른쪽 단추로 클릭하고 **통합 터미널 열기**를 선택합니다. 그런 다음, 언어 기본 설정에 적합한 명령을 실행하여 Azure Form Recognizer(문서 인텔리전스의 이전 이름) SDK 패키지를 설치합니다.

    **C#:**

    ```powershell
    dotnet add package Azure.AI.FormRecognizer --version 4.1.0
    ```

    **Python**:

    ```powershell
    pip install azure-ai-formrecognizer==3.3.0
    ```

## Azure 문서 인텔리전스 서비스를 사용할 코드 추가

이제 SDK를 사용하여 pdf 파일을 평가할 준비가 되었습니다.

1. Azure Portal에서 Azure AI 문서 인텔리전스 개요를 표시하는 브라우저 탭으로 전환합니다. 왼쪽 창의 *리소스 관리*에서 **키 및 엔드포인트**를 선택합니다. **엔드포인트** 값의 오른쪽에 있는 **클립보드로 복사** 단추를 클릭합니다.
1. **탐색기** 창의 **CSharp** 또는 **Python** 폴더에서 기본 설정 언어에 대한 코드 파일을 열고 방금 복사한 문자열로 `<Endpoint URL>`을 바꿉니다.

    **C#**: ***Program.cs***

    ```csharp
    string endpoint = "<Endpoint URL>";
    ```

    **Python**: ***document-analysis.py***

    ```python
    endpoint = "<Endpoint URL>"
    ```

1. Azure Portal에서 Azure AI 문서 인텔리전스 **키 및 엔드포인트**를 표시하는 브라우저 탭으로 전환합니다. **KEY 1** 값 오른쪽에 있는 클립보드에 복사* 단추를 클릭합니다.
1. Visual Studio Code의 코드 파일에서 이 줄을 찾아 방금 복사한 문자열로 `<API Key>`를 바꿉니다.

    **C#**

    ```csharp
    string apiKey = "<API Key>";
    ```

    **Python**

    ```python
    key = "<API Key>"
    ```

1. 주석 `Create the client`를 찾습니다. 그런 다음, 새 줄에서 다음 코드를 입력합니다.

    **C#**

    ```csharp
    var cred = new AzureKeyCredential(apiKey);
    var client = new DocumentAnalysisClient(new Uri(endpoint), cred);
    ```

    **Python**

    ```python
    document_analysis_client = DocumentAnalysisClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )
    ```

1. 주석 `Analyze the invoice`를 찾습니다. 그런 다음, 새 줄에서 다음 코드를 입력합니다.

    **C#**

    ```csharp
    AnalyzeDocumentOperation operation = await client.AnalyzeDocumentFromUriAsync(WaitUntil.Completed, "prebuilt-invoice", fileUri);
    ```

    **Python**

    ```python
    poller = document_analysis_client.begin_analyze_document_from_url(
        fileModelId, fileUri, locale=fileLocale
    )
    ```

1. 주석 `Display invoice information to the user`를 찾습니다. 그런 다음, 새 줄에서 다음 코드를 입력합니다.

    **C#**

    ```csharp
    AnalyzeResult result = operation.Value;
    
    foreach (AnalyzedDocument invoice in result.Documents)
    {
        if (invoice.Fields.TryGetValue("VendorName", out DocumentField? vendorNameField))
        {
            if (vendorNameField.FieldType == DocumentFieldType.String)
            {
                string vendorName = vendorNameField.Value.AsString();
                Console.WriteLine($"Vendor Name: '{vendorName}', with confidence {vendorNameField.Confidence}.");
            }
        }
    ```

    **Python**

    ```python
    receipts = poller.result()
    
    for idx, receipt in enumerate(receipts.documents):
    
        vendor_name = receipt.fields.get("VendorName")
        if vendor_name:
            print(f"\nVendor Name: {vendor_name.value}, with confidence {vendor_name.confidence}.")
    ```

    > [!NOTE]
    > 공급업체 이름을 표시하는 코드를 추가했습니다. 시작 프로젝트에는 *고객 이름* 및 *청구서 합계*를 표시하는 코드도 포함되어 있습니다.

1. 변경 내용을 코드 파일에 저장합니다.

1. 대화형 터미널 창에서 폴더 컨텍스트가 기본 설정 언어의 폴더인지 확인합니다. 그런 후 다음 명령을 입력하여 애플리케이션을 실행합니다.

1. ***C#에만 해당되는 내용으로서,*** 프로젝트를 빌드하려면 다음 명령을 입력합니다.

    **C#:**

    ```powershell
    dotnet build
    ```

1. 코드를 실행하려면 다음 명령을 입력합니다.

    **C#:**

    ```powershell
    dotnet run
    ```

    **Python**:

    ```powershell
    python document-analysis.py
    ```

프로그램에는 신뢰 수준이 있는 공급업체 이름, 고객 이름, 청구서 합계가 표시됩니다. 보고서 값을 이 섹션의 시작 부분에서 연 샘플 청구서와 비교합니다.

## 정리

Azure 리소스 사용이 완료되면 추가 요금이 발생하지 않도록 [Azure Portal](https://portal.azure.com/?azure-portal=true)에서 리소스를 삭제해야 합니다.

## 자세한 정보

문서 인텔리전스 서비스에 대한 자세한 내용은 [문서 인텔리전스 설명서](https://learn.microsoft.com/azure/ai-services/document-intelligence/?azure-portal=true)를 참조하세요.
