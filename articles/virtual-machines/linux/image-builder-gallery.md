---
title: Az Azure Image Builder használata linuxos virtuális gépek képgalériájával (előzetes verzió)
description: Linuxos virtuálisgép-lemezképek et hozhat létre az Azure Image Builder és a Megosztott képtár segítségével.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: bf1dca61ec6b39e52d4f76c1c77cd3def6973ab8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945014"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Előzetes verzió: Linux-lemezkép létrehozása és terjesztése egy megosztott képtárban 

Ez a cikk bemutatja, hogyan használhatja az Azure Image Builder, és az Azure CLI, egy lemezkép-verzió létrehozása a [megosztott képtárban,](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)majd a lemezkép globális terjesztése. Ezt az Azure [PowerShell](../windows/image-builder-gallery.md)használatával is megteheti.


A lemezkép konfigurálásához egy .json mintasablont fogunk használni. Az általunk használt .json fájl itt van: [helloImageTemplateforSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

A kép megosztott képtárba való terjesztéséhez a sablon a `distribute` [sharedImage-et](image-builder-json.md#distribute-sharedimage) használja a sablon szakaszának értékeként.

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

## <a name="set-variables-and-permissions"></a>Változók és engedélyek beállítása 

Mi lesz használ néhány információt többször, így hozunk létre néhány változótárolják ezt az információt.

Az előnézeti verzió esetén a lemezképkészítő csak a forrás felügyelt lemezképpel azonos erőforráscsoportban támogatja az egyéni lemezképek létrehozását. Frissítse az erőforráscsoport nevét ebben a példában, hogy ugyanaz az erőforráscsoport legyen, mint a forrás felügyelt lemezképe.

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

Hozzon létre egy változót az előfizetés-azonosítóhoz. Tudod kap ez `az account show | grep id`használ .

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Hozza létre az erőforráscsoportot.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Adjon engedélyt az Azure Image Builder-nek az adott erőforráscsoportban lévő erőforrások létrehozásához. Az `--assignee` érték a Képszerkesztő szolgáltatás alkalmazásregisztrációs azonosítója. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>Képdefiníció és -gyűjtemény létrehozása

Ha megosztott képgalériával szeretné használni a Képszerkesztőt, rendelkeznie kell egy meglévő képgalériával és képdefinícióval. A Képszerkesztő nem hozza létre a képgalériát és a képdefiníciót.

Ha még nem rendelkezik katalógus- és képdefinícióval, először hozza létre őket. Először hozzon létre egy képgalériát.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Ezután hozzon létre egy képdefiníciót.

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


## <a name="download-and-configure-the-json"></a>A .json letöltése és konfigurálása

Töltse le a .json sablont, és konfigurálja a változókkal.

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

## <a name="create-the-image-version"></a>A lemezkép verziójának létrehozása

Ez a következő rész létrehozza a kép verzió a galériában. 

Küldje el a lemezkép konfigurációját az Azure Image Builder szolgáltatásba.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Indítsa el a kép felépítését.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

A lemezkép létrehozása és replikálása mindkét régióban eltarthat egy ideig. Várjon, amíg ez a rész befejeződik, mielőtt a virtuális gép létrehozása.


## <a name="create-the-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gép a lemezkép-verzió, amely az Azure Image Builder által létrehozott.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Csatlakozzon a virtuális géphez SSH-n keresztül.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

Látnia kell, hogy a kép a nap üzenetével lett *testreszabva, amint* az SSH kapcsolat létrejött!

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha most újra szeretné testreszabni a lemezkép-verziót, hogy ugyanannak a lemezképnek az új verzióját hozza létre, hagyja ki a következő lépéseket, és folytassa az [Azure Image Builder használatával egy másik lemezkép-verzió létrehozásához.](image-builder-gallery-update-image-version.md)


Ezzel törli a létrehozott lemezképet az összes többi erőforrásfájllal együtt. Győződjön meg arról, hogy befejezte ezt a központi telepítést az erőforrások törlése előtt.

A képgaléria erőforrásainak törlésekor törölnie kell az összes képverziót, mielőtt törölheti a létrehozásukhoz használt képdefiníciót. A gyűjtemény törléséhez először törölnie kell a gyűjtemény összes képdefinícióját.

Törölje a képszerkesztő sablont.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Get a kép verzió által létrehozott kép `0.`szerkesztő, ez mindig kezdődik , majd törölje a kép verzió

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


Törölje a képdefiníciót.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Törölje a gyűjteményt.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Az erőforráscsoport törlése.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>További lépések

További információ az [Azure megosztott képgalériáiról.](shared-image-galleries.md)