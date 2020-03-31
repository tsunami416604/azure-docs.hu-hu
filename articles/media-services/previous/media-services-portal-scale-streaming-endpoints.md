---
title: Streamelési végpontok méretezése az Azure Portalon | Microsoft dokumentumok
description: Ez az oktatóanyag végigvezeti a streamelési végpontok az Azure Portalon való méretezésének lépéseit.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 23eb51428dcf4961febfb592bf957bb8beeeda57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463114"
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>A streamvégpont méretezése az Azure Portal használatával
## <a name="overview"></a>Áttekintés

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

A **prémium** szintű streamvégpontok a speciális feladatokhoz ideálisak, mert dedikált és méretezhető sávszélesség-kapacitást nyújtanak. A **prémium** streamvégponttal rendelkező ügyfelek alapértelmezés szerint kapnak egy adategységet (SU-t). A streamvégpont adategységek hozzáadásával méretezhető. Mindegyik adategység további sávszélesség-kapacitást nyújt az alkalmazásnak. A végponttípusok streameléséről és a CDN-konfigurációról a [Streamelési végpont áttekintése témakörben olvashat bővebben.](media-services-streaming-endpoints-overview.md)
 
Ez a témakör bemutatja, hogyan skálázható egy streamelési végpont.

Az árképzésrészleteiről a [Media Services díjszabási részletei című témakörben talál](https://go.microsoft.com/fwlink/?LinkId=275107)további információt.

## <a name="scale-streaming-endpoints"></a>Streamelési végpontok méretezése

A streamelési egységek számának módosításához tegye a következőket:

1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. A **Beállítások** ablakban válassza a **Streamelési végpontok**lehetőséget.
3. Kattintson a méretezéshez kívánt streamelési végpontra. 

    > [!NOTE] 
    > Csak prémium **szintű** streamelési végpontok skálázható.

4. A csúszka mozgatása a streamelési egységek számának megadásához.

    ![Streamvégpont](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>További lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

