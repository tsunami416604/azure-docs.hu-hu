---
title: QnA Maker szolgáltatás beállítása - QnA Maker
description: A QnA Maker tudásbázisai létrehozása előtt először be kell állítania egy QnA Maker szolgáltatást az Azure-ban. Bárki, aki engedéllyel rendelkezik új erőforrások létrehozására egy előfizetésben, beállíthat egy QnA Maker szolgáltatást.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 8ec57f441ba58227e45398c35c7931dc75fa658f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131709"
---
# <a name="manage-qna-maker-resources"></a>A QnA Maker erőforrásainak kezelése

A QnA Maker tudásbázisai létrehozása előtt először be kell állítania egy QnA Maker szolgáltatást az Azure-ban. Bárki, aki engedéllyel rendelkezik új erőforrások létrehozására egy előfizetésben, beállíthat egy QnA Maker szolgáltatást.

Az erőforrás létrehozása előtt hasznos a következő fogalmak alapos ismerete:

* [A QnA Maker erőforrásai](../Concepts/azure-resources.md)
* [Kulcsok készítése és közzététele](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>Új QnA Maker szolgáltatás létrehozása

Ez az eljárás létrehozza a tudásbázis tartalmának kezeléséhez szükséges Azure-erőforrásokat. Miután elvégezte ezeket a lépéseket, megtalálja az _előfizetési_ kulcsokat az erőforrás **kulcsok** lapján az Azure Portalon.

1. Jelentkezzen be az Azure Portalon, és [hozzon létre egy QnA](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) Maker-erőforrást.

1. A feltételek elolvasása után válassza a **Létrehozás** lehetőséget:

    ![Új QnA Maker szolgáltatás létrehozása](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. A **QnA Maker**alkalmazásban válassza ki a megfelelő szinteket és régiókat:

    ![Új QnA Maker szolgáltatás létrehozása – tarifacsomag és régiók](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * A **Név** mezőbe írjon be egy egyedi nevet a QnA Maker szolgáltatás azonosításához. Ez a név azonosítja azt a QnA Maker-végpontot is, amelyhez a tudásbázisok társítva lesznek.
    * Válassza ki azt az **előfizetést,** amely alatt a QnA Maker erőforrás telepítve lesz.
    * Válassza ki a QnA Maker felügyeleti szolgáltatások (portál- és felügyeleti API-k) **díjszabási szintjét.** [További részletek a Termékváltozat díjszabásáról.](https://aka.ms/qnamaker-pricing)
    * Hozzon létre egy új **erőforráscsoportot** (ajánlott), vagy használjon egy meglévőt, amelyben telepíti ezt a QnA Maker erőforrást. A QnA Maker számos Azure-erőforrást hoz létre. Amikor létrehoz egy erőforráscsoportot az erőforrások tárolására, könnyen megtalálhatja, kezelheti és törölheti ezeket az erőforrásokat az erőforráscsoport neve alapján.
    * Válasszon egy **erőforráscsoport helyét**.
    * Válassza ki az Azure Cognitive Search szolgáltatás **keresési díjszabási szintjét.** Ha az ingyenes csomag beállítás nem érhető el (szürkítve jelenik meg), az azt jelenti, hogy már van egy ingyenes szolgáltatás telepítve az előfizetésen keresztül. Ebben az esetben az alapszintű szinttel kell kezdenie. Lásd: [Azure Cognitive Search díjszabási részletek.](https://azure.microsoft.com/pricing/details/search/)
    * Válassza ki azt a **keresési helyet,** ahol telepíteni szeretné az Azure Cognitive Search-indexeket. Az ügyféladatok tárolási helyére vonatkozó korlátozások segítenek meghatározni az Azure Cognitive Search által választott helyet.
    * Az **alkalmazás név** mezőjében adja meg az Azure App Service-példány nevét.
    * Alapértelmezés szerint az App Service alapértelmezés szerint a standard (S1) szint. A tervet a létrehozás után módosíthatja. További információ az [App Service-díjakról.](https://azure.microsoft.com/pricing/details/app-service/)
    * Válassza ki azt a **webhelyet,** ahol az App Service telepítve lesz.

        > [!NOTE]
        > A **keresési hely** eltérhet a webhely **helyétől.**

    * Adja meg, hogy engedélyezi-e az **Application Insights**ot. Ha **az Application Insights** engedélyezve van, a QnA Maker gyűjti a telemetriai adatokat a forgalom, a csevegési naplók és a hibák.
    * Válassza ki azt az **Alkalmazáselemzési helyet,** ahol az Application Insights-erőforrás üzembe kerül.
    * A költségmegtakarítási intézkedések, [megoszthatja](#configure-qna-maker-to-use-different-cognitive-search-resource) néhány, de nem az összes Azure-források létre QnA Maker.

1. Az összes mező érvényesítése után válassza a **Létrehozás gombot.** A folyamat néhány percet is igénybe vehet.

1. A telepítés befejezése után a következő erőforrások jönnek létre az előfizetésben:

   ![Az erőforrás új QnA Maker szolgáltatást hozott létre](../media/qnamaker-how-to-setup-service/resources-created.png)

    A Cognitive _Services-típussal_ rendelkező erőforrás rendelkezik az _előfizetési_ kulcsokkal.

## <a name="find-subscription-keys-in-the-azure-portal"></a>Előfizetési kulcsok keresése az Azure Portalon

Megtekintheti és alaphelyzetbe állíthatja az előfizetési kulcsokat az Azure Portalon, ahol létrehozta a QnA Maker erőforrást.

1. Nyissa meg a QnA Maker erőforrást az Azure Portalon, és válassza ki a Cognitive Services-típussal rendelkező _erőforrást:_

    ![QnA Maker erőforráslista](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Ugrás a **Kulcsok**ra:

    ![Előfizetői azonosító](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>Végpontkulcsok keresése a QnA Maker portálon

A végpont ugyanabban a régióban van, mint az erőforrás, mert a végpontkulcsok a tudásbázis hívásához használatosak.

A végpontkulcsok a [QnA Maker portálról kezelhetők.](https://qnamaker.ai)

1. Jelentkezzen be a [QnA Maker portálra,](https://qnamaker.ai)nyissa meg a profilját, és válassza a **Szolgáltatás beállításai lehetőséget:**

    ![Végpontkulcs](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. A billentyűk megtekintése vagy alaphelyzetbe állítása:

    > [!div class="mx-imgBorder"]
    > ![Végpontkulcs-kezelő](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Frissítse a kulcsokat, ha úgy gondolja, hogy feltörték őket. Ez szükség lehet az ügyfélalkalmazás vagy a robotkód megfelelő módosítására.

### <a name="upgrade-qna-maker-sku"></a>QnA Maker termékváltozatának frissítése

Ha további kérdéseket és válaszokat szeretne kapni a tudásbázisában, az aktuális szinten túl, frissítse a QnA Maker szolgáltatás tarifacsomagját.

A QnA Maker felügyeleti termékváltozatának frissítése:

1. Nyissa meg a QnA Maker-erőforrást az Azure Portalon, és válassza **a Tarifacsomag**lehetőséget.

    ![QnA Maker erőforrás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Válassza ki a megfelelő termékváltozatot, és nyomja **le a Kijelölés gombot.**

    ![QnA Maker árképzés](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>Az alkalmazásszolgáltatás frissítése

 Ha a tudásbázisnak több kérést kell kiszolgálnia az ügyfélalkalmazásból, frissítse az App Service tarifacsomagját.

Az App [Service-t felskálázhatja](https://docs.microsoft.com/azure/app-service/manage-scale-up) vagy horizontális felskálázhatja.

Nyissa meg az App Service-erőforrást az Azure Portalon, és szükség szerint válassza a **Felskálázás** vagy a **Horizontális felskálázás** lehetőséget.

![QnA Maker App Service-skála](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Az Azure Cognitive Search szolgáltatás frissítése

Ha azt tervezi, hogy számos tudásbázissal rendelkezik, frissítse az Azure Cognitive Search szolgáltatás díjszabási szintjét.

Jelenleg nem hajthatja végre az Azure keresési termékváltozatának helybeni frissítését. Azonban létrehozhat egy új Azure keresési erőforrást a kívánt termékváltozattal, visszaállíthatja az adatokat az új erőforrásba, majd csatolhatja a QnA Maker veremhez. Ehhez kövesse az alábbi lépéseket:

1. Hozzon létre egy új Azure-keresési erőforrást az Azure Portalon, és válassza ki a kívánt termékváltozatot.

    ![QnA Maker Azure keresési erőforrás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Állítsa vissza az indexeket az eredeti Azure keresési erőforrásból az újra. Tekintse meg a [biztonsági másolat-visszaállítási mintakódot.](https://github.com/pchoudhari/QnAMakerBackupRestore)

1. Az adatok visszaállítása után nyissa meg az új Azure keresési erőforrást, válassza a **Kulcsok**lehetőséget, és írja le a **Nevet** és a **Rendszergazda kulcsot:**

    ![A QnA Maker Azure keresési kulcsai](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Az új Azure keresési erőforrás és a QnA Maker verem összekapcsolására nyissa meg a QnA Maker App Service-példányt.

    ![QnA Maker App Service-példány](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Válassza ki **az alkalmazásbeállításokat,** és módosítsa a beállításokat az **AzureSearchName** és **az AzureSearchAdminKey** mezőkben a 3.

    ![A QnA Maker App Service beállítása](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Indítsa újra az App Service-példányt.

    ![A QnA Maker App Service-példány újraindítása](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>A legfrissebb futásidejű frissítések betöltése

A QnAMaker futásidejű része az Azure App Service-példány, amely üzembe helyezésekor [egy QnAMaker szolgáltatás](./set-up-qnamaker-service-azure.md) az Azure Portalon. A frissítések rendszeres időközönként a futásidejű. A QnA Maker App Service-példány automatikus frissítési módban van a 2019 áprilisi webhelybővítmény-kiadás (5+-os verzió) után. Ez a frissítés a NULLA állásidő karbantartására szolgál a frissítések során.

Az aktuális verziót https://www.qnamaker.ai/UserSettingsa( A) oldalon ellenőrizheti. Ha a verzió régebbi, mint az 5.x verzió, a legújabb frissítések alkalmazásához újra kell indítania az App Service szolgáltatást:

1. Nyissa meg a QnAMaker szolgáltatást (erőforráscsoportot) az [Azure Portalon.](https://portal.azure.com)

    > [!div class="mx-imgBorder"]
    > ![QnAMaker Azure erőforráscsoport](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Jelölje ki az App Service-példányt, és nyissa meg az **Áttekintés szakaszt.**

    > [!div class="mx-imgBorder"]
    > ![QnAMaker App Service-példány](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. Indítsa újra az App Service-t. A frissítési folyamat nak néhány másodpercen belül be kell fejeződnie. A QnAMaker szolgáltatást használó függő alkalmazások vagy robotok ebben az újraindítási időszakban nem lesznek elérhetők a végfelhasználók számára.

    ![A QnAMaker App Service-példány újraindítása](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>A QnA Maker konfigurálása különböző Cognitive Search erőforrás használatára

Ha létrehoz egy QnA szolgáltatást és annak függőségeit (például a keresést) a portálon keresztül, akkor létrejön egy keresési szolgáltatás, amely a QnA Maker szolgáltatáshoz kapcsolódik. Ezen erőforrások létrehozása után frissítheti az App Service-beállítást egy korábban meglévő keresési szolgáltatás használatához, és eltávolíthatja az imént létrehozottat.

A QnA Maker **App Service-erőforrása** a Cognitive Search erőforrást használja. A QnA Maker által használt Cognitive Search erőforrás módosításához módosítania kell a beállítást az Azure Portalon.

1. A **Rendszergazdai kulcs** és a Cognitive Search erőforrás **neve,** amelyet a QnA Maker használni szeretne.

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és keresse meg a QnA Maker-erőforráshoz társított **App Service-t.** Mindkettőnek ugyanaz a neve.

1. Válassza a **Beállítások**lehetőséget, majd a **Konfiguráció**lehetőséget. Ez megjeleníti a QnA Maker app szolgáltatásának összes meglévő beállítását.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép az Azure Portalról az App Service konfigurációs beállításaival](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Módosítsa a következő kulcsok értékeit:

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. Az új beállítások használatához újra kell indítania az App service-t. Válassza **az Áttekintés**lehetőséget, majd az **Újraindítás**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép az Azure Portal újraindításáról, amely újraindítja az App Service szolgáltatást a konfigurációs beállítások módosítása után](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Ha az Azure Resource Manager-sablonokon keresztül hoz létre egy QnA-szolgáltatást, létrehozhatja az összes erőforrást, és szabályozhatja az App Service létrehozását egy meglévő keresési szolgáltatás használatához.

További információ az App [Service-alkalmazás beállításainak](../../../app-service/configure-common.md#configure-app-settings)konfigurálásáról.

## <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>Az Alkalmazásszolgáltatás tétlen beállításának konfigurálása az időhosszabbítás elkerülése érdekében

Az alkalmazásszolgáltatás, amely a QnA Maker előrejelzési futásidejű egy közzétett tudásbázis, egy tétlen időkérési konfiguráció, amely alapértelmezés szerint automatikusan időkérés, ha a szolgáltatás tétlen. A QnA Maker számára ez azt jelenti, hogy az előrejelzési futásidejű generateAnswer API időnként időbeli idővel kimenő időszakok után nincs forgalom.

Annak érdekében, hogy az előrejelzési végpont alkalmazás betöltve akkor is, ha nincs forgalom, állítsa az ajárati járat, hogy mindig be.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Keresse meg és válassza ki a QnA Maker erőforrás alkalmazásszolgáltatását. Ugyanaz lesz a neve, mint a QnA Maker erőforrásnak, de más típusú App **Service-t** fog rendelkezni.
1. Keresse meg **a Beállítások lehetőséget,** majd válassza a **Konfiguráció**lehetőséget.
1. A Konfiguráció ablaktáblán válassza az **Általános beállítások**lehetőséget, majd a **Mindig bekapcsolva**lehetőséget, majd a **Be** értéket.

    > [!div class="mx-imgBorder"]
    > ![A Konfiguráció ablaktáblán válassza a **Általános beállítások**, majd a **Mindig bekapcsolva** lehetőséget, és válassza a **Be** értéket értékként.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. A konfiguráció mentéséhez válassza a **Mentés** gombot.
1. A rendszer megkérdezi, hogy újra szeretné-e indítani az alkalmazást az új beállítás használatához. Válassza a **Folytatás** elemet.

További információ az App Service [Általános beállításainak](../../../app-service/configure-common.md#configure-general-settings)konfigurálásáról.

## <a name="delete-azure-resources"></a>Azure-erőforrások törlése

Ha törli a QnA Maker tudásbázisaihoz használt Azure-erőforrások bármelyikét, a tudásbázisok már nem fognak működni. Mielőtt bármilyen erőforrást delegálna, győződjön meg arról, hogy a tudásbázist a Beállítások lapról **exportálja.**

## <a name="next-steps"></a>További lépések

További információ az [App service](../../../app-service/index.yml) és a [Search szolgáltatásról.](../../../search/index.yml)

> [!div class="nextstepaction"]
> [Tudásbázis létrehozása és közzététele](../Quickstarts/create-publish-knowledge-base.md)