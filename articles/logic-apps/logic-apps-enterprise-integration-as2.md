---
title: B2B-vállalati integráció – Azure Logic Apps AS2-üzenetek
description: AS2-üzenetek váltása az Azure Logic Appsben és Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: b494f6524e5105a95bc8a24a6fa2521abcca3f7b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64729394"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>AS2-üzenetek váltása a B2B vállalati integráció az Azure Logic Apps Enterprise Integration Pack

Az Azure Logic Appsben AS2-üzenetek dolgozni, használhatja az AS2-összekötő, amellyel az AS2-kommunikáció kezelése tartozó eseményindítók és műveletek. Ha például létrehozni a biztonságot és a megbízhatóságot, ha üzeneteket küldő, használhatja ezeket a műveleteket:

* [**Kódolás AS2-üzenet** művelet](#encode) titkosítás, digitális aláírás és a nyugtázás a üzenet törlése értesítések (MDN) keresztül, amelyek biztosítják, amelyek támogatása érdekében nem megtagadás. Ez a művelet például AS2/HTTP-fejlécek vonatkozik, és végrehajtja ezeket a feladatokat, ha konfigurálva:

  * Kimenő üzenetek jelentkezik.
  * Kimenő üzenetek titkosítására.
  * Az üzenet tömöríti.
  * A fájl a MIME-fejléc nevét továbbítja.

* [**AS2-üzenet dekódolása** művelet](#decode) visszafejtés, a digitális aláírás és a nyugtázás a üzenet törlése értesítések (MDN) keresztül. Például ez a művelet végzi el ezeket a feladatokat: 

  * Dolgozza fel az AS2/HTTP-fejléceket.
  * Összehangolja ezzel együtt az eredeti kimenő üzenetek kapott visszaigazolással.
  * Az nem megtagadás adatbázis eszközazonosítóként, és frissíti.
  * Az AS2-állapotjelentés rekordokat ír.
  * Kimenetek base64-kódolású, a hasznos tartalom.
  * Azt határozza meg, hogy szükség-e visszaigazolással. Az AS2 alapján megállapodás, határozza meg, hogy visszaigazolással szinkron vagy aszinkron legyen-e.
  * Hoz létre szinkron vagy aszinkron visszaigazolással az AS2-megállapodás alapján.
  * A korrelációs jogkivonatok és a tulajdonságok beállítása visszaigazolással.

  Ez a művelet is ezeket a feladatokat, amikor konfigurálva hajtja végre:

  * Ellenőrzi az aláírást.
  * Mindig visszafejti az üzeneteket.
  * Kibontja az üzenetet. 
  * Ellenőrizze, és üzenet azonosítója ismétlődésének letiltása.

Ez a cikk bemutatja, hogyan adhat hozzá, az AS2-kódolás és dekódolási műveletek egy meglévő logikai alkalmazáshoz.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetésem, [regisztráljon egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/).

* A logikai alkalmazás, ahonnan csak szeretné használni az AS2-összekötő és eseményindító logikai alkalmazás munkafolyamat. Az AS2-összekötő csak műveletek, eseményindítók nem biztosít. Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és [a rövid útmutató: Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Egy [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , amely az Azure-előfizetéséhez társított, és a logikai alkalmazást, ha azt tervezi, hogy az AS2-összekötő használatához kapcsolódó. Mindkét a logikai alkalmazás és az integrációs fiók léteznie kell az ugyanazon a helyen vagy az Azure-régióban.

* Legalább két [kereskedelmi partnerekkel](../logic-apps/logic-apps-enterprise-integration-partners.md) , hogy már meghatározta az integrációs fiókjában az AS2-identitás minősítője használatával.

* Az AS2-összekötő használata előtt létre kell hoznia egy AS2 [szerződés](../logic-apps/logic-apps-enterprise-integration-agreements.md) a kereskedelmi partnerekkel és a tároló között, hogy az integrációs fiókban lévő megállapodás.

* Használatakor [Azure Key Vault](../key-vault/key-vault-overview.md) tanúsítványkezelés, ellenőrizze, hogy engedélyezi-e a tároló kulcsait a **titkosítása** és **visszafejtéséhez** műveleteket. Ellenkező esetben a kódolási és dekódolási műveletek sikertelen.

  Az Azure Portalon nyissa meg a key vault, a tároló kulcs megtekintéséhez **engedélyezett műveletek**, és ellenőrizze, hogy a **titkosítása** és **visszafejtéséhez** műveletek ki van jelölve.

  ![Ellenőrizze a key vault-műveletek](media/logic-apps-enterprise-integration-as2/vault-key-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>AS2-üzenetek kódolása

1. Ha még nem tette, az a [az Azure portal](https://portal.azure.com), nyissa meg a logikai alkalmazás a Logic App Designerben.

1. A tervezőben egy új művelet hozzáadása a logikai alkalmazáshoz. 

1. A **válasszon ki egy műveletet** , és válassza ki a Keresés **összes**. A Keresés mezőbe írja be a "kódolás as2", és válassza a következő műveletet: **Kódolás AS2-üzenet**.

   !["Az AS2-üzenetek kódolása" kiválasztása](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Az integrációs fiókba nem rendelkezik egy meglévő kapcsolatot, ha most, hogy a kapcsolat létrehozására kéri. Nevezze el a kapcsolatot, jelölje ki az integrációs fiók csatlakoztatása, és válassza a kívánt **létrehozás**.

   ![integrációs fiók felé irányuló kapcsolat](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  
 
1. Most adja meg ezeket a tulajdonságokat adatait:

   | Tulajdonság | Leírás |
   |----------|-------------|
   | **AS2-a** | Az üzenet küldője az AS2-egyezményt által megadott azonosítója |
   | **AS2-a** | A üzenetet fogadó által az AS2-egyezményt megadott azonosítója |
   | **body** | Az üzenet hasznos adattartalmából. |
   |||

   Példa:

   ![Üzenet kódolási tulajdonságai](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

<a name="decode"></a>

## <a name="decode-as2-messages"></a>AS2-üzenetek dekódolása

1. Ha még nem tette, az a [az Azure portal](https://portal.azure.com), nyissa meg a logikai alkalmazás a Logic App Designerben.

1. A tervezőben egy új művelet hozzáadása a logikai alkalmazáshoz. 

1. A **válasszon ki egy műveletet** , és válassza ki a Keresés **összes**. A Keresés mezőbe írja be a "as2-dekódolást.", és válassza a következő műveletet: **AS2-üzenet dekódolása**

   ![Válassza ki a "Dekódolási AS2-üzenet"](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Az integrációs fiókba nem rendelkezik egy meglévő kapcsolatot, ha most, hogy a kapcsolat létrehozására kéri. Nevezze el a kapcsolatot, jelölje ki az integrációs fiók csatlakoztatása, és válassza a kívánt **létrehozás**.

   ![integrációs fiók felé irányuló kapcsolat](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  

1. A **törzs** és **fejlécek**, válassza ki ezeket az értékeket az előző eseményindítót vagy műveletet kimenetek.

   Tegyük fel például, hogy a logikai alkalmazás fogadja az üzeneteket a kérelem-eseményindítóval keresztül. A kimenetek az eseményindító közül választhat.

   ![Válassza ki a szervezet és a fejlécek a kérelem kimenetek](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png) 

## <a name="sample"></a>Sample

Próbálja meg egy teljesen működőképes logic app és a minta AS2 forgatókönyv üzembe helyezéséhez, tekintse meg a [AS2 logikaialkalmazás-sablon és a forgatókönyv](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="connector-reference"></a>Összekötő-referencia

A technikai részletekért, például a triggereket, műveletek és -korlátok, leírtak szerint az összekötő OpenAPI (korábbi nevén Swagger) fájl, tekintse meg a [összekötő referenciájának oldalát](/connectors/as2/).

## <a name="next-steps"></a>További lépések

Tudjon meg többet a [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
