---
title: Útmutató a személyes adatok tárolódnak az Azure Naplóelemzés |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan kezelheti az Azure Naplóelemzés és a metódusok azonosításához és eltávolításához található személyes adatokat.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 3692c83a4991fc67ec176687bd076ab14e4c640d
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129370"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics"></a>A Naplóelemzési személyes adataihoz útmutatást

A Naplóelemzési egy adattárból, ahol a személyes adatok várhatóan található. Ez a cikk bemutatja, hogyan lehet ahol Naplóelemzési az ilyen adatok található, továbbá az ilyen adatok kezelésének elérhető lehetőségeket.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Személyes adatok kezelésére stratégia

Akár Ön és vállalata végső soron meghatározásához a stratégiát, amellyel a saját kezelnek lesz adatok (ha egyáltalán), amíg az alábbiakban néhány lehetséges módszer. Egy technikai szempontból a leginkább preferencia szerinti sorrendben szerepelnek a legalább előnyösebb:

* Ahol lehetséges, állítsa le a gyűjteménye, takarják, anonimizálásához vagy más módon állítsa be a "privát" szabálysértésnek minősülő kizárása gyűjtött adatokra. Ez az _messze_ az előnyben részesített módszert használja, így meg lehet spórolni kell létrehozni egy nagyon költséges és impactful adatkezelési stratégia.
* Ha nem lehetséges, próbálja meg az adatok csökkenteni a hatással lehet az adatok platform és a teljesítmény optimalizálása. Naplózás egy explicit felhasználói Azonosítóját, helyett hozzon létre például egy keresési adatok fog gyűjtött, a felhasználónév és a Részletek gombra, majd bejelentkezhet máshol belső azonosító. Ily módon egy felhasználó kérdezze meg személyes adataik törlését is lehet, hogy a csak a a keresési táblában a felhasználónak megfelelő a sor törlésével elegendő lesz. 
* Ha a titkos adatokat kell gyűjteni, végezetül fordítsa le a folyamat a kiürítési API elérési út és a meglévő lekérdezés API elérési út bármely kötelezettségek, előfordulhat, hogy exportálása és a felhasználóhoz társított titkos adatok törlése kielégítése érdekében. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Hol személyes adatokat a Log Analytics?

A Naplóelemzési egy rugalmas tárolás közben az adatok egy séma előíró lehetővé teszi minden mezőnek egyedi értékekkel felülbírálását. Emellett minden egyéni séma meghatározták. Ilyen lehetetlen tegyük fel például, pontosan ahol titkos adatok találhatók az adott munkaterületen. A következő helyeken, azonban nem jó kezdőpont a leltárban:

* *IP-címek*: Naplóelemzési különböző IP-adatokat gyűjt között számos különböző táblákhoz. Például a következő lekérdezés látható, minden olyan táblát IPv4-címeket az elmúlt 24 óra során összegyűjtött ahol:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *Felhasználói azonosítók*: felhasználói azonosítók számos megoldások és táblák találhatók. Egy adott felhasználónévhez között a teljes adatkészletet, a keresési parancs használatával tekintheti meg:
    ```
    search "[username goes here]"
    ```
Jegyezze meg, nem csak az emberek számára olvasható felhasználóneveket, hanem közvetlenül visszakövethető egy adott felhasználó GUID-EK!
* *Eszközazonosítókat*: például "felhasználói azonosítók, eszközazonosítókat néha titkosnak számítanak". Használja ugyanezt a megközelítést felhasználói azonosítók a fenti táblák azonosítása, ahol ez a probléma, amely lehet. 
* *Egyéni adatok*: Naplóelemzési lehetővé teszi, hogy a különböző módszereket a gyűjteményben: egyéni naplókat és az egyéni mezők a [HTTP adatait gyűjtője API](log-analytics-data-collector-api.md) , és egyéni adatgyűjtés részeként a rendszer eseménynaplóit. Ezek mind ki vannak téve a személyes adatokat tartalmazó, és ellenőrizheti, hogy létezik-e ilyen jellegű adatokat meg kell vizsgálni.
* *Megoldás rögzített adatok*: mivel a megoldás mechanizmus a nyílt egyikét, azt javasoljuk, tekintse át a megfelelőség biztosítása megoldások által létrehozott összes tábla.

