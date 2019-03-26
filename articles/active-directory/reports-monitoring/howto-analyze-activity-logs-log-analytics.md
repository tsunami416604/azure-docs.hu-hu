---
title: Az Azure Monitor-naplók (előzetes verzió) használatával az Azure Active Directory-Tevékenységnaplók elemzése |} A Microsoft Docs
description: Az Azure Monitor-naplók (előzetes verzió) használatával az Azure Active Directory-Tevékenységnaplók elemzése
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
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e195dd0e52b4938e829ea0aec1f4cc3ef7107c0
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436806"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs-preview"></a>Elemezheti az Azure ad-ben tevékenységeket tartalmazó naplók az Azure Monitor naplóira (előzetes verzió)

Miután [integrálása az Azure ad-ben tevékenységeket tartalmazó naplók az Azure Monitor naplóira](howto-integrate-activity-logs-with-log-analytics.md), a teljesítmény az Azure Monitor-naplók segítségével betekintést nyerhet a környezet. Emellett telepíthet a [Log analytics az Azure Active Directory lap megtekintései naplók](howto-install-use-log-analytics-views.md) érheti el a naplózási és bejelentkezési események körül előre elkészített jelentéseket a környezetben.

Ebből a cikkből elsajátíthatja, hogyan elemezheti az Azure AD-Tevékenységnaplók a Log Analytics-munkaterület. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek 

A lépések követéséhez le lesz szüksége:

* Az Azure-előfizetés a Log Analytics-munkaterületet. Ismerje meg, hogyan [hozzon létre egy Log Analytics-munkaterület](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Először a lépéseket követve [útvonal az Azure AD-Tevékenységnaplók Log Analytics-munkaterület az](howto-integrate-activity-logs-with-log-analytics.md).

## <a name="navigate-to-the-log-analytics-workspace"></a>Nyissa meg a Log Analytics-munkaterületet

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

2. Válassza ki **Azure Active Directory**, majd válassza ki **naplók** a a **figyelés** szakaszban nyissa meg a Log Analytics-munkaterületre. A munkaterület alapértelmezett lekérdezéssel nyílik meg.

    ![Alapértelmezett lekérdezéssel](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Az Azure Active Directory-séma megtekintéséhez naplók

A naplók kerüljenek a **Adatmodelltáblához** és **SigninLogs** táblák a munkaterületen. Ezek a táblák a séma megtekintéséhez:

1. Válassza ki az előző szakaszban az alapértelmezett lekérdezés nézet **séma** , és bontsa ki a munkaterületet. 

2. Bontsa ki a **Naplókezelés** szakaszt, és bontsa ki **Adatmodelltáblához** vagy **SignInLogs** a séma megtekintéséhez.
    ![Auditnaplók](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![bejelentkezési naplók](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Lekérdezés az Azure AD-Tevékenységnaplók

Most, hogy a naplók a munkaterületen, most már futtathatja lekérdezések őket. Helyettesítse be például a múlt héten használt felső alkalmazások lekéréséhez az alapértelmezett lekérdezéssel a következőket, majd válassza a **futtatása**

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

A legfontosabb naplózási események a múlt héten, használja a következő lekérdezést:

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Riasztás a tevékenységnapló adatainak Azure ad-ben

Is állíthat be riasztásokat a lekérdezés. Például, konfigurálhat egy riasztást, ha több mint 10 alkalmazások használtak az elmúlt hét:

1. A munkaterületen válassza **értesítés beállítása** megnyitásához a **létrehozás szabály** lapot.

    ![riasztás beállítása](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. Válassza az alapértelmezett **riasztási feltételek** frissítés és a riasztás jön létre a **küszöbérték** a 10-re az alapértelmezett metrikát.

    ![Riasztási feltételek](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. Adjon meg egy nevet és leírást a riasztást, és válassza ki a súlyossági szintet. A példánkban mi beállíthatja a **tájékoztató**.

4. Válassza ki a **műveletcsoport** , értesítést fog kapni a jel esetén. Dönthet úgy, hogy küldjön értesítést munkatársainak e-mailben vagy SMS-üzenetet, vagy webhookok, az Azure functions vagy a logic apps használatával a művelet sikerült automatizálja. Tudjon meg többet [létrehozása és kezelése az Azure Portalon csoportok riasztás](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

5. Miután konfigurálta a riasztást, válassza ki a **létrehozása riasztás** engedélyezze azt. 

## <a name="install-and-use-pre-built-views-for-azure-ad-activity-logs"></a>Telepítse, és előre elkészített nézetek használata az Azure Active Directory naplói

Is letöltheti az előre felépített log analytics az Azure Active Directory lap megtekintései naplókat. A nézetek adja meg a kapcsolódó gyakori helyzetek járó naplózási és bejelentkezési események több jelentést. Akkor is riasztja Önt bármely, a jelentések adatai az előző szakasz lépéseit követve.

* **Azure AD-fiók kiépítése események**: Ebben a nézetben látható a kiépítési tevékenységgel, például egy üzembe helyezett új felhasználóknak a számát, naplózás kapcsolatos jelentéseket kiépítési hibákhoz, azon felhasználók száma, frissítik és frissítési hibák és a szolgáltatáskulcs felhasználók és a megfelelő hibák számát.    
* **Bejelentkezési események**: Ebben a nézetben látható a leginkább releváns jelentések kapcsolódó bejelentkezési tevékenységeket, például az alkalmazás, felhasználó, eszköz, valamint nyomon követése a bejelentkezések száma idővel lemezkapacitásáról által bejelentkezések figyelése.
* **A felhasználók beleegyezést**: Ebben a nézetben látható a kapcsolódó felhasználói beleegyezés, jelentések, mint például a jóváhagyás a felhasználó, a felhasználók számára nyújtott hozzájárulási bejelentkezések, valamint a bejelentkezések a jóváhagyás-alapú alkalmazások összes alkalmazás által biztosít. 

Ismerje meg, hogyan [telepítése és a log analytics-nézetek használata az Azure Active Directory naplóinak](howto-install-use-log-analytics-views.md). 


## <a name="next-steps"></a>További lépések

* [Az Azure Monitor naplóira lekérdezések használatának első lépései](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)
* [Az Azure Portalon riasztási csoportok létrehozása és kezelése](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)
* [Telepítése és a log analytics-nézetek használata az Azure Active Directory](howto-install-use-log-analytics-views.md)
