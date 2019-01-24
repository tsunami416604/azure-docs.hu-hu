---
title: Azure Active Directory-naplók integrálása az Azure Monitor (előzetes verzió) használatával SumoLogic |} A Microsoft Docs
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
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 164026cc1abea43760d06024ded5c083a92160f4
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810601"
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
