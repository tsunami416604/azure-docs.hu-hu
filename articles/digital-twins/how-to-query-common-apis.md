---
title: Az Azure digitális Twins gyakori lekérdezési minták |} A Microsoft Docs
description: Ismerje meg a gyakori minták az Azure digitális Twins felügyeleti API-k lekérdezését.
author: dsk-2015
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 1/7/2019
ms.author: dkshir
ms.openlocfilehash: ff8638042fa10c939ff9c5fa7af99a660fcdc753
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198643"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>A gyakori feladatokhoz az Azure digitális Twins API-k lekérdezése

Ez a cikk bemutatja a lekérdezési minták segítséget nyújtanak az Azure digitális Twins-példány általános forgatókönyvek végrehajtásához. A parancs feltételezi, hogy már fut a digitális Twins-példány. Bármely REST-ügyféllel, mint például a Postman is használhatja. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>A tárolóhelyek és típusok lekérdezések

Ez a szakasz bemutatja a lekérdezés a kiépített tárolóhelyek további információt szeretne kapni. Győződjön meg a mintalekérdezések, és cserélje le a helyőrzőket a telepítő a értékekkel rendelkező hitelesített első HTTP-kérelmekre. 

- Tárolóhelyek, amelyek gyökércsomópontokat beolvasása.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- Szóközzel első név alapján, és eszközök, érzékelők, számított értékek és érzékelő értékeket tartalmaznak. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- Tárolóhelyek és a saját eszköz/érzékelőitől, szülővel az adott hely azonosítója, és amely szinten 2 – 5 [képest az adott hely](how-to-navigate-apis.md#api-navigation). 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- A megadott azonosítójú a hely, és a számított és érzékelő értékeket.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- Egy adott terület tulajdonság kulcsok beolvasása.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- Tárolóhelyek első nevű tulajdonság kulccsal *AreaInSqMeters* , értéke pedig 30. Emellett karakterlánc-műveletek, például a get tulajdonság kulcsot tartalmazó tárolóhelyek `name = X contains Y`.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- Névvel rendelkező összes név beolvasása *hőmérséklet* és a hozzá tartozó függőségek és ontológiákat.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>Szerepkörök és szerepkör-hozzárendelések lekérdezések

Ez a szakasz bemutatja az egyes lekérdezések szerepkörök és hozzárendeléseik kapcsolatban további információért. 

- Az Azure digitális Twins által támogatott összes szerepkör lekérése.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- A digitális Twins-példány összes szerepkör-hozzárendelések beolvasása. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- Szerepkör-hozzárendelések beolvasása egy adott elérési úton.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>Lekérdezések eszközökhöz

Ez a szakasz bemutatja, hogyan használhatja a felügyeleti API-k információkat lehet lekérni az eszközökre vonatkozó példákat. Minden API-hívást kell hitelesített első HTTP-kérelmekre.

- Minden eszköz beolvasása.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- Keresse meg az összes eszköz állapota.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- Egy adott eszköz beolvasása.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- Minden eszköz csatlakozik a legfelső szintű hely beolvasása.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- Az összes eszköz csatlakozik a tárolóhelyek 2 – 4. szintű beolvasása.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- Az összes eszközök közvetlenül csatlakozik egy adott helyet.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- Minden csatlakozó eszközöket egy adott hely és a leszármazottai beolvasása.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- Az összes eszköz csatlakozik a következő leszármazottai: egy szóközt, kivéve, hogy a hely beolvasása.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- Az összes eszköz csatlakozik a szóközt közvetlen gyermekeinek beolvasása.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- Egy terület szintű elődökből való listázásának egyik csatlakoztatott összes eszközök beszerzése.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- Az összes eszköz csatlakozik a descendants egy helyet, amelyek kisebb vagy egyenlő 5 szint beolvasása.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- Tárolóhelyek, amelyek azonos szinten, a hely azonosítójú csatolt összes eszközök *YOUR_SPACE_ID*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- Az eszközt az IoT Hub eszköz kapcsolati karakterláncának lekérése.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- A megadott Hardverazonosító, beleértve a csatlakoztatott érzékelőkről eszköz beolvasása.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- Ebben az esetben lekérése érzékelők adott adattípusok, *mozgásban lévő adatoknak egyaránt* és *hőmérséklet*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>Lekérdezések matchers és felhasználó által definiált függvények 

- Az összes kiépített matchers és a hozzájuk tartozó azonosítóik kaphat.

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- Egy adott megfeleltetőben megadott, beleértve a tárolóhelyek és a hozzá társított felhasználó által definiált függvény kaphasson.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- Egy megfeleltetőben megadott elleni érzékelő értékelje ki és hibakeresési célra naplózás engedélyezése. A visszatérési a HTTP GET üzenet közli, hogy e a megfeleltetőben megadott és az érzékelő tartozik az adattípus. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- A felhasználó által definiált függvények Azonosítójának lekéréséhez. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- Egy adott felhasználó által definiált függvény tartalmának lekérése 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>A felhasználók számára a lekérdezések

Ez a szakasz bemutatja néhány mintalekérdezést API az Azure digitális Twins felhasználók kezeléséhez. Győződjön meg arról, egy HTTP GET kérést, és cserélje le a helyőrzőket a telepítő a megfelelő értékekkel. 

- Minden felhasználó kaphat. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- Egy adott felhasználó beolvasása.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan hitelesítheti a felügyeleti API-val, olvassa el [API-kkal való hitelesítés közben](./security-authenticating-apis.md).

Tekintse meg az összes API-végpontokat, olvassa el [használata a digitális Twins Swagger](./how-to-use-swagger.md).
