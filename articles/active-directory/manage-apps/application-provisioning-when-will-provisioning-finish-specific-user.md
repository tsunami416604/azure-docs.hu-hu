---
title: Annak megállapítása, hogy egy adott felhasználó tud-e hozzáférni egy alkalmazáshoz | Microsoft Docs
description: Annak megállapítása, hogy egy kritikus fontosságú felhasználó hozzáférhet-e az Azure AD-vel való felhasználói üzembe helyezéshez konfigurált alkalmazáshoz
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
ms.openlocfilehash: 3c6ad7e305958131c4f544dfa2022e7471e9adac
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147110"
---
# <a name="check-the-status-of-user-provisioning"></a>A felhasználó kiépítési állapotának megtekintése

Az Azure AD-kiépítési szolgáltatás kezdeti kiépítési ciklust futtat a forrásrendszer és a célként megadott rendszeren, majd az időszakos növekményes ciklusokat követve. Egy alkalmazás kiépítési beállításakor megtekintheti a kiépítési szolgáltatás aktuális állapotát, és megtekintheti, hogy mikor férhet hozzá egy adott alkalmazáshoz.

## <a name="view-the-provisioning-progress-bar"></a>A kiépítési folyamatjelző sáv megtekintése

 Az alkalmazások **kiépítési** lapján megtekintheti az Azure ad-kiépítési szolgáltatás állapotát. A lap alján található **aktuális állapot** szakasz azt mutatja, hogy a létesítési ciklus megkezdte-e a felhasználói fiókok üzembe helyezését. Megtekintheti a ciklus előrehaladását, és megtekintheti, hogy hány felhasználó és csoport lett kiépítve, és hogy hány szerepkört hozott létre.

