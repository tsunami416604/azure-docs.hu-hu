---
title: "Vállalati arculat megjelenítése a bejelentkezési és a hozzáférési panel oldalon az Azure Active Directoryban"
description: "Megismerkedhet a vállalati arculat hozzáadásának mikéntjével az Azure bejelentkezési és hozzáférési oldalán"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: f74621b4-4ef0-4899-8c0e-0c20347a8c31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/07/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 6d4fbfe97288fcb76628b45649b8b678152198a9
ms.lasthandoff: 04/07/2017


---
# <a name="add-company-branding-to-sign-in-and-access-panel-pages"></a>Vállalati arculat megjelenítése a bejelentkezési és a hozzáférési panel oldalon
Számos cég igyekszik egységes megjelenést adni az általa kezelt webhelyeknek és szolgáltatásoknak. Az Azure Active Directory ennek érdekében lehetővé teszi, hogy a szakemberek a vállalat emblémája és képek elhelyezésével testre szabhassák az alábbi webhelyek megjelenését:

* **Bejelentkezési oldal** – Ez az oldal jelenik meg, amikor az alkalmazottak vagy a vendégek olyan Office 365-be vagy más, az Azure AD-t használó alkalmazásokba jelentkeznek be.
* **Hozzáférési panel oldala** – A hozzáférési panel egy olyan webes portál, amelyen megtekintheti és elindíthatja azokat a felhőalapú alkalmazásokat, amelyekhez az Azure AD-rendszergazda hozzáférést adott Önnek. A hozzáférési panel a [https://myapps.microsoft.com](https://myapps.microsoft.com) webhelyen található.

Ez a témakör a bejelentkezési oldal és a hozzáférési panel testreszabását ismerteti.

> [!NOTE]
> * A vállalati arculatot hozzáadó szolgáltatás kizárólag akkor érhető el, ha frissített az Azure Active Directory Prémium vagy Alapszintű kiadására, vagy rendelkezik Office 365-licenccel. További információért lásd: Azure Active Directory-kiadások.
> 
> * Az Azure Active Directory Prémium és Alapszintű kiadásai az Azure Active Directory világszerte elérhető példányával érhetők el a kínai ügyfelek számára. Az Azure Active Directory Prémium és Alapszintű kiadásai jelenleg nem támogatottak Kínában a 21Vianet által működtetett Microsoft Azure szolgáltatásban. További információkért lépjen velünk kapcsolatba az Azure Active Directory-fórumon.


## <a name="customizing-the-sign-in-page"></a>A bejelentkezési oldal testreszabása
A felhasználók általában akkor használják az Azure AD bejelentkezési oldalát, amikor a cégük által előfizetett felhőalapú alkalmazásokat és szolgáltatásokat próbálnak elérni.

A bejelentkezési oldalon alkalmazott arculatmódosításoknak a végfelhasználók számára való megjelenése akár egy órát is igénybe vehet.

A vállalati arculat megjelenítésének elemei a bérlőspecifikus URL-címek – például https://outlook.com/contoso.com –, felhasználók általi elérésekor jelennek meg az Azure AD bejelentkezési oldalán.

Amikor a felhasználók egy általános URL-címen – például www.office.com –, keresnek fel egy szolgáltatást, a bejelentkezési oldal még nem tartalmazza a vállalati arculat adatait, mert a felhasználó nem ismert a rendszer számára. A felhasználói azonosító megadása, vagy a felhasználói csempe kiválasztása után azonban megjelenik a vállalati arculat.

> [!NOTE]
> * A tartománynévnek aktív állapottal kell szerepelnie a klasszikus Azure portál **Active Directory** > **Címtár** > **Tartományok** szakaszában, ahol a vállalati arculatot konfigurálta.
> * A bejelentkezési oldal vállalati arculata személyes Microsoft-fiókok bejelentkezési oldalán nem jelenik meg. Ha az alkalmazottak vagy a vendégek személyes Microsoft-fiókkal jelentkeznek be, a bejelentkezési oldal nem fogja a céges arculatot tükrözni.
>

A következő képernyőképek a bejelentkezési oldalak testreszabását mutatják be.

### <a name="scenario-1-contoso-employee-goes-to-a-generic-app-url-for-example-wwwofficecom"></a>1. példa: Egy Contoso-alkalmazott egy általános alkalmazás URL-címét nyitja meg (például www.office.com)

Jelen példánkban a Contoso-felhasználó egy mobilalkalmazásba, vagy egy általános URL-címet használó webalkalmazásba jelentkezik be. A bal oldali ábrán mindig az alkalmazás látható, a jobb oldali tevékenységpanel pedig a megfelelő Contoso-márkaelemek megjelenítése érdekében frissül.

![Az Office 365 bejelentkezési oldala a testreszabást megelőzően és azt követően][1]

### <a name="scenario-2-contoso-employee-goes-to-contoso-app-thats-restricted-to-internal-users"></a>2. példa: A Contoso-alkalmazott a belső felhasználókra korlátozott a Contoso-alkalmazást nyitja meg

Ezen példában a Contoso-felhasználó egy vállalatfüggő URL-cím segítségével jelentkezik be egy belső alkalmazásba. A bal oldali ábrán a vállalati arculat (Contoso) látható. A jobb oldali tevékenységpanel a Contosóhoz kötött, és az alkalmazottak bejelentkezését segíti.

![korlátozott alkalmazás bejelentkezési oldala][2]

### <a name="scenario-3-contoso-employee-goes-to-a-contoso-app-thats-open-to-external-users"></a>3. példa: A Contoso-alkalmazott a külső felhasználók számára nyitott Contoso-alkalmazást nyit meg

Ebben a példában a felhasználó – aki lehet Contoso-alkalmazott, vagy sem – a Contoso egy üzletági alkalmazásába jelentkezik be. A bal oldali ábrán az erőforrás tulajdonosa (Contoso) látható, csakúgy, mint a fenti \#2. példában. Ezúttal azonban a jobb oldali tevékenységpanel nincs a Contosóhoz kötve, kifejezve hogy a külső felhasználók is beléphetnek.

![bejelentkezés nyílt hozzáféréshez][3]

### <a name="scenario-4-fabrikam-business-guest-goes-to-contoso-app-thats-open-to-external-users"></a>4. példa: A Fabrikam céghez tartozó vendég megnyitja a külső felhasználók számára nyitva álló Contoso-alkalmazást

Ezen példában a Contoso-felhasználó egy vállalatfüggő URL-cím segítségével jelentkezik be egy belső alkalmazásba. A bal oldali ábrán a vállalati arculat (Contoso) látható. A jobb oldali tevékenységpanel a Contosóhoz kötött, és az alkalmazottak bejelentkezését segíti.

![bejelentkezés külső felhasználóként][4]


## <a name="what-elements-on-the-page-can-i-customize"></a>Melyek az oldal testreszabható elemei?

A bejelentkezési oldal alábbi elemei szabhatók testre:

![][5]

| Oldalelem | Helye az oldalon |
|:--- | --- |
| Szalagcímembléma | Az oldal jobb felső részén látható. Az alkalmazás emblémáját váltja fel a felhasználó szervezetének meghatározása után (általában a felhasználó nevének megadását követően). |
| Háttér-ábra | Teljes kifutású képként jelenik meg a bejelentkezési oldal bal oldalán. Az alkalmazás ábráját váltja fel bérlő által használt bejelentkezési forgatókönyvek esetén (amikor a felhasználó a saját cég vagy egy olyan cég által közzétett alkalmazáshoz fér hozzá, amelynél vendég).<br>Alacsony sávszélességű kapcsolatok használata esetén a háttér-ábra helyett háttérszínt használ a rendszer. Például a telefonok keskeny képernyőjén az ábra nem látható.<br>Amikor a felhasználók átméretezik böngészőjüket, a háttér-ábra csonkolva lesz. Az ábra tervezésekor a vizuális elemeket tartsa a bal felső sarokban, hogy ne legyenek csonkolva. | 
| „Bejelentkezve szeretnék maradni” jelölőnégyzet | A **Jelszó** mező alatt látható. |
| A bejelentkezési oldal szövege | Az oldal lábléce fent megjelenítendő sablonszöveg. A felhasználók számára hasznos információk – például a segélyszolgálat telefonszáma, vagy egy jogi nyilatkozat –, átadására használható. |

> [!NOTE]
> Ezen elemek egyike sem kötelező. Ha például megad szalagcímemblémát, de háttér-ábrát nem, a bejelentkezési oldalon az embléma és a célhelyhez tartozó ábra jelenik meg (például az Office 365 kaliforniai főutat ábrázoló képe).
>

A bejelentkezési oldalon a **Bejelentkezve szeretnék maradni** jelölőnégyzet lehetővé teszi a felhasználó számára, hogy a böngészője bezárása és újbóli megnyitása után is bejelentkezve maradjon, és nincs hatással a munkamenet élettartamára.

A jelölőnégyzet megjelenése a **KMSI elrejtése** beállításától függ.

![KMSI elrejtése beállítás][6]

A jelölőnégyzet elrejtéséhez konfigurálja a beállítást **Rejtett** értékre.

> [!NOTE]
> A SharePoint Online és az Office 2010 egyes funkciói attól függenek, hogy a felhasználók be tudják-e jelölni ezt a jelölőnégyzetet. Ha ezt a beállítást rejtett értékre konfigurálja, a felhasználóinak további és váratlan bejelentkezési kérések jelenhetnek meg.
>
>

Az oldalon szereplő összes elem honosítható. A testreszabási összetevők „alapértelmezett” készletének összeállítását követően a különböző területi beállításokhoz további verziókat is konfigurálhat. A különböző elemek szabadon kombinálhatók. Megteheti például a következőt:

* Létrehozhat egy „alapértelmezett” ábrát, amely minden országban használható, majd hozza létre ennek angol és francia verzióját. Ha a böngésző a két nyelv valamelyikére van állítva, akkor az ábra speciális verziója, minden más nyelv esetében pedig az alapértelmezett ábra jelenik meg.
* A vállalat eltérő verziójú (például japán vagy héber) emblémáit is beállíthatja.

## <a name="access-panel-page-customization"></a>A hozzáférési panel oldalának testreszabása
A hozzáférési panel alapvetően a portál egy olyan oldala, ahonnan olyan felhőalkalmazásokhoz lehet gyorsan hozzáférni, amelyekhez a rendszergazda jogosultságot adott. Ezen az oldalon az alkalmazások kattintható alkalmazáscsempékként jelennek meg.

Az alábbi képernyőfelvétel a hozzáférési panel oldalát mutatja be a testreszabást követően.

![][8]

## <a name="configure-your-directory-with-company-branding"></a>A címtár konfigurálása a vállalati arculattal
A klasszikus Azure portálon testreszabható elemek egy alapértelmezett készletét állíthatja be címtáranként. Az alapértelmezett verziók mentését követően a rendszergazda az egyes elemek különböző nyelvekhez/területi beállításokhoz honosított verzióit is hozzáadhatja a készlethez. A testre szabható elemek egyikének sem kötelező a használata.

Ha például beállít egy alapértelmezett szalagcímemblémát, de nagy méretű ábrát nem, az embléma megjelenik a bejelentkezési oldal jobb felső sarkában. A megjelenő ábra azonban a webhely lapértelmezett ábrája lesz.

Vegyük a következő konfigurációt:

* A szalagcím alapértelmezett emblémája és a bejelentkezési oldal szövege angolul
* Nyelvspecifikus bejelentkezési oldal német nyelven

Ha az Ön választott nyelve a német, az oldal az alapértelmezett szalagcímemblémával, de német szöveggel jelenik meg.

Bár technikailag az Azure AD által támogatott valamennyi nyelvhez beállíthat különböző nyelvi készleteket, karbantartási és teljesítmény-felhasználási okokból nem javasoljuk túl sok változat fenntartását.
 
**Ha a címtárnak vállalti arculatot szeretne adni, hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com) annak a címtárnak a rendszergazdájaként, amelyet testre szeretne szabni.
2. Jelölje ki a testre szabni kívánt címtárat.
3. A felső eszköztáron kattintson a **Konfigurálás** lehetőségre.
4. Kattintson a **Customize Branding** (Márkajelzés testreszabása) lehetőségre.
5. Módosítsa az elemeket, amelyeket testre szeretne szabni. A mezők egyike sem kötelező.
6. Kattintson a **Save** (Mentés) gombra.

