---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 09c4420647043fccc408631fec75854667923721
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74085247"
---
A Microsoft Azure Cloud Service-szolgáltatással kapcsolatos problémák diagnosztizálásához a szolgáltatás naplófájljait a virtuális gépeken kell összegyűjtenie, amikor a probléma felmerül. Igény szerint használhatja a AzureLogCollector-bővítményt a naplók egy vagy több virtuális gépről (a webes szerepkörökből és a feldolgozói szerepkörökből) történő egyszeri gyűjtéséhez, és átviheti az összegyűjtött fájlokat egy Azure Storage-fiókba – mindezt anélkül, hogy távolról be kellene jelentkeznie bármelyik virtuális gépre.

> [!NOTE]
> A naplózott információk többségének leírása a következő címen érhető el:https://blogs.msdn.microsoft.com/kwill/2013/08/09/windows-azure-paas-compute-diagnostics-data/
> 
> 

A gyűjtött fájlok típusaitól függően két mód van a gyűjtésre.

* Az **Azure Guest Agent csak naplókat (GA)** használ. Ez a gyűjteményi mód tartalmazza az Azure Guest Agent és más Azure-összetevőkhöz kapcsolódó összes naplót.
* **Minden napló (teljes)**. Ez a gyűjteményi mód a GA módban található összes fájlt és a következőket gyűjti:
  
  * a rendszerek és alkalmazások eseménynaplói
  * HTTP-hibák naplói
  * IIS-naplók
  * Telepítési naplók
  * egyéb rendszernaplók

A gyűjtési módokban további adatgyűjtési mappák is megadhatók a következő struktúra gyűjteményének használatával:

* **Name (név**): a gyűjtemény neve, amelyet a zip-fájlban található almappa neve használ az összegyűjtött fájlokkal.
* **Hely**: az a virtuális gépen lévő mappa elérési útja, ahol a gyűjteni kívánt fájlok találhatók.
* **SearchPattern**: a gyűjtött fájlok neveinek mintája. Az alapértelmezett érték: " \* "
* **Rekurzív**: Ha a gyűjteni kívánt fájlok rekurzívan találhatók a megadott helyen.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](./updated-for-az.md)]

* Rendelkeznie kell egy Storage-fiókkal a bővítmény számára a létrehozott zip-fájlok mentéséhez.
* Azure PowerShell. A telepítési utasításokért lásd: [install Azure PowerShell](/powershell/azure/install-az-ps)].

