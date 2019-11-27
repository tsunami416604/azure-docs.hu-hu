---
title: Általános üzemeltetési útmutató Azure Active Directory
description: Ez az üzemeltetési útmutató ismerteti az általános műveletek biztonságossá tételéhez szükséges ellenőrzéseket és műveleteket.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 46e5af9d54cf818366bd2730de0da85dcbe6cade
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535300"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Általános üzemeltetési útmutató Azure Active Directory

Az [Azure ad üzemeltetési útmutatójának](active-directory-ops-guide-intro.md) jelen szakasza azokat az ellenőrzéseket és műveleteket ismerteti, amelyeket a Azure Active Directory (Azure ad) általános műveleteinek optimalizálása érdekében el kell végeznie.

> [!NOTE]
> Ezek a javaslatok a közzététel dátumától kezdve érvényesek, de idővel változhatnak. A szervezeteknek folyamatosan értékelniük kell működési eljárásaikat, mivel a Microsoft termékei és szolgáltatásai idővel fejlődnek.

## <a name="key-operational-processes"></a>Fő működési folyamatok

### <a name="assign-owners-to-key-tasks"></a>Tulajdonosok kiosztása a legfontosabb feladatokhoz

A Azure Active Directory kezelése a legfontosabb működési feladatok és folyamatok folyamatos végrehajtásához szükséges, ami esetleg nem része a bevezetési projektnek. Továbbra is fontos, hogy ezeket a feladatokat a környezet optimalizálása érdekében állítsa be. A legfontosabb feladatok és az ajánlott tulajdonosok a következők:

| Tevékenység | Tulajdonos |
| :- | :- |
| Az identitások biztonságos pontszámának fejlesztése | InfoSec-műveleti csapat |
| Azure AD Connect-kiszolgálók karbantartása | IAM Operations csapat |
| IdFix-jelentések rendszeres végrehajtása és osztályozása | IAM Operations csapat |
| A szinkronizálás és a AD FS osztályozása Azure AD Connect Health-riasztások | IAM Operations csapat |
| Ha nem használja a Azure AD Connect Healtht, akkor az ügyfél az egyéni infrastruktúra figyeléséhez megfelelő folyamattal és eszközökkel rendelkezik | IAM Operations csapat |
| Ha nem használja a AD FSt, akkor az ügyfél az egyéni infrastruktúra figyeléséhez megfelelő folyamattal és eszközökkel rendelkezik | IAM Operations csapat |
| Hibrid naplók monitorozása: Azure AD alkalmazás proxy-összekötők | IAM Operations csapat |
| Hibrid naplók monitorozása: továbbító hitelesítési ügynökök | IAM Operations csapat |
| Hibrid naplók figyelése: Password visszaírási Service | IAM Operations csapat |
| Hibrid naplók figyelése: helyszíni jelszavas védelmi átjáró | IAM Operations csapat |
| Hibrid naplók figyelése: Azure MFA NPS-bővítmény (ha alkalmazható) | IAM Operations csapat |

A lista áttekintése során előfordulhat, hogy tulajdonost kell rendelnie olyan feladatokhoz, amelyek nem rendelkeznek tulajdonossal, vagy nem módosítanak tulajdonjogot olyan feladatokhoz, amelyek nem illeszkednek a fenti javaslatokhoz.

#### <a name="owners-recommended-reading"></a>Ajánlott olvasás a tulajdonosoknak

- [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Irányítás az Azure-ban](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="hybrid-management"></a>Hibrid felügyelet

### <a name="recent-versions-of-on-premises-components"></a>A helyszíni összetevők legújabb verziói

A helyszíni összetevők legfrissebb verzióival az ügyfél a legújabb biztonsági frissítéseket, a teljesítménnyel kapcsolatos fejlesztéseket és a környezet további egyszerűsítését segítő funkciókat biztosít. A legtöbb összetevő automatikus frissítési beállítással rendelkezik, amely automatizálja a frissítési folyamatot.

Ezek az összetevők a következők:

- Azure AD Connect
- Azure AD Application Proxy-összekötők
- Azure AD átmenő hitelesítési ügynökök
- Azure AD Connect Health ügynökök

Ha az egyiket nem hozták létre, meg kell határoznia az összetevők frissítésének folyamatát, és ha lehetséges, az automatikus frissítési funkcióra kell támaszkodnia. Ha hat vagy több hónapot tartalmazó összetevőket talál, akkor a lehető leghamarabb frissítenie kell.

#### <a name="hybrid-management-recommended-reading"></a>Hibrid kezelés – ajánlott olvasás

- [Azure AD Connect: automatikus frissítés](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-automatic-upgrade)
- [Az Azure AD Application Proxy-összekötők ismertetése | Automatikus frissítések](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Azure AD Connect Health riasztás alapterve

A szervezeteknek [Azure ad Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#what-is-azure-ad-connect-health) kell központilag telepíteniük Azure AD Connect és AD FS monitorozásához és jelentéskészítéséhez. A Azure AD Connect és AD FS olyan kritikus fontosságú összetevők, amelyek képesek az életciklus-kezelés és a hitelesítés megszakítására, és ezért kimaradások elvégzéséhez vezethetnek. Azure AD Connect Health segít megfigyelni és betekintést nyerni a helyszíni identitás-infrastruktúrába, így biztosítva a környezet megbízhatóságát.

![Azure AD Connect Heath-architektúra](./media/active-directory-ops-guide/active-directory-ops-img16.png)

A környezet állapotának figyelése során azonnal meg kell felelnie a nagy súlyosságú riasztásoknak, majd az alacsonyabb súlyossági riasztásoknak.

#### <a name="azure-ad-connect-health-recommended-reading"></a>Azure AD Connect Health ajánlott olvasás

- [Az Azure AD Connect Health-ügynök telepítése](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

### <a name="on-premises-agents-logs"></a>Helyszíni ügynökök naplói

Néhány identitás-és hozzáférés-kezelési szolgáltatás a helyszíni ügynököket igényli a hibrid forgatókönyvek engedélyezéséhez. Ilyenek például a jelszó alaphelyzetbe állítása, az átmenő hitelesítés (PTA ESP), az Azure AD Application Proxy és az Azure MFA NPS-bővítmény. Fontos, hogy az operatív csapat alapkonfigurációja és az összetevők állapotának figyelése az összetevő-ügynök naplófájljainak archiválásával és elemzésével, például System Center Operations Manager vagy SIEM használatával. Ugyanilyen fontos, hogy az INFOSEC-műveleti csapat vagy az ügyfélszolgálat Ismerje meg a hibák elhárításának módját.

#### <a name="on-premises-agents-logs-recommended-reading"></a>A helyszíni ügynökök a javasolt olvasást naplózzák

- [Alkalmazásproxy – problémamegoldás](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)
- [Önkiszolgáló jelszó-visszaállítás hibaelhárítása – Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
- [Az Azure AD Application Proxy-összekötők ismertetése](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors)
- [Azure AD Connect: átmenő hitelesítés – problémamegoldás](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication#collecting-pass-through-authentication-agent-logs)
- [Az Azure MFA NPS-bővítményhez tartozó hibakódok megoldása](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-errors)

### <a name="on-premises-agents-management"></a>Helyszíni ügynökök kezelése

Az ajánlott eljárások bevezetése segíthet a helyszíni ügynökök optimális működésében. Tekintse át az alábbi ajánlott eljárásokat:

- Több Azure AD alkalmazásproxy-összekötőt ajánlunk a zökkenőmentes terheléselosztás és a magas rendelkezésre állás érdekében azáltal, hogy a proxy alkalmazásokhoz való hozzáférés során elkerülhetők a meghibásodási pontok. Ha jelenleg csak egy összekötő található az éles üzemben lévő alkalmazásokat kezelő összekötő csoportban, legalább két összekötőt kell üzembe helyeznie a redundancia érdekében.
- Az alkalmazás-proxy összekötő csoport hibakeresési célokra való létrehozása és használata hasznos lehet a forgatókönyvek és az új helyszíni alkalmazások előkészítése során. Javasoljuk továbbá, hogy a hálózati eszközöket, például az üzenetsor-elemzőt és a hegedűst is telepítse az összekötő-gépeken.
- Több átmenő hitelesítési ügynök használata ajánlott a zökkenőmentes terheléselosztás és a magas rendelkezésre állás biztosításához azáltal, hogy a hitelesítési folyamat során nem kerül egyetlen meghibásodási pont. Ügyeljen arra, hogy legalább két átmenő hitelesítési ügynököt helyezzen üzembe a redundancia érdekében.

#### <a name="on-premises-agents-management-recommended-reading"></a>Helyszíni ügynökök felügyeletének ajánlott olvasata

- [Az Azure AD Application Proxy-összekötők ismertetése](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors)
- [Azure AD átmenő hitelesítés – gyors útmutató](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start#step-5-ensure-high-availability)

## <a name="management-at-scale"></a>Felügyelet nagy léptékben

### <a name="identity-secure-score"></a>Identitásbiztonság pontszám

Az [identitás biztonságos pontszáma](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score) a szervezet biztonsági helyzetének számszerűsíthető mértékét biztosítja. Fontos, hogy folyamatosan áttekintse és foglalkozzon a jelentett eredményekkel, és törekedjen a lehető legmagasabb pontszámot. A pontszám az alábbiakban nyújt segítséget:

- Az identitásbiztonsági rendszer objektív felmérése
- Identitásbiztonsági fejlesztések tervezése
- A fejlesztések hatásának felmérése

![Biztonsági pontszám](./media/active-directory-ops-guide/active-directory-ops-img17.png)

Ha a szervezete jelenleg nem rendelkezik olyan programmal, amely figyeli a változásokat az identitások biztonságos Pontszámában, javasoljuk, hogy hozzon létre egy csomagot, és rendeljen tulajdonosokat a figyeléshez és a javító műveletekhez. A szervezeteknek a lehető leghamarabb ki kell javítaniuk a fejlesztési műveleteket a 30-nál nagyobb pontszámot érintő hatásokkal.

### <a name="notifications"></a>Értesítések

A Microsoft e-mailes kommunikációt küld a rendszergazdáknak a szolgáltatás különböző változásainak, a szükséges konfigurációs frissítéseknek és a rendszergazdai beavatkozást igénylő hibáknak a bejelentéséhez. Fontos, hogy az ügyfelek az értesítő e-mail-címeket úgy állítsa be, hogy az értesítéseket a megfelelő csapattagok kapják meg, akik elfogadják és felhasználhatják az összes értesítést. Azt javasoljuk, hogy több címzettet adjon hozzá az [Office 365 Message Center](https://docs.microsoft.com/office365/admin/manage/message-center) szolgáltatáshoz, és kérje meg, hogy az értesítéseket (beleértve Azure ad Connect Health értesítéseket) egy terjesztési listára vagy megosztott postaládába küldje el. Ha egy e-mail-címmel rendelkező globális rendszergazdai fiókkal rendelkezik, mindenképpen legalább két, e-mail-kompatibilis fiókot kell konfigurálnia.

Az Azure AD két "feladó" címet használ: <o365mc@email2.microsoft.com>, amely az Office 365 Message Center-értesítéseket küldi el; és <azure-noreply@microsoft.com>, amelyek a következőhöz kapcsolódó értesítéseket küldenek:

- [Azure AD-hozzáférési felülvizsgálatok](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)
- [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations#enable-email-notifications)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/notifications)
- [Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)
- [Vállalati alkalmazás lejárati tanúsítványának értesítései](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on#add-email-notification-addresses-for-certificate-expiration)
- Vállalati alkalmazások kiépítési szolgáltatásával kapcsolatos értesítések

Az alábbi táblázatból megtudhatja, hogy milyen típusú értesítéseket küld, és hogy hol keresse meg őket:

| Értesítés forrása | Az Elküldések | Hol érdemes megnézni |
|:-|:-|:-|
| Technikai kapcsolattartó | Szinkronizálási hibák | Azure Portal – Tulajdonságok panel |
| Office 365 Message Center | Az Identity Services és a O365 háttér-szolgáltatásainak incidens-és romlási megjegyzései | Office-portál |
| Identity Protection heti kivonata | Identity Protection-kivonatoló | Azure AD Identity Protection panel |
| Azure AD Connect Health | Riasztási értesítések | Azure Portal – Azure AD Connect Health panel |
| Vállalati alkalmazások értesítései | Értesítések, amikor a tanúsítványok lejárnak és kiépítési hibák | Azure Portal – vállalati alkalmazás panel (minden alkalmazás saját e-mail-cím beállítással rendelkezik) |

#### <a name="notifications-recommended-reading"></a>Ajánlott olvasási értesítések

- [Megváltoztathatja a munkahely címeit, a technikai kapcsolattartási és egyéb Office-365](https://docs.microsoft.com/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>Működési felületi terület

### <a name="ad-fs-lockdown"></a>Zárolás AD FS

Szervezetek, amelyek az [Azure ad Smart zárolási](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)szolgáltatásból közvetlenül az Azure ad-be való hitelesítéshez konfigurálják az alkalmazásokat. Ha AD FSt használ a Windows Server 2012 R2 rendszerben, implementálja AD FS [extranet zárolási védelmét](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Ha AD FSt használ a Windows Server 2016-es vagy újabb verziójára, implementálja az [extranetes intelligens zárolást](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). Legalább azt javasoljuk, hogy engedélyezze az extranet zárolását a helyszíni Active Directory elleni találgatásos támadásokkal szemben. Ha azonban AD FS a Windows 2016-es vagy újabb verziójával, akkor engedélyeznie kell az extranetes intelligens zárolást is, amely segít enyhíteni a [jelszó-szórásos](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/) támadásokat.

Ha AD FS csak az Azure AD-összevonás esetében használatos, néhány végpontot ki lehet kapcsolni a támadási felület minimalizálásához. Ha például AD FSt csak az Azure AD-hez használja, tiltsa le a WS-Trust végpontokat a **usernamemixed** és a **windowstransport**számára engedélyezett végpontokon kívül.

### <a name="access-to-machines-with-on-premises-identity-components"></a>A helyszíni identitás-összetevőket tartalmazó gépekhez való hozzáférés

A szervezeteknek a helyszíni hibrid összetevőkkel megegyező módon kell lezárniuk a gépek hozzáférését a helyszíni tartományhoz. Például a biztonságimásolat-felelős vagy a Hyper-V-rendszergazda nem tud bejelentkezni a Azure AD Connect kiszolgálóra a szabályok módosításához.

A Active Directory felügyeleti réteg modellje úgy lett kialakítva, hogy a rendszer a környezet teljes vezérlése (0. réteg) és a támadók által gyakran feltört magas kockázatú munkaállomás-eszközök között puffer zónák használatával megvédje az identitási rendszereket. ![A réteg modell három rétegét bemutató ábra](./media/active-directory-ops-guide/active-directory-ops-img18.png)

A [réteg modell](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) három szintből áll, és csak rendszergazdai fiókokat tartalmaz, nem általános jogú felhasználói fiókokat.

- **0. réteg** – a vállalati identitások közvetlen ellenőrzése a környezetben. A 0. csomag olyan fiókokat, csoportokat és egyéb eszközöket tartalmaz, amelyek közvetlen vagy közvetett felügyeleti felügyelettel rendelkeznek a Active Directory erdőben, tartományokban vagy tartományvezérlőkön, valamint az összes benne található eszközön. Az összes 0. szintű eszköz biztonsági érzékenysége egyenértékű, mivel azok hatékonyan szabályozzák egymást.
- **1. réteg** – a vállalati kiszolgálók és alkalmazások vezérlése. Az 1. szintű eszközök közé tartoznak a kiszolgálói operációs rendszerek, a Cloud Services és a vállalati alkalmazások. Az 1. szintű rendszergazdai fiókok az ezen eszközökön üzemeltetett jelentős mennyiségű üzleti érték felügyeleti felügyeletével rendelkeznek. Gyakori példa a kiszolgálói rendszergazdák, akik karbantartják ezeket az operációs rendszereket, és képesek az összes vállalati szolgáltatásra hatással.
- **2. réteg** – felhasználói munkaállomások és eszközök vezérlése. A 2. szintű rendszergazdai fiókok a felhasználói munkaállomásokon és eszközökön üzemeltetett jelentős mennyiségű üzleti érték felügyeletét szabályozzák. Ilyenek például az ügyfélszolgálat és a számítógép-támogatási rendszergazdák, mert befolyásolhatják a szinte bármilyen felhasználói adatok integritását.

A tartományvezérlők esetében ugyanúgy zárja be a helyszíni identitás-összetevők, például a Azure AD Connect, a AD FS és az SQL-szolgáltatások elérését.

## <a name="summary"></a>Összefoglalás

A biztonságos identitás-infrastruktúrának hét aspektusa van. Ez a lista segít megtalálni azokat a műveleteket, amelyeket el kell végeznie a Azure Active Directory (Azure AD) műveleteinek optimalizálása érdekében.

- Rendeljen tulajdonosokat a legfontosabb feladatokhoz.
- A helyszíni hibrid összetevők frissítési folyamatának automatizálása.
- Azure AD Connect Health üzembe helyezése Azure AD Connect és AD FS monitorozásához és jelentéskészítéséhez.
- A helyszíni hibrid összetevők állapotának monitorozása az összetevő-ügynök naplófájljainak archiválásával és elemzésével System Center Operations Manager vagy egy SIEM megoldás használatával.
- A biztonsági helyzeteket a személyazonosság biztonságos pontszámának mérésével valósíthatja meg.
- AD FS zárolása.
- A helyszíni identitás-összetevőkkel rendelkező gépekhez való hozzáférés zárolása.

## <a name="next-steps"></a>Következő lépések

Tekintse át az [Azure ad telepítési terveit](active-directory-deployment-plans.md) a nem telepített képességek megvalósítására vonatkozó részletekért.
