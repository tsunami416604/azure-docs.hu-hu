---
title: Linux rendszerű virtuális gép létrehozása az Azure rendszerkép-készítővel (előzetes verzió)
description: Hozzon létre egy linuxos virtuális gépet az Azure rendszerkép-szerkesztővel.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 1bac04bbb67c7472de92c6da322121bafc20a560
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68695436"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Előzetes verzió: Linux rendszerű virtuális gép létrehozása az Azure rendszerkép-készítővel

Ez a cikk bemutatja, hogyan hozhat létre testreszabott linuxos rendszerképeket az Azure rendszerkép-készítővel és az Azure CLI-vel. A jelen cikkben szereplő példa három különböző testreszabó használ a rendszerkép testreszabásához: [](image-builder-json.md#properties-customize)

- Shell (ScriptUri) – egy [rendszerhéj-parancsfájl](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh)letöltése és futtatása.
- Rendszerhéj (beágyazott) – adott parancsok futtatása. Ebben a példában a beágyazott parancsok magukban foglalják a címtár létrehozását és az operációs rendszer frissítését.
- Fájl – egy [fájlt](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) másol a githubból egy könyvtárba a virtuális gépen.

A `buildTimeoutInMinutes`is megadható. Az alapértelmezett érték 240 perc, és növelheti a felépítési időt, így a már futó buildek is elérhetővé válik.

A rendszerkép konfigurálásához egy minta. JSON sablont fogunk használni. Az általunk használt. JSON fájl a következő: [helloImageTemplateLinux. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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
```

Ha nem mondják a regisztrációt, futtassa a következőt:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="setup-example-variables"></a>Telepítési példák változói

Többször is fogjuk használni az adatokat, így az adatok tárolására néhány változót fogunk létrehozni.


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

Hozzon létre egy változót az előfizetés-AZONOSÍTÓhoz. Ezt a következővel `az account show | grep id`érheti el:.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>Hozza létre az erőforráscsoportot.
Ez a rendszerkép-konfigurációs sablon és a rendszerkép tárolására szolgál.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Az erőforráscsoport engedélyeinek beállítása
Adja meg a rendszerkép-készítő "közreműködő" engedélyt a rendszerkép létrehozásához az erőforráscsoporthoz. A megfelelő engedélyek nélkül a rendszerkép létrehozása sikertelen lesz. 

Az `--assignee` érték a rendszerkép-szerkesztő szolgáltatáshoz tartozó alkalmazás-regisztrációs azonosító. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Példa a sablon letöltésére

A rendszer létrehozta a paraméteres minta rendszerképének konfigurációs sablonját. Töltse le a sample. JSON fájlt, és konfigurálja a korábban beállított változókkal.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

Ezt a példát. JSON fájlt szükség szerint módosíthatja. Megnövelheti például az értékét `buildTimeoutInMinutes` a már futó buildek engedélyezéséhez. A fájlt Cloud Shell szövegszerkesztőben szerkesztheti, például `vi`a következő paranccsal:.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> A forrás rendszerkép esetében mindig meg kell [adnia egy verziót](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), amelyet nem használhat `latest`.
>
> Ha hozzáadja vagy megváltoztatja azt az erőforráscsoportot, amelyben a rendszerkép terjesztése történik, győződjön meg arról, hogy az [erőforráscsoport számára van beállítva az engedélyek](#set-permissions-on-the-resource-group).


## <a name="submit-the-image-configuration"></a>A rendszerkép konfigurációjának elküldése
A rendszerkép konfigurációjának elküldése a VM rendszerkép-készítő szolgáltatásnak

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Ha sikeresen befejeződik, a rendszer sikert jelző üzenetet küld, és létrehoz egy rendszerkép-készítő konfigurációs sablont a $imageResourceGroup. A "rejtett típusok megjelenítése" lehetőség engedélyezésével megtekintheti az erőforráscsoportot a portálon.

Továbbá a háttérben a rendszerkép-készítő létrehoz egy átmeneti erőforráscsoportot az előfizetésében. A rendszerkép-készítő a rendszerkép létrehozásához az átmeneti erőforráscsoportot használja. Az erőforráscsoport neve a következő formátumban jelenik meg: `IT_<DestinationResourceGroup>_<TemplateName>`.

> [!IMPORTANT]
> Ne törölje közvetlenül az előkészítési erőforráscsoportot. Ha törli a rendszerkép-sablon összetevőt, akkor az automatikusan törli az átmeneti erőforráscsoportot. További információkért lásd a cikk végén található [tisztítás](#clean-up) szakaszt.

Ha a szolgáltatás hibát jelez a rendszerkép-konfigurációs sablon beküldése során, tekintse meg a [hibaelhárítási](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) lépéseket. A Build elküldése előtt törölnie kell a sablont is. A sablon törlése:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>A rendszerkép létrehozásának elindítása

Indítsa el a rendszerkép buildjét.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Várjon, amíg a Build befejeződik, ebben a példában ez 10-15 percet is igénybe vehet.

Ha bármilyen hibát tapasztal, tekintse át ezeket [](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) a hibaelhárítási lépéseket.


## <a name="create-the-vm"></a>Virtuális gép létrehozása

Hozza létre a virtuális gépet a létrehozott rendszerkép használatával.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Szerezze be az IP-címet a virtuális gép létrehozásának kimenetében, és használja SSH-ra a virtuális géphez.

```azurecli-interactive
ssh azureuser@<pubIp>
```

A rendszerképet úgy kell megtekinteni, hogy az SSH-kapcsolatok létrehozása után a nap egy üzenete legyen.

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Az `exit` SSH-kapcsolatok bezárásához írja be a következőt:

## <a name="check-the-source"></a>A forrás keresése

A rendszerkép-készítő sablon tulajdonságok területén látni fogja a forrás-és a testreszabási parancsfájlt, valamint a terjesztés helyét.

```azurecli-interactive
cat helloImageTemplateLinux.json
```

További információ erről a. JSON fájlról: a [rendszerkép-szerkesztő sablonjának referenciája](image-builder-json.md)

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha elkészült, törölheti az erőforrásokat.

Törölje a rendszerkép-szerkesztő sablonját.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Törölje a rendszerkép-erőforráscsoportot.

```bash
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a cikkben használt. JSON fájl összetevőiről, tekintse meg a [rendszerkép-szerkesztői sablon referenciáját](image-builder-json.md).
