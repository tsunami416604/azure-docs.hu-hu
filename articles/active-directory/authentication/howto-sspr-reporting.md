---
title: Az önkiszolgáló jelszó-visszaállítási jelentések – az Azure Active Directory
description: -Jelentések az Azure AD önkiszolgáló jelszó-visszaállítási események
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 2560296c088491b7ae0cd414f88ae337c1383c9a
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258796"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Jelentéskészítési lehetőségek az Azure AD-jelszókezelés

Az üzembe helyezést követően számos szervezet szeretné tudni, hogy hogyan valóban használja, vagy ha az önkiszolgáló jelszó-visszaállítási (SSPR). A jelentéskészítési funkció az Azure Active Directory (Azure AD) biztosít segít előre elkészített jelentéseket a kérdésre. Ha a megfelelően licencelt, is létrehozhat egyéni lekérdezéseket.

![Jelentéskészítés][Reporting]

Az alábbi kérdésekre választ, amely az [Azure Portalt] () szerepel a jelentések szerinthttps://portal.azure.com/):

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

Ha Ön a Power BI-felhasználó, van egy tartalomcsomagot, amely tartalmazza a könnyen használható jelentéskészítési SSPR az Azure AD-hez. Használja, és üzembe helyezése a tartalomcsomag további információkért lásd: [használata az Azure Active Directory Power BI-tartalomcsomag](../active-directory-reporting-power-bi-content-pack-how-to.md). A tartalomcsomag létrehozása a saját irányítópultjait, és megoszthatja másokkal a szervezetében.

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Jelszókezelési jelentések megtekintése az Azure Portalon

Az Azure portal felületének a továbbfejlesztettük, hogy megtekintheti a jelszó-visszaállítás, és a jelszó-visszaállítási regisztrációs tevékenység módját. Használja a következő, a lépéseket kell megkeresnie a jelszót alaphelyzetbe, és a jelszó-visszaállítási regisztrációs események:

