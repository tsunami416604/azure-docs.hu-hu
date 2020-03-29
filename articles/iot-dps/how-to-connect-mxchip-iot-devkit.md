---
title: Az Azure IoT Hub-eszközkiépítési szolgáltatás automatikus kiépítésének használata az MXChip IoT DevKit regisztrálásához az IoT Hubbal | Microsoft dokumentumok
description: Az Azure IoT Hub-eszközkiépítési szolgáltatás (DPS) automatikus kiépítésének használata az MXChip IoT DevKit regisztrálásához az IoT Hubsegítségével.
author: liydu
ms.author: liydu
ms.date: 06/25/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: f05e92f0452b1cfff23e2094354203fd7eaea48b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975652"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Az Azure IoT Hub-eszközkiépítési szolgáltatás automatikus kiépítésével regisztrálhatja az MXChip IoT DevKit-et az IoT Hubbal

Ez a cikk ismerteti, hogyan azure IoT Hub-eszközkiépítési szolgáltatás [automatikus kiépítése,](concepts-auto-provisioning.md)az MXChip IoT DevKit regisztrálásához az Azure IoT Hub használatával. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

* Konfigurálja az eszközkiépítési szolgáltatás globális végpontját egy eszközön.
* X.509-es tanúsítvány létrehozásához használjon egyedi eszköztitkos kulcsot (UDS).
* Egyéni eszköz regisztrálása.
* Ellenőrizze, hogy az eszköz regisztrálva van-e.

