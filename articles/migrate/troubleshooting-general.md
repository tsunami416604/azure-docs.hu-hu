---
title: Az Azure Migrate hibáinak elhárítása |} A Microsoft Docs
description: Ismert problémák az Azure Migrate szolgáltatás és a hibaelhárítási tippek gyakran előforduló hibák áttekintést nyújt.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: raynew
ms.openlocfilehash: cb1bed847f5b7afe7c1eff0243c64e8c25ddb814
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992557"
---
# <a name="troubleshoot-azure-migrate"></a>Az Azure Migrate hibaelhárítása

## <a name="troubleshoot-common-errors"></a>Gyakori hibák elhárítása

[Az Azure Migrate](migrate-overview.md) felméri a helyszíni számítási feladatokat az Azure-ba való migrálásra. Ez a cikk segítségével üzembe helyezése és az Azure Migrate szolgáltatással kapcsolatos problémák elhárítása.

### <a name="i-am-using-the-ova-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-being-shown-in-the-portal"></a>Az OVA, amely folyamatosan felderíti a helyszíni környezetben használom, de a saját helyszíni környezetben törölt virtuális gépek továbbra is alatt látható a portálon.

A folyamatos felderítési berendezés csak az folyamatosan teljesítményadatokat gyűjt, semmilyen konfigurálási változást nem észleli a helyszíni környezetben (pl. virtuális gép hozzáadása, törlése, lemez hozzáadása stb.). Ha a helyszíni környezet konfigurációja módosul, a következőket teheti a változások tükrözésére a portálon:

- További elemek (virtuális gépek, lemezek, magok stb.): A változásoknak az Azure Portalon, a felderítés a készülék leállítása és a majd indítsa el újra. Ez biztosítja, hogy a módosítások frissítése megtörténjen az Azure Migrate-projektben.

   ![Felderítés leállítása](./media/troubleshooting-general/stop-discovery.png)

- Virtuális gépek törlése: Lehet a célja, a készülék virtuális gépek törlése nem tükrözi, akkor is, ha leállítja és elindítja a felderítést. Ennek az oka, hogy a későbbi felderítések adatait a rendszer hozzáfűzi a korábbi felderítések adataihoz, nem pedig felülírja azokat. Ebben az esetben egyszerűen figyelmen kívül hagyhatja a virtuális gépet a portálon. Ehhez távolítsa el a csoportból, és számítsa újra az értékelést.

### <a name="deletion-of-azure-migrate-projects-and-associated-log-analytics-workspace"></a>Az Azure Migrate projektek és társított Log Analytics-munkaterület törlése

Azure Migrate-projekt törlésekor törli a migrálási projekt, valamint a csoportok és értékelések. Azonban ha egy Log Analytics-munkaterületet a projekthez, azt nem törli automatikusan a Log Analytics-munkaterületet. Ennek az oka lehet szükség a Log Analytics-munkaterületnek több használati esetek. Ha szeretné, valamint a Log Analytics-munkaterület törlése kell tennie azt manuálisan.

1. Keresse meg a Log Analytics-munkaterületet a projekthez.
   a. Ha még nem törölt a migrálási projekt, megtalálhatja a hivatkozás a munkaterületet a projekt áttekintő oldaláról az Essentials szakaszban.

   ![LA Workspace](./media/troubleshooting-general/LA-workspace.png)

   b. Ha már törölte a migrálási projekt, kattintson a **erőforráscsoportok** az Azure Portalon, és nyissa meg az erőforráscsoport, amelyben a munkaterület létrejött, és keresse meg azt a bal oldali panelen.
2. Kövesse az utasításokat [ebben a cikkben](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) a munkaterület törlése.

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>Migrálási projekt létrehozása nem sikerült hiba *kérelmek felhasználó identitást meghatározó fejléceket kell tartalmaznia.*

A probléma akkor fordulhat elő, a felhasználók számára, akik nem rendelkeznek hozzáféréssel a szervezet Azure Active Directory (Azure AD) bérlőhöz. Amikor a felhasználó első alkalommal adnak hozzá az Azure AD-bérlő, hallgatója kap egy e-mailes meghívó csatlakozzanak a bérlőhöz. Nyissa meg az e-mailt, és fogadja el a meghívást, a bérlő sikeresen hozzáadja a felhasználóknak kell. Ha nem látja az e-mailt, forduljon egy felhasználó, aki már hozzáfér a bérlő, és kérje meg a megadott lépéseket követve a meghívó újraküldése [Itt](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

Miután a meghívást tartalmazó e-mail érkezik, nyissa meg az e-mailt, és kattintson a hivatkozásra az e-mailben a meghívás elfogadásához kell. Miután ez megtörtént, jelentkezzen ki az Azure Portalon kell, és jelentkezzen be ismét a böngészőlap frissítése nem fog működni. Majd megpróbálkozhat a migrálási projekt létrehozása.

### <a name="i-am-unable-to-export-the-assessment-report"></a>Az értékelési jelentés exportálása nem sikerült

Ha nem tudja az értékelési jelentés exportálása a portálról, próbálkozzon az alábbi REST API-t egy letöltési URL-cím lekérése az értékelési jelentés.

1. Telepítés *armclient* a számítógépen (Ha még nincs telepítve):

  a. Egy rendszergazdai parancssort futtassa a következő parancsot: ```@powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"```

  b. Egy rendszergazda Windows PowerShell-ablakban futtassa a következő parancsot: ```choco install armclient```

2.  A letöltési URL-cím lekérése az Azure Migrate REST API használatával az értékelési jelentéshez tartozó

  a.    Egy rendszergazda Windows PowerShell-ablakban futtassa a következő parancsot: ```armclient login```

  Ekkor megnyílik az Azure bejelentkezési előugró hol kell bejelentkezni az Azure-bA.

  b.    Ugyanebben a PowerShell ablakban futtassa a következő parancsot az értékelési jelentés (cserélje le az URI-paraméterek megfelelő értékeivel, a minta API-kérelem alább) a letöltési URL-Címének lekéréséhez

       ```armclient POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Migrate/projects/{projectName}/groups/{groupName}/assessments/{assessmentName}/downloadUrl?api-version=2018-02-02```

       Mintakérelem és kimenet:

       ```PS C:\WINDOWS\system32> armclient POST https://management.azure.com/subscriptions/8c3c936a-c09b-4de3-830b-3f5f244d72e9/r
esourceGroups/ContosoDemo/providers/Microsoft.Migrate/projects/Demo/groups/contosopayroll/assessments/assessment_11_16_2
018_12_16_21/downloadUrl?api-version=2018-02-02
{
  "assessmentReportUrl": "https://migsvcstoragewcus.blob.core.windows.net/4f7dddac-f33b-4368-8e6a-45afcbd9d4df/contosopayrollassessment_11_16_2018_12_16_21?sv=2016-05-31&sr=b&sig=litQmHuwi88WV%2FR%2BDZX0%2BIttlmPMzfVMS7r7dULK7Oc%3D&st=2018-11-20T16%3A09%3A30Z&se=2018-11-20T16%3A19%3A30Z&sp=r",
  "expirationTime": "2018-11-20T22:09:30.5681954+05:30"```

3. Másolja az URL-címet a válaszból, és nyissa meg böngészőben az értékelési jelentés letöltése.

4. A jelentéskészítő letöltését követően Excel segítségével keresse meg a letöltött mappát, és nyissa meg a fájlt az Excelben a megtekintéséhez.

### <a name="performance-data-for-disks-and-networks-adapters-shows-as-zeros"></a>Lemezek és a hálózat adapterek teljesítményadatokat nullák állapota

Ez akkor fordulhat elő, ha a statisztikai beállítást szintje a vCenter-kiszolgálón kevesebb mint három. Vagy magasabb szintű három vCenter tárolja a virtuális gépek korábbi teljesítménye számítási, tárolási és hálózati. A három szint kisebb, mint a vCenter tárolási és hálózati adatok, de csak a CPU és memória adatok nem tárolja. Ebben a forgatókönyvben teljesítmény adatokat jeleníti meg, mint az Azure Migrate nulla, és az Azure Migrate biztosít a lemezek és a helyszíni gépekről gyűjtött metaadatok alapján hálózatok mérete javaslat.

Ahhoz, hogy a lemez- és teljesítményadatok gyűjtése, módosítsa a statisztikai beállítások szintjét három. Ezt követően legalább egy napot várni a környezet felderítéséhez és értékeléséhez.

### <a name="i-specified-an-azure-geography-while-creating-a-migration-project-how-do-i-find-out-the-exact-azure-region-where-the-discovered-metadata-would-be-stored"></a>Migrálási projekt, hogyan állapítható meg a felderített metaadatokat lenne tárolásához pontos Azure-régió létrehozása során egy Azure földrajzi meg?

Megnyithatja a **Essentials** című rész a **áttekintése** a projekt a pontos helyét a metaadatokat tároló azonosításához. A hely egyben véletlenszerűen földrajzi helyen belül az Azure Migrate, és nem módosítható. Ha csak egy adott régióban hozzon létre egy projektet, a migrálási projekt létrehozása a REST API-k segítségével, adja át a kívánt régiót.

   ![Projekt helye](./media/troubleshooting-general/geography-location.png)

## <a name="collector-issues"></a>Gyűjtő kapcsolatos problémák

### <a name="deployment-of-azure-migrate-collector-failed-with-the-error-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry"></a>Az Azure Migrate Collector telepítésének nem sikerült a következő hibával: A megadott jegyzékfájl érvénytelen: OVF jegyzékfájl bejegyzés érvénytelen.

1. Győződjön meg arról, ha az Azure Migrate Collector OVA-fájl a kivonatolt érték ellenőrzésével megfelelően letölti. Tekintse meg a következő [cikket](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware#verify-the-collector-appliance) a kivonatérték ellenőrzéséhez. Ha a kivonat értéke nem egyezik, töltse le újból az OVA-fájl, és próbálkozzon újra a telepítéssel.
2. Ha továbbra is sikertelen, és a VMware vSphere-ügyfelet használja az OVF telepítéséhez, próbálja meg a vSphere webes ügyfélen keresztül telepíteni. Ha továbbra is sikertelen, próbálkozzon a különböző webböngésző használatával.
3. Az OVA közvetlenül az ESXi-gazdagép telepítése a következő próbálja vSphere webes ügyféllel, és szeretné telepíteni, a vCenter Server 6.5-ös vagy 6.7, ha az alábbi lépéseket:
  - Közvetlenül (vCenter-kiszolgáló) helyett az ESXi-gazdagép csatlakozik a webes ügyfél használata (https:// <*gazdagép IP-cím*> /ui)
  - Lépjen a kezdőlapra > leltár
  - Kattintson a fájl > telepítése OVF-sablon > keresse meg az OVA és a telepítés befejezéséhez
4. Ha az üzembe helyezés továbbra is sikertelen, forduljon az ügyfélszolgálathoz az Azure Migrate.


### <a name="collector-is-not-able-to-connect-to-the-internet"></a>Gyűjtő nem sikerül csatlakozni az internethez

Ez akkor fordulhat elő, ha a gép, használja a rendszer proxy mögött van. Ellenőrizze, hogy az engedélyezési hitelesítő adatok megadása, ha a proxy szüksége van rá.
Ha bármely URL-alapú tűzfalproxyt a kimenő kapcsolat szabályozásához, ügyeljen arra, hogy az engedélyezési listára ezeket a szükséges URL-címeket használ:

**URL-cím** | **Cél**  
--- | ---
*.portal.azure.com | Szükséges ellenőrizze a kapcsolatot az Azure-szolgáltatások, és az időszinkronizálás ellenőrzéséhez bocsát ki.
*.oneget.org | Szükséges letöltése a powershell-alapú vCenter PowerCLI modul.

**A gyűjtő nem tud kapcsolódni az internetre egy tanúsítvány érvényesítési hiba miatt**

Ez akkor fordulhat elő, ha egy lehallgató proxy segítségével csatlakozzon az internethez, és nem importálta a gyűjtő virtuális gép be a proxy tanúsítvány. A proxy tanúsítvány részletes lépésekkel importálhatja [Itt](https://docs.microsoft.com/azure/migrate/concepts-collector).

**A gyűjtő nem tud kapcsolódni a projekt használatával a projekt Azonosítóját és a kulcs másolt a portálról.**

Győződjön meg arról, hogy másolja, és a megfelelő információt beillesztett. Hibaelhárítás, a Microsoft Monitoring Agent (MMA) telepítése, és győződjön meg arról, ha az MMA módon csatlakozhat a projekt:

1. A gyűjtő virtuális Gépen, töltse le a [MMA](https://go.microsoft.com/fwlink/?LinkId=828603).
2. A telepítés elindításához kattintson duplán a letöltött fájlt.
3. A telepítő a a **üdvözlő** kattintson **tovább**. A **Licencfeltételek** oldalon kattintson az **Elfogadom** gombra a feltételek elfogadásához.
4. A **célmappa**, megtartani, vagy módosítsa az alapértelmezett telepítési mappa > **tovább**.
5. A **ügynök telepítésének beállításai**válassza **Azure Log Analytics** > **tovább**.
6. Kattintson a **Hozzáadás** hozzáadása egy új Log Analytics-munkaterületet. Illessze be a projekt Azonosítóját és kulcsát, akkor a vágólapra másolt. Ezután kattintson a **Next** (Tovább) gombra.
7. Győződjön meg arról, hogy az ügynök kapcsolódik a projekthez. Ha nem érhető el, ellenőrizze a beállításokat. Ha az ügynök kapcsolódik, de a gyűjtő nem, forduljon az ügyfélszolgálathoz.


### <a name="error-802-date-and-time-synchronization-error"></a>802. hiba: Dátum és idő szinkronizálási hiba

A kiszolgáló óra lehet, hogy ki a szinkronizálást, az aktuális idő szerint több mint öt perc alatt. A gyűjtő virtuális Gépen, a következőképpen egyezik az aktuális idő, az idő módosítása:

1. Nyisson meg egy rendszergazdai jogú parancssort a virtuális gépen.
2. Ellenőrizze az időzónát, futtassa a w32tm /tz.
3. Az idő szinkronizálása w32tm/resync futtassa.

### <a name="vmware-powercli-installation-failed"></a>A VMware PowerCLI telepítése nem sikerült

Az Azure Migrate collector PowerCLI letölti és telepíti azt a készüléket. Hiba történt a PowerCLI PowerCLI adattár nem érhető el végpontok okozhatja. A hiba elhárításához próbálja meg manuális telepítését a gyűjtő virtuális Gépen a következő lépés a PowerCLI:

1. Nyissa meg a Windows Powershellt rendszergazdai módban
2. Nyissa meg a könyvtár C:\ProgramFiles\ProfilerService\VMWare\Scripts\
3. Futtassa a szkriptet InstallPowerCLI.ps1

### <a name="error-unhandledexception-internal-error-occurred-systemiofilenotfoundexception"></a>Unhandledexception hiba történt: System.IO.FileNotFoundException

A probléma akkor fordulhat elő a VMware PowerCLI telepítése való probléma miatt. Kövesse az alábbi lépéseket a probléma megoldásához:

1. Ha nem a legújabb verziót a gyűjtő berendezés [a gyűjtő frissítése a legújabb verzióra a](https://aka.ms/migrate/col/checkforupdates) , és ellenőrizze, hogy megoldódott-e a problémát.
2. Ha már rendelkezik a gyűjtő legújabb, kövesse az alábbi lépéseket hajtsa végre a PowerCLI tiszta telepítését:

   a. Zárja be a készülék webböngészőben.

   b. Állítsa le a "Az Azure Migrate Collector" szolgáltatást a Windows Service Manager (Open "Futtatási", és írja be services.msc Windows-kezelő megnyitásához). Az Azure Migrate Collector szolgáltatás kattintson a jobb gombbal, és kattintson a Leállítás gombra.

   c. A következő helyekről: VMware"kezdve az összes mappa törlése: C:\Program Files\WindowsPowerShell\Modules  
        C:\Program Files (x86)\WindowsPowerShell\Modules

   d. Indítsa újra a "Az Azure Migrate Collector" szolgáltatást a Windows Service Manager (Open "Futtatási", és írja be services.msc Windows-kezelő megnyitásához). Az Azure Migrate Collector szolgáltatás kattintson a jobb gombbal, és kattintson a Start gombra.
   
   e. Kattintson duplán a "Futtatás gyűjtő" asztali parancsikonját a gyűjtő alkalmazás elindításához. A gyűjtő alkalmazás automatikusan töltse le és telepítse a szükséges verziót fo PowerCLI.

3. Ha a fenti nem oldja meg a problémát, telepítse manuálisan [VMware PowerCLI 6.5.2](https://www.powershellgallery.com/packages/VMware.PowerCLI/6.5.2.6268016) , és ellenőrizze, hogy megoldódott-e a problémát.

### <a name="error-unabletoconnecttoserver"></a>Error UnableToConnectToServer

Nem lehet csatlakozni a „Servername.com:9443” vCenter-kiszolgálóhoz a következő hiba miatt: Nem figyelt olyan végpont a https://Servername.com:9443/sdk címen, amely fogadni tudta volna az üzenetet.

Ha Ön a gyűjtőberendezés legújabb verzióját futtatja, ha nem, frissítse a berendezés ellenőrizze a [legújabb verzió](https://docs.microsoft.com/azure/migrate/concepts-collector).

Ha a probléma továbbra is történik a legújabb verzió, annak oka az lehet, mert a naplógyűjtő gépen nem tudja feloldani a vCenter-kiszolgáló neve vagy a megadott megadott port helytelen. Alapértelmezés szerint ha a port nincs megadva, collector megpróbálja szeretne csatlakozni a 443-as portszámhoz.

1. Próbálja meg a servername.com a naplógyűjtő gépen.
2. Ha az 1. lépés meghiúsul, próbáljon meg az IP-címen keresztül csatlakozni a vCenter-kiszolgálóhoz.
3. Adja meg a megfelelő portszámot a vCenterhez történő csatlakozáshoz.
4. Végezetül pedig ellenőrizze, hogy a vCenter-kiszolgáló fut-e.

### <a name="antivirus-exclusions"></a>Víruskereső – kizárások

Az Azure Migrate berendezés felvértezni, a készülék az alábbi mappák kizárása a víruskeresővel való ellenőrzésekből kell:

- Az Azure Migrate szolgáltatás a bináris fájlokat tartalmazó mappa. Zárja ki az összes alárendelt mappát.
  %ProgramFiles%\ProfilerService  
- Az Azure Migrate webes alkalmazást. Zárja ki az összes alárendelt mappát.
  %SystemDrive%\inetpub\wwwroot
- Helyi gyorsítótár az adatbázis és naplófájlok. Az Azure migrate szolgáltatás ezt a mappát RW lemezt hozzá kell férnie.
  %SystemDrive%\Profiler

## <a name="dependency-visualization-issues"></a>Függőségek képi megjelenítés kapcsolatos problémák

### <a name="i-am-unable-to-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Nem található a függőségek képi megjelenítésének funkcióival az Azure Government-projektekhez vagyok.

A függőségek képi megjelenítésének funkcióival a Service Map függ az Azure Migrate, és mivel a Service Map jelenleg nem érhető el az Azure Government szolgáltatásban, ez a funkció nem érhető el az Azure Government szolgáltatásban.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>A Microsoft Monitoring Agent (MMA) és a függőségi ügynök telepítése a helyszíni virtuális gépek, de a függőségek most jelennek meg az Azure Migrate portálon.

Miután telepítette az ügynököket, az Azure Migrate általában tart 15 – 30 perc, a függőségek megjelenítéséhez a portálon. Ha Ön 30 percnél hosszabb ideig várakoztak, győződjön meg arról, hogy az MMA-ügynök létrejöhet a kommunikáció az OMS-munkaterülethez, a következő az alábbi lépéseket:

A Windows virtuális gépek:
1. Lépjen a **Vezérlőpult** , majd indítsa el **Microsoft Monitoring Agent**
2. Nyissa meg a **Azure Log Analytics (OMS)** előugró MMA tulajdonságai lap
3. Ügyeljen arra, hogy a **állapot** a munkaterület zöld érték.
4. Ha az állapot nem zöld, próbálja meg a munkaterület eltávolítása és újbóli hozzáadásával az MMA.
        ![MMA Status](./media/troubleshooting-general/mma-status.png)

Linux rendszerű virtuális gép győződjön meg arról, hogy sikeres volt-e az MMA és a függőségi ügynök telepítési parancsokat.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>Mik azok az MMA által támogatott operációs rendszerek?

Az MMA által támogatott Windows operációs rendszerek listája [Itt](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
Az MMA által támogatott Linux operációs rendszerek listája [Itt](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>Mik azok a függőségi ügynök által támogatott operációs rendszerek?

Függőségi ügynök által támogatott Windows operációs rendszerek listája [Itt](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems).
Függőségi ügynök által támogatott Linux operációs rendszerek listája [Itt](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="i-am-unable-to-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>Nem sikerül az Azure Migrate függőségeinek megjelenítése több mint egy óra időtartama?
Az Azure Migrate függőségeinek megjelenítése akár egy órás időtartamának teszi lehetővé. Bár az Azure Migrate lehetővé teszi, hogy térjen vissza az az előzmények között az utolsó egy hónap legfeljebb egy adott dátumot, a maximális időtartamot, amelynek jelenítheti meg a függőségeket a legfeljebb 1 óra. Például hogy használhatja tegnap függőségek megtekintése a függőségi térkép az idő időtartamát funkciót, de csak megtekintheti azt számára egy egy órás időszak. Azonban használhatja az Azure Monitor naplók [a függőségi adatok lekérdezése](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#query-dependency-data-from-log-analytics) hosszabb ideig keresztül.

### <a name="i-am-unable-to-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Nem lehet több mint 10 virtuális géppel csoportok függőségek vizualizálása vagyok?
Is [csoportokra vonatkozó függőségek vizualizálása](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) , hogy rendelkezik mentése 10 virtuális gépekhez, ha egy csoport több mint 10 virtuális géppel, azt javasoljuk, hogy felosztása kisebb csoportok a csoport, és a Függőségek megjelenítése.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>E telepített ügynökök és a függőségek képi megjelenítéséről csoportok létrehozásához használt. Most már feladatátvétel után, a gépek művelet "Ügynök telepítése" helyett "Nézet függőségei" megjelenítése
* POST tervezett vagy nem tervezett feladatátvétel esetén a helyszíni gépek ki vannak kapcsolva, és egyenértékű gépeket hoz létre az Azure-ban. Ezek a gépek egy másik MAC-címet szerezni. Előfordulhat, hogy vásárolnak, egy másik IP-cím alapján, hogy a felhasználó úgy döntött, a helyi IP-cím megőrzése, vagy nem. MAC- és IP-címek különböznek, ha az Azure Migrate a helyszíni gépek nem rendel hozzá a Service Map függőségi adatokat, és kéri a felhasználót, hogy telepítse a helyett függőségek megtekintéséhez ügynököket.
* Feladatátvételi teszt közzététele a helyszíni gépek elvárt bekapcsolva maradjon. Egyenértékű gépeket hoz létre az Azure-ban másik MAC-címet beszerezni, és előfordulhat, hogy másik IP-cím beszerzéséhez. Kivéve, ha a felhasználó blokkok kimenő Azure Monitor-naplókat forgalom, ezek a gépek Azure Migrate nem rendel hozzá a helyszíni gépek Service Map függőségi adatokat, és kéri a felhasználót, hogy telepítse a helyett függőségek megtekintéséhez ügynököket.

## <a name="troubleshoot-azure-readiness-issues"></a>Azure-kompatibilitás hibáinak elhárítása

**A probléma** | **Fix**
--- | ---
Nem támogatott rendszerindítási típus | Azure virtuális gépek nem támogatja az EFI rendszerindítás-típus. Javasoljuk, hogy a rendszerindítási típus átalakítása BIOS-ban, az áttelepítés futtatása előtt. <br/><br/>Használhat [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-migrate-on-premises-to-azure) ilyen virtuális gépek a migrálás elvégzésének, ahogy, konvertálja a rendszerindítási típus, a virtuális gép BIOS-ban az áttelepítés során.
Feltételesen támogatott Windows operációs rendszer | Az operációs rendszer támogatása megszűnt a letelt, és szüksége van egy egyéni támogatási megállapodás (CSA) szükséges [támogatja az Azure-ban](https://aka.ms/WSosstatement), fontolja meg az operációs rendszer frissítését az Azure-ba való migrálás előtt.
Nem támogatott Windows operációs rendszer | Az Azure támogatja a csak [Windows operációsrendszer-verziók kiválasztott](https://aka.ms/WSosstatement), fontolja meg a gép operációs rendszerének frissítését az Azure-ba való migrálás előtt.
Feltételesen jóváhagyott Linux operációs rendszer | Az Azure csak vannak jóváhagyva [Linux operációsrendszer-verziók kiválasztott](../virtual-machines/linux/endorsed-distros.md), fontolja meg a gép operációs rendszerének frissítését az Azure-ba való migrálás előtt.
Nem jóváhagyott Linux operációs rendszer | A gép elindulhat az Azure-ban, de nem az operációs rendszer biztosít támogatást az Azure-ban, fontolja meg az operációs rendszer, hogy egy [által támogatott Linux-verzió](../virtual-machines/linux/endorsed-distros.md) az Azure-ba való migrálás előtt
Ismeretlen operációs rendszer | Az operációs rendszer a virtuális gép vCenter-kiszolgáló, amely miatt az Azure Migrate nem tudja azonosítani az Azure készen áll-e a virtuális gép mint az "Egyéb" van megadva. Győződjön meg arról, hogy az a gépen futó operációs rendszer [támogatott](https://aka.ms/azureoslist) az Azure-ban, a gép áttelepítése előtt.
Az operációs rendszer bitszáma nem támogatott | Az operációs rendszer 32-bit-es virtuális gép elindulhat az Azure-ban, de javasoljuk, hogy a virtuális gép operációs rendszerének frissítése a 32 bites, 64 bites az Azure-ba való migrálás előtt.
A Visual Studio-előfizetést igényel. | A gépek rendelkezik egy Windows ügyfél operációs rendszer fut, amely csak Visual Studio-előfizetés használata támogatott.
Nem található a szükséges tárolási teljesítménynek megfelelő virtuális gép. | A tárolási teljesítményt (IOPS és átviteli sebesség) a gép meghaladja az Azure virtuális gépek támogatása szükséges. Csökkenti a tárolási követelményeket a gép áttelepítés előtt.
A virtuális gép nem található a szükséges hálózati teljesítmény. | A hálózati teljesítmény (bejövő és kimenő), a gép szükséges meghaladja az Azure virtuális gépek támogatása. Csökkentheti a hálózati követelmények, a gép.
A virtuális gép nem található a megadott helyen. | Használjon egy másik célhelyet az áttelepítés előtt.
Egy vagy több lemez. | Egy vagy több lemezt a virtuális Géphez csatlakoztatott Azure-követelményeknek nem felel meg. A virtuális géphez csatlakoztatott összes lemez győződjön meg arról, hogy a lemez mérete < 4 TB-os, ha nem, a lemez mérete csökkentheti az Azure-ba való migrálás előtt. Győződjön meg arról, hogy egyes lemezek által igényelt teljesítményt (IOPS és átviteli sebesség) az Azure által támogatott [a virtuális gép lemezeinek felügyelt](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits).   
Egy vagy több hálózati adapter nem megfelelő. | Távolítsa el a használaton kívüli hálózati adapterek a gépről a migrálás előtt.
A lemezek száma meghaladja a korlátot | A fel nem használt lemezek eltávolítása a gépről a migrálás előtt.
A lemezméret meghaladja a korlátot | Az Azure támogatja a lemezek mérete legfeljebb 4 TB-ig. Lemezek csökkentheti az áttelepítés előtt kevesebb mint 4 TB-ig.
A lemez nem érhető el a megadott helyen | Ellenőrizze, hogy a lemez a célhelyen áttelepítése előtt.
A lemez a megadott redundanciával nem érhető el | A lemez a redundancia tárolótípus meghatározott az értékelési beállítások (alapértelmezés szerint LRS) kell használnia.
Belső hiba miatt nem sikerült meghatározni a lemez alkalmasságát | Próbálja meg a csoport egy új értékelés létrehozása.
Nem található a szükséges magokkal és memóriával rendelkező virtuális gép | Az Azure a megfelelő VM-típus nem lehetett rendben. Csökkentse a memória és magok a helyi gép áttelepítése előtt.
Nem sikerült meghatározni a virtuális gép alkalmasságát belső hiba miatt. | Próbálja meg a csoport egy új értékelés létrehozása.
Nem sikerült meghatározni a belső hiba miatt egy vagy több lemez alkalmasságát. | Próbálja meg a csoport egy új értékelés létrehozása.
Nem sikerült meghatározni a belső hiba miatt egy vagy több hálózati adapter alkalmasságát. | Próbálja meg a csoport egy új értékelés létrehozása.


## <a name="collect-logs"></a>Naplók gyűjtése

**Hogyan gyűjthet naplókat a gyűjtő virtuális Gépen?**

Alapértelmezés szerint a naplózás engedélyezve van. Naplók az alábbi helyeken találhatók:

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

Esemény nyomkövetése for Windows gyűjteni, tegye a következőket:

1. A gyűjtő virtuális Gépen nyisson meg egy PowerShell parancsablakot.
2. Futtatás **Get-Eseménynapló - naplónév alkalmazás |} exportálás csv-eventlog.csv**.

**Hogyan gyűjtenek portál hálózati forgalmi naplók?**

1. Nyissa meg a böngészőt, és keresse meg, és jelentkezzen be [a Portal](https://portal.azure.com).
2. Nyomja le az F12 billentyűt a fejlesztői eszközök elindítása. Ha szükséges, törölje a jelet a beállítás **navigációs bejegyzések törlése**.
3. Kattintson a **hálózati** lapra, és indítsa el a hálózati forgalom rögzítése:
 - Válassza ki a Chrome-ban, **Preserve log**. A rögzítés automatikusan elindul. A piros kör azt jelzi, hogy a forgalom rögzítése folyamatban van. Ha nem jelenik meg, kattintson a fekete körre indítása
 - A Microsoft Edge/IE, a rögzítés automatikusan elindul. Ha nem, kattintson a zöld lejátszás gombra.
4. Próbálja meg reprodukálni a hibát.
5. Korábban észlelt rögzítése közben a hiba, miután Rögzítés leállítása, és mentse a rögzített tevékenység másolatát:
 - A Chrome-ban, kattintson a jobb gombbal, és kattintson a **Mentés másként HAR tartalommal**. Ez zips alkalmazást, és exportálja a naplók .har fájl.
 - A Microsoft Edge/IE, kattintson a **exportálási rögzített forgalom** ikonra. Ez tömörít, és exportálja a naplót.
6. Keresse meg a **konzol** lapot, és ellenőrizze az összes figyelmeztetést és hibát. A konzol naplófájljának mentése:
 - A Chrome-ban kattintson a jobb gombbal bárhol a konzol naplóban. Válassza ki **Mentés másként**, hogy az exportálás és a napló zip.
 - A Microsoft Edge/IE, kattintson a jobb gombbal a hibákat, és válassza **másolhatja az összes**.
7. Zárja be a fejlesztői eszközöket.

## <a name="collector-error-codes-and-recommended-actions"></a>Gyűjtő hibakódok és ajánlott műveletek

| Hibakód | Hiba neve   | Üzenet   | A lehetséges okok | Javasolt művelet  |
| --- | --- | --- | --- | --- |
| 601       | CollectorExpired               | A gyűjtő érvényessége lejárt.                                                        | A gyűjtő érvényessége lejárt.                                                                                    | Töltse le a gyűjtő egy új verzióját, majd próbálkozzon újra.                                                                                      |
| 751       | UnableToConnectToServer        | Nem sikerült csatlakozni a(z) %Name; vCenter Server-kiszolgálóhoz a következő hiba miatt: %ErrorMessage;.     | További részletekért tekintse meg a hibaüzenetet.                                                             | Hárítsa el a problémát, és próbálkozzon újra.                                                                                                           |
| 752       | InvalidvCenterEndpoint         | A(z) %Name; nem vCenter Server-kiszolgáló.                                  | Adja meg a VCenter Server-kiszolgáló adatait.                                                                       | Próbálja megismételni a műveletet helyes vCenter Server-kiszolgálói adatokkal.                                                                                   |
| 753       | InvalidLoginCredentials        | Nem sikerült csatlakozni a(z) %Name; vCenter Server-kiszolgálóhoz a következő hiba miatt: %ErrorMessage;. | Nem sikerült csatlakozni a vCenter Server-kiszolgálóhoz, mert a bejelentkezési hitelesítő adatok érvénytelenek.                             | Győződjön meg arról, hogy a megadott bejelentkezési hitelesítő adatok helyesek.                                                                                    |
| 754       | NoPerfDataAvailable           | Nem érhetők el a teljesítményadatok.                                               | A vCenter Server statisztikájának szintjét ellenőrzése Teljesítményadatok érhető el a 3-ra kell állítani. | Módosítsa a statisztika szintjét 3-asra (5 perces, 30 perces és 2 órás időtartamhoz), és próbálkozzon legalább egy napos várakozás után.                   |
| 756       | NullInstanceUUID               | A rendszer olyan gépet észlelt, mely példányának UUID azonosítója null értékű                                  | Lehetséges, hogy a vCenter Server-kiszolgáló nem megfelelő objektummal rendelkezik.                                                      | Hárítsa el a problémát, és próbálkozzon újra.                                                                                                           |
| 757       | VMNotFound                     | A virtuális gép nem található                                                  | Lehetséges, hogy a virtuális gépet törölték: %VMID;                                                                | Győződjön meg arról, hogy a vCenter-készlet hatókörének beállításakor kiválasztott virtuális gépek léteznek az észleléskor                                      |
| 758       | GetPerfDataTimeout             | A VCenter-kérés túllépte az időkorlátot. Üzenet: % Message;                                  | A vCenter Server-kiszolgáló hitelesítő adatai helytelenek                                                              | Ellenőrizze a vCenter Server hitelesítő adatait, és győződjön meg arról, hogy a vCenter Server elérhető. Próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, forduljon a támogatási szolgálathoz. |
| 759       | VmwareDllNotFound              | A VMWare.Vim DLL-fájlja nem található.                                                     | A PowerCLI nincs megfelelően telepítve.                                                                   | Ellenőrizze, ha a PowerCLI megfelelően van-e telepítve. Próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, forduljon a támogatási szolgálathoz.                               |
| 800       | ServiceError                   | Az Azure Migrate Collector szolgáltatás nem fut.                               | Az Azure Migrate Collector szolgáltatás nem fut.                                                       | A Services.msc használatával indítsa el a szolgáltatást, majd próbálja megismételni a műveletet.                                                                             |
| 801       | PowerCLIError                  | A VMware PowerCLI telepítése nem sikerült.                                          | A VMware PowerCLI telepítése nem sikerült.                                                                  | Próbálja megismételni a műveletet. Ha a probléma tartósan fennáll, telepítse manuálisan, és próbálja megismételni a műveletet.                                                   |
| 802       | TimeSyncError                  | Az idő nincs internetes időkiszolgálóval szinkronizálva.                            | Az idő nincs internetes időkiszolgálóval szinkronizálva.                                                    | Győződjön meg arról, hogy a számítógépen az időzónájának megfelelően van beállítva az idő, majd próbálja megismételni a műveletet.                                 |
| 702       | OMSInvalidProjectKey           | A megadott projektkulcs érvénytelen.                                                | A megadott projektkulcs érvénytelen.                                                                        | Próbálja megismételni a műveletet a megfelelő projektkulccsal.                                                                                              |
| 703       | OMSHttpRequestException        | Hiba történt a kérés küldése. Üzenet: % Message;                                | Ellenőrizze a projekt azonosítóját és kulcsát, és győződjön meg arról, hogy a végpont elérhető.                                       | Próbálja megismételni a műveletet. Ha nem szűnik meg a probléma, forduljon a Microsoft támogatási szolgálatához.                                                                     |
| 704       | OMSHttpRequestTimeoutException | HTTP-kérés túllépte az időkorlátot. Üzenet: % Message;                                     | Ellenőrizze, hogy a projektazonosító és -kulcs helyes-e, és győződjön meg róla, hogy a végpont olvasható.                                       | Próbálja megismételni a műveletet. Ha nem szűnik meg a probléma, forduljon a Microsoft támogatási szolgálatához.                                                                     |
