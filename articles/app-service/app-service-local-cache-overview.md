---
title: Az Azure App Service helyi gyorsítótár áttekintése |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan engedélyezze, méretezze át és az Azure App Service helyi gyorsítótár-szolgáltatás állapotának lekérdezése
services: app-service
documentationcenter: app-service
author: SyntaxC4
manager: yochayk
editor: ''
tags: optional
keywords: ''
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2016
ms.author: cfowler
ms.openlocfilehash: 75f2dcb80514105ed663ba1fe5f7adccc05af1fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836384"
---
# <a name="azure-app-service-local-cache-overview"></a>Az Azure App Service helyi gyorsítótár áttekintése
Az Azure web app tartalom Azure Storage tárolja, és tartósan tartalom megosztásnak illesztett be van. Ez a kialakítás célja, hogy az alkalmazások különböző dolgozni, és a következő attribútumokkal rendelkezik:  

* A tartalom megosztott virtuális gép (VM) több példánya a webalkalmazást.
* A tartalom és a futó webes alkalmazások által módosítható.
* Naplófájlok és diagnosztikai adatok fájlok az azonos megosztott tartalmat tartalmazó mappa alatt érhetők el.
* Új tartalom közzététele közvetlenül frissíti a tartalmat tartalmazó mappa. Azonnal megtekintheti az SCM webhely és a futó webalkalmazás ugyanahhoz a tartalomhoz (általában egyes technológiák, például az ASP.NET, a kezdeményezni a webes alkalmazás újraindítása a fájl módosításai a legújabb tartalmának lekérdezése).

Miközben sok webes alkalmazás használja legalább mindezeket a funkciókat, néhány webalkalmazások kell nagy teljesítményű, csak olvasható tartalomtárhoz konvertál, amely futtathatják a magas rendelkezésre állású. Ezeket az alkalmazásokat is kihasználhatja a le egy Virtuálisgép-példány az adott helyi gyorsítótár.

Az Azure App Service helyi gyorsítótár-szolgáltatás a tartalom webes szerepkör nézetét biztosítja. Ezt a tartalmat egy írási-de-elvetési gyorsítótár, a tároló tartalmának aszinkron módon helyszíni indítási létrehozott. Amikor készen áll a gyorsítótár, a hely át lett váltva a gyorsítótárazott tartalom futtatni. A helyi gyorsítótár futó webalkalmazások rendelkezik a következő előnyöket biztosítja:

* Azok a késleltetések felmerülő Azure tárhelyén található tartalom elérésekor hagyja.
* A tervezett frissítések vagy nem tervezett állásidőt és bármely más üzemzavarokhoz vezethet az Azure Storage a tartalommegosztás átadott kiszolgálókon előforduló immúnis.
* Tárolási fájlmegosztás-módosítások miatt kevesebb alkalmazás újraindul rendelkeznek.

