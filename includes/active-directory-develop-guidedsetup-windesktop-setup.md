---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: c5d61da61f6ec98a1cac37ce9b12b28019ce2ae1
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44058422"
---
## <a name="set-up-your-project"></a>A projekt beállítása

Ebben a szakaszban létrehozott új projektek bemutatják, hogyan integrálható a Windows asztali .NET-alkalmazás (XAML) *Bejelentkezés Microsoft-* úgy, hogy az alkalmazás kérdezheti le jogkivonatot igénylő webes API-k.

Az alkalmazást, amelyet ez az útmutató létrehoz egy gombot, egy grafikon, egy adott területre a képernyőn az eredmények megjelenítéséhez kezdeményezték, és a Kijelentkezés gombbal jeleníti meg.

> [!NOTE]
> Töltse le ezt a mintát a Visual Studio-projekt ehelyett inkább? [Töltse le a projekt](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip), és ugorjon a [konfigurációs lépés](#register-your-application) konfigurálása a kódminta azt végrehajtása előtt.
>

Az alkalmazás létrehozásához tegye a következőket:
1. A Visual Studióban válassza ki a **fájl** > **új** > **projekt**.
2. A **sablonok**válassza **Visual C#**.
3. Válassza ki **WPF-alkalmazás** vagy **Aplikace WPF**, attól függően, Ön által használt verziója a Visual Studio verziójának.

## <a name="add-msal-to-your-project"></a>Az MSAL hozzáadása a projekthez
1. A Visual Studióban válassza ki a **eszközök** > **NuGet-Csomagkezelő**> **Package Manager Console**.
2. A Package Manager Console ablakban illessze be a következő Azure PowerShell-parancsot:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre -Version 1.1.4-preview0002
    ```

    > [!NOTE] 
    > Ez a parancs telepíti a Microsoft-hitelesítési tár. Az MSAL beszerzéséhez, gyorsítótárazási és frissítése az Azure Active Directory v2-védelemmel ellátott API-k elérésére használt felhasználói jogkivonatok kezeli.
    >

    > [!NOTE]
    > Ebben a rövid útmutatóban azonban nem használja, de az MSAL.NET, legújabb verzióját, de azt frissítése folyamatban van
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
        //Below is the clientId of your app registration. 
        //You have to replace the below with the Application Id for your app registration
        private static string ClientId = "your_client_id_here";

        public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);

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

