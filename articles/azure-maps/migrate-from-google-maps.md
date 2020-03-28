---
title: 'Oktatóanyag: Áttelepítés a Google Térképről az Azure Mapsbe | Microsoft Azure Maps'
description: Útmutató a Google Térképről a Microsoft Azure Mapsszolgáltatásba való áttelepítésről. Útmutató bemutatja, hogyan válthat az Azure Maps API-kra és Az SDK-kra.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 7b73923b7fc32ae83bfc8405d074835c02031a63
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77913701"
---
# <a name="migrate-from-google-maps-to-azure-maps"></a>Áttelepítés a Google Térképről az Azure Mapsbe

Ez az oktatóanyag betekintést nyújt a webes, mobil- és kiszolgálóalapú alkalmazások Google Térképről a Microsoft Azure Maps platformra történő áttelepítésébe. Ez az oktatóanyag összehasonlító kódmintákat, áttelepítési javaslatokat és az Azure Mapsbe való áttelepítésajánlott eljárásokat tartalmaz.

## <a name="azure-maps-platform-overview"></a>Az Azure Maps platform áttekintése

Az Azure Maps minden iparág fejlesztőiszámára hatékony térinformatikai lehetőségeket biztosít. A képességek rendszeresen frissített térképadatokkal vannak eltikelve, hogy földrajzi környezetet biztosítsanak a webes és mobilalkalmazások számára. Az Azure Maps rendelkezik egy Azure One API-kompatibilis REST API-készlet. A REST API-k térképrenderelést, keresést, útválasztást, forgalmat, időzónákat, földrajzi elhelyezést, geokerítést, térképadatokat, időjárást, mobilitást és térbeli műveleteket kínálnak. A műveleteket webes és Android SDK-k is kísérik, hogy a fejlesztés egyszerű, rugalmas és hordozható legyen több platformon.

## <a name="high-level-platform-comparison"></a>Magas szintű platform-összehasonlítás

A táblázat az Azure Maps funkcióinak magas szintű listáját tartalmazza, amelyek megfelelnek a Google Térkép funkcióinak. Ez a lista nem jeleníti meg az Azure Maps összes funkcióját. Az Azure Maps további funkciói a következők: hozzáférhetőség, geofencing, iochronok, térbeli műveletek, közvetlen térképcsempe-hozzáférés, kötegelt szolgáltatások és adatlefedettség-összehasonlítások (azaz képlefedettség).

| A Google Térkép funkció         | Az Azure Maps támogatása                     |
|-----------------------------|:--------------------------------------:|
| Web SDK                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | Tervezve                                |
| REST szolgáltatás API-k           | ✓                                      |
| Útvonalterv (Útválasztás)        | ✓                                      |
| Távolságmátrix             | ✓                                      |
| Magasság                   | Tervezve                                |
| Geokódolás (előre/hátra) | ✓                                      |
| Földrajzi hely                 | N/A                                    |
| Legközelebbi utak               | ✓                                      |
| Helyek keresése               | ✓                                      |
| Helyek részletei              | N/A – weboldal & telefonszám áll rendelkezésre |
| Helyek Fotók               | N/A                                    |
| Automatikus kiegészítés helye          | ✓                                      |
| Illesztés az úthoz                | ✓                                      |
| Sebességhatárok                | ✓                                      |
| Statikus térképek                 | ✓                                      |
| Statikus utcakép          | N/A                                    |
| Időzóna                   | ✓                                      |
| Térképek beágyazott API           | N/A                                    |
| Térkép URL-címei                    | N/A                                    |

A Google Térkép alapvető kulcsalapú hitelesítést biztosít. Az Azure Maps alapszintű kulcsalapú hitelesítést és Azure Active Directory-hitelesítést is biztosít. Az Azure Active Directory-hitelesítés több biztonsági funkciót biztosít, mint az alapszintű kulcsalapú hitelesítés.

## <a name="licensing-considerations"></a>Engedélyezési szempontok

