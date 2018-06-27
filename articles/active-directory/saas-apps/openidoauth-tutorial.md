---
title: Az Azure AD-alkalmazásgyűjtemény OpenID/OAuth alkalmazás konfigurálásának lépései |} Microsoft Docs
description: Az Azure AD-alkalmazásgyűjtemény OpenID/OAuth alkalmazás konfigurálásának lépéseit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: eedebb76-e78c-428f-9cf0-5891852e79fb
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 69e9d66458409bbc744416a58ceb508349418a76
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37019553"
---
# <a name="steps-to-configure-an-openidoauth-application-from-azure-ad-app-gallery"></a>Alkalmazás eltávolítása a Azure AD-alkalmazásgyűjtemény OpenID/OAuth konfigurálásáról

## <a name="process-of-open-id-application-addition-from-gallery"></a>Nyissa meg azonosító alkalmazás hozzáadása gyűjteményből folyamata

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb](./media/openidoauth-tutorial/tutorial_general_01.png)

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel](./media/openidoauth-tutorial/tutorial_general_02.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb](./media/openidoauth-tutorial/tutorial_general_03.png)

4. Írja be a keresőmezőbe, **alkalmazásnév**, jelölje be a **alkalmazás szükséges** eredmény panelen és bejelentkezési az alkalmazásnak.

    ![Alkalmazás felvétele](./media/openidoauth-tutorial/addfromgallery.png)

    > [!NOTE]
    > Open ID Connect és az OAuth-alkalmazások hozzáadása gomb alapértelmezés szerint le van tiltva. A bérlői rendszergazda kattintson ide az a **előfizetési** gombra, és adja meg az alkalmazásba hozzájárulás. Amely az alkalmazás fogja lekérni fel az ügyfél bérlői, nem szükséges felvennie explicit módon, és hajtsa végre a konfigurációkat.

    ![Hozzáadás gomb](./media/openidoauth-tutorial/addbutton.png)

5. A bejelentkezési hivatkozásra kattintva, ekkor megnyílik az Azure AD bejelentkezési hitelesítő adatokat tartalmazó oldalt.

6. Sikeres hitelesítést követően felhasználó rendelkezik-e a hozzájárulási hozzájárulási lapról, és ezt követően fogadásához, az alkalmazás kezdőlapja jelenik meg.

    > [!NOTE]
    > Az ügyfelek csak hozzá az alkalmazás egy példánya engedélyezett. Ha már felvett egy, és adja meg a hozzájárulási próbálta újra azt fogja nem lesznek ismét hozzáadva a bérlőben. Ezért logikailag használhatnak csak egy app-példány a bérlőben.

## <a name="authentication-flow-using-openid-connect"></a>Használja az OpenID Connect hitelesítési folyamat

A legalapvetőbb bejelentkezési folyamata a következő lépéseket tartalmazza, mert azok az alábbiakban ismertetettek.

![Használja az OpenID Connect hitelesítési folyamat](./media/openidoauth-tutorial/authenticationflow.png)

