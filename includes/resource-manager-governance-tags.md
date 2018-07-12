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
ms.openlocfilehash: 69c67f437d2f0b7bd6c1f5311eb5ba1d962d889a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38738879"
---
Az Azure-erőforrások címkézésével logikus elv mentén rendszerezheti azokat, kategóriák szerint. Minden címke egy névből és egy értékből áll. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

A címkék alkalmazása után az előfizetés összes erőforrását lekérheti az adott címke nevével és értékével. A címkék lehetővé teszi különböző erőforráscsoportokban kapcsolódó erőforrások lekérése. Ez a megoldás akkor hasznos, ha számlázás vagy felügyelet céljából kell rendszereznie erőforrásait.

Az alábbi korlátozások érvényesek a címkékre:

* Minden egyes erőforrás vagy erőforráscsoport legfeljebb 15 címkenév/érték párral rendelkezhet. A korlátozás csak a közvetlenül az erőforráscsoportra vagy erőforrásra alkalmazott címkékre érvényes. Az erőforráscsoportok sok olyan erőforrást tartalmazhatnak, amelyek mindegyike 15 címkenév/érték párral rendelkezik. Ha több mint 15 értéket kell társítania egy erőforráshoz, használjon JSON-sztringet a címke értékéhez. A JSON-sztring sok olyan értéket tartalmazhat, amelyek egyetlen címkenévre vannak alkalmazva. Ez a cikk arra mutat be egy példát, hogyan rendelhető hozzá egy JSON-sztring a címkéhez.
* A címke neve legfeljebb 512 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet. A tárfiókok esetében a címke neve legfeljebb 128 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet.
* Az egyes erőforráscsoportokra alkalmazott címkéket nem öröklik az adott erőforráscsoportba tartozó erőforrások.
* Klasszikus erőforrások, például a Cloud Services címkék nem alkalmazható.
* A címke neve nem tartalmazhatja a következő karaktereket: `<`, `>`, `%`, `&`, `\`, `?`, `/`
