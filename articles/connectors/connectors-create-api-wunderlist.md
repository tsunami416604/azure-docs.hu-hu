---
title: Csatlakozás a Wunderlist szolgáltatáshoz az Azure Logic Apps alkalmazásból
description: A Listákat, feladatokat, emlékeztetőket és egyebeket figyelő és kezelő feladatok és munkafolyamatok automatizálása a Wunderlist-fiókban az Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5ac13595bd77238aaede5fa3bdc3a35ef69e8504
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789120"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>A Wunderlist figyelése és kezelése az Azure Logic Apps használatával

Az Azure Logic Apps és a Wunderlist-összekötő segítségével automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek figyelik és kezelik a teendőlistákat, feladatokat, emlékeztetőket és egyebeket a Wunderlist-fiókban, például más műveletekkel együtt:

* Figyelheti, hogy mikor jönnek létre új feladatok, mikor esedékesek a feladatok, vagy mikor történnek emlékeztetők.
* Listák, jegyzetek, feladatok, altevékenységek és egyebek létrehozása és kezelése.
* Emlékeztetők beállítása.
* Listák, feladatok, altevékenységek, emlékeztetők, fájlok, jegyzetek, megjegyzések stb.

[A Wunderlist](https://www.wunderlist.com/) egy olyan szolgáltatás, amely segít megtervezni, kezelni és befejezni a projekteket, a teendőlistákat és a feladatokat - bármilyen eszközön, bárhol. Használhatja az eseményindítók, hogy a wunderlist fiókból kap válaszokat, és a kimenet elérhetővé más műveleteket. A Wunderlist-fiókkal feladatokat végrehajtó műveleteket is használhat. Más műveletek is használhatják a Wunderlist műveletek kimenetét. Ha például új feladatok esedékesek, üzeneteket tehet közzé a Tartalékidő-összekötővel. Ha most kezdi meg a logikai alkalmazások, tekintse át [az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* A Wunderlist-fiók és a felhasználói hitelesítő adatok

   A hitelesítő adatok engedélyezik a logikai alkalmazás számára, hogy kapcsolatot hozzon létre, és hozzáférjen a Wunderlist-fiókhoz.

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez yammer-fiókjához hozzá szeretne férni. A Wunderlist eseményindítóval való kezdéshez [hozzon létre egy üres logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Wunderlist művelet használatához indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **Ismétlődés** eseményindítóval.

## <a name="connect-to-wunderlist"></a>Csatlakozás a Wunderlist listához

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és nyissa meg a logikai alkalmazást a Logic App Designerben, ha még nem nyitott.

1. Válasszon elérési utat: 

   * Üres logikai alkalmazások esetén a keresőmezőbe írja be szűrőként a "wunderlist" kifejezést. 
   Az eseményindítók listában válassza ki a kívánt eseményindítót. 

     – vagy –

   * Meglévő logikai alkalmazások esetén: 
   
     * Az utolsó lépésben, ahol műveletet szeretne hozzáadni, válassza az **Új lépés lehetőséget.** 

       – vagy –

     * A lépések között, ahol műveletet szeretne hozzáadni, vigye az egérmutatót a lépések közötti nyíl fölé. 
     Válassza ki a**+** megjelenő pluszjelet ( ), majd válassza **a Művelet hozzáadása**lehetőséget.
     
       A keresőmezőbe írja be szűrőként a "wunderlist" kifejezést. 
       A műveletek listájában jelölje ki a kívánt műveletet.

1. Ha a rendszer kéri, hogy jelentkezzen be a Wunderlist, jelentkezzen be most, így a hozzáférést engedélyezheti.

1. Adja meg a kiválasztott eseményindító vagy művelet szükséges adatait, és folytassa a logikai alkalmazás munkafolyamatának kiépítését.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő OpenAPI (korábbi Swagger) leírása által leírt eseményindítók, műveletek és korlátok technikai részleteiért tekintse át az összekötő [referenciaoldalát.](/connectors/wunderlist/)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)