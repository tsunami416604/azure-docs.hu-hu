---
title: Érzékelők adatainak beolvasása a partnerektől
description: Ismerteti, hogyan lehet beolvasni az érzékelők adatait a partnerektől
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 424df8ac7145129dd70a9a5791648730189b528b
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889656"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Érzékelők adatainak beolvasása az érzékelő partnereitől

Az Azure FarmBeats segítségével a IoT-eszközökről és-érzékelőkről az adatközpontba helyezheti át a folyamatos adatátvitelt. Jelenleg a következő szenzor-eszköz partnerek támogatottak:

  ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/partner-information-1.png)

Az eszköz adatainak az Azure FarmBeats való integrálásával a farmon üzembe helyezett IoT-érzékelőkből az adatközpontba helyezheti a terepi adatait. A FarmBeats-gyorsító segítségével a rendelkezésre álló adatgyűjtési és AI/ML-modellek FarmBeats használatával is megjeleníthetők.

Az érzékelő adatfolyamának elindításához a következőket kell biztosítani:

-  Telepítette a FarmBeats-t az Azure Marketplace-ről.
-  Ön úgy döntött, hogy a farmján telepíteni kívánt érzékelők és eszközök.
-  Ha a talaj nedvességtartalmának érzékelők használatát tervezi, használhatja a FarmBeats szennyeződés-érzékelő elhelyezési térképét, hogy javaslatot kapjon az érzékelők számáról, és pontosan hol kell elhelyezni az érzékelőket. További információ: Maps- [Létrehozás](generate-maps.md).

- Eszköz/érzékelők vásárlása és üzembe helyezése a farmján lévő eszköz-partnertől. Győződjön meg arról, hogy az eszköz partnereinek megoldásán keresztül éri el az érzékelő adatait.

### <a name="enable-device-integration-with-farmbeats"></a>Eszköz-integráció engedélyezése a FarmBeats   

Miután elindította az érzékelők adatátvitelét, elindíthatja az adatgyűjtési folyamatot a FarmBeats-rendszeren. A következő információkat kell megadnia az eszköz szolgáltatójának az FarmBeats való integráció engedélyezéséhez:  

 - API-végpont  
 - Bérlőazonosító  
 - Ügyfél-azonosító  
 - Ügyfél titka  
 - EventHub-kapcsolatok karakterlánca

A fenti információkat a rendszerintegrátor adja meg. Az eszközök integrációjának engedélyezése során felmerülő problémákért forduljon a rendszerintegrátorhoz.

A hitelesítő adatokat úgy is létrehozhatja, hogy a parancsfájlt a Azure Cloud Shellból futtatja. Kövesse az alábbi lépéseket:

