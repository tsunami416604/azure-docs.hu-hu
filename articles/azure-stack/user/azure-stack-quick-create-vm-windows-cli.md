---
title: Windows virtuális gép létrehozása Azure-veremben Azure parancssori felületével |} Microsoft Docs
description: 'Útmutató: Windows virtuális gép létrehozása az Azure-verem Azure parancssori felület használatával'
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: E26B246E-811D-44C9-9BA6-2B3CE5B62E83
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/23/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 381c1c37b0675d97adc058979a5d9b5c4fd2cc8b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32155156"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Gyors üzembe helyezés: Windows Server virtuális gép létrehozása Azure-készletben Azure parancssori felület használatával

‎*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A Windows Server 2016-os virtuális gép az Azure parancssori felület használatával hozhat létre. Kövesse a cikkben történő létrehozásáról és használatáról a virtuális gép. Ez a cikk is lehetővé teszi az alábbi lépéseket:

* Csatlakoztassa a virtuális gép egy távoli ügyfélhez.
* Az IIS-webkiszolgáló telepítéséhez, és az alapértelmezett kezdőlapja a lapnak a megtekintésére.
* Az erőforrások törlése.

## <a name="prerequisites"></a>Előfeltételek

* Győződjön meg arról, hogy az Azure-verem operátor adja hozzá a **Windows Server 2016** az Azure-verem Piactéri lemezképet.

* Az Azure verem hozhatja létre és kezelheti az erőforrásokat az Azure parancssori felület adott verziója szükséges. Ha nem rendelkezik Azure CLI Azure verem konfigurálva, kövesse a lépéseket [telepítése és konfigurálása az Azure parancssori felület](azure-stack-version-profiles-azurecli2.md).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Erőforráscsoport egy olyan logikai tároló, ahol telepítheti és kezelheti az Azure-verem erőforrásokat. Az Azure-verem környezethez, futtassa a [az csoport létrehozása](/cli/azure/group#az_group_create) parancs futtatásával hozzon létre egy erőforráscsoportot.

>[!NOTE]
 Példák az összes változót értékek vannak hozzárendelve. Megadhat azonban új értéket, ha szeretné.

Az alábbi példakód létrehozza a helyi helyen contoso.com nevű erőforráscsoport.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gép (VM) használatával a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) parancsot. Az alábbi példakód létrehozza a myVM nevű virtuális gép. Ez a példa Bemutatofelhasznalo egy rendszergazda felhasználó neve és Demouser@123 , a felhasználó jelszavát. Ezek helyett, amelyet a környezetének megfelelő.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

A virtuális gép létrehozásakor a **PublicIPAddress** paraméter a kimenet tartalmazza a nyilvános IP-címet a virtuális géphez. Jegyezze fel a címet, mert van szüksége a virtuális gép hozzáférjen.

## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára

Ez a virtuális gép futtatása az IIS-webkiszolgáló lesz, mert szükség nyissa meg az internetes forgalmat a 80-as porton.

Használja a [az vm-port megnyitása](/cli/azure/vm#open-port) parancs futtatásával nyissa meg a 80-as porton.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez

A következő parancs segítségével a virtuális gép távoli asztali kapcsolat létrehozása. "Nyilvános IP-cím" cserélje le a virtuális gép IP-címét. Amikor a rendszer kéri, adja meg a felhasználónevet és jelszót, amely a virtuális gép használja.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Az IIS telepítése a PowerShell-lel

Most, hogy a virtuális gép már bejelentkezett, a PowerShell használatával telepítse az IIS szolgáltatást. Indítsa el a Powershellt a virtuális gépen, és futtassa a következő parancsot:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Az IIS kezdőlapjának megtekintése

Az Ön által választott webböngésző segítségével az alapértelmezett IIS üdvözli a lapnak a megtekintésére. A nyilvános IP-cím, az előző szakaszban leírt segítségével látogasson el az alapértelmezett lapon.

![Alapértelmezett IIS-webhely](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforrásokat, amelyek többé nem kell törölni. Használja a [az csoport törlése](/cli/azure/group#az_group_delete) parancsot a távolítsa el az erőforráscsoportot, a virtuális gépet, és az összes kapcsolódó erőforrásokat.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

A gyors üzembe helyezés a Windows Server alapvető virtuálisgép telepítette. További információt a verem Azure virtuális gépek, továbbra is [szempontok a virtuális gépek Azure-készletben](azure-stack-vm-considerations.md).
