---
title: A felhasználói felület testreszabása az Azure Active Directory B2C-vel egyéni szabályzatok használatával |} A Microsoft Docs
description: Ismerje meg a felhasználói felület (UI) testreszabása az Azure AD B2C-vel egyéni szabályzatok használata közben.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9908a7cf96c56e414e0a8d7faea0352b60214ea4
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446163"
---
# <a name="azure-active-directory-b2c-configure-ui-customization-in-a-custom-policy"></a>Az Azure Active Directory B2C: Egyéni szabályzat felhasználói felületének testreszabását konfigurálása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Miután elvégezte a cikkben, kell egy regisztrációs és bejelentkezési tartalmazó egyéni házirendekben a meg arculatát és megjelenését. Az Azure Active Directory B2C (Azure AD B2C-vel), megjelenik a megjelenő HTML és CSS tartalom szinte teljes hozzáférés a felhasználók számára. Egyéni szabályzat használatakor konfigurálnia felhasználói felületének testreszabását XML-vezérlők használata az Azure Portal helyett. 

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, végezze el a [Ismerkedés az egyéni szabályzatok](active-directory-b2c-get-started-custom.md). -Előfizetés, és jelentkezzen be helyi fiókot működő egyéni házirendet kell rendelkeznie.

## <a name="page-ui-customization"></a>Oldal-UI testreszabása

A lap felhasználói felületének testreszabása szolgáltatással, testre szabhatja a megjelenését és működését minden olyan egyéni szabályzatot. Márka és vizuális konzisztensek az alkalmazás és az Azure AD B2C-vel is fenntarthat.

Íme a működési elv: Azure AD B2C az ügyfél böngészőjében kódja fut, és egy modern néven ismert megközelítés [eltérő eredetű erőforrások megosztása (CORS)](http://www.w3.org/TR/cors/). Először is a testre szabott HTML-tartalmat tartalmazó egyéni házirendekben a megadhatja egy URL-címet. Az Azure AD B2C felhasználói felületi elemeket, hogy be töltve az URL-címről, és ezután megjeleníti az ügyfélnek az oldal HTML-tartalmakat a egyesíti.

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

>[!NOTE]
>Próbálja ki a felhasználói felület-testreszabási funkcióról a minta HTML és CSS-tartalom használatával szeretne? Összeállítottunk [egy egyszerű segédeszköze](active-directory-b2c-reference-ui-customization-helper-tool.md) , amely feltölti és beállítja a minta tartalmat a Blob storage-fiókjában. Ha az eszközt használ, lépjen tovább [a regisztrálási vagy bejelentkezési egyéni szabályzat módosításához](#modify-your-sign-up-or-sign-in-custom-policy).

1. Az a **tárolási** panel alatt **beállítások**, nyissa meg **CORS**.
2. Kattintson a **Hozzáadás** parancsra.
3. A **engedélyezett eredetek**, írjon be egy csillag (\*).
4. Az a **engedélyezett műveletek** legördülő listában, válassza ki mindkét **első** és **beállítások**.
5. A **engedélyezett fejlécek**, írjon be egy csillag (\*).
6. A **közzétett fejlécek**, írjon be egy csillag (\*).
7. A **maximális időtartam (másodperc)**, típus **200**.
8. Kattintson a **Hozzáadás** parancsra.

## <a name="test-cors"></a>A CORS tesztelése

Ellenőrizze, hogy készen áll az alábbiak szerint:

1. Nyissa meg a [www.test-cors.org](http://www.test-cors.org/) webhelyét, majd illessze be az URL-címet a **távoli URL-cím** mezőbe.
2. Kattintson a **kérés küldése a**.  
    Ha hibaüzenetet kap, ellenőrizze, hogy a [CORS-beállítások](#configure-cors) helyes-e. Szüntesse meg a böngésző gyorsítótárát, vagy nyisson meg egy InPrivate-böngészési munkamenetet Ctrl + Shift + P billentyűkombináció lenyomásával is igényelhet.

## <a name="modify-your-sign-up-or-sign-in-custom-policy"></a>A regisztrálási vagy bejelentkezési egyéni házirend módosítása

A legfelső szintű alatt *\<TrustFrameworkPolicy\>* címke, keresse meg *\<BuildingBlocks\>* címke. Belül a *\<BuildingBlocks\>* címkék, adjon hozzá egy *\<ContentDefinitions\>* másolja az alábbi példa a címke. Cserélje le *your_storage_account* a tárfiók nevére.

  ```xml
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.idpselections">
        <LoadUri>https://{your_storage_account}.blob.core.windows.net/customize-ui.html</LoadUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0</DataUri>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>
  ```

## <a name="upload-your-updated-custom-policy"></a>A frissített egyéni szabályzat feltöltése

1. Az a [az Azure portal](https://portal.azure.com), [váltson át az Azure AD B2C-bérlője kontextusában](active-directory-b2c-navigate-to-b2c-context.md), majd nyissa meg a **Azure AD B2C-vel** panelen.
2. Kattintson a **összes szabályzat**.
3. Kattintson a **szabályzat feltöltése**.
4. Töltse fel `SignUpOrSignin.xml` együtt a *\<ContentDefinitions\>* címkék, melyek korábban hozzáadott.

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
