---
title: Virtuális WAN Azure biztonsági alapterve
description: A virtuális WAN biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 409c8d163b52e56a1eef68a23e4db79956a68722
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328693"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Virtuális WAN Azure biztonsági alapterve

Ez a biztonsági alapterv az [Azure biztonsági teljesítményteszt 2,0-es verziójáról](../security/benchmarks/overview.md) Microsoft Azure virtuális WAN-ra vonatkozó útmutatást alkalmaz. Az Azure biztonsági teljesítményteszt az Azure-beli felhőalapú megoldások biztonságossá tételével kapcsolatos ajánlásokat tartalmaz. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** és a virtuális WAN-ra vonatkozó kapcsolódó útmutatás szerint van csoportosítva. A virtuális WAN-ra nem alkalmazható **vezérlőelemek** ki lettek zárva.

Ha szeretné megtudni, hogy a virtuális WAN hogyan teljes mértékben leképezi az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes virtuális WAN biztonsági alapkonfiguráció-hozzárendelési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információt az [Azure biztonsági teljesítményteszt: hálózati biztonság](/azure/security/benchmarks/security-controls-v2-network-security)című témakörben talál.*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: a belső forgalom biztonságának megvalósítása

**Útmutató** : az Azure Virtual WAN nem támogatja közvetlenül a virtuális hálózatokba való telepítését. Azonban továbbra is alkalmazhatja a hálózati biztonsági csoportra vonatkozó szabályokat a küllős erőforrásokon, Azure Firewall védelmét használhatja, vagy egyéni útválasztási táblákat hozhat létre a privát forgalom megelőzésére vagy engedélyezésére.

- [Egyéni útválasztási forgatókönyvek](scenario-any-to-any.md)

**Azure Security Center figyelés** : jelenleg nem érhető el

**Felelősség** : ügyfél

### <a name="ns-2-connect-private-networks-together"></a>NS-2: magánhálózati hálózatok összekapcsolása 

**Útmutató** : az Azure ExpressRoute vagy az Azure virtual private Network (VPN) használatával privát kapcsolatokat hozhat létre az Azure-adatközpontok és a helyszíni infrastruktúra között egy közös elhelyezésű környezetben. A ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át, így megbízhatóbbak, gyorsabbak és kevesebb késéssel rendelkeznek, mint a szokásos internetkapcsolatok. 

Pont – hely típusú VPN-és helyek közötti VPN esetén a VPN-beállítások és a ExpressRoute bármely kombinációjának használatával csatlakoztasson helyszíni eszközöket vagy hálózatokat egy virtuális hálózathoz. Ha két vagy több virtuális hálózatot szeretne összekapcsolni az Azure-ban, használja a Virtual Network-társítást. A kiszolgált virtuális hálózatok közötti hálózati forgalom magán, és az Azure-beli gerinces hálózaton marad.

- [ExpressRoute a virtuális WAN-ban](virtual-wan-expressroute-portal.md)

- [Helyek közötti VPN – áttekintés](virtual-wan-site-to-site-portal.md)

- [Pont – hely VPN – áttekintés](virtual-wan-point-to-site-portal.md)

- [Privát kapcsolat](howto-private-link.md)

**Azure Security Center figyelés** : jelenleg nem érhető el

**Felelősség** : megosztott

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: magánhálózati hozzáférés létrehozása az Azure-szolgáltatásokhoz

**Útmutató** : az Azure-beli privát hivatkozás használatával engedélyezheti a virtuális hálózatokról az Azure Virtual WAN-ra való privát hozzáférést anélkül, hogy az interneten át kellene lépnie. A privát hozzáférés egy másik, az Azure-szolgáltatások által kínált hitelesítés és adatforgalom melletti részletes védelmi mérőszám.

- [Az Azure privát hivatkozásának megismerése](../private-link/private-link-overview.md)

- [Virtuális WAN magánhálózati kapcsolat](howto-private-link.md)

**Azure Security Center figyelés** : jelenleg nem érhető el

**Felelősség** : ügyfél

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: a külső hálózati támadásoktól származó alkalmazások és szolgáltatások elleni védelem

**Útmutató** : az Azure-beli virtuális WAN-erőforrások védelmet biztosítanak a külső hálózatoktól érkező támadások ellen, beleértve az elosztott szolgáltatásmegtagadási (DDoS) támadásokat, az alkalmazás-specifikus támadásokat, valamint a kéretlen és potenciálisan kártékony internetes forgalmat. 

A Azure Firewall használatával védelmet biztosíthat az alkalmazások és szolgáltatások számára az internetről és más külső helyekről származó, potenciálisan rosszindulatú forgalom ellen. Az Azure-beli virtuális hálózatokon lévő támadások elleni Azure DDoS Protection válassza ki a kívánt eszközöket. A Azure Security Center használatával azonosíthatja a hálózattal kapcsolatos erőforrásokkal kapcsolatos hibás konfigurációs kockázatokat.

- [Azure Firewall dokumentáció](/azure/firewall)

- [Azure DDoS Protection standard kezelése a Azure Portal használatával](/azure/virtual-network/manage-ddos-protection) 

