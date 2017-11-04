---
title: "Az Azure-Előfeltételek OpenShift |} Microsoft Docs"
description: "Az Azure-ban OpenShift telepítendő előfeltételek."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 0c90b8a6d17fa293b6708d942afd35e1333623cb
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="common-prerequisites-for-openshift-in-azure"></a>Az Azure-ban OpenShift közös előfeltételei

Az Azure-ban OpenShift telepítésekor nincsenek függetlenül attól, hogy elérhetővé tett OpenShift származási vagy OpenShift tároló Platform néhány általános előfeltételek.

OpenShift telepítésének Ansible playbooks használatával történik. Ansible SSH a telepítési lépések végrehajtásához a fürt része lesz gazdagépek való kapcsolódáshoz használ.
Az SSH-kapcsolat kezdeményezése a távoli gazdagépek nincs semmilyen módon nem adja meg. Emiatt a titkos kulcs nem lehet vele társított egy jelszót, vagy a telepítés sikertelen lesz.
A virtuális gépek vannak telepítve a Resource Manager-sablonok használatával, mert ugyanazzal a kulccsal szolgál a hozzáférés az összes virtuális gépen. Igazolnia kell a megfelelő titkos kulccsal szúrjon be a virtuális Gépet, amely végrehajtja az összes playbooks is.
Ehhez biztonságos használjuk az Azure Key Vault továbbítja a virtuális Gépet a titkos kulccsal.

Ha az állandó tároló tárolók szükség van, állandó kötetek (PV) van szükség. Ezek PVs kell valamilyen állandó tároló támogassa. OpenShift támogatja az Azure-lemezeket (VHD-k) ezt a funkciót, de Azure először konfigurálni kell a felhőszolgáltatóként. Ebben a modellben OpenShift lesz:

- Hozzon létre egy virtuális merevlemez-objektum egy Azure Storage-fiók
- Csatlakoztassa a VHD-fájlt egy virtuális Gépet, és formázza a kötetet
- A kötet fogyasztanak csatlakoztatása

Ennek működéséhez OpenShift szükséges engedélyekkel az Azure-ban a korábbi műveletek végrehajtásához. Ennek érdekében a szolgáltatás egyszerű van szükség. A szolgáltatás egyszerű (SP) az Azure Active Directoryban, amely engedéllyel rendelkezik az erőforrások biztonsági fiók.
A szolgáltatás egyszerű hozzáférést a Storage-fiókok és a virtuális gépek, a fürtöt alkotó rendelkeznie kell. Ha az összes OpenShift fürterőforrás egyetlen erőforráscsoporthoz vannak telepítve, az SP kaphatnak engedélyeket erőforrás csoporthoz.

Ez az útmutató ismerteti a szükséges előfeltételek társított összetevők létrehozása.

> [!div class="checklist"]
> * Hozzon létre egy KeyVault a OpenShift fürthöz SSH-kulcsok kezeléséhez.
> * Hozzon létre egy egyszerű Azure Felhőszolgáltató általi használatra.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 
Jelentkezzen be Azure előfizetés a [az bejelentkezési](/cli/azure/#login) parancsot, és kövesse a képernyőn megjelenő utasításokat, vagy kattintson **próbálja ki** felhő rendszerhéj használata.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Javasoljuk, hogy használja-e egy dedikált erőforráscsoport üzemeltetéséhez a Key Vault - elkülönül az erőforráscsoport, amely a OpenShift fürt erőforrásait telepíti azokat. 

Az alábbi példa létrehoz egy erőforráscsoportot *keyvaultrg* a a *eastus* helyét.

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása
Hozzon létre egy KeyVault a fürthöz az SSH-kulcsok tárolására az [az keyvault létrehozása](/cli/azure/keyvault#create) parancsot. A Key Vault nevének globálisan egyedinek kell lennie.

Az alábbi példakód létrehozza a keyvault nevű *keyvault* a a *keyvaultrg* erőforráscsoportot.

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>SSH-kulcs létrehozása 
Biztonságos hozzáférés a OpenShift származási fürthöz SSH-kulcs szükséges. Hozzon létre egy SSH-kulcspár használatával a `ssh-keygen` (a Linux- vagy Mac) parancsot.
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Létrehozhat SSH-kulcspárral nem lehet egy hozzáférési kódot.

További információk a Windows rendszeren az SSH-kulcsok [létrehozása az SSH-kulcsok Windows](/azure/virtual-machines/linux/ssh-from-windows).

## <a name="store-ssh-private-key-in-key-vault"></a>Kulcstároló titkos SSH-kulcs tárolása
A OpenShift telepítéshez való biztonságos hozzáférés a OpenShift fő létrehozott SSH-kulcsot használ. Ahhoz, hogy az SSH-kulcs biztonságosan lekérhessék a központi telepítés, a kulcs tárolása Key Vault a következő parancsot:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift.rsa
```

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása 
OpenShift kommunikál az Azure-ban, a felhasználónév és jelszó vagy egy egyszerű szolgáltatást. Egy Azure szolgáltatás egyszerű egy biztonsági azonosító, amely alkalmazások, szolgáltatások és automatizálási eszközökkel, például a OpenShift használható. Szabályozza, és adja meg az engedélyeket, hogy milyen műveletek a szolgáltatás egyszerű hajthat végre az Azure-ban. A biztonság növelése érdekében csak a felhasználónév és jelszó megadása keresztül alábbi példa létrehoz egy alapszintű service egyszerű.

Az egyszerű szolgáltatás létrehozása [az ad sp létrehozása-az-rbac](/cli/azure/ad/sp#create-for-rbac) és kimeneti OpenShift szükséges hitelesítő adatokat.

Az alábbi példa létrehoz egy szolgáltatást egyszerű, és közreműködői engedélyekkel rendeli egy contoso.com nevű erőforráscsoport. Ha a Windows rendszerben a végrehajtást ```az group show --name myResourceGroup --query id``` külön-külön és a kimeneti segítségével hírcsatorna a--hatókörök lehetőséget.

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {Strong Password} \
          --scopes $(az group show --name myResourceGroup --query id)
```

Jegyezze fel a parancs által visszaadott appId-tulajdonság.
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
 > Ne hozzon létre nem biztonságos jelszót.  Kövesse az alábbi cikkben ismertetett útmutatást: [Az Azure AD-jelszavakra vonatkozó szabályok és korlátozások](/azure/active-directory/active-directory-passwords-policy).

A szolgáltatás rendszerbiztonsági tagok további információkért lásd: [hozzon létre egy Azure szolgáltatás egyszerű Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

## <a name="next-steps"></a>Következő lépések

Ez a cikk ismerteti a következő témaköröket:
> [!div class="checklist"]
> * Hozzon létre egy KeyVault a OpenShift fürthöz SSH-kulcsok kezeléséhez.
> * Hozzon létre egy egyszerű Azure Felhőszolgáltató általi használatra.

Most lépjen a OpenShift-fürt üzembe helyezése

- [OpenShift származási telepítése](./openshift-origin.md)
- [OpenShift tároló Platform telepítése](./openshift-container-platform.md)

