---
title: Azure Media Services hibakódok | Microsoft Docs
description: HTTP-hibakódokat kaphat a szolgáltatástól attól függően, hogy a hitelesítési tokenek a Media Services által nem támogatott műveletekhez képest lejárnak-e. Ez a cikk áttekintést nyújt Azure Media Services v2 API-hibakódokról.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d3a62a64-7608-4b17-8667-479b26ba0d6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: f5a2dd68d86a7a38fc7f2942351c42c84742d104
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74887068"
---
# <a name="azure-media-services-error-codes"></a>Azure Media Services hibakódok
Microsoft Azure Media Services használatakor HTTP-hibakódok jelenhetnek meg a szolgáltatástól attól függően, hogy a hitelesítési tokenek lejárnak-e a Media Services által nem támogatott műveletekhez. Az alábbi lista a Media Services által visszaadott **http-hibakódokat** és azok lehetséges okait sorolja fel.  

## <a name="400-bad-request"></a>400 hibás kérelem
A kérelem érvénytelen információkat tartalmaz, és a következő okok egyike miatt el lesz utasítva:

* Nem támogatott API-verzió van megadva. A legfrissebb verzióért lásd: [Media Services REST API-fejlesztés beállítása](media-services-rest-how-to-use.md).
* A Media Services API-verziója nincs megadva. További információ az API-verzió megadásáról: [Media Services operations REST API Reference](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).
  
  > [!NOTE]
  > Ha .NET-vagy Java SDK-kat használ a Media Serviceshoz való kapcsolódáshoz, akkor az API-verzió akkor van megadva, ha a Media Services a művelettel próbálkozik, és valamilyen műveletet hajt végre.
  > 
  > 
