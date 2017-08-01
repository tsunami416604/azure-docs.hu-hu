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
ms.date: 07/03/2017
ms.author: owend
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: bf56aa4bafd929010bd916d09f7025ad76823d1f
ms.contentlocale: hu-hu
ms.lasthandoff: 07/21/2017

---
# <a name="what-is-azure-analysis-services"></a>Mi az Azure Analysis Services?
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Az Azure Analysis Services vállalati szintű adatmodellezést nyújt a felhőben. Ez egy teljeskörűen felügyelt platformszolgáltatás, amely szorosan integrálódik az Azure adatplatform-szolgáltatásaival. Az Analysis Services nagymértékben optimalizált OLAP adatelemzési motorjával gazdag szemantikai modellréteget biztosít nagy, összetett és gyakran eltérő adatforrások között. Az adatok összevonásával meghatározható a szemantikai adatmodell, így nagymértékben testre szabott és nagy teljesítményű elemzési folyamatok hozhatók létre, amelyekkel kiterjedt, interaktív elemzési funkciókat használhat a modern ügyféleszközökben.

![Adatforrások](./media/analysis-services-overview/aas-overview-data-sources.png)


Tekintse meg [ezt a videót](https://sec.ch9.ms/ch9/d6dd/a1cda46b-ef03-4cea-8f11-68da23c5d6dd/AzureASoverview_high.mp4) arról, hogyan illik az Azure Analysis Services a Microsoft általános BI-képességei közé, és hogyan használhatja ki az adatmodellek a felhőben történő tárolásából adódó előnyöket.

<!--
>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/Azure-Analysis-Services-overview/player]
>
>
-->

## <a name="built-on-sql-server-analysis-services"></a>Az SQL Server Analysis Servicesre épül
Az Azure Analysis Services kompatibilis a jól ismert azonos SQL Server Analysis Services Enterprise Editionnel. Az Azure Analysis Services támogatja a táblázatos modelleket az 1200-as és 1400-as kompatibilitási szinteken. Támogatja a partíciókat, a sorszintű biztonságot, a kétirányú kapcsolatokat és a fordításokat. A memóriában tárolt és a DirectQuery módok villámgyors lekérdezéseket tesznek lehetővé nagy és összetett adatkészletekben.

A táblázatos modellek gyors fejlesztést biztosítanak, és nagymértékben testre szabhatók. A fejlesztők a táblázatos modellek között a táblázatos objektummodellt (TOM) is használhatják a modellobjektumok leírásához. A TOM a JSON-ban van közzétéve a [Táblázatos modell parancsnyelv (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) és az AMO adatdefiníciós nyelv segítségével a [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx) névtérben.

A Tabular 1400-modellek új funkciói támogatják a részletsorokat, az objektumszintű biztonságot és a hézagos hierarchiát, a modern SSDT-adatlekérési felületet az adatkapcsolat biztosításához, valamint számos további fejlesztést. Az alapul szolgáló modell metaadatai változatlanok, ezért a meglévő helyszíni táblázatosmodell-megoldások könnyen migrálhatók a felhőbe.


## <a name="better-with-azure"></a>Hatékonyabb munkavégzés az Azure-ral
Az Azure Analysis Services számos Azure-adatszolgáltatással integrálható, így kifinomult elemzési megoldásokat hozhat létre.

Az Azure Analysis Services szolgáltatásai képesek feldolgozni az Azure SQL Database-ből, az Azure SQL Data Warehouse-ból és az Azure Blob Storage-ból származó adatokat is. A vállalati adattárház-megoldások kiépítéséhez az Azure-ban csillagpontos modellt használhat, amelynek középpontjában az SQL Data Warehouse áll, körülötte pedig különböző üzleti csoportokat vagy tématerületeket célzó BI-modellek találhatók.

Az Azure Data Factoryval vezényelhető az adatok áthelyezése és átalakítása, ami minden vállalati BI- vagy elemzési megoldásban alapvető képesség. Az Azure Analysis Services bármely Azure Data Factory-folyamatba integrálható egy olyan tevékenység bevonásával, amely adatokat tölt be a modellbe. Az Azure Automation és az Azure Functions egyéni kódot használó modellek egyszerűbb vezénylésére is használható.

Az Azure Analysis Services az Azure Active Directoryval is szorosan integrálva van, így biztonságos, szerepköralapú hozzáférést biztosít a kritikus fontosságú adatokhoz.

## <a name="pricing"></a>Díjszabás
Az Azure Analysis Services fejlesztői, alap- és standard szinten is elérhető. Az egyes szinteken belül a csomagköltségek a feldolgozási teljesítmény, a QPU-k és a memória mérete alapján változnak. Amikor létrehoz egy kiszolgálót, egy adott szinten belül választ ki egy csomagot. A csomagokat a szinten belül magasabb vagy alacsonyabb szintre módosíthatja, vagy frissíthet magasabb szintre, de magasabb szintről nem léphet vissza egy alacsonyabbra.

![Szintfrissítés](./media/analysis-services-overview/aas-overview-tier-up.png)

A szintek az Azure Portalon vagy a Set-AzureRmAnalysisServicesServer PowerShell-parancsmag használatával azonnal módosíthatók. További információ a különböző csomagokról és szintekről, illetve az Önnek legmegfelelőbb csomag kiválasztására szolgáló díjkalkulátor használatáról: [Az Azure Analysis Services díjszabása](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="scale-resources"></a>Erőforrások skálázása
Vertikálisan fel- vagy leskálázhatja, illetve szüneteltetheti a kiszolgálóját. Ehhez használhatja az Azure Portalt, illetve a PowerShellt, amely azonnali és teljes körű vezérlést biztosít. A fizetés használat alapján történik.

Új kiszolgálók létrehozásakor a [New-AzureRmAnalysisServicesServer](https://docs.microsoft.com/en-us/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) parancsmaggal állíthatja be a csomagot. Meglévő kiszolgálók esetén a [Set-AzureRmAnalysisServicesServer](https://docs.microsoft.com/en-us/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver) parancsmaggal módosíthatja a csomagot. Nem használja folyamatosan a szolgáltatást? A portálon vagy a [Suspend-AzureRmAnalysisServicesServer](https://docs.microsoft.com/en-us/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver) parancsmaggal szüneteltetheti. Az újraindítás a [Resume-AzureRmAnalysisServicesServer](https://docs.microsoft.com/en-us/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver) parancsmaggal történik. Csak a kiszolgáló aktív működési ideje után kell fizetnie.


## <a name="regions"></a>Régiók
Az Azure Analysis Services szolgáltatásai a következő [Azure-régiókban](https://azure.microsoft.com/regions/) hozhatók létre:

| Amerika | Európa | Ázsia és a Csendes-óceáni térség |
|----------|--------|--------------|
|  Dél-Brazília<br> Közép-Kanada<br> USA 2. keleti régiója<br> USA északi középső régiója<br> USA déli középső régiója<br> USA nyugati középső régiója<br> USA nyugati régiója | Észak-Európa<br> Az Egyesült Királyság déli régiója<br> Nyugat-Európa |   Délnyugat-Ausztrália<br> Kelet-Japán<br> Délkelet-Ázsia<br> Nyugat-India  |

Folyamatosan veszünk fel új régiókat, így lehetséges, hogy ez a lista nem teljes. A helyet megadhatja az Azure Resource Manager-sablonok használatával, vagy a kiszolgáló Azure Portalon történő létrehozásakor. A legjobb teljesítmény eléréséhez a legnagyobb felhasználói bázisához legközelebb eső helyet válassza ki. Ha a modelljeit több régióban található redundáns kiszolgálókon helyezi üzembe, biztosíthatja a [magas rendelkezésre állást](analysis-services-bcdr.md).

## <a name="get-up-and-running-quickly"></a>Gyors beállítás és használat
Percek alatt [létrehozhat egy kiszolgálót](analysis-services-create-server.md) az Azure Portalon. Az Azure Resource Manager-sablonok és a PowerShell használatával pedig deklaratív sablonokkal helyezheti üzembe a kiszolgálókat. Egyetlen sablonnal több szolgáltatást is üzembe helyezhet, egyéb Azure-összetevőkkel (például tárfiókokkal) együtt.  További információ: [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../azure-resource-manager/resource-group-template-deploy.md).

A kiszolgáló létrehozása után közvetlenül az Azure Portalon létrehozhat egy táblázatos modellt. Az új (előzetes verziójú) webes tervező funkcióval Azure SQL Database-adatbázisokhoz és Azure SQL Data Warehouse-adatforrásokhoz kapcsolódhat, vagy importálhat Power BI Desktop- (.pbix kiterjesztésű) fájlokat. A táblák közötti kapcsolatok automatikusan jönnek létre, és közvetlenül a böngészően hozhat létre mértékeket vagy szerkesztheti a modell .bim fájlját json formátumban.

## <a name="migrate-existing-tabular-models"></a>Meglévő táblázatos modellek migrálása
Ha már rendelkezik meglévő helyszíni SQL Server Analysis Services-modellmegoldásokkal, jelentős módosítások nélkül végezhet migrálást az Azure Analysis Servicesbe. A migráláshoz az SSDT-vel helyezheti üzembe a modellt a kiszolgálón. Az SSMS biztonsági mentés és visszaállítás funkcióját vagy a TMSL-t is használhatja.

Ha helyszíni adatforrásokkal rendelkezik, telepítenie és konfigurálnia kell egy [helyszíni adatátjárót](analysis-services-gateway.md). Ha már elvégezte a szerepkörök és a szerepkörtagok konfigurálását, a szerepkörök migrálhatók, de az SSMS vagy a PowerShell használatával újra fel kell vennie a szerepkörök tagjait.


## <a name="data-sources"></a>Adatforrások
Az Azure Analysis Services szolgáltatásai támogatják a szervezet helyszíni vagy felhőalapú adatforrásaihoz történő csatlakozást. A helyszíni és a felhőalapú adatforrásokból származó adatokat kombinálhatja is egy hibrid megoldás létrehozásához. 

Az új Tabular 1400-modellek az SSDT modern Adatok lekérése funkcióját használják, amely M-képletes lekérdezési nyelvre épül. Az Adatok lekérése funkcióval további adatátalakítási és -egyesítési szolgáltatásokat érhet el, valamint saját, fejlett M-képletes nyelven történő lekérdezéseket hozhat létre és szerkeszthet. A Tabular 1400-modellekkel például modellezheti az Azure Blob Storage-ban lévő adatfájlokat.

Az Azure Analysis Services támogatja a [DirectQuery](https://docs.microsoft.com/sql/analysis-services/tabular-models/directquery-mode-ssas-tabular) használatát az Azure SQL Database, Azure SQL Data Warehouse, SQL Server, SQL Server Data Warehouse, Oracle és a Teradata relációs adatbázisaihoz való közvetlen kapcsolódáshoz.

További információ: [Támogatott adatforrások az Azure Analysis Servicesben](analysis-services-datasource.md).

## <a name="use-the-tools-you-already-know"></a>A már ismerős eszközöket használhatja

![BI-fejlesztői eszközök](./media/analysis-services-overview/aas-overview-dev-tools.png)

#### <a name="sql-server-data-tools-ssdt-for-visual-studio"></a>SQL Server Data Tools (SSDT) for Visual Studio
Az ingyenes [SQL Server Data Tools (SSDT) for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) használatával modelleket fejleszthet és helyezhet üzembe. Az SSDT Analysis Services-projektsablonokat is tartalmaz a gyors üzembe állítás érdekében. Az SSDT már tartalmazza a Tabular 1400-modellekkel használható modern Adatok lekérése adatforrás-lekérdezési és adategyesítési funkciót is. Ha már ismeri a Power BI Desktop és az Excel 2016 Adatok lekérése funkcióját, akkor tudja, milyen egyszerűen hozhat létre nagymértékben testre szabott adatforrás-lekérdezéseket.

Az új SSDT és a Tabular 1400-modellekkel már nem kell helyi Analysis Services-példányt telepítenie egy munkaterület-adatbázis üzemeltetéséhez. Az SSDT mostantól saját, integrált Analysis Services-motort és -adatbázist is tartalmaz. Ha készen áll, közvetlenül az SSDT-ből végezheti el az üzembe helyezést az Azure-beli kiszolgálóira. Az SSDT ráadásul havonta frissül, így hamar rendelkezésére állnak a legújabb funkciók is.

#### <a name="sql-server-management-studio"></a>Sql Server Management Studio
Felügyelheti kiszolgálóit és modelladatbázisait az [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) segítségével. Kapcsolódhat a felhőalapú kiszolgálóihoz. Közvetlenül az XMLA lekérdezési ablakból futtathat TMSL-szkripteket, amelyekkel automatizálhatja a feladatokat. Gyakran jelennek meg új szolgáltatások és funkciók – az SSMS havonta frissül.

#### <a name="powershell"></a>PowerShell
Az olyan kiszolgálóerőforrás-kezelési feladatok, mint például a kiszolgálók létrehozása, a kiszolgálói műveletek felfüggesztése vagy folytatása, illetve a szolgáltatásszint módosítása Azure Resource Manager- (AzureRM-) parancsmagokkal történik. Az olyan egyéb adatbázis-kezelési feladatok, mint például a szerepkörtagok hozzáadása vagy eltávolítása, illetve a TMSL-szkriptek feldolgozása vagy futtatása az SqlServer modul parancsmagjait használják. Az AzureRM- és az SQLServer-modulok elérhetők a [PowerShell-galériában](https://www.powershellgallery.com/).


## <a name="secure"></a>Biztonságos
![Adatmegjelenítések](./media/analysis-services-overview/aas-overview-secure.png)

#### <a name="authentication"></a>Authentication
Az Azure Analysis Services felhasználóhitelesítését az [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md) kezeli. Az Azure Analysis Services-adatbázisba történő bejelentkezési kísérlet során a felhasználók egy szervezeti fiókidentitást használnak, amely hozzáféréssel rendelkezik az elérni kívánt adatbázishoz. A felhasználói identitásoknak az Azure Analysis Services-kiszolgálót tartalmazó előfizetés alapértelmezett Azure Active Directoryja tagjainak kell lenniük. További információ: [Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md).

#### <a name="data-security"></a>Adatbiztonság
Az Azure Analysis Services az Azure Blob Storage segítségével őrzi meg az Analysis Services-adatbázisok tárterületét és metaadatait. A Blobon belüli adatfájlok az Azure Blob kiszolgálóoldali titkosításával (SSE) vannak titkosítva. Közvetlen lekérdezési mód használatakor a rendszer csak a metaadatokat tárolja. A tényleges adatok az adatforrásból érhetőek el a lekérdezés során.

#### <a name="on-premises-data-sources"></a>Helyszíni adatforrások
A [helyszíni adatátjáró](analysis-services-gateway.md) telepítésével és konfigurálásával biztosíthatja a szervezet helyszíni adataihoz való biztonságos hozzáférést. Az átjárók hozzáférést biztosítanak az adatokhoz közvetlen lekérdezési és memóriában tárolt módban is. Az Azure Analysis Services-modell helyszíni adatforráshoz csatlakozásakor létrejön egy lekérdezés a helyszíni adatforrás titkosított hitelesítő adataival. Az átjáró felhőszolgáltatása elvégzi a lekérdezés elemzését, majd elküldi a kérést egy Azure Service Bus részére. A helyszíni átjáró lekérdezi a függőben lévő kéréseket az Azure Service Bustól. Az átjáró megkapja a lekérdezést, visszafejti a hitelesítő adatokat, majd csatlakozik az adatforráshoz annak végrehajtása érdekében. Az eredményeket a rendszer az adatforrástól visszaküldi az átjárónak, majd tovább az Azure Analysis Services-adatbázis számára.

Az Azure Analysis Servicesre a [Microsoft Online Services feltételei](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) és a [Microsoft Online Services adatvédelmi nyilatkozata](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx) vonatkozik.
További információk az Azure Securityről: [Microsoft biztonsági és adatkezelési központ](https://www.microsoft.com/trustcenter/Security/AzureSecurity).

## <a name="client-connections"></a>Ügyfélkapcsolatok
![Adatmegjelenítések](./media/analysis-services-overview/aas-overview-clients.png)

Az olyan modern adatáttekintési és vizualizációs eszközök, mint például a Power BI, az Excel, illetve az egyéb, harmadik felektől származó eszközök interaktív és vizuálisan gazdag modelladat-elemzéseket biztosítanak a végfelhasználóknak.

Az ügyfelek MSOLAP-, AMO- vagy ADOMD- [ügyfélkódtárakkal](analysis-services-data-providers.md) kapcsolódnak az Analysis Services-kiszolgálókhoz. Az olyan Microsoft-ügyfélalkalmazások, mint a Power BI Desktop és az Excel mindhárom ügyfélkódtárat telepítik. Ne feledje azonban, hogy a frissítések verziószámától vagy gyakoriságától függően előfordulhat, hogy az ügyfélkódtárak verziója nem a legújabb, az Azure Analysis Services használatához szükséges verzió. Ugyanez vonatkozik az egyéni alkalmazásokra vagy olyan egyéb felületekre, mint például az AsCmd, a TOM vagy az ADOMD.NET. Ezekben az alkalmazásokban a kódtárakat rendszerint manuálisan, egy csomag részeként kell telepíteni.


## <a name="get-help"></a>Segítségkérés

#### <a name="documentation"></a>Dokumentáció
Az Azure Analysis Services könnyen beállítható és kezelhető. Itt minden információt megtalálhat, amely a kiszolgálói szolgáltatások létrehozásához és kezeléséhez szükséges. A kiszolgálóján üzembe helyezendő adatmodell létrehozása nagy mértékben hasonlít a helyszíni kiszolgálón üzembe helyezett adatmodell létrehozásához. Az [Analysis Services](https://docs.microsoft.com/sql/analysis-services/analysis-services) oldalán egy fogalmakat, eljárásokat, oktatóanyagokat és referenciaanyagokat tartalmazó tár található.

#### <a name="videos"></a>Videók
Itt találhat útmutató videókat: [Azure Analysis Services – Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services).

#### <a name="blogs"></a>Blogok
A dolgok gyorsan változnak. Az [Analysis Services csapatblogon](https://blogs.msdn.microsoft.com/analysisservices/) és az [Azure blogon](https://azure.microsoft.com/blog/) mindig megtekintheti a legfrissebb információkat.

#### <a name="community"></a>Közösség
Az Analysis Services felhasználói pezsgő közösséget alkotnak. Csatlakozzon a párbeszédhez az [Azure Analysis Services fórumon](https://aka.ms/azureanalysisservicesforum).

## <a name="feedback"></a>Visszajelzés
Javaslatai vagy új funkciókkal kapcsolatos felvetései vannak? Írja meg az [Azure Analysis Services visszajelzési](https://aka.ms/azureanalysisservicesfeedback) oldalán.

Javaslatai vannak a dokumentációval kapcsolatosan? Szóljon hozzá a Livefyre segítségével a cikkek alján.

## <a name="next-steps"></a>Következő lépések
Most, hogy már többet tud az Azure Analysis Servicesről, itt az ideje megtenni az első lépéseket. Megtudhatja, hogyan [hozhat létre kiszolgálót](analysis-services-create-server.md) az Azure-ban. Ha a kiszolgáló készen áll, hajtsa végre az [Adventure Works-oktatóanyag](tutorials/aas-adventure-works-tutorial.md) lépéseit. Ezekből megtudhatja, hogyan hozhat létre és helyezhet üzembe a kiszolgálón egy teljes körűen használható táblázatos modellt.

