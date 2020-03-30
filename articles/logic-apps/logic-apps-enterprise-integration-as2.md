---
title: AS2 üzenetek küldése és fogadása a B2B-hez
description: Exchange AS2-üzenetek B2B vállalati integrációs forgatókönyvekhez az Azure Logic Apps vállalati integrációs csomaggal való használatával
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: 0ce813e91750db3cdfa1e651a68fbb82d593eb32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650562"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>EXCHANGE AS2-üzenetek a B2B vállalati integrációhoz az Azure Logic Apps vállalati integrációs csomaggal

> [!IMPORTANT]
> Az eredeti AS2-összekötő elavult, ezért győződjön meg arról, hogy az **AS2 (v2)** összekötőt használja helyette. Ez a verzió ugyanazokat a képességeket biztosítja, mint az eredeti verzió, a Logic Apps futásidejű, és jelentős teljesítményjavulást biztosít az átviteli teljesítmény és az üzenet mérete tekintetében. Emellett a natív v2-összekötő nem követeli meg, hogy hozzon létre egy kapcsolatot az integrációs fiókkal. Ehelyett, az előfeltételekben leírtak szerint győződjön meg arról, hogy az integrációs fiók csatolása a logikai alkalmazás, ahol azt tervezi, hogy használja az összekötőt.

Az AS2-üzenetek azure Logic Apps használata, használhatja az AS2-összekötő, amely eseményindítók és műveletek az AS2 kommunikáció kezelésére. Az üzenetek továbbításakor például az alábbi műveleteket használhatja:

* [ **AS2 Kódolási** művelet](#encode) a titkosítás, a digitális aláírás és a visszaigazolások üzenetrögzítési értesítésekkel (MDN) keresztül történő biztosításához, amelyek segítik a nem megtagadást. Ez a művelet például AS2/HTTP fejléceket alkalmaz, és konfiguráláskor végrehajtja a következő feladatokat:

  * Kimenő üzenetek et ír alá.
  * Titkosítja a kimenő üzeneteket.
  * Tömöríti az üzenetet.
  * A fájlnév továbbítása a MIME fejlécben.

* [ **AS2 Dekódolási** művelet](#decode) a visszafejtés, a digitális aláírás és a nyugtázás üzenettörlési értesítéseken (MDN) keresztül történő biztosításához. Ez a művelet például a következő feladatokat hajtja végre:

  * As2/HTTP fejléceket dolgoz fel.
  * A fogadott MDN-ek és az eredeti kimenő üzenetek egyeztetése.
  * Frissíti és korrelálja a nem megtagadási adatbázis rekordjait.
  * Rekordok at ír az AS2 állapotjelentéshez.
  * Alap64 kódolású tartalom kimenete.
  * Azt határozza meg, hogy szükség van-e MDN-re. Az AS2-megállapodás alapján határozza meg, hogy az MDN-ek szinkronvagy aszinkron legyenek.Based on the AS2 agreement, determines whether MDNs should be be synchronous or aszinkron.
  * Szinkron vagy aszinkron MDN-eket hoz létre az AS2-megállapodás alapján.
  * Beállítja a korrelációs jogkivonatokat és tulajdonságokat az MDN-eken.

  Ez a művelet a következő feladatokat is végrehajtja, ha konfigurálva van:

  * Ellenőrzi az aláírást.
  * Visszafejti az üzeneteket.
  * Kibontja az üzenetet.
  * Az üzenetazonosító-ismétlődések ellenőrzése és leengedése.

Ez a cikk bemutatja, hogyan adhat hozzá az AS2 kódolási és dekódolási műveleteket egy meglévő logikai alkalmazáshoz.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókot.](https://azure.microsoft.com/free/)

* A logikai alkalmazás, ahonnan szeretné használni az AS2-összekötőt, és egy eseményindító, amely elindítja a logikai alkalmazás munkafolyamatát. Az AS2-összekötő csak műveleteket biztosít, nem eseményindítók. Ha most kezdi meg a logikai alkalmazásokat, tekintse át [az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a Rövid útmutató: Az első logikai alkalmazás létrehozása című ismertetése című ismertetése című ismertetése. [Quickstart: Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az Azure-előfizetéshez társított és az AS2-összekötő t használó logikai alkalmazáshoz kapcsolódó [integrációs fiók.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) Mind a logikai alkalmazás és az integrációs fiók léteznie kell ugyanazon a helyen vagy az Azure-régióban.

* Legalább két [olyan kereskedelmi partner,](../logic-apps/logic-apps-enterprise-integration-partners.md) amelyet már definiált az integrációs fiókjában az AS2 identitásminősítő használatával.

* Az AS2-összekötő használata előtt létre kell hoznia egy [AS2-megállapodást](../logic-apps/logic-apps-enterprise-integration-agreements.md) a kereskedelmi partnerei között, és a megállapodást az integrációs fiókjában kell tárolnia.

* Ha [az Azure Key Vault](../key-vault/key-vault-overview.md) tanúsítványkezelés, ellenőrizze, hogy a tároló kulcsok lehetővé teszik a **titkosítási** és **visszafejtési** műveletek. Ellenkező esetben a kódolási és dekódolási műveletek sikertelenek.

  Az Azure Portalon nyissa meg a kulcsot a kulcstartóban, tekintse át a kulcs **engedélyezett műveleteit,** és győződjön meg arról, hogy a **titkosítási** és **visszafejtési** műveletek vannak kiválasztva, például:

  ![A tároló kulcsműveleteinek ellenőrzése](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>AS2 üzenetek kódolása

1. Ha még nem, az [Azure Portalon](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic App Designer.

1. A tervezőben adjon hozzá egy új műveletet a logikai alkalmazáshoz.

1. A Művelet kiválasztása és a keresőmező **csoportban** válassza az **Összes**lehetőséget. A keresőmezőbe írja be az "as2 kódolás" parancsot, és győződjön meg arról, hogy az AS2 (v2) műveletet **választja: AS2 Kódolás**

   ![Válassza az "AS2 Kódolás" lehetőséget](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Most adjon tájékoztatást ezekről a tulajdonságokról:

   | Tulajdonság | Leírás |
   |----------|-------------|
   | **Kódolandó üzenet** | Az üzenet hasznos |
   | **AS2-tól** | Az üzenet küldőjének azonosítója az AS2-megállapodásban meghatározottak szerint |
   | **AS2-hez** | Az üzenetfogadó azonosítója az AS2-szerződésben meghatározottak szerint |
   |||

   Példa:

   ![Üzenetkódolási tulajdonságok](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

> [!TIP]
> Ha az aláírt vagy titkosított üzenetek küldésekor problémákat tapasztal, érdemes kipróbálni a különböző SHA256 algoritmusformátumokat. Az AS2 specifikáció nem nyújt semmilyen információt az SHA256 formátumokról, így minden szolgáltató saját implementációt vagy formátumot használ.

<a name="decode"></a>

## <a name="decode-as2-messages"></a>AS2 üzenetek dekódolása

1. Ha még nem, az [Azure Portalon](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic App Designer.

1. A tervezőben adjon hozzá egy új műveletet a logikai alkalmazáshoz.

1. A Művelet kiválasztása és a keresőmező **csoportban** válassza az **Összes**lehetőséget. A keresőmezőbe írja be az "as2 dekódolás" parancsot, és győződjön meg arról, hogy az AS2 (v2) műveletet **választja: AS2 Dekódolni**

   ![Válassza az "AS2 Dekódolás" lehetőséget](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Ahhoz, hogy az **üzenet kódoljon** és az **Üzenetfejlécek** tulajdonságait, jelölje ki ezeket az értékeket az előző eseményindító vagy műveletkimenetek közül.

   Tegyük fel például, hogy a logikai alkalmazás üzeneteket fogad egy kérelem eseményindítón keresztül. Kiválaszthatja a kimeneteket az adott eseményindítóból.

   ![Törzs és fejlécek kijelölése a kimenetek kérése között](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>Sample

Egy teljesen működőképes logikai alkalmazás üzembe helyezéséhez és az AS2-forgatókönyv mintaalkalmazásához tekintse meg az [AS2 logikai alkalmazássablonját és forgatókönyvét.](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötővel kapcsolatos további technikai részleteket, például az összekötő Swagger-fájljában leírt műveleteket és korlátokat az [összekötő referencialapján](https://docs.microsoft.com/connectors/as2/)találja. 

> [!NOTE]
> Az [integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)lévő logikai alkalmazások esetében az összekötő eredeti ISE-címkével ellátott verziója az [ISE-üzenetkorlátokat](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) használja.

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
