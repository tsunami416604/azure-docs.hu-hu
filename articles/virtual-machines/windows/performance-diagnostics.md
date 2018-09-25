---
title: Teljesítménybeli problémák diagnosztizálása az Azure-beli virtuális gépek |} A Microsoft Docs
description: A Windows vezet be a teljesítmény az Azure Diagnostics.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: przlplx
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 9/20/2018
ms.author: anandh
ms.openlocfilehash: 587540430dc4089973186c2862f402ccde73df65
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047642"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Azure-beli virtuális gépek a teljesítménybeli problémák diagnosztizálása

A teljesítmény diagnosztikai eszköz segítségével, amely hatással lehet egy Windows virtuális gép (VM) teljesítménnyel kapcsolatos problémáinak elhárítása. Támogatott hibaelhárítási forgatókönyvek között megtalálható az ismert problémák és ajánlott eljárások és összetett problémákat, amelyek lassú virtuális gép teljesítmény vagy a magas Processzorhasználat, lemezterület vagy memória használatának gyors ellenőrzésére. 

Futtathatja teljesítménybeli problémák diagnosztizálása közvetlenül az Azure Portalról, ahol is áttekintheti insights és a egy jelentést, a különböző naplókat, sokrétű konfigurációs és diagnosztikai adatokat. Azt javasoljuk, hogy Ön teljesítmény diagnosztika futtatása előtt a kapcsolatot a Microsoft Support, tekintse át az elemzések és a diagnosztikai adatokat.

> [!NOTE]
> Teljesítménybeli problémák diagnosztizálása jelenleg támogatott a Windows virtuális gépek, amelyeken SDK a .NET 4.5-ös verzióját vagy újabb verzióját. Teljesítménybeli problémák diagnosztizálása a klasszikus virtuális gépek futtatásához lépéseiért lásd: [Azure teljesítmény diagnosztikai Virtuálisgép-bővítménnyel](performance-diagnostics-vm-extension.md).

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
Windows 10, Windows 8, Windows 8 Enterprise, Windows 8 Pro, Windows 8.1, Windows Server 2016, Windows Server 2012, Windows Server 2012 Datacenter, Windows Server 2012 R2, Windows Server 2012 R2 Datacenter, Windows Server 2012 R2 Standard, Windows Server 2012-ben Standard, Windows Server 2008 R2, Windows Server 2008 R2 Datacenter, Windows Server 2008 R2 Enterprise, Windows Server 2008 R2 Foundation, a Windows Server 2008 R2 SP1, Windows Server 2008 R2 Standard.

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>Telepítse és futtassa a teljesítménybeli problémák diagnosztizálása a virtuális Gépen
Teljesítménybeli problémák diagnosztizálása telepíti a VM-bővítmény, amely egy diagnosztikai eszköz nevű [PerfInsights](https://aka.ms/perfinsights). Telepítse és futtassa a teljesítménybeli problémák diagnosztizálása, kövesse az alábbi lépéseket:
1)  Válassza a bal oldali oszlopban, a parancsok, **virtuális gépek**.
2)  Egy virtuális gép neve a listából válassza ki a diagnosztika futtatása kívánt virtuális Gépet.
3)  Válassza ki a jobb oldali oszlopban, a parancsok, **teljesítménybeli problémák diagnosztizálása**.

    ![Képernyőkép az Azure portal, a telepítés teljesítmény diagnosztikai gomb kiemelésével](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > A képernyőfelvételen a virtuális gép neve panel el van rejtve.
4) Válassza ki a storage-fiók (nem kötelező)

    Ha azt szeretné, egyetlen tárfiókban használata a teljesítmény diagnosztikai eredmények tárolására több virtuális gép, tárfiók kattintva kiválaszthatja a **beállítások** gomb az eszköztáron. Kattintson a **OK** gombra, miután a storage-fiókot.

    Ha nem ad meg storage-fiók, egy új storage-fiók alapértelmezés szerint létrejön.

    ![Képernyőkép-teljesítményét diagnosztikai panelen, a beállítások-eszköztárgomb kiemelésével](media/performance-diagnostics/settings-button.png)

    ![Tárolási fiók kiválasztása a teljesítmény diagnosztikai beállítások panel képernyőképe](media/performance-diagnostics/select-storage-account.png)

