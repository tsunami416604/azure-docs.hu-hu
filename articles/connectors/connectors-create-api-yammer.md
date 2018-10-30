---
title: Csatlakozhat a Yammerhez az Azure Logic Apps |} A Microsoft Docs
description: Automatizálhatja a feladatokat és a munkafolyamatok, amelyek figyelése, közzététel és üzenetek, hírcsatornák és más, a Yammer kezelése az Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: ca2d28f3438fd166fa282488206662c95777bf3b
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233327"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Figyelheti, és a Yammer-fiók kezelése az Azure Logic Apps használatával

Az Azure Logic Apps és a Yammer-összekötő hozhat létre automatizált feladatokat és a munkafolyamatok, amelyek figyelése és felügyelete üzeneteket, hírcsatornák és további részében a Yammer-fiókjába, és más műveletek, például:

* Amikor új üzenetek megjelennek a követett hírcsatornák és csoportok figyelése
* Üzenetek, csoportokat, hálózatok, felhasználók részleteit és további beolvasása.
* Közzététel és hasonló üzenetek.

Választ kaphat a Yammer-fiókjából, és egyéb műveleteket hajthat végre elérhetővé a kimeneti eseményindítók is használhatja. Használhatja a végrehajtandó feladatokat a Yammer-fiókjához. A kimenet a Yammer-műveletek használata más műveletek is rendelkezhet. Például ha új üzenet jelenik meg hírcsatornák vagy csoportokat, megoszthatja azokat az üzeneteket a Slack-összekötő. Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* A Yammer és a felhasználói fiók hitelesítő adatait

   A hitelesítő adatok engedélyezik a logikai alkalmazás, hozzon létre egy kapcsolatot, és a Yammer-fiókjába.

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol szeretné a Yammer-fiókjába. Egy Yammer-trigger indít el a [hozzon létre egy üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). Egy Yammer-műveletet használja, indítsa el a logikai alkalmazás egy másik eseményindítóval, például a **ismétlődési** eseményindító.

## <a name="connect-to-yammer"></a>Csatlakozhat a Yammerhez

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, ha nem, nyissa meg a már.

1. Válassza ki az elérési utat: 

   * Üres logikai alkalmazás, a keresőmezőbe írja be "yammer" szűrőként. 
   Eseményindítók listája alatt válassza ki a kívánt az eseményindító. 

     – vagy –

   * A meglévő logic apps: 
   
     * Válassza ki az utolsó lépésnél, ahová a művelet hozzáadása, **új lépés**. 

       – vagy –

     * A lépéseket, ahol szeretné művelet hozzáadása, között helyezze az egérmutatót a nyíl lépések között. 
     Válassza a plusz jelre (**+**), amely akkor jelenik meg, és válassza ki **művelet hozzáadása**.
     
       A Keresés mezőbe írja be "yammer" szűrőként. 
       Műveletek listája alatt válassza ki a kívánt művelet.

1. Ha kéri, jelentkezzen be a Yammeren, jelentkezzen be most Jelentkezzen most így is engedélyezi a hozzáférést.

1. Adja meg a szükséges adatokat a kijelölt eseményindítót vagy műveletet, és továbbra is használhatja a logic app-munkafolyamatot.

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részletek korlátok, eseményindítók és műveletek, amely ismerteti az összekötő OpenAPI által (korábbi nevén Swagger) leírását, tekintse át az összekötő [referencialapja](/connectors/yammer/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)