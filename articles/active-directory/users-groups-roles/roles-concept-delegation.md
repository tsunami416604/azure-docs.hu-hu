---
title: A rendszergazdai szerepkör-delegálás megismerése – Azure Active Directory | Microsoft dokumentumok
description: Delegálási modellek, példák és szerepkör-biztonság az Azure Active Directoryban
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fa3c6bf39dbef601fe64e125999f519f725f2e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67083769"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Felügyelet delegálása az Azure Active Directoryban

A szervezeti növekedés komplexial jár. Az egyik gyakori válasz a hozzáférés-kezelés feladatainak csökkentése az Azure Active Directory (AD) rendszergazdai szerepkörökkel. A lehető legkevesebb jogosultságot rendelheti hozzá a felhasználóknak az alkalmazásaik eléréséhez és feladataik elvégzéséhez. Még akkor is, ha nem rendeli hozzá a globális rendszergazdai szerepkört minden alkalmazástulajdonoshoz, alkalmazáskezelési feladatokat helyez el a meglévő globális rendszergazdákra. Sok oka van annak, hogy egy szervezet egy decentralizáltabb adminisztráció felé mozdul el. Ez a cikk segítséget nyújt a szervezeten belüli delegálás megtervezésében.

<!--What about reporting? Who has which role and how do I audit?-->

## <a name="centralized-versus-delegated-permissions"></a>Központosított és delegált engedélyek

A szervezet növekedésével nehéz lehet nyomon követni, hogy mely felhasználók rendelkeznek adott rendszergazdai szerepkörrel. Ha egy alkalmazott rendszergazdai jogokkal rendelkezik, amelyeket nem kellene, a szervezet érzékenyebb lehet a biztonsági résekre. Általában a támogatott rendszergazdák száma és az engedélyeik részletessége általában a központi telepítés méretétől és összetettségétől függ.

* Kis vagy koncepcióálló telepítések esetén egy vagy néhány rendszergazda mindent megtesz; Nincs küldöttség. Ebben az esetben hozzon létre minden egyes rendszergazdát a Globális rendszergazda szerepkörrel.
* A több géppel, alkalmazással és asztallal rendelkező nagyobb telepítésekesetén több delegálásra van szükség. Több rendszergazda is rendelkezhet konkrétabb funkcionális feladatokkal (szerepkörök). Lehetnek például kiemelt identitású rendszergazdák, mások pedig alkalmazás-rendszergazdák. Emellett a rendszergazda csak bizonyos objektumcsoportokat, például eszközöket kezelhet.
* Még a nagyobb központi telepítések is szükség lehet még részletesebb engedélyeket, valamint esetleg a rendszergazdák nem hagyományos vagy hibrid szerepkörök.

Az Azure AD-portálon [megtekintheti bármely szerepkör összes tagját,](directory-manage-roles-portal.md)amely segítségével gyorsan ellenőrizheti a központi telepítést és delegálhatja az engedélyeket.

Ha az Azure AD-ben a rendszergazdai hozzáférés helyett az Azure-erőforrásokhoz való hozzáférés delegálása érdekli, olvassa el [a Szerepköralapú hozzáférés-vezérlési (RBAC) szerepkör hozzárendelése című témakört.](../../role-based-access-control/role-assignments-portal.md)

## <a name="delegation-planning"></a>Delegálás immár tervezése

Ez a munka, hogy dolgozzon ki egy delegálási modell, amely megfelel az Ön igényeinek. A delegálási modell kidolgozása ismétlődő tervezési folyamat, és javasoljuk, hogy kövesse az alábbi lépéseket:

* A szükséges szerepkörök meghatározása
* Alkalmazásfelügyelet delegálása
* A jelentkezések regisztrálásának lehetővé tévő lehetővé tévő megadása
* Alkalmazás tulajdonjogának delegálása
* Biztonsági terv kidolgozása
* Vészhelyzeti fiókok létrehozása
* A rendszergazdai szerepkörök védelme
* Emelt szintű jogosultsággal rendelkező jogosultsági szintezésideiglenesvé

