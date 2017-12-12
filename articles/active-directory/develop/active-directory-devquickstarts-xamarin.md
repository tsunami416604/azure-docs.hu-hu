---
title: "Ismerkedés az Azure AD Xamarin |} Microsoft Docs"
description: "Xamarin-alkalmazásokat, amelyek a bejelentkezés az Azure AD integrálása, meghívják a OAuth protokollt használó Azure AD-védett API hozhatnak létre."
services: active-directory
documentationcenter: xamarin
author: jmprieur
manager: mtillman
editor: 
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 87e58e8df16f4b87b66a9ac0846be20e09073826
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="integrate-azure-ad-with-xamarin-apps"></a>Xamarin-alkalmazásokba az Azure AD integrálása
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

A Xamarin mobilalkalmazások írhat a C# futtatható iOS, Android és Windows (mobileszközök és számítógépek). Ha az alkalmazást a Xamarin most felépítése, Azure Active Directory (Azure AD) egyszerűen az Azure AD-fiókkal rendelkező felhasználók hitelesítésére. Az alkalmazás minden webes API-t az Azure AD, például az Office 365 API-k vagy az Azure API által védett biztonságosan is használ.

Xamarin-védett erőforrások elérését igénylő alkalmazások esetén az Azure AD az Active Directory Authentication Library (ADAL) biztosít. ADAL kizárólagos célja megkönnyíti a hozzáférési jogkivonatok segítségével alkalmazásokat. Annak bemutatásához, hogy milyen egyszerűen, ez a cikk bemutatja, hogyan DirectorySearcher alkalmazások készítéséhez, amely:

* Futtassa az iOS, Android, Windows asztali, Windows Phone és Windows Store.
* Egy egyetlen hordozható osztálytár (PCL) segítségével hitelesíti a felhasználókat, és a jogkivonatok lekérésére, az Azure AD Graph API-hoz.
* Keresse meg a megadott egyszerű felhasználónév a felhasználók számára egy könyvtárat.

