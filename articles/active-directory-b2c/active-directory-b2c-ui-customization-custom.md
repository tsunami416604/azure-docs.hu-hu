---
title: "Egyéni házirendek – az Azure AD B2C használatával a felhasználói felület testreszabása |} Microsoft Docs"
description: "További tudnivalók a felhasználói felület (UI) testreszabása, miközben egyéni házirendekkel használhatja az Azure AD B2C."
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: mtillman
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: c430b488016f038ed1d7a67a8d52c057df1ea40e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-configure-ui-customization-in-a-custom-policy"></a>Az Azure Active Directory B2C: Egyéni házirendek konfigurálása a felhasználói felület testreszabása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk befejezése után fog regisztráció és bejelentkezés egyéni házirendet a márka és megjelenését. Azure Active Directory B2C (az Azure AD B2C), kap a HTML- és CSS tartalom bemutatott szinte teljes hozzáférés a felhasználók számára. Egyéni házirend használata esetén konfigurálnia felhasználói felület testreszabásával XML-vezérlők használata az Azure portálon helyett. 

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, fejezze be a [Ismerkedés az egyéni házirendek](active-directory-b2c-get-started-custom.md). A regisztrációt, és jelentkezzen be helyi fiókok működő egyéni házirendet kell rendelkeznie.

## <a name="page-ui-customization"></a>Lap felhasználói felületének testreszabása

A lap felhasználói felületének testreszabása szolgáltatás segítségével testre szabhatja a bármely egyéni házirend megjelenését és működését. Kezelheti a márka és egységes megjelenést az alkalmazás és az Azure AD B2C között is.

Hogyan működik ez: Azure AD B2C az ügyfél böngészőjében kód fut, és a modern megközelítés nevű [Cross-Origin Resource Sharing (CORS)](http://www.w3.org/TR/cors/). Első lépésként ad meg URL-címet az egyéni házirendekben a testreszabott HTML-tartalmakat. Az Azure AD B2C egyesíti a HTML-tartalmakat, be töltve az URL-címről, majd a oldalát jeleníti meg, hogy az ügyfél felhasználói felületének elemmel.

## <a name="create-your-html5-content"></a>A HTML5 tartalmának létrehozása

Hozzon létre HTML tartalom a termék márkáját nevű cím.

1. Másolja az alábbi HTML-részlet. Szabályos üres elem a HTML5 nevű  *\<div id = "api"\>\</div\>*  belül található a  *\<törzs\>*  címkék. Ez az elem azt jelzi, ahol az Azure AD B2C tartalmat beszúrni.

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
   >Biztonsági okokból a JavaScript használatát jelenleg le van tiltva a testreszabáshoz.

2. Illessze be a másolt részlet egy szövegszerkesztőben, és mentse a fájlt *testreszabása ui.html*.

## <a name="create-an-azure-blob-storage-account"></a>Egy Azure Blob storage-fiók létrehozása

>[!NOTE]
> Ebben a cikkben a Azure Blob Storage tárolót a tartalom tárolására szolgáló használjuk. Választhat, hogy a tartalmát egy webkiszolgálón, de meg kell [CORS engedélyezése a webkiszolgálón](https://enable-cors.org/server.html).

A HTML-tartalmakat, a Blob Storage tárolóban tárolni, tegye a következőket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Az a **Hub** menüjében válassza **új** > **tárolási** > **tárfiók**.
3. Adjon meg egy egyedi **neve** a tárfiók.
4. **Telepítési modell** maradjanak **erőforrás-kezelő**.
5. Változás **fiók típusa** való **Blob-tároló**.
6. **Teljesítmény** maradjanak **szabványos**.
7. **Replikációs** maradjanak **RA-GRS**.
8. **Hozzáférési szint** maradjanak **gyakran használt adatok**.
9. **Storage szolgáltatás titkosítási** maradjanak **letiltott**.
10. Válassza ki a **előfizetés** a tárfiók.
11. Hozzon létre egy **erőforráscsoport** vagy válasszon egy meglévőt.
12. Válassza ki a **földrajzi hely** a tárfiók.
13. Kattintson a **Létrehozás** parancsra a tárfiók létrehozásához.  
    A telepítés befejezése után a **tárfiók** panel nyílik meg automatikusan.

## <a name="create-a-container"></a>Tároló létrehozása

A Blob-tároló egy nyilvános tároló létrehozásához tegye a következőket:

1. Kattintson a **áttekintése** fülre.
2. Kattintson a **tároló**.
3. A **neve**, típus **$root**.
4. Állítsa be **hozzáférési típus** való **Blob**.
5. Kattintson a **$root** az új tároló megnyitása.
6. Kattintson a **Feltöltés** gombra.
7. Kattintson a Tovább gombra a mappa ikon **válasszon ki egy fájlt**.
8. Ugrás a **testreszabása ui.html**, amely amelyet előzőleg létrehozott a [lap felhasználói felületének testreszabása](#the-page-ui-customization-feature) szakasz.
9. Kattintson a **Feltöltés** gombra.
10. Válassza ki a feltöltött testreszabás-ui.html blob.
11. A **URL-cím**, kattintson a **másolási**.
12. A böngészőben beilleszteni a másolt URL-címet, és nyissa meg a helyhez. Ha a hely nem érhető el, győződjön meg arról, hogy a tároló hozzáférési típus értéke **blob**.

## <a name="configure-cors"></a>A CORS konfigurálása

Konfigurálása a Blob storage eltérő eredetű erőforrások megosztása a következő módon:

>[!NOTE]
>Szeretné, hogy a minta HTML és CSS tartalom használatával a felhasználói felület testreszabása a szolgáltatás kipróbálásához? Mellékelt [egy egyszerű segédeszköze](active-directory-b2c-reference-ui-customization-helper-tool.md) , amely feltölti és konfigurálja a minta tartalmat a Blob storage-fiók. Ha az eszköz használatához ugorjon előre [módosítsa a regisztráció vagy bejelentkezés egyéni házirendet](#modify-your-sign-up-or-sign-in-custom-policy).

1. Az a **tárolási** panel alatt **beállítások**, nyissa meg **CORS**.
2. Kattintson az **Add** (Hozzáadás) parancsra.
3. A **engedélyezett eredeteket**, írja be egy csillag (\*).
4. Az a **engedélyezett műveletek** legördülő listából válassza ki, válassza ki mindkét **beolvasása** és **beállítások**.
5. A **engedélyezett fejlécek**, írja be egy csillag (\*).
6. A **fejlécek kitett**, írja be egy csillag (\*).
7. A **maximális élettartama (másodperc)**, típus **200**.
8. Kattintson az **Add** (Hozzáadás) parancsra.

## <a name="test-cors"></a>A CORS tesztelése

Ellenőrizze, hogy készen áll a következő módon:

1. Lépjen a [teszt-cors.org](http://test-cors.org/) webhelyen, majd illessze be az URL-címet a **távoli URL-cím** mezőbe.
2. Kattintson a **kérés küldése**.  
    Ha hibaüzenetet kap, ellenőrizze, hogy a [CORS-beállítások](#configure-cors) helyes-e. Is szükség lehet a böngésző gyorsítótárat kiürítheti, vagy egy InPrivate-böngészés munkamenet megnyitásához nyomja le a Ctrl + Shift + P.

## <a name="modify-your-sign-up-or-sign-in-custom-policy"></a>A regisztráció vagy bejelentkezés egyéni házirend módosítása

A legfelső szintű alatt  *\<TrustFrameworkPolicy\>*  címke, keresse meg  *\<BuildingBlocks\>*  címke. Belül a  *\<BuildingBlocks\>*  címkék, vegye fel a  *\<ContentDefinitions\>*  címke a következő példa másolásával. Cserélje le *your_storage_account* a tárfiók nevével.

  ```xml
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.idpselections">
        <LoadUri>https://{your_storage_account}.blob.core.windows.net/customize-ui.html</LoadUri>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>
  ```

## <a name="upload-your-updated-custom-policy"></a>Töltse fel a frissített egyéni házirend

1. Az a [Azure-portálon](https://portal.azure.com), [átváltani a környezetében az Azure AD B2C-bérlő](active-directory-b2c-navigate-to-b2c-context.md), majd nyissa meg a **az Azure AD B2C** panelen.
2. Kattintson a **házirend**.
3. Kattintson a **házirend feltöltése**.
4. Töltse fel `SignUpOrSignin.xml` rendelkező a  *\<ContentDefinitions\>*  címke korábban hozzáadott.

## <a name="test-the-custom-policy-by-using-run-now"></a>Az egyéni házirend tesztelése **futtatása most**

1. Az a **az Azure AD B2C** paneljén lépjen **összes házirendek**.
2. Válassza ki az egyéni házirendet, feltöltött, majd kattintson a **futtatása most** gombra.
3. Az e-mail címükkel iratkozhat fel kell lennie.

## <a name="reference"></a>Referencia

Minta sablonok a felhasználói felület testreszabásával itt található:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

A sample_templates/wingtip mappa a következő HTML-fájlokat tartalmazza:

| HTML5-sablon | Leírás |
|----------------|-------------|
| *phonefactor.HTML* | Használja ezt a fájlt egy sablont a többtényezős hitelesítés lap. |
| *ResetPassword.HTML* | Használja ezt a fájlt a sablont egy elfelejtett jelszó lap. |
| *selfasserted.HTML* | Használja ezt a fájlt egy közösségi fiók regisztrációs oldalon, a helyi fiók előfizetéshez vagy egy helyi fiók bejelentkezési oldalának sablont. |
| *Unified.HTML* | Használja ezt a fájlt egy egységes regisztráció vagy bejelentkezés lap sablont. |
| *updateprofile.HTML* | Használja ezt a fájlt egy profil frissítés lap sablont. |

Az a [módosítása a regisztráció vagy bejelentkezés egyéni házirend szakasz](#modify-your-sign-up-or-sign-in-custom-policy), konfigurálta a tartalom definíciója `api.idpselections`. A tartalom teljes készletének meghatározása az azonosítókat, és azok leírásait tartalmazza az Azure AD B2C identitás élmény keretében felismeri az alábbi táblázatban vannak:

| Tartalmi azonosító | Leírás | 
|-----------------------|-------------|
| *API.Error* | **Hibalap**. Ezen a lapon megjelenik, ha kivétel, vagy hiba történt. |
| *API.idpselections* | **Identitás-szolgáltató kiválasztása lapon**. Ezen a lapon a bejelentkezés során a felhasználó választhat az identitás-szolgáltatóktól listáját tartalmazza. Ezek a beállítások esetén a vállalati identitás-szolgáltatóktól, például a Facebookhoz és a Google + közösségi Identitásszolgáltatók, vagy helyi fiók. |
| *API.idpselections.Signup* | **Identity provider adatgyűjtésre vonatkozó felhasználói előfizetési**. Ezen a lapon a regisztráció során a felhasználó választhat az identitás-szolgáltatóktól listáját tartalmazza. Ezek a beállítások esetén a vállalati identitás-szolgáltatóktól, például a Facebookhoz és a Google + közösségi Identitásszolgáltatók, vagy helyi fiók. |
| *API.localaccountpasswordreset* | **Elfelejtett jelszó lap**. Ez a lap tartalmaz egy képernyőn a felhasználó által végrehajtandó kezdeményezheti a jelszó alaphelyzetbe állítása.  |
| *API.localaccountsignin* | **Helyi fiók bejelentkezési oldalának**. Ez a lap tartalmaz egy helyi fiók, az e-mail címet vagy egy felhasználónevet alapuló bejelentkezést a bejelentkezési űrlap. Az űrlap egy bemeneti szövegmező és a jelszó mező tartalmazhat. |
| *API.localaccountsignup* | **Helyi fiók előfizetéshez**. Ezen a lapon található regisztrációs űrlap iratkozik fel egy helyi fiók, amely egy e-mail címet vagy egy felhasználónevet alapul. Az űrlap különböző bemeneti vezérlők, például szöveg beviteli mezőt, a jelszó mező, választógomb, egyetlen legördülő listák és a többszörös kiválasztási jelölőnégyzetek is tartalmazhat. |
| *API.phonefactor* | **Többtényezős hitelesítés lap**. Ezen a lapon, a felhasználók a telefonszámok (segítségével ellenőrizheti szöveges vagy hangos) regisztráció vagy bejelentkezés során. |
| *API.selfasserted* | **Közösségi fiók bejelentkezési oldalának**. Ezen a lapon, hogy a felhasználók kell végeznie, amikor azok az egy meglévő fiókkal a Facebook-on vagy a Google + például közösségi identitásszolgáltató regisztráljon regisztrációs űrlap tartalmazza. Ezen a lapon hasonlít az előző közösségi fiók regisztrációs oldalon, a jelszó számbeviteli mezők kivételével. |
| *API.selfasserted.profileupdate* | **Profil update lapon**. Ezen a lapon, hogy a felhasználók használhatják a profil frissítése űrlap tartalmazza. Ezen a lapon hasonlít a közösségi fiók regisztrációs oldalon, a jelszó számbeviteli mezők kivételével. |
| *API.signuporsignin* | **Egyesített előfizetési vagy a bejelentkezési oldal**. Ezen a lapon a regisztráció és bejelentkezés a felhasználók, akik vállalati identitás-szolgáltatóktól, például a Facebook-on vagy a Google + és helyi fiókok közösségi Identitásszolgáltatók kezeli.  |

## <a name="next-steps"></a>Következő lépések

További információ a testre szabható felhasználói felületi elemeket: [beépített házirendek felhasználói felület testreszabása a referencia-útmutató](active-directory-b2c-reference-ui-customization.md).
