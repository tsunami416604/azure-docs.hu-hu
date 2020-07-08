---
title: Vész-helyreállítási útmutató a Azure Data Lake Storage Gen1hoz | Microsoft Docs
description: Útmutató a Azure Data Lake Storage Gen1 vész-helyreállításához
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75966057"
---
# <a name="disaster-recovery-guidance-for-data-in-azure-data-lake-storage-gen1"></a>Vész-helyreállítási útmutató a Azure Data Lake Storage Gen1 lévő adatkezeléshez

A Azure Data Lake Storage Gen1 helyileg redundáns tárolást (LRS) biztosít. Ezért a Data Lake Storage Gen1-fiókban lévő adatai az automatizált replikák segítségével rugalmasan megtalálhatók az adatközponton belüli átmeneti hardveres hibák esetén. Ez biztosítja a tartósságot és a magas rendelkezésre állást, és megfelel a Data Lake Storage Gen1 SLA-nak. Ez a cikk útmutatást nyújt arra vonatkozóan, hogy miként lehet további védelemmel ellátni az adatait az egész régióra kiterjedő vagy véletlen törléssel.

## <a name="disaster-recovery-guidance"></a>Vészhelyreállítási útmutató
Minden ügyfél számára kritikus fontosságú, hogy előkészítse a saját vészhelyreállítási tervét. A vész-helyreállítási terv elkészítéséhez olvassa el a cikkben található információkat. Itt talál néhány forrásanyagot, amelyek segítenek a saját terve létrehozásában.

* [Vészhelyreállítás és magas szintű rendelkezésre állás az Azure-alkalmazásokhoz](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Műszaki útmutató az Azure rugalmasságáról](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Ajánlott eljárások
Azt javasoljuk, hogy a kritikus fontosságú adatait egy másik régióban lévő másik Data Lake Storage Gen1-fiókba másolja, és a vész-helyreállítási terv igényeihez igazított gyakorisággal. Különböző módszerekkel másolhatja az adatokat, többek között az [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) és [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) használatával. Az Azure Data Factory hasznos szolgáltatás az adatáthelyezési folyamatok rendszeres létrehozásához és üzembe helyezéséhez.

Ha regionális leállás következik be, akkor az adatai abban a régióban érhetők el, ahol az adatait másolták. A [Azure Service Health irányítópultját](https://azure.microsoft.com/status/) figyelheti az Azure-szolgáltatások állapotának az egész világon való meghatározásához.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Adatsérülés vagy véletlen törlés helyreállításával kapcsolatos útmutató
Míg Data Lake Storage Gen1 adatrugalmasságot biztosít az automatizált replikák használatával, ez nem akadályozza meg az alkalmazás (vagy a fejlesztők/felhasználók) számára az adatok sérülését vagy véletlen törlését.

### <a name="best-practices"></a>Ajánlott eljárások
A véletlen törlés megelőzése érdekében javasoljuk, hogy először állítsa be a Data Lake Storage Gen1 fiókjának megfelelő hozzáférési házirendeket.  Ide tartozik az [Azure-erőforrások zárolásának](../azure-resource-manager/management/lock-resources.md) alkalmazása a fontos erőforrások zárolásához, valamint a fiók és a fájl szintű hozzáférés-vezérlés alkalmazásához az elérhető [Data Lake Storage Gen1 biztonsági funkciók](data-lake-store-security-overview.md)használatával. Javasoljuk továbbá, hogy a kritikus fontosságú adatairól rendszeresen készítsen másolatot a [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) vagy [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) egy másik Data Lake Storage Gen1 fiók, mappa vagy Azure-előfizetés használatával.  Ezzel helyreállíthatók az adatsérülések vagy -törlések. Az Azure Data Factory hasznos szolgáltatás az adatáthelyezési folyamatok rendszeres létrehozásához és üzembe helyezéséhez.

A szervezetek a Data Lake Storage Gen1-fiókjához is engedélyezhetik a [diagnosztikai naplózást](data-lake-store-diagnostic-logs.md) az Adathozzáférési naplózási nyomvonalak összegyűjtéséhez, amelyek információkat biztosítanak arról, hogy kik törölhettek vagy frissítettek egy fájlt.

## <a name="next-steps"></a>További lépések
* [Ismerkedés a Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)