Amikor a Google Térképről migrál az Azure Mapsbe, vegye figyelembe a licenceléssel kapcsolatos alábbi pontokat.

- Az Azure Maps díjat számít fel az interaktív térképek használatáért, amely a betöltött térképcsempék számától függ. Másrészt a Google Térkép díjat számít fel a térképvezérlő betöltése miatt. Az interaktív Azure Maps SDK-k ban a térképcsempék automatikusan gyorsítótárazva vannak a fejlesztési költségek csökkentése érdekében. Minden betöltött 15 térképcsempe egy Azure Maps-tranzakciót hoz létre. Az interaktív Azure Maps SDK-k 512 képpontos csempéket használ, és átlagosan egy vagy kevesebb tranzakciót hoz létre oldalanként.
- Gyakran a költséghatékonyabb a Google Maps webszolgáltatások statikus térképképeinek az Azure Maps Web SDK-val való lecserélése. Az Azure Maps Web SDK térképcsempéket használ. Hacsak a felhasználó nem pásztáz és nagyítja a térképet, a szolgáltatás gyakran csak egy töredékét generálja a tranzakciónak a térképterhelésenként. Az Azure Maps webes SDK-ban lehetőség van a pásztázás és a nagyítás letiltására, ha szükséges. Emellett az Azure Maps webes SDK sokkal több vizualizációs lehetőségeket biztosít, mint a statikus térkép webszolgáltatás.
- Az Azure Maps lehetővé teszi, hogy a platformról származó adatok at az Azure-ban tárolja. Emellett az adatok a [használati feltételeknek köszönhetően](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46)máshol is gyorsítótárazhatók.

Íme néhány kapcsolódó erőforrás az Azure Maps számára:

- [Az Azure Maps díjszabási lapja](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Azure-díjkalkulátor](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Az Azure Maps használati ideje](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (a Microsoft online szolgáltatásokra vonatkozó feltételekben szerepel)
- [Válassza ki a megfelelő tarifacsomagot az Azure Mapsben](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Javasolt áttelepítési terv

A következő egy magas szintű áttelepítési terv.

1. Leltárt vehet az alkalmazás által használt Google Térkép SDK-król és szolgáltatásokról. Ellenőrizze, hogy az Azure Maps alternatív SDK-kat és szolgáltatásokat biztosít-e.
2. Ha még nem rendelkezik ilyen, hozzon [https://azure.com](https://azure.com)létre egy Azure-előfizetést a.
3. Hozzon létre egy Azure Maps-fiókot ([dokumentáció](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)) és hitelesítési kulcsot vagy az Azure Active Directoryt ([dokumentáció).](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)
4. Telepítse át az alkalmazáskódot.
5. Tesztelje az áttelepített alkalmazást.
6. Telepítse az áttelepített alkalmazást éles környezetbe.

## <a name="azure-maps-technical-resources"></a>Az Azure Maps technikai erőforrásai

Az alábbiakban az Azure Maps hasznos technikai erőforrásait listáként listázta.

- Áttekintés:[https://azure.com/maps](https://azure.com/maps)
- Dokumentáció:[https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Webes SDK-kódminták:[https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Fejlesztői fórumok:[https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- Videók:[https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Blog:[https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Tech Blog:[https://aka.ms/AzureMapsTechBlog](https://aka.ms/AzureMapsTechBlog)
- Azure Maps visszajelzés (UserVoice):[https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)
- [Azure Maps Jupyter notebook] [https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook]

## <a name="migration-support"></a>Áttelepítés támogatása

A fejlesztők a fórumokon vagy az Azure számos támogatási lehetőségének egyikén keresztül kérhetnek [áttelepítési](https://aka.ms/AzureMapsForums) támogatást:[https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan telepítheti google maps alkalmazását az alábbi cikkekkel:

> [!div class="nextstepaction"]
> [Webalkalmazás migrálása](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Androidos alkalmazás migrálása](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [Webszolgáltatás migrálása](migrate-from-google-maps-web-services.md)
