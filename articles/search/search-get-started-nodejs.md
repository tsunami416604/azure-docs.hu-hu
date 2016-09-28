<properties
    pageTitle="Bevezetés az Azure Search használatába NodeJS nyelven |Microsoft Azure | Üzemeltetett felhőalapú keresőszolgáltatás"
    description="Ismerje meg egy keresőalkalmazás felépítésének lépéseit egy üzemeltetett felhőalapú keresőszolgáltatásban az Azure rendszerben, a NodeJS programozási nyelv használatával."
    services="search"
    documentationCenter=""
    authors="EvanBoyle"
    manager="pablocas"
    editor="v-lincan"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.date="07/14/2016"
    ms.author="evboyle"/>


# Bevezetés az Azure Search használatába NodeJS nyelven
> [AZURE.SELECTOR]
- [Portál](search-get-started-portal.md)
- [.NET](search-howto-dotnet-sdk.md)

Ismerje meg, hogyan hozhat létre olyan egyéni NodeJS keresőalkalmazást, amely az Azure Search szolgáltatást használja a keresésekhez. Ez az oktatóanyag az [Azure Search szolgáltatás REST API](https://msdn.microsoft.com/library/dn798935.aspx)-ját használja ebben a gyakorlatban az objektumok és műveletek összeállításához.

A kód kialakításához és teszteléséhez a következő eszközöket használjuk a Windows 8.1 operációs rendszerben: [NodeJS](https://nodejs.org) és NPM, [Sublime Text 3](http://www.sublimetext.com/3) és Windows PowerShell.

A minta futtatásához rendelkeznie kell egy Azure Search szolgáltatással, amelyre az [Azure portálon](https://portal.azure.com) regisztrálhat. A részletes utasításokat lásd: [Azure Search szolgáltatás létrehozása a portálon](search-create-service-portal.md).

## Tudnivalók az adatokról

A mintaalkalmazás az [Amerikai Egyesült Államok geológiai szolgáltatásainak (United States Geological Services, USGS)](http://geonames.usgs.gov/domestic/download_data.htm) adatait használja, az adatkészlet méretének csökkentése érdekében Rhode Island államra szűrve. Ezeket az adatokat fogjuk használni egy olyan keresőalkalmazás létrehozásához, amely jellegzetes épületeket, például kórházakat és iskolákat, valamint geológiai jellegzetességeket, például folyókat, tavakat és hegycsúcsokat ad vissza eredményül.

Ebben az alkalmazásban a **DataIndexer** program egy [indexelő](https://msdn.microsoft.com/library/azure/dn798918.aspx) szerkezet segítségével létrehozza és betölti az indexet, amelyhez egy nyilvános Azure SQL-adatbázisból kéri le a szűrt USGS-adatkészletet. A hitelesítő adatokat és az online adatforrás kapcsolódási adatait a programkód tartalmazza. Nincs szükség további konfigurációra.

> [AZURE.NOTE] Az adatkészlethez olyan szűrőt alkalmaztunk, hogy az ingyenes tarifacsomag 10 000 dokumentumos korlátja alatt maradjunk. Ha a standard csomagot használja, arra nem vonatkozik ez a korlátozás. Az egyes tarifacsomagok kapacitásával kapcsolatos részletes információkat lásd: [A Search szolgáltatásra vonatkozó korlátozások](search-limits-quotas-capacity.md).


<a id="sub-2"></a>
## Az Azure Search szolgáltatás szolgáltatásnevének és API-kulcsának megkeresése

Miután létrehozta a szolgáltatást, térjen vissza a portálra az URL-cím vagy az `api-key` beolvasása érdekében. A keresőszolgáltatáshoz való kapcsolódáshoz szükséges, hogy a hívás hitelesítéséhez az URL-cím és egy `api-key` is a rendelkezésére álljon.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Az ugrás sávon kattintson a **Keresési szolgáltatás** elemre, hogy megjelenjen az előfizetéséhez kapcsolódó összes Azure Search szolgáltatás.
3. Válassza ki a használni kívánt szolgáltatást.
4. A szolgáltatás irányítópultján megjelennek az alapvető információkat tartalmazó csempék, valamint az adminisztrációs kulcsok eléréséhez szükséges kulcs ikon.

    ![][3]

5. Másolja át a szolgáltatás URL-címét, egy adminisztrációs kulcsot és egy lekérdezési kulcsot. Később mind a háromra szüksége lesz, amikor hozzáadja őket a config.js fájlhoz.

## A mintafájlok letöltése

A minta letöltéséhez a következő módszerek bármelyikét használhatja.

1. Lépjen az [AzureSearchNodeJSIndexerDemo](https://github.com/AzureSearch/AzureSearchNodeJSIndexerDemo) elemre.
2. Kattintson a **Download ZIP** (ZIP-fájl letöltése) elemre, mentse el a .zip-fájlt, és bontsa ki a benne található összes fájlt.

Minden további fájlmódosítás és utasításfuttatás az ebben a mappában lévő fájlokra vonatkozóan fog történni.


## Frissítse a config.js fájlt. a keresőszolgáltatása URL-címével és API-kulcsával

A már korábban átmásolt URL-címmel és API-kulccsal a konfigurációs fájlban adja meg az URL-címet, az adminisztrációs kulcsot és a lekérdezési kulcsot.

Az adminisztrációs kulcsok teljes körű vezérlést biztosítanak a szolgáltatási műveletek felett, beleértve az index létrehozását vagy törlését, illetve a dokumentumok betöltését. Ezzel szemben a lekérdezési kulcsok a csak olvasható műveletekhez, jellemzően az Azure Search szolgáltatáshoz kapcsolódó ügyfélalkalmazásokhoz használhatók.

Ebben a példában a lekérdezési kulcsot arra használjuk, hogy segítsen megerősíteni a lekérdezési kulcs ügyfélalkalmazásokban történő használatának ajánlott eljárását.

Az alábbi képernyőfelvételen egy szövegszerkesztőben megnyitott **config.js** fájl látható, és a megfelelő bejegyzések meg vannak jelölve, hogy tudja, hol frissítse a fájlt a keresőszolgáltatása számára érvényes értékekkel.

![][5]


## A minta futtatókörnyezetének üzemeltetése

A mintához HTTP-kiszolgáló szükséges, amelyet az npm segítségével globálisan telepíthet.

A következő parancsokhoz használjon egy PowerShell-ablakot.

1. Keresse meg a **package.json** fájlt tartalmazó mappát.
2. Gépelje be: `npm install`.
2. Gépelje be: `npm install -g http-server`.

## Az index létrehozása és az alkalmazás futtatása

1. Gépelje be: `npm run indexDocuments`.
2. Gépelje be: `npm run build`.
3. Gépelje be: `npm run start_server`.
4. Irányítsa a böngészőt a következő helyre: `http://localhost:8080/index.html`

## USGS-adatok keresése

Az USGS-adatkészlet a Rhode Island államra vonatkozó rekordokat tartalmaz. Ha rákattint egy üres keresőmező **Search** (Keresés) gombjára, megjelenik az 50 legfontosabb bejegyzés; ez az alapértelmezett viselkedés.

A keresett kifejezés beírása elindítja a keresőmotort. Próbáljon meg a helyhez kötődő nevet beírni. „Roger Williams” volt Rhode Island első kormányzója. Számos parkot, épületet és iskolát neveztek el róla.

![][9]

Megpróbálhatja beírni az alábbi kifejezések bármelyikét is:

- Pawtucket
- Pembroke
- goose+cape


## Következő lépések

Ez az Azure Search első oktatóanyaga, amely NodeJS és USGS-adatkészlet alapján készült. Idővel majd tovább bővítjük oktatóanyagunkat, és olyan kiegészítő keresési funkciókat fogunk bemutatni, amelyeket esetleg szívesen használna egyéni megoldásaiban.

Ha már rendelkezik bizonyos tapasztalattal az Azure Search használatában, ezt a mintát akár ugródeszkaként is használhatja a javaslattevők (előre begépelt vagy automatikusan kitöltött lekérdezések), szűrők és a jellemzőalapú navigáció kipróbálásához. A keresési eredmények oldalát is tovább fejlesztheti számok és kötegelt dokumentumok hozzáadásával úgy, hogy a felhasználók lapozhassanak az eredmények között.

Mik az Azure Search újdonságai? Azt javasoljuk, próbáljon ki más oktatóanyagokat is, hogy jobban megismerhesse, mit hozhat létre. További forrásokat a [dokumentációs oldalunkon](https://azure.microsoft.com/documentation/services/search/) talál. További információkat szerezhet, ha megtekinti a [Videók és oktatóanyagok listáját](search-video-demo-tutorial-list.md).

<!--Image references-->
[1]: ./media/search-get-started-nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-nodejs/AzSearch-NodeJS-configjs.png
[9]: ./media/search-get-started-nodejs/rogerwilliamsschool.png



<!--HONumber=Sep16_HO4-->


