---
title: Az egyéni tartományokkal rendelkező Blobok elérése a Azure CDN használatával HTTPS-kapcsolaton keresztül
description: Ismerje meg, hogyan integrálhatja a Azure CDNt a blob Storage szolgáltatással a Blobok egyéni tartományokkal való eléréséhez HTTPS használatával
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 3ad599182191e41ea43d38260692a7ab46e1af6f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68844998"
---
# <a name="use-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Az egyéni tartományokkal rendelkező Blobok elérése a Azure CDN használatával HTTPS-kapcsolaton keresztül

Az Azure Content Delivery Network (Azure CDN) mostantól támogatja a HTTPS-t az egyéni tartománynevek esetében. A Azure CDN használatával a blobokat az egyéni tartománynévvel érheti el HTTPS-kapcsolaton keresztül. Ehhez engedélyezze Azure CDN a blob vagy a webes végponton, majd a Azure CDNt egy egyéni tartománynévre kell leképezni. Ha elkészült, az Azure leegyszerűsíti a HTTPS használatának engedélyezését az egyéni tartományhoz egy kattintással elérhető hozzáférés és a Tanúsítványkezelő teljes körű felügyeletével. A normál Azure CDN díjszabása nem növekszik.

A Azure CDN a webalkalmazási adatok adatvédelmének és adatintegritásának védelmét nyújtja az átvitel során. Ha az SSL protokollt használja a forgalom HTTPS protokollon keresztül történő kiszolgálására, Azure CDN titkosítja az adatokat, amikor az interneten keresztül küldi el. A HTTPS és a Azure CDN segítségével megvédheti webalkalmazásait a támadástól.

> [!NOTE]  
> Amellett, hogy az egyéni tartománynevek SSL-támogatását is biztosítja, a Azure CDN segítségével méretezhetővé teheti az alkalmazást, hogy nagy sávszélességű tartalmat nyújtson a világ minden tájáról. További információ: [Azure CDN áttekintése](../../cdn/cdn-overview.md).

## <a name="quickstart"></a>Gyors üzembe helyezés

Ha engedélyezni szeretné a HTTPS-t az egyéni blob Storage-végponthoz, tegye a következőket:

1.  [Azure Storage-fiók integrálása Azure CDNokkal](../../cdn/cdn-create-a-storage-account-with-cdn.md).  
    Ez a cikk végigvezeti a Storage-fiók létrehozásán a Azure Portalban, ha még nem tette meg.

    > [!NOTE]  
    > Ha a statikus webhelyek Azure Storage-támogatásának előzetes verziójában szeretné felvenni a Storage web-végpontot, válassza az **Egyéni forrás** lehetőséget a **forrás típusa** legördülő listában. A Azure Portal az Azure CDN-profilból kell ezt megtennie, nem közvetlenül a Storage-fiókjában.

2.  [Azure CDN tartalom leképezése egyéni tartományra](../../cdn/cdn-map-content-to-custom-domain.md).

3.  [Engedélyezze a HTTPS-t egy Azure CDN egyéni tartományon](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Közös hozzáférésű aláírások

Alapértelmezés szerint a blob Storage-végpontok nem engedélyezik a névtelen olvasási hozzáférést. Ha a blob Storage-végpont úgy van konfigurálva, hogy a névtelen olvasási hozzáférés ne legyen engedélyezve, adjon meg egy [közös hozzáférési aláírási](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) jogkivonatot az egyéni tartományhoz tartozó minden kérelemben. További információkért lás a [tárolók és blobok névtelen olvasási hozzáférésének kezelésével](storage-manage-access-to-resources.md) foglalkozó témakört.

A Azure CDN nem veszi figyelembe a közös hozzáférésű aláírási jogkivonathoz hozzáadott korlátozásokat. Például az összes megosztott hozzáférés aláírási jogkivonata lejár. Továbbra is elérheti a tartalmakat egy lejárt közös hozzáférési aláírással, amíg a tartalmat el nem távolítja az Azure CDN peremhálózati csomópontokból. A gyorsítótár válaszfejlécének beállításával szabályozható, hogy a rendszer meddig tárolja az adatokat az Azure CDN-ben. További információ: az [Azure Storage-Blobok lejáratának kezelése Azure CDNban](../../cdn/cdn-manage-expiration-of-blob-content.md).

Ha ugyanahhoz a blob-végponthoz két vagy több közös hozzáférésű aláírási URL-címet hoz létre, javasoljuk, hogy kapcsolja be a lekérdezési karakterlánc gyorsítótárazását a Azure CDN. Ez a művelet biztosítja, hogy az Azure az egyes URL-címeket egyedi entitásként kezelje. További információkért lásd: [Az Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési sztringekkel](../../cdn/cdn-query-string.md).

## <a name="http-to-https-redirection"></a>HTTP – HTTPS-átirányítás

A HTTP-forgalom átirányítható HTTPS-re. Ehhez szükség van a Verizon Azure CDN Premium ajánlatának használatára. A következő szabály alkalmazásával [bírálja felül a http-viselkedést a Azure CDN-szabályok motorjának](../../cdn/cdn-rules-engine.md) használatával:

![HTTP – HTTPS átirányítási szabály](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

*CDN – végpont –* a legördülő listában kiválasztott név a Azure CDN végponthoz konfigurált nevet jelöli. *Forrás – az elérési út* a forrásként szolgáló Storage-fiókban található elérési útra hivatkozik, ahol a statikus tartalmat tárolja a rendszer. Ha egyetlen tárolóban található összes statikus tartalmat üzemelteti, cserélje le az *Origin-Path* nevet a tároló nevére.

A szabályok mélyebb betekintéséhez tekintse meg a [Azure CDN szabályok motorjának funkcióit](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Árak és számlázás

Ha Azure CDNon keresztül fér hozzá a blobokhoz, a peremhálózati csomópontok és a forrás (blob Storage) közötti adatforgalomért a [blob Storage árát](https://azure.microsoft.com/pricing/details/storage/blobs/) kell fizetnie. A peremhálózati csomópontokból elért adatokért [Azure CDN árat](https://azure.microsoft.com/pricing/details/cdn/) kell fizetnie.

Tegyük fel például, hogy van egy olyan Storage-fiókja az USA nyugati régiójában, amely Azure CDNon keresztül éri el. Ha az Egyesült Királyságban valaki a Azure CDNon keresztül próbál hozzáférni egy blobhoz, az Azure először az Egyesült Királysághoz legközelebb eső peremhálózati csomópontban ellenőrzi a blobot. Ha az Azure megkeresi a blobot, hozzáfér egy másolathoz, és Azure CDN díjszabást használ, mert Azure CDN hozzáfér. Ha az Azure nem találja a blobot, a blobot a peremhálózati csomópontba másolja. Ez a művelet kimenő és tranzakciós díjakat eredményez, a blob Storage díjszabásának megfelelően. Az Azure ezután hozzáfér a fájlhoz a peremhálózati csomóponton, amely Azure CDN számlázást eredményez.

A [Azure CDN díjszabása lapon](https://azure.microsoft.com/pricing/details/cdn/)az egyéni tartománynevek HTTPS-támogatása csak a Verizon standard és a Premium termékek Azure CDN érhető el.

## <a name="next-steps"></a>További lépések

* [Egyéni tartománynév beállítása a blob Storage-végponthoz](storage-custom-domain-name.md)
* [Statikus webhely üzemeltetése az Azure Storage-ban](storage-blob-static-website.md)
