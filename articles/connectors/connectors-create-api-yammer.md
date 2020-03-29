---
title: Csatlakozás a Yammerhez az Azure Logic Apps alkalmazásból
description: Az Azure Logic Apps használatával automatizálhatja az üzeneteket, hírcsatornákat és egyebeket figyelő, közzékívánt és felügyelt feladatokat és munkafolyamatokat a Yammerben
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 194c08d034d44ba0a4472b3b516fc45d1d262d28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789069"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Yammer-fiókjának figyelése és kezelése az Azure Logic Apps használatával

Az Azure Logic Apps és a Yammer-összekötő segítségével automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek figyelik és kezelik az üzeneteket, hírcsatornákat és egyebeket a Yammer-fiókjában, például más műveletekkel együtt:

* Figyelje, hogy mikor jelennek meg új üzenetek a követett hírcsatornákban és csoportokban.
* Üzenetek, csoportok, hálózatok, felhasználók adatai és egyebek.
* Üzenetek küldése és kedvelése.

Olyan eseményindítókat használhat, amelyek válaszokat kapnak Yammer-fiókjából, és a kimenetet elérhetővé teszik más műveletek számára. Yammer-fiókjával műveleteket végrehajtó műveleteket használhat. Más műveletek is használhatják a Yammer-műveletek kimenetét. Ha például új üzenetek jelennek meg hírcsatornákban vagy csoportokban, megoszthatja ezeket az üzeneteket a Tartalékidő-összekötővel. Ha most kezdi meg a logikai alkalmazások, tekintse át [az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Yammer-fiókja és felhasználói hitelesítő adatai

   A hitelesítő adatok felhatalmazzák a logikai alkalmazást, hogy kapcsolatot hozzon létre, és hozzáférjen Yammer-fiókjához.

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez yammer-fiókjához hozzá szeretne férni. A Yammer-eseményindítóval való kezdéshez [hozzon létre egy üres logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Yammer-művelet használatához indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **Ismétlődés** eseményindítóval.

## <a name="connect-to-yammer"></a>Csatlakozás a Yammerhez

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és nyissa meg a logikai alkalmazást a Logic App Designerben, ha még nem nyitott.

1. Válasszon elérési utat: 

   * Üres logikai alkalmazások esetén a keresőmezőbe írja be szűrőként a "yammer" kifejezést. 
   Az eseményindítók listában válassza ki a kívánt eseményindítót. 

     – vagy –

   * Meglévő logikai alkalmazások esetén: 
   
     * Az utolsó lépésben, ahol műveletet szeretne hozzáadni, válassza az **Új lépés lehetőséget.** 

       – vagy –

     * A lépések között, ahol műveletet szeretne hozzáadni, vigye az egérmutatót a lépések közötti nyíl fölé. 
     Válassza ki a**+** megjelenő pluszjelet ( ), majd válassza **a Művelet hozzáadása**lehetőséget.
     
       A keresőmezőbe írja be szűrőként a "yammer" kifejezést. 
       A műveletek listájában jelölje ki a kívánt műveletet.

1. Ha a rendszer kéri, hogy jelentkezzen be a Yammerbe, jelentkezzen be most, hogy engedélyezhesse a hozzáférést.

1. Adja meg a kiválasztott eseményindító vagy művelet szükséges adatait, és folytassa a logikai alkalmazás munkafolyamatának kiépítését.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő OpenAPI (korábbi Swagger) leírása által leírt eseményindítók, műveletek és korlátok technikai részleteiért tekintse át az összekötő [referenciaoldalát.](/connectors/yammer/)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)