---
title: A SharePoint Server-összekötő használata a Logic Apps |} A Microsoft Docs
description: A Logic Apps a SharePoint Server-összekötő használatának első lépései
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
ms.openlocfilehash: 3ac3105231017cdbf8bfcf143b26451a68da5283
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868701"
---
# <a name="get-started-with-the-sharepoint-connector"></a>A SharePoint-összekötőben használatának első lépései
A SharePoint-összekötőben listák SharePoint szolgáltatásbeli együttműködve lehetővé teszi.

Első lépésként hozza létre a logikai alkalmazáshoz. Lásd: [hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-sharepoint"></a>Hozzon létre egy kapcsolatot a SharePoint
A SharePoint-összekötő használatához először hozzon létre egy **kapcsolat** adja meg a részleteket a tulajdonságok: 

| Tulajdonság | Szükséges | Leírás |
| --- | --- | --- |
| Jogkivonat |Igen |Adja meg a SharePoint rendszerbeli hitelesítő adatokat |

Csatlakozás **SharePoint**, adja meg az identitás (felhasználónév és jelszó, intelligens kártya hitelesítő adatait, és így tovább). Hogy a hitelesítést követően folytathatja a SharePoint-összekötő használatához a logikai alkalmazásban. 

A tervezőben a logikai alkalmazás az alábbi lépéseket követve jelentkezzen be, és hozzon létre a **kapcsolat** a logikai alkalmazásban használható:

1. A keresőmezőbe írja be a SharePoint, és várja meg a keresés való visszatéréshez és a SharePoint összes bejegyzés a név:   
   ![A SharePoint konfigurálása][1]  
2. Válassza ki **SharePoint – fájl létrehozásakor**   
3. Válassza ki **jelentkezzen be a SharePoint**:   
   ![A SharePoint konfigurálása][2]    
4. Jelentkezzen be a hitelesítést és a SharePoint, a SharePoint hitelesítő adatok megadása   
   ![A SharePoint konfigurálása][3]     
5. Hitelesítés befejezése után a program átirányítja a logikai alkalmazás SharePoint konfigurálásával végrehajtásához **amikor létrejön egy fájl** párbeszédpanel.          
   ![A SharePoint konfigurálása][4]  
6. Ezután hozzáadhatja a más eseményindítókat és műveleteket, amelyeket a logikai alkalmazás végrehajtásához szükséges.   
7. Mentse a munkáját kiválasztásával **mentése** (tetejénél) menüben.

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Megtekintheti a valamennyi eseményindítót és műveletet a swaggerben meghatározott, és emellett a korlátozott a [összekötő részletei](/connectors/sharepoint/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k listája](apis-list.md).

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png
