---
title: Arcok kivonása az Azure Media Analytics forgatókönyvével | Microsoft dokumentumok
description: Ez a témakör lépésről lépésre bemutatja, hogyan futtatható a teljes kivonási munkafolyamat az Azure Media Services Explorer (AMSE) és az Azure Media Redactor Visualizer (nyílt forráskódú eszköz) használatával.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69997681"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Arcok kivonása az Azure Media Analytics forgatókönyvével

## <a name="overview"></a>Áttekintés

**Az Azure Media Redactor** egy [Azure Media Analytics-médiaprocesszor](media-services-analytics-overview.md) (MP), amely skálázható arckivonást kínál a felhőben. Az arckivonás lehetővé teszi a videó módosítását, hogy elhomályosíthassa a kiválasztott személyek arcát. Érdemes lehet használni a face redaction szolgáltatás a közbiztonság és a hírmédia forgatókönyvek. Néhány percnyi, több arcot tartalmazó felvétel manuális kivonása órákba telhet, de ezzel a szolgáltatással az arckivonási folyamat csak néhány egyszerű lépést igényel. További információ: [ebbe a](https://azure.microsoft.com/blog/azure-media-redactor/) blogba.

Az **Azure Media Redactor-ról**a [Face kivonását áttekintő](media-services-face-redaction.md) témakörben olvashat részletesen.

Ez a témakör lépésről lépésre bemutatja, hogyan futtatható a teljes kivonási munkafolyamat az Azure Media Services Explorer (AMSE) és az Azure Media Redactor Visualizer (nyílt forráskódú eszköz) használatával.

További információ: [ebbe a](https://azure.microsoft.com/blog/redaction-preview-available-globally) blogba.

## <a name="azure-media-services-explorer-workflow"></a>Az Azure Media Services Explorer munkafolyamata

A Redactor használatának legegyszerűbb módja a nyílt forráskódú AMSE eszköz használata a GitHubon. Egyszerűsített munkafolyamatot futtathat **kombinált** módban, ha nincs szüksége a json vagy az arcjpg képekhez való hozzáférésre.

### <a name="download-and-setup"></a>Letöltés és beállítás

1. Töltse le az AMSE for AMS v2 eszközt [innen.](https://aka.ms/amseforv2)
1. Jelentkezzen be Media Services-fiókjába a szolgáltatáskulcs használatával.

    A fiók neve és a legfontosabb információk beszerzéséhez látogasson el az [Azure-portálra](https://portal.azure.com/), és válassza ki AMS-fiókját. Ezután válassza a Beállítások > a Billentyűk lehetőséget. A Kulcsok kezelése ablakban megtalálja a fiók nevét, valamint az elsődleges és másodlagos kulcsot. Másolja ki a fióknév és az elsődleges kulcs értékeit.

![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>Első lépés – elemzési mód

1. Töltse fel a médiafájlt az Asset –> Upload, vagy drag and drop segítségével. 
1. Kattintson a jobb gombbal, és dolgozza fel a médiafájlt a Media Analytics –> Azure Media Redactor –> Analyze módban. 


![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

A kimenet tartalmazni fog egy jegyzeteket json fájlt arc helyadatokkal, valamint egy jpg minden észlelt arc. 

![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>Második menet – kivonási mód

1. Töltse fel az eredeti videóeszközt az első lépés kimenetére, és állítsa be elsődleges eszközként. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. (Nem kötelező) Töltsön fel egy "Dance_idlist.txt" fájlt, amely tartalmazza a kivonásra kívánt azonosítók újvonalú, tagolt listáját. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. (Nem kötelező) A jegyzetek.json fájl bármilyen szerkesztését, például növelje a határolókeret határait. 
4. Kattintson a jobb gombbal a kimeneti eszköz az első lépés, válassza ki a Redactor, és futtassa a **Kivonás** módban. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Töltse le vagy ossza meg a végső szerkesztett kimeneti eszközt. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Az Azure Media Redactor Visualizer nyílt forráskódú eszköze

A nyílt forráskódú [vizualizátor eszköz](https://github.com/Microsoft/azure-media-redactor-visualizer) célja, hogy segítse a fejlesztők csak most kezdődik a jegyzetek formátumban elemzés és a kimenet használata.

Miután klónozta a repo, annak érdekében, hogy futtassa a projektet, akkor le kell töltenie FFMPEG a [hivatalos honlapján](https://ffmpeg.org/download.html).

Ha ön egy fejlesztő megpróbálja elemezni a JSON jegyzetelési adatokat, tekintse meg a Models.MetaData mintakód példákat.

### <a name="set-up-the-tool"></a>Az eszköz beállítása

1.  Töltse le és építse fel a teljes megoldást. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Letöltés FFMPEG [innen](https://ffmpeg.org/download.html). Ezt a projektet eredetileg a be1d324 (2016-10-04) változattal fejlesztették ki statikus összekapcsolásával. 
3.  Másolja az ffmpeg.exe fájlt és az ffprobe.exe fájlt ugyanabba a kimeneti mappába, mint az AzureMediaRedactor.exe. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Futtassa az AzureMediaRedactor.exe futtatását. 

### <a name="use-the-tool"></a>Az eszköz használata

1. Dolgozza fel a videót az Azure Media Services-fiókban a Redactor MP elemzési módban. 
2. Töltse le mind az eredeti videofájlt, mind a Kivonás - Elemzés feladat kimenetét. 
3. Futtassa a megjelenítő alkalmazást, és válassza ki a fenti fájlokat. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Tekintse meg a fájl előnézetét. Válassza ki, hogy mely arcokat szeretné elhomályosítani a jobb oldali oldalsávon keresztül. 
    
    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  Az alsó szövegmező a lapazonosítókkal frissül. Hozzon létre egy "idlist.txt" nevű fájlt ezekkel az azonosítókkal, mint újvonalú tagolt listát. 

    >[!NOTE]
    > Az idlist.txt fájlt az ANSI fájlba kell menteni. A jegyzettömb segítségével mentheti az ANSI-t.
    
6.  Töltse fel ezt a fájlt a kimeneti eszközre az 1. Töltsd fel az eredeti videót erre az eszközre is, és állítsd be elsődleges eszközként. 
7.  Futtassa a Kivonási feladatot ezen az eszközön a "Kivonás" móddal a végleges szerkesztett videó leszavazásához. 

## <a name="next-steps"></a>További lépések 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Az Azure Media Services Analytics áttekintése](media-services-analytics-overview.md)

[Az Azure Media Analytics bemutatói](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Face redaction bejelentése az Azure Media Analytics számára](https://azure.microsoft.com/blog/azure-media-redactor/)
