---
title: A Key Vault Azure-os biztonsági alapkonfigurációja
description: A Key Vault Azure-os biztonsági alapkonfigurációja
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 6e660c1244dd5566fbfb45a6da37d39294354ccb
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756984"
---
# <a name="azure-security-baseline-for-key-vault"></a>A Key Vault Azure-os biztonsági alapkonfigurációja

Az Azure Security Baseline for Key Vault javaslatokat tartalmaz, amelyek segítenek javítani a központi telepítés biztonsági állapotát.

A szolgáltatás alapkonfigurációja az [Azure Security Benchmark 1.0-s verziójából](https://docs.microsoft.com/azure/security/benchmarks/overview)származik, amely javaslatokat tartalmaz arra vonatkozóan, hogy miként biztosíthatja felhőalapú megoldásait az Azure-ban az ajánlott eljárásokra vonatkozó útmutatónkkal.

További információ: [Azure Security Baselines overview](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Hálózati biztonság

*További információt a [Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)című témakörben talál.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Erőforrások védelme hálózati biztonsági csoportokkal vagy az Azure tűzfallal a virtuális hálózaton

**Útmutató:** Integrálja az Azure Key Vaultot az Azure Private Linkkel. 

Az Azure Private Link Service lehetővé teszi az Azure-szolgáltatások (például az Azure Key Vault) és az Azure által üzemeltetett ügyfél-/partnerszolgáltatások elérését a virtuális hálózat privát végpontján keresztül.

Az Azure Private Endpoint egy olyan hálózati felület, amely privát és biztonságos kapcsolatot biztosít az Azure Private Link által működtetett szolgáltatással. A privát végpont egy privát IP-címet használ a virtuális hálózatból, hatékonyan hozza a szolgáltatást a virtuális hálózatba. A szolgáltatáshoz irányuló összes forgalom átirányítható a privát végponton keresztül, így nincs szükség átjárókra, NAT-eszközökre, ExpressRoute- vagy VPN-kapcsolatokra vagy nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Csatlakozhat egy Azure-erőforrás egy példányához, így a legmagasabb szintű részletességet biztosíthatja a hozzáférés-vezérlésben.

A Key Vault integrálása az Azure Private Linkkel:

https://docs.microsoft.com/azure/key-vault/private-link-service


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: A virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** Használja az Azure Security Centert, és kövesse a hálózatvédelmi javaslatokat a Key Vault által konfigurált erőforrások azure-beli védelméhez. 

Az Azure Security Center által biztosított hálózati biztonságról további információt talál: 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1.3: Védje a kritikus webes alkalmazásokat

**Útmutatás**: Nem alkalmazható; ez a javaslat az Azure App Service-en vagy számítási erőforrásokon futó webalkalmazásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert rosszindulatú IP-címekkel folytatott kommunikáció megtagadása

**Útmutató:** Engedélyezze az Azure DDoS Protection Standard ot a Key Vault-példányokkal társított Azure virtuális hálózatokon az elosztott szolgáltatásmegtagadási támadások elleni védelem érdekében. Az Azure Security Center integrált fenyegetésfelderítési szolgáltatásával megtagadhatja az ismert rosszindulatú vagy nem használt internetes IP-címekkel folytatott kommunikációt.

 
Az Azure DDoS Protection Standard kezelése az Azure Portalhasználatával:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Az Azure Biztonsági központban az Azure-szolgáltatási réteg fenyegetésészlelése:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Hálózati csomagok és folyamatnaplók rögzítése

**Útmutatás: Az**Azure Key Vault nem használ hálózati biztonsági csoportokat (NSG) és az Azure Key Vault folyamatnaplóit nem rögzítik. Ehelyett az Azure Private Link használatával biztonságossá az Azure Key Vault-példányok, és lehetővé teszi a diagnosztikai beállításokat a metrikák rögzítésére és a naplózási események.

Integrálja a Key Vaultot az Azure Privát hivatkozással:

https://docs.microsoft.com/azure/key-vault/private-link-service

Azure Key Vault naplózása:https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózati behatolásészlelő/behatolás-megelőző rendszerek (IDS/IPS) telepítése

**Útmutató:** Ez a követelmény az Azure Key Vault speciális veszélyforrások elleni védelmének (ATP) konfigurálásával teljesíthető. Az ATP a biztonsági intelligencia további rétegét biztosítja. Ez az eszköz észleli az Azure Key Vault-fiókok elérésére vagy kihasználására irányuló potenciálisan káros kísérleteket.

Amikor az Azure Security Center észleli a rendellenes tevékenység, riasztásokat jelenít meg. Emellett e-mailben az előfizetés rendszergazdája a gyanús tevékenység részleteit, és ajánlásokat, hogyan vizsgálja meg és orvosolja az azonosított fenyegetéseket.

Speciális veszélyforrások elleni védelem beállítása az Azure Key Vaulthoz:

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1.7: A webes alkalmazások forgalmának kezelése

**Útmutatás**: Nem alkalmazható; ez a javaslat az Azure App Service-en vagy számítási erőforrásokon futó webalkalmazásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** Az Azure Key Vault-példányokhoz hozzáférést igénylő erőforrásokhoz használja az Azure Key Vault Azure-szolgáltatáscímkéit a hálózati biztonsági csoportok vagy az Azure firewall hálózati hozzáférés-vezérlésének meghatározásához. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatáscímke nevének (pl. ApiManagement) megadásával a szabály megfelelő forrás- vagy célmezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásakor.

Az Azure szolgáltatáscímkék áttekintése:https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációinak karbantartása

**Útmutató:** Az Azure Key Vault-példányokhoz társított hálózati erőforrások szabványos biztonsági konfigurációinak definiálása és megvalósítása az Azure Policy használatával. A "Microsoft.KeyVault" és a "Microsoft.Network" névterekben az Azure Key Vault-példányok hálózati konfigurációjának naplózására vagy kényszerítésére egyéni szabályzatok létrehozásához használjon Azure Policy aliasokat. Az Azure Key Vaulthoz kapcsolódó beépített szabályzatdefiníciókat is használhatja, például:

A Key Vaultnak virtuális hálózati szolgáltatás végpontját kell használnia

Oktatóanyag: Szabályzatok létrehozása és kezelése a megfelelőség kényszerítéséhez:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure-szabályzatminták:

https://docs.microsoft.com/azure/governance/policy/samples/#networ

Rövid útmutató: Tervrajz definiálása és hozzárendelése a portálon:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Az Azure Key Vault-példányok hálózati biztonsággal és forgalomforgalommal kapcsolatos erőforrások címkéivel metaadatok és logikai szervezet biztosításához használjon címkéket.

Használja a címkézéssel kapcsolatos beépített Azure-szabályzat-definíciók bármelyikét, például a "Címke és annak értéke" használatával győződjön meg arról, hogy minden erőforrás címkékkel jön létre, és értesítheti a meglévő címkézetlen erőforrásokról.

Használhatja az Azure PowerShell vagy az Azure CLI keresni, vagy műveleteket hajt végre az erőforrások alapján a címkéket.

Címkék használatával rendszerezheti azure-erőforrásait:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrás-konfigurációk figyelésére és a változások észlelésére

**Útmutató:** Az Azure-tevékenységnapló segítségével figyelheti a hálózati erőforrás-konfigurációkat, és észlelheti az Azure Key Vault-példányokkal kapcsolatos hálózati erőforrások változásait. Hozzon létre riasztásokat az Azure Monitoron belül, amelyek a kritikus hálózati erőforrások módosításakor aktiválódnak.

Az Azure-tevékenységnapló eseményeinek megtekintése és beolvasása:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Tevékenységnapló-riasztások létrehozása, megtekintése és kezelése az Azure Monitor használatával:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Security Control: Logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Jóváhagyott időszinkronizációs források használata

**Útmutatás**: Nem alkalmazható; A Microsoft fenntartja az Azure-erőforrásokhoz, például az Azure Key Vaulthoz használt időforrást a naplókban lévő időbélyegek hez.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: A központi biztonsági napló kezelésének konfigurálása

**Útmutató:** Az Azure-figyelőn keresztüli naplók betöltése az Azure Key Vault által létrehozott biztonsági adatok összesítéséhez. Az Azure Monitoron belül az Azure Log Analytics-munkaterület használatával lekérdezheti és elvégezheti az elemzéseket, és használhatja az Azure Storage-fiókokat a hosszú távú/archiválási tároláshoz. Másik lehetőségként engedélyezheti és a fedélzeti adatok at Azure Sentinel vagy egy harmadik fél SIEM. 

Azure Key Vault naplózása:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

Rövid útmutató: Az Azure Sentinel fedélzeti szolgáltatása:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Az Azure-erőforrások naplózásának engedélyezése

**Útmutató:** Engedélyezze a diagnosztikai beállításokat az Azure Key Vault-példányokon a naplózási, biztonsági és diagnosztikai naplók eléréséhez. Az automatikusan elérhető tevékenységnaplók közé tartozik az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscímek, a célcímek és egyéb hasznos elemek.

Azure Key Vault naplózása:

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Biztonsági naplók gyűjtése az operációs rendszerekről

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2.5: A biztonsági napló tárolásának megőrzése

**Útmutató:** Az Azure Monitoron belül az Azure Key Vault-naplók tárolására használt Log Analytics-munkaterülethez állítsa be a megőrzési időszakot a szervezet megfelelőségi szabályzatainak megfelelően. Azure Storage-fiókok használata hosszú távú/archiválási tároláshoz.

Az adatmegőrzési időszak módosítása:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és felülvizsgálata

**Útmutató:** Elemezheti és figyelheti a naplókat a rendellenes viselkedéshez, és rendszeresen tekintse át az Eredményeket az Azure Key Vault által védett erőforrásokhoz. Az Azure Monitor Log Analytics-munkaterületével tekintse át a naplókat, és hajtson végre lekérdezéseket a naplóadatokon. Másik lehetőségként engedélyezheti és a fedélzeti adatok at Azure Sentinel vagy egy harmadik fél SIEM. 

Rövid útmutató: Fedélzeti Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Ismerkedés a Log Analytics szolgáltatással az Azure Monitorban:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

A naplólekérdezések első lépései az Azure Monitorban:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Riasztás engedélyezése rendellenes tevékenységre

**Útmutató:** Az Azure Security Centerben engedélyezze a speciális veszélyforrások elleni védelmet (ATP) a Key Vault számára. Engedélyezze a diagnosztikai beállításokat az Azure Key Vaultban, és küldjön naplókat egy Log Analytics-munkaterületre. A Log Analytics-munkaterület et az Azure Sentinelbe, mivel egy biztonsági vezénylési automatikus válasz (SZÁRNYALÁs) megoldást biztosít. Ez lehetővé teszi a forgatókönyvek (automatizált megoldások) létrehozását és a biztonsági problémák elhárítását.

Rövid útmutató: Fedélzeti Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Biztonsági riasztások kezelése és megválaszolása az Azure Security Centerben:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Eseményekre való válaszadás az Azure Monitor-riasztások segítségével:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2.8: A kártevőirtó naplózás központosítása

**Útmutatás**: Nem alkalmazható; Az Azure Key Vault nem dolgozza fel és nem készít kártevők elleni kapcsolódó naplókat.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2.9: Dns-lekérdezésnaplózás engedélyezése

**Útmutatás**: Nem alkalmazható; Az Azure Key Vault nem dolgozza fel és nem hoz létre DNS-sel kapcsolatos naplókat.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2.10: Parancssori naplózás engedélyezése

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt a [Biztonságvezérlés: Identitás- és hozzáférés-vezérlés](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Az adminisztratív számlák leltárának fenntartása

**Útmutató:** Leltárt tartaz Azure Active Directory ban regisztrált alkalmazások, valamint az Azure Key Vault-kulcsokhoz, titkos kulcsokhoz és tanúsítványokhoz hozzáféréssel rendelkező felhasználói fiókok ról. Használhatja az Azure Portalon vagy a PowerShell a Key Vault-hozzáférés lekérdezéséhez és egyeztetéséhez. A PowerShell-hozzáférés megtekintéséhez használja a következő parancsot:

(Get-AzResource -ResourceId [KeyVaultResourceID]). Properties.AccessPolicies

Alkalmazás regisztrálása az Azure Active Directoryval:

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

Biztonságos hozzáférés a kulcstartóhoz:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Adott esetben változtassa meg az alapértelmezett jelszavakat

**Útmutatás**: Nem alkalmazható; Az Azure Key Vault nem rendelkezik az alapértelmezett jelszavak fogalmával, mivel a hitelesítést az Active Directory biztosítja, és a szerepköralapú hozzáférés-vezérlés biztosítja.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált felügyeleti fiókok használata

**Útmutató:** Hozzon létre szabványos működési eljárásokat az Azure Key Vault-példányokhoz hozzáféréssel rendelkező dedikált felügyeleti fiókok használatával kapcsolatban. Az Azure Security Center identitás- és hozzáférés-kezelése (jelenleg előzetes verzióban) használatával figyelheti az aktív felügyeleti fiókok számát.

Identitás és hozzáférés figyelése (előzetes verzió):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Egyszeri bejelentkezés (SSO) használata az Azure Active Directoryval

**Útmutató:** Az AppId, TenantID és ClientSecret használatával egy Azure-szolgáltatásnév használatával zökkenőmentesen hitelesítheti az alkalmazást, és lekérheti az Azure Key Vault-titkos kulcsok eléréséhez használt jogkivonatot.

Szolgáltatás-szolgáltatás hitelesítés az Azure Key Vault számára a .NET használatával:

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató:** Engedélyezze az Azure Active Directory többtényezős hitelesítését, és kövesse az Azure Security Center identitás- és hozzáférés-kezelési (jelenleg előzetes verzióban elérhető) ajánlásait az Event Hub-kompatibilis erőforrások védelme érdekében.

Felhőalapú Azure többtényezős hitelesítési telepítés megtervezése:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identitás és hozzáférés figyelése (előzetes verzió):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Használjon dedikált gépeket (kiemelt hozzáférésű munkaállomásokat) minden adminisztratív feladathoz

**Útmutató:** Használjon emelt szintű hozzáférésű munkaállomás (PAW) az Azure többtényezős hitelesítés (MFA) konfigurálva, hogy jelentkezzen be, és konfigurálja a Key Vault-kompatibilis erőforrásokat. 

Kiemelt hozzáférésű munkaállomások:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations 

Felhőalapú Azure többtényezős hitelesítési telepítés megtervezése:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: A gyanús tevékenységek naplózása és riasztása az adminisztratív fiókokból

**Útmutató:** Használja az Azure Active Directory (AAD) kiemelt identitáskezelés (PIM) a naplók és riasztások létrehozásához, ha gyanús vagy nem biztonságos tevékenység történik a környezetben. Az AAD-kockázatészlelések használatával megtekintheti a riasztásokat és a kockázatos felhasználói viselkedésről szóló jelentéseket. További naplózáshoz küldjön az Azure Security Center kockázatészlelési riasztásait az Azure Monitorba, és konfigurálja az egyéni riasztási/értesítési beállításokat a műveletcsoportok használatával.

Engedélyezze a speciális veszélyforrások elleni védelmet (ATP) az Azure Key Vault számára, hogy riasztásokat generáljon a gyanús tevékenységekhez.

Az Azure AD kiemelt identitáskezelés (PIM) telepítése:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Speciális veszélyforrások elleni védelem beállítása az Azure Key Vaulthoz (előzetes verzió):https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Riasztások az Azure Key Vault (előzetes verzió):https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Az Azure Active Directory kockázatészlelései:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Műveletcsoportok létrehozása és kezelése az Azure Portalon:https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** Konfigurálja a feltételes hozzáférési házirend helyfeltételét, és kezelje a megnevezett helyeket. Az elnevezett helyek segítségével logikai csoportosításokat hozhat létre AZ IP-címtartományokból vagy országokból és régiókból. Korlátozhatja a hozzáférést a bizalmas erőforrásokhoz, például a Key Vault titkos kulcsaihoz a konfigurált elnevezett helyekre.

Mi a helyfeltétel az Azure Active Directory feltételes hozzáférésében?:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3.9: Az Azure Active Directory használata

**Útmutató:** Használja az Azure Active Directory (AAD) az Azure-erőforrások, például a Key Vault központi hitelesítési és engedélyezési rendszerként. Ez lehetővé teszi a szerepköralapú hozzáférés-vezérlés (RBAC) a bizalmas erőforrások adminisztrálása.

 

Rövid útmutató: Hozzon létre egy új bérlőt az Azure Active Directoryban:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rendszeresen vizsgálja felül és egyeztetje össze a felhasználói hozzáférést

**Útmutató:** Tekintse át az Azure Active Directory (AAD) naplók at az elavult fiókok azure key vault felügyeleti szerepkörök felderítéséhez. Emellett az AAD-hozzáférési felülvizsgálatok használatával hatékonyan kezelheti a csoporttagságokat, az Azure Key Vault eléréséhez használható vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférést rendszeresen, például 90 naponta felül kell vizsgálni, hogy csak a megfelelő felhasználók férhessenek hozzá.

Az Azure Active Directory jelentései és figyelési dokumentációja:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Mik azok az Azure AD-hozzáférési felülvizsgálatok?:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Az inaktivált fiókok elérésére tett kísérletek figyelése

**Útmutató:** Engedélyezze az Azure Key Vault és az Azure Active Directory diagnosztikai beállításait, és küldje el az összes naplót egy Log Analytics-munkaterületre. Konfigurálja a kívánt riasztásokat (például a letiltott titkos kulcsok elérésére tett kísérleteket) a Log Analytics szolgáltatásban.

Integrálja az Azure AD-naplókat az Azure Monitor-naplókkal:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Áttelepítés a régi Key Vault-megoldásból:https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltéréséről

**Útmutató:** Az Azure Active Directory identitásvédelmi és kockázatészlelési szolgáltatásaival konfigurálhatja az Azure Key Vault által védett erőforrásokkal kapcsolatos észlelt gyanús műveletekre adott automatikus válaszokat. Az Automatikus válaszokat az Azure Sentinelen keresztül engedélyeznie kell a szervezet biztonsági válaszainak megvalósításához. 

Kockázatos bejelentkezések jelentés az Azure Active Directory portálon:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

Útmutató: Kockázati házirendek konfigurálása és engedélyezése:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Az Azure Sentinel fedélzeti szolgáltatása:https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: A Microsoft hozzáférésének biztosítása a releváns ügyféladatokhoz támogatási forgatókönyvek során

**Útmutatás**: Nem alkalmazható; Az Ügyfélszéf nem támogatott az Azure Key Vault számára.

Támogatott szolgáltatások és forgatókönyvek általános anammár elérhetősége:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

## <a name="data-protection"></a>Adatvédelem

*További információ: [Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Az érzékeny információk leltárának karbantartása

**Útmutató:** Címkék használatával nyomon követheti az Azure-erőforrásokat, amelyek bizalmas információkat tárolnak vagy dolgoznak fel az Azure Key Vault-kompatibilis erőforrásokon. 

Címkék használatával rendszerezheti azure-erőforrásait:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Érzékeny információkat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Biztonságos hozzáférést biztosíthat az Azure Key Vaulthoz az adott alhálózatokhoz való hozzáférés korlátozására konfigurált virtuális hálózati szolgáltatásvégpontok használatával.

A tűzfalszabályok érvénybe lépése után csak akkor hajthat végre Azure Key Vault adatsík-műveleteket, ha a kérés engedélyezett alhálózatokból vagy IP-címtartományokból származik. Ez az Azure Key Vault-hozzáférésre is vonatkozik az Azure Portalon. Bár az Azure Portalon lévő kulcstartót böngészheti, előfordulhat, hogy nem tudja listázza a kulcsokat, titkos kulcsokat vagy tanúsítványokat, ha az ügyfélgép nem szerepel az engedélyezett listán. Ez hatással van az Azure Key Vault-választóra és más Azure-szolgáltatásokra is. Előfordulhat, hogy láthatja a Key Vaults listáit, de a kulcsokat nem, ha a tűzfalszabályok megakadályozzák az ügyfélgép ezt.

Konfigurálja az Azure Key Vault tűzfalait és virtuális hálózatait:https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Virtuális hálózati szolgáltatás végpontjai az Azure Key Vaulthoz:https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: A bizalmas adatok jogosulatlan továbbításának figyelése és blokkolása

**Útmutató:** Az Azure Key Vaultban tárolt összes adat bizalmasnak minősül. Az Azure Key Vault adatsík-hozzáférési vezérlőivel szabályozhatja az Azure Key Vault titkos kulcsaihoz való hozzáférést. A Key Vault beépített tűzfala segítségével is szabályozhatja a hálózati rétegen lévő hozzáférést. Az Azure Key Vault elérésének figyeléséhez engedélyezze a Key Vault diagnosztikai beállításait, és küldjön naplókat egy Azure Storage-fiókba vagy a Log Analytics-munkaterületre.

Biztonságos hozzáférés a kulcstartóhoz:https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

Konfigurálja az Azure Key Vault tűzfalait és virtuális hálózatait:https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure Key Vault naplózása:https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az összes bizalmas információ titkosítása szállítás közben

**Útmutató:** Az Azure Key Vaultba a hitelesítéshez, a felügyeleti és az adatsík-hozzáféréshez irányuló összes forgalom titkosítva van, és HTTPS-en keresztül megy: 443-as port. (A visszavont tanúsítványok listájának azonban időnként http[80-as port] forgalma lesz.) 

Az Azure Key Vault elérése tűzfal mögött:https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutatás**: Nem alkalmazható; az Azure Key Vaultban lévő összes adat (titkos kulcsok, kulcsok és tanúsítványok) bizalmasnak minősül.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Az Azure RBAC használatával szabályozhatja az erőforrásokhoz való hozzáférést

**Útmutató:** Biztonságos hozzáférés az Azure Key Vault-példányok felügyeleti és adatsíkjához.

Biztonságos hozzáférés a kulcstartóhoz:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: A hozzáférés-vezérlés kényszerítése gazdagépalapú adatveszteség-megelőzéssel

**Útmutató: A**Microsoft kezeli az Azure Key Vault alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy expozíciójának megelőzése érdekében.

Mi az Azure Key Vault?

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Az Azure-ügyfelek adatainak védelme:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas információk titkosítása nyugalmi

**Útmutató:** Az azure Key Vault ban minden felügyelt objektum (kulcs, tanúsítvány és titkos kulcs) titkosítva van.

Alátámasztó dokumentáció:

- [Titkosítási modell és kulcskezelési tábla](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#encryption-model-and-key-management-table)


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Naplózza és figyelmeztesse a kritikus Azure-erőforrások változásait

**Útmutató:** Az Azure Key Vault Analytics-megoldás az Azure Monitorban az Azure Key Vault naplózási eseménynaplók áttekintéséhez.

Azure Key Vault Analytics-megoldás az Azure Monitorban:

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információt a [Biztonsági ellenőrzés: Biztonsági rés kezelése című](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)témakörben talál.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Automatikus biztonsági rés-ellenőrző eszközök futtatása

**Útmutató: A**Microsoft biztonsági réskezelést hajt végre az Azure Key Vaultot támogató mögöttes rendszereken.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Az operációs rendszer automatikus javításkezelési megoldásának telepítése

**Útmutató**: N/A; A Microsoft a Key Vault ot támogató mögöttes rendszereken végez javításkezelést.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Automatizált, harmadik féltől származó szoftverjavítás-kezelési megoldás telepítése

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Hasonlítsa össze a biztonsági rés vizsgálatait

**Útmutató: A**Microsoft biztonsági réskezelést hajt végre a Key Vaultot támogató mögöttes rendszereken.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Kockázatminősítési eljárás használata a felfedezett biztonsági rések helyreállításának rangsorolására

**Útmutatás:** Használja az Azure Security Center által biztosított alapértelmezett kockázati minősítéseket (Secure Score).

Javítsa biztonságos pontszámát az Azure Security Centerben:

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Az Azure Asset Discovery használata

**Útmutatás:** Az Azure Resource Graph használatával lekérdezheti és felderítheti az összes erőforrást (beleértve az Azure Key Vault-példányokat is) az előfizetésen belül. Győződjön meg arról, hogy rendelkezik a megfelelő (olvasási) engedélyekkel a bérlőben, és képes az összes Azure-előfizetések és az előfizetéseken belüli erőforrások számbavételét.

Rövid útmutató: Futtassa az első Resource Graph-lekérdezést az Azure Resource Graph Explorer használatával:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Az aktuális fiók által elérhető előfizetések beszerezhetők.:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Mi az Azure-erőforrásokhoz (RBAC) való szerepköralapú hozzáférés-vezérlés?

https://docs.microsoft.com/azure/role-based-access-control/overview

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6.2: Az eszközök metaadatainak karbantartása

**Útmutató: Címkék**alkalmazása az Azure Key Vault-erőforrásokra, amelyek metaadatokat adnak, hogy logikusan rendszerezzék őket egy taxonómiába.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Jogosulatlan Azure-erőforrások törlése

**Útmutató:** Adott esetben tagging, felügyeleti csoportok és külön előfizetések használatával rendszerezheti és nyomon követheti az Azure Key Vault-példányokat és a kapcsolódó erőforrásokat. A készlet rendszeres egyeztetése és a jogosulatlan erőforrások időben történő törlése az előfizetésből.

Hozzon létre egy további Azure-előfizetést:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Felügyeleti csoportok létrehozása erőforrás-szervezéshez és -kezeléshez:

https://docs.microsoft.com/azure/governance/management-groups/create

Címkék használatával rendszerezheti azure-erőforrásait:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: A jóváhagyott Azure-erőforrások és szoftvercímek leltárának karbantartása

**Útmutató:** A jóváhagyott Azure-erőforrások és a számítási erőforrások jóváhagyott szoftvereinek listájának meghatározása

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: A nem jóváhagyott Azure-erőforrások figyelése

**Útmutató:** Az Azure-szabályzatok használatával korlátozásokat helyezhet el az ügyfél-előfizetés(ek)ben létrehozható erőforrások típusára vonatkozóan a következő beépített szabályzatdefiníciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával lekérdezheti/felderítheti az erőforrásokat az előfizetés(ek)en belül.

Oktatóanyag: Szabályzatok létrehozása és kezelése a megfelelőség kényszerítéséhez:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Rövid útmutató: Futtassa az első Resource Graph-lekérdezést az Azure Resource Graph Explorer használatával:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: A nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: A nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutatás**: Nem alkalmazható; ez a javaslat az Azure egészére, valamint a számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="68-use-only-approved-applications"></a>6.8: Csak jóváhagyott alkalmazásokat használjon

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató:** Az Azure-szabályzatok használatával korlátozásokat helyezhet el az ügyfél-előfizetés(ek)ben létrehozható erőforrások típusára vonatkozóan a következő beépített szabályzatdefiníciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Oktatóanyag: Szabályzatok létrehozása és kezelése a megfelelőség kényszerítéséhez:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure-szabályzatminták:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="610-implement-approved-application-list"></a>6.10: A jóváhagyott pályázati lista végrehajtása

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Korlátozza a felhasználók azon képességét, hogy parancsfájlokon keresztül kommunikáljanak az AzureResources Managerrel

**Útmutató:** Az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók azon képességét, hogy az Azure Resource Manager (ARM) használatával konfigurálja a "Blokk hozzáférés" a "Microsoft Azure Management" app. Ez megakadályozhatja a magas biztonsági szintű környezetben, például a Key Vault-konfigurációval rendelkező erőforrások létrehozását és módosításait.

Az Azure-kezeléshez való hozzáférés kezelése feltételes hozzáféréssel:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Korlátozza a felhasználók azon képességét, hogy parancsfájlokat hajtsanak végre a számítási erőforrásokon belül

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizikailag vagy logikailag elkülönítve a magas kockázatú alkalmazásokat

**Útmutatás**: Nem alkalmazható; ez a javaslat az Azure App Service-en vagy számítási erőforrásokon futó webalkalmazásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt a [Biztonsági ellenőrzés: Biztonságos konfiguráció című](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** Azure-szabályzat aliasok a "Microsoft.KeyVault" névtérben egyéni szabályzatok létrehozásához az Azure Key Vault-példányok naplózása vagy kényszerítése. Az Azure Key Vault beépített Azure-szabályzat-definícióit is használhatja, például:

A Key Vault-objektumoknak helyreállíthatónak kell lenniük

A Key Vault diagnosztikai beállításainak telepítése a Log Analytics-munkaterületre

A Key Vault diagnosztikai naplóit engedélyezni kell

A Key Vaultnak virtuális hálózati szolgáltatás végpontját kell használnia

A Key Vault diagnosztikai beállításainak telepítése az Event Hubra

Az Azure Security Center ajánlásait biztonságos konfigurációs alapkonfigurációként használhatja az Azure Key Vault-példányokhoz.

Az elérhető Azure Policy-aliasok megtekintése:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Oktatóanyag: Szabályzatok létrehozása és kezelése a megfelelőség kényszerítéséhez:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató:** Használja az Azure-szabályzat [megtagadás] és a [üzembe helyezés, ha nem létezik] az Azure Key Vault-kompatibilis erőforrások biztonságos beállításainak kényszerítéséhez. 

Oktatóanyag: Szabályzatok létrehozása és kezelése a megfelelőség kényszerítéséhez:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
Az Azure-szabályzat hatásainak ismertetése: 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Biztonságos operációsrendszer-konfigurációk karbantartása

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások biztonságos tárolása

**Útmutató:** Ha egyéni Azure-szabályzat-definíciókat használ az Azure Key Vault-kompatibilis erőforrásokhoz, használja az Azure Repos-t a kód biztonságos tárolásához és kezeléséhez.

Kód tárolása az Azure DevOps-ban: 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 

Azure Repos dokumentáció: 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Biztonságosan tárolhatja az operációs rendszer egyedi lemezképeit

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Rendszerkonfiguráció-kezelő eszközök telepítése

**Útmutató:** A "Microsoft.KeyVault" névtérben az Azure Policy aliasok használatával egyéni szabályzatokat hozhat létre a rendszerkonfigurációk riasztására, naplózására és kényszerítésére. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirendkivételek kezeléséhez.

Az Azure-szabályzat konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Rendszerkonfiguráció-kezelő eszközök telepítése operációs rendszerekhez

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Automatikus konfigurációfigyelés megvalósítása az Azure-szolgáltatásokhoz

**Útmutató: Az**Azure Security Center használatával alapszintű vizsgálatokat végezhet az Azure Key Vault által védett erőforrásokhoz 

  

Javaslatok kijavítása az Azure Security Centerben: 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Az operációs rendszerek automatizált konfigurációs figyelésének megvalósítása

**Útmutatás**: Nem alkalmazható; ez a referenciaérték számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="711-manage-azure-secrets-securely"></a>7.11: Az Azure-titkok biztonságos kezelése

**Útmutató:** A felügyelt szolgáltatásidentitás az Azure Key Vaulttal együtt egyszerűsítése és biztonságos sápkezelése a felhőalapú alkalmazások. Győződjön meg arról, hogy az Azure Key Vault helyreállítható törlés engedélyezve van.

Integrálás az Azure felügyelt identitásaival:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Key Vault létrehozása:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

A Kulcstartó hitelesítése felügyelt identitással: 

https://docs.microsoft.com/azure/key-vault/managed-identity

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: A személyazonosságok biztonságos és automatikus kezelése

**Útmutató:** A felügyelt szolgáltatásidentitás az Azure Key Vaulttal együtt egyszerűsítése és biztonságos sápkezelése a felhőalapú alkalmazások. 

  

Integrálás az Azure felügyelt identitásaival: 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity  

Key Vault létrehozása: 

https://docs.microsoft.com/azure/key-vault/quick-create-portal    

A Kulcstartó hitelesítése felügyelt identitással:  
https://docs.microsoft.com/azure/key-vault/managed-identity

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

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál. A Microsoft kezeli a kártevőirtót az alapul szolgáló platformhoz.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltöltendő fájlok előzetes beszkéselése

**Útmutató: A**Microsoft kártevőirtó eszköz e-alapú gazdagépen engedélyezve van az Azure-szolgáltatásokat támogató gazdagépen (például az Azure Key Vaultban), azonban nem fut az ügyféltartalomon.

A nem számítási Azure-erőforrásokba, például az Azure Key Vaultba feltöltött vagy küldött tartalmak előzetes beírása. A Microsoft ezekben az esetekben nem fér hozzá az Ön adataihoz.

Ismerje meg a Microsoft kártevőirtó szoftvereit az Azure Felhőszolgáltatásokhoz és a virtuális gépekhez:https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Gondoskodjon a kártevőirtó szoftverek és aláírások frissítéséről

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál. A Microsoft kezeli a kártevőirtót az alapul szolgáló platformhoz.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató:** A következő PowerShell-parancsokkal biztosíthatja a Key Vault-tanúsítványok, kulcsok, felügyelt tárfiókok és titkos kulcsok rendszeres automatikus biztonsági mentését:

- Biztonsági másolat-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Szükség esetén a Key Vault biztonsági mentéseit az Azure Backup ban is tárolhatja.

A kulcstároló-tanúsítványok biztonsági mentése:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

A kulcstároló kulcsainak biztonsági mentése:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

A Kulcstároló felügyelt tárfiókjainak biztonsági mentése:https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

A Key Vault titkainak biztonsági mentése:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Az Azure Backup engedélyezése:https://docs.microsoft.com/azure/backup



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszerbiztonsági mentés végrehajtása és biztonsági mentés bármely ügyfél által kezelt kulcs ról

**Útmutató:** Készítsen biztonsági másolatot a Key Vault-tanúsítványokról, kulcsokról, felügyelt tárfiókokról és titkos kulcsokról a következő PowerShell-parancsokkal:

- Biztonsági másolat-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Szükség esetén a Key Vault biztonsági mentéseit az Azure Backup ban is tárolhatja.

A kulcstároló-tanúsítványok biztonsági mentése:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

A kulcstároló kulcsainak biztonsági mentése:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

A Kulcstároló felügyelt tárfiókjainak biztonsági mentése:https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

A Key Vault titkainak biztonsági mentése:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Az Azure Backup engedélyezése:https://docs.microsoft.com/azure/backup



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági mentés ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** A key vault-tanúsítványok, kulcsok, felügyelt tárfiókok és titkos kulcsok adat-visszaállítása rendszeres időközönként a következő PowerShell-parancsokkal hajthatja végre az adatokat:

- Visszaállítás-AzKeyVaultCertificate

- Visszaállítás-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Visszaállítás-AzKeyVaultSecret

A Key Vault-tanúsítványok visszaállítása:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

A kulcstároló kulcsainak visszaállítása:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

A Key Vault által felügyelt tárfiókok visszaállítása:https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

A Key Vault titkos kulcsainak visszaállítása:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági mentések és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató:** Győződjön meg arról, hogy a helyreállítható törlés engedélyezve van az Azure Key Vault. A helyreállítható törlés lehetővé teszi a törölt kulcstartók és tárolóobjektumok, például kulcsok, titkos kulcsok és tanúsítványok helyreállítását. 

Az Azure Key Vault ideiglenes törlése: 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Security Control: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidenselhárítási útmutató létrehozása

**Útmutató:** Hozzon létre egy incidens-elhárítási útmutatót a szervezet számára. Győződjön meg arról, hogy vannak olyan írásos eseményreagálási tervek, amelyek meghatározzák a személyzet összes szerepkörét, valamint az incidenskezelés/-kezelés fázisait az észleléstől az incidens utáni felülvizsgálatig. Ezeknek a folyamatoknak a bizalmas rendszerek, például a Key Vault-titkos kulcsok at használó rendszerek védelmére kell összpontosítaniuk.

Munkafolyamat-automatizálások konfigurálása az Azure Security Centerben: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

Útmutató a saját biztonsági incidensek reválaszolási folyamatának felépítéséhez:  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

A Microsoft Security Response Center egy incidens anatómia:   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

Az Ügyfél a NIST számítógépes biztonsági incidenskezelési útmutatóját is felhasználhatja, hogy segítse saját eseményreagálási tervének létrehozását: 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és rangsorolási eljárás létrehozása

**Útmutatás:** A Biztonsági központ minden riasztáshoz súlyossági szintet rendel, hogy segítsen rangsorolni, mely riasztásokat kell először kivizsgálni. A súlyosság attól függ, hogy a Security Center mennyire magabiztos a keresésben, vagy a riasztás kiadásához használt analitikus, valamint a riasztáshoz vezető tevékenység mögött rosszindulatú szándék megbízhatósági szintje. Ezenkívül egyértelműen jelölje meg az előfizetéseket (pl. termelés, nem prod), és hozzon létre egy elnevezési rendszert, amely egyértelműen azonosítja és kategorizálja az Azure-erőforrásokat, különösen azokat, amelyek bizalmas adatokat, például az Azure Key Vault titkos titkait dolgoznak fel.


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10.3: A tesztelési biztonsági reagálási eljárások

**Útmutató:** Végezzen gyakorlatokat a rendszerek incidensválasz-képességeinek rendszeres ütemben való teszteléséhez az Azure Key Vault-példányok és a kapcsolódó erőforrások védelme érdekében. Azonosítsa a gyenge pontokat és hiányosságokat, és szükség szerint vizsgálja felül a tervet.

Tekintse meg a NIST kiadványát: Útmutató az informatikai tervekés képességek tesztelési, képzési és edzésprogramjaihoz: 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidensek elérhetőségét, és állítsa be a biztonsági incidensek riasztási értesítéseit

**Útmutató: A**Microsoft a biztonsági incidensek kapcsolattartási adatait arra használja fel, hogy kapcsolatba lépjen Önnel, ha a Microsoft Security Response Center (MSRC) azt észleli, hogy az Ön adataihoz jogosulatlan vagy jogosulatlan fél fért hozzá.  Tekintse át az incidenseket a problémák megoldásának biztosítása érdekében.

Az Azure Security Center biztonsági kapcsolattartójának beállítása:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidenselhárítási rendszerbe

**Útmutató:** Exportálja az Azure Security Center-riasztásokat és javaslatokat a Folyamatos exportálás funkcióval az Azure Key Vault-kompatibilis erőforrásokkal kapcsolatos kockázatok azonosításához. A Folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális exportálását, vagy folyamatos, folyamatos módon.  Használhatja az Azure Security Center adatösszekötőt a riasztások streameléséhez az Azure Sentinel. 

 

A folyamatos exportálás konfigurálása: 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

Értesítések streamelése az Azure Sentinelbe: 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztásokra adott válasz automatizálása

**Útmutató:** Az Azure Security Center munkafolyamat-automatizálási szolgáltatásával automatikusan elindíthatja a "Logic Apps" szolgáltatáson keresztül a biztonsági riasztásokra és az Azure Key Vault által védett erőforrásokvédelmére vonatkozó javaslatokra adott válaszokat. 

 

A munkafolyamat-automatizálás és a logikai alkalmazások konfigurálása: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információ: [Security Control: Penetration Tests and Red Team Exercises](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Rendszeres behatolási tesztelést végez az Azure-erőforrásokon, és 60 napon belül biztosítja az összes kritikus biztonsági megállapítás helyreállítását

**Útmutatás:** Közvetlenül nem végezhet tolltesztelést az Azure Key Vault szolgáltatáson, azonban javasoljuk, hogy tesztelje az Azure-erőforrásokat, amelyek key vault használatával biztosítják a titkos kulcsok biztonságát.

A Microsoft kötelezettségvállalási szabályzatát be kell tartania annak érdekében, hogy a behatolási tesztek ne sértsék meg a Microsoft irányelveit:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

A Microsoft stratégiájáról és a Red Teaming és az élő webhely behatolási tesztelésének végrehajtásáról a Microsoft által felügyelt felhőalapú infrastruktúrák, szolgáltatások és alkalmazások terén itt olvashat bővebben: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Megosztott

## <a name="next-steps"></a>További lépések

- Tekintse meg az [Azure biztonsági referenciaértékét](https://docs.microsoft.com/azure/security/benchmarks/overview)
- További információ az [Azure biztonsági alapkonfigurációiról](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
