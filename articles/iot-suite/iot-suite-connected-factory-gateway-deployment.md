---
title: "A csatlakoztatott gyári átjáró - Azure üzembe helyezéséhez |} Microsoft Docs"
description: "Központi telepítése a Windows vagy Linux engedélyezéséhez a csatlakoztatott gyári és átjáró előre konfigurált megoldás."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 32a62be9578ac802ee8fff1b0aa48e2d39362e63
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="deploy-a-gateway-on-windows-or-linux-for-the-connected-factory-preconfigured-solution"></a>Az előre konfigurált csatlakoztatott gyári megoldás a Windows vagy Linux-egy átjáró üzembe helyezéséhez

Az előre konfigurált csatlakoztatott gyári megoldás átjáró telepítéséhez szükséges szoftver két összetevőből áll:

* A *OPC Proxy* kapcsolatot hoz létre az IoT-központ. A *OPC Proxy* majd megvárja-e a parancs és a vezérlő üzenetek a böngészőből integrált OPC, amelyen a csatlakoztatott gyári megoldás portálon.
* A *OPC Publisher* meglévő helyszíni OPC EE kiszolgálók csatlakozik, és továbbítja telemetriai üzenetek őket az IoT-központ.

Mindkét összetevők nyílt forráskódú és érhetők el a Githubon forrásként és a Docker tárolóként:

| GitHub | DockerHub |
| ------ | --------- |
| [OPC-közzétevő][lnk-publisher-github] | [OPC-közzétevő][lnk-publisher-docker] |
| [OPC Proxy][lnk-proxy-github] | [OPC Proxy][lnk-proxy-docker] |

Nincs nyilvánosan elérhető IP-cím vagy az átjáró tűzfal lyuk vagy összetevőhöz szükségesek. A OPC Proxy és OPC szoftvergyártó használja a 443-as, 5671 és 8883 csak kimenő portok.

