---
title: Konfigurációs gyakori kérdések
description: Válaszok az Azure App Service konfigurációs és felügyeleti problémáival kapcsolatos gyakori kérdésekre.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 300294f37c809b01fe8fba7e627d6bc5bdc9903a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942935"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Konfigurációs és felügyeleti gyakori kérdések az Azure-beli webalkalmazásokról

Ez a cikk választ ad az Azure App Service Web [Apps szolgáltatásának](https://azure.microsoft.com/services/app-service/web/)konfigurációs és kezelési problémáival kapcsolatos gyakori kérdésekre.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Vannak olyan korlátozások, amelyeket figyelembe kell vegyek, ha át szeretném helyezni az App Service-erőforrásokat?

Ha azt tervezi, hogy áthelyezni az App Service-erőforrások egy új erőforráscsoport vagy előfizetés, van néhány korlátozás, hogy tudatában kell lennie. További információt az [App Service-korlátozások című témakörben talál.](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Hogyan használhatok egyéni tartománynevet a webalkalmazásomhoz?

Az egyéni tartománynév Azure-webalkalmazással való használatával kapcsolatos gyakori kérdésekre az [Egyéni tartománynév](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name)hozzáadása című hétperces videóban talál választ. A videó bemutatja, hogyan adhat hozzá egyéni tartománynevet. Azt ismerteti, hogyan használhatja a saját URL-t a *.azurewebsites.net URL-cím helyett az App Service webalkalmazással. Az [egyéni tartománynevek leképezésének](app-service-web-tutorial-custom-domain.md)részletes forgatókönyvét is megtekintheti.


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Hogyan vásárolhatok új egyéni domaint a webalkalmazásomhoz?

Ha tudni szeretné, hogyan vásárolhat és állíthat be egyéni tartományt az App Service-webalkalmazáshoz, olvassa el [az Egyéni tartománynév vásárlása és konfigurálása az App Service szolgáltatásban című témakört.](manage-custom-dns-buy-domain.md)


## <a name="how-do-i-upload-and-configure-an-existing-ssl-certificate-for-my-web-app"></a>Hogyan tölthetek fel és konfigurálhatok meglévő SSL-tanúsítványt a webalkalmazásomhoz?

Ha tudni szeretné, hogyan tölthet fel és állíthat be egy meglévő egyéni SSL-tanúsítványt, olvassa [el az SSL-tanúsítvány hozzáadása az App Service-alkalmazáshoz](configure-ssl-certificate.md).


## <a name="how-do-i-purchase-and-configure-a-new-ssl-certificate-in-azure-for-my-web-app"></a>Hogyan vásárolhatok és konfigurálhatok új SSL-tanúsítványt az Azure-ban a webalkalmazásomhoz?

Ha meg szeretné tudni, hogyan vásárolhat ssl-tanúsítványt az App Service-webalkalmazáshoz, olvassa el [az SSL-tanúsítvány hozzáadása az App Service-alkalmazáshoz](configure-ssl-certificate.md).


## <a name="how-do-i-move-application-insights-resources"></a>Hogyan helyezhetem át az Application Insights-erőforrásokat?

Jelenleg az Azure Application Insights nem támogatja az áthelyezési műveletet. Ha az eredeti erőforráscsoport tartalmaz egy Application Insights-erőforrást, nem helyezheti át az erőforrást. Ha az Application Insights-erőforrást is tartalmazza egy App Service-alkalmazás áthelyezésekénél, a teljes áthelyezési művelet sikertelen lesz. Azonban az Application Insights és az App Service-csomag nem kell ugyanabban az erőforráscsoportban, mint az alkalmazás az alkalmazás megfelelő működéséhez.

További információt az [App Service-korlátozások című témakörben talál.](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Hol találhatok útmutatásellenőrző listát, és hogyan tudhatok meg többet az erőforrás-áthelyezési műveletekről?

[Az App Service-korlátozások](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md) azt mutatják be, hogyan helyezheti át az erőforrásokat egy új előfizetésbe vagy egy új erőforráscsoportba ugyanabban az előfizetésben. Információkat kaphat az erőforrás áthelyezési ellenőrzőlistájáról, megtudhatja, hogy mely szolgáltatások támogatják az áthelyezési műveletet, és további információkat kaphat az App Service korlátairól és más témakörökről.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Hogyan állíthatom be a webalkalmazás kiszolgálói időzónáját?

A webalkalmazás kiszolgálói időzónájának beállítása:

1. Az Azure Portalon az App Service-előfizetésben nyissa meg az **Alkalmazás beállítások** menüjét.
2. Az **Alkalmazásbeállítások**csoportban adja hozzá ezt a beállítást:
    * Kulcs = WEBSITE_TIME_ZONE
    * Érték = *A kívánt időzóna*
3. Kattintson a **Mentés** gombra.

A Windows rendszeren futó alkalmazásszolgáltatások ról az [Alapértelmezett időzónák](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones) című cikk **Időzóna** oszlopában olvashat az elfogadott értékekről. A Linuxon futó alkalmazásszolgáltatások esetében állítsa be a [TZ-adatbázis nevét](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) időzóna-értékként. Íme egy példa a TZ adatbázis nevére: America/Adak.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Miért nem sikerül a folyamatos WebJobs?Why my continuous WebJobs sometimes fail?

Alapértelmezés szerint a webalkalmazások törlődnek, ha egy meghatározott ideig tétlenek. Ez lehetővé teszi, hogy a rendszer erőforrásokat takarítson meg. Az Alapszintű és a Normál csomagokban bekapcsolhatja az **Mindig bekapcsolva** beállítást, hogy a webalkalmazás mindig betöltődjön. Ha a webalkalmazás folyamatos WebJobs-alkalmazásokat futtat, kapcsolja be az **Always On (Mindig bekapcsolva)** alkalmazást, vagy a WebJobs nem fut megbízhatóan. További információt a [WebJob folyamatos futtatása című témakörben talál.](webjobs-create.md#CreateContinuous)

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Hogyan szerezhetem be a webalkalmazásom kimenő IP-címét?

A webalkalmazás kimenő IP-címeinek listájának lekérni:

1. Az Azure Portalon a webalkalmazás panelen nyissa meg a **Tulajdonságok** menüt.
2. Kimenő **IP-címek**keresése .

Megjelenik a kimenő IP-címek listája.

Ha tudni szeretné, hogyan kaphatja meg a kimenő IP-címet, ha a webhely egy App Service-környezetben található, olvassa el a Kimenő hálózati címek című [témakört.](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses)

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Hogyan kaphatok egy fenntartott vagy dedikált bejövő IP-címet a webalkalmazásomhoz?

Ha dedikált vagy fenntartott IP-címet szeretne beállítani az Azure-alkalmazás webhelyére irányuló bejövő hívásokhoz, telepítsen és konfiguráljon egy IP-alapú SSL-tanúsítványt.

Vegye figyelembe, hogy a bejövő hívások dedikált vagy fenntartott IP-címének használatához az App Service-csomagnak alapszintű vagy magasabb szintű szolgáltatási csomagban kell lennie.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Exportálhatom az App Service-tanúsítványomat az Azure-on kívüli használatra, például egy máshol üzemeltetett webhelyre? 

Igen, exportálhatja őket az Azure-on kívülre. További információt az [App Service-tanúsítványokra és az egyéni tartományokra vonatkozó gyakori kérdések című témakörben talál.](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Exportálhatom az App Service-tanúsítványomat más Azure-felhőszolgáltatásokkal való használatra?

A portál első osztályú élményt nyújt egy App Service-tanúsítvány üzembe helyezéséhez az Azure Key Vaulton keresztül az App Service-alkalmazásokszámára. Azonban már kapott kéréseket az ügyfelektől, hogy használja ezeket a tanúsítványokat az App Service platformon kívül, például az Azure virtuális gépek. Ha meg szeretné tudni, hogyan hozhat létre helyi PFX-példányt az App Service-tanúsítványból, hogy a tanúsítványt más Azure-erőforrásokkal is használhassa, olvassa el az [App Service-tanúsítvány helyi PFX-példányának létrehozása](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/)című témakört.

További információt az [App Service-tanúsítványokra és az egyéni tartományokra vonatkozó gyakori kérdések című témakörben talál.](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Miért jelenik meg a "Részlegesen sikeres" üzenet, amikor biztonsági másolatot próbálok tenni a webalkalmazásomról?

A biztonsági mentési hiba gyakori oka, hogy az alkalmazás néhány fájlt használ. A használatban lévő fájlok a biztonsági mentés végrehajtása közben zárolva vannak. Ez megakadályozza, hogy ezekről a fájlokról biztonsági másolatot, és azt eredményezheti, hogy a "részlegesen sikeres" állapot. Ezt potenciálisan megakadályozhatja, ha kizárja a fájlokat a biztonsági mentési folyamatból. Választhat, hogy biztonsági másolatot csak mi szükséges. További információ: [Biztonsági mentés a webhely fontos részeiről az Azure-webalkalmazásokkal](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/)című témakörben talál.

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Hogyan távolíthatok el egy fejlécet a HTTP-válaszból?

Ha el szeretné távolítani a fejléceket a HTTP-válaszból, frissítse a webhely web.config fájlját. További információt a [Szabványos kiszolgálófejlécek eltávolítása az Azure-webhelyeken című témakörben talál.](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Az App Service megfelel a PCI Standard 3.0 és 3.1 szabványnak?

Jelenleg az Azure App Service Web Apps szolgáltatása megfelel a PCI Data Security Standard (DSS) 3.0-s verziójának. A PCI DSS 3.1-es verziója szerepel az ütemtervünkben. A legújabb szabvány elfogadásának folyamatban van már.

A PCI DSS 3.1-es verziójának minősítése a Transport Layer Security (TLS) 1.0 letiltását igényli. Jelenleg a TLS 1.0 letiltása a legtöbb App Service-csomag esetében nem lehetséges. Ha azonban az App Service-környezetben, vagy hajlandó áttelepíteni a számítási feladatokat az App Service-környezetbe, akkor nagyobb ellenőrzést a környezetfelett. Ez magában foglalja a TLS 1.0 letiltását az Azure-támogatással való kapcsolatfelvételútján. A közeljövőben azt tervezzük, hogy ezeket a beállításokat elérhetővé tesszük a felhasználók számára.

További információt a [Microsoft Azure App Service webalkalmazás-megfelelőség a PCI Standard 3.0 és 3.1 szabványnak való megfelelés című témakörben talál.](https://support.microsoft.com/help/3124528)

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Hogyan használhatom az átmeneti környezetet és a központi telepítési helyeket?

A Standard és a Prémium App Service-csomagok, amikor telepíti a webalkalmazást az App Service-be, telepítheti egy külön üzembe helyezési hely, nem pedig az alapértelmezett éles tárolóhely. A központi telepítési helyek olyan élő webalkalmazások, amelyek saját gazdagépnévvel rendelkeznek. A webalkalmazás tartalma és konfigurációs elemei két központi telepítési hely között cserélhetők fel, beleértve az éles tárolóhelyet is.

A központi telepítési tárolóhelyek használatáról további információt az [Átmeneti környezet beállítása az App Service szolgáltatásban](deploy-staging-slots.md)című témakörben talál.

## <a name="how-do-i-access-and-review-webjob-logs"></a>Hogyan érhetem el és tekinthetem át a WebJob-naplókat?

A WebJob-naplók áttekintése:

1. Jelentkezzen be a [Kudu webhelyére.](https://*yourwebsitename*.scm.azurewebsites.net)
2. Válassza ki a WebJob elemet.
3. Válassza a **Kimenet váltása** gombot.
4. A kimeneti fájl letöltéséhez kattintson a **Letöltés** hivatkozásra.
5. Az egyes futtatások esetén válassza az **Egyéni meghívás lehetőséget.**
6. Válassza a **Kimenet váltása** gombot.
7. Válassza ki a letöltési linket.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Hibrid kapcsolatokat próbálok használni az SQL Server kiszolgálóval. Miért jelenik meg a "System.OverflowException: Aritmetikai művelet túlcsordulás" üzenet?

Ha hibrid kapcsolatokat használ az SQL Server eléréséhez, a Microsoft .NET frissítése 2016. Ez az üzenet jelenhet meg:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Megoldás:

A kivételt a hibrid kapcsolatkezelővel kapcsolatos, azóta kijavított probléma okozta. A probléma megoldásához [frissítse a hibrid csatlakozáskezelőt.](https://go.microsoft.com/fwlink/?LinkID=841308)

## <a name="how-do-i-add-a-url-rewrite-rule"></a>Hogyan adhatok hozzá URL-újraírási szabályt?

URL-újraírási szabály hozzáadásához hozzon létre egy web.config fájlt a **wwwroot** mappában lévő megfelelő konfigurációs bejegyzésekkel. További információ: [Azure App Services: Understanding URL rewrite](https://blogs.msdn.microsoft.com/madhurabharadwaj/2018/06/01/azure-app-services-understanding-url-re-write/).

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Hogyan szabályozhatom az App Service-be irányuló bejövő forgalmat?

A hely szintjén két lehetősége van az App Service-be irányuló bejövő forgalom szabályozására:

* A dinamikus IP-korlátozások bekapcsolása. A dinamikus IP-korlátozások bekapcsolásáról az [Azure-webhelyek IP- és tartománykorlátozásai](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)című témakörben olvashat.
* Kapcsolja be a modulbiztonság ot. A Modulbiztonság bekapcsolásáról a [ModSecurity webalkalmazás tűzfala az Azure-webhelyeken.](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/)

Az App Service Environment használata esetén használhatja a [Barracuda tűzfalat.](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/)

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Hogyan tudom blokkolni a portokat egy App Service-webalkalmazásban?

Az App Service megosztott bérlői környezetben az infrastruktúra jellege miatt nem lehet blokkolni bizonyos portokat. A 4020-as, 4022-es és 4024-es TCP-portok a Visual Studio távoli hibakereséséhez is nyitva lehetnek.

Az App Service-környezetben teljes mértékben szabályozhatja a bejövő és a kimenő forgalmat. A Hálózati biztonsági csoportok segítségével korlátozhatja vagy letilthatja az egyes portokat. Az App Service-környezetről további információt az [App Service-környezet bemutatása című témakörben talál.](https://azure.microsoft.com/blog/introducing-app-service-environment/)

## <a name="how-do-i-capture-an-f12-trace"></a>Hogyan rögzíthetek egy F12-es nyomkövetést?

Az F12-es nyomkövetés rögzítésére két lehetőség van:

* F12 HTTP-nyomkövetés
* F12 konzol kimenet

### <a name="f12-http-trace"></a>F12 HTTP-nyomkövetés

1. Az Internet Explorerben nyissa meg a webhelyet. Fontos, hogy jelentkezzen be, mielőtt a következő lépéseket. Ellenkező esetben az F12-es nyomkövetési adatok bizalmas bejelentkezési adatokat rögzít.
2. Nyomja le az F12 billentyűt.
3. Ellenőrizze, hogy a **Hálózat** lap ki van-e jelölve, majd jelölje ki a zöld **Lejátszás** gombot.
4. Végezze el a problémát reprodukáló lépéseket.
5. Válassza a piros **Stop** gombot.
6. Válassza a **Mentés** gombot (lemezikon), és mentse a HAR fájlt (az Internet Explorer és a Microsoft Edge programban), *vagy* kattintson a jobb gombbal a HAR fájlra, majd válassza a **Mentés HAR-ként tartalommal** (a Chrome-ban).

### <a name="f12-console-output"></a>F12 konzol kimenet

1. Válassza a **Konzol** lapot.
2. Minden nullánál több elemet tartalmazó lapon válassza a **(Hiba**, **Figyelmeztetés**vagy **Információ**) lapot. Ha a lap nincs kijelölve, a lap ikonja szürke vagy fekete lesz, amikor a kurzort távolítja el tőle.
3. Kattintson a jobb gombbal az ablaktábla üzenetterületén, és válassza az **Összes másolása parancsot.**
4. Illessze be a másolt szöveget egy fájlba, majd mentse a fájlt.

Har fájl megtekintéséhez használhatja a [HAR megjelenítőt.](https://www.softwareishard.com/har/viewer/)

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Miért jelenik meg hibaüzenet, amikor egy ExpressRoute-hoz kapcsolódó virtuális hálózathoz próbálok csatlakoztatni egy App Service-webalkalmazást?

Ha megpróbál csatlakozni egy Azure-webalkalmazáshoz egy Azure ExpressRoute-hoz kapcsolódó virtuális hálózathoz, sikertelen lesz. A következő üzenet jelenik meg: "Az átjáró nem VPN-átjáró."

Jelenleg nem lehet pont-hely VPN-kapcsolattal az ExpressRoute-hoz csatlakoztatott virtuális hálózattal. A pont-hely VPN és ExpressRoute nem létezhet együtt ugyanahhoz a virtuális hálózathoz. További információt az [ExpressRoute és a helyek közötti VPN-kapcsolatok korlátai és korlátai](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations)című témakörben talál.

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Hogyan csatlakoztathatok egy App Service-webalkalmazást egy statikus útválasztási (házirend-alapú) átjáróval rendelkező virtuális hálózathoz?

Jelenleg nem támogatott egy App Service-webalkalmazás statikus útválasztási (házirend-alapú) átjáróval rendelkező virtuális hálózathoz való csatlakoztatása. Ha a célvirtuális hálózat már létezik, az alkalmazáshoz való csatlakozás előtt engedélyeznie kell a pont-hely VPN-t egy dinamikus útválasztási átjáróval. Ha az átjáró statikus útválasztásra van állítva, nem engedélyezheti a pont-hely VPN-t. 

További információt az [Alkalmazás integrálása Azure virtuális hálózattal](web-sites-integrate-with-vnet.md)című témakörben talál.

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>Az App Service-környezetben miért hozhatok létre csak egy App Service-csomagot, annak ellenére, hogy két dolgozó áll rendelkezésre?

A hibatűrés biztosításához az App Service-környezet megköveteli, hogy minden munkavégző készletnek legalább egy további számítási erőforrásra van szüksége. A további számítási erőforrás hoz nem rendelhető hozzá számítási feladathoz.

További információ: [App Service-környezet létrehozása.](environment/app-service-web-how-to-create-an-app-service-environment.md)

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Miért jelennek meg időeltetések, amikor megpróbálok létrehozni egy App Service-környezetet?

Néha egy App Service-környezet létrehozása sikertelen. Ebben az esetben a következő hiba jelenik meg a tevékenységnaplókban:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period."}}
```

A probléma megoldásához győződjön meg arról, hogy az alábbi feltételek egyike sem teljesül:
* Az alhálózat túl kicsi.
* Az alhálózat nem üres.
* Az ExpressRoute megakadályozza az App Service-környezet hálózati kapcsolati követelményeit.
* A hibás hálózati biztonsági csoport megakadályozza az App Service-környezet hálózati kapcsolati követelményeit.
* A kényszerített bújtatás be van kapcsolva.

További információ: [Gyakori problémák az új Azure App Service-környezet üzembe helyezésekor (létrehozásakor).](https://blogs.msdn.microsoft.com/waws/2016/05/13/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase/)

## <a name="why-cant-i-delete-my-app-service-plan"></a>Miért nem tudom törölni az App Service-csomagomat?

Nem törölheti az App Service-csomagot, ha bármely App Service-alkalmazás társítva van az App Service-csomaghoz. Az App Service-csomag törlése előtt távolítsa el az összes kapcsolódó App Service-alkalmazást az App Service-csomagból.

## <a name="how-do-i-schedule-a-webjob"></a>Hogyan ütemezhetek webmunkát?

Ütemezett webfeladatot cron kifejezésekkel hozhat létre:

1. Hozzon létre egy settings.job fájlt.
2. Ebben a JSON-fájlban vegyen fel egy ütemezési tulajdonságot cron kifejezés használatával: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Az ütemezett WebJobs programról az [Ütemezett webfeladat létrehozása Cron kifejezés használatával](webjobs-create.md#CreateScheduledCRON)című témakörben talál további információt.

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Hogyan végezhetek behatolási tesztelést az App Service-alkalmazásomon?

A behatolási tesztelés elvégzéséhez [nyújtson be kérelmet.](https://portal.msrc.microsoft.com/engage/pentest)

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Hogyan állíthatok be egyéni tartománynevet egy Traffic Managert használó App Service-webalkalmazáshoz?

Ha meg szeretné tudni, hogyan használhat egyéni tartománynevet egy Olyan App Service-alkalmazással, amely az Azure Traffic Managert használja a terheléselosztáshoz, olvassa [el az Egyéni tartománynév konfigurálása egy Azure-webalkalmazáshoz a Traffic Manager segítségével című témakört.](configure-domain-traffic-manager.md)

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Az App Service-tanúsítványom csalás miatt van megjelölve. Hogyan oldhatom meg ezt?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az App Service-tanúsítványok vásárlásának tartományellenőrzése során a következő üzenet jelenhet meg:

"A tanúsítványát megjelölték lehetséges csalás miatt. A kérelem jelenleg felülvizsgálat alatt áll. Ha a tanúsítvány 24 órán belül nem válik használhatóvá, forduljon az Azure-támogatáshoz."

Ahogy az üzenet is jelzi, ez a csalás-ellenőrzési folyamat akár 24 órát is igénybe vehet. Ez alatt az idő alatt továbbra is látni fogja az üzenetet.

Ha az App Service-tanúsítvány 24 óra elteltével is megjelenik, futtassa a következő PowerShell-parancsfájlt. A parancsfájl közvetlenül a [tanúsítványszolgáltatóhoz](https://www.godaddy.com/) lép kapcsolatba a probléma megoldása érdekében.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Hogyan működik a hitelesítés és az engedélyezés az App Service-ben?

Az App Service-ben a hitelesítéshez és az engedélyezéshez szükséges részletes dokumentációt a különböző azonosító szolgáltatók bejelentkezéseiről szóló dokumentumokban találja:
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Microsoft-fiók](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Hogyan irányíthatom át az alapértelmezett *.azurewebsites.net tartományt az Azure webapp egyéni tartományára?

Amikor új webhelyet hoz létre az Azure-beli webalkalmazások használatával, a webhelyhez *egy*alapértelmezett .azurewebsites.net tartomány van hozzárendelve. Ha egyéni állomásnevet ad meg a webhelyhez, és nem szeretné, hogy a felhasználók hozzáférhessenek az alapértelmezett *.azurewebsites.net tartományhoz, átirányíthatja az alapértelmezett URL-címet. Ha meg szeretné tudni, hogyan irányíthatja át a webhely alapértelmezett tartományából származó összes forgalmat az egyéni tartományba, olvassa el [az Alapértelmezett tartomány átirányítása az egyéni tartományra az Azure-webalkalmazásokban című témakört.](https://zainrizvi.io/blog/block-default-azure-websites-domain/)

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Hogyan állapítható meg, hogy a .NET verzió melyik verziója van telepítve az App Service szolgáltatásban?

A Microsoft .NET alkalmazásszolgáltatásban telepített verziójának megkeresésének leggyorsabb módja a Kudu konzol használata. A Kudu konzolt a portálról vagy az App Service-alkalmazás URL-címének használatával érheti el. Részletes útmutatást [az App Service telepített .NET verziójának meghatározása című témakörben talál.](https://blogs.msdn.microsoft.com/waws/2016/11/02/how-to-determine-the-installed-net-version-in-azure-app-services/)

## <a name="why-isnt-autoscale-working-as-expected"></a>Miért nem az automatikus skálázás működik a várt módon?

Ha az Azure automatikus skálázása nem méretezett, vagy skálázva a webalkalmazás-példány, ahogy azt várta, előfordulhat, hogy fut egy forgatókönyv, amelyben szándékosan úgy dönt, hogy nem méretezhető, hogy elkerüljék a végtelen hurok miatt "csapkodás.". Ez általában akkor történik, ha nincs megfelelő árrés a horizontális felskálázási és a horizontális felskálázási küszöbértékek között. Ha meg szeretné tudni, hogyan kerülheti el a "csapkodást", és hogyan olvashat az automatikus skálázással kapcsolatos gyakorlati tanácsokról, olvassa el [az Automatikus skálázás ajánlott eljárások című témakört.](../azure-monitor/platform/autoscale-best-practices.md#autoscale-best-practices)

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Miért van az automatikus skálázás néha csak részben méretezhető?

Az automatikus skálázás akkor aktiválódik, ha a metrikák meghaladják az előre beállított határokat. Előfordulhat, hogy a kapacitás csak részben van feltöltve a várthoz képest. Ez akkor fordulhat elő, ha a kívánt példányok száma nem érhető el. Ebben az esetben az automatikus skálázás részben kitölti a rendelkezésre álló példányok száma. Automatikus skálázás, majd futtatja az egyensúly helyreállítása logika, hogy minél több kapacitást. A fennmaradó példányokat foglalja le. Ne feledje, hogy ez eltarthat néhány percig.

Ha néhány perc múlva nem látja a példányok várható számát, annak az lehet az oka, hogy a részleges újratöltés elegendő volt ahhoz, hogy a metrikák at a határokon belülre kerüljenek. Vagy előfordulhat, hogy az automatikus skálázás kicsinyített, mert elérte az alacsonyabb metrikák határát.

Ha a feltételek egyike sem érvényes, és a probléma továbbra is fennáll, nyújtson be támogatási kérelmet.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Hogyan kapcsolhatom be a HTTP-tömörítést a tartalmamhoz?

Ha statikus és dinamikus tartalomtípusok esetén is be szeretné kapcsolni a tömörítést, adja hozzá a következő kódot az alkalmazásszintű web.config fájlhoz:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

Megadhatja a tömörítni kívánt dinamikus és statikus MIME-típusokat is. További információkért tekintse meg a választ a fórum kérdésre a [httpCompression beállítások egy egyszerű Azure-webhely .](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview)

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Hogyan telepíthetek át egy helyszíni környezetből az App Service-be?

A webhelyek Windows- és Linux-webkiszolgálókról az App Service-be való áttelepítéséhez használhatja az Azure App Service Migration Assistant szolgáltatást. Az áttelepítési eszköz szükség szerint webes alkalmazásokat és adatbázisokat hoz létre az Azure-ban, majd közzéteszi a tartalmat. További információ: [Azure App Service Migration Assistant](https://appmigration.microsoft.com/).
