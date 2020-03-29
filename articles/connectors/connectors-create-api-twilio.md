---
title: Csatlakozás a Twilio-hoz az Azure Logic Apps alkalmazásból
description: A globális SMS-, MMS- és IP-üzeneteket kezelő feladatok és munkafolyamatok automatizálása twilio-fiókon keresztül az Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: e5b218efd9c8cfaad99d76d8118d181390a977c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789103"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Üzenetek kezelése a Twilio-ban az Azure Logic Apps alkalmazásokkal

Az Azure Logic Apps és a Twilio-összekötő segítségével automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek lekapják, elküldik és listázik az üzeneteket a Twilio-ban, amelyek globális SMS-, MMS- és IP-üzeneteket tartalmaznak. Ezeket a műveleteket használhatja a Twilio-fiókkal végzett feladatok végrehajtásához. Más műveletek is használhatják a Twilio-műveletek kimenetét. Ha például egy új üzenet érkezik, elküldheti az üzenet tartalmát a Tartalékidő-összekötővel. Ha most kezdi meg a logikai alkalmazások, tekintse át [az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Forrás: [Twilio](https://www.twilio.com/): 

  * A Twilio-fiók azonosítója és [a hitelesítési jogkivonat,](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them)amely megtalálható a Twilio irányítópulton

    A hitelesítő adatok engedélyezik a logikai alkalmazás számára, hogy hozzon létre egy kapcsolatot, és a Logikai alkalmazásból való hozzáférés. 
    Ha Twilio próbafiókot használ, csak *ellenőrzött telefonszámokon* küldhet SMS-t.

  * Ellenőrzött Twilio-telefonszám, amely képes SMS-t küldeni

  * Ellenőrzött Twilio-telefonszám, amely képes SMS-t fogadni

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol szeretné elérni a Twilio-fiók. Twilio-művelet használatához indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **Ismétlődés** eseményindítóval.

## <a name="connect-to-twilio"></a>Csatlakozás a Twilio-hoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és nyissa meg a logikai alkalmazást a Logic App Designerben, ha még nem nyitott.

1. Válasszon elérési utat: 

     * Az utolsó lépésben, ahol műveletet szeretne hozzáadni, válassza az **Új lépés lehetőséget.** 

       – vagy –

     * A lépések között, ahol műveletet szeretne hozzáadni, vigye az egérmutatót a lépések közötti nyíl fölé. 
     Válassza ki a**+** megjelenő pluszjelet ( ), majd válassza **a Művelet hozzáadása**lehetőséget.
     
       A keresőmezőbe írja be a "twilio" szűrőt. 
       A műveletek listájában jelölje ki a kívánt műveletet.

1. Adja meg a kapcsolathoz szükséges részleteket, majd válassza a **Létrehozás lehetőséget:**

   * A kapcsolathoz használandó név
   * Az Ön Twilio-fiókazonosítója 
   * A Twilio-hozzáférés (hitelesítés) jogkivonat

1. Adja meg a kiválasztott művelethez szükséges részleteket, és folytassa a logikai alkalmazás munkafolyamatának kiépítését.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő OpenAPI (korábbi Swagger) leírása által leírt eseményindítók, műveletek és korlátok technikai részleteiért tekintse át az összekötő [referenciaoldalát.](/connectors/twilio/)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)