---
title: Csatlakozás a SharePointhoz az Azure Logic Apps alkalmazásból
description: Az Azure Logic Apps használatával a helyszíni SharePoint Online-ban vagy a SharePoint Serverben erőforrásokat figyelő és kezelő feladatok és munkafolyamatok automatizálása
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: bb82ef2d6fb83c2e1b0fa81aa9504c9bb7d8234b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789256"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>SharePoint-erőforrások figyelése és kezelése az Azure Logic Apps alkalmazásokkal

Az Azure Logic Apps és a SharePoint-összekötő segítségével automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek figyelik és kezelik az erőforrásokat, például fájlokat, mappákat, listákat, elemeket, személyeket és így tovább, például a helyszíni SharePoint Online-ban vagy a SharePoint Serverben:

* Figyelje, hogy mikor hoznak létre, módosítanak vagy törölnek fájlokat vagy elemeket.
* Elemek létrehozása, bekése, frissítése és törlése.
* Mellékletek hozzáadása, bekéselése vagy törlése. A tartalom beszerezése a mellékletekből.
* Fájlok létrehozása, másolása, frissítése és törlése. 
* Fájltulajdonságok frissítése. Fájl tartalmának, metaadatainak vagy tulajdonságainak beszerezése.
* Mappák listázása vagy kibontása.
* Listák vagy listanézetek beszerezhetése.
* Tartalom-jóváhagyási állapot beállítása.
* Személyek et megoldani.
* HTTP-kérések küldése a SharePointba.
* Entitásértékek beszereznie.

Használhatja az olyan eseményindítókat, amelyek válaszokat kapnak a SharePointból, és a kimenetet más műveletek számára is elérhetővé teszik. A logikai alkalmazásokban végrehajtott műveletek segítségével feladatokat hajthat végre a SharePointban. Más műveletek is használhatják a SharePoint-műveletek kimenetét. Ha például rendszeresen letölt fájlokat a SharePointból, a Tartalékidő-összekötő használatával üzeneteket küldhet a csapatnak.
Ha most kezdi meg a logikai alkalmazások, tekintse át [az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* A SharePoint-webhely címe és felhasználói hitelesítő adatai

  A hitelesítő adatok felhatalmazzák a logikai alkalmazást, hogy kapcsolatot hozzon létre, és hozzáférjen a SharePoint-fiókjához. 

* Mielőtt logikai alkalmazásokat csatlakoztathatna a helyszíni rendszerekhez, például a SharePoint Serverhez, telepítenie kell és be kell [állítania egy helyszíni adatátjárót.](../logic-apps/logic-apps-gateway-install.md) Így megadhatja, hogy az átjáró telepítése a logikai alkalmazás SharePoint Server-kapcsolatának létrehozásakor használható.

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez hozzá szeretne férni a SharePoint-fiókjához. Ha SharePoint-eseményindítóval szeretne kezdeni, [hozzon létre egy üres logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md) SharePoint-művelet használatához indítsa el a logikai alkalmazást egy eseményindítóval, például egy Salesforce-eseményindítóval, ha Van Salesforce-fiókja.

  Például elindíthatja a logikai alkalmazást a **Rekord létrehozásakor** a Salesforce eseményindítóval. 
  Ez az eseményindító minden alkalommal aktiválódik, amikor új rekord, például érdeklődő jön létre a Salesforce-ban. 
  Ezt az eseményindítót a SharePoint **Fájllétrehozása** művelettel követheti. Így az új rekord létrehozásakor a logikai alkalmazás létrehoz egy fájlt a SharePointban, amely az új rekordra vonatkozó információkat tartalmazza.

## <a name="connect-to-sharepoint"></a>Kapcsolódás a SharePointhoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és nyissa meg a logikai alkalmazást a Logic App Designerben, ha még nem nyitott.

1. Üres logikai alkalmazások esetén a keresőmezőbe írja be szűrőként a "sharepoint" kifejezést. Az eseményindítók listában válassza ki a kívánt eseményindítót. 

   – vagy –

   Meglévő logikai alkalmazások esetén az utolsó lépésnél, ahol SharePoint-műveletet szeretne hozzáadni, válassza az **Új lépés lehetőséget.** 
   A keresőmezőbe írja be szűrőként a "sharepoint" kifejezést. 
   A műveletek listájában jelölje ki a kívánt műveletet.

   Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egérmutatót a lépések közötti nyíl fölé. 
   Válassza ki a**+** megjelenő pluszjelet ( ), majd válassza **a Művelet hozzáadása**lehetőséget.

1. Amikor a rendszer kéri a bejelentkezést, adja meg a szükséges csatlakozási adatokat. Ha SharePoint Servert használ, győződjön meg arról, hogy a **Csatlakozás a helyszíni adatátjárón keresztül lehetőséget választja.** Ha elkészült, kattintson a **Létrehozás** gombra.

1. Adja meg a kiválasztott eseményindító vagy művelet szükséges adatait, és folytassa a logikai alkalmazás munkafolyamatának kiépítését.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő OpenAPI (korábbi Swagger) leírása által leírt eseményindítók, műveletek és korlátok technikai részleteiért tekintse át az összekötő [referenciaoldalát.](/connectors/sharepoint/)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
