<properties 
pageTitle="在 Azure 雲端服務中執行啟動工作 | Microsoft Azure" 
description="啟動工作可協助您為應用程式備妥雲端服務環境。本文將說明啟動工作的運作方式，以及建立啟動工作的方法" 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="12/07/2015" 
ms.author="adegeo"/>




# 如何設定和執行雲端服務的啟動工作

您可以利用啟動工作，在角色啟動之前執行作業。 您可能想要執行的作業包括安裝元件、註冊 COM 元件、設定登錄機碼，或啟動長時間執行的處理序。
>[AZURE.NOTE] 啟動工作不適用於虛擬機器，只適用於雲端服務 Web 和背景工作角色。

## 啟動工作的運作方式

啟動工作是您的角色開始，並使用 [啟動] 項目內的 [工作] 項目 [ServiceDefinition.csdef] 檔案中定義之前採取的動作。 啟動工作經常是批次檔，但也可以是主控台應用程式，或是啟動 PowerShell 指令碼的批次檔。

環境變數可將資訊傳入啟動工作，而本機存放區可以用來傳遞來自啟動工作的資訊。 例如，環境變數可以指定您想要安裝的程式路徑，以及可以將哪些檔案寫入本機存放區，以便日後供您的角色讀取。

啟動工作可以將資訊和錯誤記錄到 **TEMP** 環境變數所指定的目錄中。 在啟動工作， **TEMP** 解析環境變數 
*C:\\Resources\\temp\\[guid]。 [角色名稱] \\RoleTemp* 雲端上執行時的目錄。

啟動工作也可以在重新開機之間執行數次。 例如，每次角色回收時，都會執行啟動工作，但每次角色回收不一定會重新開機。 啟動工作的撰寫方式，應該要可讓它們順利執行多次。

啟動工作必須以零值的 **errorlevel** (或結束代碼) 做為結尾，才算完成啟動處理序。 如果啟動工作的結尾不是零值的 **errorlevel**，角色將不會啟動。


## 角色啟動順序

以下列出 Azure 中的角色啟動程序：

1. 執行個體標示為**啟動中**，且沒有收到流量。

2. 所有啟動工作均會根據其 **taskType** 屬性來執行。
    - **simple** 的工作會以同步方式執行，一次一個。
    - **background** 和 **foreground** 工作則會以非同步方式，與啟動工作平行開始。
    > [AZURE.WARNING] 在啟動處理序中的啟動工作階段期間，IIS 可能不會完全設定好，因此特定的角色資料可能會無法使用。 啟動工作需要角色專屬的資料應該使用 [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx)。

3. 角色主機處理序已啟動，且已在 IIS 中建立網站。

4. [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) 方法呼叫。

5. 執行個體標示為**就緒**，且流量已路由傳送至執行個體。

6. [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) 方法呼叫。


## 啟動工作範例

啟動工作定義在 [ServiceDefinition.csdef] 檔案中，在 **工作** 項目。 **commandLine** 屬性會指定啟動批次檔案或主控台命令的名稱和參數，**executionContext** 屬性會指定啟動工作的權限等級，而 **taskType** 屬性則指定工作執行的方式。

在此範例中，針對啟動工作建立了環境變數 **MyVersionNumber**，並設定為值 "**1.0.0.0**"。

**ServiceDefinition.csdef**：

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

在以下範例中，**Startup.cmd** 批次檔將 "The current version is 1.0.0.0" 一行寫入了 StartupLog.txt 檔案，此檔案位於 TEMP 環境變數所指定的目錄中。  `EXIT /B 0` 行將確保啟動工作結尾 **errorlevel** 為零。

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [AZURE.NOTE] 在 Visual Studio 中， **複製到輸出目錄** 啟動批次檔的屬性應該設定為 **永遠複製** 要確保將啟動批次檔會部署至您在 Azure 上的專案 (**approot\\bin** Web 角色和 **approot** 背景工作角色)。

## 工作屬性說明

以下說明的屬性 **工作** [ServiceDefinition.csdef] 檔案中的項目:

**commandLine** - 指定啟動工作的命令列：

