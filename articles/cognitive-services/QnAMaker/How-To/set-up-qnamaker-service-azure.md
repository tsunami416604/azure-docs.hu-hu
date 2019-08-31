---
title: QnA Maker szolgáltatás beállítása – QnA Maker
titleSuffix: Azure Cognitive Services
description: Mielőtt bármilyen QnA Maker tudásbázisok hozhat létre, először be kell állítania a QnA Maker szolgáltatást az Azure-ban. Az új erőforrásokat hozhatnak létre egy előfizetésben engedélyezésével bárki is beállíthatja a QnA Maker szolgáltatást.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: bec154a7e437621f377c503642b186cf166016cc
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195317"
---
# <a name="manage-qna-maker-resources"></a>QnA Maker erőforrások kezelése

Mielőtt bármilyen QnA Maker tudásbázisok hozhat létre, először be kell állítania a QnA Maker szolgáltatást az Azure-ban. Az új erőforrásokat hozhatnak létre egy előfizetésben engedélyezésével bárki is beállíthatja a QnA Maker szolgáltatást.

## <a name="types-of-keys-in-qna-maker"></a>A QnA Makerban található kulcsok típusai

A QnA Maker szolgáltatás kezelje a kulcs, kétféle **előfizetési kulcsok** és **végpont kulcsok**.

![Kulcskezelés](../media/qnamaker-how-to-key-management/key-management.png)

