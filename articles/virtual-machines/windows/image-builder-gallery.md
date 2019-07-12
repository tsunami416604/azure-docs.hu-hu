---
title: Az Image Builder Azure lemezkép-katalógus használata Windows virtuális gépek (előzetes verzió)
description: Windows-rendszerképek létrehozása az Azure az Image Builder és megosztott lemezkép-katalógusában.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: 164fc4d8ad567c75ed5029aaf26af260398f80ba
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722700"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Előzetes verzió: Windows-lemezkép létrehozása és a egy megosztott Képkatalógus kioszthatja 

Ez a cikk az, hogy bemutatják, hogyan használhatja az Azure az Image Builder-lemezkép verziója a létrehozása egy [megosztott képgyűjtemény](shared-image-galleries.md), majd terjessze globálisan a lemezképet.

Fogjuk használni egy .JSON kiterjesztésű sablon konfigurálásához a lemezképet. Itt van a .json fájlt használjuk: [helloImageTemplateforWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json). 

A lemezkép egy megosztott lemezkép-katalógus terjesztéséhez, használja a sablon [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) értékeként a `distribute` szakaszában a sablont.

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

Fogjuk használni néhány információt ismételten, ezért hozunk létre változókat, amelyek adott információk tárolására. Cserélje le az értékeket a változók, például `username` és `vmpassword`, a saját adataira.

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
username="azureuser"
vmpassword="passwordfortheVM"
```

Hozzon létre egy változót az előfizetés-azonosítóhoz. A használatával lekérheti `az account show | grep id`.

```azurecli-interactive
subscriptionID="Subscription ID"
```

Hozza létre az erőforráscsoportot.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Az Azure az Image Builder engedélyt az adott erőforráscsoportba tartozó erőforrások létrehozásához. A `--assignee` értéke az Image Builder szolgáltatás regisztrációs Azonosítót. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>Egy rendszerkép definíciójában és gyűjtemény létrehozása

Hozzon létre egy rendszerkép-katalógusában. 

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Hozzon létre egy rendszerkép definíciójában.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher corpIT \
   --offer myOffer \
   --sku 2019 \
   --os-type Windows
```


## <a name="download-and-configure-the-json"></a>Töltse le és konfigurálja a .json

Töltse le a .json sablont, és konfigurálja azt a változók a.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json -o helloImageTemplateforWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforWinSIG.json
```

## <a name="create-the-image-version"></a>A lemezkép-verzió létrehozása

A következő részére, amelyben létrehozza a rendszerkép verziószámát a katalógusban. 

Küldje el a rendszerkép-konfiguráció az Azure az Image Builder szolgáltatásba.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Indítsa el a rendszerkép összeállítását.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforWinSIG01 \
     --action Run 
```

Hozza létre a, és replikálja azt mindkét régióban eltarthat egy ideig. Várjon, amíg ez a rész befejeződik, mielőtt továbblép a virtuális gép létrehozása.


## <a name="create-the-vm"></a>Virtuális gép létrehozása

Virtuális gép létrehozása az Azure az Image Builder által létrehozott lemezkép verziója.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm001 \
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

Megtekintheti az nevű könyvtárat `buildActions` , hogy a lemezkép-testreszabási során jött létre.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha most megpróbálja újra testreszabása a rendszerkép verziószámát ugyanazt a lemezképet, az új verzió létrehozásához **kihagyhatja ezt a lépést** , és folytassa a következővel: [használata az Azure az Image Builder hozhat létre egy másik lemezkép verziója](image-builder-gallery-update-image-version.md).


Ez a művelet törli a lemezképet, amely lett létrehozva, és a más erőforrásfájlokhoz valamennyi. Ellenőrizze, hogy befejezte a központi telepítés az erőforrások törlése előtt.

Kép galéria-erőforrásai törlésekor törölnie kell minden, a lemezkép-verzió, mielőtt törölheti a létrehozásukhoz használt rendszerkép definíciójában. Egy gyűjtemény törléséhez először törölni kell a lemezkép-definíciókat a katalógusban található összes.

A kép builder sablon törlése.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

A rendszerkép verziószámát az image builder által létrehozott, a mindig elindul a `0.`, majd törölnie kell a lemezkép verziója

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


Törölje a rendszerkép definíciójában.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Törölje a katalógusban.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Törölje az erőforráscsoportot.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>További lépések

A rendszerkép verziószámát létrehozott frissítésével kapcsolatban lásd: [használata az Azure az Image Builder hozhat létre egy másik lemezkép verziója](image-builder-gallery-update-image-version.md).