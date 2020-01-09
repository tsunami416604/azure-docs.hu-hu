---
title: Jogosultsági felügyelet – Azure AD
description: Ismerkedjen meg néhány olyan elemmel, amelyeket érdemes ellenőriznie, hogy segítsen a Azure Active Directory jogosultságok felügyeletének megoldásában.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: e291a032c1aac45ebc783126e69b524e1d0af95b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422496"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Az Azure AD-jogosultságok kezelése – problémamegoldás

Ez a cikk néhány olyan elemet ismertet, amely segítséget nyújt az Azure Active Directory (Azure AD) jogosultságok kezelésének hibaelhárításában.

## <a name="administration"></a>Felügyelet

* Ha hozzáférés-megtagadási üzenetet kap a jogosultságok kezelésének konfigurálásakor, és Ön globális rendszergazda, győződjön meg arról, hogy a címtárban van egy [prémium szintű Azure ad P2 (vagy EMS E5) licenc](entitlement-management-overview.md#license-requirements).

* Ha hozzáférési csomagok létrehozásakor vagy megtekintésekor kap hozzáférés-megtagadási üzenetet, és Ön egy katalógus-létrehozó csoport tagja, létre kell [hoznia egy katalógust](entitlement-management-catalog-create.md) az első hozzáférési csomag létrehozása előtt.

## <a name="resources"></a>Segédanyagok és eszközök

* Az alkalmazások szerepkörei az alkalmazás által definiált és az Azure AD-ben kezelhetők. Ha egy alkalmazás nem rendelkezik erőforrás-szerepkörökkel, a jogosultságok kezelése a felhasználókat egy **alapértelmezett hozzáférési** szerepkörhöz rendeli.

    Vegye figyelembe, hogy a Azure Portal olyan szolgáltatások egyszerű szolgáltatásait is megjelenítheti, amelyek nem választhatók ki alkalmazásként.  Az **Exchange Online** és a **SharePoint Online** például szolgáltatások, nem pedig a címtárban erőforrás-szerepkörökkel rendelkező alkalmazások, ezért nem szerepelhetnek hozzáférési csomagban.  Ehelyett a csoport alapú licencelés használatával hozzon létre egy megfelelő licencet azon felhasználók számára, akiknek hozzáférésre van szüksége a szolgáltatásokhoz.

* Ahhoz, hogy egy csoport egy hozzáférési csomagban lévő erőforrás legyen, módosítható kell lennie az Azure ad-ben.  A helyszíni Active Directoryból származó csoportok nem rendelhetők hozzá erőforrásként, mert a tulajdonos vagy a tag attribútumai nem módosíthatók az Azure AD-ben.   Az Exchange Online-ból származó csoportok terjesztési csoportokként nem módosíthatók az Azure AD-ben. 

* A SharePoint Online-dokumentumtárak és az egyes dokumentumok nem vehetők fel erőforrásként.  Ehelyett hozzon létre egy [Azure ad biztonsági csoportot](../fundamentals/active-directory-groups-create-azure-portal.md), amely tartalmazza azt a csoportot és egy hely szerepkört a hozzáférési csomagban, a SharePoint Online-ban pedig ezt a csoportot használja a dokumentumtárhoz vagy dokumentumhoz való hozzáférés szabályozásához.

* Ha vannak olyan felhasználók, akik már hozzá vannak rendelve egy hozzáférési csomaggal felügyelni kívánt erőforráshoz, akkor győződjön meg arról, hogy a felhasználók a megfelelő házirenddel vannak hozzárendelve a hozzáférési csomaghoz. Előfordulhat például, hogy egy olyan csoportot szeretne felvenni egy olyan hozzáférési csomagba, amely már rendelkezik a csoport felhasználóinak. Ha a csoportban lévő felhasználók folyamatos hozzáférést igényelnek, akkor megfelelő szabályzattal kell rendelkezniük a hozzáférési csomagokhoz, hogy ne veszítsék el a csoporthoz való hozzáférésüket. A hozzáférési csomagot úgy rendelheti hozzá, hogy kéri a felhasználókat, hogy kérjenek az adott erőforrást tartalmazó hozzáférési csomagot, vagy közvetlenül hozzárendelik azokat a hozzáférési csomaghoz. További információ: [hozzáférési csomag kérésének és jóváhagyási beállításainak módosítása](entitlement-management-access-package-request-policy.md).

* Ha eltávolít egy csapat tagjait, azok törlődnek az Office 365-csoportból is. Előfordulhat, hogy a csapat csevegési funkciójának eltávolítása késleltetve van. További [információ: csoporttagság](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership).

* Győződjön meg arról, hogy a címtár nincs konfigurálva több földrajzi helyhez. A jogosultságok kezelése jelenleg nem támogatja a SharePoint Online-hoz tartozó több földrajzi hely használatát. A SharePoint Online-webhelyeknek az alapértelmezett földrajzi helyen kell lenniük, hogy az a jogosultságok kezelésével legyen szabályozva. További információkért lásd: [a OneDrive és a SharePoint Online több földrajzi lehetőségei](https://docs.microsoft.com/Office365/Enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-office-365).

## <a name="external-users"></a>Külső felhasználók

* Ha egy külső felhasználó hozzáférést szeretne kérni egy hozzáférési csomaghoz, győződjön meg arról, hogy a hozzáférési csomaghoz tartozó **saját hozzáférési portál hivatkozást** használják. További információ: [megosztási hivatkozás a hozzáférési csomag igényléséhez](entitlement-management-access-package-settings.md). Ha egy külső felhasználó csak a **myaccess.microsoft.com** látogat, és nem használja a teljes saját hozzáférési portál hivatkozást, akkor a saját szervezetében, és nem a szervezeten belül elérhető hozzáférési csomagokat fogja látni.

* Ha egy külső felhasználó nem tud hozzáférést kérni egy hozzáférési csomaghoz, vagy nem fér hozzá az erőforrásokhoz, ügyeljen arra, hogy ellenőrizze a [külső felhasználók beállításait](entitlement-management-external-users.md#settings-for-external-users).

* Ha egy olyan új külső felhasználó, amely korábban még nem jelentkezett be a címtárba, olyan hozzáférési csomagot kap, amely tartalmazza a SharePoint Online-webhelyet, a hozzáférési csomagja nem teljes kézbesítésre kerül, amíg a fiók a SharePoint Online-ban nincs kiépítve. A beállítások megosztásával kapcsolatos további információkért lásd: [a SharePoint Online külső megosztási beállításainak áttekintése](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings).

## <a name="requests"></a>Kérelmek

* Ha a felhasználó hozzáférést szeretne kérni egy hozzáférési csomaghoz, győződjön meg róla, hogy a hozzáférési csomaghoz tartozó **saját hozzáférési portál hivatkozást** használja. További információ: [megosztási hivatkozás a hozzáférési csomag igényléséhez](entitlement-management-access-package-settings.md).

* Ha úgy nyitja meg a saját hozzáférési portált, hogy a böngészőben a saját vagy az Incognito üzemmódra van beállítva, akkor ez ütközik a bejelentkezési viselkedéssel. Azt javasoljuk, hogy ne használja a saját böngészőjét a saját hozzáférési portálon, hanem a saját böngészőjében.

* Ha egy olyan felhasználó, aki még nem szerepel a címtárban, bejelentkezik a saját hozzáférési portálra egy hozzáférési csomag igényléséhez, akkor győződjön meg arról, hogy az a szervezeti fiók használatával végzi a hitelesítést. A szervezeti fiók lehet egy fiók az erőforrás-címtárban, vagy egy olyan címtárban, amely a hozzáférési csomag egyik szabályzatában szerepel. Ha a felhasználó fiókja nem szervezeti fiók, vagy a hitelesítést végző címtár nem szerepel a szabályzatban, akkor a felhasználó nem fogja látni a hozzáférési csomagot. További információ: hozzáférés [kérése egy hozzáférési csomaghoz](entitlement-management-request-access.md).

* Ha a felhasználó nem tud bejelentkezni az erőforrás-könyvtárba, nem fog tudni hozzáférést kérni a saját hozzáférési portálon. Mielőtt a felhasználó hozzáférést igényelhet, el kell távolítania a bejelentkezési blokkot a felhasználó profiljából. A bejelentkezési blokk eltávolításához kattintson a Azure Portal **Azure Active Directory**, majd a **felhasználók**elemre, majd a felhasználó elemre, végül a **profil**elemre. Szerkessze a **Settings (beállítások** ) szakaszt, és módosítsa a **Letiltás bejelentkezést** a **nem**értékre. További információ: [felhasználói profil adatainak hozzáadása vagy frissítése Azure Active Directory használatával](../fundamentals/active-directory-users-profile-azure-portal.md).  Azt is megtekintheti, hogy a felhasználó blokkolva lett-e egy [Identity Protection-házirend](../identity-protection/howto-unblock-user.md)miatt.

* Ha a saját hozzáférési portálon, ha a felhasználó egy kérelmező és egy jóváhagyó is, a **jóváhagyások** lapon nem fogja látni a hozzáférési csomagra vonatkozó kérelmét. Ez szándékos viselkedés – a felhasználók nem hagyhatják jóvá a saját kérésükre. Győződjön meg arról, hogy a kért hozzáférési csomaghoz további jóváhagyók vannak konfigurálva a szabályzatban. További információ: [hozzáférési csomag kérésének és jóváhagyási beállításainak módosítása](entitlement-management-access-package-request-policy.md).

### <a name="view-a-requests-delivery-errors"></a>Kérelem kézbesítési hibáinak megtekintése

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **kérelmek**elemre.

1. Válassza ki a megtekinteni kívánt kérelmet.

    Ha a kérelem kézbesítési hibával rendelkezik, a kérelem állapota nem **kézbesítve** vagy **részben kézbesítve**lesz.

    Ha kézbesítési hibák merülnek fel, a kérelem részleteit tartalmazó ablaktáblán a kézbesítési hibák száma jelenik meg.

1. Kattintson a számra az összes kérelem kézbesítési hibájának megjelenítéséhez.

### <a name="reprocess-a-request"></a>Kérelem újrafeldolgozása

Ha egy kérelem hibát észlel, újra feldolgozhatja a kérelmet, hogy újra megpróbálja. Csak olyan kérelmeket lehet újra feldolgozni, amelyeknek a **kézbesítési állapota nem sikerült** , vagy **részlegesen kézbesítve** lett, és egy héttel rövidebb befejezési dátummal rendelkezik.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **kérelmek**elemre.

1. Kattintson az újradolgozni kívánt kérelemre.

1. A kérelem részletei ablaktáblán kattintson a **kérelem újrafeldolgozása**elemre.

    ![Sikertelen kérelem újrafeldolgozása](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>Függőben lévő kérelem megszakítása

Csak olyan függőben lévő kérést lehet megszakítani, amely még nem lett kézbesítve, vagy a kézbesítés sikertelen volt.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **kérelmek**elemre.

1. Kattintson a megszakítani kívánt kérelemre.

1. A kérelem részletei ablaktáblán kattintson a **kérelem megszakítása**elemre.

## <a name="multiple-policies"></a>Több házirend

* A jogosultságok kezelése a legalacsonyabb jogosultsági szintű ajánlott eljárásokat követi. Ha egy felhasználó olyan hozzáférési csomaghoz kér hozzáférést, amelyhez több házirend is tartozik, a jogosultságok kezelése magában foglalja a logikát, hogy a szigorúbb vagy konkrétabb házirendek elsőbbséget élveznek az általános házirendek felett. Ha a szabályzat általános, akkor előfordulhat, hogy a jogosultságok kezelése nem jeleníti meg a házirendet a kérelmezőnek, vagy a szigorúbb szabályzatot is automatikusan kijelöli.

* Vegyünk például egy olyan hozzáférési csomagot, amely két házirendet alkalmaz a belső alkalmazottak számára, amelyekben mindkét házirend a kérelmezőre vonatkozik. Az első szabályzat a kérelmezőt tartalmazó konkrét felhasználókra vonatkozik. A második szabályzat a címtár minden olyan felhasználója számára, aki tagja a kérelmezőnek. Ebben az esetben az első házirendet a rendszer automatikusan kiválasztja a kérelmező számára, mert szigorúbb. A kérelmező nem kap lehetőséget a második szabályzat kiválasztására.

* Ha több házirend van érvényben, az automatikusan kiválasztott házirend vagy a kérelmezőnek megjelenő házirendek a következő prioritási logikán alapulnak:

    | Házirend prioritása | Hatókör |
    | --- | --- |
    | P1 | Adott felhasználók és csoportok a címtárban vagy az adott csatlakoztatott szervezeteknél |
    | P2 | A címtárban lévő összes tag (a vendégek kivételével) |
    | P3 | A címtárban lévő összes felhasználó (beleértve a vendégeket is) vagy adott csatlakoztatott szervezetek |
    | P4 | Minden csatlakoztatott szervezet vagy minden felhasználó (minden csatlakoztatott szervezet + bármely új külső felhasználó) |
    
    Ha bármelyik szabályzat magasabb prioritású kategóriába tartozik, a rendszer figyelmen kívül hagyja az alacsonyabb prioritású kategóriákat. Ha például azt szeretné, hogy az azonos prioritású házirendek hogyan jelenjenek meg a kérelmező számára, tekintse meg a szabályzat [kiválasztása](entitlement-management-request-access.md#select-a-policy)című témakört.

## <a name="next-steps"></a>Következő lépések

- [Hozzáférés szabályozása külső felhasználók számára](entitlement-management-external-users.md)
- [A felhasználók hozzáférési jogosultságok kezelésével kapcsolatos jelentések megtekintése](entitlement-management-reports.md)
