---
title: Hozzon létre egy új lemezkép verziója a meglévő lemezkép verziója Azure Image Builder (előzetes verzió) használatával
description: Hozzon létre egy új lemezkép verziója egy meglévő lemezkép verziója, az Azure az Image Builder használatával.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 9155f6fc1243f0d2e4d63f2718ccfd6846ebbc50
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671505"
---
# <a name="preview-create-a-new-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Előzetes verzió: Hozzon létre egy új lemezkép verziója az Azure az Image Builder használatával meglévő lemezkép verziója

Ez a cikk bemutatja, hogyan vesz egy meglévő lemezkép verziója egy [megosztott képgyűjtemény](shared-image-galleries.md), frissítse és tegye közzé a katalógusban az új lemezkép verzióként.

Fogjuk használni .json mintasablon a kép konfigurálása. Itt van a .json fájlt használjuk: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


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


## <a name="set-variables-and-permissions"></a>Változók beállítása és engedélyek

Ha használt [hozzon létre egy rendszerképet, és a egy megosztott lemezkép-katalógus terjesztéséhez](image-builder-gallery.md) hozhat létre a megosztott lemezkép-katalógusában, már létrehozott egyes van szükségünk a változókat. Ha nem, állítsa be az egyes ebben a példában használt változókat.

Az előzetes verzió az image builder csak támogatja ugyanazt az erőforráscsoportot, a felügyelt forráslemezkép az egyéni lemezképek létrehozásának. Az erőforráscsoport neve ebben a példában ugyanazt az erőforráscsoportot, a felügyelt forráslemezkép kell frissíteni.


```azurecli-interactive
# Resource group name 
sigResourceGroup=ibLinuxGalleryRG
# Gallery location 
location=westus2
# Additional region to replicate the image version to 
additionalregion=eastus
# Name of the shared image gallery 
sigName=myIbGallery
# Name of the image definition to use
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibSIGLinuxUpdate
```

Hozzon létre egy változót az előfizetés-azonosítóhoz. A használatával lekérheti `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

A rendszerkép verziószámát, amelyet frissíteni szeretne kaphat.

```
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```


Ha már rendelkezik saját közös lemezkép-katalógusában, és nem követte az előző példával, szüksége lesz az erőforráscsoport elérhesse, ezért hozzá tudjon férni a katalógusban az Image Builder engedélyeket.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>Példa helloImage módosítása
Áttekintheti a példa tudjuk, hogyan használhatja a .json fájlt itt: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) együtt a [Image Builder sablonreferenciája](image-builder-json.md). 


Töltse le a .json példát, és konfigurálja azt a változók a. 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json -o helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromSIG.json
```

## <a name="create-the-image"></a>A rendszerkép létrehozása

Küldje el a rendszerkép-konfiguráció a virtuális gép rendszerkép jelentéskészítő szolgáltatáshoz.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

Indítsa el a rendszerkép összeállítását.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

Várjon, amíg a rendszerkép állították össze, és a replikációt, mielőtt a következő lépéssel.


## <a name="create-the-vm"></a>Virtuális gép létrehozása

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgVm001 \
  --admin-username azureuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Hozzon létre egy SSH-kapcsolatot a virtuális géphez a virtuális gép nyilvános IP-cím használatával.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Megtekintheti a rendszerkép lett egy "üzenet, a Day" szabhatók testre, amint az SSH-kapcsolat létrejött.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Típus `exit` gombra kattintva zárja be az SSH-kapcsolat.

A kép most már elérhető verziókról a katalógusban szereplő is listázhatja.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>További lépések

Az ebben a cikkben használt .JSON kiterjesztésű fájl összetevőivel kapcsolatos további tudnivalókért lásd: [Image builder sablonreferenciája](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).