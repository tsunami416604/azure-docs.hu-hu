---
title: Az RSS-hírcsatornák csatlakozhat az Azure Logic Apps |} A Microsoft Docs
description: Automatizálhatja a feladatokat és a munkafolyamatok, amelyek figyelése és felügyelete az RSS-hírcsatornák Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: a10a6277-ed29-4e68-a881-ccdad6fd0ad8
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 01573871700bbeeb653ce3efdbf6c6aca88fd454
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233055"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>Az RSS-hírcsatornák kezelése az Azure Logic Apps használatával

Az Azure Logic Apps és az RSS-összekötő automatizált feladatokat hozhat létre, és a munkafolyamatokat a minden olyan RSS-hírcsatorna, például:

* Figyelje az RSS-hírcsatornaelem közzétételekor.
* Az összes RSS-hírcsatornaelem listázása.

RSS (Rich hely összegzés), más néven az igazán egyszerű szindikálási egy népszerű webes szindikálási formátum, és gyakran frissített tartalmak, például blogbejegyzések és hírek közzététele szolgál. Számos tartalomszolgáltató biztosít olyan RSS-hírcsatorna így a felhasználók előfizethetnek a tartalomhoz. 

Használhat egy RSS-trigger, amely válaszok olvas be egy RSS-hírcsatorna, és elérhetővé teszi a kimenetben egyéb műveleteket hajthat végre. Egy RSS-műveletet a logic Apps segítségével végrehajtani egy feladatot az RSS-hírcsatorna. Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* RSS-hírcsatorna URL-címe

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol szeretné elérni az RSS-hírcsatorna. Szeretne kezdeni egy RSS-trigger [hozzon létre egy üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). Egy RSS-műveletet használ, indítsa el a logikai alkalmazás egy másik eseményindítóval, például a **ismétlődési** eseményindító.

## <a name="connect-to-an-rss-feed"></a>Csatlakozás egy RSS-hírcsatorna

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, ha nem, nyissa meg a már.

1. Válassza ki az elérési utat: 

   * Üres logic Apps a keresőmezőbe írja be szűrőként "rss". Eseményindítók listája alatt válassza ki a kívánt az eseményindító. 

     – vagy –

   * Meglévő logic Apps alkalmazások, feladatütemezésekben adjon hozzá egy műveletet, amelyre a válasszon **új lépés**. A keresőmezőbe írja be szűrőként az „rss” kifejezést. Műveletek listája alatt válassza ki a kívánt művelet.

1. Adja meg a szükséges adatokat a kijelölt eseményindítót vagy műveletet, és továbbra is használhatja a logic app-munkafolyamatot.

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részletek korlátok, eseményindítók és műveletek, amely ismerteti az összekötő OpenAPI által (korábbi nevén Swagger) leírását, tekintse át az összekötő [referencialapja](/connectors/rss/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)