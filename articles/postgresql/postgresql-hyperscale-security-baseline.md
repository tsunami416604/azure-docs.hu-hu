---
title: Azure biztonsági alapkonfiguráció a Azure Database for PostgreSQL-nagy kapacitású
description: Az Azure Database for PostgreSQL-nagy kapacitású biztonsági alapkonfiguráció eljárási útmutatást és erőforrásokat biztosít az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: dd6d3ef6f610dcbdc511297e459cc5856bebe033
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87030045"
---
# <a name="azure-security-baseline-for-azure-database-for-postgresql---hyperscale"></a>Azure biztonsági alapkonfiguráció a Azure Database for PostgreSQL-nagy kapacitású

A Azure Database for PostgreSQL-nagy kapacitású Azure biztonsági alapterve olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](https://docs.microsoft.com/azure/security/benchmarks/overview)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével.

További információ: [Azure Security](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)alapkonfigurációk áttekintése.

## <a name="network-security"></a>Hálózati biztonság

*További információkért lásd [: biztonsági ellenőrzés: hálózati biztonság](/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: Azure Database for PostgreSQL kiszolgáló tűzfala megakadályozza a nagy kapacitású (Citus) koordinátor-csomóponthoz való hozzáférést, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. A tűzfal az egyes kérések származó IP-címe alapján engedélyezi a hozzáférést a kiszolgálóhoz. A tűzfal konfigurálásakor olyan tűzfalszabályokat adhat meg, amelyek meghatározzák az elfogadható IP-címtartományokat. A tűzfalszabályok a kiszolgáló szintjén hozhatók létre.

* [Tűzfalszabályok konfigurálása a Azure Database for PostgreSQL-nagy kapacitású (Citus)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-firewall-rules)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: nem alkalmazható; Azure Database for PostgreSQL-nagy kapacitású (Citus) nem társítható virtuális hálózathoz, alhálózathoz vagy hálózati biztonsági csoporthoz.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: nem alkalmazható; a Azure Database for PostgreSQL-nagy kapacitású (Citus) által használt végpontokat a Microsoft felügyeli. Ön felelős minden olyan további ellenőrzésért, amelyet telepíteni kíván a helyszíni rendszerekre.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: nem alkalmazható; Azure Database for PostgreSQL-nagy kapacitású (Citus) nem társítható virtuális hálózathoz, alhálózathoz vagy hálózati biztonsági csoporthoz.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: nem alkalmazható; a Azure Database for PostgreSQL-nagy kapacitású (Citus) által használt végpontokat a Microsoft felügyeli. Ön felelős minden olyan további ellenőrzésért, amelyet telepíteni kíván a helyszíni rendszerekre.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: nem alkalmazható; Azure Database for PostgreSQL-nagy kapacitású (Citus) nem társítható virtuális hálózathoz, alhálózathoz vagy hálózati biztonsági csoporthoz.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: szabványos biztonsági konfigurációk definiálása és implementálása a Azure Database for PostgreSQL-példányokhoz társított hálózati beállításokhoz és hálózati erőforrásokhoz Azure Policy. Használjon Azure Policy aliasokat a "Microsoft. Network" névtérben egyéni szabályzatok létrehozásához a Azure Database for PostgreSQL példányok hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez.

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy minták a hálózatkezeléshez](https://docs.microsoft.com/azure/governance/policy/samples/#network)

* [Azure Blueprint létrehozása](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: nem alkalmazható; Azure Database for PostgreSQL-nagy kapacitású (Citus) nem társítható virtuális hálózathoz, alhálózathoz vagy hálózati biztonsági csoporthoz.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: nem alkalmazható; Azure Database for PostgreSQL-nagy kapacitású (Citus) nem társítható virtuális hálózathoz, alhálózathoz vagy hálózati biztonsági csoporthoz.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

*További információ [: Security Control: naplózás és figyelés](/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: a Microsoft fenntartja az Azure-erőforrásokhoz használt időforrást, például az Azure Database for PostgreSQL-nagy kapacitású (Citus) a naplókban található időbélyegek esetében.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: a vezérlési sík naplózásához engedélyezze az Azure-tevékenység naplójának diagnosztikai beállításait, és küldje el a naplókat egy log Analytics munkaterületre, az Azure Event hub vagy az Azure Storage-fiókba az archívumhoz. Az Azure-tevékenység naplójának adatai alapján meghatározhatja az Azure-erőforrások vezérlési síkja szintjén végrehajtott írási műveletek (PUT, közzététel, törlés) esetében a "mi, ki és mikor" lehetőséget.

A nagy kapacitású (Citus) által generált biztonsági adatokat összesítve az Azure Monitor segítségével is betöltheti a naplókat. A Azure Monitoron belül Log Analytics munkaterülettel kérdezheti le és végezheti el az elemzéseket, és használhat tárolási fiókokat a hosszú távú/archiválási tároláshoz. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó biztonsági incidensek és események felügyeletét (SIEM).

* [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Metrikák a nagy kapacitású-ben (Citus)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-monitoring)

* [Az Azure Sentinel előkészítése](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a nagy kapacitású (Citus) metrikákat biztosít a kiszolgálócsoport egyes csomópontjaihoz. A metrikák betekintést nyújtanak a támogatási erőforrások viselkedésére. Minden metrika egy egyperces gyakorisággal van kibocsátva, és akár 30 napig is eltarthat.

A vezérlési sík naplózásához engedélyezze az Azure-műveletnapló diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics munkaterületre, az Azure Event hub vagy az Azure Storage-fiókba az archívumhoz. Az Azure-tevékenység naplójának adatai alapján meghatározhatja az Azure-erőforrások vezérlési síkja szintjén végrehajtott írási műveletek (PUT, közzététel, törlés) esetében a "mi, ki és mikor" lehetőséget.

A nagy kapacitású (Citus) által generált biztonsági adatokat összesítve az Azure Monitor segítségével is betöltheti a naplókat. A Azure Monitoron belül Log Analytics munkaterülettel kérdezheti le és végezheti el az elemzéseket, és használhat tárolási fiókokat a hosszú távú/archiválási tároláshoz. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó biztonsági incidensek és események felügyeletét (SIEM).

* [Metrikák a nagy kapacitású-ben (Citus)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-monitoring)

* [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Az Azure Sentinel előkészítése](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: Azure monitor belül, a nagy kapacitású-(Citus-) naplók tárolására szolgáló log Analytics munkaterület esetében állítsa be a megőrzési időszakot a szervezet megfelelőségi szabályainak megfelelően. Használja az Azure Storage-fiókokat hosszú távú/archiválási tároláshoz.

* [Log Analytics-munkaterületek naplózási megőrzési paramétereinek beállítása](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [Erőforrás-naplók tárolása Azure Storage-fiókban](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a rendellenes viselkedést a nagy kapacitású-(Citus-) példányok naplófájljainak elemzése és figyelése. A naplók áttekintéséhez és a naplózási adatok lekérdezéséhez használja a Azure Monitor Log Analytics. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et.

* [Az Azure Sentinel előkészítése](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [További információ a Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Egyéni lekérdezések végrehajtása a Azure Monitorban](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: engedélyezheti a nagy kapacitású (Citus) diagnosztikai beállításait, és naplókat küldhet egy log Analytics munkaterületre. Az Azure-szolgáltatások metrikáinak monitorozása alapján beállíthat és fogadhat riasztásokat. A naplók áttekintéséhez és a naplózási adatok lekérdezéséhez használja a Azure Monitor Log Analytics. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et.

A Log Analytics-munkaterületet az Azure Sentinelbe irányíthatja, mivel ez egy biztonsági előkészítési automatizált választ (felszárnyaló) megoldást biztosít. Ez lehetővé teszi a forgatókönyvek (automatizált megoldások) létrehozását és a biztonsági problémák megoldására való felhasználását.

* [Metrikák a nagy kapacitású-ben (Citus)](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-alert-on-metric)

* [Az Azure-tevékenység naplójának diagnosztikai beállításainak konfigurálása](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Az Azure Sentinel előkészítése](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: nem alkalmazható; A Azure Database for PostgreSQL-nagy kapacitású (Citus) nem dolgoz fel és nem hoz létre kártevő-ellenes kapcsolódó naplókat.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: nem alkalmazható; A Azure Database for PostgreSQL nem dolgozza fel a DNS-sel kapcsolatos naplókat, és nem hoz létre.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információkért lásd: a [biztonság szabályozása: identitás-és hozzáférés-vezérlés](/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: az nagy kapacitású-(Citus-) példányok vezérlési síkjával (például Azure Portal) rendszergazdai hozzáféréssel rendelkező felhasználói fiókok leltárának fenntartása. Emellett a nagy kapacitású-(Citus-) példányok adatsíkjával (a saját adatbázisán belül) hozzáférő rendszergazdai fiókok leltárát is őrizze meg.

A nagy kapacitású (Citus) nem támogatja a beépített szerepköralapú hozzáférés-vezérlést, de adott erőforrás-szolgáltatói műveletek alapján egyéni szerepköröket is létrehozhat.

Emellett a PostgreSQL-motor szerepköröket használ az adatbázis-objektumokhoz való hozzáférés szabályozására, az újonnan létrehozott nagy kapacitású-(Citus-) kiszolgálócsoport pedig előre definiált számos szerepkört tartalmaz. A felhasználói jogosultságok módosításához használja a standard PostgreSQL-parancsokat egy olyan eszköz használatával, mint például a PgAdmin vagy a psql.

* [Az Azure-előfizetés egyéni szerepköreinek megismerése](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)

* [Azure Database for PostgreSQL erőforrás-szolgáltatói műveletek ismertetése](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbforpostgresql)

* [A Azure Database for PostgreSQL hozzáférés-kezelésének megismerése](https://docs.microsoft.com/azure/postgresql/concepts-security#access-management])

* [Felhasználók létrehozása a Azure Database for PostgreSQL-nagy kapacitású (Citus)](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users)

* [Kapcsolódás a PostgreSQL-nagy kapacitású (Citus) szolgáltatáshoz a psql használatával](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal#connect-to-the-database-using-psql)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: az Azure ad nem rendelkezik az alapértelmezett jelszavak fogalmával. Más Azure-erőforrások, amelyek jelszó megadását igénylik a bonyolultsági követelményekkel és a jelszó minimális hosszával hozhatók létre, és a szolgáltatástól függően eltérőek. Ön felelős harmadik féltől származó alkalmazásokért és piactér-szolgáltatásért, amelyek az alapértelmezett jelszavakat használhatják.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos működési eljárások létrehozása a nagy kapacitású-(Citus-) példányokhoz való hozzáféréshez használt dedikált rendszergazdai fiókok használatával. Az Azure-erőforrás kezeléséhez szükséges rendszergazdai fiókok Azure Active Directoryhoz vannak kötve, léteznek helyi kiszolgálói rendszergazdai fiókok is, amelyek az adatbázis-hozzáférési engedélyek kezelésére szolgáló nagy kapacitású-(Citus-) kiszolgálócsoport-csoporton belül találhatók. Azure Security Center identitás-és hozzáférés-kezelési szolgáltatással figyelheti a Azure Active Directoryon belüli rendszergazdai fiókok számát.

* [Azure Security Center identitás és hozzáférés ismertetése](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Felhasználók létrehozása a Azure Database for PostgreSQL-nagy kapacitású (Citus)](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutatás**: az Azure Database for PostgreSQL-nagy kapacitású (Citus) szolgáltatásba való bejelentkezés támogatott a közvetlenül az adatbázisban konfigurált Felhasználónév/jelszó használatával, valamint egy Azure Active Directory (ad) identitás használatával, valamint egy Azure ad-jogkivonat a kapcsolódáshoz való felhasználásával. Azure AD-token használatakor a rendszer különböző módszereket támogat, például egy Azure AD-felhasználót, egy Azure AD-csoportot vagy egy, az adatbázishoz csatlakozó Azure AD-alkalmazást.

* [Azure Active Directory használata a hitelesítéshez Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: a Azure Active Directory multi-Factor Authentication (MFA) engedélyezése és a Azure Security Center identitás-és hozzáférés-kezelési javaslatok követése. Ha Azure AD-jogkivonatokat használ az adatbázisba való bejelentkezéshez, ez lehetővé teszi a többtényezős hitelesítés megkövetelését az adatbázis-bejelentkezésekhez.

* [Az MFA engedélyezése az Azure-ban](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Azure Active Directory használata a hitelesítéshez Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication)

* [Identitás és hozzáférés figyelése Azure Security Centeron belül](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: az Azure-erőforrások bevezetésére és konfigurálására konfigurált, multi-Factor Authentication (MFA) rendszerjogosultságú hozzáférési munkaállomások használata.

* [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Az MFA engedélyezése az Azure-ban](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="37-alert-on-account-login-behavior-deviation"></a>3,7: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutatás**: Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik.

Az Azure AD-kockázati észlelések használatával a kockázatos felhasználói viselkedésre vonatkozó riasztásokat és jelentéseket tekinthet meg.

* [Privileged Identity Management (PIM) üzembe helyezése](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [Az Azure AD kockázati észlelések ismertetése](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel lehetővé teheti a portál és Azure Resource Manager hozzáférését az IP-címtartományok vagy országok/régiók adott logikai csoportjaiból.

* [Elnevezett helyszínek konfigurálása az Azure-ban](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

Az Azure AD hitelesítő adatai a felügyeleti sík szintjén (például Azure Portal) is használhatók a PostgreSQL rendszergazdai fiókok vezérléséhez.

* [Azure Active Directory használata a hitelesítéshez Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a hozzáférés áttekintése és összehangolása mindkét felhasználó számára, akik hozzáféréssel rendelkeznek a helyi adatbázishoz, valamint Azure Active Directoryon keresztül.

Tekintse át a Azure Active Directory (AD) naplókat az elavult fiókok felderítéséhez, amelyek magukban foglalhatják a nagy kapacitású (Citus) rendszergazdai szerepkörrel rendelkezőket. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelhetők a csoporttagságok, hozzáférhetnek a vállalati alkalmazásokhoz, amelyek a nagy kapacitású (Citus) és a szerepkör-hozzárendelések elérésére használhatók. A felhasználók hozzáférését rendszeresen felül kell vizsgálni, például 90 naponta, hogy csak a megfelelő felhasználók férhessenek hozzájuk.

* [A PostgreSQL-felhasználók és a hozzárendelt szerepkörök áttekintése](https://www.postgresql.org/docs/current/database-roles.html)

* [Az Azure AD jelentéskészítés ismertetése](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Az Azure Identity hozzáférési felülvizsgálatok használata](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: a Azure Active Directory (ad) szolgáltatásban hozzáférhet az Azure ad bejelentkezési tevékenységeihez, a naplózási és a kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik bármely Siem/monitoring eszköz integrálását.

Ezt a folyamatot leegyszerűsítheti Azure Active Directory felhasználói fiókok diagnosztikai beállításainak létrehozásával, valamint a naplók és a bejelentkezési naplók Log Analytics munkaterületre való elküldésével. Log Analytics munkaterületen belül konfigurálhatja a kívánt riasztásokat.

* [Azure-beli tevékenység-naplók integrálása a Azure Monitorba](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a Azure Active Directory identitás-védelmi és kockázati észlelési funkciói segítségével konfigurálhatja az Azure Active Directory (ad) szintjén észlelt gyanús műveletekre vonatkozó automatizált válaszokat. A szervezet biztonsági válaszainak megvalósításához az Azure Sentinel használatával engedélyezheti az automatikus válaszokat.

További vizsgálat céljából betöltheti a naplókat az Azure Sentinelbe.

* [A Azure AD Identity Protection áttekintése](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)

* [Az Azure AD kockázatos bejelentkezések megtekintése](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

* [Az Azure Sentinel előkészítése](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: jelenleg nem érhető el; A Ügyfélszéf még nem támogatott a nagy kapacitású (Citus) esetében.

* [Ügyfélszéf támogatott szolgáltatások listája](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: jelenleg nem érhető el

## <a name="data-protection"></a>Adatvédelem

*További információkért lásd [: biztonsági ellenőrzés:](/azure/security/benchmarks/security-control-data-protection)adatvédelem.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítséget nyújthat a nagy kapacitású-(Citus-) példányok és a bizalmas adatokat tároló vagy feldolgozó kapcsolódó erőforrások nyomon követésében.

* [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. A rendszergazdai szerepkörök és tűzfalszabályok együttes használatával elkülönítheti és korlátozhatja a Azure Database for PostgreSQL-példányok hálózati hozzáférését.

* [További Azure-előfizetések létrehozása](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Management Groups létrehozása](https://docs.microsoft.com/azure/governance/management-groups/create)

* [A Tűzfalszabályok ismertetése a Azure Database for PostgreSQL-nagy kapacitású (Citus)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-firewall-rules)

* [A nagy kapacitású szerepköreinek megismerése (Citus)](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: a Microsoft kezeli a Azure Database for PostgreSQL-nagy kapacitású (Citus) alapul szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy expozíciójának megakadályozására.

* [Az ügyfelek adatvédelmének megismerése az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: az nagy kapacitású (Citus) koordinátori csomóponthoz való ügyfélalkalmazás-kapcsolathoz TRANSPORT Layer Security (TLS) 1,2 szükséges. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti TLS-kapcsolatok érvényesítésével megvédheti a védelmet a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával.

A Azure Portal által kiépített összes Azure Database for PostgreSQL-kiszolgáló esetében a TLS-kapcsolatok kényszerítése alapértelmezés szerint engedélyezve van.

Bizonyos esetekben a harmadik féltől származó alkalmazásoknak egy megbízható hitelesítésszolgáltatói (CA) tanúsítványfájl (. cer) alapján létrehozott helyi tanúsítványfájl szükségesek a biztonságos kapcsolódáshoz.

* [A TLS konfigurálása Azure Database for PostgreSQL-nagy kapacitású (Citus)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-ssl-connection-security)

* [A TLS-kapcsolat tanúsítvány-ellenőrzését igénylő alkalmazások](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-ssl-connection-security)

**Azure Security Center figyelés**: igen

**Felelősség**: megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el a nagy kapacitású (Citus) számára.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

* [Az ügyfelek adatvédelmének megismerése az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozásához

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (RBAC) használata a nagy kapacitású (Citus) vezérlési síkja elérésének vezérléséhez (például Azure Portal). A RBAC nem befolyásolja a felhasználói engedélyeket az adatbázison belül.

Ha az adatbázis szintjén szeretné módosítani a felhasználói jogosultságokat, használja a standard PostgreSQL-parancsokat egy olyan eszköz használatával, mint például a PgAdmin vagy a psql.

* [A RBAC konfigurálása az Azure-ban](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

* [A felhasználói hozzáférés konfigurálása az SQL-sel Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

A Microsoft kezeli a nagy kapacitású alapul szolgáló infrastruktúrát (Citus), és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy expozíciójának megakadályozására.

* [Az ügyfelek adatvédelmének megismerése az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: naponta legalább egyszer Azure Database for PostgreSQL nagy kapacitású (Citus) az adatfájlok és az adatbázis-tranzakciós napló pillanatképes biztonsági másolatait veszi figyelembe. A biztonsági mentések lehetővé teszik a kiszolgálók bármely időpontra történő visszaállítását a megőrzési időtartamon belül. (A megőrzési időtartam jelenleg 35 nap az összes fürthöz.) Az összes biztonsági mentés titkosítása AES 256 bites titkosítás használatával történik. A PostgreSQL nagy kapacitású-(Citus-) ajánlat a Microsoft által felügyelt kulcsokat használja a titkosításhoz.

* [Az Azure PostgreSQL-nagy kapacitású (Citus) biztonsági mentések titkosításának ismertetése](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a nagy kapacitású (Citus) és más kritikus vagy kapcsolódó erőforrások éles példányaira vonatkoznak.

* [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ [: Security Control: sebezhetőségi kezelés](/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: jelenleg nem érhető el; A Azure Security Center még nem támogatja a Azure Database for PostgreSQL-nagy kapacitású (Citus) sebezhetőségi felmérését.

* [Az Azure Pásti szolgáltatásainak lefedettsége Azure Security Center](https://docs.microsoft.com/azure/security-center/features-paas)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: jelenleg nem érhető el

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: a Microsoft által felügyelt mögöttes platformhoz a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

* [Az ügyfelek adatvédelmének megismerése az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: a harmadik féltől származó szoftverek címeihez készült automatizált javítási megoldás üzembe helyezése

**Útmutató**: a Microsoft által felügyelt mögöttes platformhoz a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

* [Az ügyfelek adatvédelmének megismerése az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: a Microsoft által felügyelt mögöttes platformhoz a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

* [Az ügyfelek adatvédelmének megismerése az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: a Microsoft a sebezhetőségek kezelését a Azure Database for PostgreSQL-nagy kapacitású (Citus) támogató mögöttes rendszereken hajtja végre. Az adatbázis-fürt és a kapcsolódó erőforrások konfigurálásával kapcsolatos biztonsági rések esetén használjon egy közös kockázati pontozási programot (például a biztonsági rések pontozási rendszerét), vagy a harmadik féltől származó ellenőrzési eszköz által biztosított alapértelmezett kockázati minősítéseket.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet

*További információkért lásd [: biztonsági vezérlés: leltár és eszközkezelés](/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az összes erőforrást (beleértve a nagy kapacitású-(Citus-) példányokat) az előfizetésében. Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.

* [Lekérdezések létrehozása az Azure Graph használatával](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Azure-előfizetések megtekintése](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Az Azure RBAC ismertetése](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat a nagy kapacitású (Citus) példányokra és az egyéb kapcsolódó erőforrásokra, amelyek lehetővé teszik a metaadatok logikai rendszerezését a rendszertanban.

* [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és a különálló előfizetések használata, ahol szükséges, a nagy kapacitású-(Citus-) példányok és a kapcsolódó erőforrások rendszerezése és nyomon követése. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

* [További Azure-előfizetések létrehozása](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Management Groups létrehozása](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: az Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetésben (k) belüli erőforrásokat.

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Lekérdezések létrehozása az Azure Graph használatával](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: az Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetésben (k) belüli erőforrásokat.

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Lekérdezések létrehozása az Azure Graph használatával](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások és az Azure egészének fedezésére szolgál.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: az Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Adott erőforrástípus megtagadása a következővel Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek a Azure Resource Manager interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával. Ez megakadályozhatja az erőforrások létrehozását és módosítását a magas biztonsági környezetben, például a nagy kapacitású (Citus) olyan példányain, amelyek bizalmas információkat tartalmaznak.

* [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információkért lásd [: biztonság-vezérlés: biztonságos konfiguráció](/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: szabványos biztonsági konfigurációk definiálása és implementálása a nagy kapacitású-(Citus-) példányokhoz Azure Policy használatával. A Azure Policy használatával egyéni házirendeket hozhat létre a Azure Database for PostgreSQL példányok hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez.

Emellett Azure Resource Manager lehetősége van a sablon exportálására JavaScript Object Notation (JSON), amelyet át kell tekinteni, hogy a konfigurációk megfeleljenek/túllépik a szervezete biztonsági követelményeit.

* [Az elérhető Azure Policy aliasok megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Egy-és többerőforrásos exportálás Azure Portal sablonba](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure Policy [megtagadás] és a [telepítés, ha nem létezik] az Azure-erőforrások biztonságos beállításainak betartatására használható. Emellett Azure Resource Manager-sablonok használatával is megőrizheti a szervezete által igényelt Azure-erőforrások biztonsági konfigurációját.

* [Azure Policy effektusok ismertetése](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

* [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Resource Manager sablonok áttekintése](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: Ha egyéni Azure-szabályzat-definíciókat használ a nagy kapacitású-(Citus-) példányokhoz és a kapcsolódó erőforrásokhoz, az Azure Repos segítségével biztonságosan tárolhatja és kezelheti a kódot.

* [Kód tárolása az Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Az Azure Repos dokumentációja](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: az Azure Policy [megtagadás] és a [telepítés, ha nem létezik] az Azure-erőforrások biztonságos beállításainak betartatására használható. Emellett Azure Resource Manager-sablonok használatával is megőrizheti a szervezete által igényelt Azure-erőforrások biztonsági konfigurációját.

* [Azure Policy effektusok ismertetése](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

* [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Resource Manager sablonok áttekintése](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: konfigurációs felügyeleti eszközök telepítése operációs rendszerekhez

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. DBforPostgreSQL" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. A Azure Database for PostgreSQL példányok és a kapcsolódó erőforrások konfigurációjának automatikus érvényesítéséhez használja az Azure Policy [audit], [megtagadás] és [üzembe helyezés ha nem létezik] beállítást.

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: nem alkalmazható; Azure Key Vault használatával biztonságosan kezelheti az adatbázis-rendszergazdai jelszavát. Rendeljen hozzá megfelelő hatókörű, a Key Vault titkos kulcshoz tartozó jogosultságokat az adatbázis jelszavához való hozzáférés korlátozásához. Ne tárolja a kódokhoz tartozó adattárakat vagy adatbázis-titkokat.

* [Kapcsolódás a PostgreSQL-adatbázishoz](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal#connect-to-the-database-using-psql)

* [Key Vault létrehozása](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

* [Key Vault hitelesítés biztosítása felügyelt identitással](https://docs.microsoft.com/azure/key-vault/managed-identity)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: a Azure Database for PostgreSQL-kiszolgáló támogatja az adatbázisok elérését Azure Active Directory hitelesítéshez. A Azure Database for PostgreSQL-kiszolgáló létrehozásakor meg kell adnia egy rendszergazdai felhasználó hitelesítő adatait. Ennek a rendszergazdának a segítségével további adatbázis-felhasználók hozhatók létre.

A Azure Database for PostgreSQL-kiszolgáló elérésére használt Azure App Service-on futó Azure-Virtual Machines vagy webalkalmazások esetén a Managed Service Identity a Azure Key Vault a Azure Database for PostgreSQL kiszolgáló hitelesítő adatainak tárolására és lekérésére használható. Győződjön meg arról, Key Vault a Soft delete engedélyezve van.

Felügyelt identitások használatával biztosíthatja az Azure-szolgáltatások automatikus felügyelt identitását Azure Active Directoryban (AD). A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

* [Felügyelt identitások konfigurálása](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

* [Integráció az Azure felügyelt identitásokkal](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: jelenleg nem érhető el

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A hitelesítő adatok beolvasása azt is javasolja, hogy a felderített hitelesítő adatokat biztonságosabb helyszínekre (például Azure Key Vault) helyezze.

* [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információkért lásd [: biztonsági ellenőrzés: kártevők elleni védelem](/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

A Microsoft kártevő szoftveres verziója engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure App Service), azonban az nem az ügyfél tartalmán fut.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft kártevő szoftverrel való ellátása engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen – például nagy kapacitású (Citus) –, azonban nem az ügyfél tartalmán fut.

A nem számítási Azure-erőforrásokra feltöltött tartalom előzetes vizsgálata, például App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL stb. A Microsoft nem fér hozzá az adataihoz ezekben a példányokban.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

A Microsoft kártevő-elhárító szolgáltatása engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen – például nagy kapacitású (Citus) –, azonban nem az ügyfél tartalmán fut.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

## <a name="data-recovery"></a>Adat-helyreállítás

*További információkért lásd [: biztonsági ellenőrzés: adat-helyreállítás](/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: Azure Database for PostgreSQL – a nagy kapacitású (Citus) automatikusan létrehozza az egyes csomópontok biztonsági másolatait, és helyileg redundáns tárolóban tárolja őket. A biztonsági mentések segítségével visszaállíthatja a nagy kapacitású-(Citus-) fürtöt egy adott időpontra.

* [Biztonsági mentés és visszaállítás a Azure Database for PostgreSQL-nagy kapacitású (Citus)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: naponta legalább egyszer Azure Database for PostgreSQL pillanatfelvételt készít az adatfájlokról és az adatbázis-tranzakciós naplóról. A biztonsági mentések lehetővé teszik a kiszolgálók bármely időpontra történő visszaállítását a megőrzési időtartamon belül. A megőrzési időszak jelenleg 35 nap az összes fürthöz. Az összes biztonsági mentés titkosítása AES 256 bites titkosítás használatával történik.

A rendelkezésre állási zónákat támogató Azure-régiókban a biztonsági mentési Pillanatképek három rendelkezésre állási zónában tárolódnak. Amíg legalább egy rendelkezésre állási zóna online állapotú, a nagy kapacitású (Citus) fürt visszaállítható.

* [Biztonsági mentés és visszaállítás a Azure Database for PostgreSQL-nagy kapacitású (Citus)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a Azure Database for PostgreSQL egy nagy kapacitású-(Citus-) fürt visszaállítása új fürtöt hoz létre az eredeti csomópontok biztonsági másolatai közül. Az elmúlt 35 napon belül bármikor visszaállíthat egy fürtöt bármely időpontra. A visszaállítási folyamat egy új fürtöt hoz létre ugyanabban az Azure-régióban, előfizetésben és erőforráscsoporthoz, mint az eredeti. Az új fürtkonfiguráció ugyanaz, mint az eredeti fürtkonfiguráció: azonos számú csomópont, a virtuális mag száma, a tárterület mérete és a felhasználói szerepkörök.

A tűzfalbeállítások és a PostgreSQL-kiszolgáló paraméterei nem őrződnek meg az eredeti kiszolgáló csoportjából. az alapértelmezett értékekre állnak vissza. A tűzfal megakadályozza az összes kapcsolatot. A visszaállítás után manuálisan kell módosítania ezeket a beállításokat.

* [Biztonsági mentés és visszaállítás a Azure Database for PostgreSQL-nagy kapacitású (Citus)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a törölt nagy kapacitású (Citus) fürtök nem állíthatók vissza. Ha törli a fürtöt, a fürthöz tartozó összes csomópont törölve lesz, és nem állítható helyre. A fürt erőforrásainak a véletlen törlésből vagy váratlan változásokból történő üzembe helyezése után a rendszergazdák kihasználhatják a felügyeleti zárolásokat.

* [Biztonsági mentés és visszaállítás a Azure Database for PostgreSQL-nagy kapacitású (Citus)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ [: Security Control: incidens válasza](/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: az incidensekre adott válaszokra vonatkozó útmutató kiépítése a szervezet számára. Győződjön meg arról, hogy van olyan írásos incidens-válasz, amely meghatározza a személyzet összes szerepkörét, valamint az incidensek kezelésének és kezelésének fázisait az észleléstől az incidens utáni felülvizsgálatig.

* [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben nem termelt), és hozzon létre egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és a réseket, és szükség szerint módosítsa a tervet.

* [Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és edzésprogramjának kidolgozásához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az ügyfél adatait egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

* [A Azure Security Center biztonsági kapcsolattartó beállítása](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Használhatja a Azure Security Center adatösszekötőt a riasztások Sentinel továbbításához.

* [Folyamatos exportálás konfigurálása](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Riasztások továbbítása az Azure Sentinelbe](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.

* [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért lásd [: biztonsági ellenőrzés: behatolási tesztek és Red Team-gyakorlatok](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: * [kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [További információt a Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatos, a Microsoft által felügyelt felhő-infrastruktúra,-szolgáltatások és-alkalmazások fejlesztésére vonatkozó stratégiáról és végrehajtásáról itt talál.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

## <a name="next-steps"></a>További lépések

- Lásd az [Azure biztonsági teljesítménytesztét](/azure/security/benchmarks/overview)
- További információ az [Azure biztonsági alaptervekről](/azure/security/benchmarks/security-baselines-overview)
