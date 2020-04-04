---
title: Helyszíni Azure AD jelszóvédelem – gyakori kérdések
description: Az Azure AD password protection vel kapcsolatos gyakori kérdések áttekintése helyszíni Active Directory tartományi szolgáltatások környezetében
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16537ba72eb9f11abd8de47256150818c121a140
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652642"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Az Azure AD jelszavas védelme a helyszínen gyakori kérdések

Ez a szakasz az Azure AD jelszavas védelemmel kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

## <a name="general-questions"></a>Általános kérdések

**K: Milyen útmutatást kell adni a felhasználóknak a biztonságos jelszó kiválasztásához?**

A Microsoft jelenlegi útmutatója ebben a témában az alábbi linken található:

[Útmutató a Microsoft jelszóhoz](https://www.microsoft.com/research/publication/password-guidance)

**K: A helyszíni Azure AD password protection nem nyilvános felhőkben támogatott?**

Nem – a helyszíni Azure AD password protection csak a nyilvános felhőben támogatott. Nem jelentették be a nem nyilvános felhőelérhetőség dátumát.

Az Azure AD portál lehetővé teszi a helyszíni "Jelszavas védelem a Windows Server Active Directoryhoz" konfiguráció módosítását még nem nyilvános felhőkben is; az ilyen változások továbbra is fennállnak, de egyébként soha nem lépnek hatályba. A helyszíni proxyügynökök vagy erdők regisztrációja nem támogatott, ha nem nyilvános felhőbeli hitelesítő adatokat használnak, és az ilyen regisztrációs kísérletek mindig sikertelenek lesznek.

**K: Hogyan alkalmazhatom az Azure AD password protection előnyeit a helyszíni felhasználók egy részhalmazára?**

Nem támogatott. Üzembe helyezése után és engedélyezve az Azure AD password protection nem tesz különbséget – minden felhasználó egyenlő biztonsági előnyöket kap.

**K: Mi a különbség a jelszómódosítás és a jelszókészlet (vagy alaphelyzetbe állítás) között?**

A jelszómódosítás az, amikor a felhasználó új jelszót választ, miután bizonyítja, hogy ismeri a régi jelszót. A jelszómódosítása például az, ami akkor történik, amikor egy felhasználó bejelentkezik a Windows rendszerbe, és a rendszer új jelszó kiválasztását kéri.

A jelszókészlet (más néven jelszó-visszaállítás) az, amikor a rendszergazda lecseréli a fiók jelszavát egy új jelszóra, például az Active Directory – felhasználók és számítógépek felügyeleti eszközzel. Ez a művelet magas szintű jogosultságot igényel (általában tartományi rendszergazda), és a műveletet végző személy általában nem ismeri a régi jelszót. Az ügyfélszolgálati forgatókönyvek gyakran végeznek jelszókészleteket, például amikor segítséget nyújtanak egy felhasználónak, aki elfelejtette a jelszavát. Akkor is látni fogja a jelszókészlet-eseményeket, amikor egy teljesen új felhasználói fiókot hoz létre jelszóval.

A jelszóérvényesítési házirend ugyanúgy viselkedik, függetlenül attól, hogy jelszómódosítás vagy -készlet történik-e. Az Azure AD Password Protection DC Agent szolgáltatás különböző események naplózását végzi, hogy tájékoztassa, hogy jelszómódosítás vagy beállítási művelet történt-e.  Lásd: [Azure AD password protection figyelése és naplózása.](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor)

**K: Miért naplózzák a rendszer a duplikált jelszóelutasítási eseményeket, amikor gyenge jelszót próbál beállítani az Active Directory – felhasználók és számítógépek felügyeleti modullal?**

Az Active Directory – felhasználók és számítógépek felügyeleti beépülő modul először a Kerberos protokoll alja segítségével próbálja meg beállítani az új jelszót. Hiba esetén a beépülő modul egy második kísérletet tesz a jelszó örökölt (SAM RPC) protokoll használatával történő beállítására (a használt protokollok nem fontosak). Ha az Azure AD Password Protection gyengenek tekinti az új jelszót, ez a beépülő modul viselkedése a jelszó-visszaállítási elutasítási események két készletének naplózását eredményezi.

**K: Miért vannak az Azure AD password protection jelszóérvényesítési események naplózása egy üres felhasználónévvel?**

Az Active Directory támogatja a jelszó tesztelését annak ellenőrzésére, hogy megfelel-e a tartomány aktuális jelszó-összetettségi követelményeinek, például a [NetValidatePasswordPolicy](https://docs.microsoft.com/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) api használatával. Ha egy jelszót ilyen módon érvényesít, a tesztelés magában foglalja a jelszószűrő-dll alapú termékek, például az Azure AD password protection általi érvényesítést is , de a megadott jelszószűrő-dll-nek átadott felhasználónevek üresek lesznek. Ebben a forgatókönyvben az Azure AD password protection továbbra is érvényesíti a jelszót a jelenleg hatályos jelszóházirend használatával, és kiad egy eseménynapló-üzenetet az eredmény rögzítéséhez, azonban az eseménynapló-üzenet üres felhasználónév mezőket.

**K: Támogatja az Azure AD password protection telepítése más jelszószűrő-alapú termékekkel együtt?**

Igen. Több regisztrált jelszószűrő dll támogatása a Windows egyik alapvető szolgáltatása, és nem az Azure AD password protection-re jellemző. A jelszó elfogadása előtt minden regisztrált jelszószűrő dll-nek egyet kell értenie.

**K: Hogyan telepíthetem és konfigurálhatom az Azure AD password protection-t az Active Directory-környezetben az Azure használata nélkül?**

Nem támogatott. Az Azure AD password protection egy Azure-szolgáltatás, amely támogatja a helyszíni Active Directory-környezetbe való kiterjesztést.

**K: Hogyan módosíthatom a házirend tartalmát az Active Directory szintjén?**

Nem támogatott. A szabályzat csak az Azure AD portal használatával feladható. Lásd még az előző kérdést.

**K: Miért van szükség a DFSR-re a sysvol replikációhoz?**

Az FRS (a DFSR elődtechnológiája) számos ismert problémával rendelkezik, és a Windows Server Active Directory újabb verzióiban teljes mértékben nem támogatott. Az Azure AD password protection nulla tesztelése történik az FRS-konfigurált tartományokon.

További információkért kérjük, olvassa el az alábbi cikkeket:

[A sziszki replikáció dfsr-be történő áttelepítésének esete](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[A vége közel van az FRS-hez](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

Ha a tartomány még nem használja a DFSR-t, az Azure AD Password Protection telepítése előtt át kell telepítenie a DFSR használatához. További információt az alábbi linken talál:

[SYSVOL replikációs áttelepítési útmutató: FRS az elosztott fájlrendszer replikációs szolgáltatásához](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> Az Azure AD Password Protection DC Agent szoftver jelenleg telepíti a tartományvezérlők a tartományok, amelyek még mindig az FRS a sysvol replikáció, de a szoftver nem fog megfelelően működni ebben a környezetben. További negatív mellékhatások közé tartozik az egyes fájlok replikálása, és sysvol visszaállítási eljárások úgy tűnik, hogy sikerül, de csendben nem replikálja az összes fájlt. A dfsr által nyújtott előnyök, valamint az Azure AD password protection központi telepítésének feloldása érdekében a dfsr által irt. A szoftver jövőbeli verziói automatikusan lelesznek tiltva, ha olyan tartományban fut, amely még mindig FRS-t használ.

**K: Mennyi lemezterületet igényel a szolgáltatás a tartomány sysvol megosztás?**

A pontos helyhasználat változó, mivel olyan tényezőktől függ, mint például a Microsoft globális tiltólistájában szereplő tiltott tokenek száma és hossza, valamint a bérlőnkénti egyéni lista, valamint a titkosítási többletterhelés. Ezeknek a listáknak a tartalma a jövőben valószínűleg növekedni fog. Ezt szem előtt tartva, ésszerű elvárás, hogy a funkció lesz szükség legalább öt (5) megabájt helyet a domain sysvol részesedése.

**K: Miért van szükség újraindításra a tartományvezérlő-ügynök szoftverének telepítéséhez vagy frissítéséhez?**

Ezt a követelményt a Windows alapvető viselkedése okozza.

**K: Van-e mód arra, hogy egy tartományvezérlő-ügynököt egy adott proxykiszolgáló használatára konfiguráljon?**

Nem. Mivel a proxykiszolgáló állapot nélküli, nem fontos, hogy melyik proxykiszolgálót használja.

**K: Az Azure AD Password Protection Proxy szolgáltatás más szolgáltatásokkal, például az Azure AD Connecttel együtt telepíthető?**

Igen. Az Azure AD password protection proxy szolgáltatás és az Azure AD Connect soha nem ütközhet közvetlenül egymással.

Sajnos inkompatibilitás található az Azure AD Password Protection Proxy szoftver által telepített Microsoft Azure AD Connect Agent Updater szolgáltatás verziója és az [Azure Active Directory alkalmazásproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) szoftver által telepített szolgáltatás verziója között. Ez az inkompatibilitás azt eredményezheti, hogy az Agent Updater szolgáltatás nem tud kapcsolatba lépni az Azure-ral a szoftverfrissítésekért. Nem ajánlott telepíteni az Azure AD password protection proxy és az Azure Active Directory alkalmazásproxy ugyanazon a gépen.

**K: Milyen sorrendben kell telepíteni és regisztrálni a tartományvezérlő ügynököket és proxykat?**

A proxyügynök telepítésének, a tartományvezérlő-ügynök telepítésének, az erdőregisztrációnak és a proxyregisztrációnak a sorrendje támogatott.

**K: Aggódnom kell a miatt, hogy a tartományvezérlőknek a szolgáltatás üzembe helyezése nem éri el a teljesítményre vonatkozó találatot?**

Az Azure AD Password Protection DC Agent szolgáltatás nem befolyásolhatja jelentősen a tartományvezérlő teljesítményét egy meglévő, kifogástalan állapotú Active Directory-telepítés ben.

A legtöbb Active Directory központi telepítések jelszóváltozási műveletek egy kis részét a teljes munkaterhelés egy adott tartományvezérlő. Képzeljen el például egy 10000 felhasználói fiókkal rendelkező Active Directory-tartományt, és egy 30 napos MaxPasswordAge házirendet. Ez a tartomány naponta átlagosan 10000/30=~333 jelszómódosítási műveletet fog látni, amely akár egyetlen tartományvezérlő kisebb számú műveletét is jelenti. Fontolja meg a lehetséges legrosszabb forgatókönyv: tegyük fel, hogy ezek a ~ 333 jelszó változások egyetlen tartományvezérlőn végeztek egy óra alatt. Ez a forgatókönyv például akkor fordulhat elő, ha sok alkalmazott hétfőn reggel érkezik dolgozni. Még ebben az esetben is ~ 333/60 perc = hat jelszómódosítás percenként, ami szintén nem jelentős terhelés.

Ha azonban a jelenlegi tartományvezérlők már futnak a teljesítmény korlátozott szintjein (például a processzor, a lemezterület, a lemezterület stb.) tekintetében kiadva), a szolgáltatás telepítése előtt tanácsos további tartományvezérlőket hozzáadni vagy bővíteni a rendelkezésre álló lemezterületet. Lásd még a fenti kérdést a fenti sysvol lemezterület-használattal kapcsolatban.

**K: Szeretném tesztelni az Azure AD jelszavas védelem csak néhány tartományvezérlők a tartományomban. Lehet-e kényszeríteni a felhasználói jelszó módosításait az adott tartományvezérlők használatára?**

Nem. A Windows ügyféloperációsrendszer szabályozza, hogy melyik tartományvezérlőt használja a rendszer, amikor a felhasználó módosítja a jelszavát. A tartományvezérlő olyan tényezők alapján van kiválasztva, mint az Active Directory-hely és az alhálózati hozzárendelések, a környezetspecifikus hálózati konfiguráció stb. Az Azure AD Password Protection nem szabályozza ezeket a tényezőket, és nem befolyásolja, hogy melyik tartományvezérlő van kiválasztva a felhasználó jelszavának módosítására.

Ennek a célnak az egyik módja az Azure AD password protection üzembe helyezése egy adott Active Directory-hely összes tartományvezérlőjén. Ez a megközelítés ésszerű lefedettséget biztosít az adott helyhez rendelt Windows-ügyfelek, és így az ügyfelekbe bejelentkező és jelszavukat módosító felhasználók számára is.

**K: Ha csak az elsődleges tartományvezérlőre (PDC) telepítem az Azure AD Password Protection DC Agent szolgáltatást, a tartomány összes többi tartományvezérlője is védett lesz?**

Nem. Ha egy felhasználó jelszavát módosítja egy adott, nem elsődleges tartományvezérlőn, a rendszer soha nem küldi el a tiszta szöveges jelszót az elsődleges tartományvezérlőnek (ez az ötlet gyakori téves észlelés). Ha egy adott tartományvezérlőn új jelszót fogad el, a tartományvezérlő ezt a jelszót használja a jelszó különböző hitelesítési protokoll-specifikus kivonatainak létrehozásához, majd megőrzi ezeket a kivonatokat a címtárban. A tiszta szöveges jelszó nem marad meg. A frissített kibúnák ezután replikálódnak az elsődleges tartományvezérlőre. A felhasználói jelszavak bizonyos esetekben közvetlenül az elsődleges tartományvezérlőn módosíthatók, ismét különböző tényezőktől függően, például a hálózati topológiától és az Active Directory webhelykialakításától függően. (Lásd az előző kérdést.)

Összefoglalva, az Azure AD Password Protection DC-ügynök szolgáltatás üzembe helyezéséhez az elsődleges tartományvezérlő szükséges a szolgáltatás 100%-os biztonsági lefedettségének eléréséhez a tartományban. A szolgáltatás üzembe helyezése az elsődleges tartományvezérlőn csak nem biztosít az Azure AD password protection biztonsági előnyöket a tartomány bármely más tartományvezérlők számára.

**K: Miért nem működik az egyéni intelligens zárolás, még akkor is, ha az ügynökök telepítve vannak a helyszíni Active Directory környezetben?**

Az egyéni intelligens zárolás csak az Azure AD-ben támogatott. Az Azure AD-portál egyéni intelligens zárolási beállításainak módosításai nincsenek hatással a helyszíni Active Directory-környezetre, még az ügynökök telepítése esetén sem.

**K: Elérhető a System Center Operations Manager felügyeleti csomag az Azure AD Password Protection számára?**

Nem.

**K: Miért utasítja el az Azure AD továbbra is a gyenge jelszavakat annak ellenére, hogy úgy állítottam be a szabályzatot, hogy naplózási módban legyen?**

A naplózási mód csak a helyszíni Active Directory környezetben támogatott. Az Azure AD implicit módon mindig "kényszerítés" módban, amikor kiértékeli a jelszavakat.

**K: A felhasználók a hagyományos Windows hibaüzenetet látják, amikor az Azure AD Password Protection elutasítja a jelszót. Lehet-e testre szabni ezt a hibaüzenetet, hogy a felhasználók tudják, mi történt valójában?**

Nem. A felhasználók által a tartományvezérlő által elutasított jelszó esetén a felhasználók által látott hibaüzenetet nem a tartományvezérlő, hanem az ügyfélszámítógép vezérli. Ez a viselkedés akkor fordul elő, hogy a jelszót az alapértelmezett Active Directory jelszóházirendek vagy egy jelszószűrő-alapú megoldás, például az Azure AD password protection elutasítja.

## <a name="additional-content"></a>További tartalom

Az alábbi linkek nem részei az alapvető Azure AD Password Protection dokumentáció, de hasznos forrása lehet a funkció további információk.

[Az Azure AD password protection mostantól általánosan elérhető!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[E-mailes adathalászat-védelmi útmutató – 15. rész: A Microsoft Azure AD jelszóvédelmi szolgáltatásának megvalósítása (helyszíni használatra is!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Az Azure AD password protection és a Smart Lockout mostantól nyilvános előzetes verzióban érhető el!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Microsoft Premier\Unified support training available (Microsoft Premier\Unified support training available) (Microsoft Premier\Unified support training

Ha szeretne többet megtudni az Azure AD password protection és üzembe helyezése a környezetben, kihasználhatja a Microsoft proaktív szolgáltatás elérhető az ügyfelek számára a Premier vagy unified támogatási szerződéssel. A szolgáltatás neve Azure Active Directory: Jelszavas védelem. További információért forduljon a technikai ügyfélmenedzserhez.

## <a name="next-steps"></a>További lépések

Ha van egy helyszíni Azure AD password protection kérdés, amely nem válaszolt itt, küldje el a visszajelzési elem alatt - köszönöm!

[Azure AD jelszóvédelem üzembe helyezése](howto-password-ban-bad-on-premises-deploy.md)
