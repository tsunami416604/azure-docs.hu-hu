---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: ce95e8d0249a886e031e3ae0fe9dd8e20804f391
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59498374"
---
## <a name="set-up-your-project"></a>A projekt beállítása

Ebben a szakaszban létrehozott új projektek bemutatják, hogyan integrálható a Windows asztali .NET-alkalmazás (XAML) *Bejelentkezés Microsoft-* úgy, hogy az alkalmazás kérdezheti le jogkivonatot igénylő webes API-k.

Az alkalmazást, amelyet ez az útmutató létrehoz egy gombot, egy grafikon, egy adott területre a képernyőn az eredmények megjelenítéséhez kezdeményezték, és a Kijelentkezés gombbal jeleníti meg.

> [!NOTE]
> Töltse le ezt a mintát a Visual Studio-projekt ehelyett inkább? [Töltse le a projekt](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip), és ugorjon a [konfigurációs lépés](#register-your-application) konfigurálása a kódminta azt végrehajtása előtt.
>

Az alkalmazás létrehozásához tegye a következőket:

1. A Visual Studióban válassza ki a **fájl** > **új** > **projekt**.
2. A **sablonok**válassza **Visual C#**.
3. Válassza ki **WPF App (.NET Framework)**, attól függően, Ön által használt verziója a Visual Studio verziójának.

## <a name="add-msal-to-your-project"></a>Az MSAL hozzáadása a projekthez

1. A Visual Studióban válassza a **Tools** (Eszközök)  > **NuGet Package Manager** (NuGet-csomagkezelő)> **Package Manager Console** (Csomagkezelő konzol) elemet.
2. A Package Manager Console ablakban illessze be a következő Azure PowerShell-parancsot:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > Ez a parancs telepíti a Microsoft-hitelesítési tár. Az MSAL kezeli, beszerzése, gyorsítótárazási és frissítése az Azure Active Directory v2.0-védelemmel ellátott API-k elérésére használt felhasználói jogkivonatok
    >

## <a name="add-the-code-to-initialize-msal"></a>Adja hozzá a kódot az MSAL inicializálása

Ebben a lépésben hozzon létre egy osztályt, amely kezeli a interakció MSAL, például a jogkivonatok kezelését.

1. Nyissa meg a *App.xaml.cs* fájlt, és adja hozzá a hivatkozást az MSAL az osztályhoz:

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. Az alkalmazás osztály frissítése a következőhöz:

    ```csharp
    public partial class App : Application
    {
        static App()
        {
            _clientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
        }

        // Below are the clientId (Application Id) of your app registration and the tenant information. 
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - Te content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use `organizations`
        //   - for any Work or School accounts, or Microsoft personal account, use `common`
        //   - for Microsoft Personal account, use consumers
        private static string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        private static string Tenant = "common";

        private static IPublicClientApplication _clientApp ;

        public static IPublicClientApplication PublicClientApp { get { return _clientApp; } }
    }
    ```

## <a name="create-the-application-ui"></a>Az alkalmazás felhasználói felület létrehozása

Ez a szakasz bemutatja, hogyan egy alkalmazás például a Microsoft Graph egy védett háttérrendszeri kiszolgáló lekérdezheti-e. 

A *MainWindow.xaml* fájl automatikusan létrejönnek a projektsablon részeként. Nyissa meg ezt a fájlt, és ezután cserélje le az alkalmazás  *\<rács >* csomópont a következő kóddal:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```
