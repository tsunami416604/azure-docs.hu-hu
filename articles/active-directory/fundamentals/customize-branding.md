---
title: Azure AD-bérlő bejelentkezési oldalának testreszabása | Microsoft Docs
description: Ebben a cikkben megismerkedhet a vállalati arculat hozzáadásának mikéntjével az Azure bejelentkezési oldalán.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 07/20/2018
ms.author: lizross
ms.reviewer: kexia
custom: it-pro
ms.openlocfilehash: 7804d6b0d4a100997fb545e678458424dac6ceed
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39227268"
---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>Rövid útmutató: Vállalati arculat megjelenítése az Azure AD bejelentkezési oldalán
A félreértések elkerülése végett számos vállalat igyekszik egységes megjelenést adni az általa kezelt összes webhelynek és szolgáltatásnak. Az Azure Active Directory (Azure AD) ennek érdekében testreszabhatóvá teszi a bejelentkezési oldal megjelenését a vállalat emblémájának elhelyezésével és egyéni színsémák alkalmazásával. A bejelentkezési oldal az olyan webes alkalmazásokba, például az Office 365-be történő bejelentkezéskor jelenik meg, amelyek identitásszolgáltatóként az Azure AD-t használják. Ezzel az oldallal a hitelesítő adatainak megadásakor találkozik.

