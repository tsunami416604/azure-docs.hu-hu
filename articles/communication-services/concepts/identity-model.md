---
title: Identitási modell
titleSuffix: An Azure Communication Services concept
description: Az identitások és a hozzáférési jogkivonatok megismerése
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 10/26/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 336e708334778e107331f5f393476c4b3dbb98a3
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507484"
---
# <a name="identity-model"></a>Identitási modell

Az Azure kommunikációs szolgáltatás az Identity agnosztikus szolgáltatás. Ennek a kialakításnak több előnye is van:
- Meglévő identitások újbóli felhasználása az Identity Management rendszerből
- Integrációs forgatókönyvek rugalmassága
- Saját identitások megtartása az Azure kommunikációs szolgáltatásokban

A meglévő adatoknak a rendszerbe való másolása helyett az üzleti esetekre jellemző leképezési kapcsolatot kell fenntartania. Például az identitások leképezése 1:1, 1: N, N:1, N:M. A külső azonosítók (például a telefonszámok, a felhasználók, az eszközök, az alkalmazások és a GUID) nem használhatók Azure-beli kommunikációs identitásként. Az Azure kommunikációs szolgáltatás identitásához generált hozzáférési tokenek olyan primitívek elérésére szolgálnak, mint a csevegés vagy a hívás. 

## <a name="identity"></a>Identitás

Az identitások az Azure kommunikációs szolgáltatás felügyeleti könyvtárával jönnek létre. Az identitás azonosítóként szolgál a beszélgetésekben, és a hozzáférési jogkivonatok létrehozásához használatos. Ugyanaz az identitás több egyidejű munkamenetben is részt vehet több eszközön. Az identitás egyszerre több aktív hozzáférési jogkivonattal rendelkezhet. Az identitás, az erőforrás vagy az előfizetés törlése az összes hozzáférési jogkivonatának érvénytelenítését és az identitáshoz tárolt összes adatok törlését okozza. A törölt identitás nem tud új hozzáférési jogkivonatokat kiadni, és nem fér hozzá a korábban tárolt adatokhoz (például csevegési üzenetekhez). 

Az Ön által használt identitások száma és a primitívek használata miatt nem számítunk fel díjat. Az identitások számát nem kell korlátozni, hogyan képezhető le az alkalmazás identitásai az Azure kommunikációs szolgáltatások identitására. A feltérképezés szabadsága az adatvédelem feltételeinek felelőssége. Ha az alkalmazás felhasználóját törölni szeretné a rendszerből, törölnie kell az adott felhasználóhoz társított összes identitást.

Az Azure kommunikációs szolgáltatások nem biztosítanak speciális identitást a névtelen felhasználók számára. Nem tartja meg a felhasználók és az identitások közötti leképezést, nem érti, hogy az identitás névtelen-e. Megtervezheti a koncepciót, hogy illeszkedjen a követelményeihez. Javasoljuk, hogy hozzon létre új identitást az egyes alkalmazások névtelen felhasználói számára. Az érvényes hozzáférési token birtokában bárki hozzáférhet az identitás nem törölt tartalmához. Például a felhasználó által küldött csevegési üzenetek. A hozzáférés csak hatókörre korlátozódik, amely a hozzáférési jogkivonat részét képezi. A hatókörökkel kapcsolatos további részletek a *hozzáférési jogkivonat* szakaszban találhatók.

### <a name="mapping-of-identities"></a>Identitások megfeleltetése

Az Azure kommunikációs szolgáltatás nem replikálja az IMS funkcióit. Nem biztosít lehetőséget az ügyfelek számára az ügyfél-specifikus identitások használatára. Például telefonszám vagy e-mail-cím. Ehelyett egyedi azonosítókat biztosít, amelyeket hozzárendelhet az alkalmazás identitásához. Az Azure kommunikációs szolgáltatások semmilyen információt nem tárolnak, amelyek a felhasználók valódi identitását jelezhetik.

A Duplikálás helyett javasoljuk, hogy tervezze meg, hogyan lesz leképezve az identitás-tartomány felhasználói az Azure kommunikációs szolgáltatás identitásai. Követheti a 1:1, 1: N, N:1 vagy M:N. típusú mintákat Eldöntheti, hogy az egyes felhasználók egyetlen identitásra vagy több identitásra vannak-e leképezve. Az új identitás létrehozásakor javasoljuk, hogy tárolja az identitás hozzárendelését az alkalmazás felhasználója vagy felhasználói számára. Mivel az identitásoknak a primitívek használatához hozzáférési jogkivonatokra van szükségük, az identitásnak ismertnek kell lennie az alkalmazás felhasználója vagy felhasználói számára.

