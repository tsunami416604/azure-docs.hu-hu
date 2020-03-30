---
title: Hitelesítési munkamenet-kezelés konfigurálása – Azure Active Directory
description: Az Azure AD hitelesítési munkamenet-konfigurációjának testreszabása, beleértve a felhasználói bejelentkezési gyakoriságot és a böngészőmunkamenet-megőrzést.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e9c0c88064c00c97de7dc58a500910e81c04eef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263282"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Hitelesítési munkamenet-kezelés konfigurálása feltételes hozzáféréssel

Összetett telepítések esetén előfordulhat, hogy a szervezeteknek korlátozniuk kell a hitelesítési munkameneteket. Egyes forgatókönyvek a következők lehetnek:

* Erőforrás-hozzáférés nem felügyelt vagy megosztott eszközről
* Bizalmas információk elérése külső hálózatról
* Nagy hatású felhasználók
* Kritikus üzleti alkalmazások

A feltételes hozzáférés-vezérlők lehetővé teszik olyan házirendek létrehozását, amelyek a szervezeten belüli konkrét használati eseteket célozzák meg anélkül, hogy az összes felhasználót érintené.

Mielőtt belemerülne a házirend konfigurálásának részleteibe, vizsgáljuk meg az alapértelmezett konfigurációt.

## <a name="user-sign-in-frequency"></a>A felhasználó bejelentkezési gyakorisága

A bejelentkezési gyakoriság határozza meg azt az időszakot, amely alatt a felhasználónak újra be kell jelentkeznie egy erőforrás elérésekor.

Az Azure Active Directory (Azure AD) alapértelmezett konfigurációja a felhasználói bejelentkezési gyakoriság egy 90 napos gördülő ablak. A felhasználók hitelesítő adatok kérése gyakran ésszerű dolognak tűnik, de visszafelé sülhet el: azok a felhasználók, akik gondolkodás nélkül adják meg hitelesítő adataikat, véletlenül rosszindulatú hitelesítő adatokat adhatnak meg.

Riasztóan hangzik, ha nem kéri a felhasználót, hogy jelentkezzen be újra, a valóságban az informatikai házirendek megsértése visszavonja a munkamenetet. Néhány példa (de nem kizárólagosan) a jelszó módosítása, egy nem megfelelő eszköz, vagy a fiók letiltása. A PowerShell használatával is visszavonhatja a [felhasználói munkameneteket.](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) Az Azure AD alapértelmezett konfiguráció jön le, hogy "ne kérje meg a felhasználókat, hogy a hitelesítő adatokat, ha a biztonsági állapotát a munkamenetek nem változott".

A bejelentkezési gyakoriság beállítása olyan alkalmazásokkal működik, amelyek a szabványoknak megfelelően implementálták az OAUTH2 vagy OIDC protokollokat. A legtöbb Microsoft natív Windows, Mac és Mobile alkalmazás, beleértve a következő webalkalmazásokat is, megfelel a beállításnak.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- O365 Felügyeleti portál
- Exchange Online
- SharePoint és OneDrive
- Teams webes ügyfél
- Dynamics CRM Online
- Azure portál

### <a name="user-sign-in-frequency-and-device-identities"></a>A felhasználó bejelentkezési gyakorisága és az eszközidentitások

Ha az Azure AD-hez csatlakozott, hibrid Azure AD-hez csatlakozott, vagy az Azure AD regisztrált eszközök, amikor a felhasználó feloldja az eszköz vagy jelek interaktívan, ez az esemény megfelel a bejelentkezési gyakorisági szabályzat is. A következő 2 példában a felhasználói bejelentkezési gyakoriság 1 órára van beállítva:

1. példa:

- 00:00-kor egy felhasználó bejelentkezik a Windows 10 Azure AD-hez csatlakozott eszközére, és megkezdi a munkát a SharePoint Online-on tárolt dokumentumon.
- A felhasználó egy órán keresztül folytatja a munkát ugyanazon a dokumentumon az eszközén.
- 01:00-kor a rendszer kéri, hogy a rendszergazda által konfigurált feltételes hozzáférési házirendben a bejelentkezési gyakoriságkövetelmény alapján jelentkezzen be újra.

2. példa

- 00:00-kor egy felhasználó bejelentkezik a Windows 10 Azure AD-hez csatlakozott eszközére, és megkezdi a munkát a SharePoint Online-on tárolt dokumentumon.
- 00:30-kor a felhasználó felkel, és szünetet tart, és zárolja az eszközt.
- 00:45-kor a felhasználó visszatér a szünetből, és feloldja a készülékzárolást.
- 01:45-kor a rendszer kéri a felhasználót, hogy jelentkezzen be újra a feltételes hozzáférési házirendben a rendszergazda által a 00:45-kor történt utolsó bejelentkezés óta konfigurált feltételes hozzáférési házirend bejelentkezési gyakorisági követelménye alapján.

## <a name="persistence-of-browsing-sessions"></a>A böngészési munkamenetek megőrzése

Az állandó böngészőmunkamenet lehetővé teszi a felhasználók számára, hogy a böngészőablak bezárása és újbóli megnyitása után is bejelentkezve maradjanak.

A böngészőmunkamenet-megőrzés azure AD alapértelmezett alapértelmezett szolgáltatása lehetővé teszi a személyes eszközökön lévő felhasználók számára, hogy a "Maradjon bejelentkezve?" a sikeres hitelesítés után. Ha a böngésző adatmegőrzése van konfigurálva az AD FS az [AD FS egyszeri bejelentkezési beállítások](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)című cikkben található útmutatás t, akkor betartjuk ezt a szabályzatot, és megmarad az Azure AD-munkamenet is. Azt is beállíthatja, hogy a bérlőfelhasználói lássák-e a "Maradjon bejelentkezve?" a megfelelő beállítás módosításával az Azure Portal vállalati márkajelzési ablaktáblájában az [Azure AD bejelentkezési oldal testreszabása című cikkben](../fundamentals/customize-branding.md)található útmutatás használatával.

