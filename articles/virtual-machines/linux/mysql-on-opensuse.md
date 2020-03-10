---
title: A MySQL telepítése OpenSUSE rendszerű virtuális gépen az Azure-ban
description: Ismerje meg, hogyan telepítheti a MySQL-t egy OpenSUSE Linux rendszerű virtuális gépre az Azure-ban.
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
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944572"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>A MySQL telepítése az Azure-beli OpenSUSE Linux rendszerű virtuális gépeken

A [MySQL](https://www.mysql.com) egy népszerű, nyílt forráskódú SQL-adatbázis. Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy OpenSUSE Linux rendszerű virtuális gépet, majd telepítse a MySQL-t.


Ha a parancssori felület helyi telepítését és használatát választja, az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>OpenSUSE Linux rendszerű virtuális gép létrehozása

Először hozzon létre egy erőforráscsoportot. Ebben a példában az erőforráscsoport neve *mySQSUSEResourceGroup* , és az *USA keleti* régiójában jön létre.

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Hozza létre a virtuális gépet. Ebben a példában a virtuális gép neve *myVM* , a virtuális gép mérete pedig *Standard_D2s_v3*, de ki kell választania azt a virtuálisgép- [méretet](sizes.md) , amelyet a munkaterheléshez legmegfelelőbbnek tekint.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

Emellett hozzá kell adnia egy szabályt a hálózati biztonsági csoporthoz, hogy engedélyezze a forgalmat a MySQL-hez 3306-es porton keresztül.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

Az SSH használatával csatlakozhat a virtuális géphez. Ebben a példában a virtuális gép nyilvános IP-címe *10.111.112.113*. A virtuális gép létrehozásakor a kimenetben láthatja az IP-címet.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>A virtuális gép frissítése
 
A virtuális géphez való csatlakozás után telepítse a rendszerfrissítéseket és a javításokat. 
   
```bash
sudo zypper update
```

Az utasításokat követve frissítse a virtuális gépet.

## <a name="install-mysql"></a>A MySQL telepítése 


Telepítse a MySQL-t a virtuális gépen SSH-kapcsolaton keresztül. Válaszoljon a megfelelő kérésre.

```bash
sudo zypper install mysql
```
 
Állítsa be a MySQL-t a rendszerindításkor. 

```bash
sudo systemctl enable mysql
```
Ellenőrizze, hogy a MySQL engedélyezve van-e.

```bash
systemctl is-enabled mysql
```

Ezt vissza kell adni: engedélyezve.

Indítsa újra a kiszolgálót.

```bash
sudo reboot
```


## <a name="mysql-password"></a>MySQL-jelszó

A telepítés után a MySQL-gyökér jelszava alapértelmezés szerint üres. Futtassa a **mysql\_secure\_telepítési** parancsfájlt a MySQL védelméhez. A parancsfájl felszólítja a MySQL-gyökér jelszavának módosítására, a névtelen felhasználói fiókok eltávolítására, a távoli rendszergazdai bejelentkezés letiltására, a tesztelési adatbázisok eltávolítására és a jogosultságok tábla újratöltésére. 

A kiszolgáló újraindítása után ismét SSH-t a virtuális gépre.

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>Bejelentkezés a MySQL-be

Most már bejelentkezhet, és megadhatja a MySQL-kérést.

```bash  
mysql -u root -p
```
Ezzel átvált a MySQL-parancssorba, ahol SQL-utasításokat adhat ki az adatbázissal való kommunikációhoz.

Most hozzon létre egy új MySQL-felhasználót.

```sql
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
A Semi-Colon (;) a sor végén elengedhetetlen a parancs befejezése.


## <a name="create-a-database"></a>Adatbázis létrehozása


Hozzon létre egy adatbázist, és adja meg a `mysqluser` felhasználói engedélyeit.

```sql
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Az adatbázis-felhasználóneveket és a jelszavakat csak az adatbázishoz csatlakozó parancsfájlok használják.  Az adatbázis felhasználói fiókjainak nevei nem feltétlenül tükrözik a rendszeren ténylegesen használt felhasználói fiókokat.

Bejelentkezés engedélyezése egy másik számítógépről. Ebben a példában annak a számítógépnek az IP-címe, amely engedélyezi a bejelentkezést, a *10.112.113.114*.

```sql
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
A MySQL-adatbázis felügyeleti segédprogramból való kilépéshez írja be a következőt:

```    
quit
```


## <a name="next-steps"></a>További lépések
A MySQL-vel kapcsolatos részletekért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc).




