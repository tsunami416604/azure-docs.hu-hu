---
title: Konfiguráció – gyakori kérdések – az Azure App Service |} A Microsoft Docs
description: Konfigurálással és felügyelettel kapcsolatos gyakori kérdésekre adott válaszokat lekérése az Azure App Service Web Apps funkcióját.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: c2758084331a487d65235b70bed803a74d0a34b5
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322533"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Konfigurálás és felügyelet – gyakori kérdések az Azure Web Apps

Ez a cikk rendelkezik (GYIK) kapcsolatos gyakori kérdésekre adott válaszok a konfigurálással és felügyelettel kapcsolatos a [Azure App Service Web Apps funkciójával](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Milyen korlátozások I kell ügyelnie, ha el szeretne áthelyezni az App Service-erőforrások?

Ha azt tervezi, az App Service-erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe, néhány korlátozás érdemes figyelembe vennie. További információkért lásd: [App Service korlátai](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations).

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Hogyan használhatom a saját webes alkalmazás egy egyéni tartománynevet?

Egyéni tartománynév az Azure web app alkalmazásban való használatával kapcsolatos gyakori kérdéseket talál a 7 perces videó [egyéni tartománynév hozzáadása](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). A videó kínál egy egyéni tartománynév hozzáadása bemutatóját. Bemutatja, hogyan lehet a saját URL-cím helyett használja a *. azurewebsites.net az App Service web app URL-CÍMÉT. Azt is láthatja részletes [hogyan képezhet le egyéni tartománynevet](app-service-web-tutorial-custom-domain.md).


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Hogyan vásárolhatok új egyéni tartományt a webalkalmazás?

Ismerje meg, hogyan vásárolhat és az App Service webalkalmazás egy egyéni tartomány beállítása, lásd: [vásárlása és a egy egyéni tartománynév konfigurálása az App Service](custom-dns-web-site-buydomains-web-app.md).


## <a name="how-do-i-upload-and-configure-an-existing-ssl-certificate-for-my-web-app"></a>Hogyan töltse fel és a webalkalmazás konfigurálása meglévő SSL-tanúsítvány?

Ismerje meg, hogyan töltse fel, és állítsa be a meglévő egyéni SSL-tanúsítvány, lásd: [meglévő egyéni SSL-tanúsítvány kötése az Azure-webalkalmazás](app-service-web-tutorial-custom-ssl.md#upload).


## <a name="how-do-i-purchase-and-configure-a-new-ssl-certificate-in-azure-for-my-web-app"></a>Hogyan vásárolhat és a webalkalmazás konfigurálása új SSL-tanúsítványt az Azure-ban?

Ismerje meg, hogyan vásárolhat, és állítsa be az App Service webalkalmazás SSL-tanúsítvány, lásd: [SSL-tanúsítvány hozzáadása az App Service-alkalmazást](web-sites-purchase-ssl-web-site.md).


## <a name="how-do-i-move-application-insights-resources"></a>Hogyan helyezhetek át Application Insights-erőforrások?

Az Azure Application Insights jelenleg nem támogatja az áthelyezési művelet. Ha az eredeti erőforráscsoportot egy Application Insights-erőforrást tartalmaz, nem helyezhetők át ennek az erőforrásnak. Ha az Application Insights-erőforrást is áthelyezése egy App Service-alkalmazást észlel, a teljes áthelyezése művelet sikertelen lesz. Azonban az Application Insights és App Service-csomag nem kell lenniük az ugyanabban az erőforráscsoportban, az alkalmazás az alkalmazás megfelelő működéséhez.

További információkért lásd: [App Service korlátai](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations).

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Hol helyezhetem található útmutatást ellenőrzőlista és ismerje meg, több erőforrással kapcsolatos műveleteket helyez át?

[Az App Service korlátai](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations) bemutatja, hogyan kell helyeznie az erőforrásokat, vagy egy új előfizetést vagy egy új erőforráscsoport ugyanabban az előfizetésben. Az erőforrás-áthelyezési ellenőrzőlista adatainak beolvasása, megtudhatja, mely szolgáltatások támogatják az áthelyezési művelet, és további információk az App Service korlátai és egyéb témakörök.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Hogyan állítsa be a kiszolgáló időzónája a webalkalmazás?

A kiszolgáló időzónája a webalkalmazás beállítása:

1. Az Azure Portalon, az App Service-előfizetésében, nyissa meg a **Alkalmazásbeállítások** menü.
2. A **Alkalmazásbeállítások**, adja hozzá ezt a beállítást:
    * Kulcs = WEBSITE_TIME_ZONE
    * Érték = *kívánt időzóna*
3. Kattintson a **Mentés** gombra.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Miért hajtsa végre a folyamatos webjobs-feladatok néha nem?

Alapértelmezés szerint a webalkalmazások távolítva a memóriából ha azok egy megadott ideig inaktív. Ez lehetővé teszi, hogy a rendszer erőforrások megőrzése. Az alapszintű és standard szintű csomagokban, bekapcsolhatja a **Always On** folyamatosan beállítása, hogy a webalkalmazás betöltése. A webalkalmazás folyamatos webjobs-feladatok fut, be kell kapcsolnia **Always On**, vagy a webjobs-feladatok nem lehet megbízhatóan fusson. További információkért lásd: [hozzon létre egy folyamatosan futó webjobs-feladat](web-sites-create-web-jobs.md#CreateContinuous).

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Hogyan szerezhetem be a kimenő IP-címet a webalkalmazás?

A webalkalmazás a kimenő IP-címek listájának lekéréséhez:

1. A webalkalmazás panelére, az az Azure Portalon nyissa meg a **tulajdonságok** menü.
2. Keresse meg **kimenő ip-címek**.

A kimenő IP-címek listája jelenik meg.

Kimenő IP-címének lekéréséhez, ha a webhely App Service Environment-környezetben üzemeltetett kezelésével kapcsolatos információkért lásd: [kimenő hálózati címek](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Hogyan szerezhetem be egy fenntartott vagy dedikált bejövő IP-címet a webalkalmazás?

Állítsa be egy dedikált és fenntartott IP-címet az Azure-alkalmazás webhelyére beérkező hívások számára, telepítse és konfigurálja az IP-alapú SSL-tanúsítvány.

Vegye figyelembe, hogy egy dedikált és fenntartott IP-címet használja a bejövő hívások, az App Service-csomagban kell lennie legalább alapszintű szolgáltatáscsomag.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Exportálhatja a saját használandó kívül az Azure-ban, mint például máshol üzemeltetett webhely App Service-tanúsítvány? 

App Service-tanúsítványok minősülnek az Azure-erőforrások. Nem feltétlenül le kívül az Azure-szolgáltatások használatára. Használhatják az Azure-on kívülről nem exportálható. További információkért lásd: [– gyakori kérdések az App Service-tanúsítványokról és az egyéni tartományokról](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Exportálhatja a saját használata más Azure-felhőszolgáltatásokat App Service-tanúsítvány?

A portál az App Service-alkalmazások üzembe helyezése az App Service-tanúsítvány Azure Key vault fürtkezelési élményt biztosít. Azonban hogy rendelkezik lett fogadása kérelmek ügyfelek ezeket a tanúsítványokat, például egy App Service platformon kívül az Azure Virtual Machines. Ismerje meg, hogyan hozhat létre az App Service-tanúsítvány PFX helyi másolatát, így használhatja a tanúsítványt az egyéb Azure-erőforrások, lásd: [hozzon létre egy App Service-tanúsítvány PFX helyi példányának](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).

További információkért lásd: [– gyakori kérdések az App Service-tanúsítványokról és az egyéni tartományokról](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Miért látok a "Részben sikeres" üzenet jelenik meg a webes alkalmazás biztonsági mentése?

Egy közös biztonsági mentési hiba oka, hogy némi fájlok használatban vannak, az alkalmazás által. Használatban lévő fájlok zárolva vannak, a biztonsági mentés végrehajtása során. Ez megakadályozza, hogy ezek a fájlok biztonsági mentés alatt álló, és előfordulhat, hogy egy "Részben sikeres" állapotot eredményez. Potenciálisan megakadályozhatja a fájlok kizárása a biztonsági mentési folyamat által lépett fel. Ha szeretné, biztonsági mentése csak van szükség. További információkért lásd: [biztonsági mentése csak a hely és az Azure web Apps alkalmazások fontos részei](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/).

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Hogyan fejléc eltávolítása a HTTP-válasz?

A fejlécek eltávolítása a HTTP-válasz, frissítse a webhely web.config fájljában. További információkért lásd: [távolítsa el az Azure-webhelyeken szabványos server fejlécei](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Az App Service-ben PCI szabvány 3.0 és a 3.1 megfelelő?

Az Azure App Service Web Apps funkcióját jelenleg megfelel a PCI Data Security Standard (DSS) verzió 3.0-s Level 1. PCI DSS 3.1-es verzióját, tervbe. Tervezési már folyamatban van a legújabb standard bevezetése folytatódik hogyan.

PCI DSS 3.1-es verzióját tanúsítási kell Transport Layer Security (TLS) 1.0 letiltása. Jelenleg a TLS 1.0 letiltása lehetőség nem a legtöbb App Service-csomagok. Ha App Service-környezetben használja, vagy arra, hogy a számítási feladatok migrálása az App Service Environment-környezet, kaphat jobb vezérlését a környezetében. Ebbe beletartozik a TLS 1.0 letiltása az Azure támogatási szolgálatával. A közeljövőben tervezzük, hogy a felhasználók ezeket a beállításokat.

További információkért lásd: [Microsoft Azure App Service web app és való megfelelés PCI szabvány 3.0 3.1](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Hogyan használhatom a tesztelési környezet és üzembe helyezési pontok?

A Standard és prémium szintű App Service-csomagok a webalkalmazás az App Service-ben való telepítésekor, telepíthet egy külön üzembe helyezési pont helyett az alapértelmezett éles webalkalmazásra. Üzembe helyezési pontok, amelyek a saját állomásnevek élő webalkalmazások. Webes alkalmazás tartalmának és konfigurációs elemei felcserélhetők két üzembe helyezési tárhely, többek között az üzemelési között.

Üzembe helyezési pontok használatával kapcsolatos további információkért lásd: [App Service-ben egy átmeneti környezet beállítása](web-sites-staged-publishing.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Hogyan lehet hozzáférni és tekintse át a WebJob-naplókat?

A WebJob-naplókat, tekintse át:

1. Jelentkezzen be a [Kudu-webhely](https://*yourwebsitename*.scm.azurewebsites.net).
2. Válassza ki a webjobs-feladatot.
3. Válassza ki a **váltógomb kimeneti** gombra.
4. A kimeneti fájl letöltéséhez jelölje be a **letöltése** hivatkozásra.
5. Válassza ki az egyes futtatások **egyes meghívása**.
6. Válassza ki a **váltógomb kimeneti** gombra.
7. Válassza ki a letöltési hivatkozás.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Hibrid kapcsolatok használata az SQL Server próbálok. Miért jelenik meg az üzenet "System.OverflowException: A művelet aritmetikai túlcsordulást okozott"?

Hibrid kapcsolatok használatával az SQL Server elérését, ha a Microsoft .NET frissítés 2016. május 10-én a kapcsolódás meghiúsulását okozhatja. Ez az üzenet jelenhetnek meg:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Megoldás:

A kivétel okozta a problémát az a Hibridkapcsolat-kezelő, mivel megoldották a problémát. Ügyeljen arra, hogy [frissítése a Hibridkapcsolat-kezelő](https://go.microsoft.com/fwlink/?LinkID=841308) a probléma megoldásához.

## <a name="how-do-i-add-or-edit-a-url-rewrite-rule"></a>Hogyan hozzáadása vagy módosítása egy URL-újraírási szabályt?

Adja hozzá, vagy egy URL-átírási szabály szerkesztése:

1. Az Internet Information Services (IIS) kezelője beállítása úgy, hogy az App Service webalkalmazás csatlakozik. App Service-ben az IIS-kezelő csatlakoztatása kapcsolatban lásd: [távoli felügyelete az Azure-webhelyek IIS-kezelőben](https://azure.microsoft.com/blog/remote-administration-of-windows-azure-websites-using-iis-manager/).
2. Az IIS-kezelőben vagy szerkesztheti egy URL-újraírási szabályt. Ismerje meg, hogyan hozzáadása vagy módosítása egy URL-újraírási szabályt, lásd: [létrehozás újraírási szabályok az URL-újraíró modul](https://www.iis.net/learn/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module).

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Hogyan szabályozza a bejövő forgalmat az App Service-ben?

A hely szintjén az App Service-ben a bejövő forgalom szabályozása két lehetősége van:

* Kapcsolja be a dinamikus IP-korlátozások. Dinamikus IP-korlátozások bekapcsolása kapcsolatban lásd: [IP- és tartománykorlátozások Azure websiteshoz](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).
* Kapcsolja be a modul biztonsági. A modul biztonsági bekapcsolása kapcsolatban lásd: [modsecurity-hez a webalkalmazási tűzfal az Azure webhelyek](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/).

Ha az App Service Environment-környezet használatához [Barracuda tűzfal](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Hogyan tiltsa le a portokat az App Service-webalkalmazásban?

App Service-ben a bérlő megosztott környezetben nincs lehetőség az infrastruktúra jellege miatt adott portok blokkolására. 4016 4018 és 4020 TCP-portok is nyitva a Visual Studio távoli hibakeresési lehet.

Az App Service-környezet bejövő és kimenő forgalom teljes hozzáféréssel rendelkezik. Hálózati biztonsági csoportok segítségével korlátozhatja, vagy adott portok blokkolása. App Service Environment-környezet kapcsolatos további információkért lásd: [App Service Environment bemutatása](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Hogyan rögzítése a az F12 nyomkövetési?

Az F12 nyomkövetés rögzítése a két lehetősége van:

* F12 billentyű HTTP nyomkövetési
* F12 billentyű konzolkimenet

### <a name="f12-http-trace"></a>F12 billentyű HTTP nyomkövetési

1. Az Internet Explorerben nyissa meg a webhelyet. Fontos, hogy jelentkezzen be a következő lépések előtt. Ellenkező esetben az F12 nyomkövetési rögzíti a bizalmas bejelentkezési adatok.
2. Nyomja le az F12 billentyűt.
3. Ellenőrizze, hogy a **hálózati** lap van kijelölve, és válassza a zöld **lejátszása** gombra.
4. Hajtsa végre a lépéseket, hogy reprodukálnia a hibát.
5. Válassza ki a piros **leállítása** gombra.
6. Válassza ki a **mentése** (lemez ikon) gombra, és mentse a HAR-fájl (az Internet Explorer és a peremhálózati) *vagy* kattintson a jobb gombbal a HAR-fájlt, és válassza **Mentés másként HAR tartalommal** (a Chrome-ban).

### <a name="f12-console-output"></a>F12 billentyű konzolkimenet

1. Válassza ki a **konzol** fülre.
2. Minden egyes nullánál nagyobb elemeket tartalmazó lapon válassza a lap (**hiba**, **figyelmeztetés**, vagy **információk**). Ha a lapon nincs bejelölve, a tab ikon, a szürke vagy fekete a kurzor dobozból áthelyezésekor.
3. A panel üzenet területén kattintson a jobb gombbal, és válassza **másolhatja az összes**.
4. Illessze be a másolt szöveget a fájlba, és mentse a fájlt.

A HAR-fájl megtekintéséhez használja a [HAR megjelenítő](http://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Miért kapok hiba jelenik meg csatlakozni egy App Service-webalkalmazás az expressroute-hoz csatlakoztatott virtuális hálózat?

Az Azure-webalkalmazás csatlakoztatása egy virtuális hálózathoz, amely az Azure ExpressRoute csatlakozik-e meg, ha sikertelen. A következő üzenet jelenik meg: "Átjáró nem VPN-átjáró."

Jelenleg nem lehet az expressroute-hoz csatlakoztatott virtuális hálózat pont – hely VPN-kapcsolatok. A pont – hely VPN és ExpressRoute nem létezhet ugyanazon a virtuális hálózaton. További információkért lásd: [ExpressRoute és site-to-site VPN-kapcsolatok korlátok és korlátozások](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations).

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Hogyan kapcsolódhatok egy App Service-webalkalmazást egy virtuális hálózathoz statikus útválasztási (házirendalapú) átjáró rendelkező?

Egy App Service webalkalmazás csatlakoztatása egy virtuális hálózatot, amely rendelkezik statikus útválasztás (házirendalapú) átjáró jelenleg nem támogatott. Ha a cél virtuális hálózat már létezik, hogy pont – hely VPN engedélyezve van, a dinamikus útválasztású átjáró, mielőtt csatlakozhat egy alkalmazáshoz kell rendelkeznie. Ha az átjáró beállítása a statikus útválasztás, a pont – hely VPN nem engedélyezhető. 

További információkért lásd: [alkalmazás integrálása az Azure-beli virtuális hálózathoz](web-sites-integrate-with-vnet.md#getting-started).

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>Az App Service Environment környezetben miért is létrehozható csak egy App Service-csomag ellenére, hogy két feldolgozó is rendelkezésre áll?

Hibatűrő képességének biztosítása érdekében App Service Environment-környezet megköveteli, hogy minden feldolgozói készlethez kell legalább egy további számítási erőforrás. A további számítási erőforrás egy számítási feladatot nem lehet hozzárendelni.

További információkért lásd: [App Service-környezet létrehozása](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Miért látok időtúllépések, amikor megpróbálom hozzon létre egy App Service Environment-környezet?

Egyes esetekben egy App Service Environment-környezet létrehozása sikertelen. Ebben az esetben a Tevékenységnaplókban a következő hiba jelenik meg:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period.”}}
```

A probléma megoldásához győződjön meg arról, hogy a következő feltételek egyike sem igaz:
* Az alhálózat mérete túl kicsi.
* Az alhálózat nem üres.
* Az ExpressRoute megakadályozza, hogy az App Service Environment hálózati kapcsolati követelményeinek.
* Hibás hálózati biztonsági csoport megakadályozza, hogy az App Service Environment hálózati kapcsolati követelményeinek.
* Kényszerített bújtatás engedélyezve van.

További információkért lásd: [gyakori problémákat, üzembe helyezésekor (létrehozásakor) egy új Azure App Service-környezet](https://blogs.msdn.microsoft.com/waws/2016/05/13/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase/).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Miért nem lehet törölni az App Service-csomag?

App Service-csomag nem törölhető, ha bármely App Service-alkalmazások társítva az App Service-csomag. Mielőtt törli az App Service-csomagot, távolítsa el az összes társított App Service-alkalmazások App Service-csomag.

## <a name="how-do-i-schedule-a-webjob"></a>Hogyan ütemezhetek webjobs-feladat?

Az ütemezett webjobs-feladat Cron-kifejezések használatával hozhat létre:

1. Hozzon létre egy settings.job fájlt.
2. A JSON-fájlban a következők a schedule tulajdonság egy Cron-kifejezés használatával: 
    ```
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Az ütemezett webjobs-feladatok kapcsolatos további információkért lásd: [ütemezett webjobs-feladat létrehozása egy Cron-kifejezés használatával](web-sites-create-web-jobs.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Hogyan hajthatom végre behatolástesztelést végezhetnek az App Service-ben alkalmazásomhoz?

Behatolásvizsgálat, végrehajtásához [igényelnie](https://portal.msrc.microsoft.com/en-us/engage/pentest).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Hogyan konfigurálhatom egy App Service-webalkalmazást, amely a Traffic Manager az egyéni tartománynevet?

Egyéni tartománynév használata App Service-alkalmazások által használt Azure Traffic Manager terheléselosztásra kapcsolatban lásd: [konfigurálása egy egyéni tartománynevet az Azure-webalkalmazás létrehozása a Traffic Managerrel](web-sites-traffic-manager-custom-domain-name.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Az App Service-tanúsítvány meg van jelölve, a csalások. Hogyan oldhatom fel ez?

Egy App Service-tanúsítvány vásárolt előfizetést, a tartomány ellenőrzése közben a következő üzenetet láthatja:

"A tanúsítvány meg lett jelölve lehetséges csalás. A kérelem jelenleg felülvizsgálat alatt áll. Ha a tanúsítvány nem válik használhatóvá 24 órán belül, forduljon az Azure ügyfélszolgálatához."

Az üzenet azt jelzi, az csalás ellenőrzési folyamat akár 24 óráig is eltarthat. Ebben az időszakban, továbbra is az üzenet jelenik meg.

Ha az App Service-tanúsítvány továbbra is jelenjen meg ez az üzenet 24 óra múlva, futtassa a következő PowerShell-parancsfájlt. A parancsfájl kapcsolatba lép a [tanúsítványszolgáltató](https://www.godaddy.com/) közvetlenül a probléma megoldása érdekében.

```
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzureRmResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Hogyan hitelesítési és engedélyezési működik az App Service szolgáltatásban?

Hitelesítés és engedélyezés az App Service részletes dokumentációjáért lásd: azonosítsa a szolgáltató bejelentkezések különböző docs:
* [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md)
* [Facebook](app-service-mobile-how-to-configure-facebook-authentication.md)
* [Google](app-service-mobile-how-to-configure-google-authentication.md)
* [Microsoft-fiók](app-service-mobile-how-to-configure-microsoft-authentication.md)
* [Twitter](app-service-mobile-how-to-configure-twitter-authentication.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Hogyan átirányítja az alapértelmezett *. azurewebsites.net tartományban, a saját Azure-alapú webes alkalmazás egyéni tartomány?

Amikor új webhely létrehozása az Azure-ban, egy alapértelmezett Web Apps használatával *sitename*. azurewebsites.net tartomány a helyhez van rendelve. Ha egy egyéni nevet hozzá a helyhez, és nem szeretné a felhasználók érhetik el az alapértelmezett *. azurewebsites.net tartományban, irányíthatja át az alapértelmezett URL-címe. Az egyéni tartományban a webhely alapértelmezett tartományból származó összes forgalom átirányítása kezelésével kapcsolatos információkért lásd: [az alapértelmezett tartomány átirányítása az Azure web apps szolgáltatásban az egyéni tartomány](https://zainrizvi.io/blog/block-default-azure-websites-domain/).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Hogyan állapíthatom meg, melyik verzió a .NET a verziója telepítve van az App Service szolgáltatásban?

A leggyorsabb mód a Microsoft .NET App Service-ben telepített verzió azonosításához van, a Kudu konzol használatával. Elérheti a Kudu konzol a portálon vagy az App Service-alkalmazás URL-Címének használatával. Részletes útmutatásért lásd: [App Service-ben a telepített .NET-verzió](https://blogs.msdn.microsoft.com/waws/2016/11/02/how-to-determine-the-installed-net-version-in-azure-app-services/).

## <a name="why-isnt-autoscale-working-as-expected"></a>Automatikus skálázási miért nem működik, a várható?

Ha Azure automatikus méretezési funkciójával még nem számát vagy a web app-példány horizontálisan skálázott megfelelő-e, akkor előfordulhat, hogy fut-e be egy olyan forgatókönyvet, amelyben szándékosan lehetőséget választjuk, nem méretezhető miatt "állapota váltakozik." végtelen ciklust elkerülése érdekében Ez általában akkor fordul elő, amikor a horizontális felskálázást és a horizontális leskálázási küszöbértékek között nem megfelelő. Megtudhatja, hogyan "váltakozó" elkerülése érdekében, valamint hogy olvassa a többi az automatikus méretezés ajánlott eljárásairól, olvassa [automatikus méretezés ajánlott eljárásairól](../azure-monitor/platform/autoscale-best-practices.md#autoscale-best-practices).

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Miért nem automatikus skálázási néha méretezése csak részben?

Automatikus skálázási akkor aktiválódik, ha a metrikák túllépnek előre konfigurált határok. Egyes esetekben előfordulhat, hogy láthatja, hogy, hogy a kapacitás csak részben kitölti képest, amire számít. Ez akkor fordulhat elő, ha nem érhetők el a kívánt példányok számát. Ebben az esetben az automatikus méretezés részlegesen tölti be a rendelkezésre álló példányok számát. Automatikus skálázási majd futtatja az újraegyensúlyozási logika további kapacitásának lekérése. A fennmaradó példányok oszt ki. Vegye figyelembe, hogy ez eltarthat néhány percig.

Ha nem látja a várt számú példányok néhány perc múlva, mert a részleges Újratöltés nem volt elegendő ahhoz, hogy a metrikák határain belül lehet. Vagy az automatikus méretezés előfordulhat, hogy méretezte, mert elérte a metrikák alsó határt.

Ha ezek a feltételek egyike a alkalmazni, és a probléma tartósan fennáll, küldjön egy támogatási kérést.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Hogyan kapcsolhatom a HTTP-tömörítésének saját tartalom?

Tömörítés mindkét a statikus és dinamikus tartalomtípusok bekapcsolásához adja hozzá a következő kódot az alkalmazásszintű web.config fájlba:

```
<system.webServer>
<urlCompression doStaticCompression="true" doDynamicCompression="true" />
< /system.webServer>
```

A megadott dinamikus és statikus MIME-típusok tömöríteni kívánt is megadhatja. További információkért lásd: a fórum kérdés válasz [httpCompression beállítások egy egyszerű Azure-webhelyen](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview).

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Hogyan migrálhatok egy a helyszíni környezetből az App Service?

App Service-ben migrálni a helyeket a Windows és Linux webkiszolgálók, használhatja az Azure App Service Migration Assistant. Az áttelepítési eszköz hoz létre webalkalmazásokat és adatbázisokat az Azure-ban igény szerint, és ezután közzéteszi a tartalmat. További információkért lásd: [Azure App Service Migration Assistant](https://www.migratetoazure.net/).
