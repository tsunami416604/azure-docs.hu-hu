---
title: Helyettesítő alkalmazásokat az Azure Active Directory alkalmazásproxyt |} Microsoft Docs
description: Megtudhatja, hogyan használja a helyettesítő alkalmazásokat az Azure Active Directory alkalmazásproxyjával.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: a5466a0b5027fa8fe0edec3c9663e6cd1aafc9a3
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Helyettesítő alkalmazásokat az Azure Active Directory alkalmazásproxyt 

Az Azure Active Directoryban (Azure AD) konfigurálása a helyszíni nagy számú alkalmazások Kezelhetetlen vehet, és szükségtelen kockázatok konfigurációja vezet be, ha ezek szükséges, hogy ugyanazokat a beállításokat. A [az Azure AD-alkalmazásproxy](manage-apps/application-proxy.md), helyettesítő alkalmazás közzététele használatával közzétehet és kezelhet egyszerre több alkalmazás meg lehet oldani a probléma. Ez az olyan megoldás, amely lehetővé teszi:

-   A felügyelettel járó többletterhelést egyszerűsítése
-   Adjon meg kevesebb lehetséges hibái
-   Lehetővé teszi a felhasználók férjenek hozzá biztonságosan további erőforrások

Ez a cikk azt ismerteti a kell konfigurálnia a helyettesítő karakteres alkalmazás közzététele a környezetben.


## <a name="create-a-wildcard-application"></a>Helyettesítő karakteres-alkalmazás létrehozása 

Létrehozhat egy helyettesítő karakter (*) alkalmazást, ha az azonos konfigurációjú alkalmazások azon csoportját. Egy helyettesítő karakteres alkalmazáshoz lehetséges alternatívák: alkalmazások megosztását a következő beállításokat:

- Azok a felhasználók csoportja
- Az egyszeri bejelentkezés módszer
- A hozzáférési protokoll (http, https)

Helyettesítő karakterek az alkalmazások közzététele esetén is, a belső és külső URL-címeket a következő formátumban:

> http(s)://*.\<domain\>

Például: `http(s)://*.adventure-works.com`. A belső és külső URL-címeket használhatja a eltérő tartományokban, az ajánlott eljárás, amíg azok azonosnak kell lennie. Ha az alkalmazás közzététele, hibaüzenet jelenik meg, ha az URL-címek egyikét nem tartalmaz helyettesítő karakter.

Ha további alkalmazások különböző konfigurációs beállítások, közzé kell tennie az ilyen kivételek külön alkalmazások felülírja az alapértelmezett beállítása a helyettesítő karaktert. Helyettesítő karakter nélkül alkalmazások mindig elsőbbséget élveznek helyettesítő alkalmazásokat. A konfigurációs szempontjából ezek a "csak" rendszeres alkalmazások.

Egy helyettesítő karakteres alkalmazás létrehozása alapuló azonos [alkalmazás közzétételi folyamat](application-proxy-publish-azure-portal.md) , amely érhető el minden más alkalmazást. Az egyetlen különbség az, hogy az URL-címeket, és esetleg az SSO konfigurációs helyettesítő tartalmazza.


## <a name="prerequisites"></a>Előfeltételek

### <a name="custom-domains"></a>Egyéni tartományok

Amíg [egyéni tartományok](active-directory-application-proxy-custom-domains.md) vannak más alkalmazások esetén nem kötelező,-e helyettesítő alkalmazások előfeltétele. Egyéni tartományok létrehozásáról szükséges, hogy:

1. Hozzon létre egy ellenőrzött tartomány Azure-ban 
2. Töltse fel az alkalmazásproxy SSL-tanúsítvány PFX formátumban.

Érdemes lehet létrehozni az alkalmazás megfelelő helyettesítő tanúsítványt használ. Másik megoldásként használhatja olyan tanúsítvány, amely csak megjeleníti az adott alkalmazásokat. Ebben az esetben csak a tanúsítványban feltüntetett alkalmazások lesznek elérhetők a helyettesítő karakteres alkalmazáson keresztül.

Biztonsági okokból ez feltétele rögzített, és a helyettesítő karakterek nem támogatjuk az alkalmazásokat, amelyek nem használják az egyéni tartománynév a külső URL-címhez.

### <a name="dns-updates"></a>DNS-frissítések

Egyéni tartományok használata esetén kell egy olyan CNAME rekordot a DNS-bejegyzés létrehozása a külső URL-CÍMÉT (például `*.adventure-works.com`) a külső URL-CÍMÉT a szolgáltatásalkalmazási proxy végpont mutat. A helyettesítő alkalmazásokat a CNAME rekordot kell mutatnia a megfelelő külső URL-címek:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Győződjön meg arról, hogy megfelelően konfigurálta a CNAME REKORDOT, használja a [nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) a cél végpontok, például egyik `expenses.adventure-works.com`.  A válasz tartalmaznia kell a már említett alias (`<yourAADTenantId>.tenant.runtime.msappproxy.net`).


