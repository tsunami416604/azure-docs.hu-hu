---
title: "Konfiguráció – gyakori kérdések az Azure web Apps |} Microsoft Docs"
description: "Konfigurációs és kezelésének számos gyakran feltett kérdésekre adott válaszok beolvasása az Azure App Service Web Apps szolgáltatása."
services: app-service\web
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: genli
ms.openlocfilehash: cc17196603a5bdcd7f880c3650512846fa0facef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Konfigurálása és kezelése – gyakori kérdések az Azure Web Apps

Ez a cikk rendelkezik a konfigurációs és kezelésének számos gyakran ismételt kérdések (GYIK) választ a [az Azure App Service Web Apps szolgáltatásának](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Vannak-e I kell ügyelnie, ha szeretnék lehetőséget az App Service-erőforrások áthelyezése korlátai?

Ha azt tervezi, az App Service-erőforrások áthelyezése egy új erőforráscsoportba vagy előfizetésbe, van néhány korlátozást érdemes figyelembe vennie. További információkért lásd: [App Service korlátozásai](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations).

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>A webalkalmazás számára egy egyéni tartománynevet használata?

A dokumentumból megismerheti az Azure web app alkalmazásban egy egyéni tartománynevet használatával kapcsolatban lásd a hét perces videót [egy egyéni tartománynév hozzáadása](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). A videó kaphat az egyéni tartománynév hozzáadása kínál. Bemutatja, hogyan lehet a saját URL-cím helyett használja a *. azurewebsites.net az App Service webalkalmazás URL-cím. Részletes is látható [hogyan képezheti egy egyéni tartománynevet](app-service-web-tutorial-custom-domain.md).


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Hogyan vásárolhat egy új egyéni tartományt a webalkalmazás?

Beszerzési és az App Service webalkalmazás az egyéni tartománynév beállítása, lásd: [vásárolnia, és állítson be egy egyéni tartománynevet az App Service](custom-dns-web-site-buydomains-web-app.md).


## <a name="how-do-i-upload-and-configure-an-existing-ssl-certificate-for-my-web-app"></a>Hogyan feltöltése és konfigurálása a létező SSL-tanúsítvány a webalkalmazás?

Töltse fel, és állítson be egy meglévő egyéni SSL-tanúsítvány, lásd: [egy meglévő egyéni SSL-tanúsítvány kötését az Azure-webalkalmazás](app-service-web-tutorial-custom-ssl.md#upload).


## <a name="how-do-i-purchase-and-configure-a-new-ssl-certificate-in-azure-for-my-web-app"></a>Hogyan vásárolhat és egy új SSL-tanúsítvány konfigurálása az Azure-ban a webalkalmazás?

Megtudhatja, hogyan vásárolhat, és állítsa be az App Service webalkalmazás SSL-tanúsítvány, lásd: [SSL-tanúsítvány hozzáadása az App Service alkalmazáshoz](web-sites-purchase-ssl-web-site.md).


## <a name="how-do-i-move-application-insights-resources"></a>Hogyan helyezze át az Application Insights-erőforrások?

Azure Application Insights jelenleg nem támogatja a műveletet. Ha az eredeti erőforráscsoport Application Insights-erőforrást tartalmaz, nem helyezhető át, hogy az erőforrás. Ha az Application Insights-erőforrást próbál áthelyezni egy App Service-alkalmazást, a teljes áthelyezése művelet sikertelen lesz. Azonban Application insights szolgáltatással és az App Service-csomag nem ugyanahhoz az erőforráscsoporthoz tartozik, az alkalmazás az alkalmazás megfelelően működik-e lennie kell.

További információkért lásd: [App Service korlátozásai](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations).

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Ahol I talál útmutatást ellenőrzőlista és további erőforrás kapcsolatos műveletek áthelyezése?

[App Service korlátozásai](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations) bemutatja, hogyan kell helyeznie az erőforrásokat, vagy egy új előfizetést, vagy egy új erőforráscsoportot ugyanahhoz az előfizetéshez. Az erőforrás-áthelyezés ellenőrzőlista vonatkozó információt, ismerje meg, mely szolgáltatásokat támogatja az áthelyezés műveletet, és további tudnivalók az App Service korlátozásai és egyéb témaköröket.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Hogyan állíthatom kiszolgáló időzónáját a webalkalmazás?

A kiszolgáló időzónáját a webalkalmazás beállítása:

1. Az App Service-előfizetéshez az Azure portálon lépjen a **Alkalmazásbeállítások** menü.
2. A **Alkalmazásbeállítások**, adja hozzá ezt a beállítást:
    * Kulcs = WEBSITE_TIME_ZONE
    * Érték = *kívánt időzóna*
3. Kattintson a **Mentés** gombra.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Miért hajtsa végre a folyamatos webjobs-feladatok néha nem?

Alapértelmezés szerint a webalkalmazások a memóriából, ha a beállított időn üresjáratban. Ez lehetővé teszi, hogy a rendszer, ezért az erőforrások megőrzése. A Basic és Standard csomagokban bekapcsolása a **mindig a** mindig tartsa a webalkalmazás beállítása betöltve. A webalkalmazás fut a folyamatos webjobs-feladatok, be kell kapcsolnia **mindig a**, vagy a webjobs-feladatok nem futnak megbízhatóan. További információkért lásd: [folyamatosan futó webjobs-feladat létrehozása](web-sites-create-web-jobs.md#CreateContinuous).

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Hogyan szerezhetek a kimenő IP-címet a webalkalmazás?

A webalkalmazás kimenő IP-címek listájának lekérdezése:

1. A webalkalmazás panelen az Azure-portálon lépjen a **tulajdonságok** menü.
2. Keresse meg **kimenő IP-címek**.

A kimenő IP-címek listája jelenik meg.

Ha a webhely az App Service Environment-környezet a powerapps segítségével, a kimenő IP-cím beszerzéséről lásd: [kimenő hálózati címek](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Hogyan szerezhetek fenntartott vagy dedikált bejövő IP-címnek a webalkalmazás?

Bejövő hívások Azure alkalmazás webhelye dedikált vagy fenntartott IP-cím beállításához telepítse, és konfigurálja az IP-alapú SSL-tanúsítvány.

Vegye figyelembe, hogy bejövő egy dedikált vagy fenntartott IP-cím használatához az App Service-csomag csak az olyan alapvető vagy magasabb service-csomag.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Exportálhatja az App Service alkalmazáshoz használandó tanúsítványt kívül Azure, többek között máshol üzemeltetett webhely? 

App Service-tanúsítványok minősülnek Azure-erőforrások. Ezek nem feltétlenül kívül az Azure-szolgáltatások használatához. Nem lehet exportálni kívül Azure használhatják. További információkért lásd: [gyakori kérdések az App Service-tanúsítványok és az egyéni tartományok](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Exportálhatja a my App Service tanúsítvány más Azure felhőszolgáltatások használata?

A portál első osztályú élményt nyújt az Azure Key Vault keresztül egy App Service-tanúsítvány telepítése az App Service-alkalmazásokhoz. Azonban azt kell lett fogadása kérelmek ügyfelek számára ezek a tanúsítványok kívül az App Service platformra, például az Azure virtuális gépek használata. Az App Service tanúsítvány PFX helyi másolat létrehozása, hogy használhassa a tanúsítványt más Azure-erőforrásokkal folytatott további tudnivalókért lásd: [PFX helyi másolatot készít egy App Service tanúsítvány](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).

További információkért lásd: [gyakori kérdések az App Service-tanúsítványok és az egyéni tartományok](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Miért látom az "Részben sikeresen befejeződött" üzenet, amikor megpróbálom biztonsági másolatot készíteni a webalkalmazást?

A közös biztonsági mentési hiba oka, hogy néhány fájl van-e az alkalmazás használja. Használatban lévő fájlok zárolva van, a biztonsági mentés végrehajtása során. Ez megakadályozza, hogy ezek a fájlok biztonsági mentése folyamatban, és a "Részben sikeres" állapota esetén előfordulhat. Potenciálisan megakadályozhatja Ez kizárja a fájlokat a biztonsági mentési folyamat során előforduló. Ha szeretné, készítsen biztonsági másolatot csak mire van szükség. További információkért lásd: [biztonsági mentése csak a webhelyet a Azure web Apps alkalmazások fontos részei](http://www.zainrizvi.io/2015/06/05/creating-partial-backups-of-your-site-with-azure-web-apps/).

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Hogyan fejléc eltávolítása a HTTP-válasz?

Távolítsa el a fejléceket a HTTP-válasz, módosítsa a webhely web.config fájlt. További információkért lásd: [távolítsa el az Azure websitesban általános jogú kiszolgálói fejlécek](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Megfelel az App Service PCI szabvány 3.0 és 3.1?

Az Azure App Service Web Apps szolgáltatása jelenleg PCI adatok biztonsági szabvány (DSS) verzióját az 1. szintű 3.0-s. A terv PCI DSS 3.1-es verziója van. Tervezési már folyamatban van a legújabb standard bevezetése folytatódik hogyan.

PCI DSS 3.1-es verziója szükséges Transport Layer Security (TLS) 1.0 letiltása. A TLS 1.0 letiltása jelenleg nem a beállítás a legtöbb App Service-csomagokról. Azonban ha használja az App Service Environment-környezet, vagy a rendszer a számítási feladatok át App Service Environment-környezet, hatékonyabb vezérlését a környezet kaphat. Ez magában foglalja a TLS 1.0 letiltása lépjen kapcsolatba az Azure támogatási szolgálatához. A közeljövőben tervezzük ezeket a beállításokat elérhetővé a felhasználók számára.

További információkért lásd: [Microsoft Azure App Service web app és való megfelelés PCI szabvány 3.0 3.1](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>A tesztelési környezet és a központi telepítés tárolóhelyek használata?

A Standard és prémium szintű App Service csomagokban a webalkalmazás az App Service telepítésekor telepítene egy külön üzembe helyezési pont helyett az alapértelmezett éles tárolóhelyre. Üzembe helyezési olyan élő webes alkalmazások, amelyek a saját állomás neve. Webes alkalmazás tartalom és a konfigurációs elemek lecserélhető között két üzembe helyezési, beleértve az éles webalkalmazásra.

Üzembe helyezési használatával kapcsolatos további információkért lásd: [az App Service fejlesztői környezet beállítása](web-sites-staged-publishing.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Hogyan eléréséhez és tekintse át a webjobs-feladat naplókat?

A webjobs-feladat naplók áttekintéséhez:

1. Jelentkezzen be a [Kudu webhely](https://*yourwebsitename*.scm.azurewebsites.net).
2. Válassza ki a webjobs-feladat.
3. Válassza ki a **váltása kimeneti** gombra.
4. A kimeneti fájl letöltéséhez, jelölje be a **letöltése** hivatkozásra.
5. Egyes futtatja, válasszon **egyes meghívása**.
6. Válassza ki a **váltása kimeneti** gombra.
7. Válassza ki a letöltési hivatkozás.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Kísérlet az SQL Server használhatnak hibrid kapcsolatokat. Miért látom azt az üzenet "System.OverflowException: aritmetikai művelet túlcsordulást okozott"?

SQL-kiszolgáló eléréséhez használhatnak hibrid kapcsolatokat, a Microsoft .NET-frissítések 2016. május 10., okozhat kapcsolatok sikertelen lesz. Előfordulhat, hogy ez az üzenet jelenik meg:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Megoldás:

Dolgozunk a probléma megoldásához Hybrid Connection Manager frissítése. Lehetséges megoldások, lásd: [hibrid kapcsolatok hiba történt a következő SQL Server: System.OverflowException: aritmetikai művelet túlcsordulást okozott](https://blogs.msdn.microsoft.com/waws/2016/05/17/hybrid-connection-error-with-sql-server-system-overflowexception-arithmetic-operation-resulted-in-an-overflow/).

## <a name="how-do-i-add-or-edit-a-url-rewrite-rule"></a>Hogyan hozzáadása vagy egy URL-cím átdolgozás szabály szerkesztése?

Hozzáadhat vagy szerkeszthet egy URL-újraíró a szabályt:

1. Internet Information Services (IIS) kezelője beállítása, hogy az App Service webalkalmazásba csatlakozik. Az IIS-kezelő csatlakoztatása az App Service kapcsolatban [távoli felügyeletére, Azure-webhelyek IIS-kezelő használatával](https://azure.microsoft.com/blog/remote-administration-of-windows-azure-websites-using-iis-manager/).
2. Az IIS-kezelőben szabály felvétele vagy szerkesztése egy URL-cím átdolgozás. Az egy URL-cím átdolgozás szabály felvétele vagy szerkesztése, lásd: [létrehozás átdolgozás szabályok az URL-újraíró modul](https://www.iis.net/learn/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module).

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Hogyan szabályozza a bejövő forgalom az App Service?

A hely szintjén a bejövő forgalom az App Service vezérlése két lehetőség közül választhat:

* Kapcsolja be a dinamikus IP-korlátozásokat. Dinamikus IP-címkorlátozások bekapcsolása kapcsolatban [IP-cím és tartomány korlátozása az Azure-webhelyek](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).
* Kapcsolja be a modul biztonsági. A modul biztonsági bekapcsolása további tudnivalókért lásd: [ModSecurity webalkalmazási tűzfal Azure-webhelyeken futó](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/).

App Service Environment-környezet használatakor használhatja [Barracuda tűzfal](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Hogyan tiltsa le a portokat az App Service web app alkalmazásban?

Az App Service megosztott bérlői környezetben nincs lehetőség az infrastruktúra jellege miatt adott portok blokkolása. 4016, 4018 és 4020 TCP-portot is nyissa meg a Visual Studio távoli hibakereséshez lehet.

App Service Environment-környezet, a bejövő és kimenő forgalom teljes hozzáféréssel rendelkeznek. Hálózati biztonsági csoportok segítségével korlátozhatja, vagy adott portok blokkolása. App Service Environment-környezet kapcsolatos további információkért lásd: [App Service Environment bemutatása](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Hogyan rögzítése a az F12 nyomkövetési?

Egy F12 nyomkövetés rögzítésének két lehetőség közül választhat:

* F12 HTTP nyomkövetési
* F12 billentyű lenyomása a konzol kimeneti

### <a name="f12-http-trace"></a>F12 HTTP nyomkövetési

1. Az Internet Explorerben nyissa meg a webhelyet. Fontos a következő lépések jelentkezzenek be. Ellenkező esetben az F12 nyomkövetési rögzíti a bizalmas bejelentkezési adatokat.
2. Nyomja le az F12.
3. Ellenőrizze, hogy a **hálózati** lap van kiválasztva, majd válassza ki a zöld **lejátszása** gombra.
4. A lépések, hogy Reprodukálja a hibát.
5. Válassza ki a piros **leállítása** gombra.
6. Válassza ki a **mentése** (lemez ikon) gombra, és mentse a HAR fájlt (az Internet Explorer és a peremhálózati) *vagy* kattintson a jobb gombbal a HAR fájlt, majd válassza ki **HAR elmentse tartalmú** (a Chrome-ban).

### <a name="f12-console-output"></a>F12 billentyű lenyomása a konzol kimeneti

1. Válassza ki a **konzol** fülre.
2. Az egyes lapokon, amely nagyobb, mint nulla elemet tartalmaz, válassza ki a lapon (**hiba**, **figyelmeztetés**, vagy **információk**). Ha a lapon nincs bejelölve, a tab ikon esetén szürke vagy fekete a kurzorral el azt.
3. A panelen üzenet területén kattintson a jobb gombbal, majd válassza ki **másolhatja az összes**.
4. Illessze be a másolt szöveget a fájlba, és mentse a fájlt.

Egy HAR fájl megtekintéséhez használja a [HAR viewer](http://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Miért kapok hiba való csatlakozáshoz egy App Service web app ExpressRoute csatlakoztatott virtuális hálózathoz?

Ha Azure-webalkalmazás virtuális hálózathoz csatlakozzon, amely csatlakozik-e Azure ExpressRoute próbál, nem. A következő üzenet jelenik meg: "Átjáró nincs VPN-átjáró."

Jelenleg nem lehet pont-pont VPN-kapcsolatok ExpressRoute csatlakoztatott virtuális hálózathoz. A pont-pont VPN- és ExpressRoute nem lehet ugyanazt a virtuális hálózatot. További információkért lásd: [ExpressRoute- és telephelyek közötti VPN kapcsolatok korlátai és korlátozásai](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations).

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Hogyan kapcsolódnak az App Service web app virtuális hálózathoz, amelyen egy statikus útválasztás (házirend-alapú) átjáró?

Az App Service webes alkalmazás csatlakoztatása egy virtuális hálózathoz, amelyen egy statikus útválasztás (házirend-alapú) átjáró jelenleg nem támogatott. Ha a cél virtuális hálózat már létezik, pont-pont VPN engedélyezve van, egy dinamikus útválasztási átjáró előtt csatlakozhat egy alkalmazáshoz kell rendelkeznie. Ha az átjáró statikus útválasztás, pont-pont típusú VPN nem engedélyezhető. 

További információkért lásd: [alkalmazások integrálása az Azure virtuális hálózat](web-sites-integrate-with-vnet.md#getting-started).

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>Az App Service Environment-környezet a miért hozható létre csak egy App Service-csomag, annak ellenére, hogy van két munkavállalók elérhető?

Biztosítja a hibatűrést, a App Service Environment-környezet megköveteli, hogy minden feldolgozókészletek kell-e legalább egy további számítási erőforrás. A további számítási erőforrás nem rendelhető hozzá a munkaterhelés.

További információkért lásd: [egy App Service Environment-környezet létrehozása](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Miért látom időtúllépések, az App Service-környezetek létrehozása közben?

Egyes esetekben az App Service Environment-környezet létrehozása sikertelen. Ebben az esetben tekintse meg a következő hiba a tevékenység naplófájlokban:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period.”}}
```

A probléma megoldásához, győződjön meg arról, hogy a következő feltételek egyike teljesül:
* Az alhálózat nem elég nagy.
* Az alhálózat nem üres.
* ExpressRoute megakadályozza, hogy egy App Service Environment-környezet hálózati kapcsolati követelményeinek.
* Hibás hálózati biztonsági csoport megakadályozza, hogy egy App Service Environment-környezet hálózati kapcsolati követelményeinek.
* A kényszerített bújtatás be van kapcsolva.

További információkért lásd: [gyakori problémák (létrehozás) telepítésekor egy új Azure App Service Environment-környezet](https://blogs.msdn.microsoft.com/waws/2016/05/13/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase/).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Miért nem lehet törölni az App Service-csomag?

Az App Service-csomag nem törölhető, ha az App Service alkalmazások hozzárendelve az App Service-csomag. Az App Service-csomag törlése előtt távolítsa el minden társított App Service-alkalmazás az App Service-csomag.

## <a name="how-do-i-schedule-a-webjob"></a>Hogyan ütemezni a webjobs-feladat?

Létrehozhat egy ütemezett webjobs-feladat Cron-kifejezés használatával:

1. Hozzon létre egy settings.job fájlt.
2. A JSON-fájlt tartalmaznak egy ütemezés tulajdonság Cron-kifejezés használatával: 
    ```
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Az ütemezett webjobs-feladatok kapcsolatos további információkért lásd: [hozzon létre egy ütemezett webjobs-feladat Cron-kifejezés](web-sites-create-web-jobs.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Hogyan végezhető el az App Service-alkalmazás tesztelése behatolás?

A tesztek a behatolást vagy a biztonság, végrehajtása [igényelnie](https://security-forms.azure.com/penetration-testing/terms).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Hogyan állítson be egy egyéni tartománynevet, az App Service webalkalmazás Traffic Manager használó?

Egyéni tartománynév használata egy App Service-alkalmazás által használt Azure Traffic Manager terheléselosztás további tudnivalókért lásd: [egy egyéni tartománynév beállítása az Azure-webalkalmazás a Traffic Managerrel](web-sites-traffic-manager-custom-domain-name.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Az App Service tanúsítvány csalás van megjelölve. Hogyan lehet elhárítani ezt?

Egy App Service-tanúsítványa beszerzési tartomány ellenőrzése során a következő üzenetet láthatja:

"A tanúsítvány rendelkezik már meg van jelölve a lehetséges csalásról. A kérelem jelenleg felülvizsgálat alatt. Ha a tanúsítvány nem lesz használható 24 órán belül, forduljon Azure támogatási szolgálatához."

Az üzenet azt jelzi, az csalás ellenőrzési folyamat akár 24 óráig is eltarthat. Ebben az időszakban fogja folytatni az üzenet jelenik meg.

Ha az App Service tanúsítvány továbbra is jelenjen meg ez az üzenet 24 óra múlva, futtassa a következő PowerShell-parancsfájlt. A parancsfájl kapcsolatba lép a [tanúsítványszolgáltató](https://www.godaddy.com/) közvetlenül a probléma megoldása érdekében.

```
Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzureRmResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Hitelesítési és engedélyezési működése az App Service-ben

A részletes dokumentációt a hitelesítéshez és engedélyezéshez az App Service szolgáltatásban lásd: docs különböző határozza meg szolgáltatóhoz bejelentkezést:
* [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md)
* [Facebook](app-service-mobile-how-to-configure-facebook-authentication.md)
* [Google](app-service-mobile-how-to-configure-google-authentication.md)
* [Microsoft-fiók](app-service-mobile-how-to-configure-microsoft-authentication.md)
* [Twitter](app-service-mobile-how-to-configure-twitter-authentication.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Hogyan átirányítja az alapértelmezett *. azurewebsites.net tartományban, a saját Azure web apphoz egyéni tartományhoz?

Amikor egy új webhelyet hoz létre az alapértelmezett Azure Web Apps használatával *sitename*. azurewebsites.net tartomány nincs hozzárendelve a helyhez. Ha egy egyéni nevet adhat hozzá a helyhez, és nem szeretné a felhasználók számára érhetők el az alapértelmezett *. azurewebsites.net tartományban, irányíthatja át az alapértelmezett URL-cím. Az egyéni tartomány a webhely alapértelmezett tartomány összes forgalom átirányítására, lásd: [az alapértelmezett tartomány átirányítása Azure-webalkalmazásokban egyéni tartományhoz](http://www.zainrizvi.io/2016/04/07/block-default-azure-websites-domain/).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Hogyan állapítható meg, melyik verziója a .NET a verziója telepítve van az App Service-ben?

A leggyorsabban található a Microsoft .NET az App Service-ben telepített verziója van, a Kudu konzol használatával. A Kudu konzol a portálon vagy az App Service-alkalmazás URL-címe használatával érheti el. Részletes útmutatásért lásd: [a telepített .NET-verzió az App Service](https://blogs.msdn.microsoft.com/waws/2016/11/02/how-to-determine-the-installed-net-version-in-azure-app-services/).

## <a name="why-isnt-autoscale-working-as-expected"></a>Automatikus skálázás miért nem működik az elvárásoknak megfelelően?

Ha Azure automatikus skálázás nem méretezhető a vagy horizontálisan a web app-példány megfelelő, akkor előfordulhat, hogy fut az olyan forgatókönyvekben, ahol azt szándékosan úgy, hogy nem méretezhető elkerülése érdekében ki végtelen ciklus miatt "" ugrál". Ez általában akkor fordul elő, ha nem áll rendelkezésre megfelelő a kibővített és a skála a küszöbértékek között. "" Ugrál "" elkerülése érdekében, valamint hogy olvassa automatikus skálázás gyakorlati tanácsokat, lásd: [automatikus skálázás gyakorlati tanácsok](../monitoring-and-diagnostics/insights-autoscale-best-practices.md#autoscale-best-practices).

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Miért nem automatikus skálázás néha méretezése csak részben?

Automatikus skálázás lesz kiváltva, ha a metrikák túllépnek előre konfigurált határokat. Egyes esetekben bizonyára észrevette, hogy, hogy a kapacitás csak részben töltve várttól képest. Ez akkor fordulhat elő, ha azt szeretné, példányainak száma nem áll rendelkezésre. Adott esetben automatikus skálázás részben tölti be a példányok számát. Automatikus skálázás majd futtatja a egyensúlyozza ki újra logika további kapacitás eléréséhez. A fennmaradó példányok oszt ki. Vegye figyelembe, hogy ez eltarthat néhány percig.

Ha nem látja a példányok várt száma néhány perc múlva, történhet a részleges Újratöltés volt elegendő keretein belül a metrikák érdekében. Vagy az automatikus skálázás előfordulhat, hogy rendelkezik méretezhető, mert elérte a metrikák alsó határ.

Ha ezek a feltételek közül egyik sem vonatkoznak, és a probléma továbbra is fennáll, a támogatási kérelem elküldéséhez.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Hogyan bekapcsolni a HTTP-tömörítés a tartalom?

Tömörítés is biztosít a statikus és dinamikus tartalom bekapcsolásához adja hozzá a következő kódot az alkalmazás szintű web.config fájlban:

```
<system.webServer>
<urlCompression doStaticCompression="true" doDynamicCompression="true" />
< /system.webServer>
```

A megadott dinamikus és statikus MIME-típusok tömörítendő is megadhatja. További információkért lásd: a válasz egy fórum kérdést [httpCompression beállítások egy egyszerű Azure webhelyen](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview).

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Hogyan tudom át egy a helyszíni környezetből az App Service?

A Windows és Linux webkiszolgálók az App Service helyek áttelepítéséhez Azure App Service áttelepítési Segéd használhatja. Az áttelepítési eszköz hoz létre webes alkalmazásokat és adatbázisokat Azure igény szerint, és majd közzéteszi a tartalmat. További információkért lásd: [Azure App Service áttelepítési Segéd](https://www.movemetothecloud.net/).
