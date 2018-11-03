---
title: Diagnosztika beállítása az Azure Cloud Services és virtual machines |} A Microsoft Docs
description: Ismerje meg, hogyan állítsa be a diagnosztika hibakeresés cloude az Azure-szolgáltatások és virtuális gépek (VM) a Visual Studióban.
services: visual-studio-online
documentationcenter: na
author: mikejo
manager: douge
editor: ''
ms.assetid: e70cd7b4-6298-43aa-adea-6fd618414c26
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/28/2018
ms.author: mikejo
ms.openlocfilehash: 8f32165a7a54ead06d57a3d8d1b4282498dca635
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969614"
---
# <a name="set-up-diagnostics-for-azure-cloud-services-and-virtual-machines"></a>Az Azure Cloud Services és virtual machines diagnosztika beállítása
Amikor szüksége van egy Azure-felhőszolgáltatás vagy virtuális gépen hibaelhárítása, a Visual Studio segítségével könnyebben Azure Diagnostics beállítása. Diagnosztika a rendszer és a virtuális gépek és a virtual machine instances ajánlat, amely a felhőszolgáltatás naplózási adatait rögzíti. Diagnosztikai adatok átkerülnek egy Ön által választott tárfiókot. További információ a diagnosztikai naplózás az Azure-ban, lásd: [az Azure App Service Web Apps-alkalmazások diagnosztikai célú naplózásának engedélyezése](app-service/web-sites-enable-diagnostic-log.md).

Ebben a cikkben bemutatjuk, be-és beállítása az Azure Diagnostics, előtt és üzembe helyezés után a Visual Studio használatával. Ismerje meg, diagnosztika beállítása az Azure-beli virtuális gépeken, hogyan válassza ki azokat a diagnosztikai adatok összegyűjtése és megtekintése az adatokat a begyűjtésük után.

Azure Diagnostics beállításához használhatja az alábbi lehetőségek közül:

* A diagnosztikai beállításainak módosítása a **diagnosztikai konfigurációja** párbeszédpanel a Visual Studióban. A beállítások diagnostics.wadcfgx (az Azure SDK 2.4-os vagy korábbi, a fájl neve diagnostics.wadcfg) nevű fájlba vannak mentve. Akkor is közvetlenül módosíthatja a konfigurációs fájlban. Ha manuálisan frissíti a fájlt, a konfigurációs módosítások érvénybe a következő alkalommal, amikor a-felhő üzembe az Azure-szolgáltatás, vagy a szolgáltatás futtatása az emulátorral.
* Cloud Explorer használata vagy a Visual Studio Server Explorer egy felhőszolgáltatás vagy futtató virtuális gép diagnosztikai beállításainak módosításához.

## <a name="azure-sdk-26-diagnostics-changes"></a>Az Azure SDK 2.6-os diagnosztikai módosítások
A következő módosításokat alkalmazni az Azure SDK 2.6-os és újabb projekteket a Visual Studióban:

* A helyi emulátor mostantól támogatja a diagnosztikát. Ez azt jelenti, hogy diagnosztikai adatok gyűjtésére, és győződjön meg arról, hogy az alkalmazás hoz létre a megfelelő nyomkövetések fejlesztése és tesztelése a Visual Studióban. A kapcsolati karakterlánc `UseDevelopmentStorage=true` diagnosztikai adatok gyűjtésének bekapcsolása a futtatása során a felhőszolgáltatási projektet a Visual Studióban az Azure storage emulator használatával. Az összes diagnosztikai adatgyűjtés fejlesztési tárterülettel storage-fiókban.
* A diagnosztikai tárfiók kapcsolati sztringje `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` a szolgáltatás konfigurációs (.cscfg) fájljában tárolt. Az Azure SDK 2.5-ös a diagnosztikai tárfiók van megadva a diagnostics.wadcfgx fájlban.

A kapcsolati karakterláncot a néhány főbb megoldásokkal az Azure SDK 2.6-os, és később és Azure SDK 2.4 és korábbi eltérően működik:

* Azure SDK 2.4 és korábbi a kapcsolati karakterlánc segítségével egy modult, a beépülő modul diagnosztikai átvitelére a diagnosztikai naplók tárfiókadatok beolvasása.
* Azure SDK 2.6-os vagy újabb a Visual Studio diagnosztikai kapcsolati karakterláncát használja állíthatja be az Azure diagnosztikai bővítmény közzététele során a megfelelő tárolási fiók adataival. A kapcsolati karakterlánc segítségével határozza meg, amely közzététele során használja a Visual Studio különböző szolgáltatáskonfiguráció különböző storage-fiókot. Azonban a diagnosztikai bővítmény nem érhető el az Azure SDK 2.5-ös után, mert a .cscfg fájlban önmagában nem állítható be a diagnosztikai bővítményt. Be kell állítania a bővítmény külön-külön eszközökkel, mint például a Visual Studio vagy a PowerShell használatával.
* Egyszerűsítése érdekében a PowerShell használatával a diagnosztikai bővítmény beállításának folyamatán, a Visual Studio csomag kimenete tartalmazza a nyilvános konfigurációs XML-t a diagnosztikai bővítményt, az egyes szerepkörökhöz. A Visual Studio feltöltése a storage-fiók adatait a nyilvános konfigurációban diagnosztikai kapcsolati karakterláncát használja. A nyilvános konfigurációs fájlok jönnek létre a bővítmények mappában. A nyilvános konfigurációs fájlok PaaSDiagnostics elnevezési mintát használja. &lt;szerepkörnév\>. PubConfig.xml. A PowerShell-alapú telepítések Ez a minta használatával minden egyes konfiguráció leképezése egy szerepkört.
* A [az Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) kapcsolati karakterláncát használja a .cscfg fájlban a diagnosztikai adatokhoz való hozzáféréshez. Az adatok jelennek meg a **figyelés** fülre. A kapcsolati karakterlánc szükséges részletes monitorozási adatok a portálon megjeleníteni kívánt szolgáltatást.

## <a name="migrate-projects-to-azure-sdk-26-and-later"></a>Azure SDK 2.6-os vagy újabb projekteket
Azure SDK 2.5-ös az Azure SDK 2.6-os vagy újabb, ha a .wadcfgx fájlban megadott diagnosztikai tárfiók telepít át, ha a tárfiók fájl marad. A rugalmas különböző storage-fiókot használni a különböző tárolási konfigurációk előnyeit, manuálisan adja hozzá a kapcsolati karakterláncot a projekthez. Ha a projekt migráláshoz az Azure SDK 2.4 vagy korábban az Azure SDK 2.6-os, a diagnosztikai kapcsolati karakterláncok megmaradnak. Azonban vegye figyelembe a kapcsolati karakterláncok hogyan kezeli az Azure SDK 2.6-os, az előző szakaszban leírt változásai.

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Hogyan határozza meg a Visual Studióban a diagnosztikai tárfiók
* Ha egy diagnosztikai kapcsolati karakterláncot a .cscfg fájlban van megadva, a Visual Studio használja közzététele során, és a nyilvános konfigurációs XML-fájlok során csomagolási állít elő a diagnosztikai bővítmény beállítása.
* Ha egy diagnosztikai kapcsolati karakterláncot a .cscfg fájlban nincs megadva, a Visual Studio visszaáll a tárfiókot a diagnosztikai bővítményt a közzétételre és létrehozásához a nyilvános konfigurációjának XML beállításához .wadcfgx fájlban megadott fájlok csomagolási során.
* A diagnosztika kapcsolati karakterláncot a .cscfg fájlban élvez a .wadcfgx fájlban a tárfiók. Ha egy diagnosztikai a kapcsolati karakterlánc a .cscfg fájlban megadott Visual Studio a kapcsolati karakterláncot használja, és figyelmen kívül hagyja a storage-fiókban lévő .wadcfgx.

### <a name="what-does-the-update-development-storage-connection-strings-check-box-do"></a>Mit jelent a "Fejlesztés storage kapcsolati karakterláncok... frissítése" jelölőnégyzet?
A **fejlesztési storage kapcsolati karakterláncok frissítése diagnosztika és a gyorsítótárazás Microsoft Azure storage-fiók hitelesítő adataival a Microsoft Azure közzététel során** jelölőnégyzet be kényelmesen minden fejlesztési tárterület frissítése fiók kapcsolati karakterláncot közzététele során megadott Azure storage-fiókban.

Például ha bejelöli ezt a jelölőnégyzetet, és a diagnosztika kapcsolati karakterláncot szolgáltat `UseDevelopmentStorage=true`, amikor az Azure-bA teszi közzé a projektet a Visual Studio automatikusan frissíti a diagnosztikai kapcsolati karakterláncot a megadott tárfiók a közzétételi varázsló. Azonban ha egy valódi tárfiók van megadva, a diagnosztikai kapcsolati karakterláncot, a fiókot használja.

## <a name="diagnostics-functionality-differences-in-azure-sdk-24-and-earlier-vs-azure-sdk-25-and-later"></a>Diagnosztikai funkciók eltérései Azure SDK 2.4 és a korábbi vs. Az Azure SDK 2.5-ös és újabb verziók
Ha, amelyre a projekt az Azure SDK 2.4 és a korábban az Azure SDK 2.5-ös vagy újabb, vegye figyelembe az alábbi diagnosztikai funkciót eltérések:

* **Konfigurációs API-jai elavultak**. Diagnosztikai programozható konfigurációja elérhető az Azure SDK 2.4 és korábbi verziók, de az Azure SDK 2.5-ös és újabb elavult. A diagnosztikai konfigurációja jelenleg van definiálva a kódban, ha ezeket a beállításokat és a diagnosztikai áttelepített projekt teljesen a munka folytatásához újra kell konfigurálnia. A diagnosztika konfigurációs fájlt az Azure SDK 2.4 diagnostics.wadcfg. A diagnosztika konfigurációs fájlt az Azure SDK 2.5-ös és újabb verziók diagnostics.wadcfgx.
* **Cloud service-alkalmazások diagnosztikai adatait csak a szerepkör szinten konfigurálható**. Az Azure SDK 2.5-ös és újabb, nem állítható be a cloud service-alkalmazások diagnosztikai a példány szintjén.
* **Minden alkalommal, amikor telepíti az alkalmazását, a diagnosztikai konfigurációja frissül**. Funkcióparitási problémák léphetnek föl, ha a Visual Studio Server Explorer a diagnostics konfigurációjának módosítása, és ezt követően telepítse újra az alkalmazást.
* **Az Azure SDK 2.5-ös és újabb összeomlási memóriaképek vannak konfigurálva, a diagnosztikai konfigurációs fájlban, és nem a kód**. Ha a code-ban konfigurált összeomlási memóriaképek, manuálisan kell áthelyezni a konfiguráció code-ból a konfigurációs fájl. Összeomlási memóriaképek Azure SDK 2.6-os az áttelepítés során nem kerülnek.

## <a name="turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them"></a>Cloud service-projektek diagnosztika bekapcsolása a központi telepítésük előtt
A Visual Studióban is összegyűjtheti a diagnosztikai adatokat a szolgáltatás telepítése előtt az emulátorban való futtatásakor az Azure-ban futó szerepköröket. A Visual Studióban diagnosztikai beállításainak összes módosítását a diagnostics.wadcfgx konfigurációs fájlban menti. Ezek a beállítások megadják a tárfiókot, ahol diagnosztikai adatok mentése a felhőalapú szolgáltatások üzembe.

[!INCLUDE [cloud-services-wad-warning](../includes/cloud-services-wad-warning.md)]

### <a name="to-turn-on-diagnostics-in-visual-studio-before-deployment"></a>A telepítés előtt a Visual Studióban diagnosztika bekapcsolása

1. Válassza ki a helyi menüben a szerepkörhöz, **tulajdonságok**. A szerepkör **tulajdonságok** párbeszédpanelen válassza ki a **konfigurációs** fülre.
2. Az a **diagnosztikai** területén ellenőrizze, hogy a **diagnosztika engedélyezése** jelölőnégyzet be van jelölve.
   
    ![A diagnosztika engedélyezése beállítás eléréséhez](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796660.png)
3. Adja meg a tárfiókot a diagnosztikai adatok, válassza a három pontra (...) gombra.
   
    ![Adja meg a storage-fiók használata](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796661.png)
4. Az a **tárolási kapcsolati karakterlánc létrehozása** párbeszédpanelen adja meg, hogy szeretne összekapcsolni az Azure storage emulator, az Azure-előfizetés használatával, vagy manuálisan kell megadni a hitelesítő adatok.
   
    ![Storage-fiók párbeszédpanel](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796662.png)
   
   * Ha **a Microsoft Azure storage emulator**, a kapcsolati karakterlánc beállítása `UseDevelopmentStorage=true`.
   * Ha **az előfizetés**, válassza ki a használni kívánt Azure-előfizetést, és adjon meg egy fióknevet. Az Azure-előfizetések kezeléséhez, jelölje be a **fiókok kezelése**.
   * Ha **manuálisan kell megadni a hitelesítő adatok**, adja meg a nevét és kulcsát az Azure-fiók, amelyet használni szeretne.
5. Megtekintéséhez a **diagnosztikai konfigurációja** párbeszédpanelen jelölje ki **konfigurálása**. Az alábbiakat kivéve **általános** és **Naplókönyvtárak**, egyes lapokon is összegyűjtheti a diagnosztikai adatforrás jelöli. Az alapértelmezett **általános** lapon kínál a következő diagnosztikai adatgyűjtési beállítások: **csak a hibák**, **kapcsolatos összes információ**, és **egyéni terv**. Az alapértelmezett **csak a hibák** beállítás tárhely, a lehető legkevesebb használja, mert, figyelmeztetés vagy a nyomkövetési üzeneteket nem vesz át. A **kapcsolatos összes információ** lehetőség a legtöbb információt továbbítja, a legtöbb tárolást használ és ezért a legköltségesebb lehetőséget.

   > [!NOTE]
   > Minimális támogatott "Kvóta az MB. lemez" mérete 4GB. Azonban már gyűjti a memóriaképeket, ha növeli a például a 10 GB-os értéke.
   >
  
    ![Az Azure diagnostics- és konfigurációs engedélyezése](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
6. Ebben a példában válassza ki a **egyéni terv** beállítás, így testre szabhatja az összegyűjtött adatokat.
7. Az a **Disková kvóta v MB** mezőbe, beállíthatja a diagnosztikai adatok tárfiókja mekkora elfér. Módosíthatja, vagy fogadja el az alapértelmezett értéket.
8. A diagnosztikai adatok gyűjtése kívánt minden egyes lapon válassza ki a **átvitelének engedélyezése \<napló típusa\>**  jelölőnégyzetet. Például, ha az alkalmazásnaplókat, gyűjtendő a **Alkalmazásnaplókat** lapon jelölje be a **Alkalmazásnaplókat átvitelét engedélyezése** jelölőnégyzetet. Emellett adja meg minden egyéb szükséges információkat, minden egyes diagnosztikai adatok típusa szerint. Az egyes lapokon konfigurációs információkért lásd: a szakasz **állítsa be a diagnosztikai adatforrások** a cikk későbbi részében. 
9. Miután engedélyezte a kívánt diagnosztikai adatok gyűjtését, válassza ki a **OK**.
10. A szokásos módon futtassa az Azure felhőszolgáltatás-projekt a Visual Studióban. Az alkalmazás használata során, a naplózási adatok engedélyezett menti a megadott Azure storage-fiókban.

## <a name="turn-on-diagnostics-on-azure-virtual-machines"></a>Engedélyezze a diagnosztikát az Azure-beli virtuális gépeken
A Visual Studióban is összegyűjtheti a diagnosztikai adatok Azure-beli virtuális gépek.

### <a name="to-turn-on-diagnostics-on-azure-virtual-machines"></a>Az Azure-beli virtuális gépeken diagnosztika bekapcsolása

1. A Server Explorerben válassza ki az Azure-csomópont, és kapcsolódjon az Azure-előfizetéssel, ha Ön még nem kapcsolódott.
2. Bontsa ki a **virtuális gépek** csomópont. Hozzon létre egy új virtuális gépet, vagy válasszon ki egy meglévő csomópontot.
3. Válassza ki a kívánt virtuális gép a helyi menüben **konfigurálása**. A virtuális gép konfigurációs párbeszédpanel jelenik meg.
   
    ![Az Azure virtuális gép konfigurálása](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796663.png)
4. Ha még nem telepítette, adja hozzá a Microsoft Monitoring Agent diagnosztikai bővítményt. Ez a bővítmény az Azure-beli virtuális gép diagnosztikai adatokat gyűjthet. A **telepített bővítmények**, a a **jelöljön ki egy rendelkezésre álló bővítményt** legördülő mezőben válasszon **a Microsoft Monitoring Agent diagnosztikai**.
   
    ![Egy Azure-beli virtuálisgép-bővítmény telepítése](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766024.png)
   
    > [!NOTE]
   > Egyéb diagnosztikai bővítményei a virtuális gépek számára érhetők el. További információkért lásd: [virtuálisgép-bővítmények és szolgáltatások Windows](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features).
   > 
   > 
5. A bővítmény és a nézet hozzáadása a **diagnosztikai konfigurációja** párbeszédpanelen jelölje ki **Hozzáadás**.
6. Storage-fiók megadásához válassza **konfigurálása**, majd válassza ki **OK**.
   
    Minden lap (kivéve a **általános** és **Naplókönyvtárak**) is összegyűjtheti a diagnosztikai adatforrást jelöl.
   
    ![Az Azure diagnostics- és konfigurációs engedélyezése](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
   
    Az alapértelmezett lapon **általános**, a következő diagnosztikai adatgyűjtési beállítások kínál: **csak a hibák**, **kapcsolatos összes információ**, és **egyéni terv**. Az alapértelmezett beállítás, **csak a hibák**, a lehető legkevesebb tárolási vesz igénybe, mivel azt nem transfer, figyelmeztetés vagy a nyomkövetési üzeneteket. A **kapcsolatos összes információ** lehetőség a legtöbb információt továbbítja, és ezért, a tárolás tekintetében legköltségesebb lehetőséget.
7. Ebben a példában válassza ki a **egyéni terv** lehetőség, így testre szabhatja az adatokat gyűjteni.
8. A **Disková kvóta v MB** mezőben megadhatja, mekkora terület szeretne lefoglalni a tárfiókot a diagnosztikai adatok. Ha azt szeretné, módosíthatja az alapértelmezett érték.
9. A diagnosztikai adatok gyűjtendő minden lapon válassza ki a **átvitelének engedélyezése \<napló típusa\>**  jelölőnégyzetet.
   
    Ha szeretne gyűjteni az alkalmazásnaplókat, jelölje be például a **Alkalmazásnaplókat átvitelét engedélyezése** jelölőnégyzet be van jelölve a **Alkalmazásnaplókat** lapon. Is adja meg az egyes diagnosztikai adattípusokhoz szükséges egyéb információkat. Az egyes lapokon konfigurációs információkért lásd: a szakasz **állítsa be a diagnosztikai adatforrások** a cikk későbbi részében.
10. Miután engedélyezte az összes kívánt diagnosztikai adatok gyűjtését, válassza ki a **OK**.
11. Mentse a frissített projektet.
    
    Egy üzenet a **a Microsoft Azure-tevékenységnapló** ablak azt jelzi, hogy a virtuális gép frissítve lett.

## <a name="set-up-diagnostics-data-sources"></a>Adatforrások diagnosztika beállítása
Miután engedélyezte a diagnosztikai adatok gyűjtése, pontosan mely adatforrások szeretne gyűjteni, és gyűjtött információk köréről választhat. A következő szakaszok ismertetik a lapok a **diagnosztikai konfigurációja** párbeszédpanel, és minden egyes konfigurációs beállítás azt jelenti, hogy.

### <a name="application-logs"></a>Alkalmazásnaplók
Alkalmazásnaplók a diagnosztikai adatokat, egy webalkalmazás által előállított rendelkezik. Ha azt szeretné, rögzítheti az alkalmazásnaplókat, válassza a **Alkalmazásnaplókat átvitelét engedélyezése** jelölőnégyzetet. Növelni vagy csökkenteni a storage-fiókjában az alkalmazásnaplókat átvitelét közötti időtartam, módosítsa a **átviteli időszak (perc)** értéket. Állítsa a naplóban rögzített adatok mennyiségétől is módosíthatja a **naplózási szintjének** értéket. Válassza ki például **részletes** részletes tájékoztatást, vagy válasszon **kritikus** csak kritikus hibák rögzítésére. Ha egy alkalmazásspecifikus diagnosztikák szolgáltató, amely az alkalmazásnaplókat bocsát ki, adja hozzá a szolgáltató GUID Azonosítóját a naplók rögzítése a **szolgáltatói GUID** mezőbe.

  ![Alkalmazásnaplók](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758145.png)

Protokoly aplikací kapcsolatos további információkért lásd: [az Azure App Service Web Apps-alkalmazások diagnosztikai célú naplózásának engedélyezése](app-service/web-sites-enable-diagnostic-log.md).

### <a name="windows-event-logs"></a>Windows-eseménynaplók,
Rögzíti a Windows-eseménynaplók, jelölje be a **Windows eseménynaplók átvitel engedélyezése** jelölőnégyzetet. Növelni vagy csökkenteni a storage-fiókjában az eseménynaplókat átvitelét közötti időtartam, módosítsa a **átviteli időszak (perc)** értéket. Jelölje be a követni kívánt eseménytípusokról jelölőnégyzeteit.

![Eseménynaplók](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796664.png)

Ha az Azure SDK 2.6-os vagy újabb verzióját használja, és szeretne megadni egy egyéni adatforrás, írja be a a **\<adatforrás neve\>** szövegmezőbe, és válassza ki **Hozzáadás**. Az adatforrás a diagnostics.cfcfg fájl kerül.

Ha az Azure SDK 2.5-ös használja, és adjon meg egy egyéni adatforrás szeretne, hozzáadhat, hogy a `WindowsEventLog` a diagnostics.wadcfgx szakaszában fájlba, például az alábbi példában:

```
<WindowsEventLog scheduledTransferPeriod="PT1M">
   <DataSource name="Application!*" />
   <DataSource name="CustomDataSource!*" />
</WindowsEventLog>
```
### <a name="performance-counters"></a>Teljesítményszámlálók
Teljesítményszámláló adatai segítségével keresse meg a rendszer szűk keresztmetszetei, emellett rendszer és az alkalmazások teljesítményének finomhangolását. További információkért lásd: [teljesítményszámlálók létrehozása és használata az Azure-alkalmazások](https://msdn.microsoft.com/library/azure/hh411542.aspx). Teljesítményszámlálók rögzítése, válassza ki a **teljesítményszámlálók átvitel engedélyezése** jelölőnégyzetet. Növelni vagy csökkenteni a storage-fiókjában az eseménynaplókat átvitelét közötti időtartam, módosítsa a **átviteli időszak (perc)** értéket. Jelölje be a követni kívánt teljesítményszámlálók jelölőnégyzetéből.

![Teljesítményszámlálók](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758147.png)

Listán nem szereplő teljesítményszámláló nyomon követéséhez írja be a teljesítményszámláló a javasolt szintaxis használatával. majd **Hozzáadás**. A virtuális gépen az operációs rendszer meghatározza, hogy mely teljesítményszámlálókat követheti nyomon. A szintaxissal kapcsolatos további információkért lásd: [adja meg a számláló elérési útját](https://msdn.microsoft.com/library/windows/desktop/aa373193.aspx).

### <a name="infrastructure-logs"></a>Protokoly infrastruktury
Protokoly infrastruktury rendelkezik az Azure diagnosztikai infrastruktúra, a RemoteAccess modul és a RemoteForwarder modult. Protokoly infrastruktury adatokat gyűjthet, válassza ki a **infrastruktúra naplók átvitelét engedélyezése** jelölőnégyzetet. Növelni vagy csökkenteni a infrastruktúra naplók átvitelét a storage-fiók közötti időtartam, módosítsa a **átviteli időszak (perc)** értéket.

![Diagnosztikai infrastruktúra naplói](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758148.png)

További információkért lásd: [naplózási adatok gyűjtése az Azure Diagnostics használatával](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### <a name="log-directories"></a>Naplókönyvtárak
Naplókönyvtárat kell naplókönyvtárak-Internet Information Services (IIS) kérések, meghiúsult kérések vagy mappák Ön által választott begyűjtött adatokat. Naplókönyvtárak rögzítéséhez jelölje ki a **Naplókönyvtárak átvitelét engedélyezése** jelölőnégyzetet. Növelni vagy csökkenteni a naplók átvitelét a storage-fiók közötti időtartam, módosítsa a **átviteli időszak (perc)** értéket.

Jelölje be a jelölőnégyzeteket, amelyet szeretne gyűjteni, mint például a naplófájlok **IIS-naplók** és **sikertelen kéréseket** naplókat. Alapértelmezett tároló a tároló nevének vannak megadva, de a nevét módosíthatja.

Minden mappa naplóinak rögzítheti. Adja meg az elérési utat a **naplózási könyvtár abszolút a** szakaszt, és válassza ki **könyvtár hozzáadása**. A naplók a megadott tárolókban lesznek rögzítve.

![Naplókönyvtárak](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796665.png)

### <a name="etw-logs"></a>ETW-naplók
Használatakor [esemény nyomkövetése for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803\(v=vs.85\).aspx) (ETW), és rögzítheti az ETW-naplók, válassza ki a **ETW-naplók átvitelét engedélyezése** jelölőnégyzetet. Növelni vagy csökkenteni a naplók átvitelét a storage-fiók közötti időtartam, módosítsa a **átviteli időszak (perc)** értéket.

Az események a megadott eseményforrásokból és az Ön által megadott eseményjegyzékfájlok lesznek rögzítve. Ha meg szeretné adni az eseményforrás a **eseményforrások** című. Adjon meg egy nevet, majd válassza ki **eseményforrás hozzáadása**. Ehhez hasonlóan megadhat egy eseményjegyzékfájl a a **Eseményjegyzékfájlok** szakaszt, és válassza ki **esemény Manifest hozzáadása**.

![ETW-naplók](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766025.png)

Az ETW-keretrendszer az ASP.NET támogatta az osztályok a [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) névtér. A Microsoft.WindowsAzure.Diagnostics névteret, amely örökli, és kiterjesztheti a standard [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) osztályokat, lehetővé teszi a [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) , a naplózás Azure-beli keretrendszer. További információkért lásd: [irányításuk alá vonják a naplózás és nyomkövetés a Microsoft Azure-ban](https://msdn.microsoft.com/magazine/ff714589.aspx) és [diagnosztika engedélyezése az Azure Cloud Services és virtual machines](cloud-services/cloud-services-dotnet-diagnostics.md).

### <a name="crash-dumps"></a>összeomlási memóriaképek,
Ha egy szerepkörpéldány összeomlik vonatkozó információk rögzítéséhez, válassza ki a **engedélyezése az összeomlási memóriaképek átvitelét** jelölőnégyzetet. (Az ASP.NET kezeli a legtöbb kivételeket, mert ez a csak a feldolgozói szerepkörök számára általában hasznos.) Növelni vagy csökkenteni a tárolóhely számára az összeomlási memóriaképek aránya, módosítsa a **(%) kvóta** értéket. Módosíthatja a storage-tárolót, ha az összeomlási memóriaképek tárolják, és válassza ki, hogy rögzíteni szeretné a **teljes** vagy **Mini** memóriakép.

A következő képernyőkép a jelenleg követett folyamatok listáját. Válassza ki a rögzíteni kívánt folyamatokat jelölőnégyzetéből. Egy másik folyamat hozzáadása a listához, adja meg a folyamat nevét, majd **hozzáadása folyamat**.

![összeomlási memóriaképek,](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766026.png)

További információkért lásd: [irányításuk alá vonják a naplózás és nyomkövetés a Microsoft Azure-ban](https://msdn.microsoft.com/magazine/ff714589.aspx) és [4. a Microsoft Azure Diagnostics rész: egyéni naplózási összetevők és az Azure Diagnostics 1.3 módosítások](http://justazure.com/microsoft-azure-diagnostics-part-4-custom-logging-components-azure-diagnostics-1-3-changes/).

## <a name="view-the-diagnostics-data"></a>A diagnosztikai adatok megtekintése
Egy felhőszolgáltatás vagy virtuális gép a diagnosztikai adatok begyűjtését követően megtekintheti.

### <a name="to-view-cloud-service-diagnostics-data"></a>Cloud service diagnosztikai adatainak megtekintése
1. A felhőszolgáltatások, mint a szokásos központi telepítése, és futtassa azt.
2. Megtekintheti a diagnosztikai adatok vagy a Visual Studio által létrehozott jelentésekben, vagy a táblák a storage-fiókban. Egy jelentésben, nyissa meg a Cloud Explorer vagy a Server Explorerben nyissa meg a csomópont a szerepkörhöz, és válassza ki a helyi menü nézet **diagnosztikai adatok megtekintése**.
   
    ![Zobrazit diagnostická data](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748912.png)
   
    Megjelenik az elérhető adatokat tartalmazó jelentést.
   
    ![A Microsoft Azure Diagnostics-jelentés a Visual Studióban](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796666.png)
   
    Ha a legfrissebb adatok nem jelenik meg, hogy előfordulhat, hogy várja meg, az adatátviteli időszak leteltét.
   
    Az adatok azonnali frissítéséhez válassza ki a **frissítése** hivatkozásra. Az adatok automatikusan frissülnek, jelölje be az időközt a **automatikus frissítésének** legördülő listában. Szeretné exportálni a hibaadatokat, válassza ki a **Exportálás CSV-FÁJLBA** gombra kattintva hozzon létre egy vesszővel tagolt fájlt, amely egy Excel-munkalapot is megnyithatja.
   
    A Cloud Explorer vagy a Server Explorerben nyissa meg az üzembe helyezés társított storage-fiókot.
3. Nyissa meg a diagnosztikai táblákat a tábla viewer, és tekintse át az összegyűjtött adatokat. Az IIS-naplók és egyéni naplókra nyisson meg egy blob-tárolóba. A következő táblázat felsorolja a táblák vagy a blob-tárolók, amelyek tartalmazzák az adatokat a különböző naplófájlokat. Amellett, hogy a naplófájl adatai, a táblabejegyzéseket tartalmazhat **EventTickCount**, **DeploymentId**, **szerepkör**, és **RoleInstance** , segít azonosítani, hogy mely virtuális gépek és a szerepkör jön létre az adatokat, és mikor. 
   
   | Diagnosztikai adatok | Leírás | Hely |
   | --- | --- | --- |
   | Alkalmazásnaplók |A kód a metódusok meghívásával által létrehozott naplók a **System.Diagnostics.Trace** osztály. |WADLogsTable |
   | Eseménynaplók |A Windows-eseménynaplók, a virtuális gépek adatait. Windows információkat tárolja ezeket a naplókat, de alkalmazásokat és szolgáltatásokat is hibák jelentéséhez a naplók és naplózza az információkat. |WADWindowsEventLogsTable |
   | Teljesítményszámlálók |A bármilyen teljesítményszámláló, amely érhető el a virtuális gépen is lekérdezhet adatokat. Az operációs rendszer biztosít teljesítményszámlálókat, többek között számos statisztika, például a memória használatát, és a processzor idő. |WADPerformanceCountersTable |
   | Protokoly infrastruktury |A diagnosztikai infrastruktúra magát az előállított naplók. |WADDiagnosticInfrastructureLogsTable |
   | IIS-naplók |Rögzítő webes kéréseket. Ha a felhőszolgáltatás mellé jelentős mennyiségű forgalmat, ezek a naplók megnőhet. Jó ötlet gyűjti össze és tárolja az adatokat csak akkor, amikor szüksége. |Sikertelen kérelem naplózza a wad-az iis-failedreqlogs, mellett egy elérési utat a telepítés, a szerepkör és a példány a blobtárolóban található. Teljes körű naplók wad-az iis-naplófájlok alatt találja. Az összes fájl esetében tételek a WADDirectories táblában. |
   | összeomlási memóriaképek, |A cloud Services-folyamat (általában egy feldolgozói szerepkör) bináris lemezképet biztosít. |WAD-crush-memóriaképek blob-tároló |
   | Egyéni naplófájlok |Naplók, az előre meghatározott adatok. |Megadhatja a kódban az egyéni napló fájlokat a storage-fiókban. Ha például egy egyéni blob-tárolóba is megadhat. |
4. Bármilyen típusú adatokat a rendszer csonkolja, megpróbálhatja az adatokat a puffer növelése típus vagy lecsökkentik között átvitt adatok a virtuális gép a tárfiókhoz.
5. (Nem kötelező) Adatok törlése a tárfiókból, időnként csökkentheti tárolási költségeit.
6. Amikor ezt teszi, hogy a teljes körű telepítésére, az Azure-ban frissült a diagnostics.cscfg fájlt (az Azure SDK 2.5-ös .wadcfgx), és a felhőszolgáltatások szerzi be a diagnosztikai konfigurációs módosításait. Ha ehelyett frissít egy meglévő üzemelő példányt, a .cscfg-fájlt az Azure-ban nem frissül. Diagnosztikai beállítások, azonban továbbra is módosíthatja a következő rész lépéseit követve. Hajt végre teljes körű telepítésére és a meglévő környezet frissítésével kapcsolatos további információkért lásd: [Azure alkalmazás közzététele varázsló](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-view-virtual-machine-diagnostics-data"></a>Virtuális gép diagnosztikai adatainak megtekintése
1. A helyi menüben a virtuális gép, jelölje be a **Zobrazit Diagnostická Data**.
   
    ![Diagnosztikai adatok megtekintése az Azure virtuális gépként](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766027.png)
   
    A **souhrn Diagnostiky** párbeszédpanel jelenik meg.
   
    ![Souhrn diagnostiky Azure virtuális gépen](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796667.png)  
   
    Ha a legfrissebb adatok nem jelenik meg, hogy előfordulhat, hogy várja meg, az adatátviteli időszak leteltét.
   
    Az adatok azonnali frissítéséhez válassza ki a **frissítése** hivatkozásra. Az adatok automatikusan frissülnek, jelölje be az időközt a **automatikus frissítésének** legördülő listában. Szeretné exportálni a hibaadatokat, válassza ki a **Exportálás CSV-FÁJLBA** gombra kattintva hozzon létre egy vesszővel tagolt fájlt, amely egy Excel-munkalapot is megnyithatja.

## <a name="set-up-cloud-service-diagnostics-after-deployment"></a>Felhőszolgáltatások diagnosztikájának beállításához az üzembe helyezés után
Egy felhőszolgáltatás, amely már fut a probléma lekérdezéskapcsolatokról, előfordulhat, hogy szeretné-e nem adott meg adatok gyűjtésére, eredetileg a szerepkör telepítése előtt. Ebben az esetben elindíthatja az adatok gyűjtése a Server Explorerben a beállítások módosításával. Diagnosztikai vagy egyetlen példányra, vagy egy szerepkör, attól függően, hogy megnyitja az összes példányát beállíthatja a **diagnosztikai konfigurációja** párbeszédpanelen vagy a szerepkör-példány a helyi menüből. Konfigurálja a szerepkör-csomópont, ha módosításokat, hogy az összes példány vonatkoznak. Ha a példány csomópont konfigurál, minden elvégzett módosítások csak az annak a példánynak érvényesek.

### <a name="to-set-up-diagnostics-for-a-running-cloud-service"></a>A futó felhőszolgáltatás diagnosztika beállítása
1. A Server Explorerben bontsa ki a **Cloud Services** csomópontot, majd bontsa ki a szerepkör vagy a példány (vagy mindkettő) található csomópontok listájában, amely a vizsgálni kívánt.
   
    ![Diagnosztika konfigurálása](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748913.png)
2. Az egy példány vagy szerepkör csomópontot a helyi menüben válassza **diagnosztikai beállítások**, majd válassza ki a diagnosztikai beállításokat szeretne gyűjteni.
   
    A konfigurációs beállításaival kapcsolatos további információkért lásd: a szakasz **állítsa be a diagnosztikai adatforrások** ebben a cikkben. A diagnosztikai adatok megtekintésére. További információkért lásd: a szakasz **diagnosztikai adatok megtekintéséhez** ebben a cikkben.
   
    Ha módosítja az adatgyűjtést a Kiszolgálókezelőben, a módosítások a felmondásáig marad teljes telepítse újra a felhőalapú szolgáltatást. Ha az alapértelmezett közzétételi beállítások, a módosítások nem lettek felülírva. Az alapértelmezett beállítás közzététele frissítésére a meglévő üzemelő példányt, nem pedig egy teljes újbóli üzembe helyezés ehhez. Győződjön meg arról, hogy a beállítások központi telepítéskor törölje-e, nyissa meg a **speciális beállítások** a közzétételi varázsló lapra, és törölje a jelet a **aktualizace Nasazení** jelölőnégyzetet. A jelölőnégyzet nincs bejelölve az újbóli telepítése során a beállítások vissza azokat a .wadcfgx (vagy .wadcfg) fájlban beállított keresztül a **tulajdonságok** szerkesztő a szerepkörhöz. Ha frissíti az üzemelő példány, Azure megőrzi a korábbi beállításokat.

## <a name="troubleshoot-azure-cloud-service-issues"></a>Hibaelhárítás Azure-felhőszolgáltatások problémái
Ha problémákat tapasztal a cloud service-projektek, mint egy szerepkör, amely a "forgalmas" állapot, elakad többször újraindul, vagy egy belső kiszolgálóhiba jelez, nincsenek eszközöket és technikákat, amelyekkel diagnosztizálhatja és megoldhatja a problémát. Adott példák a gyakori problémákat és megoldásokat, és áttekintheti a fogalmakat és eszközöket, amelyek segítségével diagnosztizálhatja és megoldhatja ezeket a hibákat, lásd: [Azure PaaS számítási diagnosztikai adatok](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

## <a name="q--a"></a>Kérdések és válaszok
**Mi az a puffer méretét, és milyen méretű legyen?**

Az egyes virtuálisgép-példányokon kvóták korlátozzák, mennyi diagnosztikai adatok tárolhatók a helyi fájlrendszerben. Emellett adja meg a puffer mérete az egyes elérhető diagnosztikai adatokat. A puffer mérete úgy viselkedik, mint egy egyéni kvóta az adott típusú adatot. Annak megállapításához, a teljes kvóta- és a fennmaradó memóriamennyiség, tekintse meg a diagnosztikai adatok típusa a párbeszédpanel alján. Ha nagyobb puffert, vagy további típusú adatokat ad meg, módszerrel érdemes lesz a teljes kvótájába. A diagnostics.wadcfg vagy .wadcfgx konfigurációs fájl módosításával módosíthatja a teljes kvótájába. A diagnosztikai adatok az azonos fájlrendszereket, mint az alkalmazás adatokat tárolja. Ha az alkalmazás nagy mennyiségű lemezterületet használja, nem növeli a celková kvóta diagnostiky.

**Mi az átvitel időtartama, és mennyi ideig legyen?**

A adatátviteli idő pedig az, hogy mennyi ideig eltelte közötti adatokat rögzíti. Minden egyes átviteli az időtartamnak az elteltével adatok került a helyi fájlrendszer egy virtuális gépen a tárfiókban lévő táblák. Összegyűjtött adatok mennyisége meghaladja a kvótát egy átviteli időszak vége előtt, ha a rendszer régebbi adatokat elveti. Ha az adatok elvesztése van, mert az adatok meghaladják a puffer mérete vagy a teljes kvótájába, érdemes csökkentheti az adatátviteli időszak.

**Milyen időzóna az időbélyegek a rendszer?**

Időbélyegeket találhatók az adatközpontok, amelyen a felhőszolgáltatás a helyi időzónát. A következő három idő bélyeg log táblázat oszlopai használhatók:

* **PreciseTimeStamp**: az esemény időbélyegzője az ETW. Azt jelenti, az az idő, a rendszer naplózza az eseményt az ügyfélről.
* **IDŐBÉLYEG**: értékét **PreciseTimeStamp** a feltöltési gyakoriságának határ lefelé kerekítve. Például ha a feltöltési gyakoriságának 5 perc és az esemény időpontja. 00:17:12, IDŐBÉLYEG: 00:15:00.
* **Időbélyeg**: az időbélyeg, amikor az entitást az Azure-tábla sikeresen létrehozva.

**Hogyan kezelhetem költségeit, amikor diagnosztikai adatainak összegyűjtése?**

Az alapértelmezett beállításokat (**naplózási szintjének** beállítása **hiba**, és **átviteli időszak** beállítása **1 perces**) úgy tervezték, a költségek csökkentésére. A számítási költségek további diagnosztikai adatok összegyűjtésében, vagy ha csökkenti az átviteli időszak növelése. Nem gyűjt több adatot kell, és ne felejtse el az adatok gyűjtésének letiltása, ha már nincs szüksége. Mindig engedélyezheti azt újra, még akkor is futási időben ebben a cikkben korábban leírtaknak megfelelően.

**Hogyan sikertelen kérelmekről készült naplók gyűjtése az IIS?**

Alapértelmezés szerint az IIS sikertelen kérelmekről készült naplók nem gyűjt. Sikertelen kérelmekről készült naplók összegyűjtése a webes szerepkör a web.config fájl szerkesztésével állíthat be az IIS.

**Nem érkeznek meg hozzám nyomkövetési adatokat a RoleEntryPoint módszerek, például OnStart. mi a baj?**

A módszerek **RoleEntryPoint** nem az IIS-ben a WAIISHost.exe környezetében nevezzük. A konfigurációs adatokat a Web.config fájlban, amely általában lehetővé teszi, hogy nyomon követése nem vonatkozik. A probléma megoldásához .config fájl hozzáadása a webes szerepkör projekt, és nevezze el a fájlt a kimeneti tartalmazó szerelvény megfelelően a **RoleEntryPoint** kódot. Az alapértelmezett webes szerepkör projekt a .config fájl neve WAIISHost.exe.config kell lennie. Ehhez a fájlhoz adja hozzá a következő sorokat:

```
<system.diagnostics>
  <trace>
      <listeners>
          <add name “AzureDiagnostics” type=”Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener”>
              <filter type=”” />
          </add>
      </listeners>
  </trace>
</system.diagnostics>
```

Az a **tulajdonságok** ablakban állítsa be a **Copy to Output Directory** tulajdonságot **mindig Másolás**.

## <a name="next-steps"></a>További lépések
Diagnosztikai naplózás az Azure-ban kapcsolatos további információkért lásd: [diagnosztika engedélyezése az Azure Cloud Services és virtual machines](cloud-services/cloud-services-dotnet-diagnostics.md) és [az Azure App Service Web Apps-alkalmazások diagnosztikai célú naplózásának engedélyezése](app-service/web-sites-enable-diagnostic-log.md).

