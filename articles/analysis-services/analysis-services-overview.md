---
title: Mi az az Azure Analysis Services | Microsoft Docs?
description: "Az Azure Analysis Services átfogó áttekintése."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 83d7a29c-57ae-4aa0-8327-72dd8f00247d
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 34726377836d00d484ca01edb098f6c7cbfa9dbf
ms.contentlocale: hu-hu
ms.lasthandoff: 06/17/2017


---
<a id="what-is-azure-analysis-services" class="xliff"></a>

# Mi az Azure Analysis Services?
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

A Microsoft SQL Server Analysis Services bizonyított elemzési motorjára épülő Azure Analysis Services vállalati szintű adatmodellezést nyújt a felhőben. 

Tekintse meg ezt a videót arról, hogyan illik az Azure Analysis Services a Microsoft általános BI-képességei közé, és hogyan használhatja ki az adatmodellek a felhőben történő tárolásából adódó előnyöket.


>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/Azure-Analysis-Services-overview/player]
>
>


<a id="built-on-sql-server-analysis-services" class="xliff"></a>

## Az SQL Server Analysis Servicesre épül
Az Azure Analysis Services kompatibilis a jól ismert azonos SQL Server Analysis Services Enterprise Editionnel. Az Azure Analysis Services támogatja a táblázatos modelleket az 1200-as és újabb kompatibilitási szinteken. Támogatja a DirectQueryt, a partíciókat, a sorszintű biztonságot, a kétirányú kapcsolatokat és a fordításokat.

<a id="use-the-tools-you-already-know" class="xliff"></a>

## A már ismerős eszközöket használhatja
![BI-fejlesztői eszközök](./media/analysis-services-overview/aas-overview-dev-tools.png)

Amikor adatmodelleket hoz létre az Azure Analysis Serviceshez, ugyanazokat az eszközöket használja, mint az SQL Server Analysis Serviceshez. Modelleket hozhat létre és helyezhet üzembe az [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) segítségével. Felügyelheti kiszolgálóit és modelladatbázisait az [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) segítségével. Emellett automatizálhatja feladatait a [Powershell-lel](analysis-services-powershell.md) és az [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)-sablonokkal 

<a id="supports-the-latest-features" class="xliff"></a>

## Támogatja a legújabb funkciókat
Az Azure Analysis Services támogatja a táblázatos modelleket az 1200-as és 1400-as előzetes verziójú kompatibilitási szinteken.

