---
title: Rendelkezésre állási csoportok konfigurálása az Azure-beli virtuális gépeken futó SQL Server RHEL-Linux Virtual Machines | Microsoft Docs
description: Ismerkedjen meg a magas rendelkezésre állás beállításával egy RHEL-fürt környezetében, és állítsa be a STONITH
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 01/27/2020
ms.openlocfilehash: 3f5f51c944cab84c00e29b2f72433a726ed70d33
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024347"
---
# <a name="tutorial-configure-availability-groups-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Oktatóanyag: rendelkezésre állási csoportok konfigurálása az Azure-beli virtuális gépek RHEL SQL Server 

> [!NOTE]
> A bemutatott oktatóanyag **nyilvános előzetes**verzióban érhető el. 
>
> Ebben az oktatóanyagban a SQL Server 2017-es RHEL 7,6-et használjuk, de a SQL Server 2019 a RHEL 7 vagy a RHEL 8 használatával konfigurálható. A rendelkezésre állási csoport erőforrásainak konfigurálására vonatkozó parancsok megváltoztak a RHEL 8-ban, és érdemes megtekinteni a [rendelkezésre állási csoport erőforrásának](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) és a RHEL 8 erőforrásoknak a megfelelő parancsokkal kapcsolatos további információkért.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> - Új erőforráscsoport, rendelkezésre állási csoport és Azure Linux Virtual Machines (VM) létrehozása
> - Magas rendelkezésre állás engedélyezése (HA)
> - Pacemaker-fürt létrehozása
> - Vívó ügynök konfigurálása STONITH-eszköz létrehozásával
> - SQL Server-és MSSQL-eszközök telepítése a RHEL-on
> - SQL Server always on rendelkezésre állási csoport konfigurálása
> - A rendelkezésre állási csoport (AG) erőforrásainak konfigurálása a pacemaker-fürtben
> - Feladatátvétel és a kerítés ügynök tesztelése

Ez az oktatóanyag az Azure parancssori felületét (CLI) fogja használni az Azure-beli erőforrások üzembe helyezéséhez.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, az oktatóanyaghoz az Azure CLI 2.0.30 vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Ha egynél több előfizetéssel rendelkezik, [állítsa be azt az előfizetést](/cli/azure/manage-azure-subscriptions-azure-cli) , amelyre telepíteni kívánja ezeket az erőforrásokat.

Az alábbi parancs használatával hozzon létre egy erőforráscsoportot `<resourceGroupName>` egy régióban. Cserélje le a `<resourceGroupName>`t a választott névre. Ezt az oktatóanyagot `East US 2` használjuk. További információkért tekintse meg [a következő rövid](../quick-create-cli.md)útmutatót.

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus2
```

## <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása

A következő lépés egy rendelkezésre állási csoport létrehozása. Futtassa a következő parancsot a Azure Cloud Shellban, és cserélje le a `<resourceGroupName>`t az erőforráscsoport nevére. Válassza ki a `<availabilitySetName>`nevét.

```azurecli-interactive
az vm availability-set create \
    --resource-group <resourceGroupName> \
    --name <availabilitySetName> \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

A parancs befejeződése után a következő eredményeket kell megkapnia:

```output
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/availabilitySets/<availabilitySetName>",
  "location": "eastus2",
  "name": "<availabilitySetName>",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 2,
  "proximityPlacementGroup": null,
  "resourceGroup": "<resourceGroupName>",
  "sku": {
    "capacity": null,
    "name": "Aligned",
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```

## <a name="create-rhel-vms-inside-the-availability-set"></a>RHEL virtuális gépek létrehozása a rendelkezésre állási csoporton belül

> [!WARNING]
> Ha az utólagos elszámolású (TB) RHEL-rendszerképet választja, és magas rendelkezésre állást (HA) konfigurál, előfordulhat, hogy regisztrálnia kell az előfizetését. Ez azt eredményezheti, hogy kétszer kell fizetnie az előfizetéshez, mivel a virtuális gép Microsoft Azure RHEL-előfizetését és a Red Hat-előfizetést terheli. További információ: https://access.redhat.com/solutions/2458541.
>
> Az Azure-beli virtuális gép létrehozásakor használjon egy RHEL-képet. A RHEL-es lemezképként kínált rendszerképek is TB, HA az HA-tárház előre engedélyezve van.

1. Szerezze be azoknak a virtuálisgép-lemezképeknek a listáját, amelyek a RHEL-mel rendelkeznek:

    ```azurecli-interactive
    az vm image list --all --offer "RHEL-HA"
    ```

    A következő eredményeknek kell megjelennie:

    ```output
    [
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.4",
              "urn": "RedHat:RHEL-HA:7.4:7.4.2019062021",
              "version": "7.4.2019062021"
            },
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.5",
              "urn": "RedHat:RHEL-HA:7.5:7.5.2019062021",
              "version": "7.5.2019062021"
            },
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.6",
              "urn": "RedHat:RHEL-HA:7.6:7.6.2019062019",
              "version": "7.6.2019062019"
            }
    ]
    ```

    Ebben az oktatóanyagban a `RedHat:RHEL-HA:7.6:7.6.2019062019`rendszerképet választjuk.

    > [!IMPORTANT]
    > A rendelkezésre állási csoport beállításához a gépek nevének 15 karakternél rövidebbnek kell lennie. A Felhasználónév nem tartalmazhat nagybetűket, és a jelszónak 12 karakternél hosszabbnak kell lennie.

1. 3 virtuális gépet szeretnénk létrehozni a rendelkezésre állási csoporton belül. Cserélje le az alábbi parancsot az alábbi parancsra:

    - `<resourceGroupName>`
    - `<VM-basename>`
    - `<availabilitySetName>`
    - `<VM-Size>` – példa: "Standard_D16_v3"
    - `<username>`
    - `<adminPassword>`

    ```azurecli-interactive
    for i in `seq 1 3`; do
           az vm create \
             --resource-group <resourceGroupName> \
             --name <VM-basename>$i \
             --availability-set <availabilitySetName> \
             --size "<VM-Size>"  \
             --image "RedHat:RHEL-HA:7.6:7.6.2019062019" \
             --admin-username "<username>" \
             --admin-password "<adminPassword>" \
             --authentication-type all \
             --generate-ssh-keys
    done
    ```

A fenti parancs létrehozza a virtuális gépeket, és létrehoz egy alapértelmezett VNet a virtuális gépek számára. További információ a különböző konfigurációkról: az [VM Create](https://docs.microsoft.com/cli/azure/vm) article.

A következőhöz hasonló eredményt kell kapnia, ha a parancs minden virtuális géphez befejeződik:

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<VM1>",
  "location": "eastus2",
  "macAddress": "<Some MAC address>",
  "powerState": "VM running",
  "privateIpAddress": "<IP1>",
  "publicIpAddress": "",
  "resourceGroup": "<resourceGroupName>",
  "zones": ""
}
```

> [!IMPORTANT]
> A fenti paranccsal létrehozott alapértelmezett lemezkép alapértelmezés szerint egy 32GB operációsrendszer-lemezt hoz létre. Előfordulhat, hogy az alapértelmezett telepítéssel kifogyott a terület. A fenti `az vm create` parancshoz hozzáadva a következő paramétert használhatja egy operációsrendszer-lemez 128GB való létrehozásához példaként: `--os-disk-size-gb 128`.
>
> Ezután [konfigurálhatja a logikai kötet-kezelőt (LVM)](../../../virtual-machines/linux/configure-lvm.md) , ha ki kell bontania a megfelelő mappák köteteit a telepítéshez.

### <a name="test-connection-to-the-created-vms"></a>A létrehozott virtuális gépekhez való kapcsolódás tesztelése

Kapcsolódjon a VM1 vagy a többi virtuális géphez a következő parancs használatával Azure Cloud Shellban. Ha nem találja a virtuális gépek IP-címeit, kövesse [Azure Cloud Shell](../../../cloud-shell/quickstart.md#ssh-into-your-linux-vm).

```azurecli-interactive
ssh <username>@publicipaddress
```

Ha a kapcsolatok sikeresek, a következő kimenetnek kell megjelennie, amely a Linux-terminált jelképezi:

```output
[<username>@<VM1> ~]$
```

Az SSH-munkamenet elhagyása `exit` írja be a következőt:.

## <a name="enable-high-availability"></a>Magas rendelkezésre állás engedélyezése

> [!IMPORTANT]
> Az oktatóanyag ezen részének végrehajtásához rendelkeznie kell egy RHEL-előfizetéssel és a magas rendelkezésre állási bővítménnyel. Ha az előző szakaszban javasolt rendszerképet használ, nem kell regisztrálnia egy másik előfizetést.
 
Kapcsolódjon az egyes virtuálisgép-csomópontokhoz, és az alábbi útmutatást követve engedélyezze a HA lehetőséget. További információ: [magas rendelkezésre állású előfizetés engedélyezése a RHEL](/sql/linux/sql-server-linux-availability-group-cluster-rhel#enable-the-high-availability-subscription-for-rhel).

> [!TIP]
> Egyszerűbb, ha egyszerre nyit meg egy SSH-munkamenetet az egyes virtuális gépekhez, mivel ugyanazokat a parancsokat kell futtatni a cikk minden egyes virtuális gépén.
>
> Ha több `sudo` parancsot másol és illeszt be, és a rendszer jelszót kér, a további parancsok nem fognak futni. Az egyes parancsokat külön futtassa.


1. Futtassa a következő parancsokat minden virtuális gépen a pacemaker tűzfal portjainak megnyitásához:

    ```bash
    sudo firewall-cmd --permanent --add-service=high-availability
    sudo firewall-cmd --reload
    ```

1. A következő parancsokkal frissítheti és telepítheti a pacemaker-csomagokat az összes csomóponton:

    > [!NOTE]
    > az **nmap** ennek a parancsnak a részeként a hálózatban elérhető IP-címek keresésére szolgáló eszközként van telepítve. Nem kell telepítenie az **nmap**-t, de ez az oktatóanyag későbbi részében is hasznos lehet.

    ```bash
    sudo yum update -y
    sudo yum install -y pacemaker pcs fence-agents-all resource-agents fence-agents-azure-arm nmap
    sudo reboot
    ```

1. Állítsa be a pacemaker-csomagok telepítésekor létrehozott alapértelmezett felhasználó jelszavát. Ugyanazt a jelszót használja az összes csomóponton.

    ```bash
    sudo passwd hacluster
    ```

1. A következő parancs használatával nyissa meg a Hosts fájlt, és állítsa be az állomásnév feloldását. További információ: [configure AG](/sql/linux/sql-server-linux-availability-group-configure-ha#prerequisites) a Hosts fájl konfigurálásához.

    ```
    sudo vi /etc/hosts
    ```

    A **VI** szerkesztőben írja be a `i` szöveget, és egy üres sorban adja hozzá a megfelelő virtuális gép **magánhálózati IP-címét** . Ezután adja hozzá a virtuális gép nevét az IP-cím melletti terület után. Minden sornak külön bejegyzéssel kell rendelkeznie.

    ```output
    <IP1> <VM1>
    <IP2> <VM2>
    <IP3> <VM3>
    ```

    > [!IMPORTANT]
    > Javasoljuk, hogy a fenti **magánhálózati IP-** címet használja. Ha ebben a konfigurációban a nyilvános IP-címet használja, a telepítő sikertelen lesz, és nem javasoljuk, hogy a virtuális gépet külső hálózatokra tegye közzé.

    A **VI** -szerkesztőből való kilépéshez először nyomja meg az **ESC** billentyűt, majd írja be a `:wq` parancsot a fájl írásához és a kilépéshez.

## <a name="create-the-pacemaker-cluster"></a>A pacemaker-fürt létrehozása

Ebben a szakaszban engedélyezjük és elindítjuk a pcsd szolgáltatást, majd konfigurálni fogjuk a fürtöt. SQL Server on Linux esetén a fürt erőforrásai nem jönnek létre automatikusan. A pacemaker-erőforrásokat manuálisan kell engedélyeznie és létrehozni. További információkért lásd a [feladatátvevő fürt RHEL-példányának konfigurálásáról](/sql/linux/sql-server-linux-shared-disk-cluster-red-hat-7-configure#install-and-configure-pacemaker-on-each-cluster-node) szóló cikket.

### <a name="enable-and-start-pcsd-service-and-pacemaker"></a>A pcsd szolgáltatás és a pacemaker engedélyezése és elindítása

1. Futtassa a parancsokat az összes csomóponton. Ezek a parancsok lehetővé teszik, hogy a csomópontok újraindítást követően újra csatlakozzanak a fürthöz.

    ```bash
    sudo systemctl enable pcsd
    sudo systemctl start pcsd
    sudo systemctl enable pacemaker
    ``` 

1. Távolítsa el az összes csomópontról a meglévő fürtkonfiguráció-konfigurációt. Futtassa az alábbi parancsot:

    ```bash
    sudo pcs cluster destroy 
    sudo systemctl enable pacemaker 
    ```

1. Az elsődleges csomóponton futtassa a következő parancsokat a fürt beállításához.

    - Amikor az `pcs cluster auth` parancsot futtatja a fürtcsomópontok hitelesítéséhez, a rendszer kérni fogja a jelszót. Adja meg a korábban létrehozott **hacluster** -felhasználó jelszavát.

    ```bash
    sudo pcs cluster auth <VM1> <VM2> <VM3> -u hacluster
    sudo pcs cluster setup --name az-hacluster <VM1> <VM2> <VM3> --token 30000
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

