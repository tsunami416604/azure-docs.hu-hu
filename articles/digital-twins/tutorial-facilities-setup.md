---
title: 'Oktatóanyag: előzetes verziójú környezet és térbeli gráf üzembe helyezése – Azure digitális Twins | Microsoft Docs'
description: Ismerje meg, hogyan helyezheti üzembe az Azure digitális Twins-példány, és a térbeli erőforrások konfigurálása az ebben az oktatóanyagban szereplő lépések segítségével.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 11/12/2019
ms.openlocfilehash: 20174a4eafb4e72fb62eeff6df2d129b91016b9e
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383027"
---
# <a name="tutorial-deploy-azure-digital-twins-preview-and-configure-a-spatial-graph"></a>Oktatóanyag: az Azure digitális Twins előzetes verziójának üzembe helyezése és a térbeli gráf konfigurálása

Az Azure Digital ikrek előzetes verzió szolgáltatásával egy koherens térbeli rendszerbe rendezheti az embereket, a helyeket és az eszközöket. Ez az oktatóanyag-sorozat bemutatja, hogyan használhatja az Azure digitális Twins észleléséhez helyiségben foglaltsága hőmérséklet és a légi minőségű optimális feltételekkel. 

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

- A .NET Core SDK-t. Az Azure digitális Twins minták ezekben az oktatóanyagokban használt nyelven írták C#. A minta létrehozásához és futtatásához győződjön meg arról, hogy a fejlesztői gépen [.net Core SDK 2.1.403 vagy újabb verzió](https://www.microsoft.com/net/download) van telepítve. Győződjön meg arról, hogy a megfelelő verzió telepítve van a gépen a `dotnet --version` parancssori futtatásával.

