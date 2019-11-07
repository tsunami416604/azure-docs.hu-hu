---
title: Az adatok áttelepíthetők az Azure-ba és az adattárházból a Azure Data Factory használatával
description: Az adatok áttelepíthetők az Azure-ba a Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 7/30/2019
ms.openlocfilehash: 0be9cbc9c5af2e0778654ef70c5350b48f10c35d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73675761"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Az adatok áttelepíthetők a Azure Data Factory használatával az Azure-ba vagy az adattárházból

Ha át szeretné telepíteni a (Microsoft Azure) vagy a vállalati adattárházat (EDW), vegye fontolóra Azure Data Factory használatát. A Azure Data Factory a következő esetekben alkalmas:

- Big adatmunkaterhelés-áttelepítés az Amazon Simple Storage szolgáltatásból (Amazon S3) vagy egy helyszíni Hadoop elosztott fájlrendszer (HDFS) az Azure-ba
- EDW-áttelepítés Oracle-Exadata, Netezza, Teradata vagy Amazon vöröseltolódásról az Azure-ba

A Azure Data Factory áthelyezheti a petabájt (PB) adatait a Lake Migration adatforgalmához, és több tízezer terabájt (TB) adat adattárház-áttelepítéshez.

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Miért Azure Data Factory használható az adatáttelepítéshez

- A Azure Data Factory könnyedén méretezheti a feldolgozási teljesítmény mennyiségét, hogy kiszolgáló nélküli módon helyezze át az adatátvitelt a nagy teljesítmény, a rugalmasság és a méretezhetőség révén. És csak a ténylegesen használt funkciókért kell fizetnie. Vegye figyelembe a következőket is: 
  - Azure Data Factory nem rendelkezik az adatmennyiséggel vagy a fájlok számával kapcsolatos korlátozásokkal.
  - A Azure Data Factory teljes mértékben használhatja a hálózat és a tároló sávszélességét, hogy a lehető legnagyobb mennyiségű adatátviteli sebességet tudja elérni a környezetben.
  - A Azure Data Factory utólagos elszámolású módszert használ, így csak az Azure-ba történő adatáttelepítés során ténylegesen használt idő után kell fizetnie.  
- Azure Data Factory egyszerre egy egyszeri betöltést és ütemezett növekményes terhelést is végrehajthat.
- A Azure Data Factory az Azure Integration Runtime (IR) használatával helyezi át az adatátvitelt a nyilvánosan elérhető adattó és a raktári végpontok között. Emellett saját üzemeltetésű integrációs modult is használhat az Azure Virtual Network (VNet) vagy egy tűzfal mögött található adattó-és raktár-végpontok adatáthelyezéséhez.
- A Azure Data Factory nagyvállalati szintű biztonsággal rendelkezik: az Windows Installer (MSI) vagy a szolgáltatás identitását használhatja a biztonságos szolgáltatások közötti integrációhoz, vagy használhat Azure Key Vault a hitelesítő adatok kezeléséhez.
- A Azure Data Factory egy ingyenes, beépített monitorozási irányítópultot biztosít a kód nélküli szerzői műveletekhez.  

## <a name="online-vs-offline-data-migration"></a>Online és offline adatáttelepítés

A Azure Data Factory egy szabványos Online áttelepítési eszköz, amely hálózaton keresztül (Internet, er vagy VPN) továbbítja az adatok átvitelét. Míg az offline adatok áttelepítése esetén a felhasználók fizikailag szállítanak adatátviteli eszközöket a szervezetéről egy Azure-adatközpontba.  

Az online és az offline áttelepítési módszer közül három fő szempontot kell figyelembe venni:  

- Migrálni kívánt adatméret
- Hálózati sávszélesség
- Áttelepítési ablak

Tegyük fel például, hogy az adatáttelepítés két héten belül történő elvégzéséhez tervezi Azure Data Factory használatát (az *áttelepítési ablak*). Figyelje meg a rózsaszín/kék határvonalat a következő táblázatban. Az adott oszlop legalacsonyabb rózsaszín cellája azt az adatméretet/hálózati sávszélesség-párosítást jeleníti meg, amelynek áttelepítési ablaka a legközelebb van, de kevesebb, mint két hét. (A kék cellában lévő bármilyen méretű vagy sávszélességű párosítás Online áttelepítési időszaka több mint két hétig tart.) 

![online vagy offline](media/data-migration-guidance-overview/online-offline.png) a táblázat segítségével meghatározhatja, hogy a tervezett áttelepítési időszakot az adatmennyiség és a rendelkezésre álló hálózati sávszélesség alapján kell-e teljesíteni az online áttelepítés (Azure Data Factory) használatával. Ha az online áttelepítési ablak kettőnél több hetet használ, offline áttelepítést érdemes használni.

> [!NOTE]
> Az online áttelepítés használatával a korábbi és a növekményes adatbevitelt is elérheti egyetlen eszközön keresztül.  Ezzel a megközelítéssel az adatokat szinkronizálni lehet a meglévő tároló és az új áruház között a teljes áttelepítési időszak alatt. Ez azt jelenti, hogy újraépítheti az ETL-logikát az új tárolóban a frissített adatértékekkel.


## <a name="next-steps"></a>További lépések

- [Adatok migrálása az AWS S3-ből az Azure-ba](data-migration-guidance-s3-azure-storage.md)
- [Adatok migrálása helyszíni Hadoop-fürtről az Azure-ba](data-migration-guidance-hdfs-azure-storage.md)
- [Adatok migrálása a helyszíni Netezza-kiszolgálóról az Azure-ba](data-migration-guidance-netezza-azure-sqldw.md)
