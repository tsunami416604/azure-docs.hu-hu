---
title: "Azure IoT Hub eszköz kiépítése szolgáltatáshoz való kapcsolódáshoz MXChip IoT DevKit használata |} Microsoft Docs"
description: "MXChip IoT DevKit használata Azure IoT Hub eszköz kiépítése szolgáltatáshoz való kapcsolódáshoz"
services: iot-dps
keywords: 
author: liydu
ms.author: liydu
ms.date: 02/20/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 77c8a6a5e384d27dca2582cc0fedc2bd23c0592e
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-hub-device-provisioning-service"></a>MXChip IoT DevKit csatlakozni az Azure IoT Hub eszköz-üzembehelyezési szolgáltatás

Ez a cikk ismerteti, hogyan DevKit konfigurálása annak érdekében, hogy az IoT Hub használata az eszköz kiépítése szolgáltatáshoz való automatikus regisztrációt. Az oktatóanyag során a következőket fogja elsajátítani:

* Konfigurálja a terjesztési pontok globális végpontot eszközön
* X.509 tanúsítvány létrehozásához használt egyedi eszköz titkos kulcs (UDS)
* Egyes eszköz regisztrálása
* Ellenőrizze, hogy az eszköz regisztrálva van

A [MXChip IoT DevKit](https://aka.ms/iot-devkit) egy mindent egy Arduino kompatibilis board gazdag perifériák és érzékelők van. A használatával fejleszthet [Arduino Visual Studio Code kiterjesztése](https://aka.ms/arduino). Az érkezett egy növekvő [projektek katalógus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) hogy végigvezesse prototípus az eszközök internetes hálózatát (IoT) megoldások, amelyek a Microsoft Azure-szolgáltatások előnyeit.

## <a name="before-you-begin"></a>Előkészületek

Ebben az oktatóanyagban a lépések elvégzéséhez a következőkre lesz szüksége:

* Készítse elő a DevKit rendelkező [– első lépések útmutató](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
* Frissítsen a legújabb belső vezérlőprogrammal (> = 1.3.0) rendelkező [belső vezérlőprogram frissítése](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) oktatóanyag.
* Létrehozhatja és összekapcsolhatja az IoT Hub eszköz kiépítése szolgáltatáshoz példánnyal rendelkező [beállítása automatikus kiépítés](https://docs.microsoft.com/en-us/azure/iot-dps/quick-setup-auto-provision).

## <a name="set-up-the-device-provisioning-service-configuration-on-the-device"></a>Az eszközön az eszköz kiépítése szolgáltatáshoz konfigurációjának beállítása

Ahhoz, hogy az eszköz kiépítése szolgáltatáshoz való kapcsolódáshoz DevKit példány létrehozott:

1. Az Azure portálon, válassza ki a **áttekintése** az eszköz kiépítése szolgáltatáshoz, és jegyezze fel a **globális eszköz végpont** és **azonosító hatókör** érték.
  ![Terjesztési pontok globális végpont és a hatókör azonosítója](./media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

2. Győződjön meg arról, hogy a(z) `git` telepítve van a gépen, és a parancsablakból elérhető környezeti változókhoz van adva. Lásd: [szoftver szabadsága természetvédelmi Git ügyféleszközök elől](https://git-scm.com/download/) számára a legújabb verziója van telepítve.

3. Nyisson meg egy parancssort. A terjesztési pontok mintakód a GitHub-tárház klónozása:
  ```bash
  git clone https://github.com/DevKitExamples/DevKitDPS.git
  ```

4. Indítsa el a Visual STUDIO Code és DevKit kapcsolódni a számítógéphez, nyissa meg a klónozott kódot tartalmazó mappát.

5. Nyissa meg **DevKitDPS.ino**, keresése és cseréje `[Global Device Endpoint]` és `[ID Scope]` csak lejegyezheti értékekkel.
  ![Terjesztési pontok végpont](./media/how-to-connect-mxchip-iot-devkit/endpoint.png) hagyhatja a **registrationId** üres, mint az alkalmazás létrehoz egy a MAC-cím és a belső vezérlőprogram verziója alapján. Ha szeretné testre szabhatja, regisztrációs Azonosítót alfanumerikus, nagybetűk, és kombinációk csak a legfeljebb 128 karakter hosszú kötőjel rendelkezik. További információkért lásd: [kezelése az Azure-portálon eszközbeléptetésnél](https://docs.microsoft.com/en-us/azure/iot-dps/how-to-manage-enrollments).

6. Használjon **gyors nyissa meg** Visual STUDIO Code (Windows: `Ctrl+P`, macOS: `Cmd+P`) és típus **eszköz-feltöltési feladat** build és a kód feltölti a DevKit.

7. A feladat sikeres a kimeneti ablakban láthatja.

## <a name="save-unique-device-secret-on-stsafe-security-chip"></a>Egyedi eszköz titkos kulcs mentése a STSAFE biztonsági lapka használata

Eszköz kiépítése szolgáltatáshoz eszköz alapján konfigurálhatja a [hardveres biztonsági modul (HSM)](https://azure.microsoft.com/en-us/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/). DevKit használ [eszköz identitása összeállítás motor (DARABOLHATÓ)](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) a a [Trusted Computing Group (TCG)](https://trustedcomputinggroup.org). A **egyedi eszköz titkos kulcs (UDS)** mentett STSAFE biztonsági a DevKit lapka használja az eszköz egyedi [X.509](https://docs.microsoft.com/en-us/azure/iot-dps/tutorial-set-up-device#select-a-hardware-security-module) tanúsítvány. A tanúsítvány később alkalmas az eszköz kiépítése szolgáltatáshoz a regisztrációs folyamat esetén.

Egy tipikus **egyedi eszköz titkos kulcs (UDS)** 64 karakter hosszú karakterlánc. Egy minta UDS van, mint alatt:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Egyes két karakter lesz a biztonsági számítási hexadecimális érték. Ezért a fenti példa UDS hozzárendelve: "`0x19`, `0xe2`, `0x5a`, `0x25`, `0x9d`, `0x0c`, `0x2b`, `0xe0`, `0x3a`, `0x02`, `0xd4`, `0x16` , `0xc0`, `0x5c`, `0x48`, `0xcc`, `0xd0`, `0xcc`, `0x7d`, `0x17`, `0x43`, `0x45`, `0x8a`, `0xae`, `0x1c`, `0xb4`, `0x88`, `0xb0`, `0x74`, `0x99`, `0x3e`, `0xae`".

Menti az eszköz egyedi titkos a DevKit:

1. Nyissa meg a soros figyelő eszköz, például a Putty használatával című [konfigurációs mód használata](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) részleteiről.

2. A számítógéphez csatlakoztatott DevKit tartsa lenyomva a gombot A, majd leküldéses és kiadási adja meg a konfigurációs mód a Visszaállítás gombra. A képernyőn megjelennek a DevKit azonosítója és **"Konfiguráció"**.

3. A fenti hosszú minta UDS karakterlánc igénybe vehet, és módosítsa egy vagy több karakter más értékek között `0` és `f`. Ez a saját UDS lesz.

4. A soros figyelő ablakban írja be a **set_dps_uds [your_own_uds_value]** nyomja le az Enter billentyűt a mentse.
  > [!NOTE]
  > Például, ha az utolsó két karakterek módosításával beállíthatja a saját UDS `f`, meg kell adnia a parancs például **set_dps_uds 19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eff**.

5. A soros figyelő ablak bezárása nélkül nyomja le az alaphelyzetbe állítja a DevKit gombjára.

6. Jegyezze fel **DevKit MAC-cím** és **DevKit belsővezérlőprogram-verziónként** érték.
  ![Belső vezérlőprogram-verziója](./media/how-to-connect-mxchip-iot-devkit/firmware-version.png)

## <a name="generate-x509-certificate"></a>X.509 tanúsítvány jön létre

### <a name="windows"></a>Windows

1. Nyissa meg a Fájlkezelőt, és nyissa meg a mappa tartalmaz a DSP mintakód klónozhatók, és van egy **.build** mappa, a Keresés és a példány **DPS.ino.bin** és **DPS.ino.map** azt.
  ![Létrehozott fájlok](./media/how-to-connect-mxchip-iot-devkit/generated-files.png)
  > [!NOTE]
  > Ha módosította a `built.path` más mappába Arduino konfigurációját. Szeretné azokat a fájlokat a konfigurált mappában található.

2. Illessze be a két fájlt **eszközök** mappát ugyanazon a szinten az **.build** mappa.

3. Futtatás **dps_cert_gen.exe**, az útmutatást követve írja be a **UDS**, **MAC-cím** a DevKit a és a **belsővezérlőprogram-verziónként** létrehozásához az x.509 azonosítójú tanúsítványt.
  ![Run dps-cert-gen.exe](./media/how-to-connect-mxchip-iot-devkit/dps-cert-gen.png)

4. Sikeres létrehozás, láthatja a **.pem** tanúsítványt az azonos mappába kerül.

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>Eszközök beléptetése bejegyzés létrehozása az eszközkiépítési szolgáltatásban

1. Az Azure-portálon lépjen a létesítési szolgáltatás. Kattintson a **Regisztrációk kezelése** lehetőségre, és válassza az **Egyéni regisztrációk** lapot. ![Egyes regisztrációkat](./media/how-to-connect-mxchip-iot-devkit/individual-enrollments.png)

2. Kattintson a **Hozzáadás** parancsra.

3. A **mechanizmus**, válassza a **X.509**.
  ![Tanúsítvány feltöltése](./media/how-to-connect-mxchip-iot-devkit/upload-cert.png)

4. A **.pem vagy .cer kiterjesztésű tanúsítványfájlt**, töltse fel a **.pem** csak tanúsítvány.

5. A többi alapértelmezett hagyja, és kattintson **mentése**.

## <a name="start-the-devkit"></a>Indítsa el a DevKit

1. Indítsa el a Visual STUDIO Code, és nyissa meg a soros figyelő.

2. Nyomja meg a **alaphelyzetbe** a DevKit gombjára.

Meg kell jelennie a DevKit indítsa el a regisztrációt az eszköz kiépítése szolgáltatáshoz.

![Visual STUDIO Code kimeneti](./media/how-to-connect-mxchip-iot-devkit/vscode-output.png)

## <a name="verify-the-devkit-is-registered-on-iot-hub"></a>Ellenőrizze a DevKit regisztrálva van az IoT-központ

Egyszer az eszköz indítása, a következő műveleteket kell végrehajtani:

1. Az eszköz regisztrációs kérelmet küld az eszköz kiépítése szolgáltatáshoz.
2. Az eszköz kiépítése szolgáltatáshoz, amelyhez az eszköz válaszol regisztrációs kihívást küld vissza.
3. A sikeres regisztráció az eszköz kiépítése szolgáltatáshoz elküldi az eszközt az IoT hub URI, eszköz-Azonosítóját és a titkosított kulcs biztonsági.
4. Az IoT-központ ügyfélalkalmazás az eszköz ezután csatlakozik a központ.
5. A sikeres kapcsolat szeretne az elosztóhoz meg kell jelennie az eszközt az IoT hub eszköz Explorer jelennek meg.
  ![Regisztrált eszközök](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="change-device-id"></a>Módosítási eszköz azonosítója

Az alapértelmezett eszköz azonosítója regisztrálva az Azure IoT Hub **AZ3166**. Ha módosítani szeretné, hajtsa végre a [utasításokat itt](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémába ütközik, tekintse meg a [– gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) vagy kapcsolatba velünk a következő csatornák:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, így is automatikusan regisztrálja az IoT hubhoz nulla-Touch biztonságosan terjesztési pontok használatával feldarabolására HASZNÁLNAK, az eszköz beléptetése DevKit előkészítéséhez. Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Konfigurálja a terjesztési pontok globális végpontot eszközön
> * X.509 tanúsítvány létrehozásához használt egyedi eszköz titkos kulcs (UDS)
> * Egyes eszköz regisztrálása
> * Ellenőrizze, hogy az eszköz regisztrálva van

Továbblépés további az egyéb oktatóanyagok:

> [!div class="nextstepaction"]
> [Hozzon létre, és helyezze üzembe a szimulált eszköz](./quick-create-simulated-device.md)