1. A következő parancs futtatásával győződjön meg arról, hogy az összes csomópont online állapotban van.

    ```bash
    sudo pcs status
    ```

    Ha az összes csomópont online állapotban van, a következőhöz hasonló kimenet jelenik meg:

    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Stack: corosync
    Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    Last updated: Fri Aug 23 18:27:57 2019
    Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
     
    3 nodes configured
    0 resources configured
     
    Online: [ <VM1> <VM2> <VM3> ]
     
    No resources
     
     
    Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    ```

1. Állítsa be a várt szavazatokat az élő fürtben 3 értékre. Ez a parancs csak az élő fürtöt érinti, és nem módosítja a konfigurációs fájlokat.

    Az összes csomóponton állítsa be a várt szavazatokat a következő paranccsal:

    ```bash
    sudo pcs quorum expected-votes 3
    ```

## <a name="configure-the-fencing-agent"></a>A kerítés ügynökének konfigurálása

A STONITH-eszközök kerítési ügynököt biztosítanak. Ebben az oktatóanyagban az alábbi utasításokat módosítjuk. További információ: STONITH- [eszköz létrehozása](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#create-stonith-device).
 
[Ellenőrizze, hogy az Azure kerítés-ügynök verziója frissült-](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#cluster-installation)e. Használja az alábbi parancsot:

```bash
sudo yum info fence-agents-azure-arm
```

Az alábbi példához hasonló kimenetnek kell megjelennie.

```output
Loaded plugins: langpacks, product-id, search-disabled-repos, subscription-manager
Installed Packages
Name        : fence-agents-azure-arm
Arch        : x86_64
Version     : 4.2.1
Release     : 11.el7_6.8
Size        : 28 k
Repo        : installed
From repo   : rhel-ha-for-rhel-7-server-eus-rhui-rpms
Summary     : Fence agent for Azure Resource Manager
URL         : https://github.com/ClusterLabs/fence-agents
License     : GPLv2+ and LGPLv2+
Description : The fence-agents-azure-arm package contains a fence agent for Azure instances.
```

### <a name="register-a-new-application-in-azure-active-directory"></a>Új alkalmazás regisztrálása Azure Active Directory
 
 1. Nyissa meg a következőt: https://portal.azure.com
 2. Nyissa meg a [Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)panelt. Válassza a tulajdonságok lehetőséget, és jegyezze fel a címtár-azonosítót. Ez a `tenant ID`
 3. Kattintson [ **Alkalmazásregisztrációk**](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
 4. Kattintson az **új regisztráció** elemre.
 5. Adjon meg egy **nevet** , például `<resourceGroupName>-app`, válassza a **csak a szervezeti címtárban lévő fiókok** elemet.
 6. Válassza az alkalmazás típusa **web**lehetőséget, írja be a bejelentkezési URL-címet (például http://localhost), majd kattintson a Hozzáadás gombra. A bejelentkezési URL-cím nincs használatban, és bármely érvényes URL-cím lehet
 7. Válassza a **tanúsítványok és titkos kulcsok**lehetőséget, majd kattintson az **új ügyfél titka** elemre.
 8. Adja meg az új kulcs leírását (ügyfél titkos kulcsa), válassza a **soha nem jár le** lehetőséget, majd kattintson a **Hozzáadás** gombra.
 9. Jegyezze fel a titok értékét. Az egyszerű szolgáltatás jelszavaként van használatban
10. Válassza az **Áttekintés** lehetőséget. Jegyezze fel az alkalmazás AZONOSÍTÓját. A szolgáltatás felhasználóneveként (bejelentkezési AZONOSÍTÓként az alábbi lépésekben) használatos az egyszerű szolgáltatásnév számára
 
### <a name="create-a-custom-role-for-the-fence-agent"></a>Egyéni szerepkör létrehozása a kerítési ügynökhöz

Kövesse az oktatóanyagot, és [hozzon létre egyéni szerepkört az Azure-erőforrásokhoz az Azure CLI használatával](../../../role-based-access-control/tutorial-custom-role-cli.md#create-a-custom-role).

A JSON-fájlnak a következőhöz hasonlóan kell kinéznie:

- Cserélje le a `<username>`t az Ön által választott névre. Ennek a szerepkör-definíciónak a létrehozásakor el kell kerülnie az ismétlődést.
- Cserélje le a `<subscriptionId>`t az Azure-előfizetés azonosítójával.

```json
{
  "Name": "Linux Fence Agent Role-<username>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscriptionId>"
  ]
}
```

A szerepkör hozzáadásához futtassa a következő parancsot:

- Cserélje le a `<filename>`t a fájl nevére.
- Ha a parancsot a fájl által mentett mappától eltérő útvonalon hajtja végre, a parancsban adja meg a fájl mappájának elérési útját.

```bash
az role definition create --role-definition "<filename>.json"
```

A következő kimenetnek kell megjelennie:

```output
{
  "assignableScopes": [
    "/subscriptions/<subscriptionId>"
  ],
  "description": "Allows to power-off and start virtual machines",
  "id": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<roleNameId>",
  "name": "<roleNameId>",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/virtualMachines/powerOff/action",
        "Microsoft.Compute/virtualMachines/start/action"
      ],
      "dataActions": [],
      "notActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Linux Fence Agent Role-<username>",
  "roleType": "CustomRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="assign-the-custom-role-to-the-service-principal"></a>Az egyéni szerepkör társítása az egyszerű szolgáltatáshoz

