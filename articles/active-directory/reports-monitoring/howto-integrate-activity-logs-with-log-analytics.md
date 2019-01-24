---
title: A Log Analytics (előzetes verzió) az Azure Monitor használatával az Azure Active Directory-naplók integrálása |} A Microsoft Docs
description: 'Útmutató: Azure Active Directory-naplók integrálása a Log Analytics használatával az Azure Monitor (előzetes verzió) használatával'
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
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 8b9189ef3fff75023316d5272bd93c136106cf94
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54824677"
---
# <a name="integrate-azure-ad-logs-with-log-analytics-using-azure-monitor-preview"></a>Az Azure AD-naplók integrálása az Azure Monitor (előzetes verzió) használatával Log Analytics szolgáltatással

A log Analytics lehetővé teszi adott események megtalálásához, trendeket elemezhet, és hajtsa végre a korrelációs különböző adatforrások között adatokat lekérdezni. A-integráció az Azure AD-Tevékenységnaplók Log Analytics, a feladatokat, mint a most már elvégezheti:

 * Az Azure AD bejelentkezési naplóit a biztonsági naplók az Azure Security Center által közzétett összehasonlítása

 * Hibaelhárítás az alkalmazás bejelentkezési lapján, a teljesítmény szűk alkalmazásteljesítmény-adatokat az Azure Application Insights használatával történik.  

Az Ignite-munkamenetből a következő videó bemutatja, az Azure AD-naplók gyakorlati felhasználói esetek Log Analytics használatával járó előnyöket.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

Ebben a cikkben megismerheti, hogyan integrálható az Azure Active Directory (Azure AD) naplókban a Log Analytics az Azure Monitor használatával.

## <a name="supported-reports"></a>Támogatott jelentések

Irányíthatja tevékenység naplói és a bejelentkezési tevékenységeket tartalmazó naplók a Log Analyticshez való további elemzéseket végezhet. 

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

## <a name="send-logs-to-log-analytics"></a>Naplók küldése a Log Analyticsbe

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

2. Válassza ki **Azure Active Directory** > **diagnosztikai beállítások** -> **diagnosztikai beállítás hozzáadása**. Lehetőség kiválasztásával **beállításainak exportálása** a a **Auditnaplók** vagy **bejelentkezések** oldalon a diagnosztikai beállításokat a konfigurációs lap.  
    
3. Az a **diagnosztikai beállítások** menüben válassza a **Küldés a Log Analyticsnek** jelölőnégyzetet, majd válassza ki **konfigurálása**.

4. Válassza ki a Log Analytics-munkaterületet is szeretne küldeni a naplókat, vagy hozzon létre egy új munkaterületet a megjelenő párbeszédpanelen.  

5. A következő lehetőségek egyikét vagy mindkettőt választhatja:
    * Naplók küldése a Log Analytics-munkaterülethez, válassza a **Adatmodelltáblához** jelölőnégyzetet. 
    * Bejelentkezési naplók elküldése a Log Analytics-munkaterületet, jelölje be a **SignInLogs** jelölőnégyzetet.

6. A beállítás mentéséhez kattintson a **Mentés** gombra.

    ![Diagnosztikai beállítások](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Körülbelül 15 perc után győződjön meg arról, hogy a Log Analytics-munkaterületre átvitt események.

## <a name="next-steps"></a>További lépések

* [Elemezheti az Azure AD-Tevékenységnaplók Log Analytics](howto-analyze-activity-logs-log-analytics.md)
* [Telepítése és a Log Analytics-nézetek használata az Azure Active Directory](howto-install-use-log-analytics-views.md)
