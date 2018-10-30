---
title: Feladat-visszavétel az Azure Site Recovery a vészhelyreállítás során |} A Microsoft Docs
description: Ez a cikk áttekintést különféle típusú feladat-visszavétel és figyelmeztetések irányuló vissza a helyszíni az Azure Site Recovery szolgáltatással vészhelyreállítás során figyelembe kell venni.
services: site-recovery
author: rajani-janaki-ram
manager: guaravd
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: rajanki
ms.openlocfilehash: e97d736e7e117e0ccce6dc021a9de2ea15ac5484
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50210142"
---
# <a name="overview-of-failback"></a>Feladat-visszavétel áttekintése

Miután Ön rendelkezik a feladatátvétel az Azure-bA a vész-helyreállítási folyamat részeként is visszaadja a feladatokat a helyszíni helyre. Van két különböző típusú feladat-visszavétel az Azure Site Recovery lehetséges: 

- A feladat-visszavételhez az eredeti helyre 
- A feladat-visszavételhez egy másik helyre

Ha VMware virtuális gépek feladatátvétele, is visszaadja a feladatokat a azonos forrás a helyszíni virtuális gép Ha továbbra is létezik. Ebben a forgatókönyvben csak replikálja a módosításokat vissza. Ez a forgatókönyv más néven **eredeti helyre történő helyreállítás**. A helyszíni virtuális gép nem létezik, a forgatókönyv-e egy **eredetitől eltérő helyre**.

> [!NOTE]
> Csak a feladat-visszavétel az eredeti vCenter és a konfigurációs kiszolgálót is. Nem lehet új konfigurációs kiszolgáló üzembe helyezése, és sikertelen, vissza használja azt. Ezenkívül nem adhat új vCenter a meglévő konfigurációs kiszolgáló és a feladat-visszavétel be a vcenter-kiszolgáló.

## <a name="original-location-recovery-olr"></a>Eredeti helyre történő helyreállítás (való)
Ha a feladat-visszavételhez az eredeti virtuális gépet választja, a következő feltételeknek kell teljesülniük:

* Ha a virtuális gép vCenter-kiszolgáló által felügyelt, a fő célkiszolgáló ESX-gazdagépen a virtuális gép adattárolója hozzáféréssel kell rendelkeznie.
* Ha a virtuális gépet az ESX-gazdagépen van, de nem felügyeli a vCenter, majd a virtuális gép merevlemezének olyan adattárolón a fő cél gazdagépet elérő kell lennie.
* Ha a virtuális gépet az ESX-gazdagépen van, és nem használ vCenter, el kell végeznie a fő célkiszolgáló ESX gazdagép felderítését, az ismételt védelem előtt. Ez vonatkozik, ha fizikai kiszolgálók túl.
* Visszaadhatja a virtuális tárolóhálózat (vSAN) és a egy lemezt, amely alapján a mapping (RDM), ha a lemezek már létezik, és a helyszíni virtuális géphez csatlakoztatott nyers eszköz.

> [!IMPORTANT]
> Fontos disk.enableUUID= igaz engedélyezése, hogy a feladat-visszavétel során az Azure Site Recovery szolgáltatás nem lehet azonosítani a virtuális gépen, amelyre a függőben lévő módosítások lesz írva az eredeti VMDK. Ha ez az érték értéke nem lehet igaz, akkor a szolgáltatás megpróbálja a megfelelő helyi VMDK az elérhető legjobb lehetőség alapján azonosíthatja. Ha nem található a megfelelő VMDK, egy további lemezt hoz létre, és az adatok beolvasása írt be, amely.

## <a name="alternate-location-recovery-alr"></a>Másik helyre történő helyreállítást (ALR)
Ha a helyszíni virtuális gép nem létezik a virtuális gép ismételt védelme előtt, a forgatókönyv neve egy másodlagos hely helyreállítását. A védelem-újrabeállítás munkafolyamat újra létrehozza a helyszíni virtuális gépet. Ennek hatására a teljes adatok letöltését.

* Visszaadja a feladatokat egy másik helyre, ha a helyreállítás a virtuális gépet az ESX-gazdagépre, amelyre telepítve van a fő célkiszolgáló helyén helyreállíthatók. Az adattárral, amellyel létrehozza a lemezt a virtuális gép ismételt védelme kiválasztott adattárolóba lesz.
* Sikertelen lehet vissza csak egy virtuális gép fájlrendszer (VMFS) vagy a vSAN-adattárhoz. Ha egy RDM, védelem-újrabeállítást és a feladat-visszavétel nem működik.
* Az ismételt védelem magában foglalja egy nagy kezdeti adatátvitelt, amely követi a módosításokat. Ez a folyamat létezik, mert a virtuális gép nem létezik a helyszínen. A teljes adatokat vissza kell replikálni rendelkezik. A védelem-újrabeállításba is, mint az eredeti helyre történő helyreállítás több időt vesz igénybe.
* Nem utasíthat el vissza a lemezeket RDM-alapú. Csak új virtuális gép lemezeinek (vmdk-inak) is létrehozható egy VMFS és vsan-hoz az adattárban.

> [!NOTE]
> Az Azure-ba irányuló feladatátvételkor egy fizikai gép is lehet nem sikerült biztonsági másolatot csak a VMware virtuális gépként. Ez a következő ugyanabban a munkafolyamatban, a helyreállítás a másik helyen. Győződjön meg arról, hogy legalább egy fő célkiszolgáló és a szükséges ESX/ESXi-gazdagépek, amelyhez vissza szeretné felderíteni.

## <a name="next-steps"></a>További lépések

A lépések végrehajtásához kövesse a [feladat-visszavételi műveletet](vmware-azure-failback.md).

