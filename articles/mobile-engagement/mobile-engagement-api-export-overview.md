---
title: "Mobile Engagement exportálási API áttekintése"
description: "Alapvető tudnivalók a felhasználói eszközök kihasználja azt a saját eszközök által létrehozott nyers adatok exportálása"
services: mobile-engagement
documentationcenter: mobile
author: kpiteira
manager: erikre
editor: 
ms.assetid: 9380d47b-d7fa-4d4c-888f-97e6482196bb
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 04/26/2016
ms.author: kapiteir
ms.openlocfilehash: 346e0e480ff84ee849f135a7605d27df9e32f966
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="mobile-engagement-export-api-overview"></a>Mobile Engagement exportálási API áttekintése
## <a name="introduction"></a>Bevezetés
Ebből a dokumentumból megtudhatja, a nyers adatok a felhasználói eszközök kihasználja azt a saját eszközök által létrehozott exportálása alapjait.

## <a name="pre-requisites"></a>Előfeltételek
A nyers adatok exportálása a Mobile Engagement van szükség:

* API authentication telepítő fogja tudni használni az API-k (lásd: [hitelesítési manuális telepítési módra](mobile-engagement-api-authentication-manual.md)),
* Vagy a REST API-k vagy a [.net SDK](mobile-engagement-dotnet-sdk-service-api.md),
* Egy Azure Storage-fiókot.

> [!NOTE]
> Szintén javasoljuk a kiváló [Microsoft Azure Tártallózó](http://storageexplorer.com/), legalább a fejlesztési fázisban, mert egy könnyen használható felhasználói Felületet biztosít az Azure Storage használják.
> 
> 

## <a name="what-can-be-exported"></a>Mi lehet exportálni?
A Mobile Engagement lehetővé teszi, hogy a felhasználók számos különböző típusú adatok gyűjtéséhez és így ezek különböző adattípusokkal igazodó exportálási többféle típusú.
Exportálás 2 alapvető típusa van:

* Pillanatkép: használt általában az exportálandó adatokat, amelyek állapotát jeleníti meg egy és, amelynek a Mobile Engagement nincs előzményeit. Ez magában foglalja-jogkivonatok és a leküldéses kampány visszajelzések címkék (app-info), például. Exportálás az ilyen típusú következtében nem kapcsolódnak egy dátumot.
* korábbi: az Exportálás típusú az adatokat, például az események vagy a tevékenységek időbeli például összesít szolgál.

Az alábbi táblázat körűen minden lehetséges export ismerteti:

| Exportálás típusa | Adattípus | Leírás |
| --- | --- | --- |
| Pillanatkép |Leküldéses értesítések |Állít elő, leküldéses kampányokra visszajelzések deviceid/userid / alapon exportálása |
| Pillanatkép |Címke |Állít elő, az egyes eszközökre társított címkék (app-info) exportálása |
| Pillanatkép |Eszköz |Állít elő, például a technicals (modell, területi beállítás, az időzónát,...), a címkéket, először látott eszközökkel kapcsolatos adatok exportálása... |
| Pillanatkép |Token |Létrehozza a érvényes jogkivonatok exportálása |
| Korábbi |Tevékenység |Létrehoz egy adott időszakban az egyes eszközök az összes tevékenység exportálása |
| Korábbi |Esemény |Létrehoz egy adott időszakban az egyes eszközök az összes tevékenység exportálása |
| Korábbi |Feladat |Létrehoz egy adott időszakban az egyes eszközök a feladatok exportálása |
| Korábbi |Hiba |Létrehoz egy adott időszakban az egyes eszközök a hibákat exportálás |

## <a name="how-does-it-work"></a>Hogyan működik?
Export hosszúak futó feladatok is eredményezhet nagy fájlok. Éppen ezért azok nem hívható meg azonnal vissza egy fájlt tölthet le.
Ahhoz, hogy az adatok exportálása a Mobile Engagement, meg kell hoznia egy **exportálása feladat** megadott általában API-n keresztül:

* A típusú (pillanatképes vagy korábbi), exportálás
* Az adattípus
* A **Azure-tárolót** (beleértve az írási hozzáférés egy érvényes SAS) ahová kerülnek az Exportálás eredményét.
* pl. példa tároló URL-paramétert lenne https://[StorageAccountName].blob.core.windows.net/[ContainerName]? [SASWritePermissionsToken]  

Itt látható a valós életben példa. https://testazmeexport.BLOB.Core.Windows.NET/test1234azme?SV=2015-12-11&SS=b&SRT=SCO&SP=rwdlac&se=2016-12-17T04:59:26Z & st = 2016-12-16T20:59:26Z & spr = https & sig = KRF3aVWjp2NEJDzjlmoplmu0M9HHlLdkBWRPAFmw90Q % 3D

Vegye figyelembe, hogy a feladat indítható néhány percig is eltarthat, és majd előfordulhat, hogy fut, nagyon kicsi alkalmazások néhány másodperc több óráig alkalmazásokkal rendelkező felhasználók vagy a tevékenység.

A feladat létrehozása után is lehet ellenőrizze annak állapotát, hogy még mindig fut, vagy befejeződött.

Ha a feladat sikeres van, az eredményül kapott adatfájl érhető el a megadott tároló.

