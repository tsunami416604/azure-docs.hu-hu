---
title: A Hyper-V áttelepítésének támogatása a Azure Migrateban
description: Ismerkedjen meg a Hyper-V áttelepítésének támogatásával Azure Migrateokkal.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 96bf423f25911d0befbfd420ac2fb01ba6c8fb65
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030262"
---
# <a name="support-matrix-for-hyper-v-migration"></a>A Hyper-V áttelepítésének támogatási mátrixa

Ez a cikk a Hyper-V virtuális gépek [Azure Migrate: kiszolgáló áttelepítéssel](migrate-services-overview.md#azure-migrate-server-migration-tool) történő áttelepítésére vonatkozó támogatási beállításokat és korlátozásokat foglalja össze. Ha a Hyper-V virtuális gépek Azure-ba való áttelepítésére vonatkozó információkat keres, tekintse át az [értékelés támogatási mátrixát](migrate-support-matrix-hyper-v.md).

## <a name="migration-limitations"></a>A migrálásra vonatkozó korlátozások

Egyszerre legfeljebb 10 virtuális gépet választhat a replikáláshoz. Ha több gépet szeretne áttelepíteni, a replikálást 10 csoportba kell helyezni.


## <a name="hyper-v-hosts"></a>Hyper-V gazdagépek

| **Támogatás**                | **Részletek**               
| :-------------------       | :------------------- |
| **Üzembe helyezés**       | A Hyper-V-gazdagép önálló vagy fürtben is üzembe helyezhető. |
| **Engedélyek**           | Rendszergazdai engedélyekkel kell rendelkeznie a Hyper-V-gazdagépen. |
| **Gazda operációs rendszer** | Windows Server 2019, Windows Server 2016 vagy Windows Server 2012 R2. |
| **URL-hozzáférés** | A Hyper-V-gazdagépeknek hozzá kell férniük az alábbi URL-címekhez:<br/><br/> -login.microsoftonline.com: hozzáférés-vezérlés és Identitáskezelés Active Directory használatával.<br/><br/> -*. backup.windowsazure.com: replikálási adatok átvitele és koordinálása. Szolgáltatás URL-címének áttelepítve.<br/><br/> -*. blob.core.windows.net: adatok feltöltése a Storage-fiókba.<br/><br/> -dc.services.visualstudio.com: a belső figyeléshez használt alkalmazás-naplók feltöltése.<br/><br/> - time.windows.com | Ellenőrzi a rendszerek és a globális idő közötti időszinkronizálást.
| **Port-hozzáférés** |  Kimenő kapcsolatok a 443-es HTTPS-porton a virtuális gép replikációs adatküldéséhez.

## <a name="hyper-v-vms"></a>Hyper-V virtuális gépek

| **Támogatás**                  | **Részletek**               
| :----------------------------- | :------------------- |
| **Operációs rendszer** | Az Azure által támogatott összes Windows-és [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) - [alapú](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) operációs rendszer. |
| **Engedélyek**           | Minden felmérni kívánt Hyper-V virtuális gépen rendszergazdai engedélyekkel kell rendelkeznie. |
| **Integrációs szolgáltatások**       | Az operációs rendszer adatainak rögzítéséhez a [Hyper-V integrációs szolgáltatásoknak](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) olyan virtuális gépeken kell futniuk, amelyeket Ön értékelni fog. |
| **Az Azure szükséges módosításai** | Előfordulhat, hogy egyes virtuális gépek módosításokat igényelnek, hogy az Azure-ban is futtathatók legyenek. A Azure Migrate a következő operációs rendszerek esetében automatikusan végrehajtja ezeket a módosításokat:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> Más operációs rendszerek esetében manuálisan kell elvégezni a módosításokat az áttelepítés előtt. A kapcsolódó cikkek erre vonatkozó utasításokat tartalmaznak. |
| **Linux rendszerű rendszerindítás**                 | Ha a/boot dedikált partíción van, akkor az operációsrendszer-lemezen kell lennie, és nem szabad több lemezre osztania.<br/> Ha a/boot a gyökér (/) partíció része, akkor a "/" partíciónak az operációsrendszer-lemezen kell lennie, és nem szabad más lemezekre kiterjednie. |
| **UEFI-rendszerindítás**                  | Az Azure-ban áttelepített virtuális gép automatikusan BIOS rendszerindító virtuális gépre lesz konvertálva. A virtuális gépnek csak a Windows Server 2012-es vagy újabb verziójának kell futnia. Az operációsrendszer-lemez legfeljebb öt partíciót tartalmazhat, és az operációsrendszer-lemez mérete nem haladhatja meg a 300 GB-ot.
  |
| **Lemez mérete**                  | 2 TB az operációsrendszer-lemez, 4 TB az adatlemezek számára.
| **Lemez száma** | Legfeljebb 16 lemez virtuális gépenként.
| **Titkosított lemezek/kötetek**    | Migrálás esetén nem támogatott. |
| **RDM/továbbító lemezek**      | Migrálás esetén nem támogatott. |
| **Megosztott lemez** | A megosztott lemezeket használó virtuális gépek migrálása nem támogatott.
| **NFS**                        | A virtuális gépeken kötetként csatlakoztatott NFS-kötetek nem lesznek replikálva. |
| **ISCSI**                      | Az iSCSI-tárolókkal rendelkező virtuális gépek migrálása nem támogatott.
| **Céllemez**                | Csak felügyelt lemezekkel rendelkező Azure-beli virtuális gépekre lehet migrálni. |
| **IPv6** | Nem támogatott.
| **Hálózati adapterek összevonása** | Nem támogatott.
| **Azure Site Recovery** | Ha a virtuális gép Azure Site Recovery használatával való replikálásra engedélyezve van, nem lehet replikálni Azure Migrate-kiszolgáló áttelepítésével.
| **Portok** | Kimenő kapcsolatok a 443-es HTTPS-porton a virtuális gép replikációs adatküldéséhez.

## <a name="azure-vm-requirements"></a>Azure-beli virtuális gépekre vonatkozó követelmények

Az Azure-ba replikált összes helyszíni virtuális gépnek meg kell felelnie az ebben a táblázatban összefoglalt Azure-beli virtuálisgép-követelményeknek. Ha Site Recovery futtatja az előfeltétel-ellenőrzés replikálását, akkor az ellenőrzés sikertelen lesz, ha egyes követelmények nem teljesülnek.

**Összetevő** | **Követelmények** | **Részletek**
--- | --- | ---
Vendég operációs rendszer | Ellenőrzi a VMware virtuális gépek támogatott operációs rendszereinek áttelepítését.<br/> A támogatott operációs rendszereken futó munkaterhelések áttelepíthetők. | Az ellenőrzés sikertelen, ha nem támogatott.
Vendég operációs rendszer architektúrája | 64 bites. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációs rendszer lemezének mérete | Akár 2 048 GB-ig. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációsrendszer-lemezek száma | 1 | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemezek száma | 64 vagy kevesebb. | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemez mérete | Legfeljebb 4 095 GB | Az ellenőrzés sikertelen, ha nem támogatott.
Hálózati adapterek | Több adapter is támogatott. |
Megosztott VHD | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
FC-lemez | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
BitLocker | Nem támogatott. | A számítógép replikálásának engedélyezése előtt le kell tiltani a BitLockert.
a virtuális gép neve | 1 – 63 karakter.<br/> Csak betűket, számokat és kötőjelet tartalmazhat.<br/><br/> A gép nevének betűvel vagy számmal kell kezdődnie és végződnie. |  Frissítse az értéket a Site Recovery számítógép tulajdonságai között.
Kapcsolat az áttelepítés után – Windows | Kapcsolódás a Windows rendszerű Azure-beli virtuális gépekhez a Migrálás után:<br/> – Az áttelepítés előtt engedélyezi az RDP-t a helyszíni virtuális gépen. Ellenőrizze, hogy a **Nyilvános** profilnál felvette-e a listára a TCP- és UDP-szabályokat, valamint hogy a **Windows-tűzfal** > **Engedélyezett alkalmazások** területén az összes profil számára engedélyezve van-e az RDP.<br/> A helyek közötti VPN-hozzáféréshez engedélyezze az RDP-t, és engedélyezze az RDP használatát a **Windows tűzfalon** -> **engedélyezett alkalmazásokat és szolgáltatásokat** a **tartomány-és magánhálózat** számára. Továbbá győződjön meg arról, hogy az operációs rendszer SAN-szabályzata **OnlineAll**értékre van állítva. [További információk](prepare-for-migration.md). |
Kapcsolat Migrálás után – Linux | Kapcsolódás az Azure-beli virtuális gépekhez az SSH használatával történő áttelepítés után:<br/> Az áttelepítés előtt a helyszíni gépen győződjön meg arról, hogy a Secure Shell szolgáltatás indításra van beállítva, és hogy a tűzfalszabályok engedélyezik az SSH-kapcsolatokat.<br/> A feladatátvételt követően az Azure-beli virtuális gépen engedélyezze az SSH-porthoz való bejövő kapcsolatokat a hálózati biztonsági csoportra vonatkozó szabályokra vonatkozóan a feladatátvételen átesett virtuális gépen, valamint azt az Azure-alhálózatot, amelyhez csatlakoztatva van. Továbbá adjon hozzá egy nyilvános IP-címet a virtuális géphez. |  

## <a name="next-steps"></a>Következő lépések

[Telepítse át a Hyper-V virtuális gépeket](tutorial-migrate-hyper-v.md) az áttelepítéshez.
