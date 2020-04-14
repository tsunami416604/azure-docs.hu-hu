---
title: Egyéni jelentések a Verizontól | Microsoft dokumentumok
description: 'A CDN használati mintáit a következő jelentések segítségével tekintheti meg: Sávszélesség, Átvitt adatátvitel, találatok, gyorsítótár-állapotok, Gyorsítótár találati aránya, IPV4/IPV6 átvitt adatok.'
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: allensu
ms.openlocfilehash: 737803f5b7fcddb07787afbb48354b4ca922e9ed
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259748"
---
# <a name="custom-reports-from-verizon"></a>Egyéni jelentések a Verizontól

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

A Verizon egyéni jelentések használatával a Verizon-profilok kezelése portálon keresztül meghatározhatja a peremhálózati CNAMEs-jelentésekhez gyűjtendő adatok típusát.


## <a name="accessing-verizon-custom-reports"></a>A Verizon egyéni jelentései elérése
1. A CDN-profilpanelen kattintson a **Kezelés** gombra.
   
    ![CDN-profil kezelés gombja](./media/cdn-reports/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portál.
2. Mutasson az **Analytics** fülre, majd mutasson az **Egyéni jelentések** úszó panelre. Kattintson **az Edge CNAMEs gombra.**
   
    ![CDN felügyeleti portál – Egyéni jelentések menü](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>SzegélycNAMES egyéni jelentés
Az Edge CNAMES egyéni jelentés találatokat és adatátviteli statisztikákat biztosít azon peremhálózati cnames-ek esetében, amelyeken engedélyezve van az egyéni jelentésnaplózás. Az edge CNAMEs Azure CDN-végpontállomásnevekből és bármely kapcsolódó egyéni tartományállomásnévből áll. 

Az egyéni jelentésadatok naplózása egy órával a szegély CNAME egyéni jelentéskészítési képességének engedélyezése után kezdődik. A jelentésadatokat úgy tekintheti meg, hogy egy adott platformra vagy az összes platformra létrehoz egy edge CNAMEs-jelentést. A jelentés lefedettsége azokra a peremhálózati CNAMEs-re korlátozódik, amelyekhez a megadott időszakban egyéni jelentésadatokat gyűjtöttek. A peremhálózati CNAMEs-jelentés egy grafikonból és egy adattáblázatból áll a felső 10 peremhálózati CNAMEs számára a Metrikák beállításban meghatározott metrika szerint. 

Egyéni jelentés készítése a következő jelentésbeállítások meghatározásával:

- Mérőszámok: A következő beállítások támogatottak:

   - Lekérések: A kérelmek teljes számát jelzi, amelyek egy élCNAME-re irányulnak, amelyen az egyéni jelentéskészítési képesség engedélyezve van. Ez a metrika nem tartalmazza az ügyfélnek visszaadott állapotkódot.

   - Átvitt adatok: A peremhálózati kiszolgálókról a HTTP-ügyfelekre (például webböngészőkre) átvitt adatok teljes mennyiségét jelzi olyan kérelmek esetében, amelyek egy olyan peremhálózati CNAME-re vannak irányítva, amelyen az egyéni jelentéskészítési funkció engedélyezve van. Az átvitt adatok mennyiségét úgy számítja ki a rendszer, hogy HTTP-válaszfejléceket ad hozzá a választörzshöz. Ennek eredményeképpen az egyes eszközökre átvitt adatok mennyisége nagyobb, mint a tényleges fájlméret.

- Csoportosítások: Meghatározza a sávdiagram alatt megjelenő statisztikák típusát. A következő beállítások támogatottak:

   - HTTP-válaszkódok: A statisztikákat http-válaszkód (például 200, 403 stb.) szerint rendezi, amelyet az ügyfélnek ad vissza. 

   - Gyorsítótár állapota: A statisztikákat gyorsítótár állapota szerint rendezi.


A jelentés dátumtartományának beállításához válasszon ki egy előre definiált dátumtartományt (például **Ma** vagy Ezen a **hét**) a legördülő listából, vagy válassza az **Egyéni** lehetőséget, és manuálisan adjon meg egy dátumtartományt a naptár ikonokra kattintva. 

Miután kiválasztotta a dátumtartományt, kattintson az **Ugrás** gombra a jelentés létrehozásához.

Az adatokat Excel formátumban exportálhatja, ha az **Ugrás** gomb tól jobbra lévő Excel-szimbólumra kattint.

![CNAMEs-jelentés](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>SzegélycNAMES egyéni jelentésmezők

| Mező                     | Leírás   |
|---------------------------|---------------|
| 2xx                       | A 2xx HTTP-állapotkódot eredményező peremc CNAME-hez továbbított kérelmek vagy adatok teljes számát (MB) jelzi (például 200 OK). |
| 3xx között                       | A 3xx HTTP-állapotkódot eredményező peremc CNAME-hez továbbított kérelmek vagy adatok teljes számát (MB) jelzi (például 302 Talált vagy 304 nem módosított). |
| 4xx között                       | A 4xx HTTP-állapotkódot eredményező peremc CNAME-hez továbbított kérelmek vagy adatok teljes számát (MB) jelzi (például 400 hibás kérelem, 403 Tiltott vagy 404 nem található). |
| 5xx között                       | Az 5xx HTTP-állapotkódot eredményező peremű CNAME-hez továbbított kérelmek vagy adatok teljes számát (MB) jelzi (például 500 belső kiszolgálóhiba vagy 502 hibás átjáró). |
| Gyorsítótár találata %               | A gyorsítótárazható kérelmek százalékos arányát jelzi, amelyeket közvetlenül a gyorsítótárból kézbesíttek a kérelmezőnek. |
| Gyorsítótár-találatok                | A gyorsítótár-lekérést eredményező cname peremre vonatkozó kérelmek vagy adatok (MB) teljes számát jelzi,TCP_EXPIRED_HIT, TCP_HIT vagy TCP_PARTIAL_HIT). Gyorsítótár-lekérés akkor történik, amikor a kért tartalom gyorsítótárazott verziója található. |
| Átvitt adatok (MB)     | A peremhálózati kiszolgálókról http-ügyfelekre (webböngészőkbe) átvitt adatok teljes mennyiségét jelzi a peremhálózati CNAME számára. Az átvitt adatok mennyisége a HTTP-válaszfejlécek választörzshöz való hozzáadásával történik. Ennek eredményeképpen az egyes eszközökre átvitt adatok mennyisége nagyobb, mint a tényleges fájlméret. |
| Leírás               | Egy él CNAME-t azonosít az állomásneve alapján |
| Találatok                      | A cNAME széllel kapcsolatos kérelmek teljes számát jelzi. |
| Hiányzik                    | A gyorsítótár-tévesztést eredményező hálózati CNAME-hez továbbított kérelmek vagy adatok teljes számát (MB) jelzi (például TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS vagy TCP_MISS). Gyorsítótár-tévesztés akkor következik be, amikor a kért tartalom nem lett gyorsítótárazva a kérést megtisztelő peremhálózati kiszolgálón. | 
| Nincs gyorsítótár                  | A gyorsítótár állapotkódjának CONFIG_NOCACHE eredményező, a külső CNAME-hez továbbított kérelmek vagy adatok teljes számát (MB) jelzi.  |
| Egyéb                     | A CNAME peremre átvitt kérelmek vagy adatok teljes számát (MB) jelzi, hogy a 2xx - 5xx tartományon kívül rekedő HTTP-állapotkód jelenik meg. |
| Platform                  | Azt a platformot jelzi, amely a CNAME peremvonalát kezeli. |
| Nincs hozzárendelve               | Azon szegélyű CNAME-hez továbbított kérelmek vagy adatok teljes számát (MB) jelzi, amelyekgyorsítótár állapotkódját vagy HTTP-állapotkódadatait nem naplózták.  |
| Nem gyorsítótárazható               | A hálózati CNAME-hez továbbított kérelmek vagy adatok teljes számát (MB) jelzi, amelyek nem gyorsítótárazható gyorsítótár állapotkódot eredményeznek.  |


## <a name="considerations"></a>Megfontolandó szempontok
Jelentések csak az elmúlt 18 hónapban készíthetők.

