---
title: Csatlakozás az MQ-kiszolgálóhoz - Azure Logic Apps |} A Microsoft Docs
description: Elküldhetők és beolvashatók az Azure Logic Apps és az Azure vagy a helyszíni MQ server üzenetek
author: valrobb
ms.author: valthom
ms.date: 06/01/2017
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 9e6ae5cb0afd75a1e87fe4d4d0cf307abab5a02a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58167881"
---
# <a name="connect-to-an-ibm-mq-server-from-logic-apps-using-the-mq-connector"></a>A logic apps az MQ-összekötővel csatlakozhat az IBM MQ server kiszolgálóhoz

A Microsoft Connector MQ küld, és beolvassa a MQ Server helyszíni, vagy az Azure-ban tárolt. Ez az összekötő tartalmazza a Microsoft MQ-ügyfél, amely a TCP/IP-hálózaton keresztül egy távoli kiszolgálóval IBM MQ-val kommunikál. Ez a dokumentum egy alapszintű útmutató az MQ-összekötő használatára. Azt javasoljuk, hogy első lépésként böngészés üzenetsorba egy üzenet, és kipróbálja a más műveletek.

Az MQ-összekötő az alábbi műveleteket tartalmazza. Nincsenek nincsenek eseményindítók.

- Keresse meg egy üzenet az IBM MQ kiszolgálóról az üzenet törlése nélkül
- Keresse meg egy üzenetköteget az IBM MQ kiszolgálóról az üzenetek törlése nélkül
- Egyetlen üzenetet kap, és törli az üzenetet az IBM MQ Server kiszolgálóhoz
- Kötegelt üzenetek fogadásához, és törli az üzeneteket az IBM MQ Server kiszolgálóhoz
- Egy üzenet küldéséhez az IBM MQ Server kiszolgálóhoz

## <a name="prerequisites"></a>Előfeltételek

* Ha a helyszíni MQ-kiszolgálót, [a helyszíni adatátjáró telepítése](../logic-apps/logic-apps-gateway-install.md) a hálózaton belüli kiszolgálón. Ha az MQ Server nyilvánosan elérhető, vagy Azure-ban érhető el, majd az átjáró nem használt vagy szükséges.

    > [!NOTE]
    > A kiszolgáló, az On-Premises Data Gateway telepítési helyéül is rendelkeznie kell a .NET Framework 4.6-os az MQ-összekötő működéséhez.

* A helyszíni adatátjáró – Azure-erőforrás létrehozása [a data gateway kapcsolat beállításához](../logic-apps/logic-apps-gateway-connection.md).

* Hivatalosan támogatott IBM WebSphere MQ-verziók:
    * 7.5 MQ
    * MQ 8.0

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

1. Az a **Azure indítsa el a tábla**válassza **+** (plusz) **Web + mobil**, majd **logikai alkalmazás**.
2. Adja meg a **neve**, MQTestApp, például **előfizetés**, **erőforráscsoport**, és **hely** (használhatja azt a helyet, a helyszíni Data Gateway kapcsolat van konfigurálva). Válassza ki **rögzítés az irányítópulton**, és válassza ki **létrehozás**.  
![Logikai alkalmazás létrehozása](media/connectors-create-api-mq/Create_Logic_App.png)

## <a name="add-a-trigger"></a>Adjon hozzá egy triggert

> [!NOTE]
> Az MQ-összekötő nem tartozhat eseményindító. Tehát egy másik eseményindító használatával indítsa el a logikai alkalmazást, mint például a **ismétlődési** eseményindító.

1. A **Logic Apps Designerben** megnyílik, válassza ki **ismétlődési** közös eseményindítók listájában.
2. Válassza ki **szerkesztése** ismétlődési Trigger belül.
3. Állítsa be a **gyakorisága** való **nap**, és állítsa be a **időköz** való **7**.

## <a name="browse-a-single-message"></a>Keresse meg egy adott üzenet
1. Válassza ki **+ új lépés**, és válassza ki **művelet hozzáadása**.
2. A Keresés mezőbe írja be a `mq`, majd válassza ki **MQ - Tallózás üzenet**.  
![Keresse meg az üzenet](media/connectors-create-api-mq/Browse_message.png)

3. Ha nincs meglévő MQ-kapcsolat, majd hozza létre a kapcsolatot:  

    1. Válassza ki **kapcsolódás helyszíni adatátjárón keresztül**, és adja meg az MQ-kiszolgáló tulajdonságait.  
    A **kiszolgáló**, adja meg az MQ-kiszolgáló nevét, vagy adja meg az IP-cím egy kettőspontot és a port számát.
    2. A **átjáró** legördülő menüben bármely meglévő gateway-kapcsolatok konfigurálása megtörtént. Válassza ki az átjáró.
    3. Miután végzett, válassza a **Létrehozás** lehetőséget. A kapcsolat az alábbihoz hasonlóan néz ki:  
    ![Kapcsolat tulajdonságai](media/connectors-create-api-mq/Connection_Properties.png)

