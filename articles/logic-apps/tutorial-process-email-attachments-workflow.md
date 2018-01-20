---
title: "Munkafolyamatok feldolgozása, e-mailek és mellékletek - Azure Logic Apps |} Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogyan automatizált munkafolyamatok feldolgozása, e-mailek és mellékletek Azure Logic Apps, az Azure Storage és az Azure Functions létrehozása"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.author: LADocs; estfan
ms.openlocfilehash: 210731ce2e792452650b7a92cfc542c78a0e8014
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="process-emails-and-attachments-with-a-logic-app"></a>Feldolgozása, e-maileket és mellékleteket logikai alkalmazás

Az Azure Logic Apps segítségével munkafolyamatainak automatizálásához és adatok integrálása az Azure-szolgáltatások, a Microsoft-szolgáltatások, a más szoftver-,--szolgáltatás (SaaS) alkalmazások között, és a helyszíni rendszereket. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy [logikai alkalmazás](../logic-apps/logic-apps-overview.md) , amely kezeli a bejövő e-maileket és mellékleteket. A logic app folyamatok ezt a tartalmat a tartalom mentése az Azure storage, és a tartalom megtekintésével értesítéseket küld. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítson be [az Azure storage](../storage/common/storage-introduction.md) Tártallózó ellenőrzése a következő, a mentett e-maileket és mellékleteket.
> * Hozzon létre egy [Azure függvény](../azure-functions/functions-overview.md) HTML e-mailt, amely eltávolítja. Ez az oktatóanyag a kódot, amely is használhatja ezt a függvényt tartalmaz.
> * Üres logikai alkalmazás létrehozása.
> * Adjon hozzá egy eseményindítót figyelő e-mailekhez tartozó mellékletek.
> * Adjon hozzá olyan feltétel, amely ellenőrzi, hogy e-mailek rendelkeznek-e mellékleteket.
> * Az Azure függvényt hívja, ha az e-mail mellékletek művelet hozzáadása.
> * Adja hozzá az e-mailek és mellékletek storage blobsba létrehozó művelet.
> * Új, hogy küld e-mail értesítések művelet.

Amikor elkészült, a logikai alkalmazás néz ki a munkafolyamat magas szinten:

![Magas szintű végzett logikai alkalmazás](./media/tutorial-process-email-attachments-workflow/overview.png)

Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztrálhat egy ingyenes Azure-fiókra</a> az eljárás megkezdése előtt. 

## <a name="prerequisites"></a>Előfeltételek

