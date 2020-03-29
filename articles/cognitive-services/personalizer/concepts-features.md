---
title: 'Jellemzők: Akció és kontextus - Personalizer'
titleSuffix: Azure Cognitive Services
description: A Personalizer funkciókat, műveletekkel és környezetekkel kapcsolatos információkat használ a jobb rangsorolási javaslatok érdekében. A funkciók lehetnek nagyon általánosak, vagy egy adott elemre jellemzőek lehetnek.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 408501232891a7971d03c89acc647d9ed19609b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77026149"
---
# <a name="features-are-information-about-actions-and-context"></a>A funkciók a műveletekre és a környezetre vonatkozó információk

A Personalizer szolgáltatás úgy működik, hogy megtanulja, mit kell mutatnia az alkalmazásnak a felhasználók számára egy adott környezetben.

A Personalizer **olyan funkciókat**használ , amelyek az **aktuális környezettel** kapcsolatos információkkal választják ki a legjobb **műveletet**. A funkciók minden olyan információt képviselnek, amely ről úgy gondolja, hogy segíthet a személyre szabásban a magasabb jutalmak elérése érdekében. A funkciók lehetnek nagyon általánosak, vagy egy adott elemre jellemzőek lehetnek. 

Előfordulhat például, hogy van egy **funkciója** a következőkről:

* A _felhasználói személyiség,_ `Sports_Shopper`például a . Ez nem lehet egyéni felhasználói azonosító. 
* A _tartalom,_ például ha `Documentary`egy `Movie`videó `TV Series`a , a , vagy a, vagy hogy egy kiskereskedelmi cikk elérhető-e a boltban.
* Az _aktuális_ időszak, például a hét melyik napja.

A Personalizer nem írja elő, nem korlátozza és nem javítja ki, hogy milyen funkciókat küldhet a műveletekhez és a környezethez:

* Bizonyos funkciókat elküldhet bizonyos műveletekhez, másokhoz nem, ha nem rendelkezik velük. Előfordulhat például, hogy a tv-sorozatok olyan jellemzőket mutatnak, amelyekkel a filmek nem rendelkeznek.
* Előfordulhat, hogy néhány funkció csak néhány alkalommal érhető el. Előfordulhat például, hogy egy mobilalkalmazás több információt ad meg, mint egy weboldal. 
* Idővel hozzáadhat és eltávolíthat funkciókat a környezetről és a műveletekről. A Personalizer továbbra is tanul a rendelkezésre álló információkból.
* A környezetnek legalább egy jellemzőnek kell lennie. A Personalizer nem támogatja az üres környezetet. Ha minden alkalommal csak rögzített környezetet küld, a Personalizer csak a műveletek funkcióira vonatkozóan választja ki a rangsorolási műveletet.
* A kategorikus funkciók esetében nem kell meghatározni a lehetséges értékeket, és nem kell előre definiálni a numerikus értékek tartományait.

## <a name="supported-feature-types"></a>Támogatott szolgáltatástípusok

A Personalizer támogatja a karakterlánc-, numerikus és logikai típusok jellemzőit.

### <a name="how-choice-of-feature-type-affects-machine-learning-in-personalizer"></a>Hogyan befolyásolja a szolgáltatástípus kiválasztása a Gépi tanulást a Personalizer-ban?

* **Karakterláncok:** Karakterlánc-típusok esetén a kulcs és az érték minden kombinációja új súlyokat hoz létre a Personalizer gépi tanulási modellben. 
* **Numerikus**: Számértékeket kell használni, ha a számnak arányosan kell befolyásolnia a személyre szabás eredményét. Ez nagyon forgatókönyv függő. -ban egy leegyszerűsített példa e.g. mikor personalizing egy kicsiben elad tapasztalat, NumberOfPetsOwned tudna lenni vonás ez minden numerikus mint ön május akar emberek -val 2 vagy 3 pets -hoz befolyásol a personalization eredmény kétszer vagy háromszor annyi mint birtoklás 1 kisállat. A numerikus egységeken alapuló, de nem lineáris – például kor, hőmérséklet vagy személymagasság – jellemzők a legjobb karakterláncokként vannak kódolva, és a szolgáltatás minősége általában tartományok használatával javítható. Például, Kor lehet kódolni, mint "Kor":"0-5", "Kor":"6-10", stb.
* A "hamis" értékkel küldött **logikai** értékek úgy viselkednek, mintha egyáltalán nem lettek volna elküldve.

