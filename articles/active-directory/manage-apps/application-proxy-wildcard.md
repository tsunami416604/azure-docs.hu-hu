---
title: Az Azure Active Directory application proxy a helyettesítő karaktereket tartalmazó alkalmazások |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure Active Directory application proxy helyettesítő karaktereket tartalmazó alkalmazások.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 8c876f220cde99bbeb3b5d9f8f8878acb5584802
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140047"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Az Azure Active Directory application proxy a helyettesítő karaktereket tartalmazó alkalmazások 

Az Azure Active Directoryban (Azure AD), a helyszíni nagy számú konfigurálása az alkalmazások gyorsan Kezelhetetlen válhat, és konfigurációs hibák szükségtelen kockázatok vezet be, ha ezek közül számos ugyanazokat a beállításokat. A [Azure AD-alkalmazásproxy](application-proxy.md), tehet közzé és kezelhet egyszerre sok alkalmazásokat helyettesítő karaktert tartalmazó alkalmazás-közzététel segítségével meg lehet oldani a problémát. Ez a megoldás, amely lehetővé teszi, hogy:

-   A felügyelettel járó többletterhelést egyszerűsítése
-   A potenciális konfigurációs hibák számának csökkentése
-   A felhasználók biztonságosan hozzáférhessenek a további erőforrások

Ez a cikk a helyettesítő karaktert tartalmazó alkalmazás-közzététel konfigurálása a környezetében szükséges információkat biztosít.


## <a name="create-a-wildcard-application"></a>Helyettesítő karaktert tartalmazó alkalmazás létrehozása 

Egy helyettesítő karakter (*) alkalmazást létrehozhatja úgy is, ha ugyanazt a konfigurációt az alkalmazások egy csoportja. A helyettesítő karaktereket tartalmazó alkalmazások esetében lehetséges alternatívák: alkalmazások megosztása a következő beállításokat:

- Azok a felhasználók csoportját
- Az egyszeri bejelentkezés módszer
- A hozzáférési protokoll (http, https)

Helyettesítő karaktereket is tartalmazó alkalmazások teheti közzé, ha mind a belső és külső URL-címeket a következő formátumban:

> http(s)://*.\<domain\>

Például: `http(s)://*.adventure-works.com`. A belső és külső URL-címeket használhatja a különböző tartományokban, ajánlott eljárásként, amíg azok azonosnak kell lennie. Ha az alkalmazás közzététele hibaüzenet jelenik meg, ha egy helyettesítő karaktert tartalmazó URL-címek egyike nem rendelkezik.

Ha további alkalmazásokat különböző konfigurációs beállításokkal rendelkezik, közzé kell tennie az ilyen kivételek külön alkalmazásokként felülírja az alapértelmezett értékeket beállítani a helyettesítő karaktert. Helyettesítő karakter nélküli alkalmazások mindig elsőbbséget élveznek helyettesítő karaktereket tartalmazó alkalmazások. Konfigurációs szempontjából ezek a "csak" a szokásos alkalmazások.

Egy helyettesítő karaktert tartalmazó alkalmazás létrehozása alapján azonos [alkalmazás közzétételi folyamat](application-proxy-add-on-premises-application.md) elérhető minden más alkalmazás. Az egyetlen különbség, hogy tartalmazza-e helyettesítő karakterként az URL-címeket, és az egyszeri bejelentkezés konfigurálása.


## <a name="prerequisites"></a>Előfeltételek

### <a name="custom-domains"></a>Egyéni tartományok

Miközben [egyéni tartományok](application-proxy-configure-custom-domain.md) vannak helyettesítő karaktereket tartalmazó alkalmazások előfeltétel azok minden más alkalmazás nem kötelező. Egyéni tartományok létrehozása szükséges, hogy:

1. Hozzon létre egy ellenőrzött tartomány Azure-ban 
2. Töltse fel az application proxy SSL-tanúsítvány PFX formátumban.

Érdemes lehet egy helyettesítő tanúsítványt használja az alkalmazás azt tervezi, hogy hozzon létre megfelelő. Másik lehetőségként használhatja egy tanúsítványt, amely csak megjeleníti az adott alkalmazásokat. Csak azokat az alkalmazásokat, a tanúsítvány szerepel ebben az esetben a helyettesítő karaktereket tartalmazó alkalmazásokká keresztül elérhető lesz.

Biztonsági okokból ez egy szigorú követelmény és helyettesítő karaktereket nem támogatjuk az alkalmazásokat, amelyek a külső URL-cím nem használható egy egyéni tartományt.

### <a name="dns-updates"></a>DNS-frissítések