Rendelje hozzá az előző lépésben az egyszerű szolgáltatáshoz létrehozott egyéni szerepkör-`Linux Fence Agent Role-<username>`. Ne használja többé a tulajdonosi szerepkört!
 
1. Nyissa meg a következőt: https://portal.azure.com
2. Nyissa meg az [összes erőforrás](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAll) panelt
3. Válassza ki az első fürtcsomópont virtuális gépét.
4. Kattintson a **hozzáférés-vezérlés (iam)** elemre.
5. Kattintson **a szerepkör-hozzárendelés hozzáadása** elemre.
6. **A szerepkör listából válassza** ki a szerepkört `Linux Fence Agent Role-<username>`
7. A **Select (kiválasztás** ) listán adja meg a fent létrehozott alkalmazás nevét `<resourceGroupName>-app`
8. Kattintson a **Mentés** gombra.
9. Ismételje meg a fenti lépéseket a fürt összes csomópontján.

### <a name="create-the-stonith-devices"></a>A STONITH-eszközök létrehozása

Futtassa az alábbi parancsokat az 1. csomóponton:

- Cserélje le a `<ApplicationID>`t az alkalmazás regisztrációs AZONOSÍTÓjának értékére.
- Cserélje le a `<servicePrincipalPassword>`t az ügyfél titkos kódjának értékére.
- Az oktatóanyaghoz használt előfizetésében cserélje le a `<resourceGroupName>`t az erőforráscsoporthoz.
- Cserélje le az `<tenantID>`t és a `<subscriptionId>`t az Azure-előfizetésből.

```bash
sudo pcs property set stonith-timeout=900
sudo pcs stonith create rsc_st_azure fence_azure_arm login="<ApplicationID>" passwd="<servicePrincipalPassword>" resourceGroup="<resourceGroupName>" tenantId="<tenantID>" subscriptionId="<subscriptionId>" power_timeout=240 pcmk_reboot_timeout=900
```

Mivel már hozzáadtak egy szabályt a tűzfalhoz, hogy engedélyezzék a HA szolgáltatást (`--add-service=high-availability`), nem kell megnyitnia a következő tűzfal-portokat az összes csomóponton: 2224, 3121, 21064, 5405. Ha azonban bármilyen típusú kapcsolódási problémát tapasztal a HA-vel, a következő parancs használatával nyissa meg a HA-hoz társított portokat.

> [!TIP]
> Az oktatóanyagban szereplő összes portot egyszerre is hozzáadhatja. A megnyitni kívánt portokat az alábbi relatív szakaszban ismertetjük. Ha most szeretné felvenni az összes portot, adja hozzá a további portokat: 1433 és 5022.

```bash
sudo firewall-cmd --zone=public --add-port=2224/tcp --add-port=3121/tcp --add-port=21064/tcp --add-port=5405/tcp --permanent
sudo firewall-cmd --reload
```

## <a name="install-sql-server-and-mssql-tools"></a>SQL Server-és MSSQL-eszközök telepítése
 
Az alábbi szakasz segítségével telepítheti a virtuális gépekre SQL Server és MSSQL-eszközöket. Hajtsa végre ezeket a műveleteket az összes csomóponton. További információ: [install SQL Server a Red Hat VM](/sql/linux/quickstart-install-connect-red-hat).

### <a name="installing-sql-server-on-the-vms"></a>SQL Server telepítése a virtuális gépekre

A következő parancsokat használja a SQL Server telepítéséhez:

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2017.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```

### <a name="open-firewall-port-1433-for-remote-connections"></a>A 1433-es tűzfal megnyitása távoli kapcsolatokhoz

A távoli csatlakozáshoz meg kell nyitnia a 1433-as portot a virtuális gépen. Az alábbi parancsokkal nyithatja meg az 1433-as portot az egyes virtuális gépek tűzfalán:

```bash
sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
sudo firewall-cmd --reload
```

### <a name="installing-sql-server-command-line-tools"></a>SQL Server parancssori eszközök telepítése

A következő parancsok segítségével telepítheti SQL Server parancssori eszközöket. További információ: [install the SQL Server parancssori eszközök](/sql/linux/quickstart-install-connect-red-hat#tools).

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```
 
> [!NOTE] 
> A kényelem érdekében adja hozzá a/opt/MSSQL-Tools/bin/a PATH környezeti változóhoz. Ez lehetővé teszi, hogy a teljes elérési út megadása nélkül futtassa az eszközöket. Futtassa a következő parancsokat a bejelentkezési munkamenetek és az interaktív/nem bejelentkezési munkamenetek elérési útjának módosításához:</br></br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile`</br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc`</br>
`source ~/.bashrc`


### <a name="check-the-status-of-the-sql-server"></a>A SQL Server állapotának ellenõrzése

Ha elkészült a konfigurációval, ellenőrizheti SQL Server állapotát, és ellenőrizheti, hogy fut-e:

```bash
systemctl status mssql-server --no-pager
```

A következő kimenetnek kell megjelennie:

```output
● mssql-server.service - Microsoft SQL Server Database Engine
   Loaded: loaded (/usr/lib/systemd/system/mssql-server.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2019-12-05 17:30:55 UTC; 20min ago
     Docs: https://docs.microsoft.com/en-us/sql/linux
 Main PID: 11612 (sqlservr)
   CGroup: /system.slice/mssql-server.service
           ├─11612 /opt/mssql/bin/sqlservr
           └─11640 /opt/mssql/bin/sqlservr
```

## <a name="configure-sql-server-always-on-availability-group"></a>SQL Server always on rendelkezésre állási csoport konfigurálása

A következő lépésekkel konfigurálhatja a virtuális gépek SQL Server always on rendelkezésre állási csoportját. További információ: [SQL Server always on rendelkezésre állási csoport konfigurálása a Linux magas rendelkezésre állásához](/sql/linux/sql-server-linux-availability-group-configure-ha)

### <a name="enable-alwayson-availability-groups-and-restart-mssql-server"></a>AlAlwaysOnon rendelkezésre állási csoportok engedélyezése és az MSSQL-Server újraindítása

Engedélyezze az alAlwaysOnon rendelkezésre állási csoportokat minden SQL Server példányt futtató csomóponton. Ezután indítsa újra az MSSQL-Servert. Futtassa a következő parancsfájlt:

```
sudo /opt/mssql/bin/mssql-conf set hadr.hadrenabled 1
sudo systemctl restart mssql-server
```

### <a name="create-a-certificate"></a>Tanúsítvány létrehozása

Jelenleg nem támogatjuk az AD-hitelesítést az AG-végpontra. Ezért az AG Endpoint encryption tanúsítványát kell használnia.

1. Kapcsolódjon az **összes csomóponthoz** SQL Server Management Studio (SSMS) vagy SQL cmd használatával. Futtassa az alábbi parancsokat a AlwaysOn_health-munkamenet engedélyezéséhez és a főkulcs létrehozásához:

    > [!IMPORTANT]
    > Ha távolról csatlakozik a SQL Server-példányhoz, a tűzfalon meg kell nyitnia a 1433-as portot. Emellett engedélyeznie kell a bejövő kapcsolatokat az 1433-as porton az egyes virtuális gépek NSG. További információ: [biztonsági szabály létrehozása](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) egy bejövő biztonsági szabály létrehozásához.

    - Cserélje le a `<Master_Key_Password>`t a saját jelszavára.


    ```sql
    ALTER EVENT SESSION  AlwaysOn_health ON SERVER WITH (STARTUP_STATE=ON);
    GO
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Master_Key_Password>';
    ```

 
1. Kapcsolódjon az elsődleges replikához a SSMS vagy az SQL CMD használatával. Az alábbi parancsok létrehoznak egy tanúsítványt a `/var/opt/mssql/data/dbm_certificate.cer` és egy titkos kulcsot az elsődleges SQL Server-replika `var/opt/mssql/data/dbm_certificate.pvk`:

    - Cserélje le a `<Private_Key_Password>`t a saját jelszavára.

```sql
CREATE CERTIFICATE dbm_certificate WITH SUBJECT = 'dbm';
GO
BACKUP CERTIFICATE dbm_certificate
   TO FILE = '/var/opt/mssql/data/dbm_certificate.cer'
   WITH PRIVATE KEY (
           FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
           ENCRYPTION BY PASSWORD = '<Private_Key_Password>'
       );
```

Az `exit` parancs futtatásával lépjen ki az SQL CMD-munkamenetből, és térjen vissza az SSH-munkamenethez.
 
### <a name="copy-the-certificate-to-the-secondary-replicas-and-create-the-certificates-on-the-server"></a>Másolja a tanúsítványt a másodlagos replikára, és hozza létre a tanúsítványokat a kiszolgálón.

1. Másolja a két, ugyanazon a helyen létrehozott fájlt az összes olyan kiszolgálón, amely a rendelkezésre állási replikákat fogja tárolni.
 
    Az elsődleges kiszolgálón futtassa a következő `scp` parancsot a tanúsítvány célkiszolgálóra való másolásához:

    - Cserélje le a `<username>` és a `<VM2>`t a használt felhasználónévre és a célként megadott virtuális gép nevére.
    - Futtassa ezt a parancsot az összes másodlagos replikához.

    > [!NOTE]
    > Nem kell futtatnia `sudo -i`, amely a legfelső szintű környezetet biztosítja. Egyszerűen futtathatja az `sudo` parancsot az egyes parancsok előtt, ahogy azt az oktatóanyagban korábban elvégezte.

    ```bash
    # The below command allows you to run commands in the root environment
    sudo -i
    ```

    ```bash
    scp /var/opt/mssql/data/dbm_certificate.* <username>@<VM2>:/home/<username>
    ```

1. Futtassa a következő parancsot a célkiszolgálón:

    - Cserélje le a `<username>`t a felhasználónevére.
    - A `mv` parancs áthelyezi a fájlokat vagy a könyvtárat egyik helyről a másikra.
    - A `chown` parancs a fájlok, könyvtárak és hivatkozások tulajdonosának és csoportjának módosítására szolgál.
    - Futtassa ezeket a parancsokat az összes másodlagos replikához.

    ```bash
    sudo -i
    mv /home/<username>/dbm_certificate.* /var/opt/mssql/data/
    cd /var/opt/mssql/data
    chown mssql:mssql dbm_certificate.*
    ```

1. A következő Transact-SQL-szkript létrehoz egy tanúsítványt az elsődleges SQL Server-replikán létrehozott biztonsági másolatból. Frissítse a parancsfájlt erős jelszóval. A visszafejtési jelszó ugyanaz a jelszó, amelyet az előző lépésben a. PVK fájl létrehozásához használt. A tanúsítvány létrehozásához futtassa a következő szkriptet az SQL CMD vagy a SSMS használatával az összes másodlagos kiszolgálón:

    ```sql
    CREATE CERTIFICATE dbm_certificate
        FROM FILE = '/var/opt/mssql/data/dbm_certificate.cer'
        WITH PRIVATE KEY (
        FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
        DECRYPTION BY PASSWORD = '<Private_Key_Password>'
                );
    ```

### <a name="create-the-database-mirroring-endpoints-on-all-replicas"></a>Az adatbázis-tükrözési végpontok létrehozása az összes replikán

Futtassa az alábbi parancsfájlt minden SQL-példányon az SQL CMD vagy a SSMS használatával:

```sql
CREATE ENDPOINT [Hadr_endpoint]
    AS TCP (LISTENER_PORT = 5022)
    FOR DATABASE_MIRRORING (
    ROLE = ALL,
    AUTHENTICATION = CERTIFICATE dbm_certificate,
ENCRYPTION = REQUIRED ALGORITHM AES
);
GO

ALTER ENDPOINT [Hadr_endpoint] STATE = STARTED;
```

### <a name="create-the-availability-group"></a>A rendelkezésre állási csoport létrehozása

Kapcsolódjon az elsődleges replikát futtató SQL Server-példányhoz az SQL CMD vagy a SSMS használatával. Futtassa a következő parancsot a rendelkezésre állási csoport létrehozásához:

- Cserélje le a `ag1`t a kívánt rendelkezésre állási csoport nevére.
- Cserélje le a `<VM1>`, `<VM2>`és `<VM3>` értékeket a replikákat tároló SQL Server-példányok nevével.

```sql
CREATE AVAILABILITY GROUP [ag1]
     WITH (DB_FAILOVER = ON, CLUSTER_TYPE = EXTERNAL)
     FOR REPLICA ON
         N'<VM1>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM1>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM2>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM2>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM3>'
         WITH(
            ENDPOINT_URL = N'tcp://<VM3>:5022',
            AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
            FAILOVER_MODE = EXTERNAL,
            SEEDING_MODE = AUTOMATIC
            );
GO

ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
```

### <a name="create-a-sql-server-login-for-pacemaker"></a>SQL Server-bejelentkezés létrehozása a Pacemakerhez

Az összes SQL-kiszolgálón hozzon létre egy SQL-bejelentkezést a Pacemakerhez. A következő Transact-SQL egy bejelentkezési azonosítót hoz létre.

- Cserélje le a `<password>`t a saját összetett jelszavára.

```sql
USE [master]
GO
CREATE LOGIN [pacemakerLogin] with PASSWORD= N'<password>';
GO
ALTER SERVER ROLE [sysadmin] ADD MEMBER [pacemakerLogin];
```

