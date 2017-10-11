---
title: "Az Azure Logic Apps egy helyszíni SAP rendszerhez való csatlakozás |} Microsoft Docs"
description: "A logic app munkafolyamat a helyszíni adatok átjárón keresztül egy helyszíni SAP rendszerhez való csatlakozás"
services: logic-apps
author: padmavc
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 3fea93f558d5a4ef62550fd1f6486903cb812930
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="connect-to-an-on-premises-sap-system-from-logic-apps-with-the-sap-connector"></a>A logic Apps alkalmazásokból SAP összekötő egy helyszíni SAP rendszerhez való csatlakozás 

Az a helyszíni átjáró teszi lehetővé adatok kezelésére, és a biztonságos hozzáférés a helyszíni erőforrásokhoz. Ez a témakör bemutatja, hogyan csatlakoztathatja a logic apps egy helyszíni SAP rendszerhez. Ebben a példában a Logic Apps alkalmazást az IDOC-kérelmek HTTP Protokollon keresztül, és visszaküldi a választ vissza.    

> [!NOTE]
> Aktuális korlátozások vonatkoznak: 
> - A Logic Apps alkalmazást időtúllépés történik, ha a válasz lépéseit nem fejeződik be a [kérelem időkorlátja](./logic-apps-limits-and-config.md). Ebben a forgatókönyvben kérelmek előfordulhat, hogy blokkolnánk a hozzáférését. 
> - A fájlválasztó nem jelenik meg a rendelkezésre álló mezők. Ebben a forgatókönyvben kézzel is hozzáadhatja a elérési utakat.

## <a name="prerequisites"></a>Előfeltételek

- Telepítse és konfigurálja a legújabb [helyszíni adatátjáró](https://www.microsoft.com/download/details.aspx?id=53127) 1.15.6150.1 verzió vagy újabb. [A helyszíni átjáró a logikai alkalmazás összekapcsolása](http://aka.ms/logicapps-gateway) lépéseit sorolja fel. A folytatás előtt a helyi gépen telepítenie kell az átjárót.

- Töltse le és telepítse a legújabb SAP ügyféloldali kódtár ugyanazon a számítógépen, amelyre telepítette az átjáró. Használja az alábbi SAP-verziók valamelyikét: 
    - SAP-kiszolgálóhoz
        - Egyetlen SAP-kiszolgálóhoz sem, amely támogatja a .NET-összekötő (Ice) 3.0
 
    - SAP-ügyfél
        - SAP .NET Connector (Ice) 3.0

## <a name="add-triggers-and-actions-for-connecting-to-your-sap-system"></a>Eseményindítók és műveletek a SAP levelezőrendszerhez való csatlakozás hozzáadása

Az SAP-összekötő műveleteket, de nem eseményindítók rendelkezik. Igen azt kell használni egy másik eseményindító a munkafolyamat elején. 

1. Vegye fel a kérelem/válasz eseményindító, majd válassza ki **új lépés**.

2. Válassza ki **művelet hozzáadása**, majd válassza ki az SAP-összekötő beírásával `SAP` a keresési mezőbe:    

     ![SAP-alkalmazáskiszolgáló vagy SAP üzenet kiszolgáló kiválasztása](media/logic-apps-using-sap-connector/sap-action.png)

3. Válassza ki [ **SAP alkalmazáskiszolgáló** ](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) vagy [ **SAP üzenet Server**](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)a SAP-beállításai alapján. Ha nincs meglévő kapcsolat, a rendszer kéri a hozzon létre egyet.

   1. Válassza ki **keresztül, a helyszíni adatátjáró**, és adja meg az SAP rendszert:   

       ![Kapcsolati karakterlánc hozzáadása az SAP](media/logic-apps-using-sap-connector/picture2.png)  

   2. A **átjáró**, egy meglévő átjárót, vagy telepít egy új átjárót, válasszon **átjáró telepítése**.

        ![Telepítsen egy új átjárót](media/logic-apps-using-sap-connector/install-gateway.png)
  
   3. A részletek megadása után válassza ki a **létrehozása**. 
   A Logic Apps konfigurálja, és ellenőrzi a kapcsolatot, és annak biztosítása, hogy a kapcsolat megfelelően működik-e.

4. Adjon meg egy nevet az SAP-kapcsolat.

5. Az SAP beállításokról is elérhető. Keresse meg az IDOC szóló kategóriáját, válassza ki a listából. Vagy kézzel írja be az elérési út, és válassza ki a HTTP-válasz a **törzs** mező:

     ![SAP művelet](media/logic-apps-using-sap-connector/picture3.png)

6. Adja hozzá a művelet létrehozásához egy **HTTP-válasz**. A válaszüzenet kell lennie az SAP-kimenetből.

7. Mentse a Logic Apps alkalmazást. Az idoc-hoz, a HTTP-eseményindító URL keresztül küldött tesztelik azt. Az IDOC elküldött, várja meg a logikai alkalmazás válasza:   

     > [!TIP]
     > Ellenőrizze, hogy miként [logikai alkalmazások figyelése](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Most, hogy az SAP-összekötőt a Logic Apps alkalmazást ad hozzá, Fedezze fel egyéb funkciók:

- BAPI
- RFC

## <a name="get-help"></a>Segítségkérés

Látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps), ahol kérdéseket tehet fel és válaszolhat meg, valamint megtudhatja, mivel foglalkoznak az Azure Logic Apps más felhasználói.

Ha szeretné segíteni az Azure Logic Apps és összekötők fejlesztését, szavazzon az ötletekre az [Azure Logic Apps felhasználói visszajelzések oldalán](http://aka.ms/logicapps-wish), vagy küldje be saját javaslatait.

## <a name="next-steps"></a>Következő lépések

- Ellenőrizze, átalakítására, valamint egyéb hasonló BizTalk funkciót az a [vállalati integrációs csomag](../logic-apps/logic-apps-enterprise-integration-overview.md). 
- [A helyszíni adatokhoz](../logic-apps/logic-apps-gateway-connection.md) a logic Apps alkalmazásokból
