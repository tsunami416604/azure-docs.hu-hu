---
title: OpenShift Container Platform 3.11 az Azure előfeltételeiben
description: Az OpenShift Container Platform 3.11 azure-beli üzembe helyezésének előfeltételei.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2019
ms.author: haroldw
ms.openlocfilehash: b2b34a6fdf96613c5bc372e585598fabbe43d53d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066610"
---
# <a name="common-prerequisites-for-deploying-openshift-container-platform-311-in-azure"></a>Az OpenShift Container Platform 3.11 azure-beli üzembe helyezésének gyakori előfeltételei

Ez a cikk az OpenShift Container Platform vagy az OKD azure-beli üzembe helyezésének gyakori előfeltételeit ismerteti.

Az OpenShift telepítése Ansible forgatókönyveket használ. Az Ansible a Secure Shell (SSH) segítségével csatlakozik az összes fürtállomáshoz a telepítési lépések végrehajtásához.

Amikor ansible teszi az SSH-kapcsolat a távoli állomások, nem tud belépni a jelszót. Emiatt a személyes kulcshoz nem lehet jelszó (jelszó) társítva, különben a központi telepítés sikertelen lesz.

Mivel a virtuális gépek (VM-ek) az Azure Resource Manager-sablonokon keresztül üzembe helyezhetők, ugyanazt a nyilvános kulcsot használják az összes virtuális gép eléréséhez. A megfelelő titkos kulcsnak a virtuális gépen kell lennie, amely az összes forgatókönyvet is végrehajtja. A művelet biztonságos végrehajtásához egy Azure-key vault segítségével adja át a személyes kulcsot a virtuális gép.

Ha a tárolók állandó tárolása szükséges, akkor állandó kötetekre van szükség. OpenShift támogatja az Azure virtuális merevlemezek (VHDs) állandó kötetek, de az Azure-t először be kell állítani a felhőszolgáltató.

Ebben a modellben az OpenShift:

- VHD-objektumot hoz létre egy Azure-tárfiókban vagy egy felügyelt lemezben.
- A virtuális merevlemezt virtuális géphez csatlakoztatja, és formázza a kötetet.
- A kötetet a podhoz csatlakoztatja.

Ahhoz, hogy ez a konfiguráció működjön, az OpenShift-nek engedélyekre van szüksége a feladatok Azure-beli végrehajtásához. Erre a célra egy szolgáltatásnév használatos. Az egyszerű szolgáltatás egy biztonsági fiók az Azure Active Directoryban, amely engedéllyel rendelkezik az erőforrásokhoz.

A szolgáltatásnévnek hozzáféréssel kell rendelkeznie a fürtből álló tárfiókokhoz és virtuális gépekhez. Ha az összes OpenShift fürterőforrás egyetlen erőforráscsoportra telepíthető, az egyszerű szolgáltatás engedélyeket kaphat az adott erőforráscsoporthoz.

Ez az útmutató ismerteti, hogyan hozhat létre az előfeltételektársított összetevők.

> [!div class="checklist"]
> * Hozzon létre egy key vault sSH-kulcsok kezelésére az OpenShift fürthöz.
> * Hozzon létre egy egyszerű szolgáltatás az Azure Cloud-szolgáltató általi használatra.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 
Jelentkezzen be az Azure-előfizetésbe az [az bejelentkezési](/cli/azure/reference-index) paranccsal, és kövesse a képernyőn megjelenő utasításokat, vagy kattintson **a Próbálja ki a** Cloud Shell használatához gombra.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A key vault üzemeltetéséhez használjon dedikált erőforráscsoportot. Ez a csoport elkülönül attól az erőforráscsoporttól, amelybe az OpenShift fürt erőforrásai tanak.

A következő példa létrehoz egy *keyvaultrg* nevű erőforráscsoportot az *eastus* helyen:

```azurecli
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása
Hozzon létre egy key vault tárolja az SSH-kulcsokat a fürt az [az keyvault create](/cli/azure/keyvault) paranccsal. A kulcstartó nevének globálisan egyedinek kell lennie, és engedélyezni kell a sablon telepítéséhez, különben a telepítés sikertelen lesz a "KeyVaultParameterReferenceSecretRetrieveFailed" hibával.

A következő példa létrehoz egy keyvault nevű keyvault nevű *keyvault* a *keyvaultrg* erőforráscsoportban:

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>SSH-kulcs létrehozása 
Az OpenShift-fürthöz való hozzáférés biztosításához SSH-kulcs szükséges. Hozzon létre egy SSH `ssh-keygen` kulcspár segítségével a parancs (Linux vagy macOS):
 
```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Az SSH kulcspárnem rendelkezhet jelszóval/ jelszóval.

A Windows SSH-kulcsairól az [SSH-kulcsok létrehozása Windows rendszeren](/azure/virtual-machines/linux/ssh-from-windows)című témakörben talál további információt. Ügyeljen arra, hogy a személyes kulcsot OpenSSH formátumban exportálja.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Az SSH személyes kulcsának tárolása az Azure Key Vaultban
Az OpenShift központi telepítése a létrehozott SSH-kulcs segítségével biztosítja az OpenShift főkiszolgálóhoz való hozzáférést. Ha engedélyezni szeretné, hogy a központi telepítés biztonságosan lekérje az SSH-kulcsot, tárolja a kulcsot a Key Vaultban a következő paranccsal:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása 
Az OpenShift felhasználónévvel és jelszóval vagy egyszerű szolgáltatással kommunikál az Azure-ral. Az Azure egyszerű szolgáltatásegy biztonsági identitás, amely az alkalmazások, szolgáltatások és automatizálási eszközök, például openshift használható. Ön szabályozza és határozza meg az engedélyeket, hogy mely műveleteket a szolgáltatásnév végezhet az Azure-ban. A legjobb, ha a szolgáltatásnév engedélyeit a teljes előfizetés helyett adott erőforráscsoportokra is lehatja.

Hozzon létre egy egyszerű szolgáltatás az [ad sp create-for-rbac](/cli/azure/ad/sp) és a hitelesítő adatok kimenete, hogy Az OpenShift szüksége van.

A következő példa létrehoz egy egyszerű szolgáltatásszolgáltatást, és közreműködői engedélyeket rendel hozzá egy *openshiftrg*nevű erőforráscsoporthoz.

Először hozza létre az *openshiftrg*nevű erőforráscsoportot:

```azurecli
az group create -l eastus -n openshiftrg
```

Egyszerű szolgáltatás létrehozása:

```azurecli
az group show --name openshiftrg --query id
```

Mentse a parancs kimenetét, és használja a $scope helyett a következő parancsban

```azurecli
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --scopes $scope \
```

Vegye figyelembe az appId tulajdonságot és a parancsból visszaadott jelszót:

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
 > Ügyeljen arra, hogy írja le a biztonságos jelszót, mert nem lesz lehetséges, hogy letölteni ezt a jelszót újra.

A szolgáltatásegyszerű szolgáltatásokról további információt az [Azure service principal azure CLI-vel című témakörben](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)talál.

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Csak az Erőforrás-kezelő sablonra vonatkozó előfeltételek

Titkos kulcsokat kell létrehozni az SSH titkos kulcshoz **(sshPrivateKey),** az Azure AD ügyféltitkos kulcshoz (**aadClientSecret),** OpenShift rendszergazdai jelszóhoz (**openshiftPassword**) és a Red Hat Subscription Manager jelszóhoz vagy aktiválási kulcshoz (**rhsmPasswordOrActivationKey**).  Ezenkívül egyéni SSL-tanúsítványok esetén hat további titkos kulcsot kell létrehozni - **routingcafile**, **routingcertfile**, **routingkeyfile**, **mastercafile**, **mastercertfile**és **masterkeyfile**.  Ezeket a paramétereket részletesebben ismertetjük.

A sablon konkrét titkos nevekre hivatkozik, ezért a fent felsorolt félkövér neveket **kell** használnia (a kis- és nagybetűk megkülönböztetése).

### <a name="custom-certificates"></a>Egyéni tanúsítványok

Alapértelmezés szerint a sablon egy OpenShift-fürtöt telepít az OpenShift webkonzol és az útválasztási tartomány önaláírt tanúsítványai val. Ha egyéni SSL-tanúsítványokat szeretne használni, állítsa a "routingCertType" (routingCertType' (intézi) és a "masterCertType" (egyéni' beállítását 'custom' (egyéni) beállításra.  A tanúsítványokhoz a hitelesítésszolgáltató, a tanúsítvány és a kulcsfájlok .pem formátumban vannak.  Lehetőség van egyéni tanúsítványok használatára az egyikhez, de a másikhoz nem.

Ezeket a fájlokat a Key Vault titkos fájljaiban kell tárolnia.  Használja ugyanazt a Key Vaultot, mint a személyes kulcshoz használt.  Ahelyett, hogy 6 további bemenetet igényelne a titkos nevekhez, a sablon kódolva van, hogy az SSL-tanúsítványfájlok mindegyikéhez speciális titkos neveket használjon.  A tanúsítvány adatokat az alábbi táblázatban szereplő adatok alapján tárolhatja.

| Titkos név      | Tanúsítványfájl   |
|------------------|--------------------|
| mastercafile     | fő hitelesítéshitelesítés-fájl     |
| mastercertfile   | fő CERT fájl   |
| masterkeyfile    | főkulcsfájl    |
| routingcafile    | társhitelesítésfájl továbbítása    |
| routingcertfile  | műveletterv CERT-fájl  |
| routingkeyfile   | útválasztás kulcsfájl   |

Hozza létre a titkos kulcsokat az Azure CLI használatával. Az alábbiakban egy példa.

```azurecli
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>További lépések

Ez a cikk a következő témaköröket ismerteti:
> [!div class="checklist"]
> * Hozzon létre egy key vault sSH-kulcsok kezelésére az OpenShift fürthöz.
> * Hozzon létre egy egyszerű szolgáltatás az Azure Cloud Solution Provider általi használatra.

Ezután telepítsen egy OpenShift-fürtöt:

- [OpenShift tárolóplatform telepítése](./openshift-container-platform-3x.md)
- [OpenShift container platform saját felügyelt piactéri ajánlatának üzembe helyezése](./openshift-container-platform-3x-marketplace-self-managed.md)
