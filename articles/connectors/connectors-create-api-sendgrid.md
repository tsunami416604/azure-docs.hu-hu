---
title: Csatlakozás a SendGridhez az Azure Logic Apps alkalmazásból
description: Az Azure Logic Apps használatával automatizálhatja az e-maileket küldő feladatokat és munkafolyamatokat, és kezelheti a levelezési listákat a SendGridben
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 998020c5e39c8d50e8a14c74c43b7b435752f43d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789307"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>E-mailek küldése és levelezési listák kezelése a SendGridben az Azure Logic Apps használatával

Az Azure Logic Apps és a SendGrid-összekötő segítségével automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek e-maileket küldenek és kezelik a címzettlistákat, például:

* Küldjön e-mailt.
* Címzettek hozzáadása a listákhoz.
* A globális elnyomás letiltása, hozzáadása és kezelése.

A SendGrid-műveleteket a logikai alkalmazásokban használhatja a feladatok végrehajtásához. Más műveletek is használhatják a SendGrid-műveletek kimenetét. 

Ez az összekötő csak műveleteket biztosít, így a logikai alkalmazás elindításához használjon egy külön eseményindítót, például egy **ismétlődési eseményindítót.** Ha például rendszeresen vesz fel címzetteket a listákba, az Office 365 Outlook-összekötő vagy Outlook.com-összekötő használatával e-maileket küldhet a címzettekről és a listákról.
Ha most kezdi meg a logikai alkalmazások, tekintse át [az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* [SendGrid-fiók](https://www.sendgrid.com/) és [SendGrid API-kulcs](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   Az API-kulcs engedélyezi a logikai alkalmazást, hogy hozzon létre egy kapcsolatot, és hozzáférjen a SendGrid-fiókhoz.

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol szeretné elérni a SendGrid-fiókot. SendGrid-művelet használatához indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **Ismétlődés** eseményindítóval.

## <a name="connect-to-sendgrid"></a>Csatlakozás a SendGridhez

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és nyissa meg a logikai alkalmazást a Logic App Designerben, ha még nem nyitott.

1. Válasszon elérési utat: 

   * Az utolsó lépésben, ahol műveletet szeretne hozzáadni, válassza az **Új lépés lehetőséget.** 

     – vagy –

   * A lépések között, ahol műveletet szeretne hozzáadni, vigye az egérmutatót a lépések közötti nyíl fölé. 
   Válassza ki a**+** megjelenő pluszjelet ( ), majd válassza **a Művelet hozzáadása**lehetőséget.

1. A keresőmezőbe írja be szűrőként a "sendgrid" kifejezést. A műveletek listájában jelölje ki a kívánt műveletet.

1. Adja meg a kapcsolat nevét. 

1. Írja be a SendGrid API-kulcsot, és válassza a **Létrehozás gombot.**

1. Adja meg a kiválasztott művelethez szükséges részleteket, és folytassa a logikai alkalmazás munkafolyamatának kiépítését.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő OpenAPI (korábbi Swagger) leírása által leírt eseményindítók, műveletek és korlátok technikai részleteiért tekintse át az összekötő [referenciaoldalát.](/connectors/sendgrid/)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)