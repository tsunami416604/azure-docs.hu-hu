---
title: Azure rendszerkép-készítő sablon létrehozása (előzetes verzió)
description: Megtudhatja, hogyan hozhat létre sablont az Azure rendszerkép-készítővel való használatra.
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 13392644ebe5e163e946deceeec5fcab8f5085cc
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159717"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Előzetes verzió: Azure rendszerkép-készítő sablon létrehozása 

Az Azure rendszerkép-készítő egy. JSON-fájllal továbbítja az adatokat a rendszerkép-szerkesztő szolgáltatásba. Ebben a cikkben a JSON-fájl részeit fogjuk felépíteni, így saját maga is létrehozhat. A teljes. JSON fájlokra vonatkozó példákat a következő témakörben tekintheti meg: [Azure rendszerkép-készítő GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

Ez az alapszintű sablon formátuma:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
             }
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "build": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Típus és API-verzió

A `type` az erőforrástípus, amelynek `"Microsoft.VirtualMachineImages/imageTemplates"`nek kell lennie. A `apiVersion` az API változásakor az idő múlásával változik, de az előzetes verziónak `"2019-05-01-preview"` kell lennie.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Földrajzi egység

A hely az a régió, ahol az egyéni rendszerkép létre lesz hozva. A rendszerkép-készítő előzetes verziójában a következő régiók támogatottak:

- USA keleti régiója
- USA 2. keleti régiója
- USA nyugati középső régiója
- USA nyugati régiója
- USA 2. nyugati régiója


```json
    "location": "<region>",
```

## <a name="tags"></a>Címkék

Ezek a generált rendszerképhez megadható kulcs/érték párok.

## <a name="depends-on-optional"></a>Függ (nem kötelező)

Ez a választható szakasz használható annak biztosítására, hogy a folytatás előtt a függőségek befejeződik. 

```json
    "dependsOn": [],
```

További információ: az [erőforrás-függőségek meghatározása](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Identitáskezelés
Alapértelmezés szerint a rendszerkép-szerkesztő támogatja a parancsfájlok használatát, vagy több helyről, például a GitHubról vagy az Azure Storage-ból másolt fájlokat. Ezek használatához nyilvánosan elérhetőnek kell lenniük.

Az Ön által meghatározott Azure-felhasználó által hozzárendelt felügyelt identitást is használhat a rendszerkép-készítő Azure Storage-hoz való hozzáférésének engedélyezéséhez, feltéve, hogy az identitás legalább "Storage blob-adatolvasót" kapott az Azure Storage-fiókban. Ez azt jelenti, hogy nem kell a Storage-blobokat külsőleg elérhetővé tenni, vagy beállítani az SAS-jogkivonatokat.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

A teljes példa: [Azure-felhasználó által hozzárendelt felügyelt identitás használata az Azure Storage-beli fájlok eléréséhez](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

Rendszerkép-készítő támogatás felhasználó által hozzárendelt identitáshoz: • csak egyetlen identitást támogat, a nem támogatja az egyéni tartományneveket

További információ: [Mi az az Azure-erőforrások felügyelt identitása?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
A szolgáltatás telepítésével kapcsolatos további információkért lásd: [felügyelt identitások konfigurálása](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)Azure-beli virtuális gépeken az Azure CLI használatával.

## <a name="properties-source"></a>Tulajdonságok: forrás

A `source` szakasz a rendszerkép-szerkesztő által használt forrás-lemezképpel kapcsolatos információkat tartalmazza.

Az API-nak szüksége van egy "forrás típusa" elemre, amely meghatározza a rendszerkép-Build forrását, jelenleg három típus létezik:
- ISO – ezt akkor használja, ha a forrás egy ISO-RHEL.
- PlatformImage – jelezte, hogy a forrás rendszerkép egy Piactéri rendszerkép.
- ManagedImage – ez a megoldás akkor használható, ha egy normál felügyelt rendszerképből indul.
- SharedImageVersion – ez akkor használatos, ha a rendszerkép verzióját megosztott képtárban használja forrásként.

### <a name="iso-source"></a>ISO-forrás

Az Azure rendszerkép-szerkesztő csak a közzétett Red Hat Enterprise Linux 7. x bináris DVD ISO-k használatát támogatja az előzetes verzióhoz. A rendszerkép-szerkesztő támogatja a következőket:
- RHEL 7,3 
- RHEL 7,4 
- RHEL 7.5 
 
```json
"source": {
       "type": "ISO",
       "sourceURI": "<sourceURI from the download center>",
       "sha256Checksum": "<checksum associated with ISO>"
}
```

A `sourceURI` és `sha256Checksum` értékek beszerzéséhez lépjen a `https://access.redhat.com/downloads` majd válassza ki a termék **Red Hat Enterprise Linux**, valamint egy támogatott verziót. 

A **Red Hat Enterprise Linux-kiszolgáló telepítői és rendszerképeinek**listáján át kell másolnia a Red Hat Enterprise Linux 7. x bináris DVD-re és az ellenőrzőösszegre mutató hivatkozást.

> [!NOTE]
> A hivatkozások hozzáférési jogkivonatai gyakori időközönként frissülnek, így minden alkalommal, amikor sablont szeretne beküldeni, ellenőriznie kell, hogy módosult-e az RH-hivatkozás címe.
 
### <a name="platformimage-source"></a>PlatformImage forrása 
Az Azure Image Builder a következő Azure Marketplace-rendszerképeket támogatja:
* Ubuntu 18.04
* Ubuntu 16.04
* RHEL 7,6
* CentOS 7,6
* Windows 2016
* Windows 2019

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "18.04.201903060"
        },
```


A virtuális gép létrehozásához használt tulajdonságok ugyanazok, mint az AZ CLI használatával, a tulajdonságok lekéréséhez futtassa az alábbi parancsot: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

> [!NOTE]
> A verzió nem lehet "Latest", a fenti paranccsal kérheti le a verziószámot. 

### <a name="managedimage-source"></a>ManagedImage forrása

A forrás rendszerképét egy általánosított virtuális merevlemez vagy virtuális gép meglévő felügyelt rendszerképeként állítja be. A forrásként felügyelt rendszerképnek támogatott operációs rendszernek kell lennie, és az Azure rendszerkép-készítő sablonnal azonos régióban kell lennie. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

A `imageId`nak a felügyelt rendszerkép ResourceId kell lennie. Az elérhető rendszerképek listázásához használja a `az image list`.


### <a name="sharedimageversion-source"></a>SharedImageVersion forrása
Beállítja a forrás rendszerképet egy meglévő rendszerkép-verzióra egy megosztott rendszerkép-gyűjteményben. A rendszerkép verziószámának támogatott operációs rendszernek kell lennie, és a rendszerképet az Azure rendszerkép-készítő sablonnal azonos régióba kell replikálni. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

A `imageVersionId`nak a rendszerkép ResourceId kell lennie. A képverziók listázásához használja [az az SIG rendszerkép-Version List](/cli/azure/sig/image-version#az-sig-image-version-list) lehetőséget.

## <a name="properties-buildtimeoutinminutes"></a>Tulajdonságok: buildTimeoutInMinutes

Alapértelmezés szerint a rendszerkép-szerkesztő 240 percig fog futni. Ezt követően az időtúllépést és leállást eredményez, függetlenül attól, hogy befejeződött-e a rendszerkép létrehozása. Ha az időtúllépés eléri a találatot, a következőhöz hasonló hibaüzenet jelenik meg:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Ha nem ad meg buildTimeoutInMinutes értéket, vagy 0 értékre állítja, akkor az az alapértelmezett értéket fogja használni. Növelheti vagy csökkentheti az értéket a maximális 960mins (16hrs). A Windows esetében nem ajánlott a 60 percnél régebbi beállítást beállítani. Ha megtalálta az időtúllépést, tekintse át a [naplókat](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs), és ellenőrizze, hogy a testreszabási lépés a felhasználói adatbevitelre vár-e. 

Ha úgy találja, hogy a testreszabások befejezéséhez több időre van szüksége, állítsa be ezt a kívánt értékre, és egy kis terheléssel. De ne állítsa túl magasra, mert előfordulhat, hogy meg kell várnia az időtúllépést, mielőtt hibaüzenetet lát. 


## <a name="properties-customize"></a>Tulajdonságok: testreszabás

A rendszerkép-szerkesztő több "testreszabó" használatát is támogatja. A testreszabók olyan függvények, amelyek a rendszerkép testreszabására szolgálnak, például parancsfájlok futtatására vagy kiszolgálók újraindítására. 

`customize`használatakor: 
- Több testreszabó is használható, de egyedi `name`kell rendelkezniük.
- A testreszabók a sablonban megadott sorrendben hajthatók végre.
- Ha egy testreszabó nem sikerül, a teljes testreszabási összetevő sikertelen lesz, és hibát jelez.
- Nyomatékosan javasoljuk, hogy alaposan tesztelje a parancsfájlt, mielőtt használni lehetne a sablonban. A parancsfájl hibakeresése a saját virtuális gépen egyszerűbb lesz.
- Ne helyezzen bizalmas adatokat a parancsfájlokba. 
- A parancsfájl helyeinek nyilvánosan elérhetőnek kell lenniük, hacsak nem használ [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)-t.

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
A Testreszabás szakasz egy tömb. Az Azure rendszerkép-szerkesztő szekvenciális sorrendben fut a testreszabók között. Bármely testreszabó hibája sikertelen lesz a létrehozási folyamat során. 
 
 
### <a name="shell-customizer"></a>Rendszerhéj-testreszabó

A rendszerhéj-testreszabó támogatja a rendszerhéj-parancsfájlok futtatását, ezeknek nyilvánosan elérhetőnek kell lenniük az IB-hez való hozzáféréshez.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>"        
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

Operációs rendszer támogatása: Linux 
 
Tulajdonságok testreszabása:

- **típus** – rendszerhéj 
- **név** – a Testreszabás követésének neve 
- **scriptUri** – URI a fájl helyéhez 
- beépített rendszerhéj-parancsok **beágyazott** tömbje, vesszővel elválasztva.
 
> [!NOTE]
> Ha a rendszerhéj-testreszabó a RHEL ISO-forrással futtatja, gondoskodnia kell arról, hogy az első testreszabási rendszerhéj a Red Hat jogosultsági kiszolgálóval való regisztrálást a testreszabások előtt is kezelje. A Testreszabás befejeződése után a parancsfájlnak meg kell szüntetnie a jogosultsági kiszolgáló regisztrációját.

### <a name="windows-restart-customizer"></a>Windows újraindítási testreszabó 
Az újraindítási testreszabó lehetővé teszi egy Windows rendszerű virtuális gép újraindítását, és várjon, amíg online állapotba kerül, így olyan szoftvereket telepíthet, amelyek újraindítást igényelnek.  

```json 
     "customize": [ 
            "type{ ": "WindowsRestart", 
            "restartCommand": "shutdown /r /f /t 0 /c", 
            "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > buildArtifacts/azureImageBuilderRestart.txt",
            "restartTimeout": "5m"
         }],
```

Operációs rendszer támogatása: Windows
 
Tulajdonságok testreszabása:
- **Típus**: WindowsRestart
- **restartCommand** – az újraindítás végrehajtásához szükséges parancs (nem kötelező). A mező alapértelmezett értéke: `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – a parancs segítségével ellenőrizze, hogy az újraindítás sikeres volt-e (nem kötelező). 
- **restartTimeout** – újraindítási időkorlát, amely a magnitúdó és az egység értékének megfelelően van megadva. Például `5m` (5 perc) vagy `2h` (2 óra). Az alapértelmezett érték: "5m"


### <a name="powershell-customizer"></a>PowerShell-testreszabó 
A rendszerhéj-testreszabó támogatja a PowerShell-parancsfájlok és a beágyazott parancsok futtatását, a parancsfájloknak nyilvánosan elérhetőnek kell lenniük az IB-hez való hozzáféréshez.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "valid_exit_codes": "<exit code>" 
         } 
    ], 
```

Operációs rendszer támogatása: Windows és Linux

Tulajdonságok testreszabása:

- **típus** – PowerShell.
- **scriptUri** – URI a PowerShell-parancsfájl helyéhez. 
- **beágyazott** – a futtatandó beágyazott parancsok, vesszővel elválasztva.
- **valid_exit_codes** – választható, érvényes kódok, amelyek a parancsfájl/beágyazott parancsból visszaadhatók, így elkerülhető a parancsfájl/beágyazott parancs hibájának jelentése.

### <a name="file-customizer"></a>Fájl-testreszabó

A fájl-testreszabó lehetővé teszi, hogy a rendszerkép-készítő letöltse a fájlt egy GitHub vagy egy Azure Storage szolgáltatásból. Ha olyan rendszerkép-létrehozási folyamattal rendelkezik, amely a Build-összetevőkre támaszkodik, akkor a fájl-testreszabó beállítható úgy, hogy letöltse a Build megosztásról, és áthelyezi az összetevőket a rendszerképbe.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>" 
         }
     ]
```

Operációs rendszer támogatása: Linux és Windows 

Fájl-testreszabó tulajdonságai:

- **sourceUri** – elérhető tárolási végpont, ez lehet GitHub vagy Azure Storage. Csak egy fájlt tölthet le, nem egy teljes könyvtárat. Ha le kell töltenie egy könyvtárat, használjon tömörített fájlt, majd bontsa ki a tömörítést a rendszerhéj vagy a PowerShell-testreszabók használatával. 
- **cél** – ez a célhely teljes elérési útja és fájlneve. Minden hivatkozott útvonalnak és alkönyvtárnak léteznie kell, a rendszerhéj vagy a PowerShell-testreszabók használatával előre beállíthatja ezeket. Az elérési út létrehozásához használhatja a parancsfájl-testreszabók lehetőséget. 

