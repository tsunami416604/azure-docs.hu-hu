---
title: OpenShift az Azure-előfeltételeknek |} A Microsoft Docs
description: Előfeltétel telepíthető az OpenShift az Azure-ban.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: 25ec82c923ebe322194d868159332ef145727999
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692993"
---
# <a name="common-prerequisites-for-deploying-openshift-in-azure"></a>OpenShift az Azure-beli üzembe helyezésének általános Előfeltételek

Ez a cikk ismerteti az OpenShift Tárolóplatform, vagy az Azure-ban OKD telepítésének általános előfeltételeit.

OpenShift telepítésének Ansible-forgatókönyvek használ. Az Ansible használja a Secure Shell (SSH) való csatlakozáshoz a telepítési lépések végrehajtásához a fürt összes gazdagépére.

Amikor ansible a távoli állomások az SSH-kapcsolatot kezdeményez, nem adhat meg egy jelszót. Ebből kifolyólag a titkos kulcs nem rendelkezik társított jelszóval (jelszó) vagy a központi telepítés sikertelen lesz.

A virtuális gépek (VM) üzembe helyezés Azure Resource Manager-sablonok, mert ugyanazzal a kulccsal szolgál az összes virtuális gép eléréséhez. A virtuális gép, amely végrehajtja az összes forgatókönyvek, valamint a megfelelő titkos kulcs behelyezése kell. Biztonságosan ehhez használatával egy Azure key vault adja át a titkos kulcs a virtuális géppel.

Ha hosszú távú adattárolásra tárolók szükség van, állandó kötetek szükség. OpenShift az Azure virtuális merevlemezeket (VHD) támogatja ezt a funkciót, de az Azure először konfigurálni kell a felhőszolgáltatóként.

Ebben a modellben az OpenShift:

- Az Azure Storage-fiókot vagy egy felügyelt lemezt egy VHD-objektumot hoz létre.
- Csatlakoztatja a virtuális merevlemez virtuális géphez, és formázza a kötetet.
- Csatlakoztatja a kötetet a pod.

A konfiguráció működéséhez, az OpenShift engedélyre ezeket a feladatokat az Azure-ban. Ennek elérése egy egyszerű szolgáltatást. Az egyszerű szolgáltatás nem a biztonsági fiók, az Azure Active Directoryban, amely engedéllyel rendelkezik az erőforrásokhoz.

Egyszerű szolgáltatás hozzáféréssel kell rendelkeznie a storage-fiókok és a fürtöt alkotó virtuális gépekhez. OpenShift-fürt összes erőforrás egyetlen telepíteni, ha az egyszerű szolgáltatás engedélyeket kaphatnak az adott erőforráscsoporton.

Ez az útmutató ismerteti a társított az Előfeltételek összetevőket hozhat létre.

> [!div class="checklist"]
> * Hozzon létre egy kulcstartót az OpenShift-fürthöz az SSH-kulcsok kezeléséhez.
> * Hozzon létre egy egyszerű szolgáltatást az Azure Felhőszolgáltatók általi használatra.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 
Jelentkezzen be az Azure-előfizetésbe a [az bejelentkezési](/cli/azure/reference-index) paranccsal, és kövesse a képernyőn megjelenő utasításokat, vagy kattintson **kipróbálás** Cloud Shell használatához.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Javasoljuk, hogy egy dedikált erőforráscsoport használata a key vault üzemeltetéséhez. Ez a csoport elkülönül az erőforráscsoportot, amelybe az OpenShift fürt erőforrások üzembe helyezése.

A következő példában létrehozunk egy erőforráscsoportot, nevű *keyvaultrg* a a *eastus* helye:

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása
Hozzon létre egy kulcstartót a fürthöz az SSH-kulcsok tárolására a [az keyvault létrehozása](/cli/azure/keyvault) parancsot. A kulcstároló nevének globálisan egyedinek kell lennie.

Az alábbi példa létrehoz egy kulcstartót nevű *keyvault* a a *keyvaultrg* erőforráscsoportot:

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>SSH-kulcs létrehozása 
Biztonságos hozzáférés az OpenShift fürthöz SSH-kulcs szükséges. Hozzon létre ssh-kulcs használatával a `ssh-keygen` (a Linux vagy MacOS rendszeren) parancsot:
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Az SSH-kulcspár nem lehet jelszót vagy hozzáférési kódot.

Az SSH-kulcsokat, a Windows további információkért lásd: [hogyan hozhat létre SSH-kulcsok a Windows](/azure/virtual-machines/linux/ssh-from-windows). Ügyeljen arra, hogy a titkos kulcs exportálását OpenSSH formátumban.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>A titkos SSH-kulcs Store az Azure Key Vaultban
Az OpenShift üzemelő példány biztonságos hozzáférés az OpenShift fő létrehozott SSH-kulcsot használ. Ahhoz, hogy az SSH-kulcsot biztonságosan lekérhessék a központi telepítés, tárolja a kulcsot a Key Vault használatával a következő parancsot:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása 
OpenShift egy felhasználónév és jelszó vagy egy egyszerű szolgáltatás használatával kommunikál az Azure-ral. Azure-beli szolgáltatásnév egy biztonsági identitás, amelyekkel az alkalmazások, szolgáltatások és automatizálási eszközökkel, mint például az OpenShift. Szabályozhatja, és adja meg az engedélyeket, hogy mely műveletek az egyszerű szolgáltatás hajthat végre az Azure-ban. Célszerű a teljes előfizetés helyett a szolgáltatásnév adott erőforráscsoportokhoz az engedélyek hatóköre.

Az egyszerű szolgáltatás létrehozása [az ad sp create-for-rbac](/cli/azure/ad/sp) és kimeneti az OpenShift szükséges hitelesítő adatokat.

Az alábbi példa létrehoz egy egyszerű szolgáltatást, és hozzárendeli azt a közreműködői engedélyekkel openshiftrg nevű erőforráscsoport.

Először hozza létre a openshiftrg nevű erőforráscsoportot:

```azurecli
az group create -l eastus -n openshiftrg
```

Egyszerű szolgáltatás létrehozása:

```azurecli
scope=`az group show --name openshiftrg --query id`
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --password {Strong Password} \
      --scopes $scope
```
Ha Windows használja, hajtsa végre ```az group show --name openshiftrg --query id``` , és a kimeneti $scope helyett.

Jegyezze fel a parancs által visszaadott az appId-tulajdonság:
```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > Ügyeljen arra, hogy hozzon létre egy biztonságos jelszót. Kövesse az alábbi cikkben ismertetett útmutatást: [Az Azure AD-jelszavakra vonatkozó szabályok és korlátozások](/azure/active-directory/active-directory-passwords-policy).

A szolgáltatásnevek további információkért lásd: [egy Azure-beli szolgáltatásnév létrehozása az Azure CLI-vel](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="next-steps"></a>További lépések

Ez a cikk a következő témaköröket tartalmazza:
> [!div class="checklist"]
> * Hozzon létre egy kulcstartót az OpenShift-fürthöz az SSH-kulcsok kezeléséhez.
> * Hozzon létre egy egyszerű szolgáltatást az Azure Cloud Solution Provider általi használatra.

Ezután telepítse az OpenShift fürt:

- [Az OpenShift Container Platform üzembe helyezése](./openshift-container-platform.md)
- [OKD üzembe helyezése](./openshift-okd.md)
