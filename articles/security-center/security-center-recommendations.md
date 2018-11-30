---
title: Biztonsági javaslatok kezelése az Azure Security Centerben |} A Microsoft Docs
description: Ez a dokumentum végigvezeti hogyan javaslatokat az Azure Security Center segítségével megvédheti Azure-erőforrásait, és legyen naprakész, megfelel a biztonsági szabályzatoknak.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2018
ms.author: rkarlin
ms.openlocfilehash: 3e8333b521832579a942d3fffb06103ad0431acc
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52316617"
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>Biztonsági javaslatok kezelése az Azure Security Centerben
Ez a dokumentum végigvezeti javaslatok használatát az Azure Security Centerben az Azure-erőforrások védelmére.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  Ez a dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="what-are-security-recommendations"></a>Mik azok a biztonsági javaslatok?
A Security Center rendszeresen elemzi az Azure-erőforrások biztonsági állapotát. A Security Center javaslatokat hoz létre, amikor lehetséges biztonsági réseket észlel. A javaslatok végigvezetik Önt a szükséges vezérlők konfigurálásának folyamatán.

## <a name="implementing-security-recommendations"></a>Biztonsági javaslatok alkalmazása
### <a name="set-recommendations"></a>Set-javaslatok
A [biztonsági szabályzatok beállítása az Azure Security Center](security-center-azure-policy.md), fog megismerkedni:

* Biztonsági szabályzatok konfigurálása.
* Kapcsolja be az adatgyűjtést.
* Válassza ki, melyik javaslatok a biztonsági szabályzat részeként megtekintéséhez.

Aktuális javaslatok szabályzatközpontban Rendszerfrissítések, az alapkonfigurációs szabályok, a kártevőirtó programok, körül [hálózati biztonsági csoportok](../virtual-network/security-overview.md) alhálózatok és a hálózati adapterek, SQL database naplózási szolgáltatása, az SQL database transzparens adattitkosítás, és a webalkalmazás-tűzfalak.  [Biztonsági szabályzatok beállítása](security-center-azure-policy.md) minden javaslat beállítás leírását.

### <a name="monitor-recommendations"></a>A figyelő javaslatok
Miután beállított egy biztonsági házirendet, a Security Center elemzi az Ön erőforrásainak állapotát, hogy észlelhesse a potenciális biztonsági réseket. A **javaslatok** alatt csempe **áttekintése** lehetővé teszi, hogy ismeri a Security Center által azonosított javaslatok teljes száma.

![Javaslatok csempe][1]

Minden javaslat részleteinek megtekintéséhez válassza ki a **csempére a javaslatok** alatt **áttekintése**. **Javaslatok** nyílik meg.

![Szűrő javaslatok][2]

Javaslatok szűrheti. A javaslatok szűréséhez válasszon **szűrő** a a **javaslatok** panelen. A **szűrő** panel megnyílik, és szeretne látni a fontossága és állapota értékek választja.

A javaslatok egy tábla formájában jelennek meg, amelyben minden egyes sor egy adott javaslatot tartalmaz. Ez a tábla oszlopait a következők:

* **Leírás**: bemutatja a javaslat, és mit kell végrehajtani, e-mail-címeit.
* **ERŐFORRÁS**: sorolja fel az erőforrásokat, amelyekre ez a javaslat vonatkozik.
* **ÁLLAPOT**: a javaslat aktuális állapotát ismerteti:
  * **Nyissa meg**: A javaslattal egyelőre még nem foglalkoztak.
  * **Folyamatban lévő**: A javaslat jelenleg alkalmaznak az erőforrásokhoz, és ilyenkor Ön nem szükséges.
  * **Megoldott**: A javaslatot már befejeződött (ebben az esetben a sor szürkén jelenik meg).
* **SEVERITY** (Súlyosság): az adott javaslat súlyosságát határozza meg:
  * **Magas**: biztonsági rés fontos erőforrásnál (például egy alkalmazást, a virtuális gép vagy a hálózati biztonsági csoport) létezik, és beavatkozást igényel.
  * **Közepes**: A biztonsági rés, és nem kritikus vagy kiegészítő lépések szükségesek egy folyamat befejezéséhez vagy ezt meg kell szüntetni.
  * **Alacsony**: A biztonsági rés, amelyek beavatkozást igényel, de nem igényel azonnali beavatkozást. (Alapértelmezés szerint alacsony súlyosságú javaslatok nem jelennek meg, de Ön is szűrővel bekapcsolhatja, ha meg szeretné tekinteni azokat.)

