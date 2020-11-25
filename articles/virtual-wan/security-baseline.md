---
title: Virtuális WAN Azure biztonsági alapterve
description: A virtuális WAN biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7f75f764f378118f9a34c1eee467e78ac279e19c
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029047"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Virtuális WAN Azure biztonsági alapterve

Ez a biztonsági alapterv az [Azure biztonsági teljesítményteszt 2,0-es verziójáról](../security/benchmarks/overview.md) Microsoft Azure virtuális WAN-ra vonatkozó útmutatást alkalmaz. Az Azure biztonsági teljesítményteszt az Azure-beli felhőalapú megoldások biztonságossá tételével kapcsolatos ajánlásokat tartalmaz. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** és a virtuális WAN-ra vonatkozó kapcsolódó útmutatás szerint van csoportosítva. A virtuális WAN-ra nem alkalmazható **vezérlőelemek** ki lettek zárva.

Ha szeretné megtudni, hogy a virtuális WAN hogyan teljes mértékben leképezi az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes virtuális WAN biztonsági alapkonfiguráció-hozzárendelési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információt az [Azure biztonsági teljesítményteszt: hálózati biztonság](/azure/security/benchmarks/security-controls-v2-network-security)című témakörben talál.*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: a belső forgalom biztonságának megvalósítása

**Útmutató**: a Microsoft Azure Virtual WAN egyéni útválasztási funkciókat biztosít, és titkosítást biztosít a ExpressRoute-forgalmához. Az összes útválasztási kezelést a virtuális központ útválasztója biztosítja, amely a virtuális hálózatok közötti átvitelt is lehetővé teszi. A ExpressRoute-forgalom virtuális WAN-kapcsolaton keresztüli titkosítása titkosított átvitelt biztosít a helyszíni hálózatok és az Azure-beli virtuális hálózatok között a ExpressRoute-en keresztül, a nyilvános interneten keresztül vagy nyilvános IP-címek használata nélkül. 

