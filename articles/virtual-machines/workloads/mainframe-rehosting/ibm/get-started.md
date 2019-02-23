---
title: IBM-munkaterhelések Azure-on |} A Microsoft Docs
description: A nagyszámítógépes emulátor és egyéb szolgáltatások Microsoft-partnerek segítségével áthelyezési az IBM z/OS-alapú számítási feladatokat a Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: ccecfd6abe0e9418e217ad7f200ce980d73c7b45
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56739643"
---
# <a name="ibm-workloads-on-azure"></a>IBM-munkaterhelések Azure-ban

Számos IBM Nagyszámítógépek számítási feladatok alapján az operációs rendszer z/a funkciókat biztosítja, és anélkül, hogy a felhasználók akkor is észre a módosításokat az alapul szolgáló rendszerekben adatvesztés nélkül replikálhatók az Azure-ban. Azure-beli alkalmazások újratárolása biztosítja a rugalmasságot, rendelkezésre állást, valamint szükséges nagyszámítógépes hasonló funkciót, és potenciális költségcsökkentést kihasználhatja a felhőben.

Az Azure támogatja a integráció meglévő IBM Nagyszámítógépek környezet, áttelepítése a applicates, amelyek jelentéssel bírnak, lehetővé téve a hibrid megoldások futtassa, ha szükséges, és migrálja a idővel. Bár a teljes egészében az Azure-hoz írja újra meglévő nagyszámítógép-alapú alkalmazásokat, jelenleg egyre gyakoribb, az áthelyezési őket. Az újraírás hozzáadja költség, a bonyolultságát és az idő migration-projektek. Újratárolása, a következőket teheti:

- Áthelyezhet egy felhőalapú emulátor alkalmazásokat.

- Telepítse át az adatbázist egy felhőalapú adatbázis.

- Cserélje le a modulok és a kód átalakító motor használatával.

Ezenkívül IBM-szoftvereket, köztük a WebSphere és az MQ, már az Azure piactéren. mert ha IBM-szoftverlicenccel rendelkezik, kihasználhatja az Azure által kínált igény szerint skálázható infrastruktúra előnyeit, és gyorsan üzembe helyezhet egy virtuális gépet.

Egy kiterjedt partneri ökoszisztéma, amelyik segítségére lehet IBM Nagyszámítógépek rendszerek Azure-ban érhető el. A legtöbb hajtsa végre egy pragmatikus újból megközelítését, amikor lehetséges alkalmazná a újraírását, vagy cserélje le az alkalmazások fázisokra bontva történő üzembe helyezése előtt. További útmutatást kaphat, és a partnerek segítségével a [Azure nagyszámítógépes Migrálási központ](https://azure.microsoft.com/migration/mainframe/).

**Következő lépések**

- [Nagyszámítógépek migrálása: tévhitet és tények](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM zD & T fejlesztési-tesztelési környezet telepítése az Azure-ban](./install-ibm-z-environment.md)
- [Állítsa be az alkalmazás fejlesztők szabályozott terjesztési (ADCD) IBM zD & T v1](./demo.md)
- [IBM DB2-höz pureScale az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
