---
title: Ajánlott eljárások – Azure App Service
description: Ismerkedjen meg a Azure App Service ajánlott eljárásaival és hibaelhárításával.
services: app-service
documentationcenter: ''
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2016
ms.author: dariac
ms.custom: seodec18
ms.openlocfilehash: b4c7524415865f7db5d4514c14f8e45030620330
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636675"
---
# <a name="best-practices-for-azure-app-service"></a>Azure App Service – ajánlott eljárások
Ez a cikk a [Azure app Service](https://go.microsoft.com/fwlink/?LinkId=529714)használatának ajánlott eljárásait foglalja össze. 

## <a name="colocation"></a>Bérelt kiszolgálói
Ha olyan Azure-erőforrások, mint például egy webalkalmazás és egy adatbázis különböző régiókban találhatók, a következő hatásokkal járhat:

* Megnövekedett késés az erőforrások közötti kommunikációban
* Az [Azure díjszabási oldalán](https://azure.microsoft.com/pricing/details/data-transfers)feljegyzett, a kimenő adatforgalomra vonatkozó pénzügyi költségek.

Az azonos régióban található közös elhelyezés olyan Azure-erőforrások esetében ajánlott, mint például a webalkalmazások és a tartalom vagy az adatokat tároló adatbázis-vagy Storage-fiók. Erőforrások létrehozásakor ügyeljen arra, hogy ugyanabban az Azure-régióban legyenek, hacsak nincs konkrét üzleti vagy tervezési oka, hogy azok ne legyenek. App Service alkalmazást az adatbázissal megegyező régióba helyezheti át, ha a [app Service klónozási funkció](app-service-web-app-cloning.md) jelenleg elérhető a prémium szintű app Service csomag alkalmazásaiban.   

## <a name="memoryresources"></a>Ha az alkalmazások a vártnál több memóriát használnak
Ha észreveszi, hogy egy alkalmazás a vártnál több memóriát használ fel, ahogy azt a figyelési vagy szolgáltatási javaslatok alapján jelezte, vegye figyelembe a [app Service automatikus gyógyulási funkciót](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Az automatikus javító funkció egyik beállítása a memória küszöbértékén alapuló egyéni műveleteket veszi igénybe. A műveletek az e-mail-értesítésekben a spektrumra terjednek ki, hogy a feldolgozói folyamat újrahasznosítása révén a rendszer a memórián keresztül, a helyszínen történő csökkentéssel Az automatikus javítás a web. config fájlon keresztül és egy felhasználóbarát felhasználói felületen keresztül konfigurálható a következő blogbejegyzésben leírtak szerint: [app Service támogatási hely kiterjesztése](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>Ha az alkalmazások a vártnál több PROCESSZORt használnak
Ha észreveszi, hogy egy alkalmazás a vártnál több CPU-t használ, vagy a figyelési vagy szolgáltatási javaslatok alapján megismétli a CPU-tüskéket, vegye fontolóra a App Service terv vertikális felskálázását vagy horizontális felskálázását. Ha az alkalmazás állapot-nyilvántartó, a vertikális felskálázás az egyetlen lehetőség, míg ha az alkalmazás állapota állapot nélküli, a horizontális felskálázás nagyobb rugalmasságot és nagyobb méretezhetőséget biztosít. 

Az "állapot nélküli" és "állapot nélküli" alkalmazásokkal kapcsolatos további információkért tekintse meg ezt a videót: [Méretezhető, végpontok közötti többrétegű alkalmazás tervezése Azure app Serviceon](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). A App Service skálázással és az automatikus skálázási lehetőségekkel kapcsolatos további információkért [](manage-scale-up.md)lásd: webalkalmazás skálázása Azure app Serviceban.  

## <a name="socketresources"></a>A szoftvercsatorna erőforrásainak kimerülése esetén
A kimenő TCP-kapcsolatok kimerítésének gyakori oka az ügyféloldali kódtárak használata, amelyek nem használhatók a TCP-kapcsolatok újrafelhasználására, vagy ha egy magasabb szintű protokoll, például a HTTP-Keep-Alive nincs használatban. Tekintse át a App Service-csomag alkalmazásai által hivatkozott könyvtárak dokumentációját, hogy a rendszer konfigurálja vagy hozzáférjen a kódban a kimenő kapcsolatok hatékony újrafelhasználásához. A kapcsolatok kiszivárgásának elkerülése érdekében kövesse a könyvtár dokumentációját is a megfelelő létrehozáshoz és felszabadításhoz, illetve a karbantartáshoz. Amíg az ügyfél-kódtárak vizsgálata folyamatban van, a hatás a több példányra történő horizontális felskálázással enyhíthető lehet.

### <a name="nodejs-and-outgoing-http-requests"></a>Node. js és kimenő HTTP-kérelmek
A Node. js és számos kimenő HTTP-kérelem használata esetén fontos a HTTP-Keep-Alive kezelése. A [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` csomag használatával egyszerűbbé teheti a kódot.

Mindig kezelje `http` a választ, még akkor is, ha nem tesz semmit a kezelőben. Ha nem megfelelően kezeli a választ, az alkalmazás elakad, mert nem érhető el több szoftvercsatorna.

Például a vagy `http` `https` a csomag használata esetén:

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Ha App Service Linuxon fut egy több magot tartalmazó gépen, akkor egy másik ajánlott eljárás az, hogy a PM2 használatával több Node. js folyamat induljon el az alkalmazás végrehajtásához. Ezt megteheti egy indítási parancs megadásával a tárolóban.

Például négy példány elindításához:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="appbackup"></a>Ha az alkalmazás biztonsági mentése meghiúsul
A két leggyakoribb ok, amiért az alkalmazás biztonsági mentése meghiúsul: érvénytelen tárolási beállítások és érvénytelen adatbázis-konfiguráció. Ezek a hibák általában akkor fordulnak elő, ha módosulnak a tárolási vagy adatbázis-erőforrások, illetve az ilyen erőforrások elérésének módosításai (például a biztonsági mentési beállításokban kiválasztott adatbázishoz tartozó hitelesítő adatok). A biztonsági mentések jellemzően ütemezés szerint futnak, és hozzáférést igényelnek a tárolóhoz (a mentett fájlok kivonásához) és az adatbázisokhoz (a biztonsági mentésbe foglalandó tartalmak másolásához és olvasásához). Az ilyen erőforrások elérésének meghiúsulása konzisztens biztonsági mentési hibát eredményezhet. 

Ha a biztonsági mentési hibák történnek, tekintse át a legutóbbi eredmények listáját, és Ismerje meg, hogy milyen típusú hiba történik. A tárolási hozzáférési hibák esetében tekintse át és frissítse a biztonsági mentési konfigurációban használt tárolási beállításokat. Adatbázis-hozzáférési hibák esetén tekintse át és frissítse a kapcsolatok karakterláncait az Alkalmazásbeállítások részeként; Ezután folytassa a biztonsági mentési konfiguráció frissítésével, hogy megfelelően tartalmazza a szükséges adatbázisokat. Az alkalmazások biztonsági mentésével kapcsolatos további információkért lásd: [webalkalmazás biztonsági](manage-backup.md)mentése Azure app Serviceban.

## <a name="nodejs"></a>Új Node. js-alkalmazások telepítésekor Azure App Service
A Node. js-alkalmazások alapértelmezett konfigurációja Azure App Service a leggyakoribb alkalmazások igényeinek legmegfelelőbb. Ha a Node. js-alkalmazás konfigurációja a személyre szabott hangolás előnyeit kihasználva javítja a teljesítményt vagy optimalizálja a CPU/memória/hálózati erőforrások erőforrás-felhasználását, tekintse [meg az ajánlott eljárásokat és a hibaelhárítási útmutatót a Node-alkalmazásokhoz a Azure app Service](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md). Ez a cikk a Node. js-alkalmazás konfigurálásához szükséges iisnode-beállításokat ismerteti, ismerteti az alkalmazás által megtekinthető különböző forgatókönyveket vagy problémákat, és bemutatja, hogyan kezelheti ezeket a problémákat.

