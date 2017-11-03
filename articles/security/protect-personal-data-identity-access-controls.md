---
title: "Az Azure identitások és hozzáférések vezérlőkkel személyes adatok védelme |} Microsoft Docs"
description: "Azure-identitás és hozzáférés olyan intézkedéseket, amelyek segítséget nyújtanak a személyes adatok védelme"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 7c66a95d5a056f59e0f28dba4e0880e72e74dc3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-and-multi-factor-authentication-protect-personal-data-with-identity-and-access-controls"></a>Az Azure Active Directory és a multi-factor Authentication: identitások és hozzáférések vezérlőkkel személyes adatok védelme

Ez a cikk ismerteti, használhatja az Azure Active Directory és a többtényezős hitelesítési biztonsági szolgáltatások és szolgáltatások használatával személyes adatok védelmét.

## <a name="scenario"></a>Forgatókönyv

Egy nagy körutazás cég, az Amerikai Egyesült Államokban telephelyének bővíti a műveleteket a mediterrán, Adriai, és Balti tengerek, valamint a Brit-szigetekre útvonalak biztosítani. Támogatás ezeket, több kisebb körutazás sorok Olaszország, Németországban, Dánia és az Egyesült szerzett 

A vállalat a Microsoft Azure használ a felhőben tárolt vállalati adatokat. Ez magában foglalja a személyes azonosításra alkalmas adatokat, például neveket, címeket, telefonszámokat, és a globális felhasználói bázis hitelkártya adatait. Az összes hely például a címet, telefonszámot, azonosító számokat és egyéb információkat a vállalat alkalmazottai hagyományos emberi erőforrások adatokat is tartalmaz. A körutazás sor is fenntartja, nagy adatbázis ellenszolgáltatás és hűség program tagok nyomon követéséhez a kapcsolatokat az aktuális és korábbi ügyfelek személyes információkat is tartalmaz.

Vállalati alkalmazottak hozzáférjenek a hálózathoz, a távoli iroda a vállalat, és a világ utazás ügynökök rendelkezésre elérni egyes vállalati erőforrásokat.

## <a name="problem-statement"></a>Probléma leírása

A vállalat védelmét kell beállítani az ügyfelek és az alkalmazottak személyes adatok védelme a sérült biztonságú identitások segítségével hozzáférést kérő támadóktól. Akkor is biztosítania kell, jogos felhasználók személyes adatokhoz való hozzáférés korlátozódik, csak a munkájuk elvégzéséhez szükség.

## <a name="company-goal"></a>Vállalati cél

