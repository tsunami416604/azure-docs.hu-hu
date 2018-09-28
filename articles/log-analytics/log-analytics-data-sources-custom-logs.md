---
title: Egyéni gyűjtését az Azure Log Analyticsben |} A Microsoft Docs
description: A log Analytics képes események gyűjtésére szöveges fájlok, a Windows és Linux rendszerű számítógépeket is.  Ez a cikk ismerteti, hogyan adhat meg egy új egyéni naplót, és a Log Analytics-munkaterületen hoznak létre a rekordok részleteit.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: aca7f6bb-6f53-4fd4-a45c-93f12ead4ae1
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 5eab8e4bf6b1aa90a9eef3e26dfc3020e3e3179b
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423510"
---
# <a name="custom-logs-in-log-analytics"></a>A Log Analytics egyéni naplók
Az egyéni naplókat adatforrás a Log Analytics lehetővé teszi az események gyűjtésére a Windows és a Linux rendszerű számítógépek szöveges fájlok. Számos alkalmazás adatokat szöveges fájlok nem szabványos naplózási szolgáltatásokkal, például a Windows Eseménynapló vagy a Syslog naplófájlba.  Után minden egyes mezőket a bejelentkezés rekordja értelmezni tudja a [egyéni mezők](log-analytics-custom-fields.md) Log Analytics szolgáltatást.

![Egyéni naplók összegyűjtése](media/log-analytics-data-sources-custom-logs/overview.png)

A naplófájlok gyűjtendő egyeznie kell a következő feltételeknek.

- A napló kell, vagy minden sorában egy-egy bejegyzésnek kell vagy elején minden bejegyzés a következő formátumok egyikének megfelelő időbélyeg használja.

    ÉÉÉÉ-HH-NN ÓÓ<br>H/ÉÉÉÉ ÓÓ: PP: MP DE/DU<br>Hónap nn, éééé óó<br />ééhhnn óó<br />ddMMyy óó<br />MMM d óó<br />nn/MMM/yyyy:HH:mm:ss zzz<br />éééé-hh-ddTHH:mm:ssK

- A naplófájl nem engedélyezheti a körkörös naplózást, vagy naplóváltás, ahol a fájl új bejegyzések felülírja.
- A naplófájl ASCII vagy UTF-8 kódolást kell használnia.  Eltérő, például az UTF-16 formátumban nem támogatottak.

>[!NOTE]
>Ha ismétlődő bejegyzéseket a naplófájlban, a Log Analytics gyűjti azokat.  Azonban a keresési eredmények lesz inkonzisztens ahol a az eredmények szűréséhez, mint a eredményszámot további események megjelenítése.  Fontos, hogy ellenőrizze, hogy a napló megállapításához, hogy az alkalmazás, amely létrehozza okozza ezt a viselkedést, és oldja meg az egyéni napló gyűjtése definíció létrehozása előtt, ha lehetséges lesz.  
>
  
>[!NOTE]
> Ha az alkalmazás egy új naplófájlt hoz létre, minden nap, vagy ha egy bizonyos méretet elér, a Linuxhoz készült Log Analytics-ügynök nem deríti fel őket, amíg újra nem indítják. Ez azért, mert az ügynök csak enumerálása, és megkezdődik a figyelés indítása után a megadott naplók mintákat, és ezért meg kell terveznie, körülötte az az ügynök újraindítása automatizálásával.  Ez a korlátozás nem létezik a Windows a Log Analytics-ügynököt.  
>

## <a name="defining-a-custom-log"></a>Egyéni napló meghatározása
A következő eljárás használatával adja meg egy egyéni naplófájlt.  Ez a cikk az egyéni napló felvétele mintát bemutató végén görgessen.

### <a name="step-1-open-the-custom-log-wizard"></a>1. lépés Nyissa meg az egyéni napló varázsló
Az egyéni napló varázsló az Azure Portalon fut, és lehetővé teszi egy új egyéni naplót gyűjtéséhez.

