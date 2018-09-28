---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/13/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 2be7e4d2f3697649df669a4f20ba4db62c1fc486
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47401555"
---
Az Azure-erőforrások jogosultságot ad a metaadatok őket logikailag szervezheti a besorolás a címkékkel. Minden címke egy névből és a egy érték pár áll. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

A címkék alkalmazása után az előfizetés összes erőforrását lekérheti az adott címke nevével és értékével. A címkék lehetővé teszi különböző erőforráscsoportokban kapcsolódó erőforrások lekérése. Ez a megoldás akkor hasznos, ha számlázás vagy felügyelet céljából kell rendszereznie erőforrásait.

A besorolás figyelembe kell venni egy önkiszolgáló metaadatok címkézési stratégia mellett az Automatikus címkézés stratégia nagyobb pontosság és a felhasználók terhelésének csökkentése érdekében.

Az alábbi korlátozások érvényesek a címkékre:

* Minden egyes erőforrás vagy erőforráscsoport legfeljebb 15 címkenév/érték párral rendelkezhet. A korlátozás csak a közvetlenül az erőforráscsoportra vagy erőforrásra alkalmazott címkékre érvényes. Az erőforráscsoportok sok olyan erőforrást tartalmazhatnak, amelyek mindegyike 15 címkenév/érték párral rendelkezik. Ha több mint 15 értéket kell társítania egy erőforráshoz, használjon JSON-sztringet a címke értékéhez. A JSON-sztring sok olyan értéket tartalmazhat, amelyek egyetlen címkenévre vannak alkalmazva. Ez a cikk arra mutat be egy példát, hogyan rendelhető hozzá egy JSON-sztring a címkéhez.
* A címke neve legfeljebb 512 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet. A tárfiókok esetében a címke neve legfeljebb 128 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet.
* Virtuális gépek, legfeljebb 2048 karakter minden tartalmaznak a címkenevek és értékek összesen.
* Az egyes erőforráscsoportokra alkalmazott címkéket nem öröklik az adott erőforráscsoportba tartozó erőforrások.
* Klasszikus erőforrások, például a Cloud Services címkék nem alkalmazható.
* A címke neve nem tartalmazhatja a következő karaktereket: `<`, `>`, `%`, `&`, `\`, `?`, `/`