- [Visual Studio Code](https://code.visualstudio.com/) a mintakód vizsgálatához. 

## <a name="deploy-digital-twins"></a>A Digital Twins üzembe helyezése

Ebben a szakaszban a lépések segítségével hozzon létre egy új példányát az Azure digitális Twins szolgáltatás. Előfizetésenként csak egy példányban lehet létrehozni. Ha már rendelkezik egy futó, ugorjon a következő szakaszra. 

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="grant-permissions-to-your-app"></a>Engedélyek megadása az alkalmazásnak

A digitális Twins [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure ad) használatával vezérli a szolgáltatás [írási/olvasási hozzáférését](../active-directory/develop/v1-permissions-and-consent.md) . Minden olyan alkalmazás, amely a digitális Twins-példányhoz való csatlakozáshoz meg kell regisztrálni kell az Azure ad-ben. A jelen szakaszban ismertetett lépések bemutatják a mintaalkalmazás regisztrálásának módját.

Ha már rendelkezik az alkalmazás regisztrációját, a minta felhasználhatja azt. De mindenképpen tekintse át ezt a szakaszt, hogy az alkalmazásregisztráció megfelelően legyen konfigurálva.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="configure-the-digital-twins-sample"></a>A digitális Twins minta konfigurálásához

Ez a szakasz végigvezeti egy olyan Azure digitális Twins-alkalmazáson, amely a [digitális ikrek REST API](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index)-kkal kommunikál. 

### <a name="download-the-sample"></a>A minta letöltése

Ha már letöltötte a [szabad helyiségek keresésére szolgáló rövid útmutató](quickstart-view-occupancy-dotnet.md) mintáit, kihagyhatja ezeket a lépéseket.

1. Töltse le a [digitális Twins .net-mintákat](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip).
2. Bontsa ki a tömörített mappát a gépen tartalmát.

### <a name="explore-the-sample"></a>A minta vizsgálata

A kibontott minta mappában Nyissa meg a **Digital-Twins-Samples-csharp\digital-Twins-Samples.code-Workspace** fájlt a Visual Studio Code-ban. Ez két projektet tartalmaz:

* A kiépítési minta **kihasználtsága-** rövid útmutató segítségével konfigurálhatja és kiépítheti a [térbeli intelligencia gráfját](concepts-objectmodel-spatialgraph.md#digital-twins-object-models). Ez a diagram a fizikai szóközöket és azokat az erőforrásokat a digitális képe. Egy [objektummodell](concepts-objectmodel-spatialgraph.md#digital-twins-object-models)használatával határozza meg az intelligens épületek objektumait. A digitális Twins-objektumok és a REST API-k teljes listájáért látogasson el [erre a REST API dokumentációra](https://docs.westcentralus.azuresmartspaces.net/management/swagger) vagy a [példányhoz](#deploy-digital-twins)létrehozott felügyeleti API URL-címére.

   Ha szeretné megtekinteni a mintát, hogy megtudja, hogyan kommunikál a digitális Twins-példánnyal, elkezdheti a **src\actions** mappát. Ebben a mappában lévő fájlok ezekben az oktatóanyagokban használt parancsok végrehajtására:
    - A **provisionSample.cs** -fájl a térbeli gráf kiépítésének módját mutatja be.
    - A **getSpaces.cs** -fájl beolvassa a kiépített szóközökkel kapcsolatos információkat.
    - A **getAvailableAndFreshSpaces.cs** fájl egy felhasználó által definiált függvény nevű egyéni függvény eredményét jeleníti meg.
    - A **createEndpoints.cs** fájl végpontokat hoz létre a más szolgáltatásokkal való kommunikációhoz.

* A szimulációs minta **eszköz kapcsolata** szimulálja az érzékelő adatait, és elküldi azt a digitális Twins-példányhoz kiépített IoT hubhoz. Ezt a mintát a [következő oktatóanyagban fogja használni a térbeli gráf kiépítése után](tutorial-facilities-udf.md#simulate-sensor-data). Ez a minta konfigurálásához érzékelő- és azonosítók lehet ugyanaz, mint a gráfhoz kiépítése fogja használni.

### <a name="configure-the-provisioning-sample"></a>A kiépítési minta konfigurálása

1. Nyisson meg egy parancsablakot, és lépjen a letöltött minta. Futtassa az alábbi parancsot:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Az alábbi parancs futtatásával állítsa vissza a függőségeket a mintaprojektben:

    ```cmd/sh
    dotnet restore
    ```

1. A Visual Studio Code-ban nyissa meg a [appSettings. JSON](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) fájlt a **Foglaltság-Gyorsindítás** projektben. Frissítse a következő értékeket:
   * **ClientId**: adja meg az Azure ad-alkalmazás regisztrációjának alkalmazás-azonosítóját. Ezt az azonosítót az [alkalmazás engedélyeinek beállítása](#grant-permissions-to-your-app)című szakaszban jegyezte fel.
   * **Bérlő**: adja meg az [Azure ad-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)címtár-azonosítóját. Ezt az azonosítót az [alkalmazás engedélyeinek beállítása](#grant-permissions-to-your-app)szakaszban is megjegyezte.
   * **BaseUrl**: Adja meg a Digital Twins-példány URL-címét. Az URL-cím lekéréséhez cserélje le az URL-cím helyőrzőit a példányának értékeire: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Ezt az URL-címet úgy is megtekintheti, hogy módosítja a felügyeleti API URL-címét [az üzembe helyezés szakaszból](#deploy-digital-twins). Cserélje le a **hencegés/** **API/v 1.0/** .

1. Minta használatával megvizsgálhatja digitális Twins szolgáltatások listájának megtekintéséhez. Futtassa az alábbi parancsot:

    ```cmd/sh
    dotnet run
    ```

## <a name="understand-the-provisioning-process"></a>A kiépítési folyamat ismertetése

Ez a szakasz egy épület térbeli diagramjának kiépítését mutatja be.

A Visual Studio Code-ban keresse meg a **Occupancy-quickstart\src\actions** mappát, és nyissa meg a **provisionSample.cs**fájlt. Figyelje meg az alábbi függvényt:

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

Ez a függvény a [provisionSample. YAML](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) -t használja ugyanabban a mappában. Nyissa meg ezt a fájlt, és jegyezze fel az irodaház hierarchiáját: *helyszín*, a *padló*, a *terület*és a *szobák*. A fizikai területek bármelyike tartalmazhat *eszközöket* és *érzékelőket*. Mindegyik bejegyzéshez előre definiált `type`&mdash;tartozik, például a padló, a szoba.

A minta **YAML** -fájl egy térbeli gráfot mutat be, amely a `Default` digitális ikrek objektummodell-modelljét használja. Ez a modell biztosítja a legtöbb általános névvel. Általános névvel épület elegendőek. Példák a SensorDataType hőmérséklet, és a SpaceBlobType leképezése. Egy példa terület típus altípus FocusRoom, ahol ConferenceRoom és így tovább. 

Ha más típusú helyszínhez, például gyárhoz szeretne létrehozni térbeli diagramot, előfordulhat, hogy más objektummodellre lesz szüksége. Megtudhatja, hogy mely modellek használhatók a kiépítési minta parancssorában `dotnet run GetOntologies` parancs futtatásával. 

A térbeli gráfokkal és az Object modellekkel kapcsolatos további információkért olvassa el a [digitális Twins-objektumok modelljeinek és a térbeli intelligencia gráfjának megismerését](concepts-objectmodel-spatialgraph.md)ismertető témakört.

### <a name="modify-the-sample-spatial-graph"></a>A minta térbeli graph módosítása

A **provisionSample. YAML** fájl a következő csomópontokat tartalmazza:

- **erőforrások**: a `resources` csomópont egy Azure IoT hub-erőforrást hoz létre a telepítőben lévő eszközökkel való kommunikációhoz. Az IoT hub, a csomópont a gráfhoz, az összes eszközök és érzékelők a gráfban található kommunikálhat.  

- **spaces**: A Digital Twins-objektummodellben a `spaces` jelöli a fizikai helyeket. Minden területhez tartozik egy `Type`&mdash;például a régió, a helyszín vagy az ügyfél&mdash;és egy barátságos `Name`. Tárolóhelyek más tárolóhelyek létrehozása a hierarchikus is tartozhat. A provisionSample.yaml fájl rendelkezik egy képzeletbeli épület térbeli grafikon. Figyelje meg, hogy `Floor` típusú szóközök logikai beágyazása `Venue`, a padlón `Area`, és egy adott területen `Room` csomópontok között. 

- **devices**: A terek `devices`-elemeket tartalmazhatnak, amelyek érzékelőket kezelő fizikai vagy virtuális entitások. Egy eszköz lehet például a felhasználó telefonja, érzékelő podot Raspberry Pi vagy egy átjárót. Figyelje meg a mintában lévő képzeletbeli épületben, hogy a **Focus Room** nevű helyiség egy **Raspberry Pi 3 A1** eszközt tartalmaz. Minden eszközcsomópontot egyedi `hardwareId` azonosít, amely szoftveresen kötött a mintában. A minta tényleges éles környezethez való konfigurálásához cserélje le ezeket a környezetében lévő értékekre.  

- **érzékelők**: az eszközök több `sensors`is tartalmazhatnak. Azt is észleli, és rekord fizikai módosítása, például hőmérséklet mozgásban lévő adatoknak egyaránt és töltöttségi szint. Minden érzékelő-csomópontot egyedien azonosít egy `hardwareId`, amely itt szoftveresen kötött. Tényleges alkalmazás esetén cserélje le ezeket a telepítő az érzékelők egyedi azonosítói használatával. A provisionSample. YAML fájl két érzékelővel rendelkezik a *Motion* és a *CarbonDioxide*rögzítéséhez. Ha a *hőmérséklet* érzékelőjét is hozzá szeretné adni, adja a következő sorokat a széndioxid-érzékelő sorai alá. Vegye figyelembe, hogy ezek szerepelnek provisionSample.yaml ellátva kibővített sorok formájában. Az egyes sorok elején lévő `#` karakter eltávolításával törölheti a megjegyzéseket. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Győződjön meg arról, hogy a `dataType` és a `hardwareId` kulcsok a kódrészlet fölötti utasításokkal vannak igazítva. Azt is ellenőrizze, hogy a szerkesztő nem cserélte-e le a szóközöket tabulátorokra. 

Mentse és zárja be a provisionSample.yaml fájlt. A következő oktatóanyaggal fog további információk hozzáadása ehhez a fájlhoz, és hogyan építhet ki az Azure digitális Twins minta létrehozása.

> [!TIP]
> A térbeli gráfot az [Azure Digital Twins Graph Viewer](https://github.com/Azure/azure-digital-twins-graph-viewer)használatával tekintheti meg és módosíthatja.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, ezen a ponton felfedezése az Azure digitális Twins leállítására, nyugodtan ebben az oktatóanyagban létrehozott erőforrások törléséhez:

1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza a **minden erőforrás**elemet, válassza ki a digitális Twins-erőforráscsoportot, és válassza a **Törlés**lehetőséget.

    > [!TIP]
    > Ha törli a digitális Twins-példány problémajegyek tapasztal, szolgáltatás frissítése lett állítva a javítás. Ismételje meg a példány törlése.

1. Ha szükséges, törölje a mintaalkalmazás a munkahelyi számítógépen.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan valósíthat meg egy egyéni logikát megfigyelik a az épület minta, nyissa meg a következő oktatóanyag a sorozat: 
> [!div class="nextstepaction"]
> [Oktatóanyag: Az épület kiépítése és a munkakörülmények monitorozása](tutorial-facilities-udf.md)