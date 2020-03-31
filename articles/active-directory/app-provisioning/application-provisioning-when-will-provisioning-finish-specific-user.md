---
title: Annak megtudd, hogy egy adott felhasználó mikor férhet hozzá egy alkalmazáshoz
description: Hogyan lehet megtudni, hogy egy kritikus fontosságú felhasználó képes-e hozzáférni egy olyan alkalmazáshoz, amelyet az Azure AD-vel való felhasználói kiépítéshez konfigurált
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48370e2806b70d550bce95ceff3857a79561f247
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264140"
---
# <a name="check-the-status-of-user-provisioning"></a>A felhasználói kiépítés állapotának ellenőrzése

Az Azure AD-kiépítési szolgáltatás egy kezdeti kiépítési ciklust futtat a forrásrendszeren és a célrendszeren, amelyet időszakos növekményes ciklusok követnek. Amikor konfigurálja egy alkalmazás kiépítését, ellenőrizheti a létesítési szolgáltatás aktuális állapotát, és megtekintheti, hogy a felhasználó mikor férhet hozzá egy alkalmazáshoz.

## <a name="view-the-provisioning-progress-bar"></a>A kiépítési folyamatsáv megtekintése

 Az alkalmazás **kiépítéslapján** megtekintheti az Azure AD-kiépítési szolgáltatás állapotát. Az **Aktuális állapot** szakasz a lap alján azt mutatja, hogy a kiépítési ciklus megkezdte-e a felhasználói fiókok kiépítését. Megtekintheti a ciklus előrehaladását, megtekintheti, hogy hány felhasználó és csoport lett kiépítve, és megtekintheti, hogy hány szerepkör jön létre.

