---
title: Hierarchikus névtér Azure Data Lake Storage Gen2
description: A Azure Data Lake Storage Gen2 hierarchikus névterének fogalmát ismerteti.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 0b98892bd31b097e3dc217d54f52f12550599d32
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847147"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Hierarchikus névtér Azure Data Lake Storage Gen2

Egy kulcsfontosságú mechanizmus, amely lehetővé teszi, hogy a Azure Data Lake Storage Gen2 a fájlrendszer teljesítményének biztosítása az objektum tárolási skálázása és az árak között egy **hierarchikus névtér**hozzáadásával. Ez lehetővé teszi, hogy a fiókban lévő objektumok/fájlok gyűjteményét a rendszer a címtárak és a beágyazott alkönyvtárak hierarchiába rendezi, ugyanúgy, ahogy a számítógép fájlrendszere. Ha a hierarchikus névtér engedélyezve van, a Storage-fiók képes lesz biztosítani az objektumok tárterületének méretezhetőségét és költséghatékonyságát, valamint az elemzési motorok és keretrendszerek számára ismerős fájlrendszer-szemantikai adatkezelést.

## <a name="the-benefits-of-the-hierarchical-namespace"></a>A hierarchikus névtér előnyei

A következő előnyök olyan fájlrendszerekhez vannak társítva, amelyek a blob adatain alapuló hierarchikus névteret implementálnak:

- **Atomi címtár-manipuláció:** Az objektum megközelíti a címtár-hierarchiát úgy, hogy az elérési út szegmensének megjelenítéséhez egy beágyazási perjelet (/) tartalmazó egyezményt fogad el az objektum nevében. Habár ez az egyezmény az objektumok rendszerezésére is használható, az egyezmény nem nyújt segítséget olyan műveletekhez, mint például a könyvtárak áthelyezése, átnevezése vagy törlése. Valódi könyvtárak nélkül az alkalmazásoknak több millió egyedi blobot kell feldolgozniuk a címtár szintű feladatok eléréséhez. Ezzel szemben a hierarchikus névtér feldolgozza ezeket a feladatokat egyetlen bejegyzés (a szülő könyvtár) frissítésével.

    Ez a drámai optimalizálás különösen sok big data elemzési keretrendszer esetében jelentős jelentőséggel bír. Az olyan eszközök, mint például a kaptár, a Spark stb. gyakran küldenek kimenetet ideiglenes helyekre, majd átnevezik a helyet a feladatok befejezésekor. A hierarchikus névtér nélkül az Átnevezés gyakran hosszabb időt vehet igénybe, mint maga az elemzési folyamat. Az alacsonyabb feladatok késése az elemzési számítási feladatokhoz tartozó teljes tulajdonlási költséget (TCO) csökkenti.

- **Ismerős kezelőfelület stílusa:** A fájlrendszerek jól értik a fejlesztők és a felhasználók számára. A felhőre való áttéréskor nem kell új tárolási paradigmát megtanulnia, mivel a Data Lake Storage Gen2 által közzétett fájlrendszer-felület ugyanaz a paradigma, amely a számítógépek, a kis-és a nagyvállalatok által használt.

Az egyik oka, hogy az objektumok tárolói nem támogatják a hierarchikus névtereket, a hierarchikus névtér korlátozza a méretezést. A Data Lake Storage Gen2 hierarchikus névtér azonban lineárisan méretezhető, és nem csökkenti az adatkapacitást vagy a teljesítményt.

## <a name="when-to-enable-the-hierarchical-namespace"></a>A hierarchikus névtér engedélyezése

Javasoljuk, hogy kapcsolja be a hierarchikus névteret olyan tárolási számítási feladatokhoz, amelyeket a címtárakat kezelő fájlrendszerek számára terveztek. Ebbe beletartozik az összes olyan számítási feladat, amely elsősorban az elemzések feldolgozására szolgál. A magas fokú szervezetet igénylő adatkészletek a hierarchikus névtér engedélyezésével is hasznosak lesznek.

A hierarchikus névtér engedélyezésének okait a TCO-elemzés határozza meg. Általánosságban elmondható, hogy a tárolási gyorsítás miatti munkaterhelések késésének fejlesztése a számítási erőforrásokat kevesebb ideig igényli. Számos számítási feladat késése a hierarchikus névtér által engedélyezett atomi címtár-manipuláció miatt javítható. Számos munkaterhelésben a számítási erőforrás a teljes költség > 85%-át, így a munkaterhelés késésének szerény csökkenése is jelentős mennyiségű TCO-megtakarítást jelent. Még azokban az esetekben is, amikor a hierarchikus névtér engedélyezése növeli a tárolási költségeket, a TCO-t továbbra is csökkentik a számítási költségek miatt.

## <a name="when-to-disable-the-hierarchical-namespace"></a>Mikor kell letiltani a hierarchikus névteret

Előfordulhat, hogy egyes objektum-tárolási munkaterhelések nem kapnak előnyt a hierarchikus névtér engedélyezésével. Ilyenek például a biztonsági másolatok, a képtárolók és az egyéb alkalmazások, amelyekben az Object Organization külön tárolódik az objektumokból (például egy különálló adatbázisban).

## <a name="next-steps"></a>További lépések

- [Storage-fiók létrehozása](./data-lake-storage-quickstart-create-account.md)
