---
title: Azure Cloud Services és a virtuális gépek diagnosztika beállítva |} Microsoft Docs
description: Ismerje meg, hogyan állíthat be Azure cloude szolgáltatások és virtuális gépek (VM) a Visual Studio hibakeresési diagnosztika.
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
ms.date: 11/11/2016
ms.author: mikejo
ms.openlocfilehash: 34c667b0a594682e4d099e7bff64bfdb336b850b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
ms.locfileid: "30292540"
---
# <a name="set-up-diagnostics-for-azure-cloud-services-and-virtual-machines"></a>Azure Cloud Services és a virtuális gépek diagnosztika beállítása
Egy Azure-felhőszolgáltatásban és a virtuális gép van szüksége, amikor a Visual Studio segítségével könnyebben beállítása az Azure diagnosztikai. Diagnosztika rendszeradatok és a virtuális gépek és a virtuálisgép-példánya, amely a felhőalapú szolgáltatás futtatásához a naplózási adatokat rögzíti. Diagnosztikai adatok átvitele történik meg a választott tárfiók. Diagnosztika további információ az Azure-naplózás: [az Azure App Service Web Apps diagnosztikai naplózás engedélyezése](app-service/web-sites-enable-diagnostic-log.md).

Ez a cikk azt bemutatják a Visual Studio segítségével kapcsolja be, és előtti és utáni telepítési Azure Diagnostics beállítása. Útmutató: diagnosztika beállítása az Azure virtuális gépeken, jelölje be a diagnosztikai adatok gyűjtéséhez típusú, és az információk megtekintéséhez után.

Azure Diagnostics beállításához használhatja az alábbi lehetőségek közül:

* A diagnosztikai beállítások módosítása a **diagnosztikai konfigurációja** párbeszédpanel a Visual Studióban. A beállítások lesznek mentve (az Azure SDK 2.4 és korábbi verzióiban, a fájl neve diagnostics.wadcfg) diagnostics.wadcfgx nevű fájlban. Akkor is közvetlenül tudja módosítani a konfigurációs fájlt. Ha a felhasználó a fájl, a konfigurációs módosítások érvénybe telepít, a felhő legközelebb az Azure szolgáltatás, vagy a szolgáltatás futtatásához az emulátorban.
* Használja a Cloud Explorer vagy a Visual Studio Server Explorer egy felhőalapú szolgáltatás vagy a virtuális gépen, amelyen fut a diagnosztikai beállítások módosításához.

## <a name="azure-sdk-26-diagnostics-changes"></a>Azure SDK 2.6 diagnostics changes
A következő módosításokat az Azure SDK 2.6 és a későbbi projekteket a Visual Studio vonatkoznak:

* A helyi emulátor mostantól támogatja a diagnosztika. Ez azt jelenti, hogy diagnosztikai adatok gyűjtésére, és győződjön meg arról, hogy az alkalmazás hozza létre a megfelelő nyomkövetések míg fejlesztése és tesztelése a Visual Studio. A kapcsolati karakterlánc `UseDevelopmentStorage=true` diagnosztikai adatok gyűjtésének bekapcsolása a futtatása során a felhőszolgáltatási projektet a Visual Studio az Azure storage emulator használatával. Minden diagnosztikai adatgyűjtés fejlesztési tárolási tárfiókban.
* A diagnosztika tárolási fiók kapcsolati karakterlánc `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` a szolgáltatás konfigurációs (.cscfg) fájljában tárolja. Az Azure SDK 2.5-ös a diagnosztikai tárfiók a diagnostics.wadcfgx fájlban megadott.

A kapcsolati karakterláncban eltérő módon néhány főbb módon Azure SDK 2.6, és később és Azure SDK 2.4 és korábbi működik:

* Azure SDK 2.4 és korábbi a kapcsolati karakterlánc segítségével futtatókörnyezettel, a beépülő modul diagnosztika átvitelére a diagnosztikai naplók tárfiókadatok beolvasása.
* Azure SDK 2.6 vagy újabb a Visual Studio a diagnosztika kapcsolati karakterlánc segítségével hozza létre az Azure Diagnostics bővítmény közzététele során a megfelelő tárolási fiók adatokkal. A kapcsolati karakterlánc segítségével megadhatja a különböző szolgáltatáskonfiguráció közzététele során használó Visual Studio eltérő tárfiókokból. Azonban a beépülő modul diagnosztika Azure SDK 2.5 után nem érhető el, mert a .cscfg fájlban önmagában nem állítható be a diagnosztikai bővítményt. Be kell állítania a bővítmény külön eszközök, például a Visual Studio vagy a PowerShell használatával.
* A PowerShell használatával a diagnosztika bővítmény beállításának folyamatán leegyszerűsítése csomag kimenetét a Visual Studio tartalmazza a nyilvános konfigurációs XML-t a diagnosztika bővítmény, az egyes szerepkörökhöz. Visual Studio a diagnosztika kapcsolati karakterlánc használatával feltöltése a nyilvános konfigurációban a tárfiók adatait. A nyilvános konfigurációs fájljai létrejönnek a bővítmények mappában. A nyilvános konfigurációs fájljainak PaaSDiagnostics elnevezési mintát használja. &lt;szerepkörnév\>. PubConfig.xml. A PowerShell-alapú telepítések ebben a mintában segítségével minden egyes konfigurációs hozzárendelése egy szerepkörhöz.
* A [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040) használja a kapcsolati karakterláncot a .cscfg fájlban a diagnosztikai adatok eléréséhez. Az adatok jelennek meg a **figyelés** fülre. A kapcsolati karakterlánc szükséges adja meg a részletes figyelési adatait a portálon megjeleníteni kívánt szolgáltatást.

## <a name="migrate-projects-to-azure-sdk-26-and-later"></a>Telepítse át a projektek Azure SDK 2.6 vagy újabb
Azure SDK 2.5 az Azure SDK 2.6 vagy újabb kiadására, ha a .wadcfgx fájlban megadott diagnosztikai tárfiók telepít át, ha a tárfiók marad az adott meg. Eltérőek a tárolási konfigurációk eltérő tárfiókokból rugalmasan előnyeit, adja hozzá manuálisan a kapcsolati karakterláncot a projekthez. Ha az Azure SDK 2.4 vagy korábbi verzióról Azure SDK 2.6, amelybe migrálna projekt, a diagnosztika kapcsolati karakterláncok megmaradnak. Azonban ügyeljen arra, hogyan kapcsolati karakterláncok az Azure SDK 2.6, az előző szakaszban leírt kezelik a változásai.

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Hogyan határozza meg a Visual Studio a diagnosztikai tárfiók
* A diagnosztika kapcsolódási karakterlánc esetén a .cscfg fájlban, Visual Studio használ közzététele során, és a nyilvános konfigurációs XML-fájlok csomagolás során készít a diagnosztika bővítmény beállításához.
* Ha egy diagnosztikai kapcsolati karakterlánc nem szerepel a .cscfg fájlban, a Visual Studio visszaáll állíthatja be a diagnosztikai bővítmény közzététele, és a nyilvános konfigurációs XML-kód generálásához .wadcfgx fájlban megadott tárolási fiók használatával a fájlok csomagban.
* A diagnosztika kapcsolati karakterláncot a .cscfg fájlban élvez a tárfiók a .wadcfgx fájlban. Ha a diagnosztika a kapcsolati karakterlánc a .cscfg fájlban megadott Visual Studio kapcsolati karakterlánc és figyelmen kívül hagyja a .wadcfgx storage-fiókot.

### <a name="what-does-the-update-development-storage-connection-strings-check-box-do"></a>Mit jelent a "Fejlesztés tárolási kapcsolati karakterláncok... frissítése" négyzet?
A **fejlesztési tárolási kapcsolati karakterláncok frissítése a diagnosztika és a gyorsítótár Microsoft Azure storage-fiók hitelesítő adataival történő Microsoft Azure közzététel** jelölőnégyzet be kényelmesen minden fejlesztési tárterület frissítése fiók kapcsolódási karakterláncokat közzététele során megadott Azure storage-fiók.

Például, ha bejelöli ezt a jelölőnégyzetet, és a diagnosztika kapcsolati karakterláncot adja meg `UseDevelopmentStorage=true`, az Azure-ba, a projekt közzétételekor a Visual Studio automatikusan frissíti a diagnosztika kapcsolati karakterláncot, amelyet a storage-fiók a Közzététel varázsló. Azonban ha egy valódi tárfiók diagnosztika-kapcsolati karakterláncként megadva, a fiók használja.

## <a name="diagnostics-functionality-differences-in-azure-sdk-24-and-earlier-vs-azure-sdk-25-and-later"></a>Diagnosztikai funkciók eltérései Azure SDK 2.4 és a korábbi vs. Az Azure SDK 2.5-ös és újabb verziók
Ha a projekthez az Azure SDK 2.4 és a korábbi Azure SDK 2.5-ös vagy újabb verzióra frissít, vegye figyelembe a következő diagnosztikai funkciót különbségek:

* **Konfigurációs API-k elavultak**. Programozható konfigurációja diagnosztikai elérhető Azure SDK 2.4 és korábbi verziók, de Azure SDK 2.5-ös vagy újabb elavult. Ha a diagnosztika jelenleg definiált kódban, újra kell konfigurálnia ezeket a beállításokat az áttelepített projekt diagnosztikai teljesen új gombra. A diagnosztika konfigurációs fájl az Azure SDK 2.4 diagnostics.wadcfg. A diagnosztika konfigurációs fájl az Azure SDK 2.5-ös vagy újabb diagnostics.wadcfgx.
* **Diagnosztika felhőalkalmazásokhoz szolgáltatás beállítható, hogy csak a szerepkör szintjén**. Azure SDK 2.5-ös vagy újabb rendszerben nem állítható be szolgáltatás felhőalkalmazásokhoz diagnosztika a példány szintjén.
* **Minden alkalommal, amikor telepíti az alkalmazást, a diagnosztikai konfigurációja frissül**. Ha a Visual Studio Server Explorer a diagnosztika konfigurációjának módosítása, és ezután telepítse újra az alkalmazást paritású problémák léphetnek fel.
* **Azure SDK 2.5-ös vagy újabb rendszerben összeomlási memóriaképek vannak konfigurálva, a diagnosztika konfigurációs fájlban, és nem a kódban**. Ha összeomlási memóriaképek kódban konfigurálva, manuálisan kell áthelyezni a konfigurációs kódból konfigurációs fájlból. Összeomlási memóriaképek Azure SDK 2.6 az áttelepítés során nem kerülnek.

