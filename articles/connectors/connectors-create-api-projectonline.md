---
title: Kapcsolódás a Project online-hoz a Azure Logic Appsból | Microsoft Docs
description: A Project online-projektek,-feladatok és-erőforrások figyelésére, létrehozására és kezelésére szolgáló munkafolyamatok automatizálása Azure Logic Apps használatával
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
ms.openlocfilehash: fe571209d28fe098ce9b507cb67b0a9a5abd25a3
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050861"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>A Project online-projektek,-feladatok és-erőforrások kezelése Azure Logic Apps használatával

A Azure Logic Apps és a Project online-összekötővel automatizált feladatokat és munkafolyamatokat hozhat létre a projektekhez, feladatokhoz és erőforrásokhoz a Project online-ban az Office 365 használatával. A munkafolyamatok ezeket a műveleteket és másokat is elvégezhetik, például:

* Új projektek, feladatok vagy erőforrások létrehozásakor történő figyelése. Vagy figyelje az új projektek közzétételekor.
* Hozzon létre új projekteket, feladatokat vagy erőforrásokat.
* Meglévő projektek vagy feladatok listázása.
* Projektek kijelentkezése, beadása vagy közzététele.

A Project online lehetővé teszi, hogy a projektek és a projekt-portfólió beruházásait szinte bárhonnan, szinte bármilyen eszközről tervezze, rangsorolja és kezelje, hatékony projektmenedzsment-képességek biztosításával. A Project online-eseményindítók segítségével választ kaphat a Project online-ból, és más műveletek számára elérhetővé teheti a kimenetet. A logikai alkalmazásokban lévő műveletek használatával különféle feladatokat végezhet el a Project online-ban. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* A Project online egy [Office 365](https://www.office.com/)-fiókon keresztül érhető el, 

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez el szeretné érni a Project online adatait. Ha egy Project online-triggerrel szeretne kezdeni, [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). A Project online-műveletek használatához indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **ismétlődési** eseményindítóval.

## <a name="connect-to-project-online"></a>Kapcsolódás a Project online-hoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. Válasszon egy elérési utat: 

   * Üres logikai alkalmazások esetén a keresőmezőbe írja be a "Project online" kifejezést a szűrőként. 
   Válassza ki a kívánt eseményindítót az eseményindítók listából. 

     – vagy –

   * A meglévő Logic apps esetében azon a lépésben, amelyben hozzá szeretne adni egy műveletet, válassza az **új lépés**lehetőséget. A keresőmezőbe írja be a "Project online" kifejezést a szűrőként. A műveletek listában válassza ki a kívánt műveletet.

1. Ha a rendszer felszólítja, hogy jelentkezzen be a Project online-ba, jelentkezzen be most.

   A hitelesítő adataival engedélyezheti a logikai alkalmazásnak, hogy kapcsolatot hozzon létre a Project online-ban, és hozzáférhessen az adataihoz.

1. Adja meg a kiválasztott trigger vagy művelet szükséges adatait, és folytassa a logikai alkalmazás munkafolyamatának összeállítását.

## <a name="connector-reference"></a>Összekötő-referencia

Az eseményindítókkal, műveletekkel és korlátokkal kapcsolatos technikai részletekért lásd az összekötő OpenAPI (korábban: hencegés) leírását, tekintse át az összekötő [hivatkozási oldalát](/connectors/projectonline/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése