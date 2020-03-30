---
title: Helyettesítő karakteres alkalmazások az Azure AD-alkalmazásproxyban
description: Ismerje meg, hogyan használhatja a helyettesítő alkalmazásokat az Azure Active Directory alkalmazásproxyjában.
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
ms.openlocfilehash: 3ad2032497b4b01476389428f5a2ef4a3961a1c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481245"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Helyettesítő karakteres alkalmazások az Azure Active Directory alkalmazásproxyjában

Az Azure Active Directoryban (Azure AD) a nagyszámú helyszíni alkalmazás konfigurálása gyorsan kezelhetetlenné válhat, és szükségtelen kockázatokat okoz a konfigurációs hibákhoz, ha sokuk nak ugyanazokat a beállításokat kell igényelnie. Az [Azure AD alkalmazásproxy](application-proxy.md)val a problémát a helyettesítő alkalmazás közzétételhasználatával egyszerre számos alkalmazás közzétételével és kezelésével oldhatja meg. Ez egy olyan megoldás, amely lehetővé teszi, hogy:

- Egyszerűsítse az adminisztrációs költségeket
- A lehetséges konfigurációs hibák számának csökkentése
- További erőforrások biztonságos elérésének engedélyezése a felhasználók számára

Ez a cikk a helyettesítő alkalmazások környezetben történő közzétételének konfigurálásához szükséges információkat tartalmazza.

## <a name="create-a-wildcard-application"></a>Helyettesítő karakteres alkalmazás létrehozása

Létrehozhat egy helyettesítő (*) alkalmazást, ha azonos konfigurációval rendelkező alkalmazáscsoporttal rendelkezik. A helyettesítő karakteres alkalmazások lehetséges jelöltjei a következő beállításokat megosztó alkalmazások:

- A felhasználók csoportja, akik hozzáférnek ezekhez
- Az SSO módszer
- A hozzáférési protokoll (http, https)

A helyettesítő karakterekkel rendelkező alkalmazásokat akkor tehet közzé, ha mindkettő a következő formátumú:

> http(s)://*. \<tartomány\>

Például: `http(s)://*.adventure-works.com`.

Bár a belső és külső URL-címek különböző tartományokat használhatnak, ajánlott eljárásként azonosnak kell lenniük. Az alkalmazás közzétételekor hibaüzenet jelenik meg, ha az URL-címek egyike nem rendelkezik helyettesítő karakterrel.

Ha további, különböző konfigurációs beállításokkal rendelkező alkalmazásokkal rendelkezik, ezeket a kivételeket külön alkalmazásként kell közzétennie a helyettesítő karakter alapértelmezett beállításainak felülírásához. A helyettesítő karakter nélküli alkalmazások mindig elsőbbséget élveznek a helyettesítő alkalmazásokkal szemben. A konfigurációs szempontból ezek "csak" rendszeres alkalmazások.

Helyettesítő karakteres alkalmazás létrehozása ugyanazon [alkalmazásközzétételi folyamaton](application-proxy-add-on-premises-application.md) alapul, amely az összes többi alkalmazás számára elérhető. Az egyetlen különbség az, hogy egy helyettesítő karaktert az URL-címek és potenciálisan az SSO konfiguráció.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez győződjön meg arról, hogy megfelelt ezeknek a követelményeknek.

### <a name="custom-domains"></a>Egyéni tartományok

Bár [az egyéni tartományok](application-proxy-configure-custom-domain.md) minden más alkalmazás esetében nem kötelezőek, a helyettesítő alkalmazások előfeltételei. Egyéni tartományok létrehozásához a következőkre van szükség:

1. Hozzon létre egy ellenőrzött tartományt az Azure-on belül.
1. Töltsön fel egy PFX formátumú TLS/SSL tanúsítványt az alkalmazásproxyra.

Érdemes helyettesítő tanúsítványt használni a létrehozni kívánt alkalmazáshoz. Másik lehetőségként olyan tanúsítványt is használhat, amely csak bizonyos alkalmazásokat sorol fel. Ebben az esetben csak a tanúsítványban felsorolt alkalmazások érhetők el ezen a helyettesítő karakteres alkalmazáson keresztül.

Biztonsági okokból ez egy nehéz követelmény, és nem támogatjuk a helyettesítő karaktereket olyan alkalmazásokhoz, amelyek nem tudnak egyéni tartományt használni a külső URL-címhez.

### <a name="dns-updates"></a>DNS-frissítések

Egyéni tartományok használataesetén létre kell hoznia egy DNS-bejegyzést a külső URL-cím (például ) CNAME rekordjával, `*.adventure-works.com`amely az alkalmazásproxy-végpont külső URL-címére mutat. Helyettesítő karakteres alkalmazások esetén a CNAME rekordnak a megfelelő külső URL-címekre kell mutatnia:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

