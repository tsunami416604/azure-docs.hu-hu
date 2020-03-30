---
title: Csoportlicenc-hozzárendelési problémák megoldása - Azure Active Directory | Microsoft dokumentumok
description: A licenckiosztással kapcsolatos problémák azonosítása és megoldása az Azure Active Directory csoportalapú licencelése során
services: active-directory
keywords: Az Azure AD licencelése
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ddfc4bf7ed3bdf214a44a5dfe03259d32b2f3f94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025696"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Egy csoport licenc-hozzárendelési problémáinak azonosítása és megoldása az Azure Active Directoryban

Csoportalapú licencelés az Azure Active Directoryban (Azure AD) bemutatja a felhasználók licencelési hiba állapotban a fogalmat. Ebben a cikkben ismertetjük, hogy a felhasználók miért kerülhetnek ebbe az állapotba.

Ha a licenceket közvetlenül az egyes felhasználókhoz rendeli hozzá, csoportalapú licencelés nélkül, a hozzárendelési művelet sikertelen lehet. Ha például egy felhasználói rendszeren hajtja végre a PowerShell-parancsmast, `Set-MsolUserLicense` a parancsmag számos, az üzleti logikához kapcsolódó okból sikertelen lehet. Előfordulhat például, hogy nem áll rendelkezésre elegendő számú licenc, vagy ütközik két olyan szolgáltatási csomag között, amelyek nem rendelhetők hozzá egyszerre. A problémát azonnal jelentik önnek.

Ha csoportalapú licencelést használ, ugyanazok a hibák fordulhatnak elő, de azok a háttérben történnek, miközben az Azure AD szolgáltatás licenceket rendel hozzá. Ezért a hibákat nem lehet azonnal közölni Önvel. Ehelyett a rendszer rögzíti őket a felhasználói objektumon, majd a felügyeleti portálon keresztül jelenti őket. A felhasználó licencelésének eredeti szándéka soha nem vész el, de a későbbi vizsgálat és megoldás hibaállapotban van rögzítve.

## <a name="find-license-assignment-errors"></a>Licenchozzárendelési hibák keresése

### <a name="to-find-users-in-an-error-state-in-a-group"></a>Felhasználók megkeresése egy csoportban hibaállapotban

1. Nyissa meg a csoportot az áttekintő lapon, és válassza a **Licencek**lehetőséget. Értesítés jelenik meg, ha vannak olyan felhasználók, akik hibaállapotban vannak.

   ![Csoport- és hibaértesítések üzenete](./media/licensing-groups-resolve-problems/group-error-notification.png)

1. Válassza ki az értesítést az összes érintett felhasználó listájának megnyitásához. Az egyes felhasználókat egyenként is kijelölheti, hogy további részleteket lásson.

   ![A csoportlicencelési hibaállapotban lévő felhasználók listája](./media/licensing-groups-resolve-problems/list-of-users-with-errors.png)

1. Ha meg szeretné találni az összes olyan csoportot, amely legalább egy hibát tartalmaz, az **Azure Active Directory** panelen válassza a **Licencek**lehetőséget, majd válassza **az Áttekintés lehetőséget.** Egy információs mező jelenik meg, ha a csoportok figyelmet igényelnek.

   ![Áttekintés és információk a hibás állapotban lévő csoportokról](./media/licensing-groups-resolve-problems/group-errors-widget.png)

1. Jelölje be a jelölőnégyzetet, ha meg szeretné tekinteni a hibás csoportok listáját. További részletekért kiválaszthatja az egyes csoportokat.

   ![A hibás csoportok áttekintése és listája](./media/licensing-groups-resolve-problems/list-of-groups-with-errors.png)

A következő szakaszok ismertetik az egyes lehetséges problémákat és a probléma megoldásának módját.

## <a name="not-enough-licenses"></a>Nincs elég licenc

**Probléma:** Nincs elég elérhető licenc a csoportban megadott termékek egyikéhez. Vagy több licencet kell vásárolnia a termékhez, vagy fel kell szabadítania a fel nem használt licenceket más felhasználóktól vagy csoportoktól.

Ha meg szeretné tudni, hogy hány licenc érhető el, nyissa meg az **Azure Active Directory licencek** > **Licenses** > **minden termék**.

Ha meg szeretné tekinteni, hogy mely felhasználók és csoportok használnak licenceket, válasszon ki egy terméket. A **Licencelt felhasználók csoportban**megjelenik azoknak a felhasználóknak a listája, akikközvetlenül vagy egy vagy több csoporton keresztül rendelkeztek licencekkel. A **Licencelt csoportok csoportban**láthatja az összes olyan csoportot, amelyhez a termékek hozzá vannak rendelve.

**PowerShell:** A PowerShell-parancsmagok _countviolation_néven jelentik ezt a hibát.

## <a name="conflicting-service-plans"></a>Ütköző szolgáltatási tervek

**Probléma:** A csoportban megadott termékek egyike olyan szolgáltatási tervet tartalmaz, amely ütközik egy másik szolgáltatási tervvel, amely már hozzá van rendelve a felhasználóhoz egy másik terméken keresztül. Egyes szolgáltatási csomagok úgy vannak konfigurálva, hogy nem rendelhetők hozzá ugyanahhoz a felhasználóhoz, mint egy másik kapcsolódó szolgáltatási csomag.

Lásd az alábbi példát. A felhasználó rendelkezik közvetlenül az Office 365 Nagyvállalati *E1* csomaglicenccel, és az összes csomag engedélyezve van. A felhasználó hozzá lett adva egy olyan csoporthoz, amelyhez hozzá van rendelve az Office 365 Nagyvállalati *E3* termék. Az E3 termék olyan szolgáltatási csomagokat tartalmaz, amelyek nem fedhetik át az E1-ben szereplő terveket, így a csoportlicenc-hozzárendelés sikertelen lesz az "Ütköző szolgáltatási tervek" hibával. Ebben a példában az ütköző szolgáltatási tervek a következők:

- A SharePoint Online (2. csomag) ütközik a SharePoint Online-nal (1. terv).
- Az Exchange Online (2. csomag) ütközik az Exchange Online-nal (1. terv).

Az ütközés megoldásához le kell tiltania két tervet. Letilthatja a közvetlenül a felhasználóhoz rendelt E1 licencet. Vagy módosítania kell a teljes csoportlicenc-hozzárendelést, és le kell tiltania a csomagokat az E3 licencben. Azt is megteheti, hogy eltávolítja az E1 licencet a felhasználótól, ha az az E3 licenc keretében redundáns.

Az ütköző terméklicencek feloldására vonatkozó döntés mindig a rendszergazdáé. Az Azure AD nem oldja fel automatikusan a licencütközéseket.

**PowerShell:** A PowerShell-parancsmagok a hibát _MutuallyExclusiveViolation néven jelentik._

## <a name="other-products-depend-on-this-license"></a>Más termékek függ nek ez a licenc

**Probléma:** A csoportban megadott termékek egyike tartalmaz egy szolgáltatási csomagot, amelyet engedélyezni kell egy másik szolgáltatási tervhez, egy másik termékben a működéshez. Ez a hiba akkor fordul elő, amikor az Azure AD megpróbálja eltávolítani az alapul szolgáló szolgáltatási csomag. Ez például akkor fordulhat elő, ha eltávolítja a felhasználót a csoportból.

A probléma megoldásához meg kell győződnie arról, hogy a szükséges csomag továbbra is hozzá van rendelve a felhasználókhoz valamilyen más módszerrel, vagy hogy a függő szolgáltatások le vannak tiltva a felhasználók számára. Ezt követően megfelelően eltávolíthatja a csoportlicencet ezektől a felhasználóktól.

**PowerShell:** A PowerShell-parancsmagok _függőségimegsértésként_jelentik ezt a hibát.

## <a name="usage-location-isnt-allowed"></a>A felhasználási hely nem engedélyezett

**Probléma:** Egyes Microsoft-szolgáltatások a helyi törvények és előírások miatt nem érhetők el minden helyen. Mielőtt licencet rendelhetne egy felhasználóhoz, meg kell **adnia** a felhasználó Használati hely tulajdonságát. Megadhatja a helyet az Azure Portal **Felhasználói** > **profil** > **beállításai** szakaszában.

