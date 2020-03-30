---
title: Közös medencék
titleSuffix: Azure Data Science Virtual Machine
description: Ismerje meg, hogyan hozhat létre & az adatelemzési virtuális gépek (DSVM-ek) megosztott készletét egy csapat megosztott erőforrásaként telepítheti.
keywords: deep learning, AI, data science tools, data science virtual machine, geospatial analytics, team data science process
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: cc0efc0a076ddc3fc9425999f1e38b4a32dec7a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477340"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Adatelemzési virtuális gépek megosztott készletének létrehozása

Ebben a cikkben megtudhatja, hogyan hozhat létre egy megosztott készlet adatelemzési virtuális gépek (DSVMs) egy csapat. A megosztott készlet használatának előnyei közé tartozik a jobb erőforrás-kihasználtság, a könnyebb megosztás és együttműködés, valamint a DSVM-erőforrások hatékonyabb kezelése.

Számos módszerrel és technológiával hozhat létre DSVM-készletet. Ez a cikk az interaktív virtuális gépek (VM-ek) készleteire összpontosít. Egy másik felügyelt számítási infrastruktúra az Azure Machine Learning Compute. További információt a Számítási célok beállítása című témakörben [talál.](../how-to-set-up-training-targets.md#amlcompute)

## <a name="interactive-vm-pool"></a>Interaktív virtuális gép készlet

Az interaktív virtuális gépek készlete, amelyet a teljes AI/data science csapat megoszt, lehetővé teszi a felhasználók számára, hogy jelentkezzen be a DSVM egy rendelkezésre álló példányába, ahelyett, hogy a felhasználók minden egyes csoportjához külön példányt rendelkezne. Ez a beállítás lehetővé teszi az erőforrások jobb rendelkezésre állását és hatékonyabb kihasználását.

Az [Azure virtuálisgép-méretezési beállítások](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) technológia interaktív virtuálisgép-készlet létrehozásához. Skálázható készletek használatával azonos, terheléselosztásos és automatikus skálázási virtuális gépek csoportját hozhat létre és kezelheti.

A felhasználó bejelentkezik a fő készlet IP- vagy DNS-címére. A méretezési készlet automatikusan irányítja a munkamenetet egy elérhető DSVM-hez a méretezési készletben. Mivel a felhasználók konzisztens és ismerős környezetet szeretnének, függetlenül attól, hogy a virtuális gépbe bejelentkeznek, a méretezési csoportban lévő virtuális gép összes példánya megosztott hálózati meghajtót, például egy Azure Files-megosztást vagy egy hálózati fájlrendszer (NFS) megosztást. A felhasználó megosztott munkaterülete általában az egyes példányokra csatlakoztatott megosztott fájltárolóban marad.

A [GitHubon](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json)található egy minta Azure Resource Manager-sablon, amely az Ubuntu DSVM-példányokkal egy méretezési csoportot hoz létre. Az Azure Resource Manager-sablon [paraméterfájljának](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) egy mintáját találja ugyanazon a helyen.

A méretezési csoportot az Azure Resource Manager sablonból hozhatja létre, ha megadja a paraméterfájl értékeit az Azure CLI-ben:

```azurecli-interactive
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```

Az előző parancsok feltételezik, hogy:

* A paraméterfájl másolata a méretezési csoport példányához megadott értékekkel.
* A virtuálisgép-példányok száma.
* Az Azure Files megosztására mutató mutatók.
* Az egyes virtuális gépeken leszcsatolva lévő tárfiók hitelesítő adatai.

A paraméterfájlra helyileg hivatkozik a parancsok. A parancsfájlban paramétereket is átadhat, vagy kérheti őket.  

Az előző sablon lehetővé teszi az SSH és a JupyterHub port az előtér-skálán készlet az Ubuntu DSVMs háttér-készlet. Felhasználóként a virtuális gépre egy biztonságos rendszerhéjon (SSH) vagy a JupyterHubon a szokásos módon jelentkezik be. Mivel a virtuálisgép-példányok dinamikusan skálázhatók fel- és leskálázhatók, minden állapotot menteni kell a csatlakoztatott Azure Files megosztásba. Ugyanezt a módszert használhatja a Windows DSVM-ek készletének létrehozásához.

Az [Azure Files-megosztást csatlakoztató parancsfájl](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) a GitHub Azure DataScienceVM-tárházában is elérhető. A parancsfájl csatlakoztatja az Azure Files megosztása a megadott csatlakoztatási pont a paraméterfájlban. A parancsfájl a csatlakoztatott meghajtóhoz való lágy hivatkozásokat is létrehoz a kezdeti felhasználó kezdőkönyvtárában. Az Azure Files megosztás felhasználóspecifikus jegyzetfüzetkönyvtára a `$HOME/notebooks/remote` címtárhoz van csatolva, így a felhasználók elérhetik, futtathatják és menthetik jupyter-jegyzetfüzeteiket. Használhatja ugyanazt a konvenciót, amikor további felhasználókat hoz létre a virtuális gépen, hogy az egyes felhasználók Jupyter munkaterületét az Azure Files megosztásra irányítsa.

A virtuálisgépek méretezési készletei támogatják az automatikus skálázást. Beállíthatja, hogy mikor hozzon létre további példányokat, és mikor kell lekicsinyíteni a példányokat. Például lefelé skálázhatja a nulla példányok menteni a felhőhardver-használati költségek, ha a virtuális gépek nem használják egyáltalán. A virtuálisgép-méretezési csoportok dokumentációs lapjai részletes lépéseket tartalmaznak az [automatikus skálázáshoz.](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)

## <a name="next-steps"></a>További lépések

* [Közös identitás beállítása](dsvm-common-identity.md)
* [A hitelesítő adatok biztonságos tárolása a felhőbeli erőforrások eléréséhez](dsvm-secure-access-keys.md)
