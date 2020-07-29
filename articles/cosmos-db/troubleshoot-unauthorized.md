---
title: Azure Cosmos DB jogosulatlan kivételek megoldása
description: Jogosulatlan kivétel diagnosztizálása és kijavítása
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 872780ff65dabe5a931ae712c0f99b6e4f3e726d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294259"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exception"></a>Azure Cosmos DB jogosulatlan kivétel diagnosztizálása és megoldása

HTTP 401: a HTTP-kérelemben található MAC-aláírás nem egyezik meg a számított aláírással.
Ha a következő 401-es hibaüzenetet kapta: „A HTTP-kérelemben található MAC-aláírás nem egyezik meg a kiszámított aláírással.”, ezt a következő forgatókönyvek okozhatják.

Régebbi SDK-k esetén a kivétel érvénytelen JSON-kivételként jelenhet meg a megfelelő 401 jogosulatlan kivétel helyett. Az újabb SDK-k megfelelően kezelik ezt a forgatókönyvet, és érvényes hibaüzenetet adnak.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
A következő lista a jogosulatlan kivételek ismert okait és megoldásait tartalmazza.

### <a name="1-key-was-not-properly-rotated-is-the-most-common-scenario"></a>1. a kulcs nem megfelelően lett elforgatva a leggyakoribb forgatókönyv.
A kulcs lecserélődése után rövid időn belül megjelenik a MAC-aláírással kapcsolatos 401-es hiba, és végül magától megszűnik. 

#### <a name="solution"></a>Megoldás:
Lecserélődött a kulcs, és a kulcsrotáció során nem követték az [ajánlott eljárásokat](secure-access-to-data.md#key-rotation). A Cosmos DB fiók kulcsának elforgatása a Cosmos DB fiók méretétől függően néhány másodperctől akár napokig is eltarthat.

### <a name="2-the-key-is-misconfigured"></a>2. a kulcs hibásan van konfigurálva 
401 MAC-aláírási probléma konzisztens lesz, és minden, az adott kulcsot használó hívás esetében történik

#### <a name="solution"></a>Megoldás:
A kulcs helytelenül van konfigurálva az alkalmazásban, és nem a megfelelő kulcsot használja a fiókhoz, vagy a teljes kulcs nem lett átmásolva.

### <a name="3-the-application-is-using-the-read-only-keys-for-write-operations"></a>3. az alkalmazás a csak olvasható kulcsokat használja az írási műveletekhez
401 MAC-aláírási probléma csak olyan írási műveletekben fordul elő, mint a Create vagy a Replace, de az olvasási kérelem sikeres.

#### <a name="solution"></a>Megoldás:
A műveletek sikeres befejezésének engedélyezéséhez állítsa át az alkalmazást olvasási/írási kulcs használatára.

### <a name="4-race-condition-with-create-container"></a>4. versenyhelyzet létrehozási tárolóval
401 MAC-aláírási probléma röviddel a tároló létrehozása után látható. Ez csak akkor történik meg, ha a tároló létrehozása befejeződött.

#### <a name="solution"></a>Megoldás:
A tárolók létrehozásakor versenyhelyzet áll fenn. Egy alkalmazáspéldány megpróbál hozzáférni a tárolóhoz, még mielőtt a tároló létrehozása befejeződne. A versenyhelyzet leggyakoribb forgatókönyve az, ha az alkalmazás fut, és a tároló törölve lett, és ugyanazzal a névvel lett létrehozva. Az SDK megpróbálja használni az új tárolót, de a tároló létrehozása még folyamatban van, így még nem rendelkezik a kulcsokkal.

## <a name="next-steps"></a>További lépések
* A Azure Cosmos DB .NET SDK használatakor felmerülő problémák [diagnosztizálása és hibaelhárítása](troubleshoot-dot-net-sdk.md)
* A [.net v3](performance-tips-dotnet-sdk-v3-sql.md) és a [.NET v2](performance-tips.md) teljesítményével kapcsolatos irányelvek ismertetése