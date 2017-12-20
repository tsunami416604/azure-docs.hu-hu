---
title: "Azure Media Services-fiók létrehozása az Azure Portal használatával | Microsoft Docs"
description: "Ez az oktatóanyag végigvezeti azokon a lépéseken, amelyek segítségével Azure Media Services-fiókot hozhat létre az Azure Portal használatával."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/03/2017
ms.author: juliako
ms.openlocfilehash: 4e811dee81bfbd01b3cbe7f78a57b2ce92d1c0f9
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Azure Media Services-fiók létrehozása az Azure Portal használatával
> [!div class="op_single_selector"]
> * [Portál](media-services-portal-create-account.md)
> * [PowerShell](media-services-manage-with-powershell.md)
> * [REST](https://docs.microsoft.com/rest/api/media/mediaservice)
> 
> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Az Azure Portalon gyorsan létrehozhat egy Azure Media Services- (AMS-) fiókot. A fiókkal hozzáférhet olyan Media Services-szogáltatásokhoz, amelyekkel médiatartalmakat tárolhat, titkosíthat, kódolhat, kezelhet és továbbíthat az Azure környezetben. A Media Services-fiók létrehozásával egy időben létrejön egy kapcsolódó tárfiók is (illetve egy már meglévőt is használhat) ugyanabban a földrajzi régióban, ahol a Media Services-fiók is található.

Ez a cikk néhány általánosan használt fogalmat tárgyal, illetve bemutatja, hogyan hozhat létre Media Services-fiókot az Azure Portal használatával.

> [!NOTE]
> A különböző régiókban lévő Azure Media Services-funkciók elérhetőségéről további információért lásd [az AMS-funkciók elérhetőségét az egyes adatközpontokban](scenarios-and-availability.md#availability).

## <a name="concepts"></a>Alapelvek
A Media Services szolgáltatásainak eléréséhez két kapcsolódó fiók szükséges:

* Egy Media Services-fiók. A fiók hozzáférést biztosít több felhőalapú, az Azure szolgáltatásban elérhető Media Services-szolgáltatáshoz. A Media Services-fiók nem tárol tényleges médiatartalmakat. Ehelyett metaadatokat tárol a fiókban található médiatartalmakról és médiafeldolgozási feladatokról. A fiók létrehozásakor ki kell választania egy elérhető Media Services-régiót. A választott régió egy adatközpont, amely a fiók metaadat-rekordjait tárolja.
  
* Egy Azure-tárfiók. A tárfiókoknak a Media Services-fiókkal azonos földrajzi régióban kell lenniük. Egy Media Services-fiók létrehozásakor választhat, hogy egy ugyanabban a régióban található, meglévő tárfiókot használ, vagy létrehoz egy újat ugyanabban a régióban. Egy Media Services-fiók törlésekor a kapcsolódó tárfiókban található blobok nem törlődnek.

  > [!NOTE]
  > A Media Services kiköti, hogy az elsődleges tárfióknak **Általános célú tárfióknak** kell lennie táblákkal, üzenetsorokkal. További információ a tárolási típusokról: [Tudnivalók az Azure Storage-fiókokról](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

## <a name="create-an-ams-account"></a>AMS-fiók létrehozása
A jelen szakaszban ismertetett lépések bemutatják az AMS-fiók létrehozásának módját.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a **+New** > **Web + Mobile** > **Media Services** elemre.
   
    ![Media Services, létrehozás](./media/media-services-create-account/media-services-new1.png)
3. A **CREATE MEDIA SERVICES ACCOUNT** (Media Services-fiók létrehozása) részben adja meg a kívánt értékeket.
   
    ![Media Services, létrehozás](./media/media-services-create-account/media-services-new3.png)
   
   1. Az **Account Name** (Fiók neve) mezőben adja meg az új AMS-fiók nevét. A Media Services-fiók neve csak számokat és kisbetűket tartalmazhat, nem tartalmazhat szóközöket, és 3–24 karakterből állhat.
   2. A Subscription (Előfizetés) résznél válasszon az elérhető Azure-előfizetések közül.
   3. A **Resource Group** (Erőforráscsoport) résznél válasszon egy új vagy meglévő erőforrást.  Az erőforráscsoport közös életciklussal, engedélyekkel és házirendekkel rendelkező erőforrások gyűjteménye. További információkat [itt](../azure-resource-manager/resource-group-overview.md#resource-groups) talál.
   4. A **Location** (Hely) részben válassza ki azt a földrajzi régiót, amelyben tárolni fogja a Media Services-fiókhoz tartozó médiafájlokat és metaadat-bejegyzéseket. A rendszer e régió alapján fogja feldolgozni, illetve streamelni a médiafájlokat. A legördülő listában csak a Media Services szolgáltatásban elérhető régiók jelennek meg. 
   5. A **Storage Account** (Tárfiók) résznél válasszon egy tárfiókot, amely Blob Storage tárolót fog biztosítani a Media Services-fiókhoz tartozó médiatartalmak számára. Választhat, hogy egy meglévő, a Media Services-fiókkal azonos földrajzi régióban található tárfiókot használ, vagy létrehoz egy másik tárfiókot. Az újonnan létrehozott tárfiókok ugyanabban a régióban jönnek létre. A tárfiók nevére ugyanazok a szabályok vonatkoznak, mint a Media Services-fiókok nevére.
      
       További információkat a tárhelyről [itt](../storage/common/storage-introduction.md) talál.
   6. A fióklétrehozás előrehaladásának megtekintéséhez kattintson a **Rögzítés az irányítópulton** elemre.
4. Kattintson az űrlap alján található **Létrehozás** lehetőségre.
   
    A fiók sikeres létrehozása után betöltődik az áttekintési oldal. A streamvégpont-táblázatban a fiók alapértelmezett streamvégpontja **Leállítva** állapotban lesz. 

    >[!NOTE]
    >Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 
   
## <a name="to-manage-your-ams-account"></a>Az AMS-fiók kezelése

Az AMS-fiók kezeléséhez (például az AMS API-hoz való programozott kapcsolódáshoz, videók feltöltéséhez, objektumok kódolásához, tartalomvédelem konfigurálásához, a feladatok előrehaladásának figyeléséhez) válassza a portál bal oldalán lévő **Beállítások** elemet. A **Beállítások** területen keresse meg az egyik elérhető panelt (például: **API-hozzáférés**, **Objektumok**, **Feladatok**, **Tartalomvédelem**).


## <a name="next-steps"></a>Következő lépések

Most már feltölthet fájlokat AMS-fiókjába. További információk: [Fájlok feltöltése](media-services-portal-upload-files.md).

Ha programozott módon szeretné elérni az AMS API-t, lásd [az Azure Media Services Azure AD-hitelesítéssel történő elérését](media-services-use-aad-auth-to-access-ams-api.md) ismertető cikket.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

