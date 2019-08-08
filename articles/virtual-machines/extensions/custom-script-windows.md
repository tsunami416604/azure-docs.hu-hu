---
title: Egyéni Azure-szkriptek bővítménye Windowshoz | Microsoft Docs
description: A Windowsos virtuális gépek konfigurációs feladatainak automatizálása az egyéni szkriptek bővítményének használatával
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: robreed
ms.openlocfilehash: 58b6531a394db8f9d29dcc0fe9b4b40d1725e70a
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774587"
---
# <a name="custom-script-extension-for-windows"></a>Egyéni parancsfájl-bővítmény a Windowshoz

Az egyéni szkriptek bővítménye letölti és végrehajtja a parancsfájlokat az Azure Virtual Machines szolgáltatásban. Ez a bővítmény az üzembe helyezési konfiguráció, a Szoftvertelepítés vagy bármely egyéb konfigurációs vagy felügyeleti feladat után hasznos. A szkriptek az Azure Storage-ból vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében. Az egyéni szkriptek bővítménye Azure Resource Manager-sablonokkal integrálódik, és az Azure CLI, a PowerShell, a Azure Portal vagy az Azure virtuális gép REST API használatával futtatható.

Ez a dokumentum részletesen ismerteti, hogyan használhatók az egyéni szkriptek bővítmények a Azure PowerShell modullal, Azure Resource Manager sablonokkal és a Windows rendszerekre vonatkozó hibaelhárítási lépésekkel.

## <a name="prerequisites"></a>Előfeltételek

> [!NOTE]  
> Ne használja az egyéni szkriptek bővítményét az Update-AzVM futtatására ugyanazzal a virtuális géppel, mint a paraméterrel, mert a várakozás magára is megtörténik.  

### <a name="operating-system"></a>Operációs rendszer

A Windowshoz készült egyéni parancsfájl-bővítmény a bővítmény által támogatott bővítmény OSs-t futtatja, további információ: ez az [Azure-bővítmény által támogatott operációs rendszerek](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Parancsfájl helye

Beállíthatja, hogy a bővítmény az Azure Blob Storage hitelesítő adatait használja az Azure Blob Storage eléréséhez. A parancsfájl helye bárhol lehet, feltéve, hogy a virtuális gép átirányítja az adott végpontra, például a GitHubra vagy egy belső fájlkiszolgálóra.

### <a name="internet-connectivity"></a>Internetkapcsolat

Ha külsőleg le kell töltenie egy parancsfájlt, például a GitHubról vagy az Azure Storage-ból, akkor további tűzfal-és hálózati biztonsági csoportok portjait kell megnyitnia. Ha például a szkript az Azure Storage-ban található, az Azure NSG Service-címkék használatával engedélyezheti a hozzáférést [](../../virtual-network/security-overview.md#service-tags)a tároláshoz.

Ha a parancsfájl egy helyi kiszolgálón található, akkor továbbra is szükség lehet további tűzfal-és hálózati biztonsági csoport portjainak megnyitására.

### <a name="tips-and-tricks"></a>tippek és trükkök

* Ennek a bővítménynek a legnagyobb meghibásodási aránya a parancsfájl szintaktikai hibái miatt történik, és a parancsfájl futtatása hiba nélkül fut, és a parancsfájlba további naplózást is végez, így könnyebben megtalálhatja a sikertelenség helyét.
* Idempotens parancsfájlokat írhat. Ez biztosítja, hogy ha véletlenül újra futnak, a rendszer nem fog változásokat okozni.
* Gondoskodjon arról, hogy a szkriptek futtatásához ne legyen szükség felhasználói beavatkozásra.
* A szkript futtatásának engedélyezett időtartama 90 perc. Ha egy szkript ennél tovább fut, az a bővítmény üzembe helyezésének meghiúsulását eredményezi.
* A szkript ne váltson ki újraindításokat, mert ez a művelet problémákat okoz a telepítés alatt álló többi bővítményben. Újraindítás után a bővítmény nem fog tovább működni.
* Ha olyan szkripttel rendelkezik, amely újraindítást eredményez, majd telepíti az alkalmazásokat, és futtatja a parancsfájlokat, ütemezheti az újraindítást egy Windows ütemezett feladattal, vagy használhat olyan eszközöket, mint a DSC, a Chef vagy a Puppet Extensions.
* A bővítmény egy szkriptet csak egyszer futtat. Ha egy szkriptet minden indításkor futtatni szeretne, létre kell hoznia egy ütemezett Windows-feladatot a bővítménnyel.
* Ha ütemezni szeretné, hogy mikor fusson egy szkript, hozzon létre egy ütemezett Windows-feladatot a bővítménnyel.
* Amikor a szkript fut, az Azure Portalon vagy a CLI-n a bővítmény „átmeneti” állapotát fogja látni. Ha gyakoribb állapotfrissítést szeretne egy futó szkripthez, létre kell hoznia a saját megoldását.
* Az egyéni szkriptek bővítménye nem támogatja natív módon a proxykiszolgálót, azonban használhat olyan fájlátviteli eszközt, amely támogatja a parancsfájlban lévő proxykiszolgálót, például a *curl*
* Vegye figyelembe, hogy vannak olyan nem alapértelmezett könyvtárhelyek, amelyekre a szkriptjei és a parancsai támaszkodhatnak, és amelyek rendelkeznek a helyzet kezeléséhez szükséges logikával.
* Az egyéni szkriptek bővítménye a LocalSystem fiókban fog futni.

## <a name="extension-schema"></a>Bővítményséma

Az egyéni szkriptek bővítményének konfigurációja meghatározza a parancsfájl helyét és a futtatandó parancsot. Ezt a konfigurációt a konfigurációs fájlokban tárolhatja, megadhatja a parancssorban, vagy megadhatja egy Azure Resource Manager sablonban.

A bizalmas adatokat egy védett konfigurációban tárolhatja, amely titkosítva van, és csak a virtuális gépen végez visszafejtést. A védett konfiguráció akkor hasznos, ha a végrehajtási parancs titkos kódokat, például jelszót tartalmaz.

Ezeket az elemeket bizalmas adatokként kell kezelni, és meg kell adni a bővítmények által védett beállítások konfigurációjában. Az Azure VM-bővítmény védett beállítás adatok titkosítva, és csak az átjárót tartalmazó a cél virtuális gépen.

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.9",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```

> [!NOTE]
> Egy adott időpontban csak egy bővítmény telepíthető egy virtuális gépre, így az azonos virtuális géphez tartozó Resource Manager-sablonban kétszer is megadható az egyéni parancsfájl.

### <a name="property-values"></a>Tulajdonságok értékei

| Name (Név) | Érték és példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute | sztring |
| type | CustomScriptExtension | sztring |
| typeHandlerVersion | 1.9 | int |
| fileUris (például) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| timestamp (például:) | 123456789 | 32 bites egész szám |
| commandToExecute (például) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | Karakterlánc |
| storageAccountName (például:) | examplestorageacct | Karakterlánc |
| storageAccountKey (például) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | Karakterlánc |

>[!NOTE]
>Ezek a tulajdonságok nevei megkülönböztetik a kis-és nagybetűket. Az üzembe helyezési problémák elkerüléséhez használja az itt látható neveket.

#### <a name="property-value-details"></a>Tulajdonság értékének részletei

* `commandToExecute`: (**kötelező**, karakterlánc) a belépési pont parancsfájlját végre kell hajtani. Akkor használja ezt a mezőt, ha a parancs titkos kódokat (például jelszavakat) tartalmaz, vagy ha a fileUris bizalmasak.
* `fileUris`: (opcionális, karakterlánc-tömb) a letölteni kívánt fájl (ok) URL-címei.
* `timestamp`(opcionális, 32 bites egész szám) Ez a mező csak a parancsfájl újrafuttatásának indítására használható a mező értékének módosításával.  Bármely egész érték elfogadható; csak az előző értéktől eltérő lehet.
* `storageAccountName`: (nem kötelező, karakterlánc) a Storage-fiók neve. Ha tárolási hitelesítő adatokat ad meg, `fileUris` az összes URL-címet az Azure-Blobok számára kell megadni.
* `storageAccountKey`: (nem kötelező, karakterlánc) a Storage-fiók elérési kulcsa

A következő értékek a nyilvános vagy a védett beállításokban állíthatók be, a bővítmény elveti az összes olyan konfigurációt, ahol az alábbi értékek a nyilvános és a védett beállításokban is be vannak állítva.

* `commandToExecute`

A nyilvános beállítások használata hasznos lehet a hibakereséshez, de javasoljuk, hogy a védett beállításokat használja.

A nyilvános beállításokat a rendszer tiszta szövegként küldi el arra a virtuális gépre, amelyen a parancsfájl végre lesz hajtva.  A védett beállítások titkosítása csak az Azure-ban és a virtuális gépen ismert kulcs használatával történik. A rendszer elküldte a beállításokat a virtuális gépre, vagyis ha a beállítások titkosítva lettek, a virtuális gépen titkosítva lesznek. A titkosított értékek visszafejtéséhez használt tanúsítványt a virtuális gépen tárolja a rendszer, és a beállításokat (ha szükséges) a futtatáskor használja.

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. Az előző szakaszban részletezett JSON-séma használható Azure Resource Manager sablonban az egyéni szkriptek bővítmény futtatásához az üzembe helyezés során. Az alábbi példák azt mutatják be, hogyan használható az egyéni szkriptek bővítménye:

* [Oktatóanyag: Virtuálisgép-bővítmények üzembe helyezése Azure Resource Manager-sablonokkal](../../azure-resource-manager/resource-manager-tutorial-deploy-vm-extensions.md)
* [Kétszintű alkalmazás üzembe helyezése a Windowsban és az Azure SQL DB-ben](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>PowerShell-telepítés

A `Set-AzVMCustomScriptExtension` parancs használatával hozzáadhatja az egyéni parancsfájl-bővítményt egy meglévő virtuális géphez. További információ: [set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>További példák

### <a name="using-multiple-scripts"></a>Több parancsfájl használata

Ebben a példában három parancsfájlt használ a kiszolgáló létrehozásához. A **commandToExecute** meghívja az első parancsfájlt, majd a mások meghívásának lehetőségeit. Rendelkezhet például egy olyan főparancsfájllal, amely a megfelelő hibák kezelésével, naplózásával és állapotának kezelésével vezérli a végrehajtást. A parancsfájlok a helyi gépre lesznek letöltve a futtatáshoz. Például `1_Add_Tools.ps1` ha `2_Add_Features.ps1` a szkripthez hozzáadja `.\2_Add_Features.ps1` a parancsot, és megismétli ezt a folyamatot a ben `$settings`definiált többi parancsfájlhoz.

```powershell
$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.9" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Parancsfájlok futtatása helyi megosztásból

Ebben a példában érdemes helyi SMB-kiszolgálót használni a parancsfájl helyéhez. Ehhez a **commandToExecute**kivételével semmilyen egyéb beállítást nem kell megadnia.

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.9" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Egyéni parancsfájlok futtatása többször, a CLI használatával

Ha többször is futtatni szeretné az egyéni parancsfájl-bővítményt, akkor ezt a műveletet csak a következő feltételekkel hajthatja végre:

* A kiterjesztés **neve** paraméter megegyezik a bővítmény előző telepítésével.
* Módosítsa a konfigurációt, ellenkező esetben a parancs nem lesz újra végrehajtva. Hozzáadhat egy dinamikus tulajdonságot a parancshoz, például egy időbélyeget.

Azt is megteheti, hogy a [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) tulajdonság értéke **true (igaz**).

### <a name="using-invoke-webrequest"></a>A meghívás-webkérés használata

Ha a szkriptben a [meghívás-](/powershell/module/microsoft.powershell.utility/invoke-webrequest) webkérést használja, meg kell adnia a paramétert `-UseBasicParsing` , különben a részletes állapot ellenőrzésekor a következő hibaüzenetet fogja kapni:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```

## <a name="classic-vms"></a>A klasszikus virtuális gépeket

Az egyéni szkriptek klasszikus virtuális gépeken való üzembe helyezéséhez használhatja a Azure Portal vagy a klasszikus Azure PowerShell parancsmagokat.

### <a name="azure-portal"></a>Azure Portal

Navigáljon a klasszikus VM-erőforráshoz. Válassza a bővítmények lehetőséget a **Beállítások**területen.

Kattintson a **+ Hozzáadás** elemre, majd az erőforrások listájában válassza az **Egyéni szkriptek bővítmény**lehetőséget.

A **bővítmény telepítése** lapon válassza ki a helyi PowerShell-fájlt, és töltse ki az argumentumokat, és kattintson **az OK**gombra.

### <a name="powershell"></a>PowerShell

Használja a [set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) parancsmagot, amellyel hozzáadhatja az egyéni szkriptek bővítményét egy meglévő virtuális géphez.

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.windows.net/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítmények állapotával kapcsolatos adatok beolvashatók a Azure Portalból, és az Azure PowerShell modul használatával. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa a következő parancsot:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

A bővítmény kimenete a cél virtuális gép következő mappájában található fájlokra van naplózva.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

A megadott fájlok a cél virtuális gép következő mappájába lesznek letöltve.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

ahol `<n>` a decimális egész szám, amely változhat a bővítmény végrehajtásai között.  Az `1.*` érték megegyezik a bővítmény tényleges, `typeHandlerVersion` aktuális értékével.  Például a tényleges könyvtár lehet `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

A `commandToExecute` parancs végrehajtásakor a bővítmény beállítja ezt a könyvtárat ( `...\Downloads\2`például) az aktuális munkakönyvtárként. Ez a folyamat lehetővé teszi a relatív elérési utak használatát a `fileURIs` tulajdonságon keresztül letöltött fájlok megkereséséhez. Példákért tekintse meg az alábbi táblázatot.

Mivel az abszolút letöltési útvonal az idő múlásával változhat, érdemes lehet relatív parancsfájl-/fájlelérési utakat választani a `commandToExecute` karakterláncban, amikor csak lehetséges. Példa:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Az elérési út adatai az első URI-szegmens megtartása után `fileUris` a tulajdonságok listáján letöltött fájlok számára.  Ahogy az alábbi táblázatban is látható, a letöltött fájlok a letöltési alkönyvtárakba vannak leképezve, hogy tükrözzék `fileUris` az értékek szerkezetét.  

#### <a name="examples-of-downloaded-files"></a>Példák a letöltött fájlokra

| URI a fileUris | Relatív letöltési hely | Abszolút letöltött hely <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> az abszolút könyvtár elérési útjai megváltoznak a virtuális gép élettartama során, de nem a CustomScript-bővítmény egyetlen végrehajtásán belül.

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Egy Azure-támogatási incidens is benyújtható. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).