1. Az Azure Portalon válassza ki a **Log Analytics** > a munkaterület > **speciális beállítások**.
2. Kattintson a **adatok** > **egyéni naplók**.
3. Alapértelmezés szerint az összes konfigurációs módosítást automatikusan leküld az összes ügynököt.  Linux-ügynökök a Fluentd adatgyűjtő küld egy konfigurációs fájl.  Ha szeretné módosítani ezt a fájlt minden egyes Linux-ügynök manuálisan, törölje a jelet *alkalmaz az alábbi konfiguráció Linuxos gépeimre*.
4. Kattintson a **Add +** az egyéni napló varázsló megnyitásához.

### <a name="step-2-upload-and-parse-a-sample-log"></a>2. lépés Töltse fel és a egy mintanaplót elemzése
Indítsa el az egyéni napló minta feltöltésével.  A varázsló elemzése és megjelenítése a bejegyzéseket, hogy ellenőrizze a fájlban.  A log Analytics az elválasztó alapján azonosíthatja az egyes rekordok megadott fogja használni.

**Új sor** az alapértelmezett elválasztó karaktert, és a naplófájlokat, amelyek egy-egy bejegyzésnek soronként használható.  Ha a sor kezdődik a dátumot és időpontot a rendelkezésre álló formátumok egyikében, akkor megadhatja egy **időbélyeg** elválasztó karakter, amely támogatja a bejegyzéseket, amelyek egynél több sort.

Egy időbélyeg elválasztó használata, majd a TimeGenerated tulajdonság, az egyes rekordok tárolja a Log Analytics tölti fel, hogy a naplófájl-bejegyzést a megadott dátum/idő.  Ha egy új sor elválasztó használja, majd TimeGenerated a dátumot és időt, hogy a Log Analytics gyűjti a bejegyzés van feltöltve.


1. Kattintson a **Tallózás** , keresse meg a minta fájlt.  Vegye figyelembe, hogy ez lehetséges, hogy a gomb neve lehet **fájl kiválasztása** egyes böngészőkben.
2. Kattintson a **Tovább** gombra.
3. Az egyéni napló varázsló feltölteni a fájlt, és a listában, amely azonosítja a rekordokat.
4. Módosítsa az új rekord azonosításához, és válassza ki a kivonni kívánt a naplófájl rekordjainak legjobb azonosításához használt elválasztó karaktert.
5. Kattintson a **Tovább** gombra.

### <a name="step-3-add-log-collection-paths"></a>3. lépés Naplógyűjtemények elérési útjának felvétele
Az ügynökön, ahol keresse meg az egyéni napló definiálni kell egy vagy több elérési útvonalat.  Vagy megadhat egy adott elérési útja és a naplófájl neve, vagy a név helyettesítő karakter segítségével adható meg egy elérési utat. Ez támogatja az alkalmazásokat, amelyek minden nap, vagy ha egy fájl bizonyos méretet elér, hozzon létre egy új fájlt. Az egyetlen naplófájl több elérési úttal is megadhatja.

Egy alkalmazás előfordulhat, hogy hozzon létre például egy naplófájlt minden nap log20100316.txt hasonlóan a neve tartalmazza a dátuma. Lehet, hogy az ilyen naplók mintát *log\*.txt* amely bármely naplófájl, az alkalmazás a következő lenne érvényes a séma elnevezése.

>[!NOTE]
> Ha az alkalmazás egy új naplófájlt hoz létre, minden nap, vagy ha egy bizonyos méretet elér, a Linuxhoz készült Log Analytics-ügynök nem deríti fel őket, amíg újra nem indítják. Ez azért, mert az ügynök csak enumerálása, és megkezdődik a figyelés indítása után a megadott naplók mintákat, és ezért meg kell terveznie, körülötte az az ügynök újraindítása automatizálásával.  Ez a korlátozás nem létezik a Windows a Log Analytics-ügynököt.  
>

Az alábbi táblázat példákat érvényes minták adja meg a különböző naplófájlokat.

| Leírás | Útvonal |
|:--- |:--- |
| Az összes fájl *C:\Logs* .txt kiterjesztésű Windows-ügynök |C:\Logs\\\*.txt |
| Az összes fájl *C:\Logs* a nevével, a napló és a egy .txt kiterjesztése a Windows-ügynök indítása |C:\Logs\log\*.txt |
| Az összes fájl */var/log/audit* .txt kiterjesztésű, a Linux-ügynök |/var/log/audit/*.txt |
| Az összes fájl */var/log/audit* napló- és Linux-ügynök a .txt kiterjesztésű kezdetű névvel |/var/log/audit/log\*.txt |

