---
title: 'Oktatóanyag: Hozzon létre egy fordítóalkalmazást WPF, C# - Translator Text API-val'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy WPF-alkalmazást hoz létre a szövegfordítás, a nyelvfelismerés és a helyesírás-ellenőrzés egyetlen előfizetési kulccsal történő végrehajtásához.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: ecb42d200eb8808f6bfa4cfb91e98909e350038b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118603"
---
# <a name="tutorial-create-a-translation-app-with-wpf"></a>Oktatóanyag: Fordítási alkalmazás létrehozása WPF-fel

Ebben az oktatóanyagban egy [Windows-megjelenítési alaprendszer (WPF)](https://docs.microsoft.com/visualstudio/designers/getting-started-with-wpf?view=vs-2019) alkalmazást hozhat létre, amely az Azure Cognitive Services szolgáltatást használja a szövegfordításhoz, a nyelvfelismeréshez és a helyesírás-ellenőrzéshez egyetlen előfizetési kulccsal. Pontosabban az alkalmazás meghívja az API-kat a Translator Text és a [Bing Spell Check segítségével.](https://azure.microsoft.com/services/cognitive-services/spell-check/)

Mi az a WPF? Ez egy felhasználói felületkeretrendszer, amely asztali ügyfélalkalmazásokat hoz létre. A WPF fejlesztői platform alkalmazásfejlesztési funkciók széles körét támogatja, beleértve az alkalmazásmodellt, az erőforrásokat, a vezérlőket, a grafikákat, az elrendezést, az adatkötést, a dokumentumokat és a biztonságot. Ez a ASP.NET . A WPF az Extensible app Markup Language (XAML) alkalmazást használja az alkalmazásprogramozás deklaratív modelljének biztosításához, amelyet a következő szakaszokban tekintünk át.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * WPF-projekt létrehozása a Visual Studióban
> * Szerelvények és NuGet csomagok hozzáadása a projekthez
> * Az alkalmazás felhasználói felületének létrehozása xaml-lel
> * A Translator Text API segítségével nyelveket szerezhet be, szöveget fordítva és a forrásnyelv észleléséhez
> * A Bing helyesírás-ellenőrző API-jával ellenőrizheti a bevitelt, és javíthatja a fordítás pontosságát
> * A WPF alkalmazás futtatása

### <a name="cognitive-services-used-in-this-tutorial"></a>Az oktatóanyagban használt Cognitive Services

Ez a lista tartalmazza az oktatóanyagban használt Cognitive Services. Kövesse a hivatkozást az egyes funkciók API-hivatkozásának tallózásához.

| Szolgáltatás | Szolgáltatás | Leírás |
|---------|---------|-------------|
| Fordítói szöveg | [Nyelvek beszereznie](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) | A szövegfordítástámogatott nyelveinek teljes listájának beolvasása. |
| Fordítói szöveg | [Fordítás](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) | A szöveget több mint 60 nyelvre fordítsa le. |
| Fordítói szöveg | [Kinyomoz](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect) | A beviteli szöveg nyelvének észlelése. Tartalmazza az észlelésmegbízhatósági pontszámot. |
| Bing – Helyesírás-ellenőrzés | [Spell Check](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference) | A fordítás pontosságának javítása érdekében javítsa ki a helyesírási hibákat. |

## <a name="prerequisites"></a>Előfeltételek

Mielőtt folytatnánk, a következőkre lesz szüksége:

* Azure Cognitive Services-előfizetés. [Beszerezni egy Cognitive Services kulcsot.](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#create-a-new-azure-cognitive-services-resource)
* A Windows gép
* [Visual Studio 2019](https://www.visualstudio.com/downloads/) - Közösség vagy enterprise

> [!NOTE]
> Azt javasoljuk, hogy hozza létre az előfizetést az USA nyugati régiójában az oktatóanyaghoz. Ellenkező esetben módosítania kell a végpontokat és a régiókat a kódban a gyakorlat során.  

## <a name="create-a-wpf-app-in-visual-studio"></a>WPF-alkalmazás létrehozása a Visual Studióban

Az első dolog, amit meg kell tennünk, hogy a Visual Studio-ban állítjuk be a projektet.

1. Nyissa meg a Visual Studiót. Válassza **az Új projekt létrehozása**lehetőséget.
1. Az **Új projekt létrehozása**csoportban keresse meg és válassza a **WPF-alkalmazást (.NET Framework)**. A C# lehetőséget a **Nyelv** ből választva szűkítheti a beállításokat.
1. Válassza a **Tovább**lehetőséget, `MSTranslatorTextDemo`majd nevezze el a projektet.
1. Állítsa a keretrendszer **4.7.2-es** vagy újabb **Create**verzióját .
   ![Írja be a nevét és a keretrendszer verzióját a Visual Studióban](media/name-wpf-project-visual-studio.png)

A projekt létrejött. Észre fogod venni, hogy két `MainWindow.xaml` lap `MainWindow.xaml.cs`van megnyitva: és . Ebben az oktatóanyagban kódot adunk hozzá ehhez a két fájlhoz. Módosítjuk `MainWindow.xaml` az alkalmazás felhasználói felületét. A Fordítószöveg `MainWindow.xaml.cs` és a Bing helyesírás-ellenőrzésre irányuló hívásaink at módosítjuk.
   ![A környezet áttekintése](media/blank-wpf-project.png)

A következő szakaszban összeállításokat és nuget csomagot adunk a projektünkhöz további funkciókért, például a JSON-elemzéshez.

## <a name="add-references-and-nuget-packages-to-your-project"></a>Hivatkozások és NuGet csomagok hozzáadása a projekthez

Projektünkhöz egy maroknyi .NET Framework szerelvényre és NewtonSoft.Json-ra van szükség, amelyeket a NuGet csomagkezelővel telepítünk.

### <a name="add-net-framework-assemblies"></a>.NET keretrendszer-szerelvények hozzáadása

Adjunk hozzá összeállításokat a projektünkhöz az objektumok szerializálásához és deszerializálásához, valamint a HTTP-kérések és -válaszok kezeléséhez.

1. Keresse meg a projektet a Visual Studio Solution Explorer programban. Kattintson a jobb gombbal a projektre, majd válassza a Hivatkozás **hozzáadása >**, amely megnyitja **a Referenciakezelőt.**
1. **Az Összeállítások** lap felsorolja az összes hivatkozott . A jobb felső sarokban található keresősáv segítségével keressen hivatkozásokat.
   ![Összeállítási hivatkozások hozzáadása](media/add-assemblies-2019.png)
1. Válassza ki a következő hivatkozásokat a projekthez:
   * [System.Runtime.Serialization](https://docs.microsoft.com/dotnet/api/system.runtime.serialization)
   * [System.Web](https://docs.microsoft.com/dotnet/api/system.web)
   * System.Web.Extensions
   * [Rendszer.Windows](https://docs.microsoft.com/dotnet/api/system.windows)
1. Miután hozzáadta ezeket a hivatkozásokat a projekthez, az **OK** gombra kattintva becsukhatja a **Referenciakezelőt.**

> [!NOTE]
> Ha többet szeretne megtudni az összeállítási hivatkozásokról, olvassa [el a Hivatkozás hozzáadása vagy eltávolítása a Referenciakezelővel című témakört.](https://docs.microsoft.com/visualstudio/ide/how-to-add-or-remove-references-by-using-the-reference-manager?view=vs-2019)

### <a name="install-newtonsoftjson"></a>A NewtonSoft.Json telepítése

Alkalmazásunk a NewtonSoft.Json-t fogja használni a JSON objektumok deszerializálására. A csomag telepítéséhez kövesse az alábbi utasításokat.

1. Keresse meg a projektet a Visual Studio Megoldáskezelőjében, és kattintson a jobb gombbal a projektre. Válassza **a NuGet-csomagok kezelése lehetőséget.**
1. Keresse meg és jelölje ki a **Tallózás** lapot.
1. Írja be [NewtonSoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) a keresősávba.

    ![Keresse meg és telepítse NewtonSoft.Json](media/nuget-package-manager.png)

1. Jelölje ki a csomagot, és kattintson a **Telepítés gombra.**
1. Amikor a telepítés befejeződött, zárja be a lapot.

## <a name="create-a-wpf-form-using-xaml"></a>WPF-űrlap létrehozása XAML használatával

Az alkalmazás használatához felhasználói felületre lesz szüksége. Az XAML használatával létrehozunk egy űrlapot, amely lehetővé teszi a felhasználók számára a beviteli és fordítási nyelvek kiválasztását, a lefordítandó szöveg bevitelét és a fordítási kimenet megjelenítését.

Nézzük meg, mit építünk.

![WPF XAML felhasználói felület](media/translator-text-csharp-xaml.png)

A felhasználói felület a következő összetevőket tartalmazza:

| Név | Típus | Leírás |
|------|------|-------------|
| `FromLanguageComboBox` | Combobox | A Microsoft Translator által a szövegfordításhoz támogatott nyelvek listáját jeleníti meg. A felhasználó kiválasztja azt a nyelvet, amelyről a fordítás történik. |
| `ToLanguageComboBox` | Combobox | Ugyanazokat a nyelvek `FromComboBox`listáját jeleníti meg, mint a, de a felhasználó által lefordított nyelv kiválasztására szolgál. |
| `TextToTranslate` | TextBox | Lehetővé teszi, hogy a felhasználó beírja a lefordítandó szöveget. |
| `TranslateButton` | Gomb | Ezzel a gombbal lefordítja a szöveget. |
| `TranslatedTextLabel` | Címke | Megjeleníti a fordítást. |
| `DetectedLanguageLabel` | Címke | Megjeleníti a lefordítandó szöveg észlelt`TextToTranslate`nyelvét ( ). |

> [!NOTE]
> Ezt az űrlapot az XAML forráskóddal hozzuk létre, azonban az űrlapot a Visual Studio szerkesztőjével hozhatja létre.

Adjuk hozzá a kódot a projektünkhöz.

1. A Visual Studio alkalmazásban `MainWindow.xaml`válassza a tabulátot.
1. Másolja ezt a kódot a projektbe, majd a módosítások mentéséhez válassza **a Fájl > A MainWindow.xaml fájlmentése** lehetőséget.
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
Most látnia kell az alkalmazás felhasználói felületének előnézetét a Visual Studióban. Meg kell kinéznie hasonló a fenti képen.

Ez az, kész az űrlapod. Most írjunk egy kódot a szövegfordítás és a Bing helyesírás-ellenőrzés használatához.

> [!NOTE]
> Nyugodtan csípés ezt az űrlapot, vagy hozzon létre saját.

## <a name="create-your-app"></a>Az alkalmazás létrehozása

`MainWindow.xaml.cs`tartalmazza az alkalmazásunkat vezérlő kódot. A következő néhány szakaszban kódot adunk hozzá a legördülő menük feltöltéséhez, és megadunk egy maroknyi API-t, amelyet a Translator Text és a Bing Spell Check tesz elérhetővé.

* Amikor a program `MainWindow` elindul, és példányos, a `Languages` módszer a Translator Text API hívják letölteni és feltölti a nyelvi kiválasztási legördülő. Ez minden munkamenet elején egyszer fordul elő.
* Amikor a **Fordítás** gombra kattint, a felhasználó nyelvi kiválasztása és szövege beolvasásra kerül, helyesírás-ellenőrzést végez a bemeneten, és a fordítás és az észlelt nyelv jelenik meg a felhasználó számára.
  * A `Translate` Translator Text API metódusának neve `TextToTranslate`a szöveg fordítására szolgál. Ez a hívás `to` `from` a legördülő menük használatával kiválasztott és nyelveket is tartalmazza.
  * A `Detect` Translator Text API metódusa a program `TextToTranslate`nyelvének meghatározására szolgál.
  * A Bing helyesírás-ellenőrzés `TextToTranslate` az elírások érvényesítésére és módosítására szolgál.

Az összes projektünk az `MainWindow : Window` osztályba van ágyazva. Kezdjük azzal, hogy kódot ad hozzá az előfizetési kulcs beállításához, deklarálja a translator text és a Bing helyesírás-ellenőrzés végpontjait, és inicializálja az alkalmazást.

1. A Visual Studio alkalmazásban `MainWindow.xaml.cs`válassza a tabulátot.
1. Cserélje le az `using` előre kitöltött állításokat a következőkre.  
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
1. Keresse `MainWindow : Window` meg az osztályt, és cserélje le ezzel a kóddal:
   ```csharp
   {
       // This sample uses the Cognitive Services subscription key for all services. To learn more about
       // authentication options, see: https://docs.microsoft.com/azure/cognitive-services/authentication.
       const string COGNITIVE_SERVICES_KEY = "YOUR_COG_SERVICES_KEY";
       // Endpoints for Translator Text and Bing Spell Check
       public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
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
           System.Windows.Application.Current.Shutdown();
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
               System.Windows.Application.Current.Shutdown();
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
1. Adja hozzá a Cognitive Services-előfizetési kulcsot, és mentse.

Ebben a kódblokkban két tagváltozót deklaráltunk, amelyek a fordításhoz rendelkezésre álló nyelvekre vonatkozó információkat tartalmazzák:

| Változó | Típus | Leírás |
|----------|------|-------------|
|`languageCodes` | Karakterláncok tömbje |A nyelvkódokat gyorsítótárazza. A Translator szolgáltatás rövid kódokat használ a nyelvek azonosítására (például: `en` = angol). |
|`languageCodesAndTitles` | Rendezett szótár | A felhasználói felületen megjelenő „felhasználóbarát” neveket képezi le az API által használt rövid kódokra. Az elemeket betűrendbe rendezi, és nem veszi figyelembe a nagy- és kisbetűket. |

Ezután a `MainWindow` konstruktoron belül `HandleExceptions`hibakezelést adtunk hozzá a segítségével. Ez a hibakezelés biztosítja, hogy a riasztás biztosított, ha egy kivétel nem kezeli. Ezután egy ellenőrzés fut, hogy ellenőrizze az előfizetési kulcs biztosított 32 karakter hosszúságú. Hiba történik, ha a kulcs 32 karakternél kisebb/nagyobb.

Ha vannak olyan kulcsok, amelyek legalább `InitializeComponent()` a megfelelő hosszúságúak, a hívás legördülő funkciója a felhasználói felület et a fő alkalmazásablak XAML-leírásának megkeresésével, betöltésével és példányosításával indítja el.

Végül hozzáadtunk egy kódot a hívási módszerekhez, hogy leszerezzük a nyelveket a fordításhoz, és feltöltsük az alkalmazás felhasználói felületének legördülő menüit. Ne aggódjon, hamarosan rátérünk a hívások mögötti kódra.

## <a name="get-supported-languages"></a>Támogatott nyelvek lekérése

A Translator Text API jelenleg több mint 60 nyelvet támogat. Mivel az új nyelvi támogatás idővel hozzáadódik, azt javasoljuk, hogy a Translator Text által elérhető nyelvek erőforrást hívja meg ahelyett, hogy az alkalmazás nyelvi listáját kódolja.

Ebben a szakaszban létrehozunk `GET` egy kérelmet a Nyelvek erőforráshoz, amely megadja, hogy szeretnénk egy listát a fordításhoz rendelkezésre álló nyelvekről.

> [!NOTE]
> A Nyelvek erőforrás lehetővé teszi a nyelvi támogatás szűrését a következő lekérdezési paraméterekkel: átírás, szótár és fordítás. További információ: [API reference](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

Mielőtt tovább mennénk, vessünk egy pillantást a nyelvek forráshívás mintakimenetére:

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

Ebből a kimenetből kinyerhetjük `name` a nyelvi kódot és egy adott nyelv nyelvét. Az alkalmazás unk newtonsoft.json segítségével deszerializálja a JSON objektumot ([`JsonConvert.DeserializeObject`](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm)).

Ha ott folytatjuk, ahol az utolsó szakaszban abbahagytuk, adjunk hozzá egy módszert a támogatott nyelvek alkalmazásunkhoz való bekerüléséhez.

1. A Visual Studio alkalmazásban `MainWindow.xaml.cs`nyissa meg a lapját.
2. Adja hozzá ezt a kódot a projekthez:
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

A `GetLanguagesForTranslate()` metódus http GET-kérelmet `scope=translation` hoz létre, és a lekérdezési karakterlánc paraméter használatával a kérelem hatókörét a támogatott nyelvekre korlátozza a fordításhoz. Az `en` értékű `Accept-Language` fejléc hozzáadásával a támogatott nyelvek neve angolul jelenik meg.

A JSON-válasz elemzésre kerül, és szótárrá konvertálódik. Ezután a nyelvi kódok `languageCodes` hozzáadódnak a tag változóhoz. A nyelvi kódokat és a nyelvek felhasználóbarát nevét tartalmazó kulcs-érték-párokból az alkalmazás egy hurkot hoz létre, majd hozzáadja azokat a `languageCodesAndTitles` tagváltozóhoz. Az űrlap legördülő menüi a rövid neveket jelenítik meg, de a kódok szükségesek a fordítás igényléséhez.

## <a name="populate-language-drop-down-menus"></a>Nyelvi legördülő menük feltöltése

A felhasználói felület az XAML segítségével van definiálva, így nem kell `InitializeComponent()`sokat tennie a hívás mellett. Az egyetlen dolog, amit meg kell tennie, hogy adja hozzá a barátságos nyelv nevét a **Fordítás innen** és a Fordítás a legördülő **menükhöz.** A `PopulateLanguageMenus()` metódus hozzáadja a neveket.

1. A Visual Studio alkalmazásban `MainWindow.xaml.cs`nyissa meg a lapját.
2. Adja hozzá ezt a `GetLanguagesForTranslate()` kódot a projekthez a módszer alatt:
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

Ez a módszer átírja a `languageCodesAndTitles` szótárt, és mindkét menühöz hozzáadja az egyes billentyűket. A menük feltöltése után a nyelvek ről és nyelvekről szóló alapértelmezett beállítás **észlelési,** illetve **angol** nyelvű.

> [!TIP]
> Ha a menüknek nincs alapértelmezett értéke, a felhasználó a forrásnyelv és a célnyelv kiválasztása nélkül nem kattinthat a **Translate** (Fordítás) gombra. Ha vannak alapértelmezett beállítások, a felhasználónak nem kell ezzel törődnie.

Most, `MainWindow` hogy inicializálták, és a felhasználói felület létre, ez a kód nem fog futni, amíg a **Fordítás** gombra kattintott.

## <a name="detect-language-of-source-text"></a>A forrásszöveg nyelvének észlelése

Most fogunk létrehozni módszer felismerni a nyelvet a forrás szöveg (szöveg beírt a szöveg terület) a Translator Text API.Now we're going to create method to detect the language of the source text (text entered into our text area) using the Translator Text API. A kérés által visszaadott értéket később használjuk fel a fordítási kérelemben.

1. A Visual Studio alkalmazásban `MainWindow.xaml.cs`nyissa meg a lapját.
2. Adja hozzá ezt a `PopulateLanguageMenus()` kódot a projekthez a módszer alatt:
   ```csharp
   // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
   private string DetectLanguage(string text)
   {
       string detectUri = string.Format(TEXT_TRANSLATION_API_ENDPOINT ,"detect");

       // Create request to Detect languages with Translator Text
       HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(detectUri);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
       detectLanguageWebRequest.ContentType = "application/json; charset=utf-8";
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

Ez a módszer `POST` http-kérelmet hoz létre az erőforrás észlelése számára. Egyetlen argumentumot `text`vesz igénybe, amely a kérelem törzseként kerül átadásra. Később, amikor elkészítjük a fordítási kérelmet, a felhasználói felületünkre beírt szöveg et továbbítjuk erre a módszerre a nyelvfelismeréshez.

Emellett ez a módszer kiértékeli a válasz megbízhatósági pontszámát. Ha a pontszám `0.5`nagyobb, mint a , akkor az észlelt nyelv megjelenik a felhasználói felületen.

## <a name="spell-check-the-source-text"></a>Helyesírás-ellenőrzés a forrásszövegben

Most egy módszert fogunk létrehozni a forrásszöveg helyesírásának ellenőrzésére a Bing helyesírás-ellenőrző API-val. A helyesírás-ellenőrzés biztosítja, hogy pontos fordításokat kapjunk a Translator Text API-ból. A forrásszöveg bármilyen javítása a fordítási kérelemben jelenik meg, amikor a **Fordítás** gombra kattint.

1. A Visual Studio alkalmazásban `MainWindow.xaml.cs`nyissa meg a lapját.
2. Adja hozzá ezt a `DetectLanguage()` kódot a projekthez a módszer alatt:

```csharp
// ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
private string CorrectSpelling(string text)
{
    string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

    // Create a request to Bing Spell Check API
    HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
    spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
    spellCheckWebRequest.Method = "POST";
    spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

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

## <a name="translate-text-on-click"></a>Szöveg fordítása kattintásra

Az utolsó dolog, amit meg kell tennünk, hogy hozzon létre egy módszert, amely hivatkoznak, amikor a **Fordítás** gombra a felhasználói felület kattintott.

1. A Visual Studio alkalmazásban `MainWindow.xaml.cs`nyissa meg a lapját.
1. Adja hozzá ezt a `CorrectSpelling()` kódot a projekthez a módszer alatt, és mentse el:  
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
           request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
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

Az első lépés az, hogy a "from" és a "to" nyelvek, és a szöveg a felhasználó lépett be az űrlapon. Ha a forrásnyelv **észlelése** `DetectLanguage()` beállításra van állítva, a program a forrásszöveg nyelvének meghatározására kéri. Előfordulhat, hogy a szöveg olyan nyelven van, amelyet a Translator API nem támogat. Ebben az esetben jelenítsen meg egy üzenetet, amely tájékoztatja a felhasználót, és a szöveg fordítása nélkül térjen vissza.

Amennyiben a forrásnyelv az angol (akár megadja ezt, akár a rendszer maga ismeri fel), a `CorrectSpelling()` használatával ellenőrizheti, valamint szükség esetén javíthatja a helyesírást. A javított szöveg visszakerül a szövegterületre, így a felhasználó látja, hogy a javítás elkészült.

A szöveg fordításához kód ismerősnek kell lennie: létre kell hoznia az URI-t, kérelmet kell létrehoznia, el kell küldenie, és elemeznie kell a választ. A JSON-tömb több objektumot is tartalmazhat fordításhoz, azonban az alkalmazásunkhoz csak egy szükséges.

A sikeres kérés `TranslatedTextLabel.Content` után a `translation`helyére a , amely frissíti a felhasználói felületet a lefordított szöveg megjelenítéséhez.

## <a name="run-your-wpf-app"></a>A WPF alkalmazás futtatása

Ez az, van egy működő fordító alkalmazás segítségével épített WPF. Az alkalmazás futtatásához kattintson a **Start** gombra a Visual Studio alkalmazásban.

## <a name="source-code"></a>Forráskód

A projekt forráskódja elérhető a GitHubon.

* [A forráskód felfedezése](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp-Tutorial)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Microsoft Translator Text API-referencia](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
