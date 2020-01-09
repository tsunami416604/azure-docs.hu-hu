---
title: Video Indexer fiók kezelése
titleSuffix: Azure Media Services
description: Ez a cikk bemutatja, hogyan kezelhetők az Azure-hoz csatlakoztatott Video Indexer-fiókok.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/16/2019
ms.author: juliako
ms.openlocfilehash: f3825f6c9186c5e04807dd3890a14fcc6d370989
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454679"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Az Azure-hoz csatlakoztatott Video Indexer-fiók kezelése

Ez a cikk bemutatja, hogyan kezelheti az Azure-előfizetéshez és egy Azure Media Services-fiókhoz kapcsolódó Video Indexer fiókot.

> [!NOTE]
> A jelen témakörben ismertetett fiók-konfigurációs módosítások elvégzéséhez Video Indexer fiók tulajdonosának kell lennie.

## <a name="prerequisites"></a>Előfeltételek

Csatlakoztassa Video Indexer-fiókját az Azure-hoz az [Azure-hoz csatlakoztatva](connect-to-azure.md). 

Ügyeljen arra, hogy kövesse az [Előfeltételek](connect-to-azure.md#prerequisites) és a felülvizsgálati [szempontokat](connect-to-azure.md#considerations) a cikkben.

## <a name="examine-account-settings"></a>Fiókbeállítások vizsgálata

Ez a szakasz a Video Indexer fiókjának beállításait vizsgálja.

Beállítások megtekintése:

1. Kattintson a jobb felső sarokban található felhasználó ikonra, és válassza a **Beállítások**lehetőséget.

    ![Beállítások](./media/manage-account-connected-to-azure/select-settings.png)

2. A **Beállítások** lapon válassza a **fiók** fület.

Ha a video Indexer-fiókja csatlakozik az Azure-hoz, a következő jelenik meg:

* Az alapul szolgáló Azure Media Services fiók neve.
* A-t futtató és várólistára helyezett indexelési feladatok száma.
* A lefoglalt fenntartott egységek száma és típusa.

Ha a fióknak valamilyen módosításra van szüksége, a **Beállítások** lapon láthatja a fiók konfigurációjának megfelelő hibákat és figyelmeztetéseket. Az üzenetek a Azure Portal pontos helyeire mutató hivatkozásokat tartalmaznak, amelyekben módosítani kell a módosításokat. További információ: a következő, [hibákkal és figyelmeztetésekkel](#errors-and-warnings) foglalkozó szakasz.

## <a name="repair-the-connection-to-azure"></a>Az Azure-hoz való kapcsolódás javítása

A [video Indexer](https://www.videoindexer.ai/) lap **kapcsolatok frissítése Azure Media Services** párbeszédpanelén megkéri a következő beállítások értékének megadását: 

|Beállítás|Leírás|
|---|---|
|Azure-előfizetés azonosítója|Az előfizetés-azonosító a Azure Portalból kérhető le. A bal oldali panelen kattintson a **minden szolgáltatás** elemre, és keressen rá az "előfizetések" kifejezésre. Válassza az **előfizetések** lehetőséget, majd válassza ki a kívánt azonosítót az előfizetések listájából.|
|Azure Media Services erőforráscsoport neve|Azon erőforráscsoport neve, amelyben létrehozta a Media Services fiókot.|
|Alkalmazásazonosító|Az ehhez a Video Indexer-fiókhoz létrehozott Azure AD-alkalmazás azonosítója (a megadott Media Services fiókhoz tartozó engedélyekkel). <br/><br/>Az alkalmazás AZONOSÍTÓjának beszerzéséhez navigáljon Azure Portal. A Media Services fiók alatt válassza ki a fiókját, és lépjen az **API-hozzáférés**elemre. Kattintson **a Kapcsolódás a Media Services API-hoz az egyszerű szolgáltatásnév** -> **Azure ad alkalmazás**. Másolja ki a megfelelő paramétereket.|
|Alkalmazás kulcsa|A fent megadott Media Services-fiókhoz társított Azure AD-alkalmazás kulcsa. <br/><br/>Az alkalmazás kulcsának beszerzéséhez navigáljon Azure Portal. A Media Services fiók alatt válassza ki a fiókját, és lépjen az **API-hozzáférés**elemre. Kattintson **a Kapcsolódás az Media Services API-hoz az egyszerű szolgáltatásnév -> az** alkalmazás -> - **tanúsítványok & titkok** **kezelése** elemre. Másolja ki a megfelelő paramétereket.|

## <a name="auto-scale-reserved-units"></a>Fenntartott egységek automatikus méretezése

A **Beállítások** lapon állíthatja be a Media szolgáltatás számára fenntartott egységek (ru) automatikus skálázását. Ha a lehetőség **be van kapcsolva**, lefoglalhatja az RUS maximális számát, és biztos lehet benne, hogy a video Indexer automatikusan leállítja/elindítja az RUS-t. Ha ezt a lehetőséget választja, nem számítunk fel extra összeget a tétlen időpontig, de nem kell megvárniuk, hogy az indexelési feladatok hosszú ideig befejeződjön, amikor az indexelési terhelés magas.

Az automatikus méretezés nem méretezhető a Media Services fiók alapértelmezett korlátjának 1 RU vagy újabb verziójában. A korlát növeléséhez hozzon létre egy szolgáltatási kérelmet. A kvótákkal és korlátozásokkal, valamint a támogatási jegyek megnyitásával kapcsolatos információkért lásd: [kvóták és korlátozások](../../media-services/previous/media-services-quotas-and-limitations.md).

![Regisztráció](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Hibák és figyelmeztetések

Ha a fióknak valamilyen módosításra van szüksége, a **Beállítások** lapon megtekintheti a fiók konfigurációjának megfelelő hibákat és figyelmeztetéseket. Az üzenetek a Azure Portal pontos helyeire mutató hivatkozásokat tartalmaznak, amelyekben módosítani kell a módosításokat. Ez a szakasz további részleteket tartalmaz a hibákkal és a figyelmeztető üzenetekkel kapcsolatban.

* Event Grid

    Regisztrálnia kell a EventGrid erőforrás-szolgáltatót a Azure Portal használatával. A [Azure Portal](https://portal.azure.com/)lépjen az **előfizetések** > [előfizetés] > **ResourceProviders** > **Microsoft. EventGrid**. Ha nem **regisztrált** állapotban van, kattintson a **regisztráció**elemre. A regisztráció néhány percet vesz igénybe. 

* Folyamatos átviteli végpont

    Győződjön meg arról, hogy az alapul szolgáló Media Services fiók rendelkezik az alapértelmezett **folyamatos átviteli végponttal** egy elindított állapotban. Ellenkező esetben nem fog tudni videókat megtekinteni ebből a Media Services-fiókból vagy Video Indexerból.

* Media szolgáltatás számára fenntartott egységek 

    A videók indexeléséhez le kell foglalni a Media szolgáltatás számára fenntartott egységeket a Media Service-erőforráshoz. Az optimális indexelési teljesítmény érdekében javasoljuk, hogy legalább 10 S3 fenntartott egységet foglaljon le. A díjszabással kapcsolatos információkért tekintse meg a [Media Services díjszabási](https://azure.microsoft.com/pricing/details/media-services/) oldalának gyakori kérdések című szakaszát.   

## <a name="next-steps"></a>Következő lépések

Programozott módon használhatja a próbaverziós fiókját és/vagy az Azure-hoz kapcsolódó Video Indexer-fiókokat a következő témakörben található utasítások végrehajtásával: API-k [használata](video-indexer-use-apis.md).

Ugyanazt az Azure AD-felhasználót kell használnia, amelyet az Azure-hoz való csatlakozáskor használt.
