---
title: Az API-k navigálása – Azure digitális Twins | Microsoft Docs
description: Ismerje meg, hogyan lehet az Azure digitális Twins felügyeleti API-k lekérdezésének gyakori mintáit lekérdezni.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: 86ade45cd00e82e8787a117c23003d2a74750cf0
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552168"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Az Azure digitális Twins felügyeleti API-k használata

Az Azure Digital Twins felügyeleti API-k hatékony funkciókat biztosítanak a IoT-alkalmazásokhoz. Ez a cikk bemutatja, hogyan navigálhat az API-struktúrán.  

## <a name="api-summary"></a>API összefoglaló

Az alábbi listában a digitális Twins API-k összetevői láthatók.

* [/Spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): ezek az API-k a telepítő fizikai helyeivel működnek együtt. Ezek segítenek a fizikai helyeknek a [térbeli gráf](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph)formájában történő digitális hozzárendelésének létrehozását, törlését és kezelését.

* [/Devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): ezek az API-k a telepítő eszközeivel működnek együtt. Ezek az eszközök egy vagy több érzékelőt is kezelhetnek. Előfordulhat például, hogy egy eszköz lehet a telefonja, vagy egy málna PI szenzor Pod vagy egy Lora-átjáró, és így tovább.

* [/sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): ezek az API-k segítenek az eszközökhöz és a fizikai helyekhez kapcsolódó érzékelőkkel való kommunikációban. Az érzékelők rögzítik és elküldik a környezeti értékeket, amelyek ezután a térbeli környezet kezelésére használhatók.  

* [/Resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources): ezek az API-k segítenek a digitális Twins-példányokhoz tartozó erőforrások, például egy IoT hub beállításában.

* [/types](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): ezek az API-k lehetővé teszik, hogy a kibővített típusokat a digitális Twins-objektumokkal társítsa, hogy adott tulajdonságokat adjon hozzá az objektumokhoz. Ezek a típusok lehetővé teszik az objektumok egyszerű szűrését és csoportosítását a felhasználói felületen, valamint a telemetria-adatok feldolgozására szolgáló egyéni függvényeket. Kibővített típusok például a következők: *DeviceType*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceSubType*, *SpaceBlobType*, *SpaceResourceType*stb.

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): ezek az API-k segítenek a ontológiákat kezelésében, amely kibővített típusok gyűjteménye. Ontológiákat adja meg az Objektumtípusok nevét az általuk képviselt fizikai térben. Például a *BACnet* ontológia speciális neveket biztosít az *érzékelők típusaihoz*, az *adattípusokhoz*, a *datasubtypes*és a *dataunittypes*. A ontológiákat a szolgáltatás felügyeli és hozza létre. A felhasználók betölthetik és kitölthetik a ontológiákat. Az ontológia betöltését követően az összes társított típus neve engedélyezve van, és készen áll a térbeli gráfban való üzembe helyezésre. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): ezekkel az API-kkal egyéni tulajdonságokat hozhat létre a *tárhelyekhez*, *eszközökhöz*, *felhasználókhoz*és *érzékelőkhöz*. Ezek a tulajdonságok kulcs/érték párokként jönnek létre. Ezeknek a tulajdonságoknak az adattípusa a *PrimitiveDataType*beállításával adható meg. Megadhat például egy *BasicTemperatureDeltaProcessingRefreshTime* típusú *uint* az érzékelőkhöz, majd hozzárendelheti a tulajdonság értékét az egyes érzékelőkhöz. Ezekhez az értékekhez korlátozásokat is hozzáadhat a tulajdonság létrehozásakor, például a *min* és a *Max* tartományt, valamint az engedélyezett értékeket *ValidationData*.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): ezek az API-k lehetővé teszik a beérkező eszközök adatainak kiértékeléséhez használandó feltételek megadását. További információkért tekintse meg [ezt a cikket](concepts-user-defined-functions.md#matchers). 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): ezek az API-k lehetővé teszik egy egyéni függvény létrehozását, törlését vagy frissítését, amely akkor fut le, ha az *egyeztetések* által meghatározott feltételek alapján történik a telepítésből származó adatok feldolgozása. [Ebből a cikkből](concepts-user-defined-functions.md#user-defined-functions) megtudhatja, hogyan használhatja ezeket az egyéni függvényeket, más néven a *felhasználó által definiált függvényeket*. 

* [/endpoints](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints): ezek az API-k lehetővé teszik a végpontok létrehozását, így a digitális Twins-megoldás más Azure-szolgáltatásokkal is képes kommunikálni az adattárolással és az elemzéssel. További információért olvassa el [ezt a cikket](concepts-events-routing.md) . 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores): ezek az API-k lehetővé teszik a tárhelyek biztonsági kulcs-tárolóinak kezelését. Ezek a tárolók biztonsági kulcsok gyűjteményét tárolják, és lehetővé teszik a legújabb érvényes kulcsok lekérését.

* [/Users](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): ezek az API-k lehetővé teszik, hogy a felhasználókat a saját tárhelyéhez társítsa, ha szükséges, megkeresheti ezeket a személyeket. 

* [/System](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): ezek az API-k lehetővé teszik a rendszerszintű beállítások kezelését, például a szóközök és érzékelők alapértelmezett típusait. 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments): ezek az API-k lehetővé teszik szerepkörök hozzárendelését olyan entitásokhoz, mint például a felhasználói azonosító, a felhasználó által definiált függvény azonosítója stb. Minden szerepkör-hozzárendelés tartalmazza a társítani kívánt entitás AZONOSÍTÓját, az entitás típusát, a társítani kívánt szerepkör AZONOSÍTÓját, a bérlő AZONOSÍTÓját, valamint egy elérési utat, amely meghatározza azon erőforrás felső határát, amelyet az entitás elérhet az adott társítással. További információért olvassa el [ezt a cikket](security-role-based-access-control.md) .