- 此命令使用選擇性命令列參數，可開始啟動工作。
- 這通常是 .cmd 或 .bat 批次檔的檔名。
- 工作是相對於部署 AppRoot\\Bin 資料夾。 在決定工作的路徑和檔案時，不會展開環境變數。 如果需要展開環境變數，可以建立小型 .cmd 指令碼，藉以呼叫啟動工作。
- 可以是主控台應用程式或批次檔啟動 [PowerShell 指令碼](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)。

**executionContext** - 指定啟動工作的權限等級。 權限等級可以是 limited (受到限制) 或 elevated (提高權限)：

- **限制**  
啟動工作執行之角色的相同權限。 當 **executionContext** 屬性 (attribute) 的 [執行階段] 項目也是 **有限**, ，則會使用使用者權限。

- **elevated**  
以系統管理員權限執行啟動工作。 這可讓啟動工作安裝程式、變更 IIS 組態、執行登錄變更，以及其他系統管理員層級的工作，且不會提高角色本身的權限等級。

> [AZURE.NOTE] 啟動工作的權限等級不需要與角色本身相同。

**taskType** - 指定啟動工作執行的方式。

- **簡單**  
工作會以同步方式執行，[ServiceDefinition.csdef] 檔案中指定一個，一次的順序。 當某個 **simple** 啟動工作以零值的 **errorlevel** 做為結尾時，就會執行下一個 **simple** 啟動工作。 如果沒有任何 **simple** 啟動工作需要執行，則會啟動角色本身。
    > [AZURE.NOTE] 如果 **simple** 的結尾是非零值的 **errorlevel**，執行個體會遭到封鎖。 後續的 **simple** 啟動工作和角色本身將不會啟動。

    若要確保批次檔結尾 **errorlevel** 為零，執行命令 `EXIT /B 0` 批次檔程序的結尾。

- **背景**  
以非同步方式執行工作以平行方式與角色的啟動。

- **前景**  
以非同步方式執行工作以平行方式與角色的啟動。 **foreground** 和 **background** 工作之間的主要差異，在於 **foreground** 工作可避免角色在工作結束之前遭到回收或關閉。 **background** 工作則不具備這項限制功能。

## 環境變數

環境變數是將資訊傳遞給啟動工作的方式。 例如，您可以放上這些項目的路徑：含有要安裝之程式的 Blob，或角色要使用的連接埠號碼，或是各項可控制啟動工作功能的設定。

有兩種類型的環境變數的啟動工作。靜態環境變數和環境變數，根據 [RoleEnvironment] 類別的成員。 於 [ServiceDefinition.csdef] 檔案中的 [環境] 區段中，在進行，兩者都使用 [變數] 的項目和 **名稱** 屬性。

靜態環境變數使用 **值** [變數] 項目的屬性。 上述範例會建立環境變數 **MyVersionNumber**，這具有靜態值 "**1.0.0.0**"。 另一個範例則是建立 **StagingOrProduction** 環境變數，您可以手動將值設為 "**staging**" 或 "**production**"，以根據 **StagingOrProduction** 環境變數的值執行不同的啟動動作。

RoleEnvironment 類別的成員為基礎的環境變數不使用 **值** [變數] 項目的屬性。 相反地，[RoleInstanceValue] 的子項目，以適當 **xPath** 屬性值，用來建立環境變數，根據 [RoleEnvironment] 類別的特定成員。 值 **xPath** 屬性來存取各種 [RoleEnvironment] 值位於 [在 Azure 中的 xPath 值](https://msdn.microsoft.com/library/azure/hh404006.aspx)。



例如，若要建立環境變數是 「**，則為 true**」 時在計算模擬器中，執行個體的和 「**false**」 定域機組中執行時，使用下列 [變數] 和 [RoleInstanceValue] 項目:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>

            

            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>

        </Environment>
    </Task>
</Startup>
```

## 後續步驟

了解如何執行一些 [一般啟動工作](cloud-services-startup-tasks-common.md) 與雲端服務。

[封裝](cloud-services-model-and-package.md) 雲端服務。



[servicedefinition.csdef]: cloud-services-model-and-package.md#csdef 
[task]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task 
[startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup 
[runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime 
[environment]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment 
[variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable 
[roleinstancevalue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue 
[roleenvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx 

