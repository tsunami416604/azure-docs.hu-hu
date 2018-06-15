---
title: Az Azure Service Fabric DNS-szolgáltatás |} Microsoft Docs
description: A Service Fabric dns szolgáltatás mikroszolgáltatások létrehozására az a fürtben található felderítésének segítségével.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/27/2017
ms.author: msfussell
ms.openlocfilehash: 656aed1f1fbd3294c4318520058ace480fd2219c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204994"
---
# <a name="dns-service-in-azure-service-fabric"></a>Az Azure Service Fabric DNS-szolgáltatás
A DNS szolgáltatás nem egy választható-szolgáltatás, amely a fürt felderíteni a más szolgáltatásokkal, DNS protokoll használatával engedélyezheti. 

Számos szolgáltatás, főleg indexelése szolgáltatások lehet egy meglévő URL-címet, és tudnak majd hárítsa el őket a szabványos DNS protokoll (ahelyett, hogy a szolgáltatás protokoll) használatával kívánatos, különösen a "növekedési és az eltolás mértékét megadó" forgatókönyvek. A DNS-szolgáltatás lehetővé teszi, hogy a DNS-nevek hozzárendelése egy szolgáltatásnevet, és ezért a végpont IP-címeket feloldani. 

A DNS-szolgáltatás DNS-nevek viszont szünteti meg a szolgáltatás a szolgáltatás végpontjának vissza szolgáltatásnevek rendeli hozzá. A szolgáltatás DNS-nevét a létrehozás időpontjában valósul meg.

![Szolgáltatás-végpontok](./media/service-fabric-dnsservice/dns.png)

A DNS-szolgáltatás nem támogatja dinamikus portokat. A dinamikus portokat elérhetővé tett szolgáltatások megoldásához használja a [fordított proxy szolgáltatás](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>A DNS szolgáltatás engedélyezése
A portál használatával fürt létrehozásakor a DNS-szolgáltatás alapértelmezés szerint engedélyezve van-e a **közé tartozik a DNS-szolgáltatás** jelölőnégyzetet a **fürtkonfiguráció** menüben:

![A portálon keresztül a DNS szolgáltatás engedélyezése][2]

Ha nem használja a portál a fürt létrehozásához, vagy egy meglévő fürt frissítése folyamatban, szüksége lesz egy DNS-szolgáltatás engedélyezése:

- Új fürt üzembe helyezéséhez használhatja a [mintasablon használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) , vagy hozzon létre egy saját Resource Manager-sablon. 
- Meglévő fürt frissítéséhez lépjen a fürterőforrás-csoport a portálon, majd kattintson a **automatizálási parancsfájl** történő együttműködésre a sablont, amely a fürt és más erőforrások, a csoport aktuális állapotát. További tudnivalókért lásd: [erőforráscsoport a sablon exportálása](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template#export-the-template-from-resource-group).

Miután egy sablon, engedélyezheti a DNS-szolgáltatás a következő lépések:

1. Ellenőrizze, hogy a `apiversion` értéke `2017-07-01-preview` vagy újabb a `Microsoft.ServiceFabric/clusters` erőforrás, és, ha nem, frissítheti a következő kódrészletben látható:

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

3. A fürt sablon előző módosításainak frissítése után alkalmazza őket, és lehetővé teszik a frissítés befejeződött. A frissítés befejezése után a DNS-rendszer szolgáltatás elindul a fürtön. A szolgáltatásnév `fabric:/System/DnsService`, és a megtalálja a **rendszer** szakaszában a Service Fabric Explorerben. 


## <a name="setting-the-dns-name-for-your-service"></a>A szolgáltatás DNS-nevének beállítása
Miután a DNS-szolgáltatás fut a fürtön, beállíthat egy DNS-nevet a szolgáltatások deklaratív módon az alapértelmezett szolgáltatások vagy a `ApplicationManifest.xml` vagy PowerShell-parancsok használatával.

A DNS-neve, a szolgáltatás: a fürt teljes feloldható. Javasoljuk, hogy használja-e egy elnevezési sémája `<ServiceDnsName>.<AppInstanceName>`, például `service1.application1`. Ezzel biztosítja, a DNS-nevét, a fürt teljes egyediségét. Ha egy alkalmazás lett telepítve, használja a Docker compose, services rendszer automatikusan hozzárendeli a DNS-nevek használatával az elnevezési sémát.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>A DNS-nevét, egy alapértelmezett szolgáltatás a ApplicationManifest.xml beállítását
Nyissa meg a projektet a Visual Studio vagy a kedvenc szerkesztőt, és nyissa meg a `ApplicationManifest.xml` fájlt. Nyissa meg az alapértelmezett szolgáltatások szakaszt, és minden egyes szolgáltatás hozzáadása a `ServiceDnsName` attribútum. A következő példa bemutatja, hogyan kell beállítani a szolgáltatás DNS-neve `service1.application1`

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
A szolgáltatás DNS-neve használatával létrehozásakor beállíthatja a `New-ServiceFabricService` Powershell. Az alábbi példa létrehoz egy új állapotmentes szolgáltatást a DNS-névvel `service1.application1`

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
Ha egynél több szolgáltatást telepít, a többi szolgáltatás egy DNS-név használatával kommunikálni végpontok is megtalálhatja. A DNS-szolgáltatás tulajdonság csak állapotmentes szolgáltatások vonatkozik, mert a DNS protokoll állapotalapú szolgáltatások nem tudnak kommunikálni. Állapotalapú szolgáltatások esetén használhatja a beépített [fordított proxy szolgáltatás](./service-fabric-reverseproxy.md) http hívások egy adott szolgáltatáshoz partíció hívni. A DNS-szolgáltatás nem támogatja dinamikus portokat. A fordított proxy segítségével hárítsa el a szolgáltatásokat, a dinamikus portok használatára.

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

## <a name="next-steps"></a>További lépések
További információ a fürtön belül a szolgáltatások közötti kommunikáció [csatlakozzon és szolgáltatásokkal kommunikálni](service-fabric-connect-and-communicate-with-services.md)

[0]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[1]: ./media/service-fabric-dnsservice/servicefabric-explorer-dns.PNG
[2]: ./media/service-fabric-dnsservice/DNSService.PNG
