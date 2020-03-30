---
title: Azure Security Control – Adatvédelem
description: Biztonsági ellenőrzés adatvédelme
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 5482495f87e87e5d05d8adca6b053810a62dcb4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934524"
---
# <a name="security-control-data-protection"></a>Biztonsági ellenőrzés: Adatvédelem

Az adatvédelmi ajánlások a titkosítással, a hozzáférés-vezérlési listákkal, az identitásalapú hozzáférés-vezérléssel és az adathozzáférés naplózásával kapcsolatos problémák kezelésére összpontosítanak.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Az érzékeny információk leltárának karbantartása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 4.1 | 13.1 | Ügyfél |

A Címkék segítségével nyomon követheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrásokat.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Érzékeny információkat tároló vagy feldolgozó rendszerek elkülönítése

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 4.2 | 13.2 | Ügyfél |

Külön előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztéshez, teszteléshez és éles környezethez. Az erőforrásokat virtuális hálózatnak/alhálózatnak kell elválasztani, megfelelően címkézni kell, és NSG-vel vagy Azure tűzfallal kell biztosítani. A bizalmas adatokat tároló vagy feldolgozó erőforrásokat megfelelően el kell különíteni. A bizalmas adatokat tároló vagy feldolgozó virtuális gépek esetében valósítsa meg a szabályzatot és az eljárás(oka)t, hogy kikapcsolja őket, ha nincs használatban.

További Azure-előfizetések létrehozása:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Felügyeleti csoportok létrehozása:

https://docs.microsoft.com/azure/governance/management-groups/create

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Hogyan hozzunk létre egy virtuális hálózat:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Hogyan hozzunk létre egy NSG egy biztonsági config:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Az Azure Tűzfal telepítése:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

A riasztás vagy riasztás és a megtagadás konfigurálása az Azure Tűzfallal:

https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: A bizalmas adatok jogosulatlan továbbításának figyelése és blokkolása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 4.3 | 13.3 | Ügyfél |

Automatikus eszköz telepítése a hálózat peremén, amely figyeli a bizalmas adatok jogosulatlan továbbítását, és blokkolja az ilyen átviteleket, miközben figyelmezteti az információbiztonsági szakembereket.

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az összes bizalmas információ titkosítása szállítás közben

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 4.4 | 14.4 | Megosztott |

Titkosítsa az összes bizalmas információt az átvitel során. Győződjön meg arról, hogy az Azure-erőforrásokhoz csatlakozó ügyfelek képesek egyeztetni a TLS 1.2-es vagy annál nagyobb összeegyeztetését.

Kövesse az Azure Security Center javaslatokat az inaktív titkosításhoz és a titkosításhoz az átvitel során, adott esetben.

Az Azure-ral való átvitel során a titkosítás megismerése:

https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Aktív felderítési eszköz használata a bizalmas adatok azonosítására

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 4.5 | 14.5 | Ügyfél |

Ha az Azure-ban nem érhető el funkció az adott szolgáltatáshoz, használjon egy külső gyártótól származó aktív felderítési eszközt a szervezet technológiai rendszerei által tárolt, feldolgozott vagy továbbított összes bizalmas információ azonosítására, beleértve a helyszínen vagy egy távoli szolgáltatót, és frissítse a szervezet bizalmas információkészletét.

Az Azure Information Protection használatával azonosíthatja a bizalmas adatokat az Office 365-dokumentumokban.

Az Azure SQL Information Protection használatával segítséget nyújthat az Azure SQL-adatbázisokban tárolt információk besorolásában és címkézésében.

Az Azure SQL Data Discovery implementálja:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

Az Azure Information Protection megvalósítása:

https://docs.microsoft.com/azure/information-protection/deployment-roadmap

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Az Azure RBAC használatával szabályozhatja az erőforrásokhoz való hozzáférést

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 4.6 | 14.6 | Ügyfél |

Az Azure AD RBAC használatával szabályozhatja az adatokhoz és erőforrásokhoz való hozzáférést, más különben szolgáltatásspecifikus hozzáférés-vezérlési módszereket használhat.

Az Azure RBAC megismerése:

https://docs.microsoft.com/azure/role-based-access-control/overview

Az RBAC konfigurálása az Azure-ban:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: A hozzáférés-vezérlés kényszerítése gazdagépalapú adatveszteség-megelőzéssel

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 4.7 | 14.7 | Ügyfél |

Harmadik féltől származó eszközt, például automatizált adatveszteség-megelőzési megoldást valósíthat meg az adatokhoz való hozzáférés-szabályozás kényszerítéséhez még akkor is, ha az adatokat a rendszerből másolja át.

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas információk titkosítása nyugalmi

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 4.8 | 14.8 | Ügyfél |

Használja a titkosítást inaktívként az összes Azure-erőforrást. A Microsoft azt javasolja, hogy az Azure kezelje a titkosítási kulcsokat, azonban van lehetőség a saját kulcsok kezelésére bizonyos esetekben. 

Az Azure-ban inaktív titkosítás ismertetése:

https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Az ügyfél által kezelt titkosítási kulcsok konfigurálása:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Naplózza és figyelmeztesse a kritikus Azure-erőforrások változásait

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 4.9 | 14.9 | Ügyfél |

Az Azure Monitor és az Azure-tevékenységnapló használatával riasztásokat hozhat létre a kritikus Azure-erőforrások módosításaihoz.

Értesítések létrehozása az Azure-tevékenységnapló eseményeihez:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

## <a name="next-steps"></a>További lépések

Lásd a következő biztonsági vezérlőt: [Biztonsági rés kezelése](security-control-vulnerability-management.md)
