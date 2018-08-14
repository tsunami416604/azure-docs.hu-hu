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
ms.openlocfilehash: 50e7b7c35386bafe521078308a8809c4d3038dd8
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39501696"
---
# <a name="tutorial-stream-azure-ad-logs-to-an-azure-event-hub-preview"></a>Oktatóanyag: Azure AD-naplók streamelése Azure-eseményközpontba (előzetes verzió)

Az oktatóanyagból megtudhatja, hogyan konfigurálhatja úgy az Azure Monitort, hogy az Azure Active Directory- (Azure AD-) naplókat egy Azure-eseményközpontba streamelje. Ezzel a mechanizmussal integrálhatja a naplókat olyan külső biztonságiinformáció- és eseménykezelési (SIEM-) eszközökkel, mint a Splunk és a QRadar.

## <a name="prerequisites"></a>Előfeltételek 

A szolgáltatás használatához a következőkre lesz szüksége:

* Azure-előfizetés. Ha nincs Azure-előfizetése, [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/free/).
* Egy Azure AD-bérlő.
* Egy felhasználó, aki az adott Azure AD-bérlő *globális* vagy *biztonsági rendszergazdája*.
* Egy Event Hubs-névtér és -eseményközpont az Azure-előfizetésében. Megismerkedhet az [eseményközpont létrehozásának](https://docs.microsoft.com/azure/event-hubs/event-hubs-create.md) menetével.

## <a name="archive-logs-to-an-event-hub"></a>Naplók archiválása egy eseményközpontba

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

2. Válassza az **Azure Active Directory** > **Tevékenység** > **Auditnaplók** elemet. 

3. Válassza a **Beállítások exportálása** lehetőséget.  
    
4. A **Diagnosztikai beállítások** ablaktáblán a következő lehetőségek közül választhat:
    * Kattintson a **Beállítás szerkesztése** parancsra, ha a meglévő beállításokat szeretné szerkeszteni,
    * vagy a **Diagnosztikai beállítás hozzáadása** parancsra, ha egy új beállítást szeretne hozzáadni.  
      Legfeljebb három beállítás adható meg.

      ![Beállítások exportálása](./media/reporting-azure-monitor-diagnostics-azure-event-hub/ExportSettings.png)

5. Jelölje be a **Streamelés eseményközpontba** jelölőnégyzetet, majd válassza az **Eseményközpont/Konfigurálás** elemet.

6. Válassza ki, melyik Azure-előfizetésbe és Event Hubs-névtérbe kívánja átirányítani a naplókat.  
    Az előfizetésnek és az Event Hubs-névtérnek egyaránt azzal az Azure AD-bérlővel kell társítva lennie, amelyikről a naplók streamelve lesznek. Emellett megadhat egy eseményközpontot az Event Hubs-névtéren belül, amelyre a naplókat küldeni kell. Ha nem ad meg eseményközpontot, a rendszer létrehoz egyet a névtérben az alapértelmezett **insights-logs-audit** néven.

7. Az **OK** gombra kattintva lépjen ki az eseményközpont konfigurációs felületéről.

8. A következő lehetőségek egyikét vagy mindkettőt választhatja:
    * Jelölje be az **AuditLogs** (Naplófájlok) jelölőnégyzetet, ha az auditnaplókat szeretné továbbítani a tárfiókba. 
    * Jelölje be a **SignInLogs** (Bejelentkezési naplók) jelölőnégyzetet, ha a bejelentkezési naplókat szeretné továbbítani a tárfiókba.

9. A beállítás mentéséhez kattintson a **Mentés** gombra.

    ![Diagnosztikai beállítások](./media/reporting-azure-monitor-diagnostics-azure-event-hub/DiagnosticSettings.png)

10. Körülbelül 15 perc elteltével ellenőrizze, hogy az események megjelennek-e az eseményközpontban. Ehhez lépjen a portálon az eseményközpontra, és ellenőrizze, hogy a **bejövő üzenetek** számlálójának értéke nagyobb-e nullánál. 

    ![Naplók](./media/reporting-azure-monitor-diagnostics-azure-event-hub/InsightsLogsAudit.png)

## <a name="access-data-from-your-event-hub"></a>Adatok elérése az eseményközpontban

Miután az adatok megjelentek az eseményközpontban, kétféleképpen érheti el és olvashatja be azokat:

* **Támogatott SIEM-eszköz konfigurálásával**. Az adatok eseményközpontból való beolvasásához a legtöbb eszköz használata esetében szükség van az eseményközpont kapcsolati sztringjére és bizonyos jogosultságokra az Azure-előfizetésben. Az Azure Monitorral integrált, külső gyártótól származó eszközök közé többek között az alábbiak tartoznak:
    * **Splunk**: Az Azure AD-naplók a Splunkkal való integrálásával kapcsolatos további információkért lásd [az Azure AD-naplók a Splunkkal történő integrálását az Azure Monitor használatával](reporting-azure-monitor-diagnostics-splunk-integration.md) ismertető cikket.
    
    * **IBM QRadar**: Az eszközspecifikus modul (DSM) és az Azure Event Hub protokoll az [IBM támogatási webhelyéről](http://www.ibm.com/support) tölthető le. Az Azure-integrációról további információkat az [IBM QRadar Security Intelligence Platform 7.3.0](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0) webhelyén talál.
    
    * **SumoLogic**: Az [Azure-tevékenységnaplók eseményközpontból való összegyűjtésével](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub) foglalkozó részben leírtakat követve konfigurálhatja a SumoLogicot úgy, hogy egy eseményközpont adatait használja. 

* **Egyéni eszközök konfigurálásával**. Ha az Ön által használt SIEM-et az Azure Monitor-diagnosztika jelenleg még nem támogatja, az egyéni eszközök használatát az Event Hubs API használatával állíthatja be. További részletekért lásd [az eseményközpontból való üzenetfogadás első lépéseit](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph) ismertető cikket.


## <a name="next-steps"></a>További lépések

* [Azure AD-naplók integrálása a Splunkkal az Azure Monitor használatával](reporting-azure-monitor-diagnostics-splunk-integration.md)
* [Az auditnaplók sémájának értelmezése az Azure Monitorban](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [A bejelentkezési naplók sémájának értelmezése az Azure Monitorban](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
