---
title: A csatlakoztatott gyár átjárót – Azure |} A Microsoft Docs
description: Útmutató átjáró üzembe helyezése Windows vagy Linux rendszeren az Okosgyár-megoldásgyorsító csatlakozásának engedélyezéséhez.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: dobett
ms.openlocfilehash: ce2391014eb5e15650c0a81ebb8e2fd4a08ca96b
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139224"
---
# <a name="deploy-an-edge-gateway-for-the-connected-factory-solution-accelerator-on-windows-or-linux"></a>Egy edge-átjáró számára a Windows vagy Linux rendszeren Okosgyár-megoldásgyorsító üzembe helyezése

Két szoftverösszetevőket egy az edge-átjáró telepítéséhez szükséges a *Okosgyár* megoldásgyorsító:

- A *OPC-Proxy* Okosgyár kapcsolatot létesít. Az OPC-Proxy majd megvárja, amíg az integrált OPC böngészői, amely a csatlakoztatott gyár portál parancs és vezérlés üzeneteit.

- A *az OPC-közzétevő* csatlakozik a meglévő helyszíni OPC UA-kiszolgálók és a telemetriai üzeneteket továbbítja azokat a csatlakoztatott gyár. Az OPC klasszikus eszköz használatával csatlakoztathatja a [OPC klasszikus adapter az OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/blob/master/ComIOP/README.md).

Mindkét összetevő nyílt forráskódú és rendelkezésre állnak, mint forrás a Githubon, és a Docker-tárolókként DockerHub-on:

