---
title: Felhőszolgáltatás profilalkotása helyileg a számítási emulátorban | Microsoft dokumentumok
services: cloud-services
description: A felhőszolgáltatások teljesítményproblémáinak vizsgálata a Visual Studio profilkészítővel
documentationcenter: ''
author: mikejo
manager: jillfra
editor: ''
tags: ''
ms.assetid: 25e40bf3-eea0-4b0b-9f4a-91ffe797f6c3
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/18/2016
ms.author: mikejo
ms.openlocfilehash: 21270d3c7143ce063ffe30d939368b9813e9072e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70094098"
---
# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Egy felhőszolgáltatás teljesítményének tesztelése helyileg az Azure Compute Emulátorban a Visual Studio Profiler használatával
A felhőszolgáltatások teljesítményének teszteléséhez számos eszköz és technika áll rendelkezésre.
Amikor közzétesz egy felhőszolgáltatást az Azure-ban, beállíthatja, hogy a Visual Studio összegyűjtse a profilkészítési adatokat, majd helyileg elemezze [azokat, az Azure-alkalmazások profilkészítése című][1]részben leírtak szerint.
A diagnosztika segítségével számos teljesítményszámlálót is nyomon követhet, az [Azure teljesítményszámlálóinak használata című részében leírtak szerint.][2]
Előfordulhat, hogy szeretné az alkalmazást helyileg profilozni a számítási emulátor üzembe helyezése előtt a felhőben.

Ez a cikk a CPU-mintavételezési módszer profilkészítés, amely helyileg elvégezhető az emulátor. Cpu mintavételezés egy módszer a profilalkotás, hogy nem túl tolakodó. Egy kijelölt mintavételi időközzel a profiler pillanatképet készít a hívásveremről. Az adatokat egy adott időszakban gyűjtik, és egy jelentésben mutatják be. Ez a profilkészítési módszer általában azt jelzi, hogy egy számításigényes alkalmazásban a CPU-munka nagy része hol történik.  Ez lehetőséget ad arra, hogy összpontosítson a "forró elérési út", ahol az alkalmazás kiadások a legtöbb időt.

## <a name="1-configure-visual-studio-for-profiling"></a>1: A Visual Studio konfigurálása profilkészítéshez
Először is van néhány Visual Studio-beállítási lehetőség, amelyek hasznosak lehetnek a profilkészítéssorán. A profilkészítési jelentések értelmének érdekében az alkalmazáshoz szimbólumokra (.pdb fájlokra), valamint a rendszertárak szimbólumaira van szükség. Győződjön meg arról, hogy a rendelkezésre álló szimbólumkiszolgálókra hivatkozik. Ehhez válassza a Visual Studio **Eszközök** menüjének **Beállítások**parancsát, majd a **Hibakeresés**menü **Szimbólumok parancsot.** Győződjön meg arról, hogy a Microsoft szimbólumkiszolgálói a **Szimbólumfájl (.pdb) helyek**csoportban találhatók.  Hivatkozhat arra https://referencesource.microsoft.com/symbolsis , hogy további szimbólumfájlokkal is rendelkezhet.

![Szimbólum beállításai][4]

Ha szükséges, egyszerűsítheti a profiler által létrehozott jelentéseket a Just My Code beállításával. Ha a Just My Code engedélyezve van, a függvényhívási halmok leegyszerűsödnek, így a függvénytárak teljes egészében belső hívásai és a .NET keretrendszer rejtve maradnak a jelentésekből. Az **Eszközök menüben** válassza a **Beállítások parancsot.** Ezután bontsa ki a **Teljesítményeszközök** csomópontot, és válassza az **Általános**lehetőséget. Jelölje be a Csak a **saját kód engedélyezése jelölőnégyzetet profilozó jelentésekhez.**

![Csak a Saját kód beállításai][17]

Ezeket az utasításokat használhatja egy meglévő projekttel vagy egy új projekttel.  Ha új projektet hoz létre az alább leírt technikák kipróbálásához, válasszon egy C# Azure Cloud Service-projektet, és válasszon egy **webes szerepkört** és egy **Azure Cloud Service** **feldolgozói szerepkört.**

![Az Azure Cloud Service projektszerepkörei][5]

Például adjon hozzá néhány kódot a projekthez, amely sok időt vesz igénybe, és nyilvánvaló teljesítményproblémát mutat be. Adja hozzá például a következő kódot egy feldolgozói szerepkör-projekthez:

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

Hívja meg ezt a kódot a munkavégző szerepkör RoleEntryPoint-származtatott osztályának RunAsync metódusából. (Hagyja figyelmen kívül a szinkron módon futó metódusra vonatkozó figyelmeztetést.)

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

A felhőszolgáltatás helyi legtávolabbi létrehozása és futtatása hibakeresés nélkül (Ctrl+F5) a megoldás konfigurációjának **felszabadítása**beállításával. Ez biztosítja, hogy az összes fájl és mappa létre jön az alkalmazás helyi futtatásához, és biztosítja az összes emulátor elindítását. Indítsa el a számítási emulátor felhasználói felületét a tálcáról, és ellenőrizze, hogy fut-e a feldolgozói szerepkör.

## <a name="2-attach-to-a-process"></a>2: Csatolás egy folyamathoz
Ahelyett, hogy az alkalmazást a Visual Studio 2010 IDE-ből indítané el, a profilozót egy futó folyamathoz kell csatolnia. 

Ha a profilozót egy folyamathoz szeretné **csatolni,** válassza az Elemzés menü **Profilozó** és **Csatolás/Leválasztás**parancsot.

![Profil csatolása beállítás][6]

Feldolgozói szerepkör esetén keresse meg a WaWorkerHost.exe folyamatot.

![WaWorkerHost folyamat][7]

Ha a projektmappa hálózati meghajtón van, a profilozó megfogja kérni, hogy adjon meg egy másik helyet a profilkészítési jelentések mentéséhez.

 A Web szerepkörhöz a WaIISHost.exe fájlhoz csatolva is csatolhat.
Ha az alkalmazásban több feldolgozói szerepkör-folyamat van, a processID segítségével kell megkülönböztetni őket. A processID programozott módon lekérdezhető a Folyamat objektum elérésével. Ha például hozzáadja ezt a kódot a Szerepkör-bevitelipont-eredetű osztály Futtatás metódusához egy szerepkörben, megnézheti a naplót a Számítási emulátor felhasználói felületen, hogy megtudja, milyen folyamathoz csatlakozzon.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

A napló megtekintéséhez indítsa el a számítási emulátor felhasználói felületét.

![A számítási emulátor felhasználói felületének indítása][8]

Nyissa meg a feldolgozói szerepkör naplókonzolablakát a Számítási emulátor felhasználói felületén a konzolablak címsorára kattintva. A folyamatazonosító a naplóban látható.

![Folyamatazonosító megtekintése][9]

Az egyik már csatolt, hajtsa végre a lépéseket az alkalmazás felhasználói felületén (ha szükséges), hogy reprodukálja a forgatókönyvet.

Ha le szeretné állítani a profilkészítést, válassza a **Profilkészítés leállítása hivatkozást.**

![Profilkészítés leállítása beállítás][10]

## <a name="3-view-performance-reports"></a>3: Teljesítményjelentések megtekintése
Megjelenik az alkalmazás teljesítményjelentése.

Ezen a ponton a profilozó leállítja a végrehajtást, adatokat ment egy .vsp fájlba, és megjelenít egy jelentést, amely az adatok elemzését mutatja.

![Profiler jelentés][11]

Ha a String.wstrcpy a Hot Path-ban jelenik meg, kattintson a Just My Code gombra a nézet módosításához, hogy csak a felhasználói kódot jelenítse meg.  Ha a String.Concat, próbálja meg az Összes kód megjelenítése gombot.

Látnia kell a Concatenate metódust, és a String.Concat a végrehajtási idő nagy részét veszi fel.

![A jelentés elemzése][12]

Ha a cikkben hozzáadta a karakterlánc-összefűzési kódot, akkor a feladatlistában figyelmeztetésnek kell lennie. Azt is láthatja, hogy a szemétgyűjtés túl nagy mennyiségű, ami a létrehozott és ártalmatlanított karakterláncok száma miatt jelenik meg.

