---
title: A fizikai kiszolgáló értékelésének és áttelepítésének támogatása Azure Migrate
description: Összefoglalja a fizikai kiszolgálók értékelésének/áttelepítésének támogatását Azure Migrateokkal.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: b5b5da6282b1df6c70fd58dcf8c417250de81b73
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196336"
---
# <a name="support-matrix-for-physical-server-assessment-and-migration"></a>A fizikai kiszolgálók felmérésének és migrálásának támogatási mátrixa

A [Azure Migrate szolgáltatással](migrate-overview.md) a gépeket kivizsgálhatja és áttelepítheti a Microsoft Azure felhőbe. Ez a cikk a helyszíni fizikai kiszolgálók értékelésére és áttelepítésére vonatkozó támogatási beállításokat és korlátozásokat foglalja össze.



## <a name="physical-server-scenarios"></a>Fizikai kiszolgálói forgatókönyvek

A táblázat összefoglalja a fizikai kiszolgálók támogatott forgatókönyveit.

**Üzembe helyezés** | **Részletek***
--- | ---
**Helyszíni fizikai kiszolgálók értékelése** | [Állítsa be](tutorial-prepare-physical.md) az első értékelést.<br/><br/> Értékelés [futtatása](tutorial-assess-physical.md) .
**Fizikai kiszolgálók áttelepítése az Azure-ba** | [Próbálja ki](tutorial-migrate-physical-virtual-machines.md) az Azure-ba való áttelepítést.


## <a name="azure-migrate-projects"></a>Azure Migrate projektek

**Támogatás** | **Részletek**
--- | ---
**Azure-engedélyek** | Azure Migrate projekt létrehozásához közreműködői vagy tulajdonosi engedélyekkel kell rendelkeznie az előfizetésben.
**Fizikai kiszolgálók** | Akár 35 000 fizikai kiszolgálót is megvizsgálhat egyetlen projektben. Egy Azure-előfizetéshez több projekt is tartozhat. Egy projekt tartalmazhatja a fizikai kiszolgálókat, a VMware virtuális gépeket és a Hyper-V virtuális gépeket, akár az értékelési korlátokig is.
**Régiócsoport** | Azure Migrate-projekteket számos földrajzi régióban is létrehozhatja. Bár bizonyos földrajzi területeken létrehozhat projekteket, a gépeket más célhelyekre is felhasználhatja vagy áttelepítheti. A projekt földrajza csak a felderített metaadatok tárolására szolgál.

  **Régiócsoport** | **Metaadatok tárolási helye**
  --- | ---
  Azure Government | USA-beli államigazgatás – Virginia
  Ázsia és a Csendes-óceáni térség | Kelet-Ázsia vagy Délkelet-Ázsia
  Ausztrália | Kelet-Ausztrália vagy Délkelet-Ausztrália
  Brazília | Dél-Brazília
  Kanada | Közép-Kanada vagy Kelet-Kanada
  Európa | Észak-Európa vagy Nyugat-Európa
  Franciaország | Közép-Franciaország
  India | Közép-India vagy Dél-India
  Japán |  Kelet-japán vagy Nyugat-Japán
  Korea | Korea középső régiója vagy Dél-Korea
  Egyesült Királyság | Egyesült Királyság déli régiója vagy Egyesült Királyság nyugati régiója
  Egyesült Államok | USA középső régiója vagy USA 2. nyugati régiója


 > [!NOTE]
 > A Azure Government támogatása jelenleg csak a Azure Migrate [régebbi verziójához](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) érhető el.


## <a name="assessment-physical-server-requirements"></a>Értékelés – fizikai kiszolgálói követelmények