## <a name="before-you-get-started"></a>A kezdés előtt
* Töltse le a [projekt vázát](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip), vagy töltse le a [elkészült mintát](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Minden letöltés egy olyan Visual Studio 2013 megoldás.
* Akkor is, amelyben a felhasználók létrehozásához, és regisztrálja az alkalmazást az Azure AD-bérlő kell. Ha még nem rendelkezik a bérlő [beszerzéséről egy](active-directory-howto-tenant.md).

Ha készen áll, hajtsa végre a következő négy részből áll.

## <a name="step-1-set-up-your-xamarin-development-environment"></a>1. lépés: A Xamarin fejlesztői környezet beállítása
Mivel ez az oktatóanyag iOS, Android és Windows projektek tartalmazza, kell mind a Visual Studio és Xamarin. A szükséges környezet létrehozása, a folyamat befejezéséhez a [állítsa össze, és telepítse a Visual Studio és Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) az MSDN Webhelyén. Az utasítások tartalmazzák az anyag tekinthető kapcsolatos további Xamarin Várakozás a telepítés végrehajtása közben.

A telepítés befejezése után nyissa meg a megoldást a Visual Studióban. Itt megtalálja hat projektek: öt platform-specifikus projekteket és egy PCL, DirectorySearcher.cs, amely minden platform között meg lesz osztva.

## <a name="step-2-register-the-directorysearcher-app"></a>2. lépés: A DirectorySearcher alkalmazás regisztrálása
Ahhoz, hogy az alkalmazás a jogkivonatok lekérésére, először regisztrálja az Azure AD-bérlőben, és adja meg azt az Azure AD Graph API hozzáférési engedélyt. Ezt a következőképpen teheti meg:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A felső eszköztáron kattintson a fiókját. Ekkor a a **Directory** listára, válassza ki az Active Directory-bérlőt, ahol az alkalmazást regisztrálni kívánt.
3. Kattintson a **több szolgáltatások** a bal oldali ablaktáblán, és válassza a **Azure Active Directory**.
4. Kattintson a **App regisztrációk**, majd válassza ki **Hozzáadás**.
5. Hozzon létre egy új **natív ügyfélalkalmazás**, kövesse az utasításokat.
  * **Név** az alkalmazásnak, hogy a felhasználók ismerteti.
  * **Átirányítási URI** protokollt és a karakterlánc kombinációját, amely az Azure AD token válaszok adja vissza. Adjon meg egy értéket (például http://DirectorySearcher).
6. Regisztráció befejezését követően az Azure AD rendeli hozzá az alkalmazás egy egyedi azonosítót. Másolja az értéket a **alkalmazás** lapon, mert később szüksége.
7. Az a **beállítások** lapon jelölje be **szükséges engedélyek**, majd válassza ki **Hozzáadás**.
8. Válassza ki **Microsoft Graph** , az API-t. A **delegált engedélyek**, vegye fel a **címtáradatok olvasása** engedéllyel.  
Ez a művelet lehetővé teszi az alkalmazásnak, hogy a felhasználók számára a Graph API lekérdezése.

## <a name="step-3-install-and-configure-adal"></a>3. lépés: Telepítse és konfigurálja az adal-t
Most, hogy egy alkalmazást az Azure ad-ben, telepítse az adal-t, és az identitás-kapcsolódó kód írása. Ahhoz, hogy az adal-t az Azure AD kommunikálni, adjon neki az alkalmazás regisztrációs adatait.

1. Adal-t a Csomagkezelő konzol használatával adja hozzá a DirectorySearcher projekt.

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
    `

    Vegye figyelembe, hogy két függvénytár hivatkozások kerülnek minden olyan projekthez: az adal-t és a platform-specifikus részét PCL része.
2. A DirectorySearcherLib projektben nyissa meg a DirectorySearcher.cs.
3. Az osztály kombinálását cserélje le az értékeket, amelyeket a megadott Azure-portálon. A kód hivatkozik ezeket az értékeket, minden alkalommal adal-t.

  * A *bérlői* a tartományt az Azure AD bérlője (például contoso.onmicrosoft.com).
  * A *clientId* az ügyfél-azonosító az alkalmazás, amely a portálról kimásolt.
  * A *returnUri* az átirányítási URI-t a portálon (például http://DirectorySearcher) megadott értéke.

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>4. lépés: Használja az adal TÁRAT jogkivonatok lekérni az Azure AD
Szinte teljes egészében az alkalmazás hitelesítési logikát létrejönnie `DirectorySearcher.SearchByAlias(...)`. Minden szükséges, a platform-specifikus projektben felelt meg a környezetfüggő paramétere a `DirectorySearcher` PCL.

1. Nyissa meg a DirectorySearcher.cs, és adja hozzá az új paramétere a `SearchByAlias(...)` metódust. `IPlatformParameters`az a környezetfüggő paraméter, amely magában foglalja a platform-specifikus objektumok, amelyek a hitelesítés végrehajtásához szükséges adal-t.

    ```C#
    public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
    {
    ```

2. Inicializálni `AuthenticationContext`, vagyis az elsődleges osztály az adal-t.  
Ez a művelet továbbítja az adal-t az Azure AD-val kommunikációhoz szükséges koordinátái.
3. Hívás `AcquireTokenAsync(...)`, amely elfogadja a `IPlatformParameters` objektumra, és hívja meg a hitelesítési folyamat szükséges a jogkivonat az alkalmazáshoz való visszatérésre.

    ```C#
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

    `AcquireTokenAsync(...)`először próbálja elküldeni a kért erőforrás (az ebben az esetben Graph API) jogkivonatot arra kéri a felhasználóktól a hitelesítő adataikat (keresztül gyorsítótárazását, vagy frissíteni a régi jogkivonatok) nélkül. Szükség esetén ez mutatja felhasználók az Azure AD bejelentkezési oldal a kért token beszerzése előtt.
4. A hozzáférési jogkivonat csatolása a Graph API-kérelem a a **engedélyezési** fejléc:

    ```C#
    ...
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
    ...
    ```

Ez minden, az a `DirectorySearcher` PCL és az alkalmazás csomagazonosítóját identitás kapcsolatos kódot. Most már hívni a `SearchByAlias(...)` metódus az egyes platformokon nézetekben, és ahol szükséges, adja hozzá a felhasználói felület életciklusát megfelelően kezeli a kódot.

### <a name="android"></a>Android
1. A MainActivity.cs, adjon hozzá egy `SearchByAlias(...)` kezelő kattintson a gombra:

    ```C#
    List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
    ```
2. Bírálja felül a `OnActivityResult` továbbítani a hitelesítés életciklusa metódus átirányítja a felhasználókat vissza a megfelelő módszert. Adal-t egy segédmetódust nyújt az Android:

    ```C#
    ...
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }
    ...
    ```

### <a name="windows-desktop"></a>Windows asztali
A MainWindow.xaml.cs, hívás `SearchByAlias(...)` úgy, hogy egy `WindowInteropHelper` az asztalon `PlatformParameters` objektum:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="ios"></a>iOS
A DirSearchClient_iOSViewController.cs, az iOS `PlatformParameters` objektum veszi a nézetvezérlő hivatkozik:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

### <a name="windows-universal"></a>Windows Universal
Az univerzális Windows-, nyissa meg a MainPage.xaml.cs, és megvalósításához a `Search` metódust. Ez a módszer egy segédmetódust megosztott projektben frissíteni a felhasználói felület szükség esetén használja.

```C#
...
List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

## <a name="whats-next"></a>A következő lépések
Most már rendelkezik egy működő Xamarin-alkalmazás, amely hitelesíti a felhasználókat, és biztonságosan hívására webes API-k OAuth 2.0 különböző öt különböző platformokon.

Ha már nincs beállítva, feltöltve a felhasználókkal a bérlő, most az ehhez idő.

1. A DirectorySearcher alkalmazás futtatását, és jelentkezzen be a felhasználók közül.
2. Az egyszerű Felhasználónevük alapján más felhasználók kereséséhez.

ADAL megkönnyíti a közös szervezetiidentitás-szolgáltatások beépítse az alkalmazást. Azt gondoskodik a dirty munkát meg, például a gyorsítótár kezelése OAuth protokoll támogatása, a felhasználó egy bejelentkezési felhasználói felület, a bemutató, és a jogkivonatok frissítése lejárt. Csak egyetlen API-hívással, ismernie kell `authContext.AcquireToken*(…)`.

Hivatkozás, töltse le a [elkészült mintát](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip) (a konfigurációs értékek nélkül).

Most már továbbléphet további identitás forgatókönyvek. Például [egy .NET webes API-t az Azure ad-vel biztonságos](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
