---
title: Azure Active Directory-naplók integrálása az Azure Monitor (előzetes verzió) használatával Splunk |} A Microsoft Docs
description: 'Útmutató: Azure Active Directory-naplók integrálása az Azure Monitor (előzetes verzió) használatával Splunk'
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: c4b605b6-6fc0-40dc-bd49-101d03f34665
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d1dd62d06c7e3ed634795604ce9660694ea073ca
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239919"
---
# <a name="integrate-azure-active-directory-logs-with-splunk-using-azure-monitor-preview"></a>Azure Active Directory-naplók integrálása az Azure Monitor (előzetes verzió) használatával Splunk

Ebben a cikkben megismerheti a Azure Active Directory-naplók integrálása az Azure Monitor használatával Splunk lesz. Először is kell a naplók átirányítása egy Azure-eseményközpont, és integrálhatja a Splunk.

## <a name="prerequisites"></a>Előfeltételek

1. Az Azure Event Hubs az Azure AD-tevékenységet tartalmazó naplózza. Ismerje meg, hogyan [adatfolyam a Tevékenységnaplók eseményközpontba](reporting-azure-monitor-diagnostics-azure-event-hub.md). 
2. Használja a következő [utasításokat](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md) töltse le az Azure monitor bővítmény Splunk, és a Splunk-példány konfigurálását.

## <a name="tutorial"></a>Oktatóanyag 

1. Nyissa meg a Splunk-példány, és kattintson a **adatok összegzése**.
    ![Adatok összegzése](./media/reporting-azure-monitor-diagnostics-splunk-integration/DataSummary.png "adatok összegzése")

2. Keresse meg a **Sourcetypes** lapot, és válasszon **amal: aadal:audit** ![Sourcetypes lapon](./media/reporting-azure-monitor-diagnostics-splunk-integration/sourcetypeaadal.png "Sourcetypes lap")

3. Látni fogja az Azure AD-Tevékenységnaplók az alábbi ábrán látható módon.
    ![A Tevékenységnaplók](./media/reporting-azure-monitor-diagnostics-splunk-integration/activitylogs.png "tevékenységeket tartalmazó naplók")

> [!NOTE]
> Ha kiegészítő szolgáltatást nem lehet telepíteni a Splunk-példány (például, ha proxyt használ, vagy a Splunk-felhőben futó), is továbbíthatja ezeket az eseményeket a Splunk HTTP Eseménygyűjtő, ez [lévő üzenetek által aktivált Azure-függvény a Event hubs](https://github.com/Microsoft/AzureFunctionforSplunkVS). " 
>

## <a name="next-steps"></a>További lépések

* [Az Azure monitorban naplózási naplók séma értelmezése](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Az Azure monitor séma bejelentkezési naplók értelmezése](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [– Gyakori kérdések és ismert problémák](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)