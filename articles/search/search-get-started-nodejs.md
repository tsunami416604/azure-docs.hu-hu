---
title: Ismerkedés az Azure Search node.js-szel – Azure Search szolgáltatással
description: Keresőalkalmazás felépítési útmutatója az Azure egy üzemeltetett felhőalapú keresőszolgáltatásához a Node.js programozási nyelv használatával.
author: jj09
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/26/2017
ms.author: jjed
ms.custom: seodec2018
ms.openlocfilehash: 0b36ee9aa54f0d8dd3d246d6c5a96e767326d2d8
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53634677"
---
# <a name="get-started-with-azure-search-in-nodejs"></a>Bevezetés az Azure Search használatába Node.js-ben
> [!div class="op_single_selector"]
> * [Portál](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

Ismerje meg, hogyan hozhat létre olyan egyéni Node.js-keresőalkalmazást, amely az Azure Search szolgáltatást használja a keresésekhez. Ez az oktatóanyag az [Azure Search szolgáltatás REST API](https://msdn.microsoft.com/library/dn798935.aspx)-ját használja ebben a gyakorlatban az objektumok és műveletek összeállításához.

A kód fejlesztéséhez és teszteléséhez a [Node.js](https://Nodejs.org)-t, valamint Windows 8.1 rendszeren az NPM-et, a [Sublime Text 3](https://www.sublimetext.com/3)-at és a Windows PowerShellt használtuk.

A minta futtatásához rendelkeznie kell egy Azure Search-szolgáltatással, amelyre az [Azure Portalon](https://portal.azure.com) regisztrálhat. A részletes utasításokat lásd: [Azure Search szolgáltatás létrehozása a portálon](search-create-service-portal.md).

## <a name="about-the-data"></a>Tudnivalók az adatokról
A mintaalkalmazás az [Amerikai Egyesült Államok geológiai szolgáltatásainak (United States Geological Services, USGS)](https://geonames.usgs.gov/domestic/download_data.htm) adatait használja, az adatkészlet méretének csökkentése érdekében Rhode Island államra szűrve. Ezeket az adatokat fogjuk használni egy olyan keresőalkalmazás létrehozásához, amely jellegzetes épületeket, például kórházakat és iskolákat, valamint geológiai jellegzetességeket, például folyókat, tavakat és hegycsúcsokat ad vissza eredményül.

Ebben az alkalmazásban a **DataIndexer** program egy [indexelő](https://msdn.microsoft.com/library/azure/dn798918.aspx) szerkezet segítségével létrehozza és betölti az indexet, amelyhez egy nyilvános Azure SQL-adatbázisból kéri le a szűrt USGS-adatkészletet. A hitelesítő adatokat és az online adatforrás csatlakozási adatait a programkód tartalmazza. Nincs szükség további konfigurációra.

> [!NOTE]
> Az adatkészlethez olyan szűrőt alkalmaztunk, hogy az ingyenes tarifacsomag 10 000 dokumentumos korlátja alatt maradjunk. Ha a standard csomagot használja, arra nem vonatkozik ez a korlátozás. Az egyes tarifacsomagok kapacitásával kapcsolatos részletes információkat lásd: [A Search szolgáltatásra vonatkozó korlátozások](search-limits-quotas-capacity.md).
> 
> 

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Az Azure Search szolgáltatás szolgáltatásnevének és API-kulcsának megkeresése
Miután létrehozta a szolgáltatást, térjen vissza a portálra az URL-cím vagy az `api-key` beolvasása érdekében. A keresőszolgáltatáshoz való kapcsolódáshoz szükséges, hogy a hívás hitelesítéséhez az URL-cím és egy `api-key` is a rendelkezésére álljon.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A gyorselérési sávon kattintson a **Keresési szolgáltatás** elemre, hogy megjelenjen az előfizetéséhez kapcsolódó összes Azure Search-szolgáltatás.
3. Válassza ki a használni kívánt szolgáltatást.
4. A szolgáltatás irányítópultján meg kell jelenniük az alapvető információkat tartalmazó csempéknek, például az adminisztrációs kulcsok eléréséhez szükséges kulcs ikonnak.
5. Másolja át a szolgáltatás URL-címét, egy adminisztrációs kulcsot és egy lekérdezési kulcsot. Mindháromra később lesz szüksége, amikor hozzáadja őket a config.js fájlhoz.

## <a name="download-the-sample-files"></a>A mintafájlok letöltése
A minta letöltéséhez a következő módszerek bármelyikét használhatja.

1. Lépjen a következőhöz: [search-node-indexer-demo](https://github.com/Azure-Samples/search-node-indexer-demo).
2. Kattintson a **Download ZIP** (ZIP-fájl letöltése) elemre, mentse el a .zip-fájlt, és bontsa ki a benne található összes fájlt.

Minden további fájlmódosítás és utasításfuttatás az ebben a mappában lévő fájlokra vonatkozóan történik.

## <a name="update-the-configjs-with-your-search-service-url-and-api-key"></a>Frissítse a config.js fájlt. a keresőszolgáltatása URL-címével és API-kulcsával
A már korábban átmásolt URL-címmel és API-kulccsal a konfigurációs fájlban adja meg az URL-címet, az adminisztrációs kulcsot és a lekérdezési kulcsot.

Az adminisztrációs kulcsok teljes körű vezérlést biztosítanak a szolgáltatási műveletek felett, beleértve az index létrehozását vagy törlését, illetve a dokumentumok betöltését. Ezzel szemben a lekérdezési kulcsok a csak olvasható műveletekhez, jellemzően az Azure Search szolgáltatáshoz kapcsolódó ügyfélalkalmazásokhoz használhatók.

Ebben a példában a lekérdezési kulcsot arra használjuk, hogy segítsen megerősíteni a lekérdezési kulcs ügyfélalkalmazásokban történő használatának ajánlott eljárását.

Az alábbi képernyőfelvételen egy szövegszerkesztőben megnyitott **config.js** fájl látható, és a megfelelő bejegyzések meg vannak jelölve, hogy tudja, hol frissítse a fájlt a keresőszolgáltatása számára érvényes értékekkel.

![][5]

## <a name="host-a-runtime-environment-for-the-sample"></a>A minta futtatókörnyezetének üzemeltetése
A mintához HTTP-kiszolgáló szükséges, amelyet az npm segítségével globálisan telepíthet.

A következő parancsokhoz használjon egy PowerShell-ablakot.

1. Keresse meg a **package.json** fájlt tartalmazó mappát.
2. Gépelje be: `npm install`.
3. Gépelje be: `npm install -g http-server`.

## <a name="build-the-index-and-run-the-application"></a>Az index létrehozása és az alkalmazás futtatása
1. Gépelje be: `npm run indexDocuments`.
2. Gépelje be: `npm run build`.
3. Gépelje be: `npm run start_server`.
4. Irányítsa a böngészőt a következő helyre: `http://localhost:8080/index.html`

## <a name="search-on-usgs-data"></a>USGS-adatok keresése
Az USGS-adatkészlet a Rhode Island államra vonatkozó rekordokat tartalmaz. Ha rákattint egy üres keresőmező **Keresés** gombjára, megjelenik az 50 legfontosabb bejegyzés; ez az alapértelmezett viselkedés.

A keresett kifejezés beírása elindítja a keresőmotort. Próbáljon meg a helyhez kötődő nevet beírni. „Roger Williams” volt Rhode Island első kormányzója. Számos parkot, épületet és iskolát neveztek el róla.

![][9]

Megpróbálhatja beírni az alábbi kifejezések bármelyikét is:

* Pawtucket
* Pembroke
* goose+cape

## <a name="next-steps"></a>További lépések
Ez az Azure Search első oktatóanyaga, amely a Node.js és a USGS-adatkészlet alapján készült. Idővel majd tovább bővítjük oktatóanyagunkat, és olyan kiegészítő keresési funkciókat fogunk bemutatni, amelyeket esetleg szívesen használna egyéni megoldásaiban.

Ha már rendelkezik bizonyos tapasztalattal az Azure Search szolgáltatásban, használhatja ezt a mintát, ugródeszkaként kipróbálta a javaslattevők (előre vagy automatikusan kiegészített lekérdezések), a szűrők és a jellemzőalapú navigáció. A keresési eredmények oldalát is tovább fejlesztheti számok és kötegelt dokumentumok hozzáadásával úgy, hogy a felhasználók lapozhassanak az eredmények között.

Mik az Azure Search újdonságai? Azt javasoljuk, próbáljon ki más oktatóanyagokat is, hogy jobban megismerhesse, mit hozhat létre. További forrásokat a [dokumentációs oldalunkon](https://azure.microsoft.com/documentation/services/search/) talál. 

<!--Image references-->
[1]: ./media/search-get-started-Nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-Nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-Nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-Nodejs/AzSearch-Nodejs-configjs.png
[9]: ./media/search-get-started-Nodejs/rogerwilliamsschool.png
