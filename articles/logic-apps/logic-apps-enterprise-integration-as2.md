---
title: AS2-üzenetek küldése és fogadása a B2B-hez
description: Exchange AS2-üzenetek a B2B vállalati integrációs forgatókönyvekhez Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 08/22/2019
ms.openlocfilehash: b2d7c8840da3bb44f9e220f2963dc4fee63176e2
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790713"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Exchange AS2-üzenetek a B2B vállalati integrációhoz Azure Logic Apps a Enterprise Integration Pack

Az AS2-üzenetek Azure Logic Appsban való használatához használhatja az AS2-összekötőt, amely az AS2-kommunikáció kezelésére szolgáló eseményindítókat és műveleteket biztosít. Ha például az üzenetek továbbításakor meg szeretné teremteni a biztonságot és a megbízhatóságot, a következő műveleteket használhatja:

* Az [ **AS2** ](#encode) a titkosítás, a digitális aláírás és a nyugták küldését teszi lehetővé az üzenet-törlési értesítések (MDN) révén, ami segít a nem megtagadási támogatásban. Ez a művelet például az AS2/HTTP fejléceket alkalmazza, és a következő konfiguráláskor hajtja végre ezeket a feladatokat:

  * A kimenő üzeneteket aláírja.
  * Titkosítja a kimenő üzeneteket.
  * Tömöríti az üzenetet.
  * Továbbítja a fájl nevét a MIME-fejlécben.

* [ **AS2-dekódolási** művelet](#decode) a visszafejtés, a digitális aláírás és a nyugták küldéséhez üzenet-törlési értesítések (MDN) használatával. Ez a művelet például a következő feladatokat hajtja végre:

  * AS2/HTTP-fejlécek feldolgozása.
  * Egyezteti a kapott MDNs az eredeti kimenő üzenetekkel.
  * Frissíti és korrelálja a nem megtagadási adatbázisban lévő rekordokat.
  * Az AS2-állapot jelentéskészítési rekordjait írja be.
  * A hasznos adatok tartalmát Base64 kódolással adja vissza.
  * Meghatározza, hogy szükség van-e a MDNs. Az AS2-szerződés alapján meghatározza, hogy a MDNs szinkron vagy aszinkron legyen-e.
  * Az AS2-szerződés alapján szinkron vagy aszinkron MDNs generál.
  * A korrelációs jogkivonatok és tulajdonságok beállítása a MDNs.

  A művelet a konfiguráláskor is végrehajtja ezeket a feladatokat:

  * Ellenőrzi az aláírást.
  * Visszafejti az üzeneteket.
  * Kibontja az üzenetet.
  * Az üzenetek AZONOSÍTÓjának duplikálása és letiltása.

Ez a cikk bemutatja, hogyan adhatja hozzá az AS2-kódolást és a dekódolási műveleteket egy meglévő logikai alkalmazáshoz.

> [!IMPORTANT]
> Az eredeti AS2-összekötő elavult lesz, ezért ne felejtse el használni az **AS2 (v2)** összekötőt. Ez a verzió ugyanazokat a képességeket biztosítja, mint az eredeti verzió, a Logic Apps futtatókörnyezetnek natív, és jelentős teljesítménybeli továbbfejlesztéseket biztosít az átviteli sebesség és az üzenetek mérete tekintetében. Emellett a natív v2-összekötő nem igényli, hogy az integrációs fiókhoz hozzon létre egy kapcsolódást. Ehelyett az előfeltételek szakaszban leírtaknak megfelelően ügyeljen arra, hogy összekapcsolja az integrációs fiókját ahhoz a logikai alkalmazáshoz, ahol az összekötőt használni szeretné.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A logikai alkalmazás, amelyből az AS2-összekötőt és egy olyan triggert szeretne használni, amely elindítja a logikai alkalmazás munkafolyamatát. Az AS2-összekötő csak olyan műveleteket biztosít, amelyek nem triggerek. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át a [Mi az Azure Logic apps](../logic-apps/logic-apps-overview.md) és a gyors útmutató [: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)lehetőséget.

* Az Azure-előfizetéshez társított [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , amely ahhoz a logikai alkalmazáshoz van társítva, ahol az AS2-összekötőt tervezi használni. A logikai alkalmazásnak és az integrációs fióknak ugyanazon a helyen vagy az Azure-régióban kell lennie.

* Legalább két olyan [kereskedelmi partner](../logic-apps/logic-apps-enterprise-integration-partners.md) , amelyet már definiált az integrációs fiókjában az AS2 Identity minősítő használatával.

* Az AS2-összekötő használata előtt létre kell hoznia egy AS2- [szerződést](../logic-apps/logic-apps-enterprise-integration-agreements.md) a kereskedelmi partnerei között, és a szerződést az integrációs fiókban kell tárolnia.

* Ha [Azure Key Vaultt](../key-vault/key-vault-overview.md) használ a Tanúsítványkezelők számára, ellenőrizze, hogy a tároló kulcsai engedélyezik-e a **titkosítási** és a **visszafejtési** műveleteket. Ellenkező esetben a kódolási és a dekódolási műveletek meghiúsulnak.

  A Azure Portal lépjen a kulcstartóba, tekintse meg a tár kulcsának **engedélyezett műveleteit**, és ellenőrizze, hogy ki van-e választva a **titkosítási** és a **visszafejtési** művelet.

  ![A Vault-kulcsok műveleteinek megtekintése](media/logic-apps-enterprise-integration-as2/vault-key-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>AS2-üzenetek kódolása

1. Ha még nem tette meg, a [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. A tervezőben adjon hozzá egy új műveletet a logikai alkalmazáshoz.

1. A **művelet kiválasztása** és a keresőmező területen válassza az **összes**lehetőséget. A keresőmezőbe írja be az "AS2 kódolása" kifejezést, és győződjön meg arról, hogy az AS2 (v2) műveletet választotta: **AS2-kódolás**

   !["AS2 kódolás" kiválasztása](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Most adja meg a következő tulajdonságokkal kapcsolatos információkat:

   | Tulajdonság | Leírás |
   |----------|-------------|
   | **A kódolásra szolgáló üzenet** | Az üzenet tartalma |
   | **AS2-ból** | Az AS2-szerződés által meghatározott üzenet feladójának azonosítója |
   | **AS2-ből** | Az AS2-szerződés által meghatározott üzenet-fogadó azonosítója |
   |||

   Példa:

   ![Üzenet kódolásának tulajdonságai](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

<a name="decode"></a>

## <a name="decode-as2-messages"></a>AS2-üzenetek dekódolása

1. Ha még nem tette meg, a [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. A tervezőben adjon hozzá egy új műveletet a logikai alkalmazáshoz.

1. A **művelet kiválasztása** és a keresőmező területen válassza az **összes**lehetőséget. A keresőmezőbe írja be az "AS2 dekódolása" lehetőséget, és győződjön meg arról, hogy az AS2 (v2) műveletet választotta: **AS2-dekódolás**

   ![Válassza az AS2 dekódolása lehetőséget](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Az **üzenet kódolásához** és az **üzenetek fejlécének** tulajdonságaihoz válassza ki ezeket az értékeket az előző triggerből vagy a művelet kimenetből.

   Tegyük fel például, hogy a logikai alkalmazás fogadja az üzeneteket egy kérelem-triggeren keresztül. Kiválaszthatja az adott trigger kimeneteit.

   ![Törzs és fejlécek kiválasztása a kérelmek kimenetei közül](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>Minta

Egy teljesen működőképes logikai alkalmazás és példa AS2-forgatókönyv üzembe helyezéséhez tekintse meg az [AS2 Logic app-sablon és-forgatókönyv](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)című témakört.

## <a name="connector-reference"></a>Összekötő-referencia

A technikai részleteket, például az eseményindítókat, a műveleteket és a korlátozásokat az összekötő OpenAPI (korábban hencegő) fájljában leírtak szerint tekintse [meg az összekötő hivatkozási oldalát](/connectors/as2/).

## <a name="next-steps"></a>Következő lépések

További információ a [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
