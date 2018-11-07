---
title: Az Azure Portallal streamvégpontok méretezési |} A Microsoft Docs
description: Ez az oktatóanyag végigvezeti a lépéseken, az Azure Portallal streamvégpontok méretezését.
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
ms.openlocfilehash: fc281f8ca04941da88c0ff1b98ff0a044a3184f5
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51250832"
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>A streamvégpont méretezése az Azure Portal használatával
## <a name="overview"></a>Áttekintés

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

A **prémium** szintű streamvégpontok a speciális feladatokhoz ideálisak, mert dedikált és méretezhető sávszélesség-kapacitást nyújtanak. A **prémium** streamvégponttal rendelkező ügyfelek alapértelmezés szerint kapnak egy adategységet (SU-t). A streamvégpont adategységek hozzáadásával méretezhető. Mindegyik adategység további sávszélesség-kapacitást nyújt az alkalmazásnak. Végponttípusok és a CDN-konfiguráció streameléssel kapcsolatos további információkért lásd: a [folyamatos átviteli végponton áttekintése](media-services-streaming-endpoints-overview.md) témakör.
 
Ez a témakör bemutatja a streamvégpont méretezése.

Az árképzésre vonatkozó további információkért lásd: [Media Services Pricing Details](https://go.microsoft.com/fwlink/?LinkId=275107) (A Media Services árképzésére vonatkozó információk).

## <a name="scale-streaming-endpoints"></a>Az adatfolyam végpontjainak méretezése

A streamelési egységek számának módosításához tegye a következőket:

1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. Az a **beállítások** ablakban válassza **Streamvégpontok**.
3. Kattintson a skálázni kívánt streamvégpontra. 

    > [!NOTE] 
    > Csak méretezheti **prémium** streamvégpontok.

4. Mozgassa a csúszkát a streamelési egységek számának megadásához.

    ![Streamvégpont](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>További lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

