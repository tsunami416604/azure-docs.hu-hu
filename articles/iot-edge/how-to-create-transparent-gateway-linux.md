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
ms.openlocfilehash: 789958874784f19169e79a177beb0d811d9f60d4
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005706"
---
# <a name="create-a-linux-iot-edge-device-that-acts-as-a-transparent-gateway"></a>Hozzon létre egy Linux IoT Edge-eszköz, amely transzparens átjáróként

Ez a cikk részletes utasításokat a transzparens átjáróként IoT Edge-eszköz használatával. Ez a cikk a többi kifejezés *IoT Edge-átjáró* IoT Edge-eszköz transzparens átjáróként használt hivatkozik. Részletesebb információkért lásd: [hogyan az IoT Edge-eszközt átjáróként használható][lnk-edge-as-gateway], révén fogalmi áttekintése.

>[!NOTE]
>Jelenleg:
> * Ha az átjáró nem csatlakozik az IoT hubról, alsóbb rétegbeli eszközök az átjáró nem tudja hitelesíteni.
> * IoT Edge-eszközökön nem lehet csatlakozni az IoT Edge-átjáró.
> * Alsóbb rétegbeli eszközök nem használható a fájl feltöltése.

Transzparens átjáró létrehozása a rögzített rész biztonságos összekapcsolása az alsóbb rétegbeli eszközök átjárót. Az Azure IoT Edge lehetővé teszi, hogy ezek az eszközök közötti biztonságos TLS-kapcsolatok beállítása a PKI-infrastruktúra használatával. Ebben az esetben azt engedélyezi egy alsóbb rétegbeli eszközök transzparens átjáróként működő IoT Edge-eszköz csatlakozni.  Ésszerű biztonságának fenntartása érdekében az alsóbb rétegbeli eszközök ellenőrizze identitását a peremhálózati eszköz, mivel csak az eszközök csatlakoztatása az átjárók és a egy potenciálisan kártékony átjáró nincs.

Minden olyan tanúsítvány-infrastruktúra, amely lehetővé teszi a megbízhatósági kapcsolat szükséges az eszköz-átjáró topológiát hozhat létre. Ez a cikk feltételezzük, hogy az azonos tanúsítvány beállítása, hogy engedélyezni szeretné használni [x.509-es Hitelesítésszolgáltatói biztonsági] [ lnk-iothub-x509] az IoT Hub, amely magában foglalja egy adott IoT hub (az IoT hub tulajdonosa hitelesítésszolgáltató társított X.509 Hitelesítésszolgáltatói tanúsítvány ), és a egy sorozat tanúsítványok, a hitelesítésszolgáltató és a egy hitelesítésszolgáltató aláírt az Edge-eszköz.

![Átjáró beállítása][1]

Az átjáró a peremhálózati eszköz Hitelesítésszolgáltatói tanúsítványát az alsóbb rétegbeli eszközre során a kapcsolat kezdeményezése mutat be. Az alsóbb rétegbeli eszköz ellenőrzi, hogy a peremhálózati eszköz Hitelesítésszolgáltatói tanúsítványt a tulajdonos Hitelesítésszolgáltatói tanúsítvány aláírásával. Ez a folyamat lehetővé teszi, hogy az alsóbb rétegbeli eszközök annak ellenőrzéséhez, hogy az átjáró egy megbízható forrásból származik.

A következő lépések végigvezetik a folyamat a tanúsítványok létrehozását, és telepíti őket a megfelelő helyeken.

## <a name="prerequisites"></a>Előfeltételek
1.  Telepítse az Azure IoT Edge-futtatókörnyezet a transzparens átjáróként használni kívánt Linux rendszerű eszközön.
   * [Linux x64][lnk-install-linux-x64]
   * [Linux ARM32][lnk-install-linux-arm]

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
1.  Hozzon létre a tulajdonos Hitelesítésszolgáltatói tanúsítvány és a egy köztes tanúsítványt. Ezek az összes kerüljenek a `$WRKDIR`.

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

2.  Állítsa be a `certificate` a biztonsági démon konfigurációs fájlban yaml az elérési úthoz, amelyre helyezte a tanúsítvány és kulcs fájlok tulajdonságait.

