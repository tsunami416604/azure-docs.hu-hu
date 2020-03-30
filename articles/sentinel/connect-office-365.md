---
title: Az Office 365-adatok csatlakoztatása az Azure Sentinelhez| Microsoft dokumentumok
description: Megtudhatja, hogy miként kapcsolhatja össze az Office 365-adatokat az Azure Sentinelhez.
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
ms.openlocfilehash: 43eba727b1dc724aae6eea3ec77de1363c5db73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252512"
---
# <a name="connect-data-from-office-365-logs"></a>Adatok csatlakoztatása az Office 365-naplókból



Egyetlen kattintással streamelheti az [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) naplóit az Azure Sentinelbe. Az Office 365-ből naplónaplókat streamelhet az Azure Sentinel-munkaterületére ugyanazon a bérlőn. Az Office 365 tevékenységnapló-összekötője betekintést nyújt a folyamatban lévő felhasználói tevékenységekbe. Az Office 365-ből különböző felhasználói, rendszergazdai, rendszer- és házirend-műveletekről és eseményekről kaphat információkat. Az Office 365-naplók Azure Sentinelhez való csatlakoztatásával ezeket az adatokat irányítópultok megtekintésére, egyéni riasztások létrehozására és a vizsgálati folyamat javítására használhatja.

> [!IMPORTANT]
> Ha Rendelkezik E3 licenccel, mielőtt az Office 365 Felügyeleti tevékenység API-n keresztül hozzáférhetne az adatokhoz, engedélyeznie kell az office 365-ös szervezet egységes naplózását. Ezt az Office 365 naplójának bekapcsolásával tegye meg. További információt az [Office 365 naplókeresésének be- és kikapcsolása.](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off) További információt az [Office 365 felügyeleti tevékenység API-val kapcsolatos hivatkozási ideje](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

- Globális rendszergazdának vagy biztonsági rendszergazdának kell lennie a bérlőn.
- A bérlőnek engedélyeznie kell az egyesített naplózást. Az Office 365 E3 vagy E5 licenccel rendelkező bérlők alapértelmezés szerint engedélyezve vannak az egyesített naplózás. <br>Ha a bérlő nem rendelkezik a következő licencek egyikével, engedélyeznie kell az egységes naplózást a bérlőn az alábbi módszerek egyikével:
    - [A Set-AdminAuditLogConfig parancsmag használata](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) és a "UnifiedAuditLogIngestionEnabled" paraméter engedélyezése.
    - [A Biztonsági & megfelelőségi központ felhasználói felületének használata.](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin)

## <a name="connect-to-office-365"></a>Csatlakozás az Office 365-höz

1. Az Azure Sentinelben válassza **az Adatösszekötők** lehetőséget, majd kattintson az **Office 365** csempére.

2. Ha még nem engedélyezte, ezt megteheti a **Data Connectors** panelen, és válassza az **Office 365-összekötőt.** Itt kattinthat az **Összekötő lap megnyitása elemre,** és a **Konfiguráció** feliratú konfigurációs szakaszban válassza ki az Azure Sentinelhez csatlakozni kívánt összes Office 365-tevékenységnaplót. 
   > [!NOTE]
   > Ha már több bérlőt csatlakoztatott az Azure Sentinelben az Office 365-összekötő korábban támogatott verziójában, megtekintheti és módosíthatja az egyes bérlőktől gyűjtött naplókat. Nem tud további bérlőket hozzáadni, de eltávolíthatja a korábban hozzáadott bérlők.
3. Ha a megfelelő sémát szeretné használni az Office 365-naplók Naplóanalytics szolgáltatásában, keresse meg az **OfficeActivity**kifejezést.


## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja az Office 365-öt az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats-built-in.md)

