---
title: Azure digitális Twins API-k navigálása | Microsoft Docs
description: Ismerje meg, hogyan lehet az Azure digitális Twins felügyeleti API-k lekérdezésének gyakori mintáit lekérdezni.
author: kingdomofends
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: v-adgera
ms.openlocfilehash: 8472a86800d13cedd228ca881a7c095ff748350a
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172822"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Az Azure digitális Twins felügyeleti API-k használata

Az Azure Digital Twins felügyeleti API-k hatékony funkciókat biztosítanak a IoT-alkalmazásokhoz. Ez a cikk bemutatja, hogyan navigálhat az API-struktúrán.  

## <a name="api-summary"></a>API összefoglaló

Az alábbi listában a digitális Twins API-k összetevői láthatók.

* [/Spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): Ezek az API-k a telepítő fizikai helyeivel működnek. Ezek segítenek a fizikai helyeknek a [térbeli gráf](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph)formájában történő digitális hozzárendelésének létrehozását, törlését és kezelését.

* [/Devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): Ezek az API-k a telepítő eszközeit használják. Ezek az eszközök egy vagy több érzékelőt is kezelhetnek. Előfordulhat például, hogy egy eszköz lehet a telefonja, vagy egy málna PI szenzor Pod vagy egy Lora-átjáró, és így tovább.

* [/sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): Ezek az API-k segítenek az eszközökhöz és a fizikai helyekhez kapcsolódó érzékelőkkel való kommunikációban. Az érzékelők rögzítik és elküldik a környezeti értékeket, amelyek ezután a térbeli környezet kezelésére használhatók.  

* [/Resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources): Ezek az API-k lehetővé teszik az erőforrások, például az IoT hub beállítását a digitális Twins-példányhoz.

* [/types](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): Ezek az API-k lehetővé teszik, hogy a kibővített típusokat a digitális Twins-objektumokkal társítsa, hogy adott tulajdonságokat adjon hozzá az objektumokhoz. Ezek a típusok lehetővé teszik az objektumok egyszerű szűrését és csoportosítását a felhasználói felületen, valamint a telemetria-adatok feldolgozására szolgáló egyéni függvényeket. Kibővített típusok például a következők: *DeviceType*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceSubType*, *SpaceBlobType*, *SpaceResourceType*stb.

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): Ezek az API-k segítenek a ontológiákat felügyeletében, amelyek a kiterjesztett típusok gyűjteményei. Ontológiákat adja meg az Objektumtípusok nevét az általuk képviselt fizikai térben. Például a *BACnet* ontológia speciális neveket biztosít az *érzékelők típusaihoz*, azadattípusokhoz, a *datasubtypes*és a *dataunittypes*. A ontológiákat a szolgáltatás felügyeli és hozza létre. A felhasználók betölthetik és kitölthetik a ontológiákat. Az ontológia betöltését követően az összes társított típus neve engedélyezve van, és készen áll a térbeli gráfban való üzembe helyezésre. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): Ezen API-k használatával egyéni tulajdonságokat hozhat létre a *tárhelyekhez*, *eszközökhöz*, *felhasználókhoz*és *érzékelőkhöz*. Ezek a tulajdonságok kulcs/érték párokként jönnek létre. Ezeknek a tulajdonságoknak az adattípusa a *PrimitiveDataType*beállításával adható meg. Megadhat például egy *BasicTemperatureDeltaProcessingRefreshTime* típusú *uint* az érzékelőkhöz, majd hozzárendelheti a tulajdonság értékét az egyes érzékelőkhöz. Ezekhez az értékekhez korlátozásokat is hozzáadhat a tulajdonság létrehozásakor, például a *min* és a *Max* tartományt, valamint az engedélyezett értékeket *ValidationData*.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): Ezek az API-k lehetővé teszik a beérkező eszközök adatainak kiértékeléséhez használandó feltételek megadását. További információt [ebben a cikkben](concepts-user-defined-functions.md#matchers) talál. 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): Ezek az API-k lehetővé teszik egy olyan egyéni függvény létrehozását, törlését vagy frissítését, amely akkor fut le, amikor az egyeztető által meghatározott feltételek történnek a Telepítőtől érkező adatok feldolgozásához. [Ebből](concepts-user-defined-functions.md#user-defined-functions) a cikkből megtudhatja, hogyan használhatja ezeket az egyéni függvényeket, más néven a *felhasználó által definiált függvényeket*. 

* [/endpoints](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints): Ezek az API-k lehetővé teszik a végpontok létrehozását, így a digitális Twins-megoldás képes kommunikálni más Azure-szolgáltatásokkal az adattároláshoz és az elemzésekhez. További információért olvassa el [ezt a cikket](concepts-events-routing.md) . 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores): Ezek az API-k lehetővé teszik a tárhelyekhez tartozó biztonsági kulcsok tárolóinak kezelését. Ezek a tárolók biztonsági kulcsok gyűjteményét tárolják, és lehetővé teszik a legújabb érvényes kulcsok lekérését.

* [/Users](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): Ezek az API-k lehetővé teszik a felhasználók szóközzel való hozzárendelését, hogy szükség esetén megtalálják ezeket az személyeket. 

