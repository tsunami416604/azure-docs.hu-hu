---
title: Ajánlott eljárások – QnA Maker
titlesuffix: Azure Cognitive Services
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
ms.openlocfilehash: dd4f85822a5e6615e7ea6e31b4231c04c9d4e88c
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542855"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Ajánlott eljárások a QnA Maker Tudásbázis

A [Tudásbázis fejlesztési életciklus](../Concepts/development-lifecycle-knowledge-base.md) végigvezeti Önt a a KB-os kezelése az elejétől a végéig. Ajánlott eljárások használatával javíthatja a tudásbázist és biztosít az ügyfélalkalmazásnak jobb eredmények vagy csevegés robot a végfelhasználók számára.

## <a name="extraction"></a>Kinyerés

A QnA Maker szolgáltatást folyamatosan javul a algoritmusokat használnak a QnA-tudásbázisok kinyerése a tartalom és a támogatott fájl-és HTML formátumú bővítését. Kövesse a [irányelvek](../Concepts/data-sources-supported.md) az adatok kinyerése a dokumentum típusa alapján. 

Gyakori kérdéseket tartalmazó oldalak általában az önálló és egyéb információkat a nem összevont kell lennie. Kézikönyvek egyértelmű címsorok és lehetőleg index lapot kell rendelkeznie. 

### <a name="configuring-multi-turn"></a>Több kapcsolja konfigurálása

Hozzon létre a Tudásbázis több kapcsolja kinyerési engedélyezve van. A Tudásbázis nem és támogatnia kell a kérdés hierarchia, ha ezt a hierarchiát a dokumentum kinyert vagy létrehozása után ki kell olvasni a dokumentumot. 

<!--is this a global setting that can only be configured at kb creation time? -->

## <a name="creating-good-questions-and-answers"></a>Jó kérdések és válaszok létrehozásával

### <a name="good-questions"></a>Jó kérdések

A legjobb kérdések egyszerűek. Fontolja meg a kulcs szót vagy kifejezést az összes kérdést, majd hozza létre az adott kulcsszót vagy kifejezést a kérdés. 

Adjon hozzá annyi másodlagos kérdések van szükség, de egyszerű megtartani a módosításokat. További szavakat vagy szinonimákkal, amelyek nem szerepelnek a fő cél a kérdés hozzáadása nem működik a QnA Maker egyezést talál. 


### <a name="add-relevant-alternative-questions"></a>Alternatív kérdéseket hozzáadása

A felhasználó lehet, hogy adja meg a kérdéseket természetes nyelvi stílus, szöveg `How do I add a toner cartridge to my printer?` vagy egy kulcsszavas keresést például `toner cartridge`. A Tudásbázis kell rendelkeznie mindkét stílusok kérdések biztosítása érdekében megfelelően a legjobb választ. Ha nem tudja, milyen ügyfél lépjen be kulcsszavakat, az Application Insights-adatok segítségével elemezheti a lekérdezések.

### <a name="good-answers"></a>Helyes válasz

