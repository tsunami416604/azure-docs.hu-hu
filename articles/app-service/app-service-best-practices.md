---
title: Ajánlott eljárások – az Azure App Service-ben
description: Ismerje meg, ajánlott eljárások és hibaelhárítási az Azure App Service-ben.
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
ms.author: dariagrigoriu
ms.custom: seodec18
ms.openlocfilehash: fc3749a9ebfbf0319a57b471b6fce9f62042ba27
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57849778"
---
# <a name="best-practices-for-azure-app-service"></a>Azure App Service – ajánlott eljárások
Ez a cikk összegzi az ajánlott eljárások a [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>A közös elhelyezés
Ha egy megoldással, például egy webalkalmazás és adatbázis létrehozása Azure-erőforrások különböző régiókban találhatók, veheti fel a következő hatásai vannak:

* Az erőforrások közti kommunikációt késések
* Pénzügyi fizetni a kimenő adatok átviteli régiók szerint vonatkozóan. a [Azure díjszabási oldalán](https://azure.microsoft.com/pricing/details/data-transfers).

A közös elhelyezés ugyanabban a régióban összeállítása hasonló megoldással például egy web app és a egy adatbázis vagy a storage-fiók tartalma vagy az adatok tárolásához használt Azure-erőforrások ajánlott. Az erőforrások létrehozásához, győződjön meg arról, ha azok ugyanazon Azure-régióban kivéve, ha azok nem az adott üzleti vagy a tervezési OK. Továbbléphet az App Service-alkalmazás ugyanabban a régióban legyen az adatbázis használatával a [klónozási szolgáltatásának App Service](app-service-web-app-cloning.md) jelenleg elérhető prémium szintű App Service-csomag alkalmazásokhoz.   

## <a name="memoryresources"></a>Amikor használnak az alkalmazások a vártnál több memória
Figyelési, illetve szolgáltatási javaslatok keresztül jelzett mikor láthatja, hogy egy alkalmazást a vártnál több memóriát használ fel, fontolja meg a [App Service automatikus gyógyítás szolgáltatásával](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Egyéni műveletek egy memória küszöbértékét a egyikét az automatikus javítás funkció tart. Műveletek által a feldolgozó folyamat újrahasznosítási ívelhet át az e-mail-értesítések palettát keresztül memóriaképet a helyszíni megoldás a vizsgálatot. Automatikus javítás konfigurálható és egy egyszerű felhasználói felületen keresztül web.config ebben a blogbejegyzésben találhat az ismertetett módon a [App Service támogatási Webhelybővítmény](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>Ha használja a vártnál több CPU-alkalmazások
Megfigyelheti, hogy az alkalmazás felhasználja a vártnál nagyobb CPU vagy élményt ismétlődő CPU adatforgalmi csúcsokhoz jelzett keresztül figyelési, illetve szolgáltatási javaslatok, fontolja meg vertikális vagy horizontális felskálázás az App Service-csomag. Ha az alkalmazást állapotalapú, vertikális felskálázása csak a lehetőség, míg ha az alkalmazás állapot nélküli, méretezési out történő további rugalmasságot és újabb skálázási lehetőségeit. 

"Állapotalapú" és "állapot nélküli" alkalmazások kapcsolatos további részletekért tekintse meg ezt a videót: [Skálázható teljes körű Többrétegű alkalmazások tervezése az Azure App Service](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). App Service-ben skálázással és automatikus skálázást lehetőségekkel kapcsolatos további információkért lásd: [webalkalmazások méretezése az Azure App Service](web-sites-scale.md).  

## <a name="socketresources"></a>Amikor szoftvercsatorna-erőforrásai kimerültek
Egy skálázását kimenő TCP-kapcsolatok gyakori oka klienskódtárak, amelyek nem felelnek meg újból felhasználhatja a TCP-kapcsolatok, vagy egy magasabb szintű protokoll, például HTTP - életben tartási nem használatakor használatát. Tekintse át a kódtárakat, az App Service-csomagra konfigurálva, vagy azok érhetők el a kód a kimenő kapcsolatok hatékony használatának biztosítása érdekében az alkalmazások által hivatkozott dokumentációját. Az alábbi is a megfelelő létrehozása és a kiadás vagy karbantartási kapcsolatok kiszivárgását elkerülése érdekében könyvtár dokumentációja nyújt segítséget. Ilyen ügyfél kódtárak vizsgálat van folyamatban, amíg hatás mérsékelheti horizontális felskálázás több példányra.

### <a name="nodejs-and-outgoing-http-requests"></a>NODE.js- és kimenő http-kérelmekre
Használata a Node.js és sok kimenő http-kérelmek, HTTP - életben tartási többé vesződnie a sérült esetén fontos. Használhatja a [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` csomagot, hogy egyszerűbb legyen a kódban.

Mindig kezelni a `http` választ, akkor is, ha nem tesz semmit, a kezelő a. Ha megfelelően kezelik a választ, a az alkalmazás idővel elakad, mert nincsenek további sockets érhetők el.

Például, ha dolgozik a `http` vagy `https` csomag:

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

A linuxon futó App Service-ben több maggal rendelkező gépen fut, ha egy másik ajánlott eljárás az PM2 használata több Node.js folyamat végrehajtásához, hogy az alkalmazás indításához. Ez a tároló egy indítási parancs megadásával teheti meg.

Ha például négy példány elindításához:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="appbackup"></a>Ha az alkalmazás biztonsági mentése sikertelen kezdődik
A két leggyakoribb okait, miért van a biztonsági mentés hibája app: érvénytelen, és érvénytelen adatbázis-konfiguráció. Ezek a hibák általában akkor történik, ha vannak a tárolási és adatbázis-erőforrásokat, és hogyan hozzáférni ezekhez az erőforrásokhoz (például a hitelesítő adatok frissítése a biztonsági mentés beállításait a kijelölt adatbázis) változásainak. Biztonsági másolatokat jellemzően egy ütemezés szerint fut és hozzá kell férniük (az ad ki a biztonságimásolat-fájlok) tárolási és adatbázis (a másolás és a biztonsági mentés foglalandó olvasásakor). A sikertelen vagy ezek az erőforrások eléréséhez az eredmény alkalmazáskonzisztens biztonsági mentés sikertelen lenne. 

Biztonsági mentési hibák esetén tekintse át a legutóbbi eredményeket, hogy milyen típusú hiba történik. A tároló-hozzáférési hiba tekintse át, és a biztonsági mentési konfigurációban használt tárolási beállításainak frissítése. Adatbázis-hozzáférési hibák tekintse át és Alkalmazásbeállítások; részeként a kapcsolatok karakterláncok frissítése Ezután folytassa a megfelelő tartalmazza a szükséges adatbázisokat a biztonsági mentési konfiguráció frissítése. Alkalmazás biztonsági mentések további információkért lásd: [biztonsági mentése az Azure App Service webalkalmazás](manage-backup.md).

## <a name="nodejs"></a>Amikor új Node.js-alkalmazások vannak telepítve az Azure App Service-ben
Node.js-alkalmazások az Azure App Service alapértelmezett konfigurációjának célja igény szerint a leggyakrabban használt alkalmazások igényeinek. Ha a Node.js-alkalmazás konfigurációja előnyös a teljesítmény javítása vagy a CPU/memória/hálózati erőforrások erőforrás-használat optimalizálása érdekében tekintse meg a személyre szabott hangolása [ajánlott eljárások és hibaelhárítási útmutató csomópont-alkalmazásokhoz az Azure-alkalmazás Szolgáltatás](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md). Ez a cikk az iisnode-beállításait, előfordulhat, hogy konfigurálnia kell a Node.js-alkalmazás, ez a témakör a különböző vagy az, hogy az alkalmazás lehetséges, hogy legyen elérhető, és bemutatja, hogyan kezelheti ezeket a problémákat ismerteti.

