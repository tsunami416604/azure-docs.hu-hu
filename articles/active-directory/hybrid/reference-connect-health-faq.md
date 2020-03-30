---
title: Azure Active Directory – Gyakori kérdések az állapothoz – Azure | Microsoft dokumentumok
description: Ez a gyIK megválaszolja az Azure AD Connect Health szolgáltatással kapcsolatos kérdéseket. Ez a GYIK a szolgáltatás használatával kapcsolatos kérdéseket fedi le, beleértve a számlázási modell, a képességek, a korlátozások és a támogatás témaköreit.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0c6484f46731e0ff2d16d00cb0038202511d193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331084"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Az Azure AD Connect Health gyakori kérdései
Ez a cikk az Azure Active Directory (Azure AD) Connect Health szolgáltatással kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza. Ezek a gyakori kérdések a szolgáltatás használatával kapcsolatos kérdéseket ismertetik, beleértve a számlázási modellt, a képességeket, a korlátozásokat és a támogatást.

## <a name="general-questions"></a>Általános kérdések
**K: Több Azure AD-könyvtárat kezelek. Hogyan válthatok az Azure Active Directory Premium szolgáltatással rendelkezőre?**

A különböző Azure AD-bérlők közötti váltáshoz válassza ki a jobb felső sarokban az aktuálisan bejelentkezett **felhasználónevet,** majd válassza ki a megfelelő fiókot. Ha a fiók nem szerepel itt, válassza a **Kijelentkezés**lehetőséget, majd használja annak a könyvtárnak a globális rendszergazdai hitelesítő adatait, amelyen engedélyezve van az Azure Active Directory Premium a bejelentkezéshez.

**K: Az Azure AD Connect Health támogatja az identitásszerepkörök melyik verzióját támogatja?**

Az alábbi táblázat a szerepköröket és a támogatott operációsrendszer-verziókat sorolja fel.

