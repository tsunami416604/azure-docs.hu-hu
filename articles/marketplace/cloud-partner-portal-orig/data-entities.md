---
title: Adatok entitások |} A Microsoft Docs
description: Entitások adatainak áttekintése.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 579856ab87aaf8d051f2e3c161bb2d0e2f693ed5
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54446578"
---
# <a name="data-entities"></a>Adatok entitások

Ez a cikk határozza meg, és az adatentitások nyújt áttekintést. Az entitások képességeit, a forgatókönyvek, amelyek támogatják a, a kategóriákat, amelyek használják, és azok létrehozásának módszereivel kapcsolatos információkat tartalmaz.

## <a name="overview"></a>Áttekintés

Egy entitáshoz fizikai adatbázistáblák végrehajtásából absztrakciója. Például normalizált táblákban, előfordulhat, hogy egy vevő tábla tárolja szinte minden egyes ügyfél az adatokat, és ezután a többi egy kis készletét kapcsolódó táblák között lehetnek elosztva. Ebben az esetben egy denormalizált nézet, amelyben mindegyik sor tartalmazza az összes adatot az ügyfél-tábla és a kapcsolódó táblák az entitáshoz tartozó ügyfelek fogalmat jelenik meg. Egy entitáshoz olyan formátumra, amely megkönnyíti a fejlesztési és integrációs magában foglalja egy üzleti fogalom. Egy entitáshoz hálója jellege egyszerűbbé teheti az alkalmazások fejlesztése és testreszabása. Később a absztrakciós insulates származó a verziók közötti fizikai táblák elkerülhetetlen forgalommal is.

Összefoglalásképpen: Entitáshoz fogalmi absztrakciós és a beágyazás (denormalizált nézet) az alapul szolgáló táblasémákat, amelyek kulcsfontosságú fogalmakat és funkciókat biztosít.

## <a name="capabilities"></a>Funkciók

Egy entitáshoz a következő képességekkel rendelkezik:

- Lecseréli az AXD, adatok importálási/exportálási keretrendszer (DIXF) entitásokat, széttartó és töredezett fogalmait, és összesítést egyetlen fogalma a lekérdezi.
- Biztosít egy egyetlen verem rögzítheti az üzleti logikát, és például importálás/exportálás, integráció és programozhatóság forgatókönyvek megvalósítását teszik lehetővé.
- Application Lifecycle Management (ALM) és a bemutató forgatókönyvek csomagok adatok importálása és exportálása az elsődleges mechanizmusa válik.
- Azt is OData-szolgáltatásaival néven jelenik meg, és akkor használja a táblázatos stílusú szinkron integrációs forgatókönyvek és a Microsoft Office integrációja.

Lásd: [Adatentitásai](https://docs.microsoft.com/dynamics365/operations/dev-itpro/data-entities/data-entities) további információ.
