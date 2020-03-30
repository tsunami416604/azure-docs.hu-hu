---
title: Jogosultságkezelés – Azure AD hibaelhárítása
description: Ismerje meg, hogy néhány elemet ellenőriznie kell az Azure Active Directory jogosultságkezelésével kapcsolatos hibák elhárításához.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c38e1a61827da547bb39a699a0e92043e63466c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128476"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Az Azure AD-jogosultságok kezelésének hibaelhárítása

Ez a cikk ismerteti, néhány elem, amelyet ellenőriznie kell, hogy segítsen az Azure Active Directory (Azure AD) jogosultságkezelésének hibaelhárításában.

## <a name="administration"></a>Adminisztráció

* Ha a jogosultságkezelés konfigurálásakor megtagadott hozzáférési üzenetet kap, és globális rendszergazda, győződjön meg arról, hogy a címtár [rendelkezik Egy Azure AD Premium P2 (vagy EMS E5) licenccel.](entitlement-management-overview.md#license-requirements)

* Ha hozzáférési üzenetet kap a hozzáférési csomagok létrehozásakor vagy megtekintésekor, és tagja egy katalóguskészítői csoportnak, az első hozzáférési csomag létrehozása előtt létre kell [hoznia egy katalógust.](entitlement-management-catalog-create.md)

## <a name="resources"></a>Források

* Az alkalmazások szerepköreit maguk az alkalmazások határozzák meg, a kezelésük pedig az Azure AD-ben történik. Ha egy alkalmazás nem rendelkezik erőforrásszerepkörökkal, a jogosultságkezelés **alapértelmezett hozzáférési** szerepkörhöz rendeli a felhasználókat.

    Vegye figyelembe, hogy az Azure Portalon is megjelenítheti a szolgáltatásnévi szolgáltatások, amelyek nem választhatók ki alkalmazásként.  Az **Exchange Online** és a **SharePoint Online** szolgáltatás, nem pedig olyan alkalmazások, amelyek erőforrás-szerepkörrel rendelkeznek a címtárban, így nem vehetők fel hozzáférési csomagba.  Ehelyett csoportalapú licencelés használatával hozzon létre egy megfelelő licencet azon felhasználók számára, akiknek hozzáférésre van szükségük a kérdéses szolgáltatásokhoz.

* Ahhoz, hogy egy csoport erőforrásként belefoglalható legyen egy hozzáférési csomagba, módosíthatónak kell lennie az Azure AD-ben.  A helyszíni Active Directoryból származó csoportok nem rendelhetők hozzá erőforrásként, mert a tulajdonosi vagy tagi attribútumaik nem módosíthatók az Azure AD-ben.   Az Exchange Online-ból származó terjesztési csoportok szintén nem módosíthatók az Azure AD-ben. 

* SharePoint Online-dokumentumtárak és egyedi dokumentumok nem adhatók hozzá erőforrásként.  Ehelyett hozzon létre egy [Azure AD biztonsági csoportot,](../fundamentals/active-directory-groups-create-azure-portal.md)adja meg ezt a csoportot és egy webhelyszerepkört a hozzáférési csomagban, és a SharePoint Online-ban használja ezt a csoportot a dokumentumtárhoz vagy dokumentumhoz való hozzáférés szabályozásához.

* Ha vannak olyan felhasználók, amelyek már hozzá lettek rendelve egy hozzáférési csomaggal felügyelni kívánt erőforráshoz, akkor meg kell győződni arról, hogy a felhasználók megfelelő szabályzat használatával lettek-e hozzárendelve a hozzáférési csomaghoz. Például előfordulhat, hogy a hozzáférési csomagba belefoglalni kívánt csoportban már találhatók felhasználók. Ha az adott csoportban lévő felhasználók folyamatos hozzáférést igényelnek, akkor megfelelő szabályzattal kell rendelkezniük a hozzáférési csomagok esetében, hogy ne veszítsék el a csoporthoz való hozzáférésüket. A hozzáférési csomagok hozzárendeléséhez megkérheti a felhasználókat, hogy kérelmezzék az adott erőforrást tartalmazó hozzáférési csomagot, vagy közvetlenül hozzárendelheti őket a hozzáférési csomaghoz. További információt a [Hozzáférési csomag kérés- és jóváhagyási beállításainak módosítása című témakörben talál.](entitlement-management-access-package-request-policy.md)

* Az eltávolított csapattagok az Office 365-csoportból is törlődnek. Előfordulhat, hogy a csapat csevegési funkciójából való eltávolítás késleltetve történik meg. További információ: [Csoporttagság](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership).

* Győződjön meg arról, hogy a címtár nincs-e több földrajzi helyes használatra konfigurálva. A jogosultságkezelés jelenleg nem támogatja a Multi-Geo-helyeket a SharePoint Online esetében. A SharePoint Online-webhelyeknek az alapértelmezett földrajzi helyen kell lenniük a jogosultságkezeléssel történő szabályozáshoz. További információt a [OneDrive és a SharePoint Online Többgeo-képességei című témakörben talál.](https://docs.microsoft.com/Office365/Enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-office-365)

## <a name="access-packages"></a>Hozzáférési csomagok

* Ha megpróbál törölni egy hozzáférési csomagot vagy házirendet, és megjelenik egy hibaüzenet, amely szerint vannak aktív hozzárendelések, ha nem lát hozzárendeléseket használó felhasználókat, ellenőrizze, hogy a legutóbb törölt felhasználók rendelkeznek-e még hozzárendelésekkel. A felhasználó törlése utáni 30 napos időszakban a felhasználói fiók visszaállítható.   

## <a name="external-users"></a>Külső felhasználók

* Ha egy külső felhasználó hozzáférést szeretne kérni egy hozzáférési csomaghoz, győződjön meg arról, hogy a **Hozzáférési portál hivatkozását** használja a hozzáférési csomaghoz. További információt a [Hivatkozás megosztása hozzáférési csomag igényléséhez](entitlement-management-access-package-settings.md)című témakörben talál. Ha egy külső felhasználó csak **felkeresi myaccess.microsoft.com,** és nem használja a teljes My Access portál hivatkozást, akkor a saját szervezetében, és nem a szervezetben elérhető hozzáférési csomagokat fogja látni.

* Ha egy külső felhasználó nem tud hozzáférést kérni egy hozzáférési csomaghoz, vagy nem tud hozzáférni az erőforrásokhoz, ellenőrizze a [külső felhasználók beállításait.](entitlement-management-external-users.md#settings-for-external-users)

* Ha egy új, külső felhasználó, amely korábban még nem jelentkezett be a címtárba, SharePoint Online-webhelyet tartalmazó hozzáférési csomagot kap, akkor ez a hozzáférési csomag nem teljesen kézbesítettként jelenik meg mindaddig, amíg a felhasználó fiókja nincs létrehozva a SharePoint Online-ban. A megosztási beállításokról a [SharePoint Online külső megosztási beállításainak áttekintése](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings)című témakörben olvashat bővebben.

## <a name="requests"></a>Kérelmek

* Ha egy felhasználó hozzáférést szeretne kérni egy hozzáférési csomaghoz, győződjön meg arról, hogy a **Hozzáférési portál hivatkozását** használja a hozzáférési csomaghoz. További információt a [Hivatkozás megosztása hozzáférési csomag igényléséhez](entitlement-management-access-package-settings.md)című témakörben talál.

* Ha privát/inkognitó módba állított böngészőből nyitja meg a Saját hozzáférés portált, a beállítás problémát okozhat a bejelentkezési viselkedés esetében. Azt javasoljuk, hogy a Saját hozzáférés portál megnyitásakor ne használja a böngésző privát/inkognitó módját.

* Ha egy, a címtárban még nem szereplő felhasználó jelentkezik be a Saját hozzáférés portálra hozzáférési csomag igénylése céljából, akkor meg kell győződni arról, hogy a hitelesítést a szervezeti fiók használatával végzi el. A szervezeti fiók lehet egy fiók az erőforráskönyvtárban vagy egy olyan könyvtárban, amely a hozzáférési csomag valamelyik szabályzatában szerepel. Ha a felhasználó fiókja nem szervezeti fiók, vagy a hitelesítés helyéül szolgáló könyvtár nem szerepel a szabályzatban, akkor a felhasználó nem fogja látni a hozzáférési csomagot. További információt a Hozzáférési csomaghoz való hozzáférés kérése című [témakörben talál.](entitlement-management-request-access.md)

* Ha a felhasználó számára le van tiltva az erőforráskönyvtárba való bejelentkezés, akkor nem fog tudni hozzáférést kérni a Saját hozzáférés portálon. Ahhoz, hogy a felhasználó hozzáférést igényelhessen, el kell távolítani a bejelentkezés letiltását a felhasználó profiljából. A bejelentkezési blokk eltávolításához az Azure Portalon kattintson az **Azure Active Directory**elemre, kattintson a **Felhasználók**elemre, kattintson a felhasználóra, majd a **Profil parancsra.** Módosítsa a **Beállítások szakaszt,** és módosítsa **a Bejelentkezés tiltása** beállítást **Nem**-re. További információt a [Felhasználói profiladatok hozzáadása vagy frissítése az Azure Active Directory használatával című témakörben talál.](../fundamentals/active-directory-users-profile-azure-portal.md)  Azt is ellenőrizheti, hogy a felhasználó le volt-e tiltva [az identitásvédelemházirend](../identity-protection/howto-unblock-user.md)miatt.

* Ha a Saját hozzáférés portálon a felhasználó egykérelmező és egy jóváhagyó is, a **Jóváhagyások** lapon nem fogja látni a hozzáférési csomagra vonatkozó kérelmét. Ez a viselkedés szándékos – a felhasználók nem hagyhatják jóvá a saját kéréseiket. Győződjön meg arról, hogy a felhasználó által kérelmezett hozzáférési csomaghoz további jóváhagyók vannak konfigurálva a szabályzatban. További információt a [Hozzáférési csomag kérés- és jóváhagyási beállításainak módosítása című témakörben talál.](entitlement-management-access-package-request-policy.md)

### <a name="view-a-requests-delivery-errors"></a>A kérelem kézbesítési hibáinak megtekintése

**Előfeltételi szerepkör:** Globális rendszergazda, Felhasználói rendszergazda, Katalógustulajdonosa vagy Access-csomagkezelő

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menüben kattintson az **Access-csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson **a Kérések gombra.**

1. Jelölje ki a megtekinteni kívánt kérelmet.

    Ha a kérelem kézbesítési hibákat tartalmaz, a kérelem állapota nem lesz **kézbesítve,** vagy **részlegesen kézbesítve lesz.**

    Ha kézbesítési hibák merülnek fel, a kérelem részletes ablaktáblájában a kézbesítési hibák száma lesz.

1. Kattintson a számlálóa a kérelem összes kézbesítési hibáinak megtekintéséhez.

### <a name="reprocess-a-request"></a>Kérelem újrafeldolgozása

Ha egy kérelem hibát észlel, újra feldolgozhatja a kérelmet, hogy újra megpróbálja. Csak akkor dolgozhatja fel újra a kérelmet, amelynek állapota **nem sikerült,** vagy **részben kézbesítve,** és a befejezett dátum kevesebb, mint egy hét.

**Előfeltételi szerepkör:** Globális rendszergazda, Felhasználói rendszergazda, Katalógustulajdonosa vagy Access-csomagkezelő

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menüben kattintson az **Access-csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson **a Kérések gombra.**

1. Kattintson az újrafeldolgozni kívánt kérelemre.

1. A kérelem részletei ablaktáblán kattintson a **Kérelem újrafeldolgozása gombra.**

    ![Sikertelen kérelem újbóli feldolgozása](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>Függőben lévő kérelem visszavonása

Csak olyan függőben lévő kérelmet vonhat vissza, amely még nem lett kézbesítve, vagy amelynek kézbesítése sikertelen volt.

**Előfeltételi szerepkör:** Globális rendszergazda, Felhasználói rendszergazda, Katalógustulajdonosa vagy Access-csomagkezelő

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menüben kattintson az **Access-csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson **a Kérések gombra.**

1. Kattintson a megszakítani kívánt kérelemre.

1. A kérelem részletei ablaktáblán kattintson a **Kérelem visszavonása gombra.**

## <a name="multiple-policies"></a>Több házirend

* A jogosultságkezelés a legtöbb jogosultsággal kapcsolatos bevált gyakorlatot követi. Ha egy felhasználó hozzáférést kér egy olyan hozzáférési csomaghoz, amely több házirendet alkalmaz, a jogosultságkezelés logikát is tartalmaz annak érdekében, hogy a szigorúbb vagy konkrétabb házirendek elsőbbséget élvezjenek az általános házirendekkel szemben. Ha egy szabályzat általános, előfordulhat, hogy a jogosultságkezelés nem jeleníti meg a házirendet a kérelmező számára, vagy automatikusan kiválaszthat egy szigorúbb szabályzatot.

* Vegyünk például egy hozzáférési csomagot, amelyben két szabályzat van a belső alkalmazottakszámára, amelyben mindkét szabályzat vonatkozik a kérelmezőre. Az első házirend a kérelmezőt tartalmazó konkrét felhasználókra vonatkozik. A második házirend a címtárban lévő összes felhasználóra, amelynek a kérelmező tagja. Ebben a forgatókönyvben az első szabályzat automatikusan kiválasztódik a kérelmező, mert szigorúbb. A kérelmező nem kap lehetőséget a második házirend kiválasztására.

* Ha több házirend van érvényben, az automatikusan kiválasztott házirend vagy a kérelmező számára megjelenített házirendek a következő prioritási logikán alapulnak:

    | Szakpolitikai prioritás | Hatókör |
    | --- | --- |
    | P1 | A címtárban lévő konkrét felhasználók és csoportok vagy adott csatlakoztatott szervezetek |
    | P2 | A címtár összes tagja (a vendégek kivételével) |
    | P3 | A címtárösszes felhasználója (beleértve a vendégeket is) vagy adott csatlakoztatott szervezetek |
    | P4 | Minden kapcsolódó szervezet VAGY Minden felhasználó (minden csatlakoztatott szervezet + új külső felhasználó) |
    
    Ha bármelyik házirend magasabb prioritású kategóriába tartozik, az alacsonyabb prioritású kategóriákfigyelmen kívül maradnak. Ha például több, azonos prioritású házirend jelenik meg a kérelmező számára, olvassa el [a Házirend kiválasztása című témakört.](entitlement-management-request-access.md#select-a-policy)

## <a name="next-steps"></a>További lépések

- [Külső felhasználók hozzáférésének szabályozása](entitlement-management-external-users.md)
- [Jelentések megtekintése arról, hogy a felhasználók hogyan férhetnek hozzá a jogosultságkezeléshez](entitlement-management-reports.md)
