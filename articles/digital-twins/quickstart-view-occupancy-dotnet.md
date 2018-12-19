---
title: Teremkeresés érhető el – az Azure digitális Twins |} A Microsoft Docs
description: Ebben a rövid útmutatóban két .NET Core-alkalmazást futtathat egy adott tér szimulált mozgás- és szén-dioxid-telemetriaadatainak az Azure Digital Twinsbe való elküldésére. A cél a friss levegővel rendelkező és szabad szobák keresése a Management API-kon keresztül az adatok a felhőben való számítási feldolgozását követően.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc seodec18
ms.date: 12/17/2018
ms.author: alinast
ms.openlocfilehash: d2588090ced3e82e63397a416245ca69204f3d87
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53583085"
---
# <a name="quickstart-find-available-rooms-by-using-azure-digital-twins"></a>Gyors útmutató: Elérhető az Azure digitális Twins teremkeresés

Az Azure digitális Twins szolgáltatás lehetővé teszi, hogy hozza létre újból a fizikai környezetnek digitális képe. Ezután értesítéseket kaphat a környezetben zajló eseményekről, és testre szabhatja a rájuk adott válaszokat.

Ebben a rövid útmutatóban használt [olyan .NET-minták virtuálisgép-pár](https://github.com/Azure-Samples/digital-twins-samples-csharp) egy képzeletbeli irodaépület digitalizálhatók. Ez bemutatja, hogyan érhető el az adott épület teremkeresés. A digitális Twins sok érzékelő is társíthat a környezetben. Is talál a légi minőség a rendelkezésre álló hely-e egy szimulált érzékelő szén-dioxid segítségével optimális. A mintaalkalmazások egyike állít elő, véletlenszerű érzékelőktől kapott adatok segítségével vizualizálhatja az ebben a forgatókönyvben.

A következő videó a rövid útmutatóban ismertetett beállítási folyamatot foglalja össze:

>[!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>Előfeltételek

1. Ha még nem rendelkezik Azure-fiókkal, első lépésként hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

1. Ebben a rövid útmutatóban a két konzol alkalmazások használatával írt C#. Telepítse a [.NET Core SDK 2.1.403 verzió vagy újabb](https://www.microsoft.com/net/download) a fejlesztői gépen. Ha a .NET Core SDK telepítve van, ellenőrizze a jelenlegi verziója C# a fejlesztői gépen. Futtatás `dotnet --version` parancsot.

1. Töltse le a [minta C# projekt](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip). Bontsa ki a digitális-twins-samples-csharp-master.zip archívumot.

## <a name="create-a-digital-twins-instance"></a>Digital Twins-példány létrehozása

Hozzon létre egy új példányát a digitális Twins a [portál](https://portal.azure.com) a jelen szakaszban ismertetett lépéseket követve.

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>Az alkalmazás engedélyeinek beállítása

Ez a szakasz a mintaalkalmazást az Azure Active Directory (Azure AD) regisztrál, úgy, hogy hozzá tudjon férni a digitális Twins-példány. Ha már rendelkezik egy Azure AD-alkalmazás regisztrációjának, újból felhasználhatja a mintában. Győződjön meg arról, hogy ebben a szakaszban leírtak szerint konfigurálva van.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="build-application"></a>Az alkalmazás összeállítása

Hozza létre az foglaltsága alkalmazást az alábbi lépéseket.

1. Nyisson meg egy parancssort. Nyissa meg a mappát, ahol a `digital-twins-samples-csharp-master.zip` fájlok könyvtárban találhatók.
1. Futtassa az `cd occupancy-quickstart/src` parancsot.
1. Futtassa az `dotnet restore` parancsot.
1. Az [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) fájlban módosítsa az alábbi változókat:
    - **ClientId**: Adja meg az Azure AD alkalmazás regisztrációját, az előző szakaszban feljegyzett alkalmazás azonosítója.
    - **Bérlő**: Adja meg a címtár-azonosító az Azure AD-bérlő, is az előző szakaszban feljegyzett.
    - **BaseUrl**: A felügyeleti API URL-címe, a digitális Twins-példány a következő formátumban kell `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Cserélje le a helyőrzőket a URL-CÍMBEN szereplő értékek a példány az előző szakaszban.

## <a name="provision-graph"></a>Diagram kiépítése

Ebben a lépésben a digitális Twins térbeli gráf építi ki:

- Több szóközt.
- Egy eszköz.
- Két érzékelők.
- Egyéni függvény.
- Egy szerepkör-hozzárendelés.

A térbeli graph használatával van kiépítve a [provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) fájlt.

1. Futtassa az `dotnet run ProvisionSample` parancsot.
    >[!NOTE]
    >Az eszköz bejelentkezési Azure parancssori eszköz segítségével hitelesíti a felhasználót az Azure AD. A felhasználónak meg kell adnia, hogy egy adott kód használatával történő hitelesítéshez [a Microsoft bejelentkezési](https://microsoft.com/devicelogin) lapot. Miután a kódot is meg kell adni, lépésekkel hitelesítéséhez. Az eszköz futtatásakor a felhasználónak hitelesítenie kell magát.

    >[!TIP]
    > Amikor futtatja ezt a lépést, ellenőrizze, hogy a változók megfelelően lettek másolva, ha a következő hibaüzenet jelenik meg: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

1. A kiépítési lépés néhány percet is igénybe vehet. Azt is látja el a digitális Twins példány belül az IoT hubra. Ez végighalad mindaddig, amíg az IoT Hub állapotát jeleníti meg =`Running`.

    ![Minta üzembe helyezése][4]

1. A végrehajtás végén, másolja a `ConnectionString` az eszköz az eszköz szimulátor minta használható. Csak a képen leírt karakterlánc másolja.

    ![Minta üzembe helyezése][1]

    >[!TIP]
    > Megtekintheti és módosíthatja a térbeli graph használatával a [Azure digitális Twins Graph megjelenítő](https://github.com/Azure/azure-digital-twins-graph-viewer).

## <a name="send-sensor-data"></a>Érzékelőadatok küldése

Alkalmazás összeállítása és futtatása az érzékelő szimulátort az alábbi lépéseket.

1. Nyisson meg egy új parancssort. Nyissa meg a projekt letöltött digital-twins-samples-csharp-master mappában.
1. Futtassa az `cd device-connectivity` parancsot.
1. Futtassa az `dotnet restore` parancsot.
1. Szerkesztés [appsettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) frissíteni **DeviceConnectionString** az előző `ConnectionString`.
1. Futtatás `dotnet run` érzékelőktől kapott adatok küldésének elkezdésére. Láthatja, küldve digitális Twins az alábbi képen látható módon.

     ![Eszközkapcsolatok][2]

1. Lehetővé teszik a szimulátor futtatni, hogy párhuzamosan lesz a következő lépés művelet eredményeit tekintheti meg. Ebben az ablakban látható digitális Twins küldött szimulált érzékelőadatokat. A következő lépés lekérdezések friss vezeték nélkül regisztrálja az elérhető teremkeresés valós időben.

    >[!TIP]
    > Amikor futtatja ezt a lépést, ellenőrizze, hogy `DeviceConnectionString` helyesen másolta, ha a következő hibaüzenet jelenik meg: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Friss levegővel rendelkező szabad szobák keresése

Az érzékelő minta szimulálja véletlenszerű adatértékek két érzékelő számára. Azok a mozgásban lévő adatoknak egyaránt és szén-dioxid. Friss vezeték nélkül regisztrálja az elérhető tárolóhelyek határozzák meg a minta nem észlelnek a helyiségben jelenlétét. Ezek Ön is határozzák meg egy szén-dioxid-szint alatt 1000 ppm. Ha a feltétel nem teljesül, a hely nem érhető el, vagy a légi minősége gyenge.

1. Nyissa meg a parancssort, futtassa az előző kiépítési lépésben használt.
1. Futtassa az `dotnet run GetAvailableAndFreshSpaces` parancsot.
1. Tekintse meg a parancssort, és az érzékelő adatokat parancssor egymás mellett.

    Egy parancssorból adatokat küld a szimulált mozgásban lévő adatoknak egyaránt és szén-dioxid digitális Twins öt másodpercenként. A többi parancs beolvassa a diagramra a valós idejű véletlenszerű szimulált adatok alapján friss vezeték nélkül regisztrálja az elérhető termek megállapítása. Ezek a feltételek egyik közel valós időben a legutóbb elküldött érzékelőktől kapott adatok alapján megjeleníti:
    - Friss levegővel rendelkező szabad szobák.
    - Foglalt vagy nem megfelelő levegővel rendelkező terem.

     ![Friss levegővel rendelkező szabad szobák lekérése][3]

Szeretné megtudni, mi történt, ebben a rövid, és milyen API-k hívták, nyissa meg a [Visual Studio Code](https://code.visualstudio.com/Download) a kód munkaterület projekttel digitális-twins-samples-csharp található. Használja az alábbi parancsot:

```plaintext
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

Az oktatóanyagok meg alaposabban a kódot. Azok a szól, hogyan lehet módosítani a konfigurációs adatok és az API-k ún. További információ a felügyeleti API-k nyissa meg a digitális Twins Swagger oldalát:

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Name (Név) | Csere erre |
| --- | --- |
| YOUR_INSTANCE_NAME | A digitális Twins-példány nevét |
| YOUR_LOCATION | Melyik kiszolgáló régióban lévő üzemeltetett a példány |

Az egyszerűség kedvéért tallózással vagy [digitális Twins Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagok nyissa meg az útmutató részletesen:

- Létesítmény vezetők bent termelékenység növelése és a egy olyan alkalmazás létrehozásához.
- Működik az épület hatékonyabban.

Továbbra is az oktatóanyagok, hogy ne törölje az erőforrásokat létrehozott ebben a rövid útmutatóban. Ha nem szeretné folytatni, törölje a rövid útmutatóhoz létrehozott összes erőforrást.

1. Törölje a mappát, amely jött létre, amikor a minta tárház letöltött.
1. A bal oldali menüben a [az Azure portal](http://portal.azure.com)válassza **összes erőforrás**. Ezután válassza ki a digitális Twins erőforrás. Felső részén a **összes erőforrás** ablaktáblán válassza előbb **törlése**.

    > [!TIP]
    > Ha törli a digitális Twins-példány problémajegyek tapasztal, szolgáltatás frissítése lett állítva a javítás. Ismételje meg a példány törlése.

## <a name="next-steps"></a>További lépések

Ez a rövid útmutató egy egyszerű forgatókönyvet használja bemutatják, hogyan teremkeresés használata feltételekkel. Ez a forgatókönyv részletes elemzéséhez ebben az oktatóanyagban talál:

>[!div class="nextstepaction"]
>[Oktatóanyag: Az Azure digitális Twins telepítheti és konfigurálhatja a térbeli grafikon](tutorial-facilities-setup.md)

<!-- Images -->
[1]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png
[2]: media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png
[3]: media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png
[4]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample1.png
