---
title: Az Azure Stackben Python API-verzióprofilok használata |} A Microsoft Docs
description: Ismerje meg az Azure Stackben Python API-verzióprofilok használatával.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: d17ba9ed4548a986d6846d934aee197609ec80ca
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "34806836"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>API-verzióprofilok használata az Azure Stackben Pythonnal

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

## <a name="python-and-api-version-profiles"></a>Python- és API-verzióprofilok

A Python SDK API-verzióprofilok célozhat meg például az Azure Stack és a globális Azure más felhőalapú platformot támogatja. Használhat API-t egy hibrid felhőalapú megoldások létrehozásához. A Python SDK támogatja a következő API-profilokat:

1. **legfrissebb**  
    A profil a legújabb API-verziók célozza meg benne az Azure platform minden szolgáltató számára.
2.  **2017-03-09-profile**  
    **2017-03-09-profile**  
    A profil célozza meg benne az Azure Stack által támogatott erőforrás-szolgáltatók API verzióját.

    API-profilok és az Azure Stack kapcsolatos további információkért lásd: [kezelése API-verzióprofilok az Azure Stackben](azure-stack-version-profiles.md).

## <a name="install-azure-python-sdk"></a>Az Azure Python SDK telepítése

1.  A Git telepítése [a hivatalos hely](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2.  Telepítse a Python SDK-t útmutatásért lásd: [Python-fejlesztőknek az Azure](https://docs.microsoft.com/python/azure/python-sdk-azure-install?view=azure-python).
3.  Ha nem érhető el, hozzon létre egy előfizetést és az előfizetés-azonosító későbbi felhasználás céljából. Hozzon létre egy előfizetést az utasításokért lásd: [ajánlatok, előfizetések létrehozása az Azure Stackben](../azure-stack-subscribe-plan-provision-vm.md). 
4.  Hozzon létre egy egyszerű szolgáltatást, és mentse a azonosítója és kulcsa. Az Azure stack-beli szolgáltatásnév létrehozása az utasításokért lásd: [alkalmazások elérést biztosíthat az Azure Stack](../azure-stack-create-service-principals.md). 
5.  Ellenőrizze, hogy az egyszerű szolgáltatás rendelkezik közreműködői vagy tulajdonosi szerepkört az előfizetésben. Szerepkör hozzárendelése egyszerű szolgáltatást, lásd: [alkalmazások elérést biztosíthat az Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Előfeltételek

Annak érdekében, hogy a Python az Azure SDK használata az Azure Stack használatával, adja meg a következő értékeket, és adja meg az értékeket a környezeti változókat. Az utasításokat lásd a táblázat az operációs rendszerének a a környezeti változók beállítása után. 

| Érték | Környezeti változók | Leírás |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| Bérlőazonosító | AZURE_TENANT_ID | Az Azure Stack értékét [bérlőazonosító](../azure-stack-identity-overview.md). |
| Ügyfél-azonosító | AZURE_CLIENT_ID | A szolgáltatás egyszerű alkalmazást azonosító mentése az előző szakaszban Ez a dokumentum az egyszerű szolgáltatás létrehozásakor. |
| Előfizetés azonosítója | AZURE_SUBSCRIPTION_ID | A [előfizetés-azonosító](../azure-stack-plan-offer-quota-overview.md#subscriptions) van, hogy miként férhetnek hozzá az ajánlatok az Azure Stackben. |
| Titkos ügyfélkulcs | AZURE_CLIENT_SECRET | A szolgáltatás egyszerű alkalmazás titkos kulcs mentése egyszerű szolgáltatás létrehozásakor. |
| Resource Manager-végpont | ARM_ENDPOINT | Lásd: [az Azure Stack resource manager-végpont](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |


## <a name="python-samples-for-azure-stack"></a>Python-minták az Azure Stackben 

A következő kódminták segítségével gyakori felügyeleti feladatok végrehajtása a virtuális gépek az Azure stack.

A Kódminták megjelenítése, hogy:

- Virtuális gépek létrehozása:
    - Linuxos virtuális gépek létrehozása
    - Windowsos virtuális gép létrehozása
- A virtuális gép frissítése:
    - Bontsa ki a meghajtó
    - Virtuális gép címkézése
    - Adatlemez csatolása
    - Adatlemezek leválasztása
- Virtuális gép működtetéséhez:
    - A virtuális gép elindítása
    - Virtuális gép leállítása
    - Indítsa újra a virtuális gép
- Virtuális gépek listája
- A virtuális gép törlése

Tekintse át a kódot, ezek a műveletek végrehajtásához, tekintse át a **run_example()** függvény a Python-szkriptet a **Hybrid/unmanaged-disks/example.py** a GitHub-adattárat a [ virtuális gépek – python-kezelhető](https://github.com/viananth/virtual-machines-python-manage/tree/8643ed4bec62aae6fdb81518f68d835452872f88).

Minden művelet egyértelműen célállapotba megjegyzést, és a egy nyomtatási funkció.
A példák nem feltétlenül a a fenti listában megadott sorrendben.


## <a name="run-the-python-sample"></a>A Python-minta futtatása

1.  Ha még nem rendelkezik, [telepítik a Pythont](https://www.python.org/downloads/).

    Ez a minta (és az SDK-t) a rendszer kompatibilis a Python 2.7-es, 3.4-es, 3.5-ös és 3.6-os.

2.  A Python-fejlesztés általános javaslat, hogy egy virtuális környezethez. 
    További információkért lásd: https://docs.python.org/3/tutorial/venv.html
    
    Telepítse, és a virtuális környezet a Python 3 "venv" modul inicializálása (telepítenie kell a [virtualenv](https://pypi.python.org/pypi/virtualenv) Python 2.7):

    ````bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ````

3.  A tárház klónozásához.

    ````bash
    git clone https://github.com/Azure-Samples/virtual-machines-python-manage.git
    ````

4.  A pip használatával függőségek telepítéséhez.

    ````bash
    cd virtual-machines-python-manage\Hybrid
    pip install -r requirements.txt
    ````

5.  Hozzon létre egy [szolgáltatásnév](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) működjön az Azure Stack használatával. Győződjön meg arról, hogy rendelkezik az egyszerű szolgáltatás [közreműködői vagy tulajdonosi szerepkör](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal) az előfizetésén.

6.  Állítsa be a következő változókat, és ezeket a környezeti változókat az aktuális parancshéjban exportálása. 

    ````bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  In order to run this sample, Ubuntu 16.04-LTS and WindowsServer 2012-R2-Datacenter images must be present in Azure Stack market place. These can be either [downloaded from Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item) or [added to Platform Image Repository](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image).

8. Run the sample.

    ```
    python unmanaged-disks\example.py
    ```

## Notes

You may be tempted to try to retrieve a VM's OS disk by using
`virtual_machine.storage_profile.os_disk`.
In some cases, this may do what you want,
but be aware that it gives you an `OSDisk` object.
In order to update the OS Disk's size, as `example.py` does,
you need not an `OSDisk` object but a `Disk` object.
`example.py` gets the `Disk` object with the following:

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## Next steps

- [Azure Python Development Center](https://azure.microsoft.com/develop/python/)
- [Azure Virtual Machines documentation](https://azure.microsoft.com/services/virtual-machines/)
- [Learning Path for Virtual Machines](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)
- If you don't have a Microsoft Azure subscription, you can get a FREE trial account [here](http://go.microsoft.com/fwlink/?LinkId=330212).