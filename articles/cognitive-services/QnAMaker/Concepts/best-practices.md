---
title: Ajánlott eljárások – QnA Maker
description: Ajánlott eljárások használatával növelheti a Tudásbázis, és jobb eredményeket nyújt az application/csevegőrobot a végfelhasználók számára.
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: fb935aeed7b492a3a0c213d6d7166bd5d80144c1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389306"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Ajánlott eljárások a QnA Maker Tudásbázis

A [Tudásbázis fejlesztési életciklusa](../Concepts/development-lifecycle-knowledge-base.md) végigvezeti Önt a kb elejétől a végéig történő kezelésének lépésein. Az ajánlott eljárások segítségével javíthatja a tudásbázist, és jobb eredményeket biztosíthat az ügyfélalkalmazás vagy a csevegő robot végfelhasználói számára.

## <a name="extraction"></a>Kinyerés

A QnA Maker szolgáltatást folyamatosan javul a algoritmusokat használnak a QnA-tudásbázisok kinyerése a tartalom és a támogatott fájl-és HTML formátumú bővítését. Kövesse az adatbontásra vonatkozó [irányelveket](../Concepts/content-types.md) a dokumentum típusa alapján.

Gyakori kérdéseket tartalmazó oldalak általában az önálló és egyéb információkat a nem összevont kell lennie. Kézikönyvek egyértelmű címsorok és lehetőleg index lapot kell rendelkeznie.

### <a name="configuring-multi-turn"></a>Többszörös kapcsolás konfigurálása

[Hozzon létre egy tudásbázist](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure) , és engedélyezze a többszörös kibontást. Ha a Tudásbázis nem rendelkezik vagy támogatnia kell a kérdés-hierarchiát, ez a hierarchia kinyerhető a dokumentumból, vagy a dokumentum kinyerése után hozható létre.

## <a name="creating-good-questions-and-answers"></a>Jó kérdések és válaszok létrehozása

### <a name="good-questions"></a>Jó kérdés

A legjobb kérdések egyszerűek. Vegye figyelembe az egyes kérdésekhez tartozó kulcsszót vagy kifejezést, majd hozzon létre egy egyszerű kérdést az adott kulcsszóhoz vagy kifejezéshez.

Vegyen fel annyi alternatív kérdést, amennyire szüksége van, de a változtatások egyszerűek maradnak. Ha további szavakat vagy szövegezéseket ad hozzá, amelyek nem részei a kérdés fő célnak, nem segít QnA Maker találatok megtalálásában.


### <a name="add-relevant-alternative-questions"></a>Adjon hozzá kapcsolódó alternatív kérdéseket

A felhasználó kérdéseket is megadhat a szöveges szöveggel, `How do I add a toner cartridge to my printer?` vagy egy kulcsszavas kereséssel, például `toner cartridge`. A Tudásbázisnak mindkét típusú kérdéssel kell rendelkeznie ahhoz, hogy helyesen visszaállítsa a legjobb választ. Ha nem tudja biztosan, hogy az ügyfél mely kulcsszavakat írja be, használja a Application Insights adatokat a lekérdezések elemzéséhez.

### <a name="good-answers"></a>Helyes válaszok

