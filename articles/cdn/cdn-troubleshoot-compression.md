---
title: Az Azure CDN fájltömörítés elhárítása |} Microsoft Docs
description: Azure CDN fájltömörítés elhárítása.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 14d50cb7cac77af75dd4b7293812154d1f24e47c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="troubleshooting-cdn-file-compression"></a>A CDN-fájlok tömörítési hibáinak elhárítása
Ez a cikk segít elhárítása [CDN fájltömörítés](cdn-improve-performance.md).

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a [az MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl is az Azure támogatási incidens. Lépjen a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) kattintson **támogatás**.

## <a name="symptom"></a>Jelenség
A végponthoz tömörítés engedélyezve van, de tömörített fájlok vannak visszaküldött.

> [!TIP]
> Annak ellenőrzéséhez, hogy a fájlok tömörített vannak visszaküldött kell használnia egy eszköz, például [Fiddler](http://www.telerik.com/fiddler) vagy a böngésző [fejlesztői eszközök](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/).  Ellenőrizze a HTTP-válaszfejlécek hibát adott vissza a gyorsítótárazott CDN tartalom.  Ha nincs nevű fejléc `Content-Encoding` értékkel rendelkező **gzip**, **bzip2**, vagy **deflate**, a tartalom tömörített.
> 
> ![Tartalom-Encoding fejlécnek](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Ok
Van több lehetséges oka, beleértve:

* A kért tartalma nem jogosult a tömörítés.
* Tömörítés nincs engedélyezve a kért fájl típusa.
* A HTTP-kérelem nem tartalmazza az egy érvényes tömörítési típus a kért fejléc.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
> [!TIP]
> Csakúgy, mint a központi telepítése új végpontok, CDN konfigurációs módosítások időigényes lehet a hálózaton belüli propagálásához.  Általában a módosításai érvényesek lesznek 90 percen belül.  Ha az első alkalommal beállítása tömörítés a CDN-végpont, vegye figyelembe, hogy a beállítások a POP való propagálása tömörítés 1 – 2 óra vár. 
> 
> 

### <a name="verify-the-request"></a>A kérelem ellenőrzése
Először azt kell tennie a kérelem gyors megerősítést ellenőrzése.  A böngésző használható [fejlesztői eszközök](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) kérelem alatt megtekintéséhez.

* Ellenőrizze, hogy a kérelmet küld a végponti URL-cím `<endpointname>.azureedge.net`, és nem a forrása.
* Ellenőrizze a kérelem tartalmazza egy **elfogadás kódolás** fejlécet, valamint az, hogy a fejléc értéke tartalmaz **gzip**, **deflate**, vagy **bzip2**.

> [!NOTE]
> **Akamai Azure CDN** csak támogatási profilok **gzip** kódolást.
> 
> 

![CDN-kérelemfejlécekben](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Ellenőrizze a tömörítési beállítások (standard szintű CDN-profil)
> [!NOTE]
> Ez a lépés csak akkor, ha a CDN-profil vonatkozik egy **Azure CDN Standard Microsoft**, **Azure CDN Standard verizon**, vagy **Azure CDN Standard Akamai** profil. 
> 
> 

A következőben szereplő végpontnál: keresse meg a [Azure-portálon](https://portal.azure.com) , és kattintson a **konfigurálása** gombra.

* Ellenőrizze a tömörítés engedélyezve van.
* Ellenőrizze, hogy a tömörített formátumok listája szerepel a MIME-típus a tartalom tömörítését.

![CDN-tömörítési beállítások](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Ellenőrizze a tömörítési beállítások (prémium szintű CDN-profil)
> [!NOTE]
> Ez a lépés csak akkor, ha a CDN-profil vonatkozik egy **verizon Azure CDN Premium** profil.
> 
> 

A következőben szereplő végpontnál: keresse meg a [Azure-portálon](https://portal.azure.com) , és kattintson a **kezelése** gombra.  Ekkor megnyílik a kiegészítő portálon.  Vigye a **HTTP nagy** lapra, és vigye a **gyorsítótár beállításainak** menü.  Kattintson a **tömörítés**. 

* Ellenőrizze a tömörítés engedélyezve van.
* Ellenőrizze a **fájltípusok** lista tartalmazza-e egy vesszővel tagolt listája (szóközök nélkül) a MIME-típusok.
* Ellenőrizze, hogy a tömörített formátumok listája szerepel a MIME-típus a tartalom tömörítését.

![CDN premium tömörítési beállítások](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>Ellenőrizze a tartalom gyorsítótárazott (Verizon CDN-profil)
> [!NOTE]
> Ez a lépés csak akkor, ha a CDN-profil vonatkozik egy **Azure CDN Standard verizon** vagy **verizon Azure CDN Premium** profil.
> 
> 

A böngésző fejlesztői eszközök segítségével, ellenőrizze annak érdekében, hogy a fájl tárolja a rendszer a régió, ahol kérik a response fejlécekkel együtt.

* Ellenőrizze a **Server** válaszfejlécet.  A fejléc kell rendelkeznie a formátum **Platform (POP-kiszolgáló azonosítója)**, az alábbi példában látható módon.
* Ellenőrizze a **X-gyorsítótár** válaszfejlécet.  Olvassa el a fejléc **TALÁLATI**.  

![CDN-válaszfejlécek](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Ellenőrizze a fájl megfelel-e a kötetméretek követelményeit (Verizon CDN-profil)
> [!NOTE]
> Ez a lépés csak akkor, ha a CDN-profil vonatkozik egy **Azure CDN Standard verizon** vagy **verizon Azure CDN Premium** profil.
> 
> 

Jogosult tömörítési, egy fájlt a következő méret követelményeknek kell megfelelniük:

* Nagyobb, mint 128 bájt.
* 1 MB-nál kisebb.

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>A forrás kiszolgálón a kérés ellenőrzéséhez egy **keresztül** fejléc
A **keresztül** HTTP-fejléc határozza meg, hogy a webalkalmazás-kiszolgáló proxykiszolgáló által átadott a kérelmet.  Microsoft IIS-webkiszolgálókkal alapértelmezés szerint nem a válaszok tömörítésére vonatkozó Ha a kérelem tartalmazza a **keresztül** fejléc.  Bírálja felül ezt a viselkedést, hajtsa végre a következő:

* **Az IIS 6**: [HcNoCompressionForProxies állítsa "FALSE" = az IIS-metabázis-tulajdonságok](https://msdn.microsoft.com/library/ms525390.aspx)
* **Az IIS 7 és legfeljebb**: [mind **noCompressionForHttp10** és **noCompressionForProxies** FALSE, a kiszolgáló konfigurációjában](http://www.iis.net/configreference/system.webserver/httpcompression)

