---
title: Helyettesítő karakteres alkalmazások az Azure AD Application Proxy
description: Megtudhatja, hogyan használhatók helyettesítő alkalmazások a Azure Active Directory alkalmazásproxy használatával.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e5861e802f39adecb5661bc17c22b432f137d59
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770304"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Helyettesítő karakteres alkalmazások a Azure Active Directory alkalmazásproxy

Azure Active Directory (Azure AD) esetében a nagy számú helyszíni alkalmazás konfigurálása gyorsan nem felügyelhető, és a konfigurációs hibák szükségtelen kockázatait is bevezetheti, ha sok esetben ugyanazokat a beállításokat igénylik. Az [Azure ad Application proxy](application-proxy.md)a problémát a helyettesítő karakteres alkalmazások közzétételével kezelheti, és egyszerre több alkalmazást is közzétehet és kezelhet. Ez egy olyan megoldás, amely a következőket teszi lehetővé:

- Egyszerűsítse adminisztratív terhelését
- Csökkentse a lehetséges konfigurációs hibák számát
- Lehetővé teszi, hogy a felhasználók biztonságosan hozzáférjenek a további erőforrásokhoz

Ez a cikk azokat az információkat tartalmazza, amelyekre szükség van a helyettesítő karakteres alkalmazások közzétételének konfigurálásához a környezetben.

## <a name="create-a-wildcard-application"></a>Helyettesítő karakteres alkalmazás létrehozása

Ha ugyanazokkal a konfigurációval rendelkező alkalmazásokkal rendelkező csoporttal rendelkezik, létrehozhat helyettesítő karaktert (*). A helyettesítő karakteres alkalmazások lehetséges pályázói a következő beállításokat megosztó alkalmazások:

- Azon felhasználók csoportja, akiknek hozzáférésük van
- Az egyszeri bejelentkezés metódusa
- A hozzáférési protokoll (http, HTTPS)

A helyettesítő karakterekkel rendelkező alkalmazásokat közzéteheti, ha a belső és külső URL-címek formátuma a következő:

> http (s)://*. \<tartomány\>

Például: `http(s)://*.adventure-works.com`.

Míg a belső és külső URL-címek különböző tartományokat használhatnak, az ajánlott eljárásnak megfelelőnek kell lennie. Az alkalmazás közzétételekor hibaüzenet jelenik meg, ha az URL-címek egyike nem tartalmaz helyettesítő karaktert.

Helyettesítő karakteres alkalmazás létrehozása ugyanazon [alkalmazás-közzétételi folyamaton](application-proxy-add-on-premises-application.md) alapul, amely minden más alkalmazás számára elérhető. Az egyetlen különbség, hogy egy helyettesítő karaktert is tartalmaz az URL-címekben, és potenciálisan az egyszeri bejelentkezés konfigurációját.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként győződjön meg arról, hogy teljesítette ezeket a követelményeket.

### <a name="custom-domains"></a>Egyéni tartományok

Míg az [Egyéni tartományok](application-proxy-configure-custom-domain.md) nem kötelezőek az összes többi alkalmazáshoz, ezek a helyettesítő alkalmazások előfeltételei. Az egyéni tartományok létrehozásához a következőket kell tennie:

1. Ellenőrzött tartomány létrehozása az Azure-ban.
1. Töltsön fel egy TLS-/SSL-tanúsítványt PFX formátumban az alkalmazásproxy számára.

Érdemes helyettesítő tanúsítványt használni a létrehozni kívánt alkalmazásnak megfelelően. Azt is megteheti, hogy olyan tanúsítványt is használhat, amely csak bizonyos alkalmazásokat listáz. Ebben az esetben csak a tanúsítványban felsorolt alkalmazások lesznek elérhetők a helyettesítő alkalmazáson keresztül.

Biztonsági okokból ez nehéz követelmény, és nem támogatjuk a helyettesítő karaktereket olyan alkalmazások esetében, amelyek nem használhatnak egyéni tartományt a külső URL-címhez.

### <a name="dns-updates"></a>DNS-frissítések

Egyéni tartományok használatakor létre kell hoznia egy DNS-bejegyzést a külső URL-címhez (például `*.adventure-works.com`) egy CNAME rekorddal, amely az alkalmazásproxy-végpont külső URL-címére mutat. Helyettesítő karakteres alkalmazások esetén a CNAME rekordnak a megfelelő külső URL-címekre kell mutatnia:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Annak ellenőrzéséhez, hogy helyesen konfigurálta-e a CNAME-t, használhatja az [nslookupt](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) az egyik cél végponton, `expenses.adventure-works.com`például:.  A válasznak tartalmaznia kell a már említett aliast (`<yourAADTenantId>.tenant.runtime.msappproxy.net`).

## <a name="considerations"></a>Megfontolandó szempontok

Az alábbiakban néhány megfontolandó szempontot érdemes figyelembe venni a helyettesítő karakteres alkalmazások esetében.

