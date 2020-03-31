---
title: Bejelentkezés a Windows virtuális gépére az Azure-ban az Azure Active Directory használatával (előzetes verzió)
description: Az Azure AD bejelentkezés egy Windows-t futtató Azure virtuális gépbe
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88ae3c45126403161e35ec46e5ccc2666c3edb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050065"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Bejelentkezés a Windows virtuális gépére az Azure-ban az Azure Active Directory-hitelesítéshasználatával (előzetes verzió)

A szervezetek mostantól azure Active Directory (AD) hitelesítést használhatnak **a Windows Server 2019 Datacenter kiadást** vagy Windows **10 1809-es** és újabb rendszert futtató Azure virtuális gépeikhez. Az Azure AD használatával hitelesítheti a virtuális gépek en lehetővé teszi, hogy központilag vezérelje és kényszerítse a szabályzatokat. Az olyan eszközök, mint az Azure szerepköralapú hozzáférés-vezérlés (RBAC) és az Azure AD feltételes hozzáférése lehetővé teszik annak szabályozását, hogy ki férhet hozzá a virtuális gépekhez. Ez a cikk bemutatja, hogyan hozhat létre és konfigurálhat egy Windows Server 2019 virtuális gép az Azure AD-hitelesítés.

|     |
| --- |
| Az Azure AD bejelentkezés az Azure Windows virtuális gépek egy nyilvános előzetes funkció az Azure Active Directory. Az előzetes verziókról további információt a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz című](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) témakörben talál.|
|     |

Az Azure AD-hitelesítés használatával számos előnye van az Azure-beli Windows-virtuális gépekre való bejelentkezéshez, többek között:

- Használja ugyanazokat az összevont vagy felügyelt Azure AD-hitelesítő adatokat, amelyeket általában használ.
- A továbbiakban nem kell helyi rendszergazdai fiókokat kezelnie.
- Az Azure RBAC lehetővé teszi, hogy megadja a megfelelő hozzáférést a virtuális gépek igény alapján, és távolítsa el, ha már nincs rá szükség.
- A virtuális géphez való hozzáférés engedélyezése előtt az Azure AD feltételes hozzáférése további követelményeket kényszeríthet ki, például: 
   - Multi-Factor Authentication
   - Bejelentkezési kockázat ellenőrzése
- Automatizálhatja és skálázhatja az Azure Windows virtuális gépek Azure AD-s csatlakozását, amelyek a VDI-telepítések részét képezik.

> [!NOTE]
> Miután engedélyezte ezt a funkciót, az Azure-beli Windows-virtuális gépek az Azure AD-hez csatlakoznak. Nem csatlakozhat más tartományhoz, például a helyszíni AD-hez vagy az Azure AD DS-hez. Ha ehhez kell tennie, le kell választania a virtuális gép az Azure AD-bérlő a bővítmény eltávolításával.

## <a name="requirements"></a>Követelmények

### <a name="supported-azure-regions-and-windows-distributions"></a>Támogatott Azure-régiók és Windows-disztribúciók

A következő Windows-disztribúciók jelenleg támogatottak a szolgáltatás előzetes verzióiban:

- Windows Server 2019 Datacenter
- Windows 10 1809 és újabb

> [!IMPORTANT]
> Az Azure AD-hez csatlakozott virtuális gépekhez való távoli kapcsolat csak az Azure AD-hez csatlakozott Windows 10-es számítógépekről vagy a hibrid Azure AD-vel való csatlakozása a virtuális **gépkönyvtárhoz.** 

A következő Azure-régiók jelenleg támogatottak a funkció előzetes verzióiban:

- Az Azure összes globális régiója

> [!IMPORTANT]
> Az előzetes verziós funkció használatához csak egy támogatott Windows-disztribúciót telepítsen egy támogatott Azure-régióban. A szolgáltatás jelenleg nem támogatott az Azure Government vagy a szuverén felhők.

### <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

Az Azure-beli virtuális gépek Azure-beli virtuális gépei Azure-beli virtuális gépei számára az Azure-hitelesítés engedélyezéséhez biztosítania kell, hogy a virtuális gépek hálózati konfigurációja lehetővé tegye a kimenő hozzáférést a következő végpontokhoz a 443-as TCP-porton keresztül:

- https:\//enterpriseregistration.windows.net
- https:\//login.microsoftonline.com
- https:\//device.login.microsoftonline.com
- https:\//pas.windows.net

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Az Azure AD bejelentkezésének engedélyezése windowsos virtuális gépekhez az Azure-ban

Az Azure AD bejelentkezésaz Azure-beli virtuális gép az Azure-ban, először engedélyeznie kell az Azure AD bejelentkezési lehetőséget a Windows virtuális gép, majd konfigurálnia kell RBAC szerepkör-hozzárendelések a felhasználók számára, akik jogosultak a virtuális gépbe való bejelentkezésre.
Többféleképpen is engedélyezheti az Azure AD bejelentkezést a Windows virtuális géphez:

- Az Azure Portal felhasználói felületének használata Windows virtuális gép létrehozásakor
- Az Azure Cloud Shell-élmény használata Windows virtuális gép **vagy meglévő Windows virtuális gép** létrehozásakor

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Az Azure Portal használatával hozzon létre virtuális gép élményt az Azure AD bejelentkezésengedélyezéséhez

Engedélyezheti az Azure AD bejelentkezést A Windows Server 2019 Datacenter vagy Windows 10 1809 és újabb virtuálisgép-lemezképekhez. 

Windows Server 2019 Datacenter virtuális gép létrehozása az Azure AD-bejelentkezéssel az Azure-ban: 

1. Jelentkezzen be az [Azure Portalra,](https://portal.azure.com)amelynek hozzáférése van a virtuális gépek létrehozásához, és válassza a **+ Erőforrás létrehozása**lehetőséget.
1. Írja be a **Windows Server** kifejezést a Kereső a Piactér keresősávjában.
   1. Kattintson a **Windows Server** elemre, és válassza a **Windows Server 2019 Datacenter** (Szoftverséma kiválasztása) legördülő hely közül a Választható el.
   1. Kattintson a **Létrehozás** lehetőségre.
1. A "Kezelés" lapon engedélyezze az **AAD-hitelesítő adatokkal (előzetes verzióval) való bejelentkezés** t az Azure Active Directory szakaszban Ki és **Be**között.
1. Győződjön meg arról, hogy **a Rendszer hozzárendelt felügyelt identitása** az Identitás szakaszban **Be**beállításra van állítva. Ez a művelet automatikusan megtörténik, miután engedélyezte a bejelentkezést az Azure AD hitelesítő adatokkal.
1. A virtuális gépek létrehozásának többi élményét is átélheti. Az előzetes verzió során létre kell hoznia egy rendszergazdai felhasználónevet és jelszót a virtuális géphez.

![Bejelentkezés az Azure AD hitelesítő adatokkal hozzon létre egy virtuális gép](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Annak érdekében, hogy jelentkezzen be a virtuális gép az Azure AD hitelesítő adatokkal, először konfigurálnia kell szerepkör-hozzárendelések a virtuális gép az alábbi szakaszok egyikében leírtak szerint.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Az Azure Cloud Shell-élmény használata az Azure AD-bejelentkezés engedélyezéséhez

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések végrehajtására használhat. A gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak a Cloud Shellben a fiókjával történő használathoz. A Másolás gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz. A Cloud Shell többféleképpen is megnyitható:

Kattintson a Kipróbálás elemre egy kódblokk jobb felső sarkában.
Nyissa meg a Cloud Shellt a böngészőben.
Az [Azure Portal](https://portal.azure.com) jobb felső sarkában található menüben kattintson a Cloud Shell gombra.

Ha úgy dönt, hogy helyileg telepíti és használja a CLI-t, ez a cikk megköveteli, hogy az Azure CLI 2.0.31-es vagy újabb verzióját futassza. A verzió megkereséséhez futtassa a következő parancsot: az --version. Ha telepíteni vagy frissíteni kell, olvassa el az [Azure CLI telepítése](/cli/azure/install-azure-cli)című témakört.

1. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. 
1. Hozzon létre egy virtuális gép [az az vm létrehozása](/cli/azure/vm#az-vm-create) egy támogatott disztribúció egy támogatott régióban. 
1. Telepítse az Azure AD bejelentkezési virtuálisgép-bővítményt. 

A következő példa egy myVM nevű virtuális gép, amely win2019Datacenter, egy erőforrás csoport nevű erőforráscsoport, a southcentralus régióban. A következő példákban megadhat saját erőforráscsoport és virtuális gép nevét szükség szerint.

```AzureCLI
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --assign-identity \
    --admin-username azureuser \
    --admin-password yourpassword
```

> [!NOTE]
> Az Azure AD bejelentkezési virtuálisgép-bővítmény telepítése előtt engedélyeznie kell a rendszer hez rendelt felügyelt identitást a virtuális gépen.

A virtuális gép és a kapcsolódó erőforrások létrehozása csak néhány percet vesz igénybe.

Végül telepítse az Azure AD bejelentkezési virtuálisgép-bővítményt az Azure AD bejelentkezés engedélyezéséhez a Windows virtuális géphez. A virtuálisgép-bővítmények olyan kis alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure virtuális gépeken. Használja [az vm bővítménykészletet](/cli/azure/vm/extension#az-vm-extension-set) az AADLoginForWindows bővítmény telepítéséhez a myResourceGroup erőforráscsoport myResourceGroup erőforráscsoportjában myVM nevű virtuális gépre:

> [!NOTE]
> Az AADLoginForWindows bővítményt egy meglévő Windows Server 2019 vagy Windows 10 1809 és újabb virtuális gépre telepítheti az Azure AD-hitelesítéshez. Az AZ CLI egy példája az alábbiakban látható.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

A `provisioningState` `Succeeded` jelenik meg, ha a bővítmény telepítve van a virtuális gépre.

## <a name="configure-role-assignments-for-the-vm"></a>Szerepkör-hozzárendelések konfigurálása a virtuális géphez

Most, hogy létrehozta a virtuális gép, konfigurálnia kell az Azure RBAC-szabályzatot annak meghatározásához, hogy ki jelentkezhet be a virtuális gépbe. A virtuális gép bejelentkezésengedélyezéséhez két RBAC-szerepkör használható:

- **Virtuálisgép-rendszergazdai bejelentkezés:** A szerepkörrel rendelkező felhasználók rendszergazdai jogosultságokkal jelentkezhetnek be egy Azure-beli virtuális gépre.
- **Virtuálisgép-felhasználó bejelentkezés:** A szerepkörrel rendelkező felhasználók rendszeres felhasználói jogosultságokkal jelentkezhetnek be egy Azure-beli virtuális gépre.

> [!NOTE]
> Ahhoz, hogy egy felhasználó bejelentkezhet a virtuális géprdp-n keresztül, hozzá kell rendelnie a virtuális gép rendszergazdája bejelentkezési vagy a virtuálisgép felhasználói bejelentkezési szerepkörét. A virtuális géphez rendelt tulajdonosi vagy közreműködői szerepkörökkel rendelkező Azure-felhasználók nem rendelkeznek automatikusan a virtuális gépbe való bejelentkezéshez az RDP-n keresztül. Ez biztosítja a virtuális gépeket vezérlő személyek és a virtuális gépek eléréséhez hozzáférő személyek készletének auditált elkülönítését.

A virtuális gép szerepkör-hozzárendeléseit többféleképpen is konfigurálhatja:

- Az Azure AD Portal felhasználói felületének használata
- Az Azure Cloud Shell-élmény használata

### <a name="using-azure-ad-portal-experience"></a>Az Azure AD Portal használata

Szerepkör-hozzárendelések konfigurálása az Azure AD-kompatibilis Windows Server 2019 Datacenter virtuális gépekhez:

1. Navigálás a virtuális gép adott áttekintő lapjára
1. Válassza a **hozzáférés-vezérlés (IAM)** elemet a menüpontok között
1. Válassza **a Hozzáadás**lehetőséget, **a Szerepkör-hozzárendelés hozzáadása lehetőséget** a Szerepkör-hozzárendelés hozzáadása ablaktábla megnyitásához.
1. A **Szerepkör** legördülő listában válasszon ki egy szerepkört, például **a Virtuálisgép-rendszergazda bejelentkezést** vagy a **Virtuálisgép felhasználói bejelentkezését.**
1. A **Kijelölés** mezőben jelöljön ki egy felhasználót, csoportot, egyszerű szolgáltatást vagy felügyelt identitást. Ha a listában nem látja a rendszerbiztonsági tagot, írhat a **Kiválasztás** mezőbe megjelenítendő nevek, e-mail-címek és objektumazonosítók a címtárban történő kereséséhez.
1. A szerepkör hozzárendeléséhez válassza a **Mentés**lehetőséget.

Néhány pillanat múlva a rendszerbiztonsági tag a kijelölt hatókörhöz rendeli a szerepkört.

![Szerepkörök hozzárendelése a virtuális géphez hozzáférő felhasználókhoz](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Az Azure Cloud Shell-élmény használata

A következő példa az [az szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create) használatával rendeli hozzá a virtuális gép rendszergazdai bejelentkezési szerepkört a virtuális géphez az aktuális Azure-felhasználó számára. Az aktív Azure-fiók felhasználónevét az [az-fiók megjelenítése,](/cli/azure/account#az-account-show)és a hatókör van beállítva, hogy a virtuális gép egy előző lépésben létrehozott [virtuális gép.](/cli/azure/vm#az-vm-show) A hatókör erőforráscsoport vagy előfizetési szinten is hozzárendelhető, és a normál RBAC öröklési engedélyek érvényesek. További információt a [Szerepköralapú hozzáférés-vezérlés című témakörben talál.](../../virtual-machines/linux/login-using-aad.md)

```   AzureCLI
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Ha az AAD-tartomány és a bejelentkezési felhasználónév tartománya nem egyezik, `--assignee-object-id`akkor a felhasználói `--assignee`fiók objektumazonosítóját a , nem csak a felhasználónevét kell megadnia. A felhasználói fiók objektumazonosítóját az [az ad felhasználói listával](/cli/azure/ad/user#az-ad-user-list)szerezheti be.

Ha többet szeretne tudni arról, hogy miként kezelheti az RBAC segítségével az Azure-előfizetési erőforrásokhoz való hozzáférést, olvassa el az alábbi cikkeket:

- [Azure-erőforrásokhoz való hozzáférés kezelése az RBAC és az Azure CLI használatával](/azure/role-based-access-control/role-assignments-cli)
- [Azure-erőforrásokhoz való hozzáférés kezelése az RBAC és az Azure Portal használatával](/azure/role-based-access-control/role-assignments-portal)
- [Az RBAC és az Azure PowerShell használatával kezelheti az Azure-erőforrásokhoz való hozzáférést.](/azure/role-based-access-control/role-assignments-powershell)

## <a name="using-conditional-access"></a>Feltételes hozzáférés használata

Feltételes hozzáférési szabályzatok, például a többtényezős hitelesítés vagy a felhasználói bejelentkezési kockázat ellenőrzése kényszerítése előtt hozzáférést biztosít a Windows virtuális gépek az Azure-ban, amelyek engedélyezve vannak az Azure AD bejelentkezéssel. Feltételes hozzáférési szabályzat alkalmazásához ki kell választania az "Azure Windows VM Bejelentkezés" alkalmazást a felhőalkalmazásokból vagy műveletek hozzárendelési beállításából, majd feltételként kell használnia a bejelentkezési kockázatot, és/vagy többtényezős hitelesítést kell igényelnie a hozzáférési hozzáférés-vezérléshez. 

> [!NOTE]
> Ha a "Többtényezős hitelesítés megkövetelése" funkció val a "Többtényezős hitelesítés megkövetelése" a hozzáférési szabályrendszer a hozzáférést kérő az "Azure Windows VM Sign-In" alkalmazást, majd meg kell adnia többtényezős hitelesítési jogcím részeként az ügyfél, amely kezdeményezi az RDP-munkamenet a cél Windows Virtuális gép az Azure-ban. Ezt csak úgy érheti el egy Windows 10-es ügyfélen, ha a Windows Hello For Business PIN-kódját vagy biometrikus hitelesítést használ az RDP-ügyféllel. A biometrikus hitelesítés támogatása hozzá lett adva az RDP-ügyfélhez a Windows 10 1809-es verziójában. A Windows Hello for Business hitelesítést használó távoli asztal csak olyan központi telepítések nél érhető el, amelyek tanúsítványmegbízhatósági modellt használnak, és jelenleg nem érhetők el a kulcsmegbízhatósági modellhez.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Bejelentkezés Azure AD-hitelesítő adatokkal Windows virtuális gépbe

> [!IMPORTANT]
> Az Azure AD-hez csatlakozott virtuális gépekhez való távoli kapcsolat csak az Azure AD-hez csatlakozott Windows 10-es számítógépekről vagy a hibrid Azure AD-vel való csatlakozása a virtuális **gépkönyvtárhoz.** Emellett az RDP azure AD-hitelesítő adatok használatával, a felhasználónak a két RBAC-szerepkör, a virtuálisgép-rendszergazdai bejelentkezés vagy a virtuálisgép-felhasználó bejelentkezés egyikéhez kell tartoznia. Jelenleg az Azure Bastion nem használható az AADLoginForWindows-bővítménysel az Azure Active Directory-hitelesítéshasználatával történő bejelentkezéshez. Csak a közvetlen RDP támogatott.

Bejelentkezés windows Server 2019 virtuális gépére az Azure AD használatával: 

1. Keresse meg az Azure AD-bejelentkezéssel engedélyezett virtuális gép áttekintő lapját.
1. Válassza a **Csatlakozás** lehetőséget a Csatlakozás a virtuális géphez panel megnyitásához.
1. Válassza **az RDP-fájl letöltése lehetőséget.**
1. A Távoli asztali kapcsolat ügyfél elindításához válassza a **Megnyitás** gombot.
1. A Windows bejelentkezési párbeszédpanel ének elindításához válassza a **Csatlakozás** lehetőséget.
1. Bejelentkezés az Azure AD-hitelesítő adatokkal.

Most már be van jelentkezve a Windows Server 2019 Azure virtuális gépa a hozzárendelt szerepkör-engedélyekkel, például a virtuális gép felhasználója vagy a virtuális gép rendszergazdája. 

> [!NOTE]
> Mentheti a . RDP-fájl helyileg a számítógépen, hogy indítsa el a jövőbeli távoli asztali kapcsolatoka a virtuális gép, ahelyett, hogy navigálni a virtuális gép áttekintése lapot az Azure Portalon, és használja a csatlakozási lehetőséget.

## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="troubleshoot-deployment-issues"></a>Üzembe helyezési problémák elhárítása

Az AADLoginForWindows bővítmény sikeresen kell telepíteni ahhoz, hogy a virtuális gép az Azure AD csatlakozási folyamat befejezéséhez. Hajtsa végre a következő lépéseket, ha a virtuálisgép-bővítmény nem telepíthető megfelelően.

1. RDP a virtuális gép a helyi rendszergazdai fiókot, és vizsgálja meg a CommandExecuti'n.log alatt  
   
   C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0. 

   > [!NOTE]
   > Ha a bővítmény a kezdeti hiba után újraindul, a központi telepítési hibával rendelkező napló CommandExecution_YYYYMMDDHHMMSSSSS.log néven kerül mentésre. "
1. Nyisson meg egy parancssort a virtuális gépen, és ellenőrizze ezeket a lekérdezéseket az Azure-állomáson futó példánymetaadat-szolgáltatás (IMDS) végpontjával:

   | Futtatandó parancs | Várt kimenet |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Az Azure virtuális gépre vonatkozó információk javítása |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | Az Azure-előfizetéshez társított érvényes bérlői azonosító |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Az Azure Active Directory által a virtuális géphez rendelt felügyelt identitáshoz kiadott érvényes hozzáférési jogkivonat |

   > [!NOTE]
   > A hozzáférési jogkivonat dekódolható [http://calebb.net/](http://calebb.net/)egy olyan eszközzel, mint a. Ellenőrizze, hogy a hozzáférési jogkivonat "appid" megegyezik-e a virtuális géphez rendelt felügyelt identitással.

1. Győződjön meg arról, hogy a szükséges végpontok elérhetők a virtuális gépről a parancssorhasználatával:
   
   - göndör https:\//login.microsoftonline.com/ -D –
   - göndör https:\/`<TenantID>`/login.microsoftonline.com/ / -D -

   > [!NOTE]
   > Cserélje `<TenantID>` le az Azure AD-bérlői azonosítót, amely az Azure-előfizetéshez van társítva.

   - göndör https:\//enterpriseregistration.windows.net/ -D -
   - göndör https:\//device.login.microsoftonline.com/ -D -
   - göndör https:\//pas.windows.net/ -D -

1. Az eszközállapot a futtatásával `dsregcmd /status`tekinthető meg. A cél az, hogy `AzureAdJoined : YES`az Eszközállapot a .

   > [!NOTE]
   > Az Azure AD-csatlakozási tevékenység az Eseménynaplóban, a Felhasználói eszköz regisztrációja\Rendszergazdai napló alatt kerül rögzítésre.

Ha az AADLoginForWindows bővítmény bizonyos hibakóddal sikertelen, a következő lépéseket hajthatja végre:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>1. probléma: Az AADLoginForWindows bővítmény telepítése nem sikerül a "1007" terminálhibakóddal és a kilépési kóddal: -2145648574.

Ez a kilépési kód fordítja DSREG_E_MSI_TENANTID_UNAVAILABLE mert a bővítmény nem tudja lekérdezni az Azure AD-bérlő adatait.

1. Ellenőrizze, hogy az Azure virtuális gép lekérheti a tenantID a példány metaadat-szolgáltatás.

   - RdP a virtuális gép helyi rendszergazdaként, és ellenőrizze, hogy a végpont érvényes bérlői azonosítót ad vissza a parancs futtatásával egy rendszergazdajogú parancssorból a virtuális gépen:
      
      - curl -H metaadatok:igazhttp://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. A virtuális gép rendszergazdája megpróbálja telepíteni az AADLoginForWindows bővítményt, de a felügyelt identitáshoz rendelt rendszer nem engedélyezte először a virtuális gép. Keresse meg a virtuális gép identitáspaneljét. A Rendszer hozzárendelt lapján ellenőrizze, hogy az Állapot be van-e kapcsolva.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>2. probléma: Az AADLoginForWindows bővítmény telepítése nem sikerül az Exit kóddal: -2145648607

Ez a kilépési kód DSREG_AUTOJOIN_DISC_FAILED, mert a `https://enterpriseregistration.windows.net` bővítmény nem éri el a végpontot.

1. Ellenőrizze, hogy a szükséges végpontok elérhetők-e a virtuális gépről a parancssorhasználatával:

   - göndör https:\//login.microsoftonline.com/ -D –
   - göndör https:\/`<TenantID>`/login.microsoftonline.com/ / -D -
   
   > [!NOTE]
   > Cserélje `<TenantID>` le az Azure AD-bérlői azonosítót, amely az Azure-előfizetéshez van társítva. Ha meg kell találnia a bérlőazonosítót, a fiók neve fölé viheti a címtár/ bérlőazonosító lekért, vagy válassza az Azure Active Directory > properties > Directory-azonosítót az Azure Portalon.

   - göndör https:\//enterpriseregistration.windows.net/ -D -
   - göndör https:\//device.login.microsoftonline.com/ -D -
   - göndör https:\//pas.windows.net/ -D -

1. Ha a parancsok bármelyike sikertelen a `<URL>`"Nem oldható fel az állomás" paranccsal, próbálja meg futtatni ezt a parancsot a virtuális gép által használt DNS-kiszolgáló meghatározásához.
   
   `nslookup <URL>`

   > [!NOTE] 
   > Cserélje `<URL>` le a végpontok által használt teljesen minősített tartománynevekre, például "login.microsoftonline.com".

1. Ezután nézze meg, hogy egy nyilvános DNS-kiszolgáló megadása lehetővé teszi-e a parancs sikeressítését:

   `nslookup <URL> 208.67.222.222`

1. Ha szükséges, módosítsa a DNS-kiszolgáló, amely hozzá van rendelve a hálózati biztonsági csoport, amely hez az Azure virtuális gép tartozik.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>3. probléma: Az AADLoginForWindows bővítmény telepítése nem sikerül az Exit kóddal: 51

Exit kód 51 fordítja a "Ez a bővítmény nem támogatott a virtuális gép operációs rendszer".

A Nyilvános előzetes verzióban az AADLoginForWindows bővítmény csak Windows Server 2019 vagy Windows 10 (Build 1809 vagy újabb) rendszerre telepíthető. Győződjön meg arról, hogy a Windows verziója támogatott. Ha a Windows buildje nem támogatott, távolítsa el a virtuálisgép-bővítményt.

### <a name="troubleshoot-sign-in-issues"></a>Bejelentkezési problémák elhárítása

Néhány gyakori hiba, amikor megpróbálja RDP Az Azure AD hitelesítő adatok kal nem RBAC szerepkörök hozzárendelt, jogosulatlan ügyfél vagy 2FA bejelentkezési módszer szükséges. A problémák kijavításához használja az alábbi információkat.

Az eszköz és az SSO-állapot a futtatásával `dsregcmd /status`tekinthető meg. A cél az, hogy `AzureAdJoined : YES` az `SSO State` Eszközállapot a . `AzureAdPrt : YES`

Az Azure AD-fiókok használatával végzett RDP-bejelentkezés t az AAD\Operational event logs az Eseménynaplóban is rögzíti.

#### <a name="rbac-role-not-assigned"></a>RBAC szerepkör nincs hozzárendelve

Ha a következő hibaüzenet jelenik meg, amikor távoli asztali kapcsolatot kezdeményez a virtuális géppel: 

- A fiók úgy van beállítva, hogy megakadályozza az eszköz használatát. További információért forduljon a rendszergazdához

![A fiók úgy van beállítva, hogy megakadályozza az eszköz használatát.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Ellenőrizze, hogy [konfigurálta-e az RBAC-házirendeket](../../virtual-machines/linux/login-using-aad.md) a virtuális géphez, amely a felhasználónak a virtuálisgép-rendszergazda bejelentkezési vagy a virtuálisgép-felhasználó bejelentkezési szerepkört biztosítja:
 
#### <a name="unauthorized-client"></a>Nem engedélyezett ügyfél

Ha a következő hibaüzenet jelenik meg, amikor távoli asztali kapcsolatot kezdeményez a virtuális géppel: 

- A hitelesítő adatok nem működtek

![A hitelesítő adatok nem működtek](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Ellenőrizze, hogy a távoli asztali kapcsolat elindításához használt Windows 10-es pc-e vagy az Azure AD-hez csatlakozott, vagy a hibrid Azure AD-t ugyanahhoz az Azure AD-könyvtárhoz csatlakoztatva, amelyhez a virtuális gép csatlakozik. Az eszközidentitásról a Mi az eszközidentitás című témakörben olvashat [bővebben.](/azure/active-directory/devices/overview)

> [!NOTE]
> A Windows 10 20H1 támogatást nyújt az Azure AD regisztrált számítógépéhez, hogy távoli asztali kapcsolatot kezdeményezzen a virtuális géphez. Csatlakozzon a Windows Insider Programhoz, és próbálja ki ezt, és fedezze fel a Windows 10 új funkcióit.

Ellenőrizze azt is, hogy az AADLoginForWindows bővítmény nem lett-e eltávolítva az Azure AD-csatlakozás befejezése után.
 
#### <a name="mfa-sign-in-method-required"></a>MFA bejelentkezési módszer szükséges

Ha a következő hibaüzenet jelenik meg, amikor távoli asztali kapcsolatot kezdeményez a virtuális géppel: 

- A használni kívánt bejelentkezési módszer nem engedélyezett. Próbálkozzon egy másik bejelentkezési módszerrel, vagy forduljon a rendszergazdához.

![A használni kívánt bejelentkezési módszer nem engedélyezett.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Ha az erőforrás elérése előtt többtényezős hitelesítést (MFA) igénylő feltételes hozzáférési házirendet konfigurált, akkor meg kell győződnie arról, hogy a távoli asztali kapcsolatot a virtuális géphez létesítő Windows 10-es számítógép erős hitelesítési módszer, például a Windows Hello. Ha nem használ erős hitelesítési módszert a távoli asztali kapcsolathoz, az előző hibaüzenet jelenik meg.

Ha még nem telepítette a Windows Hello for Business alkalmazást, és ha ez egyelőre nem lehetséges, kizárhatja az MFA-követelményt a feltételes hozzáférési szabályzat konfigurálásával, amely kizárja az "Azure Windows VM Bejelentkezés" alkalmazást az MFA-t igénylő felhőalkalmazások listájából. Ha többet szeretne tudni a Windows Hello vállalati verzióról, olvassa el [a Windows Hello vállalati verzió – áttekintés című témakört.](/windows/security/identity-protection/hello-for-business/hello-identity-verification)

> [!NOTE]
> A Windows Hello For Business PIN-hitelesítését az RDP-vel a Windows 10 számos verzióban támogatja, azonban az RDP-vel való biometrikus hitelesítés támogatása a Windows 10 1809-es verziójában is megjelent. A Windows Hello for Business hitelesítési szolgáltatásának használata az RDP során csak olyan központi telepítéseknél érhető el, amelyek tanúsítványmegbízhatósági modellt használnak, és jelenleg nem érhetők el a kulcsmegbízhatósági modellhez.
 
## <a name="preview-feedback"></a>Az előzetes verzióval kapcsolatos visszajelzés

Az [Azure AD visszajelzési fórumán](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)megoszthatja az előzetes verzióval kapcsolatos visszajelzését, vagy jelentheti a problémákat.

## <a name="next-steps"></a>További lépések

Az Azure Active Directoryról a [Mi az Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) című témakörben talál további információt.
