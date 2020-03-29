---
title: Az Azure Active Directory általános műveleti útmutatójának hivatkozása
description: Ez a műveleti referencia-útmutató ismerteti azokat az ellenőrzéseket és műveleteket, amelyeket az általános műveletek biztonságossá tétele érdekében kell
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
ms.openlocfilehash: d039373d3e70076149da2b970a234b59d7aa661a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422944"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Az Azure Active Directory általános műveleti útmutatójának hivatkozása

Az Azure [AD-műveletek referencia-útmutatójának](active-directory-ops-guide-intro.md) ez a szakasza ismerteti az Okat az Azure Active Directory (Azure AD) általános műveleteinek optimalizálásához szükséges ellenőrzéseket és műveleteket.

> [!NOTE]
> Ezek az ajánlások a közzététel időpontjától aktuálisak, de idővel változhatnak. A szervezeteknek folyamatosan értékelniük kell működési gyakorlatukat, ahogy a Microsoft-termékek és -szolgáltatások idővel fejlődnek.

## <a name="key-operational-processes"></a>Kulcsfontosságú működési folyamatok

### <a name="assign-owners-to-key-tasks"></a>Tulajdonosok hozzárendelése a legfontosabb feladatokhoz

Az Azure Active Directory kezeléséhez a legfontosabb operatív feladatok és folyamatok folyamatos végrehajtására van szükség, amelyek nem feltétlenül részei a bevezetési projektnek. Továbbra is fontos, hogy ezeket a feladatokat a környezet optimalizálása érdekében állítsa be. A legfontosabb feladatok és az ajánlott tulajdonosok a következők:

| Tevékenység | Tulajdonos |
| :- | :- |
| Az identitásbiztonságos pontszám fejlesztései | InfoSec műveleti csapat |
| Azure AD Connect-kiszolgálók karbantartása | IAM műveleti csapat |
| IdFix-jelentések rendszeres végrehajtása és osztályozása | IAM műveleti csapat |
| Triage Azure AD Connect állapotriasztások szinkronizáláshoz és AD FS-hez | IAM műveleti csapat |
| Ha nem használja az Azure AD Connect Health szolgáltatást, akkor az ügyfél egyenértékű folyamattal és eszközökkel rendelkezik az egyéni infrastruktúra figyeléséhez | IAM műveleti csapat |
| Ha nem használja az AD FS-t, akkor az ügyfél egyenértékű folyamattal és eszközökkel rendelkezik az egyéni infrastruktúra figyeléséhez | IAM műveleti csapat |
| Hibrid naplók figyelése: Azure AD alkalmazásproxy-összekötők | IAM műveleti csapat |
| Hibrid naplók figyelése: Áthaladási hitelesítési ügynökök | IAM műveleti csapat |
| Hibrid naplók figyelése: Jelszó-visszaíró szolgáltatás | IAM műveleti csapat |
| Hibrid naplók figyelése: Helyszíni jelszóvédelmi átjáró | IAM műveleti csapat |
| Hibrid naplók figyelése: Azure MFA NPS-bővítmény (ha van ilyen) | IAM műveleti csapat |

A lista áttekintésekor előfordulhat, hogy tulajdonost kell hozzárendelnie a tulajdonosból hiányzó feladatokhoz, vagy módosítania kell a fenti javaslatokhoz nem igazodó tulajdonossal rendelkező feladatok tulajdonjogát.

#### <a name="owners-recommended-reading"></a>A tulajdonosok ajánlották az olvasást

