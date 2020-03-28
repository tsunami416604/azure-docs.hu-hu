---
title: 'Oktatóanyag: Előzetes környezet és térbeli grafikon üzembe helyezése – Azure Digital Twins| Microsoft dokumentumok'
description: Ismerje meg, hogyan telepítheti az Azure Digital Twins példányát, és konfigurálhatja a térbeli erőforrásokat az oktatóanyag lépéseivel.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: 878b64fe6dd491adbb61c4c74cf4a5fc039858cd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79371409"
---
# <a name="tutorial-deploy-azure-digital-twins-preview-and-configure-a-spatial-graph"></a>Oktatóanyag: Az Azure Digital Twins előzetes verzió üzembe helyezése és térbeli grafikon konfigurálása

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

Az Azure Digital Twins Preview szolgáltatás segítségével összehozhatja az embereket, helyeket és eszközöket egy koherens térbeli rendszerben. Ez az oktatóanyag-sorozat bemutatja, hogyan használhatja az Azure Digital Twins segítségével a szobakihasználtság észleléséhez optimális hőmérsékleti és levegőminőségi feltételek mellett. 

Ezek az oktatóanyagok végigvezetik a .NET konzolalkalmazáson, hogy egy irodaépület forgatókönyvét hozhassanak létre. Az épület több szintes, és minden emeleten szobák találhatók. A szobákban csatlakoztatott érzékelőkkel ellátott eszközök találhatók, amelyek érzékelik a mozgást, a környezeti hőmérsékletet és a levegőminőséget. 

Megtudhatja, hogyan replikálhatja a fizikai területeket és entitásokat az épületben digitális objektumként az Azure Digital Twins szolgáltatás használatával. Az eszközeseményeket egy másik konzolalkalmazás használatával fogja szimulálni. Ezután megtudhatja, hogyan figyelheti az eseményeket, amelyek ezekből a fizikai területekről és entitásokból származnak közel valós időben. 

Egy irodai rendszergazda ezekkel az információkkal segíthet az épületben dolgozóknak az optimális feltételeknek megfelelő tárgyalótermek lefoglalásában. Az irodahelyiségek vezetője használhatja a beállítást, hogy a szobák használati trendjeit megkapja, és karbantartási célokra figyelemmel kísérje a munkakörülményeket.

A sorozat első oktatóanyagában az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Digitális ikrek telepítése.
> * Engedélyek megadása az alkalmazáshoz.
> * Digitális Twins mintaalkalmazás módosítása.
> * Építsd ki az épületed.

Ezek az oktatóanyagok ugyanazokat a mintákat használják és módosítják, mint a [szabad helyiségek keresését bemutató rövid útmutató](quickstart-view-occupancy-dotnet.md), amelyben megtalálhatja a fogalmak részletesebb leírását.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-fiókkal, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

- A .NET core SDK. Az ezekben az oktatóanyagokban használt Azure Digital Twins-minták C#-ban vannak megírva. Győződjön meg arról, hogy a [.NET Core SDK 2.1.403-as vagy újabb verzióját](https://www.microsoft.com/net/download) telepíti a fejlesztői számítógépre a minta létrehozásához és futtatásához. Ellenőrizze, hogy a megfelelő verzió van-e telepítve a számítógépre egy parancsablakban való futtatással. `dotnet --version`

- [Visual Studio Code](https://code.visualstudio.com/) a mintakód vizsgálatához. 

## <a name="deploy-digital-twins"></a>A Digital Twins üzembe helyezése

Ebben a szakaszban található lépéseket az Azure Digital Twins szolgáltatás új példányának létrehozásához használja. Előfizetésenként csak egy példány hozható létre. Ugrás a következő szakaszra, ha már fut egy. 

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="grant-permissions-to-your-app"></a>Engedélyek megadása az alkalmazásnak

A Digital Twins [az Azure Active Directoryt](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) használja a szolgáltatás [olvasási/írási hozzáférésének](../active-directory/develop/v2-permissions-and-consent.md) szabályozásához. Minden olyan alkalmazás, amely nek csatlakoznia kell a digitális twins-példány regisztrálnia kell az Azure AD-vel. A jelen szakaszban ismertetett lépések bemutatják a mintaalkalmazás regisztrálásának módját.

Ha már rendelkezik alkalmazásregisztrációval, újból felhasználhatja azt a mintához. De mindenképpen tekintse át ezt a szakaszt, hogy az alkalmazásregisztráció megfelelően legyen konfigurálva.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="configure-the-digital-twins-sample"></a>A Digital Twins minta konfigurálása

Ez a szakasz bemutatja az Azure Digital Twins alkalmazást, amely kommunikál a [Digital Twins REST API-kkal.](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index) 

### <a name="download-the-sample"></a>A minta letöltése

Ha már letöltötte a [szabad helyiségek keresésére szolgáló rövid útmutató](quickstart-view-occupancy-dotnet.md) mintáit, kihagyhatja ezeket a lépéseket.

1. Töltse le a [Digital Twins .NET mintákat](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip).
2. Bontsa ki a zip mappa tartalmát a gépen.

### <a name="explore-the-sample"></a>A minta vizsgálata

A kicsomagolt mintamappában nyissa meg a **digital-twins-samples-csharp\digital-twins-samples.code-workspace** fájlt a Visual Studio Code-ban. Ez két projektet tartalmaz:

* A kiépítési **minta kihasználtsági gyorsindítással** konfigurálhatja és kiépítheti a [térintelligencia-grafikont.](concepts-objectmodel-spatialgraph.md#digital-twins-object-models) Ez a grafikon a fizikai terek és a bennük lévő erőforrások digitalizált képe. [Objektummodellt](concepts-objectmodel-spatialgraph.md#digital-twins-object-models)használ, amely egy intelligens épület objektumait határozza meg. A Digital Twins-objektumok és REST API-k teljes listájáért nyissa meg [ezt a REST API-dokumentációt](https://docs.westcentralus.azuresmartspaces.net/management/swagger) vagy a [példányhoz](#deploy-digital-twins) létrehozott felügyeleti API URL-címét.

   A minta feltárásához, hogy hogyan kommunikál a digitális twins példány, kezdheti a **src\actions** mappát. Az ebben a mappában található fájlok az alábbi oktatóanyagokban használt parancsokat valósítják meg:
    - A **provisionSample.cs** fájl bemutatja, hogyan kell kiépíteni a térbeli grafikon.
    - A **getSpaces.cs** fájl információt kap a kiépített terekről.
    - A **getAvailableAndFreshSpaces.cs** fájl egy felhasználó által definiált függvénynek nevezett egyéni függvény eredményeit kapja meg.
    - A **createEndpoints.cs** fájl végpontokat hoz létre, hogy más szolgáltatásokkal kommunikáljon.

* A szimulációs minta **eszköz-kapcsolat** szimulálja az érzékelő adatait, és elküldi azokat az IoT hub, amely ki van építve a digitális Twins-példány. Ezt a mintát [a következő oktatóanyagban fogja használni, miután kiépítette a térbeli grafikont.](tutorial-facilities-udf.md#simulate-sensor-data) A minta konfigurálásához használt érzékelő- és eszközazonosítóknak meg kell egyeznie a diagram kiépítéséhez használt azonosítókkal.

### <a name="configure-the-provisioning-sample"></a>A kiépítési minta konfigurálása

1. Nyisson meg egy parancsablakot, és lépjen a letöltött mintára. Futtassa az alábbi parancsot:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Az alábbi parancs futtatásával állítsa vissza a függőségeket a mintaprojektben:

    ```cmd/sh
    dotnet restore
    ```

1. A Visual Studio-kódban nyissa meg az [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) fájlt az **foglaltsági gyorsindítási** projektben. Frissítse a következő értékeket:
   * **ClientId:** Adja meg az Azure AD-alkalmazás regisztrációjának alkalmazásazonosítóját. Ezt az azonosítót abban a szakaszban jegyezte fel, ahol [az alkalmazásengedélyeket beállította.](#grant-permissions-to-your-app)
   * **Bérlő**: Adja meg az [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)címtárazonosítóját. Ezt az azonosítót abban a szakaszban is megjegyezte, ahol [az alkalmazásengedélyeket beállította.](#grant-permissions-to-your-app)
   * **BaseUrl**: Adja meg a Digital Twins-példány URL-címét. Az URL-cím lekéréséhez cserélje le az URL-ben lévő helyőrzőket a példány értékeire: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Ezt az URL-címet úgy is beszerezheti, hogy módosítja a Felügyeleti API URL-címét [a központi telepítési szakaszból.](#deploy-digital-twins) Cserélje le **a swagger/** -ot **api/v1.0/ kifejezésre.**

1. Tekintse át a digitális twins funkciók listáját, amelyeket a minta használatával fedezhet fel. Futtassa az alábbi parancsot:

    ```cmd/sh
    dotnet run
    ```

## <a name="understand-the-provisioning-process"></a>A kiépítési folyamat megismerése

Ez a szakasz egy épület térbeli diagramjának kiépítését mutatja be.

A Visual Studio-kód ban keresse meg a **kihasználtsági gyorsindítás\src\actions** mappát, és nyissa meg a fájlt **provisionSample.cs.** Figyelje meg az alábbi függvényt:

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

Ez a függvény a [provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) fájlt használja ugyanabban a mappában. Nyissa meg ezt a fájlt, és jegyezze fel egy irodaépület hierarchiáját: *Helyszín,* *Emelet*, *Terület*és *Szobák*. A fizikai területek bármelyike tartalmazhat *eszközöket* és *érzékelőket*. Minden bejegyzéshez előre `type` &mdash;meghatározott bejegyzés van, például: Emelet, Szoba.

A **minta yaml** fájl egy `Default` térbeli grafikont jelenít meg, amely a Digital Twins objektummodellt használja. Ez a modell a legtöbb típushoz általános neveket biztosít. A generikus nevek elegendőek egy épülethez. Ilyenek például a SensorDataType hőmérséklete és a SpaceBlobType leképezése. A területtípusa például a Room with subtypes FocusRoom, ConferenceRoom, és így tovább. 

Ha más típusú helyszínhez, például gyárhoz szeretne létrehozni térbeli diagramot, előfordulhat, hogy más objektummodellre lesz szüksége. Megtudhatja, hogy mely modellek érhetők el `dotnet run GetOntologies` a használatra, ha futtatja a parancsot a parancs sorában a létesítési minta. 

A térbeli grafikonokkal és objektummodellekkel kapcsolatos további információkért olvassa el [a Digitális twins objektummodellek és a térintelligencia-grafikon ismertetése című témakört.](concepts-objectmodel-spatialgraph.md)

### <a name="modify-the-sample-spatial-graph"></a>A minta térbeli diagramjának módosítása

A **provisionSample.yaml** fájl a következő csomópontokat tartalmazza:

- **erőforrások:** `resources` A csomópont létrehoz egy Azure IoT Hub-erőforrást a beállításban lévő eszközökkel való kommunikációhoz. A diagram gyökércsomópontján lévő IoT-központ képes kommunikálni a diagram összes eszközével és érzékelőjével.  

- **spaces**: A Digital Twins-objektummodellben a `spaces` jelöli a fizikai helyeket. Minden térrendelkezik `Type` &mdash;például régióval,&mdash;helyszínnel `Name`vagy ügyféllel, és egy barátságos . A szóközök más szóközökhöz is tartozhatnak, hierarchikus struktúrát hozva létre. A provisionSample.yaml fájl egy képzeletbeli épület térbeli diagramjával rendelkezik. Figyelje meg a szövegterek `Floor` `Venue`logikai beágyazását `Area` a padlóba és `Room` a csomópontokba egy adott területen. 

- **devices**: A terek `devices`-elemeket tartalmazhatnak, amelyek érzékelőket kezelő fizikai vagy virtuális entitások. Például egy eszköz lehet egy felhasználó telefon, egy Raspberry Pi érzékelő pod, vagy egy átjáró. Figyelje meg a mintában lévő képzeletbeli épületben, hogy a **Focus Room** nevű helyiség egy **Raspberry Pi 3 A1** eszközt tartalmaz. Minden eszközcsomópontot egyedi `hardwareId` azonosít, amely szoftveresen kötött a mintában. A minta tényleges éles környezethez való konfigurálásához cserélje le ezeket a környezetében lévő értékekre.  

- **érzékelők**: A készülék `sensors`több . Képesek észlelni és rögzíteni a fizikai változásokat, például a hőmérsékletet, a mozgást és az akkumulátor töltöttségi szintjét. Minden érzékelő-csomópontot egyedien azonosít egy `hardwareId`, amely itt szoftveresen kötött. Egy tényleges alkalmazás esetén cserélje ki ezeket a beállításokban lévő érzékelők egyedi azonosítóinak használatával. A provisionSample.yaml fájl két érzékelőt tartalmaz a *mozgás* és a *széndioxid* értékének rögzítéséhez. Ha a *hőmérséklet* érzékelőjét is hozzá szeretné adni, adja a következő sorokat a széndioxid-érzékelő sorai alá. Ezek a provisionSample.yaml megjegyzésként megadott sorokat biztosítják. A megjegyzéshez fűzött megjegyzés `#` hez távolítsa el a karaktert az egyes sorok elején. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Győződjön `dataType` meg `hardwareId` arról, hogy a billentyűk igazodnak a kódrészlet feletti utasításokhoz. Azt is ellenőrizze, hogy a szerkesztő nem cserélte-e le a szóközöket tabulátorokra. 

Mentse és zárja be a provisionSample.yaml fájlt. A következő oktatóanyagban további információkat adhat hozzá a fájlhoz, majd kiépítheti az Azure Digital Twins mintaépületét.

> [!TIP]
> Az Azure Digital Twins Graph Viewer segítségével megtekintheti és módosíthatja térbeli [grafikonját.](https://github.com/Azure/azure-digital-twins-graph-viewer)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezen a ponton le szeretné állítani az Azure Digital Twins felfedezését, nyugodtan törölje az oktatóanyagban létrehozott erőforrásokat:

1. Az [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza az **Összes erőforrás**lehetőséget, válassza ki a Digitális Twins erőforráscsoportot, és válassza a **Törlés**lehetőséget.

    > [!TIP]
    > Ha problémát tapasztalt a Digitális Twins-példány törlésekor, a javítással egy szolgáltatásfrissítés lett elvezetve. Próbálja meg újra kihagyni a példányt.

1. Ha szükséges, törölje a mintaalkalmazást a munkagépről.

## <a name="next-steps"></a>További lépések

Ha meg szeretné tudni, hogyan valósíthat meg egyéni logikát a mintaépület ben lévő feltételek figyelésére, folytassa a sorozat következő oktatóanyagával: 
> [!div class="nextstepaction"]
> [Oktatóanyag: Az épület kiépítése és a munkakörülmények monitorozása](tutorial-facilities-udf.md)