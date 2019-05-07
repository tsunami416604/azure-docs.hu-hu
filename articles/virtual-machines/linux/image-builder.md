---
title: Linux rendszerű virtuális gép létrehozása az Azure az Image Builder (előzetes verzió)
description: Linux rendszerű virtuális gép létrehozása az Azure az Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: 854645af95d780053d94668921e41ac189bbbfb7
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159510"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Előzetes verzió: Linux rendszerű virtuális gép létrehozása az Azure az Image Builder

Ez a cikk bemutatja, hogyan hozhat létre egy testre szabott Linux-rendszerképek az Azure az Image Builder és az Azure CLI használatával. Az ebben a cikkben szereplő példa három különböző [testreszabók](image-builder-json.md#properties-customize) testreszabásához a lemezkép:

- Letöltések és a futtatások rendszerhéj (ScriptUri) – egy [héjszkript](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Rendszerhéj (beágyazott) - parancsok futtatása. Ebben a példában a soron belüli parancsokat tartalmazza, létrehoz egy könyvtárat, és az operációs rendszer frissítése.
- Fájl - példányt egy [fájlt a Githubról](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) , a virtuális gépen egy olyan könyvtárba.

Fogjuk használni .json mintasablon a kép konfigurálása. Itt van a .json fájlt használjuk: [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

Hozzon létre egy változót az előfizetés-azonosítóhoz. A használatával lekérheti `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Hozza létre az erőforráscsoportot.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```


Az Image Builder engedélyt az adott erőforráscsoportba tartozó erőforrások létrehozásához. A `--assignee` értéke az Image Builder szolgáltatás regisztrációs Azonosítót. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-json-example"></a>Töltse le a .json példa

Töltse le a példában .JSON kiterjesztésű fájlt, és konfigurálja azt a létrehozott változókat.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

## <a name="create-the-image"></a>A rendszerkép létrehozása
Küldje el a rendszerkép-konfiguráció a virtuális gép az Image Builder-szolgáltatáshoz

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Indítsa el a rendszerkép összeállítását.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Várjon, amíg a build elkészült. Ez nagyjából 15 percet is igénybe vehet.


## <a name="create-the-vm"></a>Virtuális gép létrehozása

Hozzon létre a virtuális gép az Ön által készített lemezképből.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

A virtuális gép létrehozása kimenetéből származó IP-címének lekéréséhez, és ezzel a virtuális gép ssh-KAPCSOLATOT.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Megtekintheti a rendszerkép lett szabhatók testre a nap, amint az SSH-kapcsolat létrejött egy üzenetet!

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Típus `exit` Ha elkészült, zárja be az SSH-kapcsolat.

## <a name="check-the-source"></a>Ellenőrizze a forrást

A kép Builder sablon tulajdonságai között a forrás lemezkép láthat, testreszabási parancsfájl akkor fut le, és a terjesztési helyének.

```azurecli-interactive
cat helloImageTemplateLinux.json
```

További részletes információ a ezt a .json fájlt, tekintse meg a [Image builder tárfióksablonok referenciáját](image-builder-json.md)

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha elkészült, törölheti az erőforrást.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01

az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>További lépések

Az ebben a cikkben használt .JSON kiterjesztésű fájl összetevőivel kapcsolatos további tudnivalókért lásd: [Image Builder sablonreferenciája](image-builder-json.md).