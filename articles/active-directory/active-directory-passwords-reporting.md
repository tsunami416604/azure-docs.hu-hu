---
title: 'Reporting: Az Azure AD SSPR |} Microsoft Docs'
description: "Események jelentés készítését az Azure AD az önkiszolgáló jelszó alaphelyzetbe állítása"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
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
ms.openlocfilehash: 1d27dd77547c62a3c2f77aeba214f05326c9cab4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Jelentéskészítési lehetőségek az Azure AD-jelszókezelés

A központi telepítést követően a számos szervezet szeretnék tudni, hogy valóban használja, vagy ha az önkiszolgáló jelszó-változtatási (SSPR). A jelentéskészítési szolgáltatás, amely Azure Active Directory (Azure AD) nyújt segítséget nyújt a kérdések megválaszolása előre elkészített jelentések segítségével. Ha megfelelően licencelt, létrehozhat egyéni lekérdezéseket is.

![Jelentéskészítés][Reporting]

A következő kérdéseket vet is, amely az [Azure-portálon] szerepel a jelentések által (https://portal.azure.com/):

> [!NOTE]
> Kell [egy globális rendszergazda](active-directory-assign-admin-roles-azure-portal.md), és meg kell hagyniuk az Ön szervezete nevében összegyűjteni ezeket az adatokat. Elvégezni a beléptetést, akkor fel kell keresnie a **jelentéskészítési** lapon vagy az ellenőrzés során legalább egyszer naplózza. Addig nem adatgyűjtés a szervezet számára.
>

* Jelszó alaphelyzetbe állítása hányan regisztrált?
* Aki regisztrálva van a jelszó alaphelyzetbe állítása?
* Milyen adatokat próbál regisztrálni személyek?
* Hányan visszaállíthassák a jelszavukat, az elmúlt hét napban?
* Mik a leggyakoribb módszereket, amelyek felhasználók vagy rendszergazdák visszaállíthassák a jelszavukat?
* Mik azok a gyakori problémák felhasználók vagy rendszergazdák arcfelismerési a jelszó alaphelyzetbe állítása?
* Milyen a rendszergazdák gyakran alaphelyzetbe állítja saját jelszavukat?
* A gyanús tevékenységeket, a jelszó alaphelyzetbe állítása folyamatban van?

## <a name="power-bi-content-pack"></a>A Power BI tartalomcsomag

Ha a Power BI-felhasználó, egy tartalomcsomagot szolgál, amely tartalmazza az sspr reporting könnyen használható az Azure AD. A használatára, és a csomag telepítése További információkért lásd: [használata az Azure Active Directory Power BI tartalomcsomag](active-directory-reporting-power-bi-content-pack-how-to.md). A tartalomcsomaggal a saját irányítópultot létrehozni, és azokat megosztja másokkal a szervezetében.

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Az Azure portálon jelszó jelentések megtekintése

Az Azure portál élményt nyújt továbbfejlesztettük módon, hogy megtekintheti a jelszó alaphelyzetbe állítása és a jelszó-visszaállítási regisztrációs tevékenység. Használja a következő, a lépésekkel megállapíthatja a jelszó alaphelyzetbe állítása és a jelszó-változtatási regisztrációs események:

1. Keresse meg a [Azure-portálon](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** a bal oldali ablaktáblán.
3. Keresse meg **Azure Active Directory** a szolgáltatások listájában, és jelölje ki.
4. Válassza ki **felhasználók és csoportok**.
5. Válassza ki **naplók** a a **felhasználók és csoportok** menü. Megjelenik az, hogy a címtárban szereplő összes felhasználó ellen történt naplózási események. Ez a nézet a jelszóval kapcsolatos eseményeket jeleníthetők meg.
6. Ez a nézet csak a jelszó alaphelyzetbe állítása kapcsolatos események szűréséhez válassza a **szűrő** gombra, ha a panel tetején.
7. Az a **szűrő** menüben válassza a **kategória** legördülő listában, és módosítsa úgy, hogy a **önkiszolgáló jelszókezelés** kategória típusa.
8. Szükség esetén további szűréséhez válassza ki az adott **tevékenység** kíváncsiak vagyunk.

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>Hogyan lehet lekérni a jelszó felügyeleti események az Azure AD-jelentések és események API

Az Azure AD-jelentések és események API támogatja a jelszó alaphelyzetbe állítása szereplő összes információ lekérése, és a jelszó alaphelyzetbe állítása a szóregisztrációs jelentéseket. Ez az API használatával egyéni jelszó alaphelyzetbe állítása és a jelszó alaphelyzetbe állítása regisztrációs események letöltheti és integrálja ezeket a kiválasztott jelentési technológia.

### <a name="how-to-get-started-with-the-reporting-api"></a>Első lépések a reporting API-hoz

Ezek az adatok eléréséhez meg kell írnia egy kis alkalmazás vagy a parancsfájl azt lekérése a kiszolgálóról. További információkért lásd: [az Azure AD reporting API használatába](active-directory-reporting-api-getting-started.md).

Miután egy működő parancsfájl, érdemes vizsgálja meg az esetek teljesítéséhez kérheti le a jelszó alaphelyzetbe állítása és nyilvántartási események:

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent): az elérhető oszlopok listája, a jelszó-átállítási események.
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent): az elérhető oszlopok listája, a jelszó-átállítási regisztráció események.

