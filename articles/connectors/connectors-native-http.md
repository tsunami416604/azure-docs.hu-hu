---
title: Csatlakozás az Azure Logic Apps bármely HTTP-végpont |} A Microsoft Docs
description: Automatizálhatja a feladatokat és a munkafolyamatok, amelyek bármilyen HTTP végponttal kommunikálhat az Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e11c6b4d-65a5-4d2d-8e13-38150db09c0b
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 01da06ca55199989a3a27012bec101580f5ef853
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442205"
---
# <a name="call-http-or-https-endpoints-with-azure-logic-apps"></a>Az Azure Logic Apps HTTP vagy HTTPS-végpontokat

Az Azure Logic Apps és a Hypertext Transfer Protocol (HTTP) összekötőt akkor automatizálhatja, amely minden HTTP vagy HTTPS-végpont kommunikálni a logikai alkalmazások létrehozásával. A szolgáltatásvégpont figyelheti például a webhely. Ha egy esemény történik, ha a webhely leáll, például, hogy a végpont az esemény elindítja a logikai alkalmazás munkafolyamat, és futtatja a megadott műveleteket. 

Használhatja a HTTP-eseményindítóval első lépéseként a munkamenet-ellenőrzési vagy *lekérdezési* egy végpontot, rendszeres ütemezés szerint. Az eseményindító minden ellenőrzés hívást küld vagy *kérelem* a végponthoz. A végpont válasz határozza meg, hogy fut-e a logikai alkalmazás munkafolyamat. Az eseményindító a műveleteknek a logikai alkalmazásban a válaszból továbbítja a tartalmat. 

Használható a HTTP-művelet további lépésként a munkafolyamatban a végpontot hív, ha szeretne. Koncového bodu válasz határozza meg, hogyan fennmaradó a munkafolyamat-műveletek futtatása.

Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* A cél-végpont URL-CÍMÉT szeretné meghívni 

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahonnan csak szeretné meghívni a cél-végpontot, hogy a HTTP-eseményindítóval kezdődnek [hozzon létre egy üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). A HTTP-művelet használatához indítsa el a logikai alkalmazás egy eseményindítóval.

## <a name="add-http-trigger"></a>HTTP-eseményindító hozzáadása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és az üres logikai alkalmazás megnyitása a Logikaialkalmazás-tervezőben, ha nem, nyissa meg a már.

1. A Keresés mezőbe írja be a "http" szűrőként. Az eseményindítók listájában válassza ki a **HTTP** eseményindító. 

   ![Válassza ki a HTTP-eseményindító](./media/connectors-native-http/select-http-trigger.png)

1. Adja meg a [HTTP-trigger paraméterek és értékek](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger) fel szeretne venni a céloldali végpont hívásában. Ellenőrizze a cél-végpont számára, hogy milyen gyakran kívánja az eseményindító ismétlődési beállításával.

   ![Adja meg a HTTP-eseményindító paraméterei](./media/connectors-native-http/http-trigger-parameters.png)

   A HTTP-eseményindító, paraméterek és értékek kapcsolatos további információkért lásd: [eseményindító és művelet típusok hivatkozási](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger).

1. Folytassa a logikai alkalmazás munkafolyamat-műveletek, amelyek futtatását, amikor az eseményindító aktiválódik.

## <a name="add-http-action"></a>HTTP-művelet hozzáadása

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, ha nem, nyissa meg a már.

1. Válassza ki az utolsó lépés, ha szeretné a HTTP-művelet hozzáadása, **új lépés**. 

   Ebben a példában a logikai alkalmazás elindul a HTTP-eseményindítóval rendelkező első lépéseként.

1. A Keresés mezőbe írja be a "http" szűrőként. Műveletek listája alatt válassza ki a **HTTP** művelet.

   ![Válassza ki a HTTP-művelet](./media/connectors-native-http/select-http-action.png)

   Lépések közötti művelet hozzáadása, helyezze az egérmutatót a nyíl lépések között. 
   Válassza a plusz jelre (**+**), amely akkor jelenik meg, és válassza ki **művelet hozzáadása**.

1. Adja meg a [HTTP műveleti paraméterek és értékek](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action) fel szeretne venni a céloldali végpont hívásában. 

   ![Adja meg a HTTP műveleti paraméterek](./media/connectors-native-http/http-action-parameters.png)

1. Ha elkészült, győződjön meg arról, hogy a logikai alkalmazás mentése. A tervező eszköztárán válassza a **Mentés** parancsot. 

## <a name="authentication"></a>Authentication

Hitelesítés beállításához válassza **speciális beállítások megjelenítése** művelettel vagy eseményindítóval belül. Az elérhető hitelesítési típusok HTTP-eseményindítók és műveletek kapcsolatos további információkért lásd: [eseményindító és művelet típusok hivatkozási](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)
