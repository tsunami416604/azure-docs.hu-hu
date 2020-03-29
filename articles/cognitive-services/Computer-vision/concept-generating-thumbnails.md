---
title: Intelligens enciklopédiák - Computer Vision
titleSuffix: Azure Cognitive Services
description: A képek miniatűrök létrehozásához a Computer Vision API használatával kapcsolatos fogalmak.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4874910f37b49990a659b48af0cf27921c3fcd5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68945234"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>Intelligens enciklopédiák létrehozása a Computer Vision segítségével

A bélyegkép a kép csökkentett méretű ábrázolása. A miniatűrök a képek és egyéb adatok gazdaságosabb, elrendezésbarát abbéli ábrázolására szolgálnak. A Computer Vision API intelligens körülvágást és a kép átméretezését használja egy adott kép intuitív miniatűrök létrehozásához.

A Computer Vision miniatűr generációs algoritmus a következőképpen működik:

1. Távolítsa el a zavaró elemeket a képről, és azonosítsa a _kép_&mdash;azon részét, amelyben a fő objektum(ok) megjelennek.
1. Vágja le a képet az azonosított _érdeklődési terület_alapján.
1. Módosítsa a méretarányt úgy, hogy illeszkedjen a cél miniatűr méretéhez.

## <a name="area-of-interest"></a>Érdeklődési terület

Kép feltöltésekor a Computer Vision API elemzi azt, hogy meghatározza *az érdeklődési területet.* Ezután ezzel a területpel meghatározhatja a lemezkép körülvágását. A vágási művelet azonban mindig megfelel a kívánt méretaránynak, ha meg van adva.

Ugyanannak a *területnek* a nyers határolókeret-koordinátáit is lekaphatja, ha a **areaOfInterest** API-t hívja meg helyette. Ezután ezt az információt használhatja az eredeti kép módosításához, ahogy szeretné.

## <a name="examples"></a>Példák

A létrehozott miniatűr nagymértékben eltérhet attól függően, hogy mit ad meg a magassághoz, a szélességhez és az intelligens körülvágáshoz, ahogy az az alábbi képen látható.

![Hegyi kép a különböző vágási konfigurációk mellett](./Images/thumbnail-demo.png)

Az alábbi táblázat a Computer Vision által a példaképekhez létrehozott tipikus miniatűröket mutatja be. A bélyegképek egy megadott célmagassághoz és 50 képpont szélességhez készültek, és az intelligens körülvágás engedélyezve van.

| Kép | Miniatűr |
|-------|-----------|
|![Szabadtéri hegy napnyugtakor, egy személy sziluettjével](./Images/mountain_vista.png) | ![A szabadtéri hegy miniatűrje napnyugtakor, egy személy sziluettjével](./Images/mountain_vista_thumbnail.png) |
|![Fehér virág zöld háttérrel](./Images/flower.png) | ![Vision Analyze Virág miniatűr](./Images/flower_thumbnail.png) |
|![Egy nő a tetőn egy lakóház](./Images/woman_roof.png) | ![miniatűr egy nő a tetőn egy lakóház](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>További lépések

További információ a [képek címkézéséről](concept-tagging-images.md) és [a képek kategorizálásáról.](concept-categorizing-images.md)
