---
title: "Windows virtuális gép létrehozása Azure-veremben Azure parancssori felületével |} Microsoft Docs"
description: "Útmutató: Windows virtuális gép létrehozása az Azure-verem Azure parancssori felület használatával"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 196bf4351ebd2bf977102571de385edae6f9612b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-virtual-machine-on-azure-stack-using-azure-cli"></a>Windows virtuális gép létrehozása Azure-veremben Azure parancssori felület használatával

Az Azure CLI segítségével létrehozása és kezelése az Azure-verem erőforrások a parancssorból. Ez az útmutató adatokat Azure parancssori felület használatával a Windows Server 2016-os virtuális gép létrehozása Azure-készletben. A virtuális gép létrehozása után csatlakozzon a távoli asztal, az IIS telepítéséhez, majd az alapértelmezett webhely megtekintésére. 

## <a name="prerequisites"></a>Előfeltételek 

* Győződjön meg arról, hogy az Azure-verem operátor hozzá van adva a "Windows Server 2016" kép a verem Azure piactéren.  

* Az Azure verem hozhatja létre és kezelheti az erőforrásokat az Azure parancssori felület adott verziója szükséges. Ha nem rendelkezik Azure CLI Azure verem konfigurálva, kövesse a lépéseket [telepítése és konfigurálása az Azure parancssori felület](azure-stack-connect-cli.md).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Erőforráscsoport egy olyan logikai tároló mely Azure-verembe erőforrások telepítése és kezelése. A szoftverfejlesztői készlet vagy az Azure-verem integrált a rendszer a [az csoport létrehozása](/cli/azure/group#create) parancs futtatásával hozzon létre egy erőforráscsoportot. A jelen dokumentum összes változók értékeit hozzárendelt igazolnia, használhatja őket, vagy adjon meg másik értéket. Az alábbi példakód létrehozza a helyi helyen contoso.com nevű erőforráscsoport.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális Gépet a [az virtuális gép létrehozása](/cli/azure/vm#create) parancsot. Az alábbi példakód létrehozza a myVM nevű virtuális gép. Ez a példa Bemutatofelhasznalo egy rendszergazda felhasználó neve és Demouser@123 a jelszót. Az értékeket módosítsa a környezetének megfelelően. Ezek az értékek van szükség, ha a virtuális géphez való kapcsolódás.

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

A virtuális gép létrehozásakor, jegyezze fel a *PublicIPAddress* paraméter, amely kimeneti, mert ezzel fogja elérni a virtuális Gépet.
 
## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára

Alapértelmezés szerint csak az RDP-kapcsolatok a Windows rendszerű virtuális gép üzembe helyezett Azure verem számára engedélyezett. Ha ez a virtuális gép webkiszolgáló lesz, meg kell nyitnia a 80-as portot az internet irányából. A kívánt port megnyitásához használja az [az vm open-port](/cli/azure/vm#open-port) parancsot.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez

Használja az alábbi parancsot egy távoli asztali kapcsolat létrehozásához a virtuális géppel. Cserélje le az IP-címet a virtuális gépe nyilvános IP-címére. Ha a rendszer erre kéri, adja meg a virtuális gép létrehozásakor használt hitelesítő adatokat.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Az IIS telepítése a PowerShell-lel

Miután bejelentkezett az Azure-beli virtuális gépre, egyetlen PowerShell-utasítással telepítheti az IIS-t, és engedélyezheti, hogy a helyi tűzfalszabály átengedje a webforgalmat. Nyisson meg egy PowerShell-parancssort, és futtassa a következő parancsot:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Az IIS kezdőlapjának megtekintése

Miután az IIS telepítve lett, és a 80-as port meg van nyitva a virtuális gépen az internet irányából, egy tetszőleges böngésző használatával megtekintheti az alapértelmezett IIS-kezdőlapot. Ügyeljen arra, hogy az alapértelmezett oldalt a fentebb dokumentált nyilvános IP-cím használatával keresse fel. 

![Alapértelmezett IIS-webhely](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png) 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [az group delete](/cli/azure/group#delete) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

A gyors egyszerű Windows rendszerű virtuális gép telepítése után. További információt a verem Azure virtuális gépek, továbbra is [szempontok a virtuális gépek Azure-készletben](azure-stack-vm-considerations.md).
