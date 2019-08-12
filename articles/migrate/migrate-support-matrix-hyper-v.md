---
title: Azure Migrate támogatási mátrix a Hyper-V felméréséhez és áttelepítéséhez
description: Összefoglalja a Hyper-V felmérés és a Azure Migrate szolgáltatás használatával végzett áttelepítés beállításait és korlátozásait.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: raynew
ms.openlocfilehash: 105cbf173a9abe1adf0999f63740d47b3da51a29
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856289"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>A Hyper-V felmérésének és migrálásának támogatási mátrixa

A [Azure Migrate szolgáltatással](migrate-overview.md) a gépeket kivizsgálhatja és áttelepítheti a Microsoft Azure felhőbe. Ez a cikk a helyszíni Hyper-V virtuális gépek felmérésére és áttelepítésére vonatkozó támogatási beállításokat és korlátozásokat foglalja össze.



## <a name="hyper-v-scenarios"></a>Hyper-V-forgatókönyvek

A táblázat összefoglalja a Hyper-V virtuális gépek támogatott forgatókönyveit.

**Üzembe helyezés** | **Részletek***
--- | ---
**Helyszíni Hyper-V virtuális gépek felmérése** | [Állítsa be](tutorial-prepare-hyper-v.md) az első értékelést.<br/><br/> Nagy léptékű értékelés [futtatása](scale-hyper-v-assessment.md) .
**A Hyper-V virtuális gépek migrálása az Azure-ba** | [Próbálja ki](tutorial-migrate-hyper-v.md) az Azure-ba való áttelepítést. 

A System Center Virtual Machine Manager (VMM) által felügyelt Hyper-V-kiszolgálók áttelepítése nem támogatott Azure Migrate-kiszolgáló áttelepítése esetén. 

## <a name="azure-migrate-projects"></a>Azure Migrate projektek

**Támogatás** | **Részletek**
--- | ---
Azure-engedélyek | Azure Migrate projekt létrehozásához közreműködői vagy tulajdonosi engedélyekkel kell rendelkeznie az előfizetésben.
Hyper-V virtuális gépek | Akár 35 000 Hyper-V virtuális gépet is kivizsgálhat egyetlen projektben. Egy Azure-előfizetéshez több projekt is tartozhat. A projektek tartalmazhatják a VMware virtuális gépeket és a Hyper-V virtuális gépeket is, az értékelési korlátokig.
Földrajzi hely | Azure Migrate-projekteket számos földrajzi régióban is létrehozhatja. Habár létrehozhat projekteket adott ographies, a gépeket más célhelyekre is kielemezheti vagy áttelepítheti. A projekt földrajza csak a felderített metaadatok tárolására szolgál.

  **Régiócsoport** | **Metaadatok tárolási helye**
  --- | ---
  Azure Government | USA-beli államigazgatás – Virginia
  Ázsia és a Csendes-óceáni térség | Kelet-Ázsia vagy Délkelet-Ázsia
  Ausztrália | Kelet-Ausztrália vagy Délkelet-Ausztrália
  Kanada | Közép-Kanada vagy Kelet-Kanada
  Európa | Észak-Európa vagy Nyugat-Európa
  India | Közép-India vagy Dél-India
  Japán |  Kelet-japán vagy Nyugat-Japán
  Egyesült Királyság | Egyesült Királyság déli régiója vagy Egyesült Királyság nyugati régiója
  Egyesült Államok | USA középső régiója vagy USA 2. nyugati régiója


 > [!NOTE]
 > A Azure Government támogatása jelenleg csak a Azure Migrate [régebbi verziójához](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) érhető el.


## <a name="assessment-hyper-v-host-requirements"></a>Értékelés – Hyper-V gazdagépre vonatkozó követelmények

| **Támogatás**                | **Részletek**               
| :-------------------       | :------------------- |
| **Gazdagép üzembe helyezése**       | A Hyper-V-gazdagép önálló vagy fürtben is üzembe helyezhető. |
| **Engedélyek**           | Rendszergazdai engedélyekkel kell rendelkeznie a Hyper-V-gazdagépen. |
| **Gazda operációs rendszer** | Windows Server 2016 vagy Windows Server 2012 R2.<br/> A Windows Server 2019 rendszert futtató Hyper-V-gazdagépeken található virtuális gépek nem mérhetők fel. |
| **PowerShell távoli eljáráshívás**   | Minden gazdagépen engedélyezve kell lennie. |
| **Hyper-V replika**       | Ha a Hyper-V replikát használja (vagy több virtuális GÉPET ugyanazzal a virtuálisgép-azonosítóval), és az eredeti és a replikált virtuális gépeket is Azure Migrate használatával észleli, előfordulhat, hogy a Azure Migrate által létrehozott értékelés nem pontos. |


