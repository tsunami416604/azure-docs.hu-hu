---
title: Jelentkezzen be az Azure Active Directory hitelesítő adatokkal rendelkező Linux virtuális gép |} A Microsoft Docs
description: Ez az útmutató bemutatja hogyan kell létrehozni és konfigurálni a felhasználói bejelentkezéseket az Azure Active Directory-hitelesítés használata Linux virtuális gép
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/17/2018
ms.author: cynthn
ms.openlocfilehash: 4f86dee539e3cc5a90db828ed11dbd225a00555d
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334635"
---
# <a name="log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Jelentkezzen be egy Linux rendszerű virtuális gép az Azure-ban az Azure Active Directory-hitelesítés (előzetes verzió)

Linux rendszerű virtuális gépek (VM) az Azure-ban, a biztonság növelése érdekében integrálható az Azure Active Directory (AD) hitelesítési szolgáltatással. Ha Linux rendszerű virtuális gépekhez az Azure AD-hitelesítés használata esetén központilag szabályozhatja és engedélyezheti vagy megtagadhatja a hozzáférést a virtuális gépek olyan házirendek kényszerítését. Ez a cikk bemutatja, hogyan hozhat létre, és a egy Linux rendszerű virtuális gép az Azure AD-hitelesítés használatára konfigurálja.

> [!NOTE]
> Ez a funkció előzetes verzióban érhető el, és az éles virtuális gépek vagy a számítási feladatok nem ajánlott. A funkció használatához a tesztelés után elveti várt tesztelési virtuális gépen.

Nincsenek számos előnnyel jár, jelentkezzen be az Azure-beli, Linux rendszerű virtuális gépek az Azure AD-hitelesítés használatával többek között:

- **Továbbfejlesztett biztonság:**
  - A vállalati AD hitelesítő adatait használhatja a bejelentkezni az Azure Linux rendszerű virtuális gépek. Hiba esetén nem kell helyi rendszergazdai fiókok létrehozása és kezelése a hitelesítő adatok élettartama.
  - A helyi rendszergazdai fiókok támaszkodás csökkentésével, nem kell aggódnia a hitelesítő adatok elvesztése/lopás, felhasználók gyenge azonosító adatokat stb.
  - A jelszó összetettségét és az Azure AD címtárhoz konfigurált élettartama jelszóházirendek segítségével biztonságos Linux virtuális gépek is.
  - További biztonságos jelentkezzen be az Azure virtual machines a multi-factor authentication is konfigurálhat.
  - Jelentkezzen be a Linux rendszerű virtuális gépek az Azure Active Directory lehetővé teszi az ügyfelek által használt is működik [összevonási szolgáltatások](../../active-directory/hybrid/how-to-connect-fed-whatis.md).

- **Zökkenőmentes együttműködés:** With Role-Based hozzáférés-vezérlés (RBAC), is megadhat, aki bejelentkezhet egy adott virtuális gépre normál felhasználóként vagy rendszergazdai jogosultságokkal. Amikor a felhasználó csatlakozzon, vagy hagyja meg a csapat, frissítheti a virtuális gép megfelelő hozzáférést biztosít az RBAC házirend. Ez a tapasztalat jóval egyszerűbb, mint el kell távolítani a virtuális gépek eltávolítja a felesleges nyilvános SSH-kulcsokat, ha. Amikor az alkalmazottak elhagyják a szervezet és a felhasználói fiók le van tiltva vagy Azure AD-ből eltávolított, már nem rendelkeznek az erőforrásokhoz való hozzáférés.

## <a name="supported-azure-regions-and-linux-distributions"></a>Támogatott Azure-régiók és a Linux-disztribúciók

A következő Linux-disztribúció jelenleg a szolgáltatás az előzetes verzióban támogatja:

| Disztribúció | Verzió |
| --- | --- |
| CentOS | CentOS 6.9 és a CentOS 7.4 |
| Debian | Debian 9 |
| RedHat Enterprise Linux | RHEL RHEL 7, 6 | 
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04, Ubuntu Server 17.10 és Ubuntu Server 18.04 |

A következő Azure-régiók jelenleg a szolgáltatás az előzetes verzióban támogatja:

- Globális Azure-régiók

>[!IMPORTANT]
> Az előzetes verziójú funkció használatához csak egy támogatott Linux-disztribúció telepítése és a egy támogatott Azure-régióban. A funkció nem támogatott az Azure Government vagy független felhőkben.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a helyi telepítése és használata a parancssori felület választja, az oktatóanyaghoz, hogy futnak-e az Azure CLI 2.0.31-es verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-linux-virtual-machine"></a>Linuxos virtuális gépek létrehozása

