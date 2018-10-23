---
title: Szabad szobák keresése az Azure Digital Twins használatával (C#) | Microsoft Docs
description: Ebben a rövid útmutatóban két .NET Core-alkalmazást futtathat egy adott tér szimulált mozgás- és szén-dioxid-telemetriaadatainak az Azure Digital Twinsbe való elküldésére. A cél a friss levegővel rendelkező és szabad szobák keresése a Management API-kon keresztül az adatok a felhőben való számítási feldolgozását követően.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/02/2018
ms.author: alinast
ms.openlocfilehash: e0b47b1322a520ad8b09fd2fe2967e628b5e4e03
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49322879"
---
# <a name="quickstart-find-available-rooms-using-azure-digital-twins"></a>Rövid útmutató: Szabad szobák keresése az Azure Digital Twins segítségével

Az Azure Digital Twins szolgáltatással előállítható a fizikai környezet digitális képe. Ezután értesítéseket kaphat a környezetben zajló eseményekről, és testre szabhatja a rájuk adott válaszokat. 

Ez a rövid útmutató [két .NET-minta](https://github.com/Azure-Samples/digital-twins-samples-csharp) használatával digitalizál egy képzeletbeli épületet, és bemutatja, hogyan lehet szabad szobákat keresni az épületben. A Digital Twinsszel több érzékelőt rendelhet hozzá a környezethez. A szimulált szén-dioxid-érzékelő segítségévvel a szobák foglaltsága mellett azt is megtudhatja, hogy a szabad szobákban jó-e a levegőminőség. A mintaalkalmazások egyike véletlenszerű érzékelőadatokat hoz létre a forgatókönyv jobb vizuális megjelenítése érdekében.

## <a name="prerequisites"></a>Előfeltételek

1. Ha még nem rendelkezik Azure-fiókkal, első lépésként hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

1. A rövid útmutatóban futtatott két konzolalkalmazás a C# használatával készült. A fejlesztői gépen telepítenie kell a [.NET Core SDK 2.1.403-as vagy újabb verzióját](https://www.microsoft.com/net/download). Ha a .NET Core SDK telepítve van, a C# aktuális verziójának ellenőrzéséhez futtassa a következő parancsot egy parancssorban a fejlesztői gépen: `dotnet --version`.

1. Töltse le a [C#-mintaprojektet](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip), és bontsa ki a digital-twins-samples-csharp-master.zip archívumot. 


## <a name="create-a-digital-twins-instance"></a>Digital Twins-példány létrehozása

Az ebben a szakaszban ismertetett lépések végrehajtásával hozzon létre egy új Digital Twins-példányt a [portálon](https://portal.azure.com).

[!INCLUDE [create-digital-twins-portal](../../includes/create-digital-twins-portal.md)]

## <a name="set-permissions-for-your-app"></a>Az alkalmazás engedélyeinek beállítása

Ez a szakasz regisztrálja a mintaalkalmazást az Azure Active Directoryban (AAD), hogy elérhesse a Digital Twins-példányt. Ha már rendelkezik AAD-alkalmazásregisztrációval, felhasználhatja azt ehhez a mintához, de mindenképp úgy konfigurálja, ahogy az ebben a szakaszban szerepel. 

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]


## <a name="build-application"></a>Az alkalmazás összeállítása

A foglaltságjelző alkalmazást a következő lépések végrehajtásával állíthatja össze:

1. Nyisson meg egy parancssort, és lépjen arra a mappára, ahová a digital-twins-samples-csharp-master.zip fájljait kicsomagolta.
1. Futtassa az `cd occupancy-quickstart/src` parancsot.
1. Futtassa az `dotnet restore` parancsot.
1. Az *appSettings.json* fájlban módosítsa az alábbi változókat:
    - *ClientId*: Adja meg az AAD-alkalmazásregisztráció *alkalmazásazonosítóját*, amelyet az előző szakaszban jegyzett fel.
    - *Tenant*: Adja meg az AAD-bérlő *címtár-azonosítóját*, amelyet szintén az előző szakaszban jegyzett fel.
    - *BaseUrl*: A Digital Twins-példány *Management API-jának* URL-címe, amely a következő formátumot követi: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Az URL-cím helyőrzőit cserélje le a saját példányai adataira, amelyeket az előző szakaszban jegyzett fel.

## <a name="provision-graph"></a>Diagram kiépítése

Ez a lépés üzembe helyezi a Digital Twins térbeli diagramot néhány térrel, egy eszközzel, két érzékelővel, egy egyéni függvénnyel és egy szerepkör-hozzárendeléssel. A térbeli diagramot a [*provisionSample.yaml*](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) fájl használatával helyezi üzembe.

1. Futtassa az `dotnet run ProvisionSample` parancsot.
    >[!NOTE]
    >A felhasználók Azure AD-ben való hitelesítéséhez az Azure CLI Device Login eszközét használjuk. A felhasználónak egy adott kódot kell megadnia a hitelesítéshez [a Microsoft bejelentkezési](https://microsoft.com/devicelogin) oldalán. A kód megadása után kövesse az alábbi lépéseket a hitelesítéshez. A felhasználónak minden alkalommal hitelesítenie kell, amikor az eszköz fut.
    
    >[!TIP]
    > Ha a lépés végrehajtása során bármikor az alábbi hibaüzenetet kapja, ellenőrizze, hogy a változókat megfelelően másolta-e át. 
    > `EXIT: Unexpected error: The input is not a valid Base-64 string ...`


1. A kiépítési lépés végrehajtása igénybe vehet néhány percet. A lépés egy IoT Hub-központot is kiépít a Digital Twins-példányon belül, és addig fut, amíg végül nem fut (azaz az IoT Hub állapota `Running` lesz).

    ![Üzembehelyezési minta][4]

1. A lépés végrehajtásának befejezésekor másolja ki az eszköz `ConnectionString` sztringjét, mivel az eszközszimulátor ezt fogja használni. Csak az alábbi ábrán kiemelt sztringet másolja:

    ![Üzembehelyezési minta][1]

## <a name="send-sensor-data"></a>Érzékelőadatok küldése

Az érzékelőszimulátor alkalmazást az alábbi lépések végrehajtásával állíthatja össze és futtathatja:

1. Nyisson egy új parancssort, és lépjen a letöltött projekthez a digital-twins-samples-csharp-master mappába.
1. Futtassa az `cd device-connectivity` parancsot.
1. Futtassa az `dotnet restore` parancsot.
1. Az *appsettings.json* fájlban módosítsa a *DeviceConnectionString* értékét a fenti `ConnectionString` sztringre.
1. A `dotnet run` paranccsal indítsa el az érzékelőadatok küldését, és látnia kell, ahogy az adatok küldése megkezdődik a Digital Twins szolgáltatásba az alábbi képen látható módon:

     ![Eszközkapcsolatok][2]

1. Hagyja futni a szimulátort, így a következő lépésben végrehajtott műveletben egymás mellett láthatja az eredményeket. Ebben az ablakban a Digital Twinsre küldött szimulált érzékelőadatok láthatók, a következő lépés pedig valós idejű lekérdezésekkel keresi majd a friss levegővel rendelkező szabad szobákat.

    >[!TIP]
    > Ha a lépés végrehajtása során bármikor az alábbi hibaüzenetet kapja, ellenőrizze, hogy a `DeviceConnectionString` sztringet megfelelően másolta-e át.  
    > `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Friss levegővel rendelkező szabad szobák keresése

Az érzékelőminta két érzékelő, egy mozgás- és egy szén-dioxid-érzékelő véletlenszerű adatértékeit szimulálja. A friss levegővel rendelkező szabad szobákat ebben a példában úgy definiáljuk, hogy senki nem tartózkodik a teremben, és a szén-dioxidszint 1000 ppm alatt marad. Ha a feltételek nem teljesülnek, a terem nem szabad, vagy nem megfelelő a levegő minősége.

1. Nyissa meg a parancssort, amelyben a fenti üzembehelyezési lépést futtatta.
1. Futtassa az `dotnet run GetAvailableAndFreshSpaces` parancsot.
1. Nyissa meg ezt és az érzékelőadatokat mutató parancssori ablakot egymás mellett az alább látható módon. 
1. Az egyik parancssor 5 másodpercenként szimulált mozgás- és szén-dioxidadatokat küld a Digital Twinsre. A másik parancs valós időben olvassa a diagramot, és friss levegővel rendelkező szabad szobákat keres a véletlenszerű szimulált adatok alapján. Közel valós időben megjeleníti valamelyik feltételt attól függően, hogy milyen érzékelőadatok érkeztek a legutóbb:
    - Friss levegővel rendelkező szabad szobák.
    - Foglalt vagy nem megfelelő levegővel rendelkező terem.

     ![Friss levegővel rendelkező szabad szobák lekérése][3]

Ha szeretné megérteni, mi történt ebben a rövid útmutatóban, és hogy milyen API-k lettek meghívva, nyissa meg a [Visual Studio Code-ban](https://code.visualstudio.com/Download) a digital-twins-samples-csharp fájlban található kódmunkaterület-projektet (lásd az alábbi parancsot). Az oktatóanyagokban részletesen megismerheti a kódot, és megtanulhatja, hogyan lehet módosítani a konfigurációs adatokat és a meghívott API-kat. A Management API-k részletesebb megismeréséhez látogasson el a Digital Twins Swagger-oldalára (`https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net//management/swagger`), vagy böngéssze a [Digital Twins Swaggert](https://docs.westcentralus.azuresmartspaces.net/management/swagger). 

```
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagok részletesen leírják, hogyan hozhat létre egy létesítménykezelő alkalmazást, amellyel növelhető az épületben tartózkodók termelékenysége, illetve hatékonyabban üzemeltethető az épület.

Ha azt tervezi, hogy ezekkel az oktatóanyaggal folytatja, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti a rövid útmutatóhoz létrehozott összes erőforrást:

1. Törölje a mintaadattár letöltésekor létrehozott mappát.
1. Az [Azure Portal](http://portal.azure.com) bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki a Digital Twins erőforrást. Az **Összes erőforrás** panel tetején kattintson a **Törlés** elemre.

## <a name="next-steps"></a>További lépések

Ez a rövid útmutató egy egyszerű forgatókönyvet mutatott be a megfelelő munkakörülményeket biztosító szobák megkeresésére. A forgatókönyv részletesebb elemzéséhez folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Digital Twins üzembe helyezése és egy térbeli diagram konfigurálása](tutorial-facilities-setup.md)

<!-- Images -->
[1]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png
[2]: media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png
[3]: media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png
[4]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample1.png
