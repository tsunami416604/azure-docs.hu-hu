---
title: A gyakori riasztási séma integrálása a Logic Apps használatával
description: Ismerje meg, hogyan hozhat létre egy logikai alkalmazást, amely minden riasztás kezelése közös riasztási sémát.
author: ananthradhakrishnan
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: 13cb3880662e1665b03dd63f009645acbe97fc75
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734892"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>A gyakori riasztási séma integrálása a Logic Apps használatával

Ez a cikk bemutatja, hogyan hozhat létre egy logikai alkalmazást, amely minden riasztás kezelése közös riasztási sémát.

## <a name="overview"></a>Áttekintés

A [gyakori riasztási séma](https://aka.ms/commonAlertSchemaDocs) biztosít a szabványos és bővíthető JSON-séma minden más riasztási típus. Gyakori riasztási sémája a leghasznosabb, ha a webhookok, a runbookok és a logic Apps-alkalmazások programozott módon – ki. Ebben a cikkben bemutatjuk, hogyan egy-egy logikai alkalmazást hozhat létre minden riasztás kezelése. Ugyanezek az elvek alkalmazhatók az egyéb programozott módszerekkel. A logikai alkalmazás ebben a cikkben leírt jól definiált változókat hoz létre a ["fontos" mezők](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#essentials-fields), és azt is leírja, hogy miként kezelheti [riasztási típus](/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) specifikus logika.


## <a name="prerequisites"></a>Előfeltételek 

Ez a cikk feltételezi, hogy az olvasó már megszokott 
* Riasztási szabályok beállítása ([metrika](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric), [log](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log), [tevékenységnapló](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log))
* Beállítása [Műveletcsoportok](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* Engedélyezi a [gyakori riasztási séma](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) a Műveletcsoportok belül

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Hozzon létre egy logikai alkalmazást, kihasználva a gyakori riasztási séma

1. Kövesse a [a logikai alkalmazás létrehozása lépéseinek végrehajtása](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app). 

1.  Az eseményindító kiválasztása: **HTTP-kérés fogadásakor**.

    ![A logikai alkalmazások eseményindítói](media/action-groups-logic-app/logic-app-triggers.png "Logikaialkalmazás-triggerek")

1.  Válassza ki **szerkesztése** módosíthatja a HTTP-kérés eseményindító.

    ![HTTP-kérelem eseményindítók](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP-kérelem eseményindítók")


1.  Másolja és illessze be a következő mintát követik:

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

1. Válassza ki **+** **új lépés** majd **művelet hozzáadása**.

    ![Művelet hozzáadása](media/action-groups-logic-app/add-action.png "művelet hozzáadása")

1. Ezen a ponton többfajta összekötőt (Microsoft Teams, Slack, Salesforce stb.) a Speciális üzleti igények alapján adhat hozzá. A "lényeges mezők"-a-beépített is használhatja. 

    ![Alapvető mezők](media/alerts-common-schema-integrations/logic-app-essential-fields.png "alapvető mezők")
    
    Másik lehetőségként hozhat létre feltételes logika alapján a riasztási típus az "Expression" lehetőséget.

    ![Logic app kifejezés](media/alerts-common-schema-integrations/logic-app-expressions.png "Logic app kifejezés")
    
     A ["monitoringService" mező](/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) lehetővé teszi, hogy egyedi azonosítására a riasztási típus alapján a amely hozhat létre feltételes logikát.

    
    Ha például az alábbi kódrészlet ellenőrzi, ha a riasztás az Application Insights-alapú naplóriasztás, és ha igen jelenít meg a keresési eredmények között. Más esetben "NA" jelenít meg.

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Tudjon meg többet [logic app-kifejezések írása](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions).

    


## <a name="next-steps"></a>További lépések

* [További információ a műveletcsoportokról](../../azure-monitor/platform/action-groups.md).
* [További információ a gyakori riasztási séma](https://aka.ms/commonAlertSchemaDocs).

