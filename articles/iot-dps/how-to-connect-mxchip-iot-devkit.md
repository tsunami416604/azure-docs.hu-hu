---
title: Az Azure IoT Hub Device Provisioning Service automatikus kiépítés használata az MXChip IoT DevKit regisztrálása az IoT Hub |} A Microsoft Docs
description: Hogyan használható az Azure IoT Hub Device Provisioning Service automatikus kiépítés az MXChip IoT DevKit regisztrálása az IoT hubbal.
author: liydu
ms.author: liydu
ms.date: 12/18/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: 513d4e51ced798f5fe49e2e1e59fcc8ec02d9c2c
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55699193"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Az MXChip IoT DevKit IoT Hub-regisztráció az Azure IoT Hub Device Provisioning Service automatikus kiépítés használatával

Ez a cikk azt ismerteti, hogyan használható az Azure IoT Hub Device Provisioning Service [automatikus kiépítés](concepts-auto-provisioning.md), regisztrálja az MXChip IoT fejlesztői készlet az Azure IoT Hub szolgáltatással. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

* A Device Provisioning szolgáltatás globális végpontja konfigurálja az eszközön.
* Az eszköz egyedi titkos kulcsot (frissítési) használatával hozzon létre egy X.509 tanúsítvány.
* Egy adott eszköz regisztrálásához.
* Győződjön meg arról, hogy az eszköz regisztrálva van-e.

