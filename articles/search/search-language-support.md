---
title: Az Azure Search több nyelv indexelő |} A Microsoft Docs
description: Az Azure Search kihasználva a lucene-től és a természetes nyelvi feldolgozás, a Microsoft technológiai nyelvi elemzők 56 nyelvhez támogatja.
author: yahnoosh
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: jlembicz
ms.openlocfilehash: 38f93f5415282d2f976d9f3acc2b0a7aeead6c3d
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35926156"
---
# <a name="create-an-index-for-documents-in-multiple-languages-in-azure-search"></a>Az Azure Search több nyelven elérhető dokumentumok indexének létrehozása
> [!div class="op_single_selector"]
>
> * [Portál](search-language-support.md)
> * [REST](https://msdn.microsoft.com/library/azure/dn879793.aspx)
> * [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)
>
>

A naplóelemzésben rejlő nyelvi elemzők az olyan egyszerű, mintha a beállítás egy tulajdonsága az Indexdefiníció egy kereshető mező. Most már teheti meg ezt a lépést, a portálon.

Az Azure Search, amelyek lehetővé teszik a felhasználók számára az indexséma határozza meg az alábbiakban az Azure Portal paneljeinek pillanatképeiért. Ezen a panelen a felhasználók létrehozásához minden mezőnek és a analyzer tulajdonsága mindegyikük számára.

> [!IMPORTANT]
> Csak akkor állíthat egy nyelvi elemzőt során mező definíciót, mint a be egy új index az alapoktól létrehozásakor, vagy ha egy új mezőt ad hozzá egy meglévő index. Ellenőrizze, hogy teljes mértékben adja meg az összes attribútum, az elemző, beleértve a mező létrehozása során. Nem sikerült az attribútumok szerkesztése vagy elemző típusának módosítása után a módosítások mentéséhez.
>
>

## <a name="define-a-new-field-definition"></a>Új mező-definíció megadása
1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és nyissa meg a keresési szolgáltatásnak az szolgáltatás panelje.
2. Kattintson a **index hozzáadása** a parancssorban a szolgáltatás irányítópultján egy új index indításra, vagy nyisson meg egy elemző beállításához adja hozzá egy meglévő indexhez új mezőket a meglévő index tetején.
3. A mezők panel jelenik meg, a sémát az index definiálása lehetőségeit többek között az elemző lapon használt nyelvi elemző kiválasztása.
4. A mezőbe indítsa el mező definícióját egy nevet, az adattípus kiválasztása és megjelölése a mező teljes szöveges kereshető, lekérdezhető a keresési eredmények között, használható értékkorlátozó navigációs struktúrát, rendezhető és egyebekben attribútum.
5. Mielőtt továbblép a következő mezőre, nyissa meg a **Analyzer** fülre.

![][1]
*Egy elemző kiválasztásához kattintson az elemző lapon, a mezők panelen*

## <a name="choose-an-analyzer"></a>Válasszon egy elemző
1. Megadja a mező megtekintéséhez görgessen.
2. Ha a mező kereshető, még nem jelölt meg, kattintson a jelölőnégyzetre most, és jelölje meg **kereshető**.
3. Kattintson az elemző területen elérhető elemzők listájának megjelenítéséhez.
4. Válassza ki a használni kívánt elemzőt.

![][2]
*Válasszon ki egy minden mező esetében a támogatott elemzők*

Alapértelmezés szerint az összes kereshető mezőt használja a [Standard Lucene-elemzőt](http://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) azaz nyelvtől. Támogatott elemzők teljes listájának megtekintéséhez: [nyelvi támogatás az Azure Search](https://msdn.microsoft.com/library/azure/dn879793.aspx).

Miután a nyelvi elemző mező van kiválasztva, akkor együtt használható indexelés és keresés kéréseknek erre a mezőre. Egy lekérdezés segítségével különböző elemzők több mező alapján kiadásakor a lekérdezés által a megfelelő elemzők az egyes mezőkhöz tartozó egymástól függetlenül lesz feldolgozva.

Számos webes és mobilalkalmazások kiszolgálni a világ minden pontján, több különböző nyelvet használó felhasználók. Ilyen forgatókönyv esetén index definiálása az által támogatott egyes nyelvekhez tartozó mezők létrehozása, lehetőség.

![][3]
*Index definíciója támogatott egyes nyelvekhez tartozó mező leírása*

Az ügynök kiállító lekérdezés nyelvének ismert, ha egy keresési kérelmet hatóköre egy adott mezőben az a **searchFields** lekérdezési paraméter. Csak a leírást, a Lengyel szemben állít ki a következő lekérdezést:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2017-11-11`

Lekérdezheti az indexet a portál használatával **keresési ablak** hasonló a fenti lekérdezést beilleszteni. A keresési ablak a parancssávon, a szolgáltatási panelen érhető el. Lásd: [a portálon az Azure Search-index lekérdezése](search-explorer.md) részleteiről.

Időnként az ügynök kiállító lekérdezés nyelve nem ismert, ebben az esetben a lekérdezés adhatók ki mező alapján egyszerre. Ha szükséges, eredményeket egy bizonyos nyelvi beállításait is definiálhatók [pontozási profilok](https://msdn.microsoft.com/library/azure/dn798928.aspx). Az alábbi példában angolul leírásában található egyezés pontszáma lesz újabb egyezés, lengyel és francia viszonyítva:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2017-11-11`

Ha Ön a .NET-fejlesztők, vegye figyelembe, hogy a nyelvi elemzőkkel használatával konfigurálhatja a [Azure Search .NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Search). A legújabb verziót, valamint a Microsoft nyelvi elemzők támogatását tartalmazza.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png