## <a name="define-roles"></a>Szerepkörök definiálása

Határozza meg a rendszergazdák által végzett Active Directory-feladatokat, és azt, hogy hogyan felelnek meg a szerepköröknek. Részletes [szerepkör-leírások](directory-manage-roles-portal.md) az Azure Portalon megtekintheti.

Minden feladatot ki kell értékelni a gyakoriság, a fontosság és a nehézség szempontjából. Ezek a feltételek a feladatdefiníció alapvető szempontjai, mivel szabályozzák, hogy egy engedélyt delegálni kell-e:

* A rutinszerűen végzett feladatok, amelyek korlátozott kockázattal járnak, és triviálisak, kiváló jelöltek a delegálásra.
* Feladatok, hogy nem ritkán, de nagy hatással van az egész szervezet, és megkövetelik a magas képzettségi szint kell figyelembe venni nagyon óvatosan, mielőtt delegálása. Ehelyett [ideiglenesen emelhet egy fiókot a szükséges szerepkörre,](../active-directory-privileged-identity-management-configure.md) vagy újra hozzárendelheti a feladatot.

## <a name="delegate-app-administration"></a>Alkalmazásfelügyelet delegálása

Az alkalmazások elburjánzása a szervezeten belül megterhelheti a delegálási modellt. Ha az alkalmazáshozzáférés-kezelés terhét a globális rendszergazda, valószínű, hogy a modell növeli a többletterhelést az idő előrehaladtával. Ha globális rendszergazdai szerepkört adott a személyeknek például a vállalati alkalmazások konfigurálásához, most már kiszervezheti őket a következő, kevésbé kiemelt jogosultságú szerepkörökbe. Ezzel segít javítani a biztonsági testtartást, és csökkenti a szerencsétlen hibák lehetőségét. A legnagyobb jogosultsági szintű alkalmazásrendszergazdai szerepkörök a következők:

* Az **Alkalmazásfelügyelő** szerepkör, amely lehetővé teszi a címtárban lévő összes alkalmazás kezelését, beleértve a regisztrációkat, az egyszeri bejelentkezési beállításokat, a felhasználói és csoport-hozzárendeléseket és a licencelést, az alkalmazásproxy-beállításokat és a jóváhagyást. Nem teszi lehetővé a feltételes hozzáférés kezelését.
* A **Felhőalkalmazás-rendszergazda** szerepkör, amely az alkalmazás-rendszergazda összes képességét biztosítja, kivéve, hogy nem ad hozzáférést az alkalmazásproxy-beállításokhoz (mivel nem rendelkezik helyszíni engedéllyel).

## <a name="delegate-app-registration"></a>Alkalmazásregisztráció delegálása

Alapértelmezés szerint minden felhasználó létrehozhat alkalmazásregisztrációkat. Alkalmazás-regisztrációk létrehozásának szelektív megadása:

* A **felhasználók regisztrálhatnak alkalmazásokat** nem beállításra a **Felhasználói beállításokban**
* A felhasználó hozzárendelése az Alkalmazásfejlesztői szerepkörhöz

Az alkalmazások adatokhoz való hozzáférésének engedélyezéséhez való hozzájárulás lehetőségének szelektív megadása:

* A felhasználók beállítása **beleegyezhet abba, hogy a vállalati adatokhoz a nevükben hozzáférő alkalmazások** ne jelennek meg a **Felhasználói beállításokban**
* A felhasználó hozzárendelése az Alkalmazásfejlesztői szerepkörhöz

Amikor egy alkalmazásfejlesztő új alkalmazásregisztrációt hoz létre, a rendszer automatikusan hozzáadja őket első tulajdonosként.

## <a name="delegate-app-ownership"></a>Alkalmazás tulajdonjogának delegálása

