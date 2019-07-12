---
title: Hyper-V elemzésekhez és migráláshoz az Azure Migrate támogatási mátrix
description: Beállítások és a Hyper-V elemzésekhez és migráláshoz az Azure Migrate szolgáltatás használatával korlátozásokat foglalja össze.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/02/2019
ms.author: raynew
ms.openlocfilehash: f6edbe19429b38d68aea1f1ecfe426c9b2d194d0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811348"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>Hyper-V elemzésekhez és migráláshoz támogatási mátrix

Használhatja a [Azure Migrate szolgáltatás](migrate-overview.md) segítségével mérheti fel, és a gépek áttelepítése a Microsoft Azure felhőbe. Ez a cikk összegzi a támogatási beállításait és korlátozásai és a helyszíni Hyper-V virtuális gépek migrálnak.



## <a name="hyper-v-scenarios"></a>A Hyper-V-forgatókönyvek

A táblázat összefoglalja a támogatott forgatókönyveket Hyper-V virtuális gépek számára.

**Üzembe helyezés** | **Részletek*** 
--- | --- 
**A helyszíni Hyper-V virtuális gépek értékelése** | [Állítsa be a](tutorial-prepare-hyper-v.md) az első értékelést.<br/><br/> [Futtatás](scale-hyper-v-assessment.md) a nagy léptékű értékelést.
**A Hyper-V virtuális gépek áttelepítése az Azure-bA** | [Próbálja ki](tutorial-migrate-hyper-v.md) áttelepítése az Azure-bA.

    

## <a name="azure-migrate-projects"></a>Az Azure Migrate-projekt

**Támogatás** | **Részletek**
--- | ---
Azure-engedélyeket | Az Azure Migrate-projekt létrehozása az előfizetés közreműködő vagy tulajdonos engedélyek szükségesek.
Hyper-V virtuális gépek | Mérje fel, legfeljebb 10 000 Hyper-V virtuális gépek, egyetlen projekt.

A projekt tartalmazhatnak VMware virtuális gépek és a Hyper-V virtuális gépek, egészen az értékelés korlátjáig.


## <a name="assessment-hyper-v-host-requirements"></a>Értékelés – Hyper-V gazdagép-követelményei

| **Támogatás**                | **Részletek**               
| :-------------------       | :------------------- |
| **Gazdagép üzembe helyezése**       | A Hyper-V-gazdagép lehet önálló vagy fürtben üzembe helyezett. |
| **Engedélyek**           | A Hyper-V gazdagépen rendszergazdai jogosultság szükséges. |
| **Gazdagép operációs rendszere** | A Windows Server 2016 vagy Windows Server 2012 R2.<br/> A Windows Server 2019 futtató Hyper-V-gazdagépeken található virtuális gépek nem tudja felmérni. |
| **PowerShell távoli eljáráshívás**   | Minden gazdagépen engedélyezni kell. |
| **A Hyper-V replika**       | Ha a Hyper-V replikát használ (vagy több virtuális gépei az ugyanazon Virtuálisgép-azonosítókkal), és Fedezze fel is az eredeti és a replikált virtuális gépek Azure Migrate szolgáltatással, az Azure Migrate által generált értékelés nem feltétlenül pontos. |


## <a name="assessment-hyper-v-vm-requirements"></a>Értékelés – a Hyper-V Virtuálisgép-követelmények

