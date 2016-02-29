<properties 
pageTitle="雲端服務的常見啟動工作 | Microsoft Azure" 
description="提供一些常見的啟動工作範例，做為您在雲端服務 Web 角色或背景工作角色中執行的參考。" 
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

# 常見的雲端服務啟動工作

本文提供一些常見的啟動工作範例，做為您在雲端服務中執行的參考。 您可以利用啟動工作，在角色啟動之前執行作業。 您可能想要執行的作業包括安裝元件、註冊 COM 元件、設定登錄機碼，或啟動長時間執行的處理序。 

請參閱 [這篇文章](cloud-services-startup-tasks.md) 瞭解啟動工作的運作方式，以及如何明確建立的項目，定義啟動工作。

此處的許多工作都使用了以下項目： 

>[AZURE.NOTE] 不適用的虛擬機器，只以雲端服務 Web 和背景工作角色啟動工作。


## 定義角色啟動前的環境變數

您可以定義整個角色的環境變數新增 [Runtime] 的服務定義檔中的角色定義的項目。

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Runtime>
            <Environment>
                <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
            </Environment>
        </Runtime>
    </WebRole>
</ServiceDefinition>
```

如果您需要針對特定的工作定義的環境變數，其他工作，不共用，您可以使用 [Environment] 內的項目 [Task] 項目。

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WebRole>
</ServiceDefinition>
```

