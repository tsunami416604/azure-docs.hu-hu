---
title: Az Azure Data Lake Storage Gen1 vészhelyreállítási útmutató |} A Microsoft Docs
description: Az Azure Data Lake Storage Gen1 vész-helyreállítási útmutató
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: twooley
ms.openlocfilehash: b3f1888a73baf2b7f9efa9f5e7cdb3305aa9f90d
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878111"
---
# <a name="disaster-recovery-guidance-for-data-in-azure-data-lake-storage-gen1"></a>Az Azure Data Lake Storage Gen1 adatok vészhelyreállítási útmutató

Az Azure Data Lake Storage Gen1 nyújt a helyileg redundáns tárolás (LRS). Ezért a Data Lake Storage Gen1 fiók adatait képes legyen ellenállni a átmeneti hardverhibák esetén biztosítja az automatikus replikációval adatközponton belül. Ez biztosítja a tartósság és magas rendelkezésre állás érdekében a Data Lake Storage Gen1 SLA teljesíti. Ez a cikk nyújt útmutatást hogyan védheti még jobban az adatokat a ritka régió kiterjedő ritka kimaradásokkal és véletlen törlésekkel szemben.

## <a name="disaster-recovery-guidance"></a>Vészhelyreállítási útmutató
Minden ügyfél számára kritikus fontosságú, hogy előkészítse a saját vészhelyreállítási tervét. Ebben a cikkben a vészhelyreállítási tervet hozhat létre az információk elolvasásához. Itt talál néhány forrásanyagot, amelyek segítenek a saját terve létrehozásában.

* [Vészhelyreállítás és magas rendelkezésre állás az Azure-alkalmazások](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Technikai útmutató az Azure rugalmassága](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Ajánlott eljárások
Azt javasoljuk, hogy a kritikus fontosságú adatokat másol egy másik Data Lake Storage Gen1 fiókot egy másik régióban található a Vészhelyreállítási terv igényeihez igazodó gyakorisággal. Különböző módszerekkel másolhatja az adatokat, többek között az [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) és [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) használatával. Az Azure Data Factory hasznos szolgáltatás az adatáthelyezési folyamatok rendszeres létrehozásához és üzembe helyezéséhez.

Regionális kimaradás során, ezután hozzáférhetnek a régióban, ahová az adatok másolta az adatokat. Nyomon követheti a [Azure szolgáltatásállapot-irányítópult](https://azure.microsoft.com/status/) az Azure-szolgáltatás állapotának meghatározása az egész világon.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Adatsérülés vagy véletlen törlés helyreállításával kapcsolatos útmutató
Bár a Data Lake Storage Gen1 biztosítja az adatok rugalmasságát biztosítja az automatikus replikációval, ez nem akadályozza meg az alkalmazás (vagy a fejlesztők/felhasználók) az adatok sérülését vagy véletlen törlését.

### <a name="best-practices"></a>Ajánlott eljárások
Véletlen törlés megakadályozása érdekében ajánlott először megadni a megfelelő hozzáférési házirendjeit a Data Lake Storage Gen1 fiókjához.  Ez magában foglalja az alkalmazása [Azure-erőforrászárolás](../azure-resource-manager/resource-group-lock-resources.md) való zárolását, így fontos erőforrásokat is alkalmaz, fiók- és fájlszintű hozzáférés-vezérlés az elérhető [Data Lake Storage Gen1 biztonsági funkciók](data-lake-store-security-overview.md). Azt javasoljuk, hogy rendszeresen készítsen másolatokat a kritikus fontosságú adatait az [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell-lel](data-lake-store-get-started-powershell.md) vagy [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) az egy másik Data Lake Storage Gen1 fiók, mappában vagy Azure-előfizetést.  Ezzel helyreállíthatók az adatsérülések vagy -törlések. Az Azure Data Factory hasznos szolgáltatás az adatáthelyezési folyamatok rendszeres létrehozásához és üzembe helyezéséhez.

Engedélyezheti a szervezetek [diagnosztikai naplózás](data-lake-store-diagnostic-logs.md) Data Lake Storage Gen1 fiókjuk gyűjthet adatokat a fájlhozzáférés napló ellenőrzését, amely azzal foglalkozik, akiknek előfordulhat, hogy törölte vagy frissítette a fájlokat.

## <a name="next-steps"></a>További lépések
* [Ismerkedés az Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Biztonságos adattárolás a Data Lake Storage Gen1](data-lake-store-secure-data.md)

