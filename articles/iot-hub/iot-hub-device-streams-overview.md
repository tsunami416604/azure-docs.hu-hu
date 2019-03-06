---
title: Az Azure IoT Hub eszköz adatfolyamok (előzetes verzió) |} A Microsoft Docs
description: Az IoT Hub eszköz Streamek áttekintése.
author: rezasherafat
manager: briz
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: b5e42585178505ee4c23ca4054e83f61ee1254b8
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436136"
---
# <a name="iot-hub-device-streams-preview"></a>IoT Hub eszköz adatfolyamok (előzetes verzió)

## <a name="overview"></a>Áttekintés
Az Azure IoT Hub *eszköz Streamek* biztonságos kétirányú TCP alagutakat a felhőből az eszközre irányuló kommunikáció forgatókönyvek széles létrehozását segíti elő. Egy eszköz stream által az IoT Hub által van közvetített *streamvégpont* az eszköz és szolgáltatás-végpontok közötti proxyként működik. Ezt a beállítást, az alábbi ábrán kitaláltak különösen hasznos, ha az eszközök a hálózati tűzfal mögött található, vagy magánhálózaton belüli – terheléselosztás. Mint ilyen az IoT Hub eszköz adatfolyamok segítségével cím ügyfelek kell elérni az IoT-eszközök, tűzfalbarát módon és széles körben bejövő vagy kimenő hálózati tűzfal portjainak megnyitása nélkül.

![Helyettesítő szöveg](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png "IoT Hub device streameli áttekintése")


Használja az IoT Hub eszköz adatfolyamok, eszközök biztonságáról és csak a 443-as porton keresztüli kimenő TCP-kapcsolatok az IoT hub streamvégpont megnyitásához. Ha egy stream létrejött, a szolgáltatás és a eszköz-kiszolgálóoldali alkalmazások minden egyes lesz WebSocket ügyfél objektumra küldéséhez és fogadásához egymáshoz nyers bájt programozás alapú hozzáférést. A megbízhatóság és a rendezés ezt az alagutat által nyújtott garanciák hatékonysága eléri a TCP van.

## <a name="benefits"></a>Előnyök
Az IoT Hub eszköz Streamek előnyei a következők:
- **Biztonságos kapcsolatok tűzfalbarát:** IoT-eszközök a Szolgáltatásvégpontok az eszköz vagy hálózati régebben a (csak kimenő kapcsolat az IoT Hub 443-as porton van szükség), a bejövő tűzfalportot megnyitása nélkül érhető el.

- **Hitelesítés:** Az alagút eszköz és a szolgáltatás oldalát az IoT Hub megfelelő hitelesítő adataikkal hitelesítenie kell.

- **Titkosítás:** Alapértelmezés szerint az IoT Hub eszköz Streamek használja a TLS-kompatibilis kapcsolatok. Ez biztosítja, hogy a forgalmat a rendszer mindig titkosítja függetlenül attól, hogy az alkalmazás titkosítást használ vagy sem.

- **Egyszerűség érdekében a hálózati kapcsolat:** Sok esetben eszköz Streamek használatát kiküszöböli a virtuális magánhálózatok összetett telepítési IoT-eszköz csatlakozásának engedélyezéséhez.

- **Kompatibilitás a TCP/IP-verem:** Az IoT Hub eszköz Streamek képes kezelni a TCP/IP-alkalmazásforgalomba. Ez azt jelenti, hogy a saját fejlesztésű, valamint szabványalapú protokollok széles kihasználhatják ezt a szolgáltatást.

- **Egyszerű használat a magánhálózaton beállításokat:** Szolgáltatás hivatkozik, az eszköz Azonosítóját, nem pedig eszköz IP-címének kommunikálhat egy eszközt. Ez az eszköz egy magánhálózaton belül található, és privát IP-címmel rendelkezik, vagy IP-címének dinamikusan van hozzárendelve, és nem ismeri fel a szolgáltatás oldalán hasznos.

