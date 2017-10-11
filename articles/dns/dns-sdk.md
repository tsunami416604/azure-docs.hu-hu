---
title: "DNS-zóna létrehozása, és jegyezze fel beállítása az Azure DNS a .NET SDK használatával |} Microsoft Docs"
description: "DNS-zónák és rekord létrehozása az Azure DNS-beállítja a .NET SDK használatával."
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: jonatul
ms.openlocfilehash: c0fb0be8da1c0ca48a4d43ea027d30a0bc17fe30
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Hozzon létre DNS-zónák és a .NET SDK használatával rekordhalmazok

Műveletek létrehozása, törlés vagy frissítés DNS zónák, rekordhalmazokat és rekordokat a DNS-kezelési .NET library DNS SDK segítségével automatizálható. Teljes Visual Studio-projekt érhető [itt.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Hozzon létre egy egyszerű szolgáltatás fiókja

Általában az Azure-erőforrások programozott hozzáférést kapnak a saját felhasználói hitelesítő adatokat, hanem egy külön fiókot keresztül. Ezeket a dedikált fiókokat "egyszerű" szolgáltatásfiókok nevezzük. Az Azure DNS SDK minta projekt használatához először hozzon létre egy egyszerű szolgáltatásfiókot, és rendelje hozzá a megfelelő engedélyekkel.

1. Hajtsa végre a [ezeket az utasításokat](../azure-resource-manager/resource-group-authenticate-service-principal.md) létrehozni egy egyszerű szolgáltatás fiókja (az Azure DNS SDK minta projekt jelszóalapú hitelesítés feltételezi.)
2. Hozzon létre egy erőforráscsoportot ([itt hogyan](../azure-resource-manager/resource-group-template-deploy-portal.md)).
3. Használja a Azure RBAC egyszerű szolgáltatás fiókja engedélyekkel "DNS-zóna közreműködői" az erőforráscsoport ([itt hogyan](../active-directory/role-based-access-control-configure.md).)
4. Ha az Azure DNS SDK minta projekt használja, szerkessze az alábbiak szerint a "program.cs" fájlt:

   * Helyezze be a megfelelő értékeket a tenantId, clientId (más néven Fiókazonosító), titkos kulcs (egyszerű szolgáltatásfiók jelszavának) és az 1. lépésben használt előfizetés-azonosító.
   * Adja meg az erőforráscsoport neve a 2.
   * Adjon meg egy DNS-zóna neve, az Ön által választott.

## <a name="nuget-packages-and-namespace-declarations"></a>NuGet-csomagok és a névtér-deklarációk

Az Azure DNS .NET SDK használatához telepíteni kell a **Azure DNS könyvtár** NuGet-csomagot, és más szükséges Azure csomagok.

1. A **Visual Studio**, vagy egy új projekt megnyitása.
2. Ugrás a **eszközök**  **>**  **NuGet-Csomagkezelő**  **>**  **megoldás NuGet-csomagok kezelése...** .
3. Kattintson **Tallózás**, engedélyezze a **Include prerelease** jelölőnégyzetet, és írja be **Microsoft.Azure.Management.Dns** be a keresőmezőbe.
4. Válassza ki a csomagot, és kattintson a **telepítése** veheti fel a Visual Studio-projektet.
5. Ismételje meg a következő csomagok telepítése is a fenti folyamatot: **Microsoft.Rest.ClientRuntime.Azure.Authentication** és **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Névtér-deklarációk hozzáadása

A következő névtér-deklarációk hozzáadása

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>A DNS-felügyeleti ügyfél inicializálása

A *DnsManagementClient* a módszerek és a szükséges DNS-zónák és a rekordhalmazok kezelése tulajdonságait tartalmazza.  Az alábbi kód egyszerű szolgáltatás fiókja bejelentkezik, és egy DnsManagementClient objektumot hoz létre.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>Hozható létre vagy frissíthető a DNS-zóna

DNS-zóna létrehozása, először egy "Zóna" objektumot hoz létre a DNS-zóna paramétereit. DNS-zónák nem kapcsolódnak egy adott területre, mert a hely értéke "global". Ebben a példában egy [Azure Resource Manager "tag"](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) felvétele a zónába is megjelenik.

Ténylegesen létrehozásához, vagy az Azure DNS-zóna frissítéséhez, a zóna objektum a zóna paramétereit tartalmazó van lett átadva a *DnsManagementClient.Zones.CreateOrUpdateAsyc* metódust.

> [!NOTE]
> DnsManagementClient művelet három módot támogat: szinkron ("CreateOrUpdate"), aszinkron ("CreateOrUpdateAsync"), vagy aszinkron, aki hozzáféréssel rendelkezik a HTTP-válasz (CreateOrUpdateWithHttpMessagesAsync).  Attól függően, hogy az alkalmazások igényeihez ezek mód választható.

Az Azure DNS támogatja az egyidejű hozzáférések optimista, úgynevezett [ETag-EK](dns-getstarted-create-dnszone.md). Ebben a példában megadása "*" a "If-None-Match" a fejléc közli az Azure DNS számára hozzon létre egy DNS-zónát, ha egy nem létezik.  A hívás sikertelen lesz, ha egy zónát a megadott névvel már létezik a megadott erőforráscsoportban.

```cs
// Create zone parameters
var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"

// Create a Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
dnsZoneParams.Tags = new Dictionary<string, string>();
dnsZoneParams.Tags.Add("dept", "finance");

// Create the actual zone.
// Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
// Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
// Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");
```

## <a name="create-dns-record-sets-and-records"></a>DNS-rekordhalmazok és rekordok létrehozása

DNS-rekordokat a rendszer kezeli, a rekordhalmaz. Rekordhalmaz olyan rekord az azonos nevű és típusú rekordok a zónán belül.  A rekordhalmaz nevének megadása képest a zóna nevét, nem teljesen minősített DNS-nevét.

Hozzon létre vagy frissíthető, a "Rekordhalmaz" paraméterek objektum létrehozása és átadott *DnsManagementClient.RecordSets.CreateOrUpdateAsync*. Mivel a DNS-zóna esetén három működési módokat támadja: szinkron ("CreateOrUpdate"), aszinkron ("CreateOrUpdateAsync"), vagy aszinkron, aki hozzáféréssel rendelkezik a HTTP-válasz (CreateOrUpdateWithHttpMessagesAsync).

Csakúgy, mint a DNS-zónák rekordhalmazok műveletek közé tartozik a hozzáférések optimista támogatása.  Ebben a példában mivel sem az "If-Match", sem az "If-None-Match" meg van adva, a rekordhalmaz mindig létrejön.  Ez a hívás felülírja a meglévő rekordhalmazt azonos nevű és rekordtípust a DNS-zóna.

```cs
// Create record set parameters
var recordSetParams = new RecordSet();
recordSetParams.TTL = 3600;

// Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
recordSetParams.ARecords = new List<ARecord>();
recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

// Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
recordSetParams.Metadata = new Dictionary<string, string>();
recordSetParams.Metadata.Add("user", "Mary");

// Create the actual record set in Azure DNS
// Note: no ETAG checks specified, will overwrite existing record set if one exists
var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);
```

## <a name="get-zones-and-record-sets"></a>Zónák és a rekordhalmazok

A *DnsManagementClient.Zones.Get* és *DnsManagementClient.RecordSets.Get* módszerek beolvasása az egyes zónák és a rekordhalmazok, illetve. Rekordhalmazok típusuk, nevét és a zóna és a erőforrás csoport léteznek az azonosítják. A név és az erőforráscsoport léteznek a zónák azonosítja.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Egy meglévő rekordhalmaz frissítése

Egy meglévő DNS-rekordhalmaz frissíteni, először a rekordhalmaz beolvasása, majd frissítik rekordhalmaz tartalmát, majd küldje el a módosítást.  Ebben a példában a "Etag" a "If-Match" paraméter, a beolvasott rekord készletből adtuk meg. A hívás sikertelen lesz, ha egy párhuzamos művelet módosította a rekordhalmaz időközben.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Lista zóna és a rekordhalmazok

A zónák listában használja a *DnsManagementClient.Zones.List...*  módszereket támogatja, vagy egy adott erőforráscsoportban található összes zóna vagy egy adott Azure-előfizetésben minden zóna listázása (között erőforráscsoportok.) A rekordhalmazok listában használja *DnsManagementClient.RecordSets.List...*  módszereket támogatja, vagy egy adott zónában minden rekordkészletet vagy csak adott típusú ezen rekordhalmazok listázása.

Jegyezze fel a zónák listázásakor, és a rekordhalmazok eredmények lehet, hogy paginated.  A következő példa bemutatja, hogyan iterációt az eredmények oldalát. (Egy mesterségesen kis oldalméret "2" használatával kényszerítheti a lapozás; a gyakorlatban ez a paraméter nincs megadva és az alapértelmezett oldalméret használt.)

```cs
// Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
// In practice, to improve performance you would use a large page size or just use the system default
int recordSets = 0;
var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
recordSets += page.Count();

while (page.NextPageLink != null)
{
    page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
    recordSets += page.Count();
}
```

## <a name="next-steps"></a>Következő lépések

Töltse le a [Azure DNS .NET SDK mintaprojektet](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), mely tartalmazza az Azure DNS .NET SDK-val, többek között például a más DNS-rekord típust további példákat.
