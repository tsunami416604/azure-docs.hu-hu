---
title: Hogyan távoli csomópont írja be az Azure Service Fabricben |} A Microsoft Docs
description: Ismerje meg, az Azure Service Fabric-csomóponttípus eltávolítása
services: service-fabric
documentationcenter: .net
author: v-steg
manager: JeanPaul.Connick
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/26/2018
ms.author: v-steg
ms.openlocfilehash: 3704a356763b16a30285baee1aabffdd3aa3f8aa
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53980702"
---
# <a name="remove-a-service-fabric-node-type"></a>Távolítsa el a Service Fabric-csomópont típusa

Használat [Remove-AzureRmServiceFabricNodeType](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) egy Service Fabric-csomóponttípus eltávolítása.

A Remove-AzureRmServiceFabricNodeType meghívásakor előforduló két műveletek a következők:
1.  A virtuális gép méretezési beállítása (VMSS) mögött typ uzlu törlődik.
2.  Az összes csomóponthoz, hogy a csomópont típusa belül a csomóponthoz tartozó teljes állapot eltávolítani a rendszerből. Ha ezen a csomóponton szolgáltatásokat, majd a szolgáltatások először kerülnek egy másik csomópontra. A kezelőt nem található a csomópont a replika vagy szolgáltatások, a művelet, késleltetett/blokkolja-e.

> [!NOTE]
> Typ uzlu eltávolítása egy éles fürtöt Remove-AzureRmServiceFabricNodeType használatával nem ajánlott a gyakran használható. Ebben az esetben ez megegyezik a nagyon súlyos parancs alapvetően törli a Virtual Machine Scale Set erőforrás mögött typ uzlu. Remove-AzureRmServiceFabricNodeType hívásakor a rendszer már nem lehet tudni, hogy ha az Eltávolítás folyamatban van a biztonságos konkrét. 

## <a name="durability-characteristics"></a>Tartóssági jellemzői
Biztonsági van szándék sebességét, Remove-AzureRmServiceFabricNodeType használatakor. Silver vagy Gold [tartóssági jellemzők](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) ajánlott, mert:
- Bronz nem ad meg semmilyen garanciát kapcsolatos állapotinformációkat mentése.
- Silver és Gold tartóssági trap módosításokat a vmss-hez.
- Gold ad is szabályozhatja az Azure VMSS alá frissíti.

A Service Fabric "összehangolja" mögöttes módosításokat, és frissíti, hogy ne legyen adatvesztés. Amikor eltávolít egy csomópontot a bronz tartóssági, azonban előfordulhat, hogy állapot adat elvész. Ha szeretne eltávolítani egy elsődleges csomóponttípus, és az alkalmazás állapot nélküli, bronz fogadható el. Éles környezetben állapotalapú alkalmazások és szolgáltatások futtatásakor, a minimális konfigurációs Silver kell lennie. Hasonlóképpen a termelési forgatókönyvekhez az elsődleges csomóponttípushoz mindig lehet Silver vagy Gold.

### <a name="more-about-bronze-durability"></a>További információ a bronz tartóssági

Egy csomópont típusa, amely bronz eltávolításakor a csomópontokon írja be a csomópont leáll azonnal. A Service Fabric nem trap bronz csomópontok VMSS frissítéseket, így minden virtuális gép leáll azonnal. Ha bármi állapot-nyilvántartó a csomópontokon, az adat elvész. Most, még akkor is, ha az állapot nélküli volt, a Service Fabric összes csomópontja részt a kört, így előfordulhat, hogy egy teljes hálózatok elvesztek, amely hatással lehet a fürt, másrészt.

Eltérően eltávolítása egyetlen csomópont, mert kiszorulnak, egyszerre egy csomópont eltávolíthatja, várja meg, a replikákat és a szolgáltatások átvitele, a rendszer stabilizálását, egy másik csomópont eltávolítása előtt várja meg, és így tovább.  Azonban ha egyszerre egyszerre több csomópont eltávolítja, a fürt előfordulhat, hogy leáll (mivel a Service Fabric nem trap VMSS frissítéseket a bronz tartóssági).

## <a name="recommended-node-type-removal-process"></a>Csomópont típusa eltávolítást javasolt

A csomóponttípus eltávolítása a legtöbb biztonságos és gyors módon:
1.  Ha a bronz tartóssági használ, vagy nem szeretné, a rendszer a csomópont típusa törlés, a csomópontok, amely befolyásolja a csomópont típusa eltávolításához első üres állapot-nyilvántartó adatait vesznek el állapotinformáció tartalmazó alkalmazások áthelyezését a.
2.  Futtatás [Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) egyes az eltávolítani kívánt csomópontokat.
3.  Futtatás [Remove-AzureRmVmss](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#remove-vms-from-a-scale-set) virtuális gépek, amelyek hatással lesz a csomópont típusa eltávolításához.
4. Futtatás [Remove-AzureRmServiceFabricNodeType](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) eltávolítása a csomópont típusa.

## <a name="next-steps"></a>További lépések
- További tudnivalók a fürt [tartóssági jellemzők](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).
- Tudjon meg többet [csomóponttípusok és virtuálisgép-méretezési csoportok](service-fabric-cluster-nodetypes.md).
- Tudjon meg többet [Service Fabric-fürt méretezése](service-fabric-cluster-scaling.md).