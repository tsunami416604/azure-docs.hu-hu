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
ms.openlocfilehash: f584e2cdcb038c6f8e9fcdbeecc22fb957bd7f8d
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394814"
---
# <a name="create-a-windows-iot-edge-device-that-acts-as-a-transparent-gateway"></a>Hozzon létre egy Windows IoT Edge-eszköz, amely transzparens átjáróként

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
1. [Telepítse az Azure IoT Edge-futtatókörnyezet](./how-to-install-iot-edge-windows-with-windows.md) egy transzparens átjáróként használni kívánt Windows-eszközön.

1. Windows OpenSSL beolvasása. OpenSSL telepítése számos módja van:

   >[!NOTE]
   >Ha már telepítve van az eszközén Windows OpenSSL, akkor előfordulhat, hogy ezt a lépést kihagyhatja, de győződjön meg arról `openssl.exe` érhető el a `%PATH%` környezeti változót.

   * Töltse le és telepítse a [külső OpenSSL bináris](https://wiki.openssl.org/index.php/Binaries), például a [a projektet a SourceForge](https://sourceforge.net/projects/openssl/).
   
   * Töltse le az OpenSSL-forráskódot, és a bináris fájlok felépítéséhez a számítógépen, saját magának vagy keresztül [vcpkg](https://github.com/Microsoft/vcpkg). Az alábbi utasításokat vcpkg használatával töltse le a forráskódot, fordítási és OpenSSL telepítése a Windows-gépen egyszerű lépésben.

      1. Navigáljon ahhoz a könyvtárhoz, ahol vcpkg telepíteni szeretné. Az itt azt fog hivatkozni, ez $VCPKGDIR szerint. Töltse le és telepítse az utasítások [vcpkg](https://github.com/Microsoft/vcpkg).
   
      1. A powershell parancssorba vcpkg telepítése után a következő parancsot az OpenSSL csomagot telepítse a Windows x64. A telepítés általában befejezéséhez körülbelül 5 percet vesz igénybe.

         ```PowerShell
         .\vcpkg install openssl:x64-windows
         ```
      1. Adja hozzá `$VCPKGDIR\installed\x64-windows\tools\openssl` , a `PATH` környezeti változót, hogy a `openssl.exe` fájl elindításaihoz érhető el.

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

1. Ellenőrizze a OpenSSL megfelelően telepítve van, és győződjön meg arról, hogy nem jár együtt név ütközések a meglévő tanúsítványok a következő parancs futtatásával. Ha probléma adódik, a parancsfájl kell azt ismertetik, hogyan javíthatók a rendszeren.

   ```PowerShell
   Test-CACertsPrerequisites
   ```

## <a name="certificate-creation"></a>Tanúsítvány létrehozása
1. Hozzon létre a tulajdonos Hitelesítésszolgáltatói tanúsítvány és a egy köztes tanúsítványt. A tanúsítványok összes kerüljenek a `$WRKDIR`.

      ```PowerShell
      New-CACertsCertChain rsa
      ```

1. Hozzon létre az alábbi parancsot a peremhálózati eszköz Hitelesítésszolgáltatói tanúsítvány és titkos kulcsot.

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

   A szkript a következő tanúsítványok és a kulcsot hoz létre:
   * `$WRKDIR\certs\new-edge-device.*`
   * `$WRKDIR\private\new-edge-device.key.pem`
   * `$WRKDIR\certs\azure-iot-test-only.root.ca.cert.pem`

## <a name="installation-on-the-gateway"></a>Az átjáró telepítése
1. Másolja a következő fájlokat $WRKDIR bárhol az Edge-eszközön, a kifejezés, amely szerint $CERTDIR. Ha a tanúsítványokat az Edge-eszközön jön létre, kihagyhatja ezt a lépést.

   * Eszköz CA-tanúsítvány –  `$WRKDIR\certs\new-edge-device-full-chain.cert.pem`
   * Eszköz CA titkos kulcs- `$WRKDIR\private\new-edge-device.key.pem`
   * CA - tulajdonos `$WRKDIR\certs\azure-iot-test-only.root.ca.cert.pem`

2. Állítsa be a `certificate` a biztonsági démon konfigurációs fájlban yaml az elérési úthoz, amelyre helyezte a tanúsítvány és kulcs fájlok tulajdonságait.

```yaml
certificates:
  device_ca_cert: "$CERTDIR\\certs\\new-edge-device-full-chain.cert.pem"
  device_ca_pk: "$CERTDIR\\private\\new-edge-device.key.pem"
  trusted_ca_certs: "$CERTDIR\\certs\\azure-iot-test-only.root.ca.cert.pem"
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
Az IoT Hub eszközoldali sdk inicializálásához az átjáróeszköz állomásnevét hivatkozó egy olyan kapcsolati karakterlánccal. Ez történik, amelyeket a `GatewayHostName` tulajdonságot az eszköz kapcsolati karakterláncát. Például itt látható egy minta eszköz kapcsolati karakterláncát egy eszközhöz, hogy hozzáfűzi a `GatewayHostName` tulajdonság:

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

Üzenet-útválasztással kapcsolatos további információkért lásd: [modul-összeállítását](./module-composition.md).

[!INCLUDE [iot-edge-extended-ofline-preview](../../includes/iot-edge-extended-offline-preview.md)]

## <a name="next-steps"></a>További lépések
[A követelmények és az eszközök IoT Edge-modulok megismerése](module-development.md).
