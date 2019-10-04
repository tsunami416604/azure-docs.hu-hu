---
title: Önkiszolgáló jelszó-visszaállítási GYIK – Azure Active Directory
description: Az Azure AD önkiszolgáló jelszó-visszaállítási szolgáltatásával kapcsolatos gyakori kérdések
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4001f3c88b676a2786159946a8981823e18ea5f6
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274414"
---
# <a name="password-management-frequently-asked-questions"></a>Jelszavas kezelés – gyakori kérdések

A következő gyakori kérdések (GYIK) a jelszó-visszaállítással kapcsolatos összes dologra vonatkoznak.

Ha az Azure Active Directory (Azure AD) és az önkiszolgáló jelszó-visszaállítási (SSPR) szolgáltatással kapcsolatos általános kérdéssel rendelkezik, akkor a Közösség segítséget kérhet az [Azure ad-fórumon](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). A Közösség tagjai közé tartoznak a mérnökök, a termékmenedzsment, a MVP és az informatikai szakemberek.

Ez a GYIK a következő szakaszokra oszlik:

* [A jelszó-visszaállítási regisztrációval kapcsolatos kérdések](#password-reset-registration)
* [A jelszó-visszaállítással kapcsolatos kérdések](#password-reset)
* [A jelszó módosításával kapcsolatos kérdések](#password-change)
* [A jelszavas kezelési jelentésekkel kapcsolatos kérdések](#password-management-reports)
* [A jelszó visszaírási kapcsolatos kérdések](#password-writeback)

## <a name="password-reset-registration"></a>Jelszó alaphelyzetbe állításának regisztrációja

* **K:  Regisztrálhatják a felhasználók a saját jelszó-visszaállítási adataikat?**

  > **V:** Igen. https://aka.ms/ssprsetup) Ha a jelszó alaphelyzetbe állítása engedélyezve van, és licenccel rendelkezik, a felhasználók a jelszó-visszaállítási regisztrációs portálra léphetnek be a hitelesítési adataik regisztrálásához. A felhasználók a hozzáférési panelen is regisztrálhatnak (https://myapps.microsoft.com). Ha regisztrálni szeretne a hozzáférési panelen, ki kell választania a profil képét, válassza a **profil**lehetőséget, majd válassza a **jelszó-visszaállítási regisztráció** lehetőséget.
  >
  >
* **K:  Ha Engedélyezem a jelszó-visszaállítást egy csoport számára, és úgy dönt, hogy mindenki számára engedélyezi azt, akkor a felhasználóknak újra regisztrálniuk kell?**

  > **V:** Nem. Azok a felhasználók, akiknek nincs szükségük a feltöltött hitelesítési adatokra, nem szükségesek az újbóli regisztráláshoz.
  >
  >
* **K:  Megadhatom a felhasználók nevében a jelszó-visszaállítási adataikat?**

  > **V:** Igen, ezt a Azure AD Connect, a PowerShell, a [Azure Portal](https://portal.azure.com)vagy a [Microsoft 365 felügyeleti központ](https://admin.microsoft.com)használatával teheti meg. További információ: az [Azure ad önkiszolgáló jelszó-visszaállítási szolgáltatás által használt adatok](howto-sspr-authenticationdata.md).
  >
  >
* **K:  Szinkronizálhatók a helyszíni biztonsági kérdések adatai?**

  > **V:** Nem, ez ma nem lehetséges.
  >
  >
* **K:  A felhasználók olyan módon regisztrálhatják az adataikat, hogy más felhasználók nem látják ezeket az adataimat?**

  > **V:** Igen. Amikor a felhasználók a jelszó-visszaállítási regisztrációs portál használatával regisztrálják az adataikat, a rendszer a személyes hitelesítési mezőkbe menti az adatfájlokat, amelyek csak a globális rendszergazdák és a felhasználók számára láthatók.
  >
  >
* **K:  A felhasználókat regisztrálni kell a jelszó-visszaállítás használatához?**

  > **V:** Nem. Ha az nevében elegendő hitelesítési információt határoz meg, a felhasználóknak nem kell regisztrálniuk. A jelszó alaphelyzetbe állítása akkor működik, ha megfelelően formázta a megfelelő mezőkben tárolt adatait a címtárban.
  >
  >
* **K:  Szinkronizálhatom vagy beállítom a hitelesítési telefont, a hitelesítő e-maileket vagy a másodlagos hitelesítés telefonos mezőit a felhasználók nevében?**

  > **V:** A globális rendszergazda által beállítható mezők a [SSPR-adatkövetelmények](howto-sspr-authenticationdata.md)című cikkben vannak meghatározva.
  >
  >
* **K:  Hogyan határozza meg a regisztrációs portál a felhasználók megjelenítési lehetőségeit?**

  > **V:** A jelszó-visszaállítási regisztrációs portál csak azokat a beállításokat jeleníti meg, amelyekhez engedélyezve van a felhasználók számára. Ezek a beállítások a címtár **Konfigurálás** lapjának **felhasználói jelszó-visszaállítási házirend** szakaszában találhatók. Ha például nem engedélyezi a biztonsági kérdéseket, akkor a felhasználók nem tudnak regisztrálni erre a lehetőségre.
  >
  >
* **K:  Ha a felhasználó regisztrálva van?**

  > **V:** A rendszer a SSPR regisztrált felhasználója, ha legalább annyi módszert regisztrált, amely a [Azure Portalban](https://portal.azure.com)beállított jelszó **alaphelyzetbe állításához szükséges** .
  >
  >

## <a name="password-reset"></a>Jelszó alaphelyzetbe állítva

* **K:  Meggátolja, hogy a felhasználók rövid idő alatt több kísérletet állítsanak vissza a jelszó alaphelyzetbe állítására?**

  > **V:** Igen, a jelszó-visszaállításhoz beépített biztonsági funkciók védik a visszaéléseket. 
  >
  > A felhasználók 24 órán belül csak öt jelszó-visszaállítási kísérletet tudnak kizárni, mielőtt 24 óráig kizárták őket. 
  >
  > A felhasználók megkereshetik a telefonszámot, SMS-t küldhetnek, vagy ellenőrizhetik a biztonsági kérdéseket és válaszokat egy órán belül, mielőtt 24 óráig kizárták őket. 
  >
  > A felhasználók legfeljebb 10 alkalommal küldhetnek e-mailt egy 10 perces időszakon belül, mielőtt 24 óráig kizárták őket.
  >
  > A számlálók alaphelyzetbe állnak, ha a felhasználó visszaállítja a jelszavát.
  >
  >
* **K:  Meddig kell várni egy e-mail, SMS vagy telefonhívás fogadására a jelszó alaphelyzetbe állítása után?**

  > **V:** Az e-mailek, SMS-üzenetek és telefonhívások egy perc alatt érkeznek meg. A normál esetben 5 – 20 másodperc.
  > Ha nem kapja meg az értesítést ebben az időkeretben:
  > * Keresse meg a Levélszemét mappát.
  > * Győződjön meg arról, hogy a megjelenő szám vagy e-mail szerepel a vártnál.
  > * Győződjön meg arról, hogy a címtárban található hitelesítési adat megfelelően van formázva, például: + 1 4255551234 vagy *felhasználói\@contoso.com*. 
* **K:  Milyen nyelveket támogat a jelszó-visszaállítás?**

  > **V:** A jelszó-visszaállítás felhasználói felület, az SMS-üzenetek és a hanghívások az Office 365 által támogatott nyelveken vannak honosítva.
  >
  >
* **K:  A jelszó-visszaállítási élmény mely részeit kell beolvasni, amikor beállítja a szervezeti védjegyezési elemeket a címtár konfigurálás lapján?**

  > **V:** A jelszó-visszaállítási portál megjeleníti a szervezet emblémáját, és lehetővé teszi a "Kapcsolatfelvétel a rendszergazdával" hivatkozást, hogy egy egyéni e-mailt vagy URL-címet mutasson. A jelszó-visszaállítás által küldött e-mailek tartalmazzák a szervezet emblémáját, színét és nevét az e-mailek törzsében, és testre szabhatók az adott névre vonatkozó beállításokkal.
  >
  >
* **K:  Hogyan vehetem fel a felhasználókat arra, hogy hová szeretnék visszaállítani a jelszavukat?**

  > **V:** Próbálja ki a [SSPR üzembe helyezési](howto-sspr-deployment.md#communications-plan) cikkének néhány javaslatát.
  >
  >
* **K:  Használhatom ezt a lapot egy mobileszközön?**

  > **V:** Igen, ez a lap mobileszközökön működik.
  >
  >
* **K:  Támogatja a helyi Active Directory fiókok zárolásának feloldását, amikor a felhasználók visszaállítják a jelszavukat?**

  > **V:** Igen. Ha a felhasználó alaphelyzetbe állítja a jelszavát, ha a jelszó visszaírási Azure AD Connecton keresztül lett telepítve, akkor a felhasználó fiókja automatikusan fel lesz oldva a jelszavuk visszaállításakor.
  >
  >
* **K:  Hogyan integrálható a jelszó alaphelyzetbe állítása közvetlenül a felhasználó asztali bejelentkezési felületén?**

  > **V:** Ha Ön prémium szintű Azure AD ügyfél, a Microsoft Identity Manager ingyenesen telepítheti, és üzembe helyezheti a helyszíni jelszó-visszaállítási megoldást.
  >
  >
* **K:  Megadhatok különböző biztonsági kérdéseket különböző területi beállításokhoz?**

  > **V:** Nem, ez ma nem lehetséges.
  >
  >
* **K:  Hány kérdés konfigurálható a biztonsági kérdések hitelesítése beállításhoz?**

  > **V:** A [Azure Portal](https://portal.azure.com)akár 20 egyéni biztonsági kérdést is beállíthat.
  >
  >
* **K:  Meddig lehet biztonsági kérdéseket feltenni?**

  > **V:** A biztonsági kérdések 3 – 200 karakter hosszúságú lehetnek.
  >
  >
* **K:  Milyen hosszú lehet a biztonsági kérdésekre adott válaszok?**

  > **V:** A válaszok 3 – 40 karakter hosszúak lehetnek.
  >
  >
* **K:  A rendszer duplikált válaszokat fogad el a biztonsági kérdésekre?**

  > **V:** Igen, a biztonsági kérdésekre való ismétlődő válaszokat elutasítjuk.
  >
  >
* **K:  A felhasználók többször is regisztrálhatják ugyanazt a biztonsági kérdést?**

  > **V:** Nem. Ha egy felhasználó egy adott kérdést regisztrál, akkor Másodszor nem regisztrálhat erre a kérdésre.
  >
  >
* **K:  Be lehet állítani a regisztrációhoz és az alaphelyzetbe állításhoz szükséges biztonsági kérdések minimális határértékét?**

  > **V:** Igen, egy korlátot állíthat be a regisztrációhoz, és egy másikat az alaphelyzetbe állításhoz. A regisztrációhoz három – öt biztonsági kérdés lehet szükséges, és az alaphelyzetbe állításhoz három – öt kérdés is szükséges.
  >
  >
* **K:  Úgy konfiguráltam a szabályzatot, hogy a felhasználók az alaphelyzetbe állításhoz biztonsági kérdéseket használjanak, de úgy tűnik, hogy az Azure-rendszergazdák másképp vannak konfigurálva.**

  > **V:** Ez a várt viselkedés. A Microsoft az új jelszó kérésére vonatkozó erős, alapértelmezett, két kapus szabályzat alkalmazását írja elő minden Azure rendszergazdai szerepkörhöz. Ez megakadályozza, hogy a rendszergazdák biztonsági kérdéseket használjanak. A szabályzattal kapcsolatos további információkért tekintse meg Azure Active Directory cikkben található [jelszóházirend és korlátozásokat](concept-sspr-policy.md) .
  >
  >
* **K:  Ha a felhasználó az alaphelyzetbe állításhoz szükséges maximális számú kérdésnél több regisztrált, hogyan történik az alaphelyzetbe állításkor kiválasztott biztonsági kérdések használata?**

  > **V:** *N* a biztonsági kérdések száma véletlenszerűen van kiválasztva a felhasználó által regisztrált kérdések teljes száma alapján, ahol *N* a beállítás **alaphelyzetbe állításához szükséges kérdések számára** beállított mennyiség. Ha például egy felhasználó öt biztonsági kérdést regisztrált, de csak három szükséges a jelszó alaphelyzetbe állításához, az öt kérdés közül három véletlenszerűen van kiválasztva, és az alaphelyzetbe áll. Ha meg szeretné akadályozni, hogy a felhasználó válaszoljon a kérdésekre adott válaszokra, akkor nem kell megelőznie a kiválasztási folyamat megkezdését.
  >
  >
* **K:  Mennyi ideig érvényes az e-mail és az SMS egyszeri jelszava?**

  > **V:** A jelszó-visszaállítási munkamenet élettartama 15 perc. A jelszó-visszaállítási művelet elejétől a felhasználó 15 perc alatt visszaállíthatja a jelszavát. Az e-mailek és az SMS egyszeri jelszava 5 percig érvényes a jelszó-visszaállítási munkamenet során.
  >
  >
* **K:  Letilthatom a felhasználókat a jelszavuk alaphelyzetbe állításához?**

  > **V:** Igen, ha egy csoportot használ a SSPR engedélyezéséhez, eltávolíthatja az egyes felhasználókat a csoportból, amely lehetővé teszi a felhasználók számára a jelszavuk visszaállítását. Ha a felhasználó globális rendszergazda, a rendszer megőrzi a jelszavát, és ezt nem lehet letiltani.
  >
  >

## <a name="password-change"></a>Jelszó módosítása

* **K:  Hol kell a felhasználóknak megváltoztatniuk a jelszavukat?**

  > **V:** A felhasználók bármikor megváltoztathatják a jelszavukat, ahogy a profiljuk képe vagy ikonja látható, például az [Office 365](https://portal.office.com) portál vagy a [hozzáférési panel](https://myapps.microsoft.com) jobb felső sarkában. A felhasználók a [hozzáférési panel profilja lapon](https://account.activedirectory.windowsazure.com/r#/profile)módosíthatják a jelszavukat. A felhasználókat arra is kérheti, hogy az Azure AD bejelentkezési oldalán automatikusan megváltoztassák a jelszavukat, ha a jelszavuk lejárt. Végezetül a felhasználók közvetlenül is megkereshetik az [Azure ad jelszó-módosítási portált](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) , ha módosítani szeretnék jelszavukat.
  >
  >
* **K:  Kaphatok értesítést a felhasználók az Office-portálról, ha a helyszíni jelszava lejár?**

  > **V:** Igen, ez ma is lehetséges, ha Active Directory összevonási szolgáltatások (AD FS)t (AD FS) használ. Ha AD FS használ, kövesse a [jelszó-házirend jogcímeinek küldése a AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) cikk utasításait. Ha jelszó-kivonatoló szinkronizálást használ, ez jelenleg nem lehetséges. A helyi címtárakból nem szinkronizáljuk a jelszóházirendek, így nem lehet lejárati értesítéseket közzétenni a Felhőbeli élmények között. Mindkét esetben lehetősége van arra is, hogy [értesítse azokat a felhasználókat, akiknek a jelszava hamarosan lejár a powershellen keresztül](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **K:  Letilthatom a felhasználókat a jelszavuk módosítására?**

  > **V:** A csak felhőalapú felhasználók esetében a jelszó módosításait nem lehet blokkolni. A helyszíni felhasználók esetében beállíthatja, hogy a **felhasználó ne változtassa** meg a jelszót lehetőség kiválasztását. A kiválasztott felhasználók nem módosíthatják a jelszavukat.
  >
  >

## <a name="password-management-reports"></a>Jelszavas kezelési jelentések

* **K:  Mennyi időt vesz igénybe az adatkezelési jelentések a jelszóházirend-jelentésekben?**

  > **V:** Az információknak 5 – 10 percen belül kell szerepelniük a jelszó-kezelési jelentésekben. Bizonyos esetekben akár egy órát is igénybe vehet.
  >
  >
* **K:  Hogyan szűrhetim a jelszavas kezelési jelentéseket?**

  > **V:** A jelszó-kezelési jelentések szűréséhez válassza ki a kis Nagyítót az oszlopok feliratának jobb szélén, a jelentés tetején. Ha gazdagabb szűrést szeretne végezni, töltse le a jelentést az Excelbe, és hozzon létre egy pivot táblát.
  >
  >
* **K: Mi a jelszó-kezelési jelentésekben tárolt események maximális száma?**

  > **V:** Akár 75 000 jelszó-visszaállítási, akár a jelszó-visszaállítási regisztrációs eseményt a jelszó-kezelési jelentésekben tárolja a rendszer, és 30 napig visszamenőleg visszanyúlik. Dolgozunk a szám kibontásán, hogy több eseményt is tartalmazzon.
  >
  >
* **K:  Milyen messzire kerül a jelszó-kezelési jelentések?**

  > **V:** A jelszóházirend-jelentések az elmúlt 30 napban bekövetkezett műveleteket jelenítik meg. Ha most archiválni szeretné ezeket az adatokkal, rendszeresen letöltheti a jelentéseket, és külön helyen mentheti őket.
  >
  >
* **K:  A jelszó-kezelési jelentésekben legfeljebb hány sor szerepelhet?**

  > **V:** Igen. A jelszóházirend-jelentésekben legfeljebb 75 000 sor szerepelhet, függetlenül attól, hogy megjelenjenek-e a felhasználói felületen, vagy le vannak-e töltve.
  >
  >
* **K:  Van olyan API, amely hozzáfér a jelszó-visszaállítási vagy a regisztrációs jelentéskészítési adatszolgáltatáshoz?**

  > **V:** Igen. A jelszó-visszaállítási jelentéskészítési adatfolyam elérésének megismeréséhez tekintse meg a következőt: [Tudnivalók a jelszó-visszaállítási jelentéskészítési események programozott módon való eléréséről](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).
  >
  >

## <a name="password-writeback"></a>Jelszóvisszaírás

* **K:  Hogyan működik a jelszó visszaírási a színfalak mögött?**

  > **V:** Tekintse meg a [jelszó-visszaírási működését](howto-sspr-writeback.md) ismertető cikket, amelyből megtudhatja, mi történik a jelszó-visszaírási engedélyezésekor, valamint az, hogy az adatforgalom Hogyan zajlik vissza a rendszeren a helyszíni környezetbe.
  >
  >
* **K:  Mennyi ideig tart a jelszó visszaírási? Van olyan szinkronizálási késés, mint a jelszó-kivonattal való szinkronizálás?**

  > **V:** A jelszó visszaírási azonnali. Ez egy szinkron folyamat, amely alapvetően eltérő módon működik, mint a jelszó-kivonat szinkronizálása. A jelszó visszaírási lehetővé teszi a felhasználók számára, hogy valós idejű visszajelzést kapjanak a jelszó-visszaállítási vagy-módosítási művelet sikerességéről. A jelszó sikeres visszaírási átlagos ideje 500 MS.
  >
  >
* **K:  Ha a helyszíni fiók le van tiltva, milyen hatással van a Felhőbeli fiókra és a hozzáférésre?**

  > **V:** Ha a helyszíni azonosító le van tiltva, a felhő-azonosító és a hozzáférés a következő szinkronizálási időszakban is le lesz tiltva Azure AD Connecton keresztül. Alapértelmezés szerint ez a szinkronizálás 30 percenként történik.
  >
  >
* **K:  Ha a helyszíni fiókot egy helyi Active Directory jelszóházirend korlátozza, akkor a SSPR betartja ezt a szabályzatot, amikor módosítja a jelszavam?**

  > **V:** Igen, a SSPR támaszkodik és betartja a helyszíni Active Directory jelszóházirend. Ez a szabályzat tartalmazza a tipikus Active Directory tartományi jelszavas házirendet, valamint a felhasználókra irányuló meghatározott, részletes jelszóházirendek.
  >
  >
* **K:  Milyen típusú fiókok jelszavas visszaírási működnek?**

  > **V:** A jelszó-visszaírási a helyszíni Active Directoryról az Azure AD-be szinkronizált felhasználói fiókok esetében működik, beleértve az összevont, a jelszó-kivonattal szinkronizált és a továbbított autentication-felhasználókat.
  >
  >
* **K:  A jelszó visszaírási kényszeríti a tartományom jelszavas házirendjeit?**

  > **V:** Igen. A jelszó visszaírási kikényszeríti a jelszó korát, előzményeit, összetettségét, szűrőit, valamint minden más korlátozást, amelyet a helyi tartományban található jelszavakban lehet elhelyezni.
  >
  >
* **K:  Biztonságos a jelszó visszaírási?  Hogyan lehet biztos benne, hogy nem lesz feltörve?**

  > **V:** Igen, a jelszó visszaírási biztonságos. Ha többet szeretne megtudni a Password visszaírási szolgáltatás által megvalósított több biztonsági rétegről, tekintse meg a jelszó- [visszaírási biztonsági](concept-sspr-writeback.md#password-writeback-security) szakaszt a [jelszó-visszaírási áttekintése](howto-sspr-writeback.md) című cikkben.
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
