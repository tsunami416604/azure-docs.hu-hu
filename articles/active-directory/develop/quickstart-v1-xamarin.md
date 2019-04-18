---
title: Ismerkedés az Azure AD Xamarin |} A Microsoft Docs
description: Integrálhatja az Azure AD-be, és az Azure AD-védelemmel ellátott API-jainak hívására OAuth használatával Xamarin-alkalmazásokat hozhat létre.
services: active-directory
documentationcenter: xamarin
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0a20c2e6524b0c466f5c45578e0ba8eaad351ea
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58881885"
---
# <a name="quickstart-build-a-xamarin-app-that-integrates-microsoft-sign-in"></a>Gyors útmutató: Integrálható a Microsoft bejelentkezési Xamarin-alkalmazás készítése

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

A Xamarin a mobile apps írhat C#-ban iOS, Android és Windows (mobileszközök és számítógépek) futtatható. Ha egy alkalmazást a Xamarin segítségével készít, az Azure Active Directory (Azure AD) egyszerűvé teszi felhasználók hitelesítése az Azure AD-fiókjukat. Az alkalmazás is biztonságosan felhasználhat bármely Azure AD-ben például az Office 365 API-k vagy az Azure API által védett webes API-t.

Xamarin-alkalmazásokba, védett erőforrások elérését igénylő az Azure AD biztosítja az Active Directory Authentication Library (ADAL). Kizárólagos célja az adal-t, hogy megkönnyítik az alkalmazások hozzáférési. Mutatja be, milyen egyszerűen, hogy ez a cikk bemutatja, hogyan DirectorySearcher alkalmazásokat hozhat létre, amely:

* Futtassa az iOS, Android, Windows asztali, Windows Phone és Windows Store.
* Egyetlen hordozható osztálytárat (PLC) segítségével hitelesítheti a felhasználókat és jogkivonatok lekérésére, az Azure AD Graph API-hoz.
* Keressen rá egy könyvtárat egy adott egyszerű Felhasználónévvel rendelkező felhasználók számára.

## <a name="prerequisites"></a>Előfeltételek

