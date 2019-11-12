---
title: Veszélyforrások észlelése a Azure Security Center adatszolgáltatásaiban | Microsoft Docs
description: Ez a cikk a Azure Security Centerban elérhető adatszolgáltatási riasztásokat ismerteti.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: memildin
ms.openlocfilehash: d23d9d2712923f37b3ab9da5ae5369342cd82f5d
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2019
ms.locfileid: "73906994"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Veszélyforrások észlelése a Azure Security Center adatszolgáltatásaiban

 Azure Security Center elemzi az adattárolási szolgáltatások naplóit, és riasztásokat küld, amikor fenyegetést észlel az adaterőforrásaihoz. Ez a cikk felsorolja azokat a riasztásokat, amelyeket Security Center a következő szolgáltatásokhoz generál:

* [Azure SQL Database és Azure SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## SQL Database és SQL Data Warehouse<a name="data-sql"></a>

Az SQL-veszélyforrások észlelése olyan rendellenes tevékenységeket azonosít, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához 

|Riasztás|Leírás|
|---|---|
|**Biztonsági rés az SQL-injektáláshoz**|Egy alkalmazás egy hibás SQL-utasítást generált az adatbázisban. Ez az SQL-injektálási támadások lehetséges sebezhetőségét jelezheti. A hibás utasításoknak két lehetséges oka van. Előfordulhat, hogy az alkalmazás kódjában lévő hiba a hibás SQL-utasítást alakította ki. Vagy az alkalmazás kódja vagy tárolt eljárásai nem fertőtlenítik a felhasználói adatbevitelt a hibás SQL-utasítás létrehozásakor, ami kihasználható az SQL-injektáláshoz.|
|**Lehetséges SQL-injektálás**|Aktív biztonsági rés történt egy azonosított alkalmazásban, amely sebezhető az SQL-injektálással. Ez azt jelenti, hogy a támadó rosszindulatú SQL-utasításokat próbál beszúrni a sebezhető alkalmazás kódjával vagy tárolt eljárásaival.|
|**Hozzáférés szokatlan helyről**|A hozzáférési minta módosult SQL Serverre, ahol valaki szokatlan földrajzi helyről jelentkezett be a kiszolgálóra. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás rosszindulatú műveletet észlel (egy korábbi alkalmazott vagy külső támadó).|
|**Hozzáférés az ismeretlen résztvevőtől**|A hozzáférési minta módosult SQL Serverra. Valaki egy szokatlan rendszerbiztonsági tag (felhasználó) használatával jelentkezett be a kiszolgálóra. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás rosszindulatú műveletet észlel (egy korábbi alkalmazott vagy külső támadó).|
|**Hozzáférés egy potenciálisan ártalmas alkalmazáshoz**|Az adatbázis elérésére potenciálisan ártalmas alkalmazás van használatban. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás olyan támadást észlel, amely általános eszközöket használ.|
|**Találgatásos kényszerített SQL-hitelesítő adatok**|Rendellenesen nagy számú sikertelen bejelentkezés történt a különböző hitelesítő adatokkal. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás találgatásos támadásokat észlel.|

További információ az SQL-veszélyforrások észleléséről: [Azure SQL Database fenyegetések észlelése](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview). Tekintse át a veszélyforrások észlelésével kapcsolatos riasztások szakaszt. Azt is megtudhatja, [hogyan Azure Security Center segít feltárni a cyberattack](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) , hogy megtudja, Security Center hogyan használják a rosszindulatú SQL-tevékenység észlelését a támadás felderítése érdekében.

## Azure Storage<a name="azure-storage"></a>

>[!NOTE]
> A tárterület komplex veszélyforrások elleni védelme jelenleg csak a blob Storage esetében érhető el.

A Storage komplex veszélyforrások elleni védelme egy további biztonsági intelligenciát biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel a Storage-fiókok eléréséhez vagy kiaknázásához. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelését anélkül, hogy biztonsági szakértőnek kellene lennie, és a biztonsági figyelő rendszereket kell kezelnie.

Security Center elemzi az olvasási, írási és törlési kérelmeket a blob Storage-ba a fenyegetések észlelése érdekében, és riasztást küld, ha a tevékenységben rendellenességek jelentkeznek. További információ: [Storage Analytics naplózás konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging).

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**Hozzáférés szokatlan helyről**|Azt jelzi, hogy a hozzáférési minta egy Azure Storage-fiókra módosult. Valaki hozzáfért a fiókhoz egy, a legutóbbi tevékenységhez képest ismeretlen IP-címről. Vagy egy támadó hozzáfért a fiókhoz, vagy egy megbízható felhasználó új vagy szokatlan földrajzi helyről kapcsolódott. Az utóbbi egy példa egy új alkalmazásból vagy fejlesztőből származó távoli karbantartásra.|
|**Alkalmazás-hozzáférési rendellenesség**|Azt jelzi, hogy egy szokatlan alkalmazás hozzáfért ehhez a Storage-fiókhoz. A lehetséges ok az, hogy egy támadó új alkalmazás használatával fér hozzá a Storage-fiókhoz.|
|**Névtelen hozzáférési rendellenesség**|Azt jelzi, hogy a hozzáférési minta módosult egy Storage-fiókban. A fiókhoz például névtelenül (hitelesítés nélkül) férhet hozzá, ami nem várt, mint a fiók legutóbbi hozzáférési mintája. Ennek lehetséges oka, hogy egy támadó nyilvános olvasási hozzáférést kapott a blob Storage-t tároló tárolóhoz.|
|**Tor-anomália**|Azt jelzi, hogy ez a fiók sikeresen elérhető egy olyan IP-címről, amely a Tor aktív kilépési csomópontjának (anonimizálásával-proxy) ismert. Ennek a riasztásnak a súlyossága figyelembe veszi a használt hitelesítési típust (ha van ilyen), és hogy ez az ilyen hozzáférés első esete-e. Lehetséges okok lehetnek olyan támadók, akik a Tor használatával hozzáfértek a Storage-fiókjához, vagy egy olyan legitim felhasználó, aki a Tor használatával fér hozzá a Storage-fiókhoz.|
|**Az adatkiszűrése anomália**|Azt jelzi, hogy a tárolón a legutóbbi tevékenységhez képest szokatlanul nagy mennyiségű adattal lett kibontva. A lehetséges ok az, hogy egy támadó nagy mennyiségű adatmennyiséget adott ki egy olyan tárolóból, amely blob Storage-tárolót tárol.|
|**Váratlan törlési rendellenesség**|Azt jelzi, hogy egy vagy több váratlan törlési művelet történt egy Storage-fiókban, a fiókhoz tartozó legutóbbi tevékenységhez képest. Ennek lehetséges oka, hogy egy támadó törölte az adatait a Storage-fiókjából.|
|**Azure Cloud Services-csomag feltöltése**|Azt jelzi, hogy egy Azure Cloud Services csomag (. cspkg fájl) szokatlan módon lett feltöltve egy Storage-fiókba, a fiók legutóbbi tevékenységéhez képest. A lehetséges ok az, hogy egy támadó arra készül, hogy rosszindulatú kódot helyezzen üzembe a Storage-fiókból egy Azure Cloud Service-be.|
|**Engedély-hozzáférési rendellenesség**|Azt jelzi, hogy a tároló hozzáférési engedélyei szokatlan módon módosultak. Ennek lehetséges oka, hogy egy támadó megváltoztatta a tárolók engedélyeit, hogy gyengítse biztonsági állapotát, vagy az adatmegőrzést.|
|**Ellenőrzési hozzáférési rendellenesség**|Azt jelzi, hogy a Storage-fiók hozzáférési engedélyei szokatlan módon lettek megvizsgálva, a fiók legutóbbi tevékenységéhez képest. A lehetséges ok az, hogy egy támadó egy jövőbeli támadáshoz Felderítőt hajtott végre.|
|**Adatfeltárási rendellenesség**|Azt jelzi, hogy a Storage-fiókban lévő Blobok vagy tárolók rendellenes módon vannak felsorolva, a fiók legutóbbi tevékenységéhez képest. A lehetséges ok az, hogy egy támadó egy jövőbeli támadáshoz Felderítőt hajtott végre.|
|**Lehetséges kártevők feltöltése**|Azt jelzi, hogy egy lehetséges kártevőt tartalmazó blob feltöltve lett egy Storage-fiókba. A lehetséges okok miatt előfordulhat, hogy a támadók szándékos kártevőket töltenek fel egy ártó szándékú, rosszindulatú blob általi feltöltéssel, amely egy legitim felhasználó.|

>[!NOTE]
>A komplex veszélyforrások elleni védelem jelenleg nem érhető el az Azure governmentben és a szuverén Felhőbeli régiókban.

További információ a tárolási riasztásokról: az [Azure Storage komplex veszélyforrások elleni védelme](../storage/common/storage-advanced-threat-protection.md). Különösen tekintse át a "védelmi riasztások" szakaszt.

## Azure Cosmos DB<a name="cosmos-db"></a>

A következő riasztásokat szokatlan és potenciálisan ártalmas kísérletek generálják Azure Cosmos DB fiókok eléréséhez vagy kiaknázásához:

|Riasztás|Leírás|
|---|---|
|**Hozzáférés szokatlan helyről**|Azt jelzi, hogy a hozzáférési minta egy Azure Cosmos DB fiókra módosult. Valaki a legutóbbi tevékenységhez képest ismeretlen IP-címről kapta ezt a fiókot. Vagy egy támadó hozzáfért a fiókhoz, vagy egy megbízható felhasználó új és szokatlan földrajzi helyről férhet hozzá. Az utóbbi egy példa egy új alkalmazásból vagy fejlesztőből származó távoli karbantartásra.|
|**Szokatlan adatkiszűrése**|Azt jelzi, hogy egy Azure Cosmos DB fiókból módosult az Adatkiemelési minta. Valaki a legutóbbi tevékenységhez képest szokatlan mennyiségű adattal kibontotta. Előfordulhat, hogy egy támadó nagy mennyiségű adatmennyiséget adott ki egy Azure Cosmos DB adatbázisból (például az adatok kiszűrése vagy szivárgását, vagy az adatok jogosulatlan átvitelét). Az is előfordulhat, hogy egy megbízható felhasználó vagy alkalmazás szokatlan mennyiségű adatmennyiséget adott ki egy tárolóból (például a karbantartási biztonsági mentési tevékenységek esetében).|

További információ: [a Azure Cosmos db komplex veszélyforrások elleni védelme](../cosmos-db/cosmos-db-advanced-threat-protection.md).
