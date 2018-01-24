---
title: "Az Azure IoT Hub eszköz kiépítése szolgáltatáshoz SDK-k a biztosított eszközök segítségével egyszerűbbé teheti a fejlesztési"
description: "Ez a dokumentum áttekinti a fejlesztési Azure IoT Hub eszköz kiépítése szolgáltatáshoz SDK-k a megadott eszközök"
services: iot-dps
keywords: 
author: yzhong94
ms.author: yizhon
ms.date: 01/18/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 76c6f64dea202f661691fafaa78a6d77b4a40f14
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Az SDK-ban biztosított eszközökkel használatával egyszerűbbé teheti a fejlesztési történő üzembe helyezéséhez
Az IoT Hub eszköz kiépítése szolgáltatás egyszerűbbé teszi a biztonságos és skálázható módon szolgáltatáskiépítéssel nulla érintéssel, közvetlenül az idő a telepítési folyamatot.  Meg kell adni a biztonsági tanúsítvány X.509-tanúsítvány vagy a platformmegbízhatósági modul (TPM) formájában.  Microsoft is van együttműködve [más biztonsági hardverszállító partnerétől](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) IoT telepítés biztonságossá tétele az vetett bizalmat javítása érdekében. A hardveres biztonsági követelmény ismertetése meglehetősen kihívást a fejlesztők számára lehet. Azure IoT kiépítése szolgáltatáshoz SDK-k olyan készlete, hogy a fejlesztők kényelme réteg a kommunikálhatnak a létesítési szolgáltatás ügyfelek írás találhatók. Az SDK-k is biztosítják minták gyakori helyzetek, valamint az eszközöket, egyszerűbbé teheti a fejlesztési biztonsági tanúsítvány.

## <a name="trusted-platform-module-tpm-simulator"></a>Platformmegbízhatósági modul (TPM) megbízható szimulátor
[TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security#trusted-platform-module-tpm) biztonságosan a hitelesítéshez a platform kulcsok tárolásához standard hivatkozhat, illetve a normál végrehajtási modulok együttműködhet használt i/o-felület hivatkozhat. TPM diszkrét hardverként integrált hardver, a belső vezérlőprogram-alapú vagy szoftveres létezhet.  A végleges TPM található az eszközön, vagy különálló hardver, integrált hardver, vagy a belső vezérlőprogram-alapú. A tesztelési fázisban a fejlesztők számára egy szoftveres TPM szimulátor valósul meg.  A szimulátor adattárházzal történő, a Windows platform egyelőre csak érhető el.

A TPM-szimulátorban történő használatához vezető lépések a következők:
1. [A fejlesztőkörnyezet előkészítése](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java#prepare-the-development-environment) és a GitHub-tárház klónozása:
```
git clone https://github.com/Azure/azure-iot-sdk-java.git
```
2. A TPM szimulátor mappájában navigáljon ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/```.
3. Futtassa a Simulator.exe bármely ügyfél alkalmazás üzembe helyezési eszköz futtatása előtt.
4. Lehetővé teszik a szimulátor a regisztrációs azonosítója és kulcsa az üzembe helyezési folyamat során a háttérben futnak.  Mindkét csak érvényes értéket is a Futtatás egy példánya.

## <a name="x509-certificate-generator"></a>X.509 tanúsítvány generátor
[X.509 tanúsítvány](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) segítségével egy igazoló mechanizmusként éles méretezhető és egyszerűsítse az eszközök kiépítését.  Nincsenek [többféleképpen](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) egy X.509 tanúsítvány beszerzése:
* Éles környezet érdekében javasoljuk, egy X.509 Hitelesítésszolgáltatói tanúsítvány egy nyilvános, legfelső szintű hitelesítésszolgáltatótól származó megvásárlását.
* A tesztelési környezetben, X.509 legfelső szintű tanúsítványt vagy X.509-tanúsítvány láncában használatával hozhat létre:
    * OpenSSL: Ez [módjáról útmutató](https://docs.microsoft.com/azure/iot-hub/iot-hub-security-x509-create-certificates) végigvezeti a minta PowerShell-parancsfájlokat használó [OpenSSL](https://www.openssl.org/) létrehozására és aláírására X.509-tanúsítványokat.  Ezenkívül is használható parancsfájl más nyelveken tanúsítvány generálása:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Eszköz identitása összeállítás motor (DARABOLHATÓ) emulátor: DARABOLHATÓ kriptográfiai eszközt identitás használható, és a TLS protokoll és X.509 alapján igazolás ügyféltanúsítványokat.  [Ismerje meg,](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) további információk a DARABOLHATÓ eszközidentitás.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>X.509 tanúsítvány generátor használatával DARABOLHATÓ emulátorral
Az SDK-k egy X.509 tanúsítvány generátor DARABOLHATÓ emulátorral található, adja meg a [Java SDK](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator).  A kódgenerátor platformfüggetlen működik.  A létrehozott tanúsítvány fejlesztési más nyelveken is használható.

Jelenleg, miközben a DARABOLHATÓ emulátor adja kimenetként, egy legfelső szintű tanúsítványt, egy köztes tanúsítványhoz, a levéltanúsítvány és a hozzá tartozó titkos kulcsot.  Azonban a tanúsítvány legfelső szintű vagy köztes tanúsítvány nem használható egy külön levél tanúsítványának aláírásához.  Ha az eszközregisztráció-forgatókönyvek tesztelése ahol egy aláíró tanúsítványt a több eszköz levél tanúsítványok aláírására használt, használhatja a OpenSSL lánc, létrehozásához.

A kódgenerátor X.509-tanúsítvány létrehozásához:
1. [A fejlesztőkörnyezet előkészítése](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java#prepare-the-development-environment) és a GitHub-tárház klónozása:
```
git clone https://github.com/Azure/azure-iot-sdk-java.git
```
2. Módosítsa a legfelső szintű azure-iot-sdk-java.
3. Futtatás ```mvn install -DskipTests=true``` töltse le az összes szükséges csomagokat és fordítsa le az SDK-val
4. Az X.509 tanúsítvány generátor a keresse meg a legfelső szintű ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator```.
5. A build```mvn clean install```
6. Futtassa az eszközt az alábbi parancsokkal:
```
cd target
java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
```
7. Amikor a rendszer erre kéri, opcionálisan megadhat egy _köznapi nevet_ a tanúsítvány számára.
8. Helyileg az eszköz létrehoz egy **ügyféltanúsítványt**, a **ügyfél tanúsítvány titkos kulcsához**, **köztes Cert**, és a **legfelső szintű tanúsítvány**.

**Ügyfél Cert** a levél tanúsítványt az eszközön.  **Ügyfél Cert** és a társított **ügyfél tanúsítvány titkos kulcsához** szükségesek az ügyfél. Attól függően, hogy milyen nyelven választja az ügyfélalkalmazás másképp fogalmazva mechanizmus eltérő lehet.  További információkért lásd: a [Quickstarts](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) létrehozáskor a szimulált eszköz használatával X.509 további információért.

A legfelső szintű tanúsítvány vagy köztes segítségével hozzon létre egy beléptetési csoport vagy egyedi regisztrációs [programozott módon](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) használatával vagy a [portal](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

## <a name="next-steps"></a>További lépések
* Fejlesztéséhez használ a [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) Azure IoT-központ és az Azure IoT Hub eszköz kiépítése szolgáltatáshoz