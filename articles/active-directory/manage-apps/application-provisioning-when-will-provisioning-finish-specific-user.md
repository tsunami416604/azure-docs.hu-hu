---
title: Ismerje meg, ha egy adott felhasználó fog tudni hozzáférni egy alkalmazáshoz |} A Microsoft Docs
description: Hogyan tudhatja meg, ha egy kritikus fontosságú felhasználói férhetnek hozzá a felhasználókiépítés Azure AD-val konfigurált alkalmazás
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fd6b70e7a4542a4cad2ee95fa280ddf8fbe6553
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310036"
---
# <a name="check-the-status-of-user-provisioning"></a>A felhasználók átadásának állapotának ellenőrzése

Az Azure AD létesítési szolgáltatás fut a forrás és cél rendszer, rendszeres növekményes ciklusok követ egy kezdeti üzembe helyezési ciklust. Amikor konfigurál egy alkalmazáshoz tartozó kiépítés, ellenőrizze a kiépítési szolgáltatás aktuális állapotát, és tekintse meg, amikor egy felhasználó fog tudni hozzáférni egy alkalmazáshoz.

## <a name="view-the-provisioning-progress-bar-preview"></a>A kiépítés megtekintése a folyamatjelző (előzetes verzió)

 Az a **kiépítési** oldalon az alkalmazás megtekintheti az Azure AD létesítési szolgáltatás állapotát. A **aktuális állapot** az oldal alján szakasz azt mutatja be, hogy egy üzembe helyezési ciklust a felhasználói fiókok kiépítése megkezdődött. Tekintse meg a ciklus előrehaladását, tekintse meg, hány felhasználók és csoportok lettek telepítve, és tekintse meg, hány szerepkör jön létre.

