---
title: Az Azure Digital Twins üzembe helyezése | Microsoft Docs
description: Ismerje meg, hogyan helyezheti üzembe az Azure digitális Twins-példány, és a térbeli erőforrások konfigurálása az ebben az oktatóanyagban szereplő lépések segítségével.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 61b81602342b910a50c0cc6318746ec85a659a92
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080589"
---
# <a name="tutorial-deploy-azure-digital-twins-and-configure-a-spatial-graph"></a>Oktatóanyag: Az Azure Digital Twins üzembe helyezése és egy térbeli diagram konfigurálása

Az Azure digitális Twins szolgáltatás használatával egyesítheti személyek, helyek és eszközök egy összefüggő információszállá térbeli rendszerben. Ez az oktatóanyag-sorozat bemutatja, hogyan használhatja az Azure digitális Twins észleléséhez helyiségben foglaltsága hőmérséklet és a légi minőségű optimális feltételekkel. 

Ezekben az oktatóanyagokban végigvezeti egy .NET-konzolalkalmazást hozhat létre egy forgatókönyv irodaépület. Az épületen belül minden egyes emelet több emeleteken és a rendelkezik. A termek eszközöket tartalmazó olyan csatlakoztatott mozgásfelismerés, környezeti hőmérséklet és légi minőségét. 

Megtudhatja, hogyan replikálni a fizikai területek és a digitális objektumokként az épületben entitásokat az Azure digitális Twins használatával. Eszközeseményeket egy másik Konzolalkalmazás használatával fogjuk szimulálásához. Ezután megtudhatja, hogyan figyelheti az eseményeket, amelyek e fizikai területek és a közel valós időben entitások származnak. 

Egy irodai rendszergazda ezekkel az információkkal segíthet az épületben dolgozóknak az optimális feltételeknek megfelelő tárgyalótermek lefoglalásában. Az office-létesítményekben manager használhatja a telepítő a berendezése használati trendeket, és karbantartási célból visszaállni figyelése.

A sorozat első oktatóanyagában az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Helyezze üzembe a digitális Twins.
> * Az alkalmazás engedélyt adnia.
> * Módosítsa egy digitális Twins mintaalkalmazást.
> * Az épület kiépítése.


Ezek az oktatóanyagok ugyanazokat a mintákat használják és módosítják, mint a [szabad helyiségek keresését bemutató rövid útmutató](quickstart-view-occupancy-dotnet.md), amelyben megtalálhatja a fogalmak részletesebb leírását.


## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs Azure-fiókja, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- A .NET Core SDK-t. Az Azure digitális Twins minták ezekben az oktatóanyagokban használt nyelven írták C#. Mindenképpen telepítse [.NET Core SDK 2.1.403 verzió vagy újabb](https://www.microsoft.com/net/download) a fejlesztői gépen való létrehozásához és a minta futtatásához. Ellenőrizze, hogy a megfelelő verziója telepítve van a gépén futtatásával `dotnet --version` egy parancssori ablakban.

- [Visual Studio Code](https://code.visualstudio.com/) a mintakód vizsgálatához. 

<a id="deploy" />

## <a name="deploy-digital-twins"></a>A Digital Twins üzembe helyezése

Ebben a szakaszban a lépések segítségével hozzon létre egy új példányát az Azure digitális Twins szolgáltatás. Előfizetésenként csak egy példányban lehet létrehozni. Ha már rendelkezik egy futó, ugorjon a következő szakaszra. 

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]


<a id="permissions" />

## <a name="grant-permissions-to-your-app"></a>Engedélyek megadása az alkalmazásnak

Digitális Twins használ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) vezérlésére [olvasási/írási hozzáférés](../active-directory/develop/v1-permissions-and-consent.md) a szolgáltatáshoz. Minden olyan alkalmazás, amely a digitális Twins-példányhoz való csatlakozáshoz meg kell regisztrálni kell az Azure ad-ben. A jelen szakaszban ismertetett lépések bemutatják a mintaalkalmazás regisztrálásának módját.

Ha már rendelkezik az alkalmazás regisztrációját, a minta felhasználhatja azt. De mindenképpen tekintse át ezt a szakaszt, hogy az alkalmazásregisztráció megfelelően legyen konfigurálva.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]


## <a name="configure-the-digital-twins-sample"></a>A digitális Twins minta konfigurálásához

Ez a szakasz végigvezeti egy Azure digitális Twins alkalmazás, amely kommunikál a [digitális Twins REST API-k](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index). 

### <a name="download-the-sample"></a>A minta letöltése
Ha már letöltötte a [szabad helyiségek keresésére szolgáló rövid útmutató](quickstart-view-occupancy-dotnet.md) mintáit, kihagyhatja ezeket a lépéseket.

1. Töltse le a [digitális Twins .NET-minták](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip). 
2. Bontsa ki a tömörített mappát a gépen tartalmát. 

### <a name="explore-the-sample"></a>A minta vizsgálata
A kibontott minta mappában nyissa meg a fájlt **digital-twins-samples-csharp\digital-twins-samples.code-workspace** Visual Studio Code-ban. Ez két projektet tartalmaz: 

