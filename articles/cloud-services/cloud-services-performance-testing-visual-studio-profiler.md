---
title: A Felhőszolgáltatás helyi a Compute Emulator profilkészítési |} Microsoft Docs
services: cloud-services
description: A Visual Studio profilkészítőhöz tartozó vizsgálja meg a teljesítménnyel kapcsolatos problémák, a cloud services csomag
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
ms.openlocfilehash: 8ff7b88a3086488ab669288687c274237ca30b47
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Az Azure Compute Emulator használatával a Visual Studio Profilkészítő helyileg egy felhőalapú szolgáltatás teljesítményének tesztelése
Számos különféle eszközöket és technikákat tesztelési felhőalapú szolgáltatások érhetők el.
Egy felhőalapú szolgáltatás az Azure-ba való közzétételekor a profilkészítési adatokat gyűjteni, és majd elemezze a Visual Studio lehet a helyi [Azure alkalmazás profilkészítési][1].
Használhatja diagnostics teljesítményszámlálók, számos nyomon leírtak szerint [teljesítményszámlálók segítségével az Azure-ban][2].
Érdemes azt is, a profilhoz, az alkalmazás helyi a compute emulator a felhőben való üzembe helyezése előtt.

Ez a cikk a profilkészítés CPU-mintavételezési módszerét ismerteti, amely helyben végezhető el az emulátorral. CPU mintavételi egy metódust, amely profilkészítési nem nagyon zavaró. A kijelölt mintavételi történik a Profilkészítő pillanatképet készít a a hívási verem. Az adatok gyűjtése egy meghatározott időtartamra vonatkozóan, és a jelentésben látható. Ez a módszer a profilkészítési általában azt jelzi, ha egy számításilag intenzív alkalmazásban a CPU munka nagyobb része történik-e.  Ez lehetőséget ad a "gyors path" összpontosíthat ahol az alkalmazás a legtöbb időt tölt.

## <a name="1-configure-visual-studio-for-profiling"></a>1: profilkészítési a Visual Studio konfigurálása
Először néhány Visual Studio konfigurálására lehetőség áll rendelkezésre, előfordulhat, hogy lehet hasznos, amikor a profilkészítési. A profilkészítési jelentések értelme, szüksége lesz szimbólumok (.pdb fájlok) az alkalmazás és a is rendszer könyvtárak szimbólumait. Érdemes, győződjön meg arról, hogy a rendelkezésre álló szimbólum kiszolgálók hivatkozik. Ehhez a a **eszközök** Visual Studio menüjében válassza a **beállítások**, majd válassza a **hibakeresés**, majd **szimbólumok**. Győződjön meg arról, hogy a Microsoft szimbólum kiszolgálók megtalálható-e **szimbólum (.pdb) fájlhelyek**.  Is hivatkozhat http://referencesource.microsoft.com/symbols, amely előfordulhat, hogy További szimbólumfájlok.

![Szimbólum beállításai][4]

Ha szükséges, egyszerűbbé teheti a jelentések, amely a Profilkészítő hoz létre úgy, hogy csak saját kód. Csak saját kód engedélyezve van a függvény hívási verem egyszerűsítettek, hogy teljesen belső szalagtárak és a .NET-keretrendszer hívások rejtve maradnak az a jelentések. Az a **eszközök** menüben válasszon **beállítások**. Majd bontsa ki a **teljesítmény eszközök** csomópont, és válassza a **általános**. Jelölje be a **engedélyezése csak saját kód Profilkészítő jelentések**.

![Csak saját kód beállítása][17]

Ezek az utasítások is használhatja, egy meglévő projektjébe vagy egy új projektet.  Ha hoz létre egy új projektet az alább ismertetett technikák kipróbálásához, válasszon egy C# **Azure Cloud Service** projektre, és válassza ki a **webes szerepkör** és egy **feldolgozói szerepkör**.

![Azure Cloud Service projekt szerepkörök][5]