A [MXChip IoT DevKit](https://aka.ms/iot-devkit) egy teljes körű Arduino-kompatibilis tábla a gazdag perifériák és érzékelők van. A használatával is fejleszthet [Azure IoT-eszköz Workbench](https://aka.ms/iot-workbench) vagy [Azure IoT-eszközök](https://aka.ms/azure-iot-tools) bővítőcsomagjának Visual Studio Code-ban. A fejlesztői készlet együttműködik a egyre növekvő [projektek katalógus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) is, amelyek kihasználják az Azure-szolgáltatások prototípusként Internet of Things (IoT) megoldását.

## <a name="before-you-begin"></a>Előkészületek

Ez az oktatóanyag lépéseinek végrehajtásához először a következő feladatokat végezheti el:

* Készítse elő a DevKit a lépéseket követve [IoT DevKit AZ3166 csatlakoztatása az Azure IoT hubba a felhőben](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md).
* Frissítés a legújabb belső vezérlőprogramjának (1.3.0 vagy újabb) az a [frissítés DevKit belső vezérlőprogram](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) oktatóanyag.
* Létrehozzák és összekapcsolják az IoT Hub Device Provisioning service-példánnyal a lépéseket követve [állítsa be az IoT Hub Device Provisioning Service az Azure Portallal](/azure/iot-dps/quick-setup-auto-provision).

## <a name="open-sample-project"></a>Nyissa meg a mintaprojektet

1. Győződjön meg arról, hogy az IoT DevKit **nincs csatlakoztatva** a számítógépre. Először indítsa el a VS Code, és a fejlesztői készlet csatlakoztatása a számítógéphez.

1. Kattintson a `F1` a parancskatalógus megnyitásához, írja be, és válassza ki **Azure IoT-eszköz Workbench: Példák megnyitása...** . Válassza ki **IoT DevKit** , tábla.

1. Az IoT Workbench példák oldalán található **Eszközregisztráció DPS az** kattintson **nyílt minta**. Ezután kiválasztja a mintakód letöltése az alapértelmezett elérési utat.
    ![Nyissa meg a minta](media/how-to-connect-mxchip-iot-devkit/open-sample.png)

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>Az eszköz biztonsági storage eszköz egyedi titkos kulcs mentése

Automatikus kiépítés konfigurálható egy eszközön, az eszköz alapján [igazolási mechanizmus](concepts-security.md#attestation-mechanism). Az MXChip IoT DevKit használja a [eszközt identitás összeállítás motor](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) származó a [Trusted Computing Group](https://trustedcomputinggroup.org). A **egyedi eszköz titkos kulcs** (frissítési) menti egy STSAFE biztonsági lapkával ([STSAFE-A100](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)) meg a fejlesztői készlet létrehozásához használja az eszköz egyedi a [X.509-tanúsítvány](concepts-security.md#x509-certificates). A tanúsítványt később a beléptetési folyamat a Device Provisioning service-ben, és a futásidőben a regisztráció során használatos.

Egy tipikus frissítési Tartománnyal 64 karakterből álló karakterláncnak, az alábbi mintában látható módon:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Egy frissítési Tartománnyal mentéséhez kattintson a fejlesztői készlet:

1. A VS Code-ban kattintson az állapotsor választhatja ki a fejlesztői készlet a COM-portot.
  ![Válassza ki a COM-Port](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. A fejlesztői készlet, tartsa lenyomva a **gombra A**, push és kiadása a **alaphelyzetbe** gombra, majd engedje **gombra A**. A fejlesztői készlet konfigurációs módra vált.

1. Kattintson a `F1` a parancskatalógus megnyitásához, írja be, és válassza ki **Azure IoT-eszköz Workbench: -Beállítások konfigurálása... > konfigurációs eszköz egyedi karakterlánc (frissítési)**.
  ![Frissítési Tartománnyal konfigurálása](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. Jegyezze fel a létrehozott karakterlánc frissítési Tartománnyal. Szüksége lesz rá az X.509-tanúsítvány előállításához. Nyomja le az `Enter`.
  ![Másolja ki a frissítési Tartománnyal](media/how-to-connect-mxchip-iot-devkit/copy-uds.png)

1. Ellenőrizze, hogy frissítési Tartománnyal sikeresen konfigurálva lett a STSAFE meg az értesítést a.
  ![Frissítési Tartománnyal sikeres konfigurálása](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> Másik lehetőségként konfigurálhatja frissítési Tartománnyal soros porton keresztül segédprogramokkal, például a putty-kapcsolaton keresztül. Hajtsa végre a [konfigurációs mód használata](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) ennek a végrehajtására.

## <a name="update-the-global-device-endpoint-and-id-scope"></a>Frissítse a globális eszköz végpontja és az azonosító hatóköre

Az eszköz kód, meg kell adnia a [Eszközregisztrációs végpont](/azure/iot-dps/concepts-service#device-provisioning-endpoint) és azonosító hatóköre annak biztosítása érdekében a bérlők elkülönítését.

1. Az Azure Portalon válassza ki a **áttekintése** ablaktábláján a Device Provisioning service és jegyezze fel a **globális eszköz végpontja** és **azonosító hatóköre** értékeket.
  ![Device Provisioning szolgáltatás globális végpontja és azonosító hatóköre](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. Nyissa meg **DeKitDPS.ino**. Keresés és csere `[Global Device Endpoint]` és `[ID Scope]` le feljegyzett értékekkel.
  ![Eszköz kiépítési szolgáltatás végpontja](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. Töltse ki a `registrationId` változó a kódban. Csak alfanumerikus karakterek, kisbetűs, és a kötőjel kombináció, amely legfeljebb 128 karakter megengedett. Az érték is lejegyzett.
  ![Regisztrációs azonosító](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. Kattintson a `F1`, írja be, és válassza ki **Azure IoT-eszköz Workbench: Töltse fel az eszköz kód**. Elindítja a kódja lefordításának és a kód feltöltése a fejlesztői készlet.
  ![Eszköz feltöltése](media/how-to-connect-mxchip-iot-devkit/device-upload.png)

## <a name="generate-x509-certificate"></a>X.509-tanúsítvány létrehozása

A [igazolási mechanizmus](/azure/iot-dps/concepts-device#attestation-mechanism) használják ezt a mintát az X.509-tanúsítvány. A segédprogram használata a létrehozáshoz szüksége.

> [!NOTE]
> Az X.509-tanúsítványkészítőt csak támogatja a Windows most.

1. A VS Code-ban kattintson `F1`, írja be, és válassza ki **új terminál nyissa meg a** , nyissa meg a terminálablakot.

1. Futtatás `dps_cert_gen.exe` a `tool` mappát.

1. A lefordított bináris fájl helye legyen `..\.build\DevKitDPS`. Illessze be a **frissítési Tartománnyal** és **registrationId** csak lejegyzett. 
  ![X.509 készítése](media/how-to-connect-mxchip-iot-devkit/gen-x509.png)

1. A `.pem` X.509-tanúsítványt hoz létre ugyanabban a mappában.
  ![X.509-fájl](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>Eszközregisztrációs bejegyzés létrehozása

1. Az Azure Portalon nyissa meg az eszköz kiépítése szolgáltatást, navigáljon a regisztrációk szakasz kezelése, és kattintson **egyéni regisztráció hozzáadása**.
  ![Egyéni regisztráció hozzáadása](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png)

1. Fájl ikon mellett kattintson **elsődleges tanúsítvány .pem or .cer fájlja** feltölteni a `.pem` fájl jön létre.
  ![Töltse fel a .pem](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>Ellenőrizze, hogy a fejlesztői készlet regisztrálva van az Azure IoT Hub szolgáltatással

Nyomja le az **alaphelyzetbe** a DevKit gombjára. Megtekintheti az **DPS csatlakoztatott!** a fejlesztői készlet képernyőn. Miután az eszköz újraindul, a következő műveleteket hajtja végre:

1. Az eszköz regisztrációs kérést küld a Device Provisioning Service-nek.
1. A Device Provisioning service visszaküld egy regisztrációs kihívást, amelyre az eszköz válaszol.
1. Sikeres regisztráció esetén a Device Provisioning service visszaküldi az IoT Hub URI-t, az Eszközazonosítót és a titkosított kulcs az eszközhöz.
1. Az IoT Hub-ügyfélalkalmazás az eszköz csatlakozik a hubhoz.
1. Sikeres csatlakozás a hubhoz tekintse meg az eszközt az IoT Hub Device Explorer jelennek meg.
  ![Eszköz regisztrálása](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémákat tapasztal, tekintse meg az Iot DevKit [– gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/), vagy keresse fel a következő támogatási csatornákat:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan biztonságosan a Device Provisioning Service-eszközök regisztrálása az eszköz identitása összeállítás motor használatával úgy, hogy az eszköz automatikusan regisztrálhat az Azure IoT Hub szolgáltatással. 

Az összegzés megtanulta, hogyan lehet:

> [!div class="checklist"]
> * A Device Provisioning szolgáltatás globális végpontja konfigurálja az eszközön.
> * Eszköz egyedi titkos kulcs használatával hozzon létre egy X.509 tanúsítvány.
> * Egy adott eszköz regisztrálásához.
> * Győződjön meg arról, hogy az eszköz regisztrálva van-e.

Ismerje meg, hogyan [egy szimulált eszköz létrehozásával és kiépítésével](./quick-create-simulated-device.md).

