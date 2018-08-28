---
title: Csatlakozás a Twilióhoz az Azure Logic Apps |} A Microsoft Docs
description: Automatizálhatja a feladatokat és a munkafolyamatok, amelyek használatával a Twilio-fiók globális SMS, MMS és IP-üzenetek kezelése az Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: db7677042737ea1377af54cc02ee1c82c05435c8
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047574"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Az Azure Logic Apps Twilio üzenetek kezelése

Az Azure Logic Apps és a Twilio-összekötőt hozhat létre automatikus feladatokkal és munkafolyamatokkal, beolvasása, küldéséhez és üzenetek a Twilio, többek között globális IP, SMS és MMS-üzenetek listázása. Ezeket a műveleteket használhatja a Twilio-fiók a feladatok végrehajtásához. A kimenet a Twilio-műveletek használata más műveletek is rendelkezhet. Például ha új üzenet érkezik, küldhet az üzenetet a Slack-összekötővel tartalom. Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* A [Twilio](https://www.twilio.com/): 

  * A Twilio-Fiókazonosító és [hitelesítési jogkivonat](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them), amely a Twilio-irányítópulton

    A hitelesítő adatok engedélyezik a logikai alkalmazás, hozzon létre egy kapcsolatot, és a logikai alkalmazás a Twilio-fiók eléréséhez. 
    Próbaverziós Twilio-fiók használata, küldhet SMS csak *ellenőrzött* ügyfélszolgálatának telefonszámai.

  * Egy ellenőrzött Twilio telefonszám, amely lehet SMS küldése

  * Egy ellenőrzött Twilio telefonszám, amely fogadhatnak SMS

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazást, amelyre a Twilio-fiók eléréséhez. A Twilio műveletet használja, indítsa el a logikai alkalmazás egy másik eseményindítóval, például a **ismétlődési** eseményindító.

## <a name="connect-to-twilio"></a>Csatlakozás a Twilióhoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, ha nem, nyissa meg a már.

1. Válassza ki az elérési utat: 

     * Válassza ki az utolsó lépésnél, ahová a művelet hozzáadása, **új lépés**. 

       – vagy –

     * A lépéseket, ahol szeretné művelet hozzáadása, között helyezze az egérmutatót a nyíl lépések között. 
     Válassza a plusz jelre (**+**), amely akkor jelenik meg, és válassza ki **művelet hozzáadása**.
     
       A Keresés mezőbe írja be szűrőként "twilio". 
       Műveletek listája alatt válassza ki a kívánt művelet.

1. Adja meg a szükséges adatokat a kapcsolatot, és válassza a **létrehozás**:

   * A kapcsolat nevét
   * A Twilio-Fiókazonosító 
   * A Twilio-jogkivonatot (hitelesítés)

1. Adja meg a szükséges adatokat a kiválasztott művelet, és továbbra is használhatja a logic app-munkafolyamatot.

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részletek korlátok, eseményindítók és műveletek, amely ismerteti az összekötő OpenAPI által (korábbi nevén Swagger) leírását, tekintse át az összekötő [referencialapja](/connectors/twilio/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)