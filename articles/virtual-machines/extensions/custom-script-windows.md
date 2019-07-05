---
title: A Windows Azure egyéni szkriptek futtatására szolgáló bővítmény |} A Microsoft Docs
description: Az egyéni szkriptbővítmény használatával Windows virtuális gép konfigurációs feladatok automatizálása
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: robreed
ms.openlocfilehash: 8487b8477b1837fce0b1c2c6435174c48dfbded4
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478423"
---
# <a name="custom-script-extension-for-windows"></a>A Windows egyéni szkriptek futtatására szolgáló bővítmény

Az egyéni Szkriptbővítmény letölti és végrehajtja a parancsfájlok Azure-beli virtuális gépeken. A bővítmény az üzembe helyezést követő konfiguráció, Szoftvertelepítés, vagy más konfigurációs vagy fájlkezelési feladatok számára hasznos. A szkriptek az Azure Storage-ból vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében. Az egyéni szkriptek bővítménye az Azure Resource Manager-sablonokkal van integrálva, és az Azure CLI, PowerShell, az Azure portal vagy az Azure Virtual Machine – REST API használatával futtatható.

Ez a dokumentum részletesen használata az egyéni Szkriptbővítmény használatával, az Azure PowerShell-modul, az Azure Resource Manager-sablonok és hibaelhárítási lépéseket a Windows rendszerek részleteit.

## <a name="prerequisites"></a>Előfeltételek

> [!NOTE]  
> Ne használjon egyéni szkriptek futtatására szolgáló bővítmény óta magát a vár az Update-azvm parancsmag futtatásához a virtuális géppel azonos a paraméterként.  

### <a name="operating-system"></a>Operációs rendszer

