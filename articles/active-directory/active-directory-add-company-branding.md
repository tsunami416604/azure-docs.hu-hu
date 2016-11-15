---
title: "Vállalati arculat megjelenítése a bejelentkezési és a hozzáférési panel oldalon"
description: "Megismerkedhet a vállalati arculat hozzáadásának mikéntjével az Azure bejelentkezési és hozzáférési oldalán"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: f74621b4-4ef0-4899-8c0e-0c20347a8c31
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/30/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d173847a9d96e5524eae5283fb63857f02e39f10


---
# <a name="add-company-branding-to-your-signin-and-access-panel-pages"></a>Vállalati arculat megjelenítése a bejelentkezési és a hozzáférési panel oldalon
A félreértések elkerülése végett számos vállalat igyekszik egységes megjelenést adni az általa kezelt összes webhelynek és szolgáltatásnak. Az Azure Active Directory ennek érdekében testre szabhatóvá teszi az alábbi webhelyek megjelenését a vállalat emblémájának elhelyezésével és egyéni színsémák alkalmazásával:

* **Bejelentkezési oldal** – Ez az oldal jelenik meg az olyan Office 365- vagy más webes alkalmazásokra történő bejelentkezéskor, amelyek identitásszolgáltatóként az Azure AD-t használják. Ezzel az oldallal a kezdőtartomány-felderítés vagy hitelesítő adatainak megadása során találkozik. A kezdőtartomány-felderítés lehetővé teszi, hogy a rendszer átirányítsa az összevont felhasználókat a helyszíni STS-eikre (például az AD FS-re).
* **Hozzáférési panel oldala** – A hozzáférési panel egy olyan webes portál, amelyen megtekintheti és elindíthatja azokat a felhőalapú alkalmazásokat, amelyekhez az Azure AD-rendszergazda hozzáférést adott. A hozzáférési panel a következő URL-címen érhető el: [https://myapps.microsoft.com](https://myapps.microsoft.com).

Ez a témakör a bejelentkezési oldal és a hozzáférési panel testreszabását ismerteti.

> [!NOTE]
> * A vállalati arculatot hozzáadó szolgáltatás kizárólag akkor érhető el, ha frissített az Azure Active Directory Prémium vagy Alapszintű kiadására, vagy Office 365-felhasználó. További információk: [Azure Active Directory editions](active-directory-editions.md) (Azure Active Directory-kiadások).
> * Az Azure Active Directory Prémium és Alapszintű kiadásai az Azure Active Directory világszerte elérhető példányával érhetők el a kínai ügyfelek számára. Az Azure Active Directory Prémium és Alapszintű kiadásai jelenleg nem támogatottak Kínában a 21Vianet által működtetett Microsoft Azure szolgáltatásban. További információkért lépjen velünk kapcsolatba az [Azure Active Directory fórumán](https://feedback.azure.com/forums/169401-azure-active-directory/).
> 
> 

## <a name="customizing-the-signin-page"></a>A bejelentkezési oldal testreszabása
A felhasználók jellemzően akkor használják a bejelentkezési oldalt, ha böngészőalapú hozzáférésre van szükségük a szervezetük által előfizetett felhőalkalmazásaikhoz és szolgáltatásaikhoz.

A bejelentkezési oldalon alkalmazott módosítások megjelenése akár egy órát is igénybe vehet.

A vállalati arculattal ellátott bejelentkezési oldal csak akkor jelenik meg, ha bérlőspecifikus URL-címmel (például https://outlook.com/**contoso**.com vagy https://mail.**contoso**.com) rendelkező szolgáltatást keres fel.

Nem bérlőspecifikus URL-címmel (például https://mail.office365.com) ellátott szolgáltatás felkeresésekor nem vállalati arculattal rendelkező bejelentkezési oldal jelenik meg. Ebben az esetben a vállalati arculat a felhasználói azonosító megadása és a felhasználói csempe kiválasztása után jelenik meg.

> [!NOTE]
> * A tartománynévnek aktív állapottal kell szerepelnie a klasszikus Azure portál **Active Directory** > **Címtár** > **Tartományok** szakaszában, ahol a vállalati arculatot konfigurálta.
> * A bejelentkezési oldal vállalati arculata a Microsoft ügyfél-bejelentkezési oldalán nem jelenik meg. Ha személyes Microsoft-fiókkal jelentkezik be, láthatja a felhasználói csempék vállalati arculattal ellátott és Azure AD által renderelt listáját, vállalatának arculata azonban a Microsoft-fiók bejelentkezési oldalán nem jelenik meg.
> 
> 

Ha meg kívánja jeleníteni vállalatának arculatát, színeit és egyéb testreszabható elemeit ezen az oldalon, az alábbi ábrákon megfigyelheti a különbséget a két megközelítés között.

Az alábbi képernyőfelvételen szereplő példán az Office 365 bejelentkezési oldala látható egy asztali számítógépen, a testreszabást **megelőzően**:

![Az Office 365 bejelentkezési oldala a testreszabást megelőzően][1]

Az alábbi képernyőfelvételen szereplő példán az Office 365 bejelentkezési oldala látható egy asztali számítógépen, a testreszabást **követően**:

![Az Office 365 bejelentkezési oldala a testreszabást követően][2]

Az alábbi képernyőfelvételen szereplő példán az Office 365 bejelentkezési oldala látható egy mobileszközön, a testreszabást **megelőzően**:

![Az Office 365 bejelentkezési oldala a testreszabást megelőzően][3]

Az alábbi képernyőfelvételen szereplő példán az Office 365 bejelentkezési oldala látható egy mobileszközön, a testreszabást **követően**:

![Az Office 365 bejelentkezési oldala a testreszabást követően][4]

A böngészőablak átméretezésekor a nagy méretű ábrákat (mint a fentit is) a rendszer gyakran levágja a különböző képernyő-méretarányoknak megfelelően. Ennek tudatában a fontos vizuális elemeket meg kell próbálnia úgy elhelyezni az ábrán, hogy mindig a bal felső sarokban (jobbról balra író nyelvek esetében a jobb felső sarokban) jelenjenek meg. Ez azért fontos, mert az átméretezés jellemzően a jobb alsó sarokból kiindulva a felső/bal oldal vagy alulról felfelé haladva történik.

Az alábbi képen a balra átméretezett böngészőablakban található ábra levágását szemléltetjük:

![][6]

Ugyanez felfelé átméretezett böngészőablak esetében:

![][7]

## <a name="what-elements-on-the-page-can-i-customize"></a>Melyek az oldal testreszabható elemei?
A bejelentkezési oldal alábbi elemei szabhatók testre:

![][5]

| Oldalelem | Helye az oldalon |
|:--- | --- |
| Szalagcímembléma |Az oldal jobb felső részén látható. Lecseréli a bejelentkezés célhelyének (például Office 365 vagy Azure) emblémáját. |
| Nagy méretű ábra/háttérszín |Az oldal bal oldali részén látható. Lecseréli a bejelentkezés célhelyének képét. Alacsony sávszélességű kapcsolat vagy keskeny képernyő használatakor a nagy ábrák helyén a háttérszín jelenhet meg. |
| Bejelentkezve szeretnék maradni |A Jelszó szövegmező alatt látható. |
| A bejelentkezési oldal szövege |A fenti ábrán az oldal lábléce látható, ahol hasznos információkat tehet közzé, mielőtt még valaki bejelentkezne munkahelyi vagy iskolai fiókjával. Érdemes lehet például itt elhelyezni az ügyfélszolgálat telefonszámát vagy egy jogi nyilatkozatot. |

> [!NOTE]
> Ezen elemek egyike sem kötelező. Ha például megad szalagcímemblémát, de nagy méretű ábrát nem, a bejelentkezési oldalon az embléma és a célhelyhez tartozó ábra jelenik meg (ez esetben az Office 365 kaliforniai főutat ábrázoló képe).
> 
> 

A bejelentkezési oldalon a **Bejelentkezve szeretnék maradni** jelölőnégyzet lehetővé teszi a felhasználó számára, hogy a böngészője bezárása és újbóli megnyitása után is bejelentkezve maradjon. Ez nincs hatással a munkamenet élettartamára. Az Azure Active Directory bejelentkezési oldalán elrejtheti a jelölőnégyzetet.

A jelölőnégyzet megjelenése a **KMSI elrejtése** beállításától függ.

![][9]

A jelölőnégyzet elrejtéséhez konfigurálja a beállítást **Rejtett** értékre. 

> [!NOTE]
> A SharePoint Online és az Office 2010 egyes funkciói attól függenek, hogy a felhasználók be tudják-e jelölni ezt a jelölőnégyzetet. Ha ezt a beállítást rejtett értékre konfigurálja, a felhasználóinak további és váratlan bejelentkezési felszólítások jelenhetnek meg.
> 
> 

Az oldalon szereplő összes elem honosítható. A testreszabási összetevők „alapértelmezett” készletének összeállítását követően a különböző területi beállításokhoz további verziókat is konfigurálhat. A különböző elemek szabadon kombinálhatók. Megteheti például a következőt:

* Létrehozhat egy „alapértelmezett” nagy méretű ábrát, amely minden országban használható, majd hozza létre ennek angol és francia verzióját. Ha a böngésző a két nyelv valamelyikére van állítva, akkor az ábra speciális verziója, minden más nyelv esetében pedig az alapértelmezett ábra jelenik meg.
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
3. A felső eszköztáron kattintson a **Konfigurálás** lehetőségre.
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

## <a name="testing-and-examples"></a>Tesztelés és példák
Javasoljuk, hogy mielőtt éles környezetben hajtana végre módosításokat, próbálja ki azokat egy tesztelési bérlőn.

**Annak ellenőrzése, hogy a rendszer életbe léptette-e a márkajelzési beállításokat:**

1. Nyisson meg egy InPrivate vagy Incognito böngésző-munkamenetet.
2. Látogasson el a https://outlook.com/contoso.com címre, ahol a contoso.com helyére a testreszabott tartomány nevét írja be.

Ez a contoso.onmicrosoft.com formátumú tartományok esetében is működik.

A hatékony és megfelelő testreszabási készletek létrehozásában segítségére lehet az alábbi két fiktív testreszabott oldal:

* [http://aka.ms/aaddemo001](http://aka.ms/aaddemo001)
* [http://aka.ms/aaddemo002](http://aka.ms/aaddemo002)

A nyelvspecifikus beállítások teszteléséhez a böngésző alapértelmezett nyelvét a testreszabás során beállított nyelvre kell módosítani. Internet Explorer böngésző használata esetén ezt az **Internetbeállítások** menüben adhatja meg.

## <a name="customizable-elements"></a>Testreszabható elemek
Az Azure AD egyes testreszabható elemei többféleképpen is használhatók. Címtáranként egy vállalati emblémát konfigurálhat, amelyet a bejelentkezési és a hozzáférési panel oldalakon is használhat. Bizonyos testreszabható elemek kizárólag a bejelentkezési oldalon jelennek meg. A különböző testreszabható elemek részletes adatait az alábbi táblázat tartalmazza.

| Név | Leírás | Korlátozások | Javaslatok |
| --- | --- | --- | --- |
| Szalagcímembléma |A szalagcím emblémája a bejelentkezési és a hozzáférési panel oldalakon jelenik meg. |<p>JPG vagy PNG</p><p>60 x 280 képpont</p><p>10 kB</p> |<p>Használja szervezete teljes emblémáját (a piktogramot és az emblémát is beleértve).</p><p>A magassága ne legyen több 30 képpontnál, hogy a mobileszközökön ne legyen szükség görgetősáv használatára.</p><p>A mérete maradjon 4 kB alatt.</p><p>Átlátszó PNG formátumot használjon (nem érdemes feltételezni, hogy a bejelentkezési oldalak háttérszíne minden esetben fehér).</p> |
| Csempeembléma |(jelenleg nem szerepel a bejelentkezési oldalon) A jövőben ez a szöveg helyettesítheti majd különböző helyeken az általános „munkahelyi vagy iskolai fiók” piktogramot. |<p>JPG vagy PNG</p><p>120 x 120 képpont</p><p>10 kB</p> |<p>Legyen egyszerű (kisméretű szöveg nélkül), mivel a rendszer ezt a képet a felére kicsinyítheti. |
| </p> | | | |
| A felhasználónév címkéje a bejelentkezési oldalon |(jelenleg nem szerepel a bejelentkezési oldalon) A jövőben ez a szöveg helyettesítheti majd különböző helyeken az általános „munkahelyi vagy iskolai fiók” karaktersort. Olyasmire állíthatja be, mint például „Contoso-fiók” vagy „Contoso-azonosító”. |<p>Unicode szöveg, legfeljebb 50 karakter</p><p>Csak egyszerű szöveg (hivatkozások és HTML-címkék nélkül)</p> |<p>Legyen rövid és egyszerű.</p><p>Kérdezze meg felhasználóit, hogy általában hogyan hívják a nekik biztosított munkahelyi vagy iskolai fiókot.</p> |
| A bejelentkezési oldal szövege |Ez a sablonszöveg jelenik meg a bejelentkezési oldal űrlapja alatt, és a használatával további utasítások is megjeleníthetők a segítségkérés és a támogatás igénylésének módjáról. |<p>Unicode szöveg, legfeljebb 256 karakter</p><p>Csak egyszerű szöveg (hivatkozások és HTML-címkék nélkül)</p> |Legyen 250 karakternél kevesebb (nagyjából 3 soros szöveg). |
| A bejelentkezési oldal ábrája |Az ábra a bejelentkezési oldalon a bejelentkezési űrlaptól balra látható nagy méretű kép. |<p>JPG vagy PNG</p><p>1420 x 1200</p><p>500 kB</p> |<p>1420 x 1200 képpont.</p><p>Fontos: a mérete legyen a lehető legkisebb, ideálisan 200 kB alatti. Ha a kép túlságosan nagy, az kihatással van a bejelentkezési oldal teljesítményére, amikor a kép nincs gyorsítótárazva.</p><p>A különböző képarányoknak való megfelelés érdekében a rendszer a képet gyakran levágja. Az elsődleges látványelemeket a bal felső sarokban (jobbról balra író nyelvek esetén a jobb felső sarokban) helyezze el, mivel a böngészőablak átméretezése keskenyebb formára az alsó résztől/jobb saroktól a felső rész/bal sarok felé történik.</p> |
| A bejelentkezési oldal háttérszíne |A bejelentkezési oldal háttérszíne a bejelentkezési űrlaptól balra található területen érvényesül. |Hexadecimális formátumú RGB-színnek kell lennie (például: #FFFFFF) |<p>Alacsony sávszélességű kapcsolat használatakor a nagy méretű ábrák helyén a háttérszín jelenhet meg.</p><p>Javasoljuk, hogy a szalagcím emblémájának elsődleges színét válassza háttérszínnek.</p> |

## <a name="next-steps"></a>Következő lépések
* [Bevezetés a Prémium szintű Azure Active Directory használatába](active-directory-get-started-premium.md)
* [A hozzáférési és használati jelentések megtekintése](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-add-company-branding/SignInPage_beforecustomization.png
[2]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization.png
[3]: ./media/active-directory-add-company-branding/SignInPage_mobile_beforecustomization.png
[4]: ./media/active-directory-add-company-branding/SignInPage_mobile_aftercustomization.png
[5]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_elements.png
[6]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedleft.png
[7]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedtop.png
[8]: ./media/active-directory-add-company-branding/APBranding.png
[9]: ./media/active-directory-add-company-branding/hidekmsi.png



<!--HONumber=Nov16_HO2-->