## <a name="configuring-authentication-session-controls"></a>Hitelesítési munkamenet-vezérlők konfigurálása

Feltételes hozzáférés egy Azure AD Premium-képesség, és prémium szintű licencet igényel. Ha szeretne többet megtudni a feltételes hozzáférés, olvassa el [a Mi a feltételes hozzáférés az Azure Active Directoryban?](overview.md#license-requirements)

> [!WARNING]
> Ha a jelenleg nyilvános előzetes verzióban [elérhető, konfigurálható jogkivonat-élettartam](../develop/active-directory-configurable-token-lifetimes.md) funkciót használja, kérjük, vegye figyelembe, hogy nem támogatjuk két különböző szabályzat létrehozását ugyanahhoz a felhasználóhoz vagy alkalmazáskombinációhoz: az egyik ezzel a funkcióval, a másik pedig konfigurálható jogkivonat-élettartam-funkcióval rendelkezik. A Microsoft azt tervezi, hogy 2020.  

### <a name="policy-1-sign-in-frequency-control"></a>1. házirend: Bejelentkezési gyakoriság-szabályozás

1. Új házirend létrehozása
1. Válassza ki az ügyfél környezetéhez szükséges összes feltételt, beleértve a célfelhőalkalmazásokat is.

   > [!NOTE]
   > A legjobb felhasználói élmény érdekében ajánlott a legfontosabb Microsoft Office-alkalmazások, például az Exchange Online és a SharePoint Online azonos hitelesítési gyorsgyakoriságának beállítása.

1. Nyissa meg a **Hozzáférés-vezérlési** > **munkamenetet,** és kattintson **a Bejelentkezési gyakoriság ra**
1. Adja meg a napok és órák kötelező értékét az első szövegmezőbe
1. **Legördülő** menü Óra vagy **Napok** értékének kiválasztása
1. A házirend mentése

![A bejelentkezési gyakoriságra konfigurált feltételes hozzáférési házirend](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

Az Azure AD regisztrált Windows-eszközök jelentkezzen be az eszközre egy kérdés. Ha például a Bejelentkezési gyakoriságot 24 órára állította be az Office-alkalmazások hoz, az Azure AD-ben regisztrált Windows-eszközök felhasználói az eszközre való bejelentkezéskor megfelelnek a Bejelentkezésgyakoriságra vonatkozó szabályzatnak, és az Office-alkalmazások megnyitásakor nem jelennek meg újra.

Ha különböző bejelentkezési gyakoriságot konfigurált az ugyanabban a böngészőmunkamenetben futó különböző webalkalmazásokhoz, a legszigorúbb házirend mindkét alkalmazásra vonatkozik, mivel az ugyanabban a böngészőmunkamenetben futó összes alkalmazás egyetlen munkamenet-tokenen osztozik.

### <a name="policy-2-persistent-browser-session"></a>2. házirend: Állandó böngészőmunkamenet

1. Új házirend létrehozása
1. Válassza ki az összes szükséges feltételt.

   > [!NOTE]
   > Kérjük, vegye figyelembe, hogy ez a vezérlő feltételként az "Összes felhőalkalmazás" lehetőséget kell választania. A böngészőmunkamenet-megőrzést hitelesítési munkamenet-jogkivonat szabályozza. A böngészőmunkamenet ben lévő összes lap egyetlen munkamenet-jogkivonatot oszt meg, ezért mindegyiknek meg kell osztania a perzisztencia-állapotot.

1. Nyissa meg az **Access Controls** > **munkamenetet,** és kattintson **az Állandó böngészőmunkamenet** re
1. Érték kiválasztása a legördülő menüből
1. A házirend mentése

![Állandó böngészőhöz konfigurált feltételes hozzáférési házirend](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Az Azure AD feltételes hozzáférésének állandó böngészőmunkamenet-konfigurációja felülírja a "Maradjon bejelentkezve?" beállítást az Azure Portalon a vállalati márkajelzési ablaktáblában ugyanahhoz a felhasználóhoz, ha mindkét szabályzatot konfigurálta.

## <a name="validation"></a>Ellenőrzés

A Teendő eszközzel szimulálhatja a felhasználó bejelentkezését a célalkalmazásba, valamint egyéb feltételeket a házirend konfigurálásának módjától függően. A hitelesítési munkamenet-kezelési vezérlők megjelennek az eszköz eredményében.

![Feltételes hozzáférés mi után az eszköz eredményei](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Szabályzat érvénybe léptetése

Annak érdekében, hogy a szabályzat a várt módon működjön, ajánlott eljárás, hogy tesztelje, mielőtt éles környezetben. Ideális esetben egy tesztbérlő segítségével ellenőrizze, hogy az új szabályzat megfelelően működik-e. További információt az Azure Active Directory feltételes hozzáféréssel kapcsolatos gyakorlati tanácsok című [témakörben talál.](best-practices.md)

## <a name="next-steps"></a>További lépések

* Ha tudni szeretné, hogyan konfigurálhat feltételes hozzáférési szabályzatot, olvassa el az [Azure Active Directory feltételes hozzáféréssel rendelkező alkalmazásokhoz az MFA megkövetelése című cikket.](app-based-mfa.md)
* Ha készen áll a feltételes hozzáférési házirendek konfigurálására a környezetéhez, olvassa el a feltételes hozzáférés ajánlott eljárásai az Azure Active Directoryban című [témakört.](best-practices.md)