* [/System](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): Ezek az API-k lehetővé teszik a rendszerszintű beállítások kezelését, például a szóközök és érzékelők alapértelmezett típusait. 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments): Ezek az API-k lehetővé teszik szerepkörök hozzárendelését olyan entitásokhoz, mint például a felhasználói azonosító, a felhasználó által definiált függvény azonosítója stb. Minden szerepkör-hozzárendelés tartalmazza a társítani kívánt entitás AZONOSÍTÓját, az entitás típusát, a társítani kívánt szerepkör AZONOSÍTÓját, a bérlő AZONOSÍTÓját, valamint egy elérési utat, amely meghatározza azon erőforrás felső határát, amelyet az entitás elérhet az adott társítással. További információért olvassa el [ezt a cikket](security-role-based-access-control.md) .


## <a name="api-navigation"></a>API-navigáció

A digitális Twins API-k támogatják a térbeli gráf szűrését és navigálását a következő paraméterek használatával:

- **spaceId**: Az API az eredményeket a megadott lemezterület-azonosító alapján fogja szűrni. Emellett a logikai jelző **useParentSpace** is alkalmazható a [/Spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) API-kra, ami azt jelzi, hogy a megadott hely azonosítója az aktuális terület helyett a szülő területre hivatkozik. 

- **minLevel** és **maxLevel**: A legfelső szintű szóközök az 1. szintnek tekintendők. Az *n* szintű szülő szóközzel rendelkező szóközök *n + 1*szinten vannak. Ezekkel az értékekkel meghatározott szinteken szűrheti az eredményeket. Ezek befogadó értékek, ha be van állítva. Az eszközök, érzékelők és egyéb objektumok ugyanazon a szinten lesznek, mint a legközelebbi helyük. Egy adott szinten lévő összes objektum lekéréséhez állítsa a **minLevel** és a **maxLevel** értéket ugyanarra az értékre.

- **minRelative** és **maxRelative**: Ha ezek a szűrők meg vannak adva, a megfelelő szint a megadott hely AZONOSÍTÓjának szintjéhez képest:
   - A *0* . relatív szint megegyezik a megadott lemezterület-azonosítóval.
   - Az *1* . relatív szint a megadott hely azonosítójának gyermekeivel megegyező szinten lévő szóközöket jelöli. Az *n* relatív szint azt jelenti, hogy az *n* szintnél kevesebb szóköz van a megadott helynél.
   - A relatív szint *-1* érték a megadott terület fölérendelt területével azonos szinten lévő szóközöket jelöli.

- **bejárás**: Lehetővé teszi, hogy az alábbi értékek alapján áthaladjon egy adott hely AZONOSÍTÓjának egyik irányában.
   - **Nincs**: Ez az alapértelmezett érték a megadott lemezterület-AZONOSÍTÓra szűri.
   - **Lefelé**: Ezt a szűrőt a megadott lemezterület-azonosító és annak leszármazottai jelentik. 
   - **Fel**: Ezt a szűrőt a megadott lemezterület-azonosító és az őseik jelentik. 
   - **Span**: Ez a térbeli gráf vízszintes részét szűri, a megadott lemezterület-AZONOSÍTÓval megegyező szinten. Ehhez a **minRelative** vagy a **maxRelative** True értékre kell állítani. 


### <a name="examples"></a>Példák

Az alábbi lista néhány példát mutat be a [/Devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) API-kon keresztül történő navigálásra. Vegye figyelembe, hogy `YOUR_MANAGEMENT_API_URL` a helyőrző a digitális Twins API-k URI-jának formátumában `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`hivatkozik, ahol `YOUR_INSTANCE_NAME` az az Azure Digital Twins-példány neve `YOUR_LOCATION` , az pedig az a régió, ahol a példánya üzemeltetve van.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1`a legfelső szintű helyekhez csatolt összes eszközt adja vissza.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4`a 2., 3. és 4. szintű szóközökhöz csatolt összes eszközt adja vissza.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId`a mySpaceId közvetlenül csatolt összes eszközt adja vissza.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down`visszaadja a mySpaceId csatolt összes eszközt vagy annak valamelyik leszármazottját.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true`a mySpaceId leszármazottai számára csatolt összes eszközt adja vissza, a mySpaceId kivételével.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true`a mySpaceId közvetlen gyermekeihez csatolt összes eszközt adja vissza.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true`a mySpaceId egyik elődje számára csatolt összes eszközt adja vissza.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5`visszaadja az összes olyan eszközt, amely az 5 értéknél kisebb vagy azzal egyenlő mySpaceId leszármazottai számára van csatolva.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true`a mySpaceId azonos szinten lévő szóközökhez csatolt összes eszközt adja vissza.


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
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,’space’)`
- `YOUR_MANAGEMENT_API_URL/propertykeys?$filter=Scope ne ‘Spaces’`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=Size gt ‘M’`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,’k’)&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor’,Name)`
 
## <a name="next-steps"></a>További lépések

Az API-lekérdezések gyakori mintáinak megismeréséhez olvassa el [Az Azure Digital Twins API](./how-to-query-common-apis.md)-k lekérdezése gyakori feladatokhoz című témakört.

Ha többet szeretne megtudni az API-végpontokról, olvassa el a [digitális Twins hencegés használatát](./how-to-use-swagger.md)ismertető témakört.

A OData szintaxisának és az elérhető összehasonlító operátorok áttekintéséhez olvassa el [a OData-összehasonlító operátorokat a Azure Search](../search/search-query-odata-comparison-operators.md).
