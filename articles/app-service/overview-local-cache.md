---
title: Helyi gyorsítótár
description: Ismerje meg, hogyan működik a helyi gyorsítótár az Azure App Service-ben, és hogyan engedélyezheti, méretezheti át és kérdezheti le az alkalmazás helyi gyorsítótárának állapotát.
tags: optional
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.topic: article
ms.date: 03/04/2016
ms.custom: seodec18
ms.openlocfilehash: 1945730acaddb0c1c7ee1b28eeb926635efad643
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78227877"
---
# <a name="azure-app-service-local-cache-overview"></a>Az Azure App Service helyi gyorsítótárának áttekintése

> [!NOTE]
> A helyi gyorsítótár nem támogatott a Függvényalkalmazásokban vagy a tárolóba helyezett App Service-alkalmazásokban, például [a Windows-tárolókban](app-service-web-get-started-windows-container.md) vagy [a Linuxon lévő App Service szolgáltatásban.](containers/app-service-linux-intro.md)


Az Azure App Service-tartalom az Azure Storage-ban tárolódik, és tartós módon, tartalommegosztásként jelenik meg. Ez a kialakítás különböző alkalmazásokkal működik, és a következő attribútumokkal rendelkezik:  

* A tartalom az alkalmazás több virtuálisgép-példányában van megosztva.
* A tartalom tartós, és alkalmazások futtatásával módosítható.
* A naplófájlok és a diagnosztikai adatfájlok ugyanabban a megosztott tartalommappában érhetők el.
* Az új tartalom közzététele közvetlenül frissíti a tartalommappát. Azonnal megtekintheti ugyanazt a tartalmat az SCM webhelyen és a futó alkalmazáson keresztül (általában bizonyos technológiák, például ASP.NET kezdeményeznek egy alkalmazás újraindítását bizonyos fájlmódosításokon a legújabb tartalom lefelvételéhez).

Bár sok alkalmazás használja az egyik vagy az összes ilyen funkciót, néhány alkalmazásnak csak egy nagy teljesítményű, csak olvasható tartalomtárra van szüksége, amelyből magas rendelkezésre állással futtathatók. Ezek az alkalmazások egy adott helyi gyorsítótár virtuálisgép-példányának előnyeit élvezhetik.

Az Azure App Service helyi gyorsítótára szolgáltatás webes szerepkörrel látja el a tartalmat. Ez a tartalom a tárolási tartalom aszinkron módon a helyszíni indításkor létrehozott gyorsítótára. Amikor a gyorsítótár készen áll, a hely átvált a gyorsítótárazott tartalomhoz. A helyi gyorsítótárban futó alkalmazások a következő előnyökkel járnak:

