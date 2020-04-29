---
title: Azure rendszerkép-készítő sablon létrehozása (előzetes verzió)
description: Megtudhatja, hogyan hozhat létre sablont az Azure rendszerkép-készítővel való használatra.
author: danis
ms.author: danis
ms.date: 03/24/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: e1f1bc09406c34836c13deb805fa399ab4751d41
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80246789"
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

Az `type` az erőforrástípus, amelynek a következőnek kell `"Microsoft.VirtualMachineImages/imageTemplates"`lennie:. Az `apiVersion` az idő múlásával változik, ahogy az API megváltozik, de `"2019-05-01-preview"` az előzetes verziónak kell lennie.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Hely

A hely az a régió, ahol az egyéni rendszerkép létre lesz hozva. A rendszerkép-készítő előzetes verziójában a következő régiók támogatottak:

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
## <a name="vmprofile"></a>vmProfile
Alapértelmezés szerint a rendszerkép-készítő egy "Standard_D1_v2" Build virtuális gépet használ, felülbírálhatja ezt, például ha egy GPU virtuális gép rendszerképét szeretné testre szabni, GPU-s virtuálisgép-méretre van szüksége. Ez nem kötelező.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Alapértelmezés szerint a képszerkesztő nem változtatja meg a rendszerkép méretét, a méretet a forrás rendszerképből fogja használni. Megnövelheti az operációsrendszer-lemez méretét (Win és Linux), ez nem kötelező, és a 0 érték azt jelenti, hogy a forrás képével megegyező méret marad. 

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Ha nem ad meg VNET-tulajdonságokat, akkor a rendszerkép-szerkesztő létrehozza a saját VNET, a nyilvános IP-címet és a NSG. A nyilvános IP-cím a szolgáltatásnak a Build virtuális géppel folytatott kommunikációhoz használatos, azonban ha nem szeretne nyilvános IP-címet használni, vagy szeretné, hogy a rendszerkép-készítő hozzáférjen a meglévő VNET-erőforrásokhoz, például a konfigurációs kiszolgálókhoz (DSC, Chef, Puppet, Ansible), fájlmegosztás stb., akkor megadhat egy VNET. További információkért tekintse át a [hálózati dokumentációt](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibNetworking.md#networking-with-azure-vm-image-builder), ez nem kötelező.

```json
    "vnetConfig": {
        "name": "<vnetName>",
        "subnetName": "<subnetName>",
        "resourceGroupName": "<vnetRgName>"
    }
```
## <a name="tags"></a>Címkék

Ezek a generált rendszerképhez megadható kulcs/érték párok.

## <a name="depends-on-optional"></a>Függ (nem kötelező)

Ez a választható szakasz használható annak biztosítására, hogy a folytatás előtt a függőségek befejeződik. 

```json
    "dependsOn": [],
```

További információ: az [erőforrás-függőségek meghatározása](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Identitás
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

A `source` szakasz a rendszerkép-szerkesztő által használt forrás-rendszerképről tartalmaz információkat.

Az API-nak szüksége van egy "forrás típusa" elemre, amely meghatározza a rendszerkép-Build forrását, jelenleg három típus létezik:
- PlatformImage – jelezte, hogy a forrás rendszerkép egy Piactéri rendszerkép.
- ManagedImage – ez a megoldás akkor használható, ha egy normál felügyelt rendszerképből indul.
- SharedImageVersion – ez akkor használatos, ha a rendszerkép verzióját megosztott képtárban használja forrásként.

### <a name="iso-source"></a>ISO-forrás
Ezt a funkciót elavultunk a rendszerkép-szerkesztőből, mivel most már [RHEL a saját előfizetéseit](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos), tekintse át az alábbi ütemterveket:
    * 31. március 2020 – a RHEL ISO-forrásokkal rendelkező képsablonokat az erőforrás-szolgáltató már el fogja fogadni.
    * 30. április 2020 – a RHEL ISO-forrásokat tartalmazó képsablonok többé nem lesznek feldolgozva.

### <a name="platformimage-source"></a>PlatformImage forrása 
Az Azure Image Builder a Windows Servert és az ügyfelet, valamint a Linux Azure Marketplace-rendszerképeket is támogatja, [itt](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support) találja a teljes listát. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


A virtuális gép létrehozásához használt tulajdonságok ugyanazok, mint az AZ CLI használatával, a tulajdonságok lekéréséhez futtassa az alábbi parancsot: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

A verzióban a legújabbat is használhatja, a verzió kiértékelése a lemezkép létrehozásakor történik, nem pedig a sablon elküldésekor. Ha ezt a funkciót a megosztott képkatalógus céljával használja, elkerülheti a sablon ismételt elküldését, és elvégezheti a lemezképek összeállítását időközökben, így a képek újra létrejönnek a legfrissebb rendszerképekből.

### <a name="managedimage-source"></a>ManagedImage forrása

A forrás rendszerképét egy általánosított virtuális merevlemez vagy virtuális gép meglévő felügyelt rendszerképeként állítja be. A forrásként felügyelt rendszerképnek támogatott operációs rendszernek kell lennie, és az Azure rendszerkép-készítő sablonnal azonos régióban kell lennie. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

Az `imageId` értéknek a felügyelt rendszerkép ResourceId kell lennie. Az `az image list` elérhető lemezképek listázásához használható.


### <a name="sharedimageversion-source"></a>SharedImageVersion forrása
Beállítja a forrás rendszerképet egy meglévő rendszerkép-verzióra egy megosztott rendszerkép-gyűjteményben. A rendszerkép verziószámának támogatott operációs rendszernek kell lennie, és a rendszerképet az Azure rendszerkép-készítő sablonnal azonos régióba kell replikálni. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

A `imageVersionId` ResourceId kell lennie a rendszerkép verziójának. A képverziók listázásához használja [az az SIG rendszerkép-Version List](/cli/azure/sig/image-version#az-sig-image-version-list) lehetőséget.

## <a name="properties-buildtimeoutinminutes"></a>Tulajdonságok: buildTimeoutInMinutes

Alapértelmezés szerint a rendszerkép-szerkesztő 240 percig fog futni. Ezt követően az időtúllépést és leállást eredményez, függetlenül attól, hogy befejeződött-e a rendszerkép létrehozása. Ha az időtúllépés eléri a találatot, a következőhöz hasonló hibaüzenet jelenik meg:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Ha nem ad meg buildTimeoutInMinutes értéket, vagy 0 értékre állítja, akkor ez az alapértelmezett értéket fogja használni. Növelheti vagy csökkentheti az értéket a maximális 960mins (16hrs). A Windows esetében nem ajánlott a 60 percnél régebbi beállítást beállítani. Ha megtalálta az időtúllépést, tekintse át a [naplókat](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs), és ellenőrizze, hogy a testreszabási lépés a felhasználói adatbevitelre vár-e. 

Ha úgy találja, hogy a testreszabások befejezéséhez több időre van szüksége, állítsa be ezt a kívánt értékre, és egy kis terheléssel. De ne állítsa túl magasra, mert előfordulhat, hogy meg kell várnia az időtúllépést, mielőtt hibaüzenetet lát. 


## <a name="properties-customize"></a>Tulajdonságok: testreszabás

A rendszerkép-szerkesztő több "testreszabó" használatát is támogatja. A testreszabók olyan függvények, amelyek a rendszerkép testreszabására szolgálnak, például parancsfájlok futtatására vagy kiszolgálók újraindítására. 

A használatakor `customize`: 
- Több testreszabó is használható, de egyedieknek `name`kell lenniük.
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

 
A Testreszabás szakasz egy tömb. Az Azure rendszerkép-szerkesztő szekvenciális sorrendben fut a testreszabók között. Bármely testreszabó hibája sikertelen lesz a létrehozási folyamat során. 
 
 
### <a name="shell-customizer"></a>Rendszerhéj-testreszabó

A rendszerhéj-testreszabó támogatja a rendszerhéj-parancsfájlok futtatását, ezeknek nyilvánosan elérhetőnek kell lenniük az IB-hez való hozzáféréshez.

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

- **típus** – rendszerhéj 
- **név** – a Testreszabás követésének neve 
- **scriptUri** – URI a fájl helyéhez 
- beépített rendszerhéj-parancsok **beágyazott** tömbje, vesszővel elválasztva.
- **sha256Checksum** – a fájl sha256-ellenőrzőösszegének értéke, ezt helyileg létrehozhatja, majd a rendszerkép-szerkesztő ellenőrzőösszeget és érvényesítést végez.
    * A sha256Checksum létrehozása Mac/Linux rendszeren futó terminál használatával:`sha256sum <fileName>`


A felügyelői jogosultságokkal futtatandó parancsokhoz előtaggal kell rendelkeznie `sudo`.

> [!NOTE]
> Ha a rendszerhéj-testreszabó a RHEL ISO-forrással futtatja, gondoskodnia kell arról, hogy az első testreszabási rendszerhéj a Red Hat jogosultsági kiszolgálóval való regisztrálást a testreszabások előtt is kezelje. A Testreszabás befejeződése után a parancsfájlnak meg kell szüntetnie a jogosultsági kiszolgáló regisztrációját.

### <a name="windows-restart-customizer"></a>Windows újraindítási testreszabó 
Az újraindítási testreszabó lehetővé teszi egy Windows rendszerű virtuális gép újraindítását, és várjon, amíg online állapotba kerül, így olyan szoftvereket telepíthet, amelyek újraindítást igényelnek.  

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
- **restartCommand** – az újraindítás végrehajtásához szükséges parancs (nem kötelező). A mező alapértelmezett értéke: `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – a parancs segítségével ellenőrizze, hogy az újraindítás sikeres volt-e (nem kötelező). 
- **restartTimeout** – újraindítási időkorlát, amely a magnitúdó és az egység értékének megfelelően van megadva. Például: `5m` (5 perc) vagy `2h` (2 óra). Az alapértelmezett érték: "5m"

### <a name="linux-restart"></a>Linux újraindítása  
Nincs Linux-újraindítási testreszabó, azonban ha olyan illesztőprogramokat vagy összetevőket telepít, amelyek újraindítást igényelnek, akkor telepítheti őket, és a rendszerhéj-testreszabó használatával meghívhatják az újraindítást a 20min SSH-időtúllépéssel a Build virtuális géphez.

### <a name="powershell-customizer"></a>PowerShell-testreszabó 
A rendszerhéj-testreszabó támogatja a PowerShell-parancsfájlok és a beágyazott parancsok futtatását, a parancsfájloknak nyilvánosan elérhetőnek kell lenniük az IB-hez való hozzáféréshez.

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
- **scriptUri** – URI a PowerShell-parancsfájl helyéhez. 
- **beágyazott** – a futtatandó beágyazott parancsok, vesszővel elválasztva.
- **validExitCodes** – választható, érvényes kódok, amelyek a parancsfájl/beágyazott parancsból visszaadhatók, így elkerülhető a parancsfájl/beágyazott parancs hibájának jelentése.
- **runElevated** – opcionális, logikai, támogatás emelt szintű engedélyekkel rendelkező parancsok és parancsfájlok futtatásához.
- **sha256Checksum** – a fájl sha256-ellenőrzőösszegének értéke, ezt helyileg létrehozhatja, majd a rendszerkép-szerkesztő ellenőrzőösszeget és érvényesítést végez.
    * A sha256Checksum létrehozása a Windows [Get-hash](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6) PowerShell használatával


### <a name="file-customizer"></a>Fájl-testreszabó

A fájl-testreszabó lehetővé teszi, hogy a rendszerkép-készítő letöltse a fájlt egy GitHub vagy egy Azure Storage szolgáltatásból. Ha olyan rendszerkép-létrehozási folyamattal rendelkezik, amely a Build-összetevőkre támaszkodik, akkor a fájl-testreszabó beállítható úgy, hogy letöltse a Build megosztásról, és áthelyezi az összetevőket a rendszerképbe.  

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

Fájl-testreszabó tulajdonságai:

- **sourceUri** – elérhető tárolási végpont, ez lehet GitHub vagy Azure Storage. Csak egy fájlt tölthet le, nem egy teljes könyvtárat. Ha le kell töltenie egy könyvtárat, használjon tömörített fájlt, majd bontsa ki a tömörítést a rendszerhéj vagy a PowerShell-testreszabók használatával. 
- **cél** – ez a célhely teljes elérési útja és fájlneve. Minden hivatkozott útvonalnak és alkönyvtárnak léteznie kell, a rendszerhéj vagy a PowerShell-testreszabók használatával előre beállíthatja ezeket. Az elérési út létrehozásához használhatja a parancsfájl-testreszabók lehetőséget. 

Ezt a Windows-címtárak és a Linux-elérési utak támogatják, de vannak különbségek: 
- Linux operációs rendszer – az egyetlen elérési út a rendszerkép-készítő a/tmp.
- Windows – nincs elérésiút-korlátozás, de az elérési útnak léteznie kell.
 
 
Ha hiba történt a fájl letöltése vagy egy megadott címtárba való behelyezése során, a testreszabási lépés sikertelen lesz, és ez a customization. log fájlban lesz.

> [!NOTE]
> A fájl-testreszabó csak kisméretű fájlok letöltésére alkalmas, < 20 MB-ot. A nagyobb fájlok letöltéséhez parancsfájl vagy beágyazott parancs használatos, a fájlok letöltésére szolgáló kód (például:, Linux `wget` vagy `curl`Windows `Invoke-WebRequest`).

A fájl-testreszabó fájljai az [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)használatával tölthetők le az Azure Storage-ból.

### <a name="windows-update-customizer"></a>Windows Update testreszabása
Ez a testreszabó a Packer [közösségi Windows Update-kiépítési](https://packer.io/docs/provisioners/community-supported.html) csomagjára épül, amely egy nyílt forráskódú projekt, amelyet a csomagoló Közösség tart fenn. A Microsoft a rendszerkép-készítő szolgáltatással teszteli és érvényesíti a kiépítő szolgáltatást, és támogatja a problémák megoldását, a Microsoft pedig hivatalosan nem támogatja a nyílt forráskódú projektet. A Windows Update-létesítéssel kapcsolatos részletes dokumentációt és segítséget a Project adattárában talál.
 
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
- **típus** – windowsupdate.
- **searchCriteria** – nem kötelező, meghatározza, hogy a rendszer milyen típusú frissítéseket telepítsen (ajánlott, fontos stb.), BrowseOnly = 0 és IsInstalled = 0 (ajánlott) az alapértelmezett érték.
- **szűrők** – nem kötelező, lehetővé teszi egy szűrő megadását a frissítések belefoglalásához vagy kizárásához.
- **updateLimit** – nem kötelező, meghatározza, hogy hány frissítést lehet telepíteni, alapértelmezett érték: 1000.
 
 

### <a name="generalize"></a>Általánossá tétel 
Alapértelmezés szerint az Azure-rendszerkép-készítő a rendszerkép testreszabási fázisának végén a "megszüntetés" kódot is futtatja, hogy "általánosítsa" a képet. Az általánosítás egy olyan folyamat, amelyben a rendszerkép be van állítva, így több virtuális gép létrehozására is felhasználható. Windows rendszerű virtuális gépek esetén az Azure rendszerkép-készítő a Sysprept használja. Linux rendszeren az Azure rendszerkép-szerkesztő "waagent"-megszüntetést futtat. 

Előfordulhat, hogy a rendszerkép-szerkesztő felhasználói általánosított parancsai nem alkalmasak minden helyzetben, így az Azure rendszerkép-készítő lehetővé teszi, hogy szükség esetén testreszabja ezt a parancsot. 

Ha meglévő testreszabást telepít át, és különböző Sysprep/waagent parancsokat használ, használhatja a rendszerkép-készítő általános parancsait, és ha a virtuális gép létrehozása nem sikerül, használja a saját Sysprep-vagy waagent-parancsait.

Ha az Azure rendszerkép-készítő sikeresen létrehoz egy egyéni Windows-rendszerképet, és létrehoz egy virtuális gépet, akkor a virtuális gép létrehozása sikertelen lesz, vagy nem fejeződik be sikeresen, át kell tekintenie a Windows Server Sysprep dokumentációját, vagy támogatási kérelmet kell létrehoznia a Windows Server Sysprep Customer Services támogatási csapatával, aki a megfelelő Sysprep-használattal kapcsolatos hibakeresést és tanácsadást nyújt.


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

Mivel több célhely is kiterjeszthető a szolgáltatásba, a rendszerkép-szerkesztő minden olyan terjesztési cél állapotát fenntartja, amely elérhető a `runOutputName`lekérdezéssel.  Az `runOutputName` egy olyan objektum, amelyről lekérdezheti a terjesztés utáni adatokat. Lekérdezheti például a virtuális merevlemez helyét, illetve azokat a régiókat, amelyeken a rendszerkép verziója replikálva lett, vagy a SIG-rendszerkép verziója létrejött. Ez az összes terjesztési cél tulajdonsága. A `runOutputName` -nek egyedinek kell lennie az egyes terjesztési célkitűzéseknél. Íme egy példa, amely egy megosztott képgyűjtemény-eloszlás lekérdezését szemlélteti:

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
- **imageId** – a célként megadott rendszerkép erőforrás-azonosítója, a várt formátum\<:/Subscriptions/subscriptionId\<>/resourcegroups/destinationResourceGroupName\<>/Providers/Microsoft.Compute/images/imageName>
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
- **galleryImageId** – a megosztott rendszerkép-Gyűjtemény azonosítója. A\<formátum:/subscriptions/subscriptionId>/resourcegroups/\<resourceGroupName>/Providers/Microsoft.Compute/Galleries/\<sharedImageGalleryName>/images/\<imageGalleryName>.
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
 
Az Azure rendszerkép-készítő nem teszi lehetővé a felhasználó számára a tárolási fiók helyének megadását, de lekérdezheti az állapotát `runOutputs` a hely lekéréséhez.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> A virtuális merevlemez létrehozása után a lehető leghamarabb másolja át egy másik helyre. A virtuális merevlemezt az ideiglenes erőforráscsoport tárolja, amely akkor jön létre, amikor a rendszer elküldi a képsablont az Azure rendszerkép-szerkesztő szolgáltatásba. Ha törli a képsablont, akkor elveszíti a VHD-t. 
 
## <a name="next-steps"></a>További lépések

Az [Azure rendszerkép-készítő githubon](https://github.com/danielsollondon/azvmimagebuilder)különböző forgatókönyvekhez készült minta. JSON fájlok találhatók.
 
