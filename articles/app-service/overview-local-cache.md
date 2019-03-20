---
title: Helyi gyorsítótár – áttekintés – az Azure App Service |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan lehet engedélyezni, átméretezése és az Azure App Service helyi gyorsítótár-szolgáltatás állapotának lekérdezése
services: app-service
documentationcenter: app-service
author: cephalin
manager: jpconnock
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
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 1d6e233509b50f0b03678f2e62267169d02133a1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57990766"
---
# <a name="azure-app-service-local-cache-overview"></a>Az Azure App Service helyi gyorsítótár – áttekintés

> [!NOTE]
> Helyi gyorsítótár nem támogatott a Function apps vagy a tárolóalapú App Service-alkalmazások, mint például a [Linuxon futó App Service](containers/app-service-linux-intro.md).


Azure App Service-tartalom az Azure Storage tárolja, és az illesztett be, a tartalommegosztás tartósan. Ez a kialakítás célja, hogy együttműködést a különböző alkalmazások és az alábbi attribútumok tartoznak:  

* A tartalom közösen használja az alkalmazást több virtuális gép (VM) példányát.
* A tartalom tartós, és a futó alkalmazások által módosíthatók.
* Naplófájlok és a diagnosztikai adatok fájlok az azonos megosztott tartalmat tartalmazó mappa alatt érhetők el.
* Új tartalom közzététele közvetlenül frissíti a tartalmat tartalmazó mappa. Azonnal megtekintheti ugyanahhoz a tartalomhoz az SCM webhely és a futó alkalmazás (általában néhány technológiák, például az ASP.NET, a kezdeményezni az alkalmazás újraindítása, néhány fájl módosításait a legújabb tartalmának beolvasása a).

Számos alkalmazás használja legalább az összes funkciót, míg egyes alkalmazások egyszerűen nagy teljesítményű, csak olvasható tartalomtárhoz konvertál, amely a magas rendelkezésre állású futtathatják. Ezek az alkalmazások is kihasználhatják a Virtuálisgép-példány egy adott helyi gyorsítótár.

Az Azure App Service helyi gyorsítótára szolgáltatást a tartalom webes szerepkör nézetét jeleníti meg. Ez a tartalom a tároló tartalmának aszinkron módon helyszíni indítási létrehozott írási – de – elvetési gyorsítótár. Ha a gyorsítótár elkészült, a hely át lett váltva a gyorsítótárazott tartalom futtatásához. Helyi gyorsítótár rendszeren futtatott alkalmazások rendelkeznek a következő előnyökkel jár:

* Azok a védett, az Azure Storage tartalmakhoz férhetnek előforduló késéseket.
* Azok a tervezett frissítések vagy nem tervezett leállás és bármely más üzemzavarokhoz vezethet az Azure Storage, amelyek a tartalommegosztás, kiszolgáló-kiszolgáló védett.
* Tároló megosztás módosításai miatt kevesebb alkalmazás-újraindítások rendelkeznek.

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>Hogyan az a helyi gyorsítótár módosítja az App Service működését
* _D:\home_ mutat, a helyi gyorsítótár, amely a Virtuálisgép-példányon jön létre, az alkalmazás indulásakor. _D:\Local_ továbbra is a Virtuálisgép-specifikus ideiglenes tároló mutat.
* A helyi gyorsítótár egyszeri másolatát tartalmazza a _/Helykonfiguráció_ és _/siteextensions_ mappákat a megosztott tartalom tároló, _D:\home\site_ és _D:\home\ siteextensions_, illetve. A Másolás a helyi gyorsítótárba az alkalmazás indulásakor. Az egyes alkalmazások a két mappa mérete alapértelmezés szerint 300 MB-ra korlátozott, de legfeljebb 2 GB lehet növelni.
* A helyi gyorsítótár nem írható-olvasható. Azonban bármilyen módosítás elvész az alkalmazást áthelyezi a virtuális gépek, illetve újraindításakor beolvasása. Ne használja a helyi gyorsítótár alkalmazásokat, amelyek kritikus fontosságú adatok tárolása a tartalomtárhoz konvertál.
* _D:\home\LogFiles_ és _D:\home\Data_ tartalmazza a naplófájlok és az alkalmazásadatokat. A két almappát a rendszer helyben tárolja a Virtuálisgép-példányon, és másolja a megosztott tartalomtároló rendszeres időközönként. Alkalmazások ezeket a mappákat írásával megőrizheti a naplófájlokat és az adatok. Azonban a Másolás a megosztott tartalomtároló, legjobb, így lehetséges, hogy a naplófájlok és az adatok egy VM-példány egy hirtelen összeomlása miatt megszakadt.
* [A naplóstreamelés](troubleshoot-diagnostic-logs.md#streamlogs) hatással van a legjobb másolatot. Sikerült a adatfolyamként továbbított naplók perces késleltetés akár figyelje meg.
* A megosztott tartalom tárolóban változás történik a mappastruktúra a _LogFiles_ és _adatok_ mappákat a helyi gyorsítótárat használó alkalmazások esetében. Nincsenek most almappák neki, hogy az "egyedi azonosító" + időbélyeg elnevezési mintát. Egyes, több a almappával felel meg egy Virtuálisgép-példánnyal, ahol az alkalmazás fut, vagy futott.
* Más, a mappa _D:\home_ a helyi gyorsítótárban maradjon, és nem másolódnak át a megosztott tartalomtároló.
* Alkalmazás üzembe helyezése bármely támogatott módszerrel közvetlenül a tartós megosztott tartalomtároló tesz közzé. Frissítése az _D:\home\site_ és _D:\home\siteextensions_ mappák a helyi gyorsítótárban, az alkalmazásnak újra kell indítani. Ahhoz, hogy az életciklus zökkenőmentes, olvassa el a cikk későbbi részében.
* Az SCM helyet tartalom alapértelmezett nézete továbbra is, hogy a megosztott tartalom tárolóján.

## <a name="enable-local-cache-in-app-service"></a>Az App Service-ben a helyi gyorsítótár engedélyezése
Helyi gyorsítótár az alkalmazás fenntartott beállítások segítségével konfigurálhatja. Ezek a beállítások alkalmazást konfigurálhatja a következő módszerekkel:

* [Azure Portal](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Helyi gyorsítótár konfigurálása az Azure portal használatával
<a name="Configure-Local-Cache-Portal"></a>

Ennek az alkalmazásbeállításnak használatával engedélyeznie a webalkalmazás-alkalmazásonként helyi gyorsítótár: `WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Azure-portál alkalmazás beállítások: Helyi gyorsítótár](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Helyi gyorsítótár konfigurálása az Azure Resource Manager használatával
<a name="Configure-Local-Cache-ARM"></a>

```json

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

## <a name="change-the-size-setting-in-local-cache"></a>Helyi gyorsítótár mérete beállításainak módosítása
Alapértelmezés szerint a helyi gyorsítótár mérete a **300 MB**. Ez magában foglalja a /site és a tartalom tárból másolt /siteextensions mappák, valamint a helyileg létrehozott naplók és adatok mappákat. Ez a korlát növeléséhez használja a Alkalmazásbeállítás `WEBSITE_LOCAL_CACHE_SIZEINMB`. A méret legfeljebb növelheti **2 GB-os** (2000 MB) alkalmazásonként.

## <a name="best-practices-for-using-app-service-local-cache"></a>App Service helyi gyorsítótára használatának ajánlott eljárásai
Azt javasoljuk, hogy a helyi gyorsítótár együtt használja a [átmeneti környezetek](../app-service/deploy-staging-slots.md) funkció.

* Adja hozzá a *kiemelt* Alkalmazásbeállítás `WEBSITE_LOCAL_CACHE_OPTION` értékkel `Always` , a **éles** tárolóhely. Ha használ `WEBSITE_LOCAL_CACHE_SIZEINMB`, is hozzáadhatja az üzemelési egy Beragadó beállítást.
* Hozzon létre egy **átmeneti** tárolóhely, és tegye közzé az előkészítési pont. Általában nem állít be az előkészítési pont egy zökkenőmentes buildelési és üzembe helyezése-tesztelési életciklus átmeneti, ha a helyi gyorsítótár előnyeit az éles üzembe helyezési pont engedélyezése a helyi gyorsítótár használatával.
* Tesztelje webhelyét, az előkészítési pont ellen.  
* Amikor készen áll, adja ki a [felcserélési művelet](../app-service/deploy-staging-slots.md#Swap) tárhelyek az átmeneti és éles környezet között.  
* Kiemelt beállítások közé tartozik a nevét és a tárolóhelyet a kiemelt. Így átváltásakor a előkészítési pont lekérdezi éles környezetben, azt örökli a helyi gyorsítótár beállításokat. Az újonnan éles üzembe helyezési pont átváltva a helyi gyorsítótárban fog futni, néhány perc múlva, illetve fog kell bemelegíteni tárolóhely melegítési részeként lapozófájl-kapacitás után. Ezért a tárolóhelycsere befejeződése után az üzemelési fut a helyi gyorsítótárban.

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)
### <a name="how-can-i-tell-if-local-cache-applies-to-my-app"></a>Hogyan állapítható meg, ha a helyi gyorsítótárral a saját alkalmazásra érvényes?
Ha az alkalmazás szüksége van egy nagy teljesítményű, megbízható tartalomtárhoz konvertál, nem használja a tartalomtároló futásidőben kritikus fontosságú adatokat írni, és teljes mérete 2 GB-nál kevesebb, akkor a válasz a "yes" használata! A /site és /siteextensions mappa amelyek összméretén lekéréséhez használhatja a webhelybővítmény "Az Azure Web Apps lemezhasználat."

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Hogyan állapítható meg, ha a webhelyem változott-e a helyi gyorsítótár használatával?
Az átmeneti környezetek használata a helyi gyorsítótár-szolgáltatás, a csereművelet végrehajtásához helyi gyorsítótár bemelegíteni van. Ellenőrizze, hogy a webhely fut-e helyi gyorsítótárban, ellenőrizheti a munkavégző folyamat környezeti változó `WEBSITE_LOCALCACHE_READY`. Kövesse az utasításokat a a [munkavégző folyamat környezeti változó](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) lap eléréséhez a munkavégző folyamat környezeti változó több példányon.  

### <a name="i-just-published-new-changes-but-my-app-does-not-seem-to-have-them-why"></a>Éppen most közzétett új módosításokat, de az alkalmazás nem úgy tűnik, hogy azokat. Hogy miért?
Ha az alkalmazás a helyi gyorsítótárat használ, majd szeretné újraindítja a helyet, a legutóbbi változtatásokat beolvasásához. Nem a módosítások közzététele éles helyhez szeretne? Lásd az előző ajánlott eljárások szakaszban tárolóhely-beállításokat.

### <a name="where-are-my-logs"></a>Hol találhatók a naplókat?
A helyi gyorsítótár a naplók és adatok mappák máshogy néznek ki egy kicsit. Azonban az almappák szerkezete ugyanaz marad, azzal a különbséggel, hogy az almappákat is nestled alatt egy almappát a formátum "egyedi virtuális gép azonosítója" + időbélyeg.

### <a name="i-have-local-cache-enabled-but-my--app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Helyi gyorsítótár engedélyezve van, de az alkalmazás továbbra is lekérdezi újraindul. Ez miért van? E úgy Gondoltuk, helyi gyorsítótár segítségével a gyakori alkalmazás újraindul.
Helyi gyorsítótár storage szolgáltatással kapcsolatos alkalmazás-újraindítások megelőzése érdekében. Azonban az alkalmazás sikerült továbbra is mennek keresztül újraindul a virtuális gép tervezett, infrastruktúra-frissítések során. A teljes alkalmazás újraindul, hogy a hogy a helyi gyorsítótár engedélyezve kell lennie kevesebb.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>Helyi gyorsítótár kizárandó könyvtárak másolását a gyorsabb helyi meghajtó a?
A lépés, amely átmásolja a storage tartalom részeként ki van zárva minden tárház nevű mappát. Ez segít a forgatókönyvekhez, ahol a webhely tartalmát tartalmazhat egy verziókövetési adattár, amely nem lehet szükség a napi művelet az alkalmazás. 