## <a name="turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them"></a>Diagnosztika a cloud service projektek bekapcsolása a központi telepítésük előtt
A Visual Studio a szerepkörök az Azure-ban való futtatásakor a szolgáltatás telepítése előtt az emulátorban diagnosztikai adatainak hozhatja létre. A diagnosztikai beállításokat a Visual Studio összes módosítását menti a diagnostics.wadcfgx konfigurációs fájlban. Ezek a beállítások a tárfiók, ahova menteni szeretné diagnosztikai adatokat a felhőalapú szolgáltatás telepítésekor adja meg.

[!INCLUDE [cloud-services-wad-warning](../includes/cloud-services-wad-warning.md)]

### <a name="to-turn-on-diagnostics-in-visual-studio-before-deployment"></a>A telepítés előtt a Visual Studio diagnosztika bekapcsolásához

1. Válassza ki a helyi menüben a szerepkör **tulajdonságok**. A szerepkör **tulajdonságok** párbeszédpanelen jelölje ki a **konfigurációs** fülre.
2. Az a **diagnosztika** területen győződjön meg arról, hogy a **engedélyezése diagnosztikai** jelölőnégyzet be van jelölve.
   
    ![Diagnosztika engedélyezése menüpont használatának](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796660.png)
3. A tárfiók a diagnosztikai adatok megadásához válassza a három ponttal (…) gombra.
   
    ![Adja meg a tárfiók használata](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796661.png)
4. Az a **tárolási kapcsolati karakterlánc létrehozása** párbeszédpanelen adja meg, hogy szeretné, hogy az Azure storage emulator, Azure-előfizetéssel, használatával történő kapcsolódást, vagy manuálisan kell megadni a hitelesítő adatokat.
   
    ![Tárolási fiók párbeszédpanel](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796662.png)
   
   * Ha **Microsoft Azure storage emulator**, a kapcsolati karakterlánc értéke `UseDevelopmentStorage=true`.
   * Ha **az előfizetés**, válassza ki a használni kívánt Azure-előfizetést, és adjon meg egy fióknevet. Az Azure-előfizetések kezeléséhez, jelölje be **fiókok kezelése**.
   * Ha **manuálisan kell megadni a hitelesítő adatok**, adja meg a nevét, és a használni kívánt Azure-fiók kulcsát.
5. Megtekintéséhez a **diagnosztikai konfigurációja** párbeszédpanelen jelölje ki **konfigurálása**. Kivéve az **általános** és **napló könyvtárak**, minden egyes diagnosztika adatforrás gyűjtheti jelöli. Az alapértelmezett **általános** lapon kínál a következő diagnosztikai adatgyűjtési beállítások: **csak a hibák**, **kapcsolatos összes információ**, és **egyéni terv**. Az alapértelmezett **csak a hibák** beállítás is, a lehető legkevesebb használja, mert az nem továbbítja figyelmeztetés vagy a nyomkövetési adatokat. A **kapcsolatos összes információ** beállítás a legtöbb információt továbbítja, minél hatékonyabb tárolást használ, és ezért a legköltségesebb beállítás.
   
    ![Az Azure diagnosztikai és a konfiguráció engedélyezése](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
6. Ehhez a példához válassza ki a **egyéni terv** beállítás, így testre szabhatja az összegyűjtött adatokat.
7. Az a **lemezkvóta MB-ban** mezőben állíthatja be mekkora lemezterület diagnosztikai adatok tárfiókba lefoglalni. Módosíthatja, vagy fogadja el az alapértelmezett értéket.
8. Diagnosztikai adatainak gyűjtése kívánt minden egyes lapon jelölje be a **átvitelének engedélyezése \<típusú naplózása\>**  jelölőnégyzetet. Például, ha azt szeretné, gyűjti az alkalmazásnaplókat, a **alkalmazásnaplók** lapon jelölje be a **alkalmazásnaplók adatátvitel engedélyezése** jelölőnégyzetet. Is adja meg, hogy bármilyen egyéb olyan adatot, minden diagnosztikai adatok típus szükséges. A konfigurációs adatokat az egyes lapokon, tekintse meg a szakasz **diagnosztika adatforrást** című cikkben. 
9. Szeretne diagnosztikai adatgyűjtés engedélyezését, válassza ki a **OK**.
10. Futtassa az Azure-felhőszolgáltatás-projekt a Visual Studio, a szokásos módon. Az alkalmazás használata során, a naplózási adatok, hogy engedélyezve van az Azure storage-fiók, amely a megadott menti.

## <a name="turn-on-diagnostics-on-azure-virtual-machines"></a>Engedélyezze a diagnosztikát Azure virtuális gépeken
A Visual Studio diagnosztikai adatokat az Azure virtuális gépek hozhatja létre.

### <a name="to-turn-on-diagnostics-on-azure-virtual-machines"></a>Az Azure virtuális gépeken diagnosztika bekapcsolásához

1. A Server Explorer eszközben jelölje ki az Azure csomópontot, és csatlakozzon az Azure-előfizetéshez, ha még nem csatlakozott.
2. Bontsa ki a **virtuális gépek** csomópont. Hozzon létre egy új virtuális gépet, vagy válasszon ki egy meglévő csomópontot.
3. Válassza ki a helyi menüben a kívánt virtuális gép **konfigurálása**. A virtuális gép konfigurációs párbeszédpanel jelenik meg.
   
    ![Egy Azure virtuális gép konfigurálása](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796663.png)
4. Ha még nincs telepítve, adja hozzá a Microsoft Monitoring Agent diagnosztika kiterjesztést. Ez a bővítmény virtuális gépet az Azure diagnosztikai adatokat gyűjthet. A **telepített bővítmények**, a a **jelöljön ki egy rendelkezésre álló bővítményt** legördülő listában válassza ki **Microsoft Monitoring Agent diagnosztika**.
   
    ![Egy Azure virtuálisgép-bővítmény telepítése](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766024.png)
   
    > [!NOTE]
   > Más diagnosztikai bővítmények érhetők el a virtuális gépek számára. További információkért lásd: [virtuálisgép-bővítmények és a Windows szolgáltatások](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features).
   > 
   > 
5. A bővítményt, és a nézet hozzáadása a **diagnosztikai konfigurációja** párbeszédpanelen jelölje ki **Hozzáadás**.
6. A tárfiók megadásához válassza **konfigurálása**, majd válassza ki **OK**.
   
    Minden lap (kivéve a **általános** és **napló könyvtárak**) diagnosztika adatforrás gyűjtheti jelöli.
   
    ![Az Azure diagnosztikai és a konfiguráció engedélyezése](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
   
    Az alapértelmezett lapon **általános**, a következő diagnosztikai adatgyűjtési beállítások kínálja: **csak a hibák**, **kapcsolatos összes információ**, és **egyéni terv**. Az alapértelmezett beállítás, **csak a hibák**, a lehető legkevesebb tárolási időt vesz igénybe, mert az nem továbbítja figyelmeztetés vagy a nyomkövetési adatokat. A **kapcsolatos összes információ** beállítás a legtöbb információt továbbítja, és a lehetőség, ezért a legköltségesebb tárhely tekintetében.
7. Ehhez a példához válassza ki a **egyéni terv** beállítás testreszabásához, az adatok gyűjtése.
8. A **lemezkvóta MB-ban** mezőben határozza meg, hogy mennyi helyet szeretne lefoglalni a tárfiókban lévő diagnosztikai adatok. Ha azt szeretné, módosíthatja az alapértelmezett érték.
9. Az egyes lapokon diagnosztikai adatokat szeretne gyűjteni, válassza ki a **átvitelének engedélyezése \<típusú naplózása\>**  jelölőnégyzetet.
   
    Például, ha szeretne gyűjteni az alkalmazásnaplókat, jelölje be a **alkalmazásnaplók adatátvitel engedélyezése** jelölőnégyzetet a **alkalmazásnaplók** fülre. Továbbá adja meg a bármilyen egyéb olyan adatot, amely az egyes diagnosztikai adatok szükséges. A konfigurációs adatokat az egyes lapokon, tekintse meg a szakasz **diagnosztika adatforrást** című cikkben.
10. Engedélyezését, amelyet minden diagnosztikai adatok gyűjtésére, válassza ki a **OK**.
11. Mentse a frissített projektet.
    
    Az üzenet a **Microsoft Azure tevékenységnapló** ablakban az látható, hogy a virtuális gép frissült.

## <a name="set-up-diagnostics-data-sources"></a>Diagnosztika adatforrás létrehozása
Diagnosztikai adatok gyűjtésének engedélyezése után dönthet úgy, pontosan milyen adatforrásokat szeretne gyűjteni, és az összegyűjtött információkról. A következő szakaszok ismertetik a lapfülek a **diagnosztikai konfigurációja** párbeszédpanel megnyitásához, és azt jelenti, hogy minden egyes konfigurációs beállítást.

### <a name="application-logs"></a>Alkalmazásnaplók
Alkalmazásnaplók rendelkezik egy webes alkalmazás által létrehozott diagnosztikai információkat. Ha szeretné rögzíteni az alkalmazásnaplókat, jelölje be a **alkalmazásnaplók adatátvitel engedélyezése** jelölőnégyzetet. Növelje, vagy csökkentse a tárfiók a alkalmazásnaplók átvitelét között, módosítsa a **átviteli időtartam (perc)** érték. Úgy, hogy a naplóban rögzített adatmennyiség is módosíthatja a **naplózási szintjének** érték. Válassza például **részletes** további információért, vagy válasszon **kritikus** csak kritikus hibák rögzítéséhez. Ha egy adott diagnosztika szolgáltatót, amelyet alkalmazásnaplók bocsát ki, a szolgáltató GUID hozzáadásával a naplók rögzítése a **szolgáltató GUID** mezőbe.

  ![Alkalmazásnaplók](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758145.png)

Az alkalmazás naplóiban kapcsolatos további információkért lásd: [az Azure App Service Web Apps diagnosztikai naplózás engedélyezése](app-service/web-sites-enable-diagnostic-log.md).

### <a name="windows-event-logs"></a>Windows-eseménynaplók,
Rögzíti a Windows eseménynaplóiban keresse meg, válassza ki a **engedélyezése a Windows-Eseménynapló átviteli** jelölőnégyzetet. Növelje, vagy csökkentse a tárfiók az Eseménynapló átvitelét között, módosítsa a **átviteli időtartam (perc)** érték. Jelölje be a jelölőnégyzetet, a követni kívánt események típusú.

![Eseménynaplók](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796664.png)

Ha szeretne megtudni az Azure SDK 2.6-os vagy újabb verzióját, és meg szeretné adni az egyik egyéni adatforrás, akkor adja meg a a **\<adatforrás neve\>** szövegmezőbe, majd válassza ki azt **Hozzáadás**. Az adatforrás a diagnostics.cfcfg fájl kerül.

Ha az Azure SDK 2.5 használja, és adjon meg egyéni adatforrást kíván, hozzáadhat úgy, hogy a `WindowsEventLog` a diagnostics.wadcfgx szakasza fájlba, például a következő példa:

```
<WindowsEventLog scheduledTransferPeriod="PT1M">
   <DataSource name="Application!*" />
   <DataSource name="CustomDataSource!*" />
</WindowsEventLog>
```
### <a name="performance-counters"></a>Teljesítményszámlálók
Teljesítményszámláló adatai segítségével keresse meg a rendszer szűk keresztmetszetei és finomhangolhatja, rendszer és az alkalmazás teljesítményét. További információkért lásd: [az Azure-alkalmazások létrehozása és használata teljesítményszámlálók](https://msdn.microsoft.com/library/azure/hh411542.aspx). Teljesítményszámlálók rögzíteni, jelölje ki a **teljesítményszámlálók átvitel engedélyezése** jelölőnégyzetet. Növelje, vagy csökkentse a tárfiók az Eseménynapló átvitelét között, módosítsa a **átviteli időtartam (perc)** érték. Jelölje be a jelölőnégyzetet, a teljesítményszámlálókkal nyomon követni kívánt.

![Teljesítményszámlálók](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758147.png)

Listán nem szereplő teljesítményszámláló követheti nyomon, írja be a teljesítményszámláló a javasolt szintaxis használatával. Válassza ki és **Hozzáadás**. A virtuális gépen az operációs rendszer meghatározza, hogy mely teljesítményszámlálókat, követheti nyomon. Szintaxissal kapcsolatos további információkért lásd: [adja meg a számláló elérési útját](https://msdn.microsoft.com/library/windows/desktop/aa373193.aspx).

### <a name="infrastructure-logs"></a>Infrastruktúra-naplók
Infrastruktúra-naplók rendelkezik az Azure diagnosztikai infrastruktúra, a RemoteAccess, és a RemoteForwarder modul kapcsolatos információkat. Infrastruktúra-naplók információkat gyűjtsön, válassza ki a **az infrastruktúra-naplók átvitel engedélyezése** jelölőnégyzetet. Növelje, vagy csökkentse a tárfiók átvitelét az infrastruktúra-naplók közötti távolság, módosítsa a **átviteli időtartam (perc)** érték.

![Diagnosztika infrastruktúra naplók](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758148.png)

További információkért lásd: [naplózási adatok gyűjtése az Azure Diagnostics használatával](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### <a name="log-directories"></a>Napló könyvtárak
Napló könyvtárak napló könyvtárak Internet Information Services (IIS) kérések, sikertelen kérelmek vagy mappák, az Ön által gyűjtött adatokkal rendelkeznek. Napló könyvtárak rögzíteni, jelölje ki a **napló könyvtárak átvitel engedélyezése** jelölőnégyzetet. Növelje, vagy csökkentse a tárfiók átvitele a naplók közötti távolság, módosítsa a **átviteli időtartam (perc)** érték.

Jelölje be a jelölőnégyzetet, a naplók gyűjtésére, például a kívánt **IIS-napló** és **sikertelen kérelem** naplókat. Alapértelmezett tároló tároló neveket, de a nevét módosíthatja.

Egyetlen mappa származó naplók rögzítése. Adja meg az elérési utat a **naplózási könyvtár abszolút a** szakaszt, és válassza ki **könyvtár hozzáadása**. A naplókat a rendszer a megadott tárolókban lévő rögzíti.

![Napló könyvtárak](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796665.png)

### <a name="etw-logs"></a>ETW-naplók
Ha [Windows esemény-nyomkövetése](https://msdn.microsoft.com/library/windows/desktop/bb968803\(v=vs.85\).aspx) (ETW) és szeretné rögzíteni ETW-naplók, válassza ki a **az ETW-naplók átvitel engedélyezése** jelölőnégyzetet. Növelje, vagy csökkentse a tárfiók átvitele a naplók közötti távolság, módosítsa a **átviteli időtartam (perc)** érték.

Az események eseményforrások és a megadott eseményjegyzékfájlok kerülnek rögzítésre. Ha meg szeretné adni az eseményforrás a **eseményforrások** szakasz. Adjon meg egy nevet, majd válassza ki **eseményforrás hozzáadása**. Hasonlóképpen, megadhat egy esemény jegyzékfájl a a **esemény jelentkezik** szakaszt, és válassza ki **esemény Manifest hozzáadása**.

![ETW-naplók](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766025.png)

Az ETW-keretrendszer támogatott ASP.NET keresztül az osztályokat a [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) névtér. A Microsoft.WindowsAzure.Diagnostics névtér, amely örökli, és kiterjeszti a standard [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) osztályokat, lehetővé teszi a [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) , a naplózás az Azure környezetben keretrendszer. További információkért lásd: [átvenni az irányítást a naplózás és nyomkövetés a Microsoft Azure-ban](https://msdn.microsoft.com/magazine/ff714589.aspx) és [Azure Cloud Services és a virtuális gépek diagnosztika engedélyezése](cloud-services/cloud-services-dotnet-diagnostics.md).

### <a name="crash-dumps"></a>összeomlási memóriaképek,
Rögzíteni az információt, amikor a szerepkör példánya összeomlik, válassza ki a **összeomlási memóriaképek adatátvitel engedélyezése** jelölőnégyzetet. (Az ASP.NET kezeli a legtöbb kivételeket, mert azt csak a feldolgozói szerepkörök számára általában hasznos.) Növelje, vagy csökkentse a tárolóhely számára a összeomlási memóriaképek aránya, módosítsa a **kvóta (%)** érték. Módosíthatja a tárolót, ha a összeomlási memóriaképek tárolják, és válassza ki, hogy szeretné rögzíteni egy **teljes** vagy **Mini** biztonsági másolat.

A következő képernyőkép jelenleg nyomon követik a folyamatok listáját. Jelölje be a jelölőnégyzetet, a rögzíteni kívánt folyamatok. Egy másik folyamat felvenni a listára, írja be a folyamat neve, és válassza ki **hozzáadása folyamat**.

![összeomlási memóriaképek,](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766026.png)

További információkért lásd: [átvenni az irányítást a naplózás és nyomkövetés a Microsoft Azure-ban](https://msdn.microsoft.com/magazine/ff714589.aspx) és [Microsoft Azure Diagnostics rész 4: egyéni naplózási összetevők és az Azure Diagnostics 1.3 módosítások](http://justazure.com/microsoft-azure-diagnostics-part-4-custom-logging-components-azure-diagnostics-1-3-changes/).

## <a name="view-the-diagnostics-data"></a>A diagnosztikai adatainak megtekintése
Egy felhőalapú szolgáltatás, vagy a virtuális gép diagnosztikai adatok begyűjtését követően megtekintheti.

### <a name="to-view-cloud-service-diagnostics-data"></a>Cloud service diagnosztikai adatainak megtekintése
1. A felhőalapú szolgáltatás, mint a szokásos telepítése, és futtassa azt.
2. Megtekintheti a diagnosztikai adatok olyan jelentést, amely a Visual Studio hoz létre, vagy a táblák tárfiókba. A jelentésben, nyissa meg a Cloud Explorer vagy a Server Explorer eszközben, nyissa meg a csomópont, és válassza a szerepkör esetében a helyi menü nézet **diagnosztikai adatok megtekintéséhez**.
   
    ![Diagnosztikai adatok megtekintése](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748912.png)
   
    Megjelenik az elérhető adatokat tartalmazó jelentést.
   
    ![A Visual Studio a Microsoft Azure diagnosztikai jelentés](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796666.png)
   
    Ha a legfrissebb adatok nem látható, lehetséges, hogy az átviteli időszak leteltét vár.
   
    Azonnal frissítheti az adatokat, válassza ki a **frissítése** hivatkozásra. Az adatok automatikusan frissíti, jelölje be az időközt a **automatikus frissítésének** legördülő listában. A hiba adatok exportálása, válassza ki a **Exportálás CSV-FÁJLBA** gombra kattintva hozzon létre egy vesszővel tagolt fájlt, amely megnyitható az Excel-munkalap.
   
    Cloud Explorer vagy a Server Explorer nyissa meg a tárfiókot, amely a központi telepítés van társítva.
3. Nyissa meg a tábla megjelenítő a diagnosztika táblákat, és tekintse át az összegyűjtött adatokat. Az IIS-naplókba és egyéni naplókat nyissa meg a blob-tároló. Az alábbi táblázat a táblák vagy a blob tárolók, amely tartalmazza az adatokat a különböző naplófájlokat. Az adatokat, hogy a naplófájl, a táblamódosítási bejegyzéseket tartalmaz **EventTickCount**, **DeploymentId**, **szerepkör**, és **RoleInstance** , mely virtuális gépek és a szerepkör jön létre az adatok azonosítása és mikor. 
   
   | Diagnosztikai adatok | Leírás | Tartózkodási hely |
   | --- | --- | --- |
   | Alkalmazásnaplók |A kódot állít elő, módszerek meghívásával-naplók a **System.Diagnostics.Trace** osztály. |WADLogsTable |
   | Eseménynaplók |A Windows eseménynaplóiban keresse meg a virtuális gépek adatait. Windows ezek a naplók információkat tárol, de alkalmazásokhoz és szolgáltatásokhoz is a naplók hibákat, és naplózza az adatokat. |WADWindowsEventLogsTable |
   | Teljesítményszámlálók |Bármilyen teljesítményszámláló, amely elérhető a virtuális gépen Ön is gyűjti az adatokat. Az operációs rendszer biztosít teljesítményszámlálókat, többek között számos statisztika, például a memória kihasználtsága és a processzoridő tekintetében. |WADPerformanceCountersTable |
   | Infrastruktúra-naplók |A diagnosztika infrastruktúrától maga létrehozott naplók. |WADDiagnosticInfrastructureLogsTable |
   | IIS-naplók |Jegyezze fel a webes kérések naplók. A felhőalapú szolgáltatás lekérdezi a jelentős mennyiségű forgalom, ha ezek a naplók hosszadalmas lehet. Célszerű gyűjt, és ezek az adatok csak akkor, ha esetleg szükség lenne rá. |Sikertelen kérelem naplózza a blob-tárolóban alatt üvegvatta-az iis-failedreqlogs, egy adott központi telepítés, a szerepkör és a példány elérési úton található. Teljes naplók üvegvatta-az iis-naplófájlok alatt található. A fájl tételek a WADDirectories táblában. |
   | összeomlási memóriaképek, |A felhőalapú szolgáltatás folyamat (általában a feldolgozói szerepkör) bináris lemezképet biztosít. |üvegvatta-crush-memóriaképek blob tároló |
   | Egyéni naplófájlok |Naplók meg az előre megadott adatok. |Megadhatja a kódban az egyéni naplófájlok helyét a tárfiókban lévő. Például egy egyéni blob tároló is megadhat. |
4. Ha bármilyen típusú adatok csonkolva van, próbálja meg növelni a puffer az adatok típus vagy lerövidíteni adatok továbbítása a virtuális gépről a tárfiók között.
5. (Választható) A tárfiók alkalmanként általános tárolási költségek csökkentése a véglegesen törlendő adatokat törölni.
6. Ha így tesz, teljes körű telepítésére, az diagnostics.cscfg fájl (az Azure SDK 2.5 .wadcfgx) frissült az Azure-ban, és a felhőalapú szolgáltatás szerzi be a diagnosztikai konfigurációs módosításait. Ha ehelyett egy meglévő telepítését frissíti, a .cscfg fájlban az Azure-ban nem frissítésre. Diagnosztikai beállítások azonban továbbra is módosítani a következő szakaszban ismertetett lépések szerint. Teljes körű telepítésére végrehajtása és a meglévő telepítés frissítését kapcsolatos további információkért lásd: [Azure alkalmazás közzététele varázsló](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-view-virtual-machine-diagnostics-data"></a>Virtuális gép diagnosztikai adatainak megtekintése
1. Válassza ki a helyi menüben a virtuális gép **diagnosztikai adatok megtekintéséhez**.
   
    ![Egy Azure virtuális gépen diagnosztikai adatok megtekintése](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766027.png)
   
    A **diagnosztika összegzés** párbeszédpanel jelenik meg.
   
    ![Virtuális gép az Azure diagnostics összefoglaló](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796667.png)  
   
    Ha a legfrissebb adatok nem látható, lehetséges, hogy az átviteli időszak leteltét vár.
   
    Azonnal frissítheti az adatokat, válassza ki a **frissítése** hivatkozásra. Az adatok automatikusan frissíti, jelölje be az időközt a **automatikus frissítésének** legördülő listában. A hiba adatok exportálása, válassza ki a **Exportálás CSV-FÁJLBA** gombra kattintva hozzon létre egy vesszővel tagolt fájlt, amely megnyitható az Excel-munkalap.

## <a name="set-up-cloud-service-diagnostics-after-deployment"></a>A telepítés utáni cloud service diagnosztika beállítása
Ha a probléma egy felhőszolgáltatás, amely már fut éppen vizsgálja, célszerű, adatokat gyűjthet, amelyek nem adott meg a szerepkör eredetileg központi telepítése előtt. Ebben az esetben is elindítható, hogy adatokat gyűjt a Server Explorer beállításait. Diagnosztika egyetlen példányt vagy egy szerepkör, attól függően, hogy megnyitja a példányok állíthat be a **diagnosztikai konfigurációja** párbeszédpanelen a példány vagy a szerepkör a helyi menüből. Ha a szerepkör csomópont konfigurálásához bármely végrehajtott módosításokat összes példányára vonatkoznak. Ha a példány csomópont konfigurálásához bármely végrehajtott módosítások csak az adott példányhoz alkalmazni.

### <a name="to-set-up-diagnostics-for-a-running-cloud-service"></a>Egy futó felhőszolgáltatás diagnosztika beállítása
1. A Server Explorer eszközben bontsa ki a **Felhőszolgáltatások** csomópontot, majd bontsa ki a csomópontlista található a szerepkör vagy a példány (vagy mindkettőt), amelyet meg szeretne vizsgálni.
   
    ![Diagnosztika konfigurálása](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748913.png)
2. A helyi menüje egy példány vagy szerepkör csomópontot, válassza ki **frissítés diagnosztikai beállítások**, majd válassza ki a diagnosztikai beállításokat szeretne gyűjteni.
   
    A konfigurációs beállításokkal kapcsolatos további információkért lásd: a szakasz **diagnosztika adatforrást** ebben a cikkben. Diagnosztikai adatok megtekintéséhez kapcsolatos információkért című **diagnosztikai adatok megtekintéséhez** ebben a cikkben.
   
    Ha módosítja a Server Explorer adatgyűjtés, a módosítások érvényben marad mindaddig, amíg a teljes újratelepíti a felhőalapú szolgáltatás. Ha használja az alapértelmezett közzétételi beállítások, a módosítások nem íródnak felül. Az alapértelmezett beállítás közzététele a meglévő üzemelő példány frissítése helyett egy teljes újratelepítés ehhez. Győződjön meg arról, hogy a beállítások központi telepítéskor törölje, keresse fel a **speciális beállítások** a Közzététel varázsló lapot, és törölje a jelet a **üzemelő példány frissítése** jelölőnégyzetet. A jelölőnégyzet nincs bejelölve újratelepítésekor a beállításai visszaállnak az .wadcfgx (vagy .wadcfg) fájlt a beállított keresztül a **tulajdonságok** a szerepkör-szerkesztő. Ha a telepítés frissítéséhez Azure megőrzi a korábbi beállításokat.

## <a name="troubleshoot-azure-cloud-service-issues"></a>Azure cloud service hibák elhárítása
Tapasztal a felhőszolgáltatás-projektekre nézve, például egy szerepkör, amely lekérdezi a "elfoglalt" állapot Beragadt többször újraindul, vagy egy belső kiszolgálóhiba jelez, ha nincsenek eszközöket és technikákat, amelyek segítségével diagnosztizálhatja és hárítsa el a problémát. Adott példák a gyakori problémák és megoldások, és egy áttekintést a fogalmakat és eszközöket, amelyek segítségével diagnosztizálhatja és javíthatja ki ezeket a hibákat, tekintse meg a [Azure PaaS számítási diagnosztikai adatainak](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

## <a name="q--a"></a>Kérdések és válaszok
**Mi a puffer méretét, és hogyan nagy legyen?**

Összes virtuálisgép-példányt kvóták korlátozzák, mennyi diagnosztikai adatok tárolhatók a helyi fájlrendszerben. Továbbá adja meg a puffer mérete az egyes elérhető diagnosztikai adatokat. A puffer mérete úgy viselkedik, mint egy egyéni kvóta az adott adatok. A teljes kvóta és a fennmaradó memória mennyisége megállapításához lásd: a diagnosztika típusú a párbeszédpanel alján. Ha nagyobb puffert, vagy további típusú adatokat ad meg, a teljes kvóta lesz közelítik meg. A teljes kvóta diagnostics.wadcfg vagy .wadcfgx konfigurációs fájl módosításával módosíthatja. A diagnosztika tárolja, az alkalmazásadatok ugyanazt a fájlrendszert. Ha az alkalmazás nagy mennyiségű lemezterületet, nem növeli a teljes diagnosztika kvótát.

**Mi az, hogy az átviteli időszak, és mennyi ideig legyen?**

Az átvitel időtartam ennyi idő alatt, amely eltelte közötti adatokat rögzíti. Minden adatátviteli idő után van áthelyezni az adatokat a helyi fájlrendszerben; virtuális gépen a tárfiókban lévő táblákat. Ha a gyűjtött adatok mennyisége meghaladja a kvótát egy átviteli időszak vége előtt, régebbi adatok törlődnek. Ha adatok vannak elvesztése, mert az adatok mérete meghaladja a pufferméretet vagy a teljes kvóta, érdemes csökkentse az átviteli időtartamot.

**Milyen időzóna az időbélyegek, a rendszer?**

Időbélyeggel van az adatközpont, amelyen a felhőalapú szolgáltatás a helyi időzónára. A következő három alkalommal stamp napló táblázat oszlopai használhatók:

* **PreciseTimeStamp**: az esemény időbélyegzője az ETW. Ez azt jelenti, hogy az eseményt a rendszer naplózza az ügyfél idő.
* **Timestamp típusú**: értéke **PreciseTimeStamp** kerekített le a feltöltési gyakoriságának határ. Például a feltöltési gyakoriságának 5 perc és az esemény ideje 00:17:12, TIMESTAMP akkor 00:15:00.
* **Timestamp típusú**: az időbélyegzőt, amelyen létrejött az entitás az Azure tábla.

**Hogyan kezelhetők a költségek diagnosztikai adatainak összegyűjtése?**

Az alapértelmezett beállításokat (**naplózási szintjének** beállítása **hiba**, és **átviteli időszak** beállítása **1 perces**) költségek minimalizálása érdekében készültek. A számítási költségek további diagnosztikai adatok összegyűjtésében, vagy ha csökkenti az átviteli időtartam növelése. Nem gyűjt több adatot kell, és ne feledje kapcsolni az adatgyűjtést, amikor már nincs szüksége. Mindig engedélyezheti azt újra, még akkor is futási időben, a cikkben leírtak szerint.

**Hogyan gyűjthetem be a sikertelen kérelmek naplóit IIS-ből?**

Alapértelmezés szerint az IIS nem naplógyűjtéshez sikertelen kérelem. A webes szerepkör a web.config fájl szerkesztésével sikertelen kérelem naplók összegyűjtésére IIS állíthat be.

**Nyomkövetési adatok a RoleEntryPoint módszerek, például a OnStart nem érkezik. mi a baj?**

A módszerek **RoleEntryPoint** WAIISHost.exe, nem az IIS-ben környezetében nevezzük. A konfigurációs adatokat a Web.config fájlban, amely általában lehetővé teszi, hogy nyomkövetés nem vonatkozik. A probléma megoldásához, .config fájl hozzáadása a webes szerepkör projekt, és nevezze el a fájlt a kimeneti tartalmazó szerelvény megfelelően a **RoleEntryPoint** kódot. Az alapértelmezett webes szerepkör projekt a .config fájl neve WAIISHost.exe.config kell lennie. Ennek a fájlnak adja hozzá a következő sorokat:

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

Az a **tulajdonságok** ablakban a **másolása a kimeneti könyvtárba** tulajdonságot **mindig másolása**.

## <a name="next-steps"></a>További lépések
Bejelentkezés az Azure diagnostics kapcsolatos további információkért lásd: [Azure Cloud Services és a virtuális gépek diagnosztika engedélyezése](cloud-services/cloud-services-dotnet-diagnostics.md) és [az Azure App Service Web Apps diagnosztikai naplózás engedélyezése](app-service/web-sites-enable-diagnostic-log.md).

