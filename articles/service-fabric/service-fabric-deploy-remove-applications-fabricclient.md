---
title: Az Azure Service Fabric üzembe helyezése a FabricClient alkalmazással
description: A FabricClient API-k használatával alkalmazásokat telepíthet és távolíthat el a Service Fabric ben.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 25b874d1be8ab50d8076ff8fe9423c8cc0187512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75376970"
---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>Alkalmazások telepítése és eltávolítása a FabricClient használatával
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [Powershell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric parancssori felület](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient API-k](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

Egy [alkalmazástípus csomagolása][10]után készen áll az Azure Service Fabric-fürtbe való üzembe helyezésre. A telepítés a következő három lépést foglalja magában:

1. Töltse fel az alkalmazáscsomagot a képtárba
2. Az alkalmazástípus regisztrálása
3. Az alkalmazáscsomag eltávolítása a képtárból
4. Az alkalmazáspéldány létrehozása

Miután üzembe helyezett egy alkalmazást, és futtatott egy példányt a fürtben, törölheti az alkalmazáspéldányt és annak alkalmazástípusát. Az alábbi lépésekkel teljesen eltávolíthatja az alkalmazásokat a fürtből:

1. A futó alkalmazáspéldány eltávolítása (vagy törlése)
2. Az alkalmazástípus regisztrációjának megszüntetése, ha már nincs rá szüksége

Ha a Visual Studio segítségével telepíti és hibakeresés alkalmazások a helyi fejlesztési fürtön, az összes előző lépés automatikusan kezeli egy PowerShell-parancsfájlon keresztül.  Ez a parancsfájl az alkalmazásprojekt *Parancsfájlok* mappájában található. Ez a cikk háttérképet nyújt arról, hogy mit csinál a parancsfájl, így ugyanazokat a műveleteket a Visual Studio-n kívül is el tudja végezni. 
 
## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz
Csatlakozzon a fürthöz egy [FabricClient-példány](/dotnet/api/system.fabric.fabricclient) létrehozásával, mielőtt futtatja a cikkben szereplő kódpéldákat. Példák a helyi fejlesztési fürthöz vagy az Azure Active Directory, x509-tanúsítványok vagy Windows Active Directory használatával védett távoli fürthöz vagy fürthöz való csatlakozásra: [Csatlakozás biztonságos fürthöz](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis)című témakörben. A helyi fejlesztési fürthöz való csatlakozáshoz futtassa a következő példát:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Az alkalmazáscsomag feltöltése
Tegyük fel, hogy létrehoz és csomagol egy *MyApplication* nevű alkalmazást a Visual Studióban. Alapértelmezés szerint az ApplicationManifest.xml fájlban felsorolt alkalmazástípus neve "MyApplicationType".  A szükséges alkalmazásjegyzéket, szolgáltatásjegyzékeket és kód/konfigurációs/adatcsomagokat tartalmazó alkalmazáscsomag a *C:\Users\&&gt;lt;username \Documents\Visual Studio 2019\Projects\MyApplication\MyApplication\pkg\Debug mappában*található.

Az alkalmazáscsomag feltöltése olyan helyre helyezi, amely elérhető a belső Service Fabric-összetevők által elérhető. A Service Fabric ellenőrzi az alkalmazáscsomagot az alkalmazáscsomag regisztrálása során. Ha azonban helyileg (azaz feltöltés előtt) ellenőrizni szeretné az alkalmazáscsomagot, használja a [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) parancsmagját.

A [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API feltölti az alkalmazáscsomagot a fürtlemezkép-tárolóba. 

Ha az alkalmazáscsomag nagy és/vagy sok fájlt tartalmaz, [tömörítheti,](service-fabric-package-apps.md#compress-a-package) és átmásolhatja a powershell használatával a lemezképtárolóba. A tömörítés csökkenti a fájlok méretét és számát.

[A lemezkép-tároló kapcsolati karakterláncának ismertetése](service-fabric-image-store-connection-string.md) című témakörben talál további információt a lemezkép-tároló és a lemezképtároló kapcsolati karakterláncáról.

## <a name="register-the-application-package"></a>A kérelemcsomag regisztrálása
Az alkalmazásjegyzékben deklarált alkalmazástípus és verzió elérhetővé válik az alkalmazáscsomag regisztrálásakor. A rendszer beolvassa az előző lépésben feltöltött csomagot, ellenőrzi a csomagot, feldolgozza a csomag tartalmát, és átmásolja a feldolgozott csomagot egy belső rendszerhelyre.  

A [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API regisztrálja az alkalmazás típusát a fürtben, és elérhetővé teszi a központi telepítéshez.

A [GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) API az összes sikeresen regisztrált alkalmazástípusról nyújt tájékoztatást. Ezzel az API-val meghatározhatja, hogy mikor történik a regisztráció.

## <a name="remove-an-application-package-from-the-image-store"></a>Alkalmazáscsomag eltávolítása a lemezképtárolóból
Javasoljuk, hogy távolítsa el az alkalmazáscsomagot, miután az alkalmazás sikeresen regisztrált.  Az alkalmazáscsomagok lemezképtárolóból történő törlése rendszererőforrásokat szabadít fel.  A nem használt alkalmazáscsomagok megtartása lemezes tárolást eredményez, és az alkalmazás teljesítményével kapcsolatos problémákhoz vezet. Törölje az alkalmazáscsomagot a lemezképtárolóból az [RemoveApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage) API használatával.

## <a name="create-an-application-instance"></a>Alkalmazáspéldány létrehozása
A [CreateApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) API használatával bármely sikeresen regisztrált alkalmazástípusból létrehozhat egy alkalmazást. Az egyes alkalmazások nevének a *"fabric:"* sémával kell kezdődnie, és minden egyes alkalmazáspéldányhoz (fürtön belül) egyedinek kell lennie. Az alkalmazásjegyzékben a célalkalmazás típusának definiált alapértelmezett szolgáltatások is létrejönnek.

A regisztrált alkalmazástípus bármely verziójához több alkalmazáspéldány is létrehozható. Minden alkalmazáspéldány elszigetelten fut, saját munkakönyvtárral és folyamatok készletével.

A [GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) és a [GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync) API-k futtatásához futtassa a fürtben futó elnevezett alkalmazásokat és szolgáltatásokat.

## <a name="create-a-service-instance"></a>Szolgáltatáspéldány létrehozása
A [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API használatával egy szolgáltatástípusból hozhat létre egy szolgáltatást.  Ha a szolgáltatás alapértelmezett szolgáltatásként deklarálva van az alkalmazásjegyzékben, a szolgáltatás példányosodik az alkalmazás példányospéldányakor.  A [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API hívása egy már példányosított szolgáltatáshoz egy FabricException típusú kivételt ad vissza. A kivétel egy FabricErrorCode.ServiceAlreadyExists értékű hibakódot fog tartalmazni.

## <a name="remove-a-service-instance"></a>Szolgáltatáspéldány eltávolítása
Ha egy szolgáltatáspéldányra már nincs szükség, eltávolíthatja azt a futó alkalmazáspéldányból a [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) API meghívásával.  

> [!WARNING]
> Ez a művelet nem vonható vissza, és a szolgáltatás állapota nem állítható helyre.

## <a name="remove-an-application-instance"></a>Alkalmazáspéldány eltávolítása
Ha egy alkalmazáspéldányra már nincs szükség, a [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) API-val véglegesen eltávolíthatja azt név szerint. [A DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) automatikusan eltávolítja az alkalmazáshoz tartozó összes szolgáltatást is, véglegesen eltávolítva az összes szolgáltatásállapotot.

> [!WARNING]
> Ez a művelet nem vonható vissza, és az alkalmazás állapota nem állítható helyre.

## <a name="unregister-an-application-type"></a>Alkalmazástípus regisztrációjának megszüntetése
Ha egy alkalmazástípus egy adott verziójára már nincs szükség, törölje az alkalmazástípus adott verziójának regisztrációját a [Unregister-ServiceFabricApplicationType](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) API használatával. Az alkalmazástípusok nem használt verzióinak regisztrációjának megszüntetése felszabadítja a lemezképtár által használt tárhelyet. Az alkalmazástípus verziói mindaddig nem regisztrálhatók, amíg az alkalmazástípus adott verziójához egyetlen alkalmazás sem van példányosítva. Emellett az alkalmazástípusnak nem lehet függőben lévő alkalmazásfrissítése, amely az alkalmazástípus adott verziójára hivatkozik.

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage kér egy ImageStoreConnectionString
A Service Fabric SDK-környezetben már be kell állítani a megfelelő alapértelmezett beállításokat. De ha szükséges, az ImageStoreConnectionString az összes parancs meg kell egyeznie az értéket, amely a Service Fabric-fürt használ. Az ImageStoreConnectionString a fürtjegyzékben található, a [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) és a Get-ImageStoreConnectionStringFromClusterManifest parancsokkal beolvasva:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

A **Get-ImageStoreConnectionStringFromClusterManifest** parancsmag, amely a Service Fabric SDK PowerShell modul része, a rendszer a lemezképtároló kapcsolati karakterláncának leválasztására szolgál.  Az SDK-modul importálásához futtassa a következőt:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```


Az ImageStoreConnectionString a fürtjegyzékben található:

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

[A lemezkép-tároló kapcsolati karakterláncának ismertetése](service-fabric-image-store-connection-string.md) című témakörben talál további információt a lemezkép-tároló és a lemezképtároló kapcsolati karakterláncáról.

### <a name="deploy-large-application-package"></a>Nagy alkalmazáscsomag telepítése
Probléma: [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API idővel kiegy nagy alkalmazáscsomag (gb sorrendben).
Próbálja:
- Adjon meg egy nagyobb időmeghosszabbítást `timeout` a [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) metódushoz paraméterrel. Alapértelmezés szerint az időhosszabbítás 30 perc.
- Ellenőrizze a hálózati kapcsolatot a forrásgép és a fürt között. Ha a kapcsolat lassú, fontolja meg egy jobb hálózati kapcsolattal rendelkező gép használatát.
Ha az ügyfélgép a fürttől eltérő régióban található, fontolja meg egy ügyfélgép használatát a fürttel egy közelebbi vagy azonos régióban.
- Ellenőrizze, hogy külső fojtás. Ha például a lemezképtároló azure storage használatára van konfigurálva, a feltöltés szabályozható.

Probléma: A csomag feltöltése sikeresen befejeződött, de [a ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API-t idővel elévül. Próbálja:
- [A csomag tömörítése](service-fabric-package-apps.md#compress-a-package) a képtárolóba való másolás előtt.
A tömörítés csökkenti a fájlok méretét és számát, ami viszont csökkenti a szolgáltatásháló által végrehajtandó forgalom és munka mennyiségét. A feltöltési művelet lehet lassabb (különösen, ha tartalmazza a tömörítési idő), de regisztrálja és törölje az alkalmazás típusát gyorsabb.
- Adjon meg egy nagyobb időmeghosszabbítást `timeout` a [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API paraméterrel.

### <a name="deploy-application-package-with-many-files"></a>Alkalmazáscsomag telepítése sok fájllal
Probléma: [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) idővel ki egy alkalmazáscsomag sok fájlt (több ezer.
Próbálja:
- [A csomag tömörítése](service-fabric-package-apps.md#compress-a-package) a képtárolóba való másolás előtt. A tömörítés csökkenti a fájlok számát.
- Adjon meg egy nagyobb időmeghosszabbítást a [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) paraméterrel. `timeout`

## <a name="code-example"></a>Mintakód
A következő példa egy alkalmazáscsomagot másol a lemezképtárolóba, és az alkalmazás típusát tárolja. Ezután a példa létrehoz egy alkalmazáspéldányt, és létrehoz egy szolgáltatáspéldányt. Végül a példa eltávolítja az alkalmazáspéldányt, megszünteti az alkalmazás típusát, és törli az alkalmazáscsomagot a lemezképtárolóból.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Reflection;
using System.Threading.Tasks;

using System.Fabric;
using System.Fabric.Description;
using System.Threading;

namespace ServiceFabricAppLifecycle
{
class Program
{
static void Main(string[] args)
{

    string clusterConnection = "localhost:19000";
    string appName = "fabric:/MyApplication";
    string appType = "MyApplicationType";
    string appVersion = "1.0.0";
    string serviceName = "fabric:/MyApplication/Stateless1";
    string imageStoreConnectionString = "file:C:\\SfDevCluster\\Data\\ImageStoreShare";
    string packagePathInImageStore = "MyApplication";
    string packagePath = "C:\\Users\\username\\Documents\\Visual Studio 2019\\Projects\\MyApplication\\MyApplication\\pkg\\Debug";
    string serviceType = "Stateless1Type";

    // Connect to the cluster.
    FabricClient fabricClient = new FabricClient(clusterConnection);

    // Copy the application package to a location in the image store
    try
    {
        fabricClient.ApplicationManager.CopyApplicationPackage(imageStoreConnectionString, packagePath, packagePathInImageStore);
        Console.WriteLine("Application package copied to {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package copy to Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Provision the application.  "MyApplicationV1" is the folder in the image store where the application package is located. 
    // The application type with name "MyApplicationType" and version "1.0.0" (both are found in the application manifest) 
    // is now registered in the cluster.            
    try
    {
        fabricClient.ApplicationManager.ProvisionApplicationAsync(packagePathInImageStore).Wait();

        Console.WriteLine("Provisioned application type {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Provision Application Type failed:");

        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete the application package from a location in the image store.
    try
    {
        fabricClient.ApplicationManager.RemoveApplicationPackage(imageStoreConnectionString, packagePathInImageStore);
        Console.WriteLine("Application package removed from {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package removal from Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    //  Create the application instance.
    try
    {
        ApplicationDescription appDesc = new ApplicationDescription(new Uri(appName), appType, appVersion);
        fabricClient.ApplicationManager.CreateApplicationAsync(appDesc).Wait();
        Console.WriteLine("Created application instance of type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Create the stateless service description.  For stateful services, use a StatefulServiceDescription object.
    StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
    serviceDescription.ApplicationName = new Uri(appName);
    serviceDescription.InstanceCount = 1;
    serviceDescription.PartitionSchemeDescription = new SingletonPartitionSchemeDescription();
    serviceDescription.ServiceName = new Uri(serviceName);
    serviceDescription.ServiceTypeName = serviceType;

    // Create the service instance.  If the service is declared as a default service in the ApplicationManifest.xml,
    // the service instance is already running and this call will fail.
    try
    {
        fabricClient.ServiceManager.CreateServiceAsync(serviceDescription).Wait();
        Console.WriteLine("Created service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete a service instance.
    try
    {
        DeleteServiceDescription deleteServiceDescription = new DeleteServiceDescription(new Uri(serviceName));

        fabricClient.ServiceManager.DeleteServiceAsync(deleteServiceDescription);
        Console.WriteLine("Deleted service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete an application instance from the application type.
    try
    {
        DeleteApplicationDescription deleteApplicationDescription = new DeleteApplicationDescription(new Uri(appName));

        fabricClient.ApplicationManager.DeleteApplicationAsync(deleteApplicationDescription).Wait();
        Console.WriteLine("Deleted application instance {0}", appName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Un-provision the application type.
    try
    {
        fabricClient.ApplicationManager.UnprovisionApplicationAsync(appType, appVersion).Wait();
        Console.WriteLine("Un-provisioned application type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Un-provision application type failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    Console.WriteLine("Hit enter...");
    Console.Read();
}        
}
}

```

## <a name="next-steps"></a>További lépések
[A Service Fabric alkalmazásfrissítése](service-fabric-application-upgrade.md)

[A Service Fabric egészségügyi bevezetése](service-fabric-health-introduction.md)

[Service Fabric-szolgáltatás diagnosztizálása és hibaelhárítása](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Alkalmazás modellezése a Service Fabricben](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