## <a name="how-local-cache-changes-the-behavior-of-app-service"></a>Hogyan helyi gyorsítótár módosítja az App Service működését
* A helyi gyorsítótárban a webes alkalmazás /site és /siteextensions mappák másolatát. A webes alkalmazás következő indításakor helyi Virtuálisgép-példány jön létre. Egy webes alkalmazás a helyi gyorsítótár mérete alapértelmezés szerint 300 MB korlátozott, de legfeljebb 2 GB-ot is növeli.
* A helyi gyorsítótárban írható-olvasható. Azonban a módosításokat a web app virtuális gépeket helyezi át, illetve lekérdezi újraindításakor elvész. Ne használjon helyi gyorsítótár-alkalmazásokat, amelyek a kritikus fontosságú adatok tárolása a tartalomtárhoz konvertál.
* Webalkalmazások továbbra is írhat a naplófájlok és diagnosztikai adatok, mint jelenleg. Napló fájlokat és adatokat, azonban tárolják helyileg a virtuális Gépen. Majd forrásmappa keresztül rendszeres időközönként a megosztott tartalom tárolójába. A Másolás a megosztott tartalomtároló hányad elérhető--készít biztonsági elveszhet miatt Virtuálisgép-példány hirtelen összeomlását írási.
* A web Apps, amely a helyi gyorsítótárat használnak, a naplófájlok és az adatok mappák gyökérmappa-szerkezetében lévő változás áll. Nincsenek most almappák "egyedi azonosítója" + időbélyeg elnevezési mintát tárolási naplófájlok és az adatok mappák. A minden felel meg a Virtuálisgép-példány, ha a webalkalmazás fut, vagy futott.  
* A webes alkalmazás közzétételi mechanizmusok keresztül közzétételi módosításait a megosztott tartalomtároló közzé. Ez az elvárt működés, mert azt szeretnénk, ha a tartós kell közzétett tartalmat. A webes alkalmazás a helyi gyorsítótár frissítése, akkor újra kell indítani. Nem ez úgy tűnik, például egy túl sok lépést? Ahhoz, hogy az életciklus zökkenőmentes, olvassa el a cikk későbbi részében.
* A helyi gyorsítótár D:\Home mutat. D:\Local továbbra is az ideiglenes VM-specifikus tárolási mutasson.
* Az alapértelmezett tartalom nézet az SCM hely továbbra is fennáll, a megosztott tartalomtároló be.

## <a name="enable-local-cache-in-app-service"></a>Az App Service szolgáltatásban a helyi gyorsítótár engedélyezése
Helyi gyorsítótár konfigurálása az alkalmazás foglalt beállítások használatával. Ezek a beállítások alkalmazás az alábbi módszerekkel konfigurálhatja:

* [Azure Portal](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Helyi gyorsítótár konfigurálása az Azure-portál használatával
<a name="Configure-Local-Cache-Portal"></a>

Helyi gyorsítótár /--webalkalmazás alapon, az Alkalmazásbeállítás használatával engedélyezheti:`WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Az Azure portál Alkalmazásbeállítások: helyi gyorsítótárban](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Helyi gyorsítótár konfigurálása az Azure Resource Manager használatával
<a name="Configure-Local-Cache-ARM"></a>

```

...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],

"properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>A helyi gyorsítótár mérete beállításainak módosítása
Alapértelmezés szerint a helyi gyorsítótár mérete **300 MB**. Ez magában foglalja a /site, és másolja át a tartalmat tároló /siteextensions mappák, valamint a helyileg létrehozott naplók és az adatok mappákat. Ez a korlát növeléséhez használja az Alkalmazásbeállítás `WEBSITE_LOCAL_CACHE_SIZEINMB`. Növelheti a méretét legfeljebb **2 GB** (2000 MB) egy webalkalmazást.

## <a name="best-practices-for-using-app-service-local-cache"></a>Alkalmazás szolgáltatás helyi gyorsítótárából használatának ajánlott eljárásai
Javasoljuk, hogy a helyi gyorsítótár együtt használjon a [előkészítési környezetek](../app-service/web-sites-staged-publishing.md) szolgáltatás.

