---
title: Az Azure IoT Hub Device Provisioning Service biztonsággal kapcsolatos fogalmait |} A Microsoft Docs
description: Provisioning alapfogalmai és az IoT Hub Device Provisioning Service-eszközökre vonatkozó biztonsági ismerteti
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: e35330874c647eba2cddde694563c8a1d9e83df5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786727"
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>IoT Hub Device Provisioning Service biztonsággal kapcsolatos fogalmait 

IoT Hub Device Provisioning Service, amellyel egy adott IoT hub használatával beavatkozás nélküli eszközök konfigurálása IoT hub segítő szolgáltatása. A Device Provisioning Service szolgáltatással is [automatikus üzembe](concepts-auto-provisioning.md) eszközök, biztonságos és méretezhető módon. Ez a cikk áttekintést nyújt a *biztonsági* fogalmak vesz részt az eszközök kiépítését. Ez a cikk az összes személyek részt vevő első eszközök készen állnak a használatra vonatkozó.

## <a name="attestation-mechanism"></a>Igazolási mechanizmus

Az igazolási mechanizmus a megerősítő egy eszközidentitást használt módszer. Az igazolási mechanizmust is fontos a regisztrációs listához, amely arra kéri a kiépítési szolgáltatás, mely metódus igazolási egy adott eszköz használata.

> [!NOTE]
> Az IoT Hub egy hasonló fogalom a szolgáltatás "hitelesítési séma" használ.

Device Provisioning Service-állapotigazolási következő formáját támogatja:
* **X.509-tanúsítványokat** X.509 tanúsítványt a szabványos hitelesítési folyamat alapján.
* **Platformmegbízhatósági modul (TPM) megbízható** nonce problémásnak bizonyulhatnak, a TPM szabvány a kulcsok használatával nyújtjuk egy közös hozzáférésű Jogosultságkód (SAS) aláírt jogkivonat alapján. Az űrlap igazolási nincs szükség a fizikai TPM az eszközön, de a szolgáltatás használatával az ellenőrzőkulcs kiszolgálónként auditokkal vár a [TPM specifikáció](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Szimmetrikus kulcs** közös hozzáférésű jogosultságkód (SAS) alapuló [biztonsági jogkivonatokat](../iot-hub/iot-hub-devguide-security.md#security-tokens), többek között a kivonatolt aláírás és a egy beágyazott lejárati. További információkért lásd: [szimmetrikus kulcsát a kulcsigazoláshoz](concepts-symmetric-key-attestation.md).


## <a name="hardware-security-module"></a>Hardveres biztonsági modul

A hardveres biztonsági modul vagy a HSM-be, biztonságos, a hardveres eszköz titkos kulcsok tárolására szolgál, és a titkos tárolási legbiztonságosabb formája. X.509-tanúsítványokat és a SAS-tokeneket is tárolható a HSM-ben. HSM-EK is lehet használni mindkét igazolási mechanizmusok az üzembe helyezési támogatja.

> [!TIP]
> Javasoljuk, hogy az eszközök HSM használata az eszközök titkos kulcsok biztonságos tárolása.

Eszköz titkos kulcsokat is tárolhatók a szoftver (memória), de kevésbé biztonságos űrlap tárhelyet, mint a hardveres biztonsági MODULT.

## <a name="trusted-platform-module"></a>Platformmegbízhatósági modul

TPM-eszköz hivatkozhat a standard szintű, biztonságos tárolásához a platform hitelesítéséhez használt kulcsokat, vagy olvassa el a i/o-felület, a modulok, a standard végrehajtási folytatott kommunikációhoz használható. TPM diszkrét hardverként integrált hardverek, a belső vezérlőprogram-alapú vagy szoftveres létezhet. Tudjon meg többet [TPM és a TPM-eszköz igazolási](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). Device Provisioning Service csak a TPM 2.0 támogatja.

TPM-igazolást nonce problémásnak bizonyulhatnak, ami az ellenőrzőkulcs és a tárolási legfelső szintű kulcsait használja a egy aláírt közös hozzáférésű Jogosultságkód (SAS) tokent alapul.

### <a name="endorsement-key"></a>Ellenőrzőkulcs

Az ellenőrzőkulcs a TPM-eszköz, amely belsőleg generált, vagy kártevő program férkőzik, gyártási idő részletsorában aszimmetrikus kulccsal, és minden TPM esetében egyedi legyen. Az ellenőrzőkulcs nem lehet módosítani vagy eltávolítani. Az ellenőrzőkulcs titkos része a TPM-en kívül soha nem lesz kiadva, közben az ellenőrzőkulcs nyilvános részét eredeti TPM felismerni. Tudjon meg többet a [ellenőrzőkulcsot](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Tároló-gyökérkulcs

A storage legfelső szintű kulcsot a TPM-ben tárolt, és az alkalmazások által létrehozott TPM-kulcsok védelme, hogy ezek a kulcsok nem használható a TPM nélküli használatos. A tároló-gyökérkulcs jön létre, a platformmegbízhatósági modul; Ha Új felhasználó saját tulajdonba vételére, törölje a jelet a TPM-be, amikor egy új tároló-gyökérkulcs jön létre. Tudjon meg többet a [tároló-gyökérkulcs](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="x509-certificates"></a>X.509-tanúsítványok

X.509-tanúsítványokat használ az igazolási mechanizmusként szolgáltatás kiváló éles és egyszerűsítheti az eszközök kiépítését. X.509-tanúsítványokat általában egy tanúsítványlánc bizalmi kapcsolat, amelyben a lánc minden tanúsítvány aláírásával rendelkezik a titkos kulcsot a következő nagyobb tanúsítványt, és így tovább, egy önaláírt főtanúsítványt a megszakítást okozó vannak elrendezve. Ezzel az elrendezéssel fokozott egy meghatalmazott a megbízható legfelső szintű hitelesítésszolgáltató (CA) le segítségével telepítve az eszközön, a végfelhasználói "levél" tanúsítványt minden közbenső hitelesítésszolgáltató által létrehozott főtanúsítványból megbízhatósági láncot létesít. További tudnivalókért lásd: [X.509 Hitelesítésszolgáltatói tanúsítványok használatával Eszközhitelesítés](/azure/iot-hub/iot-hub-x509ca-overview). 

Gyakran a tanúsítványlánc egy bizonyos eszközök társított logikai és fizikai hierarchia. Ha például egy gyártó is:
- egy önaláírt legfelső szintű hitelesítésszolgáltató-tanúsítvány kiállításához
- a legfelső szintű tanúsítvány használatával létrehozhat egy minden factory egyedi köztes Hitelesítésszolgáltatói tanúsítvány
- minden egyes gyári tanúsítvány használatára el létrehozni az egyes gyártósor egyedi köztes Hitelesítésszolgáltatói tanúsítvány
- és végül a gyártósor tanúsítvány használatával hozzon létre minden egyes eszközhöz a sor gyártott (végfelhasználói) eszköz egyedi tanúsítványt. 

További tudnivalókért lásd: [x.509-es Hitelesítésszolgáltatói tanúsítványok az IoT-iparág fogalmi ismeretekkel](/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Főtanúsítvány

A legfelső szintű tanúsítvány egy önaláírt X.509-tanúsítvány egy hitelesítésszolgáltató (CA) jelölő. A pontjáig, vagy megbízhatósági kapcsolati alap, a tanúsítványlánc. Legfelső szintű tanúsítványok saját szervezet által kibocsátott, vagy egy legfelső szintű hitelesítésszolgáltatótól származó is. További tudnivalókért lásd: [első x.509-es Hitelesítésszolgáltatói tanúsítványok](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). A legfelső szintű tanúsítvány is lehet néven egy legfelső szintű Hitelesítésszolgáltatói tanúsítványt.

### <a name="intermediate-certificate"></a>Köztes tanúsítványt

Egy közbenső tanúsítvány egy X.509 tanúsítvány, amely a főtanúsítványt (vagy egy másik köztes tanúsítványt a legfelső szintű tanúsítvány láncában) aláírta. A lánc utolsó köztes tanúsítványt a levél tanúsítvány aláírására használatos. Egy közbenső tanúsítvány is lehet néven egy köztes Hitelesítésszolgáltatói tanúsítványhoz.

### <a name="end-entity-leaf-certificate"></a>Végfelhasználói "levél" tanúsítvány

A levél tanúsítványt, vagy a végfelhasználói tanúsítványt, azonosítja a tanúsítvány tulajdonosa. Rendelkezik a legfelső szintű tanúsítványt a tanúsítványlánc, valamint nulla vagy több köztes tanúsítványok. A levéltanúsítvány nem használatos bármely egyéb tanúsítványok aláírásához. Egyedileg azonosítja az eszközt a kiépítési szolgáltatáshoz, és az eszköz-tanúsítványt is hívják. A hitelesítés során az eszköz használja a ennek a tanúsítványnak társított titkos kulcs birtokában challenge megvalósíthatósági válaszolni a szolgáltatásból.

A használt tanúsítványok levél- [egyéni regisztráció](./concepts-service.md#individual-enrollment) bejegyzés korlátozza, amely a **tulajdonos neve** állítson be az egyéni regisztrációs bejegyzés regisztrációs azonosítójaként. A használt tanúsítványok levél egy [regisztrációs csoportot](./concepts-service.md#enrollment-group) bejegyzést kell rendelkeznie a **tulajdonos neve** állítsa be a kívánt eszköz azonosítója, amely jelenik meg, a **regisztrációs rekord** a a hitelesített eszköz beléptetési csoportnak.

További tudnivalókért lásd: [eszközök hitelesítése aláírt x.509-es Hitelesítésszolgáltatói tanúsítványok](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Eszköz X.509-tanúsítványokat az eszközkiépítési szolgáltatás hozzáférés szabályozása

A kiépítési szolgáltatás két típusú regisztrációs bejegyzésnek, amely segítségével hozzáférésének szabályozása érdekében az X.509-igazolási mechanizmusán támogató eszközöket tesz elérhetővé:  

- [Egyéni regisztráció](./concepts-service.md#individual-enrollment) bejegyzés egy adott eszközhöz társított eszköz tanúsítvány van konfigurálva. Ezek a bejegyzések ellenőrzés regisztrációk az egyes eszközöktől.
- [Regisztrációs csoportot](./concepts-service.md#enrollment-group) bejegyzések társítva egy adott köztes vagy a legfelső szintű Hitelesítésszolgáltatói tanúsítvány. Ezek a bejegyzések összes eszköz, amely rendelkezik, amely köztes tanúsítványt a tanúsítványlánc legfelső szintű regisztrációk szabályozza. 

Amikor egy eszköz csatlakozik a kiépítési szolgáltatáshoz, a szolgáltatás rangsorolja pontosabb regisztrációs bejegyzés keresztül kevésbé specifikus regisztrációs bejegyzéseket. Azt jelenti Ha az eszköz egyéni regisztrációt, a kiépítési szolgáltatás vonatkozik rá. Ha az eszköz nincs egyéni regisztrációt, és az első köztes tanúsítvány, tanúsítványlánc a eszközt regisztrációs csoportot létezik, a szolgáltatás vonatkozik a bejegyzést, és így tovább, fel a lánc a legfelső szintű. A szolgáltatás az első megfelelő bejegyzést talál, vonatkozik, hogy:

- Ha az első regisztrációs bejegyzés található engedélyezve van, a szolgáltatás látja el az eszközt.
- Az első regisztrációs bejegyzés található le van tiltva, ha a szolgáltatás nem biztosítja az eszköz.  
- Ha bármely, a tanúsítványok a tanúsítványláncokban az eszköz nincs regisztrációs bejegyzés található, a szolgáltatás nem biztosítja az eszköz. 

Ez a mechanizmus és a hierarchikus tanúsítványláncok hogyan szabályozhatja a hozzáférést, mint az eszközcsoportok egyes eszközöket, valamint a nagy teljesítményű rugalmasságot biztosít. Képzeljünk el például a következő tanúsítványláncok öt eszközöket: 

- *1 eszköz*: főtanúsítvány A -> 1 eszköz tanúsítvány tanúsítvány ->
- *Az eszköz 2*: főtanúsítvány A -> eszköz – 2. tanúsítvány tanúsítvány ->
- *Eszköz 3*: főtanúsítvány A -> eszköz 3 tanúsítvány tanúsítvány ->
- *Eszköz 4*: főtanúsítvány B -> 4 eszköz tanúsítvány tanúsítvány ->
- *5 eszköz*: főtanúsítvány B -> 5 eszköz tanúsítvány tanúsítvány ->

Kezdetben az összes öt eszköz-hozzáférés engedélyezése a legfelső szintű tanúsítvány egy engedélyezett csoport egyetlen regisztrációs bejegyzés is létrehozhat. B tanúsítvány későbbi lesz sérül, ha egy letiltott regisztrációscsoport-bejegyzést tanúsítvány B megakadályozására létrehozhat *eszköz 4* és *eszköz 5* regisztrációját. Ha még mindig újabb *eszköz 3* válik megsérül, létrehozhat egy letiltott egyéni regisztrációs bejegyzést a tanúsítványt. Ez a hozzáférés visszavonása *eszköz 3*, azonban továbbra is lehetővé teszi, hogy *eszköz 1* és *eszköz 2* regisztrálásához.