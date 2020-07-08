---
title: Azure Security Control – adatvédelem
description: Azure biztonsági vezérlés – adatvédelem
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: d89320807c6322120490db85100453edf593aded
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045535"
---
# <a name="security-control-data-protection"></a>Biztonság-ellenőrzés: Adatvédelem

Az adatvédelmi javaslatok a titkosítással, a hozzáférés-vezérlési listával, az identitás-alapú hozzáférés-vezérléssel és az adathozzáférés naplózási naplózásával kapcsolatos problémák kezelésére összpontosítanak.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 4.1 | 13,1 | Ügyfél |

A címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

- [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 4.2 | 13,2, 2,10 | Ügyfél |

Különálló előfizetések és felügyeleti csoportok használatával különítse el az elkülönítést az egyes biztonsági tartományok, például a környezeti típus és az adatérzékenység szintjén. Korlátozhatja az alkalmazásaihoz és a vállalati környezetekhez igénybe veheti az Azure-erőforrásokhoz való hozzáférés szintjét. Az Azure-erőforrásokhoz való hozzáférést az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével szabályozhatja. 

- [További Azure-előfizetések létrehozása](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Management Groups létrehozása](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 4.3 | 13,3 | Megosztott |

Egy külső gyártótól származó megoldást használhat fel az Azure Marketplace-ről olyan hálózati területekre, amelyek figyelik a bizalmas adatok jogosulatlan átvitelét, és blokkolja az ilyen átviteleket, miközben riasztást küldenek az informatikai szakemberek számára.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és védelmet nyújt az ügyfelek adatvesztése és a kitettség ellen. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyfelek adatvédelmének megismerése az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 4.4 | 14,4 | Megosztott |

Titkosítsa az összes bizalmas információt az átvitel során. Győződjön meg arról, hogy az Azure-erőforrásokhoz csatlakozó ügyfelek képesek a TLS 1,2 vagy újabb egyeztetésére.

Kövesse Azure Security Center a inaktív adatok titkosítására és az átvitel közbeni titkosításra vonatkozó ajánlásokat, ahol lehetséges.

- [A titkosítás ismertetése az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 4,5 | 14,5 | Megosztott |

Ha az Azure-ban egyetlen szolgáltatás sem érhető el, egy külső gyártótól származó aktív felderítési eszköz használatával azonosíthatja a szervezet technológiai rendszerei által tárolt, feldolgozott vagy továbbított összes bizalmas információt, beleértve a helyszínen vagy egy távoli szolgáltatón keresztül, valamint a szervezet bizalmas információinak leltározását.

Az Office 365-dokumentumokban lévő bizalmas adatok azonosításához használja a Azure Information Protection.

Az Azure SQL Information Protection használatával segítheti a Azure SQL Database tárolt információk besorolását és címkézését.

- [Az Azure SQL-adatfelderítés megvalósítása](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

- [A Azure Information Protection megvalósítása](https://docs.microsoft.com/azure/information-protection/deployment-roadmap)

- [Az ügyfelek adatvédelmének megismerése az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozásához

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 4.6 | 14,6 | Ügyfél |

Az Azure AD RBAC használatával szabályozhatja az adatelérést és az erőforrásokat, máskülönben a szolgáltatás-specifikus hozzáférés-vezérlési módszereket is használhatja.

- [A RBAC konfigurálása az Azure-ban](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 4,7 | 14,7 | Megosztott |

Ha a megfelelőség szükséges a számítási erőforrásokon, hozzon létre egy harmadik féltől származó eszközt, például egy automatizált gazdagép-alapú adatvesztés-megelőzési megoldást, hogy az adatokra akkor is kényszerítse a hozzáférés-vezérlést, ha az adatok egy rendszerből lettek átmásolva.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyfelek adatvédelmének megismerése az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 4,8 | 14,8 | Ügyfél |

Az összes Azure-erőforráson használja a titkosítást. A Microsoft azt javasolja, hogy az Azure kezelje a titkosítási kulcsokat, azonban lehetőség van arra, hogy a saját kulcsait bizonyos példányokban kezelje. 

- [A inaktív adatok titkosításának megismerése az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

- [Az ügyfél által felügyelt titkosítási kulcsok konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 4,9 | 14,9 | Ügyfél |

A Azure Monitor és az Azure-tevékenység naplójának használatával riasztásokat hozhat létre, amikor a módosítások a kritikus Azure-erőforrásokra vonatkoznak.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)


## <a name="next-steps"></a>További lépések

- Tekintse meg a következő biztonsági szabályozást: [sebezhetőségek kezelése](security-control-vulnerability-management.md)