---
title: Hozzon létre egy átlátszó Azure IoT Edge - Windows |} Microsoft Docs
description: Azure IoT peremhálózati segítségével, amely képes több eszközökre vonatkozó információkat átlátszó átjáró létrehozása
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: edc44f0ab2d2cc737807dd8ad543997cdd75bd43
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036009"
---
# <a name="create-a-windows-iot-edge-device-that-acts-as-a-transparent-gateway"></a>Hozzon létre egy Windows IoT peremhálózati eszköz, amely átlátszó átjáróként

Ez a cikk átlátszó átjáróként IoT peremhálózati eszköz használatára vonatkozó részletes útmutatást. Ez a cikk a többi kifejezés *IoT peremhálózati átjáró* átlátszó átjáróként használt IoT peremhálózati eszköz hivatkozik. Részletes információkat lásd: [hogyan egy IoT-peremhálózati eszköz egy átjáró használható][lnk-edge-as-gateway], ami fogalmi áttekintése.

>[!NOTE]
>Jelenleg:
> * Az átjáró le van választva az IoT-központ, ha az eszközöket az átjáró nem hitelesíthető.
> * Az IoT-peremeszközök IoT peremhálózati átjáró nem tud kapcsolódni.
> * Eszközöket nem használható a fájl feltöltése.

A rögzített rész átlátszó átjáró létrehozásával kapcsolatos biztonságosan kapcsolódik az átjáró eszközöket. Az Azure IoT peremhálózati lehetővé teszi, hogy ezek az eszközök közötti biztonságos TLS-kapcsolatok használatát a nyilvános kulcsokra ÉPÜLŐ infrastruktúra. Ebben az esetben azt még lehetővé teszi egy alsóbb rétegbeli eszköz átlátszó átjáróként IoT peremhálózati eszköz csatlakozni.  Ésszerű biztonság fenntartásához, az alsóbb rétegbeli eszközt kell személyazonosságának a peremhálózati eszköz szeretné csak az eszközök, az átjárók és nem a potenciálisan rosszindulatú átjáró csatlakozik.

A tanúsítvány-infrastruktúra, amely lehetővé teszi a megbízhatósági kapcsolat szükséges az eszköz-átjáró topológiát hozhat létre. Ez a cikk azt feltételezzük, hogy az ugyanazon tanúsítványt telepítő engedélyezése szeretné használni [X.509 hitelesítésszolgáltató biztonsági] [ lnk-iothub-x509] az IoT-központ, amely magában foglalja az egy adott IoT-központ (IoT hub tulajdonosa hitelesítésszolgáltató társított X.509 Hitelesítésszolgáltatói tanúsítvány ), és a tanúsítványok, a peremhálózati eszköz a hitelesítésszolgáltató és hitelesítésszolgáltató aláírva.

![Átjáró beállítása][1]

Az átjáró peremhálózati eszköz tanúsítványát az alsóbb rétegbeli eszköz megadja a kapcsolat indítása során. Az alsóbb rétegbeli eszköz ellenőrzi, hogy ellenőrizze, hogy a peremhálózati eszköz Hitelesítésszolgáltatói tanúsítványt a tulajdonos Hitelesítésszolgáltatói tanúsítvány aláírásával. Ez a folyamat lehetővé teszi, hogy az alsóbb rétegbeli eszközt, hogy erősítse meg az átjáró egy megbízható forrásból származik.

A következő lépések végigvezetik a tanúsítványok létrehozását, valamint a megfelelő helyeken történő telepítésének folyamatán.

## <a name="prerequisites"></a>Előfeltételek
1.  [Telepítse az Azure IoT peremhálózati futásidejű] [ lnk-install-windows-x64] átlátszó átjáróként használni kívánt Windows-eszközön.

1. Windows OpenSSL beolvasása. Számos módon OpenSSL telepítése. Az utasítások ehhez vcpkg használja.
   1. Töltse le, és futtassa rendszergazdaként PowerShell az alábbi parancsokkal vcpkg telepítse. Nyissa meg egy könyvtárat, amelyben OpenSSL telepíteni szeretné, hogy lehetővé teszi a híváshoz `$VCPKGDIR`.

   ```PowerShell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg integrate install
   .\vcpkg install openssl:x64-windows
   ```

   1. Környezeti változó beállítása `OPENSSL_ROOT_DIR` való `$VCPKGDIR\vcpkg\packages\openssl_x64-windows` és is hozzáadhatók `$VCPKGDIR\vcpkg\packages\openssl_x64-windows\tools\openssl` számára a `PATH` környezeti változó.

