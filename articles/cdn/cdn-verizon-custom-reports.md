---
title: Egyéni jelentések a Verizontól |} A Microsoft Docs
description: 'Az alábbi jelentések használatával a CDN használati mintáinak is megtekintheti: a sávszélesség, átvitt adatok, a találatok, gyorsítótárak állapota, gyorsítótár találati aránya, IPV4/IPV6 átvitt adatok.'
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: magattus
ms.openlocfilehash: 75a1fb4beb4025dc3ec63d29944331d07be35d8a
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092066"
---
# <a name="custom-reports-from-verizon"></a>Egyéni jelentések a Verizontól

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Verizon egyéni jelentések segítségével a kezelés portálján Verizon profilokhoz, megadhatja az edge CNAME-rekordokat jelentések gyűjtendő adatok típusát.


## <a name="accessing-verizon-custom-reports"></a>Verizon egyéni jelentések elérése
1. A CDN-profil panelje, kattintson a **kezelés** gombra.
   
    ![A CDN-profil felügyelet gombra](./media/cdn-reports/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portálját.
2. A kurzort a **Analytics** lapfülre, majd mutasson a **egyéni jelentések** úszó menü. Kattintson a **CNAME-rekordokat él**.
   
    ![CDN felügyeleti portálját – egyéni jelentések menüben](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Edge CNAME-REKORDOKAT egyéni jelentés
Az Edge CNAME-REKORDOKAT egyéni jelentés edge CNAME-rekordokat, amelyen a jelentés egyéni naplózás engedélyezve van a találatok és az átvitt adatok statisztikai tartalmazza. Edge CNAME-rekordokat az Azure CDN-végpont állomásnevek és a kapcsolódó egyéni tartomány gazdanév állnak. 

Egyéni jelentés naplózása egy edge CNAME egyéni jelentéskészítési funkció engedélyezése után egy órán keresztül kezdődik. Jelentés adatai egy adott platformhoz tartozó vagy az összes platformra Edge CNAME-rekordokat a jelentés létrehozásával tekintheti meg. Ez a jelentés tartozó lefedettséget korlátozódik a peremhálózati CNAME-rekordokat, amelyhez egyéni jelentés adatai gyűjtötte a program a megadott időtartam alatt. Az edge CNAME-rekordokat a jelentés a metrikák paneljén a meghatározott metrika szerint a felső 10 edge CNAME-rekordokat a graph és az adatok táblázatként áll. 

Egyéni jelentés létrehozása a következő jelentés beállítások megadásával:

- Metrikák: A következő beállítások támogatottak:

   - Találatok: Azt jelzi, hogy az edge CNAME, amelyen engedélyezve van az egyéni jelentéskészítési képességet irányított kérelmek teljes száma. Ez a metrika nem tartalmazza az ügyfélnek visszaadott állapotkódot követi.

   - Adatok átutalt: Azt jelzi, hogy a HTTP-ügyfelekre (például a webböngészők) a peremhálózati kiszolgálókról történő átvitt adatok teljes mennyisége a kéréseket, amely egy edge CNAME, amelyen az egyéni jelentéskészítési funkció engedélyezve van a rendszer átirányítja. Az átvitt adatok mennyisége HTTP-válaszfejlécek ad hozzá a válasz törzse számolható ki. Ennek eredményeképpen az egyes eszközök továbbított adatok mennyisége nagyobb, mint a fájl tényleges méretével.

- Csoportosítás: Határozza meg a statisztikát, amely a sávdiagram alább láthatók. A következő beállítások támogatottak:

   - HTTP-válaszkódot: HTTP-válaszkód szerint rendezi a statisztika (Ha például 200-as, 403, stb.), az ügyfél. 

   - Gyorsítótár állapota: Statisztikák gyorsítótár állapot szerint rendezi.


Hogy a jelentés a dátumtartományt, vagy egy előre definiált dátum tartományt választhat, például **Ma** vagy **ezen a héten**, a legördülő listából, vagy választhat **egyéni** és a naptár-ikonokat kattintva manuálisan adjon meg egy dátumtartományt. 

Miután kiválasztotta a dátumtartományt, kattintson a **Go** a jelentés létrehozásához.

Az Excel szimbólum jobbra található gombra kattintva exportálhatja az adatokat az Excel-formátumban a **Go** gombra.

![CNAME-rekordokat a jelentés](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Edge CNAME-REKORDOKAT a jelentés egyéni mezők

| Mező                     | Leírás   |
|---------------------------|---------------|
| 2xx                       | Azt jelzi, hogy a kérések vagy az átvitt adatok (MB) az Edge-hez, amely a 2xx HTTP-állapotkódot eredményez CNAME teljes száma (például 200 OK). |
| 3xx                       | Azt jelzi, hogy a kérések vagy az átvitt adatok (MB) az Edge-hez, amely egy 3xx HTTP-állapotkódot eredményez CNAME teljes száma (például 302 található vagy a 304 nem módosulnak. |
| 4xx                       | Azt jelzi, hogy a kérések vagy az átvitt adatok (MB) az Edge-hez, amely egy HTTP 4xx típusú állapotkódot eredményez CNAME teljes száma (például 400 Hibás kérés, 403 Tiltott vagy 404-es nem található). |
| 5XX                       | Az Edge-hez, amelyek egy 5XX kódú HTTP-állapotkódot (például 500 belső kiszolgálóhiba vagy 502 Hibás átjáró) CNAME kérések vagy az átvitt adatok (MB) teljes számát jelzi. |
| -Gyorsítótárának találati százaléka               | Azt jelzi, hogy közvetlenül a gyorsítótárból is rendereléséhez szükséges a kérelmező gyorsítótárazható kérelmek aránya. |
| Gyorsítótárbeli találatok                | Azt jelzi, hogy a kérések vagy az átvitt adatok (MB) az Edge-hez, amelyek taggyorsítótárának találati aránya (például TCP_EXPIRED_HIT, TCP_HIT vagy TCP_PARTIAL_HIT) CNAME teljes száma. Egy taggyorsítótárának találati aránya akkor fordul elő, ha egy gyorsítótárazott verziója a lekért tartalom megtalálható. |
| Átvitt adatok (MB)     | Azt jelzi, hogy az összes átvitt adatok mennyisége (MB) a peremhálózati kiszolgálókról történő az Edge CNAME (webböngésző) HTTP-ügyfelek. Az átvitt adatok mennyisége a válasz törzse ad hozzá a HTTP-válaszfejlécek számolható ki. Ennek eredményeképpen az egyes eszközök továbbított adatok mennyisége nagyobb, mint a fájl tényleges méretével. |
| Leírás               | Azonosítja az CNAME beállításával a peremhálózaton |
| Találatok                      | Azt jelzi, hogy az Edge-ben CNAME kérelmek teljes száma |
| Tévesztések                    | Azt jelzi, hogy a kérések vagy az átvitt adatok (MB) az Edge-hez, amely a gyorsítótár-tévesztés (például TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS vagy TCP_MISS) CNAME teljes száma. Gyorsítótár-tévesztés esetén az, hogy a kért tartalom nem gyorsítótárazva lett figyelembe véve a kérést a biztonsági kiszolgálón. | 
| Nincs gyorsítótárazás                  | Azt jelzi, hogy a kérések vagy az átvitt adatok (MB) az Edge-hez, amely CONFIG_NOCACHE gyorsítótár állapotkódot eredményez CNAME teljes száma.  |
| Egyéb                     | Azt jelzi, hogy a kérések vagy adatok átvitt (MB) az Edge CNAME jelzi, amely a 2xx - 5xx tartományon kívül esik HTTP-állapotkód eredményez teljes száma. |
| Platform                  | Azt jelzi, hogy azt a platformot, az edge CNAME forgalmat kezeli. |
| Nincs hozzárendelés               | Azt jelzi, a peremhálózati CNAME melyik gyorsítótár állapotkód vagy a HTTP-állapotkód: nem naplózott információ a kérések vagy az átvitt adatok (MB) teljes száma.  |
| Uncacheable               | Azt jelzi, hogy a kérések vagy az átvitt adatok (MB) az Edge-hez, amely egy UNCACHEABLE gyorsítótár állapotkódot eredményez CNAME teljes száma.  |


## <a name="considerations"></a>Megfontolandó szempontok
Jelentések csak az elmúlt 18 hónapban hozhatók létre.

