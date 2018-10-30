---
title: Understanding digitális Twins objektum modelleket és a térbeli intelligencia graph |} A Microsoft Docs
description: Az Azure digitális Twins használatával személyek, helyek és eszközök közötti kapcsolatok modellezésére
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: c1d66e0b58567244f8c1406ee258c9311994ff20
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215106"
---
# <a name="understanding-digital-twins-object-models-and-spatial-intelligence-graph"></a>Digitális Twins objektummodellt és térbeli intelligencia graph ismertetése

Az Azure digitális Twins az szolgáltatása Azure IoT-megoldás a fizikai környezetek és a kapcsolódó eszközök, érzékelők és személyek átfogó virtuális ábrázolásai. Ez növeli a fejlesztési rendezőelvként tartomány-specifikus koncepciók szerint modellekbe hasznos, amelyek magukat egy térbeli intelligencia graph belül található. Ezeket a fogalmakat hűen modellje a kapcsolatok és a személyek, szóközöket és eszközök közötti interakciókat.

_Digitális Twins objektummodellt_ tartomány-specifikus fogalmak, kategóriák és tulajdonságait ismerteti. Modellek vannak előre meghatározott felhasználók számára szeretné testre szabni a megoldást, ő konkrét igényeiknek. Ezek előre meghatározott digitális Twins objektummodellt alkotó együtt egy _Ontology_. Egy intelligens épület ontology területek, helyszínek, emeleteken, iroda, zónák, konferenciahívások és fókusz termek le. Az energia rács ontology különböző erőművekben, többek között alállomásokkal, energiaforrások és ügyfelek le. Ezek digitális Twins objektummodellt és ontológiákat, hogy testreszabása az Azure digitális Twins különböző forgatókönyveket, és igényeinek megfelelően.

A _digitális Twins objektummodellt_ és _Ontology_ teljesülnek, egy fel lehet tölteni egy _térbeli Graph_. Térbeli gráfok tárolóhelyek, eszközök és IoT-megoldás releváns személyek számos viszonyait virtuális jelképezik. Az alábbi ábrán egy térbeli graph használatával intelligens épület ontology egy példát mutat be.

![Digitális Twins térbeli gráf létrehozása][1]

<a id="model"></a>

A térbeli graph foglal tárolóhelyek, eszközök, érzékelők és felhasználókat. Minden egyes összekapcsolásával úgy, hogy a való világból a modellek: 43 helyszín rendelkezik négy emeleteken, amelyek mindegyike több különböző területekre. A felhasználók társítva a munkaállomásokat, és a gráf részeit kapjanak hozzáférést.  Ha például egy rendszergazda megfelelő jogosultságot kapó módosításokat a Térbeli diagramhoz, míg a látogatói előfordulhat, hogy csak bizonyos épület adatainak megtekintése a rights.

## <a name="digital-twins-object-models"></a>Digitális Twins objektummodellt

Digitális Twins objektum modellek támogatják a fő kategóriára objektumok:

- **Tárolóhelyek** virtuális vagy fizikai helyen vannak például `Tenant`, `Customer`, `Region`, `Venue`.
- **Eszközök** virtuális vagy fizikai eszközökre, amelyek például `AwesomeCompany Device`, `Raspberry Pi 3`.
- **Érzékelő** olyan objektumok, amelyek észlelje az eseményeket, például `AwesomeCompany Temperature Sensor`, `AwesomeCompany Presence Sensor`.
- **Felhasználók** azonosíthatja a lakók és azok tulajdonságait.

Az objektumok egyéb kategóriák a következők:

