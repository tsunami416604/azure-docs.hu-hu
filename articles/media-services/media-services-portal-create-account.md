---
title: " Azure Media Services-fiók létrehozása az Azure Portal használatával | Microsoft Docss"
description: "Ez az oktatóanyag végigvezeti azokon a lépéseken, amelyek segítségével Azure Media Services-fiókot hozhat létre az Azure Portal használatával."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/10/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: f6d6b7b1051a22bbc865b237905f8df84e832231
ms.openlocfilehash: 01c1297a6b107f91d8e6e73f17d76499d67849de


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

## <a name="concepts"></a>Alapelvek
A Media Services szolgáltatásainak eléréséhez két kapcsolódó fiók szükséges:

* Egy Media Services-fiók. A fiók hozzáférést biztosít több felhőalapú, az Azure szolgáltatásban elérhető Media Services-szolgáltatáshoz. A Media Services-fiók nem tárol tényleges médiatartalmakat. Ehelyett metaadatokat tárol a fiókban található médiatartalmakról és médiafeldolgozási feladatokról. A fiók létrehozásakor ki kell választania egy elérhető Media Services-régiót. A választott régió egy adatközpont, amely a fiók metaadat-rekordjait tárolja.
  
    Az elérhető Media Services (AMS)-régiók a következők: Észak-Európa, Nyugat-Európa, USA nyugati régiója, USA keleti régiója, Délkelet-Ázsia, Kelet-Ázsia, Nyugat-Japán, Kelet-Japán. A Media Services nem használ affinitáscsoportokat.
  
    Az AMS már a következő adatközpontokban is elérhető: Dél-Brazília, Nyugat-India, Dél-India és Közép-India. Az Azure Portalt már Media Services-fiókok létrehozására és az itt leírt különböző feladatok elvégzésére is használhatja. Azonban ezekben az adatközpontokban a Live Encoding nem támogatott. Emellett ezekben az adatközpontokban a kódoláshoz fenntartott egységeknek sem minden típusa érhető el.
  
  * Dél-Brazília: Csak a standard és alapszintű kódoláshoz fenntartott egységek érhetők el.
  * Nyugat-India, Dél-India: 
* Egy Azure-tárfiók. A tárfiókoknak a Media Services-fiókkal azonos földrajzi régióban kell lenniük. Egy Media Services-fiók létrehozásakor választhat, hogy egy ugyanabban a régióban található, meglévő tárfiókot használ, vagy létrehoz egy újat ugyanabban a régióban. Egy Media Services-fiók törlésekor a kapcsolódó tárfiókban található blobok nem törlődnek.

## <a name="create-an-ams-account"></a>AMS-fiók létrehozása
A jelen szakaszban ismertetett lépések bemutatják az AMS-fiók létrehozásának módját.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a **+New** > **Web + Mobile** > **Media Services** elemre.
   
    ![Media Services, létrehozás](./media/media-services-create-account/media-services-new1.png)
3. A **CREATE MEDIA SERVICES ACCOUNT** (Media Services-fiók létrehozása) részben adja meg a kívánt értékeket.
   
    ![Media Services, létrehozás](./media/media-services-create-account/media-services-new3.png)
   
   1. Az **Account Name** (Fiók neve) mezőben adja meg az új AMS-fiók nevét. A Media Services-fiók neve csak kisbetűket és számokat tartalmazhat, nem tartalmazhat szóközöket, és 3–24 karakterből állhat.
   2. A Subscription (Előfizetés) résznél válasszon az elérhető Azure-előfizetések közül.
   3. A **Resource Group** (Erőforráscsoport) résznél válasszon egy új vagy meglévő erőforrást.  Az erőforráscsoport közös életciklussal, engedélyekkel és házirendekkel rendelkező erőforrások gyűjteménye. További információkat [itt](../azure-resource-manager/resource-group-overview.md#resource-groups) talál.
   4. A **Location** (Hely) részben válassza ki azt a földrajzi régiót, amelyben tárolni fogja a Media Services-fiókhoz tartozó médiafájlokat és metaadat-bejegyzéseket. A rendszer e régió alapján fogja feldolgozni, illetve streamelni a médiafájlokat. A legördülő listában csak a Media Services szolgáltatásban elérhető régiók jelennek meg. 
   5. A **Storage Account** (Tárfiók) résznél válasszon egy tárfiókot, amely Blob Storage tárolót fog biztosítani a Media Services-fiókhoz tartozó médiatartalmak számára. Választhat, hogy egy meglévő, a Media Services-fiókkal azonos földrajzi régióban található tárfiókot használ, vagy létrehoz egy másik tárfiókot. Az újonnan létrehozott tárfiókok ugyanabban a régióban jönnek létre. A tárfiók nevére ugyanazok a szabályok vonatkoznak, mint a Media Services-fiókok nevére.
      
       További információkat a tárhelyről [itt](../storage/storage-introduction.md) talál.
   6. A fióklétrehozás előrehaladásának megtekintéséhez kattintson a **Rögzítés az irányítópulton** elemre.
4. Kattintson az űrlap alján található **Létrehozás** lehetőségre.
   
    A fiók sikeres létrehozása után betöltődik az áttekintési oldal. A streamvégpont-táblázatban a fiók alapértelmezett streamvégpontja **Leállítva** állapotban lesz. 

    >[!NOTE]
    >Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 
   
    ![Media Services, beállítások](./media/media-services-create-account/media-services-settings.png)
   
    Az AMS-fiók kezeléséhez (például videók feltöltéséhez, objektumok kódolásához, a feladatok előrehaladásának figyeléséhez) használja a **Settings** (Beállítások) ablakot.

## <a name="manage-keys"></a>Kulcsok kezelése
A Media Services-fiók programon keresztüli eléréséhez szüksége lesz a fiók nevére és az elsődleges kulcs adataira.

1. Válassza ki a fiókját az Azure Portalon. 
   
    Jobb oldalt megjelenik a **Beállítások** ablak. 
2. A **Beállítások** ablakban válassza a **Kulcsok** lehetőséget. 
   
    A **Kulcsok kezelése** ablakban megtalálja a fiók nevét, valamint az elsődleges és másodlagos kulcsot. 
3. Az értékek másolásához nyomja le a Másolás gombot.
   
    ![Media Services, kulcsok](./media/media-services-create-account/media-services-keys.png)

## <a name="next-steps"></a>Következő lépések
Most már feltölthet fájlokat AMS-fiókjába. További információk: [Fájlok feltöltése](media-services-portal-upload-files.md).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO2-->


