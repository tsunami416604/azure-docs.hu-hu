---
title: "Fájlok tömörítése Azure CDN szolgáltatás használata a jobb teljesítmény érdekében |} Microsoft Docs"
description: "Ismerje meg, hogyan lehet fokozni az fájl átviteli sebesség és az Azure CDN szolgáltatás használata a fájlok tömörítésével lap betöltési teljesítményét növeli."
services: cdn
documentationcenter: 
author: dksimpson
manager: akucer
editor: 
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: mazha
ms.openlocfilehash: 77d889f5d56ed839665588cf359b73e0f9ad28b5
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>A jobb teljesítmény érdekében az Azure CDN fájlok tömörítése
Tömörítés a fájl átviteli sebesség növelése és a lap betöltése teljesítmény növeléséhez a fájlméret, mielőtt a kiszolgáló egyszerű és hatékony módszer. Csökkenti a sávszélességgel kapcsolatos költségek, és gyorsabban élményt nyújt a felhasználók számára.

Két módon lehet engedélyezni a tömörítést:

* A forrás kiszolgálón tömörítésének engedélyezéséhez. Ebben az esetben a CDN a tömörített fájlok keresztül, és azokat kérő ügyfeleknek továbbítja azokat.
* Közvetlenül a CDN peremhálózati kiszolgálóinak tömörítésének engedélyezéséhez. Ebben az esetben a CDN tömöríti a fájlokat, és végfelhasználók számára, akkor is, ha a forráskiszolgáló nem tömöríti szolgál.

> [!IMPORTANT]
> CDN konfigurációs módosításokat a hálózaton belüli propagálásához hosszabb időt is igénybe vehet. A <b>Akamai Azure CDN</b> -profilok propagálása általában fejeződik be, egy perc alatt.  A <b>Azure CDN Verizon</b> -profilok propagálása általában befejezi 90 percen belül. Ha először a CDN-végpont tömörítési beállítása, várja meg, 1 – 2 óra, mielőtt a tömörítési beállítások propagálása a POP való biztosításához.
> 
> 

## <a name="enabling-compression"></a>Tömörítés engedélyezése
A Standard és prémium szintű CDN rétegek funkcionalitása azonos tömörítést, de a felhasználói felület eltér. Standard és prémium szintű CDN rétegek közötti különbségekről további információkért lásd: [Azure CDN áttekintése](cdn-overview.md).

### <a name="standard-tier"></a>Standard csomag
> [!NOTE]
> Ez a szakasz vonatkozik **Azure CDN Standard verizon** és **Azure CDN Standard Akamai** profilok.
> 
> 

1. A CDN-profil lapon válassza ki a kezelni kívánt CDN-végpont.
   
    ![CDN-profil végpontok](./media/cdn-file-compression/cdn-endpoints.png)
   
    A CDN-végpont lap nyílik meg.
2. Válassza ki **tömörítés**.

    ![CDN-profil végpontok](./media/cdn-file-compression/cdn-compress-select-std.png)
   
    A tömörítés lap nyílik meg.
3. Válassza ki **a** kapcsolja be a tömörítést.
   
    ![CDN-tömörítési beállítások](./media/cdn-file-compression/cdn-compress-standard.png)
4. Az alapértelmezett formátumot használja, vagy módosítsa a lista hozzáadásával vagy eltávolításával formátumtípust.
   
   > [!TIP]
   > Bár lehetséges, nem ajánlott tömörítés alkalmazása tömörített formátumban. Például ZIP, MP3, MP4 vagy JPG.
   > 
 
5. A módosítások elvégzése után válassza ki a **mentése**.

### <a name="premium-tier"></a>Premium szintű csomag
> [!NOTE]
> Ez a szakasz csak a vonatkozik **verizon Azure CDN Premium** profilok.
> 
> 

1. A CDN-profil lapon válassza ki a **kezelése**.
   
    ![CDN-profil kezelésére gomb](./media/cdn-file-compression/cdn-manage-btn.png)
   
    Megnyitja a CDN-felügyeleti portálon.
2. Vigye a **HTTP nagy** lapra, és vigye a **gyorsítótár beállításainak** menü. Válassza ki **tömörítés**.

    ![Fájl tömörítése kiválasztása](./media/cdn-file-compression/cdn-compress-select.png)
   
    Tömörítési beállítások jelennek meg.
   
    ![Fájl tömörítése beállítások](./media/cdn-file-compression/cdn-compress-files.png)
3. Tömörítés engedélyezése kiválasztásával **tömörítés engedélyezve**. A MIME-típusok tömörítendő (szóközök nélkül) vesszővel tagolt lista formájában adja meg a **fájltípusok** mezőbe.
   
   > [!TIP]
   > Bár lehetséges, nem ajánlott tömörítés alkalmazása tömörített formátumban. Például ZIP, MP3, MP4 vagy JPG.
   > 
    
