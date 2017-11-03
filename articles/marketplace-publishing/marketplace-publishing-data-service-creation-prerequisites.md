---
title: "Műszaki szükséges előfeltételek egy szolgáltatás létrehozása a piactér |} Microsoft Docs"
description: "Egy szolgáltatás telepítéséhez és az Azure piactéren eladásra létrehozására vonatkozó követelmények megértése"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: aaff609a-1cd1-4146-98f4-d04166b0fce0
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: 52827723477677bc292c645e2390c435fbad3ee4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="technical-pre-requisites-for-creating-a-data-service-offer-for-the-azure-marketplace"></a>Az Azure piactéren ajánlat műszaki szükséges előfeltételek adatok szolgáltatás létrehozása
> [!IMPORTANT]
> **Jelenleg dolgozunk már nem bevezetési bármely új adatszolgáltatás közzétevők. Új dataservices nem get jóváhagyott listaelem.** Ha egy SaaS-üzleti AppSource a közzétenni kívánt alkalmazás további információt talál [Itt](https://appsource.microsoft.com/partners). Ha egy infrastruktúra-szolgáltatási alkalmazások vagy fejlesztői szolgáltatás szeretne közzétenni az Azure piactérről, további információt talál [Itt](https://azure.microsoft.com/marketplace/programs/certified/).
> 
> 

A folyamat megkezdése előtt alaposan és megértettem, hogy hol és miért minden egyes lépést. Amennyire csak lehetséges, meg kell készítse elő a vállalati adatok és egyéb adatokat, töltse le a szükséges eszközök, és/vagy technikai összetevő létrehozása az ajánlat létrehozási folyamat megkezdése előtt.

Készen áll a megkezdése előtt a következő elemeket kell rendelkezniük:

## <a name="make-a-decision-on-what-technology-will-be-used-to-publish-your-data-service-offer"></a>A döntést a milyen technológiai történik az adatok szolgáltatás ajánlat közzététele
Eldöntheti, hogy a közzétevő több technológiák adatszolgáltatás Azure piactéren közzétételekor között. A fő támogatott technológiákra van az alábbiakban. Attól függetlenül történik milyen technológiai adatszolgáltatás közzétételére szolgál, a végfelhasználó használ fel az adatok a **OData-adatcsatorna** Azure piactér szolgáltatás által elérhetővé tett tárolókra. OData szolgáltatás található információ teljes [http://www.odata.org/](http://www.odata.org/)

## <a name="sql-azure-database"></a>Az SQL Azure-adatbázis
Adatkészlet készen áll az SQL Azure-ban, akkor a Publisher felelős. Szüksége lesz Azure előfizetés, kiépítése adatbázis megfelelő méretét, és töltse fel az adatokat az SQL Azure Database. A Publisher feladata továbbá képes adatok mindig naprakész állapotban tartása érdekében. További információ található Azure-szolgáltatásokhoz való előfizetés [https://azure.microsoft.com/services/sql-database/](https://azure.microsoft.com/services/sql-database/)

Ha az adatok áthelyezése az SQL Azure, az Azure piactéren táblák és nézetek is elérhetővé teheti. A közzétevő adhat meg, melyik táblák/nézetek és oszlopok érhetők el a végfelhasználói. A tartalomszolgáltató további is megadhatja, mely oszlopok szerint a végfelhasználói kérdezhetők le, és melyeket csak vissza a tartalom. Ez biztosítja, hogy a magas fokú rugalmasságot biztosít arról, hogy mely adatokat az adatbázisban elérhetővé tehető. Oszlopok esetében kérdezhetők le kell egy vagy több adatbázis indexek támogassa.

## <a name="rest-based-web-service"></a>REST-alapú webszolgáltatás
Támogatott protokollok: **csak HTTPS**

Meglévő REST-alapú szolgáltatások elérhetővé tehető az Azure piactéren keresztül. Az adatkészlet mindig kapcsolódik egy OData-adatcsatornához, a végfelhasználónak, mert az Azure piactér szolgáltatás kell tudni a szolgáltatás megfeleltetése egy OData-alapú szolgáltatás. Ennek érdekében a REST-alapú végpontok kell tenni az összes paraméterek HTTP paraméterekként.

A tartalom kell lennie egy ATOM választ rendelhető űrlapon. A szolgáltatások kell lennie az XML-formátumú, és csak válaszát ezért hasznos értékeket (például a rekordhalmaz) tartalmazó egy ismétlődő elemet tartalmaz. Az Azure piactér szolgáltatás fogja csomópontra van leképezve a ismétlődő csomópont a bejegyzés ATOM, a tartalom értékek be tulajdonság-csomópont a bejegyzés csomóponton belül.

Engedélyezési adatokat (például API fő, hitelesítési jogkivonat, stb.) meg kell adni a HTTP-paraméterként vagy a HTTP-fejlécben (kulcs-érték pár) – az egyszerű hitelesítés is támogatott. Érvényes kulcs meg kell adni, és az összes Azure piactéren keresztül alatt álló kérelmek kulcs használatával. Felhasználók figyelése és számlázási történik, az Azure piactér rétegben.

A szolgáltatás által visszaadott hibák kell leképezni a HTTP-állapotkódok. Abban az esetben, ha a szolgáltatás egy XML-kódot, amely tartalmazza a hiba, ezek lesznek képezhető le az Azure piactér szolgáltatás HTTP-állapotkódok eredményezzen.

## <a name="soap-based-web-services"></a>Alapú SOAP-webszolgáltatások
Protokoll: **csak HTTPS**

A vonatkozó követelmények nincsenek ugyanaz, mint a többi szolgáltatás szakasz alapján. Az egyetlen különbség az, hogy paraméter is megadható az Azure piactéren keresztül minden kérelmet a közzétevő szolgáltatás feladási XML törzsében. Ez azt jelenti, hogy a felhasználó biztosítja az előtér-HTTP-paraméterek a kéréshez a tartalomszolgáltató webszolgáltatáshoz feladott XML-dokumentum XML-elemek folyamatban fordításának.

## <a name="odata-based-web-services"></a>Az OData-alapú webes szolgáltatások
Protokoll: **csak HTTPS**

Adatok elérhetővé tehető OData-szolgáltatásként az Azure piactéren. A rendszer továbbítja a szolgáltatás keresztül lesz, és a szolgáltatás a legfelső szintű helyére az Azure piactér gyökerén – annak érdekében, hogy minden ezt követő hívások Azure piactéren keresztül halad.

OData-szolgáltatásaival csak nyissa meg a a háttérbeli adatbázis nem szükséges. OData bármilyen típusú vagy tárolási logikai alapjául a szolgáltatás támogatja.

## <a name="next-steps"></a>Következő lépések
Most, hogy tekintse át az Előfeltételek és a szükséges műveletek végrehajtása, áthelyezheti előre létrehozásának részletezett adatszolgáltatás ajánlatát a [adatok szolgáltatás közzétételi útmutató](marketplace-publishing-data-service-creation.md).

Vagy, ha meg szeretné tekinteni a folyamat és a megfelelő cikkek minden közzétételi fázisait, látogasson el a cikk [első lépések: az ajánlat közzététele az Azure piactéren](marketplace-publishing-getting-started.md).

[link-acct]:marketplace-publishing-accounts-creation-registration.md
