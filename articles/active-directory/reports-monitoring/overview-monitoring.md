---
title: Mit jelent az Azure Active Directory monitorozása? | Microsoft Docs
description: Ez a cikk általános áttekintést nyújt az Azure Active Directory-monitorozásról.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d63f8440ca527a746f73574bc156037d85fc3599
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "68988209"
---
# <a name="what-is-azure-active-directory-monitoring"></a>Mit jelent az Azure Active Directory monitorozása?

Az Azure Active Directory (Azure AD) monitorozásával mostantól különböző végpontokra irányíthatja az Azure AD-tevékenységnaplókat. Ezután megőrizheti őket hosszú távú használatra, vagy külső biztonságiinformáció- és eseménykezelési (SIEM-) eszközökkel integrálva elemezheti a környezetet.

A naplókat jelenleg a következő helyekre irányíthatja:

- Egy Azure-tárfiók.
- Egy Azure-eseményközpontba, amelyen keresztül integrálhatja őket a Splunk- és Sumologic-példányokba.
- Egy Azure Log Analytics-munkaterületre, ahol elemezheti az adatokat, irányítópultokat készíthet, és riasztásokat hozhat létre a specifikus eseményekhez.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="diagnostic-settings-configuration"></a>Diagnosztikai beállítások konfigurálása

Az Azure AD-tevékenységnaplók monitorozási beállításainak konfigurálásához először jelentkezzen be az [Azure Portalra](https://portal.azure.com), majd válassza az **Azure Active Directory** lehetőséget. Innen kétféleképpen érheti el a diagnosztikai beállítások konfigurációs oldalát:

* Válassza a **Diagnosztikai beállítások** lehetőséget a **Monitorozás** szakaszban.

    ![Diagnosztikai beállítások](./media/overview-monitoring/diagnostic-settings.png)
    
* Válassza az **Auditnaplók** vagy a **Bejelentkezések**, majd a **Beállítások exportálása** lehetőséget. 

    ![Beállítások exportálása](./media/overview-monitoring/export-settings.png)


## <a name="route-logs-to-storage-account"></a>Naplók átirányítása egy tárfiókba

Ha egy Azure-tárfiókba irányítja a naplókat, ott az [adatmegőrzési szabályzatainkban](reference-reports-data-retention.md) vázolt alapértelmezett megőrzési időtartamnál hosszabb ideig megőrizheti őket. További információ [az adatok a tárfiókokba való irányításáról](quickstart-azure-monitor-route-logs-to-storage-account.md).

## <a name="stream-logs-to-event-hub"></a>Naplók streamelése egy eseményközpontba

Ha egy Azure-eseményközpontba irányítja a naplókat, olyan külső SIEM-eszközökkel integrálhatja őket, mint a Sumologic és a Splunk. Ez az integráció lehetővé teszi az Azure AD-tevékenységnapló-adatok és a SIEM által kezelt egyéb adatok kombinálását, hogy gazdagabb betekintést nyújtson a környezetbe. További információ [a naplók eseményközpontokba való streameléséről](tutorial-azure-monitor-stream-logs-to-event-hub.md).

## <a name="send-logs-to-azure-monitor-logs"></a>Naplók küldése az Azure Monitor-naplókba

[Az Azure Monitor naplói](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) egy olyan megoldás, amely egyesíti a figyelési adatokat a különböző forrásokból, és biztosítja a lekérdezési nyelv és elemzési motor, amely betekintést nyújt az alkalmazások és erőforrások működésébe. Az Azure AD-tevékenységnaplók nak az Azure Monitor-naplókba való elküldésével gyorsan lekérheti, figyelheti és riasztást adhat az összegyűjtött adatokról. Megtudhatja, hogyan [küldhet adatokat az Azure Monitor naplóiba.](howto-integrate-activity-logs-with-log-analytics.md)

Az Azure AD előre elkészített nézeteinek telepítésével monitorozhatja az olyan gyakori eseményeket, mint a bejelentkezések vagy a naplóesemények. Megtudhatja, hogyan [telepítheti és használhatja az Azure AD-tevékenységnaplók naplóinak naplóelemzési nézeteit.](howto-install-use-log-analytics-views.md)

## <a name="next-steps"></a>További lépések

* [Tevékenységnaplók az Azure Monitorban](concept-activity-logs-azure-monitor.md)
* [Naplók streamelése egy eseményközpontba](tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Naplók küldése az Azure Monitor-naplókba](howto-integrate-activity-logs-with-log-analytics.md)