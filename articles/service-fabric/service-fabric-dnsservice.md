---
title: Az Azure Service Fabric DNS-szolgáltatás |} A Microsoft Docs
description: Felderítéséhez a fürtön belül a mikroszolgáltatások Service Fabric dns szolgáltatást használni.
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
ms.date: 7/20/2018
ms.author: msfussell
ms.openlocfilehash: a420033d96a1366a79f5f2032693c38d7eca4ac3
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2018
ms.locfileid: "48830973"
---
# <a name="dns-service-in-azure-service-fabric"></a>Az Azure Service Fabric DNS-szolgáltatás
A DNS szolgáltatás nem egy választható rendszerszolgáltatás, amely a fürt engedélyezheti a DNS protokollt használó egyéb szolgáltatások észlelését. 

Számos szolgáltatás, különösen a tárolóalapú szolgáltatásokat is címezhető egy már meglévő URL-címet. Oldja meg ezeket a szolgáltatásokat a Service Fabric Naming Service protokoll helyett a standard DNS protokoll használatával történő kívánatos. A DNS-szolgáltatás lehetővé teszi, hogy a DNS-név leképezése a szolgáltatás nevét, és ezért a végpont IP-címek feloldása. Funkció tartja karban a tárolóalapú szolgáltatásokat hordozhatóságát több különböző platformon, és végezhet "lift and shift" forgatókönyvek könnyebben, azáltal, hogy meglévő szolgáltatás URL-címek-ekkel nem kell újraírnia kihasználhatja az elnevezési szolgáltatásban. 

A DNS-szolgáltatás DNS-nevek viszont az elnevezési szolgáltatásban való visszatéréshez a szolgáltatásvégpont által megoldott szolgáltatásnevek rendeli hozzá. A szolgáltatás DNS-nevét a létrehozás időpontjában van megadva. Az alábbi ábrán látható, a DNS-szolgáltatás működése az állapotmentes szolgáltatások esetében.

![Szolgáltatásvégpontok](./media/service-fabric-dnsservice/stateless-dns.png)

A Service Fabric verziója 6.3 a kiadástól kezdve a Service Fabric DNS protokoll ki van terjesztve címzéshez particionált állapotalapú szolgáltatások egy sémát tartalmazza. Ezek a bővítmények koncepcióját oldja meg az adott partíció IP-címek használatával a partíció neve és az állapotalapú szolgáltatás DNS-név kombinációját. A három particionálási sémákat támogatottak:

- A particionálás nevű
- A particionálás előre
- Egyszeres particionálása

Az alábbi ábrán látható, a DNS-szolgáltatás működése particionált állapotalapú szolgáltatásokhoz.

![állapot-nyilvántartó Szolgáltatásvégpontok](./media/service-fabric-dnsservice/stateful-dns.png)

