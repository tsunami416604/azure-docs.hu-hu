---
title: Funkciók – LUIS
description: Adjon hozzá funkciókat a nyelvi modellhez, hogy javaslatokat nyújtson a címkével vagy osztályozással ellátott bemenetek felismeréséhez.
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: e0fd4470c9e1c2a56562b3783010ff1ef87ff466
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682160"
---
# <a name="machine-learning-ml-features"></a>Gépi tanulás (ML) funkciói

A gépi tanulásban a **funkció**   a rendszer által megfigyelt adattípusok megkülönböztető tulajdonságai vagy adatattribútuma.

A Machine learning-funkciók lehetővé teszik a LUIS számára, hogy hol találják meg a koncepciót megkülönböztető dolgokat. Arra utalnak, hogy LUIS használható, de nem rögzített szabályok.  Ezeket a mutatókat a címkékkel együtt használva keresheti meg az adatgyűjtést.

 A LUIS támogatja a kifejezések listáját, és más entitásokat is használ a szolgáltatásként:
* Kifejezés-lista funkció
* Modell (szándék vagy entitás) szolgáltatásként

A funkciókat a séma kialakításának szükséges részévé kell tekinteni.

## <a name="a-phrase-list-for-a-particular-concept"></a>Egy adott fogalomhoz tartozó kifejezések listája

A kifejezések listája egy adott fogalom beágyazására szolgáló szavak vagy kifejezések listája.

