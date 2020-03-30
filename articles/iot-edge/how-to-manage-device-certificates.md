---
title: Eszköztanúsítványok kezelése - Azure IoT Edge | Microsoft dokumentumok
description: Hozzon létre teszttanúsítványokat, telepítse és kezelje őket egy Azure IoT Edge-eszközön az éles telepítés előkészítéséhez.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c18c3d560adb3c3cae54bda808ee5842c260fd6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79539207"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>Tanúsítványok kezelése IoT Edge-eszközön

Minden IoT Edge-eszköz tanúsítványok használatával hoz létre biztonságos kapcsolatokat a futtatófa és az eszközön futó modulok között. Az átjáróként működő IoT Edge-eszközök ugyanazokat a tanúsítványokat használják az alsóbb rétegbeli eszközeikhez való csatlakozáshoz is.

## <a name="install-production-certificates"></a>Éles tanúsítványok telepítése

Amikor először telepíti az IoT Edge-et, és kiépíti az eszközt, az eszköz ideiglenes tanúsítványokkal van beállítva, hogy tesztelhesse a szolgáltatást.
Ezek az ideiglenes tanúsítványok 90 napon belül lejárnak, vagy a számítógép újraindításával alaphelyzetbe állíthatók.
Ha készen áll arra, hogy az eszközöket éles környezetbe helyezze át, vagy hozzon létre egy átjáró-forgatókönyvet, saját tanúsítványokat kell megadnia.
Ez a cikk bemutatja a tanúsítványok telepítésének lépéseit az IoT Edge-eszközökön.

Ha többet szeretne megtudni a különböző típusú tanúsítványokról és azok szerepköreiről egy IoT Edge-forgatókönyvben, [olvassa el A tanúsítványok Azure IoT Edge-használati módjának megismerése](iot-edge-certs.md)című témakört.

>[!NOTE]
>A "legfelső szintű hitelesítésszolgáltató" ebben a cikkben használt kifejezés az IoT-megoldás tanúsítványláncának legfelső hatósági nyilvános tanúsítványára hivatkozik. Nem kell használnia a konzorciális hitelesítésszolgáltató tanúsítványgyúját vagy a szervezet hitelesítésszolgáltatójának gyökerét. Sok esetben ez valójában egy köztes hitelesítésszolgáltató nyilvános tanúsítvány.

### <a name="prerequisites"></a>Előfeltételek

* IoT Edge-eszköz, amely [Windows](how-to-install-iot-edge-windows.md) vagy [Linux](how-to-install-iot-edge-linux.md)rendszeren fut.
* Rendelkeznie kell egy legfelső szintű hitelesítésszolgáltatói tanúsítvánnyal, amely saját maga által aláírt, vagy megbízható kereskedelmi hitelesítésszolgáltatótól ( például Baltimore, Verisign, DigiCert vagy GlobalSign) vásárolt.

Ha még nem rendelkezik legfelső szintű hitelesítésszolgáltatóval, de ki szeretné próbálni az IoT Edge-funkciókat, amelyek éles tanúsítványokat (például átjáróforgatókönyveket) igényelnek, [dedenokolhatja az IoT Edge-eszközszolgáltatások teszteléséhez.](how-to-create-test-certificates.md)

### <a name="create-production-certificates"></a>Termelési tanúsítványok létrehozása

A következő fájlok létrehozásához saját hitelesítésszolgáltatót kell használnia:

* Legfelső szintű hitelesítésszolgáltató
* Eszközhitelesítéstanúsítvány
* Az eszközhitelesítéshez tartozó személyes kulcs

Ebben a cikkben a *legfelső hitelesítésszolgáltatónem* a legfelső hitelesítésszolgáltató. Ez az IoT Edge-forgatókönyv legfelső hitelesítésszolgáltatója, amelyet az IoT Edge hub modul, a felhasználói modulok és az alsóbb rétegbeli eszközök egymás közötti megbízhatóság idomok létrehozásához használnak.