## <a name="considerations"></a>Megfontolandó szempontok


### <a name="accepted-formats"></a>Elfogadott formátumok

A helyettesítő alkalmazásokat az **belső URL-cím** kell formázni `http(s)://*.<domain>`. 

![AppId](./media/active-directory-application-proxy-wildcard\22.png)


Amikor konfigurál egy **külső URL-cím**, a következő formátumot kell használnia: `https://*.<custom domain>` 

![AppId](./media/active-directory-application-proxy-wildcard\21.png)

Más esetekben a helyettesítő karaktert, több helyettesítő karaktereket vagy más regex karakterláncok nem támogatottak, és hibák okozzák.


### <a name="excluding-applications-from-the-wildcard"></a>A helyettesítő alkalmazásokat kizárása

Kizárhat az alkalmazás által helyettesítő alkalmazásból

- A kivétel alkalmazás rendszeres alkalmazás közzététele 
- A helyettesítő karakter csak a megfelelő alkalmazások keresztül a DNS-beállítások engedélyezése  


Rendszeres alkalmazásként alkalmazás közzététele legyen az előnyben részesített módszere szeretné kizárni az alkalmazás a helyettesítő karakter. A kizárt alkalmazások előtt győződjön meg arról, hogy a kivételek az elejétől érvényesíti a helyettesítő alkalmazásokat kell közzé tenni. A legtöbb alkalmazásspecifikus mindig elsőbbséget – közzétett alkalmazás `budgets.finance.adventure-works.com` elsőbbséget élvez az alkalmazás `*.finance.adventure-works.com`, ami viszont elsőbbséget élvez az alkalmazás `*.adventure-works.com`. 

A helyettesítő karakter csak a DNS-kezelési keresztül adott alkalmazásokra vonatkozó működéséhez is korlátozható. Ajánlott eljárásként hozzon létre egy CNAME-bejegyzést, amely egy helyettesítő karaktert tartalmaz, és megfelel a konfigurált külső URL-cím formátuma. Adott alkalmazás URL-címek, a helyettesítő karakterek helyette mutasson. Például ahelyett, hogy `*.adventure-works.com`, pont `hr.adventure-works.com`, `expenses.adventure-works.com` és `travel.adventure-works.com individually` való `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`. 

Ezt a beállítást használja, is szükség van egy másik CNAME bejegyzés érték `AppId.domain`, például `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`, is a ugyanazon a helyen. Megtalálhatja az **AppId** a helyettesítő karakteres alkalmazás a kérelem tulajdonságok lapján:

![AppId](./media/active-directory-application-proxy-wildcard\01.png)



### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>A kezdőlap URL-cím a MyApps panel beállításakor

