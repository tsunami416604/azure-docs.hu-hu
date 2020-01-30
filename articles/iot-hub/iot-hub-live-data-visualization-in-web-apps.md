---
title: A IoT hub-adatai valós idejű adatvizualizációja egy webalkalmazásban
description: Egy webalkalmazás használatával megjelenítheti az érzékelőből összegyűjtött hőmérséklet-és páratartalom-adatokat, és elküldheti azokat az IOT hubhoz.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 6c7981d15acf2b2b71dfb4234f85b738efe62ce0
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76767953"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Valós idejű érzékelők adatainak megjelenítése az Azure IoT hub-ban egy webalkalmazásban

![Végpontok közötti diagram](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Ismertetett témák

Ebből az oktatóanyagból megtudhatja, hogyan jelenítheti meg a valós idejű érzékelők adatait, amelyeket az IoT hub a helyi számítógépen futó Node. js-webalkalmazással kap. A webalkalmazás helyi futtatása után igény szerint követheti a webalkalmazás futtatásának lépéseit Azure App Serviceban. Ha Power BI használatával szeretné megjeleníteni az IoT hub adatait, tekintse meg a [Power bi használata a valós idejű érzékelők adatainak megjelenítéséhez az Azure IoT hub](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Teendők

* Vegyen fel egy fogyasztói csoportot az IoT hubhoz, amelyet a webalkalmazás az érzékelő adatai olvasásához használ
* A webalkalmazás kódjának letöltése a GitHubról
* A webalkalmazás kódjának vizsgálata
* Környezeti változók konfigurálása a webalkalmazáshoz szükséges IoT Hub-összetevők tárolásához
* A webalkalmazás futtatása a fejlesztői gépen
* Nyisson meg egy weblapot az IoT hub valós idejű hőmérséklet-és páratartalom-adatainak megjelenítéséhez
* Választható A webalkalmazás üzemeltetése az Azure CLI használatával Azure App Service

## <a name="what-you-need"></a>Mi szükséges

* Fejezze be a [málna PI online szimulátor](iot-hub-raspberry-pi-web-simulator-get-started.md) oktatóanyagát vagy az eszköz egyik oktatóanyagát; például: [málna PI és Node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Ezek az alábbi követelményekre vonatkoznak:

  * Aktív Azure-előfizetés
  * Az előfizetés alá tartozó IOT hub
  * Egy ügyfélalkalmazás, amely üzeneteket küld az IOT hubhoz

* [A git letöltése](https://www.git-scm.com/downloads)

* A cikkben ismertetett lépések feltételezik a Windows fejlesztői gépeket; ezeket a lépéseket azonban egyszerűen elvégezheti az előnyben részesített rendszerhéjban található Linux rendszeren is.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

A következő parancs futtatásával adja hozzá az Azure CLI-hez készült Microsoft Azure IoT-bővítményt a Cloud Shell-példányhoz. Az IOT bővítmény a IoT Hub, IoT Edge és IoT Device kiépítési szolgáltatás (DPS) adott parancsait hozzáadja az Azure CLI-hez.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Fogyasztói csoport hozzáadása az IoT hub-hoz

A [fogyasztói csoportok](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) független nézeteket biztosítanak az esemény-adatfolyamhoz, amely lehetővé teszi, hogy az alkalmazások és az Azure-szolgáltatások egymástól függetlenül használják az adott Event hub-végpont adatait. Ebben a szakaszban egy fogyasztói csoportot ad hozzá az IoT hub beépített végpontához, amelyet a webalkalmazás az adatok olvasására fog használni.

A következő parancs futtatásával vegyen fel egy fogyasztói csoportot az IoT hub beépített végpontján:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Jegyezze fel a kiválasztott nevet, majd az oktatóanyag későbbi részében szüksége lesz rá.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>IoT hub szolgáltatáshoz tartozó szolgáltatási kapcsolatok karakterláncának beolvasása

A IoT hubok több alapértelmezett hozzáférési házirenddel jönnek létre. Az egyik ilyen szabályzat a **szolgáltatási** szabályzat, amely megfelelő engedélyeket biztosít a szolgáltatás számára az IoT hub végpontjának olvasásához és írásához. Futtassa a következő parancsot, hogy lekérje a IoT hub kapcsolódási karakterláncát, amely megfelel a szolgáltatási házirendnek:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

A kapcsolódási karakterláncnak a következőhöz hasonlóan kell kinéznie:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Jegyezze fel a szolgáltatási kapcsolatok karakterláncát, ezért az oktatóanyag későbbi részében szüksége lesz rá.

## <a name="download-the-web-app-from-github"></a>A webalkalmazás letöltése a GitHubról

Nyisson meg egy parancssorablakot, és írja be a következő parancsokat a minta GitHubról való letöltéséhez és a minta könyvtárra való váltáshoz:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>A webalkalmazás kódjának vizsgálata

A Web-Apps-Node-IOT-hub-adatvizualizáció könyvtárában nyissa meg a webalkalmazást a kedvenc szerkesztőjében. Az alábbi ábrán a VS Code-ban megtekintett fájl szerkezete látható:

![Webalkalmazás-fájl szerkezete](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Szánjon egy kis időt a következő fájlok vizsgálatára:

* A **Server. js** egy olyan szolgáltatás-oldali parancsfájl, amely a webes szoftvercsatornát és az Event hub burkoló osztályát inicializálja. Visszahívást biztosít az Event hub burkoló osztályához, amelyet az osztály a bejövő üzenetek webes szoftvercsatornára való szórására használ.

* A **Event-hub-Reader. js** egy olyan szolgáltatás-oldali parancsfájl, amely az IoT hub beépített végpontját a megadott kapcsolati sztring és fogyasztói csoport használatával köti össze. Kibontja a DeviceId és a EnqueuedTimeUtc a beérkező üzenetek metaadataiból, majd továbbítja az üzenetet a Server. js által regisztrált visszahívási módszer használatával.

* A **chart-Device-Data. js** egy ügyféloldali parancsfájl, amely figyeli a webes szoftvercsatornát, nyomon követi az egyes DeviceID-ket, és a bejövő adat utolsó 50 pontját tárolja minden eszközön. Ezután a kiválasztott eszközhöz tartozó adategységeket a diagram objektumhoz köti.

* Az **index. html** kezeli a weblap felhasználói felületének elrendezését, és az ügyféloldali logikához szükséges parancsfájlokra hivatkozik.

## <a name="configure-environment-variables-for-the-web-app"></a>Környezeti változók konfigurálása a webalkalmazáshoz

Az IoT hub adatainak beolvasásához a webalkalmazásnak szüksége van az IoT hub kapcsolati karakterláncára és annak a fogyasztói csoportnak a nevére, amelyet át kell olvasnia. Ezeket a karakterláncokat a Server. js következő soraiban található folyamat-környezetből kapja meg:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Állítsa be a környezeti változókat a parancsablakban a következő parancsokkal. Cserélje le a helyőrző értékeket az IoT hub szolgáltatási kapcsolatok karakterláncára, valamint a korábban létrehozott fogyasztói csoport nevére. Ne idézze a karakterláncokat.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>A webalkalmazás futtatása

1. Győződjön meg arról, hogy az eszköz fut és adatokat küld.

2. A parancssorablakban futtassa a következő sorokat a hivatkozott csomagok letöltéséhez és telepítéséhez, majd indítsa el a webhelyet:

   ```cmd
   npm install
   npm start
   ```

3. Meg kell jelennie a kimenetnek a konzolon, amely azt jelzi, hogy a webalkalmazás sikeresen csatlakozott az IoT hub-hoz, és a 3000-es porton figyeli a következőt:

   ![A webalkalmazás elindult a konzolon](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Nyisson meg egy weblapot az IoT hub adatainak megjelenítéséhez

Nyisson meg egy böngészőt `http://localhost:3000`.

Az eszköz **kiválasztása** listából válassza ki az eszközt, és tekintse meg az eszköz által az IoT hubhoz eljuttatott utolsó 50 hőmérséklet-és páratartalom-adatpontok egy futó ábráját.

![Webalkalmazás lap valós idejű hőmérsékletet és páratartalmat mutat](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

Azt is meg kell jelennie a konzolon, hogy a webalkalmazás által a böngésző ügyfelének küldött üzenetek megjelenjenek:  

![A webalkalmazás szórási kimenete a konzolon](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>A webalkalmazás üzemeltetése App Service

A [Azure App Service Web Apps szolgáltatása](https://docs.microsoft.com/azure/app-service/overview) a webalkalmazások üzemeltetésére szolgáló platformként szolgál (a Pásti). A Azure App Serviceban üzemeltetett webalkalmazások kihasználhatják a hatékony Azure-funkciókat, például a további biztonságot, a terheléselosztást és a méretezhetőséget, valamint az Azure-és partneri DevOps-megoldásokat, például a folyamatos üzembe helyezést, a csomagok felügyeletét stb. Azure App Service támogatja a számos népszerű nyelven fejlesztett és Windows-vagy Linux-infrastruktúrán üzembe helyezett webalkalmazást.

Ebben a szakaszban egy webalkalmazást hoz létre a App Serviceban, és üzembe helyezi a kódját az Azure CLI-parancsok használatával. Az az [WebApp](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) dokumentációjában használt parancsok részleteit megtekintheti. Mielőtt elkezdené, győződjön meg arról, hogy elvégezte az [erőforráscsoport hozzáadásának](#add-a-consumer-group-to-your-iot-hub)lépéseit az IoT hub-hoz, [szerezzen be egy szolgáltatás-kapcsolati karakterláncot az IoT hub számára](#get-a-service-connection-string-for-your-iot-hub), és [töltse le a webalkalmazást a githubról](#download-the-web-app-from-github).

1. Az [app Service-csomag](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) számítási erőforrásokat határoz meg app Service által futtatott alkalmazáshoz. Ebben az oktatóanyagban a fejlesztő/ingyenes szintet használjuk a webalkalmazás üzemeltetéséhez. Az ingyenes szinten a webalkalmazás a megosztott Windows-erőforrásokon fut más App Service alkalmazásokkal, többek között más ügyfelek alkalmazásaival. Az Azure App Service terveket is kínál a webalkalmazások Linux számítási erőforrásokon való üzembe helyezéséhez. Ezt a lépést kihagyhatja, ha már rendelkezik a használni kívánt App Service-csomaggal.

   Ha App Service csomagot szeretne létrehozni a Windows ingyenes szintjével, futtassa a következő parancsot. Használja ugyanazt az erőforráscsoportot, amelyben az IoT hub található. A szolgáltatási csomag neve kis-és nagybetűket, számokat és kötőjeleket tartalmazhat.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Most hozzon létre egy webalkalmazást a App Service tervben. A `--deployment-local-git` paraméter lehetővé teszi a webalkalmazás kódjának feltöltését és telepítését a helyi gépen található git-tárházból. A webalkalmazás nevének globálisan egyedinek kell lennie, és kis-és nagybetűket, számokat és kötőjeleket tartalmazhat. Ügyeljen arra, hogy a Node. js-futtatókörnyezet 10,6-es vagy újabb verzióját használja a `--runtime` paraméterhez. A `az webapp list-runtimes` parancs használatával lekérheti a támogatott futtatókörnyezetek listáját.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. Most adja meg az IoT hub kapcsolati karakterláncot és az Event hub fogyasztói csoportot megadó környezeti változók alkalmazás-beállításait. Az egyes beállítások a `-settings` paraméterben vannak elválasztva. Használja a IoT hub szolgáltatási kapcsolatok karakterláncát és az oktatóanyagban korábban létrehozott fogyasztói csoportot. Ne idézze az értékeket.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. Engedélyezze a web Sockets protokollt a webalkalmazáshoz, és állítsa be úgy a webalkalmazást, hogy csak HTTPS-kérelmeket fogadjon (a HTTP-kérelmeket a rendszer a HTTPS-re irányítja át)

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. A kód App Serviceba való üzembe helyezéséhez [felhasználói szintű központi telepítési hitelesítő adatait](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials)fogja használni. A felhasználói szintű központi telepítési hitelesítő adatai eltérnek az Azure-beli hitelesítő adataitól, és a git helyi és FTP-környezetekben való üzembe helyezéséhez használatosak a webalkalmazások számára. A beállítás után az Azure-fiók összes előfizetésében érvényesek lesznek az összes App Service alkalmazásban. Ha korábban beállította a felhasználói szintű központi telepítési hitelesítő adatokat, használhatja azokat.

   Ha korábban még nem állította be a felhasználói szintű központi telepítési hitelesítő adatokat, vagy nem emlékszik a jelszavára, futtassa a következő parancsot. Az üzembe helyezési felhasználónévnek egyedinek kell lennie az Azure-ban, és nem tartalmazhatja a "@" szimbólumot a helyi git leküldéséhez. Amikor a rendszer kéri, írja be és erősítse meg az új jelszót. A jelszónak legalább nyolc karakterből kell állnia, és a következő három elem közül kettőnek kell lennie: betűk, számok és szimbólumok.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Kérje meg a git URL-címét, hogy a kódot a App Serviceba küldje el.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Adjon hozzá egy távolit a klónhoz, amely a App Service webalkalmazás git-tárházára hivatkozik. \<git-klón URL-címéhez\>használja az előző lépésben visszaadott URL-címet. Futtassa a következő parancsot a parancsablakban.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. A kód App Serviceba való telepítéséhez írja be a következő parancsot a parancsablakban. Ha a rendszer hitelesítő adatokat kér, adja meg az 5. lépésben létrehozott felhasználói szintű központi telepítési hitelesítő adatokat. Győződjön meg arról, hogy leküldi a App Service távoli főágra.

    ```cmd
    git push webapp master:master
    ```

9. A központi telepítés előrehaladása a parancsablakban fog frissülni. A sikeres üzembe helyezés a következő kimenethez hasonló sorokkal végződik:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. Futtassa a következő parancsot a webalkalmazás állapotának lekéréséhez, és győződjön meg arról, hogy az fut:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. Egy böngészőben nyissa meg a `https://<your web app name>.azurewebsites.net` oldalt. A webalkalmazás helyi futtatásakor megjelenő weboldalhoz hasonló weblap. Feltételezve, hogy az eszköz fut és adatokat küld, látnia kell az eszköz által küldött, a 50 legutóbbi hőmérséklet-és páratartalom-olvasások futó ábráját.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha bármilyen probléma merül fel ezzel a mintával, próbálkozzon az alábbi részekben ismertetett lépésekkel. Ha továbbra is problémákat tapasztal, küldjön visszajelzést a témakör alján.

### <a name="client-issues"></a>Ügyfelekkel kapcsolatos problémák

* Ha egy eszköz nem jelenik meg a listában, vagy nem készül gráf, ellenőrizze, hogy az eszközön fut-e a kód.

* A böngészőben nyissa meg a fejlesztői eszközöket (számos böngészőben az F12 billentyű megnyithatja), és keresse meg a konzolt. Keresse meg az ott kinyomtatott figyelmeztetéseket és hibákat.

* Az ügyféloldali szkriptek hibakeresését a/JS/chat-Device-Data.js.-ben végezheti el

### <a name="local-website-issues"></a>Helyi webhely problémái

* Tekintse meg az ablakban azt a kimenetet, amelyen a konzol kimenetéhez tartozó csomópontot indította.

* A kiszolgáló kódjának, különösen a Server. js és a/scripts/Event-hub-Reader.js. hibakeresése

### <a name="azure-app-service-issues"></a>Azure App Service problémák

* A Azure Portalban nyissa meg a webalkalmazást. A bal oldali ablaktábla **figyelés** területén válassza a **app Service naplók**lehetőséget. Kapcsolja be az **alkalmazás naplózása (fájlrendszer)** beállítást be értékre, állítsa a hiba **szintet** , majd kattintson a **Mentés**gombra. Ezután nyissa meg a **naplózási adatfolyamot** (a **figyelés**alatt).

* A Azure Portal webalkalmazásában a **fejlesztői eszközök** területen válassza a **konzol** lehetőséget, és ellenőrizze a csomópont-és NPM verzióit `node -v` és `npm -v`.

* Ha a csomag nem talál hibát, lehetséges, hogy a lépéseket a sorrend szerint futtatta. Ha a hely telepítve van (`git push`), az App Service `npm install`fut, amely az általa konfigurált csomópont aktuális verziója alapján fut. Ha később módosítja a konfigurációt, értelmetlen módosítást kell végeznie a kódban, és újra le kell küldenie.

## <a name="next-steps"></a>Következő lépések

Sikeresen használta a webalkalmazást az IoT hub valós idejű érzékelői adatainak megjelenítéséhez.

Az Azure IoT Hubból származó adatok megjelenítésének másik módja: [a Power bi használata a valós idejű érzékelők adatainak megjelenítéséhez az IoT hub-ból](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
