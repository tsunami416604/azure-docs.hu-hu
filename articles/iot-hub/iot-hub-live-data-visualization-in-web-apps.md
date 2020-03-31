---
title: Az IoT hub-adatok valós idejű adatvizualizációja egy webalkalmazásban
description: Webalkalmazás használatával vizualizálja a hőmérséklet és a páratartalom adatokat, amelyeket egy érzékelő, és elküldte a sok hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 138e077f7b47fa9f38a4710db95eb7208cef78e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675322"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Valós idejű érzékelőadatok megjelenítése az Azure IoT hubról egy webalkalmazásban

![Végpontok között diagram](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Ismertetett témák

Ebben az oktatóanyagban megtudhatja, hogyan jelenítheti meg az IoT hub által a helyi számítógépen futó node.js webalkalmazással kapott valós idejű érzékelőadatokat. A webalkalmazás helyi futtatása után szükség esetén kövesse a webalkalmazás üzemeltetésére irányuló lépéseket az Azure App Service-ben. Ha a Power BI használatával szeretné megpróbálni megvizivizíteni az adatokat az IoT-központban, olvassa el [a Power BI használata az Azure IoT Hub valós idejű érzékelőadatainak megjelenítéséhez](iot-hub-live-data-visualization-in-power-bi.md)című témakört.

## <a name="what-you-do"></a>Mit csinálsz

* Fogyasztói csoport hozzáadása az IoT-központhoz, amelyet a webalkalmazás az érzékelőadatok olvasásához használ
* A webalkalmazás kódjának letöltése a GitHubról
* A webalkalmazás kódjának vizsgálata
* Környezeti változók konfigurálása a webalkalmazás által szükséges IoT Hub-összetevők tárolására
* A webalkalmazás futtatása a fejlesztőgépen
* Nyisson meg egy weboldalt, hogy valós idejű hőmérséklet- és páratartalom-adatokat láthasson az IoT-központból
* (Nem kötelező) Webalkalmazás üzemeltetése az Azure App Service-ben az Azure CLI használatával

## <a name="what-you-need"></a>Mi szükséges

* Töltse ki a [Raspberry Pi online szimulátor](iot-hub-raspberry-pi-web-simulator-get-started.md) oktatóanyagát vagy az eszköz oktatóanyagait; például [a Raspberry Pi a node.js-szel.](iot-hub-raspberry-pi-kit-node-get-started.md) Ezek a következő követelményekre vonatkoznak:

  * Aktív Azure-előfizetés
  * Egy sok elosztó az előfizetés alatt
  * Egy ügyfélalkalmazás, amely üzeneteket küld a Sok-központnak

* [Git letöltése](https://www.git-scm.com/downloads)

* A cikkben ismertetett lépések windowsos fejlesztői gépet feltételeznek; azonban könnyedén végrehajthatja ezeket a lépéseket egy Linux rendszeren a kívánt rendszerhéjban.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Futtassa a következő parancsot, és adja hozzá a Microsoft Azure IoT-bővítményt az Azure CLI-hez a Cloud Shell-példányhoz. Az IOT-bővítmény hozzáadja az IoT Hub, az IoT Edge és az IoT-eszközlétesítési szolgáltatás (DPS) adott parancsokat az Azure CLI-hez.

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Fogyasztói csoport hozzáadása az IoT-központhoz

[A fogyasztói csoportok](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) független nézeteket biztosítanak az eseményfolyamba, amelyek lehetővé teszik, hogy az alkalmazások és az Azure-szolgáltatások egymástól függetlenül használják fel az adatokat ugyanabból az Event Hub-végpontból. Ebben a szakaszban hozzáad egy fogyasztói csoportot az IoT hub beépített végponthoz, amelyből a webalkalmazás adatokolvasáshoz fog használni.

A következő paranccsal hozzáadhat egy fogyasztói csoportot az IoT-központ beépített végpontjához:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Jegyezze fel a választott nevet, szüksége lesz rá az oktatóanyag későbbi részében.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Szolgáltatáskapcsolat-karakterlánc beszereznie az IoT hubhoz

Az IoT-központok számos alapértelmezett hozzáférési szabályzattal jönnek létre. Az egyik ilyen szabályzat a **szolgáltatásházirend,** amely megfelelő engedélyeket biztosít egy szolgáltatás számára az IoT hub végpontjainak olvasásához és írásához. Futtassa a következő parancsot az IoT hub hoz tartozó, a szolgáltatásszabályzathoz betartott kapcsolati karakterlánc lekérni:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

A kapcsolati karakterláncnak a következőhöz hasonlóan kell kinéznie:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Vegye figyelembe a szolgáltatás kapcsolati karakterlánc, szüksége lesz rá később az oktatóanyagban.

## <a name="download-the-web-app-from-github"></a>A webalkalmazás letöltése a GitHubról

Nyisson meg egy parancsablakot, és írja be a következő parancsokat a minta Letöltéséhez a GitHubról, és váltson a mintakönyvtárra:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>A webalkalmazás kódjának vizsgálata

A web-apps-node-iot-hub-data-visualization könyvtárból nyissa meg a webalkalmazást a kedvenc szerkesztőjében. A következőkben a VS-kódban megtekintett fájlstruktúra látható:

![Webalkalmazás-fájl szerkezete](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Szánjon egy kis időbe, hogy megvizsgálja a következő fájlokat:

* **A Server.js** egy szolgáltatásoldali parancsfájl, amely inicializálja a webszoftvercsatornát és az Event Hub burkoló osztályát. Visszahívást biztosít az Event Hub burkoló osztályához, amelyet az osztály a bejövő üzenetek webszoftvercsatornára való közvetítésére használ.

* **Az Event-hub-reader.js** egy szolgáltatásoldali parancsfájl, amely a megadott kapcsolati karakterlánc és fogyasztói csoport használatával csatlakozik az IoT hub beépített végpontjához. Kinyeri a DeviceId és enqueuedTimeUtc metaadatok a bejövő üzenetek, majd továbbítja az üzenetet a visszahívási módszer által regisztrált server.js.

* **A Chart-device-data.js** egy ügyféloldali parancsfájl, amely figyeli a webszoftvert, nyomon követi az egyes DeviceId-ket, és tárolja az egyes eszközök beérkező adatainak utolsó 50 pontját. Ezután a kijelölt eszközadatokat a diagramobjektumhoz köti.

* **Az Index.html** kezeli a weblap felhasználói felületének elrendezését, és hivatkozik az ügyféloldali logikához szükséges parancsfájlokra.

## <a name="configure-environment-variables-for-the-web-app"></a>Környezeti változók konfigurálása a webalkalmazáshoz

Adatok olvasásához az IoT hub, a webalkalmazás szüksége van az IoT hub kapcsolati karakterláncés a fogyasztói csoport nevét, amelyet át kell olvasnia. Ezeket a karakterláncokat a process-környezetből a server.js következő soraiból kapja le:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Állítsa be a környezeti változókat a parancsablakban a következő parancsokkal. Cserélje le a helyőrző értékeket az IoT hub szolgáltatáskapcsolati karakterláncával és a korábban létrehozott fogyasztói csoport nevével. Ne idézd a húrokat.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>A webalkalmazás futtatása

1. Ellenőrizze, hogy az eszköz fut-e és küld-e adatokat.

2. A parancsablakban futtassa a következő sorokat a hivatkozott csomagok letöltéséhez és telepítéséhez, és indítsa el a webhelyet:

   ```cmd
   npm install
   npm start
   ```

3. A konzolon olyan kimenetnek kell lennie, amely azt jelzi, hogy a webalkalmazás sikeresen csatlakozott az IoT-központhoz, és a 3000-es porton figyel:

   ![A webalkalmazás a konzolon indult](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Nyisson meg egy weblapot az IoT-központból származó adatok megtekintéséhez

Nyisson meg `http://localhost:3000`egy böngészőt a számára.

Az **Eszköz kiválasztása** listában válassza ki az eszközt az eszköz által az IoT hubra küldött utolsó 50 hőmérséklet- és páratartalom-adatpont futó ábrázolásához.

![A valós idejű hőmérsékletet és páratartalmat megjelenítő webalkalmazás-lap](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

A konzolon is látnia kell a kimenetet, amely megjeleníti a webalkalmazás által a böngészőügyfélnek közvetített üzeneteket:  

![Webalkalmazás-közvetítés kimenete a konzolon](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>A webalkalmazás üzemeltetése az App Service-ben

Az [Azure App Service Web Apps szolgáltatása](https://docs.microsoft.com/azure/app-service/overview) platformot biztosít webalkalmazások üzemeltetéséhez (PAAS). Az Azure App Service-ben üzemeltetett webalkalmazások élvezhetik a hatékony Azure-funkciókelőnyeit, például a további biztonságot, a terheléselosztást és a méretezhetőséget, valamint az Azure-t és a partner DevOps-megoldásokat, például a folyamatos üzembe helyezést, a csomagkezelést és így tovább. Az Azure App Service támogatja a számos népszerű nyelven kifejlesztett és Windows vagy Linux-infrastruktúrára telepített webalkalmazásokat.

Ebben a szakaszban egy webalkalmazást hoz üzembe az App Service-ben, és üzembe helyezi a kódot az Azure CLI-parancsok használatával. Az [az webapp](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) dokumentációjában használt parancsok részleteit megtalálja. Mielőtt elkezdené, győződjön meg arról, hogy elvégezte az [erőforráscsoport nak az IoT hubhoz való hozzáadásának](#add-a-consumer-group-to-your-iot-hub)lépéseit, [lekérnie kell egy szolgáltatáskapcsolati karakterláncot az IoT hubhoz,](#get-a-service-connection-string-for-your-iot-hub)és [töltse le a webalkalmazást a GitHubról.](#download-the-web-app-from-github)

1. Az [App Service-csomag](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) határozza meg az App Service-ben üzemeltetett alkalmazás számítási erőforrásainak készletét. Ebben az oktatóanyagban a Fejlesztői/ingyenes szint a webalkalmazás üzemeltetéséhez. Az ingyenes szinttel a webalkalmazás megosztott Windows-erőforrásokon fut más App Service-alkalmazásokkal, beleértve más ügyfelek alkalmazásait is. Az Azure is kínál App Service-csomagok webalkalmazások linuxos számítási erőforrásokon való üzembe helyezéséhez. Kihagyhatja ezt a lépést, ha már rendelkezik egy használni kívánt App Service-csomaggal.

   Ha egy App Service-csomagot szeretne létrehozni az ingyenes Windows-csomag használatával, futtassa a következő parancsot. Használja ugyanazt az erőforráscsoportot, amelyben az IoT-központ található. A szolgáltatási terv neve kis- és nagybetűket, számokat és kötőjeleket tartalmazhat.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Most egy webalkalmazást az App Service-csomagban. A `--deployment-local-git` paraméter lehetővé teszi a webalkalmazás kódjának feltöltését és üzembe helyezését a helyi gépen lévő Git-tárházból. A webalkalmazás nevének globálisan egyedinek kell lennie, és kis- és nagybetűket, számokat és kötőjeleket tartalmazhat. Ügyeljen arra, hogy a node 10.6-os vagy újabb node-verziót adja meg a `--runtime` paraméterhez, a használt Node.js futásidő verziójától függően. A `az webapp list-runtimes` parancs segítségével lehívhatja a támogatott futásidők listáját.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. Most adja hozzá az alkalmazás beállításait a környezeti változók, amelyek meghatározzák az IoT hub kapcsolati karakterlánc és az Event hub fogyasztói csoport. Az egyes beállítások a `-settings` paraméterben szóközt foglalnak el. Használja a szolgáltatás kapcsolati karakterláncot az IoT hub és a fogyasztói csoport, amelyet korábban létrehozott ebben az oktatóanyagban. Ne idézd az értékeket.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. Engedélyezze a webalkalmazás Web Sockets protokollját, és állítsa be, hogy a webalkalmazás csak HTTPS-kérelmekfogadására (a HTTP-kérelmek et átirányítja a RENDSZER HTTPS-be).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. A kód applikációba való üzembe helyezéséhez a [felhasználói szintű telepítési hitelesítő adatokat](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials)fogja használni. A felhasználói szintű központi telepítési hitelesítő adatok eltérnek az Azure-hitelesítő adatok, és git helyi és FTP-központi telepítések egy webalkalmazásba használt. Miután beállította őket, az Összes App Service-alkalmazásban érvényesek az Azure-fiókjában lévő összes előfizetésben. Ha korábban már beállította a felhasználói szintű központi telepítési hitelesítő adatokat, használhatja őket.

   Ha korábban nem állított be felhasználói szintű központi telepítési hitelesítő adatokat, vagy nem emlékszik a jelszavára, futtassa a következő parancsot. A központi telepítési felhasználónév egyedinek kell lennie az Azure-ban, és nem tartalmazhatja a "@" szimbólumot a helyi Git leküldések. Amikor a rendszer kéri, írja be és erősítse meg az új jelszót. A jelszónak legalább nyolc karakter hosszúnak kell lennie, a következő három elem közül kettővel: betűk, számok és szimbólumok.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Szerezd meg a Git URL-cím segítségével leküldéses a kódot az App Service.Get the Git URL to use to push your code up to App Service.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Adjon hozzá egy távolit a klónhoz, amely az App Service-ben a webalkalmazás Git-tárházára hivatkozik. A \<Git\>klón URL-cím, használja az URL-t visszaadott az előző lépésben. Futtassa a következő parancsot a parancsablakban.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Ha telepíteni szeretné a kódot az App Service szolgáltatásba, írja be a következő parancsot a parancsablakba. Ha a rendszer hitelesítő adatokat kér, adja meg az 5. Győződjön meg arról, hogy leküldéses az App Service-távvezérlő fő ága.

    ```cmd
    git push webapp master:master
    ```

9. A telepítés előrehaladása a parancsablakban frissül. A sikeres telepítés a következő kimenethez hasonló sorokkal ér véget:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. A következő paranccsal lekérdezheti a webalkalmazás állapotát, és meggyőződhat arról, hogy fut:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. Egy böngészőben nyissa meg a `https://<your web app name>.azurewebsites.net` oldalt. A webalkalmazás helyi megjelenítésekor látotthoz hasonló weblap jelenik meg. Feltételezve, hogy az eszköz fut és adatokat küld, meg kell jelennie egy futó telek a 50 legutóbbi hőmérséklet és páratartalom mért által küldött eszköz.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha bármilyen probléma merül fel ezzel a mintával kapcsolatban, próbálkozzon a következő szakaszokban ismertetett lépésekkel. Ha továbbra is problémái vannak, küldjön visszajelzést a témakör alján.

### <a name="client-issues"></a>Ügyfélproblémák

* Ha egy eszköz nem jelenik meg a listában, vagy nem rajzol diagramot, ellenőrizze, hogy az eszközkód fut-e az eszközön.

* A böngészőben nyissa meg a fejlesztői eszközöket (sok böngészőben az F12 billentyű megnyitja), és keresse meg a konzolt. Keresse meg az ott nyomtatott figyelmeztetéseket és hibákat.

* Az ügyféloldali parancsfájlokat a /js/chat-device-data.js kapcsolóban debugolhatja.

### <a name="local-website-issues"></a>Helyi webhelyekkel kapcsolatos problémák

* Nézze meg a kimenetet abban az ablakban, ahol elindította a csomópontot a konzolkimenethez.

* Hibakeresés a kiszolgálókód, különösen a server.js és a /scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Az Azure App Service problémái

* Az Azure Portalon nyissa meg a webalkalmazást. A bal oldali ablaktábla **Figyelés** csoportban válassza az **App Service-naplók lehetőséget.** Kapcsolja be **az alkalmazásnaplózást (fájlrendszer),** állítsa **a Szint értéket** hibára, majd válassza a Mentés **gombot.** Ezután nyissa meg **a Naplófolyamot** (a **Figyelés**csoportban).

* Az Azure Portalon található webalkalmazásból a **Fejlesztői eszközök** csoportban `node -v` `npm -v`válassza a **Konzol** lehetőséget, és érvényesítse a csomópont- és npm-verziókat a és a alkalmazással.

* Ha hibaüzenetet lát acsomag megtalálásának hiányáról, előfordulhat, hogy a lépéseket nem sorrendben futtatta. Amikor a hely telepítve `git push`van (a) `npm install`az alkalmazásszolgáltatás fut , amely fut az aktuális verzió alapján a csomópont általa konfigurált. Ha ez később megváltozik a konfigurációban, akkor a kód értelmetlen módosítását kell tennie, és újra le kell nyomnia.

## <a name="next-steps"></a>További lépések

Sikeresen használta a webalkalmazást az IoT hubvalós idejű érzékelőadatainak megjelenítésére.

Az Azure IoT Hubból származó adatok megjelenítésének másik módjáról a [Power BI használata az IoT hubvalós idejű érzékelőadatainak megjelenítéséhez.](iot-hub-live-data-visualization-in-power-bi.md)

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
