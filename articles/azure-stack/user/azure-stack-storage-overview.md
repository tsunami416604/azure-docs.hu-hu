---
title: A verem Azure storage bemutatása
description: Azure verem tárolás
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: 092aba28-04bc-44c0-90e1-e79d82f4ff42
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: mabrigg
ms.openlocfilehash: d97a5f8aff57f4bbfd7d5222a87d258fa5c92da8
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604386"
---
# <a name="introduction-to-azure-stack-storage"></a>A verem Azure storage bemutatása

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

## <a name="overview"></a>Áttekintés

Verem az Azure Storage olyan tárolási felhőszolgáltatások, amely tartalmazza a BLOB, a táblák és a várólisták, amelyek megegyeznek az Azure Storage szolgáltatás.

## <a name="azure-stack-storage-services"></a>Az Azure verem tárolási szolgáltatások

Verem az Azure storage a következő három szolgáltatást biztosítja:

- **Blob Storage**

    A BLOB storage a strukturálatlan objektumadatokat tárolja. Egy blob állhat bármilyen szövegből vagy bináris adatból, lehet például egy dokumentum, egy médiafájl vagy egy alkalmazástelepítő.

- **Table Storage**

    A TABLE storage a strukturált adatkészleteket tárolja. A Table Storage a NoSQL-kulcsattribútumok adattára, amely gyors fejlesztési lehetőségeket és nagy adatmennyiségek gyors elérését biztosítja.

- **A Queue Storage**

    A Queue storage biztosít a munkafolyamat feldolgozásra, és a felhőszolgáltatások összetevői közötti kommunikáció megbízható üzenetküldés.

Egy Azure verem tárfiók olyan biztonságos fiók, amely lehetővé teszi az Azure verem Storage szolgáltatásaihoz való hozzáférést. A tárfiók biztosítja az egyedi névteret a tárterület erőforrásainak. Az alábbi ábrán látható tárfiókokban a verem Azure storage erőforrásainak kapcsolatai:

![Az Azure tárolási verem – áttekintés](media/azure-stack-storage-overview/AzureStackStorageOverview.png)

### <a name="blob-storage"></a>Blob Storage

A felhasználók számára a felhőben tárolt nagy mennyiségű strukturálatlan adatok a blob storage egy hatékony és méretezhető megoldást kínál. A blob storage segítségével tárolhatja a tartalmakat, mint:

- Dokumentumok
- Közösségi adatok (fényképek, videók, zene és blogok)
- Fájlok, számítógépek, adatbázisok és eszközök biztonsági másolatai
- Webalkalmazásokhoz tartozó képek és szövegek
- Konfigurációs adatok a felhőalapú alkalmazásokhoz
- Big data (naplók és egyéb nagy adatkészletek)

Minden blob egy tárolóba van rendezve. A tárolók nagy előnye, hogy az objektumok csoportjaihoz biztonsági házirendeket lehet rendelni. A storage-fiók korlátlan számú tárolót tárolhat tartalmazhat, és egy tároló korlátlan számú blobot, legfeljebb a tárfiók is tartalmazhat.

BLOB storage háromféle blobot biztosít:

- **Blokkblobok**

    Blokkblobok streaming és felhő objektumok tárolására vannak optimalizálva, és olyan tárolására, dokumentumok, médiafájlok, biztonsági mentések és stb.

- **Hozzáfűző blobokat**

    A hozzáfűző blobok a blokkblobokhoz hasonló, de a műveletek hozzáfűzésére optimalizált blobok. A naplóíró blobok csak a végükhöz hozzáadott új blokkal frissíthetők. A naplóíró blobok olyan forgatókönyvekhez felelnek meg leginkább, mint például a naplózás, ahol az új adatokat csak a blob végéhez kell hozzáírni.

- **Lapblobokat**

    A lapblobok az infrastruktúra-szolgáltatási lemezek optimalizált blobok, amelyek, és támogató véletlenszerű írási ez akár 1 TB-nál. Egy Azure verem virtuális géphez csatlakoztatott infrastruktúra-szolgáltatási lemez oldalblobként tárolt virtuális merevlemez.

### <a name="table-storage"></a>Table Storage

A modern alkalmazásokhoz gyakran az előző generációs szoftvereknél jobban méretezhető és rugalmasabb adattárolók szükségesek. A Table Storage magas rendelkezésre állású, nagymértékben méretezhető tárolót kínál, így alkalmazása a felhasználói igények függvényében automatikusan átméretezheti magát. A TABLE storage a Microsoft NoSQL kulcs-/ attribútumtár – a séma nélküli kialakítást, így a hagyományos relációs adatbázisoktól különböző rendelkezik. A séma nélküli adattárral az adatok könnyedén alkalmazkodnak az alkalmazás igényeinek fejlődéséhez. A Table Storage könnyen használható, így a fejlesztők gyorsan létrehozhatják benne alkalmazásaikat.

A TABLE storage egy kulcs-/ attribútumtár, ami azt jelenti, hogy a tábla minden egyes értékhez egy típusos tulajdonságnévvel tárolja. A tulajdonságnév felhasználható szűréshez vagy a kiválasztási feltételek megadásához is. A tulajdonságok és értékeik gyűjteménye egy entitást alkot. Mivel a table storage séma nélküli, ugyanaz a tábla két entitása a Tulajdonságok különböző gyűjteményeit tartalmazhatja, és ezek a Tulajdonságok különböző típusúak lehetnek.

A table storage segítségével rugalmas adatkészleteket tárolhat, például webes alkalmazásokat, címtárakat, eszközadatokat és bármilyen más típusú metaadatokat, amelyek a szolgáltatásnak szüksége van a felhasználói adatokat. A mai internetalapú alkalmazásoknál a table storage hasonló NoSQL-adatbázisok a hagyományos, relációs adatbázisok népszerű alternatíváját kínálják.

A storage-fiók korlátlan számú táblát tartalmazhat, és egy táblát tartalmazhat korlátlan számú entitást, a tárfiók a kapacitásán.

### <a name="queue-storage"></a>Queue Storage

A méretezhető alkalmazások tervezésekor az alkalmazás összetevői gyakran le vannak választva, hogy egymástól függetlenül lehessen őket méretezni. A Queue storage megbízható üzenetkezelési megoldást nyújt a alkalmazás-összetevő közötti aszinkron kommunikációhoz, hogy azok a felhőben, az asztalon, egy helyszíni kiszolgálón vagy egy mobileszközön futtatja. A Queue Storage támogatja az aszinkron feladatok kezelését és a feldolgozási munkafolyamatok kialakítását is.

A storage-fiókok várólisták tetszőleges számú tartalmazhat, és korlátlan számú üzenetet, a tárfiók a kapacitásán tartalmazhat. Az egyes üzenetek akár a 64 KB-os méretet is elérhetik.

## <a name="next-steps"></a>További lépések

- [Azure-konzisztens tárolási: különbségek és szempontok](azure-stack-acs-differences.md)

- Azure Storage kapcsolatos további információkért lásd: [Microsoft Azure Storage bemutatása](../../storage/common/storage-introduction.md)
