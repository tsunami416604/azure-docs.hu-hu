---
title: "Az Azure Logic Apps összekötőt |} Microsoft Docs"
description: "Az Azure App service logic Apps alkalmazások létrehozása Az SMTP protokoll e-mailek továbbítására szolgál."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 1cf96bbf8bd215d7ddb3c99860a5cb4e668be3c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-smtp-connector"></a>Az SMTP-összekötő az első lépései
Az SMTP protokoll e-mailek továbbítására szolgál.

Használandó [a csatlakozókat](apis-list.md), először hozzon létre egy logikai alkalmazást. Elkezdheti által [logikai alkalmazás létrehozása most](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-smtp"></a>SMTP kapcsolódni
A Logic Apps alkalmazást bármely szolgáltatás hozzáférni, először hozzon létre egy *kapcsolat* a szolgáltatáshoz. A [kapcsolat](connectors-overview.md) biztosít a logikai alkalmazás és egy másik szolgáltatás közötti kapcsolat. Például szeretne csatlakozni az SMTP, először egy SMTP *kapcsolat*. VPN-kapcsolat létrehozásához adja meg a hitelesítő adatok általában segítségével éri el a szolgáltatást, hogy csatlakozni. Igen a SMTP példában adja meg a hitelesítő adatokat a kapcsolat neve, az SMTP-kiszolgáló címére és a felhasználói bejelentkezésekre vonatkozó információit az SMTP-kapcsolat létrehozásához.  

### <a name="create-a-connection-to-smtp"></a>Kapcsolatot létesíthet SMTP
> [!INCLUDE [Steps to create a connection to SMTP](../../includes/connectors-create-api-smtp.md)]
> 
> 

## <a name="use-an-smtp-trigger"></a>Az SMTP-eseményindító használata
Egy eseményindító nem egy eseményt, a logikai alkalmazás definiált munkafolyamat indításához használható. [További tudnivalók az eseményindítók](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

Ebben a példában, mert SMTP nem rendelkezik saját, eseményindító fogjuk használni a **Salesforce - amikor létrejön egy objektum** eseményindító. Ehhez az eseményindítóhoz akkor aktiválódik, amikor új objektumot hoz létre a Salesforce-ban. A példa kedvéért be azt, hogy minden alkalommal új vezető jön létre a Salesforce, egy *e-mailek küldése* , egy értesítés, amely a létrehozandó új vezető az SMTP-összekötőn keresztül valósul meg.

1. Adja meg *salesforce* be a keresőmezőbe a logic apps designer válassza ki a **Salesforce - amikor létrejön egy objektum** eseményindító.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  
2. A **egy objektumának létrejöttekor** vezérlő jelenik meg.
   ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  
3. Válassza ki a **objektumtípus** válassza *vezethet* objektumok közül. Ebben a lépésben vannak jelzi, hogy hoz létre egy eseményindítót, amely értesíti a Logic Apps alkalmazást, amikor egy új vezető jön létre a Salesforce-ban.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  
4. Az eseményindító létrehozása befejeződött.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## <a name="use-an-smtp-action"></a>Az SMTP-művelethez használata
Egy művelet során a logikai alkalmazás definiált munkafolyamat által végzett. [További információ a műveletek](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

Most, hogy hozzá lett adva az eseményindítót, kövesse az alábbi lépéseket egy SMTP-művelethez egy új vezető a Salesforce-ban létrehozásakor előforduló hozzáadni.

1. Válassza ki **+ új lépés** hozzáadása a műveletet hajtson végre egy új vezető jön létre.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  
2. Válassza ki **művelet hozzáadása**. Ez a keresési mezőbe, ahol kereshet bármilyen műveletet meg szeretné igénybe megnyílik.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  
3. Adja meg *smtp* SMTP kapcsolatos műveletek kereséséhez.  
4. Válassza ki **SMTP - E-mail küldése** , a végrehajtandó műveletet, amikor az új vezető jön létre. A művelet blokk nyílik meg. A Tervező blokkban az smtp-kapcsolatot létesíteni, ha még nem meg korábban fog.  
   ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)    
5. Adjon meg a kívánt e-mailek adataihoz a **SMTP - E-mail küldése** blokkot.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  
6. Mentse a munkáját ahhoz, hogy aktiválja a munkafolyamatot.  

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/smtpconnector/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k lista](apis-list.md).