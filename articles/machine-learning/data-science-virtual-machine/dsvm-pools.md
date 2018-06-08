---
title: Adatok tudományos virtuális gép készletek - Azure |} Microsoft Docs
description: A csoport megosztott erőforrásként készletek adatok tudományos virtuális gép telepítése
keywords: a mélyhivatkozással tanulási, AI adatok tudományos eszközök, a adatok tudományos virtuális gép, a földrajzi analytics, a csapat az tudományos folyamata
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: c7aab0435ecbd0aee57a15008ac0270159ec2eb3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837082"
---
# <a name="creating-a-shared-pool-of-data-science-virtual-machines"></a>Egy megosztott készletéhez az adatok tudományos virtuális gépek létrehozása

A cikk ismerteti, hogyan egy megosztott készletéhez adatok tudományos virtuális gépek (DSVM) hozhat létre használatra a csoport. Egy megosztott készletéhez használatának előnye erőforrás-kihasználást, megosztási és az együttműködés elősegítése, és lehetővé teszi az informatikai részleg a DSVM erőforrások hatékonyabban felügyelheti. 

Számos módon és DSVM készletét létrehozásához használható különböző technológiák vannak.  Főbb forgatókönyvek a következők:

* A kötegelt feldolgozáshoz készlet
* Interaktív virtuális gépek a tárolókészletben