A nem jelen lévő szolgáltatásokat ki kell hagyni a kérelemből. Kerülje a null értékű szolgáltatások küldését, mert a modell betanításakor a rendszer meglévőként és "null" értékkel dolgozza fel.

## <a name="categorize-features-with-namespaces"></a>Szolgáltatások kategorizálása névterekkel

A Personalizer névterekbe rendezett funkciókat vesz fel. Az alkalmazásban meghatározhatja, hogy használnak-e névtereket, és mik legyenek. A névterek egy hasonló témakör vagy egy bizonyos forrásból származó szolgáltatások szolgáltatásainak csoportosítására szolgálnak.

Az alábbi példák az alkalmazások által használt szolgáltatásnévterekre mutatnak be:

* User_Profile_from_CRM
* Time
* Mobile_Device_Info
* http_user_agent
* Videofelbontás
* UserDeviceInfo
* Időjárás
* Product_Recommendation_Ratings
* current_time
* NewsArticle_TextAnalytics

A saját konvenciók szerint elnevezheti a szolgáltatás névterét, feltéve, hogy azok érvényes JSON-kulcsok. A névterek a szolgáltatások különböző készletekbe rendezésére, valamint a hasonló nevű szolgáltatások félrefogalmazására szolgálnak. A névterek a szolgáltatásnevekhez hozzáadott "előtagként" is felfoghatók. A névterek nem ágyazhatók egymásba.


A következő `user`JSON, `state`, `device` és a szolgáltatás névterek. 

> [!Note]
> Jelenleg javasoljuk, hogy az UTF-8 alapú és különböző betűkkel kezdődő szolgáltatásnévterek neveit használja. Például `user`, `state`, `device` és `u` `s`kezdje `d`a , és . Az első karakterekkel rendelkező névterek jelenleg ütközéseket eredményezhetnek a gépi tanuláshoz használt indexekben.

A JSON-objektumok tartalmazhatnak egymásba ágyazott JSON-objektumokat és egyszerű tulajdonságot/értékeket. A tömb csak akkor vehető fel, ha a tömbelemek számok. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "profileType":"AnonymousUser",
                "latlong": [47.6, -122.1]
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

### <a name="restrictions-in-character-sets-for-namespaces"></a>A névterek karakterkészleteire vonatkozó korlátozások

A névtér elnevezéséhez használt karakterláncnak bizonyos korlátozásokat kell követnie: 
* Nem lehet Unicode.
* A névtérnevekhez használhat néhány nyomtatható szimbólumot < 256-os kódokkal. 
* Nem használhat szimbólumokat < 32 (nem nyomtatható), 32 (szóköz), 58 (kettőspont), 124 (cső) és 126–140.
* Nem szabad aláhúzásjellel "_" kezdetű, különben a funkció figyelmen kívül lesz hagyva.

## <a name="how-to-make-feature-sets-more-effective-for-personalizer"></a>Hogyan lehet a funkciókészleteket hatékonyabbá tenni a Personalizer számára?

Egy jó funkciókészlet segít a Personalizernek megtanulni, hogyan kell megjósolni azt a műveletet, amely a legmagasabb jutalmat fogja meghajtani. 

Fontolja meg a personalizer rank API-nak küldött funkciók küldését, amelyek az alábbi ajánlásokat követik:

* Van elég funkció a személyre szabáshoz. Minél pontosabban célozza meg a tartalmat, annál több funkcióra van szükség.

* Van elég jellemzője a különböző *sűrűségű*. A funkció *sűrű,* ha sok elem van csoportosítva néhány vödörben. Például több ezer videó minősíthető "Hosszú" (több mint 5 min hosszú) és "Rövid" (5 év alatt hosszú) kategóriába. Ez egy *nagyon sűrű* tulajdonság. Másrészt, ugyanaz a több ezer elem lehet egy attribútum az úgynevezett "Cím", amely szinte soha nem lesz ugyanaz az érték az egyik elem a másikra. Ez egy nagyon nem sűrű vagy *ritka* funkció.  