A CNAME megfelelő beállításának ellenőrzéséhez használhatja az [nslookup-ot](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) az egyik célvégponton, `expenses.adventure-works.com`például a .  A válasznak tartalmaznia kell`<yourAADTenantId>.tenant.runtime.msappproxy.net`a már említett aliast ( ).

## <a name="considerations"></a>Megfontolandó szempontok

Íme néhány szempont, amelyet figyelembe kell vennie a helyettesítő karakteres alkalmazásokesetében.

### <a name="accepted-formats"></a>Elfogadott formátumok

Helyettesítő karakteres alkalmazások esetén a belső `http(s)://*.<domain>` **URL-címet** a formátumra kell formázni.

![Belső URL-cím esetén használja a http(ek)://*formátumot. \<tartomány>](./media/application-proxy-wildcard/22.png)

**Külső URL-cím konfigurálásakor**a következő formátumot kell használnia:`https://*.<custom domain>`

![Külső URL-cím esetén használja a https://* formátumot. \<egyéni tartomány>](./media/application-proxy-wildcard/21.png)

A helyettesítő karakter, a több helyettesítő karakter vagy más regex karakterláncok egyéb pozíciói nem támogatottak, és hibákat okoznak.

### <a name="excluding-applications-from-the-wildcard"></a>Alkalmazások kizárása a helyettesítő karakterből

Kizárhat egy alkalmazást a helyettesítő karakter alkalmazásból a

- A kivételalkalmazás közzététele normál alkalmazásként
- A helyettesítő karakter engedélyezése csak bizonyos alkalmazásokhoz a DNS-beállításokon keresztül

Az alkalmazás rendszeres alkalmazásként való közzététele az előnyben részesített módszer az alkalmazás helyettesítő karakterből való kizárásához. A kizárt alkalmazásokat a helyettesítő alkalmazások előtt közzé kell tennie, hogy a kivételek a kezdetektől fogva érvénybe lépjenek. A legspecifikusabb alkalmazás mindig elsőbbséget élvez `budgets.finance.adventure-works.com` – egy olyan alkalmazás `*.finance.adventure-works.com`, amely elsőbbséget élvez az `*.adventure-works.com`alkalmazással szemben , ami viszont elsőbbséget élvez az alkalmazással szemben .

A helyettesítő karaktert korlátozhatja úgy is, hogy a DNS-kezelésen keresztül csak bizonyos alkalmazásokesetében működjön. Ajánlott eljárásként hozzon létre egy CNAME bejegyzést, amely helyettesítő karaktert tartalmaz, és megfelel a beállított külső URL formátumának. Ehelyett azonban adott alkalmazásURL-eket a helyettesítő karakterekre irányíthat. A pont helyett `*.adventure-works.com`például `hr.adventure-works.com`a `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`. `expenses.adventure-works.com` `travel.adventure-works.com individually`

Ha ezt a beállítást használja, akkor egy másik `AppId.domain`CNAME `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`bejegyzésre is szüksége van az értékhez , például , és ugyanarra a helyre mutat. Az **AppId** a helyettesítő karakteralkalmazás alkalmazástulajdonságailapján található:

![Az alkalmazásazonosító megkeresése az alkalmazás tulajdonságlapján](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>A MyApps panel kezdőlapjának URL-címének beállítása

A helyettesítő karakter alkalmazás csak egy csempével jelenik meg a [MyApps panelen.](https://myapps.microsoft.com) Alapértelmezés szerint ez a csempe rejtett. A csempe megjelenítése és a felhasználók egy adott oldalon való landolása:

1. Kövesse a [kezdőlap URL-címének beállítására](application-proxy-configure-custom-home-page.md)vonatkozó irányelveket.
1. Állítsa **az Alkalmazás megjelenítése** **igaz** értékre az alkalmazás tulajdonságai lapon.

### <a name="kerberos-constrained-delegation"></a>Kerberos korlátozott delegálás

A [kerberos korlátozott delegálást (KCD) sso metódusként](application-proxy-configure-single-sign-on-with-kcd.md)használó alkalmazások esetében az SSO metódushoz felsorolt SPN-nek is szüksége lehet helyettesítő karakterre. Az spn például a `HTTP/*.adventure-works.com`következő lehet: . Továbbra is konfigurálnia kell az egyes SPN-eket `http://expenses.adventure-works.com and HTTP/travel.adventure-works.com`a háttérkiszolgálókon (például).

## <a name="scenario-1-general-wildcard-application"></a>1. forgatókönyv: Általános helyettesítő alkalmazás

Ebben az esetben három különböző alkalmazást szeretne közzétenni:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Mindhárom alkalmazás:

- Minden felhasználó használja
- *Integrált Windows-hitelesítés használata*
- Ugyanazokkal a tulajdonságokkal rendelkeznek

A helyettesítő karakteres alkalmazást közzéteheti az Alkalmazások közzététele az [Azure AD alkalmazásproxy használatával](application-proxy-add-on-premises-application.md)című dokumentumban ismertetett lépésekkel. Ez a forgatókönyv a következőket feltételezi:

- A következő azonosítóval rendelkező bérlő:`000aa000-11b1-2ccc-d333-4444eee4444e`
- Egy ellenőrzött tartomány `adventure-works.com` hívott konfigurálva van.
- Létrehozott **CNAME** `*.adventure-works.com` bejegyzés. `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`

A [dokumentált lépéseket](application-proxy-add-on-premises-application.md)követve hozzon létre egy új alkalmazás proxy alkalmazást a bérlőben. Ebben a példában a helyettesítő karakter a következő mezőkben található:

- Belső URL:Belső URL:Internal URL:

    ![Példa: Helyettesítő karakter a belső URL-ben](./media/application-proxy-wildcard/42.png)

- Külső URL:External URL:

    ![Példa: Helyettesítő karakter a külső URL-ben](./media/application-proxy-wildcard/43.png)

- Belső alkalmazás spn:

    ![Példa: Helyettesítő karakter az SPN-konfigurációban](./media/application-proxy-wildcard/44.png)

A helyettesítő karakteres alkalmazás közzétételével most már elérheti a három alkalmazást a használt URL-címekre (például `travel.adventure-works.com`).

A konfiguráció a következő struktúrát valósítja meg:

![A példakonfiguráció által megvalósított struktúra megjelenítése](./media/application-proxy-wildcard/05.png)

| Color | Leírás |
| ---   | ---         |
| Kék  | Alkalmazások explicit módon közzétett és látható az Azure Portalon. |
| Szürke  | A szülőalkalmazáson keresztül elérhető alkalmazások. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>2. eset: Általános helyettesítő alkalmazás kivétellel

Ebben az esetben a három általános alkalmazásmellett egy `finance.adventure-works.com`másik alkalmazással is rendelkezik, amelyet csak a Pénzügyi részleg férhet hozzá. Az aktuális alkalmazásstruktúrával a pénzügyi alkalmazás elérhető lenne a helyettesítő karakteres alkalmazáson és az összes alkalmazotton keresztül. Ennek módosításához zárja ki az alkalmazást a helyettesítő karakterből, ha a Finance-t külön alkalmazásként konfigurálja, szigorúbb engedélyekkel.

Meg kell győződnie arról, hogy `finance.adventure-works.com` léteznek olyan CNAME rekordok, amelyek az alkalmazás specifikus végpontjára mutatnak, és amelyek az alkalmazás proxylapján vannak megadva. Ebben a `finance.adventure-works.com` forgatókönyvben `https://finance-awcycles.msappproxy.net/`a pontra mutat.

A [dokumentált lépéseket követve](application-proxy-add-on-premises-application.md)ez a forgatókönyv a következő beállításokat igényli:

- A **belső URL-ben**helyettesítő karakter helyett **a pénzügyeket** állíthatja be.

    ![Példa: A belső URL-ben helyettesítő karakter helyett a pénzügyek beállítása](./media/application-proxy-wildcard/52.png)

- A **Külső URL-címben**helyettesítő karakter helyett **a pénzügyeket** állíthatja be.

    ![Példa: Finanszírozás beállítása helyettesítő karakter helyett a külső URL-ben](./media/application-proxy-wildcard/53.png)

- Belső alkalmazás SPN beállíthat **finanszírozás** helyett helyettesítő karakter.

    ![Példa: A pénzügyek beállítása helyettesítő karakter helyett az SPN-konfigurációban](./media/application-proxy-wildcard/54.png)

Ez a konfiguráció a következő forgatókönyvet valósítja meg:

![A mintaforgatókönyv által megvalósított konfiguráció megjelenítése](./media/application-proxy-wildcard/09.png)

Mivel `finance.adventure-works.com` a fajnál `*.adventure-works.com`konkrétabb URL-cím, elsőbbséget élvez. Azok a `finance.adventure-works.com` felhasználók, akik a Pénzügyi erőforrások alkalmazásban megadott felhasználói élményhez navigálnak. Ebben az esetben csak a pénzügyi `finance.adventure-works.com`alkalmazottak férhetnek hozzá.

Ha több kérelmet tesz közzé `finance.adventure-works.com` finanszírozásra, és ellenőrzött tartományként rendelkezik, közzétehet egy másik helyettesítő alkalmazással. `*.finance.adventure-works.com` Mivel ez pontosabb, mint `*.adventure-works.com`az általános, elsőbbséget élvez, ha a felhasználó hozzáfér egy alkalmazáshoz a pénzügyi tartományban.

## <a name="next-steps"></a>További lépések

- Az egyéni **tartományokról**az [Egyéni tartományok használata az Azure AD alkalmazásproxyban található munka témakörben](application-proxy-configure-custom-domain.md)olvashat bővebben.
- Ha többet szeretne megtudni **a közzétételi alkalmazásokról,** olvassa el az [Alkalmazások közzététele az Azure AD alkalmazásproxy használatával( Közzététel az Azure AD-alkalmazásproxyhasználatával) témakört.](application-proxy-add-on-premises-application.md)
