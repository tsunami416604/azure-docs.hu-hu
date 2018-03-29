---
title: Vész-helyreállítási útmutató a Azure Data Lake Store |} Microsoft Docs
description: Azure Data Lake Store vész-helyreállítási útmutatást
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 9a2705a2372ea4f2fbea1e27bf675679dfda28f8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="disaster-recovery-guidance-for-data-in-data-lake-store"></a>A Data Lake Store adatok vész helyreállítási útmutatást

Azure Data Lake Store helyileg redundáns tárolás (LRS) biztosít. Emiatt az adatokat az Azure Data Lake Store-fiókban is lehetséges legyen átmeneti hardverhibák esetén keresztül automatizált replikák régión belül. Ez tartósságot és magas szintű rendelkezésre állást nyújt, az Azure Data Lake Store SLA feltételeinek megfelelően. Ez a cikk az adatok további védelméhez a ritka régió kiterjedő valamilyen okból kimaradás vagy véletlen törlések módjáról nyújt útmutatást.

## <a name="disaster-recovery-guidance"></a>Vészhelyreállítási útmutató
Minden ügyfél számára kritikus fontosságú, hogy előkészítse a saját vészhelyreállítási tervét. Olvassa el a vész-helyreállítási terv létrehozásához a cikkben szereplő információkat. Itt talál néhány forrásanyagot, amelyek segítenek a saját terve létrehozásában.

* [Vészhelyreállítás és magas szintű rendelkezésre állás az Azure-alkalmazásokhoz](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Műszaki útmutató az Azure rugalmasságáról](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Ajánlott eljárások
Ajánlott másik régióban lévő Data Lake Store-fiókra másolni a kritikus adatokat a vészhelyreállítási terv igényeihez igazodó gyakorisággal. Különböző módszerekkel másolhatja az adatokat, többek között az [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) és [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) használatával. Az Azure Data Factory hasznos szolgáltatás az adatáthelyezési folyamatok rendszeres létrehozásához és üzembe helyezéséhez.

Egy egész régióra kiterjedő leállás esetén elérheti az adatokat arról a régióról, ahová másolta őket. Az [Azure szolgáltatásállapot-irányítópult](https://azure.microsoft.com/status/) megfigyelésével meghatározhatja az Azure szolgáltatás állapotát az egész világon.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Adatsérülés vagy véletlen törlés helyreállításával kapcsolatos útmutató
Az Azure Data Lake Store biztosítja az adatok rugalmasságát biztosítja az automatikus replikációval, ez azonban nem gátolja meg, hogy az alkalmazás (vagy a fejlesztők/felhasználók) az adatok sérülését vagy véletlen törlését okozzák.

### <a name="best-practices"></a>Ajánlott eljárások
A véletlen törlés megakadályozása érdekében ajánlott először megadni a Data Lake Store-fiók megfelelő hozzáférési házirendjeit.  Ezek közé tartozik az [Azure-erőforrászárolás](../azure-resource-manager/resource-group-lock-resources.md) alkalmazása a fontos erőforrások lezárásához, valamint a fiók- és fájlszintű hozzáférés-vezérlés alkalmazása az elérhető [Data Lake Store biztonság funkciók](data-lake-store-security-overview.md) használatával. Emellett javasoljuk, hogy rendszeresen készítsen másolatokat a kritikus adatokról az [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) vagy [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) használatával egy másik Data Lake Store-fiókban, mappában vagy Azure-előfizetésben.  Ezzel helyreállíthatók az adatsérülések vagy -törlések. Az Azure Data Factory hasznos szolgáltatás az adatáthelyezési folyamatok rendszeres létrehozásához és üzembe helyezéséhez.

A szervezetek [diagnosztikai naplózást](data-lake-store-diagnostic-logs.md) is engedélyezhetnek az Azure Data Lake Store-fiókhoz az adathozzáférés naplóinak begyűjtése érdekében, amelyek információkat nyújthatnak arról, hogy ki törölte vagy frissítette a fájlokat.

## <a name="next-steps"></a>További lépések
* [Az Azure Data Lake Store használatának első lépései](data-lake-store-get-started-portal.md)
* [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)

