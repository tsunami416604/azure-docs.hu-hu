---
title: "Létrehozása és kezelése a Linux virtuális gépek az Azure parancssori Felülettel |} Microsoft Docs"
description: "Az oktatóanyag - létrehozása és kezelése a Linux virtuális gépek az Azure parancssori felület"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: bef7f6ef13f6d31c16d40deb46f168ae52a9e61b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-linux-vms-with-the-azure-cli"></a>Létrehozása és kezelése a Linux virtuális gépek az Azure parancssori felület

Az Azure virtuális gépek adjon meg egy teljes mértékben konfigurálhatók és rugalmas számítási környezet. Ez az oktatóanyag ismerteti alapszintű Azure-beli virtuális gép telepítési elemek, például Virtuálisgép-méret kiválasztása, Virtuálisgép-lemezkép kiválasztása és a virtuális gépek telepítése. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzon létre, és csatlakoztassa a virtuális Gépet
> * Válassza ki és használja a Virtuálisgép-rendszerképek
> * Megtekintése és használata a megadott Virtuálisgép-méretek
> * Virtuális gép átméretezése
> * Megtekinteni és megérteni a virtuális gép állapota


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ha ez az oktatóanyag van szükség, hogy futnak-e az Azure parancssori felület 2.0.4 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) paranccsal. 

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Egy erőforráscsoportot a virtuális gépek előtt létre kell hozni. Ebben a példában az erőforráscsoport neve *myResourceGroupVM* jön létre a *eastus* régióban. 

```azurecli-interactive 
az group create --name myResourceGroupVM --location eastus
```

Az erőforráscsoport létrehozása vagy módosítása egy virtuális Gépet, amelynek ez az oktatóanyag teljes látható során van megadva.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

A virtuális gép létrehozása a [az virtuális gép létrehozása](https://docs.microsoft.com/cli/azure/vm#az_vm_create) parancsot. 

A virtuális gép létrehozásakor több lehetőség is elérhető, például az operációs rendszer lemezképét, a lemez méretezés és a felügyeleti hitelesítő adatokat. Ebben a példában egy virtuális gép létrehozása nevű *myVM* Ubuntu Server operációs rendszert futtató. 

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM --image UbuntuLTS --generate-ssh-keys
```

A virtuális gép létrehozásához néhány percig is eltarthat. A virtuális gép létrehozása után az Azure parancssori felület exportálja a virtuális gép kapcsolatos információkat. Vegye figyelembe a `publicIpAddress`, ez a cím segítségével fér hozzá a virtuális géphez... 

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM"
}
```

## <a name="connect-to-vm"></a>Csatlakozás virtuális géphez

A virtuális Géphez a SSH az Azure-felhő rendszerhéj vagy a helyi számítógépről is csatlakozhat. Cserélje le a példa IP-cím a `publicIpAddress` az előző lépésben feljegyzett.

```bash
ssh 52.174.34.95
```

Miután bejelentkezett a virtuális Gépet, telepítse, és konfigurálhatja az alkalmazásokat. Ha elkészült, zárja be a szokásos módon SSH-munkamenetet:

```bash
exit
```

## <a name="understand-vm-images"></a>Virtuálisgép-rendszerképek ismertetése

Az Azure piactéren tartalmaz számos rendszerképet virtuális gépek létrehozásához használható. Az előző lépésben a virtuális gép létrehozása egy Ubuntu lemezképpel. Ebben a lépésben az Azure CLI-vel keresése a piactéren CentOS kép, amit a második virtuális gép telepítése.  

A legtöbb listája általánosan használt képek megjelenítéséhez használja a [az vm képlistában](/cli/azure/vm/image#list) parancsot.

```azurecli-interactive 
az vm image list --output table
```

A parancs kimenetét a legnépszerűbb VM-lemezképekkel az Azure ad vissza.

```bash
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

Teljes listája látható hozzáadásával a `--all` argumentum. A képlistában is alapján szűrhetők `--publisher` vagy `–-offer`. Ebben a példában a listában az ajánlat, amely megfelel az összes lemezkép vannak *CentOS*. 

```azurecli-interactive 
az vm image list --offer CentOS --all --output table
```

Részleges kimenete:

```azurecli-interactive 
Offer             Publisher         Sku   Urn                                     Version
----------------  ----------------  ----  --------------------------------------  -----------
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201501         6.5.201501
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201503         6.5.201503
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201506         6.5.201506
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20150904       6.5.20150904
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20160309       6.5.20160309
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20170207       6.5.20170207
```

A virtuális gépek azokba telepítéséhez jegyezze fel az értékét a *Urn* oszlop. A kép megadásakor a lemezkép verziószámát lehet cserélni "legújabb", amely kiválasztja a terjesztési legújabb verzióját. Ebben a példában a `--image` argumentum használatával adja meg a CentOS 6.5-lemezkép legújabb verzióját.  

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM2 --image OpenLogic:CentOS:6.5:latest --generate-ssh-keys
```

## <a name="understand-vm-sizes"></a>Virtuálisgép-méretek ismertetése

A virtuális gép méretét határozza meg a számítási erőforrásokat, a virtuális gép számára elérhető például a CPU, grafikus Processzor és memória mennyisége. Virtuális gépek a várt munkahelyi terhelés méretének kell. Ha növeli a munkaterhelés, egy meglévő virtuális gépet is átméretezhetők.

### <a name="vm-sizes"></a>Virtuálisgép-méretek

A következő táblázat azokat a használati esetek méretek kategorizálja.  

| Típus                     | Méretek           |    Leírás       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Általános célú](sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| Elosztott terhelésű CPU-memória. Épp ezért tökéletes választás a fejlesztői / tesztelői és kis, közepes méretű alkalmazásokhoz és adatokhoz megoldásokhoz.  |
| [Számításra optimalizált](sizes-compute.md)   | FS, F             | Magas CPU-memória. Jó közepes forgalom alkalmazásokat, hálózati berendezéseket és kötegelt folyamatok.        |
| [Memóriaoptimalizált](../virtual-machines-windows-sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Magas memória-core. A relációs adatbázisok, közepes vagy nagyméretű gyorsítótárak és memórián belüli analytics nagy.                 |
| [Tárolásra optimalizált](../virtual-machines-windows-sizes-storage.md)      | Ls                | Magas lemez-adatátviteli és I/O-műveleti jellemzők. Ideális Big Data-, SQL- és NoSQL-adatbázisok esetén.                                                         |
| [GPU](sizes-gpu.md)          | PORTOK HV, NC            | Nagy mennyiségű grafikus megjelenítési és videó szerkesztése szánt speciális virtuális gépeket.       |
| [Magas teljesítmény](sizes-hpc.md) | H, A8-11          | A leghatékonyabb CPU rendelkező virtuális gépek nagy átviteli további hálózati adapterek (RDMA). 


### <a name="find-available-vm-sizes"></a>Elérhető Virtuálisgép-méretek keresése

Virtuálisgép-méretek érhető el az adott listájának megtekintéséhez használja a [az vm-méretek listázása](/cli/azure/vm#list-sizes) parancsot. 

```azurecli-interactive 
az vm list-sizes --location eastus --output table
```

Részleges kimenete:

```azurecli-interactive 
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 2          3584  Standard_DS1                          1           1047552                    7168
                 4          7168  Standard_DS2                          2           1047552                   14336
                 8         14336  Standard_DS3                          4           1047552                   28672
                16         28672  Standard_DS4                          8           1047552                   57344
                 4         14336  Standard_DS11                         2           1047552                   28672
                 8         28672  Standard_DS12                         4           1047552                   57344
                16         57344  Standard_DS13                         8           1047552                  114688
                32        114688  Standard_DS14                        16           1047552                  229376
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
                 4          3584  Standard_A2                           2           1047552                  138240
                 8          7168  Standard_A3                           4           1047552                  291840
                 4         14336  Standard_A5                           2           1047552                  138240
                16         14336  Standard_A4                           8           1047552                  619520
                 8         28672  Standard_A6                           4           1047552                  291840
                16         57344  Standard_A7                           8           1047552                  619520
```

### <a name="create-vm-with-specific-size"></a>Meghatározott méretű virtuális gép létrehozása

Az előző virtuális gép létrehozása a példában a mérete nem adta meg, melyik eredmények alapértelmezett mérete. A Virtuálisgép-méret választható létrehozási ideje az [az virtuális gép létrehozása](/cli/azure/vm#create) és a `--size` argumentum. 

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM3 \
    --image UbuntuLTS \
    --size Standard_F4s \
    --generate-ssh-keys
```

### <a name="resize-a-vm"></a>Virtuális gép átméretezése

A virtuális gépek telepítése után azt növeléséhez vagy csökkentéséhez az erőforrás-elosztás is átméretezhetők. Megtekintheti a virtuális gép és a méretének a jelenlegi [az vm megjelenítése](/cli/azure/vm#show):

```azurecli-interactive
az vm show --resource-group myResourceGroupVM --name myVM --query hardwareProfile.vmSize
```

A virtuális gépek átméretezésével előtt ellenőrizze, hogy a kívánt méretet cím az aktuális Azure fürtön. A [az vm-vm-átméretezési-beállításai](/cli/azure/vm#list-vm-resize-options) parancs méretek listáját adja vissza. 

```azurecli-interactive 
az vm list-vm-resize-options --resource-group myResourceGroupVM --name myVM --query [].name
```
Ha érhető el a kívánt méretet, a virtuális gép átméretezhető az alkalmazás bekapcsolja a állapotból, azonban a művelet során újraindítása után. Használja a [az vm átméretezése]( /cli/azure/vm#resize) az átméretezés végrehajtandó parancs.

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_DS4_v2
```

Ha a kívánt méretet nincs a jelenlegi fürthöz, a virtuális gép kell felszabadítása előtt az átméretezés akkor fordulhat elő. Használja a [az virtuális gép felszabadítása]( /cli/azure/vm#deallocate) parancs használatával állítsa le, és a virtuális gép felszabadítása. Vegye figyelembe, ha a virtuális gép be van kapcsolva vissza, az ideiglenes lemezen lévő adatokhoz eltávolíthat. A nyilvános IP-cím is megváltozik, kivéve, ha a statikus IP-cím használatban van. 

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupVM --name myVM
```

Az átméretezés akkor fordulhat elő, ha felszabadítása. lehetséges. 

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_GS1
```

Az átméretezés után a virtuális gép elindítható.

```azurecli-interactive 
az vm start --resource-group myResourceGroupVM --name myVM
```

## <a name="vm-power-states"></a>Virtuális gép energiaállapotokat

Egy Azure virtuális gép sok energiaállapotát egyike lehet. Ebben az állapotban lévő virtuális gép aktuális állapotát jeleníti meg a hipervizor szempontjából. 

### <a name="power-states"></a>Energiaállapotokat

| Energiaállapot | Leírás
|----|----|
| Indulás alatt | Azt jelzi, hogy a virtuális gép elindul. |
| Fut | Azt jelzi, hogy fut-e a virtuális gép. |
| Leállítás | Azt jelzi, hogy a virtuális gép leáll. | 
| Leállítva | Azt jelzi, hogy a virtuális gép leáll. A leállított állapotban lévő virtuális gépek továbbra is számítási díjakat fel Önnek.  |
| Felszabadítása | Azt jelzi, hogy a virtuális gép felszabadítása alatt. |
| Felszabadítása | Azt jelzi, hogy a virtuális gép-e a hipervizor eltávolították, de a vezérlő vezérlősík továbbra is elérhető. Virtuális gépek Deallocated állapotban nem számítunk költségeket. |
| - | Azt jelzi, hogy a teljesítmény a virtuális gép állapota ismeretlen. |

### <a name="find-power-state"></a>Energiaállapot keresése

Egy adott virtuális gép állapotának lekéréséhez használja a [az virtuálisgép-példányokat tartalmazó nézet beolvasása](/cli/azure/vm#get-instance-view) parancsot. Ne adjon meg egy érvényes nevet a virtuális gép és erőforráscsoportban. 

```azurecli-interactive 
az vm get-instance-view \
    --name myVM \
    --resource-group myResourceGroupVM \
    --query instanceView.statuses[1] --output table
```

Kimenet:

```azurecli-interactive 
ode                DisplayStatus    Level
------------------  ---------------  -------
PowerState/running  VM running       Info
```

## <a name="management-tasks"></a>Felügyeleti feladatok

Életciklusa során a virtuális gépek érdemes lehet indítása, leállítása vagy törlése a virtuális gépek például a felügyeleti feladatok futtatásához. Emellett érdemes lehet ismétlődő vagy összetett feladatok automatizálása parancsfájlok létrehozására. Az Azure parancssori felület használatával, számos gyakori felügyeleti feladatokat is futtatható a parancssorból vagy parancsfájlokban. 

### <a name="get-ip-address"></a>IP-cím beszerzése

Ez a parancs visszaadja a privát és nyilvános IP-címek egy virtuális gép.  

```azurecli-interactive 
az vm list-ip-addresses --resource-group myResourceGroupVM --name myVM --output table
```

### <a name="stop-virtual-machine"></a>Virtuális gép leállítása

```azurecli-interactive 
az vm stop --resource-group myResourceGroupVM --name myVM
```

### <a name="start-virtual-machine"></a>Virtuális gép elindítása

```azurecli-interactive 
az vm start --resource-group myResourceGroupVM --name myVM
```

### <a name="delete-resource-group"></a>Erőforráscsoport törlése

Erőforráscsoport törlésekor a belül, például a virtuális gép virtuális hálózati vagy lemez található összes erőforrást is törlődnek. A `--no-wait` paraméter visszaadja a kérdés nem várja meg a művelet elvégzéséhez. A `--yes` paraméter megerősíti, hogy szeretné-e az erőforrások ehhez egy további kérdés nélkül törli.

```azurecli-interactive 
az group delete --name myResourceGroupVM --no-wait --yes
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóprogramban megismerte alapvető virtuális gép létrehozása és kezelése például hogyan:

> [!div class="checklist"]
> * Hozzon létre, és csatlakoztassa a virtuális Gépet
> * Válassza ki és használja a Virtuálisgép-rendszerképek
> * Megtekintése és használata a megadott Virtuálisgép-méretek
> * Virtuális gép átméretezése
> * Megtekinteni és megérteni a virtuális gép állapota

A következő oktatóanyag további információt a virtuális gépek lemezei továbblépés.  

> [!div class="nextstepaction"]
> [Hozzon létre és kezelheti a virtuális gépek lemezei](./tutorial-manage-disks.md)
