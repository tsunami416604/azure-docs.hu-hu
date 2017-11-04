---
title: "Linux virtuális gép eszköz neve megváltozik az Azure-ban hibaelhárítása |} Microsoft Docs"
description: "Azt ismerteti, miért a Linux virtuális gép eszköz neve módosítása és a probléma megoldására."
services: virtual-machines-linux
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: 
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 9b1c9a283e1439c0db5fa37b6601868a7a830ed3
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Linux virtuális gép eszköz neve megváltozik hibaelhárítása

Ez a cikk azt ismerteti, miért eszköz nevének módosítása után indítsa újra a Linux virtuális Gépet, vagy csatlakoztassa újra az adatlemezek. A cikk is megoldást nyújt a probléma.

## <a name="symptoms"></a>Probléma
A következő problémákat tapasztalhat, amikor a Microsoft Azure-ban futó Linux virtuális gépek:

- A virtuális gép rendszerindító újraindítás után sikertelen lesz.
- Adatlemez leválasztása és objektumkörnyezetben, amikor az eszköz neve módosult.
- Egy alkalmazás vagy eszköz neve lemez hivatkozó parancsfájl sikertelen lesz, mivel az eszköz neve megváltozott.

## <a name="cause"></a>Ok

Eszköz elérési utak, a Linux nem garantált konzisztens újraindításainál. Eszköz neve a fő számok (nagybetűvel) és a kisebb számot áll. Ha a Linux tárolási eszközillesztő észlel egy új eszközt, az illesztőprogram rendeli hozzá az fő- és alverzió számok közötti az eszközt. Ha egy eszközt a rendszer eltávolítja, az eszköz számok felszabadítását ismételt felhasználásra.

A probléma oka, hogy lévő Linux scanning eszköz SCSI-alrendszere által ütemezett aszinkron módon történjen. Egy eszköz elérési útját ennek eredményeképpen újraindításainál változhat. 

## <a name="solution"></a>Megoldás

A probléma megoldásához, állandó elnevezési használja. Állandó elnevezési használandó négy módja van: filesystem felirat, UUID, azonosítója vagy elérési úttal. A fájlrendszer címke vagy UUID Azure Linux virtuális gépek használatát javasoljuk. 

A legtöbb terjesztéseket adja meg a `fstab` **nofail** vagy **nobootwait** paraméterek. Ezek a paraméterek engedélyezése a rendszert a Ha a lemez nem indításkor csatlakoztatni. A terjesztési dokumentációjából tájékozódhat ezeket a paramétereket. A Linux virtuális gépek UUID használja, ha hozzá adatlemezt konfigurálásával kapcsolatos további információkért lásd: [a Linux virtuális Gépet az új lemez csatlakoztatásához kapcsolódás](add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk). 

Az Azure Linux-ügynök telepítve van a virtuális gép, ha az ügynök Udev szabályok összeállításához szimbolikus hivatkozásokat tartalmaz a /dev/disk/azure elérési úton található használja. Alkalmazásokhoz és parancsfájlokhoz Udev szabályok segítségével azonosíthatja a lemezek vannak csatolva a virtuális Gépet, valamint a lemez típusát és szabad logikai egységeket.

### <a name="identify-disk-luns"></a>Azonosítsa a logikai lemez

Alkalmazások használhatják a logikai egységek található a csatlakoztatott lemezeket, és a szimbolikus csatolást összeállításához. Az Azure Linux ügynök beállítása a szimbolikus csatolást a logikai egység az eszközök Udev szabályokat tartalmazza:

    $ tree /dev/disk/azure

    /dev/disk/azure
    ├── resource -> ../../sdb
    ├── resource-part1 -> ../../sdb1
    ├── root -> ../../sda
    ├── root-part1 -> ../../sda1
    └── scsi1
        ├── lun0 -> ../../../sdc
        ├── lun0-part1 -> ../../../sdc1
        ├── lun1 -> ../../../sdd
        ├── lun1-part1 -> ../../../sdd1
        ├── lun1-part2 -> ../../../sdd2
        └── lun1-part3 -> ../../../sdd3                                    
                                 
Logikai egység adatait a Linux Vendég fiók használatával lekéri `lsscsi` vagy hasonló eszköz:

       $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

Vendég Logikaiegység-adatok az Azure Storage a partíció adatokat tartalmazó virtuális merevlemez kereséséhez használható az Azure-előfizetés metaadatokkal. Használhatja például a `az` CLI:

    $ az vm show --resource-group testVM --name testVM | jq -r .storageProfile.dataDisks                                        
    [                                                                                                                                                                  
    {                                                                                                                                                                  
    "caching": "None",                                                                                                                                              
      "createOption": "empty",                                                                                                                                         
    "diskSizeGb": 1023,                                                                                                                                             
      "image": null,                                                                                                                                                   
    "lun": 0,                                                                                                                                                        
    "managedDisk": null,                                                                                                                                             
    "name": "testVM-20170619-114353",                                                                                                                    
    "vhd": {                                                                                                                                                          
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-114353.vhd"                                                       
    }                                                                                                                                                              
    },                                                                                                                                                                
    {                                                                                                                                                                   
    "caching": "None",                                                                                                                                               
    "createOption": "empty",                                                                                                                                         
    "diskSizeGb": 512,                                                                                                                                              
    "image": null,                                                                                                                                                   
    "lun": 1,                                                                                                                                                        
    "managedDisk": null,                                                                                                                                             
    "name": "testVM-20170619-121516",                                                                                                                    
    "vhd": {                                                                                                                                                           
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-121516.vhd"                                                       
      }                                                                                                                                                            
      }                                                                                                                                                             
    ]

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Fájlrendszer UUID-k használatával blkid felderítése

Alkalmazásokhoz és parancsfájlokhoz olvassa el a kimenetét `blkid`, vagy hasonló információforrásokat, a /dev elérési út olyan szimbolikus csatolásának összeállításához. A kimenet a UUID-k a virtuális gép és a társított eszköz fájl csatolt összes lemezt a jeleníti meg:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

Az Azure Linux ügynök Udev szabályok összeállításához szimbolikus hivatkozásokat tartalmaz a /dev/disk/azure elérési úton:

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

Alkalmazások hivatkozások segítségével azonosíthatja az erőforrás (rövid élettartamú) lemez és a rendszerindító lemez eszköz. Az Azure alkalmazások felderítésére ezek a partíciók /dev/disk/azure/root-part1 vagy /dev/disk/azure-resource-part1 elérési utakat kell kinéznie.

A partíciókat a `blkid` lista adatlemezt elhelyezve. Alkalmazások karbantartása ezek a partíciók UUID, és használjon elérési futásidőben eszköz nevét:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1

    
### <a name="get-the-latest-azure-storage-rules"></a>A legújabb Azure Storage szabályok lekérése

Ahhoz, hogy a legújabb Azure Storage-szabályok, a következő parancsokat:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>Lásd még:

További információkért tekintse át a következő cikkeket:

- [Ubuntu: UUID használatával](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: Állandó elnevezése](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: UUID-k mit tehetnek meg](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev: Kezelés a modern Linux rendszereken bemutatása](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)