4. A módosítások elvégzése után válassza ki a **frissítés**.

## <a name="compression-rules"></a>Tömörítés szabályok

### <a name="azure-cdn-from-verizon-profiles-both-standard-and-premium"></a>Az Azure CDN Verizon profilok (standard és prémium)

A **Azure CDN Verizon** profilok, csak a megfelelő fájlok tömörítése. Jogosult tömörítési, egy fájlt kell:
- Lehet nagyobb, mint 128 bájt.
- Lehet kisebb, mint 1 MB.
 
Ezeket a profilokat támogatja **gzip** (GNU zip), **deflate**, **bzip2**, vagy **br** (Brotli) kódolást. Ha a kérelem több tömörítési típust támogatja, akkor ezeket a tömörítési típusok élveznek Brotli tömörítést.

Ha az adott eszköz számára a kérelem kódolásának Brotli (tartalmazza a HTTP-fejléc `Accept-Encoding: br`) és a gyorsítótár-tévesztései, Azure CDN kérelem eredményez az eszköz Brotli tömörítési végez az eredeti kiszolgálóra. Ezt követően a tömörített fájl kiszolgált közvetlenül a gyorsítótárból.

### <a name="azure-cdn-from-akamai-profiles"></a>A profilok Akamai Azure CDN

A **Akamai Azure CDN** profilok, fájlok kerülnek a tömörítést. Azonban a MIME-típust, amelyet egy fájlnak kell lennie [tömörítési konfigurált](#enabling-compression).

Ezeket a profilokat támogatja csak **gzip** kódolást. Ha egy profil kérelmeket **gzip** kódolású fájl, a forrásból, függetlenül az ügyfélkérés mindig kérték. 

## <a name="compression-behavior-tables"></a>Tömörítés viselkedés táblák
Az alábbi táblázatok bemutatják az Azure CDN tömörítési viselkedés minden forgatókönyvhöz:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>Tömörítés le van tiltva, vagy a fájl nem tömörítési
| Ügyfél által kért formátum (keresztül Accept-Encoding fejlécnek) | Gyorsítótárazott fájlformátum | Az ügyfél CDN válasz | Megjegyzések |
| --- | --- | --- | --- |
| Tömörített |Tömörített |Tömörített | |
| Tömörített |Az Uncompressed |Az Uncompressed | |
| Tömörített |Nem gyorsítótárazott |Tömörített és tömörítetlen |Forrás válasz függ |
| Az Uncompressed |Tömörített |Az Uncompressed | |
| Az Uncompressed |Az Uncompressed |Az Uncompressed | |
| Az Uncompressed |Nem gyorsítótárazott |Az Uncompressed | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>Tömörítését engedélyezték, és a fájl nem jogosult az tömörítés
| Ügyfél által kért formátum (keresztül Accept-Encoding fejlécnek) | Gyorsítótárazott fájlformátum | Az ügyfél CDN válasz | Megjegyzések |
| --- | --- | --- | --- |
| Tömörített |Tömörített |Tömörített |CDN transcodes támogatott formátumok közötti |
| Tömörített |Az Uncompressed |Tömörített |CDN tömörítési hajt végre. |
| Tömörített |Nem gyorsítótárazott |Tömörített |CDN tömörítési hajt végre, ha a forrás visszaküldi a tömörítetlen. **Verizon Azure CDN** átadja az első kérésre tömörítetlen fájl és majd tömöríti és gyorsítótárazza a További kérelmeknél fájlt. A Cache-Control rendelkező fájlok: Soha ne tömörített no-cache fejlécet. |
| Az Uncompressed |Tömörített |Az Uncompressed |CDN kitömörítés hajt végre. |
| Az Uncompressed |Az Uncompressed |Az Uncompressed | |
| Az Uncompressed |Nem gyorsítótárazott |Az Uncompressed | |

## <a name="media-services-cdn-compression"></a>Media Services-CDN tömörítés
A Media Services CDN streaming engedélyezett végpontot, tömörítés alapértelmezés szerint engedélyezve van a következő MIME-típusok: 
- application/vnd.ms-sstr+xml 
- alkalmazás/dash + xml
- application/vnd.apple.mpegurl
- alkalmazás/f4m + xml. 

Nem engedélyezi vagy letiltja a MIME-típusok tömörítési az Azure portál használatával.  

## <a name="see-also"></a>Lásd még
* [CDN-fájltömörítés hibaelhárítása](cdn-troubleshoot-compression.md)    

