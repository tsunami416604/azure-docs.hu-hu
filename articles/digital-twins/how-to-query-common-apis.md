---
title: Gyakori lekérdezési minták - Azure Digital Twins | Microsoft dokumentumok
description: Ismerje meg az Azure Digital Twins felügyeleti API-k számos gyakori API-lekérdezési mintázatát.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 133c0e0dcc07afb85a0f3af9ae51d2207abac293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589113"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>Az Azure Digital Twins API-k lekérdezése gyakori feladatokhoz

Ez a cikk lekérdezési mintákat jelenít meg, amelyek segítségével az Azure Digital Twins-példány gyakori forgatókönyveit futtathatja. Ez feltételezi, hogy a digitális twins példány már fut. Bármilyen REST-ügyfelet használhat, például postást. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>Szóközök és -típusok lekérdezései

Ez a szakasz mintalekérdezéseket jelenít meg a kiépített terekkel kapcsolatos további információkért. Hitelesített GET HTTP-kérelmeket tehet a mintalekérdezésekkel, és a helyőrzőket a beállításból származó értékekre cserélheti. 

- Gyökércsomópontok at kap nak.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- Név szerint helyet kaphat, és eszközöket, érzékelőket, számított értékeket és érzékelőértékeket tartalmazhat. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- Helyek és az eszköz/érzékelő adatainak lefedése, amelyek szülője az adott térazonosító, és amelyek [az adott területhez képest](how-to-navigate-apis.md#api-navigation)2–5. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- A megadott azonosítóval helyet kaphat, és meg kell adnia a számított és az érzékelő értékeket.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- Tulajdonságkulcsok beszereznie egy adott területhez.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- *AreaInSqMeters* nevű tulajdonságkokkal rendelkező szóközök bekérése, értéke pedig 30. Karakterlánc-műveleteket is eltud végezni, például a `name = X contains Y`tulajdonságkulcsot tartalmazó szóközöket a segítségével.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- Az összes név lejáró *neve Hőmérséklet* és a kapcsolódó függőségek és ontológiák.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>Szerepkörök és szerepkör-hozzárendelések lekérdezései

Ez a szakasz néhány lekérdezést mutat be, hogy további információkat kapjon a szerepkörökről és azok hozzárendeléseiről. 

- Az Azure Digital Twins által támogatott összes szerepkört lekaphatja.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Az összes szerepkör-hozzárendelést a digitális twins-példányban kaphatja meg. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- Szerepkör-hozzárendelések beszereznie egy adott elérési útra.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>Eszközök lekérdezései

Ez a szakasz néhány példát mutat be arra, hogyan használhatja a felügyeleti API-kat az eszközökre vonatkozó konkrét információk lefelvételéhez. Minden API-hívást hitelesíteni kell a GET HTTP-kérelmek.

- Minden eszközt beszerezni.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- Keresse meg az összes eszközállapotot.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- Szerezzen be egy adott eszközt.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- Az összes eszköz csatlakoztatása a gyökérterülethez.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- Kap minden eszköz csatlakozik terek szinten 2-4.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- Az összes eszköz közvetlenül egy adott térazonosítóhoz csatlakoztatva.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- Kap minden eszköz csatlakozik egy adott térben, és annak leszármazottai.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- Kap minden eszköz csatolt leszármazottai egy helyet, kivéve, hogy a tér.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- Az összes eszköz csatlakoztatása a tér közvetlen gyermekeihez.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- Kap minden eszköz csatlakozik az egyik őse itér.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- Az 5-nél kisebb vagy azzal egyenlő szintű szóköz leszármazottaihoz csatlakoztatott összes eszköz beszerezni.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- Az azonosítóval rendelkező térrel azonos szintű szóközökhöz csatlakoztatott összes eszköz *beszerezni YOUR_SPACE_ID.*

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- Az IT Hub-eszköz kapcsolati karakterláncának beszereznie az eszközhöz.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- Az adott hardverazonosítóval rendelkező eszköz, beleértve a csatlakoztatott érzékelőket is.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- Az egyes adattípusok, ebben az esetben a *Mozgás* és *a Hőmérséklet*érzékelőkbe kerül.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>Egyeztetők és felhasználó által definiált függvények lekérdezései 

- Az összes kiépített egyeztető és az azonosítók.

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- Egy adott egyező részletei, beleértve a szóközöket és a hozzá társított felhasználó által definiált függvényt.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- Értékelje ki a matcher egy érzékelő, és lehetővé teszi a naplózás hibakeresési célokra. A HTTP GET üzenet visszaadása jelzi, hogy a matcher és az érzékelő az adattípushoz tartozik-e. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- A felhasználó által definiált függvények azonosítójának beszereznie. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- Egy adott felhasználó által definiált függvény tartalmának beszerezni 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>Felhasználóklekérdezései

Ez a szakasz néhány minta API-lekérdezések a felhasználók kezelésére az Azure Digital Twins. Http GET-kérést kérek, amely a helyőrzőket a telepítőértékeire cseréli. 

- Minden felhasználó beszerezni. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- Szerezzen be egy adott felhasználót.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>További lépések

Ha meg szeretné tudni, hogyan hitelesítheti magát a Felügyeleti API-val, olvassa el [a Hitelesítés API-kkal](./security-authenticating-apis.md).

Ha többet szeretne megtudni az API-végpontokról, olvassa el [a Digitális twins swagger használata](./how-to-use-swagger.md)című.
