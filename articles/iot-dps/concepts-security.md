---
title: "Az Azure IoT Hub eszköz kiépítése szolgáltatáshoz biztonsági fogalmak |} Microsoft Docs"
description: "Biztonsági kiépítése a eszköz kiépítése szolgáltatáshoz és az IoT-központ jellemző fogalmakat ismerteti"
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 3ccbaaf55d2bdfedffcdb5ca069798328e2d75fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>IoT Hub eszköz kiépítése szolgáltatáshoz biztonsági fogalmak 

IoT Hub eszköz kiépítése szolgáltatáshoz olyan IoT hub, amely egy megadott IoT-központ nulla érintéssel eszközkiépítési konfigurálására használt segítő szolgáltatás. Az eszköz kiépítése szolgáltatáshoz megadhat eszközök millióira biztonságos és skálázható módon. Ez a cikk áttekintést a *biztonsági* fogalmak részt vevő eszközök kiépítését. Ez a cikk akkor szükséges, az eszköz telepítési Felkészülés részt vevő összes személyeknek.

## <a name="attestation-mechanism"></a>Állapotigazolási mechanizmus

Az igazolás módszer használata általában egy eszközidentitás használt módszert. Az igazolás mechanizmus a tanúsítványigénylési listája, amely arra kéri a létesítési szolgáltatás melyik adott eszközzel használni kívánt igazolási módszert is fontos.

> [!NOTE]
> Az IoT-központ "hitelesítési séma" hasonló fogalma az adott szolgáltatást használ.

Eszköz kiépítése szolgáltatás igazolási két formáját támogatja:
* **X.509 tanúsítvány** a szabványos X.509 tanúsítvány hitelesítési folyamat alapján.
* **SAS-tokenje** TPM használó kulcs nonce kihívást alapján. Ennek a fizikai TPM az eszközön nem szükséges, de a szolgáltatás várhatóan igazolják, hogy az ellenőrzőkulcsot / használatával az [TPM spec](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).

## <a name="hardware-security-module"></a>Hardveres biztonsági modul

A hardveres biztonsági modult, vagy a hardveres biztonsági MODULT, biztonságos, hardver-alapú eszköz titkos kulcsok tárolására szolgál, és a titkos tárolási legbiztonságosabb formája. X.509-tanúsítványokat és a SAS-tokenje tárolható a HSM-ben. A HSM lehet használni mindkét igazolás mechanizmusok az üzembe helyezési támogatja.

> [!TIP]
> Erősen ajánlott eszközök biztonságosan tárolni a titkos kulcsok az eszközök hardveres biztonsági modul használata.

Eszköz titkokat is tárolhatók a szoftver (memória), de tárolási kevésbé biztonságos formája, mint a hardveres biztonsági MODULT.

## <a name="trusted-platform-module-tpm"></a>Platformmegbízhatósági modul (TPM)

TPM jelentheti a platform hitelesítéséhez használt kulcsokat biztonságosan tárolásához standard, vagy a modulok, a standard végrehajtási együttműködhet használt i/o-felület hivatkozhat. TPM diszkrét hardverként integrált hardver, a belső vezérlőprogram-alapú vagy szoftveres létezhet. További információ [TPM és TPM igazolás](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). Eszköz kiépítése szolgáltatáshoz csak a TPM 2.0 támogatja.

## <a name="endorsement-key"></a>Ellenőrzőkulcs

Az ellenőrzőkulcsot a TPM-be, amely okozott gyártási idő, és minden TPM egyedi VHD aszimmetrikus kulcsok. Az ellenőrző kulcs nem lehet módosítani vagy eltávolítani. Az ellenőrzőkulccsal titkos része soha nem szabadul kívül a TPM-, míg az ellenőrző kulcs nyilvános részének az eredeti TPM ismeri fel. További információ a [ellenőrzőkulccsal](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

## <a name="storage-root-key"></a>Tároló-gyökérkulcs

A legfelső szintű kulcsot a TPM tárolja, és alkalmazások által létrehozott TPM-kulcsok védelmét, hogy ezek a kulcsok nem használható a TPM nélkül. A tároló-gyökérkulcs jön létre, amikor Ön saját tulajdonba a TPM; Új felhasználó tulajdonba, törölje a jelet a TPM-be, amikor egy új tároló-gyökérkulcs jön létre. További információ a [tároló-gyökérkulcs](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="root-certificate"></a>Legfelső szintű tanúsítvány

Egy legfelső szintű tanúsítványt a hitelesítésszolgáltatótól képviselő X.509-tanúsítvány típusú, és önaláírt. A tanúsítványlánc pontjáig.

## <a name="intermediate-certificate"></a>Köztes tanúsítvány

Egy köztes tanúsítványhoz egy X.509 tanúsítvány, amely aláírt a főtanúsítványt (vagy egy másik tanúsítványt a főtanúsítvány láncában), és amely a levél tanúsítvány aláírására szolgál.

## <a name="leaf-certificate"></a>Levéltanúsítvány

A levéltanúsítvány, illetve a végfelhasználói tanúsítvány azonosítására szolgál a tanúsítvány tulajdonosa, és a tanúsítványlánc rendelkezik a legfelső szintű tanúsítvány. A levél nem tanúsítvánnyal bármely egyéb tanúsítványok aláírásához.