Az [MXChip IoT DevKit](https://aka.ms/iot-devkit) egy all-in-one Arduino-kompatibilis alaplap gazdag perifériákkal és érzékelőkkel. Az [Azure IoT Device Workbench](https://aka.ms/iot-workbench) vagy [az Azure IoT Tools](https://aka.ms/azure-iot-tools) bővítménycsomag gal fejleszthet a Visual Studio-kódban. A DevKit egy növekvő [projektkatalógust](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) kínál, amely az Azure-szolgáltatások előnyeit kihasználó Dolgok internetes (IoT) prototípus-megoldásait irányítja.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag lépéseinek végrehajtásához először hajtsa végre a következő feladatokat:

* Konfigurálja a DevKit Wi-Fi-jét, és készítse elő a fejlesztői környezetet az [IoT DevKit AZ3166 csatlakoztatása az Azure IoT Hubhoz a felhőben](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started#prepare-the-development-environment)című szakasz "A fejlesztői környezet előkészítése" című szakaszának követésével.
* Frissítsen a legújabb firmware-re (1.3.0 vagy újabb) a [DevKit-verzió belső vezérlőprogramjának frissítése](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) segítségével.
* Hozzon létre és csatolja az IoT Hub egy eszközkiépítési szolgáltatáspéldány az [IoT Hub-eszközkiépítési szolgáltatás beállítása az Azure Portalon](/azure/iot-dps/quick-setup-auto-provision)című lépéseit követve.

## <a name="open-sample-project"></a>Mintaprojekt megnyitása

1. Győződjön meg arról, hogy az IoT DevKit **nincs csatlakoztatva** a számítógéphez. Először indítsa el a VS-kódot, majd csatlakoztassa a DevKit-et a számítógéphez.

1. Kattintson ide `F1` a parancspaletta megnyitásához, írja be és válassza az **Azure IoT Device Workbench: Open Examples...** lehetőséget. Ezután válassza **az IoT DevKit** fórumon.

1. Az IoT Workbench-példák lapon keresse meg az **Eszközregisztráció t a DPS-szel,** és kattintson **a Minta megnyitása gombra.** Ezután kiválasztja a mintakód letöltéséhez vezető alapértelmezett elérési utat.
    ![Nyitott minta](media/how-to-connect-mxchip-iot-devkit/open-sample.png)

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>Egyedi eszköztitok mentése az eszköz biztonsági tárolóján

Az automatikus üzembe építés az eszközön konfigurálható az eszköz [igazolási mechanizmusa](concepts-security.md#attestation-mechanism)alapján. Az MXChip IoT DevKit a [Trusted Computing Group](https://trustedcomputinggroup.org) [eszközazonosító-összetételi motorját](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) használja. A DevKit-en található STSAFE biztonsági chipben[(STSAFE-A100)](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)mentett **egyedi eszköztitkos** kulcsot (UDS) használják az eszköz egyedi [X.509 tanúsítványának](concepts-security.md#x509-certificates)létrehozásához. A tanúsítvány később az eszközkiépítési szolgáltatás ban és futásidőben történő regisztráció során a regisztrációs folyamathoz használatos.

Egy tipikus UDS egy 64 karakterből álló karakterlánc, amint az a következő mintában látható:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

UDS mentése a DevKiten:

1. A VS-kód ban kattintson az állapotsorra a DevKit COM-portjának kiválasztásához.
  ![COM-port kiválasztása](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. A DevKit en tartsa lenyomva **az A gombot,** nyomja meg és engedje fel a **reset** gombot, majd engedje fel az **A gombot.** A DevKit konfigurációs módba lép.

1. Kattintson ide `F1` a parancspaletta megnyitásához, írja be és válassza az **Azure IoT-eszköz munkapadját: Eszközbeállítások konfigurálása... > Config Unique Device String (UDS) lehetőséget.**
  ![UDS konfigurálása](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. Jegyezze fel a létrehozott UDS-karakterláncot. Szüksége lesz rá az X.509 tanúsítvány létrehozásához. Ezután `Enter`nyomja meg a gombot.
  ![UDS másolása](media/how-to-connect-mxchip-iot-devkit/copy-uds.png)

1. Erősítse meg az értesítésből, hogy az UDS sikeresen konfigurálva van az STSAFE-en.
  ![UdS-siker konfigurálása](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> Azt is megteheti, hogy az UDS-t soros porton keresztül konfigurálja olyan segédprogramok használatával, mint a Putty. Ehhez kövesse [a Konfigurációs mód használata](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) parancsot.

## <a name="update-the-global-device-endpoint-and-id-scope"></a>A globális eszközvégpont és -azonosító hatókör frissítése

Az eszközkódban meg kell adnia az [eszközkiépítési végpontot](/azure/iot-dps/concepts-service#device-provisioning-endpoint) és az azonosító hatókört a bérlői elkülönítés biztosításához.

1. Az Azure Portalon válassza ki az eszközkiépítési szolgáltatás **áttekintő** ablaktábláját, és jegyezze fel a **globális eszközvégpontés** **az azonosító hatókör értékeit.**
  ![Az eszközkiépítési szolgáltatás globális végpont- és azonosítóhatóköre](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. Nyissa meg **a DevKitDPS.ino**. Keresse meg `[Global Device Endpoint]` `[ID Scope]` és cserélje ki, és az értékeket, amit csak megjegyezte le.
  ![Eszközkiépítési szolgáltatás végpontja](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. Töltse `registrationId` ki a változót a kódban. Csak alfanumerikus, kisbetűs és kötőjelkombináció megengedett, legfeljebb 128 karakterrel. Is megjegyezte, le az értéket.
  ![Regisztrációs azonosító](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. Kattintson a gombra, `F1`írja be, és válassza az Azure **IoT-eszköz munkapadját: Eszközkód feltöltése**. Elkezdi a kód összeállítását és feltöltését a DevKitbe.
  ![Eszköz feltöltése](media/how-to-connect-mxchip-iot-devkit/device-upload.png)

## <a name="generate-x509-certificate"></a>X.509 tanúsítvány létrehozása

A minta által használt [igazolási mechanizmus](/azure/iot-dps/concepts-device#attestation-mechanism) az X.509 tanúsítvány. A létrehozáshoz segédprogramot kell használnia.

1. A VS-kód `F1`csoportban kattintson a gombra, és válassza **az Új terminál megnyitása** lehetőséget a terminálablak megnyitásához.

1. Fuss `dps_cert_gen.exe` `tool` a mappában.

1. Adja meg a lefordított `..\.build\DevKitDPS`bináris fájl helyét a néven. Ezután illessze be az **UDS-t** és **a registrationId-et,** amit az imént észlelt. 
  ![X.509 létrehozása](media/how-to-connect-mxchip-iot-devkit/gen-x509.png)

1. Egy `.pem` X.509-es tanúsítvány ugyanabban a mappában jön létre.
  ![X.509 fájl](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>Eszközregisztrációs bejegyzés létrehozása

1. Az Azure Portalon nyissa meg az Eszközkiépítési szolgáltatást, keresse meg a Regisztrációk kezelése szakaszt, és kattintson **az Egyéni regisztráció hozzáadása gombra.**
  ![Egyéni regisztráció hozzáadása](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png)

1. A létrehozott fájl feltöltéséhez kattintson az `.pem` Elsődleges tanúsítvány **.pem vagy .cer fájl** melletti fájlikonra.
  ![.pem feltöltése](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>Ellenőrizze, hogy a DevKit regisztrálva van-e az Azure IoT Hubban

Nyomja **meg** a Visszaállítás gombot a DevKit.Nyomja meg a Reset gombot a DevKit. Önnek kellene lát **DPS Összekapcsolt!** a DevKit képernyőn. Az eszköz újraindítása után a következő műveletek rekednek:

1. Az eszköz regisztrációs kérést küld a Device Provisioning Service-nek.
1. Az Eszközkiépítési szolgáltatás visszaküld egy regisztrációs kihívást, amelyre az eszköz válaszol.
1. A sikeres regisztráció során az eszközkiépítési szolgáltatás elküldi az IoT Hub URI-t, az eszközazonosítót és a titkosított kulcsot az eszközre.
1. Az eszköz IoT Hub-ügyfélalkalmazása csatlakozik a hubhoz.
1. A hubhoz való sikeres csatlakozáskor megjelenik az eszköz az IoT Hub Eszközkezelőben.
  ![Regisztrált eszköz](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>Problémák és visszajelzések

Ha problémákat tapasztal, olvassa el az Iot DevKit [GYIK-et,](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)vagy forduljon a következő csatornákhoz támogatásért:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta biztonságosan regisztrálni az eszközt az eszközkiépítési szolgáltatáshoz az eszközidentitás-összetételi motor használatával, hogy az eszköz automatikusan regisztrálhasson az Azure IoT Hubhasználatával. 

Összefoglalva, megtanulta, hogyan:

> [!div class="checklist"]
> * Konfigurálja az eszközkiépítési szolgáltatás globális végpontját egy eszközön.
> * Az X.509-es tanúsítvány létrehozásához használjon egyedi eszköztitkos kulcsot.
> * Egyéni eszköz regisztrálása.
> * Ellenőrizze, hogy az eszköz regisztrálva van-e.

Ismerje meg, hogyan [hozhat létre és építhet ki szimulált eszközt.](./quick-create-simulated-device.md)

