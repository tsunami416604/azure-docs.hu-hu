---
title: Adatfolyam-végpontok és az Azure portál méretezési |} Microsoft Docs
description: Ez az oktatóanyag végigvezeti a intenzív streamvégpontok az Azure portálon.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: juliako
ms.openlocfilehash: f2a14f2622d78a4222a8518172eb1ce8ed9e6637
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>A streamvégpont méretezése az Azure Portal használatával
## <a name="overview"></a>Áttekintés

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

A **prémium** szintű streamvégpontok a speciális feladatokhoz ideálisak, mert dedikált és méretezhető sávszélesség-kapacitást nyújtanak. A **prémium** streamvégponttal rendelkező ügyfelek alapértelmezés szerint kapnak egy adategységet (SU-t). A streamvégpont adategységek hozzáadásával méretezhető. Mindegyik adategység további sávszélesség-kapacitást nyújt az alkalmazásnak. Adatfolyam-típusú végpontok esetében és a CDN konfigurációs kapcsolatos további információkért tekintse meg a [Streaming Endpoint áttekintése](media-services-streaming-endpoints-overview.md) témakör.
 
Ez a témakör bemutatja a streamvégpontján méretezése.

Az árképzésre vonatkozó további információkért lásd: [Media Services Pricing Details](http://go.microsoft.com/fwlink/?LinkId=275107) (A Media Services árképzésére vonatkozó információk).

## <a name="scale-streaming-endpoints"></a>Az adatfolyam végpontjainak méretezése

A streamelési egységek számának megváltoztatásához tegye a következőket:

1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. Az a **beállítások** ablakban válassza ki **adatfolyam-végpontok**.
3. Kattintson a skálázni kívánt streamvégpontra. 

    > [!NOTE] 
    > Csak méretezheti **prémium** adatfolyam-végpontok.

4. Mozgassa a csúszkát adja meg a streamelési egységek számát.

    ![Streamvégpont](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>További lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

