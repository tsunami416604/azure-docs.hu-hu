---
title: Adatok áttelepítése az adattóból és az adattárházból az Azure-ba
description: Az Azure Data Factory segítségével áttelepítheti az adatokat az adattóból és az adattárházból az Azure-ba.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/30/2019
ms.openlocfilehash: 4408546c892299e5bbbc22b00a4b334c36eda616
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416426"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Az Azure Data Factory segítségével áttelepítheti az adatokat az adattóból vagy az adattárházból az Azure-ba

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ha át szeretné telepíteni a data lake vagy a vállalati adattárházat (EDW) a Microsoft Azure-ba, fontolja meg az Azure Data Factory használatát. Az Azure Data Factory a következő esetekben is jól illeszkedik:

- Big data-számítási feladatok áttelepítése az Amazon Simple Storage Service -ből (Amazon S3) vagy egy helyszíni Hadoop Distributed File System (HDFS) rendszerből az Azure-ba
- EDW áttelepítés az Oracle Exadata, a Netezza, a Teradata vagy az Amazon Redshift alkalmazásból az Azure-ba

Az Azure Data Factory áthelyezheti a petabájt (PB) adatok adattótó-áttelepítés, és több tíz terabájt (TB) adatok adattárház áttelepítése.

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Miért használható az Azure Data Factory adatáttelepítéshez?

- Az Azure Data Factory könnyedén felskálázhatja a feldolgozási teljesítményt az adatok kiszolgáló nélküli, nagy teljesítmény, rugalmasság és méretezhetőség érdekében történő mozgatásához. És csak azért fizetsz, amit használsz. Is vegye figyelembe a következőket: 
  - Az Azure Data Factory nincs korlátozás az adatmennyiség re vagy a fájlok számára.
  - Az Azure Data Factory teljes mértékben használhatja a hálózati és tárolási sávszélességet a környezetében a legnagyobb mennyiségű adatátviteli átviteli sebesség eléréséhez.
  - Az Azure Data Factory használatalapú fizetési módot használ, így csak az Azure-ba való adatáttelepítés tényleges futtatásához használt időért kell fizetnie.  
- Az Azure Data Factory egyszeri előzmény- és ütemezett terheléseket is végrehajthat.
- Az Azure Data Factory az Azure-integrációs futásidejű (IR) használatával mozgatja az adatokat a nyilvánosan elérhető adattó- és raktári végpontok között. Saját üzemeltetésű infravörös kapcsolaton kívül is használhatja az adatok tó- és raktárvégpontjainak áthelyezését az Azure virtuális hálózaton (VNet) belül vagy egy tűzfal mögött.
- Az Azure Data Factory nagyvállalati szintű biztonsággal rendelkezik: használhatja a Windows Installer (MSI) vagy a Service Identity biztonságos szolgáltatás-szolgáltatás integráció, vagy az Azure Key Vault a hitelesítő adatok kezeléséhez.
- Az Azure Data Factory kódmentes szerzői élményt és gazdag, beépített figyelési irányítópultot biztosít.  

## <a name="online-vs-offline-data-migration"></a>Online és offline adatáttelepítés

Az Azure Data Factory egy szabványos online adatáttelepítési eszköz az adatok hálózaton (interneten, ER-n vagy VPN-en) keresztül történő átviteléhez. Mivel az offline adatáttelepítés, a felhasználók fizikailag szállít adatátviteli eszközök a szervezet egy Azure Data Center.  

Az online és offline áttelepítési megközelítés kiválasztásakor három fő szempontot kell figyelembe venni:  

- Az áttelepítendő adatok mérete
- Hálózati sávszélesség
- Áttelepítési ablak

Tegyük fel például, hogy az Azure Data Factory használatával két héten belül befejezi az adatáttelepítést (az *áttelepítési ablakot).* Figyelje meg a rózsaszín/kék vágási vonalat az alábbi táblázatban. Az adott oszlop legalacsonyabb rózsaszín cellája azt az adatméretet/hálózati sávszélesség-párosítást mutatja, amelynek áttelepítési ablaka a legközelebb van, de kevesebb, mint két hét. (A kék cellában lévő bármely méret/sávszélesség párosítás több mint két hetes online áttelepítési ablakkal rendelkezik.) 

![online és](media/data-migration-guidance-overview/online-offline.png) offline Ez a táblázat segítségével meghatározhatja, hogy az adatok mérete és a rendelkezésre álló hálózati sávszélesség alapján megfelelhet-e a tervezett áttelepítési ablaknak az online áttelepítés (Azure Data Factory) segítségével. Ha az online áttelepítési ablak több mint két hét, érdemes használni offline áttelepítés.

> [!NOTE]
> Az online áttelepítés használatával a korábbi adatbetöltést és a növekményes hírcsatornákat is elérheti egyetlen eszközön keresztül.  Ezzel a módszersel az adatok a teljes áttelepítési időszakban szinkronizálhatók a meglévő és az új tároló között. Ez azt jelenti, hogy az ETL-logika az új tároló frissített adatokkal újraépíthető.


## <a name="next-steps"></a>További lépések

- [Adatok migrálása az AWS S3-ból az Azure-ba](data-migration-guidance-s3-azure-storage.md)
- [Adatok áttelepítése a helyszíni hadoop-fürtről az Azure-ba](data-migration-guidance-hdfs-azure-storage.md)
- [Adatok migrálása helyszíni Netezza-kiszolgálóról az Azure-ba](data-migration-guidance-netezza-azure-sqldw.md)
