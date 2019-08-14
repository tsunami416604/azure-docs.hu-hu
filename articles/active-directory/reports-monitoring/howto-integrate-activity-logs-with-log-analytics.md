---
title: Stream Azure Active Directory naplók a Azure Monitor naplókhoz | Microsoft Docs
description: Ismerje meg, hogyan integrálhat Azure Active Directory naplókat Azure Monitor naplókba
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: bffe16d604ac6b86b489092f50fbdc0b856867b3
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989769"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>Azure AD-naplók integrálása Azure Monitor naplókkal

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

A Azure Monitor naplók lehetővé teszik az adatok lekérdezését az egyes események megtalálásához, a trendek elemzéséhez és a különböző adatforrások közötti korreláció végrehajtásához. Az Azure AD-tevékenységek Azure Monitor naplókba való integrálásával mostantól például a következő feladatokat végezheti el:

 * Az Azure AD bejelentkezési naplóinak összehasonlítása Azure Security Center által közzétett biztonsági naplókkal

 * Az alkalmazás bejelentkezési oldalának teljesítménybeli szűk keresztmetszetei az Azure-Application Insights alkalmazás teljesítményadatait összekapcsolásával orvosolhatók.  

Az alábbi videó egy Ignite-munkamenetből mutatja be, hogy milyen előnyökkel jár a Azure Monitor naplók használata az Azure AD-naplókhoz gyakorlati felhasználói forgatókönyvekben.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

Ebből a cikkből megtudhatja, hogyan integrálhatja a Azure Active Directory-(Azure AD-) naplókat a Azure Monitorokkal.

## <a name="supported-reports"></a>Támogatott jelentések

További elemzés céljából átirányíthatja a naplózási tevékenységek naplóit és a bejelentkezési tevékenységek naplóit Azure Monitor naplókba. 

* **Naplók**: A [naplózási tevékenység jelentés](concept-audit-logs.md) a bérlőn végrehajtott összes feladat előzményeihez biztosít hozzáférést.
* **Bejelentkezési naplók**: A [bejelentkezési tevékenység jelentés](concept-sign-ins.md)segítségével meghatározhatja, hogy ki hajtotta végre a naplókban jelentett feladatokat.

> [!NOTE]
> A B2C-hez kapcsolódó audit- és bejelentkezési tevékenységnaplók jelenleg nem támogatottak.
>

## <a name="prerequisites"></a>Előfeltételek 

A szolgáltatás használatához a következőkre lesz szüksége:

* Azure-előfizetés. Ha nincs Azure-előfizetése, [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/free/).
* Egy Azure AD-bérlő.
* Egy felhasználó, aki az adott Azure AD-bérlő *globális* vagy *biztonsági rendszergazdája*.
* Egy Log Analytics munkaterület az Azure-előfizetésében. Megtudhatja, hogyan [hozhat létre log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace)munkaterületet.

## <a name="send-logs-to-azure-monitor"></a>Naplók elküldése a Azure Monitorba

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

2. Válassza **Azure Active Directory** > **diagnosztikai beállítások** -> **Hozzáadás diagnosztikai beállítást**. A **naplózási naplók** vagy a **bejelentkezések** lapon is kiválaszthatja a **Beállítások exportálása** lehetőséget a diagnosztikai beállítások konfigurációs oldalának beolvasásához.  
    
3. A **diagnosztikai beállítások** menüben jelölje be a **Küldés log Analytics** munkaterületre jelölőnégyzetet, majd válassza a **Konfigurálás**lehetőséget.

4. Válassza ki azt a Log Analytics munkaterületet, amelyhez el szeretné küldeni a naplókat, vagy hozzon létre egy új munkaterületet a megadott párbeszédpanelen.  

5. A következő lehetőségek egyikét vagy mindkettőt választhatja:
    * Ha naplókat szeretne küldeni a Log Analytics munkaterületre, jelölje be a **AuditLogs** jelölőnégyzetet. 
    * Ha bejelentkezési naplókat szeretne küldeni a Log Analytics munkaterületre, jelölje be a **SignInLogs** jelölőnégyzetet.

6. A beállítás mentéséhez kattintson a **Mentés** gombra.

    ![Diagnosztikai beállítások](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Körülbelül 15 perc elteltével ellenőrizze, hogy az események továbbítva vannak-e a Log Analytics-munkaterületre.

## <a name="next-steps"></a>További lépések

* [Azure AD-beli tevékenység-naplók elemzése Azure Monitor naplókkal](howto-analyze-activity-logs-log-analytics.md)
* [A log Analytics-nézetek telepítése és használata Azure Active Directory](howto-install-use-log-analytics-views.md)