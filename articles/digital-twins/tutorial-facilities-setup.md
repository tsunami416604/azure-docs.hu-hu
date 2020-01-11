---
title: 'Oktatóanyag: előzetes verziójú környezet és térbeli gráf üzembe helyezése – Azure digitális Twins | Microsoft Docs'
description: Megtudhatja, hogyan helyezheti üzembe az Azure Digital Twins-példányait, és hogyan konfigurálhatja a térbeli erőforrásokat az oktatóanyag lépéseinek használatával.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: bf07a165b6ea933719eb06b6625a91033030a120
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895451"
---
# <a name="tutorial-deploy-azure-digital-twins-preview-and-configure-a-spatial-graph"></a>Oktatóanyag: az Azure digitális Twins előzetes verziójának üzembe helyezése és a térbeli gráf konfigurálása

Az Azure Digital ikrek előzetes verzió szolgáltatásával egy koherens térbeli rendszerbe rendezheti az embereket, a helyeket és az eszközöket. Ez az oktatóanyag azt mutatja be, hogyan használható az Azure Digital ikrek a helyiségek kihasználtságának észlelésére a hőmérséklet és a levegőminőség optimális feltételeivel. 

Ezek az oktatóanyagok végigvezetik a .NET-konzol alkalmazásán, hogy létrejöjjön egy Office-építés forgatókönyve. Az épületen belül több emelet és szoba található. A helyiségek olyan csatlakoztatott érzékelőkkel rendelkező eszközöket tartalmaznak, amelyek a mozgást, a környezeti hőmérsékletet és a levegőminőség minőségét figyelik. 

Megtudhatja, hogyan replikálhatja a fizikai területeket és entitásokat az épületben digitális objektumokként az Azure Digital Twins szolgáltatással. Az eszközök eseményeinek szimulálása egy másik konzolszoftver használatával végezhető el. Ezután megtudhatja, hogyan figyelheti a fizikai területekről és entitásokból származó eseményeket közel valós időben. 

Egy irodai rendszergazda ezekkel az információkkal segíthet az épületben dolgozóknak az optimális feltételeknek megfelelő tárgyalótermek lefoglalásában. Az Office-létesítmények kezelője használhatja a telepítőt a szobák használati trendjeinek beszerzésére, valamint a karbantartási célokra használt munkafeltételek figyelésére.

A sorozat első oktatóanyagában az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Digitális Twins üzembe helyezése.
> * Engedélyek megadása az alkalmazás számára.
> * Digitális Twins-minta alkalmazás módosítása.
> * Építheti ki az építést.

Ezek az oktatóanyagok ugyanazokat a mintákat használják és módosítják, mint a [szabad helyiségek keresését bemutató rövid útmutató](quickstart-view-occupancy-dotnet.md), amelyben megtalálhatja a fogalmak részletesebb leírását.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs Azure-fiókja, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- A .NET Core SDK. Az oktatóanyagokban használt Azure digitális Twins-mintákat itt C#lehet beírni. A minta létrehozásához és futtatásához győződjön meg arról, hogy a fejlesztői gépen [.net Core SDK 2.1.403 vagy újabb verzió](https://www.microsoft.com/net/download) van telepítve. Győződjön meg arról, hogy a megfelelő verzió telepítve van a gépen a `dotnet --version` parancssori futtatásával.

