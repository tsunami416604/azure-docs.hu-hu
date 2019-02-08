---
title: A MySQL telepítése OpenSUSE rendszerű virtuális gépen az Azure-ban |} A Microsoft Docs
description: Ismerje meg a MySQL telepítése OpenSUSE Linux rendszerű virtuális gépen az Azure-ban.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: za-rhoads
ms.openlocfilehash: 06fb4b9c39b773393d7a58bba44f240265e2200f
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55893701"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>A MySQL telepítése Azure-ban működő, OpenSUSE Linux rendszerű virtuális gépen

[MySQL](http://www.mysql.com) népszerű, nyílt forráskódú SQL-adatbázis. Ez az oktatóanyag bemutatja, hogyan lehet OpenSUSE Linux rendszerű virtuális gép létrehozása, majd a MySQL telepítése.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>OpenSUSE Linux rendszerű virtuális gép létrehozása

Először hozzon létre egy erőforráscsoportot. Ebben a példában az erőforráscsoport neve *mySQSUSEResourceGroup* létrehozták, és a *USA keleti Régiójában* régióban.

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

A virtuális gép létrehozásához. Ebben a példában a virtuális gép neve *myVM* és a virtuális gép mérete *Standard_D2s_v3*, de kell kiválasztani a [Virtuálisgép-méret](sizes.md) úgy gondolja, hogy a számítási feladathoz leginkább megfelelő.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

Is hozzá kell egy szabályt a hálózati biztonsági csoporthoz adatforgalom engedélyezéséhez 3306-os porton keresztül a MySQL-hez.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

Csatlakozzon a virtuális Géphez SSH fogja használni. Ebben a példában a virtuális gép nyilvános IP-cím áll *10.111.112.113*. Az IP-címet, a kimenetben láthatja a virtuális gép létrehozásakor.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>A virtuális gép frissítése
 
Miután csatlakozott a virtuális Gépet, telepítse a rendszer frissítéseit és javításait. 
   
```bash
sudo zypper update
```

Kövesse az utasításokat a virtuális gép frissítése.

## <a name="install-mysql"></a>A MySQL telepítése 


A MySQL telepítése a virtuális gép ssh-n keresztül. Válaszoljon a megfelelő utasításokat.

```bash
sudo zypper install mysql
```
 
Állítsa be a MySQL, akkor kezdődik, amikor a rendszer. 

```bash
sudo systemctl enable mysql
```
Győződjön meg arról, hogy engedélyezve van-e a MySQL.

```bash
systemctl is-enabled mysql
```

A kapott: engedélyezve van.

Indítsa újra a kiszolgálót.

```bash
sudo reboot
```


## <a name="mysql-password"></a>MySQL-jelszavát

A telepítés után a MySQL rendszergazdai jelszó alapértelmezés szerint üres. Futtassa a **mysql\_biztonságos\_telepítési** parancsfájl MySQL biztonságossá tételéhez. A parancsfájl kérni fogja, hogy a MySQL rendszergazdai jelszó módosítása, távolítsa el a névtelen felhasználói fiókot, tiltsa le a távoli legfelső szintű bejelentkezési, távolítsa el az adatbázisok vizsgálati és töltse be újra a jogosultságok tábla. 

Ha a kiszolgáló újraindul, ssh a virtuális gép újra.

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>Jelentkezzen be a MySQL-hez

Most jelentkezzen be, és adja meg a MySQL-parancssorban.

```bash  
mysql -u root -p
```
Ez akkor vált, amennyiben a MySQL-parancssorban ahol adhat ki az adatbázis SQL-utasításokkal.

Most hozzon létre egy új MySQL-felhasználót.

```sql
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Pontosvesszővel (;) a sor végén található elengedhetetlen a befejezési a parancsot.


## <a name="create-a-database"></a>Adatbázis létrehozása


Hozzon létre egy adatbázist, és adja meg a `mysqluser` felhasználói engedélyeket.

```sql
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Adatbázis felhasználónevek és jelszavak csak az adatbázishoz csatlakozással parancsfájlok segítségével.  Adatbázis-felhasználói fiókok nevei feltétlenül nem felelnek meg a tényleges felhasználói fiókokat a rendszer.

Jelentkezzen be egy másik számítógépről engedélyezése. Ebben a példában az IP-cím annak a számítógépnek, hogy a bejelentkezési van *10.112.113.114*.

```sql
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Lépjen ki a MySQL-adatbázis felügyeleti segédprogram, írja be:

```    
quit
```


## <a name="next-steps"></a>További lépések
MySQL kapcsolatos részletekért lásd: a [MySQL dokumentációja](http://dev.mysql.com/doc).




