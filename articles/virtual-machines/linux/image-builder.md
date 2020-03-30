---
title: Linuxos virtuális gép létrehozása az Azure Image Builder segítségével (előzetes verzió)
description: Hozzon létre egy Linux virtuális gép az Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: 0d36d7db4d85ece8de77040925c535305951562b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066680"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Előzetes verzió: Linuxos virtuális gép létrehozása az Azure Image Builder segítségével

Ez a cikk bemutatja, hogyan hozhat létre egy testreszabott Linux-lemezképet az Azure Image Builder és az Azure CLI használatával. A cikkben szereplő példa három különböző [testreszabót](image-builder-json.md#properties-customize) használ a kép testreszabásához:

- Shell (ScriptUri) - letölti és futtatja a [shell script](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Shell (inline) - meghatározott parancsokat futtat. Ebben a példában a szövegközi parancsok közé tartozik egy könyvtár létrehozása és az operációs rendszer frissítése.
- Fájl – egy fájlt másol [a GitHubról](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) a virtuális gép egy könyvtárába.

Megadhat egy `buildTimeoutInMinutes`. Az alapértelmezett érték 240 perc, és növelheti a buildelési időt, hogy lehetővé tegye a hosszabb ideig futó buildek.

A lemezkép konfigurálásához egy .json mintasablont fogunk használni. Az általunk használt .json fájl itt van: [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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

## <a name="setup-example-variables"></a>Példaváltozók beállítása

Mi lesz használ néhány információt többször, így hozunk létre néhány változótárolják ezt az információt.


```console
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

Hozzon létre egy változót az előfizetés-azonosítóhoz. Tudod kap ez `az account show | grep id`használ .

```console
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>Hozza létre az erőforráscsoportot.
Ez a lemezképkonfigurációs sablon műtermékének és a lemezképnek a tárolására szolgál.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Engedélyek beállítása az erőforráscsoporthoz
Adjon a Képszerkesztő "közreműködő" számára a lemezkép létrehozásához az erőforráscsoportban. A megfelelő engedélyek nélkül a lemezkép összeállítása sikertelen lesz. 

Az `--assignee` érték a Képszerkesztő szolgáltatás alkalmazásregisztrációs azonosítója. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>A példasablon letöltése

Egy paraméterezett mintaképkonfigurációs sablont hoztak létre a használandó célokra. Töltse le a minta .json fájlt, és konfigurálja a korábban beállított változókkal.

```bash
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

Ezt a példát .json példában szükség szerint módosíthatja. Például növelheti az értékét, `buildTimeoutInMinutes` hogy lehetővé tegye a hosszabb ideig futó buildek. A cloud shellben a fájl szerkesztővel `vi`szerkeszthető, például a .

```bash
vi helloImageTemplateLinux.json
```

> [!NOTE]
> A forrásképhez mindig meg kell adni `latest`a [verziót,](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure)amely et nem használhatja.
>
> Ha hozzáadja vagy módosítja azt az erőforráscsoportot, amelyben a lemezkép terjesztése folyamatban van, meg kell győződnie arról, hogy az [engedélyek be vannak állítva az erőforráscsoporthoz.](#set-permissions-on-the-resource-group)


## <a name="submit-the-image-configuration"></a>A képkonfiguráció elküldése
Küldje el a lemezkép konfigurációját a VM Image Builder szolgáltatás

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Ha sikeresen befejeződik, sikeres üzenetet ad vissza, és létrehoz egy képkészítő konfigurációs sablonösszetevőt a $imageResourceGroup. Az erőforráscsoport a portálon látható, ha engedélyezi a "Rejtett típusok megjelenítése" engedélyezését.

Emellett a háttérben az Image Builder létrehoz egy átmeneti erőforráscsoportot az előfizetésben. A Képszerkesztő az átmeneti erőforráscsoportot használja a lemezkép létrehozásához. Az erőforráscsoport neve a következő formátumban lesz: `IT_<DestinationResourceGroup>_<TemplateName>`.

> [!IMPORTANT]
> Ne törölje közvetlenül az átmeneti erőforráscsoportot. Ha törli a képsablon-összetevőt, az automatikusan törli az átmeneti erőforráscsoportot. További információt a cikk végén található [Karbantartás](#clean-up) című szakaszban talál.

Ha a szolgáltatás hibát jelent a lemezképkonfigurációs sablon beküldése során, olvassa el a [hibaelhárítási](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) lépéseket. A build újraküldése előtt törölnie kell a sablont is. A sablon törlése:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>A kép összeállításának megkezdése

Indítsa el a kép felépítését.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Várjon, amíg a build befejeződik, ebben a példában 10-15 percet is igénybe vehet.

Ha bármilyen hibát észlel, olvassa el ezeket [a hibaelhárítási](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) lépéseket.


## <a name="create-the-vm"></a>Virtuális gép létrehozása

Hozza létre a virtuális gép a létrehozott lemezkép használatával.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Az IP-cím a virtuális gép létrehozásának kimenetéből, és használja azt az SSH a virtuális gép.

```bash
ssh azureuser@<pubIp>
```

Látnia kell, hogy a kép a nap üzenetével lett testreszabva, amint az SSH kapcsolat létrejött!

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Írja `exit` be, ha végzett az SSH-kapcsolat bezárásával.

## <a name="check-the-source"></a>A forrás ellenőrzése

A Képszerkesztő sablonban a "Tulajdonságok" területen megjelenik a forráskép, a rajta futtatott testreszabási parancsfájl és az elosztás helye.

```bash
cat helloImageTemplateLinux.json
```

A .json fájlról további információt a [Képszerkesztő sablon hivatkozási útmutatója című témakörben talál.](image-builder-json.md)

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha végzett, törölheti az erőforrásokat.

Törölje a képszerkesztő sablont.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Törölje a képerőforrás-csoportot.

```azurecli
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>További lépések

A cikkben használt .json fájl összetevőiről a [Képszerkesztő sablonhivatkozása című témakörben olvashat bővebben.](image-builder-json.md)
