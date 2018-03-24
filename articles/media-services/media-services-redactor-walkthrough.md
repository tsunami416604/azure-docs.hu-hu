---
title: Az Azure Médiaelemzés használatával forgatókönyv lapok kivonás |} Microsoft Docs
description: Ez a témakör bemutatja a részletes útmutatást tartalmaz egy Azure Media Services Explorer (AMSE) és az Azure Media Redactor Vizualizálója (nyílt forráskódú eszköz) használatával teljes kivonási munkafolyamat futtatásához.
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
ms.openlocfilehash: 6b33da21a32373196d86ec8c93f180b3d40b1c6e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Az Azure Médiaelemzés használatával forgatókönyv lapok kivonása

## <a name="overview"></a>Áttekintés

**Az Azure Media Redactor** van egy [Azure Médiaelemzés használatával](media-services-analytics-overview.md) media processzor (MP), amely a felhőben méretezhető arcfelismerési kivonási nyújt. Arcfelismerési kivonási lehetővé teszi, hogy a videó ahhoz, hogy a kijelölt személyeket felületei életlenítés módosíthatja. Érdemes lehet nyilvános biztonsági és hírek media helyzetekben használhatja a tapasztalt kivonási szolgáltatás. Több lapokat tartalmazó felvételei, néhány perc múlva a kivonás a manuálisan órát is igénybe vehet, de ezzel a szolgáltatással a tapasztalt kivonási folyamat néhány egyszerű lépésben szükséges. További információkért lásd: [ez](https://azure.microsoft.com/blog/azure-media-redactor/) blog.

Vonatkozó további információért **Azure Media Redactor**, tekintse meg a [Arcfelismerési kivonási áttekintése](media-services-face-redaction.md) témakör.

Ez a témakör bemutatja a részletes útmutatást tartalmaz egy Azure Media Services Explorer (AMSE) és az Azure Media Redactor Vizualizálója (nyílt forráskódú eszköz) használatával teljes kivonási munkafolyamat futtatásához.

További információkért lásd: [ez](https://azure.microsoft.com/en-us/blog/redaction-preview-available-globally) blog.

## <a name="azure-media-services-explorer-workflow"></a>Az Azure Media Services Explorer munkafolyamat

A legegyszerűbb Ismerkedés a Redactor módja a nyílt forráskódú AMSE eszköz használata a githubon. Keresztül egyszerűsített munkafolyamat futtatása **kombinált** mód, ha nincs szüksége a jegyzet json és a tapasztalt jpg képek a hozzáférést.

### <a name="download-and-setup"></a>A letöltés és telepítés

1. Töltse le az AMSE eszköz [Itt](https://github.com/Azure/Azure-Media-Services-Explorer).
1. Jelentkezzen be a Media Services-fiók, a szolgáltatás-kulcs használatával.

    A fiók neve és a legfontosabb információk beszerzéséhez látogasson el az [Azure-portálra](https://portal.azure.com/), és válassza ki AMS-fiókját. Válassza a beállítások > kulcsok. A Kulcsok kezelése ablakban megtalálja a fiók nevét, valamint az elsődleges és másodlagos kulcsot. Másolja ki a fióknév és az elsődleges kulcs értékeit.

![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>Először adja át – mód elemzése

1. A médiafájl keresztül eszköz feltöltés –> feltöltési, sem a fogd és vidd segítségével. 
1. Kattintson a jobb gombbal, és dolgozza fel a médiafájl Médiaelemzés használatával Azure Media Redactor –> –> elemzési módot. 


![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

A kimenet egy jegyzetek json-fájl, arc helyadatok, valamint minden észlelt felületen jpg tartalmazza. 

![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>Második fázis – mód kivonása

1. Az első fázisban a töltse fel az eredeti video asset a kimeneti, és állítsa be elsődleges eszközként. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. (Választható) A kivonás kívánt azonosító Sortöréssel elválasztott listáját tartalmazó "Dance_idlist.txt" fájl feltöltése. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. (Választható) Végezze el a módosításokat a annotations.json fájlba, például a határoló mező határait növelését. 
4. A kimeneti adategységen az első fázisban a kattintson a jobb gombbal, válassza ki a Redactor, futtassa a **Redact** mód. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Töltse le, vagy a végső kivont kimeneti adategységen fájlmegosztás. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Azure Media Redactor Vizualizálója nyílt forráskódú eszköz

Egy nyílt forráskódú [vizualizálója eszköz](https://github.com/Microsoft/azure-media-redactor-visualizer) célja, hogy segítségével a fejlesztők a jegyzetek formátumú elemzése, és a kimeneti használatával indítása.

Után a projekt futtatásához a tárházban klónozását, akkor töltse le a FFMPEG a [hivatalos webhely](https://ffmpeg.org/download.html).

Ha egy fejlesztő a jegyzet JSON-adatok elemzése, nyissa meg Models.MetaData minta kód példákat.

### <a name="set-up-the-tool"></a>Az eszköz beállítása

1.  Töltse le, és a teljes megoldás létrehozásához. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Töltse le a FFMPEG [Itt](https://ffmpeg.org/download.html). Ez a projekt eredetileg fejlesztettek verzió be1d324 (2016-10-04) a statikus hivatkozást. 
3.  A kimeneti mappában, amelyben AzureMediaRedactor.exe ffmpeg.exe és ffprobe.exe másolja. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Futtassa a AzureMediaRedactor.exe. 

### <a name="use-the-tool"></a>Az eszköz használatával

1. A videó az Azure Media Services-fiókban Redactor felügyeleti csomag az elemzési módot a feldolgozni. 
2. Töltse le az eredeti videó fájl- és a kivonási kimenetét, mert a feladat elemzése. 
3. Futtassa a vizualizálója alkalmazást, és válassza ki a fenti fájlok. 

    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Tekintse meg a fájlt. Válassza ki, mely lapok lehetőséget a jobb oldali az oldalsávon keresztül szeretné. 
    
    ![Arcszerkesztés](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  Az alsó szövegmező frissíteni fogja a tapasztalt azonosítók. Ezek az azonosítók "idlist.txt" nevű fájl létrehozása Sortöréssel elválasztott listáját. 

    >[!NOTE]
    > A idlist.txt ANSI kell menteni. ANSI mentéséhez használja a Jegyzettömböt.
    
6.  A feltöltés a kimeneti adategységen az 1. lépésben. Az eredeti videó feltöltése, valamint az ehhez az eszközhöz, és állítsa be elsődleges eszközként. 
7.  Ez az eszköz "Redact" módban fusson kivonási feladat végső beolvasandó kivont videó. 

## <a name="next-steps"></a>További lépések 

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Azure Media Services elemző áttekintése](media-services-analytics-overview.md)

[Az Azure Médiaelemzés bemutatók](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Az Azure Médiaelemzés használatával Arcfelismerési kivonási bejelentése](https://azure.microsoft.com/blog/azure-media-redactor/)
