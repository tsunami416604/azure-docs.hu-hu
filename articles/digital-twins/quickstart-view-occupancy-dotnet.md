---
title: 'Gyors útmutató: elérhető szobák keresése – Azure digitális Twins | Microsoft Docs'
description: Ebben a rövid útmutatóban két .NET Core-alkalmazást futtathat egy adott tér szimulált mozgás- és szén-dioxid-telemetriaadatainak az Azure Digital Twinsbe való elküldésére. A cél a friss levegővel rendelkező és szabad szobák keresése a Management API-kon keresztül az adatok a felhőben való számítási feldolgozását követően.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc seodec18
ms.date: 01/10/2020
ms.openlocfilehash: 6c9c5df27f4a361e534bac2fe21b2c470f8d0186
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382222"
---
# <a name="quickstart-find-available-rooms-by-using-azure-digital-twins"></a>Gyors útmutató: Elérhető Teremkeresés Azure digitális Twins használatával

Az Azure digitális Twins szolgáltatás lehetővé teszi, hogy hozza létre újból a fizikai környezetnek digitális képe. Ezután értesítéseket kaphat a környezetben zajló eseményekről, és testre szabhatja a rájuk adott válaszokat.

Ez a rövid útmutató [a .net-minták egy pár változatát](https://github.com/Azure-Samples/digital-twins-samples-csharp) használja egy képzeletbeli irodaház kialakításához. Ez bemutatja, hogyan érhető el az adott épület teremkeresés. A digitális Twins sok érzékelő is társíthat a környezetben. Is talál a légi minőség a rendelkezésre álló hely-e egy szimulált érzékelő szén-dioxid segítségével optimális. A mintaalkalmazások egyike állít elő, véletlenszerű érzékelőktől kapott adatok segítségével vizualizálhatja az ebben a forgatókönyvben.

A következő videó összefoglalja a gyors üzembe helyezési beállítást:

>[!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>Előfeltételek

1. Ha még nem rendelkezik Azure-fiókkal, első lépésként hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

1. Ebben a rövid útmutatóban a két konzol alkalmazások használatával írt C#. Telepítse a [.net Core SDK 2.1.403 vagy újabb verziót](https://www.microsoft.com/net/download) a fejlesztői gépre. Ha a .NET Core SDK telepítve van, ellenőrizze a jelenlegi verziója C# a fejlesztői gépen. `dotnet --version` futtatása parancssorból.

1. A [minta C# projekt](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip)letöltése. Bontsa ki a digitális-twins-samples-csharp-master.zip archívumot.

## <a name="create-a-digital-twins-instance"></a>Digital Twins-példány létrehozása

A jelen szakaszban ismertetett lépéseket követve hozzon létre egy új digitális Twins-példányt a [portálon](https://portal.azure.com) .

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>Az alkalmazás engedélyeinek beállítása

Ez a szakasz a mintaalkalmazást az Azure Active Directory (Azure AD) regisztrál, úgy, hogy hozzá tudjon férni a digitális Twins-példány. Ha már rendelkezik egy Azure AD-alkalmazás regisztrációjának, újból felhasználhatja a mintában. Győződjön meg arról, hogy ebben a szakaszban leírtak szerint konfigurálva van.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="build-application"></a>Az alkalmazás összeállítása

Hozza létre az foglaltsága alkalmazást az alábbi lépéseket.

1. Nyisson meg egy parancssort. Nyissa meg azt a mappát, ahová a `digital-twins-samples-csharp-master.zip` fájlokat kibontotta.
1. Futtassa az `cd occupancy-quickstart/src` parancsot.
1. Futtassa az `dotnet restore` parancsot.
1. Az [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) fájlban módosítsa az alábbi változókat:
    - **ClientId**: adja meg az Azure ad-alkalmazás regisztrációs azonosítóját az előző szakaszban leírtak szerint.
    - **Bérlő**: adja meg az Azure ad-bérlő CÍMTÁR-azonosítóját, amelyet az előző szakaszban is fel kell tüntetni.
    - **BaseUrl**: a digitális Twins-példány felügyeleti API URL-címének formátuma `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Cserélje le a helyőrzőket a URL-CÍMBEN szereplő értékek a példány az előző szakaszban.

    Mentse a módosított fájlt.

## <a name="provision-graph"></a>Diagram kiépítése

Ebben a lépésben a digitális Twins térbeli gráf építi ki:

- Több szóközt.
- Egy eszköz.
- Két érzékelők.
- Egyéni függvény.
- Egy szerepkör-hozzárendelés.

A térbeli gráf a [provisionSample. YAML](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) fájl használatával lett kiépítve.

1. Futtassa az `dotnet run ProvisionSample` parancsot.

    >[!NOTE]
    >Az eszköz bejelentkezési Azure parancssori eszköz segítségével hitelesíti a felhasználót az Azure AD. A felhasználónak egy megadott kódot kell megadnia a hitelesítéshez [a Microsoft bejelentkezési](https://microsoft.com/devicelogin) oldalának használatával. A kód megadása után végezze el a hitelesítés lépéseit. Az eszköz futtatásakor a felhasználónak hitelesítenie kell magát.

    >[!TIP]
    > Ha ezt a lépést futtatja, ellenőrizze, hogy a változók megfelelően lettek-e másolva, ha a következő hibaüzenet jelenik meg: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

1. A kiépítési lépés néhány percet is igénybe vehet. Azt is látja el a digitális Twins példány belül az IoT hubra. Ez a ciklus egészen addig, amíg a IoT Hub megjeleníti az állapot =`Running`.

    [![kiépíteni a minta-status = futást](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-provision-sample.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-provision-sample.png#lightbox)

1. A végrehajtás végén másolja az eszköz `ConnectionString` a Device Simulator-mintában való használatra. Csak a képen leírt karakterlánc másolja.

    [![a kapcsolatok karakterláncának másolása](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-connection-string.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-connection-string.png#lightbox)

    >[!TIP]
    > A térbeli gráfot az [Azure Digital Twins Graph Viewer](https://github.com/Azure/azure-digital-twins-graph-viewer)használatával tekintheti meg és módosíthatja.

A konzol ablakát később is megnyitva használhatja.

## <a name="send-sensor-data"></a>Érzékelőadatok küldése

Az alábbi lépéseket követve hozza létre és futtassa az Sensor Simulator eszköz alkalmazást.

1. Nyisson meg egy új parancssort. Lépjen a `digital-twins-samples-csharp-master` mappában letöltött projekthez.
1. Futtassa az `cd device-connectivity` parancsot.
1. Futtassa az `dotnet restore` parancsot.
1. Szerkessze a [appSettings. JSON](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) fájlt a **DeviceConnectionString** frissítéséhez az előző `ConnectionString`. Mentse a módosított fájlt.
1. `dotnet run` futtatása az érzékelői adatok küldésének megkezdéséhez. A rendszer a következő képen látható módon küldi el az Azure digitális Twins-nek.

     [![eszköz kapcsolata](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-device-connectivity.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-device-connectivity.png#lightbox)

1. Lehetővé teszik a szimulátor futtatni, hogy párhuzamosan lesz a következő lépés művelet eredményeit tekintheti meg. Ebben az ablakban látható digitális Twins küldött szimulált érzékelőadatokat. A következő lépés lekérdezések friss vezeték nélkül regisztrálja az elérhető teremkeresés valós időben.

    >[!TIP]
    > Ha ezt a lépést futtatja, győződjön meg arról, hogy a `DeviceConnectionString` megfelelően lett másolva, ha a következő hibaüzenet jelenik meg: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Friss levegővel rendelkező szabad szobák keresése

Az érzékelő minta szimulálja véletlenszerű adatértékek két érzékelő számára. Azok a mozgásban lévő adatoknak egyaránt és szén-dioxid. Friss vezeték nélkül regisztrálja az elérhető tárolóhelyek határozzák meg a minta nem észlelnek a helyiségben jelenlétét. Ezek Ön is határozzák meg egy szén-dioxid-szint alatt 1000 ppm. Ha a feltétel nem teljesül, a hely nem érhető el, vagy a légi minősége gyenge.

1. Nyissa meg a parancssort, amelyet korábban a kiépítési lépés futtatásához használt.
1. Futtassa az `dotnet run GetAvailableAndFreshSpaces` parancsot.
1. Tekintse meg a parancssort, és az érzékelő adatokat parancssor egymás mellett.

    Az érzékelő adatkérési parancssora öt másodpercenként szimulált mozgást és széndioxid-adatokat küld a digitális ikreknek. A másik parancssor valós időben beolvassa a diagramot, hogy a véletlenszerűen szimulált adatokat tartalmazó, friss levegővel rendelkező szobákból kiderítse a rendelkezésre álló szobákat. Ezek a feltételek egyik közel valós időben a legutóbb elküldött érzékelőktől kapott adatok alapján megjeleníti:
   - `Room is available and air is fresh`
   - `Room is not available or air quality is poor`

     [![rendelkezésre álló tárhelyek lekérése friss levegővel](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-get-available.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-get-available.png#lightbox)

Ha meg szeretné tudni, hogy mi történt ebben a rövid útmutatóban, és milyen API-kat hívott meg, nyissa meg a [Visual Studio Code](https://code.visualstudio.com/Download) -ot a `digital-twins-samples-csharp`címen található Code Workspace- Használja az alábbi parancsot:

```cmd
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

Az oktatóanyagok meg alaposabban a kódot. Azok a szól, hogyan lehet módosítani a konfigurációs adatok és az API-k ún. További információ a felügyeleti API-k nyissa meg a digitális Twins Swagger oldalát:

```URL
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Name (Név) | Csere erre |
| --- | --- |
| YOUR_INSTANCE_NAME | A digitális Twins-példány nevét |
| YOUR_LOCATION | Az a kiszolgáló régiója, ahol a példánya üzemeltetve van |

Vagy a kényelemért keresse meg a [digitális ikrek hencegő](https://docs.westcentralus.azuresmartspaces.net/management/swagger)elemét.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagok nyissa meg az útmutató részletesen:

- Létesítmény vezetők bent termelékenység növelése és a egy olyan alkalmazás létrehozásához.
- Működik az épület hatékonyabban.

Továbbra is az oktatóanyagok, hogy ne törölje az erőforrásokat létrehozott ebben a rövid útmutatóban. Ha nem szeretné folytatni, törölje a rövid útmutatóhoz létrehozott összes erőforrást.

1. Törölje a mappát, amely jött létre, amikor a minta tárház letöltött.
1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza az **összes erőforrás**lehetőséget. Ezután válassza ki a digitális Twins erőforrás. A **minden erőforrás** ablaktábla tetején válassza a **Törlés**lehetőséget.

    > [!TIP]
    > Ha korábban már problémát észlelt a digitális Twins-példány törlése során, a rendszer a javítást a javítással együtt kivezette. Ismételje meg a példány törlése.

## <a name="next-steps"></a>Következő lépések

Ez a rövid útmutató egy egyszerű forgatókönyvet és egy példaként szolgáló alkalmazást használt, amely bemutatja, hogyan használhatók a digitális ikrek a megfelelő munkafeltételekkel rendelkező szobák megtalálására. A forgatókönyv részletes elemzéséhez olvassa el ezt az oktatóanyagot:

>[!div class="nextstepaction"]
>[Oktatóanyag: Az Azure Digital Twins üzembe helyezése és egy térbeli diagram konfigurálása](tutorial-facilities-setup.md)