Referenciaként az alábbi táblázat segítségével segítenek megérteni a rendelkezésre álló ajánlásokat, és mindegyik funkciója alkalmazásuk esetén.

> [!NOTE]
> Érdemes tudni a [klasszikus és Resource Manager üzembe helyezési modellek](../azure-classic-rm.md) az Azure-erőforrásokhoz.
>
>

| Ajánlás | Leírás |
| --- | --- |
| [Adatgyűjtés engedélyezése az előfizetések számára](security-center-enable-data-collection.md) |Javasolja, hogy kapcsolja be az adatgyűjtést a biztonsági szabályzatban az előfizetések és az összes Azure-beli virtuális gépek (VM) és a nem Azure-beli számítógépek mindegyike esetében. |
| [Biztonsági konfigurációk javítása](security-center-remediate-os-vulnerabilities.md) |Javasolja, hogy az operációs rendszer konfigurálását az ajánlott biztonsági konfigurációs szabályok, például igazítás, ne engedélyezze a jelszavak mentését. |
| [Rendszerfrissítések alkalmazása](security-center-apply-system-updates.md) |Javasolja, hogy telepít hiányzó rendszerbiztonsági és kritikus frissítések a Windows és Linux rendszerű virtuális gépek és számítógépek. |
| [A Just-In-Time a alkalmazni a hálózati hozzáférés-vezérlés](security-center-just-in-time.md) | Javasolja, time VM access csak a alkalmazni. Az igény szerinti funkció érhető el a Standard szintű, a Security Center. A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md). |
| [Rendszerfrissítések utáni újraindítás](security-center-apply-system-updates.md#reboot-after-system-updates) |Javasolja, hogy a rendszerfrissítések alkalmazási folyamatának befejezéséhez indítson újra egy virtuális gépet. |
| [Webalkalmazási tűzfal hozzáadása](security-center-add-web-application-firewall.md) |Javasolja, hogy telepít egy webalkalmazási tűzfal (WAF) webes végpontok. A WAF ajánlás bármely nyilvános IP-Címmel (példány szintű IP vagy Load elosztott terhelésű IP), amely rendelkezik egy társított hálózati biztonsági csoport nyílt bejövő webes porttal (80,443) jelennek meg. </br>A Security Center javasolja, hogy a WAF elleni támadásokat a webalkalmazások virtuális gépeken, és az App Service Environment célcsoportkezelés segítségével telepítsen. Van egy App Service Environment (ASE) egy [prémium](https://azure.microsoft.com/pricing/details/app-service/) szolgáltatás, amely az Azure App Service-alkalmazások biztonságos futtatása teljesen elkülönített és dedikált környezetet biztosít az Azure App Service csomag lehetőséget. ASE kapcsolatos további információkért tekintse meg a [App Service Environment dokumentációja](../app-service/environment/intro.md).</br>A Security Center több webalkalmazás védheti meg ezeket az alkalmazásokat a meglévő WAF-példányok hozzáadásával. |
| [Alkalmazásvédelem véglegesítése](security-center-add-web-application-firewall.md#finalize-application-protection) |A WAF konfigurációjának befejezéséhez forgalmat a WAF berendezésre át irányítva. Ez a javaslat a következő befejezi a szükséges telepítőfájlokat módosításokat. |
| [Újgenerációs tűzfal hozzáadása](security-center-add-next-generation-firewall.md) |Javasolja, hogy egy Microsoft-partner a biztonsági megoldásokat növelése érdekében adjon hozzá egy Next Generation Firewall (NGFW). |
| [Csak az újgenerációs tűzfalon keresztül haladjon a forgalom](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Javasolja, hogy konfigurálja a hálózati biztonsági csoport (NSG) szabályai kényszerítheti a bejövő forgalmat a virtuális géphez az Újgenerációs tűzfalon keresztül. |
| [Endpoint Protection telepítése](security-center-install-endpoint-protection.md) |Javasolja, hogy telepítsen kártevőirtó programokat a virtuális gépekre (csak Windows rendszerű virtuális gépek esetében). |
| [Hálózati biztonsági csoportok engedélyezése az alhálózatokra vagy virtuális gépekre](security-center-enable-network-security-groups.md) |Az NSG-ket alhálózatokhoz vagy virtuális gépeken engedélyezését javasolja. |
| [Internetről elérhető végponton keresztüli hozzáférés korlátozása](security-center-restrict-access-through-internet-facing-endpoints.md) |Javasolja, hogy a bejövő forgalomra vonatkozó szabályokat az NSG-k konfigurálása. |
| [Naplózás és fenyegetésészlelés engedélyezése az SQL-kiszolgálókon](security-center-enable-auditing-on-sql-servers.md) |Javasolja, hogy kapcsolja be a naplózás és fenyegetésészlelés az Azure SQL-kiszolgálók. (Csak az azure SQL-szolgáltatás esetében. Nem tartalmazza a virtuális gépeken futó SQL.) |
| [Naplózás és fenyegetésészlelés engedélyezése az SQL-adatbázisokon](security-center-enable-auditing-on-sql-databases.md) |Javasolja, hogy kapcsolja be az Azure SQL Database naplózás és fenyegetésészlelés. (Csak az azure SQL-szolgáltatás esetében. Nem tartalmazza a virtuális gépeken futó SQL.) |
| [Az SQL Database-adatbázisok transzparens adattitkosítás engedélyezése](security-center-enable-transparent-data-encryption.md) |SQL-adatbázisok titkosításának engedélyezését javasolja. (Az azure SQL-szolgáltatás csak.) |
| [Virtuálisgép-ügynök engedélyezése](security-center-enable-vm-agent.md) |Lehetővé teszi a virtuálisgép-ügynök alkalmazását igénylő virtuális gépek megtekintését. A Virtuálisgép-ügynök üzembe helyezése javítás-és alapkonfiguráció-keresés, valamint a kártevőirtó programok, virtuális gépeken kell telepíthető. Az Azure Marketplace-ről üzembe helyezett virtuális gépek esetében a virtuálisgép-ügynök alapértelmezés szerint telepítve van. A virtuálisgép-ügynök telepítéséről a [Virtuális gép-ügynök és -bővítmények – 2. rész](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) cikkben talál információkat. |
| [Lemeztitkosítás alkalmazása](security-center-apply-disk-encryption.md) |Javasolja, hogy végezze el a virtuális gép titkosítását az Azure Disk Encryption használatával (Windows és Linux rendszerű virtuális gépek esetében). A titkosítás elvégzése az operációs rendszer és az adatkötetek esetében egyaránt javasolt a virtuális gépen. |
| [Biztonsági kapcsolattartói adatok megadása](security-center-provide-security-contact-details.md) |Javasolja, hogy biztosítson biztonsági kapcsolattartási adatok minden egyes előfizetés esetében. Kapcsolattartási adatok, e-mail címét és telefonszámát szám. Az adatok kapcsolatba lépni Önnel, ha a biztonsági csoportunk, hogy az erőforrások kerülnek veszélybe. |
| [Operációs rendszer verziójának frissítése](security-center-update-os-version.md) |Javasolja, hogy frissíti az operációs rendszer (OS) verzióját a legújabb elérhető verzió a felhőalapú szolgáltatás az operációsrendszer-család.  Cloud Services kapcsolatos további információkért tekintse meg a [Cloud Services – áttekintés](../cloud-services/cloud-services-choose-me.md). |
| [A sebezhetőségi felmérés nincs telepítve](security-center-vulnerability-assessment-recommendations.md) |Javasolja, hogy telepítsen egy biztonsági rések felmérése szolgáló megoldást a virtuális gépére. |
| [Sebezhetőségek javítása](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Lehetővé teszi a virtuális gépre telepített sebezhetőségfelmérő megoldás által észlelt rendszer- és alkalmazássebezhetőségek megtekintését. |
| [Az Azure Storage-fiók titkosításának engedélyezése](security-center-enable-encryption-for-storage-account.md) | Inaktív adatok Azure Storage szolgáltatás titkosításának engedélyezését javasolja. Storage Service Encryption (SSE) működik, az adatok titkosítása az Azure storage-bA írt, és visszafejti őket a lekérés előtt. SSE jelenleg csak az Azure Blob service érhető el, és a blokkblobok, lapblobok és használható, és hozzáfűző blobok. További tudnivalókért lásd: [inaktív adatok a Storage Service Encryption](../storage/common/storage-service-encryption.md).</br>Az SSE csak Resource Manager-tárfiókok esetében támogatott. |
| [Az adaptív alkalmazások vezérlők engedélyezése](security-center-adaptive-application.md) | Javasolja, hogy alkalmazza-e a Windows virtuális gépeken az adaptív alkalmazásvezérlők. Ez a funkció a Security Center Standard szinten érhető el. A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md). |
| App Service-ben csak elérhetőnek kell lennie HTTPS-kapcsolaton keresztül | Javasolja, hogy korlátozza az App Service hozzáférését a HTTPS-kapcsolaton keresztül csak. |
| Webes szoftvercsatornák le kell tiltani a webes alkalmazás| Keresse fel a Web Sockets websocket webalkalmazásokban használatát javasolja.  A Websocket protokoll téve a különböző típusú biztonsági fenyegetéseket. |
| Egyéni tartományok használata az zabalení Webové Aplikace | Egyéni tartományok közös adathalász támadások és más DNS-sel támadások elleni webalkalmazás használatát javasolja. |
| Webes alkalmazás IP-korlátozások konfigurálása | Javasolja, hogy az alkalmazás-hozzáférést IP-címek listájának meghatározását.  Az IP-korlátozások használatát megvédheti webalkalmazását a gyakori támadásoktól. |
| Ne engedélyezze az összes ("*") erőforrások hozzáférhetnek az alkalmazáshoz | Javasolja, hogy nem WEBSITE_LOAD_CERTIFICATES paraméter értéke "*". A paraméter beállítása "*" azt jelenti, hogy minden tanúsítvány betöltődik a webes alkalmazások személyes tanúsítványtárolójába.  A minimális jogosultság elvével való visszaéléshez ez is vezethet, mert nem valószínű, hogy a hely összes tanúsítvány futásidőben hozzá kell férnie. |
| A CORS nem teszi lehetővé az alkalmazás eléréséhez annak minden erőforrás | Javasolja, hogy lehetővé tegye a kommunikál a webes alkalmazások csak a szükséges tartományok. Közötti eredetű erőforrások megosztása (CORS) kell nem teszi lehetővé minden tartománynak a webalkalmazáshoz való hozzáférés. |
| A legújabb támogatott .NET-keretrendszer-webalkalmazás | A .NET-keretrendszer legújabb verzióját a legújabb biztonsági osztályok használatát javasolja. Régebbi osztályok és típusok használata sérülékennyé teheti az alkalmazását. |
| A Java legújabb támogatott verzióját használja a webalkalmazáshoz | Javasolja, hogy a legújabb Java-verzió esetében a legújabb biztonsági osztályokkal. Régebbi osztályok és típusok használata sérülékennyé teheti az alkalmazását. |
| A PHP legújabb támogatott verzióját használja a webalkalmazáshoz | A PHP legújabb verzióját a legújabb biztonsági osztályok használatát javasolja. Régebbi osztályok és típusok használata sérülékennyé teheti az alkalmazását. |
| [Webalkalmazási tűzfal hozzáadása](security-center-add-web-application-firewall.md) |Javasolja, hogy telepít egy webalkalmazási tűzfal (WAF) webes végpontok. A WAF ajánlás bármely nyilvános IP-Címmel (példány szintű IP vagy Load elosztott terhelésű IP), amely rendelkezik egy társított hálózati biztonsági csoport nyílt bejövő webes porttal (80,443) jelennek meg.</br></br>A Security Center javasolja, hogy a WAF elleni támadásokat a webalkalmazások virtuális gépeken, és az App Service Environment célcsoportkezelés segítségével telepítsen. Van egy App Service Environment (ASE) egy [prémium](https://azure.microsoft.com/pricing/details/app-service/) szolgáltatás, amely az Azure App Service-alkalmazások biztonságos futtatása teljesen elkülönített és dedikált környezetet biztosít az Azure App Service csomag lehetőséget. ASE kapcsolatos további információkért tekintse meg a [App Service Environment dokumentációja](../app-service/environment/intro.md).</br></br>A Security Center több webalkalmazás védheti meg ezeket az alkalmazásokat a meglévő WAF-példányok hozzáadásával. |
| [Alkalmazásvédelem véglegesítése](security-center-add-web-application-firewall.md#finalize-application-protection) |A WAF konfigurációjának befejezéséhez forgalmat a WAF berendezésre át irányítva. Ez a javaslat a következő befejezi a szükséges telepítőfájlokat módosításokat. |
| A legújabb támogatott Node.js verzió használata a webalkalmazáshoz | Javasolja, hogy a legfrissebb Node.js-verzió esetében a legújabb biztonsági osztályokkal. Régebbi osztályok és típusok használata sérülékennyé teheti az alkalmazását. |
| A CORS nem teszi lehetővé a Függvényalkalmazás eléréséhez minden erőforrás | Javasolja, hogy lehetővé tegye a kommunikál a webes alkalmazások csak a szükséges tartományok. Közötti eredetű erőforrások megosztása (CORS) kell nem teszi lehetővé minden tartománynak a függvény-alkalmazás elérésére. |
| Egyéni tartományok használata az Függvényalkalmazás | Egyéni tartományok közös adathalász támadások és más DNS-sel támadások elleni függvényalkalmazás használatát javasolja. |
| Függvényalkalmazás IP-korlátozások konfigurálása | Javasolja, hogy az alkalmazás-hozzáférést IP-címek listájának meghatározását. Az IP-korlátozások használata gyakori támadások ellen védi a függvényalkalmazást. |
| Alkalmazás függvény csak elérhetőnek kell lennie HTTPS-kapcsolaton keresztül | Hozzáférés korlátozásáról függvényalkalmazások HTTPS-kapcsolaton keresztül csak javasolni a felhasználóknak. |
| Távoli hibakeresést ki kell kapcsolni a Függvényalkalmazás | Javasolja, kapcsolja ki a hibakeresési függvényalkalmazás, ha már nincs rá szüksége. Távoli hibakeresés használatához meg kell nyitni a Függvényalkalmazás bejövő portokat. |
| Webes szoftvercsatornák le kell tiltani a Függvényalkalmazás | Javasolja, hogy alaposan tekintse át a Web Sockets funkciót alkalmazásokon belüli használata. A Websocket protokoll téve a különböző típusú biztonsági fenyegetéseket. |
| Az előfizetéshez legalább egy tulajdonos kijelölése | Javasolja, hogy több mint egy előfizetés-tulajdonost kijelölni rendszergazdai hozzáférés redundanciájának biztosításához. |
| Az előfizetés legfeljebb 3 tulajdonos kijelölése | Javasolja, hogy kevesebb mint 3 előfizetés-tulajdonost kijelölni az esetleges illetéktelen behatolás feltört tulajdonosa. |
| Az MFA engedélyezése az előfizetésben tulajdonosi engedélyekkel rendelkező fiókok | A fiókok vagy az erőforrások biztonsági incidenseinek megelőzése rendszergazdai jogosultságokkal rendelkező összes előfizetési fiókban multi-factor Authentication (MFA) engedélyezését javasolja. |
| Az MFA engedélyezése az előfizetés írási jogosultsággal rendelkező fiókjaiban | Javasolja, hogy a multi-factor Authentication (MFA) az a fiókok vagy az erőforrások biztonsági incidenseinek megelőzése írási jogosultsággal rendelkező összes előfizetési fiókban engedélyezni. |
| Az MFA engedélyezése az előfizetés olvasási jogosultsággal rendelkező fiókjaiban | Javasolja a fiókok vagy az erőforrások biztonsági incidenseinek megelőzése olvasási jogosultságokkal rendelkező összes előfizetési fiókban engedélyezni a multi-factor Authentication (MFA). |
| Olvasási engedélyekkel rendelkező külső fiókok eltávolítása az előfizetésből | Javasolja, hogy távolítsa el az olvasási jogosultsággal rendelkező külső fiókok előfizetésből nem monitorozott hozzáférések megelőzése céljából. |
| Írási engedélyekkel rendelkező külső fiókok eltávolítása az előfizetésből | Javasolja, hogy távolítsa el az írási jogosultsággal rendelkező külső fiókok előfizetésből nem monitorozott hozzáférések megelőzése céljából. |
| Tulajdonosi engedélyekkel rendelkező külső fiókok eltávolítása az előfizetésből | Javasolja, hogy távolítsa el tulajdonosi engedélyekkel rendelkező külső fiókok előfizetésből nem monitorozott hozzáférések megelőzése céljából. |
| Elavult fiókok eltávolítása az előfizetésből | Javasolja, hogy eltávolítja az előfizetések közül fiókok elavult. |
| Tulajdonosi engedélyekkel rendelkező elavult fiókok eltávolítása az előfizetésből | Javasolja, hogy eltávolítja az előfizetések a tulajdonosi engedélyekkel rendelkező fiókok elavult. |

### <a name="apply-recommendations"></a>Javaslatok alkalmazása
Összes javaslat áttekintése után döntse el, amely egy alkalmazza először. Azt javasoljuk, hogy használja a besorolásával, mely ajánlások kiértékelheti, hogy a fő paraméter előbb alkalmazni kell.

A fenti ajánlásokat tábla válasszon ki egy javaslatot, és haladjon végig, mint egy példát egy javaslat alkalmazásához.

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megismerhette a Security Center biztonsági javaslatokat. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Center](security-center-azure-policy.md) – ismerje meg, hogyan konfigurálhat biztonsági házirendeket az Azure-előfizetések és -erőforráscsoportok.
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Kezelése és válaszadás a biztonsági riasztásokra az Azure Security Center](security-center-managing-and-responding-alerts.md) – ismerje meg, hogyan kezelése és válaszadás a biztonsági riasztásokra.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
