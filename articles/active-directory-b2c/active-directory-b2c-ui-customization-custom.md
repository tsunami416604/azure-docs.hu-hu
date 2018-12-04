---
title: Az alkalmazás egyéni szabályzat használata az Azure Active Directory B2C a felhasználói felület testreszabása |} A Microsoft Docs
description: Ismerje meg az egyéni szabályzat használata az Azure Active Directory B2C felhasználói felület testreszabása.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b0e24da86d253139a85e792bf3c59d777cf5db6a
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52833941"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Az alkalmazás egyéni szabályzat használata az Azure Active Directory B2C a felhasználói felület testreszabása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Miután elvégezte a cikkben, kell egy regisztrációs és bejelentkezési tartalmazó egyéni házirendekben a meg arculatát és megjelenését. Az Azure Active Directory B2C (Azure AD B2C-vel), megjelenik a megjelenő HTML és CSS tartalom szinte teljes hozzáférés a felhasználók számára. Egyéni szabályzat használatakor konfigurálnia felhasználói felületének testreszabását XML-vezérlők használata az Azure Portal helyett. 

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a [egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md). -Előfizetés, és jelentkezzen be helyi fiókot működő egyéni házirendet kell rendelkeznie.

## <a name="page-ui-customization"></a>Oldal-UI testreszabása

A lap felhasználói felületének testreszabása szolgáltatással, testre szabhatja a megjelenését és működését minden olyan egyéni szabályzatot. Lehetősége van fenntartani az alkalmazása és az Azure AD B2C közötti márkabeli és vizuális egységességet is.

Íme a működési elv: Azure AD B2C az ügyfél böngészőjében kódja fut, és egy modern néven ismert megközelítés [eltérő eredetű erőforrások megosztása (CORS)](https://www.w3.org/TR/cors/). Először is a testre szabott HTML-tartalmat tartalmazó egyéni házirendekben a megadhatja egy URL-címet. Az Azure AD B2C egyesíti a felhasználói felület elemeit az URL-ről betöltött HTML-tartalommal, majd megjeleníti az oldalt az ügyfélnek.

## <a name="create-your-html5-content"></a>A HTML5-alapú tartalom létrehozása

HTML címét a termék márkáját nevű tartalom létrehozása.

1. Másolja az alábbi HTML-kódrészlet. Helytelen formátumú legyen üres elem a HTML5-alapú nevű *\<div azonosítója = "api"\>\</div\>* belül található a *\<törzs\>* címkék. Ez az elem azt jelzi, ahol az Azure AD B2C-t tartalmak beszúrandó.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

   >[!NOTE]
   >Biztonsági okokból JavaScript használata jelenleg le van tiltva a testreszabáshoz.

2. Illessze be a másolt kódrészletet egy szövegszerkesztőben, és mentse a fájlt az *testreszabása ui.html*.

## <a name="create-an-azure-blob-storage-account"></a>Azure Blob storage-fiók létrehozása

>[!NOTE]
> Ez a cikk az Azure Blob storage használjuk tartalom üzemeltetéséhez. Dönthet úgy, hogy a tartalmakat a webkiszolgáló üzemeltetésére, de meg kell [CORS engedélyezése a webkiszolgálón](https://enable-cors.org/server.html).

A HTML-tartalmat, Blob Storage-tároló, tegye a következőket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Az a **Hub** menüjében válassza **új** > **tárolási** > **tárfiók**.
3. Adjon meg egy egyedi **neve** a tárfiók számára.
4. **Üzemi modell** maradhat **Resource Manager**.
5. Változás **fiók típusa** való **a Blob storage-**.
6. **Teljesítmény** maradhat **Standard**.
7. **Replikációs** maradhat **RA-GRS**.
8. **Hozzáférési szint** maradhat **gyors**.
9. **A Storage service encryption** maradhat **letiltott**.
10. Válassza ki a **előfizetés** a tárfiók számára.
11. Hozzon létre egy **erőforráscsoport** , vagy válasszon ki egy meglévőt.
12. Válassza ki a **földrajzi hely** a tárfiók számára.
13. Kattintson a **Létrehozás** parancsra a tárfiók létrehozásához.  
    Az üzembe helyezés befejezése után a **tárfiók** automatikusan megnyílik a panel.

## <a name="create-a-container"></a>Tároló létrehozása

Blob storage-ban hozzon létre egy nyilvános tárolóban, tegye a következőket:

1. Kattintson a **áttekintése** fülre.
2. Kattintson a **tároló**.
3. A **neve**, típus **$root**.
4. Állítsa be **típus eléréséhez** való **Blob**.
5. Kattintson a **$root** , nyissa meg az új tárolóhoz.
6. Kattintson a **Feltöltés** gombra.
7. Kattintson a Tovább gombra a mappa ikont **válasszon ki egy fájlt**.
8. Lépjen a **testreszabása ui.html**, amely korábban a létrehozott a [oldal-UI testreszabása](#the-page-ui-customization-feature) szakaszban.
9. Kattintson a **Feltöltés** gombra.
10. Válassza ki a feltöltött testreszabás-ui.html blob.
11. A **URL-cím**, kattintson a **másolási**.
12. Egy böngészőben beilleszteni a másolt URL-címet, és nyissa meg a helyet. Ha a hely nem érhető el, ellenőrizze, hogy a tároló hozzáférési típus értéke **blob**.

## <a name="configure-cors"></a>A CORS konfigurálása

A Blob storage konfigurálhatja az eltérő eredetű erőforrások megosztása az alábbiak szerint:

1. Válassza a menüben **CORS**.
2. A **engedélyezett eredetek**, adja meg `your-tenant-name.b2clogin.com`. Cserélje le `your-tenant-name` az Azure AD B2C-bérlő nevével. Például: `fabrikam.b2clogin.com`. Csak kisbetűk használata a bérlő nevének megadásakor kell.
3. A **engedélyezett metódusok**, válassza ki mindkét `GET` és `OPTIONS`.
4. A **engedélyezett fejlécek**, írjon be egy csillagot (*).
5. A **közzétett fejlécek**, írjon be egy csillagot (*).
6. A **maximális életkora**, adja meg a 200-as.
7. Kattintson a **Save** (Mentés) gombra.

## <a name="test-cors"></a>A CORS tesztelése

Ellenőrizze, hogy készen áll az alábbiak szerint:

1. Nyissa meg a [www.test-cors.org](https://www.test-cors.org/) webhelyét, majd illessze be az URL-címet a **távoli URL-cím** mezőbe.
2. Kattintson a **kérés küldése a**.  
    Ha hibaüzenetet kap, ellenőrizze, hogy a [CORS-beállítások](#configure-cors) helyes-e. Szüntesse meg a böngésző gyorsítótárát, vagy nyisson meg egy InPrivate-böngészési munkamenetet Ctrl + Shift + P billentyűkombináció lenyomásával is igényelhet.

## <a name="modify-the-extensions-file"></a>A bővítmények fájl módosítása

Konfigurálhatja a felhasználói felületének testreszabását, másolja a **ContentDefinition** és annak alárendelt elemei a bővítmények fájl az alap-fájlból.

1. Nyissa meg a szabályzat alapszintű fájlt. Ha például *TrustFrameworkBase.xml*.
2. Keresse meg és másolja ki a teljes tartalmát a **ContentDefinitions** elemet.
3. Nyissa meg a kiterjesztésű fájlt. Ha például *TrustFrameworkExtensions.xml*. Keresse meg a **BuildingBlocks** elemet. Ha az elem nem létezik, adja hozzá.
4. Illessze be a teljes tartalmát a **ContentDefinitions** gyermekeként kimásolt elem a **BuildingBlocks** elemet. 
5. Keresse meg a **ContentDefinition** tartalmazó `Id="api.signuporsignin"` XML-másolta.
6. Módosítsa az értéket a **LoadUri** URL-címét a tárolóba feltöltött HTML-fájl. Például "https://mystore1.azurewebsites.net/b2c/customize-ui.html.
    
    Az egyéni házirendet a következőhöz hasonlóan kell kinéznie:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

7. Mentse a bővítmények fájlt.

## <a name="upload-your-updated-custom-policy"></a>A frissített egyéni szabályzat feltöltése

1. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
4. Válassza ki **identitás-kezelőfelületi keretrendszer**.
2. Kattintson a **összes szabályzat**.
3. Kattintson a **szabályzat feltöltése**.
4. Töltse fel a korábban módosított bővítmények fájlt.

## <a name="test-the-custom-policy-by-using-run-now"></a>Az egyéni házirend tesztelése használatával **Futtatás most**

1. Az a **Azure AD B2C-vel** panelen lépjen a **összes szabályzat**.
2. Válassza ki az egyéni házirend feltöltött, majd kattintson a **Futtatás most** gombra.
3. Meg kell tudni regisztrálnia kell az e-mail-cím.

## <a name="reference"></a>Leírások

Mintasablonok felhasználói felületének testreszabását itt találja:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

A sample_templates/wingtip mappa a következő HTML-fájlokat tartalmazza:

| HTML5-sablon | Leírás |
|----------------|-------------|
| *phonefactor.html* | Ez a fájl sablonként használni a multi-factor Authentication hitelesítés lap. |
| *resetpassword.html* | Ezt a fájlt használja a sablont egy elfelejtett jelszó oldal. |
| *selfasserted.html* | Sablonként használni ezt a fájlt egy közösségi fiók regisztrálására szolgáló oldal, egy helyi fiók regisztrálási oldala vagy egy helyi fiók bejelentkezési oldalára. |
| *unified.html* | Ez a fájl sablonként használni egy egyesített regisztrálási vagy bejelentkezési oldal. |
| *updateprofile.html* | Ez a fájl sablonként használni a profil frissítés laphoz. |

Az a [módosítása a regisztrálási vagy bejelentkezési egyéni szabályzat szakasz](#modify-your-sign-up-or-sign-in-custom-policy), konfigurálta a tartalom definíciója `api.idpselections`. A teljes körű tartalom az Azure AD B2C-identitás-kezelőfelületi keretrendszer és a hozzájuk tartozó leírások által elfogadottakkal szerepkördefiníció-azonosítók az alábbi táblázatban vannak:

| Tartalomdefiníció azonosítója | Leírás | 
|-----------------------|-------------|
| *api.error* | **Hibalap**. Ez az oldal jelenik meg kivételt vagy hibát észlelt. |
| *api.idpselections* | **Identitásszolgáltató kiválasztása oldal**. Ezen a lapon identitásszolgáltatókat, amelyek a felhasználó kiválaszthat a bejelentkezés során listáját tartalmazza. Ezek a lehetőségek állnak a vállalati identitás-szolgáltatóktól, például a Facebook és a Google + közösségi identitásszolgáltatókat, vagy helyi fiókot. |
| *api.idpselections.signup* | **Identity provider adatgyűjtésre vonatkozó felhasználói előfizetési**. Ezen a lapon identitásszolgáltatókat, amelyek a felhasználók választhatnak a regisztráció során listáját tartalmazza. Ezek a lehetőségek állnak a vállalati identitás-szolgáltatóktól, például a Facebook és a Google + közösségi identitásszolgáltatókat, vagy helyi fiókot. |
| *api.localaccountpasswordreset* | **Elfelejtett jelszó oldal**. Ez az oldal a felhasználó által végrehajtandó kezdeményezni a jelszó-visszaállítás űrlap tartalmazza.  |
| *api.localaccountsignin* | **Helyi fiók bejelentkezési oldal**. Ez az oldal számára egy egy e-mail-cím vagy felhasználónév alapján helyi fiókkal történő bejelentkezés bejelentkezési űrlap tartalmazza. Az űrlap egy bemeneti szövegmezőt és a jelszó mező tartalmazhat. |
| *api.localaccountsignup* | **Helyi fiók regisztrálási oldala**. Ez az oldal tartalmazza egy regisztrációs űrlap egy helyi fiókot, egy e-mail-cím vagy felhasználónév alapján. Az űrlap különböző bemeneti vezérlők, például egy bemeneti szövegmezőt, egy bejegyzés mezőben, választógomb, egyszeri kiválasztásos legördülő listák és többszörös kiválasztási jelölőnégyzetet is tartalmazhat. |
| *api.phonefactor* | **A multi-factor authentication-oldal**. Ezen a lapon felhasználók ellenőrizheti azok telefonszámok (a szöveges vagy szóbeli) regisztrálási vagy bejelentkezési során. |
| *api.selfasserted* | **Közösségi fiók regisztrálására szolgáló oldal**. Ezen a lapon található egy bejelentkezési űrlap kitöltése, hogy a felhasználók hajtsa végre, amikor regisztrál egy közösségi identitásszolgáltatót például Facebook vagy a Google + a meglévő fiók használatával. Ezen a lapon a hasonló az előző közösségi fiók regisztrációs oldalon, a jelszó számbeviteli mezők kivételével. |
| *api.selfasserted.profileupdate* | **Frissítés profillapján**. Ez az oldal tartalmaz egy képernyő, amelyen a felhasználók saját profil frissítése. Ez az oldal hasonlít a közösségi fiók regisztrációs oldalon, a jelszó számbeviteli mezők kivételével. |
| *api.signuporsignin* | **Egyesített regisztrálási vagy bejelentkezési oldal**. Ezen a lapon mind a regisztrációs és jelentkezzen be a vállalati identitás-szolgáltatóktól, például Facebook vagy a Google + vagy a helyi fiókok közösségi identitásszolgáltatókat használó felhasználók kezeli.  |

## <a name="next-steps"></a>További lépések

További információ a felhasználói felületi elemeket, testre szabható: [beépített szabályzatok felhasználói felületének testreszabását az útmutatót](active-directory-b2c-reference-ui-customization.md).