Mentse az összes SQL-kiszolgálón a SQL Server bejelentkezéshez használt hitelesítő adatokat. 

1. Hozza létre a fájlt:

    ```bash
    sudo vi /var/opt/mssql/secrets/passwd
    ```

1. Adja hozzá a következő két sort a fájlhoz:

    ```bash
    pacemakerLogin
    <password>
    ```

    A **VI** -szerkesztőből való kilépéshez először nyomja meg az **ESC** billentyűt, majd írja be a `:wq` parancsot a fájl írásához és a kilépéshez.

1. A fájl csak a root által olvasható legyen:

    ```bash
    sudo chown root:root /var/opt/mssql/secrets/passwd
    sudo chmod 400 /var/opt/mssql/secrets/passwd
    ```

### <a name="join-secondary-replicas-to-the-availability-group"></a>Másodlagos replikák csatlakoztatása a rendelkezésre állási csoporthoz

1. Ahhoz, hogy a másodlagos replikákat az AG-hoz lehessen csatlakoztatni, a tűzfalon meg kell nyitnia az 5022-es portot az összes kiszolgálón. Futtassa az alábbi parancsot az SSH-munkamenetben:

    ```bash
    sudo firewall-cmd --zone=public --add-port=5022/tcp --permanent
    sudo firewall-cmd --reload
    ```

1. A másodlagos replikákban futtassa a következő parancsokat az AG-hez való csatlakozáshoz:

    ```sql
    ALTER AVAILABILITY GROUP [ag1] JOIN WITH (CLUSTER_TYPE = EXTERNAL);
    GO

    ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
    ```

1. Futtassa a következő Transact-SQL-parancsfájlt az elsődleges replikán és minden másodlagos replikán:

    ```sql
    GRANT ALTER, CONTROL, VIEW DEFINITION ON AVAILABILITY GROUP::ag1 TO pacemakerLogin;
    GO
    
    GRANT VIEW SERVER STATE TO pacemakerLogin;
    ```

1. A másodlagos replikák csatlakoztatása után megtekintheti őket a SSMS Object Explorer az **Always on magas rendelkezésre állású** csomópont kibontásával:

    ![Availability-Group-joined. png](media/sql-server-linux-rhel-ha-stonith-tutorial/availability-group-joined.png)

### <a name="add-a-database-to-the-availability-group"></a>Adatbázis hozzáadása a rendelkezésre állási csoporthoz

A [rendelkezésre állási csoport konfigurálása című cikket a következő témakörben találja: adatbázis hozzáadása](/sql/linux/sql-server-linux-availability-group-configure-ha#add-a-database-to-the-availability-group).

Ebben a lépésben a következő Transact-SQL-parancsokat használja a rendszer. Futtassa ezeket a parancsokat az elsődleges replikán:

```sql
CREATE DATABASE [db1]; -- creates a database named db1
GO

ALTER DATABASE [db1] SET RECOVERY FULL; -- set the database in full recovery mode
GO

BACKUP DATABASE [db1] -- backs up the database to disk
   TO DISK = N'/var/opt/mssql/data/db1.bak';
GO

ALTER AVAILABILITY GROUP [ag1] ADD DATABASE [db1]; -- adds the database db1 to the AG
```

### <a name="verify-that-the-database-is-created-on-the-secondary-servers"></a>Annak ellenőrzése, hogy az adatbázis létrejött-e a másodlagos kiszolgálókon

Minden másodlagos SQL Server-replikán futtassa a következő lekérdezést, hogy ellenőrizze, hogy létrejött-e a db1-adatbázis, és SZINKRONIZÁLt állapotban van-e:

```
SELECT * FROM sys.databases WHERE name = 'db1';
GO
SELECT DB_NAME(database_id) AS 'database', synchronization_state_desc FROM sys.dm_hadr_database_replica_states;
```

Ha `db1`esetében SZINKRONIZÁLta a `synchronization_state_desc` listát, ez azt jelenti, hogy a replikák szinkronizálva vannak. A formátumú másodlagos zónák az elsődleges replikában `db1` láthatók.

## <a name="create-availability-group-resources-in-the-pacemaker-cluster"></a>Rendelkezésre állási csoport erőforrásainak létrehozása a pacemaker-fürtben

A [rendelkezésre állási csoport erőforrásainak a pacemaker-fürtön történő létrehozásához](/sql/linux/sql-server-linux-create-availability-group#create-the-availability-group-resources-in-the-pacemaker-cluster-external-only)a következő útmutatót fogjuk ismertetni:.

### <a name="create-the-ag-cluster-resource"></a>Az AG-fürt erőforrásának létrehozása

1. A következő parancs használatával hozza létre az erőforrás-`ag_cluster` a rendelkezésre álló csoport `ag1`.

    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s master notify=true
    ```

1. Ellenőrizze az erőforrást, és győződjön meg arról, hogy online állapotban van, mielőtt továbblép a következő parancs használatával:

    ```bash
    sudo pcs resource
    ```

    A következő kimenetnek kell megjelennie:

    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
    Masters: [ <VM1> ]
    Slaves: [ <VM2> <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM1>
    ```

### <a name="create-a-virtual-ip-resource"></a>Virtuális IP-erőforrás létrehozása

1. A hálózatról elérhető statikus IP-cím használatával hozzon létre egy virtuális IP-erőforrást. A `nmap`parancssori eszköz használatával is megtalálhatja az egyiket.

    ```bash
    nmap -sP <IPRange>
    # For example: nmap -sP 10.0.0.*
    # The above will scan for all IP addresses that are already occupied in the 10.0.0.x space.
    ```

1. A **stonith-kompatibilis** tulajdonság beállítása false (hamis) értékre

    ```bash
    sudo pcs property set stonith-enabled=false
    ```

1. Hozza létre a virtuális IP-erőforrást a következő parancs használatával:

    - Cserélje le az alábbi `<availableIP>` értéket egy nem használt IP-címhez.

    ```bash
    sudo pcs resource create virtualip ocf:heartbeat:IPaddr2 ip=<availableIP>
    ```

### <a name="add-constraints"></a>Megkötések hozzáadása

1. Annak érdekében, hogy az IP-cím és az AG-erőforrás ugyanazon a csomóponton fusson, konfigurálni kell egy közös elhelyezési korlátozást. Futtassa az alábbi parancsot:

    ```bash
    sudo pcs constraint colocation add virtualip ag_cluster-master INFINITY with-rsc-role=Master
    ```

1. Hozzon létre egy megrendelési korlátozást, amely biztosítja, hogy az AG-erőforrás az IP-cím előtt fusson. Míg a elhelyezési megkötés egy megrendelés megkötését jelenti, ez kikényszeríti azt.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start virtualip
    ```

1. A megkötések ellenőrzéséhez futtassa a következő parancsot:

    ```bash
    sudo pcs constraint list --full
    ```

    A következő kimenetnek kell megjelennie:

    ```
    Location Constraints:
    Ordering Constraints:
          promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
          virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

### <a name="re-enable-stonith"></a>Stonith újbóli engedélyezése

Készen áll a tesztelésre. Engedélyezze újra a stonith a fürtben a következő parancs futtatásával az 1. csomóponton:

```bash
sudo pcs property set stonith-enabled=true
```

### <a name="check-cluster-status"></a>A fürt állapotának ellenőrzése

A fürt erőforrásainak állapotát a következő parancs használatával tekintheti meg:

```output
[<username>@VM1 ~]$ sudo pcs status
Cluster name: az-hacluster
Stack: corosync
Current DC: <VM3> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
Last updated: Sat Dec  7 00:18:38 2019
Last change: Sat Dec  7 00:18:02 2019 by root via cibadmin on VM1

3 nodes configured
5 resources configured

Online: [ <VM1> <VM2> <VM3> ]

Full list of resources:

 Master/Slave Set: ag_cluster-master [ag_cluster]
     Masters: [ <VM2> ]
     Slaves: [ <VM1> <VM3> ]
 virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
 rsc_st_azure   (stonith:fence_azure_arm):      Started <VM1>

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
```

## <a name="test-failover"></a>Feladatátvétel tesztelése

Annak biztosítása érdekében, hogy a konfiguráció eddig sikeres volt, tesztelni fogjuk a feladatátvételt. További információ: [Always On rendelkezésre állási csoport feladatátvétele Linuxon](/sql/linux/sql-server-linux-availability-group-failover-ha).

1. Futtassa a következő parancsot az elsődleges replika manuális feladatátvételéhez `<VM2>`. Cserélje le a `<VM2>` értéket a kiszolgálónév értékére.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Ha újra megtekinti a korlátozásokat, a manuális feladatátvétel miatt egy másik korlátozás lett hozzáadva:

    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-master)
    Ordering Constraints:
            promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

1. Távolítsa el a (z) AZONOSÍTÓJÚ korlátozást a következő parancs használatával `cli-prefer-ag_cluster-master`:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Ellenőrizze a fürt erőforrásait a `sudo pcs resource`parancs használatával, és látnia kell, hogy az elsődleges példány most már `<VM2>`.

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         ag_cluster (ocf::mssql:ag):        FAILED <VM1> (Monitoring)
         Masters: [ <VM2> ]
         Slaves: [ <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         Masters: [ <VM2> ]
         Slaves: [ <VM1> <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
     
    ```

## <a name="test-fencing"></a>A kerítés tesztelése

A következő parancs futtatásával tesztelheti a STONITH. Futtassa az alábbi parancsot a `<VM3>``<VM1>`.

```bash
sudo pcs stonith fence <VM3> --debug
```

> [!NOTE]
> Alapértelmezés szerint a kerítés művelettel a csomópont ki-és bekapcsolva állapotba kerül. Ha csak a csomópontot szeretné offline állapotba helyezni, használja a parancsban a `--off` lehetőséget.

A következő kimenetnek kell megjelennie:

```output
[<username>@<VM1> ~]$ sudo pcs stonith fence <VM3> --debug
Running: stonith_admin -B <VM3>
Return Value: 0
--Debug Output Start--
--Debug Output End--
 
Node: <VM3> fenced
```
A kerítések eszközének tesztelésével kapcsolatos további információkért tekintse meg a következő [Red Hat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/s1-stonithtest-haar) cikket.

## <a name="next-steps"></a>Következő lépések

Ahhoz, hogy a rendelkezésre állási csoport figyelőjét az Azure-ban létrehozott SQL-kiszolgálókhoz is használni tudja, először létre kell hoznia és konfigurálnia kell egy terheléselosztó-t.

> [!div class="nextstepaction"]
> [A terheléselosztó létrehozása és konfigurálása a Azure Portal](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md#create-and-configure-the-load-balancer-in-the-azure-portal)