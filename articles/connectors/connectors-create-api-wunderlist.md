---
title: Csatlakozhat a Wunderlisthez az Azure Logic Apps |} A Microsoft Docs
description: Automatizálhatja a feladatokat és a munkafolyamatok, amelyek figyelése és felügyelete a listákat, feladatok, emlékeztetők és egyéb, a Wunderlist-fiókban az Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: e3570ab1227ca388ac62bffdc74bb68b1ddc41d1
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230167"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>Figyelheti és kezelheti a Wunderlist Azure Logic Apps használatával

Az Azure Logic Apps és a Wunderlist-összekötő hozhat létre automatizált feladatokat és a munkafolyamatok, amelyek figyelése és felügyelete todo listák, feladatok, emlékeztetők és további részében a Wunderlist-fiók és más műveletek, például:

* Ez a figyelő létrehozásakor új feladatok beolvasása a feladatok esedékes vagy emlékeztetők fordulhat elő.
* Létrehozása és kezelése, listák, jegyzeteket, feladatok, altevékenység és több.
* Emlékeztető beállítása.
* Listák, feladatok, altevékenység, emlékeztetőket, fájlok, megjegyzések, megjegyzések és további beolvasása.

[Wunderlist](https://www.wunderlist.com/) egy szolgáltatás, amely tervezhet, kezelése, és fejezze be a projektek, a teendőlista-kezelő listák és a feladatok – bármilyen eszközről, bárhonnan. Választ kaphat a Wunderlist-fiókjából, és egyéb műveleteket hajthat végre elérhetővé a kimeneti eseményindítók is használhatja. Használhatja a végrehajtandó feladatokat a Wunderlist-fiókjához. Wunderlist-műveletek a kimenetét használják más műveleteket is rendelkezhet. Például ha új feladat esedékes, felteheti üzenetet a Slack-összekötővel. Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* A Wunderlist fiók és felhasználó hitelesítő adatai

   A hitelesítő adatok engedélyezik a logikai alkalmazás, hozzon létre egy kapcsolatot, és a Wunderlist-fiókjába.

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol szeretné a Yammer-fiókjába. Egy Wunderlist-trigger indít el a [hozzon létre egy üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). Egy Wunderlist-teendő használatához indítsa el a logikai alkalmazás egy másik eseményindítóval, például a **ismétlődési** eseményindító.

## <a name="connect-to-wunderlist"></a>Csatlakozhat a Wunderlisthez

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, ha nem, nyissa meg a már.

1. Válassza ki az elérési utat: 

   * Üres logic Apps a keresőmezőbe, írja be szűrőként "wunderlist". 
   Eseményindítók listája alatt válassza ki a kívánt az eseményindító. 

     – vagy –

   * A meglévő logic apps: 
   
     * Válassza ki az utolsó lépésnél, ahová a művelet hozzáadása, **új lépés**. 

       – vagy –

     * A lépéseket, ahol szeretné művelet hozzáadása, között helyezze az egérmutatót a nyíl lépések között. 
     Válassza a plusz jelre (**+**), amely akkor jelenik meg, és válassza ki **művelet hozzáadása**.
     
       A keresőmezőbe írja be szűrőként "wunderlist". 
       Műveletek listája alatt válassza ki a kívánt művelet.

1. Ha kéri, jelentkezzen be a Wunderlist, jelentkezzen be most már így is engedélyezi a hozzáférést.

1. Adja meg a szükséges adatokat a kijelölt eseményindítót vagy műveletet, és továbbra is használhatja a logic app-munkafolyamatot.

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részletek korlátok, eseményindítók és műveletek, amely ismerteti az összekötő OpenAPI által (korábbi nevén Swagger) leírását, tekintse át az összekötő [referencialapja](/connectors/wunderlist/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)