1.  Szerezze be a parancsfájlok a következő paranccsal szükséges nem éles tanúsítványainak előállításához. Ezek a parancsfájlok segítségével hozhat létre a szükséges tanúsítványok átlátszó átjárót a beállítása.

   ```PowerShell
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

1. Keresse meg a használni kívánt könyvtárát. Az itt fog ez lesz az $WRKDIR.  Ez a könyvtár összes fájl létrejön.

   CD $WRKDIR

1. Konfigurációs és a parancsfájl fájlok másolása a munkakönyvtárat.
   ```PowerShell
   copy azure-iot-sdk-c\tools\CACertificates\*.cnf .
   copy azure-iot-sdk-c\tools\CACertificates\ca-certs.ps1 .
   ```

1. A parancsfájlok futtatásához a következő parancs futtatásával PowerShell engedélyezése
   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

1. A Funkciók, a parancsfájlok PowerShell globális névtérbe rögzítési a következő paranccsal által használt kapcsolása
   ```PowerShell
   . .\ca-certs.ps1
   ```

1. Ellenőrizze a OpenSSL megfelelően van telepítve, és győződjön meg arról, hogy nem lesznek meglévő tanúsítványok összeütközések neve a következő parancs futtatásával.
   ```PowerShell
   Test-CACertsPrerequisites
   ```

## <a name="certificate-creation"></a>Tanúsítvány létrehozása
1.  A tulajdonos Hitelesítésszolgáltatói tanúsítvány és egy közbenső tanúsítvány létrehozása. Ezek az összes kerülnek `$WRKDIR`.

   ```PowerShell
   New-CACertsCertChain rsa
   ```

   A kimenetének a parancsfájl végrehajtása a következő tanúsítványok és kulcsok:
   * Tanúsítványok
      * `$WRKDIR\certs\azure-iot-test-only.root.ca.cert.pem`
      * `$WRKDIR\certs\azure-iot-test-only.intermediate.cert.pem`
   * Kulcsok
      * `$WRKDIR\private\azure-iot-test-only.root.ca.key.pem`
      * `$WRKDIR\private\azure-iot-test-only.intermediate.key.pem`

1.  Az alábbi paranccsal hozhat létre a peremhálózati eszköz Hitelesítésszolgáltatói tanúsítvány és titkos kulcsot.

   >[!NOTE]
   > **NE** olyan nevet válasszon, amely megegyezik az átjáró DNS-állomásnevet. Ez ugyanis az ügyfél hitelesítő sikertelen lesz a tanúsítványokkal szemben.

      ```PowerShell
      New-CACertsEdgeDevice "<gateway device name>"
      ```

   A parancsfájl végrehajtása kimenetének a következő tanúsítvány és kulcs:
   * `$WRKDIR\certs\new-edge-device.*`
   * `$WRKDIR\private\new-edge-device.key.pem`

## <a name="certificate-chain-creation"></a>Tanúsítvány lánc létrehozása
Hozzon létre tanúsítványt a tulajdonos Hitelesítésszolgáltatói tanúsítvány, a köztes tanúsítvány és a peremhálózati eszköz hitelesítésszolgáltató tanúsítványát az alábbi paranccsal. Helyezés lánc fájl lehetővé teszi könnyedén telepíthető a peremhálózati eszköz, amely átlátszó átjáróként működik.

   ```PowerShell
   Write-CACertsCertificatesForEdgeDevice "<gateway device name>"
   ```

## <a name="installation-on-the-gateway"></a>Az átjáró telepítése
1.  Másolja a következő fájlokat a peremhálózati eszköz a bármelyik $WRKDIR, kifejezés, amely szerint $CERTDIR. Ha a tanúsítványok jön létre, a peremhálózati eszközön kihagyhatja ezt a lépést.

   * Eszköz CA-tanúsítvány –  `$WRKDIR\certs\new-edge-device-full-chain.cert.pem`
   * Eszköz CA titkos kulcs- `$WRKDIR\private\new-edge-device.key.pem`
   * CA - tulajdonos `$WRKDIR\certs\azure-iot-test-only.root.ca.cert.pem`

2.  Állítsa be a `certificate` a biztonsági démon konfigurációs fájlban yam elérési helyet a tanúsítvány és kulcs fájlok tulajdonságait.

```yaml
certificates:
  device_ca_cert: "$CERTDIR\certs\new-edge-device-full-chain.cert.pem"
  device_ca_pk: "$CERTDIR\private\new-edge-device.key.pem"
  trusted_ca_certs: "$CERTDIR\certs\azure-iot-test-only.root.ca.cert.pem"
