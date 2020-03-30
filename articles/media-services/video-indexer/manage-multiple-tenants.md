---
title: Több bérlő kezelése a Video Indexer rel – Azure
description: Ez a cikk különböző integrációs lehetőségeket javasol több bérlő videoindexelővel való kezeléséhez.
services: media-services
documentationcenter: ''
author: ika-microsoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/15/2019
ms.author: ikbarmen
ms.openlocfilehash: 18f2cf3daa281400151ba223e1735e7138d97e8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76990504"
---
# <a name="manage-multiple-tenants"></a>Több bérlő kezelése

Ez a cikk ismerteti a különböző beállítások kezelésére több bérlő videoindexelő. Válassza ki a forgatókönyvnek leginkább megfelelő módszert:

* Videoindexer-fiók bérlőnként
* Egyvideós indexelő-fiók az összes bérlőhöz
* Azure-előfizetés bérlőnként

## <a name="video-indexer-account-per-tenant"></a>Videoindexer-fiók bérlőnként

Az architektúra használatakor minden bérlőhöz létrejön egy videoindexer-fiók. A bérlők teljes elkülönítéssel rendelkeznek az állandó és számítási rétegben.  

![Videoindexer-fiók bérlőnként](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>Megfontolandó szempontok

* Az ügyfelek nem osztanak meg tárfiókokat (kivéve, ha az ügyfél manuálisan konfigurálja).
* Az ügyfelek nem osztják meg a számítási (fenntartott egységeket), és nem befolyásolják egymás feldolgozási feladatainak idejét.
* A Video Indexer-fiók törlésével könnyedén eltávolíthatja a bérlőt a rendszerből.
* Nincs képes megosztani az egyéni modellek bérlők között.

    Győződjön meg arról, hogy nincs üzleti követelmény az egyéni modellek megosztásához.
* Nehezebb encikba kezelni a bérlőnkénti több videoindexelő (és a kapcsolódó Media Services) fiókok miatt.

> [!TIP]
> Hozzon létre egy rendszergazdai felhasználót a rendszerhez a [Video Indexer Developer Portal webhelyen,](https://api-portal.videoindexer.ai/) és az Engedélyezési API segítségével biztosítsa a bérlőknek a megfelelő [fiók-hozzáférési jogkivonatot.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token)

## <a name="single-video-indexer-account-for-all-users"></a>Egyvideós indexelő-fiók az összes felhasználó számára

Az architektúra használatakor az ügyfél felelős a bérlők elkülönítéséért. Minden bérlőnek egyetlen Video Indexer-fiókot kell használnia egyetlen Azure Media Service-fiókkal. Tartalom feltöltésekor, keresésekor vagy törlésekor az ügyfélnek ki kell szűrnie az adott bérlő megfelelő eredményeit.

![Egyvideós indexelő-fiók az összes felhasználó számára](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

Ezzel a beállítással testreszabási modellek (személy, nyelv és márkák) megoszthatók vagy elkülöníthetők a bérlők között a modellek bérlő szerinti szűrésével.

[Videók feltöltésekor](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)megadhat egy másik partícióattribútumot bérlőnként. Ez lehetővé teszi az elkülönítést a [keresési API-ban.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Search-videos?) A partícióattribútum megadásával a keresési API-ban csak a megadott partíció eredményeit kapja meg. 

### <a name="considerations"></a>Megfontolandó szempontok

* Tartalom- és testreszabási modellek megosztása a bérlők között.
* Az egyik bérlő hatással van a többi bérlő teljesítményére.
* Az ügyfélnek összetett felügyeleti réteget kell építenie a Video Indexer tetejére.

> [!TIP]
> A [prioritásattribútum](upload-index-videos.md) segítségével rangsorolhatja a bérlők feladatait.

## <a name="azure-subscription-per-tenant"></a>Azure-előfizetés bérlőnként 

Az architektúra használatakor minden bérlő saját Azure-előfizetéssel rendelkezik. Minden felhasználó hoz létre egy új videoindexer-fiókot a bérlői előfizetésben.

![Azure-előfizetés bérlőnként](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>Megfontolandó szempontok

* Ez az egyetlen lehetőség, amely lehetővé teszi a számlázási elkülönítést.
* Ez az integráció több felügyeleti többletterheléssel rendelkezik, mint a bérlőnkénti Video Indexer-fiók. Ha a számlázás nem követelmény, javasoljuk, hogy használja a cikkben ismertetett egyéb lehetőségek egyikét.

## <a name="next-steps"></a>További lépések

[Áttekintés](video-indexer-overview.md)
