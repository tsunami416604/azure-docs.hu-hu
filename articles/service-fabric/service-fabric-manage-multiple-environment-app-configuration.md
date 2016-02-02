<properties
   pageTitle="管理 Service Fabric 中的多個環境 |Microsoft Azure"
   description="Service Fabric 應用程式可以在任意大小 (從一部電腦至數千部電腦) 的叢集上執行。在某些情況下，您會想要針對各種環境以不同的方式設定應用程式。本文說明如何定義每個環境的不同應用程式參數。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/25/2015"
   ms.author="seanmck"/>


# 管理多個環境的應用程式參數

使用任意數量的電腦 (從一至數千部) 可以構成 Service Fabric 叢集。 雖然不需針對各種環境進行修改，即可執行應用程式二進位檔，但您通常會視您要部署的機器數目，以不同的方式設定應用程式。

簡單的範例，請考慮 `InstanceCount` 無狀態服務。 在 Azure 中執行時，您通常會想將此參數設定為特殊值 -1，以確保您的服務能在叢集中的每個節點上執行。 不過，此組態不適用於一個方塊的叢集，因為不能有多個處理序同時在單一機器上的相同端點上接聽。 相反地，您通常會設定 `InstanceCount` 為 1。

## 指定環境特有的參數

解決方案是一組參數化預設服務和應用程式參數檔案，其中會填入指定之環境的參數值。

### 預設服務

Service Fabric 應用程式是由服務執行個體集合所組成。 雖然可以先建立一個空的應用程式，然後再動態建立所有的服務執行個體，但是大部分的應用程式都有一組應一律在應用程式具現化時建立的核心服務。 這些稱為「預設服務」並於應用程式資訊清單中指定，而多餘的括號中包含每個環境組態的預留位置：

    <DefaultServices>
        <Service Name="Stateful1">
            <StatefulService
                ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
    
                <UniformInt64Partition
                    PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808"
                    HighKey="9223372036854775807"
                />
        </StatefulService>
    </Service>

  </DefaultServices>

必須在應用程式資訊清單的 [參數] 元素中定義每個具名參數：

    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>

DefaultValue 屬性指定當指定的環境缺少更特定的參數時所要使用的值。
>[AZURE.NOTE] 並非所有的服務執行個體參數都適用於每個環境組態。 在上述範例中，已針對服務的所有執行個體明確定義服務資料分割配置的 LowKey 和 HighKey 值，因為資料分割範圍是資料網域 (而不是環境) 的函數。


### 每個環境的服務組態設定

[Service Fabric 應用程式模型](service-fabric-application-model.md) 可讓服務包含設定套件，其中包含可讀取在執行階段自訂的索引鍵-值組。 這些設定的值可以也來區別環境藉由指定 `ConfigOverride` 應用程式資訊清單中。

假設您有下列設定中的 Config\Settings.xml `Stateful1` 服務:


    <Section Name="MyConfigSection">
      <Parameter Name="MaxQueueSize" Value="25" />
    </Section>

若要覆寫此值為特定的應用程式環境配對，請建立 `ConfigOverride` 時匯入應用程式資訊清單中的服務資訊清單。

    <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>

  </ConfigOverrides>

接著可依照環境設定此參數，如上所示：在應用程式資訊清單的參數區段中加以宣告，並在應用程式參數檔案中指定環境特有的值。
>[AZURE.NOTE] 在服務組態設定的情況下，有三個地方可以設定索引鍵的值：服務組態封裝、應用程式資訊清單和應用程式參數檔案。 Service Fabric 將一律先從應用程式參數檔案 (若已指定) 進行選擇，然後從應用程式資訊清單選擇，最後再從組態封裝選擇。


### 應用程式參數檔案

Service Fabric 應用程式專案可以包含一或多個應用程式參數檔案，而每個參數檔案會針對應用程式資訊清單中定義的參數定義特定值：

    
    
    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="2" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>

根據預設，新的應用程式包含兩個參數檔案 (名為 Local.xml 和 Cloud.xml)：

![方案總管中的應用程式參數][app-parameters-solution-explorer]

若要建立新的參數檔案，只需複製並貼上現有的參數檔案並為它提供新名稱。

## 在部署期間識別環境特有的參數

在部署階段，您需選擇要套用於您的應用程式的適當參數檔案。 您可以透過 Visual Studio 或 PowerShell 中的 [發佈] 對話方塊進行。

### 從 Visual Studio 部署

您可以在 Visual Studio 發佈應用程式時，從可用的參數檔案清單進行選擇。

![在 ][publishdialog]

### 從 PowerShell 部署

`DeployCreate FabricApplication.ps1` PowerShell 指令碼接受為參數的參數檔案。

    ./DeployCreate-FabricApplication -ApplicationPackagePath <app_package_path> -ApplicationDefinitionFilePath <app_instance_definition_path>

## 後續步驟

若要深入了解一些本主題中討論的核心概念，請參閱 [Service Fabric 技術概觀](service-fabric-technical-overview.md)。 其他 Visual Studio 中的應用程式管理功能的相關資訊，請參閱 [管理 Service Fabric 應用程式在 Visual Studio](service-fabric-manage-application-in-visual-studio.md)。




[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png 
[app-parameters-solution-explorer]: ./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png 

