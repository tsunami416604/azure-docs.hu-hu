---
title: 2.8 .NET-hez készült Azure SDK kibocsátási megjegyzései
description: 2.8 .NET-hez készült Azure SDK kibocsátási megjegyzései
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: de7207ff-ba4f-4008-9141-8742fcaa3254
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 6aa2684a900dffecd481d51b8876b0e674c1a6ea
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235530"
---
# <a name="azure-sdk-for-net-28-281-and-282"></a>Azure SDK 2.8-as, 2.8.1-es és 2.8.2 .NET-es kódtárával
## <a name="overview"></a>Áttekintés
A cikkben a kibocsátási megjegyzéseket (ismert problémák és kompatibilitástörő változásokat tartalmazza) az Azure SDK for .NET 2.8 2.8.1-es és 2.8.2-es kiadások. 

Új szolgáltatásokat és az ebben a kiadásban végzett frissítések teljes listáját lásd: a [Azure SDK 2.8-as a Visual Studio 2013 és a Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) közleményt. 

## <a name="azure-sdk-for-net-28"></a>Azure SDK for .NET 2.8
### <a name="download-azure-sdk-for-net-28"></a>2.8 .NET-hez készült Azure SDK letöltése
[Az Azure SDK for .NET csomaggal 2.8-as, a Visual Studio 2015](https://go.microsoft.com/fwlink/?LinkId=699285) 

[Azure SDK for .NET 2.8-as, a Visual Studio 2013-hoz](https://go.microsoft.com/fwlink/?LinkId=699287)

### <a name="net-452-support"></a>Támogatja a .NET 4.5.2.
#### <a name="known-issues"></a>Ismert problémák
Az Azure .NET SDK 2.8-as lehetővé teszi, hogy a .NET 4.5.2-es létrehozása Cloud Service-csomagok. Azonban a .NET 4.5.2-es keretrendszer nem lesz telepítve a vendég operációs rendszer lemezképek csak 2016. január vendég operációs rendszer kiadási alapértelmezés szerint. Adott, a .NET 4.5.2-es előtt keretrendszer egy külön a vendég operációs rendszer verziója – November 2015-02 érhető el. Tekintse meg a [Azure Vendégoperációsrendszer-kiadásainak listáját és az SDK-kompatibilitási mátrixot](../cloud-services/cloud-services-guestos-update-matrix.md) lapon nyomon követheti az elérhető lesz a lemezképet.  Követően jelent meg a November 2015-02-lemezkép kiválaszthatja, hogy a rendszerkép használata a Felhőszolgáltatás konfigurációs fájljához (.cscfg) fájl frissítésével. A szolgáltatáskonfiguráció szolgáltatásbeállítása fájlt az osVersion attribútum értéke az a szolgáltatáskonfiguráció elem értékre a karakterlánc "WA-VENDÉG-operációsrendszer-4.26_201511-02". Ha úgy dönt, hogy ez a rendszerkép használata majd többé nem kap a vendég operációs rendszer automatikus frissítései. Az automatikus frissítések beszerzéséhez az osVersion állítsa "*" és a .NET 4.5.2-es csak akkor jelenik meg a 2016. január Automatikus frissítésen keresztül.

### <a name="azure-data-factory"></a>Azure Data Factory
#### <a name="known-issues"></a>Ismert problémák
Során egy **Data Factory sablon** projekt létrehozásának érintő mintaadatokat, az azure PowerShell parancsfájl meghiúsulhat, ha az azure power shell verziója telepítve van a gépen, miután 0.9.8.

Sikerült létrehozni a projekt típusa, telepítsen [azure power shell verzió 0.9.8-as](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi).

### <a name="azure-resource-manager-tools"></a>Azure Resource Manager-eszközök
#### <a name="breaking-changes"></a>Kompatibilitástörő változások
Ebben a kiadásban dolgozhat az új Azure PowerShell-parancsmagok 1.0-s verziója a PowerShell-szkriptet az Azure erőforráscsoport-projekt által biztosított frissítve lett.  Ez a szkript nem fog működni a Visual studióval amikor az SDK 2.8-as előtti verzióját használja.  

Az SDK korábbi verzióival létrehozott projekt parancsfájlok nem fog futni a Visual Studión belül a 2.8-as SDK használatakor.  Az összes parancsfájl továbbra is a Visual Studión kívül használata az Azure PowerShell-parancsmagokat a megfelelő verzióját.  

Az SDK 2.8-t az Azure PowerShell-parancsmagok 1.0-s verzióját igényli.  Az SDK összes verziója szükséges az Azure PowerShell-parancsmagok 0.9.8-as verzióját.  További információ: [ez](https://go.microsoft.com/fwlink/?LinkID=623011) blog.

### <a name="web-tools-extensions"></a>Webes eszközök, bővítmények
#### <a name="known-issues"></a>Ismert problémák
Az alábbi ismert hibákat a következő kiadásban kibocsátásokban megtörténik.

* App Service-ben kapcsolódó felhőalapú és a Server Explorer nem éles környezetekben (például az Azure China vagy az Azure Stack ügyfelei) hitelesítési módok nem fognak működni. Az ügyfelek a következő érintett területeken a közzétételi profil letöltése az Azure Portalról megjelenését a techeden közzétételi lehetőség. Egy későbbi kiadásban Azure China és Stack felhasználói hitelesítési módok, például a "Hibakereső csatolása" és "View Streaming Logs" javítsa ki. 
* Ügyfeleink App Service létrehozása, amikor az App Insights-példány, amelyre telepíti a keleti régióban van során hibák jelenhet meg. Ezekben az esetekben egy App Service létrehozása a portálon, és a közzétételi profil letöltése közzétételi alábbi forgatókönyveket teszi lehetővé. 

### <a name="azure-hdinsight-tools"></a>Az Azure HDInsight-eszközök
#### <a name="new-updates"></a>Új frissítések
* Hajtsa végre a Hive-lekérdezést a HiveServer2-n keresztül a fürt szinte nélkül állnak, és tekintse meg a feladat bejelentkezik a valós idejű.
* Az új Hive-feladat-végrehajtási nézet, is a mélyebb feladat részletes részleteket, és lehetséges problémáinak meghatározásában.

További információ: [Azure SDK 2.8-as a Visual Studio 2013 és a Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/). 

## <a name="azure-sdk-for-net-281"></a>Azure SDK a .NET 2.8.1-es verziójához
### <a name="known-issues-for-visual-studio-2013-and-visual-studio-2015"></a>A Visual Studio 2013 és a Visual Studio 2015 kapcsolatos ismert problémák
1. Aktivált webjobs-feladat közzéteszi a tárolóhelyek lesz megjelenítése és a hiba, és nem beállított ütemezés szerint, de fogja leküldeni a webjobs-feladatot az Azure-bA. Ügyfelek, akik le kell egy ütemezett feladat felhasználhatja az Azure Portalon a webjobs-feladat ütemezésének beállítása. 
2. Python-ügyfelek hibakereső problémákat tapasztalhat. Service csapatának van jelennek meg a javítást erre, de ha azok érintenék ügyfeleit, kérem, forduljon a Microsoft ismeri a fórumokban, vagy a bejelentést a blogon vagy a kibocsátási megjegyzések a Megjegyzések szakaszban. 
3. Ügyfelek (például a Dél-India) bizonyos régiókban tapasztalható hibák App Service-ben. Ez megfelel a portálon, és az ügyfelek, akik a probléma az Azure portal segítségével kérhet hozzáférést az e földrajzi régiók közzétételére. Az Azure portal kiépítése után ezekben a régiókban használatával hozzáférést kérnek működik. 

## <a name="azure-sdk-for-net-282"></a>Az Azure SDK for .NET 2.8.2-es
A 2.8.2-es telepítését eszközök, az ügyfelek a következő hibát tapasztalhat.         

* Ha Windows 10-es használ, és nem telepítette az Internet Explorer, "Internet Explorer nem található" hibaüzenetet kaphat.
  A probléma elhárításához telepítse az Internet Explorer, a Windows-összetevők hozzáadása vagy eltávolítása párbeszédpanel használatával.

Megfigyelte a probléma, ha a Küldés a mosoly egy szolgáltatás használatával jelentse be.

További információkért lásd: [ez](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-2-for-net/) közzététele.

## <a name="other-updates"></a>Egyéb frissítések
Egyéb frissítések megtekintéséhez [Azure SDK 2.8-as közlemény post](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="also-see"></a>Lásd még:
[Az Azure SDK 2.8-as közlemény post](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[Támogatási és kivezetési információkat az Azure SDK for .NET és az API-khoz](https://msdn.microsoft.com/library/azure/dn479282.aspx)