Dinamikus portok nem támogatottak a DNS-szolgáltatás. A dinamikus portokat elérhetővé tett szolgáltatások megoldásához használja a [fordított proxy szolgáltatás](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>A DNS szolgáltatás engedélyezése
A portállal egy fürtöt hoz létre, amikor a DNS-szolgáltatás alapértelmezés szerint engedélyezve van-e a **közé tartozik a DNS-szolgáltatás** jelölőnégyzet be van jelölve a **fürtkonfiguráció** menüben:

![A portálon keresztül DNS szolgáltatás engedélyezése](./media/service-fabric-dnsservice/enable-dns-service.png)

Ha nem használ a portálon a fürt létrehozásához, vagy amikor frissít egy meglévő fürthöz, akkor engedélyeznie kell a DNS szolgáltatás a sablonban:

- Új fürt üzembe helyezéséhez használhatja a [mintasablon](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) vagy a saját Resource Manager-sablon létrehozása. 
- Meglévő fürt frissítéséhez navigálhat a portálon, majd kattintson a fürt erőforráscsoportot **Automation-szkript** dolgozhat a fürt és egyéb erőforrások a csoport aktuális állapotát tükröző sablont. További tudnivalókért lásd: [az erőforráscsoport sablonjának exportálása](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template#export-the-template-from-resource-group).

Miután egy sablont, a DNS szolgáltatás a következő lépésekkel engedélyezhető:

1. Ellenőrizze, hogy a `apiversion` értékre van állítva `2017-07-01-preview` vagy újabb a `Microsoft.ServiceFabric/clusters` erőforrás, és ha nem, frissítse azt a következő példában látható módon:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Most lehetővé teszi a DNS szolgáltatás a következő módszerek valamelyikével:

   - Ahhoz, hogy a DNS-szolgáltatás az alapértelmezett beállításokkal, adja hozzá a `addonFeatures` szakaszon belül a `properties` szakasz az alábbi példában látható módon:

       ```json
           "properties": {
              ...

              "addonFeatures": [
                "DnsService"
              ],
              ...
           }
       ```
   - A szolgáltatás az alapértelmezett beállítások eltérő engedélyezéséhez adjon hozzá egy `DnsService` részt a `fabricSettings` szakaszon belül a `properties` szakaszban. Ebben az esetben nem kell hozzáadnia a nincs, `addonFeatures`. A DNS-szolgáltatás megadható tulajdonságaival kapcsolatos további tudnivalókért lásd: [DNS-szolgáltatás beállítások](./service-fabric-cluster-fabric-settings.md#dnsservice).

       ```json
           "properties": {
             ...  
             "fabricSettings": [
               ...
               {
                 "name": "DnsService",
                 "parameters": [
                   {
                     "name": "IsEnabled",
                     "value": "true"
                   },
                   {
                     "name": "PartitionSuffix",
                     "value": "--"
                   },
                   {
                     "name": "PartitionPrefix",
                     "value": "--"
                   }
                 ]
               },
               ...
              ]
            }
       ```
1. A módosítások a fürt sablon frissítése után alkalmazza őket, és lehetővé teszik a frissítés befejeződött. A frissítés befejeződése után a rendszer DNS-szolgáltatás a fürtön belül elindul. A szolgáltatás neve `fabric:/System/DnsService`, és annak alapján, a **rendszer** szakaszában a Service Fabric Explorerben. 


## <a name="setting-the-dns-name-for-your-service"></a>A DNS-név, a szolgáltatás beállítása
Beállíthat egy DNS-nevet, a szolgáltatások deklaratív az alapértelmezett szolgáltatások az ApplicationManifest.xml fájl- vagy PowerShell-parancsok használatával.

A szolgáltatás DNS-neve feloldható terjesszen a fürtön, ezért fontos a DNS-név az egyediség biztosítása céljából a fürtön. 

Azt javasoljuk, hogy használhatja-e egy elnevezési sémája `<ServiceDnsName>.<AppInstanceName>`; például `service1.application1`. Ha egy alkalmazást helyezünk üzembe a Docker compose, a szolgáltatások automatikusan hozzárendeli ezt a fájlelnevezési rendszert használó DNS-neveket.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>A DNS-név, egy alapértelmezett szolgáltatás beállítása az ApplicationManifest.xml
Nyissa meg a projektjét a Visual Studióban, vagy a kedvenc szerkesztőjében, és nyissa meg az ApplicationManifest.xml fájlt. Nyissa meg az alapértelmezett szolgáltatások szakaszt, és minden egyes szolgáltatás hozzáadása a `ServiceDnsName` attribútum. Az alábbi példa bemutatja, hogyan állíthatja be a szolgáltatást, hogy a DNS-neve `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
Az alkalmazás üzembe helyezése után, a szolgáltatás-példánya a Service Fabric Explorerben ebben az esetben a DNS-nevét jeleníti meg az alábbi ábrán látható módon: 

![Szolgáltatásvégpontok](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

A következő példa egy állapotalapú szolgáltatás DNS-nevét állítja `statefulsvc.app`. A szolgáltatás egy elnevezett particionálási sémát használ. Figyelje meg, hogy a partíció neve kisbetűket. Ez azért szükséges, hogy a DNS-lekérdezések; célzott partíciók További információkért lásd: [így DNS-lekérdezéseket egy állapotalapú szolgáltatás partíció](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-dnsservice#preview-making-dns-queries-on-a-stateful-service-partition).

```xml
    <Service Name="Stateful1" ServiceDnsName="statefulsvc.app" />
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="2" MinReplicaSetSize="2">
        <NamedPartition>
         <Partition Name="partition1" />
         <Partition Name="partition2" />
        </NamedPartition>
      </StatefulService>
    </Service>
```

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>A DNS-név, egy Powershell-lel szolgáltatás beállítása
A szolgáltatás DNS-név használatával létrehozásakor beállíthatja a `New-ServiceFabricService` Powershell-parancsot. A következő példában létrehozunk egy új állapotmentes szolgáltatás DNS-névvel `service1.application1`

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

## <a name="preview-making-dns-queries-on-a-stateful-service-partition"></a>[Előzetes verzió] DNS-lekérdezések tétele az állapotalapú szolgáltatás partíción
A Service Fabric DNS-szolgáltatás a Service Fabric verziója 6.3 a kiadástól kezdve a szolgáltatás partícióinak támogatja lekérdezéseket.

A DNS-lekérdezésekben használt partíciók az alábbi elnevezési korlátozások vonatkoznak:

   - Partíciónevek DNS megfelelőnek kell lennie.
   - Több címke partíciónevek (, amelyek tartalmaznak, ponttal, ".", a név) nem használható.
   - Partíciónevek kisbetűs kell lennie.

DNS-lekérdezések egy partíció célzó a következőképpen vannak formázva:

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
Az elemek magyarázata:

- *First-Label-Of-Partitioned-Service-DNSName* a szolgáltatás DNS-nevének első része.
- *PartitionPrefix* érték, amely akkor állítható be a fürtjegyzék vagy a fürt Resource Manager-sablon segítségével nincs szakaszában. Az alapértelmezett érték "-". További tudnivalókért lásd: [DNS-szolgáltatás beállítások](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Partíció-célnév* a partíció neve. 
- *PartitionSuffix* érték, amely akkor állítható be a fürtjegyzék vagy a fürt Resource Manager-sablon segítségével nincs szakaszában. Az alapértelmezett érték: üres karakterlánc. További tudnivalókért lásd: [DNS-szolgáltatás beállítások](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Fennmaradó-particionált-Service-DNSName* hátralévő része a szolgáltatás DNS-nevét.

Az alábbi példák bemutatják, amely az alapértelmezett beállításokkal rendelkezik a fürtön futó particionált szolgáltatások DNS-lekérdezései `PartitionPrefix` és `PartitionSuffix`: 

- Partíció "0" a szolgáltatás DNS-névvel megoldásához `backendrangedschemesvc.application` , amely egy ranged particionálási sémát használ, használja `backendrangedschemesvc-0.application`.
- Partíció megoldásához "first" a szolgáltatás DNS-névvel `backendnamedschemesvc.application` , amely egy elnevezett particionálási sémát használ, használja `backendnamedschemesvc-first.application`.

A DNS szolgáltatás a partíció az elsődleges replika IP-címét adja vissza. Ha nincs partíció van megadva, a szolgáltatás egy véletlenszerűen kiválasztott partíció az elsődleges replika IP-címét adja vissza.

## <a name="using-dns-in-your-services"></a>A szolgáltatások a DNS-sel
Ha egynél több szolgáltatást telepít, a végpontok egy DNS-név használatával kommunikálni más szolgáltatások is megtalálhatja. A DNS-szolgáltatás működik az állapotmentes szolgáltatások esetében, és a Service Fabric 6.3-es és újabb verziók, az állapotalapú szolgáltatások esetében. A Service Fabric előtt 6.3 verzióin futó állapotalapú szolgáltatások esetében is használhatja a beépített [fordított proxy szolgáltatás](./service-fabric-reverseproxy.md) meghívni egy adott szolgáltatás partíció http-hívásokhoz. 

Dinamikus portok nem támogatottak a DNS-szolgáltatás. A fordított proxy szolgáltatás használatával oldja meg a dinamikus portokat használó szolgáltatások.

A következő kód bemutatja, hogyan hívhat meg egy állapotmentes szolgáltatás DNS-en keresztül. Egyszerűen egy normál http-hívás legyen, ahol meg kell a DNS-név, a port és bármilyen választható útvonalat az URL-cím részeként.

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

A következő kód mutatja egy, az állapotalapú szolgáltatások egy adott partícióra. Ebben az esetben a DNS-neve tartalmazza a partíció neve (partition1). A hívás feltételezi, hogy a fürt alapértelmezett értékekkel `PartitionPrefix` és `PartitionSuffix`.

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
            Uri uri = new Uri("http://service2-partition1.application1:8080/api/values");
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

## <a name="known-issues"></a>Ismert problémák
* A Service Fabric 6.3 és újabb verziók szolgáltatás-nevek DNS-nevet a kötőjelet tartalmazó DNS-keresések probléma van. Ennél a hibánál további információkért kérjük nyomon követése a következő [GitHub-problémát](https://github.com/Azure/service-fabric-issues/issues/1197). Ez javítja a következő 6.3 frissítés hamarosan elérhető. 

## <a name="next-steps"></a>További lépések
További információ a fürtben a szolgáltatások közötti kommunikáció [csatlakozhat, és a kommunikáció a szolgáltatásokkal](service-fabric-connect-and-communicate-with-services.md)

