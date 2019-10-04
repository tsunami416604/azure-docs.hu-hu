---
title: Linux rendszerű virtuális gép létrehozása az Azure az Image Builder (előzetes verzió)
description: Linux rendszerű virtuális gép létrehozása az Azure az Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 2966a1803d0664312d71ba992a5ba65f73b27370
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667528"
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

## <a name="setup-example-variables"></a>Példa változók beállítása

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

## <a name="create-the-resource-group"></a>Hozza létre az erőforráscsoportot.
Ez a rendszerkép konfigurációs sablon összetevő és a lemezkép tárolására szolgál.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Az erőforráscsoport engedélyeinek beállítása
Engedélyt az Image Builder "közreműködői" erőforráscsoporthoz tartozik, a lemezkép létrehozásához. A rendszerkép összeállítását a megfelelő engedélyek nélkül sikertelen lesz. 

A `--assignee` értéke az Image Builder szolgáltatás regisztrációs Azonosítót. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>A sablon példa letöltése

Paraméteres lemezkép configuration mintasablon létrejött kell használni. Töltse le a minta .json fájlt, és konfigurálja azt a korábban beállított változókat.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

Ebben a példában .json igény szerint módosíthatja. Például értékének növelése `buildTimeoutInMinutes` hosszabb ideig futó buildek engedélyezésére. Szerkesztheti a fájlt a Cloud Shell használatával `vi`.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> A forrás lemezkép, meg kell mindig [adjon meg egy verziót](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), nem használhat `latest`.
>
> Hozzáadásakor, vagy módosítsa az erőforráscsoport, a lemezkép terjesztési helyének, győződjön meg arról, hogy szüksége a [vannak beállítva az erőforráscsoport](#set-permissions-on-the-resource-group).


## <a name="submit-the-image-configuration"></a>Küldje el a rendszerkép-konfiguráció
Küldje el a rendszerkép-konfiguráció a virtuális gép az Image Builder-szolgáltatáshoz

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Ha sikeresen befejeződik, azt fogja adja vissza egy sikert jelző üzenettel, és hozzon létre egy rendszerképet a jelentéskészítő konfigurációs sablon összetevőt a $imageResourceGroup. Az erőforráscsoportot a portálon megtekintheti, ha engedélyezi a "Rejtett típusok megjelenítése".

Emellett a háttérben, az Image Builder erőforráscsoportot hoz létre egy átmeneti az előfizetésében. Az Image Builder a rendszerkép összeállítását az átmeneti erőforráscsoportot használ. Az erőforráscsoport nevét a következő formátumban lesz: `IT_<DestinationResourceGroup>_<TemplateName>`.

> [!IMPORTANT]
> Az átmeneti erőforráscsoport ne törölje közvetlenül. Ha törli a lemezképet sablon összetevő, automatikusan törli az előkészítési erőforráscsoportot. További információkért lásd: a [tisztítása](#clean-up) szakaszban Ez a cikk végén található.

Ha a szolgáltatás a kép konfigurációs sablon elküldése közben. hibát jelez, tekintse meg a [hibaelhárítási](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) lépéseket. A sablon törlése, mielőtt ismét megpróbálkozik a build elküldése is kell. A sablon törlése:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Indítsa el a rendszerkép összeállítását

Indítsa el a rendszerkép összeállítását.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Várjon, amíg a build elkészült, ebben a példában, 10 – 15 percet is igénybe vehet.

Ha hibát észlel, tekintse át ezeket [hibaelhárítási](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) lépéseket.


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

Amikor elkészült, törölheti az erőforrásokat.

A kép builder sablon törlése.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

A kép az erőforráscsoport törlése.

```bash
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>További lépések

Az ebben a cikkben használt .JSON kiterjesztésű fájl összetevőivel kapcsolatos további tudnivalókért lásd: [Image Builder sablonreferenciája](image-builder-json.md).
