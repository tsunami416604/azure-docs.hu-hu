---
title: Probléma a felhasználók átadása egy Azure AD-katalógusában alkalmazás konfigurálása |} Microsoft Docs
description: Konfigurálását a felhasználók átadása egy alkalmazás már szerepel az Azure AD Application Gallery tapasztalt kapcsolatos gyakori hibák elhárítása
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 637a2af17cf3b36392e0a694df8c10a55d616675
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333023"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>A probléma a felhasználók átadása egy Azure AD-katalógusában alkalmazás konfigurálása

Konfigurálása [automatikus felhasználólétesítés](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) alkalmazás (ha támogatott), megköveteli, hogy a konkrét utasításokat kell követni az alkalmazás automatikus kiépítés előkészítéséhez. Az Azure portál segítségével majd az alkalmazás felhasználói fiókok szinkronizálása a létesítési szolgáltatás konfigurálásához.

Keresse meg a telepítő az oktatóanyag az alkalmazás kiépítés beállítására vonatkozó mindig el kell kezdenie. Kövesse a fenti lépések végrehajtásával konfigurálhatja az alkalmazást és az Azure AD-be a létesítési kapcsolat létrehozása. Egy alkalmazás bemutatók felsorolása található [integrálhatja SaaS-alkalmazásokhoz az Azure Active Directoryval kapcsolatos lista a bemutatók](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

## <a name="how-to-see-if-provisioning-is-working"></a>Hogy működik-e a kiépítés megtekintése 

Ha a szolgáltatás már konfigurálva van, a művelet a szolgáltatás a legtöbb betekintést megállapítható két helyen:

-   **A naplók** – a telepítési naplók az rekord minden a létesítési szolgáltatás, így az Azure ad-val lekérdezése által végrehajtott műveletek felelőse felhasználók hatókörébe történő üzembe helyezéséhez. A lekérdezés a cél alkalmazás azoknak a felhasználóknak a felhasználói objektumok között a rendszer összehasonlítja a megléte. Majd adja hozzá, frissíteni vagy tiltsa le a felhasználói fiókot a célrendszeren, az összehasonlítás alapján. A telepítési naplók érhetők el az Azure portálon, a **Azure Active Directory &gt; vállalati alkalmazások &gt; \[alkalmazásnév\] &gt; vizsgálati naplókban** fülre. A naplók szűrést végezni a **fiók** kategóriát csak az adott alkalmazáshoz létesítési események megjelenítéséhez.

-   **Előkészítési állapotát –** utolsó kiépítési összegzését futtatni egy adott alkalmazás látható a **Azure Active Directory &gt; vállalati alkalmazások &gt; \[alkalmazásnév\] &gt;Kiépítés** szakaszban, a szolgáltatás beállításai alapján a képernyő alján. Ez a szakasz foglalja össze, hogy hány felhasználók (és/vagy csoportok) vannak jelenleg szinkronizálja a két rendszer között, és ha hiba történik. Hiba legutolsó részletes adatai a felügyeleti naplók találhatók. Vegye figyelembe, hogy az üzembe helyezési állapota nem tölthető fel, amíg egy teljes kezdeti szinkronizálása befejeződött az Azure AD között és az alkalmazás.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Vegye figyelembe, hogy az általános problémás területek

Az alábbiakban olvashat egy listát részletezhető Ha egy meghatározni, hogy hol kell elkezdeni az általános probléma területet.

* [Szolgáltatás kiépítését nem jelenik meg elindítani](#provisioning-service-does-not-appear-to-start)
* [Konfiguráció nem működik app hitelesítő adatok miatt nem lehet menteni.](#can’t-save-configuration-due-to-app-credentials-not-working)
* [Naplók fel felhasználókat "kihagyva", és nincs telepítve, akkor is, ha hozzárendeli őket](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Szolgáltatás kiépítését nem jelenik meg elindítani

Ha a **kiépítési állapot** kell **a** a a **Azure Active Directory &gt; vállalati alkalmazások &gt; \[alkalmazásnév\] &gt;Kiépítési** szakasza az Azure-portálon. Azonban nincs más állapot részletei jelennek meg, hogy a lap után további Újratölti. Valószínű, hogy a szolgáltatás fut, de egy kezdeti szinkronizálást még nem fejeződött be. Ellenőrizze a **naplók** milyen műveleteket hajt végre a szolgáltatást, hogy a fent leírt, és ha hiba történik.

>[!NOTE]
>Egy kezdeti szinkronizálást is igénybe vehet 20 percet az Azure AD-címtár és a felhasználók kialakítási hatókörében számát méretétől függően több órát. A kezdeti szinkronizálás után az ezt követő szinkronizálások lehet gyorsabb, mint a létesítési szolgáltatás, amelyben a kezdeti szinkronizálást, és ezt követő szinkronizálások teljesítményének javítása után mindkét állapota egy vízjelek tárolja.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Konfiguráció nem működik app hitelesítő adatok miatt nem lehet menteni.

Ahhoz, hogy a létesítési működéséhez az Azure AD érvényes hitelesítő adatokat, hogy engedélyezi-e csatlakozni a felhasználói felügyeleti API-alkalmazás által biztosított van szükség. Ha ezek a hitelesítő adatok nem működik, vagy nem tudja, Nyugat-afrikai vannak, tekintse át az oktatóanyag az alkalmazáshoz, a fentiekben ismertetett beállításához.

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Naplók mondja ki a felhasználók figyelmen hagyja, és nincs telepítve, akkor is, ha hozzárendeli őket

Amikor a felhasználó megjelenik, a "kihagyva" a naplófájlban, nagyon fontos olvasni a kiterjesztett részleteit a napló üzenet okának megállapításához. Az alábbiakban gyakori okok és megoldások:

-   **Hatókörként szűrő konfigurációja** **, amely van kiszűrése a felhasználói egy attribútum-érték alapján**. A helyezése hatókörszűrőkkel további információkért lásd: <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **A felhasználó az "nem hatékony jogosult".** Ha hibaüzenet jelenik meg, azért, mert az Azure AD-ben tárolt felhasználói hozzárendelés rekord problémáját. Hárítsa el a problémát, megszüntetése a felhasználó (vagy csoport) az alkalmazásból, és újból rendelje hozzá újra. A hozzárendelés további információkért lásd: <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Egy kötelező attribútum hiányzik vagy nem ki van töltve egy felhasználó.** Kiépítés beállítása során figyelembe kell venni egy fontos dolog lehet áttekintése és konfigurálása a attribútum-leképezésekhez és a munkafolyamatok, amelyek meghatározzák, milyen felhasználói (vagy a csoport) tulajdonságok folyamata az Azure AD az alkalmazásnak. Ez magában foglalja a "egyező property" beállítás használható egyedileg azonosíthatja és felel meg a felhasználókat/csoportokat a két rendszer között. További információ a fontos folyamatban: <https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings>.

   * **Azon csoportok attribútum:** telepítése a csoport nevét és a csoport részletes adatait, a tagok, ha az egyes alkalmazásokra támogatott mellett. Engedélyezheti vagy letilthatja ezt a funkciót engedélyezésével vagy letiltásával a **leképezési** a csoport objektumainak látható a **kiépítési** fülre. Ha csoportok kiépítés engedélyezve van, feltétlenül olvassa el a attribútum-leképezésekhez ellenőrizze a megfelelő mezőben használja a "egyező azonosító". Ez lehet a megjelenített nevet vagy e-mail címét), mert a csoport és annak tagjait nem építhető ki, ha a megfelelő tulajdonság értéke üres vagy nem ki van töltve a csoport az Azure AD.

## <a name="next-steps"></a>További lépések
[Felhasználói kiépítésének és megszüntetésének biztosítása SaaS-alkalmazásokhoz az Azure Active Directoryval történő automatizálásához](active-directory-saas-app-provisioning.md)
