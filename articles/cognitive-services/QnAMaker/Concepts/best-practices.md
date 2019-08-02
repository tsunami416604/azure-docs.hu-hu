---
title: Ajánlott eljárások – QnA Maker
titleSuffix: Azure Cognitive Services
description: Ajánlott eljárások használatával növelheti a Tudásbázis, és jobb eredményeket nyújt az application/csevegőrobot a végfelhasználók számára.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: bbd6c55337eb30c883ecbd542abbdfc0f842e3f9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563128"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Ajánlott eljárások a QnA Maker Tudásbázis

A [Tudásbázis fejlesztési életciklus](../Concepts/development-lifecycle-knowledge-base.md) végigvezeti Önt a a KB-os kezelése az elejétől a végéig. Az ajánlott eljárások segítségével javíthatja a tudásbázist, és jobb eredményeket biztosíthat az ügyfélalkalmazás vagy a csevegő robot végfelhasználói számára.

## <a name="extraction"></a>Kinyerés

A QnA Maker szolgáltatást folyamatosan javul a algoritmusokat használnak a QnA-tudásbázisok kinyerése a tartalom és a támogatott fájl-és HTML formátumú bővítését. Kövesse a [irányelvek](../Concepts/data-sources-supported.md) az adatok kinyerése a dokumentum típusa alapján. 

Gyakori kérdéseket tartalmazó oldalak általában az önálló és egyéb információkat a nem összevont kell lennie. Kézikönyvek egyértelmű címsorok és lehetőleg index lapot kell rendelkeznie. 

### <a name="configuring-multi-turn"></a>Többszörös kapcsolás konfigurálása

Hozzon létre egy tudásbázist, és engedélyezze a többszörös kibontást. Ha a Tudásbázis nem rendelkezik vagy támogatnia kell a kérdés-hierarchiát, ez a hierarchia kinyerhető a dokumentumból, vagy a dokumentum kinyerése után hozható létre. 

<!--is this a global setting that can only be configured at kb creation time? -->

## <a name="creating-good-questions-and-answers"></a>Jó kérdések és válaszok létrehozása

### <a name="good-questions"></a>Jó kérdés

A legjobb kérdések egyszerűek. Vegye figyelembe az egyes kérdésekhez tartozó kulcsszót vagy kifejezést, majd hozzon létre egy egyszerű kérdést az adott kulcsszóhoz vagy kifejezéshez. 

Vegyen fel annyi alternatív kérdést, amennyire szüksége van, de a változtatások egyszerűek maradnak. Ha további szavakat vagy szövegezéseket ad hozzá, amelyek nem részei a kérdés fő célnak, nem segít QnA Maker találatok megtalálásában. 


### <a name="add-relevant-alternative-questions"></a>Adjon hozzá kapcsolódó alternatív kérdéseket

A felhasználó kérdéseket is megadhat egy szöveges szöveggel vagy egy kulcsszavas `How do I add a toner cartridge to my printer?` kereséssel, `toner cartridge`például:. A Tudásbázisnak mindkét típusú kérdéssel kell rendelkeznie ahhoz, hogy helyesen visszaállítsa a legjobb választ. Ha nem tudja biztosan, hogy az ügyfél mely kulcsszavakat írja be, használja a Application Insights adatokat a lekérdezések elemzéséhez.

### <a name="good-answers"></a>Helyes válaszok

