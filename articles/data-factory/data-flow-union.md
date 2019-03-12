---
title: Az Azure Data Factory-térképadatokat új ág átalakítási folyamat
description: Az Azure Data Factory-térképadatokat új ág átalakítási folyamat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 1eebc879ad56ba4f35e6a8a1b857ae877a6a2f01
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726260"
---
# <a name="mapping-data-flow-union-transformation"></a>Adatátalakítás folyamat union leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Union egy, az SQL Union ezeket adatfolyamok, a Union átalakítás új kimenete az össze a több adatfolyamokat. Minden egyes bemeneti streamből a séma egyesítve jön létre az adatok folyamaton, anélkül, hogy rendelkezik egy illesztési kulccsal.

A beállítások tábláját Streamek n számú mellett minden egyes konfigurált sor a "+" ikont választva kombinálhatók.

![A UNION átalakítási](media/data-flow/union.png "Union")

Ebben az esetben különálló metaadatok (az ebben a példában három különböző forrásfájlok) több forrásból egyesítheti és kombinálhatja őket, egyetlen streammel:

![Union átalakítást – áttekintés](media/data-flow/union111.png "Union 1")

Ennek érdekében hozzáadhat további sorokat az Unió között beleértve a hozzáadni kívánt összes forrás. Esetén nem kell egy közös lookup vagy illesztési kulcs:

![A UNION átalakítási beállítások](media/data-flow/unionsettings.png "Union beállításai")

Válassza ki az átalakítás után az Unió állít be, ha tudják, egymást átfedő és, amelyek nem voltak nevű headerless forrásból származó mezők átnevezése. Kattintson a "Vizsgálat" az összevonás metaadat az ebben a példában három különböző forrásokból származó 132 összesen oszlopokban tekintheti meg:

![Végső Union átalakítási](media/data-flow/union333.png "Union 3")
