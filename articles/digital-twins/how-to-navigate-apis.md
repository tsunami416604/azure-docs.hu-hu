---
title: Navigálás az API-k között – Azure Digital Twins | Microsoft dokumentumok
description: Ismerje meg, hogyan lehet az Azure Digital Twins felügyeleti API-k lekérdezésének közös mintáit.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: e9cdfd40a9672d19ef32dede0baadcdd56266bab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265167"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Az Azure Digital Twins felügyeleti API-k használata

Az Azure Digital Twins felügyeleti API-k hatékony funkciókat biztosítanak az IoT-alkalmazásokhoz. Ez a cikk bemutatja, hogyan navigálhat az API-struktúra között.  

## <a name="api-summary"></a>API összefoglaló

Az alábbi lista a digitális twins API-k összetevőit tartalmazza.

* [/szóközök:](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces)Ezek az API-k a beállított fizikai helyekkel kommunikálnak. Ezek segítségével [térbeli grafikon](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph)formájában hozhatja létre, törölheti és kezelheti a fizikai helyek digitális leképezését.

* [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): Ezek az API-k a beállításban lévő eszközökkel működnek. Ezek az eszközök egy vagy több érzékelőt képesek kezelni. Például egy eszköz lehet a telefon, vagy egy Raspberry Pi érzékelő pod, vagy egy Lora átjáró, és így tovább.

* [/sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): Ezek az API-k segítenek kommunikálni az eszközökhöz társított érzékelőkkel és a fizikai helyekkel. Az érzékelők rögzítik és elküldik a környezeti értékeket, amelyek segítségével a térbeli környezetet is manipulálhatja.  

* [/resources:](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources)Ezek az API-k segítségével erőforrásokat, például egy IoT-központot állíthat be a digital Twins-példányhoz.

* [/types](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): Ezek az API-k lehetővé teszik, hogy kiterjesztett típusokat társítson a digitális Twins objektumokhoz, hogy adott jellemzőket adjon ezekhez az objektumokhoz. Ezek a típusok lehetővé teszik az objektumok egyszerű szűrését és csoportosítását a felhasználói felületen és a telemetriai adatokat feldolgozó egyéni függvényekben. Kiterjesztett típusok például a *DeviceType*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceBlobType*, *SpaceResourceType*és így tovább. *SpaceBlobType*

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): Ezek az API-k segítenek az ontológiák kezelésében, amelyek kiterjesztett típusú gyűjtemények. Az ontológiák az általuk képviselt fizikai térnek megfelelően adják meg az objektumtípusok nevét. A *BACnet* ontológia például egyedi neveket ad az *érzékelőtípusoknak,* *adattípusoknak*, *adataltípusoknak*és *adategységtípusoknak.* Az ontológiákat a szolgáltatás kezeli és létrehozza. A felhasználók betölthetik és eltávolíthatják az ontológiákat. Ha egy ontológia be van töltve, az összes társított típusnév engedélyezve van, és készen áll a térbeli grafikonon való kiépítésre. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): Ezekkel az API-kkal egyéni tulajdonságokat hozhat létre a *terekhez*, *eszközökhez*, *felhasználókhoz*és *érzékelőkhöz.* Ezek a tulajdonságok kulcs-/értékpárokként jönnek létre. Ezeknek a tulajdonságoknak az adattípusát a *PrimitiveDataType*beállításával határozhatja meg. Például megadhat egy *BasicTemperatureDeltaProcessingRefreshTime* nevű tulajdonságot *uint* típusú az érzékelőkhöz, majd hozzárendelhet egy értéket ehhez a tulajdonsághoz az egyes érzékelőkhöz. A tulajdonság létrehozásakor is hozzáadhat kényszereket ezekhez az értékekhez, például *a Min* és *a Max* tartományokat, valamint az engedélyezett értékeket, *például ValidationData*.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): Ezek az API-k lehetővé teszik, hogy adja meg a feltételeket, amelyeket ki szeretne értékelni a bejövő eszköz adatait. További információkért tekintse meg [ezt a cikket](concepts-user-defined-functions.md#matchers). 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): Ezek az API-k lehetővé teszik egy egyéni függvény létrehozását, törlését vagy frissítését, amely akkor fog működni, amikor a *matchers* által meghatározott feltételek történnek, a telepítőből származó adatok feldolgozásához. Ebben [a cikkben](concepts-user-defined-functions.md#user-defined-functions) további információt talál ezekről az egyéni függvényekről, más néven a *felhasználó által definiált függvényekről.* 

* [/endpoints:](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints)Ezek az API-k lehetővé teszik, hogy végpontokat hozzon létre, így a Digital Twins megoldás kommunikálhat más Azure-szolgáltatásokadat-tárolás és elemzés. További információt ebben a [cikkben](concepts-events-routing.md) talál. 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores): Ezek az API-k lehetővé teszik a biztonsági kulcs tárolóinak kezelését a terekhez. Ezek az üzletek biztonsági kulcsok gyűjteményét tárolhatják, és lehetővé teszik a legújabb érvényes kulcsok egyszerű beolvasását.

* [/users](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): Ezek az API-k lehetővé teszik a felhasználók társítására a terekhez, hogy szükség esetén megkeresse ezeket a személyeket. 

