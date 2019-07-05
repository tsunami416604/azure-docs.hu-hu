---
title: Hozzon létre egy Azure Image Builder-sablon (előzetes verzió)
description: Ismerje meg, hogyan hozzon létre egy sablont az Azure az Image Builder használandó.
author: cynthn
ms.author: cynthn
ms.date: 05/10/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: a577a2a2a3b21cb027ba699450631a627f4f7a39
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67501878"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Előzetes verzió: Az Azure az Image Builder-sablon létrehozása 

Az Azure az Image Builder át az információkat az Image Builder szolgáltatásba egy .JSON kiterjesztésű fájlt használ. Ebben a cikkben azt, akkor kerülnek át a json-fájlt a szakaszait, így könnyedén létrehozhatja a saját. Példák a teljes .JSON kiterjesztésű fájlokat, olvassa el a [Azure kép Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

Ez a alapvető sablon formátuma:

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

A `type` erőforrástípusa, amelynek kell `"Microsoft.VirtualMachineImages/imageTemplates"`. A `apiVersion` , az API-módosítás idővel változni fognak, de célszerű `"2019-05-01-preview"` előzetes verzióra.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Location

A hely az a régió, ahol létrejön az egyéni rendszerképet. Az Image Builder előzetes verzióra a következő régiókban érhető el:

- East US
- USA 2. keleti régiója
- USA nyugati középső régiója
- USA nyugati régiója
- USA nyugati régiója, 2.


```json
    "location": "<region>",
```
    
## <a name="depends-on-optional"></a>Attól függ, (nem kötelező)

Ez az opcionális szakasz segítségével győződjön meg arról, hogy a függőségek végezhető el a folytatás előtt. 

```json
    "dependsOn": [],
```

További információkért lásd: [meghatározása az erőforrás-függőségek](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Identitás
Alapértelmezés szerint a szkriptek használatával, vagy a fájlok másolása több helyről, például a GitHub és az Azure storage az Image Builder támogatja. Ezek használatához nyilvánosan hozzáférhetőnek kell lenniük.

Azure User-Assigned felügyelt identitást, Ön által meghatározott használatával hozzáférést az Image Builder Azure Storage, mindaddig, amíg a kapott "Storage-Blobadatok olvasója" legalább az Azure storage-fiókban. Ez azt jelenti, hogy nem kell, hogy a storage-blobokat kívülről elérhető-e, vagy a telepítő SAS-tokeneket.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Egy teljes példa: [ Azure User-Assigned felügyelt identitás használata fájlok eléréséhez az Azure Storage-ban](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

Image Builder támogatása a User-Assigned identitás: • egy egyetlen identitást támogató csak • nem támogatja az egyéni tartománynevek

További tudnivalókért lásd: [Mi az Azure-erőforrások felügyelt identitások?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Ez a funkció üzembe helyezéséről további információkért lásd: [konfigurálása felügyelt identitások az Azure-erőforrások egy Azure virtuális gépen az Azure CLI-vel](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

## <a name="properties-source"></a>Tulajdonságok: forrás

A `source` című rész tartalmazza a forrás lemezkép, amelyet az Image Builder fog használni.

Az API-t igényel a "forrás típusa", amely meghatározza a forrás lemezkép build, jelenleg három feltételtípus érhető el:
- ISO - használja ezt a forrás egy RHEL ISO esetén.
- PlatformImage – jelzi, hogy a forrás lemezkép Piactéri lemezképet.
- ManagedImage - használja ezt a rendszeresen felügyelt rendszerképből indításakor.
- SharedImageVersion – egy kép verzióját használja egy megosztott rendszerkép-katalógusában forrásaként használható.

### <a name="iso-source"></a>ISO-forrás

Az Azure az Image Builder csak támogatja a közzétett Red Hat Enterprise Linux 7.x bináris DVD ISO, előzetes verzióra. Az Image Builder támogatja:
- RHEL 7.3. 
- 7\.4 RHEL 
- RHEL 7.5 
 
```json
"source": {
       "type": "ISO",
       "sourceURI": "<sourceURI from the download center>",
       "sha256Checksum": "<checksum associated with ISO>"
}
```

Az első a `sourceURI` és `sha256Checksum` értékek, keresse fel `https://access.redhat.com/downloads` válassza ki a termék **Red Hat Enterprise Linux**, és a egy támogatott verziójára. 

Listájában **telepítők és lemezképek a Red Hat Enterprise Linux Server**, át kell másolnia a Red Hat Enterprise Linux 7.x bináris DVD-ről, és az ellenőrzőösszeg mutató hivatkozás.

> [!NOTE]
> A hozzáférési tokenek hivatkozások rendszeres időközönként frissülnek, így minden alkalommal, amikor szeretne elküldeni egy sablont, ellenőrizze a RH csatolása címe megváltozott.
 
### <a name="platformimage-source"></a>PlatformImage forrás 
Az Azure az Image Builder támogatja a következő Azure Marketplace-rendszerképek:
* Ubuntu 18.04
* Ubuntu 16.04
* RHEL 7.6
* CentOS 7.6
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


Itt a Tulajdonságok megegyeznek használt virtuális gépek létrehozása, a parancssori felületről AZ, futtassa az alábbi tulajdonságait: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

> [!NOTE]
> Verzió nem lehet "legújabb", verzió számának a fenti parancsot kell használnia. 

### <a name="managedimage-source"></a>ManagedImage source

A forrás lemezkép beállítása egy már létező felügyelt rendszerkép egy általánosított virtuális Merevlemezt vagy virtuális Gépet. A felügyelt forráslemezkép egy támogatott operációs rendszer legyen, és az Azure az Image Builder-sablonként ugyanabban a régióban kell. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

A `imageId` az erőforrás-azonosító, a felügyelt rendszerkép kell lennie. Használat `az image list` elérhető rendszerképek listázásához.


### <a name="sharedimageversion-source"></a>SharedImageVersion forrás
A forrás lemezkép beállítja a meglévő lemezkép verziója egy megosztott rendszerkép-katalógusában. A rendszerkép verziószámát kell egy támogatott operációs rendszer, és a lemezkép replikálni kell az ugyanabban a régióban, az Azure az Image Builder-sablonként. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

A `imageVersionId` kell lennie az erőforrás-azonosító, a lemezkép-verzió. Használat [az sig lemezkép-verzió lista](/cli/azure/sig/image-version#az-sig-image-version-list) lista kép verzióra.

## <a name="properties-customize"></a>Tulajdonságok: testreszabása


Az Image Builder támogatja a több "testreszabók". A testreszabók olyan funkció, amely segítségével testre szabhatja a rendszerképet például-parancsfájlok futtatásakor, vagy a kiszolgálók újraindítása. 

Használata esetén `customize`: 
- Több testreszabók is használhat, de rendelkeznie kell egy egyedi `name`.
- A testreszabók hajtsa végre a sablonban megadott sorrendben.
- Ha egy rendszertestreszabó sikertelen, akkor a teljes testreszabási összetevő meghiúsul, és jelentéseket küldhetnek vissza hibát.
- Fontolja meg a rendszerkép összeállítását igényelnek, és állítsa be a "buildTimeoutInMinutes" tulajdonságot, hogy az image builder elegendő ideig mennyi ideig.
- Javasoljuk, tesztelje a parancsfájl alaposan a sablon használatához. A parancsfájl a saját virtuális Gépét a hibakeresés könnyebb lesz.
- Bizalmas adatok ne telepítse a szkriptekben. 
- A parancsfájl helyeket kell nyilvánosan hozzáférhető, kivéve, ha használ [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

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

 
A Testreszabás szakaszában egy tömb. Az Azure az Image Builder az egymást követő sorrendben testreszabók keresztül fog futni. Bármilyen hiba történt a bármely rendszertestreszabó a létrehozási folyamat meghiúsul. 
 
 
### <a name="shell-customizer"></a>Rendszerhéj rendszertestreszabó

A rendszerhéj rendszertestreszabó támogatja rendszerhéj-parancsfájlok futtatásakor, ezek nyilvánosan hozzáférhetőnek kell lennie a a IB érheti el őket.

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

Operációsrendszer-támogatást: Linux 
 
Tulajdonságok személyre szabásához:

- **típus** – héjastól 
- **név** - követési a testreszabási neve 
- **scriptUri** -URI-t a fájl helye 
- **beágyazott** -rendszerhéjparancsokat, vesszővel elválasztva tömbje.
 
> [!NOTE]
> A rendszerhéj rendszertestreszabó RHEL ISO forrással futtatásakor kell biztosítani az első testreszabási rendszerhéj leírók regisztrálása egy Red Hat-jogosultság kiszolgálóval, akkor fordul elő, bármilyen egyéni módosítás előtt. Testreszabása akkor fejeződik be, miután a parancsfájl a jogosultság-kiszolgálóval kell regisztrációját.

### <a name="windows-restart-customizer"></a>Windows-újraindítás rendszertestreszabó 
Az újraindítás rendszertestreszabó lehetővé teszi, hogy a Windows virtuális gép újraindítása, és várjon, amíg ismét online elérhető lesz, ez lehetővé teszi, hogy telepítse az újraindítást igénylő szoftvert.  

```json 
     "customize": [ 
            "type{ ": "WindowsRestart", 
            "restartCommand": "shutdown /r /f /t 0 /c", 
            "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > buildArtifacts/azureImageBuilderRestart.txt",
            "restartTimeout": "5m"
         }],
```

Operációsrendszer-támogatást: Windows
 
Tulajdonságok személyre szabásához:
- **Típus**: WindowsRestart
- **restartCommand** -parancsot (nem kötelező) az újraindítást. A mező alapértelmezett értéke: `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – paranccsal ellenőrizheti, ha az újraindítás sikerült (nem kötelező). 
- **restartTimeout** -indítsa újra a magnitude és az egység egy karakterláncként megadott időkorlát. Ha például `5m` (5 perc), vagy `2h` (2 óra). Az alapértelmezett érték: '5m'


### <a name="powershell-customizer"></a>PowerShell rendszertestreszabó 
A rendszerhéj rendszertestreszabó támogatja a beágyazott parancs PowerShell-parancsfájlok futtatásakor, a parancsfájlok a érheti el őket a IB nyilvánosan elérhetőnek kell lennie.

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

Operációsrendszer-támogatást: Windows és Linux

Tulajdonságok személyre szabásához:

- **típus** – PowerShell.
- **scriptUri** -URI-t a PowerShell parancsfájl helyét. 
- **beágyazott** – beágyazott parancsok futtatására, vesszővel elválasztva.
- **valid_exit_codes** – nem kötelező, a parancsfájl vagy beágyazott visszaadható érvényes kódok paranccsal, így elkerülhető, a parancsfájl vagy beágyazott parancs jelentett hiba.

### <a name="file-customizer"></a>Fájl rendszertestreszabó

A fájl rendszertestreszabó lehetővé teszi, hogy az image builder egy fájlt a GitHub vagy az Azure storage töltheti le. Ha egy kép buildelési folyamat támaszkodik a build-összetevőket, ezután állítsa be a fájl rendszertestreszabó töltheti le a build-megosztást, és helyezze át az összetevőket a lemezképpel.  

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

Operációsrendszer-támogatást: A Linux és Windows 

Fájltulajdonságok rendszertestreszabó:

- **sourceUri** -elérhető tárolási végpont GitHub vagy az Azure storage is lehet. Csak egy fájlt, egy teljes könyvtár nem töltheti le. Ha le kell töltenie egy könyvtárat, egy tömörített fájl, majd bontsa ki azt a rendszerhéjat vagy PowerShell testreszabók segítségével. 
- **cél** – Ez a teljes cél elérési útja és fájlneve. Minden hivatkozott elérési utat és alkönyvtárak kell létezik, a rendszerhéjat vagy PowerShell testreszabók használatával ezek előre beállítva. A parancsfájl testreszabók segítségével hozza létre az elérési utat. 

Ez Windows-könyvtárak és a Linux elérési út támogatott, de van néhány eltérés: 
- Linux operációs rendszer a – egyetlen elérési kép builder írhat az ügynökszámítógépen.
- Windows – nincs elérési útja korlátozás, de az elérési út már léteznie kell.
 
 
Ha hiba előfordulásakor töltse le a fájlt, vagy a megadott könyvtárba helyezze a Testreszabás lépés sikertelen lesz, és ez lesz a customization.log.

>> Megjegyzés! A fájl rendszertestreszabó csak ideális kis letöltéseket, < 20 MB-ot. Nagyobb méretű fájlok letöltése egy parancsfájl vagy beágyazott parancs használatával, a használható kóddal töltse le a fájlokat, például a Linux `wget` vagy `curl`, Windows, `Invoke-WebRequest`.

A fájl rendszertestreszabó fájlokat az Azure Storage-ból letölthető használatával [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="generalize"></a>Általánossá tétel 
Alapértelmezés szerint az Azure az Image Builder fog kódot is futtathat megszüntetése"minden lemezkép testre szabási szakaszhoz ugranak, '' a lemezkép általánossá tétele a végén. Általánosítása egy olyan folyamat, ahol a kép be van állítva, így felhasználhatók a több virtuális gép létrehozása. Windows virtuális gépek Azure az Image Builder Sysprep használja. Linux rendszeren az Azure az Image Builder fut "waagent-deprovision". 

A parancsok az Image Builder felhasználók általánosításához nem lehet minden esetben alkalmas, az Azure az Image Builder testre szabhatja ezt a parancsot, szükség esetén lehetővé teszi. 

Meglévő testreszabási telepít át, és különböző Sysprep/waagent-parancsok használata, ha az Image Builder általános parancsokkal is, és ha a virtuális gép létrehozása sikertelen, a saját Sysprep vagy waagent parancsok használata.

Azure az Image Builder hoz létre egyéni Windows-lemezkép sikeresen megtörtént, és azt, majd keresse meg a virtuális gép létrehozása sikertelen lesz, vagy nem sikeres, a virtuális Gépet hoz létre, szüksége lesz a Windows Server a Sysprep dokumentációjában olvashat, vagy növeljük a támogatási kérelmet a A Windows szolgáltatások Server Sysprep ügyfélszolgálathoz csapat, akik elhárítása és a megfelelő Sysprep-használat tanácsokkal.


#### <a name="default-sysprep-command"></a>Sysprep parancs alapértelmezett
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Alapértelmezett Linux-deprovision parancsot

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>A parancsok felülbírálása
Felülbírálja a parancsok, nevű ugyanolyan, mint a parancs-fájlok létrehozása a PowerShell vagy Shell script provisioners használja, és helyezze őket a megfelelő könyvtárak:

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Az Image Builder fogja beolvasni ezeket a parancsokat, ezek írja a AIB naplók "customization.log". Lásd: [hibaelhárítási](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) a naplók összegyűjtése.
 
## <a name="properties-distribute"></a>Tulajdonságok: terjesztése

Az Azure az Image Builder három telepítési célok támogatja: 

- **managedImage** – felügyelt rendszerkép.
- **sharedImage** – megosztott lemezkép-katalógusában.
- **Virtuális merevlemez** -VHD-t, a storage-fiókban.

Akkor is ugyanaz a konfiguráció cél típusa a lemezképet terjeszti, olvassa el a [példák](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Terjeszteni kívánt egynél több cél is rendelkezik, mert az Image Builder megőrzi-e olyan állapotban, minden terjesztési cél, amely lekérdezésével érhető el a `runOutputName`.  A `runOutputName` lekérdezheti, ha az objektum közzététele, hogy a terjesztés információ a terjesztési. Például lekérdezheti, ha a virtuális Merevlemezt, vagy régióban, ahol a rendszerkép verziószámát replikált helyét. Ez az, hogy minden terjesztési target tulajdonsága. A `runOutputName` egyes tartalomterjesztési cél egyedinek kell lennie.
 
### <a name="distribute-managedimage"></a>Terjesztése: managedImage

A kép kimeneti egy felügyelt rendszerképet erőforrás lesz.

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
 
Terjesztése tulajdonságai:
- **típus** – managedImage 
- **imageId** – erőforrás-Azonosítóját a cél lemezképet, a várt formátum: /subscriptions/<subscriptionId>/resourceGroups/<destinationResourceGroupName>/providers/Microsoft.Compute/images/<imageName>
- **hely** – a felügyelt lemezkép helyét.  
- **runOutputName** – egyedi terjesztési azonosítására szolgáló név.  
- **artifactTags** – nem kötelező felhasználó által megadott kulcs-érték pár címkék.
 
 
> [!NOTE]
> A cél erőforráscsoport léteznie kell.
> Ha azt szeretné, hogy a kép egy másik régióba elosztott, növeli az üzembe helyezési időt. 

### <a name="distribute-sharedimage"></a>Terjesztése: sharedImage 
Az Azure Shared lemezkép-katalógusában egy új rendszerképet felügyeleti szolgáltatás, amely lehetővé teszi a lemezkép régió replikációt, felügyeletét verziókezelés és egyéni rendszerképek megosztása. Az Azure az Image Builder támogatja terjesztése ezzel a szolgáltatással, így megosztott kép katalógusok által támogatott régiók lemezképek terjesztése. 
 
Egy megosztott képgyűjtemény tevődik össze: 
 
- Katalógus - tároló több megosztott rendszerképeket. Egy gyűjtemény egy adott régióban üzemel.
- Lemezkép-definíciók – rendszerképek fogalmi csoportosítását. 
- Lemezkép verziója – Ez az egy lemezkép típusa, a virtuális gép vagy a méretezési csoport üzembe helyezéséhez használt. Lemezkép verziója más régiókban, ahol a virtuális gépeket kell üzembe helyezni lehet replikálni.
 
A lemezkép-katalógusban való terjesztése, előtt kell hozza létre a katalógus és a egy rendszerkép definíciójában, lásd: [lemezképek megosztott](shared-images.md). 

```json
{
     "type": "sharedImage",
     "galleryImageId": “<resource ID>”,
     "runOutputName": "<name>",
     "artifactTags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
     "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]}
``` 

Megosztott kép katalógusok tulajdonságainak terjeszteni:

- **típus** -sharedImage  
- **galleryImageId** – megosztott lemezkép-katalógus azonosítója. A formátum: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>.
- **runOutputName** – egyedi terjesztési azonosítására szolgáló név.  
- **artifactTags** – nem kötelező felhasználó által megadott kulcs-érték pár címkék.
- **replicationRegions** -replikáció régiók tömbje. A régió, ahol a katalógus üzemel egy régiót kell lennie.
 
> [!NOTE]
> A katalógusban, hogy egy másik régióban is használhatja az Azure az Image Builder, de az Azure az Image Builder szolgáltatás kell a lemezkép átvitele az adatközpontok közötti, és ez hosszabb időt vesz igénybe. Az Image Builder automatikusan az verzió a kép egy monoton egész szám alapján, jelenleg nem lehet megadni. 

### <a name="distribute-vhd"></a>Terjeszteni: VHD  
A virtuális merevlemez készíthető. Ezután másolja a VHD-t, és közzététele az Azure piactéren, vagy használja az Azure Stack használatával.  

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
 
Operációsrendszer-támogatást: Windows és Linux

Virtuális merevlemez paraméterek terjeszteni:

- **típus** -VHD-t.
- **runOutputName** – egyedi terjesztési azonosítására szolgáló név.  
- **a címkék** – nem kötelező felhasználó által megadott kulcs-érték pár címkék.
 
Az Azure az Image Builder nem engedélyezi a felhasználó megadhatja a tárfiók helye, de lekérdezheti az állapotát a `runOutputs` helyét.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> A virtuális merevlemez létrehozása után másolja egy másik helyre, amint lehetséges. A VHD-t az ideiglenes erőforráscsoport jön létre, amikor a lemezkép sablon elküldve az Azure az Image Builder-szolgáltatás a tárfiókban tárolja. Ha törli a lemezképet sablont, majd elveszíti a VHD-t. 
 
## <a name="next-steps"></a>További lépések

A különböző helyzetekhez minta .JSON kiterjesztésű fájlok vannak a [Azure kép Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder).
 
 
 
 
 
 
 
 
 
 
