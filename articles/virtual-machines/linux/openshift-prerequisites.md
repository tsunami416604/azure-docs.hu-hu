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
ms.openlocfilehash: c6758e8e1a9d9595ae8efb0b8c5aba0b81b0dc38
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2017
---
# <a name="common-prerequisites-for-deploying-openshift-in-azure"></a>Az Azure-ban OpenShift telepítésének közös előfeltételei

Ez a cikk OpenShift származási vagy OpenShift tároló Platform az Azure-ban történő telepítésének általános előfeltételeit ismerteti.

A telepítése az OpenShift Ansible playbooks használja. Ansible Secure Shell (SSH) segítségével csatlakozzon a fürt összes gazdagépére telepítés végrehajtásához.

A távoli gazdagépekhez az SSH-kapcsolat kezdeményezésekor nem adhat meg egy jelszót. Emiatt a titkos kulcs nem lehet vele társított jelszót, vagy központi telepítése sikertelen.

A virtuális gépek (VM) telepítése Azure Resource Manager-sablonok keresztül, mert ugyanazzal a kulccsal minden virtuális gép elérésére szolgál. A virtuális Gépet, amely végrehajtja az összes playbooks, valamint a megfelelő titkos kulcsnak behelyezése kell. Ehhez biztonságos segítségével egy az Azure key vault továbbítja a titkos kulcsot a virtuális Gépet.

Ha az állandó tároló tárolók szükség van, állandó kötetek szükség. OpenShift támogatja az Azure virtuális merevlemezek (VHD-k) ezt a funkciót, de Azure először konfigurálni kell a felhőszolgáltatóként. 

Ebben a modellben OpenShift:

- Az Azure Storage-fiók egy VHD-objektumot hoz létre.
- Csatlakoztatja a VHD-fájlt egy virtuális gép és a formátum a kötetet.
- A kötet fogyasztanak csatlakoztatja.

A konfiguráció működéséhez OpenShift kell a korábbi műveletek végre az Azure-ban. Ez egy egyszerű szolgáltatással elérése. Az egyszerű szolgáltatásnév az Azure Active Directoryban, amely engedéllyel rendelkezik az erőforrások biztonsági fiók.

Az egyszerű szolgáltatásnév hozzáférhetnek a storage-fiókok és a virtuális gépek, a fürtöt alkotó rendelkeznie kell. Minden OpenShift fürt erőforrásait telepíti egyetlen erőforráscsoportként működnek, ha a szolgáltatás egyszerű kaphatnak engedélyeket erőforrás csoporthoz.

Ez az útmutató ismerteti a Előfeltételek társított összetevők létrehozása.

> [!div class="checklist"]
> * Hozzon létre egy kulcstartót a OpenShift fürthöz SSH-kulcsok kezeléséhez.
> * Hozzon létre egy egyszerű szolgáltatást az Azure Cloud Solution Provider általi használatra.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 
Jelentkezzen be Azure előfizetés a [az bejelentkezési](/cli/azure/#login) parancsot, és kövesse a képernyőn megjelenő utasításokat, vagy kattintson **próbálja ki** felhő rendszerhéj használata.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Egy dedikált erőforráscsoportot a key vault-segítségével. Ez a csoport nem azonos az erőforráscsoport, amelybe a OpenShift fürterőforrások telepítése. 

Az alábbi példa létrehoz egy erőforráscsoportot *keyvaultrg* a a *eastus* helye:

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása
Hozzon létre egy kulcstartót a fürthöz az SSH-kulcsok tárolására az [az keyvault létrehozása](/cli/azure/keyvault#create) parancsot. A kulcstároló neve globálisan egyedinek kell lennie.

Az alábbi példakód létrehozza nevű kulcstároló *keyvault* a a *keyvaultrg* erőforráscsoport:

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>SSH-kulcs létrehozása 
Biztonságos hozzáférés a OpenShift származási fürthöz SSH-kulcs szükséges. Hozzon létre egy SSH-kulcspárral a `ssh-keygen` parancsot (a Linux- vagy macOS):
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Az SSH-kulcspárral nem lehet a jelszót.

Az SSH-kulcsok a Windows további információkért lásd: [létrehozása az SSH-kulcsok Windows](/azure/virtual-machines/linux/ssh-from-windows).

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>A titkos SSH-kulcs tárolása az Azure Key Vault
A OpenShift telepítéshez való biztonságos hozzáférés a OpenShift fő létrehozott SSH-kulcsot használ. Ahhoz, hogy az SSH-kulcs biztonságosan lekérhessék a központi telepítés, tárolja a kulcsot a Key Vault a következő paranccsal:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift.rsa
```

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása 
OpenShift Azure keresztül kommunikál a felhasználónév és jelszó vagy egy egyszerű szolgáltatást. Egy Azure szolgáltatás egyszerű egy biztonsági azonosító, amely alkalmazások, szolgáltatások és automatizálási eszközökkel, például a OpenShift használható. Szabályozza, és adja meg az engedélyeket, hogy mely műveletek a szolgáltatás egyszerű hajthat végre az Azure-ban. Túl csak a felhasználónév és jelszó megadása a biztonság növelése érdekében ez a példa létrehoz egy alapszintű service egyszerű.

Az egyszerű szolgáltatás létrehozása [az ad sp létrehozása-az-rbac](/cli/azure/ad/sp#create-for-rbac) és kimeneti OpenShift szükséges hitelesítő adatokat.

Az alábbi példa létrehoz egy szolgáltatást egyszerű, és közreműködői engedélyekkel rendeli egy contoso.com nevű erőforráscsoport. Ha használja a Windows, a végrehajtást ```az group show --name myResourceGroup --query id``` külön-külön és a kimeneti segítségével hírcsatorna a--hatókörök lehetőséget.

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {Strong Password} \
          --scopes $(az group show --name myResourceGroup --query id)
```

Jegyezze fel a parancs által visszaadott appId-tulajdonság:
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
 > Győződjön meg arról, biztonságos jelszó létrehozása. Kövesse az alábbi cikkben ismertetett útmutatást: [Az Azure AD-jelszavakra vonatkozó szabályok és korlátozások](/azure/active-directory/active-directory-passwords-policy).

A szolgáltatás rendszerbiztonsági tagok további információkért lásd: [hozzon létre egy Azure szolgáltatás egyszerű Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="next-steps"></a>Következő lépések

Ez a cikk ismerteti a következő témaköröket:
> [!div class="checklist"]
> * Hozzon létre egy kulcstartót a OpenShift fürthöz SSH-kulcsok kezeléséhez.
> * Hozzon létre egy egyszerű szolgáltatást az Azure Cloud Solution Provider általi használatra.

A következő OpenShift-fürt üzembe helyezése:

- [OpenShift származási telepítése](./openshift-origin.md)
- [OpenShift tároló Platform telepítése](./openshift-container-platform.md)

