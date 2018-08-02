---
title: Az Azure CDN használatával HTTPS-kapcsolaton keresztül egyéni tartománnyal rendelkező blobok elérése
description: Ismerje meg, hogyan integrálható az Azure CDN a blob storage-egyéni tartománnyal rendelkező blobok elérése a HTTPS-kapcsolaton keresztül
services: storage
author: michaelhauss
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: mihauss
ms.component: blobs
ms.openlocfilehash: 7c4acc7d0832442b94735619ea3a01cb319da993
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398255"
---
# <a name="using-the-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Az Azure CDN használatával HTTPS-kapcsolaton keresztül egyéni tartománnyal rendelkező blobok elérése
Az Azure Content Delivery Network (CDN) a HTTPS már támogatja az egyéni tartománynevek. Ez a funkció eléréséhez a storage-blobokat használ, az egyéni tartomány HTTPS-kapcsolaton keresztül használhatja. Ehhez először szüksége engedélyezi az Azure CDN a blob vagy webes végponton, és a CDN leképezése egy egyéni tartomány nevét. Az egyéni tartomány HTTPS engedélyezése után ezeket a lépéseket egyszerűsödött keresztül egykattintásos lehetővé tétele, teljes körű, és az összes, a normál CDN díjszabás további költség nélkül.

Ez a lehetőség azért fontos, mert lehetővé teszi a védelmét és az átvitel során az érzékeny webes alkalmazásadatok adatok integritását. Az SSL protokoll használatával HTTPS-kapcsolaton keresztül forgalmat biztosítja, hogy adat titkosítva van, amikor az interneten keresztül elküldi. HTTPS megbízhatósági és hitelesítést biztosít, és támadások ellen védi a webes alkalmazások.

> [!NOTE]  
> SSL-támogatás az egyéni tartománynevek amellett az Azure CDN segítségével skálázhatja alkalmazását a világ különböző pontjain található tartalmak nagy sávszélességű kézbesítéséhez. További tudnivalókért tekintse meg [az Azure CDN áttekintése](../../cdn/cdn-overview.md).

## <a name="quick-start"></a>Első lépések
HTTPS engedélyezése az egyéni blob storage-végpont szükséges lépések az alábbiak:

1.  [Az Azure storage-fiók integrálása az Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).
    Ez a cikk végigvezeti egy storage-fiók létrehozása az Azure Portalon, ha még nem meg már.

    > [!NOTE]  
    > Az Azure Storage statikus webhelyek támogatását az előzetes verzióban válassza ki az "egyéni forrás" a "forrás típusa" legördülő menüre, és adja hozzá a tárolási webes végpontra. Az Azure-portálon kell ehhez a közvetlenül a storage-fiókja helyett a CDN-profil.

2.  [Az Azure CDN-tartalom leképezése egyéni tartományra](../../cdn/cdn-map-content-to-custom-domain.md).
3.  [Az Azure CDN egyéni tartományon HTTPS engedélyezése](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Közös hozzáférésű Jogosultságkódok
A blob storage-végpont letiltása a névtelen olvasási hozzáférés van beállítva, ha kell adnia egy [közös hozzáférésű Jogosultságkód (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) token az egyes kérések választja ki az egyéni tartományban. Alapértelmezés szerint a blob storage-végpont letiltása a névtelen olvasási hozzáférés. Lásd: [tárolók és blobok névtelen olvasási hozzáférésének kezelése](storage-manage-access-to-resources.md) közös hozzáférésű jogosultságkódok további tájékoztatást.

Az Azure CDN nem veszi figyelembe a SAS-jogkivonat hozzáadott korlátozások. Például a SAS-tokenek lejárati időt rendelkezik. Ez azt jelenti, hogy a tartalom továbbra is elérhető egy lejárt SAS használatával, amíg a tartalom a CDN-határcsomópontra van törölve. Szabályozhatja, hogy mennyi ideig adatokat a rendszer gyorsítótárazza a CDN-en a gyorsítótár válaszfejléc beállításával. Lásd: [kezelése az Azure CDN-ben az Azure Storage-BLOB elévülésének](../../cdn/cdn-manage-expiration-of-blob-content.md) útmutatást.

Ha hoz létre a blob egyazon végpont több SAS URL-címek, azt javasoljuk, ne tudják bekapcsolni a lekérdezési karakterláncot az Azure CDN-gyorsítótárazás. Ez azért szükséges, hogy minden egyes URL-címe egyedi egységként kezelik. Lásd: [szabályozása az Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési karakterláncokkal](../../cdn/cdn-query-string.md) további információt.

## <a name="http-to-https-redirection"></a>HTTP – HTTPS átirányításról
HTTP-forgalom átirányítása HTTPS, választhatja. Ehhez a ajánlat verizon Azure CDN premium használatát. Kell [felülbírálása HTTP viselkedését az Azure CDN szabálymotorral](../../cdn/cdn-rules-engine.md) a következő szabálynak:

![](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

"Cdn-végpont-name", a CDN-végpontra beállított nevére hivatkozik. Ezt az értéket a legördülő listából választhatja meg. Az elérési utat, ahol a statikus tartalom található forrás tárfiókon belül "Forrás elérési útja" hivatkozik. Egyetlen tároló összes statikus tartalmat üzemelteti, ha "forrás elérési útja" cserélje a tároló neve.

Szabályok részletesebben megismerni, tekintse meg a [Azure CDN szabálymotor funkciói](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Árak és számlázás
Egy Azure CDN keresztül érhetők el a blobokat, ha fizet [Blob storage-díjak](https://azure.microsoft.com/pricing/details/storage/blobs/) a forrás (Blob storage), és a peremhálózati csomópontok közötti forgalom és [CDN díjai](https://azure.microsoft.com/pricing/details/cdn/) a élcsomópontokból elért adatok esetében.

Tegyük fel például, van egy storage-fiókot, amely használatban van egy Azure CDN szolgáltatás használata az USA nyugati régiójában. Ha az Egyesült Királyságban valaki megpróbál hozzáférés blobok a tárfiók a CDN-en keresztül, az Azure először ellenőrzi az élcsomópont legközelebb az Egyesült Királyság, hogy a BLOB. Ha található, akkor fér hozzá, hogy a BLOB másolása és fogja használni a CDN-díjszabás, mivel a CDN-en van használatban. Ha nem talál, az Azure a blob másolása az élcsomóponthoz, amely a Blob storage szolgáltatás díjszabása a kimenő forgalom és a tranzakciós díjakat eredményez, és majd érheti el a fájlt az élcsomóponton, mely a CDN számlázásának.

Ha megnézzük a [CDN-díjszabást ismertető oldalon](https://azure.microsoft.com/pricing/details/cdn/), vegye figyelembe, hogy a HTTPS-támogatás az egyéni tartománynevek csak akkor érhető el az Azure CDN a Verizontól termékek (Standard és prémium).

## <a name="next-steps"></a>További lépések
* [A Blob storage-végpont egyéni tartománynév konfigurálása](storage-custom-domain-name.md)
* [Statikus webhely üzemeltetése az Azure Storage (előzetes verzió)](storage-blob-static-website.md)
