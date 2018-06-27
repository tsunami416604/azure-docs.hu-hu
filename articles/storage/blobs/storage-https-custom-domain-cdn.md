---
title: Az Azure CDN használatával blobokhoz való hozzáférést az egyéni tartomány HTTPS-KAPCSOLATON keresztül
description: További tudnivalók az Azure CDN integrálása blob-tároló blobokhoz való hozzáférést az egyéni tartomány HTTPS-KAPCSOLATON keresztül
services: storage
documentationcenter: ''
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: mihauss
ms.openlocfilehash: b3b1b5064e51b68bb64cb8c4dbec6075705795d6
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025852"
---
# <a name="using-the-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Az Azure CDN használatával blobokhoz való hozzáférést az egyéni tartomány HTTPS-KAPCSOLATON keresztül
Az Azure Content Delivery Network (CDN) HTTPS mostantól támogatja az egyéni tartománynevek. Kihasználhatja a szolgáltatás használatával az egyéni tartomány HTTPS-KAPCSOLATON keresztül tárolási blobokhoz való hozzáférést. Ehhez először Azure CDN engedélyezése a blob vagy webes végponton, és a CDN leképezése egy egyéni tartománynevet. Ha ezeket a lépéseket HTTPS engedélyezése az egyéni tartomány az egyszerűsített egy kattintással engedélyezését, teljes és minden további költség nélkül a normál a CDN-díjszabást keresztül.

Ez a lehetőség azért fontos, mert lehetővé teszi a biztonságát és az adatok integritását a bizalmas webes alkalmazás adatok az átvitel során. Az SSL protokoll használatával keresztül HTTPS forgalmat biztosítja az adatok titkosítását, ha az interneten keresztül továbbítja azokat. HTTPS megbízhatóság és a hitelesítési biztosít, valamint a webalkalmazások védi a támadások ellen.

> [!NOTE]  
> Csupán az SSL használatához az egyéni tartománynevek, az Azure CDN segítségével az alkalmazás számára a tartalmak nagy sávszélességű kézbesítéséhez a világ különböző méretezni. További tudnivalókért tekintse meg [az Azure CDN áttekintése](../../cdn/cdn-overview.md).

## <a name="quick-start"></a>Első lépések
A HTTPS engedélyezéséhez a egyéni blob storage-végponthoz szükséges lépések a következők:

1.  [Azure-tárfiók integrálása az Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).
    Ez a cikk végigvezeti a storage-fiók létrehozása az Azure portálon, ha még nem meg már.

    > [!NOTE]  
    > Statikus webhelyek kiszolgálására támogatás az Azure Storage előzetes válassza a "egyéni origin" a "forrása típusa" legördülő menüből, hogy a tároló webes végpontjának felvétele. Az Azure-portálon kell ehhez a CDN-profil ahelyett, hogy közvetlenül a tárfiókban lévő az.

2.  [Azure CDN-tartalom hozzárendelése egyéni tartományhoz](../../cdn/cdn-map-content-to-custom-domain.md).
3.  [Engedélyezze a HTTPS, egyéni Azure CDN-tartomány](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Megosztott hozzáférési aláírásokkal
Ha a blob storage endpoint nem engedélyezi a névtelen olvasási hozzáférés van konfigurálva, meg kell adnia egy [közös hozzáférésű Jogosultságkód (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) token mindegyik kérelem elvégezte az egyéni tartomány. Alapértelmezés szerint a blob storage végpontok letiltása a névtelen olvasási hozzáférés. Lásd: [tárolók és blobok névtelen olvasási hozzáférés kezelése](storage-manage-access-to-resources.md) közös hozzáférésű jogosultságkód olvashat.

Az Azure CDN nem veszi figyelembe a SAS-jogkivonat hozzá korlátozásokat. Például minden SAS-tokenje rendelkezik lejárati időt. Ez azt jelenti, hogy a tartalom továbbra is elérhető a egy lejárt SAS használatával, amíg a tartalom peremhálózati CDN csomópontjából véglegesen törlődnek. Azt is szabályozhatja, hogy mennyi ideig adatokat a rendszer gyorsítótárazza a CDN a úgy, hogy a gyorsítótár válaszfejlécet. Lásd: [Azure Storage blobs szolgáltatásban az Azure CDN kezelésében](../../cdn/cdn-manage-expiration-of-blob-content.md) utasításokat.

Ha azonos blob végpont több SAS URL-cím, ajánlott bekapcsolja a gyorsítótárazást az Azure CDN a lekérdezési karakterlánc. Ez azért szükséges, hogy minden URL-címe egyedi egységként kell kezelni. Lásd: [szabályozása Azure CDN a lekérdezési karakterláncok gyorsítótárazásának](../../cdn/cdn-query-string.md) további információt.

## <a name="http-to-https-redirection"></a>HTTP – HTTPS átirányítás
HTTP-forgalom átirányítása HTTPS választhatja. Ez az ajánlat verizon Azure CDN premium használatát igényli. Kell [használata az Azure CDN szabályok felülbírálása HTTP viselkedés](../../cdn/cdn-rules-engine.md) a következő szabálynak:

![](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

"Cdn-végpont-name" CDN-végpont beállított nevére hivatkozik. Ezt az értéket a legördülő listából kiválaszthatja. "Forrás-path" az elérési út belül a statikus tartalmat tároló származási tárfiókját hivatkozik. Ha egyetlen tároló összes statikus tartalmat tároló, cserélje le "forrás-path", hogy a tároló nevét.

Mélyebb bemutatója szabályokat, ellenőrizze a [Azure CDN szabályok motor szolgáltatások](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Árak és számlázás
Blobok az Azure CDN keresztül érhetők el, amikor kell fizetnie [Blob-tároló árak](https://azure.microsoft.com/pricing/details/storage/blobs/) és a forrás (Blob-tároló), a peremhálózati csomópontok közötti forgalom és [CDN árak](https://azure.microsoft.com/pricing/details/cdn/) a peremhálózati csomópontok elért adatok számára.

Tegyük fel például, hogy a storage-fiókot, amely használatban van egy Azure CDN használatával USA nyugati régiója. Esetén az Egyesült Királyságban hozzáférés az, hogy a CDN keresztül tárfiókban lévő blobokat, az Azure először ellenőrzi, hogy a BLOB UK legközelebbi élcsomópont. Ha található, akkor éri el, hogy a BLOB másolása és fogja használni a CDN-díjszabást, mivel a CDN a van használatban. Ha nem található, Azure másolatot készít az élcsomóponthoz, amely a Blob storage szolgáltatás díjszabása a kimenő forgalom és a tranzakciós költségek eredményez, és majd elérni a fájlt a peremhálózati csomóponton, ami CDN számlázási eredményezi, a blob.

Ha a [árképzést ismertető oldalra CDN](https://azure.microsoft.com/pricing/details/cdn/), vegye figyelembe, hogy HTTPS egyéni tartománynevek támogatása csak érhető el az Azure CDN Verizon termékekből (Standard és prémium).

## <a name="next-steps"></a>További lépések
* [A Blob storage-végponthoz egyéni tartománynév konfigurálása](storage-custom-domain-name.md)
* [Statikus webhely üzemeltetéséhez az Azure Storage (előzetes verzió)](storage-blob-static-website.md)