## <a name="add-the-extension"></a>A bővítmény hozzáadása
A AzureLogCollector bővítmény hozzáadásához használhatja [Microsoft Azure PowerShell](https://msdn.microsoft.com/library/dn495240.aspx) parancsmagokat vagy a [Service Management REST API-kat](https://msdn.microsoft.com/library/ee460799.aspx) .

Cloud Services esetében a **set-AzureServiceExtension**meglévő Azure PowerShell-parancsmag használatával engedélyezhető a bővítmény a Cloud Service szerepkör példányain. A bővítmény ezen a parancsmagon keresztül történő engedélyezésekor a rendszer a kiválasztott szerepkörök kiválasztott szerepkörű példányain indítja el a napló-gyűjteményt.

Virtual Machines esetében a **set-AzureVMExtension**meglévő Azure PowerShell-parancsmag használatával engedélyezheti a bővítményt a Virtual Machinesn. Minden alkalommal, amikor a bővítmény engedélyezve van a parancsmagokon keresztül, a rendszer minden egyes példányon elindítja a naplózási gyűjteményt.

Belsőleg ez a bővítmény a JSON-alapú PublicConfiguration és PrivateConfiguration használja. A következő egy példa JSON-minta a nyilvános és a privát konfigurációhoz.

### <a name="publicconfiguration"></a>PublicConfiguration

```json
{
    "Instances":  "*",
    "Mode":  "Full",
    "SasUri":  "SasUri to your storage account with sp=wl",
    "AdditionalData":
    [
      {
              "Name":  "StorageData",
              "Location":  "%roleroot%storage",
              "SearchPattern":  "*.*",
              "Recursive":  "true"
      },
      {
            "Name":  "CustomDataFolder2",
            "Location":  "c:\customFolder",
            "SearchPattern":  "*.log",
            "Recursive":  "false"
      },
    ]
}
```

### <a name="privateconfiguration"></a>PrivateConfiguration

```json
{

}
```

> [!NOTE]
> Ehhez a bővítményhez nem szükséges **privateConfiguration**. A **– PrivateConfiguration** argumentumhoz csak üres struktúrát adhat meg.
> 
> 

A következő két lépés egyikével adhatja hozzá a AzureLogCollector egy felhőalapú szolgáltatás vagy egy virtuális gép egy vagy több példányához, amely elindítja a gyűjteményeket az egyes virtuális gépeken, és elküldi az összegyűjtött fájlokat a megadott Azure-fiókba.

## <a name="adding-as-a-service-extension"></a>Hozzáadás szolgáltatás-bővítményként
1. Azure PowerShell az előfizetéséhez való kapcsolódáshoz kövesse az utasításokat.
2. Adja meg a szolgáltatás nevét, a tárolóhelyet, a szerepköröket és a szerepkör-példányokat, amelyekhez hozzá kívánja adni a AzureLogCollector bővítményt, és engedélyezze azt.

   ```powershell
   #Specify your cloud service name
   $ServiceName = 'extensiontest2'

   #Specify the slot. 'Production' or 'Staging'
   $slot = 'Production'

   #Specified the roles on which the extension will be installed and enabled
   $roles = @("WorkerRole1","WebRole1")

   #Specify the instances on which extension will be installed and enabled.  Use wildcard * for all instances
   $instances = @("*")

   #Specify the collection mode, "Full" or "GA"
   $mode = "GA"
   ```

3. Itt adhatja meg azt a további adatmappát, amelybe a rendszer összegyűjti a fájlokat (ez a lépés nem kötelező).

   ```powershell
   #add one location
   $a1 = New-Object PSObject

   $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
   $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
   $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
   $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"

   $AdditionalDataList+= $a1
   #more locations can be added....
   ```

   > [!NOTE]
   > A token használatával `%roleroot%` megadhatja a szerepkör gyökerének meghajtóját, mivel nem rögzített meghajtót használ.
   > 
   > 
4. Adja meg az Azure Storage-fiók nevét és kulcsát, ahová az összegyűjtött fájlok fel lesznek töltve.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

5. Hívja meg a SetAzureServiceLogCollector.ps1 (a cikk végén található) az alábbiak szerint, hogy engedélyezze a AzureLogCollector bővítményt a felhőalapú szolgáltatásokhoz. A végrehajtás befejeződése után megkeresheti a feltöltött fájlt a`https://YourStorageAccountName.blob.core.windows.net/vmlogs`

   ```powershell
   .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances –Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList
   ```

A következő a parancsfájlnak átadott paraméterek definíciója. (Ezt az alábbi táblázat is másolja.)

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string[]] $Roles ,

  [Parameter(Mandatory=$false)]
  [string[]] $Instances,

  [Parameter(Mandatory=$false)]
  [string]   $Slot = 'Production',

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)
```

* **Szolgáltatásnév**: a felhőalapú szolgáltatás neve.
* **Szerepkörök**: szerepkörök listája, például "webrole1 webes" vagy "WorkerRole1".
* **Példányok**: a szerepkör-példányok neve, vesszővel elválasztva – használja a helyettesítő karakterláncot ("*") az összes szerepkör-példányhoz.
* **Tárolóhely**: tárolóhely neve. "Éles" vagy "előkészítés".
* **Mode**: gyűjtemény mód. "Teljes" vagy "GA".
* **StorageAccountName**: az összegyűjtött adatok tárolására szolgáló Azure Storage-fiók neve.
* **StorageAccountKey**: az Azure Storage-fiók kulcsának neve.
* **AdditionalDataLocationList**: a következő struktúra listája:

  ```powershell
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="adding-as-a-vm-extension"></a>Hozzáadás virtuálisgép-bővítményként
Azure PowerShell az előfizetéséhez való kapcsolódáshoz kövesse az utasításokat.

