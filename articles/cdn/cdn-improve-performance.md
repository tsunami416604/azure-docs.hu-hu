---
title: "Fájlok tömörítése Azure CDN szolgáltatás használata a jobb teljesítmény érdekében |} Microsoft Docs"
description: "Ismerje meg, hogyan lehet fokozni az fájl átviteli sebesség és az Azure CDN szolgáltatás használata a fájlok tömörítésével lap betöltési teljesítményét növeli."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: b4e27ec57543daed35811fff347f457b0dd2cd5c
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2017
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>A jobb teljesítmény érdekében az Azure CDN fájlok tömörítése
Tömörítés a fájl átviteli sebesség növelése és a lap betöltése teljesítmény növeléséhez a fájlméret, mielőtt a kiszolgáló egyszerű és hatékony módszer. Csökkenti a sávszélességgel kapcsolatos költségek, és gyorsabban élményt nyújt a felhasználók számára.

Két módon lehet engedélyezni a tömörítést:

* A forrás kiszolgálón tömörítésének engedélyezéséhez. Ebben az esetben a CDN a tömörített fájlok keresztül, és azokat kérő ügyfeleknek továbbítja azokat.
* Közvetlenül a CDN peremhálózati kiszolgálóinak tömörítésének engedélyezéséhez. Ebben az esetben a CDN tömöríti a fájlokat, és végfelhasználók számára, akkor is, ha a forráskiszolgáló nem tömöríti szolgál.

> [!IMPORTANT]
> CDN konfigurációs módosításokat a hálózaton belüli propagálásához hosszabb időt is igénybe vehet.  A <b>Akamai Azure CDN</b> -profilok propagálása általában fejeződik be, egy perc alatt.  A <b>Azure CDN Verizon</b> profilok, a módosítások általában érvényesek 90 percen belül.  Ha először a CDN-végpont tömörítési beállítása, várja meg, 1 – 2 óra, mielőtt a tömörítési beállítások propagálása a POP való biztosításához.
> 
> 

## <a name="enabling-compression"></a>Tömörítés engedélyezése
> [!NOTE]
> A Standard és prémium szintű CDN rétegek funkcionalitása azonos tömörítést, de a felhasználói felület eltér.  Standard és prémium szintű CDN rétegek közötti különbségekről további információkért lásd: [Azure CDN áttekintése](cdn-overview.md).
> 
> 

### <a name="standard-tier"></a>Standard csomag
> [!NOTE]
> Ez a szakasz vonatkozik **Azure CDN Standard verizon** és **Azure CDN Standard Akamai** profilok.
> 
> 

1. A CDN-profil lapon kattintson a CDN-végpont felügyelni kíván.
   
    ![CDN-profil végpontok](./media/cdn-file-compression/cdn-endpoints.png)
   
    A CDN-végpont lap nyílik meg.
2. Kattintson a **konfigurálása** gombra.
   
    ![CDN-profil kezelésére gomb](./media/cdn-file-compression/cdn-config-btn.png)
   
    A CDN-konfiguráció lapon nyílik meg.
3. Kapcsolja be a **tömörítés**.
   
    ![CDN-tömörítési beállítások](./media/cdn-file-compression/cdn-compress-standard.png)
4. Használja az alapértelmezett típusokat, vagy módosítsa a listát fájltípusok hozzáadásával.
   
   > [!TIP]
   > Bár lehetséges, nem ajánlott tömörítés alkalmazása tömörített formátumban. Például ZIP, MP3, MP4 vagy JPG.
   > 
 
5. A módosítások elvégzése után kattintson a **mentése** gombra.

### <a name="premium-tier"></a>Premium szintű csomag
> [!NOTE]
> Ez a szakasz vonatkozik **verizon Azure CDN Premium** profilok.
> 
> 

1. A CDN-profil lapon kattintson a **kezelése** gombra.
   
    ![CDN-profil kezelésére gomb](./media/cdn-file-compression/cdn-manage-btn.png)
   
    Megnyitja a CDN-felügyeleti portálon.
2. Vigye a **HTTP nagy** lapra, és vigye a **gyorsítótár beállításainak** menü.  Kattintson a **tömörítés**.

    ![Fájl tömörítése kiválasztása](./media/cdn-file-compression/cdn-compress-select.png)
   
    Tömörítési beállítások jelennek meg.
   
    ![Fájl tömörítése beállítások](./media/cdn-file-compression/cdn-compress-files.png)
3. Tömörítésének engedélyezéséhez kattintson a **tömörítés engedélyezve** választógombot.  A MIME-típusok kívánja tömörítése (szóközök nélkül) vesszővel tagolt lista formájában adja meg a **fájltípusok** szövegmező.
   
   > [!TIP]
   > Bár lehetséges, nem ajánlott tömörítés alkalmazása tömörített formátumban. Például ZIP, MP3, MP4 vagy JPG.
   > 
4. A módosítások elvégzése után kattintson a **frissítés** gombra.

## <a name="compression-rules"></a>Tömörítés szabályok
Ezek a táblázatok ismertetik Azure CDN tömörítési viselkedés minden forgatókönyvhöz.

> [!IMPORTANT]
> A **Azure CDN Verizon** (Standard és prémium), csak a megfelelő fájlok tömörített profilok.  Jogosult tömörítési, egy fájlt kell:
> 
> * Lehet nagyobb, mint 128 bájt.
> * Lehet kisebb, mint 1 MB.
> 
> Ezeket a profilokat támogatja **gzip** (GNU zip), **deflate**, **bzip2**, vagy **br** (Brotli) kódolást. A kódolási Brotli, a tömörítés azért van szükség, forráson. Az ügyfélböngészőnek Brotli kódolási kérelmet kell küldenie, és a tömörített eszköz kell tömörített a forrás oldalon először. 

> [!IMPORTANT]
> A **Akamai Azure CDN** profilok, fájlok kerülnek a tömörítést. Azonban a fájl a MIME-típust, amelyet kell [tömörítési konfigurált](#enabling-compression).
> 
>Ezeket a profilokat támogatja csak **gzip** kódolást. Ha egy profil kérelmeket **gzip** kódolású fájl, a forrásból, függetlenül az ügyfélkérés mindig kérték. 

### <a name="compression-disabled-or-file-is-ineligible-for-compression"></a>A tömörítés le van tiltva, vagy a fájl nem tömörítés
| Az ügyfél kért formátum (keresztül Accept-Encoding fejlécnek) | Gyorsítótárazott fájlformátum | Az ügyfél CDN válasz | Megjegyzések |
| --- | --- | --- | --- |
| Tömörített |Tömörített |Tömörített | |
| Tömörített |Az Uncompressed |Az Uncompressed | |
| Tömörített |Nem gyorsítótárazott |Tömörített és tömörítetlen |Forrás válasz függ |
| Az Uncompressed |Tömörített |Az Uncompressed | |
| Az Uncompressed |Az Uncompressed |Az Uncompressed | |
| Az Uncompressed |Nem gyorsítótárazott |Az Uncompressed | |

### <a name="compression-enabled-and-file-is-eligible-for-compression"></a>A tömörítés engedélyezve van és a fájl nem jogosult az tömörítés
| Az ügyfél kért formátum (keresztül Accept-Encoding fejlécnek) | Gyorsítótárazott fájlformátum | Az ügyfél CDN válasz | Megjegyzések |
| --- | --- | --- | --- |
| Tömörített |Tömörített |Tömörített |CDN transcodes támogatott formátumok közötti |
| Tömörített |Az Uncompressed |Tömörített |CDN tömörítési hajt végre. |
| Tömörített |Nem gyorsítótárazott |Tömörített |CDN tömörítési hajt végre, ha a forrás visszaküldi tömörítetlen.  **Verizon Azure CDN** átadja az első kérésre tömörítetlen fájl és majd tömöríti és gyorsítótárazza a További kérelmeknél fájlt.  A fájlok a `Cache-Control: no-cache` soha nem tömörített fejléc. |
| Az Uncompressed |Tömörített |Az Uncompressed |CDN kitömörítés hajt végre. |
| Az Uncompressed |Az Uncompressed |Az Uncompressed | |
| Az Uncompressed |Nem gyorsítótárazott |Az Uncompressed | |

## <a name="media-services-cdn-compression"></a>Media Services-CDN tömörítés
A Media Services CDN engedélyezett streamvégpontok tömörítési alapértelmezés szerint engedélyezve van a következő típusú tartalomhoz: 
- alkalmazás/vnd.ms-sstr + xml 
- alkalmazás/dash + xml
- application/vnd.apple.mpegurl
- alkalmazás/f4m + xml. 

Nem engedélyezi vagy letiltja az említett típusa tömörítése az Azure portál használatával.  

## <a name="see-also"></a>Lásd még:
* [CDN-fájltömörítés hibaelhárítása](cdn-troubleshoot-compression.md)    

