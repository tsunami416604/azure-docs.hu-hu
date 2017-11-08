---
title: "Gyakran ismételt kérdések: Azure AD SSPR |} Microsoft Docs"
description: "Gyakori kérdések az Azure AD az önkiszolgáló jelszó alaphelyzetbe állítása"
services: active-directory
keywords: "Az Active directory-jelszókezelés, jelszókezelés, az Azure AD self service jelszó alaphelyzetbe állítása"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 14c565bb67480681e1d398a0a21a11448f405e4e
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="password-management-frequently-asked-questions"></a>A jelszókezelés gyakran ismételt kérdések

Az alábbiakban néhány gyakori kérdés a jelszó alaphelyzetbe állítása kapcsolódó van.

Ha az Azure AD általános kérdése van, és az önkiszolgáló jelszó alaphelyzetbe állítása, nem megválaszolandó itt, megkérheti a közösségi Ha segítségre van szüksége a a [Azure Ad-fórumok](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). A Közösség tagjai közé tartozik a mérnökök, termék kezelők, MVP és ösztöndíjas informatikai szakemberek számára.

Ez a GYIK a következő szakaszok oszlik:

* [**A Jelszóátállítás regisztrációját kapcsolatos kérdések**](#password-reset-registration)
* [**Jelszó alaphelyzetbe állítása kérdések**](#password-reset)
* [**A jelszó módosítása kapcsolatos kérdések**](#password-change)
* [**Tudnivalók a jelszókezelésről kérdések jelentések**](#password-management-reports)
* [**A jelszóvisszaírás kapcsolatos kérdések**](#password-writeback)

## <a name="password-reset-registration"></a>Jelszó-átállítási regisztrációk

* **K: a felhasználók regisztrálhatják saját jelszó alaphelyzetbe állítása adatokat?**

  > **V:** Igen, mindaddig, amíg engedélyezve van a jelszó alaphelyzetbe állítása, és azok licencét, akkor lépjen a jelszó-átállítási regisztráció portálon, a hitelesítési adataikat regisztrálni http://aka.ms/ssprsetup. A felhasználók is regisztrálhatják a hozzáférési panelre a http://myapps.microsoft.com címen, a profil lapon majd beállítás jelszó-átállítási regisztráció gombra kattintva.
  >
  >
* **K: jelszó alaphelyzetbe állítása adatok is definiálása a felhasználók nevében?**

  > **V:** Igen, akkor az Azure AD Connect, PowerShell, a [Azure-portálon](https://portal.azure.com), vagy az Office felügyeleti portálon. További információkért lásd: a cikk [az Azure AD önkiszolgáló jelszó-változtatási által használt adatok](active-directory-passwords-data.md).
  >
  >
* **K: biztonsági kérdéseket tesz fel a helyszíni adatok szinkronizálása?**

  > **V:** erre nincs lehetőség ma.
  >
  >
* **K: a felhasználók regisztrálhatják adatokat úgy, hogy más felhasználók nem láthatják ezeket az adatokat?**

  > **V:** Igen, ha a felhasználók regisztrálhatnak az adatokat, és a jelszó-változtatási regisztrációs portálra a Mentés személyes hitelesítési mezőkbe, amelyek csak a globális rendszergazdák és a felhasználó által látható.
  >
  >
* **K: a felhasználók rendelkeznek regisztrálni kell a jelszó alaphelyzetbe állítása használatához?**

  > **V:** nem, a nevében elég hitelesítési adatainak megadása esetén felhasználóknak nem kell regisztrálni. Jelszó-átállítási működik, amíg a megfelelően formázott a megfelelő mezőket a könyvtárban tárolt adatokat.
  >
  >
* **K: I szinkronizálása vagy a hitelesítéshez megadott telefonját, a hitelesítési e-mailben vagy alternatív hitelesítéshez megadott telefonját mezők beállítása a felhasználók nevében?**

  > **V:** erre nincs lehetőség ma.
  >
  >
* **K: hogyan nem a regisztrációs portálon, hogy mely beállítások megjelenítése a felhasználók számára?**

  > **V:** a jelszó-visszaállítási portál csak lehetőségeket mutatja be, hogy a felhasználók számára engedélyezett. Ezek a beállítások a könyvtár konfigurálása lap felhasználói jelszó-visszaállítási házirend szakasza alatt találhatók. Például ez azt jelenti, hogy ha nem engedélyezi a biztonsági kérdéseket, majd felhasználók képesek nem regisztrálja az adott beállítási mód.
  >
  >
* **Amikor egy felhasználó tekinthető k: regisztrálni?**

  > **V:** regisztrált sspr, amikor regisztrálják azokat A felhasználó akkor tekinthető legalább a **számos módszer alaphelyzetbe kell állítani** beállított a [Azure-portálon](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Új jelszó létrehozása

* **K: mennyi ideig várjon egy e-mailben, SMS vagy telefonhívás fogadása jelszó alaphelyzetbe állítása?**

  > **V:** e-mailben, SMS-t, és telefonhívásokat egy perc alatt 5-20 másodperc normál esetben a kell érkeznek.
    >Ha nem jelenik meg az értesítés a időkereten belül:
        > * Ellenőrizze a Levélszemét mappát.
        > * Ellenőrizze a vagy e-mail, amelyhez csatlakozik egy várt.
        > * Ellenőrizze, hogy a címtárban a hitelesítési adatok megfelelően van formázva.
                >     * Példa: "+ 1 4255551234" vagy "user@contoso.com"
  >
  >
* **K: jelszó alaphelyzetbe állítása milyen nyelveket támogatja?**

  > **V:** a jelszó-visszaállítási felhasználói felület, SMS-t, és hanghívások honosítva vannak az Office 365-ben támogatott nyelven.
  >
  >
* **A jelszó alaphelyzetbe állítása élmény részeinek első vállalati arculattal, szervezeti a címtárban branding meg k: tartozó konfigurálása lapon?**

  > **V:** a jelszó-változtatási portál jeleníti meg a szervezeti embléma, és lehetővé teszi az ügyfél konfigurálása a rendszergazda hivatkozás egy egyéni e-mail vagy URL-CÍMRE mutasson. Minden e-mailben küldi el jelszóvisszaállítás tartalmazza a vállalati embléma, a színeket, a neve az e-mail törzsében, és testre szabott neve.
  >
  >
* **K: hogyan lehet ismertetni kell a felhasználók visszaállíthassák a jelszavukat önállóan hol találhatnak kapcsolatban?**

  > **V:** próbáljon ki a javaslatok a [SSPR telepítési cikk](active-directory-passwords-best-practices.md#email-based-rollout)
  >
  >
* **K: használhatok ezen a lapon egy mobileszközről?**

  > **V:** Igen, az ezen a lapon a mobileszközök működik.
  >
  >
* **K: támogatják a feloldásának helyi active directory-fiókokat, amikor a felhasználók visszaállíthassák a jelszavukat?**

  > **V:** Igen, ha a felhasználó visszaállítja a jelszavát, és a jelszóvisszaíró az Azure AD Connect használatával lett telepítve, adott felhasználói fiók esetén automatikusan feloldani a jelszavát.
  >
  >
* **K: hogyan integrálhatók a jelszó alaphelyzetbe állítása közvetlenül a saját felhasználói asztali bejelentkezés során tapasztal élmény?**

  > **V:** Ha az Azure AD Premium felhasználóinál, telepítse a Microsoft Identity Manager további költségek nélkül, és ez a követelmény teljesítéséhez a helyszíni jelszó alaphelyzetbe állítása megoldás üzembe helyezéséhez.
  >
  >
* **K: beállítása a különböző területi beállításokhoz különböző biztonsági kérdést?**

  > **V:** erre nincs lehetőség ma.
  >
  >
* **K: hogyan több kérdést azt a állíthatja be a biztonsági kérdések hitelesítési lehetőséget?**

  > **V:** legfeljebb 20 egyéni biztonsági kérdéseket is beállíthat a [Azure-portálon](https://portal.azure.com).
  >
  >
* **K: mennyi ideig lehet, hogy a biztonsági kérdések kell?**

  > **V:** biztonsági kérdések 3 és 200 karakter közötti lehet.
  >
  >
* **K: mennyi ideig biztonsági kérdésekre adott válaszok lehet?**

  > **V:** válaszok 3-40 karakter hosszú lehet.
  >
  >
* **K: ismétlődő válaszokat ad a biztonsági kérdésekre elutasított vannak?**

  > **V:** Igen, azt utasítsa el az ismétlődő válaszokat ad a biztonsági kérdésekre.
  >
  >
* **K: Előfordulhat, hogy a felhasználó regisztrálása a ugyanazt biztonsági kérdésre adott egynél többször?**

  > **V:** nem, miután a felhasználó regisztrál egy adott kérdést, előfordulhat, hogy nem regisztrálják az adott kérdést, még egyszer.
  >
  >
* **K: van lehetőség a minimális addig, amíg a regisztráció biztonsági kérdések, és alaphelyzetbe állítja?**

  > **V:** Igen, egy korlát a regisztrációs és egy másik alaphelyzetbe állítása a állítható be. lehet, hogy 3-5 biztonsági kérdések regisztrációjához szükséges, és lehet, hogy 3-5 alaphelyzetbe állítása szükséges.
  >
  >
* **K: beállítása a házirendet a biztonsági kérdések használata alaphelyzetbe állítása felhasználóknak kötelező legyen, de az Azure a rendszergazdák úgy tűnik, hogy eltérően kell konfigurálni.**

  > **V:** Ez az elvárt működés. A Microsoft érvényesíti egy erős alapértelmezett két kapu jelszó-visszaállítási házirend bármely Azure rendszergazdai szerepkörhöz. Ez letiltja a rendszergazdák a biztonsági kérdések használata. Ezzel a házirend-további információ található a cikkben [jelszóházirendek és -korlátozások az Azure Active Directoryban](active-directory-passwords-policy.md#administrator-password-policy-differences)
  >
  >
* **K:, ha a felhasználó regisztrálva van a több maximális kérdések alaphelyzetbe kell állítani, hogyan vannak a biztonsági kérdések kijelölt alaphelyzetbe állítása során?**

  > **V:** N biztonsági kérdések véletlenszerűen kiválasztott felhasználó regisztrálva van, ahol N az kérdések száma kívül a **alaphelyzetbe kell állítani kérdések számát**. Például ha egy felhasználó rendelkezik-e regisztrálva 5 biztonsági kérdéseit, de csak 3 szükséges alaphelyzetbe állítása, az 5 3 véletlenszerűen és jelenik meg a következő alaphelyzetbe állítása. Ha a felhasználó élvezheti a kérdésekre adott válaszai nem megfelelő, a tanúsítványkiválasztási folyamat nem szűnik meg kérdés beütés megelőzése érdekében.
  >
  >
* **K: el azt, hogy felhasználók jelszó-változtatási rövid időn belül időn belül többször próbálnak?**

  > **V:** Igen, vannak beépítve a jelszó alaphelyzetbe állítása visszaélés elleni védelme érdekében biztonsági funkciók. Előfordulhat, hogy a felhasználók csak megpróbálják 5 jelszó alaphelyzetbe állítása kísérletek 24 óra alatt zárolása előtt egy órán belül. Felhasználók csak próbálkozzon 5 alkalommal 24 óra alatt zárolása előtt egy órán belül egy telefonszámot érvényesítéséhez. A felhasználók csak lehet, hogy megpróbálják egy egyetlen hitelesítési módszer 24 óra alatt zárolása előtt egy órán belül 5 alkalommal.
  >
  >
* **K:, hogy mennyi ideig érvényesek az e-mailek és SMS egyszer használatos jelszót?**

  > **V:** a munkamenetek élettartamát, jelszó-visszaállításhoz érték 15 perc. A jelszó-visszaállítási művelet kezdetétől a felhasználó rendelkezik-e jelszavuk 15 perc. Az e-mailek és SMS egyszer használatos jelszót érvénytelenek, ebben az időszakban lejárata után is.
  >
  >
* **K: a jelszó alaphelyzetbe állításával felhasználók megakadályozása?**

  > **V:** Igen, ha egy csoportot használja az önkiszolgáló jelszó-visszaállítás engedélyezése eltávolíthatja azokat a csoportból, amellyel ez a lehetőség.
  >
  >

## <a name="password-change"></a>A jelszó módosítása

* **K: hol kell nyissa meg a felhasználók a jelszavuk módosítása?**

  > **V:** felhasználók előfordulhat, hogy módosítsák jelszavukat bárhol akkor jelenik meg a profil kép vagy ikon (felső sarkában, például a [Office 365](https://portal.office.com) vagy [hozzáférési Panel](https://myapps.microsoft.com) észlel. Felhasználók módosíthatja a jelszavukat a a [hozzáférési Panel profilszerkesztési lap](https://account.activedirectory.windowsazure.com/r#/profile). Felhasználók is kérheti a jelszavukat, automatikusan az Azure AD bejelentkezési képernyő, ha a jelszavuk. Végezetül felhasználók előfordulhat, hogy keresse meg a [Azure AD-jelszó módosítása Portal](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) közvetlenül történő jelszavukat.
  >
  >
* **K: a felhasználók értesítést kaphat az Office portálon helyszíni jelszavukat lejárati?**

  > **V:** Ez azért lehetséges ma használata az AD FS utasítások itt: [küldése jelszó házirend jogcímek az AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396). Ha a Jelszókivonat-szinkronizálást használ, ez nem lehetséges ma. Ennek oka az, nem azt szinkronizálása a helyszíni jelszóházirendek, ezért nem lehetséges utáni lejárati értesítéseinek feladatait a felhőbe való. Mindkét esetben is lehetőség [értesítse a felhasználókat, amelyeknek a jelszava lejár, a PowerShell használatával](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **K: felhasználók módosítsák a jelszavukat blokkolja?**

  > **V:** csak felhőalapú felhasználók ez nem tiltható le. Beállíthatja a helyszíni felhasználók `User cannot change password` az be van jelölve, és ezek a felhasználók nem fognak tudni megváltoztatni a jelszavát.
  >
  >

## <a name="password-management-reports"></a>Jelszó-kezelési jelentések

* **K: mennyi ideig tart a jelenik meg a jelszó jelentések adatai?**

  > **V:** adatokat meg kell jelennie a jelszó jelentések 5-10 percen belül. Az egyes példányok, azt is tarthat egy órát jelennek meg.
  >
  >
* **K: hogyan szűrheti a jelszó-kezelési jelentések**

  > **V:** a kis nagyítóüveg szélsőséges jobb oldalán az oszlopfejléceket, a jelentés felső részén kattintson a jelszó jelentések szűrheti. Ha szeretné gazdagabb szűrés, letöltheti a jelentést az excel és kimutatás létrehozása.
  >
  >
* **K: Mi az az események maximális száma a jelszó jelentések tárolódnak?**

  > **V:** 75,000 jelszó alaphelyzetbe állítása vagy a jelszó alaphelyzetbe állítása regisztrációs események tárolódnak a jelszó-kezelési jelentések, akár átfedés biztonsági akár 30 napig.  Jelenleg dolgozunk, bontsa ki ezt a számot, hogy további események tartalmazzák.
  >
  >
* **K: milyen távolságban vissza a jelszó jelentések Ugrás?**

  > **V:** a jelszókezelés jelentések megjelenítése művelet az elmúlt 30 napban. A lépést Ha ezek az adatok archiválása kell után rendszeresen töltse le a jelentések is mentse őket másik helyen.
  >
  >
* **K: van-e a jelszó jelentések megjeleníthető sorok maximális száma?**

  > **V:** Igen, 75,000 sorok maximális szerepelhet vagy a jelszó jelentések, hogy éppen megtekinthető a felhasználói felületen vagy tölti le.
  >
  >
* **K: van egy API-t a jelszó alaphelyzetbe állítása vagy a jelentés adatainak regisztrációs eléréséhez?**

  > **V:** Igen, tekintse meg a következő dokumentációjából megtudhatja, hogyan férhet hozzá a jelszó-változtatási reporting adatfolyam.  [Jelszó alaphelyzetbe állítása programozott módon jelentési események elérése](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).
  >
  >

## <a name="password-writeback"></a>Jelszóvisszaíró

* **K: hogyan működik a jelszóvisszaírást a háttérben?**

  > **V:** lásd [jelszóvisszaírás működése](active-directory-passwords-writeback.md) az annak magyarázatát, mi történik, ha engedélyezi a jelszóvisszaírás, és hogyan adatáramlás rendszeren keresztül vissza a helyszíni környezetbe.
  >
  >
* **K: mennyi időt jelszóvisszaírás igénybe működéséhez?  A szinkronizálás késleltetés, például a Jelszókivonat-szinkronizálás van?**

  > **V:** jelszóvisszaírás azonnali. Egy szinkron folyamatot, amely a Jelszókivonat-szinkronizálást alapvetően eltérően működik. A jelszóvisszaírás lehetővé teszi a felhasználóknak a valós idejű visszajelzést a jelszó alaphelyzetbe állítása sikeres vagy módosítsa a műveletet. A jelszó sikeres visszaírása átlagos ideje alatt 500 ms.
  >
  >
* **K:, ha a helyi fiók le van tiltva, milyen hatással a van a felhő fiókelérést?**

  > **V:** a helyszíni-azonosítója le van tiltva, ha a felhő azonosítója/access is letiltja a következő szinkronizálás időközönként keresztül AAD-csatlakozás alapértelmezés szerint ez az 30 percenként.
  >
  >
* **K:, ha egy a helyszíni Active Directory jelszóházirend korlátozza a helyi fiók, önkiszolgáló jelszó-Változtatási felel meg a házirend a jelszó módosításakor?**

  > **V:** Igen, önkiszolgáló jelszó-Változtatási alapul, és megfelel a helyszíni AD-jelszóházirendet, beleértve az AD-tartományi jelszóházirend tipikus, valamint a meghatározott részletes jelszóházirendek egy adott felhasználót céloz meg.
  >
  >
* **K: milyen típusú fiókok jelszóvisszaírás működik?**

  > **V:** jelszóvisszaírás külső és a jelszó kivonatát szinkronizált felhasználók működik.
  >
  >
* **K: jelszóvisszaírás jelszóházirendjeinek érvényesítéséhez a tartományomat?**

  > **V:** Igen, érvénybe lépteti a jelszóvisszaírást a jelszó élettartama, előzmények, összetettségét, szűrők és helyezhet jelszavak helyen vannak a helyi tartományban lévő bármely más korlátozás.
  >
  >
* **K: az jelszóvisszaírás biztonságos?  Hogyan lehet, hogy I nem fognak első megtámadott?**

  > **V:** Igen, a jelszóvisszaírás biztonságos-e. Olvasható további információ a négy biztonsági réteg alkalmazása a jelszó visszaírási szolgáltatás által megvalósított, tekintse meg a [jelszó visszaírási biztonsági modell](active-directory-passwords-writeback.md#password-writeback-security-model) jelszóvisszaírás működése című szakasza.
  >
  >

## <a name="next-steps"></a>Következő lépések

* [Hogyan végezhető el az SSPR sikeres bevezetése?](active-directory-passwords-best-practices.md)
* [Új jelszó kérése vagy jelszó módosítása](active-directory-passwords-update-your-own-password.md).
* [Regisztráció új jelszó önkiszolgáló kérésére](active-directory-passwords-reset-register.md).
* [Kérdése van a licenceléssel kapcsolatban?](active-directory-passwords-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](active-directory-passwords-data.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](active-directory-passwords-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](active-directory-passwords-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](active-directory-passwords-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](active-directory-passwords-how-it-works.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
