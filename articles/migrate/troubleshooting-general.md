---
title: Azure Migrate problémák elhárítása | Microsoft Docs
description: Áttekintést nyújt a Azure Migrate szolgáltatás ismert problémáiról, valamint a gyakori hibákkal kapcsolatos hibaelhárítási tippekről.
author: musa-57
ms.manager: abhemraj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: hamusa
ms.openlocfilehash: 96c5190988d79885f3a1335b6fd431e028bba8fc
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384063"
---
# <a name="troubleshoot-azure-migrate"></a>Az Azure Migrate hibaelhárítása

A [Azure Migrate](migrate-services-overview.md) az értékelési és áttelepítési eszközök, valamint a külső gyártóktól származó független szoftvergyártók (ISV-ajánlatok) központját biztosítja. Ez a cikk segítséget nyújt a Azure Migrateával, Azure Migrate a kiszolgálók értékelésével és a Azure Migrate kiszolgáló áttelepítésével kapcsolatos problémák elhárításában.


## <a name="find-a-project"></a>Projekt keresése

A Azure Migrate [két verziója](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) létezik.


Ha a Azure Migrate projektet a Azure Migrate aktuális verziójában hozta létre, tegye a következőket:

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Migrate**.
2. A Azure Migrate irányítópulton > **kiszolgálók**lapon válassza a **módosítás** lehetőséget a jobb felső sarokban.

    ![Váltás meglévő Azure Migrate projektre](./media/troubleshooting-general/switch-project.png)

3. Válassza ki a megfelelő előfizetést és Azure Migrate projektet.


Ha a projektet a Azure Migrate előző verziójában hozta létre, tegye a következőket:

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Migrate**.
2. Ha a Azure Migrate-irányítópulton létrehozott egy projektet az előző verzióban, megjelenik egy régebbi projektekre hivatkozó szalagcím. Válassza ki a szalagcímet.

    ![Meglévő projektek elérése](./media/troubleshooting-general/access-existing-projects.png)

3. Tekintse át a régi projekt listáját.


## <a name="create-additional-projects"></a>További projektek létrehozása

Hozzon létre egy új Azure Migrate projektet a következőképpen:

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Migrate**.
2. A Azure Migrate irányítópulton > **kiszolgálók**lapon válassza a **módosítás** lehetőséget a jobb felső sarokban.

   ![Azure Migrate projekt módosítása](./media/troubleshooting-general/switch-project.png)

3. Új projekt létrehozásához válassza a **kattintson ide**.

   ![Második Azure Migrate projekt létrehozása](./media/troubleshooting-general/create-new-project.png)

## <a name="review-supported-geographies"></a>Támogatott földrajzi területek áttekintése

Tekintse át a [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) és a [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)támogatott földrajzi területeit.

## <a name="delete-projectsworkspaces"></a>Projektek/munkaterületek törlése

Azure Migrate projektek és Log Analytics munkaterületek törlésekor vegye figyelembe a következőket:

- Ha töröl egy Azure Migrate projektet, a rendszer törli a projektet *és* a felderített gépek metaadatait.
- Ha Log Analytics munkaterületet csatolt a kiszolgáló-értékelési eszközhöz, a munkaterület nem törlődik automatikusan.
- Ugyanaz a Log Analytics munkaterület több forgatókönyv esetén is használható.
- Ha törölni szeretné a Log Analytics munkaterületet, manuálisan kell elvégeznie.


### <a name="delete-a-project"></a>Projekt törlése

Projekt törlése a Azure Migrate aktuális verziójában:

1. Nyissa meg azt az Azure-erőforráscsoportot, amelyben a projekt létrejött.
2. Az erőforráscsoport lapon válassza a **rejtett típusok megjelenítése**elemet.
3. Válassza ki a törölni kívánt áttelepíthető projektet. Az erőforrástípus a Microsoft. migrálása/migrateprojects, és törli.

Egy projekt törlése a Azure Migrate korábbi verziójában:

1. Nyissa meg azt az Azure-erőforráscsoportot, amelyben a projekt létrejött.
2. Válassza ki a törölni kívánt áttelepíthető projektet. Az erőforrás típusa áttelepítési projekt, és törli azt.


### <a name="delete-a-workspace"></a>Munkaterület törlése

Tallózással keresse meg a projekthez csatolt Log Analytics munkaterületet.
* Ha még nem törölte a Azure Migrate projektet, a munkaterületre mutató hivatkozást az **Essentials** > **Server Assessment**webhelyen találja.
       ![LA munkaterület](./media/troubleshooting-general/loganalytics-workspace.png)

     * If you've already deleted the Azure Migrate project, select **Resource Groups** in the left pane of the Azure portal. Locate the workspace in the relevant resources group, and [follow the instructions](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) to delete it.


## <a name="error-requests-must-contain-user-identity-headers"></a>Hiba: a kérelmeknek felhasználói azonosító fejléceket kell tartalmazniuk.

Projekt létrehozásakor ez a hiba arra utalhat, hogy nincs hozzáférése a szervezet Azure Active Directory (Azure AD) bérlőhöz.

- Amikor első alkalommal ad hozzá egy Azure AD-bérlőhöz, e-mailben meghívót kap a bérlőhöz való csatlakozáshoz.
- Fogadja el a meghívót, hogy sikeresen bekerüljön a bérlőbe.
    - Ha nem látja az e-mailt, lépjen kapcsolatba a felhasználóval a bérlőhöz, és kérje meg őket, hogy küldje el újra [a meghívót](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users) .
    - A meghívót tartalmazó e-mail fogadása után nyissa meg a meghívót, és válassza ki a hivatkozást a meghívás elfogadásához. Ezután jelentkezzen ki a Azure Portalból, és jelentkezzen be újra. (a böngésző frissítése nem fog működni.) Ezután megkezdheti az áttelepítési projekt létrehozását.


## <a name="error-invalid-ovf-manifest-entry"></a>Hiba: "érvénytelen OVF manifest-bejegyzés"

Ha "a megadott jegyzékfájl érvénytelen: érvénytelen OVF manifest-bejegyzés" hibaüzenet jelenik meg, tegye a következőket:

1. Ellenőrizze, hogy a Azure Migrate készülék PETESEJTJEInek fájlja helyesen van-e letöltve a kivonatoló értékének ellenőrzésével. [További információk](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Ha a kivonatoló érték nem egyezik, töltse le újra a PETESEJT-fájlt, majd próbálja megismételni a telepítést.
2. Ha a telepítés továbbra is meghiúsul, és a VMware vSphere ügyfél használatával telepíti a OVF-fájlt, próbálja meg telepíteni a vSphere webes ügyfelén keresztül. Ha az üzembe helyezés továbbra is sikertelen, próbálkozzon más böngészővel.
3. Ha a vSphere webes ügyfélprogramot használja, és vCenter Server 6,5-es vagy 6,7-es számítógépen szeretné telepíteni, próbálja meg közvetlenül telepíteni a PETESEJTeket az ESXi-gazdagépre:
   - Kapcsolódjon közvetlenül az ESXi-gazdagéphez (vCenter Server helyett) a webes ügyféllel (https://<*gazdagép IP-címe*>/UI).
   - A **kezdőlap** > **leltár**területen válassza a **fájl** > **OVF-sablon telepítése**lehetőséget. Tallózással keresse meg a PETESEJTeket, és fejezze be a telepítést.
4. Ha az üzembe helyezés továbbra is sikertelen, forduljon Azure Migrate támogatási szolgálathoz.

## <a name="appliance-cant-connect-to-the-internet"></a>A készülék nem tud csatlakozni az internethez

Ez akkor fordulhat elő, ha a berendezés számítógépe proxy mögött van.

- Győződjön meg arról, hogy megadja az engedélyezési hitelesítő adatokat, ha a proxynak szüksége van rájuk.
- Ha URL-alapú tűzfal-proxyt használ a kimenő kapcsolatok vezérléséhez, adja hozzá ezeket az URL-címeket egy engedélyezési listához:

    - [A VMware Assessment URL-címei](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
    - [A Hyper-V értékelésének URL-címei](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
    - [A VMware ügynök nélküli Migrálás URL-címei](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
    - [A VMware-ügynök alapú áttelepítés URL-címei](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
    - [A Hyper-V áttelepítésének URL-címei](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

- Ha elfogó proxyt használ az internethez való csatlakozáshoz, [ezeket a lépéseket követve](https://docs.microsoft.com/azure/migrate/concepts-collector)importálja a tanúsítványt a berendezés virtuális gépén.

## <a name="errordatetime-synchronization"></a>Hiba: dátum/idő szinkronizálása

A dátum-és időszinkronizálással (802) kapcsolatos hiba azt jelzi, hogy a kiszolgáló órája több mint öt percen belül nem lehet szinkronizálva az aktuális idővel. A gyűjtő virtuális gép órájának módosítása a jelenlegi időpontra:

1. Nyisson meg egy rendszergazdai parancssort a virtuális gépen.
2. Az időzóna ellenõrzéséhez futtassa a **w32tm/TZ**.
3. Az idő szinkronizálásához futtassa a **w32tm resync**.


## <a name="error-unabletoconnecttoserver"></a>Hiba: UnableToConnectToServer

Ha ezt a hibaüzenetet kapja, előfordulhat, hogy nem tud csatlakozni a (z) vCenter Server *servername*. com: 9443. A hiba részletei azt jelzik, hogy nincs olyan végpont, amely a https://*servername*. com: 9443/SDK segítségével fogadja el az üzenetet.

- Győződjön meg arról, hogy a készülék legújabb verzióját futtatja-e. Ha nem, frissítse a készüléket a [legújabb verzióra](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Ha a probléma továbbra is a legújabb verzióban következik be, előfordulhat, hogy a készülék nem tudja feloldani a megadott vCenter Server nevet, vagy a megadott port helytelen lehet. Alapértelmezés szerint, ha a port nincs megadva, a gyűjtő megpróbál csatlakozni a 443-as számú portszámhoz.

    1. Pingelje a *servername*. com-t a készülékről.
    2. Ha az 1. lépés meghiúsul, próbáljon meg csatlakozni a vCenter-kiszolgálóhoz az IP-cím használatával.
    3. Azonosítsa a vCenter Serverhoz való csatlakozáshoz használandó portszámot.
    4. Ellenőrizze, hogy a vCenter Server működik-e.


## <a name="error-appliance-might-not-be-registered"></a>Hiba: lehet, hogy a berendezés nincs regisztrálva

- 60052-es hiba: "előfordulhat, hogy a készülék nem regisztrálható sikeresen a Azure Migrate projektben", ha a berendezés regisztrálásához használt Azure-fiók nem rendelkezik megfelelő engedélyekkel.
    - Győződjön meg arról, hogy a készülék regisztrálásához használt Azure-beli felhasználói fiók legalább közreműködői engedélyekkel rendelkezik az előfizetésben.
    - [További](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) információ a szükséges Azure-szerepkörökről és-engedélyekről.
- 60039-es hiba: "előfordulhat, hogy a készülék nem regisztrálható sikeresen a Azure Migrate projektben" akkor fordulhat elő, ha a regisztráció sikertelen, mert a berendezés regisztrálásához használt Azure Migrate projekt nem található.
    - A Azure Portalon, és győződjön meg arról, hogy a projekt létezik az erőforráscsoporthoz.
    - Ha a projekt nem létezik, hozzon létre egy új Azure Migrate projektet az erőforráscsoporthoz, és regisztrálja újra a készüléket. [Ismerje meg, hogyan](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) hozhat létre új projektet.

## <a name="error-key-vault-management-operation-failed"></a>Hiba: a Key Vault felügyeleti művelet sikertelen volt.

Ha a 60030-es vagy a 60031-es hibát kapja, "az Azure Key Vault felügyeleti művelet sikertelen", tegye a következőket:
- Győződjön meg arról, hogy a készülék regisztrálásához használt Azure-beli felhasználói fiók legalább közreműködői engedélyekkel rendelkezik az előfizetésben.
- Győződjön meg arról, hogy a fiók rendelkezik hozzáféréssel a hibaüzenetben megadott kulcstartóhoz, majd próbálja megismételni a műveletet.
- Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához.
- [További](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) információ a szükséges Azure-szerepkörökről és-engedélyekről.

## <a name="fix-discovery-couldnt-be-initiated"></a>Javítás: nem sikerült elindítani a felderítést

60028-es hiba: "a felderítés hiba miatt nem indítható el. A gazdagépek vagy fürtök megadott listája nem tudta végrehajtani a műveletet, mert nem sikerült elindítani a felderítést a hiba miatt, mert a virtuális gép információinak elérése vagy beolvasása során probléma merült fel. A többi gazdagép hozzáadása sikeresen megtörtént.

- Adja hozzá a hibaüzenetben felsorolt gazdagépeket a **gazdagép hozzáadása** lehetőség használatával.
- Ha érvényesítési hiba történt, tekintse át a Szervizelési útmutatót a hibák kijavításához, majd próbálkozzon újra a **Mentés és a felderítés megkezdése** lehetőséggel.

## <a name="fix-azure-ad-operation-failed-60025"></a>Javítás: az Azure AD-művelet nem sikerült (60025)

60025-es hiba: "az Azure AD-művelet nem sikerült. Hiba történt az Azure AD-alkalmazás létrehozásakor vagy frissítésekor: akkor következik be, amikor a felderítés elindításához használt Azure-felhasználói fiók eltér a berendezés regisztrálásához használt fióktól. Tegye a következők valamelyikét:

- Győződjön meg arról, hogy a felderítést kezdeményező felhasználói fiók megegyezik a berendezés regisztrálásához használttal.
- Adja meg Azure Active Directory alkalmazás hozzáférési engedélyeit ahhoz a felhasználói fiókhoz, amelyhez a felderítési művelet sikertelen.
- Törölje az Azure Migrate projekthez korábban létrehozott erőforráscsoportot. Hozzon létre egy másik erőforráscsoportot az újraindításhoz.
- [További](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) információ Azure Active Directory alkalmazás engedélyeiről.


## <a name="discovered-vms-not-in-portal"></a>Felderített virtuális gépek nem a portálon

Ha elindítja a felderítést, hogy a **kiszolgáló értékelése** és a **kiszolgáló áttelepítése** **folyamatban van**, de még nem látja a virtuális gépeket a portálon, vegye figyelembe a következőket:

- A felderítésnek a készülékről való elindítása után körülbelül 15 percet vesz igénybe a VMware virtuális gépek felderítése, és körülbelül két perc a Hyper-V virtuális gép felderítéséhez hozzáadott összes gazdagép esetében.
- Ha a várakozási időszakok után továbbra is a **felderítés folyamatban** van, akkor a **kiszolgálók** lapon válassza a **frissítés** lehetőséget. Ennek meg kell jelennie a felderített kiszolgálók számának a **kiszolgáló értékelése** és a **kiszolgáló áttelepítése**során.


## <a name="deleted-vms-in-the-portal"></a>Virtuális gépek törölve a portálon

Ha olyan készüléket helyezett üzembe, amely folyamatosan felfedi a helyszíni környezetet, de a törölt virtuális gépek továbbra is megjelennek a portálon, vegye figyelembe a következőket:  

- Akár 30 percet is igénybe vehet, amíg a berendezés által összegyűjtött felderítési adatok megjelennek a portálon.
- Ha 30 perc elteltével nem jelenik meg naprakész információ, frissítse az adatokat a következő lépésekkel:

    1. A **kiszolgálók** > **Azure Migrate kiszolgáló értékelése**lapon válassza az **Áttekintés**lehetőséget.
    2. A **kezelés**területen válassza a **Agent Health**
    3. Válassza az **ügynök frissítése**lehetőséget.
    1. Várjon, amíg a frissítési művelet befejeződik. Ekkor megjelenik a naprakész információ.

## <a name="vm-information-isnt-in-the-portal"></a>A virtuális gép adatai nem szerepelnek a portálon

- Akár 30 percet is igénybe vehet, amíg a berendezés által összegyűjtött felderítési adatok megjelennek a portálon.
- Ha 30 perc elteltével nem jelenik meg naprakész információ, frissítse az adatokat a következő lépésekkel:

    1. A **kiszolgálók** > **Azure Migrate kiszolgáló értékelése**lapon válassza az **Áttekintés**lehetőséget.
    2. A **kezelés**területen válassza a **Agent Health**
    3. Válassza az **ügynök frissítése**lehetőséget.
    1. Várjon, amíg a frissítési művelet befejeződik. Ekkor megjelenik a naprakész információ.


## <a name="fix-cant-connect-to-host-or-cluster"></a>Javítás: nem lehet csatlakozni a gazdagéphez vagy a fürthöz

50004-es hiba: "nem lehet csatlakozni egy gazdagéphez vagy fürthöz, mert a kiszolgáló neve nem oldható fel. WinRM-hibakód: 0x803381B9 "akkor fordulhat elő, ha a berendezés Azure DNS szolgáltatása nem tudja feloldani a megadott fürtöt vagy állomásnevet.

- Ha ezt a hibát látja a fürtön, a fürt teljes tartományneve.
- Előfordulhat, hogy ez a hiba a fürtben lévő gazdagépek esetében is megjelenik. Ez azt jelzi, hogy a berendezés tud csatlakozni a fürthöz, de a fürt olyan állomásnévket ad vissza, amelyek nem teljes tartománynevek. A hiba megoldásához frissítse a gazdagépek fájlját a készüléken az IP-cím és az állomásnevek hozzárendelésének hozzáadásával:
    1. Nyissa meg a Jegyzettömböt rendszergazdaként.
    2. Nyissa meg a C:\Windows\System32\Drivers\etc\hosts fájlt.
    3. Adja hozzá az IP-címet és az állomásnevet egy sorban. Ismételje meg az összes olyan gazdagépet vagy fürtöt, ahol ez a hiba látható.
    4. Mentse és zárja be a hosts fájlt.
    5. Győződjön meg arról, hogy a készülék tud-e csatlakozni a gazdagépekhez a berendezés-kezelő alkalmazás használatával. 30 perc elteltével a Azure Portalban láthatja a gazdagépek legfrissebb információit.

## <a name="application-discovery-issues"></a>Alkalmazás-felderítési problémák

Az alkalmazások felderítése jelenleg csak a VMware virtuális gépek esetében támogatott. A Hyper-V virtuális gépek és a fizikai kiszolgálók támogatása a jövőben is engedélyezve lesz.

Az alkalmazások felderítéséhez virtuálisgép-hitelesítő adatokat kell megadnia a berendezésben, ha nem adta meg a virtuális gép hitelesítő adatait a berendezésben, az alkalmazások felderítése nem fog működni. [További](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions) információ a vCenter Server és a VMWare virtuális gépekhez szükséges hozzáférési jogosultságokról. Ha a berendezésben virtuális gép hitelesítő adatait adta meg, és az alkalmazás felderítése sikertelen, tekintse át a következő táblázatot a hiba okának megállapításához és a Szervizelési művelethez:

**Hibakód** | **Üzenetet** | **Lehetséges ok** | **Javasolt művelet**
--- | --- | --- | ---
10000 | Nem sikerült felderíteni a kiszolgálóra telepített alkalmazásokat. | Ez akkor fordulhat elő, ha a kiszolgálón futó operációs rendszer sem Windows, sem Linux. | A telepített alkalmazások felderítése csak Windows-és Linux-kiszolgálókon támogatott.
10001 | Nem sikerült beolvasni a kiszolgálót telepítő alkalmazásokat. | Ennek oka egy belső hiba, mert hiányoznak fájlok a berendezésben. | Vegye fel a kapcsolatot Microsoft ügyfélszolgálata.
10002 | Nem sikerült beolvasni a kiszolgálót telepítő alkalmazásokat. | Ez akkor fordulhat elő, ha a Azure Migrate készülék felderítési ügynöke nem működik megfelelően. | A probléma 24 órán belül automatikusan fel lesz oldva. Ha a probléma továbbra is fennáll, vegye fel a kapcsolatot Microsoft ügyfélszolgálata.
10003 | Nem sikerült beolvasni a kiszolgálót telepítő alkalmazásokat. | Ez akkor fordulhat elő, ha a felderítési ügynök nem működik megfelelően. | A probléma 24 órán belül automatikusan fel lesz oldva. Ha a probléma továbbra is fennáll, vegye fel a kapcsolatot Microsoft ügyfélszolgálata.
10004 | Nem sikerült felderíteni a telepített alkalmazásokat < Windows/Linux > gépekhez. |  A < Windows/Linux > gépekhez való hozzáféréshez szükséges hitelesítő adatok nem lettek megadva a Azure Migrate berendezésben | Adja meg a hitelesítő adatokat a Azure Migrate berendezésben, amely hozzáféréssel rendelkezik a < Windows/Linux > gépekhez.
10005 | Nem lehet hozzáférni a helyszíni kiszolgálóhoz. | Ez akkor fordulhat elő, ha a számítógép számára a kiszolgálóhoz való hozzáféréshez megadott hitelesítő adatok helytelenek. | Frissítse a berendezésben megadott hitelesítő adatokat, és ellenőrizze, hogy a kiszolgáló elérhető-e a hitelesítő adatok használatával.
10006 | Nem lehet hozzáférni a helyszíni kiszolgálóhoz. | Ez akkor fordulhat elő, ha a kiszolgálón futó operációs rendszer sem Windows, sem Linux. | A telepített alkalmazások felderítése csak Windows-és Linux-kiszolgálókon támogatott.
9000 | Nem sikerült felderíteni a virtuális gépre telepített alkalmazásokat. | Előfordulhat, hogy a VMware-eszközök nincsenek telepítve vagy sérültek. | Telepítse/Telepítse újra a VMware-eszközöket a virtuális gépen, és ellenőrizze, hogy fut-e.
9001 | Nem sikerült felderíteni a virtuális gépre telepített alkalmazásokat. | Előfordulhat, hogy a VMware-eszközök nincsenek telepítve vagy sérültek. | Telepítse/Telepítse újra a VMware-eszközöket a virtuális gépen, és ellenőrizze, hogy fut-e.
9002 | Nem sikerült felderíteni a virtuális gépre telepített alkalmazásokat. | Előfordulhat, hogy a VMware-eszközök nem futnak. | Telepítse/Telepítse újra a VMware-eszközöket a virtuális gépen, és ellenőrizze, hogy fut-e.
9003 | Nem sikerült felderíteni a kiszolgálóra telepített alkalmazásokat. | Ez akkor fordulhat elő, ha a kiszolgálón futó operációs rendszer sem Windows, sem Linux. | A telepített alkalmazások felderítése csak Windows-és Linux-kiszolgálókon támogatott.
9004 | Nem sikerült felderíteni a kiszolgálóra telepített alkalmazásokat. | Ez akkor fordulhat elő, ha a virtuális gép ki van kapcsolva. | A telepített alkalmazások a kiszolgálón való felderítéséhez győződjön meg arról, hogy a virtuális gép be van kapcsolva.
9005 | Nem sikerült felderíteni a virtuális gépre telepített alkalmazásokat. | Ez akkor fordulhat elő, ha a virtuális gépen futó operációs rendszer sem Windows, sem Linux. | A telepített alkalmazások felderítése csak Windows-és Linux-kiszolgálókon támogatott.
9006 | Nem sikerült beolvasni a kiszolgálót telepítő alkalmazásokat. | Ez akkor fordulhat elő, ha a felderítési ügynök nem működik megfelelően. | A probléma 24 órán belül automatikusan fel lesz oldva. Ha a probléma továbbra is fennáll, vegye fel a kapcsolatot Microsoft ügyfélszolgálata.
9007 | Nem sikerült beolvasni a kiszolgálót telepítő alkalmazásokat. | Ez akkor fordulhat elő, ha a felderítési ügynök nem működik megfelelően. | A probléma 24 órán belül automatikusan fel lesz oldva. Ha a probléma továbbra is fennáll, vegye fel a kapcsolatot Microsoft ügyfélszolgálata.
9008 | Nem sikerült beolvasni a kiszolgálót telepítő alkalmazásokat. | A probléma belső hiba miatt fordulhat elő.  | A probléma 24 órán belül automatikusan fel lesz oldva. Ha a probléma továbbra is fennáll, vegye fel a kapcsolatot Microsoft ügyfélszolgálata.
9009 | Nem sikerült beolvasni a kiszolgálót telepítő alkalmazásokat. | A probléma akkor fordulhat elő, ha a kiszolgálón a Windows felhasználói fiókok felügyelete (UAC) beállításai korlátozzák, és megakadályozzák a telepített alkalmazások felderítését. | Keresse meg a "felhasználói fiókok felügyelete" beállításait a kiszolgálón, és konfigurálja az UAC beállítást a kiszolgálón úgy, hogy az az alacsonyabb két szint egyike legyen.
9010 | Nem sikerült beolvasni a kiszolgálót telepítő alkalmazásokat. | A probléma belső hiba miatt fordulhat elő.  | A probléma 24 órán belül automatikusan fel lesz oldva. Ha a probléma továbbra is fennáll, vegye fel a kapcsolatot Microsoft ügyfélszolgálata.
8084 | A VMware-hiba miatt nem sikerült felderíteni az alkalmazásokat: <Exception from VMware> | Az Azure Migrate készülék VMware API-kat használ az alkalmazások felderítéséhez. Ez a probléma akkor fordulhat elő, ha a vCenter Server az alkalmazások felderítésére tett kísérlet során kivételt okozott. A VMware hibaüzenet jelenik meg a portálon megjelenített hibaüzenetben. | Tekintse át a [VMware-dokumentációt](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html), keresse meg a hibaüzenetet, és kövesse a VMware-cikkben leírt hibaelhárítási lépéseket a probléma megoldásához. Ha továbbra sem tudja elhárítani a problémát, Microsoft ügyfélszolgálata.


## <a name="fix-assessment-readiness"></a>Kiértékelési készültség javítása

Javítsa ki az értékelés készültségi problémáit az alábbiak szerint:

**Kérdés** | **Javítsa ki**
--- | ---
Nem támogatott rendszerindítási típus | Az Azure nem támogatja az EFI rendszerindítási típussal rendelkező virtuális gépeket. Javasoljuk, hogy az áttelepítés futtatása előtt alakítsa át a rendszerindítási típust BIOS-ra. <br/><br/>Az ilyen virtuális gépek áttelepítésének kezeléséhez Azure Migrate kiszolgáló áttelepítését használhatja. Az áttelepítés során a rendszer a virtuális gép rendszerindítási típusát a BIOS-ba konvertálja.
Feltételesen támogatott Windows operációs rendszer | Az operációs rendszer elérte a támogatás befejezési dátumát, és az [Azure-támogatáshoz](https://aka.ms/WSosstatement)egyéni támogatási szerződés (CSA) szükséges. Az Azure-ba való Migrálás előtt érdemes lehet frissíteni.
Nem támogatott Windows operációs rendszer | Az Azure csak a [kiválasztott Windows operációsrendszer-verziókat](https://aka.ms/WSosstatement)támogatja. Érdemes lehet frissíteni a gépet az Azure-ba való áttelepítés előtt.
Feltételesen támogatott Linux operációs rendszer | Az Azure csak a [kiválasztott LINUXOS operációsrendszer-verziókat](../virtual-machines/linux/endorsed-distros.md)támogatja. Érdemes lehet frissíteni a gépet az Azure-ba való áttelepítés előtt.
Nem támogatott Linux operációs rendszer | Előfordulhat, hogy a gép az Azure-ban indul el, de az Azure nem biztosít operációs rendszer támogatását. Javasoljuk, hogy az Azure-ba való Migrálás előtt frissítsen egy [támogatott Linux-verzióra](../virtual-machines/linux/endorsed-distros.md) .
Ismeretlen operációs rendszer | A virtuális gép operációs rendszere a következőként lett megadva: vCenter Server. Ez a viselkedés blokkolja Azure Migrate a virtuális gép Azure-készültségének ellenőrzéséhez. Mielőtt áttelepíti a számítógépet, győződjön meg arról, hogy az Azure [támogatja](https://aka.ms/azureoslist) az operációs rendszert.
Nem támogatott bites verzió | Az 32 bites operációs rendszerekkel rendelkező virtuális gépek az Azure-ban indíthatók, de javasoljuk, hogy az Azure-ba való Migrálás előtt frissítsen a 64-bites verzióra.
Microsoft Visual Studio-előfizetést igényel | A gép Windows-ügyfél operációs rendszert futtat, amely csak Visual Studio-előfizetésen keresztül támogatott.
Nem található virtuális gép a szükséges tárolási teljesítményhez | A géphez szükséges tárolási teljesítmény (bemeneti/kimeneti műveletek száma másodpercenként [IOPS] és átviteli sebesség) meghaladja az Azure-beli virtuális gépek támogatását. Csökkentse a gép tárolási követelményeit az áttelepítés előtt.
Nem található virtuális gép a szükséges hálózati teljesítményhez | A gép számára szükséges hálózati teljesítmény (be/ki) meghaladja az Azure-beli virtuális gépek támogatását. Csökkentse a gép hálózati követelményeit.
A virtuális gép nem található a megadott helyen | A Migrálás előtt használjon másik célhelyet.
Egy vagy több nem megfelelő lemez | A virtuális géphez csatolt egy vagy több lemez nem felel meg az Azure követelményeinek. Egy<br/><br/> Azure Migrate: a kiszolgáló értékelése jelenleg nem támogatja ultra SSD lemezek használatát, és a prémium szintű felügyelt lemezekre vonatkozó korlátok alapján értékeli a lemezeket (32 TB).<br/><br/> Győződjön meg arról, hogy a virtuális géphez csatolt minden egyes lemez mérete < 64 TB (ultra SSD-lemezek által támogatott).<br/><br/> Ha nem, csökkentse a lemez méretét az Azure-ba való Migrálás előtt, vagy használjon több lemezt az Azure-ban, és [csoportosítsa őket](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) a nagyobb tárolási korlátok eléréséhez. Győződjön meg arról, hogy az egyes lemezek által igényelt teljesítmény (IOPS és átviteli sebesség) támogatott az Azure által [felügyelt virtuálisgép-lemezek](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)esetében.
Egy vagy több nem megfelelő hálózati adapter. | A nem használt hálózati adapterek eltávolítása a gépről az áttelepítés előtt.
A lemezek száma meghaladja a korlátot | Az áttelepítés előtt távolítsa el a nem használt lemezeket a gépről.
A lemez mérete meghaladja a korlátot | Azure Migrate: a kiszolgáló értékelése jelenleg nem támogatja ultra SSD lemezek használatát, és a prémium szintű lemezes korlátok alapján értékeli a lemezeket (32 TB).<br/><br/> Az Azure azonban a legfeljebb 64 TB méretű lemezeket támogatja (ultra SSD lemezek által támogatott). A lemezeket az áttelepítés előtt kevesebb mint 64 TB-ra csökkenti, vagy több lemezt is használhat az Azure-ban [, és a](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) nagyobb tárolási korlátok eléréséhez.
A lemez nem érhető el a megadott helyen | A Migrálás előtt ellenőrizze, hogy a lemez a célhelyen van-e.
A lemez nem érhető el a megadott redundancia esetén | A lemeznek az értékelési beállításokban definiált redundancia-tárolási típust kell használnia (alapértelmezés szerint LRS).
Belső hiba miatt nem sikerült meghatározni a lemez alkalmasságát | Próbálja meg létrehozni a csoport új értékelését.
Nem található a szükséges magokkal és memóriával rendelkező virtuális gép | Az Azure nem talált megfelelő virtuálisgép-típust. A Migrálás előtt csökkentse a helyszíni gép memóriáját és a magok számát.
Belső hiba miatt nem sikerült meghatározni a virtuális gép alkalmasságát | Próbálja meg létrehozni a csoport új értékelését.
Belső hiba miatt nem sikerült meghatározni egy vagy több lemez alkalmasságát | Próbálja meg létrehozni a csoport új értékelését.
Belső hiba miatt nem sikerült meghatározni egy vagy több hálózati adapter megfelelőségét | Próbálja meg létrehozni a csoport új értékelését.

## <a name="linux-vms-are-conditionally-ready"></a>A Linux rendszerű virtuális gépek "feltételesen kész"

A kiszolgáló értékelése a Linux rendszerű virtuális gépeket "feltételesen felkészültként" jelöli meg, mivel a kiszolgáló értékelésében egy ismert rés van

- A rés megakadályozza a helyszíni virtuális gépeken telepített Linux operációs rendszer másodlagos verziójának észlelését.
- Például a RHEL 6,10 esetében a kiszolgáló-Assessment jelenleg csak az RHEL 6 verziót észleli operációs rendszerként.
-  Mivel az Azure csak a Linux bizonyos verzióit támogatja, a Linux rendszerű virtuális gépek jelenleg feltételesen készen állnak a kiszolgáló értékelése során.
- Megtekintheti, hogy a helyszíni virtuális gépen futó Linux operációs rendszer az Azure [Linux-támogatás](https://aka.ms/migrate/selfhost/azureendorseddistros)áttekintésével van-e jóváhagyva az Azure-ban.
-  A támogatott terjesztés ellenőrzése után figyelmen kívül hagyhatja ezt a figyelmeztetést.

## <a name="azure-skus-exceed-on-premises-sizing"></a>Az Azure SKU-kal meghaladja a helyszíni méretezést

Azure Migrate kiszolgáló értékelése a felmérés típusa alapján több magot és memóriát is javasolhat az Azure-beli virtuális gépekhez, mint az aktuális helyszíni foglalás:


- A VM SKU-javaslat az értékelés tulajdonságaitól függ.
- Ezt a kiszolgáló értékelése során végrehajtott értékelés típusa befolyásolja: *teljesítmény-alapú* *vagy helyszíni*.
- A teljesítmény-alapú értékelések esetében a kiszolgáló értékelése a helyszíni virtuális gépek (CPU, memória, lemez és hálózat kihasználtsága) kihasználtsági adatait veszi figyelembe a helyszíni virtuális gépekhez tartozó megfelelő virtuálisgép-SKU meghatározásához. Emellett a hatékony kihasználtság meghatározásakor is felvesz egy kényelmi tényezőt.
- A helyszíni méretezés esetén a teljesítményadatok nem számítanak, és a célként megadott SKU-t a helyszíni kiosztás alapján ajánlott használni.

Ha szeretné megmutatni, hogy ez milyen hatással lehet a javaslatokra, vessünk egy példát:

A helyszíni virtuális gép négy maggal és nyolc GB memóriával rendelkezik, 50%-os CPU-kihasználtsággal és 50%-os memória-kihasználtsággal, valamint a 1,3 megadott kényelmi tényezővel.

-  Ha az értékelés **a helyszínen**történik, az Azure-beli VM-SKU 4 maggal és 8 GB memóriát ajánlott.
- Ha az értékelés teljesítmény-alapú, a processzor és a memória tényleges kihasználtsága (50%-a 4 mag * 1,3 = 2,6 magok 50, valamint a 8 GB-os memória használata * 1,3 = 5,3-GB memória), a négy mag (a legközelebbi támogatott alapszám) és a nyolc GB memória (a legközelebbi támogatott a memória mérete) ajánlott.
- [További](concepts-assessment-calculation.md#sizing) információ az értékelés méretezéséről.

## <a name="azure-disk-skus-bigger-than-on-premises"></a>A helyszínen nagyobb méretű Azure Disk SKU-lemez

Azure Migrate kiszolgáló értékelése az értékelés típusától függően nagyobb lemezt javasolhat.
- A kiszolgáló értékelése során a lemez méretezése két értékelési tulajdonságtól függ: a méretezési feltételektől és a tárolási típustól.
- Ha a méretezési feltétel **teljesítmény-alapú**, és a tároló típusa **automatikus**, a rendszer a lemez IOPS és átviteli értékeit veszi figyelembe a céllemez típusának (standard HDD, standard SSD vagy prémium) azonosításakor. Ezt követően a lemezből származó SKU-t ajánlott használni, és a javaslat a helyszíni lemez méretére vonatkozó követelményeket is figyelembe veszi.
- Ha a méretezési feltétel **teljesítmény-alapú**, és a tárolási típus **prémium**, az Azure-ban prémium szintű lemezes SKU-t ajánlott használni a helyszíni lemez IOPS, átviteli sebessége és mérete alapján. Ugyanazt a logikát kell használni a lemez méretezéséhez, ha a méretezési feltételek **a helyszínen** vannak, és a tárolási típus **standard HDD**, **standard SSD**vagy **prémium**.

Ha például egy helyszíni lemez 32 GB memóriával rendelkezik, de a lemez összesített olvasási és írási IOPS értéke 800 IOPS, a Server Assessment a prémium szintű lemezt javasolja (a magasabb IOPS-követelmények miatt), majd javaslatot tesz az r-t támogató lemez SKU-ra. zükséges-IOPS és-méret. Ebben a példában a legjobb megoldást a P15-ös változat (256 GB, 1100 IOPS) adja. Bár a helyszíni lemez által igényelt méret 32 GB volt, a Server Assessment a helyszíni lemez magas IOPS követelménye miatt nagyobb lemezt javasol.

## <a name="fix-percentage-of-utilized-core-or-memory-missing"></a>Javítás: hiányzó felhasznált mag vagy memória százalékos aránya

A kiszolgáló-értékelési jelentések "PercentageOfCoresUtilizedMissing" vagy "PercentageOfMemoryUtilizedMissing", ha a Azure Migrate berendezés nem tud teljesítményadatokat gyűjteni a megfelelő helyszíni virtuális gépekhez.

- Ez akkor fordulhat elő, ha a virtuális gépek ki vannak kapcsolva az értékelés időtartama alatt. A készülék nem tud teljesítményadatokat gyűjteni a virtuális gép számára, ha ki van kapcsolva.
- Ha csak a teljesítményszámlálók hiányoznak, és a Hyper-V virtuális gépeket próbálja értékelni, ellenőrizze, hogy a dinamikus memória engedélyezve van-e ezeken a virtuális gépeken. Csak a Hyper-V virtuális gépek ismert hibája van, amelyben egy Azure Migrate berendezés nem tudja gyűjteni a memória-kihasználtsági adatokat olyan virtuális gépek számára, amelyeken nincs engedélyezve a dinamikus memória.
- Ha a teljesítményszámlálók bármelyike hiányzik, Azure Migrate kiszolgáló értékelése visszaesik a lefoglalt magokra és a memóriára, és a virtuális gép megfelelő méretét javasolja.

## <a name="is-the-vm-os-license-cost-included-in-cost-assessment"></a>A virtuális gép operációs rendszerének licence a Cost Assessment része?

Azure Migrate a kiszolgáló értékelése jelenleg csak Windows rendszerű gépek esetében veszi figyelembe az operációsrendszer-licenc költségeit. A Linux rendszerű gépek licencelési költségei jelenleg nem tekintendők.

## <a name="performance-history-and-percentile-use"></a>A teljesítmény előzményei és a percentilis használata

Ezek a tulajdonságok csak a Azure Migrate kiszolgáló értékelése során alkalmazott teljesítmény-alapú méretezésre vonatkoznak.

A Server Assessment folyamatosan gyűjti a helyszíni gépek teljesítményadatait, és ezek alapján tesz javaslatot az Azure-beli virtuálisgép- és lemez-termékváltozatra. A teljesítményadatokat a kiszolgáló értékelése a következőképpen gyűjti össze:
- A Azure Migrate készülék folyamatosan a helyszíni környezetet a VMware virtuális gépek esetében 20 másodpercenként, a Hyper-V virtuális gépek esetében pedig 30 másodpercenként gyűjti a valós idejű kihasználtsági adatokat.
- A készülék 10 percenként összesíti a 20 vagy 30 másodperces mintát, hogy egyetlen adatpontot hozzon létre 10 percenként. Az egyetlen adatpont létrehozásához a készülék kiválasztja a 20 másodperc és a 30 másodperc közötti csúcsérték értéket, majd elküldi az Azure-nak.
- Amikor értékelést hoz létre a Server Assessmentben a teljesítmény-időtartam és a teljesítményelőzmények percentilisértéke alapján, a rendszer meghatározza a jellemző kihasználtsági értéket. Ha például a teljesítmény előzményei egy hét, és a percentilis kihasználtsága 95., a Azure Migrate az utolsó egy hétre vonatkozó 10 perces mintát rendezi növekvő sorrendbe, majd kijelöli a 95. percentilis értéket a reprezentatív értékként.
- A 95. percentilis értéke biztosítja, hogy figyelmen kívül hagyja a kiugró adatokat, amelyek akkor szerepelhetnek, ha kiveszi a esetek 99% percentilis értékét.
- Ha az időszakra vonatkozó csúcsérték-használatot szeretné kihagyni, és nem szeretné lemondani a kiugró adatokat, válassza ki a esetek 99% percentilis értéket a percentilis kihasználtsága érdekében.


## <a name="i-cant-find-dependency-visualization-for-azure-government"></a>Nem találom a függőségi vizualizációt Azure Government

Azure Migrate a függőségi vizualizáció funkciójának Service Map függ. Mivel a Service Map jelenleg nem érhető el a Azure Governmentban, ez a funkció nem érhető el a Azure Governmentban.

## <a name="dependencies-dont-show-after-installing-agents"></a>A függőségek nem jelennek meg az ügynökök telepítése után

Miután telepítette a függőségi vizualizációs ügynököket a helyszíni virtuális gépekre, Azure Migrate általában 15-30 percet vesz igénybe, hogy megjelenjenek a függőségei a portálon. Ha több mint 30 percet várt, győződjön meg arról, hogy a Microsoft monitoring Agent (MMA) tud csatlakozni a Log Analytics munkaterülethez.

Windows rendszerű virtuális gépek esetén:
1. A Vezérlőpulton indítsa el az MMA-t.
2. A **Microsoft monitoring Agent tulajdonságai** > **Azure log Analytics (OMS)** területen győződjön meg arról, hogy a munkaterület **állapota** zöld.
3. Ha az állapot nem zöld, próbálja meg eltávolítani a munkaterületet, és adja hozzá újra az MMA-hoz.

      ![MMA tulajdonságai párbeszédpanel](./media/troubleshooting-general/mma-status.png)

Linux rendszerű virtuális gépek esetén győződjön meg arról, hogy az MMA és a függőségi ügynök telepítési parancsai sikeresek voltak.

## <a name="supported-mma-os"></a>Támogatott MMA operációs rendszer

 Tekintse át a támogatott [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)-és [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) -operációs rendszereket.

## <a name="supported-dependency-agent-os"></a>Támogatott függőségi ügynök operációs rendszer

Tekintse át a támogatott [Windows-és Linux-](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) operációs rendszereket.

## <a name="dependencies-for-more-than-an-hour"></a>Több mint egy óra függőségei

Bár a Azure Migrate lehetővé teszi, hogy visszalépjen az előző hónapban egy adott dátumra, a függőségek megjelenítésének maximális időtartama egy óra.

A függőségi Térkép időidőtartam funkciójának használatával például megtekintheti a tegnapi függőségeket, de csak egy órás időszakra megtekintheti őket.

[A függőségi adat](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) hosszabb időtartamon keresztül történő lekérdezéséhez azonban Azure monitor naplókat is használhat.

## <a name="i-cant-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Nem tudom megjeleníteni a több mint 10 virtuális géppel rendelkező csoportok függőségeit

Azure Migrate kiszolgáló értékelése során a legfeljebb 10 virtuális géppel rendelkező [csoportok függőségeit jelenítheti](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) meg. Nagyobb csoportok esetén azt javasoljuk, hogy a függőségek megjelenítéséhez ossza fel a virtuális gépeket kisebb csoportokra.

## <a name="machines-show-install-agent-not-view-dependencies"></a>Gépek "telepítési ügynök" nem "View függőségei"

Miután az Azure-ba engedélyezte a függőségi vizualizációval rendelkező gépek áttelepítését, az alábbi viselkedés miatt előfordulhat, hogy a gépek "ügynök telepítése" műveletet mutatnak a "függőségek megtekintése" helyett.


- Az Azure-ba való Migrálás után a helyszíni gépek ki vannak kapcsolva, és az egyenértékű virtuális gépek az Azure-ban vannak. Ezek a gépek eltérő MAC-címeket vásárolnak.
- A gépek más IP-címmel is rendelkezhetnek, attól függően, hogy megtartotta-e a helyszíni IP-címet.
- Ha a MAC és az IP-címek is eltérnek a helyi környezettől, Azure Migrate nem rendeli hozzá a helyszíni gépeket a Service Map függőségi adatokhoz. Ebben az esetben az ügynököt a függőségek megtekintése helyett az ügynök telepítésére fogja látni.
- Az Azure-ba való áttelepítést követően a helyszíni gépek továbbra is a várt módon lesznek bekapcsolva. Az Azure-ban megjelenő egyenértékű gépek eltérő MAC-címet igényelnek, és különböző IP-címeket is megvásárolhatnak. Hacsak nem tiltja le a kimenő Azure Monitor ezen gépekről érkező forgalmat, Azure Migrate nem rendeli hozzá a helyszíni gépeket semmilyen Service Map függőségi adatokhoz, így a függőségek megtekintése helyett az ügynökök telepítésének lehetőségét fogja látni.


## <a name="collect-network-traffic-logs-in-portal"></a>Hálózati forgalmi naplók gyűjtése a portálon

A naplók gyűjtése a következőképpen történik:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
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
