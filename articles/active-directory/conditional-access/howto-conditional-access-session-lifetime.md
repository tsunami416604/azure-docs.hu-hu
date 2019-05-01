---
title: A feltételes hozzáférés az Azure Active Directory hitelesítési munkamenet-kezelés konfigurálása
description: Testre szabhatja az Azure AD hitelesítési munkamenet-konfiguráció beleértve a felhasználói bejelentkezési gyakoriság és a böngésző-munkamenet megőrzését.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 884b3c2d3f4414427df87ee5f04138416f363b1c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577225"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Hitelesítési munkamenet-kezelés konfigurálása a feltételes hozzáférés

Összetett telepítések a szervezetek lehet hitelesítési munkamenetek korlátozni kell. Egyes forgatókönyvekben előfordulhat, hogy a következők:

* Erőforrás-hozzáférés egy nem felügyelt vagy megosztott eszközről
* Bizalmas adatokat egy külső hálózathoz való hozzáférés
* Nagy hatású felhasználók
* Kritikus fontosságú üzleti alkalmazások

Feltételes hozzáférés-vezérlés lehetővé teszik a szervezeten belüli adott használati eseteket működésének megzavarása nélkül megtesztelheti az összes felhasználó célzó szabályzatokat hozhat létre.

A szabályzat konfigurálásának ról részletei, előtt hozzunk vizsgálja meg az alapértelmezett konfiguráció.

## <a name="user-sign-in-frequency"></a>Felhasználói bejelentkezési gyakoriság

Bejelentkezési gyakoriság az adott időszakban, mielőtt a felhasználó felkérést kap, jelentkezzen be újra egy erőforrás elérésekor határozza meg.

Felhasználói bejelentkezési gyakoriság az Azure Active Directory (Azure AD) alapértelmezett konfigurációja a 90 napos gördülő ablak. Kell megadniuk a hitelesítő adatok gyakran egy kézenfekvő teendő tűnik, de azt is backfire: felhasználók, amelyek véletlenül is meg kell adniuk adataikat nélkül terhelése is képzett megadnia őket rosszindulatú kéri.

