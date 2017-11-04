
A Microsoft Azure-felhőszolgáltatásban problémák diagnosztizálása szükséges virtuális gépeken a szolgáltatás-naplófájl összegyűjtése, a hibák bekövetkezésekor. A AzureLogCollector bővítmény igény segítségével naplók egyszeri gyűjteménye hajtson végre egy vagy több felhőalapú szolgáltatás virtuális gépek (a egyaránt webes és feldolgozói szerepkörök), és az összegyűjtött fájlok átvitele az Azure storage-fiók – bármely távoli bejelentkezés nélkül a virtuális gépeket.

> [!NOTE]
> A naplózott információk többsége leírásainak http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.asp helyen találhatók meg.
> 
> 

Gyűjtemény két módja van a gyűjtendő fájlok típusait függ.

* Az Azure Vendég ügynök naplók csak (GA). A gyűjtemény üzemmódja Azure vendégügynökök és az egyéb Azure összetevők kapcsolódó összes naplófájlt tartalmazza.
* Összes napló (teljes). A gyűjtemény üzemmódja összegyűjti az összes fájl plusz GA módban:
  
  * rendszer- és eseménynaplók
  * A HTTP hibanaplókat.
  * IIS-napló
  * Telepítési naplók
  * egyéb rendszer naplóit

Mindkét gyűjtemény módban gyűjteménymappához további adatokat az alábbi szerkezettel gyűjteménye használatával adhatók meg:

* **Név**: a gyűjteményt, amely gyűjtendő almappa belül a zip-fájl neve lesz a neve.
* **Hely**: a mappa a virtuális gépen, hova legyenek összegyűjtve fájl elérési útját.
* **SearchPattern**: A minta a gyűjtendő fájlok nevét. Alapértelmezett érték a "*"
* **Rekurzív**: Ha a fájlok lesznek a mappa alatt gyűjtött rekurzív módon.

