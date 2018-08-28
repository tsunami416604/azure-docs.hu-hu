---
title: Az Azure Data Catalog az adatforrások dokumentálása
description: Útmutató a cikk az Azure Data Catalogban az adategységek dokumentálása kiemelése.
services: data-catalog
author: spelluru
ms.author: spelluru
ms.assetid: 053b1701-b848-4ada-b726-6f485caa9961
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 4d3dfac425caf36e9ca9a4d3593bba336b18b7a3
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053759"
---
# <a name="document-data-sources"></a>Adatforrások dokumentálása
## <a name="introduction"></a>Bevezetés
**A Microsoft Azure Data Catalog** egy teljes körűen felügyelt felhőszolgáltatás, amely egy regisztrációs és felderítőrendszert biztosít a vállalati adatforrások. Más szóval **Azure Data Catalog** lényege segít az embereknek felderítését, *ismertetése*, és használhatják az adatforrásokat, és így a szervezetek hatékonyabban a meglévő adatokból.

Ha egy adatforrás regisztrálva **Azure Data Catalog**, másolt és a szolgáltatás indexeli metaadatait, de a történetet ott nem végződhet. **Az Azure Data Catalog** lehetővé teszi a felhasználók a saját teljes dokumentáció, leírhatja a használat és a gyakori alkalmazási esetei az adatforrás.

A [hogyan adatforrások ellátása megjegyzésekkel](data-catalog-how-to-annotate.md), azt ismerteti, hogy szakértői, aki ismeri az adatforrás is láthatja el jegyzetekkel azokat a címkék és a egy leírást. A **Azure Data Catalog** portál tartalmaz egy formázott szöveges szerkesztő, hogy a felhasználók adategységeket és tárolók teljes mértékben lehet dokumentálni. A szerkesztő bekezdés formázását, például fejlécek, szöveg formázása, listajeles listák készítéséhez, számozott listák és táblákat tartalmaz.

A címkék és leírások egyszerű jegyzetek kitűnően alkalmasak. Az adatfelhasználók jobb megértése érdekében egy adatforrás egy adatforrást, és üzleti forgatókönyvek használatának érdekében azonban szakértői biztosíthat teljes körű, részletes dokumentációt. Adatforrások dokumentálása könnyebbé vált. Válasszon ki egy adategységet, vagy a tárolót, és válassza a **dokumentáció**.

![](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Adategységek dokumentálása
Az előnye, hogy **Azure Data Catalog** dokumentáció lehetővé teszi, hogy a Data Catalog használatával egyben tartalomtárként az adategységeket. Részletes tartalom, amely leírja a tárolók és táblák feltárhatja. Ha már rendelkezik tartalmat egy másik tartalom adattár, például a SharePoint és a egy fájlmegosztást, az eszköz dokumentációs hivatkozásokat való hivatkozáshoz a meglévő tartalom adhat hozzá. Ez a funkció lehetővé teszi a meglévő dokumentumok könnyebben felfedezhetővé teheti.

> [!NOTE]
> Dokumentáció a search-index nem érhető el.
>
>

![](media/data-catalog-documentation/data-catalog-documentation2.png)

A dokumentáció szintjét a jellemzők és a egy tárolón belül táblaséma részletes leírását az eszközintelligencia adattároló érték leíró terjedhet. A dokumentáció szintjét kell meghatározni az üzleti igények szerint. De általában az alábbiakban néhány és dokumentálja az adategységeket a hátrányai:

* A dokumentum csak: az összes tartalom egy helyen, de előfordulhat, hogy nem rendelkezik a szükséges adatokat a felhasználók képesek tájékozott döntés.
* Csak a táblák dokumentálni: tartalmat adott az adott objektumhoz, de a felhasználók rendelkeznek dokumentumok több helyen.
* Dokumentum-tárolók és táblák: legátfogóbb módszert használja, de előfordulhat, hogy a dokumentumok több karbantartási használ.

## <a name="summary"></a>Összegzés
Az adatforrások címkézésével **Azure Data Catalog** egy narratíva az adategységekre vonatkozó hozhat létre a lehető legtöbb részletet van szüksége.  Hivatkozások segítségével kapcsolat egy meglévő content tárház, amely egyesíti a már meglévő docs és adategységeket tárolt tartalmakhoz. Miután a felhasználók a megfelelő adategységek felderítése, dokumentáció teljes készletét is rendelkeznek.
