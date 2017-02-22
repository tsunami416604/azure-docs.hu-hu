---
title: "Gyakori kérdések az Azure Data Lake Store-ról | Microsoft Docs"
description: "Útmutató az Azure Data Lake Store hibaelhárításához és a hibák csökkentéséhez"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf7fd555-3e30-43ce-b28c-c3ad0a241fdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a3629845014cb401df96d2d8bf7b9801a0664150
ms.openlocfilehash: 2f184f5289b9394572023fe9d1aec2d28a73c4f7


---
# <a name="frequently-asked-questions-for-azure-data-lake-store"></a>Gyakori kérdések az Azure Data Lake Store-ról
Ebben a cikkben az Azure Data Lake Store-ral kapcsolatos gyakran ismételt kérdésekről olvashat.

## <a name="how-can-i-further-protect-my-data-from-region-wide-disasters-or-accidental-deletions"></a>Hogyan gondoskodhatok még jobban az adataim védelméről a régiószintű katasztrófák és a véletlen törlés ellen?
Az Azure Data Lake Store-fiókban lévő adatok automatikus replikációja biztosítja a rugalmasságot az átmeneti hardverhibák esetén az egyes régiókban. Ez tartósságot és magas szintű rendelkezésre állást nyújt, az Azure Data Lake Store SLA feltételeinek megfelelően. Itt talál útmutatást azzal kapcsolatban, hogyan védheti még jobban az adatait a több régióra kiterjedő ritka kimaradásokkal és véletlen törlésekkel szemben.

### <a name="disaster-recovery-guidance"></a>Vészhelyreállítási útmutató
Minden ügyfél számára kritikus fontosságú, hogy előkészítse a saját vészhelyreállítási tervét. A saját vészhelyreállítási tervének elkészítéséhez tekintse meg az alábbi Azure-dokumentációt. Itt talál néhány forrásanyagot, amelyek segítenek a saját terve létrehozásában.

* [Vészhelyreállítás és magas szintű rendelkezésre állás az Azure-alkalmazásokhoz](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Műszaki útmutató az Azure rugalmasságáról](../resiliency/resiliency-technical-guidance.md)

#### <a name="best-practices"></a>Ajánlott eljárások
Ajánlott másik régióban lévő Data Lake Store-fiókra másolni a kritikus adatokat a vészhelyreállítási terv igényeihez igazodó gyakorisággal. Különböző módszerekkel másolhatja az adatokat, többek között az [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) és [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md) használatával. Az Azure Data Factory hasznos szolgáltatás az adatáthelyezési folyamatok rendszeres létrehozásához és üzembe helyezéséhez.

Egy egész régióra kiterjedő leállás esetén elérheti az adatokat arról a régióról, ahová másolta őket. Az [Azure szolgáltatásállapot-irányítópult](https://azure.microsoft.com/status/) megfigyelésével meghatározhatja az Azure szolgáltatás állapotát az egész világon.

### <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Adatsérülés vagy véletlen törlés helyreállításával kapcsolatos útmutató
Az Azure Data Lake Store biztosítja az adatok rugalmasságát biztosítja az automatikus replikációval, ez azonban nem gátolja meg, hogy az alkalmazás (vagy a fejlesztők/felhasználók) az adatok sérülését vagy véletlen törlését okozzák.

#### <a name="best-practices"></a>Ajánlott eljárások
A véletlen törlés megakadályozása érdekében ajánlott először megadni a Data Lake Store-fiók megfelelő hozzáférési házirendjeit.  Ezek közé tartozik az [Azure-erőforrászárolás](../azure-resource-manager/resource-group-lock-resources.md) alkalmazása a fontos erőforrások lezárásához, valamint a fiók- és fájlszintű hozzáférés-vezérlés alkalmazása az elérhető [Data Lake Store biztonság funkciók](data-lake-store-security-overview.md) használatával. Emellett javasoljuk, hogy rendszeresen készítsen másolatokat a kritikus adatokról az [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) vagy [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md) használatával egy másik Data Lake Store-fiókban, mappában vagy Azure-előfizetésben.  Ezzel helyreállíthatók az adatsérülések vagy -törlések. Az Azure Data Factory hasznos szolgáltatás az adatáthelyezési folyamatok rendszeres létrehozásához és üzembe helyezéséhez.

A szervezetek [diagnosztikai naplózást](data-lake-store-diagnostic-logs.md) is engedélyezhetnek az Azure Data Lake Store-fiókhoz az adathozzáférés naplóinak begyűjtése érdekében, amelyek információkat nyújthatnak arról, hogy ki törölte vagy frissítette a fájlokat.

## <a name="next-steps"></a>Következő lépések
* [Az Azure Data Lake Store használatának első lépései](data-lake-store-get-started-portal.md)
* [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)




<!--HONumber=Feb17_HO2-->


