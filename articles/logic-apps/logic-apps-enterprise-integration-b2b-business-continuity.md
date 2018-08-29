---
title: B2B-integrációs fiókok – Azure Logic Apps vészhelyreállítása |} A Microsoft Docs
description: Felkészülés a régiók közötti vész-helyreállítási az Azure Logic Appsben
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 04/10/2017
ms.openlocfilehash: 3d465123f814887282bf2b29a5b6e0836601c243
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123904"
---
# <a name="cross-region-disaster-recovery-for-b2b-integration-accounts-in-azure-logic-apps"></a>Régiók közötti vészhelyreállítása az Azure Logic Apps B2B-integrációs fiókok

B2B pénzt tranzakciók, például a rendeléseket és számlákat telepítse. A vész-helyreállítási esemény során, egy vállalati kritikus fontosságú a gyors helyreállítás megfelelni a vállalati szintű SLA-k teljesítésével partnereikkel. Ez a cikk bemutatja, hogyan hozhat létre egy üzletmenet folytonosságát biztosító terve B2B számítási feladatokhoz. 

* Vész-helyreállítási készültségi 
* Feladatátvételt egy vész-helyreállítási esemény során másodlagos régióba 
* Térhet vissza az elsődleges régióban egy vész-helyreállítási esemény után

## <a name="disaster-recovery-readiness"></a>Vész-helyreállítási készültségi  