Ezt a Windows-címtárak és a Linux-elérési utak támogatják, de vannak különbségek: 
- Linux operációs rendszer – az egyetlen elérési út a rendszerkép-készítő a/tmp.
- Windows – nincs elérésiút-korlátozás, de az elérési útnak léteznie kell.
 
 
Ha hiba történt a fájl letöltése vagy egy megadott címtárba való behelyezése során, a testreszabási lépés sikertelen lesz, és ez a customization. log fájlban lesz.

> [!NOTE]
> A fájl-testreszabó csak kisméretű fájlok letöltésére alkalmas, < 20 MB-ot. A nagyobb fájlok letöltéséhez parancsfájl vagy beágyazott parancs használatos, a fájlok letöltésére szolgáló kód (például:, Linux `wget` vagy `curl`, Windows `Invoke-WebRequest`.

A fájl-testreszabó fájljai az [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)használatával tölthetők le az Azure Storage-ból.

### <a name="generalize"></a>Általánossá tétel 
Alapértelmezés szerint az Azure-rendszerkép-készítő a rendszerkép testreszabási fázisának végén a "megszüntetés" kódot is futtatja, hogy "általánosítsa" a képet. Az általánosítás egy olyan folyamat, amelyben a rendszerkép be van állítva, így több virtuális gép létrehozására is felhasználható. Windows rendszerű virtuális gépek esetén az Azure rendszerkép-készítő a Sysprept használja. Linux rendszeren az Azure rendszerkép-szerkesztő "waagent"-megszüntetést futtat. 

Előfordulhat, hogy a rendszerkép-szerkesztő felhasználói általánosított parancsai nem alkalmasak minden helyzetben, így az Azure rendszerkép-készítő lehetővé teszi, hogy szükség esetén testreszabja ezt a parancsot. 

Ha meglévő testreszabást telepít át, és különböző Sysprep/waagent parancsokat használ, használhatja a rendszerkép-készítő általános parancsait, és ha a virtuális gép létrehozása meghiúsul, használja a saját Sysprep vagy waagent parancsait.

Ha az Azure rendszerkép-készítő sikeresen létrehoz egy egyéni Windows-rendszerképet, és létrehoz egy virtuális gépet, akkor a virtuális gép létrehozása sikertelen lesz, vagy nem fejeződik be sikeresen, át kell tekintenie a Windows Server Sysprep dokumentációját, vagy támogatási kérést kell létrehoznia a A Windows Server Sysprep Customer Services támogatási csapata, amely a megfelelő Sysprep-használattal kapcsolatos hibakeresést és tanácsadást tesz lehetővé.


#### <a name="default-sysprep-command"></a>Alapértelmezett Sysprep-parancs
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Alapértelmezett Linux-kiépítés parancs

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>A parancsok felülbírálása
A parancsok felülbírálásához a PowerShell vagy a rendszerhéj parancsfájl-kiépítő használatával hozza létre a megfelelő fájlnevet tartalmazó parancsfájlokat, és helyezze azokat a megfelelő könyvtárakba:

* Windows: c:\DeprovisioningScript.ps1
* Linux:/tmp/DeprovisioningScript.sh

A képszerkesztő beolvassa ezeket a parancsokat, ezeket a rendszer kiírja a "customization. log" AIB-naplókba. Lásd: a naplók gyűjtésével [kapcsolatos hibaelhárítás](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) .
 
## <a name="properties-distribute"></a>Tulajdonságok: terjesztés

Az Azure rendszerkép-szerkesztő három terjesztési célt támogat: 

- **managedImage** által felügyelt rendszerkép.
- **sharedImage** – közös rendszerkép-gyűjtemény.
- **VHD** – VHD egy Storage-fiókban.

A rendszerképeket az azonos konfigurációban található cél típusokra is terjesztheti, [példákat](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80)talál.

Mivel több célt is kiterjesztheti a szolgáltatásba, a rendszerkép-szerkesztő minden olyan terjesztési cél állapotát fenntartja, amely a `runOutputName`lekérdezésével érhető el.  A `runOutputName` egy olyan objektum, amelyről lekérdezheti a terjesztés utáni adatokat. Lekérdezheti például a virtuális merevlemez helyét, illetve azokat a régiókat, amelyeken a rendszerkép verziója replikálva van. Ez az összes terjesztési cél tulajdonsága. A `runOutputName`nak egyedinek kell lennie az egyes terjesztési célkitűzéseknél.
 
### <a name="distribute-managedimage"></a>Terjesztés: managedImage

A rendszerkép kimenete felügyelt rendszerkép-erőforrás lesz.

```json
"distribute": [
        {
"type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
             "<name>": "<value>"
               }
         }]
```
 
Elosztás tulajdonságai:
- **típus** – managedImage 
- **imageId** – a célként megadott rendszerkép erőforrás-azonosítója, a várt formátum:/Subscriptions/\<subscriptionId >/ResourceGroups/\<destinationResourceGroupName >/Providers/Microsoft.Compute/images/\<imageName >
- **hely** – a felügyelt rendszerkép helye.  
- **runOutputName** – a terjesztés azonosítására szolgáló egyedi név.  
- **artifactTags** – opcionális felhasználó által megadott kulcs érték párok címkéi.
 
 
> [!NOTE]
> A cél erőforráscsoport léteznie kell.
> Ha azt szeretné, hogy a rendszerképet egy másik régióba lehessen terjeszteni, azzal megnöveli a központi telepítés idejét. 

### <a name="distribute-sharedimage"></a>Terjesztés: sharedImage 
Az Azure Shared Image Gallery egy új rendszerkép-kezelő szolgáltatás, amely lehetővé teszi a lemezkép-régiók replikálásának, verziószámozásának és az Egyéni rendszerképek megosztásának kezelését. Az Azure Image Builder támogatja a szolgáltatás terjesztését, így képeket oszthat meg a megosztott képtárak által támogatott régiókba. 
 
A megosztott képkatalógusok a következőket alkotják: 
 
- Gallery – több megosztott rendszerkép tárolója. Egy gyűjtemény egy régióban van üzembe helyezve.
- Képdefiníciók – a rendszerképek fogalmi csoportosítása. 
- Lemezkép-verziók – ez egy virtuális gép vagy méretezési csoport üzembe helyezésére szolgáló rendszerkép-típus. A lemezkép-verziók más régiókban is replikálhatók, ahol a virtuális gépeket telepíteni kell.
 
A lemezkép-katalógusba való terjesztés előtt létre kell hoznia egy gyűjteményt és egy rendszerkép-definíciót, lásd: [megosztott lemezképek](shared-images.md). 

```json
{
    "type": "sharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

Megosztott képtárak tulajdonságainak terjesztése:

- **típus** – sharedImage  
- **galleryImageId** – a megosztott rendszerkép-Gyűjtemény azonosítója. A formátum:/Subscriptions/\<subscriptionId >/resourceGroups/\<resourceGroupName >/providers/Microsoft.Compute/galleries/\<sharedImageGalleryName >/images/\<imageGalleryName >.
- **runOutputName** – a terjesztés azonosítására szolgáló egyedi név.  
- **artifactTags** – opcionális felhasználó által megadott kulcs érték párok címkéi.
- **replicationRegions** – a replikálási régiók tömbje. Az egyik régió az a régió, amelyben a katalógus üzembe van helyezve.
 
> [!NOTE]
> Az Azure rendszerkép-készítőt egy másik régióban használhatja a katalógusban, de az Azure rendszerkép-készítő szolgáltatásnak át kell vinnie a rendszerképet az adatközpontok között, és ez hosszabb időt vesz igénybe. A rendszerkép-készítő a rendszerképet egy monoton egész szám alapján automatikusan elvégzi, ezért jelenleg nem adható meg. 

### <a name="distribute-vhd"></a>Terjesztés: VHD  
A kimenetet virtuális merevlemezre is kipróbálhatja. Ezután átmásolhatja a VHD-t, és felhasználhatja az Azure MarketPlace-en való közzétételhez, vagy használhatja Azure Stack.  

```json
{ 
    "type": "VHD",
    "runOutputName": "<VHD name>",
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
    }
}
```
 
Operációs rendszer támogatása: Windows és Linux

VHD-paraméterek terjesztése:

- **típus** – VHD.
- **runOutputName** – a terjesztés azonosítására szolgáló egyedi név.  
- **címkék** – nem kötelező felhasználó által megadott kulcs-érték párok címkéi.
 
Az Azure rendszerkép-készítő nem teszi lehetővé a felhasználó számára a tárolási fiók helyének megadását, de lekérdezheti az `runOutputs` állapotát a hely beszerzéséhez.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> A virtuális merevlemez létrehozása után a lehető leghamarabb másolja át egy másik helyre. A virtuális merevlemezt az ideiglenes erőforráscsoport tárolja, amely akkor jön létre, amikor a rendszer elküldi a képsablont az Azure rendszerkép-szerkesztő szolgáltatásba. Ha törli a képsablont, akkor elveszíti a VHD-t. 
 
## <a name="next-steps"></a>Következő lépések

Az [Azure rendszerkép-készítő githubon](https://github.com/danielsollondon/azvmimagebuilder)különböző forgatókönyvekhez készült minta. JSON fájlok találhatók.
 
 
 
 
 
 
 
 
 
 
