---
title: Kapcsolódás SAP-rendszerekhez
description: Az SAP-erőforrások elérése és kezelése a munkafolyamatok automatizálásával Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/27/2020
tags: connectors
ms.openlocfilehash: 36e22fd92d937271a3859d03367e2a7ef80ef3d2
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118668"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Csatlakozás SAP-rendszerekhez az Azure Logic Appsből

> [!IMPORTANT]
> A korábbi SAP-alkalmazáskiszolgáló és az SAP Message Server-összekötők a 2020. február 29-én elavultak. A jelenlegi SAP-összekötő összevonja ezeket az előző SAP-összekötőket, így nem kell módosítania a kapcsolat típusát, teljes mértékben kompatibilis az előző összekötővel, számos további képességet biztosít, és folytatja az SAP .net-összekötő függvénytár (SAP NKH) használatát.
>
> A régebbi összekötőket használó logikai alkalmazások esetében [telepítse át a legújabb összekötőt](#migrate) az Érvénytelenítési dátum előtt. Ellenkező esetben ezek a logikai alkalmazások a végrehajtási hibákat tapasztalják, és nem tudnak üzeneteket küldeni az SAP-rendszernek.

Ez a cikk bemutatja, hogyan érheti el a helyszíni SAP-erőforrásokat egy logikai alkalmazásból az SAP-összekötő használatával. Az összekötő az SAP klasszikus kiadásaival működik, mint például az R/3 és az ECC rendszerek a helyszínen. Az összekötő az SAP újabb HANA-alapú SAP-rendszereivel, például az S/4 HANA-vel való integrációt is lehetővé teszi, függetlenül attól, hogy azok a helyszínen vagy a felhőben vannak tárolva. Az SAP-összekötő támogatja az SAP NetWeaver-alapú rendszerek üzenet-vagy adatintegrációját köztes dokumentumon (IDoc), a Business Application Programming Interface (BAPI) vagy a Remote Function Call (RFC) szolgáltatáson keresztül.

Az SAP-összekötő az [SAP .net Connector (NKH) függvénytárát](https://support.sap.com/en/product/connectors/msnet.html) használja, és a következő műveleteket biztosítja:

* **Üzenet küldése az SAP**-nak: küldje el az IDoc-t a tRFC-on keresztül, hívja meg az BAPI függvényt az RFC-en keresztül, vagy hívja az RFC/tRFC
* **Ha üzenet érkezik az SAP**: Receive IDoc over tRFC, hívja a BAPI functions-t a tRFC-on keresztül, vagy hívja meg az RFC/tRFC-t az SAP Systems-ben.
* **Sémák előállítása**: sémák előállítása az SAP-összetevőkhöz a IDoc, a BAPI és az RFC számára.

Ezen műveletek esetében az SAP-összekötő a felhasználónevek és jelszavak használatával támogatja az alapszintű hitelesítést. Az összekötő támogatja a [biztonságos hálózati kommunikációt (Snc)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true)is. A SNC az SAP NetWeaver egyszeri bejelentkezéshez (SSO), vagy egy külső biztonsági termék által biztosított további biztonsági képességekhez használható.

Az SAP-összekötő helyszíni SAP-rendszerekkel integrálódik a helyszíni [adatátjárón](../logic-apps/logic-apps-gateway-connection.md)keresztül. A küldési forgatókönyvek esetében például, amikor egy logikai alkalmazásból egy SAP-rendszerbe küldenek egy üzenetet, az adatátjáró RFC-ügyfélként működik, és továbbítja a logikai alkalmazástól az SAP-nek fogadott kérelmeket. Hasonlóképpen, a fogadási helyzetekben az adatátjáró olyan RFC-kiszolgálóként működik, amely az SAP-kérelmeket fogadja, és továbbítja őket a logikai alkalmazásnak.

Ebből a cikkből megtudhatja, hogyan hozhat létre az SAP-nal integrálható, például a korábban leírt integrációs forgatókönyveket használó logikai alkalmazásokat. A régebbi SAP-összekötőket használó Logic apps esetén ez a cikk bemutatja, hogyan telepíthet át logikai alkalmazásait a legújabb SAP-összekötőre.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Előfeltételek

Ennek a cikknek a követéséhez a következő elemek szükségesek:

* Azure-előfizetés. Ha még nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Az a logikai alkalmazás, amelyről el szeretné érni az SAP-rendszerét, valamint egy triggert, amely elindítja a logikai alkalmazás munkafolyamatát. Ha most ismerkedik a Logic apps szolgáltatással, olvassa el a [Mi a Azure Logic apps?](../logic-apps/logic-apps-overview.md) és a gyors útmutató [: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)című témakört.

* Az [SAP-alkalmazáskiszolgáló](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) vagy az [SAP-üzenetküldési kiszolgáló](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).

* [Töltse le és telepítse a helyszíni adatátjárót](../logic-apps/logic-apps-gateway-install.md) a helyi számítógépre. Ezután [hozzon létre egy Azure Gateway-erőforrást](../logic-apps/logic-apps-gateway-connection.md#create-azure-gateway-resource) az adott átjáróhoz a Azure Portal. Az átjáró segítségével biztonságosan férhet hozzá a helyszíni információkhoz és erőforrásokhoz. 

  * Ajánlott eljárásként győződjön meg arról, hogy a helyszíni adatátjáró támogatott verzióját használja. A Microsoft minden hónapban új verziót bocsát ki. A Microsoft jelenleg az utolsó hat verziót támogatja. Ha az átjáróval kapcsolatos problémát tapasztal, próbálkozzon [a legújabb verzióra való frissítéssel](https://aka.ms/on-premises-data-gateway-installer), amely a probléma megoldásához szükséges frissítéseket is tartalmazhatja.

* [Töltse le, telepítse és konfigurálja a legújabb SAP ügyféloldali kódtárat](#sap-client-library-prerequisites) ugyanarra a számítógépre, mint a helyszíni adatátjárót.

* Az SAP-kiszolgálónak küldendő üzenetek, például a minta IDoc fájlnak XML formátumúnak kell lennie, és tartalmaznia kell a használni kívánt SAP-művelet névterét.

### <a name="sap-client-library-prerequisites"></a>Az SAP-ügyfél függvénytárának előfeltételei

* Alapértelmezés szerint az SAP-telepítő az alapértelmezett telepítési mappában helyezi el az összeállítási fájlokat. Másolja a szerelvény fájljait az alapértelmezett telepítési mappából az átjáró telepítési mappájába.

    * Ha az SAP-kapcsolat meghiúsul a következő hibaüzenettel: "Kérjük, ellenőrizze a fiók adatait és/vagy engedélyeit, és próbálkozzon újra", előfordulhat, hogy a szerelvény fájljai nem megfelelő helyen találhatók. Győződjön meg arról, hogy a szerelvény fájljait átmásolta az átjáró telepítési mappájába. Ezt követően [használja a .net Assembly kötési napló megjelenítőjét a hibaelhárításhoz](https://docs.microsoft.com/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer), amely lehetővé teszi annak ellenőrzését, hogy a szerelvény fájljai a megfelelő helyen vannak-e.

    * Igény szerint a **globális szerelvény-gyorsítótár regisztrációját** is kiválaszthatja az SAP ügyféloldali kódtár telepítésekor.

* Győződjön meg arról, hogy a legújabb verziót, az [SAP Connectort (nkh 3,0) telepíti a .NET-keretrendszer 4,0-Windows 64-bit (x64) 3.0.22.0 lefordított Microsoft .net](https://softwaredownloads.sap.com/file/0020000001000932019)a következő okok miatt:

    * A korábbi SAP NKH-verziók holtpontra válhatnak, ha egyszerre több IDoc üzenetet küld. Ez az állapot blokkolja az SAP-célhelyre küldött összes újabb üzenetet, ami miatt az üzenetek időtúllépést okoznak.
    * A helyszíni adatátjáró csak 64 bites rendszereken fut. Ellenkező esetben a "hibás rendszerkép" hibaüzenet jelenik meg, mert az adatátjáró-gazda szolgáltatás nem támogatja a 32 bites szerelvényeket.

    * Az adatátjáró-gazdagép és a Microsoft SAP-adapter egyaránt a .NET-keretrendszer 4,5-es frissítését használja. A .NET-keretrendszer 4,0-es SAP-NKH olyan folyamatokkal működik, amelyek .NET Runtime 4,0-et használnak a 4.7.1. A .NET-keretrendszer 2,0-es verziójának SAP-NKH olyan folyamatokkal működik, amelyek .NET Runtime 2,0-t használnak a 3,5-as verzióra, de már nem működik a legújabb helyszíni adatátjáróval.

### <a name="snc-prerequisites"></a>A SNC előfeltételei

Adja meg ezeket a beállításokat, ha a következőt használja: SNC (opcionális):

* Ha a SNC-t egyszeri bejelentkezéssel használja, győződjön meg róla, hogy az átjáró az SAP-felhasználóhoz hozzárendelt felhasználóként fut. Az alapértelmezett fiók módosításához válassza a **fiók módosítása**lehetőséget, és adja meg a felhasználói hitelesítő adatokat.

  ![Átjáró fiókjának módosítása](./media/logic-apps-using-sap-connector/gateway-account.png)

* Ha engedélyezi a SNC-t egy külső biztonsági termékkel, másolja a SNC-könyvtárat vagy-fájlokat ugyanarra a gépre, amelyen az átjáró telepítve van. Néhány példa a SNC-termékekre: [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), KERBEROS és NTLM.

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>Migrálás az aktuális összekötőre

1. Ha még nem tette meg, frissítse a helyszíni [adatátjárót](https://www.microsoft.com/download/details.aspx?id=53127) , hogy a legújabb verzióval rendelkezzen. További információ: [helyszíni adatátjáró telepítése Azure Logic Appshoz](../logic-apps/logic-apps-gateway-install.md).

1. A régebbi SAP-összekötőt használó logikai alkalmazásban törölje a **Küldés az SAP-** ba műveletet.

1. A legújabb SAP-összekötőből adja hozzá az **üzenet küldése az SAP** -művelethez lehetőséget. A művelet használata előtt hozza létre újra a kapcsolódást az SAP-rendszerrel.

1. Ha elkészült, mentse a logikai alkalmazást.

<a name="add-trigger"></a>

## <a name="send-message-to-sap"></a>Üzenet küldése az SAP-nak

Ez a példa egy logikai alkalmazást használ, amely HTTP-kéréssel aktiválható. A logikai alkalmazás egy IDoc küld egy SAP-kiszolgálónak, és visszaadja a logikai alkalmazásnak nevezett választ a kérelmezőnek.

### <a name="add-an-http-request-trigger"></a>HTTP-kérelem triggerének hozzáadása

Azure Logic Apps minden logikai alkalmazásnak egy [eseményindítóval](../logic-apps/logic-apps-overview.md#logic-app-concepts)kell kezdődnie, amely akkor következik be, amikor egy adott esemény történik, vagy ha egy adott feltétel teljesül. A Logic Apps motor létrehoz egy Logic app-példányt, és elindítja az alkalmazás munkafolyamatát.

> [!NOTE]
> Ha egy logikai alkalmazás IDoc-csomagokat fogad az SAP-ból, a [kérés-trigger](https://docs.microsoft.com/azure/connectors/connectors-native-reqres) nem támogatja az SAP WE60 IDoc dokumentációjában létrehozott "egyszerű" XML-sémát. Az "egyszerű" XML-séma azonban támogatott olyan forgatókönyvek esetén, amelyek üzeneteket küldenek a Logic apps *és az* SAP között. A kérelem triggert az SAP IDoc XML-kódjával használhatja, de az RFC-IDoc nem. Vagy átalakíthatja az XML-t a szükséges formátumba. 

Ebben a példában egy Azure-beli végponttal rendelkező logikai alkalmazást hoz létre, így *http post-kéréseket* küldhet a logikai alkalmazásnak. Ha a logikai alkalmazás fogadja ezeket a HTTP-kéréseket, az eseményindító elindít és futtatja a következő lépést a munkafolyamatban.

1. A [Azure Portal](https://portal.azure.com)hozzon létre egy üres logikai alkalmazást, amely megnyitja a Logic app designert.

1. A keresőmezőbe írja be `http request` szűrőként a kifejezést. Az **Eseményindítók** listából válassza ki, **hogy mikor érkezik HTTP-kérelem**.

   ![HTTP-kérési trigger hozzáadása](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Most mentse a logikai alkalmazást, hogy létrehozzon egy végponti URL-címet a logikai alkalmazáshoz. A tervező eszköztárán válassza a **Mentés**lehetőséget.

   A végpont URL-címe most megjelenik az triggerben, például:

   ![URL-cím előállítása a végponthoz](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>SAP-művelet hozzáadása

Azure Logic Apps a [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) egy olyan lépés a munkafolyamatban, amely egy triggert vagy egy másik műveletet követ. Ha még nem adott hozzá triggert a logikai alkalmazáshoz, és szeretné követni ezt a példát, [adja hozzá az ebben a részben ismertetett triggert](#add-trigger).

1. A Logic app Designerben az trigger alatt válassza az **új lépés**lehetőséget.

   ![Új lépés hozzáadása a logikai alkalmazáshoz](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. A keresőmezőbe írja be `sap` szűrőként a kifejezést. A **műveletek** listából válassza az **üzenet küldése az SAP**-nek lehetőséget.
  
   ![Az "üzenet küldése az SAP-nek" művelet kiválasztása](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Vagy válassza a **vállalat** lapot, és válassza ki az SAP-műveletet.

   ![Az "üzenet küldése az SAP-nek" művelet kiválasztása a vállalati lapon](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Ha a kapcsolat már létezik, folytassa a következő lépéssel, hogy beállítsa az SAP-műveletet. Ha azonban a rendszer megkéri a kapcsolat részleteinek megadását, adja meg az adatokat, hogy most létrehozza a kapcsolatát a helyszíni SAP-kiszolgálóval.

   1. Adja meg a kapcsolatok nevét.

   1. Az **adatátjáró** szakaszban az **előfizetés**területen válassza ki azt az átjáró-erőforráshoz tartozó Azure-előfizetést, amelyet az átjáró telepítéséhez Azure Portal hozott létre. 
   
   1. A **csatlakoztatási átjáró**területen válassza ki az átjáró erőforrását.

   1. Folytassa a kapcsolatok adatainak biztosítását. A **bejelentkezési típus** tulajdonságnál kövesse a lépést attól függően, hogy a tulajdonság az **Application Server** vagy a **Group**értékre van-e állítva:
   
      * Az **alkalmazáskiszolgáló**esetében ezek a tulajdonságok, amelyek általában opcionálisak, kötelezőek:

        ![SAP Application Server-kapcsolatok létrehozása](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * A **Group (csoport**) esetében ezek a tulajdonságok, amelyek általában opcionálisak, kötelezőek:

        ![SAP-üzenetkezelő kiszolgáló kapcsolatainak létrehozása](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Alapértelmezés szerint az erős beírással ellenőrizhető az érvénytelen értékek ellenőrzése a séma XML-érvényesítésének végrehajtásával. Ez a viselkedés segítséget nyújt a korábbi problémák észlelésében. A **biztonságos gépelési** lehetőség visszamenőleges kompatibilitáshoz érhető el, és csak a karakterlánc hosszát ellenőrzi. További információ a [biztonságos gépelési lehetőségről](#safe-typing).

   1. Ha elkészült, válassza a **Létrehozás**lehetőséget.

      Logic Apps beállítja és teszteli a kapcsolódást, hogy a kapcsolódás megfelelően működjön.

1. Most keresse meg és válasszon ki egy műveletet az SAP-kiszolgálóról.

   1. Az **SAP-művelet** mezőben válassza a mappa ikont. A fájl listában keresse meg és válassza ki a használni kívánt SAP-üzenetet. A lista a nyilak használatával navigálható.

      Ez a példa egy IDoc jelöl ki a **megrendelések** típusával.

      ![IDoc művelet keresése és kiválasztása](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Ha nem találja a kívánt műveletet, manuálisan is megadhat egy elérési utat, például:

      ![Adja meg manuálisan a IDoc művelet elérési útját](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Adja meg az **SAP-művelet** értékét a kifejezés-szerkesztőben. Így ugyanazt a műveletet használhatja a különböző típusú üzenetekhez.

      További információ a IDoc-műveletekről: [IDoc-műveletek üzenet-sémái](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Kattintson a **beviteli üzenet** szövegmezőbe, hogy megjelenjen a dinamikus tartalmak listája. A listából válassza ki a **törzs** MEZŐT a **http-kérelem fogadása**alatt.

      Ez a lépés a HTTP-kérelmi trigger törzsének tartalmát tartalmazza, és elküldi a kimenetet az SAP-kiszolgálónak.

      ![Válassza ki a "Body" tulajdonságot az triggerből](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Ha elkészült, az SAP-művelet a következő példához hasonlóan néz ki:

      ![Az SAP-művelet befejezése](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés**lehetőséget.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>HTTP-Response művelet hozzáadása

Most adjon hozzá egy válasz műveletet a logikai alkalmazás munkafolyamataihoz, és adja meg az SAP-művelet kimenetét. Így a logikai alkalmazás az SAP-kiszolgáló eredményeit visszaadja az eredeti kérelmezőnek.

1. A Logic app Designer SAP-művelet területén válassza az **új lépés**lehetőséget.

1. A keresőmezőbe írja be `response` szűrőként a kifejezést. A **műveletek** listából válassza a **Válasz**lehetőséget.

1. Kattintson a **törzs** mezőbe, hogy megjelenjen a dinamikus tartalmak listája. A listából válassza az **üzenet küldése az SAP**-be lehetőséget, majd a **törzs** mezőt.

   ![SAP-művelet végrehajtása](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Mentse a logikai alkalmazást.

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. Ha a logikai alkalmazás még nincs engedélyezve, a logikai alkalmazás menüjében válassza az **Áttekintés**lehetőséget. Az eszköztáron válassza az **Engedélyezés**lehetőséget.

1. A tervező eszköztárán válassza a **Futtatás**lehetőséget. Ez a lépés manuálisan elindítja a logikai alkalmazást.

1. Aktiválja a logikai alkalmazást egy HTTP POST-kérelem küldésével a HTTP-kérelemben megadott triggerben.
Adja meg az üzenet tartalmát a kérelemmel. A kérelem elküldéséhez olyan eszközt is használhat, mint például a [Poster](https://www.getpostman.com/apps).

   Ebben a cikkben a kérelem egy IDoc-fájlt küld, amelynek XML-formátumúnak kell lennie, és tartalmaznia kell az Ön által használt SAP-művelet névterét, például:

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. A HTTP-kérés elküldése után várjon a logikai alkalmazás válaszára.

   > [!NOTE]
   > Előfordulhat, hogy a logikai alkalmazás túllépi az időkorlátot, ha a válaszhoz szükséges összes lépés nem fejeződik be a [kérés időkorlátján](./logic-apps-limits-and-config.md)belül. Ha ez az állapot történik, előfordulhat, hogy a kérések le vannak tiltva. A problémák diagnosztizálásához megtudhatja, hogyan [ellenőrizheti és figyelheti a logikai alkalmazásokat](../logic-apps/monitor-logic-apps.md).

Ezzel létrehozott egy logikai alkalmazást, amely képes kommunikálni az SAP-kiszolgálóval. Most, hogy beállított egy SAP-csatlakozást a logikai alkalmazáshoz, megismerheti a többi elérhető SAP-műveletet, például a BAPI és az RFC-t is.

<a name="receive-from-sap"></a>

## <a name="receive-message-from-sap"></a>Üzenet fogadása az SAP-től

Ez a példa egy olyan logikai alkalmazást használ, amely akkor aktiválódik, amikor az alkalmazás egy SAP-rendszerből kap üzenetet.

### <a name="add-an-sap-trigger"></a>SAP-trigger hozzáadása

1. A Azure Portal hozzon létre egy üres logikai alkalmazást, amely megnyitja a Logic app designert.

1. A keresőmezőbe írja be `sap` szűrőként a kifejezést. Az **Eseményindítók** listából válassza ki, **hogy mikor érkezik üzenet az SAP-től**.

   ![SAP-trigger hozzáadása](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   Vagy válassza a **vállalat** lapot, majd válassza ki az triggert:

   ![SAP-trigger hozzáadása a vállalati lapról](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Ha a kapcsolat már létezik, folytassa a következő lépéssel, hogy beállítsa az SAP-műveletet. Ha azonban a rendszer megkéri a kapcsolat részleteinek megadását, adja meg az adatokat, hogy most létrehozza a kapcsolatát a helyszíni SAP-kiszolgálóval.

   1. Adja meg a kapcsolatok nevét.

   1. Az **adatátjáró** szakaszban az **előfizetés**területen válassza ki azt az átjáró-erőforráshoz tartozó Azure-előfizetést, amelyet az átjáró telepítéséhez Azure Portal hozott létre. 

   1. A **csatlakoztatási átjáró**területen válassza ki az átjáró erőforrását.

   1. Folytassa a kapcsolatok adatainak biztosítását. A **bejelentkezési típus** tulajdonságnál kövesse a lépést attól függően, hogy a tulajdonság az **Application Server** vagy a **Group**értékre van-e állítva:

      * Az **alkalmazáskiszolgáló**esetében ezek a tulajdonságok, amelyek általában opcionálisak, kötelezőek:

        ![SAP Application Server-kapcsolatok létrehozása](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * A **Group (csoport**) esetében ezek a tulajdonságok, amelyek általában opcionálisak, kötelezőek:

        ![SAP-üzenetkezelő kiszolgáló kapcsolatainak létrehozása](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Alapértelmezés szerint az erős beírással ellenőrizhető az érvénytelen értékek ellenőrzése a séma XML-érvényesítésének végrehajtásával. Ez a viselkedés segítséget nyújt a korábbi problémák észlelésében. A **biztonságos gépelési** lehetőség visszamenőleges kompatibilitáshoz érhető el, és csak a karakterlánc hosszát ellenőrzi. További információ a [biztonságos gépelési lehetőségről](#safe-typing).

   1. Ha elkészült, válassza a **Létrehozás**lehetőséget.

      Logic Apps beállítja és teszteli a kapcsolódást, hogy a kapcsolódás megfelelően működjön.

1. Adja meg a [szükséges paramétereket](#parameters) az SAP rendszerkonfigurációja alapján.

   Szükség esetén egy vagy több SAP-műveletet is megadhat. A műveletek listája azokat az üzeneteket határozza meg, amelyeket az indító az adatátjárón keresztül fogad az SAP-kiszolgálótól. Az üres lista azt adja meg, hogy az trigger megkapja az összes üzenetet. Ha a lista egynél több üzenetet tartalmaz, az trigger csak a listában megadott üzeneteket kapja meg. Az átjáró visszautasítja az SAP-kiszolgálóról küldött összes többi üzenetet.

   Kiválaszthat egy SAP-műveletet a file pickerből:

   ![SAP-művelet hozzáadása a logikai alkalmazáshoz](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Vagy manuálisan is megadhat egy műveletet:

   ![SAP-művelet manuális megadása](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Az alábbi példa bemutatja, hogyan jelenik meg a művelet, amikor beállítja, hogy a trigger egynél több üzenetet kapjon.

   ![Több üzenetet fogadó trigger – példa](media/logic-apps-using-sap-connector/example-trigger.png)

   Az SAP-művelettel kapcsolatos további információkért lásd: [IDOC-műveletek üzenet-sémái](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. Most mentse a logikai alkalmazást, így elkezdheti az SAP-rendszerből érkező üzenetek fogadását. A tervező eszköztárán válassza a **Mentés**lehetőséget.

A logikai alkalmazás most már készen áll az SAP-rendszerből érkező üzenetek fogadására.

> [!NOTE]
> Az SAP-trigger nem egy lekérdezési trigger, de egy webhook-alapú trigger helyette. Az triggert csak akkor hívja meg az átjáró, ha van ilyen üzenet, ezért nincs szükség lekérdezésre.

<a name="parameters"></a>

#### <a name="parameters"></a>Paraméterek

Az egyszerű karakterlánc-és szám típusú bemenetekkel együtt az SAP-összekötő a következő táblázatos paramétereket ( `Type=ITAB` bemeneteket) fogadja el:

* Táblázat irányának paraméterei, a bemenet és a kimenet is a régebbi SAP-kiadásokhoz.
* A paraméterek módosítása, amelyek lecserélik a tábla irányának paramétereit az újabb SAP-kiadásokra.
* Hierarchikus tábla paramétereinek

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. A logikai alkalmazás elindításához küldjön üzenetet az SAP-rendszerből.

1. A logikai alkalmazás menüjében válassza az **Áttekintés**lehetőséget. Tekintse át a logikai alkalmazás **futtatási előzményeit** .

1. Nyissa meg a legutóbbi futtatást, amely az SAP-rendszerből a trigger kimenetek szakaszban küldött üzenetet mutatja.

## <a name="receive-idoc-packets-from-sap"></a>IDOC-csomagok fogadása az SAP-ból

Beállíthatja az SAP-t, hogy [IDOCs küldjön a csomagokban](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/en-US/4ab38886549a6d8ce10000000a42189c.html), amelyek kötegek vagy IDOCs-csoportok. A IDOC-csomagok fogadásához az SAP-összekötőt, és különösen az triggert nem kell külön konfigurálni. Ahhoz azonban, hogy az trigger megkapja a csomagot, a IDOC-csomagok minden egyes elemét fel kell dolgozni, néhány további lépés szükséges a csomag különálló IDOCs való felosztásához.

Az alábbi példa bemutatja, hogyan lehet kinyerni az egyes IDOCs egy csomagból a következő [ `xpath()` függvény](./workflow-definition-language-functions-reference.md#xpath)használatával:

1. A Kezdés előtt egy SAP-triggerrel rendelkező logikai alkalmazásra van szükség. Ha még nem rendelkezik ezzel a logikai alkalmazással, az ebben a témakörben ismertetett lépéseket követve [beállíthat egy SAP-triggerrel rendelkező logikai alkalmazást](#receive-from-sap).

   Például:

   ![SAP-trigger hozzáadása a logikai alkalmazáshoz](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. Szerezze be a gyökér névteret a logikai alkalmazás által az SAP-től kapott XML-IDOC. A névtér XML-dokumentumból való kinyeréséhez adjon hozzá egy olyan lépést, amely létrehoz egy helyi karakterlánc-változót, és egy kifejezéssel tárolja a névteret `xpath()` :

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Gyökérszintű névtér beolvasása a IDOC](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Egy adott IDOC kinyeréséhez adjon hozzá egy olyan lépést, amely létrehoz egy tömböt változót, és egy másik kifejezéssel tárolja a IDOC gyűjteményt `xpath()` :

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Elemek tömbének beolvasása](./media/logic-apps-using-sap-connector/get-array.png)

   A Array változó lehetővé teszi, hogy minden IDOC elérhető legyen a logikai alkalmazás számára a gyűjteményre való enumerálással. Ebben a példában a logikai alkalmazás az egyes IDOC egy SFTP-kiszolgálóra továbbítja egy hurok használatával:

   ![IDOC küldése SFTP-kiszolgálónak](./media/logic-apps-using-sap-connector/loop-batch.png)

   Minden IDOC tartalmaznia kell a legfelső szintű névteret, ami azt okozza, hogy a fájl tartalma miért van becsomagolva egy `<Receive></Receive` elembe a legfelső szintű névtérrel együtt, mielőtt elküldi a IDOC az alsóbb rétegbeli alkalmazásba vagy SFTP-kiszolgálóra ebben az esetben.

A minta rövid útmutató sablonjának használatával új logikai alkalmazás létrehozásakor kiválaszthatja ezt a sablont a Logic app Designer alkalmazásban.

![Batch Logic app-sablon kiválasztása](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Sémák előállítása összetevők számára az SAP-ban

Ez a példa egy logikai alkalmazást használ, amely HTTP-kéréssel aktiválható. Az SAP-művelet kérést küld egy SAP-rendszernek, hogy létrehozza a sémákat a megadott IDoc és BAPI. A válaszban visszaadott sémákat az Azure Resource Manager-összekötő használatával kell feltölteni egy integrációs fiókba.

### <a name="add-an-http-request-trigger"></a>HTTP-kérelem triggerének hozzáadása

1. A Azure Portal hozzon létre egy üres logikai alkalmazást, amely megnyitja a Logic app designert.

1. A keresőmezőbe írja be `http request` szűrőként a kifejezést. Az **Eseményindítók** listából válassza ki, **hogy mikor érkezik HTTP-kérelem**.

   ![HTTP-kérési trigger hozzáadása](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Most mentse a logikai alkalmazást, hogy létrehozzon egy végponti URL-címet a logikai alkalmazáshoz.
A tervező eszköztárán válassza a **Mentés**lehetőséget.

   A végpont URL-címe most megjelenik az triggerben, például:

   ![URL-cím előállítása a végponthoz](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>SAP-művelet hozzáadása sémák létrehozásához

1. A Logic app Designerben az trigger alatt válassza az **új lépés**lehetőséget.

   ![Új lépés hozzáadása a logikai alkalmazáshoz](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. A keresőmezőbe írja be `sap` szűrőként a kifejezést. A **műveletek** listából válassza a **sémák előállítása**lehetőséget.
  
   !["Sémák előállítása" művelet hozzáadása a logikai alkalmazáshoz](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Vagy válassza a **vállalat** lapot, és válassza ki az SAP-műveletet.

   ![SAP-küldési művelet kiválasztása a vállalati lapon](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Ha a kapcsolat már létezik, folytassa a következő lépéssel, hogy beállítsa az SAP-műveletet. Ha azonban a rendszer megkéri a kapcsolat részleteinek megadását, adja meg az adatokat, hogy most létrehozza a kapcsolatát a helyszíni SAP-kiszolgálóval.

   1. Adja meg a kapcsolatok nevét.

   1. Az **adatátjáró** szakaszban az **előfizetés**területen válassza ki azt az átjáró-erőforráshoz tartozó Azure-előfizetést, amelyet az átjáró telepítéséhez Azure Portal hozott létre. 
   
   1. A **csatlakoztatási átjáró**területen válassza ki az átjáró erőforrását.

   1. Folytassa a kapcsolatok adatainak biztosítását. A **bejelentkezési típus** tulajdonságnál kövesse a lépést attól függően, hogy a tulajdonság az **Application Server** vagy a **Group**értékre van-e állítva:
   
      * Az **alkalmazáskiszolgáló**esetében ezek a tulajdonságok, amelyek általában opcionálisak, kötelezőek:

        ![SAP Application Server-kapcsolatok létrehozása](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * A **Group (csoport**) esetében ezek a tulajdonságok, amelyek általában opcionálisak, kötelezőek:

        ![SAP-üzenetkezelő kiszolgáló kapcsolatainak létrehozása](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Alapértelmezés szerint az erős beírással ellenőrizhető az érvénytelen értékek ellenőrzése a séma XML-érvényesítésének végrehajtásával. Ez a viselkedés segítséget nyújt a korábbi problémák észlelésében. A **biztonságos gépelési** lehetőség visszamenőleges kompatibilitáshoz érhető el, és csak a karakterlánc hosszát ellenőrzi. További információ a [biztonságos gépelési lehetőségről](#safe-typing).

   1. Ha elkészült, válassza a **Létrehozás**lehetőséget.

      Logic Apps beállítja és teszteli a kapcsolódást, hogy a kapcsolódás megfelelően működjön.

1. Adja meg annak az összetevőnek az elérési útját, amelyhez el szeretné készíteni a sémát.

   Kiválaszthatja az SAP-műveletet a file pickerből:

   ![SAP-művelet kiválasztása](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Vagy manuálisan is megadhatja a műveletet:

   ![SAP-művelet manuális megadása](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Több összetevőhöz tartozó sémák létrehozásához adja meg az egyes összetevőkre vonatkozó SAP-művelet részleteit, például:

   ![Válassza az új elem hozzáadása lehetőséget.](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Két elem megjelenítése](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Az SAP-művelettel kapcsolatos további információkért tekintse meg az [IDOC-műveletek üzenet-sémái](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)című témakört.

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés**lehetőséget.

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. A tervező eszköztárán válassza a **Futtatás** elemet a logikai alkalmazás futtatásának elindításához.

1. Nyissa meg a futtatást, és keresse meg a **sémák előállítása** művelet kimeneteit.

   A kimenetek megjelenítik a létrehozott sémákat az üzenetek adott listájához.

### <a name="upload-schemas-to-an-integration-account"></a>Sémák feltöltése integrációs fiókba

A létrehozott sémákat (például blob, Storage vagy integrációs fiók) is letöltheti vagy tárolhatja a tárházban. Az integrációs fiókok minden más XML-művelettel rendelkeznek, ezért ez a példa azt mutatja be, hogyan tölthet fel sémákat egy integrációs fiókba ugyanahhoz a logikai alkalmazáshoz az Azure Resource Manager-összekötő használatával.

1. A Logic app Designerben az trigger alatt válassza az **új lépés**lehetőséget.

1. A keresőmezőbe írja be `Resource Manager` szűrőként a kifejezést. Válassza **az erőforrás létrehozása vagy frissítése**lehetőséget.

   ![Azure Resource Manager művelet kiválasztása](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Adja meg a művelet részleteit, beleértve az Azure-előfizetést, az Azure-erőforráscsoportot és az integrációs fiókot. Ha SAP-tokeneket szeretne hozzáadni a mezőkhöz, kattintson a mezők mezőire, és válassza ki a megjelenő dinamikus tartalom listából.

   1. Nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a **hely** és a **Tulajdonságok** mezőket.

   1. Adja meg az új mezők részleteit az ebben a példában látható módon.

      ![Adja meg Azure Resource Manager művelet részleteit](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   Az SAP- **létrehozási sémák** művelet gyűjteményként hoz létre sémákat, így a tervező automatikusan hozzáadja az **egyes** hurkokat a művelethez. Az alábbi példa bemutatja, hogyan jelenik meg ez a művelet:

   ![Azure Resource Manager művelet az "for each" ciklussal](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > A sémák Base64 kódolású formátumot használnak. A sémák integrációs fiókba való feltöltéséhez a függvény használatával dekódolni kell őket `base64ToString()` . Az alábbi példa az elem kódját mutatja be `"properties"` :
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés**lehetőséget.

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. A tervező eszköztárán válassza a **Futtatás** lehetőséget a logikai alkalmazás manuális elindításához.

1. Sikeres Futtatás után lépjen az integrációs fiókra, és győződjön meg arról, hogy a létrehozott sémák léteznek.

## <a name="enable-secure-network-communications"></a>Biztonságos hálózati kommunikáció engedélyezése

Mielőtt elkezdené, győződjön meg arról, hogy teljesítette a korábban felsorolt [előfeltételeket](#pre-reqs):

* A helyszíni adatátjáró olyan gépre van telepítve, amely az SAP-rendszerrel megegyező hálózaton van.
* Az SSO esetében az átjáró egy SAP-felhasználóhoz hozzárendelt felhasználóként fut.
* A további biztonsági funkciókat biztosító SNC-függvénytár ugyanarra a gépre van telepítve, mint az adatátjáró. Ilyenek például a [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), a Kerberos és az NTLM.

   Ha engedélyezni szeretné a SNC-t az SAP-rendszerből vagy azokból érkező kérésekhez, jelölje be az SAP-kapcsolatban a **Snc használata** jelölőnégyzetet, és adja meg a következő tulajdonságokat:

   ![Az SAP SNC konfigurálása a kapcsolatban](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Tulajdonság | Leírás |
   |----------| ------------|
   | **SNC-könyvtár elérési útja** | A SNC-könyvtár neve vagy elérési útja NKH-telepítési helyhez vagy abszolút elérési úthoz viszonyítva. Ilyenek például a következők: `sapsnc.dll` vagy `.\security\sapsnc.dll` `c:\security\sapsnc.dll` . |
   | **SNC SSO** | Ha a-t a SNC-n keresztül kapcsolódik, a rendszer általában a-hívó hitelesítésére használja a SNC-identitást. Egy másik lehetőség, hogy felülbírálja a felhasználó és a jelszó információit a hívó hitelesítéséhez, de a sor továbbra is titkosítva van. |
   | **SNC nevem** | A legtöbb esetben ez a tulajdonság nem hagyható el. A telepített SNC-megoldás általában ismeri a saját SNC-nevét. Csak a több identitást támogató megoldások esetében lehet, hogy meg kell adnia az adott célhelyhez vagy kiszolgálóhoz használni kívánt identitást. |
   | **SNC-partner neve** | A háttér-végpont neve. |
   | **SNC-védelem minősége** | Az adott cél vagy kiszolgáló SNC-kommunikációjához használt szolgáltatás minősége. Az alapértelmezett értéket a háttérrendszer határozza meg. A maximális értéket a SNC-hez használt biztonsági termék határozza meg. |
   |||

   > [!NOTE]
   > Ne állítsa be a környezeti változókat SNC_LIB és SNC_LIB_64 azon a gépen, amelyen az adatátjáró és a SNC-könyvtár található. Ha be van állítva, elsőbbséget élveznek az összekötőn átadott SNC-függvénytár értékével szemben.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Biztonságos gépelés

Alapértelmezés szerint az SAP-kapcsolatok létrehozásakor a rendszer erős beírással ellenőrzi az érvénytelen értékeket a séma XML-érvényesítésének végrehajtásával. Ez a viselkedés segítséget nyújt a korábbi problémák észlelésében. A **biztonságos gépelési** lehetőség visszamenőleges kompatibilitáshoz érhető el, és csak a karakterlánc hosszát ellenőrzi. Ha a **biztonságos gépelés**lehetőséget választja, a dats típusa és a Tims típus az SAP-ban karakterláncként lesz kezelve, nem pedig az XML-megfelelő, `xs:date` és `xs:time` ahol `xmlns:xs="http://www.w3.org/2001/XMLSchema"` . A biztonságos gépelés hatással van a séma összes generációjának viselkedésére, az "elküldött" adattartalomra és a "kapott" válaszra, valamint az aktiválásra vonatkozó üzenet küldésére. 

Ha erős gépelés van használatban (a**biztonságos gépelés** nincs engedélyezve), a séma a dats és a Tims típusait egyszerűbb XML-típusokra képezi le:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Ha erős gépeléssel küld üzeneteket, a DATS és a TIMS válasza megfelel a megfelelő XML-típus formátumának:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Ha a **biztonságos gépelés** engedélyezve van, a séma leképezi a dats és a Tims típusú XML-karakterlánc-mezőket csak hosszúsági korlátozásokkal, például:

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

Ha az üzenetek **biztonságos gépeléssel** való küldése engedélyezve van, a dats és a Tims válasz a következő példához hasonlít:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>Speciális forgatókönyvek

### <a name="confirm-transaction-explicitly"></a>Tranzakció explicit megerősítése

Ha Logic Appsről küld tranzakciókat az SAP-nak, ez az Exchange két lépésben történik az SAP-dokumentumban, a [tranzakciós RFC-kiszolgáló programjaiban](https://help.sap.com/doc/saphelp_nwpi71/7.1/en-US/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true)leírtak szerint. Alapértelmezés szerint a **Küldés az SAP-be** művelet a függvények átvitelének lépéseit és a tranzakciós megerősítést egyetlen hívásban kezeli. Az SAP-összekötő lehetővé teszi, hogy leválasztsa ezeket a lépéseket. IDOC küldhet, és nem lehet automatikusan megerősíteni a tranzakciót, a explicit tranzakció- **azonosító megerősítése** műveletet használhatja.

Ez a tranzakció-azonosító megerősítése leválasztásának lehetősége akkor hasznos, ha nem szeretne ismétlődő tranzakciókat használni az SAP-ban, például olyan helyzetekben, ahol a hibák oka lehet például a hálózati problémák miatt. A tranzakció-azonosító külön megerősítésével a tranzakció csak egyszer lesz végrehajtva az SAP-rendszeren.

Az alábbi példa a következő mintát mutatja be:

1. Hozzon létre egy üres logikai alkalmazást, és adjon hozzá egy HTTP-triggert.

1. Az SAP-összekötőben adja hozzá a **Send IDOC** műveletet. Adja meg az SAP-rendszernek küldött IDOC adatait.

1. Ha explicit módon szeretné megerősíteni a tranzakció AZONOSÍTÓját egy külön lépésben, a **TID megerősítése** mezőben válassza a **nem**lehetőséget. A nem kötelező **tranzakció-azonosító GUID-azonosítója** mezőben manuálisan is megadhatja az értéket, vagy az összekötő automatikusan létrehozhatja és visszaküldheti ezt a GUID azonosítót a Send IDOC művelet válaszában.

   ![IDOC művelet tulajdonságainak küldése](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. A tranzakció AZONOSÍTÓjának explicit megerősítéséhez adja hozzá a **tranzakció-azonosító megerősítése** műveletet. Kattintson a **tranzakció-azonosító** mezőbe, hogy megjelenjen a dinamikus tartalmak listája. Ebből a listából válassza ki a **tranzakció-azonosító** értékét, amelyet a **Send IDOC** művelet adott vissza.

   ![Tranzakció-azonosító művelet megerősítése](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   A lépés futtatása után az aktuális tranzakció mindkét végén meg van jelölve, az SAP-összekötő oldalon és az SAP-rendszer oldalon.

## <a name="known-issues-and-limitations"></a>Ismert problémák és korlátozások

Az SAP-összekötő jelenleg ismert problémái és korlátai:

* Az SAP-trigger nem támogatja az adatátjáró-fürtöket. Bizonyos feladatátvételi esetekben az SAP-rendszerrel kommunikáló adatátjáró-csomópont eltérhet az aktív csomóponttól, ami váratlan viselkedést eredményez. A küldési forgatókönyvek esetében az adatátjáró-fürtök támogatottak.

* Az SAP-összekötő jelenleg nem támogatja az SAP útválasztó karakterláncait. A helyszíni adatátjárónak ugyanazon a helyi hálózaton kell lennie, mint a csatlakozni kívánó SAP-rendszernek.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő részletes technikai részleteiről, például az eseményindítók, a műveletek és a korlátok az összekötő hencegő fájljában leírtak alapján: az [összekötő hivatkozási lapja](https://docs.microsoft.com/connectors/sap/).

> [!NOTE]
> Az [integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)logikai alkalmazásai esetében az összekötő ISE által címkézett verziója az [ISE-üzenetek korlátait](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) használja helyette.

## <a name="next-steps"></a>További lépések

* [Kapcsolódjon a helyi rendszerekhez](../logic-apps/logic-apps-gateway-connection.md) Azure Logic Appsról.
* Megtudhatja, hogyan érvényesítheti, átalakíthatja és használhatja más üzenetkezelési műveleteket a [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).
* További [Logic apps összekötők](../connectors/apis-list.md)megismerése.