Egyéni tartományok használata esetén szüksége egy CNAME rekordot a DNS-bejegyzés létrehozása a külső URL-cím (például `*.adventure-works.com`) a külső végpont URL-címét az application proxy mutat. Helyettesítő karaktereket tartalmazó alkalmazások esetében a CNAME rekordot kell mutatnia a megfelelő külső URL-címek:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Győződjön meg arról, hogy megfelelően konfigurálta a CNAME, használhatja a [nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) a cél végpontok például egyik `expenses.adventure-works.com`.  A válasz tartalmaznia kell a már említett alias (`<yourAADTenantId>.tenant.runtime.msappproxy.net`).


## <a name="considerations"></a>Megfontolandó szempontok


### <a name="accepted-formats"></a>Elfogadott formátumok

Helyettesítő karaktereket tartalmazó alkalmazások esetében a **belső URL-cím** formátumban kell lenniük `http(s)://*.<domain>`. 

![AppId](./media/application-proxy-wildcard/22.png)


Konfigurálása során egy **külső URL-cím**, a következő formátumot kell használnia: `https://*.<custom domain>` 

![AppId](./media/application-proxy-wildcard/21.png)

Más esetekben a helyettesítő karaktert, több helyettesítő karaktereket vagy más regex karakterláncok nem támogatottak, és hibák okozzák.


### <a name="excluding-applications-from-the-wildcard"></a>A helyettesítő karaktereket tartalmazó alkalmazások kizárása

Egy alkalmazás kizárása a helyettesítő karaktereket tartalmazó alkalmazásokká szerint

- Az alkalmazás rendszeres kivétel alkalmazás közzététele 
- A helyettesítő karakter, csak az adott alkalmazások a DNS-beállítások engedélyezése  


Rendszeres alkalmazásként alkalmazás közzététele a kizárandó egy alkalmazás egy helyettesítő karaktert tartalmazó előnyben részesített módszer. Tegyen közzé a kizárt alkalmazást, mielőtt a helyettesítő karaktereket tartalmazó alkalmazások annak érdekében, hogy a kivételek az elejétől is érvényben vannak. A legtöbb adott alkalmazás mindig elsőbbséget – közzétett alkalmazás `budgets.finance.adventure-works.com` elsőbbséget élvez az alkalmazás `*.finance.adventure-works.com`, ami viszont elsőbbséget élvez az alkalmazás `*.adventure-works.com`. 

A helyettesítő karaktert csak a megfelelő alkalmazások a DNS-management szolgáltatáson keresztül működik is korlátozhatja. Ajánlott eljárásként hozzon létre egy CNAME-bejegyzést, amely egy helyettesítő karaktert tartalmaz, és megfelel a konfigurált külső URL-cím formátuma. Adott alkalmazás URL-címek azonban a helyettesítő karakterek inkább mutat. Például, nem pedig `*.adventure-works.com`, pont `hr.adventure-works.com`, `expenses.adventure-works.com` és `travel.adventure-works.com individually` való `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`. 

Ha ezt a beállítást használja, is szüksége lesz egy másik CNAME bejegyzés értéke `AppId.domain`, például `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`, ugyanarra a helyre is mutat. Annak a **AppId** az alkalmazás Tulajdonságok lapján a helyettesítő karaktereket tartalmazó alkalmazásokká:

![AppId](./media/application-proxy-wildcard/01.png)



### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>A MyApps panel kezdőlap URL-Címének beállítása

