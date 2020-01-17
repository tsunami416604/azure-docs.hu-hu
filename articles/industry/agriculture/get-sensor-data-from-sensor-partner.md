---
title: Érzékelők adatainak beolvasása a partnerektől
description: Ez a cikk azt ismerteti, hogyan lehet beolvasni az érzékelők adatait a partnerektől.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 7c40cfc8fbc6a9957281be842f6714fa9289a3c8
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122219"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Érzékelők adatainak beolvasása az érzékelő partnereitől

Az Azure FarmBeats segítségével a IoT-eszközökről és-érzékelőkről átviheti a streamet a Datahub-be. Jelenleg a következő érzékelő-eszköz partnerek támogatottak.

  ![FarmBeats-partnerek](./media/get-sensor-data-from-sensor-partner/partner-information-1.png)

Az eszközök Azure FarmBeats-vel való integrálásával a farmon üzembe helyezett IoT-érzékelőkből származó adatokkal szerezheti be a datahub. A rendelkezésre álló, a FarmBeats-gyorsító használatával megjeleníthető adatelemek. Az adatok a FarmBeats használatával adatfúziós és gépi tanulási/mesterséges intelligencia (ML/AI) modellek létrehozásához használhatók.

Az érzékelő adatfolyamának elindításához a következőket kell biztosítani:

-  Telepítette a FarmBeats az Azure Marketplace-en.
-  Ön dönti el, hogy milyen érzékelőkkel és eszközökkel kívánja telepíteni a farmon.
-  Ha a talaj nedvességtartalmának érzékelőit kívánja használni, használja a FarmBeats-szennyeződési érzékelő elhelyezési térképét, hogy javaslatot tegyen az érzékelők számára, és hogy pontosan hol kell elhelyezni azokat. További információ: Maps- [Létrehozás](generate-maps-in-azure-farmbeats.md).
- A farmján megvásárolhatja és üzembe helyezheti eszközeit és érzékelőit. Győződjön meg arról, hogy az eszköz partnereinek megoldásán keresztül éri el az érzékelő adatait.

## <a name="enable-device-integration-with-farmbeats"></a>Eszköz-integráció engedélyezése a FarmBeats

Az érzékelők adatközvetítésének megkezdése után megkezdheti az adatgyűjtési folyamat beszerzését a FarmBeats-rendszeren. Adja meg a következő információkat az eszköz szolgáltatójának az FarmBeats való integráció engedélyezéséhez:

 - API-végpont
 - Bérlőazonosító
 - Ügyfél-azonosító
 - Titkos ügyfélkulcs
 - EventHub-kapcsolatok karakterlánca

Az előző adatokat a rendszerintegrátor kapja meg. Az eszközök integrációjának engedélyezésekor esetlegesen felmerülő problémákért forduljon a rendszerintegrátorhoz.

A hitelesítő adatokat úgy is létrehozhatja, hogy a parancsfájlt Azure Cloud Shellról futtatja. Kövesse az alábbi lépéseket.

