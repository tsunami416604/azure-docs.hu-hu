---
title: A Hyper-V áttelepítésének támogatása a Azure Migrateban
description: Ismerkedjen meg a Hyper-V áttelepítésének támogatásával Azure Migrateokkal.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 1e1fa7e9c2e2105bef02f833e40dadf113dea4b5
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84323773"
---
# <a name="support-matrix-for-hyper-v-migration"></a>A Hyper-V áttelepítésének támogatási mátrixa

Ez a cikk a Hyper-V virtuális gépek [Azure Migrate: kiszolgáló áttelepítéssel](migrate-services-overview.md#azure-migrate-server-migration-tool) történő áttelepítésére vonatkozó támogatási beállításokat és korlátozásokat foglalja össze. Ha a Hyper-V virtuális gépek Azure-ba való áttelepítésére vonatkozó információkat keres, tekintse át az [értékelés támogatási mátrixát](migrate-support-matrix-hyper-v.md).

## <a name="migration-limitations"></a>A migrálásra vonatkozó korlátozások

Egyszerre legfeljebb 10 virtuális gépet választhat a replikáláshoz. Ha több gépet szeretne áttelepíteni, a replikálást 10 csoportba kell helyezni.


## <a name="hyper-v-hosts"></a>Hyper-V gazdagépek

| **Támogatás**                | **Részletek**               
| :-------------------       | :------------------- |
| **Üzembe helyezés**       | A Hyper-V-gazdagép önálló vagy fürtben is üzembe helyezhető. <br/>Azure Migrate replikációs szoftver (Hyper-V replikációs szolgáltató) telepítve van a Hyper-V-gazdagépeken.|
| **Engedélyek**           | Rendszergazdai engedélyekkel kell rendelkeznie a Hyper-V-gazdagépen. |
| **Gazda operációs rendszer** | Windows Server 2019, Windows Server 2016 vagy Windows Server 2012 R2. |
| **Port-hozzáférés** |  Kimenő kapcsolatok a 443-es HTTPS-porton a virtuális gép replikációs adatküldéséhez.

### <a name="url-access-public-cloud"></a>URL-hozzáférés (nyilvános felhő)

A Hyper-V-gazdagépeken a replikációs szolgáltató szoftverének hozzá kell férnie ezekhez az URL-címekhez.

**URL-cím** | **Részletek**
--- | ---
login.microsoftonline.com | Hozzáférés-vezérlés és Identitáskezelés Active Directory használatával.
backup.windowsazure.com | Replikálási adatok átvitele és koordinálása.
*.hypervrecoverymanager.windowsazure.com | Replikációs felügyelethez használatos.
*.blob.core.windows.net | Adatok feltöltése a Storage-fiókba. 
dc.services.visualstudio.com | A belső figyeléshez használt alkalmazás-naplók feltöltése.
time.windows.com | Ellenőrzi a rendszerek és a globális idő közötti időszinkronizálást.

### <a name="url-access-azure-government"></a>URL-hozzáférés (Azure Government)

A Hyper-V-gazdagépeken a replikációs szolgáltató szoftverének hozzá kell férnie ezekhez az URL-címekhez.

**URL-cím** | **Részletek**
--- | ---
login.microsoftonline.us | Hozzáférés-vezérlés és Identitáskezelés Active Directory használatával.
backup.windowsazure.us | Replikálási adatok átvitele és koordinálása.
*. hypervrecoverymanager.windowsazure.us | Replikációs felügyelethez használatos.
*. blob.core.usgovcloudapi.net | Adatok feltöltése a Storage-fiókba.
dc.services.visualstudio.com | A belső figyeléshez használt alkalmazás-naplók feltöltése.
time.nist.gov | Ellenőrzi a rendszerek és a globális idő közötti időszinkronizálást.


## <a name="hyper-v-vms"></a>Hyper-V virtuális gépek

| **Támogatás**                  | **Részletek**               
| :----------------------------- | :------------------- |
| **Operációs rendszer** | Az Azure által támogatott összes Windows-és [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) - [alapú](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) operációs rendszer. |
| **Az Azure szükséges módosításai** | Előfordulhat, hogy egyes virtuális gépek módosításokat igényelnek, hogy az Azure-ban is futtathatók legyenek. A módosításokat manuálisan végezze el az áttelepítés előtt. A kapcsolódó cikkek erre vonatkozó utasításokat tartalmaznak. |
| **Linux rendszerű rendszerindítás**                 | Ha a/boot dedikált partíción van, akkor az operációsrendszer-lemezen kell lennie, és nem szabad több lemezre osztania.<br/> Ha a/boot a gyökér (/) partíció része, akkor a "/" partíciónak az operációsrendszer-lemezen kell lennie, és nem szabad más lemezekre kiterjednie. |
| **UEFI-rendszerindítás**                  | Az Azure-ban áttelepített virtuális gép automatikusan BIOS rendszerindító virtuális gépre lesz konvertálva. A virtuális gépnek csak a Windows Server 2012-es vagy újabb verziójának kell futnia. Az operációsrendszer-lemez legfeljebb öt partíciót tartalmazhat, és az operációsrendszer-lemez mérete nem haladhatja meg a 300 GB-ot.|
| **Lemezméret**                  | 2 TB az operációsrendszer-lemez, 4 TB az adatlemezek számára.|
| **Lemez száma** | Legfeljebb 16 lemez virtuális gépenként.|
| **Titkosított lemezek/kötetek**    | Migrálás esetén nem támogatott.|
| **RDM/továbbító lemezek**      | Migrálás esetén nem támogatott.|
| **Megosztott lemez** | A megosztott lemezeket használó virtuális gépek migrálása nem támogatott.|
| **NFS**                        | A virtuális gépeken kötetként csatlakoztatott NFS-kötetek nem lesznek replikálva.|
| **ISCSI**                      | Az iSCSI-tárolókkal rendelkező virtuális gépek migrálása nem támogatott.
| **Céllemez**                | Csak felügyelt lemezekkel rendelkező Azure-beli virtuális gépekre lehet migrálni. |
| **IPv6** | Nem támogatott.|
| **Hálózati adapterek összevonása** | Nem támogatott.|
| **Azure Site Recovery** | Ha a virtuális gép Azure Site Recovery használatával való replikálásra engedélyezve van, nem lehet replikálni Azure Migrate-kiszolgáló áttelepítésével.|
| **Portok** | Kimenő kapcsolatok a 443-es HTTPS-porton a virtuális gép replikációs adatküldéséhez.|

## <a name="azure-vm-requirements"></a>Azure virtuálisgép-követelmények

Az Azure-ba replikált összes helyszíni virtuális gépnek meg kell felelnie az ebben a táblázatban összefoglalt Azure-beli virtuálisgép-követelményeknek.

**Összetevő** | **Követelmények** | **Részletek**
--- | --- | ---
Operációsrendszer-lemez mérete | Akár 2 048 GB-ig. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációsrendszer-lemezek száma | 1 | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemezek száma | 16 vagy kevesebb. | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemez mérete | Legfeljebb 4 095 GB | Az ellenőrzés sikertelen, ha nem támogatott.
Hálózati adapterek | Több adapter is támogatott. |
Megosztott VHD | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
FC-lemez | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
BitLocker | Nem támogatott. | A számítógép replikálásának engedélyezése előtt le kell tiltani a BitLockert.
a virtuális gép neve | 1 – 63 karakter.<br/> Csak betűket, számokat és kötőjelet tartalmazhat.<br/><br/> A gép nevének betűvel vagy számmal kell kezdődnie és végződnie. |  Frissítse az értéket a Site Recovery számítógép tulajdonságai között.
Kapcsolat az áttelepítés után – Windows | Kapcsolódás a Windows rendszerű Azure-beli virtuális gépekhez a Migrálás után:<br/> – Az áttelepítés előtt engedélyezi az RDP-t a helyszíni virtuális gépen. Ellenőrizze, hogy a **Nyilvános** profilnál felvette-e a listára a TCP- és UDP-szabályokat, valamint hogy a **Windows-tűzfal** > **Engedélyezett alkalmazások** területén az összes profil számára engedélyezve van-e az RDP.<br/> A helyek közötti VPN-hozzáféréshez engedélyezze az RDP-t, és engedélyezze az RDP használatát a **Windows tűzfal**  ->  **engedélyezett alkalmazásaiban és szolgáltatásaiban** a **tartomány és a magánhálózatok** számára. Továbbá győződjön meg arról, hogy az operációs rendszer SAN-szabályzata **OnlineAll**értékre van állítva. [További információ](prepare-for-migration.md). |
Kapcsolat Migrálás után – Linux | Kapcsolódás az Azure-beli virtuális gépekhez az SSH használatával történő áttelepítés után:<br/> Az áttelepítés előtt a helyszíni gépen győződjön meg arról, hogy a Secure Shell szolgáltatás indításra van beállítva, és hogy a tűzfalszabályok engedélyezik az SSH-kapcsolatokat.<br/> A feladatátvételt követően az Azure-beli virtuális gépen engedélyezze az SSH-porthoz való bejövő kapcsolatokat a hálózati biztonsági csoportra vonatkozó szabályokra vonatkozóan a feladatátvételen átesett virtuális gépen, valamint azt az Azure-alhálózatot, amelyhez csatlakoztatva van. Továbbá adjon hozzá egy nyilvános IP-címet a virtuális géphez. |  

## <a name="next-steps"></a>Következő lépések

[Telepítse át a Hyper-V virtuális gépeket](tutorial-migrate-hyper-v.md) az áttelepítéshez.
