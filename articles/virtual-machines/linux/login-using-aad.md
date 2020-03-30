---
title: Bejelentkezés Linuxos virtuális gépbe az Azure Active Directory hitelesítő adataival
description: Ismerje meg, hogyan hozhat létre és konfigurálhat linuxos virtuális gépet az Azure Active Directory-hitelesítés használatával történő bejelentkezéshez.
author: iainfoulds
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 08/29/2019
ms.author: iainfou
ms.openlocfilehash: 2731693667d2129a72da72455c6bbdd74c277697
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366491"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>Előzetes verzió: Bejelentkezés Linux os virtuális gépre az Azure-ban az Azure Active Directory-hitelesítéshasználatával

A Linux virtuális gépek (VM-ek) biztonságának növelése az Azure-ban, integrálhatja az Azure Active Directory (AD) hitelesítés. Ha Az Azure AD-hitelesítés linuxos virtuális gépek, központilag szabályozhatja és kényszerítheti a virtuális gépekhez való hozzáférést engedélyező vagy megtagadása szabályzatok. Ez a cikk bemutatja, hogyan hozhat létre és konfigurálhat egy Linux virtuális gép az Azure AD-hitelesítés.


> [!IMPORTANT]
> Az Azure Active Directory-hitelesítés jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
> Ezt a funkciót olyan tesztvirtuális gépen használja, amelyet a tesztelés után várhatóan elvet.
>


Az Azure AD-hitelesítés használatával számos előnye van az Azure-beli Linux-virtuális gépekre való bejelentkezéshez, többek között:

- **Fokozott biztonság:**
  - A vállalati AD-hitelesítő adatok segítségével jelentkezzen be az Azure Linux virtuális gépek. Nincs szükség helyi rendszergazdai fiókok létrehozására és a hitelesítő adatok élettartamának kezelésére.
  - Azáltal, hogy csökkenti a helyi rendszergazdai fiókokra való támaszkodást, nem kell aggódnia a hitelesítő adatok elvesztése / ellopása, a gyenge hitelesítő adatokat konfiguráló felhasználók stb.
  - A jelszó összetettsége és az Azure AD-címtárhoz konfigurált jelszóélettartam-házirendek a Linux-virtuális gépek biztonságossá tétele érdekében is.
  - Az Azure virtuális gépekbe való bejelentkezés további biztonságossá tétele érdekében konfigurálhatja a többtényezős hitelesítést.
  - Az Azure Active Directoryval linuxos virtuális gépekre való bejelentkezés az [Összevonási szolgáltatásokat](../../active-directory/hybrid/how-to-connect-fed-whatis.md)használó ügyfelek számára is működik.

- **Zökkenőmentes együttműködés:** A szerepköralapú hozzáférés-vezérlés (RBAC) segítségével megadhatja, hogy ki jelentkezhet be egy adott virtuális gépbe normál felhasználóként vagy rendszergazdai jogosultságokkal. Amikor a felhasználók csatlakoznak a csapathoz, vagy kilép a csapatból, frissítheti az RBAC-szabályzatot a virtuális gép számára, hogy szükség szerint hozzáférést biztosítson. Ez a tapasztalat sokkal egyszerűbb, mint a virtuális gépek scrub a felesleges SSH nyilvános kulcsok eltávolítása. Amikor az alkalmazottak elhagyják a szervezetet, és felhasználói fiókjuk le van tiltva vagy törlődik az Azure AD-ből, már nem férnek hozzá az erőforrásokhoz.

## <a name="supported-azure-regions-and-linux-distributions"></a>Támogatott Azure-régiók és Linux-disztribúciók

A következő Linux-disztribúciók jelenleg támogatottak a funkció előzetes verzióiban:

| Disztribúció | Verzió |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE Ugrás 42.3 |
| RedHat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04 és Ubuntu Server 18.04 |


A következő Azure-régiók jelenleg támogatottak a funkció előzetes verzióiban:

- Az összes globális Azure-régió

>[!IMPORTANT]
> Az előzetes verziós funkció használatához csak egy támogatott Linux-disztribúciót és egy támogatott Azure-régióban telepítsen. A szolgáltatás nem támogatott az Azure Government vagy a szuverén felhők.
>
> Ez a bővítmény nem használható az Azure Kubernetes Service (AKS) fürtök. További információt az [AKS támogatási szabályzatai című témakörben talál.](../../aks/support-policies.md)


Ha úgy dönt, hogy helyileg telepíti és használja a CLI-t, ez az oktatóanyag megköveteli, hogy az Azure CLI 2.0.31-es vagy újabb verzióját futassza. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

Az Azure-beli virtuális gépek Azure-beli virtuális gépei Azure-beli virtuális gépei számára az Azure-hitelesítés engedélyezéséhez biztosítania kell, hogy a virtuális gépek hálózati konfigurációja lehetővé tegye a kimenő hozzáférést a következő végpontokhoz a 443-as TCP-porton keresztül:

* https:\//login.microsoftonline.com
* https:\//login.windows.net
* https:\//device.login.microsoftonline.com
* https:\//pas.windows.net
* https:\//management.azure.com
* https:\//packages.microsoft.com

> [!NOTE]
> Jelenleg az Azure hálózati biztonsági csoportok nem konfigurálhatók az Azure AD-hitelesítéssel engedélyezett virtuális gépekhez.

## <a name="create-a-linux-virtual-machine"></a>Linuxos virtuális gép létrehozása

