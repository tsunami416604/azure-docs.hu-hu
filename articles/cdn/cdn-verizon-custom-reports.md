---
title: Verizon egyéni jelentések |} Microsoft Docs
description: 'Megtekintheti használati minták a CDN a következő jelentések segítségével: sávszélesség, adatokat továbbít, a találatok, gyorsítótár állapotok, gyorsítótári találati aránya, IPV4/IPV6 adatokat továbbít.'
services: cdn
documentationcenter: ''
author: dksimpson
manager: ''
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: v-deasim
ms.openlocfilehash: f09195dc07a96ebcca7f7a9e4bcf521fae13630c
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
ms.locfileid: "26343888"
---
# <a name="custom-reports-from-verizon"></a>Verizon egyéni jelentések

[!INCLUDE[cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Verizon egyéni jelentések segítségével a kezelés portálon Verizon profilokhoz, megadhatja a peremhálózati CNAME jelentések gyűjtendő adatok típusát.


## <a name="accessing-verizon-custom-reports"></a>Verizon egyéni jelentések használata
1. A CDN-profil panelje, kattintson a **kezelése** gombra.
   
    ![CDN-profil Manage gomb](./media/cdn-reports/cdn-manage-btn.png)
   
    Megnyitja a CDN-felügyeleti portálon.
2. Vigye a **Analytics** lapra, és vigye a **egyéni jelentések** menü. Kattintson a **CNAME él**.
   
    ![Kezelési portál CDN - egyéni jelentések menü](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Peremhálózati CNAME egyedi jelentés
A peremhálózati CNAME egyéni jelentés biztosít a találatok és a statisztika átvitt adatok peremhálózati CNAME, amelyen az egyéni naplózás engedélyezve van. Peremhálózati CNAME Azure CDN-végpont állomásnevek és bármely tartozó egyéni tartomány állomásnevek állnak. 

Egyéni jelentés adatok naplózásának engedélyezése az edge CNAME egyéni jelentéskészítési képességet után egy órán keresztül kezdődik. Jelentés adatainak megtekintéséhez egy peremhálózati CNAME jelentés egy adott platform vagy az összes platformra létrehozásakor. A jelentés a szervezet a peremhálózati CNAME, amelyhez egyéni jelentés adatokat gyűjtött a program a megadott időtartam alatt korlátozódik. A metrika a metrikák beállítás meghatározása szerint egy grafikonon és adattáblát 10 tetejéhez CNAME a peremhálózaton CNAME jelentés áll. 

Egyéni jelentés készítése a következő jelentés beállításainak megadásával:

- Metrikák: A következő beállításokat támogatja:

   - Találatok: Azt jelzi, hogy egy biztonsági CNAME, amelyeken engedélyezve van az egyéni jelentéskészítési képességet irányított kérelmek teljes száma. Ez a metrika nem tartalmazza az állapotkódot adott vissza az ügyfélnek.

   - Adatok átutalt: A HTTP-ügyfelek (például a webböngészők) a peremhálózati kiszolgálókról átvitt adatok teljes mennyisége egy peremhálózati CNAME, amelyeken engedélyezve van az egyéni jelentéskészítési képességet irányított kérelmek jelzi. Az átvitt adatok mennyisége HTTP-válaszfejlécek ad hozzá az adott válasz törzsének kiszámítása. Ennek eredményeképpen a minden eszköz átvitt adatok mérete nagyobb, mint a fájl tényleges mérete.

- Csoportosítás: Határozza meg a statisztikai adatokat tartalmaz a sávdiagram alább látható. A következő beállításokat támogatja:

   - A HTTP válaszkódot: Statisztikák rendszerezi által HTTP-válaszkód (például 200, 403-as, stb.) az ügyfél számára. 

   - Gyorsítótár állapota: Statisztikák gyorsítótár állapot szerint rendezi.


A jelentés dátumtartományának beállítása, kiválaszthat vagy egy előre definiált dátumtartományt, például a **Ma** vagy **ezen a héten**, a legördülő listából, vagy meg is kiválaszthat **egyéni** és a naptár ikonok kattintva manuálisan adjon meg egy dátumtartományt. 

Miután kiválasztotta a dátumtartományt, kattintson a **Ugrás** jelentést.

Exportálhatja az Excel formátumú adatokat az Excel szimbólum jobb oldalán kattintson a **Ugrás** gombra.

![CNAME-jelentés](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Peremhálózati egyéni CNAME jelentésmezők

| Mező                     | Leírás   |
|---------------------------|---------------|
| 2xx                       | A kéréseket, és az átvitt adatok (MB) a peremhálózati CNAME REKORDOT, amely egy 2xx HTTP-állapotkódot eredményez a teljes számát jelöli (például 200 OK). |
| 3xx                       | A kéréseket, és az átvitt adatok (MB) a peremhálózati CNAME REKORDOT, amely egy 3xx HTTP-állapotkódot eredményez a teljes számát jelöli (például 302 található vagy 304 nem módosultak. |
| 4xx                       | A kéréseket, és az átvitt adatok (MB) a peremhálózati CNAME REKORDOT, amely egy 4xx HTTP-állapotkódot eredményez a teljes számát jelöli (például 400 Hibás kérelem, 403 Tiltott vagy 404-es nem található). |
| 5XX                       | Jelzi a kéréseket, és az átvitt adatok (MB) teljes számát a szegély CNAME REKORDOT, amely egy 5xx HTTP-állapotkódot (például 500 belső kiszolgálóhiba vagy 502 Hibás átjáró) eredményez. |
| -Gyorsítótárának találati százaléka               | Azt jelzi, hogy a kérelmező gyorsítótár-ről volt szolgáltatott kérelmeznék kérelem azon százaléka. |
| Gyorsítótárbeli találatok                | Azt jelzi, hogy a kéréseket, és az átvitt adatok (MB) a peremhálózati CNAME REKORDOT, amely egy találati aránya (például TCP_EXPIRED_HIT, TCP_HIT vagy TCP_PARTIAL_HIT) eredményez a teljes száma. A gyorsítótár-találat következik be, amikor a kért tartalom egy gyorsítótárazott verziója található. |
| Az átvitt adatmennyiség (MB)     | Azt jelzi, hogy a teljes az átvitt adatok mennyisége (MB) a peremhálózati kiszolgálókról HTTP ügyfelek (webböngésző) a peremhálózati CNAME felé. Az átvitt adatok mennyisége kiszámítása a HTTP-válaszfejlécek ad hozzá az adott válasz törzse. Ennek eredményeképpen a minden eszköz átvitt adatok mérete nagyobb, mint a fájl tényleges mérete. |
| Leírás               | Az állomásnév szerint él CNAME azonosítja. |
| Találatok                      | Azt jelzi, a peremhálózati CNAME kérelmek teljes száma |
| Tévesztések                    | Azt jelzi, hogy a kéréseket, és az átvitt adatok (MB) a peremhálózati CNAME REKORDOT, amely egy gyorsítótár-tévesztései (például TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS vagy TCP_MISS) eredményez a teljes száma. A gyorsítótár-tévesztései következik be, amikor a kért tartalom nem került a gyorsítótárba, figyelembe véve a kérelem a biztonsági kiszolgálón. | 
| Gyorsítótár                  | Azt jelzi, hogy a kéréseket, és az átvitt adatok (MB) a peremhálózati CNAME REKORDOT, amely egy CONFIG_NOCACHE gyorsítótár állapotkódot eredményez a teljes száma.  |
| Egyéb                     | Azt jelzi, hogy a kéréseket, és a 2xx - 5xx tartományon kívül eső HTTP-állapotkódot eredményez átvitt (MB) a peremhálózati CNAME megjelölt adatok teljes száma. |
| Platform                  | Azt jelzi, hogy a platform, amely a peremhálózati CNAME-forgalmat kezeli. |
| Nincs hozzárendelve               | A peremhálózati CNAME melyik gyorsítótár állapotkód vagy a HTTP-állapotkód: információ nem naplózza a kéréseket, és az átvitt adatok (MB) teljes számát jelzi.  |
| Uncacheable               | Azt jelzi, hogy a kéréseket, és az átvitt adatok (MB) a peremhálózati CNAME REKORDOT, amely egy UNCACHEABLE gyorsítótár állapotkódot eredményez a teljes száma.  |


## <a name="considerations"></a>Megfontolandó szempontok
Jelentések csak az utolsó 18 hónapon belül létrehozható.