- [Az Azure Security Centerre vonatkozó ajánlások](../security-center/recommendations-reference.md#recs-network)

**Azure Security Center figyelés** : igen

**Felelősség** : megosztott

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató** : a beléptetési észlelés vagy a megelőzési rendszerek üzembe helyezésének legjobb módja az Azure Virtual WAN-ban egy hálózati virtuális berendezés használata a virtuális hubhoz csatlakoztatott küllős hálózatokban.  További információ a hivatkozott hivatkozásokon elérhető útválasztási forgatókönyvekben található. 

- [Forgatókönyv – Forgalom irányítása NVA-n keresztül](scenario-route-through-nva.md)

**Azure Security Center figyelés** : jelenleg nem érhető el

**Felelősség** : ügyfél

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: a hálózati biztonsági szabályok egyszerűsítése

**Útmutató** : az Azure Virtual Network szolgáltatás-címkék használatával definiálhat hálózati hozzáférés-vezérlést az Azure-beli hálózati biztonsági csoportokon, vagy a virtuális WAN-erőforrásokhoz konfigurált Azure Firewall. 

Biztonsági szabályok létrehozásakor használja a szolgáltatási címkéket adott IP-címek helyett. A szolgáltatási címke nevének (például: {VirtualWAN: Virtual Network}) megadásával a szabály megfelelő forrás vagy cél mezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

**Azure Security Center figyelés** : jelenleg nem érhető el

**Felelősség** : ügyfél

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: biztonságos tartománynév szolgáltatás (DNS)

**Útmutató** : az Azure Virtual WAN egyéni DNS-kiszolgálókat kínál a pont – hely VPN-átjárók számára. 

Kövesse a DNS-biztonság ajánlott eljárásait a gyakori támadásokkal szemben, például a DNS, a DNS-erősítési támadások, a DNS-mérgezés és a hamisítás és így tovább.

Ha a Azure DNS a mérvadó DNS-szolgáltatásként van használatban, győződjön meg arról, hogy a DNS-zónák és-rekordok védelme az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) és az erőforrás-zárolások használatával a véletlen vagy rosszindulatú módosításoktól védve van.

- [Azure DNS áttekintése](../dns/dns-overview.md) 

