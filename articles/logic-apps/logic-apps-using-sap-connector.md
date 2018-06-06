---
title: Csatlakozás SAP rendszerekhez - Azure Logic Apps |} Microsoft Docs
description: Elérése és az Azure Logic Apps munkafolyamatok automatizálásával SAP-erőforrások kezelése
author: ecfan
manager: cfowler
ms.author: estfan
ms.date: 05/31/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, divswa, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 658fb40f618b61fcd973a626e4e00afc3ad8151a
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34756711"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Csatlakozás az Azure Logic Apps SAP rendszerekhez

Ez a cikk bemutatja, hogyan végezheti el a logikai alkalmazás belül az SAP-erőforrások az SAP-kiszolgáló és az SAP üzenet Server összekötők használatával. Ily módon automatizálhatja feladatok, folyamatok és munkafolyamatok, amelyek az SAP-adatok és erőforrások által készített logic Apps alkalmazásokat kezeléséhez.

Ebben a példában a logikai alkalmazás, amely indíthat el egy HTTP-kérelem használ. A logikai alkalmazást egy köztes dokumentumot (az idoc-hoz) küld egy SAP-kiszolgálónak, és választ küld a kérelmező, amelyek a logikai alkalmazás neve.
Az aktuális SAP-összekötők rendelkezik műveleteket, de nem eseményindítók, ezért a példa a [HTTP-kérelem eseményindító](../connectors/connectors-native-reqres.md) a logic app munkafolyamat első lépéseként. SAP connector-specifikus műszaki információkért tanulmányozza a referencia: 

* <a href="https://docs.microsoft.com/connectors/sapapplicationserver/" target="blank">SAP Application Server-összekötő</a>
* <a href="https://docs.microsoft.com/connectors/sapmessageserver/" target="blank">SAP üzenet Server-összekötő</a>

Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiók</a>.

## <a name="prerequisites"></a>Előfeltételek

Kövesse a cikk együtt, ezek az elemek szükségesek:

* A logikai alkalmazást, ahonnan szeretné az SAP rendszer és a Logic Apps alkalmazást munkafolyamat elinduló eseményindító eléréséhez. Az SAP-összekötők jelenleg csak a műveleteket adja meg. Ha most ismerkedik a logic apps, tekintse át a [Mi az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és [gyors üzembe helyezés: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* A <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">SAP alkalmazáskiszolgáló</a> vagy <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">SAP üzenet kiszolgáló</a>

* Töltse le és telepítse a legújabb [helyszíni adatátjáró](https://www.microsoft.com/download/details.aspx?id=53127) a helyi számítógépen. Győződjön meg arról, hogy az átjáró beállítását az Azure-portálon a folytatás előtt. Az átjáró segítségével biztonságos hozzáférés adatokat és erőforrásokat a helyszínen. További információkért lásd: [telepítése a helyszíni adatátjáró az Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

* Töltse le és telepítse a legújabb SAP ügyféloldali kódtár, amely jelenleg <a href="https://softwaredownloads.sap.com/file/0020000000086282018" target="_blank">SAP-összekötő (Ice) 3.0.20.0 Microsoft .NET-keretrendszer 4.0-s és a Windows 64 bites (x64)</a>, mint a helyszíni adatok átjáró ugyanazon a számítógépen. Ezt a verziót telepíteni vagy újabb ezen okok miatt:

  * Korábbi SAP Ice képes lesz-e holtpontba egy időben elküldött egynél több IDoc-üzenetek. 
  Ez az állapot blokkol minden későbbi, a SAP célra, az üzenetek időtúllépést okozó küldött állapotüzenetek.

  * Az a helyszíni átjáró csak 64 bites rendszeren fut. 
  Ellenkező esetben egy "hibás" hiba beolvasni, mert az adatkezelési átjáró gazdaszolgáltatást nem támogatja a 32-bites szerelvényekhez.

  * Az adatkezelési átjáró gazdaszolgáltatást és a Microsoft SAP-adaptert is használja a .NET-keretrendszer 4.5. Az SAP Ice a .NET keretrendszer 4.0-s .NET-futtatókörnyezet 4.0 a 4.7.1 használó folyamatok működik. 
  Az SAP Ice a .NET-keretrendszer 2.0 .NET-futtatókörnyezet 2.0-3.5 használó folyamatokkal működik, és a legújabb helyszíni data gateway már nem működik.

* Az SAP-kiszolgálóhoz, például az IDoc mintafájl küldhet az üzenet tartalmát. A tartalom XML-formátumú legyen, és tartalmazza a névteret a használni kívánt SAP művelet.

<a name="add-trigger"></a>

## <a name="add-http-request-trigger"></a>Adja hozzá a HTTP-kérelem eseményindító

Az Azure Logic Apps, minden logikai alkalmazást be kell kezdődnie egy [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely akkor indul, amikor egy adott esemény történik, vagy ha egy adott feltétel teljesül. Minden alkalommal, amikor az eseményindító következik be, a Logic Apps motor hoz létre egy logic app-példány, az alkalmazás a munkafolyamat futásának indításakor.

Ebben a példában, hozza létre a logikai alkalmazás, amelynek van végpontja az Azure-ban is elküldheti *HTTP POST kérelmek* a logikai alkalmazáshoz. Amikor a Logic Apps alkalmazást kap a HTTP-kérelmekre, az eseményindító következik be, és a következő lépésben futtatja a munkafolyamatban.

1. Az Azure-portálon hozzon létre egy üres logikai alkalmazás, amely megnyitja a Logic App Tervező. 

2. A keresési mezőbe írja be a "http-kérelem" szűrőként. Az eseményindítók listájából válassza ki az eseményindító: **kérelem - amikor egy HTTP-kérelem érkezett**

   ![Adja hozzá a HTTP-kérelem eseményindító](./media/logic-apps-using-sap-connector/add-trigger.png)

3. A Logic Apps alkalmazást most mentse, létrehozhat egy végponti URL-cím a logikai alkalmazásnak.
A tervező eszköztárán válassza a **Mentés** parancsot. 

   A végpont URL-cím jelenik meg az eseményindító, például:

   ![A végpont URL-cím létrehozása](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

## <a name="add-sap-action"></a>SAP-művelet hozzáadása

Az Azure Logic Apps egy [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) a munkafolyamatban, amely egy eseményindító vagy egy másik művelet a következő lépés. Ha még nem adott hozzá egy eseményindító a logikai alkalmazáshoz sem, és kövesse az ebben a példában [vegye fel a jelen szakaszban ismertetett eseményindító](#add-trigger).

1. Válassza a Logic App az eseményindító Designer **új lépés** > **művelet hozzáadása**.

   ![Művelet hozzáadása](./media/logic-apps-using-sap-connector/add-action.png) 

2. A keresési mezőbe írja be a "sap server" szűrőként. A műveletek listáról válassza ki a művelet az SAP-kiszolgáló: 

   * **SAP alkalmazáskiszolgáló - SAP küldés**
   * **SAP üzenet kiszolgáló - SAP küldés**

   A példában ez a művelet: **SAP alkalmazáskiszolgáló - SAP küldés**

   ![Válassza ki a "SAP alkalmazáskiszolgáló" vagy "SAP üzenet kiszolgáló"](media/logic-apps-using-sap-connector/select-sap-action.png)

3. Ha a számítógép a kapcsolódási adatait, most már a SAP kapcsolat létrehozásához. Ellenkező esetben ha a kapcsolat már létezik, folytassa a következő lépés, amelyen beállíthatja az SAP-műveletet. 

   **A helyszíni SAP-kapcsolat létrehozása**

   1. A **átjárók**, jelölje be **keresztül, a helyszíni adatátjáró** , hogy a helyi kapcsolat tulajdonságai jelennek meg.

   2. A Kapcsolatadatok megadása az SAP-kiszolgáló. 
   Az a **átjáró** tulajdonság, válassza ki az adatátjáró létrehozott Azure-portálon az átjáró telepítésének, például:

      **SAP-alkalmazáskiszolgáló**

      ![SAP alkalmazás kiszolgálói kapcsolat létrehozása](./media/logic-apps-using-sap-connector/create-SAP-app-server-connection.png)  

      **SAP üzenet kiszolgáló**

      ![SAP üzenet kiszolgálói kapcsolat létrehozása](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png) 

   2. Ha elkészült, kattintson a **Létrehozás** gombra.

      A Logic Apps állít be, és ellenőrzi a hálózati kapcsolatot, annak biztosítása, hogy a kapcsolat megfelelően működik-e.

4. Most már található, és válasszon ki egy műveletet az SAP-kiszolgálóról. 

   1. Az a **SAP művelet** válassza ki azt a mappát ikonra. 
   A mappa listában keresse meg és válassza ki a használni kívánt műveletet. 

      Ez a példa kiválasztja a **IDOC** kategória az IDoc-művelet. 

      ![Keresse meg és jelölje ki az idoc-hoz műveletet](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Ha nem találja a kívánt műveletre, manuális egy elérési útját is megadhatja, például:

      ![Az idoc-hoz művelet elérési manuális megadása](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      IDoc-műveletekkel kapcsolatos további információkért lásd: [üzenet-sémák IDOC-műveletek](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

   2. Kattintson a **bemeneti üzenet** jelölőnégyzetet, hogy a dinamikus tartalom listába jelenik meg. 
   Abban a párbeszédpanel **érkezik, amikor egy HTTP-kérelem**, jelölje be a **törzs** mező. 

      Ez a lépés a HTTP-kérelem eseményindító a szervezet rendelkezik, és küld, amelyek kimenete az SAP-kiszolgálóhoz.

      ![Válassza ki a "Törzs" mezőt](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Amikor elkészült, a SAP művelet néz ki ebben a példában:

      ![Teljes SAP művelet](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

6. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot.

<a name="add-response"></a>

## <a name="add-http-response-action"></a>Adja hozzá a HTTP-válasz művelet

Most egy válasz művelet hozzáadása a logikai alkalmazás munkafolyamat, és az SAP művelet eredményének tartalmazza. Ily módon a Logic Apps alkalmazást ad vissza az eredményeket az SAP-kiszolgáló az eredeti kérelmezőnek. 

1. Válassza a Logic App SAP művelet Designer **új lépés** > **művelet hozzáadása**.

2. A keresési mezőbe írja be a "válasz" szűrőként. Válassza ezt a műveletet a műveletek listájának: **kérelem - válasz**

3. Kattintson a **törzs** jelölőnégyzetet, hogy a dinamikus tartalom listába jelenik meg. A listáról a **küldeni a SAP**, jelölje be a **törzs** mező. 

   ![Teljes SAP művelet](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

4. Mentse a logikai alkalmazást. 

## <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. Ha a logikai alkalmazás nincs engedélyezve, a logic app menüben válasszon **áttekintése**. Az eszköztáron válassza **engedélyezése**. 

2. A Logic App Designer eszköztáron válassza **futtatása**. Ebben a lépésben kézzel indítja el a Logic Apps alkalmazást.

3. A Logic Apps alkalmazást indítás HTTP POST-kérelmet küld a HTTP-kérelem indítási URL-cím, és a tartalom a kérelemhez üzenet tartalmazza. A kérelem küldéséhez használhatja egy eszközt, mint [Postman](https://www.getpostman.com/apps). 

   Ebben a cikkben a kérést küld egy IDoc fájlt, amely tartalmazza a névteret használ, például SAP művelethez és XML-formátuma: 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//620/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. A HTTP-kérelem elküldése után várja meg a Logic Apps alkalmazást válaszát.

> [!NOTE]
> A Logic Apps alkalmazást esetleg túllépi az időkorlátot, ha a válasz szükséges összes lépést nem fejeződik be a [kérelem időkorlátja](./logic-apps-limits-and-config.md). Ez az állapot akkor fordul elő, ha kérelmek előfordulhat, hogy blokkolnánk a hozzáférését. Problémák diagnosztizálásához segítségével megtudhatja, hogyan zajlik [ellenőrizze és logikai alkalmazások figyelése](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Gratulálunk, a most létrehozott logikai alkalmazás, amely képes kommunikálni az SAP-kiszolgálóval. Most, hogy beállította a Logic Apps alkalmazást az SAP-kapcsolat, ismerje meg a többi elérhető SAP műveletek, például BAPI és RFC.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő által az összekötők Swagger fájlok technikai részleteiért tanulmányozza a referencia: 

* [SAP-alkalmazáskiszolgáló](/connectors/sapapplicationserver/)
* [SAP üzenet kiszolgáló](/connectors/sapmessageserver/)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* [Csatlakozás helyszíni rendszerekhez](../logic-apps/logic-apps-gateway-connection.md) a logic Apps alkalmazásokból
* Ellenőrizze, átalakítására, valamint az egyéb üzenetművelet a [vállalati integrációs csomag](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Tudnivalók más [Logic Apps-összekötők](../connectors/apis-list.md)