```
## <a name="deploy-edgehub-to-the-gateway"></a>Az átjáró EdgeHub telepítése
Az Azure IoT Edge egyik legfontosabb képessége a modulok felhőből való üzembe helyezése az IoT Edge-eszközökön. Ez a szakasz rendelkezik, akkor látszólag üres központi telepítés; Biztonsági központ azonban bekerül az összes központi telepítésre, még akkor is, ha nincs nem található más modulok automatikus megállapítása. Biztonsági központ az a peremhálózati eszközön van szüksége az átjáróként működő átlátszó, egy üres központi telepítés létrehozása elegendő egyetlen modul. 
1. Az Azure Portalon keresse meg az IoT-központot.
2. Ugrás a **IoT peremhálózati** és az átjáró használni kívánt IoT peremhálózati eszköz kiválasztásához.
3. Válassza a **Modulok beállítása** lehetőséget.
4. Kattintson a **Tovább** gombra.
5. Az a **adja meg az útvonalakat** lépés, rendelkeznie kell egy alapértelmezett útvonalat, amely minden modul összes üzenetet küld az IoT-központ. Ha nem, adja hozzá a következő kódot, majd válasszon **következő**.
   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```
6. A felülvizsgálati sablon lépésben válassza ki a **Submit**.

## <a name="installation-on-the-downstream-device"></a>Az alsóbb rétegbeli eszköz telepítése
Alsóbb rétegbeli lehet bármely alkalmazás használata a [Azure IoT-eszközök SDK][lnk-devicesdk], például az egyszerű egy ismertetett [csatlakoztassa az eszközt az IoT hub .NET használatával] [ lnk-iothub-getstarted]. Egy alsóbb rétegbeli eszköz alkalmazásnak megbízzanak a **CA tulajdonosa** ellenőrizni fogja az átjáró eszközökre a TLS-kapcsolatokhoz tanúsítvány. Ezt a lépést általában két módon hajtható végre: az operációs rendszer szintjén, vagy (az egyes nyelvek) az alkalmazás szintjén.

### <a name="os-level"></a>Az operációs rendszer szint
Ez a tanúsítvány telepítése az operációs rendszer tanúsítványtárolójában lehetővé teszi minden alkalmazások legyen használva a tulajdonos Hitelesítésszolgáltatói tanúsítvány megbízható tanúsítvány.

* Ubuntu - itt található példa bemutatja, hogyan CA tanúsítvány telepítése Ubuntu-gazdagépen.

   ```cmd
   sudo cp $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem  /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```
 
    Megjelenik egy üzenet azt jelzi, "a tanúsítványok frissítése a /etc/ssl/certs... 1 hozzáadása, eltávolítása 0; rendelés."

* Windows - [ez](https://msdn.microsoft.com/en-us/library/cc750534.aspx) cikk részletezi ezzel a Windows eszköz a tanúsítvány importálása varászló használatával.

### <a name="application-level"></a>Alkalmazásszintű
A .NET-alkalmazásokban adja hozzá a következő kódrészletet a hitelesítés a PEM-formátumba. A változó inicializálására `certPath` rendelkező `$CERTDIR\certs\azure-iot-test-only.root.ca.cert.pem`.

   ```
   using System.Security.Cryptography.X509Certificates;

   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

## <a name="connect-the-downstream-device-to-the-gateway"></a>Az alsóbb rétegbeli eszköz csatlakozni az átjáróhoz
Az IoT Hub eszköz sdk inicializálnia kell egy olyan kapcsolati karakterlánccal az átjáróeszköz állomásnév hivatkozik. Hozzáfűzésével ebben az esetben a `GatewayHostName` tulajdonság az eszköz kapcsolati karakterlánc módosításait. Például Íme egy minta eszköz kapcsolati karakterláncot egy eszközhöz, azt hozzáfűzi a `GatewayHostName` tulajdonság:

   ```
   HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com
   ```

   >[!NOTE]
   >Ez egy minta parancs melyik tesztet, amelyet minden állítsa be megfelelően. Ön sohuld egy üzenetet megkapta a "OK ellenőrzése".
   >
   >openssl s_client-mygateway.contoso.com:8883 - CAfile $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem - showcerts csatlakozás

## <a name="routing-messages-from-downstream-devices"></a>Üzenetek az eszközöket
Az IoT-Edge futásidejű hasonlóan modulok által küldött üzenetek eszközöket küldött üzenetek irányíthatja. Ez lehetővé teszi az átjáró futó adatokról a felhőbe történő elküldése előtt modulban elemzés végrehajtásához. Az alábbi útvonal szeretne használni egy alsóbb rétegbeli eszközről neve üzeneteket küldeni `sensor` a modul nevére `ai_insights`.

   ```json
   { "routes":{ "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" } }
   ```

Tekintse meg a [modul összeállítás cikk] [ lnk-module-composition] üzenet útválasztási olvashat.

## <a name="next-steps"></a>További lépések
[Követelmények és eszközök IoT peremhálózati modulok adattárházzal][lnk-module-dev].

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
