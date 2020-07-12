---
title: Eszközök tanúsítványainak kezelése – Azure IoT Edge | Microsoft Docs
description: Hozzon létre tesztelési tanúsítványokat, telepítsen és kezelje őket egy Azure IoT Edge eszközön az éles üzembe helyezés előkészítéséhez.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4c49345f7036dfee7d1f37c15a4647202b3e5670
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86257830"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>Tanúsítványok kezelése egy IoT Edge eszközön

Minden IoT Edge eszköz tanúsítványokat használ a futtatókörnyezet és az eszközön futó modulok közötti biztonságos kapcsolatok létrehozásához. IoT Edge az átjáróként működő eszközök ugyanezeket a tanúsítványokat használják az alsóbb rétegbeli eszközökhöz való kapcsolódáshoz.

## <a name="install-production-certificates"></a>Éles tanúsítványok telepítése

Amikor először telepíti IoT Edge és kiépíti az eszközt, az eszköz ideiglenes tanúsítvánnyal lesz beállítva, hogy tesztelni tudja a szolgáltatást.
Ezek az ideiglenes tanúsítványok 90 nap múlva lejárnak, vagy visszaállíthatók a gép újraindításával.
Ha éles környezetbe helyezi az eszközt, vagy egy átjárót szeretne létrehozni, meg kell adnia a saját tanúsítványait.
Ez a cikk bemutatja a tanúsítványok IoT Edge-eszközökön való telepítésének lépéseit.

Ha többet szeretne megtudni a tanúsítványok különböző típusairól és szerepköreiről, tekintse meg a [Azure IoT Edge tanúsítványok használatának](iot-edge-certs.md)ismertetése című témakört.

>[!NOTE]
>Az ebben a cikkben használt "legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ" kifejezés a IoT-megoldáshoz tartozó tanúsítványlánc legfelső szintű nyilvános tanúsítványára vonatkozik. Nem kell használnia egy konzorciális hitelesítésszolgáltató tanúsítványának gyökerét vagy a szervezet hitelesítésszolgáltatójának gyökerét. Sok esetben valójában egy közbenső HITELESÍTÉSSZOLGÁLTATÓ nyilvános tanúsítványa.

### <a name="prerequisites"></a>Előfeltételek

* IoT Edge-eszköz, amely [Windows](how-to-install-iot-edge-windows.md) vagy [Linux](how-to-install-iot-edge-linux.md)rendszeren fut.
* Rendelkeznie kell egy legfelső szintű hitelesítésszolgáltató (CA) tanúsítvánnyal, amely önaláírt vagy megbízható kereskedelmi hitelesítésszolgáltatótól vásárolt, például Baltimore, VeriSign, DigiCert vagy GlobalSign.

Ha még nem rendelkezik legfelső szintű hitelesítésszolgáltatóval, de szeretné kipróbálni IoT Edge olyan szolgáltatásokat, amelyek éles tanúsítványokat igényelnek (például átjárói forgatókönyvek), létrehozhat [bemutató-tanúsítványokat a IoT Edge eszköz funkcióinak teszteléséhez](how-to-create-test-certificates.md).

### <a name="create-production-certificates"></a>Éles tanúsítványok létrehozása

A következő fájlok létrehozásához használja a saját hitelesítésszolgáltatóját:

* Legfelső szintű hitelesítésszolgáltató
* Eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa
* Eszköz HITELESÍTÉSSZOLGÁLTATÓI titkos kulcsa

Ebben a cikkben a *legfelső szintű hitelesítésszolgáltatót* nevezzük, nem pedig a szervezet legfelső szintű hitelesítésszolgáltatója. Ez a legfelső szintű hitelesítésszolgáltató a IoT Edge-forgatókönyvhöz, amelyet az IoT Edge hub modul, a felhasználói modulok és az alsóbb rétegbeli eszközök az egymás közötti megbízhatósági kapcsolat létrehozására használnak.

> [!NOTE]
> Jelenleg a libiothsm korlátozásai meggátolják a 2050 január 1-jén vagy azt követően lejáró tanúsítványok használatát.

