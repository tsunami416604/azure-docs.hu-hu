---
title: Az Azure Digital Twins üzembe helyezése | Microsoft Docs
description: Megtudhatja, hogy a jelen oktatóanyagban lévő lépésekkel hogyan helyezheti üzembe az Azure Digital Twins-példányát, és hogyan konfigurálhatja a térbeli erőforrásait.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 7e51513e5cc17b18b6822925051b207f61e20ea1
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283852"
---
# <a name="tutorial-deploy-azure-digital-twins-and-configure-a-spatial-graph"></a>Oktatóanyag: Az Azure Digital Twins üzembe helyezése és egy térbeli diagram konfigurálása

Az Azure Digital Twins szolgáltatás lehetővé teszi, hogy egy összefüggő térbeli rendszerbe gyűjtsön össze személyeket, helyeket és eszközöket. Ez az oktatóanyag-sorozat bemutatja, hogyan használható az Azure Digital Twins a helyiségek foglaltságának, valamint az optimális hőmérséklet és levegőminőség észleléséhez. Ezek az oktatóanyagok végigvezetik egy .NET-konzolalkalmazás használatán, amellyel egy többemeletes, minden emeleten több helyiséggel rendelkező irodaépülethez hoz létre forgatókönyvet. A helyiségek eszközöket tartalmaznak, amelyekhez mozgást, környezeti hőmérsékletet és levegőminőséget észlelő érzékelők vannak csatlakoztatva. Megtanulja, hogyan replikálhatja digitális objektumokként az épületben lévő fizikai területeket és entitásokat a Digital Twins szolgáltatás használatával. Egy másik konzolalkalmazással eszközeseményeket fog szimulálni. Ezután megtanulja, hogyan monitorozhatja az ezekről a fizikai területekről és entitásokról érkező eseményeket közel valós időben. Egy irodai rendszergazda ezekkel az információkkal segíthet az épületben dolgozóknak az optimális feltételeknek megfelelő tárgyalótermek lefoglalásában. Az irodai létesítmények vezetői lekérhetik a helyiségek használati trendjeit, és karbantartási célból monitorozhatják a munkafeltételeket.

A sorozat első oktatóanyagában az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A Digital Twins üzembe helyezése
> * Engedélyek megadása az alkalmazásnak
> * A Digital Twins-mintaalkalmazás módosítása
> * Az épület kiépítése


Ezek az oktatóanyagok ugyanazokat a mintákat használják és módosítják, mint a [szabad helyiségek keresését bemutató rövid útmutató](quickstart-view-occupancy-dotnet.md), amelyben megtalálhatja a fogalmak részletesebb leírását.


## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs Azure-fiókja, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Az ezekben az oktatóanyagokban használt Digital Twins-minták C# nyelven íródtak. Mindenképpen telepítse a minta létrehozására és futtatására szolgáló fejlesztői gépen a [.NET Core SDK 2.1.403-as vagy újabb](https://www.microsoft.com/net/download) verzióját. Egy parancsablakban a `dotnet --version` parancs futtatásával ellenőrizze, hogy a megfelelő verzió van-e telepítve.

- [Visual Studio Code](https://code.visualstudio.com/) a mintakód vizsgálatához. 

<a id="deploy" />

## <a name="deploy-digital-twins"></a>A Digital Twins üzembe helyezése

A szakaszban leírt lépésekkel hozza létre a Digital Twins szolgáltatás egy új példányát. Előfizetésenként csak egy példány hozható létre. Ha már rendelkezik futó példánnyal, hagyja ki ezt a szakaszt, és folytassa a következővel. 

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]


<a id="permissions" />

## <a name="grant-permissions-to-your-app"></a>Engedélyek megadása az alkalmazásnak

A Digital Twins az [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) használatával vezérli a szolgáltatás [olvasási/írási hozzáférését](../active-directory/develop/v1-permissions-and-consent.md). Regisztrálni kell az Azure Active Directoryban minden olyan alkalmazást, amelynek a Digital Twins-példányhoz kell csatlakoznia. A jelen szakaszban ismertetett lépések bemutatják a mintaalkalmazás regisztrálásának módját.

Ha már rendelkezik *alkalmazásregisztrációval*, újból felhasználhatja azt a mintához. De mindenképpen tekintse át ezt a szakaszt, hogy az alkalmazásregisztráció megfelelően legyen konfigurálva.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]


## <a name="configure-digital-twins-sample"></a>A Digital Twins-minta konfigurálása

Ez a szakasz a [Digital Twins REST API-kkal](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index) kommunikáló Digital Twins-alkalmazás használatán vezeti végig. 

### <a name="download-the-sample"></a>A minta letöltése
Ha már letöltötte a [szabad helyiségek keresésére szolgáló rövid útmutató](quickstart-view-occupancy-dotnet.md) mintáit, kihagyhatja ezeket a lépéseket.

