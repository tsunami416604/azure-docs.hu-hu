---
title: Az Azure IoT Hub Device Provisioning Service SDK ismertetett eszközök segítségével leegyszerűsítheti a fejlesztést
description: Ez a dokumentum áttekinti a fejlesztés Azure IoT Hub Device Provisioning Service SDKs található eszközök
author: yzhong94
ms.author: yizhon
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.openlocfilehash: eb5eecaca65cc8394bcc12fc5a475cf5e762f1c9
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55729965"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Az SDK-k ismertetett eszközök segítségével leegyszerűsítheti a fejlesztést kialakítási útmutató
Az IoT Hub Device Provisioning Service leegyszerűsíti a kiépítési folyamat beavatkozás nélküli, just-in-time [automatikus kiépítés](concepts-auto-provisioning.md) biztonságos és méretezhető módon.  Biztonsági tanúsítvány X.509-tanúsítvány vagy a platformmegbízhatósági modul (TPM) formájában szükség.  A Microsoft emellett partneri együttműködés a [más biztonsági a hardvergyártó partnerektől](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) magabiztosan az IoT-környezet biztonságossá tétele javítása érdekében. A hardveres biztonsági követelmények ismertetése meglehetősen nehéz fejlesztőknek lehet. Az Azure IoT Provisioning Service SDK-k készlete biztosítják, hogy a fejlesztők használhatják a kényelem réteg írása ügyfelek kommunikálhatnak a kiépítési szolgáltatáshoz. Az SDK-k is adja meg a minták gyakori forgatókönyvek, valamint a biztonsági igazolási fejlesztés egyszerűsítése eszközöket.

## <a name="trusted-platform-module-tpm-simulator"></a>Platformmegbízhatósági modul (TPM) megbízható szimulátor
[A TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security) olvassa el a platform hitelesítéséhez kulcsok biztonságos tárolásához egy standard, vagy olvassa el a i/o-felület, a modulok, a standard végrehajtási folytatott kommunikációhoz használható. TPM diszkrét hardverként integrált hardverek, a belső vezérlőprogram-alapú vagy szoftveres létezhet.  Éles környezetben TPM található az eszközön, vagy különálló hardver, integrált hardver, vagy a belső vezérlőprogram-alapú. A tesztelési folyamatba szoftveralapú TPM-szimulátor nyújtja a fejlesztők számára.  A szimulátor csak akkor használható, mostantól a Windows platform fejlesztéséhez.

A TPM-szimulátor használatához vezető lépések a következők:
1. [A fejlesztési környezet előkészítését](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) , és klónozza a GitHub-adattárát:
```
git clone https://github.com/Azure/azure-iot-sdk-java.git
```
2. Keresse meg a TPM-szimulátor mappáját alatt ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/```.
3. Futtassa a Simulator.exe bármelyik ügyfélalkalmazás üzembe helyezési eszköz futtatása előtt.
4. Lehetővé teszik a szimulátor fut a háttérben a regisztrációs azonosító és az ellenőrzőkulcsot az üzembe helyezési folyamat során.  Mindkét értékei csak a Futtatás egyetlen példánya esetében érvényes.

## <a name="x509-certificate-generator"></a>X.509-tanúsítványkészítőt
[X.509-tanúsítványokat](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) éles és egyszerűsítheti a device provisioning az igazolási mechanizmusként használható.  Nincsenek [többféleképpen](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) egy X.509-tanúsítvány beszerzése:
* Éles környezet megvásárlását javasoljuk egy nyilvános legfelső szintű hitelesítésszolgáltatótól X.509 Hitelesítésszolgáltatói tanúsítvány.
* A tesztelési környezethez, létrehozhat egy X.509 legfelső szintű tanúsítványt vagy X.509-tanúsítvány láncában használatával:
    * OpenSSL: A tanúsítvány létrehozása a parancsfájlokat használhat:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell vagy a Bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Eszköz identitása összeállítás Engine (DICE) emulátort: DICE titkosítási eszközidentitás használható, és a TLS protokoll- és X.509 ügyféltanúsítványok alapján igazolás.  [Ismerje meg,](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) további információk a DICE-eszközidentitást.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>X.509-tanúsítványkészítőt használatával DICE emulátorral
Az SDK-k, adjon meg egy X.509-tanúsítványkészítőt DICE emulátort található a [Java SDK](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator).  A generátor többplatformos működik.  Az előállított tanúsítvány fejlesztés más nyelveken is használható.

Jelenleg, miközben a DICE-emulátor kimenete egy főtanúsítványt, egy közbenső tanúsítvány, egy levéltanúsítványt és a hozzá tartozó titkos kulcsot.  Azonban a tanúsítvány legfelső szintű vagy köztes tanúsítványt nem használható egy külön levél tanúsítvány aláírására.  Ha szeretne csoport regisztrációs forgatókönyv teszteléséhez ahol egy aláíró tanúsítványt, több eszközre a levél tanúsítványok aláírásához használt, OpenSSL segítségével előállít egy tanúsítványláncában szereplő tanúsítványok egyikével.

A generátor használatával X.509-tanúsítvány létrehozásához:
1. [A fejlesztési környezet előkészítését](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) , és klónozza a GitHub-adattárát:
```
git clone https://github.com/Azure/azure-iot-sdk-java.git
```
2. Módosítsa a legfelső szintű azure-iot-sdk-java-re.
3. Futtatás ```mvn install -DskipTests=true``` töltse le az összes szükséges csomagokat és fordítsa le az SDK-val
4. Az X.509-Tanúsítványkészítőt esetében keresse meg a legfelső szintű ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator```.
5. Fejlesztés ```mvn clean install```
6. Futtassa az eszközt az alábbi parancsokkal:
```
cd target
java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
```
7. Amikor a rendszer erre kéri, opcionálisan megadhat egy _köznapi nevet_ a tanúsítvány számára.
8. Az eszköz helyi hoz létre egy **ügyféltanúsítvány**, a **ügyféltanúsítvány titkos kulcsa**, **köztes tanúsítvány**, és a **főtanúsítványt**.

**Ügyféltanúsítvány** levél a tanúsítvány az eszközön.  **Ügyféltanúsítvány** és a társított **ügyféltanúsítvány titkos kulcsa** szükségesek az eszközügyfélen. Attól függően, melyik nyelvet választja a mechanizmus helyezni ezt az ügyfélalkalmazás eltérő lehet.  További információkért lásd: a [útmutatóink](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) létrehozáskor szimulált eszközzel X.509 további információt.

A legfelső szintű tanúsítvány vagy a köztes segítségével létrehozhat egy regisztrációs csoportnak, vagy az egyéni regisztráció [programozott módon](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) használatával vagy a [portál](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

## <a name="next-steps"></a>További lépések
* Fejlesztés használatával a [Azure IoT SDK-val]( https://github.com/Azure/azure-iot-sdks) az Azure IoT Hub és az Azure IoT Hub Device Provisioning Service
