---
title: Egy felhasználó sem törlődnek alatt egy Azure AD-katalógusában alkalmazás |} Microsoft Docs
description: Kapcsolatos gyakori hibák elhárítása tapasztalt felhasználók jelennek meg nem jelenik meg az Azure AD egy gyűjtemény alkalmazás már konfigurálta a felhasználók átadása az Azure ad-vel
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: asteen
ms.openlocfilehash: a36d60b8915ae0b46226bb2127829a8f1767daba
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
ms.locfileid: "26616293"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Egy felhasználó sem törlődnek alatt egy Azure AD-katalógusában alkalmazáshoz

Amennyiben az Automatikus kiépítés konfigurálták az alkalmazáshoz (beleértve az ellenőrzése, hogy a alkalmazást az Azure AD-be az alkalmazáshoz megadott hitelesítő adatok érvényesek). Majd a felhasználók és/vagy csoportok törlődnek az alkalmazásba, és határozza meg a következőket:

-   Amelyek felhasználókat és csoportokat **hozzárendelt** az alkalmazáshoz. A hozzárendelés további információkért lásd: [egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

-   E **attribútum-hozzárendelések** engedélyezni és konfigurálni az alkalmazást az Azure AD érvényes attribútumok szinkronizálása. Attribútum-leképezésekhez további információkért lásd: [testreszabása felhasználói kiépítés attribútum-leképezésekhez az SaaS-alkalmazásokhoz az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

-   Van-e egy **tartalmazó szűrő** jelen van, hogy felhasználók egyedi attribútum értékek alapján. A helyezése hatókörszűrőkkel további információkért lásd: [alkalmazások Attribútumalapú üzembe helyezése hatókörszűrőkkel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

Amikor tartják be, hogy felhasználók nem alatt törlődnek, tekintse meg a felügyeleti naplók az Azure ad-ben, és keresse meg naplóbejegyzések adott felhasználó.

A telepítési naplók érhetők el az Azure portálon, a **Azure Active Directory &gt; vállalati alkalmazások &gt; \[alkalmazásnév\] &gt; vizsgálati naplókban** fülre. A naplók szűrést végezni a **fiók** kategóriát csak az adott alkalmazáshoz létesítési események megjelenítéséhez. A "egyező ID" attribútum-leképezésekhez számukra konfigurált alapján is kereshet. Például, ha az "egyszerű felhasználónév" vagy "e-mail cím" az Azure AD-oldalán a megfelelő attribútumaként konfigurálva, és a felhasználó nem kiépítés értéke "audrey@contoso.com". Majd keresse meg a naplókban talál "audrey@contoso.com", és tekintse át ezt követően eredményt adott vissza.

A létesítési naplók az összes műveletet végzi el a létesítési szolgáltatás, így az Azure AD-hez hozzárendelt felhasználók hatókörébe kiépítés, a cél alkalmazás meglétét azoknak a felhasználóknak az lekérdezése, a felhasználói objektumok között a rendszer összehasonlítja a lekérdezése rekord. Majd adja hozzá, frissíteni vagy tiltsa le a felhasználói fiókot a célrendszeren, az összehasonlítás alapján.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>A Szolgáltatáskiépítéssel megfontolandó általános problémás területek

Az alábbiakban olvashat egy listát részletezhető Ha egy meghatározni, hogy hol kell elkezdeni az általános probléma területet.

* [Szolgáltatás kiépítését nem jelenik meg elindítani](#provisioning-service-does-not-appear-to-start)
* [Naplók mondja ki a felhasználók figyelmen hagyja, és nincs telepítve, akkor is, ha hozzárendeli őket](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Szolgáltatás kiépítését nem jelenik meg elindítani

Ha a **kiépítési állapot** kell **a** a a **Azure Active Directory &gt; vállalati alkalmazások &gt; \[alkalmazásnév\] &gt;kiépítési** szakasza az Azure-portálon. Azonban nem más állapot részletei jelennek meg a lap után további betölti, valószínű, hogy a szolgáltatás fut, de egy kezdeti szinkronizálást még nem fejeződött be. Ellenőrizze a **naplók** milyen műveleteket hajt végre a szolgáltatást, hogy a fent leírt, és ha hiba történik.

>[!NOTE]
>Egy kezdeti szinkronizálást is igénybe vehet 20 percet az Azure AD-címtár és a felhasználók kialakítási hatókörében számát méretétől függően több órát. A kezdeti szinkronizálás után az ezt követő szinkronizálások nem gyorsabb, mivel a létesítési szolgáltatás, amelyben a kezdeti szinkronizálás után mindkét állapota egy vízjelek tárolja. Ez javítja ezt követő szinkronizálások teljesítményét.
>
>

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Naplók mondja ki a felhasználók figyelmen hagyja, és nincs telepítve, akkor is, ha hozzárendeli őket

Amikor a felhasználó megjelenik, a "kihagyva" a naplófájlban, nagyon fontos olvasni a kiterjesztett részleteit a napló üzenet okának megállapításához. Az alábbiakban gyakori okok és megoldások:

-   **Hatókörként szűrő konfigurációja** **, amely van kiszűrése a felhasználói egy attribútum-érték alapján**. A helyezése hatókörszűrőkkel további információkért lásd: <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **A felhasználó az "nem hatékony jogosult".** Ha hibaüzenet jelenik meg, azért, mert az Azure AD-ben tárolt felhasználói hozzárendelés rekord problémáját. Hárítsa el a problémát, megszüntetése a felhasználó (vagy csoport) az alkalmazásból, és újból rendelje hozzá újra. A hozzárendelés további információkért lásd: <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Egy kötelező attribútum hiányzik vagy nem ki van töltve egy felhasználó.** Kiépítés beállítása során figyelembe kell venni egy fontos dolog lehet áttekintése és konfigurálása a attribútum-leképezésekhez és a munkafolyamatok, amelyek meghatározzák, milyen felhasználói (vagy a csoport) tulajdonságok folyamata az Azure AD az alkalmazásnak. Ez magában foglalja a "egyező property" beállítás használható egyedileg azonosíthatja és felel meg a felhasználókat/csoportokat a két rendszer között. További információ a fontos folyamatban: [testreszabása felhasználói kiépítés attribútum-leképezésekhez az SaaS-alkalmazásokhoz az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

  * **Azon csoportok attribútum:** telepítése a csoport nevét és a csoport részletes adatait, a tagok, ha az egyes alkalmazásokra támogatott mellett. Engedélyezheti vagy letilthatja ezt a funkciót engedélyezésével vagy letiltásával a **leképezési** a csoport objektumainak látható a **kiépítési** fülre. Ha csoportok kiépítés engedélyezve van, feltétlenül olvassa el a attribútum-leképezésekhez ellenőrizze a megfelelő mezőben használja a "egyező azonosító". Ez lehet a megjelenített nevet vagy e-mail címét), mert a csoport és annak tagjait nem építhető ki, ha a megfelelő tulajdonság értéke üres vagy nem ki van töltve a csoport az Azure AD.

## <a name="next-steps"></a>Következő lépések
[Azure AD Connect szinkronizálása: Understanding deklaratív kiépítés](active-directory-aadconnectsync-understanding-declarative-provisioning.md)

