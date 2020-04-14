---
title: A teljesítmény javítása az Azure CDN fájljainak tömörítésével | Microsoft dokumentumok
description: Ismerje meg, hogyan javíthatja a fájlátviteli sebességet, és növelheti az oldalbetöltési teljesítményt a fájlok tömörítésével az Azure CDN-ben.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: allensu
ms.openlocfilehash: 7124dd40d4510674014afe012a8f40dcb5bb6153
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253764"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>A teljesítmény javítása a fájlok tömörítésével az Azure CDN-ben
A fájltömörítés egy egyszerű és hatékony módszer a fájlátviteli sebesség növelésére és az oldalbetöltési teljesítmény növelésére azáltal, hogy csökkenti a fájl méretét, mielőtt a kiszolgálóról elküldené. A fájltömörítés csökkentheti a sávszélesség költségeit, és gyorsabb felhasználói élményt biztosít.

A fájltömörítés kétféleképpen engedélyezheti:

- Engedélyezze a tömörítést az eredeti kiszolgálón. Ebben az esetben az Azure CDN továbbítja a tömörített fájlokat, és azokat az ügyfeleknek, amelyek kérik őket.
- Engedélyezze a tömörítést közvetlenül a CDN POP-kiszolgálókon (*tömörítés menet közben*). Ebben az esetben a CDN tömöríti a fájlokat, és kiszolgálja azokat a végfelhasználóknak, még akkor is, ha azokat nem az eredeti kiszolgáló tömöríti.

> [!IMPORTANT]
> Az Azure CDN konfigurációs módosításai eltarthat egy ideig a hálózaton keresztül iszporkolódás során: 
> - A **Microsoft Azure CDN Standard** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
> - Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
> - A **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
> 
> Ha első alkalommal állítja be a tömörítést a CDN-végponthoz, várjon 1-2 órát a hibaelhárítás előtt, hogy a tömörítési beállítások propagáljanak a POP-ok számára.

## <a name="enabling-compression"></a>Tömörítés engedélyezése
A szabványos és prémium szintű CDN-szintek ugyanazt a tömörítési funkciót biztosítják, de a felhasználói felület eltérő. A standard és a prémium szintű CDN-rétegek közötti különbségekről az [Azure CDN áttekintése című témakörben olvashat bővebben.](cdn-overview.md)

### <a name="standard-cdn-profiles"></a>Szabványos CDN-profilok 
> [!NOTE]
> Ez a szakasz a **Microsoft Azure CDN Standard,** **a Verizon Azure CDN Standard**és az **Akamai-profilokból származó Azure CDN Standard szolgáltatásra** vonatkozik.
> 
> 

1. A CDN-profillapon jelölje ki a kezelni kívánt CDN-végpontot.

    ![CDN-profil végpontjai](./media/cdn-file-compression/cdn-endpoints.png)

    Megnyílik a CDN végpont lapja.
2. Válassza a **Tömörítés**lehetőséget.

    ![CDN tömörítés kiválasztása](./media/cdn-file-compression/cdn-compress-select-std.png)

    Megnyílik a tömörítési lap.
3. A tömörítés bekapcsolására a **Be** gombra választva kapcsolhatja be a gombot.

    ![CDN-fájltömörítési beállítások](./media/cdn-file-compression/cdn-compress-standard.png)
4. Használja az alapértelmezett MIME-típusokat, vagy módosítsa a listát MIME-típusok hozzáadásával vagy eltávolításával.

   > [!TIP]
   > Bár lehetséges, nem ajánlott tömörítést alkalmazni a tömörített formátumokra. Például ZIP, MP3, MP4 vagy JPG.
   > 

   > [!NOTE]
   > A MIME-típusok alapértelmezett listájának módosítása jelenleg nem támogatott a Microsoft Azure CDN Standard szolgáltatásában.
   > 

5. A módosítások elvégzése után válassza a **Mentés gombot.**

### <a name="premium-cdn-profiles"></a>Prémium CDN profilok
> [!NOTE]
> Ez a szakasz csak a **Verizon-profilokból származó Azure CDN Premium szolgáltatásra** vonatkozik.
> 

1. A CDN-profil lapon válassza a **Kezelés lehetőséget.**

    ![CDN-kezelés kiválasztása](./media/cdn-file-compression/cdn-manage-btn.png)

    Megnyílik a CDN felügyeleti portál.
2. Mutasson a **NAGY HTTP-fülre,** majd mutasson a **Gyorsítótár beállításai** úszó panelre. Válassza a **Tömörítés**lehetőséget.

    ![CDN tömörítés kiválasztása](./media/cdn-file-compression/cdn-compress-select.png)

    Megjelennek a tömörítési beállítások.

    ![CDN-fájltömörítési beállítások](./media/cdn-file-compression/cdn-compress-files.png)
3. A tömörítés engedélyezése a **Tömörítés engedélyezve lehetőséget választva.** Írja be a Fájltípusok mezőbe azokat a MIME-típusokat, amelyeket vesszővel tagolt listaként (szóköz **nélkül)** szeretne tömöríteni.

   > [!TIP]
   > Bár lehetséges, nem ajánlott tömörítést alkalmazni a tömörített formátumokra. Például ZIP, MP3, MP4 vagy JPG.
   > 

4. A módosítások elvégzése után válassza a **Frissítés**lehetőséget.

## <a name="compression-rules"></a>Tömörítési szabályok

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Azure CDN Standard microsoftos profilokból

