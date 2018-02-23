---
title: "fájl belefoglalása"
description: "fájl belefoglalása"
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 13aa40331849c775898913129f8048a06a1f8456
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
Az Azure-erőforrások címkézésével logikus elv mentén rendszerezheti azokat, kategóriák szerint. Minden címke egy névből és egy értékből áll. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

A címkék alkalmazása után az előfizetés összes erőforrását lekérheti az adott címke nevével és értékével. Címkék lehetővé teszik a különböző erőforráscsoportokra kapcsolódó erőforrások lekérése. Ez a megoldás akkor hasznos, ha számlázás vagy felügyelet céljából kell rendszereznie erőforrásait.

Az alábbi korlátozások érvényesek a címkékre:

* Minden egyes erőforrás vagy erőforráscsoport legfeljebb 15 címkenév/érték párral rendelkezhet. A korlátozás csak a közvetlenül az erőforráscsoportra vagy erőforrásra alkalmazott címkékre érvényes. Az erőforráscsoportok sok olyan erőforrást tartalmazhatnak, amelyek mindegyike 15 címkenév/érték párral rendelkezik. Ha több mint 15 értéket kell társítania egy erőforráshoz, használjon JSON-karakterláncot a címke értékéhez. A JSON-karakterlánc sok olyan értéket tartalmazhat, amelyek egyetlen címkenévre vannak alkalmazva. Ez a cikk arra mutat be egy példát, hogyan rendelhető hozzá egy JSON-karakterlánc a címkéhez.
* A címke neve legfeljebb 512 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet. A tárfiókok esetében a címke neve legfeljebb 128 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet.
* Az egyes erőforráscsoportokra alkalmazott címkéket nem öröklik az adott erőforráscsoportba tartozó erőforrások.
* Hagyományos erőforrások, például a Cloud Services címkék nem alkalmazható.
* Ezek a karakterek nem támogatottak: `<`, `>`, `%`, `&`, `\\`, `?`, `/`
