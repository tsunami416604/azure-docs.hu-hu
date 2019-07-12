---
title: Hozzon létre munkafolyamatokat az Azure IoT Central-összekötő a Microsoft Flow |} A Microsoft Docs
description: Az IoT Central-összekötő a Microsoft Flow használatával aktiválhatja a munkafolyamatokat és létrehozása, beolvasása, frissítése, eszközök és parancsok futtatása munkafolyamatokban.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
manager: hegate
ms.openlocfilehash: e8bc8b8d4e3585ea4c0505f2e36abc6d1da7f8eb
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797709"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Hozzon létre munkafolyamatokat az IoT Central-összekötő a Microsoft Flow

*Ez a témakör létrehozói és a rendszergazdák vonatkozik.*

Munkafolyamatok automatizálása a számos alkalmazásokat és szolgáltatásokat, üzleti felhasználók által használt, a Microsoft Flow használatával. Az IoT Central-összekötő használatával a Microsoft Flow, beállíthat munkafolyamatokat IoT-központ a szabály aktiválásakor. Egy munkafolyamatban, IoT-központ vagy bármely más alkalmazás által aktivált használhatja az IoT Central-összekötő a műveletek:
- Eszköz létrehozása
- Eszközadatok beolvasása
- Egy eszköz tulajdonságait és beállítások frissítése
- Futtassa a parancsot az eszközön
- Eszköz törlése

Tekintse meg [a Microsoft Flow sablonok](https://aka.ms/iotcentralflowtemplates) IoT-központ más szolgáltatásokba, mint a mobil értesítések és a Microsoft Teams kapcsolódnak.

## <a name="prerequisites"></a>Előfeltételek

- A Pay-As-You-Go application
- Egy Microsoft személyes és munkahelyi vagy iskolai fiókot használata a Microsoft Flow ([tudjon meg többet a Microsoft Flow-tervek](https://aka.ms/microsoftflowplans))
- A munkahelyi vagy iskolai fiókkal az Azure IoT Central-összekötő használatára

## <a name="trigger-a-workflow"></a>Egy munkafolyamat-trigger

Ez a szakasz bemutatja, hogy miként indítható el a Flow mobilalkalmazásnak, amikor egy IoT-központ a szabály aktiválásakor a mobil értesítés. A teljes munkafolyamatot belül az IoT Central-alkalmazást, a beágyazott Microsoft Flow Tervező használatával hozhat létre.

1. Első lépésként [olyan szabályt hoz létre az IoT-központ](howto-create-telemetry-rules.md). Miután mentette a szabály feltételeit, jelölje be a **Microsoft Flow művelet** új műveletként. Párbeszédpanel megnyílik egy, a munkafolyamat konfigurálása. Az IoT-központ felhasználói fiók bejelentkezett Microsoft Flow-ba való bejelentkezéshez használható.

    ![Hozzon létre egy új Microsoft Flow-művelet](media/howto-add-microsoft-flow/createflowaction.png)

1. Látni fogja, amely rendelkezik hozzáféréssel, és a csatlakoztatott IoT-központ szabály munkafolyamatok listáját. Kattintson a **sablonok böngészése** vagy **új > Létrehozás sablonból** és bármely, a rendelkezésre álló sablonok közül választhat. 

    ![Elérhető a Microsoft Flow sablonok](media/howto-add-microsoft-flow/flowtemplates1.png)

1. Jelentkezzen be a a kiválasztott sablonban fog kérni. 

    > [!NOTE]
    > Az Azure IoT Central-összekötő használatához jelentkezzen be egy Azure Active Directory-fiókkal (munkahelyi vagy iskolai fiók). Egy személyes fiók, mint például abc@outlook.com vagy abc@live.com az Azure IoT Central-összekötő által nem támogatott.

    Miután bejelentkezett az összekötők, ekkor megnyílik az hozhat létre a munkafolyamat-tervezőben. A munkafolyamat, amely rendelkezik az alkalmazás és a szabály már kitöltött IoT-központ eseményindító tartozik.

1. Az adatokat, a művelet és az új műveletek hozzáadásával testre szabásával testre szabhatja a munkafolyamatot. Ebben a példában a művelet akkor **értesítések - mobil értesítés küldése**. Megadhat *dinamikus tartalom* az IoT-központ szabályból, mentén fontos információkat, például az eszköz és az időbélyegző-értesítések való átadásához.

    > [!NOTE]
    > Válassza ki a **Továbbiak** a dinamikus tartalom ablakban elolvashatja a mérés és tulajdonság értékei, amely kiváltotta a szabály a szöveget.

    ![Folyamatvezérlés dinamikus ablaktáblán nyissa meg a művelet szerkesztése](./media/howto-add-microsoft-flow/flowdynamicpane1.png)

1. Ha elkészült a művelet szerkesztése, válassza ki **mentése**. Akkor jut el a munkafolyamatot – áttekintés oldalra. Itt láthatja a futtatási előzményeket, és megoszthatja más munkatársakkal.

    > [!NOTE]
    > Ha azt szeretné, más felhasználók az IoT-központ alkalmazásban ez a szabály szerkesztéséhez, meg kell osztania azt velük a Microsoft Flow. A Microsoft Flow-fiókok hozzáadása tulajdonosként a munkafolyamatban.

1. Ha visszatér az IoT Central alkalmazáshoz, megjelenik ez a szabály a Microsoft Flow művelettel rendelkezik a műveletek területen.

Az IoT Central-összekötő közvetlenül a Microsoft Flow használatával munkafolyamatok is létrehozható. Kiválaszthatja, hogy mely IoT Central-alkalmazást szeretne csatlakozni.

## <a name="create-a-device-in-a-workflow"></a>A munkafolyamat-eszköz létrehozásához

Ez a szakasz bemutatja, hogyan használatával hozhat létre egy új eszközt az IoT Central egy gombra a leküldéses egy mobileszközön, a Microsoft Flow mobilalkalmazás használatával. Ez a művelet a Flow segítségével például a Dynamics ERP-rendszerekkel integrálhatja IoT-központ új eszköz létrehozása, ha egy eszköz hozzáadása máshol.

1. Először hozzon létre egy üres munkafolyamatot a Microsoft Flow.

1. Keresse meg **folyamatgomb mobilhoz** eseményindítóként.

1. Az erre az eseményindítóra, adjon hozzá szövegbevitelt, nevű **eszköznév**. Módosítani kell a leíró szöveg **adja meg az eszköz neve, az új**.

1. Adjon meg új műveletet. Keresse meg a **az Azure IoT Central - eszköz létrehozása** művelet.

1. Válassza ki az alkalmazást, és válasszon egy sablont az eszköz az eszköz létrehozásához kijelölhető. Láthatja, hogy a művelet, bontsa ki a tulajdonságok és az eszköz beállítások megjelenítése.

1. Válassza ki az eszköz neve mezőt. A dinamikus tartalmú ablaktáblában válassza **eszköznév**. Ez az érték a felhasználó beírja a mobilalkalmazáson keresztül, és az új eszközt az IoT-központ neve a bemeneti átadott. Ebben a példában az egyetlen kötelezően kitöltendő mező az eszköz nevét, a piros csillaggal jelölve. Előfordulhat, hogy egy másik eszköz a sablon ki, hogy hozzon létre egy új eszközt szeretne több kötelező mező.

    ![Folyamat létrehozása eszköz dinamikus műveletpanel](./media/howto-add-microsoft-flow/flowcreatedevice1.png)

1. (Nem kötelező) Adja meg a többi mező létrehozása új eszközökhöz tetszés szerint.

1. Végül mentse a munkafolyamatot.

1. Próbálja ki a munkafolyamatot a Microsoft Flow mobilalkalmazásban. Nyissa meg a **gombok** lap az alkalmazásban. Az eszköz új munkafolyamat létrehozása -> gombra kell megjelennie. Adja meg az új eszköz nevét, és tekintse meg az IoT-központ megjelenítése.

    ![Folyamat létrehozása az eszköz mobil képernyőképe](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>A munkafolyamat-eszköz frissítése

Ez a szakasz bemutatja, hogyan eszközbeállítások, illetve egy mobileszközön, a Microsoft Flow mobilalkalmazás használatával egy gombra a leküldéses az IoT-központ tulajdonságainak frissítéséhez.

1. Először hozzon létre egy üres munkafolyamatot a Microsoft Flow.

1. Keresse meg **folyamatgomb mobilhoz** eseményindítóként.

1. Az erre az eseményindítóra, például a beviteli mód hozzáadása egy **hely** szövegbevitel, amely megfelel egy beállítást vagy tulajdonság módosítani szeretné. Módosítani leírásának szövege.

1. Adjon meg új műveletet. Keresse meg a **egy eszköz frissítése az Azure IoT Central -** művelet.

1. Válassza ki az alkalmazás a legördülő listából. Most szüksége lesz a frissíteni kívánt meglévő eszköz Azonosítóját. 

    > [!NOTE] 
    > **Az URL-cím található Azonosítóval kell használnia** meg az eszköz frissíti az eszköz részleteit tartalmazó oldalra. Az eszköz azonosítója, találhatók meg a device Explorerben az eszközök nem a Microsoft Flow használatához a megfelelőt.

    ![IoT-központ azonosító URL-címről](./media/howto-add-microsoft-flow/iotcdeviceidurl.png)

1. Frissítheti az eszköz nevét. Az eszköz tulajdonságok és a beállítások bármelyikét frissítéséhez válassza ki a frissíteni kívánt az eszköz az eszköz sablon a **eszköz sablon** legördülő listából. A művelet csempe kibontása a tulajdonságok és frissítheti beállítások megjelenítése.

    ![A folyamat eszköz munkafolyamat](./media/howto-add-microsoft-flow/flowupdatedevice1.png)

1. Válassza ki az egyes tulajdonságok és frissíteni a kívánt beállításokat. A dinamikus tartalmú ablaktábla válassza ki a megfelelő bemenet a trigger által. Ebben a példában a hely érték propagálja le tulajdonság frissítése az eszköz helyét.

1. Végül mentse a munkafolyamatot.

1. Próbálja ki a munkafolyamatot a Microsoft Flow mobilalkalmazásban. Nyissa meg a **gombok** lap az alkalmazásban. Megjelenik a frissítési eszköz munkafolyamat -> gombra. Adja meg a bemeneti adatok, és tekintse meg az eszköz frissíti az IoT Central!

## <a name="get-device-information-in-a-workflow"></a>A munkafolyamat eszközadatok beolvasása

Az azonosító használatával eszköz információkat szerezhet a **az Azure IoT Central - eszközök beszerzése** művelet. 
> [!NOTE] 
> **Az URL-cím található Azonosítóval kell használnia** meg az eszköz frissíti az eszköz részleteit tartalmazó oldalra. Az eszköz azonosítója, találhatók meg a device Explorerben az eszközök nem a Microsoft Flow használatához a megfelelőt.

Például eszköznév, eszköz sablon neve, tulajdonságértékeket és a munkafolyamat későbbi műveletekben átadása beállításértékeket adatai olvashatók be. Íme egy példa-munkafolyamat, amely továbbítja az ügyfél neve tulajdonság értéke az eszközről a Microsoft Teams.

   ![A folyamat get eszköz munkafolyamat](./media/howto-add-microsoft-flow/flowgetdevice1.png)


## <a name="run-a-command-on-a-device-in-a-workflow"></a>A munkafolyamat egy eszközön a parancs futtatása
A parancs futtatható egy eszközön, az azonosító használatával a **Azure IoT Central - parancs futtatása** művelet. 

> [!NOTE] 
> **Az URL-cím található Azonosítóval kell használnia** meg az eszköz frissíti az eszköz részleteit tartalmazó oldalra. Az eszköz azonosítója, találhatók meg a device Explorerben az eszközök nem a Microsoft Flow használatához a megfelelőt.
    
Kiválaszthatja, hogy a parancs futtatásához, és továbbítja a parancs paraméterei ezt a műveletet. Íme egy példa-munkafolyamat, amely egy újraindítási parancsot futtatja, a Microsoft Flow mobilalkalmazásban egy gomb.

   ![A folyamat get eszköz munkafolyamat](./media/howto-add-microsoft-flow/flowrunacommand1.png)

## <a name="delete-a-device-in-a-workflow"></a>A munkafolyamat eszköz törlése

Egy eszköz az azonosító használatával törölheti az **eszköz törlése az Azure IoT Central -** művelet. 
> [!NOTE] 
> **Az URL-cím található Azonosítóval kell használnia** meg az eszköz frissíti az eszköz részleteit tartalmazó oldalra. Az eszköz azonosítója, találhatók meg a device Explorerben az eszközök nem a Microsoft Flow használatához a megfelelőt.

Íme egy példa-munkafolyamat, amely törli egy eszköz a Microsoft Flow mobilalkalmazásban egy gombra a leküldéses.

   ![A folyamat törlése eszköz munkafolyamat](./media/howto-add-microsoft-flow/flowdeletedevice.png)

## <a name="troubleshooting"></a>Hibaelhárítás

Ha gondjai vannak szeretne létrehozni az Azure IoT Central-összekötő kapcsolatot, az alábbiakban néhány tipp.

1. Személyes Microsoft-fiókok (például @hotmail.com, @live.com, @outlook.com tartományok) jelenleg nem támogatottak. Meg kell egy Azure Active Directory (AD) munkahelyi vagy iskolai fiókjával.

2. Az IoT Central-összekötő a Microsoft Flow használatához, akik bejelentkeztek legalább egyszer az IoT Central alkalmazáshoz. Ellenkező esetben az alkalmazás nem jelenik meg az alkalmazás legördülő menüjére.

3. Ha egy Azure AD-fiók használata során hiba azért küldtük Önnek, próbálja meg megnyitni a Windows PowerShell, és a következő parancsmagok esetén futtassa rendszergazdaként.

    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a Microsoft Flow használatával munkafolyamatok létrehozását, a javasolt következő lépésre, hogy [Eszközkezelés](howto-manage-devices.md).