A cikkben leírt lépéseket mutatja be egy Docker használatával vagy az átjáró üzembe helyezéséhez [Windows](#windows-deployment) vagy [Linux](#linux-deployment). Az átjáró lehetővé teszi, hogy a kapcsolatot a csatlakoztatott gyári előre konfigurált megoldáshoz.

> [!NOTE]
> A Docker-tároló futó átjáró szoftverek [Azure IoT peremhálózati].

## <a name="windows-deployment"></a>Windows központi telepítése

> [!NOTE]
> Ha még nem rendelkezik egy átjáróeszköz, a Microsoft azt javasolja, kereskedelmi átjáró vásárol a partnerek egyikét. Látogasson el a [Azure IoT eszköz katalógus] a csatlakoztatott gyári megoldás kompatibilis átjáró eszközök listáját. Kövesse az utasításokat, amelyek az átjáró beállítása az eszközre. Azt is megteheti a következő utasításokat követve manuálisan állítsa be a meglévő átjáró egyik.

### <a name="install-docker"></a>Docker telepítése

Telepítés [Docker for Windows] átjáró Windows-alapú eszközén. Windows Docker a telepítés során válassza ki a Docker megosztása a számítógép egyik meghajtójára. Az alábbi képernyőfelvételen látható megosztása a D meghajtón a Windows rendszeren:

![Docker telepítése][img-install-docker]

Ezután hozzon létre egy nevű **docker** a megosztott meghajtó gyökérkönyvtárában található.
Ezt a lépést hajtsa végre a docker telepítése után a **beállítások** menü.

### <a name="configure-the-gateway"></a>Az átjáró konfigurálása

1. Van szüksége a **iothubowner** kapcsolati karakterlánca a Azure IoT Suite gyári központi telepítést, hogy az összes átjáró csatlakozik. Az a [Azure-portálon], keresse meg az IoT-központ a csatlakoztatott gyári megoldás üzembe helyezésekor létrehozott erőforráscsoportban. Kattintson a **megosztott elérési házirendek** eléréséhez a **iothubowner** kapcsolati karakterlánc:

    ![Keresse meg az IoT-központ kapcsolati karakterláncot][img-hub-connection]

    Másolás a **kapcsolati karakterlánc – elsődleges kulcs** érték.

1. Az átjáró konfigurálása az IoT hub a két átjáró modulok futtatásával **után** a parancssorból:

    `docker run -it --rm -h <ApplicationName> -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:2.1.1 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:1.0.2 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;**  biztosítanak a OPC EE Publisher formátumú neve **publisher.&lt; a teljesen minősített tartománynevét&gt;**. Például, ha a gyári hálózati nevezik **myfactorynetwork.com**, a **ApplicationName** értéke **publisher.myfactorynetwork.com**.
    * **&lt;IoTHubOwnerConnectionString&gt;**  van a **iothubowner** az előző lépésben kimásolt kapcsolati karakterláncot. Ez a kapcsolati karakterlánc csak ebben a lépésben követően nem kell az alábbi lépéseket:

    Használhatja a csatlakoztatott D:\\docker mappát (a `-v` argumentum) később, a két X.509-tanúsítványokat az átjáró modulok használata megtartására.

### <a name="run-the-gateway"></a>Futtassa az átjáró

1. Indítsa újra az átjárót, az alábbi parancsok használatával:

    `docker run -it --rm -h <ApplicationName> --expose 62222 -p 62222:62222 -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/shared -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:2.1.1 <ApplicationName>`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:1.0.2 -D /mapped/cs.db`

1. Biztonsági okokból két X.509-tanúsítványokat őrzi meg a D:\\docker mappa tartalmazza a titkos kulcsot. Ez a mappa a hitelesítő adatokhoz való hozzáférés korlátozása (általában **rendszergazdák**) használja a Docker-tároló futtatásához. Kattintson a jobb gombbal a D:\\docker mappát, válassza a **tulajdonságok**, majd **biztonsági**, majd **szerkesztése**. Adjon **rendszergazdák** teljes hozzáférés, és távolítsa el a többi felhasználó:

    ![Engedélyezze, hogy a Docker-megosztás][img-docker-share]

1. Ellenőrizze a hálózati kapcsolatot. A parancssorból, írja be a parancsot `ping publisher.<your fully qualified domain name>` Pingelje meg az átjáró. Ha a cél nem érhető el, adja hozzá az IP-cím és az állomásleíró fájlhoz meg az átjáró az átjárója nevére. A hosts fájl található a **Windows\\System32\\illesztőprogramok\\stb** mappát.

1. Ezt követően próbálja meg csatlakozni a közzétevőhöz, az átjáró fut helyi OPC EE-ügyfél használatával. A OPC EE-végpont URL-cím `opc.tcp://publisher.<your fully qualified domain name>:62222`. Ha egy OPC EE-ügyfél nincs telepítve, töltse le és használja az [nyílt forráskódú OPC EE ügyfél].

1. Ezek a helyi tesztek sikeresen befejeződött, keresse meg a a **csatlakoztassa a saját OPC EE kiszolgálót** lapjára a csatlakoztatott gyári megoldás. Adja meg a kiadó végponti URL-cím (`tcp://publisher.<your fully qualified domain name>:62222`), majd **Connect**. Egy tanúsítvány figyelmeztetés, majd kattintson a **folytatása.** Ezután hibaüzenetet kap, amely a közzétevő nem megbízható az EE webes ügyfél. Ez a hiba megoldása érdekében másolja a **EE webes ügyfél** a tanúsítvány a **D:\\docker\\elutasított tanúsítványok\\Tanúsítványos** mappát a **D:\\docker\\EE-alkalmazások\\Tanúsítványos** az átjáró mappájába. Nem kell újraindítania az átjáró. Ismételje meg ezt a lépést. Most csatlakozhat az átjáró a felhőből, és készen áll a OPC EE-kiszolgálók hozzáadása a megoldáshoz.

### <a name="add-your-opc-ua-servers"></a>A OPC EE-kiszolgálók hozzáadása

1. Keresse meg a **csatlakoztassa a saját OPC EE kiszolgálót** lapjára a csatlakoztatott gyári megoldás. Kövesse a lépéseket, ahogy az előző szakaszban a csatlakoztatott gyári portál és a OPC EE-kiszolgáló közötti megbízhatósági kapcsolat létrehozásához. Ez a lépés a csatlakoztatott gyári portál és a OPC EE-kiszolgálóról a tanúsítványok kölcsönös megbízhatósági kapcsolatot hoz létre, és kapcsolatot hoz létre.

1. Keresse a OPC EE csomópontok fájának OPC EE-kiszolgálóját, kattintson a jobb gombbal a OPC csomópontok, és válassza ki **közzététele**. Közzététel ennek megfelelően működik, a OPC EE-kiszolgáló és a közzétevő ugyanazon a hálózaton kell lennie. Ez azt jelenti Ha a teljesen minősített tartományneve, a közzétevő **publisher.mydomain.com** akkor OPC EE kiszolgáló teljesen minősített tartománynevét kell lennie, például **myopcuaserver.mydomain.com**. Ha a telepítő nem, manuálisan csomópont is hozzáadható a publishesnodes.json fájl található a **D:\\docker** mappa. A publishesnodes.json fájl automatikusan jön létre az első sikeres közzététele egy OPC-csomópont.

1. Telemetria most zajlik az átjáró eszközről. A telemetriai adatok megtekintéséhez a **gyári helyek** a csatlakoztatott gyári portál ábrázolása **új előállító**.

## <a name="linux-deployment"></a>Linux-telepítés

> [!NOTE]
> Ha még nem rendelkezik egy átjáróeszköz, a Microsoft azt javasolja, kereskedelmi átjáró vásárol a partnerek egyikét. Látogasson el a [Azure IoT eszköz katalógus] a csatlakoztatott gyári megoldás kompatibilis átjáró eszközök listáját. Kövesse az utasításokat, amelyek az átjáró beállítása az eszközre. Azt is megteheti a következő utasításokat követve manuálisan állítsa be a meglévő átjáró egyik.

[Telepítse a Docker] Linux átjáró eszközén.

### <a name="configure-the-gateway"></a>Az átjáró konfigurálása

1. Van szüksége a **iothubowner** kapcsolati karakterlánca a Azure IoT Suite gyári központi telepítést, hogy az összes átjáró csatlakozik. Az a [Azure-portálon], keresse meg az IoT-központ a csatlakoztatott gyári megoldás üzembe helyezésekor létrehozott erőforráscsoportban. Kattintson a **megosztott elérési házirendek** eléréséhez a **iothubowner** kapcsolati karakterlánc:

    ![Keresse meg az IoT-központ kapcsolati karakterláncot][img-hub-connection]

    Másolás a **kapcsolati karakterlánc – elsődleges kulcs** érték.

1. Az átjáró konfigurálása az IoT hub a két átjáró modulok futtatásával **után** egy rendszerhéjból rendelkező:

    `sudo docker run -it --rm -h <ApplicationName> -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/ -v /shared:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:2.1.1 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `sudo docker run --rm -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:1.0.2 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;**  formátumú létrehozza az átjáró OPC EE-alkalmazás neve **publisher.&lt; a teljesen minősített tartománynevét&gt;**. Például **publisher.microsoft.com**.
    * **&lt;IoTHubOwnerConnectionString&gt;**  van a **iothubowner** az előző lépésben kimásolt kapcsolati karakterláncot. Ez a kapcsolati karakterlánc csak ebben a lépésben követően nem kell az alábbi lépéseket:

    Használja a **/ megosztott** mappát (a `-v` argumentum) később, a két X.509-tanúsítványokat az átjáró modulok használata megtartására.

### <a name="run-the-gateway"></a>Futtassa az átjáró

1. Indítsa újra az átjárót, az alábbi parancsok használatával:

    `sudo docker run -it -h <ApplicationName> --expose 62222 -p 62222:62222 --rm -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v /shared:/shared -v /shared:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:2.1.1 <ApplicationName>`

    `sudo docker run -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:1.0.2 -D /mapped/cs.db`

1. Biztonsági okokból két X.509-tanúsítványokat őrzi meg a **/ megosztott** mappa tartalmazza a titkos kulcsot. Ez a mappa a Docker-tároló futtatásához használt hitelesítő adatokhoz való hozzáférés korlátozásához. Az engedélyek beállítása **legfelső szintű** csak, használja a `chmod` rendszerhéj-parancs a mappához.

1. Ellenőrizze a hálózati kapcsolatot. A rendszerhéj, írja be a parancsot `ping publisher.<your fully qualified domain name>` Pingelje meg az átjáró. Ha a cél nem érhető el, adja hozzá az IP-cím és az átjáró a állomások fájlba az átjárója nevére. A hosts fájl található a **/stb** mappát.

1. Ezt követően próbálja meg csatlakozni a közzétevőhöz, az átjáró fut helyi OPC EE-ügyfél használatával. A OPC EE-végpont URL-cím `opc.tcp://publisher.<your fully qualified domain name>:62222`. Ha egy OPC EE-ügyfél nincs telepítve, töltse le és használja az [nyílt forráskódú OPC EE ügyfél].

1. Ezek a helyi tesztek sikeresen befejeződött, keresse meg a a **csatlakoztassa a saját OPC EE kiszolgálót** lapjára a csatlakoztatott gyári megoldás. Adja meg a kiadó végponti URL-cím (`tcp://publisher.<your fully qualified domain name>:62222`), majd **Connect**. Egy tanúsítvány figyelmeztetés, majd kattintson a **folytatása.** Ezután hibaüzenetet kap, amely a közzétevő nem megbízható az EE webes ügyfél. Ez a hiba megoldása érdekében másolja a **EE-webügyfél** a tanúsítvány a **/megosztott/elutasított tanúsítványok/Tanúsítványos** mappát a **/shared/UA alkalmazások/Tanúsítványos** az átjáró mappájába. Nem kell újraindítania az átjáró. Ismételje meg ezt a lépést. Most csatlakozhat az átjáró a felhőből, és készen áll a OPC EE-kiszolgálók hozzáadása a megoldáshoz.

### <a name="add-your-opc-ua-servers"></a>A OPC EE-kiszolgálók hozzáadása

1. Keresse meg a **csatlakoztassa a saját OPC EE kiszolgálót** lapjára a csatlakoztatott gyári megoldás. Kövesse a lépéseket, ahogy az előző szakaszban a csatlakoztatott gyári portál és a OPC EE-kiszolgáló közötti megbízhatósági kapcsolat létrehozásához. Ez a lépés a csatlakoztatott gyári portál és a OPC EE-kiszolgálóról a tanúsítványok kölcsönös megbízhatósági kapcsolatot hoz létre, és kapcsolatot hoz létre.

1. Keresse a OPC EE csomópontok fájának OPC EE-kiszolgálóját, kattintson a jobb gombbal a OPC csomópontok, és válassza ki **közzététele**. Közzététel ennek megfelelően működik, a OPC EE-kiszolgáló és a közzétevő ugyanazon a hálózaton kell lennie. Ez azt jelenti Ha a teljesen minősített tartományneve, a közzétevő **publisher.mydomain.com** akkor OPC EE kiszolgáló teljesen minősített tartománynevét kell lennie, például **myopcuaserver.mydomain.com**. Ha a telepítő nem, manuálisan csomópont is hozzáadható a publishesnodes.json fájl található a **/ megosztott** mappát. A publishesnodes.json automatikusan jön létre az első sikeres közzététele egy OPC-csomópont.

1. Telemetria most zajlik az átjáró eszközről. A telemetriai adatok megtekintéséhez a **gyári helyek** a csatlakoztatott gyári portál ábrázolása **új előállító**.

## <a name="next-steps"></a>Következő lépések

Az előre konfigurált csatlakoztatott gyári megoldás architektúrájával kapcsolatos további tudnivalókért lásd: [csatlakoztatott gyári előre konfigurált megoldás forgatókönyv][lnk-walkthrough].

További tudnivalók a [OPC Publisher hivatkozás megvalósítási](iot-suite-connected-factory-publisher.md).

[img-install-docker]: ./media/iot-suite-connected-factory-gateway-deployment/image1.png
[img-hub-connection]: ./media/iot-suite-connected-factory-gateway-deployment/image2.png
[img-docker-share]: ./media/iot-suite-connected-factory-gateway-deployment/image3.png

[Docker for Windows]: https://www.docker.com/docker-windows
[Azure IoT eszköz katalógus]: https://catalog.azureiotsuite.com/?q=opc
[Azure-portálon]: http://portal.azure.com/
[nyílt forráskódú OPC EE ügyfél]: https://github.com/OPCFoundation/UA-.NETStandardLibrary/tree/master/SampleApplications/Samples/Client.Net4
[Telepítse a Docker]: https://www.docker.com/community-edition#/download
[lnk-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[Azure IoT peremhálózati]: https://github.com/Azure/iot-edge

[lnk-publisher-github]: https://github.com/Azure/iot-edge-opc-publisher
[lnk-publisher-docker]: https://hub.docker.com/r/microsoft/iot-gateway-opc-ua
[lnk-proxy-github]: https://github.com/Azure/iot-edge-opc-proxy
[lnk-proxy-docker]: https://hub.docker.com/r/microsoft/iot-gateway-opc-ua-proxy