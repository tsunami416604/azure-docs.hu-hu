---
title: Méretezhető médiafeldolgozás az Azure portal használatával |} A Microsoft Docs
description: Ez az oktatóanyag végigvezeti a lépéseken, méretezési médiatartalmak feldolgozásának, az Azure portal használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 810d1bf6a550539b402721d089d8b041cfb48634
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55991453"
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

## <a name="overview"></a>Áttekintés

A Media Services-fiókok Fenntartott egység típussal vannak társítva, amely meghatározza a médiafeldolgozási feladatok feldolgozásának sebességét. A következő fenntartott egység típusok közül választhat: **S1**, **S2**, vagy **S3**. Ugyanaz a kódolási feladat például gyorsabban fut, amikor az **S2** Fenntartott egység típust használja az **S1** típus helyett.

A Fenntartott egység típusának meghatározása mellett megadhatja, hogy ellátja-e a fiókot **Fenntartott egységekkel** (RU-kkal). A megadott Fenntartott egységek száma határozza meg az egy adott fiókon egy időben feldolgozható médiafeladatok számát.

>[!NOTE]
>A Fenntartott egységek az összes médiafeldolgozás párhuzamossá tételéért felelősek, beleértve az Azure Media Indexerrel végzett indexelési feladatokat is. De a kódolással ellentétben az indexelési feladatok feldolgozása nem lesz gyorsabb a gyorsabb Fenntartott egységekkel.

> [!IMPORTANT]
> Mindenképpen tekintse át a [áttekintése](media-services-scale-media-processing-overview.md) médiafeldolgozási témakör méretezésével kapcsolatos további információért a témakörben.
> 
> 

## <a name="scale-media-processing"></a>Méretezhető médiafeldolgozás
Ha módosítani szeretné a fenntartott egység típusának és a szolgáltatás számára fenntartott egységek számát, tegye a következőket:

1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. Az a **beállítások** ablakban válassza **Media szolgáltatás számára fenntartott egységek**.
   
    A kijelölt fenntartott egység típusának számára fenntartott egységek számát módosításához használja a **Media kiszolgált egységek** a képernyő tetején lévő csúszkával.
   
    Módosíthatja a **FENNTARTOTT egység TÍPUSSAL**, kattintson a a **fenntartott feldolgozási egységek sebessége** sáv. Ezután válassza ki a tarifacsomagot van szüksége: S1, S2 vagy S3.
   
3. A módosítások mentéséhez kattintson a SAVE (Mentés) gombra.
   
    Az új szolgáltatás számára fenntartott egységek lefoglalásának, amikor lenyomja a Mentés gombra.

## <a name="next-steps"></a>További lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

