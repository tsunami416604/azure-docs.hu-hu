---
title: Vészhelyreállítási útmutató az Azure Data Lake Store |} A Microsoft Docs
description: Az Azure Data Lake Store vész-helyreállítási útmutató
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: b51f0c1e0c6ef713bf8d3ff0a124300f446a9373
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306808"
---
# <a name="disaster-recovery-guidance-for-data-in-data-lake-store"></a>A Data Lake Store adatok vészhelyreállítási útmutató

Az Azure Data Lake Store biztosítja a helyileg redundáns tárolás (LRS). Ezért az adatok az Azure Data Lake Store-fiókban az átmeneti hardverhibák esetén biztosítja az automatikus replikációval adatközponton belül ellenálló legyen. Ez tartósságot és magas szintű rendelkezésre állást nyújt, az Azure Data Lake Store SLA feltételeinek megfelelően. Ez a cikk nyújt útmutatást hogyan védheti még jobban az adatokat a ritka régió kiterjedő ritka kimaradásokkal és véletlen törlésekkel szemben.

## <a name="disaster-recovery-guidance"></a>Vészhelyreállítási útmutató
Minden ügyfél számára kritikus fontosságú, hogy előkészítse a saját vészhelyreállítási tervét. Ebben a cikkben a vészhelyreállítási tervet hozhat létre az információk elolvasásához. Itt talál néhány forrásanyagot, amelyek segítenek a saját terve létrehozásában.

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

