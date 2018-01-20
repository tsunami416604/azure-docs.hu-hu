---
title: "A SharePoint Server-összekötő használata a Logic Apps |} Microsoft Docs"
description: "Az első lépéseiben a Logic Apps alkalmazásait a SharePoint Server-összekötő"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: da863e0249cb46e4e569812a851f3199d57b2107
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-sharepoint-connector"></a>A SharePoint-összekötő az első lépései
A SharePoint-összekötő segítségével egy SharePoint-webhelyre listák használata.

Hozzon létre egy logic app; első lépései Lásd: [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-sharepoint"></a>Kapcsolatot létesíthet SharePoint
A SharePoint-összekötő használatához először létre kell hoznia egy **kapcsolat** adja meg a részleteket a tulajdonságok: 

| Tulajdonság | Szükséges | Leírás |
| --- | --- | --- |
| Jogkivonat |Igen |Adja meg a SharePoint rendszerbeli hitelesítő adatokat |

Csatlakozni **SharePoint**, adja meg az identitás (felhasználónév és jelszó, intelligens kártyához tartozó hitelesítő adatok, stb.) a SharePoint. Amennyiben Ön már hitelesítve, folytassa a SharePoint-összekötő használatához a Logic Apps alkalmazást a. 

A Logic Apps alkalmazást designer, a következő lépések segítségével jelentkezzen be a SharePoint, a VPN-kapcsolat létrehozásához **kapcsolat** használható a Logic Apps alkalmazást:

1. A keresőmezőbe írja be a SharePoint, és várja meg, a Keresés a SharePoint összes bejegyzés a nevét adja vissza:   
   ![A SharePoint konfigurálása][1]  
2. Válassza ki **SharePoint - fájl létrehozásakor**   
3. Válassza ki **jelentkezzen be a SharePoint**:   
   ![A SharePoint konfigurálása][2]    
4. Jelentkezzen be a SharePoint való hitelesítéshez szükséges a SharePoint hitelesítő adatok megadása   
   ![A SharePoint konfigurálása][3]     
5. A hitelesítés befejezése után meg fogja átirányítani a Logic Apps alkalmazást úgy konfigurálja a SharePoint befejezéséhez **fájl létrehozásának** párbeszédpanel.          
   ![A SharePoint konfigurálása][4]  
6. Más eseményindítók és műveletek végre kell hajtania a Logic Apps alkalmazást, majd adja hozzá.   
7. Mentse a munkáját kiválasztásával **mentése** fenti menüsávjában.  

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/sharepoint/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k lista](apis-list.md).

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png
