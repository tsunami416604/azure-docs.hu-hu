---
title: AS2-üzenet beállítások – Azure Logic Apps
description: Az útmutatót AS2 küldése és kapják meg az Azure Logic Apps Enterprise Integration Pack-beállítások
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ead92094b9af1dff56ff68e1ff58a3a4cdd9dca5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "63769451"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>AS2-üzenetek beállításai az Azure Logic Appsben és Enterprise Integration Pack-referencia

Ez a hivatkozás, amely lehet a megadása, hogyan kezeli az AS2-egyezményt a kereskedelmi partnerek között küldött és fogadott üzenetek tulajdonságokat ismerteti. Állítsa be ezeket a tulajdonságokat a partnerrel, amely az üzeneteket, a szerződés alapján.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2-fogadási beállítások

![Válassza a "Kapják meg a beállításokat"](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Tulajdonság | Szükséges | Leírás |
|----------|----------|-------------|
| **Üzenettulajdonságok felülbírálása** | Nem | Felülbírálja a bejövő üzenetek tulajdonságait a eszköztulajdonság-beállítások az. |
| **Üzenet alá kell írni** | Nem | Itt adhatja meg, hogy az összes bejövő üzenetek digitális aláírással kell. Aláírás, ha az a **tanúsítvány** listában, válasszon ki egy meglévő Vendég partner nyilvános az aláírás az üzenetek ellenőrzése. Ha a tanúsítvány nem rendelkezik, tudjon meg többet [tanúsítványok hozzáadása](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Üzenetnek titkosítottnak kell lennie** | Nem | Megadja, hogy minden bejövő üzenetet titkosítani kell. A nem titkosított üzeneteket a rendszer elutasítja. Ha szüksége van a titkosítás, a **tanúsítvány** listájához, válassza ki a meglévő gazdagép partner privát tanúsítványt bejövő üzenetek tartalomkulcsot. Ha a tanúsítvány nem rendelkezik, tudjon meg többet [tanúsítványok hozzáadása](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Üzenetnek tömörítettnek kell lennie** | Nem | Itt adhatja meg, hogy minden bejövő üzenetet kell tömöríteni kell. Nem-tömörített üzeneteket a rendszer elutasítja. |
| **Duplikált üzenetazonosítók letiltása** | Nem | Itt adhatja meg, hogy ismétlődő azonosítókkal rendelkező üzenetek engedélyezi-e. Ha ismétlődő azonosítók letiltása, válassza ki az ellenőrzések között eltelt napok számát. Azt is kiválaszthatja, hogy e ismétlődések felfüggesztése. |
| **MDN-szöveg** | Nem | Adja meg az alapértelmezett üzenet törlése (MDN), amelyeket szeretne küldött értesítést az üzenet küldője. |
| **MDN küldése** | Nem | Megadja, hogy szinkron visszaigazolással érkező üzenetek küldéséhez.  |
| **Aláírt MDN küldése** | Nem | Megadja, hogy aláírt visszaigazolással érkező üzenetek küldéséhez. Aláírás, ha az a **MIC-algoritmust** listájához, válassza ki a üzenetek aláírásához használt algoritmust. |
| **Aszinkron MDN küldése** | Nem | Megadja, hogy az aszinkron küldés visszaigazolással. Ha az aszinkron visszaigazolással a **URL-cím** mezőben adjon meg az URL-címet, amelyre a visszaigazolással. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>AS2-küldési beállítások

![Válassza a "Beállítások küldése"](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Tulajdonság | Szükséges | Leírás |
|----------|----------|-------------|
| **Üzenetek aláírásának engedélyezése** | Nem | Itt adhatja meg, hogy az összes kimenő üzenetek digitális aláírással kell. Ha aláírási van szüksége, válassza ki ezeket az értékeket: <p>-A a **aláírási algoritmus** listájához, válassza ki a üzenetek aláírásához használt algoritmust. <br>-A a **tanúsítvány** listában, válasszon ki egy meglévő gazdagép partner privát üzenetek aláírását. Ha a tanúsítvány nem rendelkezik, tudjon meg többet [tanúsítványok hozzáadása](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Üzenettitkosítás engedélyezése** | Nem | Megadja, hogy titkosítani kell az összes kimenő üzenetek. Titkosítás megkövetelése, válassza ki ezeket az értékeket: <p>-A a **titkosítási algoritmus** listájához, válassza ki a Vendég partner nyilvános tanúsítvány algoritmus üzenetek titkosítására használandó. <br>-A a **tanúsítvány** listájához, válassza ki a meglévő Vendég partner privát tanúsítványt a kimenő üzenetek titkosítására. Ha a tanúsítvány nem rendelkezik, tudjon meg többet [tanúsítványok hozzáadása](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Üzenettömörítés engedélyezése** | Nem | Itt adhatja meg, e minden kimenő üzenetek kell tömöríti. |
| **HTTP-fejlécek kibontása** | Nem | A HTTP-be illeszti be `content-type` egyetlen sor alakzatot fejléc. |
| **A MIME-fejlécben fájlnév továbbítására** | Nem | Megadja, hogy a fájl neve tartalmazza a MIME-fejlécben. |
| **MDN kérése** | Nem | Megadja, hogy az üzenet törlése értesítések (visszaigazolással) minden kimenő üzenetek fogadásához. |
| **Aláírt MDN kérése** | Nem | Megadja, hogy aláírt visszaigazolással minden kimenő üzenetek fogadásához. Aláírás, ha az a **MIC-algoritmust** listájához, válassza ki a üzenetek aláírásához használt algoritmust. |
| **Aszinkron MDN kérése** | Nem | Megadja, hogy visszaigazolással aszinkron fogadásához. Ha az aszinkron visszaigazolással a **URL-cím** mezőben adjon meg az URL-címet, amelyre a visszaigazolással. |
| **NRR engedélyezése** | Nem | Itt adhatja meg, nem megtagadás visszaigazolási (NRR) kötelező legyen-e. A kommunikációs attribútum igazolja, hogy az adatok érkezett, a címzett. |
| **SHA2-algoritmus formátuma** | Nem | Meghatározza a fejléceket, a kimenő AS2-üzenet vagy az MDN az aláíráshoz használandó a MIC-algoritmus formátuma |
||||

## <a name="next-steps"></a>További lépések

[AS2-üzenetek váltása](../logic-apps/logic-apps-enterprise-integration-as2.md)
