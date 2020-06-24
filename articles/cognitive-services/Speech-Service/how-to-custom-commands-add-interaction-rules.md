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
ms.openlocfilehash: 5f66e29e4c1bc85981202251e0de8288f4baee4e
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307842"
---
# <a name="add-interaction-rules"></a>Kapcsolatitevékenység-szabályok hozzáadása

Ebben a cikkben az **interakciós szabályokról**olvashat. Ezek a további szabályok az összetettebb vagy összetett helyzetek kezelésére szolgálnak. Habár ingyenes a saját egyéni interakciós szabályainak megalkotása, ebben a cikkben a következő célcsoportokra vonatkozó interakciós szabályokat használhatja:

* Parancsok megerősítése
* Egy lépésből álló javítás hozzáadása a parancsokhoz

A kapcsolati szabályokkal kapcsolatos további információkért tekintse meg a [hivatkozások](./custom-commands-references.md) szakaszt.

## <a name="prerequisites"></a>Előfeltételek

A következő cikkekben ismertetett lépéseket kell végrehajtania:
> [!div class="checklist"]
> * [Útmutató: alkalmazás létrehozása egyszerű parancsokkal](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Útmutató: paraméterek hozzáadása parancsokhoz](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="add-confirmations-to-a-command"></a>Megerősítések hozzáadása parancshoz

Megerősítés hozzáadásához használja a **SetTemperature** parancsot. A megerősítés érdekében a következő lépésekkel hozhat létre interakciós szabályokat.

1. Válassza a **SetTemperature** parancsot a bal oldali ablaktáblán.
2. A középső ablaktáblán a **Hozzáadás** elem kiválasztásával, majd a **Kapcsolatitevékenység-szabályok**  >  **megerősítése parancs**kiválasztásával kapcsolatitevékenység-szabályokat adhat hozzá.

    Ez 3 interakciós szabályt ad hozzá, ez a szabály kéri a felhasználót, hogy erősítse meg a riasztás dátumát és időpontját, és egy megerősítést (igen/nem) vár a következő bekapcsoláshoz.

    1. Módosítsa a **parancs megerősítése** interakciós szabályt a következő konfiguráció szerint
        1. Nevezze át a **nevet** a következőre: **`Confirm Temperature`** .
        1. Új feltétel hozzáadása **kötelező paraméterek > hőmérséklet**
        1. Új művelet típusként való hozzáadása **> a beszédfelismerési válasz küldése `Are you sure you want to set the temperature as {Temperature} degrees?` >**
        1. Az elvárások szakaszban hagyja meg az alapértelmezett érték **megérkezését a felhasználótól** .
      
         > [!div class="mx-imgBorder"]
         > ![Kötelező paraméter-válasz létrehozása](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Módosítsa a **megerősítő** sikeres interakciós szabályt a sikeres megerősítés kezelésére (a felhasználó azt mondta, hogy igen).
      
          1. Módosítsa a **nevet** a következőre: **`Confirmation temperature succeeded`** .
          1. Hagyja, hogy a már meglévő **megerősítés sikeres** állapotú legyen.
          1. Új feltétel típusként való hozzáadása **> kötelező paraméterek > hőmérséklet**
          1. Hagyja meg a **végrehajtás utáni állapot** alapértelmezett értékét **végrehajtási befejezési szabályokként**.

    1. Módosítsa a **megtagadást** (azt mondta, hogy a felhasználó nem) olyan forgatókönyvek kezelésére, amikor a megerősítés megtagadva.

          1. Módosítsa a **nevet** a következőre: **`Confirmation temperature denied`** .
          1. Hagyja meg a már meglévő **megerősítés** feltételét.
          1. Új feltétel típusként való hozzáadása **> kötelező paraméterek > hőmérséklet**
          1. Új művelet típusként való hozzáadása **> a beszédfelismerési válasz küldése `No problem. What temperature then?` >**
          1. A **felhasználó bemenetének várakozás**után hagyja meg a **végrehajtás utáni állapot** alapértelmezett értékét.

> [!IMPORTANT]
> Ebben a cikkben a beépített megerősítő funkciót használta. Azt is megteheti, hogy az interakciós szabályokat egyenként, manuálisan adja hozzá.
   

### <a name="try-out-the-changes"></a>Próbálja ki a módosításokat

Válassza a **vonat**lehetőséget, várjon, amíg befejeződik a képzés, és válassza a **teszt**lehetőséget.

- Bemenet: hőmérséklet beállítása 80 fokos értékre
- Kimenet: ok 80?
- Bemenet: nem
- Kimenet: nincs probléma. milyen hőmérsékletet?
- Bemenet: 83 fok
- Kimenet: ok 83?
- Bemenet: igen
- Kimenet: ok, hőmérséklet beállítása 83 fok


## <a name="implementing-corrections-in-a-command"></a>Javítások végrehajtása egy parancsban

Ebben a szakaszban egy lépésenkénti korrekciót konfigurálunk, amelyet a rendszer a teljesítési művelet végrehajtása után használ. Azt is láthatja, hogy a javítás alapértelmezés szerint engedélyezve van abban az esetben, ha a parancs még nem teljesül. Ha a parancs nem fejeződött be, adjon hozzá egy új **AlarmTone**paramétert.

Válassza a **SetAlarm** parancsot a bal oldali ablaktáblán, és adjon hozzá egy új **AlarmTone**paramétert.
        
- **név** > `AlarmTone`
- **Írja be** a > karakterláncot
- **Alapértelmezett érték** > `Chimes`
- A **konfiguráció** > fogad előre megadott bemeneti értékeket a belső katalógusból
- **Előre meghatározott bemeneti értékek**:  >  `Chimes` `Jingle` , és `Echo` . Mindegyik egyedi előre megadott bemenet.


Ezután frissítse a DateTime paraméterre adott választ a következőre: `Ready to set alarm with tone as {AlarmTone}. For what time?` . Ezután módosítsa a befejezési szabályt az alábbiak szerint.

1. Válassza ki a meglévő befejezési szabályt **ConfirmationResponse**.
1. A jobb oldali panelen vigye a kurzort a meglévő művelet fölé, és válassza a **Szerkesztés** gombot.
1. Beszédre adott válasz frissítése`Ok, alarm set for {DateTime}. The alarm tone is {AlarmTone}.`

### <a name="try-out-the-changes"></a>Próbálja ki a módosításokat

Válassza ki a **betanítás elemet, várjon**, amíg befejeződik a képzés, és válassza a **teszt**lehetőséget.
Próbálja ki a következő hosszúságú kimondott szöveg:

- Bemenet: riasztás beállítása
- Kimenet: készen áll a riasztás hangjelzéssel való beállítására. Mennyi ideig?
- Bemenet: ébresztés beállítása a csengőhangként a 9-es holnapnál
- Kimenet: ok, riasztás beállítva a 2020-05-30 09:00:00-hez. Az ébresztési hang a Jingle.

> [!IMPORTANT]
> Figyelje meg, hogy az ébresztési hang egy folyamatban lévő parancs explicit konfigurációja nélkül is módosítható, azaz ha a parancs még nem fejeződött be. **A javítás alapértelmezés szerint engedélyezve van az összes parancs paraméteréhez, függetlenül attól, hogy a parancs még teljesítve van-e.**

### <a name="correction-when-command-is-completed"></a>Javítás a parancs befejezésekor

Az egyéni parancsok platform az egylépéses javítás lehetőségét is biztosítja, még akkor is, ha a parancs befejeződött. Ez a funkció azonban alapértelmezés szerint nincs engedélyezve, és explicit módon kell konfigurálni. Az alábbi lépésekkel konfigurálhatja az egylépéses javítást.

1. A **SetAlarm** parancsban vegyen fel egy **korábbi frissítési művelet** típusú interakciós szabályt a korábban beállított riasztás frissítéséhez. Nevezze át a kapcsolatitevékenység-szabály alapértelmezett **nevét** az **előző riasztás frissítéséhez**.
1. Hagyja meg az alapértelmezett feltétel **előző parancsának frissítését** .
1.  Adjon hozzá egy új feltételt **Type > Required paraméter > datetime**.
1. Vegyen fel egy új műveletet **típusként > a beszédfelismerési válasz küldése `Updating previous alarm time to {DateTime}.` > egyszerű szerkesztő >**
1. A végrehajtás utáni állapot alapértelmezett értékének meghagyása a **parancs befejezésekor**.

### <a name="try-out-the-changes"></a>Próbálja ki a módosításokat

Válassza a **vonat**lehetőséget, várjon, amíg befejeződik a képzés, és válassza a **teszt**lehetőséget.

- Bemenet: riasztás beállítása
- Kimenet: készen áll a riasztás hangjelzéssel való beállítására. mikor?
- Bemenet: ébresztés beállítása a csengőhangként a 9-es holnapnál
- Kimenet: ok, riasztás beállítva a 2020-05-21 09:00:00-hez. Az ébresztési hang a Jingle.
- Bemenet: nem, 8
- Kimenet: az előző riasztási idő frissítése 2020-05-29 08:00-re.

> [!NOTE]
> Egy valós alkalmazásban a helyesbítő szabály műveletek szakaszában is el kell küldenie egy tevékenységet az ügyfélnek, vagy egy HTTP-végpontot kell meghívnia, hogy frissítse a riasztási időt a rendszeren. Ez a művelet csak a riasztási idő frissítése és a parancs egyéb attribútuma (ebben az esetben az ébresztési hang) esetében lehet külön felelős.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Útmutató: nyelvi létrehozási sablonok hozzáadása a beszédfelismerési válaszokhoz](./how-to-custom-commands-add-language-generation-templates.md)