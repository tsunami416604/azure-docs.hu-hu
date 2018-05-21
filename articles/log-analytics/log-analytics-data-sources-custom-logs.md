---
title: Az Azure Naplóelemzés egyéni naplógyűjtéshez |} Microsoft Docs
description: A Naplóelemzési képes eseményeket gyűjteni szöveges fájlt a Windows és Linux rendszerű számítógépeken.  Ez a cikk ismerteti, hogyan adható meg egy új egyéni napló és a rekordok hoznak létre a Naplóelemzési munkaterület részleteit.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: aca7f6bb-6f53-4fd4-a45c-93f12ead4ae1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2018
ms.author: bwren
ms.openlocfilehash: e4e2edeb6703e8c55a16b488175fbcdb0dfe56a9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="custom-logs-in-log-analytics"></a>A Naplóelemzési egyéni naplókat
Az egyéni naplókat adatforrásra Naplóelemzési eseményeinek gyűjtése a Windows és Linux számítógépeken egyaránt szövegfájlból teszi lehetővé. Számos alkalmazás adatainak naplózása szöveges fájlok, például a Windows Eseménynapló vagy a Syslog szabványos naplózási szolgáltatások helyett.  Összegyűjtését követően elemezni a rekordokban a bejelentkezés használatával az egyes mezők a [egyéni mezők](log-analytics-custom-fields.md) Naplóelemzési szolgáltatása.

![Egyéni naplógyűjtést](media/log-analytics-data-sources-custom-logs/overview.png)

A naplófájlok kell gyűjteni a következő feltételeknek kell.

- A napló kell el egy soronként egy bejegyzést, vagy minden egyes belépési elején a következő formátumok egyikének megfelelő időbélyeggel használjon.

    ÉÉÉÉ-HH-NN ÓÓ: PP:<br>H/ÉÉÉÉ ÓÓ: PP: MP DE/DU<br>F nn, éééé óó: pp:<br />ééhhnn óó: pp:<br />ddMMyy óó: pp:<br />HHH d óó: pp:<br />nn/hhh/yyyy:HH:mm:ss zzz<br />éééé-hh-ddTHH:mm:ssK

- A naplófájl nem engedélyezheti a körkörös naplózás vagy naplóváltás, ha a fájl új bejegyzések felülírja.
- A naplófájl ASCII vagy UTF-8 kódolást kell használnia.  Más például az UTF-16 formátum nem támogatott.

>[!NOTE]
>Ha ne legyenek ismétlődő bejegyzések a naplófájlban, Log Analyticshez gyűjt őket.  Azonban a keresési eredmények lesz inkonzisztens ahol a szűrő eredményben további események, mint az eredmények száma.  Fontos, hogy a napló határozza meg, ha az azt létrehozó alkalmazást okozza-e ezt a viselkedést, és oldja meg, ha lehetséges az egyéni naplódefiníciója gyűjtemény létrehozása előtt ellenőrizze lesz.  
>
  
## <a name="defining-a-custom-log"></a>Egy egyéni napló meghatározása
A következő eljárás használatával adja meg egy egyéni naplófájlt.  Görgesse le ez a cikk egy egyéni napló hozzáadásának minta útmutatást.

### <a name="step-1-open-the-custom-log-wizard"></a>1. lépés Nyissa meg az egyéni naplózás varázsló
Az egyéni naplózás varázsló az Azure-portálon fut, és adhatók meg egy új egyéni napló gyűjtése.

1. Válassza ki az Azure-portálon **Naplóelemzési** > a munkaterület > **speciális beállítások**.
2. Kattintson a **adatok** > **egyéni naplói**.
3. Összes konfigurációs módosításhoz alapértelmezés szerint automatikusan leküldéssel az összes ügynököt.  Linux-ügynökök, a konfigurációs fájlt a Fluentd adatgyűjtő küld.  Ha manuálisan minden egyes Linux-ügynök a következő fájl módosításához, törölje a jelet *alkalmaz az alábbi konfiguráció a Linuxos gépeimre*.
4. Kattintson a **Add +** egyéni napló varázsló megnyitásához.

