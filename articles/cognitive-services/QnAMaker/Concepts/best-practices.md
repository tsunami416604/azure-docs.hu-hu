---
title: Ajánlott eljárások – QnA Maker
titleSuffix: Azure Cognitive Services
description: Az ajánlott eljárások segítségével javíthatja tudásbázisát, és jobb eredményeket biztosíthat az alkalmazás-és csevegési robot végfelhasználóinak.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 2fd85e43fb2aa53299b4e37eca5163b7da8fc6ec
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843803"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>QnA Maker Tudásbázis ajánlott eljárásai

A [Tudásbázis fejlesztési életciklusa](../Concepts/development-lifecycle-knowledge-base.md) végigvezeti Önt a kb elejétől a végéig történő kezelésének lépésein. Az ajánlott eljárások segítségével javíthatja a tudásbázist, és jobb eredményeket biztosíthat az ügyfélalkalmazás vagy a csevegő robot végfelhasználói számára.

## <a name="extraction"></a>Kinyerési

A QnA Maker szolgáltatás folyamatosan fejleszti azokat az algoritmusokat, amelyek kinyerik a QnAs a tartalomból, és kibővítik a támogatott fájl-és HTML-formátumok listáját. Kövesse az adatbontásra vonatkozó [irányelveket](../Concepts/content-types.md) a dokumentum típusa alapján.

Általánosságban elmondható, hogy a GYIK-lapoknak önállónak kell lenniük, és nem kombinálhatók más adatokkal. A termék-kézikönyvek egyértelmű fejléceket és lehetőleg index oldalt tartalmazhatnak.

### <a name="configuring-multi-turn"></a>Többszörös kapcsolás konfigurálása

[Hozzon létre egy tudásbázist](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure) , és engedélyezze a többszörös kibontást. Ha a Tudásbázis nem rendelkezik vagy támogatnia kell a kérdés-hierarchiát, ez a hierarchia kinyerhető a dokumentumból, vagy a dokumentum kinyerése után hozható létre.

<!--is this a global setting that can only be configured at kb creation time? -->

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

## <a name="chit-chat"></a>Chit-csevegés
Vegyen fel egy csevegést a robotba, hogy minél több beszélgetést és részvételt hozzon létre a robotban, kis erőfeszítéssel. A KB létrehozásakor könnyedén hozzáadhat a Chit-Chat-adatkészleteket az előre definiált személyes adatokból, és bármikor módosíthatja azokat. Megtudhatja, hogyan [adhat hozzá a kb-hoz a Chit-chatet](../How-To/chit-chat-knowledge-base.md).

A Chit-Chat [több nyelven](../how-to/chit-chat-knowledge-base.md#language-support)is támogatott.

### <a name="choosing-a-personality"></a>Személyiség kiválasztása
A Chit-Chat több előre definiált személy számára is támogatott:

|Személyiség |Adatkészlet-fájl QnA Maker |
|---------|-----|
|Professzionális |[qna_chitchat_professional. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Rövid |[qna_chitchat_friendly. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Szellemes |[qna_chitchat_witty. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Gondoskodó |[qna_chitchat_caring. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Lelkes |[qna_chitchat_enthusiastic. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

A válaszok a formálistól az informális és a tiszteletlenig terjedhetnek. Válassza ki azt a személyiséget, amely a legközelebb a robothoz használni kívánt hanghoz van igazítva. Megtekintheti az [adatkészleteket](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets), és kiválaszthatja az egyiket, amely a robot alapjául szolgál, majd testreszabhatja a válaszokat.

### <a name="edit-bot-specific-questions"></a>A bot-specifikus kérdések szerkesztése
Van néhány, a Chit-Chat-adathalmaz részét képező bot-specifikus kérdés, amely általános válaszokkal lett kitöltve. Változtassa meg ezeket a válaszokat, hogy a legjobban tükrözzék a robot adatait.

Javasoljuk, hogy a következő Chit-Chat-QnAs konkrétabb legyen:

* hogy vagy?
* Mit tehet?
* mennyi idős vagy?
* Ki hozta létre?
* Üdvözöljük!

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Egyéni Chit-Chat hozzáadása metaadat-címkével

Ha saját Chit-csevegési QnA-párokat ad hozzá, ügyeljen arra, hogy metaadatokat adjon hozzá, így ezek a válaszok visszakerülnek. A metaadatok neve/értéke pár `editorial:chitchat`.

## <a name="searching-for-answers"></a>Válaszok keresése

A GenerateAnswer API mindkét kérdést és a választ használja a felhasználó lekérdezésére való legjobb válaszok keresésére.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Kérdések keresése csak akkor, ha a válasz nem releváns

Ha nem szeretne válaszokat keresni, használja a [`RankerType=QuestionOnly`](#choosing-ranker-type) .

Ilyen például, ha a Tudásbázis a betűszók katalógusa, amely a válaszként megjelenő teljes űrlappal kapcsolatos kérdés. A válasz értéke nem fog segíteni a megfelelő válasz megkeresésében.

## <a name="rankingscoring"></a>Rangsorolás/pontozás
Győződjön meg arról, hogy a rangsorolási funkciók legjobb használatát QnA Maker támogatja. Ennek köszönhetően javul annak a valószínűsége, hogy egy adott felhasználói lekérdezés megfelelő válaszra van válaszolva.

### <a name="choosing-a-threshold"></a>Küszöbérték kiválasztása

A küszöbértékként használt alapértelmezett [megbízhatósági pontszám](confidence-score.md) 50, azonban az igényeinek megfelelően módosíthatja a KB-os [küszöbértéket](confidence-score.md#set-threshold) . Mivel minden KB különböző, érdemes tesztelni és kiválasztani a KB-hoz legmegfelelőbb küszöbértéket.

### <a name="choosing-ranker-type"></a>A ranker típusának kiválasztása
Alapértelmezés szerint a QnA Maker kérdésekkel és válaszokkal keres. Ha csak kérdésekkel szeretne keresni, válasz létrehozásához használja a `RankerType=QuestionOnly` a GenerateAnswer kérelem POST törzsében.

### <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása
A [másodlagos kérdések](../How-To/edit-knowledge-base.md) javítják a felhasználói lekérdezésekkel való egyezés valószínűségét. A helyettesítő kérdések akkor hasznosak, ha több módon is megteheti a kérdést. Ez magában foglalhatja a mondat szerkezetének és a szó stílusainak változásait is.

|Eredeti lekérdezés|Alternatív lekérdezések|Módosítás|
|--|--|--|
|Elérhető a parkoló?|Van parkolója?|mondat szerkezete|
 |szia|Yo<br>Sziasztok!|szó-stílus vagy szleng|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>A kérdések és válaszok szűrése metaadat-címkék használatával

A [metaadatok](../How-To/edit-knowledge-base.md) lehetővé teszik, hogy az ügyfélalkalmazás tudja, hogy nem fogadja el az összes választ, hanem a metaadat-címkék alapján leszűkíti a felhasználói lekérdezés eredményét. A Tudásbázis válasza a metaadatok címkéje alapján is eltérhet, még akkor is, ha a lekérdezés ugyanaz. Ha például a *"hol van a parkolóban található"* , akkor lehet, hogy más válasz van, ha az éttermi ág helye eltérő – vagyis a metaadatok *helye: Seattle* és *Location: Redmond*.

### <a name="use-synonyms"></a>Szinonimák használata
Míg az angol nyelv szinonimái is vannak, a kis-és nagybetűk megkülönböztetése az [Alters API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) -n keresztül a különböző űrlapokat használó kulcsszavak szinonimáinak hozzáadását is lehetővé teheti. A szinonimákat a szolgáltatás szintjén adja hozzá a rendszer, és a szolgáltatás minden tudásbázisa megosztja a QnA Maker.

|Eredeti szó|Szinonimák|
|--|--|
|venni|beszerzési<br>NET – banki szolgáltatások<br>nettó banki|

### <a name="use-distinct-words-to-differentiate-questions"></a>Különböző szavak használata a kérdések megkülönböztetéséhez
A QnA Maker rangsorolási algoritmusa, amely egy, a Tudásbázisban kérdéssel rendelkező felhasználói lekérdezésnek felel meg, akkor a legjobban működik, ha az egyes kérdések eltérő igényt mutatnak. A kérdések között megjelenő szó ismétlődése csökkenti annak a valószínűségét, hogy a megfelelő válasz van kiválasztva egy adott felhasználói lekérdezéshez ezekkel a szavakkal.

Előfordulhat például, hogy két különálló QnAs rendelkezik a következő kérdésekkel:

|QnAs|
|--|
|Hol található a parkoló *helye*|
|Hol található az ATM *helye*|

Mivel ez a két QnAs nagyon hasonló szavakkal van megfogalmazva, ez a hasonlóság nagyon hasonló pontszámokat okozhat számos olyan felhasználói lekérdezés esetében, amelyek *"hol van a `<x>` helye"* . Ehelyett próbálja meg egyértelműen megkülönböztetni a lekérdezéseket, például *a "hol van a parkolóban"* és *"hol van az ATM"* , a "location" kifejezéssel, amely a KB-ban sok kérdésben lehet.

## <a name="collaborate"></a>Együttműködés
QnA Maker lehetővé teszi a felhasználók számára, hogy a Tudásbázisban [működjenek együtt](../How-to/collaborate-knowledge-base.md) . A tudásbázisok eléréséhez a felhasználóknak hozzá kell férniük az Azure QnA Maker erőforráscsoporthoz. Előfordulhat, hogy néhány szervezet szeretné kiszervezni a Tudásbázis szerkesztését és karbantartását, és továbbra is képes lesz biztosítani az Azure-erőforrásokhoz való hozzáférést. Ez a szerkesztő-jóváhagyó modell két azonos [QnA Maker-szolgáltatásnak](../How-to/set-up-qnamaker-service-azure.md) a különböző előfizetésekben való beállításával és a szerkesztési tesztelési ciklus egyikének kiválasztásával valósítható meg. A tesztelés befejezése után a Tudásbázis tartalma [importálási és exportálási](../Tutorials/migrate-knowledge-base.md) folyamattal kerül át a jóváhagyó QnA Maker szolgáltatásához, amely végül közzéteszi a tudásbázist, és frissíti a végpontot.



## <a name="active-learning"></a>Aktív tanulás

Az [aktív tanulás](../How-to/improve-knowledge-base.md) a legjobb megoldás arra, hogy alternatív kérdéseket tegyen fel, ha számos minőségi és mennyiségű felhasználó-alapú lekérdezést tartalmaz. Fontos, hogy az ügyfélalkalmazások felhasználói lekérdezései részt vegyenek az aktív tanulási visszajelzési hurokban a cenzúra nélkül. Ha kérdése van a QnA Maker-portálon, akkor a **[javaslatok alapján szűrheti a javaslatokat](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** , majd áttekintheti, elfogadhatja vagy elutasíthatja ezeket a javaslatokat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudásbázis szerkesztése](../How-to/edit-knowledge-base.md)
