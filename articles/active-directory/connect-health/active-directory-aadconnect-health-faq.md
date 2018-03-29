---
title: Az Azure Active Directory Connect Health – gyakori kérdések – Azure |} Microsoft Docs
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
ms.openlocfilehash: 983c2c8aeb4c9d37213061dd70d6d64bae3de9d7
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Azure AD Connect Health – gyakori kérdések
Ez a cikk kapcsolatos gyakori kérdések (GYIK) Azure Active Directory (Azure AD) Connect Health rájuk adott válaszokat tartalmazza. Ezeket a gyakori kérdések a szolgáltatás, amely tartalmazza a számlázási modell, képességek, korlátozások és támogatás használatával kapcsolatos kérdésekre foglalkozik.

## <a name="general-questions"></a>Általános kérdések
**K: kezelhető több Azure AD-címtártól. Hogyan kapcsoló, amely rendelkezik az Azure Active Directory Premium?**

Váltás másik Azure AD bérlőre, válassza ki a jelenleg bejelentkezett **felhasználónév** a jobb felső sarokban, és válassza ki a megfelelő fiókot. Ha a fiók nem szerepel, válassza ki a **Kijelentkezés**, majd használja, amely az Active Directory prémium szintű Azure bejelentkezni engedélyezve van a címtár globális rendszergazdai hitelesítő adatokat.

**K: milyen identitás szerepkörök az Azure AD Connect Health által támogatott verzióját?**

A következő táblázat felsorolja azokat a szerepköröket, és a támogatott operációs rendszer verziója.

