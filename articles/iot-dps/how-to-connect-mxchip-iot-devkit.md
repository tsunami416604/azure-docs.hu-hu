---
title: Hogyan használható az Azure IoT Hub eszköz kiépítése szolgáltatáshoz automatikus átadásának regisztrálni a MXChip IoT DevKit IoT-központ |} Microsoft Docs
description: Hogyan használható az Azure IoT Hub eszköz kiépítése szolgáltatáshoz automatikus átadásának regisztrálni a MXChip IoT DevKit IoT-központot.
author: liydu
ms.author: liydu
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: a5030c7363d02fe9600183e058f2ff3cc23e9475
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750366"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Azure IoT Hub eszköz kiépítése szolgáltatáshoz automatikus átadásának segítségével regisztrálja a MXChip IoT DevKit IoT hubbal

Ez a cikk ismerteti, hogyan használható az Azure IoT Hub eszköz kiépítése szolgáltatáshoz [automatikus kiépítés](concepts-auto-provisioning.md), a MXChip IoT DevKit regisztrálni Azure IoT-központot. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

* Konfigurálja a globális végpontot az eszköz kiépítése szolgáltatáshoz az eszközön.
* Egy X.509 tanúsítvány létrehozásához használt titkos kulcs (UDS) az eszköz egyedi.
* Egy adott eszköz regisztrálását.
* Győződjön meg arról, hogy az eszköz regisztrálva van-e.

A [MXChip IoT DevKit](https://aka.ms/iot-devkit) egy mindent egy Arduino-kompatibilis board gazdag perifériák és érzékelők van. Fejleszthet ki azt a a [Arduino Visual Studio Code kiterjesztése](https://aka.ms/arduino). A DevKit tartalmaz egy növekvő [projektek katalógus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) útmutatást a prototípus az eszközök internetes hálózatát (IoT) megoldások Azure-szolgáltatások előnyeit.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag lépéseinek végrehajtásához, hajtsa végre a következő feladatokat:

* Készítse elő a DevKit lépéseit követve [IoT DevKit AZ3166 csatlakozzon az Azure IoT hubhoz a felhőben](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
* Frissítés a legújabb belső vezérlőprogrammal (1.3.0 vagy újabb) rendelkező a [frissítés DevKit belső vezérlőprogram](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) oktatóanyag.
* Hozzon létre, és az IoT-központ és egy eszköz-üzembehelyezési szolgáltatáspéldány lépéseit követve összekapcsolásához [a IoT Hub eszköz kiépítése szolgáltatás beállítása az Azure portálon](/azure/iot-dps/quick-setup-auto-provision).

## <a name="build-and-deploy-auto-provisioning-registration-software-to-the-device"></a>Hozza létre és automatikus kiépítés regisztrációs szoftver telepítése az eszközre

A DevKit az eszköz-üzembehelyezési létrehozott szolgáltatáspéldány való csatlakozáshoz:

1. Az Azure portálon, válassza ki a **áttekintése** ablaktábláján az eszköz-üzembehelyezési szolgáltatás, és ne feledkezzen meg a **globális eszköz végpont** és **azonosító hatókör** értékeket.
  ![Terjesztési pontok globális végpont és a hatókör azonosítója](./media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

2. Győződjön meg arról, hogy `git` a gépen telepítve van, és hogy a környezeti változókat a parancssori ablakban számára elérhető lesz. Lásd: [szoftver szabadsága természetvédelmi Git ügyféleszközök elől](https://git-scm.com/download/) számára a legújabb verziója van telepítve.

3. Nyisson meg egy parancssort. Az eszköz kiépítése szolgáltatáshoz mintakód a GitHub-tárház klónozása:
  ```bash
  git clone https://github.com/DevKitExamples/DevKitDPS.git
  ```

4. Nyissa meg a Visual Studio Code, a DevKit kapcsolódni a számítógéphez, és nyissa meg a klónozott kódot tartalmazó mappát.

5. Nyissa meg **DevKitDPS.ino**. Keresés és csere `[Global Device Endpoint]` és `[ID Scope]` le feljegyzett értékekkel.
  ![Terjesztési pontok végpont](./media/how-to-connect-mxchip-iot-devkit/endpoint.png) hagyhatja a **registrationId** üres. Az alkalmazás létrehoz egyet a MAC-cím és a belső vezérlőprogram verziója alapján. Ha szeretné testre szabni a regisztrációs Azonosítót, használjon csak alfanumerikus, kis, és az kötőjel kombinációk egy legfeljebb 128 karakter hosszú lehet. További információkért lásd: [kezelése az Azure-portálon eszközbeléptetésnél](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

6. Gyors Megnyitás Visual STUDIO Code használata (Windows: `Ctrl+P`, macOS: `Cmd+P`) és típus *eszköz-feltöltési feladat* build és a kód feltölti a DevKit.

7. A kimeneti ablakban látható, hogy sikeres volt-e a feladatot.

## <a name="save-a-unique-device-secret-on-an-stsafe-security-chip"></a>Az eszköz egyedi titkos kulcs mentése a egy STSAFE biztonsági lapka használata

Automatikus kiépítés konfigurálható egy eszközön, az eszköz alapján [állapotigazolási mechanizmus](concepts-security.md#attestation-mechanism). A MXChip IoT DevKit használja a [eszköz identitása összeállítás motor](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) a a [Trusted Computing Group](https://trustedcomputinggroup.org). A *az eszköz egyedi titkos kulcs* (UDS) egy STSAFE biztonsági mentése a DevKit lapka létrehozásához használja az eszközhöz tartozó egyedi [X.509 tanúsítvány](concepts-security.md#x509-certificates). Ezzel a tanúsítvánnyal később a beléptetési folyamat az eszköz-üzembehelyezési szolgáltatás, és futásidőben regisztrálás során.

Egy tipikus eszköz egyedi titkos kulcs egy 64 karakterből álló karakterlánc, az alábbi mintában látható módon:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Egyes két karakter a hexadecimális értékként a biztonsági számítás szolgál. The preceding sample UDS is resolved to: `0x19`, `0xe2`, `0x5a`, `0x25`, `0x9d`, `0x0c`, `0x2b`, `0xe0`, `0x3a`, `0x02`, `0xd4`, `0x16`, `0xc0`, `0x5c`, `0x48`, `0xcc`, `0xd0`, `0xcc`, `0x7d`, `0x17`, `0x43`, `0x45`, `0x8a`, `0xae`, `0x1c`, `0xb4`, `0x88`, `0xb0`, `0x74`, `0x99`, `0x3e`, `0xae`.

Az eszköz egyedi titkos kulcs található a DevKit mentéséhez:

1. Nyissa meg a soros figyelő megfelelő eszköz, például a Putty használatával. Lásd: [konfigurációs mód használata](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) részleteiről.

2. A számítógéphez csatlakoztatott DevKit, tartsa lenyomva a **A** gombra, és nyomja le az és a kibocsátási a **alaphelyzetbe** gombra kattintva adja meg a konfigurációs mód. A képernyőn látható, a DevKit azonosító és a konfiguráció.

3. A mintát UDS karakterláncot, és egy vagy több karaktert átállítása más értékek között `0` és `f` a saját UDS.

4. A soros figyelő ablakban írja be a *set_dps_uds [your_own_uds_value]* válassza ki az ENTER billentyűt.
  > [!NOTE]
  > Például, ha az utolsó két karakterek módosításával beállíthatja a saját UDS `f`, meg kell adnia az ehhez hasonló parancsot: `set_dps_uds 19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eff`.

5. A soros figyelő ablak bezárása nélkül nyomja meg a **alaphelyzetbe** a DevKit gombjára.

6. Jegyezze fel a **DevKit MAC-cím** és **DevKit belsővezérlőprogram-verziónként** értékeket.
  ![Belső vezérlőprogram-verziója](./media/how-to-connect-mxchip-iot-devkit/firmware-version.png)

## <a name="generate-an-x509-certificate"></a>Egy X.509 tanúsítvány létrehozása

### <a name="windows"></a>Windows

1. Nyissa meg a Fájlkezelőt, és nyissa meg az eszköz-üzembehelyezési korábbi klónozása szolgáltatás mintakódot tartalmazó mappát. Az a **.build** mappa, a Keresés és a példány **DPS.ino.bin** és **DPS.ino.map** be a kódot tartalmazó mappát.
  ![Létrehozott fájlok](./media/how-to-connect-mxchip-iot-devkit/generated-files.png)
  > [!NOTE]
  > Ha módosította a `built.path` Arduino konfigurációját egy másik mappába, kell konfigurált mappában található fájlokat.

2. Illessze be a két fájlt a **eszközök** ugyanazon a szinten, a mappa a **.build** mappa.

3. Futtatás **dps_cert_gen.exe**. Az útmutatást követve írja be a **UDS**, a **MAC-cím** a DevKit a és a **belsővezérlőprogram-verziónként** X.509-tanúsítvány létrehozásához.
  ![Run dps-cert-gen.exe](./media/how-to-connect-mxchip-iot-devkit/dps-cert-gen.png)

4. Miután az X.509 tanúsítvány jön létre, egy **.pem** tanúsítvány ugyanabban a mappában vannak mentve.

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>Hozzon létre egy eszköz beléptetési az eszköz-üzembehelyezési szolgáltatás

1. Az Azure-portálon keresse meg az eszköz kiépítése szolgáltatáspéldány. Válassza ki **regisztrációkat kezelése**, majd válassza ki a **egyedi regisztrációkat** lapon. ![Egyes regisztrációkat](./media/how-to-connect-mxchip-iot-devkit/individual-enrollments.png)

2. Válassza a **Hozzáadás** lehetőséget.

3. A "Hozzáadás regisztrációja" panelen:
   - Válassza ki **X.509** alatt **mechanizmus**
   - Válassza a "Fájl kijelölése" **elsődleges .pem vagy a .cer fájlt**
   - a fájl megnyitása párbeszédpanelen keresse meg és töltse fel a **.pem** imént létrejött tanúsítványt
   - a többi alapértelmezett hagyja, és kattintson **mentése**

   ![Tanúsítvány feltöltése](./media/how-to-connect-mxchip-iot-devkit/upload-cert.png)

  > [!NOTE]
  > Ha ez az üzenet hiba:
  >
  > `{"message":"BadRequest:{\r\n \"errorCode\": 400004,\r\n \"trackingId\": \"1b82d826-ccb4-4e54-91d3-0b25daee8974\",\r\n \"message\": \"The certificate is not a valid base64 string value\",\r\n \"timestampUtc\": \"2018-05-09T13:52:42.7122256Z\"\r\n}"}`
  >
  > Nyissa meg a tanúsítványfájl **.pem** szövegként (Nyissa meg a Jegyzettömbben vagy bármilyen szövegszerkesztővel), és a sorok törlése:
  >
  > `"-----BEGIN CERTIFICATE-----"` és `"-----END CERTIFICATE-----"`.
  >


## <a name="start-the-devkit"></a>Indítsa el a DevKit

1. Nyissa meg a Visual STUDIO Code és a soros figyelő.

2. Nyomja meg a **alaphelyzetbe** a DevKit gombjára.

A regisztrációs DevKit elindítása az eszköz üzembe helyezési szolgáltatással láthatja.

![Visual STUDIO Code kimeneti](./media/how-to-connect-mxchip-iot-devkit/vscode-output.png)

## <a name="verify-that-the-devkit-is-registered-with-azure-iot-hub"></a>Győződjön meg arról, hogy a DevKit regisztrálva van-e az Azure IoT Hub

Miután elindul az eszközt, a következő műveletekre kerül sor:

1. Az eszköz regisztrációs kérelmet küld az eszköz kiépítése szolgáltatáshoz.
2. Az eszköz-üzembehelyezési szolgáltatás, amelyhez az eszköz válaszol regisztrációs kihívást küld vissza.
3. A sikeres regisztráció az eszköz-üzembehelyezési szolgáltatás visszaküldi az IoT Hub URI, eszköz-Azonosítóját és a titkosított kulcs az eszközön.
4. Az IoT-központ ügyfélalkalmazás az eszközön a központ csatlakozik.
5. Sikeres csatlakozás szeretne az elosztóhoz megtekintheti az eszközt az IoT Hub eszköz Explorer jelennek meg.
  ![Regisztrált eszközök](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="change-the-device-id"></a>Módosítsa az eszköz azonosítója

Az alapértelmezett eszköz azonosítója regisztrálva az Azure IoT Hub *AZ3166*. Ha szeretné módosítani az azonosítója, kövesse az utasításokat a [Eszközazonosító testreszabása](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémába ütközik, tekintse meg az Iot-DevKit [– gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/), vagy a következő támogatási csatornák érheti el:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, az eszköz-üzembehelyezési szolgáltatás eszköz identitása összeállítás motor használatával, úgy, hogy az eszköz automatikusan regisztrálhat az Azure IoT Hub biztonságosan eszköz regisztrálásához. 

Összefoglalva, megtudta, hogyan:

> [!div class="checklist"]
> * Konfigurálja a globális végpontot az eszköz kiépítése szolgáltatáshoz az eszközön.
> * Egy X.509 tanúsítvány létrehozásához használt eszköz egyedi titkos kulcs.
> * Egy adott eszköz regisztrálását.
> * Győződjön meg arról, hogy az eszköz regisztrálva van-e.

Megtudhatja, hogyan [létrehozása és biztosítása a szimulált eszköz](./quick-create-simulated-device.md).

