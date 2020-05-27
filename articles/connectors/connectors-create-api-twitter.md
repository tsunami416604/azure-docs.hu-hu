---
title: Kapcsolódás a Twitterhez Azure Logic Apps
description: Automatizálja a tweeteket figyelő és kezelő feladatokat és munkafolyamatokat, valamint az Ön Twitter-Azure Logic Apps fiókjával kapcsolatos adatok lekérése, a követett felhasználók, más felhasználók, határidők és egyebek
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: f2db6d614c3c12cb1be87724e79d79a16769d6b8
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83829596"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>A Twitter figyelése és kezelése Azure Logic Apps használatával

A Azure Logic Apps és a Twitter-összekötővel olyan automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek figyelik és kezelhetik a Twitteren, például a tweetekben, a követőiben, a felhasználóknál és a követett felhasználóknál, időpontokban és egyéb műveletekben, valamint más műveletekben, például:

* Megfigyelheti, közzéteheti és keresheti a tweeteket.
* Többek között a követői, a felhasználók, az idővonalak és sok egyéb információ beolvasása.

Használhat olyan eseményindítókat, amelyek válaszokat kapnak a Twitter-fiókjából, és más műveletek számára elérhetővé teszik a kimenetet. A Twitter-fiókkal feladatokat végrehajtó műveleteket is használhat. A Twitter-műveletek kimenetét más műveletekkel is elvégezheti. Ha például egy új Tweet jelenik meg egy adott hashtagtel, akkor üzeneteket küldhet a Slack-összekötővel. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Twitter-fiókja és felhasználói hitelesítő adatai

   A hitelesítő adatai engedélyezik a logikai alkalmazásnak a kapcsolat létrehozását és a Twitter-fiók elérését.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez el szeretné érni a Twitter-fiókját. A Twitter-triggerrel való kezdéshez [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). Twitter-művelet használatához indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **ismétlődési** eseményindítóval.

## <a name="connect-to-twitter"></a>Csatlakozás a Twitterhez

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. Válasszon egy elérési utat: 

   * Üres logikai alkalmazások esetén a keresőmezőbe írja be szűrőként a "Twitter" kifejezést. 
   Válassza ki a kívánt eseményindítót az eseményindítók listából. 

     -vagy-

   * Meglévő Logic apps esetén: 
   
     * Az utolsó lépésben, amelyben hozzá szeretne adni egy műveletet, válassza az **új lépés**lehetőséget. 

       -vagy-

     * A művelethez hozzáadni kívánt lépések között vigye az egérmutatót a lépések közötti nyíl fölé. 
     Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása**lehetőséget.
     
       A keresőmezőbe írja be a "Twitter" kifejezést a szűrőként. 
       A műveletek listában válassza ki a kívánt műveletet.

1. Ha a rendszer arra kéri, hogy jelentkezzen be a Twitterbe, jelentkezzen be most, így engedélyezheti a hozzáférést a logikai alkalmazáshoz.

1. Adja meg a kiválasztott trigger vagy művelet szükséges adatait, és folytassa a logikai alkalmazás munkafolyamatának összeállítását.

## <a name="examples"></a>Példák

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Twitter-trigger: új Tweet közzétételekor

Ez az aktiválás egy logikai alkalmazás munkafolyamatát indítja el, ha az trigger új tweetet észlel, például a hashtag #Seattle. Így például, ha ezek a tweetek találhatók, hozzáadhat egy fájlt a tárhelyhez, például egy Dropbox-fiókot a Dropbox-összekötő használatával. 

Igény szerint olyan feltételt is megadhat, amely jogosult tweetek a megadott számú követővel rendelkező felhasználóktól származnak.

**Vállalati példa**: ezzel a triggerrel figyelheti a vállalattal kapcsolatos tweeteket, és feltöltheti a tweetek tartalmát egy SQL-adatbázisba.

### <a name="twitter-action-post-a-tweet"></a>Twitter-művelet: Tweet közzététele

Ez a művelet tweetet könyvel, de beállíthatja a műveletet úgy, hogy a tweet tartalmazza a korábban leírt trigger által talált tweetek tartalmát. 

## <a name="connector-reference"></a>Összekötő-referencia

Az eseményindítókkal, műveletekkel és korlátokkal kapcsolatos technikai részletekért lásd az összekötő OpenAPI (korábban: hencegés) leírását, tekintse át az összekötő [hivatkozási oldalát](/connectors/twitterconnector/).

## <a name="get-support"></a>Támogatás kérése

* Ha kérdése van, látogasson el a [Microsoft Q&a Azure Logic apps vonatkozó kérdés oldalára](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