* Töltse le a [projekt vázát](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip), vagy töltse le a [elkészült mintát](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Minden letöltés a Visual Studio 2013 megoldás.
* Azure AD-bérlő, amelyben a felhasználók létrehozásához, és regisztrálja az alkalmazást is szükséges. Ha még nem rendelkezik bérlővel, [itt megtudhatja, hogyan tehet szert egyre](quickstart-create-new-tenant.md).

Amikor készen áll, hajtsa végre a következő négy szakasz.

## <a name="step-1-set-up-your-xamarin-development-environment"></a>1. lépés: A Xamarin fejlesztői környezet beállítása

Mivel ebben az oktatóanyagban a projektek iOS, Android és Windows tartalmazza, a Visual Studio és a Xamarin van szükség. A szükséges környezet létrehozásához hajtsa végre a folyamat [beállítása és telepítése a Visual Studio és Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) az MSDN Webhelyén. Az utasítások tartalmazzák az anyagokat, további információ a Xamarinról amíg várakozik a telepítés befejezésének tekinthető meg.

A telepítés befejezése után nyissa meg a megoldást a Visual Studióban. Itt megtalálja a hat projektek: öt platformspecifikus projektek és a egy plc, DirectorySearcher.cs, amely minden platformon között meg lesz osztva.

## <a name="step-2-register-the-directorysearcher-app"></a>2. lépés: A DirectorySearcher alkalmazás regisztrálása

Ahhoz, hogy az alkalmazásnak, hogy a jogkivonatok lekérésére, akkor először regisztrálja az Azure AD-bérlőhöz, és engedélyezi azt az Azure AD Graph API eléréséhez. Ezt a következőképpen teheti meg:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A felső eszköztáron kattintson a fiók. Ezt követően a **Directory** listájához, válassza ki az Active Directory-bérlő kívánja az alkalmazás regisztrálásához.
3. Kattintson a **Minden szolgáltatás** lehetőségre a bal oldali panelen, majd válassza az **Azure Active Directory** elemet.
4. Kattintson az **Alkalmazásregisztrációk** elemre, majd válassza a **Hozzáadás** parancsot.
5. Hozzon létre egy új **natív ügyfélalkalmazás**, kövesse az utasításokat.
   * **Név** ismerteti az alkalmazást a felhasználók számára.
   * Az **Átirányítási URI** egy sémából és sztringből álló kombináció, amelyet az Azure AD jogkivonatválaszok visszaadására használ. Adjon meg egy értéket (például `http://DirectorySearcher`).
6. Regisztráció befejezése után az Azure AD rendeli az alkalmazást egy alkalmazás egyedi azonosítója. Másolja az értéket a **alkalmazás** lapon, mert később szüksége.
7. Az a **beállítások** lapon jelölje be **szükséges engedélyek**, majd válassza ki **Hozzáadás**.
8. Válassza ki **Microsoft Graph** az API-ként. A **delegált engedélyek**, adja hozzá a **címtáradatok olvasása** engedéllyel. 
   Ez a művelet lehetővé teszi az alkalmazásnak, hogy a felhasználók számára a Graph API lekérdezéséhez.

## <a name="step-3-install-and-configure-adal"></a>3. lépés: Telepítse és konfigurálja az adal-t

Most, hogy egy alkalmazást az Azure ad-ben, telepítheti az adal-t és az identitással kapcsolatos kód írása. Ahhoz, hogy az adal-t kommunikálni az Azure ad-ben, adjon meg néhány információt az alkalmazás regisztrációját.

1. A Package Manager konzol használatával vegye fel a DirectorySearcher projektbe adal-t.

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
    ```

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
    ```

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
    ```

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
    ```

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
    ```

    Vegye figyelembe, hogy mindegyik projekthez két erőforrástár-hivatkozás kerülnek: az adal-t és a egy platformspecifikus részében PLC része.
2. A DirectorySearcherLib projektben nyissa meg a DirectorySearcher.cs.
3. Az osztály tag értékeket cserélje le az értékeket, amelyeket az Azure Portalon megadott. A kód hivatkozik, amikor az adal-t használja ezeket az értékeket.

   * A *bérlői* a tartomány az Azure AD-bérlő (például contoso.onmicrosoft.com).
   * A *clientId* az alkalmazás, amely a portálról kimásolt ügyfél-azonosítója.
   * A *returnUri* az átirányítási URI-t, amely a portálon megadott (például `http://DirectorySearcher`).

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>4. lépés: Adal-t használó tokenekhez Azure AD-ből való

Szinte teljes egészében az alkalmazás hitelesítési logikát létrejönnie `DirectorySearcher.SearchByAlias(...)`. Minden szükséges a platformspecifikus projektek átadni egy környezetfüggő paramétert a `DirectorySearcher` Plc.

1. Nyissa meg a DirectorySearcher.cs, és adja hozzá az új paraméter a `SearchByAlias(...)` metódust. `IPlatformParameters` az a környezetfüggő paraméter, amely magában foglalja az egyes platformokra vonatkozó objektumok adal-t a hitelesítés elvégzéséhez szüksége van.

    ```csharp
    public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
    {
    ```

2. Inicializálása `AuthenticationContext`, vagyis az elsődleges osztály az adal-t. Ez a művelet átadja az adal-t, és az Azure AD közötti kommunikációhoz szükséges a koordinátákat.
3. Hívás `AcquireTokenAsync(...)`, amely elfogadja a `IPlatformParameters` objektumra, és hívja meg a szükséges jogkivonat térjen vissza az alkalmazás hitelesítési folyamatát.

    ```csharp
    ...
        AuthenticationResult authResult = null;
        try
        {
            AuthenticationContext authContext = new AuthenticationContext(authority);
            authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
        }
        catch (Exception ee)
        {
            results.Add(new User { error = ee.Message });
            return results;
        }
    ...
    ```

    `AcquireTokenAsync(...)` első kísérlet nélkül szeretne visszatérni a kért erőforrás (az ebben az esetben a Graph API) jogkivonatot arra kéri a felhasználót, adja meg a hitelesítő adataikat (gyorsítótárazás, illetve a régi jogkivonatok). Szükség szerint jeleníti meg felhasználókat az Azure AD bejelentkezési oldal a kért token beszerzése előtt.
4. A hozzáférési jogkivonatot a Graph API-kérelem a csatolása a **engedélyezési** fejléc:

    ```csharp
    ...
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
    ...
    ```

Ez minden, az a `DirectorySearcher` PLC és az alkalmazás a identitással kapcsolatos kódot. Most már hívja a `SearchByAlias(...)` metódus az egyes platformokon nézeteket, és szükség szerint adja hozzá a kód megfelelően kezeli a felhasználói felület életciklus.

### <a name="android"></a>Android
1. A MainActivity.cs, adja hozzá a hívás `SearchByAlias(...)` kezelő kattintson a gombra:

    ```csharp
    List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
    ```
2. Bírálja felül a `OnActivityResult` továbbítják a hitelesítés életciklusa metódus átirányítja a felhasználót vissza a megfelelő módszert. Adal-t egy olyan segédmetódus ehhez az Android biztosítja:

    ```csharp
    ...
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }
    ...
    ```

### <a name="windows-desktop"></a>Windows asztal

MainWindow.xaml.cs, a hívás `SearchByAlias(...)` átadásával egy `WindowInteropHelper` a desktopban `PlatformParameters` objektum:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="ios"></a>iOS
A DirSearchClient_iOSViewController.cs, az IOS-es `PlatformParameters` objektum vesz igénybe egy hivatkozást a View-Controller:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

### <a name="windows-universal"></a>Windows Universal
A Windows Universal, nyissa meg a MainPage.xaml.cs, és megvalósításához a `Search` metódust. Ez a módszer egy megosztott projekt egy olyan segédmetódus frissíteni a felhasználói felület szükség esetén használja.

```csharp
...
List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

Most már rendelkezik egy működő Xamarin-alkalmazás, amely képes hitelesíteni a felhasználókat, és biztonságos hívása a webes API-k öt különböző platformok közötti OAuth 2.0 használatával.

## <a name="step-5-populate-your-tenant"></a>5. lépés: A bérlő feltöltése 

Ha Ön még nem már fel van töltve a bérlő felhasználóival, most már az az idő, ehhez.

1. A DirectorySearcher alkalmazás futtatásához, és jelentkezzen be a felhasználók közül.
2. Keressen más felhasználókat az egyszerű felhasználónevük alapján.

## <a name="next-steps"></a>További lépések

Adal-t egyszerűen közös identitás funkciókat építhet be az alkalmazást. Ez gondoskodik dirty munka, például a gyorsítótár kezeléséhez, OAuth protokoll támogatása, a bejelentkezési felhasználói felület, a felhasználó jelentő és frissítése lejárt jogkivonatok. Csak egyetlen API hívással, ismernie kell `authContext.AcquireToken*(…)`.

* Töltse le a [elkészült mintát](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip) (nélkül a konfigurációs értékeket).
* Ismerje meg, hogyan [.NET webes API Azure AD-vel biztonságos](quickstart-v1-dotnet-webapi.md).
