---
title: "A Service Fabric több környezet kezelése |} Microsoft Docs"
description: "Service Fabric-alkalmazások futtatható fürtökben, amelyek több ezer gép egyik gépről mérete között. Bizonyos esetekben érdemes állítsa be az alkalmazását eltérően ezeket változatos környezetekben. Ez a cikk bemutatja, hogyan adhat környezet egy másik alkalmazás paramétereit."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: mikkelhegn
ms.openlocfilehash: 671cc9b0f7b7b37fcf5b052f7e34bc98e66b2838
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-application-parameters-for-multiple-environments"></a>Alkalmazás paramétereinek több környezet kezelése
Azure Service Fabric-fürtök hozhat létre egyet a sok ezer gépek bárhol használva. Amíg a bináris alkalmazásfájlokat módosítás nélkül futtathatja a széles skáláját környezetek között, gyakran konfigurálni szeretné az alkalmazás másképp, attól függően, hogy hány számítógépre történő telepítése esetén.

Egy egyszerű példa, fontolja meg a `InstanceCount` az állapotmentes szolgáltatások. Ha alkalmazások futnak az Azure-ban, általában szeretné a paraméter értéke a speciális érték-1". Ez a konfiguráció biztosítja, hogy fut-e a szolgáltatás minden csomóponton a fürtben (vagy minden csomópont, a csomópont típusban, ha egy elhelyezési korlátozás van beállítva). Azonban ez a konfiguráció nem alkalmas egyszámítógépes fürt figyel egy gépen azonos végponton több folyamat nem tartozik. Ehelyett általában beállított `InstanceCount` "1".

## <a name="specifying-environment-specific-parameters"></a>Környezetfüggő paramétereinek megadása
A megoldás a konfigurációs probléma a paraméteres alapértelmezett szolgáltatások és alkalmazásparaméter-fájlokat az adott értékei az adott környezetben. Az alkalmazás és szolgáltatás jegyzékfájlokban alapértelmezett szolgáltatások és alkalmazások paraméterek vannak konfigurálva. A séma meghatározása a ServiceManifest.xml és ApplicationManifest.xml fájlok telepítve van a Service Fabric SDK-val, és az eszközök *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

### <a name="default-services"></a>Alapértelmezett szolgáltatások
Service Fabric-alkalmazások szolgáltatáspéldány gyűjteménye épülnek fel. Is lehetséges, hogy hozzon létre egy üres alkalmazást, majd minden szolgáltatáspéldány dinamikusan, a legtöbb alkalmazás rendelkezik alapvető szolgáltatások mindig jöjjenek létre, amikor az alkalmazás létrejön. Ezek "alapértelmezett szolgáltatások" nevezzük. Az alkalmazás jegyzékében szögletes zárójelek között szerepel környezeti konfiguráció helyőrzőkkel vannak megadva:

```xml
  <DefaultServices>
      <Service Name="Stateful1">
          <StatefulService
              ServiceTypeName="Stateful1Type"
              TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]"
              MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

              <UniformInt64Partition
                  PartitionCount="[Stateful1_PartitionCount]"
                  LowKey="-9223372036854775808"
                  HighKey="9223372036854775807"
              />
        </StatefulService>
    </Service>
  </DefaultServices>
```

Az elnevezett paraméterek az alkalmazás jegyzékének paraméterek elemen belül kell megadni:

```xml
    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>
```

A DefaultValue attribútumot egy több-specifikus paraméter hiányában egy adott környezetben használandó értékét adja meg.

> [!NOTE]
> Nem minden szolgáltatás példány paraméterei megfelelő környezet konfigurációhoz. A fenti példában a szolgáltatás particionálási sémát LowKey és HighKey értékeit explicit módon határozzák meg a szolgáltatás minden példányának óta a partíciótartomány feladata az adatok tartományi, nem a környezetben.
> 
> 

### <a name="per-environment-service-configuration-settings"></a>Környezet szolgáltatás konfigurációs beállításai
A [Service Fabric-alkalmazás modell](service-fabric-application-model.md) lehetővé teszi, hogy a szolgáltatások futási időben olvasható egyéni kulcs-érték párokat tartalmazó konfigurációs csomagokat tartalmazza. Ezek a beállítások értékeit is is lehet szerint megkülönböztetett környezet megadásával egy `ConfigOverride` az alkalmazásjegyzékben.

Tegyük fel, hogy rendelkezik-e a következő beállítást Config\Settings.xml fájljában a `Stateful1` szolgáltatás:

```xml
  <Section Name="MyConfigSection">
     <Parameter Name="MaxQueueSize" Value="25" />
  </Section>
```
Bírálja felül ezt az értéket egy adott alkalmazás-környezet párhoz, hozzon létre egy `ConfigOverride` importálásakor a szolgáltatás jegyzékben található az alkalmazás jegyzékében.

```xml
  <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>
```
Ennek a paraméternek, majd konfigurálhatja környezet a fentiek szerint. Ehhez a Paraméterek szakaszban az alkalmazás jegyzékének deklaráló azt és környezetfüggő értékek megadása a alkalmazásparaméter-fájlokat.

> [!NOTE]
> Szolgáltatás konfigurációs beállításait, ha nincsenek három helyen, ahol a kulcsnak az értéke beállítható: a szolgáltatás konfigurációs csomagot, az alkalmazás jegyzékében és az alkalmazás paraméterfájl. A Service Fabric mindig választhat az paraméter fájl első (ha az meg van adva), majd az alkalmazás jegyzékében, és végül a konfigurációs csomagot.
> 
> 

