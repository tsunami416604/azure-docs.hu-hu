---
title: IBM számítási feladatok az Azure-ban | Microsoft Docs
description: A Microsoft partnereitől származó mainframe-emulátorral és más szolgáltatásokkal áthelyezheti az IBM z/OS munkaterheléseket Microsoft Azure használatával.
services: virtual-machines-linux
ms.service: virtual-machines-linux
ms.subservice: workloads
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: efc4f6293623278314df3ac55b2fecae2e28f7d8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968349"
---
# <a name="ibm-workloads-on-azure"></a>IBM számítási feladatok az Azure-ban

Számos, a z/OS-alapú IBM-es nagyszámítógépes számítási feladat replikálható az Azure-ban a funkciók elvesztése nélkül, és anélkül, hogy a felhasználók még a mögöttes rendszerek változásait is észrevették. Az Azure-beli alkalmazások újraüzemeltetése biztosítja a szükséges nagyvállalati funkciókat, valamint a felhő rugalmasságát, rendelkezésre állását és lehetséges költségmegtakarítását.

Az Azure támogatja az integrációt a meglévő IBM mainframe-környezetekkel, lehetővé téve a applicates, hogy szükség esetén hibrid megoldásokat futtasson, és az idő múlásával áttelepítse azokat. Habár teljes mértékben újraírhatók a meglévő Nagyvállalati alkalmazások az Azure-hoz, sokkal gyakoribb, hogy áthelyezi őket. Az újraírással az áttelepítési projektek költségeit, összetettségét és időpontját adhatja meg. Az újraüzemeltetéssel a következőket teheti:

- Alkalmazások áthelyezése felhőalapú emulátorba.

- Telepítse át az adatbázist egy felhőalapú adatbázisba.

- Cserélje le a modulokat és a kódokat a Code Transformation Engines használatával.

Ezen kívül az IBM-szoftverek, például a WebSphere és az MQ, már az Azure piactéren is elérhetők. Az IBM-szoftverek licence lehetővé teszi az Azure által biztosított igény szerinti infrastruktúra-méretezés előnyeit a virtuális gépek gyors elindításához.

Egy kiterjedt partneri ökoszisztéma segíti az IBM mainframe rendszerek áttelepíthetők az Azure-ba. A legtöbb esetben az újraírás vagy az alkalmazások cseréjének fokozatos üzembe helyezése előtt a lehető legalkalmasabb módszert kell alkalmazni az újrafelhasználásra. További útmutatást és segítséget nyújt a partnereknek az [Azure mainframe Migration Centerben](https://azure.microsoft.com/migration/mainframe/).

**Következő lépések**

- [Mainframe-áttelepítés: mítoszok és tények](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Az IBM&T fejlesztési és tesztelési környezet telepítése az Azure-ban](./install-ibm-z-environment.md)
- [Alkalmazás-fejlesztőknek vezérelt terjesztés (ADCD) beállítása az IBM&T v1-ben](./demo.md)
- [IBM DB2-pureScale az Azure-ban](../../../linux/ibm-db2-purescale-azure.md)