## <a name="assessment-hyper-v-vm-requirements"></a>Értékelés – Hyper-V virtuális gépekre vonatkozó követelmények

| **Támogatás**                  | **Részletek**               
| :----------------------------- | :------------------- |
| **Operációs rendszer** | Az Azure által támogatott összes Windows-és [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) - [alapú](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) operációs rendszer. |
| **Engedélyek**           | Minden felmérni kívánt Hyper-V virtuális gépen rendszergazdai engedélyekkel kell rendelkeznie. |
| **Integrációs szolgáltatások**       | Az operációs rendszer adatainak rögzítéséhez a [Hyper-V integrációs szolgáltatásoknak](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) olyan virtuális gépeken kell futniuk, amelyeket Ön értékelni fog. |
| **UEFI-rendszerindítás**                  | Az UEFI-rendszerindítással rendelkező virtuális gépek migrálása nem támogatott. |
| **Titkosított lemezek/kötetek**    | A titkosított lemezekkel/kötetekkel rendelkező virtuális gépek migrálása nem támogatott. |
| **RDM/továbbító lemezek**      | Ha a virtuális gépek RDM vagy továbbító lemezzel rendelkeznek, ezek a lemezek nem replikálódnak az Azure-ba. |
| **NFS**                        | A virtuális gépeken kötetként csatlakoztatott NFS-kötetek nem lesznek replikálva. |
| **Céllemez**                | Azure Migrate felmérések csak a felügyelt lemezekkel rendelkező Azure-beli virtuális gépekre való Migrálás javasoltak. |


## <a name="assessment-appliance-requirements"></a>Felmérés – készülékre vonatkozó követelmények

Az értékeléshez Azure Migrate egy könnyű berendezést futtat a Hyper-V virtuális gépek felderítéséhez, és a virtuális gép metaadatait és teljesítményadatait a Azure Migrateba küldi. A készülék egy Hyper-V virtuális gépen fut, és a Azure Portalból letöltött tömörített Hyper-V virtuális merevlemez használatával van beállítva. A következő táblázat összefoglalja a készülékre vonatkozó követelményeket.

| **Támogatás**                | **Részletek**               
| :-------------------       | :------------------- |
| **Azure Migrate projekt**  |  Egy készülék egyetlen projekthez is társítható.<br/> Akár 5000 Hyper-V virtuális gépet is felfedezhet egyetlen berendezéssel.
| **Hyper-V**    |  A berendezést Hyper-V virtuális gépként kell üzembe helyezni.<br/> A készülékhez megadott VM a Hyper-V VM 5,0-es verziója.<br/> A virtuális gépnek Windows Server 2012 R2 vagy újabb rendszernek kell futnia.<br/> A készülék virtuális gépe számára elegendő terület szükséges a 16 GB RAM, 8 vCPU és 1 külső kapcsoló lefoglalásához.<br/> A készülék statikus vagy dinamikus IP-címet, valamint internet-hozzáférést igényel.


## <a name="assessment-appliance-url-access"></a>Felmérés – készülék URL-hozzáférése

A virtuális gépek felméréséhez az Azure Migrate berendezésnek internetkapcsolatra van szüksége.

- A berendezés telepítésekor a Azure Migrate az alábbi táblázatban összefoglalt URL-címek kapcsolatát.
- Ha URL-alapú proxyt használ, engedélyezze a hozzáférést a táblázatban lévő URL-címekhez, és győződjön meg arról, hogy a proxy feloldja az URL-címek keresése során fogadott CNAME-rekordokat.
- Ha elfogó proxyval rendelkezik, előfordulhat, hogy importálnia kell a kiszolgálói tanúsítványt a proxykiszolgálóhoz a készülékre.