| **Támogatás**                  | **Részletek**               
| :----------------------------- | :------------------- |
| **Operációs rendszer** | Az összes [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) és [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) Azure által támogatott operációs rendszerek. |
| **Engedélyek**           | Az egyes Hyper-V virtuális Gépeken, az értékelni kívánt rendszergazdai jogosultság szükséges. |
| **Az integrációs szolgáltatások**       | [A Hyper-V integrációs szolgáltatásokat](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) virtuális gépeket, felmérheti, annak érdekében, hogy az operációs rendszer adatainak rögzítése rendszeren kell futnia. |
| **Az Azure-hoz szükséges módosításokat** | Néhány virtuális gépet módosításait igénylik, hogy az Azure rendszerben futtatnak. Az Azure Migrate szolgáltatással automatikusan a következő operációs rendszerekhez ezeket a módosításokat:<br/> – A Red Hat Enterprise Linux 6.5-ös + 7.0 +<br/> - CentOS 6.5+, 7.0+</br> – SUSE Linux Enterprise Server 12 SP1 +<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> -Debian 7,8<br/><br/> Más operációs rendszerek esetén hajtsa végre az esetlegesen manuálisan az áttelepítés előtt kell. Az ide tartozó cikkekre ehhez útmutatást tartalmaz. |
| **Linux-rendszerindítás**                 | Ha gyökérpartíció dedikált partíción, az operációsrendszer-lemez a kell tárolni, és nem oszlanak meg több lemezre kiterjedő.<br/> Gyökérpartíció része a gyökér (/) partíció, ha ezután a "/" partíció kell az operációsrendszer-lemez lehet, és nem terjed ki a többi lemezen. |
| **UEFI-rendszerindítás**                  | Az UEFI-rendszerindítást virtuális gépek nem telepíthetők át. |
| **Titkosított lemezek vagy kötetek**    | Titkosított lemezek vagy kötetek rendelkező virtuális gépek áttelepítése nem támogatott. |
| **RDM-/ lemezei**      | Ha a virtuális gép RDM vagy csatlakoztatott lemezzel rendelkezik, ezeket a lemezeket az Azure-ba nem replikálható. |
| **NFS**                        | NFS-kötetek kötetek fürtkötetként a virtuális gépeken nem replikálható. |
| **Céllemez**                | Azure Migrate-értékelések ajánlott áttelepítési csak a felügyelt lemezekkel rendelkező Azure virtuális gépekre. |


## <a name="assessment-appliance-requirements"></a>Értékelés-berendezés követelmények

Értékelés az Azure Migrate futtatja a Hyper-V virtuális gépek felderítéséhez és a virtuális gép metaadatainak és a teljesítmény adatokat küldeni az Azure Migrate egy egyszerűsített készülék. A berendezés a Hyper-V virtuális gép fut, és beállíthat egy tömörített Hyper-V virtuális Merevlemezt, amely az Azure Portalról letöltött felhasználja. A következő táblázat összefoglalja a készülék követelményeknek.

| **Támogatás**                | **Részletek**               
| :-------------------       | :------------------- |
| **Az Azure Migrate-projekt**  |  Egy készülék egyetlen projekt társítható.<br/> Legfeljebb 5000-es Hyper-V virtuális gépek egy egyetlen berendezéssel képes felderíteni.
| **A Hyper-V-korlátozások**    |  A berendezés egy Hyper-V virtuális gépként üzembe helyezése.<br/> A berendezés virtuális Gépet a megadott Hyper-V virtuális gép 5.0-s verzió.<br/> A Virtuálisgép-gazda futnia kell a Windows Server 2012 R2 vagy újabb.<br/> Elegendő lemezterület lefoglalása 16 GB RAM, 4 virtuális processzor szükséges, és 1 külső kapcsoló a virtuális berendezés.<br/> Készülék statikus vagy dinamikus IP-címet, és a internet-hozzáférés szükséges.
| **A Hyper-V-készülék**      |  A berendezés egy Hyper-V virtuális gépként van beállítva.<br/> A virtuális merevlemez letöltésre Hyper-V virtuális gép 5.0-s verzió.
| **Gazdagép**                   | A berendezés virtuális gép futó Virtuálisgép-gazda futnia kell a Windows Server 2012 R2 vagy újabb.<br/> 16 GB RAM, 4 virtuális processzor és a egy külső kapcsoló a virtuális berendezés lefoglalni elegendő terület szükséges.<br/> Készülék statikus vagy dinamikus IP-címet, és a internet-hozzáférés szükséges. |
| **Áttelepítés támogatása**      | Gépek replikálásának megkezdéséhez az áttelepítés átjárószolgáltatás a készülék 1.18.7141.12919 kell lennie, vagy később. Jelentkezzen be a készülék webalkalmazás a verzió ellenőrzéséhez. |

