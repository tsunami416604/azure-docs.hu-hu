---
title: Office 365-adatkapcsolatok összekapcsolhatók az Azure Sentinel-vel | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók az Office 365-es adatszolgáltatások az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: df5aade7244f69e7264f901364ecc164351eec50
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815785"
---
# <a name="connect-data-from-office-365-logs"></a>Adatok összekötése az Office 365-naplókból



Egyetlen kattintással továbbíthatja az [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) -naplókat az Azure sentinelbe. A naplókat több bérlőről is továbbíthatja egyetlen munkaterületre az Azure Sentinelben. Az Office 365 Activity log-összekötő betekintést nyújt a folyamatban lévő felhasználói tevékenységekre. Az Office 365 különböző felhasználói, rendszergazdai, rendszer-és házirend-műveleteivel és eseményeivel kapcsolatos információkat talál. Az Office 365-naplók Azure Sentinelbe való csatlakoztatásával ezek az adatai megtekinthetik az irányítópultokat, létrehozhatnak egyéni riasztásokat, és javíthatják a vizsgálat folyamatát.

> [!IMPORTANT]
> Ha E3 licenccel rendelkezik, mielőtt az Office 365 felügyeleti tevékenység API-n keresztül fér hozzá az adataihoz, engedélyeznie kell az egyesített naplózási naplózást az Office 365-szervezet számára. Ezt az Office 365-napló bekapcsolásával teheti meg. Útmutatásért lásd: [az Office 365 naplózási naplójának keresése be-és kikapcsolása](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). További információt az [Office 365 felügyeleti tevékenység API-referenciája](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

- A bérlő globális rendszergazdájának vagy biztonsági rendszergazdájának kell lennie
- A számítógépen, amelyről bejelentkezett az Azure Sentinelbe a kapcsolat létrehozásához, ellenőrizze, hogy a 4433-es port nyitva van-e a webes forgalom számára. A csatlakozás sikeres elvégzése után a portot újra be lehet zárni.
- Ha a bérlő nem rendelkezik Office 365 E3 vagy Office 365 E5 licenccel, engedélyeznie kell az egyesített naplózást a bérlőn a következő folyamatok egyikének használatával:
    - [Használja a set-AdminAuditLogConfig parancsmagot](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) , és engedélyezze a "UnifiedAuditLogIngestionEnabled" paramétert.
    - [Vagy használja a biztonsági és megfelelőségi központ felhasználói felületét](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).

## <a name="connect-to-office-365"></a>Csatlakozás az Office 365-höz

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, majd kattintson az **Office 365** csempére.

2. Ha még nem tette volna meg, ezt az **Adatösszekötők** panelen, az **Office 365** -összekötő kiválasztásával teheti meg. Itt rákattinthat az **összekötő megnyitása lapra** , és a konfiguráció szakaszban címkézve **engedélyezheti az Office 365-megoldás használatát a munkaterületen** a **megoldás telepítése** gombra kattintva engedélyezheti. Ha már engedélyezve van, akkor a rendszer a már engedélyezett módon azonosítja a kapcsolatok képernyőjén.
1. Az Office 365 lehetővé teszi az adatok több bérlőről az Azure Sentinelbe való továbbítását. Minden olyan bérlőhöz, amelyhez csatlakozni szeretne, adja hozzá a bérlőt az **Azure sentinelhez a bérlők összekapcsolásához**. 
1. Megnyílik egy Active Directory képernyő. A rendszer arra kéri, hogy a globális rendszergazda felhasználóval hitelesítse magát minden olyan bérlőn, amelyhez csatlakozni szeretne az Azure Sentinel szolgáltatáshoz, és adja meg az Azure Sentinel számára a naplók olvasásához szükséges engedélyeket. 
5. A bérlői listában láthatja az Azure AD-címtár AZONOSÍTÓját (bérlői AZONOSÍTÓját) és két jelölőnégyzetet az Exchange-és SharePoint-naplókhoz. Kiválaszthatja azokat a felsorolt szolgáltatásokat, amelyeket be szeretne venni a Sentinelbe. Jelenleg az Azure Sentinel támogatja az Exchange-és SharePoint-naplókat a meglévő Office 365-szolgáltatásokon belül.

4. Miután kiválasztotta a szolgáltatásokat (Exchange, SharePoint stb.), kattintson a Save (Mentés) gombra a bérlői hozzáadási kereten az oldalon. 

3. Az Office 365-naplókhoz tartozó Log Analytics vonatkozó sémájának használatához keresse meg a **OfficeActivity**.


## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtanulta, hogyan csatlakoztatható az Office 365 az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).

