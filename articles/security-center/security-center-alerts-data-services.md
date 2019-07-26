---
title: Veszélyforrások észlelése a Azure Security Center adatszolgáltatásaiban | Microsoft Docs
description: Ez a témakör a Azure Security Centerban elérhető adatszolgáltatási riasztásokat mutatja be.
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
ms.date: 7/24/2019
ms.author: v-mohabe
ms.openlocfilehash: f33b69ac443a1bb8f6b7d6e1b19f2f077bf38f58
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501484"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Veszélyforrások észlelése a Azure Security Center adatszolgáltatásaiban

 Security Center elemzi az adattárolási szolgáltatások naplóit, és riasztásokat küld, amikor fenyegetést észlel az adaterőforrásaihoz. Ez a témakör felsorolja azokat a riasztásokat, amelyeket Security Center a következő szolgáltatásokhoz generál:

* [Azure SQL Database és SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)
* [Cosmos DB](#cosmos-db)

## Azure SQL Database és SQL Data Warehouse<a name="data-sql"></a>

Az SQL veszélyforrások észlelése rendellenes tevékenységeket észlel, amelyekben szokatlan és potenciálisan ártalmas próbálkozások érhetők el az adatbázisok eléréséhez vagy kiaknázásához. Security Center elemzi az SQL-naplókat, és natív módon futtatja az SQL-motorban.

|Riasztás|Leírás|
|---|---|
|**Biztonsági rés az SQL-injektáláshoz**|Egy alkalmazás egy hibás SQL-utasítást generált az adatbázisban. Ez az SQL-injektálási támadások lehetséges sebezhetőségét jelezheti. A hibás utasításokat két dolog okozhatja: Vagy az alkalmazás kódjában található hiba a hibás SQL-utasítást alakította ki. Vagy az alkalmazás kódja vagy tárolt eljárásai nem fertőtlenítik a felhasználói adatbevitelt a hibás SQL-utasítás létrehozásakor, amely az SQL-injektálás esetén kihasználható.|
|**Lehetséges SQL-injektálás**|Aktív biztonsági rés történt egy azonosított alkalmazásban, amely sebezhető az SQL-injektálással. Ez azt jelenti, hogy a támadó rosszindulatú SQL-utasításokat próbál beszúrni a sebezhető alkalmazás kódjával vagy tárolt eljárásaival.|
|**Hozzáférés szokatlan helyről**|Módosult az SQL Server hozzáférési mintája, ahol valaki szokatlan földrajzi helyről jelentkezett be az SQL-kiszolgálóra. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).|
|**Hozzáférés az ismeretlen résztvevőtől**|Módosult az SQL Server hozzáférési mintája – valaki szokatlan rendszerbiztonsági tag (SQL-felhasználó) használatával jelentkezett be az SQL-kiszolgálóra. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).|
|**Hozzáférés egy potenciálisan ártalmas alkalmazáshoz**|Az adatbázis elérésére potenciálisan ártalmas alkalmazás van használatban. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy gyakori támadóeszközökkel végrehajtott támadást észlel.|
|**Találgatásos kényszerített SQL-hitelesítő adatok**|Szokatlanul nagy számú sikertelen bejelentkezés történt a különböző hitelesítő adatokkal. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy találgatásos támadást észlel.|

További információ az SQL Threat észlelési riasztásokról:[Azure SQL Database veszélyforrások észlelése](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview), és a veszélyforrások észlelésével kapcsolatos riasztások szakasz áttekintése. Azt is megtudhatja, [hogyan Azure Security Center segít feltárni a Cyberattack](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) , hogy megtudja, Security Center hogyan használják a rosszindulatú SQL-tevékenység észlelését a támadás felderítése érdekében.

## Azure Storage<a name="azure-storage"></a>

>[!NOTE]
> Az Azure Storage komplex veszélyforrások elleni védelme jelenleg csak Blob Storage érhető el.

Az Azure Storage-hoz nyújtott komplex veszélyforrások elleni védelem egy további biztonságiintelligencia-réteget ad, amely észleli a tárfiókok elérésére és felhasználására tett szokatlan és feltehetően ártalmas kísérleteket. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelését anélkül, hogy biztonsági szakértőnek kellene lennie, és a biztonsági figyelő rendszereket kell kezelnie.

