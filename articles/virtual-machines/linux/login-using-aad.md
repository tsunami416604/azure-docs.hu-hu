---
title: Jelentkezzen be Azure Active Directory hitelesítő adatokkal rendelkező Linux virtuális gép |} Microsoft Docs
description: Ez az útmutató az útmutató létrehozása és konfigurálása az Azure Active Directory-hitelesítés használatára a felhasználó bejelentkezések során a Linux virtuális gépek
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/16/2018
ms.author: iainfou
ms.openlocfilehash: 96cc7aeb5fd1c64dc3793a801a4a5b759e7558b9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652872"
---
# <a name="log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Jelentkezzen be egy Linux virtuális gép az Azure-ban az Azure Active Directory-hitelesítés (előzetes verzió)

A Linux virtuális gépek (VM) az Azure-ban a biztonság növelése érdekében Azure Active Directory (AD) hitelesítési integrálható. Linux virtuális gépek az Azure AD-alapú hitelesítés használatakor, központilag szabályozza, és házirendek kényszerítését, engedélyezi vagy megtagadja a hozzáférést a virtuális gépekhez. Ez a cikk bemutatja, hogyan hozza létre és konfigurálja a Linux virtuális Gépet az Azure AD-hitelesítés használatát.

> [!NOTE]
> Ez a funkció jelenleg előzetes verzióban érhető, és a termelési virtuális gépek és a munkaterhelések nem ajánlott. A szolgáltatás használatához a teszt virtuális gép várhatóan tesztelés után elveti.

Nincsenek az Azure AD-hitelesítés használatával próbál bejelentkezni a Linux virtuális gépek az Azure számos előnyt többek között:

- **Továbbfejlesztett biztonság:**
  - A vállalati AD hitelesítő adatok segítségével jelentkezzen be Azure Linux virtuális gépek. Nincs szükség helyi rendszergazdai fiók létrehozása és kezelése a hitelesítő adatok élettartama.
  - Csökkenti a helyi rendszergazda fiókok támaszkodik, nem kell foglalkoznia a hitelesítő adatok elvesztése/lopás, felhasználók gyenge hitelesítő adatok stb.
  - A jelszó összetettségét, és az Azure AD-címtár beállított élettartam jelszóházirendek segítségével biztonságos Linux virtuális gépek is.
  - További biztonságos bejelentkezés az Azure virtuális gépekhez többtényezős hitelesítést is konfigurálhat.
  - A Linux virtuális gépekhez, és az Azure Active Directory bejelentkezési lehetőséget is működik használó ügyfelek számára [összevonási szolgáltatások](../../active-directory/connect/active-directory-aadconnectfed-whatis.md).

- **Zökkenőmentes együttműködés:** With Role-Based hozzáférés-vezérlés (RBAC), adhatja ki lehet bejelentkezni egy adott virtuális gép felhasználói vagy rendszergazdai jogosultságokkal. Ha a felhasználók csatlakozni, vagy hagyja meg a csoport, frissítheti a Szerepalapú házirendet a virtuális gép a megfelelő engedélyt. A szolgáltatás sokkal egyszerűbb, mint a virtuális gépek eltávolítja a szükségtelen nyilvános SSH-kulcsok megtisztítás kellene. Amikor az alkalmazottak elhagyják a munkahelyet, és a felhasználói fiók le van tiltva vagy az Azure AD-ből eltávolítva, már nem rendelkeznek az erőforrásokhoz való hozzáférés.

### <a name="supported-azure-regions-and-linux-distributions"></a>Támogatott Azure-régiók és a Linux terjesztéseket

A következő Linux terjesztésekről jelenleg az előzetes funkció támogatja:

| Disztribúció | Verzió |
| --- | --- |
| CentOS | CentOS 6.9 és 7.4 CentOS |
| RedHat Enterprise Linux | RHEL 7 | 
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04 és Ubuntu Server 17.10 |

A következő Azure-régiók jelenleg az előzetes funkció támogatja:

- Globális Azure-régiók

>[!IMPORTANT]
> Ezt az előzetes funkciót használ, csak telepítenie kell egy támogatott Linux distro és a támogatott Azure-régiót. A funkció nem támogatott Azure Government vagy szuverén felhők.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ha ez az oktatóanyag van szükség, hogy futnak-e az Azure parancssori felület 2.0.31 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-linux-virtual-machine"></a>Linuxos virtuális gépek létrehozása

Hozzon létre egy erőforráscsoportot a [az csoport létrehozása](/cli/azure/group#az-group-create), majd hozza létre a virtuális gép és [az virtuális gép létrehozása](/cli/azure/vm#az-vm-create) támogatott distro használatával és egy támogatott régióban. A következő példa telepíti egy nevű virtuális gép *myVM* használó *Ubuntu 16.04 LTS* be egy erőforráscsoport nevű *myResourceGroup* a a *southcentralus*  régióban. A következő példákban megadhatja a saját erőforráscsoport és a virtuális gépek nevénél igény szerint.

```azurecli-interactive
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

A virtuális gép és a kapcsolódó erőforrások létrehozása csak néhány percet vesz igénybe.

## <a name="install-the-azure-ad-login-vm-extension"></a>Az Azure AD bejelentkezési Virtuálisgép-bővítmény telepítése

Jelentkezzen be az Azure AD hitelesítő adatokkal rendelkező Linux virtuális Gépet, telepítse az Azure Active Directory napló Virtuálisgép-bővítmény. Virtuálisgép-bővítmények kis alkalmazásokat, amelyek telepítés utáni konfigurációs és automatizálási feladatok Azure virtuális gépeken. Használja [az virtuálisgép-bővítmény készlet](/cli/azure/vm/extension#az-vm-extension-set) telepítéséhez a *AADLoginForLinux* nevű virtuális gép kiterjesztés *myVM* a a *myResourceGroup* erőforrás csoport:

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

A *provisioningState* a *sikeres* jelenik meg a bővítményt a virtuális gép telepítése után.

## <a name="configure-role-assignments-for-the-vm"></a>A virtuális gép szerepkör-hozzárendelések konfigurálása

Azure szerepköralapú hozzáférés-vezérlés (RBAC) házirend határozza meg, akik jelentkezhetnek be a virtuális Gépet. Két RBAC-szerepkörök használatával engedélyezi a VM bejelentkezési:

- **Virtuális gép rendszergazdai bejelentkezés**: a szerepkörrel rendelkező felhasználók jelentkezhetnek be egy Azure virtuális gépen a rendszergazda Windows vagy Linux legfelső szintű felhasználói jogosultságokkal.
- **Virtuális gép felhasználói bejelentkezés**: a szerepkörrel rendelkező felhasználók jelentkezhetnek be az Azure virtuális gép normál felhasználói jogosultságokkal.

> [!NOTE]
> Ahhoz, hogy a felhasználónak be kell jelentkeznie a virtuális gép SSH-n keresztül, vagy rendeljen a *virtuális gép rendszergazdai bejelentkezés* vagy *virtuális gép felhasználói bejelentkezés* szerepkör. Egy Azure felhasználót a *tulajdonos* vagy *közreműködő* szerepkörök hozzárendelése a virtuális gépek automatikusan nincs jogosultsága a bejelentkezés a virtuális gép SSH-n keresztül.

Az alábbi példában [az szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create) hozzárendelni a *virtuális gép rendszergazdai bejelentkezés* a virtuális gép az aktuális Azure felhasználói szerepkört. Az aktív Azure-fiók felhasználóneve kapott [az fiók megjelenítése](/cli/azure/account#az-account-show), és a *hatókör* van beállítva a virtuális gép az előző lépésben létrehozott [az vm megjelenítése](/cli/azure/vm#az-vm-show). A hatókör is hozzárendelhetők egy erőforrás csoport vagy az előfizetés szintjén, és normál RBAC öröklési engedélyek vonatkoznak. További információkért lásd: [szerepköralapú hozzáférés-vezérlést](../../azure-resource-manager/resource-group-overview.md#access-control)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Ha az AAD-tartományban, és a bejelentkezési felhasználónév tartomány nem egyezik, meg kell adnia a felhasználói fiók az Objektumazonosító a *--hozzárendelt személy-objektum-azonosítója*, nem csak a felhasználónév *--megbízottja*. Ezt úgy szerezheti be az Objektumazonosító felhasználó fiókját, [az ad felhasználó-lista](/cli/azure/ad/user#az-ad-user-list).

Az RBAC használata az Azure-előfizetés erőforrásokhoz való hozzáférés kezelése a további információkért lásd: használatával a [Azure CLI 2.0](../../role-based-access-control/role-assignments-cli.md), [Azure-portálon](../../role-based-access-control/role-assignments-portal.md), vagy [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Konfigurálhatja az Azure AD-be való bejelentkezéshez a Linux virtuális gép egy adott felhasználó többtényezős hitelesítést is. További információkért lásd: [Ismerkedés az Azure multi-factor Authentication a felhőben](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="log-in-to-the-linux-virtual-machine"></a>Jelentkezzen be a Linux virtuális gép

Először, megtekintheti a nyilvános IP-címet a virtuális gép a [az vm megjelenítése](/cli/azure/vm#az-vm-show):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Jelentkezzen be a Azure Linux virtuális gép az Azure AD hitelesítő adataival. A `-l` paraméter lehetővé teszi, hogy a saját Azure AD-fiókot címe adható meg. Adja meg a virtuális gép nyilvános IP-címét az előző parancs kimenete:

```azurecli-interactive
ssh -l azureuser@contoso.onmicrosoft.com publicIps
```

Jelentkezzen be az Azure AD egy egyszeri használata kóddal, kéri [ https://microsoft.com/devicelogin ](https://microsoft.com/devicelogin). Másolja és illessze be az egyszeri használata kódot az eszköz bejelentkezési oldalhoz, az alábbi példában látható módon:

```bash
~$ ssh -l azureuser@contoso.onmicrosoft.com 13.65.237.247
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJS3K6X4D to authenticate. Press ENTER when ready.
```

Amikor a rendszer kéri, adja meg a bejelentkezési oldalon Azure AD bejelentkezési hitelesítő adatait. A következő üzenet jelenik a webböngészőben amikor sikeresen hitelesített:

    You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.

Zárja be a böngészőablakot, térjen vissza az SSH rákérdezés, és nyomja le az **Enter** kulcs. Most jelentkezett be a Azure Linux virtuális gép szerepkör engedélyeivel hozzárendelni, például a *VM felhasználói* vagy *VM rendszergazda*. Ha a felhasználói fiók hozzá van rendelve a *virtuális gép rendszergazdai bejelentkezés* szerepkör, használhatja a `sudo` legfelső szintű jogosultságokat igénylő parancsok futtatásához.

## <a name="troubleshoot-sign-in-issues"></a>Bejelentkezési problémák elhárítása

Néhány gyakran előforduló hiba, amikor az Azure AD hitelesítő adatait az SSH megpróbálja nincs hozzárendelve, RBAC-szerepkörök közé tartoznak, és jelentkezzen be a megjelenő utasításokat. Az alábbi szakaszok segítségével javítsa ki ezeket a problémákat.

### <a name="access-denied-rbac-role-not-assigned"></a>A hozzáférés megtagadva: RBAC-szerepkört nem hozzá van rendelve.

Ha az SSH-kérés a következő hiba látható, ellenőrizze, hogy rendelkezik [RBAC-házirendet konfigurált](#configure-rbac-policy-for-the-virtual-machine) a virtuális gép, amely engedélyezi a felhasználó vagy a *virtuális gép rendszergazdai bejelentkezés* vagy *virtuális Számítógép-felhasználói bejelentkezés* szerepkör:

```bash
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>Folyamatos SSH bejelentkezés kéri

Sikeresen végrehajtotta a hitelesítési lépést webböngészőben, ha lehetséges, hogy azonnal kérni fogja jelentkezzen be egy friss kóddal. Ez a hiba oka rendszerint az SSH-parancssorba megadott bejelentkezési név és az Azure ad Szolgáltatásba való bejelentkezéshez eltérést. Ez a probléma elhárításához:

- Győződjön meg arról, hogy az SSH-parancssorban megadott bejelentkezési neve helyesen-e. Egy elírás a bejelentkezési név a támadó a az SSH-parancssorban megadott bejelentkezési név és az Azure ad Szolgáltatásba való bejelentkezéshez eltérést. Például beírt *azuresuer@contoso.onmicrosoft.com* helyett *azureuser@contoso.onmicrosoft.com*.
- Ha több felhasználói fiókot, ellenőrizze, hogy egy másik felhasználói fiókkal a böngészőablakban nem ad meg, ha az Azure AD bejelentkezés.
- Linux operációs rendszer kis-és nagybetűket. Között eltérés van "Azureuser@contoso.onmicrosoft.com"és"azureuser@contoso.onmicrosoft.com", amely eltérést okozhat. Győződjön meg arról, hogy a helyes Kisbetű/nagybetű megkülönböztetése az SSH-parancssorba, adja meg az egyszerű Felhasználónevet.

## <a name="preview-feedback"></a>Előzetes visszajelzés

A visszajelzést az előzetes funkció vagy a jelentés problémák azt megosztása a [az Azure AD-visszajelzési fórumon](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>További lépések

Az Azure Active Directory további információkért lásd: [Mi az Azure Active Directory](../../active-directory/active-directory-whatis.md) és [Ismerkedés az Azure Active Directoryval](../../active-directory/get-started-azure-ad.md)
