---
title: Azure Security teljesítményteszt v2 – Endpoint Security
description: Azure Security teljesítményteszt v2 Endpoint Security
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 452b1a33520309ae696ab318c034f0186c993fdc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91402922"
---
# <a name="security-control-v2-endpoint-security"></a>Biztonsági vezérlő v2: Endpoint Security

A Endpoint Security a végpontok észlelésével és válaszával kapcsolatos vezérlőket fedi le. Ez magában foglalja a végpontok észlelését és válaszát (EDR) és a kártevők elleni szolgáltatást az Azure-környezetekben található végpontokhoz.

## <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: végpont-észlelés és-válasz használata (EDR)

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| ES-1 | 8.1 | SI-2, SI-3, SC-3 |

A végpont-észlelési és-reagálási (EDR) képességek engedélyezése a kiszolgálók és az ügyfelek számára, valamint az SIEM-és biztonsági műveletek folyamatainak integrálására.

A Microsoft Defender komplex veszélyforrások elleni védelem a nagyvállalati végpontok biztonsági platformjának részeként biztosít EDR képességet a fejlett fenyegetések megelőzésére, észlelésére, kivizsgálására és reagálására. 

- [A Microsoft Defender komplex veszélyforrások elleni védelem áttekintése](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Microsoft Defender ATP-szolgáltatás Windows-kiszolgálókhoz](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Microsoft Defender ATP szolgáltatás a nem Windows rendszerű kiszolgálókhoz](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security)

- [Fenyegetésészlelési intelligencia](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: központilag felügyelt, modern kártevő szoftverek használata

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| ES – 2 | 8.1 | SI-2, SI-3, SC-3 |

A valós idejű és rendszeres vizsgálatra alkalmas, központilag felügyelt végpontok elleni kártevő megoldás használata

A Azure Security Center automatikusan képes azonosítani a virtuális gépek számos népszerű kártevő szoftveres megoldását, és jelentést készít az Endpoint Protection futási állapotáról, és javaslatokat tesz. 

A Microsoft antimalware for Azure Cloud Services a Windows rendszerű virtuális gépek (VM) alapértelmezett kártevő szoftvere. Linux rendszerű virtuális gépek esetén használjon harmadik féltől származó antimalware-megoldást.  Az Azure Storage-fiókokba feltöltött kártevők észleléséhez használhatja a Azure Security Center veszélyforrások észlelését is az adatszolgáltatások számára. 

- [A Microsoft antimalware konfigurálása Cloud Services és Virtual Machines](../fundamentals/antimalware.md)

- [Támogatott Endpoint Protection-megoldások](https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows#supported-endpoint-protection-solutions-)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security)

- [Fenyegetésészlelési intelligencia](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

| Azure-azonosító | CIS Controls v 7.1 azonosító (k) | NIST SP800-53 R4 azonosító (k) |
|--|--|--|--|
| ES – 3 | 8.2 | SI-2, SI-3 |

Győződjön meg arról, hogy a kártevők elleni aláírások gyorsan és következetesen frissülnek. 

Kövesse az Azure Security Center: "számítási alkalmazások" című témakörben található javaslatokat &amp; , hogy az összes végpont naprakész legyen a legújabb aláírásokkal. A Microsoft antimalware alapértelmezés szerint automatikusan telepíti a legújabb aláírásokat és a motor-frissítéseket. Linux esetén használjon harmadik féltől származó antimalware-megoldást.

- [A Microsoft antimalware telepítése Azure Cloud Services és Virtual Machines](../fundamentals/antimalware.md)

**Felelősség**: ügyfél

**Ügyfelek biztonságával foglalkozó érdekelt felek** ([További információ](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktúra és végpontbiztonság](/azure/cloud-adoption-framework/organize/cloud-security)

- [Fenyegetésészlelési intelligencia](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Biztonsági megfelelőség kezelése](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Helyzetkezelés](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Endpoint Protection-Értékelés és javaslatok a Azure Security Center](../../security-center/security-center-endpoint-protection.md)
