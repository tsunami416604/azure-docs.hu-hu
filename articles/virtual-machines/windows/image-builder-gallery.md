---
title: Az Azure rendszerkép-készítő használata Windows rendszerű virtuális gépekhez készült rendszerkép-katalógussal (előzetes verzió)
description: Windowsos virtuálisgép-rendszerképek létrehozása az Azure Image Builder és a Shared Image Gallery szolgáltatással.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: 1d9763ccc5f5967b9fc9932a11fff655e6120fd0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976077"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Előzetes verzió: Windows-rendszerkép létrehozása és terjesztése megosztott képgyűjteménybe 

Ebből a cikkből megtudhatja, hogyan hozhat létre egy rendszerkép-verziót az Azure rendszerkép-készítő használatával egy [megosztott rendszerkép](shared-image-galleries.md)-katalógusban, majd hogyan terjesztheti a rendszerképet globálisan.

A rendszerkép konfigurálásához egy. JSON sablont fogunk használni. Az általunk használt. JSON fájl a következő: [helloImageTemplateforWinSIG. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json). 

A rendszerkép megosztott képgyűjteménybe való terjesztéséhez a sablon a [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) használja a sablon `distribute` szakaszának értékeként.

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
az provider show -n Microsoft.Compute | grep registrationState
```

Ha nem mondják a regisztrációt, futtassa a következőt:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```

## <a name="set-variables-and-permissions"></a>Változók és engedélyek beállítása 

Többször is fogjuk használni az adatokat, így az adatok tárolására néhány változót fogunk létrehozni. Cserélje le a változók értékeit, például a `username` és a `vmpassword`t a saját adataival.

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

Hozzon létre egy változót az előfizetés-AZONOSÍTÓhoz. Ezt a `az account show | grep id`használatával érheti el.

```azurecli-interactive
subscriptionID="Subscription ID"
```

Hozza létre az erőforráscsoportot.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Adja meg az Azure rendszerkép-szerkesztőnek, hogy erőforrásokat hozzon létre az adott erőforráscsoporthoz. A `--assignee` érték a rendszerkép-szerkesztő szolgáltatáshoz tartozó alkalmazás-regisztrációs azonosító. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="create-an-image-definition-and-gallery"></a>Rendszerkép-definíció és-gyűjtemény létrehozása

Ha közös rendszerkép-katalógussal szeretné használni a képszerkesztőt, rendelkeznie kell egy meglévő képtárat és képdefiníciót tartalmazó képpel. A rendszerkép-szerkesztő nem hozza létre az Ön számára a rendszerkép-gyűjteményt és a rendszerkép definícióját.

Ha még nem rendelkezik a használni kívánt gyűjtemény-és képdefinícióval, először hozza létre őket. Először hozzon létre egy képtárat.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Ezután hozzon létre egy rendszerkép-definíciót.

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


## <a name="download-and-configure-the-json"></a>A. JSON letöltése és konfigurálása

Töltse le a. JSON sablont, és konfigurálja a változókkal.

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

## <a name="create-the-image-version"></a>A rendszerkép verziójának létrehozása

A következő rész a rendszerkép verzióját fogja létrehozni a katalógusban. 

Küldje be a rendszerkép konfigurációját az Azure rendszerkép-szerkesztő szolgáltatásba.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Indítsa el a rendszerkép buildjét.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforWinSIG01 \
     --action Run 
```

A rendszerkép létrehozása és replikálása mindkét régióban eltarthat egy ideig. Várjon, amíg ez a rész be nem fejeződik a virtuális gép létrehozásához való továbblépés előtt.


## <a name="create-the-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az Azure-rendszerkép-szerkesztő által létrehozott rendszerkép-verzióból.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm001 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```


## <a name="verify-the-customization"></a>A Testreszabás ellenőrzése
Hozzon létre egy Távoli asztal-csatlakozást a virtuális géphez a virtuális gép létrehozásakor beállított Felhasználónév és jelszó használatával. A virtuális gépen nyisson meg egy parancssort, és írja be a következőt:

```console
dir c:\
```

A rendszerkép testreszabása során létrehozott `buildActions` nevű könyvtárat kell megjelennie.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha most újra testre szeretné szabni a rendszerkép verzióját, hogy ugyanazon rendszerkép új verzióját hozza létre, **hagyja ki ezt a lépést** , és folytassa az [Azure rendszerkép-készítő használatával egy másik rendszerkép-verzió létrehozásához](image-builder-gallery-update-image-version.md).


Ezzel törli a létrehozott rendszerképet, valamint az összes többi erőforrás-fájlt is. Az erőforrások törlése előtt ellenőrizze, hogy befejeződött-e az üzemelő példány.

A rendszerkép-katalógus erőforrásainak törlésekor törölnie kell az összes rendszerkép-verziót, mielőtt törölné a létrehozásához használt képdefiníciót. A katalógus törléséhez először törölnie kell a gyűjtemény összes rendszerkép-definícióját.

Törölje a rendszerkép-szerkesztő sablonját.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Szerezze be a rendszerkép-szerkesztő által létrehozott rendszerkép verzióját, amely mindig a `0.`, majd a rendszerkép verziójának törlésével kezdődik.

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


Törölje a rendszerkép definícióját.

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

Törölje az erőforráscsoportot.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan frissítheti a létrehozott rendszerkép-verziót, tekintse meg az [Azure rendszerkép-készítő használata egy másik rendszerkép-verzió létrehozásához](image-builder-gallery-update-image-version.md)című témakört.