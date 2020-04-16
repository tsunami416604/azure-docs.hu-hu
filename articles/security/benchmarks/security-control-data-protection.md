---
title: Azure Security Control – Adatvédelem
description: Az Azure Security Control adatvédelmi adatai
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 035894c80e619851264aae91daa2d7852d156964
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408543"
---
# <a name="security-control-data-protection"></a>Biztonsági ellenőrzés: Adatvédelem

Az adatvédelmi ajánlások a titkosítással, a hozzáférés-vezérlési listákkal, az identitásalapú hozzáférés-vezérléssel és az adathozzáférés naplózásával kapcsolatos problémák kezelésére összpontosítanak.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Az érzékeny információk leltárának karbantartása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 4.1 | 13.1 | Ügyfél |

A Címkék segítségével nyomon követheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrásokat.

- [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Érzékeny információkat tároló vagy feldolgozó rendszerek elkülönítése

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 4.2 | 13.2, 2.10 | Ügyfél |

Elkülönítést valósíthat meg külön előfizetések és felügyeleti csoportok használatával az egyes biztonsági tartományokhoz, például a környezet típusához és az adatérzékenységi szinthez. Korlátozhatja az Azure-erőforrásokhoz való hozzáférés szintjét, amelyet az alkalmazások és a vállalati környezetek igényelnek. Az Azure-erőforrásokhoz való hozzáférést az Azure Active Directory szerepköralapú hozzáférés-vezérléssel szabályozhatja. 

- [További Azure-előfizetések létrehozása](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Felügyeleti csoportok létrehozása](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: A bizalmas adatok jogosulatlan továbbításának figyelése és blokkolása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 4.3 | 13.3 | Megosztott |

Használja ki az Azure Piactér külső megoldását a hálózat peremén, amely figyeli a bizalmas adatok jogosulatlan átvitelét, és blokkolja az ilyen átviteleket, miközben figyelmezteti az információbiztonsági szakembereket.

A Microsoft által kezelt mögöttes platform esetében a Microsoft minden ügyféltartalmat érzékenynek és az ügyfelek adatvesztésével és expozíciójával szemben idáig kezel. Annak érdekében, hogy az Azure-on belüli ügyféladatok biztonságban legyenek, a Microsoft hatékony adatvédelmi vezérlőket és képességeket vezetett be és tart fenn.

- [Az ügyfelek adatainak védelme az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az összes bizalmas információ titkosítása szállítás közben

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 4.4 | 14.4 | Megosztott |

Titkosítsa az összes bizalmas információt az átvitel során. Győződjön meg arról, hogy az Azure-erőforrásokhoz csatlakozó ügyfelek képesek egyeztetni a TLS 1.2-es vagy annál nagyobb összeegyeztetését.

Kövesse az Azure Security Center javaslatokat az inaktív titkosításhoz és a titkosításhoz az átvitel során, adott esetben.

- [A titkosítás megértése az Azure-ral való átvitel során](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Aktív felderítési eszköz használata a bizalmas adatok azonosítására

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 4.5 | 14.5 | Megosztott |

Ha az Azure-ban nem érhető el szolgáltatás az adott szolgáltatáshoz, használjon egy külső gyártótól származó aktív felderítési eszközt a szervezet technológiai rendszerei által tárolt, feldolgozott vagy továbbított összes bizalmas információ azonosítására, beleértve a helyszínen vagy egy távoli szolgáltatónál található bizalmas adatokat, és frissítse a szervezet bizalmas információkészletét.

Az Azure Information Protection használatával azonosíthatja a bizalmas adatokat az Office 365-dokumentumokban.

Az Azure SQL Information Protection használatával segítséget nyújthat az Azure SQL-adatbázisokban tárolt információk besorolásában és címkézésében.

- [Az Azure SQL Data Discovery implementálja](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

- [Az Azure Information Protection implementiója](https://docs.microsoft.com/azure/information-protection/deployment-roadmap)

- [Az ügyfelek adatainak védelme az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozásához

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 4.6 | 14.6 | Ügyfél |

Az Azure AD RBAC használatával szabályozhatja az adatokhoz és erőforrásokhoz való hozzáférést, más különben szolgáltatásspecifikus hozzáférés-vezérlési módszereket használhat.

- [Az RBAC konfigurálása az Azure-ban](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: A hozzáférés-vezérlés kényszerítése gazdagépalapú adatveszteség-megelőzéssel

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 4.7 | 14.7 | Megosztott |

Ha a számítási erőforrások megfelelőségéhez szükséges, hajtson végre egy külső gyártótól származó eszközt, például egy automatizált gazdagép-alapú adatveszteség-megelőzési megoldást, hogy az adatokhoz való hozzáférés-szabályozás tanusítsa, még akkor is, ha adatokat másol a rendszerről.

A Microsoft által kezelt mögöttes platform esetében a Microsoft minden ügyféltartalmat érzékenynek tekint, és mindent megtesz azért, hogy megvédje az ügyfelek adatvesztését és expozícióját. Annak érdekében, hogy az Azure-on belüli ügyféladatok biztonságban legyenek, a Microsoft hatékony adatvédelmi vezérlőket és képességeket vezetett be és tart fenn.

- [Az ügyfelek adatainak védelme az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas információk titkosítása nyugalmi

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 4.8 | 14.8 | Ügyfél |

Használja a titkosítást inaktívként az összes Azure-erőforrást. A Microsoft azt javasolja, hogy az Azure kezelje a titkosítási kulcsokat, azonban van lehetőség a saját kulcsok kezelésére bizonyos esetekben. 

- [Az Azure titkosításának megértése](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

- [Az ügyfél által kezelt titkosítási kulcsok konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Naplózza és figyelmeztesse a kritikus Azure-erőforrások változásait

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 4.9 | 14.9 | Ügyfél |

Az Azure Monitor és az Azure-tevékenységnapló használatával riasztásokat hozhat létre a kritikus Azure-erőforrások módosításaihoz.

- [Értesítések létrehozása az Azure-tevékenységnapló eseményeihez](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)


## <a name="next-steps"></a>További lépések

- Tekintse meg a következő biztonsági vezérlőt: [Biztonsági rés kezelése](security-control-vulnerability-management.md)