## <a name="device-stream-workflows"></a>Eszköz Stream munkafolyamatok
Egy eszköz stream indul, amikor a szolgáltatás csatlakozni, hogy egy eszköz azáltal, hogy az eszköz azonosítójával. Ez a munkafolyamat egy ügyfél-kiszolgáló modellt, beleértve az SSH és RDP-t, ha egy felhasználó távolról csatlakozni a SSH vagy RDP-kiszolgáló SSH vagy RDP ügyfélprogrammal, az eszközön futó kíván különösen illeszkedik.

Az eszköz stream létrehozási folyamata magában foglalja a egy egyeztetést, az eszköz, a szolgáltatás, az IoT hub fő és a streamvégpontok között. Fő végponthoz az IoT hub létrehozása egy eszköz stream koordinálja, míg a tartalomstreameléshez használt streamvégpont kezeli a forgalmat a szolgáltatás és az eszköz között.

### <a name="device-stream-creation-flow"></a>Eszköz stream létrehozási folyamat
Egy eszköz streamet, az SDK-val programozott létrehozása magában foglalja az alábbi lépésekkel, amelyeket a rendszer kitaláltak, az alábbi ábrán is:

![Helyettesítő szöveg](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png "eszköz adatfolyam-kézfogás folyamata")


1. Az eszköz alkalmazás regisztrál egy visszahívást, előzetesen, hogy értesítést kapjon amikor egy új eszköz stream indításakor az eszközön. Ezt a lépést általában akkor kerül sor, amikor az eszköz elindul, és csatlakozik az IoT hubhoz.

2. A Szolgáltatásoldali program azáltal, hogy az eszköz azonosítója szükség esetén egy eszköz stream kezdeményez (_nem_ az IP-cím).

3. Az IoT hub eszközoldali program értesíti az egyes moduloknak a visszahívási az 1. lépésben regisztrált. Az eszköz lehet, hogy fogadja el, vagy a stream kezdeményezés kérelem elutasítása. Lehet, hogy a logikai alkalmazás forgatókönyvnek adott. A streamkérelmet elutasította az eszközt, ha az IoT Hub; arról tájékoztatja a szolgáltatás egyéb esetben kövesse az alábbi lépéseket.

4. Az eszköz a tartalomstreameléshez használt streamvégpont biztonságos kimenő TCP-kapcsolatot hoz létre a 443-as porton keresztül, és a kapcsolat a WebSocket rendszerre frissül. URL-címét a tartalomstreameléshez használt streamvégpont, valamint a hitelesítő adatokat használják a hitelesítéshez is számára az eszköz által biztosított IoT Hub a 3. lépésben küldött kérelem részeként.

5. A szolgáltatás fogadja a stream eszköz eredményéről értesítést kap, és a saját WebSocket-ügyfél a streamvégpont létrehozása abból. Ehhez hasonlóan kapja, a streamelési végpont URL-címet és hitelesítési adatait az IoT hubról.

A fenti kézfogás folyamata:
- A kézfogás folyamata végrehajtandó 60 másodperc (2 – 5. lépés), ellenkező esetben a kézfogás sikertelen, és az időtúllépés, és a szolgáltatás ennek megfelelő értesítést fog kapni.

- A fenti stream létrehozási folyamat befejezése után a tartalomstreameléshez használt streamvégpont proxyként lesz, és fog forgalmat a szolgáltatás és az eszköz közötti hálózaton keresztül helyezze át a megfelelő websockets protokollt.

- Eszköz és a szolgáltatás két fő végponthoz az IoT Hub, valamint a tartalomstreameléshez használt streamvégpont kimenő kapcsolódnia kell a 443-as porton keresztül. Az URL-címét a végpontokkal érhető el az *áttekintése* fülre az IoT Hub portálon.

- A megbízhatóság és a egy meglévő adatfolyam garanciák rendezése hatékonysága eléri a TCP van.

- Az IoT Hub és a tartalomstreameléshez használt streamvégpont irányuló összes kapcsolatot TLS használatát, és vannak titkosítva.

### <a name="termination-flow"></a>Megszakítási folyamat
Egy meglévő adatfolyam megszakítása, ha a TCP-kapcsolat az átjáróhoz (amelyet a szolgáltatás vagy az eszköz) le vannak választva. Ez történhet önkéntesen zárja be a WebSocket az eszköz vagy a szolgáltatás a programokat, vagy érzékelőire hálózati kapcsolat időtúllépése vagy a folyamat hiba esetén. Eszköz vagy a szolgáltatás a tartalomstreameléshez használt streamvégpont kapcsolat megszűnése után a TCP-kapcsolatot is (kényszerített) befejeződik, és a szolgáltatás és az eszköz felelősek újra létre kell hoznia a streamet, ha szükséges.


## <a name="connectivity-requirements"></a>Hálózati kapcsolati követelményeinek

Az eszköz és a egy eszköz stream szolgáltatás oldalán is képes kapcsolatokat a TLS-kompatibilis IoT hubot és a tartalomstreameléshez használt streamvégpont kell lennie. Kimenő kapcsolat ehhez a végpontokkal 443-as porton keresztül. Az állomásnév, ezeket a végpontokat társított találhatók a *áttekintése* fülre az IoT Hub, az alábbi ábrán látható módon: ![Helyettesítő szöveg](./media/iot-hub-device-streams-overview/device-stream-portal.png "eszköz stream végpontok")

Másik lehetőségként a végpontok információk használata lehet beolvasni a hub tulajdonságok szakasz alatt, Azure CLI használatával `property.hostname` és `property.deviceStreams` kulcsok.

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

JSON-objektum, amely a hub eszköz és szolgáltatás lehet csatlakoztatni kell egy eszköz stream jöjjön összes végpont kimenete.

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> Győződjön meg arról, telepített Azure CLI-vel 2.0.57 verzió vagy újabb. A legújabb verziót [innen](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) töltheti le.
> 

## <a name="whitelist-device-streaming-endpoints"></a>Engedélyezési lista eszköz adatfolyam-továbbítási végpontok

