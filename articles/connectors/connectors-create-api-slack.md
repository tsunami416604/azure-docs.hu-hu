---
title: Csatlakozás a Slackhez az Azure Logic Apps alkalmazásból
description: A fájlokat figyelő és csatornákat, csoportokat és üzeneteket kezelő feladatok és munkafolyamatok automatizálása a Slack-fiókban az Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5f61009ee7b43be618e37acb4a783a54dbf11e55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789171"
---
# <a name="monitor-and-manage-slack-with-azure-logic-apps"></a>A Slack figyelése és kezelése az Azure Logic-alkalmazásokkal

Az Azure Logic Apps és a Slack-összekötő segítségével automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek figyelik a Tartaléklasztó-fájlokat, és kezelik a Tartalékcsatornákat, üzeneteket, csoportokat és így tovább, például:

* Figyelje, ha új fájlok jönnek létre.
* Csatornák létrehozása, listázása és illesztése 
* Üzeneteket küldeni.
* Hozzon létre csoportokat, és állítsa be a Ne zavarjanak.

Használhatja az eseményindítók, hogy a slack-fiókból kap válaszokat, és a kimenet elérhetővé más műveleteket. A Tartalékidő-fiókkal feladatokat végrehajtó műveleteket is használhatja. Más műveletek is használhatják a Tartalékidő-műveletek kimenetét. Ha például új fájlt hoz létre, e-mailt küldhet az Office 365 Outlook-összekötővel. Ha most kezdi meg a logikai alkalmazások, tekintse át [az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* [A Tartalékidő-fiók](https://slack.com/) és a felhasználói hitelesítő adatok

  A hitelesítő adatok engedélyezik a logikai alkalmazás számára, hogy hozzon létre egy kapcsolatot, és hozzáférjen a Slack-fiókhoz.

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol szeretné elérni a Slack-fiók. A Tartalékidő-eseményindítóval való kezdéshez [hozzon létre egy üres logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Tartalékidő-művelet használatához indítsa el a logikai alkalmazást egy eseményindítóval, például egy tartalékidő-eseményindítóval vagy egy másik eseményindítóval, például az **Ismétlődés** eseményindítóval.

## <a name="connect-to-slack"></a>Csatlakozás a tartalékidőhöz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és nyissa meg a logikai alkalmazást a Logic App Designerben, ha még nem nyitott.

1. Üres logikai alkalmazások esetén a keresőmezőbe írja be a "tartalékidő" szót szűrőként. Az eseményindítók listában válassza ki a kívánt eseményindítót. 

   – vagy –

   Meglévő logikai alkalmazások esetén az utolsó lépésben, ahol műveletet szeretne hozzáadni, válassza az **Új lépés lehetőséget.** 
   A keresőmezőbe írja be szűrőként a "tartalékidő" szót. 
   A műveletek listájában jelölje ki a kívánt műveletet.

   Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egérmutatót a lépések közötti nyíl fölé. 
   Válassza ki a**+** megjelenő pluszjelet ( ), majd válassza **a Művelet hozzáadása**lehetőséget.

1. Ha a rendszer kéri, hogy jelentkezzen be a Slack be, jelentkezzen be a Slack-munkaterületre. 

   ![Bejelentkezés a Slack munkaterületre](./media/connectors-create-api-slack/slack-sign-in-workspace.png)

1. Engedélyezze a hozzáférést a logikai alkalmazáshoz.

   ![Hozzáférés engedélyezése a Tartalékidőhöz](./media/connectors-create-api-slack/slack-authorize-access.png)

1. Adja meg a kiválasztott eseményindító vagy művelet szükséges adatait. A logikai alkalmazás munkafolyamatának további létrehozásához adjon hozzá további műveleteket.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő OpenAPI (korábbi Swagger) leírása által leírt eseményindítók, műveletek és korlátok technikai részleteiért tekintse át az összekötő [referenciaoldalát.](/connectors/slack/)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
