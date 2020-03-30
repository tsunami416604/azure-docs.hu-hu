---
title: Videoindexelő-fiók kezelése
titleSuffix: Azure Media Services
description: Ismerje meg, hogyan kezelheti az Azure-hoz csatlakoztatott Video Indexer-fiókot.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/16/2019
ms.author: juliako
ms.openlocfilehash: 5b9ec1c04dd90af9d9380a1f4d30386c8f9cc3ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499668"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Azure-hoz csatlakoztatott Video Indexer-fiók kezelése

Ez a cikk bemutatja, hogyan kezelheti az Azure-előfizetéséhez és egy Azure Media Services-fiókhoz kapcsolódó Video Indexer-fiókot.

> [!NOTE]
> A videóindexelő fiók tulajdonosának kell lennie ahhoz, hogy a jelen témakörben tárgyalt fiókkonfigurációs korrekciókat végezhet.

## <a name="prerequisites"></a>Előfeltételek

Csatlakoztassa videoindexelő-fiókját az Azure-hoz, az [Azure-hoz csatlakoztatás a Csatlakozás az Azure-hoz](connect-to-azure.md)című részben leírtak szerint.

Győződjön meg arról, hogy kövesse [az előfeltételeket,](connect-to-azure.md#prerequisites) és tekintse át a cikkben [található szempontokat.](connect-to-azure.md#considerations)

## <a name="examine-account-settings"></a>Fiókbeállítások vizsgálata

Ez a szakasz a Video Indexer-fiók beállításait vizsgálja.

A beállítások megtekintése:

1. Kattintson a felhasználó ikonjára a jobb felső sarokban, és válassza a **Beállítások lehetőséget.**

    ![Beállítások a Video Indexelőben](./media/manage-account-connected-to-azure/select-settings.png)

2. A **Beállítások** lapon válassza a **Fiók** lapot.

Ha a Videók Indexelő fiókja csatlakozik az Azure-hoz, a következő dolgokjelennek meg:

* Az alapul szolgáló Azure Media Services-fiók neve.
* A futó és várólistára helyezett indexelési feladatok száma.
* A lefoglalt fenntartott egységek száma és típusa.

Ha fiókjának szüksége van bizonyos korrekciókra, a **Beállítások** oldalon a fiók konfigurációjával kapcsolatos releváns hibákat és figyelmeztetéseket fog látni. Az üzenetek az Azure Portalon található pontos helyekre mutató hivatkozásokat tartalmaznak, ahol módosításokat kell végrehajtania. További információt az alábbi [hibák és figyelmeztetések](#errors-and-warnings) című részben talál.

## <a name="repair-the-connection-to-azure"></a>Az Azure-ral létesített kapcsolat javítása

A [Video Indexelő](https://www.videoindexer.ai/) lap **Azure Media Services szolgáltatással létesített kapcsolat frissítése** párbeszédpanelen a rendszer kéri, hogy adja meg a következő beállítások értékeit:

|Beállítás|Leírás|
|---|---|
|Azure-előfizetés azonosítója|Az előfizetés-azonosító lehívható az Azure Portalon. Kattintson **az Összes szolgáltatás** a bal oldali panelen, és keressen az "előfizetések". Válassza **az Előfizetések** lehetőséget, és válassza ki a kívánt azonosítót az előfizetések listájából.|
|Az Azure Media Services erőforráscsoportjának neve|Annak az erőforráscsoportnak a neve, amelyben létrehozta a Media Services-fiókot.|
|Alkalmazásazonosító|Az Azure AD-alkalmazásazonosító (a megadott Media Services-fiók engedélyeivel), amelyet ehhez a Video Indexer-fiókhoz hozott létre. <br/><br/>Az alkalmazásazonosító lekért, keresse meg az Azure Portalon. A Media Services-fiók alatt válassza ki a fiókját, és nyissa meg az **API Access lehetőséget.** Válassza **a Csatlakozás a Media Services API-hoz az egyszerű** -> **Azure AD alkalmazással**lehetőséget. Másolja a megfelelő paramétereket.|
|Alkalmazáskulcs|A Media Services-fiókhoz társított, fent megadott Azure AD alkalmazáskulcs. <br/><br/>Az alkalmazáskulcs leéséhez keresse meg az Azure Portalt. A Media Services-fiók alatt válassza ki a fiókját, és nyissa meg az **API Access lehetőséget.** Válassza **a Csatlakozás a Media Services API-hoz egyszerű** -> **alkalmazástanúsítvány-&** -> **titkos kulcsokkal**lehetőséget. Másolja a megfelelő paramétereket.|

## <a name="autoscale-reserved-units"></a>Automatikus skálázási fenntartott egységek

A **Beállítások** lap lehetővé teszi az adathordozószámára fenntartott egységek (RU) automatikus skálázásának beállítását. Ha a beállítás **Be**, akkor lefoglalhatja a felhasználói lehetőségek maximális számát, és meggyőződhet arról, hogy a Video Indexer automatikusan leállítja/elindítja a válaszhelyeket. Ezzel a beállítással nem kell külön pénzt fizetniaz tétlen időért, de ne várjon arra, hogy az indexelési feladatok hosszú ideig befejeződjenek, amikor az indexelési terhelés magas.

Az automatikus skálázás nem skálázható 1 RU alá vagy a Media Services-fiók alapértelmezett korlátja fölé. A korlát növeléséhez hozzon létre egy szolgáltatáskérelmet. A kvótákról és korlátozásokról, valamint a támogatási jegy megnyitásáról a [Kvóták és korlátozások](../../media-services/previous/media-services-quotas-and-limitations.md)című témakörben talál további információt.

![Automatikus skálázási fenntartott egységek Video Indexer](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Hibák és figyelmeztetések

Ha fiókjának szüksége van néhány módosításra, a **Beállítások** oldalon releváns hibák at és figyelmeztetéseket láthat a fiók konfigurációjával kapcsolatban. Az üzenetek az Azure Portalon található pontos helyekre mutató hivatkozásokat tartalmaznak, ahol módosításokat kell végrehajtania. Ez a szakasz további részleteket tartalmaz a hibaüzenetekről és a figyelmeztető üzenetekről.

* EventGrid

    Regisztrálnia kell az EventGrid erőforrás-szolgáltatót az Azure Portalon keresztül. Az [Azure Portalon](https://portal.azure.com/)nyissa meg **az Előfizetések** > [előfizetés] > **A ResourceProviders** > **Microsoft.EventGrid**. Ha nincs **a Regisztrált** állapotban, válassza a **Regisztráció**lehetőséget. A regisztráció néhány percet vesz igénybe.

* Streamvégpont

    Győződjön meg arról, hogy az alapul szolgáló Media Services-fiók rendelkezik az alapértelmezett **streamelési végpont egy** elindított állapotban. Ellenkező esetben nem nézhet videókat ebből a Media Services-fiókból vagy a Video Indexer alkalmazásban.

* Média számára fenntartott egységek

    A videók indexeléséhez media szolgáltatás számára fenntartott egységeket kell lefoglalnia a Media Service-erőforráson. Az optimális indexelési teljesítmény érdekében ajánlott legalább 10 S3 fenntartott egységet lefoglalni. Az árképzéssel kapcsolatos információkért tekintse meg a [Media Services díjszabási](https://azure.microsoft.com/pricing/details/media-services/) lapjának GYIK szakaszát.

## <a name="next-steps"></a>További lépések

Programozott módon kezelheti az Azure-hoz kapcsolódó próba- vagy VideoIndexer-fiókokat a következő utasítások szerint: [API-k használata](video-indexer-use-apis.md).

Használja ugyanazt az Azure AD-felhasználót, amelyet az Azure-hoz való csatlakozáskor használt.