* Használhatja az eszközkiépítési minta **foglaltsága-quickstart** konfigurálására és kiosztására egy [térbeli intelligencia graph](concepts-objectmodel-spatialgraph.md#graph). Ez a diagram a fizikai szóközöket és azokat az erőforrásokat a digitális képe. Használja az [hálózatiobjektum-modellt](concepts-objectmodel-spatialgraph.md#model), amely megadja, hogy egy intelligens épület-objektumokat. Digitális Twins objektumok és a REST API-k teljes listáját a Microsoft [a REST API-dokumentáció](https://docs.westcentralus.azuresmartspaces.net/management/swagger) vagy a felügyeleti API URL-címe, amelyhez létrehozták [a példány](#deploy).

   Részletesebben is áttekinti a megtekintéséhez, hogyan kommunikál a digitális Twins-példány, hogy kezdhet az **src\actions** mappát. Ebben a mappában lévő fájlok ezekben az oktatóanyagokban használt parancsok végrehajtására:
    - A **provisionSample.cs** fájl bemutatja, hogyan építheti ki a térbeli grafikon.
    - A **getSpaces.cs** fájlt a kiépített tárolóhelyek adatainak beolvasása.
    - A **getAvailableAndFreshSpaces.cs** fájl lekérdezi egy felhasználó által definiált függvény nevű egyéni függvény eredménye.
    - A **createEndpoints.cs** fájl más szolgáltatásokkal interakcióba végpontokat hoz létre.

* A szimuláció minta **eszközkapcsolattal** szimulálja az adatokat, és elküldi azt az IoT hubnak, amely ki van építve a digitális Twins példány. Ez a példa a használni kívánt [a következő oktatóanyaggal, miután a térbeli graph üzembe](tutorial-facilities-udf.md#simulate). Ez a minta konfigurálásához érzékelő- és azonosítók lehet ugyanaz, mint a gráfhoz kiépítése fogja használni.

### <a name="configure-the-provisioning-sample"></a>A kiépítési minta konfigurálása
1. Nyisson meg egy parancsablakot, és lépjen a letöltött minta. Futtassa az alábbi parancsot:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Az alábbi parancs futtatásával állítsa vissza a függőségeket a mintaprojektben:

    ```cmd/sh
    dotnet restore
    ```

1. A Visual Studio Code-ban nyissa meg a **appSettings.json** fájlt a **foglaltsága-quickstart** projekt. Frissítse a következő értékeket:
   * **ClientId**: Adja meg az Azure AD-alkalmazás regisztrációjának alkalmazás Azonosítóját. Ezt az Azonosítót az szakaszban feljegyzett ahol Ön [Alkalmazásengedélyek beállítása](#permissions).
   * **Bérlő**: Adja meg a címtár-azonosító, a [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant). Ezt az Azonosítót az szakaszban is feljegyzett ahol, [Alkalmazásengedélyek beállítása](#permissions).
   * **BaseUrl**: Adja meg a Digital Twins-példány URL-címét. Az URL-cím lekéréséhez cserélje le az URL-ben lévő helyőrzőket a példány értékeire: _https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/_. Az URL-címet is beszerezheti a felügyeleti API URL-Címének a módosításával [a telepítésről szóló rész](#deploy). Cserélje le **swagger /** a **api/v1.0/**.

1. Minta használatával megvizsgálhatja digitális Twins szolgáltatások listájának megtekintéséhez. Futtassa az alábbi parancsot:

    ```cmd/sh
    dotnet run
    ```

<a id="provision-spaces" />

## <a name="understand-the-provisioning-process"></a>A kiépítési folyamat ismertetése
Ez a szakasz egy épület térbeli diagramjának kiépítését mutatja be. 

A Visual Studio Code-ban keresse meg a **foglaltsága-quickstart\src\actions** mappába és nyissa meg a **provisionSample.cs**. Figyelje meg az alábbi függvényt:

```csharp
public static async Task<IEnumerable<ProvisionResults.Space>> ProvisionSample(HttpClient httpClient, ILogger logger)
{
    IEnumerable<SpaceDescription> spaceCreateDescriptions;
    using (var r = new StreamReader("actions/provisionSample.yaml"))
    {
        spaceCreateDescriptions = await GetProvisionSampleTopology(r);
    }

    var results = await CreateSpaces(httpClient, logger, spaceCreateDescriptions, Guid.Empty);

    Console.WriteLine($"Completed Provisioning: {JsonConvert.SerializeObject(results, Formatting.Indented)}");

    return results;
}

```

Ez a funkció **provisionSample.yaml** ugyanabban a mappában. Nyissa meg a fájlt, és jegyezze fel a hierarchia irodaépület: *helyszín*, *emelet*, *terület*, és *termek*. A fizikai területek bármelyike tartalmazhat *eszközöket* és *érzékelőket*. Mindegyik bejegyzés rendelkezik egy előre meghatározott `type` &mdash;például emelet, szobában. 

A minta **yaml** fájl használó térbeli grafikonon jeleníti meg a `Default` digitális Twins hálózatiobjektum-modellt. Ez a modell biztosítja a legtöbb általános névvel. Általános névvel épület elegendőek. Példák a SensorDataType hőmérséklet, és a SpaceBlobType leképezése. Egy példa terület típus altípus FocusRoom, ahol ConferenceRoom és így tovább. 

Ha más típusú helyszínhez, például gyárhoz szeretne létrehozni térbeli diagramot, előfordulhat, hogy más objektummodellre lesz szüksége. Megtudhatja, mely modellek legyenek elérhetők a parancs futtatásával `dotnet run GetOntologies` a kiépítési mintában a parancssoron. 

A térbeli diagramjait és objektummodellt további információért olvassa el [ismertetése digitális Twins objektum modelleket és a térbeli intelligencia graph](concepts-objectmodel-spatialgraph.md). 

### <a name="modify-the-sample-spatial-graph"></a>A minta térbeli graph módosítása
A **provisionSample.yaml** fájl a következő csomópontokat tartalmazza:

- **erőforrások**: A `resources` csomópont létrehoz egy Azure IoT Hub-erőforrást, és a telepítő az eszközök közötti kommunikációt. Az IoT hub, a csomópont a gráfhoz, az összes eszközök és érzékelők a gráfban található kommunikálhat.  

- **spaces**: A Digital Twins-objektummodellben a `spaces` jelöli a fizikai helyeket. Minden egyes címtérhez tartozik egy `Type` &mdash;például a régió, a helyszín vagy az ügyfél&mdash;és a egy rövid `Name`. Tárolóhelyek más tárolóhelyek létrehozása a hierarchikus is tartozhat. A provisionSample.yaml fájl rendelkezik egy képzeletbeli épület térbeli grafikon. Vegye figyelembe a logikai típusú tárolóhelyek beágyazását `Floor` belül `Venue`, `Area` a egy emelet és `Room` csomópontok területen. 

- **devices**: A terek `devices`-elemeket tartalmazhatnak, amelyek érzékelőket kezelő fizikai vagy virtuális entitások. Egy eszköz lehet például a felhasználó telefonja, érzékelő podot Raspberry Pi vagy egy átjárót. Figyelje meg a mintában lévő képzeletbeli épületben, hogy a **Focus Room** nevű helyiség egy **Raspberry Pi 3 A1** eszközt tartalmaz. Minden eszközcsomópontot egyedi `hardwareId` azonosít, amely szoftveresen kötött a mintában. A minta tényleges éles környezethez való konfigurálásához cserélje le ezeket a környezetében lévő értékekre.  

- **érzékelő**: egy eszköz több tartalmazhat `sensors`. Azt is észleli, és rekord fizikai módosítása, például hőmérséklet mozgásban lévő adatoknak egyaránt és töltöttségi szint. Minden érzékelő-csomópontot egyedien azonosít egy `hardwareId`, amely itt szoftveresen kötött. Tényleges alkalmazás esetén cserélje le ezeket a telepítő az érzékelők egyedi azonosítói használatával. A provisionSample.yaml fájl rendelkezik két érzékelők rögzítése *mozgásban lévő adatoknak egyaránt* és *CarbonDioxide*. Ha a *hőmérséklet* érzékelőjét is hozzá szeretné adni, adja a következő sorokat a széndioxid-érzékelő sorai alá. Vegye figyelembe, hogy ezek szerepelnek provisionSample.yaml ellátva kibővített sorok formájában. Akkor is távolítsa el őket eltávolításával a `#` minden egyes sorban található karaktert. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Győződjön meg arról, hogy a `dataType` és `hardwareId` kulcsok összhangba kerüljenek az utasításokat, ez a kódrészlet felett. Azt is ellenőrizze, hogy a szerkesztő nem cserélte-e le a szóközöket tabulátorokra. 

Mentse és zárja be a provisionSample.yaml fájlt. A következő oktatóanyaggal fog további információk hozzáadása ehhez a fájlhoz, és hogyan építhet ki az Azure digitális Twins minta létrehozása.

> [!TIP]
> Megtekintheti és módosíthatja a térbeli graph használatával a [Azure digitális Twins Graph megjelenítő](https://github.com/Azure/azure-digital-twins-graph-viewer).


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, ezen a ponton felfedezése az Azure digitális Twins leállítására, nyugodtan ebben az oktatóanyagban létrehozott erőforrások törléséhez:

1. A bal oldali menüben lévő a [az Azure portal](http://portal.azure.com), jelölje be **összes erőforrás**, a digitális Twins erőforráscsoportot, válassza ki és **törlése**.
   
    > [!TIP]
    > Ha törli a digitális Twins-példány problémajegyek tapasztal, szolgáltatás frissítése lett állítva a javítás. Ismételje meg a példány törlése.

1. Ha szükséges, törölje a mintaalkalmazás a munkahelyi számítógépen. 


## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan valósíthat meg egy egyéni logikát megfigyelik a az épület minta, nyissa meg a következő oktatóanyag a sorozat: 
> [!div class="nextstepaction"]
> [Oktatóanyag: Az épület kiépítése és a munkakörülmények monitorozása](tutorial-facilities-udf.md)

