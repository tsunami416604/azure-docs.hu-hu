---
title: Az Image Builder Azure lemezkép-katalógus használata Linux rendszerű virtuális gépek (előzetes verzió)
description: Hozzon létre Linux-rendszerképeket az Azure az Image Builder és megosztott lemezkép-katalógusában.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: d29fa8700cb1f530cfe85f0bdf6852d75ec1613e
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508154"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Előzetes verzió: Hozzon létre egy Linuxos rendszerképet, és továbbítsa azt a megosztott lemezkép-katalógusában 

Ez a cikk bemutatja, hogyan használhatja az Azure az Image Builder-lemezkép verziója a létrehozása egy [megosztott képgyűjtemény](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries), majd terjessze globálisan a lemezképet.


Fogjuk használni .json mintasablon a kép konfigurálása. Itt van a .json fájlt használjuk: [helloImageTemplateforSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

A lemezkép egy megosztott lemezkép-katalógus terjesztéséhez, használja a sablon [sharedImage](image-builder-json.md#distribute-sharedimage) értékeként a `distribute` szakaszában a sablont.

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

## <a name="set-variables-and-permissions"></a>Változók beállítása és engedélyek 

Fogjuk használni néhány információt ismételten, ezért hozunk létre változókat, amelyek adott információk tárolására.

Az előzetes verzió az image builder csak támogatja ugyanazt az erőforráscsoportot, a felügyelt forráslemezkép az egyéni lemezképek létrehozásának. Az erőforráscsoport neve ebben a példában ugyanazt az erőforráscsoportot, a felügyelt forráslemezkép kell frissíteni.

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

Hozzon létre egy változót az előfizetés-azonosítóhoz. A használatával lekérheti `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
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
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>Töltse le és konfigurálja a .json

Töltse le a .json sablont, és konfigurálja azt a változók a.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>A lemezkép-verzió létrehozása

A következő részére, amelyben létrehozza a rendszerkép verziószámát a katalógusban. 

Küldje el a rendszerkép-konfiguráció az Azure az Image Builder szolgáltatásba.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Indítsa el a rendszerkép összeállítását.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

Hozza létre a, és replikálja azt mindkét régióban eltarthat egy ideig. Várjon, amíg ez a rész befejeződik, mielőtt továbblép a virtuális gép létrehozása.


## <a name="create-the-vm"></a>Virtuális gép létrehozása

Virtuális gép létrehozása az Azure az Image Builder által létrehozott lemezkép verziója.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

SSH-t a virtuális Gépet.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

Megtekintheti a lemezkép testre lett egy *üzenet a nap* , amint létrejön az SSH-kapcsolat!

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha szeretne próbáljuk meg a lemezkép-verziót, hogy ugyanaz a rendszerkép új verzióját hozza létre újra testreszabása, hagyja ki a következő lépéseket, és folytassa a [használata az Azure az Image Builder hozhat létre egy másik lemezkép verziója](image-builder-gallery-update-image-version.md).


Ez a művelet törli a lemezképet, amely lett létrehozva, és a más erőforrásfájlokhoz valamennyi. Ellenőrizze, hogy befejezte a központi telepítés az erőforrások törlése előtt.

Kép galéria-erőforrásai törlésekor törölnie kell minden, a lemezkép-verzió, mielőtt törölheti a létrehozásukhoz használt rendszerkép definíciójában. Egy gyűjtemény törléséhez először törölni kell a lemezkép-definíciókat a katalógusban található összes.

A kép builder sablon törlése.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
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

Tudjon meg többet [Azure megosztott kép katalógusok](shared-image-galleries.md).