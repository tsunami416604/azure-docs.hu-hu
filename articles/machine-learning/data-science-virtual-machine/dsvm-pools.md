---
title: Adatok tudományos virtuális gép készletek - Azure |} Microsoft Docs
description: A csoport megosztott erőforrásként készletek adatok tudományos virtuális gépek telepítése
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
ms.openlocfilehash: 0740ff7542d066442146b8e80e188ad5ba49a2b5
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309398"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Hozzon létre egy megosztott készletéhez az adatok tudományos virtuális gépek

A cikk ismerteti, hogyan hozhat létre egy megosztott készletéhez az adatok tudományos virtuális gépek (DSVMs) csoport használatára. Egy megosztott készletéhez használatának előnyei erőforrás-kihasználást, megosztási és az együttműködés elősegítése, sokkal hatékonyabb, erőforrások és kezelésére DSVM. 

Sok módszerek és technológiák hozhat létre DSVMs készletét. Ez a cikk foglalkozik a készletek kötegelt feldolgozásra és interaktív virtuális gépeket.

## <a name="batch-processing-pool"></a>Kötegfeldolgozási készlet
Ha azt szeretné, hozzon létre DSVMs készletét főként offline egy kötegelt feladatok futtatása, használhatja a [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) vagy [Azure Batch](https://docs.microsoft.com/azure/batch/) szolgáltatás. Ez a cikk az Azure Batch AI összpontosít.

A DSVM Ubuntu kiadása támogatott, egy Azure Batch AI a lemezképet. Az Azure parancssori felület vagy a Python SDK, ahol az Azure Batch AI fürtöt hoz létre, megadhatja a `image` paramétert, majd állítsa be `UbuntuDSVM`. Kiválaszthatja, hogy milyen típusú feldolgozó csomópontok kíván: GPU-alapú példányok csak CPU-példányt, processzort és memóriát száma és a [kiterjedő Virtuálisgép-példányok kiválasztása](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) érhető el az Azure-on. 

Használatakor a Ubuntu DSVM kép kötegelt AI csomópontjainak GPU-alapú, szükséges GPU illesztőprogramok és keretrendszerek tanulási mély előtelepítése mellett. Az előtelepítési menti, jelentős időt a kötegelt csomópontok előkészítése. Valójában Ha az egy Ubuntu DSVM meg interaktív módon, láthatja, hogy a kötegelt AI csomópontok pontosan azokat a azonos telepítő és a környezet konfigurációjától. 

Általában egy kötegelt AI fürt létrehozásakor is létrehozhat egy fájlmegosztást, amely az összes csomópontja csatlakoztatva van. A fájlmegosztás bemeneti és kimeneti adatok, valamint a kötegelt feladat kódot/parancsprogramok tárolásához használt szolgál. 

A kötegelt AI fürt létrehozása után használhatja a azonos CLI vagy Python SDK futtatható feladatok küldéséhez. Csak arra az időre, amellyel a kötegelt fizetnie. 

További információkért lásd:
* Részletes útmutató segítségével [Azure CLI](https://docs.microsoft.com/azure/batch-ai/quickstart-cli) kötegelt AI kezelése
* Részletes útmutató segítségével [Python](https://docs.microsoft.com/azure/batch-ai/quickstart-python) kötegelt AI kezelése
* [AI receptet köteg](https://github.com/Azure/BatchAI) , amelyek bemutatják, hogyan lehet használni a különböző AI és a kötegelt AI keretrendszerek tanulási mély

## <a name="interactive-vm-pool"></a>Interaktív Virtuálisgép-készlet

A teljes AI/adatok tudományos csapat által megosztott interaktív virtuális gépek készletét lehetővé teszi a felhasználóknak ahelyett, hogy az egyes felhasználók egy kijelölt példány DSVM elérhető példányt bejelentkezni. A telepítő segít jobb rendelkezésre állási és az erőforrások hatékonyabb használatát. 

A technológia, amely egy interaktív Virtuálisgép-készlet létrehozásához használt [Azure virtuálisgép-méretezési csoportok](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). Méretezési csoportok segítségével azonos, elosztott terhelésű csoportja és az automatikus skálázás virtuális gépek létrehozása és kezelése. 

A felhasználó bejelentkezik a fő készletébe IP- vagy DNS-címét. A méretezési automatikusan útvonalak a munkamenet számára a méretezési csoportban lévő egy elérhető DSVM. Mert a felhasználók függetlenül a virtuális gép hasonló környezetet azok most jelentkezik be, a virtuális Gépet, a méretezési csoportban lévő összes példánya csatlakoztatása egy megosztott hálózati meghajtó, például egy Azure fájlok megosztás vagy az NFS-megosztások. A felhasználó megosztott munkaterület általában az egyes példányok csatlakoztattak megosztott fájltároló másolatok. 

Egy minta Azure Resource Manager sablon által létrehozott terjedő skálán be Ubuntu DSVM osztályt található [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). A minta a [paraméterfájl](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) az Azure Resource Manager sablon van ugyanazon a helyen. 

A méretezési készletben az Azure Resource Manager sablon Azure CLI a paraméterfájl értékeinek megadásával hozhat létre. 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
A fenti parancsok során feltételezzük, hogy:
* A példány, a méretezési megadott értékek paraméter fájl másolatát.
* A Virtuálisgép-példányok száma.
* Ossza meg az Azure-fájlok mutató hivatkozások.
* Az egyes virtuális gépek is csatlakoztatva tárolási fiók hitelesítő adatait. 

A paraméterfájl helyileg a parancsok hivatkoznak. Paraméterek beágyazott vagy kérdés számukra a parancsfájlban is átadhatja.  

A fenti sablon lehetővé teszi, hogy az SSH és az előtér-méretezési készletben Ubuntu DSVMs háttér-készletéhez JupyterHub portjával. Felhasználóként most jelentkezik be a virtuális gép SSH vagy JupyterHub a szokásos módon. Mivel a Virtuálisgép-példányok akár is méretezhető, vagy le dinamikusan, bármely állapotban kell lennie menteni a csatlakoztatott Azure fájlokat megosztani. Ugyanezt a megközelítést hozhat létre Windows DSVMs készletét. 

A [parancsfájlt, amely csatlakoztatja Azure fájlok](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) is rendelkezésre áll, a github Azure DataScienceVM tárházban. A parancsfájl az Azure-fájlok megosztás: a paraméter fájl a megadott csatlakozási pont csatlakoztatja. A parancsfájl a csatlakoztatott meghajtóra mutató szoftveres hivatkozásokat is a kezdeti felhasználói kezdőkönyvtárban hoz létre. A felhasználó-specifikus notebook címtárhoz, az Azure fájlok enyhe csatolva a `$HOME/notebooks/remote` mappában, így a felhasználók elérhetik, futtatása és mentése a Jupyter notebookok. Az azonos egyezmény is használhatja, amikor további felhasználók hoz létre a virtuális gép Azure fájlok minden egyes felhasználó Jupyter munkaterület mutassanak. 

Virtuálisgép-méretezési támogatási automatikus skálázás beállítása. Mikor hozzon létre több példányt, és mikor érdemes méretezni le példányokat szabályainak beállítása. Például méretezhető nulla felhő használati hardverköltségek menti a virtuális gépek használata nem minden példányára. A virtuálisgép-méretezési csoportok dokumentációs oldalát ismerteti részletesen a [automatikus skálázás](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>További lépések

* [Közös identitás beállítása](dsvm-common-identity.md)
* [Biztonságosan tárolni a felhőben lévő erőforrások eléréséhez szükséges hitelesítő adatokat](dsvm-secure-access-keys.md)















