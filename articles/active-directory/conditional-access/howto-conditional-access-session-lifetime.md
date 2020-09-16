---
title: A hitelesítési munkamenetek kezelésének konfigurálása – Azure Active Directory
description: Az Azure AD-alapú hitelesítési munkamenet konfigurációjának testreszabása, beleértve a felhasználói bejelentkezés gyakoriságát és a böngésző munkamenetének megőrzését.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 06/29/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 034d2410b97562946216815e5bdafd35fe1bc40b
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90601673"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>A hitelesítési munkamenetek kezelésének konfigurálása feltételes hozzáféréssel

Összetett központi telepítések esetén a szervezeteknek szükségük lehet a hitelesítési munkamenetek korlátozására. Egyes forgatókönyvek például a következők lehetnek:

* Erőforrás-hozzáférés nem felügyelt vagy megosztott eszközről
* Bizalmas adatokhoz való hozzáférés külső hálózatról
* Nagy hatású felhasználók
* Kritikus fontosságú üzleti alkalmazások

A feltételes hozzáférés vezérlőkkel olyan házirendeket hozhat létre, amelyek konkrét használati eseteket céloznak meg a szervezeten belül anélkül, hogy ez befolyásolná az összes felhasználót.

Mielőtt megkezdené a szabályzat konfigurálásának részleteit, vizsgáljuk meg az alapértelmezett konfigurációt.

## <a name="user-sign-in-frequency"></a>Felhasználói bejelentkezés gyakorisága

A bejelentkezési gyakoriság határozza meg azt az időtartamot, ameddig a felhasználónak újra be kell jelentkeznie, amikor egy erőforráshoz próbál hozzáférni.

A felhasználói bejelentkezés gyakoriságának Azure Active Directory (Azure AD) alapértelmezett konfigurációja a 90 napos gördülő ablak. A felhasználók a hitelesítő adatokkal való megkérdezése gyakran úgy tűnik, mint egy értelmes dolog, de nem sül el: a hitelesítő adatok megadására betanított felhasználók szándékosan nem tudják megadni őket rosszindulatú hitelesítő adatok megadásához.