### <a name="accepted-formats"></a>Elfogadott formátumok

Helyettesítő karakteres alkalmazások esetén a **belső URL-címet** a `http(s)://*.<domain>`következő formátumban kell megadni:.

![A belső URL-cím esetében használja a http (s)://* formátumot. \<tartomány>](./media/application-proxy-wildcard/22.png)

**Külső URL-cím**konfigurálásakor a következő formátumot kell használnia:`https://*.<custom domain>`

![A külső URL-cím esetében használja a * https://formátumot. \<egyéni tartomány>](./media/application-proxy-wildcard/21.png)

A helyettesítő karakterek, több helyettesítő karakter vagy más regex sztringek egyéb helyei nem támogatottak, és hibákat okoznak.

### <a name="excluding-applications-from-the-wildcard"></a>Alkalmazások kizárása a helyettesítő karakterből

Az alkalmazás nem zárható ki a helyettesítő karakteres alkalmazásból a következővel:

- A kivételként szolgáló alkalmazás közzététele normál alkalmazásként
- A helyettesítő karakterek engedélyezése csak bizonyos alkalmazásokhoz a DNS-beállításokon keresztül

Az alkalmazások normál alkalmazásként történő közzététele az előnyben részesített módszer, amellyel kizárható egy alkalmazás egy helyettesítő karakterből. A kizárt alkalmazásokat a helyettesítő alkalmazások előtt közzé kell tenni, hogy a kivételek kikényszerítve legyenek az elejétől. A legpontosabb alkalmazás mindig elsőbbséget élvez – egy közzétett `budgets.finance.adventure-works.com` alkalmazás elsőbbséget élvez az alkalmazással `*.finance.adventure-works.com`szemben, ami elsőbbséget élvez az alkalmazással `*.adventure-works.com`szemben.

A helyettesítő karaktert úgy is korlátozhatja, hogy csak bizonyos alkalmazások esetében működjön a DNS-kezelésen keresztül. Ajánlott eljárásként olyan CNAME bejegyzést kell létrehoznia, amely tartalmaz egy helyettesítő karaktert, és megfelel a konfigurált külső URL-cím formátumának. Ehelyett az alkalmazás URL-címeit is kijelölheti a helyettesítő karakterekkel. `*.adventure-works.com`Például a `hr.adventure-works.com`, pont `expenses.adventure-works.com` és `travel.adventure-works.com individually` a `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`helyett.

Ha ezt a lehetőséget választja, egy másik CNAME-bejegyzésre is szüksége lesz `AppId.domain`az értékhez, `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`például ugyanarra a helyre mutat. A **AppID** a helyettesítő karakteres alkalmazás alkalmazás tulajdonságai lapján található:

![Keresse meg az alkalmazás AZONOSÍTÓját az alkalmazás tulajdonságlapján](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>A MyApps panel kezdőlapjának URL-címének beállítása

A helyettesítő karakteres alkalmazás csak egy csempével jelenik meg a [MyApps panelen](https://myapps.microsoft.com). Alapértelmezés szerint ez a csempe rejtett. A csempék megjelenítéséhez és a felhasználók egy adott oldalon való kirakásához:

1. Kövesse a [Kezdőlap URL-címének beállításához](application-proxy-configure-custom-home-page.md)szükséges útmutatást.
1. Állítsa az **alkalmazás megjelenítése** **igaz** értékre az alkalmazás Tulajdonságok lapján.

### <a name="kerberos-constrained-delegation"></a>Kerberos által korlátozott delegálás

A Kerberos által [korlátozott delegálást (KCD) használó alkalmazások egyszeri bejelentkezéses módszere](application-proxy-configure-single-sign-on-with-kcd.md)esetén az egyszeri bejelentkezéses metódushoz megadott egyszerű szolgáltatásnév is helyettesítő karakternek kell lennie. Az egyszerű szolgáltatásnév például a következő lehet: `HTTP/*.adventure-works.com`. Továbbra is rendelkeznie kell a háttér-kiszolgálókon konfigurált egyedi SPN-ek (például `HTTP/expenses.adventure-works.com and HTTP/travel.adventure-works.com`:).

## <a name="scenario-1-general-wildcard-application"></a>1. forgatókönyv: általános helyettesítő karakteres alkalmazás

Ebben az esetben három különböző alkalmazást szeretne közzétenni:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Mindhárom alkalmazás:

- Az összes felhasználó használja
- *Integrált Windows-hitelesítés* használata
- Ugyanazok a Tulajdonságok

A helyettesítő karakteres alkalmazást közzéteheti az [alkalmazások közzététele az Azure ad Application proxy használatával](application-proxy-add-on-premises-application.md)című témakörben ismertetett lépésekkel. Ez a forgatókönyv a következőket feltételezi:

- Egy bérlő a következő AZONOSÍTÓval:`000aa000-11b1-2ccc-d333-4444eee4444e`
- Egy nevű `adventure-works.com` ellenőrzött tartomány konfigurálva lett.
- A **CNAME** rendszer létrehoz egy CNAME `*.adventure-works.com` bejegyzést `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` , amely a következőre mutat:.

A [dokumentált lépéseket](application-proxy-add-on-premises-application.md)követve hozzon létre egy új alkalmazásproxy-alkalmazást a bérlőben. Ebben a példában a helyettesítő karakter a következő mezőkben szerepel:

- Belső URL-cím:

    ![Példa: helyettesítő karakter a belső URL-címben](./media/application-proxy-wildcard/42.png)

- Külső URL-cím:

    ![Példa: helyettesítő karakter a külső URL-címben](./media/application-proxy-wildcard/43.png)

- Belső alkalmazás SPN-je:

    ![Példa: helyettesítő karakter az SPN-konfigurációban](./media/application-proxy-wildcard/44.png)

A helyettesítő karakteres alkalmazás közzétételével most már elérheti a három alkalmazást, ha a használt URL-címekre navigál (például: `travel.adventure-works.com`).

A konfiguráció a következő struktúrát valósítja meg:

![Megjeleníti a példa konfigurációjában megvalósított struktúrát](./media/application-proxy-wildcard/05.png)

| Color | Leírás |
| ---   | ---         |
| Kék  | A Azure Portal explicit módon közzétett és látható alkalmazások. |
| Szürke  | A fölérendelt alkalmazás segítségével elérhető alkalmazások. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>2. forgatókönyv: általános helyettesítő karakteres alkalmazás kivétel esetén

Ebben a forgatókönyvben a három általános alkalmazás mellett egy másik alkalmazást `finance.adventure-works.com`is tartalmaz, amely csak a pénzügyi részleg által elérhető. A jelenlegi alkalmazás szerkezete révén a pénzügyi alkalmazása a helyettesítő karakteres alkalmazás és az összes alkalmazott számára elérhetővé válik. Ennek módosításához kizárhatja az alkalmazását a helyettesítő karakterből úgy, hogy a Pénzügyt külön alkalmazásként konfigurálja, és szigorúbb engedélyekkel rendelkezik.

Győződjön meg arról, hogy létezik egy CNAME rekord, amely az `finance.adventure-works.com` alkalmazáshoz megadott végpontra mutat, amely az alkalmazás alkalmazásproxy lapján van megadva. Ebben a forgatókönyvben `finance.adventure-works.com` a következőre mutat: `https://finance-awcycles.msappproxy.net/`.

A [dokumentált lépéseket](application-proxy-add-on-premises-application.md)követve ehhez a forgatókönyvhöz a következő beállítások szükségesek:

- A **belső URL-címben**helyettesítő karakter helyett a **Pénzügy** értéket kell beállítania.

    ![Példa: a pénzügy beállítása helyettesítő karakter helyett a belső URL-címben](./media/application-proxy-wildcard/52.png)

- A **külső URL-címben**helyettesítő karakter helyett a **Pénzügy** értéket kell beállítania.

    ![Példa: a pénzügy beállítása helyettesítő karakter helyett a külső URL-címben](./media/application-proxy-wildcard/53.png)

- Belső alkalmazás SPN-ben a **Penzugy** helyett helyettesítő karaktert kell beállítania.

    ![Példa: a pénzügy beállítása helyettesítő karakter helyett a SPN-konfigurációban](./media/application-proxy-wildcard/54.png)

Ez a konfiguráció a következő helyzetet valósítja meg:

![A minta forgatókönyv által megvalósított konfiguráció megjelenítése](./media/application-proxy-wildcard/09.png)

Mivel `finance.adventure-works.com` a egy konkrétabb URL-cím `*.adventure-works.com`, mint a, elsőbbséget élvez. `finance.adventure-works.com` A felhasználók a pénzügyi erőforrások alkalmazásban megadhatják a felhasználói élményt. Ebben az esetben csak a pénzügyi alkalmazottak férhetnek hozzá `finance.adventure-works.com`.

Ha több alkalmazás van közzétéve a Finance szolgáltatásban, `finance.adventure-works.com` és Ön ellenőrzött tartománnyal rendelkezik, közzétehet egy másik helyettesítő karaktert `*.finance.adventure-works.com`. Mivel ez az általánosnál `*.adventure-works.com`sokkal pontosabb, elsőbbséget élvez, ha egy felhasználó a pénzügyi tartományban található alkalmazáshoz fér hozzá.

## <a name="next-steps"></a>További lépések

- Az **Egyéni tartományokkal**kapcsolatos további tudnivalókért tekintse meg az [Egyéni tartományok használata az Azure ad Application proxy-ban](application-proxy-configure-custom-domain.md)című témakört.
- További információ az **alkalmazások közzétételéről**: [alkalmazások közzététele az Azure ad Application proxy használatával](application-proxy-add-on-premises-application.md)
