---
title: Az Office 365-beli Azure Sentinel előzetes verziójának összekötése | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók az Office 365-es adatszolgáltatások az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: ff7c862e-2e23-4a28-bd18-f2924a30899d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2019
ms.author: rkarlin
ms.openlocfilehash: 0013540bf0ca921b2f41260dea185f6aa32567d7
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679258"
---
# <a name="connect-data-from-office-365-logs"></a>Adatok összekötése az Office 365-naplókból

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Egyetlen kattintással továbbíthatja az [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) -naplókat az Azure sentinelbe. A naplókat több bérlőről is továbbíthatja egyetlen munkaterületre az Azure Sentinelben. Az Office 365 Activity log-összekötő betekintést nyújt a folyamatban lévő felhasználói tevékenységekre. Az Office 365 különböző felhasználói, rendszergazdai, rendszer-és házirend-műveleteivel és eseményeivel kapcsolatos információkat talál. Az Office 365-naplók Azure Sentinelbe való csatlakoztatásával ezek az adatai megtekinthetik az irányítópultokat, létrehozhatnak egyéni riasztásokat, és javíthatják a vizsgálat folyamatát.

> [!IMPORTANT]
> Ha E3 licenccel rendelkezik, mielőtt az Office 365 felügyeleti tevékenység API-n keresztül fér hozzá az adataihoz, engedélyeznie kell az egyesített naplózási naplózást az Office 365-szervezet számára. Ezt az Office 365-napló bekapcsolásával teheti meg. Útmutatásért lásd: [az Office 365 naplózási naplójának keresése be-és kikapcsolása](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). További információt az [Office 365 felügyeleti tevékenység API](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)-referenciája című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

- A bérlő globális rendszergazdájának vagy biztonsági rendszergazdájának kell lennie
- A számítógépen, amelyről bejelentkezett az Azure Sentinelbe a kapcsolat létrehozásához, ellenőrizze, hogy a 4433-es port nyitva van-e a webes forgalom számára.

## <a name="connect-to-office-365"></a>Csatlakozás az Office 365-höz

1. Az Azure Sentinelben válassza az adatösszekötők lehetőséget, majd kattintson az **Office 365** csempére.

2. Ha még nem tette meg, akkor a **Kapcsolódás** alatt az **Engedélyezés** gomb használatával engedélyezze az Office 365-megoldást. Ha már engedélyezve van, akkor a rendszer a már engedélyezett módon azonosítja a kapcsolatok képernyőjén.
1. Az Office 365 lehetővé teszi az adatok több bérlőről az Azure Sentinelbe való továbbítását. Minden olyan bérlőhöz, amelyhez csatlakozni szeretne, adja hozzá a bérlőt az **Azure sentinelhez**a bérlők összekapcsolásához. 
1. Megnyílik egy Active Directory képernyő. A rendszer arra kéri, hogy a globális rendszergazda felhasználóval hitelesítse magát minden olyan bérlőn, amelyhez csatlakozni szeretne az Azure Sentinel szolgáltatáshoz, és adja meg az Azure Sentinel számára a naplók olvasásához szükséges engedélyeket. 
5. A stream Office 365 Activity naplók területen kattintson a **kiválasztás** elemre, és válassza ki, hogy mely naplózási típusokat szeretné továbbítani az Azure sentinelbe. Az Azure Sentinel jelenleg támogatja az Exchange-et és a SharePointot.

4. Kattintson a **módosítások alkalmazása**lehetőségre.

3. Az Office 365-naplókhoz tartozó Log Analytics vonatkozó sémájának használatához keresse meg a **OfficeActivity**.


## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtanulta, hogyan csatlakoztatható az Office 365 az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).