**URL-cím** | **Részletek**  
--- | ---
*.portal.azure.com | Navigálás a Azure Portal
*.windows.net | Bejelentkezés az Azure-előfizetésbe
*.microsoftonline.com | Azure Active Directory-alkalmazások létrehozása a berendezés és a szolgáltatások közötti kommunikációhoz.
management.azure.com | Azure Active Directory-alkalmazások létrehozása a berendezés és a szolgáltatások közötti kommunikációhoz.
dc.services.visualstudio.com | Naplózás és figyelés
*.vault.azure.net | Azure Key Vault titkok kezelése a készülék és a szolgáltatás közötti kommunikáció során.
aka.ms/* | Hozzáférés engedélyezése az aka hivatkozásokhoz.
https://download.microsoft.com/download/* | A letölthető fájlok letöltése a Microsoft letöltési webhelyéről.



## <a name="assessment-port-requirements"></a>Értékelés – portra vonatkozó követelmények

A következő táblázat összefoglalja az értékeléshez szükséges portokra vonatkozó követelményeket.

**Device** | **kapcsolat**
--- | ---
**Berendezés** | Bejövő kapcsolatok a 3389-as TCP-porton, hogy engedélyezze a távoli asztali kapcsolatokat a berendezéssel.<br/> Bejövő kapcsolatok a 44368-as porton a berendezés-kezelő alkalmazás távoli eléréséhez az URL-cím használatával:``` https://<appliance-ip-or-name>:44368 ```<br/> A 443-es porton kimenő kapcsolatok a felderítési és teljesítményi metaadatok küldéséhez Azure Migrate.
**Hyper-V-gazdagép/-fürt** | A WinRM-portok 5985 (HTTP) és 5986 (HTTPS) bejövő kapcsolatai a Hyper-V virtuális gépek konfigurációjának és teljesítményének lekéréséhez CIM (CIM) munkamenet használatával.

## <a name="migration-hyper-v-host-requirements"></a>Migrálás – a Hyper-V gazdagépre vonatkozó követelmények

| **Támogatás**                | **Részletek**               
| :-------------------       | :------------------- |
| **Gazdagép üzembe helyezése**       | A Hyper-V-gazdagép önálló vagy fürtben is üzembe helyezhető. |
| **Engedélyek**           | Rendszergazdai engedélyekkel kell rendelkeznie a Hyper-V-gazdagépen. |
| **Gazda operációs rendszer** | Windows Server 2019, Windows Server 2016 vagy Windows Server 2012 R2. |

## <a name="migration-hyper-v-vm-requirements"></a>Migrálás – Hyper-V virtuális gépekre vonatkozó követelmények

| **Támogatás**                  | **Részletek**               
| :----------------------------- | :------------------- |
| **Operációs rendszer** | Az Azure által támogatott összes Windows-és [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) - [alapú](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) operációs rendszer. |
| **Engedélyek**           | Minden felmérni kívánt Hyper-V virtuális gépen rendszergazdai engedélyekkel kell rendelkeznie. |
| **Integrációs szolgáltatások**       | Az operációs rendszer adatainak rögzítéséhez a [Hyper-V integrációs szolgáltatásoknak](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) olyan virtuális gépeken kell futniuk, amelyeket Ön értékelni fog. |
| **Az Azure szükséges módosításai** | Előfordulhat, hogy egyes virtuális gépek módosításokat igényelnek, hogy az Azure-ban is futtathatók legyenek. A Azure Migrate a következő operációs rendszerek esetében automatikusan végrehajtja ezeket a módosításokat:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> Más operációs rendszerek esetében manuálisan kell elvégezni a módosításokat az áttelepítés előtt. A kapcsolódó cikkek erre vonatkozó utasításokat tartalmaznak. |
| **Linux rendszerű rendszerindítás**                 | Ha a/boot dedikált partíción van, akkor az operációsrendszer-lemezen kell lennie, és nem szabad több lemezre osztania.<br/> Ha a/boot a gyökér (/) partíció része, akkor a "/" partíciónak az operációsrendszer-lemezen kell lennie, és nem szabad más lemezekre kiterjednie. |
| **UEFI-rendszerindítás**                  | Az UEFI-rendszerindítással rendelkező virtuális gépek migrálása nem támogatott.  |
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





## <a name="migration-hyper-v-host-url-access"></a>Áttelepítés – Hyper-V gazdagép URL-hozzáférése

A következő táblázat összefoglalja a Hyper-V-gazdagépek URL-hozzáférési követelményeit.

**URL-cím** | **Részletek**  
--- | ---
login.microsoftonline.com | Hozzáférés-vezérlés és Identitáskezelés Active Directory használatával.
*.backup.windowsazure.com | Replikálási adatok átvitele és koordinálása.
*.hypervrecoverymanager.windowsazure.com | Kapcsolódjon Azure Migrate szolgáltatás URL-címeihez.
*.blob.core.windows.net | Adatok feltöltése a Storage-fiókba.
dc.services.visualstudio.com | A belső figyeléshez használt alkalmazás-naplók feltöltése.
time.windows.com | Ellenőrzi a rendszerek és a globális idő közötti időszinkronizálást.

## <a name="migration-port-access"></a>Áttelepítési porthoz való hozzáférés

A következő táblázat összefoglalja a Hyper-V-gazdagépeken és virtuális gépeken a VM-Migrálás portokra vonatkozó követelményeit.

**Device** | **kapcsolat**
--- | ---
Hyper-V-gazdagépek/virtuális gépek | Kimenő kapcsolatok a 443-es HTTPS-porton, hogy a virtuális gép replikációs szolgáltatásait Azure Migrate küldje el.




## <a name="next-steps"></a>További lépések

[Felkészülés a Hyper-V virtuális](tutorial-prepare-hyper-v.md) gépek áttelepítésre való értékelésére.
