---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 10/30/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c141e67157f3ec17d475062ec76406ec765c4f50
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199144"
---
Címkéket alkalmazhat az Azure-erőforrásokhoz, így a metaadatok logikailag rendszerezve lesznek a besorolásban. Minden címke egy nevet és egy érték párokat tartalmaz. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

A címkék alkalmazása után az előfizetés összes erőforrását lekérheti az adott címke nevével és értékével. A címkék lehetővé teszik a kapcsolódó erőforrások különböző erőforráscsoportokből való lekérését. Ez a megoldás akkor hasznos, ha számlázás vagy felügyelet céljából kell rendszereznie erőforrásait.

A besorolásnak egy önkiszolgáló metaadat-címkézési stratégiát kell figyelembe vennie egy automatikus címkézési stratégia mellett, amely csökkenti a felhasználók terhelését, és növelheti a pontosságot.

Az alábbi korlátozások érvényesek a címkékre:

* Nem minden erőforrástípus támogatja a címkéket. Annak megállapításához, hogy lehet-e címkét alkalmazni az erőforrás típusára, tekintse meg [Az Azure-erőforrások támogatásának címkézését](../articles/azure-resource-manager/tag-support.md)ismertető témakört.
* Minden erőforrás vagy erőforráscsoport legfeljebb 50 címke név/érték párokat tartalmazhat. Ha a maximálisan megengedettnél több címkét kell alkalmaznia, használjon egy JSON-karakterláncot a címke értékhez. A JSON-sztring sok olyan értéket tartalmazhat, amelyek egyetlen címkenévre vannak alkalmazva. Az erőforráscsoportok számos olyan erőforrást tartalmazhatnak, amelyek mindegyike 50 címke név/érték párokat tartalmaz.
* A címke neve legfeljebb 512 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet. A tárfiókok esetében a címke neve legfeljebb 128 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet.
* Az általánosított virtuális gépek nem támogatják a címkéket.
* Az egyes erőforráscsoportokra alkalmazott címkéket nem öröklik az adott erőforráscsoportba tartozó erőforrások.
* A címkék nem alkalmazhatók a klasszikus erőforrásokra, például a Cloud Servicesra.
* A címkék neve nem tartalmazhatja a következő karaktereket: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > Jelenleg Azure DNS zónák és Traffic Manger-szolgáltatások nem engedélyezik a szóközök használatát a címkében. 
