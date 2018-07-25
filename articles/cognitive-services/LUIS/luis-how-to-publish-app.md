---
title: A LUIS-alkalmazás közzététele |} A Microsoft Docs
description: Miután hozhat létre, és az alkalmazás tesztelése a Language Understanding (LUIS) használatával, közzéteheti webszolgáltatásként, amely az Azure-ban.
services: cognitive-services
titleSuffix: Azure
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry;
ms.openlocfilehash: b827f157ccd2919c959385eefd0e0223a8e23607
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225540"
---
# <a name="publish-your-trained-app"></a>A betanított alkalmazás közzététele
Ha befejezte a elkészítését és tesztelését a LUIS-alkalmazás, tegye közzé. Az alkalmazás közzététele után a közzétételi oldalon látható-e az összes kapcsolódó HTTP [végpontok](luis-glossary.md#endpoint). Ezeket a végpontokat kiszolgálónként [régió](luis-reference-regions.md) és a egy [kulcs](luis-how-to-manage-keys.md), majd integrálva vannak minden olyan ügyfél, a csevegőrobot, vagy a háttérrendszer alkalmazás. 

Bármikor [tesztelése](luis-interactive-test.md) mielőtt közzéteszi az alkalmazást. 

## <a name="production-and-staging-slots"></a>Éles és átmeneti tárhelyek
Az alkalmazás, közzétehet a **előkészítési pont** vagy a **üzemelési**. Két közzétételi tárolóhely használatával Ez lehetővé teszi, hogy két különböző végpontok közzétett végpontokkal rendelkező két különböző verziója vagy verziójával rendelkezik. 

<!-- TBD: what is the technical difference? log files, endpoint quota? -->

## <a name="settings-configuration-requires-publishing-model"></a>Beállítások konfigurálása a közzétételi modellre van szüksége.
A végpont közzététele után a következő beállítások módosításait. 

## <a name="external-services-settings"></a>Külső szolgáltatások beállításai
Külső szolgáltatási beállítások közé tartozik a **[Hangulatelemzés](#enable-sentiment-analysis)** és  **[Speech előkészítése](#enable-speech-priming)**.

### <a name="enable-sentiment-analysis"></a>Hangulatelemzés engedélyezése
Az a **külső szolgáltatások beállításai**, a **Hangulatelemzés engedélyezése** jelölőnégyzet lehetővé teszi, hogy a LUIS integrálása [Szövegelemzés](https://azure.microsoft.com/services/cognitive-services/text-analytics/) vélemények és kulcskifejezések elemzés. Nem kell adnia a Text Analytics kulcsot, és nem jár költségekkel számlázási ezt a szolgáltatást az Azure-fiókjába. Ha bejelöli ezt a beállítást, az állandó. 

Véleményadatok egy 1 és 0 a pozitív jelző közötti pontszámot (közelebb 1) vagy negatív (0 közelebb) az adatok a róluk szóló véleményeket.

A JSON-végpont választ véleményelemzéssel kapcsolatos további információkért lásd: [hangulatelemzés](luis-concept-data-extraction.md#sentiment-analysis)

### <a name="enable-speech-priming"></a>Beszéd betanítási művelet engedélyezése 
Az a **külső szolgáltatások beállításai**, a **Speech előkészítése engedélyezése** jelölőnégyzet lehetővé teszi, hogy egyetlen végpontot használja a beszélt utterance kérhet egy csevegőrobot, vagy a LUIS-hívása alkalmazás, és a LUIS fogadására előrejelzési választ. A beszédfelismerés betanítási művelet használja a Cognitive Services-szolgáltatás [Speech API](../Speech-Service/rest-apis.md). 

![Beszéd betanítási művelet megerősítő párbeszédpanel képe](./media/luis-how-to-publish-app/speech-prime-modal.png)

Ez a funkció engedélyezését követően tegye közzé az alkalmazást. A LUIS-alkalmazás közzétételekor a Speech service előkészíteni az app model küldött saját beszédszolgáltatás. A modell adatait van **nem** kívül a saját szolgáltatást használni. 

Használja a következő információkat kell végrehajtani a Speech betanítási művelet használatát, a [beszéd SDK](../speech-service/speech-sdk-reference.md):
* A LUIS végponti kulcs.
* A LUIS alkalmazás azonosítóját.
* Egy végpont tartományban néven "Állomásnév" Speech SDK-ban, például a "westus.api.cognitive.microsoft.com,", az első altartomány-e a régióban, ahol az alkalmazás közzé van téve.

További információkért lásd: a [Beszédszándék](http://aka.ms/speechsdk) minta.

A LUIS-alkalmazás törlik, vagy törli a Speech szolgáltatást, ha a modell adatai törlődnek. 

## <a name="endpoint-url-settings"></a>Végpont URL-beállítások
Végpont URL-cím szolgáltatások beállítások közé tartozik a **[időzóna](#set-timezone-offset)** eltolva  **[összes előrejelzett pontszámok szándék](#include-all-predicted-intent-scores)**, és  **[ A Bing helyesírás-ellenőrzővel](#enable-bing-spell-checker)**.

### <a name="set-timezone-offset"></a>Időzóna-eltolás beállítása 
A tárolóhely választás részét képezi az időzóna kiválasztásától. Az időzóna-beállítás lehetővé teszi a LUIS [alter](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) előre összeállított datetimeV2 bármikor értékek előrejelzés során, úgy, hogy a visszaadott Entitásadatok megfelelő kiválasztott időzónájának megfelelően. 

### <a name="include-all-predicted-intent-scores"></a>Tartalmazza az összes, szándék előrejelzett pontszámok
A **minden beágyazott előre jelzett szándék pontszámok** jelölőnégyzet lehetővé teszi, hogy a végpont lekérdezésekre adott válaszok tartalmazzák az előrejelzési pontszámának egyes szándékot. 

Ez a beállítás lehetővé teszi, hogy a csevegőrobot, vagy a LUIS-hívása alkalmazás a programozott döntéshozáshoz, a visszaadott leképezések a pontszámok alapján. A felső két szándék általában a legjobban érdekli. Ha a felső pontszám nem a nincs szándék, a csevegőrobot, amely lehetővé teszi a nincs szándékának és a nagy pontozási célt végleges választhat követő kérdést feltenni választhat. 

A leképezések és azok pontszámok is vannak-e a végpont naplók tartalmazza. Is [exportálása](luis-how-to-start-new-app.md#export-app) ezeket a naplókat és elemezheti a pontszámokat. 

```
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

### <a name="enable-bing-spell-checker"></a>A Bing helyesírás-ellenőrzés engedélyezése 
Az a **végpont URL-beállítások**, a **engedélyezése a Bing helyesírás-ellenőrzővel** jelölőnégyzet lehetővé teszi, hogy a LUIS, mielőtt előrejelzési kijavítsa a hibásan leírt szavakat. Hozzon létre egy  **[kulcs a Bing Spell Check](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**. A kulcs létrehozása után két lekérdezési karakterlánc paraméterei kerülnek a közzétételi oldalon található végponti URL-cím. 

Adja hozzá a **helyesírás-ellenőrzés = true** sztringet és a **bing – helyesírás-ellenőrzés-subscription-key = {YOUR_BING_KEY_HERE}** . Cserélje le a `{YOUR_BING_KEY_HERE}` és a Bing helyesírás-ellenőrző kulcs.

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

## <a name="publish-your-trained-app-to-an-http-endpoint"></a>Egy HTTP-végpontot a betanított alkalmazás közzététele
Nyissa meg az alkalmazás nevére kattintva a **saját alkalmazások** lapon, és kattintson a **közzététel** a az ablak tetején. 

![Közzététel lap –](./media/luis-how-to-publish-app/publish-to-production.png)
 
Ha az alkalmazás sikeresen közzé lett téve, egy zöld, sikeres értesítés jelenik meg, a böngésző tetején. 

* Döntse el, hogy közzé **éles** vagy **átmeneti** válassza ki a legördülő menü alatt **válassza tárolóhely**. 

## <a name="assign-key"></a>Kulcs hozzárendelése

Ha szeretné használni egy kulcs más, az ingyenes Starter_Key látható, kattintson a **kulcs hozzáadása** gombra. Ezzel megnyílik egy párbeszédpanel, amely lehetővé teszi, hogy válassza ki az alkalmazás hozzárendelése meglévő végponti kulcs. Hogyan hozhat létre, és a végpont kulcs hozzáadása a LUIS-alkalmazás további információkért lásd: [a kulcsok kezelését](luis-how-to-manage-keys.md).

Végpontok és a társított régiók kulcsainak megtekintéséhez használja a választógombok régiók váltani. Minden egyes sorára a **erőforrások és a kulcsok** táblázat a fiókhoz társított Azure-erőforrások és a társított erőforrás végpont kulcsokat.

## <a name="endpoint-url-construction"></a>Végpont URL-cím építése
A végponti URL-cím felel meg a végpont kulcs társított Azure-régióban.

Ez a táblázat kényelmesen közzétételi konfigurációjához a az URL-cím végpont útválasztási lehetőségeket és a lekérdezési karakterlánc értékeit jeleníti meg. Ha a LUIS-hívása alkalmazáshoz is hozhat létre a végponti URL-címek, győződjön meg arról, ezek azonos útvonalakat és a lekérdezési karakterlánc értékek vannak beállítva, a végpont a alkalmazni – Ha azt szeretné, állítsa be.

Az URL-útvonal jön létre a régióban, és az alkalmazás azonosítóját. Más régiókban, illetve más alkalmazásokkal közzétételekor, a végpont URL-címe a régiók és app ID értékek módosításával lehet létrehozni. 

* Válasza a Production (Termelés) helyet, és kattintson a **Publish** (Közzététel) gombra. Ha a közzététel sikeres volt, a megjelenített végponti URL-cím használatával a LUIS-alkalmazás elérésére. 

### <a name="optional-query-string-parameters"></a>Opcionális lekérdezési karakterlánc paraméterei
A következő lekérdezési karakterlánc paraméterei használható a végpont URL-címe:

<!-- TBD: what about speech priming? -->

|Lekérdezési sztring|Típus|Példaérték|Cél|
|--|--|--|--|
|részletes|logikai|true|Például [összes szándék pontszámok](#include-all-predicted-intent-scores) az utterance (kifejezés)|
|timezoneOffset|szám (egység érték perc)|60|Állítsa be [időzóna-eltolás](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) a [datetimeV2 előre összeállított entitások](luis-reference-prebuilt-datetimev2.md)|
|Helyesírás-ellenőrzés|logikai|true|[Javítsa ki a helyesírás-ellenőrzés](#enable-bing-spell-checker) az utterance (kifejezés) – a bing – helyesírás-ellenőrzés-subscription-key lekérdezési karakterlánc paramétereként együtt használható|
|Bing – helyesírás-ellenőrzés-subscription-key|előfizetés-azonosító||Helyesírás-ellenőrzés lekérdezési karakterlánc paramétereként együtt használható|
|átmeneti|logikai|false|Válassza ki az átmeneti és éles végpontot|
|napló|logikai|true|Adja hozzá a lekérdezési és az eredményeket|


## <a name="test-your-published-endpoint-in-a-browser"></a>Egy böngészőben a közzétett végpont tesztelése
Tesztelje a közzétett végpont URL-cím kiválasztásával a **végpont** oszlop. Az alapértelmezett böngésző megnyitja a létrehozott URL-címet. Az URL paramétert állítsa "& q", a teszt lekérdezést. Például hozzáfűzése `&q=Book me a flight to Boston on May 4` a saját URL-címet, és nyomja le az ENTER billentyűt. A böngésző ugyanúgy megjeleníti a HTTP-végpont a JSON-választ. 

![A közzétett HTTP-végpontot a JSON-válasz](./media/luis-how-to-publish-app/luis-publish-app-json-response.png)

## <a name="next-steps"></a>További lépések

* Lásd: [kulcsok kezelése](./luis-how-to-manage-keys.md) kulcs hozzáadása a LUIS-alkalmazás, és ismerje meg hogyan kulcsok leképezése régióban.
* Lásd: [Train és tesztelje alkalmazását](luis-interactive-test.md) vonatkozó utasításokat a közzétett alkalmazás tesztelése a test-konzolon.
