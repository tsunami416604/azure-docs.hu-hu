---
title: Skála media feldolgozása az Azure portál használatával |} Microsoft Docs
description: Ez az oktatóanyag végigvezeti a méretezési adathordozó feldolgozása az Azure portál használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: juliako
ms.openlocfilehash: 51973916c97282ac93032ab833402d9d1356647e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790313"
---
# <a name="change-the-reserved-unit-type"></a>A fenntartott egység típusának módosítása
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portál](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

> [!NOTE]
> A Java SDK legújabb verziójának beszerzéséhez és a Java-fejlesztés megkezdéséhez tekintse meg [Az Azure Media Services Java ügyfél-SDK használatának megkezdése](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use) című cikket. <br/>
> A legfrissebb Media Services PHP SDK letöltéséhez keresse meg a Microsoft Azure-csomag 0.5.7-es verzióját a [Packagist-adattárban](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

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
   
    A kijelölt fenntartott egységnek típus fenntartott egységek számának módosításához használja a **Media kiszolgált egységek** csúszkát a képernyő tetején.
   
    Módosíthatja a **FENNTARTOTT EGYSÉGTÍPUS**, kattintson a a **fenntartott feldolgozóegység sebessége** sáv. Ezután válassza ki az árképzési szint van szüksége: S1, S2 vagy S3.
   
3. A módosítások mentéséhez kattintson a SAVE (Mentés) gombra.
   
    Az új fenntartott egységek MENTÉS megnyomásakor foglal le.

## <a name="next-steps"></a>További lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

