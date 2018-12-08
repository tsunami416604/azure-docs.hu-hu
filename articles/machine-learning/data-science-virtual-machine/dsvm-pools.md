---
title: Data Science virtuális gép készletek – Azure |} A Microsoft Docs
description: Adatelemzési virtuális gépek, a csapat számára a megosztott erőforrások üzembe helyezése készleteinek
keywords: deep learning, AI, beépített adatelemzési eszközzel, az adatelemzési virtuális gépet, a térinformatikai elemzés, a csoportos adatelemzési folyamat
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: acae59922f5a46f059e19db6865491f5186139f7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103404"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Az adatelemző virtuális gépek megosztott készlet létrehozása

Ez a cikk ismerteti, hogyan hozhat létre egy megosztott készletet az adatelemző virtuális gépek (Dsvm) egy csapat használatára. Közös használatának előnyei jobb erőforrás-használatot, megosztása és együttműködés megkönnyítése és hatékonyabb DSVM-erőforrások kezelését. 

Számos módszer és technológiák használatával Dsvm készletét hozza létre. Ez a cikk a kötegelt feldolgozásra és interaktív virtuális gépek készletein foglalkozik.

## <a name="batch-processing-pool"></a>Kötegelt feldolgozási készlet
Ha szeretne beállítása Dsvm készletét, főleg offline kötegelt feladatok végrehajtásához, használhatja a [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) vagy [Azure Batch](https://docs.microsoft.com/azure/batch/) szolgáltatás. Ez a cikk az Azure Batch AI összpontosít.

Az Azure Batch AI-rendszerképeket is támogatott, az Ubuntu kiadása a dsvm-hez. Az Azure CLI vagy a Python SDK-t, ahol az Azure Batch AI-fürtöt hoz létre, megadhatja a `image` paraméter, illetve beállíthatja `UbuntuDSVM`. Kiválaszthatja, hogy milyen típusú feldolgozó csomópontok kívánt: GPU-alapú példányok csak CPU-példányok, a processzorokat és memóriát száma és a egy [számos választási lehetőség a Virtuálisgép-példányok](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) elérhető az Azure-ban. 

Használatakor a Ubuntu DSVM-rendszerkép a Batch AI GPU-alapú csomópont összes szükséges GPU-illesztők és deep learning-keretrendszerek előre telepítve. Az előtelepítési menti, jelentős időt a batch-csomópontok. Sőt Ha fejleszt egy Ubuntu DSVM az interaktív módon, láthatja, hogy a Batch AI csomópontok pontosan a azonos beállítás és konfiguráció a környezet. 

Általában amikor létrehoz egy Batch AI-fürtöt, is létrehozhat egy fájlmegosztást, amely szerint az összes csomópont csatlakoztatva van. A fájlmegosztás a bemeneti és kimeneti adatok, valamint a batch-feladat-kód/parancsprogramok tárolásához használatos. 

Miután létrehozott egy Batch AI-fürtöt, parancssori felület vagy a Python SDK használatával futtatandó feladatok elküldéséhez. Csak a kötegelt feladatok végrehajtásához használt időért kell fizetnie. 

További információkért lásd:
* Részletes útmutató segítségével [Azure CLI-vel](https://docs.microsoft.com/azure/batch-ai/quickstart-cli) kezelése a Batch AI
* Részletes útmutató segítségével [Python](https://docs.microsoft.com/azure/batch-ai/quickstart-python) kezelése a Batch AI
* [Batch AI-receptek](https://github.com/Azure/BatchAI) , amelyek bemutatják, hogyan használható a mesterséges Intelligencia és a deep learning-keretrendszerek, a Batch AI különböző

## <a name="interactive-vm-pool"></a>Interaktív VM-készletet

Egy interaktív virtuális gépek, az egész mesterséges Intelligencia/data science csapat által megosztott készletét lehetővé teszi a felhasználóknak jelentkezzen be a dsvm-hez dedikált-példányt az egyes felhasználók nem érhető el példányát. A telepítő segít a jobb rendelkezésre állás és az erőforrások hatékonyabb kihasználását. 

A technológia, amely létrehoz egy interaktív virtuális gép készlet [Azure-beli virtuálisgép-méretezési csoportok](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). A méretezési csoportok segítségével azonos, elosztott terhelésű egy csoportját, és automatikus méretezést végző virtuális gépek létrehozása és kezelése. 

A felhasználó bejelentkezik a fő készlet IP- vagy DNS-cím. A méretezési csoport automatikusan útvonalakat a munkamenet a rendelkezésre álló dsvm-hez a méretezési csoportban. Mivel a felhasználók meg szeretnék hasonló környezetet, függetlenül a virtuális gép, már bejelentkezett, a virtuális Gépet, a méretezési csoportban lévő összes példánya, például egy Azure-fájlmegosztással vagy egy NFS-megosztás egy megosztott hálózati meghajtó csatlakoztatása. A felhasználó megosztott munkaterületen általában tartani, az egyes példányok csatlakoztatott megosztott fájlrendszert Store-ból. 

Egy minta Azure Resource Manager-sablon, amely létrehoz egy méretezési csoportot az Ubuntu DSVM-példányok a annak [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). A minta a [paraméterfájl](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) az Azure Resource Manager sablon van ugyanazon a helyen. 

A méretezési csoportot az Azure Resource Manager-sablon Azure CLI-ben a paraméterfájl értékeinek megadásával hozhat létre. 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
A fenti parancsok feltételezik, hogy rendelkezik:
* A példány a méretezési megadott értékekkel alkalmazásparaméter-fájlt egy példányát.
* A Virtuálisgép-példányok száma.
* Ossza meg az Azure Files mutatókat tartalmaznak.
* Hitelesítő adatok a storage-fiók, amely lesz csatlakoztatva az egyes virtuális Gépeken. 

A paraméterfájl helyileg hivatkozik a parancsokat. Is átadhat paraméterek a beágyazott vagy írja be őket a szkriptben.  

Az előző sablon lehetővé teszi, hogy az SSH és a JupyterHub portot az előtérbeli méretezési csoportból, a háttérkészlet Ubuntus Dsvm. Felhasználóként akkor csak jelentkezzen be a virtuális géphez SSH vagy JupyterHub a szokásos módon. Mivel a Virtuálisgép-példányok méretezhetők vagy lefelé dinamikusan, minden olyan állapotban kell lennie a csatlakoztatott mentve az Azure Files osztozik. Ugyanezzel a módszerrel használhatja a Dsvm Windows-készlet létrehozása. 

A [parancsfájlt, amely csatlakoztatja az Azure-fájlmegosztási](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) érhető el az Azure DataScienceVM tárházban a Githubon. A parancsfájl csatlakoztatja az Azure-fájlmegosztással a paraméterfájlban megadott csatlakoztatási ponton. A parancsfájl a kezdeti felhasználó kezdőkönyvtárának is létrehoz a csatlakoztatott meghajtó mutató szoftveres hivatkozásokat. Egy felhasználó-specifikus notebook címtárhoz az Azure-fájlmegosztást az enyhe csatolva a `$HOME/notebooks/remote` , hogy a felhasználók eléréséhez, futtatása és mentése a Jupyter notebookok könyvtárat. Használhatja ugyanazt az konvenciót további felhasználók minden egyes felhasználó Jupyter munkaterületét átirányítása az Azure-fájlmegosztást a virtuális gép létrehozásakor. 

Virtuálisgép-méretezési csoport beállítása az automatikus skálázás támogatása. Mikor hozzon létre további példányokat, és mikor kell példányokat leskálázás beállíthatja a szabályokat. Ha például vertikális nulla-példányok menteni a felhőbeli hardver adathasználati költségek, ha a virtuális gépek egyáltalán nem használja. A virtual machine scale sets dokumentációja lapok adja meg a részletes lépései [az automatikus skálázás](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>További lépések

* [Közös identitás beállítása](dsvm-common-identity.md)
* [A felhőbeli erőforrások eléréséhez szükséges hitelesítő adatok biztonságos tárolása](dsvm-secure-access-keys.md)















