---
title: A SharePoint Server-összekötő használata a Logic Apps |} Microsoft Docs
description: Az első lépéseiben a Logic Apps alkalmazásait a SharePoint Server-összekötő
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: c153b4987e37a5d97b95d4f1249de1ed92e851b3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295944"
---
# <a name="get-started-with-the-sharepoint-connector"></a>A SharePoint-összekötő az első lépései
A SharePoint-összekötő segítségével egy SharePoint-webhelyre listák használata.

Hozzon létre egy logic app; első lépései Lásd: [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-sharepoint"></a>Kapcsolatot létesíthet SharePoint
A SharePoint-összekötő használatához először létre kell hoznia egy **kapcsolat** adja meg a részleteket a tulajdonságok: 

| Tulajdonság | Szükséges | Leírás |
| --- | --- | --- |
| Jogkivonat |Igen |Adja meg a SharePoint rendszerbeli hitelesítő adatokat |

Csatlakozni **SharePoint**, adja meg az identitás (felhasználónév és jelszó, intelligens kártyához tartozó hitelesítő adatok, és így tovább). Ha már hitelesítése megtörtént, a SharePoint-összekötő használatához a Logic Apps alkalmazást a lépne. 

Az a logikai alkalmazás designer, a következő lépésekkel jelentkezzen be, és hozzon létre a **kapcsolat** használható a Logic Apps alkalmazást:

1. A keresőmezőbe írja be a SharePoint, és várja meg, a Keresés a SharePoint összes bejegyzés a nevét adja vissza:   
   ![A SharePoint konfigurálása][1]  
2. Válassza ki **SharePoint - fájl létrehozásakor**   
3. Válassza ki **jelentkezzen be a SharePoint**:   
   ![A SharePoint konfigurálása][2]    
4. Jelentkezzen be a SharePoint való hitelesítéshez szükséges a SharePoint hitelesítő adatok megadása   
   ![A SharePoint konfigurálása][3]     
5. Hitelesítés befejezése után a program átirányítja a logikai alkalmazáshoz, konfigurálásával SharePoint befejezéséhez **fájl létrehozásának** párbeszédpanel.          
   ![A SharePoint konfigurálása][4]  
6. Más eseményindítók és műveletek végre kell hajtania a Logic Apps alkalmazást, majd adja hozzá.   
7. Mentse a munkáját kiválasztásával **mentése** (tetejénél) menüben.

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/sharepoint/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k lista](apis-list.md).

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png
