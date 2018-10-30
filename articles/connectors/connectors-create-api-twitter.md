---
title: Csatlakozás a Twitterhez, az Azure Logic Apps |} A Microsoft Docs
description: Automatizálhatja a feladatokat és a munkafolyamatok, amelyek figyelése és a Twitter-üzenetek kezelése, valamint a követők, a követett felhasználók, más felhasználók, ütemtervek és több, a Twitter-fiókjából kapcsolatos adatok beolvasása az Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 0fbd89202796cb4543dbecbeee605c9b87cc9d05
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230659"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>Figyelheti és kezelheti a Twitter Azure Logic Apps használatával

Az Azure Logic Apps és a Twitter-összekötő automatizált feladatokat hozhat létre és munkafolyamatok, amelyek figyelése és felügyelete az adatokat konkrét Twitter-például Twitter, a követők, felhasználók, és követni a felhasználók, ütemtervek és más és más műveletek, például:

* Figyelheti, közzététel és tweetek keresése.
* Például a követők, a követett felhasználók, az ütemtervek és egyéb adatok lekérése.

Választ kaphat a Twitter-fiókjával, és egyéb műveleteket hajthat végre elérhetővé a kimeneti eseményindítók is használhatja. Használhatja a végrehajtandó feladatokat a Twitter-fiókjával. Twitter-műveletek a kimenetét használják más műveleteket is rendelkezhet. Például egy adott hashtaggel rendelkező új tweet megjelenésekor küldhet üzeneteket a Slack-összekötővel. Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* A Twitter és a felhasználói fiók hitelesítő adatait

   A hitelesítő adatok engedélyezik a logikai alkalmazás, hozzon létre egy kapcsolatot, és a Twitter-fiókja eléréséhez.

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol szeretné a Twitter-fiók eléréséhez. Egy Twitter-eseményindítót használ első lépésként [hozzon létre egy üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). Egy Twitter-műveletet használja, indítsa el a logikai alkalmazás egy másik eseményindítóval, például a **ismétlődési** eseményindító.

## <a name="connect-to-twitter"></a>Csatlakozás a Twitterhez

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, ha nem, nyissa meg a már.

1. Válassza ki az elérési utat: 

   * Üres logikai alkalmazások, a Keresés mezőbe írja be "twitter" szűrőként. 
   Eseményindítók listája alatt válassza ki a kívánt az eseményindító. 

     – vagy –

   * A meglévő logic apps: 
   
     * Válassza ki az utolsó lépésnél, ahová a művelet hozzáadása, **új lépés**. 

       – vagy –

     * A lépéseket, ahol szeretné művelet hozzáadása, között helyezze az egérmutatót a nyíl lépések között. 
     Válassza a plusz jelre (**+**), amely akkor jelenik meg, és válassza ki **művelet hozzáadása**.
     
       A Keresés mezőbe írja be "twitter" szűrőként. 
       Műveletek listája alatt válassza ki a kívánt művelet.

1. Ha kéri, jelentkezzen be a Twitter, jelentkezzen be most már így is engedélyezze a hozzáférést a a logikai alkalmazás.

1. Adja meg a szükséges adatokat a kijelölt eseményindítót vagy műveletet, és továbbra is használhatja a logic app-munkafolyamatot.

## <a name="examples"></a>Példák

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Twitter-eseményindítót: egy új tweet közzétételekor

Ez az eseményindító a logikaialkalmazás-munkafolyamat az eseményindító észlel egy új tweetet, például a hashtaggel, #Seattle kezdődik. Így például esetén ezek a tweeteket, hozzáadhat egy fájl tartalmát a tartalmazó tweeteket olyan tárhelyre, mint egy Dropbox-fiókra, a Dropbox-összekötő használatával. 

Szükség esetén belefoglalhatja a egy feltételt, amely jogosult tweetek követők legalább egy megadott számú rendelkező felhasználókat kell származnia.

**Példa vállalati**: a trigger használatával tweetek monitorozása a céggel kapcsolatos és a egy SQL Database-adatbázishoz a tartalmazó tweeteket tartalom feltöltése.

### <a name="twitter-action-post-a-tweet"></a>Twitter-művelet: tweet közzététele

Ez a művelet egy tweetet tesz közzé, de beállíthatja a műveletet, hogy a tweet tartalma a tweetek által a korábban ismertetett trigger található. 

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részletek korlátok, eseményindítók és műveletek, amely ismerteti az összekötő OpenAPI által (korábbi nevén Swagger) leírását, tekintse át az összekötő [referencialapja](/connectors/twitterconnector/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)
