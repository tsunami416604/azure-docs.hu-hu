---
title: Az Azure IoT Hub-eszközkiépítési szolgáltatás SDK-eszközeinek használata
description: Ez a dokumentum áttekinti az Azure IoT Hub-eszközkiépítési szolgáltatás (DPS) SDK-kban fejlesztési célokra biztosított eszközöket
author: robinsh
ms.author: robinsh
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: b817b3cfe47ed08cae9e7e0b1c2c24363f2ccfed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271524"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Az SDK-kban található eszközök használata a kiépítés fejlesztésének egyszerűsítése érdekében
Az IoT Hub-eszközkiépítési szolgáltatás leegyszerűsíti a kiépítési folyamat nulla érintéses, just-in-time [automatikus kiépítése](concepts-auto-provisioning.md) biztonságos és méretezhető módon.  X.509 tanúsítvány vagy platformmegbízhatósági modul (TPM) formájában kiállított biztonsági tanúsítvány szükséges.  A Microsoft más [biztonsági hardverpartnerekkel](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) is együttműködik az IoT telepítésének biztonságossá tétele iránti bizalom növelése érdekében. A hardverbiztonsági követelmény megértése nagy kihívást jelenthet a fejlesztők számára. Az Azure IoT-létesítési szolgáltatás SDK-k egy készletét biztosítjuk, hogy a fejlesztők egy kényelmi réteget használhassanak a kiépítési szolgáltatással beszélő ügyfelek írásához. Az SDK-k is biztosít mintákat a közös forgatókönyvek, valamint egy sor eszközt, hogy egyszerűsítse a biztonsági tanúsítvány fejlesztés alatt.

## <a name="trusted-platform-module-tpm-simulator"></a>Platformmegbízhatósági modul (TPM) szimulátor
[A TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security) hivatkozhat a kulcsok biztonságos tárolására vonatkozó szabványra a platform hitelesítéséhez, vagy hivatkozhat a szabványt megvalósító modulokkal való interakcióhoz használt I/O interfészre. A TPM-ek különálló hardverként, integrált hardverként, belső vezérlőprogram-alapúként vagy szoftveralapúként létezhetnek.  Éles környezetben a TPM az eszközön található, akár különálló hardverként, integrált hardverként vagy belső vezérlőprogram-alapúként. A tesztelési fázisban egy szoftveralapú TPM-szimulátort biztosítunk a fejlesztőknek.  Ez a szimulátor egyelőre csak Windows platformon fejlesztésre alkalmas.

A TPM-szimulátor használatának lépései a következők:
1. [Készítse elő a fejlesztői környezetet,](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) és klónozza a GitHub-tárházat:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Nyissa meg a TPM-szimulátor mappát a csoportban. ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/```
3. Futtassa a Simulator.exe programot, mielőtt bármilyen ügyfélalkalmazást futtatna az eszköz létesítéséhez.
4. Hagyja, hogy a szimulátor fut a háttérben a kiépítési folyamat során a regisztrációs azonosító és az ellenőrzőkulcs beszerzéséhez.  Mindkét érték csak a futtatás egy példányára érvényes.

## <a name="x509-certificate-generator"></a>X.509 tanúsítványgenerátor
[Az X.509 tanúsítványok](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) igazolási mechanizmusként használhatók az éleskörnyezet méretezéséhez és az eszközkiépítés egyszerűsítéséhez.  Az X.509 tanúsítvány beszerzésének [számos módja](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) van:
* Éles környezetben azt javasoljuk, hogy vásároljon egy X.509 hitelesítésszolgáltatói tanúsítványt egy nyilvános legfelső szintű hitelesítésszolgáltatótól.
* Tesztelési környezetben x.509-es főtanúsítványt vagy X.509 tanúsítványláncot hozhat létre a következő kkel:
    * OpenSSL: Parancsfájlok használhatók a tanúsítványok létrehozásához:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell vagy Bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Device Identity Composition Engine (DICE) Emulator: DICE használható kriptográfiai eszköz identitás és tanúsítvány alapján TLS protokoll és X.509 ügyfél tanúsítványokat.  [További információ](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) az eszközidentitásról a DICE segítségével.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>Az X.509 tanúsítványgenerátor használata DICE emulátorral
Az SDK-k egy X.509 tanúsítványgenerátort biztosítanak a DICE emulátorral, amely a [Java SDK-ban](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator)található.  Ez a generátor működik cross-platform.  A létrehozott tanúsítvány más nyelveken is használható fejlesztésre.

Jelenleg, míg a DICE emulátor kimeneti főtanúsítvány, köztes tanúsítvány, levél tanúsítvány, és a kapcsolódó személyes kulcs.  A főtanúsítvány vagy a köztes tanúsítvány azonban nem használható külön levéltanúsítvány aláírására.  Ha tesztelni kívánja a csoportigénylési forgatókönyvet, amelyben egy aláíró tanúsítványt használ több eszköz levéltanúsítványainak aláírására, az OpenSSL segítségével tanúsítványok láncolatát hozhat létre.

X.509 tanúsítvány létrehozása ezzel a generátorral:
1. [Készítse elő a fejlesztői környezetet,](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) és klónozza a GitHub-tárházat:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Módosítsa a gyökeret azúr-iot-sdk-java-ra.
3. Futtassa ```mvn install -DskipTests=true``` az összes szükséges csomag letöltéséhez és az SDK fordításához
4. Keresse meg az X.509 tanúsítványgenerátor gyökérét a ban. ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator```
5. Építsd meg```mvn clean install```
6. Futtassa az eszközt az alábbi parancsokkal:
   ```
   cd target
   java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
   ```
7. Amikor a rendszer erre kéri, opcionálisan megadhat egy _köznapi nevet_ a tanúsítvány számára.
8. Az eszköz helyileg létrehoz egy **ügyféltanúsítványt**, az **ügyféltanúsítvány titkos kulcsát,** **a köztes tanúsítványt**és a **gyökértanúsítványt.**

**Az ügyféltanúsítvány** az eszközön lévő levéltanúsítvány.  **Az ügyféltanúsítványra** és a kapcsolódó **ügyféltanúsítvány titkos kulcsra** van szükség az eszközügyfélben. Attól függően, hogy milyen nyelvet választ, a mechanizmus, hogy ezt az ügyfélalkalmazás eltérő lehet.  További információt az X.509-es használatával szimulált eszköz létrehozásáról szóló [rövid útmutatók](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) ban talál.

A főtanúsítvány vagy a köztes tanúsítvány [programozott módon](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) vagy a [portál](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments)használatával lehet regisztrációs csoportot vagy egyéni igénylést létrehozni.

## <a name="next-steps"></a>További lépések
* Fejlesztés az Azure IoT Hubhoz és az Azure IoT Hub eszközkiépítési szolgáltatásához tervezett [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) használatával
