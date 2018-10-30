---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d14cfb82ae74f85425dbd3e8a365e8b99969641d
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227250"
---
## <a name="using-vm-extensions"></a>Virtuálisgép-bővítmények használatával
Az Azure Virtuálisgép-bővítmények megvalósítása viselkedések vagy szolgáltatásokat vagy más Azure-beli virtuális gépeken működik programok (például a **WebDeployForVSDevTest** bővítmény lehetővé teszi, hogy a Visual Studio, a Web Deploy-megoldások az Azure virtuális gép), vagy adja meg a arra, hogy az, hogy a virtuális gép interakcióba néhány egyéb viselkedés támogatásához (például használhatja a Virtuálisgép-hozzáférési extensions, PowerShell, az Azure CLI és REST-ügyfelek visszaállítása vagy távoli hozzáférés értékek módosítása az Azure virtuális gépen).

> [!IMPORTANT]
> Az általuk támogatott funkciókról a bővítmények teljes listáját lásd: [Azure Virtuálisgép-bővítmények és szolgáltatások](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Mivel minden egyes Virtuálisgép-bővítmény egy adott szolgáltatás támogatja, pontosan akkor is és mit nem kiterjesztésű attól függ, a bővítményt. Ezért módosítja a virtuális Géphez, mielőtt győződjön meg arról, hogy elolvasta a használni kívánt Virtuálisgép-bővítmény dokumentációját. Egyes Virtuálisgép-bővítmények eltávolítása nem támogatott. mások olyan tulajdonságokkal rendelkeznek, akkor állítható be, amely jelentősen módosítható a virtuális gép működése.
> 
> 

A leggyakoribb feladatok a következők:

1. Elérhető bővítmények keresése
2. A betöltött bővítmények frissítése
3. Bővítmények hozzáadása
4. Bővítmények eltávolítása

## <a name="find-available-extensions"></a>Elérhető bővítmények keresése
Megkeresheti a bővítményt, és részletes információk segítségével:

* PowerShell
* Az Azure platformfüggetlen parancssori felület (Azure CLI)
* Szolgáltatásfelügyeleti REST API

### <a name="azure-powershell"></a>Azure PowerShell
Néhány bővítmény rendelkezik a PowerShell-parancsmagok, amelyek adott, amely előfordulhat, hogy könnyebben konfigurációjuk powershellből; de a következő parancsmagokat minden Virtuálisgép-bővítmények fog működni.

A következő parancsmag használatával információkat kaphat az elérhető bővítmények:

* A webes szerepkört vagy feldolgozói szerepkörök példányait, használhatja a [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) parancsmagot.
* A virtuálisgép-példányok, használhatja a [Get-AzureVMAvailableExtension](https://msdn.microsoft.com/library/azure/dn722480.aspx) parancsmagot.
  
   Például az alábbi példakód bemutatja, hogyan listázhatja a vonatkozó információkat a **IaaSDiagnostics** bővítmény PowerShell használatával.
  
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
Néhány bővítmény rendelkezik az Azure CLI-parancsokat, amelyek a rájuk adott (a Docker VM-bővítmény a egy példa), amely könnyebben lehet, hogy a konfigurációs; de a következő parancsokat az összes Virtuálisgép-bővítmények fog működni.

Használhatja a **azure virtuálisgép-bővítmény lista** parancsot a rendelkezésre álló bővítményeket szerezhet, és használja a **–-json** beállítás egy vagy több bővítményt az összes rendelkezésre álló információit jeleníti meg. Ha nem használja a bővítmény nevét, a parancs visszaadja az összes rendelkezésre álló bővítményeket JSON-leírásuk.

Például az alábbi példakód bemutatja, hogyan listázhatja a vonatkozó információkat a **IaaSDiagnostics** kiterjesztése az Azure CLI-vel **azure virtuálisgép-bővítmény lista** parancs, és használja a **–-json**  lehetőség, teljes körű információkat ad vissza.

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
Az alábbi REST API-k használatával információkat kaphat az elérhető bővítmények:

* A webes szerepkört vagy feldolgozói szerepkörök példányait, használhatja a [listában elérhető bővítmények](https://msdn.microsoft.com/library/dn169559.aspx) műveletet. Elérhető bővítmények verzióinak listázása használható [bővítmény-verziók listája](https://msdn.microsoft.com/library/dn495437.aspx).
* A virtuálisgép-példányok, használhatja a [lista erőforrás-bővítményt](https://msdn.microsoft.com/library/dn495441.aspx) műveletet. Elérhető bővítmények verzióinak listázása használható [List erőforrás-bővítmény Versions](https://msdn.microsoft.com/library/dn495440.aspx).

## <a name="add-update-or-disable-extensions"></a>Hozzáadása, frissítése vagy bővítmények letiltása
Egy példány létrehozásakor, illetve egy futó példányával követően adhatók hozzá bővítmények adhat hozzá. Bővítmények frissítése, letiltott vagy eltávolított. Végrehajthatja ezeket a műveleteket az Azure PowerShell-parancsmagokkal vagy a Service Management REST API műveletek használatával. Paraméterek telepítése és beállítása az egyes bővítmények szükségesek. Nyilvános és privát paraméterek-bővítmények használata támogatott.

### <a name="azure-powershell"></a>Azure PowerShell
Azure PowerShell-parancsmagok használata a legegyszerűbben úgy hozzáadása és bővítmények frissítése. A bővítmény-parancsmagok használata esetén a bővítmény konfigurációja a legtöbb, készen áll. Időnként szükség lehet programozott módon egy bővítmény hozzáadásához. Ha erre van szükség, meg kell adnia a bővítmény konfigurációját.

Az alábbi parancsmagok segítségével tudja, hogy egy bővítmény igényli-e a nyilvános és privát-paraméterek konfigurációja:

* A webes szerepkört vagy feldolgozói szerepkörök példányait, használhatja a **Get-AzureServiceAvailableExtension** parancsmagot.
* A virtuálisgép-példányok, használhatja a **Get-AzureVMAvailableExtension** parancsmagot.

### <a name="service-management-rest-apis"></a>Szolgáltatáskezelési REST API-k
Elérhető bővítmények listáját a REST API-k használatával kérheti le, amikor kap információ arról, hogy a bővítmény kell konfigurálni. A visszaadott információk egy nyilvános séma- és privát séma által képviselt paraméteradatoknak előfordulhat, hogy megjelenítése. Nyilvános paraméter értékét adja vissza a példányok kapcsolatos lekérdezések. Privát paraméter értéke nem lehet megjeleníteni.

Az alábbi REST API-k segítségével tudja, hogy egy bővítmény igényli-e a nyilvános és privát-paraméterek konfigurációja:

* A webes szerepkört vagy feldolgozói szerepkörök példányait a **PublicConfigurationSchema** és **PrivateConfigurationSchema** elemek válaszából adatokat tartalmazzák a [lista érhető el Bővítmények](https://msdn.microsoft.com/library/dn169559.aspx) műveletet.
* A virtuális gépek példányainak a **PublicConfigurationSchema** és **PrivateConfigurationSchema** elemek válaszából adatokat tartalmazzák a [lista erőforrás Bővítmények](https://msdn.microsoft.com/library/dn495441.aspx) műveletet.

> [!NOTE]
> Bővítmények segítségével is meghatározott konfigurációk JSON-fájllal. Ha az ilyen típusú bővítmények használata esetén csak a **SampleConfig** elem szolgál.
> 
> 

