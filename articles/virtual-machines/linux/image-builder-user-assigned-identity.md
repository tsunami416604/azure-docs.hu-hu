---
title: Virtuálisgép-lemezkép létrehozása és felhasználó által hozzárendelt felügyelt identitás használata az Azure Storage-fájlok eléréséhez (előzetes verzió)
description: Hozzon létre virtuálisgép-lemezképet az Azure Image Builder használatával, amely a felhasználó által hozzárendelt felügyelt identitás használatával hozzáférhet az Azure Storage-ban tárolt fájlokhoz.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: 27f4073efc8647d331faa14afbda0e15f92b8d50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060753"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Lemezkép létrehozása és felhasználó által hozzárendelt felügyelt identitás használata az Azure Storage-fájlok eléréséhez 

Az Azure Image Builder támogatja a parancsfájlok használatát, vagy fájlok másolása több helyről, például a GitHub és az Azure storage stb. Ezek használatához külsőleg elérhetőnek kell lennie az Azure Image Builder számára, de megvédheti az Azure Storage-blobokat a SAS-jogkivonatok használatával.

Ez a cikk bemutatja, hogyan hozhat létre egy testreszabott lemezképet az Azure VM Image Builder használatával, ahol a szolgáltatás egy [felhasználó által hozzárendelt felügyelt identitást](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) fog használni a fájlok eléréséhez az Azure storage-ban a lemezkép testreszabásához, anélkül, hogy a fájlokat nyilvánosan hozzáférhetővé kellene tenni, vagy SAS-jogkivonatok beállítása.

Az alábbi példában két erőforráscsoportot hoz létre, az egyik et az egyéni lemezképhez használja, a másik pedig egy Azure Storage-fiókot, amely egy parancsfájlt tartalmaz. Ez szimulálja a valós forgatókönyv, ahol lehet, hogy a build összetevők, vagy a képfájlok at különböző tárfiókok, az Image Builder kívül. Létre fog hozni egy felhasználó által hozzárendelt identitást, majd megadja az olvasási engedélyeket a parancsfájlhoz, de nem állít be nyilvános hozzáférést a fájlhoz. Ezután a Shell-testreszabó segítségével letöltheti és futtathatja a parancsfájlt a tárfiókból.


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


## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Mi lesz használ néhány információt többször, így hozunk létre néhány változótárolják ezt az információt.


```console
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

Hozzon létre egy változót az előfizetés-azonosítóhoz. Tudod kap ez `az account show | grep id`használ .

```console
subscriptionID=<Your subscription ID>
```

Hozza létre az erőforráscsoportokat a lemezképhez és a parancsfájltárolóhoz is.

```console
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```


Hozza létre a tárolót, és másolja a mintaparancsfájlt a GitHubról.

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```



Adjon engedélyt a Képszerkesztőnek erőforrások létrehozására a képerőforrás-csoportban. Az `--assignee` érték a Képszerkesztő szolgáltatás alkalmazásregisztrációs azonosítója. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Hozza létre az identitást, és rendeljen hozzá engedélyeket a parancsfájltár-fiókhoz. További információ: [User-Assigned Managed Identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

```azurecli-interactive
# Create the user assigned identity 
identityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $identityName
# assign the identity permissions to the storage account, so it can read the script blob
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
# create the user identity URI
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName
```


## <a name="modify-the-example"></a>A példa módosítása

Töltse le a példát .json fájlt, és konfigurálja a létrehozott változókkal.

```console
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>A rendszerkép létrehozása

Küldje el a lemezkép konfigurációját az Azure Image Builder szolgáltatásba.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Indítsa el a kép felépítését.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Várja meg, amíg befejeződik a build. Ez körülbelül 15 percet is igénybe vehet.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gép a lemezképből. 

```azurecli
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

A virtuális gép létrehozása után indítsa el az SSH-munkamenetet a virtuális géppel.

```console
ssh aibuser@<publicIp>
```

Látnia kell, hogy a kép a nap üzenetével lett testreszabva, amint az SSH kapcsolat létrejött!

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha végzett, törölheti az erőforrásokat, ha már nincs rájuk szükség.

```azurecli-interactive
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>További lépések

Ha problémái vannak az Azure Image Builder használatával, olvassa el a [Hibaelhárítás című témakört.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json)