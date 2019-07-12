---
title: Fenyegetések észlelése a data services, az Azure Security Centerben |} A Microsoft Docs
description: Ez a témakör bemutatja a szolgáltatások Adatriasztások elérhető az Azure Security Centerben.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 87cfd2769e473d26c2dcae1b7b418f6fb1739915
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626284"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>A data services, az Azure Security Center fenyegetésészlelési

 A Security Center elemzi a storage-adatszolgáltatások naplókat, és értesítést küld, ha az adatok erőforrások fenyegetést észlel. Ez a témakör felsorolja a riasztásokat a Security Center által létrehozott, a következő szolgáltatásokat:

* [Az Azure SQL Database és az SQL Data warehouse-bA](#data-sql)
* [Azure Storage](#azure-storage)

## Az Azure SQL Database és az SQL Data warehouse-bA <a name="data-sql"></a>

SQL threat detection szokatlan és vélhetően kárt okozó jelző rendellenes tevékenységek észleli az adatbázisokat elérni vagy kiaknázni próbál. A Security Center elemzi az SQL-naplók, és az SQL-kezelő a natív módon futtatható.

|Riasztás|Leírás|
|---|---|
|**Biztonsági rés az SQL-injektálás**|Egy alkalmazás egy hibás SQL-utasítást jön létre az adatbázisban. Ez azt jelezheti SQL injektálási támadásokkal kihasználható biztonsági rést jelezhet. A hibás utasításokat két dolog okozhatja: Egy hiba kialakítani, vagy, a hibás SQL-utasítást. Vagy az alkalmazáskódok és tárolt eljárások nem ellenőrzik a felhasználói adatbevitelt a hibás SQL-utasítást, amely lehet használni. az SQL-injektálás létrehozásánál.|
|**Potenciális SQL-injektálás**|Egy aktív biztonsági rés kiaknázása elleni sebezhető az SQL-injektálás azonosított alkalmazások történt. Ez azt jelenti, hogy egy támadó próbál beszúrása rosszindulatú SQL-utasításokat a sebezhető alkalmazáskód vagy tárolt eljárásokat.|
|**Hozzáférés szokatlan helyről**|Az SQL Server, amikor valaki jelentkezett be az SQL Server egy szokatlan földrajzi helyről hozzáférési mintájában változás történt. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).|
|**Hozzáférés résztvevő részéről**|Az SQL-kiszolgáló hozzáférési mintájában a változás történt – valaki jelentkezett be az SQL server egy szokatlan résztvevő (SQL-felhasználó) használatával. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).|
|**Hozzáférés potenciálisan káros-alkalmazás**|Az adatbázis eléréséhez egy potenciálisan káros alkalmazást használatban van. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy gyakori támadóeszközökkel végrehajtott támadást észlel.|
|**Találgatásos támadás SQL hitelesítő adatai**|Egy rendellenes nagy számú különböző hitelesítő adatok használatával történő sikertelen bejelentkezések történtek. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy találgatásos támadást észlel.|

További információ az SQL threat detection riasztások lásd:[Azure SQL Database fenyegetésészlelési](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview), és tekintse át a threat detection riasztások szakaszban. Is láthatja, [útmutató az Azure Security Center segít a kibertámadás esetén felfedése](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) egy példát, hogyan a Security Center kártékony SQL-tevékenység felderítésének felderítésére szolgál a támadás megtekintéséhez.

## Azure Storage<a name="azure-storage"></a>

>[!NOTE]
> Az Azure Storage komplex veszélyforrások elleni védelem csak a Blob Storage jelenleg érhető el. 

Az Azure Storage-hoz nyújtott komplex veszélyforrások elleni védelem egy további biztonságiintelligencia-réteget ad, amely észleli a tárfiókok elérésére és felhasználására tett szokatlan és feltehetően ártalmas kísérleteket. A védelmi réteg lehetővé teszi cím fenyegetések, nem kell szakértői biztonsági, és a biztonsági monitorozási rendszerek felügyelete.

