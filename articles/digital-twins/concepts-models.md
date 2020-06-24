---
title: Egyéni modellek
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogy az Azure Digital Twins hogyan használja a felhasználó által definiált modelleket a környezetben található entitások leírására.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: cacf4c21e92b434aeb73cd76e6dda26508f41d77
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85261358"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>A Twin modellek ismertetése az Azure Digital Twinsban

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Az Azure Digital Twins egyik fő jellemzője, hogy saját szókincset definiálhat, és saját maga által definiált üzleti feltételeit felépítheti. Ezt a képességet felhasználó által definiált **modelleken**keresztül biztosítjuk. A modelleket a világa leírásában szereplő nevekre lehet gondolni. 

A modell hasonlít egy objektumorientált programozási nyelv **osztályára** , amely egy adott koncepcióhoz tartozó adatalakzatot határoz meg a valós munkahelyi környezetben. A modellek névvel rendelkeznek (például a *Room* vagy a *hőmérséklet-érzékelő*), és olyan elemeket tartalmaznak, mint például a tulajdonságok, telemetria/események és parancsok, amelyek leírják, hogy milyen típusú entitást lehet a környezetben. Később ezeket a modelleket fogja használni, hogy olyan [**digitális ikreket**](concepts-twins-graph.md) hozzon létre, amelyek megfelelnek az ilyen típusú leírásnak megfelelő entitásoknak.

A modellek a JSON-alapú **Digital Twin Definition Language (DTDL) nyelv**használatával íródnak.  

## <a name="digital-twin-definition-language-dtdl-for-writing-models"></a>Digital Twin Definition Language (DTDL) modellek írásához

Az Azure Digital Twins modelljei a digitális Twins Definition Language (DTDL) használatával definiálhatók. A DTDL JSON-LD-alapú, és a programozási nyelvtől független. A DTDL nem kizárólagos az Azure Digital Twins-ban, de más IoT-szolgáltatásokban, például a [IoT Plug and Playban](../iot-pnp/overview-iot-plug-and-play.md)is használhatók. Az Azure Digital Twins a DTDL *2-es verzióját*használja.

