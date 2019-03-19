---
title: Az SAP-rendszerek – Azure Logic Apps csatlakoztatása |} A Microsoft Docs
description: Elérése és SAP-erőforrások kezelése az Azure Logic Apps a munkafolyamatok automatizálásával
author: ecfan
ms.author: estfan
ms.date: 05/31/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, divswa, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: d677c0eae9c92f90783ed4ebd95a528b34c872ec
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58170836"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Csatlakozás az Azure Logic Apps a SAP-rendszerek

> [!NOTE]
> Az SAP-összekötő az avuláshoz van ütemezve. Használjon, vagy át a [újabb és speciális SAP-összekötő](./logic-apps-using-sap-connector.md). 

Ez a cikk azt ismerteti, hogyan férhet hozzá a logikai alkalmazás az SAP-erőforrások az SAP-alkalmazáskiszolgáló és az SAP Üzenetkiszolgáló összekötők használatával. Ezzel a módszerrel automatizálhatja feladatok, folyamatok és munkafolyamatok, amelyek a logikai alkalmazások létrehozásával az SAP-adatok és erőforrások kezelése.

Ebben a példában egy HTTP-kérelem használ egy logikai alkalmazást, amely is indíthat. A logikai alkalmazás küld egy köztes dokumentumot (az idoc-hoz) SAP-kiszolgálóhoz, és választ küld a kérelmezőnek, amelyek a logikai alkalmazás neve.
Az aktuális SAP-összekötők rendelkezik műveleteket, de nem eseményindítók, így ez a példa a [HTTP-kérés eseményindító](../connectors/connectors-native-reqres.md) a logikai alkalmazás munkafolyamatának első lépéseként. SAP összekötő-specifikus technikai tudnivalókért tanulmányozza a következő hivatkozás cikkeket: 

* <a href="https://docs.microsoft.com/connectors/sapapplicationserver/" target="blank">SAP alkalmazáskiszolgáló-összekötő</a>
* <a href="https://docs.microsoft.com/connectors/sapmessageserver/" target="blank">SAP Üzenetkiszolgáló összekötő</a>

Ha nem rendelkezik Azure-előfizetésem, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókkal</a>.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk követni, ezek az elemek szükségesek:

