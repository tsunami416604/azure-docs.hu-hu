---
title: Tárolóblobok elérése Azure CDN-alapú egyéni tartomány használatával HTTPS-kapcsolaton keresztül
description: Megtudhatja, hogyan adhat hozzá egy Azure CDN-egyéni tartományt, és engedélyezheti a HTTPS-t az adott tartományban az egyéni blobtárolási végponthoz.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 5b6fe2b2704f101a7775b7eb700375105b0a9eca
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259884"
---
# <a name="tutorial-access-storage-blobs-using-an-azure-cdn-custom-domain-over-https"></a>Oktatóanyag – Tárolóblobok elérése egyéni Azure CDN-tartomány használatával HTTPS-en keresztül

Miután integrálta az Azure Storage-fiókot az Azure Content Delivery Network (CDN) hálózattal, hozzáadhat egy egyéni tartományt, és engedélyezheti a HTTPS-t a tartományon az egyéni blobtároló végpontjához. 

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elvégezhetné a jelen oktatóanyag lépéseit, először integrálnia kell az Azure Storage-fiókot az Azure CDN-nel. További információért lásd az [Oktatóanyag: Azure Storage-fiók integrálása az Azure CDN-nel](cdn-create-a-storage-account-with-cdn.md) című cikket.

## <a name="add-a-custom-domain"></a>Egyéni tartomány hozzáadása
Miután létrehoz egy CDN-végpontot a profiljában, a végpont neve – amely az azureedge.net altartománya – része lesz annak az URL-címnek, amelyre alapértelmezés szerint a rendszer a CDN-tartalmat irányítja. Emellett lehetősége van arra, hogy CDN-végpontot rendeljen egy egyéni tartományhoz. Így a tartalom továbbításakor az egyéni tartomány neve szerepel majd az URL-ben a végpont neve helyett. Ha egyéni tartományt szeretne hozzáadni a végponthoz kövesse az [Egyéni tartomány hozzáadása az Azure CDN-végponthoz](cdn-map-content-to-custom-domain.md) című oktatóanyagban szereplő utasításokat.

## <a name="configure-https"></a>HTTPS konfigurálása
A HTTPS-protokoll egyéni tartományon belüli használatával biztosítható, hogy a bizalmas adatokat a rendszer biztonságosan, TLS/SSL-titkosításon keresztül továbbítsa az interneten. Amikor a böngésző HTTPS-protokollal kapcsolódik egy webhelyhez, akkor ellenőrzi a webhely biztonsági tanúsítványát, és megállapítja, hogy azt arra jogosult hitelesítésszolgáltató adta-e ki. Ha az egyéni tartományon szeretné konfigurálni a HTTPS-t, kövesse a [HTTPS konfigurálása Azure CDN egyéni tartományon](cdn-custom-ssl.md) című oktatóanyag lépéseit.

## <a name="shared-access-signatures"></a>Közös hozzáférésű jogosultságkódok
Ha a Blob Storage-végpont a névtelen olvasási hozzáférések elutasítására van konfigurálva, akkor minden, az egyéni tartományhoz küldött kérésben meg kell adnia egy [közös hozzáférésű jogosultságkód (SAS-)](cdn-sas-storage-support.md) tokent. Alapértelmezés szerint a Blob Storage-végpontok nem engedélyezik a névtelen olvasási hozzáférést. Az SAS-szel kapcsolatos további információkért lásd a [tárolók és blobok névtelen olvasási hozzáférésének kezelésével](../storage/blobs/storage-manage-access-to-resources.md) foglalkozó témakört.

Az Azure CDN figyelmen kívül hagyja a SAS-jogkivonathoz hozzáadott korlátozásokat. Például minden SAS-token rendelkezik lejárati idővel, ami azt jelenti, hogy a tartalmak lejárt SAS-tokennel is hozzáférhetők, amíg az adott tartalom véglegesen törölve nem lesz a CDN jelenléti pont (POP-) kiszolgálókról. A gyorsítótár válaszfejlécének beállításával szabályozható, hogy a rendszer meddig tárolja az adatokat az Azure CDN-ben. További információkért lásd: [Azure Storage-blobok lejárati idejének kezelése az Azure CDN-ben](cdn-manage-expiration-of-blob-content.md).

Ha ugyanazon végponthoz több SAS URL-címet hoz létre, fontolja meg a lekérdezési sztringek gyorsítótárazásának engedélyezését. Ezzel biztosíthatja, hogy a rendszer minden egyes URL-címet egyedi entitásként kezeljen. További információkért lásd: [Az Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési sztringekkel](cdn-query-string.md).

## <a name="http-to-https-redirection"></a>HTTP–HTTPS átirányítás
Dönthet úgy, hogy a HTTP-forgalmat HTTPS-re irányítja át, ha létrehoz egy URL-átirányítási szabályt a [Standard rules motorral](cdn-standard-rules-engine.md) vagy a [Verizon Premium szabálymotorral.](cdn-verizon-premium-rules-engine.md) A Standard Rules motor csak a Microsoft-profilokból származó Azure CDN-hez érhető el, míg a Verizon prémium szintű szabálymotorja csak a Verizon-profilokból származó Azure CDN Premium szolgáltatásból érhető el.

![Microsoft átirányítási szabály](./media/cdn-storage-custom-domain-https/cdn-standard-redirect-rule.png)

A fenti szabályban az Állomásnév, az Elérési út, a Lekérdezési karakterlánc és a Fragment elhagyása a bejövő értékeket használja az átirányításban. 

![Verizon átirányítási szabály](./media/cdn-storage-custom-domain-https/cdn-url-redirect-rule.png)

A fenti szabályban a *Cdn-végpont neve* a CDN-végponthoz konfigurált névre hivatkozik, amelyet a legördülő listából választhat. A *forrásútvonal* értéke a forrásként szolgáló tárfiókban szereplő útvonalra utal, ahol a statikus tartalmak található. Ha minden statikus tartalmat egyetlen tárolóban tárolja, cserélje le a *forrásútvonal* értékét az adott tároló nevére.

## <a name="pricing-and-billing"></a>Árak és számlázás
Ha a blobokat az Azure CDN-en keresztül éri el, akkor a [Blob Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/blobs/) alapján fizet a POP-kiszolgálók és a forrás (Blob Storage) közötti forgalomért, a POP-kiszolgálókról elért adatokért pedig az [Azure CDN díjszabása](https://azure.microsoft.com/pricing/details/cdn/) alapján.

Ha például rendelkezik egy Storage-fiókkal az Egyesült Államokban, amelyhez az Azure CDN-en keresztül fér hozzá, és valaki Európából az Azure CDN-en keresztül megkísérli az ott található blobok egyikéhez való hozzáférést, az Azure CDN először megkeresi az Európához legközelebb eső POP-kiszolgálót a blob számára. Ha megtalálta, az Azure CDN hozzáfér a blob másolatához, és a CDN díjszabását használja, mert a hozzáférés az Azure CDN-en történik. Ha nem talál ilyet, az Azure CDN átmásolja a blobot a POP-kiszolgálóra, amely a Blob Storage díjszabásában megadott kimenő forgalmi és tranzakciós díjakat vonja maga után, majd a POP-kiszolgálón hozzáfér a fájlhoz, amelyre már az Azure CDN díjszabása vonatkozik.

## <a name="next-steps"></a>További lépések
[Oktatóanyag: Azure CDN gyorsítótárazási szabályainak beállítása](cdn-caching-rules-tutorial.md)




