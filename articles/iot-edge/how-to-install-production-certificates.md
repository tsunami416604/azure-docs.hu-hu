---
title: Tanúsítványok telepítése az eszközön – Azure IoT Edge | Microsoft Docs
description: Hozzon létre tesztelési tanúsítványokat, és Ismerje meg, hogyan telepítheti őket egy Azure IoT Edge eszközön az éles üzembe helyezés előkészítéséhez.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cf073572cd5b371ec484c99f14cbefb4cba75ce7
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509903"
---
# <a name="install-production-certificates-on-an-iot-edge-device"></a>Éles tanúsítványok telepítése IoT Edge eszközön

Minden IoT Edge eszköz tanúsítványokat használ a futtatókörnyezet és az eszközön futó modulok közötti biztonságos kapcsolatok létrehozásához.
IoT Edge az átjáróként működő eszközök ugyanezeket a tanúsítványokat használják az alsóbb rétegbeli eszközökhöz való kapcsolódáshoz.

Amikor először telepíti IoT Edge és kiépíti az eszközt, az eszköz ideiglenes tanúsítvánnyal lesz beállítva, hogy tesztelni tudja a szolgáltatást.
Ezek az ideiglenes tanúsítványok 90 nap múlva lejárnak, vagy visszaállíthatók a gép újraindításával.
Ha készen áll az eszközök éles környezetbe való áthelyezésére, vagy egy átjárót szeretne létrehozni, meg kell adnia a saját tanúsítványait.
Ez a cikk bemutatja a tanúsítványok IoT Edge-eszközökön való telepítésének lépéseit.

Ha többet szeretne megtudni a tanúsítványok különböző típusairól és szerepköreiről IoT Edge forgatókönyvben, tekintse meg a [Azure IoT Edge tanúsítványok használatának ismertetése](iot-edge-certs.md)című témakört.

>[!NOTE]
>Az ebben a cikkben használt "legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ" kifejezés a IoT-megoldáshoz tartozó tanúsítványlánc legfelső szintű nyilvános tanúsítványára vonatkozik. Nem kell használnia egy konzorciális hitelesítésszolgáltató tanúsítványának gyökerét vagy a szervezet hitelesítésszolgáltatójának gyökerét. Sok esetben valójában egy közbenső HITELESÍTÉSSZOLGÁLTATÓ nyilvános tanúsítványa.

## <a name="prerequisites"></a>Előfeltételek

* IoT Edge-eszköz, amely [Windows](how-to-install-iot-edge-windows.md) vagy [Linux](how-to-install-iot-edge-linux.md)rendszeren fut.
* Rendelkeznie kell egy legfelső szintű hitelesítésszolgáltató (CA) tanúsítvánnyal, amely önaláírt vagy megbízható kereskedelmi hitelesítésszolgáltatótól vásárolt, például Baltimore, VeriSign, DigiCert vagy GlobalSign.

Ha még nem rendelkezik legfelső szintű hitelesítésszolgáltatóval, de szeretné kipróbálni IoT Edge olyan szolgáltatásokat, amelyek éles tanúsítványokat igényelnek (például átjárói forgatókönyvek), létrehozhat [bemutató-tanúsítványokat a IoT Edge eszköz funkcióinak teszteléséhez](how-to-create-test-certificates.md).

## <a name="create-production-certificates"></a>Éles tanúsítványok létrehozása

A következő fájlok létrehozásához használja a saját hitelesítésszolgáltatóját:

* Legfelső szintű hitelesítésszolgáltató
* Eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa
* Eszköz HITELESÍTÉSSZOLGÁLTATÓI titkos kulcsa

Ebben a cikkben a *legfelső szintű hitelesítésszolgáltatót* nevezzük, nem pedig a szervezet legfelső szintű hitelesítésszolgáltatója. Ez a legfelső szintű hitelesítésszolgáltató a IoT Edge-forgatókönyvhöz, amelyet az IoT Edge hub modul, a felhasználói modulok és az alsóbb rétegbeli eszközök az egymás közötti megbízhatósági kapcsolat létrehozására használnak.

Ha szeretné megtekinteni a tanúsítványok példáját, tekintse át a bemutató tanúsítványokat létrehozó parancsfájlokat a [teszt hitelesítésszolgáltatói tanúsítványok kezelése a mintákhoz és az oktatóanyagokhoz](https://github.com/Azure/iotedge/tree/master/tools/CACertificates)című témakörben.

## <a name="install-certificates-on-the-device"></a>Tanúsítványok telepítése az eszközön

Telepítse a tanúsítványláncot a IoT Edge eszközre, és konfigurálja a IoT Edge futtatókörnyezetet az új tanúsítványokra való hivatkozáshoz.

Ha például a minta parancsfájlokat használta a [bemutató tanúsítványok létrehozásához](how-to-create-test-certificates.md), akkor a IoT Edge eszközre másolandó három fájl a következő lesz:

* Eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Eszköz HITELESÍTÉSSZOLGÁLTATÓjának titkos kulcsa: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. Másolja a három tanúsítvány-és kulcsfájl-fájlt a IoT Edge eszközre.

   Használhat olyan szolgáltatásokat, mint például a [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) vagy a [biztonságos másolási protokollt](https://www.ssh.com/ssh/scp/) használó függvények a tanúsítványfájl áthelyezéséhez.  Ha saját maga hozta létre a tanúsítványokat a IoT Edge eszközön, kihagyhatja ezt a lépést, és használhatja a munkakönyvtár elérési útját.

2. Nyissa meg a IoT Edge biztonsági démon konfigurációs fájlját.

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Állítsa be a **tanúsítvány** tulajdonságait a config. YAML fájlban a tanúsítvány és a kulcsok fájljának URI-ja számára a IoT Edge eszközön. Távolítsa el a `#` karaktert, mielőtt a tanúsítvány tulajdonságai megszüntessék a négy sort. Győződjön meg arról, hogy a (z) **:** sor nem rendelkezik korábbi szóközökkel, és hogy a beágyazott elemek két szóközzel vannak behúzva. Példa:

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "file:///c:/path/device-ca.cert.pem"
        device_ca_pk: "file:///c:/path/device-ca.key.pem"
        trusted_ca_certs: "file:///c:/path/root-ca.root.ca.cert.pem"
      ```

   * Linux:

      ```yaml
      certificates:
        device_ca_cert: "file:///path/device-ca.cert.pem"
        device_ca_pk: "file:///path/device-ca.key.pem"
        trusted_ca_certs: "file:///path/root-ca.root.ca.cert.pem"
      ```

4. Linux-eszközökön ellenőrizze, hogy a felhasználó **iotedge** rendelkezik-e olvasási engedéllyel a tanúsítványokat tároló címtárhoz.

## <a name="next-steps"></a>Következő lépések

A tanúsítványok IoT Edge eszközön történő telepítése szükséges lépés a megoldás éles környezetben történő üzembe helyezése előtt. További információ a [IoT Edge-megoldás éles környezetben való üzembe helyezésének előkészítéséről](production-checklist.md).
