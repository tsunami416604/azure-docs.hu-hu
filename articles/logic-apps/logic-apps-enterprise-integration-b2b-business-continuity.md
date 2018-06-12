---
title: Vész-helyreállítási B2B integrációs fiók - Azure Logic Apps |} Microsoft Docs
description: Logic Apps B2B katasztrófa utáni helyreállítás
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 65c7262916219a74dcd6bdab487306b5bd5f709f
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299096"
---
# <a name="logic-apps-b2b-cross-region-disaster-recovery"></a>Logic Apps B2B kereszt-régió katasztrófa utáni helyreállítás

B2B munkaterhelések vonatkozhat például rendeléseket és a számlák pénz tranzakciók. A vész-események esetén fontos a vállalati a gyors helyreállítás felel meg a vállalati szintű SLA megegyezésre a partnerekkel együttműködve. Ez a cikk bemutatja, hogyan hozhat létre egy üzletmenet folytonosságát biztosító terve B2B munkaterhelésekhez. 

* Vész-helyreállítási készültségi 
* Feladatok átadása a másodlagos régióba katasztrófa esemény során 
* Térhet vissza az elsődleges régióban katasztrófa esemény után

## <a name="disaster-recovery-readiness"></a>Vész-helyreállítási készültségi  

1. Egy másodlagos régióban azonosításához, és hozzon létre egy [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) másodlagos régióban.

2. Partnerek, sémákat és a szükséges üzenet adatfolyamok, amikor a futtatási állapot kell replikálni a másodlagos régióba integrációs fiók megállapodások hozzáadása.

   > [!TIP]
   > Győződjön meg arról, nincs konzisztencia integrációs fiók összetevő elnevezési régiók között. 

3. A futtatási állapot le az elsődleges régióban, a másodlagos régióban logikai alkalmazás létrehozása. 

   A logikai alkalmazás rendelkeznie kell egy *eseményindító* és egy *művelet*. 
   Az eseményindító elsődleges régió integrációs fiókkal csatlakozik, és másodlagos régióba integrációs fiók csatlakozzon a műveletet. 
   Az időtartam alapján, az eseményindító kérdezze le az elsődleges régióban állapot tábla futtatni, és lekéri az új bejegyzések, ha van ilyen. A művelet frissíti őket másodlagos régióba integrációs fiókhoz. 
   Ez segít növekményes futási állapotának beolvasása az elsődleges régióban másodlagos régióba.

4. Az üzletmenet folytonossága Logic Apps-integráció fiók célja, hogy támogatja a B2B protokollokat - X12, és a AS2, EDIFACT. Részletes lépéseket megkereséséhez válassza ki a megfelelő hivatkozásokra.

5. Az ajánljuk, hogy egy másodlagos régióban található összes elsődleges régió erőforrások túl telepítése. 

   Az Azure SQL Database vagy Azure Cosmos DB, Azure Service Bus és az Azure Event Hubs üzenetküldési, az Azure API Management és az Azure Logic Apps szolgáltatás az Azure App Service-ben használt elsődleges régió erőforrásai.   

6. Létesítsen kapcsolatot az elsődleges régióban másodlagos régióba. A futtatási állapot lekéréses elsődleges régióban, egy másodlagos régióban logikai alkalmazás létrehozása. 

   A logikai alkalmazás rendelkeznie kell egy eseményindító és egy műveletet. 
   Az eseményindító csatlakozzon egy elsődleges régió integrációs fiókot. 
   A művelet egy másodlagos régióban integrációs fiók csatlakozzon. 
   Az időtartam alapján, az eseményindító kérdezze le az elsődleges régióban állapot tábla futtatni, és lekéri az új bejegyzések, ha van ilyen. 
   A művelet frissíti őket egy másodlagos régióban integrációs fiókhoz. 
   Ez az eljárás segít az elsődleges régióban növekményes futási állapotának beolvasása a másodlagos régióba.

A Logic Apps-integráció fiókban az üzletmenet folytonossága támogatja a B2B protokollok X12 AS2 és EDIFACT alapján. A X12 és az AS2 részletes lépéseiért lásd: [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) és [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) ebben a cikkben.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Feladatok átadása a másodlagos régióba katasztrófa esemény során

A vész események, ha az elsődleges régióban az üzletmenet folytonossága érdekében, a másodlagos régióba közvetlen forgalom nem érhető el. Egy másodlagos régióba segítséget nyújt a helyreállításához a helyreállítási Időkorlát/Helyreállításiidő teljesítéséhez gyorsan funkciók üzleti megegyezett a partnerek. Is minimalizálja azon törekvéseit, hogy a feladatátvételt a egy régió tartozik egy másik régióban. 

