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
ms.openlocfilehash: 11eb2e0363682d39a00a3f47cd3cc6c4badc040f
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086491"
---
# <a name="migrate-from-google-maps-to-azure-maps"></a>Migrálás a Google Mapsből a Azure Mapsba

Ebből az oktatóanyagból megtudhatja, hogyan telepíthet át webes, mobil-és Server-alapú alkalmazásokat a Google Mapsből a Microsoft Azure Maps platformra. Ez az oktatóanyag a Azure Mapsre való áttelepítéshez szükséges összehasonlító kód-mintákat, áttelepítési javaslatokat és ajánlott eljárásokat tartalmazza.

## <a name="azure-maps-platform-overview"></a>Azure Maps platform áttekintése

Azure Maps az összes iparágban hatékony térinformatikai képességeket biztosít a fejlesztőknek. A funkciók a webes és mobil alkalmazások földrajzi kontextusának biztosítása érdekében rendszeresen frissített térképi információkkal vannak becsomagolva. Azure Maps rendelkezik egy Azure-beli API-kompatibilis REST API-készlettel. Ezek a REST API-k a térképek renderelését, keresését, útválasztását, forgalmát, időzónáit, földrajzi elhelyezkedését, Geokerítések, leképezési adatokat, időjárási, mobilitási és térbeli műveleteit nyújtják. A műveletek a webes és az Android SDK-k együttes használatával könnyen, rugalmasan és több platformon is elérhetővé teszik a fejlesztést.

## <a name="high-level-platform-comparison"></a>Magas szintű platform-összehasonlítás

A táblázat a Google Maps szolgáltatásainak megfelelő Azure Maps-funkciók magas szintű listáját tartalmazza. Ez a lista nem jeleníti meg az összes Azure Maps funkciót. A további Azure Maps funkciók közé tartozik a kisegítő lehetőségek, a geokerítések API-k, a izokrón, a térbeli műveletek, a közvetlen Térkép csempe-hozzáférés, a Batch-szolgáltatások és az adatlefedettség-összehasonlítás (azaz a képek lefedettsége).

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
| Térinformatikai                 | N/A                                    |
| Helyek keresése               | ✓                                      |
| Helyek részletei              | N/A – A webhely & telefonszáma elérhető |
| Fényképek elhelyezése               | N/A                                    |
| Automatikus kiegészítés          | ✓                                      |
| Statikus térképek                 | ✓                                      |
| Statikus utcai nézet          | N/A                                    |
| Időzóna                   | ✓                                      |
| Maps Embedded API           | N/A                                    |
| Térkép URL-címei                    | N/A                                    |

A Google Maps alapszintű kulcs-alapú hitelesítést biztosít. Azure Maps az alapszintű kulcs-alapú hitelesítést és Azure Active Directory hitelesítést is biztosítja. Azure Active Directory több biztonsági funkcióval rendelkezik, mint az alapszintű kulcs-alapú hitelesítés.

## <a name="licensing-considerations"></a>Licencelési megfontolások

Ha a Google Maps szolgáltatásból Azure Mapsra végez áttelepítést, vegye figyelembe a következő szempontokat a licenceléssel kapcsolatban:.

- Az interaktív térképek használatának Azure Maps díja, amely a betöltött Térkép csempék számától függ. Másfelől a Google a Térkép vezérlőelem betöltésének díját is leképezi. Az interaktív Azure Maps SDK-k esetében a Térkép csempéi automatikusan gyorsítótárazva vannak, hogy csökkentsék a fejlesztési költségeket. Egy Azure Maps tranzakció jön létre minden betöltött 15 Térkép csempéhez. Az interaktív Azure Maps SDK-k 512 képpont méretű csempéket használnak, és átlagosan egy vagy kevesebb tranzakciót állítanak elő oldalanként.
- Gyakran költséghatékony, hogy a Google Maps webszolgáltatások statikus térképi képeit cserélje le a Azure Maps web SDK-val. A Azure Maps web SDK Térkép csempéket használ, és ha a felhasználó serpenyőben és nagyítja a térképet, a szolgáltatás gyakran csak egy tranzakció töredékét generálja a leképezési terhelésnek megfelelően. A Azure Maps web SDK-val lehetőség van a pásztázás és a nagyítás letiltására, ha szükséges. Emellett a Azure Maps web SDK sokkal több vizualizációs lehetőséget kínál, mint a statikus Térkép webszolgáltatása.
- Azure Maps lehetővé teszi, hogy a platformról származó adatok az Azure-ban legyenek tárolva. Emellett a használati [feltételeknek](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46)megfelelően akár hat hónapig is gyorsítótárazhatja az adatmennyiséget.

Íme néhány kapcsolódó erőforrás a Azure Mapshoz:

- [Azure Maps díjszabási oldala](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Azure-díjkalkulátor](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Azure Maps használati idő](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (a Microsoft Online Services használati feltételeiben szerepel)
- [Válassza ki a megfelelő díjszabási szintet Azure Maps](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Javasolt áttelepítési terv

A következő egy magas szintű áttelepítési terv.

1. Az alkalmazás által használt Google Maps SDK-k és szolgáltatások leltárba vétele. Ellenőrizze, hogy a Azure Maps biztosít-e alternatív SDK-kat és szolgáltatásokat.
2. Ha még nem rendelkezik ilyennel, hozzon létre egy Azure-előfizetést [https://azure.com](https://azure.com)címen.
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

## <a name="migration-support"></a>Áttelepítés támogatása

A fejlesztők a [fórumok](https://aka.ms/AzureMapsForums) vagy a számos Azure-támogatási lehetőség közül választhatják a Migrálás támogatását: [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan telepítheti át Google Maps-alkalmazását a következő cikkekkel:

> [!div class="nextstepaction"]
> [Webalkalmazás migrálása](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Android-alkalmazás migrálása](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [Webszolgáltatás migrálása](migrate-from-google-maps-web-services.md)
