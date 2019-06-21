---
title: 'Funkciók: A művelet és a környezet – Personalizer'
titleSuffix: Azure Cognitive Services
description: Personalizer jobb rangsorolási javaslatokat funkciókat, műveleteket és a környezetben használja. Szolgáltatások nagyon általános vagy adott elem lehet.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: b2054aa963991ffa2d92aabf1ce896031f2d87fc
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296062"
---
# <a name="features-are-information-about-actions-and-context"></a>Funkciói a következők műveletek és a környezeti információk

A Personalizer szolgáltatás működik, tanulás, amit az alkalmazás kell megjelenítése a felhasználónak egy adott környezetben.

Personalizer használ **funkciók**, amely az kapcsolatos információ a **aktuális környezet** , válassza ki a legjobb **művelet**. A szolgáltatásokat jelölik úgy gondolja, hogy segítségére lehetnek a nagyobb profit érdekében személyre szabása minden információt. Szolgáltatások nagyon általános vagy adott elem lehet. 

Például előfordulhat, hogy rendelkezik egy **funkció** kapcsolatban:

* A _felhasználói_ például egy `UserID`. 
* A _tartalom_ például ha egy videó van egy `Documentary`, amely egy `Movie`, vagy egy `TV Series`, vagy e-kereskedelmi elem érhető el tárolójában.
* A _aktuális_ időszakának idő, például a hét mely napján legyen.

Personalizer nem írja elő, korlátozhatja, vagy hárítsa el a műveletek és a környezet küldhet funkciók:

* Egyes funkciók bizonyos műveletek másoknak pedig nem, ha nincs is küldhet. TV-sorozatának Előfordulhat például, filmek nincs attribútumok.
* Előfordulhat, egyes funkciói csak néhány alkalommal. Például egy mobilalkalmazás, mint a weblapon további információt tartalmaznak. 
* Az idő múlásával, előfordulhat, hogy szolgáltatások hozzáadására és eltávolítására vonatkozó környezeti és műveletek. Personalizer továbbra is fennáll, további információt a rendelkezésre álló információt.
* A környezet legalább egy szolgáltatás kell lennie. Personalizer nem támogatja az üres objektumkörnyezethez. Ha azt csak egy rögzített környezet küldése minden alkalommal, Personalizer választja ki módosítják a rangsort tartozó művelet csak az a funkciók kapcsolatos műveletek. 
* Válassza ki, amely a leginkább mindenki bármikor műveleteket personalizer megpróbálja.

## <a name="supported-feature-types"></a>Támogatott szolgáltatás típusa

Personalizer karakterlánc, numerikus és logikai típusú funkcióját támogatja.

### <a name="how-choice-of-feature-type-affects-machine-learning-in-personalizer"></a>Hogyan befolyásolja az szolgáltatástípus kiválasztása a Machine Learning Personalizer

* **Karakterláncok**: A karakterlánc típusú kulcs-érték minden egyes kombinációjához új súlyok a Personalizer gépi tanulási modellt hoz létre. 
* **Numerikus**: Numerikus értékeket kell használnia, ha a szám arányosan befolyásolhatja a személyre szabási eredményének. Ez a függő nagyon forgatókönyv. A példában egy egyszerűsített pl. amikor személyre szabásához kiskereskedelmi észlel, NumberOfPetsOwned lehet egy szolgáltatás, amely befolyásolhatja az kétszer vagy kevesebb mint háromszor kellene 1 kisállat személyre szabása eredménye 2 vagy 3 kisállatok rendelkező személyek érdemes numerikus van. Funkciók, amelyek numerikus egységek alapulnak, de ahol értelmében nem lineáris - kor, hőmérséklet vagy személy magasság - legjobb kódolt, karakterláncok és a szolgáltatás-minőségi általában javítása érdekében tartományok használatával. Például kora sikerült kódolni, "Age": "0-5", "Age": "6 – 10", stb.
* **Logikai** elküldött act "false" értékű, ha azok minden korábban elküldött értékek.

Az funkciók, amelyek nem találhatók a kérelem ki lehet hagyni. Kerülje a küldési szolgáltatások null értékű, mert fogja feldolgozni, meglévő, és a egy értéke "null" Ha a modell betanításához.

## <a name="categorize-features-with-namespaces"></a>A névterek funkcióinak kategorizálása

