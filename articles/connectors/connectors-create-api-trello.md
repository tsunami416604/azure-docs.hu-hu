---
title: Csatlakozás Trello-hoz az Azure Logic Apps alkalmazásból
description: Az Azure Logic Apps használatával automatizálhatja azokat a feladatokat és munkafolyamatokat, amelyek figyelik és kezelik a Trello-projektek listáit, tábláit és kártyáit
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5c4fcb9b4fea1a4d982b5cf665564599d371b7cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789137"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Trello figyelése és kezelése az Azure Logic-alkalmazásokkal

Az Azure Logic Apps és a Trello-összekötő segítségével automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek figyelik és kezelik a Trello-listákat, kártyákat, táblákat, csapattagokat és így tovább, például:

* Figyelje, hogy mikor ad nak hozzá új kártyákat táblákhoz és listákhoz. 
* Táblák, kártyák és listák létrehozása, leése és kezelése.
* Megjegyzések és tagok hozzáadása a kártyákhoz.
* List táblák, tábla címkék, kártyák táblák, kártya megjegyzéseket, kártya tagjai, csapattagjai, és a csapatok, ahol te vagy a tagja. 
* Szerezz csapatokat.

Használhatja az eseményindítók, hogy kap választ a Trello-fiók, és a kimenet elérhetővé más műveleteket. A Trello-fiókkal feladatokat végrehajtó műveleteket is használhat. Más műveletek is használhatják a Trello-műveletek kimenetét. Ha például új kártyát ad hozzá a táblához vagy a listához, üzeneteket küldhet a Tartalékidő-összekötővel. Ha most kezdi meg a logikai alkalmazások, tekintse át [az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Az Ön Trello-fiókja és felhasználói hitelesítő adatai

  A hitelesítő adatok engedélyezik a logikai alkalmazás számára, hogy kapcsolatot hozzon létre, és hozzáférjen a Trello-fiókhoz.

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol szeretné elérni a Trello-fiókot. A Trello-eseményindítóval való kezdéshez [hozzon létre egy üres logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Trello-művelet használatához indítsa el a logikai alkalmazást egy eseményindítóval, például az **Ismétlődés** eseményindítóval.

## <a name="connect-to-trello"></a>Csatlakozás Trello-hoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és nyissa meg a logikai alkalmazást a Logic App Designerben, ha még nem nyitott.

1. Üres logikai alkalmazások esetén a keresőmezőbe írja be szűrőként a "trello" kifejezést. Az eseményindítók listában válassza ki a kívánt eseményindítót. 

   – vagy –

   Meglévő logikai alkalmazások esetén az utolsó lépésben, ahol műveletet szeretne hozzáadni, válassza az **Új lépés lehetőséget.** 
   A keresőmezőbe írja be szűrőként a "trello" szót. 
   A műveletek listájában jelölje ki a kívánt műveletet.

   Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egérmutatót a lépések közötti nyíl fölé. 
   Válassza ki a**+** megjelenő pluszjelet ( ), majd válassza **a Művelet hozzáadása**lehetőséget.

1. Ha a rendszer kéri, hogy jelentkezzen be a Trello, engedélyezze a hozzáférést a logikai alkalmazás hoz, és jelentkezzen be.

1. Adja meg a kiválasztott eseményindító vagy művelet szükséges adatait, és folytassa a logikai alkalmazás munkafolyamatának kiépítését.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő OpenAPI (korábbi Swagger) leírása által leírt eseményindítók, műveletek és korlátok technikai részleteiért tekintse át az összekötő [referenciaoldalát.](/connectors/trello/)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)