A bejelentkezési oldal vállalati arculatán alkalmazott módosítások megjelenése akár egy órát is igénybe vehet.

**Nyelvspecifikus vállalati arculat hozzáadásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com) annak a címtárnak a rendszergazdájaként, amelyet testre szeretne szabni.
2. Jelölje ki a testre szabni kívánt címtárat.
fs3. A felső eszköztáron kattintson a **Konfigurálás** lehetőségre.
4. Kattintson a **Customize Branding** (Márkajelzés testreszabása) lehetőségre.
5. Kattintson az **Add branding for a specific language** (Márkajelzés hozzáadása adott nyelven) lehetőségre.
6. Válassza ki az embléma testreszabásánál használni kívánt nyelvet, majd kattintson a **Tovább** gombra.
7. Csak azokat az elemeket szerkessze, amelyeknek felül szeretné írni a nyelvét. A mezők egyike sem kötelező. Az üresen hagyott mezőkbe az egyéni alapértelmezett érték kerül (vagy a Microsoft által beállított alapérték, ha egyéni nincsen konfigurálva).
8. Kattintson a **Save** (Mentés) gombra.

**Ha el szeretné távolítani a címtárból a vállalati arculatot, hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com) annak a címtárnak a rendszergazdájaként, amelyet testre szeretne szabni.
2. Jelölje ki a testre szabni kívánt címtárat.
3. A felső eszköztáron kattintson a **Konfigurálás** lehetőségre.
4. Kattintson a **Customize Branding** (Márkajelzés testreszabása) lehetőségre.
5. A márkajelzés testreszabásával foglalkozó oldalon válassza a **Edit Existing Branding Settings** (Meglévő márkajelzési beállítások szerkesztése) lehetőséget, majd lépjen a következő oldalra.
6. Attól függően, hogy melyik elemet kívánja eltávolítani, végezze el a megfelelő műveleteket az alábbiak közül:

    a. A **Banner Logo** (Szalagcímembléma) területen válassza a **Remove uploaded logo** (Feltöltött embléma eltávolítása) lehetőséget.

    b. A **Tile Logo** (Csempe emblémája) területen válassza a **Remove uploaded logo** (Feltöltött embléma eltávolítása) lehetőséget.

    c. Távolítsa el a szöveget az összes szövegmezőből.

    d. Kattintson a **Tovább** gombra.

    e. Távolítsa el a szöveget az összes szövegmezőből.
