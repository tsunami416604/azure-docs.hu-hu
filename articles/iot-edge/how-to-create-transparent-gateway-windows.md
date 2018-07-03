---
title: Transzparens átjáró létrehozása az Azure IoT Edge-ben – Windows |} A Microsoft Docs
description: Az Azure IoT Edge segítségével, amely képes feldolgozni az adatokat több eszközön is transzparens átjáró létrehozása
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 96ca5a7ec8b0c87984ea2c76af446d7a8b5504a1
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344300"
---
# <a name="create-a-windows-iot-edge-device-that-acts-as-a-transparent-gateway"></a>Hozzon létre egy Windows IoT Edge-eszköz, amely transzparens átjáróként

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
1.  [Telepítse az Azure IoT Edge-futtatókörnyezet] [ lnk-install-windows-x64] egy transzparens átjáróként használni kívánt Windows-eszközön.

1. Windows OpenSSL beolvasása. OpenSSL telepítése számos módja van:

   >[!NOTE]
   >Ha már telepítve van az eszközén Windows OpenSSL, akkor előfordulhat, hogy ezt a lépést kihagyhatja, de győződjön meg arról `openssl.exe` érhető el a `%PATH%` környezeti változót.

   * Töltse le és telepítse a [külső OpenSSL bináris](https://wiki.openssl.org/index.php/Binaries), például a [a projektet a SourceForge](https://sourceforge.net/projects/openssl/).
   
   * Töltse le a OpenSSL forráskód és a bináris fájlok felépítéséhez a számítógépen a saját magának vagy ehhez keresztül [vcpkg](https://github.com/Microsoft/vcpkg). Az alábbi utasításokat vcpkg használatával töltse le a forráskódot, fordítsa le és OpenSSL telepítse a Windows-gépen használata egyszerű lépésben.

      1. Navigáljon ahhoz a könyvtárhoz, ahol vcpkg telepíteni szeretné. Az itt azt fog hivatkozni, ez $VCPKGDIR szerint. Töltse le és telepítse az utasítások [vcpkg](https://github.com/Microsoft/vcpkg).
   
      1. A powershell parancssorba vcpkg telepítése után a következő parancsot az OpenSSL csomagot telepítse a Windows x64. Ez általában befejezéséhez körülbelül 5 percig tart.

         ```PowerShell
         .\vcpkg install openssl:x64-windows
         ```
      1. Adja hozzá `$VCPKGDIR\vcpkg\packages\openssl_x64-windows\tools\openssl` , a `PATH` környezeti változót, hogy a `openssl.exe` fájl elindításaihoz érhető el.

1. Lépjen abba a könyvtárba, amelyben a használni kívánt. Az itt azt fog hivatkozni, ez $WRKDIR szerint.  Minden fájl ebben a címtárban létrejön.
   
   CD $WRKDIR

1.  Szerezze be az alábbi parancsot a szükséges nem éles tanúsítványokat létrehozni a szkripteket. Ezek a parancsfájlok segítségével hozhat létre a szükséges tanúsítványok beállítása a transzparens átjáró.

      ```PowerShell
      git clone https://github.com/Azure/azure-iot-sdk-c.git
      ```

1. Másolja a konfigurációs és a parancsfájl a munkakönyvtárban. Ezenkívül az env változó OPENSSL_CONF openssl_root_ca.cnf konfigurációs fájllal történő beállítása.

   ```PowerShell
   copy azure-iot-sdk-c\tools\CACertificates\*.cnf .
   copy azure-iot-sdk-c\tools\CACertificates\ca-certs.ps1 .
   $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
   ```

1. A parancsfájlok futtatásához a következő parancs futtatásával PowerShell engedélyezése

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

1. A PowerShell a globális névtérhez a rögzítési művelet a következő paranccsal a parancsfájlok által használt funkciók használata
   
   ```PowerShell
   . .\ca-certs.ps1
   ```

1. Ellenőrizze a OpenSSL megfelelően telepítve van, és győződjön meg arról, hogy nem jár együtt név ütközések a meglévő tanúsítványok a következő parancs futtatásával. Ha probléma adódik, a parancsfájl hogyan hárítsa el ezeket a rendszer kell ismertetnie.

   ```PowerShell
   Test-CACertsPrerequisites
   ```

## <a name="certificate-creation"></a>Tanúsítvány létrehozása
1.  Hozzon létre a tulajdonos Hitelesítésszolgáltatói tanúsítvány és a egy köztes tanúsítványt. Ezek az összes kerüljenek a `$WRKDIR`.

      ```PowerShell
      New-CACertsCertChain rsa
      ```

1.  Hozzon létre az alábbi parancsot a peremhálózati eszköz Hitelesítésszolgáltatói tanúsítvány és titkos kulcsot.

   >[!NOTE]
   > **NE** egy nevet, amely megegyezik az átjáró DNS-állomásnevet használja. Ezzel az ügyfél elvégezzék a tanúsítását ezeket a tanúsítványokat a sikertelen művelettel.

   ```PowerShell
   New-CACertsEdgeDevice "<gateway device name>"
   ```

## <a name="certificate-chain-creation"></a>Tanúsítvány láncot létrehozása
A Hitelesítésszolgáltatói tanúsítvány tulajdonosa, a köztes tanúsítványt és a peremhálózati eszköz Hitelesítésszolgáltatói tanúsítvány az alábbi paranccsal hozzon létre egy tanúsítványláncra. Ennek elhelyezését egy lánc fájlban segítségével egyszerűen telepíthető az Edge-eszköz, amely transzparens átjáróként működik.

   ```PowerShell
   Write-CACertsCertificatesForEdgeDevice "<gateway device name>"
   ```

   A parancsfájl végrehajtása kimenete a következő tanúsítványok és a kulcs:
   * `$WRKDIR\certs\new-edge-device.*`
   * `$WRKDIR\private\new-edge-device.key.pem`
   * `$WRKDIR\certs\azure-iot-test-only.root.ca.cert.pem`

## <a name="installation-on-the-gateway"></a>Az átjáró telepítése
1.  Másolja a következő fájlokat $WRKDIR bárhol az Edge-eszközön, a kifejezés, amely szerint $CERTDIR. Ha a tanúsítványok hozza létre az Edge-eszközön hagyja ki ezt a lépést.

   * Eszköz CA-tanúsítvány –  `$WRKDIR\certs\new-edge-device-full-chain.cert.pem`
   * Eszköz CA titkos kulcs- `$WRKDIR\private\new-edge-device.key.pem`
   * CA - tulajdonos `$WRKDIR\certs\azure-iot-test-only.root.ca.cert.pem`

2.  Állítsa be a `certificate` a biztonsági démon konfigurációs fájlban yaml az elérési úthoz, amelyre helyezte a tanúsítvány és kulcs fájlok tulajdonságait.

```yaml
certificates:
  device_ca_cert: "$CERTDIR\\certs\\new-edge-device-full-chain.cert.pem"
  device_ca_pk: "$CERTDIR\\private\\new-edge-device.key.pem"
  trusted_ca_certs: "$CERTDIR\\certs\\azure-iot-test-only.root.ca.cert.pem"
```
## <a name="deploy-edgehub-to-the-gateway"></a>Az átjáró üzembe helyezése EdgeHub
Az Azure IoT Edge egyik legfontosabb képessége a modulok felhőből való üzembe helyezése az IoT Edge-eszközökön. Ez a szakasz rendelkezik, akkor látszólag üres üzembe helyezése; azonban az Edge Hub központi telepítések hozzáadni, akkor sem, ha nem található más modulok automatikus megállapítása. Edge Hub az a csak modul egy Edge-eszközön, hogy transzparens átjáró szerepét, így elegendő az üres telepítést hoz létre szüksége. 
1. Az Azure Portalon keresse meg az IoT-központot.
2. Lépjen a **IoT Edge** , és válassza ki az IoT Edge-eszközt, hogy az átjáró használni kívánt.
3. Válassza a **Modulok beállítása** lehetőséget.
4. Kattintson a **Tovább** gombra.
5. Az a **útvonalak megadása** lépés, rendelkeznie kell egy alapértelmezett útvonalat, amely az összes üzenetet küld az összes modulok az IoT hubnak. Ha nem, adja hozzá a következő kódot, majd válassza ki **tovább**.
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
A .NET-alkalmazásokban a következő kódrészletet egy PEM formátumú tanúsítvány megbízható is hozzáadhat. A változó inicializálása `certPath` a `$CERTDIR\certs\azure-iot-test-only.root.ca.cert.pem`.

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
[lnk-install-windows-x64]: ./how-to-install-iot-edge-windows-with-windows.md
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