變數也可以使用 [有效的 Azure XPath 值](https://msdn.microsoft.com/library/azure/hh404006.aspx) 參考部署相關的項目。 而不是使用 `value` 屬性，定義 [RoleInstanceValue] 子項目。

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## 使用 AppCmd.exe 設定 IIS 啟動

 [AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) 命令列工具可用來管理在 Azure 上啟動的 IIS 設定。 *AppCmd.exe* 提供方便、 命令列存取在 Azure 上的啟動工作中使用的組態設定。 使用 *AppCmd.exe*, ，網站設定可以新增、 修改或移除應用程式和網站。

不過，有幾點来注意的使用中 *AppCmd.exe* 做為啟動工作:

- 啟動工作可以在重新開機之間執行多次。 例如，角色回收時就可能會發生這種情形。
- 某些 *AppCmd.exe* 動作可能產生錯誤，如果您多次執行。 嘗試加入的區段 *Web.config* 兩次就會產生錯誤。
- 如果傳回非零的結束程式碼，啟動工作失敗或 **errorlevel**。 這可能因為發生 *AppCmd.exe* 會產生錯誤。

基於上列因素，最好經常檢查 **errorlevel** 之後呼叫 *AppCmd.exe*, ，這很簡單，如果該呼叫包裝 *AppCmd.exe* 與 *.cmd* 檔案。 若您偵測到已知 **errorlevel** 回應，您可以忽略它，否則會傳回它。 以下利用範例進一步說明。

所傳回的 errorlevel *AppCmd.exe* 會列在 winerror.h 檔案中，而且也會出現在 [MSDN](https://msdn.microsoft.com/library/windows/desktop/ms681382.aspx)。

### 範例

本範例的壓縮區段和壓縮項目新增至 json *Web.config* 錯誤處理和記錄檔。

相關章節 [ServiceDefinition.csdef] 檔案如下所示，其中包括設定 [executionContext](https://msdn.microsoft.com/library/azure/gg557552.aspx#Task) 屬性設定為 `elevated` 讓 *AppCmd.exe* 足夠的權限設定中的變更 *Web.config* 檔案:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Startup.cmd 批次檔新增至 JSON 的壓縮區段和壓縮項目會使用 AppCmd.exe *Web.config* 檔案。 預期 **errorlevel** 183 設為零的使用驗證。EXE 命令列程式。 非預期的 errorlevel 會記錄至 StartupErrorLog.txt。

    REM   *** Add a compression section to the Web.config file. ***
    %windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
    
    REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
    REM   batch file were executed twice. This can occur and must be accounted for in a Azure startup
    REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
    REM   command will safely set the ERRORLEVEL to zero.
    IF %ERRORLEVEL% EQU 183 DO VERIFY > NUL
    
    REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
    IF %ERRORLEVEL% NEQ 0 (
        ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
        GOTO ErrorExit
    )
    
    REM   *** Add compression for json. ***
    %windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
    IF %ERRORLEVEL% EQU 183 VERIFY > NUL
    IF %ERRORLEVEL% NEQ 0 (
        ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
        GOTO ErrorExit
    )
    
    REM   *** Exit batch file. ***
    EXIT /b 0
    
    REM   *** Log error and exit ***
    :ErrorExit
    REM   Report the date, time, and ERRORLEVEL of the error.
    DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
    TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
    EXIT %ERRORLEVEL%


## 新增防火牆規則

Azure 實際上擁有兩個防火牆。 第一道防火牆會控制虛擬機器與外界之間的連結。 這由控制 [EndPoints] 中的項目 [ServiceDefinition.csdef] 檔案。

第二道防火牆會控制虛擬機器與該虛擬機器中處理序之間的連結。 這是由 `netsh advfirewall firewall` 命令列工具所控制，而且也是本文的重點。

Azure 會針對在角色內啟動的處理序建立防火牆規則。 例如，在您啟動服務或程式時，Azure 會自動建立必要的防火牆規則，藉此允許該服務與網際網路通訊。 不過，如果您建立的服務是由角色外部的處理序啟動 (例如 COM+ 服務，或是使用 Windows 排程器啟動的程式)，您就必須手動建立防火牆規則以允許存取該服務。 您可以使用啟動工作建立這些防火牆規則。

啟動工作，會建立防火牆規則必須具有 [executionContext][Task] 的 **提高權限**。 將下列啟動工作來加入 [ServiceDefinition.csdef] 檔案。

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

若要加入防火牆規則，您必須在啟動批次檔中使用適當的 `netsh advfirewall firewall` 命令。 在此範例中，啟動工作的 TCP 連接埠 80 需要具備高安全性與加密功能。

    REM   Add a firewall rule in a startup task.
    
    REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
    netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1
    
    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%


## 封鎖特定的 IP 位址

您也可以修改您的 IIS 一組指定的 IP 位址限制 Azure web 角色存取 **web.config** 檔案，並建立命令檔可解除鎖定 **ip 安全性** 區段 **ApplicationHost.config** 檔案。

首先，建立您的角色啟動會解除鎖定時執行的命令檔 **ip 安全性** 區段 **ApplicationHost.config** 檔案。 建立新的資料夾，稱為 web 角色的根層級 **啟動** ，在此資料夾中，建立批次檔呼叫 **startup.cmd**。 設定這個檔案的內容 **永遠複製** 以確保它將會部署。

將下列啟動工作來加入 [ServiceDefinition.csdef] 檔案。

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

新增此命令可 **startup.cmd** 檔案:

    %windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity

這會導致 **startup.cmd** 每次初始化 web 角色，如此可確保所需執行批次檔 **ip 安全性** 區段會解除鎖定。

最後，修改 [system.webServer 區段](http://www.iis.net/configreference/system.webserver/security/ipsecurity#005) 您的 web 角色 **web.config** 檔案以新增一份 IP 位址被授與存取，如下列範例所示:

此範例組態 **允許** 存取以外定義的兩個伺服器的所有 Ip

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are granted access-->
    <ipSecurity>
        <!--The following IP addresses are denied access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

此範例組態 **拒絕** 存取除了定義兩個伺服器的所有 Ip。

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## 建立 PowerShell 啟動工作

Windows PowerShell 指令碼不能直接從呼叫 [ServiceDefinition.csdef] 檔案，但是它們可以叫用從啟動批次檔內。

根據預設，PowerShell 不會執行未簽署的指令碼。 除非簽署指令碼，否則需要將 Windows PowerShell 設為執行未簽署的指令碼。 若要執行未簽署的指令碼， **ExecutionPolicy** 必須設為 **不受限制**。  **ExecutionPolicy** 設定，您使用為基礎的 Windows PowerShell 版本。

    REM   Run an unsigned PowerShell script and log the output
    PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
        
    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%


如果您使用的客體 OS 是執行 PowerShell 2.0 或 1.0，可以強制執行第 2 版，如果無法這麼做，請改用第 1 版。

    REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
    PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

    REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
    IF %ERRORLEVEL% EQU -393216 (

       PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
       PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
    )

    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%

## 透過啟動工作在本機儲存體中建立檔案

您可以使用本機儲存體資源，儲存啟動工作所建立的檔案，以便日後供您的應用程式存取。

若要建立本機儲存體資源， [LocalResources] 區段 [ServiceDefinition.csdef] 檔案，然後加入 [LocalStorage] 子項目。 將本機儲存體資源命名為不重複的名稱，然後為啟動工作提供適當的大小。

若要在啟動工作中使用本機儲存體資源，您需要建立環境變數來參考本機儲存體資源的位置。 接著，啟動工作和應用程式就能對本機儲存體資源讀取及寫入檔案。

相關章節 **ServiceDefinition.csdef** 檔案如下所示:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        
        <LocalResources>
          <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
        </LocalResources>
        
        ...
        
        <Runtime>
            <Environment>
                <Variable name="PathToStartupStorage">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
                </Variable>
            </Environment>
        </Runtime>
        
        ...
        
        <Startup>
          <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

例如，這 **Startup.cmd** 批次檔使用 **PathToStartupStorage** 環境變數，以建立檔案 **MyTest.txt** 上的本機儲存體位置。

    REM   Create a simple text file.

    ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
    ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
    ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
    ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

    REM   Exit the batch file with ERRORLEVEL 0.

    EXIT /b 0

您也可以使用 Azure sdk 存取本機儲存體 [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) 方法。 標準檔案讀取和寫入作業會隨即讀取和寫入本機儲存體資源的內容。 

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTest.txt"));
```


## 區分在模擬器和雲端中執行的差異

當啟動工作在雲端中運作時，您可以讓啟動工作執行有別於在計算模擬器中運作時的步驟。 例如，在模擬器中執行時，您可能只想使用 SQL 資料的全新複本。 或者，您可能想針對雲端執行效能最佳化作業，而這是在模擬器中執行時不需要的作業。

這項功能在計算模擬器和雲端上執行不同動作可藉由建立環境變數中的 [ServiceDefinition.csdef] 檔案，則在啟動工作中測試的環境變數。

若要建立環境變數， [Variable]/[RoleInstanceValue] 項目，並建立的 XPath 值 `/RoleEnvironment/Deployment/@emulated`。 值 **%computeemulatorrunning%** 環境變數會 `"true"` 運算模擬器上執行時和 `"false"` 雲端上執行時。


```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">

        ...
        
        <Runtime>
            <Environment>
                <Variable name="ComputeEmulatorRunning">
                    <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
                </Variable>
            </Environment>
        </Runtime>

    </WebRole>
</ServiceDefinition>
```

現在可以使用任何工作執行 **%computeemulatorrunning%** 雲端或在模擬器中執行角色是否根據環境變數，以執行不同的動作。 以下是檢查該環境變數的 .cmd 命令介面指令碼。

    REM   Check if this task is running on the compute emulator.

    IF "%ComputeEmulatorRunning%" == "true" (
        REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
        
    ) ELSE (
        REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
        
    )


## 偵測工作是否已在執行

角色可能會未經重新開機便進行回收，導致您的啟動工作再執行一次。 標幟可指出工作是否已在裝載的 VM 上執行。 可能有些工作即便多次執行也沒關係。 不過，在某些情況下，您必須避免工作執行一次以上。

偵測工作已執行的最簡單方式是建立的檔案中 **%TEMP%** 資料夾的工作成功時，查看工作的開始它。 以下是可為您執行此作業的範例 cmd Shell 指令碼。

    REM   If Task1_Success.txt exists, then Application 1 is already installed.
    IF EXIST "%RoleRoot%\Task1_Success.txt" (
      ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
      GOTO Finish
    )

    REM   Run your real exe task
    ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

    IF %ERRORLEVEL% EQU 0 (
      REM   The application installed without error. Create a file to indicate that the task
      REM   does not need to be run again.

      ECHO This line will create a file to indicate that Application 1 installed correctly. > "%RoleRoot%\Task1_Success.txt"
      
    ) ELSE (
      REM   An error occurred. Log the error and exit with the error code.

      DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
      TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
      ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

      EXIT %ERRORLEVEL%
    )

    :Finish

    REM   Exit normally.
    EXIT /B 0


## 工作最佳作法
為 Web 或背景工作角色設定工作時，應該遵循的最佳作法如下。

### 務必記錄啟動活動

Visual Studio 並未提供可逐步執行批次檔的偵錯工具，因此最好盡可能取得越多批次檔作業上的資料。 記錄的批次檔或輸出兩者 **stdout** 和 **stderr**, ，可以提供重要資訊時嘗試偵錯並修正批次檔。 若要記錄都 **stdout** 和 **stderr** 至 StartupLog.txt 檔案的目錄中所指的 **%TEMP%** 環境變數，將文字加入 `>>  "%TEMP%\\StartupLog.txt" 2>&1` 至您想要記錄的特定一行的結尾。 例如，若要執行 setup.exe **%pathtoapp1install%** 目錄:

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

如果不在每一行結尾加上 `>> "%TEMP%\StartupLog.txt" 2>&1`，又想要記錄啟動工作的所有輸出，您需要使用兩個啟動批次檔。 第一個批次檔會藉由重新導向呼叫第二個批次檔案，以記錄第二個批次檔的所有活動。 重新導向必須順利執行，才能如實完成記錄。

以下示範如何從啟動批次檔重新導向所有的輸出。 在此範例中，ServerDefinition.csdef 檔案會建立啟動工作，以呼叫 Startup1.cmd。 Startup1.cmd 呼叫 Startup2.cmd，將所有輸出重新都導向至 %temp%\\startuplog.txt。

ServiceDefinition.cmd：

```xml
<Startup>
    <Task commandLine="Startup1.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

Startup1.cmd：

    REM   Startup1.cmd calls the main startup batch file, Startup2.cmd, redirecting all output
    REM   to the StartupLog.txt log file.

    REM   Log the startup date and time.
    ECHO Startup1.cmd: >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO Current date and time: >> "%TEMP%\StartupLog.txt" 2>&1
    DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
    TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO Starting up Startup2.cmd. >> "%TEMP%\StartupLog.txt" 2>&1

    REM   Call the Startup2.cmd batch file, redirecting all output to the StartupLog.txt log file.
    START /B /WAIT Startup2.cmd >> "%TEMP%\StartupLog.txt" 2>&1

    REM   Log the completion of Startup1.cmd.
    ECHO Returned to Startup1.cmd. >> "%TEMP%\StartupLog.txt" 2>&1

    IF ERRORLEVEL EQU 0 (
       REM   No errors occurred. Exit Startup1.cmd normally.
       EXIT /B 0
    ) ELSE (
       REM   Log the error.
       ECHO An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
       EXIT %ERRORLEVEL%
    )

Startup2.cmd：

    REM   This is the batch file where the startup steps should be performed. Because of the
    REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
    REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

    REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
    REM   calling batch file.
    EXIT /B %ERRORLEVEL%

### 正確設定啟動工作的 executionContext

正確設定啟動工作的權限。 有時候，即使角色是以正常的權限執行，啟動工作也必須以較高的權限執行。

 [ExecutionContext][Task] 屬性來設定啟動工作的權限等級。 使用 `executionContext="limited"` 表示啟動工作會有和角色相同的權限等級。 使用 `executionContext="elevated"` 表示啟動工作將具備系統管理員權限，您不用將系統管理員權限提供給您的角色，便可讓啟動工作執行系統管理員工作。

舉例來說，需要更高權的限的啟動工作可啟動工作，會使用 **AppCmd.exe** 將 IIS 設定。 **AppCmd.exe** 需要 `executionContext="elevated"`。

### 使用正確的 taskType

 [TaskType][Task] 屬性會決定將執行的方式啟動工作。 有三種值: **簡單**, ，**背景**, ，和 **前景**。 background 和 foreground 工作會以非同步方式啟動，而 simple 工作會以同步方式執行，一次一個。

使用 **簡單** 啟動工作，您可以設定工作將會發生的順序，由 ServiceDefinition.csdef 檔案中列出工作的順序。 如果 **簡單** 工作結束非零的結束代碼，然後啟動程序將會停止角色將不會啟動。

之間的差異 **背景** 啟動工作和 **前景** 啟動工作是 **前景** 工作仍將持續執行，直到角色 **前景** 工作結束。 這也表示如果 **前景** 工作停止回應或當機，才會回收角色直到 **前景** 工作強制關閉。 基於這個理由， **背景** 非同步啟動工作，除非您需要該功能的建議工作 **前景** 工作。

### 以 EXIT /B 0 結束批次檔

如果角色才會開始 **errorlevel** 從每個您簡單啟動工作是零。 並非所有的程式設定 **errorlevel** (結束代碼) 正確，因此批次檔結尾必須 `EXIT /B 0` 如果所有項目是否正確執行。

啟動批次檔的結尾遺漏 `EXIT /B 0`，是角色無法啟動的常見原因。

### 啟動工作預計會執行一次以上

並非所有角色回收都會重新開機，但所有角色回收都會執行全部的啟動工作。 換句話說，啟動工作必須能夠在重新開機之間，順利地執行多次。 就此討論 [上方](#detect-that-your-task-has-already-run)。

### 使用本機儲存體儲存必須在角色中存取的檔案

如果您想要在啟動工作期間複製或建立檔案，然後供您的角色存取，則該檔案必須放在本機儲存體中。 請參閱 [區段](#create-files-in-local-storage-from-a-startup-task) 上方。

## 後續步驟

檢閱定域機組 [服務模型和封裝](cloud-services-model-and-package.md)

深入了解如何 [工作](cloud-services-startup-tasks.md) 運作。

[建立及部署](cloud-services-how-to-create-deploy-portal.md) 雲端服務封裝。


[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Task]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Environment]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Endpoints]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LocalStorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[LocalResources]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue

