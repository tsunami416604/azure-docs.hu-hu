---
title: Csatlakozás RSS-hírcsatornákhoz az Azure Logic Apps alkalmazásból
description: Automatizálhatja az RSS-hírcsatornákat figyelő és kezelő feladatokat és munkafolyamatokat az Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 3f1e092c2ff325cdcbc32c617af316d6fbe6dd74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789341"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>RSS-hírcsatornák kezelése az Azure Logic Apps használatával

Az Azure Logic Apps és az RSS-összekötő segítségével automatizált feladatokat és munkafolyamatokat hozhat létre bármely RSS-hírcsatornához, például:

* Figyelje, hogy mikor teszik közzé az RSS-hírcsatorna-elemeket.
* Az összes RSS-hírcsatorna-elem listázása.

RSS (Rich Site Summary), más néven Really Simple Syndication, egy népszerű formátum a webes szindikálás és a gyakran frissített tartalmak, például blogbejegyzések és hírek címoldalára. Számos tartalomközzétevő biztosít RSS-hírcsatornát, hogy a felhasználók előfizethethessenek az adott tartalomra. 

Használhat egy RSS-eseményindítót, amely válaszokat kap egy RSS-hírcsatornából, és elérhetővé teszi a kimenetet más műveletek számára. A logikai alkalmazásokban egy RSS-művelet segítségével végrehajthat egy feladatot az RSS-hírcsatornával. Ha most kezdi meg a logikai alkalmazások, tekintse át [az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* RsS-hírcsatorna URL-címe

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol rss-hírcsatornát szeretne elérni. Az RSS-eseményindítóval való kezdéshez [hozzon létre egy üres logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md) RSS-művelet használatához indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **Ismétlődés** eseményindítóval.

## <a name="connect-to-an-rss-feed"></a>Csatlakozás RSS-hírcsatornához

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és nyissa meg a logikai alkalmazást a Logic App Designerben, ha még nem nyitott.

1. Válasszon elérési utat: 

   * Üres logikai alkalmazások esetén a keresőmezőbe írja be szűrőként az "rss" szót. Az eseményindítók listában válassza ki a kívánt eseményindítót. 

     – vagy –

   * Meglévő logikai alkalmazások esetén a művelet hozzáadásának lépésalatt válassza az **Új lépés lehetőséget.** A keresőmezőbe írja be szűrőként az „rss” kifejezést. A műveletek listájában jelölje ki a kívánt műveletet.

1. Adja meg a kiválasztott eseményindító vagy művelet szükséges adatait, és folytassa a logikai alkalmazás munkafolyamatának kiépítését.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő OpenAPI (korábbi Swagger) leírása által leírt eseményindítók, műveletek és korlátok technikai részleteiért tekintse át az összekötő [referenciaoldalát.](/connectors/rss/)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)