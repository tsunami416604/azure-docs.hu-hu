---
title: Az Azure AD-kiépítés működésének megismerése | Microsoft dokumentumok
description: Ismerje meg, hogyan működik az Azure AD kiépítése
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 555fb39836054be05102f4c28167d72016805639
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481500"
---
# <a name="how-provisioning-works"></a>Az üzembe helyezés menete

Az automatikus kiépítés a felhasználói identitások és szerepkörök létrehozását a felhőalapú alkalmazásokban, amelyekhez a felhasználóknak hozzáférésre van szükségük. A felhasználói identitások létrehozása mellett az automatikus kiépítés magában foglalja a felhasználói identitások karbantartását és eltávolítását az állapot vagy a szerepkörök változásakor. A központi telepítés megkezdése előtt tekintse át ezt a cikket, hogy megtudja, hogyan működik az Azure AD-szolgáltatás kiépítése, és konfigurációs javaslatokat kaphat. 

Az **Azure AD-kiépítési szolgáltatás** a felhasználók számára a SaaS-alkalmazások és más rendszerek csatlakoztatásával a rendszer a tartományok közötti identitáskezelés (SCIM) 2.0 felhasználói felügyeleti API-végpont az alkalmazás szállítója által biztosított. Ez az SCIM-végpont lehetővé teszi, hogy az Azure AD programozott módon hozzon létre, frissítsen és távolítson el felhasználókat. A kiválasztott alkalmazások esetében a létesítési szolgáltatás további identitással kapcsolatos objektumokat, például csoportokat és szerepköröket is létrehozhat, frissíthet és eltávolíthat. Az Azure AD és az alkalmazás közötti kiépítéshez használt csatorna HTTPS TLS-titkosítással van titkosítva.


![Az Azure AD-kiépítési szolgáltatás](./media/how-provisioning-works/provisioning0.PNG)
*1.*

![Kimenő felhasználói kiépítési](./media/how-provisioning-works/provisioning1.PNG)
munkafolyamat*2.*

![Bejövő felhasználói kiépítési](./media/how-provisioning-works/provisioning2.PNG)
munkafolyamat*3.*

## <a name="provisioning-using-scim-20"></a>Kiépítés az SCIM 2.0 használatával

Az Azure AD-kiépítési szolgáltatás az [SCIM 2.0 protokollt](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards) használja az automatikus kiépítéshez. A szolgáltatás csatlakozik az alkalmazás SCIM-végpontjához, és scim felhasználói objektumséma és REST API-k segítségével automatizálja a felhasználók és csoportok kiépítését és kiépítésmegszüntetését. Egy SCIM-alapú kiépítési összekötő az Azure AD-gyűjteményben a legtöbb alkalmazás számára biztosított. Amikor alkalmazásokat készít az Azure AD-hez, a fejlesztők az SCIM 2.0 felhasználói felügyeleti API-t használhatják egy SCIM-végpont létrehozásához, amely integrálja az Azure AD-t a kiépítéshez. További információt az [SCIM-végpont létrehozása és a felhasználói kiépítés konfigurálása című témakörben talál.](../app-provisioning/use-scim-to-provision-users-and-groups.md)

Ha egy olyan alkalmazáshoz szeretne automatikus Azure AD-kiépítési összekötőt kérni, amely jelenleg nem rendelkezik ilyentel, töltsön ki egy [Azure Active Directory-alkalmazáskérelmet.](https://aka.ms/aadapprequest)

## <a name="authorization"></a>Engedélyezés

Hitelesítő adatok szükségesek az Azure AD az alkalmazás felhasználói felügyeleti API-hoz való csatlakozáshoz. Az alkalmazás automatikus felhasználói kiépítésének konfigurálása közben érvényes hitelesítő adatokat kell megadnia. Az alkalmazás oktatóanyagára hivatkozva megtalálhatja az alkalmazás hitelesítő adatok típusait és követelményeit. Az Azure Portalon tesztelheti a hitelesítő adatokat azáltal, hogy az Azure AD megkísérli az alkalmazás létesítési alkalmazáshoz való csatlakozást a megadott hitelesítő adatok használatával.

Ha saml-alapú egyszeri bejelentkezés is konfigurálva van az alkalmazáshoz, az Azure AD belső, alkalmazásonkénti tárolási korlátja 1024 bájt. Ez a korlát tartalmazza az összes tanúsítványt, titkos jogkivonatokat, hitelesítő adatokat és az alkalmazás egyetlen példányához társított összes konfigurációs adatot (más néven egyszerű szolgáltatásrekordot az Azure AD-ben). Saml-alapú egyszeri bejelentkezés konfigurálásakor az SAML-jogkivonatok aláírásához használt tanúsítvány gyakran a terület több mint 50%-át használja fel. A felhasználó létesítése során megadott további elemek (titkos tokenek, URI-k, értesítési e-mail-címek, felhasználónevek és jelszavak) túlléphetik a tárolási korlátot. További információt a [Rendszergazdahitelesítő adatok mentése a felhasználói kiépítés konfigurálása során](../manage-apps/application-provisioning-config-problem-storage-limit.md)című témakörben talál.

## <a name="mapping-attributes"></a>Hozzárendelési attribútumok

Ha engedélyezi a felhasználó kiépítése egy külső SaaS-alkalmazás, az Azure Portal szabályozza az attribútum értékeit attribútumleképezések en keresztül. A leképezések határozzák meg az Azure AD és a célalkalmazás között a felhasználói fiókok kiépítésekor vagy frissítésekor átfolyó felhasználói attribútumokat.

Az Azure AD felhasználói objektumai és az egyes SaaS-alkalmazások felhasználói objektumai között egy előre konfigurált attribútum- és attribútumleképezési készlet található. Egyes alkalmazások más típusú objektumokat is kezelnek a Felhasználókkal együtt, például a Csoportokkal együtt.

A kiépítés beállításakor fontos, hogy tekintse át és konfigurálja az attribútum-leképezések és munkafolyamatok, amelyek meghatározzák, hogy mely felhasználói (vagy csoport) tulajdonságok áramlását az Azure AD az alkalmazásba. Tekintse át és konfigurálja a megfelelő tulajdonságot (**Objektumok egyeztetése ezzel az attribútummal**), amely a két rendszer közötti felhasználók/csoportok egyedi azonosítására és egyezésére szolgál.

Az alapértelmezett attribútum-hozzárendeléseket az üzleti igényeknek megfelelően szabhatja testre. Így módosíthatja vagy törölheti a meglévő attribútum-leképezéseket, vagy új attribútum-leképezéseket hozhat létre. További információt a [Felhasználói kiépítési attribútumleképezések testreszabása SaaS-alkalmazásokhoz.](../manage-apps/customize-application-attributes.md)

SaaS-alkalmazásba való kiépítés konfigurálásakor az attribútumleképezések egyik megadott típusa egy kifejezésleképezés. Ezekhez a leképezésekhez meg kell írnia egy parancsfájl-szerű kifejezést, amely lehetővé teszi a felhasználók adatainak a SaaS-alkalmazás számára elfogadhatóbb formátumgá alakítását. További információt az [Attribútumleképezések kifejezések írása című témakörben talál.](functions-for-customizing-application-data.md)

## <a name="scoping"></a>Hatókör 
### <a name="assignment-based-scoping"></a>Hozzárendelés-alapú hatókör

Az Azure AD-ből egy SaaS-alkalmazásba történő kimenő kiépítése esetén a [leggyakoribb](../manage-apps/assign-user-or-group-access-portal.md) módja annak, hogy meghatározza, mely felhasználók vannak kiépítés hatókörében. Mivel a felhasználói hozzárendelések is használják egyszeri bejelentkezés engedélyezéséhez, ugyanazt a módszert lehet használni a hozzáférés és a kiépítés kezelésére. A hozzárendelés-alapú hatókör nem vonatkozik a bejövő kiépítési forgatókönyvekre, például a Workday és a Successfactors.

* **Csoportok.** Az Azure AD Premium-licenccsomag használatával csoportok segítségével hozzáférést rendelhet egy SaaS-alkalmazáshoz. Ezt követően, ha a létesítési hatókör van beállítva, hogy **csak a hozzárendelt felhasználók és csoportok szinkronizálása,** az Azure AD-létesítési szolgáltatás kiépítése vagy de-provision felhasználók alapján, hogy ők az alkalmazáshoz rendelt csoport tagjai. Maga a csoportobjektum nincs kiépítve, kivéve, ha az alkalmazás támogatja a csoportobjektumokat.

* **Dinamikus csoportok.** Az Azure AD felhasználói létesítési szolgáltatás képes olvasni és kiépíteni a felhasználók [dinamikus csoportokban.](../users-groups-roles/groups-create-rule.md) Tartsa szem előtt ezeket a kifogásokat és ajánlásokat:

  * A dinamikus csoportok hatással lehetnek az Azure AD-ről saas-alkalmazásokra történő végpontok között történő kiépítés teljesítményére.

  * Adinamikus csoport ban lévő felhasználó kiépítésének vagy kiépítésének gyorsavá vált egy SaaS-alkalmazásban, attól függ, hogy a dinamikus csoport milyen gyorsan tudja kiértékelni a tagsági változásokat. A dinamikus csoportok feldolgozási állapotának ellenőrzéséről a [Tagsági szabály feldolgozási állapotának ellenőrzése](../users-groups-roles/groups-create-rule.md)című témakörben talál további információt.

  * Ha egy felhasználó elveszíti a dinamikus csoport tagságát, akkor kiépítési eseménynek minősül. Fontolja meg ezt a forgatókönyvet, amikor szabályokat hoz létre a dinamikus csoportokhoz.

* **Beágyazott csoportok.** Az Azure AD felhasználói létesítési szolgáltatás nem tudja olvasni vagy kiépíteni a beágyazott csoportokban lévő felhasználókat. A szolgáltatás csak olvasni és kiépíteni a felhasználók, amelyek közvetlen tagjai egy explicit módon hozzárendelt csoport. A "csoportalapú hozzárendelések alkalmazásokhoz" korlátozása az egyszeri bejelentkezést is érinti [(lásd: Csoport használata a SaaS-alkalmazásokhoz való hozzáférés kezeléséhez).](../users-groups-roles/groups-saasapps.md) Ehelyett közvetlenül rendeljehozzá, vagy más módon hatókört a [csoportok,](define-conditional-rules-for-provisioning-user-accounts.md) amelyek tartalmazzák a felhasználók, akikki ki kell építeni.

### <a name="attribute-based-scoping"></a>Attribútumalapú hatókör 

A hatókörszűrők segítségével attribútumalapú szabályokat határozhat meg, amelyek meghatározzák, hogy mely felhasználók vannak kiépítve egy alkalmazásba. Ez a módszer gyakran használják a bejövő kiépítése a HCM-alkalmazások az Azure AD és az Active Directory. Hatókör-szűrők az egyes Azure AD-felhasználók létesítési összekötőattribútum-leképezések részeként konfigurálva vannak konfigurálva. Az attribútumalapú hatókörszűrők konfigurálásáról az [Attribútumalapú alkalmazáskiépítés hatókörszűrőkkel című témakörben](define-conditional-rules-for-provisioning-user-accounts.md)talál további információt.

### <a name="b2b-guest-users"></a>B2B (vendég) felhasználók

Az Azure AD-felhasználói kiépítési szolgáltatás használatával b2B (vagy vendég) felhasználók kiépítése az Azure AD SaaS-alkalmazások. Azonban a B2B-felhasználók számára, hogy jelentkezzen be a SaaS-alkalmazás az Azure AD használatával, a SaaS-alkalmazás nak rendelkeznie kell az SAML-alapú egyszeri bejelentkezési képesség konfigurálva egy adott módon. ARról, hogy miként konfigurálhatja a SaaS-alkalmazásokat a B2B-felhasználók bejelentkezésének támogatására, olvassa [el az SaaS-alkalmazások konfigurálása a B2B együttműködéshez](../b2b/configure-saas-apps.md)című témakört.

Ne feledje, hogy a vendégfelhasználó userPrincipalName gyakran "alias#EXT#@domain.com" néven van tárolva. ha a userPrincipalName forrásattribútumként szerepel az attribútumleképezésekben, a #EXT# a rendszer eltávolítja a userPrincipalName-ből. Ha azt szeretné, hogy a #EXT# jelen legyen, cserélje le a userPrincipalName-t az originalUserPrincipalName attribútumra. 

## <a name="provisioning-cycles-initial-and-incremental"></a>Kiépítési ciklusok: Kezdeti és növekményes

Ha az Azure AD a forrásrendszer, a létesítési szolgáltatás a [Különbözeti lekérdezés használata a Microsoft Graph-adatok változásainak nyomon követésére](https://docs.microsoft.com/graph/delta-query-overview) a felhasználók és csoportok figyelésére. A kiépítési szolgáltatás egy kezdeti ciklust futtat a forrásrendszeren és a célrendszeren, amelyet időszakos növekményes ciklusok követnek.

### <a name="initial-cycle"></a>Kezdeti ciklus

A létesítési szolgáltatás indításakor az első ciklus:

1. Az összes felhasználó és csoport lekérdezése a forrásrendszerből, az [attribútum-hozzárendelésekben](customize-application-attributes.md)definiált összes attribútum beolvasása.

2. Szűrje a visszaadott felhasználókat és [csoportokat](../manage-apps/assign-user-or-group-access-portal.md) bármely konfigurált hozzárendelés vagy [attribútumalapú hatókörszűrő](define-conditional-rules-for-provisioning-user-accounts.md)használatával.

3. Amikor egy felhasználó hozzá van rendelve, vagy a kiépítés hatókörében van, a szolgáltatás lekérdezi a célrendszert egy megfelelő felhasználó számára a megadott [egyező attribútumok](customize-application-attributes.md#understanding-attribute-mapping-properties)használatával. Példa: Ha a userPrincipal név a forrásrendszerben az egyező attribútum, és leképezi a userName a célrendszerben, majd a létesítési szolgáltatás lekérdezi a célrendszer userNames, amelyek megfelelnek a userPrincipal név értékeit a forrásrendszerben.

4. Ha egy megfelelő felhasználó nem található a célrendszerben, akkor a forrásrendszerből visszaadott attribútumok használatával jön létre. A felhasználói fiók létrehozása után a létesítési szolgáltatás észleli és gyorsítótárazza a célrendszer azonosítóját az új felhasználó számára. Ez az azonosító az adott felhasználó összes jövőbeli műveletének futtatására szolgál.

5. Ha egy megfelelő felhasználó tanak, akkor a forrásrendszer által biztosított attribútumok használatával frissül. A felhasználói fiók egyeztetése után a létesítési szolgáltatás észleli és gyorsítótárazza a célrendszer azonosítóját az új felhasználó számára. Ez az azonosító az adott felhasználó összes jövőbeli műveletének futtatására szolgál.

6. Ha az attribútumleképezések "referencia" attribútumokat tartalmaznak, a szolgáltatás további frissítéseket végez a célrendszeren a hivatkozott objektumok létrehozásához és csatolásához. Előfordulhat például, hogy egy felhasználó rendelkezik egy "Manager" attribútummal a célrendszerben, amely a célrendszerben létrehozott másik felhasználóhoz kapcsolódik.

7. A kezdeti ciklus végén megmarad egy vízjel, amely a későbbi növekményes ciklusok kiindulópontját biztosítja.

Egyes alkalmazások, például a ServiceNow, a G Suite és a Box nem csak a felhasználók kiépítését, hanem a csoportok és tagjaik kiépítését is támogatják. Ezekben az esetekben, ha a csoport kiépítése engedélyezve van a [leképezések,](customize-application-attributes.md)a létesítési szolgáltatás szinkronizálja a felhasználók és a csoportok, majd később szinkronizálja a csoporttagságok.

### <a name="incremental-cycles"></a>Növekményes ciklusok

A kezdeti ciklus után az összes többi ciklus:

1. A forrásrendszer lekérdezése minden olyan felhasználó és csoport számára, amely az utolsó vízjel tárolása óta frissült.

2. Szűrje a visszaadott felhasználókat és [csoportokat](../manage-apps/assign-user-or-group-access-portal.md) bármely konfigurált hozzárendelés vagy [attribútumalapú hatókörszűrő](define-conditional-rules-for-provisioning-user-accounts.md)használatával.

3. Amikor egy felhasználó hozzá van rendelve, vagy a kiépítés hatókörében van, a szolgáltatás lekérdezi a célrendszert egy megfelelő felhasználó számára a megadott [egyező attribútumok](customize-application-attributes.md#understanding-attribute-mapping-properties)használatával.

4. Ha egy megfelelő felhasználó nem található a célrendszerben, akkor a forrásrendszerből visszaadott attribútumok használatával jön létre. A felhasználói fiók létrehozása után a létesítési szolgáltatás észleli és gyorsítótárazza a célrendszer azonosítóját az új felhasználó számára. Ez az azonosító az adott felhasználó összes jövőbeli műveletének futtatására szolgál.

5. Ha egy megfelelő felhasználó tanak, akkor a forrásrendszer által biztosított attribútumok használatával frissül. Ha egy újonnan hozzárendelt fiók, amely egyeztetett, a létesítési szolgáltatás észleli és gyorsítótárazza a célrendszer azonosítóját az új felhasználó számára. Ez az azonosító az adott felhasználó összes jövőbeli műveletének futtatására szolgál.

6. Ha az attribútumleképezések "referencia" attribútumokat tartalmaznak, a szolgáltatás további frissítéseket végez a célrendszeren a hivatkozott objektumok létrehozásához és csatolásához. Előfordulhat például, hogy egy felhasználó rendelkezik egy "Manager" attribútummal a célrendszerben, amely a célrendszerben létrehozott másik felhasználóhoz kapcsolódik.

7. Ha egy felhasználó, amely korábban a kiépítési hatókör eltávolítása hatókör, beleértve a hozzárendelés nélküli, a szolgáltatás letiltja a felhasználót a célrendszerben egy frissítés segítségével.

8. Ha egy felhasználó, aki korábban a kiépítési hatókörle van tiltva, vagy ideiglenesen törölt a forrásrendszerben, a szolgáltatás letiltja a felhasználót a célrendszerben egy frissítés en keresztül.

9. Ha egy felhasználó, amely korábban a kiépítési hatókörben van, a forrásrendszerben törölt, a szolgáltatás törli a felhasználót a célrendszerben. Az Azure AD-ben a felhasználók a helyreállíthatóan törölt 30 nap után ők helyreállíthatóan törölt.

10. A növekményes ciklus végén egy új vízjel megőrzése, amely a későbbi növekményes ciklusok kiindulópontját biztosítja.

> [!NOTE]
> A **Létrehozás**, **frissítés**vagy **törlés** műveleteket a Hozzárendelések szakasz **Célobjektum-műveletek** jelölőnégyzetével [tilthatja](customize-application-attributes.md) le. A rendszer egy olyan mezőből, mint például a "accountEnabled" mezőből származó attribútumleképezés segítségével is szabályozza a felhasználó letiltásának logikáját.

A létesítési szolgáltatás továbbra is fut back-to-back növekményes ciklusok a végtelenségig, az [oktatóanyag-specifikus az egyes alkalmazások.](../saas-apps/tutorial-list.md) A növekményes ciklusok addig folytatódnak, amíg az alábbi események valamelyike be nem következik:

- A szolgáltatás manuálisan leáll az Azure Portalhasználatával, vagy a megfelelő Microsoft Graph API-parancs használatával.
- Egy új kezdeti ciklus aktiválódik a **Clear állapot és újraindítás** beállítás az Azure Portalon, vagy a megfelelő Microsoft Graph API-parancs használatával. Ez a művelet törli a tárolt vízjelet, és az összes forrásobjektum újra kiértékelését eredményezi.
- Az új kezdeti ciklus az attribútumleképezések vagy a hatókörszűrők változása miatt aktiválódik. Ez a művelet törli a tárolt vízjelet is, és az összes forrásobjektum újra kiértékelését eredményezi.
- A létesítési folyamat a magas hibaarány miatt karanténba kerül (lásd alább), és négy hétnél tovább karanténban marad. Ebben az esetben a szolgáltatás automatikusan le lesz tiltva.

### <a name="errors-and-retries"></a>Hibák és újrapróbálkozások

Ha a célrendszer hibája megakadályozza, hogy egy adott felhasználót hozzáadjanak, frissítsenek vagy törölnek a célrendszerben, a művelet a következő szinkronizálási ciklusban újra próbálkozik. Ha a felhasználó továbbra is sikertelen, majd az újrapróbálkozások csökkenni kezd, és fokozatosan visszaskálázás napi egy kísérletre. A hiba megoldásához a rendszergazdáknak ellenőrizniük kell a [létesítési naplókat](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) a kiváltó ok meghatározásához, és meg kell tenniük a megfelelő lépéseket. Gyakori hibák lehetnek:

- Azok a felhasználók, akiknek nincs olyan attribútumuk a forrásrendszerben, amely a célrendszerben szükséges
- Azok a felhasználók, akik attribútumértékkel rendelkeznek a forrásrendszerben, amelyhez egyedi megkötés van érvényben a célrendszerben, és ugyanaz az érték szerepel egy másik felhasználói bejegyzésben

Ezeket a hibákat úgy oldhatja meg, hogy módosítja az érintett felhasználó attribútumértékeit a forrásrendszerben, vagy módosítja az attribútumleképezéseket, hogy azok ne okozzanak ütközést.

### <a name="quarantine"></a>Karantén

Ha a célrendszeren keresztül kezdeményezett hívások többsége vagy mindegyike egy hiba (például érvénytelen rendszergazdai hitelesítő adatok) miatt következetesen sikertelen, a létesítési feladat "karantén" állapotba kerül. Ez az állapot a [kiépítési összefoglaló jelentésben](../manage-apps/check-status-user-account-provisioning.md) és e-mailben jelenik meg, ha az Azure Portalon e-mail-értesítések vannak konfigurálva.

Karanténban a növekményes ciklusok gyakorisága fokozatosan csökken naponta egyszer.

A kiépítési feladat kilép a karanténból, miután az összes jogsértő hibát kijavították, és a következő szinkronizálási ciklus elindul. Ha a létesítési feladat négy hétnél hosszabb ideig karanténban marad, a létesítési feladat le van tiltva. A karantén állapotáról [itt](../manage-apps/application-provisioning-quarantine-status.md)olvashat bővebben.

### <a name="how-long-provisioning-takes"></a>Mennyi időt vesz igénybe az átadás?

A teljesítmény attól függ, hogy a létesítési feladat fut egy kezdeti kiépítési ciklus vagy egy növekményes ciklus. A kiépítés hosszú ságáról és a kiépítési szolgáltatás állapotának figyeléséről [a Felhasználói kiépítés állapotának ellenőrzése című](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)témakörben talál részletesen.

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>Hogyan állapítható meg, hogy a felhasználók megfelelően vannak-e kiépítve?

A felhasználói létesítési szolgáltatás által futtatott összes művelet et az Azure [AD-kiépítési naplók (előzetes verzió)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)rögzítik. A naplók tartalmazzák a forrás- és célrendszereken végzett összes olvasási és írási műveletet, valamint az egyes műveletek során olvasott vagy írt felhasználói adatokat. A kiépítési naplók azure portalon való olvasásáról a [kiépítési jelentéskészítési útmutatóban](../manage-apps/check-status-user-account-provisioning.md)olvashat.

## <a name="de-provisioning"></a>Kiépítés ből való kiépítés

Az Azure AD-kiépítési szolgáltatás a forrás- és célrendszereket szinkronban tartja a fiókok kiszervezésének megszüntetésével, amikor a felhasználóknak már nem kell hozzáférniük. 

Az Azure AD-kiépítési szolgáltatás ideiglenesen törli a felhasználót egy alkalmazásból, amikor az alkalmazás iszlamista törlésre kerül (frissítési kérelem aktív = hamis) és a következő események bármelyike történik:

* A felhasználói fiók törlődik az Azure AD-ben
*   A felhasználó nincs hozzárendelve az alkalmazásból
*   A felhasználó már nem felel meg a hatókörszűrőnek, és nem terjed ki a hatókörből
    * Alapértelmezés szerint az Azure AD-kiépítési szolgáltatás ideiglenesen törli vagy letiltja a hatókörön kívül eső felhasználókat. Ha felül szeretné bírni ezt az alapértelmezett viselkedést, beállíthatja, hogy a jelző [kihagyja a hatókörön kívüli törléseket.](../app-provisioning/skip-out-of-scope-deletions.md)
*   Az AccountEnabled tulajdonság értéke Hamis.

Ha a fenti négy esemény egyike következik be, és a célalkalmazás nem támogatja a helyreállítható törlést, a kiépítési szolgáltatás DELETE-kérelmet küld a felhasználó végleges törlésére az alkalmazásból. 

30 nappal azután, hogy egy felhasználó törlődik az Azure AD-ben, véglegesen törlődnek a bérlőből. Ezen a ponton a létesítési szolgáltatás küld egy DELETE kérelmet véglegesen törli a felhasználót az alkalmazásban. A 30 napos időszak alatt bármikor [manuálisan törölheti a felhasználót véglegesen,](../fundamentals/active-directory-users-restore.md)amely törlési kérelmet küld az alkalmazásnak.

Ha az attribútumleképezésekben IsSoftDeleted attribútum jelenik meg, a rendszer meghatározza a felhasználó állapotát, valamint azt, hogy küldjön-e egy frissítési kérelmet aktív = false értékkel a felhasználó helyrehozása érdekében. 

## <a name="next-steps"></a>Következő lépések

[Automatikus felhasználóátadást használó üzembe helyezés tervezése](../app-provisioning/plan-auto-user-provisioning.md)

[Üzembe helyezés konfigurálása katalógusbeli alkalmazás esetében](../manage-apps/configure-automatic-user-provisioning-portal.md)

[SciM-végpont létrehozása és kiépítés konfigurálása saját alkalmazás létrehozásakor](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[A felhasználók alkalmazásba való konfigurálásával és kiépítésével kapcsolatos problémák elhárítása.](../manage-apps/application-provisioning-config-problem.md)
