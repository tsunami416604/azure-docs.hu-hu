---
title: Alsóbb rétegbeli IoT Edge-eszközök csatlakoztatása – Azure IoT Edge | Microsoft Docs
description: IoT Edge eszköz konfigurálása Azure IoT Edge Gateway-eszközökhöz való kapcsolódáshoz.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: d5da6576258d3e33296781bbc262494220140ddc
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489284"
---
# <a name="connect-a-downstream-iot-edge-device-to-an-azure-iot-edge-gateway-preview"></a>Alsóbb rétegbeli IoT Edge eszköz csatlakoztatása Azure IoT Edge átjáróhoz (előzetes verzió)

Ez a cikk egy IoT Edge átjáró és egy alsóbb rétegbeli IoT Edge-eszköz közötti megbízható kapcsolat létesítéséhez nyújt útmutatást.

>[!NOTE]
>Ehhez a szolgáltatáshoz a IoT Edge 1,2-es verziója szükséges, amely nyilvános előzetes verzióban, Linux-tárolókat futtat.

Egy átjáró-forgatókönyvben egy IoT Edge eszköz lehet átjáró és alsóbb rétegbeli eszköz is. Több IoT Edge átjáró is létrehozható az eszközök hierarchiájának létrehozásához. Az alsóbb rétegbeli (vagy gyermek) eszközök az átjáró (vagy a szülő) eszközön keresztül hitelesíthetők, és küldhetők vagy fogadhatnak üzeneteket.

Az átjáró-hierarchiában két különböző konfiguráció van IoT Edge-eszközökhöz, és ez a cikk mindkettőt is tárgyalja. Az első a **legfelső réteg** IoT Edge eszköz. Ha több IoT Edge-eszköz csatlakozik egymáshoz, minden olyan eszköz, amely nem rendelkezik fölérendelt eszközzel, de közvetlenül kapcsolódik a IoT Hubhoz, a felső rétegben kell tekinteni. Az eszköz feladata, hogy a kérelmeket az alatta lévő összes eszközről kezelje. A másik konfiguráció minden IoT Edge eszközre érvényes a hierarchia **alsóbb rétegében** . Ezek az eszközök lehetnek átjárók más alsóbb rétegbeli IoT és IoT Edge eszközökhöz, de a saját szülői eszközein keresztül is át kell irányítani a kommunikációt.

Egyes hálózati architektúrák megkövetelik, hogy a hierarchiában csak a legfelső IoT Edge eszköz tud csatlakozni a felhőhöz. Ebben a konfigurációban a hierarchia alsóbb rétegeiben lévő összes IoT Edge-eszköz csak az átjáróval (vagy a szülővel) és az alárendelt (vagy gyermek) eszközökkel tud kommunikálni.

A cikkben ismertetett lépések a [IoT Edge-eszköz konfigurálása, amely transzparens átjáróként működik](how-to-create-transparent-gateway.md), amely egy IoT Edge eszköz átjáróként való használatát állítja be az alsóbb rétegbeli IoT-eszközökhöz. Ugyanazok az alapvető lépések érvényesek az összes átjáróra:

* **Hitelesítés** : hozzon létre IoT hub identitásokat az átjáró-hierarchiában lévő összes eszközhöz.
* **Engedélyezés** : állítsa be a szülő/gyermek kapcsolatot IoT hub, hogy a gyermek eszközök csatlakozzanak a fölérendelt eszközhöz, például a IoT hubhoz.
* **Átjáró felderítése** : gondoskodjon arról, hogy a gyermek eszköz a helyi hálózaton találja a fölérendelt eszközét.
* **Biztonságos kapcsolat** : hozzon létre biztonságos kapcsolatot az azonos lánc részét képező megbízható tanúsítványokkal.

## <a name="prerequisites"></a>Előfeltételek

* Egy ingyenes vagy standard IoT hub.
* Legalább két **IoT Edge eszköz** , az egyik a legfelső rétegű eszköz, és egy vagy több alsóbb rétegbeli eszköz. Ha nem áll rendelkezésre IoT Edge eszköz, [futtathatja Azure IoT Edge Ubuntu rendszerű virtuális gépeken](how-to-install-iot-edge-ubuntuvm.md)is.
* Ha az Azure CLI-t használja az eszközök létrehozásához és kezeléséhez, az Azure CLI v 2.3.1-es verziója az Azure IoT Extension v 0.10.6 vagy újabb verzióra van telepítve.