Védekezés riasztó nem kérni a felhasználót, hogy a informatikai házirendek bármilyen megsértése visszavonják a munkamenet valóságban 90 napja, jelentkezzen be. Néhány példa tartalmazza (de nem korlátozódnak) jelszómódosítás, kapcsolópéldány eszköz vagy fiók letiltásához. Explicit módon biztosítania is [visszavonása a PowerShell használatával a felhasználók munkameneteit](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Az Azure ad-ben alapértelmezett konfiguráció "Ne kérje a felhasználóknak megadniuk hitelesítő adataikat, biztonsági állapotát a munkamenet nem változott. Ha" érhető el.

Bejelentkezési gyakoriság beállítása olyan alkalmazásokkal, amelyek valósította OATH2 vagy OIDC protokollok, az előírásoknak megfelelően működik. A legtöbb Microsoft natív alkalmazások Windows, Mac és mobil megfelelnek a beállítást.

## <a name="persistence-of-browsing-sessions"></a>Böngészési munkamenetet megőrzése

Állandó böngésző-munkamenet lehetővé teszi a felhasználók bejelentkezve maradjanak, után bezárása és újbóli megnyitása a böngészőablakot.

Az Azure ad-ben alapértelmezett böngésző-munkamenet megőrzését lehetővé teszi, hogy a felhasználók a saját eszközét, döntse el, hogy a munkamenet megőrzése "Tartózkodás jelentkezett be?" megjelenítésével Rákérdezés a sikeres hitelesítés után. Ha a böngésző adatmegőrzés van konfigurálva az AD FS-cikk útmutatása [AD FS egyszeri bejelentkezési beállításainak](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
), rendszer megfelelnek az adott házirendnek és az Azure AD munkamenet megőrzése. Is beállítható, hogy a felhasználók a saját bérlőjében, tekintse meg a "tartózkodás jelentkezett be?" Rákérdezés a vállalati arculat a panel az Azure Portalon, a cikk útmutatása a megfelelő beállítás módosításával [az Azure AD bejelentkezési oldal testreszabása](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Munkamenet-vezérlők hitelesítés konfigurálása

Feltételes hozzáférés egy prémium szintű Azure AD-képesség, és a prémium licencre van szükség. Ha szeretne további információ a feltételes hozzáférésről, lásd: [Mi az az Azure Active Directory feltételes hozzáférés?](overview.md#license-requirements-for-using-conditional-access)

> [!WARNING]
> Ha használja a [konfigurálható jogkivonat élettartama](../develop/active-directory-configurable-token-lifetimes.md) funkció jelenleg nyilvános előzetes verzióban érhető el, vegye figyelembe, hogy nem támogatjuk a ugyanazon felhasználó vagy alkalmazás kombináció két különböző szabályzatokat hoznak létre: egy ezt a szolgáltatást és a egy másik a szolgáltatás konfigurálható jogkivonat élettartama. A Microsoft tervezi a konfigurálható jogkivonat élettartama szolgáltatás kivonása. November 1-jén, és cserélje le a feltételes hozzáférés hitelesítési munkamenet felügyeleti funkció.  

### <a name="policy-1-sign-in-frequency-control"></a>1. szabályzat: Bejelentkezési gyakoriság vezérlő

1. Új szabályzat létrehozása
1. Válassza ki az ügyfél környezetben, beleértve a célként megadott felhőalapú alkalmazások az összes szükséges feltételeket.

   > [!NOTE]
   > Javasoljuk, hogy egyenlő hitelesítési kérdés gyakoriságot legjobb felhasználói élmény kulcs a Microsoft Office-alkalmazások például az Exchange online-hoz és a SharePoint online-hoz.

1. Lépjen a **hozzáférés-vezérlés** > **munkamenet** kattintson **bejelentkezési gyakoriság**
1. Az első mezőben adja meg a szükséges érték nap és óra
1. Válasszon ki egy értéket a **óra** vagy **nap** legördülő listából
1. Házirend mentése

![Bejelentkezési gyakoriság az konfigurált feltételes hozzáférési szabályzat](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

Az Azure ad-ben regisztrált Windows eszközökre jelentkezzen be az eszköz számít egy parancssort. Például ha már konfigurálta a bejelentkezési gyakoriság 24 óráig Office-alkalmazások, az Azure ad-ben regisztrált felhasználók Windows-eszköz eleget tesz a bejelentkezési gyakoriság házirend jelentkezzen be az eszközt, és nem kéri újra Office-alkalmazások megnyitásakor.

Konfigurálta a különböző bejelentkezési gyakoriság az ugyanazon böngésző-munkamenet futó különböző web Apps, a legszigorúbb házirend áfakulcs mindkét alkalmazást, mert az ugyanazon böngésző-munkamenet futó összes alkalmazás egy munkamenet-jogkivonat.

### <a name="policy-2-persistent-browser-session"></a>2. szabályzat: Állandó böngésző-munkamenet

1. Új szabályzat létrehozása
1. Válassza ki az összes szükséges feltételeket.

   > [!NOTE]
   > Vegye figyelembe, hogy ez a vezérlő kiválasztása a "Minden felhőalapú alkalmazásra" vonatkozó feltételben előírja

1. Lépjen a **hozzáférés-vezérlés** > **munkamenet** kattintson **állandó böngésző-munkamenet**
1. Válasszon ki egy értéket a legördülő listából
1. Házirend mentése

![Állandó böngésző beállított feltételes hozzáférési szabályzat](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Az Azure AD feltételes hozzáférési állandó böngésző-munkamenet-konfiguráció felülírja a "tartózkodási jelentkezett be?" a vállalati arculat megjelenítése a panel az adott felhasználó számára az Azure Portalon, ha már konfigurálta a mindkét házirend beállításával.

## <a name="validation"></a>Ellenőrzés

A Lehetőségelemzési eszközzel szimulálása a célalkalmazás és egyéb feltételek alapján hogyan konfigurálta a házirend a felhasználói bejelentkezés. A hitelesítési munkamenet felügyeleti vezérlők az eszköz az eredmény jelenik meg.

![Mi történik, ha az eredmények eszköz feltételes hozzáférés](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Házirend központi telepítése

Győződjön meg arról, hogy a szabályzat a várt módon működik-e, hogy az ajánlott eljárás tesztelése, mielőtt éles környezetben megvalósítaná. Ideális esetben egy tesztelési bérlőn használatával győződjön meg arról, hogy az új szabályzat helyesen működik-e. További információkért tekintse meg a cikket [ajánlott eljárások az Azure Active Directory feltételes hozzáférés](best-practices.md).

## <a name="next-steps"></a>További lépések

* Ha szeretné tudni, hogy a feltételes hozzáférési szabályzat konfigurálása, tekintse meg a cikket [többtényezős hitelesítés megkövetelése az Azure Active Directory feltételes hozzáférés az adott alkalmazások](app-based-mfa.md).
* Ha készen áll a környezetnek a feltételes hozzáférési szabályzatok konfigurálhatók, tekintse meg a cikket [ajánlott eljárások az Azure Active Directory feltételes hozzáférés](best-practices.md).