Amikor először konfigurálja az Automatikus kiépítés a **aktuális állapot** a lap alján található szakasz a kezdeti üzembe helyezési ciklust állapotát jeleníti meg. Ez a szakasz egy növekményes ciklus minden futtatásakor frissíti. Az alábbi részletek láthatók:
- Az üzembe helyezési ciklust (kezdeti vagy növekményes), amely jelenleg fut, vagy utolsó befejeződött típusa.
- A **folyamatjelző sáv** , hogy befejeződött az üzembe helyezési ciklust arányát mutatja. A százalékos kiosztott oldalak számát tükrözi. Vegye figyelembe, hogy mindegyik oldalon több felhasználók vagy csoportok, így a százalékos közvetlenül nem kapcsolható a felhasználók száma, csoportok vagy szerepkörök kiosztása tartalmazhatnak.
- A **frissítése** használhatja, hogy a nézet frissítése gombra.
- Hány **felhasználók** és **csoportok** kiépítve, és a létrehozott szerepkörök száma. A kezdeti ciklus során a **felhasználók** száma dátumonként 1 amikor egy felhasználó létrehozásakor vagy frissítésekor és számolja 1 felhasználó törlésekor. Egy növekményes ciklus során felhasználói frissítések nem befolyásolják a **felhasználók** száma; a megváltozása csak akkor, amikor a felhasználók számára létrehozott vagy törölt.
- A **Audit naplók megtekintése** hivatkozásra, amely megnyitja az Azure AD naplózási naplók esetében a felhasználó a kiépítési szolgáltatást, ideértve a kiépítési állapota az egyes felhasználók által futtatott összes művelet részleteit (lásd a [használati naplók](#use-audit-logs-to-check-a-users-provisioning-status)szakaszt).

Üzembe helyezési ciklust befejezése után a **dátum statisztikák** szakasz felhasználókat és csoportokat, dátum, a befejezési dátumot és az utolsó ciklus időtartamát és a kiépített összesített számát mutatja. A **Tevékenységazonosító** egyedileg azonosítja a legutóbbi üzembe helyezési ciklust. A **Feladatazonosító** az üzembe helyezési feladat egyedi azonosítója, és a bérlőben található alkalmazás jellemző.

A kiépítési folyamat is megtekinteni az Azure Portalon a **Azure Active Directory &gt; vállalati alkalmazások &gt; \[alkalmazásnév\] &gt; kiépítési** fülre.

![Üzembe helyezési oldal folyamatjelző sáv](media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-audit-logs-to-check-a-users-provisioning-status"></a>Auditnaplók használatát egy felhasználó kiépítési állapotának ellenőrzése

Tekintse meg a kiválasztott felhasználóról a kiépítési állapotot, tekintse meg a naplók az Azure ad-ben. Az Azure AD-ben a kiépítési szolgáltatás felhasználó által futtatott összes művelet rögzíti a naplók. Ez tartalmazza az összes olvasási és írási műveleteken végrehajtott a forrás- és rendszerek és a felhasználói adatokat az egyes műveletek során írt adatok és olvasható.

Az üzembe helyezési naplók elérhetők az Azure Portalon a **Azure Active Directory &gt; vállalati alkalmazások &gt; \[alkalmazásnév\] &gt; Auditnaplók** fülre. A naplók szűrése a **fiók üzembe helyezésének** kategóriát csak az alkalmazás az üzembe helyezési események megtekintéséhez. A felhasználók számára a "egyező azonosító", amelyet a számukra az attribútumleképezések alapján kereshet. 

Például, ha konfigurálta a "felhasználó egyszerű neve" vagy "e-mail-cím", a megfelelő attribútum az Azure ad-ben oldalán, és a felhasználó nem kiépítése, amelynek értéke "audrey@contoso.com", majd keresse meg a naplófájlok "audrey@contoso.com" majd tekintse át a bejegyzéseket, és adott vissza.

Az üzembe helyezési naplók jegyezze fel a kiépítési szolgáltatás által végrehajtott összes műveletet többek között:

* Az Azure AD-hez hozzárendelt felhasználók a kiépítés hatókörébe lekérdezése
* Ezek a felhasználók létezik-e a célalkalmazásnak lekérdezése
* A felhasználói objektumok között a rendszer összehasonlítása
* Hozzáadása, frissítése vagy a felhasználói fiók letiltása a célrendszeren, az összehasonlítás alapján

Olvassa el a naplókat az Azure Portalon való további információkért lásd: a [kiépítési jelentéskészítési útmutató](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Mennyi ideig tart a felhasználók?
Felhasználók automatikus átadása az alkalmazás használatakor az Azure AD automatikusan építi ki és frissíti a felhasználói fiókok alkalmazást többek között a [felhasználó és csoport-hozzárendelések](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) rendszeresen ütemezett időintervallum, általában 10 percenként.

Ki kell építeni az adott felhasználó számára szükséges idő attól függ, főleg az üzembe helyezési feladat fut-e egy kezdeti szinkronizálást és a egy növekményes szinkronizálást.

- A **a kezdeti szinkronizálás**, a feladat ideje függ a sok tényező befolyásolja, többek között a felhasználókat és csoportokat, kiépítés hatókörébe, és a felhasználókat és csoportokat a forrásrendszerben teljes száma. Az első szinkronizálás az Azure AD között és a egy alkalmazást is igénybe vehet 20 perc alatt az Azure AD-címtár és a kiépítés hatókörébe felhasználók méretétől függően több órát. Kezdeti szinkronizálás teljesítményét befolyásoló tényezőket átfogó listája ebben a szakaszban később foglalja össze.

- A **növekményes szinkronizálás** a kezdeti szinkronizálást követően feladat alkalommal általában gyorsabb (pl. 10 percen belül), ahogy a kiépítési szolgáltatás tárolja, amely mindkét rendszer állapotát képviselik a teljesítmény javítása a kezdeti szinkronizálást követően a vízjelek az ezt követő szinkronizálások. A feladat ideje függ, hogy üzembe helyezési ciklust észlelt módosítások számát. Ha 5000-nél több felhasználó vagy csoport tagsága módosul, a feladat be tudja belül egyetlen növekményes üzembe helyezési ciklust. 

A következő táblázat összefoglalja a szinkronizálás időpontjait általános üzembe helyezési forgatókönyvhöz. Ezekben az esetekben a forrásrendszerben Azure ad-ben pedig a célrendszeren egy SaaS-alkalmazáshoz. A szinkronizálási idő származik, a ServiceNow, a munkahelyen, a Salesforce és a G Suite SaaS-alkalmazásokhoz tartozó szinkronizálási feladatokat statisztikai elemzése.


| Hatókör-konfiguráció | Felhasználók, csoportok és tagok hatókörében | Kezdeti szinkronizálás ideje | Növekményes szinkronizálás ideje |
| -------- | -------- | -------- | -------- |
| Hozzárendelt felhasználók és csoportok csak szinkronizálás |  < 1,000 |  < 30 minutes | < 30 minutes |
| Hozzárendelt felhasználók és csoportok csak szinkronizálás |  1000–10 000 | 142 - 708 perc | < 30 minutes |
| Hozzárendelt felhasználók és csoportok csak szinkronizálás |   10,000 - 100,000 | 1,170 - 2,340 perc | < 30 minutes |
| Minden felhasználó és csoport szinkronizálása az Azure ad-ben |  < 1,000 | < 30 minutes  | < 30 minutes |
| Minden felhasználó és csoport szinkronizálása az Azure ad-ben |  1000–10 000 | < 30 – 120 perc | < 30 minutes |
| Minden felhasználó és csoport szinkronizálása az Azure ad-ben |  10,000 - 100,000  | 713 - 1,425 perc | < 30 minutes |
| Összes felhasználók szinkronizálása az Azure ad-ben|  < 1,000  | < 30 minutes | < 30 minutes |
| Összes felhasználók szinkronizálása az Azure ad-ben | 1000–10 000  | 43 - 86 perc | < 30 minutes |


A konfiguráció **szinkronizálási rendelt felhasználók és csoportok csak**, a következő képletek segítségével meghatározhatja a hozzávetőleges minimális és maximális várt **a kezdeti szinkronizálás** időpontok:

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Összegzés befejezéséhez szükséges idő befolyásoló tényezők egy **a kezdeti szinkronizálás**:

- A felhasználók és csoportok a kiépítés hatókörébe teljes száma.

- A felhasználók, csoportok és a forrás rendszerben (Azure AD) csoport tagjai teljes száma.

- E felhasználók kiépítés hatókörébe teljesül, a meglévő felhasználók számára a célalkalmazás vagy az első alkalommal kell létrehozni. Szinkronizálási feladat, amelynek minden felhasználó jönnek létre az első alkalommal körülbelül igénybe *kétszer mindaddig* , feladatok, amelynek minden felhasználó egyeztetését a meglévő felhasználók szinkronizálása.

- Hibák száma a [auditnaplók](check-status-user-account-provisioning.md). Teljesítmény lassabb lesz, ha sok hiba, és az eszközkiépítési szolgáltatás karantén állapotba került.    

- A sebességkorlátokat és a szabályozás, melyet a célrendszer megvalósított kérelmet. Néhány célrendszereket megvalósítása kérelem sebességkorlátok és a szabályozás, amely befolyásolhatja a teljesítményt nagy szinkronizálási műveletek során. Ezen feltételek mellett egy alkalmazást, amely megkapja a túl sok kérelem túl nagy lehet, hogy a válaszadási arányát lassú vagy bontja a kapcsolatot. A teljesítmény javítása érdekében az összekötőjét beállítása nem az alkalmazás kérések küldésével gyorsabb, mint az alkalmazás azokat fel tudná dolgozni. Üzembe helyezési összekötők a Microsoft által készített ellenőrizze ezt a módosítást. 

- A szám és hozzárendelt csoportok méretét. Hozzárendelt csoportok szinkronizálása, mint a felhasználók szinkronizálása hosszabb időt vesz igénybe. A szám és a méretét a hozzárendelt csoportok hatással lehet a teljesítményre. Ha egy alkalmazásnak [engedélyezve csoport objektum szinkronizálása a hozzárendelések](customize-application-attributes.md#editing-group-attribute-mappings), a csoport tulajdonságai, például a csoportneveket és csoporttagságok szinkronizált felhasználók mellett. Ezek további szinkronizálások csak a felhasználói objektumok szinkronizálása hosszabb vesz igénybe.

## <a name="next-steps"></a>További lépések
[Felhasználók átadásának és megszüntetésének automatizálása az SaaS-alkalmazásokban az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)
