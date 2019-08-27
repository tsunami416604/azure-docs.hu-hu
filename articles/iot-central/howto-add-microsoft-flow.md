---
title: Munkafolyamatok létrehozása az Azure IoT Central-összekötővel Microsoft Flowban | Microsoft Docs
description: A munkafolyamatok elindításához és a munkafolyamatokban való futtatásához használja a Microsoft Flow IoT Central-összekötőjét.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 08/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: hegate
ms.openlocfilehash: a972ab52f64a37ac6876194202324b4380460817
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050572"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Munkafolyamatok létrehozása a IoT Central-összekötővel Microsoft Flow

*Ez a témakör az építők és a rendszergazdákra vonatkozik.*

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

A Microsoft Flow segítségével automatizálhatja a munkafolyamatokat az üzleti felhasználók által használt számos alkalmazás és szolgáltatás között. A Microsoft Flow IoT Central-összekötőjét használva elindíthatja a munkafolyamatokat, amikor egy szabály aktiválódik a IoT Central. IoT Central vagy bármely más alkalmazás által aktivált munkafolyamatban a IoT Central-összekötő műveletei a következőre használhatók:
- Eszköz létrehozása
- Eszköz adatainak beolvasása
- Eszköz tulajdonságainak és beállításainak frissítése
- Parancs futtatása eszközön
- Eszköz törlése

