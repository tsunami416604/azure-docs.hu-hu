---
title: API verziója profilokkal a Python Azure verem |} Microsoft Docs
description: Ismerje meg az API verzió profilokkal a Python Azure-készletben.
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
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34806836"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Python Azure verem API-verzió profilok használata

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

## <a name="python-and-api-version-profiles"></a>Python és API-verzió profilok

A Python SDK API verziója profilok, amelyekre a felhő különböző platformokon, például Azure verem és a globális Azure támogatja. A hibrid felhő megoldások létrehozása API-profilok is használhatja. A Python SDK az alábbi API profilokat támogatja:

1. **legújabb**  
    A profil az Azure platform minden szolgáltatók számára a legújabb API verzióját célozza.
2.  **2017-03-09-profil**  
    **2017-03-09-profil**  
    A profil az erőforrás-szolgáltatók támogatott Azure verem API verzióit célozza.

    API-profilok és az Azure-verem kapcsolatos további információkért lásd: [kezelése API verziója profilok Azure verem](azure-stack-version-profiles.md).

## <a name="install-azure-python-sdk"></a>Az Azure Python SDK telepítése

1.  Telepítse a Git [a hivatalos webhely](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2.  További tájékoztatást a Python SDK telepítéséhez [Azure Python-fejlesztőknek](https://docs.microsoft.com/python/azure/python-sdk-azure-install?view=azure-python).
3.  Nem érhető el, ha előfizetés létrehozása és mentése a későbbi előfizetés-azonosító. Előfizetés létrehozása a útmutatásért lásd: [ajánlatok-előfizetések létrehozása az Azure-készletben](../azure-stack-subscribe-plan-provision-vm.md). 
4.  Hozzon létre egy egyszerű szolgáltatást, és mentse az Azonosítót és titkos kulcs. Hozzon létre egy egyszerű Azure verem utasításokért lásd: [alkalmazások elérést biztosíthat az Azure-verem](../azure-stack-create-service-principals.md). 
5.  Győződjön meg arról az egyszerű szolgáltatásnév rendelkezik közreműködői és a tulajdonosi szerepkört az előfizetés. A szerepkör hozzárendelése egyszerű útmutatásért lásd: [alkalmazások elérést biztosíthat az Azure-verem](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy az Azure-verem Python Azure SDK-t használja, adja meg a következő értékeket, és utána állítsa be a környezeti változók értékeit. Az utasításokat lásd a táblázat az operációs rendszerhez a környezeti változók beállítása után. 

| Érték | Környezeti változók | Leírás |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| Bérlőazonosító | AZURE_TENANT_ID | Az Azure-verem értékének [bérlői azonosító](../azure-stack-identity-overview.md). |
| Ügyfél-azonosító | AZURE_CLIENT_ID | A szolgáltatás egyszerű Alkalmazásazonosító mentett egyszerű szolgáltatás létrehozása az előző szakasz ebben a dokumentumban. |
| Előfizetés azonosítója | AZURE_SUBSCRIPTION_ID | A [előfizetés-azonosító](../azure-stack-plan-offer-quota-overview.md#subscriptions) miként férhetnek hozzá az ajánlatok van Azure-készletben. |
| Titkos ügyfélkulcs | AZURE_CLIENT_SECRET | A szolgáltatás egyszerű alkalmazás titkos kulcs mentésekor egyszerű szolgáltatásnév jött létre. |
| Erőforrás-kezelő végpont | ARM_ENDPOINT | Lásd: [az Azure verem resource manager-végpont](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |


## <a name="python-samples-for-azure-stack"></a>Azure verem Python-minták 

A következő kódminták segítségével közös felügyeleti feladatok végrehajtása a virtuális gépek az Azure-készletben.

A Kódminták megjelenítése, hogy:

- Virtuális gépek létrehozása:
    - Linuxos virtuális gépek létrehozása
    - Windowsos virtuális gép létrehozása
- A virtuális gép frissítése:
    - Bontsa ki a meghajtót
    - A virtuális gépek címke
    - Adatlemez csatolása
    - Adatlemezek leválasztása
- A virtuális gép működtetéséhez:
    - A virtuális gép elindítása
    - A virtuális gép leállítása
    - A virtuális gép újraindításához
- Virtuális gépek listája
- A virtuális gép törlése

Tekintse át a kódot, ezek a műveletek végrehajtásához, tekintse meg a **run_example()** függvényt a Python-parancsfájl **Hybrid/unmanaged-disks/example.py** a GitHub-tárházban [ virtuális-gépek-python-kezelése](https://github.com/viananth/virtual-machines-python-manage/tree/8643ed4bec62aae6fdb81518f68d835452872f88).

Egyes műveletek egyértelműen feliratú Megjegyzés és a nyomtatási funkció.
A példák nem feltétlenül a fenti listában szereplő sorrendben.


## <a name="run-the-python-sample"></a>A Python-minta futtatása

1.  Ha még nem telepítette, [Python telepítése](https://www.python.org/downloads/).

    Ez a minta (és az SDK-val) található Python 2.7, 3.4, 3.5-ös és 3.6 kompatibilis.

2.  A Python fejlesztői általános javasoljuk, hogy a virtuális környezetet használni. 
    További információkért lásd: https://docs.python.org/3/tutorial/venv.html
    
    Telepítse, és a virtuális környezet a Python 3 "venv" modul inicializálása (telepítenie kell a [virtualenv](https://pypi.python.org/pypi/virtualenv) Python 2.7):

    ````bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ````

3.  Klónozza a tárházat.

    ````bash
    git clone https://github.com/Azure-Samples/virtual-machines-python-manage.git
    ````

4.  A pip használatával függőségek telepítése.

    ````bash
    cd virtual-machines-python-manage\Hybrid
    pip install -r requirements.txt
    ````

5.  Hozzon létre egy [egyszerű](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) Azure verem használható. Győződjön meg arról, hogy a szolgáltatás egyszerű [közreműködő vagy tulajdonosa szerepkör](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal) előfizetéséhez.

6.  A következő változókat, és ezek a környezeti változók exportálása az aktuális rendszerhéjban. 

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