> [!NOTE]
> * A vállalati arculat hozzáadása kizárólag akkor érhető el, ha megvásárolta az Azure AD Prémium vagy Alapszintű licencét, vagy egy Office 365-licencet. A licenctípus által támogatott szolgáltatásokkal kapcsolatos információkért tekintse meg az [Azure Active Directory díjszabási információkat tartalmazó oldalát](https://azure.microsoft.com/pricing/details/active-directory/).
> 
> * Az Azure AD Prémium és Alapszintű kiadása az Azure Active Directory világszerte elérhető példányával érhetők el a kínai ügyfelek számára. Az Azure AD Prémium és Alapszintű kiadása jelenleg nem támogatott Kínában a 21Vianet által működtetett Azure-szolgáltatásban. További információért lépjen velünk kapcsolatba az [Azure Active Directory fórumán](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customizing-the-sign-in-page"></a>A bejelentkezési oldal testreszabása

<!--You can customize the following elements on the sign-in page: <attach image>-->

A testreszabott vállalati arculat akkor jelenik meg az Azure AD bejelentkezési oldalán, ha a felhasználó egy bérlőspecifikus URL-címet nyit meg (például: [*https://outlook.com/contoso.com*](https://outlook.com/contoso.com)), vagy az URL-címben átadja a tartományi változót (például: [*https://passwordreset.microsoftonline.com/?whr=contoso.com*](https://passwordreset.microsoftonline.com/?whr=contoso.com)).

Például ha egy felhasználó megnyitja a www.office.com oldalt, a bejelentkezési oldalon nem jelenik meg a testreszabott vállalati arculat, mivel a felhasználó még nem adott meg hitelesítő adatokat. Miután a felhasználó megadja a felhasználói azonosítóját, vagy kiválaszt egy felhasználói csempét, megjelenik a vállalati arculat.

> [!NOTE]
> * A tartománynévnek aktív állapottal kell szerepelnie az Azure Portal **Tartományok** szakaszában, ahol a vállalati arculat konfigurálva lett. További információért lásd az [egyéni tartománynév hozzáadását](add-custom-domain.md) ismertető cikket.
> * A bejelentkezési oldal vállalati arculata személyes Microsoft-fiókok bejelentkezési oldalán nem jelenik meg. Ha az alkalmazottak vagy a vendégek személyes Microsoft-fiókkal jelentkeznek be, a bejelentkezési oldal nem tükrözi a vállalati arculatot.


### <a name="banner-logo"></a>Szalagcímembléma 

Leírás | Korlátozások | Javaslatok
------- | ------- | ----------
A szalagcím emblémája a bejelentkezési és a hozzáférési panel oldalán jelenik meg.<br>A bejelentkezési oldalon az embléma a felhasználónév megadását követően jelenik meg. | Átlátszó JPG vagy PNG<br>Maximális magasság: 36 képpont<br>Maximális szélesség: 245 képpont | Itt használhatja a szervezet emblémáját.<br>Használjon egy átlátszó képet. Ne feltételezze azt, hogy a háttér színe fehér lesz.<br>A kép ne tartalmazzon szegélyt az embléma körül, mert ettől az embléma aránytalanul kicsinek tűnhet.

### <a name="username-hint"></a>Felhasználónévre vonatkozó tipp   
Leírás | Korlátozások | Javaslatok
------- | ------- | ----------
Ez a beállítás testreszabja a felhasználónév mezőben található súgószöveget. | Unicode szöveg, legfeljebb 64 karakter<br>Csak egyszerű szöveg | Ha várható, hogy a szervezeten kívüli vendégfelhasználók is bejelentkeznek majd az alkalmazásba, akkor javasoljuk, hogy ezt a lehetőséget ne állítsa be.
            
### <a name="sign-in-page-text"></a>A bejelentkezési oldal szövege   
Leírás | Korlátozások | Javaslatok
------- | ------- | ----------
Ez a lehetőség a bejelentkezési űrlap alján jelenik meg, és további információkat közölhet, például az ügyfélszolgálat telefonszámát vagy egy jogi nyilatkozatot. | Unicode szöveg, legfeljebb 256 karakter<br>Csak egyszerű szöveg (hivatkozások és HTML-címkék nélkül)    

### <a name="sign-in-page-image"></a>A bejelentkezési oldal képe  
Leírás | Korlátozások | Javaslatok
------- | ------- | ----------
Ez a bejelentkezési oldal hátterében jelenik meg, a látható terület közepére rögzítve, a böngészőablak méretéhez igazítva és vágva.    <br>Keskeny képernyőkön, például mobiltelefonokon ez a kép nem jelenik meg.<br>Az oldal betöltésekor a kép fölé egy 0,55 átlátszatlanságú fekete maszk kerül. | JPG vagy PNG<br>Kép méretei: 1920 x 1080 képpont<br>Fájlméret: &lt; 300 KB | <br>Használjon olyan képet, amely nem fókuszál erősen egy bizonyos tárgyra. Az átlátszatlan bejelentkezési űrlap a kép közepe fölött jelenik meg, és a böngészőablak méretétől függően a kép bármekkora részét kitakarhatja.<br>A gyors oldalbetöltési idők eléréséhez gondoskodjon arról, hogy a fájl mérete kicsi maradjon. 

### <a name="sign-in-page-background-color"></a>A bejelentkezési oldal háttérszíne
Leírás | Korlátozások | Javaslatok
------- | ------- | ----------
Alacsony sávszélességű kapcsolat használatakor ez a háttérszín jelenik meg a háttérkép helyett. | Hexadecimális formátumú RGB-szín (például: #FFFFFF) | Javasoljuk, hogy a szalagcím emblémájának elsődleges színét vagy a szervezet színét válassza háttérszínnek.

### <a name="square-logo-image"></a>Négyzet alakú embléma képe
Leírás | Korlátozások | Javaslatok
------- | ------- | ----------
Ez a kép jelenik meg a telepítés során az új vállalati Windows 10-es számítógépeken. Biztosítja a megfelelő környezetet az alkalmazottaknak az új munkahelyi számítógépek üzembe helyezésekor. A kép azon bérlők számára jelenik meg, akik a [Windows AutoPilot](https://blogs.windows.com/business/2017/06/29/delivering-modern-promise-windows-10/?utm_source=dlvr.it&utm_medium=twitter#gDTp1u6q35bvDWIS.97) segítségével végzik a munkahelyi eszközeik üzembe helyezését, valamint az egyéb Windows 10-változatokban a jelszókérő képernyőn látható. | Átlátszó PNG (előnyben részesített) vagy JPG<br>Kép méretei: 240 x 240 képpont<br>Fájlméret: &lt; 10 KB | Itt használhatja a szervezet emblémáját.<br> Használjon egy átlátszó képet.<br>Ne feltételezze azt, hogy a háttér színe fehér lesz.<br>A kép ne tartalmazzon szegélyt az embléma körül, mert ettől az embléma aránytalanul kicsinek tűnhet.

### <a name="show-option-to-remain-signed-in"></a>A bejelentkezve maradás lehetőségének megjelenítése
Leírás | Korlátozások | Javaslatok
------- | ------- | ----------
Az Azure AD bejelentkezési oldala lehetővé teszi a felhasználó számára, hogy a böngészője bezárása és újbóli megnyitása után is bejelentkezett állapotban maradjon. Ez a beállítás elrejti ezt a lehetőséget.<br>Állítsa be a **Nem** értéket, ha el kívánja rejteni ezt a lehetőséget a felhasználók elől. | &nbsp; | A lehetőség elrejtése nincs hatással a munkamenet élettartamára.<br>A SharePoint Online és az Office 2010 egyes funkciói attól függenek, hogy a felhasználók bejelentkezve tudnak-e maradni. Ha ennél a lehetőségnél a **Nem** értéket adja meg, a felhasználóinak további váratlan bejelentkezési felszólítások jelenhetnek meg.

> [!NOTE]
> Ezen elemek egyike sem kötelező. Ha például megad egy szalagcímemblémát, de háttérkép nélkül, akkor a bejelentkezési oldalon az embléma és a célhelyhez (például az Office 365-höz) tartozó háttérkép jelenik meg.

## <a name="add-company-branding-to-your-directory"></a>Vállalati arculat hozzáadása a címtárhoz

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a bérlő globális rendszergazdája.
2. Válassza az **Azure Active Directory** > **Vállalati védjegyezés** > **Szerkesztés** lehetőséget.
  
  ![Egyéni arculat megnyitása](./media/customize-branding/navigation-to-branding.png)
3. Módosítsa az elemeket, amelyeket testre szeretne szabni. Ezen elemek egyike sem kötelező.
  
  ![Egyéni arculat szerkesztése](./media/customize-branding/edit-branding.png)
4. Amikor elkészült, válassza a **Mentés** lehetőséget.

A bejelentkezési oldal arculatán alkalmazott módosítások megjelenítése akár egy órát is igénybe vehet.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Nyelvspecifikus vállalati arculat hozzáadása a címtárhoz

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza az **Azure Active Directory** > **Vállalati védjegyezés** > **Új nyelv** lehetőséget.
  
  ![Nyelvspecifikus arculati elemek hozzáadása](./media/customize-branding/add-language.png)
3. Módosítsa az elemeket, amelyeket testre szeretne szabni. Ezen elemek egyike sem kötelező.
4. Amikor elkészült, válassza a **Mentés** lehetőséget.

A bejelentkezési oldal arculatán alkalmazott módosítások megjelenítése akár egy órát is igénybe vehet.

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban megismerhette, hogyan adhat hozzá vállalati arculatot az Azure AD-címtárhoz. 

Az alábbi hivatkozásra kattintva az Azure Portal segítségével konfigurálhatja a vállalati arculatot az Azure AD-ben.

> [!div class="nextstepaction"]
> [Vállalati arculat konfigurálása](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 