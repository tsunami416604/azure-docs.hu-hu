---
title: A Service Management API-val (Python) - funkcióismertető
description: Megtudhatja, hogyan programozott módon hajtható végre az általános szolgáltatás-felügyeleti feladatokat a Python.
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
ms.openlocfilehash: b89f1aad46621d35728934ea068a5893ba674094
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
ms.locfileid: "29124799"
---
# <a name="use-service-management-from-python"></a>Kezelés a Python használata
Ez az útmutató bemutatja, hogyan programozott módon hajtható végre az általános szolgáltatás-felügyeleti feladatokat a Python. A **ServiceManagementService** osztályt a [Azure SDK for Python](https://github.com/Azure/azure-sdk-for-python) programozott hozzáférést a nagy részét a szolgáltatás-felügyelettel kapcsolatos funkció elérhető támogatja a [Azure portál][management-portal]. Ez a funkció segítségével létrehozása, frissítése és törlése a felhőszolgáltatások, központi telepítések, adatok szolgáltatások és virtuális gépek. Ez a funkció akkor lehet hasznos, szolgáltatás-felügyelet programozott hozzáférést igénylő alkalmazások fejlesztése során.

## <a name="WhatIs"></a>Mi az az service management?
Az Azure szolgáltatásfelügyeleti API nagy részét a szolgáltatás felügyeleti funkció keresztül elérhető programozott hozzáférést biztosít a [Azure-portálon][management-portal]. A felhőszolgáltatás és a storage-fiókok kezeléséhez használhatja az Azure SDK for Python.

A szolgáltatásfelügyeleti API használatával kell [az Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"></a>Fogalmak
Az Azure SDK for Python becsomagolja a [szolgáltatásfelügyeleti API][svc-mgmt-rest-api], vagyis a REST API-t. Minden API-műveleteket SSL keresztül történik, és kölcsönösen hitelesített X.509 v3 tanúsítványokkal. A felügyeleti szolgáltatás belül az Azure-ban futó szolgáltatásban is elérhetők. Azt is elérhetők közvetlenül bármely alkalmazásból, amely egy HTTPS-kéréseket küldhet és fogadhat HTTPS választ az interneten keresztül.

## <a name="Installation"></a>Telepítése
Ebben a cikkben leírt összes funkcióját érhetők el a `azure-servicemanagement-legacy` csomagot, amely a pip használatával telepítheti. (Például, ha most ismerkedik a Python) telepítésével kapcsolatos további információkért lásd: [Python telepítése és az Azure SDK](../python-how-to-install.md).

## <a name="Connect"></a>Szolgáltatásfelügyelet kapcsolódás
A szolgáltatás felügyeleti végponthoz kapcsolódni, Azure-előfizetése Azonosítóját és egy érvényes felügyeleti tanúsítványt kell. Ezt úgy szerezheti be az előfizetés-Azonosítóval keresztül a [Azure-portálon][management-portal].

> [!NOTE]
> Windows futtatásakor OpenSSL létre tanúsítványokat most is használhat. Python 2.7.4 vagy újabb verzió szükséges. Azt javasoljuk, hogy használjon OpenSSL helyett .pfx, mivel a .pfx-tanúsítványok valószínű, hogy a jövőben eltávolításra támogatása.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Felügyeleti tanúsítványok Windows vagy Mac/Linux (OpenSSL)
Használhat [OpenSSL](http://www.openssl.org/) a felügyeleti tanúsítvány létrehozása. Hozzon létre egy, a kiszolgáló két tanúsítványt kell (a `.cer` fájl) és egy, az ügyfél (egy `.pem` fájl). Létrehozásához a `.pem` fájlt, hajtható végre:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Létrehozásához a `.cer` tanúsítvány, hajtható végre:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Az Azure-tanúsítványokkal kapcsolatos további információkért lásd: [tanúsítványok áttekintése Azure-szolgáltatásokhoz](cloud-services-certs-create.md). OpenSSL-paraméter teljes leírását lásd: a dokumentációban a [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

Miután létrehozta ezeket a fájlokat, töltse fel a `.cer` fájl az Azure-bA. Az a [Azure-portálon][management-portal], az a **beállítások** lapon jelölje be **feltöltése**. Vegye figyelembe, ahová menteni szeretné a `.pem` fájlt.

Miután beszerezte az előfizetés-Azonosítóval, hozzon létre egy tanúsítványt, és töltse fel a `.cer` fájlt az Azure, az Azure felügyeleti végponthoz kapcsolódni. Csatlakozás úgy, hogy az előfizetés-azonosító és elérési útját a `.pem` fájl **ServiceManagementService**.

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

Az előző példában `sms` van egy **ServiceManagementService** objektum. A **ServiceManagementService** osztály az Azure-szolgáltatások kezelésére szolgáló elsődleges osztály.

### <a name="management-certificates-on-windows-makecert"></a>A Windows (MakeCert) felügyeleti tanúsítványok
Létrehozhat egy önaláírt felügyeleti tanúsítvány a gépen a `makecert.exe`. Nyissa meg a **Visual Studio parancssorból** , egy **rendszergazda** és használja a következő parancsot, cseréje *AzureCertificate* használni kívánt tanúsítvány nevű:

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

A parancs létrehozza a `.cer` fájlt, és telepíti azt a a **személyes** tanúsítványtárolójába. További információkért lásd: [tanúsítványok áttekintése Azure-szolgáltatásokhoz](cloud-services-certs-create.md).

Miután létrehozta a tanúsítványt, töltse fel a `.cer` fájl az Azure-bA. Az a [Azure-portálon][management-portal], az a **beállítások** lapon jelölje be **feltöltése**.

Miután beszerezte az előfizetés-Azonosítóval, hozzon létre egy tanúsítványt, és töltse fel a `.cer` fájlt az Azure, az Azure felügyeleti végponthoz kapcsolódni. Csatlakozás úgy, hogy az előfizetés-azonosító és a tanúsítvány helye a **személyes** tanúsítványtárolójának **ServiceManagementService** (ebben az esetben cserélje le *AzureCertificate* a tanúsítvány nevével).

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

Az előző példában `sms` van egy **ServiceManagementService** objektum. A **ServiceManagementService** osztály az Azure-szolgáltatások kezelésére szolgáló elsődleges osztály.

## <a name="ListAvailableLocations"></a>Elérhető helyről felsorolása
A helyek elérhető szolgáltatásait üzemeltető kilistázhatja a **lista\_helyek** metódus.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Egy felhőalapú szolgáltatás vagy a storage szolgáltatás létrehozásakor meg kell adnia egy érvényes helyet. A **lista\_helyek** metódus mindig a jelenleg elérhető helyről naprakész listáját adja vissza. Től írásának, a helyeket a következők:

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

## <a name="CreateCloudService"></a>Felhőalapú szolgáltatás létrehozása
Amikor létrehoz egy alkalmazást, és futtassa az Azure-ban, a kódot és konfigurációs együtt nevezzük az Azure [felhőalapú szolgáltatás][cloud service]. (Az ismert egy *üzemeltetett szolgáltatás* a korábbi Azure kibocsátásokban megtörténik.) Használhatja a **létrehozása\_üzemeltetett\_szolgáltatás** hozzon létre egy új módszer üzemeltetett szolgáltatásban. A szolgáltatás létrehozása, azáltal, hogy egy futtatott szolgáltatás nevét (amelyen az Azure-ban egyedinek kell lennie), (automatikusan Base64 kódolású) címke, leírását és egy helyet.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Előfizetés az összes üzemeltetett szolgáltatás listázhatja a **lista\_üzemeltetett\_szolgáltatások** metódust.

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Egy adott tárolt szolgáltatás adatainak lekéréséhez adja át a futtatott szolgáltatás nevét, hogy a **beolvasása\_üzemeltetett\_szolgáltatás\_tulajdonságok** metódust.

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Miután létrehozott egy felhőalapú szolgáltatás, a kód telepítésére a szolgáltatás a **létrehozása\_telepítési** metódus.

## <a name="DeleteCloudService"></a>Felhőszolgáltatás törlése
Egy felhőalapú szolgáltatás törölheti úgy, hogy a szolgáltatás nevét a **törlése\_üzemeltetett\_szolgáltatás** metódust.

    sms.delete_hosted_service('myhostedservice')

A szolgáltatás törlése előtt a szolgáltatás központi telepítéseinek először törölni kell. További információkért lásd: [törölni olyan üzemelő példányt](#DeleteDeployment).

## <a name="DeleteDeployment"></a>a központi telepítés törlése
A központi telepítés törléséhez használja a **törlése\_telepítési** metódust. A következő példa bemutatja, hogyan törölhető egy központi telepítést `v1`:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"></a>Tárolási szolgáltatás létrehozása
A [társzolgáltatás](../storage/common/storage-create-storage-account.md) biztosít hozzáférést az Azure-bA [blobok](../storage/blobs/storage-python-how-to-use-blob-storage.md), [táblák](../cosmos-db/table-storage-how-to-use-python.md), és [várólisták](../storage/queues/storage-python-how-to-use-queue-storage.md). Tárolási szolgáltatás létrehozása szüksége van a szolgáltatás (kisbetűket 3 és 24 közötti és egyedi az Azure) nevét. Szükség leírását, a címkék (legfeljebb 100 karakter, automatikusan a Base64 kódolású) és egy helyet. A következő példa bemutatja, hogyan hozza létre a társzolgáltatás a hely megadásával:

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

Az előző példában állapotának a **létrehozása\_tárolási\_fiók** művelet által visszaadott eredmény átadásával kérhető **létrehozása\_tárolási\_ fiók** számára a **beolvasása\_művelet\_állapot** metódus. 

A storage-fiókok és az azok tulajdonságait listázhatja a **lista\_tárolási\_fiókok** metódus.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"></a>Egy társzolgáltatás törlése
A storage szolgáltatás törléséhez adja át a szolgáltatás nevét a a **törlése\_tárolási\_fiók** metódust. A storage szolgáltatás törlése (blobot, táblát és üzenetsort) szolgáltatásban tárolt összes adat törlése.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"></a>Listából a rendelkezésre álló operációs rendszeren
Elérhető szolgáltatásait üzemeltető operációs rendszerek listáját, használja a **lista\_működő\_rendszerek** metódust.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Másik lehetőségként használhatja a **lista\_működő\_rendszer\_családok** metódus, amely csoportosítja a termékcsalád operációs rendszerek.

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"></a>Az operációsrendszer-lemezkép létrehozása
A lemezképtár adhat hozzá az operációs rendszer lemezképét, a **hozzáadása\_os\_kép** metódus.

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

Kilistázhatja az elérhető rendszerképek az **lista\_os\_képek** metódus. Összes platform képek és felhasználói lemezképeket tartalmaz.

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

## <a name="DeleteVMImage"></a>Egy operációsrendszer-lemezkép törlése
Egy felhasználói lemezkép törléséhez használja a **törlése\_os\_kép** metódust.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"></a>Virtuális gép létrehozása
A virtuális gép létrehozásához először hozzon létre egy [felhőalapú szolgáltatás](#CreateCloudService). Ezután hozzon létre virtuálisgép-példány használatával a **létrehozása\_virtuális\_gép\_telepítési** metódus.

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

## <a name="DeleteVM"></a>Egy virtuális gép törlése
Törölje a virtuális gépet, használatához először törölni az üzemelő példány használatával a **törlése\_telepítési** metódust.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

A felhőalapú szolgáltatás majd segítségével törölhetők a **törlése\_üzemeltetett\_szolgáltatás** metódus.

    sms.delete_hosted_service(service_name='myvm')

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>A virtuális gép egy rögzített virtuálisgép-lemezkép létrehozása
A virtuális gép lemezképének, akkor először hívja meg a **rögzítése\_vm\_kép** metódust.

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

Győződjön meg arról, hogy sikerült rögzíteni a képet, használja a **lista\_vm\_képek** API. Győződjön meg arról, hogy a kép megjelenik az eredmények között.

    images = sms.list_vm_images()

Végül a rögzített lemezkép használatával hozhat létre a virtuális gép, a **létrehozása\_virtuális\_gép\_telepítési** módszer, mielőtt, de ezúttal adjon át a vm_image_name helyette.

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

A klasszikus üzembe helyezési modellel Linux virtuális gép rögzítése kapcsolatban további tudnivalókért lásd: [Linux virtuális gép rögzítése](../virtual-machines/linux/classic/capture-image-classic.md).

A klasszikus üzembe helyezési modellel Windows virtuális gép rögzítése kapcsolatban további tudnivalókért lásd: [Windows virtuális gép rögzítése](../virtual-machines/windows/classic/capture-image-classic.md).

## <a name="What's Next"></a>További lépések
Most, hogy megismerte a service management alapjait, hogy elérhető a [teljes API referenciadokumentációt tartalmaz az Azure Python SDK](http://azure-sdk-for-python.readthedocs.org/) , és végezze el az összetett feladatok könnyedén kezelheti a Python alkalmazást.

További információ: [Python fejlesztői központban](/develop/python/).

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
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[cloud service]:/services/cloud-services/
