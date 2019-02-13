---
title: Azure Media Services-fiók létrehozása az Azure Portal használatával | Microsoft Docs
description: Ez az oktatóanyag végigvezeti azokon a lépéseken, amelyek segítségével Azure Media Services-fiókot hozhat létre az Azure Portal használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: d5b3af2b950dd33c797af874d56806de8049b358
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210679"
---
# <a name="create-a-media-services-account-using-the-azure-portal"></a>Hozzon létre egy Media Services-fiókot az Azure portal használatával

Az Azure Portalon gyorsan létrehozhat egy Azure Media Services- (AMS-) fiókot. A fiókkal hozzáférhet olyan Media Services-szogáltatásokhoz, amelyekkel médiatartalmakat tárolhat, titkosíthat, kódolhat, kezelhet és továbbíthat az Azure környezetben. Amikor hoz létre egy Media Services-fiókot Ön is társított storage-fiók létrehozása (vagy használjon egy meglévőt). Egy Media Services-fiók törlésekor a kapcsolódó tárfiókban található blobok nem törlődnek.

Az elsődleges tárfiók lehet általános célú v1 vagy általános célú v2. Az Azure Portal jelenleg csak a v1 kiválasztását támogatja, de hozzáadhatja a v2-t is, ha API-val vagy PowerShell-lel hoz létre fiókot. További információ a tárolási típusokról: [Tudnivalók az Azure Storage-fiókokról](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

A Media Services-fiók és a társított tárfiókok az Azure-előfizetéshez kell lennie. Erősen ajánlott a storage-fiókok használata a Media Services-fiók ugyanazon a helyen a további késleltetés és az adatok kimenő adatforgalmi költségek elkerülése érdekében.

Ez a cikk bemutatja, hogyan hozhat létre a Media Services-fiókba az Azure portal használatával.

> [!NOTE]
> A különböző régiókban lévő Azure Media Services-funkciók elérhetőségéről további információért lásd [az AMS-funkciók elérhetőségét az egyes adatközpontokban](scenarios-and-availability.md#availability).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="create-an-ams-account"></a>AMS-fiók létrehozása

A jelen szakaszban ismertetett lépések bemutatják az AMS-fiók létrehozásának módját.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a **+New** > **Web + Mobile** > **Media Services** elemre.
   
    ![Media Services, létrehozás](./media/media-services-create-account/media-services-new1.png)
3. A **CREATE MEDIA SERVICES ACCOUNT** (Media Services-fiók létrehozása) részben adja meg a kívánt értékeket.
   
    ![Media Services, létrehozás](./media/media-services-create-account/media-services-new3.png)
   
   1. Az **Account Name** (Fiók neve) mezőben adja meg az új AMS-fiók nevét. A Media Services-fiók neve csak számokat és kisbetűket tartalmazhat, nem tartalmazhat szóközöket, és 3–24 karakterből állhat.
   2. A Subscription (Előfizetés) résznél válasszon az elérhető Azure-előfizetések közül.
   3. A **Resource Group** (Erőforráscsoport) résznél válasszon egy új vagy meglévő erőforrást.  Az erőforráscsoport közös életciklussal, engedélyekkel és házirendekkel rendelkező erőforrások gyűjteménye. További információkat [itt](../../azure-resource-manager/resource-group-overview.md#resource-groups) talál.
   4. A **Location** (Hely) részben válassza ki azt a földrajzi régiót, amelyben tárolni fogja a Media Services-fiókhoz tartozó médiafájlokat és metaadat-bejegyzéseket. A rendszer e régió alapján fogja feldolgozni, illetve streamelni a médiafájlokat. A legördülő listában csak a Media Services szolgáltatásban elérhető régiók jelennek meg. 
   5. A **Storage Account** (Tárfiók) résznél válasszon egy tárfiókot, amely Blob Storage tárolót fog biztosítani a Media Services-fiókhoz tartozó médiatartalmak számára. Választhat, hogy egy meglévő, a Media Services-fiókkal azonos földrajzi régióban található tárfiókot használ, vagy létrehoz egy másik tárfiókot. Az újonnan létrehozott tárfiókok ugyanabban a régióban jönnek létre. A tárfiók nevére ugyanazok a szabályok vonatkoznak, mint a Media Services-fiókok nevére.
      
       További információkat a tárhelyről [itt](../../storage/common/storage-introduction.md) talál.
   6. A fióklétrehozás előrehaladásának megtekintéséhez kattintson a **Rögzítés az irányítópulton** elemre.
4. Kattintson az űrlap alján található **Létrehozás** lehetőségre.
   
    A fiók sikeres létrehozása után betöltődik az áttekintési oldal. A streamvégpont-táblázatban a fiók alapértelmezett streamvégpontja **Leállítva** állapotban lesz. 

    >[!NOTE]
    >Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 
   
## <a name="to-manage-your-ams-account"></a>Az AMS-fiók kezelése

Az AMS-fiók kezeléséhez (például az AMS API-hoz való programozott kapcsolódáshoz, videók feltöltéséhez, objektumok kódolásához, tartalomvédelem konfigurálásához, a feladatok előrehaladásának figyeléséhez) válassza a portál bal oldalán lévő **Beállítások** elemet. Az a **beállítások**, keresse meg az egyik elérhető panelt (például: **API-hozzáférés**, **eszközök**, **feladatok**, **Content protection szolgáltatása**).


## <a name="next-steps"></a>További lépések

Most már feltölthet fájlokat AMS-fiókjába. További információk: [Fájlok feltöltése](media-services-portal-upload-files.md).

Ha programozott módon szeretné elérni az AMS API-t, lásd [az Azure Media Services Azure AD-hitelesítéssel történő elérését](media-services-use-aad-auth-to-access-ams-api.md) ismertető cikket.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