1. Adja meg a szolgáltatás nevét, a virtuális gépet és a gyűjtemény módját.

   ```powershell
   #Specify your cloud service name
   $ServiceName = 'YourCloudServiceName'

   #Specify the VM name
   $VMName = "'YourVMName'"

   #Specify the collection mode, "Full" or "GA"
   $mode = "GA"

   Specify the additional data folder for which files will be collected (this step is optional).

   #add one location
   $a1 = New-Object PSObject

   $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
   $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
   $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
   $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"

   $AdditionalDataList+= $a1
        #more locations can be added....
   ```
  
2. Adja meg az Azure Storage-fiók nevét és kulcsát, ahová az összegyűjtött fájlok fel lesznek töltve.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

3. Hívja meg a SetAzureVMLogCollector.ps1 (a cikk végén található) az alábbiak szerint, hogy engedélyezze a AzureLogCollector bővítményt a felhőalapú szolgáltatásokhoz. A végrehajtás befejeződése után megkeresheti a feltöltött fájlt a`https://YourStorageAccountName.blob.core.windows.net/vmlogs`

A következő a parancsfájlnak átadott paraméterek definíciója. (Ezt az alábbi táblázat is másolja.)

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string] $VMName ,

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)
```

* **Szolgáltatásnév**: a felhőalapú szolgáltatás neve.
* **VMName**: a virtuális gép neve.
* **Mode**: gyűjtemény mód. "Teljes" vagy "GA".
* **StorageAccountName**: az összegyűjtött adatok tárolására szolgáló Azure Storage-fiók neve.
* **StorageAccountKey**: az Azure Storage-fiók kulcsának neve.
* **AdditionalDataLocationList**: a következő struktúra listája:

  ```
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="extention-powershell-script-files"></a>Kiterjesztési PowerShell-parancsfájlok fájljai
### <a name="setazureservicelogcollectorps1"></a>SetAzureServiceLogCollector.ps1

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string[]] $Roles ,

  [Parameter(Mandatory=$false)]
  [string[]] $Instances = '*',

  [Parameter(Mandatory=$false)]
  [string]   $Slot = 'Production',

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)

$publicConfig = New-Object PSObject

if ($Instances -ne $null -and $Instances.Count -gt 0)  #Instances should be separated by ,
{
  $instanceText = $Instances[0]
  for ($i = 1;$i -lt $Instances.Count;$i++)
  {
    $instanceText = $instanceText+ "," + $Instances[$i]
  }
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value $instanceText
}
else  #For all instances if not specified.  The value should be a space or *
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value " "
}

if ($Mode -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
}
else
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
}

#
#we need to get the Sasuri from StorageAccount and containers
#
$context = New-AzStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
  "Container ($ContainerName) doesn't exist. Creating it now.."
  New-AzStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
$publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

#
#Add AdditionalData to collect data from additional folders
#
if ($AdditionDataLocationList -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
}

#
# Convert it to JSON format
#
$publicConfigJSON = $publicConfig | ConvertTo-Json
"publicConfig is:  $publicConfigJSON"

#we just provide an empty privateConfig object
$privateconfig = "{
}"

if ($Roles -ne $null)
{
  Set-AzureServiceExtension -Service $ServiceName -Slot $Slot -Role $Roles -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
}
else
{
  Set-AzureServiceExtension -Service $ServiceName -Slot $Slot  -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
}