1. Válassza ki, Windows vagy Linux rendszerű, adja meg, hogy mely elérési út formátuma ad hozzá.
2. Írja be a elérési utat, majd kattintson a **+** gombra.
3. Ismételje meg a folyamatot minden olyan további elérési utak.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>4. lépés Adjon meg egy nevet és leírást a napló
Az Ön által megadott név lesz használható a napló típusa fent leírtak szerint.  Mindig megszűnik az egyéni napló, csatornától való _CL.

1. Írja be a napló nevét.  A  **\_CL** utótag automatikusan elérhető.
2. Adjon hozzá egy nem kötelező **leírás**.
3. Kattintson a **tovább** az egyéni napló-definíció mentéséhez.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>5. lépés Ellenőrizze, hogy az egyéni naplókat gyűjtött
Igénybe vehet egy órát a kezdeti adatok az új egyéni naplót megjelennek a Log Analyticsben.  Elindul az bejegyzések gyűjtése a ponttól, hogy az egyéni napló megadott megadott a naplókból, az elérési úton található.  Azt nem őrzi meg az egyéni napló létrehozása során feltöltött bejegyzések, de a naplófájlokban talál, már meglévő bejegyzéseket gyűjt.

Megkezdéséről Log Analytics gyűjti össze az egyéni napló, a rekordok egy naplókereséssel elérhető lesz.  Mint az egyéni napló megadott nevét használja a **típus** a lekérdezésben.

> [!NOTE]
> A RawData tulajdonság hiányzik a keresési, szükség lehet bezárja és újra megnyitja a böngészőt.
>
>

### <a name="step-6-parse-the-custom-log-entries"></a>6. lépés Az egyéni napló bejegyzései elemzése
A teljes naplóbejegyzés lesz tárolva egyetlen tulajdonsággal **RawData**.  Valószínűleg érdemes a különböző információt tárolja a rekordban levő egyes tulajdonságokat az egyes bejegyzések elválasztásához.  Ehhez használja a [egyéni mezők](log-analytics-custom-fields.md) Log Analytics szolgáltatást.

Részletes lépéseit az egyéni naplóbejegyzés elemzés nem tartozik ide.  Tekintse meg a [egyéni mezők](log-analytics-custom-fields.md) ezt az információt a dokumentáció.

## <a name="removing-a-custom-log"></a>Egyéni napló eltávolítása
A következő folyamat használja az Azure Portalon, amely korábban definiált egyéni napló eltávolítása.

1. Az a **adatok** menüjében a **speciális beállítások** a munkaterületén válassza **egyéni naplók** listázásához az egyéni naplókat.
2. Kattintson a **eltávolítása** mellett az egyéni napló eltávolítása.


## <a name="data-collection"></a>Adatgyűjtés
A log Analytics új bejegyzések-fájlokból gyűjt mindegyik egyéni napló körülbelül 5 percenként.  Az ügynök által gyűjtött, akkor minden egyes naplófájlban a helyére jegyezze fel.  Ha az ügynököt egy ideig offline állapotba kerül, majd a Log Analytics bejegyzések-fájlokból gyűjt ahol utolsó abbamaradtak, akkor is, ha tételekhez jöttek létre, miközben az ügynök offline állapotban volt.

A naplóbejegyzés teljes tartalmát írt egyetlen tulajdonsággal **RawData**.  Ez az elemzett és definiálásával külön-külön keresni lehet több tulajdonságot tudja elemezni [egyéni mezők](log-analytics-custom-fields.md) az egyéni napló létrehozása után.

## <a name="custom-log-record-properties"></a>Az egyéni napló rekord tulajdonságai
Az egyéni napló rögzíti az alábbi táblázatban egy típus a napló nevére, Ön által megadott és a Tulajdonságok rendelkezik.