* A logikai alkalmazás, ahonnan csak szeretné elérni az SAP-rendszerhez, és a egy eseményindítót, amely elindítja a logikai alkalmazás munkafolyamat. Az SAP-összekötők jelenleg csak a műveleteket meg. Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és [a rövid útmutató: Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* A <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">SAP-alkalmazáskiszolgáló</a> vagy <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">SAP Üzenetkiszolgáló</a>

* Töltse le és telepítse a legújabb [a helyszíni adatátjáró](https://www.microsoft.com/download/details.aspx?id=53127) bármely a helyi számítógépen. Győződjön meg arról, hogy az átjáró beállításához az Azure Portalon a folytatás előtt. Az átjáró segítségével biztonságosan érheti el adatait, és a helyszíni erőforrások vannak. További információkért lásd: [telepítése a helyszíni adatátjáró Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

* Töltse le és telepítse a legújabb SAP ügyféloldali kódtár, amely jelenleg <a href="https://softwaredownloads.sap.com/file/0020000000086282018" target="_blank">(Ice) 3.0.20.0 SAP-összekötő a Microsoft .NET-keretrendszer 4.0-s és a Windows 64 bites (x64)</a>, mint a helyszíni adatátjáró ugyanazon a számítógépen. Ezt a verziót telepíteni vagy újabb ezen okok miatt:

  * SAP Ice korábbi verzióival is válnak holtponti, ha egynél több IDoc-üzeneteket küld egy időben. 
  Ez a feltétel blokkolja az üzenetek időtúllépést okoz, a SAP célhelyre küldött összes újabb üzenetet.

  * A helyszíni átjáró csak 64 bites rendszeren fut. 
  Ellenkező esetben egy "hibás" hiba beolvasása, mivel a data gateway szolgáltatás nem támogatja a 32 bites szerelvényeket.

  * A data gateway szolgáltatás és a Microsoft az SAP-Adapter is használhatja a .NET-keretrendszer 4.5. A .NET keretrendszer 4.0-s SAP Ice együttműködik a .NET-modul 4.0-s, 4.7.1 használó folyamatokat. 
  Az SAP Ice a .NET-keretrendszer 2.0 együttműködik az olyan folyamatokat, amelyek a .NET-futtatórendszer 2.0-s, 3.5-ös verzióját használja, és többé már nem a legújabb a helyszíni adatátjáróval.

* Üzenet tartalma is elküldheti az SAP-kiszolgálóhoz, például egy mintafájlt az idoc-hoz. Ez a tartalom XML formátumú legyen, és tartalmazza a névteret, az SAP-művelethez használni kívánt.

<a name="add-trigger"></a>

## <a name="add-http-request-trigger"></a>HTTP-kérés eseményindító hozzáadása

Az Azure Logic Appsben, mindegyik logikai alkalmazásnak kell kezdődnie, egy [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely akkor aktiválódik, ha egy adott esemény történik, vagy ha egy adott feltétel teljesül. Minden alkalommal, amikor akkor aktiválódik, a Logic Apps-motor létrehoz egy logikaialkalmazás-példányt, és megkezdi az alkalmazás munkafolyamatában.

Ebben a példában egy logikai alkalmazást fog létrehozni az Azure-ban a végpont az, hogy küldhet *HTTP POST-kérések* a logikai alkalmazáshoz. Amikor a logikai alkalmazás a HTTP-kéréseket fogad, a akkor aktiválódik, majd futtatja a következő lépés a munkafolyamatban.

1. Az Azure Portalon hozzon létre egy üres logikai alkalmazást, amely megnyílik a Logikaialkalmazás-Tervező. 

2. A Keresés mezőbe írja be a "http-kérelem" szűrőként. Az eseményindítók listában jelölje ki az eseményindító: **Kérelem - HTTP-kérés fogadásakor.**

   ![HTTP-kérelem típusú trigger hozzáadása](./media/logic-apps-using-sap-connector-old/add-trigger.png)

3. Így létrehozhat egy végponti URL-cím a logikai alkalmazás most már a logikai alkalmazás mentése.
A tervező eszköztárán válassza a **Mentés** parancsot. 

   A végpont URL-cím mostantól megjelenik az eseményindító, például:

   ![Végpont URL-címet generálni](./media/logic-apps-using-sap-connector-old/generate-http-endpoint-url.png)

<a name="add-action"></a>

## <a name="add-sap-action"></a>Az SAP-művelet hozzáadása

Az Azure Logic Apps- [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) a munkafolyamat egy eseményindító vagy egy másik műveletet a következő lépés. Ha még nem adott hozzá egy eseményindítót a logikai alkalmazáshoz még, és szeretné követni az ebben a példában [adja hozzá az ebben a szakaszban leírt eseményindító](#add-trigger).

1. A Logic App Designerben az eseményindító területén válassza a **új lépés** > **művelet hozzáadása**.

   ![Művelet hozzáadása](./media/logic-apps-using-sap-connector-old/add-action.png) 

2. A Keresés mezőbe írja be a "sap-kiszolgáló" szűrőként. A műveletek listából válassza ki a műveletet, az SAP-kiszolgáló: 

   * **SAP alkalmazáskiszolgáló - SAP küldése**
   * **SAP Üzenetkiszolgáló – SAP küldése**

   Ebben a példában ez a művelet: **SAP alkalmazáskiszolgáló - SAP küldése**

   ![Válassza ki a "SAP-alkalmazáskiszolgáló" vagy "SAP Üzenetkiszolgáló"](media/logic-apps-using-sap-connector-old/select-sap-action.png)

3. Ha a kapcsolat adatait kéri, hozzon létre most az SAP-kapcsolat. Ellenkező esetben, ha a kapcsolat már létezik, folytassa a következő lépés tehát beállíthatja az SAP-műveletet. 

   **Helyszíni SAP-kapcsolat létrehozása**

   1. A **átjárók**válassza **kapcsolódás helyszíni adatátjárón keresztül** úgy, hogy a helyi kapcsolat tulajdonságai jelennek meg.

   2. Adja meg a kapcsolati adatokat, az SAP-kiszolgáló. 
   Az a **átjáró** tulajdonság, válassza ki a létrehozott az Azure Portalon a kapcsolódásiátjáró-telepítés, például átjáró:

      **SAP alkalmazáskiszolgáló**

      ![SAP alkalmazás kiszolgálói kapcsolat létrehozása](./media/logic-apps-using-sap-connector-old/create-SAP-app-server-connection.png)  

      **SAP Üzenetkiszolgáló**

      ![SAP üzenet kiszolgálói kapcsolat létrehozása](media/logic-apps-using-sap-connector-old/create-SAP-message-server-connection.png) 

   2. Ha elkészült, kattintson a **Létrehozás** gombra.

      A Logic Apps állít be, és gondoskodik róla, hogy a kapcsolat megfelelően működik, a kapcsolat tesztelése.

4. Most már található, és válasszon ki egy műveletet az SAP-kiszolgáló. 

   1. Az a **SAP művelet** válassza ki a mappa ikont. 
   A mappa listában keresse meg és válassza ki a használni kívánt műveletet. 

      Ebben a példában kiválasztja a **IDOC** kategória az IDoc-művelet. 

      ![Keresse meg és válassza a művelet az idoc-hoz](./media/logic-apps-using-sap-connector-old/SAP-app-server-find-action.png)

      Ha nem találja a kívánt művelet, manuálisan adhatja meg egy elérési utat, például:

      ![Manuálisan adja meg az idoc-hoz a művelet elérési útja](./media/logic-apps-using-sap-connector-old/SAP-app-server-manually-enter-action.png)

      IDoc-műveletekkel kapcsolatos további információkért lásd: [üzenet sémák IDOC-műveletek](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

   2. Kattintson a **bemeneti üzenet** mezőre, hogy a dinamikus tartalmak listája jelenik meg. 
   Annak, hogy listájáról **amikor egy HTTP-kérés érkezik**, jelölje be a **törzs** mező. 

      Ebben a lépésben a HTTP-kérés eseményindító a törzs tartalma, és elküldi, amelyek kimenete az SAP-kiszolgálóhoz.

      !["Törzs" mező kiválasztása](./media/logic-apps-using-sap-connector-old/SAP-app-server-action-select-body.png)

      Ha elkészült, az SAP-művelet alábbi példára hasonlít:

      ![Teljes SAP-művelet](./media/logic-apps-using-sap-connector-old/SAP-app-server-complete-action.png)

6. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot.

<a name="add-response"></a>

## <a name="add-http-response-action"></a>HTTP-válasz művelet hozzáadása

Adja meg a logikai alkalmazás munkafolyamat egy válaszművelet és közé tartozik az SAP-művelet kimenete. Ily módon a logikai alkalmazás adja vissza az eredményeket az SAP-kiszolgáló az eredeti kérelmezőnek. 

1. A Logic App Designerben az SAP művelet alatt válassza ki a **új lépés** > **művelet hozzáadása**.

2. A Keresés mezőbe írja be a "response" szűrőként. A műveletek listából válassza a következő műveletet: **Kérés - válasz**

3. Kattintson a **törzs** mezőre, hogy a dinamikus tartalmak listája jelenik meg. A listából a **küldeni az SAP**, jelölje be a **törzs** mező. 

   ![Teljes SAP-művelet](./media/logic-apps-using-sap-connector-old/select-sap-body-for-response-action.png)

4. Mentse a logikai alkalmazást. 

## <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. Ha a logikai alkalmazás még nincs engedélyezve, a logikai alkalmazás menüjében válassza a **áttekintése**. Az eszköztáron válassza **engedélyezése**. 

2. Logikaialkalmazás-Tervező eszköztárán válassza a **futtatása**. Ezzel a lépéssel manuálisan indíthatja a logikai alkalmazást.

3. HTTP POST-kérelmet küld a HTTP-kérés eseményindító URL-cím a logikai alkalmazás elindításához, és a tartalmat a kérés a üzenet hozzáadása. A kérelem küldése, használhatja a eszközt például [Postman](https://www.getpostman.com/apps). 

   Ebben a cikkben a kérést küld egy IDoc fájlt, amely XML formátumban kell és tartalmazza a névteret használ, például az SAP-művelet: 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//620/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. Miután elküldte a HTTP-kérés, várja meg a logikai alkalmazás válasza.

> [!NOTE]
> A logikai alkalmazás időtúllépés előfordulhat, ha a válasz szükséges összes lépést nem fejeződnek be belül a [kérelem időkorlátja](./logic-apps-limits-and-config.md). Ez az állapot akkor fordul elő, ha a kérések egyébként blokkolná. Könnyebben diagnosztizálhatja a problémákat, megtudhatja, hogyan zajlik [ellenőrzése és a logic apps figyelése](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Gratulálunk, sikeresen létrehozott egy logikai alkalmazást, amely képes kommunikálni az SAP-kiszolgálóval. Most, hogy beállította a logikai alkalmazás egy SAP-kapcsolatot, áttekintheti az egyéb elérhető SAP műveletek, például BAPI és RFC.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő leírtak szerint az összekötők Swagger-fájlok, technikai részleteit a következő hivatkozás cikkek szolgálnak: 

* [SAP alkalmazáskiszolgáló](/connectors/sapapplicationserver/)
* [SAP Üzenetkiszolgáló](/connectors/sapmessageserver/)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* [Csatlakozhat a helyszíni rendszerek](../logic-apps/logic-apps-gateway-connection.md) logikai alkalmazásokból
* Ismerje meg, hogyan érvényesítéséhez, alakítson át, és a többi üzenet műveletek a a [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)
