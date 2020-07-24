---
title: Élő videó-elemzések – IoT Edge – Azure
description: Ez a cikk a IoT Edge élő videó-elemzések hibaelhárítási lépéseit ismerteti.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: 62163217862f586be7ed5c0a6000693f8e7fcdd6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87043184"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Élő videó-elemzések hibáinak megoldása IoT Edge

Ez a cikk a Azure IoT Edge élő video Analytics (LVA) hibaelhárítási lépéseit ismerteti.

## <a name="troubleshoot-deployment-issues"></a>Üzembe helyezési problémák elhárítása

### <a name="diagnostics"></a>Diagnosztika

Az élő video Analytics üzembe helyezésének részeként be kell állítania az Azure-erőforrásokat, például a IoT Hub és IoT Edge eszközöket. A problémák diagnosztizálásának első lépéseként mindig ellenőrizze, hogy a peremhálózati eszköz megfelelően van-e beállítva az alábbi utasítások követésével:

1. [Futtassa a `check` parancsot](../../iot-edge/troubleshoot.md#run-the-check-command).
1. [Győződjön meg róla, hogy IoT Edge verziója](../../iot-edge/troubleshoot.md#check-your-iot-edge-version).
1. A [IoT Edge Security Manager és a naplóinak állapotának ellenõrzése](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs).
1. [Megtekintheti az IoT Edge hub-on keresztül haladó üzeneteket](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub).
1. [Indítsa újra a tárolókat](../../iot-edge/troubleshoot.md#restart-containers).
1. [Keresse meg a tűzfal és a port konfigurációs szabályait](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules).

### <a name="pre-deployment-issues"></a>Üzembe helyezés előtti problémák

Ha a peremhálózat-infrastruktúra rendben van, megkeresheti a telepítési jegyzékfájlval kapcsolatos problémákat. Ha a IoT Edge eszközön IoT Edge modulban szeretné üzembe helyezni az élő videó-elemzést a többi IoT-modul mellett, akkor olyan központi telepítési jegyzékfájlt használ, amely tartalmazza az IoT Edge hubot, a IoT Edge ügynököt és az egyéb modulokat és azok tulajdonságait. Ha a JSON-kód nem megfelelően van formázva, a következő hibaüzenet jelenhet meg: 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

Nem sikerült elemezni a JSON-fájlt a következő fájlból: " <deployment manifest.json> " a "Content" argumentummal: "extra adatok: sor 101 oszlop 1 (char 5325)"

Ha ezt a hibát tapasztalja, javasoljuk, hogy ellenőrizze a JSON-t a hiányzó zárójelek vagy más problémák esetén a fájl struktúrájával. A fájl struktúrájának ellenőrzéséhez használhat olyan ügyfelet, mint például a [Notepad + + JSON Viewer beépülő modullal](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) vagy egy online eszközzel, például a [json formázó & validator](https://jsonformatter.curiousconcept.com/).

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>Üzembe helyezés során: Diagnosztizálás a Media Graph közvetlen módszereivel 

Miután a IoT Edge eszközön megfelelően telepítette az élő videó-elemzést IoT Edge modulon, a [közvetlen metódusok](direct-methods.md)meghívásával létrehozhatja és futtathatja az adathordozó-diagramot. A Azure Portal használatával a Media Graph diagnosztizálását közvetlen metódusok segítségével futtathatja:

1. A Azure Portal nyissa meg a IoT Edge eszközhöz csatlakoztatott IoT hubot.

1. Keresse meg az **automatikus eszközkezelés**elemet, majd válassza a **IoT Edge**lehetőséget.  

1. Az Edge-eszközök listájában válassza ki a diagnosztizálni kívánt eszközt.  
         
    ![A Edge-eszközök listáját megjelenítő Azure Portal képernyőképe](./media/troubleshoot-how-to/lva-sample-device.png)

1. Ellenőrizze, hogy a válasz kódja *200 – OK*. A [IoT Edge futtatókörnyezethez](../../iot-edge/iot-edge-runtime.md) tartozó egyéb válaszok a következők:
    * 400 – a telepítési konfiguráció helytelen formátumú vagy érvénytelen.
    * 417 – az eszköz nem rendelkezik telepítési konfigurációs készlettel.
    * 412 – a séma verziója érvénytelen a telepítési konfigurációban.
    * 406 – a IoT Edge eszköz offline állapotban van, vagy nem küld állapotjelentéseket.
    * 500 – hiba történt a IoT Edge futtatókörnyezetben.

1. Ha 501-as állapotú kódot kap, ellenőrizze, hogy a közvetlen metódus neve pontos-e. Ha a metódus neve és a kérelmek hasznos adatai pontosak, akkor az eredményeket a siker Code = 200 értékkel együtt kell megkapnia. Ha a kérelem tartalma pontatlan, a rendszer a következő állapotot jeleníti meg: 400 és egy válasz hasznos adat, amely a közvetlen metódus hívásával kapcsolatos probléma diagnosztizálásához segítséget nyújtó hibakódot és üzenetet mutatja.
    * A jelentett és a kívánt tulajdonságok ellenőrzése segíthet megérteni, hogy a modul tulajdonságai szinkronizálva lettek-e a telepítéssel. Ha nem, akkor újraindíthatja IoT Edge eszközét. 
    * A [közvetlen metódusok](direct-methods.md) útmutatóval hívhat meg néhány módszert, különösen az egyszerűeket, például a GraphTopologyList. Az útmutató a várt kérelmek és válaszok hasznos adatait és hibakódokat is megadja. Az egyszerű közvetlen metódusok sikeres elvégzése után biztos lehet abban, hogy az élő videó-elemzési IoT Edge modul működés közben rendben van.
        
       ![Képernyőkép a IoT Edge modul "Direct metódus" paneljéről.](./media/troubleshoot-how-to/direct-method.png) 

1. Ha a **központi telepítésben megadott** és az **eszköz oszlopai által jelentett** érték azt jelzi, hogy az *Igen*, közvetlen metódusokat hívhat meg az élő videó Analytics IoT Edge modulján. Válassza ki a modult, ahol megtekintheti a kívánt és jelentett tulajdonságokat, és közvetlen metódusokat hívhat meg. Fontos szem előtt tartani a következőket: 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>Üzembe helyezés után: a Futtatás során felmerülő hibák diagnosztizálásának diagnosztizálása 

A IoT Edge modulhoz tartozó tároló naplófájljainak diagnosztikai adatokat kell tartalmazniuk a modul futtatókörnyezete során felmerülő hibák hibakereséséhez. A probléma megoldásához megtekintheti a [tárolói naplókat, és megvizsgálhatja](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) a problémát. 

Ha az összes korábbi ellenőrzést futtatta, és továbbra is problémákba ütközik, Gyűjtse össze a naplókat a IoT Edge eszközről [az `support bundle` paranccsal](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command) , hogy az Azure csapata további elemzéseket végezzen. A támogatási szolgálattal és az összegyűjtött naplók beküldésével [kapcsolatba léphet velünk](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) .

## <a name="common-error-resolutions"></a>Gyakori hibák megoldásai

Az élő videó elemzése IoT Edge modulként van telepítve a IoT Edge eszközön, és együttműködik a IoT Edge ügynökkel és a hub-modulokkal. Az élő videó Analytics-telepítéssel kapcsolatos gyakori hibákat az alapul szolgáló IoT-infrastruktúrával kapcsolatos problémák okozzák. A hibák a következők:

* [A IoT Edge ügynök egy perc múlva leáll](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute).
* [A IoT Edge ügynök nem fér hozzá a modul rendszerképéhez (403)](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403).
* [Az IoT Edge Agent modul "üres konfigurációs fájlt" jelent, és egyetlen modul sem indul el az eszközön](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
* [Az IoT Edge hub nem indul el](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start).
* [A IoT Edge biztonsági démon érvénytelen állomásnévvel meghiúsul](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname).
* [Az élő videó-elemzés vagy bármely más egyéni IoT Edge modul nem tud üzenetet küldeni a peremhálózati hubhoz 404 hibával](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
* [A IoT Edge modul üzembe helyezése sikeresen megtörtént, majd eltűnik az eszközről](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device).

### <a name="edge-setup-script-issues"></a>A Edge telepítési parancsfájlokkal kapcsolatos problémák

A dokumentáció részeként egy [telepítési szkriptet](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) adtunk meg a Edge-és a Felhőbeli erőforrások üzembe helyezéséhez, és megkezdheti az élő videó Analytics Edge-t. Ez a szakasz néhány parancsfájl-hibát tartalmaz, amelyek felmerülhetnek, és hibakeresési megoldásokkal együtt.

Probléma: a szkript fut, részben kevés erőforrást hoz létre, de a következő üzenettel meghiúsul:

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
1. Győződjön meg arról, hogy telepítve van a következő bővítmények. A cikk közzétételét követően a bővítmények és azok verziói a következők:

    | Mellék | Verzió |
    |---|---|
    |azure-cli   |      2.5.1|
    |parancssori modulok – nspkg         |   2.0.3|
    |Core  |    2.5.1|
    |nspkg    | 3.0.4|
    |telemetria| 1.0.4|
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. Ha telepítve van egy telepített bővítmény, amelynek verziója korábbi, mint az itt felsorolt kiadási szám, frissítse a bővítményt a következő paranccsal:

    ```
    az extension update --name <Extension name>
    ```

    Előfordulhat például, hogy futtatja a következőt: `az extension update --name azure-iot` .

### <a name="sample-app-issues"></a>Példák az alkalmazással kapcsolatos problémákra

A kiadás részeként elérhetővé tettünk néhány .NET-mintakód a fejlesztői közösségi bootstrapped beszerzéséhez. Ez a szakasz néhány olyan hibát tartalmaz, amely a mintakód futtatásakor, valamint a hibakeresési megoldásokban is felmerülhet.

Probléma: a Program.cs a következő hibával meghiúsul a közvetlen metódus meghívásakor:

```
Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
```

1. Győződjön meg arról, hogy a Visual Studio Code-környezetében telepítve van az [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) , és hogy beállította a IoT hub-hoz való kapcsolódást. Ehhez válassza a CTRL + SHIFT + P billentyűkombinációt, majd válassza a **IoT hub módszer kiválasztása**lehetőséget.

1. Ellenőrizze, hogy tud-e közvetlen metódust meghívni a IoT Edge modulban a Visual Studio Code használatával. Például hívja meg a GraphTopologyList a következő hasznos adatokkal &nbsp; : {" @apiVersion ": "1,0"}. A következő választ kell kapnia: 

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

    ![Képernyőkép a válaszról a Visual Studio Code-ban.](./media/troubleshoot-how-to/visual-studio-code1.png)
1. Ha az előző megoldás meghiúsul, próbálkozzon a következőkkel:

    a. Nyissa meg a parancssort IoT Edge eszközén, és futtassa a következő parancsot:
    
      ```
      sudo systemctl restart iotedge
      ```

      Ezzel a paranccsal újraindítható a IoT Edge eszköz és az összes modul. Várjon néhány percet, majd a közvetlen módszer ismételt használata előtt győződjön meg arról, hogy a modulok futnak a következő parancs futtatásával:

      ```
      sudo iotedge list
      ```

    b. Ha az előző módszer is meghiúsul, próbálja meg újraindítani a virtuális gépet vagy a számítógépet.

    c. Ha az összes módszer meghiúsul, futtassa a következő parancsot egy tömörített fájl beszerzéséhez az összes [releváns naplóval](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command), és csatolja azt egy [támogatási jegyhez](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

    ```
    sudo iotedge support-bundle --since 2h
    ```

1. Ha *400* -as hibakódot kap, győződjön meg arról, hogy a metódus Meghívási adattartalma megfelelően van formázva, a [közvetlen metódusok](direct-methods.md) útmutatója alapján.
1. Ha a *200* -as kódú állapotot kapja, az azt jelzi, hogy a központ jól működik, és a modul üzembe helyezése megfelelő és rugalmas. 

1. Ellenőrizze, hogy az alkalmazás konfigurációja pontos-e. Az alkalmazás konfigurációja a következő mezőkből áll a fájl *appsettings.js* . Ellenőrizze, hogy az deviceId és a moduleId pontosak-e. Az ellenőrzés egyszerű módja a Visual Studio Code Azure IoT Hub bővítmény szakasza. A fájl és a IoT Hub szakasz *appsettings.js* értékeinek egyezniük kell.
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

1. Győződjön meg arról, hogy a *appsettings.js* fájljában meg van adni a IoT hub a kapcsolatok karakterlánca, *nem* pedig a IoT hub eszköz-kapcsolatok karakterlánca, mert a [kapcsolatok karakterlánc-formátumai eltérőek](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/).

### <a name="live-video-analytics-working-with-external-modules"></a>Külső modulok használata élő videó-elemzéssel

A HTTP-bővítményt használó élő videó-elemzés kiterjesztheti az adathordozó-gráfot más IoT Edge-moduloktól származó adatok küldésére és fogadására HTTP-n keresztül a REST használatával. Ebben a [példában](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)a Media Graph képkockákat küldhet képként egy külső következtetési modulba, mint például a Yolo v3, és JSON-alapú elemzési eredményeket kap. Ilyen topológiában az események rendeltetése többnyire az IoT hub. Olyan helyzetekben, amikor nem látja a következtetési eseményeket a központban, ellenőrizze a következőket:

* Ellenőrizze, hogy a Media Graph által közzétett központ és a megtekinteni kívánt központ azonos-e. Ha több központi telepítést hoz létre, előfordulhat, hogy több hubokat is felvesz, és helytelenül vizsgálja meg a nem megfelelő hubot az eseményekhez.
* A Visual Studio Code-ban ellenőrizze, hogy a külső modul telepítve van-e és fut-e. A példában szereplő képen a rtspsim és a CV IoT Edge a lvaEdge modulon kívül futó modulok.

    ![Képernyőkép, amely megjeleníti a modulok futó állapotát az Azure IoT Hubban.](./media/troubleshoot-how-to/iot-hub.png)

* Ellenőrizze, hogy a megfelelő URL-végpontra küld-e eseményeket. A külső AI-tároló egy URL-címet és egy portot tesz elérhetővé, és visszaadja a POST kérések adatait. Ez az URL-cím `endpoint: url` a http-bővítmény processzorának tulajdonsága. Ahogy a [topológia URL-címe](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json)is látható, a végpont az URL-cím paraméterre van beállítva. Győződjön meg arról, hogy a [paraméter](http://yolov3/score) vagy az átadott érték alapértelmezett értéke pontos. Teszteléssel ellenőrizheti, hogy működik-e az ügyfél URL-címe (cURL) használatával.  

    Példaként itt látható egy olyan Yolo v3-tároló, amely a helyi gépen fut a 172.17.0.3 IP-címével. Az IP-cím megkereséséhez használja a Docker-vizsgálat lehetőséget.

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Eredmény visszaadva:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

* Ha egy vagy több, a HTTP-bővítményt használó gráf egy vagy több példányát futtatja, akkor minden HTTP-bővítmény processzora előtt meg kell jelennie a frame rate szűrőnek, hogy kezelje a videó adatcsatornájának másodpercenkénti számát (FPS). 

   Bizonyos helyzetekben, ahol a peremhálózati gép PROCESSZORa vagy memóriája magas kihasználtsággal rendelkezik, elveszítheti bizonyos következtetési eseményeket. A probléma megoldásához állítson be egy alacsony értéket a maximumFps tulajdonsághoz a frame rate szűrőben. A gráf minden egyes példányán beállíthatja a 0,5 ("maximumFps": 0,5) értékre, majd újból futtathatja a példányt, hogy az a központban megtekintse a következtetési eseményeket.

   Azt is megteheti, hogy egy nagyobb teljesítményű, nagyobb PROCESSZORral és memóriával rendelkező peremhálózati gépet szerez be.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Több közvetlen metódus párhuzamos – időtúllépési hiba 

A IoT Edge Live Video Analytics közvetlen, módszeren alapuló programozási modellt biztosít, amely lehetővé teszi több topológia és több gráf-példány beállítását. A topológia és a gráf beállításának részeként a IoT Edge modulban több közvetlen metódus hívása is meghívja őket. Ha párhuzamosan hívja meg ezeket a több metódust, különösen azokat, amelyek elindítják és leállítják a diagramokat, időtúllépési hibát tapasztalhat, például az alábbiakat: 

A szerelvény inicializálási metódusa Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync kivételt okozott. Microsoft. Azure. Devices. Common. kivétellistájára. IotHubException: Microsoft. Azure. Devices. Common. kivételek. IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

Javasoljuk, hogy a közvetlen metódusokat *ne* hívja párhuzamosan. Hívja meg őket egymás után (azaz hozzon egy közvetlen metódust csak az előző befejezése után).

### <a name="collect-logs-for-submitting-a-support-ticket"></a>Naplók gyűjtése támogatási jegy elküldéséhez

Ha az önállóan vezérelt hibaelhárítási lépések nem oldják meg a problémát, lépjen a Azure Portal, és [Nyisson meg egy támogatási jegyet](../../azure-portal/supportability/how-to-create-azure-support-request.md).

A jegyhez hozzáadni kívánt naplók összegyűjtéséhez kövesse a következő szakasz utasításait. A naplófájlokat a támogatási kérelem **részletek** ablaktábláján töltheti fel.

### <a name="use-the-support-bundle-command"></a>A support-Bundle parancs használata

Ha IoT Edge-eszközről kell összegyűjtenie a naplókat, a legegyszerűbb módszer a `support-bundle` parancs használata. Ez a parancs a következőket gyűjti:

- Modulok naplói
- A IoT Edge Security Manager és a Container Engine naplói
- Iotedge-ellenőrzési JSON-kimenet
- Hasznos hibakeresési információk

1. Futtassa a `support-bundle` parancsot a *--since* jelzővel annak megadásához, hogy mennyi idő elteltével szeretné lefedni a naplókat. Például a 2H az elmúlt két órában naplózza a naplókat. A jelző értékét módosíthatja úgy, hogy a különböző időszakokra vonatkozó naplókat tartalmazzon.

    ```
    sudo iotedge support-bundle --since 2h
    ```

   Ez a parancs létrehoz egy *support_bundle.zip* nevű fájlt abban a könyvtárban, ahol a parancsot futtatta. 
   
1. Csatolja a *support_bundle.zip* fájlt a támogatási jegyhez.

### <a name="live-video-analytics-debug-logs"></a>Élő videó-elemzési hibakeresési naplók

Ha a hibakeresési naplók létrehozásához IoT Edge modul Live Video Analytics szolgáltatását szeretné konfigurálni, tegye a következőket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg az IoT hubot.
1. A bal oldali ablaktáblán válassza a **IoT Edge**lehetőséget.
1. Az eszközök listájában válassza ki a cél eszköz AZONOSÍTÓját.
1. A panel tetején válassza a **modulok beállítása**lehetőséget.

   ![Képernyőkép a "modulok beállítása" gombra a Azure Portal.](media/troubleshoot-how-to/set-modules.png)

1. A **IoT Edge-modulok** szakaszban keresse meg és válassza a **lvaEdge**lehetőséget.
1. Válassza a **tároló létrehozási beállítások lehetőséget**.
1. A **kötések** szakaszban adja hozzá a következő parancsot:

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > Ez a parancs a peremhálózati eszköz és a tároló közötti naplók mappáját köti össze. Ha a naplókat egy másik helyen szeretné összegyűjteni, használja a következő parancsot, és cserélje le **$LOG _LOCATION_ON_EDGE_DEVICE** a használni kívánt helyre:`/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. Válassza a **Frissítés** lehetőséget.
1. Válassza a **felülvizsgálat + létrehozás**lehetőséget. Egy sikeres érvényesítési üzenet a zöld szalagcím alatt lesz közzétéve.
1. Kattintson a **Létrehozás** gombra.
1. Frissítse a **modul Identity Twin** értékét, hogy az DebugLogsDirectory paraméterre mutasson, amely arra a könyvtárra mutat, amelyben a naplók gyűjtése történik:

    a. A **modulok** táblázatban válassza a **lvaEdge**lehetőséget.  
    b. A panel tetején válassza a **modul Identity Twin**elemet. Megnyílik egy szerkeszthető ablaktábla.  
    c. A **kívánt kulcs**alatt adja hozzá a következő kulcs/érték párokat:  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > Ez a parancs a peremhálózati eszköz és a tároló közötti naplók mappáját köti össze. Ha a naplókat egy másik helyen szeretné összegyűjteni, használja a következő parancsot, és cserélje le **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** a használni kívánt helyre:  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE"`  

    d. Válassza a **Mentés** lehetőséget.

1. Reprodukálja a problémát.
1. Kapcsolódjon a virtuális géphez a portál **IoT hub** oldaláról.
1. A *debugLogs* mappában található összes fájl zip.

   > [!NOTE]
   > Ezek a naplófájlok nem az öndiagnosztika célja. Ezeket az Azure mérnöki csapatának szánták, hogy elemezzék a problémákat.

   a. A következő parancsban ne felejtse el lecserélni **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICEt** a korábban beállított peremhálózati eszközön a hibakeresési naplók helyére.  

   ```
   sudo apt install zip unzip  
   zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
   ```

   b. Csatolja a *debugLogs.zip* fájlt a támogatási jegyhez.

1. Leállíthatja a naplózási gyűjteményt úgy, hogy az értéket a **modul Identity Twin** értékével *NULL értékűre*állítja. Térjen vissza a **modul Identity Twin** oldalára, és frissítse a következő paramétert:

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>További lépések

[Oktatóanyag: eseményvezérelt videofelvétel a felhőben és a felhőből való lejátszás](event-based-video-recording-tutorial.md)