* Adja hozzá a *állandóságát* Alkalmazásbeállítás `WEBSITE_LOCAL_CACHE_OPTION` értékű `Always` számára a **éles** tárolóhely. Ha használ `WEBSITE_LOCAL_CACHE_SIZEINMB`, is hozzá kell adnia a kapcsolódó beállításként az éles tárolóhelyre.
* Hozzon létre egy **átmeneti** tárolóhely, és tegye közzé az átmeneti tárolási helyre. Általában nem állít az előkészítési pont helyi gyorsítótár segítségével engedélyezze a zökkenőmentes build telepítése teszt életciklusa átmeneti, ha a helyi gyorsítótár előnyeit lekérése az éles webalkalmazásra.
* Tesztelje a átmeneti aljzat a helyen.  
* Ha készen áll, ki egy [a felcserélési művelet](../app-service/web-sites-staged-publishing.md#Swap) között az átmeneti és üzemi tárolóhely.  
* A kapcsolódó beállítások közé tartoznak a név és a kapcsolódó egy helyre. Ezért az átmeneti tárhely éles környezetben lekérdezi cserélni, amikor örökli a helyi gyorsítótár alkalmazás beállításai. Az újonnan cserélve éles tárolóhelyre néhány perc múlva a helyi gyorsítótárban fog futni, és fog kell tárolóhelyspecifikus tárolóhely melegítési részeként swap után. Ezért a tárolóhelycsere befejeződése után az éles tárolóhelyre fut a helyi gyorsítótárban.

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)
### <a name="how-can-i-tell-if-local-cache-applies-to-my-web-app"></a>Hogyan állapítható meg, ha a helyi gyorsítótár vonatkozik-e a webalkalmazást?
Ha a webalkalmazás kell egy nagy teljesítményű, megbízható tartalomtárhoz konvertál, nem használja a tartalomtároló futásidőben kritikus fontosságú adatokat írni, és 2 GB-nál kisebb teljes mérete, a válasz akkor "yes"! Ahhoz, hogy a /site és /siteextensions mappa teljes méretét, használja a hely bővítményét "Azure Web Apps lemezek használata terén."

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Hogyan állapítható meg, ha a hely váltott, hogy a helyi gyorsítótár segítségével?
Ha a helyi gyorsítótár funkció az átmeneti környezetének használata esetén a csereművelet végrehajtásához helyi gyorsítótár tárolóhelyspecifikus van. Ellenőrizze, hogy a webhely fut-e helyi gyorsítótár ellen, ellenőrizheti a munkavégző folyamat környezeti változót `WEBSITE_LOCALCACHE_READY`. Kövesse az utasításokat a a [munkavégző folyamat környezeti változó](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) lapján a munkavégző folyamat környezeti változó több példány eléréséhez.  

### <a name="i-just-published-new-changes-but-my-web-app-does-not-seem-to-have-them-why"></a>Új módosítások csak közzétett, de a webalkalmazás nem úgy tűnik, hogy azokat. Hogy miért?
Ha a webes alkalmazás a helyi gyorsítótár használ, akkor szüksége elérhetők legyenek a legutóbbi változtatások a webhely újraindítására. Nem kívánok változások közzétételére a munkakörnyezeti helyet? Lásd az előző bevált gyakorlatok szakaszban tárolóhely beállításokat.

### <a name="where-are-my-logs"></a>Hol találhatók a naplókat?
Helyi gyorsítótár a naplókat és az adatok mappák keressen eltérő. Azonban az almappák szerkezete változatlan marad, azzal a különbséggel, hogy az almappákat is nestled alatt kiegészítve a formátum "egyedi virtuális gép azonosítója" időbélyeg almappában.

### <a name="i-have-local-cache-enabled-but-my-web-app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Helyi gyorsítótár engedélyezve van, de a webes alkalmazás továbbra is lekérdezi újraindul. Az oka? I-re helyi gyorsítótár segített a gyakori alkalmazás újraindul.
Helyi gyorsítótár tárolással kapcsolatos webes alkalmazás újraindul megelőzése érdekében. Azonban a webalkalmazás sikerült továbbra is változni újraindítja a virtuális gép tervezett infrastruktúra frissítéskor. A teljes alkalmazás újraindul, hogy a hogy a helyi gyorsítótár engedélyezve kevesebb kell lennie.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>Helyi gyorsítótár kizárása könyvtárak a gyorsabb helyi meghajtóról történő másolását?
A tárház nevű mappát, amely a tárolási tartalmat másolja. a lépés részeként ki van zárva. Ezzel a megoldással esetén, ahol a webhely tartalmát a verziókövetési tárházat, amely nem lehet szükség a napi műveletet a webes alkalmazás is tartalmazhat. 
