---
title: Az integrációs fiókok vészhelyreállítása
description: Az integrációs fiókok és a B2B-összetevők beállítása régiók közötti vészhelyreállítással az Azure Logic Apps-ben
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/10/2017
ms.openlocfilehash: 09b77862ad3379efeb8b3063a9d6c60b062ca2d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905124"
---
# <a name="set-up-cross-region-disaster-recovery-for-integration-accounts-in-azure-logic-apps"></a>Régiók közötti vészhelyreállítás beállítása integrációs fiókokhoz az Azure Logic Apps-ben

A B2B számítási feladatok pénztranzakciókat foglalnak magukban, például megrendeléseket és számlákat. Egy katasztrófa esetén elengedhetetlen, hogy egy vállalkozás gyorsan felépüljön, hogy megfeleljen a partnereikkel egyeztetett üzleti szintű SLAN-oknak. Ez a cikk bemutatja, hogyan hozhat létre egy üzletmenet-folytonossági terv B2B számítási feladatokhoz. 

* Vész-helyreállítási készültség 
* Feladatátvétel a másodlagos régióba katasztrófa esetén 
* Visszaaz elsődleges régióba egy katasztrófa esemény után

## <a name="disaster-recovery-readiness"></a>Vész-helyreállítási készültség  

1. Azonosítsa a másodlagos régiót, és hozzon létre egy [integrációs fiókot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a másodlagos régióban.

2. Adjon hozzá partnereket, sémákat és megállapodásokat a szükséges üzenetfolyamokhoz, ahol a futtatási állapotot replikálni kell a másodlagos régió integrációs fiókjába.

   > [!TIP]
   > Győződjön meg arról, hogy az integrációs fiók összetevő névhasználati konvenció régiók közötti konzisztencia. 

3. A futtatási állapot lekérése az elsődleges régióból, hozzon létre egy logikai alkalmazást a másodlagos régióban. 

   Ennek a logikai alkalmazásnak rendelkeznie kell egy *eseményindítóval* és egy *művelettel.* 
   Az eseményindítónak csatlakoznia kell az elsődleges régió integrációs fiókjához, és a műveletnek csatlakoznia kell a másodlagos régió integrációs fiókjához. 
   Az időintervallum alapján az eseményindító lekérdezi az elsődleges régió futási állapottábláját, és lekéri az új rekordokat, ha vannak ilyenek. A művelet frissíti őket a másodlagos régió integrációs fiókba. 
   Ez segít a növekményes futásidejű állapot leírásában az elsődleges régióból a másodlagos régióba.

4. A Logic Apps integrációs fiók üzletmenet-folytonossága a B2B protokollokon – X12, AS2 és EDIFACT – alapuló támogatásra szolgál. A részletes lépések megkereséséhez válassza ki a megfelelő hivatkozásokat.

5. A javaslat az, hogy az összes elsődleges régió erőforrásait egy másodlagos régióban is. 

   Az elsődleges régióerőforrásai közé tartozik az Azure SQL Database vagy az Azure Cosmos DB, az Azure Service Bus és az Azure Event Hubs, amelyet az üzenetküldéshez használnak, az Azure API-kezelés és az Azure Logic Apps szolgáltatás az Azure App Service-ben.   

6. Hozzon létre kapcsolatot egy elsődleges régióból egy másodlagos régióval. A futtatási állapot lekérése egy elsődleges régióból, hozzon létre egy logikai alkalmazást egy másodlagos régióban. 

   A logikai alkalmazásnak rendelkeznie kell egy eseményindítóval és egy művelettel. 
   Az eseményindítónak csatlakoznia kell egy elsődleges régióintegrációs fiókhoz. 
   A műveletnek egy másodlagos régióintegrációs fiókhoz kell kapcsolódnia. 
   Az időintervallum alapján az eseményindító lekérdezi az elsődleges régió futási állapottábláját, és lekéri az új rekordokat, ha vannak ilyenek. 
   A művelet egy másodlagos régió integrációs fiókra frissíti őket. 
   Ez a folyamat segít a növekményes futásidejű állapot leírásában az elsődleges régióból a másodlagos régióba.

A Logic Apps integrációs fiók üzletmenet-folytonossága az X12, AS2 és EDIFACT B2B protokollok on alapuló támogatást nyújt. Az X12 és az AS2 használatának részletes lépéseit a cikk [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) és [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) című cikkében olvashatja.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Átadott feladat egy másodlagos régióba egy katasztrófaesemény során

Egy katasztrófa esemény során, ha az elsődleges régió nem érhető el az üzletmenet folytonosságát, közvetlen forgalmat a másodlagos régióba. Egy másodlagos régió segíti a vállalkozásokat abban, hogy gyorsan helyreállítsák a funkciókat, hogy megfeleljenek a partnereik által elfogadott RPO/RTO-nak. Emellett minimálisra csökkenti az egyik régióból a másikba történő feladatátvételre irányuló erőfeszítéseket. 

A vezérlőszámok elsődleges régióból egy másodlagos régióba másolása közben várható késés. Annak elkerülése érdekében, hogy egy katasztrófaesemény során duplikált vezérlőszámokat küldjön a partnereknek, a javaslat az, hogy a [powershell-parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0)használatával a másodlagos régióbeli megállapodásokban lévő vezérlőszámok at kell hozzáadni.

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Visszaad egy elsődleges régiónak a katasztrófa utáni eseményhez

Ha vissza szeretne állni egy elsődleges régióba, amikor az elérhetővé válik, kövesse az alábbi lépéseket:

1. Ne fogadja a másodlagos régióbeli partnerektől érkező üzenetekfogadását.  

2. Az elsődleges régiómegállapodások hozlétre generált vezérlőszámainak növekménye [powershell-parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0)használatával.  

3. Közvetlen forgalom a másodlagos régióból az elsődleges régióba.

4. Ellenőrizze, hogy a másodlagos régióban létrehozott logikai alkalmazás az elsődleges régió futási állapotának lekérnie engedélyezve van-e.

## <a name="x12"></a>X12 

Az EDI X12 dokumentumok üzletmenet-folytonossága a következő vezérlőszámokon alapul:

> [!TIP]
> Az [X12 gyorsindítási sablonnal](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/) logikai alkalmazásokat is létrehozhat. Az elsődleges és másodlagos integrációs fiókok létrehozása a sablon használatának előfeltétele. A sablon segít két logikai alkalmazás létrehozásában, az egyik a fogadott vezérlőszámok, a másik pedig a létrehozott vezérlőszámok létrehozásában. A megfelelő eseményindítók és műveletek jönnek létre a logikai alkalmazások, az eseményindító az elsődleges integrációs fiókhoz, és a művelet a másodlagos integrációs fiókhoz.

**Előfeltételek**

A bejövő üzenetek vészutáni helyreállításának engedélyezéséhez válassza az X12-megállapodás fogadási beállításai között az ismétlődő ellenőrzési beállításokat.

![Ismétlődő ellenőrzési beállítások kiválasztása](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Hozzon létre egy [logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md) egy másodlagos régióban.    

2. Keressen az **X12-n,** és válassza az **X12 – Vezérlőszám módosításakor**lehetőséget.   

   ![X12 keresése](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   Az eseményindító arra kéri, hogy hozzon létre egy kapcsolatot egy integrációs fiókkal. 
   Az eseményindítót egy elsődleges régióintegrációs fiókhoz kell csatlakoztatni.

3. Adjon meg egy kapcsolatnevet, válassza ki az *elsődleges régióintegrációs fiókot* a listából, és válassza a **Létrehozás gombot.**   

   ![Elsődleges régió integrációs fiókneve](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. A **vezérlőszám szinkronizálásának elindításához** szükséges DateTime beállítás megadása nem kötelező. A **gyakoriság** ot egy intervallummal **nap,** **óra,** **perc**vagy **második** értékre állíthatja.   

   ![Dátumidő és gyakoriság](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Válassza **az Új lépés** > **Művelet hozzáadása**lehetőséget.

   ![Új lépés, Művelet hozzáadása](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Keressen az **X12-en**, és válassza az **X12 – Vezérlőszámok hozzáadása vagy frissítése lehetőséget.**   

   ![Vezérlőszámok hozzáadása vagy frissítése](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Ha egy műveletet egy másodlagos régióintegrációs fiókhoz szeretne csatlakoztatni, válassza a **Kapcsolat** > módosítása Új**kapcsolat hozzáadása** lehetőséget az elérhető integrációs fiókok listájához. Adjon meg egy kapcsolatnevet, válassza ki a *másodlagos régióintegrációs fiókot* a listából, és válassza a **Létrehozás gombot.** 

   ![Másodlagos régió integrációs fiókneve](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Váltson nyers bemenetre a jobb felső sarokban lévő ikonra kattintva.

   ![Váltás nyers bemenetre](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Válassza a Törzs lehetőséget a dinamikus tartalomválasztóból, és mentse a logikai alkalmazást.

   ![Dinamikus tartalommezők](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Az időintervallum alapján az eseményindító lekérdezi az elsődleges régiót, amely megkapta a vezérlőszám-táblát, és lekéri az új rekordokat. 
   A művelet frissíti a másodlagos régió integrációs fiókjában lévő rekordokat. 
   Ha nincsenek frissítések, az eseményindító állapota **Kihagyott**állapotként jelenik meg.   

   ![Vezérlőszám tábla](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Az időintervallum alapján a növekményes futásidejű állapot replikálja az elsődleges régióból egy másodlagos régióba. Katasztrófa esetén, ha az elsődleges régió nem érhető el, irányítsa a forgalmat a másodlagos régióba az üzletmenet folytonossága érdekében. 

## <a name="edifact"></a>EDIFACT 

Az EDI EDIFACT dokumentumok üzletmenet-folytonossága az ellenőrző számokon alapul.

**Előfeltételek**

A bejövő üzenetek vészutáni helyreállításának engedélyezéséhez válassza ki az EDIFACT-szerződés fogadási beállításaiközött az ismétlődő ellenőrzési beállításokat.

![Ismétlődő ellenőrzési beállítások kiválasztása](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Hozzon létre egy [logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md) egy másodlagos régióban.    

2. Keresés az **EDIFACT**mezőben , és válassza **az EDIFACT - Vezérlőszám módosításakor**lehetőséget.

   ![EDIFACT keresése](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   Az eseményindító arra kéri, hogy hozzon létre egy kapcsolatot egy integrációs fiókkal. 
   Az eseményindítót egy elsődleges régióintegrációs fiókhoz kell csatlakoztatni. 

3. Adjon meg egy kapcsolatnevet, válassza ki az *elsődleges régióintegrációs fiókot* a listából, és válassza a **Létrehozás gombot.**    

   ![Elsődleges régió integrációs fiókneve](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. A **vezérlőszám szinkronizálásának elindításához** szükséges DateTime beállítás megadása nem kötelező. A **gyakoriság** ot egy intervallummal **nap,** **óra,** **perc**vagy **második** értékre állíthatja.    

   ![Dátumidő és gyakoriság](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Válassza **az Új lépés** > **Művelet hozzáadása**lehetőséget.    

   ![Új lépés, Művelet hozzáadása](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Keressen az **EDIFACT webhelyen**, és válassza **az EDIFACT – Vezérlőszámok hozzáadása vagy frissítése lehetőséget.**   

   ![Vezérlőszámok hozzáadása vagy frissítése](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Ha egy műveletet egy másodlagos régióintegrációs fiókhoz szeretne csatlakoztatni, válassza a **Kapcsolat** > módosítása Új**kapcsolat hozzáadása** lehetőséget az elérhető integrációs fiókok listájához. Adjon meg egy kapcsolatnevet, válassza ki a *másodlagos régióintegrációs fiókot* a listából, és válassza a **Létrehozás gombot.**

   ![Másodlagos régió integrációs fiókneve](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Váltson nyers bemenetre a jobb felső sarokban lévő ikonra kattintva.

   ![Váltás nyers bemenetre](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Válassza a Törzs lehetőséget a dinamikus tartalomválasztóból, és mentse a logikai alkalmazást.   

   ![Dinamikus tartalommezők](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Az időintervallum alapján az eseményindító lekérdezi az elsődleges régiót, amely megkapta a vezérlőszám-táblát, és lekéri az új rekordokat.
   A művelet frissíti a rekordokat a másodlagos régió integrációs fiókhoz. 
   Ha nincsenek frissítések, az eseményindító állapota **Kihagyott**állapotként jelenik meg.

   ![Vezérlőszám tábla](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Az időintervallum alapján a növekményes futásidejű állapot replikálja az elsődleges régióból egy másodlagos régióba. Katasztrófa esetén, ha az elsődleges régió nem érhető el, irányítsa a forgalmat a másodlagos régióba az üzletmenet folytonossága érdekében. 

## <a name="as2"></a>AS2 

Az AS2 protokollt használó dokumentumok üzletmenet-folytonossága az üzenetazonosítón és a MIC-értéken alapul.

> [!TIP]
> Az [AS2 gyorsindítási sablonnal](https://github.com/Azure/azure-quickstart-templates/pull/3302) logikai alkalmazásokat is létrehozhat. Az elsődleges és másodlagos integrációs fiókok létrehozása a sablon használatának előfeltétele. A sablon segít létrehozni egy logikai alkalmazást, amely rendelkezik egy eseményindítóval és egy művelettel. A logikai alkalmazás kapcsolatot hoz létre egy eseményindítóegy elsődleges integrációs fiókés egy műveletet egy másodlagos integrációs fiókhoz.

1. Hozzon létre egy [logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md) a másodlagos régióban.  

2. Keresés az **AS2**területen, és válassza **az AS2 – MIC érték létrehozásakor**lehetőséget.   

   ![AS2 keresése](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   Az eseményindító kéri, hogy hozzon létre egy kapcsolatot egy integrációs fiókkal. 
   Az eseményindítót egy elsődleges régióintegrációs fiókhoz kell csatlakoztatni. 
   
3. Adjon meg egy kapcsolatnevet, válassza ki az *elsődleges régióintegrációs fiókot* a listából, és válassza a **Létrehozás gombot.**

   ![Elsődleges régió integrációs fiókneve](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. A **MIC értékszinkronizálás elindításához szükséges DateTime** beállítás megadása nem kötelező. A **gyakoriság** ot egy intervallummal **nap,** **óra,** **perc**vagy **második** értékre állíthatja.   

   ![Dátumidő és gyakoriság](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Válassza **az Új lépés** > **Művelet hozzáadása**lehetőséget.  

   ![Új lépés, Művelet hozzáadása](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Keressen az **AS2**területen, és válassza **az AS2 – MIC-tartalom hozzáadása vagy frissítése**lehetőséget.  

   ![MIC kiegészítés vagy frissítés](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Ha egy műveletet egy másodlagos integrációs fiókhoz szeretne csatlakoztatni, válassza a **Kapcsolat** > **módosítása Új kapcsolat hozzáadása** lehetőséget az elérhető integrációs fiókok listájához. Adjon meg egy kapcsolatnevet, válassza ki a *másodlagos régióintegrációs fiókot* a listából, és válassza a **Létrehozás gombot.**

   ![Másodlagos régió integrációs fiókneve](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Váltson nyers bemenetre a jobb felső sarokban lévő ikonra kattintva.

   ![Váltás nyers bemenetre](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Válassza a Törzs lehetőséget a dinamikus tartalomválasztóból, és mentse a logikai alkalmazást.   

   ![Dinamikus tartalom](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Az időintervallum alapján az eseményindító lekérdezi az elsődleges régiótáblát, és lekéri az új rekordokat. A művelet frissíti őket a másodlagos régió integrációs fiók. 
   Ha nincsenek frissítések, az eseményindító állapota **Kihagyott**állapotként jelenik meg.  

   ![Elsődleges terület tábla](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Az időintervallum alapján a növekményes futásidejű állapot replikálódik az elsődleges régióból a másodlagos régióba. Katasztrófa esetén, ha az elsődleges régió nem érhető el, irányítsa a forgalmat a másodlagos régióba az üzletmenet folytonossága érdekében. 

## <a name="next-steps"></a>További lépések

[B2B-üzenetek monitorozása Azure Monitor-naplókkal](../logic-apps/monitor-b2b-messages-log-analytics.md)

