---
title: Azure-biztonsági alapkonfiguráció az Azure Container Registry számára
description: Azure-biztonsági alapkonfiguráció az Azure Container Registry számára
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 9225cfd9793a84f371387d6450a3dfa80ba74de3
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547542"
---
# <a name="azure-security-baseline-for-azure-container-registry"></a>Azure-biztonsági alapkonfiguráció az Azure Container Registry számára

Az Azure Security Baseline for Azure Container Registry javaslatokat tartalmaz, amelyek segítenek a központi telepítés biztonsági állapotának javításában.

A szolgáltatás alapkonfigurációja az [Azure Security Benchmark 1.0-s verziójából](https://docs.microsoft.com/azure/security/benchmarks/overview)származik, amely javaslatokat tartalmaz arra vonatkozóan, hogy miként biztosíthatja felhőalapú megoldásait az Azure-ban az ajánlott eljárásokra vonatkozó útmutatónkkal.

További információ: [Azure Security Baselines overview](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Hálózati biztonság

*További információt a [Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)című témakörben talál.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Erőforrások védelme hálózati biztonsági csoportokkal vagy az Azure tűzfallal a virtuális hálózaton

**Útmutató:** Az Azure Virtuális hálózat biztonságos, magánhálózati szolgáltatást biztosít az Azure-hoz és a helyszíni erőforrásokhoz. A privát Azure-tároló beállításjegyzék-hozzáférés egy Azure virtuális hálózat, biztosítja, hogy csak a virtuális hálózat erőforrásait a beállításjegyzékben. A létesítmények közötti forgatókönyvek, tűzfalszabályok at is konfigurálhat, hogy a rendszerleíró adatbázis csak adott IP-címekről való hozzáférést. A tűzfal mögül konfigurálja a tűzfal-hozzáférési szabályokat és a szolgáltatáscímkéket a tároló beállításjegyzékének eléréséhez.

Az Azure-tároló beállításjegyzékéhez való hozzáférés korlátozása azure-beli virtuális hálózati vagy tűzfalszabályok használatával:https://docs.microsoft.com/azure/container-registry/container-registry-vnet 

Szabályok konfigurálása a tűzfal mögötti Azure-tároló beállításjegyzékéhez:https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: A virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** Használja az Azure Security Centert, és orvosolja a hálózatvédelmi javaslatokat a hálózati erőforrások védelméhez az Azure-ban. Engedélyezze az NSG-folyamatnaplókat, és küldjön naplókat egy tárfiókba a forgalomnaplózáshoz.

Az NSG-folyamatnaplók engedélyezése:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A hálózati erőforrások védelme:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations



**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1.3: Védje a kritikus webes alkalmazásokat

**Útmutató**: Nem alkalmazható. A Benchmark az Azure App Service vagy a webalkalmazásokat tároló számítási erőforrások számára készült.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert rosszindulatú IP-címekkel folytatott kommunikáció megtagadása

**Útmutató:** Engedélyezze a DDoS Standard védelmet a virtuális hálózatokon a DDoS-támadások elleni védelem érdekében. Az Azure Security Center integrált fenyegetésfelderítési szolgáltatásával megtagadhatja az ismert rosszindulatú vagy nem használt internetes IP-címekkel folytatott kommunikációt.  Telepítse az Azure Firewall-t a szervezet minden hálózati határain, és a fenyegetésfelderítés engedélyezve van, és úgy van beállítva, hogy "Riasztás és megtagadás" a rosszindulatú hálózati forgalom esetén.

Használhatja az Azure Security Center Just In Time hálózati hozzáférést az NSG-k konfigurálásához, hogy korlátozott ideig korlátozza a végpontok kitettségét a jóváhagyott IP-címekre. Emellett az Azure Security Center Adaptive Network Hardening használatával olyan NSG-konfigurációkat javasolhat, amelyek a tényleges forgalom és a fenyegetésfelderítés alapján korlátozzák a portokat és a forrás IP-eket.

A DDoS-védelem konfigurálása:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Az Azure Tűzfal telepítése:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Ismerje meg az Azure Security Center integrált fenyegetésekkel kapcsolatos intelligenciáját:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Ismerje meg az Azure Security Center Adaptive Network edzését:https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Az Azure Security Center just in time hálózati hozzáférés-vezérlés:https://docs.microsoft.com/azure/security-center/security-center-just-in-time


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Hálózati csomagok és folyamatnaplók rögzítése

**Útmutató:** Engedélyezze a hálózati biztonsági csoport (NSG) folyamatnaplóit az alhálózathoz csatlakoztatott NSG számára, amelyet az Azure-tároló beállításjegyzékének védelmére használnak. Az NSG-folyamatnaplókat rögzítheti egy Azure Storage-fiókba folyamatrekordok létrehozásához. Ha a rendellenes tevékenységek kivizsgálásához szükséges, engedélyezze az Azure Network Watcher csomagrögzítését.

Az NSG-folyamatnaplók engedélyezése:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Network Watcher engedélyezése:https://docs.microsoft.com/azure/network-watcher/network-watcher-create


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózati behatolásészlelő/behatolás-megelőző rendszerek (IDS/IPS) telepítése

**Útmutató:** Válasszon ki egy ajánlatot az Azure Piactérről, amely támogatja az IDS/IPS funkciókat a hasznos adat vizsgálati képességekkel. Ha a behatolásészlelés és/vagy a hasznos adatvizsgálaton alapuló megelőzés nem követelmény, az Azure Firewall fenyegetési intelligenciával használható. Az Azure Firewall Threat intelligenciaalapú szűrés riasztást adhat és megtagadhatja az ismert rosszindulatú IP-címekre és tartományokra irányuló forgalmat. Az IP-címek és tartományok a Microsoft Threat Intelligence hírcsatornából származnak.

Telepítse az Ön által választott tűzfalmegoldást a szervezet minden hálózati határain a rosszindulatú forgalom észleléséhez és/vagy megtagadásához.

Azure Piactér:https://azuremarketplace.microsoft.com/marketplace/?term=Firewall 

Az Azure Tűzfal telepítése:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

A riasztások konfigurálása az Azure Tűzfallal:https://docs.microsoft.com/azure/firewall/threat-intel


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1.7: A webes alkalmazások forgalmának kezelése

**Útmutató**: Nem alkalmazható. A Benchmark az Azure App Service szolgáltatáson vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** A tároló beállításjegyzékhez való hozzáférést igénylő erőforrásokhoz használja az Azure Container Registry szolgáltatás virtuális hálózati szolgáltatáscímkéit a hálózati hozzáférés-vezérlések meghatározásához a hálózati biztonsági csoportokon vagy az Azure tűzfalon. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatáscímke nevének "AzureContainerRegistry" megadásával a szabály megfelelő forrás- vagy célmezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásakor.

Hozzáférés engedélyezése szolgáltatáscímke szerint:https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációinak karbantartása

**Útmutató:** Az Azure-tároló-beállításjegyzékekhez társított hálózati erőforrások szabványos biztonsági konfigurációinak definiálása és megvalósítása az Azure Policy használatával. A "Microsoft.ContainerRegistry" és a "Microsoft.Network" névterekben az Azure Policy aliasok használatával egyéni szabályzatokat hozhat létre a tároló-beállításjegyzékek hálózati konfigurációjának naplózásához vagy érvényesítéséhez. 

Az Azure Blueprints használatával egyszerűsítheti a nagyméretű Azure-telepítéseket a kulcsfontosságú környezeti összetevők, például az Azure Resource Manager-sablonok, az RBAC-vezérlők és a szabályzatok egyetlen tervezetdefinícióban történő csomagolásával. Egyszerűen alkalmazhatja a tervezetet az új előfizetésekre, és finomhangolhatja a vezérlést és a felügyeletet a verziószámozással.

Az Azure-tároló-beállításjegyzékek megfelelőségének naplózása az Azure-szabályzat használatával:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure Blueprint létrehozása:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Az ügyfél használhatja az Azure Blueprints egyszerűsítésére nagyszabású Azure-központi telepítések csomagolásával kulcsfontosságú környezeti összetevők, például az Azure Resource Manager-sablonok, RBAC-vezérlők és szabályzatok, egyetlen tervezet definíciójában. Egyszerűen alkalmazhatja a tervezetet az új előfizetésekre, és finomhangolhatja a vezérlést és a felügyeletet a verziószámozással.

Azure Blueprint létrehozása:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrás-konfigurációk figyelésére és a változások észlelésére

**Útmutató:** Az Azure-tevékenységnapló segítségével figyelheti a hálózati erőforrás-konfigurációkat, és észlelheti a tárolórendszerjegyzékekhez kapcsolódó hálózati erőforrások változásait. Hozzon létre riasztásokat az Azure Monitoron belül, amelyek a kritikus hálózati erőforrások módosításakor aktiválódnak.

Az Azure-tevékenységnapló eseményeinek megtekintése és beolvasása:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Riasztások létrehozása az Azure Monitorban:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Security Control: Logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Jóváhagyott időszinkronizációs források használata

**Útmutató: A**Microsoft időforrásokat tart fenn az Azure-erőforrásokhoz, azonban lehetősége van a számítási erőforrások időszinkronizálási beállításainak kezelésére.

Az Azure számítási erőforrásainak időszinkronizálásának konfigurálása:https://docs.microsoft.com/azure/virtual-machines/windows/time-sync


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: A központi biztonsági napló kezelésének konfigurálása

**Útmutató:** Az Azure Monitoron keresztüli naplók betöltése az Azure-tároló beállításjegyzéke által létrehozott biztonsági adatok összesítéséhez. Az Azure Monitoron belül a Log Analytics-munkaterület(ek) segítségével lekérdezheti és elvégezheti az elemzéseket, és használhatja az Azure Storage-fiókokat a hosszú távú/archiválási tároláshoz.

Az Azure Container Registry naplók diagnosztikai kiértékeléshez és naplózáshoz:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs



**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Az Azure-erőforrások naplózásának engedélyezése

**Útmutatás: Az**Azure Monitor erőforrásnaplókat (korábbi nevén diagnosztikai naplókat) gyűjt a rendszerleíró adatbázisban lévő felhasználó által vezérelt eseményekhez. Gyűjtse össze és használja fel ezeket az adatokat a rendszerleíró adatbázis hitelesítési eseményeinek naplózásához, és teljes tevékenységnyomvonalat biztosítson a rendszerleíró adatbázis összetevőiről, például a lekéréses és leküldéses eseményekről, hogy diagnosztizálhassa a beállításjegyzékkel kapcsolatos biztonsági problémákat.

Az Azure Container Registry naplók diagnosztikai kiértékeléshez és naplózáshoz:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Biztonsági naplók gyűjtése az operációs rendszerekről

**Útmutató**: Nem alkalmazható. A teljesítményteszt számítási erőforrásokhoz készült.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="25-configure-security-log-storage-retention"></a>2.5: A biztonsági napló tárolásának megőrzése

**Útmutató:** Az Azure Monitoron belül állítsa be a Log Analytics-munkaterület-megőrzési időszakot a szervezet megfelelőségi előírásainak megfelelően. Azure Storage-fiókok használata hosszú távú/archiválási tároláshoz.

A loganalytics-munkaterületek naplómegőrzési paramétereinek beállítása:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és felülvizsgálata

**Útmutató:** Elemezze és figyelje az Azure Container Registry naplók rendellenes viselkedés és rendszeresen tekintse át az eredményeket. Az Azure Monitor Log Analytics-munkaterületével tekintse át a naplókat, és hajtson végre lekérdezéseket a naplóadatokon.

Az Azure Container Registry naplók diagnosztikai kiértékeléshez és naplózáshoz:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

A Log Analytics-munkaterület ismertetése:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Egyéni lekérdezések végrehajtása az Azure Monitorban:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Riasztás engedélyezése rendellenes tevékenységre

**Útmutató:** Az Azure Log Analytics-munkaterület használatával figyelheti és riasztást készíthet a biztonsági naplókban és az Azure-tároló beállításjegyzékéhez kapcsolódó eseményekben.

Az Azure Container Registry naplók diagnosztikai kiértékeléshez és naplózáshoz:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

A naplóelemzési napló adatainak riasztása:https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2.8: A kártevőirtó naplózás központosítása

**Útmutató**: Nem alkalmazható. Az Azure Container Registry nem dolgozza fel és nem készít kártevők elleni kapcsolódó naplókat.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="29-enable-dns-query-logging"></a>2.9: Dns-lekérdezésnaplózás engedélyezése

**Útmutató**: Nem alkalmazható. Az Azure Container Registry egy végpont, és nem kezdeményez kommunikációt, és a szolgáltatás nem kérdezi le a DNS-t.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="210-enable-command-line-audit-logging"></a>2.10: Parancssori naplózás engedélyezése

**Útmutató**: Nem alkalmazható. A teljesítményteszt számítási erőforrásokhoz készült.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt a [Biztonságvezérlés: Identitás- és hozzáférés-vezérlés](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Az adminisztratív számlák leltárának fenntartása

**Útmutatás:** Az Azure Active Directory (Azure AD) beépített szerepköröket, amelyeket explicit módon hozzá kell rendelni, és lekérdezhető. Az Azure AD PowerShell modul használatával ad hoc lekérdezések végrehajtásával felügyeleti csoportok tagjaiként rendelkező fiókok felderítéséhez.

Minden Egyes Azure-tároló beállításjegyzék, nyomon követheti, hogy a beépített rendszergazdai fiók engedélyezve van, vagy le van tiltva. Tiltsa le a fiókot, ha nincs használatban.

Címtárszerepkör beszereznie az Azure AD-ben a PowerShell használatával:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Címtárszerepkör tagjainak beszereznie az Azure AD-ben a PowerShell segítségével:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Azure Container Registry rendszergazdai fiók:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Adott esetben változtassa meg az alapértelmezett jelszavakat

**Útmutató:** Az Azure Active Directory (Azure AD) nem rendelkezik az alapértelmezett jelszavak fogalmát. Más Azure-erőforrások, amelyek jelszót igényelnek, kényszerítsék a jelszót, hogy összetettségi követelményekkel és minimális jelszóhosszal kell létrehozni, amely a szolgáltatástól függően eltérő. Ön felelős a külső alkalmazásokért és a Marketplace-szolgáltatásokért, amelyek alapértelmezett jelszavakat használhatnak.

Ha az Azure-tároló beállításjegyzék alapértelmezett rendszergazdai fiókja engedélyezve van, a rendszer automatikusan komplex jelszavakat hoz létre, és el kell forgatni. Tiltsa le a fiókot, ha nincs használatban.

Azure Container Registry rendszergazdai fiók:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált felügyeleti fiókok használata

**Útmutató**: Szabványos működési eljárások létrehozása a kijelölt adminisztratív számlák használata körül. Az Azure Security Center identitás- és hozzáférés-kezelése segítségével figyelheti a felügyeleti fiókok számát.

Hozzon létre eljárásokat a tárolóbeállítás-jegyzék beépített felügyeleti fiókjának engedélyezéséhez is. Tiltsa le a fiókot, ha nincs használatban.

Ismerje meg az Azure Security Center identitását és hozzáférését:https://docs.microsoft.com/azure/security-center/security-center-identity-access

Azure Container Registry rendszergazdai fiók:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Egyszeri bejelentkezés (SSO) használata az Azure Active Directoryval

**Útmutató:** Ahol csak lehetséges, használja az Azure Active Directory egyszeri szolgáltató t az egyes önálló hitelesítő adatok szolgáltatásonkénti konfigurálása helyett. Használja az Azure Security Center identitás- és hozzáférés-kezelési javaslatait.

A tároló beállításjegyzék-beállításhoz való egyéni hozzáféréshez használja az Azure Active Directoryba integrált egyéni bejelentkezést.

Az SSO ismertetése az Azure AD-vel:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

Egyéni bejelentkezés a tárolók rendszerleíró adatbázisába:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató:** Engedélyezze az Azure Active Directory (Azure AD) többtényezős hitelesítést (MFA), és kövesse az Azure Security Center identitás- és hozzáférés-kezelési javaslatait.

Az MFA engedélyezése az Azure-ban:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Az Azure Security Center identitásának és hozzáférésének figyelése:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Használjon dedikált gépeket (kiemelt hozzáférésű munkaállomásokat) minden adminisztratív feladathoz

**Útmutató:** Emelt szintű munkaállomások (kiemelt hozzáférésű munkaállomások) használata az MFA konfigurálva az Azure-erőforrásokba való bejelentkezéshez és konfigurálásához.

Tudnivalók a kiemelt hozzáférésű munkaállomásokról:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Az MFA engedélyezése az Azure-ban:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Az Azure Security Center figyelése**: N/A

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: A gyanús tevékenységek naplózása és riasztása az adminisztratív fiókokból

**Útmutató:** Használja az Azure Active Directory (Azure AD) biztonsági jelentések et a naplók és riasztások létrehozásához, ha gyanús vagy nem biztonságos tevékenység történik a környezetben. Az Azure Security Center használatával figyelheti az identitás- és hozzáférési tevékenységeket.

Kockázatos tevékenységesetén megjelölt Azure AD-felhasználók azonosítása:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

A felhasználók identitásának és hozzáférési tevékenységének figyelése az Azure Security Centerben:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** A feltételes hozzáféréssel elnevezett helyek használatával csak az IP-címtartományok vagy országok/régiók meghatározott logikai csoportjaiból engedélyezheti a hozzáférést.

Named-helyek konfigurálása az Azure-ban:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3.9: Az Azure Active Directory használata

**Útmutató:** Használja az Azure Active Directoryt (Azure AD) központi hitelesítési és engedélyezési rendszerként. Az Azure AD védi az adatokat azáltal, hogy erős titkosítást használ az inaktív és az átvitel során. Az Azure AD is sók, kihegedések, és biztonságosan tárolja a felhasználói hitelesítő adatokat.

Azure AD-példány létrehozása és konfigurálása:https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rendszeresen vizsgálja felül és egyeztetje össze a felhasználói hozzáférést

**Útmutató:** Az Azure Active Directory (Azure AD) naplókat biztosít az elavult fiókok felderítéséhez. Emellett az Azure Identity Access-vélemények használatával hatékonyan kezelheti a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen ellenőrizhető, hogy csak a megfelelő Felhasználók rendelkezhessenek folyamatos hozzáféréssel.

Ismerje meg az Azure AD-jelentéseket:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Az Azure-identitás-hozzáférési felülvizsgálatok használata:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Az inaktivált fiókok elérésére tett kísérletek figyelése

**Útmutató:** Hozzáféréssel rendelkezik az Azure Active Directory (Azure AD) bejelentkezési tevékenység, naplózási és kockázati eseménynapló-forrásokhoz, amelyek lehetővé teszik a biztonsági információk és események kezelésével (SIEM) /Monitoring eszközzel való integrációt.

Ezt a folyamatot egyszerűsítheti az Azure Active Directory felhasználói fiókok diagnosztikai beállításainak létrehozásával, valamint a naplónaplók és a bejelentkezési naplók elküldésével a Log Analytics-munkaterületre. A Log Analytics-munkaterületen konfigurálhatja a kívánt riasztásokat.

Az Azure-tevékenységnaplók integrálása az Azure Monitorba:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltéréséről

**Útmutató:** Az Azure Active Directory (Azure AD) kockázat- és identitásvédelmi szolgáltatásaival konfigurálhatja a felhasználói identitásokkal kapcsolatos észlelt gyanús műveletekre adott automatikus válaszokat. 

Az Azure AD kockázatos bejelentkezései megtekintése:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Az identitásvédelem kockázati házirendjeinek konfigurálása és engedélyezése:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: A Microsoft hozzáférésének biztosítása a releváns ügyféladatokhoz támogatási forgatókönyvek során

**Útmutató**: Nem áll rendelkezésre; Az Ügyfélszéf jelenleg nem támogatott az Azure Container Registry számára.

Az ügyfélszéfáltal támogatott szolgáltatások listája:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információ: [Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Az érzékeny információk leltárának karbantartása

**Útmutató:** Erőforrás-címkék segítségével nyomon követheti az Azure-tároló kretén beállításjegyzékek, amelyek bizalmas adatokat tárolnak vagy dolgoznak fel.

Címkézze meg és verziótároló-lemezképeket vagy más összetevőket címkézzen meg a beállításjegyzékben, és zárolja a képeket vagy adattárakat, hogy segítse a bizalmas adatokat tároló vagy feldolgozó lemezképek nyomon követését.

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Javaslatok a tárolórendszerképek címkézéséhez és verziószámozásához:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version

Tárolórendszerkép zárolása egy Azure-tároló beállításjegyzékében:https://docs.microsoft.com/azure/container-registry/container-registry-image-lock



**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Érzékeny információkat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Külön tároló-jegyzékek, előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztéshez, teszteléshez és éles környezethez. A bizalmas adatokat tároló vagy feldolgozó erőforrásokat megfelelően el kell különíteni.

Az erőforrásokat virtuális hálózatnak vagy alhálózatnak kell elválasztani, megfelelően címkézni kell, és egy hálózati biztonsági csoportnak (NSG) vagy az Azure Tűzfalnak kell biztosítania.

További Azure-előfizetések létrehozása:https://docs.microsoft.com/azure/billing/billing-create-subscription

Felügyeleti csoportok létrehozása:https://docs.microsoft.com/azure/governance/management-groups/create

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Az Azure-tároló beállításjegyzékéhez való hozzáférés korlátozása azure-beli virtuális hálózati vagy tűzfalszabályok használatával:https://docs.microsoft.com/azure/container-registry/container-registry-vnet

Hogyan hozzunk létre egy NSG egy biztonsági config:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Az Azure Tűzfal telepítése:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

A riasztás vagy riasztás és a megtagadás konfigurálása az Azure Tűzfallal:

https://docs.microsoft.com/azure/firewall/threat-intel



**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: A bizalmas adatok jogosulatlan továbbításának figyelése és blokkolása

**Útmutató:** Telepítsen egy automatizált eszközt a hálózat peremén, amely figyeli a bizalmas adatok jogosulatlan továbbítását, és blokkolja az ilyen átviteleket, miközben figyelmezteti az információbiztonsági szakembereket.

A Microsoft által kezelt mögöttes platform esetében a Microsoft minden ügyféltartalmat érzékenynek tekint, és mindent megtesz azért, hogy megvédje az ügyfelek adatvesztését és expozícióját. Annak érdekében, hogy az Azure-on belüli ügyféladatok biztonságban legyenek, a Microsoft hatékony adatvédelmi vezérlőket és képességeket vezetett be és tart fenn.

Az ügyfelek adatainak védelme az Azure-ban:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az összes bizalmas információ titkosítása szállítás közben

**Útmutató:** Győződjön meg arról, hogy az Azure Container Registry-hez csatlakozó ügyfelek képesek-e a TLS 1.2-es vagy nagyobb egyeztetésére. A Microsoft Azure-erőforrások alapértelmezés szerint a TLS 1.2-t tárgyalják.

Kövesse az Azure Security Center javaslatokat az inaktív titkosításhoz és a titkosításhoz az átvitel során, adott esetben.

Az Azure-ral való átvitel során a titkosítás megismerése:https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit



**Az Azure Security Center figyelése:** Igen

**Felelősség**: Megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató:** Az adatok azonosításával, besorolásával és a veszteségmegelőzési funkciókmég nem érhetők el az Azure Container Registry számára. Harmadik féltől származó megoldás megvalósítása, ha a megfelelőségi célokból szükséges.

A Microsoft által kezelt mögöttes platform esetében a Microsoft minden ügyféltartalmat érzékenynek tekint, és mindent megtesz azért, hogy megvédje az ügyfelek adatvesztését és expozícióját. Annak érdekében, hogy az Azure-on belüli ügyféladatok biztonságban legyenek, a Microsoft hatékony adatvédelmi vezérlőket és képességeket vezetett be és tart fenn.

Az ügyfelek adatainak védelme az Azure-ban:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Megosztott

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Az Azure RBAC használatával szabályozhatja az erőforrásokhoz való hozzáférést

**Útmutató:** Az Azure Active Directory (Azure AD) RBAC használatával szabályozhatja az adatokhoz és erőforrásokhoz való hozzáférést egy Azure-tároló beállításjegyzékben. 

Az RBAC konfigurálása az Azure-ban:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Azure Container Registry szerepkörök és engedélyek:https://docs.microsoft.com/azure/container-registry/container-registry-roles



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: A hozzáférés-vezérlés kényszerítése gazdagépalapú adatveszteség-megelőzéssel

**Útmutató:** Ha a számítási erőforrások megfelelőségéhez szükséges, hajtson végre egy harmadik féltől származó eszközt, például egy automatizált gazdagép-alapú adatveszteség-megelőzési megoldást, hogy az adatokhoz való hozzáférés-szabályozás tanusítsa még akkor is, ha az adatokat a rendszerről másolja.

A Microsoft által kezelt mögöttes platform esetében a Microsoft minden ügyféltartalmat érzékenynek tekint, és mindent megtesz azért, hogy megvédje az ügyfelek adatvesztését és expozícióját. Annak érdekében, hogy az Azure-on belüli ügyféladatok biztonságban legyenek, a Microsoft hatékony adatvédelmi vezérlőket és képességeket vezetett be és tart fenn.

Az ügyfelek adatainak védelme az Azure-ban:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Megosztott

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas információk titkosítása nyugalmi

**Útmutató:** Használja a titkosítást inaktívan az összes Azure-erőforrást. Alapértelmezés szerint az Azure-tároló beállításjegyzékében lévő összes adat inkultatra van titkosítva a Microsoft által felügyelt kulcsok használatával.

Az Azure-ban inaktív titkosítás ismertetése:https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Ügyfél által felügyelt kulcsok az Azure Container Registry-ben:https://aka.ms/acr/cmk



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Naplózza és figyelmeztesse a kritikus Azure-erőforrások változásait

**Útmutatás: Az**Azure Monitor erőforrásnaplókat (korábbi nevén diagnosztikai naplókat) gyűjt a rendszerleíró adatbázisban lévő felhasználó által vezérelt eseményekhez. Gyűjtse össze és használja fel ezeket az adatokat a rendszerleíró adatbázis hitelesítési eseményeinek naplózásához, és teljes tevékenységnyomvonalat biztosítson a rendszerleíró adatbázis összetevőiről, például a lekéréses és lekéréses eseményekről, hogy diagnosztizálhassa a beállításjegyzékműködési problémáit.

Az Azure Container Registry naplók diagnosztikai kiértékeléshez és naplózáshoz:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információt a [Biztonsági ellenőrzés: Biztonsági rés kezelése című](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)témakörben talál.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Automatikus biztonsági rés-ellenőrző eszközök futtatása

**Útmutató:** Kövesse az Azure Security Center ajánlásait a tárolórendszerképeken a biztonsági rések értékelésének elvégzésével kapcsolatos javaslatokat. Szükség esetén külső megoldásokat is üzembe helyezhet az Azure Piactérről a rendszerbiztonsági biztonsági rések felmérése érdekében.

Az Azure Security Center biztonsági résértékelési javaslatainak megvalósítása:https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

Az Azure Container Registry integrációja a Security Centerrel (előzetes verzió):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Az operációs rendszer automatikus javításkezelési megoldásának telepítése

**Útmutató: A**Microsoft javítások felügyeletét végzi az Azure Container Registry szolgáltatást támogató mögöttes rendszereken.

Automatizálhatja a tárolórendszerkép-frissítéseket, ha az operációs rendszer ből és más javításokból származó alaplemezképek frissítései észlelhetők.

Az Azure Container Registry feladatok alaprendszerkép-frissítései:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Automatizált, harmadik féltől származó szoftverjavítás-kezelési megoldás telepítése

**Útmutató**: Harmadik féltől származó megoldással foltozhatja ki az alkalmazás képeit.  Emellett futtathatja az Azure Container Registry feladatokat az alkalmazásrendszerképek frissítéseinek automatizálásához egy tároló beállításjegyzékében a biztonsági javítások vagy az alaplemezképek más frissítései alapján.

Az ACR-feladatok alapképfrissítései:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Hasonlítsa össze a biztonsági rés vizsgálatait

**Útmutató:** Integrálja az Azure Container Registry (ACR) az Azure Security Center rel a tárolórendszerképek rendszeres vizsgálatának lehetővé tétele a biztonsági rések engedélyezéséhez. Szükség esetén az Azure Piactérről is telepíthet külső megoldásokat a rendszerbiztonsági rés rendszeres vizsgálatához.

Az Azure Container Registry integrációja a Security Centerrel (előzetes verzió):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Kockázatminősítési eljárás használata a felfedezett biztonsági rések helyreállításának rangsorolására

**Útmutató:** Integrálja az Azure Container Registry (ACR) az Azure Security Center, hogy rendszeres en a tároló rendszerképek a biztonsági rések et, és a kockázatok osztályozása érdekében. Szükség esetén az Azure Marketplace-en is üzembe helyezhet külső megoldásokat a rendszerbiztonsági rés időszakos vizsgálatához és a kockázati besoroláshoz.

Az Azure Container Registry integrációja a Security Centerrel (előzetes verzió):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Az Azure Security Center figyelése**: N/A

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Az Azure Asset Discovery használata

**Útmutatás:** Az Azure Resource Graph használatával lekérdezheti/felderítheti az összes erőforrást (például a számítási, tárolási, hálózati, portok és protokollok stb.) az előfizetés(ek)en belül).  Biztosítsa a megfelelő (olvasási) engedélyeket a bérlőben, és számba vesse az összes Azure-előfizetést, valamint az előfizetéseken belüli erőforrásokat.

Bár a klasszikus Azure-erőforrások fedezhetők fel a Resource Graph, erősen ajánlott az Azure Resource Manager erőforrásainak létrehozása és használata a jövőben.

Lekérdezések létrehozása az Azure Resource Graph segítségével:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Az Azure-előfizetések megtekintése:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0 

Az Azure RBAC megismerése:https://docs.microsoft.com/azure/role-based-access-control/overview



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6.2: Az eszközök metaadatainak karbantartása

**Útmutató:** Az Azure Container Registry metaadatokat tart fenn, beleértve a címkéket és a rendszerképek et a beállításjegyzékben. Kövesse az összetevők címkézésének ajánlott módszereit.

A nyilvántartásokról, adattárakról és képekről:https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Javaslatok a tárolórendszerképek címkézéséhez és verziószámozásához:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Jogosulatlan Azure-erőforrások törlése

**Útmutató:** Az Azure Container Registry metaadatokat tart fenn, beleértve a címkéket és a rendszerképek et a beállításjegyzékben. Kövesse az összetevők címkézésének ajánlott módszereit.

A nyilvántartásokról, adattárakról és képekről:https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Javaslatok a tárolórendszerképek címkézéséhez és verziószámozásához:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: A jóváhagyott Azure-erőforrások és szoftvercímek leltárának karbantartása

**Útmutató:** Létre kell hoznia egy leltárt a jóváhagyott Azure-erőforrások, mint egy a szervezeti igényeket.  

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: A nem jóváhagyott Azure-erőforrások figyelése

**Útmutató:** Az Azure-szabályzat használatával korlátozásokat helyezhet el az előfizetés(ek)ben létrehozható erőforrások típusára vonatkozóan.

Az Azure Resource Graph használatával erőforrásokat kérdezhet le/fedezhet fel az előfizetéseikben.  Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóváhagyásra kerül.

Az Azure-tároló-beállításjegyzékek megfelelőségének naplózása az Azure-szabályzat használatával:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Lekérdezések létrehozása az Azure Graph segítségével:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: A nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató:** Elemezze és figyelje az Azure Container Registry naplók rendellenes viselkedés és rendszeresen tekintse át az eredményeket. Az Azure Monitor Log Analytics-munkaterületével tekintse át a naplókat, és hajtson végre lekérdezéseket a naplóadatokon.

Az Azure Container Registry naplók diagnosztikai kiértékeléshez és naplózáshoz:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

A Log Analytics-munkaterület ismertetése:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Egyéni lekérdezések végrehajtása az Azure Monitorban:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: A nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutatás: Az**Azure Automation teljes körű vezérlést biztosít a telepítés, a műveletek és a számítási feladatok és erőforrások leszerelése során.  Saját megoldást valósíthat meg a jogosulatlan Azure-erőforrások eltávolítására. Bevezetés az Azure Automation bemutatkozása:https://docs.microsoft.com/azure/automation/automation-intro


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="68-use-only-approved-applications"></a>6.8: Csak jóváhagyott alkalmazásokat használjon

**Útmutató**: Nem alkalmazható. A benchmark számítási erőforrásokhoz készült.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató:** Használja az Azure-szabályzatot, hogy korlátozza, mely szolgáltatásokat hozhat ki a környezetben.

Az Azure-tároló-beállításjegyzékek megfelelőségének naplózása az Azure-szabályzat használatával:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Adott erőforrástípus megtagadása az Azure-szabályzattal:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="610-implement-approved-application-list"></a>6.10: A jóváhagyott pályázati lista végrehajtása

**Útmutató**: Nem alkalmazható. A benchmark számítási erőforrásokhoz készült.



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Korlátozza a felhasználók azon képességét, hogy parancsfájlokon keresztül kommunikáljanak az AzureResources Managerrel

**Útmutató:** Az operációs rendszer-specifikus konfigurációk vagy harmadik féltől származó erőforrások használatával korlátozhatja a felhasználók azon képességét, hogy parancsfájlokat hajtsanak végre az Azure számítási erőforrásain belül.

A feltételes hozzáférés beállítása az Azure Resources Manager elérésének letiltásához:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Korlátozza a felhasználók azon képességét, hogy parancsfájlokat hajtsanak végre a számítási erőforrásokon belül

**Útmutató:** Az operációs rendszer-specifikus konfigurációk vagy a külső erőforrások segítségével korlátozhatja a felhasználók azon képességét, hogy parancsfájlokat hajtson végre az Azure számítási erőforrásokon belül.

Például a PowerShell-parancsfájlok végrehajtásának szabályozása Windows-környezetekben:https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizikailag vagy logikailag elkülönítve a magas kockázatú alkalmazásokat

**Útmutatás:** Az üzleti műveletekhez szükséges, de a szervezet számára nagyobb kockázatot merülő szoftvert el kell különíteni a saját virtuális gépén és/vagy virtuális hálózatán belül, és megfelelően kell biztosítani az Azure tűzfal vagy a hálózati biztonsági csoport használatával.

Hogyan hozzunk létre egy virtuális hálózat:https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Hogyan hozzunk létre egy NSG egy biztonsági config:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt a [Biztonsági ellenőrzés: Biztonságos konfiguráció című](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** Az Azure Policy vagy az Azure Security Center használatával az összes Azure-erőforrás biztonsági konfigurációját karbantarthatja.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Az Azure-tároló-beállításjegyzékek megfelelőségének naplózása az Azure-szabályzat használatával:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató:** Használja az Azure Security Center "A virtuális gépek biztonsági konfigurációinak biztonsági biztonsági biztonsági rései" című ajánlását, amely az összes számítási erőforrás biztonsági konfigurációinak karbantartásához szükséges.

Az Azure Security Center-javaslatok figyelése:https://docs.microsoft.com/azure/security-center/security-center-recommendations

Az Azure Security Center rekretijavaslatei:https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató:** Használja az Azure-szabályzat [megtagadás] és a [üzembe helyezés, ha nem létezik] biztonságos beállítások kényszerítése az Azure-erőforrások között.

Az Azure-tároló-beállításjegyzékek megfelelőségének naplózása az Azure-szabályzat használatával:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Az Azure-szabályzat hatásainak ismertetése:https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Biztonságos operációsrendszer-konfigurációk karbantartása

**Útmutató**: Nem alkalmazható. A teljesítményteszt számítási erőforrásokhoz készült.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Megosztott

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások biztonságos tárolása

**Útmutató:** Ha egyéni Azure-szabályzat-definíciókat használ, az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódot.

Kód tárolása az Azure DevOps-ban:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos dokumentáció:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Biztonságosan tárolhatja az operációs rendszer egyedi lemezképeit

**Útmutató**: Nem alkalmazható. A teljesítményteszt számítási erőforrásokra vonatkozik.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Rendszerkonfiguráció-kezelő eszközök telepítése

**Útmutató:** Az Azure-szabályzat használatával riasztást, naplózást és kényszerítést rendszerkonfigurációk. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirendkivételek kezeléséhez.

Az Azure-tároló-beállításjegyzékek megfelelőségének naplózása az Azure-szabályzat használatával:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Rendszerkonfiguráció-kezelő eszközök telepítése operációs rendszerekhez

**Útmutató**: Nem alkalmazható. A teljesítményteszt számítási erőforrásokra vonatkozik.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Automatikus konfigurációfigyelés megvalósítása az Azure-szolgáltatásokhoz

**Útmutató:** Az Azure Security Center használatával alapszintű vizsgálatokat hajthat végre az Azure Resources számára.

Az Azure Policy használatával korlátozhatja az előfizetés(ek)ben létrehozható erőforrások típusát.

Javaslatok kijavítása az Azure Security Centerben:https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

Az Azure-tároló-beállításjegyzékek megfelelőségének naplózása az Azure-szabályzat használatával:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Az operációs rendszerek automatizált konfigurációs figyelésének megvalósítása

**Útmutató**: Nem alkalmazható. A teljesítményteszt számítási erőforrásokra vonatkozik.


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="711-manage-azure-secrets-securely"></a>7.11: Az Azure-titkok biztonságos kezelése

**Útmutató:** A felügyelt szolgáltatásidentitás az Azure Key Vaulttal együtt egyszerűsítése és biztonságos sápkezelése a felhőalapú alkalmazások.

Integrálás az Azure felügyelt identitásaival:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Key Vault létrehozása:https://docs.microsoft.com/azure/key-vault/quick-create-portal

A Kulcstartó hitelesítése felügyelt identitással:https://docs.microsoft.com/azure/key-vault/managed-identity

Azure által felügyelt identitás használata az Azure Container Registry feladatokban:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-authentication-managed-identity


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: A személyazonosságok biztonságos és automatikus kezelése

**Útmutató:** Felügyelt identitások használatával biztosítható az Azure-szolgáltatások automatikusan felügyelt identitást az Azure AD-ben. Felügyelt identitások lehetővé teszi, hogy hitelesítse az Azure AD-hitelesítést támogató szolgáltatások, beleértve a Key Vault, hitelesítő adatok nélkül a kódot.

Felügyelt identitások konfigurálása:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Felügyelt identitás használatával hitelesítheti magát egy Azure-tároló beállításjegyzékében:https://docs.microsoft.com/azure/container-registry/container-registry-authentication-managed-identity


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A nem kívánt hitelesítő adatok expozíciójának kiküszöbölése

**Útmutató:** A hitelesítő adatok at a kódon belüli hitelesítő adatok azonosítására valósíthatja meg. A Hitelesítő adatok olvasója azt is ösztönzi a felderített hitelesítő adatok áthelyezése biztonságosabb helyekre, például az Azure Key Vault.

A Hitelesítő adatok képolvasójának beállítása:https://secdevtools.azurewebsites.net/helpcredscan.html


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információ: [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Központilag kezelt kártevőirtó szoftverek használata

**Útmutató:** A Microsoft Antimalware for Azure Cloud Services és a virtuális gépek segítségével folyamatosan figyelheti és védheti az erőforrásokat. Linux esetén használjon harmadik féltől származó kártevőirtó megoldást.

A Microsoft Kártevőirtó szolgáltatás konfigurálása felhőszolgáltatásokhoz és virtuális gépekhez:https://docs.microsoft.com/azure/security/fundamentals/antimalware


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltöltendő fájlok előzetes beszkéselése

**Útmutató: A**Microsoft Antimalware engedélyezve van az alapul szolgáló gazdagépen, amely támogatja az Azure-szolgáltatásokat (például az Azure Container Registry), azonban nem fut az ügyfél tartalom.

A nem számítási Azure-erőforrásokba feltöltött fájlok, például az App Service, a Data Lake Storage, a Blob Storage stb.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Gondoskodjon a kártevőirtó szoftverek és aláírások frissítéséről

**Útmutató**: Nem alkalmazható. A teljesítményteszt számítási erőforrásokhoz készült. A Microsoft kezeli a kártevőirtót az alapul szolgáló platformhoz.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató: A**Microsoft Azure-tároló beállításjegyzékében lévő adatok mindig automatikusan replikálódnak a tartósság és a magas rendelkezésre állás biztosítása érdekében. Az Azure Container Registry úgy másolja az adatokat, hogy azok védve legyenek a tervezett és nem tervezett eseményekkel szemben

Szükség esetén georeplikálhatja a tároló beállításjegyzékét a rendszerleíró replikák karbantartásához több Azure-régióban. 

Georeplikáció az Azure Container Registry szolgáltatásban:https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszerbiztonsági mentés végrehajtása és biztonsági mentés bármely ügyfél által kezelt kulcs ról

**Útmutató:** Tetszés szerint biztonsági másolatot készíteni a tárolórendszerképekről az egyik rendszerleíró adatbázisból a másikba történő importálással.

Az Azure Key Vaultban az ügyfelek által felügyelt kulcsokról az Azure parancssori eszközeivel vagy SDK-kkal is biztonsági másolatot kell kapnia.

Tárolórendszerképek importálása tárolóbeállításjegyzékbe:https://docs.microsoft.com/azure/container-registry/container-registry-import-images

A kulcstároló kulcsainak biztonsági mentése az Azure-ban:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági mentés ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** Az Azure Key Vaultban az ügyfél által felügyelt kulcsok biztonsági mentésének tesztelése az Azure parancssori eszközeivel vagy SDK-kkal.

Az Azure Key Vault-kulcsok visszaállítása az Azure-ban:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági mentések és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató:** Engedélyezheti a soft-delete az Azure Key Vault ban a kulcsok véletlen vagy rosszindulatú törlés elleni védelme érdekében.

A soft-delete engedélyezése a Key Vaultban:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Security Control: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidenselhárítási útmutató létrehozása

**Útmutató:** Hozzon létre egy incidens-elhárítási útmutatót a szervezet számára. Győződjön meg arról, hogy vannak olyan írásos eseményreagálási tervek, amelyek meghatározzák a személyzet összes szerepkörét, valamint az incidenskezelés/-kezelés fázisait az észleléstől az incidens utáni felülvizsgálatig.

Munkafolyamat-automatizálások konfigurálása az Azure Security Centerben:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Útmutató a saját biztonsági incidensek reválaszolási folyamatának felépítéséhez:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

A Microsoft Security Response Center egy incidens anatómia:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Az Ügyfél a NIST számítógépes biztonsági incidenskezelési útmutatóját is felhasználhatja, hogy segítse saját eseményreagálási tervének létrehozását:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és rangsorolási eljárás létrehozása

**Útmutatás: Az**Azure Security Center minden riasztássúlyosságát hozzárendeli, hogy segítsen rangsorolni, mely riasztásokat kell először megvizsgálni. A súlyosság attól függ, hogy a Security Center mennyire magabiztos a keresésben, vagy a riasztás kiadásához használt analitikus, valamint a riasztáshoz vezető tevékenység mögött rosszindulatú szándék megbízhatósági szintje.

Ezenkívül egyértelműen jelölje meg az előfizetéseket (pl. termelés, nem prod), és hozzon létre egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosítására és kategorizálására.


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10.3: A tesztelési biztonsági reagálási eljárások

**Útmutató:** Végezzen gyakorlatokat a rendszerek incidenselhárítási képességeinek rendszeres ütemben való teszteléséhez. Azonosítsa a gyenge pontokat és hiányosságokat, és szükség szerint vizsgálja felül a tervet.

Tekintse meg a NIST kiadványát: Útmutató az informatikai tervekés képességek tesztelési, képzési és edzésprogramjaihoz:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidensek elérhetőségét, és állítsa be a biztonsági incidensek riasztási értesítéseit

**Útmutató: A**Microsoft a biztonsági incidensek kapcsolattartási adatait arra használja fel, hogy kapcsolatba lépjen Önnel, ha a Microsoft Security Response Center (MSRC) azt észleli, hogy az ügyfél adataihoz jogellenes vagy jogosulatlan fél fért hozzá.  Tekintse át az incidenseket a problémák megoldásának biztosítása érdekében.

Az Azure Security Center biztonsági kapcsolattartójának beállítása:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidenselhárítási rendszerbe

**Útmutató:** Exportálja az Azure Security Center riasztások és javaslatok a folyamatos exportálás funkció használatával. A Folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális exportálását, vagy folyamatos, folyamatos módon. Használhatja az Azure Security Center adatösszekötő a Sentinel riasztások streameléséhez.

A folyamatos exportálás konfigurálása:https://docs.microsoft.com/azure/security-center/continuous-export

Értesítések streamelése az Azure Sentinelbe:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztásokra adott válasz automatizálása

**Útmutató:** Az Azure Security Center munkafolyamat-automatizálási szolgáltatásával automatikusan elindíthatja a "Logic Apps" biztonsági riasztásokra és javaslatokra vonatkozó válaszokat.

A munkafolyamat-automatizálás és a logikai alkalmazások konfigurálása:https://docs.microsoft.com/azure/security-center/workflow-automation


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információ: [Security Control: Penetration Tests and Red Team Exercises](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Rendszeres behatolási tesztelést végez az Azure-erőforrásokon, és 60 napon belül biztosítja az összes kritikus biztonsági megállapítás helyreállítását

**Útmutató:** Kövesse a Microsoft kötelezettségvállalási szabályzatát annak érdekében, hogy a behatolási tesztek ne sértsék meg a Microsoft irányelveit:https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

A Microsoft stratégiájáról és a Red Teaming és az élő webhely behatolási tesztelésének végrehajtásáról a Microsoft által felügyelt felhőalapú infrastruktúrák, szolgáltatások és alkalmazások terén itt olvashat bővebben:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Megosztott

## <a name="next-steps"></a>További lépések

- Tekintse meg az [Azure biztonsági referenciaértékét](https://docs.microsoft.com/azure/security/benchmarks/overview)
- További információ az [Azure biztonsági alapkonfigurációiról](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
