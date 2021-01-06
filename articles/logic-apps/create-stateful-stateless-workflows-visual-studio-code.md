---
title: Logic Apps előnézeti munkafolyamatok létrehozása a Visual Studio Code-ban
description: Hozzon létre és futtasson munkafolyamatokat az automatizálási és integrációs forgatókönyvekhez a Visual Studio Code-ban a Azure Logic Apps (előzetes verzió) bővítménnyel.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: be8d00d795c19399d494db21578e9a7ba8dd9711
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934016"
---
# <a name="create-stateful-and-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Állapot-nyilvántartó és állapot nélküli munkafolyamatok létrehozása a Visual Studio Code-ban a Azure Logic Apps (előzetes verzió) bővítménnyel

> [!IMPORTANT]
> Ez a képesség nyilvános előzetes verzióban érhető el, nem vonatkozik rá szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A [Azure Logic apps előzetes](logic-apps-overview-preview.md)verzióban automatizálási és integrációs megoldásokat hozhat létre az alkalmazásokban, az adattárakban, a Cloud Servicesben és a rendszerekben olyan logikai alkalmazások létrehozásával és futtatásával, amelyek a Visual Studio Code-ban a Azure Logic apps (előzetes verzió) bővítmény használatával tartalmaznak [ *állapot* -nyilvántartó és *állapot nélküli* munkafolyamatokat](logic-apps-overview-preview.md#stateful-stateless) . Az új logikai alkalmazás típusának használatával több munkafolyamatot is létrehozhat, amelyeket az újratervezett Azure Logic Apps előzetes verzió futtat, amely hordozhatóságot, jobb teljesítményt és rugalmasságot biztosít különböző üzemeltetési környezetekben történő üzembe helyezéshez és működtetéshez, nem csak az Azure-ban, hanem a Docker-tárolókban is. Az új logikai alkalmazás típusával kapcsolatos további tudnivalókért tekintse meg a [Azure Logic apps előzetes verziójának áttekintését](logic-apps-overview-preview.md).

![Képernyőkép, amely a Visual Studio Code, a Logic app Project és a Workflow alkalmazást jeleníti meg.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

A Visual Studio Code-ban elkezdheti egy olyan projekt létrehozásával, amely *helyileg* létrehozza és futtatja a logikai alkalmazás munkafolyamatait a fejlesztési környezetben a Azure Logic apps (előzetes verzió) bővítmény használatával. Habár az [új **Logic app (előzetes verzió)** erőforrás létrehozásával](create-stateful-stateless-workflows-azure-portal.md)is elindítható a Azure Portalban, mindkét módszer biztosítja a képességet, hogy a logikai alkalmazást ugyanolyan típusú üzemeltetési környezetekben lehessen üzembe helyezni és futtatni.

Addig is létrehozhatja az eredeti logikai alkalmazás típusát. Bár a Visual Studio Code fejlesztői tapasztalatai eltérnek az eredeti és az új logikai alkalmazás típusától, az Azure-előfizetés mindkét típust magában foglalhatja. Az Azure-előfizetésben megtekintheti és elérheti az összes telepített Logic apps-alkalmazást, de az alkalmazások a saját kategóriákba és csoportokba vannak rendezve.

Ez a cikk bemutatja, hogyan hozhatja létre a logikai alkalmazást és a munkafolyamatot a Visual Studio Code-ban a Azure Logic Apps (előzetes verzió) bővítménnyel és a magas szintű feladatok végrehajtásával:

* Hozzon létre egy projektet a logikai alkalmazáshoz és a munkafolyamathoz.

* Adjon hozzá egy triggert és egy műveletet.

* A futtatási előzmények helyi futtatása, tesztelése, hibakeresése és áttekintése.

* Üzembe helyezés az Azure-ban, amely opcionálisan engedélyezi a Application Insights.

* Felügyelheti az üzembe helyezett logikai alkalmazást a Visual Studio Code-ban és a Azure Portalban.

* Állapot nélküli munkafolyamatok futtatási előzményeinek engedélyezése.

* Az üzembe helyezést követően engedélyezze vagy nyissa meg a Application Insights.

* Helyezzen üzembe egy olyan Docker-tárolón, amelyet bárhol futtathat.

> [!NOTE]
> Az aktuális ismert problémákkal kapcsolatos információkért tekintse át a [Logic apps nyilvános előzetes verzió ismert problémáit ismertető oldalt a githubon](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

## <a name="prerequisites"></a>Előfeltételek

### <a name="access-and-connectivity"></a>Hozzáférés és kapcsolódás

* Hozzáférés az internethez, így letöltheti a követelményeket, csatlakozhat a Visual Studio Code-ból az Azure-fiókjához, és közzéteheti a Visual Studio Code-ból az Azure-ba, a Docker-tárolóba vagy más környezetbe.

* Azure-fiók és -előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Ha ugyanezt a logikai alkalmazást szeretné felépíteni ebben a cikkben, szüksége van egy Office 365 Outlook e-mail fiókra, amely Microsoft munkahelyi vagy iskolai fiókot használ a bejelentkezéshez.

  Ha úgy dönt, hogy más, [Azure Logic apps által támogatott e-mail-összekötőt](/connectors/)használ, például a Outlook.com vagy a [Gmail szolgáltatást](../connectors/connectors-google-data-security-privacy-policy.md), akkor továbbra is követheti a példát, és az általános átfogó lépések ugyanazok, de a felhasználói felület és a beállítások bizonyos módokon eltérőek lehetnek. Ha például az Outlook.com-összekötőt használja, használja a személyes Microsoft-fiók a bejelentkezéshez.

<a name="storage-requirements"></a>

### <a name="storage-requirements"></a>Tárolási követelmények

#### <a name="windows-and-linux"></a>Windows és Linux

1. Töltse le és telepítse az [Azure Storage Emulator 5,10](https://go.microsoft.com/fwlink/p/?linkid=717179)-es telepítését.

1. Az emulátor futtatásához rendelkeznie kell egy helyi SQL-adatbázis-telepítéssel, például az ingyenes [SQL Server 2019 Express kiadással](https://go.microsoft.com/fwlink/p/?linkid=866658). További információ: [Az Azure Storage Emulator használata fejlesztéshez és teszteléshez](../storage/common/storage-use-emulator.md).

   > [!IMPORTANT]
   > Mielőtt megnyitja a tervezőt a munkafolyamat létrehozásához, győződjön meg arról, hogy elindítja az emulátort. Ellenkező esetben a következő üzenet jelenik meg: `Workflow design time could not be started` .
   >
   > ![Képernyőkép, amely megjeleníti az Azure Storage-emulátort.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

#### <a name="macos"></a>macOS

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és [hozzon létre egy Azure Storage-fiókot](../storage/common/storage-account-create.md?tabs=azure-portal), amely a [Azure functions előfeltétele](../azure-functions/storage-considerations.md).

1. [Keresse meg és másolja a Storage-fiókhoz tartozó kapcsolatok karakterláncát](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys), például:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Képernyőkép, amely megjeleníti a Azure Portal a Storage-fiók hozzáférési kulcsainak és a kapcsolati karakterláncnak a másolásával.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

1. Mentse a karakterláncot biztonságos helyre, hogy később hozzáadja a **local.settings.js** karakterláncot a projektben található azon fájlokhoz, amelyeket a logikai alkalmazás Visual Studio Code-ban való létrehozásához használ.

Amikor később megpróbál megnyitni egy munkafolyamathoz tartozó tervezőt a logikai alkalmazásban, egy üzenet jelenik meg, amely a `Workflow design time could not be started` . Miután ez az üzenet megjelenik, hozzá kell adnia a Storage-fiók kapcsolódási karakterláncát a projektben lévő fájlok kétlocal.settings.js, majd újra **meg** kell nyitnia a tervezőt.

### <a name="tools"></a>Eszközök

* A [Visual Studio Code 1.30.1 (január 2019) vagy újabb](https://code.visualstudio.com/), amely ingyenes. Töltse le és telepítse ezeket a további eszközöket a Visual Studio Code-hoz, ha még nem rendelkezik ezekkel:

  * [Azure-fiók bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account), amely egyetlen közös Azure bejelentkezési és előfizetés-szűrési élményt biztosít a Visual Studio Code összes többi Azure-bővítményéhez.

  * [C# a Visual Studio Code bővítményhez](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), amely lehetővé teszi az F5 funkció számára a logikai alkalmazás futtatását.

  * [Azure functions Core Tools 3.0.2931 vagy újabb verziót](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.2931) a Microsoft Installer (MSI) használatával.

    Ezek az eszközök tartalmazzák a Azure Functions futtatókörnyezetet, amely a Visual Studio Code-ban az előzetes verziójú bővítmény által használt futtatókörnyezetre is kiterjed.

    > [!IMPORTANT]
    > Ha a verziónál korábbi telepítés van telepítve, először távolítsa el az adott verziót, vagy győződjön meg arról, hogy a PATH környezeti változó a letöltött és telepített verzióban található.

  * [Azure Logic apps (előzetes verzió) bővítmény a Visual Studio Code-](https://go.microsoft.com/fwlink/p/?linkid=2143167)hoz. Ez a bővítmény lehetővé teszi, hogy logikai alkalmazásokat hozzon létre, ahol olyan állapot-és állapot nélküli munkafolyamatokat építhet ki, amelyek helyileg futnak a Visual Studio Code-ban, majd ezeket a logikai alkalmazásokat közvetlenül az Azure-ba vagy a Docker-tárolóba helyezik üzembe.

    Jelenleg az eredeti Azure Logic Apps-bővítmény és a nyilvános előzetes verzió is telepítve van a Visual Studio Code-ban. Bár a fejlesztési tapasztalatok különböző módokon különböznek a bővítmények között, az Azure-előfizetés magában foglalhatja a bővítményekkel létrehozott logikai alkalmazások típusát is. A Visual Studio Code megjeleníti az Azure-előfizetésében lévő összes telepített Logic apps-alkalmazást, de a bővítmények nevei, **Logic apps** és **Azure Logic apps (előzetes verzió)** alapján különböző részekre rendezi őket.

    > [!IMPORTANT]
    > Ha a korábbi Private Preview bővítménnyel létrehozott Logic apps-bővítményt, ezek a logikai alkalmazások nem fognak működni a nyilvános előzetes verziójú bővítménnyel. Ezeket a logikai alkalmazásokat azonban áttelepítheti a privát előnézet bővítmény eltávolítása után, törölheti a kapcsolódó fájlokat, és telepítheti a nyilvános előzetes bővítményt. Ezután létrehoz egy új projektet a Visual Studio Code-ban, és átmásolja a korábban létrehozott Logic apps **munkafolyamat. definition** -fájlját az új projektbe. További információ: [áttelepítés a Private Preview bővítménnyel](#migrate-private-preview).

    A **Azure Logic apps (előzetes verzió)** bővítmény telepítéséhez kövesse az alábbi lépéseket:

    1. A Visual Studio Code-ban a bal oldali eszköztáron válassza a **bővítmények** lehetőséget.

    1. A bővítmények keresési mezőjébe írja be a értéket `azure logic apps preview` . Az eredmények listából válassza a **Azure Logic apps (előzetes verzió)** **>** **telepítését**.

       A telepítés befejezése után az előnézet bővítmény a **bővítmények: telepített** listában jelenik meg.

       ![Képernyőfelvétel: a Visual Studio Code telepített bővítmények listája, amelyekben a "Azure Logic Apps (előzetes verzió)" bővítmény aláhúzva jelenik meg.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

* A webhook-alapú triggerek és műveletek helyi futtatásához, például a [beépített http webhook eseményindítóhoz](../connectors/connectors-native-webhook.md)a Visual Studio Code-ban [be kell állítania a visszahívási URL-cím továbbítását](#webhook-setup).

* A cikkben létrehozott példa logikai alkalmazás teszteléséhez olyan eszközre van szükség, amely képes hívásokat küldeni a kérelem-triggernek, amely a példa logikai alkalmazás első lépése. Ha nem rendelkezik ilyen eszközzel, a [Poster](https://www.postman.com/downloads/)letöltése, telepítése és használata is használható.

* Ha a logikai alkalmazást a [Application Insights](../azure-monitor/app/app-insights-overview.md)használatát támogató beállításokkal hozza létre és telepíti, akkor engedélyezheti a diagnosztikai naplózást és a nyomkövetést a logikai alkalmazáshoz. Ezt akkor teheti meg, ha a logikai alkalmazást a Visual Studio Code-ból vagy üzembe helyezés után telepíti. Rendelkeznie kell egy Application Insights példánnyal, de az erőforrást [előre](../azure-monitor/app/create-workspace-resource.md)is létrehozhatja a logikai alkalmazás telepítésekor vagy az üzembe helyezés után.

<a name="migrate-private-preview"></a>

## <a name="migrate-from-private-preview-extension"></a>Migrálás a privát előzetes verzió bővítményből

A **Azure Logic apps (privát előzetes verzió)** bővítménnyel létrehozott logikai alkalmazások nem fognak működni az PublicPreview bővítménnyel. Ezeket a logikai alkalmazásokat azonban áttelepítheti egy új Visual Studio Code-projektbe a következő lépésekkel:

1. Távolítsa el a privát előnézet bővítményt.

1. Törölje a társított kiterjesztési köteget és a NuGet csomag mappáját a következő helyen:

   * A **Microsoft. Azure. functions. ExtensionBundle. workflows** mappa, amely az előző bővítményi csomagokat tartalmazza, és az elérési út mentén található:

     * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`

     * `C:\Users\{userName}.azure-functions-core-tools\Functions\ExtensionBundles`

   * A **Microsoft. Azure. workflows. webjobs. Extension** mappa, amely a [NuGet](/nuget/what-is-nuget) cache a Private Preview bővítményhez, és az elérési út mentén található:

     `C:\Users\{userName}\.nuget\packages`

1. Telepítse a **Azure Logic apps (előzetes verzió)** bővítményt.

1. Hozzon létre egy új projektet a Visual Studio Code-ban.

1. Másolja a korábban létrehozott logikai alkalmazás **workflow. definition** -fájlját az új projektbe.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>A Visual Studio Code telepítése

1. Győződjön meg arról, hogy az összes bővítmény megfelelően van telepítve, töltse be újra vagy indítsa újra a Visual Studio Code-ot.

1. Engedélyezze vagy erősítse meg, hogy a Visual Studio Code automatikusan megkeresi és telepíti a bővítmények frissítéseit, hogy a PublicPreview bővítmény megkapja a legújabb frissítéseket. Ellenkező esetben manuálisan el kell távolítania az elavult verziót, és telepítenie kell a legújabb verziót.

   A beállítás megadásához kövesse az alábbi lépéseket:

   1. A **fájl** menüben válassza **a beállítások** **>** **Beállítások menüpontot**.

   1. A **felhasználó** lapon válassza a **szolgáltatások** **>** **bővítmények** lehetőséget.

   1. Ellenőrizze, hogy be van-e jelölve az **automatikus ellenőrzés frissítései** és az **automatikus frissítés** .

1. Engedélyezze vagy erősítse meg, hogy ezek a bővítmény-beállítások a megfelelő beállításokat adtak meg:

   * **Azure Logic Apps v2: panel mód**
   * **Azure Logic Apps v2: Project Runtime**

   1. A **fájl** menüben válassza **a beállítások** **>** **Beállítások menüpontot**.

   1. A **felhasználó** lapon válassza a **>** **bővítmények** **>** **Azure Logic apps (előzetes verzió)** lehetőséget.

   1. A bővítmény beállításainak megerősítése:

      * A **Azure Logic apps v2: panel módban** ellenőrizze, hogy be van-e jelölve az **Engedélyezés panel mód** .

      * A **Azure Logic apps v2: Project Runtime** alatt ellenőrizze, hogy a verzió értéke **~ 3**.

        > [!IMPORTANT]
        > Ha a macOS és a Linux rendszerhez jelenleg nem elérhető [beágyazott kód-műveleti](../logic-apps/logic-apps-add-run-inline-code.md)műveleteket szeretne használni, a **Project Runtime** beállítása 3-as verzióra van szükség.

      ![A "Azure Logic Apps (előzetes verzió)" bővítmény Visual Studio Code-beállításait bemutató képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz

1. A Visual Studio Code tevékenység sávján válassza az Azure ikont.

   ![Képernyőkép, amely a Visual Studio Code tevékenység sávját és a kiválasztott Azure ikont jeleníti meg.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. Az Azure ablaktáblán az **Azure: Logic apps (előzetes verzió)** területen válassza a **Bejelentkezés az Azure**-ba lehetőséget. Amikor megjelenik a Visual Studio Code Authentication oldal, jelentkezzen be az Azure-fiókjával.

   ![Képernyőkép, amely az Azure-ablaktáblát és az Azure-bejelentkezéshez kiválasztott hivatkozást mutatja.](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   Miután bejelentkezett, az Azure panel megjeleníti az Azure-fiók előfizetéseit. Ha rendelkezik a nyilvánosan elérhető bővítménnyel, az adott bővítménnyel létrehozott logikai alkalmazásokat a **Logic apps** szakaszban találja, nem a **Logic apps (előzetes verzió)** szakaszt.
   
   Ha a várt előfizetések nem jelennek meg, vagy azt szeretné, hogy a panel csak bizonyos előfizetéseket jelenítsen meg, kövesse az alábbi lépéseket:

   1. Az előfizetések listájában vigye a mutatót az első előfizetés mellé, amíg megjelenik az **előfizetések kiválasztása** gomb (szűrő ikon). Kattintson a szűrő ikonra.

      ![Képernyőkép, amely az Azure panelt és a kiválasztott szűrő ikont jeleníti meg.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      Vagy a Visual Studio Code állapotjelző sávjában válassza ki az Azure-fiókját. 

   1. Ha megjelenik egy másik előfizetések listája, válassza ki a kívánt előfizetéseket, és győződjön meg róla, hogy az **OK gombra** kattint.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Helyi projekt létrehozása

A logikai alkalmazás létrehozása előtt hozzon létre egy helyi projektet, hogy a Visual Studio Code-ból kezelhesse és üzembe lehessen helyezni a logikai alkalmazást. Az alapul szolgáló projekt hasonló egy Azure Functions projekthez, más néven Function app-projekthez. Ezek a projekttípus azonban egymástól függetlenek, így a Logic apps és a Function alkalmazások nem létezhetnek ugyanabban a projektben.

1. A számítógépen hozzon létre egy *üres* helyi mappát a projekthez, amelyet később a Visual Studio Code-ban fog létrehozni.

1. A Visual Studio Code-ban zárjunk be minden és minden megnyitott mappát.

1. Az Azure ablaktáblán, az **Azure: Logic apps (előzetes verzió)** területen válassza az **új projekt létrehozása** (ikon, amely egy mappát és egy villámot mutatja).

   ![Képernyőkép, amely az Azure panel eszköztárát az "új projekt létrehozása" lehetőség kiválasztásával jeleníti meg.](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Ha a Windows Defender-tűzfal felszólítja a hálózati hozzáférés megadására `Code.exe` , amely a Visual Studio Code, és a `func.exe` , amely a Azure functions Core Tools, válassza a **magánhálózatok, például a saját otthoni vagy munkahelyi hálózat** **>** **lehetővé teszi a hozzáférést**.

1. Keresse meg azt a helyet, ahová a projekt mappáját létrehozta, válassza ki a mappát, és folytassa a folytatást.

   ![A "mappa kiválasztása" párbeszédpanelt megjelenítő képernyőkép, amely egy újonnan létrehozott Project-mappával és a "kiválasztás" gomb kiválasztásával jelenik meg.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. A megjelenő sablonok listából válassza az **állapot-nyilvántartó munkafolyamat** vagy az **állapot nélküli munkafolyamat** lehetőséget. Ez a példa **állapot-nyilvántartó munkafolyamatot** jelöl ki.

   ![Képernyőkép, amely a munkafolyamat-sablonok listáját jeleníti meg "állapot-nyilvántartó munkafolyamat" beállítással.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Adja meg a munkafolyamat nevét, majd nyomja le az ENTER billentyűt. Ez a példa `Fabrikam-Stateful-Workflow` a nevet használja.

   ![Képernyőkép, amely az "új állapot-nyilvántartó munkafolyamat létrehozása (3/3)" és a "Fabrikam-állapot-munkafolyamat" nevet mutatja a munkafolyamat neveként.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

   A Visual Studio Code befejezi a projekt létrehozását, és megnyitja a munkafolyamathoz tartozó **workflow.js** fájlt.

1. A Visual Studio eszköztárán nyissa meg az Explorer ablaktáblát, ha még nincs megnyitva.

   Az Explorer ablaktáblán látható a projekt, amely mostantól automatikusan generált projektfájlt is tartalmaz. A projektnek például van egy olyan mappája, amely megjeleníti a munkafolyamat nevét. A mappában a **workflow.js** fájl a munkafolyamat MÖGÖTTes JSON-definícióját tartalmazza.

   ![Képernyőkép, amely megjeleníti az Explorer ablaktáblát a Project mappában, a munkafolyamat mappájában és a "workflow.json" fájlt.](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-the-designer"></a>A munkafolyamat-definíciós fájl megnyitása a tervezőben

1. A következő parancs futtatásával keresse meg a számítógépen telepített verziókat:

   `..\Users\{yourUserName}\dotnet --list-sdks`

   Ha .NET Core SDK 5. x, ez a verzió megakadályozhatja, hogy megnyissa a logikai alkalmazás mögöttes munkafolyamat-definícióját a tervezőben. Ha nem távolítja el ezt a verziót, a projekt legfelső szintjén hozzon létre egy **global.js** olyan fájlon, amely a .net Core Runtime 3. x verziójára hivatkozik, amely a 3.1.201-nél későbbi, például:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   Győződjön meg arról, hogy explicit módon hozzáadja a fájl **global.jsa** projekt gyökérszintű szintjéhez a Visual Studio Code-ból belül. Ellenkező esetben a tervező nem nyílik meg.

1. Ha Windows vagy Linux rendszeren futtatja a Visual Studio Code-ot, győződjön meg arról, hogy az Azure Storage-emulátor fut. További információkért tekintse át az [előfeltételeket](#prerequisites). Ha macOS rendszeren futtatja a Visual Studio Code-ot, folytassa a következő lépéssel.

1. Bontsa ki a munkafolyamat projekt mappáját. Nyissa meg a **workflow.jsa** fájl helyi menüjében, és válassza a **Megnyitás a tervezőben** lehetőséget.

   ![Képernyőfelvétel: az Explorer panel és a parancsikon ablaka a "Megnyitás a Designerben" beállítással rendelkező fájl workflow.js.](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

   Ha az alábbi hibaüzenet jelenik meg `Workflow design time could not be started` , ellenőrizze a következő feltételeket:

   * **Windows vagy Linux**: Ellenőrizze, hogy fut-e az Azure Storage Emulator. Egyéb esetben tekintse meg a [problémák és hibák elhárítása](#troubleshooting)című témakört.

   * **MacOS**: próbálja ki a következő megoldást, és ha nem sikerül, tekintse meg a [problémák és hibák elhárítása](#troubleshooting)című témakört.

     1. A projektben nyissa meg a **local.settings.js** fájlokat, amelyek a projekt gyökérkönyvtárában és a **munkafolyamat-designtime** mappában találhatók.

        ![Képernyőkép, amely megjeleníti a projekt Explorer ablaktábláját és a "local.settings.json" fájlokat.](./media/create-stateful-stateless-workflows-visual-studio-code/local-settings-json-files.png)

     1. Az egyes fájlokban keresse meg a `AzureWebJobsStorage` tulajdonságot, például:

        ```json
        {
           "IsEncrypted": false,
           "Values": {
              "AzureWebJobsStorage": "UseDevelopmentStorage=true",
              "FUNCTIONS_WORKER_RUNTIME": "dotnet"
            }
        }
        ```

      1. Cserélje le a `AzureWebJobsStorage` tulajdonság értékét a Storage-fiókból korábban mentett, például:

         ```json
         {
            "IsEncrypted": false,
            "Values": {
               "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net",
               "FUNCTIONS_WORKER_RUNTIME": "dotnet"
            }
         }
         ```

      1. Mentse a módosításokat, és próbálja meg újra megnyitni a **workflow.jst** a Designerben.

1. Az **Összekötők engedélyezése az Azure-ban** listában válassza az Azure-beli **Összekötők használata** lehetőséget, amely az Azure-ban elérhető és az Azure-ban üzembe helyezett összes felügyelt összekötőre vonatkozik, nem csak az Azure-szolgáltatások összekötői.

   ![Képernyőfelvétel: az "összekötők engedélyezése az Azure-ban" lista megnyitása és az "összekötők használata az Azure-ban" lehetőség van kiválasztva.](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > Az állapot nélküli munkafolyamatok jelenleg csak az Azure-ban üzembe helyezett [felügyelt összekötők](../connectors/apis-list.md#managed-api-connectors) *műveleteit* támogatják, és nem indítják el azokat. Bár lehetősége van az Azure-beli összekötők engedélyezésére az állapot nélküli munkafolyamatokhoz, a tervező nem jelenít meg felügyelt összekötő-eseményindítókat a kiválasztáshoz.

1. Az **előfizetés kiválasztása** listából válassza ki a logikai alkalmazás projekthez használni kívánt Azure-előfizetést.

   ![Képernyőkép, amely az "előfizetés kiválasztása" és a kiválasztott előfizetés kiválasztására szolgáló Explorer ablaktáblát jeleníti meg.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-subscription.png)

1. Az erőforráscsoportok listából válassza az **Új erőforráscsoport létrehozása** lehetőséget.

   ![Képernyőkép, amely megjeleníti az Explorer ablaktáblát az erőforráscsoportok listájának és az "új erőforráscsoport létrehozása" beállítással.](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Adja meg az erőforráscsoport nevét, majd nyomja le az ENTER billentyűt. Ez a példa a következőt használja: `Fabrikam-Workflows-RG`.

   ![Képernyőkép, amely megjeleníti az Explorer ablaktáblát és az erőforráscsoport neve mezőt.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. A helyszínek listában keresse meg és válassza ki az erőforráscsoport és az erőforrások létrehozásakor használni kívánt Azure-régiót. Ez a példa az **USA nyugati középső** régióját használja.

   ![Képernyőkép, amely megjeleníti az Explorer ablaktáblát a helyszínek listával és az "USA nyugati középső régiója" kijelöléssel.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   A lépés elvégzése után a Visual Studio Code megnyitja a munkafolyamat-tervezőt.

   > [!NOTE]
   > Ha a Visual Studio Code elindítja a munkafolyamat tervezési idejű API-ját, megjelenik egy üzenet, amely eltarthat néhány másodpercig. Figyelmen kívül hagyhatja ezt az üzenetet, vagy választhatja **az OK** gombot.

   A tervező megjelenése után a **művelet kiválasztása** üzenet jelenik meg a tervezőben, és alapértelmezés szerint ki van választva, amely megjeleníti a **művelet hozzáadása** ablaktáblát.

   ![A munkafolyamat-tervezőt bemutató képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Ezután [adjon hozzá egy triggert és műveleteket](#add-trigger-actions) a munkafolyamathoz.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Trigger és műveletek hozzáadása

A tervező megnyitása után a **művelet kiválasztása** kérdés megjelenik a tervezőben, és alapértelmezés szerint ki van választva. Most már megkezdheti a munkafolyamat létrehozását egy trigger és művelet hozzáadásával.

Az ebben a példában szereplő munkafolyamat ezt az triggert és a következő műveleteket használja:

* A beépített [kérelem triggere](../connectors/connectors-native-reqres.md), **Amikor HTTP-kérés érkezik**, amely bejövő hívásokat fogad, és olyan végpontot hoz létre, amelyet más szolgáltatások vagy logikai alkalmazások hívhatnak meg.

* Az [Office 365 Outlook művelet](../connectors/connectors-create-api-office365-outlook.md), **e-mail küldése**.

* A beépített [Válasz művelet](../connectors/connectors-native-reqres.md), amellyel a rendszer visszaküldi a választ, és visszaküldi azokat a hívónak.

### <a name="add-the-request-trigger"></a>A kérelem-trigger hozzáadása

1. A tervező mellett, az **trigger hozzáadása** panelen, a művelet keresésének **kiválasztása** mezőben ellenőrizze, hogy be van-e jelölve a **beépített elem** , így a natív módon futtatható trigger kiválasztható.

1. A **válasszon műveletet** keresési mezőben adja meg a kifejezést `when a http request` , majd válassza ki a **http-kérelem fogadásakor** megnevezett beépített kérelem-triggert.

   ![Képernyőfelvétel: a Munkafolyamat-tervező és a * * trigger * * panel hozzáadása a "HTTP-kérelem fogadásakor" trigger kiválasztásával.](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   Amikor az trigger megjelenik a tervezőben, megnyílik az trigger részletek panelje, amely megjeleníti az trigger tulajdonságait, beállításait és egyéb műveleteit.

   ![Képernyőkép, amely a "HTTP-kérés fogadásakor" triggert jeleníti meg a Munkafolyamat-tervezőben, és nyitva van az trigger részletei ablaktábla.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Ha a részleteket tartalmazó ablaktábla nem jelenik meg, ellenőrizze, hogy az trigger ki van-e választva a tervezőben.

1. Ha törölni szeretne egy elemet a tervezőből, kövesse az [alábbi lépéseket az elemek tervezőből való törléséhez](#delete-from-designer).

### <a name="add-the-office-365-outlook-action"></a>Az Office 365 Outlook-művelet hozzáadása

1. A tervezőben a hozzáadott trigger alatt válassza az **új lépés** lehetőséget.

   A **válasszon műveletet** kérő üzenet jelenik meg a tervezőben, és a **művelet hozzáadása** ablaktábla újra megnyílik, így kiválaszthatja a következő műveletet.

1. A **művelet hozzáadása** ablaktábla **művelet** keresése mezőjében válassza az **Azure** lehetőséget, így megkeresheti és kiválaszthatja az Azure-ban üzembe helyezett felügyelt összekötőhöz tartozó műveletet.

   Ez a példa kiválasztja és használja az Office 365 Outlook műveletet, **E-mail küldése (v2)**.

   ![A munkafolyamat-tervezőt bemutató képernyőkép, valamint a * * művelet * * ablaktábla hozzáadása az Office 365 Outlook "e-mail küldése" művelet kiválasztásával.](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. A művelet részleteit tartalmazó ablaktáblán válassza a **Bejelentkezés** lehetőséget, hogy létre tudja hozni a kapcsolatát az e-mail-fiókjával.

   ![Képernyőfelvétel: a Munkafolyamat-tervező és a * * e-mail küldése (v2) * * ablaktábla, ahol a "Bejelentkezés" lehetőség be van jelölve.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

1. Ha a Visual Studio Code megkéri az e-mail-fiók elérésének jóváhagyását, válassza a **Megnyitás** lehetőséget.

   ![Képernyőkép, amely a Visual Studio Code promptot jeleníti meg a hozzáférés engedélyezéséhez.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > A jövőbeli kérések megelőzése érdekében válassza a **megbízható tartományok konfigurálása** lehetőséget, hogy a hitelesítési lapot megbízható tartományként adja hozzá.

1. A bejelentkezéshez, a hozzáférés engedélyezéséhez és a Visual Studio Code-hoz való visszatéréshez kövesse a következő utasításokat.

   > [!NOTE]
   > Ha túl sok idő telik el az utasítások elvégzése előtt, a hitelesítési folyamat túllépi az időkorlátot, és sikertelen lesz. Ebben az esetben térjen vissza a tervezőhöz, és ismételje meg a bejelentkezést a kapcsolódás létrehozásához.

1. Ha a Azure Logic Apps (előzetes verzió) bővítmény megkéri az e-mail fiók elérésének jóváhagyását, válassza a **Megnyitás** lehetőséget. A hozzáférés engedélyezéséhez kövesse az ezt követő kérdést.

   ![A hozzáférés engedélyezését bemutató, előzetes verziójú bővítményre vonatkozó kérést megjelenítő képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > Ha meg szeretné akadályozni a jövőbeli kérdéseket, válassza a **bővítmény ne Kérdezzen rá többé** lehetőséget.

   Miután a Visual Studio Code létrehozta a kapcsolatát, néhány összekötő megjeleníti az üzenetet `The connection will be valid for {n} days only` . Ez az időkorlát csak arra az időtartamra vonatkozik, amikor a logikai alkalmazást a Visual Studio Code-ban készíti el. Az üzembe helyezést követően ez a korlát már nem érvényes, mert a logikai alkalmazás az automatikusan engedélyezett, [rendszer által hozzárendelt felügyelt identitás](../logic-apps/create-managed-service-identity.md)használatával tud hitelesítést végezni futásidőben. Ez a felügyelt identitás eltér a kapcsolatok létrehozásakor használt hitelesítő adatoktól vagy kapcsolódási karakterlánctól. Ha letiltja ezt a rendszer által hozzárendelt felügyelt identitást, a kapcsolatok nem működnek futásidőben.

1. Ha a tervezőben nem jelenik meg az **E-mail küldése** művelet, válassza ki ezt a műveletet.

1. A művelet részletek ablaktáblájának **Paraméterek** lapján adja meg a művelethez szükséges adatokat, például:

   ![Képernyőfelvétel: az Office 365 Outlook "e-mail küldése" műveletének részleteit bemutató Munkafolyamat-tervező.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Művelet** | Yes | <*saját e-mail cím*> | Az e-mail címzettje, amely a tesztelési célú e-mail-címe lehet. Ez a példa a fiktív e-mailt használja `sophiaowen@fabrikam.com` . |
   | **Tárgy** | Yes | `An email from your example workflow` | Az e-mail tárgya |
   | **Törzs** | Yes | `Hello from your example workflow!` | Az e-mail szövegtörzsének tartalma |
   ||||

   > [!NOTE]
   > Ha módosítani kívánja a **beállításokat a beállítások**, a **statikus eredmény** vagy a **Futtatás után** lap részletek ablaktábláján, akkor ügyeljen arra, hogy a **kész** gombra kattintva véglegesítse ezeket a módosításokat, mielőtt átváltja a lapokat, vagy módosítani szeretné a fókuszt a tervezőre. Ellenkező esetben a Visual Studio Code nem fogja megőrizni a módosításokat. További információkért tekintse át a [Logic apps nyilvános előzetes verzió ismert problémáit ismertető oldalt a githubon](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

1. A tervezőben válassza a **Mentés** lehetőséget.

> [!IMPORTANT]
> Webhook-alapú triggert vagy műveleteket használó munkafolyamatok helyi futtatásához (például a [beépített http-webhook-trigger vagy művelet](../connectors/connectors-native-webhook.md) [) a webhook visszahívási URL-címére való továbbítás beállításával](#webhook-setup)engedélyeznie kell ezt a funkciót.

<a name="webhook-setup"></a>

## <a name="enable-locally-running-webhooks"></a>Helyileg futó webhookok engedélyezése

Ha webhook-alapú triggert vagy műveletet használ, például a **http-webhookot**, amelyen az Azure-ban futó logikai alkalmazás fut, a Logic apps futtatókörnyezet előfizet a szolgáltatási végpontra egy visszahívási URL-cím létrehozásával és regisztrálásával a végponttal. A trigger vagy művelet ezután megvárja, amíg a szolgáltatási végpont meghívja az URL-címet. Ha azonban a Visual Studio Code-ban dolgozik, a generált visszahívási URL-cím is elindul `http://localhost:7071/...` . Ez az URL-cím a localhost-kiszolgáló, amely magán, így a szolgáltatási végpont nem hívhatja ezt az URL-címet.

Ha a Visual Studio Code-ban helyileg szeretné futtatni a webhook-alapú eseményindítókat és műveleteket, be kell állítania egy nyilvános URL-címet, amely közzéteszi a localhost-kiszolgálót, és biztonságosan továbbítja a hívásokat a szolgáltatás-végpontról a webhook visszahívási URL-címére. Használhatja a továbbítási szolgáltatást és az eszközt, például a [**ngrok**](https://ngrok.com/)-t, amely egy http-alagutat nyit meg a localhost porton, vagy használhatja a saját eszközét is.

#### <a name="set-up-call-forwarding-using-ngrok"></a>Hívásátirányítás beállítása a **ngrok** használatával

1. Ha még nem rendelkezik ilyennel, [regisztráljon egy **ngrok** -fiókra](https://dashboard.ngrok.com/signup) . Ellenkező esetben [Jelentkezzen be a fiókjába](https://dashboard.ngrok.com/login).

1. Szerezze be a személyes hitelesítési tokent, amelyhez a **ngrok** -ügyfélnek csatlakoznia kell, és hitelesítenie kell a fiókhoz való hozzáférést.

   1. A [hitelesítési jogkivonat oldalának](https://dashboard.ngrok.com/auth/your-authtoken)megkereséséhez a fiók irányítópultjának menüjében bontsa ki a **hitelesítés** csomópontot, majd válassza ki **a aad**.

   1. A **aad** mezőjéből másolja a tokent egy biztonságos helyre.

1. A [ **ngrok** letöltési oldaláról](https://ngrok.com/download) vagy [a fiók irányítópultról](https://dashboard.ngrok.com/get-started/setup)töltse le a kívánt **ngrok** -verziót, és bontsa ki a. zip fájlt. További információt az [1. lépés: a telepítéshez való kicsomagolása](https://ngrok.com/download)című témakörben talál.

1. A számítógépen nyissa meg a parancssori eszközt. Keresse meg azt a helyet, ahol a **ngrok.exe** fájl található.

1. A következő parancs futtatásával kapcsolja össze a **ngrok** -ügyfelet a **ngrok** -fiókjával. További információt a [2. lépés: a fiók összekapcsolásával](https://ngrok.com/download)foglalkozó témakörben talál.

   `ngrok authtoken <your_auth_token>`

1. A következő parancs futtatásával nyissa meg a HTTP-alagutat a localhost 7071-es portra. További információ [: 3. lépés: tűz](https://ngrok.com/download).

   `ngrok http 7071`

1. A kimenetben keresse meg a következő sort:

   `http://<domain>.ngrok.io -> http://localhost:7071`

1. Másolja és mentse az alábbi formátumú URL-címet: `http://<domain>.ngrok.io`

#### <a name="set-up-the-forwarding-url-in-your-app-settings"></a>A továbbítási URL beállítása az alkalmazás beállításaiban

1. A Visual Studio Code-ban a projekt legfelső szintjén nyissa meg a **local.settings.js** fájlt.

1. Az `Values` objektumban adjon hozzá egy nevű tulajdonságot `Workflows.WebhookRedirectHostUri` , és állítsa be az értéket a korábban létrehozott továbbítási URL-címre, például:

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "FUNCTIONS_V2_COMPATIBILITY_MODE": "true",
         "Workflows.WebhookRedirectHostUri": "http://xxxXXXXxxxXXX.ngrok.io",
         <...>
      }
   }
   ```

Amikor első alkalommal indítja el a helyi hibakeresési munkamenetet, vagy ha hibakeresés nélkül futtatja a munkafolyamatot, a Logic Apps futtatókörnyezet regisztrálja a munkafolyamatot a szolgáltatás-végponttal, és Előfizet erre a végpontra a webhook műveleteinek értesítéséhez. A munkafolyamat következő futtatásakor a futásidejű szolgáltatás nem regisztrálja vagy újrairatkozik, mert az előfizetés regisztrálása már létezik a helyi tárolóban.

Amikor egy helyileg futtatott webhook-alapú eseményindítót vagy műveletet használó munkafolyamat-Futtatás hibakeresési munkamenetét állítja le, a meglévő előfizetés-regisztrációk nem törlődnek. A regisztráció törléséhez manuálisan el kell távolítania vagy törölnie kell az előfizetés regisztrációját.

> [!NOTE]
> A munkafolyamat futásának megkezdése után a terminál ablaka a következőhöz hasonló hibákat jelenhet meg:
>
> `message='Http request failed with unhandled exception of type 'InvalidOperationException' and message: 'System.InvalidOperationException: Synchronous operations are disallowed. Call ReadAsync or set AllowSynchronousIO to true instead.`
>
> Ebben az esetben nyissa meg a **local.settings.js** fájlt a projekt legfelső szintjén, és győződjön meg arról, hogy a tulajdonság a következőre van beállítva `true` :
>
> `"FUNCTIONS_V2_COMPATIBILITY_MODE": "true"`

<a name="manage-breakpoints"></a>

## <a name="manage-breakpoints-for-debugging"></a>Töréspontok kezelése hibakereséshez

Mielőtt futtatja és teszteli a logikai alkalmazás munkafolyamatát egy hibakeresési munkamenet elindításával, beállíthatja a [töréspontokat](https://code.visualstudio.com/docs/editor/debugging#_breakpoints) a **workflow.json** belül az egyes munkafolyamatokhoz. Nincs szükség további telepítésre. 

Jelenleg a töréspontok csak műveletekhez támogatottak, nem eseményindítók. Mindegyik műveleti definícióban a következő töréspontok találhatók:

* A művelet nevét megjelenítő sorban állítsa be a kezdő töréspontot. Ha ezt a töréspontot a hibakeresési munkamenet során eléri, a kiértékelés előtt áttekintheti a művelet bemeneteit.

* Állítsa be a befejezési töréspontot a sorban, amely megjeleníti a művelet záró kapcsos zárójelét (**}**). Ha ez a Töréspont a hibakeresési munkamenet során eléri a műveletet, akkor a művelet eredményének futtatása előtt áttekintheti a művelet eredményeit.

Töréspont hozzáadásához kövesse az alábbi lépéseket:

1. Nyissa meg a hibakereséshez használni kívánt munkafolyamathoz tartozó **workflow.js** fájlt.

1. Azon a sorban, ahová a töréspontot be szeretné állítani, a bal oldali oszlopban válassza ki az oszlopon belül. A Töréspont eltávolításához válassza ki a töréspontot.

   Amikor elindítja a hibakeresési munkamenetet, a Futtatás nézet megjelenik a kód ablak bal oldalán, míg a hibakeresési eszköztár felül jelenik meg.

   > [!NOTE]
   > Ha a Futtatás nézet nem jelenik meg automatikusan, nyomja le a CTRL + SHIFT + D billentyűkombinációt.

1. Ha meg szeretné tekinteni az elérhető információkat a töréspontok találatakor, a Futtatás nézetben vizsgálja meg a **változók** ablaktáblát.

1. A munkafolyamat-végrehajtás folytatásához a hibakeresés eszköztáron válassza a **Folytatás** (Lejátszás gomb) lehetőséget. 

A munkafolyamat futtatása során bármikor hozzáadhat és eltávolíthat töréspontokat. Ha azonban a Futtatás elindítása után frissíti a **workflow.js** fájlt, a töréspontok nem frissülnek automatikusan. A töréspontok frissítéséhez indítsa újra a logikai alkalmazást.

Általános információk: [Töréspontok – Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging#_breakpoints).

<a name="run-test-debug-locally"></a>

## <a name="run-test-and-debug-locally"></a>Helyi Futtatás, tesztelés és hibakeresés

A logikai alkalmazás teszteléséhez kövesse az alábbi lépéseket a hibakeresési munkamenet elindításához, és keresse meg a kérelem-trigger által létrehozott végpont URL-címét. Erre az URL-címre szüksége lesz, hogy később küldjön egy kérést erre a végpontra.

1. Az állapot nélküli munkafolyamatok egyszerűbb hibakereséséhez [engedélyezheti a munkafolyamat futtatási előzményeit](#enable-run-history-stateless).

1. A Visual Studio Code tevékenység sávján nyissa meg a **Futtatás** menüt, és válassza a **hibakeresés indítása** (F5) lehetőséget.

   Megnyílik a **terminál** ablak, ahol áttekintheti a hibakeresési munkamenetet.

1. Most keresse meg a végpont visszahívási URL-címét a kérelem triggerében.

   1. Nyissa meg újra az Explorer ablaktáblát, és tekintse meg a projektet.

   1. A **workflow.js** fájl helyi menüjében válassza az **Áttekintés** lehetőséget.

      ![Képernyőkép, amely megjeleníti az Explorer ablaktáblát és a parancsikont az "áttekintés" kijelölt fájl workflow.js.](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. Keresse meg a **visszahívási URL-címet** , amely hasonlít ehhez az URL-címhez a példa kérésének triggeréhez:

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![Képernyőkép a munkafolyamat áttekintő oldalának visszahívási URL-címéről](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. Ha tesztelni szeretné a visszahívási URL-címet a logikai alkalmazás munkafolyamatának elindításával, nyissa meg a [Poster](https://www.postman.com/downloads/) vagy a kívánt eszközt a kérelmek létrehozására és küldésére.

   Ez a példa a Poster használatával folytatódik. További információ: [poster első lépések](https://learning.postman.com/docs/getting-started/introduction/).

   1. Az utólagos eszköztáron válassza az **új** lehetőséget.

      ![Képernyőkép, amely az új gomb kiválasztásával mutatja be a Poster elemet](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. Az **új ablaktábla létrehozás** paneljén kattintson a **kérelem** **elemre**.

   1. A kérelem **mentése** ablakban a kérelem **neve** alatt adja meg a kérelem nevét, például: `Test workflow trigger` .

   1. A **menteni kívánt gyűjtemény vagy mappa kiválasztása** területen válassza a **gyűjtemény létrehozása** lehetőséget.

   1. A **minden gyűjtemény** területen adja meg a kérések rendszerezéséhez létrehozandó gyűjtemény nevét, nyomja le az ENTER billentyűt, majd válassza a **Mentés lehetőséget <*gyűjtemény neve* >** elemre. Ez a példa `Logic Apps requests` a gyűjtemény nevét használja.

      A Poster kérelmi panelje megnyílik, így kérést küldhet a visszahívási URL-címre a kérelem triggere számára.

      ![Képernyőkép, amely a megnyitott kérelem ablaktáblán jeleníti meg a Poster-t](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Térjen vissza a Visual Studio Code-ba. a munkafolyamat áttekintés lapján másolja a **visszahívási URL-** tulajdonság értékét.

   1. Vissza a Poster-re. A kérelem ablaktáblán a metódusok listája, amely jelenleg az alapértelmezett kérési módszerként **jelenik meg** , illessze be a cím mezőbe korábban átmásolt visszahívási URL-címet, és válassza a **Küldés** lehetőséget.

      ![Képernyőkép, amely a Poster és a visszahívási URL-címet jeleníti meg a cím mezőben kiválasztott küldés gombbal](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      A példa logikai alkalmazás munkafolyamata egy e-mailt küld, amely az alábbi példához hasonlóan jelenik meg:

      ![Az Outlook e-maileket megjelenítő képernyőkép a példában leírtak szerint](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. A Visual Studio Code-ban térjen vissza a munkafolyamat áttekintés lapjára.

   Ha állapot-nyilvántartó munkafolyamatot hozott létre, akkor az elküldte a munkafolyamatot indító kérést, az Áttekintés oldalon a munkafolyamat futtatási állapota és előzményei láthatók.

   > [!TIP]
   > Ha a futtatási állapot nem jelenik meg, próbálja meg frissíteni az Áttekintés lapot a **frissítés** lehetőség kiválasztásával. A rendszer nem teljesíti az olyan triggereket, amelyek nem teljesülő feltételek miatt kimaradnak, vagy nem találnak semmilyen adatsort.

   ![Képernyőkép a munkafolyamat áttekintő oldaláról a futtatási állapottal és előzményekkel](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   | Futtatás állapota | Leírás |
   |------------|-------------|
   | **Megszakítva** | A Futtatás leállt vagy nem fejeződik be külső problémák miatt, például rendszerleállás vagy elévült Azure-előfizetés. |
   | **Megszakítva** | A Futtatás elindítva és elindítva, de lemondási kérelem érkezett. |
   | **Sikertelen** | A Futtatás során legalább egy művelet sikertelen volt. A munkafolyamatban nem történt további művelet, amely a hiba kezelésére lett beállítva. |
   | **Futó** | A Futtatás aktiválva lett, és folyamatban van, de ez az állapot a [művelet korlátai](logic-apps-limits-and-config.md) vagy a [jelenlegi díjszabási csomag](https://azure.microsoft.com/pricing/details/logic-apps/)miatt szabályozott Futtatás esetén is megjelenhet. <p><p>**Tipp**: Ha [diagnosztikai naplózást](monitor-logic-apps-log-analytics.md)állít be, a megjelenő összes szabályozási eseményről információt kaphat. |
   | **Sikeres** | A Futtatás sikerült. Ha bármilyen művelet meghiúsult, a munkafolyamat egy későbbi művelete ezt a hibát kezelte. |
   | **Időtúllépés** | A Futtatás időtúllépés miatt meghaladta az időkorlátot, mert a jelenlegi időtartam túllépte a futtatási időtartam határértékét, amelyet a [ **futtatási Előzmények megőrzése nap** beállításban](logic-apps-limits-and-config.md#run-duration-retention-limits)szabályoz. A Futtatás időtartamát a Futtatás kezdő időpontja és a futtatási időtartam korlátja alapján számítjuk ki a kezdési időpontnál. <p><p>**Megjegyzés**: Ha a Futtatás időtartama szintén meghaladja az aktuális *futtatási előzmények megőrzési korlátját* is, amelyet a futtatási [ **Előzmények megőrzésének napja** beállítás](logic-apps-limits-and-config.md#run-duration-retention-limits)is szabályoz, a futtatást a rendszer a napi törlési feladatokból törli a futtatási előzményekből. Függetlenül attól, hogy a futási idő ki vagy fejeződik be, a megőrzési időszakot a rendszer mindig a Futtatás kezdési ideje és az *aktuális* megőrzési korlát használatával számítja ki. Tehát ha csökkenti a repülés közbeni futás időtartamának korlátját, a Futtatás időtúllépést eredményez. A Futtatás azonban marad, vagy törlődik a futtatási előzmények alapján, attól függően, hogy a Futtatás időtartama túllépte-e a megőrzési korlátot. |
   | **Várakozó** | A Futtatás nem indult el vagy szüneteltetve van, például egy olyan korábbi munkafolyamat-példány miatt, amely még fut. |
   |||

1. Ha szeretné áttekinteni az egyes lépésekhez tartozó állapotokat egy adott futtatásban, valamint a lépés bemeneteit és kimeneteit, válassza a futtatáshoz tartozó három pont (**...**) gombot, és válassza a **Futtatás megjelenítése** lehetőséget.

   ![Képernyőfelvétel: a munkafolyamat futtatási előzményeinek sora az ellipszisek gomb és a "Futtatás megjelenítése" beállítással](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   A Visual Studio Code megnyitja a figyelés nézetet, és megjeleníti a Futtatás egyes lépéseinek állapotát.

   ![Képernyőfelvétel a munkafolyamat-Futtatás egyes lépéseiről és állapotukról](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-action-status.png)

   > [!NOTE]
   > Ha a Futtatás sikertelen volt, és a figyelés nézet egy lépése megjeleníti a `400 Bad Request` hibát, a probléma oka lehet egy hosszabb Eseményindító neve vagy művelet neve, amelynek hatására az alapul szolgáló Uniform Resource Identifier (URI) túllépi az alapértelmezett karakteres korlátot. További információ: ["400 hibás kérelem"](#400-bad-request).

   A munkafolyamat egyes lépései a következő lehetséges állapotokat tartalmazhatják:

   | Művelet állapota | Ikon | Leírás |
   |---------------|------|-------------|
   | Megszakítva | ![A "megszakított" művelet állapotának ikonja][aborted-icon] | A művelet a külső problémák miatt leállt vagy nem zárult, például rendszerleállás vagy elévült Azure-előfizetés. |
   | Lemondva | ![A "megszakított" művelet állapotának ikonja][cancelled-icon] | A művelet futása megszakadt, de lemondási kérelmet kapott. |
   | Sikertelen | ![A "sikertelen" művelet állapotának ikonja][failed-icon] | A művelet sikertelen volt. |
   | Futó | ![A "futó" művelet állapotának ikonja][running-icon] | A művelet jelenleg fut. |
   | Kimarad | ![A "kihagyott" művelet állapotának ikonja][skipped-icon] | A rendszer kihagyta a műveletet, mert a közvetlenül megelőző művelet meghiúsult. Egy művelet olyan `runAfter` feltétellel rendelkezik, amely megköveteli, hogy az előző művelet sikeresen befejeződik az aktuális művelet futtatása előtt. |
   | Sikeres | ![A "sikeres" művelet állapotának ikonja][succeeded-icon] | A művelet sikeresen befejeződött. |
   | Az újrapróbálkozások sikeresek voltak | !["Sikeres újrapróbálkozások" művelet ikonja][succeeded-with-retries-icon] | A művelet sikeresen befejeződött, de csak egy vagy több újrapróbálkozás után. Az újrapróbálkozási előzmények áttekintéséhez a futtatási előzmények részletei nézetben válassza ki ezt a műveletet, így megtekintheti a bemeneteket és a kimeneteket. |
   | Időtúllépés | !["Időtúllépés" műveleti állapot ikonja][timed-out-icon] | A művelet a művelet beállításaiban megadott időkorlát miatt leállt. |
   | Várakozó | ![A "Waiting" művelet állapotának ikonja][waiting-icon] | Egy olyan webhook-műveletre vonatkozik, amely egy hívótól érkező bejövő kérésre vár. |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/waiting.png

1. Ha szeretné áttekinteni az egyes lépések bemeneteit és kimeneteit, válassza ki a megvizsgálni kívánt lépést.

   ![Képernyőfelvétel: a munkafolyamat egyes lépéseinek állapotát, valamint a kibontott "e-mail küldése" művelet bemeneteit és kimeneteit bemutató képernyőkép](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. Az adott lépéshez tartozó nyers bemenetek és kimenetek további áttekintéséhez válassza a **nyers bemenetek megjelenítése** vagy a **nyers kimenet megjelenítése** lehetőséget.

1. A hibakeresési munkamenet leállításához a **Futtatás** menüben válassza a **hibakeresés leállítása** (SHIFT + F5) lehetőséget.

<a name="return-response"></a>

## <a name="return-a-response"></a>Válasz visszaadása

Ha olyan választ szeretne visszaadni arra a hívóra, amely kérelmet küldött a logikai alkalmazásnak, a kérelem-triggerrel kezdődő munkafolyamathoz használhatja a beépített [Válasz műveletet](../connectors/connectors-native-reqres.md) .

1. A Munkafolyamat-tervezőben az **E-mail küldése** művelet alatt válassza az **új lépés** lehetőséget.

   A **válasszon műveletet** kérő üzenet jelenik meg a tervezőben, és a **művelet hozzáadása ablaktábla** újra megnyílik, így kiválaszthatja a következő műveletet.

1. Győződjön meg arról, hogy a **művelet hozzáadása** ablaktáblán a **művelet** keresése mezőben a **beépített elem** van kiválasztva. A keresőmezőbe írja be a kifejezést `response` , majd válassza ki a **Válasz** műveletet.

   ![Képernyőkép, amely a kiválasztott válasz művelettel rendelkező munkafolyamat-tervezőt jeleníti meg.](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   Ha a **Válasz** művelet megjelenik a tervezőben, a művelet részletei ablaktábla automatikusan megnyílik.

   ![Képernyőkép, amely a "válasz" művelet részleteket tartalmazó ablaktábláján megnyitva jeleníti meg a munkafolyamat-tervezőt, az "Body" tulajdonság pedig az "e-mail küldése" művelet "Body" tulajdonságának értéke.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. A **Parameters (paraméterek** ) lapon adja meg a hívni kívánt függvényhez szükséges adatokat.

   Ez a példa az **E-mail küldése** művelet eredményét adja vissza a **Body** tulajdonság értékével.

   1. Kattintson a **törzs** tulajdonságmezőbe, hogy a dinamikus tartalom lista megjelenjen, és megjeleníti a munkafolyamat előző triggerének és műveleteinek elérhető kimeneti értékeit.

      ![Képernyőfelvétel: a "válasz" művelet részleteket tartalmazó ablaktáblája a "Body" tulajdonságban található egérmutatóval, hogy megjelenjen a dinamikus tartalmak listája.](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. A dinamikus tartalom lista **E-mail küldése** területén válassza a **törzs** lehetőséget.

      ![Képernyőkép, amely a dinamikus tartalom megnyitása listát jeleníti meg. Az "e-mail küldése" fejléc alatti listában a "Body" kimeneti érték van kiválasztva.](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      Ha elkészült, a Response művelet **Body** tulajdonsága most az **E-mail küldése** művelet **törzsének** kimenete értékre van állítva.

      ![A munkafolyamat egyes lépéseinek állapotát, valamint a kibontott "válasz" művelet bemeneteit és kimeneteit megjelenítő képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. A tervezőben válassza a **Mentés** lehetőséget.

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>A logikai alkalmazás újratesztelése

A logikai alkalmazás frissítéseinek elvégzése után futtasson egy másik tesztet a hibakereső futtatásával a Visual Studióban, és küldjön egy másik kérést a frissített logikai alkalmazás elindításához, hasonlóan a [Futtatás, tesztelés és hibakeresés helyi](#run-test-debug-locally)lépéseihez.

1. A Visual Studio Code tevékenység sávján nyissa meg a **Futtatás** menüt, és válassza a **hibakeresés indítása** (F5) lehetőséget.

1. A Poster vagy a kérések létrehozására és küldésére szolgáló eszközében küldjön egy másik kérést a munkafolyamat elindításához.

1. Ha állapot-nyilvántartó munkafolyamatot hozott létre, a munkafolyamat áttekintés lapján ellenőrizze a legutóbbi Futtatás állapotát. Az egyes lépések állapotának, bemenetének és kimenetének megtekintéséhez kattintson a futtatáshoz tartozó három pontra (**...**), majd válassza a **Futtatás megjelenítése** lehetőséget.

   Például itt látható a Futtatás lépésenkénti állapota, miután a minta munkafolyamat frissült a válasz művelettel.

   ![A frissített munkafolyamat egyes lépéseinek állapotát, valamint a kibontott "válasz" művelet bemeneteit és kimeneteit bemutató képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. A hibakeresési munkamenet leállításához a **Futtatás** menüben válassza a **hibakeresés leállítása** (SHIFT + F5) lehetőséget.

<a name="deploy-azure"></a>

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban

A Visual Studio Code-ból közvetlenül közzéteheti a projektet az Azure-ban, amely az új **logikai alkalmazás (előzetes verzió)** erőforrástípus használatával helyezi üzembe a logikai alkalmazást. A Azure Functionsban található Function app-erőforráshoz hasonlóan az új erőforrástípus üzembe helyezéséhez ki kell választania egy [üzemeltetési csomagot és egy díjszabási szintet](../app-service/overview-hosting-plans.md), amelyet az üzembe helyezés során be lehet állítani. Az üzemeltetési csomagokról és a díjszabásról a következő témakörökben talál további információt:

* [Vertikális felskálázás Azure App Service](../app-service/manage-scale-up.md)
* [Az Azure Functions méretezése és üzemeltetése](../azure-functions/functions-scale.md)

A logikai alkalmazást új erőforrásként teheti közzé, amely automatikusan létrehoz minden további szükséges erőforrást, például egy [Azure Storage-fiókot, amely a Function app követelményeihez hasonló](../azure-functions/storage-considerations.md). Vagy közzéteheti a logikai alkalmazást egy korábban telepített **Logic app (előzetes verzió)** erőforráson, amely felülírja a logikai alkalmazást.

### <a name="publish-to-a-new-logic-app-preview-resource"></a>Közzététel egy új logikai alkalmazás (előzetes verzió) erőforráson

1. A Visual Studio Code tevékenység sávján válassza az Azure ikont.

1. Az **Azure: Logic apps (előzetes verzió)** panel eszköztárán válassza a **telepítés logikai alkalmazásba** lehetőséget.

   ![Az "Azure: Logic Apps (előzetes verzió)" panel és az ablaktábla eszköztárának "üzembe helyezés logikai alkalmazásba" beállítását bemutató képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. A Visual Studio Code megnyíló listából válassza a következő lehetőségek közül:

   * **Új logikai alkalmazás létrehozása (előzetes verzió) az Azure-ban** (gyors)
   * **Új logikai alkalmazás (előzetes verzió) létrehozása az Azure Advanced szolgáltatásban**
   * Korábban üzembe helyezett **Logic app (előzetes verzió)** erőforrás, ha létezik

   Ez a példa folytatja az **új logikai alkalmazás (előzetes verzió) létrehozását az Azure Advanced** szolgáltatásban.

   ![Az "Azure: Logic Apps (előzetes verzió)" panelt tartalmazó képernyőkép, amely az "új logikai alkalmazás létrehozása (előzetes verzió) az Azure-ban" elemet mutatja.](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Az új **logikai alkalmazás (előzetes verzió)** erőforrás létrehozásához kövesse az alábbi lépéseket:

   1. Adja meg az új logikai alkalmazás globálisan egyedi nevét, amely a **Logic app (előzetes verzió)** erőforráshoz használandó név. Ez a példa a következőt használja: `Fabrikam-Workflows-App`.

      ![Az "Azure: Logic Apps (előzetes verzió)" panelt megjelenítő képernyőkép, amely a létrehozandó új logikai alkalmazás nevének megadását kéri.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Válassza ki az új logikai alkalmazás üzemeltetési tervét [**app Service csomag**](../azure-functions/dedicated-plan.md) vagy [**prémium**](../azure-functions/functions-premium-plan.md). Ez a példa **app Service tervet** választ.

      ![Képernyőkép az "Azure: Logic Apps (előzetes verzió)" ablaktábláról, valamint egy "App Service-csomag" vagy "Prémium" kiválasztására vonatkozó kérdés.](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. Hozzon létre egy új App Service tervet, vagy válasszon ki egy meglévő csomagot. Ez a példa kiválasztja az **új App Service terv létrehozása** lehetőséget.

      ![Képernyőkép, amely az "Azure: Logic Apps (előzetes verzió)" panelt jeleníti meg, és rákérdez az "új App Service terv létrehozása" elemre, vagy válasszon egy meglévő App Service csomagot.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. Adja meg a App Service terv nevét, majd válassza ki a csomag [díjszabási szintjét](../app-service/overview-hosting-plans.md) . Ebben a példában az **F1 ingyenes** csomagot választjuk.

      ![Képernyőkép, amely az "Azure: Logic Apps (előzetes verzió)" ablaktáblát és egy díjszabási csomag kiválasztására szolgáló kérdést jelenít meg.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. Az optimális teljesítmény érdekében keresse meg és válassza ki ugyanazt az erőforráscsoportot, mint az üzemelő példány projektjét.

      > [!NOTE]
      > Bár létrehozhat vagy használhat egy másik erőforráscsoportot, ami hatással lehet a teljesítményre. Ha létrehoz vagy kiválaszt egy másik erőforráscsoportot, de a megerősítést kérő üzenet megjelenésekor a Mégse gombra kattint, a rendszer a központi telepítést is megszakítja.

   1. Állapot-nyilvántartó munkafolyamatok esetén válassza az **új Storage-fiók** vagy egy meglévő Storage-fiók létrehozása lehetőséget.

      ![Az "Azure: Logic Apps (előzetes verzió)" ablaktáblát és a Storage-fiók létrehozására vagy kiválasztására vonatkozó kérést bemutató képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. Ha a logikai alkalmazás létrehozása és üzembe helyezése a [Application Insights](../azure-monitor/app/app-insights-overview.md)használatával támogatott, akkor engedélyezheti a diagnosztikai naplózást és a nyomkövetést a logikai alkalmazáshoz. Ezt akkor teheti meg, ha a logikai alkalmazást a Visual Studio Code-ból vagy üzembe helyezés után telepíti. Rendelkeznie kell egy Application Insights példánnyal, de az erőforrást [előre](../azure-monitor/app/create-workspace-resource.md)is létrehozhatja a logikai alkalmazás telepítésekor vagy az üzembe helyezés után.

      A naplózás és a nyomkövetés engedélyezéséhez kövesse az alábbi lépéseket:

      1. Válasszon ki egy meglévő Application Insights-erőforrást, vagy **hozzon létre új Application Insights-erőforrást**.

      1. A [Azure Portal](https://portal.azure.com)lépjen a Application Insights erőforráshoz.

      1. Az erőforrás menüben válassza az **Áttekintés** lehetőséget. Keresse meg és másolja a kialakítási **kulcs** értékét.

      1. A Visual Studio Code-ban a projekt legfelső szintjén nyissa meg a **local.settings.js** fájlt.

      1. Az `Values` objektumban adja hozzá a `APPINSIGHTS_INSTRUMENTATIONKEY` tulajdonságot, és állítsa be az értéket a kialakítási kulcsra, például:

         ```json
         {
            "IsEncrypted": false,
            "Values": {
               "AzureWebJobsStorage": "UseDevelopmentStorage=true",
               "FUNCTIONS_WORKER_RUNTIME": "dotnet",
               "APPINSIGHTS_INSTRUMENTATIONKEY": <instrumentation-key>
            }
         }
         ```

         > [!TIP]
         > Megtekintheti, hogy az trigger és a műveletek nevei helyesen jelenjenek-e meg az Application Insights-példányban.
         >
         > 1. A Azure Portal lépjen a Application Insights erőforráshoz.
         >
         > 2. Az erőforrás-erőforrás menü **vizsgálat** területén válassza az **alkalmazás-hozzárendelés** elemet.
         >
         > 3. Tekintse át a térképen megjelenő műveletek nevét.
         >
         > Előfordulhat, hogy a beépített eseményindítók néhány bejövő kérelme ismétlődőként jelenik meg az alkalmazás-hozzárendelésben. 
         > A formátum használata helyett `WorkflowName.ActionName` ezek a duplikált elemek a munkafolyamat nevét használják a művelet neveként, és a Azure functions gazdagépről származnak.

      1. Ezután beállíthatja a logikai alkalmazás által gyűjtött és a Application Insights-példányra küldött nyomkövetési adatok súlyossági szintjét.

         Minden alkalommal, amikor egy munkafolyamat kapcsolatos esemény történik, például egy munkafolyamat indításakor vagy egy művelet futtatásakor a futtatókörnyezet különböző nyomkövetéseket bocsát ki. Ezek a Nyomkövetések a munkafolyamat élettartamát fedik le, de nem korlátozódnak a következő típusú eseményekre:

         * Szolgáltatási tevékenység, például indítás, leállítás és hibák.
         * Feladatok és diszpécser tevékenységek.
         * Munkafolyamat-tevékenység, például trigger, művelet és Futtatás.
         * Tárolási kérelem tevékenysége, például sikeres vagy sikertelen.
         * HTTP-kérelem tevékenység, például bejövő, kimenő, sikeres és sikertelen.
         * Ad hoc fejlesztési Nyomkövetések, például hibakeresési üzenetek.

         Minden eseménytípus hozzá van rendelve egy súlyossági szinthez. A `Trace` szint például rögzíti a legrészletesebb üzeneteket, míg a `Information` szint rögzíti az általános tevékenységet a munkafolyamatban, például a logikai alkalmazás, a munkafolyamat, a trigger és a műveletek elindítását és leállítását. Ez a táblázat a súlyossági szinteket és azok nyomkövetési típusait ismerteti:

         | Súlyossági szint | Nyomkövetés típusa |
         |----------------|------------|
         | Kritikus | A logikai alkalmazás helyreállíthatatlan hibáját leíró naplók. |
         | Hibakeresés | A fejlesztés során használható naplók, például a bejövő és kimenő HTTP-hívások. |
         | Hiba | A munkafolyamat-végrehajtás hibáját jelző naplók, de nem általános hiba a logikai alkalmazásban. |
         | Információ | A logikai alkalmazásban vagy munkafolyamatban az általános tevékenységet nyomon követő naplók, például: <p><p>-Ha egy trigger, művelet vagy Futtatás elindul és végződik. <br>-Ha a logikai alkalmazás elindul vagy véget ér. |
         | Nyomkövetés | A legrészletesebb üzeneteket, például tárolási kérelmeket vagy diszpécser tevékenységeket tartalmazó naplók, valamint a munkafolyamat-végrehajtási tevékenységgel kapcsolatos összes üzenet. |
         | Figyelmeztetés | A logikai alkalmazás rendellenes állapotát kiemelő naplók, de nem akadályozza a futását. |
         |||

         A súlyossági szint megadásához a projekt legfelső szintjén nyissa meg a **host.js** fájlt, és keresse meg az `logging` objektumot. Ez az objektum vezérli a logikai alkalmazás összes munkafolyamatának naplózási szűrését, és a [naplózási típus szűrésének ASP.net Core elrendezését](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering)követi.

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               }
            }
         }
         ```

         Ha az `logging` objektum nem tartalmaz olyan `logLevel` objektumot, amely tartalmazza a `Host.Triggers.Workflow` tulajdonságot, adja hozzá ezeket az elemeket. Állítsa a tulajdonságot a kívánt nyomkövetési típus súlyossági szintjére, például:

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               },
               "logLevel": {
                  "Host.Triggers.Workflow": "Information"
               }
            }
         }
         ```

   Ha elkészült az üzembe helyezés lépéseivel, a Visual Studio Code elindítja a logikai alkalmazás közzétételéhez szükséges erőforrások létrehozását és üzembe helyezését.

1. A telepítési folyamat áttekintéséhez és figyeléséhez a **nézet** menüben válassza a **kimenet** lehetőséget. A kimeneti ablak eszköztárán válassza a **Azure Logic apps** lehetőséget.

   ![Képernyőfelvétel: az eszköztár listájában kiválasztott "Azure Logic Apps" kimeneti ablak a telepítési folyamattal és állapotokkal együtt.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Ha a Visual Studio Code befejezi a logikai alkalmazás üzembe helyezését az Azure-ba, a következő üzenet jelenik meg:

   ![Képernyőkép, amely az Azure-ba való központi telepítés sikeres befejezését jelző üzenetet jelenít meg.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Gratulálunk, a logikai alkalmazás most már az Azure-ban él, és alapértelmezés szerint engedélyezve van.

Ezután megtudhatja, hogyan hajthatja végre ezeket a feladatokat:

* [Adjon hozzá egy üres munkafolyamatot a projekthez](#add-workflow-existing-project).

* Az [üzembe helyezett logikai alkalmazások kezelése a Visual Studio Code-ban](#manage-deployed-apps-vs-code) vagy a [Azure Portal](#manage-deployed-apps-portal)használatával.

* [Futtatási előzmények engedélyezése állapot nélküli munkafolyamatokon](#enable-run-history-stateless).

* [Az üzembe helyezett logikai alkalmazások Azure Portal figyelés nézetének engedélyezése](#enable-monitoring).

<a name="add-workflow-existing-project"></a>

## <a name="add-blank-workflow-to-project"></a>Üres Munkafolyamat hozzáadása a projekthez

Több munkafolyamattal is rendelkezhet a logikai alkalmazás projektjeiben. Ha üres munkafolyamatot szeretne hozzáadni a projekthez, kövesse az alábbi lépéseket:

1. A Visual Studio Code tevékenység sávján válassza az Azure ikont.

1. Az Azure ablaktáblán, az **Azure: Logic apps (előzetes verzió)** mellett válassza a **munkafolyamat létrehozása** (Azure Logic apps) lehetőséget.

1. Válassza ki azt a munkafolyamat-típust, amelyet hozzá szeretne adni, **állapotának** vagy **állapot nélkülinek** kell lennie.

1. Adja meg a munkafolyamat nevét.

Ha elkészült, egy új munkafolyamat-mappa jelenik meg a projektben, valamint egy **workflow.jsa** fájlban a munkafolyamat-definícióhoz.

<a name="manage-deployed-apps-vs-code"></a>

## <a name="manage-deployed-logic-apps-in-visual-studio-code"></a>Üzembe helyezett logikai alkalmazások kezelése a Visual Studio Code-ban

A Visual Studio Code-ban az összes telepített Logic apps megtekinthető az Azure-előfizetésben, függetlenül attól, hogy az eredeti **Logic apps** vagy a **logikai alkalmazás (előzetes verzió)** erőforrástípus, valamint olyan feladatokat választ, amelyek segítenek a logikai alkalmazások kezelésében. Mindkét erőforrástípus eléréséhez azonban szükség van a Visual Studio Code **Azure Logic apps** és a **Azure Logic apps (előzetes verzió)** bővítményekre is.

1. A bal oldali eszköztáron válassza az Azure ikont. Az **Azure: Logic apps (előzetes verzió)** ablaktáblán bontsa ki az előfizetését, amely megjeleníti az előfizetéshez tartozó összes telepített logikai alkalmazást.

1. Keresse meg és válassza ki a kezelni kívánt logikai alkalmazást. Nyissa meg a logikai alkalmazás helyi menüjét, és válassza ki a végrehajtani kívánt feladatot.

   Kijelölhet például olyan feladatokat, mint például a telepített logikai alkalmazás leállítása, elindítása, újraindítása vagy törlése.

   ![Képernyőfelvétel: a megnyitott "Azure Logic Apps (előzetes verzió)" kiterjesztési panel és az üzembe helyezett munkafolyamat megnyitása a Visual Studio Code-ban.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. A logikai alkalmazás összes munkafolyamatának megtekintéséhez bontsa ki a logikai alkalmazást, majd bontsa ki a **munkafolyamatok** csomópontot.

1. Egy adott munkafolyamat megtekintéséhez nyissa meg a munkafolyamat helyi menüjét, és válassza a **Megnyitás a tervezőben** lehetőséget, amely csak olvasható módban nyitja meg a munkafolyamatot.

   A munkafolyamat szerkesztéséhez a következő lehetőségek közül választhat:

   * A Visual Studio Code-ban nyissa meg a projekt **workflow.jsját** a Munkafolyamat-tervezőben, végezze el a módosításokat, majd telepítse újra a logikai alkalmazást az Azure-ba.

   * A Azure Portal keresse meg [és nyissa meg a logikai alkalmazást](#manage-deployed-apps-portal). A munkafolyamat megkeresése, szerkesztése és mentése.

1. Az üzembe helyezett logikai alkalmazás a Azure Portalban való megnyitásához nyissa meg a logikai alkalmazás helyi menüjét, és válassza a **Megnyitás a portálon** lehetőséget.

   A Azure Portal megnyílik a böngészőben, és automatikusan bejelentkezik a portálra, ha bejelentkezett a Visual Studio Code-ba, és megjeleníti a logikai alkalmazást.

   ![Képernyőkép, amely a logikai alkalmazás Azure Portal lapját mutatja a Visual Studio Code-ban.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Külön is bejelentkezhet a Azure Portalba, a portál keresőmezőt használva megkeresheti a logikai alkalmazást, majd kiválaszthatja a logikai alkalmazást az eredmények listából.

   ![Képernyőfelvétel: a Azure Portal és a keresősáv megjelenítése a kiválasztott logikai alkalmazás keresési eredményeivel, amely kiválasztva jelenik meg.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="manage-deployed-apps-portal"></a>

## <a name="manage-deployed-logic-apps-in-the-portal"></a>Üzembe helyezett logikai alkalmazások kezelése a portálon

A Azure Portal az Azure-előfizetésében lévő összes telepített Logic apps megtekinthető, függetlenül attól, hogy az eredeti **Logic apps** erőforrástípus vagy a **logikai alkalmazás (előzetes verzió)** típusú erőforrástípus. Az egyes erőforrástípusok jelenleg külön kategóriákként vannak rendszerezve és kezelve az Azure-ban. Az alábbi lépéseket követve megkeresheti a **Logic app (előzetes verzió)** típusú erőforrásokkal rendelkező logikai alkalmazásokat:

1. A Azure Portal keresőmezőbe írja be a kifejezést `logic app preview` . Amikor megjelenik az eredmények listája, a **szolgáltatások** területen válassza a **logikai alkalmazás (előzetes verzió)** lehetőséget.

   ![A "Logic app Preview" keresési szöveget megjelenítő Azure Portal keresőmezőt bemutató képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. A **logikai alkalmazás (előzetes verzió)** ablaktáblán keresse meg és válassza ki a Visual Studio Code-ból üzembe helyezett logikai alkalmazást.

   ![Képernyőkép, amely az Azure-ban üzembe helyezett Azure Portal és logikai alkalmazás (előzetes verzió) erőforrásait mutatja be.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   A Azure Portal megnyitja a kiválasztott logikai alkalmazás egyedi erőforrás-lapját.

   ![A logikai alkalmazás munkafolyamatának erőforrás-lapját megjelenítő képernyőkép a Azure Portal.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. A logikai alkalmazás munkafolyamatainak megtekintéséhez a logikai alkalmazás menüjében válassza a **munkafolyamatok** lehetőséget.

   A **munkafolyamatok** ablaktábla megjeleníti az aktuális logikai alkalmazás összes munkafolyamatát. Ez a példa a Visual Studio Code-ban létrehozott munkafolyamatot mutatja be.

   ![Képernyőkép: a "Logic app (előzetes verzió)" erőforrás-oldal, amelyen a "munkafolyamatok" ablaktábla meg van nyitva, és az üzembe helyezett munkafolyamat](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. Munkafolyamat megtekintéséhez a **munkafolyamatok ablaktáblán** válassza ki a munkafolyamatot.

   Megnyílik a munkafolyamat panel, és megjeleníti az adott munkafolyamaton végrehajtható további információkat és feladatokat.

   Ha például a munkafolyamat lépéseit szeretné megtekinteni, válassza a **tervező** lehetőséget.

   ![Képernyőfelvétel: a kiválasztott munkafolyamat "áttekintés" panelje, míg a munkafolyamat menü a kiválasztott "Designer" parancsot jeleníti meg.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   Megnyílik a Munkafolyamat-tervező, és megjeleníti a Visual Studio Code-ban létrehozott munkafolyamatot. Most már módosíthatja a munkafolyamatot a Azure Portalban.

   ![A Visual Studio Code-ból üzembe helyezett munkafolyamat-tervezőt és munkafolyamatot bemutató képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflow-portal"></a>

## <a name="add-another-workflow-in-the-portal"></a>Másik munkafolyamat hozzáadása a portálon

A Azure Portal használatával üres munkafolyamatokat adhat hozzá a Visual Studio Code-ból üzembe helyezett **logikai alkalmazás (előzetes verzió)** erőforráshoz, és felépítheti ezeket a munkafolyamatokat a Azure Portal.

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki a telepített **Logic app (előzetes verzió)** erőforrást.

1. A logikai alkalmazás menüjében válassza a **munkafolyamatok** lehetőséget. A **munkafolyamatok** ablaktáblán válassza a **Hozzáadás** lehetőséget.

   ![A kiválasztott logikai alkalmazás "munkafolyamatok" paneljét és az eszköztárat a "Hozzáadás" parancs kiválasztásával ábrázoló képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. Az **Új munkafolyamat** ablaktáblán adja meg a munkafolyamat nevét. Válassza ki az **állapot-nyilvántartó** vagy az **állapot nélküli** **>** **Létrehozás** elemet.

   Miután az Azure üzembe helyezte az új munkafolyamatot, amely megjelenik a **munkafolyamatok** ablaktáblán, válassza ki ezt a munkafolyamatot, hogy kezelje és végrehajtsa a többi feladatot, például a tervező vagy a kód nézet megnyitását.

   ![Képernyőkép, amely a kiválasztott munkafolyamatot jeleníti meg a felügyeleti és felülvizsgálati beállításokkal.](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Ha például egy új munkafolyamat tervezőjét nyitja meg, egy üres vászon jelenik meg. Most már létrehozhatja ezt a munkafolyamatot a Azure Portal.

   ![A munkafolyamat-tervezőt és egy üres munkafolyamatot bemutató képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Állapot nélküli munkafolyamatok futtatási előzményeinek engedélyezése

Az állapot nélküli munkafolyamatok egyszerűbb hibakereséséhez engedélyezheti a munkafolyamat futtatási előzményeit, majd letilthatja a futtatási előzményeket, ha elkészült. Kövesse ezeket a lépéseket a Visual Studio Code-ban, vagy ha a Azure Portal dolgozik, tekintse meg [az állapot-nyilvántartó és állapot nélküli munkafolyamatok létrehozása a Azure Portalban](create-stateful-stateless-workflows-azure-portal.md#enable-run-history-stateless)című témakört.

1. A Visual Studio Code projektben bontsa ki a **munkafolyamat-designtime** mappát, és nyissa meg a **local.settings.js** fájlt.

1. Adja hozzá a `Workflows.{yourWorkflowName}.operationOptions` tulajdonságot, és állítsa be az értéket `WithStatelessRunHistory` , például:

   **Windows vagy Linux**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

   **macOS**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct; \
             AccountKey=<access-key>;EndpointSuffix=core.windows.net",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. Ha végzett a futtatási előzmények letiltásával, állítsa be a `Workflows.{yourWorkflowName}.OperationOptions` tulajdonságot a értékre `None` , vagy törölje a tulajdonságot és annak értékét.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-view-in-the-azure-portal"></a>Figyelési nézet engedélyezése a Azure Portal

Miután telepítette a **Logic app (előzetes verzió)** erőforrást a Visual Studio Code-ból az Azure-ba, áttekintheti az adott erőforráshoz tartozó munkafolyamatok összes elérhető futási előzményeit és részleteit az adott munkafolyamathoz tartozó Azure Portal és a **figyelési** élmény használatával. Először azonban engedélyeznie kell a **figyelő** nézet képességeit a logikai alkalmazás erőforrásán.

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki a telepített **Logic app (előzetes verzió)** erőforrást.

1. Az erőforrás menüjében, az **API** alatt válassza a **CORS** lehetőséget.

1. A **CORS** ablaktábla **engedélyezett eredetek** területén adja hozzá a helyettesítő karaktert (*).

1. Ha elkészült, a **CORS** eszköztárán válassza a **Mentés** lehetőséget.

   ![Képernyőkép, amely megjeleníti a Azure Portal egy üzembe helyezett Logic app (előzetes verzió) erőforrással. Az erőforrás menüben a "CORS" lehetőség van kiválasztva egy új bejegyzéssel az "Allowed Origins" értékre, amely a "*" karaktert helyettesítő karakterre van állítva.](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Application Insights engedélyezése vagy megnyitása az üzembe helyezés után

A munkafolyamat végrehajtása során a logikai alkalmazás más eseményekkel együtt bocsát ki telemetria. Ennek a telemetria a segítségével jobban megismerheti a munkafolyamat futását, valamint azt, hogy a Logic Apps futtatókörnyezet hogyan működik különböző módokon. [Application Insights](../azure-monitor/app/app-insights-overview.md)használatával figyelheti a munkafolyamatot, amely közel valós idejű telemetria (élő metrikákat) biztosít. Ezzel a képességgel könnyebben vizsgálhatja meg a hibákat és a teljesítménnyel kapcsolatos problémákat, amikor ezeket az adatait a problémák diagnosztizálásához, a riasztások beállításához és a diagramok létrehozásához használja.

Ha a logikai alkalmazás létrehozása és üzembe helyezése a [Application Insights](../azure-monitor/app/app-insights-overview.md)használatával támogatott, akkor engedélyezheti a diagnosztikai naplózást és a nyomkövetést a logikai alkalmazáshoz. Ezt akkor teheti meg, ha a logikai alkalmazást a Visual Studio Code-ból vagy üzembe helyezés után telepíti. Rendelkeznie kell egy Application Insights példánnyal, de az erőforrást [előre](../azure-monitor/app/create-workspace-resource.md)is létrehozhatja a logikai alkalmazás telepítésekor vagy az üzembe helyezés után.

Ha a Application Insightst egy telepített logikai alkalmazásban szeretné engedélyezni, vagy ha már engedélyezve van az Application Insights adatai, kövesse az alábbi lépéseket:

1. A Azure Portal keresse meg a telepített logikai alkalmazást.

1. A logikai alkalmazás menü **Beállítások** területén válassza a **Application Insights** lehetőséget.

1. Ha Application Insights nincs engedélyezve, a **Application Insights** panelen válassza a **bekapcsolás Application Insights** lehetőséget. A panel frissítéseinek alján kattintson az **alkalmaz** gombra.

   Ha a Application Insights engedélyezve van, akkor a **Application Insights** ablaktáblán válassza az **Application Insights-adatbázis megtekintése** lehetőséget.

Application Insights megnyitása után áttekintheti a logikai alkalmazás különböző mérőszámait. További információ: [Azure Logic apps fut bárhol-Monitor Application Insights – 1. rész](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849).

<a name="deploy-docker"></a>

## <a name="deploy-to-docker"></a>Üzembe helyezés a Docker-ben

A logikai alkalmazást üzembe helyezheti egy [Docker-tárolóban](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) üzemeltetési környezetként a [.net CLI](/dotnet/core/tools/)használatával. Ezekkel a parancsokkal létrehozhatja és közzéteheti a logikai alkalmazás projektjét. Ezután létrehozhatja és futtathatja a Docker-tárolót a logikai alkalmazás üzembe helyezésének célhelye.

Ha még nem ismeri a Docker-t, tekintse át a következő témaköröket:

* [Mi a Docker?](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)
* [A tárolók és a Docker bemutatása](/dotnet/architecture/microservices/container-docker-introduction/)
* [A .NET és a Docker bemutatása](/dotnet/core/docker/introduction)
* [Docker-tárolók, lemezképek és kibocsátásiegység-forgalmi jegyzékek](/dotnet/architecture/microservices/container-docker-introduction/docker-containers-images-registries)
* [Oktatóanyag: Bevezetés a Docker használatába (Visual Studio Code)](/visualstudio/docker/tutorials/docker-tutorial)

### <a name="requirements"></a>Követelmények

* A logikai alkalmazás által az üzembe helyezéshez használt Azure Storage-fiók

* Docker-fájl egy olyan .NET-munkafolyamathoz, amelyet a Docker-tároló létrehozásakor használ

   Ez a minta Docker-fájl például üzembe helyez egy logikai alkalmazást egy állapot-nyilvántartó munkafolyamattal. A fájl megadja a logikai alkalmazás Azure Portal való közzétételéhez használt Azure Storage-fiókhoz tartozó kapcsolati karakterláncot és hozzáférési kulcsot.

   ```text
   FROM mcr.microsoft.com/dotnet/core/sdk3.1 AS installer-env

   COPY . /src/dotnet-function-app
   RUN cd /src/dotnet-function-app && \
       mkdir -p /home/site/wwwroot && \
       dotnet publish *.csproj --output /home/site/wwwroot

   FROM mcr.microsoft.com/azure-functions/dotnet:3.0
   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
       AzureFunctionsJobHost__Logging__Console__IsEnabled=true \
       FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY --from=installer-env ["/home/site/wwwroot", "/home/site/wwwroot"]
   ```

   További információ: [ajánlott eljárások a Docker-fájlok írásához](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

### <a name="build-and-publish-your-app"></a>Az alkalmazás létrehozása és közzététele

1. Ha helyileg szeretné felépíteni a logikai alkalmazás projektjét, nyisson meg egy parancssort, és futtassa a következő parancsot:

   `dotnet build -c release`

   További információkért lásd a DotNet- [Build](/dotnet/core/tools/dotnet-build/) referenciáját ismertető oldalt.

1. A következő parancs futtatásával közzéteheti a projekt buildjét egy olyan mappába, amely az üzemeltetési környezetben való üzembe helyezéshez használható:

   `dotnet publish -c release`

   További információkért tekintse meg a [DotNet publish](/dotnet/core/tools/dotnet-publish/) -referenciát ismertető oldalt.

### <a name="access-to-your-storage-account"></a>Hozzáférés a Storage-fiókhoz

A Docker-tároló létrehozása és futtatása előtt be kell szereznie a Storage-fiók hozzáférési kulcsait tartalmazó kapcsolati karakterláncot.

1. A Azure Portal a Storage-fiók menü **Beállítások** területén válassza a **hozzáférési kulcsok** elemet. 

   ![Képernyőkép, amely megjeleníti a Azure Portal a Storage-fiók hozzáférési kulcsainak és a kapcsolati karakterláncnak a másolásával.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

1. A **kapcsolatok karakterlánca** alatt másolja ki a Storage-fiókhoz tartozó kapcsolatok karakterláncát. A kapcsolódási karakterlánc a következőhöz hasonlóan néz ki:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey={access-key};EndpointSuffix=core.windows.net`

   További információkért tekintse át a [Storage-fiók kulcsainak kezelése](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)című témakört.

1. Mentse a kapcsolatok karakterláncát biztonságos helyre. A Logic app-projektben ezt a karakterláncot kell hozzáadnia **local.settings.js** fájlokhoz. Ezt a karakterláncot is hozzá kell adnia a Docker-fájlhoz.

### <a name="build-and-run-your-docker-container-image"></a>Docker-tároló rendszerképének létrehozása és futtatása

1. Hozza létre a Docker-tároló rendszerképét a Docker-fájl használatával, és futtassa a következő parancsot:

   `docker build --tag local/workflowcontainer .`

   További információ: [Docker Build](https://docs.docker.com/engine/reference/commandline/build/).

1. Mentse a karakterláncot biztonságos helyre, hogy később hozzáadja a **local.settings.js** karakterláncot a projektben található azon fájlokhoz, amelyeket a logikai alkalmazás Visual Studio Code-ban való létrehozásához használ.

1. Futtassa helyileg a tárolót a következő paranccsal:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   További információ: [Docker Run](https://docs.docker.com/engine/reference/commandline/run/).

### <a name="get-callback-url-for-request-trigger"></a>Visszahívás URL-címének beolvasása kérelem-triggerhez

A kérelem-trigger visszahívási URL-címének beszerzéséhez küldje el a kérelmet:

`POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2020-05-01-preview&code={master-key}`

Az <*Master-key*> érték az Azure Storage-fiókban van definiálva, amelyet a `AzureWebJobsStorage` fájl, az **Azure-webjobs-Secrets/{Deployment-Name}/host.jsa** fájlban beállított, ahol megtalálhatja az értéket ebben a szakaszban:

```json
{
   <...>
   "masterKey": {
      "name": "master",
      "value": "<master-key>",
      "encrypted": false
   },
   <...>
   }
```

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Elemek törlése a tervezőből

Ha törölni szeretne egy elemet a munkafolyamatból a tervezőből, kövesse az alábbi lépéseket:

* Jelölje ki az elemet, nyissa meg az elem helyi menüjét (Shift + F10), majd válassza a **Törlés** lehetőséget. A megerősítéshez válassza az **OK** lehetőséget.

* Jelölje ki az elemet, majd nyomja le a DELETE billentyűt. A megerősítéshez válassza az **OK** lehetőséget.

* Válassza ki az elemet, hogy a részleteket tartalmazó ablaktábla megnyíljon az adott elemhez. Az ablaktábla jobb felső sarkában nyissa meg az ellipszisek (**..**.) menüt, és válassza a **Törlés** lehetőséget. A megerősítéshez válassza az **OK** lehetőséget.

  ![Képernyőfelvétel: a megnyitott részletek ablaktáblával és a kijelölt ellipszisek és a "Törlés" paranccsal megjelenített kiválasztott elem a tervezőben.](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

  > [!TIP]
  > Ha az ellipszisek menü nem látható, bontsa ki a Visual Studio Code ablakát, hogy a részleteket tartalmazó ablaktábla a jobb felső sarokban megjelenjen az ellipszisek (**..**.) gomb.

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>Hibák és problémák elhárítása

<a name="designer-fails-to-open"></a>

### <a name="opening-designer-fails-with-error-workflow-design-time-could-not-be-started"></a>A tervező megnyitása a következő hibával meghiúsul: "a munkafolyamat tervezési ideje nem indítható el"

1. A Visual Studio Code-ban nyissa meg a kimeneti ablakot. A **nézet** menüben válassza a **kimenet** lehetőséget.

1. A kimeneti ablak címsorában lévő listából válassza a **Azure Logic apps (előzetes verzió)** lehetőséget, hogy a bővítmény kimenetét át tudja tekinteni, például:

   ![A "Azure Logic Apps" kijelölt kimeneti ablakot megjelenítő képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

1. Tekintse át a kimenetet, és ellenőrizze, hogy megjelenik-e a hibaüzenet:

   ```text
   A host error has occurred during startup operation '{operationID}'.
   System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
   ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.1\bin\
   DurableTask.AzureStorage.dll' already exists.
   Value cannot be null. (Parameter 'provider')
   Application is shutting down...
   Initialization cancellation requested by runtime.
   Stopping host...
   Host shutdown completed.
   ```

   Ez a hiba akkor fordulhat elő, ha korábban megpróbálta megnyitni a tervezőt, majd megszüntette vagy törölte a projektet. Ennek a hibának a megoldásához törölje a **ExtensionBundles** mappát ezen a helyen **. ..\Users \\ {your-username} \AppData\Local\Temp\Functions\ExtensionBundles**, majd próbálja **meg** újra megnyitni aworkflow.jsfájlt a tervezőben.

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>Az új eseményindítók és műveletek hiányoznak a tervező választóból a korábban létrehozott munkafolyamatokhoz

Azure Logic Apps előzetes verzió támogatja a beépített műveleteket az Azure functions műveletekhez, a folyékony műveletekhez és az XML-műveletekhez, például az **XML-hitelesítéshez** és az **XML-átalakításhoz**. A korábban létrehozott Logic apps esetében azonban előfordulhat, hogy ezek a műveletek nem jelennek meg a tervező választóban, így kiválaszthatja, hogy a Visual Studio Code a kiterjesztési csomag elavult verzióját használja-e `Microsoft.Azure.Functions.ExtensionBundle.Workflows` .

Emellett az **Azure Function Operations** Connector és a műveletek nem jelennek meg a tervező választóban, kivéve, ha engedélyezte vagy bejelölte az **Azure-összekötők használatát** a logikai alkalmazás létrehozásakor. Ha nem engedélyezte az Azure-központilag telepített összekötők telepítését az alkalmazás létrehozásakor, a Visual Studio Code-ban engedélyezheti azokat a projektben. Nyissa meg a **workflow.jsa** helyi menüben, majd válassza az **Összekötők használata az Azure-ból** lehetőséget.

Az elavult köteg kijavításához kövesse az alábbi lépéseket az elavult köteg törléséhez, amely lehetővé teszi, hogy a Visual Studio Code automatikusan frissítse a bővítmény-csomagot a legújabb verzióra.

> [!NOTE]
> Ez a megoldás csak azokra a logikai alkalmazásokra vonatkozik, amelyeket a Visual Studio Code használatával hoz létre és telepít a Azure Logic Apps (előzetes verzió) bővítménnyel, nem pedig a Azure Portal használatával létrehozott logikai alkalmazásokkal. [A Azure Portalban található tervezőből hiányzik a támogatott eseményindítók és műveletek](create-stateful-stateless-workflows-azure-portal.md#missing-triggers-actions).

1. Mentse el az összes olyan munkát, amelyet el szeretne veszíteni, és zárjuk be a Visual studiót.

1. A számítógépen keresse meg a következő mappát, amely a meglévő csomag verziószámmal ellátott mappáit tartalmazza:

   `...\Users\{your-username}\.azure-functions-core-tools\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Törölje a korábbi csomag verziószámát, például ha rendelkezik a 1.1.3-es verzió mappájával, törölje a mappát.

1. Most keresse meg a következő mappát, amely a szükséges NuGet-csomag verziószámú mappáit tartalmazza:

   `...\Users\{your-username}\.nuget\packages\microsoft.azure.workflows.webjobs.extension`

1. Törölje a korábbi csomag verziószámát, például ha rendelkezik egy, a 1.0.0.8 előzetes verziójával rendelkező mappával, törölje a mappát.

1. Nyissa meg újra a Visual Studio Code, a projekt és a **workflow.jst** a Designer fájljában.

A hiányzó eseményindítók és műveletek mostantól megjelennek a tervezőben.

<a name="400-bad-request"></a>

### <a name="400-bad-request-appears-on-a-trigger-or-action"></a>"400 hibás kérelem" jelenik meg egy triggeren vagy műveleten

Ha egy Futtatás meghiúsul, és megvizsgálja a Futtatás figyelés nézetben nézetet, a hiba megjelenhet egy hosszabb nevű eseményindítón vagy műveleten, ami miatt a mögöttes Uniform Resource Identifier (URI) túllépi az alapértelmezett karakteres korlátot.

A probléma megoldásához és a hosszú URI-ra való igazításhoz módosítsa a `UrlSegmentMaxCount` számítógépen a és a `UrlSegmentMaxLength` beállításkulcsokat a következő lépésekkel. A kulcs alapértelmezett értékeit a témakör ismerteti, [Http.sys beállításjegyzék-beállításokat a Windows rendszerhez](/troubleshoot/iis/httpsys-registry-windows).

> [!IMPORTANT]
> Mielőtt elkezdené, győződjön meg róla, hogy mentette a munkáját. Ehhez a megoldáshoz újra kell indítani a számítógépet, miután elkészült, hogy a módosítások érvénybe lépnek.

1. A számítógépén nyissa meg a **Futtatás** ablakot, és futtassa a `regedit` parancsot, amely megnyitja a beállításszerkesztőt.

1. A **felhasználói fiókok felügyelete** mezőben válassza az **Igen** lehetőséget a számítógép módosításainak engedélyezéséhez.

1. A bal oldali ablaktábla **számítógép** területén bontsa ki a csomópontokat az elérési út mentén, **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters**, majd válassza a **Parameters (paraméterek**) lehetőséget.

1. A jobb oldali ablaktáblában keresse meg a `UrlSegmentMaxCount` és a `UrlSegmentMaxLength` beállításkulcsot.

1. Növelje ezeket a kulcs-értékeket úgy, hogy az URI-k a használni kívánt neveket is elférjenek. Ha ezek a kulcsok nem léteznek, adja hozzá őket a **Parameters (paraméterek** ) mappához a következő lépésekkel:

   1. A **Paraméterek** helyi menüjében válassza az **új**  >  **Duplaszó (32 bites) értéket**.

   1. A megjelenő szerkesztés mezőben adja meg `UrlSegmentMaxCount` az új kulcs nevét.

   1. Nyissa meg az új kulcs helyi menüjét, és kattintson a **módosítás** lehetőségre.

   1. A megjelenő **karakterlánc szerkesztése** mezőben adja meg a hexadecimális vagy decimális formátumban használni kívánt **Value** Key értéket. Például `400` a hexadecimális érték a decimális értékkel egyenlő `1024` .

   1. A `UrlSegmentMaxLength` kulcs értékének hozzáadásához ismételje meg ezeket a lépéseket.

   A kulcsok értékének növelését vagy hozzáadását követően a Beállításszerkesztő a következő példához hasonló:

   ![A Rendszerleíróadatbázis-szerkesztőt bemutató képernyőkép.](media/create-stateful-stateless-workflows-visual-studio-code/edit-registry-settings-uri-length.png)

1. Ha elkészült, indítsa újra a számítógépet, hogy a módosítások érvénybe lépnek.

## <a name="next-steps"></a>További lépések

Szeretnénk hallani a Azure Logic Apps (előzetes verzió) bővítménnyel kapcsolatos tapasztalatairól.

* Hibák vagy problémák esetén [hozza létre a problémákat a githubon](https://github.com/Azure/logicapps/issues).
* Kérdések, kérések, megjegyzések és egyéb visszajelzések esetén [használja ezt a visszajelzési űrlapot](https://aka.ms/lafeedback).