A helyettesítő karakteres alkalmazás szerepel a csak egy csempe az a [MyApps panel](https://myapps.microsoft.com). Alapértelmezés szerint ez a csempe rejtett. A csempe megjelenítése és felhasználók föld egy adott oldalon:

1. Útmutatás [a kezdőlap URL-cím beállításakor](application-proxy-office365-app-launcher.md).
2. Állítsa be **megjelenítése alkalmazás** való **igaz** alkalmazás Tulajdonságok lapján találhat.

### <a name="kerberos-constrained-delegation"></a>Kerberos által korlátozott delegálás

Használó alkalmazások esetében [kerberos által korlátozott delegálás (KCD) a egyszeri bejelentkezési módszerként](active-directory-application-proxy-sso-using-kcd.md), az egyszerű szolgáltatásnév felsorolt számára az egyszeri bejelentkezés módszer is szükség lehet a helyettesítő karakter. Például az egyszerű szolgáltatásnév lehet: `HTTP/*.adventure-works.com`. Továbbra is kell rendelkeznie a háttérkiszolgálókhoz konfigurált egyes SPN-ek (például `http://expenses.adventure-works.com and HTTP/travel.adventure-works.com`).



## <a name="scenario-1-general-wildcard-application"></a>1. forgatókönyv: Általános helyettesítő alkalmazás

Ebben a forgatókönyvben a közzétenni kívánt három különböző alkalmazás van:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Mindhárom alkalmazás:

- A felhasználók által használt
- Használjon *integrált Windows-hitelesítés* 
- Az azonos jellemzőkkel rendelkezik


A az itt leírt lépésekkel helyettesítő alkalmazást is közzétehet [alkalmazások közzététele az Azure AD-alkalmazásproxy használatával](application-proxy-publish-azure-portal.md). Ez a forgatókönyv azt feltételezi, hogy:

- A következő Azonosítóval rendelkező bérlő: `000aa000-11b1-2ccc-d333-4444eee4444e` 

- Egy ellenőrzött tartomány nevű `adventure-works.com` van konfigurálva.

- A **CNAME** bejegyzést, amely a `*.adventure-works.com` való `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` létrejött.

A következő a [lépéseket dokumentált](application-proxy-publish-azure-portal.md), létrehozhat egy új application proxy alkalmazás az Ön bérelt szolgáltatásának. Ebben a példában a helyettesítő karakter lehet, a következő területeken:

- Belső URL-címe:

    ![Belső URL-cím](./media/active-directory-application-proxy-wildcard\42.png)


- Külső URL-címe:

    ![Külső URL-címe](./media/active-directory-application-proxy-wildcard\43.png)

 
- Belső alkalmazás egyszerű szolgáltatásnév: 

    ![SPN-konfiguráció](./media/active-directory-application-proxy-wildcard\44.png)


A helyettesítő karakteres alkalmazás közzététele által érhetők el a három alkalmazások nyissa meg az URL-címek biztos használt (például `travel.adventure-works.com`).

A konfigurációs valósítja meg az alábbi szerkezettel:

![AppId](./media/active-directory-application-proxy-wildcard\05.png)

| Szín | Leírás |
| ---   | ---         |
| Kék  | Alkalmazások explicit módon közzétéve és az Azure portálon. |
| Szürke  | Alkalmazások beállíthatja az alkalmazás keresztül érhető el. |




## <a name="scenario-2-general-wildcard-application-with-exception"></a>2. forgatókönyv: Általános helyettesítő alkalmazás kivétel miatt:

Ebben a forgatókönyvben kell továbbá a három általános alkalmazás egy másik alkalmazás `finance.adventure-works.com`, amely csak legyen elérhető a pénzügyi részleg. Az aktuális alkalmazás struktúrával a pénzügyi alkalmazás érhető el, a helyettesítő karakteres alkalmazáson keresztül, és amelyet minden alkalmazott lenne. Ennek módosításához kizárni az alkalmazás a helyettesítő pénzügyi szigorúbb engedélyekkel külön alkalmazás konfigurálásával.



Meg kell győződnie arról, hogy egy CNAME-rekordok megtalálhatók-e az adott pontok `finance.adventure-works.com` az alkalmazásproxy oldalon az alkalmazáshoz megadott alkalmazás adott végpontra. Ebben a forgatókönyvben `finance.adventure-works.com` mutat `https://finance-awcycles.msappproxy.net/`. 

A következő a [lépéseket dokumentált](application-proxy-publish-azure-portal.md), ebben a forgatókönyvben igényel a következő beállításokat:


- Az a **belső URL-cím**, beállított **pénzügyi** helyettesítő karakter helyett. 

    ![Belső URL-cím](./media/active-directory-application-proxy-wildcard\52.png)

- Az a **külső URL-cím**, beállított **pénzügyi** helyettesítő karakter helyett. 

    ![Külső URL-címe](./media/active-directory-application-proxy-wildcard\53.png)

- Belső alkalmazás SPN beállított **pénzügyi** helyettesítő karakter helyett.

    ![SPN-konfiguráció](./media/active-directory-application-proxy-wildcard\54.png)


Ez a konfiguráció valósítja meg a következő forgatókönyvet:

![Forgatókönyv](./media/active-directory-application-proxy-wildcard\09.png)

Mivel `finance.adventure-works.com` mint pontosabb URL-cím `*.adventure-works.com`, akkor lép érvénybe. Meglátogató felhasználók `finance.adventure-works.com` pénzügyi erőforrások kérelemben tapasztalattal rendelkeznie. Ebben az esetben csak a pénzügyi alkalmazottak elérő eszköztípusokra `finance.adventure-works.com`.

Ha több alkalmazást is pénzügyi közzétéve, és rendelkezik `finance.adventure-works.com` ellenőrzött tartományt, mint egy másik helyettesítő alkalmazás sikerült közzétenni `*.finance.adventure-works.com`. Mivel ez pontosabb, mint az általános `*.adventure-works.com`, akkor lép érvénybe, ha egy felhasználó hozzáfér egy alkalmazás a pénzügyi tartományban.


## <a name="next-steps"></a>További lépések

További információ:

- **Egyéni tartományok**, lásd: [egyéni tartományok az Azure AD alkalmazásproxy használata](active-directory-application-proxy-custom-domains.md).

- **Alkalmazás-közzététel**, lásd: [az Azure AD-alkalmazásproxy használó alkalmazások közzététele](application-proxy-publish-azure-portal.md)


