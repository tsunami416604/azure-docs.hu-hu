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
ms.date: 12/10/2018
ms.author: gokuma
ms.openlocfilehash: 648b0818f07aca8763fa4af01380076ae307b0a2
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408952"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Az adatelemző virtuális gépek megosztott készlet létrehozása

Ez a cikk ismerteti, hogyan hozhat létre egy megosztott készletet az adatelemző virtuális gépek (Dsvm) egy csapat használatára. Közös használatának előnyei jobb erőforrás-használatot, megosztása és együttműködés megkönnyítése és hatékonyabb DSVM-erőforrások kezelését. 

Számos módszer és technológiák használatával Dsvm készletét hozza létre. Ez a cikk interaktív-beli virtuális gépek készletein foglalkozik. Egy alternatív felügyelt számítási infrastruktúra képes legyen az Azure Machine Learning Compute. Lásd: [állítsa be a számítási célokhoz](../service/how-to-set-up-training-targets.md#amlcompute) további információt.

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















