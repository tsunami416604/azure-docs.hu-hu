---
title: A Video Indexer – Azure több bérlő kezelése
description: Ez a cikk a különböző integrációs beállításainak kezelése több bérlőnél a Video Indexer javasol.
services: media-services
documentationcenter: ''
author: ika-microsoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/08/2019
ms.author: ikbarmen
ms.openlocfilehash: 800cd04f8f4acf7a9768a8ccdce5a4d9783d3a4d
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55985175"
---
# <a name="manage-multiple-tenants"></a>Több bérlő kezelése

Ez a cikk ismerteti a Video Indexer több bérlő kezelésének különböző lehetőségei. Válasszon, amely a forgatókönyvhöz leginkább megfelelő módszert:

* Video Indexer-fiókot bérlőnként
* Egyetlen Video Indexer-fiókot az összes bérlőre vonatkozóan
* Azure-előfizetés bérlőnként

## <a name="video-indexer-account-per-tenant"></a>Video Indexer-fiókot bérlőnként

Ez az architektúra használatakor a Video Indexer-fiókot minden bérlő jön létre. A bérlők teljes elkülönítést rendelkezik az állandó, és a számítási réteg.  

![Video Indexer-fiókot bérlőnként](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>Megfontolandó szempontok

* Ügyfelek ne ossza meg tárfiókok (kivéve, ha az ügyfél manuálisan konfigurált).
* Az ügyfelek ne ossza meg a számítási (szolgáltatás számára fenntartott egységek), és ne befolyásolják egy másik feldolgozási feladatok időt.
* Törli a Video Indexer-fiókot a rendszer egyszerű eltávolításához egy bérlőt.
* Nem létezik a bérlők között egyéni modellek megosztásának lehetősége nélkül.

    Ellenőrizze, hogy üzleti nem követelmény, egyéni modellek megosztásához.
* Nehezebb fiókok bérlőnként több Video Indexer (és a kapcsolódó Media Services) miatt kezelése.

> [!TIP]
> Hozzon létre egy rendszergazdai felhasználót a rendszer a [Video Indexer fejlesztői portál](https://api-portal.videoindexer.ai/) és a hitelesítési API segítségével biztosíthat a bérlők számára a megfelelő [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token).

## <a name="single-video-indexer-account-for-all-users"></a>Egyetlen Video Indexer-fiókot az összes felhasználó számára

Ez az architektúra használatával, amikor az ügyfél felelős bérlők elkülönítését. Az összes bérlőre kell használnia egy egyetlen Video Indexer-fiókot egy Azure Media Services-fiókkal. Feltöltésekor, keresést, vagy törölje a tartalmat, az ügyfél kell, hogy a bérlő számára a megfelelő eredmények szűréséhez.

![Egyetlen Video Indexer-fiókot az összes felhasználó számára](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

Ezzel a beállítással testreszabási modellek (személy, nyelv és márkákat) lehet megosztott vagy elszigetelt bérlő által a modellek szűrésével bérlők között.

Amikor [megkezdené a videófeltöltést](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?), bérlőnként egy másik partíció attribútumot is megadhat. Ez lehetővé teszi az elkülönítés a [search API](https://api-portal.videoindexer.ai/docs/services/operations/operations/Search-videos?). Megadja a partíció attribútum a search API csak a megadott partíció eredményeket fog kapni. 

 ### <a name="considerations"></a>Megfontolandó szempontok

* Tartalom és a Testreszabás modellek bérlők között megosztásának lehetősége.
* Egyetlen bérlő teljesítményére hatással van a többi bérlőtől.
* Ügyfél egy összetett réteg felül a Video Indexer létre kell hoznia.

> [!TIP]
> Használhatja a [prioritású](upload-index-videos.md) bérlők feladatok rangsorolására attribútum.

## <a name="azure-subscription-per-tenant"></a>Azure-előfizetés bérlőnként 

Ez az architektúra használatakor az egyes bérlők lesz a saját Azure-előfizetést. Minden felhasználó számára egy új Video Indexer-fiókot hoz létre a bérlő előfizetés.

![Azure-előfizetés bérlőnként](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>Megfontolandó szempontok

* Ez a lehetőség csak, amely lehetővé teszi a számlázási elkülönítését.
* Ez az integráció több felügyeleti költséggel járnak, mint a Video Indexer-fiókot bérlőnként rendelkezik. Ha a számlázási nem követelmény, ajánlott a többi beállítást a jelen cikkben ismertetett valamelyikével.

## <a name="next-steps"></a>További lépések

[Áttekintés](video-indexer-overview.md)
