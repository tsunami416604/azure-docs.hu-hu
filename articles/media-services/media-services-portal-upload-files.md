---
title: " Fájlok feltöltése Media Services-fiókba az Azure Portal használatával | Microsoft Docss"
description: "Ez az oktatóprogram végigvezeti azon lépéseken, amelyek segítségével fájlokat tölthet fel egy Media Services-fiókba az Azure Portal használatával."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/13/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: ed8ea30b87c8086d41cab879acce82062f08b31c
ms.openlocfilehash: f27ab42ab3c7c704804b9a5493c8b3acd954decb


---
# <a name="upload-files-into-a-media-services-account-using-the-azure-portal"></a>Fájlok feltöltése Media Services-fiókba az Azure Portal használatával
> [!div class="op_single_selector"]
> * [Portál](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 
> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/). 
> 


A Media Services szolgáltatásban a digitális fájlok feltöltése egy adategységbe történik. Az Adategység tartalmazhat videót, hangot, képeket, miniatűröket, szövegsávokat és feliratfájlokat (valamint mindezen fájlok metaadatait). A fájlok feltöltése után a tartalom a felhőben lesz biztonságosan tárolva további feldolgozás és adatfolyam-továbbítás céljából.


## <a name="upload-files"></a>Fájlok feltöltése

>[!NOTE]
>A Media Services által feldolgozható maximális támogatott fájlméret korlátozott. A fájlméretre vonatkozó korlátozással kapcsolatban további információt [ebben](media-services-quotas-and-limitations.md) a témakörben talál.
>

1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. A **Beállítások** panelen kattintson az **Objektumok** elemre.
   
    ![Fájlok feltöltése](./media/media-services-portal-vod-get-started/media-services-upload.png)
3. Kattintson az **Upload** (Feltöltés) gombra.
   
    Megjelenik az **Upload a video asset** (Videóobjektum feltöltése) ablak.
   
   > [!NOTE]
   > Tetszőleges méretű fájlt választhat.
   > 
   > 
4. Keresse meg a kívánt videót a számítógépen, jelölje ki, majd kattintson az OK gombra.  
   
    Megkezdődik a feltöltés. A folyamat előrehaladását a fájlnév alatt követheti nyomon.  

A feltöltés befejezését követően az új objektum bekerül az **Assets** (Objektumok) ablakban található listába. 

## <a name="next-steps"></a>Következő lépések
Most már kódolhatja a feltöltött adategységeket. További információ: [Encode Assets](media-services-portal-encode.md) (Adategységek kódolása).

Emellett az Azure Functions használatával is elindíthatja a kódolási feladatokat a konfigurált tárolóba érkező fájlok alapján. További információkért tekintse meg [ezt a mintát](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Feb17_HO2-->


