---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 07/11/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 099bca7483100da1a4ee2f8f10057c416ad145b0
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841468"
---
Az Azure-erőforrások jogosultságot ad a metaadatok őket logikailag szervezheti a besorolás a címkékkel. Minden címke egy névből és a egy érték pár áll. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

A címkék alkalmazása után az előfizetés összes erőforrását lekérheti az adott címke nevével és értékével. A címkék lehetővé teszi különböző erőforráscsoportokban kapcsolódó erőforrások lekérése. Ez a megoldás akkor hasznos, ha számlázás vagy felügyelet céljából kell rendszereznie erőforrásait.

A besorolás figyelembe kell venni egy önkiszolgáló metaadatok címkézési stratégia mellett az Automatikus címkézés stratégia nagyobb pontosság és a felhasználók terhelésének csökkentése érdekében.

Az alábbi korlátozások érvényesek a címkékre:

* Nem minden erőforrástípus támogatja a címkék. Ha alkalmazhat egy címkét egy erőforrástípus megállapításához lásd: [címkézése Azure-erőforrások támogatása](../articles/azure-resource-manager/tag-support.md).
* Minden egyes erőforrás vagy erőforráscsoport legfeljebb 50 címkenév/érték párral rendelkezhet. Jelenleg a storage-fiókok csak 15 címkével, de ezt a korlátot a rendszer generál egy későbbi kiadásban 50-re. Ha további címkékkel, mint a maximálisan megengedett szám van szüksége, használja a JSON-karakterláncot a címke értéke. A JSON-sztring sok olyan értéket tartalmazhat, amelyek egyetlen címkenévre vannak alkalmazva. Egy erőforráscsoport tartalmazhatja, amelyek mindegyikére 50 címkenév/érték párral sok erőforrást.
* A címke neve legfeljebb 512 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet. A tárfiókok esetében a címke neve legfeljebb 128 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet.
* Általánosított virtuális gépek nem támogatják a címkéket.
* Az egyes erőforráscsoportokra alkalmazott címkéket nem öröklik az adott erőforráscsoportba tartozó erőforrások.
* Klasszikus erőforrások, például a Cloud Services címkék nem alkalmazható.
* A címke neve nem tartalmazhatja a következő karaktereket: `<`, `>`, `%`, `&`, `\`, `?`, `/`
