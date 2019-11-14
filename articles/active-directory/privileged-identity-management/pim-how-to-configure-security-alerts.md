---
title: Biztonsági riasztások az Azure AD-szerepkörökhöz a PIM-ben – Azure AD | Microsoft Docs
description: Az Azure AD-szerepkörökre vonatkozó biztonsági riasztások konfigurálása a Azure Active Directoryban Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e05502b40f7f917526b2514d83dcc58636ac06f3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023046"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>Azure AD-szerepkörökhöz tartozó biztonsági riasztások konfigurálása Privileged Identity Management

Privileged Identity Management (PIM) riasztásokat állít elő, amikor gyanús vagy nem biztonságos tevékenység van a Azure Active Directory (Azure AD) szervezetében. Riasztás aktiválásakor a Privileged Identity Management irányítópulton jelenik meg. Válassza ki a riasztást, hogy megjelenjen egy jelentés, amely felsorolja a riasztást kiváltó felhasználókat vagy szerepköröket.

![Azure AD-szerepkörök – riasztási ablaktábla, amely a riasztásokat és a súlyosságot tartalmazza](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Biztonsági riasztások

Ez a szakasz felsorolja az Azure AD-szerepkörökkel kapcsolatos összes biztonsági riasztást, valamint a javítás és a megelőzés módját. A súlyosság a következő jelentéssel bír:

- **Magas**: a szabályzat megsértése miatt azonnali műveletre van szükség.
- **Közepes**: nincs szükség azonnali műveletre, de az esetleges szabályzat megsértését jelzi.
- **Alacsony**: nincs szükség azonnali műveletre, de javasolt a szabályzat módosítása.

### <a name="administrators-arent-using-their-privileged-roles"></a>A rendszergazdák nem használják a Kiemelt szerepköröket

| | |
| --- | --- |
| **Súlyosság** | Alacsony |
| **Miért kapok ez a riasztás?** | Azok a felhasználók, akik Kiemelt szerepköröket rendeltek hozzá, nem kell megnövelniük a támadás esélyét. Emellett a támadók a nem aktívan használatban lévő fiókoknál is könnyebben észrevehetetlen maradnak. |
| **Hogyan lehet javítani?** | Tekintse át a listában szereplő felhasználókat, és távolítsa el őket a nem szükséges Kiemelt szerepkörökből. |
| **Megelőzés** | A Kiemelt szerepköröket csak olyan felhasználók számára rendelheti hozzá, akik rendelkeznek üzleti indoklással. </br>Ütemezze a rendszeres [hozzáférési felülvizsgálatokat](pim-how-to-start-security-review.md) annak ellenőrzéséhez, hogy a felhasználóknak továbbra is szükségük van a hozzáférésre. |
| **A portálon belüli kockázatcsökkentő művelet** | Eltávolítja a fiókot a Kiemelt szerepkörből. |
| **Eseményindító** | Akkor aktiválódik, ha egy felhasználó a megadott számú napon belül elhalad a szerepkör aktiválása nélkül. |
| **Napok száma** | Ez a beállítás határozza meg a napok maximális számát 0 és 100 között, amelyet a felhasználó a szerepkör aktiválása nélkül tud eljárni.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>A szerepkörök nem igénylik a többtényezős hitelesítést az aktiváláshoz

| | |
| --- | --- |
| **Súlyosság** | Alacsony |
| **Miért kapok ez a riasztás?** | A többtényezős hitelesítés nélkül a feltört felhasználók is aktiválhatja a Kiemelt szerepköröket. |
| **Hogyan lehet javítani?** | Tekintse át a szerepkörök listáját, és a [többtényezős hitelesítés megkövetelése](pim-how-to-change-default-settings.md) minden szerepkörhöz. |
| **Megelőzés** | Az [MFA megkövetelése](pim-how-to-change-default-settings.md) minden szerepkörhöz.  |
| **A portálon belüli kockázatcsökkentő művelet** | A többtényezős hitelesítés szükséges a Kiemelt szerepkör aktiválásához. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>A szervezet nem rendelkezik prémium szintű Azure AD P2-vel

| | |
| --- | --- |
| **Súlyosság** | Alacsony |
| **Miért kapok ez a riasztás?** | Az aktuális Azure AD-szervezetnek nincs prémium szintű Azure AD P2-je. |
| **Hogyan lehet javítani?** | Tekintse át az [Azure ad-kiadásokkal](../fundamentals/active-directory-whatis.md)kapcsolatos információkat. Frissítsen prémium szintű Azure AD P2-re. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Lehetséges elavult fiókok egy kiemelt szerepkörben

| | |
| --- | --- |
| **Súlyosság** | Közepes |
| **Miért kapok ez a riasztás?** | A Kiemelt szerepkörben lévő fiókok nem változtatták meg a jelszavukat az elmúlt 90 napban. Ezek a fiókok lehetnek olyan szolgáltatás-vagy megosztott fiókok, amelyek nincsenek karbantartva és sebezhetők a támadók számára. |
| **Hogyan lehet javítani?** | Tekintse át a listában szereplő fiókokat. Ha már nincs szükségük hozzáférésre, távolítsa el őket a Kiemelt szerepkörökből. |
| **Megelőzés** | Ügyeljen arra, hogy a megosztott fiókok erős jelszavakat használjanak, amikor a jelszót ismerő felhasználók módosulnak. </br>A jogosultsági szintű szerepkörökkel rendelkező fiókokat rendszeresen tekintse át [hozzáférési felülvizsgálatok](pim-how-to-start-security-review.md) használatával, és távolítsa el a már nem szükséges szerepkör-hozzárendeléseket. |
| **A portálon belüli kockázatcsökkentő művelet** | Eltávolítja a fiókot a Kiemelt szerepkörből. |
| **Gyakorlati tanácsok** | Megosztott, szolgáltatás-és segélyhívó fiókok, amelyek jelszavas hitelesítéssel és a magas jogosultsági szintű rendszergazdai szerepkörökhöz vannak hozzárendelve, például a globális rendszergazda vagy a biztonsági rendszergazda számára a következő esetekben kell elforgatni a jelszavukat:<ul><li>Az olyan biztonsági incidensek után, amelyekkel visszaélés vagy a rendszergazdai hozzáférési jogosultságok sérülnek</li><li>Miután megtörtént a felhasználói jogosultságok módosítása, hogy azok ne legyenek rendszergazdaként (például egy olyan alkalmazott, aki a rendszergazda, elhagyja vagy elhagyja a szervezetet)</li><li>Rendszeres időközönként (például negyedévente vagy évente), még akkor is, ha nem történt ismert jogsértés vagy az informatikai személyzetre való váltás</li></ul>Mivel több ember fér hozzá ezekhez a fiókokhoz a hitelesítő adatokhoz, a hitelesítő adatokat el kell forgatni annak biztosításához, hogy a szerepköröket elhagyó személyek többé nem férhetnek hozzá a fiókokhoz. [Részletek](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>A szerepkörök hozzárendelése a Privileged Identity Managementon kívül történik

| | |
| --- | --- |
| **Súlyosság** | Magas |
| **Miért kapok ez a riasztás?** | A Privileged Identity Managementon kívül végrehajtott Kiemelt szerepkör-hozzárendelések nincsenek megfelelően figyelve, és az aktív támadásra utalhatnak. |
| **Hogyan lehet javítani?** | Tekintse át a listában szereplő felhasználókat, és távolítsa el őket a Privileged Identity Managementon kívül hozzárendelt Kiemelt szerepkörökből. |
| **Megelőzés** | Vizsgálja meg, hogy a felhasználók mikor kapnak jogosultsági szintű szerepköröket a Privileged Identity Managementon kívül, és nem tilthatják meg a jövőbeli hozzárendeléseket. |
| **A portálon belüli kockázatcsökkentő művelet** | Eltávolítja a felhasználót a Kiemelt szerepkörből. |

### <a name="there-are-too-many-global-administrators"></a>Túl sok globális rendszergazda van

| | |
| --- | --- |
| **Súlyosság** | Alacsony |
| **Miért kapok ez a riasztás?** | A globális rendszergazda a legmagasabb jogosultsági szintű szerepkör. Ha a globális rendszergazda biztonsága sérül, a támadó hozzáfér az összes engedélyéhez, amely a teljes rendszerét veszélyezteti. |
| **Hogyan lehet javítani?** | Tekintse át a listában szereplő felhasználókat, és távolítsa el azokat, amelyek nem feltétlenül szükségesek a globális rendszergazdai szerepkörhöz. </br>Ehelyett alacsonyabb jogosultsági szintű szerepköröket rendelhet hozzájuk a felhasználókhoz. |
| **Megelőzés** | Rendelje hozzá a felhasználókat a szükséges legalacsonyabb jogosultsági szintű szerepkörhöz. |
| **A portálon belüli kockázatcsökkentő művelet** | Eltávolítja a fiókot a Kiemelt szerepkörből. |
| **Eseményindító** | Akkor aktiválódik, ha két különböző feltétel teljesül, és mindkettőt konfigurálhatja. Először el kell érnie a globális rendszergazdák egy bizonyos küszöbértékét. Másodszor, a teljes szerepkör-hozzárendelések egy adott százalékának globális rendszergazdának kell lennie. Ha csak a fenti mérések egyikét elégíti ki, a riasztás nem jelenik meg. |
| **Globális rendszergazdák minimális száma** | Ez a beállítás határozza meg, hogy az Azure AD-szervezetnek milyen számú globális rendszergazdát kell megadnia (2 – 100). |
| **Globális rendszergazdák százalékos aránya** | Ezzel a beállítással adható meg, hogy a rendszergazdák milyen minimális százalékos arányban kapják meg a globális rendszergazdákat, 0%-ról 100%-ra, és nem szeretné, hogy az Azure AD-szervezet bemerítést végezzen. |

### <a name="roles-are-being-activated-too-frequently"></a>Túl gyakran aktiválják a szerepköröket

| | |
| --- | --- |
| **Súlyosság** | Alacsony |
| **Miért kapok ez a riasztás?** | Ugyanazon felhasználó több aktiválást is ugyanazzal a jogosultsági szinttel rendelkező szerepkörbe kapcsol egy támadás jeleként. |
| **Hogyan lehet javítani?** | Tekintse át a listában szereplő felhasználókat, és győződjön meg arról, hogy a Kiemelt szerepkör [aktiválási időtartama](pim-how-to-change-default-settings.md) elég hosszú ahhoz, hogy elvégezze a feladatait. |
| **Megelőzés** | Győződjön meg arról, hogy a Kiemelt szerepkörök [aktiválási időtartama](pim-how-to-change-default-settings.md) elég hosszú ahhoz, hogy a felhasználók elvégezzék a feladataikat.</br>[Többtényezős hitelesítés megkövetelése](pim-how-to-change-default-settings.md) olyan Kiemelt szerepkörökhöz, amelyek több rendszergazda által megosztott fiókkal rendelkeznek. |
| **A portálon belüli kockázatcsökkentő művelet** | N/A |
| **Eseményindító** | Akkor aktiválódik, ha egy felhasználó egy adott időszakon belül többször aktiválja ugyanazt a Kiemelt szerepkört. Beállíthatja az időtartamot és az aktiválások számát is. |
| **Aktiválás megújítási időkerete** | Ez a beállítás a gyanús megújítások nyomon követéséhez használni kívánt időszakot adja meg napokban, órában, percben és másodpercben. |
| **Az aktiválás megújításának száma** | Ezzel a beállítással adható meg az aktiválások száma 2 és 100 között, amelyekről értesítést szeretne kapni a választott időkereten belül. A beállítás módosításához mozgassa a csúszkát, vagy írjon be egy számot a szövegmezőbe. |

## <a name="configure-security-alert-settings"></a>Biztonsági riasztás beállításainak konfigurálása

A Privileged Identity Management a biztonsági riasztások némelyikét testreszabhatja a szervezet igényeinek és biztonsági céljainak megfelelően. A biztonsági riasztás beállításainak megnyitásához kövesse az alábbi lépéseket:

1. **Privileged Identity Management** megnyitása az Azure ad-ben.

1. Válassza az **Azure ad-szerepkörök**elemet.

1. Válassza a **Beállítások** , majd a **riasztások**lehetőséget.

    ![Azure AD-szerepkörök – beállítások a kiválasztott riasztásokkal](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Válassza ki a riasztás nevét az adott riasztás beállításának konfigurálásához.

    ![A kiválasztott riasztáshoz a biztonsági riasztás beállításai panel](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>Következő lépések

- [Azure AD-szerepkör beállításainak konfigurálása Privileged Identity Management](pim-how-to-change-default-settings.md)