| Tulajdonság | Leírás |
|:--- |:--- |
| TimeGenerated |Dátum és idő, a rekord a Log Analytics által összegyűjtött.  Ha a napló egy időalapú elválasztó használ majd ez az az idő a bejegyzés gyűjtött. |
| SourceSystem |A rekord gyűjtötte a program az ügynök típusa. <br> OpsManager – Windows-ügynök, vagy közvetlen csatlakozás vagy a System Center Operations Manager <br> Linux – az összes Linux-ügynökök |
| RawData |Teljes szöveges összegyűjtött bejegyzés. |
| ManagementGroupName |A System Center Operations kezelése ügynökök a felügyeleti csoport neve.  Más ügynökök esetén ez AOI -\<munkaterület azonosítója\> |

## <a name="log-searches-with-custom-log-records"></a>Az egyéni napló rekordokat tartalmazó naplókeresések
Csakúgy, mint bármely más adatforrás rekordjainak a Log Analytics-munkaterületen tárolja a rekordokat az egyéni naplókat.  Így használhatja a Type tulajdonság a keresésben beolvasni egy konkrét naplófájlokból származó összegyűjtött rekordokkal meghatározása naplóban során megadandó névnek megfelelő típus rendelkeznek.

Az alábbi táblázat példákat különböző egyéni naplók le a rekordokat, a naplókeresésekben.

| Lekérdezés | Leírás |
|:--- |:--- |
| MyApp_CL |Egyéni összes eseménye elnevezett MyApp_CL naplózása. |
| MyApp_CL &#124; ahol Severity_CF == "error" |Egyéni összes eseménye elnevezett MyApp_CL jelentkezzen értékkel *hiba* nevű egyéni mező *Severity_CF*. |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Egyéni napló hozzáadásának mintaútmutató
Egy példa egy egyéni napló létrehozása a következő szakasz ismerteti.  Az összegyűjtött mintanapló kezdve a dátumot és időt, és ezután vesszővel tagolt mezők a kódot, állapota és üzenet soronként egy-egy bejegyzésnek rendelkezik.  Több minta bejegyzések alább láthatók.

    2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2016-03-10 01:38:22 302,Error,Application could not connect to database
    2016-03-10 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Töltse fel és a egy mintanaplót elemzése
Azt a naplófájlok egyikét adja meg, és láthatja az eseményeket, azt fogja gyűjtése.  Ebben az esetben új sor esetében egy elegendő elválasztó karaktert.  Ha egy-egy bejegyzésnek a naplóban sikerült ívelhet át több sort, és a egy időbélyeg elválasztó karaktert kell használni.

![Töltse fel és a egy mintanaplót elemzése](media/log-analytics-data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Naplógyűjtemények elérési útjának felvétele
A naplófájlokban található *C:\MyApp\Logs*.  Új fájl jön létre minden nap, amelynek neve tartalmazza a dátum a mintában *appYYYYMMDD.log*.  Ez a napló a megfelelő mintát lenne *C:\MyApp\Logs\\\*.log*.

![Napló elérési útvonala](media/log-analytics-data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Adjon meg egy nevet és leírást a napló
Egy nevét használjuk *MyApp_CL* , és írja be a **leírás**.

![Napló neve](media/log-analytics-data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Ellenőrizze, hogy az egyéni naplókat gyűjtött
Használjuk a lekérdezést *típus = MyApp_CL* összegyűjtött napló minden rekord visszaadása.

![Az egyéni mezők naplólekérdezés](media/log-analytics-data-sources-custom-logs/query-01.png)

### <a name="parse-the-custom-log-entries"></a>Az egyéni napló bejegyzései elemzése
Egyéni mezők definiálásához használjuk a *EventTime*, *kód*, *állapot*, és *üzenet* mezőket, és láthatja a rekordok közötti különbség a lekérdezés által visszaadott, amely.

![Egyéni mezőkkel naplólekérdezés](media/log-analytics-data-sources-custom-logs/query-02.png)

## <a name="next-steps"></a>További lépések
* Használat [egyéni mezők](log-analytics-custom-fields.md) elemezni az egyéni jelentkezzen be az egyes mezők bejegyzést.
* Ismerje meg [naplókereséseket](log-analytics-log-searches.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez.