|Szerepkör| Operációs rendszer / verzió|
|--|--|
|Active Directory összevonási szolgáltatások (AD FS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | 1.0.9125-ös vagy újabb verzió|
|Active Directory tartományi szolgáltatások (AD DS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Vegye figyelembe, hogy a szolgáltatás által nyújtott szolgáltatások a szerepkörtől és az operációs rendszertől függően eltérőek lehetnek. Más szóval előfordulhat, hogy az összes szolgáltatás nem érhető el az összes operációs rendszer verzióhoz. A részleteket lásd a funkcióleírásokban.

**K: Hány licencre van szükségem az infrastruktúrám figyeléséhez?**

* Az első Connect Health Agent legalább egy Azure AD Premium-licencet igényel.
* Minden további regisztrált ügynökhöz 25 további Azure AD Premium-licenc szükséges.
* Az ügynökök száma megegyezik az összes figyelt szerepkör (AD FS, Azure AD Connect és/vagy AD DS) regisztrált ügynökök teljes számával.
* Az AAD Connect Health licencelése nem követeli meg, hogy a licencet adott felhasználókhoz rendelje. Csak a szükséges számú érvényes licenccel kell rendelkeznie.

A licencelési információk az [Azure AD díjszabási lapján](https://aka.ms/aadpricing)is megtalálhatók.

Példa:

| Bejegyzett ügynökök | Szükséges licencek | Példa figyelési konfigurációra |
| ------ | --------------- | --- |
| 1 | 1 | 1 Azure AD Connect kiszolgáló |
| 2 | 26| 1 Azure AD Connect kiszolgáló és 1 tartományvezérlő |
| 3 | 51 | 1 Active Directory összevonási szolgáltatások (AD FS) kiszolgáló, 1 AD FS-proxy és 1 tartományvezérlő |
| 4 | 76 | 1 AD FS-kiszolgáló, 1 AD FS-proxy és 2 tartományvezérlő |
| 5 | 101 | 1 Azure AD Connect-kiszolgáló, 1 AD FS-kiszolgáló, 1 AD FS-proxy és 2 tartományvezérlő |

**K: Támogatja az Azure AD Connect Health az Azure Germany Cloud-ot?**

Az Azure AD Connect Health nem támogatott a Germany Cloud szolgáltatásban, kivéve a [szinkronizálási hibák jelentési funkcióját.](how-to-connect-health-sync.md#object-level-synchronization-error-report)

| Szerepkörök | Szolgáltatások | Német felhőben támogatott |
| ------ | --------------- | --- |
| Az állapot összekapcsolása szinkronizáláshoz | Monitoring / Insight / Figyelmeztetések / Elemzés | Nem |
|  | Szinkronizálási hibajelentés | Igen |
| Az ADFS állapotának összekapcsolása | Monitoring / Insight / Figyelmeztetések / Elemzés | Nem |
| Az állapot összeadáshoz való csatlakoztatása | Monitoring / Insight / Figyelmeztetések / Elemzés | Nem |

A Connect Health szinkronizáláshoz szükséges ügynöki kapcsolatának biztosításához ennek megfelelően konfigurálja a [telepítési követelményt.](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints)

## <a name="installation-questions"></a>Telepítési kérdések

**K: Milyen hatással van az Azure AD Connect health Agent telepítése az egyes kiszolgálókra?**

A Microsoft Azure AD Connect Health Agent, Az AD FS, a webalkalmazás-proxykiszolgálók, az Azure AD Connect (szinkronizálási) kiszolgálók, a tartományvezérlők telepítésének hatása minimális a processzor, a memóriafelhasználás, a hálózati sávszélesség és a tárhely tekintetében.

A következő számok közelítést jelentenek:

* CPU-fogyasztás: ~1-5%-os növekedés.
* Memóriafelhasználás: A teljes rendszermemória akár 10 %-a.

> [!NOTE]
> Ha az ügynök nem tud kommunikálni az Azure-ral, az ügynök tárolja az adatokat helyileg egy meghatározott maximális korlát. Az ügynök felülírja a "gyorsítótárazott" adatokat a "legutolsóként szervizelt" alapon.
>
>

* Helyi puffertároló az Azure AD Connect állapotügynökei számára: ~20 MB.
* AD FS-kiszolgálók esetén azt javasoljuk, hogy 1024 MB (1 GB) lemezterületet létesítsen az AD FS naplózási csatornához az Azure AD Connect health Agents számára, hogy az összes naplózási adatot feldolgozza, mielőtt felülírná.

**K: Újra kell indítanom a kiszolgálóimat az Azure AD Connect állapotügynökök telepítése során?**

Nem. Az ügynökök telepítéséhez nem szükséges újraindítani a kiszolgálót. Egyes előfeltétel-lépések telepítése azonban a kiszolgáló újraindítását teheti szükségessé.

A Windows Server 2008 R2 rendszerben például a .NET 4.5 Framework telepítéséhez a kiszolgáló újraindítása szükséges.

**K: Működik az Azure AD Connect Health egy átmenő HTTP-proxyn keresztül?**

Igen. A folyamatban lévő műveletek, konfigurálhatja az állapotügynök egy HTTP-proxy a kimenő HTTP-kérelmek továbbítása.
További információ [a HTTP-proxy konfigurálásáról az állapotügynökök számára.](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)

Ha az ügynök regisztrációja során proxyt kell konfigurálnia, előfordulhat, hogy előzetesen módosítania kell az Internet Explorer proxybeállításait.

1. Nyissa meg az Internet Explorer > **Internetbeállítások** > **Internetbeállítások** > **– kapcsolatok** > **LAN-beállításait.**
2. Válassza **a Proxykiszolgáló használata a helyi hálózathoz**lehetőséget.
3. Válassza **a Speciális** lehetőséget, ha különböző proxyportokkal rendelkezik a HTTP és a HTTPS/Secure protokollhoz.

**K: Támogatja az Azure AD Connect Health alapszintű hitelesítést HTTP-proxykhoz való csatlakozáskor?**

Nem. Az egyszerű hitelesítéshez tetszőleges felhasználónév és jelszó megadására szolgáló mechanizmus jelenleg nem támogatott.

**K: Milyen tűzfalportokat kell megnyitni az Azure AD Connect health agent működéséhez?**

Tekintse meg a tűzfalportok listáját és az egyéb kapcsolódási követelményeket tartalmazó [követelményeket.](how-to-connect-health-agent-install.md#requirements)

**K: Miért jelenik meg két azonos nevű kiszolgáló az Azure AD Connect Health portálon?**

Amikor eltávolít egy ügynököt egy kiszolgálóról, a kiszolgáló nem törlődik automatikusan az Azure AD Connect Health portálról. Ha manuálisan eltávolít egy ügynököt egy kiszolgálóról, vagy magát a kiszolgálót, manuálisan kell törölnie a kiszolgálóbejegyzést az Azure AD Connect Health portalról.

Előfordulhat, hogy újraképez egy kiszolgálót, vagy létrehoz egy új kiszolgálót ugyanazzal a részlettel (például a számítógép nevével). Ha nem távolította el a már regisztrált kiszolgálót az Azure AD Connect health portálról, és telepítette az ügynököt az új kiszolgálóra, előfordulhat, hogy két bejegyzés jelenik meg ugyanazzal a névvel.

Ebben az esetben manuálisan törölje a régebbi kiszolgálóhoz tartozó bejegyzést. A kiszolgáló adatainak elavultnak kell lenniük.

## <a name="health-agent-registration-and-data-freshness"></a>Az egészségügyi ügynök regisztrálása és az adatok frissessége

**K: Milyen gyakori okai vannak az állapotfigyelő regisztrációs hibáinak, és hogyan háríthatók el a problémák?**

Az egészségügyi ügynök a következő lehetséges okok miatt sikertelenül regisztrálhat:

* Az ügynök nem tud kommunikálni a szükséges végpontokkal, mert a tűzfal blokkolja a forgalmat. Ez különösen gyakori a webalkalmazás-proxykiszolgálókon. Győződjön meg arról, hogy engedélyezte a kimenő kommunikációt a szükséges végpontokkal és portokkal. A [részleteket](how-to-connect-health-agent-install.md#requirements) lásd a követelmények részben.
* A kimenő kommunikációt a hálózati réteg TLS-vizsgálatnak veti alá. Ennek hatására az ügynök által használt tanúsítványt a felügyeleti kiszolgáló/entitás cseréli le, és az ügynök regisztrációjának befejezéséhez szükséges lépések sikertelenek.
* A felhasználó nak nincs hozzáférése az ügynök regisztrációjához. A globális rendszergazdák alapértelmezés szerint hozzáférhetnek. A [szerepköralapú hozzáférés-vezérléssel](how-to-connect-health-operations.md#manage-access-with-role-based-access-control) más felhasználók hozzáférését delegálhatja.

**K: Kapok figyelmeztetést, hogy "Az egészségügyi szolgáltatás adatai nem naprakészek." Hogyan háríthatók el a probléma?**

Az Azure AD Connect Health akkor hozza létre a riasztást, ha az elmúlt két órában nem kapja meg az összes adatpontot a kiszolgálóról. [Tovább](how-to-connect-health-data-freshness.md)- tovább .

## <a name="operations-questions"></a>Működési kérdések
**K: Engedélyeznem kell a naplózást a webalkalmazás proxykiszolgálóin?**

Nem, a naplózást nem kell engedélyezni a webalkalmazás-proxykiszolgálókon.

**K: Hogyan oldhatók meg az Azure AD Connect állapotriasztásai?**

Az Azure AD Connect health riasztások sikeres állapotban oldhatók meg. Az Azure AD Connect állapotügynökök észleli és rendszeresen jelentia sikeres feltételeit a szolgáltatásnak. Néhány riasztás esetén a letiltás időalapú. Más szóval, ha ugyanazt a hibafeltételt nem figyeli meg a riasztás generálásától számított 72 órán belül, a riasztás automatikusan megoldódik.

**K: Kapok figyelmeztetést, hogy "Teszt hitelesítési kérelem (szintetikus tranzakció) nem tudott megszerezni egy jogkivonatot." Hogyan háríthatók el a probléma?**

Az Azure AD Connect Health for AD FS akkor hozza létre ezt a riasztást, ha az AD FS-kiszolgálón telepített állapotügynök nem tud tokenbejutni az állapotügynök által kezdeményezett szintetikus tranzakció részeként. Az egészségügyi ügynök a helyi rendszer környezetében, és megpróbálja lekérni egy jogkivonatot egy saját falánk entitás. Ez egy catch-all teszt annak biztosítására, hogy az AD FS jogkivonatok kibocsátása állapotban van.

Ez a teszt leggyakrabban sikertelen, mert az állapotügynök nem tudja feloldani az AD FS farm nevét. Ez akkor fordulhat elő, ha az AD FS-kiszolgálók egy hálózati terheléselosztó mögött vannak, és a kérést egy olyan csomópontról kezdeményezi, amely a terheléselosztó mögött van (szemben a rendszeres ügyféllel, amely a terheléselosztó előtt van). Ez javítható a "C:\Windows\System32\drivers\etc" alatt található "hosts" fájl frissítésével, amely tartalmazza az AD FS-kiszolgáló IP-címét vagy az AD FS farm sts.contoso.com nevének visszacsatolási IP-címét (127.0.0.1). A gazdafájl hozzáadása rövidre zárja a hálózati hívást, így lehetővé teszi az egészségügyi ügynök számára a jogkivonat beírását.

**K: Kaptam egy e-mailt, amely jelzi, hogy a gépeim nem foltozottak a legutóbbi zsarolóprogramok támadásaihoz. Miért kaptam meg ezt az e-mailt?**

Az Azure AD Connect Health szolgáltatás ellenőrizte az összes általa figyelt gépet, hogy megbizonyosodjon aszükséges javítások telepítéséről. Az e-mailt a bérlői rendszergazdák küldték el, ha legalább egy gép nem rendelkezik a kritikus javításokkal. A meghatározáshoz a következő logikát használták.
1. Keresse meg a számítógépre telepített összes gyorsjavítást.
2. Ellenőrizze, hogy a definiált listából legalább egy gyorsjavítás található-e.
3. Ha igen, a gép védett. Ha nem, a gép veszélyben van a támadás.

Az ellenőrzés manuális végrehajtásához a következő PowerShell-parancsfájl t használhatja. A fenti logikát valósítja meg.

```powershell
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

**K: Miért a PowerShell-parancsmag <i>Get-MsolDirSyncProvisioningError</i> kevesebb szinkronizálási hibákat jelenít meg az eredményben?**

<i>A Get-MsolDirSyncProvisioningError</i> csak dirSync kiépítési hibákat ad vissza. Emellett a Connect Health portal más szinkronizálási hibatípusokat is megjelenít, például exportálási hibákat. Ez összhangban van az Azure AD Connect különbözeti eredményével. További információ az [Azure AD Connect Sync hibáiról.](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors)

**K: Miért nem jönnek létre az ADFS-auditok?**

Használja a <i>Get-AdfsProperties -AuditLevel</i> PowerShell-parancsmaggal annak biztosításához, hogy a naplónaplók nincsenek letiltva állapotban. További információ az [ADFS naplóiról](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016). Figyelje meg, ha speciális naplózási beállítások vannak az ADFS-kiszolgálóra, az auditpol.exe fájlban végrehajtott módosítások felülíródnak (esemény, ha az alkalmazás által létrehozott alkalmazás nincs konfigurálva). Ebben az esetben állítsa be a helyi biztonsági házirendet az alkalmazás által generált hibák és a siker naplózásához.

**K: Mikor lesz az ügyintézői tanúsítvány automatikus megújítása a lejárat előtt?**
Az ügynöki tanúsítvány automatikusan megújul **6 hónappal** a lejárati dátum előtt. Ha nem újítja meg, győződjön meg arról, hogy az ügynök hálózati kapcsolata stabil. Indítsa újra az ügynökszolgáltatásokat, vagy frissítsen a legújabb verzióra, ami szintén megoldhatja a problémát.


## <a name="related-links"></a>Kapcsolódó hivatkozások
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Az Azure AD Connect állapotügynök telepítése](how-to-connect-health-agent-install.md)
* [Az Azure AD Connect állapotműveletei](how-to-connect-health-operations.md)
* [Az Azure AD Connect Health használata az AD FS szolgáltatással](how-to-connect-health-adfs.md)
* [Az Azure AD Connect Health használata szinkronizáláshoz](how-to-connect-health-sync.md)
* [Az Azure AD Connect Health használata az AD DS szolgáltatással](how-to-connect-health-adds.md)
* [Az Azure AD Connect Health verzióelőzményei](reference-connect-health-version-history.md)
