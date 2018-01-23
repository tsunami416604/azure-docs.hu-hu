---
title: "MySQL telepíthető egy OpenSUSE Azure-ban |} Microsoft Docs"
description: "Ismerje meg, MySQL telepítsen egy OpenSUSE Linux VMirtual gépet az Azure-ban."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: cynthn
ms.openlocfilehash: 88bd895cb3a384f1ada0394fe2da206aca86b981
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>A MySQL telepítése Azure-ban működő, OpenSUSE Linux rendszerű virtuális gépen

[MySQL](http://www.mysql.com) egy népszerű, nyílt forráskódú SQL-adatbázis. Ez az oktatóanyag bemutatja, hogyan OpenSUSE Linuxot futtató virtuális gép létrehozása, majd telepítse a MySQL.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha telepítése és a parancssori felület helyileg használata mellett dönt, az Azure parancssori felület verzió szüksége 2.0-s vagy újabb. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>OpenSUSE Linuxot futtató virtuális gép létrehozása

Először hozzon létre egy erőforráscsoportot. Ebben a példában az erőforráscsoport elnevezési azt *mySQSUSEResourceGroup* hozza létre azt a *USA keleti régiója* régióban.

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

A virtuális gép létrehozása. Ebben a példában a virtuális gép jelenleg elnevezési *myVM*. Is fogjuk használni egy Virtuálisgép-méretet *Standard_D2s_v3*, de kell kiválasztani a [Virtuálisgép-méretet](sizes.md) úgy gondolja, hogy a munkaterhelés számára leginkább megfelelő.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

Szükség szabályokat adhat hozzá a hálózati biztonsági csoport forgalmának engedélyezésére MySQL 3306 porton keresztül.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

Csatlakoztassa a virtuális Gépet SSH fogja használni. Ebben a példában a virtuális gép nyilvános IP-címe van *10.111.112.113*. Az IP-cím, a kimenetben látható a virtuális gép létrehozása után.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>A virtuális gép frissítése
 
Miután a virtuális gép csatlakozik, telepítse a rendszer frissítések és javítások. 
   
```bash
sudo zypper update
```

Kövesse az utasításokat a virtuális gép frissítése.

## <a name="install-mysql"></a>A MySQL telepítése 


A MySQL telepítse a virtuális gép SSH-n keresztül. Válaszoljon a megfelelő utasításokat.

```bash
sudo zypper install mysql
```
 
Állítsa be a MySQL induljon indul el, a rendszer. 

```bash
sudo systemctl enable mysql
```
Győződjön meg arról, hogy engedélyezve van-e a MySQL.

```bash
systemctl is-enabled mysql
```

Ez kell visszaadnia: engedélyezve van.


## <a name="mysql-password"></a>MySQL-jelszó

A telepítés után a MySQL gyökér szintű jelszó alapértelmezés szerint üres. Futtassa a **mysql\_biztonságos\_telepítési** parancsfájl MySQL biztonságossá tételéhez. A parancsprogram kéri a MySQL gyökér szintű jelszó módosítása, távolítsa el a névtelen felhasználói fiókok, tiltsa le a távoli legfelső szintű bejelentkezéseket, távolítsa el az adatbázisok vizsgálati és töltse be újra a jogosultságok tábla. 


```bash
mysql_secure_installation
```

## <a name="log-in-to-mysql"></a>Jelentkezzen be a MySQL

Ezután jelentkezzen be, és adja meg a MySQL-parancssorba.

```bash  
mysql -u root -p
```
Ez vált, akkor a MySQL-parancssorba ahol kommunikál az adatbázis SQL-utasításokat adhatnak ki.

Most hozzon létre egy új MySQL-felhasználó.

```   
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
A pontosvessző (;) a sor végén elengedhetetlen a befejezési a parancsot.


## <a name="create-a-database"></a>Adatbázis létrehozása


Hozzon létre egy adatbázist, és adja meg a `mysqluser` felhasználói engedélyek.

```   
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Adatbázis felhasználónevek és jelszavak csak az adatbázishoz való kapcsolódás parancsfájlok által használt.  Adatbázis-felhasználói fiók neve nem feltétlenül jelenti azt, a rendszer a tényleges felhasználói fiókokat.

Egy másik számítógépről bejelentkezés engedélyezése. Ebben a példában a számítógépen, amely szeretnénk jelentkezzen be az IP-címe van *10.112.113.114*.

```   
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Lépjen ki a MySQL-adatbázis felügyeleti segédprogram, írja be:

```    
quit
```


## <a name="next-steps"></a>További lépések
MySQL kapcsolatos részletekért lásd: a [MySQL dokumentáció](http://dev.mysql.com/doc/index-topic.html).




