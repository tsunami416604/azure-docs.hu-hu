---
title: Több bérlő kezelése Video Indexer-Azure-val
description: Ez a cikk különböző integrációs lehetőségeket javasol több bérlő Video Indexer-nal való kezeléséhez.
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
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990504"
---
# <a name="manage-multiple-tenants"></a>Több bérlő kezelése

Ez a cikk a több bérlő Video Indexer-vel való felügyeletének különböző lehetőségeit tárgyalja. Válasszon egy módszert, amely a legmegfelelőbb a forgatókönyvhöz:

* Video Indexer fiók/bérlő
* Egyetlen Video Indexer fiók az összes bérlőhöz
* Azure-előfizetés/bérlő

## <a name="video-indexer-account-per-tenant"></a>Video Indexer fiók/bérlő

Ezen architektúra használatakor létrejön egy Video Indexer fiók az egyes bérlők számára. A bérlők teljes elkülönítéssel rendelkeznek az állandó és a számítási rétegben.  

![Video Indexer fiók/bérlő](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>Megfontolandó szempontok

* Az ügyfelek nem osztják meg a Storage-fiókokat (kivéve, ha az ügyfél manuálisan konfigurálta).
* Az ügyfelek nem osztják meg a számítási feladatokat (fenntartott egységeket), és nem befolyásolják egymást a feladatok feldolgozásának időpontját.
* A Video Indexer fiók törlésével könnyedén eltávolíthatja a bérlőt a rendszerből.
* A bérlők között nincs lehetőség egyéni modellek megosztására.

    Győződjön meg arról, hogy az egyéni modellek megosztására nincs üzleti követelmény.
* A több Video Indexer (és a társított Media Services) fiókokat bérlők miatt nehezebb kezelni.

> [!TIP]
> Hozzon létre egy rendszergazdai felhasználót a rendszeréhez [video Indexer fejlesztői portálon](https://api-portal.videoindexer.ai/) , és az engedélyezési API segítségével adja meg a bérlők számára a megfelelő [fiók hozzáférési tokenjét](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token).

## <a name="single-video-indexer-account-for-all-users"></a>Egyetlen Video Indexer fiók az összes felhasználó számára

Ha ezt az architektúrát használja, az ügyfél felelős a bérlők elkülönítésében. Minden bérlőnek egyetlen, egyetlen Azure Media Service-fiókkal rendelkező Video Indexer fiókot kell használnia. Tartalom feltöltésekor, keresésekor vagy törlésekor az ügyfélnek szűrnie kell a bérlő megfelelő eredményét.

![Egyetlen Video Indexer fiók az összes felhasználó számára](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

Ezzel a beállítással a különböző testreszabási modellek (személy, nyelv és márkák) megoszthatók és elkülöníthetők a bérlők között a modellek bérlő szerinti szűrésével.

[Videók feltöltésekor](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)egy másik Partition attribútumot is megadhat bérlőként. Ez lehetővé teszi az elkülönítést a [keresési API](https://api-portal.videoindexer.ai/docs/services/operations/operations/Search-videos?)-ban. A Search API-ban a Partition attribútum megadásával csak a megadott partíció eredményei lesznek elérhetők. 

### <a name="considerations"></a>Megfontolandó szempontok

* A tartalom és a testreszabási modellek megosztása a bérlők között.
* Az egyik bérlő más bérlők teljesítményére is hatással van.
* Az ügyfélnek összetett felügyeleti réteget kell létrehoznia Video Indexer-on felül.

> [!TIP]
> A [prioritás](upload-index-videos.md) attribútum használatával rangsorolhatja a bérlők feladatait.

## <a name="azure-subscription-per-tenant"></a>Azure-előfizetés/bérlő 

Ha ezt az architektúrát használja, minden bérlő saját Azure-előfizetéssel fog rendelkezni. Minden felhasználóhoz létre kell hoznia egy új Video Indexer fiókot a bérlői előfizetésben.

![Azure-előfizetés/bérlő](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>Megfontolandó szempontok

* Ez az egyetlen olyan lehetőség, amely lehetővé teszi a számlázás elkülönítését.
* Ez az integráció több felügyeleti terheléssel rendelkezik, mint a Video Indexer-fiók. Ha a számlázás nem követelmény, javasoljuk, hogy használja az ebben a cikkben ismertetett egyéb lehetőségek egyikét.

## <a name="next-steps"></a>Következő lépések

[Áttekintés](video-indexer-overview.md)