A kifejezések listájának hozzáadásakor a következőképpen állíthatja be a szolgáltatást:
* **[Globális](#global-features)**. A globális funkció a teljes alkalmazásra vonatkozik.

### <a name="when-to-use-a-phrase-list"></a>Mikor kell használni a kifejezések listáját

Ha a LUIS-alkalmazásnak képesnek kell lennie általánosítani és azonosítani a fogalom új elemeit, használjon egy kifejezés listát. A kifejezések listája hasonló a tartományhoz tartozó szókincshez, amely segít a szándékok és az entitások megismerésének javításában.

### <a name="how-to-use-a-phrase-list"></a>Kifejezések listájának használata

A kifejezést tartalmazó listával LUIS a kontextust és az általánosításokat úgy tekinti meg, hogy azonosítsa a hasonló elemeket, de nem pontos szöveges egyezést.

A kifejezések listájának használatának lépései:
* Indítás gépi tanulással rendelkező entitással
    * Példa kimondott szövegek hozzáadása
    * Címke gépi tanulási entitással
* Kifejezések listájának hozzáadása
    * Szavak hozzáadása hasonló jelentéssel – ne **adjon hozzá** minden lehetséges szót vagy kifejezést. Ehelyett adjon hozzá néhány szót vagy kifejezést egyszerre, majd végezze el az újratanítást és a közzétételt.
    * Javasolt szavak áttekintése és hozzáadása

### <a name="a-typical-scenario-for-a-phrase-list"></a>Egy kifejezés listára jellemző forgatókönyv

A kifejezések listájának egy tipikus forgatókönyve az adott ötlethez kapcsolódó szavak kiemelése.

Olyan szavakra, amelyeknek szüksége lehet egy kifejezésre, hogy a jelentőségük felgyorsítsa az orvosi feltételeket. A feltételek konkrét fizikai, kémiai, terápiás vagy absztrakt jelentésekkel rendelkezhetnek. LUIS nem fogja tudni, hogy a feltételek nem fontosak a tárgy tartománya számára kifejezési lista nélkül.

Ha ki szeretné bontani az orvosi feltételeket:
* Először hozzon létre például hosszúságú kimondott szöveg és címkézett orvosi kifejezéseket a hosszúságú kimondott szöveg belül.
* Ezután hozzon létre egy kifejezéseket tartalmazó listát a tárgy tartományon belüli kifejezésekre vonatkozó példákkal. A kifejezések listájának tartalmaznia kell a ténylegesen felcímkézett kifejezést és más, ugyanezen fogalmakat leíró kifejezéseket.
* Adja hozzá a kifejezés listát ahhoz a entitáshoz vagy alentitáshoz, amely kibontja a kifejezés listában használt fogalmat. A leggyakoribb forgatókönyv a gépi tanulási entitások egyik összetevője (gyermeke). Ha a kifejezések listáját az összes leképezésben vagy entitásban alkalmazni kell, akkor a kifejezések listáját a globális kifejezés listaként kell megjelölnie. A `enabledForAllModels` jelző a modell hatókörét szabályozza az API-ban.

<a name="how-to-use-phrase-lists"></a>
<a name="how-to-use-a-phrase-lists"></a>
<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="a-model-as-a-feature-helps-another-model"></a>A modell mint szolgáltatás segít egy másik modellben

Hozzáadhat egy modellt (szándék vagy entitás) egy másik modell (cél vagy entitás) szolgáltatásként. Egy meglévő szándék vagy entitás szolgáltatásként való hozzáadásával egy jól definiált fogalmat adhat hozzá a címkével ellátott példákkal.

A modell szolgáltatásként való hozzáadásakor a következőképpen állíthatja be a szolgáltatást:
* **[Kötelező megadni](#required-features)**. Meg kell találni egy szükséges szolgáltatást ahhoz, hogy a modellt vissza lehessen adni az előrejelzési végpontból.
* **[Globális](#global-features)**. A globális funkció a teljes alkalmazásra vonatkozik.

### <a name="when-to-use-an-entity-as-a-feature-to-an-intent"></a>Ha egy entitást szolgáltatásként kíván használni egy szándékhoz

Vegyen fel egy entitást szolgáltatásként, ha az adott entitás észlelése jelentős a szándék szempontjából.

Ha például a cél egy járat foglalása, `BookFlight` és az entitás a jegy információi (például a munkahelyek száma, a forrás és a célhely), akkor a jegy információs entitásának megkeresésével jelentős súlyt kell adni a szándék előrejelzéséhez `BookFlight` .

### <a name="when-to-use-an-entity-as-a-feature-to-another-entity"></a>Az entitás szolgáltatásként való használata egy másik entitáshoz

Egy entitást (A) a szolgáltatásként egy másik entitáshoz (B) kell hozzáadni, ha az adott entitás (A) észlelése jelentős az entitás (B) előrejelzéséhez.

Ha például az n szállítási címek entitása egy utcai címet tartalmazó alentitást foglalt, akkor az utca címe alentitása jelentős súlyozást biztosít a szállítási címek entitásának előrejelzéséhez.

* Szállítási címe (gépi megtanult entitás)
    * Utca száma (alentitás)
    * Utca címe (alentitás)
    * Város (alentitás)
    * Állam vagy megye (alentitás)
    * Ország (alentitás)
    * Irányítószám (alentitás)

## <a name="nested-subentities-with-features"></a>Beágyazott alentitások funkciókkal

Egy gépi megtanult alentitás jelzi, hogy a szülő entitásnak van-e egy másik alentitása vagy a felső entitása. Az alentitások értéke szolgáltatásként viselkedik a szülője számára.

Az alentitások tartalmazhatnak egy kifejezési listát is, valamint egy modellt (egy másik entitást) a szolgáltatásként.

Ha az alentitásban szerepel egy kifejezés lista, ez növeli a koncepció szókincsét, de nem ad hozzá semmilyen információt az előrejelzés JSON-válaszához.

Ha az alentitás egy másik entitás szolgáltatásával rendelkezik, a JSON-válasz tartalmazza a másik entitás kinyert értékeit.

## <a name="required-features"></a>Szükséges szolgáltatások

Meg kell találni egy szükséges szolgáltatást ahhoz, hogy a modellt vissza lehessen adni az előrejelzési végpontból. Használjon kötelező szolgáltatást, ha tudja, hogy a bejövő adatainak meg kell egyezniük a szolgáltatással.

Ha a kiírás szövege nem egyezik meg a szükséges funkcióval, nem lesz kibontva.

**Egy kötelező szolgáltatás nem gépi megtanult entitást használ**:
* Reguláriskifejezés-entitás
* Listaentitás
* Előre elkészített entitás

Milyen hasznos funkciókat kell megadnia a kötelezően? Ha biztos abban, hogy a modell az adataiban található, a szolgáltatást szükség szerint állítsa be. Egy szükséges szolgáltatás nem ad vissza semmit, ha nem található.

Folytassa a szállítási címek példáját:
* Szállítási címe (gépi megtanult entitás)
    * Utca száma (alentitás)
    * Utca címe (alentitás)
    * Utca neve (alentitás)
    * Város (alentitás)
    * Állam vagy megye (alentitás)
    * Ország (alentitás)
    * Irányítószám (alentitás)

### <a name="required-feature-using-prebuilt-entities"></a>Kötelező funkció előre elkészített entitások használatával

A város, az állam és az ország általában egy lezárt listát jelenít meg, ami azt jelenti, hogy az idő múlásával nem változnak. Ezek az entitások rendelkezhetnek a megfelelő ajánlott funkciókkal, és ezek a funkciók kötelezőként jelölhetők meg. Ez azt jelenti, hogy a teljes szállítási címet nem adja vissza a rendszer nem találja a szükséges szolgáltatásokat tartalmazó entitásokat.

Mi a helyzet abban az esetben, ha a város, az állam vagy az ország teljes mértékben szerepel, de olyan helyen vagy szlengben, amelyet LUIS nem vár? Ha szeretne valamilyen utólagos feldolgozást végezni, hogy segítsen megoldani az entitást, mivel a LUIS alacsony megbízhatósági pontszáma miatt nem kell megjelölnie a szolgáltatást.

Egy másik példa a szállítási címnek egy szükséges szolgáltatására, hogy az utca egy szükséges [előre elkészített](luis-reference-prebuilt-entities.md) szám legyen. Ez lehetővé teszi, hogy a felhasználó "1 Microsoft Way" vagy "egy Microsoft Way" értéket adjon meg. Mindkettő az utcai szám alentitáshoz tartozó "1" értékre lesz feloldva.

### <a name="required-feature-using-list-entities"></a>Kötelező szolgáltatás a lista entitások használatával

A [lista entitások](reference-entity-list.md) a kanonikus nevek listáját használják a szinonimákkal együtt. Szükség esetén, ha a kihagyás nem tartalmazza a kanonikus nevet vagy a szinonimát, az entitás nem kerül vissza az előrejelzési végpont részeként.

Ha továbbra is a szállítási címet szeretné megállapítani, tegyük fel, hogy a vállalat csak korlátozott számú országba szállít. Létrehozhat egy list entitást, amely többféle módon hivatkozhat az országra. Ha a LUIS nem talál pontos egyezést a kiírás szövegében, akkor az adott entitás (amely a lista entitás szükséges funkciójával rendelkezik) nem jelenik meg az előrejelzésben.

|Kanonikus név|Szinonimák|
|--|--|
|Egyesült Államok|Egyesült Államok<br>U. S. A<br>USA<br>USA<br>0|

Az ügyfélalkalmazás, például a csevegési robot felteheti a kérdést, így az ügyfél megérti, hogy az ország kiválasztása korlátozott és _kötelező_.

### <a name="required-feature-using-regular-expression-entities"></a>Reguláris kifejezési entitásokat használó kötelező funkció

A kötelező szolgáltatásként használt [reguláris kifejezési entitások](reference-entity-regular-expression.md) gazdag szöveg-egyeztetési képességeket biztosítanak.

Ha folytatja a szállítási címet, létrehozhat egy reguláris kifejezést, amely rögzíti az országbeli postai kódok szintaxisának szabályait.

## <a name="global-features"></a>Globális funkciók

Habár a leggyakoribb a funkció alkalmazása egy adott modellre, a szolgáltatás **globális szolgáltatásként** is konfigurálható a teljes alkalmazásra való alkalmazáshoz.

A globális szolgáltatás leggyakoribb felhasználási célja, hogy további szókincset (például más nyelvből származó szavakat) adjon hozzá az alkalmazáshoz. Ha az ügyfelek az elsődleges nyelvet használják, de az is előfordulhat, hogy egy adott kifejezésen belül más nyelvet kíván használni, hozzáadhat egy olyan funkciót, amely a másodlagos nyelvből származó szavakat tartalmaz.

Mivel a felhasználónak a második nyelvet kell használnia bármilyen szándékon vagy entitáson, hozzá kell adni egy kifejezési listához a globális szolgáltatásként konfigurált kifejezési listával.

## <a name="best-practices"></a>Ajánlott eljárások
Ismerje meg az [ajánlott eljárásokat](luis-concept-best-practices.md).

## <a name="next-steps"></a>További lépések

* Az alkalmazás modelljeinek [kiterjesztése](schema-change-prediction-runtime.md) előrejelzési futtatókörnyezetben
* További információ a szolgáltatások hozzáadása a LUIS-alkalmazáshoz [című témakörben](luis-how-to-add-features.md) talál további információt.
