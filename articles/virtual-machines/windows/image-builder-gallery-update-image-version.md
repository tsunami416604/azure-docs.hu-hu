---
title: Hozzon létre egy új lemezkép verziója a meglévő lemezkép verziója Azure Image Builder (előzetes verzió) használatával
description: Hozzon létre egy új lemezkép verziója egy meglévő lemezkép verziója, az Azure az Image Builder használatával.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: d60a7680bc283ba015d0649fb1d2671f8e5cf793
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718634"
---
# <a name="preview-create-a-new-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Előzetes verzió: Hozzon létre egy új lemezkép verziója az Azure az Image Builder használatával meglévő lemezkép verziója

Ez a cikk bemutatja, hogyan vesz egy meglévő lemezkép verziója egy [megosztott képgyűjtemény](shared-image-galleries.md), frissítse és tegye közzé a katalógusban az új lemezkép verzióként.

Fogjuk használni .json mintasablon a kép konfigurálása. Itt van a .json fájlt használjuk: [helloImageTemplateforSIGfromWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json). 

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
az provider show -n Microsoft.Compute | grep registrationState
```

Ha ezek nem tegyük fel, hogy regisztrált, futtassa a következő parancsot:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```


## <a name="set-variables-and-permissions"></a>Változók beállítása és engedélyek

Ha használt [hozzon létre egy rendszerképet, és a egy megosztott lemezkép-katalógus terjesztéséhez](image-builder-gallery.md) hozhat létre a megosztott lemezkép-katalógusában, már létrehozott ellenőriznünk kell a változókat. Ha nem, állítsa be az egyes ebben a példában használt változókat.

Az előzetes verzió az image builder csak támogatja ugyanazt az erőforráscsoportot, a felügyelt forráslemezkép az egyéni lemezképek létrehozásának. Az erőforráscsoport neve ebben a példában ugyanazt az erőforráscsoportot, a felügyelt forráslemezkép kell frissíteni.

```azurecli-interactive
# Resource group name - we are using ibsigRG in this example
sigResourceGroup=myIBWinRG
# Datacenter location - we are using West US 2 in this example
location=westus
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=my22stSIG
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=winSvrimages
# image distribution metadata reference name
runOutputName=w2019SigRo
# User name and password for the VM
username="user name for the VM"
vmpassword="password for the VM"
```

Hozzon létre egy változót az előfizetés-azonosítóhoz. A használatával lekérheti `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

A rendszerkép verziószámát, amelyet frissíteni szeretne kaphat.

```azurecli-interactive
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
Áttekintheti a példa tudjuk, hogyan használhatja a .json fájlt itt: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) együtt a [Image Builder sablonreferenciája](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 


Töltse le a .json példát, és konfigurálja azt a változók a. 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>A rendszerkép létrehozása

Küldje el a rendszerkép-konfiguráció a virtuális gép rendszerkép jelentéskészítő szolgáltatáshoz.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

Indítsa el a rendszerkép összeállítását.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

Várjon, amíg a rendszerkép állították össze, és a replikációt, mielőtt a következő lépéssel.


## <a name="create-the-vm"></a>Virtuális gép létrehozása

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```

## <a name="verify-the-customization"></a>Ellenőrizze a testreszabása
Hozzon létre egy távoli asztali kapcsolatot a virtuális Gépet, a felhasználónév és a virtuális gép létrehozásakor beállított jelszó használatával. A virtuális gép nyisson meg egy parancssort, írja be:

```console
dir c:\
```

Meg kell jelennie két címtárat:
- `buildActions` az első rendszerkép verziója, amely sikeresen létrehozva.
- `buildActions2` hogy fel a második kép verzió létrehozásához az első lemezkép-verzió frissítése során jött létre.


## <a name="next-steps"></a>További lépések

Az ebben a cikkben használt .JSON kiterjesztésű fájl összetevőivel kapcsolatos további tudnivalókért lásd: [Image builder sablonreferenciája](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).