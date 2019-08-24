---
title: Data Science virtuális gép készletek – Azure |} A Microsoft Docs
description: Dsvm-készletek üzembe helyezése megosztott erőforrásként egy csapat számára
keywords: deep learning, AI, beépített adatelemzési eszközzel, az adatelemzési virtuális gépet, a térinformatikai elemzés, a csoportos adatelemzési folyamat
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: vijetaj
ms.openlocfilehash: 76a715838fa5eb4f806fccc98675ea5acdbe1822
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992037"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Az adatelemző virtuális gépek megosztott készlet létrehozása

Ez a cikk azt ismerteti, hogyan hozhat létre egy csapat számára egy megosztott adatelemzési Virtual Machines (Dsvm). A megosztott készlet használatának előnyei közé tartozik a jobb erőforrás-használat, a könnyebb megosztás és együttműködés, valamint a DSVM erőforrások hatékonyabb kezelése.

Számos módszer és technológiák használatával Dsvm készletét hozza létre. Ez a cikk az interaktív virtuális gépek (VM-EK) készleteit tárgyalja. Egy alternatív felügyelt számítási infrastruktúra Azure Machine Learning számítás. További információ: [számítási célok beállítása](../service/how-to-set-up-training-targets.md#amlcompute).

## <a name="interactive-vm-pool"></a>Interaktív VM-készletet

Egy interaktív virtuális gépek, az egész mesterséges Intelligencia/data science csapat által megosztott készletét lehetővé teszi a felhasználóknak jelentkezzen be a dsvm-hez dedikált-példányt az egyes felhasználók nem érhető el példányát. Ez a beállítás lehetővé teszi az erőforrások jobb rendelkezésre állását és hatékonyabb kihasználását.

Az [Azure virtuálisgép](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) -méretezési csoportok technológiájának használatával interaktív virtuálisgép-készletet hozhat létre. A méretezési csoportok segítségével azonos, elosztott terhelésű egy csoportját, és automatikus méretezést végző virtuális gépek létrehozása és kezelése.

A felhasználó bejelentkezik a fő készlet IP- vagy DNS-cím. A méretezési csoport automatikusan útvonalakat a munkamenet a rendelkezésre álló dsvm-hez a méretezési csoportban. Mivel a felhasználók egységes és ismerős környezetet kívánnak, függetlenül attól, hogy milyen virtuális gépről jelentkeznek be, a méretezési csoport minden példánya megosztott hálózati meghajtót csatlakoztat, például egy Azure Files megosztást vagy egy hálózati fájlrendszert (NFS-megosztást). A felhasználó megosztott munkaterületen általában tartani, az egyes példányok csatlakoztatott megosztott fájlrendszert Store-ból.

Egy minta Azure Resource Manager-sablon, amely létrehoz egy méretezési csoportot az Ubuntu DSVM-példányok a annak [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Ugyanazon a helyen talál egy mintát a [](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) Azure Resource Manager sablonhoz.

A méretezési csoport a Azure Resource Manager sablon alapján hozható létre az Azure CLI-ben található paraméterérték értékének megadásával:

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

Az előző sablon lehetővé teszi, hogy az SSH és a JupyterHub portot az előtérbeli méretezési csoportból, a háttérkészlet Ubuntus Dsvm. Felhasználóként a virtuális gépre egy Secure Shell (SSH) vagy a JupyterHub normál módon jelentkezhet be. Mivel a virtuálisgép-példányok dinamikusan méretezhetők vagy leállíthatók, minden állapotot a csatlakoztatott Azure Files-megosztásba kell menteni. Ugyanezzel a módszerrel használhatja a Dsvm Windows-készlet létrehozása.

A [parancsfájlt, amely csatlakoztatja az Azure-fájlmegosztási](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) érhető el az Azure DataScienceVM tárházban a Githubon. A parancsfájl csatlakoztatja az Azure-fájlmegosztással a paraméterfájlban megadott csatlakoztatási ponton. A parancsfájl a kezdeti felhasználó kezdőkönyvtárának is létrehoz a csatlakoztatott meghajtó mutató szoftveres hivatkozásokat. A Azure Files-megosztásban lévő felhasználó-specifikus jegyzetfüzet-könyvtár nem kapcsolódik a `$HOME/notebooks/remote` címtárhoz, így a felhasználók hozzáférhetnek, futtathatnak és menthetik a Jupyter-jegyzetfüzeteket. Használhatja ugyanazt az konvenciót további felhasználók minden egyes felhasználó Jupyter munkaterületét átirányítása az Azure-fájlmegosztást a virtuális gép létrehozásakor.

Virtuálisgép-méretezési csoport beállítása az automatikus skálázás támogatása. Szabályokat állíthat be a további példányok létrehozásához és a példányok méretezési idejéhez. Ha például vertikális nulla-példányok menteni a felhőbeli hardver adathasználati költségek, ha a virtuális gépek egyáltalán nem használja. A virtual machine scale sets dokumentációja lapok adja meg a részletes lépései [az automatikus skálázás](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>További lépések

* [Közös identitás beállítása](dsvm-common-identity.md)
* [A felhőbeli erőforrások eléréséhez szükséges hitelesítő adatok biztonságos tárolása](dsvm-secure-access-keys.md)