Az automatikus kiépítés első konfigurálásakor a lap alján található **Aktuális állapot** szakasz a kezdeti kiépítési ciklus állapotát jeleníti meg. Ez a szakasz minden alkalommal frissül, amikor egy növekményes ciklus fut. A következő részletek jelennek meg:
- A jelenleg futó vagy utoljára befejeződött kiépítési ciklus típusa (kezdeti vagy növekményes).
- A kiépítési ciklus befejezett százalékos arányát megjelenítő **folyamatjelző.** A százalékos érték a kiosztott oldalak számát tükrözi. Vegye figyelembe, hogy minden oldal több felhasználót vagy csoportot tartalmazhat, így a százalékos arány nem kapcsolódik közvetlenül a kiosztott felhasználók, csoportok vagy szerepkörök számához.
- A Nézet naprakészen tartására használható **Frissítés** gomb.
- Az összekötő adattárban lévő **felhasználók** és **csoportok** száma. A szám növekszik, ha egy objektum hozzá van adva a kiépítés hatóköréhez. A számláló nem fog lemenni, ha a felhasználó helyreállíthatóan törölt vagy törölt, mert ez nem távolítja el az objektumot az összekötő adattárából. A CDS alaphelyzetbe állítása után a számláló az első szinkronizálást [újrafelállítja.](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) 
- A **Naplózási naplók megtekintése** hivatkozás, amely megnyitja az Azure AD kiépítési naplók a felhasználói létesítési szolgáltatás által futtatott összes művelet részleteit, beleértve az egyes felhasználók kiépítési állapotát (lásd alább a [Kiépítési naplók használata](#use-provisioning-logs-to-check-a-users-provisioning-status) című szakaszt).

A kiépítési ciklus befejezése után a **Statisztika a mai napig** szakasz ban a felhasználók és csoportok összesített száma, amelyek kivannak építve a mai napig, valamint a befejezési dátum és időtartama az utolsó ciklus. A **tevékenységazonosító** egyedileg azonosítja a legutóbbi kiépítési ciklust. A **feladatazonosító** a létesítési feladat egyedi azonosítója, és a bérlőben lévő alkalmazásra jellemző.

A kiépítési folyamat megtekinthető az Azure Portalon, az **Azure Active &gt; Directory Enterprise Apps &gt; \[alkalmazás\] &gt; név kiépítés** lapon.

![A lap állapotának kiépítése](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>A kiépítési naplók használata a felhasználó kiépítési állapotának ellenőrzéséhez

A kijelölt felhasználó létesítési állapotának megtekintéséhez tekintse meg a [kiépítési naplók (előzetes verzió)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) az Azure AD-ben. A felhasználói létesítési szolgáltatás által futtatott összes művelet et az Azure AD-kiépítési naplók rögzítik. Ez magában foglalja a forrás- és célrendszereken végzett összes olvasási és írási műveletet, valamint az egyes műveletek során olvasott vagy írt felhasználói adatokat.

Az Azure-portálon a létesítési naplók eléréséhez válassza az **Azure Active Directory** &gt; **Vállalati alkalmazások** &gt; **kiépítése naplók (előzetes verzió)** a **tevékenység** szakaszban. A kiépítési adatok ban a felhasználó neve vagy az azonosító alapján kereshet a forrásrendszerben vagy a célrendszerben. További információt a [Naplók kiépítése (előzetes verzió) című témakörben talál.](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 

A létesítési naplók rögzítik a létesítési szolgáltatás által végrehajtott összes műveletet, beleértve a következőket:

* Azure AD lekérdezése olyan hozzárendelt felhasználók számára, akik kiépítéshatókörben vannak
* A célalkalmazás lekérdezése a felhasználók létezéséről
* A felhasználói objektumok összehasonlítása a rendszer között
* A felhasználói fiók hozzáadása, frissítése vagy letiltása a célrendszerben az összehasonlítás alapján

A kiépítési naplók azure portalon való olvasásáról a [kiépítési jelentéskészítési útmutatóban](check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="how-long-will-it-take-to-provision-users"></a>Mennyi ideig tart a felhasználók kiépítése?
Ha egy alkalmazással automatikus felhasználói kiépítést használ, az Azure AD automatikusan rendelkezik, és frissíti a felhasználói fiókokat egy alkalmazásban például [a felhasználói és csoport-hozzárendelés](../manage-apps/assign-user-or-group-access-portal.md) alapján rendszeresen ütemezett időintervallumban, általában 40 percenként.

Egy adott felhasználó kiépítéséhez szükséges idő főként attól függ, hogy a létesítési feladat egy kezdeti ciklust vagy egy növekményes ciklust futtat.

- A **kezdeti ciklusban**a feladat idő számos tényezőtől függ, beleértve a felhasználók számát és a kiépítés hatókörében lévő csoportokat, valamint a felhasználók és csoportok teljes számát a forrásrendszerben. Az Azure AD és egy alkalmazás közötti első szinkronizálás 20 perctől több óráig is eltarthat, az Azure AD-címtár méretétől és a kiépítési hatókörben lévő felhasználók számától függően. A kezdeti ciklus teljesítményét befolyásoló tényezők átfogó listáját a szakasz későbbi részében foglaljuk össze.

- A kezdeti ciklus utáni **növekményes ciklusok** esetében a feladatidők általában gyorsabbak (pl. 10 percen belül), mivel a létesítési szolgáltatás olyan vízjeleket tárol, amelyek a kezdeti ciklus után mindkét rendszer állapotát képviselik, javítva a későbbi szinkronizálások teljesítményét. A feladat ideje az adott kiépítési ciklusban észlelt módosítások számától függ. Ha kevesebb, mint 5000 felhasználó vagy csoport tagsága változik, a feladat egyetlen növekményes kiépítési cikluson belül befejeződhet. 

Az alábbi táblázat összefoglalja a szinkronizálási időket a gyakori kiépítési forgatókönyvek esetében. Ezekben a forgatókönyvekben a forrásrendszer az Azure AD, és a célrendszer egy SaaS-alkalmazás. A szinkronizálási idők a ServiceNow, workplace, salesforce és g suite SaaS-alkalmazások szinkronizálási feladatainak statisztikai elemzéséből származnak.


| Hatókör konfigurációja | A hatókör felhasználói, csoportjai és tagjai | Kezdeti ciklusidő | Növekményes ciklusidő |
| -------- | -------- | -------- | -------- |
| Csak a hozzárendelt felhasználók és csoportok szinkronizálása |  1000 < |  < 30 perc | < 30 perc |
| Csak a hozzárendelt felhasználók és csoportok szinkronizálása |  1,000 - 10,000 | 142 - 708 perc | < 30 perc |
| Csak a hozzárendelt felhasználók és csoportok szinkronizálása |   10,000 - 100,000 | 1170 - 2340 perc | < 30 perc |
| Az összes felhasználó és csoport szinkronizálása az Azure AD-ben |  1000 < | < 30 perc  | < 30 perc |
| Az összes felhasználó és csoport szinkronizálása az Azure AD-ben |  1,000 - 10,000 | < 30 - 120 perc | < 30 perc |
| Az összes felhasználó és csoport szinkronizálása az Azure AD-ben |  10,000 - 100,000  | 713 - 1425 perc | < 30 perc |
| Az Azure AD összes felhasználójának szinkronizálása|  1000 <  | < 30 perc | < 30 perc |
| Az Azure AD összes felhasználójának szinkronizálása | 1,000 - 10,000  | 43 - 86 perc | < 30 perc |


A konfiguráció **szinkronizálása csak a hozzárendelt felhasználók és csoportok**, használhatja a következő képletek meghatározására a hozzávetőleges minimális és maximális várható kezdeti **ciklusidő:**

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
A kezdeti ciklus befejezéséhez szükséges időt befolyásoló tényezők **összefoglalása:**

- A kiépítés hatókörében lévő felhasználók és csoportok teljes száma.

- A forrásrendszerben (Azure AD) jelen lévő felhasználók, csoportok és csoporttagok teljes száma.

- Azt jelzi, hogy a kiépítés hatókörében lévő felhasználók megfelelnek-e a célalkalmazás meglévő felhasználóinak, vagy első alkalommal kell-e létrehozni őket. A szinkronizálási feladatok, amelyekhez az összes felhasználó először jön létre, körülbelül *kétszer annyi ideig tartanak, mint* a szinkronizálási feladatok, amelyekhez az összes felhasználó a meglévő felhasználókhoz igazodik.

- A [kiépítési naplókban](check-status-user-account-provisioning.md)előforduló hibák száma . A teljesítmény lassabb, ha sok hiba van, és a létesítési szolgáltatás karanténállapotba került. 

- A célrendszer által megvalósított kérelemsebesség-korlátok és szabályozás. Egyes célrendszerek alkalmazási sebesség korlátok és szabályozás, amely hatással lehet a teljesítmény nagy szinkronizálási műveletek során. Ilyen körülmények között egy olyan alkalmazás, amely túl sok kérést fogad, lelassíthatja a válaszadási sebességét, vagy bezárhatja a kapcsolatot. A teljesítmény javítása érdekében az összekötő nek módosítania kell azáltal, hogy nem küldi el gyorsabban az alkalmazáskérelmeket, mint ahogy azt az alkalmazás fel tudja dolgozni. A Microsoft által készített összekötők kiépítése ezt a módosítást. 

- A hozzárendelt csoportok száma és mérete. A hozzárendelt csoportok szinkronizálása hosszabb időt vesz igénybe, mint a felhasználók szinkronizálása. A hozzárendelt csoportok száma és mérete egyaránt hatással van a teljesítményre. Ha egy alkalmazás [lehetővé tette a hozzárendelések csoportobjektum-szinkronizáláshoz,](customize-application-attributes.md#editing-group-attribute-mappings)a felhasználók mellett a csoportnevek és a tagságok is szinkronizálódnak. Ezek a további szinkronizálások hosszabb időt vesznek igénybe, mint a felhasználói objektumok szinkronizálása.

- Ha a teljesítmény problémává válik, és megpróbálja kiépíteni a felhasználók és csoportok többségét a bérlőben, használjon hatókörszűrőket. A hatókörszűrők lehetővé teszik, hogy finomhangolja azokat az adatokat, amelyeket a létesítési szolgáltatás kibont az Azure AD-ből, adott attribútumértékek alapján kiszűrve a felhasználókat. A szűrők hatókörének kezeléséről további információt az [Attribútumalapú alkalmazáskiépítés hatókörszűrőkkel című témakörben talál.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

## <a name="next-steps"></a>További lépések
[A felhasználók kiépítésének és a SaaS-alkalmazásokba való kiépítés automatizálása az Azure Active Directoryval](user-provisioning.md)
