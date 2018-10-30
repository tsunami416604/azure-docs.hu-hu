---
title: Csatlakozás a Project Online az Azure Logic Apps |} A Microsoft Docs
description: Figyelő, hozzon létre, és a Project Online-projektek, feladatok és erőforrások kezelése az Azure Logic Apps használatával munkafolyamatok automatizálása
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: 40ce621e-4925-4653-93bb-71ab9abcbdf1
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 663363d05c1875d22a0ecc0478abcf7e0ec89c99
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230369"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Project Online-ban projektek, feladatok és erőforrások kezelése az Azure Logic Apps használatával

Az Azure Logic Apps és a Project Online-összekötő hozhat létre automatizált feladatok és a munkafolyamatok a projektek, feladatok és erőforrások a Project Online Office 365-tel. A munkafolyamatok hajthatja végre ezeket a műveleteket és más, például:

* Ez a figyelő az új projektek, feladatok és erőforrások létrehozásakor. Vagy a figyelő észleli, ha az új projektek közzététele.
* Az új projektek, feladatok és erőforrások létrehozásához.
* Meglévő projektek vagy feladatok listája.
* Tekintse meg, vagy ellenőrizze a projektek közzététele.

Project Online segítségével tervezze, rangsorolására és kezelhetik projektjeiket és a projektporfólió-befektetéseket szinte bárhonnan, szinte bármilyen eszközön azáltal, hogy projektvezetési funkciókat. Project Online-ban eseményindítók, amely választ kaphat a Project Online-hoz és más műveletek számára elérhetővé tenni a kimeneti is használhatja. Műveletek a logic Apps segítségével a Project Online-ban különböző feladatok elvégzésére. Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* A Project Online, elérhető egy [Office 365-fiókja](https://www.office.com/), 

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahová a Project Online-adatok eléréséhez. Project Online-ban eseményindítóval, elindításához [hozzon létre egy üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). Project Online-műveletek használata, indítsa el a logikai alkalmazás egy másik eseményindítóval, például a **ismétlődési** eseményindító.

## <a name="connect-to-project-online"></a>Csatlakozhat a Project Online-hoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, ha nem, nyissa meg a már.

1. Válassza ki az elérési utat: 

   * Üres logic Apps, a keresési mezőbe írja be "Project Online-ban" szűrőként. 
   Eseményindítók listája alatt válassza ki a kívánt az eseményindító. 

     – vagy –

   * Meglévő logic Apps alkalmazások, feladatütemezésekben adjon hozzá egy műveletet, amelyre a válasszon **új lépés**. A Keresés mezőbe írja be "Project Online" szűrőként. Műveletek listája alatt válassza ki a kívánt művelet.

1. Ha kéri, jelentkezzen be a Project Online-ban, jelentkezzen be most.

   A hitelesítő adatok engedélyezik a logikai alkalmazás, hozzon létre egy kapcsolatot a Project Online-hoz, és az adatok eléréséhez.

1. Adja meg a szükséges adatokat a kijelölt eseményindítót vagy műveletet, és továbbra is használhatja a logic app-munkafolyamatot.

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részletek korlátok, eseményindítók és műveletek, amely ismerteti az összekötő OpenAPI által (korábbi nevén Swagger) leírását, tekintse át az összekötő [referencialapja](/connectors/projectonline/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)