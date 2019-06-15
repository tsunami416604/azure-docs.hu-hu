---
title: Windows virtuális gép létrehozása az Azure az Image Builder (előzetes verzió)
description: Hozzon létre egy Windows virtuális gép az Azure az Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: jeconnoc
ms.openlocfilehash: 01109aa83c12bda9b1d21ec25784d663f8abf700
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65159720"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Előzetes verzió: Egy Windows virtuális gép létrehozása az Azure az Image Builder

Ez a cikk az bemutatják, hogyan hozhat létre egy testre szabott Windows-lemezképet az Azure virtuális gép az Image Builder használatával. Az ebben a cikkben szereplő példa három különböző [testreszabók](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) testreszabásához a lemezkép:
- PowerShell (ScriptUri) – töltse le és futtassa a [PowerShell-parancsprogram](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Windows-újraindítás – a virtuális gép újraindul.
- PowerShell (beágyazott) – egy adott parancs futtatásához. Ebben a példában létrehoz egy könyvtárat a virtuális gép használatával `mkdir c:\\buildActions`.
- Fájl – a fájl másolása a Githubról be a virtuális Gépre. Ebben a példában másolja [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) való `c:\buildArtifacts\index.html` a virtuális gépen.

Fogjuk használni .json mintasablon a kép konfigurálása. Itt van a .json fájlt használjuk: [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


> [!IMPORTANT]
> Az Azure az Image Builder jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="register-the-features"></a>Regisztráció az funkciók

Az előzetes verzió ideje alatt az Azure az Image Builder használatához, regisztrálnia kell az új szolgáltatást.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

A szolgáltatás regisztrációs állapotának ellenőrzése.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Ellenőrizze a regisztrációt.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Ha ezek nem tegyük fel, hogy regisztrált, futtassa a következő parancsot:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Fogjuk használni néhány információt ismételten, ezért hozunk létre változókat, amelyek adott információk tárolására.

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

Hozzon létre egy változót az előfizetés-azonosítóhoz. A használatával lekérheti `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Hozza létre az erőforráscsoportot.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

Az Image Builder engedélyt az adott erőforráscsoportba tartozó erőforrások létrehozásához. A `--assignee` értéke az Image Builder szolgáltatás regisztrációs Azonosítót. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-json-example"></a>Töltse le a .json példa

Töltse le a példában .JSON kiterjesztésű fájlt, és konfigurálja azt a létrehozott változókat.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

## <a name="create-the-image"></a>A rendszerkép létrehozása

Küldje el a rendszerkép-konfiguráció a virtuális gép az Image Builder-szolgáltatáshoz

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Indítsa el a rendszerkép összeállítását.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Várjon, amíg a build elkészült. Ez nagyjából 15 percet is igénybe vehet.

## <a name="create-the-vm"></a>Virtuális gép létrehozása

Hozzon létre a virtuális gép az Ön által készített lemezképből. Cserélje le *<password>* a saját jelszavát a `aibuser` a virtuális gépen.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Ellenőrizze a testreszabása

Hozzon létre egy távoli asztali kapcsolatot a virtuális Gépet, a felhasználónév és a virtuális gép létrehozásakor beállított jelszó használatával. A virtuális gép nyisson meg egy parancssort, írja be:

```console
dir c:\
```

A lemezkép-testreszabási során létrehozott két könyvtárak kell megjelennie:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha elkészült, törölheti az erőforrást.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
az group delete -n $imageResourceGroup
```

## <a name="next-steps"></a>További lépések

Az ebben a cikkben használt .JSON kiterjesztésű fájl összetevőivel kapcsolatos további tudnivalókért lásd: [Image builder sablonreferenciája](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