A helyettesítő karaktereket tartalmazó alkalmazásokká jelölt a csak egy csempe az [MyApps panel](https://myapps.microsoft.com). Alapértelmezés szerint ez a csempe eltűnik. A csempe megjelenítése, és rendelkezik az adott oldalon található felhasználók föld:

1. Útmutatás [kezdőlap URL-cím beállítása](application-proxy-configure-custom-home-page.md).
2. Állítsa be **alkalmazás megjelenítése** való **igaz** az alkalmazás Tulajdonságok oldalán található.

### <a name="kerberos-constrained-delegation"></a>Kerberos által korlátozott delegálás

Használó alkalmazások számára [kerberos általi korlátozott delegálás (KCD) egyszeri bejelentkezési módszerként](application-proxy-configure-single-sign-on-with-kcd.md), az egyszerű szolgáltatásnév számára az egyszeri bejelentkezés módszer is szükség lehet a helyettesítő karakter szerepel. Lehet például az egyszerű szolgáltatásnév: `HTTP/*.adventure-works.com`. Továbbra is kell rendelkeznie az egyes SPN-ek a háttérkiszolgálókhoz konfigurált (például `http://expenses.adventure-works.com and HTTP/travel.adventure-works.com`).



## <a name="scenario-1-general-wildcard-application"></a>1. forgatókönyv: Általános helyettesítő karaktereket tartalmazó alkalmazásokká

Ebben a forgatókönyvben három különböző alkalmazás közzétenni kívánt rendelkezik:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Mindhárom alkalmazás:

- Minden felhasználó által használt
- Használat *integrált Windows-hitelesítés* 
- Az azonos tulajdonságokkal rendelkezik.


A helyettesítő karaktereket tartalmazó alkalmazásokká, az ismertetett lépéseket követve közzéteheti [alkalmazások közzététele az Azure AD-alkalmazásproxy](application-proxy-add-on-premises-application.md). Ez a forgatókönyv feltételezi, hogy:

- A következő Azonosítóval rendelkező bérlő: `000aa000-11b1-2ccc-d333-4444eee4444e` 

- Egy ellenőrzött tartomány nevű `adventure-works.com` lett konfigurálva.

- A **CNAME** mutató bejegyzés `*.adventure-works.com` való `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` létrejött.

A következő a [lépéseket dokumentált](application-proxy-add-on-premises-application.md), egy új application proxy-alkalmazást hoz létre a bérlőben. Ebben a példában a helyettesítő karakter szerepel a következő mezőket:

- Belső URL-címe:

    ![Belső URL-cím](./media/application-proxy-wildcard/42.png)


- Külső URL-címe:

    ![Külső URL-cím](./media/application-proxy-wildcard/43.png)

 
- Belső alkalmazás egyszerű Szolgáltatásneve: 

    ![Egyszerű szolgáltatásnév konfigurációja](./media/application-proxy-wildcard/44.png)


Tegye közzé a helyettesítő karaktereket tartalmazó alkalmazásokká, mostantól elérheti a három alkalmazásait az Ön használt URL-címek (például `travel.adventure-works.com`).

A konfiguráció az alábbi struktúrával valósít meg:

![AppId](./media/application-proxy-wildcard/05.png)

| Szín | Leírás |
| ---   | ---         |
| Kék  | Alkalmazások explicit módon lett közzétéve és az Azure Portalon látható. |
| Szürke  | Alkalmazások az alkalmazás-n keresztül elérhető is. |




## <a name="scenario-2-general-wildcard-application-with-exception"></a>2. forgatókönyv: Általános helyettesítő karaktereket tartalmazó alkalmazásokká kivétel

Ebben a forgatókönyvben rendelkezik emellett három általános alkalmazásokat egy másik alkalmazás `finance.adventure-works.com`, amely csak akkor érhető el a pénzügyi részleg. Az aktuális alkalmazás struktúrával a pénzügyi alkalmazás elérhető-e a helyettesítő karaktereket tartalmazó alkalmazásokká keresztül, és amelyet minden alkalmazott lenne. Ennek módosításához, zárja ki az alkalmazás a helyettesítő karakteres külön alkalmazás korlátozóbb engedélyekkel pénzügyi konfigurálásával.



Győződjön meg arról, hogy egy CNAME-rekordok léteznek-e a pontok kell `finance.adventure-works.com` az alkalmazásproxy oldalon az alkalmazás a megadott alkalmazás adott végpontnak. Ebben a forgatókönyvben `finance.adventure-works.com` mutat `https://finance-awcycles.msappproxy.net/`. 

A következő a [lépéseket dokumentált](application-proxy-add-on-premises-application.md), ehhez a forgatókönyvhöz szükséges a következő beállításokat:


- Az a **belső URL-cím**, beállított **pénzügyi** helyettesítő karakter helyett. 

    ![Belső URL-cím](./media/application-proxy-wildcard/52.png)

- Az a **külső URL-cím**, beállított **pénzügyi** helyettesítő karakter helyett. 

    ![Külső URL-cím](./media/application-proxy-wildcard/53.png)

- Belső alkalmazás egyszerű Szolgáltatásnevét beállított **pénzügyi** helyettesítő karakter helyett.

    ![Egyszerű szolgáltatásnév konfigurációja](./media/application-proxy-wildcard/54.png)


Ezzel a konfigurációval megvalósul az alábbi forgatókönyvet:

![Forgatókönyv](./media/application-proxy-wildcard/09.png)

Mivel `finance.adventure-works.com` pontosabban meghatározott URL-cím, mint `*.adventure-works.com`, akkor lép érvénybe. A felhasználók részen `finance.adventure-works.com` a a pénzügyi források alkalmazásban meghatározott felhasználói élményt. Ebben az esetben csak a pénzügyi dolgozókra tudnak hozzáférni `finance.adventure-works.com`.

Ha több alkalmazást is közzé a pénzügyi, és rendelkezik `finance.adventure-works.com` ellenőrzött tartományt, mint egy másik helyettesítő karaktereket tartalmazó alkalmazásokká közzététele sikerült `*.finance.adventure-works.com`. Mivel ez pontosabb, mint az általános `*.adventure-works.com`, akkor lép érvénybe, ha egy felhasználó hozzáfér az alkalmazáshoz a pénzügyi tartományban.


## <a name="next-steps"></a>További lépések

További információ:

- **Egyéni tartományok**, lásd: [egyéni tartományok használata az Azure AD-alkalmazásproxy](application-proxy-configure-custom-domain.md).

- **Alkalmazás-közzététel**, lásd: [alkalmazások közzététele az Azure AD-alkalmazásproxy használatával](application-proxy-add-on-premises-application.md)


