---
title: Windows rendszerű virtuális gép létrehozása az Azure rendszerkép-készítővel (előzetes verzió)
description: Hozzon létre egy Windows rendszerű virtuális gépet az Azure rendszerkép-szerkesztővel.
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: 9dc4909db5560be6eb082dbad85d4b2d42113bdd
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828691"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Előzetes verzió: Windows rendszerű virtuális gép létrehozása az Azure rendszerkép-készítővel

Ebből a cikkből megtudhatja, hogyan hozhat létre testreszabott Windows-rendszerképeket az Azure VM rendszerkép-készítő használatával. A cikkben szereplő példa testreszabók [](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) használatával testreszabja a rendszerképet:
- PowerShell (ScriptUri) – [PowerShell-parancsfájl](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)letöltése és futtatása.
- Windows újraindítása – újraindítja a virtuális gépet.
- PowerShell (beágyazott) – adott parancs futtatása. Ebben a példában egy könyvtárat hoz létre a virtuális gépen a használatával `mkdir c:\\buildActions`.
- Fájl – másolja a fájlt a GitHubról a virtuális gépre. Ez a példa [](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) a index.MD `c:\buildArtifacts\index.html` másolja a virtuális gépre.

A `buildTimeoutInMinutes`is megadható. Az alapértelmezett érték 240 perc, és növelheti a felépítési időt, így a már futó buildek is elérhetővé válik.

A rendszerkép konfigurálásához egy minta. JSON sablont fogunk használni. Az általunk használt. JSON fájl a következő: [helloImageTemplateWin. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


> [!IMPORTANT]
> Az Azure rendszerkép-szerkesztő jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="register-the-features"></a>A szolgáltatások regisztrálása

Ha az előzetes verzióban szeretné használni az Azure képszerkesztőt, regisztrálnia kell az új szolgáltatást.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

A szolgáltatás regisztrációjának állapotát vizsgálja meg.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Győződjön meg a regisztrációról.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Ha nem mondják a regisztrációt, futtassa a következőt:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="set-variables"></a>Változók beállítása

Többször is fogjuk használni az adatokat, így az adatok tárolására néhány változót fogunk létrehozni.


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

Hozzon létre egy változót az előfizetés-AZONOSÍTÓhoz. Ezt a következővel `az account show | grep id`érheti el:.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Ez az erőforráscsoport a rendszerkép-konfigurációs sablon és a rendszerkép tárolására szolgál.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Az erőforráscsoport engedélyeinek beállítása

Adja meg a rendszerkép-készítő "közreműködő" engedélyt a rendszerkép létrehozásához az erőforráscsoporthoz. Enélkül a rendszerkép létrehozása sikertelen lesz. 

Az `--assignee` érték a rendszerkép-szerkesztő szolgáltatáshoz tartozó alkalmazás-regisztrációs azonosító. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-image-configuration-template-example"></a>Példa a rendszerkép konfigurációs sablonjának letöltésére

Egy paraméteres rendszerkép-konfigurációs sablon lett létrehozva a kipróbáláshoz. Töltse le a example. JSON fájlt, és konfigurálja a korábban beállított változókkal.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

Ezt a példát módosíthatja a terminálon egy szövegszerkesztővel, például `vi`a paranccsal.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> A forrás rendszerkép esetében mindig [meg kell adnia egy verziót](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), amelyet nem használhat `latest`.
> Ha hozzáadja vagy megváltoztatja azt az erőforráscsoportot, amelyben a rendszerkép el van terjesztve, meg kell adnia az [engedélyeket](#set-permissions-on-the-resource-group) az erőforráscsoporthoz.
 
## <a name="create-the-image"></a>A rendszerkép létrehozása

A rendszerkép konfigurációjának elküldése a VM rendszerkép-készítő szolgáltatásnak

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Ha elkészült, a rendszer visszaküldi a sikert jelző üzenetet a-konzolra `Image Builder Configuration Template` , és `$imageResourceGroup`létrehoz egy-t a alkalmazásban. Ezt az erőforrást az erőforráscsoport Azure Portalban tekintheti meg, ha engedélyezi a "rejtett típusok megjelenítése" beállítását.

A háttérben a rendszerkép-szerkesztő létrehoz egy átmeneti erőforráscsoportot is az előfizetésében. Ez az erőforráscsoport a rendszerkép létrehozásához használatos. Ez a következő formátumban fog megjelenni:`IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Az átmeneti erőforráscsoportot nem szabad közvetlenül törölni. Először törölje a rendszerkép-sablon összetevőt, így az átmeneti erőforrás-csoport törölve lesz.

Ha a szolgáltatás hibát jelez a rendszerkép-konfigurációs sablon beküldésekor:
-  Tekintse [](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) át ezeket a hibaelhárítási lépéseket. 
- A Küldés megkísérlése előtt törölnie kell a sablont a következő kódrészlet használatával.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>A rendszerkép létrehozásának elindítása
A rendszerkép-létrehozási folyamat elindítása az [az Resource meghívása-Action](/cli/azure/resource#az-resource-invoke-action)paranccsal.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Várjon, amíg a Build befejeződik. Ez körülbelül 15 percet vesz igénybe.

Ha bármilyen hibát tapasztal, tekintse át ezeket [](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) a hibaelhárítási lépéseket.


## <a name="create-the-vm"></a>Virtuális gép létrehozása

Hozza létre a virtuális gépet a létrehozott rendszerkép használatával. Cserélje le  *\<a jelszó >* a `aibuser` saját jelszavával a virtuális gépre.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>A Testreszabás ellenőrzése

Hozzon létre egy Távoli asztal-csatlakozást a virtuális géphez a virtuális gép létrehozásakor beállított Felhasználónév és jelszó használatával. A virtuális gépen nyisson meg egy parancssort, és írja be a következőt:

```console
dir c:\
```

A rendszerkép testreszabása során létrehozott két könyvtárat kell látnia:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha elkészült, törölje az erőforrásokat.

### <a name="delete-the-image-builder-template"></a>A rendszerkép-szerkesztő sablonjának törlése
```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-image-resource-group"></a>A rendszerkép-erőforráscsoport törlése
```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a cikkben használt. JSON fájl összetevőiről, tekintse meg a [rendszerkép-szerkesztői sablon referenciáját](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
