---
title: Az Azure Service Fabric Mesh alkalmazások méretezhetősége
description: Az alkalmazások Service Fabric Mesh szolgáltatásra való üzembe helyezésének egyik előnye, hogy könnyedén skálázhatja a szolgáltatásokat manuálisan vagy automatikus skálázási szabályzatokkal.
author: dkkapur
ms.author: dekapur
ms.date: 10/26/2018
ms.topic: conceptual
ms.openlocfilehash: 474eda904df653d514fd2ee59fa046f1f87a66aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259174"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Szolgáltatásháló-alkalmazások méretezése

Az alkalmazások Service Fabric Mesh szolgáltatásba való üzembe helyezésének egyik fő előnye, hogy könnyedén skálázhatja a szolgáltatásokat. Ezt a szolgáltatások különböző terhelésének kezelésére vagy a rendelkezésre állás javítására kell használni. Manuálisan skálázhatja a szolgáltatásokat, illetve ki, vagy automatikus skálázási házirendek beállítása.

## <a name="manual-scaling-instances"></a>Manuális méretezési példányok

Az alkalmazás erőforrásának üzembehelyezési sablonjában mindkét szolgáltatás rendelkezik egy *replicaCount* (replikaszám) tulajdonsággal, amellyel megadhatja, hány példányban szeretné a szolgáltatást üzembe helyezni. Egy alkalmazás több szolgáltatásból is állhat, és minden szolgáltatás egyedi *replicaCount* értékkel rendelkezik. A replikákat a rendszer együtt kezeli és helyezi üzembe. A szolgáltatásreplikák számának módosításához módosítsa minden olyan szolgáltatás *replicaCount* értékét az üzembehelyezési sablonban vagy a paraméterfájlban, amelyet horizontálisan le szeretne skálázni. Ezt követően frissítse az alkalmazást.

A szolgáltatások példányainak manuális méretezése például [a Szolgáltatások manuális méretezése be- és kiszállítása.](service-fabric-mesh-tutorial-template-scale-services.md)

## <a name="autoscaling-service-instances"></a>Automatikus skálázási szolgáltatáspéldányok
Automatikus skálázás egy további képesség a Service Fabric dinamikusan skálázhatja a szolgáltatáspéldányok száma (horizontális skálázás) további képessége. Az automatikus skálázás nagy rugalmasságot biztosít, és lehetővé teszi a szolgáltatáspéldányok kiépítését vagy eltávolítását a PROCESSZOR vagy a memória kihasználtsága alapján.  Automatikus skálázás lehetővé teszi, hogy a megfelelő számú szolgáltatáspéldányok futtatásához a számítási feladatok hoz, és optimalizálja a költségeket.

Az automatikus skálázási házirend szolgáltatásonként van definiálva a szolgáltatáserőforrás-fájlban. Minden skálázási szabályzat két részből áll:

- A skálázási eseményindító, amely leírja, ha a szolgáltatás méretezése kerül végrehajtásra. Három tényező határozza meg, hogy a szolgáltatás mikor méreteződik. *Az alacsonyabb terhelési küszöbérték* egy olyan érték, amely meghatározza, hogy a szolgáltatás mikor lesz méretezve. Ha a partíciók összes példányának átlagos terhelése alacsonyabb, mint ez az érték, akkor a szolgáltatás lesz méretezve. *A felső terhelési küszöbérték* egy olyan érték, amely meghatározza, hogy a szolgáltatás mikor lesz horizontálisan kiskálázva. Ha a partíció összes példányának átlagos terhelése magasabb, mint ez az érték, akkor a szolgáltatás horizontálisan ki lesz osztva. *A méretezési időköz* határozza meg, hogy az eseményindító milyen gyakran (másodpercben) legyen ellenőrizve. Miután az eseményindító be van jelölve, ha méretezésre van szükség, a rendszer alkalmazza a mechanizmust. Ha nincs szükség méretezésre, akkor a program nem történik művelet. Mindkét esetben az eseményindító nem lesz újra ellenőrizve, mielőtt a méretezési időköz lejár.

- A skálázási mechanizmus, amely leírja, hogyan lesz a méretezés, amikor aktiválódik. *A méretezési növekmény* határozza meg, hogy hány példány kerül hozzáadásra vagy eltávolításra a mechanizmus aktiválásakor. *A maximális példányszám* határozza meg a méretezés felső határát. Ha a példányok száma eléri ezt a korlátot, majd a szolgáltatás nem lesz horizontális felskálázva, függetlenül a terhelés. *A minimális példányszám* határozza meg a méretezés alsó határát. Ha a partíció példányainak száma eléri ezt a korlátot, majd a szolgáltatás nem lesz méretezve, függetlenül a terhelés.

Ha meg szeretné tudni, hogyan állíthat be automatikus skálázási szabályzatot a szolgáltatáshoz, olvassa el [az automatikus skálázási szolgáltatásokat.](service-fabric-mesh-howto-auto-scale-services.md)

## <a name="next-steps"></a>További lépések

Az alkalmazásmodellről a [Service Fabric-erőforrások című](service-fabric-mesh-service-fabric-resources.md) témakörben talál további információt.
