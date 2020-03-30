---
title: Feladat-visszavétel a vészhelyreállítás során az Azure Site Recovery szolgáltatással | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt a különböző típusú feladat-visszavételi és kikötések figyelembe kell venni, miközben nem sikerült vissza a helyszíni vész-helyreállítás során az Azure Site Recovery szolgáltatás.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: raynew
ms.openlocfilehash: c0eaf28f9aeb4050fd35a6036a53e3e91d00f3eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281833"
---
# <a name="failback-of-vmware-vms-after-disaster-recovery-to-azure"></a>VMware virtuális gépek feladat-visszavétele az Azure-ba történő vészhelyreállítás után

Miután a vész-helyreállítási folyamat részeként feladatátvételt végzett az Azure-ba, visszaveheti a helyszíni helyre. Az Azure Site Recovery két különböző típusú feladat-visszavételt tud: 

- Visszaadott feladat az eredeti helyre 
- Feladat-visszavétel másik helyre

Ha feladatátvételt végzett egy VMware virtuális gépen, akkor a feladat-visszaad-hoz ugyanazt a forrást a helyszíni virtuális gép, ha még mindig létezik. Ebben a forgatókönyvben csak a módosítások replikálódnak vissza. Ezt a forgatókönyvet **eredeti hely-helyreállításnak nevezzük.** Ha a helyszíni virtuális gép nem létezik, a forgatókönyv egy **másik hely helyreállítása.**

> [!NOTE]
> Csak az eredeti vCenter és konfigurációs kiszolgálóra lehet visszaadni. Nem telepíthet új konfigurációs kiszolgálót, és nem lehet visszakapni azt. Emellett nem adhat hozzá új virtuális központot a meglévő konfigurációs kiszolgálóhoz, és feladat-visszavételt az új vCenterbe.

## <a name="original-location-recovery-olr"></a>Eredeti hely helyreállítása (OLR)
Ha úgy dönt, hogy visszaadja a feladatát az eredeti virtuális gépnek, a következő feltételeknek kell teljesülniük:

* Ha a virtuális gépet egy vCenter-kiszolgáló kezeli, akkor a fő cél ESX-gazdagépének hozzáféréssel kell rendelkeznie a virtuális gép adattárához.
* Ha a virtuális gép egy ESX-gazdagépen van, de nem a vCenter kezeli, akkor a virtuális gép merevlemezének egy olyan adattárban kell lennie, amelyhez a fő célgazda hozzáférhet.
* Ha a virtuális gép egy ESX-gazdagépen van, és nem használja a vCentert, akkor az újbóli védelem előtt végre kell végeznie a fő cél ESX-gazdagépének felderítését. Ez akkor is érvényes, ha a fizikai kiszolgálókat is visszaszeretné.
* Visszaveheti a kapcsolatot egy virtuális tárolóhálózatra (vSAN) vagy egy nyers eszközleképezésen (RDM) alapuló lemezre, ha a lemezek már léteznek, és a helyszíni virtuális géphez csatlakoznak.

> [!IMPORTANT]
> Fontos, hogy engedélyezze disk.enableUUID= TRUE, hogy a feladat-visszavétel során az Azure Site Recovery szolgáltatás képes azonosítani az eredeti VMDK a virtuális gépen, amelyre a függőben lévő módosításokat kell írni. Ha ez az érték nincs TRUE értékre állítva, akkor a szolgáltatás megpróbálja azonosítani a megfelelő helyszíni VMDK-t a legjobb erőfeszítés alapján. Ha a megfelelő VMDK nem található, létrehoz egy extra lemezt, és az adatok at kap írva, hogy.

## <a name="alternate-location-recovery-alr"></a>Alternatív hely helyreállítása (ALR)
Ha a helyszíni virtuális gép nem létezik a virtuális gép újbóli védelme előtt, a forgatókönyv et egy alternatív hely-helyreállításnak nevezzük. Az újravédelmi munkafolyamat újra létrehozza a helyszíni virtuális gépet. Ez teljes adatletöltést is eredményez.

* Ha egy másik helyre visszavált, a rendszer visszahelyezi a virtuális gépet ugyanarra az ESX-állomásra, amelyen a fő célkiszolgáló telepítve van. A lemez létrehozásához használt adattár ugyanaz lesz, amelyet a virtuális gép újbóli védelmekor választottak ki.
* Csak egy virtuálisgép-fájlrendszer (VMFS) vagy vSAN adattár feladat-visszavétele. Ha rdm-je van, az újbóli védelem és a feladat-visszavétel nem fog működni.
* Az újravédelem egy nagy kezdeti adatátvitelt foglal magában, amelyet a módosítások követnek. Ez a folyamat azért létezik, mert a virtuális gép nem létezik a helyszínen. A teljes adatokat vissza kell replikálni. Ez az újravédelem is több időt vesz igénybe, mint az eredeti hely helyreállítása.
* Az RDM-alapú lemezeknem adhatók vissza. Csak új virtuálisgép-lemezek (VMDK-k) hozhatók létre a VMFS/vSAN adattárban.

> [!NOTE]
> A fizikai gép, ha feladatátvételt az Azure-ba, csak vmware virtuális gépként lehet visszakerülni. Ez ugyanazt a munkafolyamatot követi, mint az alternatív hely helyreállítása. Győződjön meg arról, hogy legalább egy fő célkiszolgálót és a szükséges ESX/ESXi állomásokat felderíti, amelyeknek vissza kell adnia a feladatátvételt.

## <a name="next-steps"></a>További lépések

A [feladat-visszavételi művelet](vmware-azure-failback.md)végrehajtásához kövesse a lépéseket.

