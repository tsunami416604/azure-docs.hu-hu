---
title: Digitális Twins objektum modellek és térbeli intelligencia graph ismertetése |} A Microsoft Docs
description: Az Azure Digital Twins szolgáltatással emberek, helyek és eszközök közötti összefüggéseket modellezhet
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: alinast
ms.openlocfilehash: 912a3ed558f8fabfcad517aeb7b7e864cf8f359e
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436925"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>Digitális Twins objektum modellek és térbeli intelligencia graph ismertetése

Az Azure digitális Twins az szolgáltatása Azure IoT-megoldás a fizikai környezetek és a kapcsolódó eszközök, érzékelők és személyek átfogó virtuális ábrázolásai. Ez növeli a fejlesztési, tartomány-specifikus fogalmak rendszerezéséhez hasznosak modellekbe. A modellek majd egy térbeli intelligencia graph belül található. Ezeket a fogalmakat hűen modellje a kapcsolatok és a személyek, szóközöket és eszközök közötti interakciókat.

Digitális Twins objektummodellt tartomány-specifikus fogalmak, kategóriák és tulajdonságait írják le. Modellek előre a felhasználók számára szeretne testre szabni a megoldást, ő konkrét igényeiknek. Együtt ezeket az előre meghatározott objektummodellt alkotó digitális Twins- _ontology_. Egy intelligens épület ontology területek, helyszínek, emeleteken, iroda, zónák, konferenciahívások és fókusz termek ismerteti. Az energia rács ontology különböző erőművekben, többek között alállomásokkal, energiaforrások és ügyfelek ismerteti. A digitális Twins objektummodellt és ontológiákat különböző forgatókönyveket, és igényeinek megfelelően testre szabható.

A digitális Twins objektummodellt és a egy helyen ontology fel lehet tölteni egy _térbeli graph_. Térbeli gráfok tárolóhelyek, eszközök és személyeket, amely a IoT-megoldás számos viszonyait virtuális jelképezik. Ez az ábra egy térbeli graph által használt intelligens épület ontology egy példát mutat be.

![Digitális Twins térbeli gráf létrehozása][1]

<a id="model"></a>

A térbeli graph foglal tárolóhelyek, eszközök, érzékelők és felhasználókat. Minden egyes összekapcsolásával úgy, hogy a való világból a modellek. Ebben a példában a helyszín 43 négy emeleteken, számos különböző területekre mindegyike rendelkezik. Felhasználók a munkaállomásokat társított, és hozzáférést biztosítani a diagram egyes részeit. A rendszergazda rendelkezik jogosultságokkal a hajtsa végre a módosításokat a Térbeli diagramhoz, míg a látogatói rights csak az egyes épület adatainak megtekintéséhez.

## <a name="digital-twins-object-models"></a>Digitális Twins objektummodellt

Digitális Twins objektum modellek támogatják a fő kategóriára objektumok:

- **Tárolóhelyek** virtuális vagy fizikai helyen vannak például `Tenant`, `Customer`, `Region`, és `Venue`.
- **Eszközök** virtuális vagy fizikai eszközökre, amelyek például `AwesomeCompany Device` és `Raspberry Pi 3`.
- **Érzékelő** olyan objektumok, amelyek észlelje az eseményeket, például `AwesomeCompany Temperature Sensor` és `AwesomeCompany Presence Sensor`.
- **Felhasználók** azonosíthatja a lakók és azok tulajdonságait.

Az objektumok egyéb kategóriák a következők:

- **Erőforrások** szóközt is csatlakozik, és általában képviseli az objektumok a térbeli grafikon, például által használt Azure-erőforrások `IoTHub`.
- **Blobok** csatolt objektumok (például a tárolóhelyek, eszközök, érzékelők és felhasználókat). Használhatók mime-típus és metaadatok, fájlokat, például `maps`, `pictures`, és `manuals`.
- **Kiterjesztett típus** vannak, amelyek az adott tulajdonságokkal rendelkező entitások például bővítésével bővíthető enumerálások `SpaceType` és `SpaceSubtype`.
- **Ontológiákat** ábrázolásához kiterjesztett típusú, például `Default`, `Building`, `BACnet`, és `EnergyGrid`.
- **Vlastnost kulcsokat és értékeket** tárolóhelyek, eszközök, érzékelők és a felhasználók egyéni jellemzőit is. Is használhatók együtt beépített jellemzőkkel, például `DeltaProcessingRefreshTime` kulcsként és `10` értékként.
- **Szerepkörök** engedélyek hozzárendelve a felhasználók és eszközök a térbeli grafikon, például eljáráscsoport `Space Administrator`, `User Administrator`, és `Device Administrator`.
- **Szerepkör-hozzárendelések** vannak a szerepkör és a térbeli Graph objektum közötti társítást. Ha például egy felhasználó vagy szolgáltatásnév adható adható meg a térbeli Graph kezelésére jogosult.
- **Biztonsági kulcs tárolók** adja meg a biztonsági kulcsok, hogy az eszköz digitális Twins biztonságosan kommunikáljon egy adott összekötőtér objektuma alapján a hierarchiában lévő összes eszközre.
- **Felhasználó által definiált függvények** (UDF-EK) lehetővé teszi a személyre szabható érzékelő telemetria-feldolgozási a térbeli diagramon belül. Ha például egy UDF is:
  - Érzékelő állította be.
  - Hajtsa végre az egyéni logikát érzékelőinek adatai alapján, és a kimenetét állítsa egy szóközt.
  - Metaadatok csatolása egy szóközt.
  - Értesítések küldése az előre meghatározott feltételek teljesülése esetén. Jelenleg a JavaScript UDF-EK írhatók.
