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
ms.date: 09/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: a2b467eed010edbb842d536bd8f6e3f4107fcea8
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984362"
---
# <a name="manage-qna-maker-resources"></a>QnA Maker erőforrások kezelése

Mielőtt bármilyen QnA Maker tudásbázisok hozhat létre, először be kell állítania a QnA Maker szolgáltatást az Azure-ban. Az új erőforrásokat hozhatnak létre egy előfizetésben engedélyezésével bárki is beállíthatja a QnA Maker szolgáltatást.

## <a name="types-of-keys-in-qna-maker"></a>A QnA Makerban található kulcsok típusai

A QnA Maker szolgáltatás két típusú kulccsal foglalkozik: az **előfizetés kulcsaival** és a **végpont kulcsaival**.

![Kulcskezelés](../media/qnamaker-how-to-key-management/key-management.png)

|Name (Név)|Location|Cél|
|--|--|--|
|Előfizetői azonosító|[Azure Portal](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|Ezek a kulcsok a [QnA Maker Management szolgáltatás API-jai](https://go.microsoft.com/fwlink/?linkid=2092179)elérésére szolgálnak. Ezek az API-k lehetővé teszik a tudásbázisban található kérdések és válaszok szerkesztését, valamint a Tudásbázis közzétételét. Ezek a kulcsok akkor jönnek létre, amikor új QnA Maker szolgáltatást hoz létre.<br><br>Keresse meg ezeket a kulcsokat a **kulcsok** oldalon található **Cognitive Services** erőforrásban.|
|Végponti kulcs|[QnA Maker portál](http://www.qnamaker.ai)|Ezek a kulcsok a közzétett Tudásbázis-végpont elérésére szolgálnak, így választ kaphat a felhasználó kérdéseire. Ezt a végpontot általában a csevegési robotjában vagy a QnA Maker szolgáltatáshoz csatlakozó ügyfélalkalmazás kódjában használja. Ezek a kulcsok akkor jönnek létre, amikor közzéteszi a QnA Maker tudásbázist.<br><br>Keresse meg ezeket a kulcsokat a **szolgáltatás beállításai** lapon. Keresse meg ezt a lapot a legördülő menüben található oldal jobb felső sarkában található felhasználó menüjében.|

## <a name="create-a-new-qna-maker-service"></a>Hozzon létre egy új QnA Maker szolgáltatást

Ez az eljárás létrehozza a Tudásbázis tartalmának kezeléséhez szükséges Azure-erőforrásokat. A lépések elvégzése után megtalálhatja az _előfizetési_ kulcsokat a Azure Portal erőforrás **kulcsok** lapján.

1. Jelentkezzen be a Azure Portalba, és [hozzon létre egy QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) -erőforrást.

1. Válassza a **Létrehozás** lehetőséget a használati feltételek elolvasása után:

    ![Hozzon létre egy új QnA Maker szolgáltatást](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. A **QnA Maker**területen válassza ki a megfelelő szintet és régiókat:

    ![Hozzon létre egy új QnA Maker – tarifacsomag és régiók](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * A **név** mezőben adjon meg egy egyedi nevet a QnA Maker szolgáltatás azonosításához. Ez a név azonosítja azt a QnA Maker végpontot is, amelyhez a Tudásbázis társítva lesz.
    * Válassza ki azt az **előfizetést** , amely alatt a QnA Maker erőforrást telepíteni fogja.
    * Válassza ki a QnA Maker felügyeleti szolgáltatások (portál és felügyeleti API-k) **díjszabási szintjét** . [További részletek a SKU díjszabásáról](https://aka.ms/qnamaker-pricing).
    * Hozzon létre egy új **erőforráscsoportot** (ajánlott), vagy használjon egy meglévőt, amelyben a QnA Maker-erőforrást szeretné telepíteni. QnA Maker több Azure-erőforrást hoz létre. Amikor létrehoz egy erőforráscsoportot az erőforrások tárolásához, egyszerűen megkeresheti, kezelheti és törölheti ezeket az erőforrásokat az erőforráscsoport neve alapján.
    * Válassza ki az **erőforráscsoport helyét**.
    * Válassza ki a **tarifacsomag keresési** az Azure Search szolgáltatás. Ha az ingyenes csomag lehetőség nem érhető el (szürkén jelenik meg), az azt jelenti, hogy már rendelkezik az előfizetésen keresztül üzembe helyezett ingyenes Azure Search-csomaggal. Ebben az esetben az alapszintű Azure Search szinttel kell kezdődnie. Lásd: [Azure Search díjszabása](https://azure.microsoft.com/pricing/details/search/).
    * Válassza ki azt a **keresési helyet** , ahová Azure Search szeretné telepíteni az adatközpontot. Az ügyféladatok tárolási helyétől való korlátozásával meghatározható a Azure Search kiválasztott hely.
    * Az **alkalmazás neve** mezőben adja meg Azure app Service példányának nevét.
    * Alapértelmezés szerint App Service alapértelmezett érték a standard (S1) szint. Létrehozása után módosíthatja a tervet. További információ a [app Service díjszabásáról](https://azure.microsoft.com/pricing/details/app-service/).
    * Válassza ki a **webhely helyét** , ahol a app Service telepíteni fogja.

        > [!NOTE]
        > A **Keresés helye** eltérő lehet a **webhely helyétől**.

    * Adja meg, hogy szeretné-e engedélyezni **Application Insights**. Ha **Application Insights** van engedélyezve, QnA Maker telemetriát gyűjt a forgalom, a csevegési naplók és a hibákat.
    * Válassza ki azt az **alkalmazás-bepillantást** , ahol a Application Insights erőforrást telepíteni fogja.
    * A költségmegtakarítási mértékek esetében [megoszthat](#share-existing-services-with-qna-maker) néhányat, de nem minden QnA Makerhoz létrehozott Azure-erőforrást. 

1. Miután az összes mezőt érvényesíti, válassza a **Létrehozás**lehetőséget. A folyamat eltarthat néhány percig.

1. Az üzembe helyezés befejezése után az előfizetésében létrehozott alábbi erőforrások jelennek meg:

   ![Létrehozott egy új QnA Maker szolgáltatás-erőforrást](../media/qnamaker-how-to-setup-service/resources-created.png)

    Az _Cognitive Services_ típusú erőforrás rendelkezik az _előfizetési_ kulcsokkal.

## <a name="find-subscription-keys-in-the-azure-portal"></a>Előfizetési kulcsok keresése a Azure Portal

Az előfizetési kulcsokat megtekintheti és alaphelyzetbe állíthatja a Azure Portal, ahol létrehozta a QnA Maker erőforrást.

1. Nyissa meg a Azure Portal QnA Maker erőforrását, és válassza ki a _Cognitive Services_ típust tartalmazó erőforrást:

    ![Rendszererőforrás-lista a QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Ugrás a **kulcsok**:

    ![Előfizetői azonosító](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>Végponti kulcsok keresése a QnA Maker portálon

A végpont ugyanabban a régióban található, mint az erőforrás, mert a végponti kulcsok használatával hívható meg a Tudásbázis.

Végpont kulcsok kezelhető a [QnA Maker portal](https://qnamaker.ai).

1. Jelentkezzen be a [QnA Maker portálra](https://qnamaker.ai), nyissa meg a profilt, majd válassza a **szolgáltatás beállításai**:

    ![Végponti kulcs](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. A kulcsok megtekintése és alaphelyzetbe állítása:

    ![Endpoint Key Manager](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Frissítse a kulcsokat, ha úgy gondolja, hogy sérült a biztonsága. Ehhez szükség lehet az ügyfélalkalmazás vagy a robot kódjának megfelelő módosítására.

## <a name="share-existing-services-with-qna-maker"></a>Meglévő szolgáltatások megosztása QnA Maker

QnA Maker több Azure-erőforrást hoz létre. A felügyelet és a költségmegosztás előnyeinek csökkentése érdekében az alábbi táblázat segítségével megismerheti, hogy mit tehet és nem oszthat meg:

|Szolgáltatás|Megosztás|Reason|
|--|--|--|
|Cognitive Services|X|Nem lehetséges a kialakítás|
|App Service-csomag|✔|App Service csomag számára lefoglalt rögzített lemezterület. Ha az azonos App Service-csomaggal rendelkező más alkalmazások jelentős lemezterületet használnak, akkor a QnAMaker App Service példánya problémákba ütközik.|
|App Service|X|Nem lehetséges a kialakítás|
|Application Insights|✔|Megosztható|
|Keresési szolgáltatás|✔|1. `testkb` a QnAMaker szolgáltatás számára fenntartott név, amelyet mások nem használhatnak.<br>2. A név `synonym-map` szerint a QnAMaker szolgáltatás számára van fenntartva a szinonimák leképezése.<br>3. A közzétett tudásbázisok számát a keresési szolgáltatás szintje korlátozza. Ha ingyenes indexek állnak rendelkezésre, más szolgáltatások is használhatják őket.|

További információ az [app Service](../../../app-service/index.yml) és a [Search szolgáltatásról](../../../search/index.yml).

### <a name="using-a-single-search-service"></a>Egyetlen keresési szolgáltatás használata

Ha létrehoz egy QnA szolgáltatást és annak függőségeit (például a keresést) a portálon keresztül, akkor létrejön egy keresési szolgáltatás, amely a QnA Maker szolgáltatáshoz van csatolva. Az erőforrások létrehozása után frissítheti az App Service beállítást egy korábban meglévő keresési szolgáltatás használatára, és eltávolíthatja az imént létrehozott szolgáltatást.

Ha Azure Resource Manager sablonokon keresztül hoz létre QnA szolgáltatást, az összes erőforrást létrehozhatja, és szabályozhatja a App Service létrehozását egy meglévő keresési szolgáltatás használatára.

## <a name="upgrade-qna-maker"></a>QnA Maker frissítése

|Frissítés|Reason|
|--|--|
|[Frissítés](#upgrade-qna-maker-sku) QnA Maker felügyeleti SKU|További kérdésekkel és válaszokkal szeretne rendelkezni a Tudásbázisban.|
|[Frissítés](#upgrade-app-service) App Service SKU|A Tudásbázisnak több kérést kell kiszolgálnia az ügyfélalkalmazástól, például egy csevegési robottól.|
|[Frissítés](#upgrade-the-azure-search-service) Azure Search szolgáltatás|Számos tudásbázist tervez.|


### <a name="upgrade-qna-maker-sku"></a>QnA Maker SKU frissítése

Ha további kérdésekkel és válaszokkal szeretne rendelkezni a Tudásbázisban, az aktuális szinten túlmutatva frissítse a QnA Maker Service díjszabási szintjét.

A QnA Maker felügyeleti SKU frissítése:

1. Nyissa meg a QnA Maker erőforrást a Azure Portal, és válassza az **árképzési szintet**.

    ![Erőforrás QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Válassza ki a megfelelő SKU-t, majd kattintson a **kiválasztás**gombra.

    ![Díjszabás QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>Az App Service frissítése

 Ha a Tudásbázisnak több kérést kell kiszolgálnia az ügyfélalkalmazás számára, frissítse a App Service díjszabási szintjét.

A App Service vertikális [fel](https://docs.microsoft.com/azure/app-service/manage-scale-up) -vagy felskálázása.

Nyissa **meg a Azure Portal** app Service erőforrását, és szükség szerint válassza ki a vertikális felskálázás vagy a **kibővítés** lehetőséget.

![QnA Maker App Service skálázás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-search-service"></a>A Azure Search szolgáltatás frissítése

Ha sok tudásbázist tervez, frissítse a Azure Search Service díjszabási szintjét.

Jelenleg nem végezhető el az Azure Search SKU helyben történő frissítése. Létrehozhat azonban egy új Azure Search-erőforrást a kívánt SKU-val, visszaállíthatja az új erőforrást, majd csatolhatja azt a QnA Maker veremhez. Ehhez kövesse az alábbi lépéseket:

1. Hozzon létre egy új Azure Search-erőforrást a Azure Portalban, és válassza ki a kívánt SKU-t.

    ![Azure Search-erőforrás QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Állítsa vissza az indexeket az eredeti Azure Search-erőforrásból az újat. Tekintse meg a [biztonsági másolat visszaállítási mintájának kódját](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Az adatvisszaállítást követően nyissa meg az új Azure Search-erőforrást, válassza a **kulcsok**lehetőséget, és jegyezze fel a **nevet** és a **rendszergazdai kulcsot**:

    ![Azure Search-kulcsok QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Ha az új Azure Search-erőforrást az QnA Maker veremhöz szeretné kapcsolni, lépjen a QnA Maker App Service példányra.

    ![QnA Maker App Service példány](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Válassza az **Alkalmazásbeállítások** lehetőséget, és módosítsa a **AzureSearchName** és a **AzureSearchAdminKey** mezők beállításait a 3. lépésből.

    ![QnA Maker App Service beállítás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Indítsa újra a App Service példányt.

    ![A QnA Maker App Service példány újraindítása](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>A legújabb futtatókörnyezet-frissítések beszerzése

Az QnAMaker Runtime a Azure App Service példány része, amelyet akkor telepítenek, amikor [létrehoz egy QnAMaker szolgáltatást](./set-up-qnamaker-service-azure.md) a Azure Portal. A futtatókörnyezet rendszeres időközönként végrehajtott frissítéseket. A QnA Maker App Service példánya automatikus frissítési módban van az április 2019-es hely kiterjesztésének kiadása után (5. verzió +). Ez a frissítés úgy lett kialakítva, hogy a frissítések során ne legyenek nulla állásidő.

A jelenlegi verzióját a következő címen tekintheti meg: https://www.qnamaker.ai/UserSettings. Ha a verziószáma 5. x verziónál régebbi, a legújabb frissítések alkalmazásához újra kell indítania App Servicet:

1. Lépjen a QnAMaker szolgáltatásra (erőforráscsoport) a [Azure Portal](https://portal.azure.com).

    ![QnAMaker Azure-erőforráscsoport](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Válassza ki a App Service példányt, és nyissa meg az **Áttekintés** szakaszt.

    ![QnAMaker App Service-példány](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. App Service újraindítása. A frissítési folyamatnak néhány másodpercen belül be kell fejeződnie. A QnAMaker szolgáltatást használó függő alkalmazások vagy robotok nem lesznek elérhetők a végfelhasználók számára az újraindítási időszakban.

    ![A QnAMaker App Service példány újraindítása](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="management-service-region"></a>Felügyeleti szolgáltatási régió

QnA Maker felügyeleti szolgáltatása csak a QnA Maker portál és a kezdeti adatfeldolgozás esetében használatos. Ez a szolgáltatás csak az USA nyugati régiójában érhető el. Ebben a Nyugat-amerikai szolgáltatásban nem tárolunk ügyféladatokat.

## <a name="next-steps"></a>További lépések

További információ az [app Service](../../../app-service/index.yml) és a [Search szolgáltatásról](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Hozzon létre és Tudásbázis közzététele](../Quickstarts/create-publish-knowledge-base.md)
