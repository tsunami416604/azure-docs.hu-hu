---
title: Fájlok feltöltése Azure Media Services-fiókba az Aspera használatával | Microsoft Docs
description: Ez az oktatóanyag végigvezeti azon lépéseken, amelyek segítségével fájlokat tölthet fel egy Media Services-fiókhoz társított tárfiókba az Azure **Aspera Server On Demand** szolgáltatásával.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.assetid: 8812623a-b425-4a0f-9e05-0ee6c839b6f9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: aeeacdd5a83ff41eeb900b142e09030b5642f4f8
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55993826"
---
# <a name="upload-files-into-a-media-services-account-using-the-aspera-server-on-demand-service-on-azure"></a>Fájlok feltöltése Media Services-fiókba az Azure Aspera Server On Demand szolgáltatásával 

## <a name="overview"></a>Áttekintés

Az **Aspera** egy nagy sebességű fájlátvitelre szolgáló szoftver. Az Azure **Aspera Server On Demand** szolgáltatása lehetővé teszi a nagy fájlok nagy sebességű fel- és letöltését közvetlenül az Azure Blob objektumtárba. Az **Aspera On Demand** szolgáltatással kapcsolatos további információkért tekintse meg az [Aspera Cloud](http://cloud.asperasoft.com/) webhelyét. 
  
Az Azure-hoz készült **Aspera Server On Demand** megvásárolható az [Azure Marketplace-en](https://azure.microsoft.com/marketplace/). Az Azure-hoz készült **Aspera Server On Demand** megvásárlásához jelentkezzen be az Azure Marketplace-re Windows Live ID-jával.

Ez az oktatóanyag végigvezeti azon lépéseken, amelyek segítségével fájlokat tölthet fel egy Media Services-fiókhoz társított tárfiókba az Azure **Aspera Server On Demand** szolgáltatásával. 

[Itt](https://github.com/Azure-Samples/media-services-dotnet-functions-integration/tree/master/103-aspera-ingest) találhat egy példát, amely bemutatja az Azure Functions, valamint az Aspera és a Media Services együttes használatát.

>[!NOTE]
>Az Azure Media Services médiafeldolgozókkal (MP-k) feldolgozható maximális támogatott fájlméret korlátozva van. A fájlméretre vonatkozó korlátozással kapcsolatban további információt [ebben](media-services-quotas-and-limitations.md) a cikkben talál.
>

## <a name="prerequisites"></a>Előfeltételek 

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Egy Windows Live ID
* Egy [Azure-fiók](https://azure.microsoft.com). További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/). 
* Egy [Azure Media Services-fiók](media-services-portal-create-account.md).

## <a name="purchase-aspera-on-demand-for-azure"></a>Az Azure-hoz készült Aspera On Demand megvásárlása

Ha bejelentkezett az Azure Marketplace-re, hajtsa végre ezeket az alapvető lépéseket az Azure-hoz készült Aspera On Demand megvásárlásához.

1. Keressen rá az Aspera kifejezésre, és válassza a „Server On Demand” lehetőséget.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera001.png)

2. Tekintse át az előfizetési csomagokat, és kattintson a „Regisztráció” gombra.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera002.png)

3. Adja meg a Server on-Demand-előfizetés adatait.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera003.png)

4. Kattintson a **Tarifacsomag** elemre, és válassza ki a kívánt havi mennyiséget az alpanelen. A **Csomag részletei** panelen kattintson az **OK** gombra. Ezután a **Tarifacsomag kiválasztása** panelen kattintson a **Kiválasztás** elemre.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera004.png)

5. Kattintson a **Jogi feltételek** elemre a jogi feltételek megtekintéséhez és elfogadásához az alpanelen. Ha áttekintette a jogi feltételeket, kattintson a **Vásárlás** elemre.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera005.png)

6. A **Létrehozás** gombra kattintva véglegesítse a vásárlást.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera006.png)

7. Az Azure irányítópultján meg fog jelenni, hogy a rendszer üzembe helyezi a szolgáltatást.  Ha a rendszer elvégezte az üzembe helyezést, az új erőforrás megkereséséhez keressen rá a szolgáltatás nevére az erőforrások között. Ha megtalálta a szolgáltatást, kattintson rá duplán a szolgáltatásfelügyeleti portál elindításához.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera007.png)

