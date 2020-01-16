---
title: A fizikai kiszolgáló értékelésének támogatása a Azure Migrate
description: Ismerje meg a fizikai kiszolgálók értékelésének támogatását a Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 32080605217cde78bd648ca6192f73d1025dea4c
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028776"
---
# <a name="support-matrix-for-physical-server-assessment"></a>A fizikai kiszolgáló értékelésének támogatási mátrixa 

A [Azure Migrate szolgáltatással](migrate-overview.md) a gépeket kivizsgálhatja és áttelepítheti a Microsoft Azure felhőbe. Ez a cikk a helyszíni fizikai kiszolgálók értékelésére és áttelepítésére vonatkozó támogatási beállításokat és korlátozásokat foglalja össze.


## <a name="overview"></a>Áttekintés

Ha a helyszíni gépeket az Azure-ba való áttelepítésre szeretné felmérni, adja hozzá a Azure Migrate: Server Assessment eszközt egy Azure Migrate projekthez. Üzembe helyezi a [Azure Migrate berendezést](migrate-appliance.md). A készülék folyamatosan felfedi a helyszíni gépeket, és konfigurációs és teljesítményadatokat küld az Azure-nak. A gép felderítése után összegyűjtheti a felderített gépeket csoportokba, és értékelést futtathat egy csoport számára.

## <a name="limitations"></a>Korlátozások

**Támogatás** | **Részletek**
--- | ---
**Értékelési korlátok**| Akár 35 000 fizikai kiszolgálót is felderítheti és felbecsülheti egyetlen [projektben](migrate-support-matrix.md#azure-migrate-projects).
**Projekt korlátai** | Egy Azure-előfizetésben több projektet is létrehozhat. A projektek tartalmazhatják a VMware virtuális gépeket, a Hyper-V virtuális gépeket és a fizikai kiszolgálókat, akár az értékelési korlátokig is.
**Felderítés** | A Azure Migrate berendezés akár 250 fizikai kiszolgálót is képes észlelni.
**Értékelés** | Egyetlen csoportban legfeljebb 35 000 gépet adhat hozzá.<br/><br/> Egyetlen értékeléssel akár 35 000 gépet is megvizsgálhat.

[További](concepts-assessment-calculation.md) információ az értékelésekről.




## <a name="physical-server-requirements"></a>A fizikai kiszolgáló követelményei

| **Támogatás**                | **Részletek**               
| :-------------------       | :------------------- |
| **Fizikai kiszolgáló üzembe helyezése**       | A fizikai kiszolgáló önálló vagy egy fürtben is üzembe helyezhető. |
| **Engedélyek**           | **Windows:** Állítson be egy helyi felhasználói fiókot az összes olyan Windows-kiszolgálón, amelyet fel szeretne venni a felderítésbe. A felhasználói fiókot fel kell venni a következő csoportokhoz: Távoli asztal felhasználók, Teljesítményfigyelő felhasználók és Teljesítménynapló felhasználói. <br/> **Linux:** Szüksége lesz egy rendszergazdai fiókra a felderíteni kívánt Linux-kiszolgálókon. |
| **Operációs rendszer** | Az összes [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -és [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -operációs rendszer támogatott, a következők kivételével:<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate készülékre vonatkozó követelmények

A Azure Migrate a [Azure Migrate berendezést](migrate-appliance.md) használja a felderítéshez és értékeléshez. A fizikai kiszolgálók berendezése virtuális gépen vagy fizikai gépen is futtatható. A Azure Portalból letöltött PowerShell-parancsfájl használatával állíthatja be.

- Ismerje meg a fizikai kiszolgálók [készülékre vonatkozó követelményeit](migrate-appliance.md#appliance---physical) .
- További információ a készülék eléréséhez szükséges [URL-címekről](migrate-appliance.md#url-access) .

## <a name="port-access"></a>Port-hozzáférés

A következő táblázat összefoglalja az értékeléshez szükséges portokra vonatkozó követelményeket.

**Eszköz** | **Kapcsolat**
--- | ---
**Berendezés** | Bejövő kapcsolatok a 3389-as TCP-porton, hogy engedélyezze a távoli asztali kapcsolatokat a berendezéssel.<br/> Bejövő kapcsolatok a 44368-as porton a berendezés-kezelő alkalmazás távoli eléréséhez az URL-cím használatával: ``` https://<appliance-ip-or-name>:44368 ```<br/> Kimenő kapcsolatok a 443, 5671 és 5672 portokon a felderítési és teljesítményi metaadatok küldéséhez Azure Migrate.
**Fizikai kiszolgálók** | **Windows:** Bejövő kapcsolatok a 443-as porton, WinRM-portok 5985 (HTTP) és 5986 (HTTPS) a konfiguráció és a teljesítmény metaadatainak lekéréséhez a Windows-kiszolgálókról. <br/> **Linux:**  Bejövő kapcsolatok a 22-es porton (UDP) a konfiguráció és a teljesítmény metaadatainak lekéréséhez Linux-kiszolgálókról. |


## <a name="next-steps"></a>Következő lépések

[Felkészülés a fizikai kiszolgáló értékelésére](tutorial-prepare-physical.md).
