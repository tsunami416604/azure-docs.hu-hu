---
title: Azure biztonsági és megfelelőségi tervezetének - elemzések a FedRAMP
description: Azure biztonsági és megfelelőségi tervezetének - elemzések a FedRAMP
services: security
author: jomolesk
ms.assetid: b4675715-668e-4557-9c1c-698aabf62ceb
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: db9e49cc4dc02b6864bee2dc4b73ff3c085f5380
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-fedramp"></a>Azure biztonsági és megfelelőségi tervezetének: FedRAMP az elemzés

## <a name="overview"></a>Áttekintés

A [Szövetségi kockázat és engedélyezési felügyeleti Program (FedRAMP)](https://www.fedramp.gov/) egy Egyesült Államok kormánya kiterjedő program, amely a biztonsági értékelést, engedélyezési és állandó felügyelet szabványosított megközelítését ismerteti a felhő termékei és szolgáltatásai. Az Azure biztonsági és megfelelőségi tervezetének útmutatással szolgál hogyan telepíthet egy Microsoft Azure analytics architektúra, amely segít a FedRAMP nagy vezérlők egy részhalmazát valósítja meg. Ez a megoldás a nyújt útmutatást a központi telepítés és az Azure-erőforrások konfigurációs egy közös referencia-architektúrában, módon, ahol az ügyfelek bizonyos biztonsági és megfelelőségi követelményeknek megfelel, amely tartalmazza, és alapjaként szolgál az ügyfelek számára hozza létre, és a saját elemzési megoldásokat konfigurálása az Azure-ban.

A referencia-architektúrában, tartozó vezérlőelem alkalmazási útmutatók és fenyegetés modellek célja, hogy az ügyfelek úgy, hogy az egyedi követelményeket alapjaként szolgálnak, és nem használható mint – egy termelési környezetben. Erre a környezetre módosítás nélkül az alkalmazások központi telepítése nem elegendő teljesen követelményeinek teljesítése érdekében az FedRAMP nagy alaptervhez. Vegye figyelembe a következőket:
- Az architektúra munkaterhelések telepítése az Azure-bA FedRAMP megfelelő módon ügyfelek alapterv.
- Ügyfelek végző megfelelő biztonsági és megfelelőségi értékelése használatával készített ebbe az architektúrába követelmények változhatnak megoldások a részletekről az egyes ügyfelek általi alapján.

## <a name="architecture-diagram-and-components"></a>Architektúrája és összetevői

A megoldás biztosít egy elemzési platformot, amelyre az ügyfelek a saját elemzőeszközök hozhat létre. A referencia-architektúrában ismerteti egy általános használati eset, ahol az ügyfelek bemeneti adatok tömeges adatok importálása az SQL-adatfájl rendszergazda vagy a működési adatok frissítések keresztül egy operatív felhasználó keresztül. Mindkét működik beépítése adatfolyamok [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) az adatok importálása az SQL-adatbázis. Az Azure Functions be kell állítani az egyes ügyfelek saját egyedi importálási feladatok kezelése az Azure portálon keresztül analytics követelményeinek.

A Microsoft Azure számos jelentéskészítés és elemzés szolgáltatások, az ügyfél; azonban ez a megoldás az Azure SQL Database használatával gyorsan tallózzon adatok, és kézbesíti az ügyféladatok intelligensebb modellezési keresztül gyorsabb eredmény együtt Azure Analysis Services magában foglalja. Azure Analytics-szolgáltatásokat a gépi tanulás felderíti az új kapcsolatok adatkészletek között lekérdezés sebesség növelése érdekében javasolt egy formája, amely. Miután az adatok még betanítva keresztül néhány statisztikai függvények, legfeljebb 7 további lekérdezési készletek (beleértve az ügyfél-kiszolgáló teljes 8) szinkronizálhatók terjednek lekérdezés munkaterhelés és a válaszhoz szükséges idő csökkentése a azonos táblázatos modellek.

Továbbfejlesztett elemzési és jelentéskészítési SQL-adatbázisok oszloptárindexekkel konfigurálható. Azure Analytics szolgáltatásokat, mind az SQL-adatbázisok felfelé vagy lefelé méretezhető és ügyfél-használati válaszul teljesen le. Az összes SQL-forgalom azáltal, hogy önaláírt tanúsítványokat SSL van titkosítva. Ajánlott eljárásként Azure azt javasolja, hogy egy megbízható hitelesítésszolgáltató használatát a biztonság növelése.

Amikor adatokat feltölteni az Azure SQL Database és Azure Analysis Services betanítása, a rendszer kivonatolt működési felhasználói mind az SQL-adatfájl Admin a Power BI által. A Power BI intuitív adatait jeleníti meg, és nagyobb betekintés megrajzolásához több adatkészletet között együtt információk lekérése. A magas fokú alkalmazkodás és egyszerű integráció az Azure SQL Database biztosítja, hogy a felhasználók beállíthatják, hogy az üzleti igényeiknek által megkövetelt forgatókönyvek széles köréről kezelni.

A teljes megoldás egy Azure Storage, amelyet fiók-ügyfelek konfigurálása az Azure portálról épül. Az Azure Storage titkosítja az összes adatot a Storage szolgáltatás titkosítási inaktív adatok fenntartásához.  Földrajzi redundancia tárolás (GRS) biztosítja, hogy az ügyfél elsődleges adatközpont káros eseményt nem eredményez az adat, a másodpéldány egy külön helyet több száz miles számítógépnél tárolódnak.

A fokozott biztonság érdekében a ebbe az architektúrába kezeli az Azure Active Directory és az Azure Key Vault-erőforrásokat. Rendszerállapot Operations Management Suite (OMS) és az Azure-figyelő felügyelik. Ügyfelek konfigurálása mindkét figyelési szolgáltatásokat naplók rögzítése, és egyetlen, könnyen hajózható irányítópult rendszerállapot megjelenítéséhez.

Az Azure SQL Database keresztül SQL Server Management Studio (SSMS), az Azure SQL-adatbázist egy biztonságos VPN- vagy ExpressRoute-kapcsolaton keresztül csatlakozik a helyi gépről futtató gyakran kezeli. **Azure azt javasolja, hogy a referencia architektúra erőforrás csoportjához felügyelete és az adatok importálása a VPN- vagy Azure ExpressRoute-kapcsolat beállítása.**

![Elemzések a FedRAMP referencia architektúra diagramja](images/fedramp-analytics-reference-architecture.png?raw=true "elemzések a FedRAMP referencia architektúra diagramja")

### <a name="roles"></a>Szerepkörök
Elemzés szerkezeti terve bemutatja a forgatókönyvet, és általános felhasználói háromféle: a működési felhasználó, az SQL-adatfájl rendszergazda és a rendszer a visszafejtés. Azure szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi, hogy a beépített egyéni szerepkörök keresztül pontos kezelési végrehajtását. Erőforrások elérhetők a konfigurálása [szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) és tagolása és végrehajtási [előre definiált szerepkörök](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

#### <a name="systems-engineer"></a>Rendszerek a visszafejtés
A rendszerek mérnök tulajdonosa az ügyfél-előfizetést, az Azure-ba, és konfigurálja a a megoldás üzembe helyezése az Azure portálon keresztül.

#### <a name="sqldata-administrator"></a>SQL-adatfájl rendszergazda
Az SQL-adatfájl rendszergazda megállapítja a tömeges adatok importálása és a működési adatok frissítés függvény feltöltése az Azure SQL adatbázishoz. Az SQL-adatfájl rendszergazda nem felelős az adatbázisban a működési adatok frissítéseket, de az adatokat a Power BI segítségével megtekintheti lesz.

#### <a name="operational-user"></a>Működési felhasználó
A működési felhasználó rendszeresen frissíti az adatokat, és a mindennapos adatok generálása tulajdonosa. A működési felhasználó is lesz a Power BI eredmények értelmezéséhez.

### <a name="azure-services"></a>Azure-szolgáltatások

A megoldás az Azure-szolgáltatásokat. Az üzembe helyezési architektúrája részleteit szerepelnek a [üzembe helyezési architektúrája](#deployment-architecture) szakasz.
- Azure Functions
- Azure SQL Database
- Az Azure Analysis Service
- Azure Active Directory
- Azure Key Vault
- OMS
- Azure Monitor
- Azure Storage
- Az ExpressRoute és a VPN-átjáró
- Power BI-irányítópult

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája
Az alábbi szakasz részletesen fejlesztése és megvalósítása elemek.

![helyettesítő szöveg](images/fedramp-analytics-components.png?raw=true "Analytics FedRAMP összetevők diagram")

**Az Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) megoldások kisebb kódrészletek a felhőben futó legtöbb programozási nyelv használatával. Ebben a megoldásban funkciók integrálása az Azure Storage automatikusan le tudja ügyféladatok kiterjeszti a felhőbe, más Azure-szolgáltatásokkal való integráció elősegítésére. Funkciók könnyedén méretezhető, és csak fel Önnek a költség, amikor futnak.

**Az Azure Analysis Service**: [Azure Analysis Service](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) biztosít a vállalati adatok modellezési és a az Azure data platform szolgáltatásokkal való integrációt. Az Azure Analysis Service felgyorsítja a nagy mennyiségű adatot keresse meg a különböző forrásokból származó adatok összefésülésére egyetlen adatmodell a által.

**A Power BI**: [Power BI](https://docs.microsoft.com/power-bi/service-azure-and-power-bi) szerinti elemzést biztosít, és jelentéskészítési funkció kívüli adatok feldolgozása érdekében nagyobb insight lekéréses próbál ügyfelek esetén.

### <a name="networking"></a>Hálózat
**Hálózati biztonsági csoportok**: [NSG-k](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) be vannak állítva a telepített erőforrások és szolgáltatások felé irányuló forgalom kezelésére. Hálózati biztonsági csoportok egy megtagadási alapértelmezés szerint séma értékre van beállítva, és csak a forgalom, az az előre beállított hozzáférés-vezérlési lista (ACL) szereplő engedélyezése.

Az NSG-k, amelyek mindegyikének egyedi portokat és protokollokat nyissa meg, hogy a megoldás megfelelően és biztonságosan együttműködhet. Ezenkívül a következő konfigurációk engedélyezve vannak az egyes NSG:
  - [Diagnosztikai naplók és események](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) engedélyezve van, és a storage-fiókban tárolt
  - OMS [Naplóelemzési](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics) csatlakozik-e az NSG diagnosztikai naplókat.

### <a name="data-at-rest"></a>Inaktív adat
Az architektúra történik a titkosítás, az adatbázis naplózását, és egyéb adatok védelmét.

**Adatreplikálás** Azure Government két lehetősége van [adatreplikáció](https://docs.microsoft.com/azure/storage/common/storage-redundancy):
 - Az alapértelmezett beállítás adatok a replikáció **Georedundáns tárolás (GRS)**, amely aszinkron módon tárolja az ügyféladatok kívül az elsődleges régióban külön adatközpontban. Ez biztosítja, hogy az elsődleges adatközpont teljes adatvesztés adatok helyreállítását.
 - **Helyileg redundáns tárolás (LRS)** másik lehetőségként beállítható, hogy az Azure Storage-fiók használatával. LRS replikálja az adatokat egy tárolási méretezési egység, amely ugyanabban a régióban, amelyben a felhasználói fiókjuk létrehoz egy belül. Minden adatait replikálja a rendszer párhuzamosan, győződjön meg arról, hogy a elsődleges skálázási egység hiba nincs biztonsági mentési adatok elvesznek.

**Az Azure Storage** titkosított adatok rest-követelmények teljesítéséhez, az összes szolgáltatás telepítése a referencia architektúra emelés [Azure Storage](https://azure.microsoft.com/services/storage/), amely tárolja az adatokat a [Storage szolgáltatás titkosítási](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Az Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) kihasználja a Windows operációs rendszer és az adatlemezek kötettitkosítást biztosít a BitLocker-szolgáltatás. A megoldás integrálódik az Azure Key Vault szabályozni, és a lemez-titkosítási kulcsok kezeléséhez.

**Az Azure SQL Database** az Azure SQL Database-példányt használja a következő adatbázis biztonsági intézkedéseket:
-   [AD hitelesítési és engedélyezési](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) adatbázis-felhasználók és más Microsoft-szolgáltatásokban egyetlen központi helyen identitás kezelését teszi lehetővé.
-   [SQL-adatbázis naplózásának](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) nyomon követi az adatbázisok események, mind az írás őket naplózási jelentkezzen be Azure storage-fiók.
-   SQL-adatbázis használatára van konfigurálva [átlátszó Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), amely végrehajtja a valós idejű titkosítási és visszafejtési adatainak és naplókönyvtárainak fájlok aktívan információk védelme érdekében. TDE biztosítja, hogy a tárolt adatok nem lett bárki hozzáférhet.
-   [Tűzfal-szabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) tagadni a hozzáférést minden adatbázis-kiszolgálók csak a megfelelő engedélyekkel. A tűzfal biztosítja az adatbázisokhoz való hozzáférést az egyes kérések kiindulási IP-címe alapján.
-   [A Fenyegetésészlelés SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) lehetővé teszi, hogy felderítésére és a lehetséges veszélyforrásokra választ, adja meg a biztonsági riasztások adatbázis gyanús tevékenységek, a potenciális biztonsági réseket, az SQL injektálási támadások és a rendellenes adatbázis-hozzáférési előforduló minták.
-   [Mindig titkosítja az oszlopok](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) győződjön meg arról, hogy bizalmas adatok soha nem jelenik meg az adatbázis rendszerében szövegként. Miután engedélyezte az adattitkosítást, csak az ügyfélalkalmazások vagy a kulcsoknak access app kiszolgálók hozzáférhet egyszerű szöveges adatokat.
-   [SQL-adatbázis dinamikus adatmaszkolási](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) teheti, miután telepíti a referencia-architektúrában. Állítsa be a dinamikus adatmaszkolási beállítások igazodnia kell az adatbázis-séma esetén szükséges.

### <a name="logging-and-audit"></a>Naplózási és naplózása
[Az Azure figyelő](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) hoz létre egy teljes megjelenített figyelési adatok, beleértve a tevékenység naplókat, metrikákat és diagnosztikai adatok engedélyezése a felhasználók hozzanak létre a rendszerállapot átfogó képet.  
[Az Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) rendszer és felhasználói tevékenységek, valamint a rendszerállapot-részletes naplózást végez. Az OMS [Naplóelemzési](https://azure.microsoft.com/services/log-analytics/) megoldás gyűjti és elemzi az adatokat az Azure-erőforrások által létrehozott és a helyszíni környezetben.
- **Tevékenységi naplóit**: [tevékenységi naplóit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) előfizetés erőforrásainak végrehajtott műveletek betekintést nyújtanak.
- **Diagnosztikai naplók**: [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) minden erőforrás által kibocsátott összes naplók tartalmazzák. Ezek a naplók tartalmazzák a Windows rendszer-eseménynaplói és Azure Blob storage táblák és várólista naplókat.
- **Tűzfal-naplók**: az Alkalmazásátjáró biztosít teljes diagnosztikai és a naplók eléréséhez. Alkalmazásátjáró WAF-kompatibilis erőforrások elérhetők tűzfal naplók.
- **Archiválás jelentkezzen**: minden diagnosztikai naplók írni egy központosított és titkosított Azure storage-fiókjához archiválási 2 nap meghatározott megőrzési idővel rendelkező. Ezek a naplók feldolgozása, tárolására és irányítópult reporting Azure Naplóelemzés csatlakozni.

Emellett a következő OMS-megoldások tartoznak ebbe az architektúrába részeként:
-   [Azure Automation szolgáltatásbeli](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): az Azure Automation-megoldás tárolja, fut, és kezeli a runbookok.
-   [Biztonság és a naplózási](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): A biztonság és a naplózási irányítópult erőforrások biztonsági állapotát egy magas szintű betekintést biztosít a biztonsági tartományok, jelentős problémák, észlelések, fenyegetésfelderítési adataival és közös biztonsági lekérdezések metrikák megadásával.
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): megoldás az SQL állapotának ellenőrzése a kockázat és környezetek állapotának értékelésére rendszeres időközönkénti és nyújt a felhasználók számára a telepített kiszolgálói infrastruktúra vonatkozó javaslatok a rangsorolt listáját.
-   [Az Azure tevékenységi naplóit](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A tevékenység Naplóelemzési megoldás segítséget nyújt a elemzése az Azure tevékenységi naplóit az ügyfél az összes Azure-előfizetések között.

### <a name="identity-management"></a>Identitáskezelés
-   Az alkalmazás-hitelesítés az Azure AD használatával történik. További információkért lásd: [alkalmazások integrálása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Emellett az adatbázis oszlop titkosítási segítségével az Azure AD hitelesíti az alkalmazás az Azure SQL Database. További információkért lásd: hogyan [bizalmas adatokat az SQL-adatbázis védelme](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Az Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) észleli a potenciális biztonsági réseket egy szervezet identitásait érintő, konfigurálja az automatikus válaszokat ad egy szervezet identitásait kapcsolódó észlelt gyanús tevékenységek és pedig megvizsgálja a gyanús incidensek a megfelelő művelettel hárítsa el őket.
-   [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) lehetővé teszi, hogy az Azure kezelési kialakításával foglalkozik. Előfizetés hozzáférés korlátozódik előfizetés-rendszergazdaként.

Az Azure SQL Database biztonsági szolgáltatásaival kapcsolatos további tudnivalókért tekintse meg a [Contoso klinikán bemutató alkalmazás](https://github.com/Microsoft/azure-sql-security-sample) minta.

### <a name="security"></a>Biztonság
**Titkos kulcsok kezelése**: A megoldás az [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) kulcsok és titkos kulcsok kezeléséhez. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében.

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok

### <a name="expressroute-and-vpn"></a>ExpressRoute- és VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy biztonságos VPN-alagúton kell úgy, hogy biztonságos kapcsolatot létrehozni a data elemzés referenciaarchitektúra részeként telepített erőforrások. ExpressRoute-kapcsolatok az interneten keresztül halad, mivel ezeket a kapcsolatokat további megbízhatóságát, gyorsabb sebességű, kisebb késések fordulnak elő, és kínálnak nagyobb biztonságot nyújtana tipikus kapcsolatok az interneten keresztül. Beállításával megfelelően expressroute-on vagy VPN-en, az ügyfelek adhat hozzá egy védelmi réteget biztosít adatokat átvitel közben.

### <a name="azure-active-directory-setup"></a>Az Azure Active Directory beállítása
[Az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) fontos a központi telepítés kezelése és a kiépítés a környezetben való interakció csoporthoz való hozzáférés. Meglévő Windows Server Active Directory integrálható az AAD-ben [négy kattintással](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Az ügyfelek is nagy terhelést jelent a telepített Active Directory-infrastruktúrát (tartományvezérlők) egy meglévő aad-ben való azáltal, hogy a telepített Active Directory-infrastruktúra altartománya: az AAD-erdőt.

### <a name="additional-services"></a>További szolgáltatások
#### <a name="iaas---vm-vonsiderations"></a>IaaS - VM vonsiderations
A PaaS megoldás nem foglalja magában bármely Azure IaaS virtuális gépeket. Az ügyfél létrehozhat egy Azure virtuális Gépen nagy része a PaaS szolgáltatások futtatásához. Ebben az esetben a szolgáltatások és az üzletmenet folytonosságának és az OMS-szolgáltatások sikerült javítható:

##### <a name="business-continuity"></a>Az üzletmenet folytonossága
- **Magas rendelkezésre állású**: kiszolgáló-munkaterhelések vannak csoportosítva egy [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) az Azure virtuális gépek magas rendelkezésre állásának biztosításához. Legalább egy virtuális gép érhető el egy tervezett vagy nem tervezett karbantartási események esetén a 99,95 % értekezlet Azure SLA-t.

- **Recovery Services-tároló**: A [Recovery Services-tároló](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) Kezelőkód biztonsági mentési adatokat, és az összes konfiguráció Azure virtuális gépek ebben az architektúrában védelmét. A Recovery Services-tároló, az ügyfelek is fájlok és mappák visszaállítása egy infrastruktúra-szolgáltatási virtuális gép helyreállítása a teljes virtuális gép, gyorsabb visszaállítás engedélyezése nélkül.

##### <a name="oms"></a>OMS
-   [AD Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): az Active Directory állapotát ellenőrző megoldás a kockázat és környezetek állapotának értékelésére rendszeres időközönkénti, és a telepített kiszolgálói infrastruktúra vonatkozó javaslatok a rangsorolt listáját tartalmazza.
-   [Kártevőirtó Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): A kártevőirtó megoldást jelent a kártevő szoftver, a fenyegetések és a védelmi állapot.
-   [Frissítéskezelés](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): A frissítés felügyeleti megoldás lehetővé teszi az operációs rendszer biztonsági frissítések, a rendelkezésre álló frissítések állapot és a kötelező frissítések telepítésének folyamatát, például felhasználói kezelését.
-   [Ügynök állapotfigyelő](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): az ügyfélállapot-megoldást jelent, hány ügynök van telepítve, és a földrajzi eloszlása, valamint hány ügynökök, amelyek nem válaszoló és ügynökök, amelyek a működési adatok elküldése.
-   [A változáskövetés](https://docs.microsoft.com/azure/automation/automation-change-tracking): A változáskövetési megoldás lehetővé teszi az ügyfelek azonosítását a változtatásokat a környezetben.

##### <a name="security"></a>Biztonság
- **Kártevők elleni védekezés**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) virtuális gépek számára lehetővé teszi a valós idejű védelem segítségével azonosíthatja, és távolítsa el a vírusok, kémprogramok és más, kártevő szoftverek, konfigurálható a riasztások Ha ismert kártevő vagy nemkívánatos szoftverek próbálják telepíteni futtassa a védett virtuális gépet.
- **Felügyeleti javítás**: Windows virtuális gépek telepítése a referencia-architektúrában részeként az automatikus frissítések kap a Windows Update szolgáltatás alapértelmezés szerint vannak konfigurálva. Ez a megoldás is magában foglalja az OMS [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) szolgáltatás, amelyen keresztül a frissített telepítések is létrehozható a következő virtuális gépek szükség esetén.

#### <a name="azure-commercial"></a>Az Azure kereskedelmi
Bár az adatok analytics architektúrájának nem szánt központi telepítést, hogy a [Azure kereskedelmi](https://azure.microsoft.com/overview/what-is-azure/) környezet, hasonló célok érhető el a referencia-architektúrában, és a szolgáltatások, valamint További szolgáltatások csak az Azure kereskedelmi környezetben elérhető. Vegye figyelembe, hogy Azure kereskedelmi tart fenn a FedRAMP JAB P-ATO szinten mérsékelt hatása, állami intézményekhez és a partnerek számára, hogy telepíti a felhőbe, az Azure kereskedelmi környezetben, ami mérsékelten bizalmas adatokat.

Az Azure kereskedelmi jogosultak-e nagy mennyiségű adat kívül insights analytics szolgáltatások számos kínál:
-   [Az Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio), egy összetevőjét a [Cortana Intelligence Suite](https://azure.microsoft.com/overview/ai-platform/), házon belül fejlesztett alkalmazásokra egy prediktív elemzési modell egy vagy több adatforrásokból. Statisztikai függvények alkalmazásokhoz, mint a Power BI tudják felhasználni egy hatékony modell létrehozásához használt több ismétlési keresztül.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) lehetővé teszi, hogy a műveleti és felderítési jellegű egyetlen helyen bármilyen méretű, típusú és feldolgozási sebességű adatok rögzítését. Azure Data Lake Store a Hadoop-ökoszisztéma legtöbb nyílt forráskódú összetevőjével kompatibilis, és más Azure-szolgáltatásokkal is jól integrálható.

## <a name="threat-model"></a>Fenyegetések modellezése

Az adatok folyamatábrája (DFD) a referenciaarchitektúra érhető el [letöltése](https://aka.ms/blueprintanalyticsthreatmodel) vagy alatt található:

## <a name="compliance-documentation"></a>Megfelelőségi dokumentáció
A [Azure biztonsági és megfelelőségi tervezetének – FedRAMP magas ügyfél felelősségi mátrix](https://aka.ms/blueprinthighcrm) sorolja fel a FedRAMP nagy alapkonfiguráció által igényelt összes biztonsági vezérlő. Hasonlóképpen a [Azure biztonsági és megfelelőségi tervezetének – FedRAMP mérsékelt ügyfél felelősségi mátrix](https://aka.ms/blueprintanalyticscimmod) sorolja fel a FedRAMP mérsékelt alapkonfiguráció által igényelt összes biztonsági vezérlő. A dokumentumok részletességi, hogy minden vezérlő végrehajtásának feladata a Microsoft, az ügyfél vagy a kettő között megosztott.

A [Azure biztonsági és megfelelőségi tervezetének - FedRAMP magas vezérlés megvalósítása mátrix](https://aka.ms/blueprintanalyticscimhigh) és a [Azure biztonsági és megfelelőségi tervezetének - FedRAMP mérsékelt vezérlés megvalósítása mátrix](https://aka.ms/blueprintanalyticscimmod) adja meg Amikor vezérlők hatálya FedRAMP minden egyes referenciakonfigurációnál, beleértve a részletes leírását, hogyan végrehajtása megfelel minden érintett vezérlő analytics architektúrájának adatokat.

## <a name="disclaimer"></a>Jogi nyilatkozat

 - Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT NEM VÁLLAL SEMMILYEN KIFEJEZETT, KIFEJEZETT, VÉLELMEZETT VAGY TÖRVÉNYES, HOGY A JELEN DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓK. Ez a dokumentum biztosított ",-van." Információk és nézetek ebben a dokumentumban, beleértve az URL-CÍMEK és más internetes webhelyet, értesítés nélkül változhatnak. Ez a dokumentum olvasásakor az ügyfelek az alkalmazást saját felelősségére használja.
 - Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft-termék vagy a megoldások található szellemi tulajdonhoz rendelkező ügyfelek.
 - Az ügyfelek azonban másolható és ez a dokumentum belső hivatkozási célokra használja.
 - Bizonyos ajánlások a dokumentum azt eredményezheti, megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és megnövelheti az ügyfél Azure licencek vagy előfizetések költségeit.
 - Ebbe az architektúrába célja, hogy az ügyfelek úgy, hogy az egyedi követelményeket alapjaként szolgálnak, és nem használható mint – egy termelési környezetben.
 - Ez a dokumentum hivatkozásként fejlesztése, és nem használható minden olyan eszközt, amely szerint az ügyfél megfelel adott megfelelőségi követelmények és szabályok meghatározásához. Az ügyfelek jóváhagyott ügyfél-hitelesítés megvalósításához a szervezetek jogi támogatnia kell kérnie.
