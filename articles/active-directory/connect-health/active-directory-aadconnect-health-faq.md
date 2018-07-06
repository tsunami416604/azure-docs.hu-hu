---
title: Az Azure Active Directory Connect Health – gyakori kérdések – Azure |} A Microsoft Docs
description: Ez a GYIK az Azure AD Connect Health kapcsolatos kérdésekre ad választ. Ez a GYIK a szolgáltatás használatával kapcsolatos kérdéseket fedi le, beleértve a számlázási modell, a képességek, a korlátozások és a támogatás témaköreit.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 957a68c80f9fcc07ef6f84b2b08f344745a58d95
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866024"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Azure AD Connect Health – gyakori kérdések
Ez a cikk az Azure Active Directory (Azure AD) Connect Health szolgáltatással kapcsolatos gyakori kérdések (GYIK) rájuk adott válaszokat tartalmazza. Ezek a gyakori kérdések a szolgáltatás, amely tartalmazza a számlázási modell, képességek, korlátozások és támogatás használatáról terjed ki.

## <a name="general-questions"></a>Általános kérdések
**Kérdés a több Azure AD-címtár kezeléséhez. Hogyan lehet váltani, amely rendelkezik az Azure Active Directory Premium?**

Váltás másik Azure AD-bérlőt, válassza ki a jelenleg bejelentkezett **felhasználónév** a jobb felső sarokban, és válassza a megfelelő fiókot. Ha a fiók nem szerepel itt, válassza ki **Kijelentkezés**, majd használja a globális rendszergazdai hitelesítő adatait a címtár, amely rendelkezik az Active Directory prémium szintű engedélyezve van a bejelentkezni.

**K: mely identitás szerepkörök az Azure AD Connect Health által támogatott verzióját?**

Az alábbi táblázat tartalmazza azokat a szerepköröket, és a támogatott operációsrendszer-verziók.