Hozzon létre egy erőforráscsoportot a [az csoport létrehozása](/cli/azure/group#az-group-create), majd hozzon létre egy virtuális Gépet a [az virtuális gép létrehozása](/cli/azure/vm#az-vm-create) támogatott disztribúciójának használatával és a egy támogatott régióban. Az alábbi példa egy nevű virtuális Gépet helyez üzembe *myVM* használó *Ubuntu 16.04 LTS* helyezzen egy erőforráscsoportban nevű *myResourceGroup* a a *southcentralus*  régióban. A következő példákban megadhatja a saját erőforráscsoport és igény szerint egy virtuális gép neve.

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

Jelentkezzen be egy Linuxos virtuális Gépre az Azure AD hitelesítő adatait, telepítse az Azure Active Directory naplózási Virtuálisgép-bővítményt. A Virtuálisgép-bővítmények olyan kisebb alkalmazásoknál, amelyek az üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosít az Azure-beli virtuális gépeken. Használati [virtuálisgép-bővítmény csoportot az](/cli/azure/vm/extension#az-vm-extension-set) telepítéséhez a *AADLoginForLinux* nevű virtuális gép futtatására szolgáló bővítmény *myVM* a a *myResourceGroup* erőforrás csoport:

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

A *provisioningState* , *sikeres* a bővítményt a virtuális gép telepítése után jelenik meg.

## <a name="configure-role-assignments-for-the-vm"></a>A virtuális gép szerepkör-hozzárendelések konfigurálása

Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) házirend határozza meg, aki bejelentkezhet a virtuális Gépet. Engedélyezze a virtuális gép bejelentkezési két RBAC-szerepkörök használhatók:

- **A virtuális gépre való rendszergazdai bejelentkezés**: az ehhez a szerepkörhöz rendelt felhasználók bejelentkezhet egy Azure virtuális géphez, a Windows rendszergazdai vagy a Linux gyökérszintű felhasználójának jogosultságaival.
- **A virtuális gépre való felhasználói bejelentkezés**: az ehhez a szerepkörhöz rendelt felhasználók bejelentkezhet egy Azure virtuális gépen a normál felhasználói jogosultságokat.

> [!NOTE]
> Lehetővé teszi, hogy jelentkezzen be a virtuális Gépre ssh-n keresztül, hozzá kell rendelnie vagy a *virtuális gépre való rendszergazdai bejelentkezés* vagy *virtuális gépre való felhasználói bejelentkezés* szerepkör. Az Azure-felhasználó a *tulajdonosa* vagy *közreműködői* egy virtuális géphez hozzárendelt szerepkörök automatikusan nem rendelkezik jogosultságokkal jelentkezzen be a virtuális gép ssh-n keresztül.

Az alábbi példában [az szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create) hozzárendelése a *virtuális gépre való rendszergazdai bejelentkezés* a virtuális gép az aktuális Azure felhasználói szerepkört. Az aktív Azure-fiók felhasználóneve beszerzett [az fiók show](/cli/azure/account#az-account-show), és a *hatókör* van állítva az előző lépésben létrehozott virtuális géphez [az vm show](/cli/azure/vm#az-vm-show). A hatókört is hozzárendelhetők egy erőforrás csoportba vagy előfizetésbe szintjén, és a normál öröklési RBAC-engedélyek vonatkoznak. További információkért lásd: [szerepköralapú hozzáférés-vezérlés](../../azure-resource-manager/resource-group-overview.md#access-control)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Ha az AAD-tartomány és a tartomány felhasználónév nem egyezik, meg kell adnia a felhasználói fiókjához való Objektumazonosítóját a *--assignee-object-id*, nem csak a felhasználónév *--assignee*. Objektumazonosító szerezheti be a felhasználói fiókjához [az ad felhasználó-lista](/cli/azure/ad/user#az-ad-user-list).

RBAC használata az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése a további információkért tekintse meg a használatával a [Azure CLI-vel](../../role-based-access-control/role-assignments-cli.md), [az Azure portal](../../role-based-access-control/role-assignments-portal.md), vagy [Azure PowerShell-lel](../../role-based-access-control/role-assignments-powershell.md).

Beállíthatja, hogy jelentkezzen be a Linux rendszerű virtuális gép egy adott felhasználót többtényezős hitelesítés megkövetelése az Azure ad-ben. További információkért lásd: [Azure multi-factor Authentication a felhőben – első lépések](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="log-in-to-the-linux-virtual-machine"></a>Jelentkezzen be a Linux rendszerű virtuális gép

Először is megtekintheti a virtuális gép nyilvános IP-címét [az vm show](/cli/azure/vm#az-vm-show):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Jelentkezzen be az Azure-beli Linuxos virtuális gép az Azure ad-ben használt hitelesítő adataival. A `-l` paraméter lehetővé teszi a saját Azure AD-fiók címét adja meg. Adja meg a virtuális gép nyilvános IP-címét az előző parancs kimenete:

```azurecli-interactive
ssh -l azureuser@contoso.onmicrosoft.com publicIps
```

Jelentkezzen be Azure AD egy egyszeri használható kóddal, a rendszer felszólítja [ https://microsoft.com/devicelogin ](https://microsoft.com/devicelogin). Másolja és illessze be a bejelentkezési oldaláról, az egyszeri használható kódja a következő példában látható módon:

```bash
~$ ssh -l azureuser@contoso.onmicrosoft.com 13.65.237.247
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJS3K6X4D to authenticate. Press ENTER when ready.
```

Amikor a rendszer kéri, adja meg az Azure AD bejelentkezési hitelesítő adatait, a bejelentkezési oldalára. A következő üzenet jelenik meg a böngésző, ha a hitelesítés sikerült:

    You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.

Zárja be a böngészőablakot, térjen vissza az SSH parancssor, és nyomja le az **Enter** kulcsot. Most már bejelentkezett az Azure-beli Linuxos virtuális gép szerepkör engedélyeivel, hozzárendelt, mint például *a virtuális gép felhasználójának* vagy *VM rendszergazda*. Ha a felhasználói fiók hozzá van rendelve a *virtuális gépre való rendszergazdai bejelentkezés* szerepkör, használhatja a `sudo` legfelső szintű jogosultságokat igénylő parancsok futtatásához.

## <a name="sudo-and-aad-login"></a>Sudo és AAD-bejelentkezés

Az első futtatásakor sudo, a rendszer kéri, másodszor hitelesítéséhez. Ha nem szeretné futtatni a sudo újra hitelesíteni kell, szerkesztheti a sudoers fájl `/aad/etc/sudoers.d/aad_admins` , és cserélje le ezt a sort:

```bash
%aad_admins ALL=(ALL) ALL
```
az ezt a sort:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Bejelentkezési problémák elhárítása

Az Azure AD-beli hitelesítő ssh meg néhány gyakori hibák közé tartozik a nincs hozzárendelve, RBAC-szerepkörök és ismételni utasításokat követve jelentkezzen be. Az alábbi szakaszok segítségével javítsa ki ezeket a problémákat.

### <a name="access-denied-rbac-role-not-assigned"></a>A hozzáférés megtagadva: RBAC szerepkör nincs hozzárendelve

Ha az SSH-parancssorban a következő hibát látja, ellenőrizze, hogy [konfigurált RBAC-házirendeket](#configure-rbac-policy-for-the-virtual-machine) a virtuális gép, amely engedélyezi a felhasználó vagy a *virtuális gépre való rendszergazdai bejelentkezés* vagy *virtuális Számítógép-felhasználói bejelentkezésre* szerepkör:

```bash
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>Folyamatos SSH bejelentkezési kérések

Ha sikeresen befejezte a hitelesítési lépés egy webböngészőben, azonnal kérheti az ismételt bejelentkezéshez egy friss kóddal. Ez a hiba általában okozza az SSH-parancssorban megadott bejelentkezési nevet és a regisztrált az Azure AD-fiók nem egyeznek. A probléma megoldása:

- Győződjön meg arról, hogy az SSH-parancssorban megadott bejelentkezési neve helyesen-e. A bejelentkezési neve egy elgépelte az SSH-parancssorban megadott bejelentkezési nevet és a regisztrált az Azure AD-fiók közötti eltérést okozhat. Ha például a beírt *azuresuer@contoso.onmicrosoft.com* helyett *azureuser@contoso.onmicrosoft.com*.
- Ha több felhasználói fiók rendelkezik, győződjön meg arról, egy másik felhasználói fiókkal, a böngésző ablakában nem ad meg Azure ad-ben való bejelentkezéskor.
- Linux rendszerű, a kis-és nagybetűket operációs rendszer. Van különbség a "Azureuser@contoso.onmicrosoft.com"és"azureuser@contoso.onmicrosoft.com", amely eltérést okozhat. Győződjön meg arról, hogy a megfelelő Kisbetű/nagybetű megkülönböztetése az SSH a parancssorba az UPN megadását.

## <a name="preview-feedback"></a>Előzetes verzióval kapcsolatos visszajelzések

Ossza meg velünk véleményét a használja a előzetes funkciót vagy a jelentés problémákkal kapcsolatban az [az Azure AD Visszajelzési fórum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>További lépések

További információ az Azure Active Directory: [Mi az Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
