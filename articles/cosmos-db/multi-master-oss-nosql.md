---
title: Azure Cosmos DB több főkiszolgálós használata a nyílt forráskódú NoSQL-adatbázisok
description: ''
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ea3a16cf6b5aa4e46ad401e59aacb4d936a7429a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963900"
---
# <a name="using-azure-cosmos-db-multi-master-with-open-source-nosql-databases"></a>Azure Cosmos DB több főkiszolgálós használata a nyílt forráskódú NoSQL-adatbázisok

Az Azure Cosmos DB több főkiszolgálós támogatást a natív kiszolgálóoldali megvalósítás, amely a nyílt forráskódú nosql-alapú ajánlatokat Cosmos DB által támogatott érhető el. Is elérhető mind a Cosmos DB támogatott SDK-k.

Az Azure Cosmos DB az első szolgáltatása a világ több főkiszolgálós támogatást, ezek nyílt forráskódú NoSQL-adatbázisok.

|Modell  |Támogatás  |
|---------|---------|
|MongoDB  | Aktív-aktív  |
|Graph  | Aktív-aktív |
|Cassandra  | Aktív-aktív   |

## <a name="use-mongodb-clients-with-multi-master"></a>Használja a MongoDB-ügyfelekből több főkiszolgálós

A több főkiszolgálós funkció engedélyezve van a MongoDB API-fiókok ugyanúgy más az Azure Cosmos DB API-k engedélyezve van. Miután engedélyezte a MongoDB API-fiókok több főkiszolgálós, minden példány egy ügyfélalkalmazás is válassza ki az elsődleges régió olvasást és írást. A MongoDB-illesztőprogram tervvel az elsődleges régió jelenik meg az ügyfél beállítása elsődleges replikaként. Ily módon az elosztott adatbázis minden régióban állítsa be az elsődleges replika működhet. Az Azure Cosmos DB több főkiszolgálós jelentősen csökkentheti az írási késése, globálisan elosztott MongoDB alkalmazásai számára teszi lehetővé. 

### <a name="set-the-primary-region"></a>Állítsa az elsődleges régió

Minden példánya egy MongoDB-ügyfelet is válassza ki az elsődleges régió hozzáfűzésével `@<preferred_primary_region_name>` az "application name" mezőben fogadja el a MongoDB-ügyfél illesztőprogramját. A legtöbb illesztőprogramok fogadása a kapcsolati karakterláncban, például:

`mongodb://fabrikam:KEY@fabrikam.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&appname=@West US`

Miután csatlakozott, egyet a következő esetekben fordulhat elő:

* Ha az elsődleges régió neve érhető el, mint egy írási régiót, majd az Azure Cosmos DB kijelöli azt, az elsődleges régió.

* Ha egy kívánt régióhoz neve nincs megadva, majd az Azure Cosmos DB választja egy alapértelmezett régiójához, az elsődleges régió.

* Ha egy kívánt régióhoz név van megadva, de nem érhető el, egy írási régió esetében az adatbázis-fiókot, Azure Cosmos DB válassza a legközelebbi írási régió, amely az elsődleges régióban érhető el.

Egyes illesztőprogramok, mint például a NodeJS-illesztőprogramjának első probléma ír a gazdagép mindig lesz megadva a kezdeti kapcsolati karakterláncot. Az ilyen illesztőprogramokat írásokat a rendszer átirányítja az elsődleges régióban, mellett az alkalmazás nevét, hogy kell módosítania a DNS-nevet a kapcsolati karakterláncot tartalmazza a régió neve belül. Győződjön meg arról, adja meg, hogy a régió nevét, szóközök nélkül. Példa:

`mongodb://fabrikam:KEY@fabrikam-westus.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&appname=@West US`

### <a name="conflict-resolution-mode"></a>Ütközés feloldása mód

Az ütközés feloldása üzemmód az Azure Cosmos DB MongoDB API-fiókok esetében mindig az utolsó író – wins-, a regionális kiszolgáló időbélyeg, amely fogadja az írási használatával.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett az Azure Cosmos DB MongoDB API-fiókok több főkiszolgálós támogatást. Ezután keresse meg az alábbi forrásanyagokat:

* [Azure Cosmos DB-fiókok több főkiszolgálós engedélyezése](enable-multi-master.md)

* [Az Azure Cosmos DB ütközésfeloldás ismertetése](multi-master-conflict-resolution.md)
