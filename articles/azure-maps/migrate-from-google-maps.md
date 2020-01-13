---
title: 'Oktatóanyag: Migrálás a Google Maps szolgáltatásból a Azure Mapsba | Microsoft Azure térképek'
description: Útmutató a Google Maps szolgáltatásból Microsoft Azure Maps-re való áttelepítéshez. Az útmutató végigvezeti a Azure Maps API-k és SDK-k átváltásának lépésein.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 0e841b1f386d45ddb4af8598855d8e739750307e
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910738"
---
# <a name="migrate-from-google-maps-to-azure-maps"></a>Migrálás a Google Mapsből a Azure Mapsba

Ebből az oktatóanyagból megtudhatja, hogyan telepíthet át webes, mobil-és Server-alapú alkalmazásokat a Google Mapsből a Microsoft Azure Maps platformra. Ez az oktatóanyag a Azure Mapsre való áttelepítéshez szükséges összehasonlító kód-mintákat, áttelepítési javaslatokat és ajánlott eljárásokat tartalmazza.

## <a name="azure-maps-platform-overview"></a>Azure Maps platform áttekintése

Azure Maps az összes iparágban hatékony térinformatikai képességekkel rendelkező fejlesztőket biztosít, amelyek a rendszeresen frissített térképi adatokkal rendelkeznek a webes és mobil alkalmazások földrajzi kontextusának biztosításához. A Azure Maps az Azure egy API-kompatibilis REST API-készlettel rendelkezik a Maps, a Search, az Útválasztás, a Traffic, az időzóna, a földrajzi hely, a Geokerítések, az adatok leképezése, az időjárási, a mobilitási és a térbeli műveletekhez, valamint a webes és az Android SDK-k révén a fejlesztés egyszerű, rugalmas és több platformon is hordozható.

## <a name="high-level-platform-comparison"></a>Magas szintű platform-összehasonlítás

Az alábbi táblázat a Google Maps szolgáltatásainak magas szintű listáját és a Azure Maps szolgáltatásainak relatív támogatását tartalmazza. Ez a lista nem tartalmaz olyan további Azure Maps szolgáltatásokat, mint a kisegítő lehetőségek, a geokerítések API-k, a izokrón, a térbeli műveletek, a közvetlen Térkép csempe-hozzáférés, a kötegelt szolgáltatások és az adatlefedettség-összehasonlítás (például a képkezelési lefedettség).

| Google Maps szolgáltatás         | Azure Maps támogatás                     |
|-----------------------------|:--------------------------------------:|
| Web SDK                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | Tervezve                                |
| REST-szolgáltatás API-k           | ✓                                      |
| Irányok (Útválasztás)        | ✓                                      |
| Távolsági mátrix             | ✓                                      |
| Jogosultságszint                   | Tervezve                                |
| Helymeghatározáshoz (előre/hátra) | ✓                                      |
| Földrajzi hely                 | –                                    |
| Helyek keresése               | ✓                                      |
| Helyek részletei              | N/A – A webhely & telefonszáma elérhető |
| Fényképek elhelyezése               | –                                    |
| Automatikus kiegészítés          | ✓                                      |
| Statikus térképek                 | ✓                                      |
| Statikus utcai nézet          | –                                    |
| Időzóna                   | ✓                                      |
| Maps Embedded API           | –                                    |
| Térkép URL-címei                    | –                                    |

A Google Maps alapszintű kulcs-alapú hitelesítést biztosít. Azure Maps az alapszintű kulcs-alapú hitelesítést, valamint a nagyon biztonságos Azure Active Directory hitelesítést is biztosítja.

## <a name="licensing-considerations"></a>Licencelési megfontolások

Ha a Google Maps szolgáltatásból Azure Mapsra végez áttelepítést, a következő szempontokat kell figyelembe venni a licencelés tekintetében.

- Az interaktív leképezések használatának Azure Maps díja a betöltött térképi csempék száma alapján, míg a Google Maps a Térkép vezérlőelem betöltésének díját számítja fel. Az interaktív Azure Maps SDK-k esetében a Térkép csempék automatikusan gyorsítótárazva vannak, hogy csökkentse a fejlesztő költségeit. Egy Azure Maps tranzakció jön létre minden betöltött 15 Térkép csempéhez. Az interaktív Azure Maps SDK-k 512 képpont méretű csempéket használnak, és átlagosan egy vagy kevesebb tranzakciót állítanak elő oldalanként.
- Gyakran sokkal költséghatékonyabb, hogy a Google Maps webszolgáltatások statikus térképi képeit cserélje le a Azure Maps web SDK-ra, mivel ez a Térkép csempéket használja, kivéve, ha a felhasználó serpenyőbe helyezi és nagyítja a térképet, gyakran csak egy tranzakció töredékét eredményezi. A Azure Maps web SDK-val lehetőség van a pásztázás és a nagyítás letiltására. Emellett a Azure Maps web SDK sokkal több vizualizációs lehetőséget kínál, mint a statikus Térkép webszolgáltatás.
- Azure Maps lehetővé teszi, hogy a platformról származó adatok az Azure-ban legyenek tárolva. Ezen kívül a használati [feltételek](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46)alapján legfeljebb hat hónapig lehet gyorsítótárazni.

Íme néhány kapcsolódó erőforrás a Azure Mapshoz:

- [Azure Maps díjszabási oldala](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Azure-díjkalkulátor](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Azure Maps használati idő](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (a Microsoft Online Services használati feltételeiben szerepel)
- [Válassza ki a megfelelő díjszabási szintet Azure Maps](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Javasolt áttelepítési terv

A következő egy magas szintű áttelepítési terv.

1. Készítsen leltárt arról, hogy az alkalmazás milyen Google Maps SDK-kat és szolgáltatásokat használ, és ellenőrizze, hogy Azure Maps biztosít-e alternatív SDK-kat és-szolgáltatásokat, hogy áttelepítsen.
2. Hozzon létre egy Azure-előfizetést (ha még nem rendelkezik ilyennel) a következő címen: [https://azure.com](https://azure.com).
3. Hozzon létre egy Azure Maps fiókot ([dokumentációt](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)) és hitelesítési kulcsot, vagy Azure Active Directory ([dokumentáció](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)).
4. Telepítse át az alkalmazás kódját.
5. Tesztelje az áttelepített alkalmazást.
6. Telepítse az áttelepített alkalmazást éles környezetbe.

## <a name="azure-maps-technical-resources"></a>Technikai erőforrások Azure Maps

Az alábbi lista a Azure Maps hasznos technikai erőforrásait sorolja fel.

- Áttekintés: [https://azure.com/maps](https://azure.com/maps)
- Dokumentáció: [https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Web SDK-kód minták: [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Fejlesztői fórumok: [https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- Videók: [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Blog: [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Azure Maps visszajelzés (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)

## <a name="migration-support"></a>Migrálás támogatása

A fejlesztők a [fórumok](https://aka.ms/AzureMapsForums) vagy a számos Azure-támogatási lehetőség közül választhatják a Migrálás támogatását: [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan telepítheti át Google Maps-alkalmazását a következő cikkekkel:

> [!div class="nextstepaction"]
> [Webalkalmazás migrálása](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Android-alkalmazás migrálása](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [Webszolgáltatás migrálása](migrate-from-google-maps-web-services.md)
