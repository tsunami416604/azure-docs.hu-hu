---
title: Érzelemfelismerési API C# oktatóanyag |} Microsoft Docs
description: Egy alapszintű Windows-alkalmazást, amely a kognitív szolgáltatások Érzelemfelismerési API ismeri fel a kép lapok által kifejezett érzelmek vizsgálatát.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 01/23/2017
ms.author: anroth
ms.openlocfilehash: f015e5720f65d0951a77de76ce8882a6dcdc1c3b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347591"
---
# <a name="emotion-api-in-c35-tutorial"></a>Érzelemfelismerési API c.&#35; oktatóanyag

> [!IMPORTANT]
> Villámnézet API a 2017. október 30 véget ér. Kipróbálhatja az új [videó indexelő API előnézete](https://azure.microsoft.com/services/cognitive-services/video-indexer/) insights könnyen kibontani videók, és tartalom felderítési lép, például a keresési eredmények, növelje a szóbeli szavakat, a lapok, a karakterek és a érzelmek észlelésével. [További információk](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Egy alapszintű Windows-alkalmazás, amely Érzelemfelismerési API ismeri fel a kép lapok által kifejezett érzelmek vizsgálatát. Az alábbi példa segítségével elküldeni egy kép URL-CÍMÉT vagy egy helyileg tárolt fájlt. A nyílt forráskódú példa sablonként használhatja a saját alkalmazás a Windows rendszert a Érzelemfelismerési API-t és a WPF (a Windows Presentation Foundation), a .NET-keretrendszer része kiépítéséhez.

## <a name="Prerequisites">Előfeltételek</a>
#### <a name="platform-requirements"></a>Platform-követelmények  
Az alábbi példában kidolgozott a .NET-keretrendszer használatával [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).  

#### <a name="subscribe-to-emotion-api-and-get-a-subscription-key"></a>Érzelemfelismerési API-ra szolgáltatásra, és egy előfizetés kulcs beszerzése  
Mielőtt létrehozná a példa, elő kell fizetnie Érzelemfelismerési API-hoz a Microsoft kognitív szolgáltatások részét képező. Lásd: [előfizetések](https://azure.microsoft.com/try/cognitive-services/). Ebben az oktatóanyagban mind az elsődleges és másodlagos kulcsot is használható. Ügyeljen arra, hogy kövesse a bevált gyakorlatokat, hogy az API-kulcs titkos kulcsot, és biztonságáról.  

#### <a name="get-the-client-library-and-example"></a>Az ügyfél első könyvtár és példa  
Előfordulhat, hogy töltse le a Érzelemfelismerési API ügyféloldali kódtár keresztül [SDK](https://www.github.com/microsoft/cognitive-emotion-windows). A letöltött zip-fájl szükséges a kiválasztott mappába kinyerni, sok felhasználó válassza ki a Visual Studio 2015 mappát.
## <a name="Step1">1. lépés: Nyissa meg a példa</a>
1.  Indítsa el a Microsoft Visual Studio 2015-öt, és kattintson a **fájl**, jelölje be **nyitott**, majd **projekt/megoldás**.
2.  Keresse meg a mappát, ahová menteni szeretné a letöltött Érzelemfelismerési API-fájlokat. Kattintson a **Érzelemfelismerési**, majd **Windows**, majd a **minta-WPF** mappát.
3.  Kattintson duplán a Visual Studio 2015 megoldás (.sln) fájl nevű **EmotionAPI-WPF-Samples.sln**. Ekkor megnyílik a megoldást a Visual Studióban.

## <a name="Step2">2. lépés: A példában létrehozása</a>
1. A **Megoldáskezelőben**, kattintson a jobb gombbal **hivatkozások** válassza **NuGet-csomagok kezelése**.

  ![Nyissa meg a Nuget-Csomagkezelő](../Images/EmotionNuget.png)

2.  A **NuGet-Csomagkezelő** ablak nyílik meg. Először válassza **Tallózás** a bal felső sarokban, majd a keresési mezőbe írja be a "Newtonsoft.Json", válassza ki a **Newtonsoft.Json** csomagot, majd kattintson a **telepítése**.  

  ![Keresse meg a NuGet-csomag](../Images/EmotionNugetBrowse.png)  

3.  Nyomja le a Ctrl + Shift + B, vagy kattintson **Build** menüszalagon válassza a menü, majd **megoldás fordítása**.

## <a name="Step3">3. lépés: A példában futtatása</a>
1.  A létrehozás befejezése után nyomja le az **F5** , vagy kattintson a **Start** a menüszalag menü a példa futtatásához.
2.  Keresse meg a Érzelemfelismerési API-ablakot a **szövegmező** olvasása "**illessze be ide az Előfizetés kulcs elindításához**". Az Előfizetés kulcs illessze be a szövegmezőben Képernyőkép az alábbiak szerint. Az Előfizetés kulcs a számítógépen vagy hordozható megőrizni a "Kulcs mentése" gombra kattintva választhat. Ha törölni szeretné az Előfizetés kulcs a rendszerből, kattintson a "Kulcs törlése" eltávolítása a PC- vagy hordozható.
  
  ![Érzelemfelismerési funkció felület](../Images/EmotionKey.png)

3.  A "**válassza ki a forgatókönyv**"kattintson ide a két említett helyzet bármelyikével"**észleléséhez használja a stream érzelemfelismerési**"vagy"**észleli az egy URL-cím segítségével érzelemfelismerési**", és kövesse az utasításokat a képernyőn. A Microsoft a képek feltöltése, és előfordulhat, hogy felhasználja javítására Érzelemfelismerési API-t és a kapcsolódó szolgáltatások kap. Kép történő elküldésekor által meggyőződött róla, hogy elvégezte a [fejlesztői viselkedési](https://azure.microsoft.com/support/legal/developer-code-of-conduct/).
4.  A mintaalkalmazás használható például képek vannak. Ezeket a lemezképeket található [a Arcfelismerési API Github-tárház](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) alatt a **adatok** mappát. Vegye figyelembe ezeket a lemezképeket használatát licencbe valós használja megállapodás használhat ez a példa tesztelési, de nem újbóli közzétételét azok jelentését.

## <a name="Review">Tekintse át, és ismerje meg</a>
Most, hogy a futó alkalmazások, ossza meg velünk tekintse át a példa az alkalmazás kognitív Microsoft-szolgáltatásokkal integrálja az hogyan. Ez megkönnyíti épület ezt az alkalmazást, vagy a saját Microsoft Érzelemfelismerési API-jával alkalmazás fejlesztése. 

Ez például az alkalmazás lehetővé teszi a Érzelemfelismerési API ügyféloldali kódtár, a dinamikus C# ügyfél burkolót a Microsoft Érzelemfelismerési API-t használja. A fent ismertetett példa app parancsfájlkezelő, ha az ügyféloldali kódtár kapott NuGet-csomagot. Tekintse át a mappában, című ügyféloldali kódtár forráskódját "[ügyféloldali kódtár](https://github.com/Microsoft/Cognitive-Emotion-Windows/tree/master/ClientLibrary)" alatt **Érzelemfelismerési**, **Windows**, **ügyféloldali kódtár** , amely a letöltött fájl tárház részeként fenti a [Előfeltételek](#Prerequisites).
 
Azt is megtudhatja, hogyan használható az ügyféloldali kódtár kód **Megoldáskezelőben**: A **EmotionAPI-WPF_Samples**, bontsa ki a **DetectEmotionUsingStreamPage.xaml** számára Keresse meg **DetectEmotionUsingStreamPage.xaml.cs**, amely egy helyileg tárolt fájlt tallózással szolgál, vagy bontsa ki a **DetectEmotionUsingURLPage.xaml** található  **DetectEmotionUsingURLPage.xaml.cs**, amellyel feltöltésekor a rendszer egy kép URL-CÍMÉT. Kattintson duplán a. Ezek xaml.cs fájlok megnyitása a Visual Studio új windows. 

Vizsgáljuk meg megtekintette a Érzelemfelismerési ügyféloldali kódtár lekérdezi használatáról példa alkalmazásnak, a két kódtöredékek **DetectEmotionUsingStreamPage.xaml.cs** és **DetectEmotionUsingURLPage.xaml.cs**. Minden fájl "Kulcs minta kód KEZDŐDIK itt" és "Kulcs minta kód karakterlánccal végződik-e itt" hogy könnyebben megtalálja az alábbiakban bemutatott kódtöredékek kód jelző kód megjegyzéseket tartalmaz.

A Érzelemfelismerési API dolgozhasson bemenetként kép URL-címe vagy bináris adatok (formájában egy octet-stream). A két lehetőség alatt felülvizsgálata. Mindkét esetben először látnia egy irányelv, amely lehetővé teszi, hogy a Érzelemfelismerési ügyféloldali Kódtárára. 
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

A kódrészletet bemutatja, hogyan nyújt az Előfizetés kulcs és a Érzelemfelismerési API szolgáltatás fénykép URL-CÍMÉT az ügyféloldali kódtár segítségével. 

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

Alább látható, hogyan nyújt az Előfizetés kulcs és a Érzelemfelismerési API-t egy helyileg tárolt lemezképet. 


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