|Name (Név)|Location|Cél|
|--|--|--|
|Előfizetői azonosító|[Azure Portal](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|Ezek a kulcsok a [QnA Maker Management szolgáltatás API-jai](https://go.microsoft.com/fwlink/?linkid=2092179)elérésére szolgálnak. Ezek az API-k lehetővé teszik a tudásbázisban található kérdések és válaszok szerkesztését, valamint a Tudásbázis közzétételét. Ezek a kulcsok akkor jönnek létre, amikor új QnA Maker szolgáltatást hoz létre.<br><br>A kulcsok lapon keresse meg ezeket a kulcsokat a **Cognitive Services** erőforráson.|
|Végpont kulcsa|[QnA Maker portál](http://www.qnamaker.ai)|Ezek a kulcsok a közzétett Tudásbázis-végpont elérésére szolgálnak, így választ kaphat a felhasználó kérdéseire. Ezt a végpontot általában a csevegési robotjában vagy a QnA Maker szolgáltatáshoz csatlakozó ügyfélalkalmazás kódjában használja. Ezek a kulcsok akkor jönnek létre, amikor közzéteszi a QnA Maker tudásbázist.<br><br>Keresse meg ezeket a kulcsokat a **szolgáltatás beállításai** lapon. A lap tetején, a jobb oldali felhasználó menüjében választhat a legördülő menüben.|

## <a name="create-a-new-qna-maker-service"></a>Hozzon létre egy új QnA Maker szolgáltatást

Ez az eljárás létrehozza a Tudásbázis tartalmának kezeléséhez szükséges Azure-erőforrásokat. A lépések elvégzése után megkeresi az előfizetési kulcsokat a Azure Portal erőforrás **kulcsok** lapján.

1. Jelentkezzen be a Azure Portalba, és [hozzon létre egy QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) -erőforrást.

1. A feltételek és kikötések elolvasása után válassza a **Létrehozás** lehetőséget.

    ![Hozzon létre egy új QnA Maker szolgáltatást](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. A **QnA Maker**, válassza ki a megfelelő szintek és régiók.

    ![Hozzon létre egy új QnA Maker – tarifacsomag és régiók](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Töltse ki a **neve** a QnA Maker szolgáltatás azonosítására, egyedi névvel. Ez a név is azonosítja a QnA Maker végpontot, amelyhez a tudásbázisok lesz társítva.
    * Válassza ki a **előfizetés** a QnA Maker erőforrás meg lesz telepítve.
    * Válassza ki a QnA Maker felügyeleti szolgáltatások (portál és felügyeleti API-k) **díjszabási szintjét** . Lásd: [Itt](https://aka.ms/qnamaker-pricing) az SKU-k, a díjszabással kapcsolatos információkért.
    * Hozzon létre egy új **erőforráscsoport** (ajánlott), vagy használjon egy meglévőt, melyben szeretné üzembe helyezni a QnA Maker erőforrás. QnA Maker több Azure-erőforrást hoz létre; Amikor létrehoz egy erőforráscsoportot az erőforrások tárolásához, egyszerűen megkeresheti, kezelheti és törölheti ezeket az erőforrásokat az erőforráscsoport neve alapján.
    * Válassza ki az **erőforráscsoport helyét**.
    * Válassza ki a **tarifacsomag keresési** az Azure Search szolgáltatás. Ha látja az ingyenes szint lehetőség szürkén jelenik meg, az azt jelenti, már rendelkezik egy ingyenes Azure Search szolgáltatásréteg, az előfizetésében üzembe helyezve. Ebben az esetben szüksége lesz az alapszintű Azure Search szolgáltatásréteg kezdődhet. Részletek megtekintése az Azure search díjszabása [Itt](https://azure.microsoft.com/pricing/details/search/).
    * Válassza ki a **hely keresése** hol szeretné üzembe helyezni az Azure Search-adatok. Ahol a vásárlói adatokat tárolni kell a korlátozások tájékoztatja a helyet úgy dönt, az Azure search.
    * Nevezze el az App Service- **alkalmazásnév**.
    * Alapértelmezés szerint az App Service-ben alapértelmezés szerint a standard (S1) szintre. Létrehozása után módosíthatja a tervet. További részleteket az App service díjszabását [Itt](https://azure.microsoft.com/pricing/details/app-service/).
    * Válassza ki a **webhely helye** ahol az App Service üzembe helyezve.

        > [!NOTE]
        > A keresés helyét a webhely helye eltérő lehet.

    * Válassza ki, hogy engedélyezni szeretné **Application Insights** vagy sem. Ha **Application Insights** van engedélyezve, QnA Maker telemetriát gyűjt a forgalom, a csevegési naplók és a hibákat.
    * Válassza ki a **alkalmazáselemzések helye** ahol Application Insights-erőforrás üzembe helyezve.
    * A költségmegtakarítási mértékek esetében megoszthat néhányat, de nem minden QnA Makerhoz létrehozott Azure-erőforrást. [](#share-existing-services-with-qna-maker) 

1. Miután az összes mezőt érvényesíti, válassza a **Létrehozás**lehetőséget. Néhány percet is igénybe vehet.

1. Ha végzett az üzembe helyezés, látni fogja az alábbi, az előfizetésben létrehozott erőforrásokat.

    ![Létrehozott egy új QnA Maker szolgáltatás-erőforrást](../media/qnamaker-how-to-setup-service/resources-created.png)

    A _Cognitive Services_ típusú erőforrás rendelkezik _előfizetési_ kulcsokkal.

## <a name="find-subscription-keys-in-azure-portal"></a>Előfizetési kulcsok keresése a Azure Portalban

Megtekintheti és alaphelyzetbe állíthatja az előfizetési kulcsokat, amelyekkel szerkesztheti a Azure Portal, ahol létrehozta a QnA Maker erőforrást. 

1. Nyissa meg a QnA Maker erőforrást a Azure Portalban, és válassza ki az erőforrást az _Cognitive Services_típussal.

    ![Rendszererőforrás-lista a QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Lépjen a **kulcsok**.

    ![Előfizetői azonosító](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-qna-maker-portal"></a>Végponti kulcsok keresése QnA Maker portálon

Végpont kulcsok kezelhető a [QnA Maker portal](https://qnamaker.ai).

1. Jelentkezzen be a [QnA Maker portálra](https://qnamaker.ai), nyissa meg a profilt, majd kattintson a **szolgáltatás beállításai**elemre.

    ![Végponti kulcs](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Megtekintheti, vagy visszaállíthatja a kulcsokat.

    ![Endpoint-kezelő](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Frissítse a kulcsokat, ha úgy érzi, hogy sérült a biztonsága. Ehhez szükség lehet az ügyfélalkalmazás vagy a robot kódjának megfelelő módosítására.

## <a name="share-existing-services-with-qna-maker"></a>Meglévő szolgáltatások megosztása QnA Maker

QnA Maker több Azure-erőforrást hoz létre. A felügyelet és a költségmegosztás előnyeinek csökkentése érdekében az alábbi táblázat segítségével megismerheti, hogy mit tehet és nem oszthat meg:

|Szolgáltatás|Megosztás|Reason|
|--|--|--|
|Cognitive Services|X|Nem lehetséges a kialakítás|
|App Service-csomag|✔|Az App Service-csomaghoz lefoglalt rögzített lemezterület. Ha más alkalmazások is ugyanazt a App Service-csomagot osztják meg, akkor jelentős lemezterületet is igénybe vesz, a QnAMaker App Service problémákba kerül.|
|App Service-ben|X|Nem lehetséges a kialakítás|
|Application Insights|✔|Megosztható|
|Keresési szolgáltatás|✔|1. `testkb` a QnAMaker szolgáltatás számára fenntartott név, amelyet mások nem használhatnak.<br>2. A név `synonym-map` szerinti szinonimák a QnAMaker szolgáltatás számára vannak fenntartva.<br>3. A közzétett Tudásbázis számát a keresési szolgáltatás szintje korlátozza. Ha ingyenes indexek állnak rendelkezésre, más szolgáltatások is használhatják azt.|

### <a name="using-a-single-search-service"></a>Egyetlen keresési szolgáltatás használata

Ha létrehoz egy QnA szolgáltatást és annak függőségeit (például a keresést) a portálon keresztül, a rendszer létrehoz egy keresési szolgáltatást, és a QnA Maker szolgáltatáshoz kötődik. Az erőforrások létrehozása után frissítheti az App Service-beállítást egy korábban létező keresési szolgáltatás használatára, és eltávolíthatja az éppen létrehozott keresési szolgáltatást.

Ha Azure Resource Manager-sablonokon keresztül hoz létre QnA szolgáltatást, az összes erőforrást létrehozhatja, és szabályozhatja az App Service létrehozását egy meglévő keresési szolgáltatás használatára. 

## <a name="upgrade-qna-maker"></a>QnA Maker frissítése

|Frissítés|Reason|
|--|--|
|[Frissítés](#upgrade-qna-maker-sku) QnA Maker felügyeleti SKU|További kérdésekkel és válaszokkal kell rendelkeznie a Tudásbázisban.|
|[Frissítés](#upgrade-app-service) App Service SKU|A Tudásbázisnak több kérést kell kiszolgálnia az ügyfélalkalmazás, például egy csevegési robot használatával.|
|[Frissítés](#upgrade-azure-search-service) Azure Search szolgáltatás|Számos tudásbázist tervez.|


### <a name="upgrade-qna-maker-sku"></a>QnA Maker SKU frissítése

Ha további kérdésekkel és válaszokkal kell rendelkeznie a Tudásbázisban, az aktuális szinten túlmutatva frissítse a QnA Maker Service díjszabási szintjét. 

A QnA Maker felügyeleti SKU frissítése:

1. Nyissa meg a QnA Maker erőforrást a Azure Portal, és válassza az **árképzési szintet**.

    ![Erőforrás QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Válassza ki a megfelelő SKU-t, majd kattintson a **kiválasztás**gombra.

    ![Díjszabás QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>Az App Service frissítése

 Ha a Tudásbázisban több kérést kell kiszolgálni az ügyfélalkalmazástól, frissítse az App Service díjszabási szintjét.

Az App Service vertikális [fel](https://docs.microsoft.com/azure/app-service/manage-scale-up) -vagy leskálázást végez.

Nyissa meg az App Service-erőforrást a Azure Portalban , és szükség szerint válassza a vertikális felskálázás vagy a leskálázás lehetőséget.

![QnA Maker app Service-méretezés](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-azure-search-service"></a>Azure Search szolgáltatás frissítése

Ha sok tudásbázist tervez, frissítse a Azure Search Service díjszabási szintjét. 

Jelenleg nem végezhető el az Azure Search SKU helyben történő frissítése. Létrehozhat azonban egy új Azure Search-erőforrást a kívánt SKU-val, visszaállíthatja az új erőforrást, majd csatolhatja azt a QnA Maker veremhez.

1. Hozzon létre egy új Azure Search-erőforrást a Azure Portalban, és válassza ki a kívánt SKU-t.

    ![Azure Search-erőforrás QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Állítsa vissza az indexeket az eredeti Azure Search-erőforrásból az újat. Tekintse meg a biztonsági másolat-visszaállítási mintakód [itt](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Az adatvisszaállítás után nyissa meg az új Azure Search-erőforrást, válassza a **kulcsok**lehetőséget, és jegyezze fel a **nevet** és a **rendszergazdai kulcsot**.

    ![Azure Search-kulcsok QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Ha az új Azure Search-erőforrást az QnA Maker veremhöz szeretné kapcsolni, lépjen a QnA Maker app Service weboldalára.

    ![QnA Maker appservice](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Válassza az **Alkalmazásbeállítások** lehetőséget, és cserélje le a **AzureSearchName** és a **AzureSearchAdminKey** mezőket a 3. lépésből.

    ![QnA Maker appservice-beállítás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Indítsa újra az App Service-ben.

    ![QnA Maker appservice újraindítása](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-latest-runtime-updates"></a>A legújabb futtatókörnyezet frissítéseinek beolvasása

Az QnAMaker Runtime a [QnAMaker szolgáltatás](./set-up-qnamaker-service-azure.md) Azure Portal-ban való létrehozásakor üzembe helyezett Azure app Service része. A futtatókörnyezet rendszeres időközönként végrehajtott frissítéseket. A QnA Maker App Service az április 2019-es hely kiterjesztésének kiadása után automatikus frissítési módban van. Ez már úgy van kialakítva, hogy a frissítések során nulla állásidőt lehessen felügyelni. 

A jelenlegi verzióját a következő címen tekintheti meg: https://www.qnamaker.ai/UserSettings. Ha a verzió régebbi, mint az 5. x verzió, újra kell indítania a App Service a legújabb frissítések alkalmazásához.

1. Keresse meg a QnAMaker szolgáltatást (erőforráscsoport) a [Azure Portalon](https://portal.azure.com)

    ![QnAMaker Azure-erőforráscsoport](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Kattintson a az App Service-ben, és nyissa meg az Áttekintés szakaszban

     ![QnAMaker App Service-ben](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Indítsa újra az App Service-ben. Néhány másodpercen belül teljesítéséhez. A QnAMaker szolgáltatást használó függő alkalmazások vagy robotok nem lesznek elérhetők a végfelhasználók számára az újraindítási időszakban.

    ![QnAMaker az App Service-újraindítás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="management-service-region"></a>Felügyeleti szolgáltatási régió

A QnA Maker felügyeleti szolgáltatása, amelyet csak a QnA Maker portálon & a kezdeti adatfeldolgozáshoz használ, csak az USA nyugati régiójában érhető el. Ebben a Nyugat-amerikai szolgáltatásban nem tárolunk ügyféladatokat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hozzon létre és Tudásbázis közzététele](../Quickstarts/create-publish-knowledge-base.md)
