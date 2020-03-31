---
title: Csatlakozás SAP-rendszerekhez
description: Sap-erőforrások elérése és kezelése az Azure Logic Apps munkafolyamatainak automatizálásával
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/30/2019
tags: connectors
ms.openlocfilehash: 39ab222f64d964e95b16e043c9cdeccd8170ace3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651015"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Csatlakozás SAP-rendszerekhez az Azure Logic Appsből

> [!IMPORTANT]
> A korábbi SAP Application Server és SAP Message Server-összekötők elavultak február 29-én, 2020. Az aktuális SAP-összekötő konszolidálja ezeket a korábbi SAP-összekötőket, így nem kell módosítania a kapcsolat típusát, teljes mértékben kompatibilis a korábbi összekötőkkel, számos további lehetőséget biztosít, és továbbra is használja az SAP .Net összekötő könyvtárat ( SAP NCo).
>
> A régebbi összekötőket használó logikai alkalmazások esetében [telepítse át a legújabb összekötőt](#migrate) az eprecációs dátum előtt. Ellenkező esetben ezek a logikai alkalmazások végrehajtási hibákat tapasztalnak, és nem tudnak üzeneteket küldeni az SAP-rendszerbe.

Ez a cikk bemutatja, hogyan érheti el a helyszíni SAP-erőforrások egy logikai alkalmazás az SAP-összekötő használatával. Az összekötő az SAP klasszikus kiadásaival, például a helyszíni R/3 és ECC rendszerekkel működik. Az összekötő lehetővé teszi az SAP újabb HANA-alapú SAP-rendszerekkel való integrációt is, például az S/4 HANA-t, függetlenül attól, hogy a helyszínen vagy a felhőben vannak-e üzemeltetve. Az SAP-összekötő támogatja az SAP NetWeaver-alapú rendszerekbe és az SAP-ból történő üzenet- vagy adatintegrációt a köztes dokumentumon (IDoc), a BUSINESS Application Programming Interface (BAPI) vagy a Távoli függvényhíváson (RFC) keresztül.

Az SAP-összekötő az [SAP .NET Connector (NCo) függvénytárat](https://support.sap.com/en/product/connectors/msnet.html) használja, és a következő műveleteket biztosítja:

* **Üzenet küldése az SAP-nak:** Küldje el az IDoc-ot a tRFC-n keresztül, hívja meg a BAPI-függvényeket az RFC-n keresztül, vagy hívja az RFC/tRFC-t az SAP-rendszerekben.
* **Amikor üzenet érkezik az SAP-tól:** IDoc fogadása tRFC-n keresztül, hívja meg a BAPI-függvényeket tRFC-n keresztül, vagy hívja meg az RFC/tRFC-t SAP-rendszerekben.
* **Sémák létrehozása:** Sémák létrehozása az IDoc, a BAPI vagy az RFC SAP-összetevőihez.

Ezekhez a műveletekhez az SAP-összekötő támogatja az alapszintű hitelesítést felhasználóneveken és jelszavakon keresztül. A csatlakozó támogatja a [biztonságos hálózati kommunikációt (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true)is. Az SNC használható az SAP NetWeaver egyszeri bejelentkezéshez (SSO) vagy egy külső biztonsági termék által biztosított további biztonsági képességekhez.

Az SAP-összekötő integrálja a helyszíni SAP-rendszerekkel a [helyszíni adatátjárón](../logic-apps/logic-apps-gateway-connection.md)keresztül. A küldési forgatókönyvek, például amikor egy üzenetet küld egy logikai alkalmazásból egy SAP-rendszer, az adatátjáró rfc-ügyfélként működik, és továbbítja a kapott kérelmeket a logikai alkalmazásból az SAP-nak. Hasonlóképpen a fogadási forgatókönyvek, az adatátjáró működik, mint egy RFC-kiszolgáló, amely megkapja az SAP-tól érkező kérelmeket, és továbbítja azokat a logikai alkalmazás.

Ez a cikk bemutatja, hogyan hozhat létre példa logikai alkalmazások, amelyek integrálhatók az SAP-val, miközben a korábban leírt integrációs forgatókönyvek. A régebbi SAP-összekötőket használó logikai alkalmazások esetében ez a cikk bemutatja, hogyan telepítheti át a logikai alkalmazásokat a legújabb SAP-összekötőkbe.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Előfeltételek

A cikk követéséhez az alábbi elemekre van szükség:

* Azure-előfizetés. Ha még nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókot.](https://azure.microsoft.com/free/)

* A logikai alkalmazás, ahonnan szeretné elérni az SAP-rendszer és egy eseményindító, amely elindítja a logikai alkalmazás munkafolyamatát. Ha most kezdi meg a logikai alkalmazások, lásd: [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md) és rövid [útmutató: Az első logikai alkalmazás létrehozása.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az [SAP alkalmazáskiszolgáló](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) vagy [az SAP-üzenetkiszolgáló](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).

* Töltse le és telepítse a legújabb [helyszíni adatátjárót](https://www.microsoft.com/download/details.aspx?id=53127) bármely helyszíni számítógépre. A folytatás előtt állítsa be az átjárót az Azure Portalon. Az átjáró segítségével biztonságosan hozzáférhet a helyszíni adatokhoz és erőforrásokhoz. További információ: [Telepítsen egy helyszíni adatátjárót az Azure Logic Apps alkalmazáshoz.](../logic-apps/logic-apps-gateway-install.md)

* Ha SNC-t használ az SSO-val, győződjön meg arról, hogy az átjáró olyan felhasználóként fut, amely le van képezve az SAP-felhasználóval. Az alapértelmezett fiók módosításához válassza a **Fiók módosítása**lehetőséget, és adja meg a felhasználói hitelesítő adatokat.

  ![Átjárófiók módosítása](./media/logic-apps-using-sap-connector/gateway-account.png)

* Ha engedélyezi az SNC-t egy külső biztonsági termékkel, másolja az SNC-könyvtárat vagy fájlokat ugyanazon a gépen, ahol az átjáró telepítve van. Az SNC-termékek közé tartozik a [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), a Kerberos és az NTLM.

* Töltse le és telepítse a legújabb SAP ügyfélkönyvtárat, amely jelenleg az [SAP Connector (NCo 3.0) a Microsoft .NET 3.0.22.0 számára, amelyet a .NET Framework 4.0 - Windows 64 bites (x64) rendszerrel állítottak össze,](https://softwaredownloads.sap.com/file/0020000001000932019)ugyanarra a számítógépre, mint a helyszíni adatátjáró. Telepítse ezt a verziót vagy újabb verziót a következő okok miatt:

  * A korábbi SAP NCo-verziók holtpontra kerülhetnek, ha egyszerre több IDoc-üzenet érkezik. Ez a feltétel blokkolja az SAP-célba küldött összes későbbi üzenetet, ami az üzenetek időout-át.
  
  * A helyszíni adatátjáró csak 64 bites rendszereken fut. Ellenkező esetben "hibás lemezkép" hibaüzenet jelenik meg, mert az adatátjáró gazdagép-szolgáltatása nem támogatja a 32 bites szerelvényeket.
  
  * Mind az adatátjáró gazdagép szolgáltatása, mind a Microsoft SAP adapter a .NET Framework 4.5-öt használja. A .NET Framework 4.0 SAP NCo-ja a .NET 4.0 és 4.7.1-es futásidejű folyamatokkal működik. Az SAP NCo for .NET Framework 2.0 a .NET 2.0 és 3.5 között futófolyamatokkal működik, de már nem működik a legújabb helyszíni adatátjáróval.

* Az SAP-kiszolgálóra küldhető üzenettartalomnak, például egy iDoc-mintának XML formátumúnak kell lennie, és tartalmaznia kell a használni kívánt SAP-művelet névterét.

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>Áttelepítés az aktuális összekötőre

1. Ha még nem tette meg, frissítse a [helyszíni adatátjárót,](https://www.microsoft.com/download/details.aspx?id=53127) hogy a legújabb verzióval rendelkezhessen. További információ: [Telepítsen egy helyszíni adatátjárót az Azure Logic Apps alkalmazáshoz.](../logic-apps/logic-apps-gateway-install.md)

1. A régebbi SAP-összekötőt használó logikai alkalmazásban törölje a **Küldés az SAP-nak** műveletet.

1. A legújabb SAP-összekötő, adja hozzá az **üzenet küldése az SAP-hoz** művelet. A művelet használata előtt hozza létre újra a kapcsolatot az SAP-rendszerrel.

1. Ha elkészült, mentse a logikai alkalmazást.

<a name="add-trigger"></a>

## <a name="send-message-to-sap"></a>Üzenet küldése az SAP-nak

Ez a példa egy logikai alkalmazást használ, amelyet HTTP-kérelemmel aktiválhat. A logikai alkalmazás egy IDoc-ot küld egy SAP-kiszolgálónak, és választ ad a kérőnek, amely a logikai alkalmazást hívta.

### <a name="add-an-http-request-trigger"></a>HTTP-kérelem eseményindítójának hozzáadása

Az Azure Logic Apps minden logikai alkalmazás kell kezdeni egy [eseményindító,](../logic-apps/logic-apps-overview.md#logic-app-concepts)amely egy adott esemény bekövetkezésekor, vagy ha egy adott feltétel teljesül. Minden alkalommal, amikor az eseményindító aktiválódik, a Logic Apps motor létrehoz egy logikai alkalmazáspéldányt, és megkezdi az alkalmazás munkafolyamatának futtatását.

Ebben a példában létrehoz egy logikai alkalmazást egy végpontot az Azure-ban, így *http-posta-kérelmeket* küldhet a logikai alkalmazásnak. Amikor a logikai alkalmazás megkapja ezeket a HTTP-kérelmeket, az eseményindító aktiválódik, és futtatja a munkafolyamat következő lépését.

1. Az [Azure Portalon](https://portal.azure.com)hozzon létre egy üres logikai alkalmazást, amely megnyitja a Logic App Designer.

1. A keresőmezőbe írja `http request` be szűrőként. Az **Eseményindítók** listában válassza a **HTTP-kérelem fogadásának kora**lehetőséget.

   ![HTTP-kérelem hozzáadása eseményindító](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Most mentse a logikai alkalmazást, hogy létrehozhassa a logikai alkalmazás végponti URL-címét. A tervező eszköztárán válassza a **Mentés gombot.**

   A végpont URL-címe most megjelenik az eseményindítóban, például:

   ![URL létrehozása a végponthoz](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>SAP-művelet hozzáadása

Az Azure Logic Apps egy [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) egy lépés a munkafolyamatban, amely követi az eseményindító vagy egy másik művelet. Ha még nem adott hozzá eseményindítót a logikai alkalmazáshoz, és követni szeretné ezt a példát, [adja hozzá az ebben a szakaszban leírt eseményindítót.](#add-trigger)

1. A Logic App Designer az eseményindító alatt válassza az **Új lépés lehetőséget.**

   ![Új lépés hozzáadása a logikai alkalmazáshoz](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. A keresőmezőbe írja `sap` be szűrőként. A **Műveletek** listában válassza az **Üzenet küldése az SAP-nak lehetőséget.**
  
   ![Válassza az "Üzenet küldése az SAP-nak" műveletet](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Vagy kiválaszthatja a **Vállalati** lapot, és kiválaszthatja az SAP-műveletet.

   ![Válassza az "Üzenet küldése az SAP-nak" művelet lehetőséget a Vállalati lapon](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Ha a kapcsolat már létezik, folytassa a következő lépéssel, így beállíthatja az SAP-műveletet. Ha azonban a rendszer kéri a kapcsolat részleteit, adja meg az adatokat, hogy most már létrehozhat egy kapcsolatot a helyszíni SAP-kiszolgálóval.

   1. Adja meg a kapcsolat nevét.

   1. A **Data Gateway** **szakaszelőfizetés**csoportban először válassza ki az Azure-előfizetést az azure-portálon létrehozott átjáró-erőforráshoz az átjáró telepítéséhez. 
   
   1. A **Connection Gateway csoportban**válassza ki az átjáróerőforrást.

   1. Továbbra is adjon tájékoztatást a kapcsolatról. A **Bejelentkezés típusa** tulajdonságesetén kövesse a lépést annak alapján, hogy a tulajdonság **alkalmazáskiszolgálóvagy** **csoport**lesz-e:
   
      * **Az Application Server**esetében ezek a tulajdonságok, amelyek általában nem kötelezőnek tűnnek, szükségesek:

        ![SAP-alkalmazáskiszolgáló-kapcsolat létrehozása](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * A **Csoport**esetében ezek a tulajdonságok, amelyek általában nem kötelezőnek tűnnek, szükségesek:

        ![SAP-üzenetkiszolgáló-kapcsolat létrehozása](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Alapértelmezés szerint az erős gépelés az érvénytelen értékek ellenőrzésére szolgál a sémán végzett XML-érvényesítés végrehajtásával. Ez a viselkedés segíthet a korábbi problémák észlelésén. A **Biztonságos gépelés** beállítás visszamenőleges kompatibilitásesetén érhető el, és csak a karakterlánc hosszát ellenőrzi. További információ a [Biztonságos gépelés beállításról.](#safe-typing)

   1. Ha végzett, válassza a **Létrehozás gombot.**

      A Logic Apps beállítja és teszteli a kapcsolatot, hogy megbizonyosodjon arról, hogy a kapcsolat megfelelően működik.

1. Most keresse meg és válassza ki a műveletet az SAP-kiszolgálóról.

   1. Az **SAP művelet mezőjében** jelölje ki a mappa ikonját. A fájllistában keresse meg és jelölje ki a használni kívánt SAP-üzenetet. A listában való navigáláshoz használja a nyilakat.

      Ez a példa a Rendelések típusú IDoc-ot **választja** ki.

      ![IDoc művelet keresése és kijelölése](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Ha nem találja a kívánt műveletet, manuálisan is megadhat egy elérési utat, például:

      ![Az IDoc-művelet elérési útjának manuális biztosítása](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Adja meg az **SAP Action** értékét a kifejezésszerkesztőn keresztül. Így ugyanazt a műveletet használhatja a különböző üzenettípusokhoz.

      Az IDoc-műveletekről további információt az [IDOC-műveletek üzenetsémái című témakörben talál.](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

   1. Kattintson a **Beviteli üzenet** mezőbe, hogy a dinamikus tartalomlista megjelenjen. Ebből a listából a **HTTP-kérelem érkezésekor**csoportban válassza a **Törzs** mezőt.

      Ez a lépés tartalmazza a http-kérelem eseményindító törzstartalmát, és elküldi a kimenetet az SAP-kiszolgálónak.

      ![Válassza ki a "Body" tulajdonságot az eseményindítóból](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Ha elkészült, az SAP-művelet így néz ki:

      ![Az SAP-művelet befejezése](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.**

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>HTTP-válaszművelet hozzáadása

Most adjon hozzá egy válaszműveletet a logikai alkalmazás munkafolyamatához, és adja hozzá az SAP-művelet kimenetét. Így a logikai alkalmazás visszaadja az eredményeket az SAP-kiszolgálóról az eredeti kérelmezőnek.

1. A Logic App Designer ben az SAP-művelet alatt válassza az **Új lépés lehetőséget.**

1. A keresőmezőbe írja `response` be szűrőként. A **Műveletek** listában válassza a **Válasz**lehetőséget.

1. Kattintson a **Törzs** mezőbe, hogy megjelenjen a dinamikus tartalomlista. Ebből a listából az **Üzenet küldése az SAP-nak**csoportban válassza a **Törzs** mezőt.

   ![Az SAP-művelet befejezése](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Mentse a logikai alkalmazást.

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. Ha a logikai alkalmazás még nincs engedélyezve, a logikai alkalmazás menüjében válassza **az Áttekintés lehetőséget.** Az eszköztáron válassza az **Engedélyezés**lehetőséget.

1. A tervező eszköztárán válassza a **Futtatás**lehetőséget. Ez a lépés manuálisan elindítja a logikai alkalmazást.

1. Indítsa el a logikai alkalmazást egy HTTP POST-kérelem elküldésével a HTTP-kérelem eseményindítóurl-címére.
Az üzenet tartalmát is mellékelje a kéréshez. A kérelem elküldéséhez használhat egy eszközt, például [a Postmant.](https://www.getpostman.com/apps)

   Ebben a cikkben a kérelem egy IDoc-fájlt küld, amelynek XML formátumúnak kell lennie, és tartalmaznia kell a használt SAP-művelet névterét, például:

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. A HTTP-kérelem elküldése után várja meg a választ a logikai alkalmazásból.

   > [!NOTE]
   > Előfordulhat, hogy a logikai alkalmazás túllépi az időkorlátot, ha a válaszhoz szükséges összes lépés nem fejeződik be a [kérelem időkorlátján](./logic-apps-limits-and-config.md)belül. Ha ez a feltétel megtörténik, előfordulhat, hogy a kérelmek letiltásra kerülnek. A problémák diagnosztizálásának elősegítése érdekében ismerje meg, hogyan [ellenőrizheti és figyelheti a logikai alkalmazásokat.](../logic-apps/monitor-logic-apps.md)

Most létrehozott egy logikai alkalmazást, amely képes kommunikálni az SAP-kiszolgálóval. Most, hogy beállítottegy SAP-kapcsolatot a logikai alkalmazáshoz, más elérhető SAP-műveleteket is megvizsgálhat, például a BAPI-t és az RFC-t.

<a name="receive-from-sap"></a>

## <a name="receive-message-from-sap"></a>Üzenet fogadása az SAP-tól

Ez a példa egy logikai alkalmazást használ, amely akkor aktiválódik, amikor az alkalmazás üzenetet kap egy SAP-rendszerből.

### <a name="add-an-sap-trigger"></a>SAP-eseményindító hozzáadása

1. Az Azure Portalon hozzon létre egy üres logikai alkalmazást, amely megnyitja a Logic App Designer.

1. A keresőmezőbe írja `sap` be szűrőként. Az **Eseményindítók** listában válassza a **Amikor üzenet érkezik az SAP-tól,** válassza az Üzenet érkezésekor lehetőséget.

   ![SAP-eseményindító hozzáadása](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   Vagy válassza ki a **Vállalati** lapot, majd válassza ki az eseményindítót:

   ![SAP-eseményindító hozzáadása a Vállalati lapról](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Ha a kapcsolat már létezik, folytassa a következő lépéssel, így beállíthatja az SAP-műveletet. Ha azonban a rendszer kéri a kapcsolat részleteit, adja meg az adatokat, hogy most már létrehozhat egy kapcsolatot a helyszíni SAP-kiszolgálóval.

   1. Adja meg a kapcsolat nevét.

   1. A **Data Gateway** **szakaszelőfizetés**csoportban először válassza ki az Azure-előfizetést az azure-portálon létrehozott átjáró-erőforráshoz az átjáró telepítéséhez. 

   1. A **Connection Gateway csoportban**válassza ki az átjáróerőforrást.

   1. Továbbra is adjon tájékoztatást a kapcsolatról. A **Bejelentkezés típusa** tulajdonságesetén kövesse a lépést annak alapján, hogy a tulajdonság **alkalmazáskiszolgálóvagy** **csoport**lesz-e:

      * **Az Application Server**esetében ezek a tulajdonságok, amelyek általában nem kötelezőnek tűnnek, szükségesek:

        ![SAP-alkalmazáskiszolgáló-kapcsolat létrehozása](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * A **Csoport**esetében ezek a tulajdonságok, amelyek általában nem kötelezőnek tűnnek, szükségesek:

        ![SAP-üzenetkiszolgáló-kapcsolat létrehozása](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Alapértelmezés szerint az erős gépelés az érvénytelen értékek ellenőrzésére szolgál a sémán végzett XML-érvényesítés végrehajtásával. Ez a viselkedés segíthet a korábbi problémák észlelésén. A **Biztonságos gépelés** beállítás visszamenőleges kompatibilitásesetén érhető el, és csak a karakterlánc hosszát ellenőrzi. További információ a [Biztonságos gépelés beállításról.](#safe-typing)

   1. Ha végzett, válassza a **Létrehozás gombot.**

      A Logic Apps beállítja és teszteli a kapcsolatot, hogy megbizonyosodjon arról, hogy a kapcsolat megfelelően működik.

1. Adja meg a szükséges paramétereket az SAP-rendszer konfigurációja alapján.

   Opcionálisan egy vagy több SAP-műveletet is megadhat. Ez a műveletlista határozza meg az okat az üzeneteket, amelyeket az eseményindító kap az SAP-kiszolgálóról az adatátjárón keresztül. Az üres lista azt adja meg, hogy az eseményindító megkapja az összes üzenetet. Ha a listában egynél több üzenet található, az eseményindító csak a listában megadott üzeneteket fogadja. Az SAP-kiszolgálóról küldött egyéb üzeneteket az átjáró elutasítja.

   A fájlválasztóból kiválaszthat egy SAP-műveletet:

   ![SAP-művelet hozzáadása a logikai alkalmazáshoz](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Vagy manuálisan is megadhat egy műveletet:

   ![SAP-művelet manuális megadása](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Íme egy példa, amely bemutatja, hogyan jelenik meg a művelet, amikor úgy állítja be az eseményindítót, hogy egynél több üzenetet fogadjon.

   ![Több üzenetet fogadó eseményindító példa](media/logic-apps-using-sap-connector/example-trigger.png)

   Az SAP-műveletről további információt az [IDOC-műveletek üzenetsémái című témakörben talál.](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. Most mentse a logikai alkalmazást, így elkezdheti az SAP-rendszerből érkező üzenetek fogadását. A tervező eszköztárán válassza a **Mentés gombot.**

A logikai alkalmazás most már készen áll az SAP-rendszerből érkező üzenetek fogadására.

> [!NOTE]
> Az SAP-eseményindító nem egy lekérdezési eseményindító, hanem egy webhook-alapú eseményindító helyett. Az eseményindító t csak akkor hívják meg az átjáróról, ha létezik üzenet, így nincs szükség lekérdezésre.

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. A logikai alkalmazás aktiválásához küldjön egy üzenetet az SAP-rendszerből.

1. A logikai alkalmazás menüjében válassza az **Áttekintés**lehetőséget. Tekintse át a **futtatások előzményeit** a logikai alkalmazás hoz a logikai alkalmazáshoz.

1. Nyissa meg a legutóbbi futtatást, amely az SAP-rendszertől küldött üzenetet jeleníti meg az eseményindító kimenetek szakaszában.

## <a name="receive-idoc-packets-from-sap"></a>IDOC csomagok fogadása az SAP-tól

Beállíthatja, hogy az SAP [iDOC-ket küldjön csomagokban](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/en-US/4ab38886549a6d8ce10000000a42189c.html), amelyek az IDOC-k kötegei vagy csoportjai. IDOC-csomagok fogadásához az SAP-összekötő, és különösen az eseményindító, nem igényel további konfigurációt. Ahhoz azonban, hogy az eseményindító a csomag fogadása után feldolgozza az IDOC-csomag minden elemét, néhány további lépésre van szükség ahhoz, hogy a csomagot egyedi IDOC-kre ossza fel.

Íme egy példa, amely bemutatja, hogyan lehet kinyerni [ `xpath()` ](./workflow-definition-language-functions-reference.md#xpath)az egyes IDOC-kat egy csomagból a funkció használatával:

1. Mielőtt elkezdené, szüksége van egy SAP-eseményindítóval rendelkező logikai alkalmazásra. Ha még nem rendelkezik ezzel a logikai alkalmazással, kövesse a témakör előző lépéseit [egy SAP-eseményindítóval rendelkező logikai alkalmazás beállításához.](#receive-from-sap)

   Példa:

   ![SAP-eseményindító hozzáadása a logikai alkalmazáshoz](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. A gyökérnévtér beszerezni az XML IDOC, hogy a logikai alkalmazás kap az SAP-tól. Ha ki szeretné nyerni ezt a névteret az XML-dokumentumból, adjon hozzá `xpath()` egy lépést, amely létrehoz egy helyi karakterlánc-változót, és a névteret egy kifejezés használatával tárolja:

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Root névtér bekéselése az IDOC-tól](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Egyéni IDOC kinyeréséhez adjon hozzá egy lépést, amely létrehoz egy `xpath()` tömbváltozót, és egy másik kifejezés használatával tárolja az IDOC-gyűjteményt:

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Elemek tömbjeinek beszereznie](./media/logic-apps-using-sap-connector/get-array.png)

   A tömbváltozó minden iDOC elérhetővé teszi a logikai alkalmazás számára, hogy a gyűjtemény enumerálásával külön-külön dolgozzon fel. Ebben a példában a logikai alkalmazás minden IDOC-ot átad egy SFTP-kiszolgálóra egy ciklus használatával:

   ![IDOC küldése az SFTP-kiszolgálóra](./media/logic-apps-using-sap-connector/loop-batch.png)

   Minden IDOC-nak tartalmaznia kell a gyökérnévteret, ezért a `<Receive></Receive` fájltartalma egy elembe van csomagolva a gyökérnévtérrel együtt, mielőtt az IDOC-ot az alsóbb rétegbeli alkalmazásnak vagy ebben az esetben Az SFTP-kiszolgálónak elküldené.

A mintához használható rövid útmutató sablont a sablon kijelölésével a Logic App Designer ben, amikor új logikai alkalmazást hoz létre.

![Köteglogikai alkalmazássablon kiválasztása](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Sémák létrehozása az SAP-ban lévő műtermékekhez

Ez a példa egy logikai alkalmazást használ, amelyet HTTP-kérelemmel aktiválhat. Az SAP-művelet egy SAP-rendszernek küld egy kérelmet a megadott IDoc és BAPI sémák létrehozásához. Sémák, amelyek a válaszban a válaszban a feltöltésre egy integrációs fiók az Azure Resource Manager-összekötő használatával.

### <a name="add-an-http-request-trigger"></a>HTTP-kérelem eseményindítójának hozzáadása

1. Az Azure Portalon hozzon létre egy üres logikai alkalmazást, amely megnyitja a Logic App Designer.

1. A keresőmezőbe írja `http request` be szűrőként. Az **Eseményindítók** listában válassza a **HTTP-kérelem fogadásának kora**lehetőséget.

   ![HTTP-kérelem hozzáadása eseményindító](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Most mentse a logikai alkalmazást, így létrehozhat egy végpont URL-címet a logikai alkalmazáshoz.
A tervező eszköztárán válassza a **Mentés gombot.**

   A végpont URL-címe most megjelenik az eseményindítóban, például:

   ![URL létrehozása a végponthoz](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Sap-művelet hozzáadása sémák létrehozásához

1. A Logic App Designer az eseményindító alatt válassza az **Új lépés lehetőséget.**

   ![Új lépés hozzáadása a logikai alkalmazáshoz](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. A keresőmezőbe írja `sap` be szűrőként. A **Műveletek** listában válassza **a Sémák létrehozása**lehetőséget.
  
   !["Séma létrehozása" művelet hozzáadása a logikai alkalmazáshoz](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Vagy kiválaszthatja a **Vállalati** lapot, és kiválaszthatja az SAP-műveletet.

   ![Válassza az SAP küldési műveletet a Vállalati lapon](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Ha a kapcsolat már létezik, folytassa a következő lépéssel, így beállíthatja az SAP-műveletet. Ha azonban a rendszer kéri a kapcsolat részleteit, adja meg az adatokat, hogy most már létrehozhat egy kapcsolatot a helyszíni SAP-kiszolgálóval.

   1. Adja meg a kapcsolat nevét.

   1. A **Data Gateway** **szakaszelőfizetés**csoportban először válassza ki az Azure-előfizetést az azure-portálon létrehozott átjáró-erőforráshoz az átjáró telepítéséhez. 
   
   1. A **Connection Gateway csoportban**válassza ki az átjáróerőforrást.

   1. Továbbra is adjon tájékoztatást a kapcsolatról. A **Bejelentkezés típusa** tulajdonságesetén kövesse a lépést annak alapján, hogy a tulajdonság **alkalmazáskiszolgálóvagy** **csoport**lesz-e:
   
      * **Az Application Server**esetében ezek a tulajdonságok, amelyek általában nem kötelezőnek tűnnek, szükségesek:

        ![SAP-alkalmazáskiszolgáló-kapcsolat létrehozása](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * A **Csoport**esetében ezek a tulajdonságok, amelyek általában nem kötelezőnek tűnnek, szükségesek:

        ![SAP-üzenetkiszolgáló-kapcsolat létrehozása](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Alapértelmezés szerint az erős gépelés az érvénytelen értékek ellenőrzésére szolgál a sémán végzett XML-érvényesítés végrehajtásával. Ez a viselkedés segíthet a korábbi problémák észlelésén. A **Biztonságos gépelés** beállítás visszamenőleges kompatibilitásesetén érhető el, és csak a karakterlánc hosszát ellenőrzi. További információ a [Biztonságos gépelés beállításról.](#safe-typing)

   1. Ha végzett, válassza a **Létrehozás gombot.**

      A Logic Apps beállítja és teszteli a kapcsolatot, hogy megbizonyosodjon arról, hogy a kapcsolat megfelelően működik.

1. Adja meg annak a műterméknek az elérési útját, amelyhez a sémát létre kívánja hozni.

   Az SAP-műveletet a fájlválasztóból választhatja ki:

   ![SAP-művelet kiválasztása](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Vagy manuálisan is megadhatja a műveletet:

   ![SAP-művelet manuális megadása](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Ha egynél több összetevőséméhez szeretne sémákat létrehozni, adja meg az SAP-művelet részleteit az egyes összetevőkhöz, például:

   ![Válassza az Új elem hozzáadása lehetőséget.](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Két elem megjelenítése](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Az SAP-műveletről további információt az [IDOC-műveletek üzenetsémái című témakörben talál.](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.**

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. A tervező eszköztáron válassza a **Futtatás** lehetőséget a logikai alkalmazás futtatásának elindításához.

1. Nyissa meg a futtatást, és ellenőrizze a **sémák létrehozása** művelet kimeneteit.

   A kimenetek az üzenetek megadott listájához létrehozott sémákat jelenítik meg.

### <a name="upload-schemas-to-an-integration-account"></a>Sémák feltöltése integrációs fiókba

Szükség esetén letöltheti vagy tárolhatja a létrehozott sémákat az adattárakban, például egy blobban, tárolóban vagy integrációs fiókban. Az integrációs fiókok első osztályú élményt nyújtanak más XML-műveletekkel, így ez a példa bemutatja, hogyan tölthet fel sémákat egy integrációs fiókba ugyanahhoz a logikai alkalmazáshoz az Azure Resource Manager-összekötő használatával.

1. A Logic App Designer az eseményindító alatt válassza az **Új lépés lehetőséget.**

1. A keresőmezőbe írja `Resource Manager` be szűrőként. Válassza **az Erőforrás létrehozása vagy frissítése**lehetőséget.

   ![Az Azure Resource Manager művelet kiválasztása](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Adja meg a művelet részleteit, beleértve az Azure-előfizetést, az Azure-erőforráscsoportot és az integrációs fiókot. Ha SAP-jogkivonatokat szeretne hozzáadni a mezőkhöz, kattintson a mezők mezőibe, és válasszon a megjelenő dinamikus tartalomlistából.

   1. Nyissa **meg** az Új paraméter hozzáadása listát, és jelölje ki a **Hely** és **tulajdonságok** mezőket.

   1. Adja meg az új mezők részleteit a példában látható módon.

      ![Adja meg az Azure Resource Manager művelet részleteit](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   Az SAP **séma létrehozása** művelet létrehoz sémák, mint egy gyűjtemény, így a tervező automatikusan hozzáad egy **minden** hurok a művelethez. Íme egy példa, amely bemutatja, hogyan jelenik meg ez a művelet:

   ![Az Azure Resource Manager művelet "minden" ciklussal](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > A sémák base64 kódolású formátumot használnak. A sémák egy integrációs fiókba való feltöltéséhez `base64ToString()` a függvény használatával kell dekódolni őket. Íme egy példa, amely az `"properties"` elem kódját mutatja:
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.**

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. A tervező eszköztárán válassza a **Futtatás** lehetőséget a logikai alkalmazás manuális aktiválásához.

1. Sikeres futtatás után nyissa meg az integrációs fiókot, és ellenőrizze, hogy a létrehozott sémák léteznek-e.

## <a name="enable-secure-network-communications"></a>Biztonságos hálózati kommunikáció engedélyezése

Mielőtt elkezdené, győződjön meg arról, hogy megfelelt a korábban felsorolt [előfeltételeknek:](#pre-reqs)

* A helyszíni adatátjáró egy olyan gépen van telepítve, amely ugyanabban a hálózatban van, mint az SAP-rendszer.
* Az SSO számára az átjáró egy SAP-felhasználóhoz leképezett felhasználóként fut.
* A további biztonsági funkciókat kiszolgáló SNC-könyvtár ugyanarra a számítógépre van telepítve, mint az adatátjáró. Néhány példa a [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos és NTLM.

   Ha engedélyezni szeretné az SNC-t az SAP-rendszerbe irányuló vagy onnan érkező kérésekhez, jelölje be az **SNC használata** jelölőnégyzetet az SAP-kapcsolatban, és adja meg a következő tulajdonságokat:

   ![SAP SNC konfigurálása kapcsolatban](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Tulajdonság | Leírás |
   |----------| ------------|
   | **SNC könyvtár elérési útja** | Az SNC-könyvtár neve vagy elérési útja az NCo telepítési helyéhez vagy az abszolút elérési úthoz viszonyítva. Példák `sapsnc.dll` `.\security\sapsnc.dll` vagy `c:\security\sapsnc.dll`. |
   | **SNC SSO** | Amikor Az SNC-n keresztül csatlakozik, az SNC-identitás általában a hívó hitelesítésére szolgál. Egy másik lehetőség, hogy felülbírálja, hogy a felhasználói és jelszóadatok at lehessen használni a hívó hitelesítéséhez, de a vonal továbbra is titkosítva van. |
   | **SNC Nevem** | A legtöbb esetben ez a tulajdonság elhagyható. A telepített SNC megoldás általában tudja a saját SNC nevét. Csak a több identitást támogató megoldások esetén előfordulhat, hogy meg kell adnia az adott célhoz vagy kiszolgálóhoz használandó identitást. |
   | **SNC partner neve** | A háttérSNC neve. |
   | **SNC Védelem minősége** | Az adott célállomás vagy szerver SNC-kommunikációhoz használandó szolgáltatás minősége. Az alapértelmezett értéket a háttérrendszer határozza meg. A maximális értéket az SNC-hez használt biztonsági termék határozza meg. |
   |||

   > [!NOTE]
   > Ne állítsa be a környezeti változókat SNC_LIB és SNC_LIB_64 azon a számítógépen, ahol az adatátjáró és az SNC-könyvtár található. Ha be van állítva, elsőbbséget élveznek az összekötőn áthaladó SNC-könyvtár értékével szemben.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Biztonságos gépelés

Alapértelmezés szerint az SAP-kapcsolat létrehozásakor erős gépelést használ az érvénytelen értékek ellenőrzésére a sémán végzett XML-érvényesítés végrehajtásával. Ez a viselkedés segíthet a korábbi problémák észlelésén. A **Biztonságos gépelés** beállítás visszamenőleges kompatibilitásesetén érhető el, és csak a karakterlánc hosszát ellenőrzi. Ha a **Biztonságos gépelés lehetőséget választja,** az SAP-ban a DATS és a `xs:date` `xs:time`TIMS-típust a rendszer karakterláncként kezeli, nem pedig XML-megfelelőként, és ahol `xmlns:xs="http://www.w3.org/2001/XMLSchema"`. A biztonságos gépelés hatással van az összes sémagenerálás, a küldési üzenet mind a "küldve" hasznos adat, mind a "fogadott" válasz, valamint az eseményindító viselkedésére. 

Erős gépelés használata esetén ( a**biztonságos gépelés** nincs engedélyezve), a séma a DATS- és TIMS-típusokat egyszerűbb XML-típusokra képezi le:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Ha erős gépeléssel küld üzeneteket, a DATS- és TIMS-válasz megfelel a megfelelő XML-típusformátumnak:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Ha a **Biztonságos gépelés** engedélyezve van, a séma a DATS- és TIMS-típusokat csak hosszkorlátozásokkal rendelkező XML-karakterláncmezőkre képezi le:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

Ha az üzenetek küldése engedélyezve van a **Biztonságos gépelés funkcióval,** a DATS- és TIMS-válasz a következő példához hasonlóan néz ki:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>Speciális forgatókönyvek

### <a name="confirm-transaction-explicitly"></a>Tranzakció kifejezett megerősítése

Amikor tranzakciókat küld az SAP-nak a Logic Apps alkalmazásból, ez az adatcsere két lépésben történik az SAP-dokumentumban, a [Transactional RFC Server Programs programban leírtak szerint.](https://help.sap.com/doc/saphelp_nwpi71/7.1/en-US/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true) Alapértelmezés szerint a **Küldés az SAP-nak** művelet kezeli a függvényátvitel és a tranzakció egyetlen hívásban történő megerősítésének lépéseit. Az SAP-összekötő lehetővé teszi, hogy ezeket a lépéseket leválassza. IDOC-ot küldhet, és ahelyett, hogy automatikusan megerősítené a tranzakciót, használhatja az explicit **Tranzakcióazonosító-igazolás** id-műveletet.

Ez a képesség a tranzakcióazonosító megerősítésének leválasztására akkor hasznos, ha nem szeretné duplikálni a tranzakciókat az SAP-ban, például olyan esetekben, ahol hibák fordulhatnak elő olyan okok miatt, mint például a hálózati problémák. A tranzakcióazonosító külön-külön történő megerősítésével a tranzakció csak egyszer fejeződik be az SAP-rendszerben.

Íme egy példa, amely bemutatja ezt a mintát:

1. Hozzon létre egy üres logikai alkalmazást, és adjon hozzá egy HTTP-eseményindítót.

1. Az SAP-összekötőből adja hozzá az **IDOC küldése** műveletet. Adja meg az SAP-rendszerbe küldött IDOC adatait.

1. Ha külön lépésben szeretné megerősíteni a tranzakcióazonosítót, a **TID-megerősítés** mezőben válassza a **Nem**lehetőséget. A választható **tranzakcióazonosító guid** mezőesetén manuálisan megadhatja az értéket, vagy beállíthatja, hogy az összekötő automatikusan létrehozza és visszaadja ezt a GUID azonosítót az IDOC küldése műveletből adott válaszban.

   ![IDOC-művelet tulajdonságainak küldése](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. A tranzakcióazonosító kifejezett megerősítéséhez adja hozzá a **Tranzakcióazonosító megerősítése** műveletet. Kattintson a **Tranzakcióazonosító** mezőbe, hogy a dinamikus tartalomlista megjelenjen. Ebből a listából válassza ki az **IDOC küldése** műveletből visszaadott **tranzakcióazonosító-értéket.**

   ![Tranzakcióazonosító-művelet megerősítése](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   Ez a lépés futtatása után az aktuális tranzakció van megjelölve kész mindkét végén, az SAP-összekötő oldalán és az SAP-rendszer oldalán.

## <a name="known-issues-and-limitations"></a>Ismert problémák és korlátozások

Az SAP-összekötő jelenleg ismert problémái és korlátai a következők:

* Az SAP-eseményindító nem támogatja az adatátjáró-fürtöket. Egyes feladatátvételi esetekben az SAP-rendszerrel kommunikáló adatátjáró-csomópont eltérhet az aktív csomóponttól, ami váratlan viselkedést eredményez. A küldési forgatókönyvek esetében az adatátjáró-fürtök támogatottak.

* Az SAP-összekötő jelenleg nem támogatja az SAP-útválasztó karakterláncait. A helyszíni adatátjárónak ugyanazon a helyi hálózaton kell lennie, mint a csatlakoztatni kívánt SAP-rendszernek.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötővel kapcsolatos további technikai részleteket, például az eseményindítókat, műveleteket és korlátokat az összekötő Swagger-fájlja szerint lásd az [összekötő referencialapján.](https://docs.microsoft.com/connectors/sap/)

> [!NOTE]
> [Az integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)lévő logikai alkalmazások esetében az összekötő ISE-címkével ellátott verziója az [ISE-üzenetkorlátokat](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) használja.

## <a name="next-steps"></a>További lépések

* [Csatlakozzon a helyszíni rendszerekhez](../logic-apps/logic-apps-gateway-connection.md) az Azure Logic Apps alkalmazásból.
* Megtudhatja, hogy miként ellenőrizheti, alakíthatja át és használhatja az egyéb üzenetműveleteket az [Enterprise Integration Pack csomaggal.](../logic-apps/logic-apps-enterprise-integration-overview.md)
* További információ a [Logic Apps-összekötőkről.](../connectors/apis-list.md)
