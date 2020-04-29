---
title: Hasonlóság és pontozás – áttekintés
titleSuffix: Azure Cognitive Search
description: Ismerteti a hasonlóság és a pontozás fogalmát, valamint azt, hogy mit tehet a fejlesztő a pontozási eredmények testreszabásához.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 9f9cc4c29b117c83595a36c4e28b1edb428c3cde
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254121"
---
# <a name="similarity-and-scoring-in-azure-cognitive-search"></a>Hasonlóság és pontozás az Azure Cognitive Search

A pontozás a teljes szöveges keresési lekérdezések keresési eredményei között visszaadott keresési pontszámok kiszámítására vonatkozik. A pontszám az elemek relevanciáját jelzi az aktuális keresési művelet kontextusában. Minél nagyobb a pontszám, annál relevánsabb az elem. A keresési eredmények között az elemek sorrendje magasról alacsonyra van rendezve, az egyes elemek kiszámított keresési pontszáma alapján. 

Alapértelmezés szerint a rendszer az első 50-as értéket adja vissza a válaszban, de a **$Top** paraméterrel kisebb vagy nagyobb számú elemet adhat vissza (legfeljebb 1000 egyetlen válaszban), és **$skip** a következő eredmények beszerzéséhez.

A keresési pontszám kiszámítása az adatok és a lekérdezés statisztikai tulajdonságai alapján történik. Az Azure Cognitive Search megkeresi azokat a dokumentumokat, amelyek megfelelnek a keresési kifejezéseknek ( [searchMode](https://docs.microsoft.com/rest/api/searchservice/search-documents#searchmodeany--all-optional)függően), a keresési kifejezés számos példányát tartalmazó dokumentumokat. A keresési pontszám még magasabbra nő, ha a kifejezés az adatindexben ritkán előfordul, de a dokumentumon belül is. A számítástechnikai szempontoknak való megfelelés alapja a *TF-IDF vagy* a kifejezés gyakorisága – fordított dokumentum gyakorisága.

A keresési pontszám értékei megismételhetők egy eredményhalmaz során. Ha több találat azonos keresési pontszámmal rendelkezik, az azonos pontszámú elemek sorrendje nincs definiálva, és nem stabil. Futtassa újra a lekérdezést, és előfordulhat, hogy az elemek eltolási pozíciója látható, különösen akkor, ha az ingyenes szolgáltatást vagy egy számlázható szolgáltatást több replikával használ. Az azonos pontszámú két elem esetében nincs garancia arra, hogy az egyik első megjelenjen.

Ha meg szeretné szüntetni a döntetlent az ismétlődő pontszámok között, hozzáadhat egy **$OrderBy** záradékot az első sorrend szerint a pontszám szerint, majd egy másik rendezhető mező szerint `$orderby=search.score() desc,Rating desc`(például:). További információ: [$OrderBy](https://docs.microsoft.com/azure/search/search-query-odata-orderby).

> [!NOTE]
> A `@search.score = 1.00` egy nem pontszámmal ellátható vagy nem rangsorolt eredményhalmaz. A pontszám egységes az összes eredményben. A nem pontozásos eredmények akkor fordulnak elő, ha a lekérdezési űrlap nem intelligens keresés, helyettesítő karakter vagy regex lekérdezés, vagy egy **$Filter** kifejezés. 

## <a name="scoring-profiles"></a>Pontozási profilok

Egyéni *pontozási profil*definiálásával testre szabhatja a különböző mezők rangsorolásának módját. A pontozási profilok nagyobb mértékben szabályozzák a keresési eredményekben lévő elemek rangsorolását. Előfordulhat például, hogy a bevételi potenciál alapján szeretné növelni az elemeket, előléptetheti az újabb elemeket, vagy növelheti az olyan elemeket, amelyek túl hosszúak voltak a leltárban. 

A pontozási profil az index definíciójának részét képezi, amely a súlyozott mezőkből, függvényekből és paraméterekből áll. A definiálásával kapcsolatos további információkért lásd: [pontozási profilok](index-add-scoring-profiles.md).

## <a name="scoring-statistics"></a>Pontozási statisztika

A méretezhetőség érdekében az Azure Cognitive Search horizontálisan osztja el az egyes indexeket egy horizontális Felskálázási folyamaton keresztül, ami azt jelenti, hogy az index részei fizikailag elkülönítve vannak.

Alapértelmezés szerint a rendszer a dokumentum pontszámát a szegmensen *belüli*adatstatisztikai tulajdonságok alapján számítja ki. Ez a megközelítés általában nem jelent problémát a nagy mennyiségű adat esetében, és jobb teljesítményt nyújt, mint a pontszám kiszámításához az összes szegmens információi alapján. Ez azt eredményezte, hogy ez a teljesítmény-optimalizálás két nagyon hasonló dokumentumot (vagy akár azonos dokumentumokat) is okozhat, amelyek a különböző szegmensekben való befejezéskor különböző releváns pontszámokkal rendelkeznek.

Ha az összes szegmens statisztikai tulajdonságai alapján szeretné kiszámítani a pontszámot, ezt a *scoringStatistics = Global* [lekérdezési paraméterként](https://docs.microsoft.com/rest/api/searchservice/search-documents) való hozzáadásával teheti meg (vagy a *"scoringStatistics": "Global"* értéket adja hozzá a [lekérdezési kérelem](https://docs.microsoft.com/rest/api/searchservice/search-documents)törzsének paraméteréhez).

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?scoringStatistics=global
  Content-Type: application/json
  api-key: [admin key]  
```

> [!NOTE]
> A `scoringStatistics` paraméterhez rendszergazdai API-kulcs szükséges.

## <a name="similarity-ranking-algorithms"></a>Hasonlósági rangsorolási algoritmusok

Az Azure Cognitive Search két különböző hasonlósági algoritmust támogat: egy *klasszikus hasonlósági* algoritmust és a *Okapi BM25* algoritmus hivatalos implementációját (jelenleg előzetes verzióban érhető el). A klasszikus hasonlósági algoritmus az alapértelmezett algoritmus, azonban a dátum után létrehozott új szolgáltatások az új BM25 algoritmust használják. Az új szolgáltatásokban ez lesz az egyetlen elérhető algoritmus.

Egyelőre megadhatja, hogy melyik hasonlósági rangsorolási algoritmust szeretné használni. További információ: [rangsorolási algoritmus](index-ranking-similarity.md).

## <a name="watch-this-video"></a>Videó megtekintése

Ebben a 16 perces videóban a Merouche-es informatikus mérnök ismerteti a pontozási profilok indexelésének, lekérdezésének és létrehozásának folyamatát. Jó képet nyújt arról, hogy mi történik a motorháztető alatt a dokumentumok indexelése és beolvasása során.

>[!VIDEO https://channel9.msdn.com/Shows/AI-Show/Similarity-and-Scoring-in-Azure-Cognitive-Search/player]

+ 2-3 perces indexelés: szövegszerkesztés és lexikális analízis.
+ 3-4 perces indexelés: invertált indexek.
+ 4-6 perces lekérdezés: lekérés és rangsorolás.
+ a 7-16 perc a pontozási profilokat tartalmazza.

## <a name="see-also"></a>Lásd még

 [Pontozási profilok](index-add-scoring-profiles.md) [REST API referenciája](https://docs.microsoft.com/rest/api/searchservice/)   
 [Dokumentumok keresése API](https://docs.microsoft.com/rest/api/searchservice/search-documents)   
 [Azure Cognitive Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
