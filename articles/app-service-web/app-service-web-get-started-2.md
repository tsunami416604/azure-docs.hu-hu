<properties 
    pageTitle="Funkciók hozzáadása az első webalkalmazáshoz" 
    description="Néhány perc alatt menő funkciókat adhat hozzá az első webalkalmazásához." 
    services="app-service\web"
    documentationCenter=""
    authors="cephalin" 
    manager="wpickett" 
    editor="" 
/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="hero-article"
    ms.date="05/12/2016" 
    ms.author="cephalin"
/>

# Funkciók hozzáadása az első webalkalmazáshoz

A [Webalkalmazás üzembe helyezése az Azure-on 5 perc alatt](app-service-web-get-started.md) fejezetben egy egyszerű webalkalmazást telepített az [Azure App Service-be](../app-service/app-service-value-prop-what-is.md). Ebben a cikkben megtudhatja, hogyan adhat gyorsan hozzá remek funkciókat az üzembe helyezett webalkalmazáshoz. Néhány perc alatt:

- hitelesítést kényszeríthet ki felhasználói számára,
- automatikusan skálázhatja az alkalmazást,
- az alkalmazás teljesítményével kapcsolatos riasztásokat fogadhat.

Függetlenül attól, hogy az előző cikkben melyik példaalkalmazást helyezte üzembe, követheti a jelen oktatóanyag utasításait.

A jelen oktatóanyagban lévő három tevékenység csupán néhány példája a számos hasznos funkciónak, amelyeket az App Service-be helyezett webalkalmazások biztosítanak. Számos funkció érhető el az **Ingyenes** szinten (amelyen az első webalkalmazás is fut) és a próbakrediteknek köszönhetően lehetősége nyílik magasabb tarifacsomagokhoz tartozó funkciók kipróbálására is. Biztos lehet benne, hogy a webalkalmazás mindaddig az **Ingyenes** szinten fog maradni, amíg Ön kifejezetten nem módosítja azt másik tarifacsomagra.