| **Támogatás**                | **Részletek**               
| :-------------------       | :------------------- |
| **Fizikai kiszolgáló üzembe helyezése**       | A fizikai kiszolgáló önálló vagy egy fürtben is üzembe helyezhető. |
| **Engedélyek**           | **Windows:** Állítson be egy helyi felhasználói fiókot az összes olyan Windows-kiszolgálón, amelyet fel szeretne venni a felderítésbe. A felhasználói fiókot fel kell venni a következő csoportokhoz: Távoli asztal felhasználók, Teljesítményfigyelő felhasználók és Teljesítménynapló felhasználói. <br/> **Linux:** Szüksége lesz egy rendszergazdai fiókra a felderíteni kívánt Linux-kiszolgálókon. |
| **Operációs rendszer** | Az összes [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -és [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -operációs rendszer támogatott, a következők kivételével:<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="assessment-appliance-requirements"></a>Felmérés – készülékre vonatkozó követelmények

Az értékeléshez Azure Migrate egy könnyű berendezést futtat a fizikai kiszolgálók felderítéséhez, valamint a kiszolgálói metaadatok és teljesítményadatok küldéséhez a Azure Migrate. A készülék fizikai kiszolgálón vagy virtuális gépen is futtatható, és a Azure Portalból letöltött PowerShell-parancsfájl használatával állíthatja be. A következő táblázat összefoglalja a készülékre vonatkozó követelményeket.

| **Támogatás**                | **Részletek**               
| :-------------------       | :------------------- |
| **Berendezések üzembe helyezése**   |  A készüléket fizikai vagy virtuális gépen helyezheti üzembe.<br/>  A gazdagépnek Windows Server 2012 R2 vagy újabb rendszert kell futtatnia.<br/> A gazdagépnek elegendő helyet kell biztosítania 16 GB RAM, 8 vCPU, körülbelül 80 GB tárterület és egy külső kapcsoló lefoglalásához a készülék virtuális gépe számára.<br/> A készüléknek statikus vagy dinamikus IP-címnek, valamint internet-hozzáférésre van szüksége.
| **Azure Migrate projekt**  |  Egy készülék egyetlen projekthez is társítható.<br/> Tetszőleges számú berendezés társítható egyetlen projekthez.<br/> Egy projektben legfeljebb 35 000 gépet lehet felmérni.
| **Felfedezés**              | Egyetlen készülék akár 250-kiszolgálót is képes felderíteni.
| **Értékelési csoport**       | Egyetlen csoportban legfeljebb 35 000 gépet adhat hozzá.
| **Értékelés**             | Egyetlen értékeléssel akár 35 000 gépet is megvizsgálhat.


## <a name="assessment-appliance-url-access"></a>Felmérés – készülék URL-hozzáférése

A virtuális gépek felméréséhez az Azure Migrate berendezésnek internetkapcsolatra van szüksége.

- A berendezés telepítésekor a Azure Migrate az alábbi táblázatban összefoglalt URL-címek kapcsolatát.
- Ha URL-alapú proxyt használ, engedélyezze a hozzáférést a táblázatban lévő URL-címekhez, és győződjön meg arról, hogy a proxy feloldja az URL-címek keresése során fogadott CNAME-rekordokat.
- Ha elfogó proxyval rendelkezik, előfordulhat, hogy importálnia kell a kiszolgálói tanúsítványt a proxykiszolgálóhoz a készülékre.


**URL-cím** | **Részletek**  
--- | ---
*.portal.azure.com | Navigálás a Azure Portal
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | Jelentkezzen be az Azure-előfizetésébe
*.microsoftonline.com <br/> *.microsoftonline-p.com | Azure Active Directory-alkalmazások létrehozása a berendezés és a szolgáltatások közötti kommunikációhoz.
management.azure.com | Azure Active Directory-alkalmazások létrehozása a berendezés és a szolgáltatások közötti kommunikációhoz.
dc.services.visualstudio.com | Naplózás és figyelés
*.vault.azure.net | Azure Key Vault titkok kezelése a készülék és a szolgáltatás közötti kommunikáció során.
aka.ms/* | Hozzáférés engedélyezése az aka hivatkozásokhoz.
https://download.microsoft.com/download/* | A letölthető fájlok letöltése a Microsoft letöltési webhelyéről.



## <a name="assessment-port-requirements"></a>Értékelés – portra vonatkozó követelmények

A következő táblázat összefoglalja az értékeléshez szükséges portokra vonatkozó követelményeket.

**Eszköz** | **Kapcsolat**
--- | ---
**Berendezés** | Bejövő kapcsolatok a 3389-as TCP-porton, hogy engedélyezze a távoli asztali kapcsolatokat a berendezéssel.<br/> Bejövő kapcsolatok a 44368-as porton a berendezés-kezelő alkalmazás távoli eléréséhez az URL-cím használatával: ``` https://<appliance-ip-or-name>:44368 ```<br/> Kimenő kapcsolatok a 443, 5671 és 5672 portokon a felderítési és teljesítményi metaadatok küldéséhez Azure Migrate.
**Fizikai kiszolgálók** | **Windows:** Bejövő kapcsolatok a 443-es, 5989-as portokon a konfiguráció és a teljesítmény metaadatainak lekéréséhez Windows-kiszolgálókról. <br/> **Linux:**  Bejövő kapcsolatok a 22-es porton (UDP) a konfiguráció és a teljesítmény metaadatainak lekéréséhez Linux-kiszolgálókról. |


## <a name="next-steps"></a>Következő lépések

[Felkészülés a fizikai kiszolgáló](tutorial-prepare-physical.md) értékelésére a fizikai kiszolgálók értékeléséhez és áttelepítéséhez.