A nagy sűrűségű funkciók segítenek a Personalizer számára, hogy az egyik elemről a másikra tanuljon. De ha csak néhány funkció van, és túl sűrűek, a Personalizer megpróbálja pontosan megcélozni a tartalmat csak néhány vödör közül választhat.

### <a name="improve-feature-sets"></a>A szolgáltatáskészletek fejlesztése 

Elemezze a felhasználói viselkedést egy kapcsolat nélküli kiértékeléssel. Ez lehetővé teszi, hogy tekintse meg a korábbi adatokat, hogy milyen funkciók jelentősen hozzájárulnak a pozitív jutalmak szemben azokkal, amelyek hozzájárulnak kevesebb. Láthatjuk, hogy milyen funkciók segítenek, és ez lesz rajtad múlik, és az alkalmazás, hogy jobb funkciókat küldeni Personalizer eredmények javítása érdekében még tovább.

A következő szakaszok a Personalizer-nak küldött funkciók javításának általános gyakorlatai.

#### <a name="make-features-more-dense"></a>A funkciók sűrűbbé adják

Lehetőség van a funkciókészletek fejlesztésére szerkesztéssel, hogy nagyobbak és többé-kevésbé sűrűek legyenek.

Például egy időbélyeg le a második egy nagyon ritka funkció. Az időpontok "reggel", "dél", "délután" stb.

A helyadatok általában szélesebb körű besorolások létrehozásának előnyeit is élvezik. Például egy szélességi-hosszúsági koordináta, mint a Lat: 47.67402° N, Hosszú: 122.12154° W túl pontos, és arra kényszeríti a modellt, hogy a szélességi és hosszúsági fokot különböző méretként tanulja meg. Amikor a helyadatok alapján próbál személyre szabni, segít a nagyobb szektorokban lévő helyadatok csoportosításában. Egy egyszerű módja annak, hogy az, hogy válasszon egy megfelelő kerekítési pontossága a Lat-Long számok, és összekapcsolják szélességi és hosszúsági a "területek" azáltal, hogy azok egy string. Például, egy jó módja annak, hogy képviselje 47.67402° N, Hosszú: 122.12154° W régiókban körülbelül néhány kilométer széles lenne "location":"34.3 , 12.1".


#### <a name="expand-feature-sets-with-extrapolated-information"></a>A szolgáltatáskészletek kibontása extrapolált információkkal

Ön is kap több funkciót gondolkodás feltáratlan attribútumok, hogy lehet levezetni az információ már van. Például egy fiktív filmlista személyre szabásában lehetséges, hogy egy hétvége és hétköznap okozzák a felhasználók tól eltérő viselkedést? Az idő kiterjeszthető egy "hétvégi" vagy "hétköznap" attribútumra. A nemzeti kulturális ünnepek bizonyos filmtípusokra irányulnak? Például a "Halloween" attribútum olyan helyeken hasznos, ahol releváns. Lehetséges, hogy az esős időjárás jelentős hatással van a választás egy film sok ember számára? Az idő és a hely, a meteorológiai szolgálat is biztosítja ezt az információt, és felveheti, mint egy extra funkció. 

#### <a name="expand-feature-sets-with-artificial-intelligence-and-cognitive-services"></a>Bővítse ki a funkciókészleteket mesterséges intelligenciával és kognitív szolgáltatásokkal

A mesterséges intelligencia és a használatra kész Kognitív szolgáltatások a Personalizer nagyon hatékony kiegészítője lehet. 

A termékek mesterséges intelligencia szolgáltatások használatával történő előfeldolgozásával automatikusan kinyerheti azokat az információkat, amelyek valószínűleg relevánsak a személyre szabás szempontjából.

Példa:

