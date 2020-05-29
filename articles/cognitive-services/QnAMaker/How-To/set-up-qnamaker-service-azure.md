---
title: QnA Maker szolgáltatás beállítása – QnA Maker
description: Mielőtt bármilyen QnA Maker tudásbázist létrehozni, először be kell állítania egy QnA Maker szolgáltatást az Azure-ban. Az előfizetésben lévő új erőforrások létrehozásához szükséges engedélyekkel rendelkező bárki beállíthat QnA Maker szolgáltatást.
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: 521d0388e4ee739b1ac840e482174ac466781f5f
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171174"
---
# <a name="manage-qna-maker-resources"></a>QnA Maker erőforrások kezelése

Mielőtt bármilyen QnA Maker tudásbázist létrehozni, először be kell állítania egy QnA Maker szolgáltatást az Azure-ban. Az előfizetésben lévő új erőforrások létrehozásához szükséges engedélyekkel rendelkező bárki beállíthat QnA Maker szolgáltatást.

Az erőforrás létrehozása előtt a következő fogalmak alapos ismerete hasznos:

* [Erőforrások QnA Maker](../Concepts/azure-resources.md)
* [Kulcsok létrehozása és közzététele](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>Új QnA Maker szolgáltatás létrehozása

Ez az eljárás létrehozza a Tudásbázis tartalmának kezeléséhez szükséges Azure-erőforrásokat. A lépések elvégzése után megtalálhatja az _előfizetési_ kulcsokat a Azure Portal erőforrás **kulcsok** lapján.

1. Jelentkezzen be a Azure Portalba, és [hozzon létre egy QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) -erőforrást.

1. Válassza a **Létrehozás** lehetőséget a használati feltételek elolvasása után:

    ![Új QnA Maker szolgáltatás létrehozása](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. A **QnA Maker**területen válassza ki a megfelelő szintet és régiókat:

    ![Új QnA Maker szolgáltatás létrehozása – árképzési réteg és régiók](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * A **név** mezőben adjon meg egy egyedi nevet a QnA Maker szolgáltatás azonosításához. Ez a név azonosítja azt a QnA Maker végpontot is, amelyhez a Tudásbázis társítva lesz.
    * Válassza ki azt az **előfizetést** , amely alatt a QnA Maker erőforrást telepíteni fogja.
    * Válassza ki a QnA Maker felügyeleti szolgáltatások (portál és felügyeleti API-k) **díjszabási szintjét** . [További részletek a SKU díjszabásáról](https://aka.ms/qnamaker-pricing).
    * Hozzon létre egy új **erőforráscsoportot** (ajánlott), vagy használjon egy meglévőt, amelyben a QnA Maker-erőforrást szeretné telepíteni. QnA Maker több Azure-erőforrást hoz létre. Amikor létrehoz egy erőforráscsoportot az erőforrások tárolásához, egyszerűen megkeresheti, kezelheti és törölheti ezeket az erőforrásokat az erőforráscsoport neve alapján.
    * Válassza ki az **erőforráscsoport helyét**.
    * Válassza ki az Azure Cognitive Search szolgáltatás **keresési díjszabási szintjét** . Ha az ingyenes csomag lehetőség nem érhető el (szürkén jelenik meg), az azt jelenti, hogy már rendelkezik az előfizetésen keresztül üzembe helyezett ingyenes szolgáltatással. Ebben az esetben az alapszintű csomaggal kell kezdődnie. Tekintse meg az [Azure Cognitive Search díjszabását](https://azure.microsoft.com/pricing/details/search/).
    * Válassza ki azt a **keresési helyet** , ahová telepíteni szeretné az Azure Cognitive Search indexeit. Az ügyféladatok tárolási helyétől való korlátozásával meghatározhatja az Azure Cognitive Search számára kiválasztott helyet.
    * Az **alkalmazás neve** mezőben adja meg Azure app Service példányának nevét.
    * Alapértelmezés szerint App Service alapértelmezett érték a standard (S1) szint. A tervet a létrehozás után is módosíthatja. További információ a [app Service díjszabásáról](https://azure.microsoft.com/pricing/details/app-service/).
    * Válassza ki a **webhely helyét** , ahol a app Service telepíteni fogja.

        > [!NOTE]
        > A **Keresés helye** eltérő lehet a **webhely helyétől**.

    * Adja meg, hogy szeretné-e engedélyezni **Application Insights**. Ha a **Application Insights** engedélyezve van, QnA Maker gyűjti a telemetria a forgalom, a csevegési naplók és a hibák alapján.
    * Válassza ki azt az **alkalmazás-bepillantást** , ahol a Application Insights erőforrást telepíteni fogja.
    * A költségmegtakarítási mértékek esetében [megoszthat](#configure-qna-maker-to-use-different-cognitive-search-resource) néhányat, de nem minden QnA Makerhoz létrehozott Azure-erőforrást.

1. Miután az összes mezőt érvényesíti, válassza a **Létrehozás**lehetőséget. A folyamat eltarthat néhány percig.

1. Az üzembe helyezés befejezése után az előfizetésében létrehozott alábbi erőforrások jelennek meg:

   ![Az erőforrás új QnA Maker szolgáltatást hozott létre](../media/qnamaker-how-to-setup-service/resources-created.png)

    Az _Cognitive Services_ típusú erőforrás rendelkezik az _előfizetési_ kulcsokkal.


## <a name="find-subscription-keys-in-the-azure-portal"></a>Előfizetési kulcsok keresése a Azure Portal

Az előfizetési kulcsokat megtekintheti és alaphelyzetbe állíthatja a Azure Portal, ahol létrehozta a QnA Maker erőforrást.

1. Nyissa meg a Azure Portal QnA Maker erőforrását, és válassza ki a _Cognitive Services_ típust tartalmazó erőforrást:

    ![QnA Maker erőforráslista](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Ugrás a **kulcsok**:

    ![Előfizetői azonosító](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>Végponti kulcsok keresése a QnA Maker portálon

A végpont ugyanabban a régióban található, mint az erőforrás, mert a végponti kulcsok használatával hívható meg a Tudásbázis.

A végponti kulcsok kezelhetők a [QnA Maker portálról](https://qnamaker.ai).

1. Jelentkezzen be a [QnA Maker portálra](https://qnamaker.ai), nyissa meg a profilt, majd válassza a **szolgáltatás beállításai**:

    ![Végpont kulcsa](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. A kulcsok megtekintése és alaphelyzetbe állítása:

    > [!div class="mx-imgBorder"]
    > ![Endpoint Key Manager](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Frissítse a kulcsokat, ha úgy gondolja, hogy sérült a biztonsága. Ehhez szükség lehet az ügyfélalkalmazás vagy a robot kódjának megfelelő módosítására.

## <a name="upgrade-qna-maker-sku"></a>QnA Maker SKU frissítése

Ha további kérdésekkel és válaszokkal szeretne rendelkezni a Tudásbázisban, az aktuális szinten túlmutatva frissítse a QnA Maker Service díjszabási szintjét.

A QnA Maker felügyeleti SKU frissítése:

1. Nyissa meg a QnA Maker erőforrást a Azure Portal, és válassza az **árképzési szintet**.

    ![Erőforrás QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Válassza ki a megfelelő SKU-t, majd kattintson a **kiválasztás**gombra.

    ![Díjszabás QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>App Service frissítése

 Ha a Tudásbázisnak több kérést kell kiszolgálnia az ügyfélalkalmazás számára, frissítse a App Service díjszabási szintjét.

A App Service vertikális [fel](https://docs.microsoft.com/azure/app-service/manage-scale-up) -vagy felskálázása.

Nyissa **meg a Azure Portal** app Service erőforrását, és szükség szerint válassza ki a vertikális felskálázás vagy a **kibővítés** lehetőséget.

![QnA Maker App Service skálázás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-the-azure-cognitive-search-service"></a>Az Azure Cognitive Search szolgáltatás frissítése

Ha sok tudásbázist tervez, frissítse az Azure Cognitive Search Service díjszabási szintjét.

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

Az QnAMaker Runtime a Azure App Service példány része, amelyet akkor telepítenek, amikor [létrehoz egy QnAMaker szolgáltatást](./set-up-qnamaker-service-azure.md) a Azure Portal. A rendszer rendszeresen frissíti a frissítéseket a futtatókörnyezetben. A QnA Maker App Service példánya automatikus frissítési módban van az április 2019-es hely kiterjesztésének kiadása után (5. verzió +). Ez a frissítés úgy lett kialakítva, hogy a frissítések során ne legyenek nulla állásidő.

A jelenlegi verzióját a következő címen tekintheti meg: https://www.qnamaker.ai/UserSettings . Ha a verziószáma 5. x verziónál régebbi, a legújabb frissítések alkalmazásához újra kell indítania App Servicet:

1. Lépjen a QnAMaker szolgáltatásra (erőforráscsoport) a [Azure Portal](https://portal.azure.com).

    > [!div class="mx-imgBorder"]
    > ![QnAMaker Azure-erőforráscsoport](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Válassza ki a App Service példányt, és nyissa meg az **Áttekintés** szakaszt.

    > [!div class="mx-imgBorder"]
    > ![QnAMaker App Service-példány](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. App Service újraindítása. A frissítési folyamatnak néhány másodpercen belül be kell fejeződnie. A QnAMaker szolgáltatást használó függő alkalmazások vagy robotok nem lesznek elérhetők a végfelhasználók számára az újraindítási időszakban.

    ![A QnAMaker App Service példány újraindítása](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>QnA Maker konfigurálása különböző Cognitive Search-erőforrások használatára

Ha létrehoz egy QnA szolgáltatást és annak függőségeit (például a keresést) a portálon keresztül, akkor létrejön egy keresési szolgáltatás, amely a QnA Maker szolgáltatáshoz van csatolva. Az erőforrások létrehozása után frissítheti az App Service beállítást egy korábban meglévő keresési szolgáltatás használatára, és eltávolíthatja az imént létrehozott szolgáltatást.

A QnA Maker **app Service** erőforrása a Cognitive Search erőforrást használja. A QnA Maker által használt Cognitive Search erőforrás módosításához módosítania kell a beállítást a Azure Portalban.

1. Kérje le a használni QnA Maker kívánt Cognitive Search erőforrás **rendszergazdai kulcsát** és **nevét** .

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és keresse meg a QnA Maker erőforráshoz társított **app Service** . Mindkettő ugyanazzal a névvel van ellátva.

1. Válassza a **Beállítások**, majd a **konfiguráció**lehetőséget. Ekkor megjelenik a QnA Maker App Service összes meglévő beállítása.

    > [!div class="mx-imgBorder"]
    > ![A App Service konfigurációs beállításait bemutató Azure Portal képernyőképe](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Módosítsa a következő kulcsok értékeit:

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. Az új beállítások használatához újra kell indítania az App Service-t. Válassza az **Áttekintés**, majd az **Újraindítás**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a Azure Portal újraindításáról App Service a konfigurációs beállítások módosítása után](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Ha Azure Resource Manager sablonokon keresztül hoz létre QnA szolgáltatást, az összes erőforrást létrehozhatja, és szabályozhatja a App Service létrehozását egy meglévő keresési szolgáltatás használatára.

További információ a App Service [alkalmazás beállításainak](../../../app-service/configure-common.md#configure-app-settings)konfigurálásáról.

## <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>Az App Service tétlen beállítása az időkorlát elkerüléséhez

Az App Service, amely a közzétett Tudásbázis QnA Maker előrejelzési futtatókörnyezetét szolgálja, Üresjárati időkorlát-konfigurációval rendelkezik, amely alapértelmezés szerint automatikusan időtúllépést okoz, ha a szolgáltatás üresjáratban van. A QnA Maker esetében ez azt jelenti, hogy az előrejelzési futtatókörnyezet generateAnswer API-ját időnként a forgalom nélküli időszakok után időtúllépés jelzi.

Annak érdekében, hogy az előrejelzési végpont alkalmazás betöltődik, még akkor is, ha nincs forgalom, állítsa az inaktív állapotot mindig bekapcsolt értékre.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg és válassza ki a QnA Maker erőforrás app Service-t. Ugyanaz a neve, mint a QnA Maker erőforrásnak, de más **típusú** app Service is fog rendelkezni.
1. Keresse meg a **beállításokat** , majd válassza a **Konfigurálás**lehetőséget.
1. A konfiguráció ablaktáblán válassza az **általános beállítások**, majd a **mindig bekapcsolva**lehetőséget, **és válassza a be értéket** .

    > [!div class="mx-imgBorder"]
    > ![A konfiguráció ablaktáblán válassza a * * általános beállítások * * lehetőséget, majd a * * always on * * elemet, és válassza a * * * * elemet az értékként.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. A konfiguráció mentéséhez kattintson a **Mentés** gombra.
1. A rendszer megkérdezi, hogy szeretné-e újraindítani az alkalmazást az új beállítás használatához. Válassza a **Folytatás** elemet.

További információ a App Service [általános beállításainak](../../../app-service/configure-common.md#configure-general-settings)konfigurálásáról.

## <a name="business-continuity-with-traffic-manager"></a>Üzletmenet-folytonosság a Traffic Managerrel

Az üzletmenet-folytonossági terv elsődleges célja, hogy egy rugalmas Tudásbázis-végpontot hozzon létre, amely nem gondoskodik a bot vagy az alkalmazás elfogyasztásának leállásáról.

> [!div class="mx-imgBorder"]
> ![QnA Maker BCP-terv](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

A fentiekben képviselt magas szintű ötlet a következő:

1. Két párhuzamos QnA Maker- [szolgáltatás](set-up-qnamaker-service-azure.md) beállítása az [Azure párosított régiókban](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

1. [Készítsen biztonsági másolatot](../../../app-service/manage-backup.md) az elsődleges QnA Maker app Service-ről, és [állítsa vissza](../../../app-service/web-sites-restore.md) a másodlagos telepítésben. Ez biztosítja, hogy mindkét beállítás ugyanazzal az állomásnévvel és kulcsokkal működjön.

1. Tartsa szinkronban az elsődleges és a másodlagos Azure Search-indexeket. A GitHub [-minta használatával](https://github.com/pchoudhari/QnAMakerBackupRestore) megtekintheti, hogyan állíthatja vissza az Azure indexeket.

1. A Application Insights biztonsági mentése [folyamatos exportálással](../../../application-insights/app-insights-export-telemetry.md).

1. Az elsődleges és másodlagos csomagok beállítása után a [Traffic Manager](../../../traffic-manager/traffic-manager-overview.md) segítségével konfigurálja a két végpontot, és állítson be útválasztási módszert.

1. Létre kell hoznia egy Transport Layer Security (TLS), korábbi nevén SSL (SSL) tanúsítványt a Traffic Manager-végponthoz. [A TLS/SSL-tanúsítvány kötése](../../../app-service/configure-ssl-bindings.md) az App Servicesben.

1. Végül használja a Traffic Manager-végpontot a robotban vagy az alkalmazásban.

## <a name="delete-azure-resources"></a>Azure-erőforrások törlése

Ha törli a QnA Maker tudásbázishoz használt Azure-erőforrásokat, a tudásbázisok nem fognak működni. Az erőforrások törlése előtt győződjön meg arról, hogy a tudásbázisokat a **Beállítások** lapról exportálja.

## <a name="next-steps"></a>Következő lépések

További információ az [app Service](../../../app-service/index.yml) és a [Search szolgáltatásról](../../../search/index.yml).

> [!div class="nextstepaction"]
> [További információ a másokkal való létrehozásról](../how-to/collaborate-knowledge-base.md)