A Security Center elemzi az olvasási, írási és törlési kérelmek a Blob storage, észlelheti a fenyegetéseket a diagnosztikai naplók, és értesítést küld a tevékenység anomáliák esetén. További információkért lásd: [Storage Analytics naplózási](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging) további információt.

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**Hely szokatlan hozzáférés anomáliadetektálási**|A mintavételezett hálózati forgalomelemzés rendellenes kimenő távoli asztal protokoll (RDP)-kommunikációt észlelt származó egy erőforrást a központi telepítésben. Ez a Tevékenység ebben a környezetben rendellenesnek számít, és utalhat, hogy az erőforrás biztonsága sérült, és az használják külső találgatásos RDP-végpontot. Megjegyzendő, hogy ez a tevékenység külső entitások számára rosszindulatúként sorolhatja be az Ön IP-címét is.|
|**Alkalmazás-hozzáférési anomáliadetektálási**|Azt jelzi, hogy szokatlan alkalmazáshoz fért hozzá ezt a tárfiókot. Egy lehetséges oka, hogy egy támadó fért hozzá a tárfiók, egy új alkalmazással.|
|**Névtelen hozzáférés anomáliadetektálási**|Azt jelzi, hogy a tárfiók hozzáférési mintájában változás történik. Például a fiók rendelkezik hozzáfértek névtelenül (hitelesítés) nélkül, amelyek nem várt ehhez a fiókhoz a legutóbbi hozzáférés minta képest. Egy lehetséges oka, hogy egy támadó bejutott nyilvános olvasási hozzáférés, hogy visszatartással érvényteleníteni storage-tárolóba.|
|**Adatok kiszűrése anomáliadetektálási**|Azt jelzi, hogy egy szokatlanul nagy mennyiségű adat lehívása a storage-tárolót a közelmúltbeli tevékenység képest. Egy lehetséges oka, hogy a támadó rendelkezik kinyert nagy mennyiségű adatot, hogy visszatartással érvényteleníteni storage tárolóban.|
|**Anomáliadetektálási váratlan delete**|Azt jelzi, hogy egy vagy több váratlan törlési műveletek lépett-e a storage-fiókban, ehhez a fiókhoz a közelmúltbeli tevékenység képest. Egy lehetséges oka, hogy egy támadó törölte-e az adatokat a tárfiókból.|
|**Azure Cloud Service-csomag feltöltése.**|Azt jelzi, hogy egy Azure Cloud Service-csomag (.cspkg fájl) fel van töltve a tárfiókhoz szokatlan módon, ehhez a fiókhoz a közelmúltbeli tevékenység képest. Egy lehetséges oka, hogy az egy támadó rosszindulatú kódot a tárfiókból egy Azure-felhőszolgáltatásban üzembe helyezéséhez előkészítése-e.|
|**Engedély hozzáférés anomáliadetektálási**|Azt jelzi, hogy módosult-e a hozzáférési engedélyeket a storage-tároló szokatlan módon. A lehetséges oka, hogy egy támadó megváltozott gyengíthetik a biztonsági állapotáról, vagy próbál a jeggyel adatmegőrzés tároló engedélyeit.|
|**Hálózatfelügyeleti hozzáférés anomáliadetektálási**|Azt jelzi, hogy a tárfiók hozzáférési engedélyek ellenőrzése után szokatlan módon, ehhez a fiókhoz a közelmúltbeli tevékenység képest. Egy lehetséges oka, hogy egy támadó hajtanak végre egy jövőbeli támadások.|
|**Adatok feltárása anomáliadetektálási**|Azt jelzi, hogy BLOB vagy egy tárfiókban lévő tárolók lettek besorolva rendellenes módon, ehhez a fiókhoz a közelmúltbeli tevékenység képest. Egy lehetséges oka, hogy egy támadó hajtanak végre egy jövőbeli támadások.|

>[!NOTE]
>Komplex veszélyforrások elleni védelem az Azure Storage jelenleg nem érhető el az Azure government és szuverén felhő-régiók.

A riasztások Storage kapcsolatos további információkért lásd: a [komplex veszélyforrások elleni védelem az Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection) című cikket, és tekintse át a védelmi riasztások szakaszban.
