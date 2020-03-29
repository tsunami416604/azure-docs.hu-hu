---
title: Önkiszolgáló jelszó-visszaállítási jelentések – Azure Active Directory
description: Jelentés az Azure AD önkiszolgáló jelszó-visszaállítási eseményeiről
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27d219f46f82f4a74cb5dee0a6b11b673a4a3509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76155007"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Az Azure AD-jelszókezelés jelentéskészítési lehetőségei

A telepítés után számos szervezet szeretné tudni, hogyan, vagy ha önkiszolgáló jelszó-visszaállítás (SSPR) valóban használják. Az Azure Active Directory (Azure AD) által biztosított jelentéskészítési funkció segítségével előre összeállított jelentések használatával válaszolhatja meg a kérdéseket. Ha megfelelő licenccel rendelkezik, egyéni lekérdezéseket is létrehozhat.

![Az SSPR jelentése az Azure AD naplóinak használatával][Reporting]

Az [Azure Portalon](https://portal.azure.com/)található jelentések a következő kérdésekre válaszolhatnak:

> [!NOTE]
> [Globális rendszergazdának](../users-groups-roles/directory-assign-admin-roles.md)kell lennie, és engedélyeznie kell, hogy ezeket az adatokat a szervezet nevében összegyűjtsék. A jelentkezéshez legalább egyszer fel kell keresnie a **Jelentés** lapot vagy a naplónaplókat. Addig a rendszer nem gyűjt adatokat a szervezetről.
>

* Hányan regisztráltak jelszó-visszaállításra?
* Ki regisztrált a jelszó-visszaállításra?
* Milyen adatokat regisztrálnak az emberek?
* Hányan állították vissza a jelszavukat az elmúlt hét napban?
* Melyek a leggyakoribb módszerek, amelyekkel a felhasználók vagy a rendszergazdák a jelszavuk visszaállítására használják?
* Milyen gyakori problémákkal szembesülnek a felhasználók vagy a rendszergazdák a jelszó-visszaállítás iszsalékozása során?
* Milyen rendszergazdák alaphelyzetbe a saját jelszavakat gyakran?
* Van-e gyanús tevékenység folyik a jelszó-visszaállítás?

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>A jelszókezelési jelentések megtekintése az Azure Portalon

Az Azure Portalon továbbfejlesztettük a jelszó-visszaállítási és jelszó-visszaállítási regisztrációs tevékenység megtekintésének módját. A jelszó-visszaállítási és jelszó-visszaállítási regisztrációs események megkereséséhez kövesse az alábbi lépéseket:

1. Keresse fel az [Azure Portalt](https://portal.azure.com).
2. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget.
3. Keresse meg az **Azure Active Directoryt** a szolgáltatások listájában, és válassza ki.
4. Válassza a **Felhasználók** lehetőséget a Kezelés szakaszban.
5. Válassza **a Naplók naplózása** lehetőséget a **Felhasználók** panelen. Ez a címtár összes felhasználójával szemben bekövetkezett összes naplózási eseményt mutatja. Szűrheti ezt a nézetet az összes jelszóval kapcsolatos esemény megtekintéséhez.
6. Az ablaktábla tetején található **Szűrő** menüben válassza ki a **Szolgáltatás** legördülő listát, és módosítsa az **Önkiszolgáló Jelszókezelés** szolgáltatás típusára.
7. Szükség esetén további szűrést is okozhat a lista kiválasztásával az Önt érdeklő konkrét **tevékenység** kiválasztásával.

### <a name="converged-registration-preview"></a>Konvergens regisztráció (előzetes verzió)

Ha részt vesz a konvergens regisztráció nyilvános előzetes verziójában, a felhasználói tevékenységre vonatkozó információk a naplózási naplókban a **Biztonsági** > **hitelesítési módszerek**területen találhatók.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>A jelentésoszlopok leírása az Azure Portalon

Az alábbi lista részletesen ismerteti az Azure Portal jelentésoszlopait:

* **Felhasználó**: Az a felhasználó, aki jelszó-visszaállítási regisztrációs műveletet kísérelt meg.
* **Szerep**: A felhasználó szerepe a címtárban.
* **Dátum és idő:** A kísérlet dátuma és időpontja.
* **Regisztrált adatok**: A felhasználó által a jelszó-visszaállítási regisztráció során megadott hitelesítési adatok.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>A jelentésértékek leírása az Azure Portalon

Az alábbi táblázat ismerteti a különböző értékeket, amelyek az Azure Portal egyes oszlopaihoz beállíthatók:

| Oszlop | Megengedett értékek és jelentésük |
| --- | --- |
| Regisztrált adatok |**Másodlagos e-mail**: A felhasználó egy másodlagos e-mail vagy hitelesítési e-mailt használt a hitelesítéshez.<p><p>**Irodai telefon**: A felhasználó irodai telefont használt a hitelesítéshez.<p>**Mobiltelefon**: A felhasználó mobiltelefont vagy hitelesítési telefont használt a hitelesítéshez.<p>**Biztonsági kérdések**: A felhasználó biztonsági kérdéseket használt a hitelesítéshez.<p>**Az előző módszerek bármilyen kombinációja, például másodlagos e-mail + mobiltelefon**: Akkor következik be, amikor kétkapus házirend van megadva, és megmutatja, hogy a felhasználó milyen két módszert használta a jelszó-visszaállítási kérelem hitelesítésére. |

## <a name="self-service-password-management-activity-types"></a>Önkiszolgáló jelszókezelési tevékenységtípusok

A következő tevékenységtípusok jelennek meg az **Önkiszolgáló jelszókezelés** naplózási eseménykategóriában:

* [Az önkiszolgáló jelszó-visszaállítás blokkolva](#activity-type-blocked-from-self-service-password-reset): Azt jelzi, hogy a felhasználó 24 óra alatt több mint öt alkalommal próbált meg alaphelyzetbe állítani egy jelszót, egy adott kaput használni, vagy egy telefonszámot érvényesíteni.
* [Jelszó módosítása (önkiszolgáló)](#activity-type-change-password-self-service): Azt jelzi, hogy a felhasználó önkéntes vagy kényszerített (a lejárati) jelszómódosítást hajtott végre.
* [Jelszó alaphelyzetbe állítása (rendszergazda által)](#activity-type-reset-password-by-admin): Azt jelzi, hogy a rendszergazda végrehajtotta a jelszó-alaphelyzetbe állítást az Azure Portalon lévő felhasználó nevében.
* [Jelszó alaphelyzetbe állítása :](#activity-type-reset-password-self-service)Azt jelzi, hogy a felhasználó sikeresen visszaállította jelszavát az [Azure AD jelszó-visszaállítási portáljáról.](https://passwordreset.microsoftonline.com)
* [Önkiszolgáló jelszó-visszaállítási folyamat folyamatának folyamata :](#activity-type-self-serve-password-reset-flow-activity-progress)A jelszó-visszaállítási folyamat részeként a felhasználó által végighaladt minden egyes lépés, például egy adott jelszó-visszaállítási hitelesítési kapu átadása.
* [Felhasználói fiók zárolásának feloldása (önkiszolgáló)](#activity-type-unlock-a-user-account-self-service): Azt jelzi, hogy a felhasználó sikeresen feloldotta az Active Directory-fiók zárolását anélkül, hogy alaphelyzetbe állította volna a jelszavát az [Azure AD jelszó-visszaállítási portáljáról](https://passwordreset.microsoftonline.com) az Active Directory fiókfeloldási szolgáltatásának visszaállítás a visszaállítás nélkül.
* [Az önkiszolgáló jelszó-visszaállításra regisztrált felhasználó](#activity-type-user-registered-for-self-service-password-reset): Azt jelzi, hogy a felhasználó minden szükséges információt regisztrált ahhoz, hogy a megadott bérlői jelszó-visszaállítási házirendnek megfelelően alaphelyzetbe állíthassa jelszavát.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Tevékenység típusa: Az önkiszolgáló jelszó visszaállítása blokkolva van

Az alábbi lista részletesen ismerteti ezt a tevékenységet:

* **Tevékenység leírása**: Azt jelzi, hogy a felhasználó 24 óra alatt több mint öt alkalommal próbált meg alaphelyzetbe állítani egy jelszót, egy adott kaput használni, vagy egy telefonszámot érvényesíteni.
* **Tevékenységszereplő**: Az a felhasználó, aki további alaphelyzetbe állítási műveletek végrehajtásából lett szabályozva. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Tevékenységcél:** Az a felhasználó, aki további alaphelyzetbe állítási műveletek et hajtott végre. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Tevékenység állapota**:
  * _Sikeres_: Azt jelzi, hogy a felhasználó torkig volt a további alapbeállítások végrehajtásával, a további hitelesítési módszerek megkísérlésével vagy a következő 24 órában a további telefonszámok érvényesítésével.
* **Tevékenységállapot-hiba oka:** Nem alkalmazható.

### <a name="activity-type-change-password-self-service"></a>Tevékenység típusa: Jelszó módosítása (önkiszolgáló)

Az alábbi lista részletesen ismerteti ezt a tevékenységet:

* **Tevékenység leírása**: Azt jelzi, hogy a felhasználó önkéntes vagy kényszerített (a lejárati) jelszómódosítást hajtott végre.
* **Tevékenységszereplő**: Az a felhasználó, aki megváltoztatta a jelszavát. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Tevékenységcél**: Az a felhasználó, aki megváltoztatta a jelszavát. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Tevékenység állapotai**:
  * _Sikeres_: Azt jelzi, hogy a felhasználó sikeresen módosította a jelszavát.
  * _Hiba_: Azt jelzi, hogy a felhasználó nem tudta megváltoztatni a jelszavát. A sor kiválasztásával megtekintheti a **Tevékenység állapotok** kategóriáját, ha többet szeretne megtudni arról, hogy miért történt a hiba.
* **Tevékenységállapot-hiba oka**:
  * _FuzzyPolicyViolationInvalidPassword_: A felhasználó olyan jelszót választott ki, amelyet automatikusan kitiltottak, mert a Microsoft tiltott jelszóészlelési képességei túl gyakorinak vagy különösen gyengének találták.

### <a name="activity-type-reset-password-by-admin"></a>Tevékenység típusa: Jelszó alaphelyzetbe állítása (rendszergazda által)

Az alábbi lista részletesen ismerteti ezt a tevékenységet:

* **Tevékenység leírása:** Azt jelzi, hogy a rendszergazda végrehajtott a jelszó-visszaállítás nevében egy felhasználó az Azure Portalon.
* **Tevékenységszereplő**: Az a rendszergazda, aki egy másik végfelhasználó vagy rendszergazda nevében végrehajtotta a jelszó-visszaállítást. Jelszó-rendszergazdának, felhasználórendszergazdának vagy ügyfélszolgálati rendszergazdának kell lennie.
* **Tevékenységcél**: Az a felhasználó, akinek a jelszavát alaphelyzetbe állították. A felhasználó lehet végfelhasználó vagy másik rendszergazda.
* **Tevékenység állapotai**:
  * _Sikeres_: Azt jelzi, hogy egy rendszergazda sikeresen visszaállította a felhasználó jelszavát.
  * _Hiba_: Azt jelzi, hogy egy rendszergazda nem tudta módosítani a felhasználó jelszavát. A sor kiválasztásával megtekintheti a **Tevékenység állapotok** kategóriáját, ha többet szeretne megtudni arról, hogy miért történt a hiba.

### <a name="activity-type-reset-password-self-service"></a>Tevékenység típusa: Jelszó alaphelyzetbe állítása (önkiszolgáló)

Az alábbi lista részletesen ismerteti ezt a tevékenységet:

* **Tevékenység leírása**: Azt jelzi, hogy a felhasználó sikeresen visszaállította jelszavát az [Azure AD jelszó-visszaállítási portáljáról.](https://passwordreset.microsoftonline.com)
* **Tevékenységszereplő**: Az a felhasználó, aki új jelszót állít alaphelyzetbe. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Tevékenységcél**: Az a felhasználó, aki új jelszót állít alaphelyzetbe. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Tevékenység állapotai**:
  * _Sikeres_: Azt jelzi, hogy a felhasználó sikeresen visszaállította saját jelszavát.
  * _Hiba_: Azt jelzi, hogy a felhasználó nem tudta alaphelyzetbe állítani a saját jelszavát. A sor kiválasztásával megtekintheti a **Tevékenység állapotok** kategóriáját, ha többet szeretne megtudni arról, hogy miért történt a hiba.
* **Tevékenységállapot-hiba oka**:
  * _FuzzyPolicyViolationInvalidPassword_: Az admin kiválasztott egy jelszót, amely automatikusan betiltották, mert a Microsoft tiltott jelszó észlelése képességek találta, hogy túl gyakori, vagy különösen gyenge.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Tevékenység típusa: Saját kiszolgálásjelszó-visszaállítási folyamat folyamatának folyamata

Az alábbi lista részletesen ismerteti ezt a tevékenységet:

* **Tevékenység leírása**: A jelszó-visszaállítási folyamat részeként a felhasználó által végighaladva minden egyes lépés (például egy adott jelszó-visszaállítási hitelesítési kapu átadása).
* **Tevékenységszereplő**: Az a felhasználó, aki a jelszó-visszaállítási folyamat egy részét végrehajtotta. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Tevékenységcél**: Az a felhasználó, aki a jelszó-visszaállítási folyamat egy részét végrehajtotta. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Tevékenység állapotai**:
  * _Sikeres_: Azt jelzi, hogy a felhasználó sikeresen végrehajtotta a jelszó-visszaállítási folyamat egy adott lépését.
  * _Hiba_: Azt jelzi, hogy a jelszó-visszaállítási folyamat egy adott lépése nem sikerült. A sor kiválasztásával megtekintheti a **Tevékenység állapotok** kategóriáját, ha többet szeretne megtudni arról, hogy miért történt a hiba.
* **Tevékenység állapotának okai**: Az alábbi táblázatban [az összes megengedett alaphelyzetbe állítási tevékenység állapotának okait lásd](#description-of-the-report-columns-in-the-azure-portal).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Tevékenység típusa: Felhasználói fiók zárolásának feloldása (önkiszolgáló)

Az alábbi lista részletesen ismerteti ezt a tevékenységet:

* **Tevékenység leírása:** Azt jelzi, hogy a felhasználó sikeresen feloldotta az Active Directory-fiók visszaállítása nélkül a jelszót az [Azure AD jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com) on az Active Directory szolgáltatás a fiók feloldása alaphelyzetbe állítás nélkül.
* **Tevékenységszereplő**: Az a felhasználó, aki a jelszavának alaphelyzetbe állítása nélkül feloldotta a fiókját. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Tevékenységcél**: Az a felhasználó, aki a jelszavának alaphelyzetbe állítása nélkül feloldotta a fiókját. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Engedélyezett tevékenységállapotok:**
  * _Sikeres_: Azt jelzi, hogy a felhasználó sikeresen feloldotta a saját fiókját.
  * _Hiba_: Azt jelzi, hogy a felhasználó nem tudta feloldani a fiókját. A sor kiválasztásával megtekintheti a **Tevékenység állapotok** kategóriáját, ha többet szeretne megtudni arról, hogy miért történt a hiba.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Tevékenység típusa: Az önkiszolgáló jelszó-visszaállításhoz regisztrált felhasználó

Az alábbi lista részletesen ismerteti ezt a tevékenységet:

* **Tevékenység leírása**: Azt jelzi, hogy a felhasználó regisztrált a szükséges adatokat, hogy képes legyen visszaállítani a jelszavát a jelenleg megadott bérlői jelszó-visszaállítási házirendnek megfelelően. 
* **Tevékenységszereplő**: Az a felhasználó, aki regisztrált a jelszó-visszaállításhoz. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Tevékenységcél**: Az a felhasználó, aki jelszó-visszaállításra regisztrált. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Engedélyezett tevékenységállapotok:**
  * _Sikeres_: Azt jelzi, hogy egy felhasználó sikeresen regisztrált a jelszó-visszaállításhoz az aktuális házirendnek megfelelően. 
  * _Hiba_: Azt jelzi, hogy egy felhasználó nem tudott regisztrálni a jelszó alaphelyzetbe állításához. A sor kiválasztásával megtekintheti a **Tevékenység állapotok** kategóriáját, ha többet szeretne megtudni arról, hogy miért történt a hiba.

     >[!NOTE]
     >A hiba nem jelenti azt, hogy a felhasználó nem tudja alaphelyzetbe állítani saját jelszavát. Ez azt jelenti, hogy nem fejezték be a regisztrációs folyamatot. Ha a fiókjukban helyes, nem ellenőrzött adatok vannak, például egy nem érvényesített telefonszám, annak ellenére, hogy nem ellenőrizték ezt a telefonszámot, továbbra is használhatják a jelszó visszaállításához.

## <a name="next-steps"></a>További lépések

* [SSPR és MFA-használat és elemzési jelentések](howto-authentication-methods-usage-insights.md)
* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Új jelszó kérése vagy jelszó módosítása](../user-help/active-directory-passwords-update-your-own-password.md).
* [Regisztráció új jelszó önkiszolgáló kérésére](../user-help/active-directory-passwords-reset-register.md).
* [Van engedélyezési kérdése?](concept-sspr-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](howto-sspr-authenticationdata.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Azt hiszem, valami eltört. Hogyan háríthatók el az SSPR hibái?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Példa az SSPR-tevékenység naplózási naplóira az Azure AD-ben"