* Ezek immunisak a késések, amelyek akkor jelentkeznek, amikor az Azure Storage-tartalom elérésekor.
* Ezek immunisak a tervezett frissítések vagy nem tervezett állásidők és az Azure Storage- használatával kapcsolatos egyéb megszakításokra, amelyek a tartalommegosztást kiszolgáló kiszolgálókon fordulnak elő.
* Kevesebb alkalmazás-újraindítással rendelkeznek a tárolási megosztás módosításai miatt.

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>Hogyan változtatja meg a helyi gyorsítótár az App Service működését?
* _D:\home_ a helyi gyorsítótárra mutat, amely az alkalmazás indításakor jön létre a virtuálisgép-példányon. _D:\a helyi_ továbbra is az ideiglenes virtuálisgép-specifikus tárolóra mutat.
* A helyi gyorsítótár a megosztott tartalomtár _/site_ and _/siteextensions_ mappáinak egyszeri másolatát tartalmazza a _D:\home\site_ és _a D:\home\siteextensions_mappában. A fájlok at az alkalmazás indításakor a helyi gyorsítótárba másolja a rendszer. Az egyes alkalmazások két mappájának mérete alapértelmezés szerint legfeljebb 300 MB lehet, de akár 2 GB-ra is növelhető. Ha a másolt fájlok mérete meghaladja a helyi gyorsítótár méretét, az App Service csendben figyelmen kívül hagyja a helyi gyorsítótárat, és a távoli fájlmegosztásból olvas.
* A helyi gyorsítótár írási és olvasási. Azonban minden módosítás elvetésre kerül, amikor az alkalmazás áthelyezi a virtuális gépeket, vagy újraindul. Ne használja a helyi gyorsítótárat olyan alkalmazásokhoz, amelyek kritikus fontosságú adatokat tárolnak a tartalomtárolóban.
* _D:\home\LogFiles_ és _D:\home\Az adatok_ naplófájlokat és alkalmazásadatokat tartalmaznak. A két almappa helyileg tárolódik a virtuálisgép-példányon, és rendszeresen átmásolja őket a megosztott tartalomtárolóba. Az alkalmazások megőrizhetik a naplófájlokat és az adatokat, ha ezekbe a mappákba írják őket. Azonban a másolat a megosztott tartalomtárolóba a legjobb erőfeszítés, így lehetséges, hogy a naplófájlok és az adatok elvesznek a virtuális gép példányának hirtelen összeomlása miatt.
* [A naplóstreamelést](troubleshoot-diagnostic-logs.md#stream-logs) a legjobb anamnézisű példány befolyásolja. Megfigyelheti, hogy akár egy perces késés a streamelt naplók.
* A megosztott tartalomtárolóban a _helyi gyorsítótárat_ használó alkalmazások Naplófájlok és _Adat_ mappák mappaszerkezete megváltozik. Most már almappák vannak bennük, amelyek az "egyedi azonosító" + időbélyegző elnevezési mintáját követik. Minden almappa egy virtuálisgép-példánynak felel meg, ahol az alkalmazás fut vagy fut.
* A _D:\home_ többi mappája a helyi gyorsítótárban marad, és nem kerül a megosztott tartalomtárolóba.
* Az alkalmazás központi telepítése bármely támogatott módszeren keresztül közvetlenül a tartós megosztott tartalomtárban teszi közzé. A helyi gyorsítótárBan található _D:\home\site\siteextensions_ mappák frissítéséhez az alkalmazást újra kell indítani. _D:\home\siteextensions_ Az életciklus zökkenőmentessé tételéhez tekintse meg a cikk későbbi részében található információkat.
* Az SCM-webhely alapértelmezett tartalomnézete továbbra is a megosztott tartalomtár nézete.

## <a name="enable-local-cache-in-app-service"></a>Helyi gyorsítótár engedélyezése az App Service-ben
A helyi gyorsítótárat a fenntartott alkalmazásbeállítások kombinációjával konfigurálhatja. Ezeket az alkalmazásbeállításokat a következő módszerekkel konfigurálhatja:

* [Azure-portál](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Helyi gyorsítótár konfigurálása az Azure Portal használatával
<a name="Configure-Local-Cache-Portal"></a>

A helyi gyorsítótárat webalkalmazásonként engedélyezi ezzel az alkalmazásbeállítással:`WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Az Azure Portal alkalmazás beállításai: Helyi gyorsítótár](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

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

## <a name="change-the-size-setting-in-local-cache"></a>A helyi gyorsítótár méretbeállításának módosítása
Alapértelmezés szerint a helyi gyorsítótár mérete **300 MB**. Ez magában foglalja a /site és /siteextensions mappákat, amelyeket a tartalomtárból másolnak, valamint a helyileg létrehozott naplókat és adatmappákat. A korlát növeléséhez használja `WEBSITE_LOCAL_CACHE_SIZEINMB`az alkalmazásbeállítást. Alkalmazásonként akár **2 GB** (2000 MB) méret is növelhető.

## <a name="best-practices-for-using-app-service-local-cache"></a>Gyakorlati tanácsok az App Service helyi gyorsítótárának használatához
Azt javasoljuk, hogy használja a helyi gyorsítótár együtt átmeneti [környezetek](../app-service/deploy-staging-slots.md) szolgáltatás.

* Adja hozzá a `WEBSITE_LOCAL_CACHE_OPTION` *ragadós* `Always` alkalmazás beállítást az értékkel a **termelési** tárolóhelyhez. Ha a , `WEBSITE_LOCAL_CACHE_SIZEINMB`is add hozzá, mint egy ragacsos beállítást a termelési slot.
* Hozzon létre egy **átmeneti** tárolóhelyet, és tegye közzé az átmeneti tárolóhelyen. Általában nem állítja be az átmeneti tárolóhelyet a helyi gyorsítótár használatára, hogy zökkenőmentes build-deploy-teszt életciklust engedélyezze az előkészítéshez, ha az éles tárolóhely helyi gyorsítótárának előnyeit kapja.
* Tesztelje a webhelyet az átmeneti tárolóhelyével.  
* Ha készen áll, adjon ki egy [csereműveletet](../app-service/deploy-staging-slots.md#Swap) az átmeneti és a termelési tárolóhelyek között.  
* Sticky beállítások közé tartozik a név és ragadós, hogy egy slot. Így amikor az átmeneti tárolóhely éles környezetbe kerül, örökli a helyi gyorsítótár alkalmazás beállításait. Az újonnan cserélt termelési tárolóhely néhány perc múlva a helyi gyorsítótárral szemben fog futni, és a csere utáni nyílásbemelegedés részeként melegszik fel. Így amikor a tárolóhely-csere befejeződött, a termelési tárolóhely fut a helyi gyorsítótárban.

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

### <a name="how-can-i-tell-if-local-cache-applies-to-my-app"></a>Hogyan állapítható meg, hogy a helyi gyorsítótár vonatkozik-e az alkalmazásomra?
Ha az alkalmazásnak nagy teljesítményű, megbízható tartalomtárra van szüksége, nem használja a tartalomtárolót kritikus adatok futásidőben történő írására, és teljes mérete kevesebb, mint 2 GB, akkor a válasz "igen"! A /site és /siteextensions mappák teljes méretének lehívásához használja az "Azure Web Apps lemezhasználat" webhelybővítményt.

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Hogyan állapítható meg, hogy a webhelyem áttért-e a helyi gyorsítótár használatára?
Ha a helyi gyorsítótár szolgáltatást átmeneti környezetekkel használja, a lapozóművelet nem fejeződik be, amíg a helyi gyorsítótár be nem melegszik. Annak ellenőrzéséhez, hogy a webhely fut-e a helyi `WEBSITE_LOCALCACHE_READY`gyorsítótárban, ellenőrizheti a munkavégző folyamat környezeti változóját. A [munkavégző folyamat környezeti változólapján](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) található utasításokkal több példányon is elérheti a munkavégző folyamat környezeti változóját.  

### <a name="i-just-published-new-changes-but-my-app-does-not-seem-to-have-them-why"></a>Most tettem közzé az új módosításokat, de úgy tűnik, hogy az alkalmazásom nem rendelkezik velük. Hogy miért?
Ha az alkalmazás helyi gyorsítótárat használ, akkor újra kell indítania a webhelyet a legújabb módosítások leolvasásához. Nem szeretne módosításokat közzétenni egy éles területen? Tekintse meg a bővítőhely-beállításokat az előző ajánlott eljárások szakaszban.

### <a name="where-are-my-logs"></a>Hol vannak a naplóim?
A Helyi gyorsítótár segítségével a naplók és az adatmappák egy kicsit másképp néznek ki. Az almappák szerkezete azonban nem változott, azzal a különbséggel, hogy az almappák egy almappa alá vannak ágyazva, amelynek formátuma "egyedi virtuálisgép-azonosító" + időbélyegző.

### <a name="i-have-local-cache-enabled-but-my--app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>A helyi gyorsítótár engedélyezve van, de az alkalmazás továbbra is újraindul. Miért? Azt hittem, a helyi cache segített a gyakori app újraindul.
A helyi gyorsítótár segít megakadályozni a tárolással kapcsolatos alkalmazások újraindítását. Azonban az alkalmazás továbbra is újraindul a virtuális gép tervezett infrastruktúra-frissítések során. A helyi gyorsítótár engedélyezésével tapasztalt általános alkalmazás újraindul, amelynek engedélyezésével tapasztalkisebb.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>Kizárja a helyi gyorsítótár a könyvtárakat a gyorsabb helyi meghajtóra való másolásból?
A tárolótartalmat másoló lépés részeként a rendszer kizárja a tárház nevű mappákat. Ez segít olyan esetekben, amikor a webhely tartalma olyan forrásvezérlő tárházat tartalmazhat, amelyre előfordulhat, hogy nincs szükség az alkalmazás napi működésére. 