Tekintse meg [ezeket a Microsoft flow sablonokat](https://aka.ms/iotcentralflowtemplates) , amelyek más szolgáltatásokhoz, például a mobil értesítésekhez és a Microsoft csapatokhoz kapcsolódnak IoT Central.

## <a name="prerequisites"></a>Előfeltételek

- A Pay-As-You-Go application
- A Microsoft személyes vagy munkahelyi vagy iskolai fiókja Microsoft Flow használatára (további[információ a Microsoft flow csomagokról](https://aka.ms/microsoftflowplans))
- Munkahelyi vagy iskolai fiók az Azure IoT Central-összekötő használatához

## <a name="trigger-a-workflow"></a>Munkafolyamat elindítása

Ebből a szakaszból megtudhatja, hogyan indíthat el mobil értesítéseket a flow Mobile alkalmazásban, ha egy szabály IoT Central. Ezt a teljes munkafolyamatot a beágyazott Microsoft Flow Designer használatával is létrehozhatja a IoT Central alkalmazásban.

1. Első lépésként [hozzon létre egy szabályt a IoT Central](howto-create-telemetry-rules.md). A szabály feltételeinek mentése után válassza ki a **Microsoft flow műveletet** új műveletként. Megnyílik egy párbeszédpanel a munkafolyamat konfigurálásához. A rendszer a bejelentkezett IoT Central felhasználói fiókot használja a Microsoft Flowba való bejelentkezéshez.

    ![Új Microsoft Flow művelet létrehozása](media/howto-add-microsoft-flow/createflowaction.png)

1. Ekkor megjelenik azoknak a munkafolyamatoknak a listája, amelyekhez hozzá van rendelve, és csatolva van ehhez a IoT Central szabályhoz. Kattintson a **sablonok tallózása** vagy az **új > Létrehozás sablonból** lehetőségre, és bármelyik elérhető sablon közül választhat. 

    ![Elérhető Microsoft Flow sablonok](media/howto-add-microsoft-flow/flowtemplates1.png)

1. A rendszer kérni fogja, hogy jelentkezzen be az összekötők között a kiválasztott sablonba. 

    > [!NOTE]
    > Az Azure IoT Central-összekötő használatához be kell jelentkeznie egy Azure Active Directory-fiókkal (munkahelyi vagy iskolai fiókkal). Az Azure IoT Central abc@outlook.com - abc@live.com összekötő nem támogatja a személyes fiókot.

    Miután bejelentkezett az összekötők között, a Designerben fog megjelenni a munkafolyamat összeállításához. A munkafolyamat olyan IoT Central-triggerrel rendelkezik, amely már kitöltötte az alkalmazást és a szabályt.

1. A munkafolyamat testreszabható úgy, hogy testreszabja a műveletnek átadott adatokat, és új műveleteket ad hozzá. Ebben a példában a művelet **értesítések – mobil értesítés küldése**. A *dinamikus tartalmat* a IoT Central szabályból is felhasználhatja, így a fontos információk, például az eszköz neve és az időbélyege is áthalad az értesítésre.

    > [!NOTE]
    > Jelölje be a **további** szöveg megjelenítése a dinamikus tartalom ablakban a szabályt kiváltó mérési és tulajdonságértékek beszerzéséhez.

    ![Folyamat-szerkesztési művelet dinamikus ablaktáblával megnyitva](./media/howto-add-microsoft-flow/flowdynamicpane1.png)

1. Ha elkészült a művelet szerkesztésével, válassza a **Mentés**lehetőséget. A munkafolyamat áttekintés lapjára lesz irányítva. Itt láthatja a futtatási előzményeket, és megoszthatja azokat más munkatársakkal.

    > [!NOTE]
    > Ha azt szeretné, hogy a IoT Central alkalmazásban lévő más felhasználók is szerkesszék ezt a szabályt, akkor azt Microsoft Flow kell megosztania velük. Adja hozzá a Microsoft Flow-fiókjait tulajdonosként a munkafolyamatban.

1. Ha visszatér a IoT Central alkalmazáshoz, akkor ez a szabály Microsoft Flow műveletet fog látni a műveletek területen.

Az IoT Central-összekötő használatával is létrehozhat munkafolyamatokat közvetlenül a Microsoft Flowból. Ezután kiválaszthatja, hogy melyik IoT Central alkalmazáshoz kapcsolódjon.

## <a name="create-a-device-in-a-workflow"></a>Eszköz létrehozása munkafolyamatban

Ebből a szakaszból megtudhatja, hogyan hozhat létre egy új eszközt a IoT Central egy mobileszközön lévő gomb leküldésekor a Microsoft Flow Mobile App használatával. Ezzel a művelettel a flow-ban olyan ERP-rendszereket integrálhat, mint például a IoT Central új eszköz létrehozásával, ha az eszközt máshová helyezik.

1. Először hozzon létre egy üres munkafolyamatot a Microsoft Flow.

1. A **mobil folyamat gombjának** keresése triggerként.

1. Ebben az triggerben adjon hozzá egy **eszköznév**nevű szövegbeviteli bemenetet. Módosítsa a Leírás szövegét, hogy **az új eszköz nevét adja meg**.

1. Adjon hozzá egy új műveletet. Keresse meg az **Azure IoT Central – eszköz létrehozása** műveletet.

1. Válassza ki az alkalmazást, és válasszon ki egy eszközt a legördülő listából egy eszköz létrehozásához. Ekkor megjelenik a művelet kibontása az eszköz összes tulajdonságának és beállításának megjelenítéséhez.

1. Válassza ki az eszköz neve mezőt. A dinamikus tartalom ablaktáblán válassza az **eszköznév**lehetőséget. Ezt az értéket adja meg a felhasználó által a Mobile alkalmazásban megadott bemenettől, és az új eszköz neve IoT Centralban. Ebben a példában az egyetlen kötelező mező az eszköz neve, amelyet a piros csillag jelöl. Egy másik eszköz sablonja több kötelező mezőt is tartalmazhat, amelyeket ki kell töltenie egy új eszköz létrehozásához.

    ![Folyamat létrehozása művelet dinamikus panelje](./media/howto-add-microsoft-flow/flowcreatedevice1.png)

1. Választható Töltse ki a többi mezőt úgy, hogy az új eszközök létrehozásához illeszkedjen.

1. Végül mentse a munkafolyamatot.

1. Próbálja ki a munkafolyamatot a Microsoft Flow Mobile alkalmazásban. Nyissa meg az alkalmazás **gombok** lapját. Ekkor megjelenik a gomb – > hozzon létre egy új eszköz-munkafolyamatot. Adja meg az új eszköz nevét, és nézze meg, hogy megjelenik-e a IoT Central!

    ![Folyamat létrehozása – mobileszköz-létrehozási képernyőkép](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>Eszköz frissítése egy munkafolyamatban

Ebből a szakaszból megtudhatja, hogyan frissítheti IoT Central eszköz beállításait és tulajdonságait egy mobileszköz gombjának leküldésekor a Microsoft Flow Mobile App használatával.

1. Először hozzon létre egy üres munkafolyamatot a Microsoft Flow.

1. A **mobil folyamat gombjának** keresése triggerként.

1. Ebben az triggerben adjon hozzá egy olyan bemenetet, mint egy olyan **hely** szövegbeviteli bemenete, amely megfelel a módosítani kívánt beállításnak vagy tulajdonságnak. Módosítsa a Leírás szövegét.

1. Adjon hozzá egy új műveletet. Keresse meg az **Azure IoT Central – eszköz frissítése** műveletet.

1. Válassza ki az alkalmazást a legördülő listából. Most szüksége lesz a frissíteni kívánt meglévő eszköz AZONOSÍTÓJÁRA. 

    > [!NOTE] 
    > A frissíteni kívánt eszköz eszköz részletei lapján **található URL-címen található azonosítót kell használnia** . A Device Explorer eszközök listájában található azonosító nem az a legmegfelelőbb, amelyet a Microsoft Flow használhat.

    ![AZONOSÍTÓ IoT Central URL-címről](./media/howto-add-microsoft-flow/iotcdeviceidurl.png)

1. Frissítheti az eszköz nevét. Az eszköz tulajdonságainak és beállításainak frissítéséhez ki kell választania az eszköz sablonját, amelyet frissíteni kíván az **eszköz sablonjának** legördülő menüjében. A művelet csempe kibontja a frissíteni kívánt tulajdonságok és beállítások megjelenítését.

    ![Flow frissítési eszközének munkafolyamata](./media/howto-add-microsoft-flow/flowupdatedevice1.png)

1. Válassza ki a frissíteni kívánt tulajdonságokat és beállításokat. A dinamikus tartalom ablaktáblán válassza ki a megfelelő bemenetet az triggerből. Ebben a példában a Location (hely) értéket propagálja a rendszer, hogy frissítse az eszköz Location tulajdonságát.

1. Végül mentse a munkafolyamatot.

1. Próbálja ki a munkafolyamatot a Microsoft Flow Mobile alkalmazásban. Nyissa meg az alkalmazás **gombok** lapját. Ekkor megjelenik a gomb – > frissítse az eszköz munkafolyamatát. Adja meg a bemeneteket, és tekintse meg az eszköz frissítését IoT Centralban!

## <a name="get-device-information-in-a-workflow"></a>Eszköz adatainak beolvasása munkafolyamatban

Az eszköz adatait az **Azure IoT Central – eszköz** beszerzése művelet használatával szerezheti be. 
> [!NOTE] 
> A frissíteni kívánt eszköz eszköz részletei lapján **található URL-címen található azonosítót kell használnia** . A Device Explorer eszközök listájában található azonosító nem az a legmegfelelőbb, amelyet a Microsoft Flow használhat.

Az eszköz neve, az eszköz sablon neve, a tulajdonságértékek és a beállítások értékeit a munkafolyamatban a későbbi műveletekhez továbbíthatja. Az alábbi példa egy olyan munkafolyamat-munkafolyamatot mutat be, amely egy eszközről a Microsoft Teams szolgáltatásba továbbítja az ügyfél neve tulajdonság értékét.

   ![Folyamat – eszköz-munkafolyamat lekérése](./media/howto-add-microsoft-flow/flowgetdevice1.png)


## <a name="run-a-command-on-a-device-in-a-workflow"></a>Parancs futtatása egy munkafolyamatban lévő eszközön
Az azonosító által megadott eszközön futtathat egy parancsot az **Azure IoT Central – parancs futtatása** művelettel. 

> [!NOTE] 
> A frissíteni kívánt eszköz eszköz részletei lapján **található URL-címen található azonosítót kell használnia** . A Device Explorer eszközök listájában található azonosító nem az a legmegfelelőbb, amelyet a Microsoft Flow használhat.
    
Kiválaszthatja a parancs futtatását, és átadhatja a parancs paramétereit a művelettel. Az alábbi példa egy olyan munkafolyamat, amely egy eszköz újraindítási parancsát futtatja a Microsoft Flow Mobile alkalmazás egyik gombján.

   ![Folyamat – eszköz-munkafolyamat lekérése](./media/howto-add-microsoft-flow/flowrunacommand1.png)

## <a name="delete-a-device-in-a-workflow"></a>Eszköz törlése egy munkafolyamatban

Az eszközt az AZONOSÍTÓjának használatával törölheti az **Azure IoT Central – eszköz törlése** művelettel. 
> [!NOTE] 
> A frissíteni kívánt eszköz eszköz részletei lapján **található URL-címen található azonosítót kell használnia** . A Device Explorer eszközök listájában található azonosító nem az a legmegfelelőbb, amelyet a Microsoft Flow használhat.

Íme egy példa egy olyan munkafolyamatra, amely töröl egy eszközt a Microsoft Flow Mobile App gomb leküldésekor.

   ![Folyamat törlése eszköz munkafolyamat](./media/howto-add-microsoft-flow/flowdeletedevice.png)

## <a name="troubleshooting"></a>Hibaelhárítás

Ha nem sikerül létrehoznia az Azure IoT Central-összekötőhöz való kapcsolódást, néhány tippet talál a segítségére.

1. A Microsoft személyes fiókjai ( @hotmail.compéldául @outlook.com , @live.com, tartományok) jelenleg nem támogatottak. Azure Active Directory (AD) munkahelyi vagy iskolai fiókot kell használnia.

2. Ha a IoT Central-összekötőt a Microsoft Flowban szeretné használni, legalább egyszer be kell jelentkeznie a IoT Central alkalmazásba. Ellenkező esetben az alkalmazás nem jelenik meg az alkalmazás legördülő listájában.

3. Ha Azure AD-fiók használatakor hibaüzenetet kap, próbálja meg megnyitni a Windows PowerShellt, és futtassa a következő parancsmagok rendszergazdaként.

    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan használhatók a Microsoft Flow a munkafolyamatok létrehozásához, a javasolt következő lépés az [eszközök kezelése](howto-manage-devices.md).

