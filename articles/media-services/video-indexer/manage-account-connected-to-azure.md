---
title: A Video Indexer-fiók kezelése
titlesuffix: Azure Media Services
description: Ez a cikk bemutatja, hogyan kezelheti a Video Indexer-fiókot az Azure-hoz csatlakoztatva.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: 43e08298618f059ff706d11ce6bbb3d486642872
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292366"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>A Video Indexer-fiókot az Azure-hoz csatlakoztatott kezelése

Ez a cikk bemutatja, hogyan kezelheti az Azure-előfizetéséhez kapcsolódó Video Indexer-fiókot és az Azure Media Services-fiók.

> [!NOTE]
> Ebben a témakörben tárgyalt konfigurációs beállításainak fiókot ehhez a Video Indexer fiók tulajdonosa van.

## <a name="prerequisites"></a>Előfeltételek

Csatlakozás a Video Indexer-fiókot az Azure-bA leírtak szerint [kapcsolódik az Azure](connect-to-azure.md). 

Ügyeljen arra, hogy hajtsa végre a [Előfeltételek](connect-to-azure.md#prerequisites) , és tekintse át [szempontok](connect-to-azure.md#considerations) című cikkben.

## <a name="examine-account-settings"></a>Vizsgálja meg a Fiókbeállítások

Ez a szakasz megvizsgálja a Video Indexer-fiókot beállításait.

Beállítások megtekintése:

1. Kattintson a jobb felső sarokban lévő felhasználói ikonra, és válassza ki **beállítások**.

    ![Beállítások](./media/manage-account-connected-to-azure/select-settings.png)

2. Az a **beállítások** lapon válassza ki a **fiók** fülre.

Ha a videók Indexer-fiókot az Azure-hoz csatlakozik, akkor tekintse meg a következőket:

* Az alapul szolgáló Azure Media Services-fiók neve.
* Az indexelési feladatok futtatása, és aszinkron száma.
* Száma és típusa lefoglalt szolgáltatás számára fenntartott egységeket.

Ha a fiók néhány van szüksége, látni fogja kapcsolódó hibák és figyelmeztetések a fiók konfigurálásával kapcsolatban a a **beállítások** lapot. Az üzeneteket az Azure Portalon pontos helyeket, ahol módosításokat szeretne hivatkozásokat tartalmaznak. További információkért lásd: a [hibák és figyelmeztetések](#errors-and-warnings) a következő szakasz.

## <a name="auto-scale-reserved-units"></a>Automatikus skálázás szolgáltatás számára fenntartott egységek

A **beállítások** lap lehetővé teszi, hogy állítsa be az automatikus skálázás, a Media szolgáltatás számára fenntartott egységek (RU). Ha a beállítás **a**, lefoglalni a fenntartott egységek maximális száma, és győződjön meg, hogy a Video Indexer leállítása/elindítja RUs automatikusan. Ezzel a beállítással üresjárati idő plusz költséget takaríthat meg nem kell fizetnie, de még nem vár az indexelési feladatokat, ha túl magas az indexelési terhelés hosszú ideig végrehajtásához.

Automatikus skálázás nem méretezhető alább 1 RU vagy meghaladja az alapértelmezett a Media Services-fiók. Annak érdekében, hogy a korlát növelését, hozzon létre egy szolgáltatási kérelmet. Kvóták és korlátozások és a egy támogatási jegyet kapcsolatos információkért lásd: [kvóták és korlátozások](../../media-services/previous/media-services-quotas-and-limitations.md).

![Regisztráció](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Hibák és figyelmeztetések

Ha a fiók néhány van szüksége, lát kapcsolódó hibák és figyelmeztetések a fiók konfigurálásával kapcsolatban a a **beállítások** lap. Az üzeneteket az Azure Portalon pontos helyeket, ahol módosításokat szeretne hivatkozásokat tartalmaznak. Ez a szakasz további tájékoztatást nyújt a hibaüzenetek és figyelmeztető üzenetek.

* Event Grid

    Az Azure portal használatával EventGrid erőforrás-szolgáltatót regisztrálnia kell. Az a [az Azure portal](https://portal.azure.com/), lépjen a **előfizetések** > [. előfizetés] > **ResourceProviders** > **Microsoft.EventGrid**. Ha nem található a **regisztrált** állapotban van, kattintson a **regisztrálása**. Néhány perc alatt regisztrálni vesz igénybe. 

* Streamvégpont

    Ellenőrizze, hogy az alapul szolgáló Media Services-fiók rendelkezik az alapértelmezett **folyamatos átviteli végponton** egy elindított állapotú. Ellenkező esetben nem lesz nézhet videókat a Media Services-fiók vagy a Video Indexer található.

* Media szolgáltatás számára fenntartott egységek 

    A Media szolgáltatás számára fenntartott egységek érdekében, hogy az index videókat a Media szolgáltatás erőforráson kell lefoglalni. Az optimális indexelési teljesítmény érdekében javasoljuk, hogy legalább 10 S3 fenntartott egységek lefoglalása. Díjszabási információkért tekintse meg a gyakori kérdésekkel foglalkozó szakaszban, a [Media Services-díjszabás](https://azure.microsoft.com/pricing/details/media-services/) lapot.   

## <a name="next-steps"></a>További lépések

Programozott módon kezelheti a próbaverziós fiókjához és/vagy a Video Indexer-fiókokkal, amelyek köti össze az Azure-ban utasításait követve: [használható API-khoz](video-indexer-use-apis.md).

Az azonos Azure AD-felhasználót az Azure-ba való kapcsolódáskor használt kell használnia.