|Szerepkör| Operációs rendszer / verzió|
|--|--|
|Active Directory összevonási szolgáltatások (AD FS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | 1.0.9125 verzió vagy újabb|
|Active Directory Domain Services (AD DS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Vegye figyelembe, hogy a szolgáltatás által kínált szolgáltatásokkal a szerepkör és az operációs rendszer megfelelően változhatnak. Minden szolgáltatás más szóval nem lehet minden operációsrendszer-verzió érhető el. Tekintse meg a részleteket a szolgáltatások leírása.

**K: hány licenc van szükségem az infrastruktúra felügyeletéhez?**

* Az első Connect Health-ügynök legalább egy Azure AD Premium-licenc szükséges.
* Minden további regisztrált ügynökhöz 25 további Azure AD Premium-licencek van szükség.
* Ügynökök száma megegyezik az ügynökök, amelyek regisztrálva vannak az összes figyelt szerepkör (AD FS, az Azure AD Connect, illetve az AD DS) közötti teljes száma.
* AAD Connect Health-licencelés nem igényel licencet az adott felhasználókhoz rendel. Csak ki kell a megfelelő számú érvényes licenccel rendelkezik.

A licencelési adatokat is talál a [Azure AD díjszabási oldala](https://aka.ms/aadpricing).

Példa:

| Regisztrált ügynökök | Szükséges licencek | Példa figyelési konfiguráció |
| ------ | --------------- | --- |
| 1 | 1 | 1 azure AD Connect-kiszolgáló |
| 2 | 26| 1 azure AD Connect-kiszolgáló és a tartományvezérlő 1 |
| 3 | 51 | Az active Directory összevonási szolgáltatások (AD FS) 1 kiszolgáló, 1 AD FS-proxy és 1 tartományvezérlő |
| 4 | 76 | 1 AD FS-kiszolgálón, 1 AD FS-proxy és a tartományvezérlők 2 |
| 5 | 101 | 1 azure AD Connect-kiszolgáló, 1 AD FS-kiszolgálón, 1 AD FS-proxy és 2 tartományvezérlők |

**K: az Azure AD Connect Health-támogatás az Azure Germany Cloud?**

Az Azure AD Connect Health nem támogatott a Németországi Felhőhöz kivételével a [szinkronizálási hibák jelentés funkció](active-directory-aadconnect-health-sync.md#object-level-synchronization-error-report-preview). 

| Szerepkörök | Szolgáltatások | Támogatja a német felhőben |
| ------ | --------------- | --- |
| Connect Health szinkronizálási szolgáltatás | Figyelési / Insight / riasztásokat / elemzése | Nem |
|  | Szinkronizálási hibajelentés | Igen |
| Connect Health for AD FS | Figyelési / Insight / riasztásokat / elemzése | Nem |
| ADDS készült Connect Health | Figyelési / Insight / riasztásokat / elemzése | Nem |

Ahhoz, hogy az ügynök csatlakozását az Connect Health szinkronizálási szolgáltatás, állítsa be a [telepítési követelmény](active-directory-aadconnect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) ennek megfelelően.   

## <a name="installation-questions"></a>Telepítési kérdések

**K: Mi a hatását, hogy az Azure AD Connect Health-ügynök telepítése az egyes kiszolgálókon?**

A Microsoft Azure AD Connect Health Agent, az AD FS, webalkalmazás-proxy kiszolgálók, Azure AD Connect (sync) kiszolgálók telepítése a hatását, a tartományvezérlők minimális garanciát a CPU, a memóriahasználat, a hálózati sávszélesség és a storage.

A következő számokat közelítés:

* CPU-felhasználás: ~ 1 – 5 %-os növekedést.
* Memóriahasználat: 10 %-át megtakaríthatja a teljes rendszermemória.

> [!NOTE]
> Ha az ügynök nem tud kommunikálni az Azure-ral, az ügynök tárol helyben az adatokat egy meghatározott maximális korlátot. Az ügynök felülírja a "gyorsítótárazott" adatok "legrégebben szervizelt" alapon.
>
>

* Helyi puffer storage for Azure AD Connect Health-ügynököket: ~ 20 MB-ot.
* Az AD FS-kiszolgálók azt javasoljuk, hogy telepítsen egy (1 GB-os) 1 024 MB szabad lemezterület az Azure AD Connect Health-ügynököket a naplózási adatokat feldolgozni, mielőtt a rendszer felülírja azt az AD FS naplózási csatorna.

**K: kezelnem kell a kiszolgálók újraindítását az Azure AD Connect Health-ügynökök telepítése során?**

Nem. Az ügynökök telepítése nem szükséges, hogy indítsa újra a kiszolgálót. Néhány előfeltételként felsorolt lépéseket telepítését azonban szükség lehet a kiszolgáló újraindítását.

A Windows Server 2008 R2, például a .NET-keretrendszer 4.5-ös verziójának telepítése server újraindítást igényel.

**K: az Azure AD Connect Health munkahelyi csatlakoztatott HTTP proxyn keresztül?**

Igen. A folyamatban lévő műveletek konfigurálhatja a Health Agent egy HTTP-proxy használatával továbbítsa a kimenő HTTP-kérelmekre.
Tudjon meg többet [HTTP Proxy for Health-ügynökök konfigurálása](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Ha a proxy konfigurálása ügynök regisztrációja során van szüksége, szüksége lehet előre az Internet Explorer-Proxy beállításainak módosítására.

1. Nyissa meg az Internet Explorer > **beállítások** > **Internetbeállítások** > **kapcsolatok** > **LAN-beállítások** .
2. Válassza ki **proxykiszolgáló használata a helyi hálózaton**.
3. Válassza ki **speciális** Ha különböző proxyhoz HTTP és HTTPS/Secure rendelkeznek.

**K: az Azure AD Connect Health az alapszintű hitelesítés támogatása, amikor csatlakozik a HTTP proxyk?**

Nem. Adjon meg egy tetszőleges felhasználónevet és jelszót, az egyszerű hitelesítés jelenleg nem támogatott mechanizmussal.

**K: mely tűzfalportok van szükségem az Azure AD Connect Health-ügynök működik megnyitásához?**

Tekintse meg a [követelmények szakaszt](active-directory-aadconnect-health-agent-install.md#requirements) tűzfalportokkal és a többi kapcsolódási követelmények listáját.

**K: Miért látok két kiszolgálót ugyanazzal a névvel, az Azure AD Connect Health portálon?**

Ha az ügynök eltávolítása a kiszolgálóról, a kiszolgáló nem törlődik automatikusan az Azure AD Connect Health portálról. Ha manuálisan az ügynök eltávolítása a kiszolgálóról, vagy eltávolítja magát a kiszolgálót, manuálisan törölje a kiszolgáló bejegyzést az Azure AD Connect Health portálról szeretné.

Előfordulhat, hogy újból lemezképet létrehozni egy kiszolgálót, vagy hozzon létre egy új kiszolgálót a azonos adatokkal (például a számítógép neve). Ha az Azure AD Connect Health portálról nem távolította el a már regisztrált kiszolgáló, és telepítette az ügynököt az új kiszolgálón, két bejegyzés ugyanazzal a névvel jelenhet meg.

Ebben az esetben manuálisan törölje a bejegyzést, amely a korábbi kiszolgáló tartozik. Lehet, hogy az adatokat a kiszolgáló elavult.

## <a name="health-agent-registration-and-data-freshness"></a>Health ügynök regisztrációs és az adatok frissessége

**K: Mi a Health-ügynök regisztrációs hibák leggyakoribb okai és hogyan lehet elhárítani a problémákat?**

A health agent regisztrálása a következő lehetséges okok miatt meghiúsulhat:

* Az ügynök nem tud kommunikálni a szükséges végpontokat, mert egy tűzfal blokkolja a forgalmat. Ez a leggyakrabban a webalkalmazás-proxy kiszolgálókon. Győződjön meg arról, hogy engedélyezte-e a szükséges végpontokat és portokat a kimenő kommunikáció. Tekintse meg a [követelmények szakaszt](active-directory-aadconnect-health-agent-install.md#requirements) részleteiről.
* Kimenő kommunikáció által a hálózati réteg SSL-ellenőrzést van kitéve. Ennek hatására az ügynök által a hálózatfelügyeleti kiszolgáló/entitás helyébe a tanúsítványt, és a lépéseket az ügynök regisztrálása sikertelen lesz.
* A felhasználó nem rendelkezik az ügynök regisztrációja végezhetők el. A globális rendszergazdák alapértelmezés szerint hozzáféréssel rendelkezik. Használhat [szerepköralapú hozzáférés-vezérlés](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) más felhasználókkal való hozzáférés delegálására.

**Kérdés vagyok első riasztást kap, hogy "Állapotfigyelő szolgáltatás adatai nem naprakészek." Hogyan háríthatom el a problémát?**

Az Azure AD Connect Health a riasztást állít elő, ha azt nem kap az adatpontok a kiszolgálóról az elmúlt két órában. [További információk](active-directory-aadconnect-health-data-freshness.md).

## <a name="operations-questions"></a>Műveletek kérdések
**K: van szükségem a webalkalmazás-proxy kiszolgálók a naplózás engedélyezéséhez?**

Nem, naplózás nem kell engedélyezni kell a webalkalmazás-proxy kiszolgálókon.

**K: hogyan megfogalmazott hajtsa végre az Azure AD Connect Health-riasztások?**

Azure AD Connect Health-riasztások megfogalmazott sikerességi feltétel. Az Azure AD Connect Health-ügynököket észleli, és jelentse a sikerességi feltételek a szolgáltatás rendszeres időközönként. Néhány riasztások a Mellőzés ideje alapján. Más szóval ha ugyanez a hiba feltétel nem tartják be a riasztás előállítás 72 órán belül, a riasztás automatikusan megoldódott-e.

**Kérdés vagyok első riasztást kap, hogy "Test-hitelesítési kérelmet (szintetikus tranzakciónak) nem sikerült jogkivonatot beszereznie." Hogyan háríthatom el a problémát?**

Az Azure AD Connect Health for AD FS ezt a riasztást állít elő, ha az AD FS-kiszolgálóra telepíthető Health-ügynök nem sikerült jogkivonatot beszereznie a Health-ügynök által kezdeményezett szintetikus tranzakció részeként. A Health agent használja a helyi rendszer környezetében, és a egy jogkivonatot kapjon a függő entitás adatbázisreplika próbál. Ez a kevésbé a vizsgálat győződjön meg arról, hogy az AD FS jogkivonatok kiállításának állapotban van.

Általában ez a teszt meghiúsul, mert a Health-ügynök nem tudja feloldani az AD FS-farm neve. Ez akkor fordulhat elő, ha a kérelem egy csomópontról (ellentétben rendszeres ügyfél, amely a terheléselosztó elé) a terheléselosztó mögötti lekérdezi kezdeményezett, és az AD FS-kiszolgálók egy hálózati terheléselosztók mögött. Ez a "C:\Windows\System32\drivers\etc" az AD FS-kiszolgáló IP-címét és a egy visszacsatolási IP-cím (127.0.0.1) számára az AD FS-farm neve (például sts.contoso.com) alatt található "hosts" fájl frissítésével kell rögzíteni. Hozzáadása a gazdagép-fájlt fogja testzárlat a hálózati hívást, ezzel lehetővé téve a Health Agent beszerezni a jogkivonatot.

**K: kaptam egy e-mailt, jelezve, hogy a gép nem a legutóbbi ransomeware támadások tudjon fókuszálni. Miért kapott e-mailt?**

Az Azure AD Connect Health service vizsgálja a gépek figyeli annak biztosítása érdekében a szükséges javítások lettek telepítve az összes. Ha legalább egy gép nincs a kritikus javításokat az e-mailben küldött a bérlői rendszergazdák. A következő logikai ennek meghatározásához használt.
1. Keresse meg az összes gyorsjavítás telepítve van a gépen.
2. Ellenőrizze, hogy a gyorsjavítások a definiált listában legalább egy megtalálható.
3. Ha igen, a gép védelméhez. Ha nem, a gép a támadás veszélyének.

A következő PowerShell-parancsfájl segítségével manuálisan az ellenőrzés elvégzéséhez. A fenti logikai valósítja meg.

```
Function CheckForMS17-010 ()
{
    $hotfixes = "KB3205409", "KB3210720", "KB3210721", "KB3212646", "KB3213986", "KB4012212", "KB4012213", "KB4012214", "KB4012215", "KB4012216", "KB4012217", "KB4012218", "KB4012220", "KB4012598", "KB4012606", "KB4013198", "KB4013389", "KB4013429", "KB4015217", "KB4015438", "KB4015546", "KB4015547", "KB4015548", "KB4015549", "KB4015550", "KB4015551", "KB4015552", "KB4015553", "KB4015554", "KB4016635", "KB4019213", "KB4019214", "KB4019215", "KB4019216", "KB4019263", "KB4019264", "KB4019472", "KB4015221", "KB4019474", "KB4015219", "KB4019473"

    #checks the computer it's run on if any of the listed hotfixes are present
    $hotfix = Get-HotFix -ComputerName $env:computername | Where-Object {$hotfixes -contains $_.HotfixID} | Select-Object -property "HotFixID"

    #confirms whether hotfix is found or not
    if (Get-HotFix | Where-Object {$hotfixes -contains $_.HotfixID})
    {
        "Found HotFix: " + $hotfix.HotFixID
    } else {
        "Didn't Find HotFix"
    }
}

CheckForMS17-010

```

**K: Mi a PowerShell-parancsmag does <i>Get-MsolDirSyncProvisioningError</i> kevesebb szinkronizálási hibák jelennek meg az eredményt?**

<i>Get-MsolDirSyncProvisioningError</i> hibák DirSync csak adja vissza. Amellett, hogy Connect Health portálon is bemutatja, egyéb szinkronizálási alkalmazáshiba-típusok, például az exportálási hibák. Ez az az Azure AD Connect különbözeti eredményt. Tudjon meg többet [az Azure AD Connect-szinkronizálás hibáinak](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors).

**K: Miért van az AD FS naplózása nem hoz létre?**

PowerShell-parancsmagot <i>Get-AdfsProperties - AuditLevel</i> annak biztosítása érdekében a vizsgálati naplók nem a Letiltva állapot. Tudjon meg többet [AD FS-naplók](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016). Figyelje meg a rendszer speciális leküldött az ADFS-kiszolgáló naplózási beállításait, ha az auditpol.exe módosításokat lesz-e felülírt (esemény Ha alkalmazás létrehozott nincs konfigurálva). Ebben az esetben állítsa be a helyi biztonsági házirend alkalmazás létrehozott hibák, illetve sikeres-e bejelentkezni. 


## <a name="related-links"></a>Kapcsolódó hivatkozások
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Az Azure AD Connect Health-ügynök telepítése](active-directory-aadconnect-health-agent-install.md)
* [Az Azure AD Connect Health műveletei](active-directory-aadconnect-health-operations.md)
* [Az Azure AD Connect Health használata az AD FS szolgáltatással](active-directory-aadconnect-health-adfs.md)
* [Az Azure AD Connect Health szinkronizálási szolgáltatás használata](active-directory-aadconnect-health-sync.md)
* [Az Azure AD Connect Health használata az AD DS szolgáltatással](active-directory-aadconnect-health-adds.md)
* [Az Azure AD Connect Health verzióelőzményei](active-directory-aadconnect-health-version-history.md)
