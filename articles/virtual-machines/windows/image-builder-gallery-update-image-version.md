---
title: Új lemezkép-verzió létrehozása meglévő lemezkép-verzióból az Azure Image Builder használatával (előzetes verzió)
description: Hozzon létre egy új virtuálisgép-lemezkép-verziót egy meglévő lemezkép-verzióból az Azure Image Builder használatával.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: how-to
ms.service: virtual-machines-windows
ms.openlocfilehash: 766e7d5c4151000a582bcf07d80b89af3b7d8a65
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869540"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Előzetes verzió: Új virtuálisgép-lemezkép-verzió létrehozása meglévő lemezkép-verzióból az Azure Image Builder használatával

Ez a cikk bemutatja, hogyan vehet idát egy [megosztott képtárban,](shared-image-galleries.md)hogyan frissítheti azt, és hogyan teheti közzé új képverzióként a gyűjteményben.

A lemezkép konfigurálásához egy .json mintasablont fogunk használni. Az általunk használt .json fájl itt van: [helloImageTemplateforSIGfromWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json). 

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
az provider show -n Microsoft.Compute | grep registrationState
```

Ha nem mondják, hogy regisztrált, futtassa a következőket:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```


## <a name="set-variables-and-permissions"></a>Változók és engedélyek beállítása

Ha [a Kép létrehozása és a megosztott képtárba való terjesztés](image-builder-gallery.md) t, a megosztott képtár létrehozásához használt, akkor már létrehozta a szükséges változókat. Ha nem, kérjük, állítson be néhány változót, amelyeket ebben a példában használni fog.

Az előnézeti verzió esetén a lemezképkészítő csak a forrás felügyelt lemezképpel azonos erőforráscsoportban támogatja az egyéni lemezképek létrehozását. Frissítse az erőforráscsoport nevét ebben a példában, hogy ugyanaz az erőforráscsoport legyen, mint a forrás felügyelt lemezképe.

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

Hozzon létre egy változót az előfizetés-azonosítóhoz. Tudod kap ez `az account show | grep id`használ .

```azurecli-interactive
subscriptionID=<Subscription ID>
```

A frissíteni kívánt lemezkép-verzió beszerezése.

```azurecli-interactive
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
A .json fájl megnyitásával áttekintheti a használni kívánt példát: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) az [Image Builder sablon hivatkozásával](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)együtt. 


Töltse le a .json példát, és konfigurálja a változókkal. 

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

Küldje el a lemezkép konfigurációját a Virtuálisgép-lemezképszerkesztő szolgáltatásnak.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

Indítsa el a kép felépítését.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

Várja meg, amíg a rendszerkép megépül, és a replikáció, mielőtt a következő lépésre.


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

## <a name="verify-the-customization"></a>A testreszabás ellenőrzése
Hozzon létre egy távoli asztali kapcsolatot a virtuális géppel a virtuális gép létrehozásakor megadott felhasználónév és jelszó használatával. A virtuális gépbelsejében nyisson meg egy cmd-s kérdést, és írja be a következőt:

```console
dir c:\
```

Most két könyvtárat kell látnia:
- `buildActions`az első képverzióban jött létre.
- `buildActions2`hogy jött létre, mint része ként frissítése az első kép verziója, hogy megteremtse a második kép verzió.


## <a name="next-steps"></a>További lépések

A cikkben használt .json fájl összetevőiről a [Képszerkesztő sablon hivatkozása című témakörben olvashat bővebben.](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)