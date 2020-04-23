---
title: Windows virtuális gép létrehozása az Azure Image Builder segítségével (előzetes verzió)
description: Hozzon létre egy Windows virtuális gép az Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: 269b2f4674f2c99fc438c1a7be65e5660ca58d08
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869495"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Előzetes verzió: Hozzon létre egy Windows virtuális gép az Azure Image Builder

Ez a cikk bemutatja, hogyan hozhat létre egy testreszabott Windows-lemezképet az Azure VM Image Builder használatával. A cikkben szereplő példa [testreszabókat](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) használ a kép testreszabásához:
- PowerShell (ScriptUri) – [PowerShell-parancsfájl](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)letöltése és futtatása.
- Windows Restart - újraindítja a virtuális gép.
- PowerShell (inline) – egy adott parancs futtatása. Ebben a példában létrehoz egy könyvtárat `mkdir c:\\buildActions`a virtuális gép használatával.
- Fájl – másolja a fájlt a GitHubról a virtuális gépre. Ez a példa `c:\buildArtifacts\index.html` a [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) másolja a virtuális gépre.

Megadhat egy `buildTimeoutInMinutes`. Az alapértelmezett érték 240 perc, és növelheti a buildelési időt, hogy lehetővé tegye a hosszabb ideig futó buildek.

A lemezkép konfigurálásához egy .json mintasablont fogunk használni. Az általunk használt .json fájl itt van: [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


> [!IMPORTANT]
> Az Azure Image Builder jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)


## <a name="register-the-features"></a>A funkciók regisztrálása

Az Azure Image Builder az előzetes verzióban való használatához regisztrálnia kell az új funkciót.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Ellenőrizze a szolgáltatás regisztrációjának állapotát.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Ellenőrizze a regisztrációját.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Ha nem mondják, hogy regisztrált, futtassa a következőket:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="set-variables"></a>Változók beállítása

Mi lesz használ néhány információt többször, így hozunk létre néhány változótárolják ezt az információt.


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

Hozzon létre egy változót az előfizetés-azonosítóhoz. Tudod kap ez `az account show | grep id`használ .

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Ez az erőforráscsoport a lemezképkonfigurációs sablon összetevőjének és a lemezképnek a tárolására szolgál.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Engedélyek beállítása az erőforráscsoporthoz

Adjon a Képszerkesztő "közreműködő" számára a lemezkép létrehozásához az erőforráscsoportban. Enélkül a lemezkép összeállítása sikertelen lesz. 

Az `--assignee` érték a Képszerkesztő szolgáltatás alkalmazásregisztrációs azonosítója. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-image-configuration-template-example"></a>Példa a képkonfigurációs sablon letöltése

Egy paraméterezett képkonfigurációs sablont hoztak létre, hogy megpróbálja. Töltse le a példát .json fájlt, és konfigurálja a korábban beállított változókkal.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

Ezt a példát a terminálon is `vi`módosíthatja egy szövegszerkesztő vel, például a .

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> A forrásképhez mindig meg kell adni a `latest` [verziót,](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure)amelyet nem használhat.
> Ha hozzáadja vagy módosítja azt az erőforráscsoportot, ahová a lemezképet terjesztik, akkor az [engedélyeket be](#set-permissions-on-the-resource-group) kell állítaniaz erőforráscsoportra.
 
## <a name="create-the-image"></a>A rendszerkép létrehozása

Küldje el a lemezkép konfigurációját a VM Image Builder szolgáltatás

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Ha elkészült, ez egy sikeres üzenetet küld vissza `Image Builder Configuration Template` a `$imageResourceGroup`konzolra, és létrehoz egy a ban. Ez az erőforrás az erőforráscsoportban az Azure Portalon, ha engedélyezi a "Rejtett típusok megjelenítése" engedélyezés.

A háttérben az Image Builder egy átmeneti erőforráscsoportot is létrehoz az előfizetésében. Ez az erőforráscsoport a lemezkép létrehozásához használatos. Ez lesz ebben a formátumban:`IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Az átmeneti erőforráscsoportot nem törölheti közvetlenül. Először törölje a képsablon-összetevőt, így az átmeneti erőforráscsoport törlődik.

Ha a szolgáltatás hibát jelent a lemezképkonfigurációs sablon beküldése során:
-  Tekintse át ezeket [a hibaelhárítási](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) lépéseket. 
- A küldés újbóli megkísérlése előtt törölnie kell a sablont a következő kódrészlet használatával.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>A kép összeállításának megkezdése
Indítsa el a lemezkép-építési folyamatot [az erőforrás-meghívási művelet](/cli/azure/resource#az-resource-invoke-action)használatával.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Várjon, amíg a build befejeződik. Ez körülbelül 15 percet is igénybe vehet.

Ha bármilyen hibát észlel, olvassa el ezeket [a hibaelhárítási](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) lépéseket.


## <a name="create-the-vm"></a>Virtuális gép létrehozása

Hozza létre a virtuális gép a létrehozott lemezkép használatával. Cserélje * \<* le a jelszót>`aibuser` a saját jelszavával a virtuális gépen.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>A testreszabás ellenőrzése

Hozzon létre egy távoli asztali kapcsolatot a virtuális géppel a virtuális gép létrehozásakor megadott felhasználónév és jelszó használatával. A virtuális gépbelsejében nyisson meg egy cmd-s kérdést, és írja be a következőt:

```console
dir c:\
```

A kép testreszabása során létrehozott két könyvtárnak meg kell jelennie:
- buildActions (buildActions)
- buildArtifacts (műtárgyak)

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha elkészült, törölje az erőforrásokat.

### <a name="delete-the-image-builder-template"></a>A képszerkesztő sablon törlése

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-image-resource-group"></a>A képerőforrás-csoport törlése

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>További lépések

A cikkben használt .json fájl összetevőiről a [Képszerkesztő sablon hivatkozása című témakörben olvashat bővebben.](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
