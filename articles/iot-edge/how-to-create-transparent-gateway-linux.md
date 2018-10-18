---
title: Transzparens átjáró létrehozása az Azure IoT Edge - Linux |} A Microsoft Docs
description: Az Azure IoT Edge segítségével, amely képes feldolgozni az adatokat több eszközön is transzparens átjáró létrehozása
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 079a22ebaa7abfec7e8db142bc8f277ff12ab77e
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394969"
---
# <a name="create-a-linux-iot-edge-device-that-acts-as-a-transparent-gateway"></a>Hozzon létre egy Linux IoT Edge-eszköz, amely transzparens átjáróként

Ez a cikk részletes utasításokat a transzparens átjáróként IoT Edge-eszköz használatával. Ez a cikk a többi kifejezés *IoT Edge-átjáró* IoT Edge-eszköz transzparens átjáróként használt hivatkozik. Részletesebb információkért lásd: [hogyan az IoT Edge-eszközt átjáróként használható](./iot-edge-as-gateway.md), révén fogalmi áttekintése.

>[!NOTE]
>Jelenleg:
> * Ha az átjáró nem csatlakozik az IoT hubról, alsóbb rétegbeli eszközök az átjáró nem tudja hitelesíteni.
> * Edge-kompatibilis eszközök IoT Edge-átjáró nem tud kapcsolódni. 
> * Alsóbb rétegbeli eszközök fájl feltöltése nem használható.

Transzparens átjáró létrehozása a rögzített rész biztonságos összekapcsolása az alsóbb rétegbeli eszközök átjárót. Az Azure IoT Edge lehetővé teszi, hogy ezek az eszközök közötti biztonságos TLS-kapcsolatok beállítása a PKI-infrastruktúra használatával. Ebben az esetben azt engedélyezi egy alsóbb rétegbeli eszközök transzparens átjáróként működő IoT Edge-eszköz csatlakozni.  Ésszerű biztonságának fenntartása érdekében az alsóbb rétegbeli eszközök ellenőrizze identitását a peremhálózati eszköz, mivel csak az eszközök csatlakoztatása az átjárók és a egy potenciálisan kártékony átjáró nincs.

Minden olyan tanúsítvány-infrastruktúra, amely lehetővé teszi a megbízhatósági kapcsolat szükséges az eszköz-átjáró topológiát hozhat létre. Ez a cikk feltételezzük, hogy az azonos tanúsítvány beállítása, hogy engedélyezni szeretné használni [x.509-es Hitelesítésszolgáltatói biztonsági](../iot-hub/iot-hub-x509ca-overview.md) az IoT Hub, amelyek egy adott IoT hub (az IoT hub tulajdonos CA), és a egy sorozat tanúsítványok társított X.509 Hitelesítésszolgáltatói tanúsítvány, a hitelesítésszolgáltató és hitelesítésszolgáltató aláírt az Edge-eszköz számára.

![Átjáró beállítása](./media/how-to-create-transparent-gateway/gateway-setup.png)

Az átjáró a peremhálózati eszköz Hitelesítésszolgáltatói tanúsítványát az alsóbb rétegbeli eszközre során a kapcsolat kezdeményezése mutat be. Az alsóbb rétegbeli eszköz ellenőrzi, hogy a peremhálózati eszköz Hitelesítésszolgáltatói tanúsítványt a tulajdonos Hitelesítésszolgáltatói tanúsítvány aláírásával. Ez a folyamat lehetővé teszi, hogy az alsóbb rétegbeli eszközök annak ellenőrzéséhez, hogy az átjáró egy megbízható forrásból származik.

A következő lépések végigvezetik a folyamat a tanúsítványok létrehozását, és telepíti őket a megfelelő helyeken.

## <a name="prerequisites"></a>Előfeltételek
1.  Telepítse az Azure IoT Edge-futtatókörnyezet a transzparens átjáróként használni kívánt Linux rendszerű eszközön.
   * [Linux x64](./how-to-install-iot-edge-linux.md)
   * [Linux ARM32](./how-to-install-iot-edge-linux-arm.md)