- **Erőforrások** szóközt is csatlakozik, és általában képviseli az Azure-erőforrások által használható objektumok a térbeli grafikon, például `IoTHub`.
- **Blobok** csatolt objektumok (például a tárolóhelyek, eszközök, érzékelők és felhasználók) és a használt mime-típus és metaadatok, fájlokat, például `maps`, `pictures`, `manuals`.
- **Kiterjesztett típus** vannak, amelyek az adott tulajdonságokkal rendelkező entitások például bővítésével bővíthető enumerálások `SpaceType`, `SpaceSubtype`.
- **Ontológiákat** ábrázolásához kiterjesztett típusú, például `Default`, `Building`, `BACnet`, `EnergyGrid`.
- **Vlastnost kulcsokat és értékeket** tárolóhelyek, eszközök, érzékelők és a felhasználók egyéni jellemzőit is. Használat mellett beépített jellemzőkkel, például `DeltaProcessingRefreshTime` kulcsként és `10` értékként.
- **Szerepkörök** engedélyek hozzárendelve a felhasználók és eszközök a térbeli grafikon, például eljáráscsoport `Space Administrator`, `User Administrator`, `Device Administrator`.
- **Szerepkör-hozzárendelések** egy szerepkör és a egy objektumot a térbeli grafikon, egy felhasználó vagy szolgáltatás egyszerű engedély adható meg a térbeli Graph kezeléséhez például biztosítása közötti társítás vannak.
- **Biztonsági kulcs tárolók** adja meg a biztonsági kulcsok, hogy az eszköz biztonságos kommunikációt a digitális Twins szolgáltatással egy adott összekötőtér objektuma alapján a hierarchiában lévő összes eszközre.
- **Felhasználó által megadott függvények** vagy **UDF-EK** lehetővé testre szabható érzékelő telemetria-feldolgozási a térbeli diagramon belül. Például egy UDF is érzékelő állította be, hajtsa végre az egyéni logikát érzékelőinek adatai alapján és a kimenetét állítsa egy szóközt, metaadatok csatlakoztathat egy szóközzel és értesítéseket küldeni, előre meghatározott feltételek teljesülése esetén. Jelenleg a JavaScript UDF-EK írhatók.
- **Matchers** objektumok, amelyek meghatározzák, hogy mely felhasználói függvények mindenképpen végrehajtja az adott telemetriai üzenetet is.
- **Végpontok** a helyek, ahol telemetriai üzeneteket és a digitális Twins események átirányíthatók, például `Event Hub`, `Service Bus`, `Event Grid`.

<a id="graph"></a>

## <a name="spatial-intelligence-graph"></a>Térbeliintelligencia-diagramok

**Térbeli graph** a hierarchikus grafikonjával kezdheti, szóközöket, eszközök, és meghatározott személyek a **digitális Twins hálózatiobjektum-modellt**. Támogatja a térbeli graph _öröklési_, _szűrés_, _áthaladó_, _méretezhetőség_, és _bővíthetőség_ . Felhasználók kezelése és a REST API-k (lásd alább) gyűjteménye a térbeli graph interakcióba.

