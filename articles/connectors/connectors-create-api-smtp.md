---
title: Csatlakozás SMTP-hez az Azure Logic Apps alkalmazásból
description: Az SMTP-fiókon keresztül e-maileket küldő feladatok és munkafolyamatok automatizálása az Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 60acd128495176cd0a90418c61edf53bdcd88e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77647579"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>E-mail küldése Az SMTP-fiókból az Azure Logic Apps segítségével

Az Azure Logic Apps és a Simple Mail Transfer Protocol (SMTP) összekötővel automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek az SMTP-fiókból küldenek e-mailt. Az SMTP-műveletek kimenetét más műveletek is használhatják. Például miután az SMTP e-mailt küld, értesítheti a slack-összekötővel a Slack-ben lévő csapatát. Ha most kezdi meg a logikai alkalmazások, tekintse át [az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Az Ön SMTP-fiókja és felhasználói hitelesítő adatai

  A hitelesítő adatok engedélyezik a logikai alkalmazás számára, hogy kapcsolatot hozzon létre, és hozzáférjen az SMTP-fiókhoz.

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol szeretné elérni az SMTP-fiók. SMTP-művelet használatához indítsa el a logikai alkalmazást egy eseményindítóval, például egy Salesforce-eseményindítóval, ha Salesforce-fiókkal rendelkezik.

  Például elindíthatja a logikai alkalmazást a **Rekord létrehozásakor** a Salesforce eseményindítóval. 
  Ez az eseményindító minden alkalommal aktiválódik, amikor új rekord, például érdeklődő jön létre a Salesforce-ban. 
  Ezután kövesse ezt az eseményindítót az SMTP **E-mail küldése** művelettel. Így az új rekord létrehozásakor a logikai alkalmazás e-mailt küld az SMTP-fiókból az új rekordról.

## <a name="connect-to-smtp"></a>Csatlakozás az SMTP-hez

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és nyissa meg a logikai alkalmazást a Logic App Designerben, ha még nem nyitott.

1. Az utolsó lépésben, ahol SMTP-műveletet szeretne hozzáadni, válassza az **Új lépés lehetőséget.** 

   Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egérmutatót a lépések közötti nyíl fölé. 
   Válassza ki a**+** megjelenő pluszjelet ( ), majd válassza **a Művelet hozzáadása**lehetőséget.

1. A keresőmezőbe írja be szűrőként az "smtp" szót. A műveletek listájában jelölje ki a kívánt műveletet.

1. Amikor a rendszer kéri, adja meg a kapcsolatadatait:

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Kapcsolat neve** | Igen | Az SMTP-kiszolgálóval való kapcsolat neve | 
   | **SMTP-kiszolgáló címe** | Igen | Az SMTP-kiszolgáló címe | 
   | **Felhasználónév** | Igen | Az SMTP-fiók felhasználóneve | 
   | **Jelszó** | Igen | Az SMTP-fiók jelszava | 
   | **SMTP-kiszolgálóport** | Nem | A használni kívánt SMTP-kiszolgáló egy adott portja | 
   | **Engedélyezi az SSL-t?** | Nem | Az SSL-titkosítás be- és kikapcsolása. | 
   |||| 

1. Adja meg a kiválasztott művelethez szükséges részleteket. 

1. Mentse a logikai alkalmazást, vagy folytassa a logikai alkalmazás munkafolyamatának kiépítését.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötővel kapcsolatos további technikai részleteket, például az eseményindítókat, műveleteket és korlátokat az összekötő Swagger-fájlja szerint lásd az [összekötő referencialapján.](https://docs.microsoft.com/connectors/smtpconnector/)

> [!NOTE]
> [Az integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)lévő logikai alkalmazások esetében az összekötő ISE-címkével ellátott verziója az [ISE-üzenetkorlátokat](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) használja.

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)