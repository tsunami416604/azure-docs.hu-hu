---
title: Kapcsolódás a Trello-hez Azure Logic Apps
description: Automatizálja a Trello-projektekben található listát, táblákat és kártyákat figyelő és kezelő feladatokat és munkafolyamatokat Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5c4fcb9b4fea1a4d982b5cf665564599d371b7cb
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789137"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Trello figyelése és kezelése Azure Logic Apps

A Azure Logic Apps és a Trello-összekötővel automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek figyelik és kezelhetik a Trello-listát, a kártyákat, a táblákat, a csapattagokat stb. például:

* Figyelje meg, hogy mikor kerül be új kártyák a táblákba és a listára. 
* Táblák, kártyák és címlisták létrehozása, beolvasása és kezelése.
* Megjegyzéseket és tagokat adhat hozzá a kártyákhoz.
* Táblák, üzenőfalak, kártyák a lapokon, a kártyák megjegyzései, a kártyák tagjai, a csapattagok és a csapatok névsora, ahol Ön a tag. 
* Csapatok beolvasása.

Használhat olyan eseményindítókat, amelyek válaszokat kapnak a Trello-fiókjából, és más műveletek számára elérhetővé teszik a kimenetet. A Trello-fiókkal feladatokat végrehajtó műveleteket is használhat. Más műveletek is használhatók a Trello műveletek kimenetének használatával. Ha például új kártyát adnak hozzá a táblához vagy a listához, akkor üzeneteket küldhet a Slack-összekötővel. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* A Trello-fiók és a felhasználói hitelesítő adatok

  A hitelesítő adatai engedélyezik a logikai alkalmazásnak, hogy kapcsolatot hozzon létre, és hozzáférhessen a Trello-fiókhoz.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez el szeretné érni a Trello-fiókját. Egy Trello triggerrel való kezdéshez [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ha Trello műveletet szeretne használni, indítsa el a logikai alkalmazást egy eseményindítóval, például az **Ismétlődés** eseményindítóját.

## <a name="connect-to-trello"></a>Kapcsolódás a Trello

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. Üres logikai alkalmazások esetén a keresőmezőbe írja be szűrőként a "trello" kifejezést. Válassza ki a kívánt eseményindítót az eseményindítók listából. 

   – vagy –

   Meglévő Logic apps esetén az utolsó lépésben, amelyhez műveletet szeretne hozzáadni, válassza az **új lépés**lehetőséget. 
   A keresőmezőbe írja be szűrőként a "trello" kifejezést. 
   A műveletek listában válassza ki a kívánt műveletet.

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. 
   Válassza ki a megjelenő pluszjelet ( **+** ), majd válassza a **művelet hozzáadása**lehetőséget.

1. Ha a rendszer felszólítja, hogy jelentkezzen be a Trello-be, engedélyezze a hozzáférést a logikai alkalmazáshoz, és jelentkezzen be.

1. Adja meg a kiválasztott trigger vagy művelet szükséges adatait, és folytassa a logikai alkalmazás munkafolyamatának összeállítását.

## <a name="connector-reference"></a>Összekötő-referencia

Az eseményindítókkal, műveletekkel és korlátokkal kapcsolatos technikai részletekért lásd az összekötő OpenAPI (korábban: hencegés) leírását, tekintse át az összekötő [hivatkozási oldalát](/connectors/trello/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése