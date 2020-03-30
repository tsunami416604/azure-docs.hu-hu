---
title: Azure Image Builder sablon létrehozása (előzetes verzió)
description: Ismerje meg, hogyan hozhat létre sablont az Azure Image Builder használatával.
author: danis
ms.author: danis
ms.date: 03/24/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: e1f1bc09406c34836c13deb805fa399ab4751d41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246789"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Előzetes verzió: Azure Image Builder sablon létrehozása 

Az Azure Image Builder egy .json fájlt használ az Image Builder szolgáltatás információinak átadására. Ebben a cikkben átmegyünk a json fájl szakaszait, így sajátot építhetsz. A teljes .jsonfájlokpéldáit az [Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)című témakörben talál.

Ez az alapvető sablonformátum:

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
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "name": "<vnetName>",
                "subnetName": "<subnetName>",
                "resourceGroupName": "<vnetRgName>"
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Típus és API-verzió

Az `type` az erőforrástípus, amelynek a . `"Microsoft.VirtualMachineImages/imageTemplates"` Az `apiVersion` API változásakor idővel változik, de előzetes verzióban kell lennie. `"2019-05-01-preview"`

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Hely

A hely az a régió, ahol az egyéni lemezkép létrejön. A Képszerkesztő előzetes verziójában a következő régiók támogatottak:

- USA keleti régiója
- USA 2. keleti régiója
- USA nyugati középső régiója
- USA nyugati régiója
- USA nyugati régiója, 2.
- Észak-Európa
- Nyugat-Európa


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfil
Alapértelmezés szerint image builder fogja használni a "Standard_D1_v2" build virtuális gép, akkor felülbírálhatja ezt, például ha szeretné testre szabni egy képet a GPU virtuális gép, szüksége van egy GPU virtuális gép méretét. Ez nem kötelező.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Alapértelmezés szerint a Képszerkesztő nem változtatja meg a kép méretét, hanem a forráskép méretét fogja használni. Növelheti az operációs rendszer lemezének méretét (Win és Linux), ez nem kötelező, és a 0 érték azt jelenti, hogy a forráslemez méretével megegyező méretű marad. 

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Ha nem ad meg virtuális hálózat tulajdonságait, majd az Image Builder saját virtuális hálózatot, nyilvános IP-t és NSG-t hoz létre. A nyilvános IP-cím a szolgáltatás számára a build virtuális géppel való kommunikációhoz használatos, azonban ha nem szeretne nyilvános IP-címet, vagy ha azt szeretné, hogy a Képszerkesztő hozzáférjen a meglévő virtuális hálózat erőforrásaihoz, például konfigurációs kiszolgálókhoz (DSC, Chef, Puppet, Ansible), fájlmegosztásokhoz stb. , majd megadhatja a virtuális hálózat. További információért tekintse át a [hálózati dokumentációt,](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibNetworking.md#networking-with-azure-vm-image-builder)ez nem kötelező.

```json
    "vnetConfig": {
        "name": "<vnetName>",
        "subnetName": "<subnetName>",
        "resourceGroupName": "<vnetRgName>"
    }
```
## <a name="tags"></a>Címkék

Ezek kulcs/érték párok, amelyeket a létrehozott lemezképhez adhat meg.

## <a name="depends-on-optional"></a>Attól függ, hogy (nem kötelező)

Ez a választható szakasz annak biztosítására használható, hogy a függőségek befejeződtek a folytatás előtt. 

```json
    "dependsOn": [],
```

További információt az [Erőforrás-függőségek megadása című témakörben talál.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson)

## <a name="identity"></a>Identitás
Alapértelmezés szerint az Image Builder támogatja a parancsfájlok használatát, vagy fájlok másolása több helyről, például a GitHub és az Azure storage. Ezek használatához nyilvánosan hozzáférhetőnek kell lenniük.

Az Ön által meghatározott Azure-felhasználó által hozzárendelt felügyelt identitást is használhatja az Arcképszerkesztő Azure Storage-hozzáférés engedélyezéséhez, feltéve, hogy az identitás legalább "Storage Blob Data Reader" lehetőséget kapott az Azure-tárfiókban. Ez azt jelenti, hogy nem kell a tárolási blobok külsőleg elérhető, vagy a telepítő SAS-jogkivonatok.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

A teljes példa: [Azure-felhasználó által hozzárendelt felügyelt identitás az Azure Storage-fájlok eléréséhez.](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)

Képszerkesztő támogatása a felhasználó által rendelt identitáshoz: • Csak egyetlen identitást támogat • Nem támogatja az egyéni tartományneveket

További információ: [Mi az Azure-erőforrások felügyelt identitásai?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
A szolgáltatás üzembe helyezéséről további információt az [Azure-erőforrások felügyelt identitásának konfigurálása Azure-alapú virtuális gépeken az Azure CLI használatával című](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)témakörben talál.

## <a name="properties-source"></a>Tulajdonságok: forrás

A `source` szakasz az Image Builder által használt forrásképről tartalmaz információkat.

Az API-hoz egy "SourceType" szükséges, amely meghatározza a rendszerkép-összeállítás forrását, jelenleg három típus létezik:
- PlatformImage - jelezte, hogy a forráskép egy Marketplace-lemezkép.
- ManagedImage - használja ezt, ha egy normál felügyelt lemezképből indul.
- SharedImageVersion - ez akkor használatos, ha egy megosztott képtárban lévő képverziót használ forrásként.

### <a name="iso-source"></a>ISO forrás
Mi deprecating ezt a funkciót a képszerkesztő, mivel most [RHEL Bring Your Own Subscription képek](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos), kérjük, olvassa el az alábbi ütemtervek:
    * 2020. március 31.
    * 2020. április 30.

### <a name="platformimage-source"></a>PlatformImage forrás 
Az Azure Image Builder támogatja a Windows Server és az ügyfél, és a Linux Azure Marketplace-lemezképek, [lásd itt](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support) a teljes listát. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


Az itt található tulajdonságok megegyeznek a virtuális gépek létrehozásához használt, AZ CLI használatával, futtassa az alábbi tulajdonságokat: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

Használhatja a "legújabb" a verzióban, a verzió kiértékelése, amikor a rendszerkép-összeállítás történik, nem pedig a sablon elküldésekor. Ha ezt a funkciót a Megosztott képtár célwebhelyével használja, elkerülheti a sablon újbóli elküldését, és időnként újrafuttathatja a lemezképet, hogy a képek újra létrejönnek a legújabb képekből.

### <a name="managedimage-source"></a>ManagedImage forrás

A forráslemezképet egy általános virtuális merevlemez vagy virtuális gép meglévő felügyelt lemezképeként állítja be. A forrás felügyelt lemezképének egy támogatott operációs rendszerből kell származnia, és ugyanabban a régióban kell lennie, mint az Azure Image Builder sablon. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

A `imageId` kell a felügyelt rendszerkép ResourceId. Az `az image list` elérhető képek felsorolására használható.


### <a name="sharedimageversion-source"></a>SharedImageVersion forrás
A forráskép beállítja a megosztott képtár meglévő képverzióját. A rendszerkép verziójának támogatott operációs rendszerből kell rendelkeznie, és a lemezképet ugyanabba a régióba kell replikálni, mint az Azure Image Builder sablont. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

A `imageVersionId` kell a ResourceId a rendszerkép verziója. A képverziók listázásához használja [az sig image-version list(az sig image-version) listát.](/cli/azure/sig/image-version#az-sig-image-version-list)

## <a name="properties-buildtimeoutinminutes"></a>Tulajdonságok: buildTimeoutInMinutes

Alapértelmezés szerint a Képszerkesztő 240 percig fog futni. Ezt követően időtúltöltést és leállítási időt, függetlenül attól, hogy a rendszerkép összeállítása befejeződött-e vagy sem. Ha az időout levan ütve, a következőhöz hasonló hibaüzenet jelenik meg:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Ha nem ad meg buildTimeoutInMinutes értéket, vagy 0-ra állítja, akkor az alapértelmezett értéket fogja használni. Növelheti vagy csökkentheti az értéket, legfeljebb 960 perc (16 óra). Windows esetén ezt nem javasoljuk, hogy ezt 60 perc alatt tegyék meg. Ha úgy találja, hogy az időtúllépés, tekintse át a [naplókat,](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)hogy lássa, hogy a testreszabási lépés vár-e valami hasonló felhasználói bevitelre. 

Ha úgy találja, hogy több időre van szüksége a testreszabások befejezéséhez, állítsa be azt, amire úgy gondolja, hogy szüksége van, egy kis rezsivel. De ne állítsa túl magasra, mert előfordulhat, hogy meg kell várnia az időtúlértéket, mielőtt hibát látna. 


## <a name="properties-customize"></a>Tulajdonságok: testreszabás

Image Builder támogatja a több "testreszabók". A testreszabók olyan funkciók, amelyek a lemezkép testreszabására szolgálnak, például parancsfájlok futtatása vagy kiszolgálók újraindítása. 

A `customize`következők használata esetén: 
- Több testreszabót is használhat, de egyedi `name`.
- A testreszabók a sablonban megadott sorrendben hajtanak végre.
- Ha egy testreszabó meghibásodik, akkor a teljes testreszabási összetevő meghibásodik, és hibát jelez.
- Erősen ajánlott, hogy alaposan tesztelje a parancsfájlt, mielőtt sablonban használná. A parancsfájl saját virtuális gépén történő hibakeresés egyszerűbb lesz.
- Ne helyezzen bizalmas adatokat a parancsfájlokba. 
- A parancsfájlhelyeknek nyilvánosan hozzáférhetőnek kell lenniük, kivéve, ha [MSI-t](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)használ.

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
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

 
A testreszabási szakasz egy tömb. Az Azure Image Builder sorrendben fut végig a testreszabókon. A testreszabó bármely hibája sikertelen lesz a létrehozási folyamatsorán. 
 
 
### <a name="shell-customizer"></a>Shell-testreszabó

A rendszerhéj-testreszabó támogatja a rendszerhéj-parancsfájlok futtatását, ezeknek nyilvánosan hozzáférhetőnek kell lenniük ahhoz, hogy az IB hozzáférhessen azokhoz.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
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

- **típus** – Shell 
- **name** - név a testreszabás nyomon követéséhez 
- **scriptUri** - URI a fájl helyére 
- **inline** - tömb parancssor, vesszővel elválasztva.
- **sha256Checksum** - Értéke sha256 ellenőrző összeg a fájl, akkor ezt a helyileg, majd Image Builder ellenőrizni fogja, és érvényesíti.
    * A sha256Checksum létrehozásához mac/Linux terminál használatával:`sha256sum <fileName>`


Ahhoz, hogy a parancsok rendszergazdai jogosultságokkal `sudo`fussanak, előtaggal kell rendelkeznie a.

> [!NOTE]
> Ha rhel ISO-forrással futtatja a rendszerhéj-testreszabót, gondoskodnia kell arról, hogy az első testreszabási rendszerhéj-kezelő leírók regisztráljanak egy Red Hat jogosultsági kiszolgálón, mielőtt bármilyen testreszabás történik. A testreszabás befejezése után a parancsfájlnak törölnie kell a regisztrációt a jogosultsági kiszolgálóval.

### <a name="windows-restart-customizer"></a>Windows-újraválasztó 
Az újraindítás testreszabó lehetővé teszi, hogy indítsa újra a Windows VM, és várja meg, hogy jöjjön vissza online, ez lehetővé teszi, hogy telepítse a szoftvert, amely újraindítást igényel.  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

Operációs rendszer támogatása: Windows
 
Tulajdonságok testreszabása:
- **Típus**: WindowsRestart
- **restartCommand** - Parancs az újraindítás végrehajtásához (nem kötelező). A mező alapértelmezett értéke: `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – Parancs annak ellenőrzéséhez, hogy az újraindítás sikeres volt-e (nem kötelező). 
- **restartTimeout** - Újraindítás időout megadott karakterlánc nagysága és egysége. Például `5m` (5 perc) `2h` vagy (2 óra). Az alapértelmezett érték: "5m"

### <a name="linux-restart"></a>Linux újraindítás  
Nincs Linux restart customizer, azonban ha illesztőprogramokat telepít, vagy alkatrészeket, amelyek újraindítást igényelnek, telepítheti őket, és meghívja az újraindítást a Shell testreszabó, van egy 20 min SSH időtúltöltés a build virtuális gép.

### <a name="powershell-customizer"></a>PowerShell-testreszabó 
A rendszerhéj-testreszabó támogatja a PowerShell-parancsfájlok és a szövegközi parancs futtatását, a parancsfájlok nyilvánosan elérhetőnek kell lenniük az IB számára azok eléréséhez.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

Operációs rendszer támogatása: Windows és Linux

Tulajdonságok testreszabása:

- **típus** – PowerShell.
- **scriptUri** - URI a PowerShell parancsfájl helyére. 
- **inline** - Szövegközi parancsok futtatásához, vesszővel elválasztva.
- **validExitCodes** – Nem kötelező, érvényes kódok, amelyek a parancsfájl/szövegközi parancsból visszaadhatók, így elkerülhető a parancsfájl/inline parancs jelentett hibája.
- **runElevated** – Nem kötelező, logikai, a parancsok és parancsfájlok magas szintű engedélyekkel való futtatásának támogatása.
- **sha256Checksum** - Értéke sha256 ellenőrző összeg a fájl, akkor ezt a helyileg, majd Image Builder ellenőrizni fogja, és érvényesíti.
    * A sha256Checksum létrehozása, PowerShell használata Windows [Get-Hash rendszeren](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)


### <a name="file-customizer"></a>Fájltestreszabó

A Fájltestreszabó lehetővé teszi, hogy a képszerkesztő letöltsön egy fájlt egy GitHub- vagy Azure-tárhelyről. Ha rendelkezik egy lemezkép-összeállítási folyamattal, amely a buildösszetevőkre támaszkodik, beállíthatja, hogy a fájltestreszabó töltse le a buildmegosztásból, és helyezze át az összetevőket a lemezképbe.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
         }
     ]
```

Operációs rendszer támogatása: Linux és Windows 

Fájltestreszabó tulajdonságai:

- **sourceUri** – egy akadálymentes tárolási végpont, ez lehet GitHub vagy Azure storage. Csak egy fájlt tölthet le, egy teljes könyvtárat nem. Ha le kell töltenie egy könyvtárat, használjon tömörített fájlt, majd csomagolja ki a Shell vagy a PowerShell testreszabók használatával. 
- **cél** – ez a teljes célelérési út és fájlnév. Minden hivatkozott elérési út és alkönyvtárak léteznie kell, használja a Shell vagy a PowerShell testreszabók beállítani ezeket előre. A parancsfájl-testreszabók segítségével létrehozhatja az elérési utat. 

Ezt a Windows könyvtárak és a Linux-útvonalak is támogatják, de vannak különbségek: 
- Linux operációs rendszer - az egyetlen út Image builder írhat a / tmp.
- Windows – Nincs útvonalkorlátozás, de az elérési útnak léteznie kell.
 
 
Ha hiba történik a fájl letöltése koronként, vagy egy megadott könyvtárba helyezve, a testreszabási lépés sikertelen lesz, és ez a customization.log fájlban lesz.

> [!NOTE]
> A fájl testreszabó csak akkor alkalmas kis fájl letöltések, < 20MB. A nagyobb fájlletöltések parancsfájlt vagy szövegközi parancsot használnak, a `wget` kód `curl`használata `Invoke-WebRequest`a fájlok , például a Linux vagy a , a Windows, a .

A fájltestreszabóban lévő fájlok letölthetők az Azure Storage-ból az [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)használatával.

### <a name="windows-update-customizer"></a>Windows Update testreszabó
Ez a testreszabó a [nyilvános Windows Update Provisioner](https://packer.io/docs/provisioners/community-supported.html) for Packer-re épül, amely egy nyílt forráskódú projekt, amelyet a Packer-közösség tart fenn. A Microsoft teszteli és érvényesíti a kiépítőt a Képszerkesztő szolgáltatással, és támogatja a problémák kivizsgálását, és dolgozik a problémák megoldásán, azonban a nyílt forráskódú projektet a Microsoft hivatalosan nem támogatja. A Windows Update-kiépítő részletes dokumentációját és segítségét a projekttárban találja.
 
     "customize": [
            {
                "type": "WindowsUpdate",
                "searchCriteria": "IsInstalled=0",
                "filters": [
                    "exclude:$_.Title -like '*Preview*'",
                    "include:$true"
                            ],
                "updateLimit": 20
            }
               ], 
Operációs rendszer támogatása: Windows

Tulajdonságok testreszabása:
- **típus** – WindowsUpdate.
- **searchCriteria** - Nem kötelező, meghatározza, hogy milyen típusú frissítések vannak telepítve (Ajánlott, Fontos stb.), BrowseOnly=0 és IsInstalled=0 (Ajánlott) az alapértelmezett.
- **szűrők** – Nem kötelező, lehetővé teszi a frissítések felvételéhez vagy kizárásához szükséges szűrő megadását.
- **updateLimit** – Nem kötelező, meghatározza, hogy hány frissítés telepíthető, alapértelmezett 1000.
 
 

### <a name="generalize"></a>Általánossá tétel 
Alapértelmezés szerint az Azure Image Builder is fut "deprovision" kód végén minden kép testreszabási fázisban, a lemezkép "általánosítani". Általánosítás egy olyan folyamat, ahol a rendszerkép van beállítva, így újra fel lehet használni, hogy több virtuális gép létrehozása. Windows virtuális gépek esetén az Azure Image Builder a Sysprep programot használja. Linux esetén az Azure Image Builder a "waagent -deprovision" fut. 

Előfordulhat, hogy a képszerkesztő-felhasználók általánosításra vonatkozó parancsai nem minden helyzetre alkalmasak, ezért az Azure Image Builder lehetővé teszi, hogy szükség esetén testre szabja ezt a parancsot. 

Ha meglévő testreszabást telepít át, és különböző Sysprep/waagent parancsokat használ, használhatja a Képszerkesztő általános parancsait, és ha a virtuális gép létrehozása sikertelen, használja a saját Sysprep vagy waagent parancsokat.

Ha az Azure Image Builder sikeresen létrehoz egy Windows-rendszerképet, és virtuális gépről hoz létre, majd úgy találja, hogy a virtuális gép létrehozása sikertelen vagy nem fejeződik be sikeresen, át kell tekintenie a Windows Server Sysprep dokumentációját, vagy támogatási kérelmet kell benyújtania a Windows Server Sysprep ügyfélszolgálati csoport, amely a Sysprep megfelelő használatával kapcsolatos hibaelhárítást és tanácsadást tud nyújtani.


#### <a name="default-sysprep-command"></a>Alapértelmezett Sysprep parancs
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Alapértelmezett Linux-megszüntetési parancs

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>A parancsok felülbírálása
A parancsok felülbírálásához használja a PowerShell vagy a Shell parancsfájl-kiépítőket a parancsfájlok pontos fájlnévvel történő létrehozásához, és helyezze őket a megfelelő könyvtárakba:

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Image Builder fogja olvasni ezeket a parancsokat, ezek vannak írva ki az AIB naplók, "customization.log". Tekintse meg a naplók gyűjtésének [hibaelhárítását.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs)
 
## <a name="properties-distribute"></a>Tulajdonságok: terjesztés

Az Azure Image Builder három terjesztési célt támogat: 

- **managedImage** - felügyelt lemezkép.
- **sharedImage** - Megosztott képgaléria.
- **VHD** - VHD egy tárfiókban.

A képet mindkét céltípusra terjesztheti ugyanabban a konfigurációban, lásd a [példákat.](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80)

Mivel egynél több célhoz is terjeszthet, a Képszerkesztő minden olyan terjesztési célhoz fenntart `runOutputName`egy állapotot, amely a lekérdezésével érhető el.  Az `runOutputName` olyan objektum, amelyet a terjesztés utáni lekérdezéssel kérdezhet le az adott terjesztéssel kapcsolatos információkért. Lekérdezheti például a virtuális merevlemez helyét, vagy azokat a régiókat, ahol a lemezkép-verzió replikálása történt, vagy a SIG-lemezkép-verziót létrehozva. Ez minden elosztási célpont tulajdona. A `runOutputName` kell lennie az egyes terjesztési cél. Íme egy példa, ez a lekérdezés egy megosztott képtár disztribúció:

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2019-05-01-preview
```

Kimenet:
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

### <a name="distribute-managedimage"></a>Terjesztés: managedImage

A lemezkép kimenete felügyelt rendszerkép-erőforrás lesz.

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
 
Tulajdonságok elosztása:
- **típus** – managedImage 
- **imageId** – A célkép erőforrásazonosítója, a várt\<formátum: /subscriptions/\<subscriptionId>/resourceGroups/ destinationResourceGroupName\<>/providers/Microsoft.Compute/images/ imageName>
- **hely** - a felügyelt kép helye.  
- **runOutputName** – egyedi név a disztribúció azonosítására.  
- **artifactTags** - Nem kötelező felhasználó által megadott kulcsérték-pár címkék.
 
 
> [!NOTE]
> A célerőforrás-csoportnak léteznie kell.
> Ha azt szeretné, hogy a rendszerkép egy másik régióba kerüljön, az növeli a telepítési időt. 

### <a name="distribute-sharedimage"></a>Terjesztés: sharedImage 
Az Azure megosztott képtár egy új képkezelő szolgáltatás, amely lehetővé teszi a lemezképrégió replikációjának, verziószámozásának és egyéni lemezképek megosztásának kezelését. Az Azure Image Builder támogatja a szolgáltatás sal történő terjesztését, így képeket terjeszthet a megosztott képgalériák által támogatott régiókba. 
 
A megosztott képgaléria a következőkből áll: 
 
- Galéria – Tároló több megosztott lemezképekhez. Egy galéria egy régióban van telepítve.
- Képdefiníciók – a képek fogalmi csoportosítása. 
- Lemezkép-verziók – ez egy virtuális gép vagy méretezési csoport üzembe helyezéséhez használt lemezképtípus. A lemezképverziók más régiókba replikálhatók, ahol virtuális gépeket kell telepíteni.
 
A képtárba való terjesztés előtt létre kell hoznia egy galériát és egy képdefiníciót, [lásd: Megosztott képek](shared-images.md). 

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

A megosztott képgalériák tulajdonságainak terjesztése:

- **típus** - sharedImage  
- **galleryImageId** - A megosztott képgaléria azonosítója. A formátum:\</subscriptions/ subscriptionId>/resourceGroups/\<resourceGroupName>/providers/Microsoft.Compute/galleries/\<sharedImageGalleryName>/images/\<imageGalleryName>.
- **runOutputName** – egyedi név a disztribúció azonosítására.  
- **artifactTags** - Nem kötelező felhasználó által megadott kulcsérték-pár címkék.
- **replicationRegions** - Régiók tömbje replikációhoz. Az egyik régiónak annak a régiónak kell lennie, ahol a katalógus telepítve van.
 
> [!NOTE]
> Használhatja az Azure Image Builder egy másik régióban a katalógusban, de az Azure Image Builder szolgáltatás át kell vinnie a lemezképet az adatközpontok között, és ez hosszabb időt vesz igénybe. A Képszerkesztő automatikusan verziószámba veszi a képet egy monoton egész szám alapján, jelenleg nem adható meg. 

### <a name="distribute-vhd"></a>Terjesztés: VHD  
A virtuális merevlemezre is kimenetel. Ezután másolja a virtuális merevlemezt, és használja az Azure MarketPlace-ben való közzétételhez, vagy az Azure Stackhasználatával való használatra.  

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

VHD-paraméterek elosztása:

- **típus** - VHD.
- **runOutputName** – egyedi név a disztribúció azonosítására.  
- **címkék** - Nem kötelező a felhasználó által megadott kulcsérték-pár címkék.
 
Az Azure Image Builder nem teszi lehetővé a felhasználó számára, hogy `runOutputs` adja meg a tárfiók helyét, de lekérdezheti az állapotát a hely lekérdezéséhez.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Miután a virtuális merevlemez létrehozása, másolja egy másik helyre, a lehető leghamarabb. A virtuális merevlemez egy tárfiókban tárolja az ideiglenes erőforráscsoport ban létrehozott, amikor a rendszerképsablon elküldése az Azure Image Builder szolgáltatás. Ha törli a képsablont, akkor elveszíti a virtuális merevlemezt. 
 
## <a name="next-steps"></a>További lépések

Az [Azure Image Builder GitHubban](https://github.com/danielsollondon/azvmimagebuilder)a .json mintafájlok különböző forgatókönyvekhez tartoznak.
 
