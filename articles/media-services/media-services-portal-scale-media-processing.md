---
title: "Skála media feldolgozása az Azure portál használatával |} Microsoft Docs"
description: "Ez az oktatóanyag végigvezeti a méretezési adathordozó feldolgozása az Azure portál használatával."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: juliako
ms.openlocfilehash: 46ca29d3e66701f2abcb185791089e94761984e8
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="change-the-reserved-unit-type"></a>A fenntartott egység típusának módosítása
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Áttekintés

A Media Services-fiókok Fenntartott egység típussal vannak társítva, amely meghatározza a médiafeldolgozási feladatok feldolgozásának sebességét. A következő Fenntartott egység típusok közül választhat: **S1**, **S2** vagy **S3**. Ugyanaz a kódolási feladat például gyorsabban fut, amikor az **S2** Fenntartott egység típust használja az **S1** típus helyett.

A Fenntartott egység típusának meghatározása mellett megadhatja, hogy ellátja-e a fiókot **Fenntartott egységekkel** (RU-kkal). A megadott Fenntartott egységek száma határozza meg az egy adott fiókon egy időben feldolgozható médiafeladatok számát.

>[!NOTE]
>A Fenntartott egységek az összes médiafeldolgozás párhuzamossá tételéért felelősek, beleértve az Azure Media Indexerrel végzett indexelési feladatokat is. De a kódolással ellentétben az indexelési feladatok feldolgozása nem lesz gyorsabb a gyorsabb Fenntartott egységekkel.

> [!IMPORTANT]
> Mindenképpen tekintse át a [áttekintése](media-services-scale-media-processing-overview.md) témakör feldolgozása media méretezésével kapcsolatos további információkat a témakör.
> 
> 

## <a name="scale-media-processing"></a>Méretezhető médiafeldolgozás
A fenntartott egységnek típusát és fenntartott egységek számának megváltoztatásához tegye a következőket:

1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. Az a **beállítások** ablakban válassza ki **Media szolgáltatás számára fenntartott egység**.
   
    A kijelölt fenntartott egységnek típus fenntartott egységek számának módosításához használja a **Media kiszolgált egységek** csúszkát.
   
    Módosíthatja a **FENNTARTOTT EGYSÉGTÍPUS**, nyomja le az S1, S2 vagy S3.
   
    ![Feldolgozók lap](./media/media-services-portal-scale-media-processing/media-services-scale-media-processing.png)
3. A módosítások mentéséhez kattintson a SAVE (Mentés) gombra.
   
    Az új fenntartott egységek MENTÉS megnyomásakor foglal le.

## <a name="next-steps"></a>Következő lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

