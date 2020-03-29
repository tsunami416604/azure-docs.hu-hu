---
title: Csatlakozás a Salesforce-hoz az Azure Logic Apps alkalmazásból
description: A Salesforce-rekordokat és -feladatokat figyelő, létrehozó és kezelő feladatok és munkafolyamatok automatizálása az Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 000f4381ef2a7c0a2099a021b991087725ff2070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789290"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Salesforce-erőforrások figyelése, létrehozása és kezelése az Azure Logic Apps használatával

Az Azure Logic Apps és a Salesforce-összekötő segítségével automatizált feladatokat és munkafolyamatokat hozhat létre a Salesforce-erőforrásokhoz, például rekordokhoz, feladatokhoz és objektumokhoz:

* A rekordok létrehozásának vagy módosításának figyelése. 
* Feladatok és rekordok létrehozása, bekerülése és kezelése, beleértve a műveletek beszúrását, frissítését és törlését.

Használhatja a Salesforce eseményindítókat, amelyek válaszokat kapnak a Salesforce-tól, és a kimenetet elérhetővé teszik más műveletek számára. A logikai alkalmazásokban végrehajtott műveletek segítségével feladatokat hajthat végre a Salesforce-erőforrásokkal. Ha most kezdi meg a logikai alkalmazások, tekintse át [az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* [Salesforce-fiók](https://salesforce.com/)

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez hozzá szeretne férni a Salesforce-fiókhoz. A Salesforce eseményindítóval való kezdéshez [hozzon létre egy üres logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Salesforce-művelet használatához indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **Ismétlődés** eseményindítóval.

## <a name="connect-to-salesforce"></a>Csatlakozás a Salesforce-hoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és nyissa meg a logikai alkalmazást a Logic App Designerben, ha még nem nyitott.

1. Válasszon elérési utat: 

   * Üres logikai alkalmazások esetén a keresőmezőbe írja be szűrőként az "salesforce" kifejezést. 
   Az eseményindítók listában válassza ki a kívánt eseményindítót. 

     – vagy –

   * Meglévő logikai alkalmazások esetén a művelet hozzáadásának lépésalatt válassza az **Új lépés lehetőséget.** A keresőmezőbe írja be szűrőként az "salesforce" kifejezést. A műveletek listájában jelölje ki a kívánt műveletet.

1. Ha a rendszer kéri, hogy jelentkezzen be a Salesforce-ba, jelentkezzen be most, és engedélyezze a hozzáférést.

   A hitelesítő adatok felhatalmazzák a logikai alkalmazást, hogy kapcsolatot hozzon létre a Salesforce-szal, és hozzáférjen az adatokhoz.

1. Adja meg a kiválasztott eseményindító vagy művelet szükséges adatait, és folytassa a logikai alkalmazás munkafolyamatának kiépítését.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő OpenAPI (korábbi Swagger) leírása által leírt eseményindítók, műveletek és korlátok technikai részleteiért tekintse át az összekötő [referenciaoldalát.](/connectors/salesforce/)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)