- [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Irányítás az Azure-ban](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="hybrid-management"></a>Hibrid menedzsment

### <a name="recent-versions-of-on-premises-components"></a>A helyszíni összetevők legújabb verziói

A helyszíni összetevők legfrissebb verzióival rendelkezik, így az ügyfél nek biztosíthatja a legújabb biztonsági frissítéseket, teljesítménybeli fejlesztéseket, valamint olyan funkciókat, amelyek segíthetnek a környezet további egyszerűsítésében. A legtöbb összetevő automatikus frissítési beállítással rendelkezik, amely automatizálja a frissítési folyamatot.

Ezek az összetevők a következők:

- Azure AD Connect
- Azure AD alkalmazásproxy-összekötők
- Azure AD átmenő hitelesítési ügynökök
- Azure AD Connect állapotügynökök

Ha nincs megállapítva, meg kell határoznia egy folyamatot az összetevők frissítéséhez, és amikor csak lehetséges, az automatikus frissítési szolgáltatásra kell támaszkodnia. Ha olyan összetevőket talál, amelyek legalább hat hónappal le vannak maradva, a lehető leghamarabb frissítenie kell.

#### <a name="hybrid-management-recommended-reading"></a>Hibrid menedzsment ajánlott olvasás

- [Azure AD Connect: automatikus frissítés](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-automatic-upgrade)
- [Az Azure AD alkalmazásproxy-összekötők ismertetése | Automatikus frissítések](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Az Azure AD Connect állapotriasztásalapkonfigurációja

A szervezeteknek telepíteniük kell az [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#what-is-azure-ad-connect-health) szolgáltatást az Azure AD Connect és az AD FS figyeléséhez és jelentéséhez. Az Azure AD Connect és az AD FS olyan kritikus összetevők, amelyek megszakíthatják az életciklus-kezelést és -hitelesítést, és így kimaradásokhoz vezethetnek. Az Azure AD Connect Health segítségével figyelheti és betekintést nyerhet a helyszíni identitásinfrastruktúrába, így biztosítva a környezet megbízhatóságát.

![Azure AD Connect Heath architektúra](./media/active-directory-ops-guide/active-directory-ops-img16.png)

A környezet állapotának figyelése során azonnal foglalkoznia kell a magas súlyosságú riasztásokkal, majd alacsonyabb súlyosságú riasztásokkal.

#### <a name="azure-ad-connect-health-recommended-reading"></a>Az Azure AD Connect Health ajánlott olvasása

- [Az Azure AD Connect Health-ügynök telepítése](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

### <a name="on-premises-agents-logs"></a>Helyszíni ügynökök naplói

Egyes identitás- és hozzáférés-kezelési szolgáltatások a hibrid forgatókönyvek engedélyezéséhez helyszíni ügynököket igényelnek. Ilyenek például a jelszó-visszaállítás, az átmenő hitelesítés (PTA), az Azure AD alkalmazásproxy és az Azure MFA NPS-bővítmény. Kulcsfontosságú, hogy az operatív csapat alapkonfigurációja és az összetevők állapotának figyelése az összetevő-ügynök naplók archiválásával és elemzésével olyan megoldások használatával, mint a System Center Operations Manager vagy a SIEM. Ugyanilyen fontos, hogy az Infosec műveleti csapata vagy ügyfélszolgálata ismerje meg, hogyan háríthatja el a hibamintákat.

#### <a name="on-premises-agents-logs-recommended-reading"></a>A helyszíni ügynökök naplózzák az ajánlott olvasást

- [Alkalmazásproxyval kapcsolatos hibaelhárítás](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)
- [Önkiszolgáló jelszó-visszaállítás – hibaelhárítás – Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
- [Az Azure AD alkalmazásproxy-összekötők megismerése](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors)
- [Azure AD Connect: Áthaladási hitelesítés sel kapcsolatos hibaelhárítás](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication#collecting-pass-through-authentication-agent-logs)
- [Hibakódok elhárítása az Azure MFA NPS-bővítményhez](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-errors)

### <a name="on-premises-agents-management"></a>Helyszíni ügynökök kezelése

Az ajánlott eljárások alkalmazása segíthet a helyszíni ügynökök optimális működésében. Vegye figyelembe a következő ajánlott eljárásokat:

- Összekötőcsoportonként több Azure AD alkalmazásproxy-összekötő használata ajánlott zökkenőmentes terheléselosztást és magas rendelkezésre állást biztosítva azáltal, hogy a proxyalkalmazások elérésekor elkerüli az egyetlen meghibásodási pontokat. Ha jelenleg csak egy összekötő egy összekötő csoportban, amely kezeli az alkalmazások éles környezetben, legalább két összekötőt kell telepítenie a redundancia érdekében.
- Az alkalmazásproxy-összekötő csoport hibakeresési célokra történő létrehozása és használata hibaelhárítási célokra hasznos lehet a forgatókönyvek elhárításához és az új helyszíni alkalmazások beépítésekor. Azt is javasoljuk, hogy telepítse ningingeszközök, mint például a Message Analyzer és a Fiddler a csatlakozó gépek.
- Több átmenő hitelesítési ügynökök ajánlott zökkenőmentes terheléselosztás és a magas rendelkezésre állás elkerülése érdekében egyetlen pont hiba a hitelesítési folyamat során. Győződjön meg arról, hogy legalább két átmenő hitelesítési ügynökök redundancia üzembe helyezéséhez.

#### <a name="on-premises-agents-management-recommended-reading"></a>A helyszíni ügynökök kezelése ajánlott olvasás

- [Az Azure AD alkalmazásproxy-összekötők megismerése](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors)
- [Azure AD áthaladási hitelesítés – rövid útmutató](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>Menedzsment nagyságrendben

### <a name="identity-secure-score"></a>Identitásbiztonság pontszám

Az [identitás biztonságos pontszám](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score) számszerűsíthető mértéke a szervezet biztonsági állapotát. Kulcsfontosságú, hogy folyamatosan felülvizsgálja és foglalkozzon a jelentett eredményeket, és arra törekszenek, hogy a lehető legmagasabb pontszámot. A pontszám az alábbiakban nyújt segítséget:

- Az identitásbiztonsági rendszer objektív felmérése
- Identitásbiztonsági fejlesztések tervezése
- A fejlesztések hatásának felmérése

![Biztonsági pontszám](./media/active-directory-ops-guide/active-directory-ops-img17.png)

Ha a szervezet jelenleg nem rendelkezik olyan programmal, amely az identitásbiztonságos pontszám változásait figyelheti, ajánlott egy tervet megvalósítani, és a tulajdonosokat hozzárendelni a fejlesztési műveletek figyeléséhez és végrehajtásához. A szervezeteknek a lehető leghamarabb ki kell javítaniuk a 30-nál nagyobb pontszámmal rendelkező fejlesztési műveleteket.

### <a name="notifications"></a>Értesítések

A Microsoft e-mailes üzeneteket küld a rendszergazdáknak, hogy értesítse a szolgáltatás különböző módosításait, a szükséges konfigurációs frissítéseket és a rendszergazdai beavatkozást igénylő hibákat. Fontos, hogy az ügyfelek úgy állítsa be az értesítési e-mail-címeket, hogy a rendszer értesítéseket küldjön a megfelelő csapattagoknak, akik minden értesítést nyugtázhatnak, és azok szerint cselekedhetnek. Azt javasoljuk, hogy több címzettet adjon hozzá az [Office 365 Üzenetközponthoz,](https://docs.microsoft.com/office365/admin/manage/message-center) és kérje, hogy értesítéseket (beleértve az Azure AD Connect Health-értesítéseket) küldjön egy terjesztési listára vagy megosztott postaládába. Ha csak egy globális rendszergazdai fiókkal rendelkezik e-mail címmel, ügyeljen arra, hogy legalább két e-mail-kompatibilis fiókot állítson be.

Az Azure AD két "Honnan" <o365mc@email2.microsoft.com>címet használ: , amely az Office 365 Üzenetközpont értesítéseit küldi; és <azure-noreply@microsoft.com>, amely a következőkre vonatkozó értesítéseket küldi:

- [Az Azure AD Access értékelései](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)
- [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations#enable-email-notifications)
- [Azure AD Identity Protection](/azure/active-directory/identity-protection/howto-identity-protection-configure-notifications)
- [Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)
- [Vállalati alkalmazáslejáró tanúsítványértesítések](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on#add-email-notification-addresses-for-certificate-expiration)
- Vállalati alkalmazáskiépítési szolgáltatás értesítések

Az alábbi táblázatból megtudhatja, hogy milyen típusú értesítéseket küld, és hol kell ellenőrizni őket:

| Értesítés iforrása | Az elküldés | Hol kell ellenőrizni |
|:-|:-|:-|
| Műszaki kapcsolat | Szinkronizálási hibák | Azure Portal – tulajdonságok panel |
| Office 365 üzenetközpont | Az Identity Services és az O365 háttérszolgáltatások incidens- és lebontási értesítései | Office-portál |
| Identity Protection Heti Digest | Identitásvédelmi kivonat | Az Azure AD Identity Protection panel |
| Azure AD Connect Health | Riasztási értesítések | Azure portal – Az Azure AD Connect Health panel |
| Vállalati alkalmazások értesítései | Értesítések a tanúsítványok lejáratáról és a kiépítési hibákról | Azure Portal – Vállalati alkalmazáspanel (minden alkalmazás saját e-mail-címbeállítással rendelkezik) |

#### <a name="notifications-recommended-reading"></a>Értesítések ajánlott olvasás

- [A szervezet címének, technikai kapcsolattartójának és egyebeknek a módosítása – Office 365](https://docs.microsoft.com/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>Működési felület

### <a name="ad-fs-lockdown"></a>AD FS zárolás

Szervezetek, amelyek konfigurálják az alkalmazásokat, hogy közvetlenül az Azure AD-n való hitelesítésre részesüljenek, az [Azure AD intelligens zárolásának](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)előnyeit élvezik. Ha az AD FS rendszert Windows Server 2012 R2 rendszerben használja, valósítsa meg az AD FS [extranet zároláselleni védelmét.](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection) Ha az AD FS-t Windows Server 2016-ban vagy újabb rendszeren használja, valósítsa meg [az extranetes intelligens zárolást.](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016) Legalább azt javasoljuk, hogy engedélyezze az extranet zárolást a helyszíni Active Directory elleni találgatásos támadások kockázatának visszaszorítása érdekében. Ha azonban a Windows 2016-ban vagy újabb verzióban rendelkezik AD FS szolgáltatással, engedélyeznie kell az extranetes intelligens zárolást is, amely segít a [jelszószórásos](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/) támadások csökkentésében.

Ha az AD FS csak az Azure AD-összevonáshoz használatos, vannak olyan végpontok, amelyek kikapcsolhatók a támadási felület területének minimalizálása érdekében. Ha például az AD FS szolgáltatást csak az Azure AD-hez használja, a **felhasználónév-vegyes** és **a windowsátvitelhez**engedélyezett végpontoktól eltérő WS-Trust végpontokat kell letiltania.

### <a name="access-to-machines-with-on-premises-identity-components"></a>Hozzáférés a helyszíni identitás-összetevőkkel rendelkező gépekhez

A szervezeteknek ugyanúgy le kell zárniuk a helyszíni hibrid összetevőkkel rendelkező gépekhez való hozzáférést, mint a helyszíni tartományban. Például egy biztonsági mentési operátor vagy a Hyper-V rendszergazda nem tud bejelentkezni az Azure AD Connect Server szabályok módosításához.

Az Active Directory felügyeleti réteg modelljét úgy tervezték, hogy a környezet teljes vezérlése (0. szint) és a támadók által gyakran feltört magas kockázatú munkaállomás-eszközök közötti pufferzónák használatával védje az identitásrendszereket. ![A többrétegű modell három rétegét bemutató ábra](./media/active-directory-ops-guide/active-directory-ops-img18.png)

A [rétegmodell](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) három szintből áll, és csak rendszergazdai fiókokat tartalmaz, nem általános jogú felhasználói fiókokat.

- **0. szint** – A vállalati identitások közvetlen vezérlése a környezetben. A 0. réteg olyan fiókokat, csoportokat és más elemeket tartalmaz, amelyek közvetlen vagy közvetett ellenőrzéssel rendelkeznek az Active Directory-erdő, -tartomány vagy -tartományvezérlő, valamint a bennük található valamennyi erőforrás felett. A 0. rétegbeli erőforrások biztonsági érzékenysége egyenértékű, ugyanis gyakorlatilag egymás ellenőrzése alatt állnak.
- **1. szint** – Vállalati kiszolgálók és -alkalmazások vezérlése. Az 1. réteg erőforrásai közé a kiszolgálói operációs rendszerek, a felhőszolgáltatások és a vállalati alkalmazások tartoznak. Az 1. rétegbeli rendszergazdai fiókok az ezeken az erőforrásokon található jelentős mennyiségű üzleti érték felett gyakorolnak ellenőrzést. Általános példaként említhetjük erre a kiszolgálói rendszergazdák szerepkörét, akik ezeket az operációs rendszereket karbantartják, és az összes vállalati szolgáltatásra hatással lehetnek.
- **2. szint** – A felhasználói munkaállomások és eszközök vezérlése. A 2. rétegbeli rendszergazdai fiókok a munkaállomásokon és eszközökön található jelentős mennyiségű üzleti érték felett gyakorolnak ellenőrzést. Példaként említhetők erre az ügyfélszolgálati és számítógép-támogatási rendszergazdák, hiszen ők szinte bármilyen felhasználói adat épségére hatással lehetnek.

A helyszíni identitás-összetevőkhöz, például az Azure AD Connecthez, az AD FS-hez és az SQL-szolgáltatásokhoz való hozzáférés zárolása ugyanúgy, mint a tartományvezérlőkesetében.

## <a name="summary"></a>Összefoglalás

A biztonságos identitás-infrastruktúra hét szempontból áll. Ez a lista segít megtalálni az Okat az Azure Active Directory (Azure AD) műveletek optimalizálásához szükséges műveleteket.

- Rendeljen tulajdonosokat a legfontosabb feladatokhoz.
- Automatizálja a helyszíni hibrid összetevők frissítési folyamatát.
- Az Azure AD Connect Health üzembe helyezése az Azure AD Connect és az AD FS figyeléséhez és jelentéséhez.
- A helyszíni hibrid összetevők állapotának figyelése az összetevő-ügynök naplóinak archiválásával és elemzésével a System Center Operations Manager vagy egy SIEM-megoldás használatával.
- A biztonsági fejlesztéseket az Identitás biztonságos pontszámával való biztonsági állapot mérésével valósíthatja meg.
- Zárják le az AD FS-t.
- A helyszíni identitás-összetevőkkel rendelkező gépekhez való hozzáférés zárolása.

## <a name="next-steps"></a>További lépések

Tekintse meg az [Azure AD üzembe helyezési tervek](active-directory-deployment-plans.md) megvalósítási részleteket minden olyan képességek, amelyek még nem telepített.
