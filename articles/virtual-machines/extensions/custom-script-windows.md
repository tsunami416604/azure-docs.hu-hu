---
title: A Windows Azure egyéni parancsprogramok futtatására szolgáló bővítmény |} Microsoft Docs
description: Az egyéni parancsprogramok futtatására szolgáló bővítmény használatával Windows virtuális gép konfigurációs feladatok automatizálásához
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/24/2018
ms.author: danis
ms.openlocfilehash: 34c16b686a50994862bef14cefec1a4799a343c4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="custom-script-extension-for-windows"></a>A Windows egyéni parancsprogramok futtatására szolgáló bővítmény

Az egyéni parancsprogramok futtatására szolgáló bővítmény és hajtanak végre a parancsfájl az Azure virtuális gépeken. A bővítmény az üzembe helyezést követő konfiguráció, szoftvertelepítés, illetve bármely konfigurációs/felügyeleti feladat végrehajtása során hasznos. A szkriptek az Azure Storage-ból vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében. Az egyéni szkriptek bővítménye az Azure Resource Manager-sablonokkal van integrálva, és az Azure CLI, a PowerShell, az Azure Portal vagy az Azure Virtual Machine REST API használatával is futtatható.

Ez a dokumentum részletesen az egyéni parancsprogramok futtatására szolgáló bővítmény használatával az Azure PowerShell modul, Azure Resource Manager-sablonok és a Windows rendszer hibaelhárítási részletek használata.

## <a name="prerequisites"></a>Előfeltételek

> [!NOTE]  
> Nem használhatja egyéni parancsprogramok futtatására szolgáló bővítmény futtatásához frissítés-AzureRmVM operátormetódusának a paraméterével ugyanazon VM óta, várjon a saját magát.  
>   
> 

### <a name="operating-system"></a>Operációs rendszer

Az egyéni parancsprogramok futtatására szolgáló bővítmény Linux fog futni, a támogatott bővítmény kiterjesztést rendszerbeli, további információkért lásd: a [cikk](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Parancsfájl helyét

A bővítmény használhatja az Azure Blob storage hitelesítő adatok Azure Blob-tároló elérésére használhat. Alternatív megoldásként a parancsfájl helyét lehet bármely where, mindaddig, amíg a virtuális gép irányíthatja a végpontot, például a github webhelyen, a belső fájlkiszolgáló stb.


### <a name="internet-connectivity"></a>Internetkapcsolat
Ha le kell töltenie a külsőleg a Githubból vagy az Azure Storage például egy parancsfájlt, majd további tűzfal/hálózati biztonsági csoportjának portokat kell megnyitni. Például ha a parancsfájl az Azure Storage található, engedélyezheti a Azure NSG szolgáltatás címkék segítségével érhető [tárolási](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#service-tags).

Ha a parancsfájl egy helyi kiszolgálón, akkor lehet szükség további tűzfal/hálózati biztonsági csoport portokat kell megnyitni.

### <a name="tips-and-tricks"></a>tippek és trükkök
* Ehhez a kiterjesztéshez legmagasabb hibaaránya okozza-e a parancsfájl, a parancsfájl hiba nélkül fut teszt szintaktikai hibákat, és is be további, a parancsfájl használatával könnyebben találja, ahol sikertelen bejelentkezés.
* Az idempotent, amelyek parancsfájlokat írhat, az beszerzése futásuk egynél többször véletlenül, ha rendszer módosítása nem okoz.
* Győződjön meg arról, a parancsfájlok nem igényelnek felhasználói beavatkozást futtatásakor.
* A parancsfájl futtatásához engedélyezett 90 perc, semmit hosszabb ideig sikertelen nyújtását a bővítmény eredményez.
* A parancsfájl belül újraindítások ne helyezze, ennek hatására problémákat más kiterjesztésű fájl, amelyek telepítése folyamatban van, és utáni újraindítás, a bővítmény nem fog az újraindítás után. 
* Ha olyan parancsfájlt, amely hatására a számítógép újraindítása, alkalmazásokat telepíteni, és futtassa a parancsfájlok stb. Az újraindítás a Windows ütemezett feladat, vagy például vagy a DSC, Chef, Puppet, bővítmények eszközökkel kell ütemezni.
* A kiterjesztés csak akkor fog futni egy parancsfájl egyszer, ha szeretné parancsprogram futtatása minden rendszerindításkor, akkor a bővítmény használatához a Windows ütemezett feladat létrehozásához szükséges.
* Ha szeretne ütemezni, amikor a parancsprogram futni fog, a bővítmény Windows ütemezett feladat létrehozásához kell használnia. 
* Csak akkor jelenik meg a "átviteli" bővítmény állapotát az Azure-portálon vagy a parancssori felület, a parancsfájl futása közben. Ha azt szeretné, hogy a futó parancsfájl gyakoribb állapotfrissítéseit, szüksége lesz a saját megoldás létrehozásához.
* Egyéni parancsprogramok futtatására szolgáló bővítmény natív módon támogatja a proxykiszolgálót, azonban a fájl adatátviteli eszközzel, mint a parancsfájl-proxy kiszolgálók támogató *Curl* 
* Ügyeljen a nem alapértelmezett directory-helyeken a parancsfájlok vagy parancsok hivatkozhatnak, kell kezelni a programot.


## <a name="extension-schema"></a>Bővítményséma

Az egyéni parancsprogramok futtatására szolgáló bővítmény konfigurációja határozza meg, többek között a parancsfájl helyét és a futtatandó parancsot. Ebben a konfigurációban tárolható konfigurációs fájlok, a parancssorban adja meg azt, vagy adja meg az Azure Resource Manager-sablonok. 

Egy védett konfigurációban, amely titkosított, és csak a virtuális gépen belüli visszafejteni bizalmas adatokat is tárolhatja. A védett konfiguráció akkor hasznos, ha a végrehajtási parancs tartalmazza a titkos kulcsokat például a jelszót.

Legyen bizalmas adatokat a rendszer ezeket az elemeket, és a bővítmények védett beállítás konfigurációjában megadott. Az Azure Virtuálisgép-bővítmény védett beállítás adatokat titkosít, és csak visszafejti a cél virtuális gépen.

```json
{
    "apiVersion": "2015-06-15",
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
            ]
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```
**Megjegyzés:** -kiterjesztés csak egy verziója is telepíthető egy virtuális Gépet egy időben, egyéni parancsfájl megadásával kétszer szerepel ugyanaz a Resource Manager-sablon, az azonos virtuális gép sikertelen lesz. 

### <a name="property-values"></a>A tulajdonság értékek

| Name (Név) | Érték / – példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | dátum |
| publisher | Microsoft.Compute | karakterlánc |
| type | CustomScriptExtension | karakterlánc |
| typeHandlerVersion | 1.9 | int |
| fileUris (például) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | tömb |
| commandToExecute (például) | PowerShell - ExecutionPolicy Unrestricted - fájl konfigurálása zene-app.ps1 | karakterlánc |
| storageAccountName (például) | examplestorageacct | karakterlánc |
| storageAccountKey (például) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | karakterlánc |

>[!NOTE]
>A tulajdonságnevek megkülönböztetik a kis-és nagybetűket. Alkalmazástelepítéssel kapcsolatos problémák elkerülése érdekében használja a nevét itt látható módon.

#### <a name="property-value-details"></a>A tulajdonság értékének részletei
 * `commandToExecute`: (**szükséges**, karakterlánc) a belépési pont parancsfájl végrehajtására. Ehelyett használja ebben a mezőben, ha a parancs tartalmazza a titkos kulcsok, például jelszavak, vagy a fileUris különbözőnek számítanak.
* `fileUris`: (nem kötelező, karakterlánc-tömbben) URL-címéből (oka) t, le kell tölteni.
* `storageAccountName`: (nem kötelező, karakterlánc) storage-fiók nevét. Ha megadja a tároló hitelesítő adatait, az összes `fileUris` az Azure BLOB URL-címeket kell lennie.
* `storageAccountKey`: (nem kötelező, karakterlánc) storage-fiók hozzáférési kulcsának

A következő értékeket a nyilvános vagy védett beállításokat lehet megadni, a bővítmény elutasítják beállításra, amennyiben az alábbi értékeket a nyilvános és a védett beállításai megfelelőek-e.
* `commandToExecute`

Hibakeresés, de hasznos lehet, hogy nyilvános beállítások használata erősen ajánlott védett beállítások használata.

Nyilvános beállításai küldése nyílt szövegben a virtuális gép, ha a parancsfájl végrehajtása a.  Védett beállítások csak az Azure és a virtuális gép ismert kulccsal titkosított. A beállítások mentése a virtuális Gépet, a küldés, vagyis ha a beállítások titkosított mentett titkosított a virtuális Gépen. A tanúsítvány használatával fejthetők vissza a titkosított értékek tárolja a virtuális Gépre, és beállításokat (ha szükséges) futásidőben visszafejtésére szolgáló jelszó.

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonok is telepíthető. Az előző szakaszban ismertetett JSON-séma segítségével az Azure Resource Manager-sablonok az egyéni parancsprogramok futtatására szolgáló bővítmény futtatása az Azure Resource Manager sablon üzembe helyezése során. Itt található, amely tartalmazza az egyéni parancsprogramok futtatására szolgáló bővítmény mintasablon [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="powershell-deployment"></a>PowerShell telepítése

A `Set-AzureRmVMCustomScriptExtension` parancs használható az egyéni parancsprogramok futtatására szolgáló bővítmény hozzáadása egy meglévő virtuális gépet. További információkért lásd: [Set-AzureRmVMCustomScriptExtension ](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension).
```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location myLocation `
    -FileUri myURL `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```
## <a name="further-examples"></a>További példák

### <a name="using-multiple-script"></a>Több parancsfájl használata
Ebben a példában három parancsfájlok alapján állítja össze a kiszolgáló, a "commandToExecute" hívások az első parancsfájlt, majd rendelkeznie, hogy a többi hívása beállítások, például rendelkezhet egy fő parancsfájlt, amely szabályozza a végrehajtási, a jobb oldali hiba: kezelési, a naplózás vagy az állapot felügyeletéig.

```powershell

$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$Settings = @{"fileUris" = $fileUri};

$storageaccname = "xxxxxxx"
$storagekey = "1234ABCD"
$ProtectedSettings = @{"storageAccountName" = $storageaccname; "storageAccountKey" = $storagekey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzureRmVMExtension -ResourceGroupName myRG `
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "buildserver1" ` 
    -Publisher "Microsoft.Compute" ` 
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" ` 
    -Settings $Settings ` 
    -ProtectedSettings $ProtectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Parancsfájlok futtatásához helyi megosztásból
Ebben a példában, érdemes lehet egy helyi SMB-kiszolgálón használja a parancsfájl helyéhez vegye figyelembe, nem kell átadni egy másik egyéb beállításokat, kivéve *commandToExecute*.

```powershell
$ProtectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};
 
Set-AzureRmVMExtension -ResourceGroupName myRG 
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "serverUpdate" 
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" `
    -ProtectedSettings $ProtectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Egyéni parancsfájl egynél többször futtatása a parancssori felület
Ha az egyéni parancsprogramok futtatására szolgáló bővítmény többször futtatni szeretné, csak ehhez ezen feltételek alapján:
1. A bővítmény "Név" paraméternek megegyezik a korábbi központi telepítés a bővítményt.
2. A konfiguráció vagy a parancs fog újbóli végrehajtása nem kell frissíteni, például hozzáadhatja a dinamikus tulajdonság a parancshoz, például egy időbélyegző. 

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Bővítmény központi telepítések állapotára vonatkozó lehet adatokat beolvasni az Azure-portálon, és az Azure PowerShell modul segítségével. A megadott virtuális gépek bővítmények központi telepítési állapotának megtekintéséhez futtassa a következő parancsot:

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

A cél virtuális gépen a következő könyvtárban található fájlok bővítmény végrehajtás kimenetének van bejelentkezve.
```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

A megadott fájlok letöltése a cél virtuális gépen a következő könyvtárba.
```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
Ha `<n>` decimális egész szám, amely módosíthatja a bővítmény végrehajtások közötti.  A `1.*` értéke megegyezik a tényleges, jelenlegi `typeHandlerVersion` érték a kiterjesztést.  A tényleges directory lehet például `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Végrehajtásakor a `commandToExecute` parancs, a bővítmény állítja be a könyvtár (pl. `...\Downloads\2`), az aktuális munkakönyvtárban. Ez lehetővé teszi, hogy keresse meg a letöltött fájlokat relatív elérési utak használatát a `fileURIs` tulajdonság. Lásd az alábbi táblázatban a példákat.

Mivel a abszolút letöltési mappa elérési útját idővel változhatnak, ajánlott, hogy a fájl relatív parancsfájl elérési utak a a `commandToExecute` karakterlánc, amikor csak lehetséges. Példa:
```json
    "commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Útvonal-információinak után az első URI-szegmens őrzi meg a letöltött fájlok a `fileUris` tulajdonságlistát.  Ahogy az alábbi táblázatban is látható, letöltött fájlok letöltési alkönyvtárak szerkezete megfelelően be van leképezve a `fileUris` értékeket.  

#### <a name="examples-of-downloaded-files"></a>A letöltött fájlok példák

| URI-azonosító található fileUris | A letöltött helyzetéhez viszonyítva | Abszolút helye letöltött * |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\* Mint a fenti abszolút elérési útjainak módosítsa a virtuális gép, de nem esik a CustomScript bővítménnyel egyetlen végrehajtásának életciklusa alatt.

### <a name="support"></a>Támogatás

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a a [MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza ki a Get-támogatási szolgálathoz. Támogatja az Azure használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).
