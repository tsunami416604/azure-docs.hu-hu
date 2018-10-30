---
title: Csatlakozhat a Trellóhoz az Azure Logic Apps |} A Microsoft Docs
description: Automatizálhatja a feladatokat és a munkafolyamatok, amelyek figyelése és felügyelete a listája, a tanácsok és a kártya a Trello-projektek az Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: fe7a4377-5c24-4f72-ab1a-6d9d23e8d895
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 31357fa313cfa31c0f8a90c0f7722f627e4394d1
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50229332"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Figyelése és felügyelete az Azure Logic Apps Trello

Az Azure Logic Apps és a Trello-összekötő hozhat létre automatizált feladatokat és a munkafolyamatok, amelyek figyelése és felügyelete a Trello-lista, kártyák, célra szolgáló kártyákat, a csapattagok és így tovább, például:

* Ez a figyelő hozzáadásakor új kártyák célra szolgáló kártyákat és -listáikhoz. 
* Hozzon létre, lekérése és kezelheti a célra szolgáló kártyákat, kártyákat és listák.
* Megjegyzések és a tagok hozzáadása a kártyák.
* Listázza a célra szolgáló kártyákat, a tábla címkék, a kártyák a célra szolgáló kártyákat, kártya megjegyzések, kártya tagjainak, a csapattagok és a csapatok, ahol Ön a tagja. 
* Csoportok lekérése.

Választ kaphat a Trello-fiókjából, és egyéb műveleteket hajthat végre elérhetővé a kimeneti eseményindítók is használhatja. Használhatja a végrehajtandó feladatokat a Trello-fiókjához. Trello-műveletek a kimenetét használják más műveleteket is rendelkezhet. Például tábla vagy a lista egy új kártya hozzáadásakor küldhet üzeneteket a Slack-összekötővel. Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* A Trello és a felhasználói fiók hitelesítő adatait

  A hitelesítő adatok engedélyezik a logikai alkalmazás, hozzon létre egy kapcsolatot, és a Trello-fiókjába.

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol szeretné a Trello-fiókjába. Egy Trello-trigger indít el a [hozzon létre egy üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). Egy Trello-művelet használatához a logikai alkalmazást egy trigger indít el, például a **ismétlődési** eseményindító.

## <a name="connect-to-trello"></a>Csatlakozhat a Trellóhoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, ha nem, nyissa meg a már.

1. Üres logic Apps a keresőmezőbe írja be szűrőként "trello". Eseményindítók listája alatt válassza ki a kívánt az eseményindító. 

   – vagy –

   Meglévő logic Apps alkalmazások, az utolsó lépés, adjon hozzá egy műveletet, amelyre a válasszon **új lépés**. 
   A Keresés mezőbe írja be szűrőként "trello". 
   Műveletek listája alatt válassza ki a kívánt művelet.

   Lépések közötti művelet hozzáadása, helyezze az egérmutatót a nyíl lépések között. 
   Válassza a plusz jelre (**+**), amely akkor jelenik meg, és válassza ki **művelet hozzáadása**.

1. Ha az kéri, jelentkezzen be a Trello, a logikai alkalmazás hozzáférés engedélyezését, és jelentkezzen be.

1. Adja meg a szükséges adatokat a kijelölt eseményindítót vagy műveletet, és továbbra is használhatja a logic app-munkafolyamatot.

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részletek korlátok, eseményindítók és műveletek, amely ismerteti az összekötő OpenAPI által (korábbi nevén Swagger) leírását, tekintse át az összekötő [referencialapja](/connectors/trello/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)