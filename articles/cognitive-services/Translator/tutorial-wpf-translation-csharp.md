---
title: 'Oktatóanyag: Hozzon létre egy fordítási alkalmazást a WPF, C# – Translator Text API'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban létrehozhat egy Windows megjelenítési Alaprendszeri (WPF) alkalmazást, amely a Cognitive Service API-k szövegfordítás, nyelv észlelése és a helyesírás-ellenőrzési egyetlen előfizetés kulccsal. Ebben a gyakorlatban bemutatja, hogyan funkcióinak a Translator Text API és a Bing Spell Check API használatát.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: erhopf
ms.openlocfilehash: 97766472ea5f7b62a452e6cc5a71a77426e975ad
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56235410"
---
# <a name="tutorial-create-a-translation-app-with-wpf"></a>Oktatóanyag: A WPF-fordítási alkalmazás létrehozása

Ez az oktatóanyag során kiépíti egy [Windows megjelenítési Alaprendszeri (WPF)](https://docs.microsoft.com/visualstudio/designers/getting-started-with-wpf?view=vs-2017) alkalmazást, amely az Azure Cognitive Services szövegfordítás, nyelv észlelése és a helyesírás-ellenőrzést is használ egyetlen előfizetés kulccsal. Pontosabban, az alkalmazás fogja API-jainak hívására a Translator Text és [a Bing Spell Check](https://azure.microsoft.com/services/cognitive-services/spell-check/).

Mit jelent a WPF? Felhasználóifelület-keretrendszert, amely létrehozza az asztali alkalmazások. A WPF-fejlesztési platform támogatja az alkalmazás alkalmazásfejlesztési szolgáltatások, többek között egy az app model, erőforrások, vezérlők, grafikus, elrendezése, adatkötés, dokumentumok és biztonsági széles körének köszönhetően. Egy része a .NET-keretrendszert, így ha a korábban létrehozott alkalmazásokat a .NET-keretrendszer használata az ASP.NET vagy Windows Forms, a programozási környezetet tisztában kell lennie. WPF az Extensible Markup Language (XAML) egy deklaratív modellt biztosít programozási, alkalmazás, amely a következő szakaszokban áttekintheti alkalmazást használ.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * A WPF-projekt létrehozása a Visual Studióban
> * Szerelvények és NuGet-csomagok hozzáadása a projekthez
> * Az alkalmazás felhasználói felületén hozzon létre XAML
> * A Translator Text API használatával nyelvek beolvasása, szöveg lefordítása és a Forrásnyelv felismerése
> * A Bing Spell Check API használatával a bemenetet a következők ellenőrzi és javítja a fordítási pontossága
> * A WPF-alkalmazás futtatása

### <a name="cognitive-services-used-in-this-tutorial"></a>A jelen oktatóanyagban használt, a cognitive Services

Ez a lista tartalmazza a Cognitive Services, a jelen oktatóanyagban használt. Kövesse a hivatkozásra kattintva keresse meg az API-referencia, az egyes szolgáltatásokhoz.

| Szolgáltatás | Szolgáltatás | Leírás |
|---------|---------|-------------|
| Fordítói szöveg | [Nyelvek beolvasása](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) | Szövegfordítás támogatott nyelvekről teljes listájának lekéréséhez. |
| Fordítói szöveg | [Fordítása](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) | Szövegfordítás több mint 60 nyelvet. |
| Fordítói szöveg | [Észlelése](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect) | Észlelje a nyelvet, amely a bemeneti szöveg. Észlelési magabiztossági pontszámot tartalmaz. |
| Bing – Helyesírás-ellenőrzés | [Helyesírás-ellenőrzés](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference) | Kijavíthatja a helyesírási hibák fordítási pontosságának növelése érdekében. |

## <a name="prerequisites"></a>Előfeltételek

A folytatás előtt a következőkre lesz szüksége:

* Az Azure Cognitive Services-előfizetés. [Cognitive Services kulcs lekérése](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#multi-service-subscription).
* Egy Windows-gép
* [A Visual Studio 2017](https://www.visualstudio.com/downloads/) -közösségi vagy vállalati

> [!NOTE]
> Azt javasoljuk, hogy az előfizetés létrehozása az USA nyugati régiójában ehhez az oktatóanyaghoz. Ellenkező esetben kell módosítani a végpontok és a kód régiók ebben a gyakorlatban keresztül végzett munka.  

## <a name="create-a-wpf-app-in-visual-studio"></a>WPF-alkalmazás létrehozása a Visual Studióban

Először azt kell tennünk, hogy a projekt a Visual Studióban be van állítva.

1. Nyissa meg a Visual Studiót. Válassza ki **File > New > Project**.
2. A bal oldali panelen keresse meg és jelölje ki **Visual C#** . Ezután válassza ki **WPF App (.NET Framework)** a középső panelen.
   ![WPF-alkalmazás létrehozása a Visual Studióban](media/create-wpf-project-visual-studio.png)
3. Nevezze el a projektet, állítsa a Framework **.NET-keretrendszer 4.5.2-es vagy újabb**, majd kattintson a **OK**.
4. A projekt létrejött. Láthatja, hogy nincsenek két lap megnyitása: `MainWindow.xaml` és `MainWindow.xaml.cs`. Ez az oktatóanyag során adunk hozzá kódot a két fájlt. Az alkalmazás felhasználói felületén; az első az utóbbi a Translator Text és a Bing Spell Check-hívásokhoz.
   ![A környezet áttekintése](media/blank-wpf-project.png)

A következő szakaszban fogjuk szerelvényeket és a egy NuGet-csomag hozzáadása a projekthez, további funkciók, például JSON-elemzés.

## <a name="add-references-and-nuget-packages-to-your-project"></a>Hivatkozások és a NuGet-csomagok hozzáadása a projekthez

A projekt sestavení rozhraní .NET Framework és a newtonsoft.JSON elemet, amely a NuGet-Csomagkezelő használatával telepíteni fogunk néhány szükséges.

### <a name="add-net-framework-assemblies"></a>Sestavení rozhraní .NET Framework hozzáadása

Adjunk szerelvények szerializálható és deszerializálható objektumokat, és kezelheti a HTTP-kérések és válaszok a projekthez.

1. Keresse meg a projekt a Visual Studio Solution Explorer (jobb oldali panelen). Kattintson jobb gombbal a projektre, majd válassza ki **Hozzáadás > hivatkozás...** , ekkor **hivatkozáskezelő**.
   ![Szerelvényhivatkozások hozzáadása](media/add-assemblies-sample.png)
2. A szerelvények lap felsorolja az összes sestavení rozhraní .NET Framework való hivatkozáshoz elérhető. A, a képernyő jobb felső sarokban a Keresősáv használatával keresse meg ezeket a hivatkozásokat, és adja hozzá a projekthez:
   * [System.Runtime.Serialization](https://docs.microsoft.com/dotnet/api/system.runtime.serialization?view=netframework-4.7.2)
   * [System.Web](https://docs.microsoft.com/dotnet/api/system.web?view=netframework-4.7.2)
   * [System.Web.Extensions](https://docs.microsoft.com/dotnet/api/system.web?view=netframework-4.7.2)
3. Miután hozzáadta a projekthez mutató hivatkozásokat, kattinthat **OK** bezárásához **hivatkozáskezelő**.

> [!NOTE]
> Ha szeretne további információ az összeállítási referenciát, lásd: [hogyan: Adja hozzá, vagy távolítsa el a referencia-kezelő segítségével hivatkozást](https://docs.microsoft.com/visualstudio/ide/how-to-add-or-remove-references-by-using-the-reference-manager?view=vs-2017).

### <a name="install-newtonsoftjson"></a>Telepítse a newtonsoft.JSON elemet

Az alkalmazás fogja használni a NewtonSoft.Json JSON-objektumok deszerializálása. Kövesse az alábbi utasításokat a csomag telepítéséhez.

1. Keresse meg a projekt a Visual Studio Megoldáskezelőben, és kattintson jobb gombbal a projektre. Válassza ki **NuGet-csomagok kezelése...** .
2. Keresse meg és válassza ki a **Tallózás** fülre.
3. Típus [NewtonSoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) kifejezést a keresősávba.
   ![Keresse meg és telepítse a newtonsoft.JSON elemet](media/add-nuget-packages.png)
4. Válassza ki a csomagot, és kattintson a **telepítése**.
5. A telepítés befejeződése után a lap bezárásához.

## <a name="create-a-wpf-form-using-xaml"></a>XAML használatával WPF űrlap létrehozása

Az alkalmazás használatához fog egy felhasználói felületet kell. XAML használatával, létrehozunk egy űrlapot, amely lehetővé teszi, hogy a bemeneti és a fordítás nyelveket választ ki, adja meg a szöveg fordítását, hogy a felhasználók és a fordítási kimenetet jeleníti meg.

Vessünk egy pillantást, hogy milyen hozunk létre.

![WPF XAML felhasználói felület](media/translator-text-csharp-xaml.png)

A felhasználó interfacer alábbi összetevőket tartalmazza:

| Name (Név) | Típus | Leírás |
|------|------|-------------|
| `FromLanguageComboBox` | Kombinált lista | Szöveg fordítása a Microsoft Translator által támogatott nyelvek listáját jeleníti meg. A felhasználó kiválasztja azt a nyelvet, amelyről a fordítás történik. |
| `ToLanguageComboBox` | Kombinált lista | Nyelvek mellett azonos listáját jeleníti meg `FromComboBox`, de válassza ki a nyelvet, a felhasználó van fordítása való használatával. |
| `TextToTranslate` | TextBox | Lehetővé teszi a felhasználónak meg kell adnia a fordítandó szöveg. |
| `TranslateButton` | Gomb | Szöveg lefordítása a gomb használatával. |
| `TranslatedTextLabel` | Címke | A fordítás jeleníti meg. |
| `DetectedLanguageLabel` | Címke | Megjeleníti a fordítandó szöveg felismert nyelv (`TextToTranslate`). |

> [!NOTE]
> Az űrlap a XAML forráskód használatával hozunk létre, azonban létrehozhat az űrlap a szerkesztőben a Visual Studióban.

Most adja hozzá a kódot a projekthez.

1. A Visual Studióban válassza ki a lapon `MainWindow.xaml`.
2. Másolja ezt a kódot a projekthez, és mentse.
   ```xaml
   <Window x:Class="MSTranslatorTextDemo.MainWindow"
           xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
           xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
           xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
           xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
           xmlns:local="clr-namespace:MSTranslatorTextDemo"
           mc:Ignorable="d"
           Title="Microsoft Translator" Height="400" Width="700" BorderThickness="0">
       <Grid>
           <Label x:Name="label" Content="Microsoft Translator" HorizontalAlignment="Left" Margin="39,6,0,0" VerticalAlignment="Top" Height="49" FontSize="26.667"/>
           <TextBox x:Name="TextToTranslate" HorizontalAlignment="Left" Height="23" Margin="42,160,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="600" FontSize="14" TabIndex="3"/>
           <Label x:Name="EnterTextLabel" Content="Text to translate:" HorizontalAlignment="Left" Margin="40,129,0,0" VerticalAlignment="Top" FontSize="14"/>
           <Label x:Name="toLabel" Content="Translate to:" HorizontalAlignment="Left" Margin="304,58,0,0" VerticalAlignment="Top" FontSize="14"/>

           <Button x:Name="TranslateButton" Content="Translate" HorizontalAlignment="Left" Margin="39,206,0,0" VerticalAlignment="Top" Width="114" Height="31" Click="TranslateButton_Click" FontSize="14" TabIndex="4" IsDefault="True"/>
           <ComboBox x:Name="ToLanguageComboBox"
                   HorizontalAlignment="Left"
                   Margin="306,88,0,0"
                   VerticalAlignment="Top"
                   Width="175" FontSize="14" TabIndex="2">

           </ComboBox>
           <Label x:Name="fromLabel" Content="Translate from:" HorizontalAlignment="Left" Margin="40,58,0,0" VerticalAlignment="Top" FontSize="14"/>
           <ComboBox x:Name="FromLanguageComboBox"
               HorizontalAlignment="Left"
               Margin="42,88,0,0"
               VerticalAlignment="Top"
               Width="175" FontSize="14" TabIndex="1"/>
           <Label x:Name="TranslatedTextLabel" Content="Translation is displayed here." HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
           <Label x:Name="DetectedLanguageLabel" Content="Autodetected language is displayed here." HorizontalAlignment="Left" Margin="39,288,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="84" BorderThickness="0"/>
       </Grid>
   </Window>
   ```
3. Meg kell jelennie egy előzetes verziója az alkalmazás felhasználói felület a Visual Studióban. A fenti képen hasonlóan kell kinéznie.

Ennyi az egész, készen áll az űrlapon. Most pedig írjon egy kódrészletet Szövegfordítás és a Bing helyesírás-ellenőrzés.

> [!NOTE]
> Nyugodtan Teljesítménybeállítások ezt a kérdőívet, vagy létrehozhatja a sajátját.

## <a name="create-your-app"></a>Az alkalmazás létrehozása

`MainWindow.xaml.cs` tartalmazza a kódot, amely az alkalmazás szabályozza. A következő néhány szakaszban fogjuk adja hozzá a kódot a legördülő menükben feltöltéséhez és minősített által a Translator Text és a Bing Spell Check API meghívásához.

* Amikor a program elindul, és `MainWindow` létrejön, a `Languages` a Translator Text API-módszert hívja meg lekérni, és töltse fel a nyelv kiválasztása legördülő listákból. Ez után minden munkamenet elején történik.
* Ha a **fordítás** gombra kattint, a felhasználó nyelvének kiválasztása és szöveges adatok lekérése, helyesírás-ellenőrzést hajtanak végre a a bemeneti és a felhasználó megjelenik a fordítás és felismert nyelv.
  * A `Translate` a Translator Text API-módszert hívja meg a szöveg lefordítása `TextToTranslate`. Ez a hívás is magában foglalja a `to` és `from` a legördülő menük használatával kiválasztott nyelvek.
  * A `Detect` a Translator Text API-módszert hívja meg meghatározni a szöveg nyelvét `TextToTranslate`.
  * A Bing helyesírás-ellenőrzés segítségével megállapítható `TextToTranslate` és módosítsa úgy a elírások.

A projekt összes van beágyazva a `MainWindow : Window` osztály. Először adja hozzá a kódot az előfizetési kulcs, végpontok deklarálja a Translator Text és a Bing Spell Check, és az alkalmazás inicializálása.

1. A Visual Studióban válassza ki a lapon `MainWindow.xaml.cs`.
2. Cserélje le az előre összeállított `using` az alábbi utasításokat.  
   ```csharp
   using System;
   using System.Windows;
   using System.Net;
   using System.Net.Http;
   using System.IO;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using Newtonsoft.Json;
   ```
3. Keresse meg a `MainWindow : Window` osztályt, és cserélje le ezt a kódot:
   ```csharp
   {
       // This sample uses the Cognitive Services subscription key for all services. To learn more about
       // authentication options, see: https://docs.microsoft.com/azure/cognitive-services/authentication.
       const string COGNITIVE_SERVICES_KEY = "YOUR_COG_SERVICES_KEY";
       // Endpoints for Translator Text and Bing Spell Check
       public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api- version=3.0";
       const string BING_SPELL_CHECK_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/bing/v7.0/spellcheck/";
       // An array of language codes
       private string[] languageCodes;

       // Dictionary to map language codes from friendly name (sorted case-insensitively on language name)
       private SortedDictionary<string, string> languageCodesAndTitles =
           new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

       // Global exception handler to display error message and exit
       private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
       {
           Exception e = (Exception)args.ExceptionObject;
           MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
           System.Windows.app.Current.Shutdown();
       }
       // MainWindow constructor
       public MainWindow()
       {
           // Display a message if unexpected error is encountered
           AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

           if (COGNITIVE_SERVICES_KEY.Length != 32)
           {
               MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                   "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                   "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
               System.Windows.app.Current.Shutdown();
           }
           else
           {
               // Start GUI
               InitializeComponent();
               // Get languages for drop-downs
               GetLanguagesForTranslate();
               // Populate drop-downs with values from GetLanguagesForTranslate
               PopulateLanguageMenus();
           }
       }
   // NOTE:
   // In the following sections, we'll add code below this.
   }
   ```
   4. Adja meg a Cognitive Services előfizetési kulcsot, és mentse.

A kód blokk azt már deklarálva két tagváltozók elérhető nyelveket a fordítás adatait tartalmazzák:

| Változó | Typo | Leírás |
|----------|------|-------------|
|`languageCodes` | karakterláncok tömbje |C nyelvkódok aches. A Translator szolgáltatás rövid kódokat használ a nyelvek azonosítására (például: `en` = angol). |
|`languageCodesAndTitles` | Rendezett szótár | A felhasználói felületen megjelenő „felhasználóbarát” neveket képezi le az API által használt rövid kódokra. Az elemeket betűrendbe rendezi, és nem veszi figyelembe a nagy- és kisbetűket. |

Ezt követően belül a `MainWindow` konstruktor, tettünk elérhetővé a hibakezelési `HandleExceptions`. Ez biztosítja, hogy adott-e riasztást, ha kivétel nem kezelt. Ezután a rendszer ellenőrzi, erősítse meg a megadott előfizetési kulcs hossza 32 karakter hosszú lehet. Hiba fordul elő, ha a kulcs kisebb, mint / 32 karakternél.

Ha vannak, legalább a megfelelő hosszát, a kulcsok a `InitializeComponent()` hívás lekérdezi a felhasználói felület működés közbeni keresés, betöltés és hárítható el a fő alkalmazás ablakának XAML leírása alapján.

Utolsó lehetőségekkel bővült a fordítás nyelvek beolvasása, és töltse fel az alkalmazás felhasználói felületén a legördülő menük módszerek meghívásához. Ne aggódjon, a leghamarabb elindítjuk a hívásokat a háttérkód.

## <a name="get-supported-languages"></a>Támogatott nyelvek lekérése

A Translator Text API jelenleg több mint 60 nyelvet támogat. Új nyelvi támogatás hozzáadása is várható, mivel ajánlott a nyelvi listájáról az alkalmazások hardcoding helyett a Translator Text által elérhetővé tett nyelvek erőforrás hívása.

Ebben a szakaszban létrehozunk egy `GET` kérelem a nyelvek erőforráshoz, adja meg, hogy a fordítás kívánt elérhető nyelvek listáját.

> [!NOTE]
> A nyelvek erőforrás lehetővé teszi, hogy a nyelvi támogatás a következő lekérdezési paraméterek szűrése: átbetűzésű szótár és fordítási. További információkért lásd: [API-referencia](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

Mielőtt továbbmennénk, vessünk egy pillantást a nyelvek erőforrás hívása egy mintakimenet:

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    }
    // Additional languages are provided in the full JSON output.
}
```

A kimenetből azt a nyelvi kód kinyerése és a `name` , egy adott nyelven. Az alkalmazásunk NewtonSoft.Json használja a JSON-objektum deszerializálása ([`JsonConvert.DeserializeObject`](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm)).

Vesz fel, ahol abbahagytuk az előző szakaszban ismertetett, adjon hozzá egy metódus az alkalmazásnak a támogatott nyelvek lekéréséhez.

1. A Visual Studióban nyissa meg a lap `MainWindow.xaml.cs`.
2. Ez a kód hozzáadása a projekthez:
   ```csharp
   // ***** GET TRANSLATABLE LANGUAGE CODES
   private void GetLanguagesForTranslate()
   {
       // Send request to get supported language codes
       string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
       WebRequest WebRequest = WebRequest.Create(uri);
       WebRequest.Headers.Add("Accept-Language", "en");
       WebResponse response = null;
       // Read and parse the JSON response
       response = WebRequest.GetResponse();
       using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
       {
           var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
           var languages = result["translation"];

           languageCodes = languages.Keys.ToArray();
           foreach (var kv in languages)
           {
               languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
           }
       }
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

A `GetLanguagesForTranslate()` metódus egy HTTP GET kérést hoz létre, és használja a `scope=translation` lekérdezési karakterlánc paramétereként támogatott nyelvek a kérést a fordítás hatókörének korlátozására szolgál. Az `en` értékű `Accept-Language` fejléc hozzáadásával a támogatott nyelvek neve angolul jelenik meg.

A JSON-válasz elemezni, és a egy szótárban alakítani. Ezután a nyelvkódokról kerülnek a `languageCodes` tag változó. A nyelvi kódokat és a nyelvek felhasználóbarát nevét tartalmazó kulcs-érték-párokból az alkalmazás egy hurkot hoz létre, majd hozzáadja azokat a `languageCodesAndTitles` tagváltozóhoz. Az űrlap a legördülő menük felhasználóbarát nevek megjelenítése, de a kódokat a fordítási kérelem van szükség.

## <a name="populate-language-drop-down-menus"></a>Töltse ki a nyelvet a legördülő menük

A felhasználói felület használatával XAML, így nem kell tennie, sok mindent kell beállítása mellett hívás van definiálva `InitializeComponent()`. Kell tennie az egyik dolog, a nyelv rövid nevét adja hozzá a **fordítani** és **fordítás** legördülő menük, ez a lépés a `PopulateLanguageMenus()` metódus.

1. A Visual Studióban nyissa meg a lap `MainWindow.xaml.cs`.
2. Ez a kód hozzáadása a projekthez az alábbi a `GetLanguagesForTranslate()` módszer:
   ```csharp
   private void PopulateLanguageMenus()
   {
       // Add option to automatically detect the source language
       FromLanguageComboBox.Items.Add("Detect");

       int count = languageCodesAndTitles.Count;
       foreach (string menuItem in languageCodesAndTitles.Keys)
       {
           FromLanguageComboBox.Items.Add(menuItem);
           ToLanguageComboBox.Items.Add(menuItem);
       }

       // Set default languages
       FromLanguageComboBox.SelectedItem = "Detect";
       ToLanguageComboBox.SelectedItem = "English";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Ez a módszer ismétel a `languageCodesAndTitles` szótár, és mindkét menük ad hozzá minden egyes kulcs. A menük fel van töltve, miután alapértelmezés szerint a kezdő és a nyelv beállítása **hibakeresés** és **angol** jelölik.

> [!TIP]
> Ha a menüknek nincs alapértelmezett értéke, a felhasználó a forrásnyelv és a célnyelv kiválasztása nélkül nem kattinthat a **Translate** (Fordítás) gombra. Ha vannak alapértelmezett beállítások, a felhasználónak nem kell ezzel törődnie.

Most, hogy `MainWindow` inicializálása megtörtént, és a felhasználói felület létrehozása, ez a kód futtatásának megakadályozása amíg a **fordítás** gombra kattint.

## <a name="detect-language-of-source-text"></a>Forrás szöveg nyelvének észlelése

Most a létrehozási módszerének, észlelje a nyelvet a forrás szöveg (a szövegterület megadott szöveg) fogunk a Translator Text API használatával. A kérelem által visszaadott értéket később lesz használható a fordítási kérelem.

1. A Visual Studióban nyissa meg a lap `MainWindow.xaml.cs`.
2. Ez a kód hozzáadása a projekthez az alábbi a `PopulateLanguageMenus()` módszer:
   ```csharp
   // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
   private string DetectLanguage(string text)
   {
       string detectUri = string.Format(TEXT_TRANSLATION_API_ENDPOINT ,"detect");

       // Create request to Detect languages with Translator Text
       HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(detectUri);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
       detectLanguageWebRequest.ContentType = "app/json; charset=utf-8";
       detectLanguageWebRequest.Method = "POST";

       // Send request
       var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
       string jsonText = serializer.Serialize(text);

       string body = "[{ \"Text\": " + jsonText + " }]";
       byte[] data = Encoding.UTF8.GetBytes(body);

       detectLanguageWebRequest.ContentLength = data.Length;

       using (var requestStream = detectLanguageWebRequest.GetRequestStream())
           requestStream.Write(data, 0, data.Length);

       HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

       // Read and parse JSON response
       var responseStream = response.GetResponseStream();
       var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
       dynamic jsonResponse = serializer.DeserializeObject(jsonString);

       // Fish out the detected language code
       var languageInfo = jsonResponse[0];
       if (languageInfo["score"] > (decimal)0.5)
       {
           DetectedLanguageLabel.Content = languageInfo["language"];
           return languageInfo["language"];
       }
       else
           return "Unable to confidently detect input language.";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Ez a módszer létrehoz egy HTTP `POST` kérelem a hibakeresés erőforráshoz. Egy egyetlen argumentumot vesz `text`, amelyeket továbbít van, a kérelem törzsében. Később azt a fordítási kérelem, a felhasználói felületen megadott szöveg létrehozásakor fog adható át ezt a módszert a nyelvfelismerés.

Ez a módszer emellett a megbízhatósági pontszám a válasz értékeli ki. A pontszám értéke nagyobb, mint ha `0.5`, majd a felhasználói felületen jelenik meg a felismert nyelv.

## <a name="spell-check-the-source-text"></a>Helyesírás-ellenőrzés a forrás-szöveg

Most létre fogunk egy metódust helyesírás-ellenőrzése a forrás szöveget, a Bing Spell Check API használatával. Ez biztosítja, hogy felvesszük pontos fordítások Translator Text API-ból. A forrás szövegre korrekciókat a fordítási továbbítódnak kérése, amikor a **fordítás** gombra kattint.

1. A Visual Studióban nyissa meg a lap `MainWindow.xaml.cs`.
2. Ez a kód hozzáadása a projekthez az alábbi a `DetectLanguage()` módszer:

```csharp
// ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
private string CorrectSpelling(string text)
{
    string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

    // Create a request to Bing Spell Check API
    HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
    spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
    spellCheckWebRequest.Method = "POST";
    spellCheckWebRequest.ContentType = "app/x-www-form-urlencoded"; // doesn't work without this

    // Create and send the request
    string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
    byte[] data = Encoding.UTF8.GetBytes(body);
    spellCheckWebRequest.ContentLength = data.Length;
    using (var requestStream = spellCheckWebRequest.GetRequestStream())
        requestStream.Write(data, 0, data.Length);
    HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

    // Read and parse the JSON response; get spelling corrections
    var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
    var responseStream = response.GetResponseStream();
    var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
    dynamic jsonResponse = serializer.DeserializeObject(jsonString);
    var flaggedTokens = jsonResponse["flaggedTokens"];

    // Construct sorted dictionary of corrections in reverse order (right to left)
    // This ensures that changes don't impact later indexes
    var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
    for (int i = 0; i < flaggedTokens.Length; i++)
    {
        var correction = flaggedTokens[i];
        var suggestion = correction["suggestions"][0];  // Consider only first suggestion
        if (suggestion["score"] > (decimal)0.7)         // Take it only if highly confident
            corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
    }

    // Apply spelling corrections, in order, from right to left
    foreach (int i in corrections.Keys)
    {
        var oldtext = corrections[i][0];
        var newtext = corrections[i][1];

        // Apply capitalization from original text to correction - all caps or initial caps
        if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
        else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

        text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
    }
    return text;
}
// NOTE:
// In the following sections, we'll add code below this.
```

## <a name="translate-text-on-click"></a>Kattintson a szöveg lefordítása

A legutolsó dolog, hogy kell tennünk, hozzon létre egy metódust, amelyet ha meghívása a **fordítás** a felhasználói felület frissítésén gombra kattint.

1. A Visual Studióban nyissa meg a lap `MainWindow.xaml.cs`.
2. Ez a kód hozzáadása a projekthez az alábbi a `CorrectSpelling()` metódust, és mentse:  
   ```csharp
   // ***** PERFORM TRANSLATION ON BUTTON CLICK
   private async void TranslateButton_Click(object sender, EventArgs e)
   {
       string textToTranslate = TextToTranslate.Text.Trim();

       string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
       string fromLanguageCode;

       // auto-detect source language if requested
       if (fromLanguage == "Detect")
       {
           fromLanguageCode = DetectLanguage(textToTranslate);
           if (!languageCodes.Contains(fromLanguageCode))
           {
               MessageBox.Show("The source language could not be detected automatically " +
                   "or is not supported for translation.", "Language detection failed",
                   MessageBoxButton.OK, MessageBoxImage.Error);
               return;
           }
       }
       else
           fromLanguageCode = languageCodesAndTitles[fromLanguage];

       string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

       // spell-check the source text if the source language is English
       if (fromLanguageCode == "en")
       {
           if (textToTranslate.StartsWith("-"))    // don't spell check in this case
               textToTranslate = textToTranslate.Substring(1);
           else
           {
               textToTranslate = CorrectSpelling(textToTranslate);
               TextToTranslate.Text = textToTranslate;     // put corrected text into input field
           }
       }
       // handle null operations: no text or same source/target languages
       if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
       {
           TranslatedTextLabel.Content = textToTranslate;
           return;
       }

       // send HTTP request to perform the translation
       string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
       string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

       System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
       var requestBody = JsonConvert.SerializeObject(body);

       using (var client = new HttpClient())
       using (var request = new HttpRequestMessage())
       {
           request.Method = HttpMethod.Post;
           request.RequestUri = new Uri(uri);
           request.Content = new StringContent(requestBody, Encoding.UTF8, "app/json");
           request.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
           request.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
           request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

           var response = await client.SendAsync(request);
           var responseBody = await response.Content.ReadAsStringAsync();

           var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
           var translation = result[0]["translations"][0]["text"];

           // Update the translation field
           TranslatedTextLabel.Content = translation;
       }
   }
   ```

Az első lépés, hogy kap a "feladó" és "a" nyelveket és a felhasználó az űrlapon megadott szöveg. Ha az adatforrás nyelvi beállítása **hibakeresés**, `DetectLanguage()` hívja meg a forrás szöveg nyelvét határozza meg. Lehet, hogy a szöveg egy nyelvet, amely a Translator API nem támogatja. Ebben az esetben jelennek meg a megjelenő üzenet tájékoztatja a felhasználót, és nem fordítja le a szöveget adja vissza.

Amennyiben a forrásnyelv az angol (akár megadja ezt, akár a rendszer maga ismeri fel), a `CorrectSpelling()` használatával ellenőrizheti, valamint szükség esetén javíthatja a helyesírást. A javított szöveg van újra hozzáadja őket a szövegmező úgy, hogy a felhasználó látja, hogy történt-e a javítást.

Szöveg lefordítása a kódot Bizonyára ismerős lesz: az URI-t hozhat létre, hozzon létre egy kérést, küldje el és a válasz elemzéséhez. A JSON-tömböt a fordítás egynél több objektumot is tartalmazhat, azonban az alkalmazás csak kéri.

A kérelem sikeres, miután `TranslatedTextLabel.Content` helyére a `translation`, amely frissíti a felhasználói felület a lefordított szöveget jeleníthet meg.

## <a name="run-your-wpf-app"></a>A WPF-alkalmazás futtatása

Ennyi az egész, van egy működő fordítási alkalmazása WPF használatával jönnek létre. Az alkalmazás futtatásához kattintson a **Start** gomb a Visual Studióban.

## <a name="source-code"></a>Forráskód

Ez a projekt forráskódja elérhető a Githubon.

* [Forráskód tallózása](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp-Tutorial)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Microsoft Translator Text API-referencia](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
