---
title: Statikus webhely integrálása a Azure CDN Azure Storage szolgáltatással
description: Ismerje meg, hogyan gyorsítótárazhatja a statikus webhelyek tartalmát egy Azure Storage-fiókból az Azure Content Delivery Network (CDN) használatával.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 01/22/2020
ms.openlocfilehash: aaf61ccbb3577036c614aa6196d2af57124550fa
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76908552"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>Statikus webhely integrálása Azure CDN

Az [azure Content Delivery Network (CDN)](../../cdn/cdn-overview.md) szolgáltatás lehetővé teszi, hogy egy Azure Storage-fiókban üzemeltetett [statikus webhelyről](storage-blob-static-website.md) gyorsítótárazza a tartalmat. A Azure CDN használatával konfigurálhatja a statikus webhely egyéni tartomány végpontját, kiépítheti az egyéni SSL-tanúsítványokat, és konfigurálhatja az egyéni Újraírási szabályokat. A Azure CDN a további költségekkel jár, de a világ bármely pontján elérhetővé teszi a webhelye számára a konzisztens kis késleltetést. A Azure CDN a saját tanúsítvánnyal is biztosít SSL-titkosítást. 

További információ a Azure CDN díjszabásáról: [Azure CDN díjszabása](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="enable-azure-cdn-for-your-static-website"></a>Azure CDN engedélyezése a statikus webhelyhez

A statikus webhely Azure CDNét közvetlenül a Storage-fiókjából engedélyezheti. Ha szeretne speciális konfigurációkat beállítani a CDN-végponthoz (például a [nagyméretű fájl letöltésének optimalizálását](../../cdn/cdn-optimization-overview.md#large-file-download)), az [Azure CDN bővítményt](../../cdn/cdn-create-new-endpoint.md) is használhatja CDN-profil és végpont létrehozásához.

1. Keresse meg a Storage-fiókját a Azure Portalban, és jelenítse meg a fiók áttekintését.

2. A Azure CDN konfigurálásához válassza **Azure CDN** a **blob szolgáltatás** menüjében.

    Megjelenik az **Azure CDN** oldal.

    ![CDN-végpont létrehozása](../../cdn/media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png)

3. A **CDN-profil** szakaszban válasszon egy új vagy meglévő CDN-profilt. 

4. Határozza meg a CDN-végpont díjszabási szintjét. A díjszabással kapcsolatos további tudnivalókért tekintse meg a [Content Delivery Network díjszabását](https://azure.microsoft.com/pricing/details/cdn/). Az egyes rétegek szolgáltatásaival kapcsolatos további információkért lásd: [Azure CDN termék funkcióinak összehasonlítása](../../cdn/cdn-features.md).

5. A **CDN-végpont neve** mezőben adja meg a CDN-végpont nevét. A CDN-végpontnak egyedinek kell lennie az Azure-ban.

6. Itt adhatja meg a statikus webhely végpontját a **forrás állomásneve** mezőben. 

   A statikus webhely végpontjának megkereséséhez keresse meg a **statikus webhely** beállításait a Storage-fiókhoz.  Másolja az elsődleges végpontot, és illessze be a CDN-konfigurációba.

   > [!IMPORTANT]
   > Ügyeljen arra, hogy a protokoll azonosítóját (*például*HTTPS) és az URL-cím záró perjelét távolítsa el. Ha például a statikus webhely végpontja `https://mystorageaccount.z5.web.core.windows.net/`, akkor a **forrás állomásneve** mezőben meg kell adnia `mystorageaccount.z5.web.core.windows.net`.

   Az alábbi képen egy példa végpont-konfiguráció látható:

   ![A példa CDN-végpont konfigurációját bemutató képernyőkép](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

7. Válassza a **Létrehozás**lehetőséget, majd várjon, amíg propagálja. A létrejött végpont megjelenik a végpontok listájában.

8. Annak ellenőrzéséhez, hogy a CDN-végpont megfelelően van-e konfigurálva, kattintson a végpontra, és navigáljon a beállításaihoz. A Storage-fiókhoz tartozó CDN áttekintésében keresse meg a végpont állomásnevét, és navigáljon a végponthoz az alábbi ábrán látható módon. A CDN-végpont formátuma hasonló lesz a `https://staticwebsitesamples.azureedge.net`hoz.

    ![A CDN-végpont áttekintését bemutató képernyőkép](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

9. A CDN-végpont propagálásának befejeződése után a CDN-végpontra való navigálás megjeleníti a statikus webhelyre korábban feltöltött index. html fájl tartalmát.

10. A CDN-végpont forrás-beállításainak áttekintéséhez navigáljon a **forráshoz** a CDN-végpont **Beállítások** szakaszában. Látni fogja, hogy a **forrás típusa** mező *Egyéni forrásként* van beállítva, és a **forrás állomásnév** mező megjeleníti a statikus webhely végpontját.

    ![A CDN-végpont forrás-beállításainak megjelenítését bemutató képernyőkép](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>Tartalmak eltávolítása az Azure CDN-ről

Ha egy objektumot nem szeretne a továbbiakban gyorsítótárazni az Azure CDN-ben, hajtsa végre a következő műveletek valamelyikét:

* Állítsa a tárolót privátra (nyilvános helyett). További információkért lás a [tárolók és blobok névtelen olvasási hozzáférésének kezelésével](storage-manage-access-to-resources.md) foglalkozó témakört.
* Tiltsa le vagy törölje a CDN-végpontot az Azure Portalon.
* Módosítsa az üzemeltetett szolgáltatást, hogy ne válaszoljon az objektumra vonatkozó kérelmekre.

Egy, az Azure CDN-ben már gyorsítótárazott objektum mindaddig gyorsítótárazva marad, amíg az adott objektum élettartama le nem jár, vagy amíg a végpontot [véglegesen nem törli](../../cdn/cdn-purge-endpoint.md). Amikor az élettartam lejár, az Azure CDN megállapítja, hogy a CDN-végpont továbbra is érvényes, az objektum pedig névtelenül továbbra is elérhető-e. Ha nem, az objektum a továbbiakban nem lesz gyorsítótárazva.

## <a name="next-steps"></a>Következő lépések

Választható Adjon hozzá egy egyéni tartományt a Azure CDN-végponthoz. Lásd [: oktatóanyag: egyéni tartomány hozzáadása az Azure CDN-végponthoz](../../cdn/cdn-map-content-to-custom-domain.md).
