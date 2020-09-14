---
title: Azure Security teljesítményteszt v2 – adatvédelem
description: Az Azure Security teljesítményteszt v2 adatvédelme
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e377c6669b1db96f549958173aded4f29bae6713
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059252"
---
# <a name="security-control-data-protection"></a>Biztonság-ellenőrzés: Adatvédelem

Az adatvédelem magában foglalja az adatvédelem inaktív állapotban, továbbításkor és a jogosult hozzáférési mechanizmusok útján történő felügyeletét. Ez magában foglalja a bizalmas adategységek felderítését, osztályozását, védelmének és figyelését a hozzáférés-vezérlés, a titkosítás és az Azure-beli naplózás használatával.

## <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: bizalmas adatok felderítése, osztályozása és címkézése

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| DP-1 | 14,5, 14,7 | SI-4, SC-28 |

Felderítheti, osztályozhatja és címkézheti a bizalmas adatokat, így megtervezheti a megfelelő vezérlőket, hogy biztosítsa a bizalmas adatok tárolását, feldolgozását és biztonságos továbbítását a szervezet technológiai rendszereinek segítségével. 

Az Azure-ban, a helyszínen, az Office 365-on és más helyeken található Office-dokumentumokban található bizalmas információkhoz használja a Azure Information Protection (és a hozzá tartozó ellenőrzési eszközt). 

Az Azure SQL Information Protection használatával segítséget nyújthat az Azure SQL-adatbázisokban tárolt adatok besorolásában és címkézésében.

- [Bizalmas adatok címkézése Azure Information Protection használatával](/azure/information-protection/what-is-information-protection) 

- [Az Azure SQL-adatfelderítés megvalósítása](/azure/sql-database/sql-database-data-discovery-and-classification)

**Felelősség**: megosztott

**Ügyfelek biztonsági résztvevői**:

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)  

- [Adatbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-data-security) 

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-2-protect-sensitive-data"></a>DP-2: bizalmas adatok védelme

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| DP-2 | 13,2, 2,10 | SC-7, AC-4 |

A bizalmas adatok védelme az Azure szerepköralapú Access Control (Azure RBAC), a hálózati hozzáférés-vezérlés és az Azure-szolgáltatások adott vezérlői (például az SQL-és egyéb adatbázisok titkosítása) használatával történő hozzáférés korlátozásával. 

A konzisztens hozzáférés-vezérlés biztosításához a hozzáférés-vezérlés összes típusát a vállalati szegmentálási stratégiához kell igazítani. A nagyvállalati szegmentálási stratégiát is tájékoztatni kell a bizalmas vagy üzleti szempontból kritikus fontosságú adatok és rendszerek helyéről.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és védelmet nyújt az ügyfelek adatvesztése és a kitettség ellen. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft néhány alapértelmezett adatvédelmi vezérlőt és képességet alkalmazott.

- [Azure-beli szerepköralapú Access Control (RBAC)](../../role-based-access-control/overview.md)

- [Az ügyfelek adatvédelmének megismerése az Azure-ban](../fundamentals/protection-customer-data.md)

**Felelősség**: megosztott

**Ügyfelek biztonsági résztvevői**:

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Adatbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: a bizalmas adatok jogosulatlan átvitelének figyelése

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| DP-3 | 13,3 | AC-4, SI-4 |

Az adatok jogosulatlan átvitelének figyelése a nagyvállalati láthatóságon és a vezérlésen kívüli helyszíneken. Ez általában olyan rendellenes tevékenységek (nagy vagy szokatlan átvitelek) figyelését jelenti, amelyek jogosulatlan adatkiszűrése jelezhetnek. 

Az Azure Storage komplex veszélyforrások elleni védelem (ATP) és az Azure SQL ATP riasztást tud adni a bizalmas adatok jogosulatlan átvitelét jelző információk rendellenes átviteléről. 

