---
title: Oktatóanyag – Azure Active Directory-naplók streamelése egy Azure-eseményközpontba (előzetes verzió) | Microsoft Docs
description: Itt megtudhatja, hogyan állíthatja be az Azure Diagnosticsban az Azure Active Directory-naplók leküldését egy eseményközpontba (előzetes verzió)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: c4f5bf1b49d7f59b2bb938a7ddc53b96c1d354ef
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240216"
---
# <a name="tutorial-stream-azure-active-directory-logs-to-an-azure-event-hub-preview"></a>Oktatóanyag: Azure Active Directory-naplók streamelése Azure eseményközpontba (előzetes verzió)

Az oktatóanyagból megtudhatja, hogyan konfigurálhatja úgy az Azure Monitort, hogy az Azure Active Directory-naplókat egy Azure-eseményközpontba streamelje. Ezzel a mechanizmussal integrálhatja a naplókat olyan külső SIEM-eszközökkel, mint a Splunk és a QRadar.

## <a name="prerequisites"></a>Előfeltételek 

A következők szükségesek:

* Azure-előfizetés. Ha nincs Azure-előfizetése, [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/free/).
* Egy Azure Active Directory-bérlő
* Egy felhasználó, aki az adott bérlő globális vagy biztonsági rendszergazdája
* Egy Azure Event Hubs-névtér és -eseményközpont az Azure-előfizetésében. Tekintse meg, hogyan [hozhat létre egyet](https://docs.microsoft.com/azure/event-hubs/event-hubs-create.md).

## <a name="archive-logs-to-event-hub"></a>Naplók archiválása egy Event Hubs-eseményközpontba

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Kattintson az **Azure Active Directory** -> **Tevékenység** -> **Auditnaplók** elemre. 
3. A **Beállítások exportálása** gombra kattintva nyissa meg a Diagnosztikai beállítások panelt. Kattintson a **Beállítás szerkesztése** gombra, ha a meglévő beállításokat szeretné szerkeszteni, vagy a **Diagnosztikai beállítás hozzáadása** gombra, ha egy új beállítást szeretne hozzáadni. Legfeljebb három beállítás adható meg. 
    ![Beállítások exportálása](./media/reporting-azure-monitor-diagnostics-azure-event-hub/ExportSettings.png "Beállítások exportálása")

4. Jelölje be a **Streamelés eseményközpontba** jelölőnégyzetet, és kattintson az **Eseményközpont/Konfigurálás** elemre.
5. Válassza ki, melyik Azure-előfizetésbe és Event Hubs-névtérbe kívánja átirányítani a naplókat. Az előfizetésnek és az Event Hubs-névtérnek egyaránt azzal az Active Directory-bérlővel kell társítva lennie, amelyikről a naplók streamelve lesznek. Emellett megadhat egy eseményközpontot az Event Hubs-névtéren belül, amelyre a naplókat küldeni kell. Ha nem ad meg eseményközpontot, a rendszer létrehoz egyet a névtérben az alapértelmezett **insights-logs-audit** néven.
6. Az **OK** gombra kattintva lépjen ki az eseményközpont konfigurációs felületéről.
7. Jelölje be az **AuditLogs** (Naplófájlok) jelölőnégyzetet, ha az auditnaplókat szeretné továbbítani a tárfiókba. 
8. Jelölje be a **SignInLogs** (Bejelentkezési naplók) jelölőnégyzetet, ha a bejelentkezési naplókat szeretné továbbítani a tárfiókba.
9. Kattintson a **Mentés** gombra a beállítás mentéséhez.
    ![Diagnosztikai beállítások](./media/reporting-azure-monitor-diagnostics-azure-event-hub/DiagnosticSettings.png "Diagnosztikai beállítások")

10. Körülbelül 15 perc elteltével ellenőrizze, hogy az események megjelennek-e az eseményközpontban. Ehhez lépjen a portálon az eseményközpontra, és ellenőrizze, hogy a **bejövő üzenetek** számláló értéke nagyobb, mint nulla. 
    ![Naplók](./media/reporting-azure-monitor-diagnostics-azure-event-hub/InsightsLogsAudit.png "Naplók")


## <a name="access-data-from-event-hubs"></a>Adatok elérése az Event Hubsban

Miután az adatok megjelentek az eseményközpontban, kétféleképp érheti el őket.

* **Támogatott SIEM-eszközök konfigurálása az adatok olvasására**: A legtöbb eszköz használata esetében szükség van az eseményközpont kapcsolati sztringjére és bizonyos jogosultságokra az Azure-előfizetésben az adatok az eseményközpontból való beolvasásához. Íme az Azure Monitorral integrált eszközök nem kizárólagos listája:
    1. **Splunk**: Az Azure AD-naplók a Splunkkal való integrálásával kapcsolatos további információkért lásd: [Azure Active Directory-naplók integrálása a Splunkkal az Azure Monitor használatával](reporting-azure-monitor-diagnostics-splunk-integration.md).
    
    2. **IBM QRadar**: A Microsoft Azure eszközspecifikus modul (DSM) és a Microsoft Azure Event Hub protokoll az [IBM támogatási webhelyéről](http://www.ibm.com/support) tölthető le. További információkat [az Azure-ral való integrációról itt talál](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
    
    3. **SumoLogic**: [Ezeket az utasításokat](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub) követve konfigurálhatja a SumoLogicot, hogy egy eseményközpont adatait használja. 

* **Egyéni eszközök konfigurálása az adatok olvasására**: Ha az Ön által használt SIEM-et az Azure Monitor-diagnosztika jelenleg még nem támogatja, az egyéni eszközök használatát az Event Hubs API-k használatával állíthatja be. További részletekért lásd [az Event Hubs API-kat ismertető cikket](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph).


## <a name="next-steps"></a>További lépések

* [Azure Active Directory-naplók integrálása a Splunkkal az Azure Monitor diagnosztikája használatával](reporting-azure-monitor-diagnostics-splunk-integration.md)
* [Az auditnaplók sémájának értelmezése az Azure Monitor diagnosztikájában](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [A bejelentkezési naplók sémájának értelmezése az Azure Monitor diagnosztikájában](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)