Ha a felhasználók tárolására a viszonyítási adatbázist használja, a megvalósítás eltérő lehet a leképezési forgatókönyv alapján. Az 1:1-as vagy N:1 leképezést használó forgatókönyvek esetében felvehet egy *CommunicationServicesId* oszlopot a táblába az Azure kommunikációs szolgáltatások identitásának tárolásához. Az 1: N vagy N:M kapcsolattal rendelkező forgatókönyvekben érdemes lehet külön táblát létrehozni a relációs adatbázisban.

## <a name="access-token"></a>Hozzáférési jogkivonat

A hozzáférési jogkivonat egy JWT token, amely az Azure kommunikációs szolgáltatásbeli primitívek elérésére használható. A kiállított hozzáférési jogkivonat integritási védelemmel rendelkezik, a kibocsátás után a jogcímek nem módosíthatók. Ez azt eredményezi, hogy a tulajdonságok, például az identitás, a lejárat vagy a hatókörök manuális módosítása a hozzáférési tokent érvénytelenvé teszi. A nem hitelesített tokenekkel rendelkező primitívek használata megtagadja a hozzáférést a primitívhez. 

A hozzáférési jogkivonat tulajdonságai a következők: *identitás, lejárat* és *hatókörök*. A hozzáférési jogkivonat mindig 24 órán át érvényes. Ezt követően a hozzáférési jogkivonat érvénytelenné válik, és nem használható a primitívek eléréséhez. Az identitásnak úgy kell lennie, hogyan kérhet új hozzáférési jogkivonatot a kiszolgálóoldali szolgáltatástól. A paraméter *hatóköre* olyan primitívek nem üres készletét határozza meg, amely használható. Az Azure kommunikációs szolgáltatások a következő hatóköröket támogatják a hozzáférési jogkivonatok esetén:

|Név|Leírás|
|---|---|
|Csevegés|  Lehetőséget biztosít csevegésben való részvételre|
|VoIP|  Identitások és telefonszámok meghívásának lehetősége|


Ha vissza szeretné vonni a hozzáférési jogkivonatot a lejárat előtt, az Azure kommunikációs szolgáltatás felügyeleti függvénytárát használhatja. A jogkivonat visszavonása nem azonnali, és a propagálás akár 15 percet is igénybe vehet. Az identitás, az erőforrás vagy az előfizetés eltávolítása az összes hozzáférési jogkivonat visszavonását eredményezi. Ha el szeretné távolítani egy felhasználó hozzáférését bizonyos funkciókhoz, vonja vissza az összes hozzáférési jogkivonatot. Ezután adjon ki egy új hozzáférési jogkivonatot, amely szűkebb hatókörrel rendelkezik.
Az Azure kommunikációs szolgáltatás elérési kulcsainak rotációja a korábbi hozzáférési kulccsal létrehozott összes aktív hozzáférési jogkivonat visszavonását eredményezi. Az összes identitás elveszíti az Azure kommunikációs szolgáltatáshoz való hozzáférést, és új hozzáférési jogkivonatok kibocsátására van szükség. 

Javasoljuk, hogy a hozzáférési jogkivonatokat a kiszolgálóoldali szolgáltatásban, nem pedig az ügyfél alkalmazásában adja meg. Ennek az az oka, hogy a kiadáshoz hozzáférési kulcsra vagy felügyelt identitásra van szükség. Biztonsági okokból nem ajánlott a hozzáférési kulcsok megosztása az ügyfél alkalmazásával. Az ügyfélalkalmazás olyan megbízható szolgáltatási végpontot használjon, amely képes hitelesíteni az ügyfeleit, és kiadni a hozzáférési jogkivonatot a nevükben. Az architektúrával kapcsolatos további részletek [itt](./client-and-server-architecture.md)találhatók.

Ha a hozzáférési jogkivonatokat egy tárolóba gyorsítótárazza, javasoljuk, hogy használja a titkosítást. A hozzáférési jogkivonat bizalmas adatok, és rosszindulatú tevékenységekhez használható, ha az nem védett. A hozzáférési jogkivonat birtokában inicializálhatja az SDK-t, és hozzáférhet az API-hoz. Az elérhető API csak hatókörön alapul, és a hozzáférési jogkivonat rendelkezik. Javasoljuk, hogy csak a hatókörökkel rendelkező hozzáférési tokenek legyenek kibocsátva, amelyek szükségesek.