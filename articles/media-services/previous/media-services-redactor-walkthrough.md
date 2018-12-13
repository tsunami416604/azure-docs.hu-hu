---
title: Az Azure Media Analytics bemutató arcok szereplők |} A Microsoft Docs
description: Ez a témakör részletes útmutatást az Azure Media Services Explorer (AMSE) és az Azure Media Redactor Vizualizálója (nyílt forráskódú eszköz) teljes kivonási munkafolyamat futtatása jeleníti meg.
services: media-services
documentationcenter: ''
author: Lichard
manager: cfowler
editor: ''
ms.assetid: d6fa21b8-d80a-41b7-80c1-ff1761bc68f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/03/2017
ms.author: rli; juliako;
ms.openlocfilehash: 81618446930a23a7ea713da19bb7c63a06d135ed
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53162439"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Az Azure Media Analytics bemutató arcok szereplők

## <a name="overview"></a>Áttekintés

**Az Azure Media Redactor** van egy [Azure Media Analytics](media-services-analytics-overview.md) médiafeldolgozót. (pont) által kínált méretezhető arcszerkesztés a felhőben. Arcszerkesztés lehetővé teszi, hogy a videó módosíthatja annak érdekében, hogy arcának elmossa. Előfordulhat, hogy szeretné használni a face kivonási szolgáltatás közbiztonsági megfontolásokból és sajtóbeli híradásoknál. Néhány perc alatt több arcokat tartalmazó képanyag manuálisan szereplők órát is igénybe vehet, de ezzel a szolgáltatással a face kivonási folyamat néhány egyszerű lépéssel van szükség. További információkért lásd: [ez](https://azure.microsoft.com/blog/azure-media-redactor/) blog.

További információk **Azure Media Redactor**, lásd: a [Face kivonási áttekintése](media-services-face-redaction.md) témakör.

Ez a témakör részletes útmutatást az Azure Media Services Explorer (AMSE) és az Azure Media Redactor Vizualizálója (nyílt forráskódú eszköz) teljes kivonási munkafolyamat futtatása jeleníti meg.

További információkért lásd: [ez](https://azure.microsoft.com/blog/redaction-preview-available-globally) blog.

## <a name="azure-media-services-explorer-workflow"></a>Az Azure Media Services Explorer munkafolyamat

Redactor használatának legegyszerűbb módja, hogy a nyílt forráskódú AMSE eszköz használata a Githubon. Egy egyszerűsített munkafolyamat futtatása **kombinált** mód, ha már nincs szüksége a jegyzet json vagy a face jpg-lemezképek elérését.

### <a name="download-and-setup"></a>Letöltés és telepítés

1. Töltse le az AMSE eszköz a [Itt](https://github.com/Azure/Azure-Media-Services-Explorer).
1. Jelentkezzen be a Media Services-fiók a szolgáltatáskulcs használatával.

    A fiók neve és a legfontosabb információk beszerzéséhez látogasson el az [Azure-portálra](https://portal.azure.com/), és válassza ki AMS-fiókját. Ezt követően válassza a beállítások > kulcsok. A Kulcsok kezelése ablakban megtalálja a fiók nevét, valamint az elsődleges és másodlagos kulcsot. Másolja ki a fióknév és az elsődleges kulcs értékeit.

![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>Először adja át – mód elemzése

1. A médiafájl keresztül eszköz feltöltési tekintse feltölthet, fogd és vidd keresztül. 
1. Kattintson a jobb gombbal, és feldolgozása a médiafájl Médiaelemzés használatával tekintse meg az Azure Media Redactor tekintse elemzés mód. 


![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

A kimenet tartalmazza a face helyadatok-jegyzetek json-fájlt, valamint az egyes észlelt face jpg. 

![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>A második fázis – szereplők mód

1. Töltse fel az eredeti videó eszköz a kimenetbe az első fázis a, és állítsa be elsődleges eszközként. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. (Nem kötelező) Töltse fel egy "Dance_idlist.txt" fájlt, amely a kívánt szereplők azonosítók sortöréssel tagolt listáját tartalmazza. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. (Nem kötelező) Győződjön meg arról, módosítani szeretné a annotations.json fájlba, például a határoló mező határait növelése. 
4. Kattintson a jobb gombbal az első fázis, a kimeneti objektum, a Redactor válassza ki, futtassa a **Redact** mód. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Töltse le, vagy megoszthatja a végső kivonatosan kimeneti adategység. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Az Azure Media Redactor Vizualizálója nyílt forráskódú eszköz

Egy nyílt forráskódú [vizualizálója eszköz](https://github.com/Microsoft/azure-media-redactor-visualizer) célja, hogy segítségével a fejlesztők most használja először a jegyzetek formátumban, elemzés, és a kimeneti használatával.

Klónozza a tárházat, annak érdekében, hogy futtassa a projektet, után kell letölteni az FFMPEG azok [hivatalos webhely](https://ffmpeg.org/download.html).

Ha egy fejlesztő történt a jegyzet JSON-adatok elemzése, keressen belül Models.MetaData minta hitelesítésikód-példák.

### <a name="set-up-the-tool"></a>Az eszköz beállítása

1.  Töltse le, és a teljes megoldás létrehozásához. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Töltse le az FFMPEG [Itt](https://ffmpeg.org/download.html). Ez a projekt eredetileg fejlesztettek verzió be1d324 (2016-10-04) a statikus hivatkozást. 
3.  Ffmpeg.exe és ffprobe.exe AzureMediaRedactor.exe azonos kimeneti mappába másolja. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Futtassa a AzureMediaRedactor.exe. 

### <a name="use-the-tool"></a>Az eszköz használata

1. Az Azure Media Services-fiók a Redactor felügyeleti csomag az elemzés módtól függ a videó feldolgozása. 
2. Töltse le az eredeti videó fájlt és a a kivonási kimenete – elemzését a feladatot. 
3. Futtassa az vizualizálója alkalmazást, és válassza ki a fenti fájlok. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Tekintse meg a fájlt. Válassza ki azt az arcokat akkor, ha elmossa keresztül a jobb oldalon az oldalsávon. 
    
    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  Az alsó szövegmező frissíteni fogja a Face azonosítók. Hozzon létre egy "idlist.txt" nevű fájlt egy sortöréssel tagolt lista formájában azonosítóit a részletekben. 

    >[!NOTE]
    > A idlist.txt ANSI mentése. Használhatja a Jegyzettömböt ANSI mentéséhez.
    
6.  Töltse fel ezt a fájlt az 1. a kimeneti adategység. Az eredeti videó feltöltése, valamint az ehhez az eszközhöz, és állítsa be elsődleges eszközként. 
7.  Kivonási feladatok futtatása az adategység "Redact" mód, a végső get kivont videó. 

## <a name="next-steps"></a>További lépések 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Az Azure Media Services analitikai funkcióinak áttekintése](media-services-analytics-overview.md)

[Az Azure Médiaelemzés bemutatók](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Az Azure Médiaelemzés használatával Arcszerkesztés bejelentése](https://azure.microsoft.com/blog/azure-media-redactor/)
