---
title: Szenzoradatok beszedése a partnerektől
description: Ez a cikk bemutatja, hogyan szerezhetsz be érzékelőadatokat a partnerektől.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 113ab07af8ada16c0779da510c5f5b1f1f5a290b
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398233"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Érzékelőadatok beszereznie az érzékelőpartnerektől

Az Azure FarmBeats segítségével streamelési adatokat hozhat létre ioT-eszközeiről és érzékelőiről a Datahubba. Jelenleg a következő érzékelőeszköz-partnerek támogatottak.

  ![FarmBeats partnerek](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

Az eszközadatok Azure FarmBeats-rel való integrálása segít a farmban üzembe helyezett IoT-érzékelők földi adatainak be- és leküzdése az adatközpontba. Az adatok, amint elérhetővé válnak, a FarmBeats gyorsítón keresztül jeleníthetők meg. Az adatok felhasználhatók az adatok fúziós és gépi tanulás/mesterséges intelligencia (ML/AI) modell épület segítségével FarmBeats.

Az érzékelő adatfolyam-átvitelének elindításához győződjön meg a következőkről:

-  A FarmBeats szolgáltatást telepítette az Azure Marketplace-en.
-  Ön döntött az érzékelők és eszközök, hogy telepíteni szeretné a gazdaságban.
-  Ha talajnedvesség-érzékelőket szeretne használni, használja a FarmBeats talajnedvesség-érzékelő elhelyezési térképét, hogy ajánlást kapjon az érzékelők számáról és apontosról, hogy pontosan hova kell helyeznie őket. További információ: [Térképek létrehozása](generate-maps-in-azure-farmbeats.md).
- Eszközöket vagy érzékelőket vásárolhat és helyezhet üzembe eszközpartnerétől a farmon. Győződjön meg róla, hogy az eszközpartnerek megoldásán keresztül hozzáférhet az érzékelő adataihoz.

## <a name="enable-device-integration-with-farmbeats"></a>Eszközintegráció engedélyezése a FarmBeats alkalmazással

Az érzékelőadatok streamelésének megkezdése után megkezdheti az adatok FarmBeats rendszerbe való bejutásának folyamatát. Adja meg a következő adatokat az eszközszolgáltatónak a FarmBeats-integráció engedélyezéséhez:

 - API-végpont
 - Bérlőazonosító
 - Ügyfél-azonosító
 - Titkos ügyfélkulcs
 - EventHub-kapcsolati karakterlánc

A fenti információk létrehozásához kövesse az alábbi lépéseket:

> [!NOTE]
> Ezeket a lépéseket el kell végezni az Azure-ban az Azure-előfizetés eléréséhez, ahol a FarmBeats telepítve van.

1. Jelentkezzen be itt: https://portal.azure.com/.

2. **Ha a FarmBeats 1.2.7-es vagy újabb verzióját használja, hagyja ki az a, b és c lépéseket, és folytassa a 3.** A FarmBeats verziót a FarmBeats felhasználói felületének jobb felső sarkában található **Beállítások** ikonra kattintva ellenőrizheti.

      a.  Látogasson el az **Azure Active Directory** > **alkalmazásregisztrációira**

      b. Válassza ki a FarmBeats központi telepítésének részeként létrehozott **alkalmazásregisztrációt.** Ugyanaz lesz a neve, mint a FarmBeats datahub.

      c. Válassza **az API->** válassza **az Ügyfélalkalmazás hozzáadása** lehetőséget, és írja be a **04b07795-8ddb-461a-bbee-02f9e1bf7b46 parancsot,** és jelölje be a Hatókör engedélyezése **jelölőnégyzetet.** Ez hozzáférést biztosít az Azure CLI -hez (Cloud Shell) az alábbi lépések végrehajtásához:

3. Nyissa meg a Cloud Shellt. Ez a beállítás az Azure Portal jobb felső sarkában található eszköztáron érhető el.

    ![Az Azure Portal eszköztára](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. Győződjön meg arról, hogy a környezet **PowerShell-re**van állítva. Alapértelmezés szerint Bash értékre van állítva.

    ![A PowerShell eszköztárának beállítása](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. Nyissa meg a kezdőkönyvtárat.

    ```azurepowershell-interactive 
    cd  
    ```

6. Futtassa a következő parancsot. Ezzel parancsfájlt tölt le a kezdőkönyvtárba.

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

7. Futtassa az alábbi parancsprogramot. A parancsfájl kéri a bérlői azonosítót, amely beszerezhető az **Azure Active Directory** > **áttekintése** lapon.

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

8. A képernyőn megjelenő utasításokat követve rögzítse az **API-végpont,** **a bérlőazonosító,** **az ügyfélazonosító**, **az ügyféltitkos kulcs**és az **EventHub-kapcsolati karakterlánc értékeit.**

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>Eszközadatok integrálása a létrehozott hitelesítő adatok használatával

Most már a következő információkat generált az előző szakaszban.
 - API-végpont
 - EventHub-kapcsolati karakterlánc
 - Ügyfél-azonosító
 - Titkos ügyfélkulcs
 - Bérlőazonosító

Ezt meg kell adnia az eszközpartnerének a FarmBeats összekapcsolásához. Ugyanezt az eszközpartneri portálon keresztül. Például abban az esetben, ha davis i hangszereken használ, a Teralytic vagy a Pessl Instruments (Metos.at) az alábbi megfelelő oldalakra lép:

1. [Davis Instruments](https://weatherlink.github.io/azure-farmbeats/setup)

2. [Teralitikus](https://app.teralytic.com/)

3. [Pessl Instruments](https://ng.fieldclimate.com/user-api-services)

Az eszközszolgáltató megerősíti a sikeres integrációt. A megerősítést követően megtekintheti az Azure FarmBeats összes eszközét és érzékelőjét.

## <a name="view-devices-and-sensors"></a>Eszközök és érzékelők megtekintése

A következő szakaszban megtekintheti a farm eszközeit és érzékelőit.

### <a name="view-devices"></a>Eszközök megtekintése

A FarmBeats jelenleg a következő eszközöket támogatja:

- **Csomópont**: Olyan eszköz, amelyhez egy vagy több érzékelő csatlakozik.
- **Átjáró**: Olyan eszköz, amelyhez egy vagy több csomópont csatlakozik.

Kövesse az alábbi lépéseket:

1. A kezdőlapon válassza a menü **Eszközök** elemét.
  Az **Eszközök** lap megjeleníti az eszköz típusát, típusát, állapotát, azt a farmot, amelybe bekerül, valamint a metaadatok utolsó frissített dátumát. Alapértelmezés szerint a farm oszlopértéke *NULL*. Választhat, hogy egy eszközt egy farmhoz rendel. További információt az [Eszközök hozzárendelése című](#assign-devices)témakörben talál.
2. Válassza ki az eszközt az eszköz tulajdonságainak, telemetriai és gyermekeszközeinek megtekintéséhez.

    ![Eszközök lap](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Érzékelők megtekintése

Kövesse az alábbi lépéseket:

1. A kezdőlapon válassza a menü **Érzékelők** elemét.
  Az **Érzékelők** lap részletesen ismerteti az érzékelő típusát, a farmot, amelyhez csatlakozik, a szülőeszközre, a port nevére, a port típusára és a legutóbbi frissített állapotra.
2. Válassza ki az érzékelőt az érzékelő tulajdonságainak, az aktív riasztásoknak és az érzékelőttt.

    ![Érzékelők oldal](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Eszközök hozzárendelése  

Miután az érzékelő adatai beáramlanak, hozzárendelheti azt ahhoz a farmhoz, ahol az érzékelőket telepítette.

1. A kezdőlapon válassza a menü **Farmok** elemét. Megjelenik **a Farmok** listaoldala.
2. Jelölje ki azt a farmot, amelyhez hozzá szeretné rendelni az eszközt, és válassza az **Eszközök hozzáadása**lehetőséget.
3. **Megjelenik az Eszközök hozzáadása** ablak. Jelölje ki a farmhoz rendelni kívánt eszközt.

    ![Eszközök hozzáadása ablak](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Válassza **az Eszközök hozzáadása**lehetőséget. Másik lehetőségként lépjen az **Eszközök** menübe, jelölje ki a farmhoz rendelni kívánt eszközöket, és válassza **az Eszközök társítása**lehetőséget.
5. Az **Eszközök társítása** ablakban válassza ki a farmot a legördülő listából, és válassza az **Alkalmazás az összesre** lehetőséget, ha a farmot az összes kijelölt eszközhöz szeretné társítani.

    ![Eszközök társítása ablak](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Ha az egyes eszközöket egy másik farmhoz szeretné társítani, jelölje ki a **Farmhoz hozzárendelés** oszlopban lévő legördülő nyilat, és jelöljön ki egy farmot az egyes eszközsorokhoz.

7. Az eszközhozzárendelés befejezéséhez válassza a **Hozzárendelés** lehetőséget.

### <a name="visualize-sensor-data"></a>Érzékelőadatok megjelenítése

Kövesse az alábbi lépéseket:

1. A kezdőlapon válassza a **Farmok** parancs menüelem parancsát a **Farmok** lap megtekintéséhez.
2. Válassza ki azt a **farmot,** amelynek az érzékelő adatait meg szeretné tekinteni.
3. A **Farm** irányítópulton megtekintheti a telemetriai adatokat. Megtekintheti az élő telemetriai adatokat, vagy **az Egyéni tartomány** használatával megtekintheti egy adott dátumtartományt.

    ![Farm irányítópultja](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>Érzékelő törlése

Kövesse az alábbi lépéseket:

1. A kezdőlapon válassza az **Érzékelők** lehetőséget a menüből az **Érzékelők** oldal megtekintéséhez.
2. Jelölje ki a törölni kívánt eszközt, és a megerősítést kérő ablakban válassza a **Törlés** lehetőséget.

    ![Törlés gomb](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Egy megerősítő üzenet jelzi, hogy az érzékelő sikeresen törlődött.

## <a name="delete-devices"></a>Eszközök törlése

Kövesse az alábbi lépéseket:

1. A kezdőlapon válassza a menü **Eszközök parancsát** az **Eszközök** lap megtekintéséhez.
2. Jelölje ki a törölni kívánt eszközt, és a megerősítést kérő ablakban válassza a **Törlés** lehetőséget.

    ![Törlés gomb](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>További lépések

Most már az Azure FarmBeats-példányba áramló érzékelőadatokkal rendelkezik. Most, megtanulják, hogyan kell [létrehozni térképeket](generate-maps-in-azure-farmbeats.md#generate-maps) a gazdaságok.
