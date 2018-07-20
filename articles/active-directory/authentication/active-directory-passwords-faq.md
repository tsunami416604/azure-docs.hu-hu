---
title: Az önkiszolgáló jelszó-visszaállítási – gyakori kérdések – Azure Active Directory
description: Gyakori kérdések az Azure AD önkiszolgáló jelszó alaphelyzetbe állítása
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 92f9732adadc4eb580d89f8a43cf76177450aeb7
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160742"
---
# <a name="password-management-frequently-asked-questions"></a>A jelszókezelés – gyakori kérdések

Az alábbiakban néhány gyakori kérdések (GYIK) jelszó-visszaállítás kapcsolatos összes dolgot.

Ha az Azure Active Directory (Azure AD) általános kérdésem van, és az önkiszolgáló jelszó-visszaállítás (SSPR), amely itt nem kaptam választ, megkérheti a Közösség segítségért a a [az Azure AD fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). A Közösség tagjai közé tartozik, mérnökök, termék kezelők, MVP-k és megosztana informatikai szakemberek számára.

Ez a GYIK a következő részekre van felosztva:

* [Kérdése jelszó alaphelyzetbe állításának regisztrációja](#password-reset-registration)
* [Jelszó-visszaállítással kapcsolatos kérdések](#password-reset)
* [Jelszó módosítása kapcsolatos kérdések](#password-change)
* [Jelszókezelési jelentések kapcsolatos kérdések](#password-management-reports)
* [A jelszóvisszaíró kapcsolatos kérdések](#password-writeback)

## <a name="password-reset-registration"></a>Jelszó alaphelyzetbe állításának regisztrációja

* **Kérdés: a felhasználók regisztrálhatják saját jelszó alaphelyzetbe állítása adatait?**

  > **V:** Igen. Mindaddig, amíg engedélyezve van a jelszó-visszaállítás, és rendelkeznek licenccel, a felhasználók ellátogathatnak a jelszó-visszaállítási portál (https://aka.ms/ssprsetup) regisztrálja a hitelesítési adataik. Felhasználók is regisztrálhatja a hozzáférési panelen keresztül (http://myapps.microsoft.com). A hozzáférési panelen keresztül regisztrálásához válassza ki a profilhoz tartozó fotó kiválasztása szükségük **profil**, majd válassza ki a **regisztráljanak a jelszóátállításra** lehetőséget.
  >
  >
* **K: Ha engedélyezhető a jelszó alaphelyzetbe állítása egy csoport, és úgy dönt, hogy engedélyezze a mindenki saját felhasználók szükséges regisztrálja újra?**

  > **V.:** Nem. Felhasználók, akik ki vannak töltve a hitelesítési adatok nem szükségesek az újbóli regisztrációt.
  >
  >
* **K: jelszó alaphelyzetbe állítása adatok is meghatározása a felhasználók nevében?**

  > **V:** Igen, ehhez az Azure AD Connect, a PowerShell, a [az Azure portal](https://portal.azure.com), vagy az Office 365 felügyeleti központban. További információkért lásd: [adatokat használják az Azure AD önkiszolgáló jelszó-visszaállítási](howto-sspr-authenticationdata.md).
  >
  >
* **K: biztonsági kérdéseket a helyszíni adatok szinkronizálása?**

  > **V:** nem, ez azonban nem lehet még ma.
  >
  >
* **Kérdés: a felhasználók regisztrálhatnak adatokat oly módon, hogy más felhasználók nem láthatják ezeket az adatokat?**

  > **V:** Igen. Amikor a felhasználók adatokat a jelszó alaphelyzetbe állítása regisztrációs portál, az adatok mentése csak a globális rendszergazdák és a felhasználó számára látható magánhálózati hitelesítési mezőkbe.
  >
  >
* **Kérdés: a felhasználók rendelkeznek kell regisztrálni a jelszó-visszaállítás használatához?**

  > **V.:** Nem. Ha elegendő hitelesítési adatok saját nevükben határozza meg, a felhasználóknak ne kelljen regisztrálniuk. Új jelszó kérésének módja mindaddig, amíg a megfelelő mezőket a könyvtárban tárolt adatok megfelelően formázza.
  >
  >
* **K: felhasználhatom szinkronizálása vagy a hitelesítéshez használt telefon, hitelesítő e-mail-cím vagy alternatív hitelesítési telefon mezők beállítása a felhasználók nevében?**

  > **V:** a mezőket, amelyek a globális rendszergazda állíthatja a cikkben meghatározott [SSPR-adatkövetelmények](howto-sspr-authenticationdata.md).
  >
  >
* **K: hogyan határozza meg a regisztrációs portálon a felhasználók megjelenítése, mely beállítások?**

  > **V:** a jelszó-visszaállítási regisztrációs portál megjeleníti csak azokat a beállításokat, hogy a felhasználók számára engedélyezett. Ezek a beállítások alatt találhatók a **felhasználói jelszó-visszaállítási házirend** szakasz a címtár **konfigurálása** fülre. Például ha nem engedélyezi a biztonsági kérdések, majd munkavégzésük nem tud regisztrálni ezt a lehetőséget.
  >
  >
* **K: Ha a felhasználó számít regisztrálni?**

  > **V:** A felhasználó akkor minősül az SSPR regisztrálva van regisztrálásakor legalább a **új jelszó kéréséhez szükséges módszerek száma** állított be jelszót a [az Azure portal](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Új jelszó létrehozása

* **K: ne, hogy a felhasználók új jelszót kért egy rövid idő alatt több kísérletek?**

  > **V:** Igen, vannak új jelszó kérésére vonatkozó való visszaélés elleni védelem beépített biztonsági szolgáltatásait. 
  >
  > Felhasználók próbálja csak öt jelszó alaphelyzetbe állítása kísérlet egy 24 órás időszakban, mielőtt azok zárolva van 24 órán belül. 
  >
  > Felhasználók próbálkozzon érvényesíteni egy telefonszám, SMS küldése vagy érvényesítéséhez biztonsági kérdéseit és válaszait csak ötször egy órát, mielőtt azok zárolva van 24 órán belül. 
  >
  > Felhasználók e-mailt küldhet a legfeljebb egy 10 perces időszak, mielőtt azok zárolva van 24 órán belül 10 alkalommal.
  >
  > A számlálók után a felhasználó a jelszavát alaphelyzetbe állnak vissza.
  >
  >
* **K: mennyi ideig várjon egy e-mailben, SMS vagy telefonhíváson alapuló fogadjon a jelszó-visszaállítás?**

  > **V:** E-mail, SMS-eket, és a egy perc alatt telefonhívásokat megérkezik a. Normális esetben érték 5-20 másodperc.
    >Ha ezen időszakon belül nem kapja meg az értesítés:
        > * Ellenőrizze a Levélszemét mappát.
        > * Ellenőrizze, hogy vagy az e-mail, amelyhez csatlakozik egy várt.
        > * Ellenőrizze, hogy a megadott hitelesítési adat a directory megfelelően van formázva, például + 1 4255551234 vagy *user@contoso.com*. 
  >
  >
* **Kérdés: milyen nyelveket támogat a jelszó-visszaállítás?**

  > **V:** a jelszó-visszaállítási felhasználói felület, SMS-EK és hanghívások honosítva ugyanazokat a nyelveket, amelyek támogatottak az Office 365-ben.
  >
  >
* **K: mely részeit a jelszóvisszaállítási első termékeire, a szervezeti márkajelzési elemeket a címtáramban meg a konfigurálás lap?**

  > **V:** a jelszó-visszaállítási portál jeleníti meg a vállalati embléma, és lehetővé teszi, hogy a "Forduljon a rendszergazdához" hivatkozást a kívánt egyéni e-mail vagy URL-cím konfigurálása. Bármely jelszó-visszaállítás által küldött e-mailt a szervezet embléma, színek és neve szerepel az e-mail törzse, és testre szabott beállítások adott név alapján.
  >
  >
* **K: hogyan is tájékoztassa arról, hogy hová lépjen a felhasználók visszaállíthassák a jelszavukat?**

  > **V:** próbálja meg a javaslatok némelyike a [SSPR üzembehelyezési](howto-sspr-deployment.md#sample-communication) cikk.
  >
  >
* **K: Használhatom ezt oldal mobileszközt?**

  > **V:** Igen, ez az oldal működik mobileszközökön.
  >
  >
* **K: támogatják a videókban rejlő információk helyi Active Directory-fiókokat, amikor a felhasználók visszaállíthassák a jelszavukat?**

  > **V:** Igen. Amikor a felhasználó alaphelyzetbe állítja a jelszavát, ha jelszóvisszaíró az Azure AD Connect segítségével telepítve lett, nem a felhasználói fiók, automatikusan oldják a zárolást, ha azok az új jelszót kérnek.
  >
  >
* **K: hogyan integrálhatók a jelszó-visszaállítást közvetlenül a saját felhasználói asztali bejelentkezési élmény?**

  > **V:** már a prémium szintű Azure AD-ügyfelek, ha további költségek nélkül a Microsoft Identity Manager telepíthető és a jelszó alaphelyzetbe állítása a helyszíni megoldás üzembe helyezése.
  >
  >
* **K: állíthatok különböző területi beállításokhoz különböző biztonsági kérdések?**

  > **V:** nem, ez azonban nem lehet még ma.
  >
  >
* **K: hogyan számos kérdések is konfigurálja a biztonsági kérdések hitelesítési lehetőség?**

  > **V:** konfigurálhatja a legfeljebb 20 egyéni biztonsági kérdések a [az Azure portal](https://portal.azure.com).
  >
  >
* **K: mennyi ideig biztonsági kérdések lehet?**

  > **V:** biztonsági kérdések 3 és 200 karakter hosszú lehet.
  >
  >
* **K: mennyi ideig a biztonsági kérdésekre adott válaszok lehet?**

  > **V:** válaszokat 3 és 40 karakter hosszú lehet.
  >
  >
* **K: elutasította a biztonsági kérdésekre ismétlődő válaszokat vannak?**

  > **V:** Igen, hogy elutasítja a duplikált biztonsági kérdésekre adott válaszok.
  >
  >
* **Kérdés: a felhasználó regisztrálhatja az azonos biztonsági kérdés egynél többször?**

  > **V.:** Nem. Miután egy felhasználó regisztrál egy adott kérdésre, azok nem sikerül regisztrálnia a kérdés másodszor.
  >
  >
* **K: van, és állítsa be a biztonsági kérdések regisztrációs a minimális korlát lehetséges?**

  > **V:** Igen, az egyik határértékről regisztráció és a egy másik alaphelyzetbe állítása a állítható. Lehet, hogy a 3-5 biztonsági kérdéseket a regisztrációhoz szükséges, és 3-5 kérdések a visszaállításhoz szükséges lehet.
  >
  >
* **Kérdés a szabályzat a felhasználók a biztonsági kérdések-visszaállítás konfigurálva, de az Azure-rendszergazdák a Microsoft-eltérően kell konfigurálni.**

  > **V:** Ez az az elvárt működés. A Microsoft érvényesíti a alapértelmezés szerint két-kapu jelszó kérésére vonatkozó szabályzat minden olyan Azure-rendszergazdai szerepkörhöz. Ez megakadályozza, hogy a rendszergazdák biztonsági kérdések használatával. További információ a jelen szabályzatban foglaltak annak a [jelszóházirendek és -korlátozások az Azure Active Directoryban](concept-sspr-policy.md) cikk.
  >
  >
* **K: Ha a felhasználó több, mint a maximális új jelszó kéréséhez szükséges kérdések száma regisztrálva van, hogyan vannak a biztonsági kérdéseket a kijelölt alaphelyzetbe állítása során?**

  > **V:** *N* biztonsági kérdések számát véletlenszerűen kiválasztott vannak kérdések, amelyre a felhasználó regisztrálta az összes számítógéphez képest *N* mutatja meg, hogy van beállítva a **száma Új jelszó kéréséhez szükséges kérdések** lehetőséget. Például ha egy felhasználó öt biztonsági kérdések regisztrálva van, de csak három szükség a jelszó alaphelyzetbe állítása, három az öt kérdés rendszer véletlenszerűen választja ki, és jelennek meg, alaphelyzetbe állítása. Hogy a kérdés újrapróbálkozni, ha a felhasználó élvezheti a kérdésekre adott válaszokat nem megfelelő a tanúsítványkiválasztási folyamat elindul keresztül.
  >
  >
* **K: mennyi ideig vannak az e-mailek és SMS egyszeri PIN-kódok érvényes?**

  > **V:** a munkamenetek élettartamát, jelszó-visszaállítás beállítás 15 perc. A jelszó-visszaállítási művelettel elejéről a felhasználók számára a jelszavuk 15 perc. Az e-mailek és SMS egyszer használatos jelszót érvénytelenek, ez az időtartam lejárta után is.
  >
  >
* **K: blokkolja a jelszó alaphelyzetbe állításával felhasználók?**

  > **V:** Igen, ha egy csoport használatával SSPR engedélyezése, eltávolíthatja egy adott felhasználó a csoportból, amely lehetővé teszi a felhasználók visszaállíthatják a jelszavukat. Ha a felhasználó globális rendszergazdai lesz megőrzését, alaphelyzetbe állíthatja a jelszavát, és ez nem lehet letiltani.
  >
  >

## <a name="password-change"></a>Jelszó módosítása

* **K: hol felhasználóim el kell küldeni a jelszavukat módosítani?**

  > **V:** felhasználókat jelszavuk módosításához bárhol láthatják a profilhoz tartozó fotó vagy ikonra, például a jobb felső sarkában a [Office 365](https://portal.office.com) portál vagy [hozzáférési Panel](https://myapps.microsoft.com) során lép fel. Felhasználók a jelszavuk módosításához a [hozzáférési Panel profilja lap](https://account.activedirectory.windowsazure.com/r#/profile). Felhasználók automatikus az Azure AD bejelentkezési lapján, a jelszavukat módosítani, ha a jelszavuk is meg kell adnunk. Végül felhasználók megnyithatja a [az Azure AD jelszóátállítási portál](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) közvetlenül, ha szeretnék, hogy módosítsák jelszavukat.
  >
  >
* **Kérdés: a felhasználók értesítést kaphat az Office portálon helyi jelszó lejár?**

  > **V:** Igen, ez a lehetőség jelenleg az Active Directory összevonási szolgáltatások (AD FS) használatakor. Ha az AD FS használja, kövesse a a [jelszó házirend jogcímeket az AD FS-sel küldjön](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) cikk. Ha a Jelszókivonat-szinkronizálást használ, ez azonban nem lehet még ma. Jelszóházirendek a helyszíni címtárakban, hogy nem szinkronizálódnak, ezért nem lehetséges közzététele lejárati értesítéseinek élményt felhőbe való. Mindkét esetben is lehetőség [értesíti a felhasználókat, amelyeknek a jelszava hamarosan lejár, a PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **K: blokkolja a felhasználók számára a jelszavát?**

  > **V:** csak felhőbeli felhasználók, a jelszó módosítására nem lehet blokkolni. A helyszíni felhasználók számára, beállíthatja a **nem lehet változtatni a jelszót** ahhoz, hogy a beállítást. A kijelölt felhasználók nem lehet módosítani a jelszavát.
  >
  >

## <a name="password-management-reports"></a>Jelszókezelési jelentések

* **K: mennyi ideig tart az adatok jelennek meg az a jelszókezelési jelentések?**

  > **V:** adatokat kell megjelennie a jelszókezelési jelentések 5-10 perc múlva. Bizonyos esetekben ez is igénybe vehet egy órát jelennek meg.
  >
  >
* **K: hogyan szűrheti a jelszókezelési jelentések?**

  > **V:** a jelszókezelési jelentések szűréséhez válasszon a kis, rendkívüli jobbra az oszlopfejléceket, a jelentés tetején található nagyító ikonra. Ha szeretné gazdagabb szűrést, töltse le a jelentés az Excel, és egy olyan kimutatás létrehozásának.
  >
  >
* **K: Mi a jelszókezelési jelentések tárolt események maximális száma?**

  > **V:** akár 75 000 jelszó alaphelyzetbe állítása vagy a jelszó alaphelyzetbe állítása regisztrációs események a jelszókezelési jelentések, átfedés vissza 30 nap szerint vannak tárolva. Folyamatban van, bontsa ki ezt a számot, hogy további események tartalmazzák.
  >
  >
* **Kérdés: milyen vissza a jelszókezelési jelentések lépjen?**

  > **V:** a jelszókezelés jelentések megjelenítése műveletek, amelyek az elmúlt 30 napban történt. Most ha archiválásához ezeket az adatokat, letöltheti a jelentések rendszeres időközönként és mentse őket egy külön helyen.
  >
  >
* **K: van, amely a jelszókezelési jelentések is megjelenhetnek sorok maximális számát?**

  > **V:** Igen. Legfeljebb 75 000 sort e jelennek meg a felhasználói felületen vagy a rendszer letölti a is megjelenhetnek a jelszókezelési jelentések egyikét.
  >
  >
* **K: van egy API-t a jelszó-visszaállítás vagy a jelentés adatainak regisztrációs eléréséhez?**

  > **V:** Igen. Című cikk nyújt tájékoztatást, hogyan férhet hozzá az adatfolyam jelentéskészítési jelszó-visszaállítás, [elérése programozott módon jelentési események jelszó-visszaállítás](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).
  >
  >

## <a name="password-writeback"></a>Jelszóvisszaíró

* **K: hogyan működik a jelszóvisszaírást a színfalak mögött?**

  > **V:** tekintse meg a cikket [jelszóvisszaírás működéséről](howto-sspr-writeback.md) működéséről, mi történik, ha engedélyezte a jelszóvisszaírást, és hogyan áramlanak keresztül az adatok a rendszer biztonsági a helyszíni környezetbe.
  >
  >
* **K: mennyi jelszóvisszaíró tart működik? A hiba a szinkronizálás késleltetés, hogy a Jelszókivonat-szinkronizálás?**

  > **V:** jelszóvisszaíró azonnali. Egy szinkron folyamatot, amely a Jelszókivonat-szinkronizálás alapvetően eltérően működik. A jelszóvisszaíró lehetővé teszi a felhasználóknak a jelszó alaphelyzetbe állítása sikeres kapcsolatos valós idejű visszajelzést kapni, vagy módosítsa a műveletet. A jelszó sikeres visszaírása átlagos ideje alatt 500 ms.
  >
  >
* **K: Ha a saját helyi fiók le van tiltva, hogyan van saját felhő-fiók és a hozzáférés érintett?**

  > **V:** a helyszíni azonosítója le van tiltva, ha a felhő-azonosító és a hozzáférés is letiltja a rendszer a következő szinkronizálás időközönként Azure AD Connecten keresztül. Alapértelmezés szerint a szinkronizálás a 30 percenként.
  >
  >
* **K: Ha a saját helyi fiók egy helyi Active Directory jelszóházirend korlátozza, nem az SSPR betartani a szabályzat Ha módosítom a jelszavamat?**

  > **V:** Igen, az SSPR támaszkodik, és betartja a helyszíni Active Directory jelszóházirend. Ez a házirend a szokásos Active Directory tartományi jelszóházirend, valamint bármely definiált, a részletes jelszóházirendek egy felhasználónak szánt tartalmazza.
  >
  >
* **Kérdés: milyen típusú fiókok jelszóvisszaíró működik?**

  > **V:** jelszó visszaírási működik az Azure AD a helyszíni Active Directoryból szinkronizált felhasználói fiókok esetében, beleértve az összevont, jelszókivonatok szinkronizálása és átmenő Autentication felhasználók számára.
  >
  >
* **K: a jelszóvisszaíró felhasználók saját tartomány?**

  > **V:** Igen. A jelszóvisszaíró jelszó kora, előzmények, bonyolultsága, szűrők és bármely más, vannak-e jelszavak helyen a helyi tartomány korlátozás kényszeríti.
  >
  >
* **K: van a jelszóvisszaíró biztonságos?  Hogyan lehet arról szeretnék nem get lesznek?**

  > **V:** Igen, a jelszóvisszaíró biztonságos-e. Olvassa el a jelszóvisszaíró szolgáltatás implementálja további információt a több rétegű biztonság, tekintse át a [jelszó-visszaírás biztonsági](concept-sspr-writeback.md#password-writeback-security) című rész a [jelszó-visszaírás áttekintése](howto-sspr-writeback.md) cikk.
  >
  >

## <a name="next-steps"></a>További lépések

* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Jelszó visszaállítása vagy módosítása](../user-help/active-directory-passwords-update-your-own-password.md)
* [Regisztráció önkiszolgáló jelszó-visszaállításra](../user-help/active-directory-passwords-reset-register.md)
* [Kérdése van a licenceléssel kapcsolatban?](concept-sspr-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](howto-sspr-authenticationdata.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](howto-sspr-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
