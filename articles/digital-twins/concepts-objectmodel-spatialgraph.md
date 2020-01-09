---
title: Az Object models és a térbeli intelligencia Graph áttekintése – Azure digitális Twins | Microsoft Docs
description: Az Azure Digital Twins szolgáltatással emberek, helyek és eszközök közötti összefüggéseket modellezhet
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: cafec321e7c40e27d8de731feda1103451271507
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562595"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>A digitális ikrek Object models és a térbeli intelligencia Graph ismertetése

Az Azure Digital Twins egy Azure IoT-szolgáltatás, amely a fizikai környezetek és a hozzájuk kapcsolódó eszközök, érzékelők és személyek átfogó virtuális ábrázolását is felruházza. A fejlesztést a tartományra vonatkozó fogalmak hasznos modellekre való szervezésével javítja. A modellek ezután a térbeli intelligencia gráfon belül helyezkednek el. Ezek a fogalmak hűen modellezik a kapcsolatokat és az interakciókat a személyek, a szóközök és az eszközök között.

A digitális Twins-objektumok modelljei a tartományra jellemző fogalmakat, kategóriákat és tulajdonságokat írják le. A modelleket olyan felhasználók határozzák meg, akik szeretnék a megoldást egyedi igényeiknek megfelelően testre szabni. Ezek együttesen az előre definiált digitális Twins-objektumok modelljei egy _ontológia_-t alkotnak. Az intelligens épület ontológia a régiókat, a helyszíneket, a szinteket, az irodákat, a zónákat, a konferenciatermeket és a fókusz termeket ismerteti. Az Energy Grid ontológia különböző erőműveket, alállomásokat, energiaforrásokat és ügyfeleket tartalmaz. A digitális ikrek Object models és ontológiákat használatával a különböző forgatókönyvek és igények testreszabhatók.

A Digital ikrek Object models és az ontológia a helyén egy _térbeli gráfot_is feltölthet. A térbeli diagramok a helyek, az eszközök és a IoT-megoldásokhoz kapcsolódó személyek közötti kapcsolatok számos kapcsolatának virtuális ábrázolásai. Ez az ábra egy intelligens épületben lévő ontológia-t használó térbeli gráf példáját mutatja be.

