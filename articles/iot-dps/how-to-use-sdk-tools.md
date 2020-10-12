---
title: Az Azure IoT Hub Device Provisioning Service SDK-eszközök használata
description: Ez a dokumentum az Azure IoT Hub Device Provisioning Service (DPS) SDK-k fejlesztéséhez biztosított eszközöket tekinti át
author: wesmc7777
ms.author: wesmc
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 28d682a9ca698afb72d08b4d111562c725d46996
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530845"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Az SDK-k által biztosított eszközök használata a kiépítés fejlesztésének egyszerűsítése érdekében
A IoT Hub Device Provisioning Service a kiépítési folyamat leegyszerűsíti a biztonságos és skálázható módon történő [üzembe](about-iot-dps.md#provisioning-process) helyezést, valamint az igény szerinti kiépítés idejét.  A biztonsági igazolás X. 509 tanúsítvány vagy platformmegbízhatósági modul (TPM) formájában szükséges.  A Microsoft [más biztonsági partnerekkel](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) is együttműködik a IoT-telepítés biztonságossá tétele érdekében. A hardveres biztonsági követelmények megismerése nagyon nagy kihívást jelenthet a fejlesztők számára. Az Azure IoT-létesítési szolgáltatás SDK-k készletét biztosítjuk, így a fejlesztők egy kényelmi réteget használhatnak a kiépítési szolgáltatással kommunikáló ügyfelek írásához. Az SDK-k emellett mintákat is biztosítanak a gyakori forgatókönyvekhez, valamint a biztonsági igazolások fejlesztésének egyszerűsítésére szolgáló eszközkészletet.

## <a name="trusted-platform-module-tpm-simulator"></a>Platformmegbízhatósági modul (TPM) szimulátor
A [TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security) szabványos módon hivatkozhat a kulcsok biztonságos tárolására a platform hitelesítéséhez, vagy hivatkozhat a szabványt megvalósító modulokkal való interakcióhoz használt I/O-felületre. A TPM különálló hardverként, integrált hardverként, belső vezérlőprogram-vagy szoftver-alapúként is létezhetnek.  Éles környezetben a TPM különálló hardverként, integrált hardverként vagy belső vezérlőprogram-alapúként található az eszközön. A tesztelési fázisban egy szoftveres TPM-szimulátort biztosítanak a fejlesztők számára.  Ez a szimulátor jelenleg csak a Windows platformon való fejlesztéshez érhető el.

A TPM-szimulátor használatának lépései a következők:
1. [A fejlesztési környezet előkészítése](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) és a GitHub-tárház klónozása:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Navigáljon a TPM-szimulátor mappához a alatt ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/``` .
3. Simulator.exe futtatása a kiépítési eszközhöz tartozó ügyfélalkalmazás futtatása előtt.
4. A kiépítési folyamat során a szimulátor a háttérben fut a regisztrációs azonosító és a jóváhagyó kulcs beszerzéséhez.  Mindkét érték csak a Futtatás egy példánya esetében érvényes.

## <a name="x509-certificate-generator"></a>X. 509 tanúsítvány-generátor
Az [X. 509 tanúsítványokat](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) igazolási mechanizmusként használhatja a termelés méretezéséhez és az eszközök üzembe helyezésének egyszerűsítéséhez.  Az X. 509 tanúsítvány beszerzésének [több módja](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) is van:
* Éles környezetben ajánlott egy X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány vásárlása egy nyilvános legfelső szintű hitelesítésszolgáltatótól.
* Tesztelési környezetben létrehozhat egy X. 509 főtanúsítványt vagy X. 509 tanúsítványláncot a következő használatával:
    * OpenSSL: parancsfájlokat használhat a tanúsítvány generálásához:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell vagy bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Az eszköz Identity kompozíciós motor (DICE) emulátora: a DICE a TLS protokoll és az X. 509 ügyféltanúsítvány alapján történő titkosítási eszköz identitásához és igazolásához használható.  [További információ](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) az eszköz identitásáról a Dice szolgáltatásban.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>Az X. 509 tanúsítvány-generátor használata a DICE emulátorral
Az SDK-k egy X. 509 tanúsítvány-generátort biztosítanak a [Java SDK](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator)-ban található Dice Emulator használatával.  Ez a generátor több platformon működik.  A generált tanúsítvány más nyelveken is használható fejlesztéshez.

A DICE-emulátor jelenleg a főtanúsítványt, köztes tanúsítványt, egy levél tanúsítványát és a hozzá tartozó titkos kulcsot jeleníti meg.  Azonban a főtanúsítvány vagy köztes tanúsítvány nem használható külön levél-tanúsítvány aláírására.  Ha olyan csoportos beléptetési forgatókönyvet szeretne tesztelni, amelyben egy aláíró tanúsítvány több eszközön lévő levél-tanúsítványok aláírására szolgál, akkor az OpenSSL-t használhatja tanúsítványok láncának létrehozásához.

X. 509 tanúsítvány generálása a következő generátor használatával:
1. [A fejlesztési környezet előkészítése](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) és a GitHub-tárház klónozása:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Módosítsa a gyökeret az Azure-IOT-SDK-Java értékre.
3. ```mvn install -DskipTests=true```Az összes szükséges csomag letöltéséhez és az SDK fordításához futtassa a parancsot.
4. Navigáljon az X. 509 tanúsítvány-generátor gyökeréhez a alkalmazásban ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator``` .
5. Build ```mvn clean install```
6. Futtassa az eszközt az alábbi parancsokkal:
   ```
   cd target
   java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
   ```
7. Amikor a rendszer erre kéri, opcionálisan megadhat egy _köznapi nevet_ a tanúsítvány számára.
8. Az eszköz helyileg létrehoz egy **ügyfél-tanúsítványt**, **az ügyfél tanúsítványának titkos kulcsát**, a **köztes tanúsítványt**és a **főtanúsítványt**.

Az **ügyfél** tanúsítványa a levél tanúsítványa az eszközön.  Az **ügyfél tanúsítványa** és a társított **ügyféltanúsítvány titkos kulcsa** szükséges az eszköz ügyfelében. Attól függően, hogy milyen nyelvet választ, az ügyfélalkalmazás ezt a mechanizmust más lehet.  További információért lásd a szimulált eszköz X. 509 használatával történő létrehozásával [kapcsolatos rövid](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) útmutatót.

A főtanúsítvány vagy a köztes tanúsítvány használatával létrehozhat egy regisztrációs csoportot vagy egyéni beléptetést [programozott](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) módon vagy a [portálon](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

## <a name="next-steps"></a>Következő lépések
* Fejlesztés az Azure IoT Hub és az Azure-hoz készült [Azure IOT SDK]( https://github.com/Azure/azure-iot-sdks) -val IoT hub Device Provisioning Service
