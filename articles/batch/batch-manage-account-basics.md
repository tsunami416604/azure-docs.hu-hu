---
title: Fiók kezelése – Azure Batch | Microsoft Docs
description: Megtudhatja, mi tartalmaz egy Azure Batch fiókot
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 72630a2003b63e60ba79882e1861283173840425
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375186"
---
# <a name="manage-your-batch-account"></a>A Batch-fiók kezelése

A Batch-fiókok a Batch szolgáltatáson belül egyedileg azonosított entitások. Minden feldolgozás Batch-fiókkal van társítva.

Azure Batch-fiókot az [Azure Portalon](batch-account-create-portal.md)hozhat létre, vagy programozott módon, például a [Batch Management .NET könyvtár](batch-management-dotnet.md) használatával. Egy fiók létrehozásakor társíthatja azt egy Azure Storage-fiókhoz, a feladatokkal kapcsolatos bemeneti vagy kimeneti adatok, illetve alkalmazások tárolása céljából.

Több Batch számítási feladatot is futtathat egyetlen Batch-fiókon, de el is oszthatja a számítási feladatokat ugyanazon előfizetéshez, de különböző Azure-régiókhoz tartozó Batch-fiókok között.

## <a name="components-of-the-batch-account"></a>A Batch-fiók összetevői

A Batch-fiók lehetővé teszi, hogy hatékonyan futtasson nagy méretű párhuzamos és nagy teljesítményű számítástechnikai (HPC) kötegelt feladatokat az Azure-ban. A felügyelt fiókon belül:

- a futtatott alkalmazások

- készletek és csomópontok kiosztása a készleteken belül

- a feladatok száma és típusai 

- az adatok bemenete és kimenete. A feladatok kezeléséhez nincs szükség további szoftver telepítésére.

- A Batch-fiók létrehozásakor a rendszer megkéri, hogy rendeljen hozzá egy nevet. Ez a név megegyezik az AZONOSÍTÓval, és a hozzárendelés után nem módosítható.

- Egy fiók nevének módosításához törölnie kell, és létre kell hoznia egy új batch-fiókot.

- A fiók a használni kívánt előfizetésen belül jön létre.

- A fiók használatával azonosíthatja és lekérheti az elsődleges és a másodlagos fiókok kulcsait az előfizetésében található bármely batch-fiókból.

- A fiók a készlet kiosztásával és a fő kvótákkal kapcsolatos információkat tart fenn.  

- A fiók a hely adatait tartalmazza.

- A fiók azonosítja a Storage-fiókját.

## <a name="next-steps"></a>Következő lépések

- Hozzon létre egy batch-fiókot a [Azure Portal](batch-account-create-portal.md)használatával.
- Hozzon létre egy batch-fiókot programozott módon, például a [Batch Management .net könyvtárával](batch-management-dotnet.md).
- A [Azure batch-készletben lévő számítási csomópontok távoli elérésének konfigurálása vagy letiltása](pool-endpoint-configuration.md).
- [Feladat-előkészítési és feladat-kiadási feladatok futtatása kötegelt számítási csomópontokon](batch-job-prep-release.md)
