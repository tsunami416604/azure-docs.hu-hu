---
title: Az Azure IoT Hub-eszközadatfolyamok | Microsoft dokumentumok
description: Az Azure IoT Hub-eszközadatfolyamok áttekintése, amelyek megkönnyítik a biztonságos kétirányú TCP-alagutakat a felhőből az eszközre irányuló különböző kommunikációs forgatókönyvekhez.
author: robinsh
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: robinsh
ms.openlocfilehash: ff738e56226f7cbb720a754573a9d8607e0e3247
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890460"
---
# <a name="iot-hub-device-streams-preview"></a>IoT Hub-eszközadatfolyamok (előzetes verzió)

Az Azure IoT *Hub-eszközstreamek megkönnyítik* a biztonságos kétirányú TCP-alagutak létrehozását a felhőből az eszközre irányuló különböző kommunikációs forgatókönyvekhez. Az eszközadatfolyam-továbbítást egy IoT *Hub-streamelési végpont* közvetíti, amely proxyként működik az eszköz és a szolgáltatás végpontjai között. Ez az alábbi ábrán látható beállítás különösen akkor hasznos, ha az eszközök hálózati tűzfal mögött vannak, vagy magánhálózaton belül tartózkodnak. Mint ilyen, az IoT Hub-eszközstreamek segítenek az ügyfelek nek az IoT-eszközök tűzfalbarát módon történő elérésében anélkül, hogy széles körben meg kellene nyitniuk a bejövő vagy kimenő hálózati tűzfalportokat.

!["IoT Hub-eszközstreamek áttekintése"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png )

Az IoT Hub-eszközadatfolyamok használatával az eszközök továbbra is biztonságosak maradnak, és csak kimenő TCP-kapcsolatokat kell megnyitniuk az IoT hub streamelési végpontjához a 443-as porton keresztül. Az adatfolyam létrehozása után a szolgáltatásoldali és az eszközoldali alkalmazások programozott hozzáféréssel rendelkeznek a WebSocket ügyfélobjektumhoz, amely nyers bájtokat küld és fogad egymásnak. Az alagút által nyújtott megbízhatósági és rendelési garanciák a TCP-vel egyenrangúak.

## <a name="benefits"></a>Előnyök

Az IoT Hub-eszközadatfolyamok a következő előnyöket biztosítják:

* **Tűzfalbarát biztonságos kapcsolat:** Az IoT-eszközök a szolgáltatás végpontjairól is elérhetők az eszköz vagy a hálózat peremén bejövő tűzfalport megnyitása nélkül (csak a 443-as porton keresztül van szükség az IoT Hubhoz való kimenő kapcsolatra).

* **Hitelesítés:** Az alagút eszköz- és szolgáltatásoldalait is hitelesíteni kell az IoT Hubbal a megfelelő hitelesítő adatok használatával.

* **Titkosítás:** Alapértelmezés szerint az IoT Hub-eszközadatfolyamok TLS-kompatibilis kapcsolatokat használnak. Ez biztosítja, hogy a forgalom mindig titkosítva legyen, függetlenül attól, hogy az alkalmazás titkosítást használ-e vagy sem.

* **A kapcsolat egyszerűsége:** Sok esetben az eszközadatfolyamok használata szükségtelenné teszi a virtuális magánhálózatok összetett beállítását az IoT-eszközökhöz való kapcsolódás engedélyezéséhez.

* **Kompatibilitás a TCP/IP-veremekkel:** Az IoT Hub-eszközadatfolyamok képesek a TCP/IP-alkalmazásforgalom befogadására. Ez azt jelenti, hogy a szabadalmaztatott és a szabványokon alapuló protokollok széles köre használhatja ezt a funkciót.

* **Egyszerű használat a magánhálózati beállításokban:** A szolgáltatás az eszköz IP-címének hivatkozásával kommunikálhat az eszközzel. Ez olyan helyzetekben hasznos, amikor egy eszköz egy magánhálózaton belül található, és magánhálózati IP-címmel rendelkezik, vagy ip-címe dinamikusan van hozzárendelve, és a szolgáltatási oldal ismeretlen.

## <a name="device-stream-workflows"></a>Eszközstream-munkafolyamatok

Az eszközadatfolyam akkor indul el, amikor a szolgáltatás az eszközazonosító megadásával kéri az eszközhöz való csatlakozást. Ez a munkafolyamat különösen illeszkedik egy ügyfél/kiszolgáló kommunikációs modelljébe, beleértve az SSH-t és az RDP-t is, ahol a felhasználó távolról kíván csatlakozni az eszközön ssh- vagy RDP-ügyfélprogrammal futó SSH- vagy RDP-kiszolgálóhoz.