## <a name="api-navigation"></a>API-navigáció

A digitális Twins API-k támogatják a térbeli gráf szűrését és navigálását a következő paraméterek használatával:

- **spaceId**: az API a megadott lemezterület-azonosító alapján fogja szűrni az eredményeket. Emellett a logikai jelző **useParentSpace** is alkalmazható a [/Spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) API-kra, ami azt jelzi, hogy a megadott hely azonosítója az aktuális terület helyett a szülő területre hivatkozik. 

- **minLevel** és **maxLevel**: a gyökérszintű szóközök az 1. szinten tekinthetők meg. Az *n* szintű szülő szóközzel rendelkező szóközök *n + 1*szinten vannak. Ezekkel az értékekkel meghatározott szinteken szűrheti az eredményeket. Ezek befogadó értékek, ha be van állítva. Az eszközök, érzékelők és egyéb objektumok ugyanazon a szinten lesznek, mint a legközelebbi helyük. Egy adott szinten lévő összes objektum lekéréséhez állítsa a **minLevel** és a **maxLevel** értéket ugyanarra az értékre.

- **minRelative** és **maxRelative**: ha ezek a szűrők meg vannak adva, a megfelelő szint a megadott hely azonosítójának szintjéhez képest:
   - A *0* . relatív szint megegyezik a megadott lemezterület-azonosítóval.
   - Az *1* . relatív szint a megadott hely azonosítójának gyermekeivel megegyező szinten lévő szóközöket jelöli. Az *n* relatív szint azt jelenti, hogy az *n* szintnél kevesebb szóköz van a megadott helynél.
   - A relatív szint *-1* érték a megadott terület fölérendelt területével azonos szinten lévő szóközöket jelöli.

- **bejárás**: lehetővé teszi, hogy a következő értékek alapján egy adott hely azonosítójának bármelyik irányában bejárjon.
   - **Nincs**: ez az alapértelmezett érték a megadott lemezterület-azonosítóra szűri.
   - Leállt: ezt a szűrőt a megadott lemezterület-azonosító és annak leszármazottai határozzák **meg**. 
   - **Up**: ezt a szűrőt a megadott lemezterület-azonosító és az őseik jelentik. 
   - **Span**: Ez a térbeli gráf vízszintes részét szűri, a megadott lemezterület-azonosítóval megegyező szinten. Ehhez a **minRelative** vagy a **maxRelative** True értékre kell állítani. 


### <a name="examples"></a>Példák

Az alábbi lista néhány példát mutat be a [/Devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) API-kon keresztül történő navigálásra. Vegye figyelembe, hogy a helyőrző `YOUR_MANAGEMENT_API_URL` a digitális Twins API-k URI-JÁT adja meg `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`formátumban, ahol a `YOUR_INSTANCE_NAME` az Azure Digital Twins-példány neve, a `YOUR_LOCATION` pedig az a régió, ahol a példány üzemeltetve van.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1` a legfelső szintű helyekhez csatolt összes eszközt visszaadja.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4` a 2., 3. és 4. szintű szóközökhöz csatolt összes eszközt adja vissza.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId` visszaadja az összes olyan eszközt, amely közvetlenül a mySpaceId van csatolva.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down` visszaadja a mySpaceId csatolt összes eszközt vagy annak valamelyik leszármazottját.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true` visszaadja a mySpaceId leszármazottai számára csatolt összes eszközt, kivéve a mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true` a mySpaceId közvetlen gyermekeihez csatolt összes eszközt visszaadja.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true` a mySpaceId egyik elődje számára csatolt összes eszközt visszaadja.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5` visszaadja az összes olyan eszközt, amely az 5 értéknél kisebb vagy azzal egyenlő mySpaceId leszármazottai számára van csatolva.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true` visszaadja a mySpaceId azonos szinten lévő szóközökhez csatolt összes eszközt.


## <a name="odata-support"></a>OData-támogatás

A gyűjteményeket visszaadó API-k többsége, mint például a GET Call on/Spaces, az általános [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) rendszer-lekérdezési lehetőségek következő részhalmazát támogatja:  

* **$filter**
* **$orderby** 
* **$top**
* **$skip** – ha a teljes gyűjteményt szeretné megjeleníteni, egyetlen hívásban teljes készletként kell igényelnie, majd az alkalmazásban lapozást kell végrehajtania. 

> [!NOTE]
> Bizonyos OData lehetőségek (például a lekérdezési beállítások **$Count**, a **$expand**és a **$Search**) nem támogatottak.

### <a name="examples"></a>Példák

Az alábbi lista számos, érvényes OData szintaxissal rendelkező lekérdezést ábrázol:

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,'space')`
- `YOUR_MANAGEMENT_API_URL/devices?$filter=TypeId eq 2`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=StatusId ne 1`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,'k')&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor',Name)`
 
## <a name="next-steps"></a>Következő lépések

Az API-lekérdezések gyakori mintáinak megismeréséhez olvassa el [Az Azure Digital Twins API-k lekérdezése gyakori feladatokhoz című témakört](./how-to-query-common-apis.md).

Ha többet szeretne megtudni az API-végpontokról, olvassa el a [digitális Twins hencegés használatát](./how-to-use-swagger.md)ismertető témakört.

A OData szintaxis és a rendelkezésre álló összehasonlító operátorok áttekintéséhez olvassa el [a OData-összehasonlító operátorokat az Azure Cognitive Searchban](../search/search-query-odata-comparison-operators.md).
