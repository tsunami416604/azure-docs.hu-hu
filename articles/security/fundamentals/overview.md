---
title: Bevezetés az Azure biztonsága | Microsoft dokumentumok
description: Ismerje meg az Azure Security szolgáltatást, annak szolgáltatásait és működését.
services: security
documentationcenter: na
author: UnifyCloud
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2019
ms.author: TomSh
ms.openlocfilehash: a7957c7cbcfa511ea441d8c7bd4371f56ab87560
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461684"
---
# <a name="introduction-to-azure-security"></a>Bevezetés az Azure biztonsági megoldásaiba
## <a name="overview"></a>Áttekintés
Tudjuk, hogy a biztonság az első feladat a felhőben, és mennyire fontos, hogy pontos és időszerű információkat találjon az Azure biztonságáról. Az Azure alkalmazásaihoz és szolgáltatásaihoz való használatának egyik legjobb oka a biztonsági eszközök és képességek széles skálájának kihasználása. Ezek az eszközök és képességek lehetővé teszik biztonságos megoldások létrehozását a biztonságos Azure platformon. A Microsoft Azure biztosítja az ügyféladatok titkosságát, integritását és elérhetőségét, ugyanakkor átlátható elszámoltathatóságot is lehetővé teszi.

Ez a cikk átfogó áttekintést nyújt az Azure-ban elérhető biztonságról.

### <a name="azure-platform"></a>Azure-platform
Az Azure egy nyilvános felhőszolgáltatási platform, amely az operációs rendszerek, programozási nyelvek, keretrendszerek, eszközök, adatbázisok és eszközök széles választékát támogatja. Linux-tárolókat futtathat a Docker-integrációval; alkalmazások készítése JavaScript, Python, .NET, PHP, Java és Node.js nyelven; háttérrendszereket készíthet iOS, Android és Windows rendszerű eszközökre.

Az Azure nyilvános felhőszolgáltatásai ugyanazokat a technológiákat támogatják, amelyekben már több millió fejlesztő és informatikai szakember támaszkodik és bízik. Amikor egy nyilvános felhőszolgáltatóra épít, vagy informatikai eszközöket telepít át, akkor az adott szervezet azon képességére támaszkodik, amely az alkalmazások és az adatok védelmére szolgál a felhőalapú eszközök biztonságának kezeléséhez biztosított szolgáltatásokkal és vezérlőkkel.

Az Azure infrastruktúráját a létesítménytől az alkalmazásokig tervezték, hogy egyszerre több millió ügyfelet szolgálhassanak, és megbízható alapot biztosít, amelyre a vállalkozások megfelelhetnek biztonsági követelményeiknek.

Emellett az Azure a konfigurálható biztonsági beállítások széles skáláját biztosítja, és lehetővé teszi azok vezérlését, hogy testre szabhassa a biztonságot, hogy megfeleljen a szervezet központi telepítései egyedi követelményeinek. Ez a dokumentum segít megérteni, hogy az Azure biztonsági képességei hogyan segíthetnek ezeknek a követelményeknek a teljesítésében.

> [!Note]
> A dokumentum elsődleges célja az ügyfél felé néző vezérlők, amelyek segítségével testre szabhatja és növelheti az alkalmazások és szolgáltatások biztonságát.
>
> Arról, hogy a Microsoft hogyan biztosítja az Azure platformot, tekintse meg az [Azure infrastruktúra-biztonság című témakört.](infrastructure.md)

## <a name="summary-of-azure-security-capabilities"></a>Az Azure biztonsági képességeinek összefoglalása

### <a name="features-to-secure-the-azure-platform"></a>Az Azure platform védelmére irányuló funkciók
A következő funkciók olyan képességek, amelyeket áttekinthet, hogy biztosítsa az Azure Platform biztonságos kezelését. A további részletezéshez a Microsoft négy területen foglalkozik az ügyfelek bizalmával kapcsolatos kérdésekkel: biztonságos platform, adatvédelmi & ellenőrzések, megfelelőség és átláthatóság.

