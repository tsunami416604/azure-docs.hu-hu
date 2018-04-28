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
ms.openlocfilehash: d407b015699925a6a3ce6ef9108ace1e9900303f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
## <a name="set-up-your-project"></a>A projekt beállítása

Ebben a szakaszban a létrehozhat egy új projektet bemutatják, hogyan integrálhatja a Windows asztali .NET-alkalmazás (XAML) *jelentkezzen be Microsoft* , hogy az alkalmazás webes API-k jogkivonat igénylő lekérdezheti.

Az alkalmazást, amely létrehozta a jelen útmutató egy grafikon, az eredményeket a képernyőn megjeleníthető területe meghívásához használt, és a kijelentkezési gomb jeleníti meg.

> [!NOTE]
> Ez a minta Visual Studio-projekt letöltése helyette inkább? [Töltse le a projekt](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip), és ugorjon a [konfigurációs lépés](#register-your-application) konfigurálása a példakód azt végrehajtása előtt.
>

Az alkalmazás létrehozásához tegye a következőket:
1. A Visual Studio válassza **fájl** > **új** > **projekt**.
2. A **sablonok**, jelölje be **Visual C#**.
3. Válassza ki **WPF-alkalmazás** vagy **WPF-alkalmazás**, attól függően, a Visual Studio verziója használata verzióját.

## <a name="add-msal-to-your-project"></a>MSAL hozzáadása a projekthez
1. A Visual Studio válassza **eszközök** > **NuGet-Csomagkezelő**> **Csomagkezelő konzol**.
2. A Package Manager Console ablakban illessze be a következő Azure PowerShell-parancsot:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > Ez a parancs telepíti a Microsoft-hitelesítési tár. MSAL kezeli az beszerzése, gyorsítótárazás, és az Azure Active Directory v2 által védett API-k elérésére használt felhasználói jogkivonatokhoz frissítésekor.
    >

## <a name="add-the-code-to-initialize-msal"></a>Adja hozzá a kódot MSAL inicializálása
Ebben a lépésben hozzon létre egy osztályt, például jogkivonatokat kezelésének MSAL, interakcióba kezelésére.

1. Nyissa meg a *App.xaml.cs* fájlt, és adja hozzá a hivatkozás MSAL az osztályra:

    ```csharp
    using Microsoft.Identity.Client;
    ```
<!-- Workaround for Docs conversion bug -->

2. Frissítse az alkalmazás osztály a következőhöz:

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

Ez a szakasz bemutatja, hogyan tudja lekérdezni egy alkalmazást a védett háttér-kiszolgálók, például a Microsoft Graph. 

A *MainWindow.xaml* fájl automatikusan létrejöjjön a projekt sablon részeként. Nyissa meg a fájlt, és lecseréli a az alkalmazás  *\<rács >* csomópont a következő kóddal:

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