8. Indítsa el az Aspera felügyeleti portált. Ha megtalálta az új Aspera szolgáltatást, a szolgáltatásra kattintva elérheti a felügyeleti portált.  Ekkor egy új panel jelenik meg. Ezen az új panelen kattintson az új szolgáltatás **erőforrásnevére**.  Az alábbi képernyőképen az erőforrásnév „AsperaTransferDemo”. Ha az erőforrásnévre kattint, megjelenik egy új panel. Az újonnan megjelenő panelen látható a „Felügyelet” hivatkozás. Kattintson a „Felügyelet” hivatkozásra az Aspera felügyeleti portál elindításához.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera008.png)

9. A Felügyelet hivatkozásra kattintva a rendszer átirányítja a regisztrációs lapra, amely a szolgáltatáshoz való hozzáféréshez szükséges.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera009.png)

10. Most már készen áll arra, hogy hozzáférjen az Aspera szolgáltatás felügyeleti portáljához, ahol létrehozhat hozzáférési kulcsokat, letöltheti az Aspera-ügyfeleket és -licenceket, megtekintheti a használatot és megismerheti az API-kat.

    Az alábbi képernyőképen a hozzáférés létrehozása látható. 

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera010.png)

    Az alábbi képernyőképen a használatra vonatkozó jelentések készítéséhez szükséges felületek láthatók. 

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera011.png)

## <a name="upload-files-with-aspera"></a>Fájlok feltöltése az Asperával

1. Töltse le és telepítse az Aspera-ügyfélszoftvert:
    
    * [Böngészőbővítmény](http://downloads.asperasoft.com/connect2/)
    * [Funkciógazdag ügyfél](http://downloads.asperasoft.com/en/downloads/2)

2. Hajtsa végre az első átvitelt. Az Aspera-ügyfelet a következő lépések végrehajtása után használhatja átvitelre az Aspera átviteli szolgáltatással: 

    1. Hozzon létre egy hozzáférési kulcsot az Aspera portálon.  
    2. Töltse le, telepítse, és licencelje az Aspera-ügyfelet (a szoftver az Aspera portálról érhető el).  

    >[!NOTE]
    >A konfigurációs információkért olvassa el az Aspera-ügyfél útmutatóját.
    
    3. Kérdezze le az Azure Media Accounthoz társított tárfiók adatait az [Azure Portal](https://portal.azure.com/) segítségével. Egészen pontosan a nevet és a kulcsot, valamint annak a Storage-blobtárolónak a nevét, ahová a tartalmat helyezni szeretné. 

        * Tárolóadatok lekérdezése a portálról: keresse meg a tárfiókot, kattintson a hozzáférési kulcsokra, és másolja a fiók nevét és kulcsát.
        * A tároló nevének lekérdezése: keresse meg a tárfiókot, válassza a **Blobok** lehetőséget, majd annak a tárolónak a nevét, ahová a tartalmat fel kívánja tölteni. 

    Az alábbi képernyőképen az Aspera-ügyfél **Csatlakozáskezelője** látható, ahol meg kell adnia az Azure Storage-szolgáltatástípust, a hitelesítő adatokat, valamint a blobtárolót.

    ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera012.png)

## <a name="resources"></a>További források

A következő források vannak megemlítve ebben a cikkben. 

* [Böngészőbővítmény csatlakoztatása](http://downloads.asperasoft.com/connect2/)
* [Csatlakoztatási útmutató](http://downloads.asperasoft.com/en/documentation/8)
* [Aspera-ügyfél](http://downloads.asperasoft.com/en/downloads/2)
* [Ügyfélútmutató](http://downloads.asperasoft.com/en/documentation/2)

## <a name="next-steps"></a>További lépések

Most már tudja, hogyan [másolhat blobokat egy tárfiókból egy AMS-fiókba](media-services-copying-existing-blob.md#copy-blobs-from-a-storage-account-into-an-ams-account).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