Security Center elemzi az olvasási, írási és törlési kérelmeket a blob Storage-ba a fenyegetések észlelése érdekében, és riasztást küld, ha a tevékenységben rendellenességek jelentkeznek. További információ: [Storage Analytics naplózás konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging) további információkhoz.

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**Szokatlan hely elérési rendellenessége**|A mintavételes hálózati forgalom elemzése rendellenes kimenő RDP protokoll (RDP) kommunikációt észlelt az üzemelő példányból származó erőforrásból. Ez a tevékenység rendellenesnek minősül ebben a környezetben, és arra utalhat, hogy az erőforrást feltörték, és már használatban van a külső RDP-végpont találgatásos kényszerítéséhez. Megjegyzendő, hogy ez a tevékenység külső entitások számára rosszindulatúként sorolhatja be az Ön IP-címét is.|
|**Alkalmazás-hozzáférési rendellenesség**|Azt jelzi, hogy egy szokatlan alkalmazás hozzáfért ehhez a Storage-fiókhoz. A lehetséges ok az, hogy egy támadó új alkalmazás használatával fér hozzá a Storage-fiókhoz.|
|**Névtelen hozzáférési rendellenesség**|Azt jelzi, hogy a hozzáférési minta módosult egy Storage-fiókban. A fiókhoz például névtelenül (hitelesítés nélkül) férhet hozzá, ami nem várt, mint a fiók legutóbbi hozzáférési mintája. Ennek lehetséges oka, hogy egy támadó nyilvános olvasási hozzáférést kapott egy olyan tárolóhoz, amely blob (ok) tárolót tárol.|
|**Az adatkiszűrése anomália**|Azt jelzi, hogy a tárolón a legutóbbi tevékenységhez képest szokatlanul nagy mennyiségű adattal lett kibontva. A lehetséges ok az, hogy egy támadó nagy mennyiségű adattal gyűjtött ki egy olyan tárolóból, amely blob (ok) tárolót tárol.|
|**Váratlan törlési rendellenesség**|Azt jelzi, hogy egy vagy több váratlan törlési művelet történt egy Storage-fiókban, a fiókhoz tartozó legutóbbi tevékenységhez képest. Ennek lehetséges oka, hogy egy támadó törölte az adatait a Storage-fiókjából.|
|**Azure Cloud Service-csomag feltöltése**|Azt jelzi, hogy egy Azure Cloud Service-csomag (. cspkg fájl) szokatlan módon lett feltöltve egy Storage-fiókba, a fiók legutóbbi tevékenységéhez képest. A lehetséges ok az, hogy egy támadó arra készül, hogy rosszindulatú kódot helyezzen üzembe a Storage-fiókból egy Azure Cloud Service-be.|
|**Engedély-hozzáférési rendellenesség**|Azt jelzi, hogy a tároló hozzáférési engedélyei szokatlan módon módosultak. Ennek lehetséges oka, hogy egy támadó megváltoztatta a tároló engedélyeit, hogy gyengítse biztonsági állapotát, vagy az adatmegőrzést.|
|**Ellenőrzési hozzáférési rendellenesség**|Azt jelzi, hogy a Storage-fiók hozzáférési engedélyei szokatlan módon lettek megvizsgálva, a fiók legutóbbi tevékenységéhez képest. A lehetséges ok az, hogy egy támadó egy jövőbeli támadáshoz Felderítőt hajtott végre.|
|**Adatfeltárási rendellenesség**|Azt jelzi, hogy a Storage-fiókban lévő Blobok vagy tárolók rendellenes módon vannak felsorolva, a fiók legutóbbi tevékenységéhez képest. A lehetséges ok az, hogy egy támadó egy jövőbeli támadáshoz Felderítőt hajtott végre.|

>[!NOTE]
>Az Azure Storage komplex veszélyforrások elleni védelme jelenleg nem érhető el az Azure governmentben és a szuverén Felhőbeli régiókban.

További információ a tárolási riasztásokról: az [Azure Storage komplex veszélyforrások elleni védelme](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection) , valamint a védelmi riasztások szakasz áttekintése.

## Cosmos DB<a name="cosmos-db"></a>

A következő riasztásokat szokatlan és potenciálisan ártalmas kísérletek generálják Azure Cosmos DB fiókok eléréséhez vagy kiaknázásához:

|Riasztás|Leírás|
|---|---|
|**Hozzáférés szokatlan helyről**|Azt jelzi, hogy a hozzáférési minta módosult egy Cosmos DB-fiókra. Valaki a legutóbbi tevékenységhez képest ismeretlen IP-címről kapta ezt a fiókot. Vagy egy támadó hozzáfért egy Cosmos DB fiókhoz, vagy egy megbízható felhasználó egy új és szokatlan földrajzi helyről fér hozzá a Cosmos DB fiókhoz. Például: új alkalmazás vagy fejlesztői karbantartás távoli számítógépről.|
|**Szokatlan adatkiszűrése**|Azt jelzi, hogy módosult egy Cosmos DB-fiók adatkiemelési mintája. Valaki a legutóbbi tevékenységhez képest szokatlan mennyiségű adattal kibontotta. Egy támadó nagy mennyiségű adattal kibontotta Cosmos DB adatbázisát. Például: az adatok kiszűrése/szivárgása, az adatok jogosulatlan átvitele. Vagy egy megbízható felhasználó vagy alkalmazás szokatlan mennyiségű adattal kibontotta a tárolót. Például: karbantartási biztonsági mentési tevékenység.|

További információ: [a Azure Cosmos db komplex veszélyforrások elleni védelme](../cosmos-db/cosmos-db-advanced-threat-protection.md).