Üzembe helyez egy digitális Twins-szolgáltatás előfizetésének a felhasználó lesz globális rendszergazdája a legfelső szintű csomópont automatikusan a teljes struktúra teljes hozzáférést biztosít. Ez a felhasználó majd helyezhet üzembe a tárolóhelyek a grafikon, a hely API-val. Eszközök sikerült építhető ki az eszköz API-val, érzékelők sikerült kiosztott használatával érzékelő API-t, és így tovább. Emellett [nyílt forráskódú eszközök](https://github.com/Azure-Samples/digital-twins-samples-csharp) a diagramra a tömeges kiépítéséhez.

Graph _öröklési_ engedélyeit és a egy szülő csomópont, az összes csomóponton alatta csökkenő növekvő tulajdonságok vonatkozik. Például egy szerepkör van rendelve egy felhasználó egy adott csomópont, amikor a felhasználó engedéllyel rendelkezik majd a szerepkör az adott csomópont, és minden csomópont alá. Emellett minden egyes tulajdonság kulcs és a egy adott csomópont meghatározott kiterjesztett típus örökli a csomópontokon, hogy a csomópont alatt.

Graph _szűrés_ lehetővé teszi a felhasználók azonosítók, name, típusok, altípus, szülő hely, társított tárolóhelyek, érzékelő adattípusok, tulajdonság kulcsok és értékek, a kérelem eredmények szűkítéséhez *bejárása*,  *minLevel*, *maxLevel*, és egyéb OData szűrési paraméterekhez.

Graph _áthaladó_ lehetővé teszi a felhasználóknak, hogy a térbeli grafikon a mélységének és szélességének keresztül. A mélység, a graph sikerült kell haladnia fentről lefelé vagy alulról felfelé navigációs paraméterek használatával *bejárása*, *minLevel*, *maxLevel*. A technológiai spektrumunk kihasználtságának növelését a graph sikerült munkamenetének beolvasása testvér csomópontok közvetlenül kapcsolódik a fölérendelt hely vagy a leszármazottai közül. Egy objektum lekérdezésekor sikerült-e az összes kapcsolódó objektumok, amelyek kapcsolatok az, hogy az objektum a *tartalmaz* az első API-k paraméterében.

Az Azure digitális Twins garantálja graph _méretezhetőség_, így a való életből vett számítási feladatokat tud kezelni. Digitális Twins képviselő ingatlan, infrastruktúra, eszközök, érzékelők, telemetriai adatok és további nagy portfóliók kialakításában is használható.

Graph _bővíthetőség_ lehetővé teszi a felhasználóknak az alapul szolgáló digitális Twins objektum modellek az új típusai és ontológiákat testreszabása. A digitális twins adatokat is is lehet renderelésre bővíthető tulajdonságokat és értékeket.

### <a name="spatial-intelligence-graph-management-apis"></a>Térbeli intelligencia graph felügyeleti API-k

Az Azure a digitális Twins üzembe helyez a [az Azure portal](https://portal.azure.com), a [Swagger](https://swagger.io/tools/swagger-ui/) URL-címét a felügyeleti API-k automatikusan jön létre, és megjelenik az Azure Portalon **áttekintése** formátuma a következő szakaszt:

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| Egyéni attribútum neve | Cserélje le |
| --- | --- |
| *Példánynév* | Az Azure digitális Twins-példány nevét |
| *yourLocation* | Melyik kiszolgáló régióban lévő üzemeltetett a példány |

 A teljes URL-formátum használja az alábbi ábrán láthatók:

![Digitális Twins portál felügyeleti API][2]

Térbeli üzletiintelligencia-diagramok használatával. További részletekért látogasson el az Azure digitális Twins felügyeleti API-k szakmai bemutatóhoz.

> [!TIP]
> A Swagger szakmai bemutatóhoz biztosítunk az API-funkció bemutatásához beállítása.
> Vannak tárolva [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Tudjon meg többet [hogyan generáljon a swaggerrel](how-to-use-swagger.md).

Minden API-hívás használatával lehet hitelesíteni [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). Az API-k kövesse [Microsoft REST API-útmutatójának konvenciók](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). A gyűjtemények visszaadó API-kat a legtöbb támogatja a [OData](http://www.odata.org/getting-started/basic-tutorial/#queryData) rendszerlekérdezési beállításait.

## <a name="next-steps"></a>További lépések

Eszköz csatlakoztatása, és hogyan telemetriai üzeneteket küldhet az Azure digitális Twins szolgáltatás kapcsolatos további információkért olvassa el a [Azure digitális Twins eszköz kapcsolat- és telemetriabevitelt](concepts-device-ingress.md).

Felügyeleti API-korlátozásokkal és szabályozások kapcsolatos további információkért olvassa el a [Azure digitális Twins API kezelése és korlátozásai](concepts-service-limits.md).

<!-- Images -->
[1]: media/concepts/digital-twins-spatial-graph-building.png
[2]: media/concepts/digital-twins-spatial-graph-management-api-url.png