A még finomabb alkalmazáshozzáférés-delegáláshoz az egyes vállalati alkalmazásokhoz tulajdonjogot rendelhet. Ez kiegészíti az alkalmazásregisztráció-tulajdonosok hozzárendeléséhez nyújtott meglévő támogatást. A tulajdonjog vállalati alkalmazásonként van hozzárendelve a Vállalati alkalmazások panelen. A haszon az, hogy a tulajdonosok csak a saját vállalati alkalmazásaikat kezelhetik. Például hozzárendelhet egy tulajdonost a Salesforce alkalmazáshoz, és a tulajdonos kezelheti a Salesforce-hoz való hozzáférést és a konfigurációt, más alkalmazásokat nem. Egy vállalati alkalmazásnak sok tulajdonosa lehet, és a felhasználó számos vállalati alkalmazás tulajdonosa lehet. Két alkalmazástulajdonosi szerepkör létezik:

* A **Vállalati alkalmazás tulajdonosa** szerepkör lehetővé teszi a "a felhasználó tulajdonában lévő vállalati alkalmazások kezelését, beleértve az egyszeri bejelentkezési beállításokat, a felhasználói és csoport-hozzárendeléseket, valamint további tulajdonosok hozzáadását. Nem teszi lehetővé az alkalmazásproxy-beállítások vagy a feltételes hozzáférés kezelését.
* Az **alkalmazásregisztráció tulajdonosi** szerepköre lehetővé teszi a felhasználó tulajdonában lévő alkalmazásregisztrációk kezelését, beleértve az alkalmazásjegyzéket és további tulajdonosok hozzáadását.

## <a name="develop-a-security-plan"></a>Biztonsági terv kidolgozása

Az Azure AD átfogó útmutatót nyújt az Azure AD-rendszergazdai szerepkörök biztonsági tervének tervezéséhez [és végrehajtásához, a kiemelt hozzáférés biztosításához a hibrid és felhőalapú telepítésekhez.](directory-admin-roles-secure.md)

## <a name="establish-emergency-accounts"></a>Vészhelyzeti fiókok létrehozása

Az identitáskezelési tárolóhoz való hozzáférés fenntartásához probléma esetén készítse elő a vészelérési fiókokat [a Vészhozzáférés-rendszergazdai fiókok létrehozása](directory-emergency-access.md)szolgáltatással.

## <a name="secure-your-administrator-roles"></a>A rendszergazdai szerepkörök védelme

A támadók, akik a kiemelt jogosultságú fiókok vezérlését is hatalmas károkat okozhat, ezért ezeket a fiókokat először védje, az [alapszintű hozzáférési szabályzat,](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/) amely alapértelmezés szerint elérhető az összes Azure AD-bérlők (nyilvános előzetes verzióban). A szabályzat többtényezős hitelesítést kényszerít ki a kiemelt Azure AD-fiókokon. Az Azure AD alapszabályzata a következő Azure AD-szerepköröket fedi le:

* Globális rendszergazda
* SharePoint-rendszergazda
* Exchange-rendszergazda
* Feltételes hozzáférés rendszergazdája
* Biztonsági rendszergazda

## <a name="elevate-privilege-temporarily"></a>Jogosultság ideiglenes felemelése

A legtöbb napi tevékenység esetében nem minden felhasználónak van szüksége globális rendszergazdai jogokra, és nem mindegyiknek kell véglegesen hozzárendelnie a globális rendszergazdai szerepkörhöz. Ha a felhasználóknak szükségük van egy globális rendszergazda engedélyére, aktiválniuk kell a szerepkör-hozzárendelést az Azure AD [kiemelt identitáskezelés](../active-directory-privileged-identity-management-configure.md) ben, akár saját fiókjukon, akár egy másik felügyeleti fiókon.

## <a name="next-steps"></a>További lépések

Az Azure AD-szerepkör-leírásokra való hivatkozásért [lásd: Rendszergazdai szerepkörök hozzárendelése az Azure AD-ben](directory-assign-admin-roles.md)
