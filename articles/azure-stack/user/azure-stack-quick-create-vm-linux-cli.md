---
title: "Linux virtuális gép létrehozása Azure-készletben Azure parancssori felület használatával |} Microsoft Docs"
description: "Hozzon létre egy Linux virtuális gép CLI Azure-készletben."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 21F7D599-1FEC-4827-A5C3-06495C5F53A4
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: ea0bc72c03c7c51f79b838493eb2f6d3efe4f8f7
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="create-a-linux-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Linux virtuális gép létrehozása Azure-készletben Azure parancssori felület használatával

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Az Azure CLI segítségével létrehozása és kezelése az Azure-verem erőforrások a parancssorból. A gyors üzembe helyezés adatokat az Azure parancssori felület használatával egy Linux virtuális gép létrehozása Azure-készletben.  A virtuális gép létrehozása után a webkiszolgálón telepítve van, és a 80-as port meg van nyitva, hogy a webes forgalom.

## <a name="prerequisites"></a>Előfeltételek 

* Győződjön meg arról, hogy az Azure-verem operátor hozzá van adva a "Ubuntu Server 16.04 LTS" kép a verem Azure piactéren. 

* Az Azure verem hozhatja létre és kezelheti az erőforrásokat az Azure parancssori felület adott verziója szükséges. Ha még nem rendelkezik Azure CLI Azure verem konfigurált, jelentkezzen be a [szoftverfejlesztői készlet](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), vagy egy Windows-alapú külső ügyfél Ha [VPN-en keresztül csatlakozó](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) és kövesse a lépéseket a [telepítése és konfigurálja az Azure parancssori felület](azure-stack-connect-cli.md).

* A név id_rsa.pub a nyilvános SSH-kulcs a Windows-felhasználói profil .ssh könyvtárban kell létrehozni. Az SSH-kulcsok létrehozásának részletes információkért lásd: [létrehozása SSH kulcsok Windows](../../virtual-machines/linux/ssh-from-windows.md). 

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
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

Művelet befejeződése után a parancs paramétereit a virtuális gép lesz kimeneti.  Jegyezze fel a *PublicIPAddress*, mivel ez csatlakozási és kezelési a virtuális gép használja.

## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára

Alapértelmezés szerint kizárólag SSH-kapcsolatok engedélyezettek az Azure-ban üzembe helyezett, Linux rendszerű virtuális gépeken. Ha ez a virtuális gép webkiszolgáló lesz, meg kell nyitnia a 80-as portot az internet irányából. A kívánt port megnyitásához használja az [az vm open-port](/cli/azure/vm#open-port) parancsot.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>Bejelentkezés a virtuális gépre SSH-val

Ha olyan rendszert használ, amelyre telepítve van az SSH, a következő paranccsal csatlakozhat a virtuális géphez. Ha Windows rendszert használ, a [Putty](http://www.putty.org/) segítségével hozhatja létre a kapcsolatot. Ügyeljen arra, hogy cserélje le a megfelelő nyilvános IP-címet a virtuális gép. A fenti példában az IP-cím lett 192.168.102.36.

```bash
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>Az NGINX telepítése

Az alábbi bash-parancsfájl segítségével frissítse a csomagforrásokat, és telepítse a legújabb NGINX-csomagot. 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Az NGINX kezdőlapjának megtekintése

Most, hogy az NGINX telepítve van, és a 80-as port meg van nyitva a virtuális gépén az internet irányából, tetszőleges böngészőt használhat az alapértelmezett NGINX-kezdőlap megtekintéséhez. Ügyeljen arra, hogy az alapértelmezett oldalt a fentebb dokumentált *publicIPAddress* használatával keresse fel. 

![Alapértelmezett NGINX-webhely](./media/azure-stack-quick-create-vm-linux-cli/nginx.png) 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [az group delete](/cli/azure/group#delete) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezés egy egyszerű Linux virtuális gép telepítése után. További információt a verem Azure virtuális gépek, továbbra is [szempontok a virtuális gépek Azure-készletben](azure-stack-vm-considerations.md).

