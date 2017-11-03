---
title: "A Slackhez Connector használata az Azure logic Apps alkalmazásait |} Microsoft Docs"
description: "A logic apps a csatlakozás a Slackhez"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: fc5fc128efe01bd0727e3ff30d8938918e89ac3a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-slack-connector"></a>A Slack-összekötő az első lépései
A Slack egy csoportos kommunikációs eszköz, amely a csoporton belüli összes kommunikációt egy helyre fogja össze. Ez a hely azonnal kereshető, és bárhonnan elérhető. 

Első lépések egy logikai alkalmazás létrehozása most; Lásd: [logikai alkalmazás létrehozása](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-slack"></a>Kapcsolatot létesíthet a Slackhez
A Slack-összekötő használatához először létre kell hoznia egy **kapcsolat** adja meg a részleteket a tulajdonságok: 

| Tulajdonság | Szükséges | Leírás |
| --- | --- | --- |
| Token |Igen |Adja meg a Slack hitelesítő adatok |

Kövesse az alábbi lépéseket követve jelentkezzen be a Slackhez, és konfigurációjához a Slackhez, **kapcsolat** a logikai alkalmazásban:

1. Válassza ki **ismétlődési**
2. Válassza ki a **gyakoriság** , és írja be egy **időköz**
3. Válassza ki **művelet hozzáadása**  
   ![Konfigurálja a Slackhez][1]  
4. A keresőmezőbe írja be a Slackhez, és várja meg, a Keresés a Slackhez összes bejegyzés a nevét adja vissza
5. Válassza ki **Slackhez - üzenet közzététele**
6. Válassza ki **jelentkezzen be a Slackhez**:  
   ![Konfigurálja a Slackhez][2]
7. Jelentkezzen be az alkalmazás a Slack hitelesítő adatok megadása    
   ![Konfigurálja a Slackhez][3]  
8. Meg kell átirányítani a szervezet bejelentkezési oldalán. **Engedélyezi** Slackhez, amellyel kommunikálni tud a Logic Apps alkalmazást:      
   ![Konfigurálja a Slackhez][5] 
9. Az engedélyezés befejezése után meg fogja átirányítani a Logic Apps alkalmazást befejezéseként konfigurálja a **Slack - összes üzenet** szakasz. Vegyen fel más eseményindítók és műveletek, amelyekre szüksége van.  
   ![Konfigurálja a Slackhez][6]
10. Mentse a munkáját kiválasztásával **mentése** fenti menüsávjában.

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/slack/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k lista](apis-list.md).

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png
