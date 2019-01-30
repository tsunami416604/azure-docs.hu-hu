---
title: 'Oktatóanyag: Egy entitás hivatkozási alkalmazás összeállítása –C#'
titlesuffix: Azure Cognitive Services
description: Az Entity Linking API használatával szöveg és hivatkozás nevű entitások elemezését végezheti el a tudásbázis megfelelő bejegyzései alapján.
services: cognitive-services
author: DavidLiCIG
manager: cgronlun
ms.service: cognitive-services
ms.subservice: entity-linking-intelligence
ms.topic: tutorial
ms.date: 07/06/2016
ms.author: davl
ms.openlocfilehash: 883d566fd3a6089eb9e72498089f995697a318f1
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216812"
---
# <a name="tutorial-build-an-entity-linking-app-with-c"></a>Oktatóanyag: Egy entitás hivatkozási alkalmazás létrehozásaC#

A Microsoft Entity Linking egy természetes nyelvi feldolgozási eszköz, amellyel a szöveg és hivatkozás nevű egyedeknek a tudásbázis megfelelő bejegyzéseivel történő elemzése végezhető el. 

Az oktatóanyag az entitás-összekapcsolást vizsgálja meg az Entity Linking ügyfélkódtár NuGet csomagként történő használatával. 

### <a name="Prerequisites">Előfeltételek</a>

- Visual Studio 2015
- Microsoft Cognitive Services API kulcs
- Az ügyfélkódtár és a példák letöltése
- Microsoft Entity Linking NuGet csomag

Az Entity Linking Intelligence Service API ügyfélkódtára letölthető az [SDK](https://www.github.com/microsoft/cognitive-entitylinking-windows)-n keresztül. A letöltött zip-fájlt ki kell csomagolni egy tetszőleges mappába. Számos felhasználó a Visual Studio 2015 mappát választja.

### <a name="step-1-subscribe-entity-linking-intelligence-service-and-get-your-own-key">1. lépés: Fizessen elő az Entitáskapcsolási intelligens szolgáltatás és a kulcs lekérése</a>
Az Entity Linking Intelligence Service használatához először regisztrálni kell az API kulcsért. Lásd [Előfizetések](https://www.microsoft.com/cognitive-services/en-us/sign-up). Ehhez az oktatóanyaghoz az elsődleges és a másodlagos kulcs is használható.

### <a name="step-2-create-a-new-project-in-visual-studio"> 2. lépés: Hozzon létre egy új projektet a Visual Studióban</a>

Kezdjük azzal, hogy létrehozunk egy új projektet a Visual Studióban. Először indítsa el a Visual Studio 2015-öt a Start menüből. Ezután hozzon létre egy új projektet a **Telepített→ Sablonok → Visual C# → Windows Universal → Üres alkalmazás** kiválasztásával a projekt sablonnak:

 ![Univerzális alkalmazás készítése](./Images/CreateUWP.png)

### <a name="step-3-add-the-entity-linking-nuget-package-to-your-project">3. lépés: Az entitás Linking NuGet csomagjának hozzáadása a projekthez</a>

A Cognitive Services az entitás-összekapcsolást NuGet.org csomagként teszi közzé, használat előtt telepíteni kell.
Hozzáadása a projekthez: a **Megoldáskezelő** lapon kattintson jobb gombbal a projektre és válassza a **Nuget-csomagok kezelése** pontot.

Először a **NuGet-Csomagkezelő** ablakban a jobb felső sarokban adja meg a NuGet.org-ot a **Csomag forrásának**. A bal felső sarokban a **Tallózás** kiválasztása után írja be a keresőmezőbe, hogy „ProjectOxford.EntityLinking”. Válassza ki a **Microsoft.ProjectOxford.EntityLinking** NuGet csomagot, és kattintson a **Telepítésre**.

Ezután keresse meg a Newtonsoft.Json elemet és telepítse. Ha a rendszer kéri a változtatások áttekintését, kattintson az **OK**-ra. Ha megjelennek a EntityLinking licencfeltételei, kattintson az **Elfogadom** lehetőségre.

Az Entity Linking most már telepítve van az alkalmazás részeként. Ezt azzal ellenőrizheti, hogy a Megoldáskezelőben a projekt része-e a ** Microsoft.ProjectOxford.EntityLinking** hivatkozás.

 ![A nuget könyvtár belefoglalása a projektbe](./Images/NugetLibraryInProject.png)
 
### <a name="step-4-add-an-input-and-output-text-block-to-your-apps-xaml">4. lépés: Az alkalmazás XAML ad hozzá egy bemeneti és kimeneti szövegterület</a>
Navigáljon a ** MainPage.xaml ** részhez a **Megoldáskezelőben**, majd kattintson a fájlra, amely megnyílik egy új ablakban. Az egyszerűség kedvéért, ha duplán kattint az **XAML** gombra a **Designer** lapon, azzal elrejtheti a **Vizuális Tervezőt** és minden helyet megkap a kódnézet.

 ![A nuget könyvtár belefoglalása a projektbe](./Images/UWPMainPage.png)
 
Szöveg szolgáltatásról lévén szó, a működés szemléltetésének legjobb módja, ha bemenő és kimenő szövegtömböt hozunk létre. Ehhez adja hozzá a következő XAML-t a **Táblázatba**. Ez a kód három elemet vesz fel, egy szöveges beviteli szövegmezőt, egy kimeneti szövegterületet és az indítógombot.
 
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
 
### <a name="step-5-proceed-to-add-entity-linking-intelligence-service">5. lépés: Hozzáadhatja az Entitáskapcsolási intelligens szolgáltatás</a>
 
A felhasználói felület létrehozása megtörtént. Mielőtt az entitás-összekapcsolás szolgáltatást használhatnánk, hozzá kell adnunk a kattintáskezelőt. Nyissa meg a **MainPage.xaml** fájlt a **Megoldáskezelőben**. A gomb végéhez adja hozzá a button_Click kezelőt.
 
 ```XAML
 <Button x:Name="button" Grid.Row="2" Content="Get Result" Click="button_Click" />
 ```
 
A button_Click kezelőt a kódban kell kifejteni. Nyissa meg **MainPage.xaml.cs** fájlt a **Megoldáskezelőben** a button-Click megvalósításához. Az EntityLinkingServiceClient egy burkoló az entitás-összekapcsolás válaszok lekéréséhez. A EntityLinkingServiceClient konstruktorának argumentuma a Cognitive Services előfizetési kulcs. Az Entitás-összekapcsolás szolgáltatás hívásához illessze be az **1. lépésben** kapott előfizetési kulcsot. 

Az alábbi példa az Entity Linking Service használatával hozzáadja a „wikipediaId”-t a válaszhoz. 
 
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
 
Most már készen áll az első természetes nyelvi feldolgozási entitás-összekapcsolási alkalmazás futtatásához. Nyomja le az **F5 billentyűt** a fordításhoz és az alkalmazás elindításához. Illesszen kódrészlet szöveget vagy bekezdést a bemeneti mezőbe. Nyomja meg a „Eredmény kérése” gombot és figyelje meg az azonosított elemeket a kimeneti tömbben.
 
 ![UWP-minta eredmény](./Images/DemoCodeResult.png)
 
### <a name="summary">összegzés</a>
 
Ebben az oktatóanyagban bemutattuk, hogyan hozhat létre egy alkalmazást az Entity Linking Intelligence Service ügyfélkódtár kihasználásával mindössze néhány C# és XAML sor segítségével. 

