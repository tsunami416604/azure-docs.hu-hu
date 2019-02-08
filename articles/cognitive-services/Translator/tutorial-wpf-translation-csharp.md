---
title: 'Oktatóanyag: A WPF-alkalmazás létrehozása C# – Translator Text API'
titleSuffix: Azure Cognitive Services
description: Az oktatóanyag bemutatja, hogy a Translator Text API használatával és egy C# nyelven írt WPF-alkalmazással többek között hogyan fordíthat le szövegeket vagy szerezheti be a támogatott nyelvek honosított listáját.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: erhopf
ms.openlocfilehash: e0d09c857a3b90682415252656effba189083d65
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857974"
---
# <a name="tutorial-write-a-wpf-application-for-translator-text-using-c35"></a>Oktatóanyag: A WPF-alkalmazás írása a Translator Text C használatával&#35;

Ebben az oktatóanyagban egy interaktív szövegfordító eszközt fog létrehozni a Translator Text API (V3) használatával, ami az Azure-ban található Microsoft Cognitive Services része. A következőket fogja megtanulni:

> [!div class="checklist"]
> * A szolgáltatás által támogatott nyelvek listájának lekérése.
> * Felhasználó által megadott szöveg fordítása az egyik nyelvről a másikra.

Az alkalmazás további két Microsoft Cognitive Services-szolgáltatással működik együtt.

