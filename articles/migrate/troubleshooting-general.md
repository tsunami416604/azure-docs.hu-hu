---
title: Azure Migrate problémák elhárítása | Microsoft Docs
description: Áttekintést nyújt a Azure Migrate szolgáltatás ismert problémáiról, valamint a gyakori hibákkal kapcsolatos hibaelhárítási tippekről.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: raynew
ms.openlocfilehash: 0e2a8f269a98babc17f36ceff209ee2f057e6911
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302310"
---
# <a name="troubleshoot-azure-migrate"></a>Az Azure Migrate hibaelhárítása

A [Azure Migrate](migrate-services-overview.md) a Microsoft eszközeinek egy központját biztosítja az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártók (ISV) ajánlatait. Ez a dokumentum segítséget nyújt a hibák elhárításához a Azure Migrate Azure Migrate: Kiszolgáló értékelése és Azure Migrate: Kiszolgáló áttelepítése.

## <a name="azure-migrate-project-issues"></a>A projekttel kapcsolatos problémák Azure Migrate

### <a name="i-am-unable-to-find-my-existing-azure-migrate-project"></a>Nem találom a meglévő Azure Migrate projektet.

A Azure Migrate [két verziója](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) létezik. Attól függően, hogy melyik verziót hozta létre a projektben, kövesse az alábbi lépéseket a projekt megkereséséhez:

1. Ha olyan projektet keres, amely Azure Migrate áteresztő (régi) verziójával lett létrehozva, a projekt megkereséséhez kövesse az alábbi lépéseket.

    1. Lépjen a [Azure Portalra](https://portal.azure.com), és keressen rá a **Azure Migrate**kifejezésre.
    2. A Azure Migrate irányítópultján megjelenik egy szalagcím, amely a régebbi projektek elérését tárgyalja. Ez a szalagcím csak akkor jelenik meg, ha létrehozott egy projektet a régi felülettel. Kattintson a szalagcímre.

    ![Meglévő projektek elérése](./media/troubleshooting-general/access-existing-projects.png)

    3. Ekkor megjelenik a Azure Migrate korábbi verziójával létrehozott meglévő projektek listája.

2. Ha az aktuális verzióval (új felülettel) létrehozott projektet keres, kövesse az alábbi lépéseket a projekt megkereséséhez.

    1. Lépjen a [Azure Portalra](https://portal.azure.com), és keressen rá a **Azure Migrate**kifejezésre.
    2. A Azure Migrate irányítópulton lépjen a **kiszolgálók** lapra a bal oldali ablaktáblán, és válassza a **módosítás** lehetőséget a jobb felső sarokban:

    ![Váltás meglévő Azure Migrate projektre](./media/troubleshooting-general/switch-project.png)

    3. Válassza ki a  megfelelő előfizetést, és **telepítse át a projektet**.

### <a name="i-am-unable-to-create-a-second-azure-migrate-project"></a>Nem tudok második Azure Migrate projektet létrehozni.

Új Azure Migrate projekt létrehozásához kövesse az alábbi lépéseket.

1. Lépjen a [Azure Portalra](https://portal.azure.com), és keressen rá a **Azure Migrate**kifejezésre.
2. A Azure Migrate irányítópulton lépjen a **kiszolgálók** lapra a bal oldali ablaktáblán, és válassza a **módosítás** lehetőséget a jobb felső sarokban:

   ![Azure Migrate projekt módosítása](./media/troubleshooting-general/switch-project.png)

3. Új projekt létrehozásához válassza az alábbi képernyőképen látható **kattintson ide** .

   ![Második Azure Migrate projekt létrehozása](./media/troubleshooting-general/create-new-project.png)

### <a name="deletion-of-azure-migrate-projects-and-associated-log-analytics-workspace"></a>Azure Migrate projektek és a társított Log Analytics munkaterület törlése

Azure Migrate-projekt törlésekor törli az áttelepítési projektet a felderített gépek metaadataival együtt. Ha azonban csatolt egy Log Analytics munkaterületet a kiszolgáló-értékelési eszközhöz, az nem törli automatikusan a Log Analytics munkaterületet. Ennek az az oka, hogy ugyanaz a Log Analytics munkaterület több felhasználási esethez is használható. Ha a Log Analytics munkaterületet is törölni szeretné, manuálisan kell elvégeznie.

1. Tallózással keresse meg a projekthez csatolt Log Analytics munkaterületet.
     1. Ha még nem törölte az áttelepítési projektet, a munkaterületre mutató hivatkozást az alapvető erőforrások szakasz kiszolgáló értékelése áttekintése lapján találja.

     ![LA munkaterület](./media/troubleshooting-general/loganalytics-workspace.png)

     2. Ha már törölte az áttelepítési projektet, válassza az **erőforráscsoportok** lehetőséget a Azure Portal bal oldali ablaktábláján. Keresse meg azt az erőforráscsoportot, amelyben a munkaterületet létrehozták, majd keresse meg.
2. A munkaterület törléséhez kövesse az [ebben a cikkben](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) szereplő utasításokat.

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>Az áttelepítési projekt létrehozása nem sikerült – a hibaüzeneteknek *tartalmazniuk kell a felhasználói azonosító fejléceit*

Ez a probléma olyan felhasználók számára fordulhat elő, akik nem férnek hozzá a szervezet Azure Active Directory (Azure AD) bérlője számára. Amikor első alkalommal ad hozzá egy felhasználót egy Azure AD-bérlőhöz, egy e-mailt kap a bérlőhöz való csatlakozáshoz. A felhasználóknak meg kell nyitniuk az e-mailt, és el kell fogadniuk a meghívót, hogy sikeresen bekerüljön a bérlőbe. Ha nem látja az e-mailt, forduljon egy olyan felhasználóhoz, aki már rendelkezik hozzáféréssel a bérlőhöz, és kérje meg őket, hogy küldje el újra a meghívót az [itt](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)megadott lépések használatával.

A meghívót tartalmazó e-mail fogadása után nyissa meg az e-mailt, és kattintson az e-mailben található hivatkozásra a meghívás elfogadásához. Ha ez megtörtént, ki kell jelentkeznie Azure Portal és újra be kell jelentkeznie. a böngésző frissítése nem fog működni. Ezután megpróbálkozhat az áttelepítési projekt létrehozásával.

## <a name="appliance-issues"></a>Készülékkel kapcsolatos problémák

### <a name="deployment-of-azure-migrate-appliance-for-vmware-failed-with-the-error-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry"></a>A következő hiba miatt nem sikerült üzembe helyezést végrehajtani a VMware Azure Migrate készülékén: A megadott jegyzékfájl érvénytelen: Érvénytelen OVF jegyzék-bejegyzés.

1. Ellenőrizze, hogy a Azure Migrate készülék PETESEJTJEInek fájlja helyesen van-e letöltve a kivonatoló értékének ellenőrzésével. Tekintse meg a következő [cikket](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware#verify-the-collector-appliance) a kivonatérték ellenőrzéséhez. Ha a kivonatoló érték nem megfelelő, töltse le újra a PETESEJT-fájlt, majd próbálja megismételni a telepítést.
2. Ha továbbra is sikertelen, és a VMware vSphere-ügyfelet használja az OVF telepítéséhez, próbálja meg a vSphere webes ügyfélen keresztül telepíteni. Ha továbbra sem sikerül, próbálkozzon más webböngésző használatával.
3. Ha vSphere webes ügyfélprogramot használ, és vCenter Server 6,5-es vagy 6,7-es verzióra próbálja telepíteni, próbálja meg közvetlenül telepíteni a PETESEJTeket az ESXi-gazdagépen az alábbi lépések végrehajtásával:
   - Kapcsolódjon közvetlenül az ESXi-gazdagéphez (vCenter Server helyett) a webes ügyfél használatával (https://<*gazdagép IP-címe*>/UI).
   - Nyissa meg a **Kezdőlap** > **leltárt**.
   - Kattintson a **fájl** > **telepítése OVF sablon**elemre. Tallózással keresse meg a PETESEJTeket, és fejezze be a telepítést.
4. Ha az üzembe helyezés továbbra is sikertelen, forduljon Azure Migrate támogatási szolgálathoz.

### <a name="appliance-is-not-able-to-connect-to-the-internet"></a>A készülék nem tud csatlakozni az internethez

Ez akkor fordulhat elő, ha a használt gép proxy mögött van. Győződjön meg arról, hogy megadja az engedélyezési hitelesítő adatokat, ha a proxynak szüksége van rá.
Ha bármilyen URL-alapú tűzfal-proxyt használ a kimenő kapcsolatok vezérléséhez, ügyeljen arra, hogy ezeket a szükséges URL-címeket hozzáadja az engedélyezési listához:

Forgatókönyv | URL-lista
--- | ---
Kiszolgáló-értékelés VMware-hez | [itt](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
A Hyper-V kiszolgálói értékelése | [itt](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
Kiszolgáló áttelepítése VMware-re (ügynök nélkül) | [itt](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
Kiszolgáló áttelepítése VMware-re (ügynök-alapú) | [itt](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
Kiszolgáló áttelepítése Hyper-V-re | [itt](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

Ha elfogó proxyt használ az internethez való csatlakozáshoz, importálnia kell a proxy-tanúsítványt a berendezés virtuális gépén. A proxy tanúsítványát az [itt](https://docs.microsoft.com/azure/migrate/concepts-collector)részletesen ismertetett lépések segítségével importálhatja.

### <a name="error-802-date-and-time-synchronization-error"></a>802-es hiba: Dátum-és időszinkronizálási hiba

Előfordulhat, hogy a kiszolgáló órája több mint öt perc alatt szinkronizálva van az aktuális idővel. Módosítsa a gyűjtő virtuális gép órájának időpontját úgy, hogy az megfeleljen az aktuális időpontnak, az alábbiak szerint:

1. Nyisson meg egy rendszergazdai parancssort a virtuális gépen.
2. Az időzóna ellenõrzéséhez futtassa a w32tm/TZ.
3. Az idő szinkronizálásához futtassa a w32tm/resync.


### <a name="error-unabletoconnecttoserver"></a>UnableToConnectToServer hiba

Nem lehet csatlakozni a „Servername.com:9443” vCenter-kiszolgálóhoz a következő hiba miatt: Nem figyelt olyan végpont a https://Servername.com:9443/sdk címen, amely fogadni tudta volna az üzenetet.

Ellenőrizze, hogy a gyűjtő berendezés legújabb verzióját futtatja-e, ha nem, frissítse a készüléket a [legújabb verzióra](https://docs.microsoft.com/azure/migrate/concepts-collector).

Ha a probléma továbbra is a legújabb verziónál fordul elő, annak oka az lehet, hogy a gyűjtő számítógép nem tudja feloldani a megadott vCenter Server nevet, vagy a megadott port helytelen. Alapértelmezés szerint, ha a port nincs megadva, a gyűjtő megpróbál csatlakozni a 443-as számú porthoz.

1. Próbálja meg pingelni a Servername.com a gyűjtő gépről.
2. Ha az 1. lépés meghiúsul, próbáljon meg az IP-címen keresztül csatlakozni a vCenter-kiszolgálóhoz.
3. Adja meg a megfelelő portszámot a vCenterhez történő csatlakozáshoz.
4. Végezetül pedig ellenőrizze, hogy a vCenter-kiszolgáló fut-e.

## <a name="discovery-issues"></a>Felderítési problémák

### <a name="i-started-discovery-but-i-dont-see-the-discovered-vms-on-azure-portal-server-assessment-and-server-migrate-tiles-show-a-status-of-discovery-in-progress"></a>Elindítottam a felderítést, de nem látom a felderített virtuális gépeket Azure Portal. A kiszolgálók értékelése és a kiszolgáló átmigrálása a "felderítés folyamatban" állapotot jeleníti meg
A felderítésnek a készülékről való elindítása után némi időbe telik, amíg a felderített gépek megjelennek a Azure Portal. A VMware-felderítés körülbelül 15 percet vesz igénybe, és a Hyper-V felderítéséhez hozzáadott gazdagépeken körülbelül 2 perc van. Ha továbbra is a "felderítés folyamatban" állapotot látja, akkor a **kiszolgálók** lapon kattintson a **frissítés** gombra. Ennek meg kell jelennie a felderített kiszolgálók számának a kiszolgáló-értékelési és a kiszolgáló-áttelepítési csempén.


### <a name="i-am-using-the-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-being-shown-in-the-portal"></a>Olyan készüléket használok, amely folyamatosan felfedi a helyszíni környezetet, de a helyszíni környezetben törölt virtuális gépek továbbra is megjelennek a portálon.

Akár 30 percet is igénybe vehet, hogy a berendezés által összegyűjtött felderítési adatok tükrözzék a portálon. Ha 30 perc után sem jelenik meg naprakész információ, a következő lépések végrehajtásával állítson ki frissítést az adatokról:

1. A kiszolgálók > Azure Migrate: Kiszolgáló értékelése, kattintson az **Áttekintés**elemre.
2. A **kezelés**alatt kattintson a **Agent Health** elemre.
3. Az **ügynök frissítéséhez**kattintson a lehetőségre. Ezt az alábbi lehetőséget fogja látni az ügynökök listájának alatt.
4. Várjon, amíg a frissítési művelet befejeződik. Ellenőrizze, hogy látható-e naprakész információ a virtuális gépekről.

### <a name="i-dont-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>Nem a legfrissebb információk a helyszíni virtuális gépekről a portálon

Akár 30 percet is igénybe vehet, hogy a berendezés által összegyűjtött felderítési adatok tükrözzék a portálon. Ha 30 perc után sem jelenik meg naprakész információ, a következő lépések végrehajtásával állítson ki frissítést az adatokról:

1. A kiszolgálók > Azure Migrate: Kiszolgáló értékelése, kattintson az **Áttekintés**elemre.
2. A **kezelés**alatt kattintson a **Agent Health** elemre.
3. Az **ügynök frissítéséhez**kattintson a lehetőségre. Ez a lehetőség az ügynökök listájának alatt jelenik meg.
4. Várjon, amíg a frissítési művelet befejeződik. Ekkor a virtuális gépek naprakész információit kell látnia.

### <a name="unable-to-connect-to-hosts-or-cluster-as-the-server-name-cannot-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>Nem lehet csatlakozni a gazdagéphez vagy fürthöz, mert a kiszolgáló neve nem oldható fel. WinRM-hibakód: 0x803381B9 (hiba azonosítója: 50004)
Ez a hiba akkor fordul elő, ha a készüléket kiszolgáló DNS nem tudja feloldani a megadott fürtöt vagy állomásnevet. Ha ezt a hibát látja a fürtön, próbálja meg a fürt teljes tartománynevét biztosítani. 

Ezt a hibát a fürtben található gazdagépek esetében is megtekintheti. Ebben az esetben a berendezés kapcsolódhat a fürthöz. A fürt azonban olyan állomásnévket adott vissza, amelyek nem teljesen minősített tartománynevek. 

A hiba megoldásához frissítse a gazdagépek fájlját a készüléken az IP-cím és az állomásnevek leképezésének hozzáadásával.
1. Nyissa meg a Jegyzettömböt rendszergazda felhasználóként. A C:\Windows\System32\Drivers\etc\hosts. fájl megnyitása
2. Adja hozzá az IP-címet és az állomásnevet egy sorban. Ismételje meg az összes olyan gazdagépet vagy fürtöt, ahol ez a hiba látható.
3. Mentse és zárd be a Hosts fájlt.
4. Megtekintheti, hogy a készülék tud-e csatlakozni a gazdagépekhez a berendezés-kezelő alkalmazás használatával. 30 perc elteltével a Azure Portalon láthatja a legújabb információkat a gazdagépeken. 


## <a name="assessment-issues"></a>Értékelési problémák

### <a name="azure-readiness-issues"></a>Azure-készültségi problémák

Probléma | Szervizelés
--- | ---
Nem támogatott rendszerindítási típus | Az Azure nem támogatja az EFI rendszerindítási típussal rendelkező virtuális gépeket. Javasoljuk, hogy az áttelepítés futtatása előtt alakítsa át a rendszerindítási típust BIOS-ra. <br/><br/>Az ilyen virtuális gépek áttelepítésének elvégzéséhez Azure Migrate kiszolgáló áttelepítését használhatja, mivel a virtuális gép rendszerindítási típusát átalakítja a BIOS-ba az áttelepítés során.
Feltételesen támogatott Windows operációs rendszer | Az operációs rendszer elvégezte a támogatási dátum végét, és egyéni támogatási szerződést (CSA) igényel az [Azure támogatásához](https://aka.ms/WSosstatement), érdemes frissíteni az operációs rendszert az Azure-ba való Migrálás előtt.
Nem támogatott Windows operációs rendszer | Az Azure csak a [kiválasztott Windows operációsrendszer](https://aka.ms/WSosstatement)-verziókat támogatja, érdemes lehet a gép operációs rendszerét frissíteni az Azure-ba való Migrálás előtt.
Feltételesen támogatott Linux operációs rendszer | Az Azure csak a [kiválasztott LINUXOS operációsrendszer](../virtual-machines/linux/endorsed-distros.md)-verziókat támogatja, érdemes lehet a gép operációs rendszerének frissítését az Azure-ba való Migrálás előtt.
Nem támogatott Linux operációs rendszer | A gép elindítható az Azure-ban, de az Azure nem biztosít operációsrendszer-támogatást, érdemes lehet az operációs rendszert egy [támogatott Linux](../virtual-machines/linux/endorsed-distros.md) -verzióra frissíteni az Azure-ba való Migrálás előtt
Ismeretlen operációs rendszer | A virtuális gép operációs rendszere "other" (vCenter Server) lett megadva, mivel a Azure Migrate nem tudja azonosítani a virtuális gép Azure-felkészültségét. A gép migrálása előtt győződjön meg arról, hogy az Azure [támogatja](https://aka.ms/azureoslist) a GÉPEN futó operációs rendszert.
Nem támogatott operációsrendszer-bitszáma | Az 32 bites operációs rendszert futtató virtuális gépek az Azure-ban is elindíthatók, de javasoljuk, hogy a virtuális gép operációs rendszerét 32-bitesről 64-bitesre frissítse az Azure-ba való Migrálás előtt.
Visual Studio-előfizetést igényel. | A gépen egy Windows-ügyfél operációs rendszer fut, amely csak a Visual Studio-előfizetésben támogatott.
Nem található virtuális gép a szükséges tárolási teljesítményhez. | A gép számára szükséges tárolási teljesítmény (IOPS/átviteli sebesség) meghaladja az Azure-beli virtuális gépek támogatását. Csökkentse a gép tárolási követelményeit az áttelepítés előtt.
Nem található virtuális gép a szükséges hálózati teljesítményhez. | A gép számára szükséges hálózati teljesítmény (be/ki) meghaladja az Azure-beli virtuális gépek támogatását. Csökkentse a gép hálózati követelményeit.
A virtuális gép nem található a megadott helyen. | A Migrálás előtt használjon másik célhelyet.
Egy vagy több nem megfelelő lemez. | A virtuális géphez csatolt egy vagy több lemez nem felel meg az Azure követelményeinek. A virtuális géphez csatlakoztatott minden lemez esetében ügyeljen arra, hogy a lemez mérete < 4 TB legyen, ha nem, a lemez méretének csökkentése az Azure-ba való áttelepítés előtt. Győződjön meg arról, hogy az egyes lemezek által igényelt teljesítmény (IOPS/átviteli sebesség) támogatott az Azure által [felügyelt virtuálisgép-lemezek](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)esetében.   
Egy vagy több nem megfelelő hálózati adapter. | A nem használt hálózati adapterek eltávolítása a gépről az áttelepítés előtt.
A lemezek száma meghaladja a korlátot | Az áttelepítés előtt távolítsa el a nem használt lemezeket a gépről.
A lemez mérete meghaladja a korlátot | Az Azure legfeljebb 4 TB méretű lemezeket támogat. Az áttelepítés előtt a lemezeket 4 TB-nál kevesebbre csökkentheti.
A lemez nem érhető el a megadott helyen | A Migrálás előtt ellenőrizze, hogy a lemez a célhelyen van-e.
A lemez nem érhető el a megadott redundancia esetén | A lemeznek az értékelési beállításokban definiált redundancia-tárolási típust kell használnia (alapértelmezés szerint LRS).
Belső hiba miatt nem sikerült meghatározni a lemez alkalmasságát | Próbálja meg létrehozni a csoport új értékelését.
Nem található a szükséges magokkal és memóriával rendelkező virtuális gép | Az Azure nem tudott megfelelő virtuálisgép-típust kimutatni. A Migrálás előtt csökkentse a helyszíni gép memóriáját és a magok számát.
Belső hiba miatt nem sikerült meghatározni a virtuális gép alkalmasságát. | Próbálja meg létrehozni a csoport új értékelését.
Belső hiba miatt nem sikerült meghatározni egy vagy több lemez alkalmasságát. | Próbálja meg létrehozni a csoport új értékelését.
Belső hiba miatt nem sikerült meghatározni egy vagy több hálózati adapter megfelelőségét. | Próbálja meg létrehozni a csoport új értékelését.

### <a name="i-am-unable-to-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>Nem tudom megadni az Nagyvállalati Szerződés (EA) szolgáltatást Azure-ajánlatként az értékelési tulajdonságok között?
Azure Migrate: A kiszolgáló értékelése jelenleg nem támogatja a Nagyvállalati Szerződés (EA) alapú díjszabást. A megkerülő megoldás az, ha az "utólagos elszámolású" lehetőséget használja az Azure-ajánlathoz, és a "kedvezmény" tulajdonsággal megadja a kapott egyéni kedvezményeket. [További információ az értékelés testreszabásáról](https://aka.ms/migrate/selfhelp/eapricing).

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready-is-there-anything-i-need-to-do-to-fix-this"></a>Miért jelzi a kiszolgáló értékelése a Linux rendszerű virtuális gépek feltételeit. Van valami, amit végre kell hajtani a probléma megoldásához?
Létezik egy ismert rés a kiszolgáló értékelése során, amely nem képes észlelni a helyszíni virtuális gépeken telepített Linux operációs rendszer alverzióját (például a RHEL 6,10-as verzió esetében a kiszolgáló értékelése jelenleg csak a RHEL 6 operációs rendszert észleli). Mivel az Azure csak a Linux bizonyos verzióit támogatja, a Linux rendszerű virtuális gépek jelenleg feltételesen készen állnak a kiszolgáló értékelése során. Manuálisan is ellenőrizheti, hogy a helyszíni virtuális gépen futó Linux operációs rendszer az Azure-ban az [Azure Linux támogatási dokumentációjának](https://aka.ms/migrate/selfhost/azureendorseddistros)áttekintésével van-e jóváhagyva. A támogatott disztribúció ellenőrzése után figyelmen kívül hagyhatja ezt a figyelmeztetést.

### <a name="the-vm-sku-recommended-by-server-assessment-has-more-number-of-cores-and-a-larger-memory-size-than-what-is-allocated-on-premises-why-is-that-so"></a>A kiszolgáló értékelése által ajánlott virtuálisgép-SKU több magot tartalmaz, és nagyobb a memória mérete, mint a helyszínen lefoglalt mennyiség. Miért van így?
A kiszolgáló-értékelés virtuálisgép-SKU-ajánlása az értékelés tulajdonságaitól függ. Kétféle értékelést hozhat létre a kiszolgáló értékelése, a "teljesítmény-alapú" és a "helyszíni" értékelések alapján. A teljesítmény-alapú értékelések esetében a kiszolgáló értékelése a helyszíni virtuális gépek (CPU, memória, lemez és hálózat kihasználtsága) kihasználtsági adatait veszi figyelembe a helyszíni virtuális gépekhez tartozó megfelelő virtuálisgép-SKU meghatározásához. Emellett a teljesítmény-alapú méretezéshez a komfort tényezőt is figyelembe kell venni a hatékony kihasználtság azonosítása érdekében. A helyszíni méretezés esetén a teljesítményadatokat nem számítjuk fel, és a cél SKU-t a helyszíni lefoglalások alapján ajánljuk.

Tegyük fel például, hogy egy helyszíni virtuális gép 4 maggal és 8 GB memóriával rendelkezik, 50%-os CPU-kihasználtsággal és 50%-os memóriával, és az értékeléshez a 1,3 kényelmi tényezője van megadva. Ha az értékelés méretezési feltétele "mint helyszíni", 4 maggal és 8 GB memóriával rendelkező Azure VM SKU használata ajánlott, azonban ha a méretezési feltétel teljesítmény-alapú, a processzor és a memória tényleges kihasználtsága (50%-a 4 mag * 1,3 = 2,6 magok és a 8 GB-os 50%-a) memória * 1,3 = 5,3-GB memória), a négy mag (a legközelebbi támogatott alapszám) és a 8 GB-os memória mérete (a legközelebbi támogatott memória mérete) esetében ajánlott a legolcsóbb VM-SKU. [További információ arról, hogy a kiszolgáló értékelése hogyan végzi a méretezést.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing)

### <a name="the-disk-sku-recommended-by-server-assessment-has-a-bigger-size-than-what-is-allocated-on-premises-why-is-that-so"></a>A kiszolgáló értékelése által ajánlott lemez SKU mérete nagyobb, mint a helyszíni foglalás. Miért van így?
A kiszolgáló értékelése során a lemez méretezése két értékelési tulajdonságtól függ – a méretezési feltételtől és a tárolási típustól. Ha a méretezési feltétel "Performance-Based", és a tárolási típus értéke "Automatic", a lemez IOPS és átviteli sebessége a céllemez típusának (standard HDD, standard SSD vagy prémium lemez) azonosítására szolgál. A rendszer a lemez típusán belüli lemezes SKU-t javasolja, figyelembe véve a helyszíni lemez méretére vonatkozó követelményeket. Ha a méretezési feltétel "teljesítmény-alapú", és a tárolási típus a "Prémium", az Azure-ban prémium szintű lemezes SKU-t ajánlott használni a helyszíni lemez IOPS, átviteli sebessége és mérete alapján. Ugyanez a logikát használja a lemezek méretezésére, ha a méretezési feltétel "a helyszíni" méretezési és tárolási típus: "standard HDD", "standard SSD" vagy "Prémium".

Ha például egy helyszíni lemez 32 GB memóriával rendelkezik, de a lemez összesített olvasási és írási IOPS értéke 800 IOPS, a kiszolgáló értékelése a prémium szintű lemez típusát javasolja (a magasabb IOPS-követelmények miatt), majd javasoljon egy lemezes SKU-t, amely támogatja a a szükséges IOPS és-méret. Ebben a példában a legközelebbi egyezés P15 (256 GB, 1100 IOPS). Annak ellenére, hogy a helyszíni lemez által igényelt méret 32 GB volt, a Server Assessment a helyszíni lemez magas IOPS követelménye miatt nagyobb méretű lemezt javasolt.

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>A kiszolgáló értékelése alapján becsült számítási költségek tartalmazzák-e a virtuális gép operációsrendszer-licencének költségeit?
A kiszolgáló értékelése jelenleg csak a Windows rendszerű gépekre érvényes operációsrendszer-licencek árát veszi figyelembe, és a Linux RENDSZERű gépekre vonatkozó licencek ára jelenleg nem tekinthető meg. 

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Milyen hatással van a teljesítmény-előzményekre és a percentilis kihasználtságára a javasolt méret?
Ezek a tulajdonságok csak a "teljesítmény-alapú" méretezésre alkalmazhatók. A kiszolgáló értékelése folyamatosan gyűjti a helyszíni gépek teljesítményadatait, és azt használja az Azure-beli VM SKU és Disk SKU használatára. A kiszolgáló értékelése során az alábbi módon gyűjti a teljesítményadatokat:
- A Azure Migrate készülék folyamatosan a helyszíni környezetet gyűjti, hogy a VMware virtuális gépek esetében 20 másodpercenként, a Hyper-V virtuális gépek esetében pedig 30 másodpercenként gyűjtsön valós idejű kihasználtsági adatokat.
- A készülék összesíti a 20/30 másodperces mintákat, hogy egyetlen adatpontot hozzon létre 10 percenként. Az egyetlen adatpont létrehozásához a berendezés a 20/30 másodperces mintákból kiválasztja a csúcsérték értéket, és elküldi azt az Azure-nak.
- Amikor értékelést hoz létre a kiszolgáló értékelése során, a teljesítmény időtartama és a teljesítményi előzmények percentilis értéke alapján a rendszer azonosítja a reprezentatív kihasználtsági értéket. Ha például a teljesítmény előzményei egy hét, és a percentilis kihasználtsága 95., a Azure Migrate az utolsó egy hét összes 10 perces mintavételi pontját növekvő sorrendbe rendezi, majd kijelöli a 95. percentilis értéket a reprezentatív értékként.
A 95. percentilis értéke biztosítja, hogy figyelmen kívül hagyja a kiugró adatokat, amelyek akkor szerepelhetnek, ha kiveszi a esetek 99% percentilis értékét. Ha az időszakra vonatkozó csúcsérték-használatot szeretné kiválasztani, és nem szeretné figyelmen kívül hagyni a kiugró adatokat, válassza a esetek 99% percentilis lehetőséget a percentilis kihasználtsága lehetőségnél.

## <a name="dependency-visualization-issues"></a>Függőségi vizualizációval kapcsolatos problémák

### <a name="i-am-unable-to-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Nem találom a függőségi vizualizáció funkciót Azure Government projektekhez.

Azure Migrate a függőségi vizualizáció funkciójának Service Map függ, és mivel a Service Map jelenleg nem érhető el a Azure Governmentban, ez a funkció nem érhető el a Azure Governmentban.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>Telepítettem a Microsoft monitoring Agent (MMA) és a függőségi ügynököt a helyszíni virtuális gépeken, de a függőségek mostantól megjelennek a Azure Migrate portálon.

Miután telepítette az ügynököket, Azure Migrate általában 15-30 percet vesz igénybe, hogy megjelenjenek a függőségek a portálon. Ha több mint 30 percet várt, győződjön meg arról, hogy az MMA-ügynök tud kommunikálni az OMS-munkaterülettel az alábbi lépések végrehajtásával:

Windows rendszerű virtuális gép esetén:
1. Nyissa  meg a Vezérlőpultot, és indítsa el a **Microsoft monitoring agentet**.
2. Nyissa meg az **Azure log Analytics (OMS)** lapot az MMA tulajdonságok előugró ablakban.
3. Győződjön meg arról, hogy a munkaterület **állapota** zöld.
4. Ha az állapot nem zöld, próbálja meg eltávolítani a munkaterületet, és adja hozzá újra az MMA-hoz.
        ![MMA-állapot](./media/troubleshooting-general/mma-status.png)

Linux rendszerű virtuális gép esetén győződjön meg arról, hogy az MMA és a függőségi ügynök telepítési parancsai sikeresek voltak.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>Mik az MMA által támogatott operációs rendszerek?

Az MMA által támogatott Windows operációs rendszerek listája [itt](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)található.
Az MMA által támogatott Linux operációs rendszerek listája [itt](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)található.

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>Mik a függőségi ügynök által támogatott operációs rendszerek?

A függőségi ügynök által támogatott Windows operációs rendszerek listája [itt](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems)található.
A függőségi ügynök által támogatott Linux operációs rendszerek listája [itt](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems)található.

### <a name="i-am-unable-to-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>Nem tudom megjeleníteni a függőségeket Azure Migrateban több mint egy órányi időtartamra?
Azure Migrate lehetővé teszi a függőségek megjelenítését legfeljebb egy órás időtartamra. Bár a Azure Migrate lehetővé teszi, hogy egy adott dátumra térjen vissza az előzményekben az elmúlt egy hónapra, a függőségek megjelenítésének maximális időtartama legfeljebb 1 óra lehet. Használhatja például a függőségi Térkép időidőtartam funkcióját a tegnapi függőségeinek megtekintésére, de csak egy órás időszakra tekinthet meg. [A függőségi adat](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) hosszabb időtartamon keresztül történő lekérdezéséhez azonban Azure monitor naplókat is használhat.

### <a name="i-am-unable-to-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Nem tudom megjeleníteni a több mint 10 virtuális géppel rendelkező csoportok függőségeit?
A legfeljebb 10 virtuális géppel rendelkező [csoportok függőségeinek megjelenítéséhez](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) , ha több mint 10 virtuális géppel rendelkező csoporttal rendelkezik, javasoljuk, hogy ossza szét a csoportot kisebb csoportokba, és jelenítse meg a függőségeket.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>Telepítettem az ügynököket, és a függőségi vizualizációt használták a csoportok létrehozásához. Most feladatátvétel után a gépek "az ügynök telepítése" műveletet mutatják "a függőségek megtekintése" helyett
* A tervezett vagy nem tervezett feladatátvétel után a helyszíni gépek ki vannak kapcsolva, és az egyenértékű gépek az Azure-ban vannak. Ezek a gépek eltérő MAC-címeket vásárolnak. Más IP-címet is beszerezhetnek, attól függően, hogy a felhasználó úgy döntött, hogy megőrizze a helyszíni IP-címet. Ha a MAC és az IP-címek eltérőek, a Azure Migrate nem rendeli hozzá a helyszíni gépeket semmilyen Service Map függőségi adattal, és a függőségek megtekintése helyett kéri a felhasználót, hogy telepítse az ügynököket.
* A feladatátvételi teszt után a helyszíni gépek a várt módon maradnak bekapcsolva. Az Azure-ban megpördült egyenértékű gépek eltérő MAC-címet igényelnek, és különböző IP-címet igényelhetnek. Ha a felhasználó blokkolja a kimenő Azure Monitor az ezekről a gépekről érkező forgalmat, Azure Migrate nem rendeli hozzá a helyszíni gépeket semmilyen Service Map függőségi adatokhoz, és nem kéri a felhasználót, hogy a függőségek megtekintése helyett ügynököket telepítsen.

## <a name="collect-azure-portal-logs"></a>Azure Portal naplók gyűjtése

**Hogyan gyűjteni Azure Portal hálózati forgalmi naplókat?**

1. Nyissa meg a böngészőt, és navigáljon [a portálra](https://portal.azure.com), és jelentkezzen be.
2. Nyomja meg az F12 billentyűt a Fejlesztői eszközök elindításához. Ha szükséges, törölje a **jelölések törlése**a navigációban beállítást.
3. Kattintson a **hálózat** fülre, és indítsa el a hálózati forgalom rögzítése:
   - A Chrome-ban válassza a **napló megőrzése**lehetőséget. A rögzítésnek automatikusan el kell indulnia. A piros kör azt jelzi, hogy a forgalom rögzítése folyamatban van. Ha nem jelenik meg, kattintson a fekete körre a kezdéshez.
   - A Microsoft Edge/IE-ben a rögzítés automatikusan elindul. Ha nem, kattintson a zöld Lejátszás gombra.
4. Próbálja megismételni a hibát.
5. Miután észlelte a hibát a rögzítés során, állítsa le a rögzítést, és mentse a rögzített tevékenység másolatát:
   - A Chrome-ban kattintson a jobb gombbal, majd kattintson a **Mentés a tartalommal**elemre. Ezzel tömöríti és exportálja a naplókat. har-fájlként.
   - A Microsoft Edge/IE-ben kattintson a **rögzített forgalom exportálása** ikonra. Ezzel tömöríti és exportálja a naplót.
6. Az esetleges figyelmeztetések és hibák kereséséhez navigáljon a **konzol** lapra. A konzol naplójának mentése:
   - A Chrome-ban kattintson a jobb gombbal a konzol naplójában bárhová. Válassza a **Mentés másként**lehetőséget, exportálja és zip-ként a naplót.
   - A Microsoft Edge/IE-ben kattintson a jobb gombbal a hibákra, és válassza az **összes másolása**lehetőséget.
7. Fejlesztői eszközök bezárásához.
