---
title: Csatlakozás az Azure Logic Apps a SMTP |} A Microsoft Docs
description: Automatizálhatja a feladatokat és a munkafolyamatok, amelyek az SMTP (Simple Mail Transfer Protocol) fiókon keresztül e-mail küldése az Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 78b1eb6272fa97ef392e97723454d29cf56bb4bf
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230505"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>E-mail küldése az SMTP-fiókból az Azure Logic Apps

Az Azure Logic Apps és a Simple Mail Transfer Protocol (SMTP)-összekötő hozhat létre automatizált feladatokat és a munkafolyamatok, amelyek e-mailek küldése az SMTP-fiókból. SMTP-műveletek a kimenetét használják más műveleteket is rendelkezhet. Például az SMTP-e-mailt küld, miután értesítheti a csapatot Slack a Slack-összekötővel. Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* Az SMTP fiók és felhasználói hitelesítő adatok

  A hitelesítő adatok engedélyezik a logikai alkalmazás, hozzon létre egy kapcsolatot, és az SMTP-fiók eléréséhez.

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazást, amelyre az SMTP-fiók eléréséhez. SMTP-művelethez használja, indítsa el a logikai alkalmazás egy eseményindítóval, például a Salesforce eseményindító, ha rendelkezik egy Salesforce-fiókban.

  Például a logikai alkalmazás megkezdheti az **egy rekord létrehozásakor** Salesforce eseményindító. 
  Ez az eseményindító minden alkalommal, amikor egy új rekordot, például egy érdeklődő a Salesforce-ban létrehozott következik be. 
  Kövesse ezt az SMTP-eseményindító **E-mail küldése** művelet. Így ha az új rekord jön létre, a logikai alkalmazás egy e-mailt küld a SMTP-fiókjával kapcsolatban az új rekordban.

## <a name="connect-to-smtp"></a>Csatlakozás SMTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, ha nem, nyissa meg a már.

1. Válassza ki az utolsó lépésnél, ahol szeretne hozzáadni egy SMTP-művelethez, **új lépés**. 

   Lépések közötti művelet hozzáadása, helyezze az egérmutatót a nyíl lépések között. 
   Válassza a plusz jelre (**+**), amely akkor jelenik meg, és válassza ki **művelet hozzáadása**.

1. A Keresés mezőbe írja be szűrőként "smtp". Műveletek listája alatt válassza ki a kívánt művelet.

1. Amikor a rendszer kéri, adja meg a kapcsolati információkat:

   | Tulajdonság | Szükséges | Leírás |
   |----------|----------|-------------|
   | **Kapcsolat neve** | Igen | A kapcsolat az SMTP-kiszolgáló nevét | 
   | **SMTP-kiszolgáló címe** | Igen | Az SMTP-kiszolgáló címe | 
   | **Felhasználónév** | Igen | A felhasználónév az SMTP-fiók | 
   | **Jelszó** | Igen | Az SMTP-fiók jelszavát | 
   | **SMTP-kiszolgáló portját** | Nem | Egy adott portot a használni kívánt SMTP-kiszolgálón | 
   | **SSL engedélyezése?** | Nem | Kapcsolja be, vagy kapcsolja ki az SSL-titkosítást. | 
   |||| 

1. Adja meg a szükséges adatokat a kiválasztott művelet. 

1. A logikai alkalmazás mentéséhez, vagy továbbra is használhatja a logic app-munkafolyamatot.

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részletek korlátok, eseményindítók és műveletek, amely ismerteti az összekötő OpenAPI által (korábbi nevén Swagger) leírását, tekintse át az összekötő [referencialapja](/connectors/smtpconnector/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)