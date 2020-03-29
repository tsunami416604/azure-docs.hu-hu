---
title: AS2-üzenetek beállításai
description: Útmutató az AS2 küldési és fogadási beállításaihoz az Azure Logic Apps enterprise integration pack csomaggal
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793028"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Hivatkozás az AS2 üzenetbeállításaihoz az Azure Logic Apps vállalati integrációs csomaggal

Ez a hivatkozás azokat a tulajdonságokat ismerteti, amelyeket beállíthat annak megadásához, hogy az AS2-megállapodás hogyan kezelje a kereskedelmi partnerek között küldött és fogadott üzeneteket. Ezeket a tulajdonságokat az Önnel üzeneteket cserélő partnerrel kötött megállapodás alapján állítsa be.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2 fogadási beállítások

![Válassza ki a "Fogadási beállítások" lehetőséget](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Tulajdonság | Kötelező | Leírás |
|----------|----------|-------------|
| **Üzenet tulajdonságainak felülbírálása** | Nem | Felülírja a bejövő üzenetek tulajdonságait a tulajdonságbeállításokkal. |
| **Az üzenetet alá kell írni** | Nem | Itt adható meg, hogy az összes bejövő üzenetet digitálisan alá kell-e írni. Ha aláírásra van szüksége, a **Tanúsítvány** listából válasszon ki egy meglévő vendégpartnernyilvános tanúsítványt az üzenetek aláírásának érvényesítéséhez. Ha nem rendelkezik tanúsítvánnyal, további információ a [tanúsítványok hozzáadásáról.](../logic-apps/logic-apps-enterprise-integration-certificates.md) |
| **Az üzenetet titkosítani kell** | Nem | Itt adható meg, hogy az összes bejövő üzenetet titkosítani kell-e. A nem titkosított üzenetek et a rendszer elutasítja. Ha titkosításra van szüksége, a **Tanúsítvány** listából válasszon ki egy meglévő állomáspartneri magántanúsítványt a bejövő üzenetek visszafejtéséhez. Ha nem rendelkezik tanúsítvánnyal, további információ a [tanúsítványok hozzáadásáról.](../logic-apps/logic-apps-enterprise-integration-certificates.md) |
| **Az üzenetet tömöríteni kell** | Nem | Itt adható meg, hogy az összes bejövő üzenetet tömöríteni kell-e. A nem tömörített üzenetek et a rendszer elutasítja. |
| **Üzenetazonosító-ismétlődések leengedése** | Nem | Itt adható meg, hogy engedélyezi-e az ismétlődő azonosítóval rendelkező üzenetek et. Ha nem engedélyezi az ismétlődő azonosítókat, adja meg a csekkek közötti napok számát. Azt is megadhatja, hogy felfüggeszti-e az ismétlődéseket. |
| **MDN-szöveg** | Nem | Itt adható meg az üzenet küldőjének elküldeni kívánt alapértelmezett üzenetintézkedési értesítés (MDN). |
| **MDN küldése** | Nem | Itt adható meg, hogy a fogadott üzenetekhez küldjön-e szinkron MDN-eket.  |
| **Aláírt MDN küldése** | Nem | Itt adható meg, hogy a fogadott üzenetekhez küldjön-e aláírt MDN-eket. Ha aláírásra van szüksége, a **MIC algoritmus** listából válassza ki az üzenetek aláírásához használni kívánt algoritmust. |
| **Aszinkron MDN küldése** | Nem | Itt adható meg, hogy az MDN-eket aszinkron módon kell-e küldeni. Ha aszinkron MDN-eket választ, az **URL-címmezőben** adja meg az MDN-ek elküldésének helyének URL-címét. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>AS2 Küldési beállítások

![Válassza a "Beállítások küldése" lehetőséget](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Tulajdonság | Kötelező | Leírás |
|----------|----------|-------------|
| **Üzenetaláírás engedélyezése** | Nem | Itt adható meg, hogy az összes kimenő üzenetet digitálisan alá kell-e írni. Ha aláírásra van szüksége, válassza ki a következő értékeket: <p>- Az **Aláíró algoritmus** listában válassza ki az üzenetek aláírásához használni kívánt algoritmust. <br>- A **Tanúsítvány** listából válasszon ki egy meglévő állomáspartner magántanúsítványt az üzenetek aláírásához. Ha nem rendelkezik tanúsítvánnyal, további információ a [tanúsítványok hozzáadásáról.](../logic-apps/logic-apps-enterprise-integration-certificates.md) |
| **Üzenettitkosítás engedélyezése** | Nem | Itt adható meg, hogy az összes kimenő üzenetet titkosítani kell-e. Ha titkosításra van szüksége, válassza ki a következő értékeket: <p>- A **Titkosítási algoritmus** listából válassza ki az üzenetek titkosításához használni kívánt vendégpartner nyilvános tanúsítványalgoritmusát. <br>- A **Tanúsítvány** listából válasszon ki egy meglévő vendégpartner iontológiát a kimenő üzenetek titkosításához. Ha nem rendelkezik tanúsítvánnyal, további információ a [tanúsítványok hozzáadásáról.](../logic-apps/logic-apps-enterprise-integration-certificates.md) |
| **Üzenettömörítés engedélyezése** | Nem | Itt adható meg, hogy az összes kimenő üzenetet tömöríteni kell-e. |
| **HTTP-fejlécek kibontása** | Nem | A HTTP-fejlécet `content-type` egyetlen sorba helyezi. |
| **Fájlnév továbbítása a MIME fejlécben** | Nem | Itt adható meg, hogy a fájlnév szerepeljen-e a MIME fejlécben. |
| **MDN kérése** | Nem | Itt adható meg, hogy az összes kimenő üzenethez fogadja-e az üzenetintézkedési értesítéseket.Specifies whether to receive message at message to position position notifications (MDNs) for all a menyed messages. |
| **Aláírt MDN-kérelem** | Nem | Itt adható meg, hogy az összes kimenő üzenethez megkapja-e az aláírt MDN-eket. Ha aláírásra van szüksége, a **MIC algoritmus** listából válassza ki az üzenetek aláírásához használni kívánt algoritmust. |
| **Aszinkron MDN kérése** | Nem | Itt adható meg, hogy az MDN-eket aszinkron módon fogadja-e. Ha aszinkron MDN-eket választ, az **URL-címmezőben** adja meg az MDN-ek elküldésének helyének URL-címét. |
| **NRR engedélyezése** | Nem | Itt adható meg, hogy szükség van-e megtagadási elismervényre (NRR). Ez a kommunikációs attribútum bizonyítja, hogy az adatok címzettként érkeztek. |
| **SHA2 algoritmus formátum** | Nem | Megadja a kimenő AS2-üzenetek vagy MDN-üzenetek fejléceiben való aláíráshoz használandó MIC algoritmusformátumot. |
||||

## <a name="next-steps"></a>További lépések

[AS2-üzenetek váltása](../logic-apps/logic-apps-enterprise-integration-as2.md)
