---
title: A fiók kezelése - Azure Batch | Microsoft dokumentumok
description: Ismerje meg, mi tartalmaz egy Azure Batch-fiókot
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
ms.date: 03/30/2020
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ecae47f6aa0ab3f179632467b7da7805f06162d6
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397267"
---
# <a name="manage-your-batch-account"></a>A Batch-fiók kezelése

A Batch-fiókok a Batch szolgáltatáson belül egyedileg azonosított entitások. Minden feldolgozás Batch-fiókkal van társítva.

Azure Batch-fiókot az [Azure Portalon](batch-account-create-portal.md)hozhat létre, vagy programozott módon, például a [Batch Management .NET könyvtár](batch-management-dotnet.md) használatával. Egy fiók létrehozásakor társíthatja azt egy Azure Storage-fiókhoz, a feladatokkal kapcsolatos bemeneti vagy kimeneti adatok, illetve alkalmazások tárolása céljából.

Több Batch számítási feladatot is futtathat egyetlen Batch-fiókon, de el is oszthatja a számítási feladatokat ugyanazon előfizetéshez, de különböző Azure-régiókhoz tartozó Batch-fiókok között.

## <a name="components-of-the-batch-account"></a>A Batch számla összetevői

A Batch-fiók lehetővé teszi, hogy az Azure-ban hatékonyan futtasson nagyméretű párhuzamos és nagy teljesítményű számítási (HPC) kötegelt feladatokat. A kezelt fiókon belül:

- A futtatott alkalmazások

- A készletek és csomópontok elosztása a készleteken belül

- A feladatok száma és típusa 

- Az adatok bemenete és kimenete. A feladatok kezeléséhez nem kell további szoftvereket telepítenie.

- A Batch-fiók létrehozásakor a rendszer arra kéri, hogy rendeljen hozzá nevet. Ez a név az azonosítója, és a hozzárendelt név nem módosítható.

- A fiók nevének módosításához törölnie kell azt, és létre kell hoznia egy új Batch-fiókot.

- A fiók a használni kívánt előfizetésen belül jön létre.

- A fiók segítségével azonosíthatja és lekérheti az elsődleges és másodlagos fiókkulcsokat az előfizetés bármely Batch-fiókjából.

- A fiók a készletfelosztással és az alapkvótákkal kapcsolatos információkat tartja fenn.  

- A fiók helyadatokat tartalmaz.

- A fiók azonosítja a tárfiókot.

## <a name="next-steps"></a>További lépések

- Batch-fiók létrehozása az [Azure Portalhasználatával.](batch-account-create-portal.md)
- Hozzon létre egy Batch-fiókot programozott módon, például a [Kötegkezelés .NET könyvtárral.](batch-management-dotnet.md)
- [Konfigurálja vagy tiltsa le a távoli hozzáférést az Azure Batch-készlet számítási csomópontjaihoz.](pool-endpoint-configuration.md)
- [Feladat-előkészítési és feladatkiadási feladatok futtatása kötegelt számítási csomópontokon](batch-job-prep-release.md)

