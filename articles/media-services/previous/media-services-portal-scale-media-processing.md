---
title: Médiafeldolgozás méretezése az Azure portal használatával | Microsoft dokumentumok
description: Ez az oktatóanyag végigvezeti a médiafeldolgozás azure portalhasználatával történő méretezésének lépéseit.
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
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: c840764dc978a8dacb3450c0aca5e5d93284b8a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61127541"
---
# <a name="change-the-reserved-unit-type"></a>A fenntartott egység típusának módosítása
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portál](media-services-portal-scale-media-processing.md)
> * [Többi](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [Php](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Áttekintés

A Media Services-fiókok Fenntartott egység típussal vannak társítva, amely meghatározza a médiafeldolgozási feladatok feldolgozásának sebességét. A következő fenntartott egységtípusok közül választhat: **S1,** **S2**vagy **S3**. Ugyanaz a kódolási feladat például gyorsabban fut, amikor az **S2** Fenntartott egység típust használja az **S1** típus helyett.

A fenntartott egység típusának megadása mellett megadhatja, hogy a fiók a fenntartott egységek (RT) segítségével is kiépítse a **fiókot.** A megadott Fenntartott egységek száma határozza meg az egy adott fiókon egy időben feldolgozható médiafeladatok számát.

>[!NOTE]
>A Fenntartott egységek az összes médiafeldolgozás párhuzamossá tételéért felelősek, beleértve az Azure Media Indexerrel végzett indexelési feladatokat is. De a kódolással ellentétben az indexelési feladatok feldolgozása nem lesz gyorsabb a gyorsabb Fenntartott egységekkel.

> [!IMPORTANT]
> Győződjön meg arról, hogy tekintse át az [áttekintő](media-services-scale-media-processing-overview.md) témakört, hogy további információt kapjon a médiafeldolgozási témakör méretezéséről.
> 
> 

## <a name="scale-media-processing"></a>Médiafeldolgozás méretezése
A lefoglalt egység típusának és a lefoglalt egységek számának módosításához tegye a következőket:

1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. A **Beállítások** ablakban válassza a **Media számára fenntartott egységek lehetőséget.**
   
    A kijelölt lefoglalt egység típusszámára fenntartott egységek számának módosításához használja a képernyő tetején található **Media Served Units** csúszkát.
   
    A **FENNTARTOTT EGYSÉG TÍPUS**módosításához kattintson a lefoglalt **feldolgozóegységek sebességsávjára.** Ezután válassza ki a szükséges tarifacsomagot: S1, S2 vagy S3.
   
3. A módosítások mentéséhez kattintson a SAVE (Mentés) gombra.
   
    Az új lefoglalt egységek a MENTÉS billentyű lenyomásakor kerülnek kiosztásra.

## <a name="next-steps"></a>További lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