7. Az elemek eltávolításához kattintson a **Mentés** gombra.
8. Amennyiben szükséges, kattintson újra a **Customize Branding** (Márkajelzés testreszabása) gombra, majd ismételje meg ezeket a lépéseket az összes eltávolítandó nyelvspecifikus márkajelzés esetében.
    Ha a **Customize Branding** (Márkajelzés testreszabása) elemre kattint, az összes márkajelzési beállítás törlődik, és a **Customize Default Branding** (Alapértelmezett márkajelzés konfigurálása) űrlap jelenik meg, megadott beállítások nélkül.


## <a name="customizable-elements"></a>Testreszabható elemek
A vállalati emblémát a bejelentkezési és a hozzáférési panel oldalak használják, míg más elemeket csak a bejelentkezési oldal használ. A különböző testreszabható elemek részletes adatait az alábbi táblázat tartalmazza.

| Név | Leírás | Korlátozások | Javaslatok |
| --- | --- | --- | --- |
| Szalagcímembléma |A szalagcímembléma a bejelentkezési és a hozzáférési panel oldalakon jelenik meg. |<p>JPG vagy PNG</p><p>60 x 280 képpont</p><p>10 kB</p> |<p>Használja szervezete teljes emblémáját (a piktogramot és az emblémát is beleértve).</p><p>A magassága ne legyen több 30 képpontnál, hogy a mobileszközökön ne legyen szükség görgetősáv használatára.</p><p>A mérete maradjon 4 kB alatt.</p><p>Átlátszó PNG formátumot használjon (nem érdemes feltételezni, hogy a bejelentkezési oldalak háttérszíne minden esetben fehér).</p> |
| Csempeembléma | Jelenleg nem használt |<p>JPG vagy PNG</p><p>120 x 120 képpont</p><p>10 kB</p> |<p>Legyen egyszerű (kisméretű szöveg nélkül), mivel a rendszer ezt a képet a felére kicsinyítheti. |
| </p> | | | |
| Bejelentkezési felhasználónév címkéje | Jelenleg nem használt |<p>Unicode szöveg, legfeljebb 50 karakter</p><p>Csak egyszerű szöveg (hivatkozások és HTML-címkék nélkül)</p> |<p>Legyen rövid és egyszerű.</p><p>Kérdezze meg felhasználóit, hogy általában hogyan hívják a nekik biztosított munkahelyi vagy iskolai fiókot.</p> |
| Bejelentkezési oldal sablonszövege |Ez a sablonszöveg jelenik meg a bejelentkezési oldal űrlapja alatt, és a használatával további utasítások is megjeleníthetők a segítségkérés és a támogatás igénylésének módjáról. |<p>Unicode szöveg, legfeljebb 256 karakter</p><p>Csak egyszerű szöveg (hivatkozások és HTML-címkék nélkül)</p> |Legyen 250 karakternél kevesebb (nagyjából 3 soros szöveg). |
| A bejelentkezési oldal háttér-ábrája | A bejelentkezési oldal bal (jobbról balra író nyelvek esetén jobb) oldalán látható nagy kép, amikor a felhasználók hozzáférnek a bérlő-specifikus URL-címekhez. |<p>JPG vagy PNG</p><p>1420 x 1200</p><p>500 kB</p> |<p>1420 x 1200 képpont.</p><p>Fontos: a mérete legyen a lehető legkisebb, ideálisan 200 kB alatti. Ha a kép túlságosan nagy, az kihatással van a bejelentkezési oldal teljesítményére, amikor a kép nincs gyorsítótárazva.</p><p>Ez a kép szinte mindig csonkolva lesz a különböző képernyő-méretarányoknak megfelelően. Az elsődleges vizuális elemeket tartsa a bal felső sarokban.</p> |
| A bejelentkezési oldal háttérszíne | Alacsony sávszélességű kapcsolatok használata esetén a háttér-ábra helyett egyszínű hátteret használ a rendszer. | Hexadecimális formátumú RGB-színnek kell lennie (például: \#FFFFFF) | Javasoljuk, hogy a szalagcímembléma elsődleges színét válassza háttérszínnek. |

## <a name="next-steps"></a>Következő lépések
* [Bevezetés a Prémium szintű Azure Active Directory használatába](active-directory-get-started-premium.md)
* [A hozzáférési és használati jelentések megtekintése](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-add-company-branding/signin-page_before-customization.png
[2]: ./media/active-directory-add-company-branding/signin-page-restricted-app.png
[3]: ./media/active-directory-add-company-branding/signin-page-open-access.png
[4]: ./media/active-directory-add-company-branding/signin-page-external-guest.png
[5]: ./media/active-directory-add-company-branding/which-elements-can-i-customize.png
[6]: ./media/active-directory-add-company-branding/hide-kmsi.png
[8]: ./media/active-directory-add-company-branding/APBranding.png
[9]: ./media/active-directory-add-company-branding/hidekmsi.png

