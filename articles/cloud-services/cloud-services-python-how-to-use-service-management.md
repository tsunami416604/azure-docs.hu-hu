---
title: A Service Management API (Python) használata – szolgáltatáskalauz használata
description: Ismerje meg, hogyan végezhet programozott módon a Python gyakori szolgáltatáskezelési feladatait.
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
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253356"
---
# <a name="use-service-management-from-python"></a>Szolgáltatáskezelés használata a Pythonból
Ez az útmutató bemutatja, hogyan programozott módon hajthatja végre a Python gyakori szolgáltatáskezelési feladatait. Az [Azure SDK python-ban](https://github.com/Azure/azure-sdk-for-python) található **ServiceManagementService** osztály programozott hozzáférést biztosít az [Azure Portalon][management-portal]elérhető szolgáltatáskezelési funkciók nagy részéhez. Ezzel a funkcióval felhőszolgáltatásokat, központi telepítéseket, adatkezelési szolgáltatásokat és virtuális gépeket hozhat létre, frissíthet és törölhet. Ez a funkció hasznos lehet olyan alkalmazások készítéséhez, amelyek programozott hozzáférést igényelnek a szolgáltatáskezeléshez.

## <a name="what-is-service-management"></a><a name="WhatIs"> </a>Mi a szolgáltatáskezelés?
Az Azure Service Management API programozott hozzáférést biztosít az [Azure Portalon][management-portal]keresztül elérhető szolgáltatáskezelési funkciók nagy részéhez. Az Azure SDK python-hoz a felhőszolgáltatások és a tárfiókok kezeléséhez.

A Service Management API használatához létre kell [hoznia egy Azure-fiókot.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="concepts"></a><a name="Concepts"> </a>Alapelvek
Az Azure SDK python-hoz burkolja a [Service Management API,][svc-mgmt-rest-api]amely egy REST API.The Azure SDK for Python wraps the Service Management API , which is a REST API. Minden API-művelet TLS-en keresztül történik, és az X.509 v3-as tanúsítványok használatával kölcsönösen hitelesítik őket. A felügyeleti szolgáltatás az Azure-ban futó szolgáltatásból érhető el. Azt is elérhető közvetlenül az interneten keresztül bármely alkalmazás, amely képes küldeni egy HTTPS-kérelmet, és https-választ kap.

## <a name="installation"></a><a name="Installation"> </a>Telepítés
A cikkben ismertetett összes funkció `azure-servicemanagement-legacy` elérhető a csomagban, amelyet pip használatával telepíthet. A telepítésről további információt (például ha még nem is a Pythont szeretné megtudni) a [Python telepítése és az Azure SDK című](/azure/developer/python/azure-sdk-install)témakörben talál.

## <a name="connect-to-service-management"></a><a name="Connect"> </a>Csatlakozás szolgáltatáskezeléshez
A szolgáltatáskezelési végponthoz való csatlakozáshoz szüksége van az Azure-előfizetés-azonosítóra és egy érvényes felügyeleti tanúsítványra. Az előfizetés-azonosítót az [Azure Portalon][management-portal]keresztül szerezheti be.

> [!NOTE]
> Windows rendszeren való futtatáskor most már használhatja az OpenSSL-lel létrehozott tanúsítványokat. Python 2.7.4-es vagy újabb szükséges. Javasoljuk, hogy a .pfx helyett használja az OpenSSL-t, mivel a .pfx tanúsítványok támogatása valószínűleg a jövőben megszűnik.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Kezelési tanúsítványok Windows/Mac/Linux rendszeren (OpenSSL)
Az [OpenSSL](https://www.openssl.org/) segítségével létrehozhatja a felügyeleti tanúsítványt. Létre kell hoznia két tanúsítványt, `.cer` egyet a kiszolgálóhoz (egy `.pem` fájlhoz) és egyet az ügyfélhez (egy fájlhoz). A `.pem` fájl létrehozásához hajtsa végre a következőt:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

A `.cer` tanúsítvány létrehozásához hajtsa végre a következőt:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Az Azure-tanúsítványokról az Azure Cloud Services tanúsítványok – áttekintéscímű témakörben olvashat [bővebben.](cloud-services-certs-create.md) Az OpenSSL paramétereinek teljes leírását a [https://www.openssl.org/docs/apps/openssl.html](https://www.openssl.org/docs/apps/openssl.html)dokumentációban találja: .

Miután létrehozta ezeket `.cer` a fájlokat, töltse fel a fájlt az Azure-ba. Az [Azure Portalon][management-portal]a **Beállítások** lapon válassza a **Feltöltés lehetőséget.** Megjegyzés: a `.pem` fájl mentési helye.

Miután megszerezte az előfizetés-azonosítóját, hozzon `.cer` létre egy tanúsítványt, és töltse fel a fájlt az Azure-ba, csatlakozzon az Azure felügyeleti végponthoz. Az előfizetés-azonosító és a fájl `.pem` elérési útjának átadása a **ServiceManagementService szolgáltatásnak.**

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

Az előző példában `sms` egy **ServiceManagementService** objektum. A **ServiceManagementService** osztály az Azure-szolgáltatások kezelésére használt elsődleges osztály.

### <a name="management-certificates-on-windows-makecert"></a>Felügyeleti tanúsítványok Windows rendszeren (MakeCert)
A használatával `makecert.exe`önaláírt felügyeleti tanúsítványt hozhat létre a számítógépen. Nyisson meg egy **Visual Studio parancssort** **rendszergazdaként,** és használja a következő parancsot, és cserélje le az *AzureCertificate tanúsítványnevét* a használni kívánt tanúsítványnevére:

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

A parancs `.cer` létrehozza a fájlt, és telepíti a **Személyes** tanúsítványtárolóban. További információt az [Azure Cloud Services tanúsítványok – áttekintés című témakörben talál.](cloud-services-certs-create.md)

A tanúsítvány létrehozása után `.cer` töltse fel a fájlt az Azure-ba. Az [Azure Portalon][management-portal]a **Beállítások** lapon válassza a **Feltöltés lehetőséget.**

Miután megszerezte az előfizetés-azonosítóját, hozzon `.cer` létre egy tanúsítványt, és töltse fel a fájlt az Azure-ba, csatlakozzon az Azure felügyeleti végponthoz. Csatlakozzon az előfizetés-azonosító és a tanúsítvány helyét a **személyes** tanúsítványtárolóban a **ServiceManagementService** (ismét cserélje *le az AzureCertificate* a tanúsítvány nevét).

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

Az előző példában `sms` egy **ServiceManagementService** objektum. A **ServiceManagementService** osztály az Azure-szolgáltatások kezelésére használt elsődleges osztály.

## <a name="list-available-locations"></a><a name="ListAvailableLocations"> </a>Az elérhető helyek listázása
A tárhelyszolgáltatások számára rendelkezésre álló helyek listázásához használja a **listahelyek\_** módszert.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Amikor létrehoz egy felhőalapú szolgáltatást vagy tárolási szolgáltatást, meg kell adnia egy érvényes helyet. A **\_listahelyek** módszer mindig a jelenleg elérhető helyek naprakész listáját adja vissza. Mivel az írás, a rendelkezésre álló helyek a következők:

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

## <a name="create-a-cloud-service"></a><a name="CreateCloudService"> </a>Felhőszolgáltatás létrehozása
Amikor létrehoz egy alkalmazást, és futtatja azt az Azure-ban, a kódot és a konfigurációt együtt [azure-felhőszolgáltatásnak][cloud service]nevezzük. (A korábbi Azure-kiadásokban *üzemeltetett szolgáltatásként* ismert.) A **hosted\_service\_** metódus létrehozása új üzemeltetett szolgáltatás létrehozásához használhatja. Hozza létre a szolgáltatást egy üzemeltetett szolgáltatás név (amely egyedinek kell lennie az Azure-ban), egy címkét (automatikusan kódolt base64), egy leírást, és egy helyet.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Az előfizetéshez kapcsolódó összes üzemeltetett szolgáltatást a **lista\_üzemeltetett\_szolgáltatások** módszerével sorolhatja fel.

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Egy adott üzemeltetett szolgáltatással kapcsolatos információkért adja át a hosztolt szolgáltatás nevét a **get\_hosted\_service\_properties** metódusnak.

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Miután létrehozott egy felhőalapú szolgáltatást, telepítse a kódot a szolgáltatása a **létrehozási\_telepítési** módszerrel.

## <a name="delete-a-cloud-service"></a><a name="DeleteCloudService"> </a>Felhőszolgáltatás törlése
A felhőalapú szolgáltatás törléséhez adja át a szolgáltatás nevét a **törlés\_üzemeltetett\_szolgáltatás** metódusát.

    sms.delete_hosted_service('myhostedservice')

Egy szolgáltatás törlése előtt a szolgáltatás összes központi telepítését először törölni kell. További információt a Központi telepítés törlése című [témakörben talál.](#DeleteDeployment)

## <a name="delete-a-deployment"></a><a name="DeleteDeployment"> </a>Központi telepítés törlése
Központi telepítés törléséhez használja a **törlési\_telepítési** módszert. A következő példa bemutatja, `v1`hogyan lehet törölni egy elnevezett központi telepítést:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="create-a-storage-service"></a><a name="CreateStorageService"> </a>Tárolási szolgáltatás létrehozása
A [storage szolgáltatás](../storage/common/storage-create-storage-account.md) hozzáférést biztosít az Azure-blobokhoz, [táblákhoz](../cosmos-db/table-storage-how-to-use-python.md)és [várólistákhoz.](../storage/queues/storage-python-how-to-use-queue-storage.md) [blobs](../storage/blobs/storage-python-how-to-use-blob-storage.md) Egy tárolási szolgáltatás létrehozásához szüksége van egy nevet a szolgáltatás (között 3 és 24 kisbetűs karakterek és egyedi az Azure-ban). Szüksége van továbbá egy leírásra, egy címkére (legfeljebb 100 karakterre, automatikusan base64-be kódolva) és egy helyre. A következő példa bemutatja, hogyan hozhat létre tárolási szolgáltatást egy hely megadásával:

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

Az előző példában a **create\_\_storage account** művelet állapota lehívható átadásával az eredmény által **visszaadott\_létrehozása tárfiók\_** a get **\_művelet\_állapot** metódusát. 

A tárfiókokat és azok tulajdonságait a **listatárfiókok\_\_** módszerrel listázhatja.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="delete-a-storage-service"></a><a name="DeleteStorageService"> </a>Tárolási szolgáltatás törlése
Egy tárolási szolgáltatás törléséhez adja át a storage szolgáltatás nevét a **\_\_tárfiók törlése** módszer. A tárolószolgáltatás törlése törli a szolgáltatásban tárolt összes adatot (blobok, táblák és várólisták).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="list-available-operating-systems"></a><a name="ListOperatingSystems"> </a>Az elérhető operációs rendszerek listája
A tárhelyszolgáltatásokhoz rendelkezésre álló operációs rendszerek listázásához használja a **list\_operációs\_rendszerek** módszerét.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Másik lehetőségként használhatja az **operációs\_\_rendszer\_családok listájának** módszerét, amely az operációs rendszereket család szerint csoportosítja.

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="create-an-operating-system-image"></a><a name="CreateVMImage"> </a>Operációs rendszerlemez létrehozása
Ha operációsrendszer-lemezképet szeretne hozzáadni a lemezképtárhoz, használja az **os\_\_lemezkép hozzáadása** módszert.

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

Az elérhető operációsrendszer-lemezképek listázásához használja a **list\_os\_images** metódust. Ez magában foglalja az összes platform képek és felhasználói képek.

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

## <a name="delete-an-operating-system-image"></a><a name="DeleteVMImage"> </a>Operációs rendszerlemez törlése
Felhasználói lemezkép törléséhez használja az **operációs rendszer\_\_képének törlését.**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="create-a-virtual-machine"></a><a name="CreateVM"> </a>Virtuális gép létrehozása
Virtuális gép létrehozásához először létre kell hoznia egy [felhőszolgáltatást.](#CreateCloudService) Ezután hozza létre a virtuális gép központi telepítését a **virtuális\_\_gép\_telepítési módszerének létrehozásával.**

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
Virtuális gép törléséhez először törölje a központi telepítést a központi telepítési módszer **\_törlésével.**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

A felhőszolgáltatás ezután törölhető a **tárolt\_\_szolgáltatás törlése** metódus használatával.

    sms.delete_hosted_service(service_name='myvm')

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Virtuális gép létrehozása rögzített virtuálisgép-lemezképből
Virtuálisgép-lemezkép rögzítéséhez először hívja meg a **capture\_vm\_image** method.

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

Győződjön meg arról, hogy sikeresen rögzítette a lemezképet, használja a **list a\_vm\_images** API-t. Ellenőrizze, hogy a kép megjelenik-e az eredmények között.

    images = sms.list_vm_images()

A virtuális gép végleges létrehozásához a rögzített lemezkép használatával használja a **létrehozott\_virtuálisgép-telepítési\_\_** módszert a korábban, de ez az idő múlásával a vm_image_name helyett.

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

Ha többet szeretne megtudni arról, hogyan rögzíthet egy Linux-virtuális gépet a klasszikus telepítési modellben, olvassa el a Linux virtuális gép rögzítése című [témakört.](../virtual-machines/linux/classic/capture-image-classic.md)

Ha többet szeretne tudni arról, hogyan rögzíthet egy Windows-virtuális gépet a klasszikus telepítési modellben, olvassa el a Windows virtuális gép rögzítése című [témakört.](../virtual-machines/windows/classic/capture-image-classic.md)

## <a name="next-steps"></a><a name="What's Next"> </a>További lépések
Most, hogy megtanulta a szolgáltatáskezelés alapjait, hozzáférhet az [Azure Python SDK teljes API-referenciadokumentációjához,](https://azure-sdk-for-python.readthedocs.org/) és összetett feladatokat hajthat végre a Python-alkalmazás egyszerű kezeléséhez.

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
