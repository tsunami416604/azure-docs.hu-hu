<properties
    pageTitle="如何使用服務管理 API (Python) - 功能指南"
    description="了解如何透過程式設計從 Python 執行一般服務管理工作。"
    services="cloud-services"
    documentationCenter="python"
    authors="huguesv"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="11/03/2015"
    ms.author="huvalo"/>

# 如何從 Python 使用服務管理

本指南將說明如何透過程式設計從 Python 執行一般服務管理工作。  **ServiceManagementService** 類別 [Azure SDK for Python](../python-how-to-install.md) 支援多種服務管理相關功能中所提供以程式設計方式存取 [Azure 傳統入口網站][management-portal] (例如 **建立、 更新及刪除雲端服務、 部署、 資料管理服務和虛擬機器**)。 這項功能在建置需要以程式設計方式存取服務管理的應用程式時，將有所幫助。

> [AZURE.NOTE] 服務管理 API 會以新資源管理 API，目前可用的預覽版本中被取代。  請參閱 [Azure 資源管理文件](http://azure-sdk-for-python.readthedocs.org/) 如需有關使用新的資源管理 API，從 Python。


## <a name="WhatIs"> </a>什麼是服務管理？
服務管理 API 提供以程式設計方式存取服務管理功能可透過 [Azure 傳統入口網站][management-portal]。 Azure SDK for Python 可讓您管理雲端服務和儲存體帳戶。

若要使用服務管理 API，您必須 [建立 Azure 帳戶](http://azure.microsoft.com/pricing/free-trial/)。

## <a name="Concepts"> </a>概念
Azure SDK for Python 含有 [Azure 服務管理 API][svc-mgmt-rest-api], ，這是 REST API。 所有 API 作業都會透過 SSL 而執行，並可使用 X.509 v3 憑證相互驗證。 管理服務可從執行於 Azure 的服務內存取，或直接透過網際網路，從任何可傳送 HTTPS 要求和接收 HTTPS 回應的應用程式存取。

## <a name="Connect"> </a>作法：連線到服務管理
若要連接到服務管理端點，您必須具備 Azure 訂閱 ID 和有效的管理憑證。 您可以取得訂閱識別碼，直到 [Azure 傳統入口網站][management-portal]。

> [AZURE.NOTE] 從 Azure SDK for Python v0.8.0 開始，您就可以使用在 Windows 上執行時使用 OpenSSL 建立的憑證。  這需要使用 Python 2.7.4 或更新版本。 建議使用者使用 OpenSSL 而非 .pfx，因為未來可能會移除 .pfx 憑證的支援。

### Windows/Mac/Linux 上的管理憑證 (OpenSSL)
您可以使用 [OpenSSL](http://www.openssl.org/) 建立管理憑證。  實際上您需要建立兩個憑證，一個用於伺服器 (`.cer` 檔案)，一個用於用戶端 (`.pem` 檔案)。 若要建立 `.pem` 檔案，請執行下列命令：

    `openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

若要建立 `.cer` 憑證，請執行下列命令：

    `openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

如需 Azure 憑證的詳細資訊，請參閱 [的 Azure 雲端服務憑證概觀](./cloud-services-certs-create.md)。 如需 OpenSSL 參數的完整說明，請參閱文件，網址 [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html)。

建立這些檔案之後，您必須上傳 `.cer` 檔案到 Azure，透過 [設定] 索引標籤的 [上傳] 動作 [Azure 傳統入口網站][management-portal], ，且必須記下您儲存 `.pem` 檔案。

取得您的訂閱識別碼之後，建立憑證，並上傳 `.cer` 檔案至 Azure，您可以將連接到 Azure 管理端點的訂閱 id 和路徑 `.pem` 檔案重新命名為 **ServiceManagementService**:

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

在上面的範例， `sms` 是 **ServiceManagementService** 物件。  **ServiceManagementService** 類別是用來管理 Azure 服務的主要類別。

### Windows 上的管理憑證 (MakeCert)

您可以使用 `makecert.exe`，在您的機器上建立自我簽署管理憑證。  開啟 **Visual Studio 命令提示字元** 為 **管理員** ，並使用下列命令，將 *AzureCertificate* 取代為您想要使用的憑證名稱。

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

此命令會建立 `.cer` 檔案，並將其安裝在 **個人** 憑證存放區。 如需詳細資訊，請參閱 [的 Azure 雲端服務憑證概觀](./cloud-services-certs-create.md)。

您已建立憑證之後，您必須上傳 `.cer` 檔案到 Azure，透過 [設定] 索引標籤的 [上傳] 動作 [Azure 傳統入口網站][management-portal]。

取得您的訂閱識別碼之後，建立憑證，並上傳 `.cer` 檔案至 Azure，您可以連接到 Azure 管理端點的訂閱 id 和憑證的位置傳遞您 **個人** 要憑證存放區 **ServiceManagementService** (同樣地，取代 *AzureCertificate* 與您的憑證名稱):

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

在上面的範例， `sms` 是 **ServiceManagementService** 物件。  **ServiceManagementService** 類別是用來管理 Azure 服務的主要類別。

## <a name="ListAvailableLocations"> </a>作法：列出可用位置

若要列出可用於裝載服務的位置，請使用 **list\_locations** 方法 ︰

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

在建立雲端服務或儲存服務時，您必須提供有效位置。  **List\_locations** 方法一律會傳回目前可用位置的最新清單。 截至本文撰寫時間為止，可用位置如下：

- 西歐
- 北歐
- 東南亞
- 東亞
- 美國中部
- 美國中北部
- 美國中南部
- 美國西部
- 美國東部
- 日本東部
- 日本西部
- 巴西南部
- 澳洲東部
- 澳洲東南部

## <a name="CreateCloudService"> </a>作法：建立雲端服務

當您建立應用程式，並在 Azure 中執行它時，程式碼和組態併稱為 Azure [cloud service] (稱為 *託管服務* 在舊版 Azure 中)。  **Create\_hosted\_service** 方法可讓您藉由提供代管的服務名稱 （必須是在 Azure 中是唯一的）、 標籤 （自動編碼為 base64）、 描述和位置建立新的託管的服務。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

您可以列出與您訂用帳戶的所有代管的服務 **list\_hosted\_services** 方法 ︰

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

如果您想要取得特定託管服務的相關資訊，您可以這樣來裝載的服務名稱傳遞 **get\_hosted\_service\_properties** 方法 ︰

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

建立雲端服務之後，您可以將您的程式碼部署到服務與 **create\_deployment** 方法。

## <a name="DeleteCloudService"> </a>作法：刪除雲端服務

您可以將服務名稱，以刪除雲端服務 **delete\_hosted\_service** 方法 ︰

    sms.delete_hosted_service('myhostedservice')

請注意，必須先刪除服務的所有部署，再刪除服務 (請參閱 [如何 ︰ 刪除部署](#DeleteDeployment) 如需詳細資訊。)

## <a name="DeleteDeployment"> </a>作法：刪除部署

若要刪除部署，使用 **delete\_deployment** 方法。 下列範例示範如何刪除名為 `v1` 的部署。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>作法：建立儲存體服務

A [儲存體服務](../storage/storage-create-storage-account.md) 可讓您存取 Azure [Blob](../storage/storage-python-how-to-use-blob-storage.md), ，[資料表](../storage/storage-python-how-to-use-table-storage.md), ，和 [佇列](../storage/storage-python-how-to-use-queue-storage.md)。 若要建立儲存服務，您必須要有服務的名稱 (3 到 24 個小寫字元，且在 Azure 中是唯一的)、描述、標籤 (最多 100 個字元，會自動編碼為 base64)，以及位置。 下列範例說明如何藉由指定位置來建立儲存服務。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mystorageaccount'
    label = 'mystorageaccount'
    location = 'West US'
    desc = 'My storage account description.'

    result = sms.create_storage_account(name, desc, label, location=location)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

請注意，在上述範例中的狀態 **create\_storage\_account** 可以擷取作業所傳回的結果傳遞給 **create\_storage\_account** 至 **get\_operation\_status** 方法。  

您可以列出儲存體帳戶和其屬性與 **list\_storage\_accounts** 方法 ︰

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"> </a>作法：刪除儲存體服務

您可以藉由傳遞儲存體服務名稱，才能刪除儲存體服務 **delete\_storage\_account** 方法。 如果刪除儲存體服務，則會刪除服務中所儲存的所有資料 (Blob、資料表和佇列)。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>作法：列出可用作業系統

若要列出可用於裝載服務的作業系統，請使用 **list\_operating\_systems** 方法 ︰

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

或者，您可以使用 **list\_operating\_system\_families** 方法，以依系列的作業系統 ︰

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"> </a>作法：建立作業系統映像

若要將作業系統映像新增至映像儲存機制，使用 **add\_os\_image** 方法 ︰

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mycentos'
    label = 'mycentos'
    os = 'Linux' # Linux or Windows
    media_link = 'url_to_storage_blob_for_source_image_vhd'

    result = sms.add_os_image(label, media_link, name, os)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

若要列出可用的作業系統映像，請使用 **list\_os\_images** 方法。 其中包括所有的平台映像和使用者映像：

    result = sms.list_os_images()

    for image in result:
        print('Name: ' + image.name)
        print('Label: ' + image.label)
        print('OS: ' + image.os)
        print('Category: ' + image.category)
        print('Description: ' + image.description)
        print('Location: ' + image.location)
        print('Media link: ' + image.media_link)
        print('')

## <a name="DeleteVMImage"> </a>作法：刪除作業系統映像

若要刪除使用者映像，請使用 **delete\_os\_image** 方法 ︰

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>作法：建立虛擬機器

若要建立虛擬機器，您首先必須建立 [雲端服務](#CreateCloudService)。  然後建立虛擬機器部署使用 **create\_virtual\_machine\_deployment** 方法 ︰

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    # Name of an os image as returned by list_os_images
    image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

    # Destination storage account container/blob where the VM disk
    # will be created
    media_link = 'url_to_target_storage_blob_for_vm_hd'

    # Linux VM configuration, you can use WindowsConfigurationSet
    # for a Windows VM instead
    linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

    os_hd = OSVirtualHardDisk(image_name, media_link)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=os_hd,
        role_size='Small')

## <a name="DeleteVM"> </a>作法：刪除虛擬機器

若要刪除虛擬機器，您先刪除部署使用 **delete\_deployment** 方法 ︰

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

雲端服務可以接著可以使用刪除 **delete\_hosted\_service** 方法 ︰

    sms.delete_hosted_service(service_name='myvm')

##作法：從擷取的虛擬機器映像建立虛擬機器

若要擷取 VM 映像，請先呼叫 **capture\_vm\_image** 方法 ︰

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    # replace the below three parameters with actual values
    hosted_service_name = 'hs1'
    deployment_name = 'dep1'
    vm_name = 'vm1'

    image_name = vm_name + 'image'
    image = CaptureRoleAsVMImage    ('Specialized',
        image_name,
        image_name + 'label',
        image_name + 'description',
        'english',
        'mygroup')

    result = sms.capture_vm_image(
            hosted_service_name,
            deployment_name,
            vm_name,
            image
        )

接下來，若要確定您已成功擷取映像，請使用 **list\_vm\_images** api，並確定您的映像會顯示在結果中 ︰

    images = sms.list_vm_images()

最後，建立虛擬機器擷取映像，請使用 **create\_virtual\_machine\_deployment** 方法，但是這次為傳入 vm_image_name

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=None,
        role_size='Small',
        vm_image_name = image_name)

若要深入了解如何擷取 Linux 虛擬機器，請參閱 [如何擷取 Linux 虛擬機器。](../virtual-machines/virtual-machines-linux-capture-image.md)

若要深入了解如何擷取 Windows 虛擬機器，請參閱 [如何擷取 Windows 虛擬機器。](../virtual-machines/virtual-machines-capture-image-windows-server.md)

## <a name="What's Next"> </a>後續步驟

現在，您了解服務管理的基本概念，您可以存取 [Azure Python SDK 的完整 API 參考文件](http://azure-sdk-for-python.readthedocs.org/) 並執行複雜的工作，輕鬆地以管理 python 應用程式。

如需詳細資訊，請參閱 [Python 開發人員中心](/develop/python/)。

[What is Service Management]: #WhatIs
[Concepts]: #Concepts
[How to: Connect to service management]: #Connect
[How to: List available locations]: #ListAvailableLocations
[How to: Create a cloud service]: #CreateCloudService
[How to: Delete a cloud service]: #DeleteCloudService
[How to: Create a deployment]: #CreateDeployment
[How to: Update a deployment]: #UpdateDeployment
[How to: Move deployments between staging and production]: #MoveDeployments
[How to: Delete a deployment]: #DeleteDeployment
[How to: Create a storage service]: #CreateStorageService
[How to: Delete a storage service]: #DeleteStorageService
[How to: List available operating systems]: #ListOperatingSystems
[How to: Create an operating system image]: #CreateVMImage
[How to: Delete an operating system image]: #DeleteVMImage
[How to: Create a virtual machine]: #CreateVM
[How to: Delete a virtual machine]: #DeleteVM
[Next Steps]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[cloud service]:https://azure.microsoft.com/en-us/documentation/services/cloud-services/



