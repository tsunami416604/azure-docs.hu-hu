---
title: Csatlakozás a Twitterhez az Azure Logic Apps alkalmazásból
description: A tweeteket figyelő és kezelő feladatok és munkafolyamatok automatizálása, valamint a követők, a követett felhasználók, más felhasználók, ütemtervek és egyebek beszereznie a Twitter-fiókjából az Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 8ffd0fd558cf759fadd912de9dff4acf49d9659f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789086"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>A Twitter figyelése és kezelése az Azure Logic Apps használatával

Az Azure Logic Apps és a Twitter-összekötő segítségével automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek figyelik és kezelik a Twitteren fontos adatokat, például a tweeteket, a követőket, a felhasználókat és a követett felhasználókat, az ütemterveket és egyebeket, például más műveletekkel együtt:

* Figyelheti, közzékell küldenie és tweeteket kell keresnie.
* Szerezzen be olyan adatokat, mint a követők, a követett felhasználók, az ütemtervek stb.

Használhatja az eseményindítók, hogy kap választ a Twitter-fiók, és a kimenet elérhetővé más műveleteket. A Twitter-fiókjával feladatokat végrehajtó műveleteket használhat. Más műveletek is használhatják a Twitter-műveletek kimenetét. Ha például egy adott hashtaggel rendelkező új tweet jelenik meg, üzeneteket küldhet a Tartalékidő-összekötővel. Ha most kezdi meg a logikai alkalmazások, tekintse át [az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Az Ön Twitter-fiókja és felhasználói hitelesítő adatai

   A hitelesítő adatok engedélyezik a logikai alkalmazás számára, hogy kapcsolatot hozzon létre, és hozzáférjen a Twitter-fiókjához.

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol szeretné elérni a Twitter-fiókját. A Twitter-eseményindítóval való kezdéshez [hozzon létre egy üres logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Twitter-művelet használatához indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **Ismétlődés** eseményindítóval.

## <a name="connect-to-twitter"></a>Csatlakozás a Twitterhez

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és nyissa meg a logikai alkalmazást a Logic App Designerben, ha még nem nyitott.

1. Válasszon elérési utat: 

   * Üres logikai alkalmazások esetén a keresőmezőbe írja be szűrőként a "twitter" kifejezést. 
   Az eseményindítók listában válassza ki a kívánt eseményindítót. 

     – vagy –

   * Meglévő logikai alkalmazások esetén: 
   
     * Az utolsó lépésben, ahol műveletet szeretne hozzáadni, válassza az **Új lépés lehetőséget.** 

       – vagy –

     * A lépések között, ahol műveletet szeretne hozzáadni, vigye az egérmutatót a lépések közötti nyíl fölé. 
     Válassza ki a**+** megjelenő pluszjelet ( ), majd válassza **a Művelet hozzáadása**lehetőséget.
     
       A keresőmezőbe írja be a "twitter" szót szűrőként. 
       A műveletek listájában jelölje ki a kívánt műveletet.

1. Ha a rendszer kéri, hogy jelentkezzen be a Twitterre, jelentkezzen be most, hogy engedélyezhesse a hozzáférést a logikai alkalmazáshoz.

1. Adja meg a kiválasztott eseményindító vagy művelet szükséges adatait, és folytassa a logikai alkalmazás munkafolyamatának kiépítését.

## <a name="examples"></a>Példák

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Twitter trigger: Ha egy új csipog van kifüggesztett

Ez az eseményindító elindítja a logikai alkalmazás munkafolyamat, amikor az eseményindító észleli egy új tweet, például a hashtag, #Seattle. Így például, ha ezek a tweetek találhatók, hozzáadhat egy fájlt a tweetek tartalmával a tárolóhoz, például egy Dropbox-fiókot a Dropbox összekötő használatával. 

Tetszés szerint megadhat egy feltételt, hogy a jogosult tweeteknek legalább meghatározott számú követővel rendelkező felhasználóktól kell származniuk.

**Példa vállalati például**: Ezzel az eseményindítóval figyelheti a vállalatával kapcsolatos tweeteket, és feltöltheti a tweetek tartalmát egy SQL-adatbázisba.

### <a name="twitter-action-post-a-tweet"></a>Twitter fellépés: Post a csipog

Ez a művelet tweetet tesz közzé, de beállíthatja a műveletet úgy, hogy a tweet tartalmazza a korábban leírt eseményindító által talált tweetek tartalmát. 

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő OpenAPI (korábbi Swagger) leírása által leírt eseményindítók, műveletek és korlátok technikai részleteiért tekintse át az összekötő [referenciaoldalát.](/connectors/twitterconnector/)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
