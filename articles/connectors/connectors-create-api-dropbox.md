---
title: Csatlakozhat a Dropboxhoz – Azure Logic Apps |} A Microsoft Docs
description: Töltheti fel és kezelheti a fájlok a Dropboxban REST API-k és Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 07/15/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 256a0b34d5050e17abe5bb98ca0c13ab0b61787e
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43094438"
---
# <a name="get-started-with-the-dropbox-connector"></a>A Dropbox-összekötő használatának első lépései
Csatlakozhat a Dropboxhoz, a fájlok kezeléséhez. Különféle műveleteket, például feltölthet, frissítése, beolvasása, és törölje a fájlokat a Dropbox.

Használandó [összekötőket](apis-list.md), először hozzon létre egy logikai alkalmazást. Úgy kezdheti [egy logikai alkalmazás most már létrehozásának](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-dropbox"></a>Csatlakozhat a Dropboxhoz
A logikai alkalmazás bármely szolgáltatási férhet hozzá, akkor először kell hoznia egy *kapcsolat* a szolgáltatáshoz. Kapcsolatot biztosít a Logic Apps-alkalmazás és a egy másik szolgáltatás közötti kapcsolat. Ha például csatlakozhat a Dropboxhoz, először kell egy Dropbox *kapcsolat*. Egy kapcsolat létrehozásához kell általában, amelyhez csatlakozni szeretne a szolgáltatás eléréséhez használt hitelesítő adatok megadása. Tehát a Dropbox példában kell a hitelesítő adatokat a Dropbox-fiókjában annak érdekében, hogy hozza létre a kapcsolatot a Dropboxba. 

### <a name="create-a-connection-to-dropbox"></a>Hozzon létre egy kapcsolatot a Dropboxba
> [!INCLUDE [Steps to create a connection to Dropbox](../../includes/connectors-create-api-dropbox.md)]
> 
> 

## <a name="use-a-dropbox-trigger"></a>Dropbox-triggert
Egy trigger egy eseményt, amely a logikai alkalmazásban definiált munkafolyamat elindításához használható. [További tudnivalók a triggerek](../logic-apps/logic-apps-overview.md#logic-app-concepts).

Ebben a példában használjuk a **amikor létrejön egy fájl** eseményindító. Ez az eseményindító esetén a rendszer nevezzük a **fájl tartalmának beolvasása elérési út segítségével** Dropbox-műveletet. 

1. Adja meg *dropbox* a keresőmezőbe, a Logic Apps-tervezőben, majd válassza ki a **Dropbox – fájl létrehozásakor** eseményindító.      
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)  
2. Válassza ki a mappát, ahol nyomon követheti a fájlok létrehozási szeretné. Válasszon... (azonosított a vörös), és tallózással válassza ki a trigger bemeneti kívánt mappát.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)  

## <a name="use-a-dropbox-action"></a>Egy Dropbox-művelet használata
Művelet definiálva, a logikai alkalmazás a munkafolyamat által végzett művelet. [További információért azokról a műveletekről](../logic-apps/logic-apps-overview.md#logic-app-concepts).

Most, hogy az eseményindító hozzá lett adva, a következő lépésekkel adja hozzá egy műveletet, amely a rendszer az új fájl tartalmának beolvasása.

1. Válassza ki **+ új lépés** hozzáadása a végrehajtani kívánt, ha új fájlt hoznak létre.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)
2. Válassza ki **művelet hozzáadása**. Ez megnyitja a keresőmezőbe, amelyen kereshetők bármely művelet, szeretné venni.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)
3. Adja meg *dropbox* Dropbox kapcsolódó tevékenységek keresésére.  
4. Válassza ki **Dropbox – fájl tartalmának az elérési út lekérése** , a végrehajtandó műveletet, amikor új fájlt hoznak létre a kijelölt Dropbox-mappában. A művelet blokk nyílik meg. A logikai alkalmazás eléréséhez a Dropbox-fiókjában, ha még nem meg korábban engedélyezésére kéri.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  
5. Válasszon... (a jobb oldalán található a **fájl elérési útja** vezérlő), és keresse meg a használni kívánt fájl elérési útja. Használhatja a **fájl elérési útja** token felgyorsítható a logikai alkalmazások létrehozása.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  
6. Mentse a munkáját, és hozzon létre egy új fájlt a Dropbox, a munkafolyamat aktiválásához.  

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Megtekintheti a valamennyi eseményindítót és műveletet a swaggerben meghatározott, és emellett a korlátozott a [összekötő részletei](/connectors/dropbox/).

## <a name="more-connectors"></a>További összekötők
Lépjen vissza a [API-k listája](apis-list.md).
