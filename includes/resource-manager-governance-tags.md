---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/20/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: f411504b0f4b7872e92a64c57fecbde863f532c6
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52270359"
---
Az Azure-erőforrások jogosultságot ad a metaadatok őket logikailag szervezheti a besorolás a címkékkel. Minden címke egy névből és a egy érték pár áll. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

A címkék alkalmazása után az előfizetés összes erőforrását lekérheti az adott címke nevével és értékével. A címkék lehetővé teszi különböző erőforráscsoportokban kapcsolódó erőforrások lekérése. Ez a megoldás akkor hasznos, ha számlázás vagy felügyelet céljából kell rendszereznie erőforrásait.

A besorolás figyelembe kell venni egy önkiszolgáló metaadatok címkézési stratégia mellett az Automatikus címkézés stratégia nagyobb pontosság és a felhasználók terhelésének csökkentése érdekében.

Az alábbi korlátozások érvényesek a címkékre:

* Nem minden erőforrástípus támogatja a címkék. Ha alkalmazhat egy címkét egy erőforrástípus megállapításához lásd: [címkézése Azure-erőforrások támogatása](../articles/azure-resource-manager/tag-support.md).
* Minden egyes erőforrás vagy erőforráscsoport legfeljebb 15 címkenév/érték párral rendelkezhet. A korlátozás csak a közvetlenül az erőforráscsoportra vagy erőforrásra alkalmazott címkékre érvényes. Az erőforráscsoportok sok olyan erőforrást tartalmazhatnak, amelyek mindegyike 15 címkenév/érték párral rendelkezik. Ha több mint 15 értéket kell társítania egy erőforráshoz, használjon JSON-sztringet a címke értékéhez. A JSON-sztring sok olyan értéket tartalmazhat, amelyek egyetlen címkenévre vannak alkalmazva. Ez a cikk arra mutat be egy példát, hogyan rendelhető hozzá egy JSON-sztring a címkéhez.
* A címke neve legfeljebb 512 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet. A tárfiókok esetében a címke neve legfeljebb 128 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet.
* Virtuális gépek, legfeljebb 2048 karakter minden tartalmaznak a címkenevek és értékek összesen.
* Az egyes erőforráscsoportokra alkalmazott címkéket nem öröklik az adott erőforráscsoportba tartozó erőforrások.
* Klasszikus erőforrások, például a Cloud Services címkék nem alkalmazható.
* A címke neve nem tartalmazhatja a következő karaktereket: `<`, `>`, `%`, `&`, `\`, `?`, `/`
