---
title: A MySQL telepítése OpenSUSE rendszerű virtuális gépen az Azure-ban | Microsoft Docs
description: Ismerje meg, hogyan telepítheti a MySQL-t egy OpenSUSE Linux rendszerű virtuális gépre az Azure-ban.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: cynthn
ms.openlocfilehash: baa52f4a17b06b6927013c88f37d4f2bc24744f3
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876018"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>A MySQL telepítése Azure-ban működő, OpenSUSE Linux rendszerű virtuális gépen

A [MySQL](https://www.mysql.com) egy népszerű, nyílt forráskódú SQL-adatbázis. Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy OpenSUSE Linux rendszerű virtuális gépet, majd telepítse a MySQL-t.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>OpenSUSE Linux rendszerű virtuális gép létrehozása

Először hozzon létre egy erőforráscsoportot. Ebben a példában az erőforráscsoport neve *mySQSUSEResourceGroup* , és az *USA keleti* régiójában jön létre.

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Hozza létre a virtuális gépet. Ebben a példában a virtuális gép neve *myVM* , a virtuális gép mérete pedig *Standard_D2s_v3*, de érdemes kiválasztani a számítási feladathoz legmegfelelőbb virtuálisgép- [méretet](sizes.md) .

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

A telepítés után a MySQL-gyökér jelszava alapértelmezés szerint üres. Futtassa a **MySQL\_biztonságos\_telepítési** szkriptjét a MySQL védelméhez. A parancsfájl felszólítja a MySQL-gyökér jelszavának módosítására, a névtelen felhasználói fiókok eltávolítására, a távoli rendszergazdai bejelentkezés letiltására, a tesztelési adatbázisok eltávolítására és a jogosultságok tábla újratöltésére. 

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


Hozzon létre egy adatbázist, `mysqluser` és adja meg a felhasználói engedélyeket.

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




