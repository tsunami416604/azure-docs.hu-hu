---
title: Teljesítmény javítása a fájlok tömörítésével az Azure CDN-ben |} A Microsoft Docs
description: Útmutató a fájl átviteli sebességének növelése és az Azure CDN-ben a fájlok tömörítésével oldal betöltési teljesítmény érdekében.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: magattus
ms.openlocfilehash: afe959e80b339db5112fa97fd79d0528390e3954
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58096452"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Teljesítmény javítása a fájlok tömörítésével az Azure CDN-ben
Fájlok tömörítési a egyszerű és hatékony módszer fájl átviteli sebességének növelése és a egy fájl méretének csökkentésével, a kiszolgálóról továbbítás előtt oldal betöltési teljesítmény érdekében. Fájlok tömörítési adatforgalmi költségek csökkentése és a egy rugalmasabb ügyfélkapcsolatok kialakítását élményt biztosíthat a felhasználóknak.

Fájl tömörítésének engedélyezéséhez a két módja van:

- A forrás kiszolgálón tömörítésének engedélyezéséhez. Ebben az esetben az Azure CDN továbbítja a tömörített fájlok, és továbbítja azokat az azokat kérő ügyfeleknek.
- Közvetlenül a CDN összes jelenléti Pontjára kiszolgálókon tömörítés engedélyezése (*működés közbeni tömörítés*). Ebben az esetben a CDN-t a fájlok tömörítése, és a végfelhasználók akkor is, ha azok nem a forráskiszolgáló által tömörített szolgál.

> [!IMPORTANT]
> Az Azure CDN konfigurációs módosítások eltarthat egy ideig a hálózaton belüli propagálásához: 
> - A **Microsoft Azure CDN Standard** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
> - Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
> - A **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
> 
> Ha először a CDN-végpont tömörítési beállítása, várja meg, 1 – 2 óra, mielőtt hozzálátna, győződjön meg arról, a tömörítési beállítások a POP-kre történő propagálása megtörtént.

## <a name="enabling-compression"></a>Tömörítés engedélyezése
A standard és prémium szintű CDN-csomag biztosítja a tömörítés, de a felhasználói felület eltér. Standard és prémium szintű CDN rétegek közötti különbségekkel kapcsolatos további információkért lásd: [Azure CDN áttekintése](cdn-overview.md).

### <a name="standard-cdn-profiles"></a>Standard szintű CDN-profilok 
> [!NOTE]
> Ez a szakasz vonatkozik **Azure CDN Standard a Microsoft**, **Azure CDN Standard verizon**, és **Azure CDN Akamai Standard** profilok.
> 
> 

1. A CDN-profiloldal jelölje ki a CDN-végpont, amelyet kezelni szeretne.

    ![Profil CDN-végpontok](./media/cdn-file-compression/cdn-endpoints.png)

    A CDN-végpont lap nyílik meg.
2. Válassza ki **tömörítési**.

    ![A CDN-tömörítés kiválasztása](./media/cdn-file-compression/cdn-compress-select-std.png)

    A tömörítés lap nyílik meg.
3. Válassza ki **a** bekapcsolni a tömörítést.

    ![CDN fájl tömörítési beállítások](./media/cdn-file-compression/cdn-compress-standard.png)
4. Használja az alapértelmezett MIME-típusok, vagy hozzáadásával vagy eltávolításával a MIME-típusok listájának módosítása.

   > [!TIP]
   > Bár lehetséges, nem ajánlott a tömörítés alkalmazandó tömörített formátum. Ha például ZIP, MP3, MP4 vagy JPG.
   > 

   > [!NOTE]
   > Az alapértelmezett MIME-típusok listájának módosításával jelenleg nem támogatott az Azure CDN Standard a Microsofttól.
   > 

5. A módosítások elvégzése után válassza ki a **mentése**.

### <a name="premium-cdn-profiles"></a>Prémium szintű CDN-profilok
> [!NOTE]
> Ez a szakasz csak vonatkozik **verizon Azure CDN Premium** profilok.
> 

1. Válassza ki a CDN-profiloldal **kezelés**.

    ![Válassza ki a CDN kezelése](./media/cdn-file-compression/cdn-manage-btn.png)

    Megnyílik a CDN felügyeleti portálját.
2. A kurzort a **HTTP nagy** lapfülre, majd mutasson a **gyorsítótár beállításainak** úszó menü. Válassza ki **tömörítési**.

    ![A CDN-tömörítés kiválasztása](./media/cdn-file-compression/cdn-compress-select.png)

    A tömörítési beállítások jelennek meg.

    ![CDN fájl tömörítési beállítások](./media/cdn-file-compression/cdn-compress-files.png)
3. Tömörítés engedélyezése kiválasztásával **tömörítés engedélyezve**. A MIME-típusok tömörítendő (szóközök nélkül) vesszővel tagolt lista formájában adja meg a **fájltípusok** mezőbe.

   > [!TIP]
   > Bár lehetséges, nem ajánlott a tömörítés alkalmazandó tömörített formátum. Ha például ZIP, MP3, MP4 vagy JPG.
   > 

4. A módosítások elvégzése után válassza ki a **frissítés**.

## <a name="compression-rules"></a>A tömörítés szabályok

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Az Azure CDN Standard a Microsoft-profilok közül

