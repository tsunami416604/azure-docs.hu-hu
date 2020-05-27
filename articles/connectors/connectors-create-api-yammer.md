---
title: Kapcsolódás a Yammer-hez Azure Logic Apps
description: Automatizálhatja az üzeneteket, a hírcsatornákat és a Yammer figyelését, közzétételét és kezelését a Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 3023aa72d713dc25351a6e509319e1c4c0ed609a
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83829664"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>A Yammer-fiók figyelése és kezelése Azure Logic Apps használatával

A Azure Logic Apps és a Yammer-összekötővel olyan automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek az üzenetek, a hírcsatornák és egyebek figyelését és kezelését végzik a Yammer-fiókban, valamint más műveleteket is, például:

* Figyelje meg, hogy mikor jelenjenek meg új üzenetek a követett hírcsatornák és csoportok között.
* Üzenetek, csoportok, hálózatok, felhasználók részleteinek beolvasása.
* Post és Like üzenetek.

Használhat olyan eseményindítókat, amelyek válaszokat kapnak a Yammer-fiókjából, és más műveletek számára elérhetővé teszik a kimenetet. A Yammer-fiókkal feladatokat végrehajtó műveleteket is használhat. Más műveletek is használhatók a Yammer műveletek kimenetének használatával. Ha például új üzenetek jelennek meg a hírcsatornákban vagy a csoportokban, megoszthatja ezeket az üzeneteket a Slack-összekötővel. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* A Yammer-fiók és a felhasználói hitelesítő adatok

   A hitelesítő adatai engedélyezik a logikai alkalmazásnak, hogy kapcsolatot hozzon létre, és hozzáférhessen a Yammer-fiókhoz.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez el szeretné érni a Yammer-fiókját. Egy Yammer triggerrel való kezdéshez [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ha Yammer műveletet szeretne használni, indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **ismétlődési** eseményindítóval.

## <a name="connect-to-yammer"></a>Kapcsolódás a Yammer

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. Válasszon egy elérési utat: 

   * Üres logikai alkalmazások esetén a keresőmezőbe írja be szűrőként a "yammer" kifejezést. 
   Válassza ki a kívánt eseményindítót az eseményindítók listából. 

     -vagy-

   * Meglévő Logic apps esetén: 
   
     * Az utolsó lépésben, amelyben hozzá szeretne adni egy műveletet, válassza az **új lépés**lehetőséget. 

       -vagy-

     * A művelethez hozzáadni kívánt lépések között vigye az egérmutatót a lépések közötti nyíl fölé. 
     Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása**lehetőséget.
     
       A keresőmezőbe írja be szűrőként a "yammer" kifejezést. 
       A műveletek listában válassza ki a kívánt műveletet.

1. Ha a rendszer felszólítja, hogy jelentkezzen be a Yammer-be, jelentkezzen be most, hogy engedélyezze a hozzáférést.

1. Adja meg a kiválasztott trigger vagy művelet szükséges adatait, és folytassa a logikai alkalmazás munkafolyamatának összeállítását.

## <a name="connector-reference"></a>Összekötő-referencia

Az eseményindítókkal, műveletekkel és korlátokkal kapcsolatos technikai részletekért lásd az összekötő OpenAPI (korábban: hencegés) leírását, tekintse át az összekötő [hivatkozási oldalát](/connectors/yammer/).

## <a name="get-support"></a>Támogatás kérése

* Ha kérdése van, látogasson el a [Microsoft Q&a Azure Logic apps vonatkozó kérdés oldalára](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése