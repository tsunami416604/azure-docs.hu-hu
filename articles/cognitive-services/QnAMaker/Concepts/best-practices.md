---
title: Gyakorlati tanácsok - QnA Maker
description: Ezekkel az ajánlott eljárásokkal javíthatja tudásbázisát, és jobb eredményeket biztosíthat az alkalmazás/csevegőrobot végfelhasználói nak.
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 9a6f7f7d6edc4544942476050a1ed3c2011af7fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053135"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>A QnA Maker tudásbázisának gyakorlati gyakorlatai

A [tudásbázis fejlesztési életciklusa](../Concepts/development-lifecycle-knowledge-base.md) végigvezeti a tudásbázis kezeléséhez az elejétől a végéig. Ezekkel az ajánlott eljárásokkal javíthatja a tudásbázist, és jobb eredményeket biztosíthat az ügyfélalkalmazás vagy a csevegőrobot végfelhasználói számára.

## <a name="extraction"></a>Kigyűjtés

A QnA Maker szolgáltatás folyamatosan fejleszti azokat az algoritmusokat, amelyek kinyerik a QnA-kat a tartalomból, és bővítik a támogatott fájl- és HTML-formátumok listáját. Kövesse [guidelines](../Concepts/content-types.md) az adatkinyerésirányelveit a dokumentum típusától függően.

A GYIK-oldalaknak általában önállónak kell lenniük, és nem szabad más információkkal kombinálniőket. A termékkézikönyveknek világos fejlécekkel és lehetőleg indexoldallal kell rendelkezniük.

### <a name="configuring-multi-turn"></a>Többfordulatos beállítás

[Hozza létre tudásbázisát](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure) a többfordulatos kinyerés engedélyezésével. Ha a tudásbázis támogatja vagy támogatnia kell a kérdéshierarchiát, akkor ez a hierarchia kinyerhető a dokumentumból, vagy a dokumentum kibontása után hozható létre.

## <a name="creating-good-questions-and-answers"></a>Jó kérdések és válaszok létrehozása

### <a name="good-questions"></a>Jó kérdések

A legjobb kérdések egyszerűek. Tekintsük a kulcsszó vagy kifejezés minden kérdésre, majd hozzon létre egy egyszerű kérdést, hogy a kulcsszó vagy kifejezés.

Adjon hozzá annyi alternatív kérdést, amennyire szüksége van, de a módosítások egyszerűek legyenek. Ha további szavakat vagy kifejezéseket ad hozzá, amelyek nem részei a kérdés fő céljának, az nem segít a QnA Maker-nek egyezést találni.


### <a name="add-relevant-alternative-questions"></a>Releváns alternatív kérdések hozzáadása

A felhasználó kérdéseket adhat meg egy beszélgetési szövegstílussal vagy egy kulcsszókereséssel, `How do I add a toner cartridge to my printer?` például `toner cartridge`. A tudásbázisnak mindkét típusú kérdésre vonatkozóan rendelkeznie kell ahhoz, hogy a legjobb választ adja vissza. Ha nem biztos abban, hogy az ügyfél milyen kulcsszavakat ír be, használja az Application Insights-adatokat a lekérdezések elemzéséhez.

### <a name="good-answers"></a>Jó válaszok

A legjobb válaszok egyszerű válaszok, de nem túl egyszerűek. Ne használjon olyan `yes` `no`válaszokat, mint a és a . Ha a válasznak más forrásokra kell hivatkoznia, vagy gazdag élményt kell nyújtania a médiával és a hivatkozásokkal `strictFilters` kapcsolatban, [a metaadatok címkézésével](../how-to/edit-knowledge-base.md#add-metadata) különbséget kell tenni a válaszok között, majd küldje el a [lekérdezést](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) metaadat-címkékkel a tulajdonságban a helyes válaszverzió lekérdezéséhez.

|Válasz|Follup-up kéri|
|--|--|
|Kapcsolja ki a Surface laptopot a billentyűzeten található bekapcsológombbal.|* Billentyűkombinációk aludni, állítsa le, és indítsa újra.<br>* Hogyan hard-boot a Surface laptop<br>* Hogyan változtassuk meg a BIOS-t a Surface laptop<br>* Közötti különbségek alvás, állítsa le és indítsa újra|
|Az ügyfélszolgálat a nap 24 órájában elérhető telefonon, Skype-on és SMS-ben.|* Elérhetőségértékesítési információk.<br> * Iroda és tárolja helyeken és órákon át egy személyes látogatást.<br> * Tartozékok a Surface laptop.|

## <a name="chit-chat"></a>Chit-chat
Add chit-chat a bot, hogy a bot több társalgási és vonzó, alacsony erőfeszítéssel. A tudásbázis létrehozásakor könnyedén hozzáadhat egy előre definiált személyiségektől származó csevegési adatkészleteket, és bármikor módosíthatja őket. További információ [arról, hogyan adhat hozzá csevegést a KB-hoz.](../How-To/chit-chat-knowledge-base.md)

Chit-chat támogatott [sok nyelven](../how-to/chit-chat-knowledge-base.md#language-support).

### <a name="choosing-a-personality"></a>A személyiség kiválasztása
Chit-chat támogatott több előre meghatározott személyiségek:

|Személyiség |QnA Maker adatkészletfájl |
|---------|-----|
|Professional |[qna_chitchat_professional.tsv.](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Barátságos |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Szellemes |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Gondoskodó |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Lelkes |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

A válaszok a formálistól az informálisig és tiszteletlenekig terjednek. Válassza ki azt a személyiséget, amely a legközelebb áll a robot kívánt hangneméhez. Megtekintheti az [adatkészleteket](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets), és kiválaszthatja, hogy melyik szolgál a robot alapjaként, majd testreszabhatja a válaszokat.

### <a name="edit-bot-specific-questions"></a>Bot-specifikus kérdések szerkesztése
Vannak olyan robotspecifikus kérdések, amelyek a csevegési adatkészlet részét képezik, és általános válaszokkal vannak kitöltve. Módosítsa ezeket a válaszokat, hogy a legjobban tükrözze a robot adatait.

Javasoljuk, hogy a következő chit-chat QnAs pontosabb:

* hogy vagy?
* Mit lehet tenni?
* mennyi idős vagy?
* Ki teremtett téged?
* helló

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Egyéni csevegés hozzáadása metaadatcímkével

Ha saját qna-csevegési QnA-párokat ad hozzá, ügyeljen arra, hogy metaadatokat adjon hozzá, hogy ezek a válaszok visszaadhassák. A metaadat-név/értékpár a `editorial:chitchat`.

## <a name="searching-for-answers"></a>Válaszok keresése

A GenerateAnswer API a kérdéseket és a választ egyaránt használja a felhasználó lekérdezésére adott legjobb válaszok kereséséhez.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Kérdések keresése csak akkor, ha a válasz nem releváns

Használja [`RankerType=QuestionOnly`](#choosing-ranker-type) a, ha nem akar keresni válaszokat.

Erre példa, amikor a tudásbázis a rövidítések katalógusa, amelynek teljes formája a válasz. A válasz értéke nem segít a megfelelő válasz keresésében.

## <a name="rankingscoring"></a>Rangsorolás/pontozás
Győződjön meg róla, hogy a lehető legjobban kihasználja a QnA Maker által támogatott rangsorolási funkciókat. Ezzel növeli annak valószínűségét, hogy egy adott felhasználói lekérdezésre megfelelő választ kap.

### <a name="choosing-a-threshold"></a>Küszöbérték kiválasztása

A küszöbértékként használt alapértelmezett [megbízhatósági pontszám](confidence-score.md) 0, azonban a tudásbázis küszöbértékét az igényeinek megfelelően [módosíthatja.](confidence-score.md#set-threshold) Mivel minden kb más, tesztelje és válassza ki a tudásbázishoz leginkább megfelelő küszöbértéket.

### <a name="choosing-ranker-type"></a>Ranker típus kiválasztása
Alapértelmezés szerint a QnA Maker kérdések és válaszok között keres. Ha csak a kérdések között szeretne keresni, hogy `RankerType=QuestionOnly` választ hozzon létre, használja a GenerateAnswer kérés POST törzsében lévő teste.

### <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása
[Az alternatív kérdések](../How-To/edit-knowledge-base.md) növelik a felhasználói lekérdezéssel való egyezés valószínűségét. Az alternatív kérdések akkor hasznosak, ha ugyanazt a kérdést többféleképpen is fel lehet tenni. Ez magában foglalhatja a mondatszerkezet és a szóstílus változásait.

|Eredeti lekérdezés|Alternatív lekérdezések|Módosítás|
|--|--|--|
|Van szabad parkolóhely?|Van parkolód?|mondat szerkezete|
 |szia|Yo<br>Sziasztok!|szó-stílusú vagy szleng|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>A kérdések és válaszok szűrése metaadatcímkék kel.

[A metaadatok](../How-To/edit-knowledge-base.md) lehetővé teszik az ügyfélalkalmazások számára, hogy tudják, nem szabad minden választ megkapnia, hanem le szűkítenie kell a metaadat-címkéken alapuló felhasználói lekérdezés eredményeit. A tudásbázis válasza a metaadat-címkén alapuló eltérést okozhat, még akkor is, ha a lekérdezés megegyezik. Például a *"hol található parkoló"* eltérő választ kaphat, ha az éttermi ág helye más - vagyis a metaadatok a *Hely: Seattle* versus *Hely: Redmond*.

### <a name="use-synonyms"></a>Szinonimák használata
Bár az angol nyelvű szinonimák at [támogatják, a kis- és nagybetűk](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) et nem érzékeny szavak módosításai segítségével adja hozzá a szinonimákat a különböző formájú kulcsszavakhoz. A szinonimák a QnA Maker szolgáltatásszintjén kerülnek hozzáadásra, és a szolgáltatás összes tudásbázisa megosztja őket.

|Eredeti szó|Szinonimák|
|--|--|
|Vásárolni|beszerzés<br>nettó banki<br>nettó banki szolgáltatások|

### <a name="use-distinct-words-to-differentiate-questions"></a>A kérdések megkülönböztetéséhez használjon különböző szavakat
A QnA Maker rangsorolási algoritmusa, amely egy felhasználói lekérdezést egy kérdéssel egyezik meg a tudásbázisban, akkor működik a legjobban, ha minden kérdés más-más igényt elégít ki. A kérdések között beállított szó ismétlése csökkenti annak valószínűségét, hogy a megfelelő választ választ kapja egy adott felhasználói lekérdezéshez ezekkel a szavakkal.

Előfordulhat például, hogy két külön qna van a következő kérdésekkel:

|QnAs között|
|--|
|hol van a *parkoló*|
|hol van az ATM *helye*|

Mivel ez a két QnA nagyon hasonló szavakkal van megfogalmazva, ez a hasonlóság nagyon hasonló pontszámokat okozhat sok felhasználói lekérdezésnél, amelyeket úgy fogalmaztak meg, mint *"hol van `<x>` a hely"*. Ehelyett próbálja meg egyértelműen megkülönböztetni a lekérdezéseket, mint *a "hol van a parkoló"* és *a "hol van az ATM",* elkerülve a szavakat, mint a "hely", hogy lehet sok kérdést a KB.

## <a name="collaborate"></a>Együttműködés
A QnA Maker lehetővé teszi a felhasználók számára, hogy [együttműködjenek](../How-to/collaborate-knowledge-base.md) egy tudásbázison. A felhasználóknak hozzáférésre van szükségük az Azure QnA Maker erőforráscsoporthoz a tudásbázisok eléréséhez. Egyes szervezetek előfordulhat, hogy kiszervezik a tudásbázis szerkesztési és karbantartási, és továbbra is képes megvédeni az Azure-erőforrásokhoz való hozzáférést. Ez a szerkesztő-jóváhagyó modell két azonos [QnA Maker-szolgáltatás](../How-to/set-up-qnamaker-service-azure.md) beállításával történik a különböző előfizetésekben, és kiválaszt egyet a szerkesztési tesztelési ciklushoz. A tesztelés befejezése után a tudásbázis tartalma [egy importálási-exportálási](../Tutorials/migrate-knowledge-base.md) folyamattal átkerül a jóváhagyó QnA Maker szolgáltatásába, amely végre közzéteszi a tudásbázist és frissíti a végpontot.



## <a name="active-learning"></a>Aktív tanulás

[Az aktív tanulás](../How-to/use-active-learning.md) a legjobb megoldás alternatív kérdésekre, ha a felhasználó alapú lekérdezések széles választékával és mennyiségével rendelkezik. Fontos, hogy az ügyfélalkalmazások felhasználói lekérdezései cenzúra nélkül is részt vehessenek az aktív tanulási visszacsatolási hurokban. Amint a QnA Maker portálon kérdéseket javasol, **[javaslatok szerint szűrhet,](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** majd áttekintheti és elfogadhatja vagy elutasíthatja ezeket a javaslatokat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis szerkesztése](../How-to/edit-knowledge-base.md)