A legjobb válaszok az egyszerű válaszok, de nem túl egyszerűek. Ne használjon olyan válaszokat, mint például a `yes` és a `no`. Ha a válasznak más forrásokhoz kell kapcsolódnia, vagy az adathordozóval és a hivatkozásokkal gazdag élményt szeretne biztosítani, használja a [metaadatok címkézését](../how-to/edit-knowledge-base.md#add-metadata) a válaszok megkülönböztetéséhez, majd [küldje el a lekérdezést](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) metaadatokkal a `strictFilters` tulajdonságban a megfelelő válasz verziójának lekéréséhez.

|Válasz|Follup-kérések|
|--|--|
|Kapcsolja ki a Surface laptopot a billentyűzet főkapcsoló gombjával.|* Key-kombinációk alvó állapotba, leállítás és újraindítás.<br>* A Surface laptop rendszerindításának menete<br>* Hogyan lehet módosítani a BIOS-t egy Surface laptop esetében<br>* Az alvó állapot, a leállítás és az újraindítás közötti különbségek|
|Az ügyfélszolgálat naponta 24 órán keresztül érhető el telefonon, Skype-on és szöveges üzenetben.|* Kapcsolattartási adatok a Sales szolgáltatáshoz.<br> * Office-és tárolási helyszínek és órák egy személyes látogatáshoz.<br> * Tartozékok egy Surface laptophoz.|

## <a name="chit-chat"></a>Csevegési Chit
A robot, hogy a robot természetes nyelvi, és vonzó, chit csevegési hozzá-kis munkamennyiség. A KB létrehozásakor könnyedén hozzáadhat a Chit-Chat-adatkészleteket az előre definiált személyes adatokból, és bármikor módosíthatja azokat. Megtudhatja, hogyan [adhat hozzá a kb-hoz a Chit-chatet](../How-To/chit-chat-knowledge-base.md).

A Chit-Chat [több nyelven](../how-to/chit-chat-knowledge-base.md#language-support)is támogatott.

### <a name="choosing-a-personality"></a>Egy személy kiválasztása
A Chit-Chat több előre definiált személy számára is támogatott:

|Személyiség |Adatkészlet-fájl QnA Maker |
|---------|-----|
|Professional |[qna_chitchat_professional. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Rövid |[qna_chitchat_friendly. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Szellemes |[qna_chitchat_witty. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Gondoskodó |[qna_chitchat_caring. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Lelkes |[qna_chitchat_enthusiastic. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

A válaszok köre a formális informális és irreverent. Akkor válassza a személy, amely legközelebb eső igazodik a képviselő hangvételét robotjait használni szeretne. Megtekintheti az [adatkészleteket](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets), és kiválaszthatja az egyiket, amely a robot alapjául szolgál, majd testreszabhatja a válaszokat.

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

Ha nem szeretne válaszokat keresni, használja a [`RankerType=QuestionOnly`](#choosing-ranker-type) .

Ilyen például, ha a Tudásbázis a betűszók katalógusa, amely a válaszként megjelenő teljes űrlappal kapcsolatos kérdés. A válasz értéke nem fog segíteni a megfelelő válasz megkeresésében.

## <a name="rankingscoring"></a>Ennek a területnek/pontozási
Győződjön meg arról, amelyek a lehető legjobban kihasználják támogatja a QnA Maker rangsorolási szolgáltatásokat. Ezzel javul a valószínűsége, hogy egy adott felhasználó lekérdezési válasz érkezik a megfelelő választ.

### <a name="choosing-a-threshold"></a>Egy küszöbértéket kiválasztása

A küszöbértékként használt alapértelmezett [megbízhatósági pontszám](confidence-score.md) 0, azonban az igényeinek megfelelően módosíthatja a KB-os [küszöbértéket](confidence-score.md#set-threshold) . Mivel minden KB különböző, teszteléséhez, és válassza ki a küszöbérték, amely a legjobb megoldás az olyan adatbázisoknál a KB.

### <a name="choosing-ranker-type"></a>A ranker típusának kiválasztása
Alapértelmezés szerint a QnA Maker kérdésekkel és válaszokkal keres. Ha csak kérdésekkel szeretne keresni, válasz létrehozásához használja a `RankerType=QuestionOnly` a GenerateAnswer kérelem POST törzsében.

### <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása
A [másodlagos kérdések](../How-To/edit-knowledge-base.md) javítják a felhasználói lekérdezésekkel való egyezés valószínűségét. Alternatív kérdések akkor hasznos, ha több módon is ugyanezt a kérdést kérni. Ezek lehetnek a módosítások a mondatok és a word-stílusú.

|Eredeti lekérdezés|Alternatív lekérdezéseket|Módosítás|
|--|--|--|
|Az ideiglenes elérhető?|Autó park van?|mondatok|
 |szia|Yo<br>Hey van!|a Word-stílus és szleng|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>A kérdések és válaszok szűrése metaadat-címkék használatával

A [metaadatok](../How-To/edit-knowledge-base.md) lehetővé teszik, hogy az ügyfélalkalmazás tudja, hogy nem fogadja el az összes választ, hanem a metaadat-címkék alapján leszűkíti a felhasználói lekérdezés eredményét. A Tudásbázis választ a metaadat-kódcímke alapján is eltérnek, akkor is, ha a lekérdezés nem ugyanaz. Ha például a *"hol van a parkolóban található"* , akkor lehet, hogy más válasz van, ha az éttermi ág helye eltérő – vagyis a metaadatok *helye: Seattle* és *Location: Redmond*.

### <a name="use-synonyms"></a>A szinonimák használata
Míg az angol nyelv szinonimái is vannak, a kis-és nagybetűk megkülönböztetése az [Alters API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) -n keresztül a különböző űrlapokat használó kulcsszavak szinonimáinak hozzáadását is lehetővé teheti. A szinonimákat a szolgáltatás szintjén adja hozzá a rendszer, és a szolgáltatás minden tudásbázisa megosztja a QnA Maker.

|Eredeti word|Szinonimák|
|--|--|
|Vásárlás|beszerzés<br>NET – banki szolgáltatások<br>nettó banki|

### <a name="use-distinct-words-to-differentiate-questions"></a>Önálló szavak használatával tesz különbséget a kérdések
A QnA Maker rangsorolási algoritmusa, amely egy, a Tudásbázisban kérdéssel rendelkező felhasználói lekérdezésnek felel meg, akkor a legjobban működik, ha az egyes kérdések eltérő igényt mutatnak. Ismétlődő kérdések között ugyanazon szó csökkenti annak valószínűsége, hogy a helyes válasz egy adott felhasználó lekérdezés azokat a szavakat az van kiválasztva.

Előfordulhat például, hogy két külön QnA-tudásbázisok az az alábbi kérdésekre:

|QnA-tudásbázisok|
|--|
|Hol található a parkoló *helye*|
|Hol található az ATM *helye*|

Mivel ez a két QnAs nagyon hasonló szavakkal van megfogalmazva, ez a hasonlóság nagyon hasonló pontszámokat okozhat számos olyan felhasználói lekérdezés esetében, amelyek *"hol van a `<x>` helye"* . Ehelyett próbálja meg egyértelműen megkülönböztetni a lekérdezéseket, például *a "hol van a parkolóban"* és *"hol van az ATM"* , a "location" kifejezéssel, amely a KB-ban sok kérdésben lehet.

## <a name="collaborate"></a>Együttműködés
QnA Maker lehetővé teszi a felhasználók számára, hogy a Tudásbázisban [működjenek együtt](../How-to/collaborate-knowledge-base.md) . Felhasználók az Azure QnA Maker erőforráscsoport hozzá kell férniük ahhoz, hogy hozzáférhessen a tudásbázisok. Egyes szervezetek érdemes kiszervezik a Tudásbázis szerkesztéséhez és a karbantartás, és továbbra is felhasználhatják az Azure-erőforrásokhoz való hozzáférés védelme. Ez a szerkesztő-jóváhagyó modell két azonos [QnA Maker-szolgáltatásnak](../How-to/set-up-qnamaker-service-azure.md) a különböző előfizetésekben való beállításával és a szerkesztési tesztelési ciklus egyikének kiválasztásával valósítható meg. A tesztelés befejezése után a Tudásbázis tartalma [importálási és exportálási](../Tutorials/migrate-knowledge-base.md) folyamattal kerül át a jóváhagyó QnA Maker szolgáltatásához, amely végül közzéteszi a tudásbázist, és frissíti a végpontot.



## <a name="active-learning"></a>Aktív tanulás

Az [aktív tanulás](../How-to/improve-knowledge-base.md) a legjobb megoldás arra, hogy alternatív kérdéseket tegyen fel, ha számos minőségi és mennyiségű felhasználó-alapú lekérdezést tartalmaz. Fontos, hogy az ügyfélalkalmazások felhasználói lekérdezései részt vegyenek az aktív tanulási visszajelzési hurokban a cenzúra nélkül. Ha kérdése van a QnA Maker-portálon, akkor a **[javaslatok alapján szűrheti a javaslatokat](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** , majd áttekintheti, elfogadhatja vagy elutasíthatja ezeket a javaslatokat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudásbázis szerkesztése](../How-to/edit-knowledge-base.md)
