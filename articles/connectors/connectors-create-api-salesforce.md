---
title: Csatlakozás a Salesforce-hoz az Azure Logic Apps |} A Microsoft Docs
description: Automatizálhatja a feladatokat és a munkafolyamatok, amelyek figyelése, létrehozása és kezelése a Salesforce-rekordok és -feladatokat az Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 54fe5af8-7d2a-4da8-94e7-15d029e029bf
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 03c250f153402c68889c2e3ac187ccab3e2d858b
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887485"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Figyelheti, létrehozása és a Salesforce-erőforrások kezelése az Azure Logic Apps használatával

Az Azure Logic Apps és a Salesforce-összekötő hozhat létre automatizált feladatok és a munkafolyamatok a Salesforce-erőforrások, többek között a rekordokat, feladatok és objektumok, például:

* Ez a figyelő rekordok létrehozott vagy módosított. 
* Hozzon létre, beolvasása, és kezelheti a feladatok és -rekordok, többek között insert, update, és törlési műveleteket.

Salesforce-eseményindítók választ kaphat a Salesforce-ból, és egyéb műveleteket hajthat végre elérhetővé a kimeneti is használhatja. Műveletek a logic Apps segítségével feladatokat a Salesforce-erőforrásokkal. Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* A [Salesforce-fiókban](https://salesforce.com/)

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol szeretné a Salesforce-fiókjába. Salesforce eseményindítóval elindításához [hozzon létre egy üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). A Salesforce műveletet használja, indítsa el a logikai alkalmazás egy másik eseményindítóval, például a **ismétlődési** eseményindító.

## <a name="connect-to-salesforce"></a>Csatlakozás a Salesforce-hoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, ha nem, nyissa meg a már.

1. Válassza ki az elérési utat: 

   * Üres logic Apps a keresőmezőbe, írja be szűrőként "salesforce". 
   Eseményindítók listája alatt válassza ki a kívánt az eseményindító. 

     – vagy –

   * Meglévő logic Apps alkalmazások, feladatütemezésekben adjon hozzá egy műveletet, amelyre a válasszon **új lépés**. A keresőmezőbe írja be szűrőként "salesforce". Műveletek listája alatt válassza ki a kívánt művelet.

1. Ha kéri, jelentkezzen be a Salesforce-hoz, jelentkezzen be most, és engedélyezi a hozzáférést.

   A hitelesítő adatok engedélyezik a logikai alkalmazás, hozzon létre egy kapcsolatot a Salesforce-hoz, és az adatok eléréséhez.

1. Adja meg a szükséges adatokat a kijelölt eseményindítót vagy műveletet, és továbbra is használhatja a logic app-munkafolyamatot.

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részletek korlátok, eseményindítók és műveletek, amely ismerteti az összekötő OpenAPI által (korábbi nevén Swagger) leírását, tekintse át az összekötő [referencialapja](/connectors/salesforce/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)