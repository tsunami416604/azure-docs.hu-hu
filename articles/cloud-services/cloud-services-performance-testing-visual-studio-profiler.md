---
title: Profilkészítés helyileg a Compute Emulator a Felhőszolgáltatás |} A Microsoft Docs
services: cloud-services
description: A cloud services teljesítményhibák vizsgálata a a Visual Studio profilerével
documentationcenter: ''
author: mikejo
manager: douge
editor: ''
tags: ''
ms.assetid: 25e40bf3-eea0-4b0b-9f4a-91ffe797f6c3
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/18/2016
ms.author: mikejo
ms.openlocfilehash: ea46039583681bd89e254d153997e3a300041d4e
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341354"
---
# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Felhőszolgáltatás teljesítményének helyi tesztelése az Azure Compute Emulator használatával a Visual Studio Profiler
Felhőszolgáltatás teljesítményének tesztelése a különböző eszközöket és technikákat érhetők el.
Amikor közzétesz egy felhőalapú szolgáltatás az Azure-ba, a Visual Studio profilkészítési adatok gyűjtéséhez és elemzésével aztán rendelkezhet leírtak szerint helyileg [egy Azure-alkalmazás profilkészítés][1].
Használhatja diagnosztikai sokféle teljesítményszámlálók, nyomon követéséhez leírtak szerint [teljesítményszámlálók segítségével az Azure-ban][2].
Előfordulhat, hogy is szeretne profilt az alkalmazást helyileg a compute emulator a felhőbeli üzembe helyezés előtt.

Ez a cikk a profilkészítés CPU-mintavételezési módszerét ismerteti, amely helyben végezhető el az emulátorral. CPU-mintavételezési akkor egy metódust, amely profilkészítés nem nagyon zavaró. A kijelölt mintavételi történik a profiler pillanatképet készít a hívási veremben. Az adatok egy időszakban összegyűjtött, és a jelentésekben látható. Ez a profilkészítési módszer általában azt jelzik, ahol egy nagy számítási igényű alkalmazást a legtöbb munkát a Processzor történik.  Ez lehetővé teszi, hogy a "gyors elérésű útvonalon" Ha az alkalmazás a legtöbb időt tölt.

## <a name="1-configure-visual-studio-for-profiling"></a>1: a profilkészítés a Visual Studio konfigurálása
Először néhány hasznos lehet a profilkészítés során a Visual Studio konfigurációs lehetőség van. Ahhoz, hogy megismerje a profilkészítési jelentéseket, kell az alkalmazás és a is rendszerkönyvtárak szimbólumokat szimbólumok (.pdb fájlok). Győződjön meg arról, hogy a rendelkezésre álló szimbólum kiszolgálók hivatkoznak, érdemes. Ehhez a a **eszközök** elemét a Visual Studióban válassza **beállítások**, majd válassza a **Debugging**, majd **szimbólumok**. Győződjön meg arról, hogy a Microsoft szimbólum kiszolgálók részen **szimbólum (.pdb) helyei**.  Is hivatkozhat http://referencesource.microsoft.com/symbols, amely előfordulhat, hogy További szimbólumfájlok.

![Szimbólum beállításai][4]

Ha szükséges, egyszerűbbé teheti a jelentéseket, hogy a profiler hoz létre, csak a saját kód beállításával. Csak saját kód engedélyezve van a függvény hívása vermek egyszerűsítettek, hogy a jelentések elől rejtve vannak a hívások teljes egészében belső kódtárak és a .NET-keretrendszer. Az a **eszközök** menüben válassza a **beállítások**. Majd bontsa ki a **teljesítménynövelő eszközök** csomópontot, majd **általános**. Jelölje be a **engedélyezése csak a saját kódját profiler jelentések**.

![Csak a saját kód beállítása][17]

Használhatja ezeket az utasításokat egy meglévő projekt vagy egy új projektet.  Ha szeretné kipróbálni az alább ismertetett technikák új projektet hoz létre, válasszon egy C# **Azure Cloud Service** projektre, és válassza ki a **webes szerepkör** és a egy **feldolgozói szerepkör**.

![Az Azure Cloud Service-projekt szerepkörök][5]

Például céljából, adja hozzá a projekthez, amely sok időt vesz igénybe, és bizonyos nyilvánvaló teljesítményprobléma bemutatja néhány kódot. A következő kód például hozzá egy feldolgozói szerepkör projekt:

```csharp
public class Concatenator
{
    public static string Concatenate(int number)
    {
        int count;
        string s = "";
        for (count = 0; count < number; count++)
        {
            s += "\n" + count.ToString();
        }
        return s;
    }
}
```

Ez a kód hívása a feldolgozói szerepkör RoleEntryPoint származtatott osztály a RunAsync metódusában. (A szinkron módon fut-e a metódussal kapcsolatos figyelmeztetést figyelmen kívül.)

```csharp
private async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.
    while (!cancellationToken.IsCancellationRequested)
    {
        Trace.TraceInformation("Working");
        Concatenator.Concatenate(10000);
    }
}
```

Helyi létrehozásához és teszteléséhez a felhőszolgáltatás (Ctrl + F5), hibakeresés nélkül a megoldás konfigurációs beállítása a **kiadási**. Ez biztosítja, hogy az összes fájlok és mappák jönnek létre az alkalmazás helyi futtatásához, és biztosítja, hogy a emulátory systému elindulnak. Győződjön meg arról, hogy a feldolgozói szerepkör fut a tálcán a Compute Emulator felhasználói felületén megkezdődött.

## <a name="2-attach-to-a-process"></a>2: egy folyamat csatolása
Ahelyett, hogy a profilkészítés az alkalmazás a Visual Studio 2010 IDE-ből elindításával, hozzá kell rendelni a profiler futó folyamat. 

A profiler csatlakoztatása egy folyamatot, az a **elemzés** menüben válassza a **Profiler** és **Attach/Detach**.

![Profil lehetőséget csatolása][6]

Egy feldolgozói szerepkör esetében keresse meg a WaWorkerHost.exe folyamat.

![WaWorkerHost folyamat][7]

Ha a projektmappa fájllistájának egy hálózati meghajtón van, a profiler rákérdez, hogy a profilkészítés jelentések mentse másik helyre.

 Is csatlakoztathat egy webes szerepkörben való WaIISHost.exe csatolásával.
Ha az alkalmazás több feldolgozói szerepkör folyamatok, szeretné használni a folyamatazonosító megkülönböztetésükhöz. A folyamatazonosító programozott módon lekérdezheti a folyamat objektumról elérésével. Például ha ezt a kódot ad hozzá a Run metódus szerepkörben lévő RoleEntryPoint származtatott osztály, megtekintheti a Compute Emulator felhasználói felületén tudja, melyik folyamat szeretne csatlakozni, a bejelentkezés.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

A napló megtekintéséhez indítsa el a Compute Emulator felhasználói felületén.

![A Compute Emulator felhasználói felületének indítása][8]

Nyissa meg a feldolgozói szerepkör log konzolablakban a Compute Emulator felhasználói felületén kattintson a konzol ablakának címsorában a. Láthatja a naplózott Folyamatazonosítója.

![Nézet folyamat azonosítója][9]

Egy csatolt, hajtsa végre a lépéseket az alkalmazás felhasználói felületén (ha szükséges) reprodukálnia a forgatókönyvet.

Amikor a Profilkészítés leállítása szeretne, válassza ki a **Profilkészítés leállítása** hivatkozásra.

![A beállítás Profilkészítés leállítása][10]

## <a name="3-view-performance-reports"></a>3: teljesítmény-jelentések megtekintése
A teljesítmény a jelentés az alkalmazás jelenik meg.

Ezen a ponton a profiler végrehajtása megszakad, .vsp fájlba menti az adatokat, és megjelenít egy jelentést, amely megjeleníti az adatok elemzését.

![Profiler-jelentés][11]

Ha a gyakori elérésű útvonal a String.wstrcpy látja, kattintson a csak saját kód megjelenítése csak a felhasználói kód a nézetet.  Ha látja String.Concat, nyomja le az összes kód megjelenítése gombra.

A ÖSSZEFŰZ módszert és a végrehajtás ideje nagy részét fel String.Concat kell megjelennie.

![A jelentés elemzése][12]

Ebben a cikkben a karakterlánc-összefűzési kódot adott hozzá, ha megjelenik egy figyelmeztetés a feladatlistában a. Is, hogy nincs-e, amelyek jönnek létre és értékesíteni száma miatt van szemétgyűjtés aránytalanul figyelmeztetés jelenhet meg.

![Teljesítmény-figyelmeztetések][14]

## <a name="4-make-changes-and-compare-performance"></a>4: módosításokat, és a teljesítmény összehasonlítása
Össze is hasonlíthatja a teljesítmény előtt és után a kód megváltoztatására.  Állítsa le a futó folyamat, és cserélje le a karakterlánc-összefűzési művelet StringBuilder használatát a kód szerkesztése:

```csharp
public static string Concatenate(int number)
{
    int count;
    System.Text.StringBuilder builder = new System.Text.StringBuilder("");
    for (count = 0; count < number; count++)
    {
        builder.Append("\n" + count.ToString());
    }
    return builder.ToString();
}
```

Hajtsa végre egy másik teljesítmény futtassa, és hasonlítsa össze a teljesítményt. A teljesítmény Explorer az fut ugyanabban a munkamenetben, ha akkor is csak válassza ki mindkét jelentés, a helyi menüben, és válassza **összehasonlítása Teljesítményjelentések készítésére**. Ha meg szeretné összehasonlítani egy másik teljesítmény munkamenetben futtató, nyissa meg a **elemzés** menüben, és válassza **összehasonlítása Teljesítményjelentések készítésére**. A megjelenő párbeszédpanelen adja meg mindkét fájlt.

![Teljesítménybeállítás jelentések összehasonlítása][15]

A jelentések jelölje ki a két fut közötti különbségeket.

![Összehasonlító jelentés][16]

Gratulálunk! Ön megtette az első lépéseket a profiler az.

## <a name="troubleshooting"></a>Hibaelhárítás
* Győződjön meg arról, hogy kiadott buildjét vannak profilkészítés és az Indítás hibakeresés nélkül.
* Ha az Attach/Detach beállítás nincs engedélyezve a Profiler menü, futtassa a teljesítmény varázslót.
* Az alkalmazás állapotának megtekintéséhez használja a Compute Emulator felhasználói felületén. 
* Ha problémába ütközik az emulátorban alkalmazások indításával, vagy állítsa a profiler csatolása a compute emulator le és indítsa újra. Ha ez nem oldja meg a problémát, próbálja meg újraindítani. Ez a probléma akkor fordulhat elő, ha használja a Compute Emulator felfüggesztése és eltávolítása a futó üzemelő példányok.
* Ha már használt a profilkészítés parancsokhoz a parancssorból, különösen a globális beállítások, győződjön meg arról, hogy VSPerfClrEnv /globaloff lett meghívva, és hogy VsPerfMon.exe le lett állítva.
* Ha az üzenet jelenik meg, amikor a mintavételi, "PRF0025: nem történt adatgyűjtés," Ellenőrizze, hogy a csatolt folyamat CPU-tevékenységet tartalmaz. Az alkalmazásokat, amelyek nem végeznek számítási munka lehet, hogy nem tudott mintavételi adatokat.  Lehetőség arra is, hogy a folyamat kilépett bármely mintavételi elkészítése előtt. Ellenőrizze, hogy a Run metódus egy szerepkörhöz, a profilkészítés nem zárja be.

## <a name="next-steps"></a>További lépések
Azure bináris az emulátorban való műszerezéséről nem támogatja a Visual Studio profilerével, de ha szeretné tesztelni a lefoglalt memória, amikor a profilkészítés is kiválasztja ezt a lehetőséget. Azt is beállíthatja egyidejűségi profilkészítés segítségével határozza meg, hogy szálak oly alkalommal használják a zárolások vannak, vagy réteg kapcsolati adatainak összegyűjtése, amely segítségével nyomon követheti a teljesítménybeli problémákat, az alkalmazás a rétegek közötti interakció során leggyakrabban gyakran között az adatszint és a egy feldolgozói szerepkörben.  Megtekintheti az alkalmazás által létrehozott adatbázis-lekérdezések és a profilkészítési adatok segítségével javíthatja az adatbázis használatát. Réteg interakció profilkészítés kapcsolatos információkért tekintse meg a következő blogbejegyzésben: [forgatókönyv: a csomag kapcsolati Profiler használata a Visual Studio Team System 2010][3].

[1]: https://docs.microsoft.com/azure/application-insights/app-insights-profiler
[2]: http://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: http://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png
