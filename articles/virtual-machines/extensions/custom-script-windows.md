---
title: Azure egyéni parancsfájl-bővítmény Windowshoz
description: A Windows virtuális gép konfigurációs feladatainak automatizálása az Egyéni parancsfájl bővítmény használatával
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: robreed
ms.openlocfilehash: 698fab470cdc8b8d04fa4319fd71c31b58d1c5a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066880"
---
# <a name="custom-script-extension-for-windows"></a>Egyéniszkript-bővítmény Windows rendszerre

Az egyéni parancsfájl-bővítmény letölti és végrehajtja a parancsfájlokat az Azure virtuális gépeken. Ez a bővítmény a telepítés utáni konfigurációhoz, a szoftvertelepítéshez vagy bármely más konfigurációs vagy felügyeleti feladathoz hasznos. A szkriptek az Azure Storage-ból vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében. Az egyéni parancsfájl-bővítmény integrálható az Azure Resource Manager-sablonokkal, és futtatható az Azure CLI, a PowerShell, az Azure Portal vagy az Azure Virtual Machine REST API használatával.

Ez a dokumentum részletezi, hogyan használhatja az egyéni parancsfájl-bővítményt az Azure PowerShell-modul, az Azure Resource Manager-sablonok és a Windows-rendszerek hibaelhárítási lépései használatával.

## <a name="prerequisites"></a>Előfeltételek

> [!NOTE]  
> Ne használja az egyéni parancsfájl-bővítményt az Update-AzVM futtatásához ugyanazzal a virtuális géptel, mint a paramétere, mivel megfog várni magára.  

### <a name="operating-system"></a>Operációs rendszer

A Windows egyéni parancsfájl-bővítménye a bővítmény által támogatott operációs rendszereken fog futni, további információkért tekintse meg ezt az [Azure Extension által támogatott operációs rendszert.](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems)

### <a name="script-location"></a>Parancsfájl helye

Beállíthatja, hogy a bővítmény az Azure Blob storage hitelesítő adatait használja az Azure Blob storage eléréséhez. A parancsfájl helye bárhol lehet, mindaddig, amíg a virtuális gép képes útvonalat, hogy a végpont, például a GitHub vagy egy belső fájlkiszolgáló.

### <a name="internet-connectivity"></a>Internetkapcsolat