## <a name="assessment-appliance-url-access"></a>Értékelés-berendezés URL-hozzáférés

Virtuális gépek értékeléséhez, az Azure Migrate készülék internetkapcsolattal kell rendelkeznie.

- A berendezés telepítésekor az Azure Migrate nem egy kapcsolat ellenőrzése az URL-címeket az alábbi táblázat foglalja össze.
- Egy URL-alapú firewall.proxy használja, ha az URL-címek elérését teszi lehetővé a táblázatban, megakadályozhatja, hogy a proxy szünteti meg minden olyan CNAME-rekordokat kapott az URL-címek keresése.
- Ha egy lehallgató proxy, szüksége lehet a tanúsítvány importálásához a proxykiszolgálót a berendezést. 

    
**URL-cím** | **Részletek**  
--- | --- 
*.portal.azure.com | Navigálás az Azure Portalra
*.windows.net | Jelentkezzen be az Azure-előfizetésébe
*.microsoftonline.com | A szolgáltatás kommunikáció berendezés alkalmazások létrehozása az Azure Active Directory.
management.azure.com | A szolgáltatás kommunikáció berendezés alkalmazások létrehozása az Azure Active Directory.
dc.services.visualstudio.com | Naplózás és figyelés 
*.vault.azure.net | Az Azure Key Vaultban titkos kulcsok kezelése, a készüléket és a szolgáltatás közötti kommunikáció során.


## <a name="assessment-port-requirements"></a>Értékelés-port követelményei

A következő táblázat összefoglalja az értékelés port követelményei.

**Device** | **kapcsolat**
--- | --- 
**Készülék** | A berendezés távoli asztali kapcsolatok engedélyezése a 3389-es TCP-porton bejövő kapcsolatokat.<br/> Bejövő kapcsolatok 44368 távoli eléréséhez a készülék felügyeleti alkalmazás, az URL-porton: https://<appliance-ip-or-name>:44368<br/> Az Azure Migrate felderítési és teljesítményére vonatkozó metaadatok küldendő 443-as porton a kimenő kapcsolatokat.
**Hyper-V gazdagép vagy fürt** | A bejövő kapcsolatok az 5985-ös (HTTP) és az 5986-os (HTTPS) munkamenet Common Information Model (CIM) használatával, a Hyper-V virtuális gépek konfigurációjára és teljesítményére metaadatok lekérése a Rendszerfelügyeleti webszolgáltatások portot.

## <a name="migration-hyper-v-host-requirements"></a>Áttelepítés – a Hyper-V gazdagép-követelményei

| **Támogatás**                | **Részletek**               
| :-------------------       | :------------------- |
| **Gazdagép üzembe helyezése**       | A Hyper-V-gazdagép lehet önálló vagy fürtben üzembe helyezett. |
| **Engedélyek**           | A Hyper-V gazdagépen rendszergazdai jogosultság szükséges. |
| **Gazdagép operációs rendszere** | A Windows Server 2019, a Windows Server 2016 vagy Windows Server 2012 R2. |

## <a name="migration-hyper-v-vm-requirements"></a>Áttelepítés a Hyper-V Virtuálisgép-követelmények

