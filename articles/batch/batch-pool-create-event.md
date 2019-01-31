---
title: Az Azure Batch-készlet létrehozása esemény |} A Microsoft Docs
description: Referencia a Batch-készlet létrehozása esemény.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 176f00de77c2d353d6efeb8b5a535a607b8f3204
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470593"
---
# <a name="pool-create-event"></a>Készlet létrehozása esemény

 Ez az esemény bocsásson ki egy készlet létrehozása után. A napló tartalmát a készlet kapcsolatos általános adatok mellett ki. Vegye figyelembe, hogy ha a cél a készlet mérete nagyobb, mint 0, számítási csomópontok, a készlet átméretezésének indítása esemény követi az esemény után azonnal.

 Az alábbi példa bemutatja, hogy a szervezet egy készlet a CloudServiceConfiguration tulajdonság használatával létrehozott készlet esemény létrehozása.

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicated": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Elem|Typo|Megjegyzések|
|-------------|----------|-----------|
|id|String|A készlet azonosítója.|
|displayName|String|A készlet megjelenítendő neve.|
|vmSize|String|A készlet virtuális gépeinek mérete. Egy készletben található összes virtuális gép csak azonos méretű. <br/><br/> További információ elérhető méretek a virtuális gépek a Cloud Services (cloudServiceConfiguration létrehozott készletek), készletek: [méretű felhőszolgáltatások](https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/). A Batch kivételével az összes Cloud Services Virtuálisgép-méretet támogatja `ExtraSmall`.<br/><br/> Rendelkezésre álló virtuális gép információ lemezképek használatával a Virtual Machines-piactér (virtualMachineConfiguration létrehozott készletek) a készletek méretek: [virtuális gépek méretei](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) vagy [virtuális méretei Gépek](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows). A Batch a `STANDARD_A0`, illetve a Premium Storage típusú méretek (`STANDARD_GS`, `STANDARD_DS` és `STANDARD_DSV2` sorozat) kivételével az összes Azure virtuálisgép-méretet támogatja.|
|[cloudServiceConfiguration](#bk_csconf)|Komplex típus|A felhőszolgáltatás-konfigurációt a készlet.|
|[virtualMachineConfiguration](#bk_vmconf)|Komplex típus|A virtuális gép konfigurációja a készlet.|
|[networkConfiguration](#bk_netconf)|Komplex típus|A készlet hálózati konfigurációja.|
|resizeTimeout|Time|A lefoglalt számítási csomópontok a készlet átméretezése az utolsó műveletet a megadott készlet időkorlátja.  (A kezdeti méretezése a készlet létrehozásakor számít egy átméretezést.)|
|targetDedicated|Int32|Számítási csomópontok a készlet kért száma.|
|enableAutoScale|Bool|Itt adhatja meg, hogy a készlet mérete automatikusan alkalmazkodik a kijelző idővel.|
|enableInterNodeCommunication|Bool|Itt adhatja meg, hogy a készlet be van állítva a közvetlen kommunikáció a csomópontok között.|
|isAutoPool|Bool|Megadja, hogy a készlet hozták-e egy feladat AutoPool mechanizmus révén.|
|maxTasksPerNode|Int32|A készlet egyetlen számítási csomóponton egyidejűleg futtatható tevékenységek maximális számát.|
|vmFillType|String|Itt adhatja meg, hogy a Batch szolgáltatás miként ossza el a tevékenységeket a készlet számítási csomópontjai között. Érvényes értékek vannak elosztva, vagy a csomag.|

###  <a name="bk_csconf"></a> cloudServiceConfiguration

|Elem neve|Typo|Megjegyzések|
|------------------|----------|-----------|
|operációsrendszer-család|String|Az Azure vendég operációs rendszerek kell telepíteni a virtuális gépek a készletben.<br /><br /> Lehetséges értékek:<br /><br /> **2** – operációs rendszer termékcsalád 2, Windows Server 2008 R2 SP1 egyenértékű.<br /><br /> **3** – operációs rendszer termékcsalád 3, egyenértékű a Windows Server 2012-re.<br /><br /> **4** – operációs rendszer termékcsalád 4, Windows Server 2012 R2 egyenértékű.<br /><br /> További információkért lásd: [Azure Vendégoperációsrendszer-kiadásainak listáját](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|
|targetOSVersion|String|A kell telepíteni a virtuális gépek, a készlet Azure vendég operációs rendszer verziója.<br /><br /> Az alapértelmezett érték **\*** amely megadja, hogy a megadott termékcsalád legújabb operációs rendszerre.<br /><br /> További egyéb engedélyezett értékek: [Azure Vendégoperációsrendszer-kiadásainak listáját](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|

###  <a name="bk_vmconf"></a> virtualMachineConfiguration

|Elem neve|Typo|Megjegyzések|
|------------------|----------|-----------|
|[imageReference](#bk_imgref)|Komplex típus|Adja meg a platform vagy a Piactéri rendszerkép használatára.|
|nodeAgentSKUId|String|Az a kiépítése a számítási csomóponton a Batch-csomóponti ügynök SKU.|
|[windowsConfiguration](#bk_winconf)|Komplex típus|A virtuális gépen adja meg a Windows operációs rendszer beállításait. Ez a tulajdonság nem lehet megadva, ha az imageReference a Linux operációs rendszer lemezképe hivatkozik.|

###  <a name="bk_imgref"></a> imageReference

|Elem neve|Typo|Megjegyzések|
|------------------|----------|-----------|
|publisher|String|A lemezkép kiadója.|
|az ajánlat|String|Az ajánlatot a lemezkép.|
|termékváltozat|String|A lemezkép Termékváltozata.|
|version|String|A lemezkép verziószámát.|

###  <a name="bk_winconf"></a> windowsConfiguration

|Elem neve|Typo|Megjegyzések|
|------------------|----------|-----------|
|enableAutomaticUpdates|Logikai|Azt jelzi, hogy a virtuális gépen engedélyezve van-e az automatikus frissítések. Ha ez a tulajdonság nincs megadva, alapértelmezett értéke true.|

###  <a name="bk_netconf"></a> networkConfiguration

|Elem neve|Typo|Megjegyzések|
|------------------|--------------|----------|
|subnetId|String|Itt adhatja meg az alhálózatot, amelyben a készlet számítási csomópontjainak létrehozása erőforrás-azonosítója.|
