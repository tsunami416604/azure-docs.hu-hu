---
title: Élő videó-elemzések – IoT Edge – Azure
description: Ez a cikk a IoT Edge élő videó-elemzések hibaelhárítási lépéseit ismerteti.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: dd55050521a1791a11f220cd5617d9df2fa2d160
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045576"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Élő videó-elemzések hibáinak megoldása IoT Edge

Ez a cikk a IoT Edge élő videó-elemzések hibaelhárítási lépéseit ismerteti.

## <a name="troubleshoot-deployment-issues"></a>Üzembe helyezési problémák elhárítása

### <a name="diagnostics"></a>Diagnosztika

A Live Video Analytics üzembe helyezésének részeként be kell állítania az Azure-erőforrásokat, például a IoT Hub és a IoT Edge eszközt. A problémák diagnosztizálásának első lépéseként mindig gondoskodjon arról, hogy a peremhálózat megfelelően legyen beállítva a következő utasítások követésével:

1. [A "pipa" parancs futtatása](https://docs.microsoft.com/azure/iot-edge/troubleshoot#run-the-check-command)
1. [A IoT Edge verziójának keresése](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-iot-edge-version)
1. [A IoT Edge Security Manager és a naplók állapotának ellenõrzése](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-the-status-of-the-iot-edge-security-manager-and-its-logs)
1. [Az IoT Edge hub-on keresztül haladó üzenetek megtekintése](https://docs.microsoft.com/azure/iot-edge/troubleshoot#view-the-messages-going-through-the-iot-edge-hub)
1. [Tárolók újraindítása](https://docs.microsoft.com/azure/iot-edge/troubleshoot#restart-containers)
1. [A tűzfal és a port konfigurációs szabályainak megkeresése](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-firewall-and-port-configuration-rules)

### <a name="pre-deployment-issues"></a>Üzembe helyezés előtti problémák

Ha a peremhálózati infrastruktúra rendben van, akkor megkeresheti a telepítési jegyzékfájlval kapcsolatos problémákat. Ha a peremhálózati eszközön IoT Edge modulban szeretné üzembe helyezni az élő video Analytics szolgáltatást, a többi IoT-modul mellett olyan központi telepítési jegyzékfájlt fog használni, amely tartalmazza a peremhálózat, az Edge-ügynök és más modulok tulajdonságait. Ha a JSON nem megfelelően van formázva, a következő hibaüzenetet kaphatja: 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

Nem sikerült elemezni a JSON-fájlt a következő fájlból: " <deployment manifest.json> " a "Content" argumentummal: "extra adatok: sor 101 oszlop 1 (char 5325)"

Ha ezt a hibát tapasztalja, javasoljuk, hogy ellenőrizze a JSON-fájlt a hiányzó zárójelek vagy más problémák esetén a fájl struktúrájával. Használhat olyan ügyfelet, mint a [Notepad + + és a JSON Viewer beépülő modul](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) , vagy egy online eszköz, például https://jsonformatter.curiousconcept.com/ a fájl struktúrájának ellenőrzése.

### <a name="deployment--diagnose-with-media-graph-direct-methods"></a>Üzembe helyezés – a Media Graph közvetlen módszereinek diagnosztizálása 

Ha az élő videó elemzése IoT Edge modulban megfelelően van telepítve a peremhálózati eszközön, a [közvetlen metódusok](direct-methods.md)meghívásával hozhatja létre és futtathatja a Media Graphot. A portálon a Media Graph diagnosztizálását közvetlen módszerekkel is futtathatja:

1. A portálon nyissa meg a peremhálózati eszközéhez csatlakozó IoT Hub.
    1. A IoT Hub panelen keresse meg az automatikus eszközkezelés->IoT Edge.
    1. Ha IoT Edgere kattint, megjelenik az Edge-eszközök listája. Válassza ki a diagnosztizálni kívánt eszközt.
         
        ![Edge-eszközök](./media/troubleshoot-how-to/lva-sample-device.png)
    1. Ellenőrizze, hogy a válasz kódja 200 – OK. A [IoT Edge futtatókörnyezetnek](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime) számos más válasza van, például:
        1. 400 – a telepítési konfiguráció helytelen formátumú vagy érvénytelen.
        1. 417 – az eszköz nem rendelkezik telepítési konfigurációs készlettel.
        1. 412 – a séma verziója érvénytelen a telepítési konfigurációban.
        1. 406 – a IoT Edge eszköz offline állapotban van, vagy nem küld állapotjelentéseket.
        1. 500 – hiba történt a IoT Edge futtatókörnyezetben.
    1. Az eszközre kattintva a várt IoT Edge modulok listáját is meg kell jeleníteni, és az állapotukat is
    1. Ha az "a központi telepítésben megadott" és "az eszköz által jelentett" oszlop az "igen" értéket jelöli, közvetlen metódusokat hívhat meg az élő videó Analytics IoT Edge modulján. Kattintson a modulra, és megnyílik egy képernyő, ahol megtekintheti a kívánt és jelentett tulajdonságokat, és közvetlen metódusokat hívhat meg. 
        1. A jelentett és a kívánt tulajdonságok ellenőrzése segít megérteni, hogy a modul tulajdonságai szinkronizálva lettek-e a telepítéssel. Ha nem, akkor újraindíthatja az Edge-t 
        1. A [közvetlen metódusok](direct-methods.md) útmutatójában néhány módszert hívhat meg, különösen azokat az egyszerűeket, mint a GraphTopologyList. Az útmutató a várt kérelmek és válaszok hasznos adatait és hibakódokat is megadja. Ha az egyszerű közvetlen metódusok sikeresek voltak, gondoskodhat arról, hogy az élő videó Analytics Edge-modulja működés közben rendben legyen.
        
        ![Közvetlen metódus](./media/troubleshoot-how-to/direct-method.png) 
1. Ha 501-as kódú állapotot kap, ellenőrizze, hogy a közvetlen metódus neve pontos-e. Ha a metódus neve és a kérelmek hasznos adatai pontosak, az eredményeket vissza kell olvasnia a siker kódja = 200 értékkel együtt. Ha a kérelem tartalma pontatlan, a rendszer a következő állapotot jeleníti meg: 400 és egy válasz hasznos adat, amely a közvetlen metódus hívásával kapcsolatos probléma diagnosztizálásához segítséget nyújtó hibakódot és üzenetet mutatja. 

### <a name="post-deployment--diagnose-logs-for-issues-during-run"></a>Telepítés utáni problémák – a naplók diagnosztizálása a Futtatás során 

A peremhálózati modulhoz tartozó tároló naplóinak diagnosztikai<!--<todo:add link to diagnostics doc>--> információ, amelynek segítségével hibakeresést végezhet a modul futtatókörnyezete során. Megtekintheti a [tárolói naplókat a problémákhoz](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-container-logs-for-issues) és az öndiagnosztizáláshoz, azonban ha a fenti ellenőrzések végrehajtása megtörtént, és továbbra is problémákba ütközik, akkor Gyűjtse össze a naplókat a IoT Edge eszközön a ["támogatási csomag" parancs](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command) segítségével, amelyet az Azure csapata tovább elemez. Az összegyűjtött naplók támogatását és beküldését is [elérheti](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) .

## <a name="common-error-resolutions"></a>Gyakori hibák megoldásai

Az élő videó elemzése IoT Edge-modulként van üzembe helyezve a peremhálózati eszközön, és együttműködik a IoT Edge-ügynökkel és a hub-modulok használatával. A Live Video Analytics üzembe helyezésével kapcsolatos gyakori hibákat az alapul szolgáló IoT-infrastruktúrával kapcsolatos problémák okozják. Néhány gyakori hiba, amely IoT Edge az ügynök és a hub a következőkkel rendelkezhet:

1. [IoT Edge ügynök egy perc múlva leáll](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-stops-after-about-a-minute).
1. [IoT Edge ügynök nem fér hozzá a modul rendszerképéhez (403)](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-cant-access-a-modules-image-403).
1. [Az Edge Agent modul "üres konfigurációs fájl" jelentést készít, és az eszközön egyetlen modul sem indul el](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
1. [IoT Edge hub nem indul el](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-hub-fails-to-start).
1. [IoT Edge biztonsági démon érvénytelen állomásnévvel meghiúsul](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-security-daemon-fails-with-an-invalid-hostname).
1. Az [élő videó-elemzés vagy bármely más egyéni IoT Edge modul nem tud üzenetet küldeni az Edge hubhoz 404-as hibával](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
1. [IoT Edge modul üzembe helyezése sikeresen megtörtént, majd eltűnik az eszközről](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-deploys-successfully-then-disappears-from-device).

### <a name="edge-set-up-script-issues"></a>A szkripttel kapcsolatos problémák üzembe állítása

A dokumentáció részeként egy üzembe helyezési [szkriptet hoztunk létre](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) a Edge-és a Felhőbeli erőforrások üzembe helyezéséhez, hogy megismerkedjen az élő videó Analytics Edge szolgáltatással. Ebben a szakaszban a szkripttel és a hibakereséssel kapcsolatos hibákat rögzítettük.

A parancsfájl részben több erőforrást hoz létre, de a következő üzenettel meghiúsul:

```
registering device...

Unable to load extension 'eventgrid: unrecognized kwargs: ['min_profile']'. Use --debug for more information.
The command failed with an unexpected error. Here is the traceback:

No module named 'azure.mgmt.iothub.iot_hub_client'
Traceback (most recent call last):
File "/opt/az/lib/python3.6/site-packages/knack/cli.py", line 215, in invoke
  cmd_result = self.invocation.execute(args)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 631, in execute
  raise ex
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 695, in _run_jobs_serially
  results.append(self._run_job(expanded_arg, cmd_copy))
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 688, in _run_job
  six.reraise(*sys.exc_info())
File "/opt/az/lib/python3.6/site-packages/six.py", line 693, in reraise
  raise value
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 665, in _run_job
  result = cmd_copy(params)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 324, in __call__
  return self.handler(*args, **kwargs)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/__init__.py", line 574, in default_command_handler
  return op(**command_args)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 75, in iot_device_list
  result = iot_query(cmd, query, hub_name, top, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 45, in iot_query
  target = get_iot_hub_connection_string(cmd, hub_name, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/common/_azure.py", line 112, in get_iot_hub_connection_string
  client = iot_hub_service_factory(cmd.cli_ctx)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/_factory.py", line 28, in iot_hub_service_factory
  from azure.mgmt.iothub.iot_hub_client import IotHubClient
ModuleNotFoundError: No module named 'azure.mgmt.iothub.iot_hub_client'
```
    
A probléma megoldása:

1. Futtassa az alábbi parancsot:

    ```
    az --version
    ```
1. Győződjön meg arról, hogy telepítve vannak a következő bővítmények. Az útmutató írásakor a bővítmények verziója a következő:

    | Mellék | Verzió |
    |---|---|
    |azure-cli   |      2.5.1|
    |parancssori modulok – nspkg         |   2.0.3|
    |Core  |    2.5.1|
    |nspkg    | 3.0.4|
    |telemetria| 1.0.4|
    |Extensions    ||
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. Ha a bővítmények bármelyike régebbi a fenti kiadási számnál, frissítse a bővítményt a legújabb verzióra a következő parancs használatával:

    ```
    az extension update --name <Extension name>
    ```

    Például: `az extension update --name azure-iot`

### <a name="sample-app-issues"></a>Példák az alkalmazással kapcsolatos problémákra

A kiadás részeként elérhetővé tettünk néhány .NET-mintakód a fejlesztői közösségi bootstrapped beszerzéséhez. Ebben a szakaszban rögzítettük azokat a hibákat, amelyek a mintakód futtatásakor és az ilyen hibák hibakeresésében is előfordulhatnak.

1. A Program.cs a következő hibával meghiúsul a közvetlen metódus meghívásakor:

    ```
    Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
    ```

    1. Győződjön meg arról, hogy rendelkezik a VS Code-környezetbe telepített [Azure IoT-eszközökkel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) , valamint a IoT hub telepítőjének kapcsolódásával. (CTRL + SHIFT + P, majd válassza ki IoT Hub módszert az előfizetéshez való kapcsolódáshoz és IoT Hub)
1. Ellenőrizze, hogy hívhat-e közvetlen metódust az Edge-modulon a VS Code használatával (például hívja meg a GraphToplogyList a következő hasznos tartalommal: {" @apiVersion ": "1,0"}), és a következő válaszokat kell visszakapnia. 

    ```
    {
      "status": 200,
      "payload": {
        "values": [
          {…
    …}
          ]
        }
    }
    ```

    ![Visual Studio Code](./media/troubleshoot-how-to/visual-studio-code1.png)
1. Ha a fentiek sikertelenek, próbálkozzon a következőkkel:
    1. Nyissa meg a parancssort a peremhálózati eszközén, és írja be a következőt:.
    
    ```
    sudo systemctl restart iotedge
    ```

    Ekkor a rendszer újraindítja a peremhálózati eszközt és az összes modult. Várjon néhány percet, és futtassa a következő parancsot annak ellenőrzéséhez, hogy a modulok futnak-e, majd próbálja meg újból használni a DirectMethod.

    ```
    sudo iotedge list
    ```
    1. Ha a fentiek szintén sikertelenek, próbálja meg újraindítani a virtuális gépet vagy a számítógépet.
    1. Ha az összes művelet meghiúsul, futtassa az alábbi parancsot egy olyan ZIP-fájl beszerzéséhez, amely az adott [támogatási jegyhez](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)csatolni kívánt összes [naplóval](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command) rendelkezik.

    ```
    sudo iotedge support-bundle --since 2h
    ```
1. Ha a 400-as kódú hibakód jelenik meg, akkor győződjön meg arról, hogy a metódus Meghívási adattartalma megfelelően van-e kialakítva a [közvetlen metódusok](direct-methods.md) útmutatója alapján.
1. Ha a 200-as kódú állapotot kapja, az azt jelzi, hogy a központ jól működik, és a modul üzembe helyezése megfelelő és rugalmas. A következő lépésben ellenőrizze, hogy az alkalmazás konfigurációja pontos-e. Az alkalmazás konfigurációja a következő mezőkből áll a fájl appsettings.js. Ellenőrizze, hogy a deviceId és a moduleId pontosak-e. Ez egy egyszerű módja annak, hogy ezt a VSCode Azure IoT Hub Extension szakasza tartalmazza. A fájl appsettings.jsértékének és a IoT Hub szakasznak meg kell egyeznie.
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

    ![IOT HUB](./media/troubleshoot-how-to/iot-hub.png)

1. Végül győződjön meg róla, hogy a appsettings.json belül megadott IoT Hub a kapcsolatok karakterlánca, nem pedig a IoT Hub eszköz-kapcsolatok karakterlánca, mivel a [formátumuk](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/) eltérő.

### <a name="live-video-analytics-working-with-external-modules"></a>Külső modulok használata élő videó-elemzéssel

A HTTP-bővítményt használó élő videó-elemzés kiterjesztheti a Media Graphot, hogy más IoT Edge-moduloktól származó adatok küldését és fogadását HTTP-n keresztül a REST használatával.  Ebben a [példában](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension) a Media Graph képkockákat küld képként egy külső következtetési modulnak, például a Yolo v3-nek, és a JSON-alapú elemzési eredményeket kapja vissza. Ilyen topológiában az események végpontja általában a IoT Hub. Olyan helyzetekben, amikor nem látja a középpontban található következtetési eseményeket, ellenőrizze a következőket:

1. Ellenőrizze, hogy a Media Graph által közzétett központnak a vs. az Ön által vizsgált központ megegyezik-e. Időnként, ha több központi telepítést hoz létre, a rendszer több hubokat is futtat, és az események helytelen központi ellenőrzését is elvégezheti.
1. Ellenőrizze a VSCode-n keresztül, ha a külső modul telepítve van és fut. A példában szereplő képen a rtspsim és a CV IoT Edge a lvaEdge modulon kívül futó modulok.

    ![IOT HUB](./media/troubleshoot-how-to/iot-hub.png)
1. Ellenőrizze, hogy a megfelelő URL-végpontra küld-e eseményeket. A külső AI-tároló egy URL-címet és egy olyan portot tesz elérhetővé, amely a POST kérelmekből származó adatokkal érkezik, és visszaadja azokat. Ez az URL-cím a http-bővítmény processzorának a végpont: URL tulajdonsága. Ahogy a [topológia URL-címe](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json) is látható, az URL-cím paraméterre van állítva. Győződjön meg arról, hogy a paraméter alapértelmezett értéke ( http://yolov3/score) vagy az átadott érték pontos, és tesztelni tudja, hogy működik-e a curl használatával.  
    1. Például a helyi gépen futó Yolo v3-tároló és a tároló IP-címe 172.17.0.3 (a Docker-ellenőrzés használatával keresse meg az IP-címet).

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Eredmény visszaadva:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

1. Ha egy gráf egy vagy több példányát futtatja, és a http-bővítmény processzorát használja, akkor minden http-bővítmény processzora előtt meg kell jelennie a frame rate szűrőnek. Bizonyos helyzetekben, ahol a peremhálózati gép PROCESSZORa/memóriája magas kihasználtsággal rendelkezik, elveszítheti bizonyos következtetési eseményeket. Ennek megoldásához állítsa be a frame rate szűrő maximumFps tulajdonságának alacsony értékét. Beállíthatja a 0,5 ("maximumFps": 0,5) értékre a gráf minden egyes példányán, majd újra megkeresheti a következtetéseket az adott központban.
    1. Azt is megteheti, hogy egy nagyobb teljesítményű, magasabb szintű PROCESSZORral és memóriával rendelkező peremhálózati gépet is elérhet.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Több közvetlen metódus párhuzamos – időtúllépési hiba 

A IoT Edge Live Video Analytics olyan közvetlen metódus-alapú programozási modellt biztosít, amely lehetővé teszi több topológia és több gráf-példány beállítását. A topológia és a gráf beállításának részeként több közvetlen metódust hív meg az Edge-modulban. Ha meghívja ezeket a több metódust, különösen a gráfok indítását és leállítását, párhuzamosan előfordulhat, hogy bizonyos időtúllépési hibákat tapasztal, például az alábbiakat. 

A szerelvény inicializálási metódusa Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync kivételt okozott. Microsoft. Azure. Devices. Common. kivétellistájára. IotHubException: Microsoft. Azure. Devices. Common. kivételek. IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

Javasoljuk, hogy ne hívja meg a közvetlen metódusokat párhuzamosan, hanem egymást követő módon, azaz  egy közvetlen metódus hívása csak az előző befejeződése után.

### <a name="collecting-logs-for-submitting-a-support-ticket"></a>A támogatási jegy beküldéséhez szükséges naplók összegyűjtése

Ha az önálló irányítású hibaelhárítási lépések nem oldják meg a problémákat, lépjen a Azure Portalra, és [Nyisson meg egy támogatási jegyet](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

Folytassa a következő lépésekkel, hogy összegyűjtse a jegyhez hozzáadandó releváns naplókat. A naplófájlokat a támogatási kérelem **részletek** lapján töltheti fel.

### <a name="support-bundle"></a>Támogatás – csomag

Ha IoT Edge-eszközről kell összegyűjtenie a naplókat, a legegyszerűbb módszer a `support-bundle` parancs használata. Ez a parancs a következőket gyűjti:

- Modulok naplói
- A IoT Edge Security Manager és a Container Engine naplói
- Iotedge-ellenőrzési JSON-kimenet
- Hasznos hibakeresési információk

#### <a name="use-the-iot-edge-security-manager"></a>A IoT Edge Security Manager használata
 
A IoT Edge Security Manager felelős az olyan műveletekért, mint a IoT Edge rendszer inicializálásakor az indítási és kiépítési eszközökön. Ha IoT Edge nem indul el, a Security Manager naplói hasznos információkat nyújthatnak. A IoT Edge Security Manager részletesebb naplófájljainak megtekintéséhez:

1. Szerkessze a IoT Edge Daemon-beállításokat az IoT Edge-eszközön:

    ```
    sudo systemctl edit iotedge.service
    ```

1. Frissítse a következő sorokat:

    ```
    [Service]
    Environment=IOTEDGE_LOG=edgelet=debug
    ```

1. Indítsa újra a IoT Edge biztonsági démont a következő parancsok futtatásával:

    ```
    sudo systemctl cat iotedge.service
    sudo systemctl daemon-reload
    sudo systemctl restart iotedge
    ```

1. Futtassa a `support-bundle` parancsot a--since jelzővel annak megadásához, hogy a múltban mennyi ideig szeretné lekérni a naplókat. Például a 2H az elmúlt két órában lekéri a naplókat. A jelző értékét módosíthatja úgy, hogy a naplókat egy másik időszakban is tartalmazza.

    ```
    sudo iotedge support-bundle --since 2h
    ```

### <a name="lva-debug-logs"></a>Hibakeresési naplók LVA

A következő lépésekkel konfigurálhatja a LVA IoT Edge modulban a hibakeresési naplók létrehozásához:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az IoT hubhoz.
1. A menüből válassza a **IoT Edge** lehetőséget.
1. Kattintson a céleszköz AZONOSÍTÓJÁRA az eszközök listájából.
1. Kattintson a **modulok beállítása** hivatkozásra a felső menüben.

  ![modulok beállítása az Azure Portalon](media/troubleshoot-how-to/set-modules.png)

5. A IoT Edge-modulok szakaszban keresse meg és kattintson a **lvaEdge**elemre.
1. Kattintson a **tároló létrehozási beállítások**elemre.
1. A kötések szakaszban adja hozzá a következő parancsot:

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    Ez köti a naplók mappáit a peremhálózati eszköz és a tároló között.

1. Kattintson az **Update (frissítés** ) gombra
1. Kattintson a lap alján található **felülvizsgálat + létrehozás** gombra. Egy egyszerű ellenőrzésre kerül sor, és a rendszer egy zöld szalagcím alatt sikeres érvényesítési üzenetet küld.
1. Kattintson a **Létrehozás** gombra.
1. Ezután frissítse a **modul Identity Twin** elemet, hogy a DebugLogsDirectory paramétert arra a könyvtárra mutasson, amelyben a naplók gyűjtése történik:
    1. A **modulok** táblázatban válassza a **lvaEdge** lehetőséget.
    1. Kattintson a **modul Identity Twin** hivatkozására. Ezt az oldal tetején találja. Ekkor megnyílik egy szerkeszthető ablaktábla.
    1. Adja hozzá a következő kulcs-érték párt a **kívánt kulcsban**:

        `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    1. Kattintson a **Save (Mentés**) gombra.

1. Reprodukálja a problémát.
1. Kapcsolódjon a virtuális géphez a portál IoT Hub oldaláról.
1. Navigáljon a mappához `/var/local/mediaservices/logs` , és küldje el a mappa bin tartalmát, és ossza meg velünk. (Ezek a naplófájlok nem a saját diagnosztika céljára szolgálnak. Ezek az Azure Engineering a problémák elemzéséhez készültek.)

1. A napló-gyűjtemény leállítható úgy, hogy a **modul identitása Twin** értékre állítja a *Null* értéket. Térjen vissza a **modul Identity Twin** oldalára, és frissítse a következő paramétert:

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>További lépések

[Oktatóanyag: eseményvezérelt videofelvétel a felhőben és a felhőből való lejátszás](event-based-video-recording-tutorial.md)
