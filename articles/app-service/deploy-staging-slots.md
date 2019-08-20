---
title: Átmeneti környezetek beállítása webalkalmazásokhoz Azure App Serviceban | Microsoft Docs
description: Megtudhatja, hogyan használhatja a szakaszos közzétételt a webalkalmazásokhoz Azure App Serviceban.
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: jpconnoc
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: a6d659d558c15a9a224196c471f7798b1a7f2660
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69623694"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Átmeneti környezetek beállítása a Azure App Serviceban
<a name="Overview"></a>

Amikor üzembe helyezi a webalkalmazást, a webalkalmazást a Linuxon, a mobil háttérben vagy az API-alkalmazáson [Azure app Service](https://go.microsoft.com/fwlink/?LinkId=529714), az alapértelmezett üzemi tárolóhely helyett külön üzembe helyezési pontot használhat a **standard**, **prémium**vagy elkülönített verzióban történő futtatáskor.App Service csomag szintje. Az üzembe helyezési pontok a saját állomásneveket tartalmazó élő alkalmazások. Az alkalmazás tartalmának és konfigurációjának elemei két üzembe helyezési pont között, beleértve az üzemi tárolóhelyet is. 

Az alkalmazás nem éles tárolóhelyre való üzembe helyezése a következő előnyökkel jár:

* Egy átmeneti telepítési tárolóhelyen ellenőrizheti az alkalmazások változásait, mielőtt az éles tárolóhelyre cseréli.
* Ha először helyez üzembe egy alkalmazást egy tárolóhelyen, és az éles környezetbe kerül, akkor győződjön meg arról, hogy a tárolóhely összes példánya bemelegszik az éles környezetbe való váltás előtt. Ezzel kiküszöbölheti az állásidőt az alkalmazás telepítésekor. A forgalom átirányítása zökkenőmentes, és a swap-műveletek miatt a rendszer nem távolít el kérelmeket. Ez a teljes munkafolyamat automatizálható úgy, hogy az [automatikus swap](#Auto-Swap) konfigurálásával nem szükséges az előzetes swap érvényesítése.
* A swap után a korábban előkészített alkalmazáshoz tartozó tárolóhely már az előző éles alkalmazásban van. Ha az éles tárolóhelyre való váltás nem a várt módon történik, akkor azonnal végrehajthatja ugyanezt a cserét, hogy az "utolsó ismert jó hely" vissza legyen hajtva.

Minden App Service díjcsomag különböző számú üzembe helyezési tárolóhelyet támogat. Az üzembe helyezési pontok használata nem jár további költségekkel. Az alkalmazás szintjei által támogatott résidők számának megállapításához tekintse meg a [app Service korlátok](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits)című témakört. 

Ha az alkalmazást egy másik szinten szeretné méretezni, győződjön meg arról, hogy a cél szintje támogatja az alkalmazás által már használt bővítőhelyek számát. Ha például az alkalmazás ötnél több tárolóhelye van, nem méretezheti le a **standard** szintű csomagra, mert a **standard** szint csak öt üzembe helyezési helyet támogat. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Tárhely felvétele
Az alkalmazásnak a **standard**, **prémium**vagy **elkülönített** szinten kell futnia, hogy több üzembe helyezési tárolóhelyet engedélyezzen.

1. A [Azure Portal](https://portal.azure.com/)nyissa meg az alkalmazás [erőforrás](../azure-resource-manager/manage-resources-portal.md#manage-resources)-lapját.

2. A bal oldali panelen válassza az **üzembe helyezési bővítőhelyek** > **hozzáadása tárolóhely**elemet.
   
    ![Új üzembe helyezési pont hozzáadása](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Ha az alkalmazás még nem szerepel a **standard**, **prémium**vagy **elkülönített** szinten, egy üzenet jelenik meg, amely a szakaszos közzététel engedélyezésének támogatott szintjeire utal. Ezen a ponton lehetősége van a **frissítés** lehetőség kiválasztására, és a folytatás előtt lépjen az alkalmazás **skálázás** lapjára.
   > 

3. A **tárolóhely hozzáadása** párbeszédpanelen adja meg a tárolóhely nevét, és válassza ki, hogy az alkalmazás konfigurációját egy másik üzembe helyezési pontról szeretné-e klónozott. A folytatáshoz válassza a **Hozzáadás** lehetőséget.
   
    ![Konfiguráció forrása](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    A konfigurációt bármely meglévő tárolóhelyről klónozással végezheti el. A klónozott beállítások közé tartozik az Alkalmazásbeállítások, a kapcsolati karakterláncok, a nyelvi keretrendszer verziói, a webes szoftvercsatornák, a HTTP-verzió és a platform bitszáma.

4. A tárolóhely hozzáadása után kattintson a **Bezárás** gombra a párbeszédpanel bezárásához. Az új tárolóhely mostantól megjelenik az **üzembe helyezési** pontok oldalon. Alapértelmezés szerint a **(z)%** értéke 0-ra van állítva az új tárolóhely esetében, és az összes ügyfél-forgalom az éles tárolóhelyre van irányítva.

5. Válassza ki az új üzembe helyezési tárolóhelyet a tárolóhely erőforrás-oldalának megnyitásához.
   
    ![Üzembe helyezési pont címe](./media/web-sites-staged-publishing/StagingTitle.png)

    Az előkészítési pont olyan felügyeleti oldallal rendelkezik, mint bármely más App Service alkalmazás. A tárolóhely konfigurációját módosíthatja. A tárolóhely neve megjelenik a lap tetején, és emlékezteti arra, hogy megtekinti az üzembe helyezési pontot.

6. Válassza ki az alkalmazás URL-címét a tárolóhely erőforrás lapján. Az üzembe helyezési pont saját állomásnévvel rendelkezik, és egy élő alkalmazás is. Az üzembe helyezési pont nyilvános hozzáférésének korlátozásához tekintse meg [Azure app Service IP-korlátozásokat](app-service-ip-restrictions.md).

Az új üzembe helyezési pontnak nincs tartalma, még akkor sem, ha a beállításokat egy másik tárolóhelyről klónozott. Például közzéteheti ezt a [tárolóhelyet a git](app-service-deploy-local-git.md)használatával. Egy másik adattárból vagy egy másik adattárból is telepítheti a tárolóhelyre. 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>Mi történik a swap során

### <a name="swap-operation-steps"></a>Műveletek cseréje lépések

Ha két tárolóhelyet cserél le (általában egy átmeneti tárolóhelyről az üzemi tárolóhelyre), App Service a következőket biztosítja annak biztosításához, hogy a cél tárolóhelye ne legyen leállás:

1. Alkalmazza a következő beállításokat a cél tárolóhelyről (például az üzemi tárolóhelyről) a forrás-tárolóhely összes példányára: 
    - A [tárolóhely-specifikus](#which-settings-are-swapped) Alkalmazásbeállítások és a kapcsolatok karakterláncai, ha vannak ilyenek.
    - [Folyamatos üzembe helyezési](deploy-continuous-deployment.md) beállítások, ha engedélyezve vannak.
    - [App Service hitelesítési](overview-authentication-authorization.md) beállítások, ha engedélyezve vannak.
    
    Ezen esetek bármelyike elindítja a forrás-tárolóhely összes példányának újraindítását. Az [előzetes verzióra való csere](#Multi-Phase)során ez az első fázis végét jelöli. A swap művelet szüneteltetve van, és ellenőrizheti, hogy a forrás bővítőhely megfelelően működik-e a cél tárolóhely beállításaival.

1. Várjon, amíg a forrás-tárolóhely minden példánya el nem végzi az újraindítást. Ha valamelyik példány nem indul újra, a swap művelet visszaállítja a forrás tárolóhelyének összes módosítását, és leállítja a műveletet.

1. Ha a [helyi gyorsítótár](overview-local-cache.md) engedélyezve van, aktiválja a helyi gyorsítótár inicializálását úgy, hogy egy HTTP-kérést küld az alkalmazás gyökerének ("/") a forrás tárolóhely minden példányán. Várjon, amíg az egyes példányok HTTP-válaszokat nem adnak vissza. A helyi gyorsítótár inicializálása egy másik újraindítást okoz az egyes példányokon.

1. Ha az [automatikus swap](#Auto-Swap) engedélyezve van az [Egyéni](#Warm-up)bemelegítővel, aktiválja az [alkalmazás indítását](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) úgy, hogy egy HTTP-kérést küld az alkalmazás gyökerének ("/") a forrás tárolóhely minden példányán.

    Ha `applicationInitialization` nincs megadva, indítson el egy HTTP-kérelmet az egyes példányok forrásoldali tárolóhelyének alkalmazás gyökerébe. 
    
    Ha egy példány bármilyen HTTP-választ ad vissza, a rendszer felmelegszik.

1. Ha a forrás tárolóhelyén lévő összes példány sikeresen befejeződik, cserélje le a két tárolóhelyet a két tárolóhely útválasztási szabályainak átváltásával. Ezt a lépést követően a cél tárolóhely (például az éles tárolóhely) azt az alkalmazást alkalmazza, amely korábban a forrás tárolóhelyen van bemelegítve.

1. Most, hogy a forrás-tárolóhely korábban a cél tárolóhelyen lévő előzetes swap alkalmazást használja, végezze el ugyanezt a műveletet az összes beállítás alkalmazásával és a példányok újraindításával.

A swap művelet bármely pontján a felcserélt alkalmazások inicializálásának minden munkája a forrás tárolóhelyen történik. A cél tárolóhely online marad, amíg a forrás tárolóhelye fel van készítve, és bemelegszik, függetlenül attól, hogy a swap sikeres vagy sikertelen volt-e. Ha egy átmeneti tárolóhelyet szeretne cserélni az üzemi tárolóhelyre, győződjön meg arról, hogy az éles tárolóhely mindig a cél tárolóhely. Így a swap művelet nem befolyásolja az üzemi alkalmazást.

### <a name="which-settings-are-swapped"></a>Mely beállítások lesznek felcserélve?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Ha egy adott tárolóhelyre vagy kapcsolódási sztringre kíván beállítani egy adott tárolóhelyet (nem cseréli le), lépjen az adott tárolóhely **konfiguráció** lapjára. Adjon hozzá vagy szerkesszen egy beállítást, majd válassza a **telepítési tárolóhely beállítása**lehetőséget. Ha bejelöli ezt a jelölőnégyzetet, App Service, hogy a beállítás nem cserélhető. 

![Tárhelybeállítás](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Két tárolóhely cseréje 
Az üzembe helyezési pontokat az alkalmazás **üzembe helyezési** pontjainak oldalán és az **Áttekintés** oldalon lehet cserélni. A tárolóhelyek swap szolgáltatásával kapcsolatos technikai részletekért lásd: [Mi történik a swap során](#AboutConfiguration).

> [!IMPORTANT]
> Mielőtt egy alkalmazást becserél egy üzembe helyezési pontról az éles környezetbe, győződjön meg arról, hogy az éles környezet a cél tárolóhelye, és hogy a forrás tárolóhelyén lévő összes beállítás pontosan úgy van konfigurálva, ahogy éles környezetben szeretné.
> 
> 

Az üzembe helyezési pontok cseréje:

1. Nyissa meg az alkalmazás **üzembe helyezési** pontjai lapot, és válassza a **Csere**lehetőséget.
   
    ![Csere gomb](./media/web-sites-staged-publishing/SwapButtonBar.png)

    A **Csere** párbeszédpanel a kiválasztott forrás és cél tárolóhelyek beállításait jeleníti meg, amelyeket a rendszer megváltoztat.

2. Válassza ki a kívánt **forrás** -és **cél** tárolóhelyeket. A cél általában az üzemi tárolóhely. Emellett válassza ki a **forrás módosításait** és a **cél módosítása** lapokat, és ellenőrizze, hogy a konfigurációs változások várhatóak-e. Ha elkészült, azonnal lecserélheti a tárolóhelyeket a **swap**lehetőség kiválasztásával.

    ![Felcserélés befejezése](./media/web-sites-staged-publishing/SwapImmediately.png)

    Ha szeretné megtekinteni, hogy a cél tárolóhelye Mikor fusson az új beállításokkal, mielőtt a swap ténylegesen megtörténne, ne válassza a **swap**lehetőséget, de kövesse a [swap with Preview (előzetes](#Multi-Phase)verzió) című témakör

3. Ha elkészült, a **Bezárás**gombra kattintva zárjuk be a párbeszédpanelt.

Ha bármilyen problémája van, olvassa el a [swap-hibák elhárítása](#troubleshoot-swaps)című témakört.

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Felcserélés előzetes verzióval (többfázisú swap)

> [!NOTE]
> Az előzetes verzióra való felcserélés nem támogatott a Linuxon futó Web Apps alkalmazásokban.

Az éles környezetbe való váltás előtt ellenőrizze, hogy az alkalmazás a megcserélt beállításokkal fut-e. A forrásként szolgáló tárolóhely a swap befejeződése előtt is felmelegszik, ami a kritikus fontosságú alkalmazások számára is kívánatos.

Ha az előzetes verzióra cserél, App Service végrehajtja ugyanazt a [swap-műveletet](#AboutConfiguration) , de az első lépés után szünetel. Ezután ellenőrizheti az eredményét az átmeneti tárolóhelyen a csere befejezése előtt. 

Ha megszakítja a cserét, App Service a konfigurációs elemeket újra alkalmazza a forrás tárolóhelyre.

Az előzetes verzióra való váltáshoz:

1. Kövesse az [üzembe helyezési pontok cseréje](#Swap) szakasz lépéseit, de válassza a csere elvégzése **előzetes**verzióval lehetőséget.

    ![Felcserélés előnézettel](./media/web-sites-staged-publishing/SwapWithPreview.png)

    A párbeszédpanelen megtekintheti, hogy a forrás tárolóhelye hogyan változik az 1. fázisban, és hogyan változik a forrás és a cél tárolóhelye a 2. fázisban.

2. Ha készen áll a swap elindítására, válassza a **Csere indítása**lehetőséget.

    Ha az 1. fázis befejeződik, a rendszer értesítést küld a párbeszédpanelen. Nyissa meg a felcserélés a forrás tárolóhelyen a következő helyre: `https://<app_name>-<source-slot-name>.azurewebsites.net`. 

3. Ha készen áll a függőben lévő csere befejezésére, válassza a swap-csere **befejezése** **műveletet** , és válassza a **Csere befejezése**lehetőséget.

    A függőben lévő csere megszakításához válassza a **Csere megszakítása** helyet.

4. Ha elkészült, a **Bezárás**gombra kattintva zárjuk be a párbeszédpanelt.

Ha bármilyen problémája van, olvassa el a [swap-hibák elhárítása](#troubleshoot-swaps)című témakört.

A többfázisú swap automatizálásához lásd: [automatizálás a PowerShell használatával](#automate-with-powershell).

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Swap visszaállítása
Ha a cél tárolóhelyen (például az üzemi tárolóhelyen) a tárolóhelyek cseréje után valamilyen hiba fordul elő, állítsa vissza a tárolóhelyeket az előcserélés előtti állapotba úgy, hogy az azonos két tárolóhelyet azonnal felcseréli.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Automatikus felcserélés konfigurálása

> [!NOTE]
> Az automatikus swap szolgáltatás nem támogatott a Linuxon futó webalkalmazásokban.

Az automatikus swap szolgáltatás egyszerűsíti az Azure DevOps-forgatókönyveket, amelyekben az alkalmazás folyamatos üzembe helyezését, valamint az alkalmazás ügyfeleinek nulla állásidőt kell alkalmaznia. Ha az automatikus swap engedélyezve van egy tárolóhelyről az éles környezetbe, minden alkalommal, amikor leküldi a kód módosításait az adott tárolóhelyre, App Service automatikusan kicseréli [az alkalmazást éles üzembe](#swap-operation-steps) , miután bemelegedett a forrás tárolóhelyen.

   > [!NOTE]
   > Mielőtt konfigurálja az automatikus swap-t az üzemi tárolóhelyre, érdemes lehet tesztelni az automatikus swap szolgáltatást egy nem éles tárolóhelyen.
   > 

Az automatikus swap beállítása:

1. Nyissa meg az alkalmazás erőforrás-lapját. Válassza ki az **üzembe helyezési** >  *\<pontok kívánt forrásának tárolóhelye >*  > a**konfigurációs** > **általános beállítások**elemet.
   
2. Ha **engedélyezve van az automatikus csere**, válassza **a be**lehetőséget. Ezután válassza ki a kívánt cél tárolóhelyet az **automatikus swap üzembe helyezési**ponthoz, és válassza a **Mentés** parancsot a parancssáv számára. 
   
    ![Automatikus felcserélés konfigurálásának kiválasztása](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Hajtson végre egy kódot a forrás tárolóhelyre való leküldéssel. Az automatikus felcserélés rövid idő elteltével történik, és a frissítés a cél tárolóhelyének URL-címén jelenik meg.

Ha bármilyen problémája van, olvassa el a [swap-hibák elhárítása](#troubleshoot-swaps)című témakört.

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Egyéni bemelegítő beállítása
Az [automatikus](#Auto-Swap)felcserélés használatakor előfordulhat, hogy egyes alkalmazásokban a swap előtt egyedi, bemelegítő műveletek szükségesek. A `applicationInitialization` web. config konfigurációs eleme lehetővé teszi egyéni inicializálási műveletek megadását. A [swap művelet](#AboutConfiguration) megvárja, amíg ez az egyéni bemelegítő befejeződik a cél tárolóhelyre való váltás előtt. Íme egy minta web. config töredék.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Az elem testreszabásával kapcsolatos további `applicationInitialization` információkért lásd: az üzembe helyezési pontok leggyakoribb felcserélésekor fellépő [hibák és azok](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/)kijavítása.

A bemelegítő viselkedést a következő [Alkalmazásbeállítások](configure-common.md)egyikével vagy mindkettővel is testreszabhatja:

- `WEBSITE_SWAP_WARMUP_PING_PATH`: A hely bemelegítésének elérési útja. Adja hozzá ezt az alkalmazás-beállítást egy olyan egyéni elérési út megadásával, amely egy perjelként kezdődik az értékként. Például: `/statuscheck`. Az alapértelmezett érték `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Érvényes HTTP-válasz kódok a bemelegítő művelethez. Adja hozzá ezt az alkalmazás-beállítást a HTTP-kódok vesszővel tagolt listájához. Példa `200,202` :. Ha a visszaadott állapotkód nem szerepel a listában, a bemelegedési és a swap művelet leáll. Alapértelmezés szerint az összes válasz kódja érvényes.

Ha bármilyen problémája van, olvassa el a [swap-hibák elhárítása](#troubleshoot-swaps)című témakört.

## <a name="monitor-a-swap"></a>Swap figyelése

Ha a [swap művelet](#AboutConfiguration) végrehajtása hosszú időt vesz igénybe, akkor a [tevékenység naplójának](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)swap műveletével kapcsolatos információkat kaphat.

Az alkalmazás erőforrás oldalán a portálon, a bal oldali ablaktáblán válassza a **műveletnapló**elemet.

A napló lekérdezésében `Swap Web App Slots`egy swap művelet jelenik meg. Kibonthatja, és kiválaszthatja az egyik alműveletet vagy hibát a részletek megjelenítéséhez.

## <a name="route-traffic"></a>Útvonal forgalma

Alapértelmezés szerint az alkalmazás éles URL-címére (`http://<app_name>.azurewebsites.net`) irányuló összes ügyfél-kérelem át lesz irányítva az üzemi tárolóhelyre. A forgalom egy részét átirányíthatja egy másik tárolóhelyre. Ez a funkció akkor hasznos, ha új frissítéshez felhasználói visszajelzésre van szüksége, de nem áll készen az éles környezetbe való kiadásra.

### <a name="route-production-traffic-automatically"></a>Éles forgalom automatikus irányítása

A termelési forgalom automatikus irányítása:

1. Nyissa meg az alkalmazás erőforrás-lapját, és válassza az **üzembe helyezési**pontok lehetőséget.

2. Az átirányítani kívánt tárolóhely **%** oszlopában meg kell adni a százalékos értéket (0 és 100 között), hogy az összes átirányítani kívánt forgalom mennyiségét képviseljék. Kattintson a **Mentés** gombra.

    ![Forgalmi arány beállítása](./media/web-sites-staged-publishing/RouteTraffic.png)

A beállítás mentése után a rendszer véletlenszerűen továbbítja az ügyfelek megadott százalékos arányát a nem éles tárolóhelyre. 

Miután egy ügyfél automatikusan átirányítja egy adott tárolóhelyre, az adott ügyfél-munkamenet élettartamára "rögzítve" lesz az adott tárolóhelyre. Az ügyfél böngészőjében megtekintheti, hogy a munkamenet melyik tárolóhelyre van rögzítve a http `x-ms-routing-name` -fejlécekben található cookie megkeresésével. Az "átmeneti" tárolóhelyre átirányított kérelem a cookie `x-ms-routing-name=staging`-val rendelkezik. Az éles tárolóhelyre átirányított kérelem a cookie `x-ms-routing-name=self`-val rendelkezik.

### <a name="route-production-traffic-manually"></a>Éles forgalom manuális irányítása

Az automatikus forgalmi útválasztás mellett App Service a kérelmeket egy adott tárolóhelyre irányíthatja. Ez akkor hasznos, ha azt szeretné, hogy a felhasználók bejelentkezhetnek a bétaverzióba, vagy letiltsák azokat. A termelési forgalom manuális átirányításához használja a `x-ms-routing-name` lekérdezési paramétert.

Ha szeretné, hogy a felhasználók elhagyják a bétaverzióját, például a következő hivatkozásra kattintva adhatja meg a weblapon:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

A karakterlánc `x-ms-routing-name=self` meghatározza az üzemi tárolóhelyet. Miután az ügyfél böngészője hozzáfér a hivatkozáshoz, a rendszer átirányítja az éles tárolóhelyre. Minden további kérelem rendelkezik a `x-ms-routing-name=self` cookie-val, amely a munkamenetet az üzemi tárolóhelyre PIN-kódra kéri.

Ha engedélyezni szeretné, hogy a felhasználók belépjenek a bétaverzióba, állítsa ugyanazt a lekérdezési paramétert a nem üzemi tárolóhely nevére. Például:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Alapértelmezés szerint az új bővítőhelyek útválasztási szabályt `0%`kapnak, amely szürke színnel jelenik meg. Ha explicit módon beállítja ezt az értéket `0%` (fekete szövegben látható), a felhasználók a `x-ms-routing-name` lekérdezési paraméter használatával manuálisan érhetik el az átmeneti tárolóhelyet. De a rendszer nem irányítja át automatikusan a tárolóhelyre, mert az útválasztási százalék értéke 0. Ez egy speciális forgatókönyv, ahol "elrejtheti" az átmeneti tárolóhelyet a nyilvános környezetből, miközben lehetővé teszi a belső csapatok számára a tárolóhelyek változásainak tesztelését.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Tárolóhely törlése

Nyissa meg az alkalmazás erőforrás-lapját. *\<>*  > Áttekintéséhezválasszaatelepítésibővítőhelyektárolóhelyét. >  Válassza a **delete (Törlés** ) lehetőséget a parancssáv sávon.  

![Üzembe helyezési pont törlése](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatizálás a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell egy modul, amely parancsmagokat biztosít az Azure-nak a Windows PowerShellen keresztüli felügyeletéhez, beleértve az üzembe helyezési pontok Azure App Service-ben való felügyeletének támogatását.

További információ a Azure PowerShell telepítéséről és konfigurálásáról, valamint az Azure-előfizetéssel végzett Azure PowerShell hitelesítéséről: [Microsoft Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).  

---
### <a name="create-a-web-app"></a>Webalkalmazás létrehozása
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Tárolóhely létrehozása
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Váltás kezdeményezése az előzetes verzióval (többfázisú swap) és a tárolóhely konfigurációjának alkalmazása a forrás tárolóhelyre
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Függőben lévő felcserélés megszakítása (áttekintéssel való csere) és a forrás tárolóhely konfigurációjának visszaállítása
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Üzembe helyezési pontok cseréje
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>A tevékenység naplójában lévő swap-események figyelése
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Tárolóhely törlése
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

---
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatizálás a parancssori felülettel

Az üzembe helyezési pontok [Azure CLI](https://github.com/Azure/azure-cli) -parancsaival kapcsolatban lásd: [az WebApp Deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Swap-hibák megoldása

Ha bármilyen hiba történik egy [tárolóhely cseréjekor](#AboutConfiguration), a rendszer bejelentkezett a *D:\home\LogFiles\eventlog.XML*. Az alkalmazásspecifikus hibanapló is be van jelentkezve.

Íme néhány gyakori swap-hiba:

- Az alkalmazás gyökerére irányuló HTTP-kérelem időkorlátja. A swap művelet minden HTTP-kérelem esetében 90 másodpercig vár, és legfeljebb 5 alkalommal próbálkozik újra. Ha az összes újrapróbálkozás időkorlátja lejárt, a swap művelet leáll.

- A helyi gyorsítótár inicializálása sikertelen lehet, ha az alkalmazás tartalma meghaladja a helyi gyorsítótárhoz megadott helyi lemezkvóta-kvótát. További információ: [helyi gyorsítótár – áttekintés](overview-local-cache.md).

- Az [Egyéni felmelegedés](#Warm-up)során a HTTP-kérések belsőleg történnek (a külső URL-cím nélkül). A *web. config fájlban*megadott URL-re vonatkozó Újraírási szabályokkal sikertelenek lehetnek. Például a tartománynevek átirányítására vagy a HTTPS érvényesítésére vonatkozó szabályok megakadályozhatják a bemelegítő kérések elérését az alkalmazás kódjának elérésekor. A probléma megkerüléséhez módosítsa az Újraírási szabályokat úgy, hogy hozzáadja a következő két feltételt:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Egyéni bemelegítő nélkül az URL-írási szabályok továbbra is letilthatják a HTTP-kérelmeket. A probléma megkerüléséhez módosítsa az Újraírási szabályokat a következő feltétel hozzáadásával:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Egyes [IP-korlátozási szabályok](app-service-ip-restrictions.md) megakadályozhatják, hogy a swap művelet http-kérelmeket küldjön az alkalmazásnak. Azok az IPv4-címtartományok, `10.` amelyek `100.` a-val kezdődnek, és belsőek az üzemelő példányon. Engedélyezze az alkalmazáshoz való kapcsolódást.

## <a name="next-steps"></a>További lépések
[Nem éles tárolóhelyekhez való hozzáférés letiltása](app-service-ip-restrictions.md)