| **Támogatás**                  | **Részletek**               
| :----------------------------- | :------------------- |
| **Operációs rendszer** | Az összes [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) és [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) Azure által támogatott operációs rendszerek. |
| **Engedélyek**           | Az egyes Hyper-V virtuális Gépeken, az értékelni kívánt rendszergazdai jogosultság szükséges. |
| **Az integrációs szolgáltatások**       | [A Hyper-V integrációs szolgáltatásokat](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) virtuális gépeket, felmérheti, annak érdekében, hogy az operációs rendszer adatainak rögzítése rendszeren kell futnia. |
| **Az Azure-hoz szükséges módosításokat** | Néhány virtuális gépet módosításait igénylik, hogy az Azure rendszerben futtatnak. Az Azure Migrate szolgáltatással automatikusan a következő operációs rendszerekhez ezeket a módosításokat:<br/> – A Red Hat Enterprise Linux 6.5-ös + 7.0 +<br/> - CentOS 6.5+, 7.0+</br> – SUSE Linux Enterprise Server 12 SP1 +<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> -Debian 7,8<br/><br/> Más operációs rendszerek esetén hajtsa végre az esetlegesen manuálisan az áttelepítés előtt kell. Az ide tartozó cikkekre ehhez útmutatást tartalmaz. |
| **Linux-rendszerindítás**                 | Ha gyökérpartíció dedikált partíción, az operációsrendszer-lemez a kell tárolni, és nem oszlanak meg több lemezre kiterjedő.<br/> Gyökérpartíció része a gyökér (/) partíció, ha ezután a "/" partíció kell az operációsrendszer-lemez lehet, és nem terjed ki a többi lemezen. |
| **UEFI-rendszerindítás**                  | Az UEFI-rendszerindítást virtuális gépek nem telepíthetők át. |
| **Titkosított lemezek vagy kötetek**    | Titkosított lemezek vagy kötetek rendelkező virtuális gépek áttelepítése nem támogatott. |
| **RDM-/ lemezei**      | Ha a virtuális gép RDM vagy csatlakoztatott lemezzel rendelkezik, ezeket a lemezeket az Azure-ba nem replikálható. |
| **NFS**                        | NFS-kötetek kötetek fürtkötetként a virtuális gépeken nem replikálható. |
| **Céllemez**                | Csak a felügyelt lemezekkel rendelkező Azure virtuális gépek áttelepíthetők. |




## <a name="migration-hyper-v-host-url-access"></a>Áttelepítés – a Hyper-V gazdagép URL-hozzáférés

Az alábbi táblázat foglalja össze a Hyper-V-gazdagépek URL-cím hozzáférési követelményeinek.

**URL-cím** | **Részletek**  
--- | ---
login.microsoftonline.com | Vezérléshez és identitáskezeléshez kezelési Active Directory használatával.
*.backup.windowsazure.com | Replikációs adatátvitelhez és összehangoláshoz.
*.hypervrecoverymanager.windowsazure.com | Csatlakozás az Azure Migrate szolgáltatás URL-címeit.
*.blob.core.windows.net | Adatok feltöltése a storage-fiókok.
dc.services.visualstudio.com | Belső a figyeléshez használt alkalmazás-naplók feltöltése.
time.windows.com | Ellenőrzi a rendszer és a globális idő közötti időszinkronizálás.

## <a name="migration-port-access"></a>Áttelepítési-port hozzáférés

A következő táblázat összefoglalja a port követelményei a Hyper-V-gazdagépek és virtuális gépek virtuális gépek migrálása.

**Device** | **kapcsolat**
--- | --- 
A Hyper-V-gazdagépek vagy virtuális gépek | Kimenő HTTPS-kapcsolatot a 443-as virtuális gép replikációs adatokat küldeni az Azure Migrate port.

  
## <a name="migration-vm-disk-support"></a>Migrálás virtuális lemezek támogatása 

**Támogatás** | **Részletek**
--- | ---
Az áttelepített lemezek | Virtuális gépek csak a managed Disks szolgáltatásba (standard HHD, prémium szintű SSD) az Azure-ban telepíthető át.
   

## <a name="next-steps"></a>További lépések

[Felkészülés a Hyper-V virtuális gépek](tutorial-prepare-hyper-v.md) az áttelepítéshez.




 