Ha szeretné megtekinteni a tanúsítványok példáját, tekintse át a bemutató tanúsítványokat létrehozó parancsfájlokat a [teszt hitelesítésszolgáltatói tanúsítványok kezelése a mintákhoz és az oktatóanyagokhoz](https://github.com/Azure/iotedge/tree/master/tools/CACertificates)című témakörben.

### <a name="install-certificates-on-the-device"></a>Tanúsítványok telepítése az eszközön

Telepítse a tanúsítványláncot a IoT Edge eszközre, és konfigurálja a IoT Edge futtatókörnyezetet az új tanúsítványokra való hivatkozáshoz.

Ha például a minta parancsfájlokat használta a [bemutató tanúsítványok létrehozásához](how-to-create-test-certificates.md), másolja át az alábbi fájlokat a IoT-peremhálózati eszközre:

* Eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa:`<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Eszköz HITELESÍTÉSSZOLGÁLTATÓjának titkos kulcsa:`<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ:`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. Másolja a három tanúsítvány-és kulcsfájl-fájlt a IoT Edge eszközre.

   Használhat olyan szolgáltatásokat, mint például a [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) vagy a [biztonságos másolási protokollt](https://www.ssh.com/ssh/scp/) használó függvények a tanúsítványfájl áthelyezéséhez.  Ha saját maga hozta létre a tanúsítványokat a IoT Edge eszközön, kihagyhatja ezt a lépést, és használhatja a munkakönyvtár elérési útját.

1. Nyissa meg a IoT Edge biztonsági démon konfigurációs fájlját.

   * Windows`C:\ProgramData\iotedge\config.yaml`
   * Linux`/etc/iotedge/config.yaml`

1. Állítsa be a **tanúsítvány** tulajdonságait a config. YAML értékre a IoT Edge eszköz tanúsítvány-és kulcsfájl-FÁJLjának URI-elérési útjára. Távolítsa el a `#` karaktert, mielőtt a tanúsítvány tulajdonságai megszüntessék a négy sort. Győződjön meg arról, hogy a (z) **:** sor nem rendelkezik korábbi szóközökkel, és hogy a beágyazott elemek két szóközzel vannak behúzva. Például:

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "file:///C:/<path>/<device CA cert>"
        device_ca_pk: "file:///C:/<path>/<device CA key>"
        trusted_ca_certs: "file:///C:/<path>/<root CA cert>"
      ```

   * Linux:

      ```yaml
      certificates:
        device_ca_cert: "file:///<path>/<device CA cert>"
        device_ca_pk: "file:///<path>/<device CA key>"
        trusted_ca_certs: "file:///<path>/<root CA cert>"
      ```

1. Linux-eszközökön ellenőrizze, hogy a felhasználó **iotedge** rendelkezik-e olvasási engedéllyel a tanúsítványokat tároló címtárhoz.

1. Ha korábban más tanúsítványokat használt IoT Edgehoz az eszközön, a IoT Edge elindítása vagy újraindítása előtt törölje a fájlokat a következő két könyvtárban:

   * Windows: `C:\ProgramData\iotedge\hsm\certs` és`C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux: `/var/lib/iotedge/hsm/certs` és`/var/lib/iotedge/hsm/cert_keys`

## <a name="customize-certificate-lifetime"></a>A tanúsítvány élettartamának testreszabása

IoT Edge több esetben automatikusan hoz létre tanúsítványokat az eszközön, beleértve a következőket:

* Ha nem ad meg saját éles tanúsítványokat a IoT Edge telepítésekor és kiépítésekor, akkor a IoT Edge Security Manager automatikusan létrehoz egy **eszköz hitelesítésszolgáltatói tanúsítványát**. Ez az önaláírt tanúsítvány kizárólag fejlesztési és tesztelési célokra szolgál, nem éles környezetben. Ez a tanúsítvány 90 nap után lejár.
* A IoT Edge Security Manager az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa által aláírt **MUNKATERHELÉS hitelesítésszolgáltatói tanúsítványt** is létrehoz.

További információ a IoT Edge eszköz különböző tanúsítványainak funkciójával kapcsolatban: a [Azure IoT Edge tanúsítványok használatának ismertetése](iot-edge-certs.md).

A két automatikusan generált tanúsítvány esetében beállíthatja a **auto_generated_ca_lifetime_days** jelzőt a config. yamlban a tanúsítványok élettartamára vonatkozó napok számának beállításához.

>[!NOTE]
>Létezik egy harmadik automatikusan generált tanúsítvány, amelyet a IoT Edge Security Manager hoz létre, a **IoT Edge hub-kiszolgáló tanúsítványát**. A tanúsítványnak mindig 90 napos élettartama van, de a lejárata előtt automatikusan megújul. A **auto_generated_ca_lifetime_days** érték nem befolyásolja ezt a tanúsítványt.

Ha a tanúsítvány lejáratát az alapértelmezett 90 napnál nem korábbi értékre szeretné beállítani, adja hozzá az értéket napokban a config. YAML fájl **tanúsítványok** szakaszába.

```yaml
certificates:
  device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
  device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
  trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
  auto_generated_ca_lifetime_days: <value>
```

> [!NOTE]
> Jelenleg a libiothsm korlátozásai meggátolják a 2050 január 1-jén vagy azt követően lejáró tanúsítványok használatát.

Ha a saját eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványait biztosította, akkor ez az érték továbbra is érvényes a munkaterhelés HITELESÍTÉSSZOLGÁLTATÓI tanúsítványára, ha a beállított élettartam értéke rövidebb, mint az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának élettartama.

Miután megadta a jelölőt a config. YAML fájlban, hajtsa végre a következő lépéseket:

1. Törölje a `hsm` mappa tartalmát.

   Windows: `C:\ProgramData\iotedge\hsm\certs and C:\ProgramData\iotedge\hsm\cert_keys` Linux:`/var/lib/iotedge/hsm/certs and /var/lib/iotedge/hsm/cert_keys`

1. Indítsa újra a IoT Edge szolgáltatást.

   Windows:

   ```powershell
   Restart-Service iotedge
   ```

   Linux:

   ```bash
   sudo systemctl restart iotedge
   ```

1. Erősítse meg az élettartam beállítást.

   Windows:

   ```powershell
   iotedge check --verbose
   ```

   Linux:

   ```bash
   sudo iotedge check --verbose
   ```

   Tekintse meg az **éles készültségi** egység kimenetét: tanúsítványok ellenőrzését, amely felsorolja, hogy hány nap elteltével járjon le az automatikusan létrehozott hitelesítésszolgáltatói tanúsítványok érvényessége.

## <a name="next-steps"></a>Következő lépések

A tanúsítványok IoT Edge eszközön történő telepítése szükséges lépés a megoldás éles környezetben történő üzembe helyezése előtt. További információ a [IoT Edge-megoldás éles környezetben való üzembe helyezésének előkészítéséről](production-checklist.md).
