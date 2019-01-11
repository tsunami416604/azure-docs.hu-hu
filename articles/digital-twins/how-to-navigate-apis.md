---
title: Keresse meg az Azure digitális Twins API-k |} A Microsoft Docs
description: Ismerje meg, hogyan lehet gyakori mintáinak lekérdezése az Azure digitális Twins felügyeleti API-k.
author: dsk-2015
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 1/7/2019
ms.author: dkshir
ms.openlocfilehash: 9cfcdc879e36b93e21bff6f91886536d799553bb
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54200955"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Az Azure digitális Twins felügyeleti API-k használata

Az Azure digitális Twins felügyeleti API-k az IoT-alkalmazások hatékony funkciókat biztosítanak. Ez a cikk bemutatja, hogyan navigálhat az API struktúráját.  

## <a name="api-summary"></a>API összefoglaló

Az alábbi lista a digitális Twins API-k összetevői láthatók.

* [/ tárolóhelyek](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): Ezen API-k a fizikai helyeken és a telepítő dolgozhat. Ezek segítenek létrehozása, törlése és a fizikai helyek formájában a digitális leképezések kezelésére egy [térbeli graph](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph).

* [/Devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): Ezen API-k a telepítő az eszközök kezelése. Ezek az eszközök egy vagy több érzékelők kezelheti. Például egy eszköz sikerült kell a telefonra, vagy a Raspberry Pi érzékelő podot, vagy a-val készült Lora-átjáró, és így tovább.

* [/Sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): Ezen API-k segítségével az érzékelők, az eszközök és a fizikai helyek társított folytatott kommunikációhoz. Az érzékelők jegyezze fel, és küldje el környezeti értékek, amelyek ezután felhasználhatók a térbeli környezet módosítására.  

* [/resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources): Ezen API-k segítségével állítsa be az erőforrások, például egy IoT hubot, a digitális Twins példány.

* [/ típusokat](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): Ezen API-k lehetővé teszik a kiterjesztett típusok társítani a digitális Twins objektumok különböző jellemzőket hozzá azokat az objektumokat. Ezek a típusok egyszerű szűrési és az objektumok a felhasználói felület és az egyéni függvények, amelyek a telemetriai adatok feldolgozása a csoportosítás lehetővé teszi. Néhány példa a kiterjesztett típusok *megtalált*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceSubType* , *SpaceBlobType*, *SpaceResourceType*, és így tovább.

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): Ezen API-k segítségével ontológiákat, amelyek kiterjesztett típusú gyűjtemények kezelése. Ontológiákat adott típusú objektumokat jelölnek a fizikai hely alapján nevet. Például a *BACnet* ontology biztosítja az egyedi nevek *érzékelő típusok*, *adattípusokat*, *datasubtypes*, és *dataunittypes*. Ontológiákat felügyelt és a szolgáltatás által létrehozott. Felhasználók betöltése és eltávolítása ontológiákat. Egy ontology betöltésekor a társított típusnevek mind engedélyezve van, és készen áll a térbeli graph építhető ki. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): Ezen API-k segítségével egyéni tulajdonságok létrehozása a *tárolóhelyek*, *eszközök*, *felhasználók*, és *érzékelők*. Ezek a tulajdonságok kulcs/érték párokként jönnek létre. Ezek a Tulajdonságok adattípusa a beállításával meghatározhatja a *PrimitiveDataType*. Például megadhatja a nevű tulajdonság *BasicTemperatureDeltaProcessingRefreshTime* típusú *uint* az érzékelők és az egyes az érzékelők majd hozzárendelése egy értéket ehhez a tulajdonsághoz. Ezeket az értékeket a korlátozásokat a tulajdonságot használja, mint például létrehozása közben is hozzáadhat *Min* és *maximális* címtartományok, valamint az engedélyezett értékek *ValidationData*.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): Ezen API-k lehetővé teszik az olyan feltételeket határoznak meg a bejövő eszköz adatokból kiértékelni kívánt. Lásd: [Ez a cikk](concepts-user-defined-functions.md#matchers) további információt. 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): Ezen API-k hozhatók létre, delete vagy update egyéni függvény, amely végrehajtja a mikor feltételek határozzák meg a *matchers* történik, a telepítő érkező adatok feldolgozásához. Lásd: [Ez a cikk](concepts-user-defined-functions.md#user-defined-functions) további információt az egyéni függvényekhez, más néven a *felhasználó által definiált függvények*. 

* [/endpoints](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints): Ezekkel az API-végpontokat hozza létre, így a digitális Twins megoldás képes kommunikálni más Azure-szolgáltatások az adattárolási és -elemzési teszi lehetővé. Olvasási [Ez a cikk](concepts-events-routing.md) további információt. 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores): Ezen API-k lehetővé teszi a tárolóhelyek legfontosabb tárolóinak biztonsági felügyeletét. Ezekkel az áruházakkal gyűjteménye, a biztonsági kulcsok tárolására, és lehetővé teszi, hogy könnyen hozzáférhet a legutóbb érvényes kulcsok.

* [/ felhasználók](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): Ezen API-k lehetővé teszik rendelje hozzá a felhasználókat a tárolóhelyek, szükség esetén ezek a személyek kereséséhez. 

* [vagy](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): Ezen API-k rendszerre kiterjedő beállítások, például a tárolóhelyek és érzékelők alapértelmezett típusú kezelését teszi lehetővé. 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments): Ezen API-k lehetővé teszik társítsa a szerepköröket, például a felhasználói Azonosítóját, a felhasználó által definiált függvény azonosító, stb. Minden egyes szerepkör-hozzárendelés tartalmazza a szeretné ellátni, az entitástípus, társítása a szerepkör Azonosítóját, a bérlő Azonosítóját és egy elérési utat, amely meghatározza a felső határ az erőforrás, amely az entitás a társítás elérhet az entitás azonosítója. Olvasási [Ez a cikk](security-role-based-access-control.md) további információt.


## <a name="api-navigation"></a>API-navigáció

A digitális Twins API-k támogatják a szűrés és a navigáció során a térbeli grafikon, a következő paraméterekkel:

- **spaceId**: Az API-t fog szűrni az eredményeket az adott hely által Ezenkívül a logikai jelzőt **useParentSpace** alkalmazható a [/tárolóhelyek](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) API-k, ami azt jelzi, hogy az adott hely azonosítója a fölérendelt hely helyett az aktuális hely hivatkozik. 

- **minLevel** és **maxLevel**: Legfelső szintű tárolóhelyek tekintendők az 1. A lemezterület-tárolóhelyek szülő szintjén *n* szintű *n + 1*. Ezen értékek beállítása az adott szinten az eredményeket szűrheti. Inkluzív érték megadása esetén. Eszközök, érzékelők és más objektumok minősülnek azonos szinten, azok legközelebb eső helyet. Összes objektum beolvasása egy adott szinten, állítsa mind **minLevel** és **maxLevel** ugyanarra az értékre.

- **minRelative** és **maxRelative**: Ezek a szűrők vannak megadva, a megfelelő szint esetén viszonyított szintjét az adott hely azonosítója:
   - Relatív szint *0* van, mint az azonos szinten az adott hely azonosítója.
   - Relatív szint *1* jelöli az adott hely azonosítójának azonos szinten, a gyermekek tárolóhelyek Relatív szint *n* tárolóhelyek alacsonyabb, mint a megadott szabad helyet jelöli *n* szinteket.
   - Relatív szint *-1* azonos szinten, a megadott hely a fölérendelt hely tárolóhelyek jelöli.

- **bejárás**: Lehetővé teszi, hogy egy adott hely forrásazonosítója leírtak szerint a következő értékeket a bármelyik irányba haladnak át.
   - **Nincs**: Ez az alapértelmezett érték szűri az adott helyet azonosító.
   - **Lefelé**: Ezzel úgy szűri az adott hely azonosítója és a leszármazottai. 
   - **Felfelé**: Ezzel úgy szűri az adott hely azonosítója és elődjéhez. 
   - **Az időtartományt**: A szűrés egy vízszintes része a térbeli graph azonos szinten, a megadott terület azonosítója. Ez van szüksége, vagy a **minRelative** vagy a **maxRelative** igaz kell beállítani. 


### <a name="examples"></a>Példák

Az alábbi listában látható néhány példa a navigáláshoz az [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) API-k. Vegye figyelembe, hogy a helyőrző `YOUR_MANAGEMENT_API_URL` URI-ját a digitális Twins API-k formátumban hivatkozik `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`, ahol `YOUR_INSTANCE_NAME` az Azure digitális Twins-példány neve és `YOUR_LOCATION` a régió, ahol a példány üzemel.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1` az összes eszköz csatlakozik a legfelső szintű tárolóhelyek adja vissza.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4` az összes eszköz csatlakozik a tárolóhelyek szintek, 2, 3 vagy 4 adja vissza.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId` minden eszköz mySpaceId közvetlenül csatlakoztatott adja vissza.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down` az összes eszköz csatlakozik a mySpaceId vagy a leszármazottai közül adja vissza.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true` az összes eszköz csatlakozik a következő leszármazottai: mySpaceId, kivéve a mySpaceId adja vissza.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true` az összes eszköz csatlakozik a szegmenscsomópontok közvetlen gyermekeinek mySpaceId adja vissza.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true` minden eszköz csatlakozik a elődei mySpaceId az egyik adja vissza.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5` minden eszköz csatlakozik, amely kisebb vagy egyenlő 5 szintű mySpaceId leszármazott adja vissza.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true` az összes eszköz csatlakozik a szóközöket a rendszer mySpaceId ugyanazon a szinten adja vissza.


## <a name="odata-support"></a>OData-támogatás
A gyűjtemények, például egy GET hívást kell /spaces, a visszatérő API-kat a legtöbb támogatja a következő részhalmazát alkotják, az általános [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) rendszerlekérdezési beállításait:  

* **$filter**
* **$orderby** 
* **$top**
* **a $skip** – szeretne megjeleníteni a teljes gyűjteményt, ha kell igényli, egyetlen hívással teljes készletként, és hajtsa végre a lapozófájl az alkalmazásban. 

Vegye figyelembe, hogy bontsa ki a más lekérdezési beállításokkal, például a $count, $, $search, nem támogatottak.

### <a name="examples"></a>Példák

Az alábbi listában néhány példa a lekérdezéseket a OData rendszerlekérdezési beállításait mutatja:

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,’space’)`
- `YOUR_MANAGEMENT_API_URL/propertykeys?$filter=Scope ne ‘Spaces’`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=Size gt ‘M’`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,’k’)&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor’,Name)`
 

## <a name="next-steps"></a>További lépések

További információt az API gyakori lekérdezési minták, [hogyan lehet a gyakori feladatokhoz az Azure digitális Twins API-k lekérdezést](how-to-query-common-apis.md).


