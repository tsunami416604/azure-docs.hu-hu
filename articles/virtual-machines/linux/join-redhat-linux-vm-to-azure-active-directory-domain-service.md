---
title: Egy RedHat Linux virtuális gép csatlakoztatása az Azure Active Directory Tartományi |} Microsoft Docs
description: Hogyan lehet egy Azure Active Directory tartományi szolgáltatások RedHat Enterprise Linux 7 meglévő virtuális csatlakoztatni.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2016
ms.author: v-livech
ms.openlocfilehash: 68971372a3c9d9671ccad43db8d7b543d286fc7a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="join-a-redhat-linux-vm-to-an-azure-active-directory-domain-service"></a>Egy RedHat Linux virtuális gép csatlakoztatása az Azure Active Directory tartományi szolgáltatások

Ez a cikk bemutatja, hogyan Red Hat Enterprise Linux (RHEL) 7 virtuális gép csatlakoztatása az Azure Active Directory tartományi szolgáltatások (AADDS) által felügyelt tartományokhoz.  Követelmények:

- [egy Azure-fiók](https://azure.microsoft.com/pricing/free-trial/)

- [SSH nyilvános- és titkoskulcs-fájlok](mac-create-ssh-keys.md)

- [egy Azure Active Directory tartományi szolgáltatások tartományvezérlő](../../active-directory-domain-services/active-directory-ds-getting-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Gyors parancsok

_Cserélje le olyan saját beállításaival._

### <a name="switch-the-azure-cli-to-classic-deployment-mode"></a>Az azure cli átválthat a klasszikus üzembe helyezési mód

```azurecli
azure config mode asm
```

### <a name="search-for-a-rhel-version-and-image"></a>Egy RHEL verziója és a lemezkép keresése

```azurecli
azure vm image list | grep "Red Hat"
```

### <a name="create-a-redhat-linux-vm"></a>Egy Redhat Linux virtuális gép létrehozása

```azurecli
azure vm create myVM \
-o a879bbefc56a43abb0ce65052aac09f3__RHEL_7_2_Standard_Azure_RHUI-20161026220742 \
-g ahmet \
-p myPassword \
-e 22 \
-t "~/.ssh/id_rsa.pub" \
-z "Small" \
-l "West US"
```

### <a name="ssh-to-the-vm"></a>SSH–kapcsolat létrehozása a virtuális géppel

```bash
ssh -i ~/.ssh/id_rsa ahmet@myVM
```

### <a name="update-yum-packages"></a>YUM-csomagok frissítése

```bash
sudo yum update
```

### <a name="install-packages-needed"></a>Szükséges csomagok telepítése

```bash
sudo yum -y install realmd sssd krb5-workstation krb5-libs
```

Most, hogy a szükséges csomagokat a Linux virtuális gépek vannak telepítve, a következő feladat a virtuális gép csatlakoztatása felügyelt tartományhoz.

### <a name="discover-the-aad-domain-services-managed-domain"></a>Az AAD tartományi szolgáltatásokra által kezelt tartomány felderítése

```bash
sudo realm discover mydomain.com
```

### <a name="initialize-kerberos"></a>Kerberos inicializálása

Adjon meg egy felhasználót, aki a "AAD DC rendszergazdák" csoportba tartozik. Csak ezek a felhasználók kapcsolódhatnak a számítógépek a felügyelt tartományra.

```bash
kinit ahmet@mydomain.com
```

### <a name="join-the-machine-to-the-domain"></a>A számítógép csatlakoztatása a tartományhoz

```bash
sudo realm join --verbose mydomain.com -U 'ahmet@mydomain.com'
```

### <a name="verify-the-machine-is-joined-to-the-domain"></a>Ellenőrizze, hogy a számítógép csatlakozik a tartományhoz

```bash
ssh -l ahmet@mydomain.com mydomain.cloudapp.net
```

## <a name="next-steps"></a>További lépések

* [Red Hat frissítési infrastruktúra (RHUI) igény Red Hat Enterprise Linux virtuális gépek Azure-ban](update-infrastructure-redhat.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [A virtuális gépek az Azure Resource Manager Key Vault beállítása](key-vault-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Központi telepítése és virtuális gépek kezelése az Azure Resource Manager-sablonok és az Azure parancssori felület használatával](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
