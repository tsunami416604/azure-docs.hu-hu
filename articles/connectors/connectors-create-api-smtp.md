---
title: Az Azure Logic Apps összekötőt |} Microsoft Docs
description: Az Azure App service logic Apps alkalmazások létrehozása E-mail küldhető SMTP csatlakozni.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 516110abc1786d99bc719d47d61475cdc2ebcc4b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296067"
---
# <a name="get-started-with-the-smtp-connector"></a>Az SMTP-összekötő az első lépései
E-mail küldhető SMTP csatlakozni.

Használandó [a csatlakozókat](apis-list.md), először hozzon létre egy logikai alkalmazást. Elkezdheti által [logikai alkalmazás létrehozása most](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-smtp"></a>SMTP kapcsolódni
A Logic Apps alkalmazást bármely szolgáltatás hozzáférni, először hozzon létre egy *kapcsolat* a szolgáltatáshoz. A [kapcsolat](connectors-overview.md) biztosít a logikai alkalmazás és egy másik szolgáltatás közötti kapcsolat. Például szeretne csatlakozni az SMTP, először egy SMTP *kapcsolat*. VPN-kapcsolat létrehozásához adja meg a hitelesítő adatok általában segítségével éri el a szolgáltatást, hogy csatlakozni. Igen a SMTP példában adja meg a hitelesítő adatokat a kapcsolat neve, az SMTP-kiszolgáló címére és a felhasználói bejelentkezésekre vonatkozó információit az SMTP-kapcsolat létrehozásához.  

### <a name="create-a-connection-to-smtp"></a>Kapcsolatot létesíthet SMTP
> [!INCLUDE [Steps to create a connection to SMTP](../../includes/connectors-create-api-smtp.md)]
> 
> 

## <a name="use-an-smtp-trigger"></a>Az SMTP-eseményindító használata
Egy eseményindító nem egy eseményt, a logikai alkalmazás definiált munkafolyamat indításához használható. [További tudnivalók az eseményindítók](../logic-apps/logic-apps-overview.md#logic-app-concepts).

Ebben a példában a SMTP saját eseményindító nem rendelkezik. Igen, használja a **Salesforce - amikor létrejön egy objektum** eseményindító. Ehhez az eseményindítóhoz akkor aktiválódik, amikor új objektumot hoz létre a Salesforce-ban. Ehhez a példához létrehozott azt, hogy minden alkalommal új vezető jön létre a Salesforce, egy *e-mailek küldése* művelet következik be, az SMTP-összekötővel, egy értesítés, amely az új vezető létrehozás alatt áll.

1. Adja meg *salesforce* be a keresőmezőbe a logic apps designer válassza ki a **Salesforce - amikor létrejön egy objektum** eseményindító.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  
2. A **egy objektumának létrejöttekor** vezérlő jelenik meg.
   ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  
3. Válassza ki a **objektumtípus** válassza *vezethet* objektumok közül. Ebben a lépésben hoz létre egy eseményindítót, amely értesíti a Logic Apps alkalmazást, amikor egy új vezető jön létre a Salesforce-ban.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  
4. Az eseményindító létrehozása befejeződött.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## <a name="use-an-smtp-action"></a>Az SMTP-művelethez használata
Egy művelet során a logikai alkalmazás definiált munkafolyamat által végzett. [További információ a műveletek](../logic-apps/logic-apps-overview.md#logic-app-concepts).

Most, hogy az eseményindító hozzá lett adva, az alábbi lépések segítségével adja hozzá egy SMTP-művelet, amikor egy új vezető jön létre a Salesforce-ban.

1. Válassza ki **+ új lépés** hozzáadása a műveletet hajtson végre egy új vezető jön létre.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  
2. Válassza ki **művelet hozzáadása**. Ez a keresési mezőbe, ahol kereshet bármilyen műveletet meg szeretné igénybe megnyílik.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  
3. Adja meg *smtp* SMTP kapcsolatos műveletek kereséséhez.  
4. Válassza ki **SMTP - E-mail küldése** , a végrehajtandó műveletet, amikor az új vezető jön létre. A művelet blokk nyílik meg. A Tervező blokkban az smtp-kapcsolatot létesíteni, ha még nem meg korábban fog.  
   ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)    
5. Adjon meg a kívánt e-mailek adataihoz a **SMTP - E-mail küldése** blokkot.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  
6. Mentse a munkáját a munkafolyamat aktiválásához.  

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/smtpconnector/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k lista](apis-list.md).