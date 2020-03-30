---
title: Átmeneti környezetek beállítása
description: Ismerje meg, hogyan telepíthet alkalmazásokat egy nem éles környezetben lévő tárolóhelyre, és hogyan cserélhet automatikusan éles környezetben. Növelje a megbízhatóságot, és szüntesse meg az alkalmazások üzemidő-üzemelő példányait.
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 03/04/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 21e025088e59c7f65f848b332ecb393b05918261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300860"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Átmeneti környezetek beállítása az Azure App Service-ben
<a name="Overview"></a>

Amikor telepíti a webalkalmazást, a Linuxon, a mobil háttérrendszeren vagy az API-alkalmazást az [Azure App Service-ben,](https://go.microsoft.com/fwlink/?LinkId=529714)a **Standard**, **Premium**vagy **Az Isolated** App Service csomag szintjén való futtatáskor az alapértelmezett éles tárolóhely helyett külön üzembe helyezési helyet is használhat. A központi telepítési helyek saját gazdagépnévvel rendelkező élő alkalmazások. Az alkalmazástartalom és a konfigurációs elemek két központi telepítési hely között cserélhetők fel, beleértve az éles tárolóhelyet is. 

Az alkalmazás nem éles tárolóhelyre való üzembe helyezése a következő előnyökkel jár:

* Az alkalmazásmódosításait egy átmeneti központi telepítési tárolóhelyen ellenőrizheti, mielőtt kicserélné azt az éles tárolóhellyel.
* Egy alkalmazás üzembe helyezése egy tárolóhelyre először, és éles környezetben való csere gondoskodik arról, hogy a tárolóhely minden példánya bemelegedjen, mielőtt éles környezetbe cserélik. Ez kiküszöböli az állásidőt az alkalmazás telepítésekor. A forgalom átirányítása zökkenőmentes, és a csereműveletek miatt nem történik kérés. Ezt a teljes munkafolyamatot automatizálhatja az [automatikus csere](#Auto-Swap) konfigurálásával, ha nincs szükség előcserélési érvényesítésre.
* A csere után a korábban előkészített alkalmazással rendelkező tárolóhely rendelkezik az előző éles alkalmazással. Ha a módosításokat cserélték a termelési slot nem a várt módon, akkor végre ugyanazt a swap azonnal, hogy a "legutolsó ismert jó hely" vissza.

Minden Egyes App Service-csomag csomag különböző számú üzembe helyezési helyet támogat. A központi telepítési tárolóhelyek használatáért nem kell külön díjat fizetni. Ha meg szeretné tudni, hogy az alkalmazás rétege hány bővítőhelyet támogat, olvassa el az [App Service-korlátozások című témakört.](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits) 

Ha az alkalmazást egy másik szintre szeretné méretezni, győződjön meg arról, hogy a célréteg támogatja az alkalmazás által már használt bővítőhelyeket. Ha például az alkalmazás ötnél több bővítőhellyel rendelkezik, nem skálázhatja le a **standard** szintre, mert a **Standard** szint csak öt üzembe helyezési helyet támogat. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Pont felvétele
Az alkalmazásnak a **Standard**, **Prémium**vagy **Elkülönített** szinten kell futnia ahhoz, hogy több központi telepítési helyet engedélyezhet.


1. az [Azure Portalon](https://portal.azure.com/)keresse meg és válassza ki **az App Services elemet,** és válassza ki az alkalmazást. 
   
    ![Alkalmazásszolgáltatások keresése](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. A bal oldali ablaktáblában válassza **a Telepítési bővítőhelyek bővítőhely** > **hozzáadása lehetőséget.**
   
    ![Új üzembehelyezési pont hozzáadása](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Ha az alkalmazás még nem a **Standard**, **Prémium**vagy **Elkülönített** szinten van, egy üzenet jelenik meg, amely jelzi a támogatott szinteket a szakaszos közzététel engedélyezéséhez. Ezen a ponton lehetősége van a **Frissítés** lehetőség kiválasztására, és a folytatás előtt az alkalmazás **Méretezés** lapjára lépve.
   > 

3. A **Helyhely hozzáadása** párbeszédpanelen adjon nevet a tárolóhelynek, és válassza ki, hogy klónozni szeretne-e egy alkalmazáskonfigurációt egy másik központi telepítési helyről. A folytatáshoz válassza a **Hozzáadás** gombot.
   
    ![Konfigurációs forrás](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    A konfigurációt bármely meglévő tárolóhelyről klónozhatja. A klónozható beállítások közé tartoznak az alkalmazásbeállítások, a kapcsolati karakterláncok, a nyelvi keretrendszer-verziók, a webszoftvercsatornák, a HTTP-verzió és a platformbitness.

4. A helyhely hozzáadása után a párbeszédpanel bezárásához válassza a **Bezárás** gombot. Az új tárolóhely most megjelenik a **központi telepítési bővítőhelyek** oldalon. Alapértelmezés szerint **a Traffic %** 0-ra van állítva az új tárolóhelyen, és az összes ügyfélforgalom az éles tárolóhelyre irányítja.

5. Válassza ki az új központi telepítési helyet a tárolóhely erőforráslapjának megnyitásához.
   
    ![Központi telepítési hely cím](./media/web-sites-staged-publishing/StagingTitle.png)

    Az átmeneti tárolóhely rendelkezik egy felügyeleti oldal, mint bármely más App Service-alkalmazás. Módosíthatja a tárolóhely konfigurációját. A helyhely neve a lap tetején jelenik meg, hogy emlékeztesse, hogy a központi telepítési hely helyet tekinti meg.

6. Válassza ki az alkalmazás URL-címét a tárolóhely erőforráslapján. A központi telepítési hely saját állomásnévvel rendelkezik, és egyben egy élő alkalmazás is. A központi telepítési helyhez való nyilvános hozzáférés korlátozásához olvassa el az [Azure App Service IP-korlátozásait.](app-service-ip-restrictions.md)

Az új központi telepítési hely nem rendelkezik tartalommal, még akkor sem, ha a beállításokat egy másik tárolóhelyről klónozza. Közzéteheti például [ezt a helyet a Git](app-service-deploy-local-git.md)segítségével. A tárolóhelyre egy másik tárházágból vagy egy másik tárházból telepítheti. 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>Mi történik a csere során?

### <a name="swap-operation-steps"></a>A művelet felcserélési lépései

Ha két bővítőhelyet cserél (általában egy átmeneti tárolóhelyről az éles tárolóhelyre), az App Service az alábbi műveleteket végzi annak érdekében, hogy a céltárolóhely ne tapasztaljon állásidőt:

1. Alkalmazza a következő beállításokat a céltárolóhelyből (például az éles tárolóhelyből) a forrástárolóhely összes példányára: 
    - [Slot-specifikus](#which-settings-are-swapped) alkalmazásbeállítások és kapcsolati karakterláncok, ha vannak ilyenek.
    - [Folyamatos üzembe helyezési](deploy-continuous-deployment.md) beállítások, ha engedélyezve van.
    - [Az App Service hitelesítési](overview-authentication-authorization.md) beállításai, ha engedélyezve vannak.
    
    Ezen esetek bármelyike elindítja az összes példányt a forrás tárolóhelyen az újraindításhoz. Az [előnézettel való csere](#Multi-Phase)során ez az első fázis végét jelzi. A lapozóművelet szünetel, és ellenőrizheti, hogy a forrástárolóhely megfelelően működik-e a céltárolótároló beállításaival.

1. Várja meg, amíg a forrástároló minden példánya befejezi az újraindítást. Ha valamelyik példány nem indul újra, a swap művelet visszaállítja a forrástárolóhely összes módosítását, és leállítja a műveletet.

1. Ha [a helyi gyorsítótár](overview-local-cache.md) engedélyezve van, indítsa el a helyi gyorsítótár inicializálását azáltal, hogy http-kérelmet küld az alkalmazás gyökérkönyvtárához ("/") a forrástároló minden példányán. Várja meg, amíg az egyes példányok visszaadnak egy HTTP-választ. A helyi gyorsítótár inicializálása minden példányon újabb újraindítást okoz.

1. Ha az [automatikus csere](#Auto-Swap) [egyéni bemelegedéssel](#Warm-up)engedélyezve van, indítsa el [az alkalmazás kezdeményezése](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) http-kérelmet az alkalmazás gyökér ("/") minden példányát a forrástárolóhely.

    Ha `applicationInitialization` nincs megadva, indítsa el a HTTP-kérelmet az alkalmazás root a forrástároló minden példányban. 
    
    Ha egy példány egy HTTP-választ ad vissza, a rendszer bemelegítettnek tekinti.

1. Ha a forrástároló n a forrástárolón lévő összes példány sikeresen bemelegedett, cserélje ki a két bővítőhelyet a két bővítőhely útválasztási szabályainak váltásával. Ezt a lépést követően a céltárolóhely (például az éles tárolóhely) rendelkezik az alkalmazás, amely korábban felmelegedett a forrástárolóban.

1. Most, hogy a forrástároló valameddig rendelkezik a csere előtti alkalmazással korábban a céltárolóban, hajtsa végre ugyanazt a műveletet az összes beállítás alkalmazásával, és újraindítja a példányokat.

A csereművelet bármely pontján a kicserélt alkalmazások inicializálásának minden munkája a forrástárolóhelyen történik. A céltároló online marad, amíg a forrástároló hely készül és bemelegszik, függetlenül attól, hogy a swap-ügylet sikeres vagy sikertelen. Átmeneti tárolóhely az éles tárolóhellyel való csere, győződjön meg arról, hogy az éles tárolóhely mindig a céltároló. Ily módon a csereművelet nincs hatással az éles alkalmazás.

### <a name="which-settings-are-swapped"></a>Mely beállítások at cserélték?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Ha egy alkalmazásbeállítást vagy kapcsolati karakterláncot úgy szeretne beállítani, hogy egy adott tárolóhelyhez ragaszkodjon (nem cserélve), nyissa meg az adott tárolóhely **Konfigurációlapját.** Adjon hozzá vagy szerkesztsen egy beállítást, majd válassza a **telepítési hely beállítását.** Ha bejelöli ezt a jelölőnégyzetet, az App Service jelzi, hogy a beállítás nem cserélhető. 

![Bővítőhely beállítása](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Két bővítőhely cseréje 
A központi telepítési helyeket az alkalmazás **központi telepítési bővítőhely-lapon** és az **Áttekintés** lapon cserélheti. A bővítőhely-csere technikai részleteiről a [Mi történik a csere során](#AboutConfiguration).

> [!IMPORTANT]
> Mielőtt egy alkalmazást éles környezetbe cserélne egy központi telepítési helyről, győződjön meg arról, hogy az éles környezet a célhely, és hogy a forrástároló hely összes beállítása pontosan úgy van beállítva, ahogy szeretné, hogy éles környezetben legyenek.
> 
> 

Központi telepítési helyek cseréje:

1. Nyissa meg az alkalmazás **telepítési bővítőhely-lemezeit** tartalmazó lapot, és válassza **a Csere**lehetőséget.
   
    ![Csere gomb](./media/web-sites-staged-publishing/SwapButtonBar.png)

    A **Felcserélés** párbeszédpanelen a kijelölt forrás- és célhelyek beállításai láthatók, amelyek módosulnak.

2. Válassza ki a kívánt **forrás-** és **célhelyeket.** Általában a cél a termelési résidő. Jelölje ki a **Forrásmódosítások** és a **Célmódosítások** lapot is, és ellenőrizze, hogy várható-e a konfigurációs módosítás. Ha elkészült, azonnal kicserélheti a bővítőhelyeket a **Csere**gombra kattintva.

    ![Csere befejezése](./media/web-sites-staged-publishing/SwapImmediately.png)

    Ha meg szeretné tekinteni, hogy a célhely hogyan futna az új beállításokkal, mielőtt a csere ténylegesen megtörténne, ne válassza a **Csere**lehetőséget, hanem kövesse a [Csere előnézettel](#Multi-Phase)című témakör utasításait.

3. Ha végzett, zárja be a párbeszédpanelt a **Bezárás**gombra kattintva.

Ha bármilyen problémája van, olvassa [el a Csereügyletek elhárítása című témakört.](#troubleshoot-swaps)

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Csere előnézettel (többfázisú csere)

Mielőtt éles környezetbe cserélne céltárolóhelyként, ellenőrizze, hogy az alkalmazás a felcserélt beállításokkal fut-e. A forrástároló hely is bemelegedik a csere befejezése előtt, ami elengedhetetlen a kritikus fontosságú alkalmazásokhoz.

Amikor egy csere az előzetes verzió, App Service végrehajtja ugyanazt a [csereműveletet,](#AboutConfiguration) de szünetel az első lépés után. Ezután ellenőrizheti az eredményt az átmeneti tárolóhelyen a csere befejezése előtt. 

Ha megszakítja a felcserélést, az App Service újra alkalmazza a konfigurációs elemeket a forrástárolóhelyre.

Csere előnézettel:

1. Kövesse a [Lapozó központi telepítési bővítőhelyeinek lépéseit,](#Swap) de válassza **a Csere végrehajtása előnézettel**lehetőséget.

    ![Csere előnézettel](./media/web-sites-staged-publishing/SwapWithPreview.png)

    A párbeszédpanelen megtudhatja, hogyan változik a forrástárolóhely konfigurációja az 1.

2. Ha készen áll a csere elindítására, válassza a **Swap indítása**lehetőséget.

    Amikor az első fázis befejeződik, a párbeszédpanelen értesítést kap. Tekintse meg a csere a `https://<app_name>-<source-slot-name>.azurewebsites.net`forrás slot megy . 

3. Ha készen áll a függőben lévő csere befejezésére, válassza a **Csere befejezése** a **csere műveletben** lehetőséget, és válassza **a Teljes csere**lehetőséget.

    Függőben lévő swap törléséhez válassza **a Swap visszavonása** lehetőséget.

4. Ha végzett, zárja be a párbeszédpanelt a **Bezárás**gombra kattintva.

Ha bármilyen problémája van, olvassa [el a Csereügyletek elhárítása című témakört.](#troubleshoot-swaps)

A többfázisú cseregyorsamának automatizálása a [PowerShell használatával](#automate-with-powershell).

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Csere visszaállítása
Ha bármilyen hiba történik a céltárolóban (például az éles tárolóhelyen) egy slot-csere után, állítsa vissza a bővítőhelyeket a csere előtti állapotokba ugyanannak a két tárolóhelynek azonnali cseréjével.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Automatikus felcserélés konfigurálása

> [!NOTE]
> Az automatikus csere nem támogatott a Linuxos webalkalmazásokban.

Az automatikus csereegyszerűsíti az Azure DevOps-forgatókönyveket, ahol folyamatosan szeretné telepíteni az alkalmazást a hidegindítások és az alkalmazás ügyfelei számára nulla állásidő mellett. Ha az automatikus csere engedélyezve van egy tárolóhelyről az éles környezetbe, minden alkalommal, amikor lenyomja a kódmódosításait az adott tárolóhelyre, az App Service automatikusan [éles környezetbe cseréli az alkalmazást,](#swap-operation-steps) miután bemelegedett a forrástárolóhelyen.

   > [!NOTE]
   > Az éles tárolóhely automatikus felcserélésének konfigurálása előtt fontolja meg az automatikus felcserélés tesztelését egy nem éles céltároló helyen.
   > 

Az automatikus csere konfigurálása:

1. Nyissa meg az alkalmazás erőforráslapját. Válassza a Központi telepítési bővítőhelyek*\<kívánt forrástárolóhely->* **Deployment slots** >   >  **konfigurációáltalános** > **beállításai t.**
   
2. Az **Automatikus csere engedélyezve**esetén válassza **a Be**lehetőséget. Ezután válassza ki a kívánt célhelyet az **automatikus cseretelepítési helyhez**, és válassza a **Mentés** a parancssávon lehetőséget. 
   
    ![Az automatikus csere beállításának kiválasztása](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Kódleküldéses parancs végrehajtása a forrástárolóhelyre. Az automatikus csere rövid idő után történik, és a frissítés megjelenik a célhely URL-címén.

Ha bármilyen problémája van, olvassa [el a Csereügyletek elhárítása című témakört.](#troubleshoot-swaps)

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Egyéni bemelegítés megadása

Egyes alkalmazások egyéni bebemelegítési műveleteket igényelhetnek a csere előtt. A `applicationInitialization` web.config konfigurációs eleme lehetővé teszi az egyéni inicializálási műveletek megadását. A [csereművelet](#AboutConfiguration) megvárja, amíg ez az egyéni bemelegítés befejeződik, mielőtt a céltárolótárolóval cserélne. Itt egy minta web.config töredék.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Az elem testreszabásával `applicationInitialization` kapcsolatos további információkért olvassa el a [Leggyakoribb telepítési helycsere-hibák at és azok javítását.](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/)

A bemelegedési viselkedést az alábbi [alkalmazásbeállítások](configure-common.md)egyikével vagy mindkettővel is testreszabhatja:

- `WEBSITE_SWAP_WARMUP_PING_PATH`: Az utat a ping, hogy felmelegedjen a webhelyen. Adja hozzá ezt az alkalmazásbeállítást egy egyéni elérési út megadásával, amely perjellel kezdődik értékként. Például: `/statuscheck`. Az alapértelmezett érték `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Érvényes HTTP-válaszkódok a bemelegedési művelethez. Adja hozzá ezt az alkalmazásbeállítást a HTTP-kódok vesszővel tagolt listájával. Egy példa `200,202` erre. Ha a visszaadott állapotkód nem szerepel a listában, a bemelegedési és a csereműveletek leállnak. Alapértelmezés szerint minden válaszkód érvényes.

> [!NOTE]
> A `<applicationInitialization>` konfigurációs elem az egyes alkalmazások indítási része, míg a két bemelegedési viselkedésalkalmazás-beállítás csak a helytároló kontőket vonatkozik.

Ha bármilyen problémája van, olvassa [el a Csereügyletek elhárítása című témakört.](#troubleshoot-swaps)

## <a name="monitor-a-swap"></a>Csere figyelése

Ha a [csereművelet](#AboutConfiguration) hosszú időt vesz igénybe, a tevékenységnaplóban információkat kaphat a [csereműveletről.](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)

Az alkalmazás erőforráslapján a portálon a bal oldali ablaktáblában válassza a **Tevékenységnapló**lehetőséget.

A naplólekérdezésben egy csereművelet jelenik meg. `Swap Web App Slots` A részletek megtekintéséhez kibonthatja, és kiválaszthatja az egyik alműveletet vagy hibát.

## <a name="route-traffic"></a>Útvonalforgalom

Alapértelmezés szerint az alkalmazás éles URL-címére`http://<app_name>.azurewebsites.net`( ) irányuló összes ügyfélkérés az éles tárolóhelyre kerül. A forgalom egy részét átirányíthatja egy másik tárolóhelyre. Ez a funkció akkor hasznos, ha felhasználói visszajelzésre van szüksége egy új frissítéshez, de még nem áll készen arra, hogy éles környezetben kiadja.

### <a name="route-production-traffic-automatically"></a>Automatikus útvonala a termelési forgalomnak

A termelési forgalom automatikus irányítása:

1. Nyissa meg az alkalmazás erőforráslapját, és válassza **a Telepítési helyek**lehetőséget.

2. A kívánt bővítőhely **Forgalom %** oszlopában adjon meg egy százalékot (0 és 100 között) az útvonalat kívánt teljes forgalom mennyiségének jelzésére. Kattintson a **Mentés** gombra.

    ![Forgalmi százalék beállítása](./media/web-sites-staged-publishing/RouteTraffic.png)

A beállítás mentése után az ügyfelek megadott százaléka véletlenszerűen a nem éles tárolóhelyre kerül. 

Miután egy ügyfél automatikusan egy adott tárolóhelyre irányítja, akkor "rögzítve" az adott tárolóhelyre az adott ügyfélmunkamenet élettartama alatt. Az ügyfélböngészőben a HTTP-fejlécekben lévő `x-ms-routing-name` cookie-t tekintve láthatja, hogy a munkamenet melyik foglalathoz van rögzítve. Az "átmeneti" tárolóhelyre irányított kérelem rendelkezik `x-ms-routing-name=staging`a cookie-val. A termelési tárolóhelyre irányított kérelem rendelkezik `x-ms-routing-name=self`a cookie-val.

   > [!NOTE]
   > Az Azure Portalon kívül is [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az-webapp-traffic-routing-set) használhatja a parancsot az Azure CLI-ben a CI/CD-eszközök, például a DevOps-folyamatok vagy más automatizálási rendszerek útválasztási százalékának beállításához.
   > 

### <a name="route-production-traffic-manually"></a>A termelési forgalom manuális átirányítása

Az automatikus forgalomútválasztás mellett az App Service egy adott tárolóhelyre is továbbíthatja a kérelmeket. Ez akkor hasznos, ha azt szeretné, hogy a felhasználók engedélyezhetjék a bétaverzióját, vagy leiratkozhassanak róluk. A termelési forgalom manuális irányításához használja a `x-ms-routing-name` lekérdezési paramétert.

Ha például azt szeretné, hogy a felhasználók letiltsák a bétaverziós alkalmazást, ezt a hivatkozást a következő weboldalon helyezheti el:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

A `x-ms-routing-name=self` karakterlánc adja meg a termelési tárolóhelyet. Miután az ügyfélböngésző hozzáfér a hivatkozáshoz, a rendszer átirányítja az éles tárolóhelyre. Minden további kérelem `x-ms-routing-name=self` rendelkezik a cookie-val, amely rögzíti a munkamenetet az éles tárolóhelyre.

Ha azt szeretné, hogy a felhasználók engedélyezzék a bétaalkalmazást, állítsa be ugyanazt a lekérdezési paramétert a nem éles környezetben lévő tárolóhely nevére. Például:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Alapértelmezés szerint az új tárolóhelyek a `0%`, szürke színnel jelennek meg. Ha ezt az értéket `0%` explicit módon állítja be (fekete szöveggel jelenik `x-ms-routing-name` meg), a felhasználók manuálisan érhetik el az átmeneti helyet a lekérdezési paraméter használatával. De nem lesznek automatikusan a tárolóhelyre irányítva, mert az útválasztási százalék 0-ra van állítva. Ez egy speciális forgatókönyv, ahol "elrejtheti" az átmeneti tárolóhelyet a nyilvánosság elől, miközben lehetővé teszi a belső csapatok számára a módosítások tesztelését a tárolóhelyen.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Bővítőhely törlése

Keresse meg és válassza ki az alkalmazást. Válassza **a Központi telepítési bővítőhelyek** > *\<helyet a>*  >  **áttekintése parancsra.** A parancssávon válassza a **Törlés** lehetőséget.  

![Központi telepítési hely törlése](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatizálás a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az Azure PowerShell egy olyan modul, amely parancsmagokkal kezelheti az Azure-t a Windows PowerShellen keresztül, beleértve a központi telepítési helyek felügyeletének támogatását az Azure App Service-ben.

Az Azure PowerShell telepítéséről és konfigurálásáról, valamint az Azure PowerShell Azure-előfizetéssel történő hitelesítéséről a [Microsoft Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)című témakörben talál további információt.  

---
### <a name="create-a-web-app"></a>Webalkalmazás létrehozása
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Bővítőhely létrehozása
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Csere kezdeményezése előnézettel (többfázisú csere) és a célhely konfigurációjának alkalmazása a forrástárolóhelyre
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Függőben lévő csere megszakítása (csere ellenőrzéssel) és a forrástárolóhely konfigurációjának visszaállítása
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Üzembehelyezési pontok cseréje
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Swap-események figyelése a tevékenységnaplóban
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Bővítőhely törlése
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>Automatizálás az Erőforrás-kezelő sablonjaival

[Az Azure Resource Manager-sablonok](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview) deklaratív JSON-fájlok, amelyek az Azure-erőforrások üzembe helyezésének és konfigurálásának automatizálására szolgálnak. Ha erőforrás-kezelő sablonokkal szeretne résidőket cserélni, két tulajdonságot kell beállítania a *Microsoft.Web/sites/slots* és a *Microsoft.Web/sites* erőforrásokon:

- `buildVersion`: ez egy karakterlánc-tulajdonság, amely a tárolóhelyen telepített alkalmazás aktuális verzióját jelöli. Például: "v1", "1.0.0.1", vagy "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: ez egy karakterlánc-tulajdonság, `buildVersion` amely meghatározza, hogy mi legyen a tárolóhelynek. Ha a targetBuildVersion nem `buildVersion`egyenlő az aktuális , akkor ez elindítja a `buildVersion`swap művelet et a megadott bővítőhely megtalálásával.

### <a name="example-resource-manager-template"></a>Példa Erőforrás-kezelő sablonra

A következő Erőforrás-kezelő `buildVersion` sablon frissíti az átmeneti `targetBuildVersion` tárolóhely, és állítsa be a termelési tárolóhelyen. Ez a csere a két bővítőhely. A sablon feltételezi, hogy már létrehozott egy "átmeneti" nevű tárolóhellyel létrehozott webappot.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "my_site_name": {
            "defaultValue": "SwapAPIDemo",
            "type": "String"
        },
        "sites_buildVersion": {
            "defaultValue": "v1",
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Web/sites/slots",
            "apiVersion": "2018-02-01",
            "name": "[concat(parameters('my_site_name'), '/staging')]",
            "location": "East US",
            "kind": "app",
            "properties": {
                "buildVersion": "[parameters('sites_buildVersion')]"
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-02-01",
            "name": "[parameters('my_site_name')]",
            "location": "East US",
            "kind": "app",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites/slots', parameters('my_site_name'), 'staging')]"
            ],
            "properties": {
                "targetBuildVersion": "[parameters('sites_buildVersion')]"
            }
        }        
    ]
}
```

Ez a Resource Manager-sablon idempotens, ami azt jelenti, hogy többször is végrehajtható, és a tárolóhelyek azonos állapotát hozza létre. Miután az `targetBuildVersion` első végrehajtás, `buildVersion`meg fog egyezni az aktuális , így a swap nem aktiválódik.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatizálhatja a CLI-t

Az [Azure CLI-parancsok](https://github.com/Azure/azure-cli) központi telepítési helyeket, lásd: [az webapp telepítési hely.](/cli/azure/webapp/deployment/slot)

## <a name="troubleshoot-swaps"></a>Csereügyletek – problémamegoldás

Ha hiba történik a [helycsere](#AboutConfiguration)során, a *d:\home\LogFiles\eventlog.xml*fájlba kerül. Az alkalmazásspecifikus hibanaplóba is be van jelentkezve.

Íme néhány gyakori cserehiba:

- Az alkalmazás gyökéréhez szóló HTTP-kérelem időzülve van elévülve. A csereművelet minden HTTP-kérelemhez 90 másodpercet vár, és legfeljebb 5 alkalommal újrapróbálkozik. Ha az összes újrapróbálkozás időbeli kimenő, a csereművelet leáll.

- A helyi gyorsítótár inicializálása sikertelen lehet, ha az alkalmazás tartalma meghaladja a helyi gyorsítótárhoz megadott helyi lemezkvótát. További információt a [Helyi gyorsítótár – áttekintés című témakörben talál.](overview-local-cache.md)

- Az egyéni bemelegítés során a [HTTP-kérelmek](#Warm-up)belsőleg történnek (anélkül, hogy a külső URL-címen keresztül haladnának). A *Web.config*bizonyos URL-újraírási szabályai val meghiúsulhatnak. Például a tartománynevek átirányítására vagy a HTTPS kényszerítésére vonatkozó szabályok megakadályozhatják, hogy a bemelegedési kérelmek elérjék az alkalmazáskódot. A probléma kerülő megoldásához módosítsa az újraírási szabályokat a következő két feltétel hozzáadásával:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Egyéni bemelegítés nélkül az URL-újraírási szabályok továbbra is blokkolhatják a HTTP-kérelmeket. A probléma kerülő megoldásához módosítsa az újraírási szabályokat a következő feltételek hozzáadásával:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Egyes [IP-korlátozási szabályok](app-service-ip-restrictions.md) megakadályozhatják, hogy a swap-művelet HTTP-kérelmeket küldjön az alkalmazásnak. Az IPv4-címtartományok, `10.` `100.` amelyek a központi telepítéssel kezdődnek, és a központi telepítésen belül vannak. Engedélyeznie kell számukra, hogy csatlakozzanak az alkalmazáshoz.

- A tárolóhely-csere után az alkalmazás váratlan újraindítást tapasztalhat. Ennek az az oka, hogy a csere után a állomásnév-kötési konfiguráció nem szinkronizálódik, ami önmagában nem okoz újraindítást. Bizonyos mögöttes tárolási események (például a tárolókötet-feladatátvételek) azonban észlelhetik ezeket az eltéréseket, és az összes munkavégző folyamat újraindítására kényszeríthetők. Az ilyen típusú újraindítások minimalizálása érdekében állítsa be az [ `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` alkalmazás beállítást](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) az *összes bővítőhelyen.* Ez az alkalmazásbeállítás azonban *nem* működik a Windows Kommunikációs alaprendszer (WCF) alkalmazásokkal.

## <a name="next-steps"></a>További lépések
[Nem éles tárolóhelyekhez való hozzáférés letiltása](app-service-ip-restrictions.md)
