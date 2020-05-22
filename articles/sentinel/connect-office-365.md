---
title: Office 365-adatkapcsolatok összekapcsolhatók az Azure Sentinel-vel | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók az Office 365-es adatszolgáltatások az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/12/2020
ms.author: yelevin
ms.openlocfilehash: c3e63063b3ea4e7fba3997ddd645aa59fe857488
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758571"
---
# <a name="connect-data-from-office-365-logs"></a>Adatok összekötése az Office 365-naplókból



Egyetlen kattintással továbbíthatja az [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) -naplókat az Azure sentinelbe. Az Office 365-naplókat az Azure Sentinel-munkaterületére is továbbíthatja ugyanazon a bérlőn. Az Office 365 Activity log-összekötő betekintést nyújt a folyamatban lévő felhasználói tevékenységekre. Az Office 365 különböző felhasználói, rendszergazdai, rendszer-és házirend-műveleteivel és eseményeivel kapcsolatos információkat talál. Az Office 365-naplók Azure Sentinelbe való csatlakoztatásával ezek az adatai megtekinthetik az irányítópultokat, létrehozhatnak egyéni riasztásokat, és javíthatják a vizsgálat folyamatát.

> [!IMPORTANT]
> Ha E3 licenccel rendelkezik, mielőtt az Office 365 felügyeleti tevékenység API-n keresztül fér hozzá az adataihoz, engedélyeznie kell az egyesített naplózási naplózást az Office 365-szervezet számára. Ezt az Office 365-napló bekapcsolásával teheti meg. Útmutatásért lásd: [az Office 365 naplózási naplójának keresése be-és kikapcsolása](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). További információt az [Office 365 felügyeleti tevékenység API-referenciája](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

- A bérlőn globális rendszergazdai vagy biztonsági rendszergazdai jogosultsággal kell rendelkeznie.
- A bérlőnek engedélyezve kell lennie az egyesített naplózásnak. Az Office 365 E3 vagy E5 licenccel rendelkező bérlők alapértelmezés szerint engedélyezve vannak az egyesített naplózással. <br>Ha a bérlő nem rendelkezik ezekkel a licencekkel, engedélyeznie kell az egyesített naplózást a bérlőn a következő módszerek egyikének használatával:
    - [Használja a set-AdminAuditLogConfig parancsmagot](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) , és engedélyezze a "UnifiedAuditLogIngestionEnabled" paramétert.
    - [A biztonsági & megfelelőségi központ felhasználói felületének használata](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).
   
   > [!NOTE]
   > Jelenleg a O365-adatösszekötő csak az adattípusok szakasz összekötő lapján szereplő, automatikusan rögzíti az Exchange-és a SharePoint-tevékenységtípus. Javasoljuk, hogy ellenőrizze [ezt a cikket arra az esetre, ha a Teams és a Sentinel használatával szeretné megkövetelni a csapatok naplózását](https://techcommunity.microsoft.com/t5/azure-sentinel/protecting-your-teams-with-azure-sentinel/ba-p/1265761) 

## <a name="connect-to-office-365"></a>Csatlakozás az Office 365-höz

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, majd kattintson az **Office 365** csempére.

2. Ha még nem tette volna meg, ezt az **Adatösszekötők** panelen, az **Office 365** -összekötő kiválasztásával teheti meg. Itt rákattinthat az **összekötő megnyitása lapra** , és a konfiguráció feliratú **konfiguráció területen válassza ki az** összes Office 365-tevékenység naplóit, amelyeket az Azure sentinelhez szeretne csatlakoztatni. 
   > [!NOTE]
   > Ha már csatlakozott több bérlőhöz az Office 365-összekötő egy korábban támogatott verziójában az Azure Sentinelben, megtekintheti és módosíthatja az egyes bérlők által gyűjtött naplókat. Nem adhat hozzá további bérlőket, de a korábban hozzáadott bérlőket is eltávolíthatja.
3. Az Office 365-naplókhoz tartozó Log Analytics vonatkozó sémájának használatához keresse meg a **OfficeActivity**.


## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtanulta, hogyan csatlakoztatható az Office 365 az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).

