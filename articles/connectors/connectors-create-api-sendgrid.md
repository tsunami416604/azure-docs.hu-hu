---
title: Csatlakozás a Sendgridhez az Azure Logic Apps |} A Microsoft Docs
description: Automatizálhatja a feladatokat és a munkafolyamatok, amelyek e-mailek küldése és a SendGrid levelezőlistáit kezelése az Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: c8747210a77879d551e323a7c0e46a9ab013fa3f
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887189"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>E-mailek küldése és a SendGrid levelezőlistáit kezelése az Azure Logic Apps használatával

Az Azure Logic Apps és a sendgrid szolgáltatás összekötőjével létrehozhat automatikus feladatokkal és munkafolyamatokkal, amely e-mailek küldése és a címzettlisták kezelésére például:

* E-mail küldése.
* A listák adja hozzá a címzetteket.
* GET, hozzáadása és kezelése a globális Mellőzés.

A SendGrid műveleteket használhat a logic Apps ezeket a feladatokat. A kimenet a SendGrid-műveletek használata más műveletek is rendelkezhet. 

Ez az összekötő biztosít csak műveletek, így a logikai alkalmazás indítására használjon egy külön eseményindító, például egy **ismétlődési** eseményindító. Például ha rendszeresen címzettek hozzá a listákat, elküldheti e-mail címzettek és listák az Office 365 Outlook-összekötőt, vagy Outlook.com-összekötő használatával.
Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* A [SendGrid-fiók](https://www.sendgrid.com/) és a egy [SendGrid API-kulcs](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   Az API-kulcsot engedélyezi a logikai alkalmazás, hozzon létre egy kapcsolatot, és a SendGrid-fiók eléréséhez.

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol szeretné a SendGrid-fiók eléréséhez. A SendGrid műveletet használja, indítsa el a logikai alkalmazás egy másik eseményindítóval, például a **ismétlődési** eseményindító.

## <a name="connect-to-sendgrid"></a>Csatlakozás a Sendgridhez

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, ha nem, nyissa meg a már.

1. Válassza ki az elérési utat: 

   * Válassza ki az utolsó lépésnél, ahová a művelet hozzáadása, **új lépés**. 

     – vagy –

   * A lépéseket, ahol szeretné művelet hozzáadása, között helyezze az egérmutatót a nyíl lépések között. 
   Válassza a plusz jelre (**+**), amely akkor jelenik meg, és válassza ki **művelet hozzáadása**.

1. A Keresés mezőbe írja be szűrőként "sendgrid". Műveletek listája alatt válassza ki a kívánt művelet.

1. Adja meg a kapcsolat nevét. 

1. Adja meg a SendGrid API-kulcsát, és válassza a **létrehozás**.

1. Adja meg a szükséges adatokat a kiválasztott művelet, és továbbra is használhatja a logic app-munkafolyamatot.

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részletek korlátok, eseményindítók és műveletek, amely ismerteti az összekötő OpenAPI által (korábbi nevén Swagger) leírását, tekintse át az összekötő [referencialapja](/connectors/sendgrid/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)