Az Azure Information Protection (felügyeleti rendszer) a besorolt és címkézett információk figyelési funkcióit biztosítja. 

Ha az adatveszteség-megelőzés (DLP) megfelelőségéhez szükség van, egy gazdagép-alapú DLP-megoldás használatával kikényszerítheti a nyomozót és/vagy a megelőző ellenőrzéseket az kiszűrése megakadályozása érdekében.

- [Az Azure SQL ATP engedélyezése](../../azure-sql/database/threat-detection-overview.md)

- [Az Azure Storage ATP engedélyezése](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-security-center)

**Felelősség**: megosztott

**Ügyfelek biztonsági résztvevői**:

- [Biztonsági műveletek központja (SOC)](/azure/cloud-adoption-framework/organize/cloud-security) 

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: bizalmas adatok titkosítása az átvitel során

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| DP-4 | 14,4 | SC-8 |

A hozzáférés-vezérlés kiegészítéseként az átvitt adatoknak védelmet kell biztosítani a "sávon kívüli" támadásoktól (pl. forgalom-rögzítés) a titkosítás használatával, hogy a támadók ne tudják egyszerűen beolvasni vagy módosítani az adatokat. 

Habár ez nem kötelező a magánhálózaton lévő forgalom esetében, ez kritikus fontosságú a külső és a nyilvános hálózatok forgalmában. HTTP-forgalom esetén győződjön meg arról, hogy az Azure-erőforrásokhoz csatlakozó összes ügyfél egyeztetheti a TLS v 1.2-es vagy újabb verzióját. A Távoli felügyelet érdekében titkosítatlan protokoll helyett használja az SSH-t (Linuxon) vagy RDP/TLS (Windows rendszeren). Az elavult SSL-, TLS-és SSH-verziók és protokollok, valamint a gyenge titkosítások le lesznek tiltva.  

Alapértelmezés szerint az Azure titkosítást biztosít az Azure-adatközpontok közötti adatátvitelhez. 

- [A titkosítás ismertetése az Azure-ban](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Információk a TLS biztonságáról](/security/engineering/solving-tls1-problem)

- [Kettős titkosítás az Azure-beli adatforgalomban](../fundamentals/double-encryption.md#data-in-transit)

**Felelősség**: megosztott

**Ügyfelek biztonsági résztvevői**:

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Adatbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

## <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: bizalmas adatok titkosítása a nyugalmi állapotban

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| DP-5 | 14,8 | SC-28, SC-12 |

A hozzáférés-vezérlés kiegészítéseként a REST-adatok védelme a "sávon kívüli" támadásoktól (például a mögöttes tárolóhoz való hozzáférés) a titkosítás használatával történik. Ezzel biztosíthatja, hogy a támadók ne tudják egyszerűen beolvasni vagy módosítani az adatforrást. 

Az Azure alapértelmezés szerint az inaktív adatok titkosítását biztosítja. A szigorúan bizalmas adatok esetében lehetősége van arra, hogy az összes Azure-erőforráson további titkosítást hajtsanak végre, ahol elérhetők. Az Azure alapértelmezés szerint kezeli a titkosítási kulcsokat, az Azure azonban lehetőséget biztosít a saját kulcsok (az ügyfél által felügyelt kulcsok) kezelésére bizonyos Azure-szolgáltatásokhoz.

- [A inaktív adatok titkosításának megismerése az Azure-ban](../fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

- [Az ügyfél által felügyelt titkosítási kulcsok konfigurálása](../../storage/common/storage-encryption-keys-portal.md)

- [Titkosítási modell és kulcskezelő tábla](../fundamentals/encryption-models.md)

- [Inaktív adatok az Azure-ban – Rest kettős titkosítás](../fundamentals/double-encryption.md#data-at-rest)

**Felelősség**: megosztott

**Ügyfelek biztonsági résztvevői**:

- [Biztonsági architektúra](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Alkalmazások biztonsága és DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Adatbiztonság](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

