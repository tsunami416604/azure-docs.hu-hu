---
title: Az Azure CDN-beli fájlok tömörítési hibáinak elhárítása |} A Microsoft Docs
description: Az Azure CDN-fájlok tömörítési hibáinak elhárítása.
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
ms.openlocfilehash: 2a41316eadb43145628d6c625935c751bfbc6ad6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531526"
---
# <a name="troubleshooting-cdn-file-compression"></a>A CDN-fájlok tömörítési hibáinak elhárítása
Ez a cikk segít a hibák elhárításában [CDN-fájltömörítés](cdn-improve-performance.md).

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a [az MSDN Azure és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik megoldásként is fájl is egy Azure-támogatási esemény. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) kattintson **támogatás kérése**.

## <a name="symptom"></a>Jelenség
A végpont tömörítés engedélyezve van, de a fájlok vannak a visszaadott tömörítetlen.

> [!TIP]
> Annak ellenőrzéséhez, hogy a fájlokat a rendszer már ad tömörített kell használnia egy eszköz, például [Fiddler](https://www.telerik.com/fiddler) vagy a böngésző [fejlesztői eszközök](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/).  Ellenőrizze a HTTP-válaszfejlécek adott vissza a gyorsítótárazott CDN-tartalom.  Van-e nevű fejléc `Content-Encoding` értékkel **gzip**, **bzip2**, vagy **deflate**, a tartalom tömörítése.
> 
> ![Tartalom kódolása fejléc](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Ok
Van több lehetséges oka lehet, többek között:

* A kért tartalmat, nem jogosult a tömörítést.
* A tömörítés nem engedélyezve van a kért fájl típusa.
* A HTTP-kérelem nem tartalmazott egy érvénytelen tömörítési típus kérése fejléc.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
> [!TIP]
> Mint az új végpontok üzembe, a CDN konfigurációs módosítások némi időt a hálózaton belüli propagálásához.  Változások általában 90 percen belül lépnek érvénybe.  Ha az első alkalommal beállította a tömörítés a CDN-végpont, vegye figyelembe, hogy a tömörítési beállítások a POP-kre történő propagálása megtörtént 1 – 2 óra várakozás. 
> 
> 

### <a name="verify-the-request"></a>A kérelem ellenőrzése
Először azt kell tennie a kérelem ellenőrzése gyors megerősítést.  A böngésző használható [fejlesztői eszközök](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) megtekintéséhez a webkiszolgálónak kéréseket.

* Ellenőrizze, hogy a kérelem érkezik a végponti URL-cím `<endpointname>.azureedge.net`, és nem a forráshoz.
* Ellenőrizze, hogy a kérés tartalmaz egy **Accept-Encoding** fejlécére, majd a fejléc értéke tartalmaz **gzip**, **deflate**, vagy **bzip2**.

> [!NOTE]
> **Az Azure CDN az Akamaitól** podporují profilok **gzip** kódolást.
> 
> 

![A CDN-kérelemfejlécek](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Ellenőrizze a tömörítési beállítások (standard szintű CDN-profilok)
> [!NOTE]
> Ez a lépés csak akkor, ha a CDN-profil vonatkozik egy **Azure CDN Standard a Microsoft**, **Azure CDN Standard verizon**, vagy **Azure CDN Akamai Standard** profilt. 
> 
> 

Keresse meg a végpont a [az Azure portal](https://portal.azure.com) , és kattintson a **konfigurálása** gombra.

* Ellenőrizze, hogy engedélyezve van a tömörítés.
* Ellenőrizze, hogy a MIME-típus tömörítendő a tartalmat a tömörített formátumok listáját tartalmazza.

![CDN tömörítési beállítások](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Ellenőrizze a tömörítési beállítások (prémium szintű CDN-profilok)
> [!NOTE]
> Ez a lépés csak akkor, ha a CDN-profil vonatkozik egy **verizon Azure CDN Premium** profilt.
> 
> 

Keresse meg a végpont a [az Azure portal](https://portal.azure.com) , és kattintson a **kezelés** gombra.  A kiegészítő portál nyílik meg.  A kurzort a **HTTP nagy** lapfülre, majd mutasson a **gyorsítótár beállításainak** úszó menü.  Kattintson a **tömörítési**. 

* Ellenőrizze, hogy engedélyezve van a tömörítés.
* Ellenőrizze a **fájltípusok** lista tartalmaz egy vesszővel tagolt listája (szóközök nélkül) a MIME-típusok.
* Ellenőrizze, hogy a MIME-típus tömörítendő a tartalmat a tömörített formátumok listáját tartalmazza.

![CDN premium tömörítési beállítások](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>Ellenőrizze a tartalom gyorsítótárazott (Verizon CDN-profilok)
> [!NOTE]
> Ez a lépés csak akkor, ha a CDN-profil vonatkozik egy **Azure CDN Standard verizon** vagy **verizon Azure CDN Premium** profilt.
> 
> 

A böngésző fejlesztői eszközök használatával, ellenőrizze a válaszfejlécek biztosításához a régióban, ahol éppen kérés gyorsítótárazza a fájlt.

* Ellenőrizze a **kiszolgáló** válaszfejléc.  A fejléc kell rendelkeznie a formátum **platformmal (POP vagy kiszolgáló-azonosító)**, az alábbi példában látható módon.
* Ellenőrizze a **X-gyorsítótár** válaszfejléc.  Beolvassa a fejléc **TALÁLATI**.  

![A CDN-válaszfejlécek](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Ellenőrizze, hogy a fájl megfelel a mérete (Verizon CDN-profilok)
> [!NOTE]
> Ez a lépés csak akkor, ha a CDN-profil vonatkozik egy **Azure CDN Standard verizon** vagy **verizon Azure CDN Premium** profilt.
> 
> 

Jogosult a tömörítést, a fájl a következő méret követelményeknek kell megfelelnie:

* Nagyobb, mint 128 bájt.
* 1 MB-nál kisebb.

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Ellenőrizze a forrás kiszolgálón a kérés egy **keresztül** fejléc
A **keresztül** HTTP-fejléc azt jelzi, hogy a webkiszolgáló egy proxykiszolgáló által átadott a kérelmet.  Microsoft IIS-kiszolgálók alapértelmezés szerint nem tömöríthetők válaszokat, ha a kérés tartalmaz egy **keresztül** fejléc.  Bírálja felül ezt a viselkedést, hajtsa végre a következőket:

* **AZ IIS 6**: [HcNoCompressionForProxies állítsa "FALSE" = az IIS-metabázis tulajdonságai](https://msdn.microsoft.com/library/ms525390.aspx)
* **Az IIS 7, és akár**: [Állítsa be mindkét **noCompressionForHttp10** és **noCompressionForProxies** hamis, a kiszolgáló konfigurációjában](http://www.iis.net/configreference/system.webserver/httpcompression)

