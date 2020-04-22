---
title: Hibasorok kezelése az Azure Data Factory leképezési adatfolyamaival
description: Ismerje meg, hogyan kezelhetők az SQL-csonkolási hibák az Azure Data Factory ban a leképezési adatfolyamok használatával.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: makromer
ms.openlocfilehash: 8225143bb75118620b45c2520bb62ea30501a617
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732691"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>SQL csonkolási hibasorok kezelése a Data Factory leképezési adatfolyamaiban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Data Factory gyakori forgatókönyve az adatfolyamok leképezése során, hogy az átalakított adatokat egy Azure SQL-adatbázisba írja. Ebben az esetben egy gyakori hibafeltétel, amelyet meg kell akadályoznia, lehetséges oszlopcsonkolás. Az alábbi lépésekkel biztosíthatja az olyan oszlopok naplózását, amelyek nem férnek bele a célkarakterlánc-oszlopba, lehetővé téve az adatfolyam folytatását ezekben az esetekben.

## <a name="scenario"></a>Forgatókönyv

1. Van egy cél Azure SQL adatbázis ```nvarchar(5)``` tábla, amely egy oszlop neve "név".

2. Az adatfolyamon belül szeretnénk leképezni a filmcímeket a mosogatótól a cél "név" oszlophoz.

    ![Mozgó adatfolyam 1](media/data-flow/error4.png)
    
3. A probléma az, hogy a film címe nem minden fér el egy mosogató oszlopban, amely csak 5 karaktert tartalmazhat. Az adatfolyam végrehajtásakor a következőhez hasonló hibaüzenet et fog kapni:```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

Ez a videó egy példa a beállítási hibasor kezelése logika az adatfolyamban:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4uOHj]

## <a name="how-to-design-around-this-condition"></a>Hogyan tervezzünk körül ezt a feltételt

1. Ebben az esetben a "név" oszlop maximális hossza öt karakterből áll. Tehát adjunk hozzá egy feltételes felosztásos transzformációt, amely lehetővé teszi számunkra, hogy naplózza sorok "címek", amelyek hosszabbak, mint öt karakter, miközben lehetővé teszi a többi sor, amely elfér az adott térben írni az adatbázisba.

    ![feltételes felosztás](media/data-flow/error1.png)

2. Ez a feltételes felosztási transzformáció a "cím" maximális hosszát ötre határozza meg. Minden olyan sor, amely legfeljebb öt, ```GoodRows``` bekerül az adatfolyamba. Az ötnél nagyobb sorok a ```BadRows``` folyamba kerülnek.

3. Most be kell jelentkeznünk a sikertelen sorokba. Adjon hozzá egy ```BadRows``` fogadóátalakítást az adatfolyamhoz a naplózáshoz. Itt az összes mező "automatikus leképezését" fogjuk "automatikusan leképezni", hogy a teljes tranzakciós rekord naplózása legyen. Ez egy szövegesen tagolt CSV-fájl kimenetegyetlen fájl blob storage.This is a text-tagd CSV file output to a single file in Blob Storage. A naplófájlt "badrows.csv"-nek hívjuk.

    ![Hibás sorok](media/data-flow/error3.png)
    
4. A kitöltött adatfolyam az alábbiakban látható. Most már képesek vagyunk leválasztani a hibasorokat, hogy elkerüljük az SQL csonkolási hibákat, és ezeket a bejegyzéseket egy naplófájlba helyezzük. Eközben a sikeres sorok továbbra is írhatnak a céladatbázisba.

    ![teljes adatáramlás](media/data-flow/error2.png)

## <a name="next-steps"></a>További lépések

* Az adatfolyam-átalakítások leképezésével az [adatfolyam-átalakítások](concepts-data-flow-overview.md)leképezésével hozhatja létre az adatfolyam-logika többi részét.