## <a name="prerequisites"></a>Előfeltételek
* Szükség van egy tárfiókja bővítmény létrehozott zip-fájlok mentéséhez.
* Meg kell győződnie arról, hogy azok be Azure PowerShell-parancsmagok V0.8.0 vagy újabb. További információkért lásd: [Azure letölti](https://azure.microsoft.com/downloads/).

## <a name="add-the-extension"></a>Adja hozzá a kiterjesztést
Használhat [Microsoft Azure PowerShell](https://msdn.microsoft.com/library/dn495240.aspx) parancsmagok vagy [Service Management REST API-k](https://msdn.microsoft.com/library/ee460799.aspx) AzureLogCollector kiterjesztéssel.

A Felhőszolgáltatások, a meglévő Azure Powershell-parancsmag **Set-AzureServiceExtension**, engedélyezi a bővítményt a felhőalapú szolgáltatás szerepkörpéldányokat használható. Minden alkalommal, amikor a bővítmény engedélyezve van ez a parancsmag használatával, naplógyűjtést akkor váltódik ki, a kiválasztott szerepkörök a kijelölt szerepkör példányain.

A virtuális gépek, a meglévő Azure Powershell-parancsmag **Set-AzureVMExtension**, engedélyezi a bővítményt a virtuális gépeken is használható. Minden alkalommal, amikor a bővítmény engedélyezve van a parancsmagokon keresztül, az egyes példányok naplógyűjtést váltja ki.

A bővítmény belső JSON-alapú PublicConfiguration és PrivateConfiguration használja. Azzal, hogy a nyilvános és titkos konfigurációs JSON minta a következő:

### <a name="publicconfiguration"></a>PublicConfiguration
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

### <a name="privateconfiguration"></a>PrivateConfiguration
    {

    }

> [!NOTE]
> Ehhez a bővítményhez nem szükséges **privateConfiguration**. Csak adja meg egy üres szerkezet a **– PrivateConfiguration** argumentum.
> 
> 

A két következő lépések végrehajtásával adja hozzá a AzureLogCollector egy vagy több példány egy felhőalapú szolgáltatás, vagy a virtuális gépet, mely az egyes virtuális gépek futtatásához és az összegyűjtött fájlok küldése a megadott Azure-fiók gyűjteményeit elindítja a kiválasztott szerepkörök követheti.

## <a name="adding-as-a-service-extension"></a>Egy bővítmény hozzáadása
1. Az utasítások az Azure PowerShell csatlakozás az előfizetéshez.
2. Adja meg a szolgáltatás nevét, a tárhely, a szerepkörök és a szerepkör-példányokat kívánt hozzáadásához és a AzureLogCollector bővítmény engedélyezéséhez.
   
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
3. Adja meg a fájlok gyűjtenek további adatok mappát (Ez a lépés nem kötelező).
   
        #add one location
        $a1 = New-Object PSObject
   
        $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
        $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
        $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
        $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"
   
        $AdditionalDataList+= $a1
              #more locations can be added....
   
   > [!NOTE]
   > Használhatja a token `%roleroot%` adhatja meg a szerepkör gyökérmeghajtóján, mivel nem használ rögzített meghajtóra.
   > 
   > 
4. Adja meg az Azure storage-fiók neve és kulcsa, amelyhez összegyűjtött fájlok lesz feltöltve.
   
        $StorageAccountName = 'YourStorageAccountName'
        $StorageAccountKey  = ‘YouStorageAccountKey'
5. A SetAzureServiceLogCollector.ps1 (a cikk végén található) az alábbiak szerint hívása a AzureLogCollector bővítmény engedélyezése egy felhőalapú szolgáltatás. Ha befejeződött a végrehajtás, a feltöltött fájl alatt található`https://YouareStorageAccountName.blob.core.windows.net/vmlogs`
   
        .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances –Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList

Az a parancsfájlnak átadott paraméterek definícióját a következő: (Ez másolódik alatt is.)

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

* *Szolgáltatásnév*: A felhőszolgáltatás neve.
* *Szerepkörök*: a szerepköröket, például "WebRole1" vagy "WorkerRole1" listáját.
* *Példányok*:--vesszővel elválasztott szerepkörpéldányok neveinek listáját használja a helyettesítő karakterlánc ("*") tartozó összes szerepkörpéldányt.
* *Tárolóhely*: tárhely neve. "Éles" vagy "Tesztelés".
* *Mód*: gyűjtemény módja. "Teljes" vagy "GA".
* *StorageAccountName*: neve az Azure storage-fiók tárolására összegyűjtött adatokat.
* *StorageAccountKey*: neve az Azure tárfiók kulcsa.
* *AdditionalDataLocationList*: listáját az alábbi szerkezettel:
  
      {
      String Name,
      String Location,
      String SearchPattern,
      Bool   Recursive
      }

## <a name="adding-as-a-vm-extension"></a>A Virtuálisgép-bővítmény hozzáadása
Az utasítások az Azure PowerShell csatlakozás az előfizetéshez.

1. Adja meg a szolgáltatás, virtuális gép, és a fizetési módot.
   
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
2. Adja meg az Azure storage-fiók neve és kulcsa, amelyhez összegyűjtött fájlok lesz feltöltve.
   
        $StorageAccountName = 'YourStorageAccountName'
        $StorageAccountKey  = ‘YouStorageAccountKey'
3. A SetAzureVMLogCollector.ps1 (a cikk végén található) az alábbiak szerint hívása a AzureLogCollector bővítmény engedélyezése egy felhőalapú szolgáltatás. Ha befejeződött a végrehajtás, a feltöltött fájl https://YouareStorageAccountName.blob.core.windows.net/vmlogs alatt található

Az a parancsfájlnak átadott paraméterek definícióját a következő: (Ez másolódik alatt is.)

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

* Szolgáltatásnév: A felhőszolgáltatás neve.
* VMName a virtuális gép nevét.
* Mód: Gyűjtemény mód. "Teljes" vagy "GA".
* StorageAccountName: A gyűjtött adatok tárolásához Azure storage-fiókjának neve.
* StorageAccountKey: Azure storage-fiók kulcs neve.
* AdditionalDataLocationList: Listáját az alábbi szerkezettel:

```
      {
        String Name,
        String Location,
        String SearchPattern,
        Bool   Recursive
      }
```

## <a name="extention-powershell-script-files"></a>Kiterjesztés PowerShell parancsfájlok
SetAzureServiceLogCollector.ps1

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


SetAzureVMLogCollector.ps1

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

    Write-Output "PublicConfigurtion is: \r\n$publicConfigJSON"

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

## <a name="next-steps"></a>Következő lépések
Most vizsgálja meg, vagy a naplók másolása egy nagyon egyszerű helyről.