|Szerepkör| Operációs rendszer / verziója|
|--|--|
|Active Directory összevonási szolgáltatások (AD FS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | 1.0.9125 verzió vagy újabb|
|Active Directory Domain Services (AD DS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Vegye figyelembe, hogy a szolgáltatás által nyújtott szolgáltatásokat eltérőek lehetnek a szerepkör és az operációs rendszer alapján. Ez azt jelenti a szolgáltatások nem érhetők el az összes operációs rendszer verzió. Tekintse meg a részleteket a szolgáltatások leírása.

**K: hogyan hány licencet kell az infrastruktúra felügyeletéhez?**

* Az első Connect Health Agent legalább egy Azure AD Premium-licencre van szükség.
* Minden további regisztrált ügynök 25 további Azure AD Premium licenc szükséges.
* Ügynökök száma az összes figyelt szerepkör (AD FS, az Azure AD Connect, és/vagy az AD DS) közötti regisztrált ügynökök száma megegyezik.
* Az AAD Connect Health licencelési nem igényel a licenc az adott felhasználókhoz rendel. Csak kell a szükséges számú érvényes licenccel rendelkezik.

Licencelési információkat is megtalálható a [az Azure AD árazás lap](https://aka.ms/aadpricing).

Példa:

| Regisztrált ügynökök | Szükséges licencek | Példa figyelési konfiguráció |
| ------ | --------------- | --- |
| 1 | 1 | 1 az azure AD Connect-kiszolgáló |
| 2 | 26| Kiszolgáló. az azure AD Connect 1. és 1 tartományvezérlő |
| 3 | 51 | 1 active Directory összevonási szolgáltatások (AD FS) kiszolgálót, 1 AD FS proxy és 1 tartományvezérlő |
| 4 | 76 | 1 AD FS-kiszolgáló, az 1 AD FS proxy és a 2 tartományvezérlők |
| 5 | 101 | 1 az azure AD Connect-kiszolgáló, 1 AD FS-kiszolgáló, 1 AD FS proxy és 2 tartományvezérlők |

**K: az Azure AD Connect Health támogatási Azure Németország felhő?**

Az Azure AD Connect Health rendelkezik egy [telepítési](active-directory-aadconnect-health-agent-install.md) a Németországi Azure. Német felhő ügyfelek számára az adatok Azure Németország felhőben maradnak.


## <a name="installation-questions"></a>Telepítési kérdések

**K: Mi az az Azure AD Connect Health-ügynök telepítése az egyes kiszolgálókon hatását?**

A Microsoft Azure AD Connect Health Agent, a az AD FS, a webalkalmazás-proxy kiszolgálók, a kiszolgálók az Azure AD Connect (sync) telepítése a hatása, a tartományvezérlők minimális tekintetében a CPU, a memória, a hálózati sávszélesség és a tárolási.

A következő számokat közelítés:

* CPU-felhasználás: ~ 1-5 %-os növelését.
* Memória-felhasználás: a teljes rendszermemória legfeljebb 10 %.

> [!NOTE]
> Ha az ügynök nem tud kommunikálni az Azure-ral, az ügynök eltárolja a helyileg definiált felső korlátja. Az ügynök felülírja a "gyorsítótárazott" data "legrégebben kiszolgált" alapon.
>
>

* Helyi puffer tárhely az Azure AD Connect Health-ügynököket: ~ 20 MB-ot.
* Az AD FS-kiszolgáló azt javasoljuk, hogy egy, 1024 MB (1 GB) szabad lemezterület kiépítése az Azure AD Connect Health-ügynököket a naplóadatok feldolgozni, mielőtt a rendszer felülírja azt az AD FS-naplózási csatorna.

**K: el kell indítsa újra a kiszolgálók az Azure AD Connect Health-ügynökök telepítése során?**

Nem. Az ügynökök telepítésének nincs szükség a kiszolgáló újraindítására. Néhány előfeltételként szükséges lépéseket a telepítés azonban a kiszolgáló újraindítását lehet szükség.

A Windows Server 2008 R2, például a .NET-keretrendszer 4.5-ös verziójának telepítése egy kiszolgáló újraindítása szükséges.

**K: az Azure AD Connect Health munkahelyi csatlakoztatott HTTP proxyn keresztül?**

Igen. A folyamatban lévő műveletek beállíthatja a rendszerállapot-ügynöke egy HTTP-proxy segítségével kimenő HTTP-kérelmek továbbítása.
Tudjon meg többet az [Health-ügynököket HTTP-Proxy konfigurálása](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Ha a proxy konfigurálása az ügynök regisztrálása közben van szüksége, szükség lehet Internet Explorer proxybeállításainak előzetesen módosításához.

1. Nyissa meg az Internet Explorert > **beállítások** > **Internetbeállítások** > **kapcsolatok** > **LAN-beállítások**.
2. Válassza ki **proxykiszolgálót használni a helyi hálózaton**.
3. Válassza ki **speciális** Ha különböző proxyhoz HTTP és HTTPS/Secure rendelkeznek.

**K: az Azure AD Connect Health támogatási egyszerű hitelesítés HTTP proxyk történő csatlakozás során?**

Nem. Eljárást, amely egy tetszőleges felhasználónevet és jelszót adja meg az egyszerű hitelesítés jelenleg nem támogatott.

**K: milyen tűzfalportok kell az Azure AD Connect Health Agent működéséhez megnyitandó?**

Tekintse meg a [követelményeket ismertető részben](active-directory-aadconnect-health-agent-install.md#requirements) tűzfalportokat és egyéb hálózati kapcsolati követelményeinek listáját.

**K: Miért látom azt két kiszolgáló ezzel a névvel, az Azure AD Connect Health portálon?**

Ha az ügynök eltávolítása a kiszolgálóról, a kiszolgáló nem törlődik automatikusan az Azure AD Connect Health portálon. Ha manuálisan az ügynök eltávolítása a kiszolgálóról, vagy távolítsa el a magán a kiszolgálón, manuálisan törölje a kiszolgálót az Azure AD Connect Health portálon szeretné.

Előfordulhat, hogy újból lemezképet létrehozni egy kiszolgálót, vagy hozzon létre egy új kiszolgálót az azonos adatokkal (például a számítógép nevét). Ha nem távolította el a már regisztrált kiszolgálót az Azure AD Connect Health portálon, és az ügynök telepítése az új kiszolgálón, két bejegyzés azonos nevű jelenhet meg.

Ebben az esetben manuálisan törölje a bejegyzést, amely a régebbi verziójú kiszolgálón tartozik. Kell, hogy az adatokat a kiszolgáló elavult.

## <a name="health-agent-registration-and-data-freshness"></a>Health ügynök regisztrálása és az adatok frissesség

**K: Mi a rendszerállapot-ügynöke eszközregisztrációs hibák leggyakoribb oka, és hogyan problémák elhárításához?**

A rendszerállapot-ügynöke meghiúsulhat, ha regisztrálja a következő lehetséges okok miatt:

* Az ügynök nem tud kommunikálni a szükséges végpontok, mert egy tűzfal blokkolja a forgalmat. Ez a különösen közös a webalkalmazás-proxy kiszolgálók. Győződjön meg arról, hogy engedélyezte a kimenő kommunikációt a szükséges végpontokat és a portok. Tekintse meg a [követelményeket ismertető részben](active-directory-aadconnect-health-agent-install.md#requirements) részleteiről.
* Kimenő kommunikáció SSL-ellenőrzést a hálózati réteg van kitéve. Ennek hatására a tanúsítványt, amely az ügynök által a hálózatfelügyeleti kiszolgáló/entitás helyébe, ezért a lépések a az ügynök regisztrálása sikertelen.
* A felhasználó nem férhet hozzá az ügynök a regisztráció elvégzéséhez. Globális rendszergazdák alapértelmezés szerint rendelkezik hozzáféréssel. Használhat [szerepköralapú hozzáférés-vezérlés](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) hozzáférést biztosíthat más felhasználók számára.

**K: I vagyok első arra figyelmezteti, hogy "Állapotfigyelő szolgáltatás adatai nem naprakészek legyenek." Hogyan segítséget nyújthatnak a hiba kijavításához?**

Az Azure AD Connect Health riasztást küld, ha azt nem az adatpontok a kiszolgálóról fogadott az elmúlt két órában. [További információk](active-directory-aadconnect-health-data-freshness.md).

## <a name="operations-questions"></a>Műveletek kérdések
**K: kell engedélyeznie a naplózást a webalkalmazás-proxy kiszolgálók?**

Nem, naplózás nem kell engedélyezni kell a webalkalmazás-proxy kiszolgálók.

**K: hogyan tegye beolvasása feloldani az Azure AD Connect Health-riasztások?**

Az Azure AD Connect Health-riasztások lekérése Megoldva a Kifogástalan állapot. Az Azure AD Connect Health-ügynököket észleli, és rendszeres időközönként jelentse a sikerességi feltételek a szolgáltatásnak. A letiltásra néhány riasztások esetén időalapú. Ez azt jelenti Ha a ugyanazon hiba feltételek nem teljesülnek a riasztás előállítás 72 órán belül, a riasztás automatikusan megoldódik.

**K: I vagyok első arra figyelmezteti, hogy "vizsgálati hitelesítési kérelmet (szintetikus tranzakciónak) nem sikerült jogkivonat beszerzése." Hogyan segítséget nyújthatnak a hiba kijavításához?**

Az Azure AD Connect Health AD FS ezt a riasztást állít elő, ha a rendszerállapot-ügynöke telepítve az AD FS-kiszolgáló nem tud jogkivonat beszerzése a rendszerállapot-ügynöke által indított szintetikus tranzakció részeként. A rendszerállapot-ügynöke használja a helyi rendszer környezetében, és megkísérli egy függő entitás önaláírt jogkivonat lekérése. Ez az általános vizsgálat győződjön meg arról, hogy az AD FS jogkivonatok kiállításának állapotban van-e.

Általában ez a teszt sikertelen, mert a rendszerállapot-ügynöke nem tudja feloldani a az AD FS farm neve. Ez akkor fordulhat elő, ha a kérelem lekérdezi kezdeményezése csomópontot (ellentétben rendszeres ügyfél, amely a terheléselosztó elé) a terheléselosztó mögött van, és az AD FS-kiszolgáló egy hálózati egy terheléselosztó mögött. Ez is kijavítva, hogy a "gazdagép" fájl "C:\Windows\System32\drivers\etc" az AD FS-kiszolgáló IP-címét vagy egy visszacsatolási IP-címet (127.0.0.1) az AD FS farm nevét (például sts.contoso.com) alatt. A Hosts fájl hozzáadása fog testzárlat a hálózati hívás, így a rendszerállapot-ügynöke, a jogkivonat segítségével.

**K: jelent meg arról, hogy a gép nincs telepítve a legutóbbi ransomeware támadások e-mailt. Miért kapta meg az e-mailt?**

Az Azure AD Connect Health szolgáltatással beolvasott összes megtörtént-e a szükséges javítások biztosításához figyeli a gépek. Ha legalább egy számítógép nem rendelkezett a kritikus javításokat az e-mailben küldött a bérlői rendszergazdák. A következő logika ennek meghatározásához használt.
1. Megállapítja a számítógépen telepített összes gyorsjavítás.
2. Ellenőrizze, hogy a gyorsjavítások a definiált listában legalább egy megtalálható.
3. Ha igen, a gép védett. Ha nem, a gép a támadás veszélyének.

A következő PowerShell-parancsfájl segítségével manuálisan az ellenőrzés elvégzéséhez. A fenti logika valósítja meg.

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

**K: Miért does a PowerShell-parancsmag <i>Get-MsolDirSyncProvisioningError</i> kevesebb szinkronizálási hibák megjelenítése az eredményben?**

<i>Get-MsolDirSyncProvisioningError</i> kiépítési hibák DirSync csak adja vissza. Amellett, hogy a Connect Health portálon is látható más szinkronizálási exportálási hibák például típusok. Ez megfelel az Azure AD Connect különbözeti eredménye. Tudjon meg többet az [Azure AD Connect szinkronizálási hibák](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors).

**K: Miért van az AD FS naplózás nem generált?**

Használjon PowerShell-parancsmag <i>Get-AdfsProperties - AuditLevel</i> ellenőrizze a naplókat a rendszer nem letiltott állapot. Tudjon meg többet az [az AD FS-naplók](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016)


## <a name="related-links"></a>Kapcsolódó hivatkozások
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Az Azure AD Connect Health-ügynök telepítése](active-directory-aadconnect-health-agent-install.md)
* [Az Azure AD Connect Health műveletei](active-directory-aadconnect-health-operations.md)
* [Az Azure AD Connect Health használata az AD FS szolgáltatással](active-directory-aadconnect-health-adfs.md)
* [Az Azure AD Connect Health szinkronizálási szolgáltatás használata](active-directory-aadconnect-health-sync.md)
* [Az Azure AD Connect Health használata az AD DS szolgáltatással](active-directory-aadconnect-health-adds.md)
* [Az Azure AD Connect Health verzióelőzményei](active-directory-aadconnect-health-version-history.md)
