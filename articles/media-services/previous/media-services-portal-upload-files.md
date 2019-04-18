---
title: Fájlok feltöltése Media Services-fiókba az Azure Portalon | Microsoft Docs
description: Ez az oktatóprogram végigvezeti azon lépéseken, amelyek segítségével fájlokat tölthet fel egy Media Services-fiókba az Azure Portalon.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 3ebeb3c601dd3f734265d49d60728056561928be
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58803213"
---
# <a name="upload-files-to-a-media-services-account-in-the-azure-portal"></a>Fájlok feltöltése Media Services-fiókba az Azure Portalon 

> [!div class="op_single_selector"]
> * [Portál](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Próbálja ki a legújabb verziót, ami a [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még [v3 a v2 migrálási útmutató](../latest/migrate-from-v2-to-v3.md)

Az Azure Media Services szolgáltatásban a digitális fájlok feltöltése egy objektumba történik. Az objektum tartalmazhat videót, hangot, képeket, miniatűröket, szövegsávokat és feliratfájlokat (valamint mindezen fájlok metaadatait). A fájlok feltöltése után a tartalom a felhőben lesz biztonságosan tárolva további feldolgozás és streamelés céljából.

A Media Services a fájlok feldolgozása esetében felső fájlméret-korlátozást alkalmaz. További részletek a fájlméret-korlátozásokról: [Media Services – Kvóták és korlátozások](media-services-quotas-and-limitations.md).

Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További részletek: [Ingyenes Azure-próbafiók](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="upload-files"></a>Fájlok feltöltése
1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. Válassza a **Settgings (Beállítások)** > **Assets (Objektumok)** lehetőséget. Ezután válassza ki a **Feltöltés** gombot.
   
    ![Fájlok feltöltése](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    Megjelenik az **Upload a video asset** (Videóobjektum feltöltése) ablak.
   
   > [!NOTE]
   > A Media Services nem korlátozza a feltölteni kívánt videók méretét.
 
3. Keresse meg a számítógépén a feltölteni kívánt videót. Jelölje ki a videót, majd válassza az **OK** lehetőséget.  
   
    Megkezdődik a feltöltés. A folyamat előrehaladását a fájlnév alatt követheti nyomon.  

A feltöltést követően az új objektum megjelenik az **Objektumok** panelen. 

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>További lépések
* Ismerkedjen meg a [feltöltött objektumok kódolásának](media-services-portal-encode.md) módjával.

* Emellett az Azure Functions használatával is elindíthatja a kódolási feladatokat a fájloknak a konfigurált tárolóba történő érkezésekor. További információkért tekintse meg, a minta [Media Services: Az Azure Media Services integrálása Azure Functions és a Logic Apps](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/).