Ha példát szeretne látni ezekre a tanúsítványokra, tekintse át azokat a parancsfájlokat, amelyek demótanúsítványokat hoznak létre a teszthitelesítési tanúsítványok kezelése című témakörben [a minták és oktatóanyagok számára.](https://github.com/Azure/iotedge/tree/master/tools/CACertificates)

### <a name="install-certificates-on-the-device"></a>Tanúsítványok telepítése az eszközre

Telepítse a tanúsítványláncot az IoT Edge-eszközön, és konfigurálja az IoT Edge futásidejű hivatkozva az új tanúsítványokat.

Ha például a mintaparancsfájlokat használta [a bemutatótanúsítványok létrehozásához,](how-to-create-test-certificates.md)másolja a következő fájlokat az IoT-Edge eszközre:

* Eszközhitelesítéstanúsítvány:`<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Eszközhitelesítésszemélyes kulcsa:`<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Legfelső szintű hitelesítésterület:Root CA:`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. Másolja a három tanúsítványt és a kulcsfájlokat az IoT Edge-eszközre.

   Használhatja a szolgáltatást, például [az Azure Key Vault](https://docs.microsoft.com/azure/key-vault) vagy a függvény, például a Secure copy [protokoll](https://www.ssh.com/ssh/scp/) a tanúsítványfájlok áthelyezéséhez.  Ha maga az IoT Edge-eszközön hozta létre a tanúsítványokat, kihagyhatja ezt a lépést, és használhatja a munkakönyvtár elérési útját.

1. Nyissa meg az IoT Edge biztonsági démon konfigurációs fájlját.

   * Windows:`C:\ProgramData\iotedge\config.yaml`
   * Linux:`/etc/iotedge/config.yaml`

1. Állítsa be a **tanúsítvány** tulajdonságait a config.yaml fájlban a teljes elérési utat a tanúsítvány és a kulcsfájlok at az IoT Edge-eszközön. A `#` tanúsítvány tulajdonságai előtti karakter eltávolítása a négy sor megjegyzésének megszüntetéséhez. Győződjön meg arról, hogy a **tanúsítványok:** sor nem rendelkezik előző szóközzel, és hogy a beágyazott elemeket két szóköz bevan húzva. Példa:

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "c:\\<path>\\device-ca.cert.pem"
        device_ca_pk: "c:\\<path>\\device-ca.key.pem"
        trusted_ca_certs: "c:\\<path>\\root-ca.root.ca.cert.pem"
      ```

   * Linux:

      ```yaml
      certificates:
        device_ca_cert: "<path>/device-ca.cert.pem"
        device_ca_pk: "<path>/device-ca.key.pem"
        trusted_ca_certs: "<path>/root-ca.root.ca.cert.pem"
      ```

1. Linux-eszközökön győződjön meg arról, hogy a felhasználó **iotedge** rendelkezik olvasási engedéllyel a tanúsítványokat tároló könyvtárhoz.

1. Ha korábban már használt más tanúsítványokat az IoT Edge-hez az eszközön, törölje a fájlokat a következő két könyvtárban az IoT Edge indítása vagy újraindítása előtt:

   * Windows: `C:\ProgramData\iotedge\hsm\certs` és`C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux: `/var/lib/iotedge/hsm/certs` és`/var/lib/iotedge/hsm/cert_keys`

## <a name="customize-certificate-lifetime"></a>Tanúsítvány élettartamának testreszabása

Az IoT Edge több esetben automatikusan létrehozza a tanúsítványokat az eszközön, többek között:

* Ha az IoT Edge telepítésekor és üzembe helyezésekor nem adja meg a saját termelési tanúsítványait, az IoT Edge biztonsági kezelője automatikusan létrehoz egy **eszközhitelesítési tanúsítványt.** Ez az önaláírt tanúsítvány csak fejlesztési és tesztelési forgatókönyvek, nem éles környezetben. Ez a tanúsítvány 90 nap után lejár.
* Az IoT Edge biztonsági kezelője egy **számítási feladat hitelesítésszolgáltatói tanúsítványt** is létrehoz, amelyet az eszköz hitelesítésszolgáltatói tanúsítványa írt alá

Az IoT Edge-eszközön lévő különböző tanúsítványok funkciójáról további információt [a Ismerje meg, hogyan használja az Azure IoT Edge a tanúsítványokat.](iot-edge-certs.md)

E két automatikusan létrehozott tanúsítvány esetén beállíthatja a **auto_generated_ca_lifetime_days** jelzőt a config.yaml fájlban a tanúsítványok élettartama szerinti napok számának beállításához.

>[!NOTE]
>Van egy harmadik automatikusan létrehozott tanúsítvány, amelyet az IoT Edge biztonsági kezelője hoz létre, az **IoT Edge hub server tanúsítvány.** Ennek a tanúsítványnak mindig 90 napja van, de a lejárat előtt automatikusan megújul. A **auto_generated_ca_lifetime_days** érték nincs hatással erre a tanúsítványra.

Ha a tanúsítvány lejáratát nem az alapértelmezett 90 napra szeretné **certificates** beállítani, adja hozzá az értéket napokban a config.yaml fájl tanúsítványszakaszában.

```yaml
certificates:
  device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
  device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
  trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
  auto_generated_ca_lifetime_days: <value>
```

Ha saját eszközhitelesítési tanúsítványokat adott meg, akkor ez az érték továbbra is vonatkozik a számítási feladat hitelesítésszolgáltatói tanúsítványára, feltéve, hogy a beállított élettartamú érték rövidebb, mint az eszköz hitelesítésszolgáltatói tanúsítványának élettartama.

Miután megadta a jelzőt a config.yaml fájlban, tegye a következő lépéseket:

1. Törölje a `hsm` mappa tartalmát.

   Windows: `C:\ProgramData\iotedge\hsm\certs and C:\ProgramData\iotedge\hsm\cert_keys` Linux:`/var/lib/iotedge/hsm/certs and /var/lib/iotedge/hsm/cert_keys`

1. Indítsa újra az IoT Edge-szolgáltatást.

   Windows:

   ```powershell
   Restart-Service iotedge
   ```

   Linux:

   ```bash
   sudo systemctl restart iotedge
   ```

1. Erősítse meg az élettartam-beállítást.

   Windows:

   ```powershell
   iotedge check --verbose
   ```

   Linux:

   ```bash
   sudo iotedge check --verbose
   ```

   Ellenőrizze a **termelési készenlét: tanúsítványok** ellenőrzése, amely felsorolja a napok száma, amíg az automatikusan generált eszköz hitelesítésszolgáltató itanúsítványok lejár.

## <a name="next-steps"></a>További lépések

A tanúsítványok telepítése egy IoT Edge-eszközön szükséges lépés a megoldás éles környezetben való üzembe helyezése előtt. További információ arról, hogyan [készülhet fel az IoT Edge-megoldás éles környezetben való üzembe helyezésére.](production-checklist.md)
