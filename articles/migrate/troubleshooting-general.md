---
title: Azure Migrate problémák elhárítása | Microsoft Docs
description: Áttekintést nyújt a Azure Migrate szolgáltatás ismert problémáiról, valamint a gyakori hibákkal kapcsolatos hibaelhárítási tippekről.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: raynew
ms.openlocfilehash: fa1e7fcf89ccc06e429831191ba5dfce3cf33797
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828317"
---
# <a name="troubleshoot-azure-migrate"></a>Az Azure Migrate hibaelhárítása

A [Azure Migrate](migrate-services-overview.md) az értékelési és áttelepítési eszközök, valamint a külső gyártóktól származó független szoftvergyártók (ISV-ajánlatok) központját biztosítja. Ez a cikk segítséget nyújt a hibák elhárításához Azure Migrate, Azure Migrate Server Assessment és Azure Migrate Server Migrálás esetén.

## <a name="azure-migrate-project-issues"></a>A projekttel kapcsolatos problémák Azure Migrate

### <a name="i-cant-find-my-existing-azure-migrate-project"></a>Nem találom a meglévő Azure Migrate projektet.

A Azure Migrate [két verziója](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) létezik. Attól függően, hogy melyik verziót hozta létre a projektben, használja a következő módszerek egyikét:

* Ha olyan projektet keres, amely a Azure Migrate korábbi verziójával (régi tapasztalatával) lett létrehozva, a projekt megkereséséhez kövesse az alábbi lépéseket:

    1. Lépjen a [Azure Portalre](https://portal.azure.com), és keressen rá a **Azure Migrate**kifejezésre.
    2. A Azure Migrate irányítópultján megjelenik egy szalagcím, amely a régebbi projektek elérését említi. Ez a szalagcím csak akkor jelenik meg, ha létrehozott egy projektet a régi felülettel. Válassza ki a szalagcímet.

       ![Meglévő projektek elérése](./media/troubleshooting-general/access-existing-projects.png)

    3. Ekkor megjelenik a Azure Migrate korábbi verziójával létrehozott meglévő projektek listája.

* Ha egy aktuális verzióval (új felülettel) létrehozott projektet keres, kövesse az alábbi lépéseket a projekt megkereséséhez:

    1. Lépjen a [Azure Portalre](https://portal.azure.com), és keressen rá a **Azure Migrate**kifejezésre.
    2. A Azure Migrate irányítópulton nyissa meg a **kiszolgálók** lapot a bal oldali ablaktáblán, és kattintson a jobb felső sarokban található **módosítás** lehetőségre.

       ![Váltás meglévő Azure Migrate projektre](./media/troubleshooting-general/switch-project.png)

    3. Válassza ki a megfelelő előfizetést és Azure Migrate projektet.

### <a name="how-do-i-create-a-second-azure-migrate-project"></a>Hogyan hozzon létre egy második Azure Migrate projektet?

Az alábbi lépéseket követve hozzon létre egy új Azure Migrate projektet:

1. Lépjen a [Azure Portalre](https://portal.azure.com), és keressen rá a **Azure Migrate**kifejezésre.
2. A Azure Migrate irányítópulton nyissa meg a **kiszolgálók** lapot a bal oldali ablaktáblán, és kattintson a jobb felső sarokban található **módosítás** lehetőségre.

   ![Azure Migrate projekt módosítása](./media/troubleshooting-general/switch-project.png)

3. Új projekt létrehozásához válassza a **kattintson ide**.

   ![Második Azure Migrate projekt létrehozása](./media/troubleshooting-general/create-new-project.png)

### <a name="which-azure-geographies-does-azure-migrate-support"></a>Mely Azure-régiók támogatják a Azure Migrate?

Tekintse meg a [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) és a [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)listáját.

### <a name="how-do-i-delete-azure-migrate-projects-and-associated-log-analytics-workspaces"></a>Hogyan törölni Azure Migrate projekteket és a hozzájuk társított Log Analytics munkaterületeket?

Ha töröl egy Azure Migrate projektet, a rendszer törli az áttelepítési projektet *és* a felderített gépek metaadatait. Ha azonban Log Analytics munkaterületet csatolt a kiszolgáló-értékelési eszközhöz, a Log Analytics munkaterület nem törlődik automatikusan. (Ugyanaz a Log Analytics munkaterület több felhasználási esethez is használható.) Ha az Log Analytics munkaterületet is törölni szeretné, ezt manuálisan kell megtennie:

1. Tallózással keresse meg a projekthez csatolt Log Analytics munkaterületet.
     * Ha még nem törölte az áttelepítési projektet, keresse meg a munkaterületre mutató hivatkozást az alapvető erőforrások szakasz kiszolgáló értékelése áttekintés lapján.

       ![LA munkaterület](./media/troubleshooting-general/loganalytics-workspace.png)

     * Ha már törölte az áttelepítési projektet, válassza az **erőforráscsoportok** lehetőséget a Azure Portal bal oldali ablaktábláján. Keresse meg azt az erőforráscsoportot, amelyben a munkaterületet létrehozták, majd keresse meg.
2. Kövesse az [Azure log Analytics munkaterület törlése a Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace)című témakör utasításait.

### <a name="migration-project-creation-failed-with-a-requests-must-contain-user-identity-headers-error"></a>Az áttelepítési projekt létrehozása nem sikerült – a kérelmekben szerepelnie kell a felhasználói azonosító fejlécének.

Ez a probléma olyan felhasználók számára fordulhat elő, akik nem rendelkeznek hozzáféréssel a szervezet Azure Active Directory (Azure AD) bérlője számára. Amikor első alkalommal ad hozzá egy felhasználót egy Azure AD-bérlőhöz, a felhasználó e-mailben meghívót kap a bérlőhöz való csatlakozáshoz. A felhasználóknak el kell fogadniuk a meghívót, hogy sikeresen bekerüljön a bérlőbe. Ha nem látja az e-mailt, forduljon egy olyan felhasználóhoz, aki már rendelkezik hozzáféréssel a bérlőhöz, és kérje meg őket, hogy küldje el újra a meghívást a [vendég felhasználói](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)számára a meghívások újraküldése részben megadott lépések segítségével.

A meghívót tartalmazó e-mail fogadása után meg kell nyitnia az e-mailt, és ki kell választania a meghívó elfogadására szolgáló hivatkozást. Ezután ki kell jelentkeznie a Azure Portalból, és újra be kell jelentkeznie. (A böngésző frissítése nem fog működni.) Ezután megkezdheti az áttelepítési projekt létrehozását.

## <a name="appliance-issues"></a>Készülékkel kapcsolatos problémák

### <a name="deployment-of-an-azure-migrate-appliance-for-vmware-failed-with-a-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry-error"></a>A VMware Azure Migrate berendezésének telepítése nem sikerült, mert a megadott jegyzékfájl érvénytelen: Érvénytelen OVF jegyzékfájl-bejegyzés: "Error.

1. Ellenőrizze, hogy a Azure Migrate készülék PETESEJTJEInek fájlja helyesen van-e letöltve a kivonatoló értékének ellenőrzésével. Útmutatásért lásd: [VMWare virtuális gépek előkészítése az Azure-ba történő értékeléshez és](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware)áttelepítéshez. Ha a kivonatoló érték nem egyezik, töltse le újra a PETESEJT-fájlt, majd próbálja megismételni a telepítést.
2. Ha a telepítés továbbra is meghiúsul, és ha a VMware vSphere-ügyfelet használja a OVF-fájl üzembe helyezéséhez, próbálja meg telepíteni a vSphere webes ügyfelén keresztül. Ha az üzembe helyezés továbbra is sikertelen, próbálkozzon más böngészővel.
3. Ha a vSphere webes ügyfélprogramot használja, és vCenter Server 6,5-es vagy 6,7-es számítógépen szeretné telepíteni, próbálja meg közvetlenül telepíteni a PETESEJTeket az ESXi-gazdagépre a következő lépések végrehajtásával:
   - Kapcsolódjon közvetlenül az ESXi-gazdagéphez (vCenter Server helyett) a webes ügyfél használatával (https://<*gazdagép IP-címe*>/UI).
   - Nyissa meg a **Kezdőlap** > **leltárt**.
   - Válassza a **fájl** > **telepítése OVF sablont**. Tallózással keresse meg a PETESEJTeket, és fejezze be a telepítést.
4. Ha a telepítés továbbra is sikertelen, forduljon Azure Migrate támogatási szolgálathoz.

### <a name="the-appliance-cant-connect-to-the-internet"></a>A készülék nem tud csatlakozni az internethez.

Ez a viselkedés akkor fordulhat elő, ha a használt gép proxy mögött van. Győződjön meg arról, hogy megadja az engedélyezési hitelesítő adatokat, ha a proxynak szüksége van rájuk.
Ha bármilyen URL-alapú tűzfal-proxyt használ a kimenő kapcsolatok vezérléséhez, ügyeljen arra, hogy ezeket a szükséges URL-címeket hozzáadja az engedélyezési listához:

- [Kiszolgáló-értékelés VMware-hez](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
- [A Hyper-V kiszolgálói értékelése](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
- [Kiszolgáló áttelepítése VMware-re (ügynök nélkül)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
- [Kiszolgáló áttelepítése VMware-re (ügynök-alapú)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
- [Kiszolgáló áttelepítése Hyper-V-re](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

Ha elfogó proxyt használ az internethez való csatlakozáshoz, importálnia kell a proxy tanúsítványt a készülék virtuális gépre. A [Azure Migrate berendezés](https://docs.microsoft.com/azure/migrate/concepts-collector)lépéseinek végrehajtásával importálhatja a proxy tanúsítványát.

### <a name="error-802-date-and-time-synchronization-error"></a>802-es hiba: Dátum-és időszinkronizálási hiba.

Előfordulhat, hogy a kiszolgáló órája legfeljebb öt perc alatt szinkronizálva van az aktuális idővel. Módosítsa a gyűjtő virtuális gép órájának időpontját úgy, hogy az megfeleljen az aktuális időpontnak, az alábbiak szerint:

1. Nyisson meg egy rendszergazdai parancssort a virtuális gépen.
2. Az időzóna ellenõrzéséhez futtassa a **w32tm/TZ**.
3. Az idő szinkronizálásához futtassa a **w32tm resync**.


###  <a name="connection-failed-error-unabletoconnecttoserver"></a>A csatlakoztatás nem sikerült. Hiba: UnableToConnectToServer.

Előfordulhat, hogy nem tud csatlakozni a (z) vCenter Server *servername*. com: 9443. A hiba részletei azt jelzik, hogy nincs olyan végpont, amely a https://*servername*. com: 9443/SDK-ban fogadja el az üzenetet.

Ebben az esetben győződjön meg arról, hogy a gyűjtő berendezés legújabb verzióját futtatja-e. Ha nem, frissítse a készüléket a [legújabb verzióra](https://docs.microsoft.com/azure/migrate/concepts-collector).

Ha a probléma továbbra is a legújabb verzióban fordul elő, előfordulhat, hogy a gyűjtő számítógép nem tudja feloldani a megadott vCenter Server nevet, vagy a megadott port helytelen lehet. Alapértelmezés szerint, ha a port nincs megadva, a gyűjtő megpróbál csatlakozni a 443-as számú portszámhoz.

1. Pingelje a *servername*. com-t a gyűjtő gépről.
2. Ha az 1. lépés meghiúsul, próbáljon meg csatlakozni a vCenter-kiszolgálóhoz az IP-cím használatával.
3. Adja meg a megfelelő portszámot a vCenterhez történő csatlakozáshoz.
4. Ellenőrizze, hogy a vCenter-kiszolgáló működik-e.


### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60052"></a>Lehet, hogy a berendezés nem regisztrálható sikeresen a Azure Migrate projektben (hiba azonosítója: 60052).

Ez a hiba akkor fordul elő, ha a berendezés regisztrálásához használt Azure-fiók nem rendelkezik megfelelő engedélyekkel. Győződjön meg arról, hogy a készülék regisztrálásához használt Azure-beli felhasználói fiók legalább közreműködői engedélyekkel rendelkezik az előfizetésben. [További](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) információ a szükséges Azure-szerepkörökről és-engedélyekről.

### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60039"></a>Lehet, hogy a berendezés nem regisztrálható sikeresen a Azure Migrate projektben (hiba azonosítója: 60039).

Előfordulhat, hogy a készülék regisztrálásához kiválasztott Azure Migrate-projekt nem található. Ebben az esetben a regisztráció meghiúsul. Lépjen a Azure Portalra, és győződjön meg arról, hogy a projekt létezik az erőforráscsoporthoz. Ha a projekt nem létezik, hozzon létre egy új Azure Migrate projektet az erőforráscsoporthoz, és regisztrálja újra a készüléket. [További](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) információ az új Azure Migrate projekt létrehozásáról.

### <a name="an-azure-key-vault-management-operation-failed-error-id-60030-60031"></a>Egy Azure Key Vault felügyeleti művelet meghiúsult (hiba azonosítója: 60030, 60031).

Győződjön meg arról, hogy a készülék regisztrálásához használt Azure-beli felhasználói fiók legalább közreműködői engedélyekkel rendelkezik az előfizetésben. Győződjön meg arról is, hogy a fiók rendelkezik hozzáféréssel a hibaüzenetben megadott kulcstartóhoz, majd próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához. [További](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) információ a szükséges Azure-szerepkörökről és-engedélyekről.

### <a name="discovery-couldnt-be-initiated-because-of-an-error-the-operation-failed-for-the-specified-list-of-hosts-or-clusters-error-id-60028"></a>Hiba miatt nem sikerült elindítani a felderítést. Nem sikerült végrehajtani a műveletet a gazdagépek vagy fürtök megadott listájában (hiba azonosítója: 60028).

Nem sikerült elindítani a felderítést a hibaüzenetben felsorolt gazdagépeken, mert probléma merült fel a virtuális gép adatainak elérése vagy beolvasása során. A többi gazdagép hozzáadása sikeresen megtörtént. Adja hozzá a hibaüzenetben felsorolt gazdagépeket a **gazdagép hozzáadása** lehetőség használatával. Ha érvényesítési hiba történt, tekintse át a Szervizelési útmutatót a hibák kijavításához, majd próbálkozzon újra a **Mentés és a felderítés** megkezdése lehetőséggel.

### <a name="an-azure-ad-operation-failed-the-error-occurred-while-creating-or-updating-the-azure-ad-application-error-id-60025"></a>Egy Azure AD-művelet nem sikerült. Hiba történt az Azure AD-alkalmazás létrehozásakor vagy frissítésekor (hiba azonosítója: 60025).

Ez a hiba akkor merül fel, ha a felderítés elindításához használt Azure-felhasználói fiók nem azonos a berendezés regisztrálásához használttal. Tegye a következők egyikét:
1. Győződjön meg arról, hogy a felderítést kezdeményező felhasználói fiók megegyezik a berendezés regisztrálásához használttal.
1. Adja meg a HRE-alkalmazás hozzáférési engedélyeit arra a másik felhasználói fiókra, amelyhez a felderítési művelet sikertelen.
1. Törölje a korábban a Azure Migrate projekthez létrehozott erőforráscsoportot, és hozzon létre egy másik erőforráscsoportot az újraindításhoz.

[További](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) információ a HRE alkalmazás engedélyeiről.

## <a name="discovery-issues"></a>Felderítési problémák

### <a name="i-started-discovery-but-dont-see-the-discovered-vms-on-the-azure-portal-the-server-assessment-and-server-migration-tiles-show-a-status-of-discovery-in-progress"></a>Elindítottam a felderítést, de nem látják a felderített virtuális gépeket a Azure Portalon. A **kiszolgáló-értékelési** és a **kiszolgáló** -áttelepítési csempék a "felderítés folyamatban" állapotot mutatják.
Miután elindította a felderítést a készülékről, hagyjon némi időt, amíg a felderített gépek megjelennek a Azure Portal. A VMware-felderítés körülbelül 15 percet vesz igénybe, és a Hyper-V felderítéséhez hozzáadott gazdagépeken körülbelül 2 perc van. Ha továbbra is a "felderítés folyamatban" állapot jelenik meg a várakozási időszakok után is, válassza a **frissítés** lehetőséget a **kiszolgálók** lapon. Ennek meg kell jelennie a felderített kiszolgálók számának a kiszolgáló-értékelési és a kiszolgáló-áttelepítési csempén.


### <a name="im-using-an-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-shown-in-the-portal"></a>Olyan készüléket használok, amely folyamatosan felfedi a helyszíni környezetet, de a helyszíni környezetben törölt virtuális gépek továbbra is megjelennek a portálon.

Akár 30 percet is igénybe vehet, amíg a berendezés által összegyűjtött felderítési adatok megjelennek a portálon. Ha 30 perc után sem jelenik meg naprakész információ, frissítse az adatokat a következő lépésekkel:

1. Lépjen a **kiszolgálók** > **Azure Migrate kiszolgáló értékelése**elemre, és válassza az **Áttekintés**lehetőséget.
2. A **kezelés**területen válassza a **Agent Health**
3. Válassza az **ügynök frissítése**lehetőséget. Ez a lehetőség az ügynökök listájában jelenik meg.
4. Várjon, amíg a frissítési művelet befejeződik. Ekkor a virtuális gépek naprakész információit kell látnia.

### <a name="i-dont-see-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>Nem látom a legújabb információkat a helyszíni virtuális gépekről a portálon.

Akár 30 percet is igénybe vehet, amíg a berendezés által összegyűjtött felderítési adatok megjelennek a portálon. Ha 30 perc után sem jelenik meg naprakész információ, frissítse az adatokat a következő lépésekkel:

1. Lépjen a **kiszolgálók** > **Azure Migrate kiszolgáló értékelése**elemre, és válassza az **Áttekintés**lehetőséget.
2. A **kezelés**területen válassza a **Agent Health**
3. Válassza az **ügynök frissítése**lehetőséget. Ez a lehetőség az ügynökök listájában jelenik meg.
4. Várjon, amíg a frissítési művelet befejeződik. Ekkor a virtuális gépek naprakész információit kell látnia.

### <a name="cant-connect-to-a-host-or-cluster-because-the-server-name-cant-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>Nem lehet csatlakozni egy gazdagéphez vagy fürthöz, mert a kiszolgáló neve nem oldható fel. WinRM-hibakód: 0x803381B9 (hiba azonosítója: 50004).
Ez a hiba akkor fordul elő, ha a berendezés Azure DNS szolgáltatása nem tudja feloldani a megadott fürtöt vagy állomásnevet. Ha ezt a hibát látja a fürtön, próbálja meg a fürt teljes tartománynevét (FQDN).

Előfordulhat, hogy ez a hiba a fürtben lévő gazdagépek esetében is megjelenik. Ebben az esetben a készülék csatlakozhat a fürthöz, a fürt azonban olyan állomásnévket ad vissza, amelyek nem teljes tartománynevek.

A hiba megoldásához frissítse a gazdagépek fájlját a készüléken az IP-cím és az állomásnevek hozzárendelésének hozzáadásával:
1. Nyissa meg a Jegyzettömböt rendszergazdaként. Ezután nyissa meg a C:\Windows\System32\Drivers\etc\hosts fájlt.
2. Adja hozzá az IP-címet és az állomásnevet egy sorban. Ismételje meg az összes olyan gazdagépet vagy fürtöt, ahol ez a hiba látható.
3. Mentse és zárja be a hosts fájlt.
4. Megtekintheti, hogy a készülék tud-e csatlakozni a gazdagépekhez a berendezés-kezelő alkalmazás használatával. 30 perc elteltével a Azure Portalon láthatja a gazdagépek legfrissebb információit.


## <a name="assessment-issues"></a>Értékelési problémák

### <a name="azure-readiness-issues"></a>Azure-készültségi problémák

Probléma | Szervizelés
--- | ---
Nem támogatott rendszerindítási típus | Az Azure nem támogatja az EFI rendszerindítási típussal rendelkező virtuális gépeket. Javasoljuk, hogy az áttelepítés futtatása előtt alakítsa át a rendszerindítási típust BIOS-ra. <br/><br/>Az ilyen virtuális gépek áttelepítésének kezeléséhez Azure Migrate kiszolgáló áttelepítését használhatja. Az áttelepítés során a rendszer a virtuális gép rendszerindítási típusát a BIOS-ba konvertálja.
Feltételesen támogatott Windows operációs rendszer | Az operációs rendszer elérte a támogatás befejezési dátumát, és az [Azure](https://aka.ms/WSosstatement)-támogatáshoz egyéni támogatási szerződés (CSA) szükséges. Az Azure-ba való Migrálás előtt érdemes frissíteni az operációs rendszert.
Nem támogatott Windows operációs rendszer | Az Azure csak a [kiválasztott Windows operációsrendszer](https://aka.ms/WSosstatement)-verziókat támogatja. Az Azure-ba való Migrálás előtt érdemes lehet frissíteni a gép operációs rendszerét.
Feltételesen jóváhagyott Linux operációs rendszer | Az Azure csak a [kiválasztott LINUXOS operációsrendszer](../virtual-machines/linux/endorsed-distros.md)-verziókat támogatja. Az Azure-ba való Migrálás előtt érdemes lehet frissíteni a gép operációs rendszerét.
Nem jóváhagyott Linux operációs rendszer | Előfordulhat, hogy a gép az Azure-ban indul el, de az Azure nem biztosít operációsrendszer-támogatást. Az Azure-ba való Migrálás előtt érdemes lehet az operációs rendszert egy [támogatott Linux](../virtual-machines/linux/endorsed-distros.md) -verzióra frissíteni.
Ismeretlen operációs rendszer | A virtuális gép operációs rendszere a következőként lett megadva: vCenter Server. Ez a viselkedés blokkolja Azure Migrate a virtuális gép Azure-készültségének ellenőrzéséhez. Mielőtt áttelepíti a gépet, győződjön meg arról, hogy az Azure [támogatja](https://aka.ms/azureoslist) a számítógép operációs rendszerét.
Az operációs rendszer bitszáma nem támogatott | A 32 bites operációs rendszert futtató virtuális gépek az Azure-ban is elindíthatók, de javasoljuk, hogy az Azure-ba való áttelepítés előtt frissítse a virtuális gép operációs rendszerét 64 bitesre.
Microsoft Visual Studio-előfizetést igényel | A gép Windows ügyfél operációs rendszert futtat, amely csak Visual Studio-előfizetésen keresztül támogatott.
Nem található a szükséges tárolási teljesítménynek megfelelő virtuális gép | A géphez szükséges tárolási teljesítmény (bemeneti/kimeneti műveletek száma másodpercenként [IOPS] és átviteli sebesség) meghaladja az Azure-beli virtuális gépek támogatását. Csökkentse a gép tárolási követelményeit az áttelepítés előtt.
Nem található a szükséges hálózati teljesítménynek megfelelő virtuális gép | A gép számára szükséges hálózati teljesítmény (be/ki) meghaladja az Azure-beli virtuális gépek támogatását. Csökkentse a gép hálózati követelményeit.
Nem található virtuális gép a megadott helyen | A Migrálás előtt használjon másik célhelyet.
Egy vagy több lemez nem megfelelő | A virtuális géphez csatolt egy vagy több lemez nem felel meg az Azure követelményeinek. Azure Migrate: A kiszolgáló értékelése jelenleg nem támogatja ultra SSD lemezeket, és a prémium szintű felügyelt lemezekre vonatkozó korlátok alapján értékeli a lemezeket (32 TB).  A virtuális géphez csatlakoztatott minden lemez esetében győződjön meg arról, hogy a lemez mérete < 64 TB (ultra SSD-lemezek által támogatott), ha nem, csökkentse a lemez méretét az Azure-ba való áttelepítés előtt, vagy használjon több lemezt az Azure-ban, és [csoportosítsa őket](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) , hogy magasabb tárolási korlátokat kapjon. Győződjön meg arról, hogy az egyes lemezek által igényelt teljesítmény (IOPS és átviteli sebesség) támogatott az Azure által [felügyelt virtuálisgép-lemezek](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)esetében.
Egy vagy több nem megfelelő hálózati adapter. | A nem használt hálózati adapterek eltávolítása a gépről az áttelepítés előtt.
A lemezek száma meghaladja a korlátot | Az áttelepítés előtt távolítsa el a nem használt lemezeket a gépről.
A lemezméret meghaladja a korlátot | Azure Migrate: A Server Assessment jelenleg nem támogatja ultra SSD lemezeket, és a prémium szintű lemezes korlátok alapján értékeli a lemezeket (32 TB). Az Azure azonban a legfeljebb 64 TB méretű lemezeket támogatja (ultra SSD lemezek által támogatott). A lemezeket a Migrálás előtt kevesebb mint 64 TB-ra csökkenti, vagy [](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) több lemezt használ az Azure-ban, és a nagyobb tárolási korlátok eléréséhez.
A lemez nem érhető el a megadott helyen | A Migrálás előtt ellenőrizze, hogy a lemez a célhelyen van-e.
A lemez a megadott redundanciával nem érhető el | A lemeznek az értékelési beállításokban definiált redundancia-tárolási típust kell használnia (alapértelmezés szerint LRS).
Belső hiba miatt nem sikerült meghatározni a lemez alkalmasságát | Próbálja meg létrehozni a csoport új értékelését.
Nem található a szükséges magokkal és memóriával rendelkező virtuális gép | Az Azure nem talált megfelelő virtuálisgép-típust. A Migrálás előtt csökkentse a helyszíni gép memóriáját és a magok számát.
Belső hiba miatt nem sikerült meghatározni a virtuális gép alkalmasságát | Próbálja meg létrehozni a csoport új értékelését.
Belső hiba miatt nem sikerült meghatározni egy vagy több lemez alkalmasságát | Próbálja meg létrehozni a csoport új értékelését.
Belső hiba miatt nem sikerült meghatározni egy vagy több hálózati adapter megfelelőségét | Próbálja meg létrehozni a csoport új értékelését.

### <a name="i-cant-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>Nem adható meg Nagyvállalati Szerződés (EA) Azure-ajánlatként az értékelés tulajdonságaiban.
Azure Migrate Server Assessment jelenleg nem támogatja Nagyvállalati Szerződés (EA) alapú díjszabást. Ennek a korlátozásnak a megkerülő megoldásához használja az utólagos elszámolású Azure-ajánlatot, és használja a **Discount** tulajdonságot a kapott egyéni kedvezmény megadásához. [Az értékelések testreszabásáról itt talál további információt](https://aka.ms/migrate/selfhelp/eapricing).

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready"></a>Miért jelöli meg a Server Assessment a Linux rendszerű virtuális gépeket "feltételesen kész" állapotban?
Létezik egy ismert rés a kiszolgáló értékelése során, amely megakadályozza, hogy észlelje a helyszíni virtuális gépeken telepített Linux operációs rendszer alverzióját (például RHEL 6,10, jelenleg a kiszolgáló értékelése csak az RHEL 6 verziót észleli). Mivel az Azure csak a Linux bizonyos verzióit támogatja, a Linux rendszerű virtuális gépek jelenleg feltételesen készen állnak a kiszolgáló értékelése során. Az [Azure Linux támogatási dokumentációjának](https://aka.ms/migrate/selfhost/azureendorseddistros)áttekintésével megállapíthatja, hogy a helyszíni virtuális gépen futó Linux operációs rendszer támogatja-e az Azure-t. A támogatott terjesztés ellenőrzése után figyelmen kívül hagyhatja ezt a figyelmeztetést.

### <a name="why-does-the-vm-sku-recommended-by-server-assessment-have-more-cores-and-more-memory-than-whats-allocated-on-premises"></a>Miért van a kiszolgáló értékelése által ajánlott virtuálisgép-SKU több magot és több memóriát tartalmaz, mint amit a helyszínen lefoglalt?
A Server Assessment által javasolt virtuálisgép-termékváltozat az értékelési tulajdonságoktól függ. A kiszolgálók értékelése során kétféle értékelést hozhat létre: *Teljesítmény-alapú* éshelyszíni. A teljesítmény-alapú értékelések esetében a kiszolgáló értékelése a helyszíni virtuális gépek (CPU, memória, lemez és hálózat kihasználtsága) kihasználtsági adatait veszi figyelembe a helyszíni virtuális gépekhez tartozó megfelelő virtuálisgép-SKU meghatározásához. Emellett a teljesítmény-alapú méretezéshez a komfort tényezőt is figyelembe kell venni a hatékony kihasználtság meghatározásakor. A helyszíni méretezés esetében a teljesítményadatokat nem számítjuk fel, és a cél SKU-t a helyszíni kiosztása alapján ajánljuk.

Tegyük fel például, hogy egy helyszíni virtuális gép 4 maggal és 8 GB memóriával rendelkezik, 50%-os CPU-kihasználtsággal és 50%-os memóriával, és az értékelésben a 1,3 kényelmi tényezője van megadva. Ha az értékelés méretezési feltételei a helyszínen vannak, akkor a rendszer egy 4 maggal és 8 gigabájt (GB) memóriával rendelkező Azure VM SKU **-** t ajánl.

Tegyük fel azonban, hogy a méretezési feltétel teljesítmény-alapú. A processzor és a memória hatékony kihasználtsága (50% 50-a 4 mag * 1,3 = 2,6 magok, valamint a 8 GB-os memória használata esetén * 1,3 = 5,3-GB memória), a négy mag (a legközelebbi támogatott alapszám) és a 8 GB memória (a legközelebbi támogatott memória mérete) alapján ajánlott. [A Server Assessment méretezési módszeréről itt talál további információt.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing)

### <a name="why-is-the-disk-sku-recommended-by-server-assessment-bigger-than-whats-allocated-on-premises"></a>Miért érdemes a Server Assessment által ajánlott lemez SKU-t használni, mint a mi a helyszínen lefoglalt?
A kiszolgáló értékelése során a lemez méretezése két értékelési tulajdonságtól függ: a méretezési feltételektől és a tárolási típustól. Ha a méretezési feltétel **teljesítmény-alapú** , és a tároló típusa **automatikus**, a lemez IOPS és átviteli sebessége a céllemez típusának (standard HDD, standard SSD vagy prémium szintű lemezek) azonosítására szolgál. Ezt követően a lemezből származó SKU-t ajánlott használni, és ez a javaslat a helyszíni lemez méretére vonatkozó követelményeket is figyelembe veszi. Ha a méretezési feltétel **teljesítmény-alapú**, és a tárolási típus **prémium**, az Azure-ban prémium szintű lemezes SKU-t ajánlott használni a helyszíni lemez IOPS, átviteli sebessége és mérete alapján. Ugyanazt a logikát kell használni a lemez méretezéséhez, ha a méretezési feltételek **a helyszínen** vannak, és a tárolási típus **standard HDD**, **standard SSD**vagy **prémium**.

Ha például egy helyszíni lemez 32 GB memóriával rendelkezik, de a lemez összesített olvasási és írási IOPS értéke 800 IOPS, a kiszolgáló értékelése a prémium szintű lemez típusát javasolja (a magasabb IOPS-követelmények miatt), majd javasoljon egy olyan lemezes SKU-t, amely képes támogatni a szükséges IOPS és-méret. Ebben a példában a legjobb megoldást a P15-ös változat (256 GB, 1100 IOPS) adja. Bár a helyszíni lemez által igényelt méret 32 GB volt, a Server Assessment a helyszíni lemez magas IOPS követelménye miatt nagyobb lemezt javasol.

### <a name="why-does-my-assessment-report-indicate-percentageofcoresutilizedmissing-or-percentageofmemoryutilizedmissing-for-some-vms"></a>Miért jelent az értékelési jelentés a "PercentageOfCoresUtilizedMissing" vagy a "PercentageOfMemoryUtilizedMissing" jelölést egyes virtuális gépek esetében?
Ezek a problémák akkor jelennek meg, ha a Azure Migrate berendezés nem tud teljesítményadatokat gyűjteni a helyszíni virtuális gépek számára. Ez a viselkedés akkor fordulhat elő, ha a virtuális gépek ki vannak kapcsolva az értékelés létrehozásakor (az elmúlt egy nap/egy hét/egy hónap). A készülék nem tud teljesítményadatokat gyűjteni a virtuális gép számára, ha ki van kapcsolva. Ha csak a teljesítményszámlálók hiányoznak, és a Hyper-V virtuális gépeket próbálja értékelni, ellenőrizze, hogy a dinamikus memória engedélyezve van-e ezeken a virtuális gépeken. Létezik egy ismert probléma, amelyben egy Azure Migrate berendezés nem tudja gyűjteni a memória-kihasználtsági adatokat olyan virtuális gépek számára, amelyeken nincs engedélyezve a dinamikus memória. Ez a probléma csak a Hyper-V virtuális gépekre, a VMware virtuális gépekre nem vonatkozik. Ha a teljesítményszámlálók bármelyike hiányzik, Azure Migrate kiszolgáló értékelése visszaesik a lefoglalt magokra és a memóriára, és a virtuális gép megfelelő méretét javasolja.

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>A kiszolgáló értékelése alapján becsült számítási költségek tartalmazzák-e a virtuális gép operációsrendszer-licencének költségeit?
A kiszolgáló értékelése jelenleg csak Windows rendszerű gépek esetében veszi figyelembe az operációsrendszer-licenc költségeit. A Linux rendszerű gépek operációsrendszer-licencének költségei jelenleg nem tekintendők.

### <a name="what-impact-do-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Milyen hatással van a teljesítménnyel kapcsolatos előzmények és a percentilis kihasználtsága a javasolt méretre?
Ezek a tulajdonságok csak a teljesítmény alapú méretezésre vonatkoznak. A Server Assessment folyamatosan gyűjti a helyszíni gépek teljesítményadatait, és ezek alapján tesz javaslatot az Azure-beli virtuálisgép- és lemez-termékváltozatra. A teljesítményadatokat a kiszolgáló értékelése a következőképpen gyűjti össze:
- A Azure Migrate készülék folyamatosan a helyszíni környezetet gyűjti, hogy a VMware virtuális gépek esetében 20 másodpercenként, a Hyper-V virtuális gépek esetében pedig 30 másodpercenként gyűjtsön valós idejű kihasználtsági adatokat.
- A készülék összesíti a 20 másodperces és a 30 másodperces mintákat, hogy egyetlen adatpontot hozzon létre 10 percenként. Az egyetlen adatpont létrehozásához a készülék kiválasztja a 20 másodperc és a 30 másodperc közötti csúcsérték értéket, majd elküldi az Azure-nak.
- Amikor értékelést hoz létre a Server Assessmentben a teljesítmény-időtartam és a teljesítményelőzmények percentilisértéke alapján, a rendszer meghatározza a jellemző kihasználtsági értéket. Ha például a teljesítmény előzményei egy hét, és a percentilis kihasználtsága 95., a Azure Migrate az utolsó egy hétre vonatkozó 10 perces mintát rendezi növekvő sorrendbe, majd kijelöli a 95. percentilis értéket a reprezentatív értékként.
A 95. percentilis értéke biztosítja, hogy figyelmen kívül hagyja a kiugró adatokat, amelyek akkor szerepelhetnek, ha kiveszi a esetek 99% percentilis értékét. Ha az időszakra vonatkozó csúcsérték-használatot szeretné kihagyni, és nem szeretné lemondani a kiugró adatokat, válassza ki a esetek 99% percentilis értéket a percentilis kihasználtsága érdekében.

## <a name="dependency-visualization-issues"></a>Függőségi vizualizációval kapcsolatos problémák

### <a name="i-cant-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Nem találom a függőségi vizualizáció funkciót Azure Government projektekhez.

Azure Migrate a függőségi vizualizáció funkciójának Service Map függ. Mivel a Service Map jelenleg nem érhető el a Azure Governmentban, ez a funkció nem érhető el a Azure Governmentban.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>Telepítettem a Microsoft monitoring Agent (MMA) és a függőségi ügynököt a helyszíni virtuális gépeken, de a függőségek mostantól megjelennek a Azure Migrate portálon.

Miután telepítette az ügynököket, Azure Migrate általában 15-30 percet vesz igénybe, hogy megjelenjenek a függőségek a portálon. Ha több mint 30 percet várt, ellenőrizze, hogy az MMA tud-e kommunikálni az OMS-munkaterülettel a következő lépésekkel.

Windows rendszerű virtuális gép esetén:
1. Nyissa meg a Vezérlőpultot, és indítsa el a Microsoft monitoring agentet.
2. A **Microsoft monitoring Agent tulajdonságai** párbeszédpanel **Azure log Analytics (OMS)** lapján győződjön meg arról, hogy a munkaterület **állapota** zöld.
3. Ha az állapot nem zöld, próbálja meg eltávolítani a munkaterületet, és adja hozzá újra az MMA-hoz.

      ![MMA tulajdonságai párbeszédpanel](./media/troubleshooting-general/mma-status.png)

Linux rendszerű virtuális gép esetén győződjön meg arról, hogy az MMA és a függőségi ügynök telepítési parancsai sikeresek voltak.

### <a name="what-operating-systems-does-mma-support"></a>Milyen operációs rendszereket támogat az MMA?

 [Itt látható az MMA által támogatott Windows operációs rendszerek listája](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
[Itt látható az MMA által támogatott Linux operációs rendszerek listája](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-operating-systems-does-the-dependency-agent-support"></a>Milyen operációs rendszereket támogat a függőségi ügynök?

[Itt látható a függőségi ügynök által támogatott Windows operációs rendszerek listája](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems). Itt látható a [függőségi ügynök által támogatott Linux operációs rendszerek listája](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="i-cant-visualize-dependencies-in-azure-migrate-for-more-than-a-one-hour-duration"></a>Egy órás időtartamon belül nem tudom megjeleníteni a függőségeket Azure Migrateban.
Azure Migrate a függőségeket legfeljebb egy órás időtartamra lehet megjeleníteni. Bár a Azure Migrate lehetővé teszi, hogy visszalépjen az előző hónapban egy adott dátumra, a függőségek megjelenítésének maximális időtartama egy óra.

A függőségi Térkép időidőtartam funkciójának használatával például megtekintheti a tegnapi függőségeket, de csak egy órás időszakra megtekintheti őket. [A függőségi adat](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) hosszabb időtartamon keresztül történő lekérdezéséhez azonban Azure monitor naplókat is használhat.

### <a name="i-cant-visualize-dependencies-for-groups-that-have-more-than-10-vms"></a>Nem tudom megjeleníteni a függőségeket a több mint 10 virtuális géppel rendelkező csoportok esetében.
A legfeljebb 10 virtuális géppel rendelkező [csoportok függőségeinek megjelenítéséhez](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) , ha több mint 10 virtuális géppel rendelkező csoporttal rendelkezik, javasoljuk, hogy ossza szét a csoportot kisebb csoportokra, és jelenítse meg a függőségeket.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>Telepítettem az ügynököket, és a függőségi vizualizációt használták a csoportok létrehozásához. A feladatátvételt követően a gépek az "ügynök telepítése" műveletet mutatják "a függőségek megtekintése" helyett.
* A tervezett vagy nem tervezett feladatátvételt követően a helyszíni gépek ki vannak kapcsolva, és az egyenértékű gépek az Azure-ban jelennek meg. Ezek a gépek eltérő MAC-címeket vásárolnak. Előfordulhat, hogy más IP-címet is beszerezhetnek attól függően, hogy a felhasználó úgy döntött-e, hogy megtartja-e a helyszíni IP-címet.

  Ha a MAC és az IP-címek eltérőek, a Azure Migrate nem társítja a helyszíni gépeket semmilyen Service Map függőségi adattal. Ehelyett arra kéri a felhasználót, hogy a függőségek megtekintése helyett ügynököket telepítsen.
* A tesztelés utáni feladatátvétel után a helyszíni gépek a várt módon bekapcsolva maradnak. Az Azure-ban megjelenő egyenértékű gépek eltérő MAC-címet igényelnek, és különböző IP-címeket is megvásárolhatnak. Ha a felhasználó blokkolja a kimenő Azure Monitor az ezekről a gépekről érkező forgalmat, Azure Migrate nem rendeli hozzá a helyszíni gépeket semmilyen Service Map függőségi adatokhoz, és nem kéri a felhasználókat, hogy a függőségek megtekintése helyett ügynököket telepítsenek.

## <a name="collect-azure-portal-logs"></a>Azure Portal naplók gyűjtése

**Hogyan gyűjteni Azure Portal hálózati forgalmi naplókat?**

1. Nyissa meg a böngészőt, lépjen [a portálra](https://portal.azure.com), és jelentkezzen be.
2. Nyomja meg az F12 billentyűt Fejlesztői eszközök elindításához. Ha szükséges, törölje a jelet a navigációs beállításban szereplő **bejegyzések törlése** elemre.
3. Válassza a **hálózat** fület, és indítsa el a hálózati forgalom rögzítését:
   - A Chrome-ban válassza a **napló megőrzése**lehetőséget. A rögzítésnek automatikusan el kell indulnia. A piros kör azt jelzi, hogy a forgalom rögzítése folyamatban van. Ha a piros kör nem jelenik meg, válassza ki az elindulni kívánt fekete kört.
   - A Microsoft Edge-ben és az Internet Explorerben a rögzítés automatikusan elindul. Ha nem, kattintson a zöld Lejátszás gombra.
4. Próbálja megismételni a hibát.
5. Miután észlelte a hibát a rögzítés során, állítsa le a rögzítést, és mentse a rögzített tevékenység másolatát:
   - A Chrome-ban kattintson a jobb gombbal, és válassza a **Save as har a tartalommal**lehetőséget. Ez a művelet tömöríti és exportálja a naplókat. har-fájlként.
   - A Microsoft Edge vagy az Internet Explorerben válassza a **rögzített forgalom exportálása** lehetőséget. Ez a művelet tömöríti és exportálja a naplót.
6. Válassza a **konzol** fület a figyelmeztetések és hibák kereséséhez. A konzol naplójának mentése:
   - A Chrome-ban kattintson a jobb gombbal a konzol naplójában bárhová. Válassza a **Mentés másként**lehetőséget, exportálja és zip-ként a naplót.
   - A Microsoft Edge vagy az Internet Explorerben kattintson a jobb gombbal a hibákra, és válassza az **összes másolása**lehetőséget.
7. Fejlesztői eszközök bezárásához.