```yaml
certificates:
  device_ca_cert: "$CERTDIR/certs/new-edge-device-full-chain.cert.pem"
  device_ca_pk: "$CERTDIR/private/new-edge-device.key.pem"
  trusted_ca_certs: "$CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem"
```

## <a name="deploy-edgehub-to-the-gateway"></a>Az átjáró üzembe helyezése EdgeHub
Az Azure IoT Edge egyik legfontosabb képessége a modulok felhőből való üzembe helyezése az IoT Edge-eszközökön. Ez a szakasz rendelkezik, akkor látszólag üres üzembe helyezése; azonban az Edge Hub központi telepítések hozzáadni, akkor sem, ha nem található más modulok automatikus megállapítása. Edge Hub az a csak modul egy Edge-eszközön, hogy transzparens átjáró szerepét, így elegendő az üres telepítést hoz létre szüksége. 
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
Alsóbb rétegbeli eszközök bármilyen alkalmazások lehetnek használatával a [Azure IoT eszközoldali SDK-t][lnk-devicesdk], például egy egyszerű ismertetett [az eszköz csatlakoztatása az IoT hubhoz .NET használatával] [ lnk-iothub-getstarted]. Egy alsóbb rétegbeli eszközök alkalmazás rendelkezik megbízzon a **tulajdonosa hitelesítésszolgáltató** tanúsítványt annak érdekében, hogy az átjáró-eszközökre a TLS kapcsolatok ellenőrzése. Ezt a lépést általában két módon hajtható végre: az operációs rendszer szintjén, vagy (az egyes nyelvekhez) az alkalmazás szintjén.

### <a name="os-level"></a>Operációs rendszer szintjén
Ez a tanúsítvány telepítése az operációs rendszer tanúsítványtárolójában lehetővé teszi minden alkalmazás használatához a tulajdonos Hitelesítésszolgáltatói tanúsítvány, egy megbízható tanúsítványt.

* Ubuntu - Íme egy példa a CA tanúsítvány telepítése egy Ubuntu-gazdagép.

   ```cmd
   sudo cp $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem  /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```
 
    Egy üzenet arról tájékoztatja, "a tanúsítványok frissítése az /etc/ssl/certs... 1 hozzáadása, eltávolítása 0; kész."

* Windows - Íme egy példa a CA tanúsítvány telepítése egy Windows-gazdagép.
  * A start menüben írja be "Manage számítógép-tanúsítványok". Ez egy nevű segédprogramot kell vizualizációjára `certlm`.
  * Navigáljon a tanúsítványokat a helyi számítógép megbízható legfelső szintű tanúsítványok-->--> tanúsítványok jobb--> kattintson a feladatok-->--> Importálás a Tanúsítványimportáló varázsló elindításához.
  * Kövesse a lépéseket megfelelően, és importálja a tanúsítványt fájl $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem.
  * Amikor elkészült, egy "Importálása sikeresen befejeződött" üzenetnek kell megjelennie.

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
Az IoT Hub eszközoldali sdk inicializálnia kell az átjáró eszköz állomásnevét hivatkozó egy olyan kapcsolati karakterlánccal. Ez történik, amelyeket a `GatewayHostName` tulajdonságot az eszköz kapcsolati karakterláncát. Például itt látható egy minta eszköz kapcsolati karakterláncát egy eszközhöz, hogy hozzáfűzi a `GatewayHostName` tulajdonság:

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

Tekintse meg a [modul összeállítás cikk] [ lnk-module-composition] üzenet-útválasztással kapcsolatos részletekért.

## <a name="next-steps"></a>További lépések
[A követelmények és az eszközök IoT Edge-modulok megismerése][lnk-module-dev].

<!-- Images -->
[1]: ./media/how-to-create-transparent-gateway/gateway-setup.png

<!-- Links -->
[lnk-install-linux-x64]: ./how-to-install-iot-edge-linux.md
[lnk-install-linux-arm]: ./how-to-install-iot-edge-linux-arm.md
[lnk-module-composition]: ./module-composition.md
[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-edge-as-gateway]: ./iot-edge-as-gateway.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus
