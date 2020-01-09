---
title: Azure Security Control – adatvédelem
description: Biztonság-vezérlés – adatvédelem
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 914d1af9793107031af9797cb965420443e80c4a
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564334"
---
# <a name="security-control-data-protection"></a>Biztonság-ellenőrzés: Adatvédelem

Az adatvédelmi javaslatok a titkosítással, a hozzáférés-vezérlési listával, az identitás-alapú hozzáférés-vezérléssel és az adathozzáférés naplózási naplózásával kapcsolatos problémák kezelésére összpontosítanak.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 4.1 | 13,1 | Ügyfél |

A címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 4.2 | 13.2 | Ügyfél |

Különböző előfizetések és/vagy felügyeleti csoportok implementálása fejlesztési, tesztelési és éles környezetekben. Az erőforrásokat VNet/alhálózattal kell elválasztani, és megfelelő címkével kell ellátni, és egy NSG vagy Azure Firewall által védettnek kell lennie. A bizalmas adatok tárolására vagy feldolgozására szolgáló erőforrásoknak elég elszigeteltnek kell lenniük. A bizalmas adatok tárolására és feldolgozására Virtual Machines a házirend és eljárás (ok) bekapcsolásával kikapcsolhatja azokat, ha nincsenek használatban.

További Azure-előfizetések létrehozása:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Management Groups létrehozása:

https://docs.microsoft.com/azure/governance/management-groups/create

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Virtual Network létrehozása:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

NSG létrehozása biztonsági konfigurációval:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Azure Firewall üzembe helyezése:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Riasztások vagy riasztások konfigurálása és a Azure Firewall elutasítása:

https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 4.3 | 13,3 | Ügyfél |

Helyezzen üzembe egy automatizált eszközt olyan hálózati peremhálózati eszközökön, amelyek figyelik a bizalmas adatok jogosulatlan átvitelét, és blokkolja az ilyen átviteleket az információs biztonsági szakemberek értesítése közben.

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 4.4 | 14,4 | Közös |

Titkosítsa az összes bizalmas információt az átvitel során. Győződjön meg arról, hogy az Azure-erőforrásokhoz csatlakozó ügyfelek képesek a TLS 1,2 vagy újabb egyeztetésére.

Kövesse Azure Security Center a inaktív adatok titkosítására és az átvitel közbeni titkosításra vonatkozó ajánlásokat, ahol lehetséges.

A titkosítás ismertetése az Azure-ban:

https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 4,5 | 14,5 | Ügyfél |

Ha az Azure-ban egyetlen szolgáltatás sem érhető el, egy külső gyártótól származó aktív felderítési eszköz használatával azonosíthatja a szervezet technológiai rendszerei által tárolt, feldolgozott vagy továbbított bizalmas adatokat, beleértve a helyszínen vagy a távoli szolgáltató, és frissítse a szervezet bizalmas információinak leltárát.

Az Office 365-dokumentumokban lévő bizalmas adatok azonosításához használja a Azure Information Protection.

Az Azure SQL Information Protection használatával segítséget nyújthat az Azure SQL Database-adatbázisokban tárolt adatok besorolásában és címkézésében.

Az Azure SQL-adatfelderítés megvalósítása:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

A Azure Information Protection megvalósítása:

https://docs.microsoft.com/azure/information-protection/deployment-roadmap

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 4.6 | 14,6 | Ügyfél |

Az Azure AD RBAC használatával szabályozhatja az adatelérést és az erőforrásokat, máskülönben a szolgáltatás-specifikus hozzáférés-vezérlési módszereket is használhatja.

Az Azure RBAC ismertetése:

https://docs.microsoft.com/azure/role-based-access-control/overview

A RBAC konfigurálása az Azure-ban:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 4.7 | 14,7 | Ügyfél |

Egy külső gyártótól származó eszköz, például egy automatizált gazdagép-alapú adatvesztés-megelőzési megoldás implementálása az adathozzáférés-vezérlés kikényszerített állapotára akkor is, ha az Adatmásolás egy rendszerből történik.

## <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 4,8 | 14,8 | Ügyfél |

Az összes Azure-erőforráson használja a titkosítást. A Microsoft azt javasolja, hogy az Azure kezelje a titkosítási kulcsokat, azonban lehetőség van arra, hogy a saját kulcsait bizonyos példányokban kezelje. 

A inaktív adatok titkosításának megismerése az Azure-ban:

https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Az ügyfél által felügyelt titkosítási kulcsok konfigurálása:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 4,9 | 14,9 | Ügyfél |

A Azure Monitor és az Azure-tevékenység naplójának használatával riasztásokat hozhat létre, amikor a módosítások a kritikus Azure-erőforrásokra vonatkoznak.

Riasztások létrehozása az Azure Activity log-eseményekhez:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő biztonsági szabályozást: [sebezhetőségek kezelése](security-control-vulnerability-management.md)