1. Keresse meg a [az Azure portal](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** a bal oldali panelen.
3. Keresse meg **Azure Active Directory** a szolgáltatások listájában, és jelölje ki.
4. Válassza a **Felhasználók és csoportok** elemet.
5. Válassza ki **Auditnaplók** származó a **felhasználók és csoportok** menü. Ez bemutatja, az összes naplózási esemény történt a címtárban szereplő összes felhasználó ellen. Ez a nézet a jelszóval kapcsolatos események megtekintéséhez szűrheti.
6. Ez a nézet megtekintéséhez, csak a jelszó alaphelyzetbe állítása kapcsolatos események szűréséhez válassza a **szűrő** gombra a panel tetején.
7. Az a **szűrő** menüben válassza a **kategória** legördülő listában, és módosítsa a **önkiszolgáló jelszókezelés** kategória típusa.
8. Szükség esetén a lista további szűréséhez válassza ki az adott **tevékenység** érdekli.

### <a name="converged-registration-preview"></a>Konvergens regisztrációs (előzetes verzió)

Ha részvételével konvergens regisztráció a nyilvános előzetes verzióban, felhasználói tevékenységet a naplók kapcsolatos információkat fogja találni a kategóriához tartozó **hitelesítési módszerek**.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Az Azure Portalon a jelentési oszlopok leírása

Az alábbi lista ismerteti részletesen ismertetjük az Azure Portalon jelentés oszlopokhoz:

* **Felhasználói**: kísérlet egy jelszó alaphelyzetbe állítása a regisztrációs művelet.
* **Szerepkör**: A szerepkör a felhasználó a címtárban.
* **Dátum és idő**: A dátum és idő a kísérlet.
* **Adatok regisztrált**: az, hogy a felhasználó által megadott során jelszó-átállítási regisztrációk hitelesítési adatokat.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>A jelentés értékeket az Azure Portalon leírása

A következő táblázat ismerteti a különböző értékek, amelyek beállíthatja az egyes oszlopok az Azure Portalon:

| Oszlop | Engedélyezett értékeket és jelentésüket |
| --- | --- |
| Regisztrált adatok |**Másodlagos e-mail cím**: A felhasználó egy másodlagos vagy hitelesítési e-mail hitelesítéséhez használandó.<p><p>**Irodai telefon**: A felhasználó hitelesítésére az irodai telefont használható.<p>**Mobiltelefon**: A felhasználó mobiltelefonon vagy hitelesítéshez használt telefon hitelesítéséhez használandó.<p>**Biztonsági kérdések**: A felhasználó hitelesítésére a biztonsági kérdések használható.<p>**A korábbi metódusok bármely kombinációja például alternatív e-mail + mobile phone**: akkor fordul elő, amikor egy két-kezdő szabályzat van megadva, és bemutatja, milyen két módszerek használt felhasználói hitelesítés a jelszó-visszaállítási kérés. |

## <a name="self-service-password-management-activity-types"></a>Önkiszolgáló jelszókezelési tevékenységtípusok

A következő típusú tevékenységek szerepelnek a **önkiszolgáló jelszókezelés** naplózási esemény kategóriája:

* [Új jelszó önkiszolgáló kérésének blokkolása](#activity-type-blocked-from-self-service-password-reset): azt jelzi, hogy egy rendszergazda felhasználó megpróbálta jelszó alaphelyzetbe állítása, használjon egy adott kapu vagy telefonszám ellenőrzése a több mint teljes ötször (24 óra).
* [Módosítsa a jelszót (önkiszolgáló)](#activity-type-change-password-self-service): azt jelzi, hogy egy felhasználó végre önkéntes, vagy a kényszerített (miatt lejárati) jelszó módosítása.
* [Jelszó alaphelyzetbe állítása (a rendszergazda által)](#activity-type-reset-password-by-admin): azt jelzi, hogy a rendszergazda végzett jelszó-visszaállításra, az Azure Portalról egy felhasználó nevében.
* [Jelszó alaphelyzetbe állítása (önkiszolgáló)](#activity-type-reset-password-self-service): azt jelzi, hogy a felhasználó sikeresen visszaállítva jelszavukat a a [az Azure AD jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com).
* [Új jelszó önkiszolgáló kérésének tevékenységállapota](#activity-type-self-serve-password-reset-flow-activity-progress): azt jelzi, hogy minden egyes adott lépésre, a felhasználó végighalad, például a hitelesítési kapu, átadja egy meghatározott jelszót alaphelyzetbe állítani, mivel az része a jelszó alaphelyzetbe állítása folyamatban.
* [Felhasználói Fiókzárolás feloldása (önkiszolgáló)](#activity-type-unlock-user-account-self-service): azt jelzi, hogy a felhasználó sikeresen feloldotta a saját Active Directory-fiókot anélkül, hogy a jelszó alaphelyzetbe állítása a [az Azure AD jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com) az aktív használatával Címtár szolgáltatás fiók zárolásának feloldása beállítások visszaállítása nélkül.
* [Felhasználó regisztrált az önkiszolgáló jelszó-visszaállítás](#activity-type-user-registered-for-self-service-password-reset): azt jelzi, hogy egy felhasználó regisztrálva van-e a szükséges információkat is visszaállíthatják a jelszavukat a jelenleg megadott tenant jelszó kérésére vonatkozó szabályzat megfelelően.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Tevékenység típusa: új jelszó önkiszolgáló kérésének blokkolása

Az alábbi lista ismerteti ezt a tevékenységet, részletesen:

* **A tevékenység leírása**: azt jelzi, hogy egy rendszergazda felhasználó megpróbálta jelszó alaphelyzetbe állítása, használjon egy adott kapu vagy telefonszám ellenőrzése a több mint teljes ötször (24 óra).
* **Tevékenység aktor**: A felhasználó, aki végrehajtani további szabályozta visszaállítási műveletről. A felhasználó lehet a felhasználó vagy rendszergazda.
* **Tevékenység cél**: A felhasználó, aki végrehajtani további szabályozta visszaállítási műveletről. A felhasználó lehet a felhasználó vagy rendszergazda.
* **A tevékenység állapota**:
  * _Sikeres_: azt jelzi, hogy egy felhasználó hajt végre semmilyen további alaphelyzetbe állítása, minden további hitelesítési módszerek kísérlet vagy bármilyen további telefonszámok érvényesítése a következő 24 órában szabályozta.
* **Tevékenység állapota a hiba oka**: nem alkalmazható.

### <a name="activity-type-change-password-self-service"></a>Tevékenységtípus: jelszó módosítása (önkiszolgáló)

Az alábbi lista ismerteti ezt a tevékenységet, részletesen:

* **A tevékenység leírása**: azt jelzi, hogy egy felhasználó végre önkéntes, vagy a kényszerített (miatt lejárati) jelszó módosítása.
* **Tevékenység aktor**: A felhasználó módosította a jelszavát. A felhasználó lehet a felhasználó vagy rendszergazda.
* **Tevékenység cél**: A felhasználó módosította a jelszavát. A felhasználó lehet a felhasználó vagy rendszergazda.
* **Tevékenység állapota**:
  * _Sikeres_: azt jelzi, hogy a felhasználó sikeresen módosította a jelszavát.
  * _Hiba_: azt jelzi, hogy a felhasználó nem sikerült módosítani a jelszavát. Kiválaszthatja, hogy a sor megtekintéséhez a **tevékenység állapota OK** kategória tudhat meg többet a hibát.
* **Tevékenység állapota a hiba oka**: 
  * _FuzzyPolicyViolationInvalidPassword_: A felhasználó kiválasztott egy jelszót, amelyet a rendszer automatikusan le van tiltva, mert a Microsoft le van tiltva jelszó észlelési képességek található, hogy túl gyakori vagy különösen gyenge lehet.

### <a name="activity-type-reset-password-by-admin"></a>Tevékenységtípus: a jelszó alaphelyzetbe állítása (a rendszergazda által)

Az alábbi lista ismerteti ezt a tevékenységet, részletesen:

* **A tevékenység leírása**: azt jelzi, hogy a rendszergazda végzett jelszó-visszaállításra, az Azure Portalról egy felhasználó nevében.
* **Tevékenység aktor**: Rendszergazdák, a jelszó-visszaállítást egy másik felhasználó vagy rendszergazda nevében végrehajtani. Kell lennie, vagy egy globális rendszergazdai, jelszókezelő, felhasználói rendszergazdája vagy ügyfélszolgálati adminisztrátor.
* **Tevékenység cél**: A felhasználó, akinek jelszó átállítása. A felhasználó lehet a felhasználó vagy egy másik rendszergazda.
* **Tevékenység állapota**:
  * _Sikeres_: azt jelzi, hogy egy rendszergazda felhasználó új jelszavának kérése sikerült.
  * _Hiba_: azt jelzi, hogy egy rendszergazda a jelszó módosítása sikertelen volt. Kiválaszthatja, hogy a sor megtekintéséhez a **tevékenység állapota OK** kategória tudhat meg többet a hibát.

### <a name="activity-type-reset-password-self-service"></a>Tevékenységtípus: a jelszó alaphelyzetbe állítása (önkiszolgáló)

Az alábbi lista ismerteti ezt a tevékenységet, részletesen:

* **A tevékenység leírása**: azt jelzi, hogy a felhasználó sikeresen visszaállítva jelszavukat a a [az Azure AD jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com).
* **Tevékenység aktor**: A felhasználó, aki az új jelszót kérnek. A felhasználó lehet a felhasználó vagy rendszergazda.
* **Tevékenység cél**: A felhasználó, aki az új jelszót kérnek. A felhasználó lehet a felhasználó vagy rendszergazda.
* **Tevékenység állapota**:
  * _Sikeres_: azt jelzi, hogy a felhasználó sikeresen visszaállítva jelszavukat.
  * _Hiba_: azt jelzi, hogy a felhasználó a saját jelszó alaphelyzetbe állítása sikertelen volt. Kiválaszthatja, hogy a sor megtekintéséhez a **tevékenység állapota OK** kategória tudhat meg többet a hibát.
* **Tevékenység állapota a hiba oka**: 
  * _FuzzyPolicyViolationInvalidPassword_: A rendszergazda kiválasztott egy jelszót, amelyet a rendszer automatikusan le van tiltva, mert a Microsoft le van tiltva jelszó észlelési képességek található, hogy túl gyakori vagy különösen gyenge lehet.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Tevékenységtípus: önkiszolgáló szolgálja ki a jelszó alaphelyzetbe állítása tevékenységállapota

Az alábbi lista ismerteti ezt a tevékenységet, részletesen:

* **A tevékenység leírása**: azt jelzi, hogy minden egyes adott lépésre, a felhasználó végighalad (mint például a hitelesítési kapu átadása egy adott jelszó alaphelyzetbe állítása), része a jelszó alaphelyzetbe állítása folyamatban.
* **Tevékenység aktor**: a jelszó részét végző felhasználó flow alaphelyzetbe állítása. A felhasználó lehet a felhasználó vagy rendszergazda.
* **Tevékenység cél**: a jelszó részét végző felhasználó flow alaphelyzetbe állítása. A felhasználó lehet a felhasználó vagy rendszergazda.
* **Tevékenység állapota**:
  * _Sikeres_: azt jelzi, hogy a felhasználó sikeresen befejeződött-e egy adott lépésre, a jelszóvisszaállítási folyamatot.
  * _Hiba_: azt jelzi, hogy egy adott lépésre a jelszó alaphelyzetbe állítása nem sikerült a folyamat. Kiválaszthatja, hogy a sor megtekintéséhez a **tevékenység állapota OK** kategória tudhat meg többet a hibát.
* **Tevékenység állapota okokból**: lásd az alábbi táblázat a [minden a megengedett alaphelyzetbe állítása tevékenység állapota okok](#allowed-values-for-details-column).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Tevékenységtípus: zárolásának feloldása (önkiszolgáló) felhasználói fiók

Az alábbi lista ismerteti ezt a tevékenységet, részletesen:

* **A tevékenység leírása**: azt jelzi, hogy a felhasználó sikeresen feloldotta a saját Active Directory-fiókot anélkül, hogy a jelszó alaphelyzetbe állítása a [az Azure AD jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com) az Active Directory szolgáltatásával fiók zárolásának feloldása beállítások visszaállítása nélkül.
* **Tevékenység aktor**: A felhasználó, aki a fiók zárolását új jelszó nélkül. A felhasználó lehet a felhasználó vagy rendszergazda.
* **Tevékenység cél**: A felhasználó, aki a fiók zárolását új jelszó nélkül. A felhasználó lehet a felhasználó vagy rendszergazda.
* **Tevékenység állapota engedélyezett**:
  * _Sikeres_: azt jelzi, hogy a felhasználó sikeresen feloldotta a saját fiókjukat.
  * _Hiba_: azt jelzi, hogy a felhasználó saját fiók feloldása sikertelen volt. Kiválaszthatja, hogy a sor megtekintéséhez a **tevékenység állapota OK** kategória tudhat meg többet a hibát.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Tevékenységtípus: felhasználó regisztrált az önkiszolgáló jelszó-visszaállítás

Az alábbi lista ismerteti ezt a tevékenységet, részletesen:

* **A tevékenység leírása**: azt jelzi, hogy egy felhasználó regisztrálva van-e a szükséges információkat is visszaállíthatják a jelszavukat a jelenleg megadott tenant jelszó kérésére vonatkozó szabályzat megfelelően. 
* **Tevékenység aktor**: új jelszó kérésére regisztráló felhasználó. A felhasználó lehet a felhasználó vagy rendszergazda.
* **Tevékenység cél**: új jelszó kérésére regisztráló felhasználó. A felhasználó lehet a felhasználó vagy rendszergazda.
* **Tevékenység állapota engedélyezett**:
  * _Sikeres_: azt jelzi, hogy egy felhasználó sikeresen regisztrálva a jelszó-visszaállítást a jelenlegi szabályzatának megfelelően. 
  * _Hiba_: azt jelzi, hogy a felhasználó nem sikerült regisztrálni a jelszó-visszaállításhoz. Kiválaszthatja, hogy a sor megtekintéséhez a **tevékenység állapota OK** kategória tudhat meg többet a hibát. 

     >[!NOTE]
     >Hiba a felhasználó nem tudja saját jelszó visszaállítása nem jelenti azt. Ez azt jelenti, hogy azok a regisztrációs folyamat nem fejeződött be. Ha a fiókjuk helyességéről, például egy telefonszám, amely nincs érvényesítve, annak ellenére, hogy nem ellenőrizte, hogy ez a telefonszám nem ellenőrzött adatokat, azok továbbra is használhatja azt visszaállíthatják a jelszavukat. További információkért lásd: [mi történik, ha egy felhasználó regisztrál?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers).
     >

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