>[AZURE.NOTE] Az Azure parancssori felület segítségével létrehozott webalkalmazás az **Ingyenes** szinten fut, amely csak egy virtuális gép példány megosztását teszi lehetővé az erőforráskvótákkal. Az **Ingyenes** szint tartalmával kapcsolatos további információ: [App Service limits](../azure-subscription-service-limits.md#app-service-limits) (Az App Service korlátozásai).

## A felhasználók hitelesítése

Most pedig lássuk, mennyire egyszerű hitelesítést hozzáadni az alkalmazáshoz (további információk: [App Service Authentication/Authorization](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/) (App Service hitelesítés/engedélyezés)).

1. Az alkalmazás imént megnyitott portálpanelén kattintson a **Beállítások** > **Hitelesítés/engedélyezés** lehetőségre.  
    ![Hitelesítés – beállítások panel](./media/app-service-web-get-started/aad-login-settings.png)
    
2. A hitelesítés bekapcsolásához kattintson a **Bekapcsolva** lehetőségre.  
    
4. A **Hitelesítésszolgáltatók** részen kattintson az **Azure Active Directory** lehetőségre.  
    ![Hitelesítés – az Azure AD kiválasztása](./media/app-service-web-get-started/aad-login-config.png)

5. Az **Azure Active Directory beállításai** panelen kattintson az **Expressz** lehetőségre, majd kattintson az **OK** gombra. Alapértelmezés szerint az új Azure AD alkalmazás az alapértelmezett címtárban jön létre.  
 ![Hitelesítés – expressz konfiguráció](./media/app-service-web-get-started/aad-login-express.png)

6. Kattintson a **Save** (Mentés) gombra.  
    ![Hitelesítés – konfiguráció mentése](./media/app-service-web-get-started/aad-login-save.png)

    A sikeres módosítást követően az értesítési csengő egy barátságos üzenet kíséretében zöld színre vált.

7. Visszatérve az alkalmazás portálpanelére kattintson az **URL** hivatkozásra (vagy a menüsoron lévő **Tallózás** lehetőségre). A hivatkozás egy HTTP-cím.  
    ![Hitelesítés – az URL tallózása](./media/app-service-web-get-started/aad-login-browse-click.png)  
    Azonban amint megnyitja az alkalmazást egy új lapon, az URL-mező számos alkalommal átirányítódik és végül HTTPS-címen nyitja meg az alkalmazást. Most azt láthatja, hogy már be van jelentkezve az Azure-előfizetésébe és a rendszer automatikusan hitelesítette Önt az alkalmazásban.  
    ![Hitelesítés – bejelentkezve](./media/app-service-web-get-started/aad-login-browse-http-postclick.png)  
    Ha tehát most egy nem hitelesített munkamenetet nyit meg egy másik böngészőben, akkor ugyanazon URL-címre lépve egy bejelentkezési képernyőt fog látni.  
    <!-- ![Authenticate - login page](./media/app-service-web-get-started/aad-login-browse.png)  -->
    Ha semmit nem tett az Azure Active Directory-val, akkor előfordulhat, hogy az alapértelmezett címtárban nincsenek Azure AD felhasználók. Ebben az esetben az itt lévő egyetlen fiók az Azure előfizetéssel rendelkező Microsoft-fiók. Ezért jelentkeztette be ezt megelőzően a rendszer automatikusan az alkalmazásba ugyanabból a böngészőből. Ezen az oldalon is bejelentkezhet ugyanazzal a Microsoft-fiókkal.

Gratulálunk, Ön mostantól minden, a webalkalmazásba irányuló forgalmat hitelesít.

Talán észrevette, hogy a **Hitelesítés/engedélyezés** panelen sokkal több lehetőség áll az Ön rendelésére, például:

- Közösségi bejelentkezés engedélyezése
- Többször bejelentkezési beállítás engedélyezése
- Az alkalmazás első meglátogatásakor az alapértelmezett viselkedés módosítása

Az App Service „kulcsrakész” megoldást kínál néhány gyakori hitelesítési igényre, így nem Önnek kell megadni a hitelesítési logikát. További információ: [App Service Authentication/Authorization](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/) (App Service hitelesítés/engedélyezés).

## Alkalmazás igény szerinti automatikus skálázása

Most pedig skálázzuk automatikusan az alkalmazást, hogy az a felhasználói igények szerint automatikusan módosítsa a kapacitását (további információ: [Scale up your app in Azure](web-sites-scale.md) (Alkalmazás vertikális felskálázása az Azure-ban) és [Scale instance count manually or automatically](../azure-portal/insights-how-to-scale.md) (Példányszám manuális vagy automatikus skálázása)). 

Röviden: a webalkalmazás két módon skálázható:

- [Vertikális felskálázás](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Nagyobb processzorteljesítmény, több memória, lemezterület és extra funkciók, például dedikált virtuális gépek, egyedi tartományok és tanúsítványok, előkészítési pontok, automatikus skálázás és még sok más. A horizontális felskálázáshoz módosítsa az alkalmazáshoz tartozó App Service-csomag tarifacsomagját.
- [horizontális felskálázás](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Az alkalmazást futtató virtuálisgép-példányok számának növelése.
A tarifacsomagtól függően legfeljebb 50 példányt lehet horizontálisan felskálázni.

Most pedig végre lássuk hozzá az automatikus skálázás beállításához.

1. Először is vertikálisan skálázzunk fel az automatikus skálázás engedélyezéséhez. Az alkalmazás portálpanelén kattintson a **Beállítások** > **Vertikális felskálázás (App Service-csomag)** lehetőségre.  
    ![Vertikális felskálázás – beállítások panel](./media/app-service-web-get-started/scale-up-settings.png)

2. Görgessen és válassza ki az automatikus skálázást támogató legalacsonyabb, **S1 Standard** szintet (a képernyőképen be van karikázva), majd kattintson a **Kiválasztás** lehetőségre.  
    ![Vertikális felskálázás – szint kiválasztása](./media/app-service-web-get-started/scale-up-select.png)

    A vertikális felskálázás kész.
    
    >[AZURE.IMPORTANT] Ez a szint elhasználja az ingyenes próbakreditjeit. Ha használat szerinti fizetéses fiókkal rendelkezik, akkor költségeket terhel a fiókjára.
    
3. Most pedig konfiguráljuk az automatikus skálázást. Az alkalmazás portálpanelén kattintson a **Beállítások** > **Horizontális felskálázás (App Service-csomag)** lehetőségre.  
    ![Horizontális felskálázás -–beállítások panel](./media/app-service-web-get-started/scale-out-settings.png)

4. A **Méretezés** beállítást módosítsa **CPU Percentage** (Processzorhasználat (%) szerint) értékre. A legördülő menü alatti csúszkák ennek megfelelően frissülnek. Ezután határozzon meg egy **1** és **2** közötti **példányszám** tartományt és egy **40** és **80**. közötti **céltartományt**. Ezt a mezőkbe gépelve vagy a csúszkák mozgatásával teheti meg.  
 ![Horizontális felskálázás – automatikus skálázás konfigurálása](./media/app-service-web-get-started/scale-out-configure.png)
    
    Ezen konfiguráció alapján az alkalmazás 80%-os processzor-kihasználtság felett horizontálisan skálázódik fel, 40%-os processzor-kihasználtság alatt pedig horizontálisan skálázódik le automatikusan. 
    
5. A menüsávon kattintson a **Mentés** gombra.

Gratulálunk, az alkalmazás mostantól automatikusan skálázódik.

Talán észrevette, hogy a **Skálázás beállításai** panelen sokkal több lehetőség áll az Ön rendelkezésére, például:

- Manuális skálázás egy adott példányszámra
- Skálázás más teljesítménymutatók, például memória-kihasználtság (%) vagy lemezvárólista alapján
- Teljesítményszabály aktiválásakor a skálázási viselkedés testreszabása
- Ütemezett automatikus skálázás
- Automatikus skálázási viselkedés beállítása jövőbeli eseményekhez

Az alkalmazás vertikális felskálázásával kapcsolatban a [Scale up your app in Azure](../app-service-web/web-sites-scale.md) (Alkalmazás vertikális felskálázása az Azure-ban) című témakörben tekinthet meg további információt. A horizontális felskálázással kapcsolatos további információ: [Scale instance count manually or automatically](../azure-portal/insights-how-to-scale.md) (Példányszám manuális vagy automatikus skálázása).

## Az alkalmazással kapcsolatos riasztások fogadása

Most, hogy az alkalmazás automatikusan skálázódik, mi történik, ha eléri a maximális példányszámot (2) és a CPU kihasználtsága a kívánt szint (80%) felett van? Riasztásokat állíthat be (további információ: [Receive alert notifications](../azure-portal/insights-receive-alert-notifications.md) (Riasztások fogadása)), hogy értesítsék erről a helyzetről, így például újabb skálázásokat hajthat végre az alkalmazáson. Állítsunk is be gyorsan egy riasztást ehhez a forgatókönyvhöz.

1. Az alkalmazás portálpanelén kattintson az **Eszközök** > **Riasztások** lehetőségre.  
    ![Riasztások – beállítások panel](./media/app-service-web-get-started/alert-settings.png)

2. Kattintson a **Riasztás hozzáadása** gombra. Ezután az **Erőforrás** mezőben válassza ki a **(serverfarms)** kifejezéssel végződő erőforrást. Ez az Ön App Service-csomagja.  
    ![Riasztások - riasztások hozzáadása az App Service-csomaghoz](./media/app-service-web-get-started/alert-add.png)

3. Adja meg a **nevet**: `CPU Maxed`, a **metrikát**: **CPU Percentage** (Processzorhasználat (%)) és a **határértéket**: `90`, majd válassza ki az **E-mail küldése a tulajdonosoknak, közreműködőknek és olvasóknak** lehetőséget, és kattintson az **OK** gombra.   
 ![Riasztások – riasztás konfigurálása](./media/app-service-web-get-started/alert-configure.png)
    
    Miután az Azure végzett a létrehozással, a riasztás a **Riasztások** panelen jelenik meg.  
    ![Riasztások – záró nézet](./media/app-service-web-get-started/alert-done.png)

Gratulálunk, mostantól riasztásokat fog kapni. 

Ez a riasztási beállítás ötpercenként ellenőrzi a processzor kihasználtságát. Ha ez a szám 90% fölé megy, akkor Ön, illetve minden engedélyezett felhasználó egy e-mail figyelmeztetést fog kapni. A riasztás fogadására jogosult összes felhasználó megtekintéséhez lépjen vissza az alkalmazás portálpaneljéhez és kattintson a **Hozzáférés** gombra.  
![A riasztás fogadására jogosultak megtekintése](./media/app-service-web-get-started/alert-rbac.png)

Látni fogja, hogy az **Előfizetés rendszergazdái** már **tulajdonosai** az alkalmazásnak. Ez a csoport Önt is magában foglalja, ha Ön az Azure előfizetés (például próbaverziós előfizetés) fiókadminisztrátora. Az Azure szerepköralapú hozzáférés-vezérlésével kapcsolatos további információk: [Azure Role-Based Access Control](../active-directory/role-based-access-control-configure.md) (Azure szerepköralapú hozzáférés-vezérlés).

> [AZURE.NOTE] A riasztási szabályok az Azure szolgáltatásai. További információ: [Receive alert notifications](../azure-portal/insights-receive-alert-notifications.md) (Riasztások fogadása). 

## Következő lépések

A riasztás konfigurálása során talán már észrevette az **Eszközök** panelen lévő eszközök széles skáláját. Segítségükkel hibákat háríthat el, figyelemmel kísérheti a teljesítményt, sebezhetőségeket kereshet, erőforrásokat kezelhet, interakcióba léphet a virtuális gép konzoljával, valamint hasznos bővítményeket adhat hozzá. Az ajánljuk, hogy az egyes eszközökre kattintva fedezze fel a kezében lévő egyszerű, mégis hatékony eszközöket. 

Tudjon meg többet arról, hogyan tehet még többet az üzembe helyezett alkalmazással. Az alábbiak csak a lista kis részét képezik:

- [Buy and configure a custom domain name](custom-dns-web-site-buydomains-web-app.md) (Egyedi tartománynév vásárlása és konfigurálása) – Vásároljon vonzó tartományt webalkalmazása számára a *.azurewebsites.net tartomány helyett. Esetleg használja már meglévő tartományai valamelyikét.
- [Set up staging environments](web-sites-staged-publishing.md) (Átmeneti környezetek beállítása) – Mielőtt hozzálátna az éles használathoz, az alkalmazását egy átmeneti URL-re helyezheti üzembe. Magabiztosan frissítheti élő webalkalmazását. Összetett, több telepítési ponttal rendelkező DevOps-megoldásokat állíthat be. 
- [Folyamatos üzembe helyezés beállítása](app-service-continuous-deployment.md) – Az alkalmazástelepítést a verziókövetési rendszerbe integrálhatja. Minden egyes véglegesítés után üzembe helyezheti az alkalmazását az Azure-ban.
- [Access on-premises resources](web-sites-hybrid-connection-get-started.md) (Helyszíni erőforrások elérése) – Meglévő, helyszíni adatbázisokhoz vagy CRM-rendszerekhez férhet hozzá.
- [Back up your app](web-sites-backup.md) (Az alkalmazás biztonsági mentése) – Biztonsági mentést készíthet webalkalmazásáról és visszaállíthatja azt. Készüljön fel a váratlan meghibásodásokra és hárítsa el azokat.
- [Enable diagnostic logs](web-sites-enable-diagnostic-log.md) (Diagnosztikai naplók engedélyezése) – Tekintse át az Azure IIS-naplóit vagy az alkalmazáskövetéseket. Streamben, letöltve vagy az[Application Insights-ba](../application-insights/app-insights-overview.md) portolva is olvashatja őket a teljes körű elemzés érdekében.
- [Scan your app for vulnerabilities](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) -
(Sebezhetőségek keresése az alkalmazásban) – Ellenőrizheti a webalkalmazás modern fenyegetésekkel szembeni sebezhetőségét a [Tinfoil Security](https://www.tinfoilsecurity.com/) által biztosított szolgáltatás segítségével.
- [Run background jobs](../azure-functions/functions-overview.md) (Háttérfeladatok futtatása) – Futtathat például adatfeldolgozási vagy jelentéskészítési feladatokat.
- [Ismerje meg az App Service működését](../app-service/app-service-how-works-readme.md) 


<!--HONumber=sep16_HO1-->


