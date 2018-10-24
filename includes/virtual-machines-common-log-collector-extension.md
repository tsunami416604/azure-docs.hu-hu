
Egy Microsoft Azure felhőalapú szolgáltatással kapcsolatos problémák diagnosztizálásához szükséges virtuális gépeken a szolgáltatás-naplófájl összegyűjtése, a hibák bekövetkezésekor. A AzureLogCollector bővítmény igény szerinti segítségével hajtsa végre a naplók egyszeri gyűjtemény egy vagy több Felhőbeli szolgáltatás virtuális gépek (a webes és feldolgozói szerepkörök), és az összegyűjtött fájlok átvitele az Azure storage-fiók – bármely távoli bejelentkezés nélkül a virtuális gépek.

> [!NOTE]
> A legtöbb naplózott információ leírása található http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.asp.
> 
> 

Nincsenek függ a gyűjtendő fájlok típusú gyűjtemény két módot.

* **Az Azure Vendég ügynök naplók csak elérhetővé tétel (GA)**. A gyűjtemény üzemmódja Azure Vendég ügynök és az egyéb Azure-összetevőkkel kapcsolatos naplók mindegyikét tartalmazza.
* **Az összes napló (teljes)**. A gyűjtemény üzemmódja gyűjti össze a plusz a végleges verzió módban lévő összes fájl:
  
  * rendszer- és eseménynaplók
  * HTTP-hibanaplók
  * IIS-naplók
  * Telepítési naplók
  * más rendszernaplók

Mindkét fizetési mód gyűjteménymappához további adatok gyűjteménye, az alábbi struktúra használatával adható meg:

* **Név**: a gyűjteményhez, a neve, a zip-fájl az összegyűjtött fájlok az almappa neve.
* **Hely**: az a mappa elérési útját a virtuális gépen ahol gyűjtendő fájlok találhatók.
* **SearchPattern**: A minta a gyűjtendő fájlok nevét. Alapértelmezett érték a "\*"
* **A rekurzív**: Ha a gyűjtendő fájlok a megadott helyen található rekurzív módon.

## <a name="prerequisites"></a>Előfeltételek
* Storage-fiók bővítmény létrehozott zip-fájlokat menteni kell.
* Használja az Azure PowerShell-parancsmagok v0.8.0 vagy újabb. További információkért lásd: [Azure letöltések](https://azure.microsoft.com/downloads/).

## <a name="add-the-extension"></a>A bővítmény hozzáadása
Használhat [a Microsoft Azure PowerShell](https://msdn.microsoft.com/library/dn495240.aspx) parancsmagok vagy [Service Management REST API-k](https://msdn.microsoft.com/library/ee460799.aspx) a AzureLogCollector bővítmény hozzáadása.

A Cloud Services, a meglévő Azure Powershell-parancsmag **Set-AzureServiceExtension**, engedélyezi a bővítményt a Felhőszolgáltatás szerepkörpéldányain is használható. Minden alkalommal, amikor ez a bővítmény engedélyezve van ez a parancsmag segítségével, az Erőforrásnapló-gyűjtés akkor aktiválódik, a kiválasztott szerepkörök a kijelölt szerepkör példányai.

Virtuális gépek, a meglévő Azure Powershell-parancsmag **Set-AzureVMExtension**, engedélyezi a bővítményt a virtuális gépeken is használható. Minden alkalommal, amikor ez a bővítmény engedélyezve van a parancsmagokon keresztül, a naplógyűjtés az egyes példányok aktiválódik.

Ez a bővítmény belsőleg, JSON-alapú PublicConfiguration és PrivateConfiguration használja. Az alábbiakban látható egy minta JSON nyilvános és titkos konfigurációs elrendezését.

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
> Ez a bővítmény nem kell **privateConfiguration**. Csak adja meg egy üres struktúráját a **– PrivateConfiguration** argumentum.
> 
> 

A AzureLogCollector hozzá egy vagy több példány egy felhőalapú szolgáltatás vagy a virtuális gép, amely elindítja a gyűjtemények egyes virtuális gépek futtatásához és az összegyűjtött fájlok küldése a megadott Azure-fiókjába a kiválasztott szerepkörök, a két következő lépések egyikét követheti.

## <a name="adding-as-a-service-extension"></a>Egy bővítmény hozzáadása
1. Kövesse az utasításokat az Azure PowerShell-lel csatlakozzon az előfizetéséhez.
2. Adja meg, amelyhez hozzá szeretné adja hozzá, és engedélyezi a AzureLogCollector bővítményt szolgáltatáspéldány nevét, a tárolóhely, a szerepkörök és a szerepkör.

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

3. Adja meg a további adatok mappát, amelynek gyűjtendő fájlok (Ez a lépés nem kötelező).

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
   > Használhatja a jogkivonat `%roleroot%` , adja meg a szerepkör legfelső szintű meghajtót, mivel nem használ rögzített meghajtóra.
   > 
   > 
4. Adja meg az Azure storage-fiók neve és kulcsa, amelyhez gyűjtött fájl lesz feltöltve.

  ```powershell
  $StorageAccountName = 'YourStorageAccountName'
  $StorageAccountKey  = 'YourStorageAccountKey'
  ```

5. A SetAzureServiceLogCollector.ps1 (a cikk végén található része) a következő hívása a AzureLogCollector bővítmény engedélyezése egy Felhőszolgáltatás számára. A végrehajtás befejezése után annak a feltöltött fájl alapján `https://YourStorageAccountName.blob.core.windows.net/vmlogs`

  ```powershell
  .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances –Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList
  ```

A parancsfájlnak átadott paraméterek definícióját a következő: (Ez lesz átmásolva alatt is.)

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
* **Szerepkörök**: szerepköröket, például "WebRole1" vagy "WorkerRole1" listáját.
* **Példányok**:--vesszővel elválasztva szerepkörpéldányok neveinek listáját használja a helyettesítő karaktereket tartalmazó karakterláncot ("*") az összes szerepkörpéldány.
* **Tárolóhely**: tárolóhely neve. "Éles" vagy "Átmeneti".
* **Mód**: gyűjtemény üzemmódja. "Teljes" vagy "Általánosan elérhető".
* **StorageAccountName**: neve az Azure storage-fiók tárolására szolgáló összegyűjtött adatokat.
* **StorageAccountKey**: neve az Azure storage-fiók kulcsát.
* **AdditionalDataLocationList**: az alábbi struktúrával listáját:

  ```powershell
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="adding-as-a-vm-extension"></a>VM-bővítmény hozzáadása
Kövesse az utasításokat az Azure PowerShell-lel csatlakozzon az előfizetéséhez.

1. Adja meg a szolgáltatás nevét, virtuális gép és a gyűjtemény használati módját.

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
  
2. Adja meg az Azure storage-fiók neve és kulcsa, amelyhez gyűjtött fájl lesz feltöltve.

  ```powershell
  $StorageAccountName = 'YourStorageAccountName'
  $StorageAccountKey  = 'YourStorageAccountKey'
  ```

3. A SetAzureVMLogCollector.ps1 (a cikk végén található része) a következő hívása a AzureLogCollector bővítmény engedélyezése egy Felhőszolgáltatás számára. A végrehajtás befejezése után annak a feltöltött fájl alapján `https://YourStorageAccountName.blob.core.windows.net/vmlogs`

A parancsfájlnak átadott paraméterek definícióját a következő: (Ez lesz átmásolva alatt is.)

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
* **VMName**: a virtuális gép nevét.
* **Mód**: gyűjtemény üzemmódja. "Teljes" vagy "Általánosan elérhető".
* **StorageAccountName**: neve az Azure storage-fiók tárolására szolgáló összegyűjtött adatokat.
* **StorageAccountKey**: neve az Azure storage-fiók kulcsát.
* **AdditionalDataLocationList**: az alábbi struktúrával listáját:

  ```
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="extention-powershell-script-files"></a>Bővítmény PowerShell parancsfájlok
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

if ($Instances -ne $null -and $Instances.Count -gt 0)  #Instances should be seperated by ,
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
$context = New-AzureStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzureStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
  "Container ($ContainerName) doesn't exist. Creating it now.."
  New-AzureStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
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

#we just provide a empty privateConfig object
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
#This is an optional step: generate a sasUri to the container so it can be shared with other people if nened
#
$SasExpireTime = [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rl -Context $context
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
$context = New-AzureStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzureStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
    "Container ($ContainerName) doesn't exist. Creating it now.."
    New-AzureStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(90).ToString("o")
$SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
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
#we just provide a empty privateConfig object
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
                          $ReadSasUri = New-AzureStorageBlobSASToken -ExpiryTime $ExpiryTimeRead  -FullUri  -Blob  $blob.name -Container $blob.Container.Name -Permission r -Context $context

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

## <a name="next-steps"></a>További lépések
Most vizsgálja meg, vagy a naplók másolása egyik egyszerű helyről.

