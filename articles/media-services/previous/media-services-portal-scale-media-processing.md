---
title: Adathordozó-feldolgozás skálázása a Azure Portal használatával | Microsoft Docs
description: Ez az oktatóanyag végigvezeti a médiafájlok feldolgozásának a Azure Portal használatával történő skálázásának lépésein.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "61127541"
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

A Media Services-fiókok Fenntartott egység típussal vannak társítva, amely meghatározza a médiafeldolgozási feladatok feldolgozásának sebességét. A következő fenntartott egység típusok közül választhat: **S1**, **S2**vagy **S3**. Ugyanaz a kódolási feladat például gyorsabban fut, amikor az **S2** Fenntartott egység típust használja az **S1** típus helyett.

A fenntartott egység típusának meghatározása mellett megadhatja, hogy a fiók a **fenntartott egységekkel** (RUs) legyen kiépítve. A megadott Fenntartott egységek száma határozza meg az egy adott fiókon egy időben feldolgozható médiafeladatok számát.

>[!NOTE]
>A Fenntartott egységek az összes médiafeldolgozás párhuzamossá tételéért felelősek, beleértve az Azure Media Indexerrel végzett indexelési feladatokat is. De a kódolással ellentétben az indexelési feladatok feldolgozása nem lesz gyorsabb a gyorsabb Fenntartott egységekkel.

> [!IMPORTANT]
> Tekintse át az [áttekintő](media-services-scale-media-processing-overview.md) témakört, ahol további információkhoz juthat a médiafájlok méretezésével kapcsolatos témakörről.
> 
> 

## <a name="scale-media-processing"></a>Adathordozó-feldolgozás skálázása
A fenntartott egység típusának és a fenntartott egységek számának megváltoztatásához tegye a következőket:

1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. A **Beállítások** ablakban válassza a **Media szolgáltatás számára fenntartott egységek**lehetőséget.
   
    A kijelölt fenntartott egység típusához tartozó fenntartott egységek számának módosításához használja a képernyő tetején található **Media-egységek** csúszkát.
   
    A **fenntartott egység típusának**módosításához kattintson a **fenntartott feldolgozási egységek sávjának sebességére** . Ezután válassza ki a szükséges árképzési szintet: S1, S2 vagy S3.
   
3. A módosítások mentéséhez kattintson a SAVE (Mentés) gombra.
   
    Az új fenntartott egységek kiosztása a Mentés gomb megnyomásakor történik.

## <a name="next-steps"></a>További lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