- **Matchers** objektumok, amelyek meghatározzák, hogy mely felhasználói függvények végrehajtása egy adott telemetriai üzenetet a rendszer.
- **Végpontok** a helyek, ahol telemetriai üzeneteket és a digitális Twins események átirányíthatók, például `Event Hub`, `Service Bus`, és `Event Grid`.

<a id="graph"></a>

## <a name="spatial-intelligence-graph"></a>Térbeliintelligencia-diagramok

Térbeli graph a hierarchikus grafikon, a tárolóhelyek, eszközök és a digitális Twins objektum modellben meghatározott személyek. A térbeli graph támogatja az öröklést, szűrés, áthaladó, a méretezhetőség és bővíthetőség. Felügyelheti, és együttműködik a térbeli grafikon, a REST API-k gyűjteménye.

Ha telepít egy digitális Twins szolgáltatás az előfizetésében, a gyökércsomópont globális rendszergazdája lesz. Ön éppen majd teljes hozzáférést kap a teljes struktúra. A gráf tárolóhelyek kiépítése a hely API-val. Az érzékelő API-val eszközök API-ja és érzékelők segítségével szolgáltatások kiépítése. [Nyílt forráskódú eszközök](https://github.com/Azure-Samples/digital-twins-samples-csharp) is elérhetők a diagramra a tömeges kiépítéséhez.

**Graph-öröklés**. Öröklés a vonatkozó engedélyeket és a egy szülő csomópont, az összes csomóponton alatta csökkenő növekvő tulajdonságok vonatkozik. Például egy szerepkör van rendelve egy felhasználó egy adott csomópont, amikor a felhasználók számára a szerepkör a megadott csomópont, és minden csomópont alá. Minden tulajdonság kulcsához és a egy adott csomópont meghatározott kiterjesztett típus örökölt csomópont alatti összes csomópontja.

**Graph-szűrés**. Segítségével az eredményeket a kérelmet. Azonosítók, név, típus, altípus, szülő hely és társított tárolóhelyek szűrheti. Emellett szerint szűrheti érzékelő adattípusok, tulajdonság kulcsok és értékek, *bejárása*, *minLevel*, *maxLevel*, és egyéb OData szűrési paraméterekhez.

**A gráf áthaladó**. A térbeli grafikon a mélység és technológiai spektrumunk kihasználtságának növelését is haladnak át. Tudni, haladnak át a gráf fentről lefelé vagy alulról felfelé a paraméterek használatával *bejárása*, *minLevel*, és *maxLevel*. A graph közvetlenül kapcsolódik a fölérendelt hely vagy a technológiai spektrumunk kihasználtságának növelését a leszármazottai közül testvér csomópontok haladnak át. Egy objektum lekérdezésekor kap, amelyek kapcsolatok az objektum nem rendelkezik az összes kapcsolódó objektumok a *tartalmaz* az első API-k paraméterében.

**Graph-méretezhetőségi**. Digitális Twins garantálja a graph méretezhetőséget, így a való életből vett számítási feladatokat tud kezelni. Digitális Twins képviselő ingatlan, infrastruktúra, eszközök, érzékelők, telemetriai adatok és további nagy portfóliók kialakításában is használható.

**Graph-bővíthetőségi**. Bővíthetőségi a digitális Twins alapul szolgáló objektum modellek az új típusai és ontológiákat testreszabásához használja. Digitális Twins adatait is képes lehet renderelésre bővíthető tulajdonságokat és értékeket.

### <a name="spatial-intelligence-graph-management-apis"></a>Térbeli intelligencia graph felügyeleti API-k

A digitális Twins telepítése után a [az Azure portal](https://portal.azure.com), a [Swagger](https://swagger.io/tools/swagger-ui/) URL-címét a felügyeleti API-k automatikusan jön létre. Az Azure Portalon, megjelenik a **áttekintése** formátuma a következő szakaszt.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Name (Név) | Csere erre |
| --- | --- |
| YOUR_INSTANCE_NAME | A digitális Twins-példány nevét |
| YOUR_LOCATION | Melyik kiszolgáló régióban lévő üzemeltetett a példány |

 A teljes URL-cím formátuma a kép jelenik meg.

![Digitális Twins portál felügyeleti API][2]

Térbeli intelligencia gráfok használatáról további részletekért látogasson el az Azure digitális Twins felügyeleti API-k szakmai bemutatóhoz.

> [!TIP]
> A Swagger szakmai bemutatóhoz biztosítunk az API-funkció bemutatásához beállítása.
> Vannak tárolva [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Tudjon meg többet [hogyan generáljon a swaggerrel](how-to-use-swagger.md).

Minden API-hívás használatával hitelesíteni kell [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). Az API-k kövesse [Microsoft REST API-útmutatójának konvenciók](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). A gyűjtemények visszaadó API-kat a legtöbb támogatja a [OData](http://www.odata.org/getting-started/basic-tutorial/#queryData) rendszerlekérdezési beállításait.

## <a name="next-steps"></a>További lépések

- Eszköz csatlakoztatása, és hogyan telemetriai üzeneteket küldhet az digitális Twins kapcsolatos további információkért olvassa el a [Azure digitális Twins eszköz kapcsolat- és telemetriabevitelt](concepts-device-ingress.md).

- Felügyeleti API-korlátozásokkal és szabályozások kapcsolatos további információkért olvassa el a [Azure digitális Twins API kezelése és korlátozásai](concepts-service-limits.md).

<!-- Images -->
[1]: media/concepts/digital-twins-spatial-graph-building.png
[2]: media/concepts/digital-twins-spatial-graph-management-api-url.png
