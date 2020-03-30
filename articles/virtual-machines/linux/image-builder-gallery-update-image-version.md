---
title: Új virtuálisgép-lemezkép-verzió létrehozása meglévő lemezkép-verzióból az Azure Image Builder használatával (előzetes verzió)
description: Hozzon létre egy új virtuálisgép-lemezkép-verziót egy meglévő lemezkép-verzióból az Azure Image Builder használatával.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: 5766e91dc6a17d50c46d396dd8a68d17081e0926
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246806"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Előzetes verzió: Új virtuálisgép-lemezkép-verzió létrehozása meglévő lemezkép-verzióból az Azure Image Builder használatával

Ez a cikk bemutatja, hogyan vehet idát egy [megosztott képtárban,](shared-image-galleries.md)hogyan frissítheti azt, és hogyan teheti közzé új képverzióként a gyűjteményben.

A lemezkép konfigurálásához egy .json mintasablont fogunk használni. Az általunk használt .json fájl itt van: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


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

Ha [a Kép létrehozása és a megosztott képtárba való terjesztés](image-builder-gallery.md) t, a megosztott képtár létrehozásához használt, akkor már létrehozott néhány szükséges változót. Ha nem, kérjük, állítson be néhány változót, amelyeket ebben a példában használni fog.

Az előnézeti verzió esetén a lemezképkészítő csak a forrás felügyelt lemezképpel azonos erőforráscsoportban támogatja az egyéni lemezképek létrehozását. Frissítse az erőforráscsoport nevét ebben a példában, hogy ugyanaz az erőforráscsoport legyen, mint a forrás felügyelt lemezképe.


```console
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

Hozzon létre egy változót az előfizetés-azonosítóhoz. Tudod kap ez `az account show | grep id`használ .

```console
subscriptionID=<Subscription ID>
```

A frissíteni kívánt lemezkép-verzió beszerezése.

```azurecli
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```


Ha már rendelkezik saját megosztott képtár, és nem követte az előző példát, akkor meg kell rendelnie engedélyeket Képszerkesztő eléréséhez az erőforráscsoport, így elérheti a katalógust.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>Módosítás helloImage példa
A .json fájl megnyitásával áttekintheti a használni kívánt példát: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) az [Image Builder sablon hivatkozásával](image-builder-json.md)együtt. 


Töltse le a .json példát, és konfigurálja a változókkal. 

```console
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

Küldje el a lemezkép konfigurációját a Virtuálisgép-lemezképszerkesztő szolgáltatásnak.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

Indítsa el a kép felépítését.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

Várja meg, amíg a rendszerkép megépül, és a replikáció, mielőtt a következő lépésre.


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

Hozzon létre egy SSH-kapcsolatot a virtuális gép a virtuális gép nyilvános IP-cím használatával.

```console
ssh azureuser@<pubIp>
```

Látnia kell, hogy a kép a "Nap üzenete" segítségével lett testreszabva, amint az SSH-kapcsolat létrejött.

```output
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Írja `exit` be az SSH-kapcsolat bezárásához.

A galériában már elérhető képverziókat is felsorolhatja.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>További lépések

A cikkben használt .json fájl összetevőiről a [Képszerkesztő sablon hivatkozása című témakörben olvashat bővebben.](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)