### <a name="reporting-api-data-retrieval-limitations"></a>Jelentéskészítési API adatok lekérését korlátozásai

Jelenleg az Azure AD-jelentések és események API lekérdezi legfeljebb *75,000 események* , a [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) és [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent) típusok. Az API-t is lefedik az *utolsó 30 nap*.

Ha szeretné beolvasni, vagy ez az ablak adatok tárolására, javasoljuk, hogy a eltéréseit – eredményező lekérdezni az API használatával külső adatbázis megőrzése. Azt javasoljuk, hogy kezdje lekérdezhetik ezeket az adatokat, önkiszolgáló jelszó-Változtatási segítségével a szervezet indításakor. Továbbra is fennáll, akkor kívülről, és folytassa a ettől kezdve az eltérések nyomon követésére.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Az Azure-portálon a jelentés oszlopok leírása

Az alábbi lista ismerteti a jelentés részletes információkat az Azure-portálon oszlopainak mindegyike:

* **Felhasználói**: kísérletet a jelszó alaphelyzetbe állítása regisztrációs műveletet.
* **Szerepkör**: a címtár felhasználói szerepkört.
* **Dátum és idő**: A dátum és idő a kísérlet.
* **Adatok regisztrált**: az, hogy a felhasználó által megadott során jelszó-átállítási regisztrációk hitelesítési adatokat.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>A jelentés értékeket az Azure portálon leírása

A következő táblázat ismerteti a különböző értékek, amelyek az egyes oszlopok az Azure-portálon állíthatja be:

| Oszlop | Megengedett értékek és azok jelentését |
| --- | --- |
| Regisztrált adatok |**Másodlagos e-mail-címet**: A felhasználó egy másodlagos e-mail vagy hitelesítési e-mail hitelesítéséhez használt.<p><p>**Irodai telefon**: A felhasználó hitelesítésére az irodai telefon használt.<p>**Mobiltelefon**: A felhasználó a mobiltelefonszám vagy a hitelesítéshez megadott telefonját hitelesítéséhez használt.<p>**Biztonsági kérdések**: A felhasználó hitelesítésére a biztonsági kérdések használt.<p>**A korábbi metódusok bármely kombinációja például alternatív e-mail + mobile phone**: akkor fordul elő, ha a két-kapu házirend van megadva, és bemutatja, melyik két módszer használt felhasználói hitelesítés a jelszó-változtatási kérelmet. |

## <a name="self-service-password-management-activity-types"></a>Önkiszolgáló jelszókezelés tevékenységtípusok

A következő típusú tevékenységek szerepelnek a **önkiszolgáló jelszókezelés** naplózási eseménykategória:

* [Önkiszolgáló jelszóváltoztatás blokkolva](#activity-type-blocked-from-self-service-password-reset): azt jelzi, hogy egy rendszergazda felhasználó megpróbálta jelszó alaphelyzetbe állítása, vagy egy adott kapu használni, egy telefonszám ellenőrzése a 24 órában teljes ötnél több alkalommal.
* [Módosítsa jelszavát (önkiszolgáló)](#activity-type-change-password-self-service): azt jelzi, hogy a felhasználó elvégzett önkéntes, vagy (mert a lejárati) kényszerített a jelszó módosítása.
* [Jelszó-átállítási (amelyeket a rendszergazda)](#activity-type-reset-password-by-admin): azt jelzi, hogy a rendszergazda végre az Azure-portálról egy felhasználó nevében új jelszót.
* [Jelszó-átállítási (önkiszolgáló)](#activity-type-reset-password-self-service): azt jelzi, hogy a felhasználó sikeresen a jelszó visszaállítása a [az Azure AD-jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com).
* [Az önkiszolgáló jelszó-átállítási folyamata tevékenység folyamatban](#activity-type-self-serve-password-reset-flow-activity-progress): azt jelzi a felhasználó végighalad, adott lépésre, például a hitelesítési kapu, átadja a jelszó alaphelyzetbe állítása, része a jelszó alaphelyzetbe állítási eljárás szerint.
* [Felhasználói fiók (önkiszolgáló) feloldása](#activity-type-unlock-user-account-self-service): azt jelzi, hogy a felhasználó nyitása sikeresen megtörtént az Active Directory-fiókjában a jelszó alaphelyzetbe állításával a [az Azure AD-jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com) az aktív használatával Directory szolgáltatás fiók feloldásához visszaállítása nélkül.
* [A felhasználó önkiszolgáló jelszó-visszaállításhoz regisztrálva](#activity-type-user-registered-for-self-service-password-reset): azt jelzi, hogy a felhasználó regisztrálva van-e a szükséges információkat szeretne a jelenleg megadott tenant jelszó-visszaállítási házirend megfelelően jelszó visszaállítása.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Tevékenységtípus: az önkiszolgáló jelszó-visszaállítás blokkolva

Az alábbi lista ismerteti ennek a tevékenységnek a részletei:

* **Tevékenység leírásában**: azt jelzi, hogy egy rendszergazda felhasználó megpróbálta jelszó alaphelyzetbe állítása, vagy egy adott kapu használni, egy telefonszám ellenőrzése a 24 órában teljes ötnél több alkalommal.
* **Tevékenység aktor**: lett halmozódni végrehajtása további felhasználó visszaállítási műveletről. A felhasználó a felhasználó vagy egy rendszergazda lehet.
* **Tevékenység cél**: lett halmozódni végrehajtása további felhasználó visszaállítási műveletről. A felhasználó a felhasználó vagy egy rendszergazda lehet.
* **A tevékenység állapota**:
  * _Sikeres_: azt jelzi, hogy egy felhasználó hajt végre semmilyen további alaphelyzetbe állítását, minden további hitelesítési módszerek megkísérlése vagy bármilyen további telefonszámok érvényesítése a következő 24 órában történt szabályozva.
* **Tevékenység állapota a hiba oka**: nem alkalmazható.

### <a name="activity-type-change-password-self-service"></a>Tevékenységtípus: jelszó módosítása (önkiszolgáló)

Az alábbi lista ismerteti ennek a tevékenységnek a részletei:

* **Tevékenység leírásában**: azt jelzi, hogy a felhasználó elvégzett önkéntes, vagy (mert a lejárati) kényszerített a jelszó módosítása.
* **Tevékenység aktor**: A felhasználó módosította a jelszavát. A felhasználó a felhasználó vagy egy rendszergazda lehet.
* **Tevékenység cél**: A felhasználó módosította a jelszavát. A felhasználó a felhasználó vagy egy rendszergazda lehet.
* **Tevékenység állapotai**:
  * _Sikeres_: azt jelzi, hogy a felhasználó sikeresen váltott a jelszavát.
  * _Hiba_: azt jelzi, hogy a felhasználó megváltoztathatja a jelszavát. Kiválaszthatja, hogy a sor tekintse meg a **tevékenység állapotának oka** kategória további információt a hiba okát.
* **Tevékenység állapota a hiba oka**: 
  * _FuzzyPolicyViolationInvalidPassword_: A felhasználó megadott, amely automatikusan lett tiltva, mert a Microsoft tiltott jelszó észlelési képességek található túl köznapi vagy különösen gyenge jelszót.

### <a name="activity-type-reset-password-by-admin"></a>Tevékenységtípus: jelszó-átállítási (amelyeket a rendszergazda)

Az alábbi lista ismerteti ennek a tevékenységnek a részletei:

* **Tevékenység leírásában**: azt jelzi, hogy a rendszergazda végre az Azure-portálról egy felhasználó nevében új jelszót.
* **Tevékenység aktor**: a jelszó-változtatási egy másik felhasználó vagy rendszergazda nevében végző rendszergazda. Kell lennie, vagy egy globális rendszergazda, jelszókezelő, felhasználó, vagy segélyszolgálat rendszergazdája.
* **Tevékenység cél**: A felhasználó, akinek a jelszó alaphelyzetbe állítása. A felhasználó lehet a felhasználó vagy egy másik rendszergazda.
* **Tevékenység állapotai**:
  * _Sikeres_: azt jelzi, hogy egy rendszergazda alaphelyzetbe állítása sikeresen megtörtént a felhasználó jelszavát.
  * _Hiba_: azt jelzi, hogy egy rendszergazda felhasználó jelszavának módosítása. Kiválaszthatja, hogy a sor tekintse meg a **tevékenység állapotának oka** kategória további információt a hiba okát.

### <a name="activity-type-reset-password-self-service"></a>Tevékenységtípus: jelszó-átállítási (önkiszolgáló)

Az alábbi lista ismerteti ennek a tevékenységnek a részletei:

* **Tevékenység leírásában**: azt jelzi, hogy a felhasználó sikeresen a jelszó visszaállítása a [az Azure AD-jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com).
* **Tevékenység aktor**: jelszó visszaállítása felhasználó. A felhasználó a felhasználó vagy egy rendszergazda lehet.
* **Tevékenység cél**: jelszó visszaállítása felhasználó. A felhasználó a felhasználó vagy egy rendszergazda lehet.
* **Tevékenység állapotai**:
  * _Sikeres_: azt jelzi, hogy a felhasználó alaphelyzetbe állítása sikeresen megtörtént a saját jelszavát.
  * _Hiba_: azt jelzi, hogy a felhasználók a saját jelszó visszaállítása sikertelen. Kiválaszthatja, hogy a sor tekintse meg a **tevékenység állapotának oka** kategória további információt a hiba okát.
* **Tevékenység állapota a hiba oka**: 
  * _FuzzyPolicyViolationInvalidPassword_: A rendszergazda egy jelszót, amely automatikusan lett tiltva, mert a Microsoft tiltott jelszó észlelési képességek található túl köznapi vagy különösen gyenge kiválasztva.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Tevékenységtípus: önkiszolgáló szolgálnak a jelszó alaphelyzetbe állítása folyamata tevékenység folyamatban

Az alábbi lista ismerteti ennek a tevékenységnek a részletei:

* **Tevékenység leírásában**: minden adott lépésre, a felhasználó végighalad (például a hitelesítési kapu átadja a jelszó alaphelyzetbe állítása) azt jelzi, mert része a jelszó alaphelyzetbe állítási eljárás.
* **Tevékenység aktor**: a jelszó részét végző felhasználó visszaállítása folyamata. A felhasználó a felhasználó vagy egy rendszergazda lehet.
* **Tevékenység cél**: a jelszó részét végző felhasználó visszaállítása folyamata. A felhasználó a felhasználó vagy egy rendszergazda lehet.
* **Tevékenység állapotai**:
  * _Sikeres_: azt jelzi, hogy a felhasználó sikeresen befejeződött-e a jelszóvisszaállítási folyamatot az adott lépésre.
  * _Hiba_: azt jelzi, hogy egy adott lépésre a jelszó alaphelyzetbe állítása nem sikerült folyamat. Kiválaszthatja, hogy a sor tekintse meg a **tevékenység állapotának oka** kategória további információt a hiba okát.
* **Tevékenység állapota okok**: lásd az alábbi táblázatban [összes megengedett alaphelyzetbe állítása tevékenység állapota okra](#allowed-values-for-details-column).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Tevékenységtípus: zárolásának feloldásához egy felhasználói fiókot (önkiszolgáló)

Az alábbi lista ismerteti ennek a tevékenységnek a részletei:

* **Tevékenység leírásában**: azt jelzi, hogy a felhasználó nyitása sikeresen megtörtént az Active Directory-fiókjában a jelszó alaphelyzetbe állításával a [az Azure AD-jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com) az Active Directory szolgáltatásával fiók zárolásának feloldása nélkül alaphelyzetbe állítása.
* **Tevékenység aktor**: A felhasználó nélkül a jelszó alaphelyzetbe állításával a fiók zárolását. A felhasználó a felhasználó vagy egy rendszergazda lehet.
* **Tevékenység cél**: A felhasználó nélkül a jelszó alaphelyzetbe állításával a fiók zárolását. A felhasználó a felhasználó vagy egy rendszergazda lehet.
* **Tevékenység állapotai engedélyezett**:
  * _Sikeres_: azt jelzi, hogy a felhasználó nyitása sikeresen megtörtént a saját fiókjukat.
  * _Hiba_: azt jelzi, hogy a felhasználó feloldhatja a fiók zárolását. Kiválaszthatja, hogy a sor tekintse meg a **tevékenység állapotának oka** kategória további információt a hiba okát.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Tevékenységtípus: felhasználó regisztrált az önkiszolgáló jelszóváltoztatáshoz

Az alábbi lista ismerteti ennek a tevékenységnek a részletei:

* **Tevékenység leírásában**: azt jelzi, hogy a felhasználó regisztrálva van-e a szükséges információkat szeretne a jelenleg megadott tenant jelszó-visszaállítási házirend megfelelően jelszó visszaállítása. 
* **Tevékenység aktor**: A regisztrált felhasználó folyamatos jelszó-visszaállításhoz. A felhasználó a felhasználó vagy egy rendszergazda lehet.
* **Tevékenység cél**: A regisztrált felhasználó folyamatos jelszó-visszaállításhoz. A felhasználó a felhasználó vagy egy rendszergazda lehet.
* **Tevékenység állapotai engedélyezett**:
  * _Sikeres_: azt jelzi, hogy a felhasználó sikerült regisztrálni a jelszó alaphelyzetbe állítása, az aktuális nyilatkozatnak. 
  * _Hiba_: azt jelzi, hogy a felhasználók regisztrálhatnak a jelszóváltoztatásra. Kiválaszthatja, hogy a sor tekintse meg a **tevékenység állapotának oka** kategória további információt a hiba okát. 

     >[!NOTE]
     >Hiba a felhasználó számára a saját jelszó visszaállítása nem jelenti azt. Azt jelenti, hogy azok a regisztrációs folyamat nem fejeződött. Ha a fiók, amely megfelel, például a telefonszám, nincs érvényesítve, annak ellenére, hogy nem ellenőrizte, hogy ez a telefonszám nem ellenőrzött adatok, azok továbbra is használhatja azt a jelszó visszaállítása. További információkért lásd: [mi történik, ha a felhasználó regisztrál?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers).
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
* [Mik az SSPR beállításai, és mit jelentenek?](active-directory-passwords-how-it-works.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

[Reporting]: ./media/active-directory-passwords-reporting/sspr-reporting.png "Az Azure AD naplózza az önkiszolgáló jelszó-Változtatási tevékenység naplózási – példa"
