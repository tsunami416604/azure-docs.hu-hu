---
title: A gyakori riasztási séma integrálása a Logic Apps alkalmazásokkal
description: Ismerje meg, hogyan hozhat létre egy logikai alkalmazást, amely a közös riasztási sémát használja az összes riasztás kezeléséhez.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/27/2019
ms.openlocfilehash: 9042ed8ddbb698192e638fa7538f74561574c262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668230"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>A gyakori riasztási séma integrálása a Logic Apps alkalmazásokkal

Ez a cikk bemutatja, hogyan hozhat létre egy logikai alkalmazást, amely a gyakori riasztási sémát használja az összes riasztás kezeléséhez.

## <a name="overview"></a>Áttekintés

A [közös riasztási séma](https://aka.ms/commonAlertSchemaDocs) szabványosított és bővíthető JSON-sémát biztosít a különböző riasztási típusok között. A közös riasztási séma akkor a leghasznosabb, ha programozott módon – webhookokon, runbookokon és logikai alkalmazásokon keresztül – a leghasznosabb. Ebben a cikkben bemutatjuk, hogyan lehet egyetlen logikai alkalmazást az összes riasztás kezeléséhez. Ugyanezek az elvek alkalmazhatók más programozott módszerekre is. A cikkben ismertetett logikai alkalmazás jól definiált változókat hoz létre az ["alapvető" mezőkhöz,](alerts-common-schema-definitions.md#essentials)és azt is leírja, hogyan kezelhető [a riasztástípus-specifikus](alerts-common-schema-definitions.md#alert-context) logika.


## <a name="prerequisites"></a>Előfeltételek 

Ez a cikk azt feltételezi, hogy az olvasó ismeri a 
* Riasztási szabályok beállítása ([metrika](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric), [napló](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log), [tevékenységnapló](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log))
* [Műveletcsoportok](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) beállítása
* A [közös riasztási séma](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) engedélyezése műveletcsoportokon belülről

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>A közös riasztási sémát kihasználva logikai alkalmazás létrehozása

1. A [logikai alkalmazás létrehozásához kövesse a felvázolt lépéseket.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app) 

1.  Válassza ki az eseményindítót: **HTTP-kérelem érkezésekor**.

    ![A logikai alkalmazások eseményindítói](media/action-groups-logic-app/logic-app-triggers.png "A logikai alkalmazások eseményindítói")

1.  A **HTTP-kérelem** eseményindítójának módosításához válassza a Szerkesztés lehetőséget.

    ![HTTP-kérelem eseményindítói](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP-kérelem eseményindítói")


1.  Másolja és illessze be a következő sémát:

    ```json
        {
            "type": "object",
            "properties": {
                "schemaId": {
                    "type": "string"
                },
                "data": {
                    "type": "object",
                    "properties": {
                        "essentials": {
                            "type": "object",
                            "properties": {
                                "alertId": {
                                    "type": "string"
                                },
                                "alertRule": {
                                    "type": "string"
                                },
                                "severity": {
                                    "type": "string"
                                },
                                "signalType": {
                                    "type": "string"
                                },
                                "monitorCondition": {
                                    "type": "string"
                                },
                                "monitoringService": {
                                    "type": "string"
                                },
                                "alertTargetIDs": {
                                    "type": "array",
                                    "items": {
                                        "type": "string"
                                    }
                                },
                                "originAlertId": {
                                    "type": "string"
                                },
                                "firedDateTime": {
                                    "type": "string"
                                },
                                "resolvedDateTime": {
                                    "type": "string"
                                },
                                "description": {
                                    "type": "string"
                                },
                                "essentialsVersion": {
                                    "type": "string"
                                },
                                "alertContextVersion": {
                                    "type": "string"
                                }
                            }
                        },
                        "alertContext": {
                            "type": "object",
                            "properties": {}
                        }
                    }
                }
            }
        }
    ```

1. Válassza **+** **az Új lépés,** majd a Művelet **hozzáadása**lehetőséget.

    ![Művelet hozzáadása](media/action-groups-logic-app/add-action.png "Művelet hozzáadása")

1. Ebben a szakaszban számos összekötőt adhat hozzá (Microsoft Teams, Slack, Salesforce stb.) az adott üzleti követelmények alapján. Használhatja az "alapvető mezők" out-of-the-box. 

    ![Alapvető területek](media/alerts-common-schema-integrations/logic-app-essential-fields.png "Alapvető területek")
    
    Másik lehetőségként feltételes logikát is létrehozhat a riasztás típusa alapján a "Kifejezés" beállítás használatával.

    ![Logikai alkalmazás kifejezése](media/alerts-common-schema-integrations/logic-app-expressions.png "Logikai alkalmazás kifejezése")
    
     A ["monitoringService" mező](alerts-common-schema-definitions.md#alert-context) lehetővé teszi, hogy egyedileg azonosítsa a riasztás típusát, amely alapján létrehozhatja a feltételes logikát.

    
    Az alábbi kódrészlet például azt ellenőrzi, hogy a riasztás egy Application Insights-alapú naplóriasztás-e, és ha igen, kinyomtatja a keresési eredményeket. Különben "NA"-t nyomtat.

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     További információ a [logikai alkalmazáskifejezések írásáról.](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions)

    


## <a name="next-steps"></a>További lépések

* [További információ a műveletcsoportokról](../../azure-monitor/platform/action-groups.md).
* [További információ a gyakori riasztási sémáról.](https://aka.ms/commonAlertSchemaDocs)