- [Visual Studio Code](https://code.visualstudio.com/) a mintakód vizsgálatához. 

## <a name="deploy-digital-twins"></a>A Digital Twins üzembe helyezése

Az ebben a szakaszban ismertetett lépések segítségével hozza létre az Azure Digital Twins szolgáltatás egy új példányát. Egy előfizetéshez csak egy példány hozható létre. Ugorjon a következő szakaszra, ha már fut egy. 

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="grant-permissions-to-your-app"></a>Engedélyek megadása az alkalmazásnak

A digitális Twins [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure ad) használatával vezérli a szolgáltatás [írási/olvasási hozzáférését](../active-directory/develop/v1-permissions-and-consent.md) . Minden olyan alkalmazást, amely a digitális Twins-példánnyal való kapcsolódásra van szüksége, regisztrálni kell az Azure AD-ben. A jelen szakaszban ismertetett lépések bemutatják a mintaalkalmazás regisztrálásának módját.

Ha már rendelkezik alkalmazás-regisztrációval, újra felhasználhatja azt a mintához. De mindenképpen tekintse át ezt a szakaszt, hogy az alkalmazásregisztráció megfelelően legyen konfigurálva.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="configure-the-digital-twins-sample"></a>A digitális ikrek mintájának konfigurálása

Ez a szakasz végigvezeti egy olyan Azure digitális Twins-alkalmazáson, amely a [digitális ikrek REST API](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index)-kkal kommunikál. 

### <a name="download-the-sample"></a>A minta letöltése

Ha már letöltötte a [szabad helyiségek keresésére szolgáló rövid útmutató](quickstart-view-occupancy-dotnet.md) mintáit, kihagyhatja ezeket a lépéseket.

1. Töltse le a [digitális Twins .net-mintákat](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip).
2. Bontsa ki a zip-mappa tartalmát a gépen.

### <a name="explore-the-sample"></a>A minta vizsgálata

A kibontott minta mappában Nyissa meg a **Digital-Twins-Samples-csharp\digital-Twins-Samples.code-Workspace** fájlt a Visual Studio Code-ban. Ez két projektet tartalmaz:

* A kiépítési minta **kihasználtsága-** rövid útmutató segítségével konfigurálhatja és kiépítheti a [térbeli intelligencia gráfját](concepts-objectmodel-spatialgraph.md#digital-twins-object-models). Ez a gráf a fizikai tárhelyek és a bennük lévő erőforrások digitalizált képe. Egy [objektummodell](concepts-objectmodel-spatialgraph.md#digital-twins-object-models)használatával határozza meg az intelligens épületek objektumait. A digitális Twins-objektumok és a REST API-k teljes listájáért látogasson el [erre a REST API dokumentációra](https://docs.westcentralus.azuresmartspaces.net/management/swagger) vagy a [példányhoz](#deploy-digital-twins)létrehozott felügyeleti API URL-címére.

   A minta megismeréséhez, hogy megtudja, hogyan kommunikál a digitális Twins-példánnyal, elkezdheti a **src\actions** mappát. A mappában található fájlok implementálják az alábbi oktatóanyagokban használt parancsokat:
    - A **provisionSample.cs** -fájl a térbeli gráf kiépítésének módját mutatja be.
    - A **getSpaces.cs** -fájl beolvassa a kiépített szóközökkel kapcsolatos információkat.
    - A **getAvailableAndFreshSpaces.cs** fájl egy felhasználó által definiált függvény nevű egyéni függvény eredményét jeleníti meg.
    - A **createEndpoints.cs** fájl végpontokat hoz létre a más szolgáltatásokkal való kommunikációhoz.

* A szimulációs minta **eszköz kapcsolata** szimulálja az érzékelő adatait, és elküldi azt a digitális Twins-példányhoz kiépített IoT hubhoz. Ezt a mintát a [következő oktatóanyagban fogja használni a térbeli gráf kiépítése után](tutorial-facilities-udf.md#simulate-sensor-data). A minta konfigurálásához használt érzékelőnek és eszköz-azonosítóknak meg kell egyezniük a gráf kiépítéséhez használt érzékelőkkel és azonosítókkal.

### <a name="configure-the-provisioning-sample"></a>A kiépítési minta konfigurálása

1. Nyisson meg egy parancssorablakot, és nyissa meg a letöltött mintát. Futtassa az alábbi parancsot:

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

1. Tekintse át a minta használatával feltárni kívánt digitális Twins-funkciók listáját. Futtassa az alábbi parancsot:

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

A minta **YAML** -fájl egy térbeli gráfot mutat be, amely a `Default` digitális ikrek objektummodell-modelljét használja. Ez a modell általános neveket biztosít a legtöbb típushoz. Az általános nevek elegendőek egy adott épületben. Ilyenek például a SensorDataType hőmérséklete, és a SpaceBlobType leképezése. A szóköz típusa például a FocusRoom, a ConferenceRoom és más típusú altípusok. 

Ha más típusú helyszínhez, például gyárhoz szeretne létrehozni térbeli diagramot, előfordulhat, hogy más objektummodellre lesz szüksége. Megtudhatja, hogy mely modellek használhatók a kiépítési minta parancssorában `dotnet run GetOntologies` parancs futtatásával. 

A térbeli gráfokkal és az Object modellekkel kapcsolatos további információkért olvassa el a [digitális Twins-objektumok modelljeinek és a térbeli intelligencia gráfjának megismerését](concepts-objectmodel-spatialgraph.md)ismertető témakört.

### <a name="modify-the-sample-spatial-graph"></a>A minta térbeli diagramjának módosítása

A **provisionSample. YAML** fájl a következő csomópontokat tartalmazza:

- **erőforrások**: a `resources` csomópont egy Azure IoT hub-erőforrást hoz létre a telepítőben lévő eszközökkel való kommunikációhoz. A Graph legfelső csomópontján lévő IoT hub képes kommunikálni a gráf összes eszközével és érzékelővel.  

- **spaces**: A Digital Twins-objektummodellben a `spaces` jelöli a fizikai helyeket. Minden területhez tartozik egy `Type`&mdash;például a régió, a helyszín vagy az ügyfél&mdash;és egy barátságos `Name`. A szóközök más helyekhez is tartozhatnak, hierarchikus struktúra létrehozásával. A provisionSample. YAML fájl egy képzeletbeli épületben egy térbeli gráftal rendelkezik. Figyelje meg, hogy `Floor` típusú szóközök logikai beágyazása `Venue`, a padlón `Area`, és egy adott területen `Room` csomópontok között. 

- **devices**: A terek `devices`-elemeket tartalmazhatnak, amelyek érzékelőket kezelő fizikai vagy virtuális entitások. Előfordulhat például, hogy egy eszköz lehet egy felhasználó telefonja, egy málna PI szenzor Pod vagy egy átjáró. Figyelje meg a mintában lévő képzeletbeli épületben, hogy a **Focus Room** nevű helyiség egy **Raspberry Pi 3 A1** eszközt tartalmaz. Minden eszközcsomópontot egyedi `hardwareId` azonosít, amely szoftveresen kötött a mintában. A minta tényleges éles környezethez való konfigurálásához cserélje le ezeket a környezetében lévő értékekre.  

- **érzékelők**: az eszközök több `sensors`is tartalmazhatnak. Képesek a fizikai változások, például a hőmérséklet, a mozgás és az akkumulátor szintjének észlelésére és rögzítésére. Minden érzékelő csomópontot egyedileg azonosít egy `hardwareId`, hardcoded itt. Egy tényleges alkalmazás esetében cserélje le ezeket az érzékelők egyedi azonosítóinak használatával a telepítőben. A provisionSample. YAML fájl két érzékelővel rendelkezik a *Motion* és a *CarbonDioxide*rögzítéséhez. Ha a *hőmérséklet* érzékelőjét is hozzá szeretné adni, adja a következő sorokat a széndioxid-érzékelő sorai alá. Ezeket a provisionSample. YAML a megjegyzésekkel ellátható sorokban biztosítjuk. Az egyes sorok elején lévő `#` karakter eltávolításával törölheti a megjegyzéseket. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Győződjön meg arról, hogy a `dataType` és a `hardwareId` kulcsok a kódrészlet fölötti utasításokkal vannak igazítva. Azt is ellenőrizze, hogy a szerkesztő nem cserélte-e le a szóközöket tabulátorokra. 

Mentse és zárjuk be a provisionSample. YAML fájlt. A következő oktatóanyagban további információkat adhat hozzá a fájlhoz, majd kiépítheti az Azure-beli digitális Twins-minta felépítését.

> [!TIP]
> A térbeli gráfot az [Azure Digital Twins Graph Viewer](https://github.com/Azure/azure-digital-twins-graph-viewer)használatával tekintheti meg és módosíthatja.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezen a ponton szeretné leállítani az Azure digitális Twins felfedezését, nyugodtan törölheti az ebben az oktatóanyagban létrehozott erőforrásokat:

1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza a **minden erőforrás**elemet, válassza ki a digitális Twins-erőforráscsoportot, és válassza a **Törlés**lehetőséget.

    > [!TIP]
    > Ha hibát észlelt a digitális Twins-példány törlésével kapcsolatban, a javítás a szolgáltatásban frissült. Próbálkozzon újra a példány törlésével.

1. Ha szükséges, törölje a minta alkalmazást a munkahelyi gépen.

## <a name="next-steps"></a>Következő lépések

Ha meg szeretné tudni, hogyan implementálhat egy egyéni logikát a minta-összeállítás feltételeinek figyeléséhez, ugorjon az adatsorozat következő oktatóanyagára: 
> [!div class="nextstepaction"]
> [Oktatóanyag: Az épület kiépítése és a munkakörülmények monitorozása](tutorial-facilities-udf.md)