|||
|-|-|
|[Szövegelemzés](https://azure.microsoft.com/services/cognitive-services/text-analytics/)|Választható funkció, automatikusan megállapítja a lefordítandó szöveg forrásnyelvét.|
|[Bing helyesírás-ellenőrzés](https://azure.microsoft.com/services/cognitive-services/spell-check/)|Kijavítja az angol nyelvű forrásszövegek helyesírási hibáit, hogy a fordítás pontosabb legyen.

![[Az oktatóprogram futás közben]](media/translator-text-csharp-session.png)

## <a name="prerequisites"></a>Előfeltételek

A kód Windows rendszeren történő futtatásához [Visual Studio 2017](https://www.visualstudio.com/downloads/) szükséges. (Az ingyenes Community Edition is elegendő.)

Ezenkívül rendelkeznie kell előfizetési kulcsokkal a programban használt három Azure-szolgáltatáshoz. A Translator Text szolgáltatáshoz szükséges kulcsot az Azure-irányítópultról szerezheti be. Az ingyenes tarifacsomag keretében havonta legfeljebb kétmillió karaktert ingyenesen fordíthat le.

A szövegelemzésből és a Bing helyesírás-ellenőrzési szolgáltatásából is elérhető ingyenes próbaverzió, amelyekre a [Cognitive Services kipróbálási](https://azure.microsoft.com/try/cognitive-services/) oldalán regisztrálhat. Mindkét szolgáltatáshoz létrehozhat előfizetést az Azure-irányítópulton. A szövegelemzési szolgáltatásnak ingyenes szintje is van.

Az oktatóanyag forráskódját az alábbiakban tekintheti meg. Az előfizetési kulcsokat be kell másolnia a forráskódba a megfelelő (pl.: `MainWindow.xaml.cs`) változóhoz a `TEXT_TRANSLATION_API_SUBSCRIPTION_KEY` fájlban.

> [!IMPORTANT]
> A szövegelemzési szolgáltatás több régióban is elérhető. Az oktatóanyag forráskódjának URI-ja a `westus` régióra vonatkozik, amelyet az ingyenes próbaverziókhoz szokás használni. Ha egy másik régióban rendelkezik előfizetéssel, annak megfelelően módosítsa az URI-t.

## <a name="source-code"></a>Forráskód

Ez a Microsoft Translator Text API forráskódja. Az alkalmazás futtatásához másolja a forráskódot a megfelelő fájlba a Visual Studio egy új WPF-projektjében.

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

Ez a fájlhoz tartozó kód, amely lehetővé teszi az alkalmazás működését.

```csharp
using System;
using System.Windows;
using System.Net;
using System.Net.Http;
using System.IO;
using System.Collections.Generic;
using System.Linq;
using System.Text;

// NOTE: Add assembly references to System.Runtime.Serialization, System.Web, and System.Web.Extensions.

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace MSTranslatorTextDemo
{
    /// <summary>
    /// This WPF application demonstrates the use of the Microsoft Translator Text API to translate a brief text string from
    /// one language to another. The languages are selected from a drop-down menu. The text of the translation is displayed.
    /// The source language may optionally be automatically detected. English text is spell-checked.
    /// </summary>
    public partial class MainWindow : Window
    {
        // Translator text subscription key from Microsoft Azure dashboard
        const string TEXT_TRANSLATION_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
        const string TEXT_ANALYTICS_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
        const string BING_SPELL_CHECK_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";

        public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
        const string TEXT_ANALYTICS_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/";
        const string BING_SPELL_CHECK_API_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

        private string[] languageCodes;     // array of language codes

        // Dictionary to map language code from friendly name (sorted case-insensitively on language name)
        private SortedDictionary<string, string> languageCodesAndTitles =
            new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

        public MainWindow()
        {
            // at least show an error dialog if there's an unexpected error
            AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

            if (TEXT_TRANSLATION_API_SUBSCRIPTION_KEY.Length != 32
                || TEXT_ANALYTICS_API_SUBSCRIPTION_KEY.Length != 32
                || BING_SPELL_CHECK_API_SUBSCRIPTION_KEY.Length != 32)
            {
                MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                    "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                    "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
                System.Windows.Application.Current.Shutdown();
            }
            else
            {
                InitializeComponent();          // start the GUI
                GetLanguagesForTranslate();     // get codes and friendly names of languages that can be translated
                PopulateLanguageMenus();        // fill the drop-down language lists
            }
        }

        // Global exception handler to display error message and exit
        private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
        {
            Exception e = (Exception)args.ExceptionObject;
            MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
            System.Windows.Application.Current.Shutdown();
        }

        // ***** POPULATE LANGUAGE MENUS
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

            // set default languages
            FromLanguageComboBox.SelectedItem = "Detect";
            ToLanguageComboBox.SelectedItem = "English";
        }

        // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
        private string DetectLanguage(string text)
        {
            string uri = TEXT_ANALYTICS_API_ENDPOINT + "languages?numberOfLanguagesToDetect=1";

            // create request to Text Analytics API
            HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(uri);
            detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_ANALYTICS_API_SUBSCRIPTION_KEY);
            detectLanguageWebRequest.Method = "POST";

            // create and send body of request
            var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
            string jsonText = serializer.Serialize(text);

            string body = "{ \"documents\": [ { \"id\": \"0\", \"text\": " + jsonText + "} ] }";
            byte[] data = Encoding.UTF8.GetBytes(body);
            detectLanguageWebRequest.ContentLength = data.Length;

            using (var requestStream = detectLanguageWebRequest.GetRequestStream())
                requestStream.Write(data, 0, data.Length);

            HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

            // read and parse JSON response
            var responseStream = response.GetResponseStream();
            var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
            dynamic jsonResponse = serializer.DeserializeObject(jsonString);

            // fish out the detected language code
            var languageInfo = jsonResponse["documents"][0]["detectedLanguages"][0];
            if (languageInfo["score"] > (decimal)0.5)
                return languageInfo["iso6391Name"];
            else
                return "";
        }

        // ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
        private string CorrectSpelling(string text)
        {
            string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

            // create request to Bing Spell Check API
            HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
            spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", BING_SPELL_CHECK_API_SUBSCRIPTION_KEY);
            spellCheckWebRequest.Method = "POST";
            spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

            // create and send body of request
            string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
            byte[] data = Encoding.UTF8.GetBytes(body);
            spellCheckWebRequest.ContentLength = data.Length;
            using (var requestStream = spellCheckWebRequest.GetRequestStream())
                requestStream.Write(data, 0, data.Length);
            HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

            // read and parse JSON response and get spelling corrections
            var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
            var responseStream = response.GetResponseStream();
            var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
            dynamic jsonResponse = serializer.DeserializeObject(jsonString);
            var flaggedTokens = jsonResponse["flaggedTokens"];

            // construct sorted dictionary of corrections in reverse order in string (right to left)
            // so that making a correction can't affect later indexes
            var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
            for (int i = 0; i < flaggedTokens.Length; i++)
            {
                var correction = flaggedTokens[i];
                var suggestion = correction["suggestions"][0];  // consider only first suggestion
                if (suggestion["score"] > (decimal)0.7)         // take it only if highly confident
                    corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                        { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
            }

            // apply the corrections in order from right to left
            foreach (int i in corrections.Keys)
            {
                var oldtext = corrections[i][0];
                var newtext = corrections[i][1];

                // apply capitalization from original text to correction - all caps or initial caps
                if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
                else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

                text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
            }

            return text;
        }

        // ***** GET TRANSLATABLE LANGUAGE CODES
        private void GetLanguagesForTranslate()
        {
            // send request to get supported language codes
            string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
            WebRequest WebRequest = WebRequest.Create(uri);
            WebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
            WebRequest.Headers.Add("Accept-Language", "en");
            WebResponse response = null;
            // read and parse the JSON response
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
                request.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
                request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
                var translations = result[0]["translations"];
                var translation = translations[0]["text"];

                // Update the translation field
                TranslatedTextLabel.Content = translation;
            }
        }
    }
}
```

### <a name="mainwindowxaml"></a>MainWindow.xaml

Ez a fájl határozza meg az alkalmazás felhasználói felületét, egy WPF-űrlapot. Amennyiben saját űrlapot szeretne tervezni, erre a XAML-fájlra nincsen szüksége.

```xml
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
        <Label x:Name="TranslatedTextLabel" Content="Translation appears here" HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
    </Grid>
</Window>
```

## <a name="service-endpoints"></a>Szolgáltatásvégpontok

A Microsoft Translator szolgáltatás több végponttal rendelkezik, amelyek különböző fordítási funkciókat biztosítanak. Ebben az oktatóanyagban a következőket fogja használni:

|||
|-|-|
|`Languages`|A Translator Text API egyéb műveletei által aktuálisan támogatott nyelvek listáját adja vissza.|
|`Translate`|A forrásszöveg, a forrás nyelvkódja, valamint a cél nyelvkódjának megadása után a forrásszöveg fordítását adja vissza a célnyelven.|

## <a name="the-translation-app"></a>A fordítóalkalmazás

A fordítóalkalmazás felhasználói felülete a Windows Presentation Foundation (WPF) használatával lett létrehozva. Új WPF-projekt létrehozásához a Visual Studióban a következőket kell tennie.

* A **File** (Fájl) menüben válassza a **New > Project** (Új > Projekt) lehetőséget.
* A New Project (Új projekt) ablakban nyissa meg az **Installed > Templates > Visual C#** (Telepítve > Sablonok > Visual C#) elemet. A párbeszédpanel közepén megjelenik az elérhető projektsablonok listája.
* Győződjön meg arról, hogy a projektsablonok listája feletti legördülő menüben a **.NET Framework 4.5.2** elem van kiválasztva.
* A projektsablonok listájában kattintson a **WPF App (.NET Framework)** (WPF-alkalmazás (.NET-keretrendszer)) elemre.
* A párbeszédablak alján található mezőkben adja meg az új projekt nevét, valamint hogy melyik megoldás tartalmazza a projektet.
* Az új projekt és a megoldás létrehozásához kattintson az **OK** gombra.

![[Új WPF-alkalmazás létrehozása a Visual Studióban]](media/translator-text-csharp-new-project.png)

Adjon hivatkozásokat a projektje következő .NET-keretrendszerbeli szerelvényeihez.

* System.Runtime.Serialization
* System.Web
* System.Web.Extensions

Ezenkívül telepítse a `Newtonsoft.Json` NuGet-csomagot a projektben.

A Megoldáskezelőben keresse meg a `MainWindow.xaml` fájlt, és nyissa meg. A fájl kezdetben üres lesz. Így kell kinéznie a kész felhasználói felületnek, a vezérlők neve kék színnel van feltüntetve. A felhasználói felület vezérlőinek adja ugyanezeket a neveket, mivel a kódban is szerepelnek.

![[A Visual Studio tervezői fő ablakának jegyzetekkel ellátott képe]](media/translator-text-csharp-xaml.png)

> [!NOTE]
> Az oktatóanyag forráskódja tartalmazza az űrlap XAML-forrását. Ezt beillesztheti a projektbe, ha nem szeretne saját űrlapot létrehozni a Visual Studióban.

* `FromLanguageComboBox` *(Kombinált lista)* – Megjeleníti a Microsoft Translator által a fordításhoz támogatott nyelvek listáját. A felhasználó kiválasztja azt a nyelvet, amelyről a fordítás történik.
* `ToLanguageComboBox` *(Kombinált lista)* – Ugyanazt a nyelvlistát jeleníti meg, mint a `FromComboBox`, de ezúttal a célnyelvet kell kiválasztani.
* `TextToTranslate` *(Szövegmező)* – A felhasználó ide írja a lefordítandó szöveget.
* `TranslateButton` *(Gomb)* – A felhasználó erre a gombra kattint (vagy lenyomja az Enter billentyűt) a szöveg lefordításához.
* `TranslatedTextLabel` *(Címke)* – A felhasználó által beírt szöveg fordítása itt jelenik meg.

Amennyiben saját űrlapot készít, nem szükséges *pontosan* ugyanolyat létrehoznia, mint amilyen a példában szerepel. Arról azonban győződjön meg, hogy a nyelvválasztó legördülő menü kellően széles-e, hogy ne vágja le a nyelv nevének végét.

## <a name="the-mainwindow-class"></a>A MainWindow osztály

A `MainWindow.xaml.cs` fájlba kerül a kód, amely biztosítja a program működését. Két alkalommal van rá szükség:

* A program indításakor és a `MainWindow` inicializálásakor lekéri a nyelvek listáját a Translator és az API-k segítségével, és feltölti adatokkal a legördülő menüket. Ezt a feladatot minden munkamenet elején elvégzi az alkalmazás.

* Amikor a felhasználó a **Translate** (Fordítás) gombra kattint, az alkalmazás lekéri a választott nyelveket és a beírt szöveget, majd meghívja a `Translate` API-t, hogy elvégezze a fordítást. Egyéb függvények hívására is szükség lehet a szöveg nyelvének megállapításához, illetve a helyesírási hibák javításához a fordítás elvégzése előtt.

Tekintse meg az osztály elejét:

```csharp
public partial class MainWindow : Window
{
    // Translator text subscription key from Microsoft Azure dashboard
    const string TEXT_TRANSLATION_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
    const string TEXT_ANALYTICS_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
    const string BING_SPELL_CHECK_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";

    public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
    const string TEXT_ANALYTICS_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/";
    const string BING_SPELL_CHECK_API_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

    private string[] languageCodes;     // array of language codes

    // Dictionary to map language code from friendly name (sorted case-insensitively on language name)
    private SortedDictionary<string, string> languageCodesAndTitles =
        new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

    public MainWindow()
    {
        // at least show an error dialog if there's an unexpected error
        AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

        if (TEXT_TRANSLATION_API_SUBSCRIPTION_KEY.Length != 32
            || TEXT_ANALYTICS_API_SUBSCRIPTION_KEY.Length != 32
            || BING_SPELL_CHECK_API_SUBSCRIPTION_KEY.Length != 32)
        {
            MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
            System.Windows.Application.Current.Shutdown();
        }
        else
        {
            InitializeComponent();          // start the GUI
            GetLanguagesForTranslate();     // get codes and friendly names of languages that can be translated
            PopulateLanguageMenus();        // fill the drop-down language lists
        }
    }
// more to come
}
```

Kettő, itt meghatározott tagváltozó tartalmaz a nyelvekre vonatkozó információt:

|||
|-|-|
|`languageCodes`<br>sztringek tömbje|A nyelvkódokat gyorsítótárazza. A Translator szolgáltatás rövid kódokat használ a nyelvek azonosítására (például: `en` = angol).|
|`languageCodesAndTitles`<br>SortedDictionary|A felhasználói felületen megjelenő „felhasználóbarát” neveket képezi le az API által használt rövid kódokra. Az elemeket betűrendbe rendezi, és nem veszi figyelembe a nagy- és kisbetűket.|

Az alkalmazás által végrehajtott első kód a `MainWindow` konstruktor. Először is beállítja, hogy a `HandleExceptions` metódus legyen az általános hibakezelő. Így legalább egy hibariasztást kap, ha egy kivételt nem kezel az alkalmazás.

Ezután meggyőződik arról, hogy az API-előfizetési kulcsok pontosan 32 karakter hosszúságúak. Ha nem, akkor *valaki* nem illesztette be az API-kulcsait. Ebben az esetben egy hibaüzenet jelenik meg, és a folyamat megszakad. (A sikeres teszt nem feltétlenül jelenti azt, hogy a kulcsok érvényesek is.)

Ha a kulcsok megfelelő hosszúságúak, az `InitializeComponent()` hívása elindítja a felhasználói felületet: megkeresi, betölti és létrehozza a fő alkalmazásablak XAML-leírásának példányát.

Végül beállítja a nyelvek legördülő menüit. Ez három különálló metódushívást igényel, amelyeket a következő szakaszok mutatnak be.

## <a name="get-supported-languages"></a>Támogatott nyelvek lekérése

A Microsoft Translator szolgáltatás e sorok írásakor 61 nyelvet támogat, de a számuk a későbbiekben tovább nőhet. Ezért nem érdemes a programban rögzíteni a támogatott nyelveket. Érdemesebb lekérdezni a Translator szolgáltatástól, hogy mely nyelveket támogatja. Minden támogatott nyelv lefordítható a többi támogatott nyelvre.

A támogatott nyelvek listáját a `Languages` API hívásával kérheti le.

A `Languages` API egy választható GET-lekérdezési paramétert (*scope*) is használ. A *scope* három értéket vehet fel: `translation`, `transliteration` és `dictionary`. A kód a `translation` értéket használja.

A `Languages` API ezenkívül egy választható HTTP-fejlécet (`Accept-Language`) is használ. Ennek a fejlécnek az értéke határozza meg, hogy az alkalmazás milyen nyelven adja vissza a támogatott nyelvek nevét. Az érték egy megfelelő formátumú BCP 47 nyelvi címkének kell lennie. A kód az `en` érték megadásakor angolul jeleníti meg a nyelvek nevét.

A `Languages` API egy JSON-választ ad vissza, amely a következőképpen néz ki.

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
    },
...
}
```

Mivel a nyelvi kódok (például: `af`) és a nyelvek neve (például: `Afrikaans`) kinyerhetők, a kód a [JsonConvert.DeserializeObject](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm) NewtonSoft.Json metódust használja.

Mindezen háttértudás birtokában hozza létre a következő, a nyelvi kódok és azok nevének lekérésére szolgáló metódust.

```csharp
private void GetLanguagesForTranslate()
{
    // send request to get supported language codes
    string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
    WebRequest WebRequest = WebRequest.Create(uri);
    WebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
    WebRequest.Headers.Add("Accept-Language", "en");
    WebResponse response = null;
    // read and parse the JSON response
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
```

A `GetLanguagesForTranslate()` először létrehozza a HTTP-kérést. A `scope=translation` lekérdezésisztring-paraméter kizárólag a szöveges fordításhoz használható, támogatott nyelveket kéri le. Az alkalmazás hozzáadja a Text Translation API előfizetési kulcsát a kérésfejlécekhez. Az `en` értékű `Accept-Language` fejléc hozzáadásával a támogatott nyelvek neve angolul jelenik meg.

A kérés teljesítése után az alkalmazás elemzi a JSON-választ, és szótárrá konvertálja, majd a nyelvi kódokat hozzáadja a `languageCodes` tagváltozóhoz. A nyelvi kódokat és a nyelvek felhasználóbarát nevét tartalmazó kulcs-érték-párokból az alkalmazás egy hurkot hoz létre, majd hozzáadja azokat a `languageCodesAndTitles` tagváltozóhoz. (Az űrlapon megjelenő legördülő menükben a felhasználóbarát nevek szerepelnek, de szükség van a kódokra a fordítás kéréséhez.)

## <a name="populate-the-language-menus"></a>A nyelvi menük feltöltése adatokkal

A felhasználói felületet a XAML határozza meg, ezért az `InitializeComponent()` hívásán kívül nincs sok tennivalója. Mindössze hozzá kell adnia a nyelvek felhasználóbarát nevét a **Translate from** (Forrásnyelv) és a **Translate to** (Célnyelv) legördülő menühöz. Ezt a `PopulateLanguageMenus()` alatt teheti meg.

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

    // set default languages
    FromLanguageComboBox.SelectedItem = "Detect";
    ToLanguageComboBox.SelectedItem = "English";
}
```

A menü feltöltéséhez egyszerűen iterálni kell a `languageCodesAndTitles` szótár tartalmát, hozzáadva minden kulcsot (a „felhasználóbarát” neveket) mindkét menühöz. A menük adatokkal való feltöltése után az alapértelmezett forrás- és célnyelvbeállítások a következők lesznek: **Detect** (észlelés; a nyelv automatikus felismerése), illetve **English** (angol).

> [!TIP]
> Ha a menüknek nincs alapértelmezett értéke, a felhasználó a forrásnyelv és a célnyelv kiválasztása nélkül nem kattinthat a **Translate** (Fordítás) gombra. Ha vannak alapértelmezett beállítások, a felhasználónak nem kell ezzel törődnie.

A `MainWindow` inicializálása és a felhasználói felület létrehozása után a kód csak arra vár, hogy a felhasználó a **Translate** (Fordítás) gombra kattintson.

## <a name="perform-translation"></a>A fordítás végrehajtása

Amikor a felhasználó a **Translate** (Fordítás) gombra kattint, a WPF meghívja az itt látható `TranslateButton_Click()` eseménykezelőt.

```csharp
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
        request.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
        request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();

        var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
        var translations = result[0]["translations"];
        var translation = translations[0]["text"];

        // Update the translation field
        TranslatedTextLabel.Content = translation;
    }
}
```

Az első lépésben le kell kérni az űrlapról a forrás- és a célnyelvet, valamint a felhasználó által beírt szöveget.

Amennyiben a forrásnyelv esetén a **Detect** (Észlelés) beállítás van megadva, a `DetectLanguage()` meghívásával állapítható meg a szöveg nyelve. Előfordulhat, hogy a szöveg olyan nyelven íródott, amelyet a Translator API-k nem támogatnak (jóval több nyelv felismerése lehetséges, mint amennyi között a fordítás is elérhető), vagy hogy a Text Analytics API nem képes felismerni azt. Erről az alkalmazás egy üzenetben tájékoztatja a felhasználót, és nem készül el a fordítás.

Amennyiben a forrásnyelv az angol (akár megadja ezt, akár a rendszer maga ismeri fel), a `CorrectSpelling()` használatával ellenőrizheti, valamint szükség esetén javíthatja a helyesírást. A kijavított szöveg visszakerül a beviteli mezőbe, hogy a felhasználó tudja, javításra volt szükség. (Ha a felhasználó kötőjellel kezdi a fordítandó szöveget, letilthatja a helyesírás-ellenőrzést.)

Ha a felhasználó nem írt be szöveget, vagy ha a forrás- és a célnyelv megegyezik, nincs szükség fordításra, és a kérés figyelmen kívül hagyható.

A fordítási kérés végrehajtására szolgáló kód ismerős lehet: létrehozza az URI-t, létrehozza a kérést, elküldi, majd elemzi a választ. A szöveg megjelenítéséhez küldje el azt a `TranslatedTextLabel` vezérlőnek.

Ezután továbbítsa a szöveget a `Translate` API számára egy POST-kérés törzsében, egy szerializált JSON-tömb részeként. A JSON-tömb több lefordítható szövegrészletet is tartalmazhatna, de ebben az esetben elég lesz egy is.

Az `X-ClientTraceId` nevű HTTP-fejléc használata nem kötelező. Az értéknek egy GUID-nak kell lennie. Az ügyfél által biztosított nyomkövetési azonosító sokat segíthet a kérések nyomon követésében, ha valami nem a várt módon alakul. Ehhez rögzíteni kell az ügyfélben az X-ClientTraceID értékét. Az ügyfél nyomkövetési azonosítója és a kérések dátuma segíthet a Microsoft szakembereinek azonosítani a fellépő problémák okát.

> [!NOTE]
> Mivel az oktatóanyag elsősorban a Microsoft Translator szolgáltatással foglalkozik, ezért a `DetectLanguage()` és a `CorrectSpelling()` metódussal nem foglalkozik részletesen.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Microsoft Translator Text API-referencia](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