#
#This is an optional step: generate a sasUri to the container so it can be shared with other people if needed.
#
$SasExpireTime = [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rl -Context $context
$SasUri = $SasUri + "&restype=container&comp=list"
Write-Output "The container for uploaded file can be accessed using this link:`r`n$sasuri"
```

### <a name="setazurevmlogcollectorps1"></a>SetAzureVMLogCollector.ps1

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
              [Parameter(Mandatory=$true)]
              [string]   $ServiceName,

              [Parameter(Mandatory=$false)]
              [string] $VMName ,

              [Parameter(Mandatory=$false)]
              [string]   $Mode = 'Full',

              [Parameter(Mandatory=$false)]
              [string]   $StorageAccountName,

              [Parameter(Mandatory=$false)]
              [string]   $StorageAccountKey,

              [Parameter(Mandatory=$false)]
              [PSObject[]] $AdditionDataLocationList = $null
        )

$publicConfig = New-Object PSObject
$publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value "*"

if ($Mode -ne $null )
{
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
}
else
{
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
}

#
#we need to get the Sasuri from StorageAccount and containers
#
$context = New-AzStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
    "Container ($ContainerName) doesn't exist. Creating it now.."
    New-AzStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(90).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
$publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

#
#Add AdditionalData to collect data from additional folders
#
if ($AdditionDataLocationList -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
}

#
# Convert it to JSON format
#
$publicConfigJSON = $publicConfig | ConvertTo-Json

Write-Output "PublicConfiguration is: \r\n$publicConfigJSON"

#
#we just provide an empty privateConfig object
#
$privateconfig = "{
}"

if ($VMName -ne $null )
{
      $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
      $VM.VM.OSVirtualHardDisk.OS

      if ($VM.VM.OSVirtualHardDisk.OS -like '*Windows*')
      {
            Set-AzureVMExtension -VM $VM -ExtensionName "AzureLogCollector" -Publisher Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.* | Update-AzureVM -Verbose

            #
            #We will check the VM status to find if operation by extension has been completed or not. The completion of the operation,either succeed or fail, can be indicated by
            #the presence of SubstatusList field.
            #
            $Completed = $false
            while ($Completed -ne $true)
            {
                    $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
                    $status = $VM.ResourceExtensionStatusList | Where-Object {$_.HandlerName -eq "Microsoft.WindowsAzure.Compute.AzureLogCollector"}

                    if ( ($status.Code -ne 0) -and ($status.Status -like '*error*'))
                    {
                        Write-Output "Error status is returned: $($Status.ExtensionSettingStatus.FormattedMessage.Message)."
                          $Completed = $true
                    }
                    elseif (($status.ExtensionSettingStatus.SubstatusList -eq $null -or $status.ExtensionSettingStatus.SubstatusList.Count -lt 1))
                    {
                          $Completed = $false
                          Write-Output "Waiting for operation to complete..."
                    }
                    else
                    {
                          $Completed = $true
                          Write-Output "Operation completed."

                    $UploadedFileUri = $Status.ExtensionSettingStatus.SubStatusList[0].FormattedMessage.Message
                          $blob = New-Object Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob($UploadedFileUri)

                  #
                        # This is an optional step:  For easier access to the file, we can generate a read-only SasUri directly to the file
                          #
                          $ExpiryTimeRead =  [DateTime]::Now.AddMinutes(120).ToString("o")
                          $ReadSasUri = New-AzStorageBlobSASToken -ExpiryTime $ExpiryTimeRead  -FullUri  -Blob  $blob.name -Container $blob.Container.Name -Permission r -Context $context

                        Write-Output "The uploaded file can be accessed using this link: $ReadSasUri"

                          #
                          #This is an optional step:  Remove the extension after we are done
                          #
                          Get-AzureVM -ServiceName $ServiceName -Name $VMName | Set-AzureVMExtension -Publisher Microsoft.WindowsAzure.Compute -ExtensionName "AzureLogCollector" -Version 1.* -Uninstall | Update-AzureVM -Verbose

                    }
                    Start-Sleep -s 5
            }
      }
      else
      {
          Write-Output "VM OS Type is not Windows, the extension cannot be enabled"
      }

}
else
{
  Write-Output "VM name is not specified, the extension cannot be enabled"
}
```

## <a name="next-steps"></a>Következő lépések
Most már megvizsgálhatja vagy átmásolhatja a naplókat egy egyszerű helyről.

