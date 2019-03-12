---
title: Használja a Service Management API (Python) – funkcióismertető
description: Megtudhatja, hogyan programozott módon a Python a gyakori felügyeleti feladatok elvégzéséhez.
services: cloud-services
documentationcenter: python
author: lmazuel
manager: wpickett
editor: ''
ms.assetid: 61538ec0-1536-4a7e-ae89-95967fe35d73
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/30/2017
ms.author: lmazuel
ms.openlocfilehash: 573c6d3ded8fea58e0c9ba1afa7da2d8dd0fce91
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531835"
---
# <a name="use-service-management-from-python"></a>Kezelés a Python használata
Ez az útmutató bemutatja, hogyan programozott módon a Python a gyakori felügyeleti feladatok elvégzéséhez. A **ServiceManagementService** az osztály a [Pythonhoz készült Azure SDK](https://github.com/Azure/azure-sdk-for-python) támogatja a programozási szintű hozzáférést felügyelettel kapcsolatos funkciók, amelyek érhető el a [Azure portál][management-portal]. Ez a funkció segítségével létrehozása, frissítése és törlése a cloud services, központi telepítések, adatkezelési szolgáltatások és virtuális gépeket. Ez a funkció akkor lehet hasznos, a service management programozás alapú hozzáférést igénylő alkalmazások készítése.

## <a name="WhatIs"> </a>Mi a service management?
Az Azure Service Management API programozás alapú hozzáférést biztosít keresztül elérhető felügyeleti funkciók a [az Azure portal][management-portal]. A Pythonhoz készült Azure SDK használatával a cloud services és a storage-fiókok kezelése.

A Service Management API használatával kell [Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"> </a>Fogalmak
A Pythonhoz készült Azure SDK burkolja az [Service Management API][svc-mgmt-rest-api], amely egy olyan REST API. Az API műveletei SSL-kapcsolaton keresztül végrehajtani, és X.509 v3 tanúsítványok használatával kölcsönösen hitelesítettek. A felügyeleti szolgáltatás egy Azure-ban futó szolgáltatáson belül is elérhetők. Azt is elérhetők közvetlenül az interneten bármely olyan alkalmazásból, amely képes HTTPS-kéréseket küldeni és HTTPS-válaszokat fogadni.

## <a name="Installation"> </a>telepítés
Minden, az ebben a cikkben leírt funkciók érhetők el a `azure-servicemanagement-legacy` csomagot, amely a pip használatával telepítheti. Telepítés (például, ha most ismerkedik a Python) kapcsolatos további információkért lásd: [telepítse a Python és az Azure SDK](../python-how-to-install.md).

## <a name="Connect"> </a>Csatlakozhat a szolgáltatásfelügyelet
A szolgáltatás felügyeleti végponthoz csatlakozik, szüksége van Azure-előfizetése Azonosítóját és a egy érvényes felügyeleti tanúsítvány. Az előfizetés-azonosító keresztül szerezheti be a [az Azure portal][management-portal].

> [!NOTE]
> Most már használhatja a tanúsítvány openssl jön létre, amikor a Windows rendszerű. Python 2.7.4 vagy újabb verzió szükséges. Javasoljuk, hogy használjon OpenSSL helyett .pfx, mivel a .pfx-tanúsítványok valószínű, hogy a jövőben el lesz távolítva támogatása.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Felügyeleti tanúsítványok Windows/Mac/Linux (OpenSSL)
Használhat [OpenSSL](https://www.openssl.org/) a felügyeleti tanúsítvány létrehozása. Hozzon létre egyet a kiszolgáló két tanúsítványt kell (a `.cer` fájl) és a egy, az ügyfél (egy `.pem` fájlt). Hozhat létre a `.pem` fájlt, hajtsa végre:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Hozhat létre a `.cer` tanúsítvány, hajtsa végre:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Az Azure-tanúsítványokkal kapcsolatos további információkért lásd: [tanúsítványok áttekintése az Azure Cloud Services](cloud-services-certs-create.md). OpenSSL-paraméter teljes leírását lásd: a dokumentációban a [ https://www.openssl.org/docs/apps/openssl.html ](https://www.openssl.org/docs/apps/openssl.html).

Miután létrehozta ezeket a fájlokat, töltse fel a `.cer` fájlt az Azure-bA. Az a [az Azure portal][management-portal], az a **beállítások** lapon jelölje be **feltöltése**. Vegye figyelembe, ahová mentette a `.pem` fájlt.

Miután beszerezte az előfizetés-azonosító, hozzon létre egy tanúsítványt, és töltse fel a `.cer` fájlt az Azure-ba, hogy csatlakozzon az Azure felügyeleti végponthoz. Csatlakozás az előfizetés-azonosító és elérési útját adja át a `.pem` fájlt **ServiceManagementService**.

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

Az előző példában `sms` van egy **ServiceManagementService** objektum. A **ServiceManagementService** osztály az Azure-szolgáltatások kezelésére szolgáló elsődleges osztály.

### <a name="management-certificates-on-windows-makecert"></a>Windows (MakeCert) a felügyeleti tanúsítványok
Létrehozhat egy önaláírt felügyeleti tanúsítvány a gépen használatával `makecert.exe`. Nyissa meg a **Visual Studio parancssorából** , egy **rendszergazda** és használja a következő parancsot, és cserélje le *AzureCertificate* használni kívánt tanúsítványt a névvel:

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

A parancs létrehozza a `.cer` fájlt, és telepíti azt a **személyes** tanúsítványtárolójába. További információkért lásd: [tanúsítványok áttekintése az Azure Cloud Services](cloud-services-certs-create.md).

Miután létrehozta a tanúsítványt, töltse fel a `.cer` fájlt az Azure-bA. Az a [az Azure portal][management-portal], az a **beállítások** lapon jelölje be **feltöltése**.

Miután beszerezte az előfizetés-azonosító, hozzon létre egy tanúsítványt, és töltse fel a `.cer` fájlt az Azure-ba, hogy csatlakozzon az Azure felügyeleti végponthoz. Az előfizetés-azonosító és a tanúsítvány helye átadásával csatlakozzon a **személyes** tanúsítványtárolójának **ServiceManagementService** (ezúttal is helyettesítse be *AzureCertificate* az a tanúsítvány nevét jelöli).

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

Az előző példában `sms` van egy **ServiceManagementService** objektum. A **ServiceManagementService** osztály az Azure-szolgáltatások kezelésére szolgáló elsődleges osztály.

## <a name="ListAvailableLocations"> </a>Elérhető helyek listája
A helyek elérhető üzemeltetési szolgáltatások listájában, használja a **lista\_helyek** metódust.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Egy felhőalapú szolgáltatás, vagy a storage szolgáltatás létrehozásakor meg kell adnia egy érvényes helyet. A **lista\_helyek** metódus mindig naprakész a jelenleg elérhető helyek listáját adja vissza. Jelen cikk írásakor az elérhető helyek a következők:

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

## <a name="CreateCloudService"> </a>Felhőszolgáltatás létrehozása
Amikor alkalmazást hoz létre, és futtassa az Azure-ban, a kód és a konfigurációt együttesen az úgynevezett Azure-beli [felhőszolgáltatás][cloud service]. (Ez néven volt ismert, egy *üzemeltetett szolgáltatás* a korábbi Azure kiadásokban.) Használhatja a **létrehozása\_üzemeltetett\_szolgáltatás** metódust hozzon létre egy új üzemeltetett szolgáltatásban. A szolgáltatás létrehozása azáltal, hogy egy üzemeltetett szolgáltatás neve (amely az Azure-ban egyedinek kell lennie), egy címkét (automatikusan base64 kódolású), egy leírást és egy helyen.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Az előfizetéséhez tartozó összes üzemeltetett szolgáltatásához listázhatja a **lista\_üzemeltetett\_szolgáltatások** metódus.

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Egy adott üzemeltetett szolgáltatás adatainak lekérése, át kell adnia az üzemeltetett szolgáltatás neve, a **első\_üzemeltetett\_szolgáltatás\_tulajdonságok** metódust.

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Miután létrehozott egy felhőalapú szolgáltatás, a kód üzembe helyezése a szolgáltatáshoz a **létrehozása\_üzembe helyezési** metódust.

## <a name="DeleteCloudService"> </a>Egy felhőalapú szolgáltatás törlése
Cloud service tartalmában való böngészéshez illessze a szolgáltatás nevét, törölheti a **törlése\_üzemeltetett\_szolgáltatás** metódus.

    sms.delete_hosted_service('myhostedservice')

Szolgáltatás törlése előtt a szolgáltatás központi telepítések először törölni kell. További információkért lásd: [üzemelő példányának törlése](#DeleteDeployment).

## <a name="DeleteDeployment"> </a>Üzemelő példányának törlése
Központi telepítés törléséhez használja a **törlése\_üzembe helyezési** metódust. A következő példa bemutatja, hogyan nevű üzemelő példányának törlése `v1`:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>A storage szolgáltatás létrehozása
A [társzolgáltatás](../storage/common/storage-create-storage-account.md) teszi elérhetővé az Azure-bA [blobok](../storage/blobs/storage-python-how-to-use-blob-storage.md), [táblák](../cosmos-db/table-storage-how-to-use-python.md), és [üzenetsorok](../storage/queues/storage-python-how-to-use-queue-storage.md). Hozzon létre egy tárolási szolgáltatásba, a szolgáltatás (3 – 24 kisbetűs karaktert és egyedi Azure-ban) nevének kell. Egy leírást, címkét (legfeljebb 100 karakter, automatikusan Base64 kódolású) és egy helyen is szükséges. Az alábbi példa bemutatja, hogyan hozhat létre egy tárolási szolgáltatásba hely megadása:

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

Az előző példában az állapotát a **létrehozása\_tárolási\_fiók** művelet által visszaadott eredmény átadásával kérhető **létrehozása\_tárolási\_ fiók** , a **első\_művelet\_állapot** metódus. 

A tárfiókok és a tulajdonságaik listázhatja a **lista\_tárolási\_fiókok** metódust.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"> </a>A storage szolgáltatás törlése
A storage szolgáltatás törléséhez adja át a storage szolgáltatás nevét, a **törlése\_tárolási\_fiók** metódust. A storage szolgáltatás törlésekor (blobok, táblák és üzenetsorok) a szolgáltatásban tárolt összes adatot.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>Elérhető operációs rendszerek listázása
Üzemeltetési szolgáltatások rendelkezésre álló operációs rendszerek listáját, használja a **lista\_működő\_rendszerek** metódus.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Másik lehetőségként használhatja a **lista\_működő\_rendszer\_családok** metódussal, amely csoportosítja a termékcsalád az operációs rendszerek.

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"> </a>Operációsrendszer-lemezkép létrehozása
Operációs rendszer lemezképének hozzáadása a lemezképtárból, használja a **hozzáadása\_operációs rendszer\_kép** metódus.

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

Az operációsrendszer-lemezképeket, amelyek a rendelkezésre álló listát, használja a **lista\_operációs rendszer\_lemezképek** metódus. Platformlemezképek és a felhasználói lemezképek tartalmazza.

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

## <a name="DeleteVMImage"> </a>Operációsrendszer-lemezkép törlése
A felhasználói lemezképek törléséhez használja a **törlése\_operációs rendszer\_kép** metódust.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Virtuális gép létrehozása
Egy virtuális gépet létrehozni, először hozzon létre egy [felhőszolgáltatás](#CreateCloudService). Majd a használatával hozza létre a virtuális gép üzembe helyezésének a **létrehozása\_virtuális\_gép\_üzembe helyezési** metódust.

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

## <a name="DeleteVM"> </a>A virtuális gép törlése
A virtuális gépek törléséhez először törölnie a központi telepítés használatával a **törlése\_üzembe helyezési** metódust.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

A felhőszolgáltatás majd használatával törölhetők a **törlése\_üzemeltetett\_szolgáltatás** metódust.

    sms.delete_hosted_service(service_name='myvm')

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Virtuális gép létrehozása egy rögzített virtuálisgép-rendszerképből
Rögzítsen egy rendszerképet, akkor először hívja a **rögzítése\_vm\_kép** metódust.

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

Ahhoz, hogy sikerült rögzíteni a képet, használja a **lista\_vm\_lemezképek** API-t. Ellenőrizze, hogy a kép megjelenik az eredményeket.

    images = sms.list_vm_images()

Végül létrehozzuk a virtuális gép a rögzített lemezképet használja, használja a **létrehozása\_virtuális\_gép\_üzembe helyezési** metódus, ahogy korábban is, de ezúttal adja át a vm_image_name helyette.

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

A klasszikus üzemi modellben Linux rendszerű virtuális gép rögzítése kapcsolatos további tudnivalókért lásd: [Linux rendszerű virtuális gép rögzítése](../virtual-machines/linux/classic/capture-image-classic.md).

A klasszikus üzemi modellben Windows virtuális gép rögzítése kapcsolatos további tudnivalókért lásd: [Windows virtuális gép rögzítése](../virtual-machines/windows/classic/capture-image-classic.md).

## <a name="What's Next"></a>Következő lépések
Most, hogy megismerte a service management alapjait, hozzáférhet a [az Azure Python SDK teljes API dokumentációja](https://azure-sdk-for-python.readthedocs.org/) és összetett feladatokat, a Python-alkalmazás kezelése.

További információ: [Python fejlesztői központban](https://azure.microsoft.com/develop/python/).

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
