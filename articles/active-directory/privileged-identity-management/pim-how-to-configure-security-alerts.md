---
title: Biztonsági riasztások az Azure AD-szerepkörökhöz a PIM-ben – Azure AD | Microsoft dokumentumok
description: Konfigurálja a biztonsági riasztások az Azure AD-szerepkörök kiemelt identitáskezelés az Azure Active Directoryban.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 03/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86dbcdc24c90ba8b161b041af96cbdd0665ad827
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253311"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>Biztonsági riasztások konfigurálása az Azure AD-szerepkörökhöz a kiemelt identitáskezelésben

A kiemelt identitáskezelés (PIM) riasztásokat hoz létre, ha gyanús vagy nem biztonságos tevékenység van az Azure Active Directory (Azure AD) szervezetben. Amikor egy riasztás aktiválódik, megjelenik a kiemelt identitáskezelés irányítópulton. Válassza ki a riasztást egy olyan jelentés megtekintéséhez, amely felsorolja a riasztást kiváltó felhasználókat vagy szerepköröket.

## <a name="determine-your-version-of-pim"></a>A PIM verziójának meghatározása

2019 novemberétől kezdődően a kiemelt identitáskezelés Azure AD-szerepkörök része frissül egy új verzióra, amely megfelel az Azure-erőforrás-szerepkörök élményének. Ez további funkciókat, valamint [a meglévő API-t is módosítja.](azure-ad-roles-features.md#api-changes) Az új verzió bevezetés alatt, hogy mely eljárásokat követi ebben a cikkben attól függ, hogy a privileged identity management jelenleg rendelkezik-e. Ebben a szakaszban ismertetett lépéseket követve határozza meg, hogy a Kiemelt identitáskezelés melyik verzióját használja. Miután ismeri a kiemelt identitáskezelés verzióját, kiválaszthatja a cikkben az adott verziónak megfelelő eljárásokat.

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) egy olyan felhasználóval, aki a [kiemelt szerepkör-rendszergazdai](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörben van.
1. Nyissa meg **az Azure AD kiemelt identitáskezelés .** Ha az áttekintő oldal tetején szalaghirdetés található, kövesse a cikk **Új verzió** lapján található utasításokat. Ellenkező esetben kövesse az **Előző verzió** lap utasításait.

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Kövesse a cikkben leírt lépéseket az Azure AD-szerepkörök biztonsági riasztásainak vizsgálatához.

# <a name="new-version"></a>[Új verzió](#tab/new)

![Azure AD-szerepkörök – Riasztási ablaktábla listák riasztási és súlyosságát](./media/pim-how-to-configure-security-alerts/view-alerts.png)

## <a name="security-alerts"></a>Biztonsági riasztások

Ez a szakasz az Azure AD-szerepkörök összes biztonsági riasztását, valamint a javítás és a megelőzés módját sorolja fel. A súlyosság jelentése a következő:

- **Magas**: Irányelvsértés miatt azonnali beavatkozást igényel.
- **Közepes**: Nem igényel azonnali műveletet, de potenciális irányelvsértést jelez.
- **Alacsony**: Nem igényel azonnali cselekvést, de előnyösebb politikai változást javasol.

### <a name="administrators-arent-using-their-privileged-roles"></a>A rendszergazdák nem használják a kiemelt szerepköreiket

| | |
| --- | --- |
| **Súlyosság** | Alacsony |
| **Miért kapok riasztást?** | Azok a felhasználók, akiknek kiemelt szerepkörökhöz vannak rendelve, nincs szükségük, növelik a támadás esélyét. A támadók számára is könnyebb észrevétlenmaradni a nem aktívan használt fiókokban. |
| **Hogyan lehet megjavítani?** | Tekintse át a felhasználók a listában, és távolítsa el őket a kiemelt szerepkörök, amelyek nem szükséges. |
| **Megelőzés** | Kiemelt szerepkörök hozzárendelése csak olyan felhasználókhoz, akik üzleti indoklással rendelkeznek. </br>Rendszeres [hozzáférés-felülvizsgálatok ütemezése](pim-how-to-start-security-review.md) annak ellenőrzéséhez, hogy a felhasználóknak továbbra is szükségük van-e a hozzáférésükre. |
| **A portálon keresztüli kockázatcsökkentési művelet** | Eltávolítja a fiókot a kiemelt szerepkörükből. |
| **Eseményindító** | Akkor aktiválódik, ha a felhasználó egy adott számú napon megy keresztül szerepkör aktiválása nélkül. |
| **Napok száma** | Ez a beállítás azt a maximális számú napot adja meg, 0 és 100 között, amerre a felhasználó szerepkör aktiválása nélkül is eltud menni.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>A szerepkörök aktiválásához nincs szükség többtényezős hitelesítésre

| | |
| --- | --- |
| **Súlyosság** | Alacsony |
| **Miért kapok riasztást?** | Többtényezős hitelesítés nélkül a sérült felhasználók aktiválhatják a kiemelt szerepköröket. |
| **Hogyan lehet megjavítani?** | Tekintse át a szerepkörök listáját, és minden szerepkörhöz [többtényezős hitelesítést igényel.](pim-how-to-change-default-settings.md) |
| **Megelőzés** | [Többfélű fa szükség](pim-how-to-change-default-settings.md) minden szerepkörhöz.  |
| **A portálon keresztüli kockázatcsökkentési művelet** | A kiemelt szerepkör aktiválásához többtényezős hitelesítésszükséges. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>A szervezet nem rendelkezik Az Azure AD Premium P2

| | |
| --- | --- |
| **Súlyosság** | Alacsony |
| **Miért kapok riasztást?** | A jelenlegi Azure AD-szervezet nem rendelkezik az Azure AD Premium P2. |
| **Hogyan lehet megjavítani?** | Tekintse át az [Azure AD-kiadásokkal](../fundamentals/active-directory-whatis.md)kapcsolatos információkat. Frissítsen az Azure AD Premium P2 csomagra. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potenciális elavult fiókok kiemelt szerepkörben

| | |
| --- | --- |
| **Súlyosság** | Közepes |
| **Miért kapok riasztást?** | A kiemelt szerepkörrel rendelkező fiókok nem változtatták meg a jelszavukat az elmúlt 90 napban. Ezek a fiókok lehetnek olyan szolgáltatások vagy megosztott fiókok, amelyek nincsenek karbantartva, és ki vannak téve a támadóknak. |
| **Hogyan lehet megjavítani?** | Tekintse át a listában szereplő fiókokat. Ha már nincs szükségük hozzáférésre, távolítsa el őket a kiemelt szerepkörökből. |
| **Megelőzés** | Győződjön meg arról, hogy a megosztott fiókok erős jelszavakat forgatnak, ha a jelszót ismerő felhasználók náluk változás történik. </br>Rendszeresen tekintse át a kiemelt szerepkörrel rendelkező fiókokat [a hozzáférési felülvizsgálatok](pim-how-to-start-security-review.md) használatával, és távolítsa el a már nem szükséges szerepkör-hozzárendeléseket. |
| **A portálon keresztüli kockázatcsökkentési művelet** | Eltávolítja a fiókot a kiemelt szerepkörükből. |
| **Bevált módszerek** | A jelszóval hitelesített és magas szintű rendszergazdai szerepkörökhöz (például globális rendszergazdai vagy biztonsági rendszergazda) rendelt megosztott, szolgáltatás- és segélyhívási fiókokjelszavát a következő esetekben kell elforgatni:<ul><li>Az adminisztratív hozzáférési jogokkal való visszaéléssel vagy az okkal való veszélyeztetéssel járó biztonsági incidensután</li><li>Miután bármely felhasználó jogosultságai megváltoztak, hogy azok már nem rendszergazdai okok (például miután egy alkalmazott, aki rendszergazda volt, elhagyja az informatikai és kilép a szervezet)</li><li>Rendszeres időközönként (például negyedéves vagy éves) időközönként, még akkor is, ha nem történt ismert jogsértés vagy az informatikai személyzet</li></ul>Mivel több en is hozzáférhetnek ezekhez a fiókok hitelesítő adataihoz, a hitelesítő adatokat el kell forgatni annak érdekében, hogy a szerepkörüket elhagyó személyek többé ne férhessenek hozzá a fiókokhoz. [További információ a fiókok védelméről](../users-groups-roles/directory-admin-roles-secure.md) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>A szerepkörök hozzárendelése a kiemelt identitáskezelésen kívül van hozzárendelve

| | |
| --- | --- |
| **Súlyosság** | Magasság |
| **Miért kapok riasztást?** | A kiemelt identitáskezelésen kívül végzett kiemelt szerepkör-hozzárendelések nem megfelelően figyelhetők, és aktív támadást jelezhetnek. |
| **Hogyan lehet megjavítani?** | Tekintse át a felhasználók a listában, és távolítsa el őket a kiemelt identitáskezelésen kívül hozzárendelt kiemelt szerepkörökből. |
| **Megelőzés** | Vizsgálja meg, hogy a felhasználók a kiemelt identitáskezelésen kívül hol vannak kiemelt szerepkörökhöz rendelve, és tiltsa le a jövőbeli hozzárendeléseket. |
| **A portálon keresztüli kockázatcsökkentési művelet** | Eltávolítja a felhasználót a kiemelt szerepkörből. |

### <a name="there-are-too-many-global-administrators"></a>Túl sok a globális rendszergazda

| | |
| --- | --- |
| **Súlyosság** | Alacsony |
| **Miért kapok riasztást?** | A globális rendszergazda a legmagasabb jogosultsági szintű szerepkör. Ha egy globális rendszergazda biztonsága sérül, a támadó minden engedélyéhez hozzáfér, ami az egész rendszert veszélyezteti. |
| **Hogyan lehet megjavítani?** | Tekintse át a listában szereplő felhasználókat, és távolítson el minden olyan felhasználót, amelynek nincs feltétlenül szüksége a globális rendszergazdai szerepkörre. </br>Rendeljen alacsonyabb jogosultsággal rendelkező szerepköröket ezekhez a felhasználókhoz. |
| **Megelőzés** | Rendelje hozzá a felhasználóknak a szükséges legkevésbé kiemelt szerepkört. |
| **A portálon keresztüli kockázatcsökkentési művelet** | Eltávolítja a fiókot a kiemelt szerepkörükből. |
| **Eseményindító** | Akkor aktiválódik, ha két különböző feltétel teljesül, és mindkettő konfigurálható. Először el kell érnie a globális rendszergazdák egy bizonyos küszöbértékét. Másodszor, a teljes szerepkör-hozzárendelések bizonyos százalékának globális rendszergazdáknak kell lennie. Ha csak az egyik ilyen mérésnek felel meg, a riasztás nem jelenik meg. |
| **A globális rendszergazdák minimális száma** | Ez a beállítás adja meg a globális rendszergazdák száma, 2-100, hogy úgy gondolja, hogy túl kevés az Azure AD-szervezet. |
| **A globális rendszergazdák százalékos aránya** | Ez a beállítás határozza meg a rendszergazdák minimális százalékos, akik globális rendszergazdák, 0%-ról 100%-ra, amely alatt nem szeretné, hogy az Azure AD-szervezet dip. |

### <a name="roles-are-being-activated-too-frequently"></a>A szerepkörök túl gyakran aktiválódnak

| | |
| --- | --- |
| **Súlyosság** | Alacsony |
| **Miért kapok riasztást?** | Ugyanazon felhasználó által ugyanannak a kiemelt szerepkörnek történő többszörös aktiválása támadás jele. |
| **Hogyan lehet megjavítani?** | Tekintse át a felhasználók a listában, és győződjön meg arról, hogy az [aktiválás időtartama](pim-how-to-change-default-settings.md) a kiemelt szerepkör van beállítva elég hosszú ahhoz, hogy a feladatok elvégzéséhez. |
| **Megelőzés** | Győződjön meg arról, hogy a kiemelt szerepkörök [aktiválási időtartama](pim-how-to-change-default-settings.md) elég hosszú ahhoz, hogy a felhasználók elvégezzék a feladataikat.</br>[Többtényezős hitelesítés megkövetelése](pim-how-to-change-default-settings.md) olyan kiemelt szerepkörök esetében, amelyek fiókjait több rendszergazda osztja meg. |
| **A portálon keresztüli kockázatcsökkentési művelet** | N/A |
| **Eseményindító** | Akkor aktiválódik, ha egy felhasználó egy adott időszakon belül többször aktiválja ugyanazt a kiemelt szerepkört. Az aktiválások időtartamát és számát is beállíthatja. |
| **Aktiválás megújításának időkerete** | Ez a beállítás napokban, órákban, percekben és a gyanús megújítások nyomon követéséhez használni kívánt időszak másodpercében adja meg. |
| **Az aktiválás megújításának száma** | Ez a beállítás azt adja meg, hogy a választott időkereten belül hány aktiválást szeretne 2 és 100 között. Ezt a beállítást a csúszka mozgatásával vagy a szövegmezőbe beírva módosíthatja. |

## <a name="configure-security-alert-settings"></a>Biztonsági riasztásbeállításainak megadása

A Riasztások lapon nyissa meg a **Beállítások lehetőséget.**

![Riasztások lap kiemelt Beállítások kal](media/pim-how-to-configure-security-alerts/alert-settings.png)

A környezet és a biztonsági célok eléréséhez testreszabhatja a különböző riasztások beállításait.

![A beállítások engedélyezéséhez és konfigurálásához szolgáló riasztás lapjának beállítása](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

# <a name="previous-version"></a>[Előző verzió](#tab/previous)

![Azure AD-szerepkörök – Riasztási ablaktábla listák riasztási és súlyosságát](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Biztonsági riasztások

Ez a szakasz az Azure AD-szerepkörök összes biztonsági riasztását, valamint a javítás és a megelőzés módját sorolja fel. A súlyosság jelentése a következő:

- **Magas**: Irányelvsértés miatt azonnali beavatkozást igényel.
- **Közepes**: Nem igényel azonnali műveletet, de potenciális irányelvsértést jelez.
- **Alacsony**: Nem igényel azonnali cselekvést, de előnyösebb politikai változást javasol.

### <a name="administrators-arent-using-their-privileged-roles"></a>A rendszergazdák nem használják a kiemelt szerepköreiket

| | |
| --- | --- |
| **Súlyosság** | Alacsony |
| **Miért kapok riasztást?** | Azok a felhasználók, akiknek kiemelt szerepkörökhöz vannak rendelve, nincs szükségük, növelik a támadás esélyét. A támadók számára is könnyebb észrevétlenmaradni a nem aktívan használt fiókokban. |
| **Hogyan lehet megjavítani?** | Tekintse át a felhasználók a listában, és távolítsa el őket a kiemelt szerepkörök, amelyek nem szükséges. |
| **Megelőzés** | Kiemelt szerepkörök hozzárendelése csak olyan felhasználókhoz, akik üzleti indoklással rendelkeznek. </br>Rendszeres [hozzáférés-felülvizsgálatok ütemezése](pim-how-to-start-security-review.md) annak ellenőrzéséhez, hogy a felhasználóknak továbbra is szükségük van-e a hozzáférésükre. |
| **A portálon keresztüli kockázatcsökkentési művelet** | Eltávolítja a fiókot a kiemelt szerepkörükből. |
| **Eseményindító** | Akkor aktiválódik, ha a felhasználó egy adott számú napon megy keresztül szerepkör aktiválása nélkül. |
| **Napok száma** | Ez a beállítás azt a maximális számú napot adja meg, 0 és 100 között, amerre a felhasználó szerepkör aktiválása nélkül is eltud menni.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>A szerepkörök aktiválásához nincs szükség többtényezős hitelesítésre

| | |
| --- | --- |
| **Súlyosság** | Alacsony |
| **Miért kapok riasztást?** | Többtényezős hitelesítés nélkül a sérült felhasználók aktiválhatják a kiemelt szerepköröket. |
| **Hogyan lehet megjavítani?** | Tekintse át a szerepkörök listáját, és minden szerepkörhöz [többtényezős hitelesítést igényel.](pim-how-to-change-default-settings.md) |
| **Megelőzés** | [Többfélű fa szükség](pim-how-to-change-default-settings.md) minden szerepkörhöz.  |
| **A portálon keresztüli kockázatcsökkentési művelet** | A kiemelt szerepkör aktiválásához többtényezős hitelesítésszükséges. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>A szervezet nem rendelkezik Az Azure AD Premium P2

| | |
| --- | --- |
| **Súlyosság** | Alacsony |
| **Miért kapok riasztást?** | A jelenlegi Azure AD-szervezet nem rendelkezik az Azure AD Premium P2. |
| **Hogyan lehet megjavítani?** | Tekintse át az [Azure AD-kiadásokkal](../fundamentals/active-directory-whatis.md)kapcsolatos információkat. Frissítsen az Azure AD Premium P2 csomagra. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potenciális elavult fiókok kiemelt szerepkörben

| | |
| --- | --- |
| **Súlyosság** | Közepes |
| **Miért kapok riasztást?** | A kiemelt szerepkörrel rendelkező fiókok nem változtatták meg a jelszavukat az elmúlt 90 napban. Ezek a fiókok lehetnek olyan szolgáltatások vagy megosztott fiókok, amelyek nincsenek karbantartva, és ki vannak téve a támadóknak. |
| **Hogyan lehet megjavítani?** | Tekintse át a listában szereplő fiókokat. Ha már nincs szükségük hozzáférésre, távolítsa el őket a kiemelt szerepkörökből. |
| **Megelőzés** | Győződjön meg arról, hogy a megosztott fiókok erős jelszavakat forgatnak, ha a jelszót ismerő felhasználók náluk változás történik. </br>Rendszeresen tekintse át a kiemelt szerepkörrel rendelkező fiókokat [a hozzáférési felülvizsgálatok](pim-how-to-start-security-review.md) használatával, és távolítsa el a már nem szükséges szerepkör-hozzárendeléseket. |
| **A portálon keresztüli kockázatcsökkentési művelet** | Eltávolítja a fiókot a kiemelt szerepkörükből. |
| **Bevált módszerek** | A jelszóval hitelesített és magas szintű rendszergazdai szerepkörökhöz (például globális rendszergazdai vagy biztonsági rendszergazda) rendelt megosztott, szolgáltatás- és segélyhívási fiókokjelszavát a következő esetekben kell elforgatni:<ul><li>Az adminisztratív hozzáférési jogokkal való visszaéléssel vagy az okkal való veszélyeztetéssel járó biztonsági incidensután</li><li>Miután bármely felhasználó jogosultságai megváltoztak, hogy azok már nem rendszergazdai okok (például miután egy alkalmazott, aki rendszergazda volt, elhagyja az informatikai és kilép a szervezet)</li><li>Rendszeres időközönként (például negyedéves vagy éves) időközönként, még akkor is, ha nem történt ismert jogsértés vagy az informatikai személyzet</li></ul>Mivel több en is hozzáférhetnek ezekhez a fiókok hitelesítő adataihoz, a hitelesítő adatokat el kell forgatni annak érdekében, hogy a szerepkörüket elhagyó személyek többé ne férhessenek hozzá a fiókokhoz. [További információ](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>A szerepkörök hozzárendelése a kiemelt identitáskezelésen kívül van hozzárendelve

| | |
| --- | --- |
| **Súlyosság** | Magasság |
| **Miért kapok riasztást?** | A kiemelt identitáskezelésen kívül végzett kiemelt szerepkör-hozzárendelések nem megfelelően figyelhetők, és aktív támadást jelezhetnek. |
| **Hogyan lehet megjavítani?** | Tekintse át a felhasználók a listában, és távolítsa el őket a kiemelt identitáskezelésen kívül hozzárendelt kiemelt szerepkörökből. |
| **Megelőzés** | Vizsgálja meg, hogy a felhasználók a kiemelt identitáskezelésen kívül hol vannak kiemelt szerepkörökhöz rendelve, és tiltsa le a jövőbeli hozzárendeléseket. |
| **A portálon keresztüli kockázatcsökkentési művelet** | Eltávolítja a felhasználót a kiemelt szerepkörből. |

### <a name="there-are-too-many-global-administrators"></a>Túl sok a globális rendszergazda

| | |
| --- | --- |
| **Súlyosság** | Alacsony |
| **Miért kapok riasztást?** | A globális rendszergazda a legmagasabb jogosultsági szintű szerepkör. Ha egy globális rendszergazda biztonsága sérül, a támadó minden engedélyéhez hozzáfér, ami az egész rendszert veszélyezteti. |
| **Hogyan lehet megjavítani?** | Tekintse át a listában szereplő felhasználókat, és távolítson el minden olyan felhasználót, amelynek nincs feltétlenül szüksége a globális rendszergazdai szerepkörre. </br>Rendeljen alacsonyabb jogosultsággal rendelkező szerepköröket ezekhez a felhasználókhoz. |
| **Megelőzés** | Rendelje hozzá a felhasználóknak a szükséges legkevésbé kiemelt szerepkört. |
| **A portálon keresztüli kockázatcsökkentési művelet** | Eltávolítja a fiókot a kiemelt szerepkörükből. |
| **Eseményindító** | Akkor aktiválódik, ha két különböző feltétel teljesül, és mindkettő konfigurálható. Először el kell érnie a globális rendszergazdák egy bizonyos küszöbértékét. Másodszor, a teljes szerepkör-hozzárendelések bizonyos százalékának globális rendszergazdáknak kell lennie. Ha csak az egyik ilyen mérésnek felel meg, a riasztás nem jelenik meg. |
| **A globális rendszergazdák minimális száma** | Ez a beállítás adja meg a globális rendszergazdák száma, 2-100, hogy úgy gondolja, hogy túl kevés az Azure AD-szervezet. |
| **A globális rendszergazdák százalékos aránya** | Ez a beállítás határozza meg a rendszergazdák minimális százalékos, akik globális rendszergazdák, 0%-ról 100%-ra, amely alatt nem szeretné, hogy az Azure AD-szervezet dip. |

### <a name="roles-are-being-activated-too-frequently"></a>A szerepkörök túl gyakran aktiválódnak

| | |
| --- | --- |
| **Súlyosság** | Alacsony |
| **Miért kapok riasztást?** | Ugyanazon felhasználó által ugyanannak a kiemelt szerepkörnek történő többszörös aktiválása támadás jele. |
| **Hogyan lehet megjavítani?** | Tekintse át a felhasználók a listában, és győződjön meg arról, hogy az [aktiválás időtartama](pim-how-to-change-default-settings.md) a kiemelt szerepkör van beállítva elég hosszú ahhoz, hogy a feladatok elvégzéséhez. |
| **Megelőzés** | Győződjön meg arról, hogy a kiemelt szerepkörök [aktiválási időtartama](pim-how-to-change-default-settings.md) elég hosszú ahhoz, hogy a felhasználók elvégezzék a feladataikat.</br>[Többtényezős hitelesítés megkövetelése](pim-how-to-change-default-settings.md) olyan kiemelt szerepkörök esetében, amelyek fiókjait több rendszergazda osztja meg. |
| **A portálon keresztüli kockázatcsökkentési művelet** | N/A |
| **Eseményindító** | Akkor aktiválódik, ha egy felhasználó egy adott időszakon belül többször aktiválja ugyanazt a kiemelt szerepkört. Az aktiválások időtartamát és számát is beállíthatja. |
| **Aktiválás megújításának időkerete** | Ez a beállítás napokban, órákban, percekben és a gyanús megújítások nyomon követéséhez használni kívánt időszak másodpercében adja meg. |
| **Az aktiválás megújításának száma** | Ez a beállítás azt adja meg, hogy a választott időkereten belül hány aktiválást szeretne 2 és 100 között. Ezt a beállítást a csúszka mozgatásával vagy a szövegmezőbe beírva módosíthatja. |

## <a name="configure-security-alert-settings"></a>Biztonsági riasztásbeállításainak megadása

Testre szabhatja a kiemelt identitáskezelés néhány biztonsági riasztását, hogy a szervezet igényeinek és biztonsági céljainak megfelelően működjön. A biztonsági riasztás beállításainak megnyitásához kövesse az alábbi lépéseket:

1. Nyissa **meg a kiemelt identitáskezelés az** Azure AD-ben.

1. Válassza az **Azure AD-szerepkörök lehetőséget.**

1. Válassza a **Beállítások,** majd **a Riasztások lehetőséget.**

    ![Azure AD-szerepkörök – Beállítások kijelölt riasztásokkal](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Válasszon egy riasztásnevet a riasztás beállításának konfigurálásához.

    ![A kijelölt riasztáshoz a biztonsági riasztás beállításai ablaktábla](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

---

## <a name="next-steps"></a>További lépések

- [Az Azure AD szerepkör-beállításainak konfigurálása a kiemelt identitáskezelésben](pim-how-to-change-default-settings.md)
