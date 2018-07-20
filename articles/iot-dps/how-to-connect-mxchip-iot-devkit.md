---
title: Az Azure IoT Hub Device Provisioning Service automatikus kiépítés használata az MXChip IoT DevKit regisztrálása az IoT Hub |} A Microsoft Docs
description: Hogyan használható az Azure IoT Hub Device Provisioning Service automatikus kiépítés az MXChip IoT DevKit regisztrálása az IoT hubbal.
author: liydu
ms.author: liydu
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: 300bde27f956b449d1e0e73f7efb54a13df27b0c
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145665"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Az MXChip IoT DevKit IoT Hub-regisztráció az Azure IoT Hub Device Provisioning Service automatikus kiépítés használatával

Ez a cikk azt ismerteti, hogyan használható az Azure IoT Hub Device Provisioning Service [automatikus kiépítés](concepts-auto-provisioning.md), regisztrálja az MXChip IoT fejlesztői készlet az Azure IoT Hub szolgáltatással. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

* Konfigurálja az eszközkiépítési szolgáltatás az eszközön, a globális végpontját.
* Az eszköz egyedi titkos kulcsot (frissítési) használatával hozzon létre egy X.509 tanúsítvány.
* Egy adott eszköz regisztrálásához.
* Győződjön meg arról, hogy az eszköz regisztrálva van-e.

A [MXChip IoT DevKit](https://aka.ms/iot-devkit) egy teljes körű Arduino-kompatibilis tábla a gazdag perifériák és érzékelők van. Is fejleszthet, használja a [Arduino a Visual Studio Code-bővítmény](https://aka.ms/arduino). A fejlesztői készlet együttműködik a egyre növekvő [projektek katalógus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) is, amelyek kihasználják az Azure-szolgáltatások prototípusként Internet of Things (IoT) megoldását.

## <a name="before-you-begin"></a>Előkészületek

Ez az oktatóanyag lépéseinek végrehajtásához először a következő feladatokat végezheti el:

* Készítse elő a DevKit a lépéseket követve [IoT DevKit AZ3166 csatlakoztatása az Azure IoT hubba a felhőben](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
* Frissítés a legújabb belső vezérlőprogramjának (1.3.0 vagy újabb) az a [frissítés DevKit belső vezérlőprogram](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) oktatóanyag.
* Hozzon létre, és a egy IoT Hub csatolása egy eszközregisztrációs szolgáltatás példányát a lépéseket követve az [állítsa be az IoT Hub Device Provisioning Service az Azure Portallal](/azure/iot-dps/quick-setup-auto-provision).

## <a name="build-and-deploy-auto-provisioning-registration-software-to-the-device"></a>Létrehozása és üzembe helyezése a regisztrációs szoftver automatikus kiépítés az eszközön

A fejlesztői készlet csatlakozás az eszközkiépítési szolgáltatás példányt Ön hozta létre:

1. Az Azure Portalon válassza ki a **áttekintése** ablaktábláján az eszközkiépítési szolgáltatás, és jegyezze le a **globális eszköz végpontja** és **azonosító hatóköre** értékeket.
  ![A DPS globális végpontja és azonosító hatóköre](./media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

2. Ellenőrizze, hogy `git` telepítve van a gépén, és hogy az hozzáadódik a parancsablakból elérhető környezeti változókhoz. Lásd: [Software Freedom Conservancy's Git ügyfél eszközeiben](https://git-scm.com/download/) szeretné, hogy a legfrissebb verzió van telepítve.

3. Nyisson meg egy parancssort. Klónozza a GitHub-adattárat a az eszközkiépítési szolgáltatás mintakód:
  ```bash
  git clone https://github.com/DevKitExamples/DevKitDPS.git
  ```

4. Nyissa meg a Visual Studio Code-ot, a DevKit csatlakoztatása a számítógéphez, és nyissa meg a klónozott kódot tartalmazó mappát.

5. Nyissa meg **DevKitDPS.ino**. Keresés és csere `[Global Device Endpoint]` és `[ID Scope]` le feljegyzett értékekkel.
  ![A DPS végpont](./media/how-to-connect-mxchip-iot-devkit/endpoint.png) hagyhatja a **registrationId** üres. Az alkalmazás létrehoz egyet a MAC-cím és a belső vezérlőprogram verziója alapján. Ha testre szeretné szabni a regisztrációs Azonosítót, csak alfanumerikus karakterek, kisbetűs használja, és az, amely legfeljebb 128 karakterből kombinációkkal kötőjelet tartalmazhat. További információkért lásd: [eszközök beléptetésének kezelése az Azure Portalon](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

6. Gyors nyissa meg a VS Code használata (Windows: `Ctrl+P`, macOS: `Cmd+P`), és írja be *eszköz feltöltési feladat* hozhat létre és töltse fel a kódot a fejlesztői készlet.

7. A kimeneti ablak mutatja, hogy a feladat sikeres volt-e.

## <a name="save-a-unique-device-secret-on-an-stsafe-security-chip"></a>Egy STSAFE biztonsági nehézségekbe ütközhet az eszköz egyedi titkos kulcs mentése

Automatikus kiépítés konfigurálható egy eszközön, az eszköz alapján [igazolási mechanizmus](concepts-security.md#attestation-mechanism). Az MXChip IoT DevKit használja a [eszközt identitás összeállítás motor](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) származó a [Trusted Computing Group](https://trustedcomputinggroup.org). A *eszköz egyedi titkos kulcs* (frissítési) egy STSAFE biztonsági mentésre a DevKit lapka létrehozásához használja az eszköz a egyedi [X.509-tanúsítvány](concepts-security.md#x509-certificates). A tanúsítványt később a beléptetési folyamat a Device provisioning service és a futásidőben a regisztráció során használatos.

Egy tipikus eszköz egyedi titkos kulcsot a következő 64 karakterből álló karakterláncnak az alábbi mintában látható módon:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

A karakterlánc karakterek párok biztonsági kiszámításakor használt van osztva. Frissítési Tartománnyal fel lett oldva az előző példa: `0x19`, `0xe2`, `0x5a`, `0x25`, `0x9d`, `0x0c`, `0x2b`, `0xe0`, `0x3a`, `0x02`, `0xd4`, `0x16` , `0xc0`, `0x5c`, `0x48`, `0xcc`, `0xd0`, `0xcc`, `0x7d`, `0x17`, `0x43`, `0x45`, `0x8a`, `0xae`, `0x1c`, `0xb4`, `0x88`, `0xb0`, `0x74`, `0x99`, `0x3e`, `0xae`.

A fejlesztői készlet eszköz egyedi titkos kulcs mentése:

1. Nyissa meg a soros figyelő egy eszközt, például a Putty használatával. Lásd: [konfigurációs mód használata](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) részleteiről.

2. A fejlesztői készlet a számítógéphez csatlakozik, tartsa lenyomva a **A** gombra, és nyomja le az és kiadása a **alaphelyzetbe** gombra kattintva adja meg a konfigurációs mód. A képernyőn látható, a fejlesztői készlet azonosítója és a konfiguráció.

3. A minta frissítési Tartománnyal karakterláncot, és módosítsa egy vagy több karakter közötti más értékeket is `0` és `f` a saját frissítési Tartománnyal.

4. A soros figyelő ablakban írja be a *set_dps_uds [your_own_uds_value]* , és nyomja le az Enter.
  > [!NOTE]
  > Például, ha a két utolsó karakterek módosításával állítsa be a saját frissítési Tartománnyal `f`, be kell írnia az ehhez hasonló parancsot: `set_dps_uds 19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eff`.

5. Anélkül, hogy a soros figyelő ablak bezárása, nyomja le az **alaphelyzetbe** a DevKit gombjára.

6. Jegyezze fel a **DevKit MAC-cím** és **DevKit belső vezérlőprogram verziója** értékeket.
  ![Belső vezérlőprogram verziója](./media/how-to-connect-mxchip-iot-devkit/firmware-version.png)

## <a name="generate-an-x509-certificate"></a>Hozzon létre egy X.509-tanúsítvány

### <a name="windows"></a>Windows

1. Nyissa meg a Fájlkezelőt, és lépjen az eszközkiépítési szolgáltatás mintakód, amely korábban klónozta tartalmazó mappát. Az a **.build** mappát, keresési és másolási **DPS.ino.bin** és **DPS.ino.map**.
  ![A létrehozott fájlok](./media/how-to-connect-mxchip-iot-devkit/generated-files.png)
  > [!NOTE]
  > Ha módosította a `built.path` Arduino konfigurációja egy másik mappába, meg kell keresnie azokat a fájlokat a konfigurált mappában.

2. Illessze be két fájlt a **eszközök** ugyanazon a szinten, a mappa a **.build** mappát.

3. Futtatás **dps_cert_gen.exe**. Kövesse az utasításokat követve adja meg a **frissítési Tartománnyal**, a **MAC-cím** számára a fejlesztői készlet és a **belső vezérlőprogram verziója** X.509-tanúsítvány létrehozásához.
  ![Run dps-cert-gen.exe](./media/how-to-connect-mxchip-iot-devkit/dps-cert-gen.png)

4. X.509-tanúsítvány jön létre, miután egy **.pem** tanúsítvány ugyanabba a mappába menti.

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>Eszközregisztrációs bejegyzés létrehozása az eszközkiépítési szolgáltatással

1. Az Azure Portalon lépjen az Eszközkiépítési szolgáltatás példányt. Válassza ki **beléptetések kezelése**, majd válassza ki a **egyéni regisztrációk** fülre. ![Egyéni regisztrációk](./media/how-to-connect-mxchip-iot-devkit/individual-enrollments.png)

2. Válassza a **Hozzáadás** lehetőséget.

3. A "Regisztráció hozzáadása" panelen:
   - Válassza ki **X.509** alatt **mechanizmus**
   - Válassza a "Fájl kijelölése" **elsődleges tanúsítvány .pem or .cer fájlja**
   - a fájl megnyitása párbeszédpanelen keresse meg és töltse fel a **.pem** imént létrehozott tanúsítvány
   - az alapértelmezett a többit bízza, és kattintson a **mentése**

   ![Tanúsítvány feltöltése](./media/how-to-connect-mxchip-iot-devkit/upload-cert.png)

  > [!NOTE]
  > Ha ez az üzenet hiba van:
  >
  > `{"message":"BadRequest:{\r\n \"errorCode\": 400004,\r\n \"trackingId\": \"1b82d826-ccb4-4e54-91d3-0b25daee8974\",\r\n \"message\": \"The certificate is not a valid base64 string value\",\r\n \"timestampUtc\": \"2018-05-09T13:52:42.7122256Z\"\r\n}"}`
  >
  > Nyissa meg a tanúsítványfájl **.pem** szövegként (Megnyitás a Jegyzettömbbe, vagy bármilyen szövegszerkesztővel), és törli a sorokat:
  >
  > `"-----BEGIN CERTIFICATE-----"` és `"-----END CERTIFICATE-----"`.
  >


## <a name="start-the-devkit"></a>Indítsa el a fejlesztői készlet

1. Nyissa meg a VS Code és a soros figyelő.

2. Nyomja le az **alaphelyzetbe** a DevKit gombjára.

A fejlesztői készlet kezdő a regisztráció láthatja a eszközök kiépítési szolgáltatáshoz.

![A VS Code kimenet](./media/how-to-connect-mxchip-iot-devkit/vscode-output.png)

## <a name="verify-that-the-devkit-is-registered-with-azure-iot-hub"></a>Győződjön meg arról, hogy a fejlesztői készlet regisztrálva van-e az Azure IoT Hub szolgáltatással

Miután az eszköz elindul, a következő műveleteket hajtja végre:

1. Az eszköz regisztrációs kérést küld a device provisioning szolgáltatás.
2. A device provisioning szolgáltatás visszaküld egy regisztrációs kihívást, amelyre az eszköz válaszol.
3. Sikeres regisztráció esetén a device provisioning szolgáltatás visszaküldi az IoT Hub URI-t, az Eszközazonosítót és a titkosított kulcs az eszközhöz.
4. Az IoT Hub-ügyfélalkalmazás az eszköz csatlakozik a hubhoz.
5. Sikeres csatlakozás a hubhoz tekintse meg az eszközt az IoT Hub Device Explorer jelennek meg.
  ![Eszköz regisztrálása](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="change-the-device-id"></a>Módosítsa az eszköz azonosítója

Az alapértelmezett eszköz azonosítója az Azure IoT Hub regisztrálva van *AZ3166*. Ha szeretné módosítani az azonosítója, kövesse a [Eszközazonosító testreszabása](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémákat tapasztal, tekintse meg az Iot DevKit [– gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/), vagy keresse fel a következő támogatási csatornákat:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan az eszközkiépítési szolgáltatással eszközt identitás összeállítás motor használatával úgy, hogy az eszköz automatikusan regisztrálhat az Azure IoT Hub biztonságos eszközök regisztrálása. 

Az összegzés megtanulta, hogyan lehet:

> [!div class="checklist"]
> * Konfigurálja az eszközkiépítési szolgáltatás az eszközön, a globális végpontját.
> * Eszköz egyedi titkos kulcs használatával hozzon létre egy X.509 tanúsítvány.
> * Egy adott eszköz regisztrálásához.
> * Győződjön meg arról, hogy az eszköz regisztrálva van-e.

Ismerje meg, hogyan [egy szimulált eszköz létrehozásával és kiépítésével](./quick-create-simulated-device.md).

