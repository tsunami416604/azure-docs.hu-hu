---
title: A webalkalmazás az Azure IoT hub érzékelői adatainak valós idejű vizualizációját |} A Microsoft Docs
description: Egy webalkalmazás használatával jelenítheti meg, amely egy érzékelő összegyűjtött és az Iot hubnak küldött hőmérséklettel és páratartalommal kapcsolatos adatokat.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 22b15a95e529d4f09560e9b7e59d9f78f70651bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476003"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Egy webalkalmazás az Azure IoT hub valós idejű érzékelői adatainak megjelenítése

![Végpontok közötti diagram](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Ismertetett témák

Ebben az oktatóanyagban elsajátíthatja, hogyan jelenítheti meg az IoT hub által fogadott a helyi számítógépen futó node.js-webalkalmazásokba az valós idejű érzékelői adatokat. Miután helyileg futtatja a web app, igény szerint lépésekkel Azure App Service-ben a webalkalmazás üzemeltetéséhez. Ha azt szeretné, próbálja meg az IoT hub az adatok megjelenítése Power BI használatával kapcsolatban lásd: [Power BI segítségével az Azure IoT Hub valós idejű érzékelői adatainak megjelenítése](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>TEENDŐ

* Egy fogyasztói csoportot adja hozzá az IoT hubhoz, amelynek használatával a webes alkalmazás érzékelőktől kapott adatok olvasása
* Töltse le a webalkalmazás kódját a Githubról
* Vizsgálja meg a webalkalmazás kódját
* Az IoT Hub a webalkalmazás által igényelt összetevők tárolására környezeti változók konfigurálása
* A webalkalmazás futtatása a fejlesztői gépen
* Nyisson meg egy weblapot, valós idejű hőmérséklettel és páratartalommal kapcsolatos adatokat az IoT hubról
* (Nem kötelező) Azure CLI használatával az Azure App Service-ben a webalkalmazás üzemeltetéséhez

## <a name="what-you-need"></a>Mi szükséges

* Végezze el a [Raspberry Pi online szimulátor](iot-hub-raspberry-pi-web-simulator-get-started.md) oktatóanyag vagy az eszköz oktatóanyagokban; például [Raspberry Pi és node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Ez érvényes a következő követelményeknek:

  * Aktív Azure-előfizetéssel
  * Az Iot hub, az előfizetéséhez
  * Egy ügyfélalkalmazás, amely üzeneteket küld az Iot hubhoz

* [A Git letöltése](https://www.git-scm.com/downloads)

* Ez a cikk lépései azt feltételezik, egy Windows fejlesztési machine lehetőségekre kattintok. azonban egyszerűen végrehajthatja ezeket a lépéseket a Linux rendszeren az előnyben részesített felületen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Futtassa a következő parancsot a Microsoft Azure IoT-bővítmény hozzáadása a Cloud Shell-példány Azure CLI-hez. Az IOT-bővítmény hozzáadása Azure CLI-vel az IoT Hub, IoT Edge és IoT Device Provisioning Service (DPS) parancsok.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Az IoT hub fogyasztói csoport hozzáadása

[Fogyasztói csoportok](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) független nézeteit, az eseménystream, amelyek lehetővé teszik az alkalmazások és az Azure-szolgáltatások egymástól függetlenül a tárolt adatokat használják az azonos Eseményközpont-végpontra. Ebben a szakaszban hozzáadja az IoT hub beépített végpont, amely a web app használatával fogja olvasni az adatokat a fogyasztói csoportot.

Futtassa a következő parancsot a beépített végponthoz az IoT hub fogyasztói csoport hozzáadása:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Megjegyzés: a választott név le, szüksége lesz rá az oktatóanyag későbbi részében.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Az IoT hub szolgáltatás kapcsolati karakterlánc beszerzése

IoT-központok több alapértelmezett hozzáférési házirendekkel jönnek létre. Egy ilyen szabályzat a **szolgáltatás** szabályzatot, amely a megfelelő engedélyeket biztosít az írási és olvasási végpontokat az IoT hub szolgáltatás. A következő parancsot az IoT hubnak, amely elfogadja a szolgáltatás szabályzat egy kapcsolati sztring lekérése:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

A kapcsolati karakterláncot a következőhöz hasonlóan kell kinéznie:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Jegyezze fel a szolgáltatáskapcsolati karakterlánc, az oktatóanyag későbbi részében szüksége lesz rá.

## <a name="download-the-web-app-from-github"></a>Töltse le a webalkalmazást a Githubról

Nyisson meg egy parancsablakot, és töltse le a mintát a Githubon, és váltson a minta a következő parancsokat:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Vizsgálja meg a webalkalmazás kódját

A web-apps-node-iot-hub-data-visualization könyvtárból nyissa meg a webalkalmazás egy tetszőleges szerkesztőben. Az alábbiakban látható a fájlstruktúra a VS Code tekinthetők meg:

![Webes alkalmazás fájlstruktúra](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Szánjon egy kis időt a következő fájlokat vizsgálja meg:

* **Server.js** Szolgáltatásoldali parancsfájl a inicializálja a web socket és az Event Hub-burkoló osztály. Egy visszahívás az Event Hubs burkoló osztály, amely az osztályt használja a web socket bejövő üzenetek szórási tartalmaz.

* **Esemény-hub-reader.js** egy Szolgáltatásoldali parancsfájl, amely csatlakozik az IoT hub használata a megadott kapcsolati karakterlánc és a fogyasztói csoport beépített végpont. Ez az eszköz azonosítójával és EnqueuedTimeUtc kigyűjti a metaadatokat a bejövő üzenetek, és ezután továbbítja az üzenet server.js által regisztrált visszahívási metódus használatával.

* **Diagram-device-data.js** egy ügyféloldali parancsfájl, amely a web socket-as, nyomon követi az egyes az eszközazonosító és a legutóbbi 50 pontok a bejövő adatok minden egyes eszközhöz tárol. Majd ennek a kijelölt eszközadatokat nA Objekt grafu.

* **Index.HTML** kezeli a felhasználói felület elrendezés a weblap és az ügyféloldali logikát a szükséges szkriptek hivatkozik.

## <a name="configure-environment-variables-for-the-web-app"></a>A webalkalmazáshoz tartozó környezeti változók konfigurálása

Adatokat olvasni az IoT hub, a web app az IoT hub kapcsolati karakterláncot, és a fogyasztói csoportot, amely keresztül olvassa el a neve van szüksége. Ezek a karakterláncok kap a folyamat környezetből a server.js a következő sorokat:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

A környezeti változók értékét az alábbi parancsokkal a parancsablakban. A helyőrző értékeket cserélje le a szolgáltatáskapcsolati karakterláncát az IoT hub és a korábban létrehozott fogyasztói csoport neve. A karakterláncok nem ajánlat.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>A webalkalmazás futtatása

1. Győződjön meg arról, hogy az eszköz fut, és az adatok küldésének.

2. A parancssori ablakban futtassa a következő sorokat tölthet le és telepítse a hivatkozott csomagokat, és indítsa el a webhelyet:

   ```cmd
   npm install
   npm start
   ```

3. A konzolon, amely jelzi, hogy a webalkalmazás sikeresen csatlakozott az IoT hubhoz, és figyel a következőn: port 3000 kimenetnek kell megjelennie:

   ![Webalkalmazás-konzolon a következőn:](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Nyisson meg egy weblapot, az IoT hub adatainak megtekintéséhez

Nyisson meg egy böngészőt, és `http://localhost:3000`.

Az a **jelöljön ki egy eszközt** listában, válassza ki az eszközt, tekintse meg a legutóbbi 50 futó rajz az IoT hub által az eszközre elküldött adatpontok hőmérséklettel és páratartalommal kapcsolatos.

![Valós idejű hőmérséklettel és páratartalommal kapcsolatos megjelenítő alkalmazás weblap](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

Akkor is kimenetnek kell megjelennie, amely a böngészőalapú ügyfél, a webalkalmazás sugárzó üzeneteket jeleníti meg a konzolon:  

![Webalkalmazás szórási kimenetet a konzolon](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Az App Service-ben a webalkalmazás üzemeltetéséhez

A [Azure App Service Web Apps funkciójával](https://docs.microsoft.com/azure/app-service/overview) platformot nyújt, a platformszolgáltatás (PAAS) a webalkalmazás üzemeltetéséhez. Az Azure App Service-ben üzemeltetett webalkalmazások a további biztonsági, terheléselosztási, például nagy teljesítményű Azure-funkciók előnyeit, és méretezhetőséget kínál, csakúgy, mint az Azure és a fejlesztési és üzemeltetési partneri megoldások, mint a folyamatos üzembe helyezés, a felügyeleti csomagot, és így tovább. Az Azure App Service számos népszerű nyelveken fejlesztett és a Windows vagy Linux-alapú infrastruktúra üzembe helyezett webalkalmazások támogatja.

Ebben a szakaszban webalkalmazás üzembe helyezése az App Service-ben, és a kód telepítése az Azure CLI-parancsok használatával. Találhat további információkat a használt parancsok a [az webapp](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) dokumentációját. Mielőtt elkezdené, győződjön meg arról, végrehajtotta a lépéseket [adjon hozzá egy erőforráscsoportot az IoT hubhoz](#add-a-consumer-group-to-your-iot-hub), [egy szolgáltatás kapcsolati karakterláncának lekérése az IoT hub](#get-a-service-connection-string-for-your-iot-hub), és [töltse le a webalkalmazást a Githubról](#download-the-web-app-from-github).

1. Egy [App Service-csomag](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) határozza meg az alkalmazás futtatása az App Service-ben üzemeltetett számítási erőforrásokat. Ebben az oktatóanyagban a fejlesztői/ingyenes szint használjuk a webalkalmazás üzemeltetéséhez. Az ingyenes szinten a webalkalmazás megosztott Windows erőforrások más App Service-alkalmazások, beleértve a más ügyfelek alkalmazásokat futtat. Az Azure is App Service-csomagok üzembe helyezése a Linuxos web apps ajánlatok a számítási erőforrásokat. Ezt a lépést kihagyhatja, ha már van egy App Service-csomag, amelyet használni szeretne.

   A Windows ingyenes szint használata App Service-csomag létrehozásához futtassa az alábbi parancsot. Használja az IoT hub szerepel ugyanabban az erőforráscsoportban. A service-csomag neve a felső és kisbetűket, számokat és kötőjeleket tartalmazhat.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Most már webalkalmazás üzembe helyezése az App Service-csomagban. A `--deployment-local-git` paraméter lehetővé teszi, hogy a webalkalmazás kódját feltöltött és telepített egy Git-adattárból a helyi gépen. A webalkalmazás nevének globálisan egyedinek kell lennie, és a felső és kisbetűket, számokat és kötőjeleket tartalmazhat.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --deployment-local-git
   ```

3. Most adjon hozzá alkalmazásbeállításokat környezeti változók, amely az IoT hub kapcsolati karakterláncra és az eseményközpont fogyasztói csoportjának megadása. Egyéni beállítások szóközzel elválasztott a rendszer a `-settings` paraméter. A szolgáltatás kapcsolati karakterláncának használata az IoT hub és a fogyasztói csoportot, ebben az oktatóanyagban korábban létrehozott. Az értékek nem ajánlat.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. Engedélyezze a Websockets protokoll a web app for, és állítsa be a webalkalmazás fogadni a HTTPS-kérelmek csak (HTTP-kérelmeket a rendszer átirányítja HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. A kód üzembe helyezése az App Service, hogy szeretné használni a [felhasználói szintű üzembe helyezési hitelesítő adatok](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials). A felhasználói szintű üzembe helyezési hitelesítő adatok különböznek az Azure hitelesítő adatait, és használható a helyi Git és a egy webalkalmazás FTP-központitelepítése. Beállítása után még érvényes összes az Azure-fiókjában található összes előfizetés az App Service-alkalmazások. Ha korábban már beállította a felhasználói szintű üzembe helyezési hitelesítő adatok, használhatja őket.

   Ha korábban nem állított be a felhasználói szintű üzembe helyezési hitelesítő adatok, vagy nem emlékszik a jelszavára, futtassa a következő parancsot. A központi telepítés felhasználói nevében Azure-on belül egyedinek kell lennie, és nem tartalmazhatja a "@" a helyi Git leküldések szimbólum. Amikor a rendszer kéri, adja meg, és erősítse meg az új jelszót. A jelszónak legalább nyolc karakter hosszú, a következő három elem közül kell lennie: betűk, számok és szimbólumok.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Küldje be a kódot, akár az App Service használatával a Git URL-cím beolvasása.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Adja hozzá a Klónozás által hivatkozott App Service-ben a webalkalmazáshoz tartozó Git-tárház egy távoli. A \<Git clone URL-cím\>, használja az URL-címet adja vissza az előző lépésben. Futtassa a következő parancsot a parancssori ablakba.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. A kód üzembe helyezése az App Service-ben, adja meg a következő parancsot a parancssori ablakba. Ha a hitelesítő adatokat kéri, adja meg a felhasználói szintű üzembe helyezési hitelesítő adatok, az 5. lépésben létrehozott. Ellenőrizze, hogy küldje le a főágba távoli App Service-ben.

    ```cmd
    git push webapp master:master
    ```

9. Az üzembe helyezés folyamatát frissíteni fogja a parancssori ablakba. Üzembe helyezés sikerességét az alábbi kimenet hasonló sorokkal végződik:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. Futtassa a webalkalmazás állapotának lekérdezése, és ellenőrizze, hogy fut-e a következő parancsot:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. Egy böngészőben nyissa meg a `https://<your web app name>.azurewebsites.net` oldalt. A hasonló weblap látott futtatásakor a webalkalmazást helyileg jeleníti meg. Feltételezve, hogy az eszköz fut, és az adatok küldésének, megtekintheti az 50 legutóbbi hőmérséklettel és páratartalommal kapcsolatos olvasmányok az eszköz által küldött futó ábrázolása.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha problémák ezt a mintát között, próbálja meg a következő szakaszok lépéseit. Ha a probléma továbbra is fennáll, küldjön visszajelzést a témakör alján.

### <a name="client-issues"></a>Ügyfél-problémák

* Ha egy eszköz nem jelenik meg a listában, vagy nincs a diagram megrajzolása folyamatban van, győződjön meg arról, az eszköz kódja fut az eszközön.

* A böngészőben nyissa meg a fejlesztői eszközök (számos böngészőben az F12 billentyű nyílik meg,), és keresse meg a konzolon. Keresse meg az összes figyelmeztetést és hibát van nyomtatva.

* /Js/chat-device-data.js ügyféloldali parancsfájlok is hibakeresése.

### <a name="local-website-issues"></a>Helyi webhely kapcsolatos problémák

* Tekintse meg a kimeneti ablakban ahonnét elindította a konzol kimenete csomópont.

* A kiszolgálói kód, kifejezetten a server.js és /scripts/event-hub-reader.js hibakeresést.

### <a name="azure-app-service-issues"></a>Az Azure App Service-problémák

* Az Azure Portalon lépjen a webalkalmazáshoz. A **figyelés** a bal oldali panelen válassza ki a **App Service-naplók**. Kapcsolja be **Application Logging (fájlrendszer)** , beállításához **szint** hiba, és válassza ki a **mentése**. Nyissa meg **naplófolyam** (alatt **figyelés**).

* Az Azure Portalon a webalkalmazás alatt **Fejlesztőeszközök** válassza **konzol** , és ellenőrizze a csomópont és az npm verziók `node -v` és `npm -v`.

* Ha nem találja a csomag kapcsolatos hibaüzenet jelenik meg, előfordulhat, hogy a lépések sorrendje futtatta. Ha a hely telepítve van (a `git push`) az app service fut `npm install`, mely alapján a csomópont aktuális verziója fut. azt konfigurálva van. Ha később konfigurációban, hogy megváltozik, szüksége összegnek nincs valós jelentése módosítsa annak a kódot, és küldje le újra.

## <a name="next-steps"></a>További lépések

A webalkalmazás sikeresen felhasználta az IoT hub valós idejű érzékelői adatainak megjelenítése.

A másik lehetőség az Azure IoT Hub adatainak megjelenítése, lásd: [Power BI segítségével az IoT hub valós idejű érzékelői adatainak megjelenítése](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
