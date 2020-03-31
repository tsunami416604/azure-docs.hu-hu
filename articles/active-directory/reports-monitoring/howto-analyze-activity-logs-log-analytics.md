---
title: Tevékenységnaplók elemzése az Azure Monitor naplóival | Microsoft dokumentumok
description: Az Azure Active Directory-tevékenységnaplók azure-figyelőnaplók használatával történő elemzésének megismerése
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
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
ms.openlocfilehash: 2d6212692465270182db541889bed5f03a08a345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008275"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>Azure AD-tevékenységnaplók elemzése az Azure Monitor-naplókkal

Miután [integrálja az Azure AD-tevékenységnaplókat az Azure Monitor-naplókkal,](howto-integrate-activity-logs-with-log-analytics.md)az Azure Monitor-naplók segítségével betekintést nyerhet a környezetébe. Az [Azure AD-tevékenységnaplók naplóinak naplóelemzési nézeteiis](howto-install-use-log-analytics-views.md) telepítheti kitaszíthatók a környezetében a naplózási és bejelentkezési események ről szóló, előre elkészített jelentésekhez.

Ebben a cikkben megtudhatja, hogyan elemezheti az Azure AD-tevékenységnaplók at a Log Analytics-munkaterületen. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek 

Követni, akkor szükség van:

* A Log Analytics munkaterület az Azure-előfizetésben. További információ a [Log Analytics-munkaterület létrehozásáról.](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace)
* Először hajtsa végre az [Azure AD-tevékenységnaplók a Log Analytics-munkaterületre való továbbításához](howto-integrate-activity-logs-with-log-analytics.md)szükséges lépéseket.
*  [Hozzáférés](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) a naplóelemzési munkaterülethez
* A következő szerepkörök az Azure Active Directoryban (ha az Azure Active Directory portálon keresztül éri el a Log Analytics szolgáltatást)
    - Biztonsági rendszergazda
    - Biztonsági olvasó
    - Jelentésolvasó
    - Globális rendszergazda
    
## <a name="navigate-to-the-log-analytics-workspace"></a>Navigálás a Log Analytics-munkaterületre

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) 

2. Válassza az **Azure Active Directory**t, majd válassza a **Naplók** a **Figyelés** szakaszban a Log Analytics-munkaterület megnyitásához. A munkaterület egy alapértelmezett lekérdezéssel nyílik meg.

    ![Alapértelmezett lekérdezés](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Az Azure AD-tevékenységnaplók sémamegtekintése

A naplók a munkaterület **AuditLogs** és **SigninLogs tábláiba** kerülnek. A táblák sémájának megtekintése:

1. Az előző szakasz alapértelmezett lekérdezési nézetében válassza a **Séma** lehetőséget, és bontsa ki a munkaterületet. 

2. Bontsa ki a **Naplókezelés** **szakaszt, majd bontsa** ki az AuditLogs vagy a **SignInLogs csomópontot** a naplóséma megtekintéséhez.
    ![Naplók naplózása](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![Bejelentkezési naplók](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Az Azure AD-tevékenységnaplók lekérdezése

Most, hogy a naplók a munkaterületen, most már futtathat lekérdezéseket ellenük. Ha például az elmúlt héten használt legnépszerűbb alkalmazásokat szeretné beszerezni, cserélje le az alapértelmezett lekérdezést a következőkre, és válassza a **Futtatás lehetőséget.**

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

Az elmúlt hét legfontosabb naplózási eseményeinek lekérdezéséhez használja a következő lekérdezést:

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Riasztás az Azure AD tevékenységnaplóadatairól

Riasztásokat is beállíthat a lekérdezésben. Például egy riasztás konfigurálásához, ha az elmúlt héten több mint 10 alkalmazást használtak:

1. A munkaterületen válassza a **Riasztás beállítása** lehetőséget a **Szabály létrehozása** lap megnyitásához.

    ![Riasztás beállítása](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. Válassza ki a riasztásban létrehozott alapértelmezett **riasztási feltételeket,** és frissítse a **Küszöbértéket** az alapértelmezett metrikában 10-re.

    ![Riasztási feltételek](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. Adja meg a riasztás nevét és leírását, és válassza ki a súlyossági szintet. A mi példánkban, mi is meg, hogy **információs**.

4. Válassza ki azt a **műveletcsoportot,** amely a jel bekövetkeztekor figyelmeztetést kap. Dönthet úgy, hogy e-mailben vagy szöveges üzenetben értesíti a csapatot, vagy automatizálhatja a műveletet webhookok, Azure-függvények vagy logikai alkalmazások használatával. További információ [a riasztási csoportok létrehozásáról és kezeléséről az Azure Portalon.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)

5. Miután konfigurálta a riasztást, válassza a **Riasztás létrehozása** lehetőséget az engedélyezéséhez. 

## <a name="install-and-use-pre-built-views-for-azure-ad-activity-logs"></a>Az Azure AD tevékenységnaplóinak előre elkészített nézeteinek telepítése és használata

Az Azure AD-tevékenységnaplók előre elkészített naplóelemzési nézeteket is letölthet. A nézetek számos jelentést tartalmaznak a naplózási és bejelentkezési eseményeket érintő gyakori forgatókönyvekhez. Az előző szakaszban ismertetett lépések segítségével a jelentésekben megadott adatok bármelyikéről is riasztást kaphat.

* **Azure AD-fiók létesítési események:** Ez a nézet a naplózási létesítési tevékenységgel kapcsolatos jelentéseket jeleníti meg, például a kiépített új felhasználók számát és a kiépítési hibákat, a frissített felhasználók számát és a frissítési hibákszámát, valamint a kiépített és a megfelelő hibák számát.    
* **Bejelentkezési események:** Ez a nézet a bejelentkezési tevékenység figyelésével kapcsolatos legfontosabb jelentéseket jeleníti meg, például az alkalmazás, a felhasználó, az eszköz, valamint a bejelentkezések számát az idő múlásával nyomon követő összefoglaló nézetet.
* **Beleegyezést teljesítő felhasználók:** Ez a nézet a felhasználói hozzájárulással kapcsolatos jelentéseket jeleníti meg, például a felhasználó által kiadott hozzájárulási engedélyeket, a beleegyezést megadó felhasználók bejelentkezéseit, valamint az alkalmazásonkénti bejelentkezéseket az összes hozzájáruláson alapuló alkalmazáshoz. 

Megtudhatja, hogyan [telepítheti és használhatja az Azure AD-tevékenységnaplók naplóinak naplóelemzési nézeteit.](howto-install-use-log-analytics-views.md) 


## <a name="next-steps"></a>További lépések

* [A lekérdezések első lépései az Azure Monitor naplóiban](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)
* [Riasztási csoportok létrehozása és kezelése az Azure Portalon](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)
* [Az Azure Active Directory naplóelemzési nézeteinek telepítése és használata](howto-install-use-log-analytics-views.md)
