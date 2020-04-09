---
title: PowerShell-példaszkriptek
description: Példák, amelyek bemutatják, hogyan kell használni az előtér-alapú PowerShell-parancsfájlokon keresztül
author: florianborn71
ms.author: flborn
ms.date: 02/12/2020
ms.topic: sample
ms.openlocfilehash: c45d2fc34ccbab6d813f12563678d036f9f35753
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891492"
---
# <a name="example-powershell-scripts"></a>PowerShell-példaszkriptek

Az Azure remote rendering a következő két REST API-t biztosítja:

- [Konvertálás REST API](../how-tos/conversion/conversion-rest-api.md)
- [Munkamenet-többi API](../how-tos/session-rest-api.md)

Az [ARR-mintatár](https://github.com/Azure/azure-remote-rendering) a *Parancsfájlok* mappában található mintaparancsfájlokat tartalmaz a szolgáltatás REST API-ival való interakcióhoz. Ez a cikk ismerteti a használat.

## <a name="prerequisites"></a>Előfeltételek

A mintaparancsfájlok végrehajtásához az [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)funkcionális beállítására van szükség.

1. Az Azure PowerShell telepítése:
    1. Rendszergazdai jogosultságokkal rendelkező PowerShell megnyitása
    1. Fuss:`Install-Module -Name Az -AllowClobber`

1. Ha hibákat kap a parancsfájlok futtatásával kapcsolatban, győződjön meg arról, hogy a [végrehajtási házirend](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) megfelelően van beállítva:
    1. Rendszergazdai jogosultságokkal rendelkező PowerShell megnyitása
    1. Fuss:`Set-ExecutionPolicy -ExecutionPolicy Unrestricted`

1. [Azure Storage-fiók előkészítése](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)

1. Jelentkezzen be az Azure távoli leképezési fiókját tartalmazó előfizetésbe:
    1. PowerShell megnyitása
    1. Futtatás: `Connect-AzAccount` és kövesse a képernyőn megjelenő utasításokat.

> [!NOTE]
> Abban az esetben, ha a szervezet egynél több előfizetéssel rendelkezik, előfordulhat, hogy meg kell adnia az Előfizetési és bérlői argumentumokat. Részletek a [Connect-AzAccount dokumentációjában](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount)találhatók.

1. Töltse le a *Scripts* mappát az [Azure Remote Rendering GithHub tárházból.](https://github.com/Azure/azure-remote-rendering)

## <a name="configuration-file"></a>Konfigurációs fájl

A `.ps1` fájlok mellett van `arrconfig.json` egy, amit ki kell töltenie:

```json
{
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure Portal>",
        "arrAccountKey": "<fill in the account key from the Azure Portal>",
        "region": "<select from available regions>"
    },
    "renderingSessionSettings": {
        "vmSize": "<select standard or premium>",
        "maxLeaseTime": "<hh:mm:ss>"
    },
  "assetConversionSettings": {
    "resourceGroup": "<resource group which contains the storage account you created, only needed when uploading or generating SAS>",
    "storageAccountName": "<name of the storage account you created>",
    "blobInputContainerName": "<input container inside the storage container>",
    "blobOutputContainerName": "<output container inside the storage container>",
    "localAssetDirectoryPath": "<fill in a path to a local directory containing your asset (and files referenced from it like textures)>",
    "inputFolderPath": "<optional: base folderpath in the input container for asset upload. uses / as dir separator>",
    "inputAssetPath": "<the path to the asset under inputcontainer/inputfolderpath pointing to the input asset e.g. box.fbx>",
    "outputFolderPath": "<optional: base folderpath in the output container - the converted asset and log files will be placed here>",
    "outputAssetFileName": "<optional: filename for the converted asset, this will be placed in the output container under the outputpath>"
  }
}
```

> [!CAUTION]
> Győződjön meg arról, hogy megfelelően elkerüli a fordított perjeleket a LocalAssetDirectoryPath elérési útban dupla fordított perjelhasználatával: "\\\\" és perjelek "/" használata az összes többi elérési úton, például a inputFolderPath és a inputAssetPath.

### <a name="accountsettings"></a>fiókbeállítások

A `arrAccountId` `arrAccountKey`és a, [lásd: Hozzon létre egy Azure Remote rendering fiók](../how-tos/create-an-account.md).
Lásd `region` az [elérhető régiók listáját.](../reference/regions.md)

### <a name="renderingsessionsettings"></a>renderingSessionSettings

Ezt a struktúrát ki kell tölteni, ha futtatni szeretné **a RenderingSession.ps1 .**

- **vmMéret:** A virtuális gép méretének kiválasztása. Válassza ki *a standard* vagy *prémium*. Állítsa le a renderelési munkameneteket, ha már nincs rájuk szüksége.
- **maxLeaseTime:** Az időtartam, amelynek a virtuális gép lízingelése. A bérlet lejártakor leáll. A bérleti idő később meghosszabbítható (lásd alább).

### <a name="assetconversionsettings"></a>assetConversionSettings (eszközkonverziós beállítások)

Ezt a struktúrát ki kell tölteni, ha futtatni szeretné a **Conversion.ps1**.

További információt az [Azure Storage-fiók előkészítése.](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)

## <a name="script-renderingsessionps1"></a>Parancsfájl: RenderingSession.ps1

Ez a parancsfájl munkamenetek létrehozására, lekérdezésére és leállítására szolgál.

> [!IMPORTANT]
> Győződjön meg róla, hogy kitöltötte a *accountSettings* és *renderingSessionSettings* szakaszok arrconfig.json.

### <a name="create-a-rendering-session"></a>Renderelési munkamenet létrehozása

Normál használat egy teljesen kitöltött arrconfig.json:

```PowerShell
.\RenderingSession.ps1
```

A parancsfájl meghívja a [munkamenet-kezelés REST](../how-tos/session-rest-api.md) API-t, hogy a renderelési virtuális gép a megadott beállításokkal. A siker, akkor letölteni a *sessionId*. Ezután lefolytatja a munkamenet tulajdonságait, amíg a munkamenet készen nem áll, vagy hiba történt.

**Alternatív konfigurációs** fájl használata:

```PowerShell
.\RenderingSession.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

A konfigurációs fájl **egyes beállításait felülbírálhatja:**

```PowerShell
.\RenderingSession.ps1 -Region <region> -VmSize <vmsize> -MaxLeaseTime <hh:mm:ss>
```

Ha csak lekérdezés nélküli munkamenetet szeretne **elindítani,** a következőket használhatja:

```PowerShell
.\RenderingSession.ps1 -CreateSession
```

A parancsfájl által leolvasott *munkamenet-azonosítót* át kell adni a legtöbb munkamenet-parancsnak.

### <a name="retrieve-session-properties"></a>Munkamenet tulajdonságainak beolvasása

A munkamenet tulajdonságainak lefelvételéhez futtassa a következőket:

```PowerShell
.\RenderingSession.ps1 -GetSessionProperties -Id <sessionID> [-Poll]
```

Ezzel `-Poll` megvárható, amíg a munkamenet *elkészül,* vagy hiba történik.

### <a name="list-active-sessions"></a>Aktív munkamenetek listázása

```PowerShell
.\RenderingSession.ps1 -GetSessions
```

### <a name="stop-a-session"></a>Munkamenet leállítása

```PowerShell
.\RenderingSession.ps1 -StopSession -Id <sessionID>
```

### <a name="change-session-properties"></a>Munkamenet tulajdonságainak módosítása

Jelenleg csak a munkamenet maxLeaseTime értékének módosítását támogatjuk.

> [!NOTE]
> A címbérlet ideje mindig számít attól az időponttól kezdve, amikor a munkamenet virtuális gép eredetileg jött létre. Így a munkamenet-bérlet további órával történő meghosszabbítása érdekében növelje *a maxLeaseTime* értéket egy órával.

```PowerShell
.\RenderingSession.ps1 -UpdateSession -Id <sessionID> -MaxLeaseTime <hh:mm:ss>
```

## <a name="script-conversionps1"></a>Parancsfájl: Conversion.ps1

Ez a parancsfájl a bemeneti modellek konvertálása az Azure Remote Rendering adott futásidejű formátumban.

> [!IMPORTANT]
> Győződjön meg arról, hogy kitöltötte a *accountSettings* és *assetConversionSettings* szakaszok at arrconfig.json.

A parancsfájl bemutatja a tárfiókok használatának két lehetőségeit a szolgáltatással:

- Azure távoli leadási fiókhoz kapcsolt tárfiók
- Hozzáférés biztosítása a tárhelyhez megosztott hozzáférésű aláírások (SAS) segítségével

### <a name="linked-storage-account"></a>Csatolt tárfiók

Miután teljesen kitöltötte az arrconfig.json t, és csatolt egy tárfiókot, használhatja a következő parancsot. A tárfiók összekapcsolása a [Fiók létrehozása](../how-tos/create-an-account.md#link-storage-accounts)című oldalon található.

A konverziós szolgáltatás használatának előnyben részesített módja a csatolt tárfiók használata, mivel nincs szükség megosztott hozzáférésű aláírások létrehozására.

```PowerShell
.\Conversion.ps1
```

1. Töltse fel az `assetConversionSettings.modelLocation` összes fájlt, amely a bemeneti blob tároló alatt az adott`inputFolderPath`
1. A [modellkonvertálási REST API](../how-tos/conversion/conversion-rest-api.md) hívása a [modellkonvertálás indításához](../how-tos/conversion/model-conversion.md)
1. A konverzió állapotának lekérdezése addig, amíg az átalakítás sikeres vagy sikertelen nem sikerült
1. A konvertált fájl helyének kimeneti adatai (tárfiók, kimeneti tároló, fájlelérési út a tárolóban)

### <a name="access-to-storage-via-shared-access-signatures"></a>Hozzáférés a tároláshoz megosztott hozzáférésű aláírásokon keresztül

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Ez:

1. Töltse fel a `assetConversionSettings.localAssetDirectoryPath` helyi fájlt a bemeneti blobtárolóba
1. SAS-URI létrehozása a bemeneti tárolóhoz
1. SAS-URI létrehozása a kimeneti tárolóhoz
1. A [modellkonvertálási REST API](../how-tos/conversion/conversion-rest-api.md) hívása a [modellkonvertálás indításához](../how-tos/conversion/model-conversion.md)
1. A konverzió állapotának lekérdezése addig, amíg az átalakítás sikeres vagy sikertelen nem sikerült
1. A konvertált fájl helyének kimeneti adatai (tárfiók, kimeneti tároló, fájlelérési út a tárolóban)
1. SAS-URI kimenete a konvertált modellbe a kimeneti blobtárolóban

### <a name="additional-command-line-options"></a>További parancssori kapcsolók

**Alternatív konfigurációs** fájl használata:

```PowerShell
.\Conversion.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Ha csak **lekérdezés nélkül szeretné elindítani a modellkonverziót,** a következőket használhatja:

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

A konfigurációs fájl **egyes beállításait** a következő parancssori kapcsolókkal bírálhatja felül:

* **Azonosító:** A GetConversionStatus-mal használt ConversionId azonosító
* **ArrAccountId:** aaccountAccountId of accountSettings
* **ArrAccountKey:** felülbírálása arrAccountKey of accountSettings
* **Régió:** felülbírálás a fiókbeállítások régiójához
* **ResourceGroup:** az assetConversionSettings erőforráscsoport felülbírálása
* **StorageAccountName:** az assetConversionSettings storageAccountName of assetConversion
* **BlobInputContainerName:** az assetConversionSettings blobInputContainer-jének felülbírálása
* **LocalAssetDirectoryPath:** az assetConversionSettings localAssetDirectoryPath felülbírálása
* **InputAssetPath:** az assetConversionSettings inputAssetPath felülbírálása
* **BlobOutputContainerName:** az assetConversionSettings blobOutputContainerName felülbírálása
* **OutputFolderPath:** az assetConversionSettings outputFolderPath felülbírálása
* **OutputAssetFileName:** az assetConversionSettings outputAssetFileName -jének felülírása

Például a megadott lehetőségek közül többis kombinálható, mint például:

```PowerShell
.\Conversion.ps1 -LocalAssetDirectoryPath "C:\\models\\box" -InputAssetPath box.fbx -OutputFolderPath another/converted/box -OutputAssetFileName newConversionBox.arrAsset
```

### <a name="run-the-individual-conversion-stages"></a>Az egyes konverziós szakaszok futtatása

Ha a folyamat egyes lépéseit szeretné futtatni, a következőket használhatja:

Csak adatok at töltsön fel az adott LocalAssetDirectoryPath-ból

```PowerShell
.\Conversion.ps1 -Upload
```

Csak a blobstorage-ba már feltöltött modell átalakítási folyamatát indítsa el (ne futtassa a Feltöltés t, ne töltse le a konverziós állapotot) A parancsfájl *konverzióazonosítót*ad vissza.

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

A konverzió konverziós állapotát pedig a következő konkra kérhető:

```PowerShell
.\Conversion.ps1 -GetConversionStatus -Id <conversionId> [-Poll]
```

Ezzel `-Poll` megvárható, amíg a konvertálás befejeződik, vagy hiba történik.

## <a name="next-steps"></a>További lépések

- [Rövid útmutató: Modell renderelése unityvel](../quickstarts/render-model.md)
- [Rövid útmutató: Modell konvertálása rendereléshez](../quickstarts/convert-model.md)
- [Modell átalakítása](../how-tos/conversion/model-conversion.md)
