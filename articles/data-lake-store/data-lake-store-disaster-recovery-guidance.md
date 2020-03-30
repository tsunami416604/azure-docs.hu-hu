---
title: Vész-helyreállítási útmutató az Azure Data Lake Storage Gen1-hez | Microsoft dokumentumok
description: Útmutató az Azure Data Lake Storage Gen1 vész-helyreállítási
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
ms.openlocfilehash: b33977ca5184ea07b5651be18e3a132d30ce4b39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966057"
---
# <a name="disaster-recovery-guidance-for-data-in-azure-data-lake-storage-gen1"></a>Vész-helyreállítási útmutató az azure Data Lake Storage Gen1-ben tárolt adatokhoz

Az Azure Data Lake Storage Gen1 helyileg redundáns tárolást (LRS) biztosít. Ezért a Data Lake Storage Gen1-fiók ban lévő adatok automatikus replikák révén rugalmas a tranziens hardverhibák egy adatközponton belül. Ez biztosítja a tartósságot és a magas rendelkezésre állást, megfelelve a Data Lake Storage Gen1 SLA-nak. Ez a cikk útmutatást nyújt az adatok ritka régiószintű kimaradások vagy véletlen törlések elleni további védelméhez.

## <a name="disaster-recovery-guidance"></a>Vészhelyreállítási útmutató
Minden ügyfél számára kritikus fontosságú, hogy előkészítse a saját vészhelyreállítási tervét. Olvassa el az ebben a cikkben a vész-helyreállítási terv létrehozásához. Itt talál néhány forrásanyagot, amelyek segítenek a saját terve létrehozásában.

* [Vészhelyreállítás és magas szintű rendelkezésre állás az Azure-alkalmazásokhoz](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Műszaki útmutató az Azure rugalmasságáról](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Ajánlott eljárások
Azt javasoljuk, hogy másolja a kritikus adatokat egy másik Data Lake Storage Gen1 fiók egy másik régióban a vész-helyreállítási terv igényeinek megfelelő gyakorisággal. Különböző módszerekkel másolhatja az adatokat, többek között az [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) és [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) használatával. Az Azure Data Factory hasznos szolgáltatás az adatáthelyezési folyamatok rendszeres létrehozásához és üzembe helyezéséhez.

Ha regionális kimaradás történik, hozzáférhet az adatokhoz abban a régióban, ahol az adatokat másolta. Figyelheti az [Azure Service Health Dashboard az](https://azure.microsoft.com/status/) Azure-szolgáltatás állapotát az egész világon.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Adatsérülés vagy véletlen törlés helyreállításával kapcsolatos útmutató
Bár a Data Lake Storage Gen1 automatikus replikákon keresztül biztosítja az adatok rugalmasságát, ez nem akadályozza meg, hogy az alkalmazás (vagy a fejlesztők/felhasználók) megrontsák az adatokat, vagy véletlenül kiirtsák azokat.

### <a name="best-practices"></a>Ajánlott eljárások
A véletlen törlés elkerülése érdekében azt javasoljuk, hogy először állítsa be a megfelelő hozzáférési szabályzatokat a Data Lake Storage Gen1 fiókjához.  Ez magában foglalja [az Azure-erőforrás-zárolások](../azure-resource-manager/management/lock-resources.md) alkalmazását a fontos erőforrások zárolásához, valamint a fiók- és fájlszintű hozzáférés-vezérlés alkalmazását a rendelkezésre álló [Data Lake Storage Gen1 biztonsági funkciók](data-lake-store-security-overview.md)használatával. Azt is javasoljuk, hogy rendszeresen hozzon létre másolatokat a kritikus adatok segítségével [ADLCopy,](data-lake-store-copy-data-azure-storage-blob.md) [Azure PowerShell](data-lake-store-get-started-powershell.md) vagy [az Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) egy másik Data Lake Storage Gen1 fiók, mappa vagy Azure-előfizetés használatával.  Ezzel helyreállíthatók az adatsérülések vagy -törlések. Az Azure Data Factory hasznos szolgáltatás az adatáthelyezési folyamatok rendszeres létrehozásához és üzembe helyezéséhez.

A szervezetek a Data Lake Storage Gen1 fiókdiagnosztikai [naplózását](data-lake-store-diagnostic-logs.md) is engedélyezhetik adathozzáférés-naplózási nyomvonalak gyűjtésére, amely információt nyújt arról, hogy ki törölt vagy frissített egy fájlt.

## <a name="next-steps"></a>További lépések
* [Első lépések az Azure Data Lake Storage Gen1 használatával](data-lake-store-get-started-portal.md)
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)

