<properties 
pageTitle="在 Azure 雲端服務中執行啟動工作 | Microsoft Azure" 
description="啟動工作可協助您為應用程式備妥雲端服務環境。 本文將說明啟動工作的運作方式，以及建立啟動工作的方法" 
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

>[AZURE.NOTE] 不適用的虛擬機器，只以雲端服務 Web 和背景工作角色啟動工作。

## 啟動工作的運作方式

啟動工作會開始您的角色，且已定義在之前採取的動作 [ServiceDefinition.csdef] 檔案使用 [Task] 內的項目 [Startup] 項目。 啟動工作經常是批次檔，但也可以是主控台應用程式，或是啟動 PowerShell 指令碼的批次檔。

環境變數可將資訊傳入啟動工作，而本機存放區可以用來傳遞來自啟動工作的資訊。 例如，環境變數可以指定您想要安裝的程式路徑，以及可以將哪些檔案寫入本機存放區，以便日後供您的角色讀取。

啟動工作可以資訊和錯誤記錄到所指定的目錄 **TEMP** 環境變數。 在啟動工作， **TEMP** 解析環境變數 
*C:\\Resources\\temp\\[guid]。[角色名稱] \\RoleTemp* 雲端上執行時的目錄。

啟動工作也可以在重新開機之間執行數次。 例如，每次角色回收時，都會執行啟動工作，但每次角色回收不一定會重新開機。 啟動工作的撰寫方式，應該要可讓它們順利執行多次。

啟動工作的結尾必須 **errorlevel** (或結束代碼) 為零完成啟動程序。 如果啟動工作會結束有非零 **errorlevel**, ，角色將不會啟動。


## 角色啟動順序

以下列出 Azure 中的角色啟動程序：

1. 執行個體標示為 **起始** 並不會接收流量。

2. 所有啟動工作會都執行根據其 **taskType** 屬性。
    -  **簡單** 工作會以同步方式，執行一次。
    -  **背景** 和 **前景** 工作會開始以非同步的方式，平行啟動工作。  
       
    > [AZURE.WARNING] IIS 可能未完全設定期間啟動工作階段，在啟動過程中，所以可能無法使用角色特定的資料。 啟動工作需要角色專屬的資料應該使用 [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx)。

3. 角色主機處理序已啟動，且已在 IIS 中建立網站。

4.  [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) 方法呼叫。

5. 執行個體標示為 **準備** 流量會路由傳送至執行個體。

6.  [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) 方法呼叫。


## 啟動工作範例

啟動工作定義在 [ServiceDefinition.csdef] 中檔案 **工作** 項目。  **CommandLine** 屬性的名稱和啟動批次檔案或主控台命令，參數會指定 **executionContext** 屬性會指定啟動工作的權限等級和 **taskType** 屬性會指定工作執行的方式。

在此範例中，環境變數， **MyVersionNumber**, 、 啟動工作建立並設定為值"**1.0.0.0**」。

**ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

在下列範例中， **Startup.cmd** 批次檔將行寫入目前的版本 」 1.0.0.0 的 StartupLog.txt 檔案 TEMP 環境變數所指定的目錄中。  `EXIT /B 0` 行將確保啟動工作結尾 **errorlevel** 為零。

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [AZURE.NOTE] 在 Visual Studio 中， **複製到輸出目錄** 啟動批次檔的屬性應該設定為 **永遠複製** 要確保將啟動批次檔會部署至您在 Azure 上的專案 (**approot\\bin** Web 角色和 **approot** 背景工作角色)。

## 工作屬性說明

以下說明的屬性 **工作** 中的項目 [ServiceDefinition.csdef] 檔案:

**命令列** -指定啟動工作的命令列:

- 此命令使用選擇性命令列參數，可開始啟動工作。
- 這通常是 .cmd 或 .bat 批次檔的檔名。
- 工作是相對於部署 AppRoot\\Bin 資料夾。 在決定工作的路徑和檔案時，不會展開環境變數。 如果需要展開環境變數，可以建立小型 .cmd 指令碼，藉以呼叫啟動工作。
- 可以是主控台應用程式或批次檔啟動 [PowerShell 指令碼](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)。

**executionContext** -指定啟動工作的權限層級。 權限等級可以是 limited (受到限制) 或 elevated (提高權限)：

- **限制**  
啟動工作執行之角色的相同權限。 當 **executionContext** 屬性 [Runtime] 項目也是 **有限**, ，則會使用使用者權限。

- **提高權限**  
以系統管理員權限執行啟動工作。 這可讓啟動工作安裝程式、變更 IIS 組態、執行登錄變更，以及其他系統管理員層級的工作，且不會提高角色本身的權限等級。  

> [AZURE.NOTE] 啟動工作的權限層級不需要是指角色本身相同。

**taskType** -指定啟動工作的執行的方式。

- **簡單**  
工作會以同步方式，執行一次，在指定的順序在 [ServiceDefinition.csdef] 檔案。 當一個 **簡單** 啟動工作以結束 **errorlevel** 為零下, 一個 **簡單** 啟動工作的執行。 如果已無其他 **簡單** 啟動工作來執行，則會啟動角色本身。   

    > [AZURE.NOTE] 如果 **簡單** 工作以非零結束 **errorlevel**, ，執行個體將會遭到封鎖。 後續 **簡單** 啟動工作和角色本身，將不會啟動。

    若要確保批次檔結尾 **errorlevel** 為零，執行命令 `EXIT /B 0` 批次檔程序的結尾。

- **背景**  
以非同步方式執行工作以平行方式與角色的啟動。

- **前景**  
以非同步方式執行工作以平行方式與角色的啟動。 主要差別 **前景** 和 **背景** 工作在於 **前景** 工作可避免角色回收或正在關閉，直到工作已結束。  **背景** 工作沒有這項限制。

## 環境變數

環境變數是將資訊傳遞給啟動工作的方式。 例如，您可以放上這些項目的路徑：含有要安裝之程式的 Blob，或角色要使用的連接埠號碼，或是各項可控制啟動工作功能的設定。

有兩種類型的環境變數的啟動工作。靜態環境變數和環境變數以成員的 [RoleEnvironment] 類別。 兩者位於 [Environment] 區段 [ServiceDefinition.csdef] 檔，並同時使用 [Variable] 項目和 **名稱** 屬性。

靜態環境變數使用 **值** 屬性 [Variable] 項目。 上述範例中建立此環境變數 **MyVersionNumber** 其中的靜態值為"**1.0.0.0**」。 另一個範例就是建立 **StagingOrProduction** 環境變數; 您可以手動設定的值"**預備**「 或 」**生產**」 來執行不同的啟動動作的值為基礎 **StagingOrProduction** 環境變數。

RoleEnvironment 類別的成員為基礎的環境變數不使用 **值** 屬性 [Variable] 項目。 相反地， [RoleInstanceValue] 子項目，以適當 **xPath** 屬性值，用來建立根據特定成員的環境變數 [RoleEnvironment] 類別。 值 **xPath** 屬性來存取各種 [RoleEnvironment] 值位於 [在 Azure 中的 xPath 值](https://msdn.microsoft.com/library/azure/hh404006.aspx)。



例如，若要建立環境變數是 「**true**」 時在計算模擬器中，執行個體的和 」**false**」 定域機組中執行時，使用下列 [Variable] 和 [RoleInstanceValue] 項目:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
    
            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->
    
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


[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Task]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Environment]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
