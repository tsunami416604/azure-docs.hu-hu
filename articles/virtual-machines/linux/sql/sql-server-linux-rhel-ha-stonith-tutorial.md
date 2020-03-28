---
title: Az SQL Server rendelkezésre állási csoportjainak konfigurálása RHEL virtuális gépeken az Azure - Linux virtuális gépeken | Microsoft dokumentumok
description: Tudnivalók a magas rendelkezésre állás beállításáról RHEL fürtkörnyezetben és a STONITH beállítása
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 02/27/2020
ms.openlocfilehash: 40c91f67231fb6a9d01191ee5215eae8d4dc045b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79096703"
---
# <a name="tutorial-configure-availability-groups-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Oktatóanyag: Az SQL Server rendelkezésre állási csoportjainak konfigurálása RHEL virtuális gépeken az Azure-ban 

> [!NOTE]
> A bemutatott bemutató **nyilvános előzetes verzióban érhető el.** 
>
> Ebben az oktatóanyagban az SQL Server 2017 és az RHEL 7.6 használatát használjuk, de az SQL Server 2019 rhel 7 vagy RHEL 8 rendszerben is használható a HA konfigurálásához. A rendelkezésre állási csoport erőforrásainak konfigurálására szolgáló parancsok megváltoztak az RHEL 8-ban, és érdemes megnézni a [rendelkezésre állási csoport erőforrásának létrehozása](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) és az RHEL 8 erőforrások című cikket a helyes parancsokról.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> - Új erőforráscsoport, rendelkezésre állási csoport és Azure Linux virtuális gépek (VM) létrehozása
> - Magas rendelkezésre állás engedélyezése (HA)
> - Szívritmus-szabályozó fürt létrehozása
> - Vívóügynök konfigurálása STONITH eszköz létrehozásával
> - Sql Server és mssql-eszközök telepítése RHEL-re
> - Sql Server mindig a rendelkezésre állási csoporton konfigurálása
> - A Pacemaker-fürt rendelkezésre állási csoportjának (AG) erőforrásainak konfigurálása
> - Feladatátvétel és a kerítésügynök tesztelése

Ez az oktatóanyag az Azure parancssori felületét (CLI) fogja használni az Azure-ban az erőforrások üzembe helyezéséhez.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát szeretné használni, ez az oktatóanyag az Azure CLI 2.0.30-as vagy újabb verzióját igényli. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Ha egynél több előfizetéssel rendelkezik, [állítsa be azt az előfizetést,](/cli/azure/manage-azure-subscriptions-azure-cli) amelyhez telepíteni szeretné ezeket az erőforrásokat.

A következő paranccsal erőforráscsoportot `<resourceGroupName>` hozhat létre egy régióban. Cserélje `<resourceGroupName>` ki az Ön által választott nevet. Ezt az `East US 2` oktatóanyagot használjuk. További információt az alábbi [rövid útmutatóban talál.](../quick-create-cli.md)

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus2
```

## <a name="create-an-availability-set"></a>Rendelkezésre állási készlet létrehozása

A következő lépés egy rendelkezésre állási csoport létrehozása. Futtassa a következő parancsot `<resourceGroupName>` az Azure Cloud Shellben, és cserélje le az erőforráscsoport nevére. Válasszon nevet `<availabilitySetName>`a számára.

```azurecli-interactive
az vm availability-set create \
    --resource-group <resourceGroupName> \
    --name <availabilitySetName> \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

A parancs befejezése után a következő eredményeket kell kapnia:

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
> Ha úgy dönt, a Pay-As-You-Go (PAYG) RHEL-lemezképet, és konfigurálja a magas rendelkezésre állású (HA), akkor szükség lehet regisztrálni az előfizetést. Ez azt eredményezheti, hogy kétszer kell fizetnie az előfizetésért, mivel a virtuális gép microsoft Azure RHEL-előfizetése és a Red Hat-előfizetés díja fizetendő. További információ: https://access.redhat.com/solutions/2458541.
>
> Annak elkerülése érdekében, hogy "dupla számlázott", használjon RHEL HA-lemezképet az Azure virtuális gép létrehozásakor. Rhel-HA-lemezképekként kínált képek szintén PAYG-képek HA repo előre engedélyezve.

1. Az RHEL-t ha-val kínáló virtuálisgép-lemezképek listájának beszerezni:

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

    Ebben az oktatóanyagban a `RedHat:RHEL-HA:7.6:7.6.2019062019`képet választjuk.

    > [!IMPORTANT]
    > Az elérhetőségi csoport beállításához a gépneveknek 15 karakternél rövidebbnek kell lenniük. A felhasználónév nem tartalmazhat nagybetűs karaktereket, és a jelszavaknak 12 karakternél hosszabbnak kell lennie.

1. 3 virtuális gépet szeretnénk létrehozni a rendelkezésre állási csoportban. Cserélje le a következőket az alábbi parancsban:

    - `<resourceGroupName>`
    - `<VM-basename>`
    - `<availabilitySetName>`
    - `<VM-Size>`- Egy példa lenne a "Standard_D16_v3"
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

A fenti parancs létrehozza a virtuális gépeket, és létrehoz egy alapértelmezett virtuális hálózatot a virtuális gépekhez. A különböző konfigurációkról további információt az [az vm create](https://docs.microsoft.com/cli/azure/vm) cikkben talál.

Az alábbihoz hasonló eredményeket kell kapnia, ha a parancs befejeződik az egyes virtuális gépekhez:

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
> A fenti paranccsal létrehozott alapértelmezett lemez alapértelmezés szerint 32 GB-os operációs rendszert hoz létre. Ezzel az alapértelmezett telepítéssel potenciálisan elfogyhat a hely. A fenti `az vm create` parancshoz hozzáadott alábbi paraméterrel hozhat létre 128 GB-os `--os-disk-size-gb 128`operációs rendszert a példaként: .
>
> Ezután [konfigurálhatja a Logikai kötetkezelőt (LVM),](../../../virtual-machines/linux/configure-lvm.md) ha ki kell bővítenie a megfelelő mappaköteteket a telepítéshez.

### <a name="test-connection-to-the-created-vms"></a>A létrehozott virtuális gépekhez való csatlakozás tesztelése

Csatlakozzon a VM1-hez vagy a többi virtuális géphez a következő paranccsal az Azure Cloud Shellben. Ha nem találja a virtuálisgép-ip-szolgáltatásokat, kövesse ezt [a rövid útmutatót az Azure Cloud Shellen.](../../../cloud-shell/quickstart.md#ssh-into-your-linux-vm)

```azurecli-interactive
ssh <username>@publicipaddress
```

Ha a kapcsolat sikeres, a következő kimenetnek kell látnia a Linux terminált:

```output
[<username>@<VM1> ~]$
```

Írja `exit` be, hogy elhagyja az SSH munkamenetet.

## <a name="enable-high-availability"></a>Magas rendelkezésre állás engedélyezése

> [!IMPORTANT]
> Az oktatóanyag ezen részének befejezéséhez rendelkeznie kell egy RHEL-előfizetéssel és a magas rendelkezésre állású bővítménysel. Ha az előző szakaszban ajánlott képet használ, nem kell másik előfizetést regisztrálnia.
 
Csatlakozzon az egyes virtuálisgép-csomópontokhoz, és kövesse az alábbi útmutatót a HA engedélyezéséhez. További információ: [Az RHEL magas rendelkezésre állású előfizetésengedélyezése](/sql/linux/sql-server-linux-availability-group-cluster-rhel#enable-the-high-availability-subscription-for-rhel)című témakörben talál.

> [!TIP]
> Egyszerűbb lesz, ha egyszerre nyit meg egy SSH-munkamenetet az egyes virtuális gépeken, mivel ugyanazokat a parancsokat kell futtatni az egyes virtuális gépeken a cikk ben.
>
> Ha több `sudo` parancsot másol és illeszt be, és a rendszer jelszót kér, a további parancsok nem fognak futni. Futtassa az egyes parancsokat külön-külön.


1. Futtassa a következő parancsokat az egyes virtuális gépeken a Pacemaker tűzfalportjainak megnyitásához:

    ```bash
    sudo firewall-cmd --permanent --add-service=high-availability
    sudo firewall-cmd --reload
    ```

1. A Pacemaker-csomagok frissítése és telepítése az összes csomópontra a következő parancsokkal:

    > [!NOTE]
    > Az **nmap** a parancsblokk részeként van telepítve a hálózatban elérhető IP-címek megkereséséhez. Nem kell telepíteni **nmap**, de hasznos lesz később ez a bemutató.

    ```bash
    sudo yum update -y
    sudo yum install -y pacemaker pcs fence-agents-all resource-agents fence-agents-azure-arm nmap
    sudo reboot
    ```

1. Állítsa be a pacemakercsomagok telepítésekor létrehozott alapértelmezett felhasználó jelszavát. Használja ugyanazt a jelszót az összes csomóponton.

    ```bash
    sudo passwd hacluster
    ```

1. A következő paranccsal nyissa meg a hosts fájlt, és állítsa be az állomásnév feloldását. További információt az [AG konfigurálása](/sql/linux/sql-server-linux-availability-group-configure-ha#prerequisites) a hosts fájl konfigurálásáról című témakörben talál.

    ```
    sudo vi /etc/hosts
    ```

    A **vi** szerkesztőben `i` írja be a szöveg beszúrásához, és egy üres sorba adja hozzá a megfelelő virtuális gép **privát IP-címét.** Ezután adja hozzá a virtuális gép nevét az IP melletti szóköz után. Minden sornak külön bejegyzéssel kell rendelkeznie.

    ```output
    <IP1> <VM1>
    <IP2> <VM2>
    <IP3> <VM3>
    ```

    > [!IMPORTANT]
    > Javasoljuk, hogy a fenti **privát IP-címét** használja. A nyilvános IP-cím ebben a konfigurációban a beállítás sikertelen lesz, és nem javasoljuk, hogy a virtuális gép külső hálózatoknak.

    A **vi** szerkesztőből való kilépéshez először nyomja meg `:wq` az **Esc** billentyűt, majd írja be a parancsot a fájl megírásához, majd lépjen ki.

## <a name="create-the-pacemaker-cluster"></a>A pacemaker-fürt létrehozása

Ebben a szakaszban engedélyezzük és elindítjuk a pcsd szolgáltatást, majd konfiguráljuk a fürtöt. Linuxos SQL Server esetén a fürterőforrások nem jönnek létre automatikusan. A pacemaker-erőforrásokat manuálisan kell engedélyeznünk és létrehozni. További információt az [RHEL feladatátvevő fürtpéldányának konfigurálásáról](/sql/linux/sql-server-linux-shared-disk-cluster-red-hat-7-configure#install-and-configure-pacemaker-on-each-cluster-node) szóló cikkben talál.

### <a name="enable-and-start-pcsd-service-and-pacemaker"></a>Pcsd szolgáltatás és pacemaker engedélyezése és indítása

1. Futtassa a parancsokat az összes csomóponton. Ezek a parancsok lehetővé teszik, hogy a csomópontok újraindítás után újra csatlakozzanak a fürthöz.

    ```bash
    sudo systemctl enable pcsd
    sudo systemctl start pcsd
    sudo systemctl enable pacemaker
    ``` 

1. Távolítsa el a meglévő fürtkonfigurációkat az összes csomópontról. Futtassa az alábbi parancsot:

    ```bash
    sudo pcs cluster destroy 
    sudo systemctl enable pacemaker 
    ```

1. Az elsődleges csomóponton futtassa a következő parancsokat a fürt beállításához.

    - Amikor a `pcs cluster auth` fürtcsomópontok hitelesítésére parancsot futtat, a rendszer jelszót kér. Adja meg a korábban létrehozott **hacluster** felhasználó jelszavát.

    ```bash
    sudo pcs cluster auth <VM1> <VM2> <VM3> -u hacluster
    sudo pcs cluster setup --name az-hacluster <VM1> <VM2> <VM3> --token 30000
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

1. Futtassa a következő parancsot annak ellenőrzéséhez, hogy az összes csomópont online állapotban van-e.

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

1. Állítsa a várt szavazatokat az élő fürtben 3-ra. Ez a parancs csak az élő fürtre van hatással, és nem módosítja a konfigurációs fájlokat.

    Állítsa be a várt szavazatokat a következő paranccsal:

    ```bash
    sudo pcs quorum expected-votes 3
    ```

## <a name="configure-the-fencing-agent"></a>A vívóügynök konfigurálása

A STONITH eszköz biztosít egy vívó anyag. Az alábbi utasítások módosulnak ehhez az oktatóanyaghoz. További információ: [Create a STONITH device](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#create-stonith-device).
 
[Ellenőrizze az Azure Fence Agent verzióját, hogy megbizonyosodjon arról, hogy az frissült.](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#cluster-installation) Használja az alábbi parancsot:

```bash
sudo yum info fence-agents-azure-arm
```

Az alábbi példához hasonló kimenetnek kell lennie.

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

### <a name="register-a-new-application-in-azure-active-directory"></a>Új alkalmazás regisztrálása az Azure Active Directoryban
 
 1. Nyissa meg a következőt: https://portal.azure.com
 2. Nyissa meg az [Azure Active Directory panelt.](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) Nyissa meg a Tulajdonságok felet, és írja le a címtárazonosítót. Ez a`tenant ID`
 3. Kattintson [ **az Alkalmazásregisztrációk elemre**](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
 4. Kattintson **az Új regisztráció gombra.**
 5. Írjon be `<resourceGroupName>-app`egy **nevet,** válassza a Fiókok lehetőséget **ebben a szervezeti címtárban.**
 6. Válassza az Alkalmazástípus **web**lehetőséget, adja http://localhost) meg a bejelentkezési URL-címet (például kattintson a Hozzáadás gombra. A bejelentkezési URL-cím nem használható, és bármilyen érvényes URL-cím lehet. Miután elkészült, kattintson **a Regisztráció gombra**
 7. Válassza a **Tanúsítványok és titkos kulcsok lehetőséget** az új alkalmazásregisztrációhoz, majd kattintson az Új **ügyféltitok** elemre.
 8. Adjon meg egy új kulcs leírását (ügyféltitok), válassza a **Soha nem jár le** lehetőséget, és kattintson a **Hozzáadás** gombra.
 9. Írja le a titok értékét. A szolgáltatásegyszerű jelszóként szolgál.
10. Válassza az **Áttekintés** lehetőséget. Írja le az alkalmazásazonosítót. A szolgáltatásnév felhasználóneveként (az alábbi lépésekben bejelentkezési azonosító)
 
### <a name="create-a-custom-role-for-the-fence-agent"></a>Egyéni szerepkör létrehozása a kerítésügynök számára

Kövesse az [oktatóanyagot az Azure-erőforrások egyéni szerepkörének létrehozásához az Azure CLI használatával című oktatóanyaglétrehozásához.](../../../role-based-access-control/tutorial-custom-role-cli.md#create-a-custom-role)

A json fájlnak a következőhöz hasonlóan kell kinéznie:

- Cserélje `<username>` le az Ön által választott nevet. Ezzel elkerülhető a párhuzamosság a szerepkör-definíció létrehozásakor.
- Cserélje `<subscriptionId>` le az Azure-előfizetés-azonosítóját.

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

- Cserélje `<filename>` le a fájl nevére.
- Ha a parancsot nem abból a mappából hajtjuk végre, ahba, ahba, ahba a fájlt menti, a parancsban szerepeljen a fájl mappaelérési útja.

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

### <a name="assign-the-custom-role-to-the-service-principal"></a>Az egyéni szerepkör hozzárendelése az egyszerű szolgáltatáshoz

Rendelje hozzá `Linux Fence Agent Role-<username>` az utolsó lépésben létrehozott egyéni szerepkört a Szolgáltatásnévhez. Ne használja többé a Tulajdonos szerepkört!
 
1. Nyissa meg a következőt: https://portal.azure.com
2. A [Minden erőforrás panel megnyitása](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAll)
3. Az első fürtcsomópont virtuális gépének kiválasztása
4. Kattintson **a Hozzáférés-vezérlés (IAM) elemre**
5. Kattintson **a Szerepkör-hozzárendelés hozzáadása gombra.**
6. A szerepkör `Linux Fence Agent Role-<username>` kiválasztása a **Szerepkör** listából
7. A **Kijelölés** listában adja meg a fent létrehozott alkalmazás nevét,`<resourceGroupName>-app`
8. Kattintson a **Mentés gombra**
9. Ismételje meg a fenti lépéseket az összes fürtcsomóponthoz.

### <a name="create-the-stonith-devices"></a>A STONITH eszközök létrehozása

Futtassa a következő parancsokat az 1- es csomóponton:

- Cserélje `<ApplicationID>` le a bejegyzés azonosítóértékére.
- Cserélje `<servicePrincipalPassword>` le az ügyféltitok ból származó értéket.
- Cserélje `<resourceGroupName>` le az oktatóanyaghoz használt előfizetés erőforráscsoportjára.
- Cserélje `<tenantID>` ki `<subscriptionId>` a és az az Azure-előfizetésből.

```bash
sudo pcs property set stonith-timeout=900
sudo pcs stonith create rsc_st_azure fence_azure_arm login="<ApplicationID>" passwd="<servicePrincipalPassword>" resourceGroup="<resourceGroupName>" tenantId="<tenantID>" subscriptionId="<subscriptionId>" power_timeout=240 pcmk_reboot_timeout=900
```

Mivel már hozzáadtunk egy szabályt a tűzfalhoz, hogy engedélyezze a HA szolgáltatást (`--add-service=high-availability`), nincs szükség a következő tűzfalportok megnyitására az összes csomóponton: 2224, 3121, 21064, 5405. Ha azonban bármilyen típusú csatlakozási problémát tapasztal a HA-val kapcsolatban, a következő paranccsal nyissa meg a HA-hoz társított portokat.

> [!TIP]
> Az oktatóanyag összes portjának hozzáadásával időt takaríthat meg. A megnyitandó portokat az alábbi relatív szakaszok ismertetik. Ha most szeretné hozzáadni az összes portot, adja hozzá a további portokat: 1433 és 5022.

```bash
sudo firewall-cmd --zone=public --add-port=2224/tcp --add-port=3121/tcp --add-port=21064/tcp --add-port=5405/tcp --permanent
sudo firewall-cmd --reload
```

## <a name="install-sql-server-and-mssql-tools"></a>Az SQL Server és az mssql-tools telepítése
 
Az alábbi szakaszban telepítheti az SQL Server és mssql-eszközöket a virtuális gépekre. Hajtsa végre ezeket a műveleteket az összes csomóponton. További információt az [SQL Server a Red Hat virtuális gép telepítése című témakörben](/sql/linux/quickstart-install-connect-red-hat)talál.

### <a name="installing-sql-server-on-the-vms"></a>Az SQL Server telepítése a virtuális gépeken

Az SQL Server telepítéséhez a következő parancsok at használjuk:

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2017.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```

### <a name="open-firewall-port-1433-for-remote-connections"></a>Nyissa meg az 1433-as tűzfalportot távoli kapcsolatokhoz

A távoli csatlakozáshoz meg kell nyitnia az 1433-as portot a virtuális számítógépen. Az alábbi parancsokkal nyissa meg az 1433-as portot az egyes virtuális gépek tűzfalán:

```bash
sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
sudo firewall-cmd --reload
```

### <a name="installing-sql-server-command-line-tools"></a>Az SQL Server parancssori eszközeinek telepítése

Az SQL Server parancssori eszközeinek telepítéséhez a következő parancsok használhatók. További információt [az SQL Server parancssori eszközeinek telepítése című](/sql/linux/quickstart-install-connect-red-hat#tools)témakörben talál.

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```
 
> [!NOTE] 
> A kényelem érdekében adja hozzá a /opt/mssql-tools/bin/ kapcsolót a PATH környezeti változóhoz. Ez lehetővé teszi az eszközök futtatását a teljes elérési út megadása nélkül. A következő parancsok futtatásával módosítsa a PATH értékeit a bejelentkezési munkamenetek és az interaktív/nem bejelentkezési munkamenetekre vonatkozóan egyaránt:</br></br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile`</br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc`</br>
`source ~/.bashrc`


### <a name="check-the-status-of-the-sql-server"></a>Az SQL Server állapotának ellenőrzése

Miután végzett a konfigurációval, ellenőrizheti az SQL Server állapotát, és ellenőrizheti, hogy fut-e:

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

## <a name="configure-sql-server-always-on-availability-group"></a>Sql Server mindig a rendelkezésre állási csoporton konfigurálása

Az alábbi lépésekkel konfigurálhatja az SQL Server Always On Availability Group szolgáltatását a virtuális gépekhez. További információ: [Az SQL Server Always On Availability Group konfigurálása magas rendelkezésre állású Linuxon](/sql/linux/sql-server-linux-availability-group-configure-ha)

### <a name="enable-alwayson-availability-groups-and-restart-mssql-server"></a>AlwaysOn rendelkezésre állási csoportok engedélyezése és az mssql-server újraindítása

Engedélyezze az AlwaysOn rendelkezésre állási csoportokat minden olyan csomóponton, amely SQL Server-példányt üzemeltet. Ezután indítsa újra az mssql-server alkalmazást. Futtassa a következő parancsfájlt:

```
sudo /opt/mssql/bin/mssql-conf set hadr.hadrenabled 1
sudo systemctl restart mssql-server
```

### <a name="create-a-certificate"></a>Tanúsítvány létrehozása

Jelenleg nem támogatjuk az AD-hitelesítést az AG végponton. Ezért az AG végpontok titkosításához tanúsítványt kell használnunk.

1. Csatlakozzon **az összes csomóponthoz az** SQL Server Management Studio (SSMS) vagy az SQL CMD használatával. Futtassa a következő parancsokat AlwaysOn_health munkamenet engedélyezéséhez, és hozzon létre egy főkulcsot:

    > [!IMPORTANT]
    > Ha távolról csatlakozik az SQL Server-példányhoz, meg kell nyitnia az 1433-as portot a tűzfalon. Emellett engedélyeznie kell a bejövő kapcsolatokat az 1433-as portra az NSG-ben az egyes virtuális gépekhez. További információt a Bejövő biztonsági szabály létrehozásához szükséges [biztonsági szabály](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) létrehozása című témakörben talál.

    - Cserélje `<Master_Key_Password>` le a saját jelszavát.


    ```sql
    ALTER EVENT SESSION  AlwaysOn_health ON SERVER WITH (STARTUP_STATE=ON);
    GO
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Master_Key_Password>';
    ```

 
1. Csatlakozzon az elsődleges replika ssms vagy SQL CMD használatával. Az alábbi parancsok létrehoznak `/var/opt/mssql/data/dbm_certificate.cer` egy tanúsítványt `var/opt/mssql/data/dbm_certificate.pvk` és egy titkos kulcsot az elsődleges SQL Server kópián:

    - Cserélje `<Private_Key_Password>` le a saját jelszavát.

```sql
CREATE CERTIFICATE dbm_certificate WITH SUBJECT = 'dbm';
GO

BACKUP CERTIFICATE dbm_certificate
   TO FILE = '/var/opt/mssql/data/dbm_certificate.cer'
   WITH PRIVATE KEY (
           FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
           ENCRYPTION BY PASSWORD = '<Private_Key_Password>'
       );
GO
```

Lépjen ki az SQL CMD-munkamenetből a `exit` parancs futtatásával, majd térjen vissza az SSH-munkamenethez.
 
### <a name="copy-the-certificate-to-the-secondary-replicas-and-create-the-certificates-on-the-server"></a>Másolja a tanúsítványt a másodlagos replikákba, és hozza létre a tanúsítványokat a kiszolgálón

1. Másolja a két létrehozott fájlt ugyanarra a helyre az összes olyan kiszolgálón, amely rendelkezésre állási replikákat tartalmaz.
 
    Az elsődleges kiszolgálón futtassa a következő `scp` parancsot a tanúsítvány célkiszolgálókra való másolásához:

    - Cserélje `<username>` `<VM2>` le, és a felhasználó nevét és a cél virtuális gép nevét, amelyet használ.
    - Futtassa ezt a parancsot az összes másodlagos replikára.

    > [!NOTE]
    > Nem kell futtatnia, `sudo -i`ami gyökérkörnyezetet biztosít. Ön tudna csak `sudo` fuss a követel előtt mindegyik követel mint mi korábban tett -ban ez konzultáció.

    ```bash
    # The below command allows you to run commands in the root environment
    sudo -i
    ```

    ```bash
    scp /var/opt/mssql/data/dbm_certificate.* <username>@<VM2>:/home/<username>
    ```

1. A célkiszolgálón futtassa a következő parancsot:

    - Cserélje `<username>` le a felhasználónevét.
    - A `mv` parancs áthelyezi a fájlokat vagy a könyvtárat egyik helyről a másikra.
    - A `chown` parancs a fájlok, könyvtárak vagy hivatkozások tulajdonosának és csoportjának módosítására szolgál.
    - Futtassa ezeket a parancsokat az összes másodlagos replikához.

    ```bash
    sudo -i
    mv /home/<username>/dbm_certificate.* /var/opt/mssql/data/
    cd /var/opt/mssql/data
    chown mssql:mssql dbm_certificate.*
    ```

1. A következő Transact-SQL parancsfájl létrehoz egy tanúsítványt az elsődleges SQL Server kópián létrehozott biztonsági másolatból. Frissítse a parancsfájlt erős jelszavakkal. A visszafejtési jelszó megegyezik a .pvk fájl előző lépésben történő létrehozásához használt jelszóval. A tanúsítvány létrehozásához futtassa a következő parancsfájlt SQL CMD vagy SSMS használatával az összes másodlagos kiszolgálón:

    ```sql
    CREATE CERTIFICATE dbm_certificate
        FROM FILE = '/var/opt/mssql/data/dbm_certificate.cer'
        WITH PRIVATE KEY (
        FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
        DECRYPTION BY PASSWORD = '<Private_Key_Password>'
                );
    GO
    ```

### <a name="create-the-database-mirroring-endpoints-on-all-replicas"></a>Az adatbázis tükrözési végpontjainak létrehozása az összes kópián

Futtassa a következő parancsfájlt az összes SQL-példányon SQL CMD vagy SSMS használatával:

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
GO
```

### <a name="create-the-availability-group"></a>A rendelkezésre állási csoport létrehozása

Csatlakozzon ahhoz az SQL Server-példányhoz, amely az elsődleges replikát üzemelteti AZ SQL CMD vagy az SSMS használatával. A rendelkezésre állási csoport létrehozásához futtassa a következő parancsot:

- Cserélje `ag1` le a kívánt rendelkezésre állási csoport nevét.
- Cserélje `<VM1>`le `<VM2>`a `<VM3>` , és az értékeket a replikákat tároló SQL Server-példányok nevére cserélje le.

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
GO
```

### <a name="create-a-sql-server-login-for-pacemaker"></a>SQL Server bejelentkezés létrehozása pacemakerhez

Az összes SQL-kiszolgálón hozzon létre egy SQL-bejelentkezést a Pacemaker számára. A következő Transact-SQL létrehoz egy bejelentkezést.

- Cserélje `<password>` ki a saját összetett jelszavát.

```sql
USE [master]
GO

CREATE LOGIN [pacemakerLogin] with PASSWORD= N'<password>';
GO

ALTER SERVER ROLE [sysadmin] ADD MEMBER [pacemakerLogin];
GO
```

Az összes SQL-kiszolgálón mentse az SQL Server bejelentkezéshez használt hitelesítő adatokat. 

1. A fájl létrehozása:

    ```bash
    sudo vi /var/opt/mssql/secrets/passwd
    ```

1. Adja hozzá a következő 2 sort a fájlhoz:

    ```bash
    pacemakerLogin
    <password>
    ```

    A **vi** szerkesztőből való kilépéshez először nyomja meg `:wq` az **Esc** billentyűt, majd írja be a parancsot a fájl megírásához, majd lépjen ki.

1. A fájl csak gyökéráltal olvashatóvá tehető:

    ```bash
    sudo chown root:root /var/opt/mssql/secrets/passwd
    sudo chmod 400 /var/opt/mssql/secrets/passwd
    ```

### <a name="join-secondary-replicas-to-the-availability-group"></a>Másodlagos replikák csatlakoztatása az elérhetőségi csoporthoz

1. Annak érdekében, hogy csatlakozzon a másodlagos replikák az AG, meg kell nyitnia port 5022 a tűzfalon az összes kiszolgálók. Futtassa a következő parancsot az SSH-munkamenetben:

    ```bash
    sudo firewall-cmd --zone=public --add-port=5022/tcp --permanent
    sudo firewall-cmd --reload
    ```

1. A másodlagos replikákon futtassa a következő parancsokat, hogy csatlakozzon hozzájuk az ag-hez:

    ```sql
    ALTER AVAILABILITY GROUP [ag1] JOIN WITH (CLUSTER_TYPE = EXTERNAL);
    GO

    ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
    GO
    ```

1. Futtassa a következő Transact-SQL parancsfájlt az elsődleges replikán és minden másodlagos replikán:

    ```sql
    GRANT ALTER, CONTROL, VIEW DEFINITION ON AVAILABILITY GROUP::ag1 TO pacemakerLogin;
    GO
    
    GRANT VIEW SERVER STATE TO pacemakerLogin;
    GO
    ```

1. Miután a másodlagos replikák csatlakozott, láthatja őket az SSMS Object Explorer kibontásával a **Mindig magas rendelkezésre állású** csomópont:

    ![availability-group-joined.png](media/sql-server-linux-rhel-ha-stonith-tutorial/availability-group-joined.png)

### <a name="add-a-database-to-the-availability-group"></a>Adatbázis hozzáadása az elérhetőségi csoporthoz

Az adatbázis [hozzáadásáról szóló, a rendelkezésre állási csoport konfigurálásáról szóló cikket](/sql/linux/sql-server-linux-availability-group-configure-ha#add-a-database-to-the-availability-group)fogjuk követni.

Ebben a lépésben a következő Transact-SQL parancsok at használjuk. Futtassa ezeket a parancsokat az elsődleges replika:

```sql
CREATE DATABASE [db1]; -- creates a database named db1
GO

ALTER DATABASE [db1] SET RECOVERY FULL; -- set the database in full recovery mode
GO

BACKUP DATABASE [db1] -- backs up the database to disk
   TO DISK = N'/var/opt/mssql/data/db1.bak';
GO

ALTER AVAILABILITY GROUP [ag1] ADD DATABASE [db1]; -- adds the database db1 to the AG
GO
```

### <a name="verify-that-the-database-is-created-on-the-secondary-servers"></a>Annak ellenőrzése, hogy az adatbázis a másodlagos kiszolgálókon van-e létrehozva

Minden másodlagos SQL Server kópián futtassa a következő lekérdezést, és nézze meg, hogy a db1 adatbázis létrejött-e, és szinkronizált állapotban van-e:

```
SELECT * FROM sys.databases WHERE name = 'db1';
GO
SELECT DB_NAME(database_id) AS 'database', synchronization_state_desc FROM sys.dm_hadr_database_replica_states;
```

Ha `synchronization_state_desc` a lista `db1`szinkronizálva van, az azt jelenti, hogy a kópiák szinkronizálása történik. A másodlagos megjelenítése `db1` az elsődleges replika.

## <a name="create-availability-group-resources-in-the-pacemaker-cluster"></a>Rendelkezésre állási csoport erőforrásainak létrehozása a Pacemaker-fürtben

Mi lesz a következő [útmutatót, hogy hozzon létre a rendelkezésre állási csoport erőforrásait a Pacemaker cluster.](/sql/linux/sql-server-linux-create-availability-group#create-the-availability-group-resources-in-the-pacemaker-cluster-external-only)

### <a name="create-the-ag-cluster-resource"></a>Az AG-fürterőforrás létrehozása

1. Az alábbi paranccsal `ag_cluster` hozza létre `ag1`az erőforrást az elérhetőségi csoportban.

    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s master notify=true
    ```

1. Ellenőrizze az erőforrást, és győződjön meg arról, hogy online állapotban vannak, mielőtt folytatná a következő parancsot:

    ```bash
    sudo pcs resource
    ```

    A következő kimenetnek kell megjelennie:

    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
    Masters: [ <VM1> ]
    Slaves: [ <VM2> <VM3> ]
    ```

### <a name="create-a-virtual-ip-resource"></a>Virtuális IP-erőforrás létrehozása

1. Virtuális IP-erőforrás létrehozásához használjon a hálózatról elérhető statikus IP-címet. A parancseszközzel `nmap`talál egyet.

    ```bash
    nmap -sP <IPRange>
    # For example: nmap -sP 10.0.0.*
    # The above will scan for all IP addresses that are already occupied in the 10.0.0.x space.
    ```

1. A **stonith-kompatibilis** tulajdonság beállítása false értékre

    ```bash
    sudo pcs property set stonith-enabled=false
    ```

1. Hozza létre a virtuális IP-erőforrást a következő paranccsal:

    - Cserélje `<availableIP>` le az alábbi értéket egy nem használt IP-címre.

    ```bash
    sudo pcs resource create virtualip ocf:heartbeat:IPaddr2 ip=<availableIP>
    ```

### <a name="add-constraints"></a>Megkötések hozzáadása

1. Annak érdekében, hogy az IP-cím és az AG erőforrás ugyanazon a csomóponton fusson, egy helymegosztási megkötést kell konfigurálni. Futtassa az alábbi parancsot:

    ```bash
    sudo pcs constraint colocation add virtualip ag_cluster-master INFINITY with-rsc-role=Master
    ```

1. Hozzon létre egy rendelési megkötést annak biztosítására, hogy az AG erőforrás az IP-cím előtt futjon. Míg a helymegosztási megkötés rendelési megkötést jelent, ez azt kényszeríti.

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

### <a name="re-enable-stonith"></a>Stonith újraengedélyezése

Készen állunk a tesztelésre. Engedélyezze újra a stonith-ot a fürtben az 1.

```bash
sudo pcs property set stonith-enabled=true
```

### <a name="check-cluster-status"></a>A fürt állapotának ellenőrzése

A fürterőforrások állapotát a következő paranccsal ellenőrizheti:

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

Annak érdekében, hogy a konfiguráció eddig sikeres volt, teszteljük a feladatátvételt. További információ: [Mindig rendelkezésre állási csoport feladatátvétel Linux on](/sql/linux/sql-server-linux-availability-group-failover-ha).

1. Futtassa a következő parancsot az `<VM2>`elsődleges replika manuális feladatátvételéhez. Cserélje `<VM2>` le a kiszolgálónév értékére.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Ha ismét ellenőrzi a korlátozásokat, látni fogja, hogy egy másik megkötés tadat a manuális feladatátvétel miatt:

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

1. Távolítsa el a kényszert `cli-prefer-ag_cluster-master` az azonosítóval a következő paranccsal:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Ellenőrizze a fürt erőforrásait a paranccsal, `sudo pcs resource`és `<VM2>`látnia kell, hogy az elsődleges példány most van.

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

## <a name="test-fencing"></a>Teszt Vívás

A STONITH-ot a következő parancs futtatásával tesztelheti. Próbálja meg futtatni `<VM1>` az `<VM3>`alábbi parancsot a számára.

```bash
sudo pcs stonith fence <VM3> --debug
```

> [!NOTE]
> Alapértelmezés szerint a kerítés művelet kikapcsolja a csomópontot, majd bekapcsolja. Ha csak a csomópontot szeretné kapcsolat nélküli `--off` módba állítani, használja a parancsban található lehetőséget.

A következő kimenetet kell kapnia:

```output
[<username>@<VM1> ~]$ sudo pcs stonith fence <VM3> --debug
Running: stonith_admin -B <VM3>
Return Value: 0
--Debug Output Start--
--Debug Output End--
 
Node: <VM3> fenced
```
A kerítéseszköz tesztelésével kapcsolatos további információkért tekintse meg a Következő [Red Hat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/s1-stonithtest-haar) cikket.

## <a name="next-steps"></a>További lépések

Annak érdekében, hogy egy rendelkezésre állási csoport figyelő az SQL-kiszolgálók, létre kell hoznia és konfigurálnia kell egy terheléselosztót.

> [!div class="nextstepaction"]
> [Oktatóanyag: Az SQL Server rendelkezésre állási csoportfigyelőjének konfigurálása RHEL virtuális gépeken az Azure-ban](sql-server-linux-rhel-ha-listener-tutorial.md)