Amikor az Azure AD megkísérli hozzárendelni a csoportlicencet egy olyan felhasználóhoz, akinek a használati helye nem támogatott, sikertelen, és hibát rögzít a felhasználón.

A probléma megoldásához távolítsa el a felhasználókat a nem támogatott helyekről a licencelt csoportból. Másik lehetőségként, ha az aktuális használati hely értékek nem a tényleges felhasználói helyet jelölik, módosíthatja őket úgy, hogy a licencek megfelelően legyenek hozzárendelve a következő alkalommal (ha az új hely támogatott).

**PowerShell:** A PowerShell-parancsmagok a hibát _ProhibitedInUsageLocationViolation néven jelentik._

> [!NOTE]
> Amikor az Azure AD csoportlicenceket rendel hozzá, a megadott használati hely nélküli felhasználók öröklik a címtár helyét. Azt javasoljuk, hogy a rendszergazdák a megfelelő használati helyértékeket a felhasználók használata előtt a csoport-alapú licencelés, hogy megfeleljenek a helyi törvények és rendeletek.

## <a name="duplicate-proxy-addresses"></a>Ismétlődő proxycímek

Ha Exchange Online-t használ, előfordulhat, hogy a bérlő egyes felhasználói helytelenül vannak konfigurálva ugyanazzal a proxycímértékkel. Amikor a csoportalapú licenccel megpróbál licencet rendelni egy ilyen felhasználóhoz, az sikertelen lesz, és azt mutatja, hogy "A proxycím már használatban van".

