---
title: Az entitás Linking API használatába |} Microsoft Docs
description: Vizsgálja meg a szöveg és a hivatkozás a Tudásbázisban megfelelő bejegyzések entitásokat nevű észlelés szolgáltatásokban az entitás Linking API használatával.
services: cognitive-services
author: DavidLiCIG
manager: wkwok
ms.service: cognitive-services
ms.component: entity-linking-intelligence
ms.topic: article
ms.date: 07/06/2016
ms.author: davl
ms.openlocfilehash: 54c4a3bbb3637c248bd7705ed291633368b542c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347131"
---
# <a name="get-started-with-entity-linking-api-in-c35"></a>Az API-C Linking entitás első lépései&#35;

A Microsoft entitás csatolás akkor szöveg elemzése és nevű entitások csatolása a megfelelő bejegyzések meg a Tudásbázis természetes nyelvű feldolgozási eszköz. 

Ez az oktatóanyag ismerteti, az entitás Linking ügyféloldali kódtára a NuGet-csomagként linking entitás. 

### <a name="Prerequisites">Előfeltételek</a>

- Visual Studio 2015
- A Microsoft kognitív szolgáltatási API-kulcs
- Az ügyfél első könyvtár és példa
- Microsoft-jogalany Linking NuGet-csomag

Az entitás Linking az Eszközintelligencia szolgáltatás API ügyféloldali kódtár segítségével letöltheti [SDK](https://www.github.com/microsoft/cognitive-entitylinking-windows). A letöltött zip-fájl szükséges a kiválasztott mappába kinyerni, sok felhasználó válassza ki a Visual Studio 2015 mappát.

### <a name="step-1-subscribe-entity-linking-intelligence-service-and-get-your-own-key">1. lépés: Entitás csatolása az Eszközintelligencia szolgáltatás előfizetni, és a kulcs lekérése</a>
Entitás csatolása az Eszközintelligencia szolgáltatás használata előtt regisztrálnia kell az API-kulcs. Lásd: [előfizetések](https://www.microsoft.com/cognitive-services/en-us/sign-up). Ebben az oktatóanyagban mind az elsődleges és másodlagos kulcsot is használható.

### <a name="step-2-create-a-new-project-in-visual-studio"> 2. lépés: A Visual Studio új projekt létrehozása</a>

Először hozzon létre egy új projektet a Visual Studióban. Először indítsa el a Visual Studio 2015 a Start menüből. Ezután hozzon létre egy új projektet kiválasztásával **→ sablonok → Visual C# → Windows Universal → üres alkalmazás telepítve** a projekt sablonhoz:

 ![Egy univerzális alkalmazás](./Images/CreateUWP.png)

### <a name="step-3-add-the-entity-linking-nuget-package-to-your-project">3. lépés: A entitás Linking NuGet-csomag hozzáadása a projekthez</a>

Entitás csatolása a kognitív szolgáltatások NuGet.org csomag megjelenik, és használata előtt telepítenie kell.
Adja hozzá a projekthez, keresse fel a **Megoldáskezelőben** lapon kattintson a jobb gombbal a projekt, és válassza ki **Nuget-csomagok kezelése**.

Az első a **NuGet-Csomagkezelő** ablakban válassza NuGet.org, mint a **csomag forrása** jobb felső sarkában található. Válassza ki **Tallózás** bal felső sarokban, és a keresési mezőbe írja be a "ProjectOxford.EntityLinking". Válassza ki a **Microsoft.ProjectOxford.EntityLinking** NuGet csomagot, majd kattintson a **telepítése**.

Ezután keresse meg a newtonsoft.JSON elemet, majd telepíteni. Ha a változtatásokat kéri, kattintson a **OK**. Ha a EntityLinking licencfeltételeket, kattintson a **elfogadom**.

Entitás Linking most már telepítve van az alkalmazás részeként. Győződhet meg úgy, hogy a ** Microsoft.ProjectOxford.EntityLinking** hivatkozás megtalálható a projektben a Megoldáskezelőre részeként.

 ![A projekt tartalmazza nuget könyvtár](./Images/NugetLibraryInProject.png)
 
### <a name="step-4-add-an-input-and-output-text-block-to-your-apps-xaml">4. lépés: Adja hozzá a bemeneti és kimeneti az alkalmazás XAML szövegblokk</a>
Keresse meg ** MainPage.xaml ** a **Megoldáskezelőben**, majd kattintson duplán a fájlra, amely egy új ablakban nyílik. Kényelmi célokat szolgál, akkor is kattintson duplán a **XAML** gombra a **Designer** lapon Ez elrejti a **vizuális tervezőt** és a teljes egészében a kód nézetre a tartalék.

 ![A projekt tartalmazza nuget könyvtár](./Images/UWPMainPage.png)
 
Szolgáltatás a legjobb módszer a funkciók megjelenítése bemeneti és egy kimeneti szövegblokk létrehozása. Ehhez adja hozzá a következő XAML a **rács**. Ez a kód hozzáadása három összetevővel, egy bemeneti szövegmezőben, egy kimeneti szövegblokk és a start gombra.
 
 ```XAML
 <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid.RowDefinitions>
        <RowDefinition Height="*"/>
        <RowDefinition Height="*" />
        <RowDefinition Height="50" />
    </Grid.RowDefinitions>
    <TextBox x:Name="inputBox" Grid.Row="0" TextWrapping="Wrap" Text="Enter a paragraph" Margin="10" AcceptsReturn="True" />
    <TextBlock x:Name="outputBlock" Grid.Row="1" TextWrapping="Wrap" Text="Result will be here" Margin="10" />
    <Button x:Name="button" Grid.Row="2" Content="Get Result" />
</Grid>
 ```
 
### <a name="step-5-proceed-to-add-entity-linking-intelligence-service">5. lépés: Lépjen entitás csatolása az Eszközintelligencia szolgáltatás hozzáadása</a>
 
A felhasználói felület megtörtént. Az entitás Linking szolgáltatás használatához vegye fel a kattintáskezelő kell. Nyissa meg **MainPage.xaml** a **Megoldáskezelőben**. Adja hozzá a button_Click kezelő a gomb végén.
 
 ```XAML
 <Button x:Name="button" Grid.Row="2" Content="Get Result" Click="button_Click" />
 ```
 
Egy kattintáskezelő meg kell valósítani a kódot. Nyissa meg **MainPage.xaml.cs** a **Megoldáskezelőben** megvalósításához a gombra kattintással. A EntityLinkingServiceClient egy burkoló entitás Linking válaszok beolvasása. A EntityLinkingServiceClient konstruktor argumentuma a kognitív szolgáltatások előfizetés kulcs. Illessze be a kapott az Előfizetés kulcs **1. lépés** hívó entitás csatolása. 

Alább van például kód, amely a "wikipediaId" hozzáadja a válasz entitás Linking szolgáltatás segítségével. 
 
 ```csharp
 private async void button_Click(object sender, RoutedEventArgs e)
{
    var text = this.inputBox.Text;
    var client = new EntityLinkingServiceClient("Your subscription key","https://api.labs.cognitive.microsoft.com");
    var linkResponse = await client.LinkAsync(text);
    var result = string.Join(", ", linkResponse.Select(i => i.WikipediaID).ToList());
    this.outputBlock.Text = result;
}
 ```
 
Most már készen áll az első természetes nyelvű feldolgozása entitás Linking alkalmazás futtatásához. Nyomja meg a **F5 billentyűt** fordításához és az alkalmazás elindításához. Vagy szövegrészek bekezdések illessze be a beviteli mezőbe. A "Get eredménye" gombra, és tekintse meg a kimeneti blokk azonosított entitást.
 
 ![UWP-minta eredménye](./Images/DemoCodeResult.png)
 
### <a name="summary">Összefoglalás</a>
 
Ebben az oktatóanyagban korábban megtudta, hogyan entitás csatolása az Eszközintelligencia szolgáltatás ügyféloldali kódtár ki csupán néhány sornyi C# és XAML-kódot az alkalmazás létrehozása. 

