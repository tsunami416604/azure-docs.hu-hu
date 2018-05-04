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
ms.date: 04/30/2018
ms.author: mabrigg
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: a4fe62ba8c0732745326831b977e8975e1210436
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2018
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Python Azure verem API-verzió profilok használata

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

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

5.  Hozzon létre egy [egyszerű](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals) Azure verem használható. Győződjön meg arról, hogy a szolgáltatás egyszerű [közreműködő vagy tulajdonosa szerepkör](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal) előfizetéséhez.

6.  A következő változókat, és ezek a környezeti változók exportálása az aktuális rendszerhéjban. 

`Note: provide an explanation of where these variables come from?`

    ````bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  Vegye figyelembe, hogy ez a minta futtatásához Ubuntu 16.04-es lts verzió és WindowsServer 2012-R2 – Datacenter képek jelen kell lennie a verem Azure piactéren. Ezek lehetnek vagy [az Azure-ból letöltött](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-download-azure-marketplace-item) vagy [Platform lemezképtárba hozzáadott](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-add-vm-image).


8. Futtassa a mintát.

    ```
    python unmanaged-disks\example.py
    ```

## <a name="notes"></a>Megjegyzések

Előfordulhat, hogy a használatával egy virtuális gép operációsrendszer-lemez lekéréséhez kísértésbe `virtual_machine.storage_profile.os_disk`.
Néhány esetben ez lehetséges, hogy elindítja mi, de vegye figyelembe, hogy biztosít egy `OSDisk` objektum.
Az operációsrendszer-lemez mérete, mint frissítéséhez `example.py` nem, akkor nem kell egy `OSDisk` objektum, de egy `Disk` objektum.
`example.py` lekérdezi a `Disk` objektumot a következőre:

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## <a name="next-steps"></a>További lépések

- [Azure Python fejlesztői központ](https://azure.microsoft.com/develop/python/)
- [Az Azure Virtual Machines – dokumentáció](https://azure.microsoft.com/services/virtual-machines/)
- [A virtuális gépek képzési terv](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)
- Ha a Microsoft Azure-előfizetés nem rendelkezik egy ingyenes próbafiók kaphat [Itt](http://go.microsoft.com/fwlink/?LinkId=330212).
