---
title: Az önkiszolgáló jelszó-átállítási gyakori kérdések – Azure Active Directoryban
description: Gyakori kérdések az Azure AD az önkiszolgáló jelszó alaphelyzetbe állítása
services: active-directory
keywords: Az Active directory-jelszókezelés, jelszókezelés, az Azure AD self service jelszó alaphelyzetbe állítása
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: seohack1
ms.openlocfilehash: 7500b456296dc69c70cd1b5500d40f7e007569b1
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="password-management-frequently-asked-questions"></a>A jelszókezelés gyakran ismételt kérdések

Az alábbiakban néhány gyakori kérdésekkel (GYIK) a jelszó alaphelyzetbe állítása kapcsolódó van.

Ha az Azure Active Directory (Azure AD) általános kérdése van, és az önkiszolgáló jelszó-visszaállításból (SSPR), amely itt nem érkezik válasz, megkérheti a közösségi Ha segítségre van szüksége a a [az Azure AD fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). A Közösség tagjai közé tartozik a mérnökök, termék kezelők, MVP és ahol informatikai szakemberek számára.

Ez a GYIK a következő szakaszok oszlik:

* [Kérdésekre jelszó-átállítási regisztrációk](#password-reset-registration)
* [Jelszó alaphelyzetbe állítása kérdések](#password-reset)
* [A jelszó módosítása kapcsolatos kérdések](#password-change)
* [Jelszó felügyeleti jelentésekkel kapcsolatos kérdések](#password-management-reports)
* [A jelszóvisszaírás kapcsolatos kérdések](#password-writeback)

## <a name="password-reset-registration"></a>Jelszó alaphelyzetbe állításának regisztrációja

* **K: a felhasználók regisztrálhatják saját jelszó alaphelyzetbe állítása adatokat?**

  > **V:** Igen. Nyissa meg, amíg a jelszó alaphelyzetbe állítása engedélyezve van, és azok licencét, a jelszó-visszaállítási portál felhasználók (https://aka.ms/ssprsetup) regisztrálni a hitelesítési adatokat. A felhasználók is regisztrálhatják a hozzáférési Panel keresztül (http://myapps.microsoft.com). A hozzáférési Panel keresztül regisztrálásához szükséges a profilkép megadásához válassza **profil**, majd válassza ki a **regisztrálhatnak a jelszóváltoztatásra** lehetőséget.
  >
  >
* **K: Ha engedélyezhető a jelszó alaphelyzetbe állítása egy csoport, és úgy dönt, hogy engedélyezze a Mindenki a felhasználók szükség regisztrálja újra?**

  > **V.:** Nem. Felhasználók, akik rendelkeznek feltöltve a hitelesítési adatok nem szükséges ismételten a regisztrációt.
  >
  >
* **K: jelszó alaphelyzetbe állítása adatok is definiálása a felhasználók nevében?**

  > **V:** Igen, akkor az Azure AD Connect, PowerShell, a [Azure-portálon](https://portal.azure.com), vagy az Office 365 felügyeleti központot. További információkért lásd: [az Azure AD az önkiszolgáló jelszó által használt adatok alaphelyzetbe állítása](howto-sspr-authenticationdata.md).
  >
  >
* **K: biztonsági kérdéseket tesz fel, a helyszíni adatok szinkronizálása?**

  > **V:** nem, ez nem lehetséges ma.
  >
  >
* **K: a felhasználók regisztrálhatják adatokat úgy, hogy más felhasználók nem láthatják ezeket az adatokat?**

  > **V:** Igen. Amikor a felhasználók regisztrálhatnak adatok a jelszó alaphelyzetbe állítása regisztrációs portál, az adatok mentése csak a globális rendszergazdák és a felhasználó számára látható személyes hitelesítési mezőkbe.
  >
  >
* **K: a felhasználók rendelkeznek regisztrálni kell a jelszó alaphelyzetbe állítása használatához?**

  > **V.:** Nem. A nevében elég hitelesítési adatainak megadása esetén a felhasználóknak nem kell regisztrálni. Jelszó-átállítási működik, amíg a megfelelő mezőket a könyvtárban tárolt adatok megfelelően formázott.
  >
  >
* **K: I szinkronizálása vagy a hitelesítéshez megadott telefonját, hitelesítési e-mail vagy más hitelesítési phone mezők beállítása a felhasználók nevében?**

  > **V:** egy globális rendszergazda által beállított kell tudni mezők definiálják a cikk [SSPR követelményeinek](howto-sspr-authenticationdata.md).
  >
  >
* **K: hogyan határozza meg a a regisztrációs portálon a felhasználók megjelenítése beállítások?**

  > **V:** a jelszó-változtatási regisztrációs portál látható csak azokat a beállításokat, hogy a felhasználók számára engedélyezett. Ezek a beállítások alatt találhatók a **felhasználói jelszó-visszaállítási házirend** a címtár **konfigurálása** fülre. Például ha nem engedélyezi az biztonsági kérdéseket, majd felhasználók képesek nem regisztrálja az adott beállítási mód.
  >
  >
* **Amikor egy felhasználó tekinthető k: regisztrálni?**

  > **V:** regisztrált sspr, amikor regisztrálják azokat A felhasználó akkor tekinthető legalább a **számos módszer alaphelyzetbe kell állítani** beállított jelszót a [Azure-portálon](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Új jelszó létrehozása

* **K: el azt, hogy többszöri rövid időn belül a jelszó alaphelyzetbe állítása a felhasználók?**

  > **V:** Igen, vannak beépítve a jelszó alaphelyzetbe állítása való visszaélés elleni biztonsági funkciók. 
  >
  > A felhasználók megpróbálják is csak öt jelszó átállítani a jelszavát egy 24 órás időszakban ahhoz, azok zárolva van 24 órán belül. 
  >
  > Felhasználók próbálkozzon egy telefonszám ellenőrzése, SMS küldése, vagy ellenőrizze a biztonsági kérdések és válaszok csak ötször ahhoz, azok zárolva van 24 órán át egy órán belül. 
  >
  > Felhasználó egy e-mailt küldhet a legfeljebb 10-szer ahhoz, azok zárolva van 24 órán át egy 10 perces időszakon belül.
  >
  > A számlálók alaphelyzetbe állnak, miután a felhasználó alaphelyzetbe állítja a jelszót.
  >
  >
* **K: mennyi ideig várjon egy e-mailben, SMS vagy telefonhívás fogadása jelszó alaphelyzetbe állítása?**

  > **V:** e-mailek, az SMS-t, és telefonhívásokat érkeznek meg egy perc alatt. Normális esetben érték 5-20 másodperc.
    >Ha nem jelenik meg az értesítés a időkereten belül:
        > * Ellenőrizze a Levélszemét mappát.
        > * Ellenőrizze, hogy a vagy e-mail, amelyhez csatlakozik egy várt.
        > * Ellenőrizze, hogy a címtárban a hitelesítési adatok megfelelően van formázva, például + 1 4255551234 vagy *user@contoso.com*. 
  >
  >
* **K: jelszó alaphelyzetbe állítása milyen nyelveket támogatja?**

  > **V:** a jelszó-visszaállítási felhasználói felület, SMS-t, és hanghívások honosítva vannak az Office 365-ben támogatott nyelven.
  >
  >
* **K: milyen részei a jelszó alaphelyzetbe állítása élmény első vállalati arculattal, a szervezeti márkajelzési elemek meg a könyvtárban a konfigurálás lap?**

  > **V:** a jelszó-változtatási portál jeleníti meg a vállalati embléma, és konfigurálhatja a "Forduljon a rendszergazdához" hivatkozásra kattintva egy egyéni e-mail vagy URL-CÍMRE mutasson. A jelszó-visszaállítás által küldött e-mailben a szervezet embléma, színek, és nevét az e-mail törzsében, és testreszabott beállításai közül az adott névvel.
  >
  >
* **K: hogyan lehet ismertetni kell a felhasználók visszaállíthassák a jelszavukat önállóan hol találhatnak kapcsolatban?**

  > **V:** próbáljon ki a javaslatok a [SSPR telepítési](howto-sspr-deployment.md#email-based-rollout) cikk.
  >
  >
* **K: használhatok ezen a lapon egy mobileszközről?**

  > **V:** Igen, az ezen a lapon a mobileszközök működik.
  >
  >
* **K: támogatják a feloldásának helyi Active Directory-fiókokat, amikor a felhasználók visszaállíthassák a jelszavukat?**

  > **V:** Igen. A felhasználó alaphelyzetbe állítja a jelszavát, ha jelszóvisszaírás van telepítve az Azure AD Connect használatával, amikor azok a jelszó visszaállítása után automatikusan fel nem adott felhasználói fiók.
  >
  >
* **K: hogyan integrálhatók a jelszó alaphelyzetbe állítása közvetlenül a saját felhasználói asztali bejelentkezés során tapasztal élmény?**

  > **V:** Ha Ön az Azure AD Premium felhasználóinál, telepítse a Microsoft Identity Manager további költségek nélkül, és a jelszó alaphelyzetbe állítása a helyszíni megoldás üzembe helyezéséhez.
  >
  >
* **K: beállítása a különböző területi beállításokhoz különböző biztonsági kérdést?**

  > **V:** nem, ez nem lehetséges ma.
  >
  >
* **K: hogyan számos kérdés is konfigurálhatja a biztonsági kérdések hitelesítési lehetőséget a?**

  > **V:** legfeljebb 20 egyéni biztonsági kérdéseket is beállíthat a [Azure-portálon](https://portal.azure.com).
  >
  >
* **K: mennyi ideig biztonsági kérdések lehet?**

  > **V:** biztonsági kérdések 3 és 200 karakter hosszú lehet.
  >
  >
* **K: mennyi ideig a biztonsági kérdésekre adott válaszok lehet?**

  > **V:** válaszok 3-40 karakter hosszú lehet.
  >
  >
* **K: ismétlődő válaszokat ad a biztonsági kérdésekre elutasított vannak?**

  > **V:** Igen, azt utasítsa el az ismétlődő válaszokat ad a biztonsági kérdésekre.
  >
  >
* **K: egy felhasználó regisztrálhatja a ugyanazt biztonsági kérdésre adott egynél többször?**

  > **V.:** Nem. Miután a felhasználó regisztrál egy adott kérdést, azok lehet regisztrálni a a kérdést, még egyszer.
  >
  >
* **K: van lehetőség a minimális addig, amíg a regisztráció biztonsági kérdések, és alaphelyzetbe állítja?**

  > **V:** Igen, egy korlát a regisztrációs és egy másik alaphelyzetbe állítása a állítható be. Lehet, hogy a 3-5 biztonsági kérdések regisztrációjához szükséges, és 3-5 kérdéseket az alaphelyzetbe állítás szükséges lehet.
  >
  >
* **K: beállítása a házirendet a biztonsági kérdések használata alaphelyzetbe állítása felhasználóknak kötelező legyen, de az Azure rendszergazdák úgy tűnik, hogy eltérően kell konfigurálni.**

  > **V:** Ez az elvárt működés. A Microsoft érvényesíti egy erős alapértelmezett két-kapu jelszó-visszaállítási házirend bármely Azure rendszergazdai szerepkörhöz. Ez megakadályozza, hogy a rendszergazdák biztonsági kérdések használata. Ez a házirend a további információ található a [jelszóházirendek és -korlátozások az Azure Active Directory](concept-sspr-policy.md#administrator-password-policy-differences) cikk.
  >
  >
* **K:, ha a felhasználó regisztrálva van a több maximális kérdések alaphelyzetbe kell állítani, hogy miként kell a biztonsági kérdések kijelölni alaphelyzetbe állítása során?**

  > **V:** *N* biztonsági kérdések számát véletlenszerűen kiválasztott felhasználó regisztrálva van, ahol kérdések száma kívül *N* mutatja meg, hogy be van állítva a **száma a kérdések alaphelyzetbe kell állítani** lehetőséget. Például ha egy felhasználó által regisztrált öt biztonsági kérdéseket, de csak három szükséges a jelszó alaphelyzetbe állítása, három öt kérdéseket a rendszer véletlenszerűen választja ki és jelennek meg, alaphelyzetbe állítása. Kérdés beütés, elkerülésére, ha a felhasználó élvezheti a kérdésekre adott válaszai nem megfelelő a tanúsítványkiválasztási folyamat elindul keresztül.
  >
  >
* **K: mennyi ideig van az e-mailek és SMS egyszeri PIN-kódok érvényes?**

  > **V:** a munkamenetek élettartamát, jelszó-visszaállításhoz érték 15 perc. A jelszó-visszaállítási művelet kezdetétől a felhasználó rendelkezik-e jelszavuk 15 perc. Az e-mailek és SMS egyszer használatos jelszót érvénytelenek, ebben az időszakban lejárata után is.
  >
  >
* **K: a jelszó alaphelyzetbe állításával felhasználók megakadályozása?**

  > **V:** Igen, ha csoportot használjon SSPR engedélyezéséhez, eltávolíthat egy adott felhasználó a csoport, amely lehetővé teszi a felhasználók visszaállíthassák a jelszavukat. Ha a felhasználó egy globális rendszergazda azok megőrzik a jelszó alaphelyzetbe állításához, és ez nem lehet letiltani.
  >
  >

## <a name="password-change"></a>Jelszó módosítása

* **K: hol kell nyissa meg a felhasználók a jelszavuk módosítása?**

  > **V:** felhasználók módosíthatják a jelszavukat bárhol láthatják a profilkép vagy ikonra, például jobb felső sarkában a [Office 365](https://portal.office.com) portal vagy [hozzáférési Panel](https://myapps.microsoft.com) észlel. Felhasználók módosíthatják a jelszavukat a a [hozzáférési Panel profilszerkesztési lap](https://account.activedirectory.windowsazure.com/r#/profile). Felhasználók jelszavukat automatikusan a bejelentkezési oldal az Azure AD, ha a jelszavuk is meg kell adnia. Végezetül felhasználók továbbléphet a [az Azure AD-jelszó módosítása portál](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) közvetlenül a jelszavukat módosítani szeretné.
  >
  >
* **K: a felhasználók értesítést kaphat az Office portálon helyszíni jelszavukat lejárati?**

  > **V:** Igen, ez az lehetséges ma Active Directory összevonási szolgáltatások (AD FS) használatakor. Ha az AD FS használatához kövesse az utasításokat a a [jelszó házirend jogcímek az AD FS küldése](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) cikk. Ha Jelszókivonat-szinkronizálást használ, ez nem lehetséges ma. Jelszóházirendek a helyszíni címtárakban, azt nem szinkronizál, ezért nem lehetséges utáni lejárati értesítéseinek feladatait a felhőbe való. Mindkét esetben is lehetőség [jelszavait lejárati idejének közeledtével a PowerShell segítségével a felhasználók értesítése](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **K: felhasználók módosítsák a jelszavukat blokkolja?**

  > **V:** csak felhőalapú felhasználók jelszó-változtatásának nem zárolható. A helyszíni felhasználók számára, beállíthatja a **felhasználó nem módosíthatja a jelszót** lehetőséget kiválasztva. A kijelölt felhasználók nem módosíthatják jelszavukat.
  >
  >

## <a name="password-management-reports"></a>Jelszó-kezelési jelentések

* **K: mennyi ideig tart a jelenik meg a jelszó jelentések adatai?**

  > **V:** adatok jelenjenek meg a jelszó jelentések, 5 – 10 perc múlva. Bizonyos esetekben az lehet, hogy tarthat egy órát jelennek meg.
  >
  >
* **K: hogyan szűrheti a jelszó-kezelési jelentések**

  > **V:** a jelszó jelentések szűréséhez válassza a kis nagyítóüveg szélsőséges jobb oldalán az oszlopfejléceket, a jelentés felső részén. Ha szeretné gazdagabb szűrés, töltse le a jelentést az Excel programba, és kimutatás létrehozása.
  >
  >
* **K: Mi az a jelszó jelentések tárolt események maximális száma?**

  > **V:** legfeljebb 75,000 jelszó alaphelyzetbe állítása vagy a jelszó alaphelyzetbe állítása regisztrációs események tárolódnak a jelszó jelentések, átfedés vissza szerint 30 nap. Jelenleg dolgozunk, bontsa ki ezt a számot, hogy további események tartalmazzák.
  >
  >
* **K: milyen távolságban vissza a jelszó jelentések Ugrás?**

  > **V:** a jelszókezelés jelentések megjelenítése műveleteket, amelyek az elmúlt 30 napban történt. A lépést Ha ezek az adatok archiválása kell után rendszeresen töltse le a jelentések is mentse őket másik helyen.
  >
  >
* **K: van-e a jelszó jelentések megjeleníthető sorok maximális száma?**

  > **V:** Igen. 75,000 sorok maximális megjelenhet vagy a jelszó jelentések, hogy megjelennek-e a felhasználói felület, vagy letöltődnek.
  >
  >
* **K: van egy API-t a jelszó alaphelyzetbe állítása vagy a jelentés adatainak regisztrációs eléréséhez?**

  > **V:** Igen. Megtudhatja, hogyan férhet hozzá a jelszó-változtatási adatfolyam reporting, lásd: [elérése programozott módon jelentési események jelszó-visszaállítás](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).
  >
  >

## <a name="password-writeback"></a>Jelszóvisszaíró

* **K: hogyan működik a jelszóvisszaírást a háttérben?**

  > **V:** cikkben [jelszóvisszaírás működése](howto-sspr-writeback.md) annak magyarázatát, mi történik, ha engedélyezi a jelszóvisszaírás, és hogyan áthaladó az adatokat a rendszer biztonsági másolatot a helyszíni környezetbe.
  >
  >
* **K: mennyi időt jelszóvisszaírás igénybe működéséhez? Van hiba a szinkronizálás késleltetés van a Jelszókivonat-szinkronizálás?**

  > **V:** jelszóvisszaírás azonnali. Egy szinkron folyamatot, amely a Jelszókivonat-szinkronizálást alapvetően eltérően működik. A jelszóvisszaírás lehetővé teszi a felhasználóknak a valós idejű visszajelzést a jelszó alaphelyzetbe állítása sikeres vagy módosítsa a műveletet. A jelszó sikeres visszaírása átlagos ideje alatt 500 ms.
  >
  >
* **K:, ha a helyi fiók le van tiltva, hogyan van a saját felhő-fiók és a hatással access?**

  > **V:** a helyszíni-azonosítója le van tiltva, ha a felhő azonosítója és a hozzáférés is letiltja az Azure AD Connect használatával a következő szinkronizálás időközönként. Alapértelmezés szerint a Szinkronizáló 30 percenként van.
  >
  >
* **K:, ha egy a helyszíni Active Directory jelszóházirend korlátozza a helyi fiók, önkiszolgáló jelszó-Változtatási felel meg a házirend jelszó módosításakor?**

  > **V:** Igen, önkiszolgáló jelszó-Változtatási alapul, és a helyszíni Active Directory jelszóházirend aláveti. Ez a házirend a szokásos Active Directory tartományi jelszóházirend, valamint bármely definiált, a részletes jelszóházirendek, amely egy felhasználónak címezték tartalmazza.
  >
  >
* **K: milyen típusú fiókok jelszóvisszaírás működik?**

  > **V:** felhasználói fiókok a helyszíni Active Directoryból szinkronizált az Azure AD-jelszó visszaírási működik, beleértve az összevont, a Jelszókivonat szinkronizálása és a csatlakoztatott Autentication felhasználók.
  >
  >
* **K: jelszóvisszaírás jelszóházirendjeinek érvényesítéséhez a tartományomat?**

  > **V:** Igen. A jelszóvisszaírás érvénybe lépteti a jelszó élettartama, előzmények, összetettségét, szűrők és bármely más, előfordulhat, hogy helyezett jelszavak helyen vannak a helyi tartomány korlátozás.
  >
  >
* **K: az jelszóvisszaírás biztonságos?  Hogyan lehet, hogy I nem fognak első megtámadott?**

  > **V:** Igen, a jelszóvisszaírás biztonságos-e. Olvasható további információ a négy biztonsági réteg alkalmazása a jelszó visszaírási szolgáltatás által megvalósított, tekintse meg a [jelszó visszaírási biztonsági modell](howto-sspr-writeback.md#password-writeback-security-model) szakasz a [jelszó visszaírási áttekintése](howto-sspr-writeback.md) cikk.
  >
  >

## <a name="next-steps"></a>További lépések

* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Jelszó visszaállítása vagy módosítása](../active-directory-passwords-update-your-own-password.md)
* [Regisztráció önkiszolgáló jelszó-visszaállításra](../active-directory-passwords-reset-register.md)
* [Kérdése van a licenceléssel kapcsolatban?](concept-sspr-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](howto-sspr-authenticationdata.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](howto-sspr-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
