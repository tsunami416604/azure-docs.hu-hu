---
title: Külső felhasználók hozzáférésének szabályozása az Azure AD jogosultságkezelésében – Azure Active Directory
description: Ismerje meg a külső felhasználók hozzáférésének szabályozásához az Azure Active Directory jogosultságkezelés.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: af0f68ca9520c1715463212da80aaabed48f8269
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128695"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>Külső felhasználók hozzáférésének szabályozása az Azure AD-jogosultságok kezelésében

Az Azure AD jogosultságkezelés az [Azure AD vállalaton belüli (B2B)](../b2b/what-is-b2b.md) szolgáltatást használja a szervezeten kívüli személyekkel való együttműködésre egy másik címtárban. Az Azure AD B2B segítségével a külső felhasználók hitelesítik magukat a kezdőkönyvtárukban, de rendelkeznek egy képviselettel a címtárban. A címtárban lévő képviselet lehetővé teszi, hogy a felhasználó nak hozzáférése legyen az erőforrásokhoz.

Ez a cikk a külső felhasználók hozzáférésének szabályozásához megadott beállításokat ismerteti.

## <a name="how-entitlement-management-can-help"></a>Hogyan segíthet a jogosultságkezelés?

Az [Azure AD B2B](../b2b/what-is-b2b.md) meghívási élmény használatakor már ismernie kell a külső vendégfelhasználók e-mail címét, amelyeket be szeretne vinni az erőforrás-címtárba, és dolgozni szeretne. Ez akkor működik nagyszerűen, ha kisebb vagy rövid távú projekten dolgozik, és már ismeri az összes résztvevőt, de ezt nehezebb kezelni, ha sok felhasználóval szeretne dolgozni, vagy ha a résztvevők idővel változnak.  Előfordulhat például, hogy egy másik szervezettel dolgozik, és egy kapcsolattartó ponttal rendelkezik az adott szervezettel, de idővel az adott szervezet további felhasználóinak is szükségük lesz a hozzáférésre.

A jogosultságkezelés segítségével olyan házirendet határozhat meg, amely lehetővé teszi, hogy a megadott szervezetek felhasználói önkiszolgálóan kérhessenek hozzáférési csomagot. Megadhatja, hogy szükség van-e jóváhagyásra, és a hozzáférés lejárati dátuma. Ha jóváhagyásra van szükség, a külső szervezet egy vagy több felhasználóját is meghívhat a címtárba, és jóváhagyóként jelölheti ki őket , mivel valószínűleg tudják, hogy a szervezetük mely külső felhasználóinak van szükségük hozzáférésre. Miután konfigurálta a hozzáférési csomagot, elküldheti a hozzáférési csomag hivatkozását a külső szervezet kapcsolattartójának (szponzorának). Ez a kapcsolattartó megoszthatja a külső szervezet más felhasználóival, és ezzel a hivatkozással kérheti a hozzáférési csomagot. Az adott szervezet azon felhasználói is használhatják ezt a hivatkozást, akik már meghívást kaptak a címtárba.

A kérelem jóváhagyásakor a jogosultságkezelés kiépíti a felhasználót a szükséges hozzáféréssel, amely magában foglalhatja a felhasználó meghívását, ha még nem szerepel a címtárban. Az Azure AD automatikusan létrehoz egy B2B vendégfiókot. Vegye figyelembe, hogy a rendszergazda korábban korlátozhatja, hogy mely szervezetek engedélyezettek az együttműködésre, ha egy [B2B engedélyezési vagy megtagadási listát](../b2b/allow-deny-list.md) állít be a más szervezeteknek szóló meghívók engedélyezésére vagy letiltására.  Ha a felhasználó nem engedélyezett az engedélyezési vagy tiltólista, akkor nem lesz meghívva.

Mivel nem szeretné, hogy a külső felhasználó hozzáférése örökké tartson, meg kell adnia egy lejárati dátumot a házirendben, például 180 napot. 180 nap elteltével, ha a hozzáférésük nem hosszabbodik meg, a jogosultságkezelés eltávolítja az adott hozzáférési csomaghoz tartozó összes hozzáférést. Alapértelmezés szerint, ha a jogosultságkezelésen keresztül meghívott felhasználónak nincs más hozzáférési csomag-hozzárendelése, majd az utolsó hozzárendelés elvesztésekor a vendégfiók 30 napra le lesz tiltva a bejelentkezéstől, majd eltávolítja őket. Ez megakadályozza a szükségtelen számlák elburjánzását. A következő szakaszokban leírtak szerint ezek a beállítások konfigurálhatók.

