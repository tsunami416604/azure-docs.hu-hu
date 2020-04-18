---
title: Az Azure Active Directory-naplók streamelése az Azure Monitor naplóiba | Microsoft dokumentumok
description: Megtudhatja, hogy miként integrálható az Azure Active Directory-naplók az Azure Monitor-naplókkal
services: active-directory
documentationcenter: ''
author: MarkusVi
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
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d0ef55f6f5117ffa77052118155afea716125a4
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639723"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>Az Azure AD-naplók integrálása az Azure Monitor-naplókkal

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Az Azure Monitor naplói lehetővé teszik az adatok lekérdezését adott események megkereséséhez, trendek elemzéséhez és a különböző adatforrások közötti korreláció végrehajtásához. Az Azure AD-tevékenységnaplók integrálásával az Azure Monitor naplóiban mostantól olyan feladatokat hajthat végre, mint például:

 * Az Azure AD bejelentkezési naplóinak összehasonlítása az Azure Security Center által közzétett biztonsági naplókkal

 * Az Azure Application Insights alkalmazásteljesítmény-adatainak összefüggésével háríthatja el az alkalmazás teljesítménybeli szűk keresztmetszeteit az alkalmazás bejelentkezési lapján.  

Az alábbi videó egy Ignite-munkamenet bemutatja az Azure Monitor-naplók használatának előnyeit az Azure AD-naplók hoz gyakorlati felhasználói forgatókönyvekben.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

Ebben a cikkben megtudhatja, hogyan integrálhatja az Azure Active Directory (Azure AD) naplók at Az Azure Monitor.

## <a name="supported-reports"></a>Támogatott jelentések

A naplózási tevékenységnaplókat és a bejelentkezési tevékenységnaplókat további elemzés céljából átirányíthatja az Azure Monitor-naplókba. 

* **Auditnaplók**: Az [auditnaplók tevékenységjelentés](concept-audit-logs.md) hozzáférést nyújt a bérlőn elvégzett összes feladat előzményeihez.
* **Bejelentkezési naplók**: A [bejelentkezések tevékenységjelentéssel](concept-sign-ins.md) meghatározhatja, hogy ki hajtotta végre az auditnaplók által jelentett feladatokat.

> [!NOTE]
> A B2C-hez kapcsolódó audit- és bejelentkezési tevékenységnaplók jelenleg nem támogatottak.
>

## <a name="prerequisites"></a>Előfeltételek 

A szolgáltatás használatához a következőkre lesz szüksége:

* Azure-előfizetés. Ha nincs Azure-előfizetése, [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/free/).
* Egy Azure AD-bérlő.
* Egy felhasználó, aki az adott Azure AD-bérlő *globális* vagy *biztonsági rendszergazdája*.
* A Log Analytics munkaterület az Azure-előfizetésben. További információ a [Log Analytics-munkaterület létrehozásáról.](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace)

## <a name="licensing-requirements"></a>Licenckövetelmények

A funkció használatához Egy Azure AD Premium P1 vagy P2 licenc szükséges. A követelményeinek leginkább megfelelő licenc kiválasztásáról lásd [az ingyenes, alapszintű és prémium kiadások általánosan elérhető szolgáltatásait összehasonlító cikket](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="send-logs-to-azure-monitor"></a>Naplók küldése az Azure Monitornak

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

2. Válassza az **Azure Active Directory** > **diagnosztikai beállításai** -> **diagnosztikai beállítás hozzáadása diagnosztikai beállítás**lehetőséget. A **Naplózási naplók** vagy a Bejelentkezések lapon a Beállítások **exportálása** lehetőséget is választhatja a diagnosztikai beállítások **konfigurációs** lapján.  
    
3. A **Diagnosztikai beállítások** menüben jelölje be a **Küldés a Log Analytics munkaterületre jelölőnégyzetet,** majd kattintson a **Konfigurálás parancsra.**

4. Jelölje ki azt a Log Analytics-munkaterületet, amelynek el szeretné küldeni a naplókat, vagy hozzon létre egy új munkaterületet a megadott párbeszédpanelen.  

5. A következő lehetőségek egyikét vagy mindkettőt választhatja:
    * Ha naplónaplókat szeretne küldeni a Log Analytics-munkaterületre, jelölje be az **AuditLogs jelölőnégyzetet.** 
    * Ha bejelentkezési naplókat szeretne küldeni a Log Analytics-munkaterületre, jelölje be a **SignInLogs jelölőnégyzetet.**

6. A beállítás mentéséhez kattintson a **Mentés** gombra.

    ![Diagnosztikai beállítások](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Körülbelül 15 perc elteltével ellenőrizze, hogy az események streamelése a Log Analytics-munkaterületre.

## <a name="next-steps"></a>További lépések

* [Azure AD-tevékenységnaplók elemzése az Azure Monitor-naplókkal](howto-analyze-activity-logs-log-analytics.md)
* [Az Azure Active Directory naplóelemzési nézeteinek telepítése és használata](howto-install-use-log-analytics-views.md)
