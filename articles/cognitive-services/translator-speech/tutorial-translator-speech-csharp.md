---
title: A fordító beszéd oktatóanyag (C#) |} Microsoft Docs
titleSuffix: Cognitive Services
description: Ismerje meg, hogy a fordító beszéd szolgáltatás használata a valós idejű szöveg lefordítani.
services: cognitive-services
author: v-jerkin
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-speech
ms.devlang: csharp
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jerkin
ms.openlocfilehash: e82c5c5ccfa6b7de8a9ec111140dad1a40ad44f6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347475"
---
# <a name="tutorial-microsoft-translator-wpf-application-in-c"></a>Oktatóanyag: Microsoft Translator WPF-alkalmazás a C#

Ez az oktatóanyag egy interaktív beszéd fordítási eszközt, amely a Microsoft Translator beszéd fordítási szolgáltatás, a Microsoft Azure-ban kognitív Services része bemutatása. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * A kérelem a szolgáltatás által támogatott nyelvek listája
> * Hang rögzítése, és továbbítja azt a szolgáltatást
> * Kap, és megjeleníti a beszédfelismerés fordításának szövegként
> * A fordítás szóbeli (szöveget beszéddé átalakító) verziója nem kötelezően lejátszása

A Visual Studio megoldás-fájl az alkalmazáshoz [elérhető a Githubon](https://github.com/MicrosoftTranslator/SpeechTranslator).

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban van szüksége a Visual Studio 2017, beleértve a Community Edition kiadását. 

A Visual Studio megoldás is létrehozta az alkalmazást telepítő. Van szüksége a [WiX eszközkészlet](http://wixtoolset.org/) és a [WiX eszközkészlet a Visual Studio bővítmény](https://marketplace.visualstudio.com/items?itemName=RobMensching.WixToolsetVisualStudio2017Extension) ezen funkciók támogatásához.

A fordító beszéd szolgáltatás, amely szerezhet be a Microsoft Azure irányítópult kell egy előfizetés kulcs is. Ingyenes tarifacsomag érhető el, amely lehetővé teszi, hogy fordítása beszéd díjmentesen havonta legfeljebb 10 óra. A réteg is elegendő ehhez az oktatóanyaghoz.

A külső [JSON.Net könyvtár](https://www.newtonsoft.com/json) (a Newtonsoft) is szükség. Ez a szerelvény által automatikusan telepített NuGet Ha egyik csomagok visszaállításának jelölőnégyzet engedélyezve vannak a Visual Studio-beállításokban.

## <a name="trying-the-translation-app"></a>A fordítás app közben

A Microsoft beszéd fordító megoldás megnyitása után (`SpeechTranslator.sln`) a Visual STudio, nyomja le az F5 felépítéséhez és az alkalmazás elindításához.  A program fő ablak.

![[Beszéd fordító fő ablak]](media/speech-translator-main-window.png)

Az első alkalommal történő futtatásakor, majd válassza **Fiókbeállítások** a a **beállítások** menü az itt látható ablak megnyitásához.

![[Beszéd fordító fő ablak]](media/speech-translator-settings-window.png)

A Microsoft Translator beszéd előfizetés kulcs illessze be az ablakot, majd kattintson az **mentéséhez.** A kulcs közötti futtatásakor a rendszer menti.

A fő ablakban válassza ki a hang bemeneti és kimeneti eszközök használata és a kezdő és a nyelvek. Ha azt szeretné, hogy hallhassa hang a fordítás, győződjön meg arról, hogy a **TTS** (szöveget beszéddé átalakító) beállítást. Ha meg szeretné tekinteni a spekulatív részleges fordítása közben beszéd, engedélyezze a **részleges eredmények** lehetőséget.

Végezetül kattintson **Start** fordítási megkezdéséhez. Tegyük fel például, valami van lefordítva, és tekintse meg a felismert szöveg és a fordítás a ablakban jelennek meg. Ha engedélyezte a szöveg-beszéd átalakítás, is hallott a fordítás.

## <a name="obtaining-supported-languages"></a>Támogatott nyelvek beszerzése

A írásának, a Microsoft Translator szolgáltatás több mint öt tucat nyelveket támogatja szöveg fordításhoz. Kisebb számú nyelven elérhető beszéd fordításhoz támogatottak. Az ilyen nyelvű támogatást igényelnek, mindkét (beszédfelismerés) vonatkozó, és a szöveg-beszéd kimeneti, összefoglaló.

Más szóval beszéd fordítását, az adatforrás nyelvi lennie kell egy támogatott vonatkozó. A kimeneti nyelvi szöveg fordításhoz, feltéve, hogy azt szeretné, hogy a szöveg eredményeként a támogatott nyelvek bármelyike lehet. Ha azt szeretné, hogy a beszéd kimeneti, így csak megfelelően felszabadulhatnak olyan szöveg-beszéd átalakítás támogatja.

A Microsoft időnként adhat hozzá új nyelvek támogatását. Emiatt nem kódolnia kell az alkalmazásban támogatott nyelvek ismerete nélkül. Ehelyett a fordító Diktálásfelismerési API biztosít egy nyelvek végpontot, amely lehetővé teszi a támogatott nyelvek futásidőben. Ha szeretné, kap egy vagy több nyelvek listája: 

| | |
|-|-|
|`speech`|Beszéd vonatkozó támogatott nyelveket. Beszéd fordítási forrás nyelv is lehet.|
|`text`|Szöveg fordításhoz támogatott nyelveket. Cél nyelvek beszéd fordítás akkor is, ha a szöveges kimenet szolgál.|
|`tts`|A hangok beszéd összefoglaló támogatott, minden egyes társított nyelv. Cél nyelvek beszéd fordításhoz akkor is, ha a szöveg-beszéd átalakítás szolgál. Lehet, hogy egy adott nyelvre támogatja egynél több hang.|

A nyelvek végpont nem szükséges egy előfizetés kulcs, és a kvóta szemben nem tartoznak bele a használatát. A megadott URI azonosító `https://dev.microsofttranslator.com/languages` és azt az eredményt ad vissza JSON formátumban.

A metódus `UpdateLanguageSettingsAsync()` a `MainWindow.xaml.cs`, látható itt, meghívja a nyelvek végpontot, a támogatott nyelvek listáját. 

```csharp
private async Task UpdateLanguageSettingsAsync()
{
    Uri baseUri = new Uri("https://" + baseUrl);
    string fullUriString = "/Languages?api-version=1.0&scope=text,speech,tts";
    if (MenuItem_Experimental.IsChecked) fullUriString += "&flight=experimental";            
    Uri fullUri = new Uri(baseUri, fullUriString);

    using (HttpClient client = new HttpClient()) //'client' is the var - using statement ensures the dispose method is used even after an exception.
    {
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, fullUri);

        // get language names for current UI culture:
        request.Headers.Add("Accept-Language", CultureInfo.CurrentUICulture.TwoLetterISOLanguageName);

        // add a client-side trace Id. In case of issues, one can contact support and provide this:
        //string traceId = "SpeechTranslator" + Guid.NewGuid().ToString();
        //request.Headers.Add("X-ClientTraceId", traceId);
        //Debug.Print("TraceId: {0}", traceId);

        client.Timeout = TimeSpan.FromMilliseconds(10000);
        HttpResponseMessage response = await client.SendAsync(request); //make the async call to the web using the client var and passing the built up URI
        response.EnsureSuccessStatusCode(); //causes exception if the return is false

        Debug.Print("Request Id returned: {0}", GetRequestId(response));

        //create dictionaries to hold the language specific data
        spokenLanguages = new Dictionary<string, string>();
        fromLanguages = new Dictionary<string, string>();
        textLanguages = new Dictionary<string, string>();
        isLTR = new Dictionary<string, bool>();
        voices = new Dictionary<string, List<TTsDetail>>();

        JObject jResponse = JObject.Parse(await response.Content.ReadAsStringAsync()); //get the json from the async call with the response var created above, parse it and put it in a var called jResponse - JObject is a newton class

        //Gather the set of TTS voices
        foreach (JProperty jTts in jResponse["tts"])
        {
            JObject ttsDetails = (JObject)jTts.Value;

            string code = jTts.Name;
            string language = ttsDetails["language"].ToString();
            string displayName = ttsDetails["displayName"].ToString();
            string gender = ttsDetails["gender"].ToString();

            if (!voices.ContainsKey(language)) //check dictionary for a specific key value
            {
                voices.Add(language, new List<TTsDetail>()); //add to the dictionary the locale key and a ttsDetail object
            }

            voices[language].Add(new TTsDetail() { Code = code, DisplayName = string.Format("{0} ({1})", displayName, gender) });
        }

        // Gather the set of speech translation languages
        foreach (JProperty jSpeech in jResponse["speech"])
        {
            JObject languageDetails = (JObject)jSpeech.Value;
            string code = jSpeech.Name;
            string simplecode = languageDetails["language"].ToString();
            string displayName = languageDetails["name"].ToString();
            spokenLanguages.Add(code, displayName);
            fromLanguages.Add(code,simplecode);
        }

        spokenLanguages = spokenLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        FromLanguage.Items.Clear();
        foreach (var language in spokenLanguages)
        {
            FromLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key});
        }

        // Gather the set of text translation languages
        foreach (JProperty jText in jResponse["text"])
        {
            JObject languageDetails = (JObject)jText.Value;
            string code = jText.Name;
            string displayName = languageDetails["name"].ToString();
            textLanguages.Add(code, displayName);

            string direction = languageDetails["dir"].ToString().ToLowerInvariant();
            bool LTR = true;
            if (direction.ToLowerInvariant() == "rtl") LTR = false;
            isLTR.Add(code, LTR);
        }

        textLanguages = textLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        ToLanguage.Items.Clear();
        foreach (var language in textLanguages)
        {
            ToLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key });
        }

        if (Properties.Settings.Default.FromLanguageIndex >= 0) FromLanguage.SelectedIndex = Properties.Settings.Default.FromLanguageIndex;
        else
        {
            for(int i=0; i < FromLanguage.Items.Count; ++i)
            {
                ComboBoxItem item = (ComboBoxItem)FromLanguage.Items[i];
                if(CultureInfo.CurrentUICulture.Name.Equals((string)item.Tag, StringComparison.OrdinalIgnoreCase))
                {
                    FromLanguage.SelectedIndex = i;
                }
            }
        }
        if (Properties.Settings.Default.ToLanguageIndex >= 0) ToLanguage.SelectedIndex = Properties.Settings.Default.ToLanguageIndex;
        else
        {
            Random rnd = new Random(DateTime.Now.Millisecond);
            ToLanguage.SelectedIndex = (rnd.Next() % textLanguages.Count);
        }
    }
}
```

Ez a módszer először hoz létre egy HTTP-kérést a nyelveket a végponthoz, a kért nyelv három listája (`text`, `speech`, és `tts`).

A nyelvek végpont használja a kérelem `Accept-Languages` fejlécének meghatározni a nyelvet, amelyben a nyelvek nevei jelennek meg. Például a nyelvet, más néven angol beszélő "Német" neve "Német" német és spanyol "Alemán", és nyelvek listáját jeleníti meg ezek a különbségek. A rendszer alapértelmezett nyelvét használható ezt a fejlécet.

Miután a rendszer elküldte a kérelmet, és a belső adatszerkezetek elemzi a JSON-válasz érkezett, a válasz. Ezen szerkezetek a a nyelvet és a nyelvi menü összeállításához használja. 

Elérhető hangok attól függ, hogy a felhasználó által választott való nyelven, mivel nem állítható be a hang menü még. Ehelyett az egyes nyelvekhez tartozó elérhető hangok későbbi használat céljából tárolja. A `ToLanguage_SelectionChanged` (ugyanabban a fájlban forrás) kezelő később frissíti a hang menü meghívásával `UpdateVoiceComboBox()` úgy dönt, amikor a felhasználó a nyelvet. 

Csak a visszatöltött a nyelvi van véletlenszerűen kiválasztott Ha a felhasználó még nem futtatott előtt az alkalmazást. (A menü beállításokat tárolja munkamenetek között.)

## <a name="authenticating-requests"></a>Kérelmek hitelesítése

A Microsoft Translator beszéd szolgáltatásához küld az Azure-előfizetés-kulcs a értékként kell `Ocp-Apim-Subscription-Key` a kapcsolódási kérelem.

## <a name="translation-overview"></a>Fordítási áttekintése

A fordítás API (websocket elemek végpont `wss://dev.microsofttranslator.com/speech/translate`) fogad hang fordítandó monophonic, 16 kHz, 16 bites aláírják WAVE formátum. A szolgáltatás egy vagy több, mind a felismert és lefordított szöveget tartalmazó JSON válaszok adja vissza. Ha egy kérelem szöveg-beszéd átalakítás, hangfájl küld el.

A felhasználó úgy dönt, a hang forrás, a fájl/mikrofon bemeneti menü segítségével. A hang származhat, hangeszközök (például a rendszerhez mikrofon) vagy az egy `.WAV` fájlt.

A metódus `StartListening_Click` nyílik meg, ha a felhasználó a Start gombra kattint. Ebben az eseménykezelőben, meghívja `Connect()` hang küldeni az API-végpont a folyamat megkezdéséhez. A `Connect()` metódus a következő feladatokat hajtja végre:


> [!div class="checklist"]
> * Felhasználói beállítások beolvasása a fő ablakot, és azokat ellenőrzése
> * A hangbemenetet inicializálása és kimenetét
> * Hívása `ConnectAsync()` a többit kezelése

`ConnectAsync()`, viszont kezeli a következő chores:

> [!div class="checklist"]
> * Azure-előfizetés kulccsal fejlécben hitelesítése `Ocp-Apim-Subscription-Key`
> * Létrehozása egy `SpeechClient` példány (található `SpeechClient.cs`) a szolgáltatással való kommunikációra
> * Inicializálása `TextMessageDecoder` és `BinaryMessageDecoder` példányok (lásd: `SpeechResponseDecoder.cs`) kezelik a választ a
> * A hang keresztül küld a `SpeechClient` példány, a fordító beszéd szolgáltatás
> * Fogadására és a fordítás eredmények feldolgozásakor

A feladatai `SpeechClient` kevesebb:

> [!div class="checklist"]
> * A WebSocket-kapcsolat a fordító beszéd szolgáltatás létrehozása
> * Hang adatok küldése és fogadása a szoftvercsatorna keresztül válaszok

## <a name="a-closer-look"></a>Részletes bemutatása

Meg kell tisztább most hogyan az alkalmazás részei működik együtt a fordítási kérelem teljesítéséhez. Vessen egy pillantást néhány kódot, a fontos részek összpontosít.

Íme egy részleges verziója `Connect()` beállítás, amely megjelenik a hang adatfolyamok:

```csharp
private void Connect()
{
    if (this.currentState != UiState.ReadyToConnect) return;

    Stopwatch watch = Stopwatch.StartNew();
    UpdateUiState(UiState.Connecting);

    // Omitted: code to validate UI settings

    string tag = ((ComboBoxItem)Mic.SelectedItem).Tag as string;
    string audioFileInputPath = null;
    if (tag == "File")
    {
        audioFileInputPath = this.AudioFileInput.Text;
        foreach (string currFile in audioFileInputPath.Split('|'))
        {
            if (!File.Exists(currFile))
            {
                SetMessage(String.Format($"Invalid audio source: selected file {currFile} does not exist."), "", MessageKind.Error);
                UpdateUiState(UiState.ReadyToConnect);
                return;
            }
        }
    }
    bool shouldSuspendInputAudioDuringTTS = this.CutInputAudioCheckBox.IsChecked.HasValue ? this.CutInputAudioCheckBox.IsChecked.Value : false;

    correlationId = Guid.NewGuid().ToString("D").Split('-')[0].ToUpperInvariant();

    // Setup speech translation client options
    SpeechClientOptions options;

    string voicename = "";
    if (this.Voice.SelectedItem != null)
    {
        voicename = ((ComboBoxItem)this.Voice.SelectedItem).Tag.ToString();
    }
    options = new SpeechTranslateClientOptions()
    {
        TranslateFrom = ((ComboBoxItem)this.FromLanguage.SelectedItem).Tag.ToString(),
        TranslateTo = ((ComboBoxItem)this.ToLanguage.SelectedItem).Tag.ToString(),
        Voice = voicename,
    };
    
    options.Hostname = baseUrl;
    options.AuthHeaderKey = "Authorization";
    options.AuthHeaderValue = ""; // set later in ConnectAsync.
    options.ClientAppId = new Guid("EA66703D-90A8-436B-9BD6-7A2707A2AD99");
    options.CorrelationId = this.correlationId;
    options.Features = GetFeatures().ToString().Replace(" ", "");
    options.Profanity = ((SpeechClient.ProfanityFilter)Enum.Parse(typeof(SpeechClient.ProfanityFilter), GetProfanityLevel(), true)).ToString();
    options.Experimental = MenuItem_Experimental.IsChecked;

    // Setup player and recorder but don't start them yet.
    WaveFormat waveFormat = new WaveFormat(16000, 16, 1);

    // WaveProvider for incoming TTS
    // We use a rather large BufferDuration because we need to be able to hold an entire utterance.
    // TTS audio is received in bursts (faster than real-time).
    textToSpeechBytes = 0;
    playerTextToSpeechWaveProvider = new BufferedWaveProvider(waveFormat);
    playerTextToSpeechWaveProvider.BufferDuration = TimeSpan.FromMinutes(5);

    ISampleProvider sampleProvider = null;
    if (audioFileInputPath != null)
    {
        // Setup mixing of audio from input file and from TTS
        playerAudioInputWaveProvider = new BufferedWaveProvider(waveFormat);
        var srce1 = new Pcm16BitToSampleProvider(playerTextToSpeechWaveProvider);
        var srce2 = new Pcm16BitToSampleProvider(playerAudioInputWaveProvider);
        var mixer = new MixingSampleProvider(srce1.WaveFormat);
        mixer.AddMixerInput(srce1);
        mixer.AddMixerInput(srce2);
        sampleProvider = mixer;
    }
    else
    {
        recorder = new WaveIn();
        recorder.DeviceNumber = (int)((ComboBoxItem)Mic.SelectedItem).Tag;
        recorder.WaveFormat = waveFormat;
        recorder.DataAvailable += OnRecorderDataAvailable;
        sampleProvider = playerTextToSpeechWaveProvider.ToSampleProvider();
    }

    if (!batchMode)
    {
        player = new WaveOut();
        player.DeviceNumber = (int)((ComboBoxItem)Speaker.SelectedItem).Tag;
        player.Init(sampleProvider);
    }

    this.audioBytesSent = 0;

    string logAudioFileName = null;
    if (LogSentAudio.IsChecked|| LogReceivedAudio.IsChecked)
    {
        string logAudioPath = System.IO.Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.ApplicationData), Properties.Settings.Default.OutputDirectory);
        try
        {
            Directory.CreateDirectory(logAudioPath);
        }
        catch
        {
            this.AddItemToLog(string.Format("Could not create folder {0}", logAudioPath));
        }

        if (LogSentAudio.IsChecked)
        {
            logAudioFileName = System.IO.Path.Combine(logAudioPath, string.Format("audiosent_{0}.wav", this.correlationId));
        }

        if (LogReceivedAudio.IsChecked)
        {
            string fmt = System.IO.Path.Combine(logAudioPath, string.Format("audiotts_{0}_{{0}}.wav", this.correlationId));
            this.audioReceived = new BinaryMessageDecoder(fmt);
        }
    }
}
```

Jelentős része `Connect()` magában foglalja a létrehozása olyan `SpeechClientOptions` példány (lásd: `SpeechClientOptions.cs`) ahhoz, hogy a fordítási lehetőségeit. A lehetőségek között (például a hitelesítési kulcs és állomásnév) szolgáltatáshoz való kapcsolódáshoz szükséges adatokat és a fordításhoz használt szolgáltatásait. A mezők itt van leképezve a fejlécmezők és a HTTP-paraméterek által elérhetővé tett [a fordító Diktálásfelismerési API](http://docs.microsofttranslator.com/speech-translate.html).

`Connect()` is hoz létre, és inicializálja a hangbemeneti eszközöket (változó `sampleProvider`), amely a beszédfelismerés lefordítani a forrásaként szolgál. Ez az eszköz, például a mikrofon bemeneti hardvereszköz vagy WAVE adatokat tartalmazó fájlt.

Itt a `ConnectAsync()` módszerrel, amely elindítja a `speechClient` osztály és a szöveg és a szolgáltatás bináris válaszainak névtelen funkciókat hurkok.

```csharp
private async Task ConnectAsync(SpeechClientOptions options, bool suspendInputAudioDuringTTS)
{
    await ADMAuthenticate(options);
    
    TextMessageDecoder textDecoder;
    
    s2smtClient = new SpeechClient((SpeechTranslateClientOptions)options, CancellationToken.None);
    
    s2smtClient.OnBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnEndOfBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnTextData += (c, a) => { textDecoder.AppendData(a); lastReceivedPacketTick = DateTime.Now.Ticks; };
    s2smtClient.OnEndOfTextData += (c, a) =>
    {
        textDecoder.AppendData(a);
        lastReceivedPacketTick = DateTime.Now.Ticks;
        textDecoder
            .Decode()
            .ContinueWith(t =>
            {
                if (t.IsFaulted)
                {
                    Log(t.Exception, "E: Failed to decode incoming text message.");
                }
                else
                {
                    object msg = t.Result;
                    if (msg.GetType() == typeof(FinalResultMessage))
                    {
                        // omitted: code to process final binary result
                    }
                    if (msg.GetType() == typeof(PartialResultMessage))
                    {
                        // omitted: code to process partial binary result
                    }
                }
            });
    };
    s2smtClient.Failed += (c, ex) =>
    {
        Log(ex, "E: SpeechTranslation client reported an error.");
    };
    s2smtClient.Disconnected += (c, ea) =>
    {
        SafeInvoke(() =>
        {
            // We only care to react to server disconnect when our state is Connected. 
            if (currentState == UiState.Connected)
            {
                Log("E: Connection has been lost.");
                Log($"E: Errors (if any): \n{string.Join("\n", s2smtClient.Errors)}");
                Disconnect();
            }
        });
    };
    await s2smtClient.Connect();
}
```

Hitelesítés után a hoz létre a `SpeechClient` példány. A `SpeechClient` osztály (a `SpeechClient.cs`) hív meg eseménykezelők bináris és a szöveges adatok fogadását. További kezelők hívják, ha a kapcsolat meghibásodik, vagy megszakad.

Bináris adatok hang (szöveg-beszéd kimeneti), a szolgáltatás által küldött, ha a szöveg-beszéd átalakítás engedélyezve van. Részleges vagy egy teljes fordítás szóbeli szöveg adata. Így után példányának, a módszer csatlakozik, ezek az üzenetek kezeléséhez funkciókat: hang tárolása a későbbi lejátszás, és a szöveg a ablakban megjelenítésével.

## <a name="next-steps"></a>További lépések

A fenti egy gazdag alkalmazás, amely tartalmazza a fordító Diktálásfelismerési API használatát. Ilyen számos valós mozgó részek megértése. A legfontosabb bits használatával már telefonon. A többi lehet fontos információkat tartalmazó állítson be néhány töréspontokat a Visual Studio és a fordítási folyamatát ismerteti. Miután megismerte a mintaalkalmazást, a van ellátva a a fordító beszéd szolgáltatás használja saját alkalmazásaiban.

> [!div class="nextstepaction"]
> [A Microsoft Translator Diktálásfelismerési API-referencia](http://docs.microsofttranslator.com/speech-translate.html)
