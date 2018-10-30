---
title: Csatlakozás a Sharepointhoz, az Azure Logic Apps |} A Microsoft Docs
description: Automatizálhatja a feladatokat és a munkafolyamatok, amelyek figyelése és a SharePoint online-hoz vagy a SharePoint Server, a helyszíni erőforrások kezelése az Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e0ec3149-507a-409d-8e7b-d5fbded006ce
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: f076b2e859d334b407fe5c93fcec1bb67cb75e48
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233259"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>Figyelheti és kezelheti a SharePoint-erőforrásokat az Azure Logic Apps

Az Azure Logic Apps és a SharePoint-összekötőben hozhat létre automatikus feladatokkal és munkafolyamatokkal, figyelheti és kezelheti az erőforrásokat, például a fájlok, mappák, listák, cikkeket, személyek, és így tovább, a SharePoint online-ban vagy a SharePoint Server, a helyszínen, például:

* Fájlok vagy elemek jönnek létre, amikor a figyelő megváltozott, vagy törölték.
* Hozzon létre, beolvasása, frissítése vagy törölhet elemeket.
* Adja hozzá, beolvasása, vagy a melléklet törlése. Lekérheti a tartalmat a mellékleteket.
* Hozzon létre, másolja, frissíteni vagy fájlok törlése. 
* Fájl tulajdonságainak frissítése. Egy fájl a tartalmát, metaadatait vagy tulajdonságok beolvasása.
* Listát, vagy bontsa ki a mappákat.
* Get-listákat és a nézetek.
* Tartalom-jóváhagyási állapot beállítása.
* Oldja meg a személyeket.
* HTTP-kéréseket küldeni a SharePoint.
* Entitásértékek beolvasása.

Eseményindítókat, amelyek választ kaphat a SharePoint és egyéb műveletek számára elérhetővé tenni a kimeneti is használhatja. Műveletek a logic Apps segítségével feladatok végrehajtása a Sharepointban. A SharePoint-művelet kimenetét használják más műveletek is rendelkezhet. Például ha rendszeresen fetch fájlokat a Sharepointból, küldhet üzeneteket csapata a Slack-összekötő használatával.
Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* A SharePoint webhely címét és a felhasználói hitelesítő adatait

  A hitelesítő adatok engedélyezik a logikai alkalmazás, hozzon létre egy kapcsolatot, és a SharePoint-fiókjába. 

* A logic apps csatlakozhat a helyszíni rendszerek, például a SharePoint Server, mielőtt kell [telepítése és beállítása egy helyszíni adatátjárót](../logic-apps/logic-apps-gateway-install.md). Ezzel a módszerrel megadhatja, hogy az átjáró telepítése használja, amikor a SharePoint-kiszolgáló kapcsolatot hoz létre a logikai alkalmazás.

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol szeretné a SharePoint-fiókjába. Egy SharePoint-trigger indít el a [hozzon létre egy üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). Egy SharePoint-műveletet használ, indítsa el a logikai alkalmazás egy eseményindítóval, például a Salesforce eseményindító, ha rendelkezik egy Salesforce-fiókban.

  Például a logikai alkalmazás megkezdheti az **egy rekord létrehozásakor** Salesforce eseményindító. 
  Ez az eseményindító minden alkalommal, amikor egy új rekordot, például egy érdeklődő a Salesforce-ban létrehozott következik be. 
  Kövesse a SharePoint erre az eseményindítóra **fájl létrehozása** művelet. Ily módon, ha az új rekord jön létre, a logikai alkalmazás létrehoz egy fájlt a SharePoint új rekord információkat.

## <a name="connect-to-sharepoint"></a>Kapcsolódás a SharePointhoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, ha nem, nyissa meg a már.

1. Üres logic Apps a keresőmezőbe írja be szűrőként "sharepoint". Eseményindítók listája alatt válassza ki a kívánt az eseményindító. 

   – vagy –

   Meglévő logic Apps alkalmazások, az utolsó lépés hozzáadása egy SharePoint-műveletet, amelyre a válasszon **új lépés**. 
   A Keresés mezőbe írja be a "sharepoint" szűrőként. 
   Műveletek listája alatt válassza ki a kívánt művelet.

   Lépések közötti művelet hozzáadása, helyezze az egérmutatót a nyíl lépések között. 
   Válassza a plusz jelre (**+**), amely akkor jelenik meg, és válassza ki **művelet hozzáadása**.

1. Amikor a bejelentkezéshez kéri, adja meg a szükséges kapcsolati információkat. Ha a SharePoint Servert használ, mindenképpen jelölje ki **kapcsolódás helyszíni adatátjárón keresztül**. Ha elkészült, kattintson a **Létrehozás** gombra.

1. Adja meg a szükséges adatokat a kijelölt eseményindítót vagy műveletet, és továbbra is használhatja a logic app-munkafolyamatot.

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részletek korlátok, eseményindítók és műveletek, amely ismerteti az összekötő OpenAPI által (korábbi nevén Swagger) leírását, tekintse át az összekötő [referencialapja](/connectors/sharepoint/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)
