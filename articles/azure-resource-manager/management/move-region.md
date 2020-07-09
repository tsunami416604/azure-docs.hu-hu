---
title: Azure-erőforrások áthelyezése másik régióba
description: Áttekintést nyújt az Azure-erőforrások Azure-régiók közötti áthelyezéséről.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 22d8bcee96b4ac52641d4f0841267195f44fe15a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75485207"
---
# <a name="moving-azure-resources-across-regions"></a>Azure-erőforrások áthelyezése régiók között

Ez a cikk az Azure-erőforrások Azure-régiók közötti áthelyezésével kapcsolatos információkat tartalmaz.

Az Azure földrajzi régiói, régiói és Availability Zones alkotják az Azure globális infrastruktúrájának alapjait. Az Azure [földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/) terület általában két vagy több [Azure-régiót](https://azure.microsoft.com/global-infrastructure/regions/)tartalmaz. A régió a földrajzon belüli terület, amely Availability Zoneseket és több adatközpontot tartalmaz. 

Az erőforrások adott Azure-régióban való üzembe helyezése után számos oka lehet, hogy az erőforrásokat egy másik régióba kívánja áthelyezni.

- **Igazítás régióhoz – indítás**: helyezze át az erőforrásokat egy újonnan bevezetett Azure-régióba, amely korábban nem volt elérhető.
- **Szolgáltatások/szolgáltatások igazítása**: az erőforrások áthelyezésével kihasználhatja az adott régióban elérhető szolgáltatások és szolgáltatások előnyeit.
- **Válaszadás az üzleti fejleményekre**: erőforrások áthelyezése egy régióba az üzleti változások, például az összeolvadások vagy a beszerzések megválaszolására válaszul.
- **Közelség**: erőforrások áthelyezése a vállalat helyi régiójába.
- Az **adattárolási követelmények teljesítése**: helyezze át az erőforrásokat az adattárolási követelményekhez vagy az adatbesorolási igényekhez való igazodás érdekében. [További információk](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Válaszadás a központi telepítési követelményekre**: a hiba miatt üzembe helyezett erőforrások áthelyezése vagy a kapacitási igényekre való áttérés. 
- **Válasz a leszerelésre**: erőforrások áthelyezése a régiók leszerelése miatt.

## <a name="move-process"></a>Folyamat áthelyezése

A tényleges áthelyezési folyamat az áthelyezett erőforrásoktól függ. Vannak azonban gyakori lépések:

- **Előfeltételek ellenőrzése**: az előfeltételek közé tartozik annak biztosítása, hogy a szükséges erőforrások elérhetők legyenek a céltartományban, ellenőrizze, hogy van-e elegendő kvóta, és ellenőrizze, hogy az előfizetése hozzáférhet-e a célcsoporthoz.
- **Függőségek elemzése**: az erőforrások függőségei lehetnek más erőforrásokon. Az áthelyezés előtt állapítsa meg a függőségeket, hogy az áthelyezett erőforrások továbbra is a várt módon működjenek az áthelyezés után.
- **Felkészülés az áthelyezésre**: ezeket a lépéseket az áthelyezés előtt az elsődleges régióban hajthatja végre. Előfordulhat például, hogy exportálnia kell egy Azure Resource Manager sablont, vagy el kell kezdenie az erőforrások replikálását a forrásról a célhelyre.
- **Helyezze át az erőforrásokat: az**erőforrások áthelyezésének módja attól függ, hogy mi a teendő. Előfordulhat, hogy telepítenie kell egy sablont a célként megadott régióban, vagy feladatátvételt kell végeznie a célhelyre.
- **Megcélzott erőforrások elvetése**: az erőforrások áthelyezése után érdemes megtekinteni az erőforrásokat a célként megadott régióban, és el kell döntenie, hogy van-e olyan teendője, amelyre nincs szüksége.
- **Az áthelyezés véglegesítése**: miután megtörtént az erőforrások ellenőrzése a céltartományban, egyes erőforrásokhoz végső véglegesítési műveletre lehet szükség. Előfordulhat például, hogy egy olyan célcsoportban, amely most az elsődleges régió, lehet, hogy a vész-helyreállítást egy új másodlagos régióra kell beállítania. 
- **A forrás tisztítása**: végül, miután minden üzembe lett hozva az új régióban, törölheti és leszerelheti az elsődleges régiójában lévő áthelyezéshez és erőforrásokhoz létrehozott erőforrásokat.



## <a name="next-steps"></a>További lépések

A régiók közötti áthelyezést támogató erőforrások listáját lásd: a [művelet támogatásának áthelyezése az erőforrásokhoz](region-move-support.md).
