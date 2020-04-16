---
title: Betöltés az Azure Data Lake Storage Gen2 szolgáltatásba az Azure Synapse Analytics szolgáltatásban
description: Ismerje meg, hogyan lehet adatokat beadni az Azure Data Lake Storage Gen2 szolgáltatásba az Azure Synapse Analytics szolgáltatásban
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 4d7d7be523749797e5dbce0e50c307fc682974f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430577"
---
# <a name="ingesting-data-into-azure-data-lake-storage-gen2"></a>Adatok betöltése az Azure Data Lake Storage Gen2 szolgáltatásba 

Ebben a cikkben megtudhatja, hogyan lehet adatokat beadni egyik helyről a másikra egy Azure Data Lake Gen 2 (Azure Data Lake Gen 2) tárfiókban az Azure Synapse Analytics használatával.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés:** Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes Azure-fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
* **Azure Storage-fiók:** Az Azure Data Lake Gen *2-t használja* forrásadattárként. Ha nem rendelkezik tárfiókkal, olvassa [el az Azure Storage-fiók létrehozása](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) című témakört a létrehozási lépésekért.

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

Az Azure Synapse Analytics egy összekapcsolt szolgáltatás, ahol meghatározza a kapcsolat adatait más szolgáltatásokkal. Ebben a szakaszban az Azure Synapse Analytics és az Azure Data Lake Gen 2 csatolt szolgáltatásokként.

1. Nyissa meg az Azure Synapse Analytics felhasználói felületét, és nyissa meg a **Kezelés** lapot.
1. A **Külső kapcsolatok csoportban**válassza a **Csatolt szolgáltatások**lehetőséget.
1. Csatolt szolgáltatás hozzáadásához kattintson az **Új**gombra.
1. Válassza ki az Azure Data Lake Storage Gen2 csempét a listából, és kattintson a **Folytatás**gombra.
1. Adja meg a hitelesítési hitelesítő adatokat. A fiókkulcs, az egyszerű szolgáltatás és a felügyelt identitás jelenleg támogatott hitelesítési típusok. Kattintson a kapcsolat tesztelésére, és ellenőrizze, hogy a hitelesítő adatok helyesek-e. 
1. Ha végzett, kattintson a **Létrehozás** gombra.

## <a name="create-pipeline"></a>Folyamat létrehozása

A folyamat egy tevékenységcsoport végrehajtásának logikai folyamatát tartalmazza. Ebben a szakaszban hozzon létre egy folyamatot, amely egy másolási tevékenységet tartalmaz, amely az Azure Data Lake Gen 2 adatait egy SQL-készletbe továbbítja.

1. Lépjen az **Orchestrate** lapra. Kattintson a csővezetékek fejléce melletti plusz ikonra, és válassza a **Folyamat lehetőséget.**
1. A Tevékenységek ablaktábla **Áthelyezés és átalakítás** csoportban húzza az Adatok **másolását** a folyamatvászonra.
1. Kattintson a másolási tevékenységre, és **New** lépjen a **Forrás** lapra.
1. Válassza ki az Azure Data Lake Storage Gen2-t az adattárként, és kattintson a folytatás gombra.
1. Formátumként válassza a DelimitedText (Tagolt szöveg) lehetőséget, majd kattintson a folytatás gombra.
1. A készlet tulajdonságok ablaktábláján jelölje ki a létrehozott ADLS csatolt szolgáltatást. Adja meg a forrásadatok fájlelérési útját, és adja meg, hogy az első sornak van-e fejléce. A sémát importálhatja a fájltárolóból vagy egy mintafájlból. Ha végzett, kattintson az OK gombra.
1. Új **fogadóadatkészlet** létrehozásához kattintson az **Új** gombra.
1. Válassza ki az Azure Data Lake Storage gen2-t az adattárként, és kattintson a folytatás gombra.
1. Formátumként válassza a DelimitedText (Tagolt szöveg) lehetőséget, majd kattintson a folytatás gombra.
1. A készlet tulajdonságok ablaktábláján jelölje ki a létrehozott ADLS csatolt szolgáltatást. Adja meg annak a mappának az elérési útját, amelyben adatokat szeretne írni. Ha végzett, kattintson az OK gombra.

## <a name="debug-and-publish-pipeline"></a>Hibakeresési és közzétételi folyamat

Miután befejezte a folyamat konfigurálását, végrehajthatja a hibakeresési futtatást, mielőtt közzéteszi az összetevőket, hogy ellenőrizze, hogy minden helyes-e.

1. A folyamat hibakereséséhez válassza a **Hibakeresés** elemet az eszköztáron. A folyamat futtatási állapotát az ablak alján található **Kimenet** lapon tekintheti meg. 
1. Miután a folyamat sikeresen lefutott, a felső eszköztáron válassza az **Összes közzététele**lehetőséget. Ez a művelet közzéteszi a Synapse Analytics szolgáltatáshoz létrehozott entitásokat (adatkészleteket és folyamatokat).
1. Várjon, amíg megjelenik a **Sikeres közzététel** üzenet. Az értesítési üzenetek megtekintéséhez kattintson a jobb felső sarokban lévő csengő gombra. 


## <a name="trigger-and-monitor-the-pipeline"></a>A folyamat aktiválása és figyelése

Ebben a lépésben manuálisan indítsa el az előző lépésben közzétett folyamatot. 

1. Az eszköztáron válassza az **Eseményindító hozzáadása** lehetőséget, majd az **Eseményindító most**lehetőséget. A **Folyamatfuttatás** lapon válassza a **Befejezés** elemet.  
1. Lépjen a bal oldali **oldalsávon** található Monitor fülre. Itt láthat egy manuális eseményindító által aktivált folyamatfuttatást. A **Műveletek** oszlopban lévő hivatkozások segítségével megtekintheti a tevékenység részleteit, és újra futtathatja a folyamatot.
1. A folyamat futásához társított tevékenységfuttatások megtekintéséhez kattintson a **Műveletek** oszlopban található **Tevékenységfuttatások megtekintése** hivatkozásra. Ebben a példában csak egy tevékenység van, így csak egy bejegyzés jelenik meg a listában. A másolási művelet részleteinek megtekintéséhez válassza a **Műveletek** oszlop **Részletek** hivatkozását (szemüveg ikon). Válassza **a folyamatfuttatások** felüli kiválasztásával lépjen vissza a folyamatfuttatások nézetbe. A nézet frissítéséhez válassza a **Frissítés** parancsot.
1. Ellenőrizze, hogy az adatok helyesen vannak-e beírva az SQL-készletbe.


## <a name="next-steps"></a>További lépések

A Synapse Analytics adatintegrációjáról további információt az [adatok sql készletbe való betöltése](data-integration-sql-pool.md) című cikkben talál.
