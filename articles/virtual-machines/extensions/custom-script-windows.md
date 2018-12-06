---
title: A Windows Azure egyéni szkriptek futtatására szolgáló bővítmény |} A Microsoft Docs
description: Az egyéni szkriptbővítmény használatával Windows virtuális gép konfigurációs feladatok automatizálása
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2018
ms.author: roiyz
ms.openlocfilehash: 1370f541f8913d86db948a3165d6660a8cd66528
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963504"
---
# <a name="custom-script-extension-for-windows"></a>A Windows egyéni szkriptek futtatására szolgáló bővítmény

Az egyéni Szkriptbővítmény letölti és végrehajtja a parancsfájlok Azure-beli virtuális gépeken. A bővítmény az üzembe helyezést követő konfiguráció, szoftvertelepítés, illetve bármely konfigurációs/felügyeleti feladat végrehajtása során hasznos. A szkriptek az Azure Storage-ból vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében. Az egyéni szkriptek bővítménye az Azure Resource Manager-sablonokkal van integrálva, és az Azure CLI, a PowerShell, az Azure Portal vagy az Azure Virtual Machine REST API használatával is futtatható.

Ez a dokumentum részletesen használata az egyéni Szkriptbővítmény használatával, az Azure PowerShell-modul, az Azure Resource Manager-sablonok és hibaelhárítási lépéseket a Windows rendszerek részleteit.

## <a name="prerequisites"></a>Előfeltételek

> [!NOTE]  
> Ne használja az Update-AzureRmVM futtatható a virtuális géppel azonos a paraméterként, mert maga a vár az egyéni szkriptek futtatására szolgáló bővítmény.  
>   
> 

### <a name="operating-system"></a>Operációs rendszer