* **Több-bérlős alkalmazás** -egy több-bérlős alkalmazáshoz készült, számos szervezet, nem csak egyetlen szervezet. Ezek a független szoftverszállító (ISV) által írt általában szoftver,--szolgáltatás (SaaS) alkalmazások. Több-bérlős alkalmazásokhoz szükség minden egyes könyvtárban, ahol használni fogják őket, regisztrálja őket a felhasználó vagy a rendszergazda jóváhagyását igénylő lesz. A hozzájárulási folyamat akkor kezdődik, amikor egy alkalmazás a könyvtárban van regisztrálva, és a Graph API vagy akár egy másik webes API-k való hozzáférése. Amikor egy felhasználó vagy egy másik szervezet rendszergazdája, iratkozzon fel az alkalmazás használatára, jelenjenek meg ezek egy párbeszédpanelt, amely megjeleníti a az alkalmazás futtatásához szükséges engedélyekkel. A felhasználó vagy a rendszergazda is majd hozzájárul az alkalmazásról, így az alkalmazás hozzáférést biztosít a megadott adatokat, és végül regisztrálja az alkalmazást a címtárban.

    > [!NOTE]
    > Ha az alkalmazás érhető el a felhasználók számára több könyvtárban, szeretné meghatározni, melyik bérlői is a. Egy egybérlős alkalmazást kell csak egy felhasználó a saját címtárban keresse meg, amíg egy több-bérlős alkalmazást az Azure AD egy adott felhasználó az összes könyvtár azonosítania kell. Ennek a feladatnak az Azure AD egy közös hitelesítési végpontot, ahol több-bérlős alkalmazásokat lehet konfigurálni a bejelentkezési kérelmek, helyett egy bérlő-specifikus végpontot biztosít. Ez a végpont [ https://login.microsoftonline.com/common ](https://login.microsoftonline.com/common) összes könyvtár az Azure ad-ben, mivel lehet, hogy a bérlő-specifikus végpont [ https://login.microsoftonline.com/contoso.onmicrosoft.com ](https://login.microsoftonline.com/contoso.onmicrosoft.com). A közös végpont különösen fontos figyelembe venni, amikor az alkalmazás fejlesztése, mert több bérlő kezelésének bejelentkezési, kijelentkezési, és a jogkivonatok érvényesség-ellenőrzése során szükséges logika lesz szüksége.

Alapértelmezés szerint az Azure AD-csoport elősegíti a több-bérlős alkalmazás, amelyek különböző szervezetek között könnyen hozzáférhető, és könnyen használható a hozzájárulási elfogadása után.

## <a name="what-is-consent-framework"></a>Mi az a keretrendszer hozzájárulás?

Az Azure AD hozzájárulási keretrendszer megkönnyíti a több-bérlős web- és natív ügyfél-alkalmazások fejlesztéséhez. Ezek az alkalmazások az Azure AD-bérlő, ahol az alkalmazás regisztrálva van egy eltérő engedélyező bejelentkezési felhasználói fiókokat. Előfordulhat, hogy is webes API-k, például a Microsoft Graph API (eléréséhez Azure Active Directory, az Intune és a szolgáltatások az Office 365-ben) és más Microsoft-szolgáltatásokban API-k, mellett a saját webes API-k eléréséhez szükséges. A keretrendszer egy felhasználó vagy a rendszergazda jogosultságot ad a beleegyezést kérő regisztrálni kell a könyvtárban, amelyek magukban foglalhatják directory adatokhoz hozzáférő alkalmazáshoz való alapul. Miután hozzájárulási kap, az ügyfélalkalmazás fog tudni hívja fel a Microsoft Graph API a felhasználó nevében, és szükség szerint használja.

A [Microsoft Graph API](https://graph.microsoft.io/) hozzáférést biztosít az adatok az Office 365-ben (például a naptárak és az Exchange, helyek és a SharePoint, a dokumentumot a onedrive-ról a OneNote-ba, a tervező számára, Excel munkafüzetekhez feladatok notebookok listák üzenetek stb.), valamint felhasználók és csoportok az Azure AD és más adatok több Microsoft felhőszolgáltatás objektumokat.

Az alábbi lépések bemutatják, hogyan a hozzájárulási tapasztalhat az alkalmazás fejlesztőjének és a felhasználó is működik.

1. Során feltételezzük, hogy egy ügyfél webalkalmazás, amelyet a kérelem konkrét engedélyeket, hogy egy erőforrás vagy API-t. Az Azure-portálon engedélyekre vonatkozó kérései deklarálható konfigurációs időpontban szolgál. A konfigurációs beállításait, például az alkalmazás az Azure AD-regisztrációval részét képezik azok:

    ![Graph API](./media/openidoauth-tutorial/graphapi.png)

2. Vegye figyelembe, hogy az Alkalmazásengedélyek frissítve lett, az alkalmazás fut, és egy felhasználó először a használni kívánt. Először az alkalmazást kell egy engedélyezési kód kérése az Azure AD / végpont hitelesítéséhez. Az engedélyezési kód majd egy új hozzáférést szerezni a és a frissítési token használható.

3. Ha a felhasználó már nem hitelesített, az Azure AD / végpont kér bejelentkezés engedélyezéséhez.

    ![Hitelesítés](./media/openidoauth-tutorial/authentication.png)

4. Miután a felhasználó bejelentkezett, az Azure AD határozza meg a felhasználónak kell jeleníthető meg a hozzájárulási lap. Ez a döntés alapján e a felhasználó (vagy annak szervezeti rendszergazdája) már megadta a kérelem jóváhagyását. Hozzájárulás már nincs megadva, ha az Azure AD kérni fogja a felhasználótól a, és megjeleníti a működéséhez szükséges szükséges engedélyekkel. A hozzájárulási párbeszédpanelen megjelenő engedélykészletüket megegyeznek a delegált engedélyek az Azure-portálon választott.

    ![Hozzájárulás lap](./media/openidoauth-tutorial/consentpage.png)

Néhány engedély is lehet hozzájárulását normál felhasználói, míg más egy Bérlői rendszergazda jóváhagyását.

## <a name="whats-the-difference-between-admin-consent-and-user-consent"></a>Mi az, hogy a rendszergazda jóváhagyását és a felhasználói hozzájárulás közötti különbség?

A rendszergazdák is is beleegyezik a delegált engedélyek egy alkalmazás a felhasználók nevében az Ön bérelt szolgáltatásának. Felügyeleti hozzájárulási megakadályozza, hogy a hozzájárulási párbeszédpanel a bérlő minden felhasználó számára megjelenő, és rendszergazdai szerepkörrel rendelkező felhasználók az Azure portálon hajtható végre. Az alkalmazás beállításait tartalmazó oldalt kattintson a szükséges engedélyek, és kattintson a engedélyt adjon gombra.

![Adja meg az engedélyt](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> A gombra kattintva engedélyt adjon explicit hozzájárulás megadása ADAL.js használó egylapos alkalmazások (SPA) jelenleg szükség. Ellenkező esetben az alkalmazás meghiúsul, ha a hozzáférési jogkivonat van szükség.

Csak alkalmazás engedélyek minden esetben szükséges egy Bérlői rendszergazda jóváhagyását. Ha az alkalmazás az alkalmazás csak engedélyt kér, és a felhasználó megpróbál bejelentkezni az alkalmazás, egy hibaüzenet jelenik meg arról, hogy a felhasználó nem tudja hozzájárulás.

Ha az alkalmazás a rendszergazda jóváhagyását igénylő engedélyek használja, a hitelesítési mód, például egy gombra való kattintást vagy hivatkozás rendelkezik, ahol a rendszergazda a művelet is kezdeményezhető szeretné. A kérelem, az alkalmazás számára ez a művelet nem a szokásos OAuth2/OpenID Connect hitelesítési kérelmet a kérdés is tartalmazó küld = admin_consent lekérdezési karakterláncot. Miután a rendszergazda hozzájárult és az egyszerű szolgáltatás létrehozása az ügyfél-bérlőben, új bejelentkezési kérelmek nem szükséges a parancssor = admin_consent paraméter. A rendszergazda úgy dönt, a kért engedélyeket elfogadhatók, mivel a rendszer bekéri a bérlő nincs más felhasználói hozzájárulás ettől kezdve. A bérlői rendszergazda letilthatja az alkalmazások beleegyezését rendszeres felhasználók. Ha ez a funkció le van tiltva, a rendszergazda hozzájárulás megadása mindig kötelező az alkalmazás a bérlő által használandó. Ha szeretné tesztelni az alkalmazás végfelhasználói hozzájárulási le van tiltva, a konfigurációs kapcsolót a található a [Azure-portálon](https://portal.azure.com/) a a [felhasználói beállítások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) szakaszában **vállalati alkalmazások**

A kérdés = admin_consent paraméter is használható az engedélyeket, nincs szükség a rendszergazda jóváhagyását igénylő alkalmazásokkal. Ha ez akkor használható, például, ha az alkalmazás a számára, ahol a bérlői rendszergazda "előfizet" egy időben, és egyéb rendszer kéri a felhasználóktól a hozzájárulásukat adják meg.

Ha egy alkalmazás rendszergazda jóváhagyását igényli, és egy rendszergazda jelentkezik be a parancssorba nélkül = admin_consent paraméter küldi el, amikor a rendszergazda sikeresen járul hozzá az alkalmazás csak a hozzájuk tartozó felhasználói fiókok vonatkozik. Rendszeres felhasználók továbbra is nem fognak tudni jelentkezni, vagy hogy az alkalmazás. Ez a szolgáltatás akkor hasznos, ha szeretne adni a bérlői rendszergazda Fedezze fel az alkalmazást, mielőtt más felhasználók hozzáférést lehessen.