### <a name="setting-and-using-environment-variables"></a>És környezeti változók használatához 
Adja meg, és a ServiceManifest.xml fájlt a környezeti változók értékét, és ezt felülbírálhatja a ApplicationManifest.xml fájlba / példány alapon.
Az alábbi példában látható, két környezeti változókat, egy értéket állítja be, és a másik felülbírálja. Környezeti változók értékeinek beállításához, hogy ezek legyenek érvényben van megadva a felülbírálásokhoz config ugyanúgy használhatja alkalmazás paramétereit.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
    <EnvironmentVariables>
      <EnvironmentVariable Name="MyEnvVariable" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentVariables>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
```
A környezeti változók a ApplicationManifest.xml felülbírálásához a kódcsomag a ServiceManifest a hivatkozik a `EnvironmentOverrides` elemet.

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="MyCode">
      <EnvironmentVariable Name="MyEnvVariable" Value="mydata"/>
    </EnvironmentOverrides>
  </ServiceManifestImport>
 ``` 
 A nevesített szolgáltatáspéldány létrehozását követően érheti el a környezeti változók kódból. például a C# tegye a következőket

```csharp
    string EnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

### <a name="service-fabric-environment-variables"></a>A Service Fabric környezeti változók
A Service Fabric környezeti változók beállítása minden szolgáltatáspéldány létrehozta. A teljes listát a környezeti változók nem éri el, ahol az félkövér azok, amelyekre szüksége lesz a szolgáltatás, a másik Service Fabric-futtatókörnyezet által használt. 

* Fabric_ApplicationHostId
* Fabric_ApplicationHostType
* Fabric_ApplicationId
* **Fabric_ApplicationName**
* Fabric_CodePackageInstanceId
* **Fabric_CodePackageName**
* **[YourServiceName] Fabric_Endpoint_ TypeEndpoint**
* **Fabric_Folder_App_Log**
* **Fabric_Folder_App_Temp**
* **Fabric_Folder_App_Work**
* **Fabric_Folder_Application**
* Fabric_NodeId
* **Fabric_NodeIPOrFQDN**
* **Fabric_NodeName**
* Fabric_RuntimeConnectionAddress
* Fabric_ServicePackageInstanceId
* Fabric_ServicePackageName
* Fabric_ServicePackageVersionInstance
* FabricPackageFileName

A kód belows ismerteti a Service Fabric környezeti változók felsorolása
 ```csharp
    foreach (DictionaryEntry de in Environment.GetEnvironmentVariables())
    {
        if (de.Key.ToString().StartsWith("Fabric"))
        {
            Console.WriteLine(" Environment variable {0} = {1}", de.Key, de.Value);
        }
    }
```
A következő példákban a környezeti változók nevű alkalmazás típusú `GuestExe.Application` egy szolgáltatás típusának neve `FrontEndService` futása közben a helyi fejlesztési számítógépén.

* **Fabric_ApplicationName = fabric:/GuestExe.Application**
* **Fabric_CodePackageName kód =**
* **Fabric_Endpoint_FrontEndServiceTypeEndpoint = 80**
* **Fabric_NodeIPOrFQDN = localhost**
* **Fabric_NodeName = _Node_2**

### <a name="application-parameter-files"></a>Alkalmazásparaméter-fájlokat
A Service Fabric-alkalmazás projekt tartalmazhat egy vagy több alkalmazásparaméter-fájlokat. Azok meghatározza, hogy az adott értékekre, az alkalmazás jegyzékében definiált paraméterek:

```xml
    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="3" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>
```
Új alkalmazás alapértelmezés szerint három alkalmazásparaméter-fájlokat, Local.1Node.xml, Local.5Node.xml és Cloud.xml nevű foglalja magában:

![A Solution Explorer alkalmazásparaméter-fájlokat][app-parameters-solution-explorer]

Paraméter-fájl létrehozásához egyszerűen másolja és illessze be egy meglévő és új nevet.

## <a name="identifying-environment-specific-parameters-during-deployment"></a>Azonosító környezetfüggő paraméterek központi telepítése során
A központi telepítéskor kell választani a megfelelő paraméterfájl alkalmazni az alkalmazással. Ehhez a Visual Studio Publish párbeszédpaneléről vagy a Powershellen keresztül.

### <a name="deploy-from-visual-studio"></a>Üzembe helyezés a Visual Studióból
A rendelkezésre álló paraméter fájlok listáját választhat az alkalmazást a Visual Studio közzétételekor.

![Válassza ki a paraméterfájl közzététele párbeszédpanelen][publishdialog]

### <a name="deploy-from-powershell"></a>A PowerShell telepítése
A `Deploy-FabricApplication.ps1` PowerShell-parancsfájlt tartalmazza az alkalmazás projektsablon fogad paraméterként közzétételi profilt, és a PublishProfile az alkalmazás paraméterfájl hivatkozást tartalmaz.

  ```PowerShell
    ./Deploy-FabricApplication -ApplicationPackagePath <app_package_path> -PublishProfileFile <publishprofile_path>
  ```

## <a name="next-steps"></a>Következő lépések
Ebben a témakörben tárgyalt alapfogalmakat némelyike kapcsolatos további tudnivalókért tekintse meg a [Service Fabric a műszaki áttekintés](service-fabric-technical-overview.md). Más elérhető a Visual Studio alkalmazás-felügyeleti képességekkel kapcsolatos információkért lásd: [kezelése a Service Fabric-alkalmazások, a Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
