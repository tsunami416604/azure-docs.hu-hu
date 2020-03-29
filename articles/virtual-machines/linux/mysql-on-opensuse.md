---
title: A MySQL telepítése OpenSUSE virtuális gépre az Azure-ban
description: Ismerje meg a MySQL telepítését egy OpenSUSE Linux virtuális gépen az Azure-ban.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 07/11/2018
ms.author: cynthn
ms.openlocfilehash: 0d3f0a61da3654c31c99cfac43c86b081876f700
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944572"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>A MySQL telepítése az Azure-ban OpenSUSE Linux szolgáltatást futtató virtuális gépre

[MySQL](https://www.mysql.com) egy népszerű, nyílt forráskódú SQL adatbázis. Ez a bemutató bemutatja, hogyan lehet létrehozni egy virtuális gépet futó OpenSUSE Linux, majd telepítse a MySQL.


Ha a parancssori felület helyi telepítését és használatát választja, az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>OpenSUSE Linux-szal működő virtuális gép létrehozása

Először hozzon létre egy erőforráscsoportot. Ebben a példában az erőforráscsoport neve *mySQSUSEResourceGroup,* és az *USA keleti régiójában* jön létre.

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Hozza létre a virtuális gép. Ebben a példában a virtuális gép neve *myVM,* és a virtuális gép mérete *Standard_D2s_v3,* de válassza ki a [virtuális gép mérete](sizes.md) úgy gondolja, a legmegfelelőbb a számítási feladatokhoz.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

A mySQL 3306-os porton keresztüli forgalom engedélyezéséhez is hozzá kell adnia egy szabályt a hálózati biztonsági csoporthoz.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

Az SSH használatával csatlakozhat a virtuális géphez. Ebben a példában a virtuális gép nyilvános IP-címe *10.111.112.113.* Láthatja az IP-címet a kimenetben, amikor létrehozta a virtuális gép.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>A virtuális gép frissítése
 
Miután csatlakozott a virtuális géphez, telepítse a rendszerfrissítéseket és javításokat. 
   
```bash
sudo zypper update
```

Kövesse az utasításokat a virtuális gép frissítéséhez.

## <a name="install-mysql"></a>A MySQL telepítése 


Telepítse a MySQL-t a virtuális gépre SSH-n keresztül. Adott esetben válaszoljon az utasításokra.

```bash
sudo zypper install mysql
```
 
Állítsa be a MySQL indításához, amikor a rendszer elindul. 

```bash
sudo systemctl enable mysql
```
Ellenőrizze, hogy a MySQL engedélyezve van-e.

```bash
systemctl is-enabled mysql
```

Ennek a következőértéket kell visszaadnia: engedélyezve.

Indítsa újra a kiszolgálót.

```bash
sudo reboot
```


## <a name="mysql-password"></a>MySQL jelszó

A telepítés után a MySQL gyökérjelszó alapértelmezés szerint üres. Futtassa a **mysql\_biztonságos\_telepítési** parancsfájlt a MySQL védelméhez. A parancsfájl kéri, hogy módosítsa a MySQL root jelszót, távolítsa el a névtelen felhasználói fiókokat, tiltsa le a távoli root bejelentkezést, távolítsa el a tesztadatbázisokat, és töltse be újra a jogosultságok táblát. 

Miután a kiszolgáló újraindul, ssh a virtuális gép újra.

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>Bejelentkezés a MySQL-be

Most már bejelentkezhet, és megadhatja a MySQL-kérdést.

```bash  
mysql -u root -p
```
Ez átkapcsolja a MySQL-parancssorba, ahol SQL-utasításokat adhat ki az adatbázissal való interakcióhoz.

Most hozzon létre egy új MySQL felhasználót.

```sql
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
A pontosvessző (;) végén a sor elengedhetetlen a parancs befejezéséhez.


## <a name="create-a-database"></a>Adatbázis létrehozása


Hozzon létre egy `mysqluser` adatbázist, és adja meg a felhasználói engedélyeket.

```sql
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Az adatbázis felhasználói nevét és jelszavát csak az adatbázishoz csatlakozó parancsfájlok használják.  Az adatbázis felhasználói fióknevei nem feltétlenül jelentenek tényleges felhasználói fiókokat a rendszerben.

Bejelentkezés engedélyezése másik számítógépről. Ebben a példában a bejelentkezést engedélyező számítógép IP-címe: *10.112.113.114*.

```sql
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
A MySQL adatbázis-felügyeleti segédprogramból való kilépéshez írja be a következőt:

```    
quit
```


## <a name="next-steps"></a>További lépések
A MySQL-ről a [MySQL dokumentációjában olvashat részletesen.](https://dev.mysql.com/doc)