> [!TIP] 
> Nem minden olyan szolgáltatás, amely a DTDL-t használja, pontosan ugyanazokat a funkciókat implementálja, mint a DTDL. Például a IoT Plug and Play nem használja a graphs szolgáltatáshoz használt DTDL-funkciókat, míg az Azure digitális Twins jelenleg nem implementál DTDL-parancsokat. Az Azure digitális Twins-ra jellemző DTDL-funkciókkal kapcsolatos további információkért tekintse meg a jelen cikk későbbi, az [Azure Digital Twins DTDL megvalósítási sajátosságai](#azure-digital-twins-dtdl-implementation-specifics)című szakaszát.

Az általános DTDL kapcsolatos további információkért tekintse meg a specifikációs dokumentációt a GitHubon: [Digital Twins Definition Language (DTDL) – 2. verzió](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

## <a name="elements-of-a-model"></a>A modell elemei

A modell definícióján belül a legfelső szintű kódrészlet egy **illesztőfelület**. Ez magában foglalja a teljes modellt, a modell többi része pedig az illesztőfelületen belül van definiálva. 

A DTDL-modell illesztőfelülete nulla, egy vagy több műveletet is tartalmazhat a következő mezők közül:
* **Tulajdonság** – a tulajdonságok olyan adatmezők, amelyek egy entitás állapotát jelölik (például a tulajdonságok számos objektumorientált programozási nyelven). A telemetria eltérően, amely egy időkorláttal rendelkező adat-esemény, a tulajdonságok biztonsági mentést végeznek, és bármikor olvashatók.
* Az **telemetria** -telemetria mezők mérési értékeket vagy eseményeket képviselnek, és gyakran használják az eszköz-érzékelők beolvasását. A telemetria nem digitális Twin tárolóban van tárolva. többek között az adatstreamek elküldésére készen állnak. 
* **Összetevő** – az összetevők lehetővé teszik, hogy a modell felületét más felületek szerelvényként hozza létre, ha szeretné. Egy összetevő például egy *frontCamera* felület (és egy másik összetevő-illesztőfelület *backCamera*), amely a modellnek a *telefonhoz*való definiálásához használatos. Először meg kell határoznia egy felületet a *frontCamera* , mintha a saját modellje lenne, majd a *telefon*definiálásakor hivatkozhat rá.

    Egy összetevővel leírhatja, hogy a megoldás szerves részét képezi-e, de nem igényel külön identitást, és nem kell egymástól függetlenül létrehozni, törölni vagy átrendezni a különálló gráfban. Ha azt szeretné, hogy az entitások a Twin gráfban független létezéssel rendelkezzenek, akkor a *kapcsolatok* (lásd a következő felsorolást) külön digitális twinsként képviseljék őket.
    
    >[!TIP] 
    >Az összetevők a szervezethez is felhasználhatók, és a kapcsolódó tulajdonságok csoportjai a modell felületén belül csoportosíthatók. Ebben az esetben az egyes összetevőket névtérként vagy "mappaként" tekintheti meg az illesztőfelületen belül.
* **Kapcsolat** – a kapcsolatok lehetővé teszik, hogy egy digitális Twin-et más digitális ikrek is felvegyen. A kapcsolatok különböző szemantikai jelentéseket jelenthetnek, például a következőt: *tartalmaz* ("a padló tartalmaz szobát"), *a (z) ("* HVAC Coolers Room"), a *isBilledTo* ("kompresszor számlázása felhasználó") stb. A kapcsolatok lehetővé teszik a megoldás számára az egymással összefüggő entitások gráfjának megadását.

> [!NOTE]
> A DTDL specifikációja olyan **parancsokat**is definiál, amelyek olyan metódusok, amelyek a digitális twin (például egy alaphelyzetbe állítási parancs, vagy a ventilátor be-és kikapcsolására szolgáló parancs) esetében hajthatók végre. *A parancsok azonban jelenleg nem támogatottak az Azure Digital Twins-ben.*

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Azure digitális Twins DTDL implementációs sajátosságai

Ahhoz, hogy egy DTDL-modell kompatibilis legyen az Azure Digital Twins szolgáltatással, meg kell felelnie ezeknek a követelményeknek.

* A modell minden legfelső szintű DTDL *illesztőfelület*típusúnak kell lennie. Ennek az az oka, hogy az Azure digitális Twins-modell API-jai olyan JSON-objektumokat fogadhatnak, amelyek a felületet vagy az illesztőfelületek tömbjét jelölik. Ennek eredményeképpen a legfelső szinten nem engedélyezettek más DTDL-típusok.
* Az Azure Digital Twins DTDL nem lehet *parancsokat*definiálni.
* Az Azure Digital Twins csak egyetlen szinten teszi lehetővé az összetevők beágyazását. Ez azt jelenti, hogy egy összetevőként használt felületnek nem lehet saját összetevője. 
* Az illesztőfelületek nem definiálhatók más DTDL-felületeken belüli beágyazott kapcsolaton belül. azokat különálló legfelső szintű entitásként kell definiálni, amelyeknek saját azonosítójuk van. Ezt követően, ha egy másik csatoló összetevőként vagy öröklés útján szeretné felvenni az illesztőfelületet, hivatkozhat az AZONOSÍTÓra.

## <a name="example-model-code"></a>Példa a modell kódjára

A Twin Type modellek bármilyen szövegszerkesztőben megírhatók. A DTDL nyelv JSON-szintaxist követ, ezért a *. JSON*kiterjesztésű modelleket kell tárolnia. A JSON-bővítmény használatával számos programozási szövegszerkesztő használható a DTDL-dokumentumok alapvető szintaxisának ellenőrzéséhez és kiemeléséhez. A [Visual Studio Code](https://code.visualstudio.com/)-hoz a [DTDL bővítmény](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) is elérhető.

Íme egy példa egy tipikus modellre, amely DTDL felületként íródott. A modell leírja a bolygókat, amelyek mindegyike névvel, tömeggel és hőmérséklettel rendelkezik. Lehetséges, hogy a bolygó műholdakat tartalmaz, és krátereket is tartalmazhat.

```json
[
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "Temperature",
        "schema": "double"
      },
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

A modell mezői a következők:

| Mező | Leírás |
| --- | --- |
| `@id` | A modell azonosítója. Formátumúnak kell lennie `dtmi:<domain>:<unique model identifier>;<model version number>` . |
| `@type` | A leírt információ típusát azonosítja. Illesztőfelület esetén a típus *illesztőfelület*. |
| `@context` | Beállítja a JSON-dokumentum [kontextusát](https://niem.github.io/json/reference/json-ld/context/) . A modelleknek használatban kell lenniük `dtmi:dtdl:context;2` . |
| `displayName` | választható Lehetővé teszi, hogy a modell felhasználóbarát nevet adjon, ha szükséges. |
| `contents` | Az összes többi illesztőfelület-adattal ide kerül, mint az attribútumok definícióinak tömbje. Minden attribútumnak `@type` (*tulajdonság*, *telemetria*, *parancs*, *kapcsolat*vagy *összetevő*) meg kell adnia az általa leírt illesztőfelület-információk, majd a tényleges attribútumot definiáló tulajdonságok készletét (például egy `name` `schema` *tulajdonság*definiálását). |

> [!NOTE]
> Vegye figyelembe, hogy az összetevő felülete (ebben a példában a*kráter* ) ugyanabban a tömbben van definiálva, mint az azt használó felület (*Planet*). Az összetevőket úgy kell meghatározni, hogy az API-hívások megtalálhatók legyenek az illesztőfelületen.

### <a name="possible-schemas"></a>Lehetséges sémák

A DTDL-hez hasonlóan a *tulajdonság* -és *telemetria* -attribútumok sémája szabványos primitív típusok –,, `integer` `double` `string` és `Boolean` – és más típusok (például `DateTime` és) lehetnek `Duration` . 

Az egyszerű típusok mellett a *Property* és a *telemetria* mezők a következő összetett típusokkal rendelkezhetnek:
* `Object`
* `Map`
* `Enum`

A *telemetria* mezők is támogatják a használatát `Array` .

### <a name="model-inheritance"></a>Modell öröklése

Időnként előfordulhat, hogy további modelleket szeretne specializálni. Előfordulhat *például, hogy egy általános modellel rendelkezik*, és speciális változatok *ConferenceRoom* és *edzőteremmel*rendelkezik. A specializáció kifejezéséhez a DTDL támogatja az öröklést: az illesztőfelületek örökölni tudnak egy vagy több másik felületről. 

Az alábbi példa a korábbi DTDL származó *bolygó* modelljét képzeli el újra egy nagyobb *CelestialBody* -modell altípusa szerint. A "Parent" modell először van definiálva, majd a "gyermek" modell a mező használatával épít rá `extends` .

```json
[
  {
    "@id": "dtmi:com:contoso:CelestialBody;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Celestial body",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "extends": "dtmi:com:contoso:CelestialBody;1",
    "contents": [
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

Ebben a példában a *CelestialBody* a *bolygó*nevét, tömegét és hőmérsékletét is segíti. A `extends` szakasz az illesztőfelület neve vagy az illesztőfelületek neveinek tömbje (ha szükséges, a több szülő modellből örökölheti a felületet).

Az öröklés alkalmazása után a kibővítési felület az összes tulajdonságot megjeleníti a teljes öröklési láncból.

A kiterjesztési felület nem változtathatja meg a szülő felületek definícióit; csak azok hozzáadására használható. Emellett nem tud újradefiniálni egy már definiált képességet a szülő felületén (még akkor is, ha a képességek definiálva vannak). Ha például egy szülő felület egy `double` tulajdonság *tömegét*határozza meg, akkor a kiterjesztési felület nem tartalmazhat *tömeges*deklarációt, még akkor is, ha az is a `double` .

## <a name="validating-models"></a>Modellek ellenőrzése

Rendelkezésre áll egy minta a modell dokumentumainak ellenőrzéséhez, hogy a DTDL érvényes legyen. A DTDL-elemző könyvtárra épül, és nyelvtől független. Itt találja: [DTDL validator minta](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator).

Ha további információt szeretne az elemző könyvtárról, beleértve a közvetlen használattal kapcsolatos példát is, tekintse meg a [How-to: modellek elemzése és ellenőrzése](how-to-use-parser.md)című témakört.

## <a name="next-steps"></a>További lépések

Tekintse meg, hogyan kezelhetők a modellek a DigitalTwinsModels API-kkal:
* [Útmutató: kettős modell kezelése](how-to-manage-model.md)

Vagy Ismerje meg, hogyan hozhatók létre a digitális ikrek a modellek alapján:
* [Fogalmak: digitális ikrek és a Twin gráf](concepts-twins-graph.md)

