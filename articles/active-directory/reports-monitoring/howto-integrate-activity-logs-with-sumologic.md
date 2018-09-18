---
title: Az Azure Monitor (előzetes verzió) használatával az Azure Active Directory-naplók integrálása SumoLogic |} A Microsoft Docs
description: Ismerje meg, hogyan SumoLogic az Azure Active Directory-naplók integrálása az Azure Monitor (előzetes verzió) használatával
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 2a90e70a9e7951b298be408992ee5e5fc6332131
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45729324"
---
# <a name="integrate-azure-ad-logs-with-sumologic-by-using-azure-monitor-preview"></a>Az Azure AD-naplók integrálása SumoLogic az Azure Monitor (előzetes verzió) használatával

Ebből a cikkből elsajátíthatja az Azure Active Directory (Azure AD) naplókban SumoLogic integrálása az Azure Monitor használatával. A naplók átirányítása az Azure event hubhoz, és majd integrálása az event hubs-SumoLogic.

## <a name="prerequisites"></a>Előfeltételek

A szolgáltatás használatához a következőkre lesz szüksége:
* Azure event hub, amely tartalmazza az Azure Active Directory-naplók. Ismerje meg, hogyan [adatfolyam a Tevékenységnaplók eseményközpontba](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Egy SumoLogic egyszeri bejelentkezés engedélyezve van az előfizetés.

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Az Azure AD-naplók integrálása SumoLogic lépések 

1. Először [Azure event hub, az Azure AD naplókat továbbíthat](quickstart-azure-monitor-stream-logs-to-event-hub.md).
2. Konfigurálja a SumoLogic [naplók gyűjtése az Azure Active Directory](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory).
3. [Telepítse az Azure AD SumoLogic alkalmazást](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) az előre konfigurált irányítópultokat, amelyek valós idejű elemzési környezet használata.

 ![Irányítópult](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>További lépések

* [Az auditnaplók sémájának értelmezése az Azure Monitorban](reference-azure-monitor-audit-log-schema.md)
* [A bejelentkezési naplók sémájának értelmezése az Azure Monitorban](reference-azure-monitor-sign-ins-log-schema.md)
* [Gyakori kérdések és ismert hibák](concept-activity-logs-in-azure-monitor.md#frequently-asked-questions)
