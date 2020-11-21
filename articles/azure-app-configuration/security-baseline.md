---
title: Azure-beli biztonsági alaptervek az Azure-alkalmazások konfigurálásához
description: Az Azure app Configuration biztonsági alapkonfigurációja az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4d74c3607610372fa6cd276dd0d72fa5ba37f391
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025805"
---
# <a name="azure-security-baseline-for-azure-app-configuration"></a>Azure-beli biztonsági alaptervek az Azure-alkalmazások konfigurálásához

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 2,0-es verziójáról](../security/benchmarks/overview.md) az Azure-alkalmazások konfigurálására vonatkozó útmutatást alkalmazza. Az Azure biztonsági teljesítményteszt az Azure-beli felhőalapú megoldások biztonságossá tételével kapcsolatos ajánlásokat tartalmaz. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint az Azure-alkalmazás konfigurálására vonatkozó kapcsolódó útmutatás. Az Azure-alkalmazás konfigurációjához nem alkalmazható **vezérlők** ki lettek zárva.

Az Azure-alkalmazások konfigurációjának teljes leképezése az Azure-os biztonsági Teljesítménytesztre című cikkből megtudhatja, hogy az Azure-alkalmazás [konfigurációjának teljes biztonsági alapmegfeleltetési fájlja](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információt az [Azure biztonsági teljesítményteszt: hálózati biztonság](/azure/security/benchmarks/security-controls-v2-network-security)című témakörben talál.*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: a belső forgalom biztonságának megvalósítása

**Útmutató**: az Azure-alkalmazás konfigurációja nem helyez üzembe semmilyen erőforrást közvetlenül egy virtuális hálózatban. Mivel a szolgáltatás nem virtuális hálózatra van telepítve, bizonyos hálózati szolgáltatások nem használhatók a szolgáltatás belső forgalmának biztonságossá tételéhez, például: hálózati biztonsági csoportok, útválasztási táblák vagy más hálózati berendezések, például egy Azure Firewall. Az alkalmazás konfigurációja azonban lehetővé teszi privát végpontok használatát az Azure-alkalmazások konfigurációjának biztonságos csatlakoztatásához egy virtuális hálózatból.

Az Azure Sentinel használatával felderítheti az örökölt nem biztonságos protokollok, például az SSL/TLSv1, a SMBv1, az LM/NTLMv1, a wDigest, az aláíratlan LDAP-kötések és a Kerberos-alapú gyenge titkosítás használatát.

- [Privát végpontok használata az Azure app Configuration szolgáltatáshoz](concept-private-endpoint.md)

- [Azure Sentinel – nem biztonságos protokollok – munkafüzet](../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

### <a name="ns-2-connect-private-networks-together"></a>NS-2: magánhálózati hálózatok összekapcsolása

**Útmutató**: az Azure-alkalmazás konfigurációja támogatja a privát végpontok használatát az adatküldés privát kapcsolaton keresztüli biztonságos elküldéséhez. Az Azure ExpressRoute vagy az Azure virtual private Network (VPN) használatával privát kapcsolatokat hozhat létre az Azure-adatközpontok és a helyszíni infrastruktúra között egy közös elhelyezésű környezetben. A ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át, így megbízhatóbbak, gyorsabbak és kevesebb késéssel rendelkeznek, mint a szokásos internetes kapcsolatok. Pont – hely típusú VPN-és helyek közötti VPN esetén a VPN-beállítások és az Azure ExpressRoute bármely kombinációja segítségével a helyszíni eszközöket vagy hálózatokat egy virtuális hálózathoz is összekapcsolhatja.

Ha két vagy több virtuális hálózatot szeretne összekapcsolni az Azure-ban, használja a virtuális hálózati társítást. A kiszolgált virtuális hálózatok közötti hálózati forgalom magán, és az Azure-beli gerinces hálózaton marad.

- [A ExpressRoute kapcsolati modelljei](../expressroute/expressroute-connectivity-models.md)

- [Az Azure VPN áttekintése](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Virtuális hálózati társviszony](../virtual-network/virtual-network-peering-overview.md)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: magánhálózati hozzáférés létrehozása az Azure-szolgáltatásokhoz

**Útmutató**: az Azure privát hivatkozásának használatával engedélyezheti az Azure-alkalmazások konfigurációját a virtuális hálózatokról anélkül, hogy az interneten keresztül kellene eljutnia.

A privát hozzáférés az Azure-szolgáltatások által kínált hitelesítéssel és adatforgalommal kapcsolatos további védelmi részletes mérték.

- [Az Azure privát hivatkozásának megismerése](../private-link/private-link-overview.md)

- [Privát hivatkozás beállítása az Azure-alkalmazások konfigurációjában](concept-private-endpoint.md)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: a külső hálózati támadásoktól származó alkalmazások és szolgáltatások elleni védelem

**Útmutatás**: Ha virtuális hálózaton keresztül éri el a konfigurációs értékeket, az erőforrásokat védeni kell a külső hálózatoktól érkező támadások ellen, beleértve az elosztott szolgáltatásmegtagadási (DDoS) támadásokat, az alkalmazásokra vonatkozó támadásokat, valamint a kéretlen és potenciálisan kártékony internetes forgalmat. A Azure Firewall használatával védelmet biztosíthat az alkalmazások és szolgáltatások számára az internetről és más külső helyekről származó, potenciálisan rosszindulatú forgalom ellen. Az adatvédelmet a DDoS-támadások ellen biztosíthatja az Azure-beli virtuális hálózatokon a DDoS standard szintű védelem engedélyezésével. A Azure Security Center használatával azonosíthatja a hálózattal kapcsolatos erőforrásokkal kapcsolatos hibás konfigurációs kockázatokat.

Az Azure-alkalmazás konfigurációja nem a webalkalmazások futtatására készült, hanem a webalkalmazások konfigurálását is lehetővé teszi. Semmilyen további beállítást nem kell konfigurálnia, vagy további hálózati szolgáltatásokat kell telepítenie a webalkalmazásokra irányuló külső hálózati támadások elleni védelemhez.

- [Azure Firewall dokumentáció](/azure/firewall/)

- [Azure DDoS Protection standard kezelése a Azure Portal használatával](/azure/virtual-network/manage-ddos-protection)

- [Az Azure Security Centerre vonatkozó ajánlások](../security-center/recommendations-reference.md#recs-network)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: a Azure Firewall és az ismert kártékony IP-címekre és tartományokra vonatkozó riasztások és/vagy azok közötti adatforgalom blokkolása veszélyforrások elleni szűréssel. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája. Ha szükség van a hasznos adatok vizsgálatára, az Azure Marketplace-en üzembe helyezhet egy harmadik féltől származó azonosító/IP-megoldást az adattartalom-ellenőrzési képességekkel. Másik lehetőségként dönthet úgy, hogy gazdagép-alapú AZONOSÍTÓkat/IP-címeket vagy gazdagép-alapú végpont-észlelési és-gyorsreagáló (EDR) megoldást használ a hálózat-alapú AZONOSÍTÓk/IP-címek helyett vagy ahelyett.

Megjegyzés: Ha az AZONOSÍTÓk/IP-címek használatára vonatkozó előírás vagy egyéb követelmény van, győződjön meg arról, hogy mindig úgy van hangolva, hogy magas színvonalú riasztásokat nyújtson az SIEM-megoldáshoz.

- [Azure Firewall üzembe helyezése](/azure/firewall/tutorial-firewall-deploy-portal)

- [Az Azure Marketplace harmadik féltől származó azonosító képességeket is tartalmaz](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Microsoft Defender ATP EDR-képesség](/windows/security/threat-protection/microsoft-defender-atp/overviewendpoint-detection-response)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: a hálózati biztonsági szabályok egyszerűsítése

**Útmutató**: az Azure Virtual Network szolgáltatás-címkék használatával meghatározhatja a hálózati biztonsági csoportokhoz vagy az alkalmazás-konfigurációs erőforrásokhoz konfigurált Azure Firewallhoz tartozó hálózati hozzáférés-vezérlést. A "AppConfiguration" szolgáltatási címkét adott IP-címek helyett használhatja a kimenő forgalomra vonatkozó biztonsági szabályok létrehozásakor az alkalmazás hálózatában. Ha a szolgáltatási címke nevét egy szabály megfelelő forrás vagy cél mezőjében adja meg, akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

- [A szolgáltatási címkék megismerése és használata](../virtual-network/service-tags-overview.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="identity-management"></a>Identitáskezelés

*További információt az [Azure biztonsági teljesítményteszt: Identitáskezelés](/azure/security/benchmarks/security-controls-v2-identity-management)című témakörben talál.*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: szabványosított Azure Active Directory központi identitás-és hitelesítési rendszerrel

**Útmutató**: az Azure-alkalmazás konfigurációja integrálva van az Azure alapértelmezett identitás-és hozzáférés-kezelési szolgáltatásának Azure Active Directory (Azure ad) szolgáltatásával. Egységesítse az Azure AD-t a szervezet identitás-és hozzáférés-kezelésének szabályozásához a ben:
- Microsoft Cloud erőforrások, például a Azure Portal, az Azure Storage, az Azure virtuális gép (Linux és Windows), a Azure Key Vault, a Pásti és az SaaS-alkalmazások.
- A szervezet erőforrásai, például az Azure-beli alkalmazások vagy a vállalati hálózati erőforrások.

Az Azure AD biztonságossá tételének magas prioritásnak kell lennie a szervezet felhőalapú biztonsági gyakorlatában. Az Azure AD egy biztonságos azonosítási pontszámot biztosít, amellyel a Microsoft ajánlott eljárási javaslataihoz képest kiértékelheti a személyazonosság biztonsági helyzetét. A pontszám használatával mérje fel, hogy a konfiguráció milyen mértékben felel meg az ajánlott eljárásokkal kapcsolatos javaslatoknak, és javítsa a biztonsági helyzetét.

Az Azure az Azure AD és a OAuth használatával engedélyezi az alkalmazások konfigurációs adataihoz való hozzáférés engedélyezését az alábbi Azure beépített szerepkörökkel:

- Alkalmazás-konfigurációs adat tulajdonosa: ezt a szerepkört használva olvasási/írási/törlési hozzáférést biztosíthat az alkalmazás konfigurációs adatához. Ez nem biztosít hozzáférést az alkalmazás konfigurációs erőforrásához.

- Alkalmazás-konfigurációs Adatolvasó: ezt a szerepkört használva olvasási hozzáférést adhat az alkalmazás konfigurációs adatához. Ez nem biztosít hozzáférést az alkalmazás konfigurációs erőforrásához.

- Közreműködő: használja ezt a szerepkört az alkalmazás konfigurációs erőforrásának kezeléséhez. Az alkalmazás konfigurációs adatai hozzáférési kulcsok használatával érhetők el, ez a szerepkör nem biztosít közvetlen hozzáférést az Azure AD-vel az adathoz.

- Olvasó: ezt a szerepkört használva olvasási hozzáférést adhat az alkalmazás konfigurációs erőforrásához. Ez nem biztosít hozzáférést az erőforrás elérési kulcsaihoz, és nem az alkalmazás konfigurációjában tárolt adathoz.

További információkért tekintse meg a következő referenciákat:

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Mi az identitások biztonságos pontszáma Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

- [Hozzáférés engedélyezése az Azure app Configuration szolgáltatáshoz az Azure AD használatával](concept-enable-rbac.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: az alkalmazás-identitások biztonságos és automatikus kezelése

**Útmutató**: az Azure által felügyelt identitások használata az Azure-alkalmazások konfigurálásához nem emberi fiókokból, például más Azure-szolgáltatásokból. Azt javasoljuk, hogy az Azure Managed Identity szolgáltatást használja ahelyett, hogy hatékonyabb emberi fiókot hozzon létre az erőforrások eléréséhez vagy végrehajtásához, hogy korlátozza a további hitelesítő adatok kezelésének szükségességét. Az Azure-alkalmazások konfigurálása olyan felügyelt identitáshoz is hozzárendelhető, amely natív módon hitelesíthető az Azure AD-hitelesítést támogató egyéb Azure-szolgáltatásokkal/erőforrásokkal. Ez akkor lehet hasznos, ha lehetővé szeretné tenni az alkalmazások konfigurációjának egyszerű elérését Azure Key Vault a titkok beolvasása során. A felügyelt identitások használatakor az identitást az Azure platform felügyeli, és nem igényli semmilyen titok kiépítését és elforgatását.

Az Azure-alkalmazás konfigurációja támogatja, hogy az alkalmazás két típusú identitást biztosítson:
- A rendszer által hozzárendelt identitás a konfigurációs erőforráshoz van kötve. A konfigurációs erőforrás törlése után törlődik. Egy konfigurációs erőforrásnak csak egy rendszerhez rendelt identitása lehet.
- A felhasználó által hozzárendelt identitás egy önálló Azure-erőforrás, amelyet a konfigurációs erőforráshoz rendelhet hozzá. A konfigurációs erőforrásokhoz több felhasználó által hozzárendelt identitás is tartozhat.

Ha a felügyelt identitásokat nem lehet kihasználni, hozzon létre egy egyszerű szolgáltatásnevet az Azure-alkalmazás konfigurációs erőforrás szintjén, amely korlátozott engedélyekkel rendelkezik. Konfigurálja ezeket a egyszerű szolgáltatásokat a tanúsítvány hitelesítő adataival, és csak az ügyfél titkos kulcsaira térjen vissza. Mindkét esetben Azure Key Vault használható az Azure felügyelt identitásokkal együtt, így a futásidejű környezet (például egy Azure-függvény) beolvashatja a hitelesítő adatokat a kulcstartóból.

- [Felügyelt identitások használata az Azure-alkalmazások konfigurálásához](overview-managed-identity.md)

- [Azure-beli felügyelt identitások](../active-directory/managed-identities-azure-resources/overview.md)

- [Az Azure-erőforrások felügyelt identitásait támogató szolgáltatások](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Felügyelt identitások használata az App Configuration eléréséhez](howto-integrate-azure-managed-service-identity.md)

- [Azure-szolgáltatásnév](/powershell/azure/create-azure-service-principal-azureps) 

- [Egyszerű szolgáltatás létrehozása tanúsítványokkal](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Rendszerbiztonsági tag regisztrációjának Azure Key Vault használata](../key-vault/general/authentication.md#app-identity-and-security-principals)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: az Azure AD egyszeri bejelentkezés (SSO) használata az alkalmazásokhoz való hozzáféréshez

**Útmutató**: az Azure App Configuration Azure Active Directory (Azure ad) használatával biztosítja az identitás-és hozzáférés-kezelést az Azure-erőforrásokhoz, a felhőalapú alkalmazásokhoz és a helyszíni alkalmazásokhoz. Ide tartoznak a vállalati identitások, például az alkalmazottak, valamint a külső identitások, például a partnerek, a szállítók és a szállítók. Az Azure AD lehetővé teszi az egyszeri bejelentkezést (SSO) az alkalmazás konfigurációs szolgáltatásának a Azure Portalon keresztüli kezeléséhez a szinkronizált vállalati Active Directory-identitások használatával. Az összes felhasználó, alkalmazás és eszköz csatlakoztatása az Azure AD-hez zökkenőmentes, biztonságos hozzáférés és jobb láthatóság és vezérlés érdekében.

- [Alkalmazás egyszeri bejelentkezésének megismerése az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: erős hitelesítési vezérlők használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: az Azure app Configuration olyan Azure Active Directory használ, amely támogatja a többtényezős hitelesítés (MFA) és az erős, jelszóval nem használható metódusok erős hitelesítési vezérlését.
- Többtényezős hitelesítés – engedélyezheti az Azure AD MFA-t, és követheti Azure Security Center identitás-és hozzáférés-kezelési javaslatait az MFA-telepítéssel kapcsolatos ajánlott eljárásokhoz. Az MFA kikényszeríthető az összes, a felhasználók vagy a felhasználónkénti szinten a bejelentkezési feltételek és a kockázati tényezők alapján.
- Jelszóval nem rendelkező hitelesítés – három jelszóval nem rendelkező hitelesítési lehetőség érhető el: a vállalati Windows Hello, Microsoft Authenticator alkalmazás és a helyszíni hitelesítési módszerek, például az intelligens kártyák.

A rendszergazda és a Kiemelt jogosultságú felhasználók számára ellenőrizze, hogy a legmagasabb szintű erős hitelesítési módszert használja-e, majd ezt követően a megfelelő erős hitelesítési házirendet a többi felhasználó számára.

Megjegyzés: az MFA kikényszeríthető az alkalmazások konfigurációját elérő és kezelő felhasználói fiókokon, a programozott szolgáltatásfiókok esetében nem. Használjon jelszó nélküli hitelesítést, például a felügyelt identitásokat, ahol lehetséges, és minden felhasználói fiókon érvényesítse az MFA-t.

- [Az MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Bevezetés a Azure Active Directory jelszavas hitelesítési lehetőségeibe](../active-directory/authentication/concept-authentication-passwordless.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: a fiókok rendellenességeit figyelő és riasztások

**Útmutató**: az Azure-alkalmazás konfigurációja integrálva van Azure Active Directory, amely a következő adatforrásokat biztosítja:

-   Bejelentkezések – a bejelentkezési jelentés információt nyújt a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról.

-   Naplók – az Azure AD különböző funkciói által végrehajtott módosítások naplókban való nyomon követését teszi lehetővé. Naplózott változások naplózása például felhasználók, alkalmazások, csoportok, szerepkörök és házirendek hozzáadásával vagy eltávolításával kapcsolatos naplók.

-   Kockázatos bejelentkezések – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa.

-   Kockázatosként megjelölt felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

Ezek az adatforrások a Azure Monitor, az Azure Sentinel vagy a harmadik féltől származó SIEM rendszerekkel integrálhatók.

Azure Security Center riasztást is beállíthat bizonyos gyanús tevékenységekről, például a sikertelen hitelesítési kísérletekről, valamint az előfizetésben elavult fiókokról. 

Az Azure komplex veszélyforrások elleni védelem (ATP) olyan biztonsági megoldás, amely helyszíni Active Directory jeleket használ a fejlett fenyegetések, a feltört identitások és a rosszindulatú bennfentes műveletek azonosítására, észlelésére és kivizsgálására.

- [Tevékenység-jelentések naplózása az Azure AD-ben](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Az Azure AD kockázatos bejelentkezések megtekintése](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [A kockázatos tevékenységre megjelölt Azure AD-felhasználók azonosítása](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [A felhasználók identitás-és hozzáférési tevékenységének figyelése Azure Security Center](../security-center/security-center-identity-access.md)

- [Riasztások Azure Security Center veszélyforrások elleni védelmi moduljában](../security-center/alerts-reference.md)

- [Azure-beli tevékenység-naplók integrálása a Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Adatok összekapcsolásának Azure AD Identity Protection](../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Advanced Threat Protection](/azure-advanced-threat-protection/what-is-atp)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: az Azure erőforrás-hozzáférés korlátozása feltételek alapján

**Útmutató**: az Azure app Configuration támogatja Azure Active Directory (Azure ad) feltételes hozzáférését egy részletesebb hozzáférés-vezérléshez felhasználó által definiált feltételek alapján, például bizonyos IP-címtartományok felhasználói bejelentkezéséhez az MFA-t kell használnia a bejelentkezéshez. A részletes hitelesítési munkamenet-kezelési szabályzat különböző használati esetekben is használható. Ezek a feltételes hozzáférési szabályzatok csak azokra a felhasználói fiókokra vonatkoznak, amelyek az Azure AD-ben hitelesítik az alkalmazást az alkalmazás konfigurációs szolgáltatásának eléréséhez és kezeléséhez, de nem vonatkoznak a konfigurációs erőforráshoz csatlakozó egyszerű szolgáltatásokra vagy kapcsolati karakterláncokra.

- [Az Azure feltételes hozzáférés áttekintése](../active-directory/conditional-access/overview.md)

- [Általános feltételes hozzáférési szabályzatok](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [A hitelesítési munkamenet felügyeletének konfigurálása feltételes hozzáféréssel](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: a nem szándékolt hitelesítő adatok expozíciójának megszüntetése

**Útmutató**: az Azure-alkalmazások konfigurálása lehetővé teszi, hogy az ügyfelek olyan konfigurációkat tároljanak, amelyek esetleg identitásokat vagy titkos kódokat tartalmazhatnak. A hitelesítő adatoknak a konfigurációkon belüli azonosításához javasolt a hitelesítőadat-olvasó bevezetése. A hitelesítő adatok beolvasása azt is javasolja, hogy a felderített hitelesítő adatokat biztonságosabb helyszínekre (például Azure Key Vault) helyezze.

Az Azure app Configuration szolgáltatást és a Azure Key Vault együtt használhatja. Tárolja a hitelesítő adatokat a Key Vaultban, majd csatolja ezeket a hitelesítő adatokat egy Key Vault-hivatkozás létrehozásához az alkalmazás konfigurációs erőforrásában. Amikor az alkalmazás konfigurációja létrehozza ezeket a hivatkozásokat, az értékek helyett a Key Vault értékek URI-azonosítóit tárolja. Az alkalmazások az alkalmazás-konfigurációhoz kapcsolódhatnak, hogy beolvassák a hitelesítő adatokat a Key Vaultból.

A GitHub esetében a natív titkos keresési funkcióval azonosíthatók a kódokon belüli hitelesítő adatok vagy egyéb titkok.

- [Oktatóanyag Key Vault referenciák ASP.NET Core alkalmazásban való használatához](use-key-vault-references-dotnet-core.md)

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [GitHub-titkos vizsgálat](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="privileged-access"></a>Emelt szintű hozzáférés

*További információt az [Azure biztonsági teljesítményteszt: privilegizált hozzáférés](/azure/security/benchmarks/security-controls-v2-privileged-access)című témakörben talál.*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: magas jogosultsági szintű felhasználók elleni védelem és korlátozás

**Útmutatás**: korlátozza a magas jogosultsági szintű fiókok vagy szerepkörök számát, és emelt szintű védelemmel látja el ezeket a fiókokat, mert az ezzel a jogosultsággal rendelkező felhasználók közvetlenül vagy közvetve elolvashatják és módosíthatják az Azure-környezet minden erőforrását.

Az Azure-erőforrásokhoz és az Azure AD-hez az Azure AD Privileged Identity Management (PIM) használatával engedélyezheti az igény szerinti (JIT) jogosultságú hozzáférést. A JIT ideiglenes engedélyeket biztosít a Kiemelt feladatok végrehajtásához, ha a felhasználóknak szükségük van rá. A PIM biztonsági riasztásokat is létrehozhat, ha az Azure AD-szervezetben gyanús vagy nem biztonságos tevékenység van.

A hozzáférési kulcsok magas jogosultsággal rendelkeznek, és az ajánlott biztonsági gyakorlat szerint rendszeresen el kell forgatni őket. A hozzáférési kulcsok olyan kapcsolati karakterláncokat tartalmaznak, amelyek hitelesítő adatokat tartalmaznak, és titkosnak számítanak. Ezeket a titkokat Azure Key Vault kell tárolni, és a kódnak hitelesítenie kell a Key Vault a lekéréséhez. A hozzáférési kulcsok írási-olvasási vagy csak olvasási hozzáférést biztosíthatnak egy alkalmazáshoz. Győződjön meg arról, hogy a megfelelő típusú hozzáférési kulcs van kiállítva a jogosulatlan hozzáférés megakadályozása érdekében. A biztonság érdekében használja az Azure AD felügyelt identitások szolgáltatását. Ehhez csak az szükséges, hogy a konfigurációs végpont URL-címe hozzáférjen a konfigurációs értékekhez.

- [Alkalmazás-konfiguráció – ajánlott eljárások](howto-best-practices.md#app-configuration-bootstrap)

- [Felügyelt identitások használata az App Configuration eléréséhez](howto-integrate-azure-managed-service-identity.md)
- [Rendszergazdai szerepkör engedélyei az Azure AD-ben](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Azure Privileged Identity Management biztonsági riasztások használata](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Emelt szintű hozzáférés biztosítása Azure AD hibrid- és felhőkörnyezetekhez](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: az üzleti szempontból kritikus fontosságú rendszerek rendszergazdai hozzáférésének korlátozása

**Útmutató**: az Azure-alkalmazások konfigurálása az Azure RBAC segítségével elkülöníti az üzleti szempontból kritikus rendszerek hozzáférését azáltal, hogy korlátozza, hogy mely fiókok emelt szintű hozzáférést kapnak. Az alkalmazás konfigurációja az Azure RBAC az erőforrás szintjén támogatja. Az üzleti szempontból kritikus fontosságú konfigurációk biztonságos kiépítéséhez a saját alkalmazás-konfigurációs erőforrásában tárolja ezeket az információkat. Egy erőforráson belül a részletes hozzáférés csak olvasási hozzáférésű fiókokon vagy kulcsokon, valamint címkézésen és címkézésen keresztül is elérhető.

A hozzáférés-vezérlés összes típusát a vállalati szegmentálási stratégiához kell igazítani, hogy biztosítsa a konzisztens hozzáférés-vezérlést.

- [Felügyeleti csoport elérése](../governance/management-groups/overview.md#management-group-access)

- [Azure-előfizetés rendszergazdái](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [A RBAC integrálása az Azure AD-val az alkalmazás konfigurációjának használatával](concept-enable-rbac.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: az Azure App Configuration Azure Active Directory (Azure ad-) fiókokat használ az erőforrások kezeléséhez, a fiókok és a hozzáférésük érvényességének biztosításához rendszeresen tekintse át a felhasználói fiókokat és a hozzáférés-hozzárendelést. 

Az Azure az Azure AD és a OAuth használatával engedélyezi az alkalmazások konfigurációs adataihoz való hozzáférés engedélyezését az alábbi Azure beépített szerepkörökkel:

- Alkalmazás-konfigurációs adat tulajdonosa: ezt a szerepkört használva olvasási/írási/törlési hozzáférést biztosíthat az alkalmazás konfigurációs adatához. Ez nem biztosít hozzáférést az alkalmazás konfigurációs erőforrásához.

- Alkalmazás-konfigurációs Adatolvasó: ezt a szerepkört használva olvasási hozzáférést adhat az alkalmazás konfigurációs adatához. Ez nem biztosít hozzáférést az alkalmazás konfigurációs erőforrásához

Az Azure AD hozzáférési felülvizsgálatok segítségével áttekintheti a csoporttagság, a vállalati alkalmazásokhoz való hozzáférés és a szerepkör-hozzárendelések, például az alkalmazás konfigurációs szerepkörei. Az Azure AD jelentéskészítési szolgáltatása lehetővé teszi, hogy a naplók segítséget nyújtsanak az elavult fiókok felderítéséhez. A felülvizsgálati folyamat megkönnyítése érdekében a Azure AD Privileged Identity Management használatával hozzáférési felülvizsgálati jelentési munkafolyamatot is létrehozhat.

Megjegyzés: a felügyelt identitásokat akkor javasoljuk, ha az alkalmazások konfigurációját egy másik szolgáltatásból vagy alkalmazásból szeretné hitelesíteni. A használat során külön kell kezelnie az alkalmazás-konfigurációhoz való hozzáféréshez konfigurált egyszerű szolgáltatásokat és kapcsolati karakterláncokat.

- [Azure-beli erőforrás-szerepkörök hozzáférési felülvizsgálatának létrehozása Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Az Azure AD-identitás és a hozzáférési felülvizsgálatok használata](/azure/active-directory/governance/access-reviews-overvie)

- [Hozzáférés engedélyezése az Azure app Configuration szolgáltatáshoz az Azure AD használatával](concept-enable-rbac.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: vészhelyzeti hozzáférés beállítása az Azure AD-ben

**Útmutató**: az Azure-alkalmazások konfigurálása az erőforrások kezeléséhez Azure Active Directory integrált. Ha nem szeretné megakadályozni, hogy véletlenül kizárja az Azure AD-szervezetét, állítson be egy vészhelyzeti hozzáférési fiókot a hozzáféréshez, ha a normál rendszergazdai fiókok nem használhatók. A vészhelyzeti hozzáférési fiókok általában magas jogosultsággal rendelkeznek, és nem rendelhetők hozzá konkrét személyekhez. A vészhelyzeti hozzáférési fiókok olyan vészhelyzeti vagy "break Glass" forgatókönyvekre korlátozódnak, amelyekben nem használhatók normál rendszergazdai fiókok.

Győződjön meg arról, hogy a vészhelyzeti hozzáférési fiókokhoz tartozó hitelesítő adatok (például jelszó, tanúsítvány vagy intelligens kártya) biztonságosak és ismertek, csak azokra a személyekre, akik jogosultak arra, hogy csak vészhelyzetben használják őket.

- [Vészhelyzeti hozzáférési fiókok kezelése az Azure AD-ben](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="pa-5-automate-entitlement-management"></a>PA-5: a jogosultságok kezelésének automatizálása 

**Útmutató**: az Azure-alkalmazások konfigurálása az erőforrások kezeléséhez Azure Active Directory integrált. Az Azure AD-jogosultságok kezelési funkciói segítségével automatizálhatja a hozzáférési kérelmek munkafolyamatait, beleértve a hozzáférési hozzárendeléseket, az értékeléseket és a lejáratokat. A kettős vagy többfázisú jóváhagyás is támogatott.

- [Az Azure AD hozzáférési felülvizsgálatai](/azure/active-directory/governance/access-reviews-overview)

- [Mi az Azure AD-jogosultságok kezelése?](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: emelt szintű hozzáférésű munkaállomások használata

**Útmutató**: a biztonságos, elkülönített munkaállomások kritikus fontosságúak a bizalmas szerepkörök, például a rendszergazdák, a fejlesztők és a kritikus fontosságú szolgáltatók biztonsága szempontjából. A biztonságos felhasználói munkaállomások és/vagy az Azure Bastion használata az alkalmazás konfigurálásával kapcsolatos adminisztratív feladatokhoz. A biztonságos és felügyelt felhasználói munkaállomás felügyeleti feladatokhoz való telepítéséhez Azure Active Directory, a Microsoft Defender komplex veszélyforrások elleni védelem (ATP) és/vagy Microsoft Intune használható. A biztonságos munkaállomások központilag felügyelhetők a biztonságos konfiguráció kényszerítéséhez, beleértve az erős hitelesítést, a szoftver-és a hardver alapkonfigurációit, a korlátozott logikai és hálózati hozzáférést.

- [Az emelt szintű hozzáférésű munkaállomások ismertetése](../active-directory/devices/concept-azure-managed-workstation.md) 

- [Emelt szintű hozzáférési munkaállomás üzembe helyezése](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: csak elég felügyelet (a legalacsonyabb jogosultsági elv) követése 

**Útmutató**: az Azure-alkalmazás konfigurációja integrálva van az Azure szerepköralapú hozzáférés-vezérléssel (RBAC) az erőforrások kezeléséhez. Az Azure RBAC lehetővé teszi az Azure-erőforrások hozzáférésének kezelését a szerepkör-hozzárendeléseken keresztül. Ezeket a szerepköröket hozzárendelheti a felhasználókhoz, a csoportok egyszerű szolgáltatásaihoz és a felügyelt identitásokhoz. Az Azure-alkalmazások konfigurációjának előre definiált beépített szerepkörei vannak, és ezeket a szerepköröket az Azure CLI-vel, a Azure PowerShell-vel vagy a Azure Portal eszközzel is leltározott vagy lekérdezheti. Az erőforrásokhoz az Azure RBAC-on keresztül hozzárendelt jogosultságokat mindig a szerepkörök által igényelt értékre kell korlátozni. Ez kiegészíti a Azure AD Privileged Identity Management (PIM) igény szerinti (JIT) megközelítését, és rendszeresen felül kell vizsgálni.

Az Azure az Azure AD és a OAuth használatával engedélyezi az alkalmazások konfigurációs adataihoz való hozzáférés engedélyezését az alábbi Azure beépített szerepkörökkel:
- Alkalmazás-konfigurációs adat tulajdonosa: ezt a szerepkört használva olvasási/írási/törlési hozzáférést biztosíthat az alkalmazás konfigurációs adatához. Ez nem biztosít hozzáférést az alkalmazás konfigurációs erőforrásához.
- Alkalmazás-konfigurációs Adatolvasó: ezt a szerepkört használva olvasási hozzáférést adhat az alkalmazás konfigurációs adatához. Ez nem biztosít hozzáférést az alkalmazás konfigurációs erőforrásához.

A beépített szerepkörök használatával lefoglalhatja az engedélyt, és szükség esetén csak egyéni szerepköröket hozhat létre. 

Az alkalmazás konfigurációja támogatja több alkalmazás konfigurációjának egyetlen alkalmazás-konfigurációs erőforrásban való tárolását. Az alkalmazások közötti hozzáférés korlátozásához hozzon létre egy alkalmazás-konfigurációs erőforrást, és állítsa be ennek megfelelően az Azure RBAC-t.

- [Mi az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../role-based-access-control/overview.md)

- [A RBAC konfigurálása az Azure-ban](../role-based-access-control/role-assignments-portal.md)

- [Az Azure AD-identitás és a hozzáférési felülvizsgálatok használata](/azure/active-directory/governance/access-reviews-overview)

- [Hozzáférés engedélyezése az Azure app Configuration szolgáltatáshoz az Azure AD használatával](concept-enable-rbac.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: a Microsoft támogatási szolgálatának jóváhagyási folyamatának kiválasztása  

**Útmutató**: szervezeti jóváhagyási folyamat megvalósítása olyan támogatási forgatókönyvek esetén, ahol a Microsoft számára szükség lehet az alkalmazás konfigurációs adataihoz való hozzáférésre. Ügyfélszéf jelenleg nem érhető el az alkalmazások konfigurálását támogató forgatókönyvekhez.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információt az [Azure biztonsági teljesítményteszt: Adatvédelem](/azure/security/benchmarks/security-controls-v2-data-protection)című témakörben talál.*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1: bizalmas adatok felderítése, osztályozása és címkézése

**Útmutató**: bizalmas adatok felderítése, osztályozása és címkézése, hogy megtervezze a megfelelő vezérlőket, hogy a bizalmas adatok tárolása, feldolgozása és továbbítása biztonságos legyen a szervezet technológiai rendszereinek megfelelően. Az alkalmazás konfigurációs erőforrásai esetében a bizalmas adatok címkézése a címkézési formában is támogatott, de a bennük található konfigurációs értékekhez nem. Ha egy alkalmazás olvasási vagy írási/olvasási hozzáféréssel rendelkezik egy konfigurációs tárolóhoz, teljes hozzáférése van a tárolóban található bármelyik konfigurációhoz.

- [Bizalmas adatok címkézése Azure Information Protection használatával](/azure/information-protection/what-is-information-protection)

- [Az adatbesorolások címkézése az Azure-ban](/azure/cloud-adoption-framework/govern/policy-compliance/data-classification#tagging-data-classification-in-azure)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="dp-2-protect-sensitive-data"></a>DP-2: bizalmas adatok védelme

**Útmutató**: a Microsoft által felügyelt mögöttes platformhoz a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és védelmet nyújt az ügyfelek adatvesztése és a kitettség ellen. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft néhány alapértelmezett adatvédelmi vezérlőt és képességet alkalmazott. Győződjön meg arról, hogy rendszeresen elforgatja a hozzáférési kulcsokat az alkalmazás konfigurációs erőforrásaihoz. A kapcsolódási karakterláncokból származó hitelesítő adatokat Azure Key Vault tárolhatja, és a kódnak hitelesítenie kell Key Vault a lekéréséhez. A hozzáférési kulcsok írási-olvasási vagy csak olvasási hozzáférést biztosíthatnak egy alkalmazáshoz. Győződjön meg arról, hogy a megfelelő típusú hozzáférési kulcs van kiállítva a jogosulatlan hozzáférés megakadályozása érdekében. A biztonság érdekében használja az Azure AD felügyelt identitások szolgáltatását. Ehhez csak az szükséges, hogy a konfigurációs végpont URL-címe hozzáférjen a konfigurációs értékekhez.

Hozzáférés korlátozása az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC):

- Különítse el a bizalmas adatokat a saját alkalmazás-konfigurációs erőforrásában, majd a RBAC szabályzatok kiosztása ennek megfelelően, hogy csak a jogosult hozzáférés legyen engedélyezve 

- Hálózati hozzáférés-vezérlés használata

- Az Azure-szolgáltatások (például az SQL és más adatbázisok titkosítása) specifikus vezérlői, valamint a konzisztens hozzáférés-vezérlés biztosítása érdekében a hozzáférés-vezérlés összes típusát össze kell hangolni a vállalati szegmentálási stratégiával.

- A nagyvállalati szegmentálási stratégiát is tájékoztatni kell a bizalmas vagy üzleti szempontból kritikus fontosságú adatok és rendszerek helyéről.

További információkért tekintse meg a következő referenciákat:

- [Hozzáférés engedélyezése az Azure-alkalmazás konfigurációjához Azure Active Directory használatával](concept-enable-rbac.md)

- [Az alkalmazás konfigurációjának adattitkosítása](faq.md#does-app-configuration-encrypt-my-data)

- [Azure-beli szerepköralapú Access Control (RBAC)](../role-based-access-control/overview.md) 

- [Az ügyfelek adatvédelmének megismerése az Azure-ban](../security/fundamentals/protection-customer-data.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: bizalmas adatok titkosítása az átvitel során

**Útmutató**: a hozzáférés-vezérlés kiegészítéseként a forgalomban lévő "sávon kívüli" támadások ellen védeni kell az adatátvitelt. Ezzel biztosíthatja, hogy a támadók ne tudják egyszerűen beolvasni vagy módosítani az adatforrást.

Az Azure-alkalmazás konfigurációja TLS-titkosítást használ az összes HTTP-kérelemhez. Az Azure-infrastruktúra további hálózati szintű titkosítást biztosít az Azure-adatközpontok közötti összes kéréshez. Győződjön meg arról, hogy a HTTP-forgalom, amelyet az alkalmazás-konfiguráció erőforrásaihoz csatlakozó ügyfelek a TLS v 1.2-es vagy újabb verzió egyeztetéséhez használhatnak.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a hozzáférés-vezérlés kiegészítéseként a REST-adatok védelme a "sávon kívüli" támadásoktól (például a mögöttes tárolóhoz való hozzáférés) a titkosítás használatával történik. Ezzel biztosíthatja, hogy a támadók ne tudják egyszerűen beolvasni vagy módosítani az adatforrást.

Az Azure alapértelmezés szerint biztosítja a REST-titkosítást. A szigorúan bizalmas adatok esetében lehetősége van arra, hogy az összes Azure-erőforráson további titkosítást hajtsanak végre, ahol elérhetők. Az Azure alapértelmezés szerint kezeli a titkosítási kulcsokat, az Azure azonban lehetőséget biztosít a saját kulcsok (az ügyfél által felügyelt kulcsok) kezelésére az Azure-alkalmazások konfigurálásához.

- [Az Azure-alkalmazások konfigurációjában az ügyfelek által felügyelt kulcsok használatával titkosíthatja az adatait](concept-customer-managed-keys.md)

- [A inaktív adatok titkosításának megismerése az Azure-ban](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Titkosítási modell és kulcskezelő tábla](../security/fundamentals/encryption-atrest.md#azure-resource-providers-encryption-model-support)

- [Inaktív adatok az Azure-ban – Rest kettős titkosítás](../security/fundamentals/double-encryption.md#data-at-rest)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

## <a name="asset-management"></a>Asset Management (Eszközkezelés)

*További információt az [Azure biztonsági teljesítményteszt: Asset Management](/azure/security/benchmarks/security-controls-v2-asset-management)című témakörben talál.*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Győződjön meg arról, hogy a biztonsági csapat az eszközök kockázataival rendelkezik

**Útmutató**: gondoskodjon arról, hogy a biztonsági csapatok biztonsági olvasói engedélyeket kapjanak az Azure-bérlőben és-előfizetésekben, hogy a biztonsági kockázatokat a Azure Security Center használatával tudják figyelni. 

A biztonsági csapat feladatainak strukturálása, a biztonsági kockázatok figyelése a központi biztonsági csapat vagy egy helyi csapat feladata lehet. Azt is mondta, hogy a biztonsági ismereteket és a kockázatokat mindig központilag összesíteni kell egy szervezeten belül. 

A biztonsági olvasó engedélyei széles körben alkalmazhatók egy teljes bérlőre (gyökérszintű felügyeleti csoportra), vagy a felügyeleti csoportokra vagy az adott előfizetésekre is. 

Megjegyzés: Előfordulhat, hogy további engedélyek szükségesek a számítási feladatok és szolgáltatások láthatóságának megismeréséhez. 

- [A biztonsági olvasó szerepkör áttekintése](../role-based-access-control/built-in-roles.md#security-reader)

- [Az Azure Management Groups áttekintése](../governance/management-groups/overview.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: gondoskodjon arról, hogy a biztonsági csapat hozzáférjen az eszközök leltárához és metaadataihoz

**Útmutató**: gondoskodjon arról, hogy a biztonsági csapatok hozzáférjenek az Azure-ban folyamatosan frissített leltárhoz, például az Azure-alkalmazások konfigurációjában. A biztonsági csapatoknak gyakran erre a leltárra van szükségük ahhoz, hogy kiértékeljék a szervezetnek a felmerülő kockázatokat, valamint a folyamatos biztonsági tökéletesítéseket. Hozzon létre egy Azure Active Directory csoportot, amely tartalmazza a szervezete jogosult biztonsági csapatát, és olvasási hozzáférést rendel hozzájuk az összes Azure-alkalmazás konfigurációs erőforrásához, ezt az előfizetésében egyetlen magas szintű szerepkör-hozzárendeléssel lehet egyszerűsíteni.

A Azure Security Center Inventory szolgáltatás és az Azure Resource Graph az előfizetések összes erőforrását lekérdezheti és felderítheti, beleértve az Azure-szolgáltatásokat, az alkalmazásokat és a hálózati erőforrásokat is.

Címkéket alkalmazhat az Azure-erőforrások,-erőforráscsoportok és-előfizetések számára, hogy logikailag szervezze őket egy besorolásba. Minden címke egy nevet és egy érték párokat tartalmaz. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md)

- [További információ az eszközök címkézéséről: erőforrás-elnevezési és címkézési döntési útmutató](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: az Azure app Configuration a Azure Policy használatával támogatja Azure Resource Manager-alapú telepítéseket és a konfiguráció kényszerítését. A Azure Policy használatával naplózhatja és korlátozhatja, hogy mely szolgáltatások felhasználói tudnak kiépíteni a környezetben. Az Azure Resource Graph segítségével lekérdezheti és felderítheti az előfizetésen belüli erőforrásokat. A Azure Monitor használatával olyan szabályokat is létrehozhat, amelyek riasztásokat aktiválnak, ha a rendszer nem jóváhagyott szolgáltatást észlel.

- [Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: az eszközök életciklus-kezelésének biztonsága

**Útmutató**: olyan biztonsági házirendeket hozhat létre vagy frissíthet, amelyek az eszköz életciklus-kezelési folyamataiban potenciálisan nagy hatású módosításokat végeznek. Ezek a módosítások magukban foglalják a módosításokat, de nem korlátozódnak a következőkre: identitás-szolgáltatók és hozzáférés, adatok érzékenysége, hálózati konfiguráció és rendszergazdai jogosultságok hozzárendelése.

Ha már nincs rájuk szükség, távolítsa el az Azure-erőforrásokat. Győződjön meg arról, hogy a rendszergazdák a hozzáférési kulcsokat rendszeresen elforgatva biztosítják, hogy csak a hitelesített felhasználók férhessenek hozzá a konfigurációs erőforráshoz.

- [Az alkalmazás konfigurálásához használt titkosítási kulcsok elforgatása](concept-customer-managed-keys.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek az Azure Resources Managerrel való interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés letiltása" beállításával.

- [Feltételes hozzáférés konfigurálása az Azure Resources Manager elérésének blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="logging-and-threat-detection"></a>Naplózás és fenyegetések észlelése

*További információt az [Azure biztonsági teljesítményteszt: naplózás és fenyegetések észlelése](/azure/security/benchmarks/security-controls-v2-logging-threat-protection)című témakörben talál.*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: a fenyegetések észlelésének engedélyezése az Azure identitás-és hozzáférés-kezeléséhez

**Útmutató**: az alkalmazás konfigurálása a Azure Active Directory (Azure ad) szolgáltatással integrálható. Ez a következő felhasználói naplókat tartalmazza, amelyeket megtekintheti az Azure AD jelentéskészítési szolgáltatásban, vagy integrálható a Azure Monitor, az Azure Sentinel vagy más SIEM/monitoring eszközökkel a kifinomultabb monitorozási és elemzési felhasználási esetekben:
- Bejelentkezések – a bejelentkezési jelentés információt nyújt a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról.
- Auditnaplók – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplók például a felhasználók, alkalmazások, csoportok, szerepkörök és házirendek hozzáadásával vagy eltávolításával kapcsolatos, az Azure AD-ban található összes erőforráson végrehajtott módosítások.
- Kockázatos bejelentkezések – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa.
- Kockázatosként megjelölt felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

Azure Security Center riasztást is beállíthat bizonyos gyanús tevékenységekre, például a sikertelen hitelesítési kísérletek túlzott számára, az előfizetés elavult fiókjaira. Az alapszintű biztonsági higiénia monitorozása mellett a Azure Security Center veszélyforrások elleni védelmi modul is gyűjthet részletesebb biztonsági riasztásokat az Azure szolgáltatási rétegeiről. Ezzel a képességgel megtekintheti az egyes erőforrásokon belüli fiókok rendellenességeit.

Az alkalmazás-konfigurációs erőforráshoz való hozzáférés másik módja hozzáférési kulcsok használata. Ezeket rendszeresen el kell forgatni annak biztosítása érdekében, hogy a jogosulatlan ügynökök ne férhessenek hozzá a konfigurációs erőforráshoz. Az elforgatása közvetlenül a portálon, a "hozzáférési kulcsok" területen végezhető el.

- [Az Azure-alkalmazások konfigurációjának engedélyezése más Azure AD-beli védett erőforrások eléréséhez felügyelt identitás használatával](overview-managed-identity.md)

- [Felügyelt identitások használata az Azure app Configuration használatával](howto-integrate-azure-managed-service-identity.md)

- [Naplózási tevékenységgel kapcsolatos jelentések a Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Az Azure Identity Protection engedélyezése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Azure app Configuration szolgáltatáshoz való hozzáférés engedélyezése az Azure AD-vel](concept-enable-rbac.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: az Azure hálózati tevékenységek naplózásának engedélyezése

**Útmutató**: az Azure-alkalmazás konfigurációja nem helyez üzembe semmilyen erőforrást közvetlenül egy virtuális hálózatban. Az alkalmazás konfigurációja azonban lehetővé teszi privát végpontok használatát az Azure-alkalmazások konfigurációjának biztonságos csatlakoztatásához egy virtuális hálózatból. Az Azure app Configuration emellett nem állít elő vagy dolgoz fel olyan DNS-lekérdezési naplókat, amelyeket engedélyezni kell.

Engedélyezze a naplózást a konfigurált alkalmazás-konfiguráció saját végpontján a rögzítéshez:
- A magánhálózati végpont által feldolgozott adatértékek (be/ki)
- A privát kapcsolati szolgáltatás által feldolgozott adatértékek (be/ki)
- NAT-port elérhetősége

További információkért tekintse meg a következő referenciákat:

- [Azure Private link-figyelés](../private-link/private-link-overview.md#logging-and-monitoring)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: az automatikusan elérhető tevékenység-naplók tartalmazzák az alkalmazás konfigurációs erőforrásainak összes írási műveletét (Put, post, DELETE), kivéve az olvasási MŰVELETEKET (Get). A Tevékenységnaplók segítségével hibát kereshet a hibaelhárítás során, vagy megfigyelheti, hogy a szervezet felhasználója hogyan módosított egy erőforrást. Az alkalmazás konfigurálásakor a tevékenységi naplók csak a vezérlési síkon érhetők el, és a Azure Resource Manager (ARM) felületen vannak felkészítve. Az alkalmazás-konfigurációhoz tartozó ügyfél-adatsíkok naplózása jelenleg nem támogatott. Az Azure-erőforrás-naplók is konfigurálhatók.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [A naplózás és a különböző naplózási típusok megismerése az Azure-ban](../azure-monitor/platform/platform-logs-overview.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: a biztonsági naplók központosított kezelése és elemzése

**Útmutató**: a korreláció engedélyezéséhez központosítsa a naplózási tárolást és az elemzést. Minden naplózási forrás esetében ellenőrizze, hogy rendelkezik-e az adatok tulajdonosával, a hozzáférési útmutatással, a tárolási hellyel, az adatok feldolgozásához és eléréséhez, valamint az adatmegőrzési követelményekhez használt eszközökhöz.

Győződjön meg arról, hogy integrálja az Azure-tevékenységek naplóit a központi naplózásba. Naplók betöltése Azure Monitor használatával a végponti eszközök, a hálózati erőforrások és más biztonsági rendszerek által generált biztonsági adatokat összesítve. Azure Monitor a Log Analytics-munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat hosszú távú és archiválási tároláshoz.

Emellett az Azure Sentinel vagy egy harmadik féltől származó SIEM-nek is engedélyezi az adatok bevezetését. Számos szervezet úgy dönt, hogy a gyakran használt és az Azure-tárolót használja a ritkábban használt adatokhoz az Azure Sentinel használatával.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: a naplózási tár megőrzésének konfigurálása

**Útmutató**: gondoskodjon arról, hogy minden olyan Storage-fiók vagy log Analytics-munkaterület, amely az alkalmazás-konfigurációs naplók tárolásához használatos, a szervezet megfelelőségi szabályainak megfelelően állítsa be a napló megőrzési időtartamát. Az Azure Storage, a Data Lake vagy a Log Analytics munkaterület-fiókok használata hosszú távú és archiválási tároláshoz.

Azure Monitor az Log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi előírásai szerint állíthatja be.

- [Log Analytics munkaterület megőrzési időtartamának konfigurálása](../azure-monitor/platform/manage-cost-storage.md)

- [Erőforrás-naplók tárolása Azure Storage-fiókban](/azure/azure-monitor/platform/resource-logs-collect-storage)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

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

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: biztonságos konfigurációk létrehozása az Azure-szolgáltatásokhoz 

**Útmutató**: az Azure-alkalmazások konfigurálása az Azure-erőforrások konfigurációjának naplózásához és érvényesítéséhez Azure Security Center elérhető, a szolgáltatásra vonatkozó házirendeket támogatja. Ezt Azure Security Center vagy Azure Policy kezdeményezésekben lehet konfigurálni.
- Az alkalmazás konfigurációjának az ügyfél által felügyelt kulcsot kell használnia: az ügyfél által felügyelt kulcsok biztosítják a fokozott adatvédelmet, mivel lehetővé teszi a titkosítási kulcsok kezelését. Ez gyakran szükséges a megfelelőségi követelmények teljesítéséhez.
- Az alkalmazás konfigurációjának privát hivatkozást kell használnia: a magánhálózati végponti kapcsolatok lehetővé teszik a virtuális hálózat ügyfelei számára, hogy privát kapcsolaton keresztül biztonságosan hozzáférjenek az Azure-alkalmazások konfigurációjához.

Az Azure tervrajzai segítségével automatizálhatja a szolgáltatások és az alkalmazások környezetének üzembe helyezését és konfigurálását, beleértve az Azure Resources Manager-sablonokat, az Azure RBAC-vezérlőket és a házirendeket egyetlen terv definíciójában.

- [További információ az alkalmazás konfigurációs házirendjeiről](../governance/policy/samples/built-in-policies.md#app-configuration)

- [A biztonsági szabályzatok használata a Azure Security Centerban](../security-center/tutorial-security-policy.md)

- [A Guardrails megvalósításának illusztrációja a vállalati léptékű kirakodási zónában](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../governance/blueprints/overview.md)

**Azure Security Center figyelés**: igen

**Felelősség**: megosztott

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: biztonságos konfigurációk fenntartása az Azure-szolgáltatásokhoz

**Útmutató**: a Azure Security Center használata az alapkonfiguráció figyelésére és a Azure Policy használatával történő betartatására. Az alkalmazások konfigurációjának Azure Policy a következőket tartalmazza: 
- Az alkalmazás konfigurációjának az ügyfél által felügyelt kulcsot kell használnia: az ügyfél által felügyelt kulcsok biztosítják a fokozott adatvédelmet, mivel lehetővé teszi a titkosítási kulcsok kezelését. Ez gyakran szükséges a megfelelőségi követelmények teljesítéséhez.
- Az alkalmazás konfigurációjának privát hivatkozást kell használnia: a magánhálózati végponti kapcsolatok lehetővé teszik a virtuális hálózat ügyfelei számára, hogy privát kapcsolaton keresztül biztonságosan hozzáférjenek az Azure-alkalmazások konfigurációjához.

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md) 

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center figyelés**: igen

**Felelősség**: megosztott

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: rendszeres támadási szimulációk végrehajtása

**Útmutatás**: igény szerint az Azure-erőforrásokon bevezetési teszteket vagy Red Team-tevékenységeket hajthat végre, és biztosíthatja az összes kritikus biztonsági vizsgálat szervizelését.
Kövesse a Microsoft Cloud penetráció tesztelési szabályait, amelyekkel biztosíthatja, hogy a behatolási tesztek ne sértsék a Microsoft szabályzatait. A Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatban a Microsoft stratégiáját és a Red Teaming és a Live site penetráció tesztelését is használhatja.

- [Behatolás-tesztelés az Azure-ban](../security/fundamentals/pen-testing.md)

- [Behatolás-tesztelési szabályok engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud vörös összevonása](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

## <a name="backup-and-recovery"></a>Biztonsági másolat és helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: biztonsági mentés és helyreállítás](/azure/security/benchmarks/security-controls-v2-backup-recovery)című témakörben talál.*

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: az elveszett kulcsok kockázatának enyhítése

**Útmutató**: Ellenőrizze, hogy vannak-e olyan intézkedések, amelyek megakadályozzák a kulcsok elvesztését és helyreállítását. A kulcsok véletlen vagy rosszindulatú törléssel szembeni védelme érdekében engedélyezze a Azure Key Vault a védelem lágy törlését és kiürítését.

- [A védelem lágy törlésének és kiürítésének engedélyezése a Key Vaultban](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

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

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure Security teljesítményteszt v2 áttekintése](/azure/security/benchmarks/overview) című témakört.
- További információ az [Azure biztonsági alaptervekről](/azure/security/benchmarks/security-baselines-overview)
