---
title: Fájltömörítés hibaelhárítása az Azure CDN-ben | Microsoft dokumentumok
description: Az Azure CDN-fájltömörítéssel kapcsolatos problémák elhárítása.
services: cdn
documentationcenter: ''
author: sohamnc
manager: danielgi
editor: ''
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: aff2dadee365fcdc7e14070714aa1d2cbba901ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476423"
---
# <a name="troubleshooting-cdn-file-compression"></a>A CDN-fájlok tömörítési hibáinak elhárítása
Ez a cikk segítséget nyújt a [CDN-fájltömörítéssel](cdn-improve-performance.md)kapcsolatos problémák elhárításához.

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel [az MSDN Azure-ban és a Veremtúlcsordulás fórumain.](https://azure.microsoft.com/support/forums/) Azt is megteheti, hogy egy Azure-támogatási incidenst is benyújthat. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és kattintson a Támogatás beszerezni e **gombra.**

## <a name="symptom"></a>Hibajelenség
A végpont tömörítése engedélyezve van, de a fájlok tömörítetlenül kerülnek visszaadásra.

> [!TIP]
> Annak ellenőrzéséhez, hogy a fájlok at tömörítetten adja-e vissza, olyan eszközt kell használnia, mint a [Fiddler](https://www.telerik.com/fiddler) vagy a böngésző [fejlesztői eszközei](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/).  Ellenőrizze a gyorsítótárazott CDN-tartalommal visszaadott HTTP-válaszfejléceket.  Ha van egy `Content-Encoding` **fejléc neve,** amelynek értéke gzip , **bzip2**, vagy **leereszt,** a tartalom tömörített.
> 
> ![Tartalomkódolás fejléce](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Ok
Számos oka lehet, többek között:

* A kért tartalom nem jogosult tömörítésre.
* A kért fájltípushoz nincs engedélyezve a tömörítés.
* A HTTP-kérelem nem tartalmazott érvényes tömörítési típust kérő fejlécet.
* Az Origin adattömböket küld.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
> [!TIP]
> Az új végpontok központi telepítéséhez ugyanúgy, mint az új végpontok üzembe helyezése, a CDN konfigurációs módosításai némi időt vesz igénybe a hálózaton keresztül történő propagáláshoz.  Általában a módosításokat 90 percen belül alkalmazzák.  Ha ez az első alkalom, hogy tömörítést állít be a CDN-végponthoz, érdemes megvárnia az 1-2 órát, hogy megbizonyosodjon arról, hogy a tömörítési beállítások propagáltak a POP-ok számára. 
> 
> 

### <a name="verify-the-request"></a>A kérelem ellenőrzése
Először is, gyorsan ellenőriznünk kell a kérést.  A böngésző [fejlesztői eszközeivel](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) megtekintheti a kéréseket.

* Ellenőrizze, hogy a kérelem a végpont `<endpointname>.azureedge.net`URL-címére érkezik-e, és ne az eredetére.
* Ellenőrizze, hogy a kérelem **tartalmaz-e Accept-Encoding fejlécet,** és a fejléc értéke tartalmazza a **gzip,** **deflate**vagy **bzip2 fejlécet.**

> [!NOTE]
> **Az Akamai-profilokból származó Azure CDN** csak a **gzip** kódolást támogatja.
> 
> 

![CDN-kérelemfejlécek](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Tömörítési beállítások ellenőrzése (szabványos CDN-profilok)
> [!NOTE]
> Ez a lépés csak akkor érvényes, ha a CDN-profil a **Microsoft Azure CDN-szabványa,** a **Verizon Azure CDN Standard**vagy az **Akamai-profilAzure CDN Standard.** 
> 
> 

Keresse meg a végpontot az [Azure Portalon,](https://portal.azure.com) és kattintson a **Konfigurálás gombra.**

* Ellenőrizze, hogy a tömörítés engedélyezve van-e.
* Ellenőrizze, hogy a tömörítendő tartalom MIME-típusa szerepel-e a tömörített formátumok listájában.

![CDN tömörítési beállítások](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Tömörítési beállítások ellenőrzése (Prémium CDN-profilok)
> [!NOTE]
> Ez a lépés csak akkor érvényes, ha a CDN-profil egy **Azure CDN Premium a Verizon-profilból.**
> 
> 

Keresse meg a végpontot az [Azure Portalon,](https://portal.azure.com) és kattintson a **Kezelés** gombra.  Megnyílik a kiegészítő portál.  Mutasson a **NAGY HTTP-fülre,** majd mutasson a **Gyorsítótár beállításai** úszó panelre.  Kattintson **a Tömörítés gombra.** 

* Ellenőrizze, hogy a tömörítés engedélyezve van-e.
* Ellenőrizze, hogy a **Fájltípusok** lista a MIME-típusok vesszővel tagolt listáját tartalmazza-e (szóközök nélkül).
* Ellenőrizze, hogy a tömörítendő tartalom MIME-típusa szerepel-e a tömörített formátumok listájában.

![CDN prémium szintű tömörítési beállítások](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>A tartalom gyorsítótárazásának ellenőrzése (Verizon CDN-profilok)
> [!NOTE]
> Ez a lépés csak akkor érvényes, ha a CDN-profil egy **Azure CDN standard a Verizon** vagy az Azure **CDN Premium a Verizon-profilból.**
> 
> 

A böngésző fejlesztői eszközeivel ellenőrizze a válaszfejléceket, hogy a fájl a rendszer a kért régióban legyen gyorsítótárazva.

* Ellenőrizze a **Kiszolgáló** válaszfejlécét.  A fejlécnek **platformformátummal (POP/Server ID) kell rendelkeznie,** ahogy az a következő példában látható.
* Ellenőrizze az **X-Cache** válaszfejlécet.  A fejléc ben kell olvasni **HIT**.  

![CDN-válaszfejlécek](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Ellenőrizze, hogy a fájl megfelel-e a méretkövetelményeknek (Verizon CDN-profilok)
> [!NOTE]
> Ez a lépés csak akkor érvényes, ha a CDN-profil egy **Azure CDN standard a Verizon** vagy az Azure **CDN Premium a Verizon-profilból.**
> 
> 

A tömörítésre való jogosultsághoz a fájlnak a következő méretkövetelményeknek kell megfelelnie:

* 128 bájtnál nagyobb.
* 1 MB-nál kisebb.

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>A forráskiszolgálón a **Via** fejléc ellenőrzése
A **HTTP-n keresztül** fejléc azt jelzi a webkiszolgálónak, hogy a kérelmet egy proxykiszolgáló továbbítja.  A Microsoft IIS webkiszolgálói alapértelmezés szerint nem tömörítik a válaszokat, ha a kérelem **Via** fejlécet tartalmaz.  A viselkedés felülbírálásához hajtsa végre az alábbi műveleteket:

* **IIS 6**: [A HcNoCompressionForProxies="FALSE" beállítása az IIS metabázis-tulajdonságaiközött](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90))
* **IIS 7 és újabb:** [Állítsa mind a **noCompressionForHttp10-et,** mind **a noCompressionForProxies értéket** False-ra a kiszolgáló konfigurációjában](https://www.iis.net/configreference/system.webserver/httpcompression)

