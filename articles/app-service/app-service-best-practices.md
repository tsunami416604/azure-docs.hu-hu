---
title: Ajánlott eljárások
description: Ismerje meg az ajánlott eljárásokat és az Azure App Service-ben futó alkalmazás gyakori hibaelhárítási forgatókönyveit.
author: dariagrigoriu
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.topic: article
ms.date: 07/01/2016
ms.author: dariac
ms.custom: seodec18
ms.openlocfilehash: ded812d5d7a0440466e7284b56c90965ea00406e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768486"
---
# <a name="best-practices-for-azure-app-service"></a>Gyakorlati tanácsok az Azure App Service-hez
Ez a cikk az [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)használatával kapcsolatos gyakorlati tanácsokat foglalja össze. 

## <a name="colocation"></a><a name="colocation"></a>Colocation
Ha egy megoldás, például egy webalkalmazás és egy adatbázis összeállítása, az Azure-erőforrások különböző régiókban találhatók, a következő hatásokkal járhatnak:

* Az erőforrások közötti kommunikáció megnövekedett késleltetése
* Az [Azure díjszabási oldalán](https://azure.microsoft.com/pricing/details/data-transfers)feltüntetett kimenő adatátvitel régióközi díjai .

Az azonos régióban lévő közös elhelyezés a legjobb az Azure-erőforrások számára, amelyek például egy webalkalmazást és egy tartalom vagy adat tárolására használt adatbázist vagy tárfiókot hoznak össze. Erőforrások létrehozásakor győződjön meg arról, hogy ugyanabban az Azure-régióban, kivéve, ha van konkrét üzleti vagy tervezési oka, hogy nem kell. Az App Service-alkalmazásokat áthelyezheti az adatbázissal azonos régióba a Prémium App Service Service-alkalmazásokhoz jelenleg elérhető [App Service-klónozási funkció](app-service-web-app-cloning.md) használatával.   

## <a name="when-apps-consume-more-memory-than-expected"></a><a name="memoryresources"></a>Ha az alkalmazások a vártnál több memóriát fogyasztanak
Ha azt veszi észre, hogy egy alkalmazás a vártnál több memóriát használ fel a figyelési vagy szolgáltatási javaslatok szerint, fontolja meg az [App Service automatikus javítási funkcióját.](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites) Az automatikus javítási funkció egyik lehetősége a memóriaküszöbértéken alapuló egyéni műveletek. A műveletek az e-mailes értesítésektől a memóriaképen keresztüli vizsgálatig, a munkavégző folyamat újrahasznosításával a helyszíni csökkentésig terjednek. Az automatikus gyógyítás konfigurálható a web.config-on keresztül és egy barátságos felhasználói felületen keresztül, ahogy azt az App Service support site extension blogbejegyzésében [leírtak szerint.](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps)   

## <a name="when-apps-consume-more-cpu-than-expected"></a><a name="CPUresources"></a>Ha az alkalmazások a vártnál több CPU-t fogyasztanak
Ha azt veszi észre, hogy egy alkalmazás a vártnál több processzort használ fel, vagy ismétlődő CPU-csúcsokat tapasztal a figyelési vagy szolgáltatási javaslatok on keresztül jelzett módon, fontolja meg az App Service-csomag felskálázását vagy horizontális felskálázását. Ha az alkalmazás állapotalapú, a skálázás az egyetlen lehetőség, míg ha az alkalmazás állapot nélküli, horizontális felskálázás nagyobb rugalmasságot és nagyobb léptékű potenciált biztosít. 

Az "állapotalapú" és az "állapotnélküli" alkalmazásokról további információt a következő videó megtekintéséhez: [Skálázható, teljes körű többrétegű alkalmazás tervezése az Azure App Service szolgáltatásban.](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid) Az App Service méretezési és automatikus skálázási beállításairól további információt a [Webalkalmazás méretezése az Azure App Service-ben című témakörben talál.](manage-scale-up.md)  

## <a name="when-socket-resources-are-exhausted"></a><a name="socketresources"></a>A szoftvercsatorna-erőforrások kimerülése esetén
A kimenő TCP-kapcsolatok kimerülésének gyakori oka az ügyfélkódtárak használata, amelyek nincsenek megvalósítva a TCP-kapcsolatok újrafelhasználásához, vagy ha egy magasabb szintű protokollt, például a HTTP - Keep-Alive protokollt nem használják. Tekintse át az App Service-csomagban az alkalmazások által hivatkozott egyes tárak dokumentációját, és győződjön meg arról, hogy konfigurálva vannak vagy elérhetők a kódban a kimenő kapcsolatok hatékony újrafelhasználása érdekében. A megfelelő létrehozáshoz és kiadáshoz vagy tisztításhoz kövesse a könyvtárdokumentációs útmutatót is a kapcsolatok szivárgásának elkerülése érdekében. Amíg az ilyen ügyfélkódtárak vizsgálata folyamatban van, a hatás csökkenthető több példányra való horizontális felskálázással.

### <a name="nodejs-and-outgoing-http-requests"></a>Node.js és kimenő http-kérések
Amikor a Node.js és sok kimenő http-kérelmek, foglalkozó HTTP - Keep-Alive fontos. Használhatja az [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` csomagot, hogy megkönnyítse a kódot.

Mindig kezelje `http` a választ, még akkor is, ha nem csinál semmit a kezelőben. Ha nem kezeli megfelelően a választ, az alkalmazás végül elakad, mert nincs több szoftvercsatorna áll rendelkezésre.

Ha például a `http` csomaggal `https` dolgozik:

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Ha több maggal rendelkező gépen fut Linuxon az App Service szolgáltatásban, egy másik ajánlott eljárás a PM2 használata több Node.js folyamat indításához az alkalmazás végrehajtásához. Ezt úgy teheti meg, hogy megad egy indítási parancsot a tárolóhoz.

Például négy példány indításához:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="when-your-app-backup-starts-failing"></a><a name="appbackup"></a>Amikor az alkalmazás biztonsági mentése sikertelennek indul
Az alkalmazás biztonsági mentésének két leggyakoribb oka a következő: érvénytelen tárolási beállítások és érvénytelen adatbázis-konfiguráció. Ezek a hibák általában akkor fordulnak elő, ha a tárolási vagy adatbázis-erőforrások módosulnak, vagy az erőforrások elérésének módosításai (például a biztonsági mentési beállításokban kiválasztott adatbázis hitelesítő adatai frissülnek). A biztonsági mentések általában ütemezés szerint futnak, és hozzáférést igényelnek a tárolóhoz (a biztonsági másolatot készítő fájlok kiírásához) és az adatbázisokhoz (a tartalom másolásához és olvasásához a biztonsági mentésben való tároláshoz). Az eredmény nem érhető el ezekhez az erőforrásokhoz lenne konzisztens biztonsági mentési hiba. 

Amikor biztonsági mentési hibák történnek, tekintse át a legutóbbi eredményeket, hogy megismerje, milyen típusú hiba történik. A tárolási hozzáférési hibák, tekintse át és frissítse a tárolási beállításokat a biztonsági mentési konfigurációban használt. Adatbázis-hozzáférési hibák esetén tekintse át és frissítse a kapcsolati karakterláncokat az alkalmazásbeállítások részeként; ezután folytassa a biztonsági másolat konfigurációjának frissítését, hogy megfelelően tartalmazza a szükséges adatbázisokat. Az alkalmazásbiztonsági mentések ről további információt a [Webalkalmazás biztonsági mentése az Azure App Service szolgáltatásban című témakörben talál.](manage-backup.md)

## <a name="when-new-nodejs-apps-are-deployed-to-azure-app-service"></a><a name="nodejs"></a>Amikor új Node.js alkalmazások vannak telepítve az Azure App Service-ben
Az Azure App Service alapértelmezett konfigurációja node.js alkalmazások célja, hogy a legjobban megfeleljen a leggyakoribb alkalmazások igényeinek. Ha a Node.js alkalmazás konfigurációja a teljesítmény javítása vagy a CPU-/memória/hálózati erőforrások erőforrás-használatának optimalizálása érdekében személyre szabott hangolást élvezne, olvassa el [az Azure App Service node alkalmazásainak gyakorlati és hibaelhárítási útmutatóját.](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md) Ez a cikk ismerteti az iisnode beállításokat, amelyeket konfigurálnia kell a Node.js alkalmazáshoz, ismerteti az alkalmazás különböző forgatókönyveit vagy problémáit, és bemutatja, hogyan oldhatja meg ezeket a problémákat.


## <a name="next-steps"></a>Következő lépések
Az ajánlott eljárásokról az [App Service Diagnosztika](https://docs.microsoft.com/azure/app-service/overview-diagnostics) webhelyre talál további információt az erőforrásra vonatkozó, bevált gyakorlati tanácsokért.

- Nyissa meg a webalkalmazást az [Azure Portalon.](https://portal.azure.com)
- Kattintson **a Problémák diagnosztizálása és megoldása** a bal oldali navigációs, amely megnyitja App Service Diagnosztika.
- Válassza az **Ajánlott eljárások** kezdőlapcsempét.
- Kattintson **a gyakorlati tanácsok a rendelkezésre állás & teljesítményéhez,** vagy az optimális **konfigurációra vonatkozó gyakorlati tanácsok ra kattintva** megtekintheti az alkalmazás aktuális állapotát az ajánlott eljárások tekintetében.

Ezen a hivatkozáson keresztül közvetlenül is megnyithatja `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`az App Service Diagnosztikát az erőforráshoz: .