A legjobb válaszok az egyszerű válaszok, de nem túl egyszerűek. Ne használjon olyan válaszokat, mint `yes` a `no`és a. Ha a válasznak más forrásokhoz kell kapcsolódnia, vagy az adathordozóval és a hivatkozásokkal gazdag élményt szeretne biztosítani, használja a [metaadatok címkézését](./knowledge-base.md#key-knowledge-base-concepts) a válaszok megkülönböztetéséhez, majd `strictFilters` [küldje el a lekérdezést](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) a tulajdonságban található metaadat-címkékkel a megfelelő válasz beszerzéséhez. verziója.

## <a name="chit-chat"></a>Csevegési Chit
A robot, hogy a robot természetes nyelvi, és vonzó, chit csevegési hozzá-kis munkamennyiség. A KB létrehozásakor könnyedén hozzáadhat a Chit-Chat-adatkészleteket az előre definiált személyes adatokból, és bármikor módosíthatja azokat. Ismerje meg, hogyan [chit csevegési ad hozzá a KB-os](../How-To/chit-chat-knowledge-base.md). 

### <a name="choosing-a-personality"></a>Egy személy kiválasztása
A Chit-Chat több előre definiált személy számára is támogatott: 

|Személyiség |Adatkészlet-fájl QnA Maker |
|---------|-----|
|Professional |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Rövid |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Szellemes |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Gondoskodó |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Lelkes |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

A válaszok köre a formális informális és irreverent. Akkor válassza a személy, amely legközelebb eső igazodik a képviselő hangvételét robotjait használni szeretne. Megtekintheti az [](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)adatkészleteket, és kiválaszthatja az egyiket, amely a robot alapjául szolgál, majd testreszabhatja a válaszokat. 

### <a name="edit-bot-specific-questions"></a>A robot-specifikus kérdések szerkesztése
Nincsenek robot-specifikus kérdésekre kaphat választ a csevegési chit adatkészlet részét képezik, és általános válaszokat kitöltötte. Ezek a válaszok robot adatait legjobban megfelelően módosítsa. 

Ennek végrehajtása a következő chit csevegési QnA-tudásbázisok pontosabb ajánlott:

* Ki Ön?
* Mire használható?
* Mennyi idős Ön?
* Ki hozta létre?
* Üdvözöljük!
   
### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Egyéni Chit-Chat hozzáadása metaadat-címkével

Ha saját Chit-csevegési QnA-párokat ad hozzá, ügyeljen arra, hogy metaadatokat adjon hozzá, így ezek a válaszok visszakerülnek. A metaadatok neve/értéke pár `editorial:chitchat`.

## <a name="searching-for-answers"></a>Válaszok keresése

A GenerateAnswer API mindkét kérdést és a választ használja a felhasználó lekérdezésére való legjobb válaszok keresésére.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Kérdések keresése csak akkor, ha a válasz nem releváns

Ha nem [`RankerType=QuestionOnly`](#choosing-ranker-type) szeretne válaszokat keresni, használja a következőt:. 

Ilyen például, ha a Tudásbázis a betűszók katalógusa, amely a válaszként megjelenő teljes űrlappal kapcsolatos kérdés. A válasz értéke nem fog segíteni a megfelelő válasz megkeresésében.

## <a name="rankingscoring"></a>Ennek a területnek/pontozási
Győződjön meg arról, amelyek a lehető legjobban kihasználják támogatja a QnA Maker rangsorolási szolgáltatásokat. Ezzel javul a valószínűsége, hogy egy adott felhasználó lekérdezési válasz érkezik a megfelelő választ.

### <a name="choosing-a-threshold"></a>Egy küszöbértéket kiválasztása

A küszöbértékként használt alapértelmezett [megbízhatósági pontszám](confidence-score.md) 50, azonban az igényeinek megfelelően [módosíthatja a](confidence-score.md#set-threshold) KB-os küszöbértéket. Mivel minden KB különböző, teszteléséhez, és válassza ki a küszöbérték, amely a legjobb megoldás az olyan adatbázisoknál a KB. 

### <a name="choosing-ranker-type"></a>A ranker típusának kiválasztása
Alapértelmezés szerint a QnA Maker kérdésekkel és válaszokkal keres. Ha csak kérdésekkel szeretne keresni, válasz létrehozásához használja a `RankerType=QuestionOnly` GenerateAnswer kérelem post törzsében.

### <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása
[Kérdések az eredetitől eltérő](../How-To/edit-knowledge-base.md) felhasználói lekérdezés egyezést valószínűségének növelése. Alternatív kérdések akkor hasznos, ha több módon is ugyanezt a kérdést kérni. Ezek lehetnek a módosítások a mondatok és a word-stílusú.

|Eredeti lekérdezés|Alternatív lekérdezéseket|Módosítás| 
|--|--|--|
|Az ideiglenes elérhető?|Autó park van?|mondatok|
 |szia|Yo<br>Hey van!|a Word-stílus és szleng|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>A kérdések és válaszok szűrése metaadat-címkék használatával

A [metaadatok](../How-To/edit-knowledge-base.md) lehetővé teszik, hogy az ügyfélalkalmazás tudja, hogy nem fogadja el az összes választ, hanem a metaadat-címkék alapján leszűkíti a felhasználói lekérdezés eredményét. A Tudásbázis választ a metaadat-kódcímke alapján is eltérnek, akkor is, ha a lekérdezés nem ugyanaz. Ha például a *"hol van a parkolóban található"* , akkor lehet, hogy más válasz van, ha az éttermi ág helye eltérő – vagyis a *metaadatok helye: Seattle*ésLocation:  *Redmond*.

### <a name="use-synonyms"></a>A szinonimák használata
Míg az angol nyelv szinonimái is vannak, a kis-és nagybetűk megkülönböztetése az [Alters API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) -n keresztül a különböző űrlapokat használó kulcsszavak szinonimáinak hozzáadását is lehetővé teheti. A szinonimákat a szolgáltatás szintjén adja hozzá a rendszer, és a szolgáltatás minden tudásbázisa megosztja a QnA Maker.

|Eredeti word|Szinonimák|
|--|--|
|Vásárlás|vásároljon<br>NET – banki szolgáltatások<br>nettó banki|

### <a name="use-distinct-words-to-differentiate-questions"></a>Önálló szavak használatával tesz különbséget a kérdések
A QnA Maker rangsorolási algoritmusa, amely egy, a Tudásbázisban kérdéssel rendelkező felhasználói lekérdezésnek felel meg, akkor a legjobban működik, ha az egyes kérdések eltérő igényt mutatnak. Ismétlődő kérdések között ugyanazon szó csökkenti annak valószínűsége, hogy a helyes válasz egy adott felhasználó lekérdezés azokat a szavakat az van kiválasztva. 

Előfordulhat például, hogy két külön QnA-tudásbázisok az az alábbi kérdésekre:

|QnA-tudásbázisok|
|--|
|hol található az ideiglenes *helye*|
|Hol található az ATM *helye*|

E két QnA-tudásbázisok vannak phrased nagyon hasonló szavakat tartalmaznak, mivel a hasonlóság okozhat számos felhasználói lekérdezések, amelyek például a rendszer phrased nagyon hasonló pontszámok *"hol van a `<x>` helye"* . Ehelyett próbálja meg egyértelműen megkülönböztetni a lekérdezéseket, például *a "hol van a parkolóban"* és *"hol van az ATM"* , a "location" kifejezéssel, amely a KB-ban sok kérdésben lehet. 

## <a name="collaborate"></a>Együttműködés
A QnA Maker lehetővé teszi a felhasználóknak [együttműködés](../How-to/collaborate-knowledge-base.md) a Tudásbázis. Felhasználók az Azure QnA Maker erőforráscsoport hozzá kell férniük ahhoz, hogy hozzáférhessen a tudásbázisok. Egyes szervezetek érdemes kiszervezik a Tudásbázis szerkesztéséhez és a karbantartás, és továbbra is felhasználhatják az Azure-erőforrásokhoz való hozzáférés védelme. Ez a szerkesztő, jóváhagyó modell végzi el beállítása két azonos [QnA Maker szolgáltatás](../How-to/set-up-qnamaker-service-azure.md) különböző előfizetésekben találhatóak, és ha kiválaszt egy, a Szerkesztés tesztelés ciklusig az újbóli. Tesztelés befejezése után a Tudásbázis-tartalmat a átkerülnek egy [az import-export](../Tutorials/migrate-knowledge-base.md) feldolgozása a QnA Maker szolgáltatás a jóváhagyó, végül pedig a Tudásbázis közzététele, és a végpont frissítéséhez.



## <a name="active-learning"></a>Aktív tanulás

Az [aktív tanulás](../How-to/improve-knowledge-base.md) a legjobb megoldás arra, hogy alternatív kérdéseket tegyen fel, ha számos minőségi és mennyiségű felhasználó-alapú lekérdezést tartalmaz. Fontos, hogy az ügyfélalkalmazások felhasználói lekérdezései részt vegyenek az aktív tanulási visszajelzési hurokban a cenzúra nélkül. Ha kérdése van a QnA Maker-portálon, akkor a **[javaslatok alapján szűrheti a javaslatokat](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** , majd áttekintheti, elfogadhatja vagy elutasíthatja ezeket a javaslatokat. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis szerkesztése](../How-to/edit-knowledge-base.md)
