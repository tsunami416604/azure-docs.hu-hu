---
title: IBM számítási feladatok az Azure-ban | Microsoft dokumentumok
description: A Microsoft-partnerek nagyszámítógépes emulátorával és egyéb szolgáltatásaival újraüzemeltetheti IBM z/os számítási feladatait a Microsoft Azure használatával.
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 13c83c53cdad719d6a4bed4cc1852b85d62082e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68834583"
---
# <a name="ibm-workloads-on-azure"></a>IBM-munkaterhelések az Azure-ban

Számos, z/OS-alapú IBM nagyszámítógépes számítási feladat replikálható az Azure-ban a funkcionalitás elvesztése nélkül, anélkül, hogy a felhasználók észrevennék az alapul szolgáló rendszerek változásait. Az Azure-beli alkalmazások újraüzemeltetése biztosítja a szükséges nagyszámítógép-szerű funkciókat, valamint a felhő rugalmasságát, rendelkezésre állását és potenciális költségmegtakarítását.

Az Azure támogatja a meglévő IBM nagyszámítógépes környezetekkel való integrációt, lehetővé téve az értelmes alkalmazások áttelepítését, szükség esetén hibrid megoldások futtatását és idővel történő áttelepítését. Bár teljesen átírhatja a meglévő nagyszámítógépes programok az Azure-ban, sokkal gyakoribb, hogy újra üzemelteti őket. Az újraírás növeli a költségeket, az összetettséget és az időt az áttelepítési projektekhez. A rehosting, akkor:

- Alkalmazások áthelyezése felhőalapú emulátorba.

- Az adatbázis áttelepítése felhőalapú adatbázisba.

- Cserélje ki a modulokat és a kódot kódátalakító motorokkal.

Emellett az IBM szoftverei, beleértve a WebSphere-t és az MQ-t, most már az Azure Piactéren vannak. Az IBM szoftverlicenccel kihasználhatja az Azure által biztosított igény szerinti infrastruktúra-skálázást a virtuális gépek gyors indításához.

Kiterjedt partneri ökoszisztéma áll rendelkezésre az IBM nagyszámítógépes rendszerek Azure-ba való áttelepítéséhez. A legtöbb enciklon pragmatikus megközelítést alkalmaz, ahol csak lehetséges, mielőtt az alkalmazások újraírásának vagy cseréjének szakaszos telepítésébe kezdene. További útmutatást és segítséget kaphat a partnerektől az [Azure Mainframe Migration centerben.](https://azure.microsoft.com/migration/mainframe/)

**További lépések**

- [Mainframe migráció: mítoszok és tények](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Az IBM zD&T fejlesztési/tesztelési környezet telepítése az Azure-ra](./install-ibm-z-environment.md)
- [Alkalmazásfejlesztők által vezérelt disztribúció (ADCD) beállítása IBM&T v1-ben](./demo.md)
- [IBM DB2 pureScale az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
