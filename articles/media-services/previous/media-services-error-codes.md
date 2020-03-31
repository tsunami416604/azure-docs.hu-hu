---
title: Azure Media Services hibakódjai | Microsoft dokumentumok
description: Http-hibakódokat kaphat a szolgáltatástól olyan problémáktól függően, mint például a Media Services által nem támogatott műveletekre lejáró hitelesítési tokenek. Ez a cikk áttekintést nyújt az Azure Media Services v2 API-hibakódokról.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887068"
---
# <a name="azure-media-services-error-codes"></a>Az Azure Media Services hibakódjai
A Microsoft Azure Media Services használata kor http-hibakódokat kaphat a szolgáltatástól, például a Media Services által nem támogatott műveletekre lejáró hitelesítési tokenektől függően. Az alábbi lista a Media Services által visszaadott **HTTP-hibakódokat** és azok lehetséges okait tartalmazza.  

## <a name="400-bad-request"></a>400 rossz kérés
A kérelem érvénytelen információkat tartalmaz, és a következő okok valamelyike miatt utasítják el:

* Nincs megadva egy nem támogatott API-verzió. A legfrissebb verzióról a [Media Services REST API Development beállítása című témakörben](media-services-rest-how-to-use.md)látható.
* A Media Services API-verziója nincs megadva. Az API-verzió megadásáról a [Media Services Operations REST API-referencia című témakörben talál további](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)információt.
  
  > [!NOTE]
  > Ha a .NET vagy a Java SDK-k segítségével csatlakozik a Media Services szolgáltatáshoz, az API-verzió minden alkalommal meg van adva, amikor valamilyen műveletet próbál végrehajtani a Media Services szolgáltatással szemben.
  > 
  > 
* Egy meg határozatlan tulajdonság van megadva. A tulajdonság neve szerepel a hibaüzenetben. Csak azok a tulajdonságok adhatók meg, amelyek egy adott entitás tagjai. Az entitások és azok tulajdonságainak listáját az [Azure Media Services REST API-referencia](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference) tekintse meg.
* Érvénytelen tulajdonságérték van megadva. A tulajdonság neve szerepel a hibaüzenetben. Tekintse meg az előző linken az érvényes tulajdonságtípusokat és azok értékeit.
* Hiányzik egy tulajdonságérték, és kötelező.
* A megadott URL-cím egy része hibás értéket tartalmaz.
* Kísérlet történt egy WriteOnce tulajdonság frissítésére.
* Kísérlet történt egy olyan feladat létrehozására, amely nek nincs megadva vagy nem határozható meg elsődleges AssetFile bemeneti eszköze.
* Kísérlet történt egy SAS-lokátor frissítésére. SAS-lokátorok csak akkor hozhatók létre vagy törölhetők. A streamelési lokátorok frissíthetők. További információ: [Locators](https://docs.microsoft.com/rest/api/media/operations/locator).
* Nem támogatott művelet vagy lekérdezés lett elküldve.

## <a name="401-unauthorized"></a>401 Jogosulatlan
A kérelmet a következő okok valamelyike miatt nem lehetett hitelesíteni (az engedélyezés előtt):

* Hiányzik a hitelesítési fejléc.
* Hibás hitelesítési fejlécérték.
  * A jogkivonat lejárt. 
  * A jogkivonat érvénytelen aláírást tartalmaz.

## <a name="403-forbidden"></a>403 – Tiltott
A kérelem a következő okok valamelyike miatt nem engedélyezett:

* A Media Services-fiók nem található, vagy törölték.
* A Media Services-fiók le van tiltva, és a kérelem típusa nem HTTP GET. A szolgáltatási műveletek 403-as választ is adnak vissza.
* A hitelesítési jogkivonat nem tartalmazza a felhasználó hitelesítő adatait: AccountName és/vagy SubscriptionId. Ezeket az információkat a Media Services-fiók Media Services felhasználói felületbővítményében találja az Azure Management Portalon.
* Az erőforrás nem érhető el.
  
  * Kísérlet történt olyan MediaProcessor használatára, amely nem érhető el a Media Services-fiókhoz.
  * Kísérlet történt a Media Services által meghatározott JobTemplate frissítésére.
  * Kísérlet történt más Media Services-fiók lokátorának felülírására.
  * Kísérlet történt más Media Services-fiók ContentKey-jének felülírására.
* Az erőforrás nem hozható létre a Media Services-fiókhoz elért szolgáltatáskvóta miatt. A szolgáltatási kvótákról a [Kvóták és korlátozások](media-services-quotas-and-limitations.md)című témakörben talál további információt.

## <a name="404-not-found"></a>404 Nem található
A kérelem nem engedélyezett egy erőforráson a következő okok valamelyike miatt:

* Kísérlet történt egy nem létező entitás frissítésére.
* Kísérlet történt egy nem létező entitás törlésére.
* Kísérlet történt egy olyan entitás létrehozására, amely nem létező entitáshoz kapcsolódik.
* Kísérlet történt egy nem létező entitás get-ére.
* Kísérlet történt egy olyan tárfiók megadására, amely nincs társítva a Media Services-fiókhoz.  

## <a name="409-conflict"></a>409 Konfliktus
A kérelem a következő okok valamelyike miatt nem engedélyezett:

* Egynél több AssetFile rendelkezik a megadott névvel az eszközön belül.
* Kísérlet történt egy második elsődleges eszközfájl létrehozására az eszközön belül.
* Kísérlet történt egy ContentKey létrehozására a megadott azonosítóval, amely már használatban van.
* Kísérlet történt egy olyan lokátor létrehozására, amelyen már használt azonosító van.
* Egynél több Betöltésijegyzék-fájl rendelkezik a megadott névvel az IngestManifest-en belül.
* Kísérlet történt egy második tárolótitkosításcontentkey és a tároló-titkosított eszköz csatolására.
* Kísérlet történt arra, hogy ugyanazt a ContentKey kulcsot csatolja az eszközhöz.
* Kísérlet történt egy lokátor létrehozására egy olyan eszközhöz, amelynek tárolótárolója hiányzik, vagy már nincs társítva az eszközhöz.
* Kísérlet történt egy olyan eszköz lokátorának létrehozására, amely már 5 használt lokátorral rendelkezik. (Az Azure Storage öt megosztott hozzáférési szabályzatra vonatkozó korlátot kényszerít ki egy tárolótárolón.)
* Egy eszköz tárfiókának egy IngestManifestAsset-hez való összekapcsolása nem egyezik meg a szülő Betasztió által használt tárfiókkal.  

## <a name="500-internal-server-error"></a>500 Belső kiszolgálóhiba
A kérelem feldolgozása során a Media Services olyan hibát észlel, amely megakadályozza a feldolgozás folytatását. Ez az alábbi okok bármelyike miatt lehet:

* Az eszköz vagy a feladat létrehozása sikertelen, mert a Media Services-fiók szolgáltatáskvóta-adatai átmenetileg nem érhetők el.
* Eszköz vagy A BeadtManifest blobtároló létrehozása sikertelen, mert a fiók tárfiókadatai átmenetileg nem érhetők el.
* Egyéb váratlan hiba.

## <a name="503-service-unavailable"></a>503 A szolgáltatás nem érhető el
A kiszolgáló jelenleg nem tudja fogadni a kéréseket. Ezt a hibát a szolgáltatáshoz intézett túlzott kérés ek okozhatják. A Media Services szabályozási mechanizmusa korlátozza a szolgáltatáshoz túlzott kérést nyújtó alkalmazások erőforrás-használatát.

> [!NOTE]
> A hibaüzenet és a hibakód-karakterlánc segítségével részletesebb információkat kaphat az 503-as hiba okáról. Ez a hiba nem mindig jelent szabályozást.
> 
> 

A lehetséges állapotleírások a következők:

* "A kiszolgáló foglalt. Az ilyen típusú kérelmek korábbi {0} futtatásai több mint másodpercet vettek igénybe."
* "A kiszolgáló foglalt. A {0} másodpercenkénti kérésnél több is szabályozható."
* "A kiszolgáló foglalt. A {0} másodperceken {1} belüli kéréseknél több is szabályozható."

A hiba kezeléséhez azt javasoljuk, hogy exponenciális vissza-vissza újrapróbálkozási logikát használjon. Ez azt jelenti, hogy fokozatosan hosszabb várakozást kell használni az egymást követő hibaválaszok újrapróbálkozásai között.  További információ: [Tranient Fault Handling Application Block](https://msdn.microsoft.com/library/hh680905.aspx).

> [!NOTE]
> Ha az [Azure Media Services SDK-t használja a .Net számára,](https://github.com/Azure/azure-sdk-for-media-services/tree/master)az SDK megvalósította az 503-as hiba újrapróbálkozási logikáját.  
> 
> 

## <a name="see-also"></a>Lásd még:
[Media Services Management hibakódok](https://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>További lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

