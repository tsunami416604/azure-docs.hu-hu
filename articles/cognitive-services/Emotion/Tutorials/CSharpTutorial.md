---
title: 'Oktatóanyag: Érzelemfelismerés a egy oldallal a kép – Emotion API,C#'
titlesuffix: Azure Cognitive Services
description: Megismerhet egy, a képeken szereplő arcok által kifejezett érzelmek felismerésére szolgáló, alapszintű Windows-alkalmazást.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: tutorial
ms.date: 01/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 7921aac33abc6df121f734778fca7f9dddb19be7
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55862547"
---
# <a name="tutorial-recognize-emotions-on-a-face-in-an-image"></a>Oktatóanyag: Érzelemfelismerés a egy oldallal a képet.

> [!IMPORTANT]
> Az Emotion API 2019. február 15-ével elavulttá válik. Az érzelemfelismerési képesség mostantól a [Face API](https://docs.microsoft.com/azure/cognitive-services/face/) részeként általánosan elérhető. 

Megismerhet egy, a képeken szereplő arcok által kifejezett érzelmek felismerésére szolgáló, az Emotion API-t használó, alapszintű Windows-alkalmazást. Az alábbi példában elküldheti egy kép URL-címét vagy egy helyileg tárolt fájlt. Ezt a nyílt forráskódú példát sablonként használhatja a saját Windows-alkalmazásának az Emotion API és a .NET-keretrendszer részeként elérhető WPF (Windows Presentation Foundation) használatával történő elkészítéséhez.

## <a name="Prerequisites">Előfeltételek</a>
#### <a name="platform-requirements"></a>Platformkövetelmények  
Az alábbi példa a [Visual Studio 2015 Community Editiont](https://www.visualstudio.com/products/visual-studio-community-vs) használó .NET-keretrendszerhez lett kidolgozva.  

#### <a name="subscribe-to-emotion-api-and-get-a-subscription-key"></a>Előfizetés az Emotion API használatára és előfizetői azonosító beszerzése  
A példa létrehozása előtt elő kell fizetnie a Microsoft Cognitive Services részét képező Emotion API-ra. Lásd az [előfizetéseket](https://azure.microsoft.com/try/cognitive-services/). Ehhez az oktatóanyaghoz az elsődleges és a másodlagos kulcs is használható. Az API-kulcs biztonságban és titokban tartásához mindenképpen kövesse az ajánlott eljárásokat.  

#### <a name="get-the-client-library-and-example"></a>Az ügyfélkódtár és a példa lekérése  
[SDK-n](https://www.github.com/microsoft/cognitive-emotion-windows) keresztül letöltheti az Emotion API ügyfélkódtárat. A letöltött zip-fájlt ki kell csomagolni egy tetszőleges mappába. Számos felhasználó a Visual Studio 2015 mappát választja.
## <a name="Step1">1. lépés: Nyissa meg a példa</a>
1.  Indítsa el a Microsoft Visual Studio 2015-öt, és kattintson a **File** (Fájl) elemre, válassza az **Open** (Megnyitás) parancsot, majd a **Project/Solution** (Projekt/Megoldás) elemet.
2.  Lépjen abba a mappába, amelybe a letöltött Emotion API-fájlokat mentette. Kattintson az **Emotion** elemre, majd a **Windows** elemre, végül a **Sample-WPF** mappára.
3.  Kattintson duplán az **EmotionAPI-WPF-Samples.sln** nevű Visual Studio 2015 Solution- (.sln-) fájl megnyitásához. Ez megnyitja a megoldást a Visual Studióban.

## <a name="Step2">2. lépés: A példában létrehozása</a>
1. A **Solution Explorer** (Megoldáskezelő) ablakban kattintson a jobb gombbal a **References** (Hivatkozások) elemre, és válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget.

  ![A NuGet-csomagkezelő megnyitása](../Images/EmotionNuget.png)

2.  Megnyílik a **NuGet Package Manager** (NuGet-csomagkezelő) ablak. Először válassza a bal felső sarokban lévő **Browse** (Tallózás) gombot, majd a keresőmezőbe írja be a „Newtonsoft.Json” kifejezést, válassza a **Newtonsoft.Json** csomagot, és kattintson az **Install** (Telepítés) parancsra.  

  ![NuGet-csomag megkeresése tallózással](../Images/EmotionNugetBrowse.png)  

3.  Nyomja le a Ctrl+Shift+B billentyűparancsot vagy kattintson a menüszalag **Build** (Összeállítás) elemére, majd a **Build Solution** (Megoldás összeállítása) parancsra.

## <a name="Step3">3. lépés: A példa Futtatás</a>
1.  A létrehozás befejezése után nyomja le az **F5** billentyűt, vagy kattintson a menüszalag **Start** (Indítás) elemére a példa futtatásához.
2.  Keresse meg az Emotion API-ablakot a „**Paste your subscription key here to start**” (Illessze be ide az előfizetési azonosítót a kezdéshez) üzenetet tartalmazó **szövegmezővel**. Illessze az előfizetési azonosítót a szövegmezőbe az alábbi képernyőképen látható módon. A „Save Key” (Kulcs mentése) gombra kattintva tárolhatja az előfizetési azonosítót a számítógépén vagy laptopján. Ha törölni szeretné az előfizetési azonosítót a rendszerről, kattintson a „Delete Key” (Kulcs törlése) parancsra, hogy eltávolítsa a számítógépéről vagy laptopjáról.

  ![Az érzelemfelismerési funkció felülete](../Images/EmotionKey.png)

3.  A „**Select Scenario**” (Forgatókönyv kiválasztása) területen kattintson a „**Detect emotion using a stream**” (Érzelemfelismerés streammel) vagy a „**Detect emotion using a URL**” (Érzelemfelismerés URL-címmel) forgatókönyvre, majd kövesse a képernyőn megjelenő utasításokat. A Microsoft megkapja a feltöltött képeket, és a használatukkal javíthatja az Emotion API és a kapcsolódó szolgáltatások működését. Egy kép elküldésekor megerősíti, hogy betartotta a [Fejlesztői magatartási kódexet](https://azure.microsoft.com/support/legal/developer-code-of-conduct/).
4.  Ezzel a példaalkalmazással példaképek is használhatók. Ezek a lemezképek találhat [a Face API GitHub-adattárat](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) alatt a **adatok** mappát. Vegye figyelembe, hogy ezek a képek a méltányos használattal kapcsolatos megállapodás hatálya alá tartoznak, ami azt jelenti, hogy a példa teszteléséhez használhatók, de ismételt közzétételhez nem.

## <a name="Review">Áttekintés és tanulás</a>
Most, hogy egy futó alkalmazással rendelkezik, tekintsük át, hogyan integrálható ez a példaalkalmazás a Microsoft Cognitive Services-szolgáltatásokkal. Ez megkönnyíti az alkalmazás fejlesztésének folytatását, vagy saját alkalmazás kifejlesztését a Microsoft Emotion API-val.

Ez a példaalkalmazás az Emotion API ügyfélkódtárat használja, amely a Microsoft Emotion API vékony C#-ügyfélburkolója. Ha a fent leírt módon építette fel a példaalkalmazást, egy NuGet-csomagból szerezte be az ügyfélkódtárat. Az ügyfélkódtár forráskódját a „[Client Library](https://github.com/Microsoft/Cognitive-Emotion-Windows/tree/master/ClientLibrary)” (Ügyfélkódtár) nevű mappában tekintheti át az **Emotion**, **Windows**, **Client Library** alatt, amely a fenti [előfeltételekben](#Prerequisites) említett letöltött fájltár részét képezi.

Az ügyféloldali kódtár kód használata is talál **Megoldáskezelőben**: Alatt **EmotionAPI-WPF_Samples**, bontsa ki a **DetectEmotionUsingStreamPage.xaml** található **DetectEmotionUsingStreamPage.xaml.cs**, navigáljon az használt egy helyileg tárolt fájlt, vagy bontsa ki a **DetectEmotionUsingURLPage.xaml** található **DetectEmotionUsingURLPage.xaml.cs**, egy kép URL-címe feltöltésekor használt. Kattintson duplán a .xaml.cs-fájlokra, hogy egy új ablakban nyissa meg azokat a Visual Studióban.

A példaalkalmazásban az Emotion ügyfélkódtár használatának áttekintéséhez nézzük meg a **DetectEmotionUsingStreamPage.xaml.cs** és a **DetectEmotionUsingURLPage.xaml.cs** két kódtöredékét. Mindegyik fájl kódmegjegyzéseket tartalmaz a „KEY SAMPLE CODE STARTS HERE” (A mintakód itt kezdődik) és a „KEY SAMPLE CODE ENDS HERE” (A mintakód itt fejeződik be) elnevezéssel, hogy könnyebben megtalálja az alább szereplő kódrészleteket.

Az Emotion API bemenetként a kép URL-címével és bináris képadatokkal (oktettstream formájában) is tud dolgozni. A két lehetőség áttekintését alább láthatja. Mindkét esetben először egy using utasítást talál, amellyel az Emotion ügyfélkódtárat használhatja.
```csharp

            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Use the following namespace for EmotionServiceClient
            // -----------------------------------------------------------------------
            using Microsoft.ProjectOxford.Emotion;
            using Microsoft.ProjectOxford.Emotion.Contract;
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
#### <a name="detectemotionusingurlpagexamlcs"></a>DetectEmotionUsingURLPage.xaml.cs

Ez a kódrészlet bemutatja, hogyan küldheti el az előfizetési azonosítót és egy fénykép URL-címét az ügyfélkódtárral az Emotion API szolgáltatásba.

```csharp

            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // -----------------------------------------------------------------------

            window.Log("EmotionServiceClient is created");

            //
            // Create Project Oxford Emotion API Service client
            //
            EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

            window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
            try
            {
                //
                // Detect the emotions in the URL
                //
                Emotion[] emotionResult = await emotionServiceClient.RecognizeAsync(url);
                return emotionResult;
            }
            catch (Exception exception)
            {
                window.Log("Detection failed. Please make sure that you have the right subscription key and proper URL to detect.");
                window.Log(exception.ToString());
                return null;
            }
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
#### <a name="detectemotionusingstreampagexamlcs"></a>DetectEmotionUsingStreamPage.xaml.cs

Alább látható, hogyan küldheti el az előfizetési azonosítót és egy helyileg tárolt képet az Emotion API-nak.


```csharp


            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // -----------------------------------------------------------------------

            //
            // Create Project Oxford Emotion API Service client
            //
            EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

            window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
            try
            {
                Emotion[] emotionResult;
                using (Stream imageFileStream = File.OpenRead(imageFilePath))
                {
                    //
                    // Detect the emotions in the URL
                    //
                    emotionResult = await emotionServiceClient.RecognizeAsync(imageFileStream);
                    return emotionResult;
                }
            }
            catch (Exception exception)
            {
                window.Log(exception.ToString());
                return null;
            }
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
<!--
## <a name="Related">Related Topics</a>
[Emotion API Overview](.)
-->
