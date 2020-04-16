---
title: Önkiszolgáló jelszó-visszaállítás – gyakori kérdések – Azure Active Directory
description: Gyakori kérdések az Azure AD önkiszolgáló jelszó-visszaállítással kapcsolatban
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1246b5b980f60c2f3e65aa5b32a7d79dd6efc7d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407163"
---
# <a name="password-management-frequently-asked-questions"></a>A jelszókezelés gyakori kérdései

Az alábbiakban feltehet néhány gyakori kérdést a jelszó-visszaállítással kapcsolatos összes kérdéssel kapcsolatban.

Ha általános kérdése van az Azure Active Directoryval (Azure AD) és az önkiszolgáló jelszó-visszaállítással (SSPR), amelyitt nem kapott választ, kérheti a közösség segítségét az [Azure AD fórumon.](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD) A közösség tagjai közé tartoznak a mérnökök, a termékmenedzserek, az MVP-k és az informatikai szakemberek.

Ez a GYIK a következő szakaszokra van felosztva:

* [Kérdések a jelszó-visszaállítás i.regisztrációjával kapcsolatban](#password-reset-registration)
* [Kérdések a jelszó alaphelyzetbe állításával kapcsolatban](#password-reset)
* [Kérdések a jelszó módosításával kapcsolatban](#password-change)
* [Kérdések a jelszókezelési jelentésekkel kapcsolatban](#password-management-reports)
* [Kérdések a jelszó-visszaírással kapcsolatban](#password-writeback)

## <a name="password-reset-registration"></a>Regisztráció új jelszó kéréséhez

* **K: Regisztrálhatják a felhasználóim saját jelszó-visszaállítási adataikat?**

  > **V:** Igen. Mindaddig, amíg a jelszó-visszaállítás engedélyezve van, és azokhttps://aka.ms/ssprsetup) licencelt, a felhasználók is megy a jelszó-visszaállítás regisztrációs portál (regisztrálni a hitelesítési adatokat. A felhasználók a hozzáférési panelen ( keresztül is regisztrálhatnak .https://myapps.microsoft.com) A hozzáférési panelen keresztül történő regisztrációhoz ki kell jelölnie a profilképét, válassza a **Profil**lehetőséget, majd válassza a **Regisztráció jelszó-visszaállításhoz** lehetőséget.
  >
  >
* **K: Ha engedélyezem a jelszó-visszaállítást egy csoporthoz, majd úgy döntök, hogy mindenki számára engedélyezem, a felhasználóimnak újra regisztrálniuk kell?**

  > **V.:** Nem. Azoka felhasználók, akik feltöltötték a hitelesítési adatokat, nem kötelesek újra regisztrálni.
  >
  >
* **K: Meg tudom határozni a jelszó-visszaállítási adatokat a felhasználóim nevében?**

  > **A.** Igen, ezt megteheti az Azure AD Connect, a PowerShell, az [Azure Portal](https://portal.azure.com)vagy a [Microsoft 365 felügyeleti központ](https://admin.microsoft.com)segítségével. További információ: [Az Azure AD önkiszolgáló jelszó-visszaállítás által használt adatok.](howto-sspr-authenticationdata.md)
  >
  >
* **K: Szinkronizálhatom az adatokat a biztonsági kérdésekhez a helyszíni környezetből?**

  > **A.** Nem, ez ma nem lehetséges.
  >
  >
* **K: Regisztrálhatnak a felhasználóim olyan módon, hogy más felhasználók ne lássák ezeket az adatokat?**

  > **V:** Igen. Amikor a felhasználók a jelszó-visszaállításregisztrációs portál használatával regisztrálnak adatokat, az adatok at magánhitelesítési mezőkbe menti a rendszer, amelyek csak a globális rendszergazdák és a felhasználó számára láthatók.
  >
  >
* **K: A felhasználókat regisztrálni kell ahhoz, hogy használhassák a jelszó-visszaállítást?**

  > **V.:** Nem. Ha elegendő hitelesítési információt ad meg a nevükben, a felhasználóknak nem kell regisztrálniuk. A jelszó-visszaállítás addig működik, amíg megfelelően formázta a címtár megfelelő mezőiben tárolt adatokat.
  >
  >
* **K: Szinkronizálhatom vagy beállíthatom a hitelesítési telefont, a hitelesítési e-mailt vagy a másodlagos hitelesítési telefonmezőket a felhasználóim nevében?**

  > **A.** A globális rendszergazda által beállítható mezőket az [SSPR-adatok követelményei](howto-sspr-authenticationdata.md)című cikk határozza meg.
  >
  >
* **K: Hogyan határozza meg a regisztrációs portál, hogy mely lehetőségek jelenjenek meg a felhasználók számára?**

  > **A.** A jelszó-visszaállítási regisztrációs portál csak azokat a beállításokat jeleníti meg, amelyeket a felhasználók számára engedélyezett. Ezek a beállítások a címtár **Konfigurálás** lapjának **Felhasználói jelszó-visszaállítási házirend** szakaszában találhatók. Ha például nem engedélyezi a biztonsági kérdéseket, akkor a felhasználók nem regisztrálhatnak erre a beállításra.
  >
  >
* **K: Mikor tekinthető regisztráltnak egy felhasználónak?**

  > **A.** Egy felhasználó akkor tekinthető regisztráltnak az SSPR-hez, ha legalább az Azure Portalon beállított jelszó **alaphelyzetbe állításához szükséges módszerek számát** [regisztrálta.](https://portal.azure.com)
  >
  >

## <a name="password-reset"></a>Új jelszó létrehozása

* **K: Megakadályozza, hogy a felhasználók rövid idő alatt többször is megpróbálják visszaállítani a jelszót?**

  > **A.** Igen, a jelszó-visszaállítás beépített biztonsági funkciók védik a visszaélésekkel szemben. 
  >
  > A felhasználók 24 órán belül csak öt jelszó-visszaállítási kísérletet próbálhatnak meg, mielőtt 24 órára zárolják őket. 
  >
  > A felhasználók megpróbálhatják érvényesíteni a telefonszámot, SMS-t küldeni, vagy a biztonsági kérdéseket és válaszokat csak öt alkalommal egy órán belül, mielőtt 24 órára zárolják őket. 
  >
  > A felhasználók 10 percen belül legfeljebb 10 alkalommal küldhetnek e-mailt, mielőtt 24 órára zárolják őket.
  >
  > A számlálók alaphelyzetbe állítása, ha a felhasználó visszaállítja a jelszavát.
  >
  >
* **K: Mennyi ideig kell várni, hogy megkapja az e-mail, SMS, vagy telefonhívást a jelszó-visszaállítás?**

  > **A.** Az e-maileknek, SMS-üzeneteknek és telefonhívásoknak egy percen belül meg kell érkezniük. A normál tok 5-20 másodperc.
  > Ha ebben az időszakban nem kapja meg az értesítést:
  > * Ellenőrizze a levélszemét mappát.
  > * Ellenőrizze, hogy a várt szám vagy e-mail a kívánt szám-e.
  > * Ellenőrizze, hogy a címtárban lévő hitelesítési adatok megfelelően vannak-e formázva, például +1 4255551234 vagy *felhasználói\@contoso.com*. 
* **K: Milyen nyelveket támogat a jelszó-visszaállítás?**

  > **A.** A jelszó-visszaállítási felhasználói felület, az SMS-üzenetek és a hanghívások az Office 365-ben támogatott nyelveken honosodnak.
  >
  >
* **K: A jelszó-visszaállítási élmény mely részei jelennek meg a címtár konfigurálási lapján lévő szervezeti márkajelzési elemek megbélyegezésekor?**

  > **A.** A jelszó-visszaállítási portál on a szervezet emblémáját jeleníti meg, és lehetővé teszi, hogy a "Forduljon a rendszergazdához" hivatkozást egyéni e-mailre vagy URL-címre. A jelszó-visszaállítással küldött e-mailek tartalmazzák a szervezet emblémáját, színeit és nevét az e-mail törzsében, és az adott név beállításaialapján vannak testreszabva.
  >
  >
* **K: Hogyan taníthatom meg a felhasználókat arról, hogy hová menjenek a jelszavuk visszaállításához?**

  > **A.** Próbálkozzon az [SSPR telepítési](howto-sspr-deployment.md#plan-communications) cikkében található javaslatokkal.
  >
  >
* **K: Használhatom ezt az oldalt mobileszközről?**

  > **A.** Igen, ez az oldal mobileszközökön is működik.
  >
  >
* **K: Támogatja a helyi Active Directory-fiókok zárolásának feloldását, amikor a felhasználók alaphelyzetbe állítják jelszavukat?**

  > **V:** Igen. Amikor egy felhasználó alaphelyzetbe állítja a jelszavát, ha a jelszó-visszaírás telepítve van az Azure AD Connecten keresztül, a rendszer automatikusan feloldja a felhasználó fiókját, amikor alaphelyzetbe állítja a jelszavát.
  >
  >
* **K: Hogyan integrálhatom a jelszó-visszaállítást közvetlenül a felhasználó asztali bejelentkezési élményébe?**

  > **A.** Ha Ön Azure AD Premium-ügyfél, további költségek nélkül telepítheti a Microsoft Identity Managert, és telepítheti a helyszíni jelszó-visszaállítási megoldást.
  >
  >
* **K: Különböző biztonsági kérdéseket állíthatok be a különböző területi beállításokhoz?**

  > **A.** Nem, ez ma nem lehetséges.
  >
  >
* **K: Hány kérdést állíthatok be a biztonsági kérdések hitelesítési beállításához?**

  > **A.** Legfeljebb 20 egyéni biztonsági kérdést konfigurálhat az [Azure Portalon.](https://portal.azure.com)
  >
  >
* **K: Mennyi ideig lehetnek a biztonsági kérdések?**

  > **A.** A biztonsági kérdések 3–200 karakter hosszúak lehetnek.
  >
  >
* **K: Mennyi ideig lehet a válasz a biztonsági kérdésekre?**

  > **A.** A válaszok 3–40 karakter hosszúak lehetnek.
  >
  >
* **K: A biztonsági kérdésekre adott ismétlődő válaszok elutasítva vannak?**

  > **A.** Igen, elutasítjuk a biztonsági kérdésekre adott ismétlődő válaszokat.
  >
  >
* **K: A felhasználó többször is regisztrálhatja ugyanazt a biztonsági kérdést?**

  > **V.:** Nem. Miután egy felhasználó regisztrál egy adott kérdést, nem tud másodszor is regisztrálni erre a kérdésre.
  >
  >
* **K: Be lehet-e állítani a biztonsági kérdések minimális korlátját a regisztrációhoz és az alaphelyzetbe állításhoz?**

  > **A.** Igen, az egyik korlát beállítható a regisztrációhoz, a másik pedig alaphelyzetbe állítás. A regisztrációhoz három-öt biztonsági kérdés re lehet szükség, és három-öt kérdés szükséges az alaphelyzetbe állításhoz.
  >
  >
* **K: Úgy állítottam be a szabályzatomat, hogy a felhasználóknak biztonsági kérdéseket kell használniuk az alaphelyzetbe állításhoz, de úgy tűnik, hogy az Azure-rendszergazdák másképp vannak konfigurálva.**

  > **A.** Ez a várt viselkedés. A Microsoft az új jelszó kérésére vonatkozó erős, alapértelmezett, két kapus szabályzat alkalmazását írja elő minden Azure rendszergazdai szerepkörhöz. Ez megakadályozza, hogy a rendszergazdák biztonsági kérdéseket használjanak. Erről a szabályzatról az [Azure Active Directory-cikkben](concept-sspr-policy.md) olvashat bővebben.
  >
  >
* **K: Ha egy felhasználó több kérdést regisztrált, mint az alaphelyzetbe állításhoz szükséges kérdések száma, hogyan kerülnek kiválasztásra a biztonsági kérdések az alaphelyzetbe állítás során?**

  > **Válasz:** *N* a biztonsági kérdések száma véletlenszerűen kerül kiválasztásra a felhasználó által regisztrált kérdések teljes száma közül, ahol *N* az az összeg, amely az **alaphelyzetbe állításhoz szükséges kérdések számához** van beállítva. Ha például egy felhasználó öt biztonsági kérdést regisztrált, de csak háromra van szükség a jelszó alaphelyzetbe állításához, az öt kérdés közül három véletlenszerűen kiválasztásra kerül, és az alaphelyzetbe állításkor jelenik meg. A kérdéskalapálás megakadályozása érdekében, ha a felhasználó rosszul kapja meg a választ a kérdésekre, a kiválasztási folyamat újraindul.
  >
  >
* **K: Mennyi ideig érvényesek az e-mail és az SMS egyszeri jelkódja?**

  > **A.** A jelszó-visszaállítás munkamenet-élettartama 15 perc. A jelszó-visszaállítási művelet kezdetétől a felhasználónak 15 percáll a jelszó visszaállítására. Az e-mail és az SMS egyszeri jelkódja 5 percig érvényes a jelszó-visszaállítási munkamenet során.
  >
  >
* **K: Letilthatom a felhasználóknak a jelszavuk visszaállítását?**

  > **A.** Igen, ha egy csoportot használ az SSPR engedélyezéséhez, eltávolíthat egy egyéni felhasználót acsoportból, amely lehetővé teszi a felhasználók számára a jelszó alaphelyzetbe állítását. Ha a felhasználó globális rendszergazda, akkor továbbra is képes visszaállítani a jelszavát, és ez nem tiltható le.
  >
  >

## <a name="password-change"></a>Jelszó módosítása

* **K: Hol kell a felhasználóknak módosítaniuk a jelszavukat?**

  > **A.** A felhasználók bárhol módosíthatják jelszavukat, ahol profilképüket vagy ikonjukat látják, például az [Office 365-portál](https://portal.office.com) vagy az [Access Panel](https://myapps.microsoft.com) felhasználói felületének jobb felső sarkában. A felhasználók a Hozzáférési [panel profillapjáról](https://account.activedirectory.windowsazure.com/r#/profile)módosíthatják jelszavukat. A felhasználók is kérhetik, hogy változtassa meg a jelszavakat automatikusan az Azure AD bejelentkezési oldalon, ha a jelszavak lejárt. Végül a felhasználók tallózhat az [Azure AD jelszómódosítási portál](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) közvetlenül, ha meg szeretné változtatni a jelszavakat.
  >
  >
* **K: Értesíthetők-e a felhasználók az Office-portálon, ha a helyszíni jelszavuk lejár?**

  > **A.** Igen, ez ma lehetséges, ha az Active Directory összevonási szolgáltatásokat (AD FS) használja. Ha AD FS-t használ, kövesse a [Jelszóházirend-jogcímek küldése az AD FS-sel](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) című cikk utasításait. Ha jelszókivonat-szinkronizálást használ, ez ma nem lehetséges. Nem szinkronizáljuk a helyszíni könyvtárak jelszóházirendjeit, így nem tudunk lejárati értesítéseket közzétenni a felhőbeli élményekben. Mindkét esetben a PowerShellen keresztül is értesítheti azokat a [felhasználókat, akiknek a jelszavai hamarosan lejárnak.](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx)
  >
  >
* **K: Letilthatom a felhasználóknak a jelszavuk megváltoztatását?**

  > **A.** Csak felhőalapú felhasználók esetében a jelszómódosítások nem tilthatók le. A helyszíni felhasználók számára beállíthatja, hogy a **Felhasználó nem módosíthatja** a jelszó beállítást a kiválasztott. A kijelölt felhasználók nem módosíthatják jelszavukat.
  >
  >

## <a name="password-management-reports"></a>Jelszókezelési jelentések

* **K: Mennyi ideig tart, amíg az adatok megjelennek a jelszókezelési jelentésekben?**

  > **A.** Az adatoknak 5–10 percen belül meg kell jelenniük a jelszókezelési jelentésekben. Bizonyos esetekben akár egy órát is igénybe vehet a megjelenés.
  >
  >
* **K: Hogyan szűrhetem a jelszókezelési jelentéseket?**

  > **A.** A jelszókezelési jelentések szűréséhez jelölje ki az oszlopcímkék jobb szélső oldalán található kis nagyítót a jelentés tetején. Ha gazdagabb szűrést szeretne végezni, letöltheti a jelentést az Excelbe, és létrehozhat egy kimutatástáblázatot.
  >
  >
* **K: Mi a jelszókezelési jelentésekben tárolt események maximális száma?**

  > **A.** A jelszókezelési jelentések legfeljebb 75 000 jelszó-visszaállítási vagy jelszó-visszaállítási regisztrációs eseményt tárolnak, amelyek akár 30 napig is visszanyúlnak. Azon dolgozunk, hogy ezt a számot további eseményekre is kiterjesszük.
  >
  >
* **K: Milyen messzire nyúlik vissza a jelszókezelési jelentések?**

  > **A.** A jelszókezelési jelentések az elmúlt 30 napban történt műveleteket jelenítik meg. Egyelőre, ha archiválnia kell ezeket az adatokat, rendszeresen letöltheti a jelentéseket, és külön helyre mentheti őket.
  >
  >
* **K: A jelszókezelési jelentésekben legfeljebb hány sor jelenhet meg?**

  > **V:** Igen. Legfeljebb 75 000 sor jelenhet meg bármelyik jelszókezelési jelentésben, függetlenül attól, hogy azok a felhasználói felületen jelennek meg vagy le töltődnek.
  >
  >
* **K: Van-e API a jelszó-visszaállítási vagy regisztrációs jelentési adatok eléréséhez?**

  > **V:** Igen. Ha többet szeretne megtudni arról, hogyan érheti el a jelszó-visszaállítási jelentési adatokat, tekintse meg az [Azure Log Analytics REST API-referencia.](/rest/api/loganalytics/)
  >
  >

## <a name="password-writeback"></a>Jelszóvisszaíró

* **K: Hogyan működik a jelszóvisszaírás a színfalak mögött?**

  > **A.** Tekintse meg a [Jelszó-visszaírás működése](howto-sspr-writeback.md) című cikket, amely ből megtudhatja, hogy mi történik, ha engedélyezi a jelszó-visszaírást, és hogyan jut vissza az adatok a rendszeren keresztül a helyszíni környezetbe.
  >
  >
* **K: Mennyi ideig tart a jelszóvisszaírás? Van-e szinkronizálási késleltetés, mint a jelszó kivonatoló szinkronizálása?**

  > **A.** A jelszóvisszaírás azonnali. Ez egy szinkron folyamat, amely alapvetően másképp működik, mint a jelszókivonat-szinkronizálás. A jelszóvisszaírás lehetővé teszi a felhasználók számára, hogy valós idejű visszajelzést kapjanak a jelszó-visszaállítási vagy -módosítási művelet ük sikeréről. A jelszó sikeres visszaírásának átlagos ideje 500 ms alatt van.
  >
  >
* **K: Ha a helyszíni fiókom le van tiltva, milyen hatással van a felhőfiókom és a hozzáférésem?**

  > **A.** Ha a helyszíni azonosító le van tiltva, a felhő-azonosító és a hozzáférés is le lesz tiltva a következő szinkronizálási időközaz Azure AD Connect. Alapértelmezés szerint ez a szinkronizálás 30 percenként történik.
  >
  >
* **K: Ha a helyszíni fiókomat egy helyszíni Active Directory jelszóházirend korlátozza, az SSPR betartja ezt a házirendet, amikor megváltoztatom a jelszavamat?**

  > **A.** Igen, az SSPR az Active Directory helyszíni jelszóházirendjere támaszkodik, és megfelel annak. Ez a házirend tartalmazza az Active Directory tartományi jelszóházirendet, valamint a felhasználót célzó definiált, részletes jelszóházirendeket.
  >
  >
* **K: Milyen típusú fiókok esetében működik a jelszó-visszaírás?**

  > **A.** A jelszó-visszaírás a helyszíni Active Directoryból az Azure AD-vel szinkronizált felhasználói fiókokhoz működik, beleértve az összevont, a jelszókivonat-szinkronizálást és az átadó autentication felhasználókat.
  >
  >
* **K: A jelszó-visszaírás kényszeríti a tartomány jelszóházirendjeit?**

  > **V:** Igen. A jelszóvisszaírás kort, előzményeket, összetettséget, szűrőket és minden egyéb korlátozást kényszerít ki a helyi tartományban lévő jelszavakra.
  >
  >
* **K: Biztonságos a jelszóvisszaírás?  Hogyan lehetek biztos benne, hogy nem törnek fel?**

  > **A.** Igen, a jelszóvisszaírás biztonságos. Ha többet szeretne megtudni a jelszó-visszaírási szolgáltatás által megvalósított többbiztonsági rétegről, olvassa el a [Jelszó visszaírásbiztonsági biztonsági](concept-sspr-writeback.md#password-writeback-security) szakaszát a [Jelszó visszaírását áttekintő](howto-sspr-writeback.md) cikkben.
  >
  >

## <a name="next-steps"></a>További lépések

* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Jelszó alaphelyzetbe állítása vagy módosítása](../user-help/active-directory-passwords-update-your-own-password.md)
* [Regisztráció az önkiszolgáló jelszó alaphelyzetbe állításához](../user-help/active-directory-passwords-reset-register.md)
* [Van engedélyezési kérdése?](concept-sspr-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](howto-sspr-authenticationdata.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](howto-sspr-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Azt hiszem, valami eltört. Hogyan háríthatók el az SSPR hibái?](active-directory-passwords-troubleshoot.md)