1. Töltse le a [zip-fájlt](https://aka.ms/farmbeatspartnerscriptv2), és bontsa ki a helyi meghajtóra. Egy fájl lesz a zip-fájlban.
2. Jelentkezzen be https://portal.azure.com/, és lépjen a Azure Active Directory-> alkalmazás regisztrációi

3. Kattintson a FarmBeats-telepítés részeként létrehozott alkalmazás-regisztrációra. Ez a neve megegyezik a FarmBeats Datahub.

4. Kattintson az "API közzététele" elemre, > kattintson az "ügyfélalkalmazás hozzáadása" elemre, és írja be a **04b07795-8ddb-461a-bbee-02f9e1bf7b46** , és jelölje be az "engedélyezés hatóköre" lehetőséget. Ez hozzáférést biztosít az Azure CLI-hez (Cloud Shell) az alábbi lépések végrehajtásához.

5. Nyissa meg a Cloud Shellt. Ez a beállítás a Azure Portal jobb felső sarkában található eszköztáron érhető el.

    ![Azure Portal eszköztár](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

6. Győződjön meg arról, hogy a környezet **PowerShell**-re van beállítva. Alapértelmezés szerint a bash értékre van beállítva.

    ![PowerShell eszköztár-beállítás](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

7. Töltse fel a fájlt az 1. lépésben az Cloud Shell-példányban.

    ![Eszköztár feltöltése gomb](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

8. Nyissa meg azt a könyvtárat, ahová a fájl fel lett töltve. Alapértelmezés szerint a fájlok a Felhasználónév alatt töltődnek fel a saját könyvtárba.

9. Futtassa az alábbi parancsprogramot. A parancsfájl kéri a bérlő AZONOSÍTÓját, amely a Azure Active Directory-> áttekintő oldaláról kérhető le.

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

10. A képernyőn megjelenő utasításokat követve rögzítheti az **API-végpont**, a **bérlői azonosító**, az **ügyfél-azonosító**, az **ügyfél titkos kulcsa**és a **EventHub kapcsolódási karakterláncának**értékét.

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>Az eszközök adatainak integrálása a generált hitelesítő adatok használatával

A FarmBeats az előző szakaszban létrehozott hitelesítő adatokkal való összekapcsolásához nyissa meg az eszköz partner portálját:

 - API-végpont
 - EventHub-kapcsolatok karakterlánca
 - Ügyfél-azonosító
 - Titkos ügyfélkulcs
 - Bérlőazonosító

 Az eszköz szolgáltatója megerősíti a sikeres integrációt. A megerősítést követően megtekintheti az összes eszközt és érzékelőt az Azure FarmBeats.

## <a name="view-devices-and-sensors"></a>Eszközök és érzékelők megtekintése

A következő szakasz segítségével megtekintheti a farm eszközeit és érzékelőit.

### <a name="view-devices"></a>Eszközök megtekintése

A FarmBeats jelenleg a következő eszközöket támogatja:

- **Csomópont**: egy eszköz, amelyhez egy vagy több érzékelő csatlakozik.
- **Átjáró**: egy eszköz, amelyhez egy vagy több csomópont csatlakozik.

Kövesse az alábbi lépéseket.

1. A kezdőlapon válassza az **eszközök** lehetőséget a menüből.
  Az **eszközök** lap megjeleníti az eszköz típusát, a modellt, az állapotot, a behelyezett farmot és a metaadatok utolsó frissítésének dátumát. Alapértelmezés szerint a farm oszlop értéke *Null*. Dönthet úgy, hogy hozzárendel egy eszközt egy farmhoz. További információ: [eszközök kiosztása](#assign-devices).
2. Válassza ki az eszközt, amely az eszközhöz csatlakoztatott eszköz tulajdonságait, telemetria és alárendelt eszközeit szeretné megtekinteni.

    ![Eszközök lap](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Érzékelők megtekintése

Kövesse az alábbi lépéseket.

1. A kezdőlapon válassza a menü **érzékelők** elemét.
  Az **érzékelők** lap az érzékelő típusával, a hozzá kapcsolódó farmtal, a szülő eszközzel, a port nevével, a port típusával és az utolsó frissített állapottal kapcsolatos részleteket jeleníti meg.
2. Válassza ki az érzékelő tulajdonságait, az aktív riasztásokat és a telemetria az érzékelőből.

    ![Érzékelők lap](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Eszközök kiosztása  

Az érzékelők bevezetésének folyamata után hozzárendelheti azt a farmhoz, ahol az érzékelőket üzembe helyezte.

1. A kezdőlapon válassza a **farmok** lehetőséget a menüből. Megjelenik a **farmok** listája lap.
2. Válassza ki azt a farmot, amelyhez hozzá szeretné rendelni az eszközt, majd válassza az **eszközök hozzáadása**lehetőséget.
3. Megjelenik az **eszközök hozzáadása** ablak. Válassza ki azt az eszközt, amelyet hozzá szeretne rendelni a farmhoz.

    ![Eszközök hozzáadása ablak](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Válassza az **eszközök hozzáadása**lehetőséget. Másik lehetőségként lépjen az **eszközök** menüre, válassza ki a farmhoz hozzárendelni kívánt eszközöket, majd válassza az **eszközök társítása**lehetőséget.
5. Az **eszközök hozzárendelése** ablakban válassza ki a farm elemet a legördülő listából, és válassza az **összes alkalmazása** lehetőséget a farm összes kiválasztott eszközhöz való hozzárendeléséhez.

    ![Eszközök hozzárendelése ablak](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Ha az egyes eszközöket egy másik farmhoz szeretné társítani, válassza a **hozzárendelés farmhoz** oszlop legördülő nyilát, és válasszon ki egy farmot az egyes eszközök sorához.
7. Válassza a **hozzárendelés** lehetőséget az eszköz hozzárendelésének befejezéséhez.

### <a name="visualize-sensor-data"></a>Érzékelők adatmegjelenítése

Kövesse az alábbi lépéseket.

1. A Kezdőlap lapon válassza a **farmok** lehetőséget a menüből a **farmok** oldal megtekintéséhez.
2. Válassza ki azt a **farmot** , amelynek az érzékelőjét meg szeretné jeleníteni.
3. A **Farm** irányítópultján megtekintheti a telemetria-adatbázisokat. Megtekintheti az élő telemetria, vagy az **egyéni tartomány** használatával megtekintheti a kívánt dátumtartományt.

    ![Farm irányítópult](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>Érzékelő törlése

Kövesse az alábbi lépéseket.

1. A kezdőlapon válassza az **érzékelők** lehetőséget a menüből az **érzékelők** lap megtekintéséhez.
2. Válassza ki a törölni kívánt eszközt, és válassza a **Törlés** lehetőséget a megerősítő ablakban.

    ![Törlés gomb](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Egy megerősítő üzenet azt mutatja, hogy az érzékelő sikeresen törölve lett.

## <a name="delete-devices"></a>Eszközök törlése

Kövesse az alábbi lépéseket.

1. A kezdőlapon válassza az **eszközök** lehetőséget a menüben az **eszközök** lap megtekintéséhez.
2. Válassza ki a törölni kívánt eszközt, és válassza a **Törlés** lehetőséget a megerősítő ablakban.

    ![Törlés gomb](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Következő lépések

Most már rendelkezik érzékelővel az Azure FarmBeats-példányba. Most megismerheti, hogyan [hozhatja](generate-maps-in-azure-farmbeats.md#generate-maps) ki a térképeket a farmokhoz.
