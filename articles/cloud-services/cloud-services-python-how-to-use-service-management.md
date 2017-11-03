---
title: "A szolgáltatáskezelő API (Python) - funkcióismertető használata"
description: "Megtudhatja, hogyan programozott módon hajtható végre az általános szolgáltatás-felügyeleti feladatokat a Python."
services: cloud-services
documentationcenter: python
author: lmazuel
manager: wpickett
editor: 
ms.assetid: 61538ec0-1536-4a7e-ae89-95967fe35d73
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/30/2017
ms.author: lmazuel
ms.openlocfilehash: 13249ba9a4b317a3154776b411ce0bb1f316b3bb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-service-management-from-python"></a>Kezelés a Python használata
Ez az útmutató bemutatja, hogyan programozott módon hajtható végre az általános szolgáltatás-felügyeleti feladatokat a Python. A **ServiceManagementService** osztályt a [Azure SDK for Python](https://github.com/Azure/azure-sdk-for-python) programozott hozzáférést a nagy részét a szolgáltatás-felügyelettel kapcsolatos funkció elérhető támogatja a [a klasszikus Azure portálon] [ management-portal] (például **létrehozása, frissítése és törlése a felhőszolgáltatások, központi telepítések, adatok szolgáltatások és virtuális gépek**). Ez a funkció akkor lehet hasznos, szolgáltatás-felügyelet programozott hozzáférést igénylő alkalmazások fejlesztése során.

## <a name="WhatIs"></a>Kezelő újdonságai
A Service Management API nagy részét a szolgáltatás felügyeleti funkció keresztül elérhető programozott hozzáférést biztosít a [a klasszikus Azure portálon][management-portal]. Az Azure SDK for Python teszi lehetővé a felhőszolgáltatások és a storage-fiókok kezeléséhez.

A szolgáltatásfelügyeleti API használatával kell [az Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"></a>Fogalmak
Az Azure SDK for Python becsomagolja a [Azure szolgáltatásfelügyeleti API][svc-mgmt-rest-api], vagyis a REST API-t. Az API műveletei SSL-kapcsolaton keresztül mennek végbe, és X.509 v3 tanúsítványok használatával kölcsönösen hitelesítettek. A felügyeleti szolgáltatás elérhető egy Azure-ban futó szolgáltatáson belülről, illetve az interneten keresztül közvetlenül is bármely olyan alkalmazásból, amely képes HTTPS-kéréseket küldeni és HTTPS-válaszokat fogadni.

## <a name="Installation"></a>Telepítése
Ebben a cikkben leírt összes funkcióját érhetők el a `azure-servicemanagement-legacy` csomagot, amely a pip használatával telepítheti. (Például, ha még nem ismeri a Python) telepítésével kapcsolatos további információkért lásd: Ez a cikk: [Python telepítése és az Azure SDK](../python-how-to-install.md)

## <a name="Connect"></a>Hogyan: szolgáltatásfelügyelet kapcsolódni
A Service Management végponthoz kapcsolódni, Azure-előfizetése Azonosítóját és egy érvényes felügyeleti tanúsítványt kell. Ezt úgy szerezheti be az előfizetés-Azonosítóval keresztül a [a klasszikus Azure portálon][management-portal].

> [!NOTE]
> Már lehetséges a Windows futtatásakor OpenSSL létre tanúsítványokat.  Python 2.7.4 igényel vagy újabb. Azt javasoljuk, hogy a felhasználók általi OpenSSL .pfx, helyett óta .pfx-tanúsítványok valószínűleg törlődni fog a későbbiekben támogatása.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Felügyeleti tanúsítványok Windows vagy Mac/Linux (OpenSSL)
Használhat [OpenSSL](http://www.openssl.org/) a felügyeleti tanúsítvány létrehozása.  Ténylegesen kell létrehoznia egy kiszolgáló két tanúsítványt (a `.cer` fájl) és egy, az ügyfél (egy `.pem` fájlt). Létrehozásához a `.pem` fájlt, hajtható végre:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Létrehozásához a `.cer` tanúsítvány, hajtható végre:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Az Azure-tanúsítványokkal kapcsolatos további információkért lásd: [tanúsítványok áttekintése Azure-szolgáltatásokhoz](cloud-services-certs-create.md). OpenSSL-paraméter teljes leírását lásd: a dokumentációban a [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

A létrehozást követően ezeket a fájlokat, szeretné-e feltölteni a `.cer` fájl az Azure-ba, a "Feltöltés" művelet "Beállítások" lapján keresztül a [a klasszikus Azure portálon][management-portal], és jegyezze fel a mentési helyét kell a `.pem` fájl.

Ha beszerezte az előfizetés-Azonosítóval, létrehozott egy tanúsítványt, és fel kell tölteni a `.cer` fájl az Azure-ba, csatlakozhat az Azure felügyeleti végpont úgy, hogy az előfizetés-azonosító és elérési útját a `.pem` fájl **ServiceManagementService**:

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

Az előző példában `sms` van egy **ServiceManagementService** objektum. A **ServiceManagementService** osztály az Azure-szolgáltatások kezelésére szolgáló elsődleges osztály.

### <a name="management-certificates-on-windows-makecert"></a>A Windows (MakeCert) felügyeleti tanúsítványok
Létrehozhat egy önaláírt felügyeleti tanúsítvány a gép használt `makecert.exe`.  Nyissa meg a **Visual Studio parancssorból** , egy **rendszergazda** és használja a következő parancsot, cseréje *AzureCertificate* használni szeretné a tanúsítványt nevű.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

A parancs létrehozza a `.cer` fájlt, és telepíti azt a a **személyes** tanúsítványtárolójába. További információkért lásd: [tanúsítványok áttekintése Azure-szolgáltatásokhoz](cloud-services-certs-create.md).

Miután létrehozta a tanúsítványt, szeretné-e feltölteni a `.cer` fájl az Azure-ba, a "Feltöltés" művelet "Beállítások" lapján keresztül a [a klasszikus Azure portálon][management-portal].

Ha beszerezte az előfizetés-Azonosítóval, létrehozott egy tanúsítványt, és fel kell tölteni a `.cer` fájl az Azure-ba, csatlakozhat az Azure felügyeleti végpont úgy, hogy az előfizetés-azonosító és a tanúsítvány helye a **személyes** tanúsítványtárolójának **ServiceManagementService** (újra, cserélje le *AzureCertificate* a tanúsítvány neve):

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

Az előző példában `sms` van egy **ServiceManagementService** objektum. A **ServiceManagementService** osztály az Azure-szolgáltatások kezelésére szolgáló elsődleges osztály.

## <a name="ListAvailableLocations"></a>Hogyan: elérhető helyről felsorolása
A helyek elérhető szolgáltatásait üzemeltető kilistázhatja a **lista\_helyek** metódus:

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

## <a name="CreateCloudService"></a>Hogyan: felhőalapú szolgáltatás létrehozása
Amikor létrehoz egy alkalmazást, és futtassa az Azure-ban, a kódot és konfigurációs együtt nevezzük az Azure [felhőalapú szolgáltatás] [ cloud service] (néven egy *üzemeltetett szolgáltatás* korábbi Azure-ban kiadások). A **létrehozása\_üzemeltetett\_szolgáltatás** metódus hozhat létre egy új kezelt szolgáltatást a futtatott szolgáltatás nevét (amelyen az Azure-ban egyedinek kell lennie), (automatikusan Base64 kódolású) címke, a leírás megadásával és helyét.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Előfizetés az összes üzemeltetett szolgáltatás listázhatja a **lista\_üzemeltetett\_szolgáltatások** módszert:

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Ha le szeretné kérdezni egy adott tárolt szolgáltatás adatainak, akkor megteheti úgy, hogy a futtatott szolgáltatás nevét, hogy a **beolvasása\_üzemeltetett\_szolgáltatás\_tulajdonságok** módszert:

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Miután létrehozott egy felhőalapú szolgáltatás, telepítheti a kódot a szolgáltatást, amely a **létrehozása\_telepítési** metódus.

## <a name="DeleteCloudService"></a>Hogyan: egy felhőalapú szolgáltatás törlése
Egy felhőalapú szolgáltatás törölheti úgy, hogy a szolgáltatás nevét a **törlése\_üzemeltetett\_szolgáltatás** módszert:

    sms.delete_hosted_service('myhostedservice')

A szolgáltatás törlése előtt a szolgáltatás központi telepítéseinek először törölni kell. (Lásd: [hogyan: törölje a központi telepítést](#DeleteDeployment) részletes.)

## <a name="DeleteDeployment"></a>Útmutató: a központi telepítés törlése
A központi telepítés törléséhez használja a **törlése\_telepítési** metódust. A következő példa bemutatja, hogyan törölhető egy központi telepítést `v1`.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"></a>Hogyan: storage szolgáltatás létrehozása
A [társzolgáltatás](../storage/common/storage-create-storage-account.md) biztosít hozzáférést az Azure-bA [Blobok](../storage/blobs/storage-python-how-to-use-blob-storage.md), [táblák](../cosmos-db/table-storage-how-to-use-python.md), és [várólisták](../storage/queues/storage-python-how-to-use-queue-storage.md). Hozzon létre egy társzolgáltatás, kell a szolgáltatás (kisbetűket 3 és 24 közötti és egyedi Azure-ban), leírását, egy címke (legfeljebb 100 karakter, automatikusan a Base64 kódolású) és egy hely nevét. A következő példa bemutatja, hogyan tárolási szolgáltatás létrehozása a hely megadásával.

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

Megjegyzés: az előző példában, amely állapotának a **létrehozása\_tárolási\_fiók** művelet által visszaadott eredmény átadásával kérhető **létrehozása\_tárolási\_fiók** számára a **beolvasása\_művelet\_állapot** metódus.  

A storage-fiókok és az azok tulajdonságait listázhatja a **lista\_tárolási\_fiókok** metódus:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"></a>Útmutató: a társzolgáltatás törlése
Egy tároló szolgáltatást úgy, hogy a szolgáltatás nevét a törölheti a **törlése\_tárolási\_fiók** metódus. A storage szolgáltatás törlése (blobot, táblát és üzenetsort) szolgáltatásban tárolt összes adat törlése.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"></a>Hogyan: listából a rendelkezésre álló operációs rendszeren
Üzemeltetési szolgáltatásokat rendelkezésre álló operációs rendszerek listáját, használja a **lista\_működő\_rendszerek** módszer:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Másik lehetőségként használhatja a **lista\_működő\_rendszer\_családok** metódus, amely csoportosítja a termékcsalád operációs rendszerek:

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"></a>Hogyan: hozzon létre egy operációsrendszer-lemezképet
A lemezképtár adhat hozzá az operációs rendszer lemezképét, a **hozzáadása\_os\_kép** módszer:

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

Kilistázhatja az elérhető rendszerképek az **lista\_os\_képek** metódus. Összes platform képek és felhasználói képeket tartalmaz:

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

## <a name="DeleteVMImage"></a>Útmutató: az operációsrendszer-lemezkép törlése
Egy felhasználói lemezkép törléséhez használja a **törlése\_os\_kép** módszert:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"></a>Útmutató: virtuális gép létrehozása
A virtuális gép létrehozásához először hozzon létre egy [felhőalapú szolgáltatás](#CreateCloudService).  Majd létre szeretne hozni a virtuális gép telepítési használja a **létrehozása\_virtuális\_gép\_telepítési** módszer:

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

## <a name="DeleteVM"></a>Útmutató: virtuális gép törlése
A virtuális gép törlése, előbb törölnie a központi telepítés használatával a **törlése\_telepítési** módszer:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

A felhőszolgáltatás használja majd törölhető a **törlése\_üzemeltetett\_szolgáltatás** módszert:

    sms.delete_hosted_service(service_name='myvm')

## <a name="how-to-create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>A virtuális gép létrehozása a rögzített virtuálisgép-lemezkép
A virtuális gép lemezképének, akkor először hívja meg a **rögzítése\_vm\_kép** módszert:

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

Ezt követően győződjön meg arról, hogy sikeresen elkészült a kép, használja a **lista\_vm\_képek** API-t, és győződjön meg arról, hogy a kép megjelenik az eredmények között:

    images = sms.list_vm_images()

Végül hozhat létre a virtuális gépet a rögzített lemezkép használatával, a **létrehozása\_virtuális\_gép\_telepítési** előtt, de ezúttal adjon át a vm_image_name helyette módszer

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

Linux virtuális gép rögzítése kapcsolatban további tudnivalókért lásd: [egy Linux virtuális gép rögzítése.](../virtual-machines/linux/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

A Windows rendszerű virtuális gép rögzítése kapcsolatos további információkért lásd: [egy Windows virtuális gép rögzítése.](../virtual-machines/windows/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="What's Next"></a>Következő lépések
Most, hogy megismerte a service management alapjait, hogy elérhető a [teljes API referenciadokumentációt tartalmaz az Azure Python SDK](http://azure-sdk-for-python.readthedocs.org/) , és végezze el az összetett feladatok könnyedén kezelheti a python alkalmazást.

További információ: [Python fejlesztői központban](/develop/python/).

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


[cloud service]:/services/cloud-services/