- [ExpressRoute-forgalom titkosítása](virtual-wan-about.md#encryption)

- [Privát hivatkozás használata a virtuális WAN-ban](howto-private-link.md)

- [Egyéni útválasztási forgatókönyvek](scenario-any-to-any.md)

- [Egyéni útválasztási táblázat dokumentációja](how-to-virtual-hub-routing.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="ns-2-connect-private-networks-together"></a>NS-2: magánhálózati hálózatok összekapcsolása 

**Útmutató**: a Microsoft Azure ExpressRoute privát kapcsolatot biztosít az Azure Virtual WAN-ral. Mivel a ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át, a ExpressRoute nagyobb megbízhatóságot, gyorsabb sebességet és kisebb késést biztosít, mint a szokásos internetes kapcsolatok. A virtuális magánhálózat használatával a helyek közötti (S2S) VPN-en vagy a pont – hely (P2S) VPN-en keresztül is csatlakozhat az Azure-hoz.

- [ExpressRoute a virtuális WAN-ban](virtual-wan-expressroute-portal.md)

- [Helyek közötti VPN – áttekintés](virtual-wan-site-to-site-portal.md)

- [Pont – hely VPN – áttekintés](virtual-wan-point-to-site-portal.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: megosztott

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: a külső hálózati támadásoktól származó alkalmazások és szolgáltatások elleni védelem

**Útmutató**: a virtuális WAN nem tesz elérhetővé olyan végpontokat külső hálózatokra, amelyeknek a hagyományos hálózati védelemmel kell rendelkezniük. A Virtual Network Protection Services használatával szabadon biztosíthatja a küllős virtuális hálózatok (virtuális hubhoz csatlakozó virtuális hálózatok) erőforrásainak védelmét. 

A Azure Firewall használatával védelmet biztosíthat az alkalmazások és szolgáltatások számára az internetről és más külső helyekről származó, potenciálisan rosszindulatú forgalom ellen. 

Az Azure által biztosított DDoS Protection az Azure-beli virtuális hálózatokon található támadások elleni védelem érdekében válassza ki az eszközöket. A Azure Security Center használatával azonosíthatja a hálózattal kapcsolatos erőforrásokkal kapcsolatos helytelen konfigurációkat.

- [Azure Firewall dokumentáció](/azure/firewall)

- [Azure DDoS Protection standard kezelése a Azure Portal használatával](/azure/virtual-network/manage-ddos-protection) 

- [Az Azure Security Centerre vonatkozó ajánlások](../security-center/recommendations-reference.md#recs-network)

**Azure Security Center figyelés**: igen

**Felelősség**: megosztott

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: a Virtual WAN egy Microsoft által felügyelt szolgáltatás. Nem nyújt natív behatolás-észlelési vagy Behatolás-megelőzési képességeket. Vannak azonban olyan biztonsági képességek, amelyeket a Azure Firewall a virtuális WAN számára biztosít a házirendek egységes vezérlésének lehetővé tételéhez. Létrehozhat egy Azure Firewall szabályzatot, és összekapcsolhatja a házirendet egy virtuális WAN-hubhoz, hogy a meglévő virtuális WAN-központ biztonságos virtuális központként működjön, és a szükséges Azure Firewall-erőforrások telepítve legyenek.

- [Azure Firewall konfigurálása virtuális WAN-központban](howto-firewall.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: a hálózati biztonsági szabályok egyszerűsítése

**Útmutató**: a hálózati biztonsági szabályok egyszerűsítése Virtual Network szolgáltatás-címkék használatával a hálózati biztonsági csoportok vagy Azure Firewall hálózati hozzáférés-vezérlésének definiálásához. A szolgáltatás címkéi a biztonsági szabályok létrehozásakor a megadott IP-címek helyett használhatók. A szolgáltatási címke nevének megadásával a szabály forrás vagy cél mezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

- [A szolgáltatási címkék megismerése és használata](../virtual-network/service-tags-overview.md)

- [Az alkalmazás biztonsági csoportjai megismerése és használata](/azure/virtual-network/security-overview#application-security-groups)

- [Azure Firewall dokumentáció](/azure/firewall/)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: biztonságos tartománynév szolgáltatás (DNS)

**Útmutató**: a biztonságos DNS-képességek a Azure Firewall használatával érhetők el a virtuális WAN számára. Konfigurálja úgy a Azure Firewallt, hogy DNS-proxyként működjön, amely az ügyfél virtuális gépei által a DNS-kiszolgálóra irányuló DNS-kérések közötti közvetítővé válik. Az egyéni DNS-kiszolgálói konfigurációk esetében engedélyezze a DNS-proxyk számára a DNS-feloldási eltérések elkerülését, és engedélyezze a teljes tartománynevek szűrését a hálózati szabályokban. 

- [Azure Firewall dokumentáció](/azure/firewall/)

- [Azure Firewall DNS-beállítások](/azure/firewall/dns-settings)

- [Azure Firewall használata DNS-továbbítóként privát hivatkozással](https://github.com/adstuart/azure-privatelink-dns-azurefirewall)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="identity-management"></a>Identitáskezelés

*További információt az [Azure biztonsági teljesítményteszt: Identitáskezelés](/azure/security/benchmarks/security-controls-v2-identity-management)című témakörben talál.*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: szabványosított Azure Active Directory központi identitás-és hitelesítési rendszerrel

**Útmutató**: a Azure Active Directory (Azure ad) az Azure-szolgáltatások alapértelmezett identitás-és hozzáférés-kezelési szolgáltatása. a virtuális WAN-t is beleértve. Az Azure AD szabványosítása a szervezet identitás-és hozzáférés-kezelésének szabályozására a ben:

- Microsoft Cloud olyan erőforrásokat, mint például a Azure Portal, az Azure Storage, az Azure Virtual Machines (Linux és Windows), a Azure Key Vault, a szolgáltatásként szolgáló platform, valamint a szolgáltatott szoftver (SaaS) alkalmazások.
- A szervezet erőforrásai, például az Azure-beli alkalmazások vagy a vállalati hálózati erőforrások

Az Azure AD magas prioritást biztosít a szervezet Felhőbeli biztonsági gyakorlatában. Mérje fel az identitást és a biztonságot a Security Center biztonsági pontszám funkciójával, és mérje fel, hogy a konfiguráció milyen mértékben illeszkedik a Microsoft ajánlott eljárási javaslataihoz. Szükség esetén alkalmazza a Microsoft ajánlott eljárásait a biztonsági helyzetének fejlesztéséhez.

Az Azure AD a külső identitásokat is támogatja, amelyek lehetővé teszik a felhasználók számára, hogy Microsoft-fiók nélkül bejelentkezzenek alkalmazásaiba és erőforrásaiba külső identitásával. 

Tekintse át az Azure AD-t a hivatkozott hivatkozásokon a pont – hely VPN-forgatókönyvekben található információk alapján.

- [Azure Active Directory (AD) bérlő létrehozása a P2S OpenVPN protokoll kapcsolataihoz](openvpn-azure-ad-tenant-multi-app.md)

- [Azure Active Directory hitelesítés konfigurálása a felhasználói VPN-hez](virtual-wan-point-to-site-azure-ad.md)

- [Bérlet Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center figyelés**: igen

**Felelősség**: megosztott

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: erős hitelesítési vezérlők használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: jelenleg Azure Active Directory (Azure ad) hitelesítés a virtuális WAN pont – hely típusú VPN-kapcsolattal való integráción keresztül érhető el.

Azure Active Directory (Azure AD) az Azure-szolgáltatások alapértelmezett identitás-és hozzáférés-kezelési szolgáltatása. Az Azure AD a többtényezős hitelesítéssel és erős, jelszóval nem rendelkező módszerekkel támogatja az erős hitelesítési vezérlőket.

Az Azure AD a következőket javasolja az erős hitelesítési vezérlőkhöz:

- Többtényezős hitelesítés – az Azure AD többtényezős hitelesítésének engedélyezése, valamint a Azure Security Center az azonosítási és hozzáférés-kezelési javaslatok követése az ajánlott biztonsági eljárásokhoz. A többtényezős hitelesítés érvényesítése az összes, a felhasználók és a felhasználónkénti szinten a bejelentkezési feltételek és a kockázati tényezők alapján

- Jelszóval nem rendelkező hitelesítés – a rendszer három jelszavas hitelesítési lehetőséget kínál. Ezek közé tartoznak a vállalati Windows Hello, Microsoft Authenticator alkalmazás és a helyszíni hitelesítési módszerek, például az intelligens kártyák

Győződjön meg arról, hogy az erős hitelesítési módszer legmagasabb szintje a rendszergazda és a rendszerjogosultságú felhasználók számára van használatban, és ezt követően egy erős hitelesítési házirendet kell kivonnia más felhasználók számára.

- [Az MFA engedélyezése a P2S VPN-ben virtuális WAN-hoz](openvpn-azure-ad-mfa.md)

**Azure Security Center figyelés**: igen

**Felelősség**: megosztott

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: az Azure erőforrás-hozzáférés korlátozása feltételek alapján

**Útmutató**: engedélyezze Azure Active Directory (Azure ad) többtényezős hitelesítés használatát a VPN-felhasználók (pont – hely) számára az Azure ad-hitelesítés használatával. Többtényezős hitelesítés konfigurálása felhasználónkénti alapon, vagy többtényezős hitelesítés használata feltételes hozzáféréssel. A feltételes hozzáférés lehetővé teszi, hogy finomabban szabályozható legyen a második tényező előléptetése. Lehetővé teheti a többtényezős hitelesítés hozzárendelését csak VPN-re, és kizárhatja az Azure AD-bérlőhöz kötött más alkalmazásokat. 

Vegye figyelembe, hogy az Azure AD-hitelesítés csak az OpenVPN protokollt használó átjárók és a Windows rendszerű ügyfelek esetében érhető el.

- [Mi a feltételes hozzáférés?](../active-directory/conditional-access/overview.md)

- [Azure Active Directory hitelesítés konfigurálása a felhasználói VPN-hez](virtual-wan-point-to-site-azure-ad.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: a nem szándékolt hitelesítő adatok expozíciójának megszüntetése

**Útmutató**: a virtuális WAN-helyek közötti VPN-kapcsolat előmegosztott kulcsokat (PSK) használ, amelyeket az ügyfél a Azure Key Vault felderített, hozott létre és felügyel. Hitelesítő adatok beolvasása a kódban a hitelesítő adatok azonosításához. A hitelesítő adatok beolvasása azt is javasolja, hogy a felderített hitelesítő adatokat biztonságosabb helyszínekre (például Azure Key Vault) helyezze.

A GitHub esetében a natív titkos keresési funkcióval azonosíthatók a kódokon belüli hitelesítő adatok vagy egyéb titkok.

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [GitHub-titkos vizsgálat](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="privileged-access"></a>Emelt szintű hozzáférés

*További információt az [Azure biztonsági teljesítményteszt: privilegizált hozzáférés](/azure/security/benchmarks/security-controls-v2-privileged-access)című témakörben talál.*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: az üzleti szempontból kritikus fontosságú rendszerek rendszergazdai hozzáférésének korlátozása

**Útmutató**: az Azure Virtual WAN az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával elkülöníti az üzleti szempontból kritikus rendszerek hozzáférését azáltal, hogy korlátozza, hogy mely fiókok kapnak jogosultsági szintű hozzáférést az előfizetésekhez és a felügyeleti csoportokhoz.

Korlátozza továbbá az üzleti szempontból kritikus Active Directory-tartomány fontosságú rendszerekre telepített ügynökökkel való rendszergazdai hozzáféréssel rendelkező felügyeleti, identitási és biztonsági rendszerekhez való hozzáférést is. Azok a támadók, akik veszélyeztetik ezeket a felügyeleti és biztonsági rendszereket, azonnal weaponize azokat az üzleti szempontból kritikus fontosságú eszközök biztonsága érdekében.

A hozzáférés-vezérlés összes típusát a vállalati szegmentálási stratégiához kell igazítani, hogy biztosítsa a konzisztens hozzáférés-vezérlést.

- [Azure-összetevők és-hivatkozási modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Felügyeleti csoport elérése](../governance/management-groups/overview.md#management-group-access) 

- [Azure-előfizetés rendszergazdái](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információt az [Azure biztonsági teljesítményteszt: Adatvédelem](/azure/security/benchmarks/security-controls-v2-data-protection)című témakörben talál.*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: bizalmas adatok titkosítása az átvitel során

**Útmutató**: pont – hely VPN, helyek közötti VPN és titkosított expressz útvonal használata a kapcsolati követelményekhez a virtuális WAN használatával. A VPN-titkosítás védi az adatokat a sávon kívüli támadásoktól (például a forgalom rögzítése), így biztosítva, hogy a támadók nem tudják olvasni vagy módosítani az adatokat. 

- [Pont-hely típusú VPN](virtual-wan-point-to-site-portal.md)

- [Helyek közötti VPN](virtual-wan-site-to-site-portal.md)

- [Titkosított ExpressRoute](vpn-over-expressroute.md)

**Azure Security Center figyelés**: igen

**Felelősség**: megosztott

## <a name="asset-management"></a>Asset Management (Eszközkezelés)

*További információt az [Azure biztonsági teljesítményteszt: Asset Management](/azure/security/benchmarks/security-controls-v2-asset-management)című témakörben talál.*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Győződjön meg arról, hogy a biztonsági csapat az eszközök kockázataival rendelkezik

**Útmutató**: gondoskodjon arról, hogy a biztonsági csapatok biztonsági olvasói engedélyeket kapjanak az Azure-bérlőben és-előfizetésekben, hogy a biztonsági kockázatokat a Azure Security Center használatával tudják figyelni. 

A biztonsági csapat feladatainak strukturálása, a biztonsági kockázatok figyelése a központi biztonsági csapat vagy egy helyi csapat feladata lehet. Ezzel a biztonsági ismereteket és a kockázatokat mindig központilag összesíteni kell egy szervezeten belül. 

A biztonsági olvasó engedélyei széles körben alkalmazhatók egy teljes bérlőre (gyökérszintű felügyeleti csoportra), vagy a felügyeleti csoportokra vagy az adott előfizetésekre is. 

Megjegyzés: Előfordulhat, hogy további engedélyek szükségesek a számítási feladatok és szolgáltatások láthatóságának megismeréséhez. 

- [A biztonsági olvasó szerepkör áttekintése](../role-based-access-control/built-in-roles.md#security-reader)

- [Az Azure Management Groups áttekintése](../governance/management-groups/overview.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: gondoskodjon arról, hogy a biztonsági csapat hozzáférjen az eszközök leltárához és metaadataihoz

**Útmutató**: címkék alkalmazása az Azure-erőforrásokra,-csoportokra és-előfizetésekre, amelyek logikailag rendszerezik őket egy besorolásban. Minden címke egy nevet és egy érték párokat tartalmaz. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra. Az Azure Virtual WAN olyan Azure Resource Manager-alapú erőforrás-telepítéseket is támogat, amelyekkel exportálhatja az eszközök sablonjait. 

- [Útmutató az erőforrások elnevezésével és címkézésével kapcsolatos döntésekhez](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure monitor használatával hozzon létre szabályokat a riasztások elindításához, ha a rendszer nem jóváhagyott szolgáltatást észlel. A virtuális WAN számos hálózati, biztonsági és útválasztási funkciót egyesít, hogy egyetlen operatív felületet biztosítson. A virtuális WAN VPN-átjárók, a ExpressRoute-átjárók és a Azure Firewall Azure Monitoron keresztül elérhetők naplózással és metrikákkal. 
 

- [Virtuális WAN-naplók és-metrikák](logs-metrics.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek az Azure erőforrás-kezelővel való interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="logging-and-threat-detection"></a>Naplózás és fenyegetések észlelése

*További információt az [Azure biztonsági teljesítményteszt: naplózás és fenyegetések észlelése](/azure/security/benchmarks/security-controls-v2-logging-threat-protection)című témakörben talál.*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: az Azure-erőforrások fenyegetés-észlelésének engedélyezése

**Útmutató**: a virtuális WAN-t használó pont – hely VPN integrálva van Azure Active Directory (Azure ad) szolgáltatással. Az Azure AD a következő felhasználói naplókat tartalmazza, amelyeket megtekintheti az Azure AD Reporting szolgáltatásban, vagy integrálható a Azure Monitor, az Azure Sentinel, a SIEM vagy a monitoring eszközeivel összetettebb veszélyforrások figyelésére és elemzési használatára. Ezek a következők:

- Bejelentkezés – a bejelentkezési jelentés információt nyújt a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról.
- Auditnaplók – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplók példái közé tartoznak az Azure AD-ban található erőforrások változásai, például felhasználók, alkalmazások, csoportok, szerepkörök és házirendek hozzáadása vagy eltávolítása.
- Kockázatos bejelentkezés – A kockázatos bejelentkezés egy olyan bejelentkezési kísérlet jelzése, amelyet a felhasználói fiók jogos tulajdonosaként elvégeztek.
- Kockázatosként megjelölt felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

A Azure Security Center használatával riasztásokat hozhat létre bizonyos gyanús tevékenységekhez, például a sikertelen hitelesítési kísérletek túlzott számához, beleértve az előfizetésben elavult fiókokat is. Az alapszintű biztonsági higiénia figyelése mellett a veszélyforrások elleni védelmi modul használatával Security Center több részletes biztonsági riasztást gyűjthet az egyes Azure számítási erőforrásokból (Virtual Machines, containers, app Service), az adatforrásokból (SQL DB és Storage) és az Azure szolgáltatási rétegeiből. Ez a funkció lehetővé teszi, hogy megtekintse az egyes erőforrásokon belüli fiókok rendellenességeit.

- [Naplózási tevékenységgel kapcsolatos jelentések a Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Az Azure Identity Protection engedélyezése](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Firewall konfigurálása virtuális WAN-központban](howto-firewall.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: megosztott

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: a fenyegetések észlelésének engedélyezése az Azure identitás-és hozzáférés-kezeléséhez

**Útmutató**: a virtuális WAN-t használó pont – hely VPN integrálva van Azure Active Directory (Azure ad) szolgáltatással. Az Azure AD a következő felhasználói naplókat tartalmazza, amelyeket megtekintheti az Azure AD Reporting szolgáltatásban, vagy integrálható a Azure Monitor, az Azure Sentinel, a SIEM vagy a monitoring eszközeivel összetettebb veszélyforrások figyelésére és elemzési használatára. Ezek a következők:

- Bejelentkezés – a bejelentkezési jelentés információt nyújt a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról.
- Auditnaplók – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplók példái közé tartoznak az Azure AD-ban található erőforrások változásai, például felhasználók, alkalmazások, csoportok, szerepkörök és házirendek hozzáadása vagy eltávolítása.
- Kockázatos bejelentkezés – A kockázatos bejelentkezés egy olyan bejelentkezési kísérlet jelzése, amelyet a felhasználói fiók jogos tulajdonosaként elvégeztek.
- Kockázatosként megjelölt felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

A Azure Security Center használatával riasztásokat hozhat létre bizonyos gyanús tevékenységekhez, például a sikertelen hitelesítési kísérletek túlzott számához, beleértve az előfizetésben elavult fiókokat is. Az alapszintű biztonsági higiénia figyelése mellett a veszélyforrások elleni védelmi modul használatával Security Center több részletes biztonsági riasztást gyűjthet az egyes Azure számítási erőforrásokból (Virtual Machines, containers, app Service), az adatforrásokból (SQL DB és Storage) és az Azure szolgáltatási rétegeiből. Ez a funkció lehetővé teszi, hogy megtekintse az egyes erőforrásokon belüli fiókok rendellenességeit.

- [Naplózási tevékenységgel kapcsolatos jelentések a Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Az Azure Identity Protection engedélyezése](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Firewall konfigurálása virtuális WAN-központban](howto-firewall.md)

**Azure Security Center figyelés**: igen

**Felelősség**: megosztott

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: az Azure hálózati tevékenységek naplózásának engedélyezése

**Útmutató**: az Azure Virtual WAN figyelése Azure monitor használatával. A virtuális WAN számos hálózati, biztonsági és útválasztási funkciót egyesít, hogy egyetlen operatív felületet biztosítson. A virtuális WAN VPN-átjárók, a ExpressRoute-átjárók és a Azure Firewall Azure Monitoron keresztül elérhetők naplózással és metrikákkal. A rendszer alapértelmezés szerint gyűjti a tevékenység naplójának bejegyzéseit, és megtekintheti őket a Azure Portalban. Az Azure-tevékenység naplóinak (korábbi nevén operatív naplók és naplók) használatával megtekintheti az Azure-előfizetéséhez benyújtott összes műveletet.

Számos diagnosztikai napló is elérhető a virtuális WAN számára, és a Azure Portal használatával konfigurálható a virtuális WAN-erőforráshoz.  Dönthet úgy is, hogy elküldi Log Analytics, streamet egy Event hub-ba, vagy egyszerűen archiválja a Storage-fiókba. 
 

- [Virtuális WAN-naplók és-metrikák](logs-metrics.md)

- [Az Azure Firewall naplói és metrikái](/azure/firewall/logs-and-metrics)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: az Azure-beli tevékenység naplófájljai automatikusan engedélyezve vannak az Azure-beli virtuális WAN-erőforrások összes írási műveletét (Put, post, DELETE), kivéve az OLVASÁSI (Get) műveleteket. A Tevékenységnaplók segítségével hibát kereshet a hibaelhárítás során, vagy megfigyelheti, hogy a szervezet felhasználója hogyan módosított egy erőforrást.

Engedélyezze az Azure-beli erőforrás-naplókat a virtuális WAN számára. Azure Security Center és Azure Policy használatával engedélyezheti az erőforrás-naplókat és naplózhatja az adatgyűjtést. Ezek a naplók kritikus fontosságúak lehetnek a biztonsági incidensek kivizsgálásához és a kriminalisztikai gyakorlatok végrehajtásához.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [A naplózás és a különböző naplózási típusok megismerése az Azure-ban](../azure-monitor/platform/platform-logs-overview.md) 

- [Az Azure Security Center adatgyűjtés ismertetése](../security-center/security-center-enable-data-collection.md)

**Azure Security Center figyelés**: igen

**Felelősség**: megosztott

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: a biztonsági naplók központosított kezelése és elemzése

**Útmutató**: a virtuális WAN biztonsági naplózásának engedélyezése Azure monitor használatával. A virtuális WAN számos hálózati, biztonsági és útválasztási funkciót egyesít, hogy egyetlen operatív felületet biztosítson. A virtuális WAN VPN-átjárók, a ExpressRoute-átjárók és a Azure Firewall Azure Monitoron keresztül elérhetők naplózással és metrikákkal. A rendszer alapértelmezés szerint gyűjti a tevékenység naplójának bejegyzéseit, és megtekintheti őket a Azure Portalban. Az Azure-tevékenység naplóinak (korábbi nevén operatív naplók és naplók) használatával megtekintheti az Azure-előfizetéséhez benyújtott összes műveletet. 

Számos diagnosztikai napló is elérhető a virtuális WAN számára, és a Azure Portal használatával konfigurálható a virtuális WAN-erőforráshoz. Küldés Log Analytics, Stream egy Event hub-ba, vagy egyszerűen archiválható egy Storage-fiókba. Emellett az Azure Sentinel vagy egy harmadik féltől származó biztonsági információk és rendezvényszervezés megoldás számára is lehetővé teszi az adatok bevezetését. 
 

- [Virtuális WAN-naplók és-metrikák](logs-metrics.md)

- [Az Azure Firewall naplói és metrikái](/azure/firewall/logs-and-metrics)

Az Azure Virtual WAN biztonsági szolgáltatásait Azure Firewallon keresztül biztosítjuk. 

- [Azure Firewall dokumentáció](/azure/firewall/overview)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: megosztott

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: a naplózási tár megőrzésének konfigurálása

**Útmutató**: a naplók megőrzésének beállítása a megfelelőségi, a szabályozási és az üzleti igényeknek megfelelően. Azure Monitor az Log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi előírásai szerint állíthatja be. Az Azure Storage, a Data Lake vagy a Log Analytics munkaterület-fiókok használata hosszú távú és archiválási tároláshoz.

- [Az adatmegőrzési időszak módosítása Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Adatmegőrzési szabályzat konfigurálása az Azure Storage-fiók naplóihoz](../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Azure Security Center riasztások és javaslatok exportálása](../security-center/continuous-export.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

## <a name="incident-response"></a>Incidensmegoldás

*További információt az [Azure biztonsági teljesítményteszt: incidens válasza](/azure/security/benchmarks/security-controls-v2-incident-response)című témakörben talál.*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: előkészítés – az incidensek frissítési folyamatának frissítése az Azure-ban

**Útmutató**: Ellenőrizze, hogy a szervezet rendelkezik-e olyan folyamatokkal, amelyek reagálnak a biztonsági incidensekre, és frissítette ezeket a folyamatokat az Azure-ban, és rendszeresen gyakorolja őket a készültség biztosítására.

- [A biztonság megvalósítása a nagyvállalati környezetben](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Incidens-válasz referenciájának útmutatója](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: előkészítés – telepítési incidens értesítése

**Útmutató**: biztonsági incidensek elérhetőségi adatainak beállítása Azure Security Centerban. A Microsoft a kapcsolattartási adatokat arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. Lehetőség van az incidensek riasztásának és az értesítések különböző Azure-szolgáltatásokban való testreszabására is az incidensek igényei alapján. 

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: észlelés és elemzés – incidensek létrehozása magas minőségi riasztások alapján

**Útmutató**: Ellenőrizze, hogy van-e olyan folyamat, amely kiváló minőségű riasztásokat hoz létre, és mérje fel a riasztások minőségét. Ez lehetővé teszi, hogy megismerje a múltbeli incidensek tanulságait, és rangsorolja a riasztásokat az elemzők számára, hogy ne pazarolja az időt a hamis pozitív értékekre. 

A kiváló minőségű riasztások a múltbeli incidensek, a hitelesített közösségi források és a riasztások létrehozásához és tisztításához tervezett eszközök alapján hozhatók létre a különböző jelforrások elutasításával és korrelációs megoldásával. 

Azure Security Center kiváló minőségű riasztásokat biztosít számos Azure-eszközön. Az ASC adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba. Az Azure Sentinel lehetővé teszi, hogy speciális riasztási szabályokat hozzon létre az incidensek automatikus létrehozásához a vizsgálathoz. 

A Azure Security Center-riasztások és-javaslatok exportálásával az Exportálás funkcióval azonosíthatók az Azure-erőforrásokkal kapcsolatos kockázatok. Riasztásokat és javaslatokat manuálisan vagy folyamatos, folyamatos módon exportálhat.

- [Az Exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások továbbítása az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: észlelés és elemzés – incidens vizsgálata

**Útmutató**: az elemzők különböző adatforrásokat tudnak lekérdezni és használni a lehetséges incidensek kivizsgálásával, így teljes képet kaphatnak arról, hogy mi történt. Különböző naplókat kell gyűjteni a lehetséges támadók tevékenységének nyomon követéséhez a támadási láncon belül a vak foltok elkerülése érdekében.  Gondoskodjon arról is, hogy az elemzések és a megszerzett ismeretek rögzítése más elemzők és a későbbi korábbi referenciák esetében is megtörténjen.  

A vizsgálathoz használt adatforrások közé tartoznak azok a központi naplózási források, amelyeket a rendszer már gyűjt a hatókörbeli szolgáltatásokból és a futó rendszerekről, de a következőket is tartalmazhatja:

- Hálózati adatok – hálózati biztonsági csoportok folyamatábrája, Azure Network Watcher és Azure Monitor a hálózati folyamat naplói és egyéb elemzési információk rögzítésére. 

- Futó rendszerek pillanatképei: 

    - Az Azure-beli virtuális gép pillanatkép-funkciója segítségével pillanatkép készíthető a futó rendszer lemezéről. 

    - A futó rendszer memóriájának pillanatképének létrehozásához használja az operációs rendszer natív memóriaképének képességét.

    - Használja az Azure-szolgáltatások pillanatkép-szolgáltatását, vagy az Ön szoftverének saját funkciója a futó rendszerek pillanatképének létrehozásához.

Az Azure Sentinel széles körű adatelemzést tesz lehetővé gyakorlatilag bármely naplózási forrás és egy eseti felügyeleti portál között az incidensek teljes életciklusának kezeléséhez. A nyomozás során felmerülő hírszerzési információk követési és jelentéskészítési célokra is társíthatók. 

- [Windows-gép lemezének pillanatképe](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [A Linux rendszerű gép lemezének pillanatképe](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure a diagnosztikai információk és a memóriakép-gyűjtés támogatása](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Incidensek vizsgálata az Azure Sentineltel](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: észlelés és elemzés – az incidensek rangsorolása

**Útmutató**: az elemzők környezetének biztosítása, amelyeken a riasztás súlyossága és az eszközök érzékenysége alapján az elsőre összpontosíthat. 

A Azure Security Center az egyes riasztásokhoz súlyosságot rendel, így rangsorolhatja, hogy a rendszer mely riasztásokat vizsgálja meg először. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett a címkék használatával megjelölheti az erőforrásokat, és létrehozhat egy elnevezési rendszert az Azure-erőforrások azonosítására és kategorizálására, különösen a bizalmas adatok feldolgozására.  Az Ön felelőssége, hogy rangsorolja a riasztások szervizelését az Azure-erőforrások és-környezet kritikus jellemzői alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: tárolás, mentesítés és helyreállítás – az incidensek kezelésének automatizálása

**Útmutató**: automatizálja a manuális ismétlődő feladatokat a válaszidő felgyorsításához és az elemzők terhének csökkentéséhez. A manuális feladatok végrehajtása hosszabb időt vehet igénybe, és lelassítja az egyes incidenseket, és csökkenti az elemző által kezelhető incidensek számát. A manuális feladatok növelik az elemzői fáradtságot is, ami növeli a késést okozó emberi hibák kockázatát, és csökkenti az elemzők azon képességét, hogy az összetett feladatokon hatékonyan összpontosítsanak. A Azure Security Center és az Azure Sentinel munkafolyamat-automatizálási funkcióinak használatával automatikusan aktiválhatja a műveleteket, vagy futtathat egy forgatókönyvet a bejövő biztonsági riasztásokra való reagáláshoz. A forgatókönyv végrehajtja a műveleteket, például értesítések küldését, fiókok letiltását és a problémás hálózatok elkülönítését. 

- [Munkafolyamat-automatizálás konfigurálása Security Centerban](../security-center/workflow-automation.md)

- [Automatizált veszélyforrásokkal kapcsolatos válaszok beállítása Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Automatizált veszélyforrásokkal kapcsolatos válaszok beállítása az Azure Sentinelben](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="posture-and-vulnerability-management"></a>A testtartás és a sebezhetőségek kezelése

*További információt az [Azure biztonsági teljesítményteszt: testtartás és sebezhetőség kezelése](/azure/security/benchmarks/security-controls-v2-vulnerability-management)című témakörben talál.*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: rendszeres támadási szimulációk végrehajtása

**Útmutatás**: igény szerint az Azure-erőforrásokon bevezetési teszteket vagy Red Team-tevékenységeket hajthat végre, és biztosíthatja az összes kritikus biztonsági vizsgálat szervizelését.
Kövesse a Microsoft Cloud penetráció tesztelési szabályait, amelyekkel biztosíthatja, hogy a behatolási tesztek ne sértsék a Microsoft szabályzatait. A Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatban a Microsoft stratégiáját és a Red Teaming és a Live site penetráció tesztelését is használhatja.

- [Behatolás-tesztelés az Azure-ban](../security/fundamentals/pen-testing.md)

- [Behatolás-tesztelési szabályok engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud vörös összevonása](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

## <a name="endpoint-security"></a>Endpoint Security

*További információt az [Azure biztonsági teljesítményteszt: Endpoint Security](/azure/security/benchmarks/security-controls-v2-endpoint-security)című témakörben talál.*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: végpont-észlelés és-válasz használata (EDR)

**Útmutató**: az ügyfelek számára nincs kifejezetten engedélyezve a végpont-észlelés és a válasz beállításainak konfigurálása. Az Azure virtuális WAN-ajánlatokban használt Virtual Machines azonban ezeket a képességeket használják. További információ ezekről az általános képességekről a hivatkozott hivatkozásokon. 

- [A Microsoft Defender komplex veszélyforrások elleni védelem áttekintése](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Microsoft Defender ATP-szolgáltatás Windows-kiszolgálókhoz](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Microsoft Defender ATP szolgáltatás a nem Windows rendszerű kiszolgálókhoz](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Azure Security Center figyelés**: igen

**Felelősség**: megosztott

## <a name="governance-and-strategy"></a>Irányítás és stratégia

*További információt az [Azure biztonsági teljesítményteszt: kormányzás és stratégia](/azure/security/benchmarks/security-controls-v2-governance-strategy)című témakörben talál.*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: eszközkezelés és adatvédelmi stratégia meghatározása 

**Útmutató**: a rendszerek és az információk folyamatos monitorozására és védelmére vonatkozó egyértelmű stratégia dokumentálása és közlése. Az üzleti szempontból kritikus fontosságú adatmennyiségek és rendszerek felderítésének, értékelésének, védelmének és figyelésének rangsorolása. 

Ennek a stratégiának tartalmaznia kell a következő elemek dokumentált útmutatását, szabályzatát és szabványait: 

-   Az adatbesorolási szabvány az üzleti kockázatoknak megfelelően

-   A biztonsági szervezet betekintést nyerhet a kockázatokra és az eszközök leltározására 

-   Az Azure-szolgáltatások használatának biztonsági szervezet általi jóváhagyása 

-   Az eszközök biztonsága életcikluson keresztül

-   A szükséges hozzáférés-vezérlési stratégia a szervezeti adatbesorolásnak megfelelően

-   Az Azure Native és a harmadik féltől származó adatvédelmi képességek használata

-   Adattitkosítási követelmények átvitel közbeni és nyugalmi használati esetekben

-   Megfelelő titkosítási szabványok

További információkért tekintse meg a következő referenciákat:
- [Azure biztonsági architektúra – javaslat – tárolás, adatkezelés és titkosítás](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Az Azure biztonsági alapjai – Azure-adatbiztonság, titkosítás és tárolás](../security/fundamentals/encryption-overview.md)

- [Felhőalapú bevezetési keretrendszer – az Azure adatbiztonsági és-titkosítási ajánlott eljárásai](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure biztonsági teljesítményteszt – eszközkezelés](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure biztonsági teljesítményteszt – adatvédelem](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: vállalati szegmentálási stratégia definiálása 

**Útmutató**: hozzon létre egy nagyvállalati stratégiát, amely az eszközök hozzáférésének szegmentálására szolgál az identitás, a hálózat, az alkalmazás, az előfizetés, a felügyeleti csoport és más vezérlők kombinációjával.

Körültekintően kell elkülöníteni a biztonsági elkülönítés szükségességét annak érdekében, hogy lehetővé váljon a rendszerek napi működésének engedélyezése, amelyeknek kommunikálnia kell egymással, és az adathozzáférésre van szükségük.

Győződjön meg arról, hogy a szegmentálási stratégia következetesen van implementálva a különböző típusú vezérlőkben, beleértve a hálózati biztonságot, az identitás-és hozzáférési modelleket, valamint az alkalmazás engedély/hozzáférési modelljeit, valamint az emberi folyamatok vezérlés

- [Útmutató a szegmentálási stratégiához az Azure-ban (videó)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Útmutató a szegmentálási stratégiához az Azure-ban (dokumentum)](/security/compass/governance#enterprise-segmentation-strategy)

- [Hálózati szegmentálás igazítása nagyvállalati szegmentálási stratégiával](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: a biztonsági testhelyzet kezelési stratégiájának meghatározása

**Útmutató**: az egyes eszközökre és a szolgáltatásban üzemeltetett környezetre vonatkozó kockázatok folyamatos mérése és enyhítése. Rangsorolja a nagy értékű eszközöket és a nagy teljesítményű támadási felületeket, például a közzétett alkalmazásokat, a hálózati bejövő és kimenő pontokat, a felhasználói és rendszergazdai végpontokat stb.

- [Azure biztonsági teljesítményteszt – a testtartás és a sebezhetőségek kezelése](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: szervezeti szerepkörök, felelősségek és elszámoltathatóság igazítása

**Útmutató**: gondoskodjon arról, hogy a biztonsági szervezetében a szerepkörökkel és a felelősségekkel kapcsolatos egyértelmű stratégiát dokumentáljon és tájékoztasson. Rangsorolja a biztonsági döntések egyértelmű elszámoltathatóságát, mindenki számára elérhetővé teszi a megosztott felelősségi modellt, és megtanítja a technikai csapatokat a felhő védelméhez.

- [Azure Security – ajánlott eljárás 1 – emberek: a csapatok képzése a Felhőbeli biztonsági úton](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure Security – ajánlott eljárás 2 – emberek: a csapatok képzése a Felhőbeli biztonsági technológiában](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure Security – ajánlott eljárás 3 – folyamat: elszámoltathatóság kiosztása a Felhőbeli biztonsági döntésekhez](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="gs-5-define-network-security-strategy"></a>GS-5: hálózati biztonsági stratégia definiálása

**Útmutató**: hozzon létre egy Azure-alapú hálózati biztonsági megközelítést a szervezet általános biztonsági hozzáférés-vezérlési stratégiájának részeként.  

Ennek a stratégiának tartalmaznia kell a következő elemek dokumentált útmutatását, szabályzatát és szabványait: 

-   Központosított Hálózatkezelés és biztonsági felelősség

-   A virtuális hálózat szegmentálási modellje a nagyvállalati szegmentálási stratégiához igazodik

-   Szervizelési stratégia a különböző veszélyforrások és támadási helyzetekben

-   Az Internet Edge és a bejövő forgalom és a kimenő forgalom stratégiája

-   Hibrid felhő és helyszíni összekapcsolási stratégia

-   Naprakész hálózati biztonsági összetevők (például hálózati diagramok, hivatkozási hálózati architektúra)

További információkért tekintse meg a következő referenciákat:
- [Azure Security – ajánlott eljárás 11 – architektúra. Egyetlen egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure biztonsági teljesítményteszt – hálózati biztonság](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Az Azure hálózati biztonság áttekintése](../security/fundamentals/network-overview.md)

- [Vállalati hálózati architektúra stratégia](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: identitás-és privilegizált hozzáférési stratégia meghatározása

**Útmutató**: hozzon létre egy Azure-identitást és egy emelt szintű hozzáférési megközelítést a szervezet általános biztonsági hozzáférés-vezérlési stratégiájának részeként.  

Ennek a stratégiának tartalmaznia kell a következő elemek dokumentált útmutatását, szabályzatát és szabványait: 

-   Központi identitás-és hitelesítési rendszer, valamint az egyéb belső és külső identitási rendszerekkel való kapcsolat

-   Erős hitelesítési módszerek különböző használati esetekben és kikötésekben

-   Magas jogosultsági szintű felhasználók védelme

-   Felhasználói tevékenységek rendellenességének figyelése és feldolgozása  

-   Felhasználói identitás és hozzáférés felülvizsgálata és egyeztetési folyamata

További információkért tekintse meg a következő referenciákat:

- [Azure biztonsági teljesítményteszt – Identitáskezelés](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure biztonsági teljesítményteszt – emelt szintű hozzáférés](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure Security – ajánlott eljárás 11 – architektúra. Egyetlen egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Az Azure Identity Management biztonsági áttekintése](../security/fundamentals/identity-management-overview.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: a naplózási és a veszélyforrások reagálási stratégiájának meghatározása

**Útmutató**: naplózási és veszélyforrási reagálási stratégia létrehozása a fenyegetések gyors észlelése és elhárítása érdekében a megfelelőségi követelmények teljesítése mellett. Rangsorolja az elemzőket kiváló minőségű riasztásokkal és zökkenőmentes tapasztalatokkal, hogy az integráció és a manuális lépések helyett a fenyegetésekre összpontosítsanak. 

Ennek a stratégiának tartalmaznia kell a következő elemek dokumentált útmutatását, szabályzatát és szabványait: 

-   A biztonsági műveletek (SecOps) szervezet szerepköre és feladatai 

-   Egy jól definiált incidens-reagálási folyamat NIST vagy más iparági keretrendszerrel való összehangolása 

-   A fenyegetések észlelését, az incidensek megválaszolását és a megfelelőségi igényeket támogató napló rögzítése és megőrzése

-   A fenyegetésekkel kapcsolatos központosított láthatóság és korrelációs információk a SIEM, a natív Azure-képességek és más források használatával 

-   Kommunikációs és értesítési terv ügyfeleivel, szállítókkal és az érdekelt felekkel

-   Az Azure Native és a harmadik féltől származó platformok használata az incidensek kezelésére, mint például a naplózás és a veszélyforrások észlelése, a kriminalisztika, valamint a támadási problémák elhárítása és felszámolása

-   Az incidensek és az incidens utáni tevékenységek kezelésének folyamatai, például a tanulságok és a bizonyítékok megőrzése

További információkért tekintse meg a következő referenciákat:

- [Azure biztonsági teljesítményteszt – naplózás és fenyegetések észlelése](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure biztonsági teljesítményteszt – incidens válasza](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security – ajánlott eljárás 4 – folyamat. Incidensek frissítési folyamatainak frissítése a felhőben](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure bevezetési keretrendszer, naplózás és jelentéskészítési döntési útmutató](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Az Azure nagyvállalati szintű skálázása, kezelése és monitorozása](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="next-steps"></a>További lépések

- Lásd az [Azure Security teljesítményteszt v2 áttekintése](/azure/security/benchmarks/overview) című témakört.
- További információ az [Azure biztonsági alaptervekről](/azure/security/benchmarks/security-baselines-overview)
