---
title: azcopy pad | Microsoft Docs
description: Ez a cikk a azcopy pad parancsra vonatkozó tudnivalókat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 40ff6c6c76e255945681e678ef296ffcf9978f61
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84485173"
---
# <a name="azcopy-benchmark"></a>azcopy-teljesítményteszt

Teljesítmény-teljesítménytesztet futtat, ha a megadott célhelyre feltölti a tesztelési adataikat. A rendszer automatikusan generálja a tesztet.

A teljesítményteszt-parancs ugyanazzal a feltöltési folyamattal fut, mint a másolás, kivéve a következőket:

  - Nincs forrás paraméter.  A parancshoz csak egy cél URL-cím szükséges. 
  
  - A hasznos adatokat a parancssori paraméterek írják le, amelyek azt szabályozzák, hogy hány fájl jön létre automatikusan, és mekkora a mérete. A létrehozási folyamat teljes mértékben a memóriában zajlik. A lemez nincs használatban.
  
  - A másolási parancs számára elérhető választható paraméterek közül csak néhány támogatott.
  
  - A további diagnosztika mérése és jelentése.
  
  - Alapértelmezés szerint a rendszer a tesztelési Futtatás végén törli az átvitt adatmennyiséget.

A teljesítményteszt mód automatikusan beállíthatja a maximális átviteli sebességet biztosító párhuzamos TCP-kapcsolatok számát. Ez a szám a végén jelenik meg. Az automatikus hangolás megakadályozásához állítsa a AZCOPY_CONCURRENCY_VALUE környezeti változót egy adott számú kapcsolatra.

A szokásos hitelesítési típusok mindegyike támogatott. A teljesítményértékelés legalkalmasabb megközelítése azonban általában egy olyan üres tároló létrehozása SAS-jogkivonattal, amely SAS-hitelesítést használ.

## <a name="examples"></a>Példák

```azcopy
azcopy benchmark [destination] [flags]
```

Teljesítményteszt-teszt futtatása alapértelmezett paraméterekkel (legfeljebb 1 GB/s sebességű hálózatok esetében alkalmas): "

- azcopy pad "https://[fiók]. blob. Core. Windows. net/[Container]? <SAS> "

Futtasson egy teljesítményteszt-tesztet, amely 100 fájlt tölt fel, amelyek mindegyike 2 GiB méretű: (gyors hálózaton, például 10 GB/s):

- azcopy pad "https://[fiók]. blob. Core. Windows. net/[Container]? <SAS> " --file-Count 100--size-per-file 2G

Futtasson teljesítményteszt-tesztet, de használjon 50 000-es fájlokat, amelyek mindegyike 8 MiB méretű, és az MD5-kivonatokat számítja ki (ugyanúgy, ahogyan a `--put-md5` jelző ezt a másolási paranccsal végzi el). A teljesítményértékelés célja `--put-md5` annak tesztelése, hogy az MD5 számítás befolyásolja-e a kiválasztott fájlok számát és méretét:

- azcopy pad "https://[fiók]. blob. Core. Windows. net/[Container]? <SAS> " --file-Count 50000-méret-fájlonként 8P--Put-MD5

## <a name="options"></a>Beállítások

**--a blob-Type** karakterlánc határozza meg a blob típusát a célhelyen. A különböző blob-típusok teljesítményértékelésének engedélyezésére szolgál. Megegyezik a másolási parancs azonos nevű paraméterével (az alapértelmezett "észlelés").

**--Block-Size-MB** lebegőpontos használata ez a blokk mérete (a MIB-ben van megadva). A rendszer automatikusan kiszámítja az alapértelmezett értéket a fájlméret alapján. Tizedes törtek engedélyezettek – például 0,25. Azonos a másolási parancs azonos nevű paraméterével.

**--delete-test--adatelemzés**  Ha az értéke igaz, a rendszer a teljesítményteszt-futtatás végén törli a teljesítményteszt-adatmennyiséget.  Állítsa be hamis értékre, ha a célhelyen szeretné megőrizni az adattárolást, például a teljesítményteszt üzemmódján kívüli manuális teszteléshez (alapértelmezett érték: true).

**--file-Count** uint a használni kívánt automatikusan generált adatfájlok száma (alapértelmezett 100).

**-h,-– Súgó**  A pad súgója

**--a log szintű** karakterlánc határozza meg a naplófájl részletességét, a rendelkezésre álló szinteket: info (minden kérelem/válasz), figyelmeztetés (lassú válasz), hiba (csak sikertelen kérések), és nincs (nincs kimeneti napló). (alapértelmezett "információ")

**--put-MD5**  Hozzon létre egy MD5-kivonatot minden fájlhoz, és mentse a kivonatot a cél blob/fájl tartalom-MD5 tulajdonságának megfelelően. (Alapértelmezés szerint a rendszer nem hozza létre a kivonatot.) Azonos a másolási parancs azonos nevű paraméterével.

– az automatikusan létrehozott adatfájlok **mérete/fájl** mérete. A számnak közvetlenül a K, M vagy G érték után kell szerepelnie, például: 12k vagy 200G (alapértelmezett "250M").

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

**--Cap-Mbps UInt32**  Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.

**--** a parancs kimenetének kimeneti típusú karakterlánc-formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text". (alapértelmezett "text").

**--a megbízható-Microsoft-utótagok** karakterlánca további tartomány-utótagokat határoz meg, amelyekben Azure Active Directory bejelentkezési tokenek küldhetők.  Az alapértelmezett érték: "*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Az itt felsorolt beállítások az alapértelmezett értékre kerülnek. A biztonság érdekében itt csak Microsoft Azure-tartományokat helyezhet el. Több bejegyzést pontosvesszővel kell elválasztani.

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