Personalizer vesz igénybe a szolgáltatásokat névtereket vannak rendezve. Azt állapítja meg, az alkalmazás, ha névtereket használ, és mit kell lenniük. Névterek használhatók hasonló témájú, illetve szolgáltatásait, amelyek egy adott forrásból származnak.

Példák az alkalmazások által használt szolgáltatás névterek a következők:

* User_Profile_from_CRM
* Time
* Mobile_Device_Info
* http_user_agent
* VideoResolution
* UserDeviceInfo
* Időjárás
* Product_Recommendation_Ratings
* current_time
* NewsArticle_TextAnalytics

A szolgáltatás névterek mindaddig, amíg azok érvényes JSON-kulcsokat a saját konvenciók következő nevet adhat.

A következő JSON-fájlban `user`, `state`, és `device` funkció névterekben vannak.

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "name":"Doug"
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true
            }
        }
    ]
}
```

## <a name="how-to-make-feature-sets-more-effective-for-personalizer"></a>Hogyan javíthatja a szolgáltatás hatékonyabb beállítja Personalizer

Egy jó szolgáltatáskészlet segítségével ismerje meg, hogy miként jelezhetők előre a műveletet, amelyek a legnagyobb ellenszolgáltatás Personalizer. 

Vegye figyelembe, hogy küldési szolgáltatások a Personalizer rang API, amely kövesse ezeket a javaslatokat:

* Nincsenek meghajtó személyre szabás elegendő funkciókat. Minél több pontosan megcélzott a tartalom kell lennie, a további funkciók szükségesek.

* Nincsenek elegendő funkcióit különféle *sűrűség*. A funkció *sűrű* Ha sok elem néhány gyűjtők szerint vannak csoportosítva. Például több ezer videókat, így csoportosíthatók, "Hosszú" (több mint 5 perc hosszú) és "Rövid" (5 perc alatt hosszú). Ez egy *nagyon sűrű* funkció. Az azonos ezer elem, másrészt "Title", amely szinte soha nem fog rendelkezni a ugyanazt az értéket az egyik cikkből a másikra nevű attribútum is rendelkezik. Ez egy nagyon nem-sűrű a vagy *ritka* funkció.  

A tanulás az egyik cikkből a másikra extrapolálja Personalizer kellene a nagy sűrűségű szolgáltatások segítségével. De ha csak néhány funkcióval, és azok túl sűrű, megpróbálja a Personalizer pontosan tartalmat csak néhány gyűjtők közül választhat a cél.

### <a name="improve-feature-sets"></a>Szolgáltatások javítása 

A felhasználói viselkedés elemzéséhez Offline értékelést kezelése végrehajtásával. Ez lehetővé teszi, hogy tekintse meg korábbi adatait, hogy lássa, milyen szolgáltatások erősen működik közre, amelyek kisebb működik közre és pozitív jutalmakat. Láthatja, hogy milyen funkciók segítenek, és határozhatják meg és az alkalmazás jobb szolgáltatásokat található küldendő Personalizer még tovább eredmények javítása érdekében.

Ezek a következő szakaszok olyan gyakori eljárások Personalizer küldött funkciók fejlesztése.

#### <a name="make-features-more-dense"></a>Győződjön meg, a szolgáltatások több sűrű

A szolgáltatáskészleteket szerkeszti őket, így nagyobb és több vagy kevesebb sűrű javítása érdekében lehetőség.

A második lefelé időbélyeg például az egyik nagyon ritka szolgáltatása. Azt sikerült végezhető további sűrű (tényleges) időpontok Írisz "reggeli", "csúcspontját", "délután", stb.


#### <a name="expand-feature-sets-with-extrapolated-information"></a>Bontsa ki a szolgáltatáskészleteket extrapolált adatokkal

További funkciók is beszerezheti a biztonságához felderítetlen attribútumok származhatnak információ áll rendelkezésére. Ha például egy fiktív movie lista személyre szabása, az, lehetséges, hogy a hétvégi vs hétköznap elhárítja a felhasználók különböző viselkedés? Idő lehetett kibontani a "hétvégi" vagy "hét napja" attribútuma. Tegye nemzeti kulturális ünnepek meghajtó figyelmet a film bizonyos? Ha például egy "Halloween" attribútum hasznos helyeken, ahol fontos. Az lehetséges, hogy Esős időjárási jelentős hatással van a film a kiválasztott sokak számára? Időben és helyen, az időjárás szolgáltatás biztosíthatja, hogy információkat, és hozzáadhatja egy külön szolgáltatás. 

#### <a name="expand-feature-sets-with-artificial-intelligence-and-cognitive-services"></a>Bontsa ki a mesterséges intelligencia és cognitive services szolgáltatáskészleteket

Mesterséges intelligencia és Cognitive Services futásra kész a Personalizer nagyon hatékony mellett is lehet. 

Előfeldolgozási az elemekről mesterségesintelligencia-szolgáltatásokkal, akkor automatikusan kinyerheti az valószínűleg testre szabási célból vonatkozó információkat.

Példa:

* Egy filmet fájl futtatása [Video Indexer](https://azure.microsoft.com/services/media-services/video-indexer/) jelenet elemeket, szöveg, hangulatát és számos más attribútum kibontásához. Ezek az attribútumok majd több sűrű megfelelően jellemzőit, amelyek az eredeti elem metaadatai nem lehet kapcsolódni. 
* Képek futtatható keresztül objektumfelismerés arcok a róluk szóló véleményeket, stb.
* Szöveges információ kiegészíthető oly módon, entitásokat, hangulatát, bővülő entitások és a Bing knowledge graph felületéhez, stb.

Több más [Azure Cognitive Services](https://www.microsoft.com/cognitive-services), például

* [Entitáskapcsolás](../entitylinking/home.md)
* [Szövegelemzés](../text-analytics/overview.md)
* [Érzelemfelismerési](../emotion/home.md)
* [Computer Vision](../computer-vision/home.md)

## <a name="actions-represent-a-list-of-options"></a>Műveletek beállítások listáját tartalmazzák.

Minden művelet:

* -Azonosítóval rendelkezik.
* Szolgáltatások listája szerepel.
* A szolgáltatások listájában lehet nagy (több száz), de javasoljuk, hogy értékelje a Funkciók, amelyek nem hozzájárulva jutalmakat első eltávolítandó funkció hatékonyságát. 
* Az a funkciók a **műveletek** előfordulhat, hogy nem rendelkezik minden olyan funkciókat a korrelációs a **környezet** Personalizer használják.
* Lehet, hogy a műveletek funkciók bizonyos műveleteket másoknak pedig nem található. 
* Az egy bizonyos művelet azonosítója szolgáltatásai előfordulhat, hogy rendelkezésre egy nap, de később már nem érhető el. 

Personalizer a gépi tanulási algoritmusok javítja a teljesítményt a szolgáltatás stabil eljáráscsoport, de rangsorolják hívások sikeresek lesznek, ha a szolgáltatás beállítása módosítások idővel.

Ne küldjön a több mint 50 műveletek során rangsorolási műveleteket. Lehet 50 ugyanazokat a műveleteket, ezek minden alkalommal, vagy azok változhatnak. Például ha 10 000 elemek egy e-kereskedelmi alkalmazás termék katalógust, használhat egy javaslat vagy szűrési motor ügyfél lehet, például, és Personalizer használatával (például hoz létre a legtöbb fejében, amelyen az első 40 meghatározásához a felhasználó hozzáadja a kosárhoz) az aktuális környezetben.


### <a name="examples-of-actions"></a>Tevékenységek

A rang API-t küld a műveletek próbált személyre függ.

Néhány példa:

|Cél|Műveletek|
|--|--|
|Személyre szabott, mely cikk kiemelt hírek-webhelyen.|Minden művelet lehetséges hír.|
|Egy webhelyen Active elhelyezési optimalizálása.|Minden művelet egy elrendezést vagy a szabályok létrehozása egy elrendezést a hirdetések (például a felül található a megfelelő, a kis képek, a big Data típusú rendszerképek) lesz.|
|Vásárlási webhelyek személyre szabott rangsorolási ajánlott elemek megjelenítése.|Minden művelet egy bizonyos termékben.|
|Javasoljuk, például egy adott fénykép alkalmazandó szűrők felhasználói felületi elemekre.|Minden művelet egy másik szűrőt is lehet.|
|Válassza ki a felhasználói szándékot tisztázása, vagy a javasolt művelet egy csevegőrobot választ.|Minden művelet, a válasz értelmezése lehetőség.|
|Válassza ki a felső részén a keresési eredmények listájában megjelenítése|Egyes műveletek egyike a felső néhány keresési eredmények között.|


### <a name="examples-of-features-for-actions"></a>Példák a szolgáltatások műveletek

A következő példák hasznos funkciót kínál a műveleteket. Ezek függ, sokkal minden kérelmet.

* A műveletek jellemzői a funkciókat. Ha például az, filmek és tv-sorozatának?
* Rendelkezik előfordulhat, hogy hogyan kezelhetők felhasználók Ez a művelet az elmúlt kapcsolatos funkciók. Ha például a film nagyrészt A vagy B demográfiai belüli személyek számára látható, általában játékkal legfeljebb egy ideje.
* Hogyan jellemzőit kapcsolatos funkciókat a felhasználó *látja* műveleteket. Például nem jelennek meg a miniatűr Belefoglalás arcok, autókból vagy környezetünk a film a poszter?

### <a name="load-actions-from-the-client-application"></a>Az ügyfélalkalmazás műveletek betöltése

A műveletek funkciók általában származhatnak a tartalomkezelő rendszerek katalógusok és ajánló rendszerek. Az alkalmazás felelős a műveletekkel kapcsolatos információk betöltése a megfelelő adatbázisokból és rendszerek rendelkezik. Ha a műveletek ne módosítsa vagy betöltött első minden alkalommal szükségtelen hatással van a teljesítményre, a logikai gyorsítótárazásához ezt az információt az alkalmazásban is hozzáadhat.

### <a name="prevent-actions-from-being-ranked"></a>Rangsorolt folyamatban a műveletek megakadályozása

Bizonyos esetekben vannak műveleteket, amelyeket nem szeretne megjeleníteni a felhasználók számára. A legjobb módja, hogy egy művelet folyamatban van az első szerint legfelső a beállítás először adja hozzá a műveleti lista a rang API.

Néhány esetben azt csak lehet meghatározni az üzleti logikát a későbbi Ha egy eredő _művelet_ egy rang API-hívás jelennek meg a felhasználó van. Ezekben az esetekben kell használnia _inaktív események_.

## <a name="json-format-for-actions"></a>JSON-formátumban műveletek

Rang hívásakor, amelyet el szeretne küldeni több művelet közül választhat:

```json
{
    "actions": [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium"
        },
        {
          "nutritionLevel": 5,
          "cuisine": "italian"
        }
      ]
    },
    {
      "id": "ice cream",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none"
        },
        {
          "nutritionalLevel": 2
        }
      ]
    },
    {
      "id": "juice",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none"
        },
        {
          "nutritionLevel": 5
        },
        {
          "drink": true
        }
      ]
    },
    {
      "id": "salad",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "low"
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ]
}
```

## <a name="examples-of-context-information"></a>Példák a környezeti információk

Adatait a _környezet_ egyes és használati esetekhez, attól függ, de ez általában tartalmaznak adatokat például:

* A felhasználó demográfiai és a profil adatait.
* HTTP-fejléceket, például a felhasználói ügynök kinyert vagy HTTP-adatokat, például IP-címek alapján földrajzi névkeresést származó információkkal.
* Az aktuális idő, például a hét hétvégi vagy nem, reggel vagy délután, az ünnepi időszakban kapcsolatos információkat, vagy nem, és így tovább.
* Mobil alkalmazások, például hely, áthelyezése vagy töltöttségi szint kinyert adatokat.
* A működés, a felhasználók – például, hogy mik a film műfajokat korábbi összesítések Ez a felhasználó tekintette meg a leginkább.

Az alkalmazás felelős a környezeti információk betöltése a vonatkozó adatbázis, érzékelők és rendszerek lehet. A környezet adatait nem változik, ha a logikai alkalmazásban való gyorsítótár ezt az információt, mielőtt elküldené a rang API is hozzáadhat.

## <a name="json-format-for-context"></a>JSON-formátumban környezethez. 

JSON-objektum a rang API-nak küldött környezet fejezzük ki:

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "name":"Doug"
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true
            }
        }
    ]
}
```

## <a name="next-steps"></a>További lépések

[Megerősítő tanulást](concepts-reinforcement-learning.md) 
