---
title: Az Azure CDN használatával HTTPS-kapcsolaton keresztül egyéni tartománnyal rendelkező blobok elérése
description: Ismerje meg, hogyan integrálható az Azure CDN egyéni tartománnyal rendelkező blobok elérése a HTTPS-kapcsolaton keresztül a blobtároló használatával
services: storage
author: michaelhauss
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: mihauss
ms.subservice: blobs
ms.openlocfilehash: 90ddb58f3499180e17df559a98ac8a46b53fb824
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579012"
---
# <a name="use-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Az Azure CDN használatával HTTPS-kapcsolaton keresztül egyéni tartománnyal rendelkező blobok elérése

Az Azure Content Delivery Network (az Azure CDN) HTTPS mostantól támogatja az egyéni tartománynevek. Az Azure CDN az egyéni tartománynév használatával HTTPS-kapcsolaton keresztül hozzáférhet a blobokat. Ehhez az Azure CDN engedélyezése a blob vagy webes végponton, és képezze le az Azure CDN egyéni tartománynevét. Miután elkészült, az Azure egyszerűbbé teszi a HTTPS engedélyezése az egykattintásos hozzáférést és teljes körű tanúsítványkezelés egyéni tartományhoz. Nincs a normál Azure CDN-tarifacsomag nem növekszik.

Az Azure CDN segítségével, védelmét és a webalkalmazás adatainak adatok integritását, bár az átvitel során. Az SSL protokoll használatával szolgálja ki a forgalom HTTPS-kapcsolaton keresztül, az Azure CDN biztosítja, hogy titkosítja, amikor az interneten keresztül elküldi az adatokat. HTTPS-en keresztül az Azure CDN segít megvédeni a webes alkalmazások támadásoktól.

> [!NOTE]  
> SSL-támogatás az egyéni tartománynevek amellett az Azure CDN segítségével skálázhatja alkalmazását a világ különböző pontjain található tartalmak nagy sávszélességű kézbesítéséhez. További tudnivalókért lásd: [áttekintése az Azure CDN](../../cdn/cdn-overview.md).

## <a name="quickstart"></a>Első lépések

HTTPS engedélyezése egyéni Blob storage-végponthoz, tegye a következőket:

1.  [Az Azure storage-fiók integrálása az Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).  
    Ez a cikk végigvezeti egy storage-fiók létrehozása az Azure Portalon, ha ezt még nem tette meg.

    > [!NOTE]  
    > Az Azure Storage statikus webhelyek támogatását az előzetes verzióban a storage webes végpont hozzáadásához válassza **egyéni forrás** a a **forrása típusa** legördülő listából. Az Azure Portalon kell ehhez az az Azure CDN-profil helyett közvetlenül a storage-fiókban.

2.  [Az Azure CDN-tartalom leképezése egyéni tartományra](../../cdn/cdn-map-content-to-custom-domain.md).

3.  [Az Azure CDN egyéni tartományon HTTPS engedélyezése](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Közös hozzáférésű jogosultságkódok

Alapértelmezés szerint a Blob storage-végpont letiltása a névtelen olvasási hozzáférés. Ha a Blob storage-végpont letiltása a névtelen olvasási hozzáférés van konfigurálva, adjon meg egy [közös hozzáférésű jogosultságkód](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) token az egyes kérések az egyéni tartományban. További információkért lás a [tárolók és blobok névtelen olvasási hozzáférésének kezelésével](storage-manage-access-to-resources.md) foglalkozó témakört.

Az Azure CDN meghaladja a paraméterméretre, a közös hozzáférésű jogosultságkód hozzáadott korlátozások. Ha például minden közös hozzáférésű jogosultságkódok jogkivonataival lejár. Egy lejárt közös hozzáférésű jogosultságkód tartalmat mindaddig, amíg a tartalom van törlődnek, az Azure CDN-határcsomópontra továbbra is elérheti. A gyorsítótár válaszfejlécének beállításával szabályozható, hogy a rendszer meddig tárolja az adatokat az Azure CDN-ben. További információ [Azure Storage-blobok az Azure CDN lejáratának kezelése](../../cdn/cdn-manage-expiration-of-blob-content.md).

Ha ugyanazt a blob-végpont URL-címei aláírás hoz létre két vagy több, közös hozzáférésű, javasoljuk, ne tudják bekapcsolni a lekérdezési karakterláncok gyorsítótárazása az Azure CDN esetében. Ez a művelet biztosítja, hogy kezeli-e az Azure minden egyes URL-címe egyedi egységként. További információkért lásd: [Az Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési sztringekkel](../../cdn/cdn-query-string.md).

## <a name="http-to-https-redirection"></a>HTTP – HTTPS átirányításról

HTTP-forgalom átirányíthatja a HTTPS-re. Ehhez szükséges, a ajánlat verizon Azure CDN premium használatát. [Az Azure CDN szabályok motor HTTP a működés felülbírálása](../../cdn/cdn-rules-engine.md) úgy, hogy alkalmazza a következő szabálynak:

![HTTP – HTTPS átirányítás szabály](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

*CDN végpontnév*, amely választja a legördülő listában, az Azure CDN-végponthoz beállított nevére hivatkozik. *Forrás elérési-útja* hivatkozik a tárfiókban forrás, a statikus tartalmat tároló elérési útja. Ha az egyetlen tároló összes statikus tartalom üzemeltetési cserélje le a *forrás elérési-útja* , hogy a tároló nevére.

Szabályok részletesebben megismerni, lásd: a [Azure CDN szabálymotor funkciói](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Árak és számlázás

Az Azure CDN keresztül érhetők el a blobokat, ha fizet [Blob storage-díjak](https://azure.microsoft.com/pricing/details/storage/blobs/) a peremhálózati csomópont és a forrás (a Blob storage) közötti forgalom. Fizetés [Azure CDN díjai](https://azure.microsoft.com/pricing/details/cdn/) adatok esetén, amelyek a élcsomópontokból érhető el.

Például tegyük fel, hogy egy storage-fiókot, amely Azure CDN-nel éri el az USA nyugati régiójában. Ha az Egyesült Királyságban valaki megpróbál hozzáférni az Azure CDN-nel a storage-fiókban található blob, az Azure először ellenőrzi az élcsomóponthoz, az Egyesült Királyság legközelebb van az a BLOB. Ha az Azure blob talál, fér hozzá egy másolatot, és használja az Azure CDN-tarifacsomag, mert az Azure CDN fér hozzá. Ha az Azure nem találja a blob, a blob másolja az élcsomópont felé. Ez a művelet a Blob storage szolgáltatás díjszabása a kimenő forgalom és a tranzakciós díjakat eredményez. Az Azure majd fér hozzá a fájl az élcsomóponton, ami az Azure CDN számlázásának eredményez.

Az a [Azure CDN-díjszabást ismertető oldalon](https://azure.microsoft.com/pricing/details/cdn/), az egyéni tartománynevek HTTPS-támogatás érhető el az Azure CDN csak a Verizon Standard és prémium szintű termékek.

## <a name="next-steps"></a>További lépések

* [A Blob storage-végpont egyéni tartománynév konfigurálása](storage-custom-domain-name.md)
* [Statikus webhely üzemeltetése az Azure Storage-ban](storage-blob-static-website.md)
