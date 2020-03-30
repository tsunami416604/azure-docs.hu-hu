---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 09c4420647043fccc408631fec75854667923721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74085247"
---
A Microsoft Azure felhőszolgáltatással kapcsolatos problémák diagnosztizálásához a szolgáltatás naplófájljait a problémák bekövetkezése esetén össze kell gyűjteni a virtuális gépeken. Az AzureLogCollector bővítmény igény szerinti használatával egy vagy több felhőszolgáltatás-virtuális gépről (webes szerepkörökből és feldolgozói szerepkörökből) származó naplók egyszeri gyűjtését, valamint az összegyűjtött fájlokat egy Azure-tárfiókba viheti át – mindezt anélkül, hogy távolról bejelentkezne bármelyikbe a virtuális gépek.

> [!NOTE]
> A legtöbb naplózott információ leírása megtalálható ahttps://blogs.msdn.microsoft.com/kwill/2013/08/09/windows-azure-paas-compute-diagnostics-data/
> 
> 

A gyűjtendő fájlok típusától két féle gyűjtemény függ.

* **Csak az Azure vendégügynök naplói (GA)**. Ez a gyűjtési mód tartalmazza az Azure vendégügynökeivel és más Azure-összetevőkkel kapcsolatos összes naplót.
* **Minden naplók (Teljes)**. Ez a gyűjtési mód az összes fájlt GA módban, valamint:
  
  * rendszer- és alkalmazáseseménynaplók
  * HTTP-hibanaplók
  * IIS-naplók
  * Telepítési naplók
  * egyéb rendszernaplók

Mindkét gyűjtési módban további adatgyűjtési mappák adhatók meg a következő struktúra gyűjteményével:

* **Név**: A gyűjtemény neve, amelyet a zip fájlban lévő almappa neveként használnak az összegyűjtött fájlokkal.
* **Hely**: A virtuális gép azon mappájának elérési útja, ahol az összegyűjtendő fájlok találhatók.
* **SearchPattern**: Az összegyűjtendő fájlok nevének mintája. Az alapértelmezett\*érték " "
* **Rekurzív:** ha az összegyűjtendő fájlok rekurzív módon a megadott helyen találhatók.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](./updated-for-az.md)]

* A létrehozott zip-fájlok mentéséhez rendelkezik egy tárfiókkal a kiterjesztéshez.
* Azure PowerShell. A telepítési útmutatót az [Azure PowerShell telepítése](/powershell/azure/install-az-ps)] című témakörben találja.

