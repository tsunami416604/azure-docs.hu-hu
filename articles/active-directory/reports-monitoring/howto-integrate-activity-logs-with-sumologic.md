---
title: Azure Active Directory-naplók az Azure Monitor (előzetes verzió) használatával SumoLogic Stream |} A Microsoft Docs
description: 'Útmutató: Azure Active Directory-naplók integrálása az Azure Monitor (előzetes verzió) használatával SumoLogic'
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
ms.openlocfilehash: 256b5d925bd924cbfdd08e9fb6600ea848fd2eb3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55195626"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor-preview"></a>Azure Active Directory-naplók integrálása az Azure Monitor (előzetes verzió) használatával SumoLogic

Ebből a cikkből elsajátíthatja az Azure Active Directory (Azure AD) naplókban integrálása az Azure Monitor használatával SumoLogic. A naplók átirányítása az Azure event hubhoz, és majd integrálása az event hubs-SumoLogic.

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
* [Gyakori kérdések és ismert hibák](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
