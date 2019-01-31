---
title: 'Oktatóanyag: Translator Speech APIC#'
titleSuffix: Azure Cognitive Services
description: A Translator Speech API használatával valós időben fordíthat szövegeket.
services: cognitive-services
author: v-jerkin
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: tutorial
ms.date: 3/5/2018
ms.author: v-jerkin
ms.openlocfilehash: 01e56366fc8ae0e711664d35e95ac1aacc89730f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55458499"
---
# <a name="tutorial-translator-speech-application-in-c"></a>Oktatóanyag: A Translator Speech alkalmazásC#

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Ez az oktatóanyag az Azure Cognitive Services részét képező Translator Speech API-t használó interaktív szövegfordító eszközt mutat be. Az alábbiakat fogja elsajátítani:

> [!div class="checklist"]
> * A szolgáltatás által támogatott nyelvek listájának lekérése
> * Hanganyagok rögzítése és közvetítése a szolgáltatás felé
> * Beszéd fordításának fogadása és szövegként való megjelenítése
> * A fordítás beszélt változatának (szövegfelolvasás) opcionális lejátszása

Az alkalmazáshoz tartozó Visual Studio-megoldásfájl [elérhető a GitHubon](https://github.com/MicrosoftTranslator/SpeechTranslator).

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz a Visual Studio 2017 valamelyik kiadására – a Community Editiont is beleértve – lesz szüksége. 

A Visual Studio-megoldás telepítőt is létrehoz az alkalmazáshoz. A funkció támogatásához a [WiX eszközkészlet](http://wixtoolset.org/) és a [WiX eszközkészlethez készült Visual Studio-bővítmény](https://marketplace.visualstudio.com/items?itemName=RobMensching.WixToolsetVisualStudio2017Extension) szükséges.

A Translator Speech szolgáltatáshoz szükség van továbbá egy előfizetői azonosítóra, amelyet a Microsoft Azure-irányítópultról szerezhet be. Az ingyenes tarifacsomag keretében havonta legfeljebb 10 órányi beszédet ingyenesen fordíthat le. Ez a szint elegendő ehhez az oktatóanyaghoz.

A külső [JSON.Net kódtár](https://www.newtonsoft.com/json) (a Newtonsofttól) is szükséges. Ezt a szerelvényt a NuGet automatikusan telepíti, ha mindkét csomag-visszaállítási jelölőnégyzet be van jelölve a Visual Studio beállításaiban.

## <a name="trying-the-translation-app"></a>A fordítóalkalmazás kipróbálása

Nyissa meg a Speech Translator megoldást (`SpeechTranslator.sln`) a Visual Studióban, majd nyomja le az F5 billentyűt az alkalmazás összeállításához és futtatásához.  Megjelenik a program fő ablaka.

![[A Speech Translator fő ablaka]](media/speech-translator-main-window.png)

Az első futtatásakor válassza a **Fiókbeállítások** elemet a **Beállítások** menüben az itt látható ablak megnyitásához.

![[A Speech Translator fő ablaka]](media/speech-translator-settings-window.png)

Illessze be a Translator Speech előfizetői azonosítóját ebbe az ablakba, majd kattintson a **Mentés** gombra. A rendszer menti a kulcsot a későbbi futtatáshoz.

A fő ablakra visszatérve válassza ki a használni kívánt hangbemeneti és -kimeneti eszközöket, majd a forrás- és célnyelvet. Ha meg kívánja hallgatni a fordítást, ügyeljen rá, hogy be legyen jelölve a **TTS** (szövegfelolvasás) lehetőség. Ha beszéd közben szeretné látni a spekulatív részleges fordításokat, engedélyezze a **Részleges eredmények** lehetőséget.

Végezetül kattintson az **Indítás** gombra a fordítás elindításához. Mondjon valamit, amit le szeretne fordítani, és figyelje, ahogy megjelenik a felismert szöveg és a fordítás az ablakban. Ha engedélyezte a szövegfelolvasás lehetőségét, hallhatja is a fordítást.

## <a name="obtaining-supported-languages"></a>Támogatott nyelvek beszerzése

E dokumentum írásakor a Translator Speech szolgáltatás a szöveges fordítást több mint öt tucat nyelven támogatja. A beszédfordítás esetében a támogatott nyelvek száma kisebb. Ezen nyelvek esetében szükség van az átírás (beszédfelismerés), valamint a szövegfelolvasáshoz a szintézis támogatására is.

Más szóval a beszédfordításhoz a forrásnyelv átírásának támogatása szükséges. A kimeneti nyelv bármely olyan nyelv lehet, amelynek a szöveges fordítása támogatott, feltéve, ha szöveges eredményt szeretne. Ha beszédkimenetre van szüksége, csak olyan nyelvre fordíthat, amelyhez a szövegfelolvasás támogatott.

A Microsoft időről időre további nyelvek támogatását vezetheti be, ezért ne rögzítse szoftveresen a támogatott nyelvekre vonatkozó ismereteket az alkalmazásaiba. A Translator Speech API egy Nyelvek végpontot biztosít, amely lehetővé teszi a támogatott nyelvek lekérdezését a futtatáskor. Egy vagy több nyelvlista lekérése közül választhat: 

| | |
|-|-|
|`speech`|A beszédátírás támogatott nyelvei. A beszédfordítás forrásnyelvei is lehetnek.|
|`text`|A szöveges fordítás támogatott nyelvei. A beszédfordítás célnyelvei is lehetnek szöveges kimenet használata esetében.|
|`tts`|A beszédszintézis támogatott hangjai, amelyek mindegyike egy adott nyelvhez tartozik. A beszédfordítás célnyelvei is lehetnek szövegfelolvasásos kimenet használata esetében. Egy adott nyelvet egynél több hang is támogathat.|

A Nyelvek végpont nem igényel előfizetői azonosítót, és a használata nem számít bele a kvótába. Az URI-ja `https://dev.microsofttranslator.com/languages`, és az eredményeket JSON formátumban adja vissza.

A `MainWindow.xaml.cs` itt látható `UpdateLanguageSettingsAsync()` metódusa hívja meg a Nyelvek végpontot a támogatott nyelvek listájának lekéréséhez. 

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

Ez a metódus először egy, a Nyelvek végpontnak címzett HTTP-kérelmet állít össze, amely mind a három nyelvlistát lekéri (`text`, `speech` és `tts`).

A Nyelvek végpont a kérelem `Accept-Languages` fejléce alapján határozza meg, melyik nyelven jelenítse meg a nyelvek nevét. Például a magyarul beszélők számára „német” néven ismert nyelv németül „Deutsch”, spanyolul pedig „Alemán”, és a nyelvek listája tükrözi ezeket az eltéréseket. Ez a fejléc a rendszer alapértelmezett nyelvét használja.

Ha a kérelem el lett küldve, és megérkezett a JSON-válasz, a választ belső adatszerkezetekbe elemzi a rendszer. A Forrásnyelv és Célnyelv menük létrehozása e belső struktúrák alapján történik. 

Mivel az elérhető hangok a felhasználó által választott Célnyelvtől függenek, még nem lehetséges a Hang menü felépítése. Ehelyett az egyes nyelvekhez tartozó elérhető hangok későbbi használatra tárolódnak. A `ToLanguage_SelectionChanged` kezelő (ugyanebben a forrásfájlban) később frissíti a Hang menüt az `UpdateVoiceComboBox()` meghívásával, amikor a felhasználó kiválasztja a Célnyelvet. 

A móka kedvéért a Célnyelv kiválasztása véletlenszerűen történik, ha a felhasználó most futtatja először az alkalmazást. (A menübeállításokat a munkamenetek között menti a rendszer.)

## <a name="authenticating-requests"></a>Kérelmek hitelesítése

A Microsoft Translator Speech szolgáltatással való hitelesítéshez az Azure előfizetői azonosítóját kell a `Ocp-Apim-Subscription-Key` értékeként elküldenie a csatlakozási kérelem fejlécében.

## <a name="translation-overview"></a>Fordítás áttekintése

A Translate API (WebSockets-végpont: `wss://dev.microsofttranslator.com/speech/translate`) mono, 16 kHz-es, 16 bites aláírt WAVE formátumban fogad hangbemenetet fordításra. A szolgáltatás egy vagy több JSON-választ ad vissza, amelyek tartalmazzák a felismert és a fordított szöveget is. Szövegfelolvasás igénylése esetén a rendszer hangfájlt küld.

A felhasználó kiválasztja a hangforrást a Mikrofon/Fájlbemenet menüben. A hang forrása lehet egy hangeszköz (pl. mikrofon) vagy egy `.WAV` fájl.

Amikor a felhasználó az Indítás gombra kattint, a rendszer meghívja a `StartListening_Click` metódust. Ez az eseménykezelő ekkor a `Connect()` meghívásával megkezdi a hanganyag elküldését a szolgáltatás API-végpontjának. A `Connect()` metódus a következő feladatokat végzi el:


> [!div class="checklist"]
> * Felhasználói beállítások lekérése a főablakból, majd azok ellenőrzése
> * A hangbemeneti és -kimeneti streamek inicializálása
> * A `ConnectAsync()` meghívása a további feladatok elvégzéséhez

A `ConnectAsync()` a következő feladatokról gondoskodik:

> [!div class="checklist"]
> * Hitelesítés a `Ocp-Apim-Subscription-Key` fejlécben lévő Azure előfizetői azonosítóval
> * `SpeechClient`-példány létrehozása (helye: `SpeechClient.cs`) a szolgáltatással való kommunikációhoz
> * `TextMessageDecoder`- és `BinaryMessageDecoder`-példányok inicializálása (lásd: `SpeechResponseDecoder.cs`) a válaszok kezeléséhez
> * A hanganyag `SpeechClient`-példány révén történő elküldése a Translator Speech szolgáltatásnak
> * A fordítás eredményének fogadása és feldolgozása

A `SpeechClient` feladatköre kisebb:

> [!div class="checklist"]
> * WebSocket-kapcsolat létesítése a Translator Speech szolgáltatással
> * Hangadatok elküldése és válaszok fogadása a szoftvercsatornán keresztül

## <a name="a-closer-look"></a>Közelebbi vizsgálat

Mostanra már jobban tisztában lehet vele, hogyan működnek együtt az alkalmazás egyes részei a fordítási kérés végrehajtása céljából. Nézzük meg a kódot, a fontos részekre koncentrálva.

Íme a `Connect()` egy részleges változata, amely a hangstreamek felépítését mutatja:

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

A `Connect()` jelentős részét egy `SpeechClientOptions`-példány létrehozása teszi ki (lásd: `SpeechClientOptions.cs`), amely a fordítás beállításait tartalmazza. A beállításokba tartoznak a szolgáltatáshoz való csatlakozáshoz szükséges információk (pl. hitelesítési kulcs és gazdagépnév), valamint a fordításhoz használt funkciók. Az itt található mezők a [Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference) által elérhetővé tett fejlécmezőkbe és HTTP-paraméterekbe lesznek leképezve.

A `Connect()` továbbá létrehozza és inicializálja a bementi hangeszközt (`sampleProvider` változó), amely a fordítandó beszédanyag forrásaként szolgál. Ez az eszköz vagy hardveres bemeneti eszköz, pl. mikrofon, vagy egy WAVE-hangadatokat tartalmazó fájl.

Íme a `ConnectAsync()` metódus, amely példányosítja a `speechClient` osztályt, és névtelen függvényeket csatlakoztat a szolgáltatástól érkező szöveges és bináris válaszok kezeléséhez.

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

A hitelesítés után a metódus létrehozza a `SpeechClient`-példányt. A `SpeechClient`-osztály (helye: `SpeechClient.cs`) eseménykezelőket hív meg a bináris és szöveges adatok fogadásakor. Ha a kapcsolat sikertelen vagy megszakad, további kezelők lesznek meghívva.

A bináris adatok a szolgáltatás által küldött hangadatok (szövegfelolvasási kimenet), ha a TTS engedélyezve van. A szöveges adatok a beszélt szöveg részleges vagy teljes fordítását tartalmazzák. Tehát a példányosítás után a metódus függvényeket csatlakoztat ezeknek az üzeneteknek a kezeléséhez: a hangadatokat későbbi lejátszás céljából tárolja, a szöveget pedig megjeleníti az ablakban.

## <a name="next-steps"></a>További lépések

Ez a kódminta egy funkciókban gazdag alkalmazás, amely a Translator Speech API használatát mutatja be. Ebből kifolyólag sok, megértést kívánó, mozgó alkatrésze van. A legfontosabb részeket már megismerte. A továbbiak megértése szempontjából tanulságos lehet töréspontokat beállítani a Visual Studióban, és végighaladni a fordítási folyamaton. Ha megértette a mintaalkalmazást, képes lesz használni a Translator Speech szolgáltatást a saját alkalmazásaiban.

> [!div class="nextstepaction"]
> [Microsoft Translator Speech API-referencia](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
