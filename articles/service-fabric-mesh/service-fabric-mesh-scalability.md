---
title: Az Azure Service Fabric méretezhetőségét alkalmazások tervezhetők |} A Microsoft Docs
description: Ismerje meg a méretezési szolgáltatások az Azure Service Fabric-háló.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 10/26/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: c58961011231f74b09abdf313b8624ddc8ad52c1
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892340"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Service Fabric-háló alkalmazások méretezése

Az alkalmazások a Service Fabric Meshben való üzembe helyezésének egyik legfőbb előnye, hogy a szolgáltatásokat egyszerűen lehet horizontálisan le- és felskálázni. Ez akkor hasznos, ha változó mértékű terhelés éri a szolgáltatásokat, vagy ha javítani szeretne a rendelkezésre álláson. A szolgáltatások vagy vagy a telepítő az automatikus skálázás házirendek manuálisan méretezhetők.

## <a name="manual-scaling-instances"></a>Manuális skálázásához példányok

Az alkalmazás erőforrásának üzembehelyezési sablonjában mindkét szolgáltatás rendelkezik egy *replicaCount* (replikaszám) tulajdonsággal, amellyel megadhatja, hány példányban szeretné a szolgáltatást üzembe helyezni. Egy alkalmazás több szolgáltatásból is állhat, és minden szolgáltatás egyedi *replicaCount* értékkel rendelkezik. A replikákat a rendszer együtt kezeli és helyezi üzembe. A szolgáltatásreplikák számának módosításához módosítsa minden olyan szolgáltatás *replicaCount* értékét az üzembehelyezési sablonban vagy a paraméterfájlban, amelyet horizontálisan le szeretne skálázni. Ezt követően frissítse az alkalmazást.

A Manuális méretezés services-példányok példákért lásd [manuálisan méretezhető, vagy a szolgáltatások](service-fabric-mesh-tutorial-template-scale-services.md).

## <a name="autoscaling-service-instances"></a>Az automatikus skálázás szolgáltatáspéldányok
Automatikus skálázás egy további lehetőség a Service Fabric dinamikusan méretezheti a szolgáltatás-példányok (horizontális skálázás). Automatikus skálázás nagy rugalmasságot biztosít, és lehetővé teszi a kiépítés vagy a CPU és memória-kihasználtság alapján szolgáltatáspéldányok eltávolítása.  Automatikus skálázás lehetővé teszi a megfelelő számú szolgáltatás példányainak a számítási feladatok futtatásához, és optimalizálhatja a költségeket.

Az automatikus skálázási szabályzat szolgáltatásonként a erőforrás fájlban van meghatározva. Minden egyes méretezési szabályzat két részből áll:

- Egy méretezési eseményindító, amely azt ismerteti, amikor a szolgáltatás méretezése történik. Nincsenek mindhárom tényezőt, amelyek meghatározzák, ha a szolgáltatás lesz skálázva. *Alsó betöltési küszöb* érték, amely azt határozza meg, ha a szolgáltatás a program átméretezi a. Ha az átlagos terhelés szoftverpéldányok a partíciók száma nem éri el ezt az értéket, majd a szolgáltatás a program átméretezi a. *Felső betöltési küszöb* érték, amely azt határozza meg, ha a szolgáltatás fogja horizontálisan felskálázott. Ha magasabb, mint ezt az értéket partíció összes példány átlagos terhelés, majd a szolgáltatás fogja terjeszthető ki. *Méretezési időközhöz* határozza meg, hogy milyen gyakran (másodpercben) az eseményindító be van jelölve. Amint az eseményindító be van jelölve, ha szükség van a méretezés a mechanizmus lépnek érvénybe. Ha már nincs szükség a méretezés, semmilyen művelet nem lesz végrehajtva. Mindkét esetben az eseményindító nem kerül sor újra méretezési időközhöz lejárta előtt.

- Egy méretezési mechanizmust, amely azt ismerteti, hogyan skálázás történik aktiválásakor. *Növekmény méretezése* meghatározza, hogy hány példányt fog hozzáadásának vagy eltávolításának a mechanizmus aktiválásakor. *Példányok maximális száma* határozza meg a felső határ méretezését. Ha a példányok száma eléri a korlátot, majd a szolgáltatás fog nem lehet horizontálisan felskálázott függetlenül a terhelés. *Példányok minimális száma* határozza meg az alsó határ méretezését. Ha a partíció-példányok száma eléri a korlátot, majd szolgáltatás fog nem növelhető a függetlenül a terhelés.

A szolgáltatás egy automatikus skálázási szabályának beállításáról, olvassa el [automatikus skálázási szolgáltatások](service-fabric-mesh-howto-auto-scale-services.md).

## <a name="next-steps"></a>További lépések

Az alkalmazásmodell kapcsolatos tudnivalókat lásd: [Service Fabric-erőforrások](service-fabric-mesh-service-fabric-resources.md)
