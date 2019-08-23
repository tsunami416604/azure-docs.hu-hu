---
title: Kapcsolódás SAP-rendszerekhez – Azure Logic Apps | Microsoft Docs
description: SAP-erőforrások elérése és kezelése a munkafolyamatok automatizálásával Azure Logic Apps
author: ecfan
ms.author: estfan
ms.date: 05/31/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, divswa, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 31f6a3fc281b8dc309ddcd237246c870c85ae20b
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971628"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Kapcsolódás SAP-rendszerekhez Azure Logic Apps

> [!NOTE]
> Ez az SAP-összekötő elavultnak van ütemezve. Használja vagy telepítse át az [újabb és a FEJLETTEBB SAP-összekötőt](./logic-apps-using-sap-connector.md). 

Ez a cikk bemutatja, hogyan érheti el az SAP-erőforrásokat a logikai alkalmazásokban az SAP Application Server és az SAP Message Server-összekötők használatával. Így automatizálhatja az SAP-adatok és-erőforrások kezelésére szolgáló feladatokat, folyamatokat és munkafolyamatokat a logikai alkalmazások létrehozásával.

Ez a példa egy logikai alkalmazást használ, amely HTTP-kéréssel aktiválható. A logikai alkalmazás egy közbenső dokumentumot (IDoc) küld egy SAP-kiszolgálónak, és visszaadja a logikai alkalmazásnak nevezett választ a kérelmezőnek.
Az aktuális SAP-összekötők műveletekkel rendelkeznek, de nem triggerek, így a példa a [http-kérelem eseményindítóját](../connectors/connectors-native-reqres.md) használja a logikai alkalmazás munkafolyamatának első lépéseként. Az SAP-összekötőre vonatkozó műszaki információkért tekintse meg a következő hivatkozási cikkeket: 

* <a href="https://docs.microsoft.com/connectors/sap" target="blank">SAP Application Server-összekötő</a>
* <a href="https://docs.microsoft.com/connectors/sap/#send-message-to-sap" target="blank">SAP Message Server-összekötő</a>

Ha még nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure</a>-fiókra.

## <a name="prerequisites"></a>Előfeltételek

Ennek a cikknek a követéséhez a következő elemek szükségesek:

* Az a logikai alkalmazás, amelyről el szeretné érni az SAP-rendszerét, valamint egy triggert, amely elindítja a logikai alkalmazás munkafolyamatát. Az SAP-összekötők jelenleg csak műveleteket biztosítanak. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps](../logic-apps/logic-apps-overview.md) és [a gyors útmutató: Hozza létre az első logikai](../logic-apps/quickstart-create-first-logic-app-workflow.md)alkalmazását.

* Az <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">SAP-alkalmazáskiszolgáló</a> vagy az <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">SAP-üzenetkezelő kiszolgáló</a>

