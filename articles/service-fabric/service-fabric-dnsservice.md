---
title: Az Azure Service Fabric DNS-szolgáltatása
description: Használja a Service Fabric dns-szolgáltatásának a fürtön belüli mikroszolgáltatások felderítéséhez.
ms.topic: conceptual
ms.date: 7/20/2018
ms.openlocfilehash: 317aa81238ec7a0dc24b69b1d00568901b9bc34f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458027"
---
# <a name="dns-service-in-azure-service-fabric"></a>DNS-szolgáltatás az Azure Service Fabricben
A DNS-szolgáltatás egy választható rendszerszolgáltatás, amely et a fürtben engedélyezheti más szolgáltatások felderítésére a DNS protokoll használatával. 

Számos szolgáltatás, különösen a tárolószolgáltatások, egy már meglévő URL-címen keresztül címezhetők. Kívánatos, hogy ezeket a szolgáltatásokat a Service Fabric naming Service protokoll helyett a szabványos DNS-protokoll használatával oldhassa fel. A DNS-szolgáltatás lehetővé teszi, hogy DNS-neveket rendeljen hozzá egy szolgáltatásnévhez, és így feloldja a végpont IP-címeit. Az ilyen funkciók fenntartják a tárolós szolgáltatások hordozhatóságát a különböző platformokközött, és megkönnyíthetik a "lift and shift" forgatókönyveket azáltal, hogy lehetővé teszik a meglévő szolgáltatás URL-címek használatát ahelyett, hogy át kellene írnia a kódot az elnevezési szolgáltatás kihasználásához. 

A DNS-szolgáltatás leképezi a DNS-neveket a szolgáltatásnevekhez, amelyeket az elnevezési szolgáltatás felold a szolgáltatás végpontjának visszaadása érdekében. A szolgáltatás DNS-neve a létrehozás időpontjában áll rendelkezésre. Az alábbi ábra bemutatja, hogyan működik a DNS-szolgáltatás az állapotmentes szolgáltatások esetében.

![szolgáltatásvégpontok](./media/service-fabric-dnsservice/stateless-dns.png)

A Service Fabric 6.3-as verziójától kezdve a Service Fabric DNS protokollja ki lett bővítve a particionált állapotalapú szolgáltatások kezelésére szolgáló sémával. Ezek a bővítmények lehetővé teszik adott partíció IP-címek feloldását az állapotalapú szolgáltatás DNS-nevének és a partíció nevének kombinációjával. Mindhárom particionálási séma támogatott:

- Elnevezett particionálás
- Tartományú particionálás
- Singleton particionálás

Az alábbi ábra bemutatja, hogyan működik a DNS-szolgáltatás particionált állapotalapú szolgáltatások.

![állapotalapú szolgáltatásvégpontok](./media/service-fabric-dnsservice/stateful-dns.png)

A DNS-szolgáltatás nem támogatja a dinamikus portokat. A dinamikus portokon elérhető szolgáltatások feloldásához használja a [fordított proxy szolgáltatást.](./service-fabric-reverseproxy.md)

## <a name="enabling-the-dns-service"></a>A DNS-szolgáltatás engedélyezése
> [!NOTE]
> A Service Fabric-szolgáltatások DNS-szolgáltatása linuxos operációs rendszer még nem támogatott.

Amikor fürtöt hoz létre a portál használatával, a DNS-szolgáltatás alapértelmezés szerint engedélyezve lesz a **Fürt konfigurációs** **menüjének DNS-szolgáltatással együtt** jelölőnégyzetében:

![DNS-szolgáltatás engedélyezése a portálon keresztül](./media/service-fabric-dnsservice/enable-dns-service.png)

Ha nem a portált használja a fürt létrehozásához, vagy ha egy meglévő fürtöt frissít, engedélyeznie kell a DNS-szolgáltatást egy sablonban:

- Új fürt központi telepítéséhez használhatja a [mintasablonokat,](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) vagy létrehozhatja saját Erőforrás-kezelő sablonját. 
- Meglévő fürt frissítéséhez keresse meg a fürt erőforráscsoportját a portálon, és kattintson az **Automation Script** parancsra, és dolgozzon egy sablont, amely tükrözi a fürt és a csoport egyéb erőforrásainak aktuális állapotát. További információ: [A sablon exportálása erőforráscsoportból](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template)című témakörben olvashat.

A sablon használata után a következő lépésekkel engedélyezheti a DNS-szolgáltatást:

1. Ellenőrizze, `apiversion` hogy az `2017-07-01-preview` `Microsoft.ServiceFabric/clusters` erőforrás beállítása vagy későbbi beállítása, és ha nem, frissítse az alábbi példában látható módon:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Most engedélyezze a DNS-szolgáltatást az alábbi módokon:

   - Ha engedélyezni szeretné a DNS-szolgáltatást az `addonFeatures` alapértelmezett `properties` beállításokkal, adja hozzá a szakaszon belüli szakaszhoz az alábbi példában látható módon:

        ```json
          "properties": {
            ...
            "addonFeatures": [
              "DnsService"
              ],
            ...
          }
        ```

   - Ha azt szeretné, hogy a szolgáltatás `DnsService` az `fabricSettings` alapértelmezett beállításoktól eltérő beállításokkal is engedélyezze, vegyen fel egy szakaszt a `properties` szakaszon belüli szakaszba. Ebben az esetben nem kell hozzáadnia a `addonFeatures`DnsService szolgáltatást a hoz. A DNS-szolgáltatáshoz beállítható tulajdonságokról a [DNS-szolgáltatás beállításai](./service-fabric-cluster-fabric-settings.md#dnsservice)című témakörben olvashat bővebben.

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
3. Miután frissítette a fürtsablont a módosításokkal, alkalmazza őket, és hagyja, hogy a frissítés befejeződjön. A frissítés befejezéseután a DNS-rendszerszolgáltatás elindul a fürtben. A szolgáltatás `fabric:/System/DnsService`neve, és a Service Fabric-kezelő **Rendszerszolgáltatás** szakaszában található. 

> [!NOTE]
> Ha a DNS-t letiltottról engedélyezve frissíti, előfordulhat, hogy a Service Fabric Explorer nem tükrözi az új állapotot. A megoldáshoz indítsa újra a csomópontokat az Azure Resource Manager-sablon upgradepolicy módosításával. További információkért tekintse meg a [Service Fabric-sablon hivatkozási.](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2019-03-01/clusters/applications)

> [!NOTE]
> Ha helyi számítógépen fejleszt a DNS-szolgáltatás, az egyes DNS-beállításokat felülír. Ha problémákat tapasztal az internethez való csatlakozással kapcsolatban, ellenőrizze a DNS-beállításokat.

## <a name="setting-the-dns-name-for-your-service"></a>A szolgáltatás DNS-nevének beállítása
A szolgáltatások DNS-nevét deklaratív módon állíthatja be az ApplicationManifest.xml fájl alapértelmezett szolgáltatásaihoz, vagy a PowerShell-parancsokon keresztül.

A szolgáltatás DNS-neve feloldható a fürtegészében, ezért fontos biztosítani a DNS-név egyediségét a fürtön keresztül. 

Erősen ajánlott a; `<ServiceDnsName>.<AppInstanceName>` például `service1.application1`. Ha egy alkalmazás docker-írással van telepítve, a szolgáltatások automatikusan DNS-neveket kapnak ezzel az elnevezési sémával.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Alapértelmezett szolgáltatás DNS-nevének beállítása az ApplicationManifest.xml fájlban
Nyissa meg a projektet a Visual Studióban vagy a kedvenc szerkesztőjében, és nyissa meg az ApplicationManifest.xml fájlt. Lépjen az alapértelmezett szolgáltatások szakaszra, és `ServiceDnsName` minden szolgáltatáshoz adja hozzá az attribútumot. A következő példa bemutatja, hogyan állíthatja be a szolgáltatás DNS-nevét`service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
Az alkalmazás üzembe helyezése után a Service Fabric-kezelő szolgáltatáspéldánya megjeleníti a példány DNS-nevét, ahogy az az alábbi ábrán látható: 

![szolgáltatásvégpontok](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

A következő példa egy állapotalapú szolgáltatás `statefulsvc.app`DNS-nevét állítja be. A szolgáltatás egy elnevezett particionálási sémát használ. Figyelje meg, hogy a partíciónevek kisbetűsek. Ez a követelmény a DNS-lekérdezésekben megcélzott partíciókesetében követelmény; További információt a [DNS-lekérdezések készítése állapotalapú szolgáltatáspartíción](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#preview-making-dns-queries-on-a-stateful-service-partition)című témakörben talál.

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

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>A PowerShell használatával egy szolgáltatás DNS-nevének beállítása
A Powershell paranccsal beállíthatja egy `New-ServiceFabricService` szolgáltatás DNS-nevét. A következő példa létrehoz egy új állapotnélküli szolgáltatást a DNS-névvel`service1.application1`

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

## <a name="preview-making-dns-queries-on-a-stateful-service-partition"></a>[Előzetes] DNS-lekérdezések készítése állapotalapú szolgáltatáspartíción
A Service Fabric 6.3-as verziójától kezdve a Service Fabric DNS-szolgáltatás támogatja a szolgáltatáspartíciók lekérdezéseit.

A DNS-lekérdezésekben használt partíciókra a következő elnevezési korlátozások vonatkoznak:

   - A partícióneveknek DNS-kompatibilisnek kell lenniük.
   - A többcímkés partícióneveket (amelyek pont, ".', a névben szerepelnek) nem használhatók.
   - A partícióneveknek kisbetűsnek kell lenniük.

A partíciót célzó DNS-lekérdezések formázása a következő:

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
Az elemek magyarázata:

- *A First-Label-Of-Partitioned-Service-DNSName* a szolgáltatás DNS-nevének első része.
- *A PartitionPrefix* olyan érték, amely a fürtjegyzék DnsService szakaszában vagy a fürt Erőforrás-kezelő sablonján keresztül állítható be. Az alapértelmezett érték a "--". További információ: [DNS-szolgáltatás beállításai](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Target-Partition-Name* a partíció neve. 
- *A PartitionSuffix* olyan érték, amely a fürtjegyzék DnsService szakaszában vagy a fürt Erőforrás-kezelő sablonján keresztül állítható be. Az alapértelmezett érték üres karakterlánc. További információ: [DNS-szolgáltatás beállításai](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *A fennmaradó particionált szolgáltatás-DNSName* a szolgáltatás DNS-nevének fennmaradó része.

Az alábbi példák a fürtön futó particionált `PartitionPrefix` szolgáltatások `PartitionSuffix`DNS-lekérdezéseit mutatják be, amelyek alapértelmezett beállításait a következőkre és : 

- A tartománybeli particionálási sémát használó DNS-névvel `backendrangedschemesvc.application` `backendrangedschemesvc-0.application`rendelkező szolgáltatás "0" partíciójának feloldásához használja a programot.
- A névvel ellátott particionálási `backendnamedschemesvc.application` sémát használó DNS-névvel rendelkező szolgáltatás "első" partíciójának feloldásához használja a programot. `backendnamedschemesvc-first.application`

A DNS-szolgáltatás a partíció elsődleges replikájának IP-címét adja vissza. Ha nincs megadva partíció, a szolgáltatás egy véletlenszerűen kiválasztott partíció elsődleges replikájának IP-címét adja vissza.

## <a name="using-dns-in-your-services"></a>A DNS használata a szolgáltatásokban
Ha egynél több szolgáltatást telepít, megtalálhatja más szolgáltatások végpontjait, amelyekkel DNS-név vel kommunikálhat. A DNS-szolgáltatás működik állapotmentes szolgáltatások, és a Service Fabric 6.3-as és újabb verziójú, állapotalapú szolgáltatások. A Service Fabric 6.3 előtti verzióin futó állapotalapú szolgáltatások esetén használhatja a beépített [fordított proxy szolgáltatást](./service-fabric-reverseproxy.md) http-hívásokhoz egy adott szolgáltatáspartíció hívásához. 

A DNS-szolgáltatás nem támogatja a dinamikus portokat. A fordított proxy szolgáltatás segítségével feloldhatja a dinamikus portokat használó szolgáltatásokat.

A következő kód bemutatja, hogyan hívegy állapotmentes szolgáltatást a DNS-en keresztül. Ez egyszerűen egy rendszeres http hívás, ahol megadja a DNS-nevet, a portot, és minden választható elérési utat az URL részeként.

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

A következő kód egy állapotalapú szolgáltatás adott partíciójára irányuló hívást jelenít meg. Ebben az esetben a DNS-név tartalmazza a partíció nevét (partition1). A hívás olyan fürtöt feltételez, `PartitionSuffix`amelynek alapértelmezett értékei a és a értékei. `PartitionPrefix`

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
* A Service Fabric 6.3-as és újabb verziói esetén probléma van a DNS-névben kötőjelet tartalmazó szolgáltatásnevek DNS-kereséseivel. A problémával kapcsolatos további információkért kövesse nyomon a következő [GitHub-problémát.](https://github.com/Azure/service-fabric-issues/issues/1197) A javítás erre jön a következő 6.3 frissítés. 

* A Service Fabric-szolgáltatások DNS-szolgáltatása linuxos operációs rendszer még nem támogatott. A DNS-szolgáltatás linuxos tárolók esetén támogatott. A Fabric Client/ServicePartitionResolver használatával történő manuális felbontás a rendelkezésre álló alternatíva.

## <a name="next-steps"></a>További lépések
További információ a fürtön belüli szolgáltatásokról a [szolgáltatásokkal való kapcsolatról és a szolgáltatásokkal való kommunikációról](service-fabric-connect-and-communicate-with-services.md)

