---
title: Vész-helyreállítási Azure-adatmegosztás esetén
description: Vész-helyreállítási Azure-adatmegosztás esetén
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 61804aaf65a97485e2b2b5bb4869c335a14ce812
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513558"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Vész-helyreállítási Azure-adatmegosztás esetén

Ez a cikk azt ismerteti, hogyan konfigurálhatja a vész-helyreállítási környezetet az Azure-adatmegosztáshoz. Az Azure adatközpont-kimaradások ritkán fordulnak elő, de akár néhány perctől akár órákig is tarthatnak. Az adatközpont-kimaradások olyan környezetek megszakadását okozhatják, amelyek az adatszolgáltató által megosztott adatkezelési lehetőségekre támaszkodnak. A jelen cikkben ismertetett lépéseket követve az adatszolgáltatók továbbra is megoszthatják az adataikat az adatfogyasztókkal az adatmegosztást üzemeltető elsődleges régió adatközpont-kimaradása esetén. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Az üzletmenet folytonosságának elérése az Azure-adatmegosztáshoz

Az adatközpont-kimaradás előkészítéséhez az adatszolgáltatónak lehet egy másodlagos régióban kiépített adatmegosztási környezete. Az adatközpontok meghibásodása esetén szükséges a zökkenőmentes feladatátvétel biztosítása. 

Az adatszolgáltatók másodlagos Azure-beli adatmegosztási erőforrásokat helyezhetnek üzembe egy további régióban. Ezek az adatmegosztási erőforrások úgy konfigurálhatók, hogy az elsődleges Azure-beli adatmegosztási erőforrásban található megosztásokat és adatkészleteket is tartalmazzák. A DR környezet vagy egy későbbi időpontban, a másodlagos megosztásokhoz is meghívhatnak adatfogyasztókat (azaz a manuális feladatátvétel lépéseinek részeként).

Ha az adatfogyasztók aktív megosztás-előfizetésekkel rendelkeznek a DR célra kiépített másodlagos környezetben, a feladatátvétel részeként engedélyezheti a pillanatkép-ütemtervet. Ha az adatfogyasztók nem kívánnak előfizetni egy másodlagos régióba DR célra, akkor később meghívhatják a másodlagos megosztást. 

Az adatfogyasztók használhatnak olyan aktív megosztási előfizetést, amely DR célra tétlen, vagy az adatszolgáltatók a manuális feladatátvételi eljárások részeként később is meghívhatják őket. 

## <a name="related-information"></a>Kapcsolódó információk

- [Üzletmenet-folytonosság és vész-helyreállítás](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [A magas rendelkezésre állás beépítése a BCDR-stratégiába](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>További lépések

Az adatmegosztás megkezdésének megismeréséhez folytassa az [adatgyűjtés megosztása](share-your-data.md) című oktatóanyagot.