Ez a cikk részletesen ismerteti azokat a lépéseket és lehetőségeket, amelyekkel a forgatókönyvhöz megfelelő átjáró-hierarchia hozható létre. Interaktív oktatóanyagért lásd: [IoT Edge-eszközök hierarchiájának létrehozása átjárók használatával](tutorial-nested-iot-edge.md).

## <a name="create-a-gateway-hierarchy"></a>Átjáró-hierarchia létrehozása

IoT Edge átjáró-hierarchiát úgy hozhat létre, hogy meghatározza a forgatókönyvben szereplő IoT Edge eszközök szülő/gyermek kapcsolatait. Beállíthat egy fölérendelt eszközt új eszköz identitásának létrehozásakor, vagy egy meglévő eszköz identitásának szülőjét és gyermekeit is kezelheti.

A szülő-gyermek kapcsolatok beállításának lépése arra jogosítja fel a gyermekeket, hogy csatlakozzanak a fölérendelt eszközhöz, például IoT Hubhoz.

Csak IoT Edge eszközök lehetnek szülő eszközök, de a IoT Edge-eszközök és a IoT-eszközök is gyermekek lehetnek. A szülőnek több gyermeke is lehet, de egy gyermek csak egyetlen szülővel rendelkezhet. Az átjáró-hierarchiát a szülő-gyermek csoportok összeláncolásával hozza létre, így az egyik eszköz gyermeke egy másik szülő.

<!-- TODO: graphic of gateway hierarchy -->

# <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Portal a szülő-gyermek kapcsolatot kezelheti új eszköz-identitások létrehozásakor vagy meglévő eszközök szerkesztésével.

Új IoT Edge-eszköz létrehozásakor lehetősége van a szülő és gyermek eszközök kiválasztására az adott központban lévő meglévő IoT Edge-eszközök listájából.