* Az e-mail-fiók egy e-mailt provider Logic Apps, például az Office 365 Outlook, Outlook.com-os vagy Gmailes által támogatott. A más szolgáltatók [tekintse át az összekötők listán Itt](https://docs.microsoft.com/connectors/).

  A logikai alkalmazás egy Office 365 Outlook-fiókot használ. 
  Ha egy másik e-mail fiókot használja, az általános lépéseket ugyanaz maradjon, de a felhasználói felület némileg eltérő jelenhet meg.

* Töltse le és telepítse a <a href="http://storageexplorer.com/" target="_blank">ingyenes Microsoft Azure Tártallózó</a>. Az eszköz segítségével ellenőrizze, hogy a tároló helyesen van beállítva.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be a <a href="https://portal.azure.com" target="_blank">Azure-portálon</a> a Azure-fiók hitelesítő adataival.

## <a name="set-up-storage-to-save-attachments"></a>Menti a mellékleteket tárolás beállítása

Mentheti a bejövő e-mailek és mellékletek blobot, amely egy [az Azure storage-tároló](../storage/common/storage-introduction.md). 

1. A tároló létrehozása előtt [hozzon létre egy tárfiókot](../storage/common/storage-create-storage-account.md#create-a-storage-account) ezekkel a beállításokkal:

   | Beállítás | Érték | Leírás | 
   | ------- | ----- | ----------- | 
   | **Name (Név)** | attachmentstorageacct | A tárfiók neve | 
   | **Telepítési modell** | Erőforrás-kezelő | A [telepítési modell](../azure-resource-manager/resource-manager-deployment-model.md) kezeléséhez az erőforrások telepítése | 
   | **Fiók típusa** | Általános célú | A [tárfiók típusa](../storage/common/storage-introduction.md#types-of-storage-accounts) | 
   | **Teljesítmény** | Standard | Ezzel a beállítással a támogatott adattípusokat és adathordozó adatainak tárolásához. Lásd: [típusú tárfiókkal](../storage/common/storage-introduction.md#types-of-storage-accounts). | 
   | **Replikáció** | Helyileg redundáns tárolás (LRS) | Ez a beállítás határozza meg, hogyan az adatok másolása, tárolt, felügyelt, és szinkronizálva. Lásd: [replikációs](../storage/common/storage-introduction.md#replication). | 
   | **Szükséges biztonságos átvitele** | Letiltva | Ezzel a beállítással a kapcsolatok érkező kéréseket szükséges biztonsági. Lásd: [biztonságos átvitelét igénylő](../storage/common/storage-require-secure-transfer.md). | 
   | **Előfizetés** | <*your-Azure-subscription-name*> | Az Azure-előfizetés nevét | 
   | **Erőforráscsoport** | LA-oktatóanyag – RG | A nevet a [Azure erőforráscsoport](../azure-resource-manager/resource-group-overview.md) rendszerezése és felügyelhetők a kapcsolódó erőforrások. <p>**Megjegyzés:** belül egy adott területre létező egy erőforráscsoportot. Bár az elemeket az oktatóanyag nem feltétlenül érhető el minden régióban, próbálja meg ugyanabban a régióban amikor lehetséges. | 
   | **Hely** | USA 2. keleti régiója | A régió a tárfiók adatait tárolására szolgáló | 
   | **Virtuális hálózatok konfigurálása** | Letiltva | Ebben az oktatóanyagban tartsa a **letiltott** beállítást. | 
   |||| 

   Is [Azure PowerShell](../storage/common/storage-quickstart-create-storage-account-powershell.md) vagy [Azure CLI](../storage/common/storage-quickstart-create-storage-account-cli.md).
  
2. Azure storage-fiók telepíti, miután a tárfiók hozzáférési kulcs lekérése:

   1. A tároló fiók menü alatti **beállítások**, válassza a **hívóbetűk**. 
   2. Található **key1** alatt **kulcsok alapértelmezett** és a tárfiók nevére.

      ![Másolja ki és mentse a tárfiók nevét és a kulcs](./media/tutorial-process-email-attachments-workflow/copy-save-storage-name-key.png)

   Is [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccountkey) vagy [Azure CLI](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest.md#az_storage_account_keys_list). 

3. Hozzon létre az e-mail mellékletek tárolót.
   
   1. A tárolási fiók menüjében a a **áttekintése** ablaktáblában válassza **Blobok** alatt **szolgáltatások**, majd válassza **+ tároló**.

   2. Adja meg a tároló neve "mellékletek". A **nyilvános hozzáférési szint**, jelölje be **tárolóban (a tárolók és blobok névtelen olvasási hozzáférés)**, és válassza a **OK**.

   Is [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer), vagy [Azure CLI](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az_storage_container_create). 
   Amikor elkészült, a tároló tárfiókba itt az Azure portálon található:

   ![A tároló kész](./media/tutorial-process-email-attachments-workflow/created-storage-container.png)

A következő Tártallózó csatlakozni a tárfiókhoz.

## <a name="set-up-storage-explorer"></a>A Tártallózó beállítása

Tártallózó most, csatlakozni a tárfiókhoz, így ellenőrizheti, hogy a logikai alkalmazás megfelelően menti a mellékleteket, a tárolási tárolóban lévő blobok.

1. Nyissa meg a Microsoft Azure Tártallózó. Ha a Tártallózó kéri az Azure storage egy kapcsolathoz, válassza **használja a tárfiók nevét és a kulcs** > **következő**.
Ha nincs parancssor jelenik meg, válassza a **fiók hozzáadása** explorer eszköztár.

2. A **név és kulcs segítségével**, adja meg a tárfiók nevét és a korábban mentett elérési kulcsot. Válasszon **következő** > **csatlakozás**.

3. Ellenőrizze, hogy a tárfiók és tároló megfelelően jelennek meg a Tártallózó alkalmazással:

   1. A **Explorer**, bontsa ki a **(helyi és kapcsolódó)** > 
    **Tárfiókok** > **attachmentstorageaccount** > 
    **Blob-tárolók**.

   2. Győződjön meg arról, hogy megjelenik-e már a "mellékletek" tárolóban. 
   Példa:

      ![A Tártallózó - tároló megerősítése](./media/tutorial-process-email-attachments-workflow/storage-explorer-check-contianer.png)

Ezután hozzon létre egy [Azure függvény](../azure-functions/functions-overview.md) HTML, amely eltávolítja a bejövő e-mailt.

## <a name="create-a-function-to-clean-html"></a>Hozzon létre egy függvényt HTML törlése

Mostantól a kódrészletet, ezeket a lépéseket által biztosított használatával hozzon létre egy Azure függvényt, amely HTML eltávolítja az összes bejövő e-mailben. Ily módon az e-mail tartalma tisztító és könnyebben folyamat. Ez a függvény a logic App majd hívható.

1. Egy függvény létrehozása előtt [függvény-alkalmazás létrehozása](../azure-functions/functions-create-function-app-portal.md) ezekkel a beállításokkal:

   | Beállítás | Érték | Leírás | 
   | ------- | ----- | ----------- | 
   | **Alkalmazás neve** | CleanTextFunctionApp | A függvény alkalmazás globálisan egyedi és leíró nevét | 
   | **Előfizetés** | <*your-Azure-subscription-name*> | Az azonos Azure-előfizetéshez előzőleg használt | 
   | **Erőforráscsoport** | LA-oktatóanyag – RG | Az azonos Azure erőforráscsoport, amelyet korábban használt | 
   | **Üzemeltetési terv** | Használatalapú csomag | Ez a beállítás azt határozza meg, lefoglalása és a skála erőforrások, például a számítási teljesítmény, a függvény alkalmazás futtatására. Lásd: [üzemeltető tervek összehasonlító](../azure-functions/functions-scale.md). | 
   | **Hely** | USA 2. keleti régiója | Ugyanabban a régióban, amelyet korábban használt | 
   | **Storage** | cleantextfunctionstorageacct | Hozzon létre egy tárfiókot, a függvény alkalmazás. Csak kisbetűket és számokat használja. <p>**Megjegyzés:** ezt a tárfiókot tartalmazza a függvény alkalmazásokat, és a korábban létrehozott tárfiók e-mail mellékletekhez eltér. | 
   | **Application Insights** | Ki | Bekapcsolja az alkalmazásfigyelés [Application Insights](../application-insights/app-insights-overview.md), ebben az oktatóanyagban tartani, de a **ki** beállítást. | 
   |||| 

   A függvény alkalmazás automatikusan nem indul el a telepítés utáni, ha az alkalmazást a Keresés a <a href="https://portal.azure.com" target="_blank">Azure-portálon</a>. A fő Azure menüben válasszon **alkalmazásszolgáltatások**, és válassza ki azt a függvény alkalmazást.

   ![Létrehozott függvény alkalmazás](./media/tutorial-process-email-attachments-workflow/function-app-created.png)

   Ha **alkalmazásszolgáltatások** nem jelenik meg az Azure menüben, írja be a **további szolgáltatások** helyette. Keresse meg a keresési mezőbe, és válassza ki **függvény alkalmazások**. További információkért lásd: [az függvény létrehozása](../azure-functions/functions-create-first-azure-function.md).

   Is [Azure CLI](../azure-functions/functions-create-first-azure-function-azure-cli.md), vagy [PowerShell és a Resource Manager sablonok](../azure-resource-manager/resource-group-template-deploy.md).

2. A **függvény alkalmazások**, bontsa ki a **CleanTextFunctionApp**, és válassza ki **funkciók**. A funkciók eszköztáron válassza **+ új függvény**.

   ![Új függvény létrehozása](./media/tutorial-process-email-attachments-workflow/function-app-new-function.png)

3. A **válassza ki a sablont, az alábbi vagy keresse fel a gyors üzembe helyezés**, jelölje be a **HttpTrigger - C#** függvény sablont.

   ![Válassza ki a függvény sablon](./media/tutorial-process-email-attachments-workflow/function-select-httptrigger-csharp-function-template.png)

4. A **a függvény neve**, adja meg ```RemoveHTMLFunction```. A **HTTP-eseményindítóval** > **jogosultsági szint**, tartsa meg az alapértelmezett **függvény** értékét, és válassza a **létrehozása**.

   ![Adjon nevet a függvénynek](./media/tutorial-process-email-attachments-workflow/function-provide-name.png)

5. Után megnyílik a szerkesztő, ezzel a kóddal, amely eltávolítja a HTML-KÓDBAN, és eredményt ad vissza. a hívónak cserélje le a sablon kódot:

   ``` CSharp
   using System.Net;
   using System.Text.RegularExpressions;

   public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
   {
      log.Info($"HttpWebhook triggered");

      // Parse query parameter
      string emailBodyContent = await req.Content.ReadAsStringAsync();

      // Replace HTML with other characters
      string updatedBody = Regex.Replace(emailBodyContent, "<.*?>", string.Empty);
      updatedBody = updatedBody.Replace("\\r\\n", " ");
      updatedBody = updatedBody.Replace(@"&nbsp;", " ");

      // Return cleaned text
      return req.CreateResponse(HttpStatusCode.OK, new { updatedBody });

   }
   ```

6. Amikor elkészült, válassza ki a **mentése**. A függvény teszteléséhez válassza **tesztelése** alatt nyílra (**<**) ikonra a szerkesztő jobb szélén. 

   ![A "Test" ablaktábla megnyitása](./media/tutorial-process-email-attachments-workflow/function-choose-test.png)

7. Az a **teszt** ablaktáblán, a **kérelem törzse**, adja meg ezt a sort, és válassza a **futtassa**.

   ```json
   {"name": "<p><p>Testing my function</br></p></p>"}
   ```

   ![A függvény tesztelése](./media/tutorial-process-email-attachments-workflow/function-run-test.png)

   A **kimeneti** ablakban látható, a függvény ennek:

   ```json
   {"updatedBody":"{\"name\": \"Testing my function\"}"}
   ```

Miután ellenőrizte, hogy működik-e a funkció, a logikai alkalmazás létrehozása. Bár ez az oktatóanyag bemutatja, hogyan hozzon létre egy függvényt, amely eltávolítja HTML e-mailek, az a Logic Apps is rendelkezik egy **HTML, Text** összekötő.

## <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

1. A fő Azure menüben válasszon **új** > **vállalati integrációs** > **logikai alkalmazás**.

   ![Logikai alkalmazás létrehozása](./media/tutorial-process-email-attachments-workflow/create-logic-app.png)

2. A **hozzon létre logikai alkalmazás**, ez a logikai alkalmazás látható és ismertetett ismertetik. Ha elkészült, válassza a **Rögzítés az irányítópulton** > **Létrehozás** lehetőséget.

   ![Adja meg a logic app információk](./media/tutorial-process-email-attachments-workflow/create-logic-app-settings.png)

   | Beállítás | Érték | Leírás | 
   | ------- | ----- | ----------- | 
   | **Name (Név)** | LA-ProcessAttachment | A logikai alkalmazás nevét | 
   | **Előfizetés** | <*your-Azure-subscription-name*> | Az azonos Azure-előfizetéshez előzőleg használt | 
   | **Erőforráscsoport** | LA-oktatóanyag – RG | Az azonos Azure erőforráscsoport, amelyet korábban használt |
   | **Hely** | USA 2. keleti régiója | Ugyanabban a régióban, amelyet korábban használt | 
   | **Log Analytics** | Ki | Ebben az oktatóanyagban tartsa a **ki** beállítást. | 
   |||| 

3. Miután Azure telepíti az alkalmazást, a Logic Apps Designer megnyílik, és egy bevezető videó és sablonok közös logic app mintákat egy lapját jeleníti meg. A **Sablonok** területen válassza az **Üres logikai alkalmazás** elemet.

   ![Üres logikaialkalmazás-sablon kiválasztása](./media/tutorial-process-email-attachments-workflow/choose-logic-app-template.png)

Ezután adja hozzá a [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts) , amely figyeli a bejövő e-mailek, amelyek rendelkeznek a mellékleteket. Minden logikai alkalmazás egy eseményindítót, amely akkor fordul elő, amikor egy adott esemény következik be, vagy amikor új adatok megfelel-e egy adott feltétel kell kezdődnie. További információkért lásd: [az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="monitor-incoming-email"></a>A bejövő e-mailek figyelése

1. A Tervező adja meg "érkezésekor e-mail" be a keresőmezőbe. Válassza ki az ehhez az eseményindítóhoz az e-mailt Provider:  **< *az e-mailt provider*> – Ha egy új e-mailek megérkeznek**, például:

   ![Válassza ki az e-mailt Provider eseményindító: "Egy új e-mailt fogadásakor"](./media/tutorial-process-email-attachments-workflow/add-trigger-when-email-arrives.png)

   * Munkahelyi vagy iskolai Azure-fiókok esetében válassza az Office 365 Outlookot. 
   * Személyes Microsoft-fiókok esetében válassza az Outlook.com-összekötőt. 

2. Ha megkérdezi, hogy a hitelesítő adatokat, jelentkezzen be az e-mail fiókjába, hogy a Logic Apps csatlakozhassanak az e-mail fiókjába.

3. Most adja meg a feltételeket, amelyek az eseményindító új e-mailek szűrése használja.

   1. Adja meg a mappát, időköz és az e-mailek ellenőrzéséhez gyakoriságának.

      ![Adja meg a mappa, időköz és az üzenetek ellenőrzése a következő gyakorisága](./media/tutorial-process-email-attachments-workflow/set-up-email-trigger.png)

      | Beállítás | Érték | Leírás | 
      | ------- | ----- | ----------- | 
      | **Folder** | Beérkezett fájlok | Az e-mail mappa ellenőrzése | 
      | **Időköz** | 1 | Ellenőrzések között intervallumok száma | 
      | **Gyakoriság** | Perc | Ellenőrzi a egysége minden közötti távolság | 
      |  |  |  | 
  
   2. Válasszon **speciális beállítások megjelenítése** , és adja meg ezeket a beállításokat:

      | Beállítás | Érték | Leírás | 
      | ------- | ----- | ----------- | 
      | **Melléklet tartozik** | Igen | Csak e-mailek mellékleteit beolvasása. <p>**Megjegyzés:** az eseményindító nem minden e-mailek eltávolítása a fiók ellenőrzése csak az új üzeneteket, és csak e-maileket a tulajdonos szűrőnek feldolgozása. | 
      | **Mellékletek** | Igen | A mellékleteket bemenetként lekérése a munkafolyamathoz, nem pedig csak keressen a mellékleteket. | 
      | **Tulajdonos szűrő** | ```Business Analyst 2 #423501``` | Az e-mail tárgyában található szöveg | 
      |  |  |  | 

4. Az eseményindító részletek most elrejtéséhez kattintson az eseményindító címsorában belül.

   ![Részletek elrejtéséhez alakzat összecsukása](./media/tutorial-process-email-attachments-workflow/collapse-trigger-shape.png)

5. Mentse a Logic Apps alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot.

   A Logic Apps alkalmazást most élő azonban nem bármi más ellenőrizze az az e-maileket. 
   Ezután adja hozzá az olyan feltétel, amely meghatározza a munkafolyamat folytatásához feltételeket.

## <a name="check-for-attachments"></a>Mellékletek ellenőrzése

1. Válassza ki az eseményindító **+ új lépés** > **feltétel hozzáadása**.

   Ha a feltétel alakzat, alapértelmezés szerint jelenik meg, vagy a listában, vagy a dinamikus tartalom listába jelenik meg, és jeleníti meg az előző lépésben munkafolyamat bemeneteként felvehető paramétereket. 
   A böngésző szélesség határozza meg, melyik listája jelenik meg.

2. Nevezze át a feltételt, a jobb leírását.

   1. Válassza ki a feltétel címsoron **folytatást jelző pontokra** (**...** ) gomb > **átnevezése**.

      Ha például a böngésző keskeny nézetben van:

      ![Nevezze át a feltétel](./media/tutorial-process-email-attachments-workflow/condition-rename.png)

      Ha a böngésző széles nézetben, és a dinamikus tartalom listába blokkolja a hárompontozást jelző gombra való hozzáférést, zárja be a lista kiválasztásával **dinamikus tartalom hozzáadása az** belül a feltétel. 
      
      ![Zárja be a dinamikus tartalom listába](./media/tutorial-process-email-attachments-workflow/close-dynamic-content-list.png)

   2. Nevezze át a leírást a feltételei:```If email has attachments and key subject phrase```

3. A feltétel leírása kifejezés megadásával. 

   1. Válassza ki a feltétel alakzaton belül **speciális módban szerkesztése**.

      ![Speciális módban feltétel szerkesztése](./media/tutorial-process-email-attachments-workflow/edit-advanced-mode.png)

   2. A szövegmezőben adja meg az ebben a kifejezésben:

      ```@equals(triggerBody()?['HasAttachment'], bool('true'))```

      Ebben a kifejezésben összehasonlítja a **HasAttachment** tulajdonság értéke az eseményindító szervezet, amely az e-mailt ebben az oktatóanyagban objektummal hívja meg a logikai ```True```. 
      Ha mindkét értékei megegyeznek, az e-mailt rendelkezik legalább egy mellékletet, a feltétel folyamaton, és a munkafolyamat továbbra is fennáll.

      A feltétel most néz ki ebben a példában:

      ![A Feltételkifejezés](./media/tutorial-process-email-attachments-workflow/condition-expression.png)

   3. Válasszon **alapvető módban szerkesztése**. A kifejezés most oldja fel, ahogy az itt látható:

      ![Megoldott kifejezés](./media/tutorial-process-email-attachments-workflow/condition-expression-resolved.png)

      > [!NOTE]
      > Manuális elkészítése kifejezés, alapszintű módban működik, és a dinamikus listáján, nyissa meg, hogy a Kifejezésszerkesztő dolgozhat. A **kifejezés**, kiválaszthatja a funkciók. A **dinamikus tartalom**, választhat olyan feladatokat használandó paraméter mezőket.
      > Ez az oktatóanyag később bemutatja, hogyan manuálisan a kifejezések felépítéséhez létre.

4. Mentse a Logic Apps alkalmazást.

### <a name="test-your-condition"></a>A feltétel tesztelése

Most megállapítására, hogy megfelelően működik, a következő feltételt:

1. Ha a logikai alkalmazás már nem fut, válassza ki a **futtatása** a designer eszköztáron.

   Ebben a lépésben kézzel Várjon, amíg a megadott intervallumban továbbítja nélkül indítja el a Logic Apps alkalmazást. 
   Azonban semmi nem történik, amíg a tesztelő e-mailt a beérkezett érkezik. 

2. Küldhet magának, egy e-mailt, amely megfelel a feltételeknek:

   * Az e-mail tárgyát rendelkezik a szöveg, amelyet az eseményindítóhoz megadott **tulajdonos szűrő**:```Business Analyst 2 #423501```

   * Az e-maileket rendelkezik egy mellékletet. 
   Most hozzon létre egy üres szöveges fájlt, és csatolja a fájlt a e-mailhez.

   Ha az e-mailek megérkeznek, a logikai alkalmazás ellenőrzi mellékletek és a megadott tulajdonos szöveg.
   Ha a feltétel megfelelt, az eseményindító következik be, és hozzon létre egy logic app-példány és a munkafolyamat elindításához a Logic Apps motort. 

3. Ellenőrizze, hogy az eseményindító következik be, és a logikai alkalmazás sikeresen lefutott, a logic app menüben válassza a **áttekintése**.

   ![Eseményindító és futtatása előzmények ellenőrzése](./media/tutorial-process-email-attachments-workflow/checkpoint-run-history.png)

   Ha a logikai alkalmazás nem indítható el, vagy annak ellenére, hogy egy sikeres eseményindító futtassa, lásd: [hibaelhárítása a Logic Apps alkalmazást](../logic-apps/logic-apps-diagnosing-failures.md).

Ezt követően adja meg az elvégzendő műveleteket a **igaz értéke esetén** ág. Mentse az e-mailek mellékleteit együtt, HTML-eltávolítja az e-mail törzsének, majd blobok létrehozni a tárolót az e-mailek és mellékletek.

> [!NOTE]
> A Logic Apps alkalmazást semmit nem kell a **hamis** fiókirodai, amikor az e-mail mellékletek nincs. Egy rendkívüli gyakorlat szerint ez az oktatóanyag befejezése után is hozzáadhat a megfelelő műveletet, amelyet meg szeretne tartani, amíg a a **hamis** ág.

## <a name="call-the-removehtmlfunction"></a>A RemoveHTMLFunction hívása

1. A logic app menüben válasszon **Logic App Designer**. Az a **igaz értéke esetén** fiókirodai, válassza a **művelet hozzáadása**.

2. "Az azure functions", és adja meg a műveletet: **Azure Functions – válasszon egy Azure függvény**

   ![Válassza ki a művelet "Az Azure Functions - válasszon egy Azure függvény"](./media/tutorial-process-email-attachments-workflow/add-action-azure-function.png)

3. Válassza ki a korábban létrehozott függvény alkalmazást: **CleanTextFunctionApp**

   ![Válassza ki az Azure-függvény alkalmazást](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function-app.png)

4. Most válassza ki a függvényt: **RemoveHTMLFunction**

   ![Válassza ki az Azure-függvény](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function.png)

5. Nevezze át a függvény alakzat ebben:```Call RemoveHTMLFunction to clean email body``` 

6. A függvény alakzat meg feldolgozni a függvény a bemeneti. Adja meg az e-mail törzsének látható és ismertetett itt:

   ![Adja meg a függvény által várt a kérelem törzsében](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing.png)

   1. A **kérelem törzse**, adja meg ezt a szöveget: 
   
      ```{ "emailBody": ``` 

      Ez a bejegyzés a következő lépésekben befejezéséig érvénytelen JSON kapcsolatos hibaüzenet jelenik meg.
      Ha korábban már tesztelt ezt a funkciót, a függvényhez megadott bemeneti használt JavaScript Object Notation (JSON). 
      Igen a kérelem törzsében formátumot kell követnie a azonos túl. 

   2. A paraméterlista vagy a dinamikus tartalom listába, válassza ki a **törzs** eleménél **egy új e-mailt fogadásakor**.
   Miután a **törzs** mezőben, adja hozzá a záró kapcsos zárójelet:```}```

      ![Adja meg a sikeres, a függvény a kérelem törzsében](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing2.png)

      A logic app-definíciót Ez a bejegyzés a következő formátumban jelenik meg:

      ```{ "emailBody": "@triggerBody()?['Body']" }```

7. Mentse a Logic Apps alkalmazást.

Ezután adja hozzá a tároló mentéséhez, az e-mail törzsének blob létrehozó művelet.

## <a name="create-blob-for-email-body"></a>Az e-mail törzsének blob létrehozása

1. Válassza ki az Azure-függvény alakzat **művelet hozzáadása**. 

2. A **művelet kiválasztását**, keressen a "blob", és válassza ki ezt a műveletet: **Azure Blob Storage-blob létrehozása**

   ![Az e-mail törzsének blob létrehozása művelet hozzáadása](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-email-body.png)

3. Ha kapcsolatot létesíthet egy Azure storage-fiók nem rendelkezik, kapcsolat létrehozása a tárfiókhoz ezekkel a beállításokkal látható és ismertetett itt. Amikor elkészült, válassza ki a **létrehozása**.

   ![Internetkapcsolat storage-fiók létrehozása](./media/tutorial-process-email-attachments-workflow/create-storage-account-connection-first.png)

   | Beállítás | Érték | Leírás | 
   | ------- | ----- | ----------- | 
   | **Kapcsolat neve** | AttachmentStorageConnection | Egy leíró nevet a kapcsolathoz | 
   | **Storage Account** | attachmentstorageacct | A tárfiók, amely korábban hozott létre mellékleteket mentése neve | 
   |||| 

4. Nevezze át a **létrehozás blob** a leírást a művelet:```Create blob for email body```

5. Az a **létrehozás blob** művelet, ezt az információt, és válassza ki ezeket a paramétereket a blob látható és ismertetett létrehozásához:

   ![Adja meg az e-mail törzsének blob információi](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body.png)

   | Beállítás | Érték | Leírás | 
   | ------- | ----- | ----------- | 
   | **Mappa elérési útja** | /attachments | Az elérési út és a korábban létrehozott tároló nevét. Is keresse meg és jelölje ki a tárolót. | 
   | **A BLOB neve** | **A** mező | Az e-mailt küldő nevét a blob neveként adja át. Válassza ki a paraméterlista vagy a dinamikus tartalom listába **a** alatt **egy új e-mailt fogadásakor**. | 
   | **BLOB tartalma** | **Tartalom** mező | A blob tartalmat továbbítani a HTML-mentes e-mail törzsének. Válassza ki a paraméterlista vagy a dinamikus tartalom listába **törzs** alatt **RemoveHTMLFunction hívja az e-mail törzsének törlése**. |
   |||| 

6. Mentse a Logic Apps alkalmazást. 

### <a name="check-attachment-handling"></a>Ellenőrizze a mellékletek kezelése

Most megállapítására, hogy a Logic Apps alkalmazást kezeli az e-mailt a megadott módon:

1. Ha a logikai alkalmazás már nem fut, válassza ki a **futtatása** a designer eszköztáron.

2. Küldhet magának, egy e-mailt, amely megfelel a feltételeknek:

   * Az e-mail tárgyát rendelkezik a szöveg, amelyet az eseményindítóhoz megadott **tulajdonos szűrő**:```Business Analyst 2 #423501```

   * Az e-maileket rendelkezik legalább egy mellékletet. 
   Most hozzon létre egy üres szöveges fájlt, és csatolja a fájlt a e-mailhez.

   * Az e-maileket rendelkezik teszttartalmat a szövegtörzset, például: 

     ```
     Testing my logic app
     ```

   Ha a logikai alkalmazás nem indítható el, vagy annak ellenére, hogy egy sikeres eseményindító futtassa, lásd: [hibaelhárítása a Logic Apps alkalmazást](../logic-apps/logic-apps-diagnosing-failures.md).

3. Ellenőrizze, hogy a logikai alkalmazás menti az e-mailt a megfelelő tárolót. 

   1. A Tártallózó, bontsa ki a **(helyi és kapcsolódó)** > 
    **Tárfiókok** > **attachmentstorageacct (külső)** > 
    **Blobtárolók** > **mellékletek**.

   2. Ellenőrizze a **mellékletek** tárolója az e-mailt. 

      Ekkor csak az e-mailt jelenik meg a tároló, mert a logikai alkalmazást még nem feldolgozni a mellékleteket.

      ![A Tártallózó mentett e-mailek ellenőrzése](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-email.png)

   3. Ha elkészült, az e-mailt a Tártallózó törlése.

4. Másik lehetőségként tesztelése a **hamis** fiókirodai nothing jelenleg, amely biztosítja, hogy nem felel meg e-mailt küldhet.

Ezután adja hozzá a e-mail-mellékletek feldolgozása hurkot.

## <a name="process-attachments"></a>Folyamat mellékletek

A logikai alkalmazást használ a **minden** hurok feldolgozni az egyes mellékletek az e-mailben.

1. Az a **létrehozás blob az e-mail törzsének** alakul, válassza a **... További**, és válassza ki ezt a parancsot: **hozzáadása egy minden**

   !["Az egyes" hurok hozzáadása](./media/tutorial-process-email-attachments-workflow/add-for-each-loop.png)

2. Nevezze át a leírás a hurok:```For each email attachment```

3. Mostantól megadhatja a hurok feldolgozni az adatokat. Kattintson a **kimenetnek válassza az előző lépéseiből** mezőbe. Válassza ki a paraméterlista vagy a dinamikus tartalom listába, azt **mellékletek**. 

   ![Válassza ki a "Mellékletek"](./media/tutorial-process-email-attachments-workflow/select-attachments.png)

   A **mellékletek** mező továbbítja az e-mailben szereplő összes mellékleteket tartalmazó tömb. 
   A **minden** hurok ismétlődik minden elemet, az átadott eseményargumentumok a tömb műveleteket.

4. Mentse a Logic Apps alkalmazást.

Ezután adja hozzá a végrehajtandó műveletet a blob az egyes mellékletek menti a **mellékletek** tároló.

## <a name="create-blobs-for-attachments"></a>Hozzon létre mellékleteket blobokat

1. Az a **minden** hurokba, válassza a **művelet hozzáadása** kell adnia a feladat végrehajtását az egyes talált mellékletek.

   ![Adja hozzá a műveletet úgy, hogy a hurok](./media/tutorial-process-email-attachments-workflow/for-each-add-action.png)

2. A **művelet kiválasztását**, keressen a "blob", majd válassza ki ezt a műveletet: **Azure Blob Storage-blob létrehozása**

   ![A blob létrehozása művelet hozzáadása](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-attachments.png)

3. Nevezze át a **létrehozás blob 2** a leírást a művelet:```Create blob for each email attachment```

4. Az a **létrehozás blob minden e-mail-melléklet** művelet, ezt az információt, és válassza ki a paramétereket, minden egyes blob látható és ismertetett létrehozásához:

   ![Adja meg a blob adatokat](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment.png)

   | Beállítás | Érték | Leírás | 
   | ------- | ----- | ----------- | 
   | **Mappa elérési útja** | /attachments | Az elérési út és a tároló nevét korábban létrehozott. Keresse meg a is, és jelöljön ki egy tárolót. | 
   | **A BLOB neve** | **Név** mező | Válassza ki a paraméterlista vagy a dinamikus tartalom listába **neve** felelt meg a blob nevének melléklet neve. | 
   | **BLOB tartalma** | **Tartalom** mező | Válassza ki a paraméterlista vagy a dinamikus tartalom listába **tartalom** a blob tartalma a melléklet tartalom átadni. |
   |||| 

5. Mentse a Logic Apps alkalmazást. 

### <a name="check-attachment-handling"></a>Ellenőrizze a mellékletek kezelése

A következő megállapítására, hogy a logikai alkalmazás kezeli a mellékleteket a úgy, hogy a megadott:

1. Ha a logikai alkalmazás már nem fut, válassza ki a **futtatása** a designer eszköztáron.

2. Küldhet magának, egy e-mailt, amely megfelel a feltételeknek:

   * Az e-mail tárgyát rendelkezik a szöveg, amelyet az eseményindítóhoz megadott **tulajdonos szűrő**:```Business Analyst 2 #423501```

   * Az e-maileket legalább két melléklet tartozik. 
   Most hozzon létre két üres szöveges fájlt, és csatolja azokat a fájlokat az e-mailhez.

   Ha a logikai alkalmazás nem indítható el, vagy annak ellenére, hogy egy sikeres eseményindító futtassa, lásd: [hibaelhárítása a Logic Apps alkalmazást](../logic-apps/logic-apps-diagnosing-failures.md).

3. Ellenőrizze, hogy a logikai alkalmazás menti az e-mailek és a mellékleteket a megfelelő tárolót. 

   1. A Tártallózó, bontsa ki a **(helyi és kapcsolódó)** > 
    **Tárfiókok** > **attachmentstorageacct (külső)** > 
    **Blobtárolók** > **mellékletek**.

   2. Ellenőrizze a **mellékletek** tároló, mind az e-mailt, és a mellékleteket.

      ![Ellenőrizze a mentett e-mailek és mellékletek](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-attachments.png)

   3. Ha elkészült, az e-mailek és mellékletek a Tártallózó törlése.

A következő művelet hozzáadása, hogy a logikai alkalmazás küld e-mailek tekintse át a mellékleteket.

## <a name="send-email-notifications"></a>E-mail értesítéseket küldhet

1. Az a **igaz értéke esetén** a fiókirodai a **minden e-mail-melléklet** hurokba, válassza a **művelet hozzáadása**. 

   ![Adja hozzá tartozó"a" művelet hurok](./media/tutorial-process-email-attachments-workflow/add-action-send-email.png)

2. A **művelet kiválasztását**, keressen a "e-mail küldése", majd jelölje ki a használni kívánt e-mail-szolgáltató a "e-mail küldési" műveletet. Az adott szolgáltatásokhoz műveletek listája szűrhető, jelölje ki az összekötő először a **összekötők**.

   ![Válassza ki az e-mailt Provider "e-mail küldési" műveletet](./media/tutorial-process-email-attachments-workflow/add-action-select-send-email.png)

   * Munkahelyi vagy iskolai Azure-fiókok esetében válassza az Office 365 Outlookot. 
   * Személyes Microsoft-fiókok esetében válassza az Outlook.com-összekötőt. 

3. Ha megkérdezi, hogy a hitelesítő adatokat, jelentkezzen be az e-mail fiókjába, hogy a Logic Apps kapcsolatot hoz létre az e-mail fiókjába.

4. Nevezze át a **egy e-mailek küldése** a leírást a művelet:```Send email for review```

5. Adja meg a művelet az adatokat, és válassza ki a mezőket, amelyeknek az e-mailben megjelenített és ismertetett szeretne. Ha üres sorokat kíván hozzáadni a szerkesztőmezőkhöz, nyomja le a Shift + Enter billentyűkombinációt.  

   Ha például a dinamikus tartalom listába dolgozik:

   ![E-mail értesítés küldése](./media/tutorial-process-email-attachments-workflow/send-email-notification.png)

   Ha egy várt mező nem található a listában, jelölje be **további** melletti **egy új e-mailt fogadásakor** a dinamikus tartalom listába vagy végén található a listában.

   | Beállítás | Érték | Megjegyzések | 
   | ------- | ----- | ----- | 
   | **Címzett** | <*recipient-email-address*> | Tesztelési célokra használhatja a saját e-mail-címét. | 
   | **Tárgy**  | ```ASAP - Review applicant for position: ``` **Subject** | Az e-mail tárgyát, amely a mellékletet. A paraméterlista vagy a dinamikus tartalom listában válassza ki a **tulajdonos** eleménél **egy új e-mailt fogadásakor**. | 
   | **Törzs** | ```Please review new applicant:``` <p>```Applicant name: ```**a** <p>```Application file location: ``` **Path** <p>```Application email content: ``` **Body** | Az e-mail törzsének tartalmát. A paraméterlista vagy a dinamikus tartalom listába a mezők kiválasztása: <p>-A **a** eleménél **egy új e-mailt fogadásakor** </br>-A **elérési** eleménél **e-mail törzsének blob létrehozása** </br>-A **törzs** eleménél **RemoveHTMLFunction hívja az e-mail törzsének törlése** | 
   |||| 

   Ha véletlenül jelölje ki a mezőt, amely tartalmazza a tömb, például a **tartalom**, vagyis olyan tömb, amely tartalmazza a mellékleteket, a tervező automatikusan hozzáadja a művelet, ezt a mezőt hivatkozó körül "az egyes" hurkot. 
   Így a logikai alkalmazás a tömb mindegyik elemén végrehajthatja az adott műveletet. 
   A hurok eltávolításához távolítsa el a mezőt a tömb, helyezze át a hivatkozó művelet kívül a hurok, válassza a folytatást jelző pontokra (**...** ) a hurok cím sáv megnyitásához, és válassza a **törlése**.
     
6. Mentse a Logic Apps alkalmazást. 

Ezt követően tesztelje a Logic Apps alkalmazást, amely most már a következőhöz hasonló, az ebben a példában:

![Befejezett logikai alkalmazás](./media/tutorial-process-email-attachments-workflow/complete.png)

## <a name="run-your-logic-app"></a>A logikai alkalmazás futtatása

1. Küldhet magának, egy e-mailt, amely megfelel a feltételeknek:

   * Az e-mail tárgyát rendelkezik a szöveg, amelyet az eseményindítóhoz megadott **tulajdonos szűrő**:```Business Analyst 2 #423501```

   * Egy vagy több mellékletek rendelkezik az e-maileket. 
   Az előző tesztből egy üres szövegfájl is felhasználhatja. 
   A modell esetben folytatása fájl csatolása.

   * Az e-mail törzsének van ez a szöveg, amelyet másolja és illessze be:

     ```
     Name: Jamal Hartnett   
     
     Street address: 12345 Anywhere Road   
     
     City: Any Town   
     
     State or Country: Any State   
     
     Postal code: 00000   
     
     Email address: jamhartnett@outlook.com   
     
     Phone number: 000-000-0000   
     
     Position: Business Analyst 2 #423501   

     Technical skills: Dynamics CRM, MySQL, Microsoft SQL Server, JavaScript, Perl, Power BI, Tableau, Microsoft Office: Excel, Visio, Word, PowerPoint, SharePoint, and Outlook   

     Professional skills: Data, process, workflow, statistics, risk analysis, modeling; technical writing, expert communicator and presenter, logical and analytical thinker, team builder, mediator, negotiator, self-starter, self-managing  
     
     Certifications: Six Sigma Green Belt, Lean Project Management   
     
     Language skills: English, Mandarin, Spanish   
     
     Education: Master of Business Administration   
     ```

2. Futtassa a logikai alkalmazást. Ha sikeres, a logikai alkalmazás küld egy e-mailt, amely ebben a példában a következőképpen néz:

   ![Logikai alkalmazás által küldött e-mail-értesítések](./media/tutorial-process-email-attachments-workflow/email-notification.png)

   Ha nem minden e-mailek, ellenőrizze az e-maileket Levélszemét mappát. 
   Az e-mailek szűrőt átirányítási előfordulhat, hogy az ilyen típusú e-mailt. 
   Ha nem biztos abban, hogy a logikai alkalmazás megfelelően futott-e, tekintse meg a [logikai alkalmazás hibaelhárításával foglalkozó szakaszt](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulálunk, most már létrehozott, és futtassa a logikai alkalmazást, amely automatizálja a feladatokat más Azure-szolgáltatások között, és az egyes egyéni kódot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor már nincs szükség, a csoport, amely tartalmazza a logikai alkalmazás és a kapcsolódó erőforrások törléséhez. Lépjen az Azure főmenü **erőforráscsoportok**, és válassza ki az erőforrás a Logic Apps alkalmazást. Válasszon **erőforrás csoport törlése**. Adja meg az erőforráscsoport neve megerősítő, és válassza a **törlése**.

![Logic app erőforráscsoport törlése](./media/tutorial-process-email-attachments-workflow/delete-resource-group.png)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott logikai alkalmazás, amely feldolgozza, és tárolja az e-mail mellékletek integrálja az Azure-szolgáltatások, például az Azure Storage és az Azure Functions. Most további információ más összekötők logic Apps alkalmazásokat készíthet is használhatja.

> [!div class="nextstepaction"]
> [További információk összekötők Logic Apps](../connectors/apis-list.md)