A vállalat célja, hogy a személyes adatokhoz való hozzáférés szigorúan ellenőrzött. Fontos, hogy a felhasználók a személyes adatokhoz való hozzáférés azonosítói erős hitelesítés védi. A házirend a [minimális jogosultság] (https://en.wikipedia.org/wiki/Principle_of_least_privilege), hogy jogos felhasználója, csak a szintű hozzáférést a szükséges, és nem több kényszerítettnek kell lennie.

## <a name="solutions"></a>Megoldások

A Microsoft Azure identitások és hozzáférések felügyeleti eszközöket biztosít személyes adatokat tartalmazó erőforrásokhoz való hozzáféréssel rendelkező vállalatok vezérlő.

### <a name="azure-active-directory"></a>Azure Active Directory

[Az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (AAD) identitások kezeli, és szabályozza a hozzáférést az Azure-ba, valamint egyéb a helyszíni és más felhőalapú erőforrásokat, adatokhoz és alkalmazásokhoz. [Az Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access) Azure rendszergazdák számára, hogy bizonyos adatokat, például a személyes adatok hozzáférő felhasználók számának minimalizálása segít. Ez lehetővé teszi, ismerje meg, korlátozza és figyelje a kiemelt jogosultságú identitások és azok erőforrásokhoz való hozzáférésének, és rendeljen ideiglenes, jogosult felhasználók fordítója (JIT) rendszergazdai jogosultságokkal. Emellett biztosítja az AAD-rendszergazdai jogosultságokkal rendelkező személyek betekintést.

Az aad-ben PIM használatával járó tevékenységei közé tartoznak:

- A Privileged Identity Management engedélyezése a címtáron

- A Privileged Identity Management felügyeleti Irányítópulton egy pillantással fontos információk

- A kiemelt jogosultságú identitások (rendszergazdák) hozzáadásával vagy eltávolításával az egyes szerepkörökhöz végleges vagy jogosult rendszergazdák kezelése

- A szerepkör-aktiválási beállításainak konfigurálása

- Szerepkörök aktiválása

- Szerepkör tevékenység áttekintése

#### <a name="how-do-i-enable-aad-pim"></a>Hogyan engedélyezhető az aad-ben PIM?

A könyvtár PIM használatának megkezdéséhez a következőkre:

1. Jelentkezzen be az Azure-portálon a címtár globális rendszergazdaként.

2. Ha a szervezet több címtárral rendelkezik, akkor kattintson a felhasználónevére az Azure Portal jobb felső sarkában. Válassza ki a könyvtárat, ahol az Azure AD Privileged Identity Management fogja használni.

3. Válassza ki **további szolgáltatások** , és használja a **szűrő** szövegmező Azure AD Privileged Identity Management kereséséhez.

4. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, majd kattintson a **Létrehozás** gombra. Megnyílik a Privileged Identity Management alkalmazás.

Az Azure AD Privileged Identity Management beállítása után az alkalmazás minden megnyitásakor megjelenik a navigációs panel.

![](media/protect-personal-data-identity-access-controls/azure-pim.png)

További információkat és utasításokat Ismerkedés az aad-ben PIM [Start használata az Azure AD Privileged Identity Management.](https://docs.microsoft.com/active-directory/active-directory-privileged-identity-management-getting-started)

### <a name="azure-role-based-access-control"></a>Azure szerepköralapú hozzáférés-vezérlés

[Szerepköralapú hozzáférés-vezérlés az Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) (RBAC) engedélyezése a felhasználó hozzárendelt szerepkörön alapuló hozzáférés biztosítása az Azure erőforrásokhoz való hozzáférés kezelése Azure rendszergazdáit segíti. Elkülönítse a csapaton belüli feladatokat, és csak olyan mértékű hozzáférést biztosítania a felhasználók, csoportok és alkalmazások, a munkája elvégzéséhez szükséges.

A szerepköralapú hozzáférés az Azure portál, az Azure parancssori segédeszközök vagy az Azure felügyeleti API-k segítségével adható a felhasználóknak.

Azure RBAC alapokkal kapcsolatos további információkért lásd: [Ismerkedés a szerepköralapú hozzáférés-vezérlés az Azure portálon.](https://docs.microsoft.com/active-directory/role-based-access-control-what-is)

#### <a name="how-do-i-manage-azure-rbac-with-powershell"></a>Hogyan kezelhető az Azure RBAC a PowerShell segítségével?

PowerShell-parancsmagok használatával kezelheti az Azure RBAC, többek között az alábbi felügyeleti feladatokat:

- Lista szerepkörök

- Lásd a kinek van hozzáférése:

- Hozzáférés biztosítása

- Megszünteti a hozzáférést

- Egyéni szerepkör létrehozása

- Egy erőforrás-szolgáltató műveleteinek beolvasása

- Egyéni szerepkör módosítása

- Egyéni szerepkör törléséhez

- Egyéni szerepkörök listája

Mobileszközök kezelése a PowerShell segítségével az Azure RBAC, lásd: [kezelése szerepköralapú hozzáférés az Azure PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell).

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

[Az Azure multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/) (MFA) egy kétlépéses ellenőrzés megoldás, amely segítségével megakadályozhatja hozzáférést adatokhoz és alkalmazásokhoz, egyszerű bejelentkezési folyamatot a felhasználó igény szerint betartása mellett. Számos (például telefonos megerősítést, szöveges üzenetet vagy mobilalkalmazást használó) ellenőrzési módszerének köszönhetően erős hitelesítést biztosít.

MFA Azure felhőben való üzembe helyezéséhez meg kell először, engedélyezése, és kapcsolja be a kétlépéses ellenőrzést, a felhasználók számára.

#### <a name="how-do-i-enable-azure-to-use-mfa"></a>Hogyan engedélyezhető az Azure MFA használata?

Ha a felhasználók, amely tartalmazza az Azure multi-factor Authentication licencek, nincs mit kell tennie az Azure többtényezős hitelesítés bekapcsolása. Ha nem, a címtárban a multi-factor Auth provider létrehozásához szükséges. Ehhez kövesse az alábbi lépéseket:

1. Válassza ki **Active Directory** a klasszikus Azure portálon (bejelentkezve rendszergazdaként).

2. Válassza ki **többtényezős hitelesítési szolgáltatók.**

3. Válassza ki **új** majd a **alkalmazásszolgáltatások** kiválasztása **többtényezős hitelesítésszolgáltató.**

4. Válassza ki **Gyorslétrehozás.**

5. A név mezőbe, és válassza ki a használati modell (engedélyezett felhasználónkénti vagy hitelesítésenkénti).

6. Kijelöl egy könyvtárat, amelyhez társítva az MFA-szolgáltató.

7. Kattintson a **Létrehozás** gombra.

![](media/protect-personal-data-identity-access-controls/quick-create.png)

A többtényezős hitelesítésszolgáltató kezeléséről további utasításokért lásd: [első lépések az Azure multi-factor Auth Provider.](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-auth-provider)

#### <a name="how-do-i-turn-on-two-step-verification-for-users"></a>Hogyan kapcsolja a kétlépéses ellenőrzést, a felhasználók számára?

Kényszerítheti a kétlépéses ellenőrzést az összes bejelentkezéseket, vagy hozhat létre feltételes hozzáférési házirendeket állíthatnak kétlépéses ellenőrzést, csak akkor, ha a megadott feltétel teljesül.

Azure MFA engedélyezése a felhasználói állapotok módosításával egy hagyományos megközelítés megkövetelő a kétlépéses ellenőrzést. Összes olyan felhasználót, engedélyeznie kell a kétlépéses ellenőrzés végrehajtásához, minden alkalommal, amikor bejelentkeznek az azonos követelmény. A feltételes hozzáférési szabályzatok, amelyek befolyásolhatják, hogy a felhasználó engedélyezése egy felhasználó felülbírálja.

Azure MFA engedélyezésével a feltételes hozzáférési házirenddel egy olyan rugalmasabb megközelítés megkövetelő a kétlépéses ellenőrzést. Csoportokat, valamint az egyes felhasználókra vonatkozó feltételes hozzáférési házirendeket is létrehozhat. Magas kockázatú csoportok adható meg több korlátozás mint alacsony kockázat csoportok, vagy a kétlépéses ellenőrzés csak magas kockázatú felhőalkalmazások szükséges, és kihagyja a alacsony kockázat néhányat a meglévők közül. Feltételes hozzáférés azonban egy fizetős az Azure Active Directory szolgáltatás.

Többtényezős hitelesítés engedélyezése felhasználói állapot módosításával, tegye a következőket:

1. Jelentkezzen be az Azure portálra rendszergazdaként.
2. Ugrás a **Azure Active Directory \> felhasználók és csoportok \> minden felhasználó**.
3. Válassza ki **a multi-factor Authentication**.
4. Keresse meg azt a felhasználót, amely az Azure MFA számára engedélyezni szeretné. Lehet, hogy módosítania kell felül a nézetet.
5. A felhasználó neve melletti négyzetet.
6. Az első lépéseket, a jobb oldalon válassza ki a **engedélyezése**.

   ![](media/protect-personal-data-identity-access-controls/quick-create.png)

7. Ellenőrizze a választást, a megjelenő előugró ablakban.  Felhasználók, akiknél a többtényezős hitelesítés engedélyezve van a következő bejelentkezéskor regisztrálni kell adnia.

Az Azure MFA engedélyezi a feltételes hozzáférési házirenddel, tegye a következőket:

1. Jelentkezzen be az Azure portálra rendszergazdaként.

2. Ugrás a **Azure Active Directory \> feltételes hozzáférés**.

3. Válassza ki **új házirend**.

4. A **hozzárendelések**, jelölje be **felhasználók és csoportok**. Használja a **Include** és **kizárása** lapra, adja meg a házirend mely felhasználók és csoportok fogja kezelni.

5. A **hozzárendelések**, jelölje be **felhőalapú alkalmazásokba.** Válassza ki a **tartoznak azok az összes felhő alkalmazások**.
6.  A **hozzáférés-szabályozási**, jelölje be **Grant**. Válasszon **többtényezős hitelesítést**.
7.  Kapcsolja be **házirend engedélyezése** való **a** majd **mentése**.

Visszaélési riasztás beállítása az Azure MFA-beállítások konfigurálásával kapcsolatos további információkért az egyszeri Mellőzés létrehozása, használjon egyedi hangüzenetek, gyorsítótár konfigurálása, adja meg a megbízható IP-címek, alkalmazásjelszavak létrehozásának, engedélyezése eszközökhöz MFA megjegyzése felhasználók megbízható, és válassza hitelesítési módszerek, lásd: [Azure multi-factor Authentication beállításainak konfigurálása.](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next)

## <a name="next-steps"></a>Következő lépések

- [Az Azure Active Directory biztonságossá tétele a privilegizált hozzáférési jogosultsága.](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access)

- [Azure Multi-Factor Authentication – gyakran ismételt kérdések](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-faq)

- [Szerepköralapú hozzáférés-vezérlés hibaelhárítása](https://docs.microsoft.com/azure/active-directory/role-based-access-control-troubleshooting)

- [Az Azure Active Directory azonosító adatok védelmét](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)
