---
title: Felügyelt fürtök Service Fabric (előzetes verzió)
description: Service Fabric felügyelt fürtök az Azure Service Fabric fürterőforrás-modell fejlődése, amely egyszerűsíti az üzembe helyezést és a fürtözést.
ms.topic: overview
ms.date: 09/28/2020
ms.openlocfilehash: 3d26a92126491662d5c51b3c4e8900ffa547f830
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91410488"
---
# <a name="service-fabric-managed-clusters-preview"></a>Felügyelt fürtök Service Fabric (előzetes verzió)

Service Fabric felügyelt fürtök az Azure Service Fabric fürterőforrás-modell fejlődése, amely egyszerűsíti az üzembe helyezési és a fürtszolgáltatási élményt.

A hagyományos Service Fabric fürtökhöz készült Azure Resource Model-(ARM-) sablonhoz a fürt erőforrásait számos támogató erőforrás mellett kell megadni. a fürt és a szolgáltatások megfelelő működéséhez az szükséges, hogy a fürt és a szolgáltatások megfelelően működjenek. Ezzel szemben a Service Fabric felügyelt fürtök beágyazási modellje egyetlen, *Service Fabric felügyelt fürterőforrás* -erőforrásból áll. A fürt összes mögöttes erőforrását elvontuk, és az Azure felügyeli az Ön nevében.

**Service Fabric hagyományos Fürtmodell** 
 ![ Service Fabric hagyományos Fürtmodell][sf-composition]

**Service Fabric felügyelt fürt modellje** 
 ![ Beágyazott Fürtmodell Service Fabric][sf-encapsulation]

A méret és az összetettség szempontjából a Service Fabric felügyelt fürt ARM-sablonja körülbelül 100 sornyi JSON-t, illetve néhány 1000 sort igényel egy tipikus Service Fabric-fürt definiálásához:

| Service Fabric-erőforrások | Felügyelt fürterőforrások Service Fabric |
|----------|-----------|
| Service Fabric-fürt | Felügyelt fürt Service Fabric |
| Virtuálisgép-méretezési csoport (ok) | |
| Terheléselosztó | |
| Nyilvános IP-cím | |
| Tárfiók(ok) | |
| Virtuális hálózat | |

Service Fabric felügyelt fürtök számos előnyt biztosítanak a hagyományos fürtökhöz képest:

**Egyszerűsített fürtök üzembe helyezése és kezelése**
- Egyetlen Azure-erőforrás üzembe helyezése és kezelése
- Tanúsítványkezelő és autorotáció
- Egyszerűsített skálázási műveletek

**Működési hibák megelőzése**
- Az alapul szolgáló erőforrásokkal való eltérő konfiguráció tiltása
- Nem biztonságos műveletek letiltása (például a mag csomópontjának törlése)

**Ajánlott eljárások alapértelmezés szerint**
- Egyszerűsített megbízhatósági és tartóssági beállítások

Service Fabric felügyelt fürtökre a fürthöz szükséges mögöttes erőforrások díja után nem jár további díj.

## <a name="service-fabric-managed-cluster-skus"></a>Service Fabric felügyelt fürt SKU-i

Service Fabric felügyelt fürtök az alapszintű és a standard SKU-ban is elérhetők.

| Szolgáltatás | Alapszintű | Standard |
| ------- | ----- | -------- |
| Hálózati erőforrás (SKU [Load Balancer](../load-balancer/skus.md), [nyilvános IP-cím](../virtual-network/public-ip-addresses.md)) | Alapszintű | Standard |
| Minimális csomópont (VM-példány) száma | 3 | 5 |
| Csomópontok maximális száma/csomópont típusa | 100 | 100 |
| Csomópontok maximális száma | 1 | 20 |
| Csomópont-típusok hozzáadása/eltávolítása | Nem | Igen |
| Zónaredundancia | Nem | Igen |

## <a name="service-fabric-managed-cluster-feature-roadmap"></a>Service Fabric felügyelt fürt szolgáltatásának ütemterve
Mivel ez a Service Fabric felügyelt fürtök korai előzetes verziója, néhány hiányosságot kell figyelembe vennie. Ezek a funkciók a későbbi kiadásokban lesznek elérhetők. 

* Alkalmazások közzététele közvetlenül a Visual studióból
* Felügyelt identitások 
* ARM-alkalmazások telepítései 
* Rendelkezésre állási zónák 
* Fordított proxy 
* Automatikus skálázás 
* NSG-szabályok frissítése 
* Automatikus operációs rendszer frissítése

## <a name="next-steps"></a>További lépések

Service Fabric felügyelt fürtök használatának megkezdéséhez próbálja ki a gyors üzembe helyezési útmutatót:

> [!div class="nextstepaction"]
> [Service Fabric felügyelt fürt létrehozása (előzetes verzió)](quickstart-managed-cluster-template.md)


[sf-composition]: ./media/overview-managed-cluster/sfrp-composition-resource.png
[sf-encapsulation]: ./media/overview-managed-cluster/sfrp-encapsulated-resource.png