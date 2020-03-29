---
title: Csatlakozás a Project Online-hoz az Azure Logic Apps alkalmazásból
description: A Project Online-projekteket, feladatokat és erőforrásokat figyelő, létrehozó és kezelő munkafolyamatok automatizálása az Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 116922e018410f260b33f4dfd76e5983fbbcc0a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789324"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Project Online-projektek, feladatok és erőforrások kezelése az Azure Logic Apps használatával

Az Azure Logic Apps és a Project Online-összekötő segítségével automatizált feladatokat és munkafolyamatokat hozhat létre projektjeihez, feladataihoz és erőforrásaihoz az Office 365-ön keresztül. A munkafolyamatok végrehajthatják ezeket a műveleteket és másokat, például:

* Figyelje, hogy mikor jönnek létre új projektek, tevékenységek vagy erőforrások. Vagy figyelheti, ha új projektek et tesznek közzé.
* Új projektek, tevékenységek vagy erőforrások létrehozása.
* Meglévő projektek vagy tevékenységek listázása.
* Projektek kijelentkezése, beadása vagy közzététele.

A Project Online segítségével szinte bárhonnan, szinte bármilyen eszközről tervezheti, rangsorolhatja és kezelheti a projekteket és a projektportfólió-befektetéseket, hatékony projektkezelési lehetőségeket biztosítva. A Project Online eseményindítóit olyan eseményindítók segítségével használhatja, amelyek válaszokat kapnak a Project Online-tól, és a kimenetet más műveletek számára is elérhetővé teszik. A logikai alkalmazásokban végrehajtott műveletek segítségével különböző feladatokat hajthat végre a Project Online-ban. Ha most kezdi meg a logikai alkalmazások, tekintse át [az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Project Online, [office 365-fiókon](https://www.office.com/)keresztül érhető el, 

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez hozzá szeretne férni a Project Online-adatokhoz. A Project Online-eseményindítóval való kezdéshez [hozzon létre egy üres logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md) A Project Online-műveletek használatához indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **Ismétlődés** eseményindítóval.

## <a name="connect-to-project-online"></a>Csatlakozás a Project Online-hoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és nyissa meg a logikai alkalmazást a Logic App Designerben, ha még nem nyitott.

1. Válasszon elérési utat: 

   * Üres logikai alkalmazások esetén a keresőmezőbe írja be szűrőként a "Project Online" kifejezést. 
   Az eseményindítók listában válassza ki a kívánt eseményindítót. 

     – vagy –

   * Meglévő logikai alkalmazások esetén a művelet hozzáadásának lépésalatt válassza az **Új lépés lehetőséget.** A keresőmezőbe írja be szűrőként a "Project Online" kifejezést. A műveletek listájában jelölje ki a kívánt műveletet.

1. Ha a rendszer kéri, hogy jelentkezzen be a Project Online-ba, jelentkezzen be most.

   A hitelesítő adatok felhatalmazzák a logikai alkalmazást, hogy kapcsolatot hozzon létre a Project Online-nal, és hozzáférjen az adatokhoz.

1. Adja meg a kiválasztott eseményindító vagy művelet szükséges adatait, és folytassa a logikai alkalmazás munkafolyamatának kiépítését.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő OpenAPI (korábbi Swagger) leírása által leírt eseményindítók, műveletek és korlátok technikai részleteiért tekintse át az összekötő [referenciaoldalát.](/connectors/projectonline/)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)