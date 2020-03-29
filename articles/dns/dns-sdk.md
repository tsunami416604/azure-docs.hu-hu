---
title: DNS-zónák és rekordkészletek létrehozása a .NET SDK használatával
titleSuffix: Azure DNS
description: Ebben a tanulási útvonalon ismerkedje meg a DNS-zónák és rekordkészletek létrehozásának megkezdéséhez az Azure DNS-ben a .NET SDK használatával.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: rohink
ms.openlocfilehash: c497209e456ff838786edaa19e46ebc5c1858d5f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938860"
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>DNS-zónák és rekordkészletek létrehozása a .NET SDK használatával

A DNS-zónák, rekordhalmazok és rekordok létrehozásához, törléséhez vagy frissítéséhez a DNS SDK és a .NET DNS Management könyvtár segítségével történő automatizálására automatizálható. A teljes Visual Studio projekt itt érhető [el.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Egyszerű szolgáltatásfiók létrehozása

Az Azure-erőforrásokhoz való programozott hozzáférés általában egy dedikált fiókon keresztül érhető el, nem pedig a saját felhasználói hitelesítő adatain keresztül. Ezeket a dedikált fiókokat "egyszerű szolgáltatás" fiókoknak nevezzük. Az Azure DNS SDK mintaprojekt használatához először létre kell hoznia egy egyszerű szolgáltatásfiókot, és hozzá kell rendelnie a megfelelő engedélyeket.

1. Kövesse [az alábbi utasításokat](../active-directory/develop/howto-authenticate-service-principal-powershell.md) egy egyszerű szolgáltatásfiók létrehozásához (az Azure DNS SDK mintaprojekt jelszóalapú hitelesítést feltételez.)
2. Hozzon létre egy erőforráscsoportot ([a következőképpen](../azure-resource-manager/templates/deploy-portal.md)).
3. Az Azure RBAC használatával az egyszerű szolgáltatásfiók "DNS Zone Contributor" engedélyeket adhat az erőforráscsoportnak ([a következőképpen](../role-based-access-control/role-assignments-portal.md).)
4. Ha az Azure DNS SDK mintaprojektet használja, a következőképpen szerkesztheti a "program.cs" fájlt:

   * Szúrja be a `tenantId`megfelelő `clientId` értékeket a hoz `secret` (más néven fiókazonosító), (egyszerű szolgáltatásfiók-jelszó) és `subscriptionId` az 1.
   * Adja meg a 2.
   * Adja meg a választott DNS-zónanevet.

## <a name="nuget-packages-and-namespace-declarations"></a>NuGet csomagok és névtérdeklarációk

Az Azure DNS .NET SDK használatához telepítenie kell az **Azure DNS Management Library** NuGet csomagot és más szükséges Azure-csomagokat.

1. A **Visual Studio programban**nyisson meg egy projektet vagy új projektet.
2. Látogasson el az **Eszközök** **>** **NuGet csomagkezelő** **>** **höz A nuget csomagok kezelése a megoldáshoz...**.
3. Kattintson a **Tallózás gombra,** engedélyezze a **Kiadás előtti belefoglalásjelölőnégyzetet,** és írja be a **Microsoft.Azure.Management.Dns** kifejezést a keresőmezőbe.
4. Jelölje ki a csomagot, és kattintson a **Telepítés** gombra a Visual Studio-projekthez való hozzáadásához.
5. Ismételje meg a fenti eljárást a következő csomagok telepítéséhez is: **Microsoft.Rest.ClientRuntime.Azure.Authentication** és **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Névtér-deklarációk hozzáadása

A következő névtérdeklarációk hozzáadása

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>A DNS-kezelő ügyfél inicializálása

Az `DnsManagementClient` tartalmazza a DNS-zónák és rekordkészletek kezeléséhez szükséges módszereket és tulajdonságokat.  A következő kód bejelentkezik az egyszerű `DnsManagementClient` szolgáltatásfiókba, és létrehoz egy objektumot.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>DNS-zóna létrehozása vagy frissítése

DNS-zóna létrehozásához először egy "Zóna" objektum jön létre a DNS-zóna paramétereinek tárolására. Mivel a DNS-zónák nem kapcsolódnak egy adott régióhoz, a hely "globális" lesz. Ebben a példában egy [Azure Resource Manager "címke"](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) is hozzáadódik a zónához.

A zóna tényleges létrehozásához vagy frissítéséhez az Azure DNS-ben a `DnsManagementClient.Zones.CreateOrUpdateAsyc` zóna paramétereket tartalmazó zónaobjektum átkerül a metódushoz.

> [!NOTE]
> A DnsManagementClient három működési módot támogat: szinkron ("CreateOrUpdate"), aszinkron ("CreateOrUpdateAsync"), vagy aszinkron a HTTP-válaszhoz való hozzáféréssel ("CreateOrUpdateWithHttpMessagesAsync").  Az alkalmazás igényeitől függően a következő módok közül választhat.

Az Azure DNS támogatja az [etag](dns-getstarted-create-dnszone.md)nevű optimista egyidejűséget. Ebben a példában a "*" megadása az "If-None-Match" fejléc megmondja az Azure DNS-t, hogy hozzon létre egy DNS-zónát, ha még nem létezik.  A hívás sikertelen, ha a megadott nevű zóna már létezik az adott erőforráscsoportban.

```cs
// Create zone parameters
var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"

// Create an Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
dnsZoneParams.Tags = new Dictionary<string, string>();
dnsZoneParams.Tags.Add("dept", "finance");

// Create the actual zone.
// Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
// Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
// Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");
```

## <a name="create-dns-record-sets-and-records"></a>DNS-rekordkészletek és -rekordok létrehozása

A DNS-rekordok at rekordkészletként kezeli a szolgáltatás. A rekordhalmaz egy zónán belüli azonos nevű és bejegyzéstípusú rekordhalmaz.  A rekordhalmaz neve a zónanévhez viszonyítva van, nem a teljesen minősített DNS-névhez viszonyítva.

Rekordkészlet létrehozásához vagy frissítéséhez létrejön egy "RecordSet" `DnsManagementClient.RecordSets.CreateOrUpdateAsync`paraméterobjektum, amelyet a program átad a rendszernek. A DNS-zónákhoz ugyanúgy három működési mód létezik: szinkron ("CreateOrUpdate"), aszinkron ("CreateOrUpdateAsync"), vagy ahttp-válaszhoz való hozzáféréssel rendelkező aszinkron ("CreateOrUpdateWithHttpMessagesAsync").

A DNS-zónákhoz ugyanúgy, mint a rekordhalmazokon végzett műveletek tartalmazzák az optimista egyidejűség támogatását.  Ebben a példában, mivel sem az "If-Match" vagy az "If-None-Match" nincs megadva, a rekordkészlet mindig létrejön.  Ez a hívás felülír minden olyan meglévő rekordot, amelynek neve és bejegyzéstípusa ebben a DNS-zónában azonos.

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

## <a name="get-zones-and-record-sets"></a>Zónák és rekordkészletek betöltése

A `DnsManagementClient.Zones.Get` `DnsManagementClient.RecordSets.Get` és a módszerek az egyes zónákat és rekordkészleteket kérik be. A rekordkészletek et a típusuk, nevük, valamint az a zóna és erőforráscsoport alapján azonosítjuk, amelyben léteznek. A zónákat a nevük és az erőforráscsoport alapján azonosítják, amelyben léteznek.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Meglévő rekordkészlet frissítése

Meglévő DNS-rekordkészlet frissítéséhez először olvassa be a rekordkészletet, frissítse a rekordkészlet tartalmát, majd küldje el a módosítást.  Ebben a példában az "If-Match" paraméterben megadott "Etag" értéket adtuk meg a beolvasott rekordkészletből. A hívás sikertelen, ha egy egyidejű művelet időközben módosította a rekordhalmazt.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Zónák és rekordkészletek listázása

A zónák listázásához használja a *DnsManagementClient.Zones.List... metódusokat,* amelyek támogatják egy adott erőforráscsoport összes zónájának vagy egy adott Azure-előfizetés összes zónájának listázását (erőforráscsoportok között).) Rekordhalmazok listázásához használja *a DnsManagementClient.RecordSets.List...* metódusokat, amelyek támogatják az adott zónában lévő összes rekordhalmaz felsorolását, vagy csak az adott típusú rekordhalmazokat.

Vegye figyelembe, ha a zónák és rekordhalmazok listázásakor az eredmények et lapszámozásra lehet felsorolni.  A következő példa bemutatja, hogyan kell végighaladni az eredmények oldalain. (A lapozás kényszerítéséhez mesterségesen kis "2" oldalméret áll érvényben; a gyakorlatban ezt a paramétert ki kell hagyni, és az alapértelmezett oldalméretet kell használni.)

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

Töltse le az [Azure DNS .NET SDK mintaprojektet,](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)amely további példákat tartalmaz az Azure DNS .NET SDK használatára vonatkozóan, beleértve más DNS-rekordtípusokpéldákat is.
