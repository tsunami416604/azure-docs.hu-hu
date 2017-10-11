---
title: "Az Azure Logic Apps Dropbox összekötő |} Microsoft Docs"
description: "Az Azure App service Logic Apps alkalmazások létrehozása A fájlok kezelését Dropbox csatlakozni. Hajtsa végre különböző műveleteket végez, például a feltöltés, frissítése, lekérése és Dropbox fájl törlése."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: cb0ae033-aba7-4ac9-beaa-be561a0f0cac
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 0d09580c60fd620811b539147439d0922839fe7e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-dropbox-connector"></a>A Dropbox-összekötő az első lépései
A fájlok kezelését Dropbox csatlakozni. Hajtsa végre különböző műveleteket végez, például a feltöltés, frissítése, lekérése és Dropbox fájl törlése.

Használandó [a csatlakozókat](apis-list.md), először hozzon létre egy logikai alkalmazást. Elkezdheti által [logikai alkalmazás létrehozása most](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-dropbox"></a>Csatlakozás a dropbox alkalmazásba
A Logic Apps alkalmazást bármely szolgáltatás hozzáférni, először hozzon létre egy *kapcsolat* a szolgáltatáshoz. Egy kapcsolat egy logikai alkalmazást és egy másik szolgáltatás közötti kapcsolatot biztosít. Például használata esetén csatlakozhassanak a dropbox alkalmazásba, először a Dropbox *kapcsolat*. VPN-kapcsolat létrehozásához kellene általában csatlakozni szeretne a szolgáltatás eléréséhez használt hitelesítő adatait. Igen a Dropbox példában kellene a hitelesítő adatokat a Dropbox-fiók a Dropbox kapcsolat létrehozásához. [További információ a kapcsolatok száma]()

### <a name="create-a-connection-to-dropbox"></a>A dropbox alkalmazásba kapcsolat létrehozása
> [!INCLUDE [Steps to create a connection to Dropbox](../../includes/connectors-create-api-dropbox.md)]
> 
> 

## <a name="use-a-dropbox-trigger"></a>A Dropbox eseményindító használata
Egy eseményindító nem egy eseményt, a logikai alkalmazás definiált munkafolyamat indításához használható. [További tudnivalók az eseményindítók](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

A jelen példában használjuk a **fájl létrehozásának** eseményindító. Ehhez az eseményindítóhoz akkor fordul elő, amikor ezt nevezik a **elérési út használatával fájl tartalmának lekérdezése** Dropbox művelet. 

1. Adja meg *dropbox* be a keresőmezőbe a Logic Apps-tervezőben, majd válassza ki a **Dropbox - fájl létrehozásakor** eseményindító.      
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)  
2. Válassza ki a mappát, ahol alkalmazni kívánja nyomon követését a fájl létrehozása. Válasszon... (a vörös téglalappal azonosított), és keresse meg azt a mappát, válassza ki van adva az eseményindítót kíván.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)  

## <a name="use-a-dropbox-action"></a>A Dropbox művelettel
Egy művelet során a logikai alkalmazás definiált munkafolyamat által végzett. [További információ a műveletek](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

Most, hogy hozzá lett adva az eseményindítót, kövesse az alábbi lépéseket, amelyek megkapják az új fájl tartalma művelet hozzáadása.

1. Válassza ki **+ új lépés** hozzáadása a műveletet hajtson végre egy új fájl jön létre.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)
2. Válassza ki **művelet hozzáadása**. Ez a keresési mezőbe, ahol kereshet bármilyen műveletet meg szeretné igénybe megnyílik.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)
3. Adja meg *dropbox* Dropbox kapcsolatos műveletek kereséséhez.  
4. Válassza ki **Dropbox - fájl tartalmának lekérése az elérési út használatával** esetén végrehajtandó műveletet, egy új fájl jön létre a kiválasztott Dropbox mappában. A művelet blokk nyílik meg. Kérni fogja a Logic Apps alkalmazást a Dropbox-fiók eléréséhez, ha még nem meg korábban engedélyezésére.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  
5. Válasszon... (jobb oldalán található a **fájl elérési útját** vezérlő), és keresse meg a használni kívánt fájl elérési útját. Másik lehetőségként a **fájl elérési útját** felgyorsítható a logic app létrehozásának token.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  
6. Mentse a munkáját, és hozzon létre egy új fájlt a dropbox-ba, a munkafolyamat aktiválásához.  

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/dropbox/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k lista](apis-list.md).