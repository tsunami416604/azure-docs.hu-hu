---
title: A csatlakoztatott gyári átjáró - Azure üzembe helyezéséhez |} Microsoft Docs
description: Ügyfélszoftverek központi telepítése egy átjáró a Windows vagy a Linux és a kapcsolódó gyári megoldásgyorsító engedélyezéséhez.
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2018
ms.author: dobett
ms.openlocfilehash: 956da99a5d67d7a2225ab3ea64b4e5a9d41ee3a1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-an-edge-gateway-for-the-connected-factory-solution-accelerator-on-windows-or-linux"></a>A Windows vagy Linux a csatlakoztatott gyári megoldásgyorsító egy peremhálózati átjáró üzembe helyezéséhez

Egy a peremhálózati átjáró üzembe helyezéséhez két szoftverösszetevőket van szüksége a *csatlakoztatott gyári* megoldásgyorsító:

- A *OPC Proxy* gyári csatlakoztatott kapcsolat jön létre. A OPC Proxy majd megvárja-e a parancs és a vezérlő üzenetek a böngészőből integrált OPC, amelyen a kapcsolódó gyári megoldás portálon.

- A *OPC Publisher* meglévő helyszíni OPC EE kiszolgálók csatlakozik, és továbbítja telemetriai üzenetek őket gyári csatlakoztatva. Egy OPC klasszikus eszköz használatával is elérheti a [OPC EE-adaptert klasszikus OPC](https://github.com/OPCFoundation/UA-.NETStandard/blob/master/ComIOP/README.md).

Mindkét összetevők nyílt forráskódú és érhetők el a Githubon forrás és a DockerHub Docker-tároló:

| GitHub | DockerHub |
| ------ | --------- |
| [OPC-közzétevő](https://github.com/Azure/iot-edge-opc-publisher) | [OPC-közzétevő](https://hub.docker.com/r/microsoft/iot-edge-opc-publisher/)   |
| [OPC Proxy](https://github.com/Azure/iot-edge-opc-proxy)         | [OPC Proxy](https://hub.docker.com/r/microsoft/iot-edge-opc-proxy/) |

Nem kell egy nyilvánosan elérhető IP-cím vagy az átjáró tűzfal megnyitott bejövő portra vagy összetevőhöz. A OPC Proxy és OPC szoftvergyártó összetevők csak a 443-as kimenő porton használja.

A cikkben leírt lépéseket mutatja be egy Docker használata a Windows vagy Linux peremhálózati átjáró üzembe helyezéséhez. Az átjáró lehetővé teszi, hogy a kapcsolatot a csatlakoztatott gyári megoldásgyorsító. Csatlakoztatott gyári nélkül is használhatja az összetevőket.

> [!NOTE]
> A modulok összetevőket egyaránt használható [Azure IoT peremhálózati](https://github.com/Azure/iot-edge).

## <a name="choose-a-gateway-device"></a>Válasszon egy átjáróeszköz

Ha még nem rendelkezik egy átjáróeszköz, a Microsoft azt javasolja, kereskedelmi átjáró vásárol a partnerek egyikét. A csatlakoztatott gyári megoldás kompatibilis átjáró eszközök listájának megtekintéséhez keresse fel a [Azure IoT eszköz katalógus](https://catalog.azureiotsuite.com/?q=opc). Kövesse az utasításokat, amelyek az átjáró beállítása az eszközre.

Azt is megteheti kövesse az alábbi utasításokat kézzel konfigurálásához egy meglévő átjáró eszközt.

## <a name="install-and-configure-docker"></a>Telepítse és konfigurálja a Docker

Telepítés [Docker for Windows](https://www.docker.com/docker-windows) az átjáró Windows-alapú eszközön vagy a Csomagkezelő docker telepítsen az átjáró Linux-alapú eszköz használatát.

Docker a Windows a telepítés során válassza ki a Docker megosztása a számítógép egyik meghajtójára. Az alábbi képernyőfelvételen látható megosztása a **D** meghajtó a gazdagépen meghajtót egy docker-tároló hozzáférés engedélyezése a Windows rendszeren:

![A Windows Docker telepítése](./media/iot-suite-connected-factory-gateway-deployment/image1.png)

> [!NOTE]
> Ezt a lépést hajtsa végre a docker telepítése után a **beállítások** párbeszédpanel. Kattintson a jobb gombbal a **Docker** ikonjára a Windows tálcán válassza **beállítások**. Fő Windows-frissítések van telepítve a rendszeren, például a Windows alá esik Creators frissítést, ha a meghajtók megosztás törlése, és megoszthatja őket újra a frissítés hozzáférési jogosultságokat.

Ha Linux használ, további konfigurációra a fájlrendszer hozzáférés engedélyezése szükséges.

Hozzon létre egy mappát a meghajtó-és Docker Windows, Linux hozzon létre egy mappát a legfelső szintű fájlrendszer. Ez a forgatókönyv hivatkozik, ez a mappa `<SharedFolder>`.

Ha hivatkozik a `<SharedFolder>` Docker parancs, ügyeljen arra, hogy a helyes szintaxis használata az operációs rendszerhez. Az alábbiakban két példa, egy másik pedig a Windows és Linux céljából:

- Ha a következők a mappát használja `D:\shared` a Windows, mint a `<SharedFolder>`, a Docker parancs szintaxisa `d:/shared`.

- Ha a következők a mappát használja `/shared` Linux, a `<SharedFolder>`, a Docker parancs szintaxisa `/shared`.

További információ: a [köteteket](https://docs.docker.com/engine/admin/volumes/volumes/) docker motor hivatkozás.

## <a name="configure-the-opc-components"></a>A OPC-összetevők konfigurálása

A OPC-összetevők telepítése előtt végezze el az alábbi lépések végrehajtásával készítse elő a környezetet:

1. Az átjáró központi telepítés befejezéséhez szükséges a **iothubowner** az IoT-központ a csatlakoztatott gyári környezetben kapcsolati karakterlánca. Az a [Azure-portálon](http://portal.azure.com/), keresse meg az IoT-központ a csatlakoztatott gyári megoldás üzembe helyezésekor létrehozott erőforráscsoportban. Kattintson a **megosztott elérési házirendek** eléréséhez a **iothubowner** kapcsolati karakterlánc:

    ![Keresse meg az IoT-központ kapcsolati karakterláncot](./media/iot-suite-connected-factory-gateway-deployment/image2.png)

    Másolás a **kapcsolati karakterlánc elsődleges kulcs** érték.

1. Docker-tárolók közötti kommunikáció lehetővé tételéhez, a felhasználó által definiált híd hálózati van szükség. A tárolók skálázása híd hálózat létrehozása a következő parancsokat a parancssorba:

    ```cmd/sh
    docker network create -d bridge iot_edge
    ```

    Győződjön meg arról, hogy a **iot_edge** hálózati híd létrejött, a következő parancsot:

    ```cmd/sh
    docker network ls
    ```

    A **iot_edge** hálózati híd hálózatok listája szerepel.

A OPC Publisher elindításához futtassa a következő parancsot a parancssorba:

```cmd/sh
docker run --rm -it -v <SharedFolder>:/docker -v x509certstores:/root/.dotnet/corefx/cryptography/x509stores --network iot_edge --name publisher -h publisher -p 62222:62222 --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-publisher:2.1.3 publisher "<IoTHubOwnerConnectionString>" --lf /docker/publisher.log.txt --as true --si 1 --ms 0 --tm true --vc true --di 30
```

- A [OPC Publisher GitHub](https://github.com/Azure/iot-edge-opc-publisher) és a [hivatkozás futtatása docker](https://docs.docker.com/engine/reference/run/) kapcsolatos további információk megadására:

  - A docker parancssori kapcsolók a tároló neve előtt meg (`microsoft/iot-edge-opc-publisher:2.1.3`).
  - A tároló neve után megadott OPC Publisher parancssori paraméterek szerinti (`microsoft/iot-edge-opc-publisher:2.1.3`).

- A `<IoTHubOwnerConnectionString>` van a **iothubowner** megosztott hozzáférési házirend kapcsolati karakterláncot az Azure portálról. Ez a kapcsolati karakterlánc az előző lépésben másolt. Csak a kapcsolati karakterlánc szükséges OPC Publisher első alkalommal történő futtatásakor. Utólagosan kell kihagyása, mert azt a biztonsági kockázatnak teheti.

- A `<SharedFolder>` használ, és a szintaxist a szakaszban ismertetett [telepítse és konfigurálja a Docker](#install-and-configure-docker). OPC közzétevő használja a `<SharedFolder>` , és a OPC Publisher konfigurációs fájl olvasási ír a naplófájlba, és a tároló kívül elérhetővé mindkét ezeket a fájlokat.

- OPC Publisher beolvassa a konfigurációját az a **publishednodes.json** fájl, amely olvasni és írni a `<SharedFolder>/docker` mappát. Ezt a konfigurációs fájlt a OPC Publisher elő kell fizetnie megadott OPC EE-kiszolgálón határozza meg, melyik OPC EE-csomópont adatait. Teljes szintaxisát a **publishednodes.json** fájl a leírása a [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) lap a Githubon. Amikor egy átjáró, egy üres put **publishednodes.json** a mappába:

    ```json
    [
    ]
    ```

- A OPC EE-kiszolgáló egy változását OPC közzétevője értesíti, amikor az új értéket az IoT-központ küld. A kötegelési beállításaitól függően lehetséges, hogy először a OPC közzétevő az adatok felhalmozhat, az adatokat az IoT hubhoz kötegben küldése előtt.

- Docker nem támogatja a NetBIOS-névfeloldás, csak a DNS-névfeloldását. Ha egy DNS-kiszolgáló nincs a hálózaton, a megoldás az előző parancssori példában látható módon használhatja. Az előző parancssori példa a `--add-host` paraméter segítségével adjon hozzá egy bejegyzést a tárolók állomások fájlba. Ez a bejegyzés lehetővé teszi, hogy a keresés állomásnév: a megadott `<OpcServerHostname>`, a megadott IP-címre feloldó `<IpAddressOfOpcServerHostname>`.

- OPC EE X.509-tanúsítványokat használ hitelesítésre és titkosításra. Szeretné elhelyezni a OPC szoftvergyártói tanúsítvány kapcsolódik, annak érdekében, megbízhatónak fogja tekinteni OPC Publisher OPC EE-kiszolgálón. A OPC Publisher tanúsítványtárolóban található a `<SharedFolder>/CertificateStores` mappát. A OPC Publisher tanúsítvány található a `trusted/certs` mappájában a `CertificateStores` mappát.

  A OPC EE-kiszolgáló konfigurálásának lépéseit az Ön által használt eszköz függ. Ezeket a lépéseket általában szerepelnek a OPC EE-kiszolgáló felhasználói kézikönyvében található.

A OPC-Proxy telepítéséhez futtassa a következő parancsot a parancssorba:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.2 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db
```

Csak futtatásához szükséges a telepítés után a rendszer.

A következő paranccsal futtassa a OPC-Proxy:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.2 -D /mapped/cs.db
```

OPC Proxy menti a kapcsolati karakterláncot a telepítés során. Utólagosan kell nincs megadva a kapcsolati karakterlánc miatt biztonsági kockázatot jelent.

## <a name="enable-your-gateway"></a>Az átjáró engedélyezése

Az alábbi lépésekkel az átjáró a csatlakoztatott gyári megoldásgyorsító engedélyezése:

1. Összetevőket egyaránt futtatásakor tallózással keresse meg a **csatlakoztassa a saját OPC EE kiszolgálót** lapjára a csatlakoztatott gyári megoldás. Ezen a lapon csak rendszergazdák számára érhető el a megoldás. Adja meg a kiadó végponti URL-cím (opc.tcp://publisher: 62222), majd **Connect**.

1. A csatlakoztatott gyári portal és OPC Publisher közötti megbízhatósági kapcsolat létrehozására. Amikor megjelenik egy tanúsítványfigyelmeztetés, kattintson **folytatása**. Ezt követően, hogy a OPC Publisher nem megbízható a EE-webügyfél hibaüzenet jelenik meg. Ez a hiba megoldása érdekében másolja a **EE-webügyfél** a tanúsítvány a `<SharedFolder>/CertificateStores/rejected/certs` mappát a `<SharedFolder>/CertificateStores/trusted/certs` az átjáró mappájába. Nem kell indítsa újra az átjárót.

Most csatlakozhat az átjáró a felhőből, és készen áll a OPC EE-kiszolgálók hozzáadása a megoldáshoz.

## <a name="add-your-own-opc-ua-servers"></a>A saját OPC EE-kiszolgálók hozzáadása

A saját OPC EE-kiszolgálók hozzáadása a csatlakoztatott gyári megoldásgyorsító:

1. Keresse meg a **csatlakoztassa a saját OPC EE kiszolgálót** lapjára a csatlakoztatott gyári megoldás.

    1. Indítsa el a OPC EE kiszolgáló, amelyhez csatlakozni kíván. Győződjön meg arról, hogy a OPC EE-kiszolgáló elérhető-e OPC közzétevő és a tárolóban futó OPC-proxyra (lásd az előző megjegyzéseket névfeloldás).
    1. Adja meg a végpont a OPC EE-kiszolgáló URL-CÍMÉT (`opc.tcp://<host>:<port>`), majd **Connect**.
    1. A csatlakozási telepítés részeként a csatlakoztatott gyári portal (OPC EE-ügyfél) és a csatlakozni próbál OPC EE-kiszolgáló közötti megbízhatósági kapcsolat jön létre. A csatlakoztatott gyári irányítópulton kap egy **nem lehetett leellenőrizni a tanúsítvány a kiszolgáló, amelyhez csatlakozni** figyelmeztetés. Amikor megjelenik egy tanúsítványfigyelmeztetés, kattintson **folytatása**.
    1. Telepítő nehezebb van a tanúsítvány konfigurálását a OPC EE-kiszolgáló kapcsolódni próbál. A PC-alapú OPC EE-kiszolgálókról, ugyanúgy kaphat egy figyelmeztető párbeszédpanel az irányítópulton, amely megerősíti is. Beágyazott OPC EE server rendszerek esetén a dokumentációt a OPC EE kiszolgáló hogyan végezhető el ez a feladat kereséséhez. A feladat végrehajtásához szükség lehet a csatlakoztatott gyári portal OPC EE ügyfél tanúsítványát. A rendszergazda a letöltheti ezt a tanúsítványt a **csatlakoztassa a saját OPC EE-kiszolgálót** lap:

        ![Megoldásportál](./media/iot-suite-connected-factory-gateway-deployment/image4.png)

1. Keresse meg a OPC EE csomópontok fájának OPC EE-kiszolgálóját, kattintson a jobb gombbal a OPC csomópontok értékek küldeni a gyári csatlakoztatva, és válassza ki a kívánt **közzététele**.

1. Telemetria most zajlik az átjáró eszközről. A telemetriai adatok megtekintéséhez a **gyári helyek** a csatlakoztatott gyári portál ábrázolása **új előállító**.

## <a name="next-steps"></a>További lépések

A csatlakoztatott gyári megoldásgyorsító architektúrájával kapcsolatos további tudnivalókért lásd: [csatlakoztatott gyári megoldás gyorsító forgatókönyv](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-sample-walkthrough).

További tudnivalók a [OPC Publisher hivatkozás megvalósítási](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-publisher).