---
title: Translator Speech oktatóanyag (C#) |} A Microsoft Docs
titleSuffix: Cognitive Services
description: Ismerje meg, hogyan valós idejű fordíthat a Translator speech service segítségével.
services: cognitive-services
author: v-jerkin
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-speech
ms.devlang: csharp
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jerkin
ms.openlocfilehash: 010ad8b5ceeaf046c8d361ff352e6058154a482d
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2018
ms.locfileid: "41987481"
---
# <a name="tutorial-microsoft-translator-wpf-application-in-c"></a>Oktatóanyag: A Microsoft Translator WPF-alkalmazás a C#-ban

Ez az oktatóanyag olyan egy interaktív speech fordítási eszközt, amely a Microsoft Translator Speech fordítási szolgáltatás, az Azure-ban a Microsoft Cognitive Services részét bemutatóját. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Kérelem a szolgáltatás által támogatott nyelvek listája
> * Hang rögzítése, és továbbítja azt a szolgáltatás
> * Kap, és megjeleníti a fordítások a beszéd szövegként
> * Igény szerint lejátszása a fordítás kimondott (szöveg-hang transzformációs) verzióját

Ehhez az alkalmazáshoz a Visual Studio megoldásfájl [elérhető a Githubon](https://github.com/MicrosoftTranslator/SpeechTranslator).

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban szüksége lesz minden Visual Studio 2017, beleértve a Community Edition kiadását. 

A Visual Studio-megoldás meg is alapozza a telepítő az alkalmazáshoz. Van szüksége a [WiX eszközkészlet](http://wixtoolset.org/) és a [WiX eszközkészlet a Visual Studio-bővítmény](https://marketplace.visualstudio.com/items?itemName=RobMensching.WixToolsetVisualStudio2017Extension) az funkciót.

A Translator Speech szolgáltatáshoz, amelyet a Microsoft Azure-irányítópulton keresztül szerezhet be egy előfizetési kulcsot is szükséges. Egy ingyenes tarifacsomag érhető el, amely lehetővé teszi, hogy akár 10 órányi díjmentes havi speech fordítása. Ezen a szinten is elegendő ehhez az oktatóanyaghoz.

A külső [JSON.Net könyvtár](https://www.newtonsoft.com/json) (a Newtonsoft) is szükség. Ez a szerelvény által automatikusan telepített NuGet-csomagok visszaállításának különül engedélyezve vannak a Visual Studio-díjcsomagok Ha.

## <a name="trying-the-translation-app"></a>A fordítási app közben

A Microsoft a Beszédfordító megoldás megnyitása után (`SpeechTranslator.sln`) a Visual Studióban lenyomja az F5 billentyűt, és indítsa el az alkalmazást.  A program fő ablakában jelenik meg.

![[Az Translator speech fő ablakban]](media/speech-translator-main-window.png)

Az első futtatásakor válassza **Fiókbeállítások** származó a **beállítások** menü az itt látható ablak megnyitásához.

![[Az Translator speech fő ablakban]](media/speech-translator-settings-window.png)

Ebben az ablakban illessze be a Microsoft Translator Speech előfizetési kulcsot, majd kattintson a **mentéséhez.** A kulcsot a rendszer menti futtatásai között.

Vissza a fő ablakban válassza ki a hang bemeneti és kimeneti eszközök szeretné használni, és a feladó és nyelvek. Ha szeretnénk hallani a hangot a fordítás, ellenőrizze, hogy a **Szövegfelolvasás** (szöveg-hang transzformációs) beállítás be van jelölve. Ha meg szeretné tekinteni a spekulatív részleges fordításokat, a beszéd, engedélyezze a **részleges eredményeket** lehetőséget.

Végül kattintson **Start** fordítási megkezdéséhez. Tegyük fel, hogy valami lefordított rendelkezik, és nézze meg a felismert szöveget és a fordítás a ablakban jelennek meg. Ha engedélyezte a szöveg-beszéd átalakítás lehetőséget, a fordítás is hallott.

## <a name="obtaining-supported-languages"></a>Támogatott nyelvek beszerzése

Írásának pillanatában a Microsoft Translator szolgáltatás több mint öt tucat nyelveket támogatja a szöveg fordítását. Beszédalapú fordítási kevesebb nyelveket támogatja. Az ilyen nyelvű mindkét beszédátírási (beszédfelismerés), és a szöveg-hang transzformációs kimenet összefoglaló támogatásra van szüksége.

Más szóval a tolmácsolás, a Forrásnyelv egyikét kell megadni beszédátírási esetében támogatott. A kimeneti nyelv szövegfordítás, feltéve, hogy azt szeretné, hogy egy szöveges eredményt a támogatott nyelvek bármelyike lehet. Ha azt szeretné, hogy a kimeneti speech, csak lefordíttathatja a szöveg-hang transzformációs támogatott nyelvre.

A Microsoft időről időre új nyelvek támogatását adhat hozzá. Ezen okból kifolyólag nem kódolnia kell az alkalmazásban támogatott nyelvek ismerete. Ehelyett a Translator Speech API, amely lehetővé teszi, hogy futásidőben a támogatott nyelvek beolvasása nyelvek végpontot biztosít. Nyelvek listája egy vagy több fogadásához választhat: 

| | |
|-|-|
|`speech`|A lejegyzés támogatott nyelveket. Beszédalapú fordítási forrás nyelvet is lehet.|
|`text`|A szöveg szövegfordítás támogatott nyelveket. Cél nyelveit beszédalapú fordítási akkor lehet, ha a szöveges kimenet szolgál.|
|`tts`|A beszédszintézishez támogatott beszédhangot, egyes társított nyelv. Cél nyelveit beszédalapú fordítási akkor lehet, ha a szöveg-hang transzformációs szolgál. Egy adott nyelven lehet, hogy egynél több hangalapú támogatja.|

A nyelvek végpont nem igényel egy előfizetési kulcsot, és azok használatát nem számítanak bele a kvótába. Az URI-ja `https://dev.microsofttranslator.com/languages` és az eredményeket JSON formátumban adja vissza.

A metódus `UpdateLanguageSettingsAsync()` a `MainWindow.xaml.cs`, látható itt, meghívja a nyelvek végpont beolvasni a támogatott nyelvek listáját. 

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

Ez a módszer először létrehoz egy HTTP-kérelem a nyelvek végpont nyelvek három listáját kérő (`text`, `speech`, és `tts`).

A nyelvek végpontot használ a kérelem `Accept-Languages` fejléc meghatározni a nyelvet, amelyben a nyelvek nevei szerepelnek. Például a nyelv angol nyelvű előadói ismert "Német" nevezik "Deutsch" német és spanyol "Alemán", és nyelvek listáját jeleníti meg a különbségeket. A rendszer alapértelmezett nyelv a fejléc szolgál.

Miután a kérelem el lett küldve, és azokat a belső adattárakon elemzi a JSON-válasz érkezik, a válasz. Ezen szerkezetek vannak majd segítségével hozza létre a a nyelv és a nyelvi menü. 

Rendelkezésre álló beszédhangot attól függ, hogy a felhasználó által kiválasztott, nyelvi, mivel nem állítható be a hangalapú menü még. Ehelyett az egyes nyelvekhez tartozó elérhető hangok tárolódnak későbbi használatra. A `ToLanguage_SelectionChanged` (ugyanabban a fájlban forrás) kezelő később frissíti a hangalapú menü meghívásával `UpdateVoiceComboBox()` amikor a felhasználó úgy dönt, a nyelvet. 

Csak a szórakoztató a nyelv véletlenszerűen kiválasztása a felhasználó nem futott az alkalmazás előtt. (A menü Beállítások tárolt munkamenetek között.)

## <a name="authenticating-requests"></a>Kérelmek hitelesítése

A Microsoft Translator Speech szolgáltatással kell az Azure-előfizetési kulcs küldése a fejléc értékeként való hitelesítésre `Ocp-Apim-Subscription-Key` a kapcsolati kérésben.

## <a name="translation-overview"></a>Fordítási áttekintése

A fordítás API-t (a websockets protokoll végpont `wss://dev.microsofttranslator.com/speech/translate`) fogad hang lehet a monophonic, 16 kHz, 16 bites aláírt audiostreamként. A szolgáltatás legalább egy JSON-válaszok tartalmazó, mind a felismert és lefordított szöveget adja vissza. Szöveg-hang transzformációs van szükség, ha egy hangfájlt zajlik.

A felhasználó úgy dönt, hogy a bemeneti mikrofon/fájl menü segítségével hangforrásról. A hang-eszköz (például a mikrofon) vagy a származhat egy `.WAV` fájlt.

A metódus `StartListening_Click` akkor aktiválódnak, ha a felhasználó a Start gombra kattint. Az eseménykezelő meghívja `Connect()` hang küldeni a szolgáltatás API-végpont a folyamat megkezdéséhez. A `Connect()` módszer a következő feladatokat hajtja végre:


> [!div class="checklist"]
> * Felhasználói beállítások lekérése a fő ablakot, és ellenőrizze őket
> * Inicializálása a hang bemeneti és kimeneti steamjeihez
> * Hívó `ConnectAsync()` , a többit bízza a munka

`ConnectAsync()`, viszont kezeli a következő teendőt:

> [!div class="checklist"]
> * Az Azure Subscription-key fejléc a hitelesítése `Ocp-Apim-Subscription-Key`
> * Létrehozás egy `SpeechClient` példány (található `SpeechClient.cs`) a szolgáltatással való kommunikációra
> * Inicializálása `TextMessageDecoder` és `BinaryMessageDecoder` példányok (lásd: `SpeechResponseDecoder.cs`) válaszok kezeléséhez
> * A hang-n keresztül küld a `SpeechClient` a Translator Speech service-példány
> * Fogadására és feldolgozására a fordítás az eredményeit

A felelősséget a `SpeechClient` kevesebb:

> [!div class="checklist"]
> * A Translator Speech service WebSocket kapcsolatot
> * Hang adatküldés és a válaszfogadás a szoftvercsatorna-n keresztül

## <a name="a-closer-look"></a>Részletes bemutatása

Kell világosabb most már az alkalmazás részei együttműködésének a fordítási kérelem végrehajtásához. Vessünk egy pillantást egy kódrészletet, a fontos részekkel kellene összpontosítani.

Íme egy részleges verziója `Connect()` , amely az audio-adatfolyamokat beállítás jelenik meg:

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

Egy jelentős része `Connect()` magában foglalja a létrehozása egy `SpeechClientOptions` példány (lásd: `SpeechClientOptions.cs`), amely tárolja a fordítási lehetőségeket. A lehetőségek között (például a hitelesítési kulcsot és állomásnév) a szolgáltatáshoz való csatlakozáshoz szükséges információkat és a fordításhoz használt funkciókhoz. Itt a mezők leképezése a üzenetfejlécének mezői és által elérhetővé tett HTTP-paraméterek [a Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference).

`Connect()` is hoz létre, és előkészíti a hangbemeneti eszköz (változó `sampleProvider`), amely kiszolgálja a speech lehet forrásaként. Ez az eszköz, vagy egy bemeneti hardvereszközhöz, például egy mikrofonnal, vagy egy WAVE hang adatokat tartalmazó fájl.

Íme a `ConnectAsync()` módszer, amely példányosítja az `speechClient` osztály és kezeli a szöveg és a szolgáltatás bináris parancsválaszait névtelen funkciók be hurkokat.

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

Hitelesítés után a metódus létrehozza a `SpeechClient` példány. A `SpeechClient` osztály (a `SpeechClient.cs`) hívja meg az eseménykezelőket beérkezési bináris és szöveges adatokat. További kezelők kerül meghívásra, ha a kapcsolat meghibásodik vagy bontja a kapcsolatot.

Bináris adatok a hang (szöveg-hang transzformációs kimeneti), a szolgáltatás által küldött, ha engedélyezve van a szöveg-beszéd átalakítás. Szöveges adatok egy részleges vagy teljes fordítását, a kimondott szöveg. Úgy hárítható el, miután a metódus kezelje ezeket az üzeneteket funkciókat hooks: audio tárolva későbbi lejátszás és szöveget az ablakban megjelenítésével.

## <a name="next-steps"></a>További lépések

Ez a kódminta a funkciógazdag alkalmazások bemutatásához a Translator Speech API használatát. Emiatt a nincsenek megértéséhez részek valós számát. A legfontosabb bits bizonnyal már. A többi lehet fontos információkat tartalmazó állítson be néhány töréspontokat a Visual Studio és a fordítási folyamat bemutatására. Miután megismerte a mintaalkalmazást, a használt látni a Translator Speech service használata a saját alkalmazásokban.

> [!div class="nextstepaction"]
> [A Microsoft Translator Speech API-referencia](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
