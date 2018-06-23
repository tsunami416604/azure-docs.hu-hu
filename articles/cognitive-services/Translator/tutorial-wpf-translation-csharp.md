---
title: A Microsoft Translator WPF-alkalmazás, a C#, Azure kognitív szolgáltatások írásával |} Microsoft Docs
description: Megtudhatja, hogyan használhatja a fordító szöveg szolgáltatás fordítása szöveg, támogatott nyelvek és további honosított listájának beolvasása.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.devlang: csharp
ms.topic: article
ms.date: 10/25/2017
ms.author: v-jansko
ms.openlocfilehash: fb58fd087de09561a0ea930748562e595d3dde1c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347686"
---
# <a name="how-to-write-a-microsoft-translator-wpf-application-in-c"></a>A Microsoft Translator WPF-alkalmazások írásával C#

Ebben az oktatóanyagban azt az API-val Microsoft Translator szöveg (V3), a Microsoft Azure-ban kognitív Services része egy interaktív szöveg fordítóeszköz fogja létrehozni. Megtudhatja, hogyan:

> [!div class="checklist"]
> * Egy listát kér a rövid kódokat a szolgáltatás által támogatott nyelvek
> * Ezek nyelvkódjait megfelelő honosított nyelvű neveinek listáját beolvasása
> * Szerezze be a felhasználó által beírt szöveg lefordított verzióját egyik nyelvről egy másikra

Ez az alkalmazás megtalálható két más Microsoft kognitív a szolgáltatásokkal való integrációt.