| GitHub | DockerHub |
| ------ | --------- |
| [Az OPC-közzétevő](https://github.com/Azure/iot-edge-opc-publisher) | [Az OPC-közzétevő](https://hub.docker.com/r/microsoft/iot-edge-opc-publisher/)   |
| [Az OPC-Proxy](https://github.com/Azure/iot-edge-opc-proxy)         | [Az OPC-Proxy](https://hub.docker.com/r/microsoft/iot-edge-opc-proxy/) |

Már nincs szüksége egy nyilvános IP-cím vagy a gateway-tűzfalhoz bejövő portok megnyitása vagy összetevő. Az OPC-Proxy és az OPC-közzétevő összetevők csak a 443-as kimenő portot használja.

A jelen cikkben ismertetett lépések bemutatják, hogyan helyezhet üzembe egy edge-átjáró Windows vagy Linux rendszeren Docker használatával. Az átjáró lehetővé teszi, hogy az Okosgyár-megoldásgyorsító kapcsolódni. Csatlakoztatott gyár nélkül is használhatja az összetevőket.

> [!NOTE]
> Mindkét összetevő használhatók a modulok [Azure IoT Edge](https://github.com/Azure/iot-edge).

## <a name="choose-a-gateway-device"></a>Válasszon egy átjáró-eszközt

Ha még nem rendelkezik átjáróeszközzel, a Microsoft azt javasolja, azok partnerei egyik kereskedelmi átjáró vásárolhat. Kompatibilis a csatlakoztatott gyár megoldás az átjáró-eszközök listáját, keresse fel a [Azure IoT-eszközök katalógusának](https://catalog.azureiotsuite.com/?q=opc). Kövesse az utasításokat, hogy az átjáró beállításához az eszközre.

Ha egy meglévő átjáró eszközt manuálisan konfigurálnia kell, kövesse az alábbi utasításokat.

## <a name="install-and-configure-docker"></a>Telepítse és konfigurálja a Docker

Telepítés [Docker for Windows](https://www.docker.com/docker-windows) az átjáró Windows-alapú eszközön vagy használata az átjáró Linux-alapú eszközön telepítheti a dockert Csomagkezelő.

Docker a Windows a telepítés során válasszon egy meghajtó megosztásához a docker használatával a gazdagépen. A következő képernyőképen látható megosztása a **D** meghajtón a Windows rendszeren. Meghajtó megosztása lehetővé teszi, hogy a gazdagép meghajtót egy docker-tárolóban:

![Docker for Windows telepítése](./media/iot-accelerators-connected-factory-gateway-deployment/image1.png)

> [!NOTE]
> Ebben a lépésben a docker telepítése után is elvégezheti a **beállítások** párbeszédpanel. Kattintson a jobb gombbal a **Docker** és a Windows tálca ikonjára, majd **beállítások**. Ha a rendszer frissítése Windows lett telepítve, mint például a Windows Fall Creators update, a meghajtók megosztás törlése, és megoszthatja őket újra a frissítés a hozzáférési jogosultságokat.

Linux használata, a fájlrendszerhez való hozzáférés engedélyezéséhez szükséges további konfiguráció nélkül.

Hozzon létre egy mappát a meghajtó, a docker használatával megosztott, Windows, Linux hozzon létre egy mappát a gyökér fájlrendszeréhez. Ez a forgatókönyv hivatkozik, ez a mappa `<SharedFolder>`.

Amikor úgy hivatkozik a a `<SharedFolder>` egy Docker-parancsban, ügyeljen arra, hogy az operációs rendszerének megfelelő szintaxis használatára. Az alábbiakban két példa, egyet a Windows és Linux rendszeren:

- Ha a rendszer a mappa használata `D:\shared` a Windows, a `<SharedFolder>`, a Docker parancssori szintaxis `d:/shared`.

- Ha a rendszer a mappa használata `/shared` , Linux rendszeren a `<SharedFolder>`, a Docker parancssori szintaxis `/shared`.

További információkért lásd: a [a kötetek](https://docs.docker.com/engine/admin/volumes/volumes/) docker engine-hivatkozás.

## <a name="configure-the-opc-components"></a>Az OPC-összetevők konfigurálása

Mielőtt telepíti az OPC-összetevőket, a következő lépéseket a környezet előkészítése:

1. Az átjáró telepítéséhez, rendelkeznie kell a **iothubowner** a csatlakoztatott gyár központi telepítésben az IoT hub kapcsolati karakterláncot. Az a [az Azure portal](http://portal.azure.com/), keresse meg az IoT hubot a csatlakoztatott gyár megoldás üzembe helyezésekor létrehozott erőforráscsoportban. Kattintson a **megosztott elérési házirendek** való hozzáférést a **iothubowner** kapcsolati karakterlánc:

    ![Keresse meg az IoT Hub kapcsolati karakterláncot](./media/iot-accelerators-connected-factory-gateway-deployment/image2.png)

    Másolás a **kapcsolati karakterlánc – elsődleges kulcs** értéket.

1. Ahhoz, hogy a docker-tárolók közötti kommunikációt, szüksége van egy felhasználó által definiált hídhálózat. Hozzon létre egy hídhálózat a tárolókhoz, a következő parancsokat a parancssorba:

    ```cmd/sh
    docker network create -d bridge iot_edge
    ```

    Ellenőrizze a **iot_edge** hálózati hidas kapcsolatán lett létrehozva, futtassa a következő parancsot:

    ```cmd/sh
    docker network ls
    ```

    A **iot_edge** hálózati hidas kapcsolatán a hálózatok listájában szerepel.

Az OPC-közzétevő futtatni, futtassa a következő parancsot a parancssorba:

```cmd/sh
docker run --rm -it -v <SharedFolder>:/docker -v x509certstores:/root/.dotnet/corefx/cryptography/x509stores --network iot_edge --name publisher -h publisher -p 62222:62222 --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-publisher:2.1.4 publisher "<IoTHubOwnerConnectionString>" --lf /docker/publisher.log.txt --as true --si 1 --ms 0 --tm true --vc true --di 30
```

- A [OPC Publisher GitHub](https://github.com/Azure/iot-edge-opc-publisher) és a [referencia futtatása docker](https://docs.docker.com/engine/reference/run/) kapcsolatos további információk megadására:

  - A docker parancssori kapcsolókat, a tároló neve előtt megadott (`microsoft/iot-edge-opc-publisher:2.1.4`).
  - Az OPC-közzétevő parancssori paraméterek, a tároló neve után megadott jelentését (`microsoft/iot-edge-opc-publisher:2.1.4`).

- A `<IoTHubOwnerConnectionString>` van a **iothubowner** megosztott hozzáférési szabályzat kapcsolati karakterláncot az Azure Portalról. Ez a kapcsolati karakterlánc az előző lépésben kimásolt. Az első futtatásakor az OPC-közzétevő csak kell ezt a kapcsolati karakterláncot. Utólagosan kell kihagyása, mert ez biztonsági kockázatot jelent.

- A `<SharedFolder>` használ, és a szintaxisát a szakaszban leírt [telepítse és konfigurálja a Docker](#install-and-configure-docker). Az OPC-közzétevő használja a `<SharedFolder>` való:

    - Olvasási és írási az OPC-közzétevő konfigurációs fájl.
    - Írja a naplófájlba.
    - Mindkét fájl akkor válik elérhetővé a tárolón kívül.

- Az OPC-közzétevő beolvassa a konfigurációját az a **publishednodes.json** fájlt, amely olvasni és írni a `<SharedFolder>/docker` mappát. Ezt a konfigurációs fájlt határozza meg, mely az OPC UA-csomópont adatait egy adott OPC UA-kiszolgálóval, az OPC-közzétevő elő kell fizetnie. A teljes szintaxisát a **publishednodes.json** fájlt a leírása a [az OPC-közzétevő](https://github.com/Azure/iot-edge-opc-publisher) lapját a githubon. Amikor hozzáad egy átjárót, helyezze egy üres **publishednodes.json** abba a mappába:

    ```json
    [
    ]
    ```

- Az OPC UA-kiszolgálóval változnak az OPC-közzétevő értesíti, amikor az új értéket az IoT Hub küldi. A kötegelés beállításaitól függően lehetséges, hogy először az OPC-közzétevő azután küldi el az adatokat az IoT hub egy kötegbe gyűlnek az adatok.

- Docker NetBIOS-névfeloldás, csak a DNS-névfeloldás nem támogatja. Ha a hálózat DNS-kiszolgáló nem rendelkezik, használhatja az előző parancssori példa látható a megoldás. Az előző parancssori példa a `--add-host` paraméter segítségével adjon hozzá egy bejegyzést a tárolók gazdagépek fájlba. Ez a bejegyzés lehetővé teszi, hogy a gazdanév keresése a megadott `<OpcServerHostname>`, az adott IP-címet feloldó `<IpAddressOfOpcServerHostname>`.

- Az OPC UA X.509-tanúsítványokat használ hitelesítésre és titkosításra. Az OPC-közzétevő tanúsítvány helyezze az OPC UA-kiszolgálóhoz csatlakozik, győződjön meg arról, hogy megbízhatónak tekinti az OPC-közzétevő. Az OPC-közzétevő tanúsítványtárolóban található a `<SharedFolder>/CertificateStores` mappát. Az OPC-közzétevő-tanúsítványt annak a `trusted/certs` mappájában a `CertificateStores` mappát.

  Az OPC UA-kiszolgáló konfigurálásának lépései használt eszköz függenek. Ezeket a lépéseket az OPC UA-kiszolgáló felhasználói kézikönyvében általában vannak dokumentálva.

Az OPC-Proxy telepítéséhez futtassa a következő parancsot a parancssorba:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.4 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db
```

Csak ki kell egyszer futtatja a telepítést a rendszeren.

Az alábbi parancs segítségével futtassa az OPC-Proxy:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.4 -D /mapped/cs.db
```

Az OPC-Proxy menti a kapcsolati karakterláncot a telepítés során. Utólagosan a kapcsolati karakterláncot kell kihagyja, mert, az biztonsági kockázatot jelent.

## <a name="enable-your-gateway"></a>Az átjáró engedélyezése

A következő lépéseket az átjárót az Okosgyár-megoldásgyorsító a engedélyezése:

1. Mindkét összetevő fut-e, amikor tallózással keresse meg a **saját OPC UA-kiszolgáló csatlakoztatása** a csatlakoztatott gyár portál oldalán. Ezen a lapon csak a megoldásban a rendszergazdák számára érhető el. Adja meg a kiadó végpont URL-címe (opc.tcp://publisher: 62222), és kattintson a **Connect**.

1. A csatlakoztatott gyár portál és az OPC-közzétevő közötti megbízhatósági kapcsolat létrehozásához. Amikor megjelenik egy tanúsítványfigyelmeztetés, kattintson a **folytatása**. Ezt követően, hogy az OPC-közzétevő nem bízik az UA webes ügyfél hibaüzenet jelenik meg. A probléma megoldásához, másolja a **UA webes ügyfél** a tanúsítvány a `<SharedFolder>/CertificateStores/rejected/certs` mappát a `<SharedFolder>/CertificateStores/trusted/certs` az átjáró mappájába. Az átjáró újraindítása nem szükséges.

Ön mostantól csatlakozhat az átjáró a felhőben, és készen áll az OPC UA-kiszolgálók hozzáadása a megoldáshoz.

## <a name="add-your-own-opc-ua-servers"></a>Saját OPC UA-kiszolgálók hozzáadása

Saját OPC UA-kiszolgálók az Okosgyár-megoldásgyorsító hozzáadása:

1. Keresse meg a **saját OPC UA-kiszolgáló csatlakoztatása** a csatlakoztatott gyár portál oldalán.

    1. Indítsa el az OPC UA-kiszolgáló, amelyhez csatlakozni kíván. Ellenőrizze, hogy az OPC UA-kiszolgálóval érhető el az OPC-közzétevő és az OPC-Proxy a tárolóban futó. Tekintse meg az előző névfeloldással kapcsolatos megjegyzések.
    1. Adja meg az OPC UA-kiszolgáló végponti URL-CÍMÉT (`opc.tcp://<host>:<port>`), és kattintson a **Connect**.
    1. A kapcsolat telepítési folyamat a csatlakoztatott gyár portál (OPC UA-ügyfél) és az OPC UA-kiszolgálóhoz szeretne csatlakozni közötti megbízhatósági kapcsolatot létesít. Az a csatlakoztatott gyári irányítópultot kap egy **nem lehet ellenőrizni a tanúsítványt a kiszolgáló, amelyhez csatlakozni** figyelmeztetés. Amikor megjelenik egy tanúsítványfigyelmeztetés, kattintson a **folytatása**.
    1. Nehezebb a telepítő van a tanúsítvány konfigurálását az OPC UA-kiszolgáló próbál csatlakozni. A PC-alapú OPC UA-kiszolgálók csak kaphat egy figyelmeztető párbeszédpanel az irányítópulton, amely elismeri is. Beágyazott OPC UA-kiszolgálórendszerekkel dokumentációja, az OPC UA-kiszolgálóval, keresse ki hogyan teheti ezt a feladatot. Ez a feladat befejezéséhez szükség lehet a tanúsítványt a csatlakoztatott gyár portál OPC UA-ügyfél. A rendszergazda letöltheti ezt a tanúsítványt a a **saját OPC UA-kiszolgáló csatlakoztatása** oldalon:

        ![Megoldásportál](./media/iot-accelerators-connected-factory-gateway-deployment/image4.png)

1. Keresse meg a OPC UA-csomópontok fa az OPC UA-kiszolgáló, kattintson a jobb gombbal a kívánt értékeket küldeni a csatlakoztatott gyár, és válassza ki az OPC-csomópontokhoz **közzététele**.

1. Telemetriai adatok mostantól jut el az átjáró eszköz. Megtekintheti a telemetriát a **gyári telephelyek** megtekintése mellett a csatlakoztatott gyár portál **új előállító**.

## <a name="next-steps"></a>További lépések

Az Okosgyár-megoldásgyorsító architektúrájával kapcsolatos további információkért lásd: [Okosgyár megoldásgyorsító bemutatója](iot-accelerators-connected-factory-sample-walkthrough.md).

További információ a [OPC-közzétevő referenciamegvalósítása](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-publisher).