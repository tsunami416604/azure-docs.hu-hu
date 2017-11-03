---
title: "Webhook riasztási műveleti minta az OMS szolgáltatáshoz |} Microsoft Docs"
description: "A műveletek futtatása a Log Analytics-riasztások válaszul egyik egy * webhook *, amely lehetővé teszi egyetlen HTTP-kérelem keresztül külső folyamat meghívni. Ez a cikk végigvezeti a Slackhez használatával Naplóelemzési figyelmeztető egy webhook művelet létrehozására láthat példát."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 13c39f0f-fd3c-472d-8324-ddf7538be45e
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: bwren
ms.openlocfilehash: 55b66132f7ec5c26c0a7cac1ec0a5c403dbd1082
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-alert-webhook-action-in-oms-log-analytics-to-send-message-to-slack"></a>Az OMS szolgáltatáshoz üzenetet küldeni a Slackhez riasztási webhook művelet létrehozása
Egy művelet futtatása válaszul egy [Naplóelemzési riasztás](log-analytics-alerts.md) van egy *webhook*, amely lehetővé teszi, hogy egyetlen HTTP-kérelem keresztül külső folyamat meghívni.  Riasztások és webhookokkal, a részleteket olvashat [Naplóelemzési riasztások](log-analytics-alerts.md)

Ebben a cikkben végigvezetjük a Slackhez, amely egy üzenetkezelési szolgáltatás használatával Naplóelemzési figyelmeztető egy webhook művelet létrehozására láthat példát.

> [!NOTE]
> Ez a minta befejezéséhez Slack fiókkal kell rendelkeznie.  Regisztrálhat egy ingyenes fiókot, [slack.com](http://slack.com).
> 
> 

## <a name="step-1---enable-webhooks-in-slack"></a>1. lépés – a Slackhez engedélyezése webhookok
1. Jelentkezzen be a Slackhez [slack.com](http://slack.com).
2. Válassza ki a csatorna a **csatornák** szakasz a bal oldali ablaktáblán.  Ez az a csatornát, amely az üzenetet kapnak.  Például válassza az alapértelmezett csatornák egyik **általános** vagy **véletlenszerű**.  Éles forgatókönyv esetében, akkor nagy valószínűséggel kell létrehoznia egy különös csatorna például **criticalservicealerts**. <br>
   
   ![Slack-csatornákon](media/log-analytics-alerts-webhooks/oms-webhooks01.png)
3. Kattintson a **hozzáadása egy alkalmazáshoz vagy egyéni integrációs** App könyvtár megnyitásához.
4. Típus *webhookok* a keresési mezőbe, majd válassza ki azt a **bejövő Webhookok**. <br>
   
   ![Slack-csatornákon](media/log-analytics-alerts-webhooks/oms-webhooks02.png)
5. Kattintson a **telepítése** a csoport neve mellett.
6. Kattintson a **konfiguráció hozzáadása**.
7. Válassza ki a a csatornát, amely ehhez a példához, és kattintson fog **bejövő Webhookok hozzáadása integrációs**.  
8. Másolás a **Webhook URL-CÍMÉT**.  Akkor lesz kell beillesztése Ez a riasztás konfigurálásában. <br>
   
    ![Slack-csatornákon](media/log-analytics-alerts-webhooks/oms-webhooks05.png)

## <a name="step-2---create-alert-rule-in-log-analytics"></a>2. lépés - a Naplóelemzési riasztási szabály létrehozása
1. [Riasztási szabályt létrehozni](log-analytics-alerts.md) a következő beállításokkal.
   * Lekérdezés:```    Type=Event EventLevelName=error ```
   * Riasztás keresésének minden: 5 perc
   * A találatok száma: nagyobb, mint 10
   * Ezen időszak alatt: 60 perc
   * Válassza ki **Igen** a **Webhook** és **nem** a más műveletekhez.
2. Illessze be a Slackhez URL-címet a **Webhook URL-CÍMÉT** mező.
3. Jelölje be a **közé tartoznak az egyéni JSON-adattartalmat**.
4. A tartalékidő vár a hasznos adatok között JSON formátumú, nevű paraméterrel *szöveg*.  Ez az a szöveg, akkor jelenítse meg az üzenet létrehozza.  Legalább egy, a riasztás paraméterek használatával is használhatja a  *#*  szimbólumának például az alábbi példában látható módon.
   
    ```
    {
    "text":"#alertrulename fired with #searchresultcount records which exceeds the over threshold of #thresholdvalue ."
    }
    ```
   
    ![Példa JSON-adattartalmat](media/log-analytics-alerts-webhooks/oms-webhooks07.png)
5. Kattintson a **mentése** menteni a riasztási szabályt.
6. Várjon, amíg elegendő időt hozható létre, és ezután ellenőrizze a Slackhez lesz a következőhöz hasonló üzenetet a riasztást.
   
   ![Példa webhook a Slackhez](media/log-analytics-alerts-webhooks/oms-webhooks08.png)

### <a name="advanced-webhook-payload-for-slack"></a>Speciális webhook hasznos a Slackhez
A Slackhez bejövő üzenetek nagymértékben testre szabható. További információkért lásd: [bejövő Webhookok](https://api.slack.com/incoming-webhooks) a Slack-webhelyen. Az alábbiakban olvashat egy összetettebb hasznos formázással gazdag üzenet létrehozása:

    {
        "attachments": [
            {
                "title":"OMS Alerts Custom Payload",
                "fields": [
                    {
                        "title": "Alert Rule Name",
                        "value": "#alertrulename"},
                    {
                        "title": "Link To SearchResults",
                        "value": "<#linktosearchresults|OMS Search Results>"},
                    {
                        "title": "Search Interval",
                        "value": "#searchinterval"},
                    {
                        "title": "Threshold Operator",
                        "value": "#thresholdoperator"},
                    {
                        "title": "Threshold Value",
                        "value": "#thresholdvalue"}
                ],
                "color": "#F35A00"
            }
        ]
    }


Ez egy üzenetet hoz létre a Slackhez a következőhöz hasonló.

![a Slackhez példa üzenet](media/log-analytics-alerts-webhooks/oms-webhooks09.png)

## <a name="summary"></a>Összefoglalás
A riasztási szabálynak helyen egy üzenet slackhez minden alkalommal, amikor a feltétel teljesülése esetén kellene lennie.  

Ez az egy műveletet, amely riasztást válaszul hozhat létre csak egy példája.  Létrehozhat olyan webhook művelet, amely meghívja a egy másik külső szolgáltatást, egy elindít egy forgatókönyvet az Azure Automation-runbook műveletet vagy egy e-mailt küldjön magának vagy a többi címzett e-mail műveletet.   

## <a name="next-steps"></a>Következő lépések
* Tudnivalók más [Naplóelemzési műveletek riasztási](log-analytics-alerts-actions.md) beleértve más műveletek.


