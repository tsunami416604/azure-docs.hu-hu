


## <a name="using-vm-extensions"></a>Virtuálisgép-bővítmények használatával
Az Azure Virtuálisgép-bővítmények megvalósítása viselkedések vagy szolgáltatásokat vagy más programok Azure virtuális gépeken használata (például a **WebDeployForVSDevTest** bővítmény lehetővé teszi, hogy a Visual Studio Web Deploy megoldásokhoz az Azure virtuális gépre), vagy adjon meg a Ahhoz, hogy a virtuális gép kommunikálni képes néhány más viselkedés támogatására (például segítségével a virtuális gép eléréséhez kapcsolódó kiegészítő mezőket PowerShell, az Azure CLI és a többi ügyféltől alaphelyzetbe állítása, vagy módosítsa a távelérés értékek az Azure virtuális gép).

> [!IMPORTANT]
> Bővítmények támogatják-e a szolgáltatások által teljes listáját lásd: [Azure Virtuálisgép-bővítmények és a szolgáltatások](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Mivel minden egyes Virtuálisgép-bővítmény egy adott funkcióhoz támogatja, pontosan mit, illetve használhat nem kiterjesztési függ a bővítményt. -Módosítása előtt a virtuális gép győződjön meg arról, hogy elolvasta a használni kívánt Virtuálisgép-bővítmény dokumentációja. Egyes Virtuálisgép-bővítmények nem támogatott; mások rendelkezik tulajdonságokkal beállítható, hogy a virtuális gép megváltozzon jelentős.
> 
> 

A leggyakoribb feladatokat is:

1. Rendelkezésre álló bővítmények keresése
2. A betöltött frissítései
3. Bővítmények hozzáadása
4. Bővítmények eltávolítása

## <a name="find-available-extensions"></a>Rendelkezésre álló bővítmények keresése
Megkeresheti a bővítményt, és részletes információk segítségével:

* PowerShell
* Az Azure platformfüggetlen parancssori felület (Azure CLI)
* Szolgáltatásfelügyeleti REST API

### <a name="azure-powershell"></a>Azure PowerShell
Néhány bővítmény rendelkezik PowerShell-parancsmagok, amelyek adott ki, amely előfordulhat, hogy könnyebben konfigurációjuk powershellből; de a következő parancsmag az összes Virtuálisgép-bővítmények fog működni.

Az alábbi parancsmagok segítségével rendelkezésre álló bővítmények kapcsolatos információkhoz:

* A webes szerepkörök vagy feldolgozói szerepkörök példányai, használhatja a [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) parancsmag.
* A virtuális gépek példányai, használhatja a [Get-AzureVMAvailableExtension](https://msdn.microsoft.com/library/azure/dn722480.aspx) parancsmag.
  
   Például az alábbi példakód bemutatja, hogyan-információinak felsorolásához a **IaaSDiagnostics** bővítmény PowerShell használatával.
  
      PS C:\> Get-AzureVMAvailableExtension -ExtensionName IaaSDiagnostics
  
      Publisher                   : Microsoft.Azure.Diagnostics
      ExtensionName               : IaaSDiagnostics
      Version                     : 1.2
      Label                       : Microsoft Monitoring Agent Diagnostics
      Description                 : Microsoft Monitoring Agent Extension
      PublicConfigurationSchema   :
      PrivateConfigurationSchema  :
      IsInternalExtension         : False
      SampleConfig                :
      ReplicationCompleted        : True
      Eula                        :
      PrivacyUri                  :
      HomepageUri                 :
      IsJsonExtension             : True
      DisallowMajorVersionUpgrade : False
      SupportedOS                 :
      PublishedDate               :
      CompanyName                 :

### <a name="azure-command-line-interface-azure-cli"></a>Az Azure parancssori felület (Azure CLI)
Néhány bővítmény van rájuk vonatkozó Azure parancssori felület parancsait (a Docker Virtuálisgép-bővítmény csak egy példa a), amely könnyebben lehet, hogy a konfigurációs; de a következő parancsokat az összes Virtuálisgép-bővítmények fog működni.

Használhatja a **azure virtuális gép Bővítménylista** parancsot a rendelkezésre álló bővítmények kapcsolatos információkhoz, és használja a **–-json** lehetőséget az összes rendelkezésre álló információkat egy vagy több bővítmények megjelenítéséhez. Ha nem használja a bővítmények nevének, a parancs az összes rendelkezésre álló bővítmények JSON leírását adja meg.

Például az alábbi példakód bemutatja, hogyan-információinak felsorolásához a **IaaSDiagnostics** az Azure parancssori felület használatával bővítmény **azure virtuális gép Bővítménylista** parancs, és használja a **–-json**  teljes információt lehetőséget.

    $ azure vm extension list -n IaaSDiagnostics --json
    [
      {
        "publisher": "Microsoft.Azure.Diagnostics",
        "name": "IaaSDiagnostics",
        "version": "1.2",
        "label": "Microsoft Monitoring Agent Diagnostics",
        "description": "Microsoft Monitoring Agent Extension",
        "replicationCompleted": true,
        "isJsonExtension": true
      }
    ]



### <a name="service-management-rest-apis"></a>Szolgáltatáskezelési REST API-k
A következő REST API-k segítségével rendelkezésre álló bővítmények kapcsolatos információkhoz:

* A webes szerepkörök vagy feldolgozói szerepkörök példányai, használhatja a [lista rendelkezésre álló bővítmények](https://msdn.microsoft.com/library/dn169559.aspx) műveletet. Rendelkezésre álló bővítmények verzióinak listázása segítségével [lista bővítmény verziók](https://msdn.microsoft.com/library/dn495437.aspx).
* A virtuális gépek példányai, használhatja a [erőforrás-kiterjesztések listájából](https://msdn.microsoft.com/library/dn495441.aspx) műveletet. Rendelkezésre álló bővítmények verzióinak listázása segítségével [lista erőforrás-bővítmény verziók](https://msdn.microsoft.com/library/dn495440.aspx).

## <a name="add-update-or-disable-extensions"></a>Adja hozzá, frissítés, vagy bővítmények letiltása
Bővítmények példány létrehozásakor, illetve azok lehet hozzáadni egy futó példány lehet hozzáadni. Bővítmények frissítése, letiltott vagy eltávolított. Azure PowerShell-parancsmagokkal vagy a Service Management REST API-műveletek segítségével ezeket a műveleteket végezheti el. Paraméterek telepítése és beállítása az egyes bővítmények szükségesek. Nyilvános és titkos paraméterek a bővítmények esetében támogatottak.

### <a name="azure-powershell"></a>Azure PowerShell
Azure PowerShell-parancsmagok használatával hozzá és frissítsenek bővítmények legegyszerűbb módja. A bővítmény-parancsmagok használata esetén a bővítmény konfigurációja a legtöbb történik meg. Esetenként esetleg programozott módon veheti fel. Amikor erre szükség, meg kell adni a bővítmény konfigurációja.

A következő parancsmag segítségével tudja, hogy egy bővítmény igényel-e a nyilvános és titkos paraméterek konfigurálása:

* A webes szerepkörök vagy feldolgozói szerepkörök példányai, használhatja a **Get-AzureServiceAvailableExtension** parancsmag.
* A virtuális gépek példányai, használhatja a **Get-AzureVMAvailableExtension** parancsmag.

### <a name="service-management-rest-apis"></a>Szolgáltatáskezelési REST API-k
Amikor a rendelkezésre álló bővítmények lista tartalmazza a REST API-k használatával, úgy kell konfigurálni, a bővítmény Mitől információt kap. A visszaadott információk megjelenítése előfordulhat, hogy egy séma nyilvános és titkos séma paraméterinformáció. Visszaadja a nyilvános paraméterértékeket a példányaira vonatkozó lekérdezésekben. Titkos paraméter értéke nem lehet megjeleníteni.

A következő REST API-k segítségével tudja, hogy egy bővítmény igényel-e a nyilvános és titkos paraméterek konfigurálása:

* A webes szerepkörök vagy feldolgozói szerepköröket, a **PublicConfigurationSchema** és **PrivateConfigurationSchema** elemek információkkal a válaszát a [lista érhető el Bővítmények](https://msdn.microsoft.com/library/dn169559.aspx) műveletet.
* A virtuális gépek példányait a **PublicConfigurationSchema** és **PrivateConfigurationSchema** elemek információkkal a válaszát a [lista erőforrás Bővítmények](https://msdn.microsoft.com/library/dn495441.aspx) műveletet.

> [!NOTE]
> Bővítmények JSON meghatározott konfigurációk is használható. Ha ilyen típusú kiterjesztések csak akkor használja, a **SampleConfig** elem szolgál.
> 
> 