### <a name="step-2-upload-and-parse-a-sample-log"></a>2. lépés Töltse fel, és elemezni egy mintanaplót
Indítsa el az egyéni napló minta feltöltésével.  A varázsló elemzése és a fájlban ahhoz, hogy ellenőrizze a bejegyzéseket megjeleníteni.  A Naplóelemzési a rekordokban azonosításához megadott elválasztó fogja használni.

**Új sor** az alapértelmezett elválasztó és a naplófájlokat, amelyek egy-egy bejegyzésnek soronként használt.  Ha a sor kezdődik-e a dátumot és időpontot a formátumok egyikében, akkor megadhatja egy **időbélyeg** szövegelválasztó, amely támogatja a bejegyzéseket, amelyek több egynél több sort.

Timestamp típusú elválasztó használata esetén majd Naplóelemzési tárolt rekordokban TimeGenerated tulajdonságának tölti fel az adott bejegyzés a naplófájlban a megadott dátum/idő.  Ha egy új sor elválasztó használja, majd TimeGenerated a telepítéskor dátum és idő, hogy a Naplóelemzési gyűjtött a bejegyzés.


1. Kattintson a **Tallózás** és egy minta-fájl megkereséséhez.  Vegye figyelembe, hogy ez lehetséges, hogy gomb neve lehet **Choose File** egyes böngészőkben.
2. Kattintson a **Tovább** gombra.
3. Az egyéni napló varázsló feltölteni a fájlt és az azt jelzi, hogy az azonosított listában.
4. A határoló, amellyel azonosíthatja egy új rekordot, és válassza ki a szövegelválasztó, amely a legjobban azonosítja a naplófájl rekordjainak módosítása.
5. Kattintson a **Tovább** gombra.

### <a name="step-3-add-log-collection-paths"></a>3. lépés Naplógyűjtemények elérési útjának felvétele
Meg kell adnia egy vagy több elérési utak az ügynökön, ahol keresse meg az egyéni naplót.  Vagy megadhatja a megadott elérési út és a naplófájl nevét, vagy egy elérési utat megadhatja a név helyettesítő karakter.  Ez a funkció támogatja alkalmazásokat, amelyek minden nap, vagy ha egy fájl elér egy adott méretet, hozzon létre egy új fájlt.  Több útvonal egy naplófájlt is biztosítható.

Például egy alkalmazás előfordulhat, hogy dátummal hozza létre a naplófájl minden nap a a neve, ahogy log20100316.txt tartalmazza. Előfordulhat, hogy az ilyen naplók minta *napló\*.txt* amely bármely naplófájl, az alkalmazás a következő lenne érvényes csomagazonosítóját elnevezési sémát.

A következő táblázat érvényes minták adhatja meg a különböző naplófájlokat.

| Leírás | Útvonal |
|:--- |:--- |
| Az összes fájl *C:\Logs* .txt kiterjesztésű, a Windows-ügynök |C:\Logs\\\*.txt |
| Az összes fájl *C:\Logs* napló és a Windows-ügynök a .txt kiterjesztésű neve |C:\Logs\log\*.txt |
| Az összes fájl */var/log/audit* .txt kiterjesztésű, a Linux-ügynök |/var/log/audit/*.txt |
| Az összes fájl */var/log/audit* napló és a Linux-ügynök a .txt kiterjesztésű neve |/var/log/audit/log\*.txt |

1. Jelölje be a Windows vagy Linux adja meg, melyik elérési út formátumot ad hozzá.
2. Írja be a elérési utat, majd kattintson a **+** gombra.
3. Bármely további elérési utak ismételje meg a műveletet.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>4. lépés Adjon nevet és leírást a napló
A megadott név lesz használható a napló típusa fent leírt módon.  Azt mindig segítségével különböztetheti meg egymástól, egy egyéni napló _CL végződik.

1. Adja meg a napló nevét.  A  **\_CL** utótag automatikusan elérhető.
2. Adja hozzá egy nem kötelező **leírás**.
3. Kattintson a **következő** menteni az egyéni napló-definícióját.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>5. lépés Ellenőrizze, hogy az egyéni naplókat a rendszer éppen gyűjti
Azt is tarthat a kezdeti adatok egy órát az új egyéni napló Naplóelemzési jelennek meg.  Bejegyzések gyűjtése fog elindulni, hogy az egyéni napló meghatározott pontról megadott a naplókból az elérési úton található.  Azt nem őrzi meg az egyéni napló létrehozása során feltöltött bejegyzéseket, de a naplófájlokban, amely azt a már meglévő bejegyzéseket gyűjt.

Naplóelemzési elindul, a rendszer összegyűjti az egyéni napló, a rekordok állnak rendelkezésre a napló keresés.  A egyéni naplót, mint a megadott nevet használja a **típus** a lekérdezésben.

> [!NOTE]
> Ha a RawData tulajdonság hiányzik a keresőmezőbe, esetleg zárja be és nyissa meg újra a böngészőt.
>
>

### <a name="step-6-parse-the-custom-log-entries"></a>6. lépés Az egyéni naplóbejegyzések elemzése
A teljes naplóbejegyzés fogja tárolni egy tulajdonságot, **RawData**.  Valószínűleg érdemes a másik adatot tárolja a rekordban levő egyes tulajdonságokat az egyes bejegyzések elválasztásához.  Ehhez használja a [egyéni mezők](log-analytics-custom-fields.md) Naplóelemzési szolgáltatása.

Nincsenek megadva itt részletes, lépésenkénti leírását az egyéni naplóbejegyzés elemzésekor.  Tekintse meg a [egyéni mezők](log-analytics-custom-fields.md) ezt az információt dokumentációját.

## <a name="removing-a-custom-log"></a>Egy egyéni napló eltávolítása
Az Azure-portálon a következő folyamat segítségével távolítsa el a korábban definiált egyéni napló.

1. Az a **adatok** menüjében a **speciális beállítások** a munkaterületen, válassza a **egyéni naplókat** listát az egyéni naplókat.
2. Kattintson a **eltávolítása** melletti távolítsa el az egyéni naplót.


## <a name="data-collection"></a>Adatgyűjtés
A Naplóelemzési összegyűjti az új bejegyzések minden egyéni naplóból körülbelül 5 percenként.  Az ügynök minden az összegyűjtő naplófájlban rögzítik a helyére.  Ha az ügynököt az adott időszakban offline állapotba kerül, majd Naplóelemzési bejegyzések az gyűjtenek ahol utolsó abbamaradtak, még akkor is, ha a tételekhez hozta létre, miközben az ügynök offline állapotban volt.

A naplóbejegyzés teljes tartalmát a rendszer egyetlen tulajdonságot, ír **RawData**.  Ez az elemzett és meghatározásával külön keresés több tulajdonságok tudja értelmezni [egyéni mezők](log-analytics-custom-fields.md) az egyéni napló létrehozása után.

## <a name="custom-log-record-properties"></a>Egyéni napló rekord tulajdonságai
Egyéni naplórekordokat tartozhat a neve, amely megadja, és a tulajdonságok az alábbi táblázatban.

| Tulajdonság | Leírás |
|:--- |:--- |
| TimeGenerated |Dátum és idő, a rekord Naplóelemzési által összegyűjtött.  Ha a napló időalapú elválasztó használ majd ez az az idő, a bejegyzés gyűjtött. |
| SourceSystem |A rekord gyűjtötte a program az ügynök típusa. <br> OpsManager – Windows-ügynök, vagy közvetlen kapcsolódás vagy a System Center Operations Manager <br> Linux – az összes Linux-ügynökök |
| RawData |Teljes szöveges a gyűjtött bejegyzés. |
| ManagementGroupName |A System Center Operations kezelése ügynökök a felügyeleti csoport neve.  Más ügynökök, ez pedig AOI -\<munkaterület azonosítója\> |

## <a name="log-searches-with-custom-log-records"></a>Napló keresést egyéni naplórekordokat:
Csakúgy, mint bármely más adatforrás bejegyzéseit a Naplóelemzési munkaterület rögzíti az egyéni naplókat tárolja.  A típus, amely megadja, hogy használhassa a Type tulajdonságot a keresés egy adott napló gyűjtött rekordok beolvasni a naplófájl definiálásakor névnek megfelelő rendelkeznek.

Az alábbi táblázat példákat különböző napló kereséseket rekordok lekérése az egyéni naplókat.

| Lekérdezés | Leírás |
|:--- |:--- |
| MyApp_CL |Az egyéni összes eseménynaplózás elnevezett MyApp_CL. |
| MyApp_CL &#124; ahol Severity_CF == "error" |Minden egyéni az eseménynaplózás elnevezett MyApp_CL értékkel rendelkező *hiba* nevű egyéni mező *Severity_CF*. |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Egy egyéni napló hozzáadásának minta forgatókönyv
A következő szakasz végigvezeti egy egyéni napló létrehozására láthat példát.  A gyűjtött napló minta soronként egy dátumot és időt, majd majd vesszővel tagolt mezők kódja, állapotát és üzenet kezdve egy-egy bejegyzésnek rendelkezik.  Több minta bejegyzések alább látható.

    2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2016-03-10 01:38:22 302,Error,Application could not connect to database
    2016-03-10 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Töltse fel, és elemezni egy mintanaplót
A naplófájlok egyikét adja meg azt, és láthatja az eseményeket, azt fogja gyűjtése.  Ebben az esetben az új sor elegendő elválasztó.  Ha a naplóban szereplő egy-egy bejegyzésnek sikerült át, ha több sorra, timestamp elválasztó kellene használni.

![Töltse fel, és elemezni egy mintanaplót](media/log-analytics-data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Naplógyűjtemények elérési útjának felvétele
A naplófájlokban található *C:\MyApp\Logs*.  Egy új fájl neve tartalmazza a dátum a mintában a naponta jön létre *appYYYYMMDD.log*.  Ez a napló az elegendő mintázatát lenne *C:\MyApp\Logs\\\*.log*.

![Naplófájl elérési útvonala](media/log-analytics-data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Adjon nevet és leírást a napló
Egy nevét használjuk *MyApp_CL* , és írja be a **leírás**.

![Napló neve](media/log-analytics-data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Ellenőrizze, hogy az egyéni naplókat a rendszer éppen gyűjti
A lekérdezés használjuk *típus = MyApp_CL* az összes rekordját adja vissza az összegyűjtött naplózási.

![Egyéni mezők napló lekérdezés](media/log-analytics-data-sources-custom-logs/query-01.png)

### <a name="parse-the-custom-log-entries"></a>Az egyéni naplóbejegyzések elemzése
Egyéni mezők definiálásához használjuk a *EventTime*, *kód*, *állapot*, és *üzenet* mezők és azt láthatja a különbség a rekordok a lekérdezés által visszaadott, amely.

![Napló lekérdezés egyéni mezőkkel](media/log-analytics-data-sources-custom-logs/query-02.png)

## <a name="next-steps"></a>További lépések
* Használjon [egyéni mezők](log-analytics-custom-fields.md) egyedi mezők egyéni bejelentkezési bejegyzései elemzése.
* További tudnivalók [keresések jelentkezzen](log-analytics-log-searches.md) az adatforrások és a megoldások gyűjtött adatok elemzésére.