> [!TIP]
> Annak megtekintéséhez, hogy van-e ismétlődő proxycím, hajtsa végre a következő PowerShell-parancsmacãdát az Exchange Online-on keresztül:
> ```
> Get-Recipient -ResultSize unlimited | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
> ```
> A problémáról további információt az [Exchange Online "Proxycím már használatban van" című hibaüzenetében talál.](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online) A cikk arról is tartalmaz [információt, hogy miként csatlakozhat az Exchange Online-hoz a távoli PowerShell használatával.](https://technet.microsoft.com/library/jj984289.aspx)

Miután megoldotta az érintett felhasználók proxycím-problémáit, győződjön meg arról, hogy a licencfeldolgozáskényszerítése a csoporton, hogy a licencek most már alkalmazhatók legyenek.

## <a name="azure-ad-mail-and-proxyaddresses-attribute-change"></a>Az Azure AD Mail és a ProxyAddresses attribútum módosítása

**Probléma:** Licenc-hozzárendelés frissítése közben egy felhasználó vagy egy csoport, előfordulhat, hogy az Azure AD Mail és ProxyAddresses attribútum egyes felhasználók módosulnak.

A felhasználó licenchozzárendelésének frissítése a proxycím-számítás elindítását eredményezi, ami megváltoztathatja a felhasználói attribútumokat. A módosítás pontos okának megértéséhez és a probléma megoldásához tekintse meg ezt a cikket [arról, hogy a proxyAddresses attribútum hogyan van feltöltve az Azure AD-ben.](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad)

## <a name="licenseassignmentattributeconcurrencyexception-in-audit-logs"></a>LicenseAssignmentAttributeConcurrencyKivétel a naplókban

**Probléma:** A felhasználó rendelkezik LicenseAssignmentAttributeConcurrencyException licenchozzárendeléshez a naplónaplókban.
Amikor a csoportalapú licencelés ugyanazon licenc egyidejű licenchozzárendelését próbálja feldolgozni egy felhasználónak, ez a kivétel a felhasználón rögzítésre kerül. Ez általában akkor fordul elő, ha a felhasználó egynél több, azonos licenccel rendelkező csoport tagja. Az AZure AD újra megpróbálja feldolgozni a felhasználói licencet, és megoldja a problémát. A probléma megoldásához az ügyféltől nincs szükség műveletre.

## <a name="more-than-one-product-license-assigned-to-a-group"></a>Egycsoporthoz rendelt több terméklicenc

Egy csoporthoz több terméklicencet is hozzárendelhet. Például hozzárendelheti az Office 365 Nagyvállalati E3 és Enterprise Mobility + Security szolgáltatást egy csoporthoz, így egyszerűen engedélyezheti az összes szolgáltatást a felhasználók számára.

Az Azure AD megkísérli a csoportban megadott összes licenc hozzárendelését az egyes felhasználókhoz. Ha az Azure AD üzleti logikai problémák miatt nem tudja hozzárendelni a termékek egyikét, akkor a csoport többi licencét sem rendeli hozzá. Például, ha nincs elég licenc az összes, vagy ha ütköznek más szolgáltatások, amelyek engedélyezve vannak a felhasználó.

Láthatja azon felhasználókat, akik nem kapták meg a hozzárendelést, és ellenőrizheti, hogy mely termékeket érinti ez a probléma.

## <a name="when-a-licensed-group-is-deleted"></a>Licencelt csoport törlésekor

A csoport törlése előtt el kell távolítania a csoporthoz rendelt összes licencet. A licencek eltávolítása azonban a csoport összes felhasználójától időbe telhet. A licenc-hozzárendelések csoportból való eltávolítása során hibák léphetnek fel, ha a felhasználó hoz egy függő licencet, vagy ha proxycím-ütközési probléma merül fel, amely megtiltja a licenc eltávolítását. Ha a felhasználó olyan licenccel rendelkezik, amely a csoport törlése miatt eltávolítandó licenctől függ, a rendszer örököltről közvetlenre konvertálja a licenchozzárendelést.

Vegyünk például egy olyan csoportot, amelyhez engedélyezve van az Office 365 E3/E5, és engedélyezve van a Skype Vállalati verzió szolgáltatási csomagja. Képzelje el azt is, hogy a csoport néhány tagja közvetlenül hozzárendelt audiokonferencia-licenccel rendelkezik. A csoport törlésekor a csoportalapú licencelés megpróbálja eltávolítani az Office 365 E3/E5-öt az összes felhasználóból. Mivel az audiokonferencia a Skype Vállalati verziótól függ, a csoportalapú licencelés az Office 365 E3/E5 licenceket közvetlen licenc-hozzárendeléssé alakítja át minden olyan felhasználó számára, akihez hozzá van rendelve az audiokonferencia.

## <a name="manage-licenses-for-products-with-prerequisites"></a>Az előfeltételeket tartalmazó termékek licenceinek kezelése

Egyes Microsoft *Online-termékek,* amelyek a tulajdonában lehetnek, bővítmények . A bővítmények hez egy előfeltételként szükséges szolgáltatási terv szükséges egy felhasználó vagy csoport számára, mielőtt licenchez rendelhetők. A csoportalapú licencelés sel a rendszer megköveteli, hogy mind az előfeltétel, mind a kiegészítő szolgáltatási csomagok ugyanabban a csoportban legyenek jelen. Ez annak érdekében történik, hogy a csoporthoz hozzáadott felhasználók megkapják a teljesen működő terméket. Vizsgáljuk meg a következő példát:

A Microsoft Workplace Analytics egy kiegészítő termék. Egyetlen, azonos nevű szolgáltatási csomagot tartalmaz. Ezt a szolgáltatási csomagot csak akkor rendelhetjük hozzá egy felhasználóhoz vagy csoporthoz, ha az alábbi előfeltételek valamelyike is hozzá van rendelve:

- Exchange Online (1. terv)
- Exchange Online (2. terv)

Ha ezt a terméket önmagában próbáljuk hozzárendelni egy csoporthoz, a portál egy értesítési üzenetet küld. Ha kiválasztjuk az elem részleteit, a következő hibaüzenet jelenik meg:

  "A licencművelet nem sikerült. Győződjön meg arról, hogy a csoport rendelkezik a szükséges szolgáltatásokkal, mielőtt hozzáadná vagy eltávolítaná a függő szolgáltatást. **A Microsoft Workplace Analytics szolgáltatáshoz az Exchange Online (2. terv) is engedélyezni kell.**

Ahhoz, hogy ezt a bővítménylicencet egy csoporthoz rendeljük, biztosítanunk kell, hogy a csoport tartalmazza az előfeltételként szükséges szolgáltatási csomagot is. Például frissíthetünk egy meglévő csoportot, amely már tartalmazza a teljes Office 365 E3 terméket, majd hozzáadhatjuk hozzá a bővítményterméket.

Lehetőség van olyan önálló csoport létrehozására is, amely csak a bővítmény működéséhez szükséges minimális termékeket tartalmazza. Ez lehet használni, hogy a licenc csak a kiválasztott felhasználók számára a bővítmény termék. Az előző példa alapján a következő termékeket rendelné ugyanahhoz a csoporthoz:

- Office 365 Nagyvállalati E3 csomag, amelyen csak az Exchange Online (2. csomag) szolgáltatáscsomag van engedélyezve
- Microsoft Workplace Analytics

Mostantól a csoporthoz hozzáadott felhasználók az E3 termék egy licencét és a Workplace Analytics-termék egy licencét használják fel. Ugyanakkor ezek a felhasználók egy másik csoport tagjai lehetnek, amely a teljes E3 terméket biztosítja számukra, és továbbra is csak egy licencet használnak az adott termékhez.

> [!TIP]
> Minden előfeltételként szervizcsomaghoz több csoportot is létrehozhat. Ha például az Office 365 Nagyvállalati E1 és az Office 365 Nagyvállalati E3 szolgáltatást is használja a felhasználók számára, két csoportot hozhat létre a Microsoft Workplace Analytics licenceléséhez: az egyik előfeltételként az E1-et, a másik pedig az E3-at használja. Ez lehetővé teszi a bővítmény terjesztését az E1 és e3 felhasználók számára további licencek fogyasztása nélkül.

## <a name="force-group-license-processing-to-resolve-errors"></a>Csoportlicenc-feldolgozás kényszerítése a hibák elhárításához

Attól függően, hogy milyen lépéseket tett a hibák elhárítása érdekében, előfordulhat, hogy manuálisan kell elindítania egy csoport feldolgozását a felhasználói állapot frissítéséhez.

Ha például bizonyos licenceket úgy szabadít fel, hogy eltávolítja a közvetlen licenc-hozzárendeléseket a felhasználóktól, akkor olyan csoportok feldolgozását kell elindítania, amelyek korábban nem tudták teljes mértékben licencelni az összes felhasználótagot. Csoport újbóli feldolgozásához lépjen a csoportablaktáblára, nyissa meg a **Licencek**lehetőséget, és válassza az **Eszköztár Újrafeldolgozás** gombját.

## <a name="force-user-license-processing-to-resolve-errors"></a>Felhasználói licenc feldolgozásának kényszerítése a hibák elhárítására

Attól függően, hogy milyen lépéseket tett a hibák elhárítása érdekében, előfordulhat, hogy manuálisan kell elindítania a felhasználó feldolgozását a felhasználói állapot frissítéséhez.

Ha például egy érintett felhasználó duplikált proxycím-problémáját oldotta fel, aktiválnia kell a felhasználó feldolgozását. A felhasználó újbóli feldolgozásához lépjen a felhasználói ablaktáblára, nyissa meg a **Licencek ablakot,** és válassza az **Eszköztár Újrafeldolgozás** gombját.

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni a csoportokon keresztüli licenckezelés egyéb forgatókönyveiről, olvassa el az alábbi témaköröket:

* [Mi a csoportalapú licencelés az Azure Active Directoryban?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Licencek hozzárendelése egy csoporthoz az Azure Active Directoryban](licensing-groups-assign.md)
* [Egyéni, licenccel rendelkező felhasználók migrálása csoportalapú licencelésre az Azure Active Directoryban](licensing-groups-migrate-users.md)
* [Felhasználók áttelepítése a terméklicencek között az Azure Active Directory csoportalapú licencelésével](licensing-groups-change-licenses.md)
* [Az Azure Active Directory csoportalapú licencelésének további forgatókönyvei](licensing-group-advanced.md)
* [Példák a Csoportalapú licenceléshez az Azure Active Directoryban](licensing-ps-examples.md)
