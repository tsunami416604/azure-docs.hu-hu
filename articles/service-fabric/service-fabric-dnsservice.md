---
title: "Az Azure Service Fabric DNS-szolgáltatás |} Microsoft Docs"
description: "A Service Fabric dns szolgáltatás mikroszolgáltatások létrehozására az a fürtben található felderítésének segítségével."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/27/2017
ms.author: msfussell
ms.openlocfilehash: 9871bc5aa4e74ab0faef401d67c4e9558eb5e14b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="dns-service-in-azure-service-fabric"></a>Az Azure Service Fabric DNS-szolgáltatás
A DNS szolgáltatás nem egy választható-szolgáltatás, amely a fürt felderíteni a más szolgáltatásokkal, DNS protokoll használatával engedélyezheti.

Számos szolgáltatás, főleg indexelése szolgáltatások lehet egy meglévő URL-címet, és tudnak majd hárítsa el őket a szabványos DNS protokoll (ahelyett, hogy a szolgáltatás protokoll) használatával kívánatos, különösen a "növekedési és az eltolás mértékét megadó" forgatókönyvek. A DNS-szolgáltatás lehetővé teszi, hogy a DNS-nevek hozzárendelése egy szolgáltatásnevet, és ezért a végpont IP-címeket feloldani. 

A DNS-szolgáltatás DNS-nevek viszont szünteti meg a szolgáltatás a szolgáltatás végpontjának vissza szolgáltatásnevek rendeli hozzá. A szolgáltatás DNS-nevét a létrehozás időpontjában valósul meg. 

![Szolgáltatás-végpontok][0]

## <a name="enabling-the-dns-service"></a>A DNS szolgáltatás engedélyezése
Először lehetővé kell tenni a DNS-szolgáltatás a fürtön. Szerezze be a sablon a fürt, amely számára telepíteni kívánja. Használhatja a [mintasablon használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) vagy Resource Manager-sablon létrehozása. Engedélyezheti a DNS-szolgáltatás a következő lépéseket:

1. Ellenőrizze, hogy a `apiversion` értékre van állítva `2017-07-01-preview` a a `Microsoft.ServiceFabric/clusters` erőforrás, és ha nem, frissítheti a következő kódrészletben látható:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Most lehetővé teszi a DNS-szolgáltatás a következő `addonFeatures` szakasz után a `fabricSettings` szakaszban, ahogy az a következő kódrészletet: 

    ```json
        "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "DnsService"
        ],
    ```

3. A fürt sablon előző módosításainak frissítése után alkalmazza őket, és lehetővé teszik a frissítés befejeződött. Művelet befejeződése után a DNS-rendszer szolgáltatás elindul a fürtön is nevezett `fabric:/System/DnsService` rendszer szolgáltatás szakaszban a Service Fabric explorer. 

Azt is megteheti a DNS-szolgáltatás a portálon keresztül is engedélyezheti a fürt létrehozása során. A négyzet bejelölésével engedélyezheti a DNS-szolgáltatás `Include DNS service` a a `Cluster configuration` menü a következő képernyőfelvételen látható módon:

![A portálon keresztül a DNS szolgáltatás engedélyezése][2]


## <a name="setting-the-dns-name-for-your-service"></a>A szolgáltatás DNS-nevének beállítása
Miután a DNS-szolgáltatás fut a fürtön, beállíthat egy DNS-nevet a szolgáltatások deklaratív módon az alapértelmezett szolgáltatások vagy a `ApplicationManifest.xml` vagy Powershell-parancsok használatával.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>A DNS-nevét, egy alapértelmezett szolgáltatás a ApplicationManifest.xml beállítását
Nyissa meg a projektet a Visual Studio vagy a kedvenc szerkesztőt, és nyissa meg a `ApplicationManifest.xml` fájlt. Nyissa meg az alapértelmezett szolgáltatások szakaszt, és minden egyes szolgáltatás hozzáadása a `ServiceDnsName` attribútum. A következő példa bemutatja, hogyan kell beállítani a szolgáltatás DNS-neve`service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
    <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
    </StatelessService>
    </Service>
```
Ha az alkalmazás központi telepítése, a szolgáltatáspéldány a Service Fabric Explorer ebben az esetben a DNS-nevét jeleníti meg az alábbi ábrán látható módon: 

![Szolgáltatás-végpontok][1]

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>A DNS-nevét, a Powershell szolgáltatás beállítása
A szolgáltatás DNS-neve használatával létrehozásakor beállíthatja a `New-ServiceFabricService` Powershell. Az alábbi példa létrehoz egy új állapotmentes szolgáltatást a DNS-névvel`service1.application1`

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="using-dns-in-your-services"></a>A szolgáltatások a DNS-sel
Ha egynél több szolgáltatást telepít, a többi szolgáltatás egy DNS-név használatával kommunikálni végpontok is megtalálhatja. A DNS-szolgáltatás tulajdonság csak állapotmentes szolgáltatások vonatkozik, mert a DNS protokoll állapotalapú szolgáltatások nem tudnak kommunikálni. Állapotalapú szolgáltatások esetén a http-hívásokban beépített fordított proxy használatával hívható meg egy adott szolgáltatáshoz partíció.

A következő kód bemutatja, hogyan hívhatja meg egy másik szolgáltatás, amely egyszerűen csak egy normál http hívás ahol meg kell adnia a port és bármilyen opcionális elérési utat az URL-cím részeként.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="next-steps"></a>Következő lépések
További információ a fürtön belül a szolgáltatások közötti kommunikáció [csatlakozzon és szolgáltatásokkal kommunikálni](service-fabric-connect-and-communicate-with-services.md)

[0]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[1]: ./media/service-fabric-dnsservice/servicefabric-explorer-dns.PNG
[2]: ./media/service-fabric-dnsservice/DNSService.PNG
