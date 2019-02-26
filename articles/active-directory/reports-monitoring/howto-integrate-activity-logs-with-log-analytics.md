---
title: Az Azure Monitor naplóira (előzetes verzió) az Azure Active Directory-naplókat Stream |} A Microsoft Docs
description: 'Útmutató: Azure Active Directory-naplók integrálása az Azure Monitor naplóira (előzetes verzió)'
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d8259cdb61d2481805dd2e07c11b539e057215c
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817065"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs-preview"></a>Az Azure AD-naplók integrálása az Azure Monitor naplóira (előzetes verzió)

Az Azure Monitor naplóira lehetővé teszi adott események megtalálásához, trendeket elemezhet, és hajtsa végre a korrelációs különböző adatforrások között adatokat lekérdezni. A-integráció az Azure AD-Tevékenységnaplók az Azure Monitor naplóira, most már elvégezheti a feladatok, mint például:

 * Az Azure AD bejelentkezési naplóit a biztonsági naplók az Azure Security Center által közzétett összehasonlítása

 * Hibaelhárítás az alkalmazás bejelentkezési lapján, a teljesítmény szűk alkalmazásteljesítmény-adatokat az Azure Application Insights használatával történik.  

Az Ignite-munkamenetből a következő videó bemutatja, gyakorlati felhasználói esetek az Azure AD-naplók az Azure Monitor naplóira használatának előnyeit.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

Ebből a cikkből elsajátíthatja az Azure Active Directory (Azure AD)-naplók integrálása az Azure Monitor szolgáltatással.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="supported-reports"></a>Támogatott jelentések

További elemzéseket végezhet az Azure Monitor naplóira is tevékenység naplói és a bejelentkezési tevékenységeket tartalmazó naplók átirányítása. 

* **Auditnaplók**: A [naplók Tevékenységjelentés](concept-audit-logs.md) teszi elérhetővé a bérlőben végrehajtott minden tevékenység előzményeit.
* **Bejelentkezési naplók**: Az a [bejelentkezési tevékenységek jelentésének](concept-sign-ins.md), megadhatja, hogy ki hajtotta végre a jelentett feladatokat, a naplók.

> [!NOTE]
> A B2C-hez kapcsolódó audit- és bejelentkezési tevékenységnaplók jelenleg nem támogatottak.
>

## <a name="prerequisites"></a>Előfeltételek 

A szolgáltatás használatához a következőkre lesz szüksége:

* Azure-előfizetés. Ha nincs Azure-előfizetése, [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/free/).
* Egy Azure AD-bérlő.
* Egy felhasználó, aki az adott Azure AD-bérlő *globális* vagy *biztonsági rendszergazdája*.
* Az Azure-előfizetés a Log Analytics-munkaterületet. Ismerje meg, hogyan [hozzon létre egy Log Analytics-munkaterület](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

## <a name="send-logs-to-azure-monitor-logs"></a>Naplók elküldése az Azure Monitor naplóira

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

2. Válassza ki **Azure Active Directory** > **diagnosztikai beállítások** -> **diagnosztikai beállítás hozzáadása**. Lehetőség kiválasztásával **beállításainak exportálása** a a **Auditnaplók** vagy **bejelentkezések** oldalon a diagnosztikai beállításokat a konfigurációs lap.  
    
3. Az a **diagnosztikai beállítások** menüben válassza a **küldeni a Log Analytics-munkaterület** jelölőnégyzetet, majd válassza ki **konfigurálása**.

4. Válassza ki a Log Analytics-munkaterületet is szeretne küldeni a naplókat, vagy hozzon létre egy új munkaterületet a megjelenő párbeszédpanelen.  

5. A következő lehetőségek egyikét vagy mindkettőt választhatja:
    * Naplók küldése a Log Analytics-munkaterülethez, válassza a **Adatmodelltáblához** jelölőnégyzetet. 
    * Bejelentkezési naplók elküldése a Log Analytics-munkaterületet, jelölje be a **SignInLogs** jelölőnégyzetet.

6. A beállítás mentéséhez kattintson a **Mentés** gombra.

    ![Diagnosztikai beállítások](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Körülbelül 15 perc után győződjön meg arról, hogy a Log Analytics-munkaterületre átvitt események.

## <a name="next-steps"></a>További lépések

* [Elemezheti az Azure ad-ben tevékenységeket tartalmazó naplók az Azure Monitor naplóira](howto-analyze-activity-logs-log-analytics.md)
* [Telepítése és a log analytics-nézetek használata az Azure Active Directory](howto-install-use-log-analytics-views.md)