5) Válassza ki a **teljesítménybeli problémák diagnosztizálása telepítése** gombra.
6) Válassza ki a **diagnosztika futtatása** jelölőnégyzetet, ha szeretné futtatni a diagnosztikát, a telepítés befejezése után. A kijelölést, ha tudják, a teljesítmény elemzése a forgatókönyv és a kapcsolódó beállítások kiválasztásához.

    ![Képernyőkép a teljesítménybeli problémák diagnosztizálása telepítése gomb](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>Válassza ki egy elemzési forgatókönyv futtatása

Az alábbi elemzési forgatókönyvek az Azure Portalon érhetők el. Válassza ki az elemzést, a teljesítménybeli problémát tapasztal, attól függően. Válassza ki a időtartam és a nyomkövetési beállítás az elemzéshez szükséges.

* **Gyors teljesítmény elemzése**  
    Ellenőrzi az ismert problémákat, és elemzi az ajánlott eljárások a diagnosztikai adatokat gyűjt. Ez az elemzés futtatása néhány percet vesz igénybe. [További információ](https://aka.ms/perfinsights/quick)

* **Teljesítményelemzés**  
    Tartalmazza az összes ellenőrzés gyors teljesítmény elemzése és magas erőforrás-használat figyelésére szolgál. Ez a verzió használatával kapcsolatos általános problémák elhárítása, például a magas CPU, memória és lemezterület. Ez az elemzés 15 percig, attól függően, a kijelölt időintervallumban 30 másodpercet vesz igénybe. [További információ](https://aka.ms/perfinsights/vmslow) 
    
* **Speciális teljesítményelemzése**  
    Tartalmazza az összes ellenőrzés teljesítmény elemzése, és a egy vagy több, a nyomokra kigyűjti, amelynél a következő szakaszokban szereplő. Ez a forgatókönyv segítségével további nyomkövetések kapcsolatos összetett problémák elhárítása. Ez a forgatókönyv futtatása hosszabb ideig megnöveli a diagnosztikai kimenetet, a virtuális gép és a nyomkövetési beállítás kiválasztott méretétől függően a teljes méretét. Ez az elemzés 15 percig szeretné futtatni, attól függően, a kijelölt időintervallumban 30 másodpercet vesz igénybe. [További információ](https://aka.ms/perfinsights/advanced) 
    
* **Az Azure Files-elemzés**  
    Tartalmazza az összes ellenőrzés teljesítmény elemzése, és rögzíti a hálózati nyomkövetés és SMB teljesítményszámlálók. Ez a forgatókönyv segítségével az Azure files-teljesítményi hibák elhárítása. Ez az elemzés 15 percig szeretné futtatni, attól függően, a kijelölt időintervallumban 30 másodpercet vesz igénybe. [További információ](https://aka.ms/perfinsights/azurefiles)


![Diagnosztika panel képernyőképe a Futtatás belüli teljesítmény diagnosztikai panelen](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>Adja meg a jelenség (nem kötelező)
Válassza ki a telepítendő, előre kiválasztott tüneteket a listából, vagy adja hozzá a jelenség új. Ez segít a későbbiekben az elemzés továbbfejlesztésében. 

### <a name="provide-support-request-number-if-available-optional"></a>Adja meg a támogatási kérelmek száma, ha elérhető (nem kötelező)
Ha a Microsoft támogatási szakember egy meglévő támogatási jegyet a dolgozik, adja meg a támogatási jegy száma. 

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>Tekintse át az adatvédelmi elveket és jogi feltételeket, és jelölje be a jelölőnégyzetet, gombra annak megerősítéséhez (kötelező)
A diagnosztika futtatása, fogadja el a jogi feltételeket, és fogadja el az adatvédelmi szabályzatát.

### <a name="select-ok-to-run-the-diagnostics"></a>Kattintson az OK gombra a diagnosztika futtatása 
Teljesítménybeli problémák diagnosztizálása telepítése elindul egy értesítés is megjelenik. A telepítés befejezése után megjelenik egy értesítés, amely azt jelzi, hogy a telepítés sikeres. A kiválasztott elemzés ezután futtassa a megadott időtartam számára. Ez akkor lehet ideje Reprodukálja a teljesítménnyel kapcsolatos problémák, hogy a diagnosztikai adatok rögzíthetők a megfelelő időben. 

Az elemzés befejezése után a következő elemek töltenek fel az Azure-táblák és a egy bináris nagy objektumtárat (BLOBTÁRAT) tárolót a megadott tárfiók:

*   Az elemzések és a Futtatás kapcsolatos információk
*   Egy kimeneti tömörített (.zip) fájlt (nevű **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.zip**), amely tartalmazza a naplófájlok
*   Egy HTML-jelentés

A feltöltés után új diagnosztikai jelentés szerepel az Azure Portalon.

![Képernyőkép a diagnosztikai jelentést a teljesítmény diagnosztikai panelen listája](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>Teljesítmény a diagnosztikai beállítások módosítása
Használja a **beállítások** eszköztár gombra kattintva módosíthatja a tárfiókot, ahol a diagnosztikai elemzéseket, és a kimeneti tárolhatók. Teljesítménybeli problémák diagnosztizálása használó több virtuális gépek ugyanazt a tárfiókot is használhat. Amikor módosítja a tárfiókot, a régi jelentést és elemzést nem törlődnek. Azonban azok már megjelennek a diagnosztikai jelentések listáját. 

## <a name="review-insights-and-performance-diagnostics-report"></a>Elemzések és a diagnosztika teljesítményjelentés áttekintése
Minden egyes Futtatás diagnosztikai elemzéseket és javaslatok, érintett erőforrások, naplófájlok és más részletes diagnosztikai adatokat gyűjtött listáját, valamint az offline megtekintésre a jelentést tartalmaz. Az összes gyűjtött diagnosztikai adatok teljes listáját lásd: [milyen típusú információkat gyűjti a PerfInsights?](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/how-to-use-perfinsights#what-kind-of-information-is-collected-by-perfinsights) 

### <a name="select-a-performance-diagnostics-report"></a>Válassza ki a diagnosztikai teljesítményjelentés
A diagnosztikai jelentések listája segítségével futtatott diagnosztikai jelentések találja. A lista tartalmazza az elemzés használt adatait, amelyek található és a hatás szintre. Válassza ki egy sort a további részletek megtekintéséhez.

![Képernyőkép a teljesítmény diagnosztikai panelen egy diagnosztikai jelentés kiválasztása](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>Tekintse át a diagnosztika teljesítményjelentés
Minden egyes teljesítményjelentés diagnosztikai több elemzések és tartalmazhat magas, közepes vagy alacsony hatás mértékét jelzi. Minden egyes insight csökkentheti a potenciálisan veszélyes ajánlásokat is tartalmaz. Insights a könnyen kezelhető szűrési vannak csoportosítva. 

Hatás szintek jelölik az esetleges teljesítménybeli problémák, például a Virtual Network szolgáltatás hibás, ismert problémákat, tényezők alapján, vagy más felhasználók által jelentett hibákat. Ön nem még léptek fel egy vagy több, a felsorolt problémák. Előfordulhat például, az SQL naplófájlokat és az adatok ugyanazon a lemezen található adatbázisfájlok. Ez a feltétel rendelkezik magas potenciális szűk keresztmetszeteket és teljesítménnyel kapcsolatos egyéb problémák, ha az adatbázis használattal magas, mivel előfordulhat, hogy nem láthatja, hogy egy probléma, ha a használat értéke alacsony.

![Képernyőkép-teljesítményét diagnosztikai jelentés áttekintés panelen](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>Diagnosztikai elemzéseket és javaslatok áttekintése
Kiválaszthat egy elemzést az érintett erőforrások, a javasolt megoldások és a referencia-kapcsolatokra vonatkozó további részletek megtekintéséhez. 

![Képernyőkép egy diagnosztikai insight teljesítmény részletei](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>Töltse le és tekintse át a teljes diagnosztikai teljesítményjelentés
Használhatja a **jelentés letöltése** gombra kattintva töltse le egy HTML-jelentést, amely tartalmazza a további részletes diagnosztikai információkat, például a storage és a hálózati konfigurációt, a teljesítményszámlálók, nyomkövetéseket, a folyamatok listájában, és naplózza. A tartalom a kiválasztott elemzés függ. Speciális hibaelhárításhoz, a jelentés és interaktív diagramok magas CPU-felhasználás, magas lemezhasználat és folyamatokat, amelyek túl sok memóriát kapcsolatos további információkat is tartalmazhat. A diagnosztika teljesítményjelentés kapcsolatos további információkért lásd: [tekintse át a diagnosztikai jelentés](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/how-to-use-perfinsights#review-the-diagnostics-report).

## <a name="manage-performance-diagnostics-reports"></a>Teljesítmény diagnosztikai jelentések kezelése
Egy vagy több teljesítmény diagnosztikai jelentések használatával törölheti az **jelentés törlése** gombra.

## <a name="how-to-uninstall-performance-diagnostics"></a>Teljesítménybeli problémák diagnosztizálása eltávolítása
Eltávolíthatja a teljesítménybeli problémák diagnosztizálása egy virtuális gépről. Ez a művelet eltávolítja a Virtuálisgép-bővítmény, de nem befolyásolja a tárfiókot a diagnosztikai adatok. 

![Képernyőkép a teljesítmény diagnosztika panel eszköztár Eltávolítás gomb kiemelésével](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>Hol található a tárolt virtuális gép diagnosztikai adatait? 
Az összes diagnosztikai elemzéseket és jelentéseket a saját tárfiókját vannak tárolva. Azure-táblák insights kerülnek. A jelentések tömörített fájlt egy bináris nagyméretű objektum (BLOB) nevű tárolót, azdiagextnresults van tárolva.

A tárfiók-információ megtekintéséhez használja a beállítások gombra az eszköztáron. 

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>Hogyan oszthatom meg ezeket az adatokat a Microsoft ügyfélszolgálata? 
A diagnosztikai jelentés megosztása a Microsofttal többféle módon lehet.

**1. lehetőség:** automatikusan megoszthatja a legújabb jelentésre  
Egy támogatási jegyet a Microsofttal, ha fontos a teljesítmény diagnosztikai jelentés megosztásához. Ha rögzítette ezek az információk megosztása a Microsofttal a diagnosztika futtatása közben (kiválasztásával a "**elfogadom a diagnosztikai információ megosztása a Microsofttal**" jelölőnégyzet), a Microsoft fog tudni hozzáférni a jelentés a storage-ból a fiók SAS-kapcsolaton keresztül kimeneti zip-fájl legfeljebb 30 napig Futtatás időpontjától számított. Csak a legutóbbi jelentés érhető el, a támogatási szakértőhöz. 

**2. lehetőség:** számára a diagnosztikai jelentést a tömörített fájlt, hozzon létre egy közös hozzáférésű jogosultságkód igénylése  
A jelentések tömörített fájlra mutató hivatkozást megoszthatja a közös hozzáférési aláírások használatával. Ehhez kövesse az alábbi lépéseket: 
1)  Az Azure Portalon keresse meg a tárfiók, amelyben a diagnosztikai adatokat tárolja.
2)  Válassza ki **Blobok** alatt a **Blob service** szakaszban. 
3)  Válassza ki a **azdiagextnresults** tároló.
4)  Válassza ki a teljesítmény diagnosztikai kimenet tömörített fájlt szeretné megosztani.
5)  Az a **SAS létrehozása** lapra, válassza ki a feltételeket, megosztására. 
6)  Kattintson a **készítése a blob SAS-jogkivonat és URL-cím**.
7)  Másolás a **a Blob SAS URL-cím**, és megoszthatja a támogatási szakértőhöz. 

**3. lehetőség:** a jelentés letöltése a storage-fiókból

1 – 4. lépéseket a 2. lehetőség használatával is megkeresheti a teljesítmény diagnosztikai jelentés tömörített fájl. Válassza ki, töltse le a fájlt, majd ossza meg e-mailben, vagy kérje meg az utasításokat követve töltse fel a fájlt a támogatási szakértőhöz.  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>Hogyan rögzítése a diagnosztikai adatok a megfelelő időpontban?
Minden egyes Futtatás Teljesítménydiagnosztika rendelkezik két szakaszból áll: 
1)  Telepítse, vagy a teljesítmény diagnosztikai Virtuálisgép-bővítmény frissítése.
2)  A diagnosztika futtatása a megadott időtartamnál.

Jelenleg nincs egyszerű mód, pontosan akkor, ha a VM-bővítmény telepítése befejeződött. Általában másodpercet vesz igénybe körülbelül 45 – 1 percet vesz igénybe a Virtuálisgép-bővítmény telepítése. A Virtuálisgép-bővítmény telepítése után futtathatja a reprodukálás lépéseit, a teljesítménybeli problémák diagnosztizálása az helyes-e az adatkészlethez tartozó hibaelhárítási rögzítése rendelkeznie. 

## <a name="next-steps"></a>További lépések
Után tekintse át a diagnosztikai elemzéseket, és a jelentés, ha még nem határozza meg a probléma okát, és további segítségre van szüksége, akkor nyissa meg egy támogatási jegyet a Microsoft ügyfélszolgálatához. 

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/), és válassza ki **támogatás**. Az Azure-támogatás használatáról további információért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).