A **Azure CDN Standard a Microsoft** profilok, csak a megfelelő fájlok tömörítve vannak. Jogosult a tömörítést, egy fájlt kell:
- Egy MIME típusú, amelynek már [tömörítési konfigurált](#enabling-compression).
- 1 KB-nál nagyobb lehet
- 8 MB-nál kisebb lehet

Ezek a profilok az alábbi tömörítési kódolásokat támogatják:
- a gzip (GNU zip)
- brotli 

Ha a kérés támogatja egynél több tömörítési típus, brotli tömörítési élvez elsőbbséget.

Amikor egy kérelem egy eszköz tömörítése a gzip és a kérés eredményét adja meg a gyorsítótár-tévesztés, az Azure CDN az eszköz tömörítése a gzip közvetlenül a POP-kiszolgálón hajt végre. A tömörített fájlt a későbbiekben a gyorsítótár szolgáltatja.

### <a name="azure-cdn-from-verizon-profiles"></a>Az Azure CDN a Verizontól profilok közül

A **Azure CDN Standard verizon** és **verizon Azure CDN Premium** profilok, csak a megfelelő fájlok tömörítve vannak. Jogosult a tömörítést, egy fájlt kell:
- Lehet nagyobb, mint 128 bájt
- 3 MB-nál kisebb lehet

Ezek a profilok az alábbi tömörítési kódolásokat támogatják:
- a gzip (GNU zip)
- DEFLATE
- bzip2
- brotli 

Ha a kérés egynél több tömörítési típus támogatja, akkor ezeket a tömörítési típusok élveznek brotli tömörítést.

Amikor egy kérelem egy eszköz brotli tömörítési Itt adhatja meg (HTTP-fejléc `Accept-Encoding: br`) és a kérés eredményeket gyorsítótár-tévesztés, az Azure CDN brotli tömörítési az eszköz elvégzi a közvetlenül a POP-kiszolgálón. A tömörített fájlt a későbbiekben a gyorsítótár szolgáltatja.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Az Azure CDN Standard Akamai-profilok

A **Azure CDN Akamai Standard** profilok fájlok kerülnek a tömörítést. Azonban kell lennie egy fájl a MIME-típust, amelynek már [tömörítési konfigurált](#enabling-compression).

Ezeket a profilokat támogatja a gzip tömörítés kódolás csak. Amikor egy profilvégpontjához a gzip-kódolású fájlt, mindig kért a forrásból, függetlenül az ügyfél kérése. 

## <a name="compression-behavior-tables"></a>A tömörítés viselkedés táblák
Az alábbi táblázatok ismertetik az Azure CDN tömörítési viselkedés minden forgatókönyvhöz:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>Tömörítési le van tiltva, vagy pedig nem lehet áttelepíteni a tömörítés
| Ügyfél által kért formátum (via fejléc Accept-Encoding) | Gyorsítótárazott fájl formátuma | A CDN válaszol az ügyfélnek | Megjegyzések&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Tömörített |Tömörített |Tömörített | |
| Tömörített |Tömörítetlen |Tömörítetlen | |
| Tömörített |Nincsenek gyorsítótárazva |Tömörített és tömörítetlen |Az eredeti válasz határozza meg, hogy CDN végrehajt egy tömörítés. |
| Tömörítetlen |Tömörített |Tömörítetlen | |
| Tömörítetlen |Tömörítetlen |Tömörítetlen | |
| Tömörítetlen |Nincsenek gyorsítótárazva |Tömörítetlen | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>Engedélyezve van a tömörítés és a fájl is költhetők a tömörítés
| Ügyfél által kért formátum (via fejléc Accept-Encoding) | Gyorsítótárazott fájl formátuma | CDN-válasz az ügyfélhez | Megjegyzések |
| --- | --- | --- | --- |
| Tömörített |Tömörített |Tömörített |A CDN olyan támogatott formátum között. |
| Tömörített |Tömörítetlen |Tömörített |A CDN-tömörítés hajt végre. |
| Tömörített |Nincsenek gyorsítótárazva |Tömörített |A CDN-tömörítés hajt végre, ha a forrás visszaküldi a tömörítetlen. <br/>**Az Azure CDN a Verizontól** továbbítja a tömörítetlen fájl első kérésére és majd tömöríti és gyorsítótárazza a fájlt a későbbi kérelmeket. <br/>A fájlok a `Cache-Control: no-cache` fejléc soha nem tömörítve vannak. |
| Tömörítetlen |Tömörített |Tömörítetlen |A CDN-Kibontás hajt végre. |
| Tömörítetlen |Tömörítetlen |Tömörítetlen | |
| Tömörítetlen |Nincsenek gyorsítótárazva |Tömörítetlen | |

## <a name="media-services-cdn-compression"></a>A Media Services a CDN-tömörítés
A végpontok engedélyezve van a CDN a Media Services streaming, tömörítés alapértelmezés szerint engedélyezve van a következő MIME-típusok esetében: 
- application/vnd.ms-sstr+xml 
- alkalmazás/dash-sel + xml
- application/vnd.apple.mpegurl
- alkalmazás/f4m + xml 

## <a name="see-also"></a>Lásd még
* [CDN-fájltömörítés hibaelhárítása](cdn-troubleshoot-compression.md)    

