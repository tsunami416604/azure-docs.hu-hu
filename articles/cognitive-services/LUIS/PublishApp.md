---
title: A LUIS alkalmazás közzététele |} Microsoft Docs
description: Miután összeállítása, és tesztelje az alkalmazás nyelvi ismertetése (LUIS) segítségével, közzéteszi az Azure webszolgáltatásként.
services: cognitive-services
titleSuffix: Azure
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: 12a63e65a739be08d436f8f1b53df566255b1fb1
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36322057"
---
# <a name="publish-your-trained-app"></a>A betanított alkalmazás közzététele
Befejezése után összeállításakor és tesztelésekor a LUIS alkalmazás közzététele. Az alkalmazás közzététele után a közzététel a lapon látható-e az összes kapcsolód HTTP [végpontok](luis-glossary.md#endpoint). Ezeket a végpontokat / [régió](luis-reference-regions.md) , valamint minden [kulcs](Manage-Keys.md), majd minden ügyfél, chatbot vagy háttér alkalmazásba integrált. 

Mindig is [tesztelése](interactive-test.md) közzététel előtt az alkalmazást. 

## <a name="production-and-staging-slots"></a>Üzemi és átmeneti üzembe helyezési ponti
Az alkalmazás közzététele a **átmeneti tárolóhely** vagy a **éles tárolóhelyre**. Két közzétételi tárhelyek használatával Ez lehetővé teszi, hogy két különböző végpontokhoz kell közzétett végpontokon két különböző verziója, illetve azonos verzióját. 

<!-- TBD: what is the technical difference? log files, endpoint quota? -->

## <a name="settings-configuration-requires-publishing-model"></a>Beállítások közzétételi modellre van szüksége.
A végpont közzététele után a következő beállítások módosításait. 

## <a name="external-services-settings"></a>Külső szolgáltatások beállításai
Külső szolgáltatás beállításai közé tartozik **[véleményeket elemzés](#enable-sentiment-analysis)** és  **[beszéd előkészítési](#enable-speech-priming)**.

### <a name="enable-sentiment-analysis"></a>Engedélyezze a céggel kapcsolatos véleményeket elemzés
A a **külső-szolgáltatások beállításai**, a **véleményeket elemzés engedélyezése** jelölőnégyzet lehetővé teszi, hogy a integrálása LUIS [Szövegelemzések](https://azure.microsoft.com/services/cognitive-services/text-analytics/) véleményeket és a kulcs kifejezés elemzés. Szövegelemzések kulcs megadása nem szükséges, és számlázási ingyenesek ehhez a szolgáltatáshoz, az Azure-fiókjába. Ha bejelöli ezt a beállítást, akkor állandó. 

Véleményeket adatai között 1 vagy 0 az pozitív jelző pontszámot (közelebb 1) vagy negatív (0-ra közelebb) az adatok céggel kapcsolatos véleményeket.

A JSON-végpont választ a céggel kapcsolatos véleményeket elemzés kapcsolatos további információkért lásd: [véleményeket elemzés](luis-concept-data-extraction.md#sentiment-analysis)

### <a name="enable-speech-priming"></a>Beszéd betanítási művelet engedélyezése 
Az a **külső-szolgáltatások beállításai**, a **beszéd előkészítési engedélyezése** jelölőnégyzet lehetővé teszi egy végpontot szóbeli utterance chatbot vagy LUIS-hívása alkalmazás és egy LUIS fogadása Előrejelzés válasz. A beszédfelismerés elsődleges használja kognitív szolgáltatást [Diktálásfelismerési API](../Speech-Service/rest-apis.md). 

![Beszéd elsődleges megerősítő párbeszédpanele képe](./media/luis-how-to-publish-app/speech-prime-modal.png)

Ez a szolgáltatás engedélyezése után az alkalmazás közzététele. A LUIS alkalmazás közzétételekor az app model érkezik a saját beszéd szolgáltatás rendszerlemez a beszédfelismerés szolgáltatás. A modell adatok **nem** kívül a saját szolgáltatás használt. 

Beszéd elsődleges használatát befejezéséhez-ban történő használatáról a következő adatokat kell a [beszéd SDK](../speech-service/speech-sdk-reference.md):
* Egy LUIS előfizetés kulcs.
* A LUIS alkalmazás azonosítóját.
* Egy végpont tartományban néven "Állomásnév" beszéd SDK, például a "westus.api.cognitive.microsoft.com," ahol az első altartomány az a régió, ahol az alkalmazás közzé van téve.

További információkért lásd: a [leképezés beszédfelismerés](http://aka.ms/speechsdk) minta.

Amikor a LUIS alkalmazást törlik, vagy törölték a beszéd-szolgáltatás, a rendszer eltávolítja a modell adatai. 

## <a name="endpoint-url-settings"></a>Végpont URL-cím beállításai
Végpont URL-cím szolgáltatások a következők: **[időzóna](#set-timezone-offset)** eltolva  **[összes leképezési pontszámok előre jelezni](#include-all-predicted-intent-scores)**, és  **[ Bing helyesírás-ellenőrző](#enable-bing-spell-checker)**.

### <a name="set-timezone-offset"></a>Időzóna-eltolás beállítása 
Tárolóhely kiválasztása része az időzóna kiválasztásától. Ez a beállítás időzónában lehetővé teszi, hogy a LUIS [alter](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) előrejelzés során az előre elkészített datetimeV2 bármikor értékeket, hogy a visszaadott entitás adatok helyesek-e a kijelölt időzóna szerint. 

### <a name="include-all-predicted-intent-scores"></a>Minden előre jelzett leképezési pontszámok tartalmazza
A **Include minden előre jelezni leképezési pontszámok** jelölőnégyzet lehetővé teszi, hogy a végpont lekérdezésválaszt tartalmazza az egyes leképezés előrejelzés pontszámot. 

Ez a beállítás lehetővé teszi, hogy a chatbot vagy LUIS-hívása alkalmazás programozott döntést a visszaadott leképezések eredményét alapján. A felső két leképezések általában a legérdekesebb. Ha a felső pontszám nincs beállítva a leképezési, a chatbot szeretné követő kérdés, amelynek eredményeképpen a végleges választhatnak a nincs leképezési és a magas pontozási célt között. 

A leképezések és eredményeiket megtalálhatók a végpont logs tartalmazza. Is [exportálása](create-new-app.md#export-app) lesznek a naplók és elemezheti a pontszámait. 

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

### <a name="enable-bing-spell-checker"></a>Bing helyesírás-ellenőrző engedélyezése 
Az a **végpont URL-beállításainak**, a **engedélyezése Bing helyesírás-ellenőrző** jelölőnégyzet lehetővé teszi, hogy a LUIS elírt szavak előrejelzés előtt. Ehhez hozzon létre egy  **[Bing helyesírás-ellenőrzés kulcs](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**. A kulcs létrehozása után két lekérdezési karakterlánc paraméter hozzáadódnak a végponti URL-cím a közzététel oldalon. 

Ha LUIS-hívása alkalmazás a saját URL-címek vannak létrehozásával, ellenőrizze, hogy a **a helyesírás-ellenőrzési = true** lekérdezési karakterlánc paraméter és a **bing helyesírás-ellenőrzés-előfizetés-kulcs = {YOUR_BING_KEY_HERE}**. Cserélje le a `{YOUR_BING_KEY_HERE}` , a Bing helyesírás-ellenőrző futtatásához, kulcs.

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

## <a name="publish-your-trained-app-to-an-http-endpoint"></a>HTTP-végpont a betanított alkalmazás közzététele
Nyissa meg az alkalmazás nevére kattintva a a **saját alkalmazások** lapon, majd **közzététel** a az ablak tetején. 

![Lap - közzététele](./media/luis-how-to-publish-app/publish-to-production.png)
 
Ha az alkalmazás sikeresen közzé van téve, a böngésző tetején megjelenik egy zöld sikeres értesítés. 

* Válassza ki, hogy a közzétételéhez **éles** vagy **átmeneti** ; ehhez válassza a legördülő menüből a **válassza tárolóhely**. 

## <a name="assign-key"></a>Kulcs hozzárendelése

Ha azt szeretné, hogy a kulcs nem látható a szabad Starter_Key, kattintson a **kulcs hozzáadása** gombra. A művelet végrehajtásakor megnyílik egy párbeszédpanel, amely lehetővé teszi az alkalmazás hozzárendelése meglévő végpont kulcs kiválasztásához. Hogyan hozhat létre és végpont-kulcs hozzáadása az LUIS alkalmazás további információkért lásd: [a kulcsok kezelése](Manage-Keys.md).

Végpontok és más régiókban társított kulcsok megtekintéséhez használja a megfelelő választógomb régiók váltani. Minden egyes sorára a **erőforrások és a kulcsok** táblázat felsorolja a fiókjához társított Azure-erőforrások és a végpont kulcsok vannak társítva az adott erőforráshoz.

## <a name="endpoint-url-construction"></a>Végpont URL-cím létrehozása
A végponti URL-cím megfelel a végpont kulcshoz tartozó Azure-régiót.

Ez a táblázat kényelmesen közzétételi konfigurációjához a URL-cím végpont útvonal lehetőségeket és a lekérdezési karakterlánc-értékek a tükrözi. Ha LUIS-hívása alkalmazás vannak hozhat létre a végponti URL-címek, ellenőrizze, hogy ezeket a azonos útvonalak és a lekérdezési karakterlánc értéke a végpont használt--Ha azt szeretné, állítsa be.

Az URL-cím útvonal össze a régiót, és az alkalmazás azonosítóját. Ha közzéteszi a más régiókban vagy más alkalmazásokkal, a végponti URL-cím a területi és app ID értékek módosításával lehet létrehozni. 

* Válassza ki az éles tárhely és a **közzététel** gombra. Ha a közzététel sikeres, használja a megjelenített végponti URL-cím a LUIS alkalmazás eléréséhez. 

### <a name="optional-query-string-parameters"></a>Nem kötelező lekérdezési karakterlánc paraméterei
A következő lekérdezési karakterlánc paraméter használható a végpont URL-címe:

<!-- TBD: what about speech priming? -->

|Lekérdezési sztring|Típus|Példaérték|Cél|
|--|--|--|--|
|részletes|logikai|true|Tartalmaznak [összes leképezési pontszámok](#include-all-predicted-intent-scores) a utterance|
|timezoneOffset|szám (egység érték perc)|60|Állítsa be [időzóna-eltolás](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) a [datetimeV2 előre elkészített entitások](luis-reference-prebuilt-datetimev2.md)|
|a helyesírás-ellenőrzési|logikai|true|[a helyesírás](#enable-bing-spell-checker) a utterance--bing helyesírás-ellenőrzés-előfizetés-kulcs lekérdezési karakterlánc paramétert együtt használható|
|Bing helyesírás-ellenőrzés-előfizetés-kulcs|előfizetés-azonosító||a helyesírás-ellenőrzési lekérdezési karakterlánc paramétert együtt használható|
|átmeneti|logikai|false|Válassza ki az átmeneti vagy üzemi végpont|
|napló|logikai|true|lekérdezés és eredményeket hozzáadása|


## <a name="test-your-published-endpoint-in-a-browser"></a>A közzétett végpontot egy böngészőben tesztelése
Tesztelje a közzétett végpont URL-cím kiválasztása a **végpont** oszlop. Az alapértelmezett böngésző megnyitja a létrehozott URL-címet. Az URL-paramétert állítsa "& q" a teszt lekérdezéshez. Például hozzáfűzése `&q=Book me a flight to Boston on May 4` a URL-címet, és nyomja le az ENTER billentyűt. A böngésző ugyanúgy megjeleníti a HTTP-végpont a JSON-választ. 

![A közzétett HTTP-végpont a JSON-válasz](./media/luis-how-to-publish-app/luis-publish-app-json-response.png)

## <a name="next-steps"></a>További lépések

* Lásd: [kulcsok kezelése](./Manage-Keys.md) kulcs hozzáadása az LUIS alkalmazást, és hogyan kulcsok régiók hozzárendelését megismerése.
* Lásd: [tanítási és az alkalmazás tesztelése a](interactive-test.md) útmutatást a közzétett alkalmazás tesztelése a test-konzolon.