|||
|-|-|
|[Szövegelemzések](https://azure.microsoft.com/services/cognitive-services/text-analytics/)|Opcionálisan automatikusan érzékeli a adatforrás nyelvi fordítandó szöveg|
|[Bing helyesírás-ellenőrző](https://azure.microsoft.com/services/cognitive-services/spell-check/)|Az angol forrásszövege korrigálására talál helyesírási hibát, így pontosabb fordítás

![[A program oktatóanyag futtatása]](media/translator-text-csharp-session.png)

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag elvégzéséhez szüksége van a Visual Studio 2017, beleértve a Community Edition kiadását.

A programban három Azure-szolgáltatás is kell előfizetéshez kulcsok. A kulcs a fordító szöveg szolgáltatás lekérheti az Azure-irányítópultjának. Ingyenes tarifacsomag érhető el, amely lehetővé teszi, hogy legfeljebb két millió karakteres díjmentesen lefordítani.

A szöveg elemzés és a Bing helyesírás-ellenőrzés szolgáltatások ajánlat ingyenes próbaverzió, amely akkor regisztrálhatnak az a [kognitív szolgáltatások próbálja](https://azure.microsoft.com/try/cognitive-services/). Előfizetés vagy szolgáltatás az Azure-irányítópultjának használatával is létrehozhat. Szövegelemzések van egy ingyenes szint.

Ez az oktatóanyag forráskódja alatt érhető el. Az előfizetés kulcsok kell másolni a forráskódban a változók `TEXT_TRANSLATION_API_SUBSCRIPTION_KEY`, és így tovább, a `MainWindow.xaml.cs`.

> [!IMPORTANT]
> A szöveg Analytics szolgáltatás több régióba érhető el. Az oktatóanyag forráskód szereplő URI azonosító szerepel a `westus` régióban, amely az a régió használt ingyenes próbaverzió. Ha egy másik régióban előfizetéssel rendelkezik, ennek megfelelően frissülnek ezt az URI.

## <a name="source-code"></a>Forráskód

Ez az a Microsoft Translator szöveg API forráskódja. Futtassa az alkalmazást, másolja a megfelelő fájlt a Visual Studio új WPF projektben a forráskódot.

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

Ez az a háttérkód fájlt, amely az alkalmazás működését.

```cs
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
    /// This WPF application demonstrates the use of the Microsoft Translator Text API to translate a brief text string
    /// one language to another. The langauges are selected from a drop-down menu. The text of the translation is displayed.
    /// The source language may optionally be automatically detected.  English text is spell-checked.
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
            // at least show an error dialog when we get an unexpected error
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

            // read and and parse JSON response
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

Ez a fájl az alkalmazáshoz, a WPF-űrlapból a felhasználói felület határozza meg. Ha szeretné az űrlap saját verziójának kialakításában, nem kell az XAML-kódot.

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

A Microsoft Translator szolgáltatást számos olyan végpontok, többféle fordítási szolgáltatásokat nyújtó rendelkezik. Ebben az oktatóanyagban használjuk azokat a következők:

|||
|-|-|
|`Languages`|Jelenleg más műveleteket a fordító szöveg API által támogatott nyelvek készletet ad vissza.|
|`Translate`|Forrásszövege megadott, egy adatforrás nyelvi kódja, és egy cél nyelvi kódot ad vissza egy forrás fordítás a megadott nyelv.|

## <a name="the-translation-app"></a>A fordítás alkalmazás

A fordító alkalmazás felhasználói felületén beépített Windows megjelenítési alaprendszer (WPF) használatával. Új WPF-projekt létrehozása a Visual Studio a következő lépések végrehajtásával.

* Az a **fájl** menüben válasszon **új > projekt**.
* Nyissa meg az új projekt ablakról **telepített > sablonok > Visual C#**. Az elérhető sablonok listájának közepét párbeszédpanel jelenik meg.
* Győződjön meg arról, hogy **.NET-keretrendszer 4.5.2-es** a legördülő menü sablon projektlista felett van kiválasztva.
* Kattintson a **WPF-alkalmazás (.NET-keretrendszer)** a projekt sablon listában.
* A mezőkkel a párbeszédpanel alján, nevezze el az új projektbe futnak, és a megoldás, amely tartalmazza azt.
* Kattintson a **OK** az új projekt és a megoldás létrehozásához.

![[Új WPF-alkalmazás létrehozása a Visual Studio]](media/translator-text-csharp-new-project.png)

Adja hozzá a következő .NET-keretrendszer szerelvényei mutató hivatkozásokat a projekthez.

* System.Runtime.Serialization
* System.Web
* System.Web.Extensions

Emellett a NuGet-csomag telepítése `Newtonsoft.Json` a projektben.

Most már található a `MainWindow.xaml` fájlt a Megoldáskezelőben, majd nyissa meg. Az érték üres kezdetben. Ez mit a befejezett felhasználói felület kell hasonlítania, megjegyzésekkel ellátott kék vezérlők nevét. Használható a felhasználói felületen, a vezérlők az azonos nevű, mert is megjelennek a kódot.

![[A fő ablak a Visual Studio-tervezőben megjegyzésekkel ellátott nézet]](media/translator-text-csharp-xaml.png)

> [!NOTE]
> Ebben az oktatóanyagban a forráskód ezt az űrlapot XAML forrását tartalmazza. Előfordulhat, hogy illessze be azt a Visual Studio az űrlap létrehozása helyett a projekthez.

* `FromLanguageComboBox` *(Ehhez: ComboBox)*  -Szöveg fordításhoz Microsoft Translator által támogatott nyelvek listáját jeleníti meg. A felhasználó kiválasztja azokat a fordítja le a nyelvét.
* `ToLanguageComboBox` *(Ehhez: ComboBox)*  -Azonos megjeleníti a nyelveket, `FromComboBox`, de segítségével válassza ki a nyelvet, a felhasználó számára van fordítása.
* `TextToTranslate` *(Szövegmező)*  – A felhasználó megadja a szöveg itt fordítani.
* `TranslateButton` *(Gomb)*  – A felhasználó a gombra kattintva (vagy megnyomja az ENTER billentyűt) lefordítani a szöveget.
* `TranslatedTextLabel` *(Címke)*  – A felhasználó szöveges fordítása itt jelenik meg.

Ha ezt az űrlapot a saját verziójának hajt végre, nem szükséges, hogy *pontosan* , például is. De gondoskodjon arról, hogy a nyelvi legördülő listákat elég nagy a nyelv neveként ki kivágáshoz elkerülése érdekében.

## <a name="the-mainwindow-class"></a>A MainWindow osztály

A háttérkód fájl `MainWindow.xaml.cs` van, ahol a kód kerül, amely lehetővé teszi, hogy a működés programmal. A munkahelyi történik, két időpontokban:

* A program indításakor. Ha `MainWindow` van beolvassuk példányként létrehozott, a nyelvi listát fordító által `GetLanguagesForTranslate` és `GetLanguageNames` API-k és a legördülő menükben való feltöltéséhez. Ez a feladat minden munkamenet elején egyszer történik.

* Amikor a felhasználó kattint a **fordítás** gomb, beolvassuk a felhasználó nyelvi beállításokat és azok a megadott szöveg. Ezután ezt nevezik a `Translate` API-t végrehajtása fordítása. Egyéb feladatok, a szöveg nyelvét és a helyesírás-fordítási előtt is lehet, hogy hívása.

Nézzük, hogyan megkezdjük a osztály:

```cs
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
        // at least show an error dialog when we get an unexpected error
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

Két tag változót deklarál Itt a nyelvek adatainak tárolásához:

|||
|-|-|
|`languageCodes`<br>a karakterlánc tömbje|A nyelvkódjait gyorsítótárazza. A fordító szolgáltatás használja rövid kódok, például a `en` angol nyelvű azonosításához.|
|`languageCodesAndTitles`<br>SortedDictionary|A Maps API-ban használt rövid kódok biztonsági a felhasználói felületen "barátságos" nevét. Folyamatosan rendezett betűrendben eset tekintet nélkül.|

Az alkalmazás által végrehajtott első kód a `MainWindow` konstruktor. Először, beállítjuk a metódus `HandleExceptions` globális hiba kezelőként. Ezzel a módszerrel azt legalább hiba figyelmeztetés megjelenítése kivételen nincs kezelve.

A következő azt ellenőrizze, hogy az API-előfizetés kulcs minden pontosan 32 karakter hosszú. Ha nem, a hiba legvalószínűbb oka az, hogy *valaki* még nem beillesztett az API-kulcsok (tsk). Ebben az esetben azt egy hibaüzenetet jeleníti meg és fedezete. (Ez a vizsgálat nem jelenti azt a kulcsok akkor érvényes, természetesen.)

Ha okunk van, amelyek legalább a megfelelő hosszúságú kulcsok a `InitializeComponent()` hívás lekérdezi a felhasználói felület, működés közbeni megkeresi, betöltése, és létrehozza az alkalmazás fő ablak XAML leírása.

Végül beállítjuk a nyelvi legördülő menükben. Ez a feladat három külön metódusba-hívások szükségesek. Ezek a módszerek a következő szakaszokban részletesen vizsgálni azt.

## <a name="get-supported-languages"></a>Támogatott nyelvek beolvasása

A Microsoft Translator szolgáltatás írásának 61 kiszolgálónyelveit összesen támogatja, és több időről időre adhatók hozzá. Ezért azt rendelkezik az ajánlott nem a merevlemez-kód a program a támogatott nyelveket. Ehelyett kérje meg a fordító szolgáltatás milyen nyelveket támogatja. Bármely támogatott nyelvi fordítható bármely más támogatott nyelven.

Hívja a `Languages` API-t listájának támogatott nyelveket.

A `Languages` API egy választható GET lekérdezési paramétert fogad, *hatókör*. *hatókör* három érték egyike lehet: `translation`, `transliteration`, és `dictionary`. Ezzel az érték `translation`.

A `Languages` API is fogad egy nem kötelező HTTP-fejlécben `Accept-Language`. Az ezt a fejlécet határozza meg a nyelvet, amelyben a rendszer visszairányítja a támogatott nyelvek nevét. Az érték lehet egy megfelelően formázott BCP 47 nyelvi címkét. Ezzel az érték `en` a nyelv nevének beolvasása a angol.

A `Languages` API ad vissza, amely a következőhöz hasonló JSON-választ.

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

Bontsa ki a nyelvkódjait szeretnénk (például `af`) és a nyelv nevének (például `Afrikaans`). A NewtonSoft.Json módszert használjuk [JsonConvert.DeserializeObject](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm) ehhez.

A háttér ismerő létrehozhatunk nyelvkódok és azok nevének beolvasása a következő metódust.

```cs
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

`GetLanguagesForTranslate()` először hoz létre a HTTP-kérelem. A `scope=translation` lekérdezési karakterlánc paraméter határozza meg, hogy szeretnénk szöveg fordításhoz támogatott nyelveket. A kérelem fejlécében a szöveg fordítási API előfizetés kulcs hozzáadása azt. Is hozzáadhat a `Accept-Language` értékű fejléc `en` jelzi azt szeretnénk, hogy a támogatott nyelvek angolul adja vissza.

A kérelem befejezése után azt a JSON-válasz elemzése, és konvertálja szótárba. Jelenleg a nyelvi kód felvenni a `languageCodes` tagváltozó. A Microsoft majd ismétlése a kulcs/érték párok, melyek nyelvkódok és a nyelvek rövid nevét, és adja hozzá ezeket a `languageCodesAndTitles` tagváltozó. (A legördülő menükben a képernyő megjeleníti a rövid nevek, de ellenőriznünk kell, hogy a kód fordítása kéréséhez.)

## <a name="populate-the-language-menus"></a>A nyelvi menü feltöltése

A felhasználói felület a legtöbb XAML-kódban, definiálva van, így nem szükséges műveletek nagy beállítása mellett hívás `InitializeComponent()`. A csak a másik műveletet kell tennie azt, adja hozzá a nyelvek rövid nevét a címzett felé és felől legördülő listákat, az történik `PopulateLanguageMenus()`.

```cs
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

A menük feltöltése keresztül léptetés egyszerű kérdése a `languageCodesAndTitles` szótár, és minden kulcsot, amely "barátságos" név, mindkét menüket. A menük feltöltéséhez, után hivatott az alapértelmezett észlelése (a nyelv automatikus észlelésű) nyelvű érkező vagy oda irányuló angol és.

> [!TIP]
> A menük hivatott nem alapértelmezett kijelölés, ha a felhasználó kattintással **fordítás** nem a a választott vagy nyelv. Az alapértelmezett érték nincs szükség az problémával foglalkozik.

Most `MainWindow` inicializálása megtörtént, a felhasználói felület. Jelenleg nem kérek vezérlő újra amíg a felhasználó kattint a **fordítás** gombra.

## <a name="perform-translation"></a>Végrehajtása fordítása

Amikor a felhasználó kattint **fordítás**, WPF meghívja a `TranslateButton_Click()` eseménykezelő, itt látható.

```cs
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

Itt először beolvassuk a címzett, és nyelven, a szöveg és a felhasználó megadott, a képernyőn.

Ha az adatforrás nyelvi hibakeresés, nevezzük `DetectLanguage()` a szöveg nyelvének meghatározásához. Lehet, hogy a szöveg a fordító API-k nem nyelven támogatása (számos további nyelveket is észlelhető mint fordítható) vagy a szöveg Analytics API nem feltétlenül azt észleli. Ebben az esetben azt üzenetben tájékoztatja a felhasználót és fordítása nélküli visszatéréshez.

Ha a forrás-nyelv az angol (-e megadva, vagy található), azt helyesírását a szöveg `CorrectSpelling()` és alkalmazza a javításokat. A javított szöveg újra üzembe a beviteli mezőt van töltve, így a felhasználó ismer, a javítás történt. (A felhasználó megelőzheti a javításra letiltásához kötőjellel éppen fordítás szöveget.)

Ha a felhasználó még nem adott meg, vagy ha a címzett és nyelvek megegyeznek, nincsenek fordítási szükség. Ebben az esetben azt ne a kérést.

A kód a fordítási kérelem teljesítéséhez Bizonyára ismerős lesz. Azt az URI azonosító létrehozása, hozzon létre egy kérést, elküldi a és a válasz elemzése. A szöveg megjelenítéséhez tároljuk azt a `TranslatedTextLabel` vezérlő.

Azt a szöveget adja át a `Translate` API-nak a szerializált JSON-tömb POST kérelem törzsében. A JSON-tömb lefordítani szöveg több adatra is tartalmazhat, de itt csak magában foglalja az egyik.

A HTTP-fejléc nevű `X-ClientTraceId` nem kötelező megadni. Az érték GUID-nak kell lennie. Az ügyfél által megadott nyomkövetési azonosító akkor hasznos, ha szeretné tárolni kívánt nyomkövetési kérelmek, ha a művelet nem a várt módon működik. Azonban hasznos lehet, X-ClientTraceID értékének rögzíteni kell az ügyfél által. Egy ügyfél-nyomkövetési Azonosítót és a dátum a kérelmek segítheti a Microsoftot, esetlegesen előforduló problémák diagnosztizálásához.

> [!NOTE]
> Ez az oktatóanyag összpontosít a Microsoft Translator szolgáltatás, így azt nem fedik le a `DetectLanguage()` és `CorrectSpelling()` módszerek részletes információkat. A szöveg elemzés és a Bing helyesírás-ellenőrzés szolgáltatások visszajelzést XML helyett JSON, és Szövegelemzések megköveteli, hogy a kérelmet, valamint formázhatók JSON. E jellemzők fiókot használja a módszerek azt korábban már látott legtöbb kód különbségeket.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Microsoft Translator szöveg API-referencia](http://docs.microsofttranslator.com/text-translate.html)