1. Az [Azure Portalon](https://portal.azure.com) keresse meg az IoT hubot.
1. A navigációs menüből válassza a **IoT Edge** lehetőséget.
1. Válassza **a IoT Edge eszköz hozzáadása** lehetőséget.
1. Az eszköz-azonosító és a hitelesítési beállítások megadása mellett beállíthat **egy fölérendelt eszközt** , vagy **kiválaszthatja a gyermek eszközöket**.
1. Válassza ki a szülőként vagy gyermekként használni kívánt eszközt vagy eszközöket.

A meglévő eszközökhöz szülő-gyermek kapcsolatokat is létrehozhat vagy kezelhet.

1. Az [Azure Portalon](https://portal.azure.com) keresse meg az IoT hubot.
1. A navigációs menüből válassza a **IoT Edge** lehetőséget.
1. Válassza ki a felügyelni kívánt eszközt a **IoT Edge eszközök** listájából.
1. Válassza **a szülő eszköz beállítása vagy a** **gyermek eszközök kezelése** lehetőséget.
1. Adjon hozzá vagy távolítson el minden szülő-vagy gyermek-eszközt.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-hez készült [Azure-IOT](/cli/azure/ext/azure-iot) bővítmény parancsokat biztosít a IOT-erőforrások kezeléséhez. A IoT és a IoT Edge eszközök szülő/gyermek kapcsolatát kezelheti új eszköz-identitások létrehozásakor vagy meglévő eszközök szerkesztésével.

Az az [IOT hub Device-Identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) set of parancsok lehetővé teszik az adott eszköz szülő-és gyermek-kapcsolatainak kezelését.

A `create` parancs paramétereket tartalmaz a gyermek eszközök hozzáadásához és a szülő eszköz beállításához az eszköz létrehozásakor.

A további eszköz-azonosító parancsok, beleértve a, a `add-children` `list-children` és a és a `remove-children` `get-parent` `set-parent` , lehetővé teszik a szülő-gyermek kapcsolatok kezelését a meglévő eszközökhöz.

---

## <a name="prepare-certificates"></a>Tanúsítványok előkészítése

A tanúsítványok konzisztens láncát az azonos átjáró-hierarchiában található eszközök között kell telepíteni, hogy azok egymás közötti biztonságos kommunikációt hozzanak létre. A hierarchiában lévő összes eszköznek, akár IoT Edge eszköznek, akár egy IoT Leaf eszköznek, ugyanarra a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány másolatára van szüksége. A hierarchia minden IoT Edge eszköze a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használja az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának gyökeréhez.

Ezzel a beállítással minden alsóbb rétegbeli IoT Edge eszköz-vagy IoT-eszköz képes ellenőrizni a szülő identitását annak ellenőrzésével, hogy a csatlakoztatott edgeHub rendelkezik-e a megosztott legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvánnyal aláírt kiszolgálói tanúsítvánnyal.

<!-- TODO: certificate graphic -->

Hozza létre a következő tanúsítványokat:

* Egy **legfelső szintű hitelesítésszolgáltatói tanúsítvány** , amely egy adott átjáró-hierarchiában lévő összes eszköz legfelső szintű megosztott tanúsítványa. Ez a tanúsítvány minden eszközre telepítve van.
* Minden olyan **köztes tanúsítvány** , amelyet fel szeretne venni a főtanúsítvány-láncba.
* Az **eszköz hitelesítésszolgáltatói tanúsítványa** és a hozzá tartozó **titkos kulcs** , amelyet a gyökér és a köztes tanúsítvány generál. Az átjáró-hierarchiában minden IoT Edge eszközhöz egyedi HITELESÍTÉSSZOLGÁLTATÓI tanúsítványra van szükség.

>[!NOTE]
>Jelenleg a libiothsm korlátozásai meggátolják a 2038 január 1-jén vagy azt követően lejáró tanúsítványok használatát.

Használhat önaláírt hitelesítésszolgáltatót, vagy vásárolhat egy megbízható kereskedelmi hitelesítésszolgáltatótól (például Baltimore, VeriSign, Digicert vagy GlobalSign).

Ha nem rendelkezik saját tanúsítványokkal, [létrehozhat bemutató tanúsítványokat IoT Edge eszköz funkcióinak teszteléséhez](how-to-create-test-certificates.md). A cikk lépéseit követve hozzon létre egy gyökér-és köztes tanúsítványokat, majd hozzon létre IoT Edge eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat az egyes eszközökhöz.

## <a name="configure-iot-edge-on-devices"></a>IoT Edge konfigurálása eszközökön

Az átjáróként való IoT Edge beállításának lépései nagyon hasonlítanak a IoT Edge alsóbb rétegbeli eszközként való beállításának lépéseihez.

Az átjárók felderítésének engedélyezéséhez minden IoT Edge átjáró-eszközt egy olyan **állomásnévvel** kell konfigurálni, amelyet a gyermek eszköze a helyi hálózaton való kereséshez használni fog. Minden alsóbb rétegbeli IoT Edge eszközt a kapcsolódáshoz **parent_hostname** kell konfigurálni. Ha egyetlen IoT Edge eszköz szülő és gyermek eszköz is, mindkét paraméternek szüksége van.

A biztonságos kapcsolatok engedélyezéséhez az átjáró-forgatókönyvben minden IoT Edge eszközt egyedi HITELESÍTÉSSZOLGÁLTATÓI tanúsítvánnyal kell konfigurálni, és az átjáró-hierarchia összes eszköze által megosztott legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány másolatát.

Az eszközön már telepítve kell lennie IoT Edge. Ha nem, kövesse a [Azure IoT Edge futtatókörnyezet telepítéséhez](how-to-install-iot-edge.md) szükséges lépéseket, majd helyezze üzembe az eszközt [szimmetrikus kulcsos hitelesítéssel](how-to-manual-provision-symmetric-key.md) vagy [X. 509 tanúsítványalapú hitelesítéssel](how-to-manual-provision-x509.md).

Az ebben a szakaszban ismertetett lépések a **legfelső szintű hitelesítésszolgáltatói tanúsítványra** és az **eszköz hitelesítésszolgáltatói tanúsítványára, valamint** a jelen cikk korábbi részében tárgyalt titkos kulcsra hivatkoznak. Ha egy másik eszközön hozta létre ezeket a tanúsítványokat, azok elérhetők lesznek az eszközön. Átviheti a fájlokat fizikailag, például USB-meghajtóval, egy szolgáltatással (például [Azure Key Vault](../key-vault/general/overview.md)), vagy egy olyan funkcióval, mint a [biztonságos fájlmásolás](https://www.ssh.com/ssh/scp/).

A következő lépésekkel konfigurálhatja a IoT Edget az eszközön.

Linux rendszeren ellenőrizze, hogy a felhasználó **iotedge** rendelkezik-e olvasási engedéllyel a tanúsítványokat és kulcsokat tároló címtárhoz.

1. Telepítse a **legfelső szintű hitelesítésszolgáltatói tanúsítványt** erre a IoT Edge eszközre.

   ```bash
   sudo cp <path>/<root ca certificate>.pem /usr/local/share/ca-certificates/<root ca certificate>.pem
   ```

1. Frissítse a tanúsítványtárolót.

   ```bash
   sudo update-ca-certificates
   ```

   A parancs kimenete azt eredményezi, hogy az egyik tanúsítvány hozzá lett adva a/etc/SSL/certs.

1. Nyissa meg a IoT Edge biztonsági démon konfigurációs fájlját.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Keresse meg a **tanúsítványok** szakaszt a config. YAML fájlban. Frissítse a három tanúsítvány mezőt úgy, hogy az a tanúsítványokra mutasson. Adja meg a fájl URI elérési útját, amely a formátumot használja `file:///<path>/<filename>` .

   * **device_ca_cert** : az eszköz egyedi hitelesítésszolgáltatói tanúsítványának fájl URI elérési útja.
   * **device_ca_pk** : a fájl URI-elérési útja az eszközhöz egyedi hitelesítésszolgáltató titkos kulcsához.
   * **trusted_ca_certs** : az átjáró-hierarchiában lévő összes eszköz által megosztott legfelső szintű hitelesítésszolgáltatói tanúsítvány FÁJLjának URI-elérési útja.

1. Keresse meg a **hostname** paramétert a config. YAML fájlban. Frissítse a gazdagépet a teljes tartománynév (FQDN) vagy a IoT Edge eszköz IP-címének megadásával.

   Ennek a paraméternek az értéke, amelyet az alsóbb rétegbeli eszközök fognak használni az átjáróhoz való kapcsolódáshoz. Az állomásnév alapértelmezés szerint a számítógépnévre kerül, de az alárendelt eszközök csatlakoztatásához a teljes tartománynév vagy az IP-cím szükséges.

   Használjon 64 karakternél rövidebb állomásnevet, amely a kiszolgálói tanúsítvány köznapi nevének a karakteres korlátja.

   Konzisztensnek kell lennie az állomásnév-mintázattal az átjáró-hierarchián belül. Használjon teljes tartománynevet vagy IP-címet, de mindkettőt nem.

1. **Ha ez az eszköz egy gyermek eszköz** , keresse meg a **parent_hostname** paramétert. Frissítse a **parent_hostname** mezőt úgy, hogy a szülő eszköz teljes tartományneve vagy IP-címe legyen, és hogy az a szülő config. YAML fájljában található állomásnévként legyen megadva.

1. Habár ez a funkció nyilvános előzetes verzióban érhető el, a IoT Edge eszközt úgy kell konfigurálnia, hogy az indításkor a IoT Edge-ügynök nyilvános előzetes verzióját használja.

   Keresse meg az **ügynök** YAML szakaszt, és frissítse a rendszerkép értékét a nyilvános előzetes rendszerképre:

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc1"
       auth: {}
   ```

1. Mentse ( `Ctrl+O` ) és `Ctrl+X` a config. YAML fájlt a Bezárás () gombra.

1. Ha korábban más tanúsítványokat is használt a IoT Edgehoz, törölje az alábbi két könyvtár fájljait, hogy megbizonyosodjon róla, hogy az új tanúsítványok érvénybe lépnek:

   * `/var/lib/iotedge/hsm/certs`
   * `/var/lib/iotedge/hsm/cert_keys`

1. A módosítások alkalmazásához indítsa újra a IoT Edge szolgáltatást.

   ```bash
   sudo systemctl restart iotedge
   ```

1. Keressen hibákat a konfigurációban.

   ```bash
   sudo iotedge check --verbose
   ```

   >[!TIP]
   >A IoT Edge-ellenőrzési eszköz egy tárolót használ a diagnosztika egyes ellenőrzésének elvégzéséhez. Ha az eszközt az alsóbb rétegbeli IoT Edge eszközökön szeretné használni, győződjön meg arról, hogy hozzáférnek-e az eszközhöz `mcr.microsoft.com/azureiotedge-diagnostics:latest` , vagy hogy a tároló képe a saját tároló beállításjegyzékében van-e.

## <a name="configure-runtime-modules-for-public-preview"></a>Futásidejű modulok konfigurálása nyilvános előzetes verzióhoz

Habár ez a funkció nyilvános előzetes verzióban érhető el, konfigurálnia kell a IoT Edge eszközt az IoT Edge Runtime-modulok nyilvános előzetes verziójára. Az előző szakasz a edgeAgent konfigurálásának lépéseit ismerteti indításkor. A futásidejű modulokat is konfigurálnia kell az eszköz telepítéséhez.

1. Konfigurálja a edgeHub modult a nyilvános előzetes rendszerkép használatára: `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc1` .

1. Konfigurálja az alábbi környezeti változókat a edgeHub modulhoz:

   | Name | Érték |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. Konfigurálja a edgeAgent modult a nyilvános előzetes rendszerkép használatára: `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc1` .

## <a name="network-isolate-downstream-devices"></a>Hálózati elkülönítésű alsóbb szintű eszközök

A cikkben ismertetett lépések IoT Edge az eszközöket átjáróként vagy alsóbb rétegbeli eszközként, és megbízható kapcsolatot hoznak létre egymás között. Az átjáró eszköz az alsóbb rétegbeli eszköz és a IoT Hub közötti interakciókat kezeli, beleértve a hitelesítést és az üzenet-útválasztást is. Az alsóbb rétegbeli IoT Edge eszközökön üzembe helyezett modulok továbbra is létrehozhatják saját kapcsolataikat a Cloud Services szolgáltatással.

Bizonyos hálózati architektúrák, például az ISA-95 szabványt követők, az internetkapcsolatok számának minimalizálására törekednek. Ezekben az esetekben az alsóbb rétegbeli IoT Edge eszközöket közvetlen internetkapcsolat nélkül is konfigurálhatja. Az útválasztási IoT Hub az átjáró eszközön keresztüli kommunikáción túl az alsóbb rétegbeli IoT Edge-eszközök az átjáró eszközét használhatják az összes Felhőbeli kapcsolathoz.

Ez a hálózati konfiguráció megköveteli, hogy csak az átjáró-hierarchia legfelső rétegében lévő IoT Edge eszköz legyen közvetlen kapcsolat a felhővel. Az alsó rétegekben IoT Edge eszközök csak a fölérendelt eszközzel vagy bármely gyermek eszközzel tudnak kommunikálni. Az átjáró-eszközök speciális moduljai lehetővé teszik ezt a forgatókönyvet, beleértve a következőket:

* Az **API-proxy modult** minden olyan IoT Edge átjárón meg kell adni, amely alatt egy másik IoT Edge eszköz található. Ez azt jelenti, hogy az alsó réteg kivételével az átjáró-hierarchia *minden rétegén* kell lennie. Ez a modul egy [Nginx](https://nginx.org) fordított proxyt használ a http-adat hálózati rétegeken keresztüli továbbítására egyetlen porton keresztül. Kiválóan konfigurálható a modul Twin és a környezeti változók segítségével, így az átjáró-forgatókönyv követelményeinek megfelelően módosítható.

* A **Docker beállításjegyzék-modulját** az átjáró-hierarchia *felső rétegében* lévő IoT Edge átjárón lehet központilag telepíteni. Ez a modul felelős a tároló lemezképek lekéréséhez és gyorsítótárazásához az alsóbb rétegekben lévő összes IoT Edge eszköz nevében. A modul felső rétegen való üzembe helyezésének alternatívája egy helyi beállításjegyzék használata, vagy a tároló lemezképek manuális betöltése az eszközökre, és a modul lekérési házirendje **soha nem** lesz beállítva.

* A **IoT Edge Azure-Blob Storage** az átjáró-hierarchia *felső rétegében* lévő IoT Edge átjárón telepíthetők. Ez a modul felelős az alsóbb rétegekben lévő összes IoT Edge eszköz nevében lévő Blobok feltöltéséhez. A Blobok feltöltésének lehetősége is lehetővé teszi az alacsonyabb rétegekben lévő IoT Edge eszközök hasznos hibaelhárítási funkcióját, például a modul naplójának feltöltését és a támogatási csomagok feltöltését.

### <a name="network-configuration"></a>Hálózati konfiguráció

A legfelső rétegben lévő összes átjáró-eszközhöz a hálózati operátoroknak a következőket kell tenniük:

* Adjon meg statikus IP-címet vagy teljes tartománynevet (FQDN).
* Engedélyezze az IP-címről érkező kimenő kommunikációt az Azure IoT Hub-állomásnévre a 443 (HTTPS) és a 5671 (AMQP) porton keresztül.
* Engedélyezze a kimenő kommunikációt erről az IP-címről a Azure Container Registry állomásnévre az 443-as (HTTPS) porton keresztül.

  Az API-proxy modul egyszerre csak egy tároló-beállításjegyzékhez tud kapcsolatot kezelni. Javasoljuk, hogy minden tároló lemezképet, beleértve a Microsoft Container Registry (mcr.microsoft.com) által biztosított nyilvános rendszerképeket, amelyeket a privát tároló beállításjegyzékében tárol.

Az alsóbb rétegbeli átjáró-eszközök esetében a hálózati operátoroknak a következőket kell tenniük:

* Adjon meg egy statikus IP-címet.
* Engedélyezze az IP-címről érkező kimenő kommunikációt a szülő átjáró IP-címére a 443 (HTTPS) és a 5671 (AMQP) porton keresztül.

### <a name="deploy-modules-to-top-layer-devices"></a>Modulok üzembe helyezése a legfelső rétegbeli eszközökön

Az átjáró-hierarchia legfelső rétegében lévő IoT Edge eszköz olyan szükséges modulokkal rendelkezik, amelyeket az eszközön esetlegesen futtatott munkaterhelés-modulok mellett telepíteni kell rá.

Az API-proxy modul úgy lett kialakítva, hogy testre legyen szabva a leggyakoribb átjáró-forgatókönyvek kezeléséhez. Ebből a cikkből megtudhatja, hogyan állíthatja be a modulokat egy alapszintű konfigurációban. Részletesebb információkat és példákat a következő témakörben talál: [az API-proxy modul konfigurálása az átjáró-hierarchia forgatókönyvéhez](how-to-configure-api-proxy-module.md) .

1. Az [Azure Portalon](https://portal.azure.com) keresse meg az IoT hubot.
1. A navigációs menüből válassza a **IoT Edge** lehetőséget.
1. Válassza ki a legfelső rétegbeli eszközt, amelyet a **IoT Edge eszközök** listájáról konfigurál.
1. Válassza a **Set modules** (Modulok beállítása) lehetőséget.
1. A **IoT Edge-modulok** szakaszban válassza a **Hozzáadás** , majd a **piactér modul** elemet.
1. Keresse meg és válassza ki a **IOT Edge API-proxy** modult.
1. Válassza ki az API-proxy modul nevét a telepített modulok listájából, és frissítse a következő modul beállításait:
   1. A **környezeti változók** lapon frissítse a **NGINX_DEFAULT_PORT** értékét a következőre: `443` .
   1. A **tároló létrehozása beállítások** lapon frissítse a port kötéseit a 443-es hivatkozási portra.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   Ezek a módosítások az API-proxy modult úgy konfigurálja, hogy az 443-es porton figyeljen. Ha meg szeretné akadályozni a port kötésének ütközését, a edgeHub modult úgy kell konfigurálnia, hogy ne figyelje a 443-es portot. Ehelyett az API-proxy modul a 443-es porton továbbítja a edgeHub forgalmat.

1. Válassza ki a **futtatókörnyezet beállításait** , és keresse meg a edgeHub modul létrehozási beállításait. Törölje az 443-es porthoz tartozó port kötését, hagyja ki a 5671 és 8883-as portok kötéseit.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Kattintson a **Save (Mentés** ) gombra a futásidejű beállítások módosításainak mentéséhez.
1. Válassza a **Hozzáadás** újra, majd a **IoT Edge modul** lehetőséget.
1. Adja meg a következő értékeket a Docker beállításjegyzék-modul telepítéséhez a központi telepítéshez:
   1. **IoT Edge modul neve** : `registry`
   1. A **modul beállításai** lapon a **rendszerkép URI-ja** : `registry:latest`
   1. A **környezeti változók** lapon adja hozzá a következő környezeti változókat:

      * **Név** : `REGISTRY_PROXY_REMOTEURL` **érték** : annak a tároló-beállításjegyzéknek az URL-címe, amelyhez a beállításjegyzék-modult le szeretné képezni. Például: `https://myregistry.azurecr`.

        A beállításjegyzék-modul csak egy tároló-beállításjegyzékhez képezhető le, ezért azt javasoljuk, hogy az összes tároló lemezképe egyetlen privát tároló-beállításjegyzékben legyen.

      * **Name** : `REGISTRY_PROXY_USERNAME` **Value** : username a Container registryben való hitelesítéshez.

      * **Name** : `REGISTRY_PROXY_PASSWORD` **Value** : jelszó a tároló beállításjegyzékében való hitelesítéshez.

   1. A **tároló létrehozása beállítások** lapon illessze be a következőt:

      ```json
      {
          "HostConfig": {
              "PortBindings": {
                  "5000/tcp": [
                      {
                          "HostPort": "5000"
                      }
                  ]
              }
          }
      }
      ```

1. A **Hozzáadás** gombra kattintva adja hozzá a modult a központi telepítéshez.
1. Válassza a Next (tovább) lehetőséget **: útvonalakon** lépjen a következő lépésre.
1. Ha engedélyezni szeretné az eszközről a felhőbe irányuló üzeneteket az alsóbb rétegbeli eszközökről a IoT Hub elérésére, adjon meg egy útvonalat, amely az összes üzenetet átadja IoT Hubnak. Például:
    1. **Név** : `Route`
    1. **Érték** : `FROM /messages/* INTO $upstream`
1. Válassza a **felülvizsgálat + létrehozás** lehetőséget az utolsó lépéshez való ugráshoz.
1. Válassza a **Létrehozás** lehetőséget az eszközön való üzembe helyezéshez.

### <a name="deploy-modules-to-lower-layer-devices"></a>Modulok üzembe helyezése az alsóbb rétegbeli eszközökön

Az átjáró-hierarchiák alsó rétegeiben lévő eszközök IoT Edge egy szükséges modullal kell rendelkezniük, amelyet az eszközön futtatott számítási feladatok modulján kívül is telepíteni kell.

#### <a name="route-container-image-pulls"></a>Útvonal-tároló képe lekérése

Mielőtt megtárgyalja az átjáró-hierarchiákban IoT Edge eszközökhöz szükséges proxy modult, fontos tisztában lennie azzal, hogy az alacsonyabb rétegekben lévő IoT Edge-eszközök Hogyan kapják meg a modul lemezképeit.

Ha az alsóbb rétegbeli eszközök nem tudnak csatlakozni a felhőhöz, de a modul rendszerképeit a szokásos módon szeretné lekérni, akkor az átjáró-hierarchia legfelső rétegbeli eszközét úgy kell konfigurálni, hogy kezelni tudja ezeket a kéréseket. A felső rétegbeli eszköznek a tároló-beállításjegyzékhez leképezett Docker **beállításjegyzék** -modult kell futtatnia. Ezután konfigurálja az API-proxy modult a tárolóra vonatkozó kérelmek átirányításához. Ezeket az adatokat a cikk korábbi szakaszaiban tárgyaljuk. Ebben a konfigurációban az alsó rétegbeli eszközök nem mutatnak a Felhőbeli tároló-jegyzékekre, hanem a felső rétegben futó beállításjegyzékre.

A hívás helyett például az `mcr.microsoft.com/azureiotedge-api-proxy:latest` alsóbb rétegbeli eszközöknek kell meghívniuk `$upstream:443/azureiotedge-api-proxy:latest` .

A **$upstream** paraméter egy alsóbb rétegbeli eszköz szülőjének mutat, így a kérés az összes rétegen át fog haladni, amíg el nem éri a legfelső réteget, amely a beállításjegyzék-modulra irányuló proxy-környezet útválasztási kérelmeit átirányítja. Az `:443` ebben a példában szereplő portot le kell cserélni, amely a szülő eszközön lévő API-proxy modul által figyelt port.

Az API-proxy modul csak egyetlen beállításjegyzék-modulhoz irányítható, és mindegyik beállításjegyzék-modul csak egy tároló-beállításjegyzékhez képezhető le. Ezért minden olyan képet, amelyet az alsóbb rétegbeli eszközöknek le kell húznia, egyetlen tároló-beállításjegyzékben kell tárolni.

Ha nem szeretné, hogy az alsóbb rétegbeli eszközök lekéréses kérelmeket hozzanak egy átjáró-hierarchián keresztül, egy másik lehetőség a helyi beállításjegyzék-megoldás kezelése. Vagy leküldheti a modul lemezképeit az eszközökre az üzembe helyezések létrehozása előtt, majd a **imagePullPolicy** **nem** értékre állíthatja.

#### <a name="bootstrap-the-iot-edge-agent"></a>A IoT Edge-ügynök indítása

A IoT Edge ügynök az első futtatókörnyezet-összetevő, amely bármely IoT Edge eszközön elindul. Győződjön meg arról, hogy minden alsóbb rétegbeli IoT Edge eszköz elérheti a edgeAgent-modul rendszerképét az indításkor, majd hozzáférhet az üzembe helyezésekhez, és elindíthatja a modul többi rendszerképét is.

Ha a config. YAML fájlt egy IoT Edge eszközön nyitja meg a hitelesítési információk, tanúsítványok és a szülő állomásnév megadásához, akkor az edgeAgent-tároló rendszerképét is frissítenie kell.

Ha a legfelső szintű átjáró eszköze a tároló képkérelmének kezelésére van konfigurálva, cserélje le `mcr.microsoft.com` a nevet a szülő állomásnévre és az API-proxy figyelési portjára. A központi telepítési jegyzékfájlban `$upstream` billentyűparancsként is használható, de ehhez a edgeHub modulnak kell kezelnie az útválasztást, és ez a modul nem indult el ezen a ponton. Például:

```yml
agent:
  name: "edgeAgent"
  type: "docker"
  env: {}
  config:
    image: "{Parent FQDN or IP}:443/azureiotedge-agent:1.2.0-rc1"
    auth: {}
```

Ha helyi tároló-beállításjegyzéket használ, vagy manuálisan adja meg a tároló lemezképeit az eszközön, akkor a config. YAML fájlt ennek megfelelően frissítse.

#### <a name="configure-runtime-and-deploy-proxy-module"></a>Futtatókörnyezet konfigurálása és proxy modul üzembe helyezése

Az **API-proxy modul** szükséges a felhő és az alsóbb rétegbeli IoT Edge eszközök közötti kommunikáció útválasztásához. A hierarchia alsó rétegében lévő IoT Edge eszköz, amely nem tartalmaz alsóbb szintű IoT Edge-eszközöket, nincs szüksége erre a modulra.

Az API-proxy modul úgy lett kialakítva, hogy testre legyen szabva a leggyakoribb átjáró-forgatókönyvek kezeléséhez. Ez a cikk röviden ismerteti a modulok alapszintű konfigurációban való beállításának lépéseit. Részletesebb információkat és példákat a következő témakörben talál: [az API-proxy modul konfigurálása az átjáró-hierarchia forgatókönyvéhez](how-to-configure-api-proxy-module.md) .

1. Az [Azure Portalon](https://portal.azure.com) keresse meg az IoT hubot.
1. A navigációs menüből válassza a **IoT Edge** lehetőséget.
1. Válassza ki az alsó rétegbeli eszközt, amelyet a **IoT Edge eszközök** listájáról konfigurál.
1. Válassza a **Set modules** (Modulok beállítása) lehetőséget.
1. A **IoT Edge-modulok** szakaszban válassza a **Hozzáadás** , majd a **piactér modul** elemet.
1. Keresse meg és válassza ki a **IOT Edge API-proxy** modult.
1. Válassza ki az API-proxy modul nevét a telepített modulok listájából, és frissítse a következő modul beállításait:
   1. A **modul beállításai** lapon frissítse a **rendszerkép URI** értékét. Cserélje le a `mcr.microsoft.com` elemet a `$upstream:443` kérdésre.
   1. A **környezeti változók** lapon frissítse a **NGINX_DEFAULT_PORT** értékét a következőre: `443` .
   1. A **tároló létrehozása beállítások** lapon frissítse a port kötéseit a 443-es hivatkozási portra.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   Ezek a módosítások az API-proxy modult úgy konfigurálja, hogy az 443-es porton figyeljen. Ha meg szeretné akadályozni a port kötésének ütközését, a edgeHub modult úgy kell konfigurálnia, hogy ne figyelje a 443-es portot. Ehelyett az API-proxy modul a 443-es porton továbbítja a edgeHub forgalmat.

1. Válassza a **futtatókörnyezet beállításait**.
1. A edgeHub modul beállításainak frissítése:

   1. A **rendszerkép** mezőben cserélje le a- `mcr.microsoft.com` t `$upstream:443` .
   1. A **create Options (beállítások létrehozása** ) mezőben törölje az 443-es porthoz tartozó port kötését, hagyja ki a 5671 és 8883 port kötéseit.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. A edgeAgent modul beállításainak frissítése:
   1. A **rendszerkép** mezőben cserélje le a- `mcr.microsoft.com` t `$upstream:443` .

1. Kattintson a **Save (Mentés** ) gombra a futásidejű beállítások módosításainak mentéséhez.
1. Válassza a Next (tovább) lehetőséget **: útvonalakon** lépjen a következő lépésre.
1. Ha engedélyezni szeretné az eszközről a felhőbe irányuló üzeneteket az alsóbb rétegbeli eszközökről a IoT Hub elérésére, adjon meg egy útvonalat, amely az összes üzenetet átadja `$upstream` . A felsőbb rétegbeli paraméter a fölérendelt eszközre mutat alsó rétegű eszközök esetén. Például:
    1. **Név** : `Route`
    1. **Érték** : `FROM /messages/* INTO $upstream`
1. Válassza a **felülvizsgálat + létrehozás** lehetőséget az utolsó lépéshez való ugráshoz.
1. Válassza a **Létrehozás** lehetőséget az eszközön való üzembe helyezéshez.

## <a name="next-steps"></a>További lépések

[IoT Edge-eszköz használata átjáróként](iot-edge-as-gateway.md)

[Az API-proxy modul konfigurálása az átjáró-hierarchia forgatókönyvéhez](how-to-configure-api-proxy-module.md)