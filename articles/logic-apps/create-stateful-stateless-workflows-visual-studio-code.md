---
title: Automatizálási munkafolyamatok létrehozása (előzetes verzió) a Visual Studio Code-ban
description: Állapot nélküli vagy állapot-nyilvántartó automatizálási munkafolyamatok létrehozása a Visual Studio Code Azure Logic Apps (előzetes verzió) bővítményével az alkalmazások, az adatközpontok, a Cloud Services és a helyszíni rendszerek integrálásához
services: logic-apps
ms.suite: integration
ms.reviewer: deli, rohitha, vikanand, hongzili, sopai, absaafan, logicappspm
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: abb6f8bcaa3b8e356bea00185702bc0ae783e071
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91270248"
---
# <a name="create-stateful-or-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Állapot-nyilvántartó vagy állapot nélküli munkafolyamatok létrehozása a Visual Studio Code-ban a Azure Logic Apps (előzetes verzió) bővítménnyel

> [!IMPORTANT]
> Ez a funkció nyilvános előzetes verzióban érhető el, és szolgáltatói szerződés nélkül van megadva, és nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az alkalmazásokban, az adattárakban, a Cloud Servicesben és a rendszerekben integrálható logikai alkalmazások munkafolyamatainak létrehozásához használhatja a Visual Studio Code és a Azure Logic Apps (előzetes verzió) bővítményt az [ *állapot* -nyilvántartó és *állapot nélküli* logikai alkalmazások munkafolyamatainak](#stateful-stateless) fejlesztési környezetbe való kiépítéséhez és helyi futtatásához.

![A Visual Studio Code és a Logic app-munkafolyamatot bemutató képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

A nyilvános előzetes bővítménnyel létrehozott logikai alkalmazások az új **logikai alkalmazás (előzetes verzió)** típusú erőforrástípust használják, és a helyi környezetben a [Azure functions](../azure-functions/functions-overview.md) futtatókörnyezet működteti. Ez az új erőforrástípus több munkafolyamatot is tartalmazhat, és a **függvényalkalmazás** erőforrástípus valamilyen módon hasonló, amely több függvényt is tartalmazhat.

Eközben az eredeti **Logic apps** erőforrástípus még létezik a Visual Studio Code-ban és a Azure Portal-ban való létrehozásához és használatához. Az eredeti erőforrástípus tapasztalatai azonban eltérnek az új erőforrás típusától. Jelenleg a **Logic apps** és a **Logic app (előzetes verzió)** típusú erőforrástípusok is létezhetnek egyszerre a Visual Studio Code-ban és a Azure Portalban. Az Azure-előfizetésben megtekintheti és elérheti az összes telepített Logic apps-alkalmazást, de úgy tűnik, hogy a saját kategóriákban és szakaszban külön tárolja őket.

Ez a cikk átfogó áttekintést nyújt erről a [nyilvános előzetes](#whats-new)verzióról, ismerteti a **logikai alkalmazás (előzetes verzió)** erőforrástípus különböző szempontjait, valamint azt, hogyan hozhatja létre ezt az erőforrást a Visual Studio Code használatával:

* Az állapot [-nyilvántartó és az állapot nélküli](#stateful-stateless) logikai alkalmazások különböznek egymástól.

* A [telepítési követelmények](#prerequisites) teljesítése és a [Visual Studio Code beállítása](#set-up) a nyilvános előzetes verziójú bővítményhez.

* Új **logikai alkalmazás (előzetes verzió)** munkafolyamatok [létrehozása projekt létrehozásával és munkafolyamat-sablon kiválasztásával](#create-project).

* Az új logikai alkalmazások helyi futtatása és hibakeresése a Visual Studio Code-ban.

* Hogyan teheti közzé ezeket az új Logic apps-alkalmazásokat közvetlenül a Visual Studio Code-ból az [Azure](#publish-azure) -ba vagy egy tetszőleges helyre futtatható [Docker-tárolóba](#deploy-docker) . További információ a Docker-ról: [Mi az a Docker](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)?

<a name="whats-new"></a>

## <a name="whats-in-this-public-preview"></a>Mi ebben a nyilvános előzetes verzióban?

A Azure Logic Apps (előzetes verzió) bővítmény számos aktuális és további Logic Apps képességet biztosít a Visual Studio Code helyi fejlesztési élményéhez, például:

* Logikai alkalmazásokat hozhat létre az integrációs és automatizálási munkafolyamatokhoz, amelyeken a [390 +-összekötők](/connectors/connector-reference/connector-reference-logicapps-connectors) a szolgáltatott szoftveres (SaaS) és a szolgáltatásként nyújtott platform-(és-) szolgáltatások, valamint az összekötők a helyszíni rendszerekhez.

  * Egyes felügyelt összekötők, például az Azure Service Bus, az Azure Event Hubs és a SQL Server a beépített natív eseményindítókkal és műveletekkel, például a HTTP-művelettel futnak.

  * Olyan logikai alkalmazásokat hozhat létre és helyezhet üzembe, amelyek bárhol futhatnak, mivel a Azure Logic Apps szolgáltatás közös hozzáférésű aláírási (SAS) kapcsolati karakterláncokat hoz létre, amelyeket ezek a logikai alkalmazások a felhőalapú kapcsolati futtatókörnyezet végpontjának küldött kérelmek küldésére használhatnak. A Logic Apps szolgáltatás menti ezeket a kapcsolódási karakterláncokat más Alkalmazásbeállítások segítségével, így egyszerűen tárolhatja ezeket az értékeket Azure Key Vault az Azure-ba való üzembe helyezéskor.

    > [!NOTE]
    > Alapértelmezés szerint a **logikai alkalmazás (előzetes verzió)** erőforrásának a [rendszer által hozzárendelt felügyelt identitása](../logic-apps/create-managed-service-identity.md) automatikusan engedélyezve van a kapcsolatok futásidőben történő hitelesítéséhez. Ez az identitás különbözik a kapcsolatok létrehozásakor használt hitelesítő adatoktól vagy a kapcsolódási karakterlánctól. Ha letiltja ezt az identitást, a kapcsolatok nem működnek futásidőben.

* Olyan állapot nélküli logikai alkalmazásokat hozhat létre, amelyek csak a memóriában futnak, így gyorsabban, gyorsabban, magasabb átviteli sebességgel és kevesebb ideig futnak, mivel a futtatási előzmények és a műveletek közötti adatok nem maradnak meg a külső tárolóban. Igény szerint engedélyezheti a futtatási előzményeket a hibakereséshez. További információ: állapot-nyilvántartó és [állapot nélküli Logic apps](#stateful-stateless).

* A Logic Apps szolgáltatást helyileg tesztelheti a Visual Studio Code fejlesztői környezetében.

* A Visual Studio Code-ból származó logikai alkalmazásokat közzéteheti és helyezheti üzembe közvetlenül különböző üzemeltetési környezetekben, például [Azure app Service](../app-service/environment/intro.md) és [Docker-tárolókban](/dotnet/core/docker/introduction).

> [!NOTE]
> Az aktuális ismert problémákkal kapcsolatos információkért tekintse át az előzetes verziójú bővítmény [ismert problémák GitHub oldalát](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

<a name="stateful-stateless"></a>

## <a name="stateful-versus-stateless-logic-apps"></a>Állapot-nyilvántartó és állapot nélküli logikai alkalmazások

* *Állapotalapú*

  Állapot-nyilvántartó logikai alkalmazások létrehozása, ha meg kell őriznie, ellenőriznie vagy hivatkoznia kell az előző eseményekről. Ezek a logikai alkalmazások megőrzik az egyes műveletek bemenetét és kimenetét, valamint a külső tárolóban lévő munkafolyamat-állapotokat, így az egyes futtatások befejeződése után a Futtatás részletei és előzményei is megtekinthetők. Az állapot-nyilvántartó logikai alkalmazások nagy rugalmasságot biztosítanak, ha az kimaradások történnek. A szolgáltatások és a rendszerek visszaállítása után a megszakított logikai alkalmazás újraépítése a mentett állapotból történik, és a logikai alkalmazások újbóli futtatása a befejezéshez. Az állapot-nyilvántartó munkafolyamatok akár egy évig is futhatnak.

* *Állapot nélküli*

  Állapot nélküli logikai alkalmazások létrehozása, ha nem kell mentenie, áttekintenie vagy hivatkoznia az előző eseményekről. Ezek a logikai alkalmazások csak a memóriában tárolják az egyes műveletek bemeneteit és kimeneteit, és nem továbbítják ezeket az adatokat a külső tárterületre. Ennek eredményeképpen az állapot nélküli logikai alkalmazások rövidebb ideig tartanak, amelyek általában nem kevesebb mint 5 percet vesznek igénybe, gyorsabb a gyorsabb válaszidő, a nagyobb átviteli sebesség és a csökkenő működési költségek, mivel a futtatási adatok és előzmények nem a külső tárolóban maradnak. Ha azonban az kimaradások történnek, a megszakított futtatások nem állíthatók automatikusan vissza, így a hívónak manuálisan kell újraküldenie a megszakított futtatásokat. A könnyebb hibakeresés érdekében engedélyezheti az állapot nélküli logikai alkalmazások [futtatási előzményeit](#run-history) .

  Az állapot nélküli munkafolyamatok jelenleg csak a [felügyelt összekötők](../connectors/apis-list.md#managed-api-connectors)műveleteit támogatják, az eseményindítók nem. A munkafolyamat elindításához válassza ki a [beépített kérelmet, Event Hubs vagy Service Bus triggert](../connectors/apis-list.md#built-ins). A nem támogatott eseményindítókkal, műveletekkel és összekötővel kapcsolatos további információkért lásd a nem [támogatott képességeket](#unsupported).

A beágyazott logikai alkalmazások állapot-nyilvántartó és állapot nélküli logikai alkalmazások közötti különbségeit lásd: [beágyazott viselkedési különbségek az állapot-nyilvántartó és az állapot nélküli logikai alkalmazások között](#nested-behavior).

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Díjszabási modell

Az új **Logic app (előzetes verzió)** típusú erőforrástípus üzembe helyezésekor a rendszer kéri, hogy válasszon egy üzemeltetési csomagot, konkrétan a díjszabási modellként használandó [app Service csomagot vagy prémium csomagot](../azure-functions/functions-scale.md) . Ha kijelöli a App Service csomagot, a rendszer a [díjszabási szintet](../app-service/overview-hosting-plans.md)is kiválasztja. A nyilvános előzetes verzióban a logikai alkalmazások App Serviceon való futtatása nem jár *további* költségekkel a kiválasztott csomag felett.

Az állapot-nyilvántartó logikai alkalmazások [külső tárterületet](../azure-functions/functions-scale.md#storage-account-requirements)használnak, ezért az Azure Storage díjszabási modellje a Azure Logic apps futtatókörnyezet által végrehajtott tárolási tranzakciókra vonatkozik. A várólisták például az ütemezéshez használatosak, míg a rendszer a táblákat és a blobokat használja a Munkafolyamat-állapotok tárolásához.

Az új erőforrástípus esetében alkalmazandó díjszabási modellekkel kapcsolatos további információkért tekintse át a következő témaköröket:

* [Az Azure Functions méretezése és üzemeltetése](../azure-functions/functions-scale.md)
* [Vertikális felskálázás Azure App Service](../app-service/manage-scale-up.md)
* [A Azure Functions díjszabása](https://azure.microsoft.com/pricing/details/functions/)
* [A App Service díjszabása](https://azure.microsoft.com/pricing/details/app-service/windows/)
* [Az Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/)

## <a name="prerequisites"></a>Előfeltételek

### <a name="access-and-connectivity"></a>Hozzáférés és kapcsolódás

* Hozzáférés az internethez, így letöltheti a követelményeket, csatlakozhat a Visual Studio Code-ból az Azure-fiókjához, és közzéteheti a Visual Studio Code-ból az Azure-ba, a Docker-tárolóba vagy más környezetbe.

* Azure-fiók és -előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Ha ugyanezt a logikai alkalmazást szeretné felépíteni ebben a cikkben, szüksége van egy Office 365 Outlook e-mail fiókra, amely Microsoft munkahelyi vagy iskolai fiókot használ a bejelentkezéshez.

  Ha úgy dönt, hogy más, [Azure Logic apps által támogatott e-mail-összekötőt](/connectors/)használ, például a Outlook.com vagy a [Gmail szolgáltatást](../connectors/connectors-google-data-security-privacy-policy.md), akkor továbbra is követheti a példát, és az általános átfogó lépések ugyanazok, de a felhasználói felület és a beállítások bizonyos módokon eltérőek lehetnek. Ha például az Outlook.com-összekötőt használja, használja a személyes Microsoft-fiók a bejelentkezéshez.

### <a name="tools"></a>Eszközök

* A [Visual Studio Code 1.30.1 (január 2019) vagy újabb](https://code.visualstudio.com/), amely ingyenes. Töltse le és telepítse ezeket a további eszközöket a Visual Studio Code-hoz, ha még nem rendelkezik ezekkel:

  * [Azure-fiók bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account), amely egyetlen közös Azure bejelentkezési és előfizetés-szűrési élményt biztosít a Visual Studio Code összes többi Azure-bővítményéhez.

  * [C# a Visual Studio Code bővítményhez](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), amely lehetővé teszi az F5 funkció számára a logikai alkalmazás futtatását.

  * [Azure functions Core Tools](../azure-functions/functions-run-local.md)a [3.0.2931](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.2931) vagy a [2.7.2936](https://github.com/Azure/azure-functions-core-tools/releases/tag/2.7.2936)verziót a Microsoft Installer (MSI) használatával. Ezek az eszközök a Visual Studio Code-ban futó Azure Functions futtatókörnyezetet is felölelő futtatókörnyezettel rendelkeznek.

    > [!IMPORTANT]
    > Ha a verziónál korábbi telepítés van telepítve, először távolítsa el az adott verziót, vagy győződjön meg arról, hogy a PATH környezeti változó a letöltött és telepített verzióban található.
    >
    > Ha a [ **beágyazott kód** műveletet](../logic-apps/logic-apps-add-run-inline-code.md) szeretné használni a JavaScript-kód futtatásához, akkor a Azure functions Runtime 3x-os verzióját kell használnia, mert a művelet nem támogatja a 2x-es verziót. Emellett ez a művelet jelenleg nem támogatott Linux operációs rendszereken.

  * [Azure Logic apps (előzetes verzió) bővítmény a Visual Studio Code-](https://go.microsoft.com/fwlink/p/?linkid=2143167)hoz. Ez a nyilvános előzetes verzió lehetővé teszi, hogy állapot-nyilvántartó és állapot nélküli logikai alkalmazásokat hozzon létre, és a Visual Studio Code-ban helyileg tesztelje őket.

    Jelenleg az eredeti **Azure Logic apps** bővítmény és az új **Azure Logic apps (előzetes verzió)** bővítmény is telepíthető egyszerre a Visual Studio Code-ban. Ha kiválasztja az Azure ikont a Visual Studio Code eszköztáron, megtekintheti az Azure-ban üzembe helyezett összes logikai alkalmazást, de minden erőforrástípus a saját bővítmény szakaszában, **Logic apps** és **Azure Logic apps (előzetes verzió)** jelenik meg.

    > [!IMPORTANT]
    > Ha a Logic Apps szolgáltatást a **Azure Logic apps (privát előzetes verzió)** bővítmény használatával hozta létre, akkor ezek a logikai alkalmazások nem fognak működni a nyilvános előzetes verziójú bővítménnyel. Ezeket a logikai alkalmazásokat azonban áttelepítheti úgy, hogy eltávolítja a privát előnézet bővítményt, végrehajtja a szükséges karbantartást, és telepíti a nyilvános előzetes bővítményt. Ezután létrehozhatja az új projektet a Visual Studio Code-ban, és átmásolhatja a korábban létrehozott Logic apps **munkafolyamat. definition** -fájlját az új projektbe.
    >
    > Ezért a nyilvános előzetes verzió telepítése előtt győződjön meg arról, hogy eltávolította az összes korábbi verziót, és törölje ezeket az összetevőket:
    >
    > * A **Microsoft. Azure. functions. ExtensionBundle. workflows** mappa, amely az előző bővítményi csomagokat tartalmazza, és az elérési út mentén található:
    >
    >   * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`
    >
    >   * `C:\Users\{userName}.azure-functions-core-tools\Functions\ExtensionBundles`
    >
    > * A **Microsoft. Azure. workflows. webjobs. Extension** mappa, amely a [NuGet](/nuget/what-is-nuget) cache a Private Preview bővítményhez, és az elérési út mentén található:
    >
    >   `C:\Users\{userName}\.nuget\packages`

    A **Azure Logic apps (előzetes verzió)** bővítmény telepítéséhez kövesse az alábbi lépéseket:

    1. A Visual Studio Code-ban a bal oldali eszköztáron válassza a **bővítmények**lehetőséget.

    1. A bővítmények keresési mezőjébe írja be a értéket `azure logic apps preview` . Az eredmények listából válassza a **Azure Logic apps (előzetes verzió)** **>** **telepítését**.

       A telepítés befejezése után a nyilvános előzetes verzió a **bővítmények: telepített** listában jelenik meg.

       ![Képernyőfelvétel: a Visual Studio Code telepített bővítmények listája, amelyekben a "Azure Logic Apps (előzetes verzió)" bővítmény aláhúzva jelenik meg.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

* A cikkben létrehozott példa logikai alkalmazás teszteléséhez olyan eszközre van szükség, amely képes hívásokat küldeni a kérelem-triggernek, amely a példa logikai alkalmazás első lépése. Ha nem rendelkezik ilyen eszközzel, a [Poster](https://www.postman.com/downloads/)letöltése, telepítése és használata is használható.

* A diagnosztikai naplózási és nyomkövetési képesség könnyebben használható [Application Insights](../azure-monitor/app/app-insights-overview.md) erőforrás hozzáadásával és használatával. Ezt az erőforrást a Logic app telepítése során, vagy a Azure Portal a logikai alkalmazás üzembe helyezése után hozhatja létre.

### <a name="storage-requirements"></a>Tárolási követelmények

Jelenleg az új **Logic app (előzetes verzió)** erőforrás létrehozása nem érhető el MacOS rendszeren. A Windows vagy más operációs rendszer, például a Linux esetében azonban be kell állítania ezt a tárolási követelményt.

1. Töltse le és telepítse az [Azure Storage Emulator 5,10](https://go.microsoft.com/fwlink/p/?linkid=717179)-es telepítését.

1. Az emulátor futtatásához rendelkeznie kell egy helyi SQL-adatbázis-telepítéssel, például az ingyenes [SQL Server 2019 Express kiadással](https://go.microsoft.com/fwlink/p/?linkid=866658). További információ: [Az Azure Storage Emulator használata fejlesztéshez és teszteléshez](../storage/common/storage-use-emulator.md).

   > [!IMPORTANT]
   > Mielőtt megnyitja a Logic app designert a logikai alkalmazás munkafolyamatának létrehozásához, győződjön meg arról, hogy elindítja az emulátort. Ellenkező esetben a következő üzenet jelenik meg: `Workflow design time could not be started` .
   >
   > ![Képernyőkép, amely megjeleníti az Azure Storage-emulátort.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>A Visual Studio Code telepítése

1. Győződjön meg arról, hogy az összes bővítmény megfelelően van telepítve, töltse be újra vagy indítsa újra a Visual Studio Code-ot.

1. Engedélyezze vagy győződjön meg arról, hogy a Visual Studio Code automatikusan megkeresi és telepíti a bővítmények frissítéseit, hogy a nyilvános előzetes bővítmény megkapja a legújabb frissítéseket.

   A beállítás megadásához kövesse az alábbi lépéseket:

   1. A **fájl** menüben válassza **a beállítások** **>** **Beállítások menüpontot**.

   1. A **felhasználó** lapon válassza a **szolgáltatások** **>** **bővítmények**lehetőséget.

   1. Ellenőrizze, hogy be van-e jelölve az **automatikus ellenőrzés frissítései** és az **automatikus frissítés** .

1. Engedélyezze vagy győződjön meg arról, hogy ezek a **Azure Logic apps (előzetes verzió)** bővítmény beállításai be vannak állítva a Visual Studio Code-ban:

   * **Azure Logic Apps v2: panel mód**
   * **Azure Logic Apps v2: Project Runtime**

   1. A **fájl** menüben válassza **a beállítások** **>** **Beállítások menüpontot**.

   1. A **felhasználó** lapon válassza a **>** **bővítmények** **>** **Azure Logic apps (előzetes verzió)** lehetőséget.

   1. A **Azure Logic apps v2: panel módban**ellenőrizze, hogy be van-e jelölve az **Engedélyezés panel mód** . A **Azure Logic apps v2: Project Runtime**alatt állítsa a verziót a korábban telepített [Azure functions Core Tools-verzió](#prerequisites) alapján a **~ 3** vagy a **~ 2**értékre.

      > [!IMPORTANT]
      > Ha a [ **beágyazott kód** műveletet](../logic-apps/logic-apps-add-run-inline-code.md) szeretné használni a JavaScript-kód futtatásához, győződjön meg arról, hogy a Project Runtime 3-as verzióját használja, mert a művelet nem támogatja a 2. verziót. Emellett ez a művelet jelenleg nem támogatott Linux operációs rendszereken.

      ![A "Azure Logic Apps (előzetes verzió)" bővítmény Visual Studio Code-beállításait bemutató képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz

1. A Visual Studio Code eszköztáron válassza az Azure ikont.

   ![Képernyőkép, amely a Visual Studio Code eszközsort és a kiválasztott Azure ikont jeleníti meg.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. Az Azure ablaktáblán az **Azure: Logic apps (előzetes verzió)** területen válassza a **Bejelentkezés az Azure**-ba lehetőséget. Amikor megjelenik a Visual Studio Code Authentication oldal, jelentkezzen be az Azure-fiókjával.

   ![Képernyőkép, amely az Azure-ablaktáblát és az Azure-bejelentkezéshez kiválasztott hivatkozást mutatja.](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   Miután bejelentkezett, az Azure panel megjeleníti az Azure-fiók előfizetéseit. Ha a várt előfizetések nem jelennek meg, vagy azt szeretné, hogy a panel csak bizonyos előfizetéseket jelenítsen meg, kövesse az alábbi lépéseket:

   1. Az előfizetések listájában vigye a mutatót az első előfizetés mellé, amíg megjelenik az **előfizetések kiválasztása** gomb (szűrő ikon). Kattintson a szűrő ikonra.

      ![Képernyőkép, amely az Azure panelt és a kiválasztott szűrő ikont jeleníti meg.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      Vagy a Visual Studio Code állapotjelző sávjában válassza ki az Azure-fiókját. 

   1. Ha megjelenik egy másik előfizetések listája, válassza ki a kívánt előfizetéseket, és győződjön meg róla, hogy az **OK gombra**kattint.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Helyi projekt létrehozása

A logikai alkalmazás létrehozása előtt hozzon létre egy helyi projektet, hogy a Visual Studio Code-ból kezelhesse és üzembe lehessen helyezni a logikai alkalmazást. Az alapul szolgáló projekt hasonló egy Azure Functions projekthez, más néven Function app-projekthez. Ezek a projekttípus azonban egymástól függetlenek, így a logikai alkalmazások munkafolyamatai és funkciói nem létezhetnek ugyanabban a projektben.

1. A számítógépen hozzon létre egy *üres* helyi mappát a projekthez, amelyet később a Visual Studio Code-ban fog létrehozni.

   Ha .NET Core SDK 5,0-es verzió van telepítve, hozzon létre egy **global.js** olyan fájlon, amely a .net Core Runtime 3. x verziójára hivatkozik, amely a 3.1.201-nél későbbi, például:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   Később, miután létrehozta a projektet, de mielőtt megpróbálta megnyitni a **workflow.js** fájlt a Logic app Designerben, hozzá kell adnia ezt a **global.jsa** fájlhoz a projekt legfelső szintű helyére.

1. A Visual Studio Code-ban zárjunk be minden és minden megnyitott mappát.

1. Az Azure ablaktáblán, az **Azure: Logic apps (előzetes verzió)** területen válassza az **új projekt létrehozása** (ikon, amely egy mappát és egy villámot mutatja).

   ![Képernyőkép, amely az Azure panel eszköztárát az "új projekt létrehozása" lehetőség kiválasztásával jeleníti meg.](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Ha a Windows Defender-tűzfal felszólítja a hálózati hozzáférés megadására `Code.exe` , amely a Visual Studio Code, és a `func.exe` , amely a Azure functions Core Tools, válassza a **magánhálózatok, például a saját otthoni vagy munkahelyi hálózat** **>** **lehetővé teszi a hozzáférést**.

1. Keresse meg azt a helyet, ahová a projekt mappáját létrehozta, válassza ki a mappát, és folytassa a folytatást.

   ![A "mappa kiválasztása" párbeszédpanelt megjelenítő képernyőkép, amely egy újonnan létrehozott Project-mappával és a "kiválasztás" gomb kiválasztásával jelenik meg.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. A megjelenő sablonok listából válassza az **állapot-nyilvántartó munkafolyamat** vagy az **állapot nélküli munkafolyamat**lehetőséget. Ez a példa **állapot-nyilvántartó munkafolyamatot**jelöl ki.

   ![Képernyőkép, amely a munkafolyamat-sablonok listáját jeleníti meg "állapot-nyilvántartó munkafolyamat" beállítással.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Adja meg a logikai alkalmazás munkafolyamatának nevét, majd nyomja le az ENTER billentyűt. Ez a példa `example-workflow` a nevet használja.

   ![Képernyőkép: "új állapot-nyilvántartó munkafolyamat létrehozása (3/4)" és "példa – munkafolyamat" a munkafolyamat neveként.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

1. A megjelenő következő listából válassza a **Megnyitás az aktuális ablakban**lehetőséget.

   ![Képernyőkép, amely a "Megnyitás az aktuális ablakban" elemet jeleníti meg.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-location.png)

   A Visual Studio Code újratöltődik, megnyitja az Explorer ablaktáblát, és megjeleníti a projektet, amely mostantól automatikusan generált projektfájlt is tartalmaz. Például a projekt tartalmaz egy mappát, amely megjeleníti a logikai alkalmazás munkafolyamatának nevét. Ebben a mappában a `workflow.json` fájl tartalmazza a logikai alkalmazás munkafolyamat mögöttes JSON-definícióját.

   ![Képernyőkép, amely megjeleníti az Explorer ablakot a Project mappában, a munkafolyamat mappájában és a "workflow.json" fájlt.](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

1. Ha .NET Core SDK 5,0 telepítve van, és létrehozott egy **global.jst a** .net Core Runtime 3. x verzióra hivatkozó fájlon, amely a 3.1.201-nál újabb, akkor hozzá kell adnia az **global.js** fájlt a projekt legfelső szintű helyére a Visual Studio Code-ban.

   > [!NOTE]
   > Győződjön meg arról, hogy elvégezte ezt a lépést, mielőtt megpróbálja megnyitni a **workflow.js** fájlt, amely tartalmazza a munkafolyamat mögöttes JSON-definícióját a Logic app Designerben. Ellenkező esetben a tervező nem nyílik meg.

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-logic-app-designer"></a>A munkafolyamat-definíciós fájl megnyitása a Logic app Designerben

1. Ha a Visual Studio Code Windows vagy Linux rendszeren fut, győződjön meg arról, hogy az Azure Storage-emulátor fut. További információkért tekintse át az [előfeltételeket](#prerequisites).

1. Bontsa ki a munkafolyamat projekt mappáját. Nyissa meg a **workflow.jsa** fájl helyi menüjében, és válassza a **Megnyitás a tervezőben**lehetőséget.

   ![Képernyőfelvétel: az Explorer panel és a parancsikon ablaka a "Megnyitás a Designerben" beállítással rendelkező fájl workflow.js.](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

   Ha a hibaüzenet jelenik meg, győződjön meg arról `Workflow design time could not be started` , hogy az Azure Storage-emulátor fut. Egyéb esetben próbálkozzon a következő hibaelhárítási javaslatokkal:

   A Visual Studio Code-ban tekintse meg az előnézet bővítmény kimenetét.

   1. A **nézet** menüben válassza a **kimenet**lehetőséget.

   1. A **kimeneti** címsor listájában válassza a **Azure Logic apps** lehetőséget, így megtekintheti az előnézet kiterjesztés kimenetét, például:

      ![A Visual Studio Code kimeneti ablakát a "Azure Logic Apps" kiválasztásával ábrázoló képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

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

      Ez a hiba akkor fordulhat elő, ha korábban megpróbálta megnyitni a tervezőt, majd megszüntette vagy törölte a projektet. Ennek a hibának a megoldásához törölje a **ExtensionBundles** mappát ezen a helyen **. ..\Users \\ {your-username} \AppData\Local\Temp\Functions\ExtensionBundles**, majd próbálja ** meg** újra megnyitni aworkflow.jsfájlt a tervezőben.

1. Az **Összekötők engedélyezése az Azure-ban** listában válassza az Azure-beli **Összekötők használata**lehetőséget, amely a Azure Portal elérhető összes felügyelt összekötőre vonatkozik, nem csak az Azure-szolgáltatásokhoz csatlakozó összekötők.

   ![Képernyőfelvétel: az "összekötők engedélyezése az Azure-ban" lista megnyitása és az "összekötők használata az Azure-ban" lehetőség van kiválasztva.](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

1. Az erőforráscsoportok listából válassza az **Új erőforráscsoport létrehozása**lehetőséget.

   ![Képernyőkép, amely megjeleníti az Explorer ablaktáblát az erőforráscsoportok listája és az "új erőforráscsoport létrehozása" beállítással](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Adja meg az erőforráscsoport nevét, majd nyomja le az ENTER billentyűt. Ez a példa a következőt használja: `example-logic-app-preview-rg` .

   ![Képernyőkép, amely megjeleníti az Explorer ablaktáblát és az erőforráscsoport neve mezőt.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. A helyszínek listában keresse meg és válassza ki a [támogatott Azure-régiót](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions) , amelyet az erőforráscsoport és az erőforrások létrehozásához kíván használni. Ez a példa az **USA nyugati középső**régióját használja.

   > [!IMPORTANT]
   > Jelenleg nem minden régió támogatott, de a további régiók hozzáadására vonatkozó frissítések folyamatban vannak. A nem támogatott régiók kiválasztása problémákat okozhat, például kapcsolatokat hozhat létre. A jelenleg támogatott régiók esetében tekintse át az előzetes verziójú bővítmény [ismert problémák GitHub-oldalát](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions).

   ![Képernyőkép, amely megjeleníti az Explorer ablaktáblát a helyszínek listával és az "USA nyugati középső régiója" kijelöléssel.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   A lépés elvégzése után a Visual Studio Code megnyitja a Logic app designert.

   > [!NOTE]
   > Ha a Visual Studio Code elindítja a munkafolyamat tervezési idejű API-ját, megjelenik egy üzenet, amely eltarthat néhány másodpercig. Figyelmen kívül hagyhatja ezt az üzenetet, vagy választhatja **az OK**gombot.

   A Logic app Designer megjelenése után a **művelet kiválasztása** üzenet jelenik meg a tervezőben, és alapértelmezés szerint ki van választva, amely megjeleníti a **művelet hozzáadása** ablaktáblát.

   ![A Logic app designert bemutató képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Ezután [adjon hozzá egy triggert és műveleteket](#add-trigger-actions) a munkafolyamathoz.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Trigger és műveletek hozzáadása

Miután megnyitotta a Logic app Designer alkalmazást a **workflow.jsa** fájl helyi menüjében, megjelenik a **művelet kiválasztása** üzenet a tervezőben, és alapértelmezés szerint ki van választva. Most már megkezdheti a munkafolyamat létrehozását egy trigger és művelet hozzáadásával.

Ebben a példában a logikai alkalmazás munkafolyamata ezt az triggert és a következő műveleteket használja:

* A beépített [kérelem triggere](../connectors/connectors-native-reqres.md), **Amikor HTTP-kérés érkezik**, amely bejövő hívásokat fogad, és olyan végpontot hoz létre, amelyet más szolgáltatások vagy logikai alkalmazások hívhatnak meg.

* Az [Office 365 Outlook művelet](../connectors/connectors-create-api-office365-outlook.md), **e-mail küldése**.

* A beépített [Válasz művelet](../connectors/connectors-native-reqres.md), amellyel a rendszer visszaküldi a választ, és visszaküldi azokat a hívónak.

### <a name="add-the-request-trigger"></a>A kérelem-trigger hozzáadása

1. A tervező mellett, az **trigger hozzáadása** panelen, a művelet keresésének **kiválasztása** mezőben ellenőrizze, hogy be van-e jelölve a **beépített elem** , így a natív módon futtatható trigger kiválasztható.

1. A **válasszon műveletet** keresési mezőben adja meg a kifejezést `when a http request` , majd válassza ki a **http-kérelem fogadásakor**megnevezett beépített kérelem-triggert.

   ![Képernyőfelvétel: a Logic app Designer és a * * trigger * * panel hozzáadása a "HTTP-kérelem fogadásakor" trigger kiválasztásával.](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   Amikor az trigger megjelenik a tervezőben, megnyílik az trigger részletek panelje, amely megjeleníti az trigger tulajdonságait, beállításait és egyéb műveleteit.

   ![Képernyőfelvétel: a Logic app Designer és a "HTTP-kérés fogadásakor" trigger kiválasztásának és a részletek ablaktábla megnyitásának megjelenítése.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Ha a részleteket tartalmazó ablaktábla nem jelenik meg, ellenőrizze, hogy az trigger ki van-e választva a tervezőben.

1. Ha törölnie kell egy tételt a tervezőben, kövesse az alábbi lépéseket:

   1. A tervezőben válassza ki az elemet.

   1. Az elem jobb oldalán megjelenő részletek ablaktábláján válassza az ellipszisek (**...**) gomb **>** **Törlés**lehetőséget. A törlés megerősítéséhez kattintson **az OK gombra**.

      ![Képernyőfelvétel: a kijelölt elem megjelenítése a tervezőben az Open details panelen és a kijelölt ellipszisek gomb és a "Törlés" lehetőséggel.](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

### <a name="add-the-office-365-outlook-action"></a>Az Office 365 Outlook-művelet hozzáadása

1. A tervezőben a hozzáadott trigger alatt válassza az **új lépés**lehetőséget.

   A **válasszon műveletet** kérő üzenet jelenik meg a tervezőben, és a **művelet hozzáadása ablaktábla** újra megnyílik, így kiválaszthatja a következő műveletet.

1. A **művelet hozzáadása** ablaktábla **művelet** keresése mezőjében válassza az **Azure** lehetőséget, így megkeresheti és kiválaszthatja az Azure-ban üzembe helyezett felügyelt összekötőhöz tartozó műveletet.

   Ez a példa kiválasztja és használja az Office 365 Outlook műveletet, **E-mail küldése (v2)**.

   ![Képernyőfelvétel: a Logic app Designer és a * * művelet hozzáadása * * ablaktábla Office 365 Outlook "e-mail küldése" művelet van kiválasztva.](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. A művelet részleteit tartalmazó ablaktáblán válassza a **Bejelentkezés** lehetőséget, hogy létre tudja hozni a kapcsolatát az e-mail-fiókjával.

   ![Képernyőfelvétel: a Logic app Designer és a * * e-mail küldése (v2) * * ablaktábla, ahol be van jelölve a "Bejelentkezés" lehetőség.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

   > [!NOTE]
   > Ha a hibaüzenet jelenik meg, lehetséges, hogy a `Failed to create connection...` logikai alkalmazáshoz jelenleg nem támogatott régiót választott. További régiók hozzáadásának frissítései folyamatban vannak. Eközben a jelenleg támogatott régiókban tekintse át az előzetes verziójú bővítmény [ismert problémák GitHub-oldalát](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions).

1. Ha a Visual Studio Code megkéri az e-mail-fiók elérésének jóváhagyását, válassza a **Megnyitás**lehetőséget.

   ![Képernyőkép, amely a Visual Studio Code promptot jeleníti meg a hozzáférés engedélyezéséhez.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > A jövőbeli kérések megelőzése érdekében válassza a **megbízható tartományok konfigurálása** lehetőséget, hogy a hitelesítési lapot megbízható tartományként adja hozzá.

1. A bejelentkezéshez, a hozzáférés engedélyezéséhez és a Visual Studio Code-hoz való visszatéréshez kövesse a következő utasításokat.

   > [!NOTE]
   > Ha túl sok idő telik el az utasítások elvégzése előtt, a hitelesítési folyamat túllépi az időkorlátot, és sikertelen lesz. Ebben az esetben térjen vissza a tervezőhöz, és ismételje meg a bejelentkezést a kapcsolódás létrehozásához.

1. Ha a Azure Logic Apps Preview bővítmény megkéri az e-mail-fiók elérésének jóváhagyását, válassza a **Megnyitás**lehetőséget. A hozzáférés engedélyezéséhez kövesse az ezt követő kérdést.

   ![A hozzáférés engedélyezését bemutató, előzetes verziójú bővítményre vonatkozó kérést megjelenítő képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > Ha meg szeretné akadályozni a jövőbeli kérdéseket, válassza a **bővítmény ne Kérdezzen rá többé**lehetőséget.

   Miután a Visual Studio Code létrehozta a kapcsolatát, egyes összekötők azt mutatják, hogy az adott időkorlát `The connection will be valid for {n} days only.` csak az időtartamra vonatkozik, amikor a logikai alkalmazást a Visual Studio Code-ban hozza létre. Az üzembe helyezést követően ez a korlát már nem érvényes, mert a logikai alkalmazás az automatikusan engedélyezett, [rendszer által hozzárendelt felügyelt identitás](../logic-apps/create-managed-service-identity.md)használatával tud hitelesítést végezni futásidőben. Ez a felügyelt identitás eltér a kapcsolatok létrehozásakor használt hitelesítő adatoktól vagy kapcsolódási karakterlánctól. Ha letiltja ezt a rendszer által hozzárendelt felügyelt identitást, a kapcsolatok nem működnek futásidőben.

1. Ha a tervezőben nem jelenik meg az **E-mail küldése** művelet, válassza ki ezt a műveletet.

1. A művelet részletek ablaktáblájának **Paraméterek** lapján adja meg a művelethez szükséges adatokat, például:

   ![Képernyőfelvétel: az Office 365 Outlook "e-mail küldése" műveletének részleteit megjelenítő Logic app Designer.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Művelet** | Yes | <*saját e-mail cím*> | Az e-mail címzettje, amely a tesztelési célú e-mail-címe lehet. Ez a példa a fiktív e-mailt használja `sophiaowen@fabrikam.com` . |
   | **Tárgy** | Yes | `An email from your example workflow` | Az e-mail tárgya |
   | **Törzs** | Yes | `Hello from your example workflow!` | Az e-mail szövegtörzsének tartalma |
   ||||

   > [!NOTE]
   > Ha módosítani kívánja a részleteket tartalmazó ablaktáblán a **Beállítások**, a **Futtatás után**vagy a **statikus eredmény** lapot, győződjön meg arról, hogy a **kész** gombra kattint a módosítások elvégzéséhez, mielőtt átváltja a lapokat, vagy megváltoztatja a fókuszt a tervezőre. Ellenkező esetben a Visual Studio Code nem fogja megőrizni a módosításokat. További információkért tekintse meg az előzetes verziójú bővítmény [ismert problémák GitHub-oldalát](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

1. A tervezőben válassza a **Mentés**lehetőséget.

Ezután a Visual Studio Code-ban helyileg hibakeresést és tesztelést végezhet a munkafolyamatban.

<a name="debug-test-locally"></a>

## <a name="debug-and-test-your-logic-app"></a>A logikai alkalmazás hibakeresése és tesztelése

1. Az állapot nélküli logikai alkalmazások munkafolyamatainak könnyebb hibakeresése érdekében [engedélyezheti a munkafolyamat futtatási előzményeit](#run-history).

1. A Visual Studio Code eszköztáron nyissa meg a **Futtatás** menüt, és válassza a **hibakeresés indítása** (F5) lehetőséget.

   Megnyílik a **terminál** ablak, ahol áttekintheti a hibakeresési munkamenetet.

1. Most keresse meg a végpont visszahívási URL-címét a kérelem triggerében.

   1. Nyissa meg újra az Explorer ablaktáblát, és tekintse meg a projektet.

   1. A **workflow.js** fájl helyi menüjében válassza az **Áttekintés**lehetőséget.

      ![Képernyőkép, amely megjeleníti az Explorer ablaktáblát és a parancsikont az "áttekintés" kijelölt fájl workflow.js.](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. Keresse meg a **visszahívási URL-címet** , amely hasonlít ehhez az URL-címhez a példa kérésének triggeréhez:

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![Képernyőkép a munkafolyamat áttekintő oldalának visszahívási URL-címéről](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. Ha tesztelni szeretné a visszahívási URL-címet a logikai alkalmazás munkafolyamatának elindításával, nyissa meg a [Poster](https://www.postman.com/downloads/) vagy a kívánt eszközt a kérelmek létrehozására és küldésére.

   Ez a példa a Poster használatával folytatódik. További információ: [poster első lépések](https://learning.postman.com/docs/getting-started/introduction/).

   1. Az utólagos eszköztáron válassza az **új**lehetőséget.

      ![Képernyőkép, amely az új gomb kiválasztásával mutatja be a Poster elemet](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. Az **új ablaktábla létrehozás** paneljén kattintson a **kérelem** **elemre**.

   1. A kérelem **mentése** ablakban a kérelem **neve**alatt adja meg a kérelem nevét, például: `Test workflow trigger` .

   1. A **menteni kívánt gyűjtemény vagy mappa kiválasztása**területen válassza a **gyűjtemény létrehozása**lehetőséget.

   1. A **minden gyűjtemény**területen adja meg a kérések rendszerezéséhez létrehozandó gyűjtemény nevét, nyomja le az ENTER billentyűt, majd válassza a **Mentés lehetőséget <*gyűjtemény neve* > **elemre. Ez a példa `Logic Apps requests` a gyűjtemény nevét használja.

      A Poster kérelmi panelje megnyílik, így kérést küldhet a visszahívási URL-címre a kérelem triggere számára.

      ![Képernyőkép, amely a megnyitott kérelem ablaktáblán jeleníti meg a Poster-t](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Térjen vissza a Visual Studio Code-ba. a munkafolyamat áttekintés lapján másolja a **visszahívási URL-** tulajdonság értékét.

   1. Vissza a Poster-re. A kérelem ablaktáblán a metódusok listája, amely jelenleg az alapértelmezett kérési módszerként **jelenik meg** , illessze be a cím mezőbe korábban átmásolt visszahívási URL-címet, és válassza a **Küldés**lehetőséget.

      ![Képernyőkép, amely a Poster és a visszahívási URL-címet jeleníti meg a cím mezőben kiválasztott küldés gombbal](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      A példa logikai alkalmazás munkafolyamata egy e-mailt küld, amely az alábbi példához hasonlóan jelenik meg:

      ![Az Outlook e-maileket megjelenítő képernyőkép a példában leírtak szerint](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. A Visual Studio Code-ban térjen vissza a munkafolyamat áttekintés lapjára.

   Ha állapot-nyilvántartó munkafolyamatot hozott létre, akkor az elküldte a munkafolyamatot indító kérést, az Áttekintés oldalon a munkafolyamat futtatási állapota és előzményei láthatók. A futtatási állapotokkal kapcsolatos további információkért lásd: futtatási [Előzmények áttekintése](../logic-apps/monitor-logic-apps.md#review-runs-history).

   ![Képernyőfelvétel a munkafolyamat áttekintési oldaláról a futtatási állapottal és az előzményekkel](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   > [!TIP]
   > Ha a futtatási állapot nem jelenik meg, próbálja meg frissíteni az Áttekintés lapot a **frissítés**lehetőség kiválasztásával.

1. Ha szeretné áttekinteni az egyes lépésekhez tartozó állapotokat egy adott futtatásban, valamint a lépés bemeneteit és kimeneteit, válassza a futtatáshoz tartozó három pont (**...**) gombot, és válassza a **Futtatás megjelenítése**lehetőséget.

   ![Képernyőfelvétel: a munkafolyamat futtatási előzményeinek sora az ellipszisek gomb és a "Futtatás megjelenítése" beállítással](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   A Visual Studio Code megjeleníti az egyes műveletek futtatási állapotát.

1. Ha szeretné áttekinteni az egyes lépések bemeneteit és kimeneteit, bontsa ki a megvizsgálni kívánt lépést. Az adott lépéshez tartozó nyers bemenetek és kimenetek további áttekintéséhez válassza a **nyers bemenetek megjelenítése** vagy a **nyers kimenet megjelenítése**lehetőséget.

   ![Képernyőfelvétel: a munkafolyamat egyes lépéseinek állapotát, valamint a kibontott "e-mail küldése" művelet bemeneteit és kimeneteit bemutató képernyőkép](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. A hibakeresési munkamenet leállításához a **Futtatás** menüben válassza a **hibakeresés leállítása** (SHIFT + F5) lehetőséget.

<a name="return-response"></a>

## <a name="return-a-response-to-the-caller"></a>Válasz küldése a hívónak

Ha vissza szeretne térni arra a hívóra, amely a logikai alkalmazásnak küldött kérelmet küld, használhatja a beépített [Válasz műveletet](../connectors/connectors-native-reqres.md) egy olyan munkafolyamathoz, amely a kérelem triggerével kezdődik.

1. A Logic app Designerben az **E-mail küldése** művelet alatt válassza az **új lépés**lehetőséget.

   A **válasszon műveletet** kérő üzenet jelenik meg a tervezőben, és a **művelet hozzáadása ablaktábla** újra megnyílik, így kiválaszthatja a következő műveletet.

1. Győződjön meg arról, hogy a **művelet hozzáadása** ablaktáblán a **művelet** keresése mezőben a **beépített elem** van kiválasztva. A keresőmezőbe írja be a kifejezést `response` , majd válassza ki a **Válasz** műveletet.

   ![Képernyőfelvétel: a Logic app Designer és a kiválasztott Response művelet.](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   Ha a **Válasz** művelet megjelenik a tervezőben, a művelet részletei ablaktábla automatikusan megnyílik.

   ![Képernyőfelvétel: a Logic app Designer és a "Response" művelet részletei panelje meg van nyitva, az "Body" tulajdonság pedig az "e-mail küldése" művelet "Body" tulajdonságának értéke.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. A **Parameters (paraméterek** ) lapon adja meg a hívni kívánt függvényhez szükséges adatokat.

   Ez a példa az **E-mail küldése** művelet eredményét adja vissza a **Body** tulajdonság értékével.

   1. Kattintson a **törzs** tulajdonságmezőbe, hogy a dinamikus tartalom lista megjelenjen, és megjeleníti a munkafolyamat előző triggerének és műveleteinek elérhető kimeneti értékeit.

      ![Képernyőfelvétel: a "válasz" művelet részleteket tartalmazó ablaktáblája a "Body" tulajdonságban található egérmutatóval, hogy megjelenjen a dinamikus tartalmak listája.](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. A dinamikus tartalom lista **E-mail küldése**területén válassza a **törzs**lehetőséget.

      ![Képernyőkép, amely a dinamikus tartalom megnyitása listát jeleníti meg. Az "e-mail küldése" fejléc alatti listában a "Body" kimeneti érték van kiválasztva.](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      Ha elkészült, a Response művelet **Body** tulajdonsága most az **E-mail küldése** művelet **törzsének** kimenete értékre van állítva.

      ![A munkafolyamat egyes lépéseinek állapotát, valamint a kibontott "válasz" művelet bemeneteit és kimeneteit megjelenítő képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. A tervezőben válassza a **Mentés**lehetőséget.

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>A logikai alkalmazás újratesztelése

Miután elvégezte a logikai alkalmazás frissítését, egy másik teszt futtatásához futtassa újra a hibakeresőt a Visual Studióban, és küldjön egy másik kérést a frissített logikai alkalmazás aktiválásához, hasonlóan a [logikai alkalmazás hibakereséséhez és teszteléséhez](#debug-test-locally)szükséges lépésekhez.

1. A Visual Studio Code eszköztáron nyissa meg a **Futtatás** menüt, és válassza a **hibakeresés indítása** (F5) lehetőséget.

1. A Poster vagy a kérések létrehozására és küldésére szolgáló eszközében küldjön egy másik kérést a munkafolyamat elindításához.

1. Ha állapot-nyilvántartó munkafolyamatot hozott létre, a munkafolyamat áttekintés lapján ellenőrizze a legutóbbi Futtatás állapotát. Az egyes lépések állapotának, bemenetének és kimenetének megtekintéséhez kattintson a futtatáshoz tartozó három pontra (**...**), majd válassza a **Futtatás megjelenítése**lehetőséget.

   Például itt látható a Futtatás lépésenkénti állapota, miután a minta munkafolyamat frissült a válasz művelettel.

   ![A frissített munkafolyamat egyes lépéseinek állapotát, valamint a kibontott "válasz" művelet bemeneteit és kimeneteit bemutató képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. A hibakeresési munkamenet leállításához a **Futtatás** menüben válassza a **hibakeresés leállítása** (SHIFT + F5) lehetőséget.

<a name="publish-azure"></a>

## <a name="publish-to-azure"></a>Közzététel az Azure-ban

A Visual Studio Code-ból közvetlenül is üzembe helyezheti a projektet az Azure-ban, amely az új **Logic app (előzetes verzió)** erőforrástípus használatával teszi közzé a logikai alkalmazást. A Azure Functionsban található Function app-erőforráshoz hasonlóan az új erőforrástípus üzembe helyezéséhez ki kell választania egy [üzemeltetési csomagot és egy díjszabási szintet](../app-service/overview-hosting-plans.md), amelyet az üzembe helyezés során be lehet állítani. Az üzemeltetési csomagokról és a díjszabásról a következő témakörökben talál további információt:

* [Vertikális felskálázás Azure App Service](../app-service/manage-scale-up.md)
* [Az Azure Functions méretezése és üzemeltetése](../azure-functions/functions-scale.md)

A logikai alkalmazást új erőforrásként teheti közzé, amely automatikusan létrehoz minden további szükséges erőforrást, például egy [Azure Storage-fiókot, amely a Function app követelményeihez hasonló](../azure-functions/storage-considerations.md). Vagy közzéteheti a logikai alkalmazást egy korábban telepített **Logic app (előzetes verzió)** erőforráson, amely felülírja a logikai alkalmazást.

### <a name="publish-as-a-new-logic-app-preview-resource"></a>Közzététel új Logic app-(előzetes verziójú) erőforrásként

1. A Visual Studio Code eszköztáron válassza az Azure ikont.

1. Az **Azure: Logic apps (előzetes verzió)** panel eszköztárán válassza a **telepítés logikai alkalmazásba**lehetőséget.

   ![Az "Azure: Logic Apps (előzetes verzió)" panel és az ablaktábla eszköztárának "üzembe helyezés logikai alkalmazásba" beállítását bemutató képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. A Visual Studio Code megnyíló listából válassza a következő lehetőségek közül:

   * **Új logikai alkalmazás létrehozása (előzetes verzió) az Azure-ban** (gyors)
   * **Új logikai alkalmazás (előzetes verzió) létrehozása az Azure Advanced szolgáltatásban**
   * Korábban üzembe helyezett **Logic app (előzetes verzió)** erőforrás, ha létezik

   Ez a példa folytatja az **új logikai alkalmazás (előzetes verzió) létrehozását az Azure Advanced**szolgáltatásban.

   ![Az "Azure: Logic Apps (előzetes verzió)" panelt tartalmazó képernyőkép, amely az "új logikai alkalmazás létrehozása (előzetes verzió) az Azure-ban" elemet mutatja.](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Az új **logikai alkalmazás (előzetes verzió)** erőforrás létrehozásához kövesse az alábbi lépéseket:

   1. Adja meg az új logikai alkalmazás globálisan egyedi nevét, amely a **Logic app (előzetes verzió)** erőforráshoz használandó név. Ez a példa a következőt használja: `example-logic-app-preview` .

      ![Az "Azure: Logic Apps (előzetes verzió)" panelt megjelenítő képernyőkép, amely a létrehozandó új logikai alkalmazás nevének megadását kéri.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Válassza ki az új logikai alkalmazás üzemeltetési tervét [**app Service csomag**](../azure-functions/functions-scale.md#app-service-plan) vagy [**prémium**](../azure-functions/functions-scale.md#premium-plan). Ez a példa **app Service tervet**választ.

      ![Képernyőkép az "Azure: Logic Apps (előzetes verzió)" ablaktábláról, valamint egy "App Service-csomag" vagy "Prémium" kiválasztására vonatkozó kérdés.](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. Hozzon létre egy új App Service tervet, vagy válasszon ki egy meglévő csomagot. Ez a példa kiválasztja az **új App Service terv létrehozása**lehetőséget.

      ![Képernyőkép, amely az "Azure: Logic Apps (előzetes verzió)" panelt jeleníti meg, és rákérdez az "új App Service terv létrehozása" elemre, vagy válasszon egy meglévő App Service csomagot.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. Adja meg a App Service terv nevét, majd válassza ki a csomag [díjszabási szintjét](../app-service/overview-hosting-plans.md) . Ebben a példában az **F1 ingyenes** csomagot választjuk.

      ![Képernyőkép, amely az "Azure: Logic Apps (előzetes verzió)" ablaktáblát és egy díjszabási csomag kiválasztására szolgáló kérdést jelenít meg.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. Az optimális teljesítmény érdekében keresse meg és válassza ki ugyanazt az erőforráscsoportot, mint az üzemelő példány projektjét.

      > [!NOTE]
      > Bár létrehozhat vagy használhat egy másik erőforráscsoportot, ami hatással lehet a teljesítményre. Ha létrehoz vagy kiválaszt egy másik erőforráscsoportot, de a megerősítést kérő üzenet megjelenésekor a Mégse gombra kattint, a rendszer a központi telepítést is megszakítja.

   1. Állapot-nyilvántartó munkafolyamatok esetén válassza az **új Storage-fiók** vagy egy meglévő Storage-fiók létrehozása lehetőséget.

      ![Az "Azure: Logic Apps (előzetes verzió)" ablaktáblát és a Storage-fiók létrehozására vagy kiválasztására vonatkozó kérést bemutató képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. A diagnosztikai naplózási és nyomkövetési képesség könnyebben kiválasztható egy meglévő Application Insights erőforrás. Ellenkező esetben kiválaszthatja az **új Application Insights erőforrás létrehozása**lehetőséget, vagy beállíthatja, hogy az alkalmazás üzembe helyezése után az Azure Portalon Application Insights.

      A telepítése előtt győződjön meg arról, hogy az `logLevel` objektumot a `logging` projekt legfelső szintjén található fájl **host.jsjában** adja hozzá a objektumhoz, és állítsa be például a `Host.Triggers.Workflow` `Information` következőt:

      ```json
      "logLevel": {
         "Host.Triggers.Workflow": "Information"
      },
      ```

      A fájl **host.jsa** következőképpen nézhet ki:

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

   Ha elkészült, a Visual Studio Code elkezdi létrehozni és telepíteni a logikai alkalmazás közzétételéhez szükséges erőforrásokat.

1. A telepítési folyamat áttekintéséhez és figyeléséhez a **nézet** menüben válassza a **kimenet**lehetőséget. A kimeneti ablak eszköztárán válassza a **Azure Logic apps**lehetőséget.

   ![Képernyőfelvétel: az eszköztár listájában kiválasztott "Azure Logic Apps" kimeneti ablak a telepítési folyamattal és állapotokkal együtt.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Ha a Visual Studio Code befejezi a logikai alkalmazás munkafolyamatának telepítését az Azure-ba, ez az üzenet jelenik meg:

   ![Képernyőkép, amely az Azure-ba való központi telepítés sikeres befejezését jelző üzenetet jelenít meg.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Gratulálunk, a logikai alkalmazás most már az Azure-ban él, és alapértelmezés szerint engedélyezve van.

Ezután megtudhatja, hogyan hajthatja végre ezeket a feladatokat:

* [Keresse meg az üzembe helyezett logikai alkalmazást a Azure Portal](#find-manage-deployed-workflows-portal) vagy [a Visual Studio Code](#find-manage-deployed-workflows-vs-code)-ban.

* [Futtatási előzmények engedélyezése az állapot nélküli logikai alkalmazások munkafolyamataiban](#run-history).

* [Egy telepített **Logic app-(előzetes verziójú)** erőforrás figyelésének engedélyezése](#enable-monitoring).

<a name="find-manage-deployed-workflows-vs-code"></a>

## <a name="find-and-manage-deployed-logic-apps-in-visual-studio-code"></a>Üzembe helyezett logikai alkalmazások keresése és kezelése a Visual Studio Code-ban

A Visual Studio Code-ban az összes telepített Logic apps megtekinthető az Azure-előfizetésben, függetlenül attól, hogy az eredeti **Logic apps** vagy a **logikai alkalmazás (előzetes verzió)** erőforrástípus, valamint olyan feladatokat választ, amelyek segítenek a logikai alkalmazások kezelésében. Mindkét erőforrástípus eléréséhez azonban szükség van a Visual Studio Code **Azure Logic apps** és a **Azure Logic apps (előzetes verzió)** bővítményekre is.

1. A bal oldali eszköztáron válassza az Azure ikont. Az **Azure: Logic apps (előzetes verzió)** ablaktáblán bontsa ki az előfizetését, amely megjeleníti az előfizetéshez tartozó összes telepített logikai alkalmazást.

1. Keresse meg és válassza ki a kezelni kívánt logikai alkalmazást. Nyissa meg a logikai alkalmazás helyi menüjét, és válassza ki a végrehajtani kívánt feladatot.

   Kijelölhet például olyan feladatokat, mint például a telepített logikai alkalmazás leállítása, elindítása, újraindítása vagy törlése.

   ![Képernyőfelvétel: a megnyitott "Azure Logic Apps (előzetes verzió)" kiterjesztési panel és az üzembe helyezett munkafolyamat megnyitása a Visual Studio Code-ban.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. A logikai alkalmazás összes munkafolyamatának megtekintéséhez bontsa ki a logikai alkalmazást, majd bontsa ki a **munkafolyamatok** csomópontot.

1. Egy adott munkafolyamat megtekintéséhez nyissa meg a munkafolyamat helyi menüjét, és válassza a **Megnyitás a tervezőben**lehetőséget, amely csak olvasható módban nyitja meg a munkafolyamatot.

   A munkafolyamat szerkesztéséhez a következő lehetőségek közül választhat:

   * A Visual Studio Code-ban nyissa meg a projekt **workflow.jsa** Logic app Designerben, végezze el a módosításokat, majd telepítse újra a logikai alkalmazást az Azure-ba.

   * A Azure Portal keresse meg [és nyissa meg a logikai alkalmazást](#find-manage-deployed-workflows-portal). A munkafolyamat megkeresése, szerkesztése és mentése.

1. Az üzembe helyezett logikai alkalmazás a Azure Portalban való megnyitásához nyissa meg a logikai alkalmazás helyi menüjét, és válassza a **Megnyitás a portálon**lehetőséget.

   A Azure Portal megnyílik a böngészőben, és automatikusan bejelentkezik a portálra, ha bejelentkezett a Visual Studio Code-ba, és megjeleníti a logikai alkalmazást.

   ![Képernyőkép, amely a logikai alkalmazás Azure Portal lapját mutatja a Visual Studio Code-ban.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Külön is bejelentkezhet a Azure Portalba, a portál keresőmezőt használva megkeresheti a logikai alkalmazást, majd kiválaszthatja a logikai alkalmazást az eredmények listából.

   ![Képernyőfelvétel: a Azure Portal és a keresősáv megjelenítése a kiválasztott logikai alkalmazás keresési eredményeivel, amely kiválasztva jelenik meg.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="find-manage-deployed-workflows-portal"></a>

## <a name="find-and-manage-deployed-logic-apps-in-the-portal"></a>Üzembe helyezett logikai alkalmazások keresése és kezelése a portálon

A Azure Portal az Azure-előfizetésében lévő összes telepített Logic apps megtekinthető, függetlenül attól, hogy az eredeti **Logic apps** erőforrástípus vagy a **logikai alkalmazás (előzetes verzió)** típusú erőforrástípus. Az egyes erőforrástípusok jelenleg külön kategóriákként vannak rendszerezve és kezelve az Azure-ban.

> [!NOTE]
> A nyilvános előzetes verzióban csak a telepített **Logic app-(előzetes verziójú)** erőforrásokat tekintheti meg a Azure Portalban, nem hozhat létre új **logikai alkalmazás-(előzetes verziójú)** erőforrásokat. Ezeket a logikai alkalmazásokat csak a Visual Studio Code-ban lehet létrehozni. Ehhez az erőforrás-típushoz azonban [hozzáadhat munkafolyamatokat](#add-workflows) a logikai alkalmazások üzembe helyezéséhez.

Az alábbi lépéseket követve megkeresheti a **Logic app (előzetes verzió)** típusú erőforrásokkal rendelkező logikai alkalmazásokat:

1. A Azure Portal keresőmezőbe írja be a kifejezést `logic app preview` . Amikor megjelenik az eredmények listája, a **szolgáltatások**területen válassza a **logikai alkalmazás (előzetes verzió)** lehetőséget.

   ![A "Logic app Preview" keresési szöveget megjelenítő Azure Portal keresőmezőt bemutató képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. A **logikai alkalmazás (előzetes verzió)** ablaktáblán keresse meg és válassza ki a Visual Studio Code-ból üzembe helyezett logikai alkalmazást.

   ![Képernyőkép, amely az Azure-ban üzembe helyezett Azure Portal és logikai alkalmazás (előzetes verzió) erőforrásait mutatja be.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   A Azure Portal megnyitja a kiválasztott logikai alkalmazás egyedi erőforrás-lapját.

   ![A logikai alkalmazás munkafolyamatának erőforrás-lapját megjelenítő képernyőkép a Azure Portal.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. A logikai alkalmazás munkafolyamatainak megtekintéséhez a logikai alkalmazás menüjében válassza a **munkafolyamatok**lehetőséget.

   A **munkafolyamatok** ablaktábla megjeleníti az aktuális logikai alkalmazás összes munkafolyamatát. Ez a példa a Visual Studio Code-ban létrehozott munkafolyamatot mutatja be.

   ![Képernyőkép: a "Logic app (előzetes verzió)" erőforrás-oldal, amelyen a "munkafolyamatok" ablaktábla meg van nyitva, és az üzembe helyezett munkafolyamat](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. Munkafolyamat megtekintéséhez a **munkafolyamatok ablaktáblán** válassza ki a munkafolyamatot.

   Megnyílik a munkafolyamat panel, és megjeleníti az adott munkafolyamaton végrehajtható további információkat és feladatokat.

   Ha például a munkafolyamat lépéseit szeretné megtekinteni, válassza a **tervező**lehetőséget.

   ![Képernyőfelvétel: a kiválasztott munkafolyamat "áttekintés" panelje, míg a munkafolyamat menü a kiválasztott "Designer" parancsot jeleníti meg.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   Megnyílik a Logic app Designer, és megjeleníti a Visual Studio Code-ban létrehozott munkafolyamatot. Most már módosíthatja a munkafolyamatot a Azure Portalban.

   ![A Visual Studio Code-ból üzembe helyezett Logic app designert és munkafolyamatot bemutató képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflows"></a>

## <a name="add-a-workflow-to-deployed-logic-apps"></a>Munkafolyamat hozzáadása a logikai alkalmazások üzembe helyezéséhez

A Azure Portal használatával üres munkafolyamatokat adhat hozzá a Visual Studio Code-ból üzembe helyezett **logikai alkalmazás (előzetes verzió)** erőforráshoz, és felépítheti ezeket a munkafolyamatokat a Azure Portal.

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki a telepített **Logic app (előzetes verzió)** erőforrást.

1. A logikai alkalmazás menüjében válassza a **munkafolyamatok**lehetőséget. A **munkafolyamatok** ablaktáblán válassza a **Hozzáadás**lehetőséget.

   ![A kiválasztott logikai alkalmazás "munkafolyamatok" paneljét és az eszköztárat a "Hozzáadás" parancs kiválasztásával ábrázoló képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. Az **Új munkafolyamat** ablaktáblán adja meg a munkafolyamat nevét. Válassza ki az **állapot-nyilvántartó** vagy az **állapot nélküli** **>** **Létrehozás**elemet.

   Miután az Azure üzembe helyezte az új munkafolyamatot, amely megjelenik a **munkafolyamatok** ablaktáblán, válassza ki a munkafolyamatot a felügyelet és egyéb feladatok végrehajtásához, például a Logic app Designer vagy a Code View megnyitásához.

   ![Képernyőkép, amely a kiválasztott munkafolyamatot jeleníti meg a felügyeleti és felülvizsgálati beállításokkal.](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Ha például egy új munkafolyamat tervezőjét nyitja meg, egy üres vászon jelenik meg. Most már létrehozhatja ezt a munkafolyamatot a Azure Portal.

   ![A Logic app designert és egy üres munkafolyamatot bemutató képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="run-history"></a>

## <a name="run-history-for-stateless-logic-app-workflows"></a>Állapot nélküli logikai alkalmazások munkafolyamatainak futtatási előzményei

Az állapot nélküli logikai alkalmazások munkafolyamatainak egyszerűbb hibakereséséhez engedélyezheti a munkafolyamathoz tartozó futtatási előzményeket a Visual Studio Code-ban vagy a Azure Portalban, majd letilthatja a futtatási előzményeket, ha elkészült.

### <a name="for-a-stateless-logic-app-workflow-in-visual-studio-code"></a>Állapot nélküli logikai alkalmazás-munkafolyamathoz a Visual Studio Code-ban

Ha a Visual Studio Code-ban helyileg dolgozik és futtatja az állapot nélküli logikai alkalmazás munkafolyamatát, kövesse az alábbi lépéseket:

1. A projektben keresse meg és bontsa ki a **munkafolyamat-designtime** mappát. Keresse meg és nyissa meg a **local.settings.js** fájlt.

1. Adja hozzá a `Workflows.{yourWorkflowName}.operationOptions` tulajdonságot, és állítsa be az értéket `WithStatelessRunHistory` , például:

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

1. A futtatási előzmények letiltásához törölje a `Workflows.{yourWorkflowName}.OperationOptions` tulajdonságot és annak értékét, vagy állítsa a tulajdonságot a következőre: `None` .

### <a name="for-a-stateless-logic-app-workflow-in-the-azure-portal"></a>Állapot nélküli logikai alkalmazás-munkafolyamathoz a Azure Portal

Ha már telepítette a projektet a Azure Portalba, kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)keresse meg és nyissa meg a **logikai alkalmazás (előzetes verzió)** erőforrását.

1. A logikai alkalmazás menüjében, a **Beállítások**területen válassza a **konfiguráció**elemet.

1. Az **Alkalmazásbeállítások** lapon válassza az **új alkalmazás beállítása**lehetőséget.

1. Az **alkalmazás hozzáadása/szerkesztése** panel **név** mezőjébe írja be a következő művelet nevét: 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. Az **érték** mezőbe írja be a következő értéket: `WithStatelessRunHistory`

   Példa:

   ![Képernyőfelvétel: a Azure Portal és Logic app (előzetes verzió) erőforrás "Configuration" > "új Alkalmazásbeállítás" < "alkalmazás hozzáadása/szerkesztése" panel megnyitása és a "munkafolyamatok". {yourWorkflowName}. A "OperationOptions" beállítás "WithStatelessRunHistory" értékre van állítva.](./media/create-stateful-stateless-workflows-visual-studio-code/stateless-operation-options-run-history.png)

1. Amikor elkészült, válassza az **OK** lehetőséget. A **konfiguráció** ablaktáblán válassza a **Mentés**lehetőséget.

Ha engedélyezni szeretné a figyelést a telepített Logic app-(előzetes verziójú) erőforráson, folytassa a következő szakasszal.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-for-deployed-logic-app-preview-resources"></a>Telepített Logic app-(előzetes verziójú) erőforrások figyelésének engedélyezése

Az üzembe helyezett **Logic app (előzetes verzió)** erőforrás figyelésének engedélyezéséhez kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki a telepített **Logic app (előzetes verzió)** erőforrást.

1. Az erőforrás menüjében, az **API**alatt válassza a **CORS**lehetőséget.

1. A **CORS** ablaktábla **engedélyezett eredetek**területén adja hozzá a helyettesítő karaktert (*).

1. Ha elkészült, a **CORS** eszköztárán válassza a **Mentés**lehetőséget.

   ![Képernyőkép, amely megjeleníti a Azure Portal egy telepített Logic Apps (előzetes verzió) erőforrással. Az erőforrás menüben a "CORS" lehetőség van kiválasztva egy új bejegyzéssel az "Allowed Origins" értékre, amely a "*" karaktert helyettesítő karakterre van állítva.](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker-container"></a>Üzembe helyezés a Docker-tárolóban

A [.net Core parancssori felület (CLI) eszköz](/dotnet/core/tools/)használatával felépítheti a projektet, majd közzéteheti a buildet. Ezután létrehozhatja és használhatja a [Docker-tárolót](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) célként a logikai alkalmazás munkafolyamatának üzembe helyezéséhez. További információkért tekintse át a következő témaköröket:

* [A tárolók és a Docker bemutatása](/dotnet/architecture/microservices/container-docker-introduction/)
* [A .NET és a Docker bemutatása](/dotnet/core/docker/introduction)
* [Docker-terminológia](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology)
* [Oktatóanyag: a Docker első lépései](/visualstudio/docker/tutorials/docker-tutorial)

1. A projekt létrehozásához nyisson meg egy parancssort, és futtassa a következő parancsot:

   `dotnet build -c release`

   További információkért lásd a DotNet- [Build](/dotnet/core/tools/dotnet-build/) referenciáját ismertető oldalt.

1. A Build közzétételéhez futtassa a következő parancsot:

   `dotnet publish -c release`

   További információkért tekintse meg a [DotNet publish](/dotnet/core/tools/dotnet-publish/) -referenciát ismertető oldalt.

1. Hozzon létre egy Docker-tárolót egy Docker-fájl használatával egy .NET-munkafolyamathoz, és futtassa a következő parancsot:

   `docker build --tag local/workflowcontainer .`

   Íme például egy olyan minta Docker-fájl, amely egy állapot-nyilvántartó logikai alkalmazást telepít, és megadja a logikai alkalmazás Azure Portal való közzétételéhez használt Azure Storage-fiókhoz tartozó kapcsolódási karakterláncot. A Storage-fiók kapcsolódási karakterláncának a Azure Portalban való megkereséséhez és másolásához tekintse át a [Storage-fiók kulcsainak kezelése](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)című részt.

   ![Képernyőkép, amely megjeleníti a Azure Portal a Storage-fiók hozzáférési kulcsainak és a kapcsolati karakterláncnak a másolásával.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   A kapcsolódási karakterlánc a következőhöz hasonlóan néz ki:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageaccount;AccountKey={access-key};EndpointSuffix=core.windows.net`

   A Docker-fájl formátuma a következő:


   ```text
   FROM mcr.microsoft.com/azure-functions/dotnet:3.0.14492-appservice

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AZURE_FUNCTIONS_ENVIRONMENT Development
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot
   ENV AzureFunctionsJobHost__Logging__Console__IsEnabled=true
   ENV FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY ./bin/Release/netcoreapp3.1/publish/ /home/site/wwwroot
   ```

   További információ: [Docker Build](https://docs.docker.com/engine/reference/commandline/build/).

1. Mentse a karakterláncot biztonságos helyre, hogy később hozzáadja a **local.settings.js** karakterláncot a projektben található azon fájlokhoz, amelyeket a logikai alkalmazás Visual Studio Code-ban való létrehozásához használ.

1. Futtassa helyileg a tárolót a következő paranccsal:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   További információ: [Docker Run](https://docs.docker.com/engine/reference/commandline/run/).

1. A kérelem-trigger visszahívási URL-címének beszerzéséhez küldje el a kérelmet:

   `POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2019-10-01-edge-preview&code={master-key}`

   Az <*Master-key*> érték az Azure Storage-fiókban van definiálva, amelyet a `AzureWebJobsStorage` fájl, az **Azure-webjobs-Secrets/{Deployment-Name}/host.jsa**fájlban beállított, ahol megtalálhatja az értéket ebben a szakaszban:

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

<a name="nested-behavior"></a>

## <a name="nested-behavior-differences-between-stateful-and-stateless-logic-apps"></a>Beágyazott viselkedési különbségek az állapot-nyilvántartó és az állapot nélküli logikai alkalmazások között

A Logic apps-munkafolyamatok más logikai alkalmazás-munkafolyamatokból is [meghívhatók](../logic-apps/logic-apps-http-endpoint.md) a [kérelem](../connectors/connectors-native-reqres.md) eseményindító, a [http-webhook](../connectors/connectors-native-webhook.md) eseményindító vagy a felügyelt összekötő eseményindítók használatával, amelyek [APICONNECTIONWEHOOK-típussal](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) rendelkeznek, és HTTPS-kéréseket kaphatnak.

Az alábbiakban láthatók a beágyazott logikai alkalmazások munkafolyamatainak viselkedési mintái, miután egy szülő munkafolyamat meghívja a gyermek munkafolyamatot:

* Aszinkron lekérdezési minta

  A szülő nem vár választ a kezdeti hívásra, de folyamatosan ellenőrzi a gyermek futási előzményeit, amíg a gyermek befejezi a futást. Alapértelmezés szerint az állapot-nyilvántartó munkafolyamatok ezt a mintát követik, ami ideális olyan hosszú ideig futó alárendelt munkafolyamatokhoz, amelyek túllépik a [kérelmek időtúllépési korlátait](../logic-apps/logic-apps-limits-and-config.md).

* Szinkron minta ("tűz és felejtsd el")

  A gyermek visszaigazolja a hívást, hogy azonnal visszaadja a `202 ACCEPTED` választ, és a szülő továbbra is a következő műveletre vár anélkül, hogy meg kellene várnia a gyermek eredményét. Ehelyett a szülő megkapja az eredményeket, amikor a gyermek befejezi a futását. Olyan alárendelt állapot-nyilvántartó munkafolyamatok, amelyek nem tartalmazzák a válasz műveletet, mindig a szinkron mintát kövessék. Gyermek állapot-nyilvántartó munkafolyamatok esetén a futtatási előzmények elérhetők az áttekintéshez.

  A viselkedés engedélyezéséhez a munkafolyamat JSON-definíciójában állítsa be a tulajdonságot a következőre: `OperationOptions` `DisableAsyncPattern` . További információ: [trigger-és Művelettípus – műveleti beállítások](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options).

* Trigger és várakozás

  Gyermek állapot nélküli munkafolyamathoz a szülő vár egy olyan választ, amely visszaadja az eredményeket a gyermektől. Ez a minta hasonló módon működik, mint a beépített [http-trigger vagy művelet](../connectors/connectors-native-http.md) , amely egy alárendelt munkafolyamatot hív meg. Olyan alárendelt állapot nélküli munkafolyamatok, amelyek nem tartalmaznak választ azonnal választ adnak vissza `202 ACCEPTED` , de a szülő megvárja, amíg a gyermek befejeződik a következő művelet folytatása előtt. Ezek a viselkedés csak alárendelt állapot nélküli munkafolyamatokra érvényesek.

Ez a tábla a gyermek munkafolyamatok viselkedését határozza meg attól függően, hogy a szülő és a gyermek állapota állapotú, állapot nélküli vagy vegyes munkafolyamat-típusok-e:

| Szülő munkafolyamat | Gyermek munkafolyamat | Gyermek viselkedése |
|-----------------|----------------|----------------|
| Állapotalapú | Állapotalapú | Aszinkron vagy szinkron `"operationOptions": "DisableAsyncPattern"` beállítással |
| Állapotalapú | Állapot nélküli | Trigger és várakozás |
| Állapot nélküli | Állapotalapú | Szinkron |
| Állapot nélküli | Állapot nélküli | Trigger és várakozás |
||||

## <a name="limits"></a>Korlátok

Bár a [Azure Logic apps számos meglévő korlátja](../logic-apps/logic-apps-limits-and-config.md) megegyezik ennél az erőforrás-típusnál, a következő különbségek jelennek meg a nyilvános előzetes bővítményben:

* Felügyelt összekötők: 50 percenkénti kérelmek száma kapcsolatonként

* A JavaScript-műveletekhez tartozó [beágyazott kód művelet](../logic-apps/logic-apps-add-run-inline-code.md) esetében ezek a korlátozások módosultak:

  * A kód karaktereinek korlátja 1 024 karakterből 100 000 karakterre nő.

  * A kód futtatásának korlátja öt másodperc és 15 másodperc közötti értékre nő.

<a name="unsupported"></a>

## <a name="unavailable-or-unsupported-capabilities"></a>Nem érhető el vagy nem támogatott képességek

Ebben a nyilvános előzetes verzióban ezek a képességek nem érhetők el, vagy nem támogatottak:

* Az új **Logic app (előzetes verzió)** erőforrás létrehozása jelenleg nem érhető el MacOS rendszeren.

* Ez az előzetes verzió nem támogatja az egyéni összekötők, webhook-alapú eseményindítók és a csúszó ablak eseményindítóját. Az állapot nélküli logikai alkalmazások munkafolyamataihoz csak a [felügyelt összekötők](../connectors/apis-list.md#managed-api-connectors)műveletei adhatók meg, az eseményindítók nem. A munkafolyamat elindításához használja a [beépített kérelmet, Event Hubs vagy Service Bus triggert](../connectors/apis-list.md#built-ins).

* Az új **Logic app (előzetes verzió)** típusú erőforrástípus csak [prémium vagy app Service üzemeltetési csomagra](#publish-azure) telepíthető az Azure-ban vagy egy [Docker-tárolóban](#deploy-docker), és nem [integrációs szolgáltatási környezetekben (ISEs)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). A **felhasználási** üzemeltetési csomagok nem támogatottak, és nem érhetők el az erőforrástípus üzembe helyezéséhez.

* A Azure Portal nem hozhat létre új logikai alkalmazásokat az új **logikai alkalmazás (előzetes verzió)** típusú erőforrással. Ezeket a logikai alkalmazásokat csak a Visual Studio Code-ban lehet létrehozni. Ha azonban a Logic apps alkalmazást a Visual Studio Code-ból az Azure-ba telepíti, akkor [új munkafolyamatokat adhat hozzá a logikai alkalmazásokhoz](#add-workflows).

## <a name="next-steps"></a>Következő lépések

Örömmel vesszük a véleményét a nyilvános előzetes verzióval kapcsolatos tapasztalatairól!

* Hibák vagy problémák esetén [hozza létre a problémákat a githubon](https://github.com/Azure/logicapps/issues).
* Kérdések, kérések, megjegyzések és egyéb visszajelzések esetén [használja ezt a visszajelzési űrlapot](https://aka.ms/lafeedback).
