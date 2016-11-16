---
title: "Logic Apps-alkalmazás létrehozása | Microsoft Docs"
description: "Útmutató SaaS szolgáltatásokhoz csatlakozó Logic Apps-alkalmazás létrehozásához"
author: jeffhollan
manager: dwrede
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ab26637d0f49abd73b55e3ccf9ace0f33f6f8731


---
# <a name="create-a-new-logic-app-connecting-saas-services"></a>Új, SaaS szolgáltatásokhoz csatlakozó logikai alkalmazás létrehozása
Ez a témakör bemutatja, hogyan kezdhet neki mindössze néhány perc alatt az [Azure Logic Apps](app-service-logic-what-are-logic-apps.md) használatának. Bemutatunk egy egyszerű munkafolyamatot, amely érdekes tweetek elküldését teszi lehetővé az e-mail-címére.

Az itt részletezett forgatókönyvhöz a következőkre lesz szüksége:

* Azure-előfizetés
* Twitter-fiók
* Outlook.com vagy üzemeltetett Office 365-postaláda

## <a name="create-a-new-logic-app-to-email-you-tweets"></a>Olyan új logikai alkalmazás létrehozása, amely Twitter-üzeneteket küld Önnek e-mailben
1. Az [Azure Portal irányítópultján](https://portal.azure.com) válassza az **Új** lehetőséget. 
2. A keresősávban keressen rá a „logikai alkalmazás” kifejezésre, majd válassza a **Logic App** lehetőséget. Választhatja az **Új**, **Web + Mobile**, **Logic App** lehetőséget is. 
3. Adja meg a logikai alkalmazás nevét, válasszon egy helyet, egy erőforráscsoportot, majd kattintson a **Létrehozás** gombra.  Ha a **Rögzítés az irányítópulton** lehetőséget választja, a logikai alkalmazás automatikusan megnyílik a telepítés után.  
4. A logikai alkalmazás első megnyitását követően a kezdéshez egy sablonból választhat.  Jelen esetben az alapoktól való felépítéshez kattintson az **Üres Logic App** lehetőségre. 
5. Az első elem, amelyet létre kell hoznia, az eseményindító.  Ez az esemény fogja elindítani a logikai alkalmazást.  Az eseményindító-kereső mezőben keressen a **twitter** kifejezésre, majd válassza ki a megfelelő elemet.
6. Most írjon be egy keresési kifejezést az indításhoz.  A **Gyakoriság** és az **Időköz** fogja meghatározni, hogy a logikai alkalmazás milyen gyakran keressen új tweeteket (és küldjön tovább minden tweetet az adott időtartományon belül).
    ![Keresés a Twitteren](./media/app-service-logic-create-a-logic-app/twittersearch.png)
7. Válassza az **Új lépés**, majd a **Művelet hozzáadása** vagy a **Feltétel hozzáadása** elemet.
8. Ha a **Művelet hozzáadása** lehetőséget választja, kereshet az [elérhető összekötők](../connectors/apis-list.md) között a művelet kiválasztásához. Például kiválaszthatja az **Outlook.com – E-mail küldése** lehetőséget egy e-mail egy outlook.com-címről történő küldéséhez:  
    ![Műveletek](./media/app-service-logic-create-a-logic-app/actions.png)
9. Ezt követően ki kell töltenie a kívánt e-mailre vonatkozó paramétereket:  ![Paraméterek](./media/app-service-logic-create-a-logic-app/parameters.png)
10. Végül a **Mentés** lehetőséget választva élesítheti a logikai alkalmazást.

## <a name="manage-your-logic-app-after-creation"></a>Létrehozott logikai alkalmazás kezelése
A logikai alkalmazás a fentiek elvégzésével működésbe lépett. Időközönként tweeteket fog keresni a beírt keresési kifejezés alapján. Ha egyező tweetet talál, egy e-mailt küld. Végül tekintsük át, hogyan lehet letiltani az alkalmazást, illetve ellenőrizni működését.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
2. Kattintson a képernyő bal oldalán található **Böngészés** elemre, majd válassza a **Logic Apps** lehetőséget.
3. Az aktuális állapot és az általános információk megtekintéséhez kattintson az újonnan létrehozott logikai alkalmazásra.
4. Az új logikai alkalmazás szerkesztéséhez kattintson a **Szerkesztés** elemre.
5. Az alkalmazás kikapcsolásához kattintson a parancssáv **Letiltás** elemére.
6. A futtatási és aktiválási előzmények megtekintésével figyelheti, hogy mikor fut a logikai alkalmazás.  Kattintson a **Frissítés** gombra a legújabb adatok megjelenítéséhez.

Az elmúlt kevesebb mint 5 percben létrehozott és beállított egy egyszerű, a felhőben futó logikai alkalmazást. A Logic Apps funkcióinak használatával kapcsolatban további információkat talál a [Use logic app features (Logikai alkalmazások funkcióinak használata)] című cikkben. Ha magukról a logikai alkalmazás definícióiról szeretne többet megtudni, olvassa el a [logikaialkalmazás-definíciók készítését](app-service-logic-author-definitions.md) bemutató cikket.

<!-- Shared links -->
[Azure Portal]: https://portal.azure.com
[Logikaialkalmazás-funkciók használata]: app-service-logic-create-a-logic-app.md



<!--HONumber=Nov16_HO2-->