A legjobb válaszokat már az egyszerű, de nem túl egyszerű. Például ne használjon válaszokat `yes` és `no`. Ha a válasz kell hivatkozni a más forrásokból, vagy egy gazdag felhasználói élményt biztosítson adathordozó és a hivatkozások, [címkézési metaadatokat](./knowledge-base.md#key-knowledge-base-concepts) megkülönböztetni a válaszokat, majd [elküldheti a lekérdezést a](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) a metaadat-címkéket a a`strictFilters` tulajdonság a helyes válasz verzió beszerzéséhez.

## <a name="chit-chat"></a>Csevegési Chit
A robot, hogy a robot természetes nyelvi, és vonzó, chit csevegési hozzá-kis munkamennyiség. Egyszerűen adja hozzá a csevegési chit adatkészletek az előre definiált személyiséghez a KB-os létrehozásakor, és bármikor módosíthatja őket. Ismerje meg, hogyan [chit csevegési ad hozzá a KB-os](../How-To/chit-chat-knowledge-base.md). 

### <a name="choosing-a-personality"></a>Egy személy kiválasztása
Számos előre definiált személyiséghez Chit csevegési támogatott: 

|Személy |A QnA Maker adatkészlet fájl |
|---------|-----|
|Professional |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Rövid |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Okos |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Ápolásáért |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Lelkes |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

A válaszok köre a formális informális és irreverent. Akkor válassza a személy, amely legközelebb eső igazodik a képviselő hangvételét robotjait használni szeretne. Megtekintheti a [adatkészletek](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets), és válasszon, amely a robot alapként szolgál, és azután testre szabhatják a válaszokat. 

### <a name="edit-bot-specific-questions"></a>A robot-specifikus kérdések szerkesztése
Nincsenek robot-specifikus kérdésekre kaphat választ a csevegési chit adatkészlet részét képezik, és általános válaszokat kitöltötte. Ezek a válaszok robot adatait legjobban megfelelően módosítsa. 

Ennek végrehajtása a következő chit csevegési QnA-tudásbázisok pontosabb ajánlott:

* Ki Ön?
* Mire használható?
* Mennyi idős Ön?
* Ki hozta létre?
* Üdvözöljük!
   
### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>A metaadat-kódcímke a egyéni chit Csevegés hozzáadása

Ha a saját chit csevegési QnA párok ad hozzá, ügyeljen arra, hogy adja hozzá a metaadatokat, hogy ezek a válaszok adja vissza. A metaadat-név-érték pár `editorial:chitchat`.

## <a name="searching-for-answers"></a>Válaszok keresése

GenerateAnswer API kérdéseket és a válasz is használja a felhasználó lekérdezése a legjobb válaszokat kereshet.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Keresés a kérdések, csak akkor, ha a válasz nem megfelelő

Használja a [ `RankerType=QuestionOnly` ](#choosing-ranker-type) Ha nem szeretné, keressen válaszokat. 

Példa erre a Tudásbázis esetén egy katalógus, betűszavakat kérdéseket, válaszként a teljes forma van. A válasz értékét nem segít, keresse meg a megfelelő választ.

## <a name="rankingscoring"></a>Ennek a területnek/pontozási
Győződjön meg arról, amelyek a lehető legjobban kihasználják támogatja a QnA Maker rangsorolási szolgáltatásokat. Ezzel javul a valószínűsége, hogy egy adott felhasználó lekérdezési válasz érkezik a megfelelő választ.

### <a name="choosing-a-threshold"></a>Egy küszöbértéket kiválasztása

Az alapértelmezett [megbízhatósági pontszám](confidence-score.md) használt, egy küszöbértéket 50, azonban az is [küszöbértékének módosítása](confidence-score.md#set-threshold) a KB igényei alapján. Mivel minden KB különböző, teszteléséhez, és válassza ki a küszöbérték, amely a legjobb megoldás az olyan adatbázisoknál a KB. 

### <a name="choosing-ranker-type"></a>Rangsorolás típusának kiválasztása
Alapértelmezés szerint a QnA Maker – kérdések és válaszok keres. Ha azt szeretné, csak a kérdések keresgélnie, készítése a választ valamely kérdésre, használja a `RankerType=QuestionOnly` a GenerateAnswer kérelem bejegyzés törzse.

### <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása
[Kérdések az eredetitől eltérő](../How-To/edit-knowledge-base.md) felhasználói lekérdezés egyezést valószínűségének növelése. Alternatív kérdések akkor hasznos, ha több módon is ugyanezt a kérdést kérni. Ezek lehetnek a módosítások a mondatok és a word-stílusú.

|Eredeti lekérdezés|Alternatív lekérdezéseket|Módosítás| 
|--|--|--|
|Az ideiglenes elérhető?|Autó park van?|mondatok|
 |szia|Yo<br>Hey van!|a Word-stílus és szleng|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Használja a metaadat-címkéket a szűrő kérdések és válaszok

[Metaadatok](../How-To/edit-knowledge-base.md) ad arra, hogy az ügyfélalkalmazás ismeri, nem hajtson végre minden válaszokat hanem egy metaadat-címkéket alapján felhasználói lekérdezési eredmények szűkítéséhez. A Tudásbázis választ a metaadat-kódcímke alapján is eltérnek, akkor is, ha a lekérdezés nem ugyanaz. Például *"hol is található ideiglenes"* egy másik választ is rendelkezhet, ha az éttermi ág helye eltérő –, hogy a metaadatok van *helye: Seattle* és *helye: Redmond*.

### <a name="use-synonyms"></a>A szinonimák használata
Míg néhány támogatás angol nyelven szinonimák, használja a kis-és word változásokból keresztül a [változásokból API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) szinonimák hozzáadása, amelyek különböző kulcsszavakat. Szinonimák szintjén a QnA Maker szolgáltatás hozzáadva, és a szolgáltatás által az összes tudásbázisok megosztott.

|Eredeti word|Szinonimák|
|--|--|
|Vásárlás|vásároljon<br>NET-banki<br>nettó banki|

### <a name="use-distinct-words-to-differentiate-questions"></a>Önálló szavak használatával tesz különbséget a kérdések
A QnA Maker rangsorolási algoritmust, egy felhasználói lekérdezés egy kérdést a Tudásbázis megfelelő akkor működik a legjobban, ha minden kérdés-címek egy másik szükséges. Ismétlődő kérdések között ugyanazon szó csökkenti annak valószínűsége, hogy a helyes válasz egy adott felhasználó lekérdezés azokat a szavakat az van kiválasztva. 

Előfordulhat például, hogy két külön QnA-tudásbázisok az az alábbi kérdésekre:

|QnA-tudásbázisok|
|--|
|hol található az ideiglenes *helye*|
|hol található a ATM *helye*|

E két QnA-tudásbázisok vannak phrased nagyon hasonló szavakat tartalmaznak, mivel a hasonlóság okozhat számos felhasználói lekérdezések, amelyek például a rendszer phrased nagyon hasonló pontszámok *"hol van a `<x>` helye"* . Ehelyett a lekérdezésekkel például jól megkülönböztethető próbál *"hol található az ideiglenes sok"* és *"hol található a ATM"* , lehetőleg ne szavakat, például a "hely", amely csak a sok kérdések a KB-ban. 

## <a name="collaborate"></a>Együttműködés
A QnA Maker lehetővé teszi a felhasználóknak [együttműködés](../How-to/collaborate-knowledge-base.md) a Tudásbázis. Felhasználók az Azure QnA Maker erőforráscsoport hozzá kell férniük ahhoz, hogy hozzáférhessen a tudásbázisok. Egyes szervezetek érdemes kiszervezik a Tudásbázis szerkesztéséhez és a karbantartás, és továbbra is felhasználhatják az Azure-erőforrásokhoz való hozzáférés védelme. Ez a szerkesztő, jóváhagyó modell végzi el beállítása két azonos [QnA Maker szolgáltatás](../How-to/set-up-qnamaker-service-azure.md) különböző előfizetésekben találhatóak, és ha kiválaszt egy, a Szerkesztés tesztelés ciklusig az újbóli. Tesztelés befejezése után a Tudásbázis-tartalmat a átkerülnek egy [az import-export](../Tutorials/migrate-knowledge-base.md) feldolgozása a QnA Maker szolgáltatás a jóváhagyó, végül pedig a Tudásbázis közzététele, és a végpont frissítéséhez.



## <a name="active-learning"></a>Aktív tanulás

[Aktív tanulás](../How-to/improve-knowledge-base.md) does alternatív kérdések javasolása széles minőségét és a felhasználó-alapú lekérdezések mennyisége, ha a legjobb feladat. Fontos, hogy az ügyfél-alkalmazások felhasználói lekérdezések részt az aktív tanulás visszajelzési ciklus ellenőrzéstől nélkül. Kérdések a QnA Maker Portal használata javasolt, ha is **[javaslatok szűrés](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** , majd tekintse át és fogadja el vagy elutasíthatja a szólhatnak. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis szerkesztése](../How-to/edit-knowledge-base.md)
