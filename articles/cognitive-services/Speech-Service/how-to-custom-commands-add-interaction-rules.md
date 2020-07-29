---
title: 'Útmutató: megerősítés hozzáadása egyéni parancshoz'
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan valósítható meg a parancsok megerősítése egyéni parancsokban.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 7d6c0928196c9e8e1abf6aa7f724a58753ce3d2a
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289050"
---
# <a name="add-interaction-rules"></a>Használati szabályok hozzáadása

Ebben a cikkben az **interakciós szabályokról**olvashat. Ezek a további szabályok az összetettebb vagy összetett helyzetek kezelésére szolgálnak. Habár ingyenes a saját egyéni interakciós szabályainak megalkotása, ebben a cikkben a következő célcsoportokra vonatkozó interakciós szabályokat használhatja:

* Parancsok megerősítése
* Egy lépésből álló korrekció hozzáadása parancsokhoz

Ha többet szeretne megtudni a kapcsolati szabályokról, ugorjon a [hivatkozások](./custom-commands-references.md) szakaszra.

## <a name="prerequisites"></a>Előfeltételek

A következő cikkekben ismertetett lépéseket kell elvégezni:
> [!div class="checklist"]
> * [Útmutató: alkalmazás létrehozása egyszerű parancsokkal](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Útmutató: paraméterek hozzáadása parancsokhoz](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="add-confirmations-to-a-command"></a>Megerősítések hozzáadása parancshoz

Megerősítés hozzáadásához használja a **SetTemperature** parancsot. A megerősítéshez a következő lépésekkel hozhat létre interakciós szabályokat.

1. Válassza ki a **SetTemperature** parancsot a bal oldali ablaktáblán.
1. A középső ablaktáblán a **Hozzáadás** gombra kattintva vegyen fel kapcsolatitevékenység-szabályokat. Ezután válassza a **kapcsolati szabályok**  >  **megerősítése parancsot**.

    Ez a művelet három interakciós szabályt ad hozzá, amely arra kéri a felhasználót, hogy erősítse meg a riasztás dátumát és időpontját, és megerősítse (igen/nem) a következő bekapcsoláshoz.

    1. Módosítsa a **parancs megerősítése** beavatkozási szabályt a következő konfiguráció szerint:
        1. Nevezze át a **nevet** a **hőmérséklet megerősítéséhez**.
        1. Adjon hozzá egy új feltételt a **kötelező paraméterek**  >  **hőmérsékletének**megfelelően.
        1. Új művelet hozzáadása a **Type**  >  **beszédfelismerési válasz**típusaként  >  **biztos, hogy a hőmérsékletet {hőmérséklet} fok értékre szeretné beállítani?**
        1. Az **elvárások** szakaszban hagyja meg az alapértelmezett érték **megérkezését a felhasználótól** .
      
         > [!div class="mx-imgBorder"]
         > ![Kötelező paraméter-válasz létrehozása](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Módosítsa a **megerősítő** sikeres interakciós szabályt a sikeres megerősítés kezelésére (a felhasználó azt mondta, hogy igen).
      
          1. A **név** **megerősítő hőmérsékletre**való módosítása sikeres volt.
          1. Hagyja, hogy a már meglévő **megerősítés sikeres** állapotú legyen.
          1. Adjon hozzá egy új feltételt **típus**  >  **kötelező paraméterek**  >  **hőmérsékletének**beállításához.
          1. Hagyja meg a **végrehajtás utáni állapot** alapértelmezett értékét **végrehajtási befejezési szabályokként**.

    1. Módosítsa a **megerősítő megtagadási** kapcsolati szabályt úgy, hogy kezelni tudja a megerősítés megtagadásakor jelentkező forgatókönyveket (a felhasználó nem).

          1. Módosítsa a **nevet** a **megerősítési hőmérséklet megtagadására**.
          1. Hagyja meg a már meglévő **megerősítés** feltételét.
          1. Adjon hozzá egy új feltételt **típus**  >  **kötelező paraméterek**  >  **hőmérsékletének**beállításához.
          1. Adjon hozzá egy új műveletet **típusként**, mert a  >  **beszédfelismerési válasz küldése**  >  **nem jelent problémát. Milyen hőmérsékletet?**
          1. A **felhasználó bemenetének várakozás**után hagyja meg a **végrehajtás utáni állapot** alapértelmezett értékét.

> [!IMPORTANT]
> Ebben a cikkben a beépített megerősítő funkciót használta. A kapcsolatitevékenység-szabályokat manuálisan is hozzáadhatja egyenként.
   

### <a name="try-out-the-changes"></a>Próbálja ki a módosításokat

Válassza a betanítás lehetőséget, várjon, amíg befejeződik a betanítás, majd válassza a **teszt** **lehetőséget.**

- **Bemenet**: hőmérséklet beállítása 80 fokos értékre
- **Kimenet**: biztos, hogy 80 fokos hőmérsékletet kíván beállítani?
- **Bemenet**: nem
- **Kimenet**: nincs probléma. Milyen hőmérsékletet?
- **Bemenet**: 72 fok
- **Kimenet**: biztos, hogy 72 fokos hőmérsékletet kíván beállítani?
- **Bemenet**: igen
- **Kimenet**: ok, hőmérséklet beállítása 83 fok


## <a name="implement-corrections-in-a-command"></a>Javítások implementálása egy parancsban

Ebben a szakaszban egy egylépéses korrekciót állít be, amelyet a rendszer a teljesítési művelet végrehajtása után használ. Azt is láthatja, hogyan engedélyezhető a javítás alapértelmezés szerint, ha a parancs még nem teljesül. Ha a parancs nem fejeződött be, adja hozzá az új **AlarmTone**paramétert a javításhoz.

Válassza ki a **SetAlarm** parancsot a bal oldali ablaktáblán, és adja hozzá az új **AlarmTone**paramétert.
        
- **Név**  >  **AlarmTone**
- **Típus**  >  **Karakterlánc**
- **Alapértelmezett érték**  >  **Harangjáték**
- **Konfiguráció**  >  **Előre megadott bemeneti értékek elfogadása a belső katalógusból**
- **Előre megadott bemeneti értékek**  >  **Harangjáték**, **Jingle**és **echo** egyéni előre definiált bemenetként


Ezután frissítse a **datetime** paraméterre adott választ úgy, hogy a **riasztást a (z) {AlarmTone} hangjelzéssel állítsa be. Milyen időpontra?** Ezután módosítsa a befejezési szabályt a következőképpen:

1. Válassza ki a meglévő befejezési szabályt **ConfirmationResponse**.
1. A jobb oldali ablaktáblán vigye a kurzort a meglévő művelet fölé, és válassza a **Szerkesztés**lehetőséget.
1. Frissítse a beszédfelismerési választ az OK értékre **, a riasztási készletet pedig a következőre: {DateTime}. Az ébresztési hang: {AlarmTone}.**

### <a name="try-out-the-changes"></a>Próbálja ki a módosításokat

Válassza a betanítás lehetőséget, várjon, amíg befejeződik a betanítás, majd válassza a **teszt** **lehetőséget.**
Próbálja ki a következő hosszúságú kimondott szöveg:

- **Bemenet**: riasztás beállítása.
- **Kimenet**: készen áll a riasztás hangjelzéssel való beállítására. Mennyi ideig?
- **Bemenet**: állítson be egy riasztást a csengőhangként a 9. holnapnál.
- **Kimenet**: ok, riasztás beállítva a 2020-05-30 09:00:00-hez. Az ébresztési hang a Jingle.

> [!IMPORTANT]
> Az ébresztési hang egy folyamatban lévő parancs explicit konfigurációja nélkül is módosítható, például ha a parancs még nem fejeződött be. *Alapértelmezés szerint a parancs összes paramétere esetében engedélyezve van a javítás, függetlenül attól, hogy a parancs még teljesítve van-e.*

### <a name="correction-when-command-is-completed"></a>Javítás a parancs befejezésekor

Az egyéni parancsok platform lehetővé teszi egy egylépéses javítás lehetőségét is, még akkor is, ha a parancs befejeződött. Ez a funkció alapértelmezés szerint nincs engedélyezve. Explicit módon kell konfigurálni. A következő lépésekkel konfigurálhat egy egylépéses javítást.

1. A **SetAlarm** parancsban vegyen fel egy interakciós szabályt az **előző verzió frissítése paranccsal** a korábban beállított riasztás frissítéséhez. Nevezze át a kapcsolatitevékenység-szabály alapértelmezett **nevét** az **előző riasztás frissítéséhez**.
1. Hagyja meg az alapértelmezett feltétel **előző parancsának frissítését** .
1. Adjon hozzá egy új feltételt **típus**  >  **kötelező paraméterének**  >  **datetime**értékként.
1. Vegyen fel egy új műveletet **típusként**a  >  **beszédfelismerési válasz**  >  **egyszerű szerkesztője**  >  **a korábbi riasztási idő frissítése a következőre: {DateTime}.**
1. A **végrehajtás utáni állapot** alapértelmezett értékének meghagyása a **parancs végrehajtása után**.

### <a name="try-out-the-changes"></a>Próbálja ki a módosításokat

Válassza a betanítás lehetőséget, várjon, amíg befejeződik a betanítás, majd válassza a **teszt** **lehetőséget.**

- **Bemenet**: riasztás beállítása.
- **Kimenet**: készen áll a riasztás hangjelzéssel való beállítására. Mennyi ideig?
- **Bemenet**: állítson be egy riasztást a csengőhangként a 9. holnapnál.
- **Kimenet**: ok, riasztás beállítva a 2020-05-21 09:00:00-hez. Az ébresztési hang a Jingle.
- **Bemenet**: nem, 8.
- **Kimenet**: az előző riasztási idő frissítése 2020-05-29 08:00-re.

> [!NOTE]
> Egy valós alkalmazásban a helyesbítő szabály **műveletek** szakaszában is el kell küldenie egy tevékenységet az ügyfélnek, vagy egy http-végpontot kell meghívnia, hogy frissítse a riasztási időt a rendszeren. Ennek a műveletnek kizárólag a riasztási idő frissítéséhez kell tartoznia, és nem a parancs egyéb attribútumait. Ebben az esetben ez az ébresztési hang lenne.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Útmutató: nyelvi létrehozási sablonok hozzáadása a beszédfelismerési válaszokhoz](./how-to-custom-commands-add-language-generation-templates.md)
