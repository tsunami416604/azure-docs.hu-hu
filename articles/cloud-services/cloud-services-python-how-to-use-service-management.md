---
title: A Service Management API (Python) szolgáltatás használata – útmutató
description: Ismerje meg, hogyan hajthat végre általános szolgáltatási felügyeleti feladatokat a Pythonból.
services: cloud-services
documentationcenter: python
author: tanmaygore
manager: vashan
editor: ''
ms.assetid: 61538ec0-1536-4a7e-ae89-95967fe35d73
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/30/2017
ms.author: tagore
ms.openlocfilehash: 135dd92f7af4397f2053ea0bdc15d98dfad93914
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81253356"
---
# <a name="use-service-management-from-python"></a>A Service Management használata a Pythonból
Ez az útmutató bemutatja, hogyan hajthat végre általános szolgáltatás-felügyeleti feladatokat a Pythonból. A [Pythonhoz készült Azure SDK](https://github.com/Azure/azure-sdk-for-python) **ServiceManagementService** osztálya támogatja a programozott hozzáférést a [Azure Portalban][management-portal]elérhető szolgáltatás-felügyeleti funkciók nagy része számára. Ezt a funkciót használhatja a Cloud Services, az üzemelő példányok, az adatkezelési szolgáltatások és a virtuális gépek létrehozásához, frissítéséhez és törléséhez. Ez a funkció hasznos lehet olyan alkalmazások létrehozásához, amelyeknek programozott hozzáférésre van szükségük a Service Management szolgáltatáshoz.

## <a name="what-is-service-management"></a><a name="WhatIs"> </a>Mi a Service Management?
Az Azure Service Management API programozott hozzáférést biztosít a [Azure Portal][management-portal]elérhető Service Management-funkciók nagy része számára. Használhatja a Pythonhoz készült Azure SDK-t a Cloud Services és a Storage-fiókok kezeléséhez.

A Service Management API használatához [létre kell hoznia egy Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/).

## <a name="concepts"></a><a name="Concepts"> </a>Alapelvek
A Pythonhoz készült Azure SDK becsomagolja a [Service Management API][svc-mgmt-rest-api], amely egy REST API. Minden API-művelet a TLS protokollon keresztül történik, és az X. 509 v3 tanúsítványok használatával kölcsönösen hitelesítve van. A kezelési szolgáltatás az Azure-ban futó szolgáltatáson belülről érhető el. Emellett közvetlenül az interneten keresztül is elérhető bármely olyan alkalmazásból, amely képes HTTPS-kéréseket küldeni és HTTPS-válaszokat fogadni.

## <a name="installation"></a><a name="Installation"> </a>Telepítés
A cikkben ismertetett összes funkció elérhető a `azure-servicemanagement-legacy` csomagban, amelyet a pip használatával telepíthet. További információ a telepítésről (például ha még nem ismeri a Pythont) a [Python és az Azure SDK telepítése](/azure/developer/python/azure-sdk-install)című témakörben talál további információt.

## <a name="connect-to-service-management"></a><a name="Connect"> </a>Kapcsolódás a Service Managementhez
A Service Management-végponthoz való kapcsolódáshoz szüksége lesz az Azure-előfizetés AZONOSÍTÓJÁRA és egy érvényes Felügyeleti tanúsítványra. Az előfizetés-azonosítót a [Azure Portal][management-portal]keresztül szerezheti be.

> [!NOTE]
> Mostantól használhatja az OpenSSL-vel létrehozott tanúsítványokat a Windows rendszeren való futtatáskor. Python-2.7.4 vagy újabb verzió szükséges. Javasoljuk, hogy a. pfx helyett használja az OpenSSL-t, mert a. pfx-tanúsítványok támogatása valószínűleg a jövőben is megszűnik.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Felügyeleti tanúsítványok Windows/Mac/Linux rendszeren (OpenSSL)
A felügyeleti tanúsítvány létrehozásához az [OpenSSL](https://www.openssl.org/) -t használhatja. Létre kell hoznia két tanúsítványt, egyet a kiszolgálóhoz (egy `.cer` fájl), egyet pedig az ügyfélhez (egy `.pem` fájl). A fájl létrehozásához hajtsa végre a `.pem` következőt:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

A tanúsítvány létrehozásához hajtsa végre a `.cer` következőt:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Az Azure-tanúsítványokkal kapcsolatos további információkért lásd: [Az Azure-beli tanúsítványok áttekintése Cloud Services](cloud-services-certs-create.md). Az OpenSSL paramétereinek teljes leírását a dokumentációjában találja [https://www.openssl.org/docs/apps/openssl.html](https://www.openssl.org/docs/apps/openssl.html).

A fájlok létrehozása után töltse fel a fájlt `.cer` az Azure-ba. A [Azure Portal][management-portal] **Beállítások** lapján válassza a **feltöltés**lehetőséget. Vegye figyelembe, hogy hová `.pem` mentette a fájlt.

Az előfizetés-azonosító beszerzése után hozzon létre egy tanúsítványt, `.cer` és töltse fel a fájlt az Azure-ba, és kapcsolódjon az Azure felügyeleti végponthoz. Kapcsolódjon az előfizetés-azonosító és a `.pem` **ServiceManagementService**fájl elérési útjának átadásával.

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

Az előző példában `sms` egy **ServiceManagementService** objektum. Az **ServiceManagementService** osztály az Azure-szolgáltatások kezeléséhez használt elsődleges osztály.

### <a name="management-certificates-on-windows-makecert"></a>Felügyeleti tanúsítványok Windows rendszeren (MakeCert)
A használatával `makecert.exe`önaláírt felügyeleti tanúsítványt hozhat létre a gépen. Nyisson meg egy **Visual Studio-parancssort** **rendszergazdaként** , és használja a következő parancsot, és cserélje le a *AzureCertificate* a használni kívánt tanúsítvány nevére:

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

A parancs létrehozza a `.cer` fájlt, és telepíti a **személyes** tanúsítványtárolóba. További információ: [Az Azure Cloud Services tanúsítványok áttekintése](cloud-services-certs-create.md).

A tanúsítvány létrehozása után töltse fel a `.cer` fájlt az Azure-ba. A [Azure Portal][management-portal] **Beállítások** lapján válassza a **feltöltés**lehetőséget.

Az előfizetés-azonosító beszerzése után hozzon létre egy tanúsítványt, `.cer` és töltse fel a fájlt az Azure-ba, és kapcsolódjon az Azure felügyeleti végponthoz. A kapcsolódáshoz adja át az előfizetés-azonosítót és a tanúsítvány helyét a **személyes** tanúsítványtárolóban a **ServiceManagementService** (újra, cserélje le a *AzureCertificate* nevet a tanúsítvány nevével).

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

Az előző példában `sms` egy **ServiceManagementService** objektum. Az **ServiceManagementService** osztály az Azure-szolgáltatások kezeléséhez használt elsődleges osztály.

## <a name="list-available-locations"></a><a name="ListAvailableLocations"> </a>Elérhető helyszínek listázása
A szolgáltatások üzemeltetéséhez elérhető helyszínek listázásához használja a **\_List Locations** metódust.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Felhőalapú szolgáltatás vagy tárolási szolgáltatás létrehozásakor érvényes helyet kell megadnia. A **List\_Locations** metódus mindig az aktuálisan elérhető helyeinek naprakész listáját adja vissza. Ebben az írásban az elérhető helyszínek a következők:

* Nyugat-Európa
* Észak-Európa
* Délkelet-Ázsia
* Kelet-Ázsia
* USA középső régiója
* USA északi középső régiója
* USA déli középső régiója
* USA nyugati régiója
* USA keleti régiója
* Kelet-Japán
* Nyugat-Japán
* Dél-Brazília
* Kelet-Ausztrália
* Délkelet-Ausztrália

## <a name="create-a-cloud-service"></a><a name="CreateCloudService"> </a>Felhőalapú szolgáltatás létrehozása
Amikor létrehoz egy alkalmazást, és futtatja az Azure-ban, a kód és a konfiguráció együtt egy Azure [Cloud Service][cloud service]néven is ismert. (A korábbi Azure-kiadásokban *üzemeltetett szolgáltatásként* ismert.) Az **\_üzemeltetett szolgáltatás létrehozása\_** módszert használhatja egy új üzemeltetett szolgáltatás létrehozásához. Hozza létre a szolgáltatást egy üzemeltetett szolgáltatás nevének megadásával (amely egyedinek kell lennie az Azure-ban), egy címkét (automatikusan kódolva Base64-re), egy leírást és egy helyet.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Az előfizetéshez tartozó összes üzemeltetett szolgáltatást listázhatja az **\_üzemeltetett\_szolgáltatások listájának** használatával.

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Egy adott üzemeltetett szolgáltatással kapcsolatos információk lekéréséhez adja át az üzemeltetett szolgáltatás nevét az **üzemeltetett\_\_szolgáltatás\_tulajdonságainak beolvasása** metódusnak.

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

A felhőalapú szolgáltatás létrehozása után telepítse a kódot a szolgáltatásba a **create\_Deployment** metódussal.

## <a name="delete-a-cloud-service"></a><a name="DeleteCloudService"> </a>Felhőalapú szolgáltatás törlése
A felhőalapú szolgáltatások törléséhez továbbítsa a szolgáltatás nevét az **\_üzemeltetett szolgáltatás\_törlése** metódusnak.

    sms.delete_hosted_service('myhostedservice')

A szolgáltatás törléséhez először törölni kell a szolgáltatás összes központi telepítését. További információ: [központi telepítés törlése](#DeleteDeployment).

## <a name="delete-a-deployment"></a><a name="DeleteDeployment"> </a>Központi telepítés törlése
A központi telepítés törléséhez használja az **\_üzembe helyezési** módszert. Az alábbi példa bemutatja, hogyan törölhet egy nevű `v1`központi telepítést:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="create-a-storage-service"></a><a name="CreateStorageService"> </a>Tárolási szolgáltatás létrehozása
A [Storage szolgáltatás](../storage/common/storage-create-storage-account.md) hozzáférést biztosít az Azure- [blobokhoz](../storage/blobs/storage-python-how-to-use-blob-storage.md),- [táblákhoz](../cosmos-db/table-storage-how-to-use-python.md)és- [várólistákhoz](../storage/queues/storage-python-how-to-use-queue-storage.md). A tárolási szolgáltatás létrehozásához szüksége lesz a szolgáltatás nevére (3 – 24 kisbetűs karakter és az Azure-on belül egyedi). Szükség van egy leírásra is, egy címkére (legfeljebb 100 karakter, automatikusan Base64-re kódolva) és egy helyre. Az alábbi példa bemutatja, hogyan hozhat létre tárolási szolgáltatást egy hely megadásával:

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

Az előző példában a **Storage\_\_-fiók létrehozása** művelet állapota lekérdezhető úgy, hogy átadja a **Storage\_-fiók létrehozásával\_** visszaadott eredményt a **lekérési\_művelet\_állapota** metódusnak. 

A Storage-fiókokat és azok tulajdonságait listázhatja a **Storage\_\_-fiókok listázása** módszer használatával.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="delete-a-storage-service"></a><a name="DeleteStorageService"> </a>Tárolási szolgáltatás törlése
A tárolási szolgáltatás törléséhez adja át a tárolási szolgáltatás nevét a **\_Storage\_-fiók törlése** metódusnak. A tárolási szolgáltatás törlésekor a szolgáltatásban tárolt összes adathalmaz (Blobok, táblák és várólisták) törlődik.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="list-available-operating-systems"></a><a name="ListOperatingSystems"> </a>Elérhető operációs rendszerek listázása
A szolgáltatások üzemeltetéséhez elérhető operációs rendszerek listázásához használja az **operációs\_rendszerek listázása\_** módszert.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Azt is megteheti, **hogy\_az\_operációsrendszer\_-családok listázása** módszert használja, amely az operációs rendszereket család szerint csoportosítja.

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="create-an-operating-system-image"></a><a name="CreateVMImage"> </a>Operációs rendszer rendszerképének létrehozása
Az operációsrendszer-rendszerkép rendszerkép-tárházhoz való hozzáadásához használja az **operációsrendszer\_\_-rendszerkép hozzáadása** módszert.

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

Az elérhető operációsrendszer-lemezképek listázásához használja az **operációsrendszer\_\_-lemezképek listázása** módszert. Minden platform-lemezképet és felhasználói lemezképet tartalmaz.

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

## <a name="delete-an-operating-system-image"></a><a name="DeleteVMImage"> </a>Operációs rendszer rendszerképének törlése
Felhasználói rendszerkép törléséhez használja az **\_operációsrendszer-rendszerkép\_törlése** módszert.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="create-a-virtual-machine"></a><a name="CreateVM"> </a>Virtuális gép létrehozása
Virtuális gép létrehozásához először létre kell hoznia egy [felhőalapú szolgáltatást](#CreateCloudService). Ezután hozza létre a virtuális gép központi telepítését a **virtuális\_\_gép\_létrehozása** módszer használatával.

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

## <a name="delete-a-virtual-machine"></a><a name="DeleteVM"> </a>Virtuális gép törlése
Egy virtuális gép törléséhez először törölje a központi telepítést az **üzembe helyezési módszer\_törlése** paranccsal.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

A Cloud Service ezután törölhető az **\_üzemeltetett szolgáltatás törlése\_** módszer használatával.

    sms.delete_hosted_service(service_name='myvm')

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Virtuális gép létrehozása rögzített virtuálisgép-rendszerképből
A virtuálisgép-lemezkép rögzítéséhez először meg kell hívnia a **\_Capture VM\_-lemezkép** módszert.

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

A lemezkép rögzítésének biztosításához használja a virtuálisgép- **\_\_lemezképek listázása** API-t. Győződjön meg arról, hogy a rendszerkép megjelenik az eredmények között.

    images = sms.list_vm_images()

Ha végül a virtuális gépet a rögzített lemezkép használatával szeretné létrehozni, használja a **\_virtuális\_\_gép központi telepítési** módszerét mint korábban, de ezúttal a vm_image_name.

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

A Linux rendszerű virtuális gépek klasszikus üzemi modellben való rögzítésével kapcsolatos további információkért lásd: [Linux rendszerű virtuális gép rögzítése](../virtual-machines/linux/classic/capture-image-classic.md).

A Windows rendszerű virtuális gépek klasszikus üzemi modellben való rögzítésével kapcsolatos további információkért lásd: [Windows rendszerű virtuális gép rögzítése](../virtual-machines/windows/classic/capture-image-classic.md).

## <a name="next-steps"></a><a name="What's Next"> </a>További lépések
Most, hogy megismerte a Service Management alapjait, elérheti az [Azure PYTHON SDK-hoz készült teljes API-dokumentációt](https://azure-sdk-for-python.readthedocs.org/) , és a Python-alkalmazások felügyeletére szolgáló összetett feladatokat is könnyedén elvégezheti.

További információ: [Python fejlesztői központ](https://azure.microsoft.com/develop/python/).

[What is service management?]: #WhatIs
[Concepts]: #Concepts
[Connect to service management]: #Connect
[List available locations]: #ListAvailableLocations
[Create a cloud service]: #CreateCloudService
[Delete a cloud service]: #DeleteCloudService
[Create a deployment]: #CreateDeployment
[Update a deployment]: #UpdateDeployment
[Move deployments between staging and production]: #MoveDeployments
[Delete a deployment]: #DeleteDeployment
[Create a storage service]: #CreateStorageService
[Delete a storage service]: #DeleteStorageService
[List available operating systems]: #ListOperatingSystems
[Create an operating system image]: #CreateVMImage
[Delete an operating system image]: #DeleteVMImage
[Create a virtual machine]: #CreateVM
[Delete a virtual machine]: #DeleteVM
[Next steps]: #NextSteps
[management-portal]: https://portal.azure.com/
[svc-mgmt-rest-api]: https://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[cloud service]:/azure/cloud-services/