- [A biztonságos tartománynévrendszer (DNS) üzembe helyezési útmutatója](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [A DNS-bejegyzések letiltásának és a tartományon belüli átvétel elkerülésének megakadályozása](../security/fundamentals/subdomain-takeover.md)

**Azure Security Center figyelés** : jelenleg nem érhető el

**Felelősség** : ügyfél

## <a name="identity-management"></a>Identitáskezelés

*További információt az [Azure biztonsági teljesítményteszt: Identitáskezelés](/azure/security/benchmarks/security-controls-v2-identity-management)című témakörben talál.*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: szabványosított Azure Active Directory központi identitás-és hitelesítési rendszerrel

**Útmutató** : az Azure Virtual WAN pont – hely VPN integrálva van Azure Active Directory (Azure ad) szolgáltatással, amely az Azure alapértelmezett identitás-és hozzáférés-kezelési szolgáltatása. Egységesítse az Azure AD-t a szervezet identitás-és hozzáférés-kezelésének szabályozásához a ben:

- Microsoft Cloud erőforrások, például a Azure Portal, az Azure Storage, az Azure Virtual Machines (Linux és Windows), a Azure Key Vault, a Pásti és az SaaS-alkalmazások.
- A szervezet erőforrásai, például az Azure-beli alkalmazások vagy a vállalati hálózati erőforrások.

Az Azure AD magas prioritású védelme a szervezet felhőalapú biztonsági gyakorlatában. Az Azure Security Center biztonsági pontszám funkciójával értékelheti az identitást és a biztonságot, hogy a konfiguráció milyen mértékben megfelel az ajánlott eljárásoknak. A Microsoft ajánlott eljárásait ajánljuk a biztonsági helyzetének fejlesztéséhez.

Az Azure AD a külső identitásokat is támogatja, amelyek lehetővé teszik a felhasználók számára, hogy Microsoft-fiók nélkül bejelentkezzenek alkalmazásaiba és erőforrásaiba külső identitásával. Tekintse át az Azure AD-t a hivatkozott hivatkozásokon a pont – hely VPN-forgatókönyvekben található információk alapján.

- [Azure Active Directory (AD) bérlő létrehozása a P2S OpenVPN protokoll kapcsolataihoz](openvpn-azure-ad-tenant-multi-app.md)

- [Azure Active Directory hitelesítés konfigurálása a felhasználói VPN-hez](virtual-wan-point-to-site-azure-ad.md)

- [Bérlet Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Külső identitás-szolgáltatók használata az alkalmazáshoz](/azure/active-directory/b2b/identity-providers)

**Azure Security Center figyelés** : igen

**Felelősség** : megosztott

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: erős hitelesítési vezérlők használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató** : az Azure Virtual WAN pont – hely VPN integrálva van Azure Active Directory (Azure ad) szolgáltatással, amely támogatja a többtényezős hitelesítéssel rendelkező erős hitelesítési vezérlőket és az erős, jelszóval nem rendelkező metódusokat.

- Többtényezős hitelesítés – az Azure AD többtényezős hitelesítésének engedélyezése és a Azure Security Center identitás-és hozzáférés-kezelési javaslatainak követése a többtényezős hitelesítés beállításának ajánlott eljárásaihoz. A többtényezős hitelesítés kikényszeríthető az összes, a felhasználók vagy a felhasználónkénti szinten a bejelentkezési feltételek és kockázati tényezők alapján.

- Jelszóval nem rendelkező hitelesítés – a rendszer három jelszavas hitelesítési lehetőséget kínál. Ezek közé tartoznak a vállalati Windows Hello, Microsoft Authenticator alkalmazás és a helyszíni hitelesítési módszerek, például az intelligens kártyák.

A rendszergazda és a Kiemelt jogosultságú felhasználók számára győződjön meg arról, hogy az erős hitelesítési módszer legmagasabb szintjét használja, majd a megfelelő erős hitelesítési házirendből kiindulva más felhasználók számára.

- [Az MFA engedélyezése a P2S VPN-ben virtuális WAN-hoz](openvpn-azure-ad-mfa.md)

**Azure Security Center figyelés** : igen

**Felelősség** : megosztott

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: az Azure erőforrás-hozzáférés korlátozása feltételek alapján

**Útmutató** : az Azure Virtual WAN pont – hely VPN támogatja az Azure ad feltételes hozzáférését egy részletesebb hozzáférés-vezérléshez a felhasználó által definiált feltételek alapján. Az egyes IP-címtartományok felhasználói bejelentkezések esetében például többtényezős hitelesítést kell használni a bejelentkezéshez. A részletes hitelesítési munkamenet-kezelési szabályzat különböző használati esetekben is használható.

- [Általános feltételes hozzáférési szabályzatok](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [A hitelesítési munkamenet felügyeletének konfigurálása feltételes hozzáféréssel](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

- [Virtuális WAN P2S VPN feltételes hozzáférés](openvpn-azure-ad-mfa.md#conditional)

**Azure Security Center figyelés** : jelenleg nem érhető el

**Felelősség** : ügyfél

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: a nem szándékolt hitelesítő adatok expozíciójának megszüntetése

**Útmutató** : hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A hitelesítő adatok beolvasása azt is javasolja, hogy a felderített hitelesítő adatokat biztonságosabb helyszínekre (például Azure Key Vault) helyezze.

A GitHub esetében a natív titkos keresési funkcióval azonosíthatók a kódokon belüli hitelesítő adatok vagy egyéb titkok.

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [GitHub-titkos vizsgálat](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure Security Center figyelés** : jelenleg nem érhető el

**Felelősség** : ügyfél

## <a name="privileged-access"></a>Emelt szintű hozzáférés

*További információt az [Azure biztonsági teljesítményteszt: privilegizált hozzáférés](/azure/security/benchmarks/security-controls-v2-privileged-access)című témakörben talál.*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: az üzleti szempontból kritikus fontosságú rendszerek rendszergazdai hozzáférésének korlátozása

**Útmutató** : az Azure Virtual WAN az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával elkülöníti az üzleti szempontból kritikus rendszerek hozzáférését azáltal, hogy korlátozza, hogy mely fiókok kapnak jogosultsági szintű hozzáférést az előfizetésekhez és a felügyeleti csoportokhoz.

Korlátozza továbbá az üzleti szempontból kritikus Active Directory-tartomány fontosságú rendszerekre telepített ügynökökkel való rendszergazdai hozzáféréssel rendelkező felügyeleti, identitási és biztonsági rendszerekhez való hozzáférést is. Azok a támadók, akik veszélyeztetik ezeket a felügyeleti és biztonsági rendszereket, azonnal weaponize azokat az üzleti szempontból kritikus fontosságú eszközök biztonsága érdekében.

A hozzáférés-vezérlés összes típusát a vállalati szegmentálási stratégiához kell igazítani, hogy biztosítsa a konzisztens hozzáférés-vezérlést.

- [Azure-összetevők és-hivatkozási modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Felügyeleti csoport elérése](../governance/management-groups/overview.md#management-group-access) 

- [Azure-előfizetés rendszergazdái](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center figyelés** : jelenleg nem érhető el

**Felelősség** : ügyfél

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: emelt szintű hozzáférésű munkaállomások használata

**Útmutató** : a biztonságos, elkülönített munkaállomások kritikus fontosságúak a bizalmas szerepkörök, például a rendszergazdák, a fejlesztők és a kritikus fontosságú szolgáltatók biztonsága szempontjából. Biztonságos felhasználói munkaállomásokat vagy Azure Bastion-t használhat a felügyeleti feladatokhoz. Az Azure Active Directory (Azure AD), a Microsoft Defender komplex veszélyforrások elleni védelem (ATP) vagy a Microsoft Intune használatával biztonságos és felügyelt felhasználói munkaállomás helyezhető üzembe felügyeleti feladatokhoz. A biztonságos munkaállomások központilag felügyelhetők a biztonságos konfiguráció kényszerítéséhez, beleértve az erős hitelesítést, a szoftver-és a hardver alapkonfigurációit, a korlátozott logikai és hálózati hozzáférést.

- [Az emelt szintű hozzáférésű munkaállomások ismertetése](../active-directory/devices/concept-azure-managed-workstation.md)

- [Emelt szintű hozzáférési munkaállomás üzembe helyezése](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center figyelés** : jelenleg nem érhető el

**Felelősség** : ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információt az [Azure biztonsági teljesítményteszt: Adatvédelem](/azure/security/benchmarks/security-controls-v2-data-protection)című témakörben talál.*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: bizalmas adatok titkosítása az átvitel során

**Útmutató** : a titkosítás kritikus fontosságú a külső és a nyilvános hálózatok forgalmában.

- A hozzáférés-vezérlés használata esetén a titkosítás használatával védeni kell a "sávon kívüli" támadások (például a forgalom rögzítése) elleni védelmet, így biztosítva, hogy a támadók ne tudják egyszerűen beolvasni vagy módosítani az adatokat.

- Győződjön meg arról, hogy a HTTP-forgalom, hogy az Azure-erőforrásokhoz csatlakozó ügyfelek a TLS v 1.2-es vagy újabb verzióját is tárgyalják.

- Távoli felügyelet esetén a titkosítatlan protokoll helyett használjon SSH-t (Linux rendszeren) vagy RDP/TLS-t (Windows rendszeren). Az elavult SSL/TLS/SSH-verziók, protokollok és gyenge titkosítások le lesznek tiltva.-

- A mögöttes infrastruktúrában az Azure alapértelmezés szerint az Azure-adatközpontok közötti adatforgalomhoz biztosít adatátviteli titkosítást.

Általánosságban elmondható, hogy titkosítást biztosítunk a biztonságos Microsoft-gerincen, és lehetőség van arra, hogy Titkosítsa a forgalmat a helyek közötti VPN, a helyek közötti VPN-en keresztül az Azure ExpressRoute és pont – hely felhasználói VPN-en keresztül.

- [A titkosítás ismertetése az Azure-ban](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Információk a TLS biztonságáról](/security/engineering/solving-tls1-problem)

- [Kettős titkosítás az Azure-beli adatforgalomban](../security/fundamentals/double-encryption.md#data-in-transit)

**Azure Security Center figyelés** : igen

**Felelősség** : megosztott

## <a name="asset-management"></a>Asset Management (Eszközkezelés)

*További információt az [Azure biztonsági teljesítményteszt: Asset Management](/azure/security/benchmarks/security-controls-v2-asset-management)című témakörben talál.*

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: gondoskodjon arról, hogy a biztonsági csapat hozzáférjen az eszközök leltárához és metaadataihoz

**Útmutató** : címkék alkalmazása az Azure-erőforrásokra,-csoportokra és-előfizetésekre, amelyek logikailag rendszerezik őket egy besorolásban. Minden címke egy nevet és egy érték párokat tartalmaz. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

Az Azure Virtual WAN a Azure Resource Manager-alapú erőforrás-telepítéseket és az Azure Resource Graph-lekérdezéseket is támogatja. 

- [További információ az eszközök címkézéséről: erőforrás-elnevezési és címkézési döntési útmutató](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md)

**Azure Security Center figyelés** : igen

**Felelősség** : ügyfél

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató** : a Azure Policy használatával korlátozhatja a környezetében üzembe helyezhető szolgáltatásokat. Az előfizetéseken belüli Azure Resource Graph-beli erőforrások lekérdezése és felderítése, valamint a Azure Monitor használatával szabályok hozhatók létre, amelyek riasztásokat küldenek, ha nem jóváhagyott szolgáltatást észlelnek.

- [Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Azure Security Center figyelés** : jelenleg nem érhető el

**Felelősség** : ügyfél

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató** : az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek az Azure erőforrás-kezelővel való interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center figyelés** : jelenleg nem érhető el

**Felelősség** : ügyfél

## <a name="logging-and-threat-detection"></a>Naplózás és fenyegetések észlelése

*További információt az [Azure biztonsági teljesítményteszt: naplózás és fenyegetések észlelése](/azure/security/benchmarks/security-controls-v2-logging-threat-protection)című témakörben talál.*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: a fenyegetések észlelésének engedélyezése az Azure identitás-és hozzáférés-kezeléséhez

**Útmutató** : a Azure Active Directory (Azure ad) a következő felhasználói naplókat nyújtja, amelyek megtekinthetők az Azure ad jelentéskészítési szolgáltatásban, illetve a Azure monitor, az Azure Sentinel, a Siem vagy a monitoring eszközeivel a kifinomultabb monitorozási és elemzési felhasználási esetekben. Ezek a következők:

- Bejelentkezés – a bejelentkezési jelentés információt nyújt a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról.
- Auditnaplók – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplók példái közé tartoznak az Azure AD-ban található erőforrások változásai, például felhasználók, alkalmazások, csoportok, szerepkörök és házirendek hozzáadása vagy eltávolítása.
- Kockázatos bejelentkezés – A kockázatos bejelentkezés egy olyan bejelentkezési kísérlet jelzése, amelyet a felhasználói fiók jogos tulajdonosaként elvégeztek.
- Kockázatosként megjelölt felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

A Azure Security Center használatával riasztásokat hozhat létre bizonyos gyanús tevékenységekhez, például a sikertelen hitelesítési kísérletek túlzott számához, beleértve az előfizetésben elavult fiókokat is. Az alapszintű biztonsági higiénia monitorozása mellett a Security Center veszélyforrások elleni védelmi moduljának használatával több részletes biztonsági riasztást gyűjthet az egyes Azure számítási erőforrásokból (virtuális gépekből, tárolóból, app Service-ből), az adatforrásokból (SQL-ADATBÁZISból és-tárolóból) és az Azure szolgáltatási rétegeiből. Ez a funkció lehetővé teszi, hogy megtekintse az egyes erőforrásokon belüli fiókok rendellenességeit.

- [Naplózási tevékenységgel kapcsolatos jelentések a Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Az Azure Identity Protection engedélyezése](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center figyelés** : igen

**Felelősség** : megosztott

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: az Azure hálózati tevékenységek naplózásának engedélyezése

**Útmutató** : a VPN-csomagok rögzítése eszközeivel rögzítheti az Azure-beli virtuális WAN-erőforrások közötti hálózati csomagokat. Ez segíthet a hibrid hálózattal kapcsolatos hibakeresési folyamaton. Noha nem telepíthet hálózati biztonsági csoportot a virtuális WAN-on, üzembe helyezheti a küllős virtuális hálózati erőforrásaira.

Engedélyezze a hálózati biztonsági csoport adatfolyam-naplóit a hálózati biztonsági csoportokon a forgalom naplózásához.

Engedélyezze az Azure Traffic Analytics szolgáltatást a Storage-fiókban megőrzött flow-naplók feldolgozásához, majd küldje el őket egy Log Analytics munkaterületre. A Traffic Analytics további betekintést nyújt az Azure-hálózatok forgalmának folyamatára. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

- [Hálózati biztonsági csoport folyamatábráinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md) 

A virtuális WAN nem hoz létre vagy dolgoz fel olyan DNS-lekérdezési naplókat, amelyeket engedélyezni kell.

**Azure Security Center figyelés** : jelenleg nem érhető el

**Felelősség** : ügyfél

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: az Azure-erőforrások naplózásának engedélyezése

**Útmutató** : az Azure-beli tevékenység naplófájljai automatikusan engedélyezve vannak az Azure-beli virtuális WAN-erőforrások összes írási műveletét (Put, post, DELETE), kivéve az OLVASÁSI (Get) műveleteket. A Tevékenységnaplók segítségével hibát kereshet a hibaelhárítás során, vagy megfigyelheti, hogy a szervezet felhasználója hogyan módosított egy erőforrást. A virtuális WAN azonban nem hoz létre Azure-erőforrás-naplókat.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 
- [A naplózás és a különböző naplózási típusok megismerése az Azure-ban](../azure-monitor/platform/platform-logs-overview.md)

**Azure Security Center figyelés** : igen

**Felelősség** : megosztott

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: a biztonsági naplók központosított kezelése és elemzése

**Útmutató** : a korreláció engedélyezéséhez központosítsa a naplózási tárolást és az elemzést. Minden naplózási forrás esetében ellenőrizze, hogy rendelkezik-e az adatok tulajdonosával, a hozzáférési útmutatással, a tárolási hellyel, az adatok feldolgozásához és eléréséhez, valamint az adatmegőrzési követelményekhez használt eszközökhöz. Győződjön meg arról, hogy integrálja az Azure-tevékenységek naplóit a központi naplózási rendszerbe. 

Naplók betöltése Azure Monitor használatával a végponti eszközök, a hálózati erőforrások és más biztonsági rendszerek által generált biztonsági adatokat összesítve. Azure Monitor a Log Analytics-munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat hosszú távú és archiválási tároláshoz. Emellett az Azure Sentinel vagy egy harmadik féltől származó SIEM-nek is engedélyezi az adatok bevezetését.

Számos szervezet úgy dönt, hogy a gyakran használt és az Azure-tárolót használja a ritkábban használt adatokhoz az Azure Sentinel használatával.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Azure Security Center figyelés** : jelenleg nem érhető el

**Felelősség** : megosztott

## <a name="incident-response"></a>Incidensmegoldás

*További információt az [Azure biztonsági teljesítményteszt: incidens válasza](/azure/security/benchmarks/security-controls-v2-incident-response)című témakörben talál.*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: előkészítés – az incidensek frissítési folyamatának frissítése az Azure-ban

**Útmutató** : Ellenőrizze, hogy a szervezet rendelkezik-e olyan folyamatokkal, amelyek reagálnak a biztonsági incidensekre, és frissítette ezeket a folyamatokat az Azure-ban, és rendszeresen gyakorolja őket a készültség biztosítására. Győződjön meg arról, hogy a szolgáltatás ajánlata szerepel az incidensek válaszának folyamatában, a megfelelő módon.

- [A biztonság megvalósítása a nagyvállalati környezetben](https://aka.ms/AzSec4) 
- [Incidens-válasz referenciájának útmutatója](https://aka.ms/IRRG)

**Azure Security Center figyelés** : jelenleg nem érhető el

**Felelősség** : ügyfél

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: előkészítés – telepítési incidens értesítése

**Útmutató** : biztonsági incidensek elérhetőségi adatainak beállítása Azure Security Centerban. A Microsoft a kapcsolattartási adatokat arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. Lehetőség van az incidensek riasztásának és az értesítések különböző Azure-szolgáltatásokban való testreszabására is az incidensek igényei alapján.

A Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. Tekintse át az incidenseket a tény után, hogy a problémák teljesen megoldhatók legyenek.

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md) 

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center figyelés** : igen

**Felelősség** : ügyfél

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: észlelés és elemzés – incidensek létrehozása magas minőségi riasztások alapján

**Útmutató** : a Azure Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint a riasztást eredményező tevékenység mögötti rosszindulatú szándékkal.

Emellett egyértelműen megjelölheti az előfizetéseket (például éles környezetben, nem éles környezetben), és létrehozhat egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md) 

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center figyelés** : igen

**Felelősség** : ügyfél

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: észlelés és elemzés – incidens vizsgálata

**Útmutató** : az incidensekre adott válaszokra vonatkozó útmutató kiépítése a szervezet számára. Gyakorlatok végrehajtása a rendszerek incidensekre adott válaszait rendszeres ütemben tesztelheti. Azonosítsa a gyenge pontokat és a réseket, és szükség szerint módosítsa a tervet.

Győződjön meg arról, hogy van olyan írásos incidens-válasz, amely meghatározza a személyzet összes szerepkörét, valamint az incidensek kezelésének és kezelésének fázisait az észleléstől az incidens utáni felülvizsgálatig.

- [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](../security-center/security-center-planning-and-operations-guide.md) 

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és edzésprogramjának kidolgozásához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center figyelés** : jelenleg nem érhető el

**Felelősség** : ügyfél

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: észlelés és elemzés – az incidensek rangsorolása

**Útmutató** : a Azure Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett egyértelműen megjelölheti az előfizetéseket (például, éles környezetben, nem éles környezetben), és létrehozhat egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md) 

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center figyelés** : igen

**Felelősség** : megosztott

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: tárolás, mentesítés és helyreállítás – az incidensek kezelésének automatizálása

**Útmutató** : a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Azure Logic apps" használatával a biztonsági riasztások és javaslatok esetében.

- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Azure Security Center figyelés** : igen

**Felelősség** : ügyfél

## <a name="posture-and-vulnerability-management"></a>A testtartás és a sebezhetőségek kezelése

*További információt az [Azure biztonsági teljesítményteszt: testtartás és sebezhetőség kezelése](/azure/security/benchmarks/security-controls-v2-vulnerability-management)című témakörben talál.*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: rendszeres támadási szimulációk végrehajtása

**Útmutatás** : az Ön igényeinek megfelelően végezzen bevezetési tesztelési vagy Red Team-tevékenységeket az Azure-erőforrásokon, és gondoskodjon az összes kritikus biztonsági vizsgálat szervizeléséről.

Kövesse a Microsoft Cloud penetráció tesztelési szabályait, amelyekkel biztosíthatja, hogy a behatolási tesztek ne sértsék a Microsoft szabályzatait. A Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatban a Microsoft stratégiája, a Red Teaming és a Live site bevezetési tesztek végrehajtása is használható.

- [Behatolás-tesztelés az Azure-ban](../security/fundamentals/pen-testing.md)

- [Behatolás-tesztelési szabályok engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud vörös összevonása](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center figyelés** : nem alkalmazható

**Felelősség** : ügyfél

## <a name="endpoint-security"></a>Endpoint Security

*További információt az [Azure biztonsági teljesítményteszt: Endpoint Security](/azure/security/benchmarks/security-controls-v2-endpoint-security)című témakörben talál.*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: végpont-észlelés és-válasz használata (EDR)

**Útmutató** : az ügyfelek számára nincs kifejezetten engedélyezve a végpont-észlelés és a válasz beállításainak konfigurálása. Az Azure virtuális WAN-ajánlatokban használt Virtual Machines azonban ezeket a képességeket használják. További információ ezekről az általános képességekről a hivatkozott hivatkozásokon. 

- [A Microsoft Defender komplex veszélyforrások elleni védelem áttekintése](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Microsoft Defender ATP-szolgáltatás Windows-kiszolgálókhoz](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Microsoft Defender ATP szolgáltatás a nem Windows rendszerű kiszolgálókhoz](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Azure Security Center figyelés** : igen

**Felelősség** : megosztott

## <a name="governance-and-strategy"></a>Irányítás és stratégia

*További információt az [Azure biztonsági teljesítményteszt: kormányzás és stratégia](/azure/security/benchmarks/security-controls-v2-governance-strategy)című témakörben talál.*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: eszközkezelés és adatvédelmi stratégia meghatározása 

**Útmutató** : a rendszerek és az információk folyamatos monitorozására és védelmére vonatkozó egyértelmű stratégia dokumentálása és közlése. Az üzleti szempontból kritikus fontosságú adatmennyiségek és rendszerek felderítésének, értékelésének, védelmének és figyelésének rangsorolása. 

Ennek a stratégiának tartalmaznia kell a következő elemek dokumentált útmutatását, szabályzatát és szabványait: 

- Az adatbesorolási szabvány az üzleti kockázatoknak megfelelően
- A biztonsági szervezet betekintést nyerhet a kockázatokra és az eszközök leltározására 
- Az Azure-szolgáltatások használatának biztonsági szervezet általi jóváhagyása 
- Az eszközök biztonsága életcikluson keresztül
- A szükséges hozzáférés-vezérlési stratégia a szervezeti adatbesorolásnak megfelelően
- Az Azure Native és a harmadik féltől származó adatvédelmi képességek használata
- Adattitkosítási követelmények átvitel közbeni és nyugalmi használati esetekben
- Megfelelő titkosítási szabványok

Tekintse át a hivatkozott hivatkozásokon elérhető további információkat.

- [Azure biztonsági architektúra – javaslat – tárolás, adatkezelés és titkosítás](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Az Azure biztonsági alapjai – Azure-adatbiztonság, titkosítás és tárolás](../security/fundamentals/encryption-overview.md)

- [Felhőalapú bevezetési keretrendszer – az Azure adatbiztonsági és-titkosítási ajánlott eljárásai](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

**Azure Security Center figyelés** : jelenleg nem érhető el

**Felelősség** : ügyfél

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: vállalati szegmentálási stratégia definiálása 

**Útmutató** : hozzon létre egy nagyvállalati stratégiát, amely az eszközök hozzáférésének szegmentálására szolgál az identitás, a hálózat, az alkalmazás, az előfizetés, a felügyeleti csoport és más vezérlők kombinációjával. Körültekintően kell elkülöníteni a biztonsági elkülönítés szükségességét annak érdekében, hogy lehetővé váljon a rendszerek napi működésének engedélyezése, amelyeknek kommunikálnia kell egymással, és az adathozzáférésre van szükségük.

Győződjön meg arról, hogy a szegmentálási stratégia következetesen van implementálva a különböző típusú vezérlőkben, beleértve a hálózati biztonságot, az identitás-és hozzáférési modelleket, valamint az alkalmazás engedélyeit, a hozzáférési modelleket és az emberi folyamatok

- [Útmutató a szegmentálási stratégiához az Azure-ban (videó)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Útmutató a szegmentálási stratégiához az Azure-ban (dokumentum)](/security/compass/governance#enterprise-segmentation-strategy)

- [Hálózati szegmentálás igazítása nagyvállalati szegmentálási stratégiával](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center figyelés** : jelenleg nem érhető el

**Felelősség** : ügyfél

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: a biztonsági testhelyzet kezelési stratégiájának meghatározása

**Útmutató** : a kockázatok folyamatos mérése és mérséklése az egyes eszközökre, valamint a környezetbe, ahol a felhasználók futnak. Rangsorolja a nagy értékű eszközöket és a nagy teljesítményű támadási felületeket, például a közzétett alkalmazásokat, a hálózati bejövő és kimenő pontokat, a felhasználói és rendszergazdai végpontokat stb.

- [Azure biztonsági teljesítményteszt – a testtartás és a sebezhetőségek kezelése](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure Security Center figyelés** : jelenleg nem érhető el

**Felelősség** : ügyfél

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: szervezeti szerepkörök, felelősségek és elszámoltathatóság igazítása

**Útmutató** : gondoskodjon arról, hogy a biztonsági szervezetében a szerepkörökkel és a felelősségekkel kapcsolatos egyértelmű stratégiát dokumentáljon és tájékoztasson. A biztonsági döntések egyértelmű elszámoltathatósága alapján rangsorolhatja erőfeszítéseit, mindenki számára elérhetővé teheti a megosztott felelősségi modellt, és megtaníthatja a technológia technikai csapatait a felhő védelmére.

- [Azure Security – ajánlott eljárás 1 – emberek: a csapatok képzése a Felhőbeli biztonsági úton](https://aka.ms/AzSec1)

- [Azure Security – ajánlott eljárás 2 – emberek: a csapatok képzése a Felhőbeli biztonsági technológiában](https://aka.ms/AzSec2)

- [Azure Security – ajánlott eljárás 3 – folyamat: elszámoltathatóság kiosztása a Felhőbeli biztonsági döntésekhez](https://aka.ms/AzSec3)

**Azure Security Center figyelés** : jelenleg nem érhető el

**Felelősség** : ügyfél

### <a name="gs-5-define-network-security-strategy"></a>GS-5: hálózati biztonsági stratégia definiálása

**Útmutató** : hozzon létre egy Azure-alapú hálózati biztonsági megközelítést a szervezet általános biztonsági hozzáférés-vezérlési stratégiájának részeként. Ennek a stratégiának tartalmaznia kell a következő elemek dokumentált útmutatását, szabályzatát és szabványait: 

- Központosított Hálózatkezelés és biztonsági felelősség
- A virtuális hálózat szegmentálási modellje a nagyvállalati szegmentálási stratégiához igazodik
- Szervizelési stratégia a különböző veszélyforrások és támadási helyzetekben
- Az Internet Edge és a bejövő forgalom és a kimenő forgalom stratégiája
- Hibrid felhő és helyszíni összekapcsolási stratégia
- Naprakész hálózati biztonsági összetevők (például hálózati diagramok, hivatkozási hálózati architektúra)

Tekintse át a hivatkozott hivatkozásokon elérhető további információkat.

- [Azure Security – ajánlott eljárás 11 – architektúra. Egyetlen egységes biztonsági stratégia](https://aka.ms/AzSec11)

- [Azure biztonsági teljesítményteszt – hálózati biztonság](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Az Azure hálózati biztonság áttekintése](../security/fundamentals/network-overview.md)

- [Vállalati hálózati architektúra stratégia](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center figyelés** : jelenleg nem érhető el

**Felelősség** : ügyfél

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: identitás-és privilegizált hozzáférési stratégia meghatározása

**Útmutató** : az Azure-identitás és az emelt szintű hozzáférési megközelítések létrehozása a szervezet általános biztonsági hozzáférés-vezérlési stratégiájának részeként. Ennek a stratégiának tartalmaznia kell a következő elemek dokumentált útmutatását, szabályzatát és szabványait: 

- Központi identitás-és hitelesítési rendszer, valamint az egyéb belső és külső identitási rendszerekkel való kapcsolat
- Erős hitelesítési módszerek különböző használati esetekben és kikötésekben
- Magas jogosultsági szintű felhasználók védelme
- Felhasználói tevékenységek rendellenességének figyelése és feldolgozása  
- Felhasználói identitás és hozzáférés felülvizsgálata és egyeztetési folyamata

Tekintse át a hivatkozott hivatkozásokon elérhető további információkat.

- [Azure biztonsági teljesítményteszt – Identitáskezelés](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure biztonsági teljesítményteszt – emelt szintű hozzáférés](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure Security – ajánlott eljárás 11 – architektúra. Egyetlen egységes biztonsági stratégia](https://aka.ms/AzSec11)

- [Az Azure Identity Management biztonsági áttekintése](../security/fundamentals/identity-management-overview.md)

**Azure Security Center figyelés** : jelenleg nem érhető el

**Felelősség** : ügyfél

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: a naplózási és a veszélyforrások reagálási stratégiájának meghatározása

**Útmutató** : naplózási és veszélyforrási reagálási stratégia létrehozása a fenyegetések gyors észlelése és elhárítása érdekében a megfelelőségi követelmények teljesítése mellett. Rangsorolja az elemzőket kiváló minőségű riasztásokkal és zökkenőmentes tapasztalatokkal, hogy az integráció és a manuális lépések helyett a fenyegetésekre összpontosítsanak. 

Ennek a stratégiának tartalmaznia kell a következő elemek dokumentált útmutatását, szabályzatát és szabványait: 

- A biztonsági műveletek (SecOps) szervezet szerepköre és feladatai
- Egy jól definiált incidens-reagálási folyamat NIST vagy más iparági keretrendszerrel való összehangolása
- A fenyegetések észlelését, az incidensek megválaszolását és a megfelelőségi igényeket támogató napló rögzítése és megőrzése
- Központilag látható és korrelációs információk a fenyegetésekről, a SIEM, a natív Azure-képességek és más források – kommunikációs és értesítési terv az ügyfelekkel, a szállítókkal és a közérdekű felekkel
- Az Azure Native és a harmadik féltől származó platformok használata az incidensek kezelésére, mint például a naplózás és a veszélyforrások észlelése, a kriminalisztika, valamint a támadási problémák elhárítása és felszámolása
- Az incidensek és az incidens utáni tevékenységek kezelésének folyamatai, például a tanulságok és a bizonyítékok megőrzése

Tekintse át a hivatkozott hivatkozásokon elérhető további információkat.
- [Azure biztonsági teljesítményteszt – naplózás és fenyegetések észlelése](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure biztonsági teljesítményteszt – incidens válasza](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security – ajánlott eljárás 4 – folyamat. Incidensek frissítési folyamatainak frissítése a felhőben](https://aka.ms/AzSec4)

- [Azure bevezetési keretrendszer, naplózás és jelentéskészítési döntési útmutató](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Azure Security Center figyelés** : jelenleg nem érhető el

**Felelősség** : ügyfél

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure Security teljesítményteszt v2 áttekintése](/azure/security/benchmarks/overview) című témakört.
- További információ az [Azure biztonsági alaptervekről](/azure/security/benchmarks/security-baselines-overview)