[![digitális Twins térbeli gráf-összeállítás](media/concepts/digital-twins-spatial-graph-building.png)](media/concepts/digital-twins-spatial-graph-building.png#lightbox)

A térbeli gráf a szóközöket, az eszközöket, az érzékelőket és a felhasználókat egyesíti. Mindegyik össze van kapcsolva oly módon, hogy a valós világban modellezhető legyen. Ebben a példában a 43-es helyszín négy emelettel rendelkezik, amelyek mindegyike számos különböző területtel rendelkezik. A felhasználók a munkaállomásokhoz vannak társítva, és hozzáférést kapnak a gráf egyes részeihez. A rendszergazdának joga van a térbeli gráf módosítására, míg a látogató jogosult arra, hogy csak bizonyos építési adatszolgáltatásokat tekintse meg.

## <a name="digital-twins-object-models"></a>Digitális ikrek Object models

A digitális Twins-objektumok modelljei a következő fő kategóriákat támogatják:

- A **szóközök** virtuális vagy fizikai helyek, például `Tenant`, `Customer`, `Region`és `Venue`.
- Az **eszközök** virtuális vagy fizikai berendezések, például `AwesomeCompany Device` és `Raspberry Pi 3`.
- Az **érzékelők** olyan objektumok, amelyek az eseményeket (például `AwesomeCompany Temperature Sensor` és `AwesomeCompany Presence Sensor`) azonosítják.
- A **felhasználók** azonosítják az utasokat és azok jellemzőit.

Az objektumok egyéb kategóriái a következők:

- Az **erőforrások** egy szóközzel vannak csatolva, és jellemzően olyan Azure-erőforrásokat jelölnek, amelyeket a térbeli gráf objektumai használnak, például `IoTHub`.
- A **Blobok** objektumokhoz (például szóközökhöz, eszközökhöz, érzékelőkhöz és felhasználókhoz) vannak csatolva. A MIME-típust és metaadatokat tartalmazó fájlként használják, például `maps`, `pictures`és `manuals`.
- A **kiterjesztett típusok** olyan bővíthető enumerálások, amelyek adott jellemzőkkel bővítik az entitásokat, például `SpaceType` és `SpaceSubtype`.
- A **ontológiákat** kiterjesztett típusok (például `Default`, `Building`, `BACnet`és `EnergyGrid`) halmazát jelölik.
- A **Tulajdonságok kulcsai és értékei** a szóközök, eszközök, érzékelők és felhasználók egyéni jellemzői. A beépített jellemzőkkel együtt használhatók, például `DeltaProcessingRefreshTime` kulcsként, `10`ként pedig értékként.
- A **szerepkörök** a térbeli gráf felhasználóihoz és eszközeihez rendelt engedélyek készletei, például `Space Administrator`, `User Administrator`és `Device Administrator`.
- A **szerepkör-hozzárendelések** egy szerepkör és egy, a térbeli gráfban található objektum közötti társítások. Előfordulhat például, hogy egy felhasználó vagy egy szolgáltatásnév engedélyt kap a térbeli gráfban található terület kezelésére.
- A **biztonsági kulcsok tárolói** biztosítják a hierarchiában lévő összes eszköz biztonsági kulcsait, hogy az eszköz biztonságosan tudjon kommunikálni a digitális ikrekkel.
- A **felhasználó által definiált függvények** (UDF) lehetővé teszik a testreszabható érzékelő telemetria feldolgozását a térbeli gráfon belül. Az UDF például a következőket teheti:
  - Állítsa be az érzékelő értékét.
  - Végezzen egyéni logikát az érzékelő beolvasása alapján, és állítsa be a kimenetet egy szóközzel.
  - Metaadatok csatolása egy szóközzel.
  - Értesítések küldése, ha az előre meghatározott feltételek teljesülnek. A UDF jelenleg JavaScript nyelven is megírhatók.
- Az **egyeztető** olyan objektumok, amelyek meghatározzák, hogy mely UDF kell végrehajtani egy adott telemetria-üzenet esetében.
- A **végpontok** azok a helyszínek, ahol a telemetria üzenetek és a digitális ikrek eseményei irányíthatók, például `Event Hub`, `Service Bus`és `Event Grid`.

## <a name="spatial-intelligence-graph"></a>Térbeliintelligencia-diagramok

A térbeli gráf a szóközök, az eszközök és a digitális Twins-objektummodell által meghatározott személyek hierarchikus gráfja. A térbeli gráf támogatja az öröklést, a szűrést, a átjárást, a méretezhetőséget és a bővíthetőséget. A térbeli gráfot a REST API-k gyűjteményével kezelheti és használhatja.

Ha az előfizetésében egy digitális Twins szolgáltatást helyez üzembe, akkor a legfelső szintű csomópont globális rendszergazdája lesz. Ezután automatikusan megkapja a teljes hozzáférést a teljes struktúrához. Szóközök kiépítése a gráfban a Space API használatával. Szolgáltatások kiépítése az eszköz API-val és érzékelőkkel az érzékelő API használatával. A [nyílt forráskódú eszközök](https://github.com/Azure-Samples/digital-twins-samples-csharp) is elérhetők a gráf tömeges kiépítéséhez.

**Gráf öröklése**. Az öröklés a szülő csomópontról az alatta lévő összes csomópontra leképezett engedélyekre és tulajdonságokra vonatkozik. Ha például egy szerepkör egy adott csomóponton lévő felhasználóhoz van rendelve, akkor a felhasználó rendelkezik az adott szerepkörhöz tartozó jogosultságokkal az adott csomóponthoz és az alatta lévő összes csomóponthoz. Az adott csomóponthoz definiált összes tulajdonságot és kiterjesztett típust a csomópont alatti összes csomópont örökli.

**Gráf szűrése** A szűrés a kérelmek eredményeinek szűkítéséhez használható. Az azonosítók, a név, a típusok, az altípusok, a szülő terület és a társított szóközök alapján szűrhet. Az adattípusok, a tulajdonságok és az értékek, a *bejárás*, a *minLevel*, a *maxLevel*és más OData-szűrési paraméterek alapján is szűrhetők.

**Gráf átjárása**. A térbeli gráfot a mélysége és szélessége alapján is áthaladhatja. A részletes adatok megjelenítéséhez a *minLevel*és a *maxLevel*paraméterek használatával a diagram felülről lefelé vagy alulról *lefelé halad.* A gráf bejárásával lekérheti, hogy a testvér-csomópontok közvetlenül a szülő területhez vagy az egyik leszármazotthoz legyenek csatolva. Amikor lekérdez egy objektumot *, a Get* API-k beolvasása paraméter használatával lekérheti az összes kapcsolódó objektumot, amely az objektummal való kapcsolattal rendelkezik.

**Gráf skálázhatósága**. A digitális Twins garantálja a gráf méretezhetőségét, így képes kezelni a valós munkaterheléseket. A digitális ikrek a Real Estate, az infrastruktúra, az eszközök, az érzékelők, a telemetria és sok más portfóliójának ábrázolására alkalmasak.

**Gráf bővíthetősége**. A bővíthetőség használatával testreszabhatja az alapul szolgáló digitális Twins-objektumok modelljét új típusokkal és ontológiákat. A digitális Twins-adatok bővíthető tulajdonságokkal és értékekkel is bővíthetők.

### <a name="spatial-intelligence-graph-management-apis"></a>Térbeli intelligencia Graph felügyeleti API-k

Miután a [Azure Portal](https://portal.azure.com)a digitális Twins üzembe helyezését, automatikusan [létrejön a felügyeleti](https://swagger.io/tools/swagger-ui/) API-k felvágási URL-címe. A Azure Portal az **Áttekintés** szakaszban a következő formátumban jelenik meg.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Név | Csere erre |
| --- | --- |
| YOUR_INSTANCE_NAME | A digitális Twins-példány neve |
| YOUR_LOCATION | A példány által üzemeltetett kiszolgálói régió |

 A képen megjelenik a teljes URL-cím formátuma.

[![digitális Twins portál Management API](media/concepts/digital-twins-spatial-graph-management-api-url.png)](media/concepts/digital-twins-spatial-graph-management-api-url.png#lightbox)

További információ a térbeli intelligencia-diagramok használatáról: Azure digitális Twins Management API-k – előzetes verzió.

> [!TIP]
> Az API-szolgáltatások készletének bemutatásához meg kell adni egy hencegő előzetes verziót.
> A szolgáltatás a [docs.westcentralus.azuresmartspaces.net/Management/Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger)webhelyen található.

További információ a [hencegés használatáról](how-to-use-swagger.md).

Az összes API-hívást hitelesíteni kell a [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code)használatával. Az API-k a [Microsoft REST API irányelvek konvencióit](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md)követik. A gyűjteményeket visszaadó API-k többsége támogatja a [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) rendszer-lekérdezési lehetőségeket.

## <a name="next-steps"></a>Következő lépések

- Az eszközök kapcsolatának megismeréséhez és a digitális Twins telemetria-üzeneteinek elküldéséhez olvassa el az [Azure Digital Twins-eszközök csatlakoztatása és a telemetria](concepts-device-ingress.md)beáramlása című témakört.

- A felügyeleti API korlátozásait és szabályozásait az [Azure digitális ikrek API Management és korlátozásai](concepts-service-limits.md)című témakör ismerteti.
