---
title: Vészhelyreállítás az Azure Data Share-hez
description: Vészhelyreállítás az Azure Data Share-hez
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: a736e3ddfcf785f9ce27140eed58374a0732c1f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483179"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Vészhelyreállítás az Azure Data Share-hez

Ebben a cikkben bemutatjuk, hogyan konfigurálhat egy vész-helyreállítási környezetet az Azure Data Share-hez. Az Azure adatközpont-kimaradások ritkák, de néhány perctől órákig tarthatnak. Az adatközpont-kimaradások zavart okozhatnak az adatszolgáltató által megosztott adatoktól függő környezetekben. A cikkben ismertetett lépéseket követve az adatszolgáltatók továbbra is megoszthatják az adatokat az adatfogyasztóikkal abban az esetben, ha az adatközpontok kimaradása az elsődleges régió, amely az adatmegosztást üzemelteti. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Az Azure Data Share üzletmenet-folytonosságának elérése

Az adatközpont-kimaradásra való felkészüléshez az adatszolgáltató egy másodlagos régióban kiépített adatmegosztási környezettel rendelkezhet. Vannak olyan intézkedések, amelyeket meg lehet tenni, amelyek biztosítják a zökkenőmentes feladatátvételt abban az esetben, ha egy adatközpont-kimaradás történik. 

Az adatszolgáltatók másodlagos Azure-adatmegosztási erőforrásokat építhetnek ki egy további régióban. Ezek az adatmegosztási erőforrások beállíthatók úgy, hogy tartalmazzák az elsődleges adatmegosztási környezetben létező adatkészleteket. Az adatfogyasztók at hozzáadható az adatmegosztáshoz a VÉSZ-környezet konfigurálásakor, vagy egy későbbi időpontban (azaz a manuális feladatátvételi lépések részeként).

Ha az adatfogyasztók egy aktív megosztási előfizetéssel rendelkeznek egy dr célokra kiépített másodlagos környezetben, engedélyezhetik a pillanatkép-ütemezést egy feladatátvétel részeként. Ha az adatfogyasztók nem akarnak előfizetni egy másodlagos régiódr célra, akkor meghívást kaphatnak a másodlagos adatmegosztásegy későbbi időpontban. 

Az adatfogyasztók rendelkezhetnek egy aktív megosztási előfizetéssel, amely vész-végrehajtási célokra tétlen, vagy az adatszolgáltatók a manuális feladatátvételi eljárások részeként egy későbbi időpontban is hozzáadhatják őket. 

## <a name="related-information"></a>Kapcsolódó információk

- [Üzletmenet folytonossága és vészhelyreállítás](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [A magas rendelkezésre állás beépítése a BCDR-stratégiába](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>További lépések

Az adatok megosztásának megkezdéséhez folytassa az [adatkezelési oktatóanyag megosztásával.](share-your-data.md)




