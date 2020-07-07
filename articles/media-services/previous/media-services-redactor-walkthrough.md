---
title: Arcok kivonása a Azure Media Analytics walkthroughvel | Microsoft Docs
description: Ez a témakör részletesen bemutatja, hogyan futtathat teljes kivonási munkafolyamatot a Azure Media Services Explorer (AMSE) és a Azure Media Redactor megjelenítő (nyílt forráskódú eszköz) használatával.
services: media-services
documentationcenter: ''
author: Lichard
manager: femila
editor: ''
ms.assetid: d6fa21b8-d80a-41b7-80c1-ff1761bc68f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: ril
ms.reviewer: juliako
ms.openlocfilehash: a8db8de6ef062dcf757f3d264379677d6550ea3a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "69997681"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Arcok kivonása Azure Media Analytics bemutatóval

## <a name="overview"></a>Áttekintés

A **Azure Media redactor** egy [Azure Media Analytics](media-services-analytics-overview.md) Media Processor (mp), amely méretezhető arc-kivonást biztosít a felhőben. Az Arcfelismerés lehetővé teszi a videó módosítását, hogy a kijelölt személyek ne legyenek elmosódottak. Érdemes lehet a Face kivonási szolgáltatást használni a közbiztonság és a hírek adathordozóján. Néhány perces felvétel, amely több arcot tartalmaz, manuálisan is eltarthat, de ezzel a szolgáltatással az arc-kivonási folyamat csak néhány egyszerű lépést igényelhet. További információkért tekintse meg [ezt a](https://azure.microsoft.com/blog/azure-media-redactor/) blogot.

A **Azure Media redactor**kapcsolatos részletekért tekintse meg a [Face kivonások áttekintése](media-services-face-redaction.md) című témakört.

Ez a témakör részletesen bemutatja, hogyan futtathat teljes kivonási munkafolyamatot a Azure Media Services Explorer (AMSE) és a Azure Media Redactor megjelenítő (nyílt forráskódú eszköz) használatával.

További információkért tekintse meg [ezt a](https://azure.microsoft.com/blog/redaction-preview-available-globally) blogot.

## <a name="azure-media-services-explorer-workflow"></a>Azure Media Services Explorer-munkafolyamat

A redactor első lépéseinek legegyszerűbb módja a nyílt forráskódú AMSE eszköz használata a GitHubon. Az egyszerűsített munkafolyamatokat **összevont** módban is futtathatja, ha nincs szüksége a jegyzet JSON vagy az arc jpg-képek elérésére.

### <a name="download-and-setup"></a>Letöltés és telepítés

1. Töltse le a AMSE for AMS v2 eszközt [innen](https://aka.ms/amseforv2).
1. Jelentkezzen be Media Services-fiókjába a szolgáltatás kulcsa alapján.

    A fiók neve és a legfontosabb információk beszerzéséhez látogasson el az [Azure-portálra](https://portal.azure.com/), és válassza ki AMS-fiókját. Ezután válassza a beállítások > kulcsok elemet. A Kulcsok kezelése ablakban megtalálja a fiók nevét, valamint az elsődleges és másodlagos kulcsot. Másolja ki a fióknév és az elsődleges kulcs értékeit.

![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>Első lépés – elemzési mód

1. Töltse fel a médiafájlt az eszköz – > feltöltés vagy a fogd és vidd használatával. 
1. Kattintson a jobb gombbal, és dolgozza fel a médiafájlt Media Analytics – > Azure Media Redactor – > elemzése mód használatával. 


![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

A kimenet tartalmazni fog egy széljegyzeteket tartalmazó JSON-fájlt, amely a Face Location adatokat, valamint az összes észlelt arc jpg-fájlját tartalmazza. 

![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>Második pass – kivonási mód

1. Töltse fel az eredeti video-eszközét az első menetből származó kimenetre, és állítsa be elsődleges eszközként. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. Választható Töltsön fel egy "Dance_idlist.txt" fájlt, amely tartalmazza a kitakarni kívánt azonosítók sortöréssel tagolt listáját. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. Választható Szerkessze a annotations.jsa fájlban, például növelje a határolókeret határait. 
4. Kattintson a jobb gombbal a kimeneti eszközre az első lépésből, válassza ki a redactor, és futtassa a **kivonási** módot. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Töltse le vagy ossza meg a végső kivont kimeneti eszközt. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Azure Media Redactor megjelenítő nyílt forráskódú eszköz

A nyílt forráskódú [megjelenítő eszköz](https://github.com/Microsoft/azure-media-redactor-visualizer) úgy lett kialakítva, hogy segítse a fejlesztőket az elemzések és a kimenet használatával.

A tárház klónozása után a projekt futtatásához le kell töltenie az FFMPEG-t a [hivatalos webhelyről](https://ffmpeg.org/download.html).

Ha a fejlesztők a JSON-megjegyzési adatokat szeretnék elemezni, tekintse meg a modelleket. a mintakód példáinak metaadatait.

### <a name="set-up-the-tool"></a>Az eszköz beállítása

1.  Töltse le és hozza létre a teljes megoldást. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Az FFMPEG-t [innen](https://ffmpeg.org/download.html)töltheti le. A projekt eredetileg a be1d324 (2016-10-04) verzióval lett kifejlesztve statikus összekapcsolással. 
3.  Másolja ffmpeg.exe és ffprobe.exe ugyanarra a kimeneti mappára, mint AzureMediaRedactor.exe. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. AzureMediaRedactor.exe futtatása. 

### <a name="use-the-tool"></a>Az eszköz használata

1. A videót feldolgozhatja a Azure Media Services-fiókjában az redactor MP elemzés módban. 
2. Töltse le az eredeti videofájl és a kivonási elemzési feladatok kimenetét. 
3. Futtassa a megjelenítő alkalmazást, és válassza ki a fenti fájlokat. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. A fájl előnézete. Válassza ki, hogy mely arcok legyenek elmosódottak a jobb oldalon található oldalsávon keresztül. 
    
    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  Az alsó szövegmező a Face ID-val fog frissülni. Hozzon létre egy "idlist.txt" nevű fájlt ezekkel az azonosítókkal egy sortöréssel tagolt listaként. 

    >[!NOTE]
    > A idlist.txtt ANSI-ben kell menteni. A Jegyzettömb használatával mentheti az ANSI-t.
    
6.  Töltse fel ezt a fájlt a kimeneti eszközre az 1. lépésben. Töltse fel az eredeti videót az eszközre, és állítsa be elsődleges eszközként. 
7.  A kivonási feladatoknak az objektumon való futtatása "kivonás" módban a végső kitakart videó lekéréséhez. 

## <a name="next-steps"></a>További lépések 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Azure Media Services Analytics áttekintése](media-services-analytics-overview.md)

[Azure Media Analytics bemutatók](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Az Azure Media Analytics arc kivonásának bejelentése](https://azure.microsoft.com/blog/azure-media-redactor/)