Ha például a GitHubról vagy az Azure Storage-ból kell letöltenie egy parancsfájlt, akkor további tűzfal- és hálózati biztonságicsoport-portokat kell megnyitni. Ha például a parancsfájl az Azure Storage-ban található, engedélyezheti a hozzáférést az Azure NSG szolgáltatáscímkék [tárolóhoz](../../virtual-network/security-overview.md#service-tags)használatával.

Ha a parancsfájl helyi kiszolgálón van, akkor további tűzfalra lehet szüksége, és a hálózati biztonsági csoport portjait meg kell nyitni.

### <a name="tips-and-tricks"></a>Tippek és trükkök

* A bővítmény legnagyobb hibaaránya a parancsfájl szintaktikai hibái miatt van, tesztelje a parancsfájl hiba nélkül fut, és további bejelentkezést is betesz a parancsfájlba, hogy könnyebben megtalálja, hol nem sikerült.
* Idempotent parancsfájlok írása. Ez biztosítja, hogy ha véletlenül újra futnak, az ne okozzon rendszerváltozásokat.
* Gondoskodjon arról, hogy a szkriptek futtatásához ne legyen szükség felhasználói beavatkozásra.
* A szkript futtatásának engedélyezett időtartama 90 perc. Ha egy szkript ennél tovább fut, az a bővítmény üzembe helyezésének meghiúsulását eredményezi.
* A szkript ne váltson ki újraindításokat, mert ez a művelet problémákat okoz a telepítés alatt álló többi bővítményben. Újraindítás után a bővítmény nem fog tovább működni.
* Ha olyan parancsfájllal rendelkezik, amely újraindítást okoz, majd alkalmazásokat telepít és parancsfájlokat futtat, ütemezheti az újraindítást egy Windows ütemezett feladat használatával, vagy használhat olyan eszközöket, mint a DSC, a Chef vagy a Puppet kiterjesztések.
* A bővítmény egy szkriptet csak egyszer futtat. Ha egy szkriptet minden indításkor futtatni szeretne, létre kell hoznia egy ütemezett Windows-feladatot a bővítménnyel.
* Ha ütemezni szeretné, hogy mikor fusson egy szkript, hozzon létre egy ütemezett Windows-feladatot a bővítménnyel.
* Amikor a szkript fut, az Azure Portalon vagy a CLI-n a bővítmény „átmeneti” állapotát fogja látni. Ha gyakoribb állapotfrissítést szeretne egy futó szkripthez, létre kell hoznia a saját megoldását.
* Az egyéni parancsfájl-kiterjesztés nem támogatja natív módon a proxykiszolgálókat, azonban használhat olyan fájlátviteli eszközt, amely támogatja a parancsfájlon belüli proxykiszolgálókat, például *a Curl*
* Vegye figyelembe, hogy vannak olyan nem alapértelmezett könyvtárhelyek, amelyekre a szkriptjei és a parancsai támaszkodhatnak, és amelyek rendelkeznek a helyzet kezeléséhez szükséges logikával.
* Az egyéni parancsfájlkiterjesztés a LocalSystem fiók alatt fog futni

## <a name="extension-schema"></a>Bővítményséma

Az egyéni parancsfájl-bővítmény konfigurációja olyan dolgokat határoz meg, mint a parancsfájl helye és a futtatandó parancs. Ezt a konfigurációt tárolhatja konfigurációs fájlokban, megadhatja a parancssorban, vagy megadhatja egy Azure Resource Manager-sablonban.

A bizalmas adatokat védett konfigurációban tárolhatja, amely titkosított, és csak a virtuális gépen belül van visszafejtve. A védett konfiguráció akkor hasznos, ha a végrehajtási parancs titkos kulcsokat, például egy jelszót tartalmaz.

Ezeket az elemeket bizalmas adatként kell kezelni, és a bővítmények védett beállítási konfigurációjában meg kell adni. Az Azure VM-bővítmény védett beállítási adatai titkosítva vannak, és csak a cél virtuális gépen fejtik vissza.

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "virtualMachineName/config-app",
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
        "typeHandlerVersion": "1.10",
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
            "storageAccountKey": "myStorageAccountKey",
            "managedIdentity" : {}
        }
    }
}
```

> [!NOTE]
> managedIdentity tulajdonság **nem** használható a storageAccountName vagy storageAccountKey tulajdonságokkal együtt

> [!NOTE]
> A bővítménynek csak egy verziója telepíthető egy virtuális gépre egy adott időpontban, és az egyéni parancsfájl kétszeri megadása ugyanabban az Erőforrás-kezelő sablonban ugyanahhoz a virtuális géphez sikertelen lesz.

> [!NOTE]
> Ezt a sémát használhatjuk a VirtualMachine erőforráson belül vagy önálló erőforrásként. Az erőforrás nevének "virtualMachineName/extensionName" formátumban kell lennie, ha ezt a bővítményt önálló erőforrásként használja az ARM sablonban. 

### <a name="property-values"></a>Tulajdonság értékek

| Név | Érték / Példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | dátum |
| közzétevő | Microsoft.Compute | sztring |
| type | CustomScriptExtension | sztring |
| typeHandlerVersion | 1.10 | int |
| fileUris (pl. | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | tömb |
| időbélyeg (pl. | 123456789 | 32 bites egész szám |
| commandToExecute (pl. | powershell -ExecutionPolicy Korlátlan -Fájlkonfigurálás-zene-app.ps1 | sztring |
| storageAccountName (pl. | példatárolásacct | sztring |
| storageAccountKey (pl. | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg=== | sztring |
| managedIdentity (pl. | { } vagy { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" } vagy { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | json objektum |

>[!NOTE]
>Ezek a tulajdonságnevek a kis- és nagybetűket nem érzékenyékelik. A telepítési problémák elkerülése érdekében használja az itt látható neveket.

#### <a name="property-value-details"></a>Ingatlan értékének részletei

* `commandToExecute`: (**kötelező,** karakterlánc) a végrehajtandó belépési pont parancsfájl. Akkor használja ezt a mezőt, ha a parancs titkos kulcsokat, például jelszavakat tartalmaz, vagy ha a fileUri-k bizalmasak.
* `fileUris`: (nem kötelező, karakterlánctömb) a letöltendő fájl(ok) URL-címei.
* `timestamp`(nem kötelező, 32 bites egész szám) csak a parancsfájl újrafuttatását szeretné kiváltani a mező értékének módosításával.  Bármely egész érték elfogadható; csak az előző értéknél eltérő lehet.
* `storageAccountName`: (nem kötelező, karakterlánc) a tárfiók neve. Ha megadja a tárolási `fileUris` hitelesítő adatokat, az Összes nek URL-nek kell lennie az Azure Blobs számára.
* `storageAccountKey`: (nem kötelező, karakterlánc) a tárfiók hozzáférési kulcsa
* `managedIdentity`: (nem kötelező, json objektum) a [felügyelt identitás](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) fájl(ok) letöltéséhez
  * `clientId`: (nem kötelező, karakterlánc) a felügyelt identitás ügyfélazonosítója
  * `objectId`: (nem kötelező, karakterlánc) a felügyelt identitás objektumazonosítója

A következő értékek nyilvános vagy védett beállításokban állíthatók be, a bővítmény elutasít minden olyan konfigurációt, ahol az alábbi értékek nyilvános és védett beállításokban is be vannak állítva.

* `commandToExecute`

A nyilvános beállítások használata hasznos lehet a hibakereséshez, de ajánlott védett beállításokat használni.

A nyilvános beállításokat a rendszer tiszta szövegként küldi el a virtuális gépnek, ahol a parancsfájl végrehajtása megtörténik.  A védett beállítások titkosítva vannak egy csak az Azure és a virtuális gép által ismert kulcs használatával. A beállításokat a rendszer a virtuális gépre menti, ahogy elküldésük kor, azaz ha a beállítások titkosítva vannak, akkor a virtuális gép titkosított anam-re kerül. A titkosított értékek visszafejtéséhez használt tanúsítvány a virtuális gép tárolja, és a beállítások visszafejtésére (ha szükséges) futásidőben.

####  <a name="property-managedidentity"></a>Tulajdonság: managedIdentity

A CustomScript (1.10-es verziótól) támogatja a [felügyelt identitásletöltést](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) a "fileUris" beállításban megadott URL-címekről. Lehetővé teszi a CustomScript számára az Azure Storage privát blobjainak vagy tárolóinak elérését anélkül, hogy a felhasználónak olyan titkos kulcsokat kellene átadnia, mint a SAS-jogkivonatok vagy a tárfiók kulcsai.

A szolgáltatás használatához a felhasználónak hozzá kell adnia egy [rendszer-hozzárendelt](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity) vagy [felhasználó által hozzárendelt](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-user-assigned-identity) identitást a virtuális géphez vagy a VMSS-hez, ahol a CustomScript várhatóan futni fog, és [biztosítania kell a felügyelt identitás hozzáférést az Azure Storage-tárolóhoz vagy -blobhoz.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage#grant-access)

A rendszer által hozzárendelt identitás használata a cél vm/VMSS, állítsa be a "managedidentity" mező egy üres json-objektum. 

> Példa:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

A felhasználó által hozzárendelt identitás használata a cél vm/VMSS, konfigurálja a "managedidentity" mező az ügyfélazonosító vagy a felügyelt identitás objektumazonosítóját.

> Példák:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> managedIdentity tulajdonság **nem** használható a storageAccountName vagy storageAccountKey tulajdonságokkal együtt

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonokkal telepíthetők. A JSON-séma, amely az előző szakaszban részletezett egy Azure Resource Manager sablonban használható az egyéni parancsfájl-bővítmény futtatásához a központi telepítés során. A következő minták bemutatják, hogyan kell használni az egyéni parancsfájl bővítményt:

* [Oktatóanyag: Virtuális gépi bővítmények üzembe helyezése Azure Resource Manager-sablonokkal](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
* [Kétrétegű alkalmazás telepítése Windows és Azure SQL DB rendszeren](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>PowerShell-telepítés

A `Set-AzVMCustomScriptExtension` parancs segítségével hozzáadhatja az egyéni parancsfájl-bővítményt egy meglévő virtuális géphez. További információ: [Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

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

Ebben a példában három parancsfájlt használ a kiszolgáló létrehozásához. A **commandToExecute** meghívja az első parancsfájlt, majd lehetősége van arra, hogy a többieket hogyan hívják. Például lehet egy fő parancsfájl, amely szabályozza a végrehajtást, a megfelelő hibakezelés, naplózás és állapotkezelés. A parancsfájlok letöltése a helyi számítógépre futásra történik. Például `1_Add_Tools.ps1` a parancsfájlhoz `.\2_Add_Features.ps1` való hozzáadással hívja meg a hívást, `$settings` `2_Add_Features.ps1` és ismételje meg ezt a folyamatot a alkalmazásban megadott többi parancsfájlesetében.

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
    -TypeHandlerVersion "1.10" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Parancsfájlok futtatása helyi megosztásról

Ebben a példában előfordulhat, hogy egy helyi SMB-kiszolgálót szeretne használni a parancsfájl helyéhez. Ezzel nem kell más beállításokat megadnia, kivéve **a commandToExecute parancsot.**

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Egyéni parancsfájl többszöri futtatása CLI-vel

Ha az egyéni parancsfájlbővítményt többször szeretné futtatni, ezt a műveletet csak a következő feltételek mellett teheti meg:

* A bővítmény **név** paramétere megegyezik a bővítmény előző központi telepítésével.
* Frissítse a konfigurációt, különben a parancs nem lesz újra végrehajtva. Hozzáadhat egy dinamikus tulajdonságot a parancshoz, például egy időbélyeget.

Másik lehetőségként beállíthatja a [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) tulajdonságot **true**értékre.

### <a name="using-invoke-webrequest"></a>Az Invoke-WebRequest használata

Ha az [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) alkalmazást használja a parancsfájlban, meg kell adnia a paramétert, `-UseBasicParsing` különben a következő hibaüzenet jelenik meg a részletes állapot ellenőrzésekor:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

Az egyéni parancsfájl-bővítmény méretezési készleten történő üzembe helyezéséhez olvassa el az [Add-AzVmssExtension című témakört.](https://docs.microsoft.com/powershell/module/az.compute/add-azvmssextension?view=azps-3.3.0)

## <a name="classic-vms"></a>A klasszikus virtuális gépeket

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Az egyéni parancsfájl-bővítmény üzembe helyezéséhez a klasszikus virtuális gépeken használhatja az Azure Portalon vagy a klasszikus Azure PowerShell-parancsmagokat.

### <a name="azure-portal"></a>Azure portál

Keresse meg a klasszikus virtuálisgép-erőforrást. Válassza **a Bővítmények** lehetőséget a Beállítások **csoportban.**

Kattintson **a + Hozzáadás** gombra, és az erőforrások listájában válassza az **Egyéni parancsfájlkiterjesztés lehetőséget.**

A **Bővítmény telepítése** lapon jelölje ki a helyi PowerShell-fájlt, és töltse ki az argumentumokat, és kattintson az **Ok**gombra.

### <a name="powershell"></a>PowerShell

A [Set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) parancsmag használatával hozzáadhatja az egyéni parancsfájl-bővítményt egy meglévő virtuális géphez.

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

A bővítmény-üzembe helyezések állapotára vonatkozó adatok az Azure Portalról és az Azure PowerShell-modul használatával is lekérdezhetők. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa a következő parancsot:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

A bővítmény kimenetét a rendszer a célvirtuális gépen a következő mappában található fájlokba naplózza.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

A megadott fájlok a célvirtuális gép következő mappájába töltődnek le.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

ahol `<n>` egy tizedes egész szám, amely változhat a kiterjesztés végrehajtásai között.  Az `1.*` érték megegyezik `typeHandlerVersion` a bővítmény tényleges, aktuális értékével.  A tényleges könyvtár lehet `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`például.  

A parancs `commandToExecute` végrehajtásakor a bővítmény ezt a `...\Downloads\2`könyvtárat (például ) állítja be aktuális munkakönyvtárként. Ez a folyamat lehetővé teszi a relatív elérési `fileURIs` utak használatát a tulajdonságon keresztül letöltött fájlok megkereséséhez. A példákat lásd az alábbi táblázatban.

Mivel az abszolút letöltési útvonal idővel változhat, jobb, ha relatív parancsfájl/ fájl elérési utat választ a `commandToExecute` karakterláncban, amikor csak lehetséges. Példa:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Az első URI-szegmens utáni elérési út `fileUris` adatai a tulajdonságlistán keresztül letöltött fájlok esetében megmaradnak.  Az alábbi táblázatban látható módon a letöltött fájlok le vannak képezve `fileUris` a letöltési alkönyvtárakba, hogy tükrözzék az értékek szerkezetét.  

#### <a name="examples-of-downloaded-files"></a>Példák a letöltött fájlokra

| URI a fileUris-ban | Relatív letöltött hely | Abszolút letöltött hely <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> Az abszolút címtárelérési utak a virtuális gép élettartama alatt változnak, de nem a CustomScript-bővítmény egyetlen végrehajtásán belül.

### <a name="support"></a>Támogatás

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel az [MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/) Azure-támogatási incidenst is fájlolhat. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza a Támogatás beszerezni lehetőséget. Az Azure-támogatás használatáról a [Microsoft Azure támogatási gyIK](https://azure.microsoft.com/support/faq/)című területén olvashat.