Nincs olyan várható késleltetés vezérlő számok másolása egy elsődleges régióban másodlagos régióba. A ajánlás az ismétlődő generált ellenőrző számainak küldése partnerek katasztrófa esemény során elkerülése, hogy a vezérlő számok növelheti a másodlagos régióba megállapodásokban használatával [PowerShell-parancsmagok](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Visszatérés egy elsődleges régió katasztrófa utáni esemény

Visszatérés egy elsődleges régióban elérhető esetén, kövesse az alábbi lépéseket:

1. A másodlagos régióban partnerektől származó üzenetek fogadását.  

2. Az összes elsődleges régió szerződéshez generált ellenőrző számainak növelhető a [PowerShell-parancsmagok](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).  

3. A másodlagos régióba közvetlen forgalmát az elsődleges régióban.

4. Ellenőrizze, hogy engedélyezve van-e a logikai alkalmazást a másodlagos régióban húzza a futtatási állapot elsődleges régióban létrehozott.

## <a name="x12"></a>X12 

X 12 dokumentumok EDI üzletmenet folytonossága vezérlő számok alapján:

> [!TIP]
> Használhatja a [X12 quick start sablon](https://azure.microsoft.com/documentation/templates/201-logic-app-x12-disaster-recovery-replication/) logic Apps alkalmazások létrehozásához. Létrehozás elsődleges és másodlagos integrációs fiókok előfeltételei használhatja a sablont. A sablon segítségével két a logic apps, kapott vezérlő számok egyet, majd egy másikat a létrehozott vezérlő számok létrehozásához. A logikai alkalmazás, az eseményindító csatlakozik a elsődleges integrációs fiók és a művelet a másodlagos integrációs fiókhoz megfelelő eseményindítók és műveletek jönnek létre.

**Előfeltételek**

Ahhoz, hogy a bejövő üzenetek vész-helyreállítási, válassza ki a duplikált beállításokat a X12 megállapodás fogadási beállítások.

![Válassza ki a duplikált beállításokat](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Hozzon létre egy [logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md) egy másodlagos régióban.    

2. A Keresés **X12**, és válassza ki **X12-ellenőrzési számot módosításakor**.   

   ![X12 keresése](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   Az eseményindító kéri, hogy kapcsolatot létesíteni a integrációs fiókkal. 
   Az eseményindító csatlakoznia kell egy elsődleges régió integrációs fiókot.

3. Adja meg a kapcsolat neve, válasszon a *elsődleges régió integrációs fiók* a listából, és válassza a **létrehozása**.   

   ![Elsődleges régió integrációs fiók neve](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. A **vezérlő számú szinkronizálás elindításához DateTime** beállítás nem kötelező. A **gyakoriság** megadható **nap**, **óra**, **perc**, vagy **második** időközzel.   

   ![Dátum és idő és gyakorisága](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Válassza ki **új lépés** > **művelet hozzáadása**.

   ![Új lépés, Action hozzáadása](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. A Keresés **X12**, és válassza ki **X12-hozzáadásakor vagy módosításakor a vezérlő számok**.   

   ![Ellenőrzőszámok hozzáadása vagy frissítése](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Művelet egy másodlagos régióban integrációs fiókkal való kapcsolódáshoz válasszon **kapcsolat módosítása** > **új kapcsolat hozzáadása** a rendelkezésre álló integrációs fiókok listáját. Adja meg a kapcsolat neve, válasszon a *másodlagos régióba integrációs fiók* a listából, és válassza a **létrehozása**. 

   ![Másodlagos régióba integrációs fiók neve](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Váltás a jobb felső sarkában található ikonra kattintva nyers bemeneti adatok.

   ![Nyers bemenetek váltani](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Válassza ki a dinamikus tartalom objektumválasztó törzs, és mentse a logikai alkalmazást.

   ![Dinamikus tartalom mezők](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Az időtartam alapján, az eseményindító kérdezze le az elsődleges régióban kapott vezérlő tábla száma, és lekéri az új bejegyzések. 
   A művelet frissíti a másodlagos régióba integrációs fiók rögzíti. 
   Ha a nincsenek frissítések, az eseményindító állapota megjelenik **kimaradnak**.   

   ![Vezérlő tábla száma](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Az időtartam alapján, a növekményes futási állapotának replikálja az elsődleges régióban másodlagos régióba. A katasztrófa események, ha az elsődleges régióban nem áll rendelkezésre, közvetlen forgalom az üzletmenet folytonossága érdekében a másodlagos régióba. 

## <a name="edifact"></a>EDIFACT 

Az üzletmenet folytonossága EDI EDIFACT-dokumentumok vezérlő számok alapul.

**Előfeltételek**

Ahhoz, hogy a bejövő üzenetek vész-helyreállítási, jelölje ki a duplikált beállításokat a EDIFACT megállapodás fogadási beállítások.

![Válassza ki a duplikált beállításokat](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Hozzon létre egy [logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md) egy másodlagos régióban.    

2. A Keresés **EDIFACT**, és válassza ki **EDIFACT - ellenőrzési számot módosításakor**.

   ![EDIFACT keresése](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   Az eseményindító kéri, hogy kapcsolatot létesíteni a integrációs fiókkal. 
   Az eseményindító csatlakoznia kell egy elsődleges régió integrációs fiókot. 

3. Adja meg a kapcsolat neve, válasszon a *elsődleges régió integrációs fiók* a listából, és válassza a **létrehozása**.    

   ![Elsődleges régió integrációs fiók neve](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. A **vezérlő számú szinkronizálás elindításához DateTime** beállítás nem kötelező. A **gyakoriság** megadható **nap**, **óra**, **perc**, vagy **második** időközzel.    

   ![Dátum és idő és gyakorisága](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Válassza ki **új lépés** > **művelet hozzáadása**.    

   ![Új lépés, Action hozzáadása](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. A Keresés **EDIFACT**, és válassza ki **EDIFACT - hozzáadásakor vagy módosításakor a vezérlő számok**.   

   ![Ellenőrzőszámok hozzáadása vagy frissítése](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Művelet egy másodlagos régióban integrációs fiókkal való kapcsolódáshoz válasszon **kapcsolat módosítása** > **új kapcsolat hozzáadása** a rendelkezésre álló integrációs fiókok listáját. Adja meg a kapcsolat neve, válasszon a *másodlagos régióba integrációs fiók* a listából, és válassza a **létrehozása**.

   ![Másodlagos régióba integrációs fiók neve](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Váltás a jobb felső sarkában található ikonra kattintva nyers bemeneti adatok.

   ![Nyers bemenetek váltani](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Válassza ki a dinamikus tartalom objektumválasztó törzs, és mentse a logikai alkalmazást.   

   ![Dinamikus tartalom mezők](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Az időtartam alapján, az eseményindító kérdezze le az elsődleges régióban kapott vezérlő tábla száma, és lekéri az új bejegyzések.
   A művelet frissíti a rekordokat a másodlagos régióba integrációs fiókhoz. 
   Ha a nincsenek frissítések, az eseményindító állapota megjelenik **kimaradnak**.

   ![Vezérlő tábla száma](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Az időtartam alapján, a növekményes futási állapotának replikálja az elsődleges régióban másodlagos régióba. A katasztrófa események, ha az elsődleges régióban nem áll rendelkezésre, közvetlen forgalom az üzletmenet folytonossága érdekében a másodlagos régióba. 

## <a name="as2"></a>AS2 

Az AS2-protokollt használó dokumentumok üzletmenet folytonossága az Üzenetazonosító és a MIC érték alapul.

> [!TIP]
> Használhatja a [AS2 gyors üzembe helyezési sablon](https://github.com/Azure/azure-quickstart-templates/pull/3302) logic Apps alkalmazások létrehozásához. Létrehozás elsődleges és másodlagos integrációs fiókok előfeltételei használhatja a sablont. A sablon segítségével, amelyen egy eseményindító és művelet logikai alkalmazás létrehozása. A logikai alkalmazás kapcsolatot hoz létre egy egy elsődleges integrációs fiókot és egy másodlagos integrációs fiók műveletet.

1. Hozzon létre egy [logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md) másodlagos régióban.  

2. A Keresés **AS2**, és válassza ki **AS2 - Ha a MIC érték jön létre**.   

   ![AS2 keresése](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   Egy eseményindító kéri, hogy kapcsolatot létesíteni a integrációs fiókkal. 
   Az eseményindító csatlakoznia kell egy elsődleges régió integrációs fiókot. 
   
3. Adja meg a kapcsolat neve, válasszon a *elsődleges régió integrációs fiók* a listából, és válassza a **létrehozása**.

   ![Elsődleges régió integrációs fiók neve](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. A **MIC érték szinkronizálás elindításához DateTime** beállítás nem kötelező. A **gyakoriság** megadható **nap**, **óra**, **perc**, vagy **második** időközzel.   

   ![Dátum és idő és gyakorisága](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Válassza ki **új lépés** > **művelet hozzáadása**.  

   ![Új lépés, Action hozzáadása](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. A Keresés **AS2**, és válassza ki **AS2 - hozzáadásakor vagy módosításakor a MIC tartalma**.  

   ![MIC hozzáadása vagy frissítése](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Művelet egy másodlagos integrációs fiókkal való kapcsolódáshoz válasszon **kapcsolat módosítása** > **új kapcsolat hozzáadása** a rendelkezésre álló integrációs fiókok listáját. Adja meg a kapcsolat neve, válasszon a *másodlagos régióba integrációs fiók* a listából, és válassza a **létrehozása**.

   ![Másodlagos régióba integrációs fiók neve](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Váltás a jobb felső sarkában található ikonra kattintva nyers bemeneti adatok.

   ![Nyers bemenetek váltani](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Válassza ki a dinamikus tartalom objektumválasztó törzs, és mentse a logikai alkalmazást.   

   ![Dinamikus tartalom](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Az időtartam alapján, az eseményindító kérdezze le az elsődleges régió tábla, és lekéri az új bejegyzések. A művelet frissíti őket a másodlagos régióba integrációs fiókhoz. 
   Ha a nincsenek frissítések, az eseményindító állapota megjelenik **kimaradnak**.  

   ![Elsődleges régió tábla](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Az időtartam alapján, a növekményes futási állapotának replikálja az elsődleges régióban a másodlagos régióba. A katasztrófa események, ha az elsődleges régióban nem áll rendelkezésre, közvetlen forgalom az üzletmenet folytonossága érdekében a másodlagos régióba. 

## <a name="next-steps"></a>További lépések

[B2B üzenetek megfigyelése](logic-apps-monitor-b2b-message.md)

