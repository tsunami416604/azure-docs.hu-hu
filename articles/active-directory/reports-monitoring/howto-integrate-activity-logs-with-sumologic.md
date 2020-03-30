---
title: Naplók streamelése a SumoLogic szolgáltatásba az Azure Monitor használatával | Microsoft dokumentumok
description: Megtudhatja, hogy miként integrálható az Azure Active Directory-naplók a SumoLogic szolgáltatással az Azure Monitor használatával
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
ms.openlocfilehash: 6cdfc4e393ca7bf4bcbd523b4fad72690d5f2744
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014388"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor"></a>Az Azure Active Directory-naplók integrálása a SumoLogic szolgáltatással az Azure Monitor használatával

Ebben a cikkben megtudhatja, hogyan integrálhatja az Azure Active Directory (Azure AD) naplók at SumoLogic az Azure Monitor használatával. Először átirányítja a naplókat egy Azure-eseményközpontba, majd integrálja az eseményközpontot a SumoLogic-mal.

## <a name="prerequisites"></a>Előfeltételek

A szolgáltatás használatához a következőkre lesz szüksége:
* Az Azure-eseményközpont, amely tartalmazza az Azure AD-tevékenységnaplók. Megtudhatja, hogyan [streamelheti a tevékenységnaplókat egy eseményközpontba.](quickstart-azure-monitor-stream-logs-to-event-hub.md) 
* A SumoLogic egyszeri bejelentkezéssel rendelkező előfizetés.

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Az Azure AD-naplók És a SumoLogic integrálásának lépései 

1. Először [továbbítsa az Azure AD-naplókat egy Azure-eseményközpontba.](quickstart-azure-monitor-stream-logs-to-event-hub.md)
2. Konfigurálja úgy a SumoLogic-példányt, hogy [naplót gyűjtsön az Azure Active Directoryhoz.](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory)
3. [Telepítse az Azure AD SumoLogic alkalmazást](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) az előre konfigurált irányítópultok használatához, amelyek valós idejű elemzést biztosítanak a környezetről.

   ![Irányítópult](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>További lépések

* [Naplónaplók sémájának értelmezése az Azure Monitorban](reference-azure-monitor-audit-log-schema.md)
* [A bejelentkezési naplók sémájának értelmezése az Azure Monitorban](reference-azure-monitor-sign-ins-log-schema.md)
* [Gyakori kérdések és ismert hibák](concept-activity-logs-azure-monitor.md#frequently-asked-questions)