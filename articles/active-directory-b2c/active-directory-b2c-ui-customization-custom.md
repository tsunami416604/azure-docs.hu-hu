---
title: Az alkalmazás felhasználói felületének testreszabása Egyéni szabályzattal Azure Active Directory B2Cban | Microsoft Docs
description: Útmutató felhasználói felület egyéni házirenddel történő testreszabásához Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 88a8258a91237c7b3eadccc32a30c3fe8149eca5
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064632"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Testre szabhatja az alkalmazás felhasználói felületét egy egyéni házirend használatával Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A cikk elvégzése után egy regisztrációs és bejelentkezési egyéni szabályzattal fog rendelkezni a márka és a megjelenés alapján. A Azure Active Directory B2C (Azure AD B2C) használatával szinte teljes mértékben vezérelheti a felhasználók számára megjelenített HTML-és CSS-tartalmakat. Ha egyéni házirendet használ, a felhasználói felület testreszabását az XML-ben kell konfigurálnia ahelyett, hogy a Azure Portal vezérlőit kellene használnia.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre az [Ismerkedés az egyéni szabályzatokkal](active-directory-b2c-get-started-custom.md)című témakör lépéseit. A bejelentkezéshez és a helyi fiókokkal való bejelentkezéshez egyéni szabályzatot kell használnia.

## <a name="page-ui-customization"></a>Oldal felhasználói felületének testreszabása

Az oldal felhasználói felületének testreszabási funkciója segítségével testre szabhatja az egyéni szabályzatok megjelenését és működését. Lehetősége van fenntartani az alkalmazása és az Azure AD B2C közötti márkabeli és vizuális egységességet is.

Ez a következőképpen működik: A Azure AD B2C kódot futtat az ügyfél böngészőjében, és egy modern megközelítést használ az eltérő [eredetű erőforrás-megosztás (CORS)](https://www.w3.org/TR/cors/)néven. Először is megadhat egy URL-címet az egyéni szabályzatban testreszabott HTML-tartalommal. Az Azure AD B2C egyesíti a felhasználói felület elemeit az URL-ről betöltött HTML-tartalommal, majd megjeleníti az oldalt az ügyfélnek.

## <a name="create-your-html5-content"></a>HTML5-tartalom létrehozása

Hozzon létre HTML-tartalmat a termék márkájának nevében.

1. Másolja a következő HTML-kódrészletet. Jól formázott HTML5 egy olyan üres elemmel, amelynek neve  *\<div id = "API"\>\<\> /div* , amely a *\<törzs\>* címkén belül található. Ez az elem azt jelzi, hogy hol kell beszúrni Azure AD B2C tartalmat.

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

1. Illessze be a másolt kódrészletet egy szövegszerkesztőbe, majd mentse a fájlt *Customize-UI. html*néven.

> [!NOTE]
> A HTML-űrlap elemei a biztonsági korlátozások miatt törlődnek, ha login.microsoftonline.com használ. Ha HTML-űrlap elemeket szeretne használni az egyéni HTML-tartalomban, használja a b2clogin.com-t. További előnyöket a [B2clogin.com használata](b2clogin.md) című témakörben talál.

## <a name="create-an-azure-blob-storage-account"></a>Azure Blob storage-fiók létrehozása

>[!NOTE]
> Ebben a cikkben az Azure Blob Storage-t használjuk a tartalom üzemeltetéséhez. Dönthet úgy, hogy webkiszolgálón üzemelteti a tartalmat, de engedélyeznie kell a [CORS a webkiszolgálón](https://enable-cors.org/server.html).

Ha ezt a HTML-tartalmat a blob Storage-ban szeretné tárolni, hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A **központi** menüben válassza az **új** > **Storage** > **Storage-fiók**lehetőséget.
1. Válasszon egy előfizetést a Storage-fiókjához.
1. Hozzon létre egy **erőforráscsoportot** , vagy válasszon ki egy meglévőt.
1. Adja meg a Storage-fiók egyedi **nevét** .
1. Válassza ki a tárolási fiók **földrajzi helyét** .
1. A **telepítési modell** maradhat a **Resource Managerben**.
1. A **teljesítmény** továbbra is **standard**maradhat.
1. Változtassa meg a **fiók típusát** a **blob Storage**-ban.
1. A **replikáció** az **ra-GRS**is maradhat.
1. A **hozzáférési szint** ismegmaradhat.
1. A Storage-fiók létrehozásához kattintson a **felülvizsgálat + létrehozás** lehetőségre.
    A telepítés befejezése után a Storage- **fiók** lap automatikusan megnyílik.

## <a name="create-a-container"></a>Tároló létrehozása

Ha nyilvános tárolót szeretne létrehozni a blob Storage-ban, hajtsa végre a következő lépéseket:

1. A bal oldali menüben a **blob Service** alatt válassza a **Blobok**elemet.
1. Kattintson a **+ tároló**elemre.
1. A **név**mezőbe írja be a *root*értéket. Ez lehet a választott név, például *wingtiptoys*, de az egyszerűség kedvéért ebben a példában a *roott* használjuk.
1. **Nyilvános hozzáférési szint**esetén válassza a **blob**lehetőséget, majd **az OK gombot**.
1. Az új tároló megnyitásához kattintson a **gyökér** elemre.
1. Kattintson a **Feltöltés** gombra.
1. Kattintson a **fájl kiválasztása**elem melletti mappa ikonra.
1. Navigáljon a lapra, és válassza ki a korábban létrehozott **Customize-UI. html fájlt** az oldal felhasználói felületének testreszabása szakaszban.
1. Ha egy almappában szeretne feltölteni, bontsa ki a **speciális** elemet, és adja meg a mappa nevét a **feltöltés mappába**.
1. Válassza a **Feltöltés** lehetőséget.
1. Válassza ki a feltöltött **Customize-UI. html** blobot.
1. Az **URL-cím** szövegmező jobb oldalán válassza a **Másolás vágólapra** ikont az URL-cím vágólapra másolásához.
1. A böngészőben nyissa meg a vágólapra másolt URL-címet, és ellenőrizze, hogy a feltöltött blob elérhető-e. Ha nem érhető el, például ha `ResourceNotFound` hibát tapasztal, győződjön meg arról, hogy a tároló hozzáférési típusa **blob**.

## <a name="configure-cors"></a>A CORS konfigurálása

Az alábbi lépések végrehajtásával konfigurálja a blob Storage-t az idegen eredetű erőforrás-megosztáshoz:

1. A menüben válassza a **CORS**lehetőséget.
1. Az **engedélyezett eredetek**mezőben `https://your-tenant-name.b2clogin.com`adja meg a következőt:. Cserélje le `your-tenant-name` az Azure AD B2C-bérlő nevével. Például: `https://fabrikam.b2clogin.com`. A bérlő nevének megadásakor az összes kisbetűs betűt kell használnia.
1. Az **engedélyezett módszerek**esetében válassza a `GET` mindkettő `OPTIONS`és a lehetőséget.
1. Az **engedélyezett fejlécek**mezőbe írjon be egy csillagot (*).
1. Aközzétett fejlécek esetében írjon be egy csillagot (*).
1. A **Max Age**értéknél adja meg a 200 értéket.
1. Kattintson a **Save** (Mentés) gombra.

## <a name="test-cors"></a>CORS tesztelése

Ellenőrizze, hogy készen áll-e a következő lépések végrehajtásával:

1. Nyissa meg a [www.test-CORS.org](https://www.test-cors.org/) webhelyét, majd illessze be az URL-címet a **távoli URL-cím** mezőbe.
1. Kattintson a **kérelem küldése**gombra.
    Ha hibaüzenetet kap, ellenőrizze, hogy helyesek-e a [CORS beállításai](#configure-cors) . Előfordulhat, hogy törölnie kell a böngésző gyorsítótárát, vagy meg kell nyitnia egy privát böngészési munkamenetet a CTRL + SHIFT + P billentyűkombináció lenyomásával.

## <a name="modify-the-extensions-file"></a>A kiterjesztések fájljának módosítása

A felhasználói felület testreszabásának konfigurálásához másolja a **ContentDefinition** és annak alárendelt elemeit az alapfájlból a kiterjesztések fájlba.

1. Nyissa meg a szabályzat alapfájlját. Például *`SocialAndLocalAccounts/`*****.`TrustFrameworkBase.xml` Ez az egyéni házirend alapszintű csomagban található egyik házirend-fájl, amelyet az előfeltételben kell megszereznie az [Egyéni szabályzatok használatának első lépéseiben](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom).
1. Keresse meg és másolja a **ContentDefinitions** elem teljes tartalmát.
1. Nyissa meg a kiterjesztési fájlt. Például: *TrustFrameworkExtensions. XML*. Keresse meg a **BuildingBlocks** elemet. Ha az elem nem létezik, adja hozzá.
1. Illessze be a **ContentDefinitions** elem teljes tartalmát, amelyet a **BuildingBlocks** elem gyermekeiként másolt.
1. Keresse meg a másolt XML `Id="api.signuporsignin"` -fájlban található ContentDefinition elemet.
1. Módosítsa a **tartalomdefinícióban** értékét a Storage-ba feltöltött HTML-fájl URL-címére. Például: `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Az egyéni szabályzatnak a következőhöz hasonlóan kell kinéznie:

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

1. Mentse a bővítmények fájlt.

## <a name="upload-your-updated-custom-policy"></a>Töltse fel a frissített egyéni szabályzatot

1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza az **identitási élmény keretrendszert**.
1. Kattintson **a minden házirend**elemre.
1. Kattintson a **szabályzat feltöltése**elemre.
1. Töltse fel a korábban módosított kiterjesztéseket tartalmazó fájlt.

## <a name="test-the-custom-policy-by-using-run-now"></a>Az egyéni házirend tesztelése a **Futtatás most** használatával

1. A **Azure ad B2C** lapon lépjen a **minden házirend**elemre.
1. Válassza ki a feltöltött egyéni szabályzatot, majd kattintson a **Futtatás most** gombra.
1. Regisztrálnia kell egy e-mail-cím használatával.

## <a name="reference"></a>Hivatkozás

### <a name="sample-templates"></a>Példasablonok
A felhasználói felület testreszabásához itt talál példákat:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

A sample_templates/Wingtip mappa a következő HTML-fájlokat tartalmazza:

| HTML5-sablon | Leírás |
|----------------|-------------|
| *phonefactor.html* | Ezt a fájlt sablonként használhatja a multi-Factor Authentication oldalon. |
| *resetpassword.html* | Ezt a fájlt sablonként használhatja egy elfelejtett jelszó oldalhoz. |
| *selfasserted.html* | Ezt a fájlt sablonként használhatja a közösségi fiók regisztrálásához, a helyi fiók regisztrációs oldalához vagy egy helyi fiók bejelentkezési oldalához. |
| *unified.html* | Ezt a fájlt sablonként használhatja az egyesített regisztrációs vagy bejelentkezési oldalhoz. |
| *updateprofile.html* | Ezt a fájlt sablonként használhatja a profil frissítési lapjához. |

A példa a következőképpen használható:

1. A tárház klónozása a helyi gépen. Válasszon egy sablon mappát a sample_templates területen. Használhatja a `contoso`vagy a-t. `wingtip`
1. Töltse fel a `css`, `fonts`és `images` a mappák alá tartozó összes fájlt a blob Storage-ba az előző szakaszokban leírtak szerint.
1. Ezután nyissa meg \*a (z) `wingtip` vagy `contoso` a gyökérkönyvtárában található. html fájlt, és cserélje le a "http://localhost" összes példányát a 2. lépésben feltöltött CSS, images és Fonts fájlok URL-címeire.
1. Mentse a \*. html fájlokat, és töltse fel őket a blob Storage-ba.
1. Most módosítsa a kiterjesztések fájlt, ahogy azt korábban már említettük a [kiterjesztések fájl módosításakor](#modify-the-extensions-file).
1. Ha a hiányzó betűkészleteket, képeket vagy CSS-ket látja, tekintse át a hivatkozásokat a \*kiterjesztések és a. HTML fájlok között.

### <a name="content-definition-ids"></a>Tartalom-definíciós azonosítók

A regisztrációs vagy bejelentkezési egyéni házirend módosítása szakaszban konfigurálta a tartalom definícióját `api.idpselections`. A Azure AD B2C Identity Experience Framework által felismert és a leírások teljes készletét a következő táblázat tartalmazza:

| Tartalom definíciójának azonosítója | Leírás |
|-----------------------|-------------|
| *api.error* | **Hiba lap**. Ez az oldal akkor jelenik meg, ha kivételt vagy hibát észlel. |
| *api.idpselections* | **Identitás-szolgáltató kiválasztása lap**. Ez az oldal azon identitás-szolgáltatók listáját tartalmazza, amelyeket a felhasználó a bejelentkezés során választhat. Ezek a vállalati identitás-szolgáltatók, a közösségi identitás-szolgáltatók, például a Facebook és a Google +, vagy a helyi fiókok. |
| *api.idpselections.signup* | **Az identitás-szolgáltató kiválasztása a regisztrációhoz**. Ez az oldal azon identitás-szolgáltatók listáját tartalmazza, amelyeket a felhasználó a regisztráció során választhat. Ezek a vállalati identitás-szolgáltatók, a közösségi identitás-szolgáltatók, például a Facebook és a Google +, vagy a helyi fiókok. |
| *api.localaccountpasswordreset* | **Elfelejtett jelszó lap**. Ez az oldal olyan űrlapot tartalmaz, amelyet a felhasználónak be kell fejeznie a jelszó-visszaállítás elindításához.  |
| *api.localaccountsignin* | **Helyi fiók bejelentkezési lapja**. Ez az oldal bejelentkezési űrlapot tartalmaz a bejelentkezéshez olyan helyi fiókkal, amely e-mail-cím vagy Felhasználónév alapján van bejelentkezve. Az űrlap tartalmazhatja a szövegbeviteli és a jelszó-beviteli mezőt is. |
| *api.localaccountsignup* | **Helyi fiók regisztrációjának lapja**. Ez az oldal egy regisztrációs űrlapot tartalmaz ahhoz, hogy egy helyi fiókra regisztráljon, amely egy e-mail-cím vagy egy Felhasználónév alapján van regisztrálva. Az űrlap különböző beviteli vezérlőket tartalmazhat, mint például a szövegbeviteli mezők, a jelszó-beviteli mezők, a választógombok, az egyszeres kijelölés legördülő listák és a többszörös kijelölés jelölőnégyzetek. |
| *api.phonefactor* | **Multi-Factor Authentication oldal**. Ezen a lapon a felhasználók megtekinthetik a telefonszámokat (szöveg vagy hang használatával) a regisztráció vagy a bejelentkezés során. |
| *api.selfasserted* | **Közösségi fiók regisztrálása oldal**. Ez az oldal egy olyan regisztrációs űrlapot tartalmaz, amelyet a felhasználóknak be kell fejezniük, amikor egy meglévő fiókot használnak egy közösségi identitás-szolgáltató, például a Facebook vagy a Google + használatával. Ez az oldal hasonló az előző közösségi fiók regisztrálási lapjához, a jelszó-beviteli mezők kivételével. |
| *api.selfasserted.profileupdate* | **Profil frissítése lap**. Ez a lap egy űrlapot tartalmaz, amelyet a felhasználók a profil frissítéséhez használhatnak. Ez az oldal hasonló a közösségi fiók regisztrációs oldalához, a jelszó-beviteli mezők kivételével. |
| *api.signuporsignin* | **Egyesített regisztrációs vagy bejelentkezési oldal**. Ez a lap a felhasználók regisztrációját és bejelentkezését is kezeli, akik vállalati identitás-szolgáltatókat, közösségi identitás-szolgáltatókat, például Facebook vagy Google + vagy helyi fiókokat használhatnak.  |

## <a name="next-steps"></a>További lépések

További információ a testreszabható felhasználói felületi elemekről: [útmutató a beépített szabályzatok felhasználói felületének testreszabásához](active-directory-b2c-reference-ui-customization.md).
