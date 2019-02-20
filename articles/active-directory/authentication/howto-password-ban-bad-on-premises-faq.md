---
title: A helyszíni Azure AD-jelszó Protection – gyakori kérdések
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
ms.openlocfilehash: 8b29e4f6e6e877a3d03ded9edd9e282e0960e177
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414629"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Előzetes verzió: Az Azure AD jelszóvédelem helyszíni – gyakori kérdések

|     |
| --- |
| Az Azure AD jelszóvédelem az Azure Active Directory nyilvános előzetes verziójú funkció. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="general-questions"></a>Általános kérdések

**K: Amikor eléri az Azure AD jelszóvédelem általánosan elérhető (GA)?**

Általánosan elérhető (előtt március 2019 vége) Q1 CY2019 tervezünk. Köszönjük, hogy mindenki számára, aki a szolgáltatást, hogy a dátum - visszajelzés mellékelt Köszönjük azt!

**K: Milyen útmutatást felhasználók kell adni egy biztonságos jelszót kijelöléséhez?**

A Microsoft jelenlegi útmutatást ebben a témakörben tekinthet meg a következő hivatkozásra:

[Tájékoztató a jelszavakról Microsoft](https://www.microsoft.com/en-us/research/publication/password-guidance)

**K: Az a helyszíni Azure AD-jelszó védelmet támogatja a nem nyilvános felhők?**

A helyszíni Azure AD jelszóvédelem nem – csak akkor támogatott a nyilvános felhőben. Nincs dátum bejelentettük a nem nyilvános felhő rendelkezésre állás érdekében.

**K: Hogyan használhatom az Azure AD jelszóvédelem előnyeit a helyszíni felhasználók egy alhalmazára?**

Nem támogatott. Miután üzembe helyezte, és engedélyezve van, nem tesz különbséget az Azure AD jelszóvédelem – az összes felhasználó megkapja egyenlő biztonsági funkcióit.

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

A pontos lemezterület-használat nem kell pontosan meg, mert azt például számát és a letiltott jelszavak jogkivonatokat a Microsoft globális letiltott jelszavak lista hossza tényezőtől függ, és a bérlőnkénti egyéni le van tiltva, jelszó listája, valamint titkosítási terhelést. Ezek a listák tartalmát valószínűleg a jövőben nő. Vegye figyelembe, hogy ésszerű aktuális tapasztalatok, hogy a szolgáltatást kell-e legalább öt (5) MB lemezterületet a tartomány sysvol-megosztás.

**K: Miért van egy újraindítás szükséges, a tartományvezérlő ügynök szoftver telepítése vagy frissítése**

Ez a követelmény alapvető Windows viselkedés okozza.

**K: Van mód konfigurálása a tartományvezérlő-ügynök egy konkrét proxykiszolgálóval?**

Nem. Vegye figyelembe, hogy mivel a proxy server állapot nélküli, ez nem fontos mely adott proxykiszolgálót használ.

**K: Rendben van az üzembe helyezéséhez az Azure AD-jelszó védelme Proxy szolgáltatás-párhuzamos más szolgáltatásokkal, például az Azure AD Connect gépen?**

Igen. Az Azure AD-jelszó védelme Proxy szolgáltatás és az Azure AD Connect kell soha nem közvetlenül egymással ütköző.

**K: Kell fordítani a a teljesítményt a tartományvezérlők, ez a szolgáltatás üzembe helyezése a?**

Az Azure AD-jelszó Protection tartományvezérlő ügynökszolgáltatás célja, hogy a lehető leghatékonyabb legyen, és jelentősen nem érinti a meglévő Active Directory-környezet, amely már elég ellátva erőforrással tartományvezérlő teljesítményét.

Ez hasznos megjegyezni, hogy a legtöbb Active Directory telepítések jelszó-módosítási műveletek egy kis részét a teljes számítási feladatnak bármely adott tartományvezérlőn. Tegyük fel imagine 10000 felhasználói fiókokat az Active Directory-tartomány és a egy MaxPasswordAge szabályzatot a 30 nap. Átlagosan, tekintse meg ezt a tartományt 10000 – 30 = ~ 333 jelszó-módosítási műveletek minden nap, amely akár egyetlen tartományvezérlő műveletek kisebb száma. Hogyan lehet továbbítani rá egy lehetséges legrosszabb esetben a példát tegyük fel, hogy azokat egyetlen tartományvezérlőn jelszómódosítások elkészült, egy egyetlen órában ~ 333 (például ebben az esetben fordulhat elő, amikor az alkalmazottak minden nagy számú jár egy hétfő reggel ismét elindíthatja a munkahelyi); még ebben az esetben továbbra is láthatunk ~333/60 perc = / perc, amely újra nem komoly terhelést hat jelszó módosítására.

Az adott mondta Ha a jelenlegi tartományvezérlők már fut (például maximumot megállapodást Processzor, a szabad lemezterület, lemez I/O stb), a teljesítmény-korlátozott szinten lenne további tartományvezérlők telepítése és/vagy bontsa ki a rendelkezésre álló szabad célszerű hely, az Azure AD jelszóvédelem üzembe helyezése előtt. Sysvol lemezterület-használat a fenti kérdése fent is látható.

**K: Szeretném tesztelni az Azure AD jelszóvédelem néhány tartományvezérlők saját tartomány. Az esetleges kényszerítése a felhasználói jelszó módosítására ezen konkrét tartományvezérlők használatához?**

Nem. Amikor a felhasználó megváltoztatja a jelszavát, a tartományvezérlőn, használja a Windows ügyfél operációs rendszer Active Directory-hely és az alhálózati hozzárendelések, környezetspecifikus hálózati konfiguráció és egyéb tényezők alapján van kiválasztva. Az Azure AD jelszóvédelem nincs hatással, vagy ezek a tényezők szabályozhatja, és ezért nem tudja befolyásolni melyik tartományvezérlő van kiválasztva egy adott felhasználói jelszó módosítása.

Az adott mondta egy megközelítést, amely a cél szimuláló közel lenne, üzembe helyezése az Azure AD-jelszó védelmét az összes, a tartományvezérlő egy adott Active Directory-helyet. Ez a megközelítés ésszerűen széles körben használt lefedettséget biztosít az adott helyhez rendelt Windows ügyfél és így is a felhasználók számára, amely jelentkezik be ezeket az ügyfeleket és jelszavuk módosítása.

**K: Az Azure AD-jelszó Protection tartományvezérlő ügynök szolgáltatás telepítése a csak az elsődleges tartományvezérlőn (PDC), a tartomány minden tartományvezérlőjének is védi?**

Nem. Amikor egy felhasználó jelszavát egy adott, nem elsődleges tartományvezérlőn módosul, a tiszta szöveges jelszó sem küldjük el az elsődleges tartományvezérlő (ezt az elképzelést egy közös hibás érzete). Ehelyett után egy új jelszót adott tartományvezérlőn elfogadja, a DC ezt a jelszót használja, hogy a jelszó a különböző hitelesítési-protocol-specifikus kivonatok létrehozásához, majd továbbra is fennáll e kivonatokat a címtárban. A frissített kivonatok replikálja az elsődleges tartományvezérlő -, de az Azure AD jelszóvédelem nem vesz részt, az adott folyamatban. Felhasználói jelszavak előfordulhat, hogy bizonyos esetekben módosítható közvetlenül az elsődleges Tartományvezérlőt, újra függően különféle tényezők, például a hálózati topológia és az Active Directory tervezése. (Lásd az előző kérdéssel.)

Összefoglalásképpen: üzembe helyezés az Azure AD jelszó védelmi DC ügynök szolgáltatás, a PDC 100 %-os biztonsági lefedettség a funkció eléréséhez szükséges, de, amely önmagában nem biztosít semmilyen más tartományvezérlők a tartomány Azure AD jelszóvédelem biztonsági funkcióit.

**K: A System Center Operations Manager (SCOM) felügyeleti csomag érhető el az Azure AD jelszóvédelem?**

Nem.

## <a name="additional-content"></a>További tartalmat

Az alábbi hivatkozások nem részei az alapvető Azure AD jelszóvédelem dokumentáció, de lehet, hogy egy hasznos forrás a szolgáltatásról további információt.

[E-mail adathalász Protection útmutatója – 15. rész: A Microsoft Azure AD-jelszó szolgáltatás megvalósítása (helyszíni túl!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Az Azure AD jelszóvédelem és az intelligens zárolás, mostantól nyilvános előzetes verzió!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="next-steps"></a>További lépések

Ha kérdése van egy helyszíni Azure AD jelszóvédelem, amely itt nem válaszolt, küldje el az alábbi – Köszönjük, hogy egy visszajelzéshez!

[Azure AD jelszóvédelem üzembe helyezése](howto-password-ban-bad-on-premises-deploy.md)
