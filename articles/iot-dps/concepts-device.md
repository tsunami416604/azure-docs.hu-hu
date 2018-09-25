---
title: Az Azure device provisioning Device fogalmak |} A Microsoft Docs
description: A device provisioning alapfogalmai és az IoT Hub Device Provisioning Service-eszközökre vonatkozó ismerteti
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 354ef48f7935536864cde9dc0d9a130fa5aeb865
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972861"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>IoT Hub Device Provisioning Service-eszközökkel kapcsolatos alapelveket ismertető

IoT Hub Device Provisioning Service, amellyel egy adott IoT hub használatával beavatkozás nélküli eszközök konfigurálása IoT hub segítő szolgáltatása. A Device Provisioning Service-szel több millió eszköz kiépítését végezheti el biztonságosan és skálázható módon.

Ez a cikk áttekintést nyújt a *eszköz* fogalmak vesz részt az eszközök kiépítését. Ez a cikk a megfelelő személyeknek, résztvevő a [gyártási lépés](about-iot-dps.md#manufacturing-step) szerezhető egy eszköz telepítésre készen áll.

## <a name="attestation-mechanism"></a>Igazolási mechanizmus

Az igazolási mechanizmus a megerősítő egy eszközidentitást használt módszer. Az igazolási mechanizmust is fontos a regisztrációs listához, amely arra kéri a kiépítési szolgáltatás, mely metódus igazolási egy adott eszköz használata.

> [!NOTE]
> Az IoT Hub egy hasonló fogalom a szolgáltatás "hitelesítési séma" használ.

A Device Provisioning Service-állapotigazolási következő formáját támogatja:
* **X.509-tanúsítványokat** X.509 tanúsítványt a szabványos hitelesítési folyamat alapján.
* **Platformmegbízhatósági modul (TPM) megbízható** nonce problémásnak bizonyulhatnak, a TPM szabvány a kulcsok használatával nyújtjuk egy közös hozzáférésű Jogosultságkód (SAS) aláírt jogkivonat alapján. A fizikai TPM az eszközön nem igényel, de a szolgáltatás használatával az ellenőrzőkulcs kiszolgálónként auditokkal vár a [TPM specifikáció](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Szimmetrikus kulcs** közös hozzáférésű jogosultságkód (SAS) alapuló [biztonsági jogkivonatokat](../iot-hub/iot-hub-devguide-security.md#security-tokens), többek között a kivonatolt aláírás és a egy beágyazott lejárati. További információkért lásd: [szimmetrikus kulcsát a kulcsigazoláshoz](concepts-symmetric-key-attestation.md).

## <a name="hardware-security-module"></a>Hardveres biztonsági modul

A hardveres biztonsági modul vagy a HSM-be, biztonságos, a hardveres eszköz titkos kulcsok tárolására szolgál, és a titkos tárolási legbiztonságosabb formája. X.509-tanúsítványokat és a SAS-tokeneket is tárolható a HSM-ben. HSM-EK használható mindkét igazolási mechanizmusok az eszközkiépítési szolgáltatás által támogatott.

> [!TIP]
> Javasoljuk, hogy az eszközök HSM használata az eszközök titkos kulcsok biztonságos tárolása.

Eszköz titkos kulcsokat is tárolhatók a szoftver (memória), de kevésbé biztonságos űrlap tárhelyet, mint a hardveres biztonsági MODULT.

## <a name="registration-id"></a>Regisztrációs azonosító

A regisztrációs azonosító a Device Provisioning Service az eszköz egyedi azonosítására szolgál. Az eszköz azonosítója az eszközkiépítési szolgáltatás egyedinek kell lennie [azonosító hatóköre](#id-scope). Minden egyes eszközének rendelkeznie kell egy regisztrációs azonosítót. A regisztrációs azonosító az alfanumerikus karakterek, kisbetűs, és kötőjeleket tartalmazhat.

* A TPM esetén a regisztrációs azonosító magából a TPM által biztosított.
* X.509-alapú igazolás esetén a regisztrációs azonosító közöljük a tanúsítvány tulajdonosának nevét.

## <a name="device-id"></a>Eszközazonosító

Az eszköz azonosítója az azonosítója megegyezik az IoT Hub megjelenik. A regisztrációs bejegyzés adható meg a kívánt eszköz azonosítója, de nem kötelező megadni. Ha nem kívánt eszköz Azonosítóját a regisztrációs listában van megadva, a regisztrációs azonosító szolgál az eszköz azonosítója az eszköz regisztrálásakor. Tudjon meg többet [eszközazonosítókat az IoT Hub](../iot-hub/iot-hub-devguide-identity-registry.md).

## <a name="id-scope"></a>Azonosító hatóköre

Az azonosító hatókörének hozzá van rendelve a Device Provisioning Service, amikor a felhasználó által létrehozott és keresztül regisztrálja az eszközt a kiépítési szolgáltatás egyedi azonosítására szolgál. Az azonosító hatókörének a szolgáltatás által létrehozott, és nem módosítható, amely garantálja, hogy egyedi-e.

> [!NOTE]
> Egyedi-e hosszú ideig futó központi telepítési műveletek és egyesülési és felvásárlási forgatókönyvek fontos.