## <a name="batch-processing-pool"></a>Kötegfeldolgozási készlet
Ha azt szeretné, hozzon létre DSVM készletét főként offline egy kötegelt feladatok futtatása, akkor is használhatja [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) szolgáltatás vagy [Azure Batch](https://docs.microsoft.com/azure/batch/). 

### <a name="azure-batch-ai"></a>Azure Batch AI
A DSVM Ubuntu kiadása támogatott, egy Azure Batch AI a lemezképet. Az Azure parancssori felület vagy a Python SDK, ahol az Azure Batch AI fürt létrehozása, megadhatja a ```image``` paramétert, majd állítsa be ```UbuntuDSVM```. Választhat, hogy milyen típusú feldolgozó csomópontok kíván – GPU-alapú példányok vs CPU csak példányok, a memória, a processzorok számának a [kiterjedő Virtuálisgép-példányok kiválasztása](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) érhető el az Azure-on. Ha az Ubuntu DSVM lemezkép használata a GPU-alapú csomópont révén az összes szükséges GPU-illesztőprogramokat kötegelt AI, és részletes tanulási keretrendszerek előtelepített így meg lehet spórolni jelentős időt a kötegelt csomópontok előkészítésében. Valójában fejlesztői egy Ubuntu DSVM meg interaktív módon, megfigyelheti, hogy a kötegelt AI csomópontok pontosan azokat a azonos telepítő és a környezet konfigurációjától. Általában egy kötegelt AI fürt létrehozásakor is létrehozhat egy fájlmegosztást, amelyet csatlakoztatva van a csomópont által használt bemeneti és kimeneti adatok, valamint a kötegelt feladat kód tárolása / parancsfájlok. 

A kötegelt AI fürt létrehozása után CLI vagy a Python SDK segítségével futtatható feladatok elküldéséhez. Csak kell fizetnie, amellyel a kötegelt alkalommal. 

#### <a name="more-information"></a>További információ
* Részletes útmutató segítségével [Azure CLI](https://docs.microsoft.com/azure/batch-ai/quickstart-cli) kötegelt AI kezeléséhez
* Részletes útmutató segítségével [Python](https://docs.microsoft.com/azure/batch-ai/quickstart-python) kötegelt AI kezeléséhez
* [AI receptet köteg](https://github.com/Azure/BatchAI) elérhető különböző AI/mély tanulási keretrendszerek használata kötegelt AI, amely tartalmazza.

## <a name="interactive-vm-pool"></a>Interaktív Virtuálisgép-készlet

A teljes AI által megosztott interaktív DSVMs készletét / adatok tudományos team lehetővé teszi a felhasználóknak ahelyett, hogy az egyes felhasználók egy kijelölt példány DSVM elérhető példányt bejelentkezni. Ezzel a megoldással rendelkező jobb rendelkezésre állást és az erőforrások hatékonyabb használatát. 

A technológia használatával hozzon létre egy interaktív VM-címkészletet a [Azure virtuálisgép-méretezési csoportok](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) (VMSS), amely lehetővé teszi a csoport az azonos, elosztott terhelésű és automatikus skálázás virtuális gépek létrehozása és kezelése. A felhasználó bejelentkezik a fő készletébe IP- vagy DNS-címét. A skála értéke automatikusan útvonalak a munkamenet egy elérhető DSVM a méretezési csoportban. Szeretné, hogy a felhasználó függetlenül a virtuális gép hasonló környezet jelentkezik be, mert a virtuális Gépet, a méretezési csoportban lévő összes példánya csatlakoztat egy megosztott hálózati meghajtóra, például egy Azure-fájlok vagy az NFS-megosztások. A felhasználó megosztott munkaterület általában az egyes példányok csatlakoztattak megosztott fájltároló másolatok. 

Hozza létre a Virtuálisgép-méretezési készlet Ubuntu DSVMs osztályt minta Azure Resource Manager sablon megtalálható a [github](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Az Azure Resource Manager sablon minta [paraméterfájl](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) is megtalálható ugyanazon a helyen. 

Az Azure Resource Manager-sablon Azure CLI-vel paraméterfájl megfelelő értékeinek megadásával állítható be a Virtuálisgép-méretezési hozhat létre. 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
A fenti parancsok során feltételezzük, hogy a paraméter fájl egy példányát a Virtuálisgép-méretezési készlet, a Virtuálisgép-példányok száma, a mutatók a Azure fájlok az egyes virtuális gépek is csatlakoztatva tárolási fiók hitelesítő adatokkal együtt a példányának megadott értékekkel. A paraméterfájl helyileg a fenti parancs hivatkoznak. Átadhatók is paraméterek beágyazott vagy kérdés számukra a parancsfájlban.  

A fenti sablon lehetővé teszi, hogy az SSH és a Virtuálisgép-méretezési előtér Jupyterhub portjával Ubuntu DSVMs háttérkészlet értékre.  Egy olyan felhasználó nevében csak jelentkezik be a virtuális gép SSH vagy JupyterHub a szokásos módon. Mivel a Virtuálisgép-példányok akár is méretezhető, vagy le dinamikusan, bármely állapotban kell lennie a csatlakoztatott mentett Azure fájlok megosztása. Ugyanezt a megközelítést Windows DSVMs készletét létrehozására használható. 

A [parancsfájlt, amely csatlakoztatja a Azure fájlok](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) az Azure DataScienceVM githubon érhetők el. Mellett a megadott csatlakozási pont a paraméter fájlban, az Azure-fájlok csatlakoztatására, is hozna létre a csatlakoztatott meghajtóra további szoftveres hivatkozásokat a kezdeti felhasználó saját könyvtárához, és egy felhasználóspecifikus notebook címtárhoz, a megosztott Azure fájlokat az ideiglenes a csatolt ```$HOME/notebooks/remote``` directory elérésére, futtatása és mentése a Jupyter notebookok felhasználói engedélyezése.  Az azonos egyezmény a virtuális gépre, mutasson a minden felhasználó Jupyter munkaterület a megosztott Azure fájlok további felhasználók létrehozásakor használható. 

Az Azure Virtuálisgép-méretezési beállítja támogatási automatikus skálázás beállítására szabályok létrehozásának további példányait, és milyen körülmények között beleértve a kerülne le kívánja, mentheti a nulla példányok példányok csökkentheti a felhő hardver használati költségek a virtuális gépek használata nem minden alatt . A Virtuálisgép-méretezési készlet dokumentációs oldalát ismerteti részletesen a [automatikus skálázás](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>További lépések

* [Közös identitás beállítása](dsvm-common-identity.md)
* [Biztonságosan tárolni a felhőben lévő erőforrások eléréséhez szükséges hitelesítő adatokat](dsvm-secure-access-keys.md)















