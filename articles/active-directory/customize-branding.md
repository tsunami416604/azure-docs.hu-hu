---
title: "Az Azure Active Directoryban a bejelentkezési oldal testreszabása |} Microsoft Docs"
description: "Útmutató: a vállalati arculat megjelenítése a Azure bejelentkezési oldal"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: curtand
ms.reviewer: kexia
custom: it-pro
ms.openlocfilehash: 41f4ceea4842303760c5b156e90bd9e0746a7825
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2017
---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>Gyors üzembe helyezés: Adja hozzá a vállalati arculat megjelenítése a bejelentkezési oldal az Azure ad-ben
A félreértések elkerülése végett számos vállalat igyekszik egységes megjelenést adni az általa kezelt összes webhelynek és szolgáltatásnak. Azure Active Directory (Azure AD) azáltal, hogy a vállalat emblémájának elhelyezésével és egyéni színsémák alkalmazásával a bejelentkezési oldal testreszabása biztosítja ezt a lehetőséget. A bejelentkezési oldal az Office 365 vagy más webes alkalmazásokat az identitás-szolgáltatóként az Azure AD-t használó bejelentkezéskor megjelenő lap. Ön a szolgáltatóosztályokkal ezen a lapon adja meg a hitelesítő adatokat.

> [!NOTE]
> * Vállalati arculat megjelenítése csak akkor, ha frissített a prémium vagy alapszintű kiadására az Azure AD, vagy az Office 365-licencre van. Ha egy támogatja a licenctípus megismeréséhez ellenőrizze a [Azure Active Directory árképzési adatai lap](https://azure.microsoft.com/pricing/details/active-directory/).
> 
> * Az Azure Active Directory Prémium és Alapszintű kiadásai az Azure Active Directory világszerte elérhető példányával érhetők el a kínai ügyfelek számára. Az Azure Active Directory Prémium és Alapszintű kiadásai jelenleg nem támogatottak Kínában a 21Vianet által működtetett Microsoft Azure szolgáltatásban. További információkért lépjen velünk kapcsolatba az [Azure Active Directory fórumán](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customizing-the-sign-in-page"></a>A bejelentkezési oldal testreszabása

<!--You can customize the following elements on the sign-in page: <attach image>-->

Márkajelzési testreszabások az Azure AD bejelentkezési lapon jelenik meg, amikor a felhasználó hozzáfér egy bérlőspecifikus URL-CÍMMEL, például a vállalat [ *https://outlook.com/contoso.com*](https://outlook.com/contoso.com).

Amikor a felhasználók által felkeresett www.office.com például általános URL-címet, a bejelentkezési oldal vállalati arculat megjelenítése a testreszabások, mert a rendszer nem ismeri a felhasználó, aki nem tartalmaz. Vállalati arculat megjelenítése felhasználók adja meg a felhasználói Azonosítóját, vagy a felhasználói csempe kiválasztása után jelennek meg.

> [!NOTE]
> * A tartománynév szerepelnie kell "Aktív" a **tartományok** része az Azure portál, amely a vállalati arculatot konfigurálta. További információkért lásd: [egy egyéni tartománynév hozzáadása](add-custom-domain.md).
> * A bejelentkezési oldal vállalati arculata személyes Microsoft-fiókok bejelentkezési oldalán nem jelenik meg. Ha az alkalmazottak vagy az üzleti vendégek jelentkezzen be személyes Microsoft-fiókkal, a bejelentkezési oldal nem tükrözi a szervezet branding.


### <a name="banner-logo"></a>Szalagcímembléma 

Leírás | Korlátozások | Javaslatok
------- | ------- | ----------
A szalagcím emblémájának jelenik meg a bejelentkezési és a hozzáférési panel oldalakon.<br>A bejelentkezési oldalon tartalmazza után a felhasználó szervezete állapota határozza meg, általában a felhasználónév megadása után.  | Transzparens JPG vagy PNG<br>Maximális magasságát: 36 képpont<br>Maximális szélessége: 245 képpont | Itt adhat meg a vállalati embléma.<br>Transzparens lemezképet használ. Nem érdemes feltételezni, hogy a háttérben fehér lesz.<br>A kép körül az embléma kitöltés nem adja hozzá, vagy az embléma aránytalanul kis fog kinézni.

### <a name="username-hint"></a>Felhasználónév-emlékeztető   
Leírás | Korlátozások | Javaslatok
------- | ------- | ----------
Ez testreszabja a emlékeztető szöveg, a felhasználónév mezőben. | Unicode szöveg, legfeljebb 64 karakter hosszú lehet<br>Csak egyszerű szöveg | Azt javasoljuk, hogy nincs megadva ez Ha vendégfelhasználók jelentkezzen be az alkalmazás a szervezeten kívülről.
            
### <a name="sign-in-page-text"></a>A bejelentkezési oldal szövege   
Leírás | Korlátozások | Javaslatok
------- | ------- | ----------
A bejelentkezési űrlap alján jelenik meg, és további információkat, például a segélyszolgálat segítségét, vagy egy jogi nyilatkozatot telefonszámát kommunikációra használható. | Unicode szöveg, legfeljebb 256 karakter hosszú lehet<br>Csak egyszerű szöveg (hivatkozások és HTML-címkék nélkül)   

### <a name="sign-in-page-image"></a>Bejelentkezési lap képe  
Leírás | Korlátozások | Javaslatok
------- | ------- | ----------
Ez a háttérben, a bejelentkezési oldal jelenik meg, a megtekinthető terület közepére van horgonyozva méretezhető és körülvágása kitöltse a böngészőablakot.    <br>Például a mobiltelefonok keskeny képernyő a lemezkép nem jelenik meg.<br>Egy fekete maszkra 0.55 átlátszatlanság fogja őket alkalmazni, a kép kód az oldal betöltésekor. | JPG vagy PNG<br>Kép méretei: 1920 x 1080 képpont<br>A fájl mérete: &lt; 300 KB | <br>A lemezképeket amennyiben nem áll rendelkezésre egy erős tulajdonos fókuszt. A nem átlátszó bejelentkezési képernyő jelenik meg a lemezkép közepére, és terjedhetnek ki a böngészőablak méretétől függően a lemezkép valamely része.<br>A fájlméret gyors betöltési idők biztosításához lehető legkisebb érdekében. 

### <a name="background-color"></a>Háttérszín
Leírás | Korlátozások | Javaslatok
------- | ------- | ----------
Ezt a színt használja a kis sávszélességű kapcsolatok használata esetén a háttérkép helyett. | Hexadecimális RGB szín (például: #FFFFFF | Javasoljuk, hogy a szalagcím emblémájának vagy a szervezet megfelelő elsődleges színét.

### <a name="show-option-to-remain-signed-in"></a>Gombra a beállítás megjelenítése
Leírás | Korlátozások | Javaslatok
------- | ------- | ----------
Az Azure AD bejelentkezés a felhasználói lehetőséget ad arra, továbbra is, ha azok zárja be és nyissa meg újra a böngészőjükben bejelentkezett. Ezzel a elrejtése ezt a beállítást.<br>Ez a "Nem" gombra, hogy ezt a beállítást, a felhasználók a elrejtése. | &nbsp; | Ez nem befolyásolja a munkamenetek élettartamát.<br>Office 2010 és SharePoint Online néhány funkciójának igényt úgy dönteni, hogy továbbra is a bejelentkezett felhasználók függ. Ha ez rejtettre van beállítva, akkor a felhasználók további és váratlan felszólítás a bejelentkezési jelenhet meg.

> [!NOTE]
> Ezen elemek egyike sem kötelező. Például ha egy szalagcím emblémájának nem háttérkép adja meg, a bejelentkezési lapon megjelenik az embléma és a célként megadott helyen (például Office 365) háttérképe.

## <a name="add-company-branding-to-your-directory"></a>Vállalati arculat megjelenítése a címtárhoz

1. Jelentkezzen be [az Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **további szolgáltatások**, adja meg **felhasználók és csoportok** a szövegmezőbe, majd válassza ki azt a **Enter**.

   ![Nyitó felhasználók kezelése](./media/customize-branding/user-management.png)
3. Az a **felhasználók és csoportok** panelen válassza **vállalati arculat**.
4. Az a **felhasználók és csoportok – a vállalati arculat** panelen válassza a **szerkesztése** parancsot.

    ![Egyéni branding szerkesztése](./media/customize-branding/edit-branding.png)
5. Módosítsa az elemeket, amelyeket testre szeretne szabni. Ezen elemek egyike sem kötelező.
6. Kattintson a **Save** (Mentés) gombra.

A bejelentkezési oldal megjelenik márkajelzési végrehajtott módosítások egy órát is igénybe vehet.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Nyelvspecifikus vállalati arculat megjelenítése a címtárban

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **felhasználók és csoportok** a szövegmezőbe, majd válassza ki azt a **Enter**.

   ![Nyitó felhasználók kezelése](./media/customize-branding/user-management.png)
3. Az a **felhasználók és csoportok** panelen válassza **vállalati arculat**.
4. Az a **felhasználók és csoportok – a vállalati arculat** panelen válassza a **nyelv hozzáadása** parancsot.

    ![Nyelvspecifikus márkajelzési elemek hozzáadása](./media/customize-branding/add-language.png)
5. Módosítsa az elemeket, amelyeket testre szeretne szabni. Ezen elemek egyike sem kötelező.
6. Kattintson a **Save** (Mentés) gombra.

A bejelentkezési oldal megjelenik márkajelzési végrehajtott módosítások egy órát is igénybe vehet.

## <a name="next-steps"></a>Következő lépések
A gyors üzembe helyezés hogyan kell a vállalati arculat megjelenítése az Azure AD-címtár hogy megismerte. 

A következő hivatkozás segítségével konfigurálhatja a vállalati védjegyadatoknak az Azure-portálon az Azure AD-ben.

> [!div class="nextstepaction"]
> [Vállalati arculat konfigurálása](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 