* Töltse le és telepítse a legújabb [helyszíni](https://www.microsoft.com/download/details.aspx?id=53127) adatátjárót a helyszíni számítógépen. A folytatás előtt győződjön meg arról, hogy az átjárót a Azure Portalban állítja be. Az átjáró segítségével biztonságosan férhet hozzá az adatforrásokhoz és az erőforrásokhoz a helyszínen. További információ: a helyszíni [adatátjáró telepítése Azure Logic Appshoz](../logic-apps/logic-apps-gateway-install.md).

* Töltse le és telepítse a legújabb SAP ügyféloldali kódtárat, amely jelenleg az <a href="https://softwaredownloads.sap.com/file/0020000000086282018" target="_blank">SAP Connector (NKH) 3.0.20.0 a Microsoft .NET Framework 4,0 és a Windows 64bit (x64)</a>rendszerhez ugyanazon a számítógépen, amelyen a helyszíni adatátjáró található. Telepítse ezt a verziót vagy később a következő okok miatt:

  * A korábbi SAP NKH-verziók holtpontra válhatnak, ha egyszerre több IDoc-üzenetet továbbítanak. 
  Ez az állapot blokkolja az SAP-célhelyre küldött összes későbbi üzenetet, így az üzenetek időtúllépést okoznak.

  * A helyszíni adatátjáró csak 64 bites rendszereken fut. 
  Ellenkező esetben a "hibás rendszerkép" hibaüzenet jelenik meg, mert az adatátjáró-gazda szolgáltatás nem támogatja a 32 bites szerelvényeket.

  * Az adatátjáró-gazdagép és a Microsoft SAP-adapter egyaránt a .NET-keretrendszer 4,5-es frissítését használja. A .NET-keretrendszer 4,0-es SAP-NKH olyan folyamatokkal működik, amelyek .NET Runtime 4,0-et használnak a 4.7.1. 
  A .NET-keretrendszer 2,0-es verziójának SAP-NKH olyan folyamatokkal működik, amelyek a .NET Runtime 2,0-et használják a 3,5-ra, és már nem működik a legújabb helyszíni adatátjáróval.

* Az SAP-kiszolgálónak küldött üzenet tartalma, például egy minta IDoc-fájl. Ennek a tartalomnak XML formátumúnak kell lennie, és tartalmaznia kell a használni kívánt SAP-művelet névterét.

<a name="add-trigger"></a>

## <a name="add-http-request-trigger"></a>HTTP-kérési trigger hozzáadása

Azure Logic Apps minden logikai alkalmazásnak egy eseményindítóval kell kezdődnie [](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely akkor következik be, amikor egy adott esemény történik, vagy ha egy adott feltétel teljesül. A Logic Apps motor létrehoz egy Logic app-példányt, és elindítja az alkalmazás munkafolyamatát.

Ebben a példában egy Azure-beli végponttal rendelkező logikai alkalmazást hoz létre, így *http post* -kéréseket küldhet a logikai alkalmazásnak. Ha a logikai alkalmazás fogadja ezeket a HTTP-kéréseket, az eseményindító elindít és futtatja a következő lépést a munkafolyamatban.

1. A Azure Portal hozzon létre egy üres logikai alkalmazást, amely megnyitja a Logic app designert. 

2. A keresőmezőbe írja be szűrőként a "http-kérelem" kifejezést. Az eseményindítók listából válassza ki a következő eseményindítót: **Kérelem – HTTP-kérés fogadásakor**

   ![HTTP-kérési trigger hozzáadása](./media/logic-apps-using-sap-connector-old/add-trigger.png)

3. Most mentse a logikai alkalmazást, hogy létrehozzon egy végponti URL-címet a logikai alkalmazáshoz.
A tervező eszköztárán válassza a **Mentés** parancsot. 

   A végpont URL-címe most megjelenik az triggerben, például:

   ![URL-cím előállítása a végponthoz](./media/logic-apps-using-sap-connector-old/generate-http-endpoint-url.png)

<a name="add-action"></a>

## <a name="add-sap-action"></a>SAP-művelet hozzáadása

Azure Logic Apps a [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) egy olyan lépés a munkafolyamatban, amely egy triggert vagy egy másik műveletet követ. Ha még nem adott hozzá triggert a logikai alkalmazáshoz, és szeretné követni ezt a példát, [adja hozzá az ebben a részben ismertetett triggert](#add-trigger).

1. A Logic app Designerben az trigger alatt válassza az **új lépés** > **művelet hozzáadása lehetőséget**.

   ![Művelet hozzáadása](./media/logic-apps-using-sap-connector-old/add-action.png) 

2. A keresőmezőbe írja be szűrőként az "SAP-kiszolgáló" kifejezést. A műveletek listából válassza ki az SAP-kiszolgáló műveletét: 

   * **SAP Application Server – küldés az SAP-be**
   * **SAP Message Server – küldés az SAP-be**

   Ez a példa a következő műveletet használja: **SAP Application Server – küldés az SAP-be**

   ![Az "SAP Application Server" vagy az "SAP Message Server" kiválasztása](media/logic-apps-using-sap-connector-old/select-sap-action.png)

3. Ha a rendszer megkérdezi a kapcsolat részleteit, hozza létre most az SAP-kapcsolatát. Ellenkező esetben, ha a kapcsolat már létezik, folytassa a következő lépéssel, hogy beállítsa az SAP-műveletet. 

   **Helyszíni SAP-kapcsolatok létrehozása**

   1. Átjárók esetében válassza a **Csatlakoztatás** helyszíni adatátjárón keresztül lehetőséget, hogy megjelenjenek a helyszíni kapcsolat tulajdonságai.

   2. Adja meg az SAP-kiszolgáló elérhetőségi adatait. 
   Az **átjáró** tulajdonságnál válassza ki az átjáró telepítéséhez Azure Portalban létrehozott adatátjárót, például:

      **SAP-alkalmazáskiszolgáló**

      ![SAP Application Server-kapcsolatok létrehozása](./media/logic-apps-using-sap-connector-old/create-SAP-app-server-connection.png)  

      **SAP-üzenet kiszolgálója**

      ![SAP-üzenetkezelő kiszolgáló kapcsolatainak létrehozása](media/logic-apps-using-sap-connector-old/create-SAP-message-server-connection.png) 

   2. Ha elkészült, kattintson a **Létrehozás** gombra.

      Logic Apps beállítja és teszteli a-kapcsolatokat, így biztosítva, hogy a kapcsolatok megfelelően működnek.

4. Most keresse meg és válasszon ki egy műveletet az SAP-kiszolgálóról. 

   1. Az **SAP-művelet** mezőben válassza a mappa ikont. 
   A mappalistában keresse meg és válassza ki a használni kívánt műveletet. 

      Ez a példa kiválasztja a **IDOC** kategóriát a IDOC művelethez. 

      ![IDoc művelet keresése és kiválasztása](./media/logic-apps-using-sap-connector-old/SAP-app-server-find-action.png)

      Ha nem találja a kívánt műveletet, manuálisan is megadhat egy elérési utat, például:

      ![Adja meg manuálisan a IDoc művelet elérési útját](./media/logic-apps-using-sap-connector-old/SAP-app-server-manually-enter-action.png)

      További információ a IDoc-műveletekről: [IDoc-műveletek üzenet](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations) -sémái

   2. Kattintson a **beviteli üzenet** szövegmezőbe, hogy megjelenjen a dinamikus tartalmak listája. 
   A listában a HTTP- **kérelem fogadása**alatt kattintson a **törzs** mezőre. 

      Ez a lépés a HTTP-kérelmi trigger törzsének tartalmát tartalmazza, és elküldi a kimenetet az SAP-kiszolgálónak.

      ![Válassza a "törzs" mezőt](./media/logic-apps-using-sap-connector-old/SAP-app-server-action-select-body.png)

      Ha elkészült, az SAP-művelet a következő példához hasonlóan néz ki:

      ![SAP-művelet végrehajtása](./media/logic-apps-using-sap-connector-old/SAP-app-server-complete-action.png)

6. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot.

<a name="add-response"></a>

## <a name="add-http-response-action"></a>HTTP-válasz hozzáadása művelet

Most adjon hozzá egy válasz műveletet a logikai alkalmazás munkafolyamataihoz, és adja meg az SAP-művelet kimenetét. Így a logikai alkalmazás az SAP-kiszolgáló eredményeit visszaadja az eredeti kérelmezőnek. 

1. A Logic app Designer SAP-művelet területén válassza az **új lépés** > **művelet hozzáadása**lehetőséget.

2. A keresőmezőbe írja be a "válasz" kifejezést a szűrőként. A műveletek listából válassza ki ezt a műveletet: **Kérelem – válasz**

3. Kattintson a **törzs** mezőbe, hogy megjelenjen a dinamikus tartalmak listája. A listából válassza a **Küldés az SAP**-be lehetőséget, majd a **törzs** mezőt. 

   ![SAP-művelet végrehajtása](./media/logic-apps-using-sap-connector-old/select-sap-body-for-response-action.png)

4. Mentse a logikai alkalmazást. 

## <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. Ha a logikai alkalmazás még nincs engedélyezve, a logikai alkalmazás menüjében válassza az **Áttekintés**lehetőséget. Az eszköztáron válassza az **Engedélyezés**lehetőséget. 

2. A Logic app Designer eszköztárán válassza a **Futtatás**lehetőséget. Ez a lépés manuálisan elindítja a logikai alkalmazást.

3. Aktiválja a logikai alkalmazást úgy, hogy HTTP POST-kérelmet küld a HTTP-kérelemben szereplő URL-címre, és belefoglalja az üzenet tartalmát a kérelembe. A kérelem elküldéséhez olyan eszközt is használhat, mint például a [Poster](https://www.getpostman.com/apps). 

   Ebben a cikkben a kérelem egy IDoc-fájlt küld, amelynek XML-formátumúnak kell lennie, és tartalmaznia kell az Ön által használt SAP-művelet névterét, például: 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//620/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. A HTTP-kérés elküldése után várjon a logikai alkalmazás válaszára.

> [!NOTE]
> Előfordulhat, hogy a logikai alkalmazás túllépi az időkorlátot, ha a válaszhoz szükséges összes lépés nem fejeződik be a [kérés](./logic-apps-limits-and-config.md)időkorlátján belül. Ha ez az állapot történik, előfordulhat, hogy a kérések le vannak tiltva. A problémák diagnosztizálásához megtudhatja, hogyan [ellenőrizheti és figyelheti a logikai alkalmazásokat](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Gratulálunk, már létrehozott egy logikai alkalmazást, amely képes kommunikálni az SAP-kiszolgálóval. Most, hogy beállított egy SAP-csatlakozást a logikai alkalmazáshoz, megismerheti a többi elérhető SAP-műveletet, például a BAPI és az RFC-t is.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötők a "hencegő fájlok" című részében leírtak alapján kapcsolatos technikai részletekért tekintse meg az alábbi hivatkozásokat: 

* [SAP-alkalmazáskiszolgáló](/connectors/sap)
* [SAP-üzenet kiszolgálója](/connectors/sap/#send-message-to-sap)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* [Kapcsolódás](../logic-apps/logic-apps-gateway-connection.md) helyszíni rendszerekhez a Logic appsből
* Megtudhatja, hogyan érvényesítheti, átalakíthatja és más üzenetkezelési műveleteket a [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