Ahogy említettük [korábbi](#Overview), az eszköz létrehoz egy kimenő kapcsolatot az IoT Hub streamvégpont során eszköz Streamek kezdeményezés folyamatot. A tűzfalnak az eszközön vagy a hálózati engedélyeznie kell a kimenő kapcsolatot a 443-as porton keresztül az adatfolyam-továbbítási átjáró (vegye figyelembe, hogy a kommunikáció történik a TLS használatával titkosított WebSocket kapcsolaton keresztül).

Az Azure IoT Hub Portal Áttekintés lapján található eszköz streamvégpont állomásnevét. ![Helyettesítő szöveg](./media/iot-hub-device-streams-overview/device-stream-portal.PNG "eszköz stream végpontok")

Másik lehetőségként találja meg ezeket az információkat az Azure CLI használatával:

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> Győződjön meg arról, telepített Azure CLI-vel 2.0.57 verzió vagy újabb. A legújabb verziót [innen](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) töltheti le.
> 

## <a name="troubleshoot-via-device-streams-activity-logs"></a>Hibaelhárítás eszköz Streamek tevékenységeket tartalmazó naplók

Az Azure Monitor naplóira állíthat az IoT hub eszköz Streamek tevékenységnaplójának gyűjtéséhez. Ez a hibaelhárítási feladatokhoz nagyon hasznos lehet.

Az Azure Monitor konfigurálásáról az IoT Hub eszköz stream tevékenységek az alábbi lépésekkel:

1. Keresse meg a *diagnosztikai beállítások* az IoT Hub lapra, majd kattintson a *diagnosztika bekapcsolása* hivatkozásra.

  ![Helyettesítő szöveg](./media/iot-hub-device-streams-overview/device-streams-diagnostics-settings.PNG "disgnostics naplók engedélyezése")


2. Adjon meg egy nevet a diagnosztikai beállítások, és válassza a *Küldés a Log Analyticsnek* lehetőséget. Válasszon egy meglévő Log Analytics munkaterület erőforrás, vagy hozzon létre egy újat a Microsofthoz. Ezenkívül ellenőrizze a *DeviceStreams* a listából.

    ![Helyettesítő szöveg](./media/iot-hub-device-streams-overview/device-streams-diagnostics.PNG "Enable device streameli naplók")

3. Most már elérheti az eszköznaplók Streamek alatt a *naplók* az IoT Hub portálon fülre. Tevékenységnaplók streamelése eszköz fog megjelenni a `AzureDiagnostics` táblát, és rendelkezik `Category=DeviceStreams`.

    <p>
Alább látható módon a céleszközön, és a művelet eredményét identitását is rendelkezésre áll a naplók.
    ![Helyettesítő szöveg](./media/iot-hub-device-streams-overview/device-streams-log-analytics.PNG "eszköz stream naplók elérése")


## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

A nyilvános előzetes verzióban az IoT Hub eszköz Streamek régiókban érhető el az USA középső Régiójában és a központi USA régiója – EUAP. Ellenőrizze, hogy ezek a régiók egyikében a hub hoz létre. 


## <a name="sdk-availability"></a>SDK-t rendelkezésre állása

Minden stream (az eszköz és szolgáltatás oldalon) két oldalán az IoT Hub SDK segítségével létrehozza az alagutat. A nyilvános előzetes verzióban a vevők a következő SDK nyelvek közül választhat:
- A C és C# SDK-k eszköz Streamek támogatja az eszközoldalon.

- A NodeJS és C# SDK támogatja az eszköz Streamek Szolgáltatásoldali.


## <a name="iot-hub-device-stream-samples"></a>IoT Hub Device Stream Samples

Microsoft közzétette két [rövid minták](/azure/iot-hub) eszköz Streamek használatának bemutatása az alkalmazások.
* A *echo* minta azt ismerteti, eszköz-Streamek programozott felhasználása (a közvetlen hívása az SDK API-k).
* A *helyi proxy* minta azt mutatja be az alkalmazásforgalomba megoldásszolgáltatóknál ügyfél-kiszolgáló (például SSH, RDP vagy web) bújtatás eszköz Streamek keresztül.

Ezek a minták terjed ki az alábbi részletesebben.

### <a name="echo-sample"></a>A minta echo
Az echo programozott küldése és fogadása service és az eszköz alkalmazások közötti eszköz-Streamek használatát mutatja be. A gyors útmutatók eléréséhez használja az alábbi hivatkozásokat. Vegye figyelembe, hogy a különböző nyelveken is használhatja a szolgáltatást és az eszköz programok, például C eszközprogram együttműködve C# szolgáltatási program.

| SDK    | Szolgáltatási Program                                          | Eszközprogram                                           |
|--------|----------------------------------------------------------|----------------------------------------------------------|
| C#     | [Hivatkozás](quickstart-device-streams-echo-csharp.md) | [Hivatkozás](quickstart-device-streams-echo-csharp.md) |
| Node.js | [Hivatkozás](quickstart-device-streams-echo-nodejs.md) | -                                                        |
| C      | -                                                        | [Hivatkozás](quickstart-device-streams-echo-c.md)      |

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>Helyi Proxy minta (az SSH vagy RDP)
A helyi proxy engedélyezhető, amely magában foglalja az ügyfél és a egy kiszolgálói program közötti kommunikációs forgalom egy meglévő alkalmazás tunneling mutatja be. Ügyfél/kiszolgáló irányú protokollok működik a beállítása, mint az SSH és az RDP, ahol a Szolgáltatásoldali (futó SSH vagy RDP ügyfélprogramok) ügyfél funkcionál, és az eszközoldali úgy működik, mint a kiszolgáló (RDP-kiszolgáló programok telepítése és az SSH démon fut). 

Ez a szakasz eszköz Streamek eszköz adatfolyamok (RDP vagy más ügyfél-kiszolgáló alkalmazás esetében is hasonlóak a protokoll megfelelő port használatával) a felhasználó számára az SSH egy eszközre való használatát ismerteti.

A telepítő kihasználja két *helyi proxy* programok, nevezetesen az alábbi ábrán látható *eszköz helyi proxy* és *szolgáltatás helyi proxy*. A helyi proxy programok hajtják végre a [eszköz stream kezdeményezés kézfogás](#device-stream-creation-flow) az IoT hubbal, és az SSH-ügyfél és az SSH démon használatával a szokásos ügyfél-kiszolgáló sockets implementálására.

![Helyettesítő szöveg](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png "eszköz stream proxy beállítása SSH vagy RDP-hez")

1. A felhasználó kezdeményezni az eszközön egy eszköz stream szolgáltatást helyi proxyt futtat a.

2. Az eszköz helyi proxykiszolgáló fogadja a stream kezdeményezés kéréseket, és az alagút létrehozása az IoT Hub streamvégpont (fentiekben leírtak szerint).

3. Az eszköz helyi proxy csatlakozik az SSH démon végpont a 22-es port az eszközön.

4. Várakozás új SSH-kapcsolatok a felhasználó a kijelölt porton figyeli a szolgáltatás helyi proxy (2222-es port használatban a mintát, de ez konfigurálható az összes rendelkezésre álló portot). A felhasználó az SSH-ügyfél a szolgáltatás helyi proxyport localhoston mutat.

### <a name="notes"></a>Megjegyzések
- A fenti lépések elvégzéséhez egy teljes körű alagutat az SSH-ügyfél (a jobb oldalon), az SSH démon (a bal oldalon) között. A végpontok közötti kapcsolat része magában foglalja a forgalmat küld az IoT Hub eszköz adatfolyam felett.

- Az ábrán a nyilak, amelyben létesít kapcsolatokat, végpontok közötti irányát jelzik. Kifejezetten vegye figyelembe, hogy nincs-e az eszköz (Ez gyakran blokkolja a tűzfal) fog egy kimenő kapcsolatot sem.

- A szolgáltatás helyi proxyn 2222-es port használatával, amely egy tetszőleges lehetőség. A proxy beállítható úgy, hogy az összes rendelkezésre álló portot használja.

- A 22-es portot, amely procotocol-függő és adott ssh ebben az esetben. Az RDP a helyzet a 3389-es portot kell használni. Ez a megadott minta programok telepítése és törlése is konfigurálható.

Útmutatást nyújt a helyi proxy programok futtatását tetszőleges nyelven használja az alábbi hivatkozásokat. Hasonló a [echo minta](#echo-sample), eszköz - és szolgáltatás helyi proxy programok futtathatók különböző nyelveken azok teljes mértékben kompatibilissé.

| SDK    | Service-Local Proxy                                       | Eszköz helyi Proxy                                |
|--------|-----------------------------------------------------------|---------------------------------------------------|
| C#     | [Hivatkozás](quickstart-device-streams-proxy-csharp.md)  | [Hivatkozás](quickstart-device-streams-proxy-csharp.md) |
| NodeJS | [Hivatkozás](quickstart-device-streams-proxy-nodejs.md)         | -                                                 |
| C      | -                                                         | [Hivatkozás](quickstart-device-streams-proxy-c.md)      |

## <a name="next-steps"></a>További lépések

További információ az eszköz adatfolyamok az alábbi hivatkozásokat:

> [!div class="nextstepaction"]
> [IoT-eszköz adatfolyamokat megjelenítése (Channel 9-on)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
> [próbálja meg egy eszköz stream a rövid útmutató](/azure/iot-hub)