1. Töltse le a [Digital Twins .Net-mintákat](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip). 
2. Csomagolja ki a gépén a ZIP-mappa tartalmát. 

### <a name="explore-the-sample"></a>A minta vizsgálata
A kicsomagolt mintamappában nyissa meg a **_digital-twins-samples-csharp\digital-twins-samples.code-workspace_** fájlt a Visual Studio Code-ban. Ez két projektet tartalmaz: 

1. Az **_occupancy-quickstart_** üzembehelyezési mintával [térbeliintelligencia-diagramot](concepts-objectmodel-spatialgraph.md#graph) konfigurálhat és hozhat létre, amely a fizikai területek és az azokban lévő erőforrások digitalizált képe. Ez [objektummodellt](concepts-objectmodel-spatialgraph.md#model) használ, amely meghatározza az intelligens épületek objektumait. A Digital Twins-objektumok és REST API-k teljes listájáért nyissa meg [ezt a REST API-dokumentációt](https://docs.westcentralus.azuresmartspaces.net/management/swagger) vagy a [példányhoz](#deploy) létrehozott **felügyeleti API** URL-címét.

   Először az **_src\actions_** mappában megvizsgálhatja, hogyan kommunikál a minta a Digital Twins-példánnyal. Az ebben a mappában található fájlok valósítják meg az ezekben az oktatóanyagokban használandó parancsokat:
    - a *provisionSample.cs* fájl a térbeli diagram kiépítésének módját mutatja be,
    - a *getSpaces.cs* fájl a kiépített területekkel kapcsolatban kér le információkat,
    - a *getAvailableAndFreshSpaces.cs* fájl a felhasználó által meghatározott egyéni függvény eredményeit kéri le, és
    - a *createEndpoints.cs* fájl végpontokat hoz létre, amelyek más szolgáltatásokkal kommunikálnak.

1. A **_device-connectivity_** szimulációs minta érzékelőadatokat szimulál, és a Digital Twins-példányhoz üzembe helyezett IoT Hubra küldi azokat. Ezt a mintát [a következő oktatóanyagban fogja használni, a térbeli diagram kiépítése után](tutorial-facilities-udf.md#simulate). A minta konfigurálásához használt érzékelő- és eszközazonosítók ugyanazok, mint amelyeket a diagram kiépítéséhez fog használni.

### <a name="configure-the-provisioning-sample"></a>A kiépítési minta konfigurálása
1. Nyisson meg egy parancsablakot, és navigáljon a letöltött mintához. Futtassa az alábbi parancsot:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Az alábbi parancs futtatásával állítsa vissza a függőségeket a mintaprojektben:

    ```cmd/sh
    dotnet restore
    ```

1. A Visual Studio Code-ban nyissa meg az **occupancy-quickstart** projekthez tartozó **_appSettings.json_** fájlt, és frissítse az alábbi értékeket:
    1. *ClientId*: Adja meg az AAD-alkalmazásregisztráció **alkalmazásazonosítóját**, amelyet az [alkalmazásengedélyek beállításáról](#permissions) szóló szakaszban jegyzett fel.
    1. *Tenant*: Adja meg az [AAD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) **címtár-azonosítóját**, amelyet szintén az [alkalmazásengedélyek beállításáról](#permissions) szóló szakaszban jegyzett fel.
    1. *BaseUrl*: Adja meg a Digital Twins-példány URL-címét. Az URL-cím lekéréséhez cserélje le az URL-ben lévő helyőrzőket a példány értékeire: **https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/**. Ezt az URL-címet [az üzembehelyezési szakaszban](#deploy) lévő **felügyeleti API** módosításával is lekérheti, amelyben a **swagger/** kifejezést kell az **api/v1.0/** elérési útra lecserélnie.

1. Az alábbi parancs futtatásával tekintheti meg a mintával felderíthető Digital Twins-funkciók listáját.

    ```cmd/sh
    dotnet run
    ```

<a id="provision-spaces" />

## <a name="understand-provisioning-process"></a>A kiépítési folyamat megismerése
Ez a szakasz egy épület térbeli diagramjának kiépítését mutatja be. 

A Visual Studio Code-ban nyissa meg az **_occupancy-quickstart\src\actions_** mappát, és nyissa meg a *provisionSample.cs* fájlt. Figyelje meg az alábbi függvényt:

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

Ez a függvény az ugyanebben a mappában lévő *provisionSample.yaml* fájlt használja. Nyissa meg a fájlt, és vegye szemügyre az irodaépület hierarchiáját: a *helyszínt*, az *emeletet*, a *területet* és a *helyiségeket*. A fizikai területek bármelyike tartalmazhat *eszközöket* és *érzékelőket*. Mindegyik bejegyzés rendelkezik előre meghatározott `type` értékkel (például: *emelet* vagy *helyiség*). 

A minta *yaml*-fájl a `Default` Digital Twins-objektummodellel jelenít meg egy térbeli diagramot. Ez a modell a legtöbb típus (például a SensorDataType hőmérséklete vagy a SpaceBlobType térképe) és területtípus (például FocusRoom, ConferenceRoom stb. altípusú helyiség) általános nevét tartalmazza, ami elegendő egy épülethez. Ha más típusú helyszínhez, például gyárhoz szeretne létrehozni térbeli diagramot, előfordulhat, hogy más objektummodellre lesz szüksége. A kiépítési minta parancssorában a `dotnet run GetOntologies` parancs futtatásával tudhatja meg, hogy mely modellek használhatók. A térbeli diagramokról és objektummodellekről további információt [a Digital Twins objektummodelljével és térbeliintelligencia-diagramjával](concepts-objectmodel-spatialgraph.md) kapcsolatos szakaszban talál. 

### <a name="modify-sample-spatial-graph"></a>Térbeli diagram mintájának módosítása
A *provisionSample.yaml* az alábbi csomópontokat tartalmazza:

- **resources**: A `resources` csomópont egy IoT Hub-erőforrást hoz létre a környezet eszközeivel való kommunikációhoz. A diagram gyökércsomópontján lévő IoT Hub a diagramban lévő összes eszközzel és érzékelővel tud kommunikálni.  

- **spaces**: A Digital Twins-objektummodellben a `spaces` jelöli a fizikai helyeket. Minden egyes címtérhez tartozik egy `Type`, például *régió*, *helyszín* vagy egy *ügyfél*, valamint egy rövid `Name`. A terek más terekhez is tartozhatnak, így hierarchikus struktúra jöhet létre. A *provisionSample.yaml* egy képzeletbeli épület térbeli diagramját tartalmazza. Figyelje meg, hogy a `Floor` típusú terek logikailag a `Venue` típusba vannak ágyazva, az `Area` típusok egy emeletbe, és a `Room` csomópontok egy területbe. 

- **devices**: A terek `devices`-elemeket tartalmazhatnak, amelyek érzékelőket kezelő fizikai vagy virtuális entitások. Egy eszköz lehet például felhasználói telefon, Raspberry Pi-érzékelőpod, egy átjáró stb. Figyelje meg a mintában lévő képzeletbeli épületben, hogy a *Focus Room* nevű helyiség egy *Raspberry Pi 3 A1* eszközt tartalmaz. Minden eszközcsomópontot egyedi `hardwareId` azonosít, amely szoftveresen kötött a mintában. A minta tényleges éles környezethez való konfigurálásához cserélje le ezeket a környezetében lévő értékekre.  

- **sensors**: Egy eszköz több `sensors` elemet tartalmazhat, amelyek olyan fizikai változásokat észlelhetnek és rögzíthetnek, mint a hőmérséklet, a mozgás, az akkumulátor töltöttségi szintje stb. Minden érzékelő-csomópontot egyedien azonosít egy `hardwareId`, amely itt szoftveresen kötött. A tényleges alkalmazáshoz cserélje le ezeket a környezetben használt érzékelők egyedi azonosítóira. A *provisionSample.yaml* fájl két érzékelőt tartalmaz a *mozgás* és a *széndioxid* értékének rögzítéséhez. Ha a *hőmérséklet* érzékelőjét is hozzá szeretné adni, adja a következő sorokat a széndioxid-érzékelő sorai alá. Vegye figyelembe, hogy ezek a sorok megjegyzésként szerepelnek a *provisionSample.yaml* fájlban. A sorok elején lévő „#” karakter eltávolításával egyszerűen eltávolíthatja a megjegyzésként való jelölést. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Győződjön meg arról, hogy a `dataType` és a `hardwareId` kulcs igazodjon a jelen kódrészlet feletti utasításokhoz. Azt is ellenőrizze, hogy a szerkesztő nem cserélte-e le a szóközöket tabulátorokra. 

Mentse és zárja be a *provisionSample.yaml* fájlt. A következő oktatóanyagban további információkat fog adni ehhez a fájlhoz, majd kiépíti az Azure Digital Twins-mintaépületet.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha most be szeretné fejezni az Azure Digital Twins megismerését, nyugodtan törölje az ebben az oktatóanyagban létrehozott erőforrásokat:

1. Az [Azure Portal](http://portal.azure.com) bal oldali menüjében kattintson a **Minden erőforrás** lehetőségre, válassza ki a Digital Twins-erőforráscsoportot, és **törölje**.
2. Szükség esetén a mintaalkalmazást is törölje a munkavégzéshez használt gépről. 


## <a name="next-steps"></a>További lépések

Lépjen a sorozat következő oktatóanyagára annak megismeréséhez, hogyan valósíthat meg egyéni logikát a mintaépület feltételeinek monitorozásához. 
> [!div class="nextstepaction"]
> [Oktatóanyag: Az épület kiépítése és a munkakörülmények monitorozása](tutorial-facilities-udf.md)

