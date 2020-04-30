---
title: Saját fiók kezelése
description: Megtudhatja, mi tartalmaz egy Azure Batch fiókot
ms.topic: conceptual
ms.date: 03/30/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ce0c612e3434249c0c7d078949b10e0b9f6b1f10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82116009"
---
# <a name="manage-your-batch-account"></a>A Batch-fiók kezelése

A Batch-fiókok a Batch szolgáltatáson belül egyedileg azonosított entitások. Minden feldolgozás Batch-fiókkal van társítva.

Azure Batch-fiókot az [Azure Portalon](batch-account-create-portal.md)hozhat létre, vagy programozott módon, például a [Batch Management .NET könyvtár](batch-management-dotnet.md) használatával. Egy fiók létrehozásakor társíthatja azt egy Azure Storage-fiókhoz, a feladatokkal kapcsolatos bemeneti vagy kimeneti adatok, illetve alkalmazások tárolása céljából.

Több Batch számítási feladatot is futtathat egyetlen Batch-fiókon, de el is oszthatja a számítási feladatokat ugyanazon előfizetéshez, de különböző Azure-régiókhoz tartozó Batch-fiókok között.

## <a name="components-of-the-batch-account"></a>A Batch-fiók összetevői

A Batch-fiók lehetővé teszi, hogy hatékonyan futtasson nagy méretű párhuzamos és nagy teljesítményű számítástechnikai (HPC) kötegelt feladatokat az Azure-ban. A felügyelt fiókon belül:

- A futtatott alkalmazások

- Készletek és csomópontok kiosztása a készleteken belül

- A feladatok száma és típusai 

- Az adatok bemenete és kimenete. A feladatok kezeléséhez nincs szükség további szoftver telepítésére.

- A Batch-fiók létrehozásakor a rendszer megkéri, hogy rendeljen hozzá egy nevet. Ez a név megegyezik az AZONOSÍTÓval, és a hozzárendelés után nem módosítható.

- Egy fiók nevének módosításához törölnie kell, és létre kell hoznia egy új batch-fiókot.

- A fiók a használni kívánt előfizetésen belül jön létre.

- A fiók használatával azonosíthatja és lekérheti az elsődleges és a másodlagos fiókok kulcsait az előfizetésében található bármely batch-fiókból.

- A fiók a készlet kiosztásával és a fő kvótákkal kapcsolatos információkat tart fenn.  

- A fiók a hely adatait tartalmazza.

- A fiók azonosítja a Storage-fiókját.

## <a name="next-steps"></a>További lépések

- Hozzon létre egy batch-fiókot a [Azure Portal](batch-account-create-portal.md)használatával.
- Hozzon létre egy batch-fiókot programozott módon, például a [Batch Management .net könyvtárával](batch-management-dotnet.md).
- A [Azure batch-készletben lévő számítási csomópontok távoli elérésének konfigurálása vagy letiltása](pool-endpoint-configuration.md).
- [Feladat-előkészítési és feladat-kiadási feladatok futtatása kötegelt számítási csomópontokon](batch-job-prep-release.md)

