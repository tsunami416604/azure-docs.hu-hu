---
title: Kapcsolódás a wunderlist-hez Azure Logic Apps
description: Automatizálja a wunderlist-fiókban lévő listát, feladatokat és emlékeztetőket figyelő és kezelő feladatokat és munkafolyamatokat a Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5ac13595bd77238aaede5fa3bdc3a35ef69e8504
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789120"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>Wunderlist figyelése és kezelése Azure Logic Apps használatával

A Azure Logic Apps és a wunderlist-összekötővel olyan automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek a wunderlist-fiókban a teendők listáját, feladatait, emlékeztetőit és egyéb műveleteit figyelik és kezelhetik, például a következő műveleteket:

* Figyelje meg, hogy mikor jönnek létre új feladatok, amikor a feladatok esedékesek, vagy emlékeztetők történnek.
* Listát, megjegyzéseket, feladatokat, alfeladatokat és egyebeket hozhat létre és kezelhet.
* Emlékeztetők beállítása
* Listázza a listát, a feladatokat, az alfeladatokat, a emlékeztetőket, a fájlokat, a megjegyzéseket, a megjegyzéseket és egyebeket.

A [wunderlist](https://www.wunderlist.com/) egy olyan szolgáltatás, amely segít megtervezni, kezelni és befejezni a projektjeit, teendői listáját és feladatait bármilyen eszközön, bárhol. Használhat olyan eseményindítókat, amelyek válaszokat kapnak a wunderlist-fiókjából, és más műveletek számára elérhetővé teszik a kimenetet. A wunderlist-fiókkal feladatokat végrehajtó műveleteket is használhat. Más műveletek is használhatók a wunderlist műveletek kimenetének használatával. Ha például az új feladatok esedékesek, üzeneteket tehet közzé a Slack-összekötővel. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* A wunderlist-fiók és a felhasználói hitelesítő adatok

   A hitelesítő adatai engedélyezik a logikai alkalmazásnak, hogy kapcsolatot hozzon létre, és hozzáférhessen a wunderlist-fiókhoz.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez el szeretné érni a Yammer-fiókját. Egy wunderlist triggerrel való kezdéshez [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ha wunderlist műveletet szeretne használni, indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **ismétlődési** eseményindítóval.

## <a name="connect-to-wunderlist"></a>Kapcsolódás a wunderlist

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. Válasszon egy elérési utat: 

   * Üres logikai alkalmazások esetén a keresőmezőbe írja be szűrőként a "wunderlist" kifejezést. 
   Válassza ki a kívánt eseményindítót az eseményindítók listából. 

     – vagy –

   * Meglévő Logic apps esetén: 
   
     * Az utolsó lépésben, amelyben hozzá szeretne adni egy műveletet, válassza az **új lépés**lehetőséget. 

       – vagy –

     * A művelethez hozzáadni kívánt lépések között vigye az egérmutatót a lépések közötti nyíl fölé. 
     Válassza ki a megjelenő pluszjelet (**+**), majd válassza a **művelet hozzáadása**lehetőséget.
     
       A keresőmezőbe írja be szűrőként a "wunderlist" kifejezést. 
       A műveletek listában válassza ki a kívánt műveletet.

1. Ha a rendszer felszólítja, hogy jelentkezzen be a wunderlist-be, jelentkezzen be most, hogy engedélyezze a hozzáférést.

1. Adja meg a kiválasztott trigger vagy művelet szükséges adatait, és folytassa a logikai alkalmazás munkafolyamatának összeállítását.

## <a name="connector-reference"></a>Összekötő-referencia

Az eseményindítókkal, műveletekkel és korlátokkal kapcsolatos technikai részletekért lásd az összekötő OpenAPI (korábban: hencegés) leírását, tekintse át az összekötő [hivatkozási oldalát](/connectors/wunderlist/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése