---
title: A Hyper-V áttelepítéstámogatása az Azure Áttelepítésben
description: Ismerje meg a Hyper-V-áttelepítés támogatását az Azure Migrate szolgáltatással.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 1eab96df7ee58a8170f75b41c5a2a06f033ced19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245823"
---
# <a name="support-matrix-for-hyper-v-migration"></a>A Hyper-V áttelepítés támogatási mátrixa

Ez a cikk összefoglalja a hyper-v virtuális gépek [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) szolgáltatással történő áttelepítésének támogatási beállításait és korlátait. Ha a Hyper-V virtuális gépek Azure-ba való áttelepítésére vonatkozó értékelésének értékelésére vonatkozó információkat keres, tekintse át az [értékelési támogatási mátrixot.](migrate-support-matrix-hyper-v.md)

## <a name="migration-limitations"></a>A migrálásra vonatkozó korlátozások

Egyszerre legfeljebb 10 virtuális gépet választhat ki a replikációhoz. Ha több gépet szeretne áttelepíteni, replikálja a 10-es csoportokban.


## <a name="hyper-v-hosts"></a>Hyper-V gazdagépek

| **Támogatás**                | **Részletek**               
| :-------------------       | :------------------- |
| **Környezet**       | A Hyper-V állomás lehet önálló vagy fürtben telepített. <br/>Az Azure Migrate replikációs szoftvert (Hyper-V replikációs szolgáltató) telepíteni kell a Hyper-V gazdagépeken.|
| **Engedélyek**           | Rendszergazdai engedélyekre van szükség a Hyper-V gazdagépen. |
| **Gazda operációs rendszer** | Windows Server 2019, Windows Server 2016 vagy Windows Server 2012 R2. |
| **URL-hozzáférés** | A Hyper-V állomások replikációs szolgáltatószoftverének hozzá kell férnie ezekhez az URL-címekhez:<br/><br/> - login.microsoftonline.com: Hozzáférés-vezérlés és identitáskezelés az Active Directory használatával.<br/><br/> - *.backup.windowsazure.com: Replikációs adatátvitel és koordináció. Szolgáltatás URL-címének áttelepítése.<br/><br/> - *.blob.core.windows.net: Adatok feltöltése tárfiókokba.<br/><br/> - dc.services.visualstudio.com: Feltöltése alkalmazás naplók használt belső ellenőrzés.<br/><br/> - time.windows.com: Ellenőrzi a rendszer és a globális idő közötti időszinkronizálást.
| **Port-hozzáférés** |  Kimenő kapcsolatok a 443-as HTTPS-porton a virtuális gép replikációs adatainak küldéséhez.

## <a name="hyper-v-vms"></a>Hyper-V virtuális gépek

| **Támogatás**                  | **Részletek**               
| :----------------------------- | :------------------- |
| **Operációs rendszer** | Az Azure által támogatott összes [Windows-](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) és [Linux-operációs](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) rendszer. |
| **Az Azure szükséges módosításai** | Egyes virtuális gépek szükség lehet a módosításokat, hogy azok az Azure-ban futtatható. Az áttelepítés előtt manuálisan kell elvégeznie a módosításokat. A vonatkozó cikkek utasításokat tartalmaznak erre vonatkozóan. |
| **Linux rendszerindítás**                 | Ha a /boot egy dedikált partíción található, akkor az operációs rendszer lemezén kell lennie, és nem szabad több lemezre osztani.<br/> Ha a /boot a gyökérpartíció (/) része, akkor a '/' partíciónak az operációs rendszer lemezén kell lennie, és nem kell más lemezekre is kiterjednie. |
| **UEFI rendszerindítás**                  | Az azure-ban áttelepített virtuális gép automatikusan bios-rendszerindítási virtuális gépté alakul át. A virtuális gépnek csak Windows Server 2012-es és újabb rendszert kell futtatnia. Az operációs rendszer lemezének legfeljebb öt partícióval vagy kevesebbel kell rendelkeznie, és az operációsrendszer-lemez méretének 300 GB-nál kisebbnek kell lennie.
  |
| **Lemezméret**                  | 2 TB az operációs rendszer lemezéhez, 4 TB adatlemezekhez.
| **Lemez száma** | Virtuális gépenként legfeljebb 16 lemez.
| **Titkosított lemezek/kötetek**    | Áttelepítésre nem támogatott. |
| **RDM/áthaladási lemezek**      | Áttelepítésre nem támogatott. |
| **Megosztott lemez** | A megosztott lemezeket használó virtuális gépek áttelepítése nem támogatott.
| **NFS**                        | A virtuális gépeken kötetként csatlakoztatott NFS-kötetek nem lesznek replikálva. |
| **Iscsi**                      | Az iSCSI-tárolókkal rendelkező virtuális gépek áttelepítése nem támogatott.
| **Céllemez**                | Az Azure virtuális gépek csak felügyelt lemezekkel telepíthetők át. |
| **IPv6** | Nem támogatott.
| **Hálózati adapterek összeállása** | Nem támogatott.
| **Az Azure webhely helyreállítása** | Nem replikálható az Azure Migrate Server Migration használatával, ha a virtuális gép engedélyezve van az Azure Site Recovery replikációjához.
| **Portok** | Kimenő kapcsolatok a 443-as HTTPS-porton a virtuális gép replikációs adatainak küldéséhez.

## <a name="azure-vm-requirements"></a>Azure virtuálisgép-követelmények

Az Azure-ba replikált összes helyszíni virtuális gépnek meg kell felelnie az ebben a táblázatban összegzett Azure virtuális gép követelményeinek.

**Összetevő** | **Követelmények** | **Részletek**
--- | --- | ---
Operációsrendszer-lemez mérete | Akár 2048 GB. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációsrendszer-lemezek száma | 1 | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemezek száma | 16 vagy kevesebb. | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemez mérete | Akár 4095 GB | Az ellenőrzés sikertelen, ha nem támogatott.
Hálózati adapterek | Több adapter is támogatott. |
Megosztott VHD | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
FC-lemez | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
BitLocker | Nem támogatott. | A gép replikációjának engedélyezése előtt le kell tiltani a BitLocker szolgáltatást.
a virtuális gép neve | 1 és 63 karakter között.<br/> Csak betűket, számokat és kötőjelet tartalmazhat.<br/><br/> A gép nevének betűvel vagy számmal kell kezdődnie és végződnie. |  Frissítse a számítógép tulajdonságainak értékét a Site Recovery szolgáltatásban.
Csatlakozás áttelepítés után-Windows | Csatlakozás Windows rendszert futtató Azure-beli virtuális gépekhez az áttelepítés után:<br/> - Az áttelepítés előtt lehetővé teszi az RDP a helyszíni virtuális gép. Ellenőrizze, hogy a **Nyilvános** profilnál felvette-e a listára a TCP- és UDP-szabályokat, valamint hogy a **Windows-tűzfal** > **Engedélyezett alkalmazások** területén az összes profil számára engedélyezve van-e az RDP.<br/> A helyek közötti VPN-hozzáférés hez engedélyezze az RDP-t, és engedélyezze az RDP szolgáltatást a **Windows tűzfal** -> **engedélyezett alkalmazásaiban és szolgáltatásaiban** **a tartományi és magánhálózatokhoz.** Ezenkívül ellenőrizze, hogy az operációs rendszer SAN-házirendje **OnlineAll lesz-e.** [További információ](prepare-for-migration.md). |
Csatlakozás áttelepítés után-Linux | Csatlakozás Az Azure virtuális gépekhez az SSH használatával történő áttelepítés után:<br/> Az áttelepítés előtt a helyszíni gépen ellenőrizze, hogy a Secure Shell szolgáltatás indításra van-e állítva, és hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.<br/> Feladatátvétel után az Azure virtuális gép, engedélyezze a bejövő kapcsolatokat az SSH-port a hálózati biztonsági csoport szabályok a feladatátvételi virtuális gép, és az Azure-alhálózat, amelyhez csatlakozik. Emellett adjon hozzá egy nyilvános IP-címet a virtuális géphez. |  

## <a name="next-steps"></a>További lépések

[Hyper-V virtuális gépek áttelepítése.](tutorial-migrate-hyper-v.md)
