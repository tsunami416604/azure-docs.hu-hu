---
title: Azure App Service – ajánlott eljárások
description: Ajánlott eljárások és hibaelhárítás az Azure App Service megismerése
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
ms.openlocfilehash: 7c5eb6190d4a4cdfa47779d2c4d7aadac5a2fb80
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
ms.locfileid: "27868318"
---
# <a name="best-practices-for-azure-app-service"></a>Azure App Service – ajánlott eljárások
Ez a cikk foglalja össze az ajánlott eljárások a [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>Közös elhelyezés
Ha Azure-erőforrások létrehozása hasonló megoldással például a web app és az adatbázis különböző régiókban található, a következő hatásai vannak rendelkezhet:

* Nagyobb késéseket erőforrások közötti kommunikáció
* Pénzügyi díjak kimenő adatok átvitele a kereszt-régió, a a [Azure díjszabása](https://azure.microsoft.com/pricing/details/data-transfers).

Közös elhelyezés ugyanabban a régióban ideális megoldás például egy webalkalmazást és a tartalom vagy adatok tárolására használt adatbázis vagy a tárolási fiók létrehozása Azure-erőforrások. Erőforrások létrehozása, ellenőrizze, az azonos Azure-régió, kivéve, ha az adott üzleti van, vagy azok nem az az oka. terv vannak. Áthelyezheti egy App Service-alkalmazást ugyanabban a régióban legyen az adatbázis használatával a [App Service klónozási szolgáltatásának](app-service-web-app-cloning.md) jelenleg elérhető alkalmazások prémium szintű App Service-csomag.   

## <a name="memoryresources"></a>Ha használnak az alkalmazások a vártnál több memória
Ha azt tapasztalja, egy alkalmazás a vártnál több memóriát fogyaszt jelzett figyelési vagy service javaslatok keresztül, akkor fontolja meg a [App Service automatikus javítás funkció](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). A memória küszöbértéke alapuló egyéni műveletek tart az automatikus javítás funkció lehetőségek közül. Műveletek a vizsgálat a helyszíni megoldás a memóriakép keresztül az értesítő e-mailek pontszámot span újrahasznosítása a munkavégző folyamat által. Automatikus javítás konfigurálható és egy rövid felhasználói felületen keresztül Web.config fájlban a következő blogbejegyzésben a részben ismertetett módon a [szolgáltatás támogatási webhely bővítmény](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>Ha használja a vártnál több CPU-alkalmazások
Bizonyára észrevette, amikor egy alkalmazást használ a vártnál több CPU, vagy feladatait ismételt CPU igényeiben jelentkező keresztül figyelési vagy service ajánlásokat, ahogyan távolítsa el a vertikális felskálázásával vagy az App Service-csomag kiterjesztése. Az alkalmazás állapotalapú, vertikális felskálázásával akkor lehetőség, amíg az alkalmazás esetén az állapot nélküli, méretezési out lehetővé teszi nagyobb rugalmasságot és nagyobb skálázási lehetséges. 

"Állapotalapú" vagy "állapotmentes" alkalmazások kapcsolatos információkért tekintse meg a videót: [méretezhető-végpontok Többrétegű alkalmazások tervezése a Microsoft Azure Web Apps](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). App Service méretezés és az automatikus skálázás beállításokkal kapcsolatos további információkért lásd: [egy webalkalmazás skálázása az Azure App Service](web-sites-scale.md).  

## <a name="socketresources"></a>A szoftvercsatorna-erőforrások elfogytak, amikor
Egy skálázását kimenő TCP-kapcsolatok gyakori oka ügyfél tárak, amelyek nincsenek megvalósítva TCP-kapcsolatok újból, vagy egy magasabb szintű protokoll, például a HTTP - életben tartási nem használatakor használatát. Tekintse át a könyvtárak, az App Service-csomag van konfigurálva vagy érhető el a kódban, a kimenő kapcsolatok hatékony használatának biztosításához az alkalmazások által hivatkozott dokumentációját. Is útmutatását library dokumentációjában megfelelő létrehozására és kiadás vagy karbantartási kapcsolatok megakadályozására elkerülése érdekében. Ilyen ügyfél szalagtárak vizsgálat van folyamatban, amíg hatás mérsékelheti kiterjesztése több példánya.

### <a name="nodejs-and-outgoing-http-requests"></a>NODE.js és kimenő http-kérelmek
Node.js és sok kimenő http-kérelmek használata, ha HTTP - életben tartási kezelésével fontos. Használhatja a [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` csomag könnyebb a kódban.

Mindig kezelni a `http` választ, még akkor is, ha így tesz, nem a kezelő. A válasz nem kezeli megfelelően, ha az alkalmazás Beragadt lekérdezi végül, mert nincs további sockets érhetők el.

Például, ha használata a `http` vagy `https` csomag:

```
var request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Ha az App Service Linux több maggal rendelkező gépen fut, egy másik ajánlott úgy PM2 használható az alkalmazás végrehajtása több Node.js folyamat elindításához. Ez a tároló indítási parancs megadásával teheti meg.

Ha például négy példányok elindításához:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="appbackup"></a>Ha az alkalmazás biztonsági mentés indítása sikertelen
Miért alkalmazás biztonsági mentés hibája vannak-e a két leggyakoribb okok: Érvénytelen tárolási beállításokat, és érvénytelen adatbázis-konfiguráció. Ezek a hibák általában akkor fordulhat elő, ha vagy a tároló- és adatbázis-erőforrások módosításairól, hogyan férhet hozzá ezekhez az erőforrásokhoz (például hitelesítő adatokat a biztonsági mentés beállításait a kijelölt adatbázis frissítése) változások. Biztonsági mentés általában ütemezhető és hozzá kell férniük (a szerint kiírta volna a biztonságimásolat-fájlok) és a adatbázisok (a másolás és a biztonsági mentés szereplő tartalmának olvasása közben). Vagy erőforrásainak elérésére sikertelen eredményét konzisztens biztonsági mentés sikertelen lesz. 

Biztonsági mentési hibák fordulhat elő, amikor tekintse át a legutóbbi eredmény milyen típusú hiba történik. A tároló hozzáférés meghibásodása esetén tekintse át, és a biztonsági mentési konfigurációban használt tárolási beállításainak módosítása. Az adatbázis-hozzáférési hibákat okozhat tekintse át, és a kapcsolatok karakterláncok frissítése Alkalmazásbeállítások; részeként Lépjen a szükséges adatbázisokat megfelelően felvenni a biztonsági mentési konfiguráció frissítése. Alkalmazás biztonsági mentések további információkért lásd: [készítsen biztonsági másolatot egy webalkalmazást az Azure App Service](web-sites-backup.md).

## <a name="nodejs"></a>Amikor új Node.js-alkalmazások vannak telepítve az Azure App Service
Node.js-alkalmazások az Azure App Service alapértelmezett konfigurációjának szolgáló legjobb igényeinek megfelelően a leggyakrabban használt alkalmazások. Ha a Node.js-alkalmazás konfigurációja előnyös a teljesítmény javítása vagy Processzor/memória/hálózati erőforrások erőforrás-használat optimalizálása érdekében tekintse meg a személyre szabott hangolása [gyakorlati tanácsok és hibaelhárítási útmutatójában csomópont alkalmazások az Azure-alkalmazás Szolgáltatás](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md). Ez a cikk ismerteti az iisnode beállítások be kell állítania a Node.js-alkalmazás, ez a témakör a különböző vagy állít ki, hogy az alkalmazásban is elérhető lehet, és bemutatja, hogyan problémák megoldásához.