Az eszközstream-létrehozási folyamat magában foglalja az eszköz, a szolgáltatás, az IoT hub fő és a streamelési végpontok egyeztetését. Míg az IoT hub fő végpontja vezényli egy eszközstream létrehozását, a streamelési végpont kezeli a szolgáltatás és az eszköz közötti forgalmat.

### <a name="device-stream-creation-flow"></a>Eszközfolyam-létrehozási folyamat

Az sdk-vel történő eszközfolyam programozott létrehozása a következő lépéseket foglalja magában, amelyek az alábbi ábrán is láthatók:

!["Eszközfolyam-kézfogási folyamat"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png)

1. Az eszközalkalmazás előre regisztrál egy visszahívást, hogy értesítést kapjon arról, hogy mikor indít el új eszközadatfolyamot az eszközre. Ez a lépés általában akkor történik meg, amikor az eszköz elindul, és csatlakozik az IoT Hubhoz.

2. A szolgáltatásoldali program szükség esetén eszközadatfolyamot kezdeményez az eszközazonosító _(nem_ az IP-cím) megadásával.

3. Az IoT hub az 1. Az eszköz elfogadhatja vagy elutasíthatja az adatfolyam-kezdeményezési kérelmet. Ez a logika lehet az alkalmazás forgatókönyv. Ha az eszköz elutasítja az adatfolyam-kérelmet, az IoT Hub ennek megfelelően tájékoztatja a szolgáltatást; ellenkező esetben az alábbi lépések következnek.

4. Az eszköz biztonságos kimenő TCP-kapcsolatot hoz létre a streamelési végponthoz a 443-as porton keresztül, és frissíti a kapcsolatot egy WebSocket-kapcsolatra. A streamelési végpont URL-címét, valamint a hitelesítéshez használt hitelesítő adatokat az IoT Hub egyaránt biztosítja az eszköznek a 3.

5. A szolgáltatás értesítést kap az átviteli adatfolyam ot fogadó eszköz eredményéről, és folytatja a saját WebSocket-ügyfél létrehozását a streamelési végponthoz. Hasonlóképpen megkapja a streamelési végpont URL-címét és az IoT Hub hitelesítési adatait.

A fenti kézfogási folyamatban:

* A kézfogási folyamatnak 60 másodpercen belül be kell fejeződnie (2-5. lépés), ellenkező esetben a kézfogás időtúllépéssel sikertelen lesz, és a szolgáltatás ennek megfelelően értesítést kap.

* Miután a stream létrehozása a fenti folyamat befejeződött, a streamvégpont proxyként fog működni, és a szolgáltatás és az eszköz közötti forgalmat a megfelelő WebSockets keresztül.

* Az eszköznek és a szolgáltatásnak egyaránt szüksége van kimenő kapcsolatra az IoT Hub fő végpontjához, valamint a 443-as porton keresztüli streamelési végponthoz. Ezeknek a végpontoknak az URL-címe az IoT Hub portál *áttekintése* lapján érhető el.

* A létrehozott adatfolyam megbízhatósága és rendelési garanciái a TCP-vel egyenrangúak.

* Az IoT Hub és a streamelési végpont minden kapcsolata TLS-t használ, és titkosított.

### <a name="termination-flow"></a>Végződési folyamat

A létrehozott adatfolyam akkor fejeződik be, amikor az átjáróhoz vezető TCP-kapcsolatok bármelyike megszakad (a szolgáltatás vagy az eszköz által). Ez történhet önkéntesen a WebSocket bezárásával az eszközön vagy a szolgáltatási programokon, vagy önkéntelenül hálózati kapcsolat időtúlszáma vagy folyamathiba esetén. Az eszköz vagy a szolgáltatás és a streamelési végpont közötti kapcsolat megszüntetésekor a másik TCP-kapcsolat is (erőteljesen) megszűnik, és a szolgáltatás és az eszköz felelős az adatfolyam újbóli létrehozásáért, ha szükséges.

## <a name="connectivity-requirements"></a>Kapcsolódási követelmények

Az eszköz és az eszközadatfolyam szolgáltatásoldalának képesnek kell lennie a TLS-kompatibilis kapcsolatok létrehozására az IoT Hubhoz és annak streamelési végpontjához. Ehhez kimenő kapcsolatra van szükség a 443-as porton keresztül ezekhez a végpontokhoz. A végpontokhoz társított állomásnév az IoT Hub *Áttekintés lapján* található, az alábbi ábrán látható módon:

!["Eszközfolyam végpontjai"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Azt is megteheti, hogy a végpontok adatait az Azure CLI használatával `property.hostname` lekérheti a hub tulajdonságai szakaszban, különösen, és `property.deviceStreams` a kulcsok.

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

A kimenet egy JSON-objektum az összes végpont, hogy a hub eszköz és szolgáltatás lehet, hogy csatlakoznia kell az eszköz adatfolyam létrehozásához.

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> Győződjön meg arról, hogy telepítette az Azure CLI 2.0.57-es vagy újabb verzióját. A legújabb verziót az [Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) lapról töltheti le.
>

## <a name="allow-outbound-connectivity-to-the-device-streaming-endpoints"></a>Kimenő kapcsolat engedélyezése az eszköz streamelési végpontjaihoz

Ahogy azt a cikk elején említettük, az eszköz kimenő kapcsolatot hoz létre az IoT Hub streamelési végpontjával az eszközstreamek kezdeményezési folyamata során. Az eszközön vagy annak hálózatán lévő tűzfalaknak engedélyezniük kell a kimenő kapcsolatot a streamelési átjáróhoz a 443-as porton keresztül (vegye figyelembe, hogy a kommunikáció a TLS használatával titkosított WebSocket-kapcsolaton keresztül történik).

Az eszközstreamelési végpont gazdagépneve megtalálható az Azure IoT ![Hub portálon az Áttekintés lapon.](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Ezt az információt az Azure CLI használatával is megtalálhatja:

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> Győződjön meg arról, hogy telepítette az Azure CLI 2.0.57-es vagy újabb verzióját. A legújabb verziót az [Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) lapról töltheti le.
>

## <a name="troubleshoot-via-device-streams-activity-logs"></a>Hibaelhárítás az eszközadatfolyamok tevékenységnaplóin keresztül

Beállíthat Azure Monitor naplók az IT Hub eszközöni adatfolyamok tevékenységnaplójának gyűjtésére. Ez nagyon hasznos lehet a hibaelhárítási forgatókönyvek.

Az alábbi lépéseket követve konfigurálhatja az Azure Monitor naplóit az IoT Hub eszközadatfolyam-tevékenységeihez:

1. Nyissa meg az IoT Hub *Diagnosztikai beállítások* lapját, és kattintson a Diagnosztikai *adatok bekapcsolása* hivatkozásra.

   !["Diagnosztikai naplók engedélyezése"](./media/iot-hub-device-streams-overview/device-streams-diagnostics-settings-pane.png)

2. Adja meg a diagnosztikai beállítások nevét, és válassza *a Küldés a Log Analytics szolgáltatásba* lehetőséget. A rendszer egy meglévő Log Analytics-munkaterületi erőforrás kiválasztásához vagy egy új at hoz létre. Ezenkívül ellenőrizze a *DeviceStreams* a listából.

    !["Eszközadatfolyam-naplók engedélyezése"](./media/iot-hub-device-streams-overview/device-streams-configure-diagnostics.png)

3. Most már elérheti az eszköz adatfolyamok naplók az IoT Hub *portálnaplók* lapján. Az eszközfolyam tevékenységnaplói `AzureDiagnostics` megjelennek `Category=DeviceStreams`a táblázatban, és rendelkeznek .

   Az alábbiakban látható, a céleszköz identitását és a művelet eredményét is elérhető a naplókban.

   !["Eszközfolyam-naplók elérése"](./media/iot-hub-device-streams-overview/device-streams-view-logs.png)

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

A nyilvános előzetes verzió során az IoT Hub eszközadatfolyamai az USA középső régiójában, az USA középső régiójában, Észak-Európában és Délkelet-Ázsiában érhetők el. Győződjön meg arról, hogy a hubot a következő régiók egyikében hozza létre.

## <a name="sdk-availability"></a>SDK elérhetősége

Az egyes adatfolyamok két oldala (az eszköz és a szolgáltatás oldalán) az IoT Hub SDK-t használja az alagút létrehozásához. A nyilvános előzetes verzió során az ügyfelek a következő SDK-nyelvek közül választhatnak:

* A C és C# SDK támogatási eszköze az eszköz oldalán streamel.

* A NodeJS és a C# SDK támogatási eszköz streameli a szolgáltatási oldalon.

## <a name="iot-hub-device-stream-samples"></a>IoT Hub-eszközstreamminták

Két [rövid útmutató minták](/azure/iot-hub) érhetők el az IoT Hub lapon érhető el. Ezek bizonyítják az eszközadatfolyamok alkalmazások általi használatát.

* Az *echo* minta bemutatja az eszközadatfolyamok programozott használatát (az SDK API közvetlen hívásával).

* A *helyi proxyminta* bemutatja a kész ügyfél/kiszolgáló alkalmazás forgalmának (például SSH, RDP vagy web) bújtatását az eszközadatfolyamokon keresztül.

Ezeket a mintákat az alábbiakban részletesebben ismertetjük.

### <a name="echo-sample"></a>Visszhang minta

A visszhangminta bemutatja az eszközadatfolyamok programozott használatát a szolgáltatás és az eszközalkalmazások közötti bájtok küldésére és fogadására. Ne feledje, hogy a szolgáltatás- és eszközprogramokat különböző nyelveken is használhatja. Használhatja például a C eszközprogramot a C# szolgáltatásprogrammal.

Itt vannak a visszhang minták:

* [C# szolgáltatás és szervizprogram](quickstart-device-streams-echo-csharp.md)

* [Node.js szolgáltatásprogram](quickstart-device-streams-echo-nodejs.md)

* [C eszközprogram](quickstart-device-streams-echo-c.md)

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>Helyi proxyminta (SSH-hoz vagy RDP-hez)

A helyi proxyminta bemutatja, hogyan lehet engedélyezni egy meglévő alkalmazás forgalmának bújtatását, amely magában foglalja az ügyfél és a kiszolgálóprogram közötti kommunikációt. Ez a beállítás olyan ügyfél-kiszolgáló protokollokhoz működik, mint az SSH és az RDP, ahol a szolgáltatási oldal ügyfélként működik (SSH vagy RDP ügyfélprogramokat futtat), és az eszközoldal kiszolgálóként működik (SSH démon- vagy RDP-kiszolgálóprogramokat futtatva).

Ez a szakasz az eszközadatfolyamok használatát ismerteti, amelyek lehetővé teszik a felhasználó számára, hogy az SSH-t egy eszközadatfolyamon keresztül ssh-t használjon (az RDP vagy más ügyfél/kiszolgáló alkalmazás esete hasonló a protokoll megfelelő portjának használatával).

A beállítás az alábbi ábrán látható két *helyi proxyprogramot* használja, nevezetesen az *eszköz-helyi proxyt* és *a szolgáltatás-helyi proxyt.* A helyi proxyprogramok felelősek az [eszközstream kezdeményezése kézfogás](#device-stream-creation-flow) ioT hubbal történő végrehajtásáért, valamint az SSH-ügyféllel és az SSH démonnal való rendszeres ügyfél-/kiszolgálószoftvercsatornák használatával való együttműködésért.

!["Eszközfolyam-proxy beállítása SSH/RDP-hez"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png)

1. A felhasználó szolgáltatáson ként használó proxyt futtat, hogy eszközadatfolyamot kezdeményezzen az eszközre.

2. Az eszköz-helyi proxy elfogadja az adatfolyam-kezdeményezési kérelmet, és az alagút jön létre az IoT Hub streamelési végpont (a fent tárgyalt).

3. Az eszköz-helyi proxy csatlakozik az SSH démon végpont figyelése port 22 az eszközön.

4. A szolgáltatás-helyi proxy figyel egy kijelölt porton, amely új SSH-kapcsolatokat vár a felhasználótól (a mintában használt 2222-es port, de ez bármely más elérhető portra konfigurálható). A felhasználó az SSH-ügyfelet a localhost szolgáltatás-helyi proxyportjára helyezi.

### <a name="notes"></a>Megjegyzések

* A fenti lépések teljes egy végpontok közötti alagút az SSH-ügyfél (a jobb oldalon) az SSH démon (a bal oldalon). A végpontok közötti kapcsolat része a forgalom küldése egy eszközadatfolyamon keresztül az IoT Hubba.

* Az ábrán lévő nyilak azt jelzik, hogy milyen irányban jönnek létre a végpontok közötti kapcsolatok. Pontosabban vegye figyelembe, hogy nincs bejövő kapcsolat megy az eszközre (ezt gyakran blokkolja a tűzfal).

* A 2222-es port használata a szolgáltatás-helyi proxyn tetszőleges választás. A proxy beállítható bármely más elérhető port használatára.

* A 22-es port választása protokollfüggő és ebben az esetben az SSH-ra jellemző. Az RDP esetében a 3389-es portot kell használni. Ez a megadott mintaprogramokban konfigurálható.

Az alábbi hivatkozásokon útmutatást talál a helyi proxyprogramok nak az Ön által választott nyelven történő futtatásához. A [visszhangmintához](#echo-sample)hasonlóan az eszköz- és szolgáltatás-helyi proxyprogramokat is futtathatja különböző nyelveken, mivel azok teljes mértékben átjárhatók.

* [C# szolgáltatás és szervizprogram](quickstart-device-streams-proxy-csharp.md)

* [Node.js szolgáltatásprogram](quickstart-device-streams-proxy-nodejs.md)

* [C eszközprogram](quickstart-device-streams-proxy-c.md)

## <a name="next-steps"></a>További lépések

Az alábbi hivatkozásokra kattintva többet is megtudhat az eszközadatfolyamokról.

> [!div class="nextstepaction"]
> [Eszközfolyamok IoT-műsorban (9. csatorna)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