Amikor először konfigurálja az automatikus kiosztást, az oldal alján található **aktuális állapot** szakasz a kezdeti kiépítési ciklus állapotát jeleníti meg. Ez a szakasz a növekményes ciklusok futtatásakor frissül. A következő részletek jelennek meg:
- A jelenleg futó vagy utolsó befejezett kiépítési ciklus típusa (kezdeti vagy növekményes).
- Egy **folyamatjelző sáv** , amely a befejezett kiépítési ciklus százalékos arányát mutatja. A százalékos arány a kiosztott lapok számát tükrözi. Vegye figyelembe, hogy az egyes lapok több felhasználót vagy csoportot is tartalmazhatnak, így a százalékos arány nem felel meg közvetlenül a felhasználók, csoportok vagy szerepkörök kiépített számával.
- A **frissítés** gombra kattintva megtarthatja a nézet frissítését.
- A kiépített **felhasználók** és **csoportok** száma, valamint a létrehozott szerepkörök száma. A kezdeti ciklusban a **felhasználók** száma 1, ha a felhasználó létrehozása vagy frissítése megtörtént, és 1 értéket számít fel a felhasználó törlésekor. Növekményes ciklusban a felhasználói frissítések nem érintik a **felhasználók** darabszámát; a szám csak a felhasználók létrehozásakor vagy törlésekor változik.
- A **naplók megtekintése** hivatkozás, amely megnyitja az Azure ad-naplókat a felhasználói kiépítési szolgáltatás által futtatott összes művelet részleteiről, beleértve az egyes felhasználók kiépítési állapotát (lásd az alábbi [naplók használata](#use-audit-logs-to-check-a-users-provisioning-status) szakaszt).

A létesítési ciklus befejezése után a **statisztikák a mai napig** szakasz megjeleníti a dátummal kiépített felhasználók és csoportok összesített számát, valamint az utolsó ciklus befejezési dátumát és időtartamát. A **tevékenység azonosítója** egyedileg azonosítja a legutóbbi kiépítési ciklust. A feladathoz tartozó **azonosító** a kiépítési feladathoz tartozó egyedi azonosító, és a bérlőn lévő alkalmazásra vonatkozik.

A kiépítési folyamat megtekinthető a Azure Portal **Azure Active Directory &gt; vállalati alkalmazások\] &gt; \[ &gt; alkalmazás neve kiépítés** lapján.

![Kiépítés oldal folyamatjelző sáv](media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-audit-logs-to-check-a-users-provisioning-status"></a>Naplók használata a felhasználó kiépítési állapotának ellenőrzéséhez

A kiválasztott felhasználó kiépítési állapotának megtekintéséhez tekintse meg az Azure AD eseménynaplóit. A felhasználói kiépítési szolgáltatás által futtatott összes műveletet az Azure AD-naplók rögzítik. Ebbe beletartozik a forrás-és a megcélzott rendszerek összes írási és olvasási művelete, valamint az egyes műveletek során olvasott vagy írt felhasználói adatok.

A kiépítési naplók a Azure Portal  **&gt; Azure Active Directory Enterprise apps &gt; \[-alkalmazás neve\] &gt; naplózási naplók** lapon érhetők el. A **fiók kiépítési** kategóriájában lévő naplók szűrésével csak az adott alkalmazás kiépítési eseményei láthatók. A felhasználók a "megfelelő azonosító" alapján kereshetik meg azokat, amelyeket a rendszer az attribútum-hozzárendelésekben konfigurált. 

Ha például az "egyszerű felhasználónév" vagy az "e-mail-cím" beállítást a megfelelő attribútumként konfigurálta az Azure ad-oldalon, és a felhasználó nem rendelkezik "audrey@contoso.com" értékkel, akkor keresseaudrey@contoso.commeg a "" naplókat, és tekintse át a bejegyzéseket visszatért.

A kiépítési naplók rögzítik a kiépítési szolgáltatás által végrehajtott összes műveletet, beleértve a következőket:

* Az Azure AD lekérdezése a kiépítés hatókörében lévő hozzárendelt felhasználók számára
* A cél alkalmazás lekérdezése a felhasználók létezéséhez
* A rendszer közötti felhasználói objektumok összehasonlítása
* A felhasználói fiók hozzáadása, frissítése vagy letiltása a megcélzott rendszeren az összehasonlítás alapján

További információ a Azure Portal naplóinak olvasásához: a [kiépítési jelentéskészítési útmutató](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Mennyi időt vesz igénybe a felhasználók kiépítése?
Ha automatikus felhasználó-kiosztást használ egy alkalmazással, az Azure AD automatikusan kiépíti és frissíti a felhasználói fiókokat az alkalmazásokban olyan dolgok alapján, mint a [felhasználó-és csoport-hozzárendelés](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) egy rendszeres ütemezett időintervallumban, jellemzően 40 percenként.

Az adott felhasználó üzembe helyezéséhez szükséges idő főleg attól függ, hogy a kiépítési feladatok kezdeti szinkronizálást vagy növekményes szinkronizálást futtatnak-e.

- A **kezdeti szinkronizálások**esetében a feladat ideje számos tényezőtől függ, beleértve a kiépítés hatókörében lévő felhasználók és csoportok számát, valamint a felhasználók és a csoport teljes számát a forrásoldali rendszeren. Az Azure AD és az alkalmazások közötti első szinkronizálás akár 20 perctől akár több óráig is eltarthat, az Azure AD-címtár méretétől és a kiépítés hatókörében lévő felhasználók számától függően. A kezdeti szinkronizálási teljesítményt befolyásoló tényezők átfogó listáját a szakasz későbbi részében találja.

- Ha **növekményes szinkronizálást** végez a kezdeti szinkronizálás után, a feladatok időtartama általában gyorsabb (például 10 percen belül), mivel a kiépítési szolgáltatás a kezdeti szinkronizálás után mindkét rendszer állapotát jelképező vízjeleket tárolja, így javítja a későbbi szinkronizálások teljesítményét. A feladatok ideje a kiépítési ciklusban észlelt változások számától függ. Ha kevesebb mint 5 000 felhasználó vagy csoporttagság változik, a feladatok egyetlen növekményes kiépítési cikluson belül is befejeződik. 

Az alábbi táblázat összefoglalja a gyakori kiépítési forgatókönyvek szinkronizálási időpontját. Ezekben a forgatókönyvekben a forrásoldali rendszer az Azure AD, és a célként megadott rendszer egy SaaS-alkalmazás. A szinkronizálási idők az SaaS-alkalmazások ServiceNow, a munkahely, a Salesforce és a G Suite szinkronizálási feladatainak statisztikai elemzésével vannak származtatva.


| Hatókör-konfiguráció | A hatókörben lévő felhasználók, csoportok és tagok | Kezdeti szinkronizálás ideje | Növekményes szinkronizálás ideje |
| -------- | -------- | -------- | -------- |
| Csak a hozzárendelt felhasználók és csoportok szinkronizálása |  < 1 000 |  < 30 perc | < 30 perc |
| Csak a hozzárendelt felhasználók és csoportok szinkronizálása |  1000–10 000 | 142 – 708 perc | < 30 perc |
| Csak a hozzárendelt felhasználók és csoportok szinkronizálása |   10 000 – 100 000 | 1 170 – 2 340 perc | < 30 perc |
| Az összes felhasználó és csoport szinkronizálása az Azure AD-ben |  < 1 000 | < 30 perc  | < 30 perc |
| Az összes felhasználó és csoport szinkronizálása az Azure AD-ben |  1000–10 000 | < 30-120 perc | < 30 perc |
| Az összes felhasználó és csoport szinkronizálása az Azure AD-ben |  10 000 – 100 000  | 713 – 1 425 perc | < 30 perc |
| Az Azure AD összes felhasználójának szinkronizálása|  < 1 000  | < 30 perc | < 30 perc |
| Az Azure AD összes felhasználójának szinkronizálása | 1000–10 000  | 43 – 86 perc | < 30 perc |


Csak a konfigurációs **szinkronizáláshoz rendelt felhasználók és csoportok**esetében a következő képletek segítségével határozhatja meg a **kezdeti szinkronizálás** várt minimális és maximális időpontját:

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
A **kezdeti szinkronizálás**befejezéséhez szükséges időt befolyásoló tényezők összefoglalása:

- A kiépítés hatókörében lévő felhasználók és csoportok teljes száma.

- A forrásoldali rendszeren (Azure AD) lévő felhasználók, csoportok és csoporttagok teljes száma.

- Azt határozza meg, hogy a kiépítés hatókörében lévő felhasználók egyeznek-e a célalkalmazás meglévő felhasználóival, vagy az első alkalommal kell létrehozni. Azok a szinkronizálási feladatok, amelyekhez az összes felhasználó először jön létre, akár *kétszer is* eltarthat, amíg a szinkronizálási feladatok, amelyekhez az összes felhasználó hozzá van hasonlítva a meglévő felhasználókhoz.

- A naplókban előforduló [](check-status-user-account-provisioning.md)hibák száma. A teljesítmény lassabb, ha sok hiba van, és a kiépítési szolgáltatás karantén állapotba került.    

- A rendszer által megvalósított kérelmek gyakoriságának korlátai és szabályozása. Egyes megcélzott rendszerek a kérelmek arányának korlátozásait és szabályozását implementálják, ami hatással lehet a teljesítményre a nagy szinkronizálási műveletek során. Ilyen körülmények között egy olyan alkalmazás, amely túl sok kérést kap, lassú lehet a válasz sebessége vagy a kapcsolat lezárása. A teljesítmény javítása érdekében az összekötőt úgy kell módosítani, hogy ne küldje el gyorsabban az alkalmazás kéréseit, mint amennyit az alkalmazás feldolgozhat. A Microsoft által létrehozott összekötők kiépítése ezt a beállítást teszi elérhetővé. 

- A hozzárendelt csoportok száma és mérete. A hozzárendelt csoportok szinkronizálása hosszabb időt vesz igénybe, mint a felhasználók szinkronizálása. A hozzárendelt csoportok száma és mérete is hatással van a teljesítményre. Ha egy alkalmazáshoz [engedélyezve vannak a csoportosítási objektumok szinkronizálása](customize-application-attributes.md#editing-group-attribute-mappings), a felhasználókon kívül a csoportok tulajdonságai, például a csoportok nevei és a tagságok szinkronizálása is megtörténik. Ezek a további szinkronizálások hosszabb időt vesznek igénybe, mint a felhasználói objektumok szinkronizálása.

## <a name="next-steps"></a>További lépések
[Felhasználók átadásának és megszüntetésének automatizálása az SaaS-alkalmazásokban az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)
