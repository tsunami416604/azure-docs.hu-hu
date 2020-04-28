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
ms.openlocfilehash: 8570bce87aeea5473b4aadf9bd30bc0a648a6f0f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "72518303"
---
# <a name="azcopy-bench"></a>azcopy bench

Teljesítmény-teljesítménytesztet futtat, ha a megadott célhelyre feltölti a tesztelési adataikat. A rendszer automatikusan generálja a tesztet.

A teljesítményteszt-parancs ugyanazzal a feltöltési folyamattal fut, mint a másolás, kivéve a következőket:

  - Nincs forrás paraméter.  A parancshoz csak egy cél URL-cím szükséges. A jelenlegi kiadásban a cél URL-címnek egy blob-tárolóra kell hivatkoznia.
  
  - A hasznos adatokat a parancssori paraméterek írják le, amelyek azt szabályozzák, hogy hány fájl legyen automatikusan létrehozva, és mekkora legyen. A létrehozási folyamat teljes mértékben a memóriában zajlik. A lemez nincs használatban.
  
  - A másolási parancs számára elérhető választható paraméterek közül csak néhány támogatott.
  
  - A további diagnosztika mérése és jelentése.
  
  - Alapértelmezés szerint a rendszer a tesztelési Futtatás végén törli az átvitt adatmennyiséget.

A teljesítményteszt mód automatikusan beállíthatja a maximális átviteli sebességet biztosító párhuzamos TCP-kapcsolatok számát. Ez a szám a végén jelenik meg. Az automatikus hangolás megakadályozásához állítsa a AZCOPY_CONCURRENCY_VALUE környezeti változót egy adott számú kapcsolatra.

A szokásos hitelesítési típusok mindegyike támogatott. A teljesítményértékelés legalkalmasabb megközelítése azonban általában egy olyan üres tároló létrehozása SAS-jogkivonattal, amely SAS-hitelesítést használ.

## <a name="examples"></a>Példák

```azcopy
azcopy bench [destination] [flags]
```

Teljesítményteszt-teszt futtatása alapértelmezett paraméterekkel (legfeljebb 1 GB/s sebességű hálózatok esetében alkalmas): "

- azcopy pad "https://[fiók]. blob. Core. Windows. net/[Container]? <SAS>"

Futtasson egy teljesítményteszt-tesztet, amely 100 fájlt tölt fel, amelyek mindegyike 2 GiB méretű: (gyors hálózaton, például 10 GB/s):

- azcopy pad "https://[fiók]. blob. Core. Windows. net/[Container]? <SAS>" --file-Count 100--size-per-file 2G

Ugyanaz, mint a fentiekben, de a 50 000-fájlok használata, a 8 MiB mérete és a kiszámított MD5-kivonatok (ugyanúgy, ahogyan a--Put-MD5 jelző ezt a másolási paranccsal végzi el). Az--Put-MD5 használata, ha a benchmarking ellenőrzi, hogy az MD5-számítás befolyásolja-e a kiválasztott fájlok számát és méretét:

- azcopy pad "https://[fiók]. blob. Core. Windows. net/[Container]? <SAS>" --file-Count 50000-méret-fájlonként 8P--Put-MD5

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

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
