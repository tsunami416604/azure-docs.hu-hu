---
title: Statikus webhely integrálása az Azure CDN - Azure Storage szolgáltatással
description: Ismerje meg, hogyan gyorsítótárazhat statikus webhelytartalmat egy Azure Storage-fiókból az Azure Content Delivery Network (CDN) használatával.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 04/07/2020
ms.openlocfilehash: 4516e9f48174a0f1f5201c46cf114badf13d99d6
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878813"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>Statikus webhely integrálása az Azure CDN-nel

Engedélyezheti, hogy [az Azure Content Delivery Network (CDN)](../../cdn/cdn-overview.md) gyorsítótárazhassa a tartalmat egy Azure-tárfiókban üzemeltetett statikus [webhelyről.](storage-blob-static-website.md) Az Azure CDN segítségével konfigurálhatja az egyéni tartomány végponta a statikus webhely, egyéni TLS/SSL-tanúsítványok kiépítése, és egyéni újraírási szabályok konfigurálása. Az Azure CDN konfigurálása további díjakat eredményez, de a világ bármely pontjáról konzisztens, alacsony késéseket biztosít a webhelyére. Az Azure CDN is biztosít TLS-titkosítást a saját tanúsítványt. 

Az Azure CDN-díjszabásáról az [Azure CDN-díjszabáscímű](https://azure.microsoft.com/pricing/details/cdn/)témakörben talál.

## <a name="enable-azure-cdn-for-your-static-website"></a>Az Azure CDN engedélyezése a statikus webhelyhez

Az Azure CDN-t közvetlenül a tárfiókból engedélyezheti a statikus webhelyhez. Ha szeretne speciális konfigurációkat beállítani a CDN-végponthoz (például a [nagyméretű fájl letöltésének optimalizálását](../../cdn/cdn-optimization-overview.md#large-file-download)), az [Azure CDN bővítményt](../../cdn/cdn-create-new-endpoint.md) is használhatja CDN-profil és végpont létrehozásához.

1. Keresse meg a tárfiókot az Azure Portalon, és jelenítse meg a fiók áttekintését.

1. A **Blob Service** menüben válassza az **Azure CDN** lehetőséget az **Azure CDN-lap** megnyitásához:

    ![CDN-végpont létrehozása](media/storage-blob-static-website-custom-domain/cdn-storage-new.png)

1. A **CDN-profil** szakaszban adja meg, hogy új CDN-profilt szeretne-e létrehozni, vagy egy meglévőt szeretne használni. A CDN-profil cdn-végpontok gyűjteménye, amelyek egy tarifarétegés szolgáltató közös. Ezután adja meg a CDN egyedi nevét az előfizetésen belül.

1. Adja meg a CDN-végpont tarifacsomagjának megadását. Ha többet szeretne megtudni az árképzésről, olvassa el a [Content Delivery Network díjszabása](https://azure.microsoft.com/pricing/details/cdn/). Az egyes szintekhez elérhető funkciókról az [Azure CDN-termékfunkciók összehasonlítása című](../../cdn/cdn-features.md)témakörben talál további információt.

1. A **CDN-végpont neve** mezőben adja meg a CDN-végpont nevét. A CDN-végpontnak egyedinek kell lennie az Azure-ban, és a végpont URL-címének első részét kell adnia. Az űrlap ellenőrzi, hogy a végpont neve egyedi-e.

1. Adja meg a statikus webhelyvégpontot az **Origin állomásnév** mezőben. 

   A statikus webhelyvégpont megkereséséhez keresse meg a **tárfiók statikus** webhelybeállításait.  Másolja az elsődleges végpontot, és illessze be a CDN-konfigurációba.

   > [!IMPORTANT]
   > Győződjön meg arról, hogy eltávolítja a protokollazonosítót *(pl.* HTTPS) és a záró perjelet az URL-ben. Ha például a statikus webhelyvégpont a `https://mystorageaccount.z5.web.core.windows.net/`, `mystorageaccount.z5.web.core.windows.net` akkor az **Origin állomásnév** mezőben kell megadnia.

   Az alábbi képen egy példa végpont konfiguráció:

   ![A CDN-végpont mintakonfigurációját bemutató képernyőkép](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Válassza **a Create (Létrehozás)** lehetőséget, majd várja meg, amíg a CDN kiépíti. A létrejött végpont megjelenik a végpontok listájában. (Ha bármilyen hiba történik az űrlapon, egy felkiáltójel jelenik meg a mező mellett.)

1. Annak ellenőrzéséhez, hogy a CDN-végpont megfelelően van-e konfigurálva, kattintson a végpontra a beállításokhoz való navigáláshoz. A tárfiók CDN-áttekintése alapján keresse meg a végpont állomásnevét, és keresse meg a végpontot, ahogy az az alábbi képen látható. A CDN-végpont formátuma hasonló `https://staticwebsitesamples.azureedge.net`lesz a hoz.

    ![A CDN-végpont áttekintését bemutató képernyőkép](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

1. A CDN-végpont kiépítése után a CDN-végpontra való navigálás megjeleníti a statikus webhelyre korábban feltöltött index.html fájl tartalmát.

1. A CDN-végpont kezdőpontjának áttekintéséhez keresse meg az **Origin** t a CDN-végpont **Beállítások** szakaszában. Látni fogod, hogy az **Origin típusú** mező *egyéni eredetre* van állítva, és hogy az **Origin állomásnév** mező megjeleníti a statikus webhelyvégpontot.

    ![A CDN-végpont Origin-beállításait bemutató képernyőkép](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>Tartalmak eltávolítása az Azure CDN-ről

Ha egy objektumot nem szeretne a továbbiakban gyorsítótárazni az Azure CDN-ben, hajtsa végre a következő műveletek valamelyikét:

* Állítsa a tárolót privátra (nyilvános helyett). További információt a [Névtelen olvasási hozzáférés kezelése a tárolókhoz és blobokhoz című témakörben talál.](storage-manage-access-to-resources.md)
* Tiltsa le vagy törölje a CDN-végpontot az Azure Portalon.
* Módosítsa az üzemeltetett szolgáltatást, hogy ne válaszoljon az objektumra vonatkozó kérelmekre.

Egy, az Azure CDN-ben már gyorsítótárazott objektum mindaddig gyorsítótárazva marad, amíg az adott objektum élettartama le nem jár, vagy amíg a végpontot [véglegesen nem törli](../../cdn/cdn-purge-endpoint.md). Amikor az élettartam lejár, az Azure CDN megállapítja, hogy a CDN-végpont továbbra is érvényes, az objektum pedig névtelenül továbbra is elérhető-e. Ha nem, az objektum a továbbiakban nem lesz gyorsítótárazva.

## <a name="next-steps"></a>További lépések

(Nem kötelező) Egyéni tartomány hozzáadása az Azure CDN-végponthoz. [Lásd: Oktatóanyag: Egyéni tartomány hozzáadása az Azure CDN-végponthoz.](../../cdn/cdn-map-content-to-custom-domain.md)
