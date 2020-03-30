---
title: azcopy pad | Microsoft dokumentumok
description: Ez a cikk az azcopy bench parancs ra vonatkozó referenciainformációkat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8570bce87aeea5473b4aadf9bd30bc0a648a6f0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72518303"
---
# <a name="azcopy-bench"></a>azcopy bench

Teljesítmény-referenciaértéket futtat egy tesztadatok feltöltésével egy adott célhelyre. A tesztadatok automatikusan generálódnak.

A benchmark parancs ugyanazt a feltöltési folyamatot futtatja, mint a "copy", azzal a különbséggel, hogy:

  - Nincs forrásparaméter.  A parancs csak egy cél URL-címet igényel. Az aktuális kiadásban a cél URL-cím kell hivatkoznia egy blob tároló.
  
  - A hasznos adat leírása parancssori paraméterek, amelyek szabályozzák, hogy hány fájl automatikusan generált, és milyen nagyok. A generációs folyamat teljes egészében a memóriában zajlik. A lemez nincs használatban.
  
  - A copy parancs számára elérhető választható paraméterek közül csak néhány támogatott.
  
  - További diagnosztikát mérnek és jelentenek.
  
  - Alapértelmezés szerint az átvitt adatok a tesztsorozat végén törlődnek.

A benchmark mód automatikusan ráhangolódik a maximális átviteli erőket adó párhuzamos TCP-kapcsolatok számára. A végén megjelenik ez a szám. Az automatikus hangolás elkerülése érdekében állítsa a AZCOPY_CONCURRENCY_VALUE környezeti változót adott számú kapcsolatra.

Az összes szokásos hitelesítési típus támogatott. Azonban a legkényelmesebb megközelítés a teljesítményértékelés általában egy sas-jogkivonattal rendelkező üres tároló létrehozása és SAS-hitelesítés használata.

## <a name="examples"></a>Példák

```azcopy
azcopy bench [destination] [flags]
```

Futtasson teljesítménytesztet alapértelmezett paraméterekkel (legfeljebb 1 Gbps-os teljesítményértékelésre alkalmas): "

- azcopy pad "https://[account].blob.core.windows.net/[container]? <SAS>"

Futtasson egy benchmark tesztet, amely 100 fájlt tölt fel, egyenként 2 GiB méretű: (alkalmas gyors hálózaton történő teljesítményértékelésre, pl. 10 Gbps):"

- azcopy pad "https://[account].blob.core.windows.net/[container]? <SAS>" --fájl-gróf 100 --méret-per-fájl 2G

Ugyanaz, mint fent, de használja 50.000 fájlokat, minden 8 MiB méretű és kiszámítja a MD5 hash (ugyanúgy, hogy a --put-md5 zászló ezt a másolás i. parancs). A --put-md5 teljesítményértékelés során annak vizsgálata, hogy az MD5 számítása befolyásolja-e a kiválasztott fájlszám és -méret átviteli átmenő tágságát:

- azcopy pad "https://[account].blob.core.windows.net/[container]? <SAS>" --fájl-gróf 50000 --méret-fájlonként 8M --put-md5

## <a name="options"></a>Beállítások

**--blob-típusú** karakterlánc Határozza meg a blob típusát a cél. Különböző blobtípusok teljesítményértékelésének engedélyezéséhez használható. Megegyezik az azonos nevű paraméterrel a copy parancsban (alapértelmezett "Észlelés").

**--block-size-mb** úszó Használja ezt a blokkméretet (mib- ben megadva). Az alapértelmezett értéket a fájlméret alapján automatikusan kiszámítja a program. Tizedes törtrészek megengedettek - pl. 0,25. Megegyezik a copy parancs azonos nevű paraméterrel.

**--delete-test-data**  Ha igaz, a referenciaadatok a referencia-futtatás végén törlődnek.  Állítsa hamisra, ha az adatokat a célhelyen szeretné tartani - pl. manuális tesztekhez használja a benchmark üzemmódon kívül (alapértelmezett igaz).

**--file-count** uint Az automatikusan generált adatfájlok száma (alapértelmezett 100).

**-h, --segítség**  Segítség a padon

**--log-level** string Adja meg a napló részletességét a naplófájlhoz, elérhető szintek: INFO(minden kérés/válasz), FIGYELEM(lassú válaszok), HIBA(csak sikertelen kérelmek) és NONE (nincs kimeneti napló). (alapértelmezett "INFO")

**--put-md5**  Hozzon létre egy MD5-kivonatot minden fájlból, és mentse a kivonatot a célblob/fájl Content-MD5 tulajdonságaként. (Alapértelmezés szerint a kivonat NEM jön létre.) Megegyezik a copy parancs azonos nevű paraméterrel.

**--size-per-file** string Minden automatikusan generált adatfájl mérete. Egy számnak kell lennie, amelyet közvetlenül a K, M vagy G.E.G. követ. 12k vagy 200G (alapértelmezett "250M").

## <a name="options-inherited-from-parent-commands"></a>Szülőparancsoktól örökölt beállítások

**--sapka-mbps uint32**  Az átviteli sebesség felső határa megabit/másodpercben. A pillanatonkénti átviteli kapacitás kissé eltérhet a kupaktól. Ha ez a beállítás nulla, vagy nincs megadva, az átviteli áteresztőmód nem lesz korlátozva.

**--output-type** string A parancs kimenetének formátuma. A lehetőségek a következők: szöveg, json. Az alapértelmezett érték a "szöveg". (alapértelmezett "szöveg").

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
