---
title: "Az Azure Search több nyelv |} Microsoft Docs"
description: "Az Azure Search 56 nyelvet támogat, Lucene és természetes nyelvű feldolgozása technológia a Microsoft a nyelvi elemzőkkel kihasználva."
services: search
documentationcenter: 
author: yahnoosh
manager: pablocas
editor: 
ms.assetid: 55a00b44-804d-41bb-9c96-e6ea498616f5
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/23/2017
ms.author: jlembicz
ms.openlocfilehash: dbbab31bac66ce73dbf9883992713a2c16581e19
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="create-an-index-for-documents-in-multiple-languages-in-azure-search"></a>Több nyelven is az Azure Search-dokumentumok index létrehozása
> [!div class="op_single_selector"]
>
> * [Portál](search-language-support.md)
> * [REST](https://msdn.microsoft.com/library/azure/dn879793.aspx)
> * [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)
>
>

A nyelvi elemzőkkel hatványa unleashing lehető legkönnyebben beállítás több tulajdonsága az index definícióját kereshető mező. Most ezt a lépést, a portálon teheti meg.

Az Azure Search, amelyek lehetővé teszik a felhasználók a sémát indexeli az alábbiakban az Azure portál paneleken példaként bemutató képernyőképeket láthat. Ezen a panelen a felhasználók létrehozásához minden mezőnek és a analyzer tulajdonsága esetében.

> [!IMPORTANT]
> Csak beállíthat egy nyelvi elemzőt során mező definícióját, és a fel az alapoktól új index létrehozásakor, vagy ha új mező hozzáadása egy meglévő indexet. Ellenőrizze, hogy teljesen meg minden attribútumot az elemző eszközt, beleértve a mező létrehozása során. Ön nem fog tudni az attribútumok szerkesztése vagy analyzer típusának módosítása után a módosítások mentéséhez.
>
>

## <a name="define-a-new-field-definition"></a>Új mező definíció megadása
1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) , és nyissa meg a keresési szolgáltatás a szolgáltatás panelre.
2. Kattintson a **index hozzáadása** parancsra a parancssávon, indítsa el az új index, vagy nyissa meg a létező indexek egy analyzer beállítása a létező indexek visszaigazolása új mezőkre szolgáltatás irányítópultján tetején.
3. A mezők panel jelenik meg, az index sémájának definiálásához lehetőségeit a Analyzer is ide használt, egy nyelvi elemzőt kiválasztására.
4. A mezőket indítsa el mező definíciójának megadása, az adatok kiválasztása és attribútum megjelölése a mező teljes szöveges keresési eredmények között, használható értékkorlátozás navigációs szerkezetben lekérhető, kereshető rendezhető és stb.
5. Mielőtt továbblép a következő mezőre, nyissa meg a **Analyzer** fülre.

![][1]
*Egy elemző eszköz kiválasztásához kattintson a mezők panelen a Analyzer lap*

## <a name="choose-an-analyzer"></a>Válasszon egy elemző eszköz
1. Görgessen a meghatározásakor mező található.
2. Ha még nem kereshető, a mezőt, és jelölje meg kattintson a jelölőnégyzetbe **kereshető**.
3. Kattintson a Analyzer területre elérhető elemzőkkel listájának megjelenítéséhez.
4. Válassza ki a használni kívánt analyzer.

![][2]
*Válassza ki a támogatott elemzőkkel az egyes mezők*

Alapértelmezés szerint az összes kereshető mezőt használja a [szabványos Lucene analyzer](http://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) Ez az nyelvi független. Támogatott lekérdezések teljes listájának megtekintéséhez lásd: [nyelvi támogatás az Azure Search](https://msdn.microsoft.com/library/azure/dn879793.aspx).

Ha a nyelvi elemzőt mező nincs bejelölve, azt fogja használni minden egyes indexelési és keresési kérelemmel mező. Amikor a lekérdezés különböző lekérdezések használatával több mezőkkel, a lekérdezés által az egyes mezők a jobb oldali elemzőkkel egymástól függetlenül lesz feldolgozva.

Sok webes és mobilalkalmazásokhoz ellenőrizhető, hogy a felhasználók különböző nyelveken használó világ körüli. Is definiálhat ilyen forgatókönyvek index által támogatott egyes nyelvekhez tartozó mező létrehozása.

![][3]
*Minden támogatott nyelven Leírás mezőt Indexdefiníció*

Ha az ügynök kiállító lekérdezés nyelvének ismert, keresési kérelem hatóköre beállítható úgy, egy adott mező használatával a **searchFields** lekérdezési paraméter. A következő lekérdezést kap, csak a Lengyel leírását szemben:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2016-09-01`

Lekérheti az index a portálról használatával **keresési ablak** illessze be a fenti hasonló lekérdezésben. A parancssávon, a szolgáltatás panelen a keresési ablak érhető el. Lásd: [a portálon az Azure Search-index lekérdezése](search-explorer.md) részleteiről.

Egyes esetekben az ügynök kiállító lekérdezés nyelvének nem ismert, ebben az esetben a lekérdezés adhatók ki minden mezőkkel egyidejűleg. Ha szükséges, egy bizonyos nyelvi eredményez preferencia definiálható [profilok pontozási](https://msdn.microsoft.com/library/azure/dn798928.aspx). Az alábbi példában angolul a leírásban talált egyező fog pontozni magasabb megegyezik, lengyel és francia képest:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2016-09-01`

Ha a .NET-fejlesztők, vegye figyelembe, hogy a nyelvi elemzőkkel használatával konfigurálhatja a [Azure Search .NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Search). A legújabb verziót, valamint a Microsoft nyelvi elemzőkkel támogatását is magában foglalja.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png