1. Töltse le a [zip-fájlt](https://aka.ms/farmbeatspartnerscript) , és bontsa ki a helyi meghajtóra. A ZIP-fájlban két fájl található.
2. Jelentkezzen be https://portal.azure.com/ és nyissa meg a Cloud Shell (ez a lehetőség a portál jobb felső sarkában érhető el)  

    ![A Project Farm veri](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Győződjön meg arról, hogy a környezet beállítása **PowerShell** – alapértelmezés szerint a bash értékre van állítva.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

4. Töltse fel a két fájlt (a fenti 1. lépésből) a Cloud Shell.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

5. Nyissa meg azt a könyvtárat, ahová a fájlok fel lettek töltve (alapértelmezés szerint a rendszer feltölti a > felhasználónévre).
6. Futtassa a következő parancsfájlt:

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```
7. A képernyőn megjelenő utasításokat követve rögzítheti az értékeket. (API-végpont, bérlő azonosítója, ügyfél-azonosító, ügyfél titka és EventHub-kapcsolatok karakterlánca).

**Az eszközök adatainak integrálása a generált hitelesítő adatok használatával**

A FarmBeats az előző szakaszban létrehozott hitelesítő adatokkal való összekapcsolásához látogasson el az eszköz partner portálra.

 - API-végpont  
 - EventHub-kapcsolatok karakterlánca  
 - Ügyfél-azonosító  
 - Ügyfél titka  
 - Bérlőazonosító  

 Az eszköz szolgáltatója megerősíti a sikeres integrációt. A megerősítést követően megtekintheti az összes eszközt és érzékelőt az Azure FarmBeats.

## <a name="view-devices-and-sensors"></a>Eszközök és érzékelők megtekintése

A következő szakasz a farmban található eszközök és érzékelők megtekintésére használható.

### <a name="view-devices"></a>Eszközök megtekintése

A FarmBeats jelenleg a következő eszközöket támogatja:

- **Csomópont**: egy eszköz, amelyhez egy vagy több érzékelő csatlakozik.
- **Átjáró**: egy eszköz, amelyhez egy vagy több érzékelő csatlakozik.

Ehhez a következő lépések szükségesek:

1. A kezdőlapon válassza az **eszközök** lehetőséget a menüből.
  Az eszközök lap megjeleníti az eszköz típusát, a modellt, az állapotot, a behelyezett farmot és a metaadatok utolsó frissítésének dátumát. Alapértelmezés szerint a farm oszlop értéke NULL. Dönthet úgy, hogy hozzárendel egy eszközt egy farmhoz. További információ: [eszközök kiosztása](#assign-devices).
2. Válassza ki az eszközt, amely az eszközhöz csatlakoztatott eszköz tulajdonságait, telemetria és alárendelt eszközeit szeretné megtekinteni.  

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Érzékelők megtekintése

Ehhez a következő lépések szükségesek:

1. A kezdőlapon válassza a menü **érzékelők** elemét.
  Az érzékelők lap az érzékelő típusával, a hozzá kapcsolódó farmtal, a fölérendelt eszközzel, a port nevével, a port típusával és a legutóbbi frissített állapottal kapcsolatos részleteket jeleníti meg.
2. Válassza ki az érzékelő tulajdonságait, az aktív riasztásokat és a telemetria az érzékelőből.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Eszközök kiosztása  

Miután megtörtént az érzékelők adatáramlása, hozzárendelheti azt a farmhoz, amelyben üzembe helyezte az érzékelőket.

1. A kezdőlapon válassza a **farmok** lehetőséget a menüből, a **farmok** listája lap jelenik meg.  
2. Válassza ki azt a farmot, amelyhez hozzá szeretné rendelni az eszközt, majd válassza az **eszközök hozzáadása**lehetőséget.  
3. Megjelenik az **eszközök hozzáadása** ablak. Válassza ki azt az eszközt, amelyet hozzá szeretne rendelni a farmhoz.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Válassza az **eszközök hozzáadása**lehetőséget. Másik lehetőségként lépjen az **eszközök** menüre, válassza ki a farmhoz hozzárendelni kívánt eszközöket, majd válassza az **eszközök társítása**lehetőséget.  
5. Az **eszközök hozzárendelése** ablakban válassza ki a farmot a legördülő listából, és válassza az **összes alkalmazása** lehetőséget a farm összes kiválasztott eszközhöz való hozzárendeléséhez.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Ha az egyes eszközöket egy másik farmhoz szeretné társítani, válassza a **hozzárendelés farmhoz** oszlop legördülő listát, és válassza ki az egyes eszközök sorához tartozó farmokat.  
7. Válassza a **hozzárendelés** lehetőséget az eszköz hozzárendelésének befejezéséhez.

### <a name="visualize-sensor-data"></a>Érzékelők adatmegjelenítése

Ehhez a következő lépések szükségesek:

1. A Kezdőlap lapon válassza a **farmok** lehetőséget a menüből a **farmok** oldal megtekintéséhez.  
2. Válassza ki azt a **farmot** , amelynek az érzékelőjét meg szeretné jeleníteni.  
3. A **Farm** irányítópultján megtekintheti a telemetria-adatbázisokat. Dönthet úgy, hogy élő telemetria jelenít meg, vagy **Egyéni tartományt** használ egy adott dátumtartomány megtekintéséhez.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-sensor"></a>Érzékelő törlése

Kövesse az alábbi lépéseket:

1. A kezdőlapon válassza az **érzékelők** lehetőséget a menüből az **érzékelők** lap megtekintéséhez.  
2. Válassza ki a törölni kívánt eszközt, és válassza a **Törlés** a megerősítési ablakban lehetőséget.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Egy megerősítő üzenet azt mutatja, hogy az érzékelő sikeresen törölve lett.  

## <a name="delete-devices"></a>Eszközök törlése

Kövesse az alábbi lépéseket:

1. A kezdőlapon válassza az **eszközök** lehetőséget a menüben az eszközök lap megtekintéséhez.  
2. Válassza ki a törölni kívánt eszközt, és válassza a **Törlés** lehetőséget a megerősítő ablakban.

    ![A Project Farm veri](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>További lépések

Most már rendelkezik érzékelővel az Azure FarmBeats-példányba. Most megismerheti, hogyan [hozhatja](generate-maps.md#generate-maps) ki a térképeket a farmokhoz.