Előfordulhat, hogy a felhasználók nem kérik vissza a felhasználót, hogy a valóságban az IT-szabályzatok megszegése visszavonja a munkamenetet. Néhány példa a jelszó módosítására, a nem megfelelő eszközre vagy a fiók letiltására vonatkozik. Explicit módon [visszavonhatja a felhasználói munkameneteket a PowerShell használatával](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Az Azure AD alapértelmezett konfigurációja a "ne kérdezze meg a felhasználókat, hogy adja meg a hitelesítő adataikat, ha a munkamenetek biztonsági helyzete nem változott".

A bejelentkezési gyakoriság beállítása olyan alkalmazásokkal működik, amelyek a szabványok alapján OAUTH2 vagy OIDC protokollokat implementáltak. A Microsoft natív alkalmazásai a Windows, Mac és Mobile rendszerekhez, beleértve a következő webalkalmazásokat is, amelyek megfelelnek a beállításnak.

- Word, Excel, PowerPoint online
- OneNote online
- Office.com
- Microsoft 365 felügyeleti portál
- Exchange Online
- SharePoint és OneDrive
- Csapat webes ügyfele
- Dynamics CRM Online
- Azure Portal

A bejelentkezési gyakoriság beállítása SAML-alkalmazásokkal is működik, feltéve, hogy nem dobja el a saját cookie-kat, és az Azure AD-re való átirányításuk rendszeresen történik.

### <a name="user-sign-in-frequency-and-multi-factor-authentication"></a>Felhasználói bejelentkezés gyakorisága és multi-Factor Authentication

Korábban csak az Azure AD-hez csatlakoztatott, a hibrid Azure AD-hez és az Azure AD-hez regisztrált eszközökön történt a bejelentkezés gyakorisága. A többtényezős hitelesítés (MFA) az adott eszközökön való újbóli betartatása nem volt egyszerű módszer. Az ügyfelek visszajelzései alapján a bejelentkezési gyakoriság az MFA-ra is érvényes lesz.

[![Bejelentkezési gyakoriság és MFA](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart-small.png)](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart.png#lightbox)

### <a name="user-sign-in-frequency-and-device-identities"></a>Felhasználói bejelentkezés gyakorisága és az eszközök identitása

Ha van csatlakoztatva az Azure AD-hez, a hibrid Azure AD-hez vagy az Azure AD-hez regisztrált eszközökhöz, amikor a felhasználó feloldja az eszközt, vagy interaktív módon jelentkezik be, akkor ez az esemény is megfelel a bejelentkezési gyakoriságra vonatkozó házirendnek. A következő két példában a felhasználói bejelentkezési gyakoriság értéke 1 óra:

1. példa:

- 00:00-kor a felhasználók bejelentkeznek a Windows 10 Azure AD-hez csatlakoztatott eszközre, és megkezdik a munkát a SharePoint Online-on tárolt dokumentumon.
- A felhasználó egy órán keresztül folytatja az eszközön való munkát ugyanazon a dokumentumon.
- A (z) 01:00-es időpontban a felhasználónak újra be kell jelentkeznie a bejelentkezési gyakoriság követelménye alapján a rendszergazda által konfigurált feltételes hozzáférési házirendben.

2. példa

- 00:00-kor a felhasználók bejelentkeznek a Windows 10 Azure AD-hez csatlakoztatott eszközre, és megkezdik a munkát a SharePoint Online-on tárolt dokumentumon.
- A 00:30-es időpontban a felhasználó felveszi a felhasználót, és az eszköz zárolását megszakítja.
- A (z) 00:45-es verzióban a felhasználó visszatér a helyükről, és feloldja az eszköz zárolását.
- A (z) 01:45-es időpontban a felhasználónak a bejelentkezés gyakorisága alapján újra be kell jelentkeznie a rendszergazda által az utolsó bejelentkezés óta konfigurált feltételes hozzáférési szabályzatban, a 00:45-es verziónál.

## <a name="persistence-of-browsing-sessions"></a>Böngészési munkamenetek megőrzése

Az állandó böngésző-munkamenet lehetővé teszi a felhasználók számára, hogy a böngészőablak bezárása és újbóli megnyitása után is bejelentkezve maradjanak.

Az Azure AD alapértelmezett böngésző-munkamenetének megőrzése lehetővé teszi, hogy a személyes eszközökön lévő felhasználók kiválaszthatják, hogy a "Stay bejelentkezett?" kifejezéssel megmaradnak-e a munkamenetek. sikeres hitelesítés után Rákérdezés. Ha a böngésző adatmegőrzési funkciója be van állítva AD FS az [egyszeri bejelentkezési beállítások AD FS](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)cikk útmutatása alapján, akkor a szabályzatnak meg kell felelnie, és az Azure ad-munkamenetet is meg kell őriznie. Azt is beállíthatja, hogy a bérlő felhasználói lássák-e a "Stay bejelentkezve?" című részt. az [Azure ad bejelentkezési oldal testreszabása](../fundamentals/customize-branding.md)című cikk útmutatását követve megváltoztathatja a megfelelő beállítást a vállalati védjegyezés panelen Azure Portal.

## <a name="configuring-authentication-session-controls"></a>A hitelesítési munkamenet vezérlőinek konfigurálása

A feltételes hozzáférés prémium szintű Azure AD képesség, és prémium szintű licencre van szükség. Ha többet szeretne megtudni a feltételes hozzáférésről, tekintse meg a [Mi a feltételes hozzáférés Azure Active Directory?](overview.md#license-requirements) című témakört.

> [!WARNING]
> Ha a [konfigurálható jogkivonat élettartama](../develop/active-directory-configurable-token-lifetimes.md) funkciót jelenleg nyilvános előzetes verzióban használja, vegye figyelembe, hogy nem támogatott két különböző szabályzat létrehozása ugyanahhoz a felhasználóhoz vagy alkalmazás-kombinációhoz: egyet ezzel a szolgáltatással, és egy másik, konfigurálható jogkivonat-élettartam funkciót. A Microsoft azt tervezi, hogy a konfigurálható jogkivonat élettartama funkciót a 2020-as és a feltételes hozzáférési hitelesítési munkamenet-kezelési szolgáltatással helyettesíti.  

### <a name="policy-1-sign-in-frequency-control"></a>1. szabályzat: a bejelentkezés gyakoriságának szabályozása

1. Új szabályzat létrehozása
1. Válassza ki az ügyfél környezetének összes kötelező feltételét, beleértve a felhőalapú alkalmazásokat is.

   > [!NOTE]
   > A legjobb felhasználói élmény érdekében javasoljuk, hogy a kulcs Microsoft Office alkalmazások, például az Exchange Online és a SharePoint Online esetében az egyenlő hitelesítési kérések gyakoriságát adja meg.

1. Nyissa meg a **hozzáférés-vezérlési**  >  **munkamenetet** , és kattintson a **bejelentkezési gyakoriság** lehetőségre.
1. Adja meg az első szövegmezőben a napok és órák szükséges értékét
1. Válassza ki az **órák** vagy **napok** értékét a legördülő listából
1. Szabályzat mentése

![Bejelentkezési gyakoriságra konfigurált feltételes hozzáférési szabályzat](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

Az Azure AD-ben regisztrált Windows-eszközökön jelentkezzen be az eszközre. Ha például az Office-alkalmazások esetében 24 órán keresztül konfigurálta a bejelentkezési gyakoriságot, akkor az Azure AD-ben regisztrált Windows-eszközökön lévő felhasználók a bejelentkezési gyakorisági szabályzatot az eszközre való bejelentkezéssel fogják kielégíteni, és az Office-alkalmazások megnyitásakor nem fognak megjelenni.

### <a name="policy-2-persistent-browser-session"></a>2. szabályzat: állandó böngésző-munkamenet

1. Új szabályzat létrehozása
1. Válassza ki az összes szükséges feltételt.

   > [!NOTE]
   > Vegye figyelembe, hogy ehhez a vezérlőhöz a "minden felhőalapú alkalmazás" lehetőséget kell választania feltételként. A böngésző-munkamenet megőrzését a hitelesítési munkamenet tokenje vezérli. A böngésző-munkamenet összes lapja egyetlen munkamenet-jogkivonattal rendelkezik, ezért mindegyiknek meg kell egyeznie az adatmegőrzési állapottal.

1. Nyissa meg a **hozzáférés-vezérlési**  >  **munkamenetet** , és kattintson az **állandó böngésző munkamenet**
1. Válasszon ki egy értéket a legördülő listából
1. Szabályzat mentése

![Állandó böngészőre konfigurált feltételes hozzáférési szabályzat](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Az Azure AD feltételes hozzáférés állandó böngésző-munkamenet-konfigurációja felülírja a "Stay bejelentkezve?" Ha mindkét szabályzatot konfigurálta, akkor a Azure Portal vállalati védjegyezése ablaktáblán állítsa be ugyanezt a felhasználót.

## <a name="validation"></a>Érvényesítés

A What-if eszköz használatával szimulálhatja a felhasználót a célalkalmazás és más feltételek alapján, hogy a szabályzatot hogyan konfigurálta. A hitelesítési munkamenetek felügyeleti vezérlői az eszköz eredményében jelennek meg.

![Feltételes hozzáférés What If eszköz eredményei](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Szabályzat érvénybe léptetése

Annak ellenőrzéséhez, hogy a házirend a várt módon működik-e, az ajánlott eljárás az, hogy tesztelje azt az éles üzemben való működés előtt. Ideális esetben egy tesztelési bérlő használatával ellenőrizheti, hogy az új szabályzat a kívánt módon működik-e. További információkért tekintse meg a [Azure Active Directory a feltételes hozzáférésre vonatkozó ajánlott eljárásokat](best-practices.md)ismertető cikket.

## <a name="next-steps"></a>További lépések

* Ha tudni szeretné, hogyan kell konfigurálni a feltételes hozzáférési szabályzatot, tekintse meg a többtényezős hitelesítés [megkövetelése adott alkalmazásokhoz Azure Active Directory feltételes hozzáféréssel](../authentication/tutorial-enable-azure-mfa.md)című cikket.
* Ha készen áll a környezet feltételes hozzáférési házirendjeinek konfigurálására, tekintse meg az [ajánlott eljárásokat a feltételes hozzáféréshez Azure Active Directory](best-practices.md).