4. A művelet tulajdonságait a következőket teheti:  

    * Használja a **várólista** tulajdonság használatával hozzáfér egy másik üzenetsor neve, mint a beállítások határozzák meg a kapcsolat
    * Használja a **üzenetazonosító**, **CorrelationId**, **GroupId**, és egyéb tulajdonságait, hogy keresse meg az üzenetet a különböző MQ-üzenet tulajdonságai alapján
    * Állítsa be **IncludeInfo** való **igaz** újabb üzenetet információval a kimenetben. Vagy állítsa az értékét **hamis** nem újabb üzenetet információval a kimenetben.
    * Adjon meg egy **időtúllépési** érték határozza meg, mennyi ideig kell várni egy üzenet érkezik egy üres üzenetsorhoz. Ha semmit nem adott meg, a rendszer lekéri a várólista első üzenetébe, és nincs várakozás egy hibaüzenet jelenik meg a fordított idő.  
    ![Keresse meg az üzenet tulajdonságai](media/connectors-create-api-mq/Browse_message_Props.png)

5. **Mentés** a kívánt módosításokat, majd **futtatása** a logikai alkalmazás:  
![Mentés és Futtatás](media/connectors-create-api-mq/Save_Run.png)

6. Néhány másodperc elteltével jelennek meg a Futtatás a lépéseket, és tekintse meg a kimenetet. Kattintson ide a részletek minden egyes lépést zöld pipa. Válassza ki **tekintse meg a nyers kimenetek** a kimeneti adatokat további részletek megtekintéséhez.  
![Keresse meg a kimeneti üzenet](media/connectors-create-api-mq/Browse_message_output.png)  

    Nyers kimenet:  
    ![Keresse meg az üzenet nyers kimenet](media/connectors-create-api-mq/Browse_message_raw_output.png)

7. Ha a **IncludeInfo** beállítás értéke igaz, a következő kimenet jelenik meg:  
![Tallózás üzenet adatait tartalmazza](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Több üzenetek tallózása
A **keresse meg az üzenetek** művelet tartalmaz egy **BatchSize** beállítás jelzi, hogy hány üzenetet a rendszer visszalépteti az üzenetsorból.  Ha **BatchSize** nem tartozik bejegyzés, minden üzenetet adja vissza. A visszaadott kimenete egy tömb, az üzenetek.

1. Hozzáadásakor a **keresse meg az üzenetek** műveletet, az első kapcsolat, amely konfigurálva van alapértelmezés szerint ki van választva. Válassza ki **kapcsolat módosítása** hozzon létre egy új kapcsolatot, vagy válasszon egy másik kapcsolatra.

2. A kimenet képernyőképe üzeneteket jeleníti meg:  
![Keresse meg a kimeneti üzenetek](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-a-single-message"></a>Egy üzenet fogadása
A **Receive message** művelet van ugyanazon a forráson, és kiírja a **Tallózás üzenet** művelet. Használata esetén **Receive message**, az üzenet törlődik az üzenetsorból.

## <a name="receive-multiple-messages"></a>Több üzenetek fogadása
A **üzenetek fogadása** művelet van ugyanazon a forráson, és kiírja a **keresse meg az üzenetek** művelet. Használata esetén **üzenetek fogadása**, az üzenetek törlése az üzenetsorból.

Ha nincsenek üzenetek az üzenetsorban a Tallózás gombra, vagy a fogadó oldalon, a lépés sikertelen lesz, a következő eredménnyel:  
![MQ nem jelenik meg hibaüzenet](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-a-message"></a>Üzenet küldése
1. Hozzáadásakor a **üzenetküldés** műveletet, az első kapcsolat, amely konfigurálva van alapértelmezés szerint ki van választva. Válassza ki **kapcsolat módosítása** hozzon létre egy új kapcsolatot, vagy válasszon egy másik kapcsolatra. Az érvényes **üzenettípusok** vannak **Datagram**, **válasz**, vagy **kérelem**.  
![Msg Kellékek küldése](media/connectors-create-api-mq/Send_Msg_Props.png)

2. Üzenet küldése a kimenet az alábbihoz hasonló:  
![Msg kimenet](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Megtekintheti a valamennyi eseményindítót és műveletet a swaggerben meghatározott, és emellett a korlátozott a [összekötő részletei](/connectors/mq/).

## <a name="next-steps"></a>További lépések
[Hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ismerje meg az egyéb elérhető összekötők a Logic Apps, a [API-k listája](apis-list.md).
