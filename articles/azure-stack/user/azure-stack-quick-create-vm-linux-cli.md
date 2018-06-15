---
title: Linux virtuális gép létrehozása Azure-készletben Azure parancssori felület használatával |} Microsoft Docs
description: Hozzon létre egy Linux virtuális gép CLI Azure-készletben.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 21F7D599-1FEC-4827-A5C3-06495C5F53A4
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 90b36183ba32e75e06d434098d26cb10f3736373
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32156691"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Gyors üzembe helyezés: Linux server virtuális gép létrehozása Azure-készletben Azure parancssori felület használatával

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Létrehozhat egy Ubuntu Server 16.04 LTS virtuális gépet az Azure parancssori felület használatával. Kövesse a cikkben történő létrehozásáról és használatáról a virtuális gép. Ez a cikk is lehetővé teszi a lépéseket:

* Csatlakoztassa a virtuális gép egy távoli ügyfélhez.
* A NGINX-webkiszolgáló telepítéséhez, és az alapértelmezett kezdőlapja a lapnak a megtekintésére.
* Nem használt erőforrások törlése.

## <a name="prerequisites"></a>Előfeltételek

* **A Linux-lemezkép a verem Azure piactéren**

   A verem Azure piactér nem tartalmazza a Linux-lemezkép alapértelmezés szerint. Adja meg az Azure-verem operátorral beolvasása a **Ubuntu Server 16.04 LTS** lemezkép van szüksége. Az operátor használhatja a leírt lépéseket a [Piactéri elemek töltse le az Azure-ból Azure verem](../azure-stack-download-azure-marketplace-item.md) cikk.

* Az Azure verem hozhatja létre és kezelheti az erőforrásokat az Azure parancssori felület adott verziója szükséges. Ha még nem rendelkezik az Azure CLI Azure verem konfigurált, jelentkezzen be a [szoftverfejlesztői készlet](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), vagy egy Windows-alapú külső ügyfél Ha [VPN-en keresztül csatlakozó](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) és kövesse a lépéseket a [ Telepítse és konfigurálja az Azure parancssori felület](azure-stack-version-profiles-azurecli2.md).

* Egy nyilvános SSH-kulcs az a név id_rsa.pub menti a felhasználói profil .ssh könyvtárában. SSH-kulcsok létrehozásával kapcsolatos részletes információkért lásd: [létrehozása SSH kulcsok Windows](../../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Erőforráscsoport egy olyan logikai tároló, ahol telepítheti és kezelheti az Azure-verem erőforrásokat. A szoftverfejlesztői készlet vagy az Azure-verem integrált a rendszer a [az csoport létrehozása](/cli/azure/group#az_group_create) parancs futtatásával hozzon létre egy erőforráscsoportot.

>[!NOTE]
 Példák az összes változót értékek vannak hozzárendelve. Megadhat azonban új értéket, ha szeretné.

Az alábbi példakód létrehozza a helyi helyen contoso.com nevű erőforráscsoport.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

A virtuális gép létrehozása a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) parancsot. Az alábbi példakód létrehozza a myVM nevű virtuális gép. Ez a példa Bemutatofelhasznalo egy rendszergazda felhasználó neve és Demouser@123 , a felhasználó jelszavát. Ezek helyett, amelyet a környezetének megfelelő.

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

A nyilvános IP-cím eredmény abban az esetben a **PublicIpAddress** paraméter. Jegyezze fel a címet, mert van szüksége a virtuális gép hozzáférjen.

## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára

A virtuális gép futtatása az IIS-webkiszolgáló lesz, mert szükség nyissa meg az internetes forgalmat a 80-as porton. A kívánt port megnyitásához használja az [az vm open-port](/cli/azure/vm#open-port) parancsot.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>Az SSH használata a virtuális géphez való kapcsolódáshoz

Az ügyfélszámítógépre telepített SSH csatlakozzon a virtuális géphez. Ha egy Windows ügyfél dolgozik, [Putty](http://www.putty.org/) a VPN-kapcsolat létrehozásához. A virtuális gép kapcsolódni a következő paranccsal:

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>Az NGINX-webkiszolgáló telepítése

Csomag erőforrásokat, és telepítse a legújabb NGINX-csomagot, futtassa a következő parancsfájlt:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Az NGINX kezdőlapjának megtekintése

Az NGINX telepítve, és a virtuális gépen nyissa meg a 80-as porton a webkiszolgálón, a virtuális gép nyilvános IP-cím használatával végezheti el. Nyisson meg egy webböngészőt, és keresse meg a ```http://<public IP address>```.

![NGINX web server kezdőlap](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforrásokat, amelyek többé nem kell törölni. Használhatja a [az csoport törlése](/cli/azure/group#az_group_delete) parancs beírásával távolítsa el ezeket az erőforrásokat. Az erőforráscsoport és az ahhoz tartozó összes erőforrást törli, a következő parancsot:

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

A gyors üzembe helyezés egy webkiszolgáló alapvető Linux-kiszolgáló virtuális gépek telepítette. További információt a verem Azure virtuális gépek, továbbra is [szempontok a virtuális gépek Azure-készletben](azure-stack-vm-considerations.md).