## <a name="how-access-works-for-external-users"></a>A hozzáférés működése külső felhasználók számára

Az alábbi ábra és lépések áttekintést nyújtanak arról, hogy a külső felhasználók hogyan kapnak hozzáférést egy hozzáférési csomaghoz.

![Külső felhasználók életciklusát bemutató diagram](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. [Hozzáad egy csatlakoztatott szervezetet](entitlement-management-organization.md) az Azure AD címtárhoz vagy tartományhoz, amelyekkel együtt kíván működni.

1. Hozzon létre egy hozzáférési csomagot a könyvtárban, amely tartalmazza a [házirendet a nem a könyvtárban lévő felhasználók számára.](entitlement-management-access-package-create.md#for-users-not-in-your-directory)

1. A [Saját Hozzáférés portál hivatkozását](entitlement-management-access-package-settings.md) a külső szervezeten lévő partnerére küldi, amelyet megoszthatnak a felhasználókkal a hozzáférési csomag kéréséhez.

1. Egy külső felhasználó **(A kéréskérő** ebben a példában) a Saját hozzáférés portál hivatkozását használja a hozzáférési [csomaghoz való hozzáférés kéréséhez.](entitlement-management-request-access.md) A felhasználó bejelentkezésének módja a csatlakoztatott szervezetben definiált címtár vagy tartomány hitelesítési típusától függ.

1. A [jóváhagyó jóváhagyja a kérelmet](entitlement-management-request-approve.md) (vagy a kérelem automatikusan jóváhagyott).

1. A kérelem a [kézbesítő állapotba](entitlement-management-process.md)kerül.

1. A B2B meghívási folyamat használatával vendégfelhasználói fiók jön létre a címtárban ( Ebben a példában a**Kéréskérő A (Vendég).** Ha [engedélyezési lista vagy megtagadási lista](../b2b/allow-deny-list.md) van megadva, a listabeállítás lesz alkalmazva.

1. A vendégfelhasználó a hozzáférési csomag összes erőforrásához kap hozzáférést. Eltarthat egy ideig, amíg az Azure AD-ben és más Microsoft Online Services-alkalmazásokban vagy csatlakoztatott SaaS-alkalmazásokban módosításokat kell végrehajtani. További információt a [Módosítások alkalmazásakor](entitlement-management-access-package-resources.md#when-changes-are-applied)című témakörben talál.

1. A külső felhasználó e-mailt kap, amely jelzi, hogy a hozzáférésük [kézbesítve](entitlement-management-process.md)volt.

1. Az erőforrások eléréséhez a külső felhasználó vagy az e-mailben lévő hivatkozásra kattint, vagy közvetlenül a címtár-erőforrások bármelyikéhez próbál hozzáférni a meghívási folyamat befejezéséhez.

1. A házirend-beállításoktól függően az idő múlásával a külső felhasználó hozzáférési csomag-hozzárendelése lejár, és a külső felhasználó hozzáférése törlődik.

1. A külső felhasználók beállításainak életciklusától függően, ha a külső felhasználó már nem rendelkezik hozzáférési csomag-hozzárendelésekkel, a külső felhasználó nem tud bejelentkezni, és a vendég felhasználói fiók törlődik a címtárból.

## <a name="settings-for-external-users"></a>Külső felhasználók beállításai

Annak érdekében, hogy a szervezeten kívüli személyek hozzáférési csomagokat kérhessenek, és hozzáférjenek az ilyen hozzáférési csomagok ban lévő erőforrásokhoz, bizonyos beállításokat ellenőriznie kell, hogy megfelelően vannak-e konfigurálva.

### <a name="enable-catalog-for-external-users"></a>Katalógus engedélyezése külső felhasználók számára

- Alapértelmezés szerint [új katalógus](entitlement-management-catalog-create.md)létrehozásakor engedélyezve van, hogy a külső felhasználók hozzáférési csomagokat kérjenek a katalógusban. Győződjön meg arról, hogy **a külső felhasználók számára engedélyezve** van,Ha **igen.**

    ![Katalógusbeállítások szerkesztése](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>Az Azure AD B2B külső együttműködési beállításainak konfigurálása

- Ha lehetővé teszi a vendégek számára, hogy más vendégeket is meghívjanak a könyvtárba, az azt jelenti, hogy a meghívottak a jogosultságkezelésen kívül is előfordulhatnak. Azt javasoljuk, hogy a Vendégek csak a megfelelően szabályozott meghívók engedélyezéséhez engedélyezze a **Vendégek meghívását** **nem** rekedésre.
- Ha a B2B engedélyezési listát használja, győződjön meg arról, hogy a jogosultságkezelés használatával partneri szeretne lenni kívánt tartomány hozzáadódik a listához. Másik lehetőségként, ha a B2B megtagadási listát használja, győződjön meg arról, hogy a partnerként használni kívánt tartomány nem kerül fel a listára.
- Ha minden **felhasználóhoz** létrehoz egy jogosultságkezelési házirendet (Minden csatlakoztatott szervezet + bármely új külső felhasználó), akkor a Listák engedélyezése vagy megtagadása minden felhasználó elsőbbséget élvez. Ezért ne felejtse el felvenni a házirendbe felvenni kívánt tartományokat az engedélyezési listába, ha használ egyet, és zárja ki őket a megtagadási listából, ha megtagadási listát használ.
- Ha olyan jogosultságkezelési házirendet szeretne létrehozni, amely tartalmazza **az összes felhasználót** (Minden csatlakoztatott szervezet + bármely új külső felhasználó), először engedélyeznie kell az e-mail egyszeri jelkód-hitelesítést a címtárhoz. További információ: [E-mail egyszeri jelkód-hitelesítés (előzetes verzió)](../b2b/one-time-passcode.md#opting-in-to-the-preview)című témakörben talál.
- Az Azure AD B2B külső együttműködési beállításairól a [B2B külső együttműködés engedélyezése és](../b2b/delegate-invitations.md)a vendégek meghívásának kezelése című témakörben olvashat bővebben.

    ![Az Azure AD külső együttműködési beállításai](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>A feltételes hozzáférési házirendek áttekintése

- Győződjön meg arról, hogy kizárja a vendégeket minden olyan feltételes hozzáférési szabályzatból, amelyet az új vendégfelhasználók nem tudnak teljesíteni, mivel ez megakadályozza, hogy bejelentkezhessenek a címtárba. Például a vendégek valószínűleg nem rendelkeznek regisztrált eszközzel, nincsenek ismert helyen, és nem szeretnének újra regisztrálni a többtényezős hitelesítésre (MFA), ezért ha ezeket a követelményeket a feltételes hozzáférési szabályzatba adja hozzá, az megakadályozza, hogy a vendégek jogosultságot használjanak. Kezelése. További információ: [Mik a feltételek az Azure Active Directory feltételes hozzáférésében?](../conditional-access/concept-conditional-access-conditions.md).

    ![Az Azure AD feltételes hozzáférési házirendje kizárja a beállításokat](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>A SharePoint Online külső megosztási beállításainak áttekintése

- Ha a külső felhasználók hozzáférési csomagjaiban SharePoint Online-webhelyeket szeretne felvenni, győződjön meg arról, hogy a szervezetszintű külső megosztási beállítás **beállítása Bárki** (a felhasználóknak nincs szükségük bejelentkezésre) vagy új és **meglévő vendégekre** (a vendégeknek be kell jelentkezniük, vagy meg kell adniuk az ellenőrző kódot). További információt a [Külső megosztás be- és kikapcsolása](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)című témakörben talál.

- Ha a jogosultságkezelésen kívül bármilyen külső megosztást korlátozni szeretne, beállíthatja a külső megosztási beállítást **a Meglévő vendégek beállításra.** Ezután csak a jogosultságkezelésen keresztül meghívott új felhasználók férhetnek hozzá ezekhez a webhelyekhez. További információt a [Külső megosztás be- és kikapcsolása](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)című témakörben talál.

- Győződjön meg arról, hogy a helyszintű beállítások engedélyezik a vendéghozzáférést (ugyanazok a beállítások, mint korábban felsoroltak). További információt a Külső megosztás be- és kikapcsolása webhelyen című [témakörben talál.](https://docs.microsoft.com/sharepoint/change-external-sharing-site)

### <a name="review-your-office-365-group-sharing-settings"></a>Az Office 365 csoportmegosztási beállításainak áttekintése

- Ha office 365-csoportokat szeretne felvenni a külső felhasználók hozzáférési csomagjaiba, győződjön meg arról, hogy a **Felhasználók új vendégek hozzáadása a szervezethez** beállítás be van kapcsolva **a** vendéghozzáférés engedélyezéséhez. További információt az [Office 365-csoportok hoz való vendéghozzáférés kezelése című témakörben talál.](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide#manage-groups-guest-access)

- Ha azt szeretné, hogy a külső felhasználók elérhesd a SharePoint Online-webhelyet és az Office 365-csoporthoz társított erőforrásokat, kapcsolja be a SharePoint Online külső megosztását. További információt a [Külső megosztás be- és kikapcsolása](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)című témakörben talál.

- Az Office 365-csoportok vendégházirendjének a PowerShell címtárszintjén való beállításáról a [Példa: Vendégházirend konfigurálása csoportokhoz címtárszinten](../users-groups-roles/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level)című témakörben talál további információt.

### <a name="review-your-teams-sharing-settings"></a>A Teams megosztási beállításainak áttekintése

- Ha a Teamst be szeretné vonni a külső felhasználók hozzáférési csomagjaiba, győződjön meg arról, hogy a Vendéghozzáférés engedélyezése a **Microsoft Teamsben** Be beállítással engedélyezze **a** vendéghozzáférést. További információt a [Vendéghozzáférés konfigurálása a Microsoft Teams felügyeleti központban című témakörben talál.](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center)

## <a name="manage-the-lifecycle-of-external-users"></a>Külső felhasználók életciklusának kezelése

Kiválaszthatja, hogy mi történjen, ha egy külső felhasználó, akit egy hozzáférésicsomag-kérelem jóváhagyásával hívtak meg a címtárba, már nem rendelkezik hozzáférési csomag-hozzárendelésekkel. Ez akkor fordulhat elő, ha a felhasználó lemond az összes hozzáférési csomag-hozzárendeléséről, vagy az utolsó hozzáférési csomag-hozzárendelésük lejár. Alapértelmezés szerint, ha egy külső felhasználó már nem rendelkezik hozzáférési csomag-hozzárendelésekkel, a rendszer letiltja a címtárba való bejelentkezést. 30 nap elteltével a vendégfelhasználói fiók törlődik a címtárból.

**Előfeltételi szerepkör:** Globális rendszergazda vagy felhasználó

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menü **Jogosultságkezelés** szakaszában kattintson a **Beállítások gombra.**

1. Kattintson a **Szerkesztés** gombra.

    ![A külső felhasználók életciklusának kezelésére szolgáló beállítások](./media/entitlement-management-external-users/settings-external-users.png)

1. A **Külső felhasználók életciklusának kezelése** csoportban válassza ki a külső felhasználók különböző beállításait.

1. Ha egy külső felhasználó elveszíti az utolsó hozzárendelését bármely hozzáférési csomaghoz, ha le szeretné tiltani, hogy bejelentkezzenek ebbe a könyvtárba, állítsa a **Külső felhasználó bejelentkezésének blokkolása ebbe a könyvtárba** **Igen**..

    > [!NOTE]
    > Ha egy felhasználó nem tud bejelentkezni ebbe a könyvtárba, akkor a felhasználó nem tudja újra kérni a hozzáférési csomagot, vagy további hozzáférést kérni ebben a könyvtárban. Ne állítsa be a bejelentkezésblokkolását, ha később más hozzáférési csomagokhoz kell hozzáférést kérniük.

1. Ha egy külső felhasználó elveszíti az utolsó hozzárendelését bármely hozzáférési csomaghoz, ha el szeretné távolítani a vendég felhasználói fiókját ebben a könyvtárban, állítsa a Külső felhasználó eltávolítása lehetőséget **Igen (Igen)** beállításra. **Remove external user**

    > [!NOTE]
    > A jogosultságkezelés csak a jogosultságkezelés során meghívott fiókokat távolítja el. Vegye figyelembe azt is, hogy a rendszer akkor is letiltja a felhasználó bejelentkezését és eltávolítását a címtárból, ha a felhasználót hozzáadta a címtárban lévő olyan erőforrásokhoz, amelyek nem férnek hozzá a csomag-hozzárendelésekhez. Ha a vendég jelen volt ebben a könyvtárban, mielőtt hozzáférést kapott volna a csomaghozzárendelésekhez, akkor azok megmaradnak. Ha azonban a vendéget egy hozzáférési csomag-hozzárendelésen keresztül hívták meg, és miután a meghívást egy OneDrive Vállalati verziós vagy SharePoint Online-webhelyhez is hozzárendelték, akkor is törlődik.

1. Ha el szeretné távolítani a vendég felhasználói fiókot ebben a könyvtárban, beállíthatja az eltávolítás előtti napok számát. Ha el szeretné távolítani a vendég felhasználói fiókot, amint azok elveszítik az utolsó hozzárendelést bármely hozzáférési csomaghoz, állítsa be **a Napok száma, mielőtt eltávolítaná a külső felhasználót a könyvtárból** **0-ra.**

1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

- [Csatlakoztatott szervezet hozzáadása](entitlement-management-organization.md)
- [A címtárban nem lévő felhasználók számára](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [Elhárítása](entitlement-management-troubleshoot.md)
