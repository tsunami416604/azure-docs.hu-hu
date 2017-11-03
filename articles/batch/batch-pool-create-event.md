---
title: "Azure Batch-készlet esemény létrehozása |} Microsoft Docs"
description: "Útmutató a Batch-készlet esemény létrehozása."
services: batch
author: tamram
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: tamram
ms.openlocfilehash: 67edaa55d7ccd00d4aebb309f11bcf95486e87fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="pool-create-event"></a>Készlet esemény létrehozása

 Ez az esemény kibocsátott készlet létrehozása után. A napló tartalmának megmutatják a készlet kapcsolatos általános információkat. Vegye figyelembe, hogy ha a cél a készlet mérete nagyobb, mint 0 számítási csomópontok, a készlet átméretezési start esemény követi ezt az eseményt után azonnal.

 A következő példa bemutatja, hogy a szervezet egy készlet a CloudServiceConfiguration tulajdonság használatával létrehozott készlet esemény létrehozása.

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

|Elem|Típus|Megjegyzések|
|-------------|----------|-----------|
|id|Karakterlánc|A készlet azonosítóját.|
|displayName|Karakterlánc|A készlet megjelenítendő nevét.|
|vmSize|Karakterlánc|A virtuális gépek a készlet mérete. A készlet összes virtuális gép méretének. <br/><br/> További információ elérhető méretek a virtuális gépek Felhőszolgáltatásai számára (készletek cloudServiceConfiguration létre), készletek: [Felhőszolgáltatások mérete](http://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/). Kötegelt támogatja kivételével az összes felhőalapú szolgáltatások Virtuálisgép-méretek `ExtraSmall`.<br/><br/> Rendelkezésre álló virtuális gép információ lemezképek használatával a virtuális gépek piactérről (virtualMachineConfiguration létrehozott készleteket) készletek mérete: [virtuális gépek méretei](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) vagy [virtuális mérete Gépek](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows). A Batch a `STANDARD_A0`, illetve a Premium Storage típusú méretek (`STANDARD_GS`, `STANDARD_DS` és `STANDARD_DSV2` sorozat) kivételével az összes Azure virtuálisgép-méretet támogatja.|
|[cloudServiceConfiguration](#bk_csconf)|Összetett típus|A felhőalapú szolgáltatás konfigurációja, a készlet.|
|[virtualMachineConfiguration](#bk_vmconf)|Összetett típus|A virtuális gép a készlet konfigurációjában.|
|[Készlet](#bk_netconf)|Összetett típus|A készlet hálózati konfigurációjában.|
|resizeTimeout|Time|A számítási csomópontok a készlethez, az utolsó átméretezési művelet a készlet a megadott foglalási időkorlátját.  (A kezdeti méretezése a készlet létrehozásakor számít a átméretezési.)|
|targetDedicated|Int32|A kért a készlet számítási csomópontok száma.|
|enableAutoScale|logikai érték|Meghatározza, hogy a készlet mérete automatikusan igazodni adott idő alatt.|
|enableInterNodeCommunication|logikai érték|Meghatározza, hogy a készlet be van állítva a közvetlen kommunikációt a csomópontok között.|
|isAutoPool|logikai érték|Speficies hogy a készlet egy feladat AutoPool mechanizmus révén készült.|
|maxTasksPerNode|Int32|A készlet egyes számítási csomóponton egyidejűleg futtatható tevékenységek maximális száma.|
|vmFillType|Karakterlánc|Itt adhatja meg, hogy a Batch szolgáltatás miként ossza el a tevékenységeket a készlet számítási csomópontjai között. Érvényes értékek van elosztva, vagy a csomag.|

###  <a name="bk_csconf"></a>cloudServiceConfiguration

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|Család|Karakterlánc|Az Azure Vendég operációsrendszer-család kell telepíteni a virtuális gépek a készletben.<br /><br /> Lehetséges értékek:<br /><br /> **2** – operációs rendszer családja 2, Windows Server 2008 R2 SP1 egyenértékű.<br /><br /> **3** – operációs rendszer termékcsalád 3, a Windows Server 2012-ben egyenértékű.<br /><br /> **4** – operációs rendszer termékcsalád 4, Windows Server 2012 R2 egyenértékű.<br /><br /> További információkért lásd: [Azure vendég operációs rendszereinek kiadásait](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|
|targetOSVersion|Karakterlánc|Az Azure vendég operációs rendszer verzióját kell telepíteni a virtuális gépek a készletben.<br /><br /> Az alapértelmezett érték  **\***  amely adja meg a megadott termékcsalád operációs rendszer legújabb.<br /><br /> Más engedélyezett értékeket, lásd: [Azure vendég operációs rendszereinek kiadásait](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|

###  <a name="bk_vmconf"></a>virtualMachineConfiguration

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|[imageReference](#bk_imgref)|Összetett típus|Megadja a platform vagy Piactéri lemezképhez használandó információkat.|
|nodeAgentSKUId|Karakterlánc|A kötegelt csomópont ügynök, a számítási csomópont lett kiépítve a Termékváltozat.|
|[windowsConfiguration](#bk_winconf)|Összetett típus|Határozza meg a virtuális gépet a Windows operációs rendszer beállításait. Ez a tulajdonság nem lehet megadva, ha az imageReference egy Linux operációsrendszer-lemezképek hivatkozik.|

###  <a name="bk_imgref"></a>imageReference

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|Közzétevő|Karakterlánc|A lemezkép kiadója.|
|az ajánlat|Karakterlánc|Az ajánlat kép.|
|Termékváltozat|Karakterlánc|A kép a Termékváltozat.|
|Verzió|Karakterlánc|A lemezkép verziószámát.|

###  <a name="bk_winconf"></a>windowsConfiguration

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|enableAutomaticUpdates|Logikai érték|Azt jelzi, hogy engedélyezve van-e a virtuális gép az automatikus frissítések. Ha ez a tulajdonság nincs megadva, alapértelmezett értéke true.|

###  <a name="bk_netconf"></a>Készlet

|Elem neve|Típus|Megjegyzések|
|------------------|--------------|----------|
|subnetId|Karakterlánc|Adja meg az erőforrás-azonosító az alhálózat, amelyben a készlet számítási csomópontok jönnek létre.|