A Custom Script bővítmény a Windows rendszer további információ a támogatott bővítmény kiterjesztés nyílt forráskódú, futtassa, és ez [Azure-bővítmény által támogatott operációs rendszerek](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>A parancsprogram helye

Beállíthatja, hogy a bővítmény használata az Azure Blob storage hitelesítő adatai az Azure Blob storage elérésére. A parancsprogram helyét lehet bárhol, mindaddig, amíg a virtuális gép is irányíthatja a végpontot, például a GitHub és a egy belső fájlkiszolgáló.

### <a name="internet-connectivity"></a>Internetkapcsolat

Ha le kell töltenie egy parancsfájlt kívülről például a GitHub vagy az Azure Storage, majd további tűzfal- és hálózati biztonsági csoport portokat kell megnyitni. Például, ha a parancsfájl az Azure Storage-ban található, is engedélyezi a hozzáférést Azure NSG szolgáltatás címkék használatával [tárolási](../../virtual-network/security-overview.md#service-tags).

Ha a parancsfájl egy helyi kiszolgálón, majd továbbra is szükség lehet további tűzfal és hálózati biztonsági csoport portokat kell megnyitni.

### <a name="tips-and-tricks"></a>tippek és trükkök

* A legmagasabb Hibaarány esetén ez a bővítmény a parancsfájl hiba nélkül fut a parancsfájl tesztelési szintaktikai hibák miatt, és is helyezheti további naplózási műveleteket a parancsfájlba, hogy könnyebben megtalálja, ahol nem sikerült.
* Parancsfájlokat írhat, amelyek idempotens. Ez biztosítja, hogy véletlenül újra futnak, ha nem okoz rendszer változásait.
* Gondoskodjon arról, hogy a szkriptek futtatásához ne legyen szükség felhasználói beavatkozásra.
* A szkript futtatásának engedélyezett időtartama 90 perc. Ha egy szkript ennél tovább fut, az a bővítmény üzembe helyezésének meghiúsulását eredményezi.
* A szkript ne váltson ki újraindításokat, mert ez a művelet problémákat okoz a telepítés alatt álló többi bővítményben. Újraindítás után a bővítmény nem fog tovább működni.
* Ha egy parancsfájl, amely egy újraindítás szükséges, ezután-alkalmazások telepítése és parancsfájlok futtatására, az újraindítás a Windows ütemezett feladat használatával ütemezheti, vagy használja az eszközök, például a DSC, Chef vagy Puppet bővítmények.
* A bővítmény egy szkriptet csak egyszer futtat. Ha egy szkriptet minden indításkor futtatni szeretne, létre kell hoznia egy ütemezett Windows-feladatot a bővítménnyel.
* Ha ütemezni szeretné, hogy mikor fusson egy szkript, hozzon létre egy ütemezett Windows-feladatot a bővítménnyel.
* Amikor a szkript fut, az Azure Portalon vagy a CLI-n a bővítmény „átmeneti” állapotát fogja látni. Ha gyakoribb állapotfrissítést szeretne egy futó szkripthez, létre kell hoznia a saját megoldását.
* Egyéni szkriptek futtatására szolgáló bővítmény nem támogatja natív módon proxykiszolgálókat, azonban egy fájlátviteli eszköz, amely támogatja a proxykiszolgálók a parancsfájlban, például használhatja *Curl*
* Vegye figyelembe, hogy vannak olyan nem alapértelmezett könyvtárhelyek, amelyekre a szkriptjei és a parancsai támaszkodhatnak, és amelyek rendelkeznek a helyzet kezeléséhez szükséges logikával.

## <a name="extension-schema"></a>Bővítményséma

Az egyéni Szkriptbővítmény konfigurációját meghatározza többek között a parancsprogram helye és a futtatandó parancsot. Konfigurációs fájlban tárolhatja az ebben a konfigurációban, adja meg azt a parancssorból, vagy adja meg az Azure Resource Manager-sablon.

Egy védett konfigurációban, amely titkosított, és csak a virtuális gépen belül visszafejteni bizalmas adatokat tárolhat. A védett konfiguráció akkor hasznos, ha a végrehajtás parancs tartalmazza a titkos kulcsokat például a jelszó.

Ezeket az elemeket kell kezelni, mint a bizalmas adatok és a bővítmények védett beállítás konfigurációjában megadott. Az Azure VM-bővítmény védett beállítás adatok titkosítva, és csak az átjárót tartalmazó a cél virtuális gépen.

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
> Egy bővítmény csak egyetlen verziója telepíthető egy virtuális Gépet egy időben, adja meg kétszer ugyanazt a Resource Manager-sablon a virtuális gép ugyanazon az egyéni parancsfájl futtatása sikertelen lesz.

### <a name="property-values"></a>Tulajdonságok értékei

| Name (Név) | Érték és példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute | string |
| type | CustomScriptExtension | string |
| typeHandlerVersion | 1.9 | int |
| fileUris (például:) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| timestamp (például:) | 123456789 | 32 bites egész szám |
| commandToExecute (például:) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | string |
| storageAccountName (például:) | examplestorageacct | string |
| storageAccountKey (például:) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

>[!NOTE]
>Ezek a tulajdonságnevek megkülönböztetik a kis-és nagybetűket. Alkalmazástelepítéssel kapcsolatos problémák elkerülése érdekében használja a nevek itt látható módon.

#### <a name="property-value-details"></a>A tulajdonság értéke részletei

* `commandToExecute`: (**szükséges**, string) végrehajtásához a BelépésiPont-szkriptet. Helyette használja ezt a mezőt, ha a parancs tartalmazza a titkos kulcsok, például jelszavak, vagy a fileUris rendszer-és nagybetűket.
* `fileUris`: (nem kötelező, csak karakterlánc-tömbben) fájl(ok) le kell tölteni az URL-címeket.
* `timestamp` Ebben a mezőben csak való egy futtassa újból a parancsfájl a mező értékének módosításával (nem kötelező, a 32 bites egész szám) használatát.  Minden olyan egész értéket az elfogadható; Ez csak különbözőnek kell lennie, mint a korábbi értéket.
* `storageAccountName`: (nem kötelező, string) a tárfiók nevére. Ha storage hitelesítő adatai, adja meg az összes `fileUris` URL-címeket kell lennie az Azure-Blobok.
* `storageAccountKey`: (nem kötelező, string) storage-fiók hozzáférési kulcsa

A következő értékeket nyilvános vagy védett beállítások is megadhatók, a bővítmény elutasítják semmilyen konfigurálást, ahol az alábbi értékek vannak beállítva, a nyilvános és a védett beállításaiban.

* `commandToExecute`

Hibakeresés, de hasznos lehet nyilvános beállítások használata javasolt, hogy védett beállításokat használja.

Nyilvános beállításokat a virtuális gép, ahol a parancsfájl végrehajtása szövegként érkeznek.  Védett beállítások vannak titkosítva, csak az Azure és a virtuális gép ismert kulcsot használ. A beállítások lesznek mentve a virtuális géphez, mivel lettek küldve, vagyis ha a beállítások titkosított mentést titkosított a virtuális gépen. A tanúsítvány használatával fejthetők vissza a titkosított értékek tárolja a virtuális gépen, és fejti vissza a futásidejű beállításokat (ha szükséges).

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. JSON-sémáját, amely az előző szakaszban részletes üzembe helyezés során az egyéni szkriptek futtatására szolgáló bővítmény futtatása az Azure Resource Manager-sablon is használható. A következő példák bemutatják, hogyan használja az egyéni szkriptek futtatására szolgáló bővítmény:

* [Oktatóanyag: Az Azure Resource Manager-sablonok virtuálisgép-bővítmények telepítése](../../azure-resource-manager/resource-manager-tutorial-deploy-vm-extensions.md)
* [A Windows és az Azure SQL Database két szintű alkalmazás üzembe helyezése](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>PowerShell környezetben végzett telepítés

A `Set-AzVMCustomScriptExtension` parancs használható az egyéni szkriptek futtatására szolgáló bővítmény hozzáadása egy meglévő virtuális gépet. További információkért lásd: [Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

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

Ebben a példában három hozhat létre a kiszolgáló által használt parancsfájlok rendelkezik. A **commandToExecute** meghívja az első parancsfájlt, majd közül választhat, hogy a többi hívása. Például rendelkezhet egy fő parancsfájlt, amely szabályozza a végrehajtási, a jobb hibakezelés, naplózás és állapot-felügyeleti. A parancsfájlok futtatásához a helyi gép letöltődnek. Például a `1_Add_Tools.ps1` lenne hívás `2_Add_Features.ps1` hozzáadásával `.\2_Add_Features.ps1` a parancsfájlt, és ismételje meg a folyamatot a többi parancsfájlok a az meghatározott `$settings`.

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

### <a name="running-scripts-from-a-local-share"></a>Parancsfájlok futtatása egy helyi meghajtóról

Ebben a példában érdemes helyi SMB-kiszolgáló használata a parancsfájl helyét. Ezzel az eljárással, nem kell megadnia a további beállításokat, kivéve a **commandToExecute**.

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

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Egyéni parancsfájl egynél többször futtatása a parancssori felülettel

Ha azt szeretné, az egyéni szkriptek futtatására szolgáló bővítmény futtatása egynél többször, ez a művelet ezen feltételek csak teheti meg:

* A bővítmény **neve** paraméter értéke ugyanaz, mint a bővítmény a korábbi telepítésben.
* Frissítse a konfigurációt más módon a parancs nem fog újra hajtható végre. Hozzáadhat egy dinamikus tulajdonságban parancsba, például egy időbélyegző.

Másik lehetőségként beállíthatja a [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) tulajdonságot **igaz**.

### <a name="using-invoke-webrequest"></a>Invoke-WebRequest használatával

Ha használ [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) a szkriptben paramétert meg kell adnia a `-UseBasicParsing` vagy más fog kapni a következő hiba, amikor a részletes állapot ellenőrzése:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```

## <a name="classic-vms"></a>A klasszikus virtuális gépeket

Az egyéni szkriptek futtatására szolgáló bővítmény klasszikus virtuális gépeket üzembe helyezéséhez használhatja az Azure portal vagy a klasszikus Azure PowerShell-parancsmagokat.

### <a name="azure-portal"></a>Azure Portal

Keresse meg a klasszikus virtuális gép erőforrást. Válassza ki **bővítmények** alatt **beállítások**.

Kattintson a **+ Hozzáadás** és az erőforrások listájában válassza **egyéni szkriptek futtatására szolgáló bővítmény**.

Az a **bővítmény telepítése** lapon válassza ki a helyi PowerShell-fájlt, és adja meg az argumentumokat, és kattintson **Ok**.

### <a name="powershell"></a>PowerShell

Használja a [Set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) parancsmag is használható az egyéni szkriptek futtatására szolgáló bővítmény hozzáadása egy meglévő virtuális gépet.

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

Bővítmény központi telepítések állapotát lehet adatokat beolvasni az Azure Portalról, és az Azure PowerShell-modul segítségével. Adott Virtuálisgép-bővítmények központi telepítési állapotának megtekintéséhez futtassa a következő parancsot:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

Bővítmény kimeneti a cél virtuális gépen a következő mappában található fájlokat a rendszer naplózza.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

A megadott fájlokat tölti be a következő mappához a cél virtuális gépen.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

ahol `<n>` decimális egész szám, amely előfordulhat, hogy módosítsa a bővítmény végrehajtások közötti.  A `1.*` értéke megegyezik a tényleges, jelenlegi `typeHandlerVersion` érték a kiterjesztést.  A tényleges directory lehet például `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Végrehajtásakor a `commandToExecute` parancsot, a bővítmény beállítja ezt a könyvtárat (például `...\Downloads\2`), az aktuális munkakönyvtár. Ez a folyamat lehetővé teszi, hogy a relatív elérési útjai keresse meg a letöltött fájlok használata a `fileURIs` tulajdonság. Tekintse meg az alábbi táblázatban példák.

Mivel a letöltés abszolút elérési útját idővel változhat, akkor célszerűbb, hogy a parancsfájl/fájl relatív elérési utakat a a `commandToExecute` string, amikor csak lehetséges. Példa:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Után az első URI-szegmens adatért a letöltött fájlok elérési útra vonatkozó információt a `fileUris` tulajdonságra a listában.  Az alábbi táblázatban látható, letöltött fájlok letöltési alkönyvtárak struktúráját tükrözi, hogy be vannak leképezve a `fileUris` értékeket.  

#### <a name="examples-of-downloaded-files"></a>Példák a letöltött fájlok

| FileUris URI-t | Relatív letöltött helye | Abszolút helye letöltött <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> az abszolút elérési utak módosítása a virtuális gép, de nem található a CustomScript bővítménnyel egyetlen végrehajtásának élettartama során.

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Emellett a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).
