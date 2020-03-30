---
title: 'Rövid útmutató: Elérhető szobák keresése - Azure Digital Twins | Microsoft dokumentumok'
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
ms.openlocfilehash: de8611e53339d38a03836bc9272ad42ff88a59b8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79371426"
---
# <a name="quickstart-find-available-rooms-by-using-azure-digital-twins"></a>Rövid útmutató: Elérhető szobák keresése az Azure Digital Twins használatával

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

Az Azure Digital Twins szolgáltatás lehetővé teszi, hogy újra hozzon létre egy digitális képet a fizikai környezetről. Ezután értesítéseket kaphat a környezetben zajló eseményekről, és testre szabhatja a rájuk adott válaszokat.

Ez a rövid útmutató [egy pár .NET mintát](https://github.com/Azure-Samples/digital-twins-samples-csharp) használ egy képzeletbeli irodaépület digitalizálásához. Megmutatja, hogyan találhat elérhető szobákat abban az épületben. A Digital Twins segítségével számos érzékelőt társíthat a környezetéhez. Azt is megtudhatja, hogy a rendelkezésre álló helyiség levegőminősége optimális-e egy szimulált szén-dioxid-érzékelő segítségével. A mintaalkalmazások egyike véletlenszerű érzékelőadatokat hoz létre, amelyek segítenek a forgatókönyv megjelenítésében.

Az alábbi videó összefoglalja a rövid útmutató t:

>[!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>Előfeltételek

1. Ha még nem rendelkezik Azure-fiókkal, első lépésként hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

1. A rövid útmutatóban futtatott két konzolalkalmazás c# használatával történik. Telepítse a [.NET Core SDK 2.1.403-as vagy újabb verzióját](https://www.microsoft.com/net/download) a fejlesztőgépre. Ha telepítve van a .NET Core SDK, ellenőrizze a C# aktuális verzióját a fejlesztőgépen. Futtassa `dotnet --version` a parancssorból.

1. Töltse le a [c# mintaprojektet.](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip) Kivonat a digitális-ikrek-minták-csharp-master.zip archívum.

## <a name="create-a-digital-twins-instance"></a>Digital Twins-példány létrehozása

Hozzon létre egy új példányt a digitális twins a [portálon](https://portal.azure.com) az ebben a szakaszban leírt lépéseket követve.

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>Az alkalmazás engedélyeinek beállítása

Ez a szakasz regisztrálja a mintaalkalmazást az Azure Active Directoryba (Azure AD), hogy hozzáférhessen a digital Twins-példányhoz. Ha már rendelkezik egy Azure AD-alkalmazás regisztráció, újra felhasználja azt a minta. Győződjön meg arról, hogy az ebben a szakaszban leírtak szerint van konfigurálva.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="build-application"></a>Az alkalmazás összeállítása

Az alábbi lépések végrehajtásával hozd létre a használt alkalmazást.

1. Nyisson meg egy parancssort. Nyissa meg azt `digital-twins-samples-csharp-master.zip` a mappát, amelyben a fájlokat kibontották.
1. Futtassa az `cd occupancy-quickstart/src` parancsot.
1. Futtassa az `dotnet restore` parancsot.
1. Az [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) fájlban módosítsa az alábbi változókat:
    - **ClientId**: Adja meg az Azure AD-alkalmazás regisztrációjának alkalmazásazonosítóját, amelyet az előző szakaszban jegyezünk fel.
    - **Bérlő:** Adja meg az Azure AD-bérlő címtárazonosítóját, amely az előző szakaszban is szerepel.
    - **BaseUrl**: A Digital Twins példány felügyeleti API-URL-címe formátumban `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`van. Cserélje le az URL-cím helyőrzőit az előző szakasz példányának értékeire.

    Mentse a módosított fájlt.

## <a name="provision-graph"></a>Diagram kiépítése

Ez a lépés a digitális twins térbeli grafikont a következőkkel teszi leva:

- Több helyen.
- Egy eszköz.
- Két szenzor.
- Egy egyéni függvény.
- Egy szerepkör-hozzárendelés.

A térbeli diagram a [provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) fájl használatával van kiépítve.

1. Futtassa az `dotnet run ProvisionSample` parancsot.

    >[!NOTE]
    >Az Eszközbejelentkezési Azure CLI eszköz a felhasználó azure AD-n való hitelesítésére szolgál. A felhasználónak meg kell adnia egy adott kódot [a hitelesítéshez a Microsoft bejelentkezési](https://microsoft.com/devicelogin) lapján keresztül. A kód megadása után kövesse a hitelesítéshez szükséges lépéseket. A felhasználónak hitelesítenie kell magát az eszköz futtatásakor.

    >[!TIP]
    > A lépés futtatásakor ellenőrizze, hogy a változók megfelelően másolva voltak-e, ha a következő hibaüzenet jelenik meg:`EXIT: Unexpected error: The input is not a valid Base-64 string ...`

1. A kiépítési lépés eltarthat néhány percet. Azt is rendelkezik egy IoT Hub a digitális twins példányon belül. Végighalad, amíg az IoT`Running`Hub status= .it loops through until the IoT Hub shows Status= .

    [![A minta kiépítése - Status=Running](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-provision-sample.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-provision-sample.png#lightbox)

1. A végrehajtás végén másolja az `ConnectionString` eszköz az eszköz szimulátor minta. Csak a képen körvonalazott karakterlánc másolása.

    [![A kapcsolati sztring másolása](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-connection-string.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-connection-string.png#lightbox)

    >[!TIP]
    > Az Azure Digital Twins Graph Viewer segítségével megtekintheti és módosíthatja térbeli [grafikonját.](https://github.com/Azure/azure-digital-twins-graph-viewer)

Tartsa nyitva a konzolablakot, hogy később újra használhassa.

## <a name="send-sensor-data"></a>Érzékelőadatok küldése

Az alábbi lépések végrehajtásával felépítheti és futtatja az érzékelőszimulátor-eszközalkalmazást.

1. Nyisson meg egy új parancssort. Nyissa meg a `digital-twins-samples-csharp-master` mappában letöltött projektet.
1. Futtassa az `cd device-connectivity` parancsot.
1. Futtassa az `dotnet restore` parancsot.
1. Edit [appsettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) frissíteni **DeviceConnectionString** `ConnectionString`az előző . Mentse a módosított fájlt.
1. Futtassa `dotnet run` az érzékelőadatok küldésének megkezdéséhez. A rendszer elküldi az Azure Digital Twins-nek, ahogy az az alábbi képen látható.

     [![Eszközkapcsolatok](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-device-connectivity.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-device-connectivity.png#lightbox)

1. Hagyja, hogy ez a szimulátor futjon, így az eredményeket egymás mellett tekintheti meg a következő lépésművelettel. Ebben az ablakban a digitális ikreknek küldött szimulált érzékelőadatok láthatók. A következő lépés valós időben kérdezi le a rendelkezésre álló szobákat friss levegővel.

    >[!TIP]
    > A lépés futtatásakor `DeviceConnectionString` győződjön meg arról, hogy a rendszer megfelelően másolta-e a programot, ha a következő hibaüzenet jelenik meg:`EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Friss levegővel rendelkező szabad szobák keresése

Az érzékelőminta két érzékelő véletlenszerű adatértékeit szimulálja. Mozgás és szén-dioxid. A friss levegővel rendelkező, rendelkezésre álló tereket a mintában úgy határozzák meg, hogy a helyiségben nincs jelenlét. 1000 ppm alatti szén-dioxid szint határozza meg őket. Ha a feltétel nem teljesül, a hely nem áll rendelkezésre, vagy a levegő minősége gyenge.

1. Nyissa meg a kiépítési lépés korábbi futtatásához használt parancssort.
1. Futtassa az `dotnet run GetAvailableAndFreshSpaces` parancsot.
1. Nézze meg ezt a parancssort és az érzékelő adatparancssorát egymás mellett.

    Az érzékelő adatparancsprompt a szimulált mozgás- és szén-dioxid-adatokat öt másodpercenként küldi el a Digital Twins-nek. A másik parancssor valós időben olvassa a grafikont, hogy véletlenszerű szimulált adatok alapján megtudja, hogy a rendelkezésre álló szobák friss levegővel rendelkeznek. Az egyik ilyen feltételt közel valós időben jeleníti meg az utoljára küldött érzékelőadatok alapján:
   - `Room is available and air is fresh`
   - `Room is not available or air quality is poor`

     [![Friss levegővel rendelkező szabad szobák lekérése](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-get-available.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-get-available.png#lightbox)

Ha tudni szeretné, hogy mi történt ebben a rövid útmutatóban, és milyen `digital-twins-samples-csharp`API-kat hívott, nyissa meg a [Visual Studio-kódot](https://code.visualstudio.com/Download) a programban található kód-munkaterületi projekttel. Használja az alábbi parancsot:

```cmd
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

Az oktató mélyen a kódot. Bemutatjuk, hogyan módosíthatja a konfigurációs adatokat, és milyen API-kat nevezzük. A felügyeleti API-kkal kapcsolatos további információkért látogasson el a Digital Twins Swagger oldalára:

```URL
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Név | Csere erre |
| --- | --- |
| YOUR_INSTANCE_NAME | A digitális twins példány neve |
| YOUR_LOCATION | Az a kiszolgálórégió, ahol a példány található |

Vagy a kényelem, keresse meg a [Digitális Twins Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktató részletesen arról, hogyan:

- Hozzon létre egy alkalmazást a létesítmény vezetők számára, hogy növelje a lakók termelékenységét.
- Az épületet hatékonyabban kell működtetni.

Az oktatóanyagok folytatásához ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem tervezi a folytatást, törölje a rövid útmutató által létrehozott összes erőforrást.

1. Törölje a mintatár letöltésekor létrehozott mappát.
1. Az [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza az **Összes erőforrás**lehetőséget. Ezután válassza ki a digitális twins erőforrást. A **Minden erőforrás** ablaktábla tetején válassza a **Törlés**lehetőséget.

    > [!TIP]
    > Ha korábban problémát tapasztalt a Digitális Twins-példány törlésekor, a javítással egy szolgáltatásfrissítés lett elvezetve. Próbálja meg újra kihagyni a példányt.

## <a name="next-steps"></a>További lépések

Ez a rövid útmutató egy egyszerű forgatókönyvés mintaalkalmazások segítségével mutatta be, hogyan használhatók a Digital Twins a jó munkakörülményekkel rendelkező szobák megtalálásához. A forgatókönyv részletes elemzéséhez olvassa el ezt az oktatóanyagot:

>[!div class="nextstepaction"]
>[Oktatóanyag: Az Azure Digital Twins üzembe helyezése és egy térbeli diagram konfigurálása](tutorial-facilities-setup.md)
