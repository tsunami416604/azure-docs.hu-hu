---
title: Hozzon létre egy virtuálisgép-rendszerképet és a egy felhasználó által hozzárendelt felügyelt identitás használata fájlok eléréséhez az Azure Storage (előzetes verzió)
description: Hozzon létre virtuálisgép-lemezkép használatával felügyelt identitás felhasználó által kijelölt Azure Storage-ban tárolt fájlokhoz hozzáférő Azure Image Builder használatával.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: b0a6c016b2be12ac6686b3748b4b16281899323e
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65511058"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Hozzon létre egy rendszerképet, és a egy felhasználó által hozzárendelt felügyelt identitás használata fájlok eléréséhez az Azure Storage-ban 

Az Azure az Image Builder támogatja szkriptek használatával, vagy a fájlok másolása több helyről, például a GitHub és az Azure storage stb. Ezek használatához kellett kívülről elérhető-e az Azure az Image Builder, de a SAS-tokeneket használó Azure Storage-blobokat sikerült védelméről.

Ez a cikk bemutatja, hogyan hozhat létre egy testre szabott lemezképet az Azure virtuális gép az Image Builder, ahol a szolgáltatás által használt használatával egy [felügyelt identitás felhasználó által hozzárendelt](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) fájlok eléréséhez az Azure storage-ban a lemezkép testreszabáshoz nélkül, győződjön meg arról, hogy kellene a fájlok nyilvánosan hozzáférhető, vagy SAS-tokeneket beállítását.

Az alábbi példában létrehozhat két erőforráscsoport, egy fogja használni az egyéni rendszerkép és Azure Storage-fiókhoz, a parancsfájl tartalmazza a másik fogja futtatni. Ez szimulálja egy valós forgatókönyv, ahol előfordulhat, hogy build-összetevőket, vagy képfájlokat, különböző tárfiókokban az Image Builder-en kívül. Egy felhasználó által hozzárendelt identitás, majd támogatást, amely olvasási engedéllyel a parancsfájl létrehozza, de bármely nyilvános hozzáférést, amely a fájl nem állítja. A rendszerhéj rendszertestreszabó használandó Ezután töltse le és futtassa a parancsfájlt a tárfiókból.


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

Hozzon létre egy változót az előfizetés-azonosítóhoz. A használatával lekérheti `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Az erőforráscsoportok, a lemezkép és a parancsfájl-tároló létrehozása.

```azurecli-interactive
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```


Hozzon létre a tárfiókot, és a minta parancsfájl másolja bele a Githubról.

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



Az Image Builder engedélyt a kép az erőforráscsoport erőforrásokat létrehozni. A `--assignee` értéke az Image Builder szolgáltatás regisztrációs Azonosítót. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Az identitás létrehozása, és a parancsfájl tárfiókhoz tartozó engedélyeket. További információkért lásd: [User-Assigned felügyelt identitás](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

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


## <a name="modify-the-example"></a>Módosítsa úgy a példát

Töltse le a példában .JSON kiterjesztésű fájlt, és konfigurálja azt a létrehozott változókat.

```azurecli-interactive
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

Küldje el a rendszerkép-konfiguráció az Azure az Image Builder szolgáltatásba.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Indítsa el a rendszerkép összeállítását.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Várjon, amíg befejeződik a build. Ez nagyjából 15 percet is igénybe vehet.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Virtuális gép létrehozása a rendszerképből. 

```bash
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

A virtuális gép létrehozása után kezdje a virtuális gép SSH-munkamenetből.

```azurecli-interactive
ssh aibuser@<publicIp>
```

Megtekintheti a rendszerkép lett szabhatók testre a nap, amint az SSH-kapcsolat létrejött egy üzenetet!

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha elkészült, törölheti az erőforrásokat, ha már nincs szükség.

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

Ha bármilyen problémája Azure Image Builder dolgozik, lásd: [hibaelhárítás](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json).