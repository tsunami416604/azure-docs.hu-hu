---
title: Azure Security alapkonfiguráció az Azure HPC cache-hez
description: Az Azure HPC cache biztonsági alapkonfigurációja eljárási útmutatást és erőforrásokat biztosít az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8a1202d4c29f7e298346696dfc11b40326758029
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301744"
---
# <a name="azure-security-baseline-for-azure-hpc-cache"></a>Azure Security alapkonfiguráció az Azure HPC cache-hez

Ez a biztonsági alapterv az [Azure biztonsági teljesítményteszt 2,0-es verziójáról](../security/benchmarks/overview.md) Microsoft Azure HPC-gyorsítótárra vonatkozó útmutatást alkalmaz. Az Azure biztonsági teljesítménytesztje javaslatokat nyújt ahhoz, hogyan védheti meg felhőalapú megoldásait az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** és az Azure HPC cache-re vonatkozó kapcsolódó útmutatás szerint van csoportosítva. Az Azure HPC-gyorsítótárra nem alkalmazható **vezérlők** ki lettek zárva.

Ha szeretné megtekinteni, hogy az Azure HPC gyorsítótára hogyan felel meg teljesen az Azure biztonsági Teljesítménytesztének, tekintse meg a [teljes Azure HPC cache biztonsági alapkonfiguráció-hozzárendelési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információt az [Azure biztonsági teljesítményteszt: hálózati biztonság](/azure/security/benchmarks/security-controls-v2-network-security)című témakörben talál.*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: a belső forgalom biztonságának megvalósítása

**Útmutató**: az Azure HPC gyorsítótár-erőforrásainak üzembe helyezésekor létre kell hoznia vagy használnia kell egy meglévő virtuális hálózatot. 

Győződjön meg arról, hogy az összes Azure-beli virtuális hálózat olyan vállalati szegmentálási elvet követ, amely az üzleti kockázatokhoz igazodik. A szervezet számára nagyobb kockázatot jelentő rendszereknek el kell különíteni a saját virtuális hálózatán belül, és megfelelően védeni kell őket egy hálózati biztonsági csoporttal (NSG) és/vagy Azure Firewallával.

A gyorsítótár használata előtt két hálózattal kapcsolatos előfeltételt kell beállítani: 

- Dedikált alhálózat az Azure HPC cache-példányhoz

- DNS-támogatás, hogy a gyorsítótár hozzáférhessen a tárolóhoz és egyéb erőforrásokhoz

Az Azure HPC cache-nek dedikált alhálózatra van szüksége a következő tulajdonságokkal: 

- Az alhálózatnak elérhetőnek kell lennie legalább 64 IP-címmel.

- Az alhálózat nem tud más virtuális gépeket üzemeltetni, még a kapcsolódó szolgáltatások, például az ügyfélszámítógépek esetében is.

- Ha több Azure HPC cache-példányt használ, mindegyiknek saját alhálózatra van szüksége.

Az ajánlott eljárás az, ha új alhálózatot hoz létre minden gyorsítótárhoz. Létrehozhat egy új virtuális hálózatot és alhálózatot a gyorsítótár létrehozásának részeként.

Ha a HPC-gyorsítótárat a helyszíni NAS-tárolóval szeretné használni, gondoskodnia kell arról, hogy a helyszíni hálózat bizonyos portjai lehetővé tegyék a nem korlátozott adatforgalom használatát az Azure HPC cache alhálózatán. 

- [Portok konfigurálása az NFS-tárolóhoz való hozzáféréshez](hpc-cache-prerequisites.md#nfs-storage-requirements)

- [Hálózati biztonsági csoport létrehozása biztonsági szabályokkal](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Azure Firewall üzembe helyezése és konfigurálása](../firewall/tutorial-firewall-deploy-portal.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="ns-2-connect-private-networks-together"></a>NS-2: magánhálózati hálózatok összekapcsolása

**Útmutató**: az Azure ExpressRoute vagy az Azure virtual private Network (VPN) használatával privát kapcsolatokat hozhat létre az Azure-adatközpontok és a helyszíni infrastruktúra között egy közös elhelyezésű környezetben. A ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át, így megbízhatóbbak, gyorsabbak és kevesebb késéssel rendelkeznek, mint a szokásos internetes kapcsolatok. Pont – hely típusú VPN-és helyek közötti VPN esetén a VPN-beállítások és az Azure ExpressRoute bármely kombinációja segítségével a helyszíni eszközöket vagy hálózatokat egy virtuális hálózathoz is összekapcsolhatja. 

Ha két vagy több virtuális hálózatot szeretne összekapcsolni az Azure-ban, használja a virtuális hálózati társítást. A kiszolgált virtuális hálózatok közötti hálózati forgalom magán, és az Azure-beli gerinces hálózaton marad.

- [A ExpressRoute kapcsolati modelljei](../expressroute/expressroute-connectivity-models.md) 

- [Az Azure VPN áttekintése](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Virtuális hálózati társviszony](../virtual-network/virtual-network-peering-overview.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: magánhálózati hozzáférés létrehozása az Azure-szolgáltatásokhoz

**Útmutató**: az Azure Virtual Network szolgáltatás-végpontok használatával biztonságos hozzáférést BIZTOSÍTHAT a HPC-gyorsítótárhoz. A szolgáltatási végpontok az Azure gerinces hálózaton az Internet átlépése nélkül optimalizált útvonalon találhatók. 

A HPC-gyorsítótár nem támogatja az Azure Private-hivatkozás használatát a felügyeleti végpontok magánhálózaton való biztonságossá tételéhez. 

Az Azure-szolgáltatások által kínált hitelesítés és adatforgalmi biztonság mellett a privát hozzáférés egy további, részletes védelmi mérőszámot is biztosít.

- [Virtual Network szolgáltatási végpontok ismertetése](../virtual-network/virtual-network-service-endpoints-overview.md) 

- [Az Azure HPC-gyorsítótár csatlakoztatásának ismertetése](hpc-cache-mount.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: a külső hálózati támadásoktól származó alkalmazások és szolgáltatások elleni védelem

**Útmutató**: a HPC gyorsítótár-erőforrásainak védelmet nyújt a külső hálózatokból érkező támadások ellen, beleértve az elosztott szolgáltatásmegtagadási (DDoS) támadásokat, az alkalmazás-specifikus támadásokat, valamint a kéretlen és potenciálisan kártékony internetes forgalmat. 

Az Azure a védelem natív funkcióit tartalmazza: 

- A Azure Firewall használatával védelmet biztosíthat az alkalmazások és szolgáltatások számára az internetről és más külső helyekről származó, potenciálisan rosszindulatú forgalom ellen. 
- Az adatvédelmet a DDoS-támadások ellen biztosíthatja az Azure-beli virtuális hálózatokon a DDoS standard szintű védelem engedélyezésével. 
- A hálózati erőforrásokkal kapcsolatos hibás konfigurációs kockázatok észleléséhez Azure Security Center használata.

Az Azure HPC cache nem a webalkalmazások futtatására szolgál, és nincs szükség további beállítások konfigurálására vagy további hálózati szolgáltatások telepítésére a webalkalmazásokra irányuló külső hálózati támadások elleni védelemhez.

- [Azure Firewall dokumentáció](/azure/firewall/) 

- [Azure DDoS Protection standard kezelése a Azure Portal használatával](/azure/virtual-network/manage-ddos-protection) 

- [Az Azure Security Centerre vonatkozó ajánlások](../security-center/recommendations-reference.md#recs-network)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: a Azure Firewall és a fenyegetés-intelligencián alapuló szűrés használata az ismert kártékony IP-címekre és tartományokra irányuló, illetve onnan érkező adatforgalom blokkolására és/vagy letiltására. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája. 

Ha szükség van a hasznos adatok vizsgálatára, egy külső gyártótól származó behatolás-észlelési/beléptetési rendszer (IDS/IP) megoldását is üzembe helyezheti az Azure Marketplace-en a hasznos adatok ellenőrzésére szolgáló funkciókkal. Másik lehetőségként dönthet úgy, hogy gazdagép-alapú AZONOSÍTÓkat/IP-címeket vagy gazdagép-alapú végpont-észlelési és-gyorsreagáló (EDR) megoldást használ a hálózat-alapú AZONOSÍTÓk/IP-címek helyett vagy ahelyett.

Megjegyzés: Ha az AZONOSÍTÓk/IP-címek használatára vonatkozó előírás vagy egyéb követelmény van, győződjön meg arról, hogy mindig úgy van hangolva, hogy magas színvonalú riasztásokat nyújtson az SIEM-megoldáshoz.

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md) 

- [Az Azure Marketplace harmadik féltől származó azonosító képességei](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Microsoft Defender ATP EDR-képesség](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: a hálózati biztonsági szabályok egyszerűsítése

**Útmutató**: nem alkalmazható; Ez az ajánlat az Azure-beli virtuális hálózatokon üzembe helyezhető ajánlatok számára készült, illetve lehetővé teszi az engedélyezett IP-címtartományok csoportosításának hatékony felügyeletét. A HPC-gyorsítótár jelenleg nem támogatja a szolgáltatási címkéket. 

Az ajánlott eljárás az, ha új alhálózatot hoz létre minden gyorsítótárhoz. Létrehozhat egy új virtuális hálózatot és alhálózatot a gyorsítótár létrehozásának részeként.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: biztonságos tartománynév szolgáltatás (DNS)

**Útmutató**: kövesse a DNS-biztonság ajánlott eljárásait a gyakori támadásokkal szemben, például a DNS, a DNS-erősítési támadások, a DNS-mérgezés és a hamisítás, valamint mások számára.

Az Azure HPC gyorsítótárának DNS-re van szüksége a gyorsítótár magánhálózati virtuális hálózatán kívüli erőforrásokhoz való hozzáféréshez. Ha a munkafolyamat az Azure-on kívüli erőforrásokat is tartalmaz, akkor a Azure DNS használata mellett be kell állítania és biztonságossá kell tennie a saját DNS-kiszolgálóját.

- Az Azure Blob Storage-végpontok, az Azure-alapú ügyfélszámítógépek vagy más Azure-erőforrások eléréséhez használja a Azure DNS.
- Ha a helyszíni tárterületet szeretné elérni, vagy az Azure-on kívüli ügyfelekről szeretne csatlakozni a gyorsítótárhoz, létre kell hoznia egy egyéni DNS-kiszolgálót, amely képes a gazdagépek feloldására.
- Ha a munkafolyamat belső és külső erőforrásokat is tartalmaz, állítsa be az egyéni DNS-kiszolgálót, hogy továbbítsa az Azure-specifikus feloldási kéréseket a Azure DNS-kiszolgálónak. 

Ha a rendszer a mérvadó DNS-szolgáltatásként Azure DNS használja, ügyeljen arra, hogy a DNS-zónák és-rekordok védve legyenek az Azure RBAC és az erőforrás-zárolások használatával a véletlen vagy rosszindulatú módosításokkal szemben.

Ha saját DNS-kiszolgálót konfigurál, kövesse az alábbi biztonsági irányelveket:

- [A biztonságos tartománynévrendszer (DNS) üzembe helyezési útmutatója](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [A DNS-bejegyzések letiltásának és a tartományon belüli átvétel elkerülésének megakadályozása](../security/fundamentals/subdomain-takeover.md) 

- [További információ a HPC gyorsítótár DNS-hozzáférési követelményeiről](hpc-cache-prerequisites.md#dns-access)

- [Azure DNS áttekintése](../dns/dns-overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="identity-management"></a>Identitáskezelés

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: identitáskezelés](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Az Azure Active Directory szabványosítása központi identitáskezelési és hitelesítési rendszerként

**Útmutató**: az Azure HPC cache nincs integrálva a Azure Active Directory belső műveletekhez. Az Azure AD azonban a Azure Portal vagy parancssori felületen lévő felhasználók hitelesítésére is használható a HPC gyorsítótár-telepítések és a kapcsolódó összetevők létrehozásához, megtekintéséhez és kezeléséhez.

Azure Active Directory (Azure AD) az alapértelmezett identitás-és hozzáférés-kezelési szolgáltatás az Azure-ban. Egységesítse az Azure AD-t a szervezet identitás-és hozzáférés-kezelésének szabályozásához a ben:

- Microsoft Cloud erőforrások, például a Azure Portal, az Azure Storage, az Azure virtuális gép (Linux és Windows), a Azure Key Vault, a Pásti és az SaaS-alkalmazások.

- a szervezet erőforrásaiban, például az Azure-on vagy a vállalati hálózat erőforrásain lévő alkalmazásokban.

Kitüntetett figyelmet kell fordítani rá a cég vagy szervezet felhőalapú biztonsági gyakorlatában, hogy az Azure AD-t biztonságossá tegyék. Az Azure AD egy biztonságos azonosítási pontszámot biztosít, amellyel a Microsoft ajánlott eljárási javaslataihoz képest kiértékelheti a személyazonosság biztonsági helyzetét. A pontszám segít felmérni, hogy a használt konfiguráció milyen mértékben felel meg az ajánlott eljárásokkal kapcsolatos javaslatoknak, és javíthatja a cég vagy szervezet biztonsági állapotát.

Megjegyzés: az Azure AD támogatja a külső identitást, amely lehetővé teszi a felhasználók számára, hogy Microsoft-fiók nélkül bejelentkezzenek alkalmazásaiba és erőforrásaiba külső identitásával.

- [Bérlet Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Külső identitásszolgáltatók használata alkalmazásokhoz](/azure/active-directory/b2b/identity-providers) 

- [Mi az identitások biztonságos pontszáma Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: az alkalmazás-identitások biztonságos és automatikus kezelése

**Útmutató**: a HPC-gyorsítótár Azure által felügyelt identitásokat használ a nem emberi fiókok, például a szolgáltatások vagy az automatizálás számára. Javasoljuk, hogy az Azure felügyelt identitási funkcióját használja, és ne hozzon létre erősebb emberi fiókot az erőforrások eléréséhez vagy végrehajtásához. 

A HPC-gyorsítótár natív módon hitelesíthető az Azure AD-hitelesítést támogató Azure-szolgáltatásokkal/erőforrásokkal előre definiált hozzáférési engedélyezési szabályok segítségével. Ezzel elkerülhető, hogy a forráskódban vagy a konfigurációs fájlokban nehezen kódolt hitelesítő adatokat kelljen használni.

- [Azure által felügyelt identitások](../active-directory/managed-identities-azure-resources/overview.md) 

- [Az Azure-erőforrások felügyelt identitásait támogató szolgáltatások](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Azure AD egyszeri bejelentkezés (SSO) használata az alkalmazások hozzáféréséhez

**Útmutató**: az Azure HPC cache nem integrálható az Azure ad-vel belső műveletekhez. Az Azure AD azonban a Azure Portal vagy parancssori felületen lévő felhasználók hitelesítésére is használható a HPC gyorsítótár-telepítések és a kapcsolódó összetevők létrehozásához, megtekintéséhez és kezeléséhez.

A Azure Active Directory identitás-és hozzáférés-kezelést biztosít az Azure-erőforrásokhoz, a felhőalapú alkalmazásokhoz és a helyszíni alkalmazásokhoz. Ennek a hatóköre a vállalati identitásokra, például az alkalmazottakra, valamint a külső identitásokra, például a partnerekre, a szállítókra és a szolgáltatókra is kiterjed. Egyszeri bejelentkezéssel (single sign-on, SSO) kezelhető és védhető a szervezet adatainak és erőforrásainak hozzáférése a helyszínen és a felhőben. Az összes felhasználót, alkalmazást és eszközt az Azure AD-hez csatlakoztathatja a zökkenőmentes, biztonságos hozzáférés, a jobb átláthatóság és hatékonyabb kontroll érdekében.

- [SSO alkalmazásokhoz az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Erős hitelesítésvezérlők használata minden Azure Active Directory-alapú hozzáféréshez

**Útmutató**: bár az Azure HPC cache nem integrálható az Azure ad-vel belső műveletekhez, az Azure ad segítségével hitelesítheti a felhasználókat a Azure Portal vagy a CLI-ben a HPC gyorsítótár-telepítések és a kapcsolódó összetevők létrehozásához, megtekintéséhez és kezeléséhez.  

Az Azure AD a többtényezős hitelesítéssel (MFA) és az erős, jelszóval nem rendelkező metódusokkal támogatja az erős hitelesítési vezérlést.

- Többtényezős hitelesítés: engedélyezze az Azure AD MFA-t, és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait az MFA-telepítés egyes ajánlott eljárásaihoz. Az MFA kikényszeríthető minden felhasználóra, kiválaszthatja a felhasználókat vagy a felhasználónkénti szinten a bejelentkezési feltételek és kockázati tényezők alapján.
- Jelszó nélküli hitelesítés – három, jelszó nélküli hitelesítési lehetőség érhető el: a Vállalati Windows Hello, a Microsoft Authenticator alkalmazás és helyszíni hitelesítési módszerek, például intelligens kártyák.

A rendszergazda és a Kiemelt jogosultságú felhasználók számára ügyeljen arra, hogy az erős hitelesítési módszer legmagasabb szintjét használja. Állítsa be a megfelelő erős hitelesítési szabályzatot más felhasználók számára.

Az Azure HPC cache támogatja a régi jelszó-alapú hitelesítést is a gyorsítótárhoz csatlakozó ügyfélszámítógépeken. Ilyen típusú kapcsolatok például a csak felhőalapú fiókok (közvetlenül az Azure-ban létrehozott felhasználói fiókok), amelyek alapkonfigurációt vagy hibrid fiókokat tartalmaznak (a helyszíni Active Directoryból származó felhasználói fiókokat), amelyek követik a helyszíni jelszóházirend-házirendeket. 

A jelszó-alapú hitelesítés használatakor az Azure AD jelszavas védelmet biztosít, amely megakadályozza, hogy a felhasználók könnyen kitalált jelszavakat állítsanak be. A Microsoft globális listát biztosít a telemetria alapján frissített tiltott jelszavakról, és az ügyfelek igényeik alapján is kiterjeszthetik a listát (például branding, kulturális referenciák stb.). Ez a jelszavas védelem csak felhőalapú és hibrid fiókok esetében használható.

Megjegyzés: a jelszó-hitelesítő adatokon alapuló hitelesítés csak a népszerű támadási módszerekre van kitéve. A nagyobb biztonság érdekében használjon erős hitelesítést, például MFA-t és egy erős jelszóházirend-beállítást. Ha olyan külső gyártótól származó alkalmazásokat és Piactéri szolgáltatásokat használ, amelyek alapértelmezett jelszavai vannak, állítson be egy új, biztonságos jelszót a szolgáltatás első beállításakor. 

- [Az MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Az Azure Active Directory jelszó nélküli hitelesítési lehetőségeinek ismertetése](../active-directory/authentication/concept-authentication-passwordless.md)

- [Az Azure AD alapértelmezett jelszószabályzata](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [A nem megfelelő jelszavak használatának kiküszöbölése az Azure AD Password Protectionnel](../active-directory/authentication/concept-password-ban-bad.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Fiókok rendellenességeinek monitorozása és a hozzájuk kapcsolódó riasztások

**Útmutató**: az Azure HPC cache Azure Active Directoryt használhat a Azure Portal felhasználói felügyeletéhez.  Azure Active Directory a következő adatforrásokat biztosítja:

- Bejelentkezések – a bejelentkezési jelentés a felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információkat biztosít.

- Auditnaplók – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplók rögzítik például az erőforrások módosításait az Azure AD-n belül, például a felhasználók, alkalmazások, csoportok, szerepkörök és szabályzatok hozzáadását vagy eltávolítását.

- Kockázatos bejelentkezések – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa.

- Kockázatosként megjelölt felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

Ezek az adatforrások a Azure Monitor, az Azure Sentinel vagy a harmadik féltől származó SIEM rendszerekkel integrálhatók.

A Azure Security Center riasztást is beállíthat bizonyos gyanús tevékenységekre, például a sikertelen hitelesítési kísérletek túlzott számára és az előfizetés elavult fiókjaira.

Az Azure Advanced Threat Protection (ATP) olyan biztonsági megoldás, amely Active Directory-jelek használatával tudja azonosítani, észlelni és kivizsgálni a súlyos fenyegetéseket, a sérült identitásokat és a rosszindulatú belső műveleteket.

- [Naplózási tevékenységek jelentései Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)

 

- [Riasztások az Azure Security Center fenyegetések felderítésére szolgáló védelmi moduljában](../security-center/alerts-reference.md) 

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: a nem szándékolt hitelesítő adatok expozíciójának megszüntetése

**Útmutató**: nem alkalmazható; A HPC-gyorsítótár nem teszi lehetővé, hogy az ügyfelek a futó környezetbe helyezzen el semmilyen megőrzött adattárolást.

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="privileged-access"></a>Emelt szintű hozzáférés

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: emelt szintű hozzáférés](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: az üzleti szempontból kritikus fontosságú rendszerek rendszergazdai hozzáférésének korlátozása

**Útmutató**: a HPC cache az Azure RBAC segítségével elkülöníti az üzleti szempontból kritikus rendszerek hozzáférését azáltal, hogy korlátozza, hogy mely fiókok kapnak jogosultsági szintű hozzáférést az előfizetésekhez és a felügyeleti csoportokhoz.

Hozzon létre szabványos működési eljárásokat a dedikált rendszergazdai fiókok használata körül. Gyorsítótár létrehozásához a HPC cache megköveteli, hogy a felhasználók megfelelő jogosultságokkal rendelkezzenek az előfizetésben a hálózati adapterek létrehozásához. BLOB Storage használata esetén az Azure roles Storage-fiók közreműködői és a Storage blob-adatközreműködői a HPC-gyorsítótár eléréséhez szükségesek a tárolóhoz való hozzáféréshez. 

Győződjön meg arról, hogy a felügyeleti, identitási és biztonsági rendszerekhez való hozzáférést is korlátozza, amelyek rendszergazdai hozzáféréssel rendelkeznek az üzleti szempontból kritikus fontosságú eszközökhöz, például a Active Directory-tartomány vezérlőkhöz (DCs), a biztonsági eszközökhöz és a Rendszerfelügyeleti eszközökhöz, amelyek az üzleti szempontból kritikus rendszerekre telepített ügynökökkel rendelkeznek. Azok a támadók, akik veszélyeztetik ezeket a felügyeleti és biztonsági rendszereket, azonnal weaponize azokat az üzleti szempontból kritikus fontosságú eszközök biztonsága érdekében.

A hozzáférés-vezérlés összes típusát a vállalati szegmentálási stratégiához kell igazítani, hogy biztosítsa a konzisztens hozzáférés-vezérlést.

- [Az Azure HPC cache RBAC engedélyei](hpc-cache-prerequisites.md#permissions)

- [Azure-összetevők és-hivatkozási modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Felügyeleti csoport elérése](../governance/management-groups/overview.md#management-group-access)

- [Azure-előfizetés rendszergazdái](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: A felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: rendszeresen tekintse át a felhasználói fiókokat és a hozzáférés-hozzárendelést, hogy a fiókok és hozzáférési szintjeik érvényesek legyenek. 

Az Azure HPC cache Azure Active Directory (Azure AD) fiókokkal felügyelheti a felhasználói hozzáférést a Azure Portal és a kapcsolódó felületeken keresztül. Az Azure AD olyan hozzáférési felülvizsgálatokat biztosít, amelyek segítséget nyújtanak a csoporttagságok áttekintésében, a vállalati alkalmazásokhoz való hozzáférésben és a szerepkör-hozzárendelésekben. Az Azure AD jelentéskészítési szolgáltatása lehetővé teszi, hogy a naplók segítséget nyújtsanak az elavult fiókok felderítéséhez. A felülvizsgálati folyamat megkönnyítése érdekében a Azure AD Privileged Identity Management használatával hozzáférési felülvizsgálati jelentési munkafolyamatot is létrehozhat.

Emellett az Azure Privileged Identity Management beállítható úgy, hogy riasztást hozzon létre túl sok rendszergazdai fiók létrehozásakor, valamint a elavult vagy helytelenül konfigurált rendszergazdai fiókok azonosításához.

Megjegyzés: egyes Azure-szolgáltatások támogatják a helyi felhasználókat és szerepköröket, amelyek nem az Azure AD-n keresztül kezelhetők. Ezeket a felhasználókat külön kell kezelnie.

Ha NFS-tárolási célokat használ, a hálózati rendszergazdákkal és a tűzfalakkal együtt kell működnie a hozzáférési beállítások ellenőrzéséhez, és biztosítania kell, hogy az Azure HPC cache képes legyen kommunikálni az NFS Storage-rendszerekkel.

- [A HPC cache-engedélyek listáját az Azure HPC gyorsítótárának előfeltételei című cikkben olvashatja.](hpc-cache-prerequisites.md) 

- [Azure-beli erőforrás-szerepkörök hozzáférési felülvizsgálatának létrehozása Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../active-directory/governance/access-reviews-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: vészhelyzeti hozzáférés beállítása az Azure AD-ben

**Útmutató**: a HPC-gyorsítótár a Azure Active Directory használatával felügyelheti az erőforrás-hozzáférést a Azure Portalon keresztül. Ha nem szeretné megakadályozni, hogy véletlenül kizárja az Azure AD-szervezetét, állítson be egy vészhelyzeti hozzáférési fiókot a hozzáféréshez, ha a normál rendszergazdai fiókok nem használhatók. A vészhelyzeti hozzáférési fiókok általában magas jogosultsággal rendelkeznek, és nem rendelhetők hozzá konkrét személyekhez. A vészhelyzeti hozzáférési fiókok olyan vészhelyzeti vagy "break Glass" forgatókönyvekre korlátozódnak, amelyekben nem használhatók normál rendszergazdai fiókok.

Győződjön meg arról, hogy a vészhelyzeti hozzáférési fiókokhoz tartozó hitelesítő adatok (például jelszó, tanúsítvány vagy intelligens kártya) biztonságosak és ismertek, csak azokra a személyekre, akik jogosultak arra, hogy csak vészhelyzetben használják őket.

- [Vészhelyzeti hozzáférési fiókok kezelése az Azure AD-ben](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="pa-5-automate-entitlement-management"></a>PA-5: a jogosultságok kezelésének automatizálása 

**Útmutató**: a HPC-gyorsítótár a Azure Active Directory segítségével kezelheti az erőforrásokhoz való hozzáférést a Azure Portal keresztül. 

Az Azure AD-jogosultságok kezelési funkciói segítségével automatizálhatja a hozzáférési kérelmek munkafolyamatait, beleértve a hozzáférési hozzárendeléseket, az értékeléseket és a lejáratokat. A kettős vagy többfázisú jóváhagyás is támogatott. 

- [Az Azure AD hozzáférési felülvizsgálatai](../active-directory/governance/access-reviews-overview.md) 

- [Mi az Azure AD-jogosultságok kezelése?](../active-directory/governance/entitlement-management-overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: A Just Enough Administration (legkisebb jogosultsági alapelv) követése 

**Útmutató**: a HPC cache integrálva van az Azure szerepköralapú hozzáférés-vezérléssel (RBAC) az erőforrások kezeléséhez. Az Azure RBAC lehetővé teszi, hogy szerepkörök hozzárendelésével felügyelje az Azure-erőforrások hozzáférését. Ezeket a szerepköröket hozzárendelheti a felhasználókhoz, a csoportok egyszerű szolgáltatásaihoz és a felügyelt identitásokhoz. Bizonyos erőforrásokhoz előre meghatározott beépített szerepkörök tartoznak, és ezek a szerepkörök olyan eszközökkel leltározhatók vagy kérdezhetők le, mint az Azure CLI, az Azure PowerShell vagy az Azure Portal. 

Az erőforrásokhoz az Azure RBAC-on keresztül hozzárendelt jogosultságokat mindig a szerepkörök által igényelt értékre kell korlátozni. Ez kiegészíti a Azure AD Privileged Identity Management (PIM) igény szerinti (JIT) megközelítését, és rendszeresen felül kell vizsgálni.

A beépített szerepkörökkel engedélyeket oszthat ki, és csak akkor kell egyéni szerepköröket létrehoznia, ha szükséges.

- [Mi az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../role-based-access-control/overview.md) 

- [Az RBAC konfigurálása az Azure-ban](../role-based-access-control/role-assignments-portal.md) 

- [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../active-directory/governance/access-reviews-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: adatvédelem](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1: bizalmas adatok felderítése, besorolása és címkézése 

**Útmutató**: a HPC-gyorsítótár érzékeny adatokat kezel, de nem képes a bizalmas adatok felderítésére, besorolására és címkézésére.

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Megosztott

### <a name="dp-2-protect-sensitive-data"></a>DP-2: A bizalmas adatok védelme

**Útmutató**: a bizalmas adatok védelme az azure szerepköralapú Access Control (Azure RBAC), a hálózati hozzáférés-vezérlés és az Azure-szolgáltatások adott vezérlői (például az SQL és más adatbázisok titkosítása) használatával történő hozzáférés korlátozásával.

A következetes hozzáférés-vezérlés biztosításához a hozzáférés-vezérlés összes típusát a vállalati szegmentálási stratégiához kell igazítani. A vállalati szegmentálási stratégiát a bizalmas vagy üzleti szempontból kritikus fontosságú adatok és rendszerek helyének tudatában kell kialakítani.

A Microsoft által felügyelt mögöttes platformon a Microsoft az összes ügyféltartalmat bizalmasként kezeli, és védelmet nyújt az ügyfelek adatainak elvesztése és mások általi hozzáférése ellen. Ahhoz, hogy az ügyféladatok az Azure-on belül biztonságban maradjanak, a Microsoft implementált néhány alapértelmezett adatvédelmi vezérlőt és képességet.

- [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md) 

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Megosztott

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: A bizalmas adatok jogosulatlan átvitelének monitorozása

**Útmutató**: a HPC cache bizalmas adatokat továbbít, de nem támogatja a bizalmas adatok jogosulatlan átvitelének figyelését.

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Megosztott

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: bizalmas adatok titkosítása az átvitel során

**Útmutató**: a HPC-gyorsítótár támogatja az adattitkosítást a TLS 2.0-s vagy újabb verziókban.

Habár ez nem kötelező a magánhálózaton lévő forgalom esetében, ez kritikus fontosságú a külső és a nyilvános hálózatok forgalmában. HTTP-forgalom esetén győződjön meg arról, hogy az Azure-erőforrásokhoz csatlakozó összes ügyfél egyeztetheti a TLS v 1.2-es vagy újabb verzióját. A Távoli felügyelet érdekében titkosítatlan protokoll helyett használja az SSH-t (Linuxon) vagy RDP/TLS (Windows rendszeren). Az elavult SSL-, TLS-és SSH-verziók és protokollok, valamint a gyenge titkosítások le lesznek tiltva.

Alapértelmezés szerint az Azure titkosítást biztosít az Azure-adatközpontok közötti adatátvitelhez.

- [A titkosítás ismertetése az Azure-ban](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Információk a TLS biztonságáról](/security/engineering/solving-tls1-problem) 

- [Kettős titkosítás az Azure-beli adatforgalomban](../security/fundamentals/double-encryption.md#data-in-transit)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Inaktív bizalmas adatok titkosítása

**Útmutató**: a hozzáférés-vezérlés kiegészítéseként a REST-adatok védelme a "sávon kívüli" támadásoktól (például a mögöttes tárolóhoz való hozzáférés) a titkosítás használatával történik. Ezzel biztosíthatja, hogy a támadók ne tudják egyszerűen beolvasni vagy módosítani az adatforrást.

Az Azure alapértelmezés szerint biztosítja a REST-titkosítást. A szigorúan bizalmas adatok esetében lehetősége van arra, hogy az összes Azure-erőforráson további titkosítást hajtsanak végre, ahol elérhetők. Az Azure alapértelmezés szerint kezeli a titkosítási kulcsokat, az Azure azonban lehetőséget biztosít a saját kulcsok (az ügyfél által felügyelt kulcsok) kezelésére bizonyos Azure-szolgáltatásokhoz.

A rendszer alapértelmezés szerint a Microsoft által felügyelt kulcsok használatával titkosítja az Azure-ban tárolt összes, a gyorsítótár-lemezeket is tartalmazó adatforgalomat. Csak akkor kell testreszabnia az Azure HPC gyorsítótár beállításait, ha az adatai titkosításához használt kulcsokat szeretné kezelni.

Ha a megfelelőség szükséges a számítási erőforrásokon, hozzon létre egy harmadik féltől származó eszközt, például egy automatizált gazdagép-alapú adatvesztés-megelőzési megoldást, hogy az adatokra akkor is kényszerítse a hozzáférés-vezérlést, ha az adatok egy rendszerből lettek átmásolva.

- [Az ügyfél által felügyelt titkosítási kulcsok használata az Azure HPC cache használatával](https://docs.microsoft.com/azure/hpc-cache/hpc-cache-create?tabs=azure-portal#enable-azure-key-vault-encryption-optional)

- [Inaktív adatok Azure-ban való titkosításának ismertetése](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Az ügyfél által felügyelt titkosítási kulcsok konfigurálása](/azure/storage/common/storage-encryption-keys-portal) 

- [Titkosítási modell és kulcskezelő tábla](../security/fundamentals/encryption-atrest.md)

 

- [Inaktív adatok az Azure-ban – Rest kettős titkosítás](../security/fundamentals/double-encryption.md#data-at-rest)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Megosztott

## <a name="asset-management"></a>Asset Management (Eszközkezelés)

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: eszközkezelés](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Gondoskodás arról, hogy a biztonsági csapat átlássa az adategységek kockázatait

**Útmutató**: Győződjön meg arról, hogy a biztonsági csapatok biztonsági olvasó engedélyekkel rendelkeznek az Azure-bérlőben és -előfizetésekben, hogy monitorozni tudják a biztonsági kockázatokat az Azure Security Centerrel. 

A biztonsági kockázatok monitorozása a biztonsági csapat felelősségi köreinek struktúrájától függően egy központi biztonsági csapat vagy egy helyi csapat felelőssége lehet. A biztonsági megállapításokat és kockázatokat azonban mindig központilag kell összesíteni egy szervezeten belül. 

A biztonsági olvasó engedélyek széles körben alkalmazhatók egy teljes bérlőre (gyökérszintű felügyeleti csoport), vagy a hatókör alkalmazható adott felügyeleti csoportokra vagy előfizetésekre. 

Megjegyzés: A számítási feladatok és a szolgáltatások átláthatóvá tételéhez további engedélyek lehetnek szükségesek. 

- [A biztonsági olvasó szerepkör áttekintése](../role-based-access-control/built-in-roles.md#security-reader)

- [Az Azure-beli felügyeleti csoportok áttekintése](../governance/management-groups/overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: gondoskodjon arról, hogy a biztonsági csapat hozzáférjen az eszközök leltárához és metaadataihoz

**Útmutató**: az Azure HPC cache támogatja a címkék használatát. Címkéket alkalmazhat az Azure-erőforrások,-erőforráscsoportok és-előfizetések számára, hogy logikailag szervezze őket egy besorolásba. Minden címke egy nevet és egy érték párokat tartalmaz. 

Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra. A címkék a gyorsítótár létrehozásakor és a gyorsítótár üzembe helyezése után is hozzáadhatók. 

Az Azure-beli virtuális gépek Leltározásával automatizálhatja a Virtual Machines szoftverrel kapcsolatos információk gyűjtését. A szoftver neve, verziója, közzétevője és frissítési ideje elérhető a Azure Portal. A telepítés dátumának és egyéb információinak eléréséhez engedélyezze a vendég szintű diagnosztikát, és a Windows-eseménynaplókat egy Log Analytics-munkaterületre vigye.
A HPC-gyorsítótár nem teszi lehetővé az alkalmazások futtatását vagy a szoftverek telepítését az erőforrásokon. 

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md) 

- [Útmutató az erőforrások elnevezésével és címkézésével kapcsolatos döntésekhez](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json) 

- [Az Azure-beli virtuális gépek leltározásának engedélyezése](../automation/automation-tutorial-installed-software.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a HPC Cache a Azure Resource Manager üzemelő példányokat támogatja. A Azure Policy használatával naplózhatja és korlátozhatja, hogy mely szolgáltatások felhasználói tudnak kiépíteni a környezetben. Az Azure Resource Graph segítségével lekérdezheti és felderítheti az előfizetésen belüli erőforrásokat. A Azure Monitor használatával olyan szabályokat is létrehozhat, amelyek riasztásokat aktiválnak, ha a rendszer nem jóváhagyott szolgáltatást észlel.

- [Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Adott erőforrástípus megtagadása a következővel Azure Policy](/azure/governance/policy/samples/not-allowed-resource-types) 

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Az objektuméletciklus-kezelés biztonságának garantálása

**Útmutató**: nem alkalmazható. Az Azure HPC-gyorsítótár nem használható az életciklus-kezelési folyamatokban lévő eszközök biztonságának biztosításához. Az ügyfél feladata, hogy fenntartsa a nagy hatásnak számító eszközök attribútumait és hálózati konfigurációit. 

Javasoljuk, hogy az ügyfél hozzon létre egy folyamatot az attribútum és a hálózati konfiguráció módosításainak rögzítéséhez, mérje fel a változás hatását, és hozzon létre szervizelési feladatokat a megfelelő módon.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="logging-and-threat-detection"></a>Naplózás és fenyegetésészlelés

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: Naplózás és fenyegetésészlelés](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: az Azure-erőforrások fenyegetés-észlelésének engedélyezése

**Útmutató**: használja a Azure Security Center beépített veszélyforrások észlelésére szolgáló funkciót, és engedélyezze az Azure Defender (az Azure Advanced Threat Protection) szolgáltatást a HPC gyorsítótár-erőforrásaihoz. Az Azure Defender for HPC cache egy további biztonsági intelligenciát biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel a gyorsítótár erőforrásainak eléréséhez vagy kiaknázásához.

Továbbítson minden, a HPC-gyorsítótárból származó naplót a SIEM-hez, amely az egyéni veszélyforrások észlelésének beállítására használható. Győződjön meg arról, hogy a különböző típusú Azure-eszközöket figyeli a lehetséges fenyegetések és rendellenességek tekintetében. A magas színvonalú riasztások beszerzésével csökkentheti a hamis pozitív értéket az elemzők számára a rendezéshez. A riasztások naplózási adatokból, ügynökökből vagy más adatokból is származnak.

- [Fenyegetésvédelem az Azure Security Centerben](/azure/security-center/threat-protection) 

- [Azure Security Center biztonsági riasztások referenciája](../security-center/alerts-reference.md) 

- [Egyéni elemzési szabályok létrehozása a fenyegetések észleléséhez](../sentinel/tutorial-detect-threats-custom.md) 

- [A Cyber Threat Intelligence és az Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Fenyegetésészlelés engedélyezése az Azure-beli identitás- és hozzáférés-kezeléshez

**Útmutató**: az Azure ad a következő felhasználói naplókat jeleníti meg, amelyek megtekinthetők az Azure ad jelentéskészítési szolgáltatásban, vagy a Azure monitor, az Azure Sentinel vagy más Siem/monitoring eszközökkel integrálhatók a kifinomultabb monitorozási és elemzési felhasználási esetekben:
- Bejelentkezések – a bejelentkezési jelentés a felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információkat biztosít.

- Naplók – nyomon követheti a naplókat az Azure AD-n belüli különböző szolgáltatások által végzett összes módosításhoz. A naplók példái közé tartoznak az Azure AD-ban található erőforrások változásai, például felhasználók, alkalmazások, csoportok, szerepkörök és házirendek hozzáadása vagy eltávolítása.

- Kockázatos bejelentkezések – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa.

- Kockázatosként megjelölt felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

A Azure Security Center riasztást is tud kapni bizonyos gyanús tevékenységekről, például a sikertelen hitelesítési kísérletek túlzott számáról vagy az előfizetésben elavult fiókokról. A biztonsági állapot alapszintű monitorozása mellett Azure Security Center fenyegetésvédelmi modulja részletesebb biztonsági riasztásokat is gyűjthet az egyes Azure-beli számítási erőforrásokból (virtuális gépek, tárolók, App Service), adatforrásokból (SQL DB és Storage) és az Azure-szolgáltatásrétegekből. Ez a funkció lehetővé teszi, hogy megtekintse az egyes erőforrásokon belüli fiókok rendellenességeit.

- [Naplózási tevékenységek jelentései Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Az Azure Identity Protection engedélyezése](../active-directory/identity-protection/overview-identity-protection.md) 

- [Fenyegetésvédelem az Azure Security Centerben](/azure/security-center/threat-protection)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: az Azure hálózati tevékenységek naplózásának engedélyezése

**Útmutató**: a VPN-átjárók és a hozzájuk tartozó csomagok rögzítési képességei is használhatók a virtuális hálózatok közötti hálózati csomagok rögzítésére szolgáló, általánosan elérhető csomag-rögzítési eszközökön kívül.

Helyezzen üzembe egy hálózati biztonsági csoportot azon a hálózaton, amelyen az Azure HPC gyorsítótár-erőforrásai üzembe vannak helyezve. Engedélyezze a hálózati biztonsági csoport adatfolyam-naplóit a hálózati biztonsági csoportokon a forgalom naplózásához.

A folyamat naplófájljai megmaradnak egy Storage-fiókban. A Traffic Analytics megoldás lehetővé teszi, hogy feldolgozza és elküldje ezeket a flow-naplókat egy Log Analytics munkaterületre. A Traffic Analytics további betekintést nyújt az Azure-hálózatok forgalmának folyamatára. A Traffic Analytics segítségével megjelenítheti a hálózati tevékenységeket, azonosíthatja a gyors helyeket, azonosíthatja a biztonsági fenyegetéseket, megismerheti a forgalmi folyamatok mintáit, és meghatározhatja a hálózati helytelen konfigurációkat.

A gyorsítótárnak szüksége van a DNS-re a virtuális hálózatán kívüli erőforrásokhoz való hozzáféréshez. Attól függően, hogy melyik erőforrást használja, lehet, hogy be kell állítania egy testreszabott DNS-kiszolgálót, és konfigurálnia kell a továbbítást a kiszolgáló és a Azure DNS kiszolgálók között. 

Egy harmadik féltől származó megoldás implementálása az Azure Marketplace-ről a DNS-naplózási megoldáshoz, amelyet a szervezeteknek szüksége van.

- [A csomagok rögzítésének konfigurálása a VPN-átjárók számára](../vpn-gateway/packet-capture.md) 

- [Hálózati biztonsági csoport folyamatábráinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md) 

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md) 

- [További információ a DNS-előfeltételekről](hpc-cache-prerequisites.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: az Azure HPC gyorsítótár-erőforrásai automatikusan hoznak létre tevékenységi naplókat. Ezek a naplók az összes írási műveletet tartalmazzák (PUT, POST, DELETE), de nem tartalmazzák az olvasási műveleteket (GET). A Tevékenységnaplók segítségével hibát kereshet a hibaelhárítás során, vagy megfigyelheti, hogy a szervezet felhasználója hogyan módosított egy erőforrást.

Az Azure Security Center és Azure Policy használatával engedélyezheti az Azure-beli erőforrás-naplókat a HPC-gyorsítótárhoz, és naplózhatja az adatok gyűjtését. Ezek a naplók kritikus fontosságúak lehetnek a biztonsági incidensek kivizsgálásához és a kriminalisztikai gyakorlatok végrehajtásához.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [A naplózás és a különböző naplózási típusok megismerése az Azure-ban](../azure-monitor/platform/platform-logs-overview.md) 

- [Az Azure Security Center adatgyűjtés ismertetése](../security-center/security-center-enable-data-collection.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: a biztonsági naplók központosított kezelése és elemzése

**Útmutató**: a korreláció engedélyezéséhez központosítsa a naplózási tárolást és az elemzést. Minden egyes naplózási forrás esetében ellenőrizze, hogy rendelkezik-e az adatok tulajdonosával, a hozzáférési útmutatással, a tárolási hellyel, az adatok feldolgozásához és eléréséhez, valamint az adatmegőrzési követelményekhez használt eszközökhöz.

Győződjön meg arról, hogy integrálja az Azure-tevékenységek naplóit a központi naplózásba. Naplók betöltése Azure Monitor használatával a végponti eszközök, a hálózati erőforrások és más biztonsági rendszerek által generált biztonsági adatokat összesítve. Azure Monitor a Log Analytics-munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat hosszú távú és archiválási tároláshoz.

Emellett az Azure Sentinel vagy egy harmadik féltől származó SIEM-nek is engedélyezi az adatok bevezetését.

Számos szervezet úgy dönt, hogy a gyakran használt és az Azure-tárolót használja a ritkábban használt adatokhoz az Azure Sentinel használatával.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: Incidensmegoldás](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Előkészítés – incidensmegoldási folyamat frissítése az Azure-ban

**Útmutató**: Győződjön meg arról, hogy a szervezet rendelkezik a biztonsági incidensek megoldásához szükséges folyamatokkal, frissítette ezeket a folyamatokat az Azure-ral való használatra, valamint rendszeresen teszteli őket, hogy készen álljanak a használatra.

- [A biztonság implementálása a vállalati környezetben](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Incidensmegoldás – gyorsútmutató](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="ir-2-preparation--set-up-incident-notification"></a>IR-2: előkészítés – incidens-értesítés beállítása 

**Útmutató**: Kapcsolattartási adatokat állíthat be a biztonsági incidensekhez az Azure Security Centerben. A Microsoft arra használja ezeket a kapcsolattartási adatokat, hogy kapcsolatba lépjen Önnel, ha a Microsoft Security Response Center (MSRC) azt észleli, hogy egy jogosulatlan vagy illetéktelen fél hozzáfért az Ön adataihoz. Az incidensekkel kapcsolatos riasztások és értesítések testreszabhatók a különböző Azure-szolgáltatásokban az incidensmegoldási követelmények alapján. 

- [Az Azure Security Center biztonsági kapcsolattartójának beállítása](../security-center/security-center-provide-security-contact-details.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Észlelés és elemzés – incidensek létrehozása kiváló minőségű riasztások alapján

**Útmutató**: Ellenőrizze, hogy van-e olyan folyamat, amely kiváló minőségű riasztásokat hoz létre, és mérje fel a riasztások minőségét. Ezzel tanulhat a korábbi incidensekből, és megadhatja a riasztások fontossági sorrendjét az elemzők számára, akik így nem pazarolnak időt a vakriasztásokra. 

A kiváló minőségű riasztások a múltbeli incidensek, a hitelesített közösségi források és a riasztások létrehozásához és tisztításához tervezett eszközök alapján hozhatók létre a különböző jelforrások elutasításával és korrelációs megoldásával. 

Azure Security Center kiváló minőségű riasztásokat biztosít számos Azure-eszközön. Az ASC adatösszekötővel streamelheti a riasztásokat az Azure Sentinelbe. Az Azure Sentinellel speciális riasztási szabályokat hozhat létre, amelyekkel automatikusan hozhat létre incidenseket a vizsgálatokhoz. 

Az exportálási funkcióval exportálhatja az Azure Security Center riasztásait és javaslatait, amelyek segítenek az Azure-erőforrások kockázatainak azonosításában. A riasztásokat és javaslatokat manuálisan vagy folyamatosan is exportálhatja.

- [Az exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Észlelés és elemzés – incidens vizsgálata

**Útmutató**: Győződjön meg arról, hogy az elemzők különböző adatforrásokat kérdezhetnek le és használhatnak a lehetséges incidensek vizsgálata során, és átfogó képet adhatnak a történtekről. A vakfoltok elkerülése érdekében különböző naplókat kell gyűjteni a potenciális támadók tevékenységének követéséhez a támadási útvonalon.  A megállapítások és eredmények rögzítéséről is érdemes gondoskodnia, hogy később más elemzők referenciaként használhassák őket.  

A vizsgálathoz használt adatforrások közé tartoznak azok a központi naplózási források, amelyeket a rendszer már gyűjt a hatókörbeli szolgáltatásokból és a futó rendszerekről, de a következők is lehetnek:

- Hálózati adatok – az Azure Network Watcher, az Azure Monitor és a hálózati biztonsági csoportok forgalmi naplóinak használata a hálózati forgalom naplóinak és más elemzési adatok rögzítéséhez. 

- A futó rendszerek pillanatképei: 

    - Az Azure-beli virtuális gép pillanatkép-készítési funkciójával pillanatképet készíthet a futó rendszer lemezéről. 

    - Az operációs rendszer natív memóriakép-készítési funkciójával pillanatképet készíthet a futó rendszer memóriájáról.

    - Az Azure-szolgáltatások vagy saját szoftvere pillanatkép-készítési funkciójával pillanatképeket készíthet a futó rendszerekről.

Az Azure Sentinel széles körű adatelemzést tesz lehetővé szinte bármely naplózási forráson és egy esetfelügyeleti portálon az incidensek teljes életciklusának kezeléséhez. A vizsgálatok során kapott információk társíthatók egy incidenssel nyomkövetés és jelentéskészítés céljából. 

- [Windows rendszerű gép lemezének pillanatképe](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux rendszerű gép lemezének pillanatképe](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure-támogatás – diagnosztikai információk és memóriaképek gyűjtése](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Incidensek vizsgálata az Azure Sentinellel](../sentinel/tutorial-investigate-cases.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Észlelés és elemzés – incidensek rangsorolása

**Útmutató**: Megadhat arra vonatkozó információkat az elemzők számára, hogy melyik incidensekre kell először összpontosítaniuk a súlyosság és az objektum bizalmassága alapján. 

Az Azure Security Center súlyossági szintet rendel az egyes riasztásokhoz, és segít rangsorolni, hogy melyik riasztásokat kell először kivizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben van megkeresve vagy a riasztás kibocsátására szolgáló analitikusan, valamint a riasztást eredményező tevékenység mögötti olyan megbízhatósági szinten, amely rosszindulatú szándékkal rendelkezik.

Emellett a címkék használatával megjelölheti az erőforrásokat, és létrehozhat egy elnevezési rendszert az Azure-erőforrások, különösen a bizalmas adatok feldolgozását végző erőforrások azonosításához és kategorizálásához.  Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](/azure/azure-resource-manager/resource-group-using-tags)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Elkülönítés, megsemmisítés és helyreállítás – incidensek kezelésének automatizálása

**Útmutató**: Automatizálhatja a manuális, ismétlődő feladatokat a válaszidő lerövidítéséhez és az elemzők terheinek csökkentéséhez. A manuális feladatok végrehajtása hosszabb időt vesz igénybe, lassítja az egyes incidensek kezelését, és csökkenti az egyetlen elemző által kezelhető incidensek számát. A manuális feladatok ki is merítik az elemzőket, ami növeli az emberi hiba lehetőségét, és az elemzők kevésbé tudnak hatékonyan összpontosítani az összetett feladatokra. Az Azure Security Center és az Azure Sentinel munkafolyamat-automatizálási funkcióival automatikusan indíthat el műveleteket és futtathat forgatókönyveket, hogy reagáljon a bejövő biztonsági riasztásokra. A forgatókönyv műveleteket hajt végre, például értesítéseket küld, fiókokat tilt le, és elkülöníti a problémás hálózatokat. 

- [Munkafolyamat-automatizálás konfigurálása a Security Centerben](../security-center/workflow-automation.md)

- [Fenyegetésre adott automatikus válaszok beállítása az Azure Security Centerben](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Fenyegetésre adott automatikus válaszok beállítása az Azure Sentinelben](../sentinel/tutorial-respond-threats-playbook.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="posture-and-vulnerability-management"></a>Állapot- és biztonságirés-kezelés

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: Állapot- és biztonságirés-kezelés](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: biztonságos konfigurációk létrehozása számítási erőforrásokhoz

**Útmutató**: a Azure Security Center és Azure Policy használatával biztonságos konfigurációkat hozhat létre minden számítási erőforráson, beleértve a virtuális gépeket, a tárolókat és másokat is.

- [Azure Security Center javaslatok figyelése](../security-center/security-center-recommendations.md) 

- [Biztonsági javaslatok – gyorsútmutató](../security-center/recommendations-reference.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Támadások rendszeres szimulálása

**Útmutató**: Szükség szerint behatolási teszteket vagy támadásszimulációs tevékenységeket hajthat végre az Azure-erőforrásokon, és gondoskodhat az észlelt kritikus biztonsági rések javításáról.
Kövesse a Microsoft Cloud behatolástesztelési szabályait, így biztosíthatja, hogy a behatolási tesztek nem sértik meg a Microsoft-szabályzatokat. Használja a Microsoft stratégiáját, és hajtson végre támadásszimulációt, valamint végezze el a Microsoft által felügyelt felhő-infrastruktúra, szolgáltatások és alkalmazások éles webhelyen történő behatolástesztelését.

- [Behatolástesztelés az Azure-ban](../security/fundamentals/pen-testing.md)

- [Behatolástesztelési szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud – támadásszimuláció](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Megosztott

## <a name="backup-and-recovery"></a>Biztonsági másolat és helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: biztonsági mentés és helyreállítás](/azure/security/benchmarks/security-controls-v2-backup-recovery)című témakörben talál.*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: rendszeres automatikus biztonsági mentések biztosítása

**Útmutató**: mivel az Azure HPC cache egy gyorsítótárazási megoldás, nem pedig tárolási rendszer, a tárolási célokban tárolt adatbiztonsági mentésének biztosítására koncentráljon. Kövesse az Azure Blob-tárolók standard eljárásait, valamint a helyszíni tárolási célok biztonsági mentését. 

A regionális kimaradások esetén a fennakadások csökkentése érdekében lépéseket tehet a régiók közötti adatelérés biztosításához.  

Minden egyes Azure HPC cache-példány egy adott előfizetésen belül és egy régióban fut. Ez azt jelenti, hogy a gyorsítótár munkafolyamata valószínűleg megszakadhat, ha a régió teljes leállás miatt leáll. A megszakítás csökkentése érdekében a szervezetnek olyan háttér-tárterületet kell használnia, amely több régióból is elérhető. Ez a tároló lehet a megfelelő DNS-támogatással rendelkező helyszíni NAS-rendszer, vagy az Azure Blob Storage, amely egy másik régióban található a gyorsítótárból.

Ahogy a munkafolyamat az elsődleges régióban folytatja, az adatait a régión kívüli hosszú távú tárolóba menti a rendszer. Ha a gyorsítótár-régió elérhetetlenné válik, létrehozhat egy duplikált Azure HPC cache-példányt egy másodlagos régióban, csatlakozhat ugyanahhoz a tárolóhoz, és folytathatja a munkát az új gyorsítótárból.

- [További információ a regionális feladatátvételről](hpc-region-recovery.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="br-2-encrypt-backup-data"></a>BR-2: biztonsági mentési adataik titkosítása

**Útmutató**: gondoskodjon róla, hogy a biztonsági másolatok védve legyenek a támadásokkal szemben. Ennek tartalmaznia kell a biztonsági másolatok titkosítását a titkosság elvesztése elleni védelem érdekében.

A helyszíni biztonsági mentéshez Azure Backup használatával a rendszer az Ön által megadott jelszót használja. Az Azure-szolgáltatások rendszeres biztonsági mentéséhez a biztonsági mentési adatai automatikusan titkosítva vannak az Azure platform által felügyelt kulcsok használatával. A biztonsági mentést az ügyfél által felügyelt kulcsok használatával is titkosíthatja. Ebben az esetben ügyeljen arra, hogy az ügyfél által felügyelt kulcs a Key vaultban is a biztonsági mentési hatókörben legyen.

Az Azure HPC cache-t a virtuális gépek gazdagépének titkosítása is védi a gyorsítótárazott adatait tároló felügyelt lemezeken, még akkor is, ha felvesznek egy ügyfél-kulcsot a gyorsítótár-lemezekhez. Az ügyfél által felügyelt kulcs dupla titkosításhoz való hozzáadásával további biztonsági szint biztosítható a magas biztonsági igényeket kielégítő ügyfelek számára. A részletekért olvassa el az Azure Disk Storage kiszolgálóoldali titkosítását ismertető oldalt.

A biztonsági másolatok és az ügyfél által felügyelt kulcsok kezeléséhez használja a Azure Backup, Azure Key Vault vagy más erőforrások szerepköralapú hozzáférés-vezérlését. Emellett speciális biztonsági funkciók is engedélyezhetők az MFA megköveteléséhez, mielőtt a biztonsági másolatok módosíthatók vagy törölhetők.

- [VM-gazdagép titkosítása](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)

- [Az Azure Disk Storage kiszolgálóoldali titkosítása](../virtual-machines/disk-encryption.md)

- [A Azure Backup biztonsági funkcióinak áttekintése](../backup/security-overview.md) 

- [Biztonsági mentési adatai titkosítása az ügyfél által felügyelt kulcsokkal](../backup/encryption-at-rest-with-cmk.md)  

- [Key Vault kulcsok biztonsági mentése az Azure-ban](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.1.0&amp;preserve-view=true)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: az összes biztonsági mentés ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: az ügyfél által felügyelt kulcsok biztonsági mentésének rendszeres ellenőrzése.

- [Key Vault kulcsok visszaállítása az Azure-ban](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-5.1.0&amp;preserve-view=true)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: az elveszett kulcsok kockázatának enyhítése

**Útmutató**: Ellenőrizze, hogy vannak-e olyan intézkedések, amelyek megakadályozzák a kulcsok elvesztését és helyreállítását. A kulcsok véletlen vagy rosszindulatú törléssel szembeni védelme érdekében engedélyezze a Azure Key Vault a védelem lágy törlését és kiürítését.

- [A védelem lágy törlésének és kiürítésének engedélyezése a Key Vaultban](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="governance-and-strategy"></a>Irányítás és stratégia

*További információért lásd: [Az Azure biztonsági teljesítménytesztje: Irányítás és stratégia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Objektumkezelési és adatvédelmi stratégia meghatározása 

**Útmutató**: Győződjön meg arról, hogy egyértelmű stratégiát dokumentál és kommunikál a rendszerek és adatok folyamatos monitorozásához és védelméhez. Priorizálja az üzletileg kritikus adatok és rendszerek felderítését, értékelését, védelmét és monitorozását. 

Ennek a stratégiának tartalmaznia kell az alábbi elemek dokumentált útmutatóit, szabályzatait és szabványait: 

-   Adatbesorolási szabvány az üzleti kockázatokkal összhangban

-   A biztonsági szervezet betekintése a kockázatokba és az objektumleltárba 

-   A biztonsági szervezet jóváhagyása a használandó Azure-szolgáltatásokhoz 

-   Az objektumok biztonsága életciklusuk során

-   A szükséges hozzáférés-vezérlési stratégia a szervezeti adatbesorolásnak megfelelően

-   Az Azure Native és a harmadik féltől származó adatvédelmi képességek használata

-   Adattitkosítási követelmények a használatban lévő és a jelenleg nem használt használati esetekhez

-   Megfelelő titkosítási szabványok

További információkért tekintse meg az alábbi hivatkozásokat:
- [Az Azure biztonsági architektúrájára vonatkozó javaslatok – Tárolás, adatok és titkosítás](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Az Azure biztonsági alapjai – Az Azure-beli adatok biztonsága, titkosítása és tárolása](../security/fundamentals/encryption-overview.md)

- [Felhőbevezetési keretrendszer – Az Azure adatbiztonsággal és titkosítással kapcsolatos ajánlott eljárásai](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure-biztonsági teljesítményteszt – Objektumkezelés](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure-biztonsági teljesítményteszt – Adatvédelem](/azure/security/benchmarks/security-controls-v2-data-protection)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Vállalati szegmentálási stratégia meghatározása 

**Útmutató**: Hozzon létre egy vállalati szintű stratégiát az objektumokhoz való hozzáférés szegmentálásához identitás, hálózat, alkalmazás, előfizetés, felügyeleti csoport és egyéb vezérlők kombinációjának használatával.

Ügyeljen az egyensúlyra a biztonsági elkülönítés és az egymással kommunikáló, az adatok elérését biztosító rendszerek napi működése között.

Győződjön meg arról, hogy a szegmentálási stratégia következetesen van implementálva a különböző típusú vezérlőkben, beleértve a hálózati biztonságot, az identitás- és hozzáférési modelleket, az alkalmazásengedély- és hozzáférési modelleket, valamint az emberi folyamatvezérlést.

- [Útmutató a szegmentálási stratégiához az Azure-ban (videó)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Útmutató a szegmentálási stratégiához az Azure-ban (dokumentum)](/security/compass/governance#enterprise-segmentation-strategy)

- [A hálózati szegmentálás igazítása a vállalati szegmentálási stratégiához](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Biztonsági helyzet kezelési stratégiájának meghatározása

**Útmutató**: Folyamatosan mérje fel és mérsékelje az egyes objektumok és az ezeket tartalmazó környezetek kockázatait. Priorizálja a nagy értékű objektumokat és a támadásoknak nagy mértékben kitett felületeket, például a közzétett alkalmazásokat, a hálózat bemeneti és kimeneti pontjait és a felhasználói és rendszergazdai végpontokat.

- [Azure-biztonsági teljesítményteszt – Állapot- és biztonságirés-kezelés](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Szervezeti szerepkörök, felelősségek és elszámoltathatóság összehangolása

**Útmutató**: Győződjön meg arról, hogy egyértelmű stratégiát dokumentál és kommunikál a biztonsági szervezet szerepköreivel és felelősségeivel kapcsolatban. Biztosítson egyértelmű elszámoltathatóságot a biztonsági döntésekhez, tájékoztasson mindenkit a megosztott felelősségi modellről, és tájékoztassa a műszaki csapatokat a technológiáról a felhő védelme érdekében.

- [Az Azure ajánlott biztonsági eljárásai 1 – Személyek: A csapatok tájékoztatása a felhőbiztonság felé vezető útról](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Az Azure ajánlott biztonsági eljárásai 2 – Személyek: A csapatok tájékoztatása a felhőbiztonsági technológiáról](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Az Azure ajánlott biztonsági eljárásai 3 – Folyamat: A felhőbiztonsági döntésekért való elszámoltathatóság biztosítása](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Hálózati biztonsági stratégia meghatározása

**Útmutató**: Hozzon létre egy Azure-beli hálózati biztonsági megközelítést a szervezet általános biztonsági hozzáférés-vezérlési stratégiájának részeként.  

Ennek a stratégiának tartalmaznia kell az alábbi elemek dokumentált útmutatóit, szabályzatait és szabványait: 

-   Központosított hálózatkezelés és biztonsági felelősség

-   A vállalat szegmentálási stratégiájához igazított virtuálishálózat-szegmentálási modell

-   Javítási stratégia a különböző fenyegetési és támadási forgatókönyvekben

-   Internetes peremhálózatra, valamint bejövő és kimenő adatforgalomra vonatkozó stratégia

-   A felhőbeli és a helyszíni infrastruktúra összekapcsolásának hibrid stratégiája

-   Naprakész hálózati biztonsági összetevők (például hálózati diagramok, hálózati referenciaarchitektúra)

További információkért tekintse meg az alábbi hivatkozásokat:
- [Az Azure ajánlott biztonsági eljárásai 11 – Architektúra. Egyetlen, egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure-biztonsági teljesítményteszt – Hálózati biztonság](/azure/security/benchmarks/security-controls-v2-network-security)

- [Az Azure-hálózat biztonsági áttekintése](../security/fundamentals/network-overview.md)

- [Vállalati hálózati architektúra stratégiája](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Identitás- és emelt szintű hozzáférési stratégia meghatározása

**Útmutató**: Hozzon létre egy Azure-beli identitás- és emelt szintű hozzáférési megközelítést a szervezete általános biztonság hozzáférés-vezérlési stratégiájának részeként.  

Ennek a stratégiának tartalmaznia kell az alábbi elemek dokumentált útmutatóit, szabályzatait és szabványait: 

-   A központosított identitás- és hitelesítési rendszer, valamint a rendszer más belső és külső rendszerekkel való összekapcsolhatósága

-   Erős hitelesítési módszerek a különböző használati esetekben és különböző feltételek esetén

-   Magas szintű jogosultságokkal rendelkező felhasználók védelme

-   Rendellenes felhasználói tevékenységek monitorozása és kezelése  

-   Felhasználói identitás és hozzáférés ellenőrzési és egyeztetési folyamata

További információkért tekintse meg az alábbi hivatkozásokat:

- [Azure-biztonsági teljesítményteszt – Identitáskezelés](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure-biztonsági teljesítményteszt – Emelt szintű hozzáférés](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Az Azure ajánlott biztonsági eljárásai 11 – Architektúra. Egyetlen, egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Az Azure-identitáskezelés biztonsági áttekintése](../security/fundamentals/identity-management-overview.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Naplózási stratégia és a fenyegetésekre adott válaszok stratégiájának meghatározása

**Útmutató**: Hozzon létre egy stratégiát a naplózáshoz és a fenyegetésekre adott válaszokhoz, amelyekkel gyorsan észlelheti és elháríthatja a fenyegetéseket, a megfelelőségi követelmények betartása mellett. Kiváló minőségű riasztásokkal és zökkenőmentes tapasztalatokkal látja el az elemzőket, hogy az integráció és a manuális lépések helyett a fenyegetésekre összpontosítsanak. 

Ennek a stratégiának tartalmaznia kell az alábbi elemek dokumentált útmutatóit, szabályzatait és szabványait: 

-   A biztonsági műveletekért (SecOps) felelős szervezet szerepköre és felelősségei 

-   Az incidensre adott, jól definiált válaszfolyamat, amely igazodik az NIST-hez és más iparági keretrendszerekhez 

-   Naplók rögzítése és megőrzése a fenyegetésészlelés, az incidensmegoldás és a megfelelőségi célkitűzések támogatásához

-   A fenyegetések SIEM, natív Azure-képességek és más erőforrások használatával biztosított, központosított láthatósága, illetve korrelációs adatai 

-   Az ügyfelek, a beszállítók és az érdekelt közszektorbeli felekkel folytatott értesítésének, valamint a velük folytatott kommunikáció terve

-   Natív Azure-beli és külső platformok használata az incidensek kezeléséhez, például a naplózáshoz és a fenyegetésészleléshez, a vizsgálathoz, és a támadások elhárításához és megsemmisítéséhez

-   Az incidenseket és az incidensek utáni tevékenységeket, például a levont következtetéseket és a bizonyítékmegőrzést kezelő folyamatok

További információkért tekintse meg az alábbi hivatkozásokat:

- [Azure-biztonsági teljesítményteszt – Naplózás és fenyegetésészlelés](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure-biztonsági teljesítményteszt – Incidensmegoldás](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Az Azure ajánlott biztonsági eljárásai 4 – Folyamat. A felhőre vonatkozó incidensmegoldási folyamatok frissítése](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Útmutató az Azure-bevezetési keretrendszerrel, a naplózással és a jelentéskészítéssel kapcsolatos döntésekhez](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Az Azure vállalati skálázása, kezelése és monitorozása](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="next-steps"></a>Következő lépések

- Lásd: [Az Azure biztonsági teljesítményteszt 2-es verziójának áttekintése](/azure/security/benchmarks/overview)
- További tudnivalók az [Azure biztonsági alapkonfigurációiról](/azure/security/benchmarks/security-baselines-overview)
