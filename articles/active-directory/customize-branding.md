---
title: A bejelentkezési oldal testreszabása az Azure Active Directory-bérlő |} Microsoft Docs
description: 'Útmutató: a vállalati arculat megjelenítése a Azure bejelentkezési oldal'
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: article
ms.date: 05/23/2018
ms.author: curtand
ms.reviewer: kexia
custom: it-pro
ms.openlocfilehash: 5573399b33da64df8b9bbf78a9583572cf148d7b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713847"
---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>Gyors üzembe helyezés: Adja hozzá a vállalati arculat megjelenítése a bejelentkezési oldal az Azure ad-ben
A félreértések elkerülése végett számos vállalat igyekszik egységes megjelenést adni az általa kezelt összes webhelynek és szolgáltatásnak. Azure Active Directory (Azure AD) azáltal, hogy a vállalat emblémájának elhelyezésével és egyéni színsémák alkalmazásával a bejelentkezési oldal testreszabása biztosítja ezt a lehetőséget. A bejelentkezési oldal akkor jelenik meg, amikor bejelentkezik a web-alapú például Office 365-öt használó alkalmazások az Azure AD az identitás-szolgáltatóként. Ön a szolgáltatóosztályokkal ezen a lapon adja meg a hitelesítő adatokat.

> [!NOTE]
> * Vállalati arculat megjelenítése csak akkor, ha az Azure AD prémium vagy alapszintű licencet vásárolt, vagy az Office 365-licencre van. Ha egy támogatja a licenctípus megismeréséhez ellenőrizze a [Azure Active Directory árképzési adatai lap](https://azure.microsoft.com/pricing/details/active-directory/).
> 
> * Az Azure AD prémium és alapszintű kiadásai érhetők el a kínai ügyfelek számára az Azure Active Directory világszerte elérhető példányával. Az Azure AD prémium és alapszintű kiadásai jelenleg nem támogatottak Kínában a 21Vianet által az Azure szolgáltatásban. További információkért kérdezze meg velünk a [Azure Active Directory fórumán](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customizing-the-sign-in-page"></a>A bejelentkezési oldal testreszabása

<!--You can customize the following elements on the sign-in page: <attach image>-->

Márkajelzési testreszabások az Azure AD bejelentkezési lapon jelenik meg, amikor a felhasználó hozzáfér egy bérlőspecifikus URL-CÍMMEL, például a vállalat [ *https://outlook.com/contoso.com* ](https://outlook.com/contoso.com).

Például amikor a felhasználók által felkeresett www.office.com, a bejelentkezési oldal bármely vállalati arculat megjelenítése a testreszabások, mert a felhasználó még nem lépett hitelesítő adatok nem mutatja. Miután a felhasználó megadja a felhasználói Azonosítóját, vagy a felhasználói csempe kiválasztása, vállalati arculat megjelenítése jeleníti meg.

> [!NOTE]
> * A tartománynév szerepelnie kell "Aktív" a **tartományok** része az Azure portál, amely a vállalati arculatot konfigurálta. További információkért lásd: [egy egyéni tartománynév hozzáadása](add-custom-domain.md).
> * A bejelentkezési oldal vállalati arculata személyes Microsoft-fiókok bejelentkezési oldalán nem jelenik meg. Ha az alkalmazottak vagy az üzleti vendégek jelentkezzen be személyes Microsoft-fiókkal, a bejelentkezési oldal nem tükrözi a szervezet branding.


### <a name="banner-logo"></a>Szalagcímembléma 

Leírás | Korlátozások | Javaslatok
------- | ------- | ----------
A szalagcím emblémájának jelenik meg a bejelentkezési és a hozzáférési panel oldalakon.<br>A bejelentkezési oldalon az embléma után a felhasználónevet jeleníti meg. | Transzparens JPG vagy PNG<br>Maximális magasságát: 36 képpont<br>Maximális szélessége: 245 képpont | Itt adhat meg a vállalati embléma.<br>Transzparens lemezképet használ. Nem érdemes feltételezni, hogy a háttérben fehér lesz.<br>A kép körül az embléma kitöltés nem adja hozzá, vagy az embléma aránytalanul kis fog kinézni.

### <a name="username-hint"></a>Felhasználónév-emlékeztető   
Leírás | Korlátozások | Javaslatok
------- | ------- | ----------
Ez a beállítás testreszabja a emlékeztető szöveg, a felhasználónév mezőben. | Unicode szöveg, legfeljebb 64 karakter hosszú lehet<br>Csak egyszerű szöveg | Vendégfelhasználók várt jelentkezzen be az alkalmazás a szervezeten kívülről, azt javasoljuk, hogy nem állít be ezt a beállítást.
            
### <a name="sign-in-page-text"></a>A bejelentkezési oldal szövege   
Leírás | Korlátozások | Javaslatok
------- | ------- | ----------
Ez a beállítás a bejelentkezési képernyő alján megjelenik, és további információkat, például a segélyszolgálat segítségét, vagy egy jogi nyilatkozatot telefonszámát kommunikációra használható. | Unicode szöveg, legfeljebb 256 karakter hosszú lehet<br>Csak egyszerű szöveg (hivatkozások és HTML-címkék nélkül)    

### <a name="sign-in-page-image"></a>Bejelentkezési lap képe  
Leírás | Korlátozások | Javaslatok
------- | ------- | ----------
Ez akkor jelenik meg, a háttérben, a bejelentkezési lap van horgonyozva megtekinthető szóköz, és méretezik és vágja ki töltse ki a böngészőablak közepére.    <br>Például a mobiltelefonok keskeny képernyő a lemezkép nem jelenik meg.<br>0.55 átlátszatlanság fekete maszkot használja a képe, amikor a lapon be van töltve. | JPG vagy PNG<br>Kép méretei: 1920 x 1080 képpont<br>A fájl mérete: &lt; 300 KB | <br>A lemezképeket amennyiben nem áll rendelkezésre egy erős tulajdonos fókuszt. A nem átlátszó bejelentkezési képernyő jelenik meg a lemezkép közepére, és terjedhetnek ki valamely része szerint a lemezképet, a böngészőablak méretétől függően.<br>A mérete maradjon kicsi, gyors betöltési idők biztosításához. 

### <a name="sign-in-page-background-color"></a>A bejelentkezési oldal háttérszíne
Leírás | Korlátozások | Javaslatok
------- | ------- | ----------
Ezt a színt használja a kis sávszélességű kapcsolatok használata esetén a háttérkép helyett. | Hexadecimális RGB szín (például: #FFFFFF | Javasoljuk, hogy a szalagcím emblémájának vagy a szervezet megfelelő elsődleges színét.

### <a name="square-logo-image"></a>Négyzetes embléma
Leírás | Korlátozások | Javaslatok
------- | ------- | ----------
A kép jelenik meg a telepítés során új Enterprise Windows 10 rendszerű számítógépeken. Környezetet biztosít az alkalmazottak új munkahelyi Számítógépükhöz beállításakor. A kép jelenik meg a bérlők számára, amelyek [Windows automata](https://blogs.windows.com/business/2017/06/29/delivering-modern-promise-windows-10/?utm_source=dlvr.it&utm_medium=twitter#gDTp1u6q35bvDWIS.97) munkahelyi eszközeit, telepítéséhez és a jelszó megadása a lapok más Windows 10 élményének. | Átlátszó PNG (ajánlott) vagy JPG<br>Kép méretei: 240 x 240 képpont<br>A fájl mérete: &lt; 10 KB | Itt adhat meg a vállalati embléma.<br> Transzparens lemezképet használ.<br>Nem érdemes feltételezni, hogy a háttérben fehér lesz.<br>Kitöltés nem tölti fel az embléma, az ábrán, vagy az embléma aránytalanul kis fog kinézni.

### <a name="show-option-to-remain-signed-in"></a>A bejelentkezve maradás lehetőségének megjelenítése
Leírás | Korlátozások | Javaslatok
------- | ------- | ----------
Az Azure AD-bejelentkezés biztosít a felhasználók, a beállítást, továbbra is jelentkezik be, zárja be és nyissa meg újra a böngészőt. Ez a beállítás eltávolítja ezt a beállítást.<br>Beállítása **nem** elrejtheti a felhasználók ezt a lehetőséget. | &nbsp; | Elrejtés a beállítás nincs hatással a munkamenetek élettartamát.<br>Office 2010 és SharePoint Online néhány funkciójának igényt úgy dönteni, hogy továbbra is a bejelentkezett felhasználók függ. Ha ez a beállítás **nem**, a felhasználók további és váratlan felszólítás bejelentkezési jelenhet meg.

> [!NOTE]
> Ezen elemek egyike sem kötelező. Például ha egy szalagcím emblémájának nem háttérkép adja meg, a bejelentkezési lapon megjelenik az embléma és a célként megadott helyen (például Office 365) háttérképe.

## <a name="add-company-branding-to-your-directory"></a>Vállalati arculat megjelenítése a címtárhoz

1. Jelentkezzen be [az Azure AD felügyeleti központot](https://aad.portal.azure.com) egy olyan fiókkal, amely a bérlő globális rendszergazdája.
2. Válassza ki **Azure Active Directory** > **vállalati arculat** > **szerkesztése**.
  
  ![Egyéni branding megnyitása](./media/customize-branding/navigation-to-branding.png)
3. Módosítsa az elemeket, amelyeket testre szeretne szabni. Ezen elemek egyike sem kötelező.
  
  ![Egyéni branding szerkesztése](./media/customize-branding/edit-branding.png)
4. Amikor elkészült, válassza ki a **mentése**.

A bejelentkezési oldal megjelenik márkajelzési végrehajtott módosítások egy órát is igénybe vehet.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Nyelvspecifikus vállalati arculat megjelenítése a címtárban

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **Azure Active Directory** > **vállalati arculat** > **új nyelvi**.
  
  ![Nyelvspecifikus márkajelzési elemek hozzáadása](./media/customize-branding/add-language.png)
3. Módosítsa az elemeket, amelyeket testre szeretne szabni. Ezen elemek egyike sem kötelező.
4. Amikor elkészült, válassza ki a **mentése**.

A bejelentkezési oldal megjelenik márkajelzési végrehajtott módosítások egy órát is igénybe vehet.

## <a name="next-steps"></a>További lépések
A gyors üzembe helyezés hogyan kell a vállalati arculat megjelenítése az Azure AD-címtár hogy megismerte. 

A következő hivatkozás segítségével konfigurálhatja a vállalati védjegyadatoknak az Azure-portálon az Azure AD-ben.

> [!div class="nextstepaction"]
> [Vállalati arculat konfigurálása](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 