---
title: "Azure App Service – ajánlott eljárások"
description: "Ajánlott eljárások és hibaelhárítás az Azure App Service megismerése"
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 1a36c11fcce33c0148fa7d0a4e947a9cc37cd276
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2018
---
# <a name="best-practices-for-azure-app-service"></a>Azure App Service – ajánlott eljárások
Ez a cikk foglalja össze az ajánlott eljárások a [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>Közös elhelyezés
Ha különböző régiókban található Azure-erőforrások hasonló megoldással például a web app és az adatbázis létrehozása a hatások tartozhatnak a következők:

* Nagyobb késéseket erőforrások közötti kommunikáció
* Pénzügyi díjak kimenő adatok átvitele a kereszt-régió, a a [Azure díjszabása](https://azure.microsoft.com/pricing/details/data-transfers).

Közös elhelyezés ugyanabban a régióban ideális megoldás például egy webalkalmazást és a tartalom vagy adatok tárolására használt adatbázis vagy a tárolási fiók létrehozása Azure-erőforrások. Győződjön meg arról, hogy erőforrások létrehozásakor vannak a azonos Azure-régió, kivéve, ha az adott üzleti rendelkezik, és a tervezési azokat nem lehet az oka. Áthelyezheti egy App Service-alkalmazást és az adatbázis ugyanabban a régióban, ami a [App Service klónozási szolgáltatásának](app-service-web-app-cloning.md) jelenleg elérhető alkalmazások prémium szintű App Service-csomag.   

## <a name="memoryresources"></a>Ha használnak az alkalmazások a vártnál több memória
Ha egy alkalmazás felügyelet jelöli a vártnál több memóriát fogyaszt, vagy fontolja meg a szolgáltatási javaslatokat figyelje a [App Service automatikus javítás funkció](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). A memória küszöbértéke alapuló egyéni műveletek tart az automatikus javítás funkció lehetőségek közül. Műveletek a vizsgálat a helyszíni megoldás a memóriakép keresztül az értesítő e-mailek pontszámot span újrahasznosítása a munkavégző folyamat által. Automatikus javítás konfigurálható és egy rövid felhasználói felületen keresztül Web.config fájlban a következő blogbejegyzésben a részben ismertetett módon a [szolgáltatás támogatási webhely bővítmény](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>Ha használja a vártnál több CPU-alkalmazások
Ha azt észleli az alkalmazás feldolgozó további CPU mint várt vagy nem észlel ismétlődő CPU igényeiben jelentkező felügyelet jelöli, vagy szolgáltatási javaslatokat távolítsa el a vertikális felskálázásával vagy az App Service-csomag kiterjesztése. Az alkalmazás állapotalapú, vertikális felskálázásával akkor lehetőség, amíg az alkalmazás esetén állapotmentes, méretezési ki fogja diktálni nagyobb rugalmasságot és nagyobb skálázási lehetséges. 

"Állapotalapú" vagy "állapotmentes" alkalmazások kapcsolatos információkért tekintse meg a videót: [méretezhető-végpontok Többrétegű alkalmazások tervezése a Microsoft Azure Web Apps](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). App Service méretezés és az automatikus skálázás beállításokkal kapcsolatos további információkért olvassa el: [egy webalkalmazás skálázása az Azure App Service](web-sites-scale.md).  

## <a name="socketresources"></a>A szoftvercsatorna-erőforrások elfogytak, amikor
Egy skálázását kimenő TCP-kapcsolatok gyakori oka klienskódtárait, amelyek nem felelnek meg ismét felhasználni, TCP-kapcsolatok, illetve a magasabb szintű protokoll, például a HTTP - Keep-Alive nem alkalmazhatók használatát. Tekintse át a könyvtárak, az App Service-csomag van konfigurálva vagy érhető el a kódban, a kimenő kapcsolatok hatékony használatának biztosításához az alkalmazások által hivatkozott dokumentációját. Is útmutatását library dokumentációjában megfelelő létrehozására és kiadás vagy karbantartási kapcsolatok megakadályozására elkerülése érdekében. Az ilyen ügyfelek szalagtárak vizsgálatokat végrehajtása közben folyamatban hatás mérsékelheti kiterjesztése több példánya.

### <a name="nodejs-and-outgoing-http-requests"></a>NODE.js és kimenő http-kérelmek
Node.js és sok kimenő http-kérelmek használata, ha HTTP - életben tartási kezelésével fontos valóban. Használhatja a [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` csomag könnyebb a kódban.

Mindig kezelje a `http` választ, még akkor is, ha így tesz, nem a kezelő. A válasz nem kezeli megfelelően, ha a rendszer az alkalmazás végül elakadnak, mert nincs további sockets érhetők el.

Például, ha használata a `http` vagy `https` csomag:

```
var request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Ha az App Service Linux több maggal rendelkező gépen fut, egy másik ajánlott úgy PM2 használható az alkalmazás végrehajtása több Node.js folyamat elindításához. Ehhez a tárolóhoz indítási parancs megadásával.

Ha például négy példányok elindításához:

`pm2 start /home/site/wwwroot/app.js --no-daemon -i 4`

## <a name="appbackup"></a>Ha az alkalmazás biztonsági mentés indítása sikertelen
Miért alkalmazás biztonsági mentés hibája vannak-e a két leggyakoribb okok: Érvénytelen tárolási beállításokat, és érvénytelen adatbázis-konfiguráció. Ezek a hibák általában akkor fordulhat elő, ha vagy a tároló- és adatbázis-erőforrások módosításairól, hogyan férhet hozzá ezekhez az erőforrásokhoz (pl. hitelesítő adatokat a biztonsági mentés beállításait a kijelölt adatbázis frissítése) változások. Biztonsági mentés általában ütemezhető és hozzá kell férniük (a fájlok a biztonsági mentés írása) és a adatbázisok (a másolás és a biztonsági mentés szereplő tartalmának olvasása közben). Vagy erőforrásainak elérésére sikertelen eredményét konzisztens biztonsági mentés sikertelen lesz. 

Biztonsági mentési hibák fordulhat elő, amikor tekintse át a legutóbbi eredmény milyen típusú hiba történik. Érdekében tárolási hozzáférés esetén nézze át, és a biztonsági mentési konfigurációban használt tárolási beállításainak módosítása. Hozzáférés adatbázishibák esetén nézze át, és a kapcsolatok karakterláncok frissítése Alkalmazásbeállítások; részeként Lépjen a szükséges adatbázisokat megfelelően felvenni a biztonsági mentési konfiguráció frissítése. Alkalmazás biztonsági mentési további tájékoztatást talál a [készítsen biztonsági másolatot egy webalkalmazást az Azure App Service](web-sites-backup.md) dokumentációját.

## <a name="nodejs"></a>Amikor új Node.js-alkalmazások vannak telepítve az Azure App Service
Node.js-alkalmazások az Azure App Service alapértelmezett konfigurációjának szolgáló legjobb igényeinek megfelelően a leggyakrabban használt alkalmazások. A Node.js-alkalmazás konfigurációja a teljesítmény javításához vagy erőforrás-használat Processzor/memória/hálózati erőforrások optimalizálására személyre szabott hangolása előnyös, ha az ajánlott eljárásokról és a hibaelhárítási lépéseket sikerült megtekintheti. Ez a dokumentáció cikk ismerteti az iisnode beállítások be kell állítania a Node.js-alkalmazás, ez a témakör a különböző vagy állít ki, hogy az alkalmazásban is elérhető lehet, és bemutatja, hogyan problémák megoldásához: [gyakorlati tanácsok és hibaelhárítási útmutatójában csomópont alkalmazások az Azure App Service](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md).   