1. Egy másodlagos régióba azonosításához, és hozzon létre egy [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a másodlagos régióban.

2. Adja hozzá a partnerek, sémákkal és szerződések, ahol a Futási állapot kell replikálni a másodlagos régióba integrációs fiók szükséges üzenet folyamatokhoz.

   > [!TIP]
   > Ellenőrizze, hogy nincs konzisztencia az integrációs fiók összetevő az elnevezési konvenciót régiók között elosztva. 

3. Kérje le a futtatási állapotát az elsődleges régióban, hozzon létre egy logikai alkalmazást a másodlagos régióban. 

   Ez a logikai alkalmazás rendelkeznie kell egy *eseményindító* és a egy *művelet*. 
   Az eseményindító kapcsolódjon az elsődleges régió integrációs fiók, és a másodlagos régióba integrációs fiók csatlakozik a műveletet. 
   Az időintervallum alapján, az eseményindító kérdezze le az elsődleges régió állapot tábla futtassa, és lekéri az új bejegyzések, ha van ilyen. A művelet frissíti őket a másodlagos régióba integrációs fiókban. 
   Ez segít növekményes futásidejű állapot kérhet elsődleges régió másodlagos régióba.

4. A Logic Apps integrációs fiókban lévő üzletmenet-folytonossági célja, hogy támogatja a B2B-protokollok - X12, és az AS2, EDIFACT alapján. Részletes lépéseket megkereséséhez válassza ki a megfelelő hivatkozásokat.

5. A javaslat az erőforrások üzembe helyezése minden elsődleges régióban egy másodlagos régióban túl. 

   Elsődleges régió erőforrások közé tartoznak, az Azure SQL Database vagy Azure Cosmos DB, Azure Service Bus és az Azure Event Hubs-üzenetkezelés, Azure API Management és az Azure Logic Apps szolgáltatás az Azure App Service-ben használt.   

6. Kapcsolat létrehozása egy elsődleges régióról egy másodlagos régióba. Kérje le a Futási állapot egy elsődleges régióban, hozzon létre egy logikai alkalmazást egy másodlagos régióban. 

   A logikai alkalmazás egy eseményindítót és műveletet kell rendelkeznie. 
   Az eseményindító csatlakozik egy elsődleges régióban integrációs fiókban. 
   A művelet csatlakozzon egy másodlagos régióba integrációs fiókban. 
   Az időintervallum alapján, az eseményindító kérdezze le az elsődleges régió állapot tábla futtassa, és lekéri az új bejegyzések, ha van ilyen. 
   A művelet frissíti őket egy másodlagos régióba integrációs fiókba. 
   Ez a folyamat segít növekményes futásidejű állapot beolvasása az elsődleges régióból a másodlagos régióba.

A Logic Apps integrációs fiókban lévő üzletmenet-folytonossági támogatja a B2B-protokollok X12, AS2 és EDIFACT alapján. A X12 és az AS2 részletes lépéseiért lásd: [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) és [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) ebben a cikkben.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Feladatátvételt egy vész-helyreállítási esemény során egy másodlagos régióba

Során vész-helyreállítási esemény, ha az elsődleges régióban nem érhető el az üzletmenet folytonosságának forgalmat a másodlagos régióba. A partnerek által teljesítésével egy másodlagos régióba segítséget nyújt egy üzleti függvények gyors felel meg az RPO/RTO történő helyreállításához. A minimálisra csökkenti a feladatátvételt egy másik régióban egy adott régióban található erőfeszítéseket is. 

Nincs a várható késés ellenőrzőszámok másolása egy elsődleges régióról egy másodlagos régióba közben. Az ajánlás az ismétlődő létrehozott ellenőrzőszámok partnereknek küldenek egy vész-helyreállítási esemény elkerüléséhez, az ellenőrzőszámok növekszik a másodlagos régióba szerződés használatával [PowerShell-parancsmagok](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Visszatérés egy elsődleges régióban utáni vész-helyreállítási esemény

Visszaállni egy elsődleges régióban elérhetővé válik, kövesse az alábbi lépéseket:

1. A másodlagos régió partnerektől származó üzenetek fogadását.  

2. Növelje a generált ellenőrzőszámok az elsődleges régió-megállapodásokat használatával [PowerShell-parancsmagok](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).  

3. Közvetlen forgalmat a másodlagos régióból az elsődleges régióba.

4. Ellenőrizze, hogy engedélyezve van-e a logikai alkalmazás futtatási állapota az elsődleges régióból történő beolvasás a másodlagos régióban létrehozott.

## <a name="x12"></a>X12 

X 12 dokumentumok EDI az üzletmenet-folytonossági ellenőrzőszámok alapul:

> [!TIP]
> Is használhatja a [X12 quick start sablon](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/) logikai alkalmazásokat hozhat létre. Az elsődleges és másodlagos integrációs fiókok létrehozását is a sablon használatának előfeltételei. A sablon segítségével hozhat létre két a logic apps, az egyik az ellenőrzőszámok kapott, és egy másikat a létrehozott ellenőrzőszámok. Megfelelő triggereket és műveleteket jönnek létre a logic Apps, az eseményindító csatlakozik az elsődleges integrációs fiók és a műveletet úgy, hogy a másodlagos integrációs fiókban.

**Előfeltételek**

Ahhoz, hogy a vész-helyreállítási bejövő üzenetek, válassza ki a duplikált ellenőrzési beállításokat a X12 szerződés fogadási beállítások.

![Ismétlődő ellenőrzési beállítások kiválasztása](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Hozzon létre egy [logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md) egy másodlagos régióban.    

2. A Keresés **X12**, és válassza ki **X12 – egy ellenőrzőszám módosításakor**.   

   ![X12 keresése](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   Az eseményindító kéri, hogy egy kapcsolatot egy integrációs fiókban. 
   Az eseményindító csatlakozniuk kell egy elsődleges régióban integrációs fiókban.

3. Adja meg a kapcsolat nevét, és válassza ki a *elsődleges régió integrációs fiók* a listából, és válassza a **létrehozás**.   

   ![Elsődleges régió integrációs fiók neve](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. A **ellenőrzőszám szinkronizálásának indítási dátuma és ideje** beállítás nem kötelező. A **gyakorisága** állítható **nap**, **óra**, **perc**, vagy **második** időközzel.   

   ![Dátum és idő és a gyakoriság](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Válassza ki **új lépés** > **művelet hozzáadása**.

   ![Új lépés, a művelet hozzáadása](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. A Keresés **X12**, és válassza ki **X12 – a hozzáadandó vagy frissítendő ellenőrzőszámok**.   

   ![Ellenőrzőszámok hozzáadása vagy frissítése](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Szeretne egy másodlagos régióba integrációs fiók művelet csatlakozni, válassza ki a **kapcsolat módosítása** > **új kapcsolat hozzáadása** a rendelkezésre álló integrációs fiókok listáját. Adja meg a kapcsolat nevét, és válassza ki a *másodlagos régióba integrációs fiók* a listából, és válassza a **létrehozás**. 

   ![Másodlagos régió integrációs fiók neve](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Nyers bemenetek váltson a jobb felső sarokban lévő ikonra kattintva.

   ![Váltson át a nyers bemenetek](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Válassza ki a szervezet a dinamikus tartalom választóból, és mentse a logikai alkalmazást.

   ![Dinamikus tartalom mezők](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Az időintervallum alapján, az eseményindító kérdezze le az elsődleges régióba érkezett vezérlő tábla száma, és lekéri az új rekordokat. 
   A művelet frissíti a rekordokat a másodlagos régióba integrációs fiókban. 
   Ha nincsenek frissítések, az eseményindító állapotú **kihagyva**.   

   ![Vezérlő a tábla száma](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Az időintervallum alapján, a növekményes futásidejű állapotát egy elsődleges régióról egy másodlagos régióba replikálja. Során vész-helyreállítási esemény, ha az elsődleges régió nem elérhető, közvetlen forgalmat a másodlagos régióba az üzletmenet folytonosságáról. 

## <a name="edifact"></a>EDIFACT 

Üzletmenet-folytonossági EDI EDIFACT-dokumentumok ellenőrzőszámok alapul.

**Előfeltételek**

Ahhoz, hogy a vész-helyreállítási bejövő üzenetek, válassza ki a duplikált ellenőrzési beállításokat az EDIFACT-egyezmény fogadási beállítások.

![Ismétlődő ellenőrzési beállítások kiválasztása](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Hozzon létre egy [logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md) egy másodlagos régióban.    

2. A Keresés **EDIFACT**, és válassza ki **EDIFACT - egy ellenőrzőszám módosításakor**.

   ![EDIFACT keresése](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   Az eseményindító kéri, hogy egy kapcsolatot egy integrációs fiókban. 
   Az eseményindító csatlakozniuk kell egy elsődleges régióban integrációs fiókban. 

3. Adja meg a kapcsolat nevét, és válassza ki a *elsődleges régió integrációs fiók* a listából, és válassza a **létrehozás**.    

   ![Elsődleges régió integrációs fiók neve](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. A **ellenőrzőszám szinkronizálásának indítási dátuma és ideje** beállítás nem kötelező. A **gyakorisága** állítható **nap**, **óra**, **perc**, vagy **második** időközzel.    

   ![Dátum és idő és a gyakoriság](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Válassza ki **új lépés** > **művelet hozzáadása**.    

   ![Új lépés, a művelet hozzáadása](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. A Keresés **EDIFACT**, és válassza ki **EDIFACT - hozzáadandó vagy frissítendő ellenőrzőszámok**.   

   ![Ellenőrzőszámok hozzáadása vagy frissítése](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Szeretne egy másodlagos régióba integrációs fiók művelet csatlakozni, válassza ki a **kapcsolat módosítása** > **új kapcsolat hozzáadása** a rendelkezésre álló integrációs fiókok listáját. Adja meg a kapcsolat nevét, és válassza ki a *másodlagos régióba integrációs fiók* a listából, és válassza a **létrehozás**.

   ![Másodlagos régió integrációs fiók neve](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Nyers bemenetek váltson a jobb felső sarokban lévő ikonra kattintva.

   ![Váltson át a nyers bemenetek](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Válassza ki a szervezet a dinamikus tartalom választóból, és mentse a logikai alkalmazást.   

   ![Dinamikus tartalom mezők](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Az időintervallum alapján, az eseményindító kérdezze le az elsődleges régióba érkezett vezérlő tábla száma, és lekéri az új rekordokat.
   A művelet frissíti a rekordokat a másodlagos régióba integrációs fiókba. 
   Ha nincsenek frissítések, az eseményindító állapotú **kihagyva**.

   ![Vezérlő a tábla száma](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Az időintervallum alapján, a növekményes futásidejű állapotát egy elsődleges régióról egy másodlagos régióba replikálja. Során vész-helyreállítási esemény, ha az elsődleges régió nem elérhető, közvetlen forgalmat a másodlagos régióba az üzletmenet folytonosságáról. 

## <a name="as2"></a>AS2 

Az AS2-protokollt használó dokumentumok üzletmenet-folytonossági az Üzenetazonosító, és a MIC-érték alapján.

> [!TIP]
> Is használhatja a [AS2 gyors üzembe helyezési sablon](https://github.com/Azure/azure-quickstart-templates/pull/3302) logikai alkalmazásokat hozhat létre. Az elsődleges és másodlagos integrációs fiókok létrehozását is a sablon használatának előfeltételei. A sablon segítségével hozzon létre egy logikai alkalmazást, amely rendelkezik egy eseményindítót és műveletet. A logikai alkalmazás létrehoz egy kapcsolatot egy eseményindítóból egy elsődleges integrációs fiók és a egy olyan műveletet, egy másodlagos integrációs fiókban.

1. Hozzon létre egy [logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md) a másodlagos régióban.  

2. A Keresés **AS2**, és válassza ki **AS2 - amikor egy MIC-érték létrehozása**.   

   ![AS2 keresése](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   Eseményindító kéri, hogy egy kapcsolatot egy integrációs fiókban. 
   Az eseményindító csatlakozniuk kell egy elsődleges régióban integrációs fiókban. 
   
3. Adja meg a kapcsolat nevét, és válassza ki a *elsődleges régió integrációs fiók* a listából, és válassza a **létrehozás**.

   ![Elsődleges régió integrációs fiók neve](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. A **MIC-érték szinkronizálásának indítási dátuma és ideje** beállítás nem kötelező. A **gyakorisága** állítható **nap**, **óra**, **perc**, vagy **második** időközzel.   

   ![Dátum és idő és a gyakoriság](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Válassza ki **új lépés** > **művelet hozzáadása**.  

   ![Új lépés, a művelet hozzáadása](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. A Keresés **AS2**, és válassza ki **AS2 - hozzáadandó vagy frissítendő MIC-tartalmak**.  

   ![MIC hozzáadása vagy frissítése](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Szeretne egy másodlagos integrációs fiók művelet csatlakozni, válassza ki a **kapcsolat módosítása** > **új kapcsolat hozzáadása** a rendelkezésre álló integrációs fiókok listáját. Adja meg a kapcsolat nevét, és válassza ki a *másodlagos régióba integrációs fiók* a listából, és válassza a **létrehozás**.

   ![Másodlagos régió integrációs fiók neve](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Nyers bemenetek váltson a jobb felső sarokban lévő ikonra kattintva.

   ![Váltson át a nyers bemenetek](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Válassza ki a szervezet a dinamikus tartalom választóból, és mentse a logikai alkalmazást.   

   ![Dinamikus tartalom](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Az időintervallum alapján, az eseményindító kérdezze le az elsődleges régió tábla, és lekéri az új rekordokat. A művelet frissíti őket a másodlagos régióba integrációs fiókba. 
   Ha nincsenek frissítések, az eseményindító állapotú **kihagyva**.  

   ![Elsődleges régió tábla](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Az időintervallum alapján, a növekményes futásidejű állapot az elsődleges régióból a másodlagos régióba replikálja. Során vész-helyreállítási esemény, ha az elsődleges régió nem elérhető, közvetlen forgalmat a másodlagos régióba az üzletmenet folytonosságáról. 

## <a name="next-steps"></a>További lépések

[B2B üzenetek megfigyelése](logic-apps-monitor-b2b-message.md)

