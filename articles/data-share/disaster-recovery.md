---
title: Vész-helyreállítási Azure-adatmegosztás esetén
description: Vész-helyreállítási Azure-adatmegosztás esetén
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: a736e3ddfcf785f9ce27140eed58374a0732c1f1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75483179"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Vész-helyreállítási Azure-adatmegosztás esetén

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a vész-helyreállítási környezetet az Azure-beli adatmegosztáshoz. Az Azure adatközpont-kimaradások ritkán fordulnak elő, de akár néhány perctől akár órákig is tarthatnak. Az adatközpont-kimaradások olyan környezetek megszakadását okozhatják, amelyek az adatszolgáltató által megosztott adatkezelési lehetőségekre támaszkodnak. A jelen cikkben ismertetett lépéseket követve az adatszolgáltatók továbbra is megoszthatják az adataikat az adatfogyasztókkal az adatmegosztást üzemeltető elsődleges régió adatközpont-kimaradása esetén. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Az üzletmenet folytonosságának elérése az Azure-adatmegosztáshoz

Az adatközpont-kimaradás előkészítéséhez az adatszolgáltatónak lehet egy másodlagos régióban kiépített adatmegosztási környezete. Vannak olyan mértékek, amelyek gondoskodnak arról, hogy zökkenőmentes feladatátvétel történjen az adatközpontok meghibásodása esetén. 

Az adatszolgáltatók másodlagos Azure-beli adatmegosztási erőforrásokat helyezhetnek üzembe egy további régióban. Ezek az adatmegosztási erőforrások konfigurálhatók úgy, hogy az elsődleges adatmegosztási környezetben található adatkészleteket is tartalmazzák. Az adatfelhasználók hozzáadhatók az adatmegosztáshoz a DR környezet konfigurálásakor, vagy egy későbbi időpontban (azaz a manuális feladatátvétel lépéseinek részeként).

Ha az adatfogyasztók aktív megosztási előfizetéssel rendelkeznek a DR célra kiépített másodlagos környezetben, akkor a feladatátvétel részeként engedélyezheti a pillanatkép-ütemtervet. Ha az adatfogyasztók nem kívánnak előfizetni egy másodlagos régióba DR célra, akkor később meghívhatják őket a másodlagos adatmegosztásra. 

Az adatfogyasztók rendelkezhetnek olyan aktív megosztási előfizetéssel, amely DR célra tétlen, vagy az adatszolgáltatók a manuális feladatátvételi eljárások részeként egy későbbi időpontban is hozzáadhatják őket. 

## <a name="related-information"></a>Kapcsolódó információk

- [Üzletmenet-folytonosság és vész-helyreállítás](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [A magas rendelkezésre állás beépítése a BCDR-stratégiába](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>További lépések

Az adatmegosztás megkezdésének megismeréséhez folytassa az [adatgyűjtés megosztása](share-your-data.md) című oktatóanyagot.