## <a name="how-to-export-and-delete-private-data"></a>Hogyan kell exportálni, és a személyes adatok törlése

Ahogyan az a [stratégia személyes adatok kezelésére](#strategy-for-personal-data-handling) szakasz korábbi, a __erősen__ ajánlott, ha az összes lehetséges átalakításának az adatvédelmi nyilatkozat a gyűjtésének a letiltása titkos adatok, obfuscating vagy névtelenítését azt, vagy ellenkező esetben módosítsa "privát" szabálysértésnek minősülő eltávolítása. Az adatok fognak legelső eredményt Ön és a csoport határozza meg és automatizálhatja a stratégia, build egy felület, az ügyfelek keresztül az adatok kezeléséhez, és folyamatos karbantartási költségek kezelésére. További számításilag költséges Naplóelemzési, és a nagy egyidejű lekérdezés vagy kiürítése API-hívások esetleg negatívan Naplóelemzési funkciójú minden más tevékenység. Amely az említett, valóban néhány érvényes esetben hova legyenek összegyűjtve személyes adatokat. Ezekben az esetekben az adatok a kezelt ebben a szakaszban leírtak szerint.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Megtekintése és exportálása

Az is megtekintheti, és exportálhatja a kérelmek, a [lekérdezés API](https://dev.loganalytics.io/) kell használni. Az alakzat az adatok átalakítása telepíthet a felhasználók számára egy megfelelő logika végrehajtásához Öntől lesz. [Az Azure Functions](https://azure.microsoft.com/services/functions/) lehetővé teszi az ilyen programot állomás nagyszerű lehetőséget.

### <a name="delete"></a>Törlés

> [!WARNING]
> Törli a Naplóelemzési ilyen beállítások mellett pusztító és nem visszafejthető! A végrehajtási körültekintően használja.

Azt elérhetővé tett egy adatvédelmi kezelési részeként egy *kiürítése* API elérési útja. Az elérési út takarékosan a kockázatának így, mert a teljesítményre gyakorolt lehetséges hatásának, és így döntés teljes összesítéseket, mérések és egyéb szempontok Naplóelemzési adatait. Tekintse meg a [stratégia személyes adatok kezelésére](#strategy-for-personal-data-handling) személyes adatok kezelésének alternatív módszerek szakaszát.

Kiürítési egy olyan magas szintű jogosultsággal rendelkező művelet, hogy egyetlen alkalmazás vagy a felhasználó (beleértve az erőforrás tulajdonosa is) az Azure-ban végrehajtása nélkül explicit módon megadott egy szerepkört az Azure Resource Manager engedélyeket kapnak. Ez a szerepkör _adatok Purger_ és az esetleges adatvesztés óvatosan delegálni. 

Miután az Azure erőforrás-kezelő szerepkör van hozzárendelve, két új API-útvonalon érhetők el: 

* [Utáni kiürítése] (https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) - adatok törlése paramétereinek megadása objektum vesz igénybe, és visszaad egy hivatkozást GUID 
* GET kiürítése állapota - a FELADÁS egy vagy több kiürítése hívás ad vissza egy "x-ms-állapot-location" fejlécet, amelyek tartalmazzák azt egy URL-címet, amely nem sikerült meghatározni az állapotát a kiürítési API hívása. Példa:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperatonalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

Miközben sokkal gyorsabb, mint az SLA-t, a Log Analyticshez által használt adatok platform nehéz gyakorolt hatásának miatt befejezéséhez kiürítése műveletek túlnyomó várhatóan a formális kiürítése műveletek befejezésének SLA nem állítják 30 nap. 

## <a name="next-steps"></a>További lépések
Milyen adatok gyűjtése, feldolgozása, és biztosított kapcsolatos további információkért lásd: [Log Analytics-adatok biztonsági](log-analytics-data-security.md).