![Teljesítményre vonatkozó figyelmeztetések][14]

## <a name="4-make-changes-and-compare-performance"></a>4: Módosítások végrehajtása és teljesítmény összehasonlítása
A teljesítmény a kódmódosítás előtt és után is összehasonlítható.  Állítsa le a futó folyamatot, és szerkesztse a kódot, hogy a karakterlánc-összefűzési műveletet a StringBuilder használatával cserélje le:

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

Végezze el a teljesítmény futtatását, majd hasonlítsa össze a teljesítményt. A Teljesítménykezelőben, ha a futtatások ugyanabban a munkamenetben vannak, csak jelölje ki mindkét jelentést, nyissa meg a helyi menüt, és válassza a **Teljesítményjelentések összehasonlítása parancsot.** Ha egy másik teljesítménymunkamenetben futtatni kívánt, nyissa meg az **Elemzés menüt,** és válassza **a Teljesítményjelentések összehasonlítása parancsot.** Adja meg mindkét fájlt a megjelenő párbeszédpanelen.

![Teljesítményjelentések összehasonlítása beállítás][15]

A jelentések kiemelik a két futtatás közötti különbségeket.

![Összehasonlító jelentés][16]

Gratulálunk! A profilozóval kezdted.

## <a name="troubleshooting"></a>Hibaelhárítás
* Győződjön meg arról, hogy egy kiadási buildet profiloz, és hibakeresés nélkül kezdi el.
* Ha a Profilozó menüben nincs engedélyezve az Csatolás/leválasztás lehetőség, futtassa a Teljesítmény varázslót.
* A számítási emulátor felhasználói felület használatával megtekintheti az alkalmazás állapotát. 
* Ha problémái vannak az alkalmazások indításával az emulátorban, vagy csatlakoztatják a profilozót, állítsa le a számítási emulátort, és indítsa újra. Ha ez nem oldja meg a problémát, próbálkozzon az újraindításlehetőséggel. Ez a probléma akkor fordulhat elő, ha a számítási emulátor használatával függeszti fel és távolítja el a futó központi telepítéseket.
* Ha a parancssorból, különösen a globális beállításokból származó profilkészítési parancsokbármelyikét használta, győződjön meg arról, hogy a VSPerfClrEnv /globaloff hívása meg van,és hogy a VsPerfMon.exe le van állítva.
* Ha mintavételkor a "PRF0025: Nincs adat gyűjtés" üzenet jelenik meg, ellenőrizze, hogy a hozzácsatolt folyamat rendelkezik-e processzortevékenységgel. Előfordulhat, hogy azok az alkalmazások, amelyek nem végeznek számítási munkát, előfordulhat, hogy nem hoznak létre mintavételi adatokat.  Az is lehetséges, hogy a folyamat kilépett, mielőtt bármilyen mintavételi került sor. Ellenőrizze, hogy a profilkészítésáltal futtatott metódus nem szűnik-e meg.

## <a name="next-steps"></a>Következő lépések
Az Azure bináris fájlok emulátorban történő instrumentálása nem támogatott a Visual Studio profilkezelőben, de ha tesztelni szeretné a memóriafoglalást, ezt a beállítást választhatja profilkészítéskor. Kiválaszthatja az egyidejűségprofilozást is, amely segít meghatározni, hogy a szálak a zárolások ért versengő időt vagy a réteginterakciós profilalkotást használják-e, ami segít a teljesítményproblémák nyomon követésében az alkalmazás szintjei között, a legtöbb gyakran az adatszint és a feldolgozói szerepkör között.  Megtekintheti az alkalmazás által létrehozott adatbázis-lekérdezéseket, és a profilkészítési adatok segítségével javíthatja az adatbázis használatát. A réteginterakciós profilalkotásról a következő blogbejegyzésben talál: [A Réteginterakciós profilkészítő használata a Visual Studio Team System 2010-ben.][3]

[1]: https://docs.microsoft.com/azure/application-insights/app-insights-profiler
[2]: https://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: https://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
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