## <a name="add-the-extension"></a>A bővítmény hozzáadása
[A Microsoft Azure PowerShell-parancsmagokkal](https://msdn.microsoft.com/library/dn495240.aspx) vagy a Service Management REST [API-k használatával](https://msdn.microsoft.com/library/ee460799.aspx) hozzáadhatja az AzureLogCollector bővítményt.

A Cloud Services esetében a meglévő Azure Powershell-parancsmag, a **Set-AzureServiceExtension**használható a bővítmény engedélyezéséhez a Cloud Service szerepkörpéldányokon. Minden alkalommal, amikor ez a bővítmény ezen a parancsmagon keresztül engedélyezve van, a naplógyűjtés a kijelölt szerepkörök kijelölt szerepkörpéldányain aktiválódik.

Virtuális gépek, a meglévő Azure Powershell-parancsmag, **Set-AzureVMExtension,** a bővítmény virtuális gépeken való engedélyezéséhez használható. Minden alkalommal, amikor ez a bővítmény engedélyezve van a parancsmagok, naplógyűjtés minden példányban aktiválódik.

Belsőleg ez a bővítmény a JSON-alapú PublicConfiguration és PrivateConfiguration. A következő egy minta JSON elrendezése nyilvános és privát konfigurációhoz.

### <a name="publicconfiguration"></a>Nyilvános konfiguráció

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

### <a name="privateconfiguration"></a>Privát konfiguráció

```json
{

}
```

> [!NOTE]
> Ennek a bővítménynek nincs szüksége **privateConfiguration**. Csak adjon meg egy üres struktúrát a **-PrivateConfiguration** argumentumhoz.
> 
> 

Az alábbi két lépés egyikét követve hozzáadhatja az AzureLogCollector-t egy felhőszolgáltatás vagy a kiválasztott szerepkörök virtuális gépének egy vagy több példányához, amely elindítja az egyes virtuális gépek gyűjteményeit az összegyűjtött fájlok futtatásához és az Azure-fiókba megadott elküldéséhez.

## <a name="adding-as-a-service-extension"></a>Hozzáadás szolgáltatásbővítményként
1. Kövesse az utasításokat az Azure PowerShell előfizetéshez való csatlakoztatásához.
2. Adja meg a szolgáltatás nevét, a tárolóhelyet, a szerepköröket és a szerepkörpéldányokat, amelyekhez hozzá szeretné adni, és engedélyezze az AzureLogCollector bővítményt.

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

3. Adja meg azt a további adatmappát, amelyhez a rendszer fájlokat gyűjt (ez a lépés nem kötelező).

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
   > A token `%roleroot%` segítségével megadhatja a szerepkör gyökérmeghajtóját, mivel nem használ rögzített meghajtót.
   > 
   > 
4. Adja meg az Azure storage-fiók nevét és kulcs, amelyhez az összegyűjtött fájlokat a rendszer feltölti.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

5. Hívja meg a SetAzureServiceLogCollector.ps1(a cikk végén található) az alábbiak szerint, hogy az AzureLogCollector bővítmény egy felhőszolgáltatás. Miután a végrehajtás befejeződött, megtalálhatja a feltöltött fájlt a`https://YourStorageAccountName.blob.core.windows.net/vmlogs`

   ```powershell
   .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances –Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList
   ```

A parancsfájlnak átadott paraméterek definíciója a következő. (Ez az alábbiakban is másolódik.)

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

* **Szolgáltatásnév**: A felhőszolgáltatás neve.
* **Szerepkörök**: Szerepkörök listája, például "WebRole1" vagy "WorkerRole1".
* **Példányok**: A vesszővel elválasztott szerepkörpéldányok nevének listája - használja a helyettesítő karakterláncot ("*") az összes szerepkörpéldányhoz.
* **Slot**: Slot neve. "Termelés" vagy "Átmeneti".
* **Mód**: Gyűjtési mód. "Teljes" vagy "GA".
* **StorageAccountName**: Az összegyűjtött adatok tárolására szolgáló Azure-tárfiók neve.
* **StorageAccountKey**: Az Azure storage-fiók kulcsának neve.
* **AdditionalDataLocationList**: A következő struktúra listája:

  ```powershell
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="adding-as-a-vm-extension"></a>Hozzáadás virtuálisgép-bővítményként
Kövesse az utasításokat az Azure PowerShell előfizetéshez való csatlakoztatásához.

1. Adja meg a szolgáltatás nevét, a virtuális gép és a gyűjtési mód.

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
  
2. Adja meg az Azure storage-fiók nevét és kulcs, amelyhez az összegyűjtött fájlokat a rendszer feltölti.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

3. Hívja meg a SetAzureVMLogCollector.ps1 (a cikk végén található) az alábbiak szerint, hogy az AzureLogCollector bővítmény egy felhőszolgáltatás. Miután a végrehajtás befejeződött, megtalálhatja a feltöltött fájlt a`https://YourStorageAccountName.blob.core.windows.net/vmlogs`

A parancsfájlnak átadott paraméterek definíciója a következő. (Ez az alábbiakban is másolódik.)

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

* **Szolgáltatásnév**: A felhőszolgáltatás neve.
* **VMName**: A virtuális gép neve.
* **Mód**: Gyűjtési mód. "Teljes" vagy "GA".
* **StorageAccountName**: Az összegyűjtött adatok tárolására szolgáló Azure-tárfiók neve.
* **StorageAccountKey**: Az Azure storage-fiók kulcsának neve.
* **AdditionalDataLocationList**: A következő struktúra listája:

  ```
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="extention-powershell-script-files"></a>Terjedelem: PowerShell-parancsfájlok
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
Most már megvizsgálja, vagy másolja a naplókat egy egyszerű helyről.

