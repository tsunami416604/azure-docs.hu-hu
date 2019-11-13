---
title: Gyakori lekérdezési minták – Azure digitális Twins | Microsoft Docs
description: Ismerkedjen meg az Azure digitális Twins felügyeleti API-kkal kapcsolatos gyakori API-lekérdezési mintákkal.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/27/2019
ms.openlocfilehash: c821524b344c8b741047ba415127cffe59a74ca2
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74005897"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>Azure digitális Twins API-k lekérdezése gyakori feladatokhoz

Ez a cikk a lekérdezési mintákat mutatja be, amelyekkel az Azure Digital Twins-példányhoz tartozó gyakori forgatókönyvek hajthatók végre. Ez azt feltételezi, hogy a digitális Twins-példány már fut. Használhatja a REST-ügyfelet, például a Poster-t. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>Szóközök és típusok lekérdezése

Ez a szakasz példákat mutat be a kiépített területekre vonatkozó további információk megjelenítéséhez. Győződjön meg arról, hogy hitelesített HTTP-kérelmeket kap a lekérdezésekben, és a helyőrzőket cserélje le a telepítőből származó értékekre. 

- A legfelső szintű csomópontokat tartalmazó szóközök beolvasása.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- Adjon meg egy helyet név szerint, és vegyen fel eszközöket, érzékelőket, számított értékeket és érzékelő értékeket. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- A szóközök és azok eszköz/érzékelő információinak lekérése, amelyek szülője a megadott terület azonosítója, és amelyek a megadott területhez [képest](how-to-navigate-apis.md#api-navigation)kettő és öt közötti szinten vannak. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- A megadott AZONOSÍTÓJÚ terület beolvasása, valamint a számított és az érzékelő értékének belefoglalása.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- Adott terület tulajdonságainak beolvasása.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- Szerezzen be szóközöket a *AreaInSqMeters* nevű tulajdonsággal, értéke pedig 30. Karakterlánc-műveleteket is végrehajthat, például lekérheti a `name = X contains Y`kal rendelkező tulajdonságértékeket tartalmazó szóközöket.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- Adja meg az összes nevet a name *hőmérséklet* és a társított függőségek és ontológiákat között.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>Szerepkörök és szerepkör-hozzárendelések lekérdezései

Ez a szakasz néhány lekérdezést mutat be, amelyekkel további információkhoz juthat a szerepkörökről és azok hozzárendeléseiről. 

- Az Azure digitális Twins által támogatott összes szerepkör beolvasása.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Az összes szerepkör-hozzárendelés beolvasása a digitális Twins-példányban. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- Szerepkör-hozzárendelések beolvasása egy adott elérési úton.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>Eszközök lekérdezései

Ez a szakasz néhány példát mutat be arra, hogyan használhatja a felügyeleti API-kat az eszközökre vonatkozó konkrét információk beszerzéséhez. Minden API-hívást hitelesíteni kell a HTTP-kérések beszerzése érdekében.

- Minden eszköz beolvasása.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- Az összes eszköz állapotának megkeresése.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- Egy adott eszköz beolvasása.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- A legfelső szintű helyhez csatlakoztatott összes eszköz beolvasása.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- A 2 – 4. szinten lévő szóközökhöz csatolt összes eszköz beolvasása.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- Minden olyan eszköz beolvasása, amely közvetlenül csatlakozik egy adott tárhely-AZONOSÍTÓhoz.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- Egy adott helyhez és annak leszármazottai csatlakoztatott összes eszköz beolvasása.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- Az összes olyan eszköz beolvasása, amely egy terület leszármazottai számára van csatolva, kivéve a területet.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- Minden eszköz, amely egy szóköz közvetlen gyermekeihez csatlakozik.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- Minden olyan eszköz beolvasása, amely egy szóköz egyik őse számára van csatlakoztatva.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- Az összes olyan eszköz beolvasása, amely az 5 értéknél kisebb vagy azzal egyenlő terület leszármazottai számára van csatolva.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- Minden olyan eszköz beolvasása, amely a *YOUR_SPACE_ID*azonosítójú szóközzel azonos szinten található.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- Az eszközhöz tartozó IoT Hub eszköz-kapcsolatok karakterláncának beolvasása.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- Szerezze be az eszközt a megadott hardver-AZONOSÍTÓval, beleértve a csatlakoztatott érzékelőket is.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- Adott adattípusok érzékelők beolvasása, ebben az esetben a *mozgás* és a *hőmérséklet*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>Egyeztető és felhasználó által definiált függvények lekérdezései 

- Az összes kiosztott megfeleltetés és azok azonosítóinak beolvasása.

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- Egy adott Matcher részleteinek beolvasása, beleértve az ahhoz társított szóközöket és felhasználó által definiált függvényeket.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- Értékelje ki a Matcher egy érzékelővel, és engedélyezze a naplózást hibakeresési célból. Ennek a HTTP GET üzenetnek a visszaadásával megtudhatja, hogy a Matcher és az érzékelő az adattípushoz tartozik-e. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- A felhasználó által definiált függvények AZONOSÍTÓjának beolvasása. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- Egy adott felhasználó által definiált függvény tartalmának beolvasása 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>Felhasználók lekérdezése

Ez a szakasz néhány példa API-lekérdezést mutat be a felhasználók Azure digitális Ikrekben való kezeléséhez. Hajtson végre egy HTTP GET-kérést, és cserélje le a helyőrzőket a telepítőből származó értékekre. 

- Minden felhasználó beolvasása. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- Egy adott felhasználó beolvasása.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan végezhet hitelesítést a felügyeleti API-val, olvassa el [a hitelesítés API](./security-authenticating-apis.md)-kkal című témakört.

Ha többet szeretne megtudni az API-végpontokról, olvassa el a [digitális Twins hencegés használatát](./how-to-use-swagger.md)ismertető témakört.
