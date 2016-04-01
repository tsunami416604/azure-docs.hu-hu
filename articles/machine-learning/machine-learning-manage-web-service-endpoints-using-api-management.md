<properties
    pageTitle="了解如何使用 API 管理來管理 AzureML Web 服務 | Microsoft Azure"
    description="示範如何使用 API 管理來管理 AzureML Web 服務的指南"
    keywords="machine learning,api management"
    services="machine-learning"
    documentationCenter=""
    authors="roalexan"
    manager="paulettm"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/08/2015"
    ms.author="roalexan" />


# 了解如何使用 API 管理來管理 AzureML Web 服務

##概觀

本指南示範如何快速開始使用 API 管理，來管理您的 AzureML Web 服務。

##什麼是 Azure API 管理？

Azure API 管理是一項 Azure 服務，可讓您藉由定義使用者存取、使用節流設定和儀表板監視，來管理 REST API 端點。 按一下 [ [這裡](http://azure.microsoft.com/services/api-management/) 如 Azure API 管理的詳細資訊。 按一下 [ [這裡](api-management/api-management-get-started.md) 如需如何開始使用 Azure API 管理的指引。 這是本指南所依據的指南，涵蓋更多主題，包括通知組態、定價層、回應處理、使用者驗證、建立產品、開發人員訂閱和使用量儀表板。

##什麼是 AzureML？

AzureML 是 Azure Machine Learning 服務，可讓您輕鬆建置、部署及共用進階分析解決方案。 按一下 [ [這裡](http://azure.microsoft.com/services/machine-learning/) 如 AzureML 的詳細資訊。

##必要條件

若要完成本指南，您需要：

* 一個 Azure 帳戶。 如果您沒有 Azure 帳戶，按一下 [ [這裡](http://azure.microsoft.com/pricing/free-trial/) 如需有關如何建立免費試用帳戶。
* AzureML 帳戶。 如果您沒有 AzureML 帳戶，按一下 [ [這裡](https://studio.azureml.net/) 如需有關如何建立免費試用帳戶。
* 部署為 Web 服務之 AzureML 實驗的工作區、服務和 API 金鑰。 按一下 [ [這裡](machine-learning/machine-learning-create-experiment.md) 如需有關如何建立 AzureML 實驗。 按一下 [ [這裡](machine-learning/machine-learning-publish-a-machine-learning-web-service.md) 的詳細說明如何部署 AzureML 實驗當做 web 服務。 此外，附錄 A 中的指示說明如何建立及測試簡單的 AzureML 實驗，並將其部署為 Web 服務。

##建立 API 管理執行個體

以下是使用 API 管理來管理您的 AzureML Web 服務的步驟。 首先建立服務執行個體。 登入 [傳統入口網站](https://manage.windowsazure.com/) 按一下 **新增** > **應用程式服務** > **API 管理** > **建立**。

![建立執行個體](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-instance.png)

指定唯一 **URL**。 本指南使用 **demoazureml** – 您必須選擇不同的東西。 選擇需要 **訂閱** 和 **區域** 服務執行個體。 進行您的選擇之後，請按下一步按鈕。

![建立服務 1](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-1.png)

指定的值 **組織名稱**。 本指南使用 **demoazureml** – 您必須選擇不同的東西。 輸入您的電子郵件地址中 **系統管理員電子郵件** 欄位。 此電子郵件地址將用於自 API 管理系統傳送通知。

![建立服務 2](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-2.png)

按一下核取方塊來建立您的服務執行個體。 *需要花費 30 分鐘的時間，建立新服務*。

##建立 API

建立服務執行個體之後，下一個步驟是建立 API。 API 包含可自用戶端應用程式叫用的一組作業。 API 作業會代理到現有的 Web 服務。 本指南會建立代理現有 AzureML RRS 和 BES Web 服務的 API。

API 是透過您經由 Azure 傳統入口網站存取的 API 發行者入口網站來建立和設定。 若要連線到發行者入口網站，請選取您的服務執行個體。

![選取服務執行個體](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-service-instance.png)

按一下 [ **管理** API 管理服務的 Azure 傳統入口網站中。

![管理服務](./media/machine-learning-manage-web-service-endpoints-using-api-management/manage-service.png)

按一下 [ **Api** 從 **API 管理** 左邊的功能表，然後按一下 **加入 API**。

![API 管理功能表](./media/machine-learning-manage-web-service-endpoints-using-api-management/api-management-menu.png)

型別 **AzureML 示範 API** 為 **Web API 名稱**。 型別 **https://ussouthcentral.services.azureml.net** 為 **Web 服務 URL**。 型別 **azureml 示範** 為 **Web API URL 尾碼**。 檢查 **HTTPS** 為 **Web API URL** 配置。 選取 **入門** 為 **產品**。 完成時，按一下 [ **儲存** 建立 API。

![加入新的 API](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-new-api.png)

##加入作業

按一下 [ **加入作業** ，將作業加入這個 API。

![加入作業](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-operation.png)

 **新作業** 視窗隨即出現， **簽章** 預設會選取索引標籤。

##加入 RRS 作業

首先建立 AzureML RRS 服務的作業。 選取 **POST** 為 **HTTP 指令動詞**。 型別 **中 /services/ {服務} / 執行？ api 版本 = {apiversion} & 詳細資料 = {details}** 為 **URL 範本**。 型別 **RRS 執行** 為 **顯示名稱**。

![加入 RRS 作業簽章](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

按一下 [ **回應** > **新增** 左邊，然後選取 **200 確定**。 按一下 [ **儲存** 儲存這個作業。

![加入 RRS 作業回應](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

##加入 BES 作業

由於加入 BES 作業的螢幕擷取畫面與加入 RRS 作業的螢幕擷取畫面很類似，因此不再提供。

###提交 (但不啟動) 批次執行工作

按一下 [ **加入作業** 將 AzureML BES 作業加入至 API。 選取 **POST** 的 **HTTP 指令動詞**。 型別 **中 /services/ {服務} / 作業？ api 版本 = {apiversion}** 的 **URL 範本**。 型別 **BES 提交** 的 **顯示名稱**。 按一下 [ **回應** > **新增** 左邊，然後選取 **200 確定**。 按一下 [ **儲存** 儲存這個作業。

###啟動批次執行工作

按一下 [ **加入作業** 將 AzureML BES 作業加入至 API。 選取 **POST** 的 **HTTP 指令動詞**。 型別 **中 /services/ {服務} /workspaces/{workspace}/services/{service}/jobs/{jobid}/start 啟動？ api 版本 = {apiversion}** 的 **URL 範本**。 型別 **BES 啟動** 的 **顯示名稱**。 按一下 [ **回應** > **新增** 左邊，然後選取 **200 確定**。 按一下 [ **儲存** 儲存這個作業。

###取得批次執行工作的狀態或結果

按一下 [ **加入作業** 將 AzureML BES 作業加入至 API。 選取 **取得** 的 **HTTP 指令動詞**。 型別 **中 /services/ {服務} /workspaces/{workspace}/services/{service}/jobs/{jobid}/start？ api 版本 = {apiversion}** 的 **URL 範本**。 型別 **BES 狀態** 的 **顯示名稱**。 按一下 [ **回應** > **新增** 左邊，然後選取 **200 確定**。 按一下 [ **儲存** 儲存這個作業。

###刪除批次執行工作

按一下 [ **加入作業** 將 AzureML BES 作業加入至 API。 選取 **刪除** 的 **HTTP 指令動詞**。 型別 **中 /services/ {服務} /workspaces/{workspace}/services/{service}/jobs/{jobid}/start？ api 版本 = {apiversion}** 的 **URL 範本**。 型別 **BES 刪除** 的 **顯示名稱**。 按一下 [ **回應** > **新增** 左邊，然後選取 **200 確定**。 按一下 [ **儲存** 儲存這個作業。

##透過開發人員入口網站呼叫作業

您可以從開發人員入口網站直接呼叫作業，以便檢視和測試 API 的操作。 在這個指南步驟中，您會呼叫 **RRS 執行** 方法已加入至 **AzureML 示範 API**。 按一下 [ **開發人員入口網站** 從頂端功能表的傳統入口網站的權限。

![開發人員入口網站](./media/machine-learning-manage-web-service-endpoints-using-api-management/developer-portal.png)

按一下 [ **Api** 從頂端功能表中，然後按一下 [ **AzureML 示範 API** 來查看可用的作業。

![demoazureml API](./media/machine-learning-manage-web-service-endpoints-using-api-management/demoazureml-api.png)

選取 **RRS 執行** 作業。 按一下 [ **試試**。

![試試看](./media/machine-learning-manage-web-service-endpoints-using-api-management/try-it.png)

要求參數，輸入您 **工作區**,  ，**服務**, ，**2.0** 的 **apiversion**, ，和  **true** 的 **詳細資料**。 您可以找到您 **工作區** 和 **服務** AzureML web 服務儀表板中 (請參閱 **測試 web 服務** 〈 附錄 A 〉)。

要求標頭中，按一下 [ **加入標頭** 和型別 **Content-type** 和 **application/json**, ，然後按一下 [ **加入標頭** 和型別 **授權** 和 **持有人 <YOUR AZUREML SERVICE API-KEY>**。 您可以找到您 **api 金鑰** AzureML web 服務儀表板中 (請參閱 **測試 web 服務** 〈 附錄 A 〉)。

型別 **{"Inputs": {"input1": {"ColumnNames": ["Col2"]"Values": [["這是美好的一天"]]}}，"GlobalParameters": {}}** 要求主體。

![AzureML 示範 API](./media/machine-learning-manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

按一下 [ **傳送**。

![傳送](./media/machine-learning-manage-web-service-endpoints-using-api-management/send.png)

叫用作業之後，開發人員入口網站會顯示 **要求的 URL** 從後端服務， **回應狀態**, 、 **回應標頭**, ，以及任何 **回應內容**。

![回應狀態](./media/machine-learning-manage-web-service-endpoints-using-api-management/response-status.png)

##附錄 A - 建立及測試簡單的 AzureML Web 服務

###建立實驗

以下步驟可讓您建立簡單的 AzureML 實驗並將其部署為 Web 服務。 Web 服務接受任意文字的資料行做為輸入，並傳回一組以整數來表示的特徵。 例如：

文字 | 雜湊的文字
--- | ---
這是美好的一天 | 1 1 2 2 0 2 0 1

首先，使用您選擇的瀏覽器巡覽至 ︰ [https://studio.azureml.net/](https://studio.azureml.net/) 並輸入您的認證進行登入。 接下來，建立新的空白實驗。

![搜尋實驗範本](./media/machine-learning-manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

重新命名為 **SimpleFeatureHashingExperiment**。 展開 **儲存的資料集** 拖曳 **來自 Amazon 的書籍評論** 拖曳到您的實驗。

![簡單的特徵雜湊實驗](./media/machine-learning-manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

展開 **資料轉換** 和 **操作** 拖曳 **專案資料行** 拖曳到您的實驗。 連接 **Amazon 的書籍評論** 至 **投影資料行**。

![專案資料行](./media/machine-learning-manage-web-service-endpoints-using-api-management/project-columns.png)

按一下 **專案資料行** 然後按一下 [ **啟動資料行選取器** ，然後選取 **Col2**。 按一下核取記號以套用這些變更。

![選取資料行](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-columns.png)

展開 **文字分析** 拖曳 **特徵雜湊** 拖曳到實驗。 連接 **投影資料行** 至 **特徵雜湊**。

![連接專案資料行](./media/machine-learning-manage-web-service-endpoints-using-api-management/connect-project-columns.png)

型別 **3** 的 **雜湊位元大小**。 這會建立 8 (23) 個資料行。

![雜湊位元大小](./media/machine-learning-manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

此時，您可以按一下 **執行** 以測試實驗。

![執行](./media/machine-learning-manage-web-service-endpoints-using-api-management/run.png)

###建立 Web 服務

現在建立 Web 服務。 展開 **Web 服務** 拖曳 **輸入** 到您的實驗。 連接 **輸入** 到 **特徵雜湊**。 也將拖曳 **輸出** 拖曳到您的實驗。 連接 **輸出** 至 **特徵雜湊**。

![輸出至特徵雜湊](./media/machine-learning-manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

按一下 [ **發佈 web 服務**。

![發佈 Web 服務](./media/machine-learning-manage-web-service-endpoints-using-api-management/publish-web-service.png)

按一下 [ **是** 發佈實驗。

![[是] 表示發佈](./media/machine-learning-manage-web-service-endpoints-using-api-management/yes-to-publish.png)

###測試 Web 服務

AzureML Web 服務是由 RSS (要求/回應服務) 和 BES (批次執行服務) 端點所組成。 RSS 適用於同步執行。 BES 適用於非同步工作執行。 若要測試您的 web 服務使用下面的範例 Python 原始碼，您可能需要下載並安裝 Azure SDK for Python (請參閱 ︰ [如何安裝 Python](python-how-to-install.md))。

您也需要 **工作區**, ，**服務**, ，和 **api 金鑰** 到下面的範例原始碼的實驗。 依序按一下 [尋找] 工作區和服務 **要求/回應** 或 **批次執行** 的 web 服務儀表板中的實驗。

![尋找工作區和服務](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

您可以找到 **api 金鑰** 依序按一下 [web 服務儀表板中的實驗。

![尋找 API 金鑰](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-api-key.png)

####測試 RRS 端點

#####測試按鈕

測試 RRS 端點的簡單方式是按一下 **測試** web 服務儀表板。

![test](./media/machine-learning-manage-web-service-endpoints-using-api-management/test.png)

型別 **這是美好的一天** 的 **col2**。 按一下核取記號。

![輸入資料](./media/machine-learning-manage-web-service-endpoints-using-api-management/enter-data.png)

您會看到類似下列畫面：

![範例輸出](./media/machine-learning-manage-web-service-endpoints-using-api-management/sample-output.png)

#####範例程式碼

測試 RRS 的另一個方式，是利用用戶端程式碼。 如果您按一下 **要求/回應** 在儀表板和捲動至底部，您會看到程式碼範例的 C#、 Python 和。您也會看到 RRS 要求，包括要求 URI、 的語法標頭和本文。

本指南顯示一個運作正常的 Python 範例。 您必須加以修改 **工作區**, ，**服務**, ，和 **api 金鑰** 的實驗。

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
    data = {
    "Inputs": {
        "input1": {
            "ColumnNames": ["Col2"],
            "Values": [ [ "This is a good day" ] ]
        },
    },
    "GlobalParameters": { }
    }
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
    headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
    body = str.encode(json.dumps(data))
    try:
        req = urllib2.Request(url, body, headers)
        response = urllib2.urlopen(req)
        result = response.read()
        print "result:" + result
            except urllib2.HTTPError, error:
        print("The request failed with status code: " + str(error.code))
        print(error.info())
        print(json.loads(error.read()))

####測試 BES 端點
按一下 [ **批次執行** 儀表板]，然後捲動至底部。 您會看到 C#、Python 和 R 的範例程式碼。您也會看到用來提交工作、啟動工作、取得工作狀態或結果，以及刪除工作的 BES 要求語法。

本指南顯示一個運作正常的 Python 範例。 您需要加以修改 **工作區**, ，**服務**, ，和 **api 金鑰** 的實驗。 此外，您必須修改 **儲存體帳戶名稱**, ，**儲存體帳戶金鑰**, ，和 **儲存體容器名稱**。 最後，您必須修改的位置 **輸入的檔** 和位置 **輸出檔**。

    import urllib2
    import json
    import time
    from azure.storage import *
    workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
    service = "<REPLACE WITH YOUR SERVICE ID>"
    api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
    storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
    storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
    storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
    input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
    output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
    input_blob_name = "mydatablob.csv"
    output_blob_name = "myresultsblob.csv"
    def printHttpError(httpError):
    print("The request failed with status code: " + str(httpError.code))
    print(httpError.info())
    print(json.loads(httpError.read()))
    return
    def saveBlobToFile(blobUrl, resultsLabel):
    print("Reading the result from " + blobUrl)
    try:
        response = urllib2.urlopen(blobUrl)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    with open(output_file, "w+") as f:
        f.write(response.read())
    print(resultsLabel + " have been written to the file " + output_file)
    return
    def processResults(result):
    first = True
    results = result["Results"]
    for outputName in results:
        result_blob_location = results[outputName]
        sas_token = result_blob_location["SasBlobToken"]
        base_url = result_blob_location["BaseLocation"]
        relative_url = result_blob_location["RelativeLocation"]
        print("The results for " + outputName + " are available at the following Azure Storage location:")
        print("BaseLocation: " + base_url)
        print("RelativeLocation: " + relative_url)
        print("SasBlobToken: " + sas_token)
        if (first):
            first = False
            url3 = base_url + relative_url + sas_token
            saveBlobToFile(url3, "The results for " + outputName)
    return

    def invokeBatchExecutionService():
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
    blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
    print("Uploading the input to blob storage...")
    data_to_upload = open(input_file, "r").read()
    blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
    print "Uploaded the input to blob storage"
    input_blob_path = "/" + storage_container_name + "/" + input_blob_name
    connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
    payload =  {
        "Input": {
            "ConnectionString": connection_string,
            "RelativeLocation": input_blob_path
        },
        "Outputs": {
            "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
        },
        "GlobalParameters": {
        }
    }
        body = str.encode(json.dumps(payload))
    headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
    print("Submitting the job...")
    # submit the job
    req = urllib2.Request(url + "?api-version=2.0", body, headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = response.read()
    job_id = result[1:-1] # remove the enclosing double-quotes
    print("Job ID: " + job_id)
    # start the job
    print("Starting the job...")
    req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    url2 = url + "/" + job_id + "?api-version=2.0"

    while True:
        print("Checking the job status...")
        # If you are using Python 3+, replace urllib2 with urllib.request in the follwing code
        req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
        try:
            response = urllib2.urlopen(req)
        except urllib2.HTTPError, error:
            printHttpError(error)
            return
        result = json.loads(response.read())
        status = result["StatusCode"]
        print "status:" + status
        if (status == 0 or status == "NotStarted"):
            print("Job " + job_id + " not yet started...")
        elif (status == 1 or status == "Running"):
            print("Job " + job_id + " running...")
        elif (status == 2 or status == "Failed"):
            print("Job " + job_id + " failed!")
            print("Error details: " + result["Details"])
            break
        elif (status == 3 or status == "Cancelled"):
            print("Job " + job_id + " cancelled!")
            break
        elif (status == 4 or status == "Finished"):
            print("Job " + job_id + " finished!")
            processResults(result)
            break
        time.sleep(1) # wait one second
    return
    invokeBatchExecutionService()