Célra, például néhány kódot hozzáadása a projekthez, amely sok időt vesz igénybe, és bemutatja a nyilvánvaló teljesítmény kapcsolatos problémára. Például adja hozzá a következő kódot a feldolgozói szerepkör projekt:

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

Ez a kód hívja a RunAsync metódusában a feldolgozói szerepkör RoleEntryPoint származtatott osztályban. (Figyelmen kívül hagyhatja a figyelmeztetést jelenít meg a metódus szinkron módon fut.)

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

Hozza létre, és futtassa a felhőalapú szolgáltatás helyileg nélkül hibakeresés (Ctrl + F5), a megoldás konfiguráció beállítva a **kiadás**. Ez biztosítja, hogy minden fájlja és mappája jönnek létre alkalmazást futtató helyi, és biztosítja, hogy az emulátorok vannak-e indítva. Indítsa el a Compute Emulator felhasználói felületén a tálcáról annak ellenőrzéséhez, hogy fut-e a feldolgozói szerepkör.

## <a name="2-attach-to-a-process"></a>2: a folyamatok csatolása
Helyett profilkészítési az alkalmazást a Visual Studio 2010 IDE a elindításával, hozzá kell rendelni a Profilkészítő futó folyamatokhoz. 

A Profilkészítő csatolni egy folyamatot, a a **elemzés** menüben válasszon **Profilkészítő** és **Attach/Detach**.

![Profil beállítás csatolása][6]

A feldolgozói szerepkör esetében a WaWorkerHost.exe folyamat található.

![WaWorkerHost folyamat][7]

Ha a projektmappa egy hálózati meghajtón van, a Profilkészítő ekkor megkérdezi, hogy adja meg a másik helyre a profilkészítési jelentések.

 Is csatolhat egy webes szerepkör WaIISHost.exe való csatolásával.
Ha több szerepkör folyamata az alkalmazásban, a folyamatazonosító segítségével különböztetheti meg szeretné. A Process objektum elérésével programozott módon lekérdezheti a folyamatazonosító. Például ha ezt a kódot ad hozzá a Run metódus szerepkör RoleEntryPoint származtatott osztály, vessen egy pillantást a naplót a Compute Emulator felhasználói felületén tudni, melyik folyamat való csatlakozáshoz.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

A napló megtekintéséhez indítsa el a Compute Emulator felhasználói felületén.

![A Compute Emulator felhasználói felületének indítása][8]

A Compute Emulator felhasználói felületén a feldolgozói szerepkör napló konzolablakban nyissa meg a kattintva a konzol ablakának címsorában. A folyamat azonosítója a naplóban tekintheti meg.

![Nézet folyamat azonosítója][9]

Egy csatolt, hajtsa végre a lépéseket az alkalmazás felhasználói felületén (ha szükséges) reprodukálásához szükséges a forgatókönyvet.

Amikor le kívánja állítani a profilkészítési, válassza a **Profilkészítés leállítása** hivatkozásra.

![Beállítás Profilkészítés leállítása][10]

## <a name="3-view-performance-reports"></a>3: teljesítmény jelentések megtekintése
A rendszerteljesítmény-jelentés az alkalmazás jelenik meg.

Ezen a ponton a Profilkészítő végrehajtása megszakad, adatok .vsp fájlba menti, és ezek az adatok elemzése a jelentés jeleníti meg.

![Profilkészítő jelentés][11]

Ha a működés közbeni elérési úton String.wstrcpy megtekintéséhez kattintson a csak saját kód megjelenítése csak a felhasználói kód a nézet módosításához.  Ha megjelenik a String.Concat, nyomja le az összes kódot megjelenítése gombra.

Meg kell jelennie a ÖSSZEFŰZ metódus és String.Concat fel a végrehajtási idő nagy részét.

![Jelentése][12]

Ebben a cikkben hozzáadott a karakterláncot kapott kódot, ha megjelenik egy figyelmeztetés a feladatlistán levő ehhez. Is megjelenik egy figyelmeztetés, hogy nincs-e aránytalanul szemétgyűjtés, ami miatt a létrehozott és használaton karakterláncok száma.

![Teljesítmény-figyelmeztetések][14]

## <a name="4-make-changes-and-compare-performance"></a>4: módosításokat, és hasonlítsa össze a teljesítmény
A teljesítmény előtt és után kódváltoztatást is összehasonlíthatja.  Állítsa le a futó folyamattal, és cserélje le a karakterlánc-összefűzés művelet StringBuilder használatát a kód szerkesztése:

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

Hajtsa végre egy másik teljesítmény futtassa, és hasonlítsa össze a teljesítmény. A teljesítmény Explorer az fut ugyanabban a munkamenetben, ha akkor is csak válassza ki mindkét jelentést, a helyi menü megnyitásához, és válassza **összehasonlítása teljesítmény jelentések**. Egy másik teljesítmény munkamenetben futtató összehasonlítandó, nyissa meg a **elemzés** menüben, és válassza **összehasonlítása teljesítmény jelentések**. A megjelenő párbeszédpanelen adja meg a fájlt.

![Jelentések teljesítménybeállítását összehasonlítása][15]

A jelentések jelölje ki a két fut közötti különbséget.

![Összehasonlító jelentés][16]

Gratulálunk! Ön a profilkészítőhöz tartozó megtette az első lépéseket.

## <a name="troubleshooting"></a>Hibaelhárítás
* Győződjön meg arról, amelyek profilkészítési kiadott buildjét és hibakeresés nélkül indítsa el.
* Ha az Attach/Detach beállítás nincs engedélyezve a Profilkészítő menüben, a teljesítmény varázsló elindítása.
* A Compute Emulator felhasználói felületén segítségével az alkalmazás állapotának megtekintése. 
* Ha problémába ütközik az emulátorban alkalmazások elindítása vagy csatlakoztatása a Profilkészítő, állítsa le a compute emulator, és indítsa újra. Ha ez nem oldja meg a problémát, próbálja meg újraindítani. Ez a probléma akkor fordulhat elő, a Compute Emulator felfüggesztése, és távolítsa el a futó központi telepítések használatakor.
* Ha a parancssorból, különösen a globális beállítások profilkészítési parancsok használt győződjön meg arról, hogy VSPerfClrEnv /globaloff meghívása és, hogy VsPerfMon.exe le lett állítva.
* Ha az üzenet jelenik meg, amikor a mintavételi, "PRF0025: nem történt adatgyűjtés," Ellenőrizze, hogy a csatolt folyamat CPU-tevékenység van-e. Előfordulhat, hogy alkalmazásokat, amelyek nem tűnik, hogy minden számítási munka tudott mintavételi adatokat.  Lehetőség arra is, hogy a folyamat kilépett a előtt minden olyan végezhető el. Ellenőrizze, hogy, hogy a Run metódus egy szerepkörhöz vannak profilkészítési nem ér véget.

## <a name="next-steps"></a>Következő lépések
A Visual Studio Profilkészítő nem támogatja az Azure bináris fájljai az emulátorban tagolása, de ha memóriafoglalás vizsgálni kívánt, ez a lehetőség választhatja, ha profilkészítési. Dönthet úgy is párhuzamossági profilkészítési, segítségével határozza meg, hogy szálak jelentős időt használják a zárolások vannak, vagy profilkészítési interakció, réteg segítségével nyomon követésük Ez teljesítményproblémákat okozhat, ha a fizetősökbe, az alkalmazások közötti kommunikáció során leggyakrabban gyakran között az adatszint és egy feldolgozói szerepkört.  Lehet, amely az alkalmazás létrehozza az adatbázis-lekérdezések megtekintése és a profilkészítési adatokat használni a használatára az adatbázis javítása érdekében. Réteg interakció profilkészítési kapcsolatos információkért lásd a következő blogbejegyzésben [forgatókönyv: a réteg interakció Profiler használatával a Visual Studio Team System 2010][3].

[1]: http://msdn.microsoft.com/library/azure/hh369930.aspx
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
