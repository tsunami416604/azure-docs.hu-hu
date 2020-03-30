---
title: Az objektummodellek és a térintelligencia-grafikon ismertetése - Azure Digital Twins | Microsoft dokumentumok
description: Az Azure Digital Twins használata személyek, helyek és eszközök közötti kapcsolatok modellezéséhez
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: cafec321e7c40e27d8de731feda1103451271507
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265206"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>A Digital Twins objektummodelljeinek és térbeli intelligenciagrafikonjának megértése

Az Azure Digital Twins egy Azure IoT-szolgáltatás, amely a fizikai környezetek és a kapcsolódó eszközök, érzékelők és személyek átfogó virtuális megjelenítését biztosítja. Javítja a fejlesztést azáltal, hogy a tartományspecifikus fogalmakat hasznos modellekké szervezi. A modellek ezután egy térbeli intelligencia grafikonon helyezkednek el. Az ilyen fogalmak hűen modellezik az emberek, terek és eszközök közötti kapcsolatokat és kölcsönhatásokat.

A Digital Twins objektummodellek tartományspecifikus fogalmakat, kategóriákat és tulajdonságokat írnak le. A modelleket előre definiálják azok a felhasználók, akik a megoldást az egyedi igényeikhez szeretnék igazítani. Ezek az előre definiált Digital Twins objektummodellek együtt _ontológiát alkotnak._ Az intelligens épület ontológia régióit, helyszíneit, padlóit, irodáit, zónáit, konferenciatermeit és fókusztermeit írja le. Az energiahálózati ontológia különböző erőműveket, alállomásokat, energiaforrásokat és ügyfeleket ír le. A Digital Twins objektummodellekkel és ontológiákkal különböző forgatókönyvek és igények szabhatók testre.

A Digital Twins objektummodellekkel és egy ontológia segítségével feltöltheti a _térbeli grafikont._ A térbeli grafikonok a terek, az eszközök és az IoT-megoldások szempontjából releváns személyek közötti számos kapcsolat virtuális ábrázolásai. Ez az ábra egy olyan térbeli grafikont mutat be, amely egy intelligens épület ontológiáját használja.

