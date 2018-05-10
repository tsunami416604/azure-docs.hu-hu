---
title: Az Azure Media Services hibakódok |} Microsoft Docs
description: A témakör áttekintést nyújt az Azure Media Services hibakód.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d3a62a64-7608-4b17-8667-479b26ba0d6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: juliako
ms.openlocfilehash: 8a374393a6a5b3d563a441654b7b5df8a510f304
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="azure-media-services-error-codes"></a>Az Azure Media Services-hibakódok
Microsoft Azure Media Services használata esetén a HTTP-hibakódokat attól függően, például a hitelesítési tokenek lejár a végrehajtandó műveleteket nem támogatja a Media Services szolgáltatásból is megjelenhet. Az alábbiakban olvashat egy listát a **HTTP hibakódok** , amely által visszaadott Media Services és a lehetséges okok a számukra.  

## <a name="400-bad-request"></a>400 Hibás kérés
A kérelem érvénytelen adatokat tartalmaz, és ez visszautasításra kerül, a következő okok egyikéből adódóan:

* Egy nem támogatott API-verzió van megadva. A legújabb verzióját, lásd: [a Media Services REST API fejlesztési telepítő](media-services-rest-how-to-use.md).
* A Media Services API-verzió nincs megadva. Adja meg az API-verzió módjáról további információkért lásd: [Media Services Operations REST API-referencia](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).
  
  > [!NOTE]
  > Ha a .NET vagy a Java SDK-k segítségével végzi a Media Services kapcsolódni, az API-verzió van megadva, ha próbálja, illetve a Media Services elleni valamilyen művelet végrehajtása.
  > 
  > 
* Egy nem definiált tulajdonság lett megadva. A tulajdonság értéke a hibaüzenet. Csak azokat a tulajdonságokat, amelyek egy adott entitás tagjai adható meg. Lásd: [Azure Media Services REST API-referencia](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference) entitásokat és a tulajdonságok listája.
* Az érvénytelen tulajdonság-érték lett megadva. A tulajdonság értéke a hibaüzenet. Érvényes Tulajdonságtípusok és azok értékeit az előző hivatkozást követve olvashatja el.
* Tulajdonság értéke nincs megadva, pedig szükséges.
* A megadott URL-cím része helytelen értéket tartalmaz.
* Kísérlet történt egy WriteOnce tulajdonság.
* Kísérlet történt hozzon létre egy feladatot, amely rendelkezik egy bemeneti objektum egy elsődleges AssetFile, amely nincs megadva, vagy nem határozható meg.
* Kísérlet történt egy SAS-kereső frissítéséhez. SAS-keresők csak létrehozott vagy törölt. Streamelési Locator lehet frissíteni. További információkért lásd: [keresők](https://docs.microsoft.com/rest/api/media/operations/locator).
* Egy nem támogatott műveletnek vagy a lekérdezés el lett küldve.

## <a name="401-unauthorized"></a>401 nem engedélyezett
A kérelem nem sikerült hitelesíteni (előtt is engedélyezhető) a következő okok egyikéből adódóan:

* Hiányzó hitelesítési fejléc.
* Hibás hitelesítési fejléc értéke.
  * A jogkivonat lejárt. 
  * A token érvénytelen aláírást tartalmaz.

## <a name="403-forbidden"></a>403 Tiltott
A kérelem nem engedélyezett a következő okok egyikéből adódóan:

* A Media Services-fiók nem található, vagy már törölték.
* A Media Services-fiók le van tiltva, és a kérés típusa nincs HTTP GET. Szolgáltatási műveleteket, valamint a 403-as választ ad vissza.
* A hitelesítési jogkivonat nem tartalmazza a felhasználói hitelesítő adatok: AccountName és/vagy előfizetés-azonosító. Ezek az információk a Media Services felhasználói felületén bővítményben a Media Services-fiók az Azure felügyeleti portálon található.
* Az erőforrás nem érhető el.
  
  * Kísérlet történt egy MediaProcessor, amely nem érhető el a Media Services-fiók használandó.
  * Kísérlet történt egy Media Services által meghatározott JobTemplate frissítéséhez.
  * Kísérlet történt néhány más Media Services-fiókhoz tartozó lokátor felülírásához.
  * Kísérlet történt néhány más Media Services-fiókhoz tartozó ContentKey felülírja.
* Az erőforrás nem lehetett létrehozni, mert elérte a Media Services-fiók szolgáltatás kvóta. A szolgáltatás kvótákkal kapcsolatos további információkért lásd: [kvóták és korlátozások](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 – Nem található
A kérelem nem engedélyezett a következő okok egyikéből adódóan függ:

* Kísérlet történt olyan entitás, amely nem létezik frissítéséhez.
* Kísérlet történt egy entitás nem létezik.
* Kísérlet történt olyan entitás, amely nem létezik mutató entitás létrehozása.
* Kísérlet történt olyan entitás, amely nem létezik eléréséhez.
* Kísérlet történt a tárfiók, amely nincs társítva a Media Services-fiók megadásához.  

## <a name="409-conflict"></a>409 ütközés
A kérelem nem engedélyezett a következő okok egyikéből adódóan:

* Egynél több AssetFile belül az eszköz a megadott névvel rendelkezik.
* Kísérlet történt egy második elsődleges AssetFile belül az eszköz létrehozásához.
* Kísérlet történt egy ContentKey létrehozása a megadott azonosítóval már használatban van.
* Kísérlet történt egy kereső létrehozása a megadott azonosítóval már használatban van.
* Egynél több IngestManifestFile rendelkezik a megadott név a IngestManifest belül.
* Kísérlet történt egy második tárolás titkosítása ContentKey összekapcsolása a tárolás titkosítása eszköz.
* Kísérlet történt a azonos ContentKey csatolása az eszközhöz.
* Kísérlet történt egy eszközhöz, amelynek a tároló hiányzik, vagy már nem tartozik az eszköz egy kereső létrehozása.
* Kísérlet történt egy eszközhöz, melynek 5 keresők használatban van egy kereső létrehozása. (Az azure Storage kikényszeríti egy tároló öt megosztott elérési házirendek korlátot.)
* Egy eszköz storage-fiók összekapcsolása egy IngestManifestAsset nem ugyanaz, mint a szülő IngestManifest által használt tárfiók.  

## <a name="500-internal-server-error"></a>500 Belső kiszolgálóhiba
A kérelem feldolgozása során a Media Services során néhány, amely megakadályozza a folyamatos feldolgozási hiba lép fel. Ez az alábbi okok bármelyike miatt lehet:

* Egy eszköz vagy a feladat létrehozása sikertelen, mert a Media Services-fiók szolgáltatás kvótaadatok átmenetileg nem érhető el.
* Egy eszköz vagy IngestManifest blob storage tárolók létrehozása sikertelen, mert a fiók tárfiókadatok átmenetileg nem érhető el.
* Váratlan hiba.

## <a name="503-service-unavailable"></a>503 A szolgáltatás nem érhető el
A kiszolgáló egy jelenleg nem fogadhatók kérések. Ez a hiba előfordulhat, hogy az okozza, hogy túl sok kéréseket. Media Services mechanizmus szabályozás korlátozza az alkalmazások, amelyek a szolgáltatás túl sok kérelem erőforrás-felhasználása.

> [!NOTE]
> Ellenőrizze a hibaüzenetről és a hiba kódja karakterlánc portáltól a 503-as hiba okát kapcsolatos részletes információkért. Ez a hiba nem mindig jelenti sávszélesség-szabályozás.
> 
> 

A leírások lehetséges a következők:

* "A kiszolgáló túlterhelt. Előző fut. az ilyen típusú kérelem tartott több mint {0} másodperc. "
* "A kiszolgáló túlterhelt. Több mint {0} kérelmek / másodperc is szabályozva. "
* "A kiszolgáló túlterhelt. Több mint {0} belül kérelmek {1} másodperc is szabályozva. "

A hiba kezelését, azt javasoljuk, exponenciális vissza az indító újrapróbálkozási logika. Ez azt jelenti, hogy egymást követő hibaválaszok próbálkozások közötti fokozatosan hosszabb ideig vár használatával.  További információkért lásd: [átmeneti hiba kezelési alkalmazás blokk](https://msdn.microsoft.com/library/hh680905.aspx).

> [!NOTE]
> Ha használ [Azure Media Services SDK for .net](https://github.com/Azure/azure-sdk-for-media-services/tree/master), az újrapróbálkozási logika az 503-as hiba az SDK-ban is implementálva lett.  
> 
> 

## <a name="see-also"></a>Lásd még:
[Media Services-felügyeleti hibakódok](http://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>További lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

