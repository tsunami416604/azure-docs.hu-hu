---
title: Az önkiszolgáló jelszó-visszaállítási jelentések – az Azure Active Directory
description: -Jelentések az Azure AD önkiszolgáló jelszó-visszaállítási események
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bc2acaa24637c3297af8e91f01b67e5d30f2931
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59577967"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Jelentéskészítési lehetőségek az Azure AD-jelszókezelés

Az üzembe helyezést követően számos szervezet szeretné tudni, hogy hogyan valóban használja, vagy ha az önkiszolgáló jelszó-visszaállítási (SSPR). A jelentéskészítési funkció az Azure Active Directory (Azure AD) biztosít segít előre elkészített jelentéseket a kérdésre. Ha a megfelelően licencelt, is létrehozhat egyéni lekérdezéseket.

![Jelentéskészítés az sspr-ben a naplózási használatával naplózza az Azure ad-ben][Reporting]

Az alábbi kérdésekre választ, amelyek szerepelnek a jelentésekben a [az Azure portal](https://portal.azure.com/):

> [!NOTE]
> Kell [globális rendszergazda](../users-groups-roles/directory-assign-admin-roles.md), és akkor jóvá kell hagyatnia a adatok gyűjthetők a szervezet nevében. Elvégezni a beléptetést, lépjen a **jelentéskészítési** tab vagy az ellenőrzés során legalább egyszer naplózza. Addig nem adatgyűjtés a szervezet számára.
>

* Új jelszó kéréséhez regisztrált hányan?
* Aki regisztrálva van a jelszó-visszaállításhoz?
* Milyen adatokat személyek regisztrálja?
* Hányan jelszavaikat az elmúlt hét napban?
* Mik a leggyakoribb módszereket használó felhasználók vagy rendszergazdák új jelszót kérjenek?
* Mik a gyakori problémák felhasználók vagy rendszergazdák face megkísérlésekor. jelszó-visszaállítás használatához?
* Milyen a rendszergazdák gyakran alaphelyzetbe állítja a saját jelszavukat?
* Van-e a jelszó-visszaállítás történik gyanús tevékenységet?

## <a name="power-bi-content-pack"></a>A Power BI-tartalomcsomag

Ha Ön a Power BI-felhasználó, van egy tartalomcsomagot, amely tartalmazza a könnyen használható jelentéskészítési SSPR az Azure AD-hez. Használja, és üzembe helyezése a tartalomcsomag további információkért lásd: [használata az Azure Active Directory Power BI-tartalomcsomag](../reports-monitoring/howto-power-bi-content-pack.md). A tartalomcsomag létrehozása a saját irányítópultjait, és megoszthatja másokkal a szervezetében.

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Jelszókezelési jelentések megtekintése az Azure Portalon

Az Azure portal felületének a továbbfejlesztettük, hogy megtekintheti a jelszó-visszaállítás, és a jelszó-visszaállítási regisztrációs tevékenység módját. Használja a következő, a lépéseket kell megkeresnie a jelszót alaphelyzetbe, és a jelszó-visszaállítási regisztrációs események:

1. Keresse fel az [Azure Portalt](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** a bal oldali panelen.
3. Keresse meg **Azure Active Directory** a szolgáltatások listájában, és jelölje ki.
4. Válassza ki **felhasználók** , a kezelés szakaszhoz.
5. Válassza ki **Auditnaplók** származó a **felhasználók** panelen. Ez bemutatja, az összes naplózási esemény történt a címtárban szereplő összes felhasználó ellen. Ez a nézet a jelszóval kapcsolatos események megtekintéséhez szűrheti.
6. Az a **szűrő** menü felső részén a panelt, válassza a **szolgáltatás** legördülő listában, és módosítsa a **önkiszolgáló jelszókezelés** szolgáltatás típusa.
7. Szükség esetén a lista további szűréséhez válassza ki az adott **tevékenység** érdekli.

### <a name="converged-registration-preview"></a>Konvergens regisztrációs (előzetes verzió)

Ha részvételével konvergens regisztráció a nyilvános előzetes verzióban, felhasználói tevékenységet a naplók kapcsolatos információkat a szolgáltatás alatt találhatók **hitelesítési módszerek**.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Az Azure Portalon a jelentési oszlopok leírása

Az alábbi lista ismerteti részletesen ismertetjük az Azure Portalon jelentés oszlopokhoz:

* **Felhasználói**: Kísérlet a jelszó alaphelyzetbe állítása a regisztrációs művelet.
* **Szerepkör**: A szerepkör a felhasználó a címtárban.
* **Dátum és idő**: A dátum és idő a kísérlet.
* **Regisztrált adatok**: A hitelesítési adatokat, hogy a felhasználó által megadott során jelszó alaphelyzetbe állításának regisztrációja.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>A jelentés értékeket az Azure Portalon leírása

A következő táblázat ismerteti a különböző értékek, amelyek beállíthatja az egyes oszlopok az Azure Portalon:

| Oszlop | Engedélyezett értékeket és jelentésüket |
| --- | --- |
| Regisztrált adatok |**Másodlagos e-mail cím**: A felhasználó egy másodlagos vagy hitelesítési e-mail hitelesítésére használja.<p><p>**Irodai telefon**: A felhasználó hitelesítésére az irodai telefont használja.<p>**Mobiltelefon**: A felhasználó mobiltelefonon vagy hitelesítéshez használt telefon hitelesítésére használja.<p>**Biztonsági kérdések**: A felhasználó hitelesítésére használt biztonsági kérdéseket.<p>**A korábbi metódusok bármely kombinációja például alternatív e-mail + mobile phone**: Amikor egy két-kezdő szabályzat van megadva, és bemutatja, milyen két módszerek használt felhasználói hitelesítés a jelszó-visszaállítási kérés. |

## <a name="self-service-password-management-activity-types"></a>Önkiszolgáló jelszókezelési tevékenységtípusok

A következő típusú tevékenységek szerepelnek a **önkiszolgáló jelszókezelés** naplózási esemény kategóriája:

* [Új jelszó önkiszolgáló kérésének blokkolása](#activity-type-blocked-from-self-service-password-reset): Azt jelzi, hogy egy rendszergazda felhasználó megpróbálta jelszó alaphelyzetbe állítása, használjon egy adott kapu vagy telefonszám ellenőrzése a több mint teljes ötször (24 óra).
* [Jelszó módosítása (önkiszolgáló)](#activity-type-change-password-self-service): Azt jelzi, hogy egy felhasználó végre önkéntes, vagy a kényszerített (miatt lejárati) jelszó módosítása.
* [Jelszó alaphelyzetbe állítása (a rendszergazda által)](#activity-type-reset-password-by-admin): Azt jelzi, hogy a rendszergazda végzett jelszó-visszaállításra, az Azure Portalról egy felhasználó nevében.
* [Jelszó alaphelyzetbe állítása (önkiszolgáló)](#activity-type-reset-password-self-service): Azt jelzi, hogy a felhasználó sikeresen visszaállítva jelszavukat a a [az Azure AD jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com).
* [Új jelszó önkiszolgáló kérésének tevékenységállapota](#activity-type-self-serve-password-reset-flow-activity-progress): Azt jelzi, minden egyes felhasználó halad keresztül, adott lépésre, mint például a hitelesítési kapu, átadja egy meghatározott jelszót alaphelyzetbe állítani, mivel az része a jelszó alaphelyzetbe állítása folyamatban.
* [Felhasználói Fiókzárolás feloldása (önkiszolgáló)](#activity-type-unlock-a-user-account-self-service)): Azt jelzi, hogy a felhasználó sikeresen feloldotta a saját Active Directory-fiókot anélkül, hogy a jelszó alaphelyzetbe állítása a [az Azure AD jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com) funkcióval az Active Directory-fiók zárolásának feloldása beállítások visszaállítása nélkül.
* [Felhasználó regisztrált az önkiszolgáló jelszó-visszaállítás](#activity-type-user-registered-for-self-service-password-reset): Azt jelzi, hogy egy felhasználó regisztrálva van-e a szükséges információkat is visszaállíthatják a jelszavukat a jelenleg megadott tenant jelszó kérésére vonatkozó szabályzat megfelelően.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Tevékenység típusa: Új jelszó önkiszolgáló kérésének blokkolása

Az alábbi lista ismerteti ezt a tevékenységet, részletesen:

* **A tevékenység leírása**: Azt jelzi, hogy egy rendszergazda felhasználó megpróbálta jelszó alaphelyzetbe állítása, használjon egy adott kapu vagy telefonszám ellenőrzése a több mint teljes ötször (24 óra).
* **Tevékenység aktor**: A felhasználó, aki végrehajtani további szabályozta visszaállítási műveletről. A felhasználó lehet a felhasználó vagy rendszergazda.
* **Tevékenység cél**: A felhasználó, aki végrehajtani további szabályozta visszaállítási műveletről. A felhasználó lehet a felhasználó vagy rendszergazda.
* **A tevékenység állapota**:
  * _Success_: Azt jelzi, hogy egy felhasználó hajt végre semmilyen további alaphelyzetbe állítása, minden további hitelesítési módszerek kísérlet vagy bármilyen további telefonszámok érvényesítése a következő 24 órában szabályozta.
* **Tevékenység állapota a hiba oka**: Nem alkalmazható.

### <a name="activity-type-change-password-self-service"></a>Tevékenység típusa: Jelszó módosítása (önkiszolgáló)

Az alábbi lista ismerteti ezt a tevékenységet, részletesen:

* **A tevékenység leírása**: Azt jelzi, hogy egy felhasználó végre önkéntes, vagy a kényszerített (miatt lejárati) jelszó módosítása.
* **Tevékenység aktor**: A felhasználó módosította a jelszavát. A felhasználó lehet a felhasználó vagy rendszergazda.
* **Tevékenység cél**: A felhasználó módosította a jelszavát. A felhasználó lehet a felhasználó vagy rendszergazda.
* **Tevékenység állapota**:
  * _Success_: Azt jelzi, hogy a felhasználó sikeresen módosította a jelszavát.
  * _Hiba_: Azt jelzi, hogy a felhasználó nem sikerült módosítani a jelszavát. Kiválaszthatja, hogy a sor megtekintéséhez a **tevékenység állapota OK** kategória tudhat meg többet a hibát.
* **Tevékenység állapota a hiba oka**:
  * _FuzzyPolicyViolationInvalidPassword_: A felhasználó kiválasztott, egy jelszót, amelyet a rendszer automatikusan le van tiltva, mert a Microsoft le van tiltva jelszó észlelési képességek található, hogy túl gyakori vagy különösen gyenge lehet.

### <a name="activity-type-reset-password-by-admin"></a>Tevékenység típusa: Jelszó átállítása (a rendszergazda által)

Az alábbi lista ismerteti ezt a tevékenységet, részletesen:

* **A tevékenység leírása**: Azt jelzi, hogy a rendszergazda végzett jelszó-visszaállításra, az Azure Portalról egy felhasználó nevében.
* **Tevékenység aktor**: A rendszergazda, aki a jelszó-visszaállítást egy másik felhasználó vagy rendszergazda nevében végrehajtani. A jelszókezelő, a felhasználó rendszergazda vagy a ügyfélszolgálati adminisztrátor kell lennie.
* **Tevékenység cél**: A felhasználó, akinek jelszó átállítása. A felhasználó lehet a felhasználó vagy egy másik rendszergazda.
* **Tevékenység állapota**:
  * _Success_: Azt jelzi, hogy egy rendszergazda felhasználó új jelszavának kérése sikerült.
  * _Hiba_: Azt jelzi, hogy egy rendszergazda a jelszó módosítása sikertelen volt. Kiválaszthatja, hogy a sor megtekintéséhez a **tevékenység állapota OK** kategória tudhat meg többet a hibát.

### <a name="activity-type-reset-password-self-service"></a>Tevékenység típusa: Új jelszó kérése (önkiszolgáló)

Az alábbi lista ismerteti ezt a tevékenységet, részletesen:

* **A tevékenység leírása**: Azt jelzi, hogy a felhasználó sikeresen visszaállítva jelszavukat a a [az Azure AD jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com).
* **Tevékenység aktor**: A felhasználó új jelszót kérnek. A felhasználó lehet a felhasználó vagy rendszergazda.
* **Tevékenység cél**: A felhasználó új jelszót kérnek. A felhasználó lehet a felhasználó vagy rendszergazda.
* **Tevékenység állapota**:
  * _Success_: Azt jelzi, hogy a felhasználó sikeresen visszaállítva jelszavukat.
  * _Hiba_: Azt jelzi, hogy a felhasználó a saját jelszó alaphelyzetbe állítása sikertelen volt. Kiválaszthatja, hogy a sor megtekintéséhez a **tevékenység állapota OK** kategória tudhat meg többet a hibát.
* **Tevékenység állapota a hiba oka**:
  * _FuzzyPolicyViolationInvalidPassword_: A rendszergazda egy jelszót, amelyet a rendszer automatikusan le van tiltva, mert a Microsoft le van tiltva jelszó észlelési képességek található, hogy túl gyakori vagy különösen gyenge lehet kiválasztani.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Tevékenység típusa: Önkiszolgáló működésre jelszó kérésének tevékenységállapota

Az alábbi lista ismerteti ezt a tevékenységet, részletesen:

* **A tevékenység leírása**: Azt jelzi, minden egyes adott lépésre, a felhasználó végighalad (mint például a hitelesítési kapu átadása egy adott jelszó alaphelyzetbe állítása), része a jelszó alaphelyzetbe állítási eljárás szerint.
* **Tevékenység aktor**: A jelszó részét végző felhasználó flow alaphelyzetbe állítása. A felhasználó lehet a felhasználó vagy rendszergazda.
* **Tevékenység cél**: A jelszó részét végző felhasználó flow alaphelyzetbe állítása. A felhasználó lehet a felhasználó vagy rendszergazda.
* **Tevékenység állapota**:
  * _Success_: Azt jelzi, hogy a felhasználó sikeresen befejeződött-e egy adott lépésre, a jelszóvisszaállítási folyamatot.
  * _Hiba_: Azt jelzi, hogy egy adott lépésre a jelszó alaphelyzetbe állítása nem sikerült a folyamat. Kiválaszthatja, hogy a sor megtekintéséhez a **tevékenység állapota OK** kategória tudhat meg többet a hibát.
* **Tevékenység állapota okokból**:   Az alábbi táblázat a [minden a megengedett alaphelyzetbe állítása tevékenység állapota okok](#description-of-the-report-columns-in-the-azure-portal).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Tevékenység típusa: Zárolás feloldása egy felhasználói fiókot (önkiszolgáló)

Az alábbi lista ismerteti ezt a tevékenységet, részletesen:

* **A tevékenység leírása**: Azt jelzi, hogy a felhasználó sikeresen feloldotta a saját Active Directory-fiókot anélkül, hogy a jelszó alaphelyzetbe állítása a [az Azure AD jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com) funkcióval az Active Directory-fiók zárolásának feloldása beállítások visszaállítása nélkül.
* **Tevékenység aktor**: A felhasználó, aki a fiók zárolását új jelszó nélkül. A felhasználó lehet a felhasználó vagy rendszergazda.
* **Tevékenység cél**: A felhasználó, aki a fiók zárolását új jelszó nélkül. A felhasználó lehet a felhasználó vagy rendszergazda.
* **Tevékenység állapota engedélyezett**:
  * _Success_: Azt jelzi, hogy a felhasználó sikeresen feloldotta a saját fiókjukat.
  * _Hiba_: Azt jelzi, hogy a felhasználó saját fiók feloldása sikertelen volt. Kiválaszthatja, hogy a sor megtekintéséhez a **tevékenység állapota OK** kategória tudhat meg többet a hibát.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Tevékenység típusa: A felhasználó új jelszó önkiszolgáló kérésére regisztrált

Az alábbi lista ismerteti ezt a tevékenységet, részletesen:

* **A tevékenység leírása**: Azt jelzi, hogy egy felhasználó regisztrálva van-e a szükséges információkat is visszaállíthatják a jelszavukat a jelenleg megadott tenant jelszó kérésére vonatkozó szabályzat megfelelően. 
* **Tevékenység aktor**: Új jelszó kérésére regisztráló felhasználó. A felhasználó lehet a felhasználó vagy rendszergazda.
* **Tevékenység cél**: Új jelszó kérésére regisztráló felhasználó. A felhasználó lehet a felhasználó vagy rendszergazda.
* **Tevékenység állapota engedélyezett**:
  * _Success_: Azt jelzi, hogy egy felhasználó sikeresen regisztrálva a jelszó-visszaállítást a jelenlegi szabályzatának megfelelően. 
  * _Hiba_: Azt jelzi, hogy a felhasználó nem sikerült regisztrálni a jelszó-visszaállításhoz. Kiválaszthatja, hogy a sor megtekintéséhez a **tevékenység állapota OK** kategória tudhat meg többet a hibát.

     >[!NOTE]
     >Hiba a felhasználó nem tudja saját jelszó visszaállítása nem jelenti azt. Ez azt jelenti, hogy azok a regisztrációs folyamat nem fejeződött be. Ha a fiókjuk helyességéről, például egy telefonszám, amely nincs érvényesítve, annak ellenére, hogy nem ellenőrizte, hogy ez a telefonszám nem ellenőrzött adatokat, azok továbbra is használhatja azt visszaállíthatják a jelszavukat.

## <a name="next-steps"></a>További lépések

* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Új jelszó kérése vagy jelszó módosítása](../user-help/active-directory-passwords-update-your-own-password.md).
* [Regisztráció új jelszó önkiszolgáló kérésére](../user-help/active-directory-passwords-reset-register.md).
* [Kérdése van a licenceléssel kapcsolatban?](concept-sspr-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](howto-sspr-authenticationdata.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Példa az SSPR aktivitásának vizsgálata naplózza az Azure ad-ben"