* A [videoindexelőn](https://azure.microsoft.com/services/media-services/video-indexer/) keresztül futtathatja a filmfájlokat a jelenetelemek, a szöveg, a hangulat és sok más attribútum kinyeréséhez. Ezek az attribútumok ezután sűrűbbé tehetők, hogy tükrözzék azokat a jellemzőket, amelyekkel az eredeti elem metaadatai nem rendelkeztek. 
* A képek objektumfelismerésen, arcokon keresztül, érzelmeken stb.
* A szövegben lévő információk kiegészíthetők entitások, hangulatok, entitások kibontásával bing tudásgráftal stb.

Számos más [Azure Cognitive Services-t](https://www.microsoft.com/cognitive-services)is használhat, például

* [Entitáskapcsolás](../entitylinking/home.md)
* [Szövegelemzés](../text-analytics/overview.md)
* [Érzelemfelismerés](../emotion/home.md)
* [Számítógépes látás](../computer-vision/home.md)

## <a name="actions-represent-a-list-of-options"></a>A műveletek a lehetőségek listáját jelölik

Minden egyes művelet:

* _Eseményazonosítója_ van. Ha már rendelkezik eseményazonosítóval, azt be kell nyújtania. Ha nem rendelkezik eseményazonosítóval, ne küldjön egyet, a Personalizer létrehoz egyet az Ön számára, és a Rang kérés válaszában visszaküldi azt. Az azonosító a Rangsorolás id-hoz van társítva, nem a felhasználóhoz. Ha létrehoz egy azonosítót, a GUID működik a legjobban. 
* Van egy listája a funkciók.
* A funkciók listája nagy lehet (több száz), de javasoljuk a funkciók hatékonyságának kiértékelését, hogy eltávolíthassuk azokat a funkciókat, amelyek nem járulnak hozzá a jutalmak megszerzéséhez. 
* A műveletek **funkciói** nak lehet, hogy nincs korrelációja a Personalizer által használt **környezetben** lévő funkciókkal.
* Egyes műveletekben a műveletek funkciói lehetnek jelen, mások nem. 
* Előfordulhat, hogy egy bizonyos műveletazonosító funkciói egy nap elérhetők, de később elérhetetlenné válnak. 

A Personalizer gépi tanulási algoritmusai jobban teljesítenek, ha stabil funkciókészletek vannak, de a Ranghívások nem fognak sikertelenek, ha a szolgáltatáskészlet idővel megváltozik.

Ne küldjön 50-nél több műveletet a Rangsorolási műveletek nél. Ezek lehetnek ugyanaz 50 akciók minden alkalommal, vagy változhatnak. Ha például egy e-kereskedelmi alkalmazáshoz 10 000 cikkből álló termékkatalógusa van, akkor egy ajánlási vagy szűrőmotor segítségével meghatározhatja, hogy az ügyfél nek mi a célja, és a Personalizer segítségével keresse meg azt, amelyik a legtöbb jutalmat generálja (például , a felhasználó hozzáadja a kosárhoz) az aktuális környezetben.


### <a name="examples-of-actions"></a>Példák a műveletekre

A Rangsorolás api-nak küldött műveletek attól függnek, hogy mit próbál személyre szabni.

Néhány példa:

|Cél|Műveletek|
|--|--|
|Szabja személyre, hogy melyik cikk van kiemelve egy hírportálon.|Minden művelet egy lehetséges hírcikk.|
|Optimalizálja a hirdetéselhelyezést egy webhelyen.|Minden művelet egy elrendezés vagy szabályok lesz, amelyek elrendezést hoznak létre a hirdetésekhez (például a tetején, a jobb oldalon, kis képek, nagy képek).|
|Az ajánlott elemek személyre szabott rangsorolását jelenítheti meg egy vásárlási webhelyen.|Minden művelet egy adott termék.|
|Javasoljon felhasználói felületi elemeket, például szűrőket egy adott fényképre.|Minden művelet lehet egy másik szűrő.|
|Válaszd ki egy csevegőrobot válaszát a felhasználói szándék tisztázása vagy művelet javaslata érdekében.|Minden művelet egy lehetőség, hogyan kell értelmezni a választ.|
|Válassza ki, hogy mit jelenítsen meg a keresési eredmények listájának tetején|Minden művelet egyike a néhány legnépszerűbb keresési eredménynek.|


### <a name="examples-of-features-for-actions"></a>Példák a műveletekhez szükséges funkciókra

Az alábbiakban jó példákat mutatunk be a műveletekhez szükséges funkciókra. Ezek nagyban függ az egyes alkalmazások.

* Jellemzők jellemzői a cselekvések. Például, ez egy film vagy egy tv-sorozat?
* Az azzal kapcsolatos szolgáltatások, hogy a felhasználók hogyan léphettek kapcsolatba ezzel a művelettel a múltban. Például, ez a film többnyire látható az emberek demográfiai A vagy B, ez általában játszott nem több, mint egy alkalommal.
* A műveletek felhasználói ként való *látható* jellemzői. Például a miniatűrben látható film posztere imposztákat, autókat vagy tájakat tartalmaz?

### <a name="load-actions-from-the-client-application"></a>Műveletek betöltése az ügyfélalkalmazásból

A műveletek funkciói általában tartalomkezelő rendszerekből, katalógusokból és ajánlórendszerekből származhatnak. Az alkalmazás feladata a műveletekre vonatkozó információk betöltése a megfelelő adatbázisokból és rendszerekből. Ha a műveletek nem változnak, vagy egyre betöltve minden alkalommal szükségtelen hatással van a teljesítményre, az alkalmazás logika hozzáadásával gyorsítótárazhatja ezeket az információkat.

### <a name="prevent-actions-from-being-ranked"></a>A műveletek rangsorolásának megakadályozása

Bizonyos esetekben vannak olyan műveletek, amelyeket nem szeretne megjeleníteni a felhasználók számára. A legjobb módja annak, hogy megakadályozzák a cselekvés rangsorolását, mint legfelső, hogy nem tartalmazza azt a műveletlistában a Rang API-t az első helyen.

Bizonyos esetekben csak akkor határozható meg az üzleti logika későbbi részében, ha a Rangsor API-hívás eredményül kapott _műveletét_ meg kell jelenlennie a felhasználó nak. Ezekben az esetekben az Inaktív események et kell _használnia._

## <a name="json-format-for-actions"></a>JSON formátum műveletekhez

A Rang hívásakor több művelet közül választhat:

A JSON-objektumok tartalmazhatnak egymásba ágyazott JSON-objektumokat és egyszerű tulajdonságot/értékeket. A tömb csak akkor vehető fel, ha a tömbelemek számok. 

```json
{
    "actions": [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium",
          "grams": [400,800]
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
          "spiceLevel": "none",
          "grams": [150, 300, 450]
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
          "spiceLevel": "none",
          "grams": [300, 600, 900]
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
          "spiceLevel": "low",
          "grams": [300, 600]
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ]
}
```

## <a name="examples-of-context-information"></a>Példák a környezeti információkra

A _környezetre_ vonatkozó információk az egyes alkalmazásoktól és használati esettől függnek, de általában olyan információkat tartalmazhatnak, mint például:

* A felhasználó demográfiai és profiladatai.
* HTTP-fejlécekből , például felhasználói ügynökből kinyert információ, vagy HTTP-adatokból, például IP-címeken alapuló név-földrajzi keresési adatokból származtatott adatok.
* Információ az aktuális időről, például a hét napja, hétvége vagy sem, reggel vagy délután, ünnepi szezon vagy sem stb.
* Mobil alkalmazásokból kinyert adatok, például hely, mozgás vagy akkumulátortöltöttség.
* A felhasználók viselkedésének történelmi aggregátumai - például mik azok a filmműfajok, amelyeket ez a felhasználó a leginkább megtekintett.

Az alkalmazás feladata a környezettel kapcsolatos információk betöltése a megfelelő adatbázisokból, érzékelőkből és rendszerekből. Ha a környezeti adatok nem változnak, hozzáadhat logikát az alkalmazásban, hogy gyorsítótárazza ezeket az információkat, mielőtt elküldené a Rank API-nak.

## <a name="json-format-for-context"></a>JSON formátum a környezethez 

A környezet a Rang API-nak küldött JSON-objektumként van kifejezve:

A JSON-objektumok tartalmazhatnak egymásba ágyazott JSON-objektumokat és egyszerű tulajdonságot/értékeket. A tömb csak akkor vehető fel, ha a tömbelemek számok. 

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
                "Windows":true,
                "screensize": [1680,1050]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>További lépések

[Megerősítő tanulás](concepts-reinforcement-learning.md) 
