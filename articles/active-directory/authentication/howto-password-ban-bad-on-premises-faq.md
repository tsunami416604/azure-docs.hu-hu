---
title: A helyszíni Azure AD-jelszó alkalmazásvédelmi gyakori kérdések – az Azure Active Directory
description: A helyszíni Azure AD-jelszó Protection – gyakori kérdések
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8c3395345093ae9a3d35deb27a08f12d331c9f3
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58861904"
---
# <a name="azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Az Azure AD jelszóvédelem helyszíni – gyakori kérdések

## <a name="general-questions"></a>Általános kérdések

**K: Milyen útmutatást felhasználók kell adni egy biztonságos jelszót kijelöléséhez?**

A Microsoft jelenlegi útmutatást ebben a témakörben tekinthet meg a következő hivatkozásra:

[Tájékoztató a jelszavakról Microsoft](https://www.microsoft.com/en-us/research/publication/password-guidance)

**K: Az a helyszíni Azure AD-jelszó védelmet támogatja a nem nyilvános felhők?**

A helyszíni Azure AD jelszóvédelem nem – csak akkor támogatott a nyilvános felhőben. Nincs dátum bejelentettük a nem nyilvános felhő rendelkezésre állás érdekében.

**K: Hogyan használhatom az Azure AD jelszóvédelem előnyeit a helyszíni felhasználók egy alhalmazára?**

Nem támogatott. Miután üzembe helyezte, és engedélyezve van, az Azure AD jelszóvédelem nem tesz különbséget – az összes felhasználó megkapja egyenlő biztonsági funkcióit.

**K: Mi a különbség a jelszómódosítás és a egy jelszóbeállítási (vagy alaphelyzetbe állítása) között?**

A jelszó módosítása esetén a felhasználó általi új jelszó után igazolására, a régi jelszó rendelkeznek. Ez a példa, mi történik, ha a felhasználó bejelentkezik a Windows kéri majd, hogy egy új jelszót.

A jelszóbeállítási (más néven a jelszó alaphelyzetbe állítása) esetén, hogy egy rendszergazda cseréli a jelszót a fiókhoz egy új jelszót, például az Active Directory – felhasználók és számítógépek felügyeleti eszköz használatával. Ehhez a művelethez szükséges egy magas szintű jogosultságokra (általában a tartományi rendszergazda), és a művelet általában végző személy nem rendelkezik a régi jelszó ismerete. Ügyfélszolgálati forgatókönyvekhez gyakran ehhez, például amikor egy felhasználó elfelejtette a jelszavát, akinek támogatást nyújtanak. Jelszó beállítása eseményeket, amikor egy új felhasználói fiók létrehozása folyamatban van egy jelszóval először is megjelenik.

A jelszóházirend-ellenőrzés ugyanúgy működik, függetlenül attól, hogy a jelszó módosítása vagy a set történik-e. Az Azure AD-jelszó DC védelmi ügynök szolgáltatás különböző eseménynaplózás tájékoztatja, hogy a jelszó módosítása vagy a set művelet volt.  Lásd: [Azure AD jelszó-védelmi figyelés és naplózás](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**K: Támogatja az Azure AD-jelszó Protection tartalomcsomagjai egyéb jelszó-szűrő-alapú termékek telepítése?**

Igen. Több regisztrált jelszó szűrő DLL-ek egy olyan alapvető Windows szolgáltatás támogatása és a nem kifejezetten az Azure AD jelszóvédelem. Az összes regisztrált jelszó szűrő DLL-ek jelszó el kell fogadnia.

**K: Hogyan lehet üzembe helyezése és konfigurálása az Azure AD jelszóvédelem saját Active Directory-környezetben anélkül, hogy az Azure?**

Nem támogatott. Az Azure AD jelszóvédelem egy Azure-szolgáltatás, amely támogatja a kiterjesztendő egy helyszíni Active Directory-környezetbe.

**K: Hogyan módosíthatja a szabályzatot, az Active Directory szintjén tartalmát?**

Nem támogatott. A szabályzat csak az Azure AD felügyeleti portál használatával lehet felügyelni. Is megtekintheti az előző kérdéssel.

**K: Elosztott fájlrendszer-replikációs miért van szükség a sysvol replikáció?**

A fájlreplikációs szolgáltatás (az elosztott fájlrendszer-replikációs megelőző technológia) számos ismert problémák merülnek fel, és nem teljes mértékben támogatott a Windows Server Active Directory újabb verzióiban. A fájlreplikációs szolgáltatás használatára konfigurált tartományok nulla tesztelése az Azure AD jelszóvédelem történik.

További információkért tekintse meg a következő cikkeket:

[Az elosztott fájlrendszer-replikációs történő áttelepítés sysvol-replikáció eset](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[A végpont az FRS Nigh](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**K: Mennyi lemezterületet igényel a funkció a tartomány sysvol-megosztás a?**

A pontos lemezterület-használat változik, mivel azt például számát és a Microsoft globális kitiltott listáját és a bérlőnkénti egyéni lista, valamint titkosítási terhelést a kitiltott jogkivonatokat hossza tényezőtől függ. Ezek a listák tartalmát valószínűleg a jövőben nő. Vegye figyelembe, hogy a fordulni, hogy a szolgáltatást kell-e legalább öt (5) MB lemezterületet a tartomány sysvol-megosztás.

**K: Miért van egy újraindítás szükséges, a tartományvezérlő ügynök szoftver telepítése vagy frissítése**

Ez a követelmény alapvető Windows viselkedés okozza.

**K: Van mód konfigurálása a tartományvezérlő-ügynök egy konkrét proxykiszolgálóval?**

Nem. Mivel a proxy server állapot nélküli, akkor nem számít mely adott proxykiszolgálót használ.

**K: Rendben van az Azure AD-jelszó védelmi proxyszolgáltatás tartalomcsomagjai más szolgáltatások, például az Azure AD Connect telepítése?**

Igen. Az Azure AD-jelszó védelme Proxy szolgáltatás és az Azure AD Connect kell soha nem közvetlenül egymással ütköző.

**K: Milyen sorrendben kell a tartományvezérlő-ügynökök és a proxyk telepítését és regisztrálását?**

Bármely Proxy ügynök telepítése, ügynök telepítése DC, erdő regisztrációs és Proxy regisztrációs rendezése támogatott.

**K: Kell fordítani a a teljesítményt a tartományvezérlők, ez a szolgáltatás üzembe helyezése a?**

Az Azure AD-jelszó DC védelmi ügynök szolgáltatás nem szabad jelentős hatással vannak a tartományvezérlő teljesítményét a meglévő megfelelő Active Directory-környezet.

A legtöbb Active Directory telepítések jelszó-módosítási műveletek a teljes számítási feladatnak az egy adott tartományvezérlőt egy kis részét. Tegyük fel imagine 10000 felhasználói fiókokat az Active Directory-tartomány és a egy MaxPasswordAge szabályzatot a 30 nap. Átlagosan, tekintse meg ezt a tartományt 10000 – 30 = ~ 333 jelszó-módosítási műveletek minden nap, amely akár egyetlen tartományvezérlő műveletek kisebb száma. Fontolja meg egy lehetséges legrosszabb esetben: Tegyük fel, hogy azokat egyetlen tartományvezérlőn jelszómódosítások elkészült, egy egyetlen órában ~ 333. Ebben a forgatókönyvben például akkor is fordulhat, ha sok alkalmazott összes működését egy hétfő reggel ismét elindíthatja az származnak. Még ebben az esetben továbbra is láthatunk ~333/60 perc = / perc, amely újra nem komoly terhelést hat jelszó módosítására.

Azonban ha az aktuális tartományvezérlők már fut (például maximumot megállapodást Processzor, a szabad lemezterület, lemez I/O, stb.) a teljesítmény-korlátozott szinteken, célszerű adjon hozzá további tartományvezérlőket, vagy bontsa ki a szabad lemezterület, mielőtt Ez a szolgáltatás üzembe helyezése. Sysvol lemezterület-használat a fenti kérdése fent is látható.

**K: Szeretném tesztelni az Azure AD jelszóvédelem néhány tartományvezérlők saját tartomány. Az esetleges kényszerítése a felhasználói jelszó módosítására ezen konkrét tartományvezérlők használatához?**

Nem. A Windows ügyfél operációs rendszer szabályozza, hogy melyik tartományvezérlő használatos, amikor a felhasználó megváltoztatja a jelszavát. A tartományvezérlő van kiválasztva, például Active Directory-hely és az alhálózati hozzárendelések, környezetspecifikus hálózati konfiguráció és egyéb tényezők alapján. Az Azure AD jelszóvédelem nem szabályozza ezeket a tényezőket, és nem befolyásolja, melyik tartományvezérlő van kiválasztva, a felhasználó jelszavának módosítására.

Részlegesen elérni a cél egyik módja lehet a üzembe helyezése az Azure AD-jelszó védelmét az összes, a tartományvezérlő egy adott Active Directory-helyet. Ez a megközelítés ésszerű lefedettséget biztosít a Windows-ügyfelek, amelyek adott helyhez hozzárendelt, és így is a felhasználók számára, amely jelentkezik be ezeket az ügyfeleket és jelszavuk módosítása.

**K: Az Azure AD-jelszó Protection tartományvezérlő ügynök szolgáltatás telepítése a csak az elsődleges tartományvezérlőn (PDC), a tartomány minden tartományvezérlőjének is védi?**

Nem. Amikor egy felhasználó jelszavát egy adott, nem elsődleges tartományvezérlőn módosul, a tiszta szöveges jelszó sem küldjük el az elsődleges tartományvezérlő (ezt az elképzelést egy közös hibás érzete). Egy új jelszót adott tartományvezérlőn elfogadása után a DC ezt a jelszót használja, hogy a jelszó a különböző hitelesítési-protocol-specifikus kivonatok létrehozásához, és majd továbbra is fennáll e kivonatokat a címtárban. A tiszta szöveges jelszó nincs megőrizve. A frissített kivonatok replikálja az elsődleges Tartományvezérlőt. Felhasználói jelszavak előfordulhat, hogy bizonyos esetekben módosítható közvetlenül az elsődleges Tartományvezérlőt, újra függően különféle tényezők, például a hálózati topológia és az Active Directory tervezése. (Lásd az előző kérdéssel.)

Összefoglalva üzembe helyezés az Azure AD-jelszó DC védelmi ügynök szolgáltatás az elsődleges tartományvezérlő szükséges elérni a 100 %-os biztonsági lefedettség a funkció a tartományban. Az elsődleges tartományvezérlő szolgáltatás telepítésének csak nem biztosít az Azure AD jelszóvédelem biztonsági előnyöket bármely más tartományvezérlők a tartomány.

**K: A System Center Operations Manager felügyeleti csomag érhető el az Azure AD jelszóvédelem?**

Nem.

**K: Miért érdemes az Azure továbbra is visszautasítja a gyenge jelszavakat annak ellenére, hogy már konfiguráltam a házirend rendszervizsgálati módban kell?**

Vizsgálati üzemmód csak akkor támogatott a helyszíni Active Directory-környezetet. Azure, amely implicit módon mindig "kényszerítéséhez" mód jelszavak értékelésekor.

## <a name="additional-content"></a>További tartalmat

Az alábbi hivatkozások nem részei az alapvető Azure AD jelszóvédelem dokumentáció, de lehet, hogy egy hasznos forrás a szolgáltatásról további információt.

[Az Azure AD jelszóvédelem már általánosan elérhető!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[E-mail adathalász Protection útmutatója – 15. rész: A Microsoft Azure AD-jelszó szolgáltatás megvalósítása (helyszíni túl!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Az Azure AD jelszóvédelem és az intelligens zárolás, mostantól nyilvános előzetes verzió!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>A Microsoft Premier\Unified támogatási képzések érhető el

Ha érdeklik a nyújt az Azure AD jelszóvédelem alaposabb megismerésére, és üzembe helyezése a környezetében, kihasználhatja elérhető Microsoft proaktív szolgáltatás azoknak az ügyfeleknek a Premier szintű vagy egyesített támogatási szerződés. A szolgáltatás Azure Active Directory neve: Jelszavas védelem. További információért forduljon a műszaki Partnerkezelőjéhez.

## <a name="next-steps"></a>További lépések

Ha kérdése van egy helyszíni Azure AD jelszóvédelem, amely itt nem válaszolt, küldje el az alábbi – Köszönjük, hogy egy visszajelzéshez!

[Azure AD jelszóvédelem üzembe helyezése](howto-password-ban-bad-on-premises-deploy.md)