* [/system](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): Ezek az API-k lehetővé teszik a rendszerszintű beállítások, például az alapértelmezett szóközök és érzékelők kezelését. 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments): Ezek az API-k lehetővé teszik, hogy szerepköröket társítson olyan entitásokhoz, mint a felhasználói azonosító, a felhasználó által definiált függvényazonosító stb. Minden szerepkör-hozzárendelés tartalmazza a társítandó entitás azonosítóját, az entitás típusát, a társítandó szerepkör azonosítóját, a bérlőazonosítót, valamint egy elérési utat, amely meghatározza az erőforrás felső határát, amelyet az entitás az adott társítással elérhet. További információt ebben a [cikkben](security-role-based-access-control.md) talál.


## <a name="api-navigation"></a>API-navigáció

A Digital Twins API-k támogatják a szűrést és a navigációt a térbeli grafikonon a következő paraméterek használatával:

- **spaceId**: Az API az eredményeket az adott területazonosító szerint szűri. Ezenkívül a **useParentSpace** logikai jelző alkalmazható a [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) API-kra, ami azt jelzi, hogy a megadott területazonosító az aktuális terület helyett a szülőterületre utal. 

- **minLevel** és **maxLevel**: A gyökérterek az 1. Az *n* szintű szülőtérrel rendelkező szóközök *az n+1*szinten vannak. Ha ezek az értékek be vannak állítva, az eredményeket adott szinteken szűrheti. Ezek inkluzív értékek, ha be van állítva. Az eszközök, érzékelők és egyéb objektumok a legközelebbi térrel azonos szinten vannak. Ha az összes objektumot egy adott szinten szeretné letenni, állítsa a **minLevel** és a **maxLevel értéket** is ugyanarra az értékre.

- **minRelatív** és **maxRelatív**: Ha ezek a szűrők meg vannak adva, a megfelelő szint az adott térazonosító szintjéhez viszonyítva van:
   - A *0* relatív szint megegyezik az adott térazonosítóval.
   - Az *1-es* relatív szint az adott térazonosító gyermekeivel azonos szinten lévő szóközöket jelöli. Az *n* relatív szint a megadott térnél alacsonyabb szóközöket *jelöli n* szintek szerint.
   - A *-1* relatív szint a megadott tér szülőterével azonos szintű szóközöket jelöl.

- **sokszögelés**: Lehetővé teszi, hogy egy adott térazonosítóból bármelyik irányban áthaladjon a következő értékek szerint.
   - **Nincs**: Ez az alapértelmezett érték az adott területazonosítóra szűr.
   - **Le**: Ez a szűrő az adott térazonosító és leszármazottai szerint. 
   - **Fel**: Ez a szűrő az adott térazonosító és ősei szerint. 
   - **Span**: Ez szűri a térbeli grafikon vízszintes részét, ugyanazon a szinten, mint az adott térazonosító. Ehhez a **minRelative** vagy a **maxRelative** értékre van szükség. 


### <a name="examples"></a>Példák

Az alábbi lista néhány példát mutat be az [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) API-kon való navigálásra. Vegye figyelembe, `YOUR_MANAGEMENT_API_URL` hogy a helyőrző a digitális twins API-k URI-jára hivatkozik a formátumban, `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`ahol `YOUR_INSTANCE_NAME` az Azure Digital Twins-példány neve, és `YOUR_LOCATION` az a régió, ahol a példány található.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1`visszaadja a gyökérterekhez csatlakoztatott összes eszközt.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4`a 2., 3.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId`visszaadja a mySpaceId-hez közvetlenül csatlakoztatott összes eszközt.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down`visszaadja a mySpaceId-hez vagy annak egyik leszármazottjához csatolt összes eszközt.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true`visszaadja a mySpaceId leszármazottaihoz csatolt összes eszközt, kivéve a mySpaceId-et.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true`visszaadja a mySpaceId közvetlen gyermekeihez csatlakoztatott összes eszközt.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true`visszaadja a mySpaceId egyik őséhez csatlakoztatott összes eszközt.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5`visszaadja a mySpaceId leszármazottaihoz csatolt összes eszközt, amelyek 5-nél kisebb vagy azzal egyenlő szinten vannak.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true`visszaadja a mySpaceId-vel azonos szinten lévő szóközökhöz csatlakoztatott összes eszközt.


## <a name="odata-support"></a>OData-támogatás

A gyűjteményeket visszaadó API-k többsége, például a /spaces GET hívása, az általános [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) rendszerlekérdezési beállítások következő részhalmazát támogatja:  

* **$filter**
* **$orderby** 
* **$top**
* **$skip** – Ha a teljes gyűjteményt meg kívánja jeleníteni, azt egyetlen hívásban kell lekérnie, majd az alkalmazásban végre kell hajtania a lapozást. 

> [!NOTE]
> Egyes OData-beállítások (például a lekérdezési beállítások **$count,** **$expand**és **$search)** jelenleg nem támogatottak.

### <a name="examples"></a>Példák

Az alábbi lista több, érvényes OData-szintaxissal rendelkező lekérdezést ábrázol:

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,'space')`
- `YOUR_MANAGEMENT_API_URL/devices?$filter=TypeId eq 2`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=StatusId ne 1`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,'k')&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor',Name)`
 
## <a name="next-steps"></a>További lépések

Ha megismerheti a gyakori API-lekérdezési mintákat, olvassa el [az Azure Digital Twins API-k lekérdezése gyakori feladatokhoz](./how-to-query-common-apis.md)című elolvasását.

Ha többet szeretne megtudni az API-végpontokról, olvassa el [a Digitális twins swagger használata](./how-to-use-swagger.md)című.

Az OData szintaxisés a rendelkezésre álló összehasonlító operátorok áttekintéséhez olvassa el [az OData-összehasonlító operátorok az Azure Cognitive Search alkalmazásban.](../search/search-query-odata-comparison-operators.md)