* Nem definiált tulajdonság lett megadva. A tulajdonság neve a következő hibaüzenetben jelenik meg:. Csak azok a tulajdonságok adhatók meg, amelyek egy adott entitás tagjai. Az entitások és tulajdonságaik listájának [Azure Media Services REST API hivatkozását](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference) itt tekintheti meg.
* Érvénytelen tulajdonságérték lett megadva. A tulajdonság neve a következő hibaüzenetben jelenik meg:. Tekintse meg az előző hivatkozást az érvényes tulajdonságértékek és azok értékeinek megtekintéséhez.
* A tulajdonság értéke hiányzik, és kötelező megadni.
* A megadott URL-cím egy része rossz értéket tartalmaz.
* Kísérlet történt egy WriteOnce tulajdonság frissítésére.
* Kísérlet történt olyan feladat létrehozására, amely olyan elsődleges AssetFile rendelkező bemeneti eszközzel rendelkezik, amely nincs meghatározva vagy nem határozható meg.
* Kísérlet történt egy SAS-lokátor frissítésére. SAS-lokátorok csak létrehozhatók vagy törölhetők. A folyamatos átviteli lokátorok frissíthetők. További információ: [lokátorok](https://docs.microsoft.com/rest/api/media/operations/locator).
* Nem támogatott művelet vagy lekérdezés lett elküldve.

## <a name="401-unauthorized"></a>401 jogosulatlan
A kérést nem lehetett hitelesíteni (a hitelesítés engedélyezése előtt) a következő okok egyike miatt:

* Hiányzó hitelesítési fejléc.
* Helytelen a hitelesítési fejléc értéke.
  * A jogkivonat lejárt. 
  * A jogkivonat érvénytelen aláírást tartalmaz.

## <a name="403-forbidden"></a>403 Tiltott
A kérelem a következő okok egyike miatt nem engedélyezett:

* A Media Services fiók nem található vagy törölve lett.
* A Media Services fiók le van tiltva, és a kérelem típusa nem HTTP GET. A szolgáltatási műveletek a 403-es választ is visszaküldik.
* A hitelesítési jogkivonat nem tartalmazza a felhasználó hitelesítő adatait: AccountName és/vagy SubscriptionId. Ezt az információt az Azure felügyeleti portál Media Services felhasználói felületének bővítményében találja Media Services-fiókjához.
* Az erőforrás nem érhető el.
  
  * Kísérlet történt olyan MediaProcessor használatára, amely nem érhető el a Media Services-fiókjához.
  * Kísérlet történt egy Media Services által meghatározott JobTemplate frissítésére.
  * Kísérlet történt egy másik Media Services-fiók lokátorának felülírására.
  * Kísérlet történt egy másik Media Services fiók ContentKey felülírására.
* Az erőforrás nem hozható létre, mert a Media Services fiókhoz elérte a szolgáltatási kvótát. A szolgáltatási kvótákkal kapcsolatos további információkért lásd: [kvóták és korlátozások](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 – Nem található
A kérelem a következő okok egyike miatt nem engedélyezett az erőforráson:

* Olyan entitás frissítésére történt kísérlet, amely nem létezik.
* Kísérlet történt olyan entitás törlésére, amely nem létezik.
* Kísérlet történt olyan entitás létrehozására, amely nem létező entitásra hivatkozik.
* Kísérlet történt olyan entitás beolvasására, amely nem létezik.
* Kísérlet történt olyan Storage-fiók megadására, amely nincs társítva a Media Services-fiókhoz.  

## <a name="409-conflict"></a>409 ütközés
A kérelem a következő okok egyike miatt nem engedélyezett:

* Egynél több AssetFile rendelkezik a megadott névvel az eszközön belül.
* Kísérlet történt egy második elsődleges AssetFile létrehozására az eszközön belül.
* Kísérlet történt egy olyan ContentKey létrehozására, amely már használatban van a megadott azonosítóval.
* Kísérlet történt egy olyan lokátor létrehozására, amely már használatban van a megadott azonosítóval.
* Egynél több IngestManifestFile rendelkezik a megadott névvel a IngestManifest belül.
* Kísérlet történt egy második tárolási titkosítási ContentKey a Storage-titkosított eszközhöz való csatolására.
* Kísérlet történt ugyanahhoz a ContentKey az eszközhöz való csatolására.
* Kísérlet történt olyan lokátor létrehozására, amelynek a tárolója hiányzik vagy már nincs társítva az objektumhoz.
* Kísérlet történt egy lokátor létrehozására egy olyan eszközre, amely már rendelkezik 5 lokátor használatban. (Az Azure Storage egy tároló tárolón öt megosztott hozzáférési házirend korlátját kényszeríti ki.)
* Egy eszköz IngestManifestAsset való csatolása nem egyezik meg a szülő IngestManifest által használt Storage-fiókkal.  

## <a name="500-internal-server-error"></a>500 Belső kiszolgálóhiba
A kérelem feldolgozása során Media Services olyan hibába ütközik, amely megakadályozza a feldolgozás folytatását. Ez az alábbi okok bármelyike miatt lehet:

* Nem sikerül létrehozni egy eszközt vagy feladatot, mert az Media Services fiók szolgáltatási kvótájának információi átmenetileg nem érhetők el.
* Az eszköz-vagy IngestManifest blob Storage-tároló létrehozása sikertelen, mert a fiók Storage-fiókjának adatai átmenetileg nem érhetők el.
* Egyéb váratlan hiba.

## <a name="503-service-unavailable"></a>503 A szolgáltatás nem érhető el
A kiszolgáló jelenleg nem tud kéréseket fogadni. Ezt a hibát a szolgáltatás túlzott kérései okozhatják. Media Services szabályozási mechanizmus korlátozza az erőforrás-használatot olyan alkalmazások esetében, amelyek túlzott kérést tesznek elérhetővé a szolgáltatás számára.

> [!NOTE]
> Tekintse meg a hibaüzenetet, és a hibakód karakterláncában részletesebb információkhoz juthat arról, hogy az 503-as hiba miatt megkapta a hibát. Ez a hiba nem mindig jelenti a szabályozást.
> 
> 

A lehetséges állapot leírása:

* "A kiszolgáló foglalt. Az ilyen típusú kérelem korábbi futtatása több mint {0} másodpercet vett igénybe. "
* "A kiszolgáló foglalt. Másodpercenként több mint {0} kérelem is szabályozható. "
* "A kiszolgáló foglalt. {1} másodpercen belül több mint {0} kérelem is szabályozható. "

Ennek a hibának a kezelése érdekében javasoljuk, hogy exponenciális visszaküldési újrapróbálkozási logikát használjon. Ez azt jelenti, hogy a több egymást követő hiba esetén az újrapróbálkozások között fokozatosan várakozik.  További információ: az [átmeneti hibák kezelésére szolgáló alkalmazás blokkja](https://msdn.microsoft.com/library/hh680905.aspx).

> [!NOTE]
> Ha a .net- [hez készült Azure Media Services SDK](https://github.com/Azure/azure-sdk-for-media-services/tree/master)-t használja, a 503-es hiba újrapróbálkozási logikáját az SDK implementálta.  
> 
> 

## <a name="see-also"></a>Lásd még:
[Media Services Felügyeleti hibakódok](https://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

