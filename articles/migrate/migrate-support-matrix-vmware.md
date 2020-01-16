---
title: VMware Assessment-támogatás a Azure Migrate
description: Ismerkedjen meg az Azure Migrate VMware Assessment támogatásával.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 2a9c5504d99f439723a250b619b9f9b660ea9c59
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028999"
---
# <a name="support-matrix-for-vmware-assessment"></a>A VMware Assessment támogatási mátrixa 

Ez a cikk a VMware virtuális gépek [Azure Migrate: kiszolgáló értékelésével](migrate-services-overview.md#azure-migrate-server-migration-tool)való értékelésének támogatási beállításait és korlátozásait foglalja össze. Ha a VMware virtuális gépek Azure-ba történő áttelepítésével kapcsolatos információkat keres, tekintse át az [áttelepítési támogatási mátrixot](migrate-support-matrix-vmware-migration.md).

## <a name="overview"></a>Áttekintés

Ha a helyszíni gépeket az Azure-ba való áttelepítésre szeretné felmérni, adja hozzá a Azure Migrate: Server Assessment eszközt egy Azure Migrate projekthez. Üzembe helyezi a [Azure Migrate berendezést](migrate-appliance.md). A készülék folyamatosan felfedi a helyszíni gépeket, és konfigurációs és teljesítményadatokat küld az Azure-nak. A gép felderítése után a felderített gépeket csoportokba gyűjtheti, és értékelést futtathat egy csoport számára.


## <a name="limitations"></a>Korlátozások

**Támogatás** | **Részletek**
--- | ---
**Értékelési korlátok**| Akár 35 000 VMware virtuális gép felderítése és értékelése egyetlen [projektben](migrate-support-matrix.md#azure-migrate-projects).
**Projekt korlátai** | Egy Azure-előfizetésben több projektet is létrehozhat. A projektek tartalmazhatják a VMware virtuális gépeket, a Hyper-V virtuális gépeket és a fizikai kiszolgálókat, akár az értékelési korlátokig is.
**Felderítés** | A Azure Migrate készülék legfeljebb 10 000 VMware virtuális gépet képes felderíteni egy vCenter Server.
**Értékelés** | Egyetlen csoportban legfeljebb 35 000 gépet adhat hozzá.<br/><br/> Egyetlen értékeléssel akár 35 000 virtuális gépet is megvizsgálhat.

[További](concepts-assessment-calculation.md) információ az értékelésekről.


## <a name="application-discovery"></a>Alkalmazásfelderítés

A gépek felderítése mellett Azure Migrate: a kiszolgáló értékelése a gépeken futó alkalmazásokat, szerepköröket és szolgáltatásokat is képes észlelni. Az alkalmazás leltárának felderítése lehetővé teszi a helyszíni munkaterhelésekhez igazított áttelepítési útvonal azonosítását és megtervezését. 

**Támogatás** | **Részletek**
--- | ---
Felderítés | A felderítés ügynök nélkül, a számítógép vendég hitelesítő adataival és a számítógépek távoli elérésével a WMI-és SSH-hívásokkal.
Támogatott gépek | Helyszíni VMware virtuális gépek.
Gépi operációs rendszer | Minden Windows-és Linux-verzió
Hitelesítő adatok | A jelenleg egyetlen hitelesítő adat használatát támogatja az összes Windows-kiszolgálón, valamint egy hitelesítő adatot az összes Linux-kiszolgáló számára.<br/><br/> Létrehoz egy vendég felhasználói fiókot a Windows rendszerű virtuális gépekhez, valamint egy normál/normál felhasználói fiókot (nem sudo hozzáférés) az összes Linux rendszerű virtuális géphez.
Korlátozások | Az App-Discovery esetében akár 10000 is felderíthető. 

## <a name="vmware-requirements"></a>VMware-követelmények

**VMware** | **Részletek**
--- | ---
**vCenter Server** | A felderítéshez és értékeléshez használni kívánt gépeket a 5,5, 6,0, 6,5 vagy 6,7 vCenter Server-es verzióval kell felügyelni.
**Engedélyek (Assessment)** | írásvédett fiók vCenter Server.
**Engedélyek (App-Discovery)** | vCenter Server fiók csak olvasási hozzáféréssel, és a virtuális gépek számára engedélyezett jogosultságokkal > vendég műveletekkel.
**Engedélyek (függőségi vizualizáció)** | A központi kiszolgáló fiók csak olvasási hozzáféréssel, valamint a **virtuális gépekhez** > **vendég műveletekhez**engedélyezett jogosultságokkal.


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate készülékre vonatkozó követelmények

A Azure Migrate a [Azure Migrate berendezést](migrate-appliance.md) használja a felderítéshez és értékeléshez. A VMware rendszerhez készült készülék a vCenter Serverba importált PETESEJT-sablonnal lett üzembe helyezve. 

- A VMware-es [készülékekre vonatkozó követelmények](migrate-appliance.md#appliance---vmware) ismertetése.
- További információ a készülék eléréséhez szükséges [URL-címekről](migrate-appliance.md#url-access) .

## <a name="port-access"></a>Port-hozzáférés

**Eszköz** | **Kapcsolat**
--- | ---
Berendezés | Bejövő kapcsolatok a 3389-as TCP-porton, hogy engedélyezze a távoli asztali kapcsolatokat a berendezéssel.<br/><br/> Bejövő kapcsolatok a 44368-as porton a berendezés-kezelő alkalmazás távoli eléréséhez az URL-cím használatával: ```https://<appliance-ip-or-name>:44368``` <br/><br/>A 443-es, 5671-as és 5672-es porton kimenő kapcsolatok a felderítési és a teljesítménybeli metaadatok küldésére Azure Migrate.
vCenter-kiszolgáló | A 443-es TCP-porton bejövő kapcsolatok lehetővé teszik, hogy a berendezés konfigurációs és teljesítménybeli metaadatokat gyűjtsön az értékelésekhez. <br/><br/> A készülék alapértelmezés szerint az 443-as porton csatlakozik a vCenter-hez. Ha a vCenter-kiszolgáló egy másik portot figyel, akkor a felderítés beállításakor módosíthatja a portot.

## <a name="dependency-visualization"></a>Függőségek vizualizációja

A függőségi vizualizáció segítséget nyújt a függőségek megjelenítéséhez a felmérni és áttelepíteni kívánt gépek között. Jellemzően függőségi leképezést használ, ha a magasabb szintű megbízhatóságú gépeket szeretné felmérni. A VMware virtuális gépek esetében a függőségi vizualizáció a következőképpen támogatott:

- **Ügynök nélküli függőségi vizualizáció**: Ez a beállítás jelenleg előzetes verzióban érhető el. Nem szükséges ügynököket telepíteni a gépekre.
    - Úgy működik, hogy rögzíti a TCP-kapcsolatok adatait azokról a gépekről, amelyeken engedélyezve van. A függőségi felderítés elindítása után a készülék öt perces lekérdezési időköz alapján gyűjt adatokat a gépekről.
    - A rendszer a következő adatokat gyűjti össze:
        - TCP-kapcsolatok
        - Aktív kapcsolattal rendelkező folyamatok nevei
        - A fenti folyamatokat futtató telepített alkalmazások nevei
        - Nem. az összes lekérdezési időszakban észlelt kapcsolatok
- **Ügynök-alapú függőségi vizualizáció**: az ügynök-alapú függőségi vizualizáció használatához le kell töltenie és telepítenie kell az alábbi ügynököket minden olyan helyszíni gépen, amelyet elemezni szeretne.
    - Telepítse a Microsoft monitoring Agent (MMA) szolgáltatást mindegyik gépre. [További](how-to-create-group-machine-dependencies.md#install-the-mma) információ az MMA-ügynök telepítéséről.
    - Telepítse a függőségi ügynököt az egyes gépekre. [További](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) információ a függőségi ügynök telepítéséről.
    - Továbbá ha olyan gépekkel rendelkezik, amelyeken nincs internetkapcsolat, ezekre le kell töltenie és telepítenie kell a Log Analytics-átjárót.


## <a name="next-steps"></a>Következő lépések

- [Tekintse át](best-practices-assessment.md) az értékelések létrehozásával kapcsolatos ajánlott eljárásokat.
- [Felkészülés a VMware](tutorial-prepare-vmware.md) -értékelésre.