Az egyéni Szkriptbővítmény Linux fog futni a támogatott bővítmény bővítmény nyílt forráskódú, további információkért lásd: Ez [cikk](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>A parancsprogram helye

A bővítmény használhatja az Azure Blob storage hitelesítő adatai az Azure Blob storage elérésére használhat. Másik lehetőségként a parancsprogram helyét bárhol lehet, mindaddig, amíg a virtuális gép irányíthatja a végpontot, például a GitHub, a belső fájlkiszolgáló stb.


### <a name="internet-connectivity"></a>Internetkapcsolat
Ha le kell töltenie egy parancsfájlt kívülről például a GitHub vagy az Azure Storage, majd további tűzfal/hálózati biztonsági csoportra portokat kell megnyitni. Például ha a parancsfájl található az Azure Storage szolgáltatásban, engedélyezheti a hozzáférés Azure NSG szolgáltatás címkék használatával [tárolási](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Ha a parancsfájl egy helyi kiszolgálón, akkor továbbra is szükség lehet további tűzfal/hálózati biztonsági csoport portokat kell megnyitni.

### <a name="tips-and-tricks"></a>tippek és trükkök
* Ehhez a kiterjesztéshez legmagasabb Hibaarány miatt a parancsfájl tesztelési hiba nélkül fut a parancsfájl szintaxishibáinak és is helyezheti további jelentkezik be, hogy könnyebben megtalálja, ahol nem sikerült a parancsfájl.
* Parancsfájlokat írhat, amelyek idempotensek, így az első futás egynél többször véletlenül, ha módosításokat a rendszer nem okoz.
* Győződjön meg arról, a parancsfájlok futtatásakor. nem szükséges felhasználói bevitel.
* A parancsfájl futtatása engedélyezett 90 perc, semmit hosszabb egy sikertelen a bővítmény kiépítése eredményez.
* A parancsfájl belül újraindítások ne helyezzen, ez a művelet lesz okozhat problémát más bővítményeket, amelyek telepítése folyamatban van. Utáni újraindítás, a bővítmény nem továbbra is az újraindítás után. 
* Ha egy parancsfájl, amely hatására az újraindítás, alkalmazásokat telepíthet, és futtassa a parancsfájlok stb. Az újraindítás a Windows ütemezett feladat használatával, vagy a DSC, vagy a Chef, Puppet, bővítmények ütemezheti.
* A bővítmény csak fut egy parancsfájl egyszer, ha azt szeretné, parancsfájl futtatása a naplózásra kerül minden rendszerindításkor, akkor a bővítmény használata a Windows ütemezett feladat létrehozásához szükséges.
* Ha azt szeretné, meghatározhatja, mikor egy parancsprogram futtatása, a bővítmény Windows ütemezett feladat létrehozásához használjon. 
* A szkript fut, amikor egy "átviteli" bővítmény állapotát az Azure Portalon vagy a parancssori felület csak akkor jelenik meg. Ha azt szeretné, hogy a futó parancsfájl gyakoribb ügyfélállapot-frissítés, szüksége saját megoldást hozhat létre.
* Egyéni szkriptek futtatására szolgáló bővítmény nem támogatja natív módon proxykiszolgálókat, azonban egy fájlátviteli eszköz, amely támogatja a proxykiszolgálók a parancsfájlban, például használhatja *Curl* 
* Nem alapértelmezett vegye figyelembe, hogy a parancsfájlok vagy parancsok függhet, directory-hely rendelkezik logikai a helyzet kezeléséhez.


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
**Megjegyzés:** -bővítmény csak egy verziója is telepíthető egy virtuális Gépet egy időben, egyéni parancsfájl kétszer ugyanazt a Resource Manager-sablont a megadásával, a sikertelen lesz, ugyanazon a virtuális Gépen. 

### <a name="property-values"></a>Tulajdonságok értékei

| Name (Név) | Érték és példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | dátum |
| publisher | Microsoft.Compute | sztring |
| type | CustomScriptExtension | sztring |
| typeHandlerVersion | 1.9 | int |
| fileUris (például:) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | tömb |
| időbélyegző (például:) | 123456789 | 32 bites egész szám |
| commandToExecute (például:) | PowerShell - ExecutionPolicy Unrestricted - fájl konfigurálása music-app.ps1 | sztring |
| storageAccountName (például:) | examplestorageacct | sztring |
| storageAccountKey (például:) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | sztring |

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

Nyilvános beállításokat a virtuális gép, ahol a parancsfájl végrehajtása szövegként érkeznek.  Védett beállítások vannak titkosítva, csak az Azure és a virtuális gép ismert kulcsot használ. A beállítások lesznek mentve a virtuális géphez, mivel lettek küldve, vagyis ha a beállítások titkosított menti őket titkosított a virtuális gépen. A tanúsítvány használatával fejthetők vissza a titkosított értékek tárolja a virtuális gépen, és fejti vissza a futásidejű beállításokat (ha szükséges).

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. A JSON-séma, amely az előző szakaszban részletes az egyéni szkriptek futtatására szolgáló bővítmény futtatása során egy Azure Resource Manager-sablon üzembe helyezése Azure Resource Manager-sablon is használható. A következő példák bemutatják, hogyan használja az egyéni szkriptek futtatására szolgáló bővítmény:

* [Oktatóanyag: Virtuálisgép-bővítmények az Azure Resource Manager-sablonok üzembe helyezése](../../azure-resource-manager/resource-manager-tutorial-deploy-vm-extensions.md)
* [A Windows és az Azure SQL Database két szintű alkalmazás üzembe helyezése](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>PowerShell környezetben végzett telepítés

A `Set-AzureRmVMCustomScriptExtension` parancs használható az egyéni szkriptek futtatására szolgáló bővítmény hozzáadása egy meglévő virtuális gépet. További információkért lásd: [Set-AzureRmVMCustomScriptExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmcustomscriptextension).

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
Ebben a példában három hozhat létre a kiszolgáló, a "commandToExecute" meghívja az első szkript, akkor van lehetőség, hogy a többi hívása által használt parancsfájlok van, például rendelkezhet egy fő parancsfájlt, amely szabályozza a végrehajtási, a jobb oldali hibával kezelési, a naplózás és állapot-felügyeleti.

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

### <a name="running-scripts-from-a-local-share"></a>Parancsfájlok futtatása egy helyi meghajtóról
Ebben a példában érdemes helyi SMB-kiszolgáló használata a parancsfájl helyét vegye figyelembe, nem kell átadni egy másik egyéb beállításokat, kivéve a *commandToExecute*.

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

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Egyéni parancsfájl egynél többször futtatása a parancssori felülettel
Ha azt szeretné, az egyéni szkriptek futtatására szolgáló bővítmény futtatása egynél többször, ez a művelet ezen feltételek csak teheti meg:
1. A bővítmény "Név" paraméternek megegyezik a bővítmény a korábbi telepítésben.
2. A parancs nem hajtható újra egyéb konfigurációs kell frissíteni. Hozzáadhat egy dinamikus tulajdonságban parancsba, például egy időbélyegző.

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Bővítmény központi telepítések állapotát lehet adatokat beolvasni az Azure Portalról, és az Azure PowerShell-modul segítségével. Adott Virtuálisgép-bővítmények központi telepítési állapotának megtekintéséhez futtassa a következő parancsot:

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Bővítmény végrehajtás kimenetének a cél virtuális gépen a következő könyvtárban található fájlokat a rendszer naplózza.
```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

A megadott fájlok letöltése a cél virtuális gépen a következő könyvtárba.
```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
ahol `<n>` decimális egész szám, amely előfordulhat, hogy módosítsa a bővítmény végrehajtások közötti.  A `1.*` értéke megegyezik a tényleges, jelenlegi `typeHandlerVersion` érték a kiterjesztést.  A tényleges directory lehet például `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Végrehajtásakor a `commandToExecute` parancsot, a bővítmény beállítja ezt a könyvtárat (például `...\Downloads\2`), az aktuális munkakönyvtár. Ez a folyamat lehetővé teszi, hogy a relatív elérési útjai keresse meg a letöltött fájlok használata a `fileURIs` tulajdonság. Tekintse meg az alábbi táblázatban példák.

Mivel a letöltés abszolút elérési útját idővel változhat, akkor célszerűbb, hogy a parancsfájl/fájl relatív elérési utakat a a `commandToExecute` string, amikor csak lehetséges. Példa:
```json
    "commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Után az első URI-szegmens őrizzük meg a letöltött fájlok elérési útra vonatkozó információt a `fileUris` tulajdonságra a listában.  Az alábbi táblázatban látható, letöltött fájlok letöltési alkönyvtárak struktúráját tükrözi, hogy be vannak leképezve a `fileUris` értékeket.  

#### <a name="examples-of-downloaded-files"></a>Példák a letöltött fájlok

| FileUris URI-t | Relatív letöltött helye | Abszolút letöltött hely * |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\* Mint a fenti az abszolút elérési utak módosítása élettartama során a virtuális gép, de nem a CustomScript bővítménnyel egyetlen végrehajtásának belül.

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Emellett a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).