A **Microsoft-profilokból származó Azure CDN Standard** esetében csak a jogosult fájlok lesznek tömörítve. A tömörítésre való jogosultsághoz a fájlnak:
- [Tömörítésre konfigurált](#enabling-compression)MIME-típusúnak kell lennie.
- Legyen nagyobb, mint 1 KB
- Legyen kisebb, mint 8 MB

Ezek a profilok a következő tömörítési kódolásokat támogatják:
- gzip (GNU zip)
- brotli között 

Ha a kérelem egynél több tömörítési típust támogat, a brotli tömörítés élvez elsőbbséget.

Amikor egy eszközre vonatkozó kérelem gzip-tömörítést ad meg, és a kérelem gyorsítótár-tévesztést eredményez, az Azure CDN közvetlenül a POP-kiszolgálón hajtja végre az eszköz gzip-tömörítését. Ezt követően a tömörített fájl a gyorsítótárból jelenik meg.

### <a name="azure-cdn-from-verizon-profiles"></a>Azure CDN a Verizon-profilokból

A **Verizon Azure CDN Standard** és a **Verizon-profilokból származó Azure CDN Premium** esetében csak a jogosult fájlok lesznek tömörítve. A tömörítésre való jogosultsághoz a fájlnak:
- Legyen nagyobb, mint 128 bájt
- Legyen kisebb, mint 3 MB

Ezek a profilok a következő tömörítési kódolásokat támogatják:
- gzip (GNU zip)
- Deflate
- bzip2
- brotli között 

Ha a kérelem egynél több tömörítési típust támogat, ezek a tömörítési típusok elsőbbséget élveznek a brotli tömörítéssel szemben.

Ha egy eszközre vonatkozó kérelem brotli tömörítést `Accept-Encoding: br`(HTTP-fejléc) határoz meg, és a kérelem gyorsítótár-tévesztést eredményez, az Azure CDN közvetlenül a POP-kiszolgálón hajtja végre az eszköz brotli tömörítését. Ezt követően a tömörített fájl a gyorsítótárból jelenik meg.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Azure CDN Standard akamai profilokból

Az **Akamai-profilokból származó Azure CDN Standard** esetében minden fájl tömörítésre jogosult. A fájlnak azonban [tömörítésre konfigurált](#enabling-compression)MIME típusúnak kell lennie.

Ezek a profilok csak a gzip tömörítési kódolást támogatják. Amikor egy profilvégpont gzip-kódolású fájlt kér, a rendszer mindig az eredeti től kéri, függetlenül az ügyfélkéréstől. 

## <a name="compression-behavior-tables"></a>Tömörítési viselkedéstáblák
Az alábbi táblázatok az Azure CDN-tömörítési viselkedését ismertetik minden forgatókönyvnél:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>A tömörítés le van tiltva, vagy a fájl nem jogosult tömörítésre
| Ügyfél által kért formátum (elfogadás-kódolásfejlécen keresztül) | Gyorsítótárazott fájlformátum | A CDN-válasz az ügyfélnek | &nbsp; &nbsp; Megjegyzések&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Tömörített |Tömörített |Tömörített | |
| Tömörített |Tömörítetlen |Tömörítetlen | |
| Tömörített |Nincs gyorsítótárazva |Tömörített vagy tömörítetlen |Az origóválasz határozza meg, hogy a CDN végrehajt-e tömörítést. |
| Tömörítetlen |Tömörített |Tömörítetlen | |
| Tömörítetlen |Tömörítetlen |Tömörítetlen | |
| Tömörítetlen |Nincs gyorsítótárazva |Tömörítetlen | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>A tömörítés engedélyezve van, és a fájl tömörítésre jogosult
| Ügyfél által kért formátum (elfogadás-kódolásfejlécen keresztül) | Gyorsítótárazott fájlformátum | CDN-válasz az ügyfélnek | Megjegyzések |
| --- | --- | --- | --- |
| Tömörített |Tömörített |Tömörített |CdN átkódolók a támogatott formátumok között. |
| Tömörített |Tömörítetlen |Tömörített |A CDN tömörítést hajt végre. |
| Tömörített |Nincs gyorsítótárazva |Tömörített |A CDN tömörítést hajt végre, ha az origó tömörítetlen fájlt ad vissza. <br/>**A Verizon Azure CDN** továbbítja a tömörítetlen fájlt az első kérésre, majd tömöríti és gyorsítótárazza a fájlt a későbbi kérelmekhez. <br/>A `Cache-Control: no-cache` fejléccel rendelkező fájlok tömörítése soha nem lesz tömörítve. |
| Tömörítetlen |Tömörített |Tömörítetlen |A CDN dekompressziót hajt végre. |
| Tömörítetlen |Tömörítetlen |Tömörítetlen | |
| Tömörítetlen |Nincs gyorsítótárazva |Tömörítetlen | |

## <a name="media-services-cdn-compression"></a>Media Services CDN tömörítés
A Media Services CDN-streameléséhez engedélyezett végpontok esetében a tömörítés alapértelmezés szerint engedélyezve van a következő MIME-típusok esetében: 
- alkalmazás/vnd.ms-sstr+xml 
- alkalmazás/kötőjel+xml
- alkalmazás/vnd.apple.mpegurl
- alkalmazás/f4m+xml 

## <a name="see-also"></a>Lásd még
* [A CDN-fájlok tömörítési hibáinak elhárítása](cdn-troubleshoot-compression.md)    