2.  Szerezze be az alábbi parancsot a szükséges nem éles tanúsítványokat létrehozni a szkripteket. Ezek a parancsfájlok segítségével hozhat létre a szükséges tanúsítványok beállítása a transzparens átjáró. 

   ```cmd
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

3. Ezek a szkriptek OpenSSL használatával is létrehozhat a szükséges tanúsítványokat, és OpenSSL néhány beállítás szükséges.
   
   1. Lépjen abba a könyvtárba, amelyben a használni kívánt. Az itt azt fog hivatkozni, ez $WRKDIR szerint.  Minden fájl ebben a címtárban létrejön.

      CD $WRKDIR
   
   1. Másolja a munkakönyvtárban config és a parancsfájl-fájlt.

      ```cmd
      cp azure-iot-sdk-c/tools/CACertificates/*.cnf .
      cp azure-iot-sdk-c/tools/CACertificates/certGen.sh .
      chmod 700 certGen.sh 
      ```

## <a name="certificate-creation"></a>Tanúsítvány létrehozása
1.  Hozzon létre a tulajdonos Hitelesítésszolgáltatói tanúsítvány és a egy köztes tanúsítványt. Ezek a tanúsítványok vannak elhelyezve `$WRKDIR`.

   ```cmd
   ./certGen.sh create_root_and_intermediate
   ```

   A kimenetek, a parancsfájl végrehajtása a következő tanúsítványok és kulcsok:
   * Tanúsítványok
      * `$WRKDIR/certs/azure-iot-test-only.root.ca.cert.pem`
      * `$WRKDIR/certs/azure-iot-test-only.intermediate.cert.pem`
   * Kulcsok
      * `$WRKDIR/private/azure-iot-test-only.root.ca.key.pem`
      * `$WRKDIR/private/azure-iot-test-only.intermediate.key.pem`

2.  Hozzon létre az alábbi parancsot a peremhálózati eszköz Hitelesítésszolgáltatói tanúsítvány és titkos kulcsot.

   >[!NOTE]
   > **NE** egy nevet, amely megegyezik az átjáró DNS-állomásnevet használja. Ezzel az ügyfél elvégezzék a tanúsítását ezeket a tanúsítványokat a sikertelen művelettel.

   ```cmd
   ./certGen.sh create_edge_device_certificate "<gateway device name>"
   ```

   A parancsfájl végrehajtása kimeneteire a következő tanúsítványok és a kulcs:
   * `$WRKDIR/certs/new-edge-device.*`
   * `$WRKDIR/private/new-edge-device.key.pem`

## <a name="certificate-chain-creation"></a>Tanúsítvány láncot létrehozása
A Hitelesítésszolgáltatói tanúsítvány tulajdonosa, a köztes tanúsítványt és a peremhálózati eszköz Hitelesítésszolgáltatói tanúsítvány az alábbi paranccsal hozzon létre egy tanúsítványláncra. Ennek elhelyezését egy lánc fájlban segítségével egyszerűen telepíthető az Edge-eszköz, amely transzparens átjáróként működik.

   ```cmd
   cat ./certs/new-edge-device.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/new-edge-device-full-chain.cert.pem
   ```

## <a name="installation-on-the-gateway"></a>Az átjáró telepítése
1.  Másolja a következő fájlokat $WRKDIR bárhol az Edge-eszközön, a kifejezés, amely szerint $CERTDIR. Ha a tanúsítványok hozza létre az Edge-eszközön hagyja ki ezt a lépést.

   * Eszköz CA-tanúsítvány –  `$WRKDIR/certs/new-edge-device-full-chain.cert.pem`
   * Eszköz CA titkos kulcs- `$WRKDIR/private/new-edge-device.key.pem`
   * CA - tulajdonos `$WRKDIR/certs/azure-iot-test-only.root.ca.cert.pem`
   
2. Nyissa meg az IoT Edge konfigurációs fájlját. Ez egy védett fájl, ezért lehet, hogy megemelt jogosultsági szintre van szükség az eléréséhez.
   
   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

3.  Állítsa be a `certificate` az Iot Edge-démon pluginconfig.JSON fájlban yaml az elérési út, amelyre helyezte a tanúsítvány és kulcs fájlok tulajdonságait.

   ```yaml
   certificates:
     device_ca_cert: "$CERTDIR/certs/new-edge-device-full-chain.cert.pem"
     device_ca_pk: "$CERTDIR/private/new-edge-device.key.pem"
     trusted_ca_certs: "$CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem"
   ```

## <a name="deploy-edgehub-to-the-gateway"></a>Az átjáró üzembe helyezése EdgeHub
Az Azure IoT Edge egyik legfontosabb képessége a modulok felhőből való üzembe helyezése az IoT Edge-eszközökön. Ez a szakasz rendelkezik, akkor látszólag üres üzembe helyezése; azonban Edge hubot automatikusan hozzáadódik központi telepítések akkor sem, ha nem található más modulok. Edge Hub az a csak modul egy Edge-eszközön, hogy transzparens átjáró szerepét, így elegendő az üres telepítést hoz létre szüksége. 
1. Az Azure Portalon keresse meg az IoT-központot.
2. Lépjen a **IoT Edge** , és válassza ki az IoT Edge-eszközt, hogy az átjáró használni kívánt.
3. Válassza a **Modulok beállítása** lehetőséget.
4. Kattintson a **Tovább** gombra.
5. Az **Útvonalak megadása** lépésben egy alapértelmezett útvonallal kell rendelkeznie, amely az összes modul összes üzenetét az IoT Hubba irányítja. Amennyiben nem így lenne, adja hozzá a következő kódot, és kattintson a **Tovább** gombra.
   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```
6. Tekintse át a sablon lépésben válassza ki a **küldés**.

## <a name="installation-on-the-downstream-device"></a>Az alsóbb rétegbeli eszközök telepítése
Alsóbb rétegbeli eszközök bármilyen alkalmazások lehetnek használatával a [Azure IoT eszközoldali SDK-t](../iot-hub/iot-hub-devguide-sdks.md), például egy egyszerű ismertetett [az eszköz csatlakoztatása az IoT hubhoz .NET használatával](../iot-hub/quickstart-send-telemetry-dotnet.md). Egy alsóbb rétegbeli eszközök alkalmazás rendelkezik megbízzon a **tulajdonosa hitelesítésszolgáltató** tanúsítványt annak érdekében, hogy az átjáró-eszközökre a TLS kapcsolatok ellenőrzése. Ezt a lépést általában két módon hajtható végre: az operációs rendszer szintjén, vagy (az egyes nyelvekhez) az alkalmazás szintjén.

### <a name="os-level"></a>Operációs rendszer szintjén
Ez a tanúsítvány telepítése az operációs rendszer tanúsítványtárolójában lehetővé teszi minden alkalmazás használatához a tulajdonos Hitelesítésszolgáltatói tanúsítvány, egy megbízható tanúsítványt.

* Ubuntu - Íme egy példa a CA tanúsítvány telepítése egy Ubuntu-gazdagép.

   ```cmd
   sudo cp $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem  /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```
 
    Egy üzenet arról tájékoztatja, "a tanúsítványok frissítése az /etc/ssl/certs... 1 hozzáadása, eltávolítása 0; kész."

* Windows - Íme egy példa egy hitelesítésszolgáltató-tanúsítvány telepítése egy Windows-gazdagépen.
  1. A start menüben írja be "Manage számítógép-tanúsítványok". Ez egy nevű segédprogramot kell vizualizációjára `certlm`.
  2. Navigáljon a **tanúsítványok helyi számítógép** > **megbízható legfelső szintű tanúsítványok** > **tanúsítványok** > kattintson a jobb gombbal > **Feladatok** > **importálása** a Tanúsítványimportáló varázsló elindításához.
  3. Kövesse a lépéseket megfelelően, és importálja a tanúsítványt fájl $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem.
  4. Amikor elkészült, egy "Importálása sikeresen befejeződött" üzenetnek kell megjelennie.

### <a name="application-level"></a>Alkalmazás-szint
A .NET-alkalmazásokban a következő kódrészletet egy PEM formátumú tanúsítvány megbízható is hozzáadhat. A változó inicializálása `certPath` a `$CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem`.

   ```
   using System.Security.Cryptography.X509Certificates;

   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

## <a name="connect-the-downstream-device-to-the-gateway"></a>Az alsóbb rétegbeli eszköz csatlakoztatása az átjáró
Az IoT Hub eszközoldali SDK inicializálásához az átjáróeszköz állomásnevét hivatkozó egy olyan kapcsolati karakterlánccal. Ez történik, amelyeket a `GatewayHostName` tulajdonságot az eszköz kapcsolati karakterláncát. Például itt látható egy minta eszköz kapcsolati karakterláncát egy eszközhöz, hogy hozzáfűzi a `GatewayHostName` tulajdonság:

   ```
   HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com
   ```

   >[!NOTE]
   >Ez egy mintául szolgáló parancs melyik tesztek mindent, állítsa be megfelelően a. Ön sohuld "rendben ellenőrzése" üzenetnek.
   >
   >openssl s_client-mygateway.contoso.com:8883 - CAfile $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem - showcerts csatlakoztatása

## <a name="routing-messages-from-downstream-devices"></a>Útválasztási üzenetek alsóbb rétegbeli eszközök
Az IoT Edge-futtatókörnyezet továbbíthatnak hasonlóan modulok által küldött üzenetek alsóbb rétegbeli eszközök által küldött üzeneteket. Ez lehetővé teszi az átjáró futó, mielőtt bármilyen adatot küld a felhő modulban elemzés végrehajtásához. Az alábbi útvonal használni kívánt üzenetek küldéséhez egy alsóbb rétegbeli eszközről nevű `sensor` egy modul nevét, `ai_insights`.

   ```json
   { "routes":{ "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" } }
   ```

Tekintse meg a [modul összeállítás cikk](./module-composition.md) üzenet-útválasztással kapcsolatos részletekért.

[!INCLUDE [iot-edge-offline-preview](../../includes/iot-edge-extended-offline-preview.md)]

## <a name="next-steps"></a>További lépések
[A követelmények és az eszközök IoT Edge-modulok megismerése](module-development.md).