**Tabular 1200** – Elsőként az SQL Server 2016 Analysis Services részeként az 1200 bevezette a táblázatos objektummodellt (TOM) az olyan objektummodellek leírásához, mint a táblák, oszlopok és a kapcsolatok. A táblázatos objektummodell a JSON-ban van közzétéve a [Táblázatos modell parancsnyelv (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) és az AMO adatdefiníciós nyelv segítségével a [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx) névtérben.

**Tabular 1400 Preview** – Bevezeti a részletsorok, az objektumszintű biztonság és a hézagos hierarchia támogatását, a modern adatlekérési élményt az adatkapcsolat során és számos további fejlesztést. A legújabb funkciók előnyeinek kihasználásához a legújabb [SQL Server Data Tools- (SSDT-)](https://msdn.microsoft.com/library/mt204009.aspx) verzió szükséges. Mivel a Tabular 1400 egyelőre előzetes verziójú, gyorsan változnak a funkciói. A legújabb verzió beszerzéséhez tekintse meg [blogbejegyzésünket](https://azure.microsoft.com/blog/1400-models-in-azure-as/).

<a id="data-sources" class="xliff"></a>

## Adatforrások
Az Azure ügyfélszolgálatának kiszolgálóin üzembe helyezett adatmodellek, amelyek szervezete helyszíni vagy felhőalapú adatforrásaihoz csatlakoznak. Kombinálhatja a helyszíni és a felhőalapú adatforrásokból származó adatokat egy hibrid BI megoldáshoz.

Mivel a kiszolgálója a felhőben található, zökkenőmentesen csatlakozhat a felhőben lévő adatforrásokhoz. A helyszíni adatforrásokhoz való csatlakozáskor a [helyszíni adatátjáró](analysis-services-gateway.md) biztosítja a gyors, biztos kapcsolatot a felhőben található kiszolgálóval. További információk a támogatott helyszíni adatforrásokról: [Támogatott adatforrások az Azure Analysis Servicesben](analysis-services-datasource.md).


<a id="explore-your-data-from-anywhere" class="xliff"></a>

## Az adatai bárhonnan elérhetőek
Bárhonnan csatlakozhat kiszolgálóihoz és elérheti az adatait. Az Azure Analysis Services támogatja a Power BI Desktop, Excel, az egyéni alkalmazások és a böngészőalapú eszközök segítségével történő csatlakozást.

![Adatmegjelenítések](./media/analysis-services-overview/aas-overview-visualization.png)


<a id="secure" class="xliff"></a>

## Biztonságos
<a id="user-authentication" class="xliff"></a>

#### Felhasználóhitelesítés
Az Azure Analysis Services felhasználóhitelesítését az [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md) kezeli. Az Azure Analysis Services-adatbázisba történő bejelentkezési kísérlet során a felhasználók egy szervezeti fiókidentitást használnak, amely hozzáféréssel rendelkezik az elérni kívánt adatbázishoz. A felhasználói identitásoknak az Azure Analysis Services-kiszolgálót tartalmazó előfizetés alapértelmezett Azure Active Directoryja tagjainak kell lenniük. Az AAD és a helyszíni Active Directory közötti [címtár-integráció](https://technet.microsoft.com/library/jj573653.aspx) révén hozzáférést biztosíthat felhasználói számára egy Azure Analysis Services-adatbázishoz, azonban ez nem minden esetben szükséges.

A felhasználók fiókjuk egyszerű felhasználónevével (UPN) és jelszavukkal jelentkeznek be. Ha szinkronizálva van egy helyszíni Active Directoryval, a felhasználók UPN-je gyakran a szervezeti e-mail-címük.

Az Azure Analysis Services-kiszolgálóerőforrás engedélyeit az Azure-előfizetésen belüli felhasználók és szerepkörök egymáshoz rendelésével kezelheti. Alapértelmezés szerint az előfizetés rendszergazdái tulajdonosi engedélyekkel rendelkeznek az Azure-beli kiszolgáló-erőforráshoz. További felhasználókat az Azure Resource Managerrel adhat hozzá.

<a id="data-security" class="xliff"></a>

#### Adatbiztonság
Az Azure Analysis Services az Azure Blob Storage segítségével őrzi meg az Analysis Services-adatbázisok tárterületét és metaadatait. A Blobon belüli adatfájlok az Azure Blob kiszolgálóoldali titkosításával (SSE) vannak titkosítva. Közvetlen lekérdezési mód használatakor a rendszer csak a metaadatokat tárolja. A tényleges adatok az adatforrásból érhetőek el a lekérdezés során.

<a id="on-premises-data-sources" class="xliff"></a>

#### Helyszíni adatforrások
Szervezete helyszíni adataihoz a biztonságos hozzáférést [helyszíni adatátjáró](analysis-services-gateway.md) telepítésével és konfigurálásával biztosíthatja. Az átjárók hozzáférést biztosítanak az adatokhoz közvetlen lekérdezési és memóriában tárolt módban is. Az Azure Analysis Services-modell helyszíni adatforráshoz csatlakozásakor létrejön egy lekérdezés a helyszíni adatforrás titkosított hitelesítő adataival. Az átjáró felhőszolgáltatása elvégzi a lekérdezés elemzését, majd elküldi a kérést egy Azure Service Bus részére. A helyszíni átjáró lekérdezi a függőben lévő kéréseket az Azure Service Bustól. Az átjáró megkapja a lekérdezést, visszafejti a hitelesítő adatokat, majd csatlakozik az adatforráshoz annak végrehajtása érdekében. Az eredményeket a rendszer az adatforrástól visszaküldi az átjárónak, majd tovább az Azure Analysis Services-adatbázis számára.

Az Azure Analysis Servicesre a [Microsoft Online Services feltételei](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) és a [Microsoft Online Services adatvédelmi nyilatkozata](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx) vonatkozik.
További információk az Azure Securityről: [Microsoft biztonsági és adatkezelési központ](https://www.microsoft.com/trustcenter/Security/AzureSecurity).

<a id="get-help" class="xliff"></a>

## Segítségkérés

<a id="documentation" class="xliff"></a>

### Dokumentáció
Az Azure Analysis Services könnyen beállítható és kezelhető. Itt minden információt megtalálhat, amely egy kiszolgáló létrehozásához és kezeléséhez szükséges. A kiszolgálóján üzembe helyezendő adatmodell létrehozása nagy mértékben hasonlít a helyszíni kiszolgálón üzembe helyezett adatmodell létrehozásához. Az [Analysis Services](https://docs.microsoft.com/sql/analysis-services/analysis-services) oldalán egy fogalmakat, eljárásokat, oktatóanyagokat és referenciaanyagokat tartalmazó tár található.

<a id="videos" class="xliff"></a>

### Videók
Itt találhat útmutató-videókat: [Azure Analysis Services – Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services).

<a id="blogs" class="xliff"></a>

### Blogok
A dolgok gyorsan változnak. Az [Analysis Services csapatblogon](https://blogs.msdn.microsoft.com/analysisservices/) és az [Azure blogon](https://azure.microsoft.com/blog/) mindig megtekintheti a legfrissebb információkat.

<a id="community" class="xliff"></a>

### Közösség
Az Analysis Services felhasználói pezsgő közösséget alkotnak. Csatlakozzon a párbeszédhez az [Azure Analysis Services fórumon](https://aka.ms/azureanalysisservicesforum).

<a id="feedback" class="xliff"></a>

## Visszajelzés
Javaslatai vagy új funkciókkal kapcsolatos felvetései vannak? Írja meg az [Azure Analysis Services visszajelzési](https://aka.ms/azureanalysisservicesfeedback) oldalán.

Javaslatai vannak a dokumentációval kapcsolatosan? Szóljon hozzá a Livefyre segítségével a cikkek alján.

<a id="next-steps" class="xliff"></a>

## Következő lépések
Most, hogy már többet tud az Azure Analysis Servicesről, itt az ideje megtenni az első lépéseket. Megtudhatja, hogyan [hozhat létre kiszolgálót](analysis-services-create-server.md) az Azure-ban, és hogyan [helyezhet rajta üzembe egy táblázatos modellt](analysis-services-deploy.md).

