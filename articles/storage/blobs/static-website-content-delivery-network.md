---
title: Statikus webhely integrálása az Azure CDN - Azure Storage szolgáltatással
description: Ismerje meg, hogyan gyorsítótárazhat statikus webhelytartalmat egy Azure Storage-fiókból az Azure Content Delivery Network (CDN) használatával.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 01/22/2020
ms.openlocfilehash: 8eeff5187d27cb75b9e55eba8311dede8970bc4a
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435223"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>Statikus webhely integrálása az Azure CDN-nel

Engedélyezheti, hogy [az Azure Content Delivery Network (CDN)](../../cdn/cdn-overview.md) gyorsítótárazhassa a tartalmat egy Azure-tárfiókban üzemeltetett statikus [webhelyről.](storage-blob-static-website.md) Az Azure CDN segítségével konfigurálhatja az egyéni tartomány végponta a statikus webhely, egyéni TLS/SSL-tanúsítványok kiépítése, és egyéni újraírási szabályok konfigurálása. Az Azure CDN konfigurálása további díjakat eredményez, de a világ bármely pontjáról konzisztens, alacsony késéseket biztosít a webhelyére. Az Azure CDN is biztosít TLS-titkosítást a saját tanúsítványt. 

Az Azure CDN-díjszabásáról az [Azure CDN-díjszabáscímű](https://azure.microsoft.com/pricing/details/cdn/)témakörben talál.

## <a name="enable-azure-cdn-for-your-static-website"></a>Az Azure CDN engedélyezése a statikus webhelyhez

Az Azure CDN-t közvetlenül a tárfiókból engedélyezheti a statikus webhelyhez. Ha szeretne speciális konfigurációkat beállítani a CDN-végponthoz (például a [nagyméretű fájl letöltésének optimalizálását](../../cdn/cdn-optimization-overview.md#large-file-download)), az [Azure CDN bővítményt](../../cdn/cdn-create-new-endpoint.md) is használhatja CDN-profil és végpont létrehozásához.

1. Keresse meg a tárfiókot az Azure Portalon, és jelenítse meg a fiók áttekintését.

2. Válassza ki az **Azure CDN-t** a **Blob Service** menüben.

    Megjelenik az **Azure CDN** oldal.

    ![CDN-végpont létrehozása](../../cdn/media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png)

3. A **CDN-profil** szakaszban adjon meg egy új vagy meglévő CDN-profilt. 

4. Adja meg a CDN-végpont tarifacsomagjának megadását. Ha többet szeretne megtudni az árképzésről, olvassa el a [Content Delivery Network díjszabása](https://azure.microsoft.com/pricing/details/cdn/). Az egyes szintekhez elérhető funkciókról az [Azure CDN-termékfunkciók összehasonlítása című](../../cdn/cdn-features.md)témakörben talál további információt.

5. A **CDN-végpont neve** mezőben adja meg a CDN-végpont nevét. A CDN-végpontnak egyedinek kell lennie az Azure-ban.

6. Adja meg, hogy ön a statikus webhelyvégpont az **Origin állomásnév** mezőben. 

   A statikus webhelyvégpont megkereséséhez keresse meg a **tárfiók statikus** webhelybeállításait.  Másolja az elsődleges végpontot, és illessze be a CDN-konfigurációba.

   > [!IMPORTANT]
   > Győződjön meg arról, hogy eltávolítja a protokollazonosítót *(pl.* HTTPS) és a záró perjelet az URL-ben. Ha például a statikus webhelyvégpont a `https://mystorageaccount.z5.web.core.windows.net/`, `mystorageaccount.z5.web.core.windows.net` akkor az **Origin állomásnév** mezőben kell megadnia.

   Az alábbi képen egy példa végpont konfiguráció:

   ![A CDN-végpont mintakonfigurációját bemutató képernyőkép](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

7. Válassza **a Létrehozás**lehetőséget, majd várja meg, amíg propagál. A létrejött végpont megjelenik a végpontok listájában.

8. Annak ellenőrzéséhez, hogy a CDN-végpont megfelelően van-e konfigurálva, kattintson a végpontra a beállításokhoz való navigáláshoz. A tárfiók CDN-áttekintése alapján keresse meg a végpont állomásnevét, és keresse meg a végpontot, ahogy az az alábbi képen látható. A CDN-végpont formátuma hasonló `https://staticwebsitesamples.azureedge.net`lesz a hoz.

    ![A CDN-végpont áttekintését bemutató képernyőkép](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

9. A CDN-végpont propagálásának befejezése után a CDN-végpontra való navigálás megjeleníti a statikus webhelyre korábban feltöltött index.html fájl tartalmát.

10. A CDN-végpont kezdőpontjának áttekintéséhez keresse meg az **Origin** t a CDN-végpont **Beállítások** szakaszában. Látni fogod, hogy az **Origin típusú** mező *egyéni eredetre* van állítva, és hogy az **Origin állomásnév** mező megjeleníti a statikus webhelyvégpontot.

    ![A CDN-végpont Origin-beállításait bemutató képernyőkép](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>Tartalmak eltávolítása az Azure CDN-ről

Ha egy objektumot nem szeretne a továbbiakban gyorsítótárazni az Azure CDN-ben, hajtsa végre a következő műveletek valamelyikét:

* Állítsa a tárolót privátra (nyilvános helyett). További információt a [Névtelen olvasási hozzáférés kezelése a tárolókhoz és blobokhoz című témakörben talál.](storage-manage-access-to-resources.md)
* Tiltsa le vagy törölje a CDN-végpontot az Azure Portalon.
* Módosítsa az üzemeltetett szolgáltatást, hogy ne válaszoljon az objektumra vonatkozó kérelmekre.

Egy, az Azure CDN-ben már gyorsítótárazott objektum mindaddig gyorsítótárazva marad, amíg az adott objektum élettartama le nem jár, vagy amíg a végpontot [véglegesen nem törli](../../cdn/cdn-purge-endpoint.md). Amikor az élettartam lejár, az Azure CDN megállapítja, hogy a CDN-végpont továbbra is érvényes, az objektum pedig névtelenül továbbra is elérhető-e. Ha nem, az objektum a továbbiakban nem lesz gyorsítótárazva.

## <a name="next-steps"></a>További lépések

(Nem kötelező) Egyéni tartomány hozzáadása az Azure CDN-végponthoz. [Lásd: Oktatóanyag: Egyéni tartomány hozzáadása az Azure CDN-végponthoz.](../../cdn/cdn-map-content-to-custom-domain.md)