Hozzon létre egy erőforráscsoportot [az csoport létrehozása](/cli/azure/group#az-group-create), majd hozzon létre egy virtuális gép az [vm létrehozása](/cli/azure/vm#az-vm-create) egy támogatott elvezető és egy támogatott régióban. A következő példa egy *myVM* nevű virtuális gép üzembe helyezése, amely *az Ubuntu 16.04 LTS-t* használja egy *myResourceGroup* nevű erőforráscsoportba a *southcentralus* régióban. A következő példákban megadhat saját erőforráscsoport és virtuális gép nevét szükség szerint.

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

## <a name="install-the-azure-ad-login-vm-extension"></a>Az Azure AD bejelentkezési virtuálisgép-bővítmény telepítése

> [!NOTE]
> Ha ezt a bővítményt egy korábban létrehozott virtuális gépre telepíti, győződjön meg arról, hogy a gép legalább 1 GB memóriával rendelkezik, máshol a bővítmény telepítése sikertelen lesz

Ha azure AD-hitelesítő adatokkal szeretne bejelentkezni egy Linux virtuális gépbe, telepítse az Azure Active Directory bejelentkezési virtuálisgép-bővítményt. A virtuálisgép-bővítmények olyan kis alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure virtuális gépeken. Használja [az vm bővítménykészletet](/cli/azure/vm/extension#az-vm-extension-set) az *AADLoginForLinux* bővítmény telepítéséhez a *myResourceGroup erőforráscsoportmyVM* nevű virtuális gépre: *myVM*

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

A *kiépítésÁllapota* *sikeres* jelenik meg, ha a bővítmény sikeresen telepítve van a virtuális gépre. A virtuális gépnek szüksége van egy futó virtuálisgép-ügynökre a bővítmény telepítéséhez. További információ: [VM Agent Overview](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows).

## <a name="configure-role-assignments-for-the-vm"></a>Szerepkör-hozzárendelések konfigurálása a virtuális géphez

Az Azure szerepköralapú hozzáférés-vezérlési (RBAC) szabályzat határozza meg, hogy ki jelentkezhet be a virtuális gépbe. A virtuális gép bejelentkezésengedélyezéséhez két RBAC-szerepkör használható:

- **Virtuálisgép-rendszergazdai bejelentkezés:** A hozzárendelt szerepkörrel rendelkező felhasználók windowsos rendszergazdai vagy Linux-gyökérfelhasználói jogosultságokkal jelentkezhetnek be egy Azure-beli virtuális gépre.
- **Virtuálisgép-felhasználó bejelentkezés:** A szerepkörrel rendelkező felhasználók rendszeres felhasználói jogosultságokkal jelentkezhetnek be egy Azure-beli virtuális gépre.

> [!NOTE]
> Ahhoz, hogy egy felhasználó bejelentkezhet a virtuális gépssSH-on keresztül, hozzá kell rendelnie a *virtuális gép rendszergazdai bejelentkezési* vagy *a virtuálisgép felhasználói bejelentkezési* szerepkörét. A virtuális géphez rendelt *tulajdonosi* vagy *közreműködői* szerepkörökkel rendelkező Azure-felhasználók nem rendelkeznek automatikusan jogosultságokkal a virtuális gépbe való bejelentkezéshez SSH-n keresztül.

A következő példa az [az szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create) használatával rendeli hozzá a *virtuális gép rendszergazdai bejelentkezési* szerepkört a virtuális géphez az aktuális Azure-felhasználó számára. Az aktív Azure-fiók felhasználónevét az [az-fiók megjelenítése,](/cli/azure/account#az-account-show)és a *hatókör* van beállítva, hogy a virtuális gép egy előző lépésben létrehozott [virtuális gép.](/cli/azure/vm#az-vm-show) A hatókör erőforráscsoport vagy előfizetési szinten is hozzárendelhető, és a normál RBAC öröklési engedélyek érvényesek. További információ: [Szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/overview.md)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Ha az AAD-tartomány és a bejelentkezési felhasználónév tartománya nem egyezik, meg kell adnia a felhasználói fiók objektumazonosítóját a *--assignee-object-id*, nem csak a *--assignee*felhasználónevével. A felhasználói fiók objektumazonosítóját az [az ad felhasználói listával](/cli/azure/ad/user#az-ad-user-list)szerezheti be.

Ha többet szeretne tudni arról, hogy miként kezelheti az RBAC segítségével az Azure-előfizetési erőforrásokhoz való hozzáférést, olvassa el az [Azure CLI,](../../role-based-access-control/role-assignments-cli.md)az [Azure Portal](../../role-based-access-control/role-assignments-portal.md)vagy az [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)használatát.

Azt is beállíthatja, hogy az Azure AD többtényezős hitelesítést igényeljen egy adott felhasználószámára a Linux virtuális gépre való bejelentkezéshez. További információ: [Az Azure többtényezős hitelesítése a felhőben](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)című témakörben talál.

## <a name="log-in-to-the-linux-virtual-machine"></a>Bejelentkezés a Linux virtuális gépre

Először tekintse meg a virtuális gép nyilvános IP-címét [az az vm](/cli/azure/vm#az-vm-show)show-val:

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Jelentkezzen be az Azure Linux virtuális gépére az Azure AD-hitelesítő adataival. A `-l` paraméter lehetővé teszi, hogy adja meg a saját Azure AD-fiók címét. Cserélje le a példafiókot a sajátjára. A számlacímeket kisbetűsen kell megadni. Cserélje le a példa IP-címét az előző parancsból származó virtuális gép nyilvános IP-címére.

```console
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

A rendszer kéri, hogy jelentkezzen be az Azure AD-ba egy egyszeri használatú kóddal a. [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) Másolja és illessze be az egyszeri használatú kódot az eszköz bejelentkezési oldalára.

Amikor a rendszer kéri, adja meg az Azure AD bejelentkezési hitelesítő adatait a bejelentkezési lapon. 

A következő üzenet jelenik meg a webböngészőben, ha sikeresen hitelesített:`You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

Zárja be a böngészőablakot, térjen vissza az SSH parancssorba, és nyomja le az **Enter** billentyűt. 

Most már be van jelentkezve az Azure Linux virtuális gépa a szerepkör engedélyek hozzárendelt, például *a virtuális gép felhasználója* vagy *a virtuális gép rendszergazdája.* Ha a felhasználói fiók rendelkezik a *virtuálisgép-rendszergazdai bejelentkezési* szerepkör, használhatja `sudo` a root jogosultságokat igénylő parancsok futtatásához.

## <a name="sudo-and-aad-login"></a>Sudo és AAD bejelentkezés

Az első alkalommal, amikor fut sudo, akkor meg kell kérni, hogy hitelesítse a második alkalommal. Ha nem szeretné, hogy újra hitelesítse magát a sudo futtatásához, szerkesztheti a sudoers fájlt, `/etc/sudoers.d/aad_admins` és lecserélheti ezt a sort:

```bash
%aad_admins ALL=(ALL) ALL
```

Ezzel a sokkal:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Bejelentkezési problémák elhárítása

Néhány gyakori hiba, amikor megpróbálja az SSH-t az Azure AD-hitelesítő adatokkal, nem tartalmaz RBAC-szerepköröket, és ismétlődő utasításokat a bejelentkezéshez. A következő szakaszokban javíthatja ezeket a problémákat.

### <a name="access-denied-rbac-role-not-assigned"></a>Hozzáférés megtagadva: Az RBAC-szerepkör nincs hozzárendelve

Ha a következő hiba jelenik meg az SSH-parancsban, ellenőrizze, hogy konfigurált-e RBAC-házirendeket a virtuális géphez, amely a felhasználónak a *virtuálisgép-rendszergazda bejelentkezési* vagy *a virtuálisgép-felhasználó bejelentkezési* szerepkörét biztosítja:

```output
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>Folyamatos SSH bejelentkezési utasítások

Ha sikeresen teljesítette a hitelesítési lépést egy webböngészőben, a rendszer azonnal kérheti, hogy újra jelentkezzen be egy új kóddal. Ezt a hibát általában az SSH-parancssorban megadott bejelentkezési név és az Azure AD-be bejelentkezett fiók közötti eltérés okozza. A probléma kijavítása:

- Ellenőrizze, hogy az SSH-kérdésben megadott bejelentkezési név helyes-e. A bejelentkezési névben lévő elírás eltérést okozhat az SSH-parancssorban megadott bejelentkezési név és az Azure AD-be bejelentkezett fiók között. Például az azureuser *\@contoso.onmicrosoft.com*helyett *az azuresuer\@contoso.onmicrosoft.com* írta be.
- Ha több felhasználói fiókkal rendelkezik, győződjön meg arról, hogy nem ad meg egy másik felhasználói fiókot a böngésző ablakban, amikor bejelentkezik az Azure AD-be.
- A Linux egy kis- és nagybetűket megkülönböztető operációs rendszer. Különbség van aAzureuser@contoso.onmicrosoft.com' ésazureuser@contoso.onmicrosoft.coma ' között, ami eltérést okozhat. Győződjön meg arról, hogy az upn-t a megfelelő kis- és nagybetűkkel adja meg az SSH parancssorban.

### <a name="other-limitations"></a>Egyéb korlátozások

Azok a felhasználók, akik egymásba ágyazott csoportokon vagy szerepkör-hozzárendeléseken keresztül örökölik a hozzáférési jogokat, jelenleg nem támogatottak. A felhasználónak vagy csoportnak közvetlenül hozzá kell rendelnie a [szükséges szerepkör-hozzárendeléseket.](#configure-role-assignments-for-the-vm) Például a felügyeleti csoportok vagy beágyazott csoport szerepkör-hozzárendelések használata nem adja meg a megfelelő engedélyeket a felhasználó bejelentkezéséhez.

## <a name="preview-feedback"></a>Az előzetes verzióval kapcsolatos visszajelzés

Az előzetes verzióval kapcsolatos visszajelzések megosztása vagy a problémák jelentése az [Azure AD visszajelzési fórumán](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>További lépések

Az Azure Active Directoryról a [Mi az Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) című témakörben talál további információt.
