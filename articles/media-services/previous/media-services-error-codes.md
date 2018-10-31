---
title: Az Azure Media Services-hibakódok |} A Microsoft Docs
description: A témakör áttekintést nyújt az Azure Media Services hibakód.
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
ms.date: 10/29/2018
ms.author: juliako
ms.openlocfilehash: 43e2b2b7f398e8adce0760678c6f0a5208c24c34
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50240528"
---
# <a name="azure-media-services-error-codes"></a>Az Azure Media Services-hibakódok
A Microsoft Azure Media Services használata esetén a HTTP-hibakódok a szolgáltatástól függően például a hitelesítési tokenek lejár, a Media Services szolgáltatásban nem támogatott műveleteket is megjelenhet. A következő lista **HTTP-hibakódok** , amely által visszaadott Media Services és a lehetséges okok a számukra.  

## <a name="400-bad-request"></a>400 Hibás kérés
A kérés érvénytelen adatokat tartalmaz, és az alábbi okok egyike miatt elutasítva:

* Nem támogatott API-verzió van megadva. A legfrissebb verzióját, lásd: [beállítása a Media Services REST API-k fejlesztését](media-services-rest-how-to-use.md).
* A Media Services API-verzió nincs megadva. Az API-verzió megadása a további információkért lásd: [Media Services Operations REST API-referencia](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).
  
  > [!NOTE]
  > Ha a Media Services-csatlakozáshoz használ a .NET vagy Java SDK-k, az API-verziót meg van adva az Ön számára, amikor próbálja ki, és a Media Services elleni valamilyen művelet végrehajtása.
  > 
  > 
* Egy nem definiált tulajdonság lett megadva. A tulajdonság neve szerepel a hibaüzenetet. Csak azokat a tulajdonságokat, amelyek tagjai egy adott entitás adható meg. Lásd: [Azure Media Services REST API-referencia](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference) entitásokhoz és tulajdonságaikhoz listáját.
* Egy érvénytelen érték lett megadva. A tulajdonság neve szerepel a hibaüzenetet. Tekintse meg a fenti hivatkozáson érvényes tulajdonságtípus és azok értékeit.
* Egy tulajdonság értéke nincs megadva, nincs szükség.
* A megadott URL-Címének részét hibás értéket tartalmaz.
* Kísérlet történt egy WriteOnce tulajdonság frissítése.
* Kísérlet történt hozzon létre egy feladatot, amely egy elsődleges AssetFile, amely nincs megadva, vagy nem sikerült meghatározni az egy bemeneti objektuma rendelkezik.
* Kísérlet történt egy SAS-kereső frissíteni. SAS-lokátorok csak létrehozása vagy törlése. A streamelési lokátorok frissíthetők. További információkért lásd: [keresők](https://docs.microsoft.com/rest/api/media/operations/locator).
* Egy nem támogatott művelet vagy a lekérdezés el lett küldve.

## <a name="401-unauthorized"></a>401-es nem engedélyezett
A kérelem nem sikerült hitelesíteni (előtt is engedélyezhető) a következő okok egyike miatt:

* Hitelesítési fejléc hiányzik.
* Hibás hitelesítés fejléc értéke.
  * A jogkivonat lejárt. 
  * A jogkivonat aláírása érvénytelen tartalmaz.

## <a name="403-forbidden"></a>403 Tiltott
A kérelem nem engedélyezett a következő okok egyike miatt:

* A Media Services-fiók nem található, vagy törölve lett.
* A Media Services-fiók le van tiltva, a kérelemtípus nem HTTP GET. Szolgáltatási műveletek 403-as, valamint választ ad vissza.
* A hitelesítési jogkivonat nem tartalmazza a felhasználói hitelesítő adatok: AccountName és/vagy előfizetés-azonosító. A Media Services-fiókba az Azure felügyeleti portálon, ezt az információt is található a Media Services felhasználói felületi bővítmény.
* Az erőforrás nem érhető el.
  
  * Kísérlet történt egy MediaProcessor, amely nem érhető el a Media Services-fiók használata.
  * Kísérlet történt egy Media Services által meghatározott JobTemplate frissíteni.
  * Kísérlet történt néhány más Media Services-fiók kereső felülírásához.
  * Kísérlet történt néhány más Media Services-fiók ContentKey felülírásához.
* Egy szolgáltatás kvóta, amely a program elérte a Media Services-fiók miatt nem lehet létrehozni az erőforrást. A szolgáltatási kvótákkal kapcsolatos további információkért lásd: [kvóták és korlátozások](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 – Nem található
A kérelem nem engedélyezett egy erőforráson, az alábbi okok egyike miatt:

* Kísérlet történt frissíthető entitás, amely nem létezik.
* Kísérlet történt egy entitás, amely nem létezik.
* Kísérlet történt olyan entitás, amely nem létezik mutató entitást létrehozni.
* Kísérlet történt az első olyan entitás, amely nem létezik.
* Kísérlet történt, amely nem a Media Services-fiókhoz társított tárfiókot adjon meg.  

## <a name="409-conflict"></a>409 ütközés
A kérelem nem engedélyezett a következő okok egyike miatt:

* Egynél több AssetFile belül az eszköz a megadott névvel rendelkezik.
* Kísérlet történt egy második elsődleges AssetFile belül az eszköz létrehozásához.
* Kísérlet történt egy ContentKey létrehozása a megadott azonosítóval már használatban van.
* Kísérlet történt már használatban van a megadott azonosítóval rendelkező Lokátorok létrehozásához.
* Egynél több IngestManifestFile rendelkezik a megadott név a IngestManifest belül.
* Kísérlet történt egy második tártitkosítás ContentKey összekapcsolása a storage-titkosítású eszköz.
* Kísérlet történt az eszköz az azonos ContentKey összekapcsolása.
* Kísérlet történt egy objektumba történik, amelynek a tároló hiányzik, vagy már nem az eszköz társítva lokátorok létrehozásához.
* Kísérlet történt egy objektumba történik, amely már rendelkezik használatban lévő 5 lokátorok lokátorok létrehozásához. (Az azure Storage kényszerít öt megosztott elérési házirendeket egy storage-tárolót a korlátot.)
* Egy eszköz a storage-fiók összekapcsolása egy IngestManifestAsset nem ugyanaz, mint a szülő IngestManifest által használt.  

## <a name="500-internal-server-error"></a>500 Belső kiszolgálóhiba
A kérelem feldolgozása során a Media Services hibába valamilyen hiba, amely megakadályozza a folyamatos feldolgozását. Ez az alábbi okok bármelyike miatt lehet:

* Egy eszköz vagy feladat létrehozása meghiúsul, mert a Media Services-fiók szolgáltatás kvótákra vonatkozó információk átmenetileg nem érhető el.
* Egy eszköz vagy IngestManifest blob storage-tároló létrehozása meghiúsul, mert a tárfiók-információ a fiók ideiglenesen nem érhető el.
* Más váratlan hiba történt.

## <a name="503-service-unavailable"></a>503 A szolgáltatás nem érhető el
A kiszolgáló jelenleg nem küldött fogadási kérelmekre küld. Ez a hiba oka túl sok a szolgáltatáshoz érkező kérések. A Media Services-szabályozás mechanizmus az alkalmazásokat, amelyek a szolgáltatás túl sok kérést az erőforrás-használati korlátozza.

> [!NOTE]
> Ellenőrizze az okkal kapcsolatos részletes információkért az 503-as hibát kapott hibaüzenet és hibakód karakterláncát. Ez a hiba nem mindig jelenti szabályozás.
> 
> 

Leírások lehetséges állapota van:

* "A kiszolgáló elfoglalt. Az ilyen típusú kérés a korábbi közvetítésekből tartott több mint {0} másodperc. "
* "A kiszolgáló elfoglalt. Több mint {0} másodpercenként küldött kérelmek szabályozva is. "
* "A kiszolgáló elfoglalt. Több mint {0} belül kérelmek {1} másodperc szabályozható. "

Ez a hiba kezelésére, javasoljuk, exponenciális visszatartási újrapróbálkozási logika használata. Ez azt jelenti, hogy használatával fokozatosan hosszabb ideig vár a válaszok egymást követő hiba újrapróbálkozások között.  További információkért lásd: [átmeneti-kezelési Alkalmazásblokk](https://msdn.microsoft.com/library/hh680905.aspx).

> [!NOTE]
> Ha használ [Azure Media Services SDK for .net](https://github.com/Azure/azure-sdk-for-media-services/tree/master), az újrapróbálkozási logika az 503-as hibát az SDK által implementálva lett.  
> 
> 

## <a name="see-also"></a>Lásd még:
[A Media Services felügyeleti hibakódok](http://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>További lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

