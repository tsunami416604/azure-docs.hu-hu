---
title: Az alkalmazás felhasználói felületének testreszabása Egyéni szabályzattal Azure Active Directory B2Cban | Microsoft Docs
description: Útmutató felhasználói felület egyéni házirenddel történő testreszabásához Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0c6186334820d0e419a06b9c60a8279825bf54c2
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927296"
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

2. Illessze be a másolt kódrészletet egy szövegszerkesztőbe, majd mentse a fájlt *Customize-UI. html*néven.

> [!NOTE]
> A HTML-űrlap elemei a biztonsági korlátozások miatt törlődnek, ha login.microsoftonline.com használ. Ha HTML-űrlap elemeket szeretne használni az egyéni HTML-tartalomban, használja a b2clogin.com-t. További előnyöket a [B2clogin.com használata](b2clogin.md) című témakörben talál.

## <a name="create-an-azure-blob-storage-account"></a>Azure Blob storage-fiók létrehozása

>[!NOTE]
> Ebben a cikkben az Azure Blob Storage-t használjuk a tartalom üzemeltetéséhez. Dönthet úgy, hogy webkiszolgálón üzemelteti a tartalmat, de engedélyeznie kell a [CORS a](https://enable-cors.org/server.html)webkiszolgálón.

A HTML-tartalom blob Storage-ban való üzemeltetéséhez tegye a következőket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A **központi** menüben válassza az **új** > **Storage** > **Storage-fiók**lehetőséget.
3. Adja meg a Storage-fiók egyedi **nevét** .
4. A **telepítési modell** maradhat a **Resource Managerben**.
5. Változtassa meg a **fiók típusát** a **blob Storage**-ban.
6. A **teljesítmény** továbbra is **standard**maradhat.
7. A **replikáció** az **ra-GRS**is maradhat.
8. A **hozzáférési szint** ismegmaradhat.
9. A **Storage szolgáltatás** titkosításatovábbra is letiltható.
10. Válasszon egy előfizetést a Storage-fiókjához.
11. Hozzon létre egy **erőforráscsoportot** , vagy válasszon ki egy meglévőt.
12. Válassza ki a tárolási fiók **földrajzi helyét** .
13. Kattintson a **Létrehozás** parancsra a tárfiók létrehozásához.  
    A telepítés befejezése után a Storage- **fiók** panelje automatikusan megnyílik.

## <a name="create-a-container"></a>Tároló létrehozása

Ha nyilvános tárolót szeretne létrehozni a blob Storage-ban, tegye a következőket:

1. A bal oldali menüben a **blob Service** alatt válassza a **Blobok**elemet.
2. Kattintson a **+ tároló**elemre.
3. A **név**mezőbe írja be a *root*értéket. Ez lehet a választott név, például *wingtiptoys*, de az egyszerűség kedvéért ebben a példában a roott használjuk.
4. **Nyilvános hozzáférési szint**esetén válassza a **blob**lehetőséget, majd **az OK gombot**.
5. Az új tároló megnyitásához kattintson a **gyökér** elemre.
6. Kattintson a **Feltöltés** gombra.
7. Kattintson a **fájl kiválasztása**elem melletti mappa ikonra.
8. Navigáljon a lapra, és válassza ki a korábban létrehozott **Customize-UI. html fájlt** az oldal felhasználói felületének testreszabása szakaszban.
9. Ha egy almappában szeretne feltölteni, bontsa ki a **speciális** elemet, és adja meg a mappa nevét a **feltöltés mappába**.
10. Válassza a **Feltöltés** lehetőséget.
11. Válassza ki a feltöltött **Customize-UI. html** blobot.
12. Az **URL-cím** szövegmező jobb oldalán válassza a **Másolás vágólapra** ikont az URL-cím vágólapra másolásához.
13. A böngészőben nyissa meg a vágólapra másolt URL-címet, és ellenőrizze, hogy a feltöltött blob elérhető-e. Ha nem érhető el, például ha `ResourceNotFound` hibát tapasztal, győződjön meg arról, hogy a tároló hozzáférési típusa **blob**.

## <a name="configure-cors"></a>A CORS konfigurálása

A blob Storage a következő módon konfigurálható a több eredetű erőforrás-megosztáshoz:

1. A menüben válassza a **CORS**lehetőséget.
2. Az **engedélyezett eredetek**mezőben `https://your-tenant-name.b2clogin.com`adja meg a következőt:. Cserélje le `your-tenant-name` az Azure AD B2C-bérlő nevével. Például: `https://fabrikam.b2clogin.com`. A bérlő nevének megadásakor az összes kisbetűs betűt kell használnia.
3. Az **engedélyezett módszerek**esetében válassza a `GET` mindkettő `OPTIONS`és a lehetőséget.
4. Az **engedélyezett fejlécek**mezőbe írjon be egy csillagot (*).
5. Aközzétett fejlécek esetében írjon be egy csillagot (*).
6. A **Max Age**értéknél adja meg a 200 értéket.
7. Kattintson a **Save** (Mentés) gombra.

## <a name="test-cors"></a>CORS tesztelése

Ellenőrizze, hogy készen áll-e a következők elvégzésével:

1. Nyissa meg a [www.test-CORS.org](https://www.test-cors.org/) webhelyét, majd illessze be az URL-címet a **távoli URL-cím** mezőbe.
2. Kattintson a **kérelem küldése**gombra.  
    Ha hibaüzenetet kap, ellenőrizze, hogy helyesek-e a [CORS beállításai](#configure-cors) . Előfordulhat, hogy törölnie kell a böngésző gyorsítótárát, vagy meg kell nyitnia egy privát böngészési munkamenetet a CTRL + SHIFT + P billentyűkombináció lenyomásával.

## <a name="modify-the-extensions-file"></a>A kiterjesztések fájljának módosítása

A felhasználói felület testreszabásának konfigurálásához másolja a **ContentDefinition** és annak alárendelt elemeit az alapfájlból a kiterjesztések fájlba.

1. Nyissa meg a szabályzat alapfájlját. Például: *TrustFrameworkBase. XML*.
2. Keresse meg és másolja a **ContentDefinitions** elem teljes tartalmát.
3. Nyissa meg a kiterjesztési fájlt. Például: *TrustFrameworkExtensions. XML*. Keresse meg a **BuildingBlocks** elemet. Ha az elem nem létezik, adja hozzá.
4. Illessze be a **ContentDefinitions** elem teljes tartalmát, amelyet a **BuildingBlocks** elem gyermekeiként másolt. 
5. Keresse meg a másolt XML `Id="api.signuporsignin"` -fájlban található ContentDefinition elemet.
6. Módosítsa a **tartalomdefinícióban** értékét a Storage-ba feltöltött HTML-fájl URL-címére. Például: `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.
    
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

7. Mentse a bővítmények fájlt.

## <a name="upload-your-updated-custom-policy"></a>Töltse fel a frissített egyéni szabályzatot

1. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza az **identitási élmény keretrendszert**.
2. Kattintson **a minden házirend**elemre.
3. Kattintson a **szabályzat feltöltése**elemre.
4. Töltse fel a korábban módosított kiterjesztéseket tartalmazó fájlt.

## <a name="test-the-custom-policy-by-using-run-now"></a>Az egyéni házirend tesztelése a **Futtatás most** használatával

1. A **Azure ad B2C** panelen lépjen az **összes házirend**elemre.
2. Válassza ki a feltöltött egyéni szabályzatot, majd kattintson a **Futtatás most** gombra.
3. Regisztrálnia kell egy e-mail-cím használatával.

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

Itt találja a minta használatának lépéseit. 
1. A tárház klónozása a helyi gépen. Válasszon egy sablon mappát a sample_templates területen. Használhatja a `contoso`vagy a-t. `wingtip`
2. Töltse fel a `css`, `fonts`és `images` a mappák alá tartozó összes fájlt a blob Storage-ba az előző szakaszokban leírtak szerint. 
3. Ezután nyissa meg \*a (z) `wingtip` vagy `contoso` a gyökérkönyvtárában található. html fájlt, és cserélje le a "http://localhost" összes példányát a 2. lépésben feltöltött CSS, images és Fonts fájlok URL-címeire.
4. Mentse a \*. html fájlokat, és töltse fel őket a blob Storage-ba.
5. Most módosítsa a kiterjesztések fájlt, ahogy azt korábban már említettük a [kiterjesztések fájl módosításakor](#modify-the-extensions-file).
6. Ha a hiányzó betűkészleteket, képeket vagy CSS-ket látja, tekintse át a hivatkozásokat a kiterjesztések és a \*. HTML fájlok között.

### <a name="content-defintion-ids"></a>Tartalom Defintion-azonosítói

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