| [Biztonságos platform](https://www.microsoft.com/trustcenter/Security/default.aspx)  | [Adatvédelmi & vezérlők](https://www.microsoft.com/trustcenter/Privacy/default.aspx)  |[Megfelelőség](https://www.microsoft.com/trustcenter/Compliance/default.aspx)   | [Átláthatóság](https://www.microsoft.com/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [Biztonsági fejlesztési ciklus](https://www.microsoft.com/sdl/), Belső auditok | [Az adatok kezelése mindig](https://www.microsoft.com/trustcenter/Privacy/You-own-your-data) | [Adatvédelmi központ](https://www.microsoft.com/trustcenter/default.aspx) |[Hogyan biztosítja a Microsoft az ügyféladatokat az Azure-szolgáltatásokban?](https://www.microsoft.com/trustcenter/Transparency/default.aspx) |
| [Kötelező biztonsági képzés, háttérellenőrzések](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) |  [Az adatok helyének ellenőrzése](https://www.microsoft.com/trustcenter/Privacy/Where-your-data-is-located) |  [Közös vezérlők központ](https://www.microsoft.com/trustcenter/Common-Controls-Hub) |[Hogyan kezeli a Microsoft az adatok helyét az Azure-szolgáltatásokban?](https://azuredatacentermap.azurewebsites.net/)|
| [Behatolásvizsgálat,](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) [behatolásészlelés, DDoS,](https://www.microsoft.com/trustcenter/Security/ThreatManagement) [naplózás & naplózás](https://www.microsoft.com/trustcenter/Security/AuditingAndLogging) | [Adathozzáférés biztosítása az Ön feltételei szerint](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [A felhőszolgáltatások átvilágítási ellenőrzőlistája](https://www.microsoft.com/trustcenter/Compliance/Due-Diligence-Checklist) |[Ki férhet hozzá az ön adataihoz a Microsoftban milyen feltételekkel](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [A legkorszerűbb adatközpont,](https://www.microsoft.com/cloud-platform/global-datacenters)fizikai biztonság, [biztonságos hálózat](network-overview.md) | [Válasz adás a bűnüldözésre](https://www.microsoft.com/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [Megfelelőség szolgáltatás, hely & ipar szerint](https://www.microsoft.com/trustcenter/Compliance/default.aspx) |[Hogyan biztosítja a Microsoft az ügyféladatokat az Azure-szolgáltatásokban?](https://www.microsoft.com/trustcenter/Transparency/default.aspx)|
|  [Biztonsági eseményekre adott válasz](https://aka.ms/SecurityResponsepaper), [Megosztott felelősség](https://aka.ms/sharedresponsibility) |[Szigorú adatvédelmi szabványok](https://www.microsoft.com/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Tekintse át az Azure-szolgáltatások, az átláthatósági központ minősítését](https://www.microsoft.com/trustcenter/Compliance/default.aspx)|

### <a name="features-to-secure-data-and-application"></a>Szolgáltatások az adatok és az alkalmazások védelméhez
Afelhő-szolgáltatás modelltől függően változó felelősség van az alkalmazás vagy szolgáltatás biztonságának kezeléséért felelős személyért. Az Azure Platformon olyan képességek állnak rendelkezésre, amelyek a beépített funkciókés az Azure-előfizetésbe üzembe helyezhető partnermegoldások révén segítenek ezeknek a feladatoknak a teljesítésében.

A beépített képességek hat funkcionális területen vannak rendszerezve: Műveletek, Alkalmazások, Tárolás, Hálózatkezelés, Számítási és Identitás. Az Azure Platformon az ebben a hat területen elérhető funkciókról és képességekről további részleteket összefoglaló információk nyújtanak.

## <a name="operations"></a>Műveletek
Ez a szakasz további információkat tartalmaz a biztonsági műveletek legfontosabb szolgáltatásairól, és összefoglaló információkat ezekről a képességekről.

### <a name="security-and-audit-dashboard"></a>Biztonsági és naplózási irányítópult
A [Biztonsági és naplózási megoldás](../../security-center/security-center-intro.md) átfogó képet nyújt a szervezet informatikai biztonsági helyzetéről, és beépített keresési [lekérdezéseket](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/) biztosít a figyelmet igénylő, jelentős problémákra. A [Biztonság és naplózás](https://technet.microsoft.com/library/mt484091.aspx) irányítópultja az Azure Monitor-naplók biztonságával kapcsolatos mindenszolgáltatás kezdőképernyője. Magas szintű betekintést nyújt a számítógépek biztonsági állapotába. Olyan funkciót is kínál, amellyel megjeleníthető az elmúlt 24 óra, 7 nap vagy bármely más egyéni időszak összes eseménye.

Ezenkívül beállíthatja a Biztonsági & megfelelőséget úgy, hogy [automatikusan végrehajtson bizonyos műveleteket,](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/) ha egy adott eseményt észlel.

### <a name="azure-resource-manager"></a>Azure Resource Manager
[Az Azure Resource Manager](../../azure-resource-manager/management/deployment-models.md) lehetővé teszi, hogy a megoldás erőforrásait csoportként dolgozzon. A megoldás összes erőforrását egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti. Egy [Azure Resource Manager sablont](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/) használ a központi telepítéshez, és ez a sablon működhet a különböző környezetekben, például a tesztelés, átmeneti és éles környezetben. A Resource Manager biztonsági, naplózási és címkézési szolgáltatásokat biztosít, hogy segítsen az erőforrások kezelésében a telepítést követően.

Az Azure Resource Manager-sablonalapú telepítések segítenek az Azure-ban üzembe helyezett megoldások biztonságának növelésében, mivel a szabványos biztonsági vezérlési beállítások szabványosított sablonalapú telepítésekbe integrálhatók. Ez csökkenti a manuális központi telepítés során esetlegesen előforduló biztonsági konfigurációs hibák kockázatát.

### <a name="application-insights"></a>Application Insights
[Az Application Insights](https://docs.microsoft.com/azure/application-insights/) egy bővíthető alkalmazásteljesítmény-kezelési (APM) szolgáltatás a webfejlesztők számára. Az Application Insights segítségével figyelheti az élő webalkalmazásokat, és automatikusan észlelheti a teljesítményanomáliákat. Hatékony elemzési eszközöket tartalmaz a problémák diagnosztizálásához és annak megértéséhez, hogy a felhasználók valójában mit csinálnak az alkalmazásokkal. Folyamatosan figyeli az alkalmazást, mind a tesztelés során, mind a közzététel vagy üzembe helyezés után.

Az Application Insights diagramokat és táblázatokat hoz létre, amelyek megmutatják például, hogy a legtöbb felhasználó milyen napszakokat kap, mennyire reagál az alkalmazás, és milyen jól szolgálják ki azokat a külső szolgáltatások, amelyektől függ.

Ha összeomlások, hibák vagy teljesítményproblémák merülnek fel, részletesen kereshet a telemetriai adatok között az ok diagnosztizálásához. És a szolgáltatás e-maileket küld Önnek, ha bármilyen változás van az alkalmazás rendelkezésre állásában és teljesítményében. Az Application Insight így értékes biztonsági eszközzé válik, mivel segít a bizalmas, integritási és rendelkezésre állási biztonsági triád ban való rendelkezésre állásban.

### <a name="azure-monitor"></a>Azure Monitor
[Az Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) vizualizációt, lekérdezést, útválasztást, riasztást, automatikus méretezést és automatizálást kínál az Azure-infrastruktúrából[(Activity Log)](../../azure-monitor/platform/platform-logs-overview.md)és az egyes Azure-erőforrásokból[(diagnosztikai naplók)](../../azure-monitor/platform/platform-logs-overview.md)származó adatokon. Az Azure Monitor segítségével riasztást adhat az Azure-naplókban létrehozott, biztonsággal kapcsolatos eseményekről.

### <a name="azure-monitor-logs"></a>Azure Monitor-naplók
[Azure Monitor naplók](https://azure.microsoft.com/documentation/services/log-analytics/) – Informatikai felügyeleti megoldást biztosít mind a helyszíni és külső felhőalapú infrastruktúra (például AWS) mellett az Azure-erőforrásokat. Az Azure Monitor ból származó adatok közvetlenül az Azure Monitor-naplókba irányíthatók, így egy helyen láthatja a teljes környezetre vonatkozó metrikákat és naplókat.

Az Azure Monitor naplói hasznos eszköz lehet a kriminalisztikai és egyéb biztonsági elemzésben, mivel az eszköz lehetővé teszi, hogy rugalmas lekérdezési megközelítéssel gyorsan keressen nagy mennyiségű, biztonsággal kapcsolatos bejegyzésekközött. Emellett a helyszíni [tűzfal- és proxynaplók exportálhatók az Azure-ba, és az Azure Monitor-naplók használatával elemzésre elérhetővé tehetők.](../../log-analytics/log-analytics-agent-windows.md)

### <a name="azure-advisor"></a>Azure Advisor
[Az Azure Advisor](../../advisor/index.yml) egy személyre szabott felhőtanácsadó, amely segít az Azure-telepítések optimalizálásának. A program elemzi az erőforrás-konfigurációs és -használati telemetriákat, Ezután olyan megoldásokat javasol, amelyek javítják az erőforrások [teljesítményét,](../../advisor/advisor-performance-recommendations.md) [biztonságát](../../advisor/advisor-security-recommendations.md)és [magas rendelkezésre állását,](../../advisor/advisor-high-availability-recommendations.md) miközben lehetőségeket keresnek [az Azure-kiadások csökkentésére.](../../advisor/advisor-cost-recommendations.md) Az Azure Advisor biztonsági javaslatokat kínál, amelyek jelentősen javíthatják az Azure-ban üzembe helyezett megoldások általános biztonsági állapotát. Ezek a javaslatok az [Azure Security Center](../../security-center/security-center-intro.md) által végzett biztonsági elemzésből származnak.

### <a name="azure-security-center"></a>Azure Security Center
[A Security Center](../../security-center/security-center-intro.md) segítségével megelőzheti, észlelheti és reagálhat a fenyegetésekre, és nagyobb rálátással és az Azure-erőforrások biztonságának szabályozására. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

Emellett a Security Center segít a biztonsági műveletekben azáltal, hogy egyetlen irányítópultot biztosít, amely riasztásokat és javaslatokat tartalmaz, amelyek azonnal kezelhetők. Gyakran előfordulhat, hogy a Biztonsági központ konzolon egyetlen kattintással orvosolhatja a problémákat.
## <a name="applications"></a>Alkalmazások
A szakasz további információkat nyújt az alkalmazásbiztonság legfontosabb szolgáltatásairól, és összefoglaló információkat tartalmaz ezekről a képességekről.

### <a name="web-application-vulnerability-scanning"></a>Webalkalmazás biztonsági résének vizsgálata
Az [App Service-alkalmazás](../../app-service/overview.md) biztonsági réseinek tesztelésének egyik legegyszerűbb módja az [Alufólia biztonsággal való integráció](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) használata az alkalmazás egykattintásos biztonsági résének leolvasásához. A teszteredményeket egy könnyen érthető jelentésben tekintheti meg, és részletes útmutatással megtudhatja, hogyan javíthatja ki az egyes biztonsági réseket.

### <a name="penetration-testing"></a>Penetrációs vizsgálat
Ha saját behatolási teszteket szeretne végrehajtani, vagy egy másik lapolvasó-csomagot vagy-szolgáltatót szeretne használni, kövesse az [Azure penetrációs tesztelési jóváhagyási folyamatát,](https://docs.microsoft.com/azure/security/fundamentals/pen-testing ) és előzetes jóváhagyást kell kérnie a kívánt behatolási tesztek elvégzéséhez.

### <a name="web-application-firewall"></a>Webalkalmazás tűzfala
Az [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) webalkalmazás-tűzfala (WAF) segít megvédeni a webalkalmazásokat a gyakori webalapú támadásoktól, például az SQL-injektálástól, a webhelyek közötti parancsfájlok futtatását és a munkamenet-eltérítéstől. Előre konfigurált védelemmel szolgál az [Open Web Application Security Project (OWASP) által a 10 leggyakoribb biztonsági résként](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)azonosított fenyegetésekkel szemben.

### <a name="authentication-and-authorization-in-azure-app-service"></a>Hitelesítés és engedélyezés az Azure App Service-ben
[Az App Service-hitelesítés / engedélyezés](../../app-service/overview-authentication-authorization.md) egy olyan szolgáltatás, amely lehetővé teszi az alkalmazás számára, hogy jelentkezzen be a felhasználók, így nem kell módosítani a kódot az alkalmazás háttérrendszerében. Ez egy egyszerű módja annak, hogy megvédje az alkalmazást, és a felhasználónkénti adatokkal való munkavégzést.

### <a name="layered-security-architecture"></a>Réteges biztonsági architektúra
Mivel [az App Service-környezetek](../../app-service/environment/app-service-app-service-environment-intro.md) egy [azure-beli virtuális hálózatba](../../virtual-network/virtual-networks-overview.md)telepített elkülönített futásidejű környezetet biztosítanak, a fejlesztők létrehozhatnak egy réteges biztonsági architektúrát, amely az egyes alkalmazásszintekhez különböző szintű hálózati hozzáférést biztosít. Gyakori vágy, hogy elrejtse az API-háttérrendszereket az általános internet-hozzáféréselől, és csak az UPSTREAM webalkalmazások számára engedélyezze az API-k hívását. [A hálózati biztonsági csoportok (NSG-k)](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/) az App Service-környezeteket tartalmazó Azure virtual network alhálózatokon használhatók az API-alkalmazásokhoz való nyilvános hozzáférés korlátozására.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Webkiszolgáló diagnosztikája és alkalmazásdiagnosztikája
Az App Service-webalkalmazások diagnosztikai funkciókat biztosítanak a webkiszolgálóról és a webalkalmazásból származó információk naplózásához. Ezek logikailag webkiszolgáló- és [alkalmazásdiagnosztikára](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx)vannak leválasztva. [web server diagnostics](../../app-service/troubleshoot-diagnostic-logs.md) A webkiszolgáló két jelentős előrelépést tartalmaz a webhelyek és alkalmazások diagnosztizálásában és hibaelhárításában.

Az első új szolgáltatás az alkalmazáskészletekkel, a munkavégző folyamatokkal, a helyekkel, az alkalmazástartományokkal és a futó kérelmekkel kapcsolatos valós idejű állapotinformációk. A második új előnye a részletes nyomkövetési események, amelyek nyomon követik a kérelmet a teljes kérelem-válasz folyamat során.

A nyomkövetési események gyűjtésének engedélyezéséhez az IIS 7 beállítható úgy, hogy automatikusan rögzítse a teljes nyomkövetési naplókat XML formátumban, az eltelt idő vagy hibaválasz kódok alapján megadott kérelmekhez.

#### <a name="web-server-diagnostics"></a>Webkiszolgáló diagnosztikája
A következő típusú naplókat engedélyezheti vagy tilthatja le:

-   Részletes hibanaplózás – Részletes hibainformáció a HTTP-állapotkódokhoz, amelyek hibát jeleznek (400-as vagy nagyobb állapotkód). Ez olyan információkat tartalmazhat, amelyek segíthetnek meghatározni, hogy a kiszolgáló miért adta vissza a hibakódot.

-   Sikertelen kérelmek nyomkövetése – részletes információk a sikertelen kérelmekről, beleértve a kérelem feldolgozásához használt IIS-összetevők és az egyes összetevőkben eltöltött idő nyomon követését. Ez akkor lehet hasznos, ha a webhely teljesítményét próbálja növelni, vagy elkülöníti, hogy mi okozza egy adott HTTP-hiba visszaadását.

-   Webkiszolgáló naplózása – A W3C kiterjesztett naplófájlformátumot használó HTTP-tranzakciókkal kapcsolatos információk. Ez akkor hasznos, ha meghatározza az általános helymetrikák, például a kezelt kérelmek száma, vagy hány kérelmek egy adott IP-címet.

#### <a name="application-diagnostics"></a>Alkalmazásdiagnosztika
[Az alkalmazásdiagnosztika](../../app-service/troubleshoot-diagnostic-logs.md) lehetővé teszi a webalkalmazás által előállított információk rögzítését. ASP.NET alkalmazások a [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) osztály segítségével naplózhatják az információkat az alkalmazás diagnosztikai naplójába. Az Application Diagnostics programon belül két fő eseménytípust különböztethetünk meg: az alkalmazások teljesítményével és az alkalmazások hibáival kapcsolatos eseményeket. A hibák tovább oszthatók csatlakozási, biztonsági és meghibásodási problémákra. A meghibásodásokat jellemzően az alkalmazás kódjának hibái okozzák.

Az Application Diagnostics felületén a következő módokon csoportosíthatók az események:

-   Összes (minden esemény megjelenítése)
-   Alkalmazáshibák (a kivételesemények megjelenítése)
-   Teljesítmény (a teljesítménnyel kapcsolatos események megjelenítése)

## <a name="storage"></a>Storage
A szakasz további információkat nyújt az Azure storage-biztonság kulcsfontosságú funkcióiról, és összefoglaló információkat ezekről a képességekről.

### <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)
A szerepköralapú hozzáférés-vezérlés (RBAC) segítségével biztosíthatja tárfiókját. A hozzáférés korlátozása a [szükséges ismeret](https://en.wikipedia.org/wiki/Need_to_know) és a [legkevésbé bizalmas biztonsági](https://en.wikipedia.org/wiki/Principle_of_least_privilege) elvek alapján elengedhetetlen azon szervezetek számára, amelyek az adatelérésbiztonsági házirendeket kívánják érvényesíteni. Ezeket a hozzáférési jogokat úgy biztosítják, hogy a megfelelő RBAC szerepkört egy bizonyos hatókörön lévő csoportokhoz és alkalmazásokhoz rendelik. A [beépített RBAC-szerepkörök](../../role-based-access-control/built-in-roles.md), például a storage-fiók közreműködője használatával jogosultságokat rendelhet a felhasználókhoz. Az [Azure Resource Manager-modell](../../storage/blobs/security-recommendations.md) használatával a tárfiók tárkulcsaihoz való hozzáférés a szerepköralapú hozzáférés-vezérlés (RBAC) segítségével szabályozható.

### <a name="shared-access-signature"></a>Megosztott hozzáférésű aláírás
A [közös hozzáférésű jogosultságkód (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) delegált hozzáférést biztosít a tárfiókon lévő erőforrásokhoz. A SAS azt jelenti, hogy korlátozott engedélyeket adhat az ügyfélnek a tárfiókban lévő objektumokhoz egy adott időszakra és egy megadott engedélykészlettel. Ezeket a korlátozott engedélyeket anélkül is megadhatja, hogy meg kellene osztania a fiók hozzáférési kulcsait.

### <a name="encryption-in-transit"></a>Titkosítás az átvitel során
Az átvitel közbeni titkosítás az adatok védelmének mechanizmusa, ha azokat hálózatokon keresztül továbbítják. Az Azure Storage segítségével a következő kkel biztosíthatja az adatokat:
-   [Átviteli szintű titkosítás](../../storage/blobs/security-recommendations.md), például HTTPS, amikor adatokat továbbít az Azure Storage-ba vagy az Azure Storage-ból.

-   [Vezetékes titkosítás](../../storage/blobs/security-recommendations.md), például [SMB 3.0 titkosítás](../../storage/blobs/security-recommendations.md) [az Azure File shares.](../../storage/files/storage-dotnet-how-to-use-files.md)

-   Ügyféloldali titkosítás, az adatok titkosítása a tárolóba való átvitel előtt, valamint az adatok visszafejtése a tárolóból való átvitel után.

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban
Sok szervezet számára az inaktív adattitkosítás kötelező lépés az adatvédelem, a megfelelőség és az adatok szuverenitása felé. Az Azure storage három biztonsági szolgáltatása biztosítja az "inaktív" adatok titkosítását:

-   [A storage service encryption](../../storage/common/storage-service-encryption.md) lehetővé teszi, hogy kérje, hogy a tárolási szolgáltatás automatikusan titkosítja az adatokat, amikor az Azure Storage-ba írásakor.

-   [Az ügyféloldali titkosítás](../../storage/common/storage-client-side-encryption.md) az inaktív titkosítás szolgáltatását is biztosítja.

-   [Az Azure Disk Encryption](../azure-security-disk-encryption-overview.md) lehetővé teszi az IaaS virtuális gép által használt operációsrendszer-lemezek és adatlemezek titkosítását.

### <a name="storage-analytics"></a>Storage Analytics
[Az Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) naplózást hajt végre, és metrikaadatokat biztosít egy tárfiókhoz. Ezeket az adatokat a kérelmek nyomon követésére, a használati trendek elemzésére és a tárfiókkal kapcsolatos problémák diagnosztizálására használhatja. A Storage Analytics naplózza a tárolási szolgáltatásoknak elküldött sikeres és sikertelen kérelmek részletes információit. Ezekkel az információkkal monitorozhatók az egyes kérelmek és diagnosztizálhatók a tárolási szolgáltatások problémái. A kérelmek naplózása a legjobb erőfeszítés alapján történik. A hitelesített kérések alábbi típusai vannak rögzítve:
-   Sikeres kérések.

-   Sikertelen kérelmek, beleértve az időtúlkérést, a szabályozást, a hálózatot, az engedélyezést és egyéb hibákat.

-   Kérelmek megosztott hozzáférésű aláírást (SAS) használó, beleértve a sikertelen és sikeres kérelmeket.

-   Elemzési adatokra vonatkozó kérések.

### <a name="enabling-browser-based-clients-using-cors"></a>Böngészőalapú ügyfelek engedélyezése CORS használatával
[A kereszteredi erőforrás-megosztás (CORS)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) egy olyan mechanizmus, amely lehetővé teszi a tartományok számára, hogy engedélyt adjanak egymás erőforrásainak elérésére. A felhasználói ügynök további fejléceket küld annak biztosítására, hogy egy bizonyos tartományból betöltött JavaScript-kód hozzáférhessen egy másik tartományban található erőforrásokhoz. Az utóbbi tartomány ezután további fejlécekkel válaszol, amelyek engedélyezik vagy megtagadják az eredeti tartomány hozzáférését az erőforrásaihoz.

Az Azure storage-szolgáltatások most már támogatják a CORS-t, így a szolgáltatás CORS-szabályainak beállítása után a rendszer kiértékeli a szolgáltatással szemben egy másik tartományból származó megfelelően hitelesített kérelmet annak megállapítására, hogy az engedélyezett-e a megadott szabályok szerint.

## <a name="networking"></a>Hálózat
A szakasz további információkat nyújt az Azure hálózati biztonság ának legfontosabb funkcióiról, és összefoglaló információkat ezekről a képességekről.

### <a name="network-layer-controls"></a>Hálózati réteg vezérlők
A hálózati hozzáférés-vezérlés az adott eszközök vagy alhálózatok közötti és az onnan érkező kapcsolatok korlátozásának eszköze, és a hálózati biztonság lényegét képviseli. A hálózati hozzáférés-vezérlés célja annak biztosítása, hogy a virtuális gépek és szolgáltatások csak azok és eszközök számára legyenek elérhetők, amelyek számára elérhetővé szeretné őket tenni.

#### <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)
A [hálózati biztonsági csoport (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) egy alapvető állapotalapú csomagszűrő tűzfal, amely lehetővé teszi a hozzáférés [5-t.](https://www.techopedia.com/definition/28190/5-tuple) Az NSG-k nem biztosítanak alkalmazásréteg-ellenőrzést vagy hitelesített hozzáférés-vezérlést. Ezek segítségével szabályozhatja az Azure virtuális hálózaton belüli alhálózatok közötti forgalmat, valamint az Azure virtuális hálózat és az internet közötti forgalmat.

#### <a name="route-control-and-forced-tunneling"></a>Útvonalvezérlés és kényszerített bújtatás
Az Azure virtuális hálózatok útválasztási viselkedésének szabályozása kritikus fontosságú hálózati biztonsági és hozzáférés-vezérlési képesség. Ha például azt szeretné, hogy az Azure virtuális hálózatba irányuló és onnan érkező összes forgalom átmegy a virtuális biztonsági berendezésen, képesnek kell lennie az útválasztási viselkedés szabályozására és testreszabására. Ezt a felhasználó által definiált útvonalak azure-ban konfigurálásával teheti meg.

[A felhasználó által definiált útvonalak](../../virtual-network/virtual-networks-udr-overview.md) lehetővé teszik a bejövő és kimenő útvonalak testreszabását az egyes virtuális gépekre vagy alhálózatokba be- és kifelé irányuló forgalom számára a lehető legbiztonságosabb útvonal biztosítása érdekében. [A kényszerített bújtatás](https://www.petri.com/azure-forced-tunneling) olyan mechanizmus, amelynek segítségével biztosíthatja, hogy a szolgáltatások ne kezdeményezhessenek kapcsolatot az interneten lévő eszközökkel.

Ez nem az, hogy képes fogadni a bejövő kapcsolatokat, majd válaszolni rájuk. Az előtér-webkiszolgálóknak válaszolniuk kell az internetes állomásoktól érkező kérésekre, és így az internetes forrásból származó forgalom bevihető ezekbe a webkiszolgálókba, és a webkiszolgálók is válaszolhatnak.

A kényszerített bújtatás gyakran használatos arra, hogy az internetre irányuló kimenő forgalmat a helyszíni biztonsági proxykon és tűzfalakon keresztül kényszerítsék.

#### <a name="virtual-network-security-appliances"></a>Virtuális hálózati biztonsági berendezések
Míg a hálózati biztonsági csoportok, a felhasználó által definiált útvonalak és a kényszerített bújtatás biztonsági szintet biztosít az [OSI-modell](https://en.wikipedia.org/wiki/OSI_model)hálózati és átviteli rétegein, előfordulhat, hogy a verem magasabb szintjein szeretné engedélyezni a biztonságot. Ezeket a továbbfejlesztett hálózati biztonsági funkciókat egy Azure partner hálózati biztonsági berendezés megoldás használatával érheti el. A legfrissebb Azure-partnerhálózati biztonsági megoldásokat az [Azure Piactér](https://azure.microsoft.com/marketplace/) felkeresésével és a "biztonság" és a "hálózati biztonság" keresésével találhatja meg.

### <a name="azure-virtual-network"></a>Azure Virtual Network
Az Azure virtuális hálózat (VNet) a saját, felhőben található hálózatának megfelelője. Ez az Azure hálózati háló az előfizetésének szentelt logikai elkülönítése. A hálózaton belül teljes mértékben irányíthatja az IP-címblokkokat, a DNS-beállításokat, a biztonsági házirendeket és az útválasztási táblázatokat. A virtuális hálózat ot alhálózatokra szegmentálhatja, és az Azure IaaS virtuális gépeket (VM-ek) és/vagy [a felhőszolgáltatások (PaaS-szerepkörpéldányok)](../../cloud-services/cloud-services-choose-me.md) az Azure virtuális hálózatokon helyezheti el.

A virtuális hálózatot ezen felül a helyszíni hálózathoz is csatlakoztathatja az Azure-ban elérhető [kapcsolati lehetőségek](../../vpn-gateway/index.yml) egyikével. Lényegében kiterjesztheti a hálózatot az Azure-ra, az IP-címblokkok teljes körű irányítása és a vállalati méretű Azure által nyújtott előnyök mellett.

Az Azure networking támogatja a különböző biztonságos távelérési forgatókönyvek. Ezek közül néhány a következők:

-   [Egyes munkaállomások csatlakoztatása Azure virtuális hálózathoz](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

-   [Csatlakozás helyszíni hálózathoz egy Azure virtuális hálózathoz VPN-nel](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

-   [A helyszíni hálózat csatlakoztatása egy Azure virtuális hálózathoz egy dedikált WAN-kapcsolattal](../../expressroute/expressroute-introduction.md)

-   [Az Azure virtuális hálózatok összekapcsolása](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

### <a name="vpn-gateway"></a>VPN Gateway
Az Azure virtuális hálózat és a helyszíni hely közötti hálózati forgalom küldéséhez létre kell hoznia egy VPN-átjárót az Azure virtuális hálózathoz. A [VPN-átjáró](../../vpn-gateway/vpn-gateway-about-vpngateways.md) olyan virtuális hálózati átjáró, amely titkosított forgalmat küld nyilvános kapcsolaton keresztül. Vpn-átjárók használatával is küldhet forgalmat az Azure virtuális hálózatok között az Azure hálózati hálón keresztül.

### <a name="express-route"></a>Express Route
A Microsoft Azure [ExpressRoute](../../expressroute/expressroute-introduction.md) egy dedikált WAN-kapcsolat, amely lehetővé teszi, hogy a helyszíni hálózatokat a Microsoft-felhőbe terjessze ki egy dedikált magánkapcsolaton keresztül, amelyet egy kapcsolódási szolgáltató segít elő.

![Express Route](./media/overview/azure-security-fig1.png)

Az ExpressRoute használatával kapcsolatokat létesíthet a Microsoft-felhőszolgáltatásokkal, például a Microsoft Azure-ral, az Office 365-tel és a CRM Online-nal. A kapcsolatok lehetnek: bármely elemek közötti (IP VPN) hálózat, pontok közötti Ethernet-hálózat vagy egy virtuális keresztkapcsolat egy kapcsolatszolgáltatón keresztül egy közös elhelyezési létesítményben.

Az ExpressRoute-kapcsolatok nem mennek át a nyilvános interneten, így biztonságosabbnak tekinthetők, mint a VPN-alapú megoldások. Így az ExpressRoute-kapcsolatok a tipikus internetes kapcsolatoknál megbízhatóbbak, gyorsabbak, gyorsabb a válaszidejük, és biztonságosabbak.


### <a name="application-gateway"></a>Application Gateway
A Microsoft [Azure Application Gateway](../../application-gateway/overview.md) [szolgáltatásként alkalmazáskézbesítési vezérlőt (ADC)](https://en.wikipedia.org/wiki/Application_delivery_controller) biztosít, amely különböző 7- es réteges terheléselosztási lehetőségeket kínál az alkalmazásszámára.

![Application Gateway](./media/overview/azure-security-fig2.png)

Lehetővé teszi a webfarm okainak optimalizálását azáltal, hogy a CPU-igényes TLS-végződést az Application Gateway-re (más néven "TLS tehermentesítés" vagy "TLS-áthidalás" néven nevezik). Emellett más Layer 7 útválasztási lehetőségeket is biztosít, beleértve a bejövő forgalom ciklikus multiplexelésű elosztását, a cookie-alapú munkamenet-affinitást, az URL-útvonal-alapú útválasztást, valamint azt a lehetőséget, hogy egyetlen Alkalmazásátjáró mögött több webhelyet is üzemeltethet. Az Azure Application Gateway egy 7. rétegbeli terheléselosztó.

Feladatátvételt és teljesítményalapú útválasztást biztosít a HTTP-kérelmek számára különböző kiszolgálók között, függetlenül attól, hogy a felhőben vagy a helyszínen vannak.

Az alkalmazás számos alkalmazáskézbesítési vezérlőt (ADC) kínál, beleértve a HTTP terheléselosztást, a cookie-alapú munkamenet-affinitást, [a TLS-kiszervezést,](../../application-gateway/tutorial-restrict-web-traffic-powershell.md)az egyéni állapotpróbákat, a többhelyes és még sok más szolgáltatást.

### <a name="web-application-firewall"></a>Web Application Firewall (Webalkalmazási tűzfal)
A webalkalmazás-tűzfal az [Azure Application Gateway](../../application-gateway/overview.md) szolgáltatása, amely védelmet nyújt az on-t használó webalkalmazások számára, amelyek alkalmazásátjárót használnak a szabványos alkalmazáskézbesítési vezérlési (ADC) függvényekhez. A webalkalmazási tűzfal ezt úgy éri el, hogy védelmet nyújt az alkalmazásoknak az OWASP 10 leggyakoribb webes biztonsági résének többségével szemben.

![Web Application Firewall (Webalkalmazási tűzfal)](./media/overview/azure-security-fig1.png)

-   SQL-injektálás elleni védelem

-   Gyakori webes támadások (például parancsinjektálás, HTTP-kéréscsempészet, HTTP-válaszfelosztás és távolifájl-beszúrásos támadás) elleni védelem

-   HTTP protokoll megsértése elleni védelem

-   HTTP protokollanomáliák (például hiányzó gazdagép-felhasználói ügynök és Accept (Elfogadás) fejlécek) elleni védelem

-   Robotprogramok, webbejárók és képolvasók elleni védelem

-   Gyakori alkalmazáshelytelen konfigurációk észlelése (azaz Apache, IIS stb.)

A webes támadásokkal szembeni védelmet nyújtó központi webalkalmazási tűzfal egyszerűbbé teszi a biztonságfelügyeletet, és nagyobb biztonságot ad az alkalmazásnak a behatolások jelentette veszéllyel szemben. Emellett a WAF-megoldás gyorsabban képes kezelni a biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett hogy az egyes webalkalmazások védelmét biztosítaná. A meglévő alkalmazásátjárókat egyszerűen lehet átalakítani webalkalmazási tűzfallal rendelkező alkalmazásátjárókká.

### <a name="traffic-manager"></a>Traffic Manager
A Microsoft [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) lehetővé teszi a különböző adatközpontokban lévő szolgáltatásvégpontok felhasználói forgalmának eloszlását. A Traffic Manager által támogatott szolgáltatásvégpontok közé tartoznak az Azure virtuális gépek, a webalkalmazások és a felhőszolgáltatások. A Traffic Manager külső, nem Azure-beli végpontokkal is használható. A Traffic Manager a DNS -t használja az ügyfélkérelmek nek a legmegfelelőbb végpontra történő irányítására a [forgalom-útválasztási módszer](../../traffic-manager/traffic-manager-routing-methods.md) és a végpontok állapota alapján.

A Traffic Manager a forgalom-útválasztási módszerek széles skáláját biztosítja a különböző alkalmazásigényeknek, a [végpontállapot-figyelésnek](../../traffic-manager/traffic-manager-monitoring.md)és az automatikus feladatátvételnek megfelelően. A Traffic Manager ellenáll a meghibásodásoknak, beleértve akár egy egész Azure-régió meghibásodását is.

### <a name="azure-load-balancer"></a>Azure Load Balancer
Az [Azure Load Balancer](../../load-balancer/load-balancer-overview.md) magas rendelkezésre állást és hálózati teljesítményt biztosít alkalmazásai számára. Ez egy 4-es réteg (TCP, UDP) terheléselosztó, amely elosztja a bejövő forgalmat a terheléselosztási halmazban definiált szolgáltatások kifogástalan példányai között. Az Azure Load Balancer a következőkre konfigurálható:

-   A virtuális gépekbe érkező internetes forgalom terheléselosztása. Ezt a [konfigurációt internetes terheléselosztásnak nevezik.](../../load-balancer/concepts-limitations.md#publicloadbalancer)

-   A virtuális hálózat ban lévő virtuális gépek, a felhőszolgáltatásokban lévő virtuális gépek, illetve a helyszíni számítógépek és a létesítmények közötti virtuális hálózat virtuális gépei közötti terheléselosztási forgalom. Ezt a konfigurációt [belső terheléselosztásnak nevezzük.](../../load-balancer/concepts-limitations.md#internalloadbalancer)

- Külső forgalom továbbítása egy adott virtuális gépre

### <a name="internal-dns"></a>Belső DNS
A felügyeleti portálon vagy a hálózati konfigurációs fájlban a virtuális hálózatban használt DNS-kiszolgálók listáját kezelheti. Az ügyfél legfeljebb 12 DNS-kiszolgálót adhat hozzá minden virtuális hálózathoz. A DNS-kiszolgálók megadásakor fontos ellenőrizni, hogy az ügyfél DNS-kiszolgálóit az ügyfél környezetének megfelelő sorrendben sorolja-e fel. A DNS-kiszolgálólisták nem működnek ciklikus multiplexeléssel. A megadott sorrendben használatosak. Ha a lista első DNS-kiszolgálója elérhető, az ügyfél ezt a DNS-kiszolgálót használja, függetlenül attól, hogy a DNS-kiszolgáló megfelelően működik-e vagy sem. Az ügyfél virtuális hálózatának DNS-kiszolgálói sorrendjének módosításához távolítsa el a DNS-kiszolgálókat a listáról, és adja hozzá őket az ügyfél által kívánt sorrendben. A DNS támogatja a "CIA" biztonsági triád elérhetőségi aspektusát.

### <a name="azure-dns"></a>Azure DNS
A [domain névrendszer](https://technet.microsoft.com/library/bb629410.aspx)vagy dns felelős a webhely vagy szolgáltatás nevének IP-címre történő fordításáért (vagy feloldásáért). Az [Azure DNS](../../dns/dns-overview.md) egy üzemeltetési szolgáltatás, amely a Microsoft Azure infrastruktúráját használja a DNS-tartományok névfeloldásához. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti. A DNS támogatja a "CIA" biztonsági triád elérhetőségi aspektusát.

### <a name="azure-monitor-logs-nsgs"></a>Az Azure Monitor naplózza az NSG-ket
A következő diagnosztikai naplókategóriákat engedélyezheti az NSG-k számára:

-   Esemény: Olyan bejegyzéseket tartalmaz, amelyekhez az NSG-szabályok a virtuális gépekre és a MAC-címen alapuló példányszerepkörökre vonatkoznak. A szabályok állapotát 60 másodpercenként gyűjti a rendszer.

-   Szabályszámláló: Az egyes NSG-szabályok megtagadására vagy engedélyezésére alkalmazott bejegyzéseket tartalmazza.

### <a name="security-center"></a>Security Center
[Az Azure Security Center](../../security-center/security-center-intro.md) folyamatosan elemzi az Azure-erőforrások biztonsági állapotát a hálózati biztonsággal kapcsolatos gyakorlati tanácsok érdekében. Amikor a Security Center potenciális biztonsági réseket azonosít, [javaslatokat](../../security-center/security-center-recommendations.md) készít, amelyek végigvezetik a szükséges vezérlők konfigurálásának folyamatán az erőforrások megkeményedése és védelme érdekében.

## <a name="compute"></a>Compute
A szakasz további információkat tartalmaz a terület legfontosabb szolgáltatásairól, és összefoglaló információkat tartalmaz ezekről a képességekről.

### <a name="antimalware--antivirus"></a>Kártevőirtó & víruskereső
Az Azure IaaS segítségével kártevőirtó szoftvereket használhat a biztonsági gyártóktól, például a Microsofttól, a Symantectől, a Trend Microtól, a McAfee-től és a Kaspersky-től, hogy megvédje a virtuális gépeket a rosszindulatú fájloktól, adware-ektől és egyéb fenyegetésektől. [A Microsoft Antimalware](antimalware.md) for Azure Cloud Services and Virtual Machines egy olyan védelmi képesség, amely segít azonosítani és eltávolítani a vírusokat, kémprogramokat és más rosszindulatú szoftvereket. A Microsoft Antimalware konfigurálható riasztásokat biztosít, ha ismert rosszindulatú vagy nemkívánatos szoftverek próbálnak telepíteni magukat vagy futtatni az Azure-rendszereken. A Microsoft Antimalware az Azure Security Center használatával is telepíthető

### <a name="hardware-security-module"></a>Hardverbiztonsági modul
A titkosítás és a hitelesítés csak akkor növeli a biztonságot, ha maguk a kulcsok védettek. Az [Azure Key Vaultban](../../key-vault/general/overview.md)való tárolással egyszerűsítheti a kritikus fontosságú titkos kulcsok és kulcsok kezelését és biztonságát. A Key Vault lehetővé teszi a kulcsok tárolását a FIPS 140-2 Level 2 szabványoknak megfelelő hardveres biztonsági modulokban (HSM). Az SQL Server titkosítási kulcsai a biztonsági mentéshez vagy [az átlátszó adattitkosításhoz](https://msdn.microsoft.com/library/bb934049.aspx) mind tárolhatók a Key Vaultban az alkalmazások bármely kulcsával vagy titkával. A védett elemekhez való engedélyek és hozzáférés kezelése az [Azure Active Directoryn](https://azure.microsoft.com/documentation/services/active-directory/)keresztül történik.

### <a name="virtual-machine-backup"></a>Virtuális gép biztonsági mentése
Az [Azure Backup](../../backup/backup-overview.md) olyan megoldás, amely nulla tőkebefektetéssel és minimális működési költségekkel védi az alkalmazásadatokat. Az alkalmazáshibák megsérülhetnek az adatokban, és az emberi hibák olyan hibákat okozhatnak az alkalmazásokban, amelyek biztonsági problémákhoz vezethetnek. Az Azure Backup segítségével a Windows és Linux rendszert futtató virtuális gépek védettek.

### <a name="azure-site-recovery"></a>Azure Site Recovery
A szervezet [üzletmenet-folytonossági/vész-helyreállítási (BCDR)](../../best-practices-availability-paired-regions.md) stratégiájának fontos része, hogy kitalálja, hogyan tarthatja fenn a vállalati munkaterheléseket és alkalmazásokat, ha tervezett és nem tervezett leállások lépnek fel. [Az Azure Site Recovery](../../site-recovery/site-recovery-overview.md) segít a számítási feladatok és alkalmazások replikációjának, feladatátvételének és helyreállításának koordinálásában, hogy azok másodlagos helyről is elérhetők, ha az elsődleges hely leáll.

### <a name="sql-vm-tde"></a>SQL VM TDE
[Az átlátszó adattitkosítás (TDE)](../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md) és oszlopszintű titkosítás (CLE) az SQL-kiszolgáló titkosítási szolgáltatásai. A titkosítás ezen formája megköveteli az ügyfelektől a titkosításhoz használt titkosítási kulcsok kezeléséhez és tárolásához.

Az Azure Key Vault (AKV) szolgáltatás célja, hogy javítsa a biztonságot és a kulcsok felügyeletét egy biztonságos és magas rendelkezésre állású helyen. Az SQL Server Connector lehetővé teszi, hogy az SQL Server használja ezeket a kulcsokat az Azure Key Vaultból.

Ha az SQL Server helyszíni gépekkel fut, a helyszíni SQL Server-gépről az Azure Key Vault eléréséhez számos lépést követhet. Az Azure-beli virtuális gépeken lévő SQL Server számára azonban időt takaríthat meg az Azure Key Vault-integráció funkció használatával. Néhány Azure PowerShell-parancsmaggal a funkció engedélyezéséhez automatizálhatja az SQL virtuális gép számára a key vault eléréséhez szükséges konfigurációt.

### <a name="vm-disk-encryption"></a>Virtuálisgép-lemeztitkosítás
[Az Azure Disk Encryption](../azure-security-disk-encryption-overview.md) egy új képesség, amely segít a Windows és a Linux IaaS virtuálisgép-lemezek titkosításában. A Windows szabványos BitLocker szolgáltatását és a Linux DM-Crypt funkcióját alkalmazza, hogy kötettitkosítást biztosítson az operációs rendszer és az adatlemezek számára. A megoldás integrálva van az Azure Key Vaultszolgáltatással, így szabályozhatja és kezelheti a kulcstároló-előfizetéslemez-titkosítási kulcsokat és titkos kulcsokat. A megoldás azt is biztosítja, hogy a virtuális gép lemezein lévő összes adat inkultatitikálva legyen az Azure storage-ban.

### <a name="virtual-networking"></a>Virtuális hálózat
A virtuális gépeknek hálózati kapcsolatra van szükségük. Ennek a követelménynek a támogatásához az Azure-nak virtuális gépekhez kell csatlakoznia egy Azure virtuális hálózathoz. Az Azure virtuális hálózat egy logikai konstrukció, amely a fizikai Azure hálózati hálóra épül. Minden logikai [Azure virtuális hálózat](../../virtual-network/virtual-networks-overview.md) el van különítve az összes többi Azure virtuális hálózatok. Ez az elkülönítés segít biztosítani, hogy a központi telepítések hálózati forgalma nem érhető el más Microsoft Azure-ügyfelek számára.

### <a name="patch-updates"></a>Javítás frissítések
A javításfrissítések alapot adnak a lehetséges problémák megtalálásához és javításához, és egyszerűsítik a szoftverfrissítés-kezelési folyamatot, egyrészt azáltal, hogy csökkentik a vállalaton belül telepíthető szoftverfrissítések számát, másrészt növelik a megfelelőség figyelését.

### <a name="security-policy-management-and-reporting"></a>Biztonsági házirend kezelése és jelentése
[A Security Center](../../security-center/security-center-intro.md) segítségével megelőzheti, észlelheti és reagálhat a fenyegetésekre, és nagyobb betekintést nyújt az Azure-erőforrások biztonságába, és szabályozhatja azokat. Integrált biztonsági figyelést és szabályzatkezelést biztosít az Azure-előfizetésekben, segít észlelni az egyébként észrevétlenül megjelenő fenyegetéseket, és a biztonsági megoldások széles ökoszisztémájával működik együtt.

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés
A rendszerek, alkalmazások és adatok biztonságossá tétele identitásalapú hozzáférés-vezérléssel kezdődik. A Microsoft üzleti termékeibe és szolgáltatásaiba beépített identitás- és hozzáférés-kezelési funkciók segítenek megvédeni a szervezeti és személyes adatokat a jogosulatlan hozzáféréstől, miközben a jogos felhasználók számára bármikor és bárhol elérhetővé teszik azokat.

### <a name="secure-identity"></a>Biztonságos identitás
A Microsoft termékeiben és szolgáltatásaiban több biztonsági gyakorlatot és technológiát is alkalmaz az identitás és a hozzáférés kezelésére.

-   [A többtényezős hitelesítés](https://azure.microsoft.com/services/multi-factor-authentication/) hez a felhasználóknak több módszert kell használniuk a hozzáféréshez, a helyszíni és a felhőhöz. Erős hitelesítést biztosít számos egyszerű ellenőrzési lehetőséggel, miközben a felhasználókat egy egyszerű bejelentkezési folyamattal biztosítja.

-   [A Microsoft Authenticator](https://aka.ms/authenticator) felhasználóbarát többtényezős hitelesítési élményt nyújt, amely a Microsoft Azure Active Directory és a Microsoft-fiókkal egyaránt működik, és támogatja a viselhető és ujjlenyomat-alapú jóváhagyásokat.

-   [A jelszóházirend-kényszerítés](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/) növeli a hagyományos jelszavak biztonságát azáltal, hogy hosszúsági és összetettségi követelményeket, kényszerített időszakos rotációt és fiókzárolást ír elő sikertelen hitelesítési kísérletek után.

-   [A tokenalapú hitelesítés](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) lehetővé teszi a hitelesítést az Azure Active Directoryn keresztül.

-   [A szerepköralapú hozzáférés-vezérlés (RBAC)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) lehetővé teszi a hozzáférés biztosítását a felhasználó hozzárendelt szerepköre alapján, így a felhasználók csak a feladatfeladataik elvégzéséhez szükséges hozzáférési mennyiséget biztosíthatják a felhasználóknak. Az RBAC-t testreszabhatja a szervezet üzleti modelljének és kockázati toleranciájának.

-   [Az integrált identitáskezelés (hibrid identitás)](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/) lehetővé teszi a felhasználók hozzáférésének felügyeletét a belső adatközpontokban és a felhőplatformokon, egyetlen felhasználói identitást hozva létre az összes erőforrás hitelesítéséhez és engedélyezéséhez.

### <a name="secure-apps-and-data"></a>Biztonságos alkalmazások és adatok
[Az Azure Active Directory,](https://azure.microsoft.com/services/active-directory/)egy átfogó identitás- és hozzáférés-kezelési felhőmegoldás, amely biztonságos hozzáférést biztosít a webhelyen és a felhőben lévő alkalmazásokban lévő adatokhoz, és leegyszerűsíti a felhasználók és csoportok kezelését. Egyesíti az alapvető címtárszolgáltatásokat, a fejlett identitáskezelést, a biztonságot és az alkalmazáshozzáférés-kezelést, és megkönnyíti a fejlesztők számára, hogy házirendalapú identitáskezelést hozzanak létre az alkalmazásaikba. Az Azure Active Directoryt kiegészítheti fizetős képességekkel is, az Azure Active Directory Alapszintű, Prémium P1 és Prémium P2 kiadásával.

| Ingyenes / Közös funkciók     | Alapvető funkciók    |Prémium P1 funkciók |Prémium P2 funkciók | Azure Active Directory csatlakozás – csak a Windows 10 kapcsolódó szolgáltatásai|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Címtárobjektumok](../../active-directory/active-directory-whatis.md), [Felhasználó/csoportkezelés (add/update/delete)/ Felhasználó alapú kiépítés, Eszközregisztráció](../../active-directory/active-directory-whatis.md), [Egyszeri bejelentkezés (SSO),](../../active-directory/active-directory-whatis.md) [Önkiszolgáló jelszómódosítása felhőbeli felhasználók számára,](../../active-directory/active-directory-whatis.md) [Csatlakozás (Szinkronizálási motor, amely kiterjeszti a helyszíni könyvtárakat az Azure Active Directoryra)](../../active-directory/active-directory-whatis.md), [Biztonsági / használati jelentések](../../active-directory/active-directory-whatis.md)       |   [Csoportalapú hozzáférés-kezelés / kiépítés](../../active-directory/active-directory-whatis.md), [Önkiszolgáló jelszó visszaállítása felhőalapú felhasználók számára](../../active-directory/active-directory-whatis.md), [Vállalati márkajelzés (Bejelentkezési lapok /Hozzáférési panel testreszabása)](../../active-directory/active-directory-whatis.md), [Alkalmazásproxy](../../active-directory/active-directory-whatis.md), [SLA 99.9%](../../active-directory/active-directory-whatis.md) |  [Önkiszolgáló csoport- és alkalmazásfelügyeleti/önkiszolgáló alkalmazáskiegészítések/dinamikus csoportok,](../../active-directory/active-directory-whatis.md) [önkiszolgáló jelszó-visszaállítás/-módosítás/feloldás helyszíni leírással,](../../active-directory/active-directory-whatis.md) [többtényezős hitelesítéssel (felhőbeli és helyszíni (MFA-kiszolgáló))](../../active-directory/active-directory-whatis.md), [MIM CAL + MIM Server](../../active-directory/active-directory-whatis.md), [Cloud App Discovery](../../active-directory/active-directory-whatis.md), Health [,](../../active-directory/active-directory-whatis.md) [Automatikus jelszóváltás a csoportfiókokhoz](../../active-directory/active-directory-whatis.md)|    [Identitásvédelem](../../active-directory/identity-protection/overview.md), [kiemelt identitáskezelés](../../active-directory/privileged-identity-management/pim-configure.md)|   [Csatlakozzon egy eszközhöz az Azure AD, az Asztali egyszeri szolgáltatások, a Microsoft Passport for Azure AD, a Administrator BitLocker helyreállításhoz](../../active-directory/active-directory-whatis.md), [az MDM automatikus igényléséhez, az önkiszolgáló BitLocker-helyreállításhoz, a Windows 10-hez további helyi rendszergazdákhoz az Azure AD Join segítségével](../../active-directory/active-directory-whatis.md)|


- [A Cloud App Discovery](../../active-directory/cloudappdiscovery-get-started.md) az Azure Active Directory egyik prémium szintű szolgáltatása, amely lehetővé teszi a szervezet alkalmazottai által használt felhőalkalmazások azonosítását.

- [Az Azure Active Directory identity Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) egy olyan biztonsági szolgáltatás, amely az Azure Active Directory anomáliadetektálási képességeit használja a kockázatészlelések és a szervezet identitását esetlegesen befolyásoló potenciális biztonsági rések összevont nézetének biztosításához.

- [Az Azure Active Directory tartományi szolgáltatások](https://azure.microsoft.com/services/active-directory-ds/) lehetővé teszi, hogy csatlakozzon az Azure virtuális gépek egy tartományhoz tartományvezérlők telepítése nélkül. A felhasználók a vállalati Active Directory-hitelesítő adataikkal jelentkezhetnek be ezekbe a virtuális gépekbe, és zökkenőmentesen hozzáférhetnek az erőforrásokhoz.

- [Az Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) egy magas rendelkezésre állású, globális identitáskezelési szolgáltatás a fogyasztók számára elérhető alkalmazások számára, amelyek több százmillió identitásra skálázhatók, és mobil- és webes platformokon integrálhatók. Az ügyfelek bejelentkezhetnek az összes alkalmazásba a meglévő közösségi médiafiókokat használó testreszabható élmények révén, vagy új önálló hitelesítő adatokat hozhat létre.

- [Az Azure Active Directory B2B Collaboration](https://aka.ms/aad-b2b-collaboration) egy biztonságos partnerintegrációs megoldás, amely támogatja a vállalatközi kapcsolatokat azáltal, hogy lehetővé teszi a partnerek számára, hogy a vállalati alkalmazásokhoz és adatokhoz szelektíven hozzáférjenek a saját felügyelt identitások használatával.

- [Az Azure Active Directory-csatlakozás](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) lehetővé teszi a felhőbeli képességek kiterjesztését a Windows 10-es eszközökre a központi felügyelet érdekében. Lehetővé teszi a felhasználók számára, hogy az Azure Active Directoryn keresztül csatlakozzanak a vállalati vagy szervezeti felhőhöz, és leegyszerűsíti az alkalmazásokhoz és erőforrásokhoz való hozzáférést.

- [Az Azure Active Directory alkalmazásproxy](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/) egyszeri és biztonságos távelérést biztosít a helyszíni üzemeltetett webalkalmazások számára.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg [közös felelősségét a felhőben.](shared-responsibility.md)

- Ismerje meg, hogy [az Azure Security Center](https://azure.microsoft.com/services/security-center/) hogyan segíthet a fenyegetések megelőzésében, észlelésében és az azokra való reagálásban az Azure-erőforrások biztonságának fokozott láthatóságával és szabályozásával.
