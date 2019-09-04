---
title: Az adatok áttelepíthetők az Azure-ba és az adattárházból az Azure-ba a Azure Data Factory használatával | Microsoft Docs
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
ms.openlocfilehash: 937a076b3e0e3c5170779d3449776f0aa1cf5b49
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259001"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Az adatok áttelepíthetők a Azure Data Factory használatával az Azure-ba vagy az adattárházból 

Azure Data Factory az adatáttelepítés elvégzésére szolgáló eszköz lehet, ha a (EDW) adattárat vagy vállalati adattárházat (Azure) át szeretné telepíteni. A (z) és az adatraktár-áttelepítés a következő forgatókönyvekhez kapcsolódik: 

- Big adatszámítási feladatok migrálása az AWS S3, helyszíni Hadoop-fájlrendszerről az Azure-ba. 
- EDW az Oracle Exadata, a Netezza, a Teradata, az AWS vöröseltolódásról az Azure-ba. 

A Azure Data Factory a adat-és adattárház-áttelepítésre vonatkozó több tízezer adat adatforgalmának áttelepítésére is képes. 

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Miért Azure Data Factory használható az adatáttelepítéshez 

- A Azure Data Factory könnyedén méretezheti a lóerős mennyiségét, hogy kiszolgáló nélküli módon helyezze át az adatátvitelt a nagy teljesítmény, a rugalmasság és a méretezhetőség érdekében, és csak a ténylegesen használt funkciókért kell fizetnie.  
  - Azure Data Factory nem korlátozható az adatmennyiség és a fájlok száma.
  - A Azure Data Factory 100%-kal a hálózat és a tároló sávszélességét használja a legmagasabb adatátviteli sebesség eléréséhez a környezetben.   
  - Azure Data Factory az utólagos elszámolású stratégiát követi, így csak akkor kell fizetnie, amikor az Azure-ba történő adatáttelepítés során Azure Data Factoryt használ.  
- A Azure Data Factory képes egyszeri betöltést és ütemezett növekményes terhelést végezni. 
- A Azure Data Factory a Azure IR használja az adatáthelyezéshez a nyilvánosan elérhető adattó/raktári végpontok között, vagy ha a saját üzemeltetésű integrációs modult használja a VNet-ben vagy a tűzfal mögött található adat-Lake/Warehouse-végpontok adatáthelyezéséhez. 
- A Azure Data Factory nagyvállalati szintű biztonsággal rendelkezik: vagy az MSI-t vagy a szolgáltatás identitását használja a biztonságos szolgáltatások közötti integrációhoz, vagy a Azure Key Vault a hitelesítő adatok kezeléséhez. 
- A Azure Data Factory egy ingyenes, beépített monitorozási irányítópultot biztosít a kód nélküli szerzői műveletekhez.  

## <a name="online-vs-offline-data-migration"></a>Online és offline adatáttelepítés

A Azure Data Factory egy tipikus online adatáttelepítési eszköz, amellyel az adatok átvitele a hálózaton keresztül történik (Internet, ÖÖÖ vagy VPN), ahol az offline adatáttelepítés lehetővé teszi, hogy az emberek fizikailag a szervezettől az Azure-adatközpontba szállítják az adatátviteli eszközöket.  

Az online és az offline áttelepítési módszer kiválasztásakor három fontos szempontot kell figyelembe venni:  

- Az áttelepítendő adatméret. 
- Hálózati sávszélesség. 
- Áttelepítési ablak.   

Ha két héten belül szeretné végrehajtani az adatáttelepítést (áttelepítési ablak), az alábbi képen egy kivágási sor jelenik meg, amely azt mutatja be, hogy mikor érdemes az online áttelepítési eszközt (Azure Data Factory) különböző adatmérettel és hálózati sávszélességgel használni.   

![online vagy offline](media/data-migration-guidance-overview/online-offline.png)

> [!NOTE]
> Az online áttelepítési módszer előnye, hogy a korábbi betöltési és a növekményes csatornák egyetlen eszközön is elérhetők.  Így az adatokat szinkronizálni lehet a meglévő és az új tároló között a teljes áttelepítési időszak alatt, így az új áruházban újraépítheti az ETL-logikát a frissített adattal. 


## <a name="next-steps"></a>További lépések

- [Adatok migrálása az AWS S3-ből az Azure-ba](data-migration-guidance-s3-azure-storage.md)
- [Adatok migrálása helyszíni Hadoop-fürtről az Azure-ba](data-migration-guidance-hdfs-azure-storage.md)
- [Adatok migrálása a helyszíni Netezza-kiszolgálóról az Azure-ba](data-migration-guidance-netezza-azure-sqldw.md)
