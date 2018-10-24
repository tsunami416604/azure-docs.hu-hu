---
title: Hozzon létre DNS-zónák és -rekordhalmazok az Azure DNS-ben a .NET SDK használatával |} A Microsoft Docs
description: Hogyan hozhat létre DNS-zónák és -rekordhalmazok az Azure DNS-ben a .NET SDK használatával.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: victorh
ms.openlocfilehash: a814c543b9f4bfe6717e639342d82ed13dac35b0
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49954606"
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Hozzon létre DNS-zónák és -rekordhalmazok a .NET SDK használatával

Műveletek létrehozása, törlése, vagy frissítse a DNS-zónák, rekordhalmazok és rekordok SDK-val a DNS a DNS-kezelési .NET Library automatizálható. Érhető el egy teljes Visual Studio-projekt [itt.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Hozzon létre egy egyszerű szolgáltatásfiókot

Általában Azure-erőforrásokhoz való programozott hozzáférést kapnak a saját felhasználói hitelesítő adatokat, hanem egy dedikált fiókot keresztül. A dedikált fiókok az úgynevezett "egyszerű" szolgáltatásfiókok. Az Azure DNS SDK mintaprojektet használatához először hozzon létre egy egyszerű szolgáltatásfiókot, és rendelje hozzá a megfelelő engedélyekkel.

1. Hajtsa végre a [ezek az utasítások](../active-directory/develop/howto-authenticate-service-principal-powershell.md) , hozzon létre egy egyszerű szolgáltatásfiókot (az Azure DNS SDK mintaprojektet a jelszóalapú hitelesítés feltételezi.)
2. Hozzon létre egy erőforráscsoportot ([Íme, miként](../azure-resource-manager/resource-group-template-deploy-portal.md)).
3. Az Azure RBAC használatával engedélyeket az egyszerű szolgáltatásfiók "DNS-zóna Közreműködője" az erőforráscsoport ([Íme, miként](../role-based-access-control/role-assignments-portal.md).)
4. Ha használja az Azure DNS SDK mintaprojektet, szerkessze a következő a "program.cs" fájlt:

   * Helyezze be a megfelelő értékeit a `tenantId`, `clientId` (más néven Fiókazonosító), `secret` (egyszerű szolgáltatásfiók jelszavának) és `subscriptionId` az 1. lépésben használt.
   * Adja meg az erőforráscsoport nevét a 2. lépésben kiválasztott.
   * Adjon meg egy tetszőleges DNS-zóna neve.

## <a name="nuget-packages-and-namespace-declarations"></a>NuGet-csomagok és a névtér-deklarációk

Az Azure DNS .NET SDK használatához telepítenie kell a **Azure DNS Management Library** NuGet-csomagot és egyéb szükséges Azure-csomagok.

1. A **Visual Studio**, nyisson meg egy projekt vagy új projektet.
2. Lépjen a **eszközök** **>** **NuGet-Csomagkezelő** **>** **a NuGet-csomagok kezelése Megoldás...** .
3. Kattintson a **Tallózás**, engedélyezése a **Include prerelease** jelölőnégyzetet, és írja be **Microsoft.Azure.Management.Dns** szöveget a keresőmezőbe.
4. Válassza ki a csomagot, és kattintson a **telepítése** a Visual Studio-projektben történő hozzáadáshoz.
5. Ismételje meg a következő csomagok is telepítheti a fenti folyamatot: **Microsoft.Rest.ClientRuntime.Azure.Authentication** és **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Névtér-deklarációk hozzáadása

Az alábbi névtér-deklarációk hozzáadása

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>A DNS-felügyeleti ügyfél inicializálása

A `DnsManagementClient` a metódusok és a szükséges DNS-zónák és -rekordhalmazok kezelése tulajdonságait tartalmazza.  A következő kódot az egyszerű szolgáltatásfiók bejelentkezik, és létrehoz egy `DnsManagementClient` objektum.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>Hozzon létre vagy DNS-zóna frissítése

A DNS-zóna létrehozása, először egy "Zóna" objektum létrehozása a DNS-zóna paramétereket tartalmaz. DNS-zónák nem kapcsolódnak egy adott régióban, mert a hely értéke "global". Ebben a példában egy [Azure Resource Manager "tag"](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) is adnak hozzá a zónát.

Valójában létrehozni vagy frissíteni a zóna Azure DNS-ben, a zóna paramétereket tartalmazó zóna objektumnak átadott a `DnsManagementClient.Zones.CreateOrUpdateAsyc` metódust.

> [!NOTE]
> DnsManagementClient művelet három módot támogat: a szinkron ("CreateOrUpdate"), aszinkron ("CreateOrUpdateAsync"), vagy a HTTP-válasz (CreateOrUpdateWithHttpMessagesAsync) hozzáféréssel rendelkező aszinkron.  Alkalmazás igényeitől függően e módok közül választhat.

Az Azure DNS támogatja az optimista egyidejűséget, nevű [ETag](dns-getstarted-create-dnszone.md). Ebben a példában adja meg "*" a "If-None-Match" fejlécet arra utasítja az Azure DNS a DNS-zóna létrehozása, ha egy nem létezik.  A hívás sikertelen lesz, ha egy zónát a megadott névvel már létezik a megadott erőforráscsoportban.

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

DNS-rekordok rekordhalmazhoz felügyelt. Rekordhalmaz rekordkészleteken a ugyanazzal a névvel és a rekordtípus zónán belül.  A rekordhalmaz nevének képest a zóna nevét, a nem teljesen minősített DNS-neve van.

Egy "A rekordhalmaz" paraméterek objektumot hozza létre és átadott létrehozása vagy frissítése egy rekordhalmazt, `DnsManagementClient.RecordSets.CreateOrUpdateAsync`. A DNS-zónák vannak művelet három mód: szinkron ("CreateOrUpdate"), aszinkron ("CreateOrUpdateAsync"), vagy a HTTP-válasz (CreateOrUpdateWithHttpMessagesAsync) hozzáféréssel rendelkező aszinkron.

DNS-zóna, a rekordhalmazok műveleteket támogatják az optimista egyidejűséget.  Ebben a példában mivel sem a "If-Match", sem pedig az "If-None-Match" nincs megadva, a rekordhalmaz mindig jön létre.  Ez a hívás bármely létező rekordot a ugyanazzal a névvel és a rekordtípus a DNS-zóna felülírja.

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

## <a name="get-zones-and-record-sets"></a>Beolvasni a zónák és -rekordhalmazok

A `DnsManagementClient.Zones.Get` és `DnsManagementClient.RecordSets.Get` módszerek lekérni az egyes zónák és -rekordhalmazok, illetve. Rekordhalmazok azok típusától, nevét és a zóna és -erőforrás csoport léteznek az azonosítja. Zónák azonosítja a nevét és az erőforráscsoportot, ezek jelenhetnek meg.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Egy meglévő rekordhalmaz frissítése

Egy meglévő DNS-rekordhalmaz frissíteni, először beolvasni a beállított rekord, majd a rekordhalmaz tartalmának frissítése, majd küldje el a módosítást.  Ebben a példában a lekért rekordhalmaz az "If-Match" paraméterben az "Etag" adjuk meg. A hívás sikertelen lesz, ha egy párhuzamos művelet módosította a rekordot, addig beállítása.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Lista zónák és -rekordhalmazok

Zónák felsorolásához használja az *DnsManagementClient.Zones.List...*  metódusokkal, amelyeket támogatja az adott erőforráscsoport tartozó összes zónát, vagy egy adott Azure-előfizetéshez tartozó összes zónát listázása (több erőforráscsoportban.) Rekordhalmazok felsorolásához használja *DnsManagementClient.RecordSets.List...*  metódusokkal, amelyeket támogatásához, vagy a megadott zónában lévő összes rekordhalmazt, vagy csak egy adott típusú ezeket rekordhalmazok listázása.

Zónák listázása során vegye figyelembe, és a rekordhalmazok az eredmények oldalakra osztott is lehet.  Az alábbi példa bemutatja, hogyan iteráció az eredmények oldalát. ("2" egy mesterségesen kis oldalméret funkciója lapozófájl kényszerítése; a gyakorlatban ez a paraméter nincs megadva az oldalméret alapértelmezés szerint.)

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

## <a name="next-steps"></a>További lépések

Töltse le a [Azure DNS .NET SDK mintaprojektet](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), amely tartalmazza az Azure DNS .NET SDK, beleértve a más DNS-rekord típust példák használatával további példákat.
