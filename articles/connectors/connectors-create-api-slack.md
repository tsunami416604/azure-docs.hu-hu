---
title: Csatlakozás az Azure Logic Apps a Slack |} A Microsoft Docs
description: Automatizálhatja a feladatokat és a munkafolyamatok, amelyek a fájl figyelése és a csatornák, csoportok és Slack-fiókjában található üzenetek kezelése az Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 675e37120b06af3add58b564495f22875647a0fa
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230352"
---
# <a name="monitor-and-manage-slack-with-azure-logic-apps"></a>Figyelése és felügyelete az Azure Logic Apps Slack

Az Azure Logic Apps és a Slack-összekötő hozhat létre automatizált feladatokat és a munkafolyamatok, amelyek a Slack fájl figyelése és kezelése a Slack-csatornán, üzeneteket, csoportok és így tovább, például:

* Figyelő, amikor új fájlt hoz létre.
* Létrehozásához, listázásához, és csatlakozzon a csatornák 
* Üzeneteket tehet közzé.
* Csoportok létrehozása és az elfoglalt állapot beállítása.

Választ kaphat a Slack-fiókjával, és egyéb műveleteket hajthat végre elérhetővé a kimeneti eseményindítók is használhatja. Használhatja a végrehajtandó feladatokat a Slack-fiókjához. Slack-műveletek a kimenetét használják más műveleteket is rendelkezhet. Például ha új fájl jön létre, küldhet e-mailt, az Office 365 Outlook-összekötőt. Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* A [Slack](https://slack.com/) fiók és felhasználói hitelesítő adatok

  A hitelesítő adatok engedélyezik a logikai alkalmazás, hozzon létre egy kapcsolatot, és a Slack-fiók eléréséhez.

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol szeretné a Slack-fiók eléréséhez. Egy Slack eseményindítóval elindításához [hozzon létre egy üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). Slack művelettel, indítsa el a logikai alkalmazás egy eseményindítóval, például egy Slack-eseményindító vagy egy eseményindítót, mint például a **ismétlődési** eseményindító.

## <a name="connect-to-slack"></a>Csatlakozhat a Slack

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, ha nem, nyissa meg a már.

1. Üres logic Apps a keresőmezőbe, írja be szűrőként "slack". Eseményindítók listája alatt válassza ki a kívánt az eseményindító. 

   – vagy –

   Meglévő logic Apps alkalmazások, az utolsó lépés, adjon hozzá egy műveletet, amelyre a válasszon **új lépés**. 
   A keresőmezőbe írja be szűrőként "slack". 
   Műveletek listája alatt válassza ki a kívánt művelet.

   Lépések közötti művelet hozzáadása, helyezze az egérmutatót a nyíl lépések között. 
   Válassza a plusz jelre (**+**), amely akkor jelenik meg, és válassza ki **művelet hozzáadása**.

1. Ha kéri, jelentkezzen be a Slack, jelentkezzen be a Slack-munkaterületre. 

   ![Jelentkezzen be a munkaterület Slack](./media/connectors-create-api-slack/slack-sign-in-workspace.png)

1. A logikai alkalmazás hozzáférés engedélyezését.

   ![Slack hozzáférés engedélyezése](./media/connectors-create-api-slack/slack-authorize-access.png)

1. Adja meg a szükséges adatokat a kijelölt eseményindítót vagy műveletet. A logikai alkalmazás munkafolyamat létrehozásához. a folytatáshoz adjon hozzá további műveleteket.

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részletek korlátok, eseményindítók és műveletek, amely ismerteti az összekötő OpenAPI által (korábbi nevén Swagger) leírását, tekintse át az összekötő [referencialapja](/connectors/slack/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)