[![Digitális Ikrek térbeli grafikon épület](media/concepts/digital-twins-spatial-graph-building.png)](media/concepts/digital-twins-spatial-graph-building.png#lightbox)

A térbeli grafikon egyesíti a tereket, eszközöket, érzékelőket és felhasználókat. Mindegyik úgy kapcsolódik össze, hogy a valós világot modellezi. Ebben a mintában a 43-as helyszín négy szintes, mindegyik különböző területtel rendelkezik. A felhasználók a munkaállomásaikhoz vannak társítva, és hozzáférést kapnak a grafikon egyes részeihez. A rendszergazda jogosult a térbeli grafikon módosítására, míg a látogatónak csak bizonyos épületadatok megtekintésére van joga.

## <a name="digital-twins-object-models"></a>Digitális Twins objektum modellek

A Digital Twins objektummodellek támogatják az objektumok alábbi fő kategóriáit:

- **A szóközök** virtuális vagy `Tenant`fizikai `Customer` `Region`helyek, `Venue`például , , , és .
- **Az eszközök** például virtuális vagy fizikai `AwesomeCompany Device` berendezések, és `Raspberry Pi 3`.
- **Az érzékelők** olyan objektumok, amelyek `AwesomeCompany Temperature Sensor` észlelik az eseményeket, például és `AwesomeCompany Presence Sensor`.
- **A felhasználók** azonosítják a lakókat és jellemzőiket.

Az objektumok egyéb kategóriái a következők:

- **Az erőforrások** egy területhez vannak csatolva, `IoTHub`és általában a térbeli grafikonban lévő objektumok által használandó Azure-erőforrásokat jelölik, például.
- **A blobok** objektumokhoz (például szóközökhöz, eszközökhöz, érzékelőkhöz és felhasználókhoz) vannak csatolva. A program a mime típusú fájlokat és a `maps`metaadatokat használja, például a , `pictures`és `manuals`a.
- **A kiterjesztett típusok** olyan bővíthető felsorolások, amelyek speciális `SpaceType` `SpaceSubtype`jellemzőkkel rendelkező entitásokat bővítenek, például és .
- **Az ontológiák** kiterjesztett típusok készletét `Default` `Building`jelentik, például , , `BACnet`és `EnergyGrid`.
- **A tulajdonságkulcsok és -értékek** a terek, eszközök, érzékelők és felhasználók egyéni jellemzői. Használhatók együtt beépített jellemzők, például a `DeltaProcessingRefreshTime` kulcs és `10` az érték.
- **A szerepkörök** a térbeli grafikonon a felhasználókhoz és `Space Administrator` `User Administrator`eszközökhöz `Device Administrator`rendelt engedélyek készletei, például , , és .
- **A szerepkör-hozzárendelések** egy szerepkör és egy objektum közötti társítás a térbeli grafikonon. Például egy felhasználó vagy egy egyszerű szolgáltatás engedélyt kaphat a térbeli gráf ban lévő tér kezeléséhez.
- **A biztonsági kulcstárolók** biztosítják a biztonsági kulcsokat a hierarchia összes eszközéhez egy adott térobjektum alatt, hogy az eszköz biztonságosan kommunikáljon a Digital Twins programmal.
- **A felhasználó által definiált függvények** (UDF-ek) lehetővé teszik a testreszabható érzékelő telemetriai feldolgozást a térbeli grafikonon belül. Egy UDF például:
  - Állítson be egy érzékelő értéket.
  - Egyéni logikát hajtson végre az érzékelő mért értékek alapján, és állítsa a kimenetet egy szóközre.
  - Metaadatok csatolása szóközhöz.
  - Értesítések küldése, ha az előre meghatározott feltételek teljesülnek. Jelenleg az UDF-ek JavaScript nyelven is írhatók.
- **A matcherek** olyan objektumok, amelyek meghatározzák, hogy mely UDF-ek vannak végrehajtva egy adott telemetriai üzenetben.
- **A végpontok** azok a helyek, ahol a telemetriai üzenetek `Event Hub` `Service Bus`és `Event Grid`a Digitális Twins események irányíthatók, például , és.

## <a name="spatial-intelligence-graph"></a>Térbeliintelligencia-diagramok

Térbeli grafikon a hierarchikus grafikon terek, eszközök és a digitális twins objektum modellben meghatározott személyek. A térbeli diagram támogatja az öröklődést, a szűrést, az átjárást, a méretezhetőséget és a bővíthetőséget. A térbeli grafikont a REST API-k gyűjteményével kezelheti és kezelheti.

Ha egy Digital Twins szolgáltatást telepít az előfizetésében, a gyökércsomópont globális rendszergazdája lesz. Ezután automatikusan teljes hozzáférést kap az egész struktúrához. A térközök üzembe helyeződése a diagramon a Tér API használatával. Szolgáltatások nyújtása az Eszköz API és érzékelők használatával a Sensor API használatával. [Nyílt forráskódú eszközök](https://github.com/Azure-Samples/digital-twins-samples-csharp) is rendelkezésre állnak a grafikon ömlesztve történő kiépítéséhez.

**Grafikon öröklés**. Az öröklés azokra az engedélyekre és tulajdonságokra vonatkozik, amelyek a szülőcsomópontról az alatta lévő összes csomópontra érkeznek. Ha például egy szerepkör egy adott csomóponton lévő felhasználóhoz van rendelve, a felhasználó rendelkezik a szerepkör engedélyeivel az adott csomóponthoz és az alatta lévő összes csomóponthoz. Minden adott csomóponthoz definiált tulajdonságkulcsot és kiterjesztett típust az adott csomópont alatti összes csomópont örökli.

**Grafikonszűrés**. A szűrés a kérelem eredmények szűkítésére szolgál. Szűrhet azonosítók, nevek, típusok, altípusok, szülőtér és a kapcsolódó szóközök szerint. Szűrhet érzékelőadattípusok, tulajdonságkulcsok és -értékek, *bejárás,* *minSzint*, *maxLevel*és egyéb OData-szűrőparaméterek szerint is.

**A grafikon áthalad**. A térbeli grafikont a mélysége és szélessége révén is átjárhatja. A mélység beállításhoz a grafikont felülről lefelé vagy alulról felfelé járja a *sokszögelés,* *a minLevel*és a *maxLevel*paraméterek használatával. A diagram on-át történő bejárásával közvetlenül egy szülőtérhez vagy annak egyik leszármazottjához kell kapcsolódnia a szélességhez. Amikor lekérdez egy objektumot, a GET *API-k tartalmazza* a kapcsolódó objektumokat tartalmazó paraméterhasználatával lekaphatja az összes kapcsolódó objektumot, amely kapcsolatban áll az adott objektummal.

**Grafikon méretezhetősége**. A Digital Twins garantálja a grafikonméretezhetőségét, így képes kezelni a valós munkaterheléseket. A Digital Twins segítségével ingatlanok, infrastruktúra, eszközök, érzékelők, telemetriai adatok és egyebek nagy portfólióit képviselheti.

**Grafikon bővíthetőség**. A bővíthetőség segítségével testre szabhatja az alapul szolgáló Digital Twins objektummodelleket új típusokkal és ontológiákkal. A Digital Twins adatok is bővíthető tulajdonságokkal és értékekkel gazdagodhatnak.

### <a name="spatial-intelligence-graph-management-apis"></a>Térintelligencia-grafikon kezelési API-k

Miután telepítette a digitális twins az [Azure Portalon,](https://portal.azure.com)a felügyeleti API-k [Swagger](https://swagger.io/tools/swagger-ui/) URL-címe automatikusan létrejön. Megjelenik az Azure Portalon az **áttekintés szakaszban** a következő formátumban.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Név | Csere erre |
| --- | --- |
| YOUR_INSTANCE_NAME | A digitális twins példány neve |
| YOUR_LOCATION | A példány mely kiszolgálórégiójában található |

 A teljes URL-formátum megjelenik ezen a képen.

[![Digitális Twins portál felügyeleti API](media/concepts/digital-twins-spatial-graph-management-api-url.png)](media/concepts/digital-twins-spatial-graph-management-api-url.png#lightbox)

A térbeli intelligencia grafikonok használatával kapcsolatos további részletekért látogasson el az Azure Digital Twins Management API-k besurranó előzetes verziójára.

> [!TIP]
> A Swagger setbesurrittió előzetes verziója az API-szolgáltatáskészlet bemutatására szolgál.
> A [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger)házigazdája.

További információ [a Swagger használatáról.](how-to-use-swagger.md)

Minden API-hívást az [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code)használatával kell hitelesíteni. Az API-k követik a [Microsoft REST API-irányelvek irányelveit.](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md) A gyűjteményeket visszaadó API-k többsége támogatja az [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) rendszer lekérdezési beállításait.

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni az eszközkapcsolatról és arról, hogyan küldhet telemetriai üzeneteket a digitális twins-nek, olvassa el [az Azure Digital Twins-eszköz kapcsolatát és a telemetriai adatok közötti kapcsolatot.](concepts-device-ingress.md)

- Ha többet szeretne megtudni a Felügyeleti API korlátairól és szabályozásáról, olvassa el [az Azure Digital Twins API-kezelést és korlátozásokat.](concepts-service-limits.md)
