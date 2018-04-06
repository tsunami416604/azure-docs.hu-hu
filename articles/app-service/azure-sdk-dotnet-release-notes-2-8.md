---
title: Az Azure SDK for .NET 2,8 kibocsátási megjegyzései
description: Az Azure SDK for .NET 2,8 kibocsátási megjegyzései
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
ms.openlocfilehash: 0b9f55d69c824e86245738a082f95fc529583f58
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="azure-sdk-for-net-28-281-and-282"></a>2.8, 2.8.1-es verziójának és 2.8.2 .NET-keretrendszerhez készült Azure SDK
## <a name="overview"></a>Áttekintés
A cikkben a kibocsátási megjegyzéseket (ismert problémák és a jelentős változásokat tartalmaz) az Azure SDK 2.8.1-es verziójának és 2.8.2 .NET 2.8 kiadja a. 

Új szolgáltatásokat és az ebben a kiadásban végrehajtott frissítések teljes listáját lásd: a [Azure SDK 2.8 a Visual Studio 2013 és a Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) közlemény. 

## <a name="azure-sdk-for-net-28"></a>Azure SDK for .NET 2.8
### <a name="download-azure-sdk-for-net-28"></a>2.8 .NET-keretrendszerhez készült Azure SDK letöltése
[Az Azure SDK for .NET 2.8 a Visual Studio 2015](http://go.microsoft.com/fwlink/?LinkId=699285) 

[Azure SDK for .NET 2.8 for Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=699287)

### <a name="net-452-support"></a>Támogatja a .NET 4.5.2.
#### <a name="known-issues"></a>Ismert problémák
Az Azure .NET SDK 2.8 hozhat létre .NET 4.5.2 Cloud Service-csomagok. Azonban .NET 4.5.2 keretrendszer nem lesz telepítve a vendég operációs rendszer képek, amíg a vendég operációs rendszer 2016. január kiadási alapértelmezett. Adott, a .NET 4.5.2 előtt keretrendszer egy külön vendég operációs rendszer verziója – 2015-02 November érhető el. Tekintse meg a [Azure vendég operációs rendszereinek kiadásait és SDK-kompatibilitási mátrixát](../cloud-services/cloud-services-guestos-update-matrix.md) nyomon követi, mikor felszabadulnak, a lemezkép lapot.  Ha megjelenik a November 2015-02-lemezkép dönthet úgy, lemezkép használata a felhőalapú szolgáltatás konfigurációs fájlját (.cscfg) fájl frissítésével. A szolgáltatáskonfiguráció szolgáltatásbeállítása fájl az ServiceConfiguration elem osVersion attribútum beállítása a karakterlánc "WA-VENDÉG-operációsrendszer-4.26_201511-02". Ha úgy dönt, hogy a kép használható, ezt követően már nem jelenik meg a vendég operációs rendszer az automatikus frissítések engedélyezve. Az automatikus frissítések beszerzéséhez az osVersion értékre kell állítani "*" és a .NET 4.5.2 csak az automatikus frissítések 2016. január érhető el.

### <a name="azure-data-factory"></a>Azure Data Factory
#### <a name="known-issues"></a>Ismert problémák
Során egy **Data Factory sablon** projekt létrehozása érintő mintaadatok, az azure PowerShell parancsfájl meghiúsulhat, ha azure power shell verzióját a számítógépen telepítve van a 0.9.8-as után.

Sikeresen létre ilyen típusú projektet, telepítsen [azure power shell verzió 0.9.8-as](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi).

### <a name="azure-resource-manager-tools"></a>Az Azure erőforrás-kezelő eszközei
#### <a name="breaking-changes"></a>Módosítások megszakítása
Ebben a kiadásban az új Azure PowerShell-parancsmagok 1.0-s verziója használható a PowerShell-parancsfájl az Azure erőforráscsoport-projekt által biztosított frissítve lett.  Ez a parancsfájl nem fog működni a Visual Studio az SDK 2.8 előtt egy verziójának használatakor.  

Parancsfájlokat a korábbi verzióiban az SDK-val létrehozott projektek nem fog futni a Visual studióban a 2,8 SDK használatakor.  Az összes parancsfájl továbbra is működnek majd a Visual Studio kívül az Azure PowerShell-parancsmagok a megfelelő verzióját.  

A 2,8 SDK az Azure PowerShell-parancsmagok 1.0-s verziója szükséges.  Az SDK összes verziója szükséges az Azure PowerShell-parancsmagok 0.9.8-as verzióját.  További információ: [ez](http://go.microsoft.com/fwlink/?LinkID=623011) blog.

### <a name="web-tools-extensions"></a>Webes bővítmények eszközei
#### <a name="known-issues"></a>Ismert problémák
A következő ismert problémák kiiktatása a következő kiadásban.

* App Service kapcsolódó felhő és a Server Explorer nem éles környezetben (például az Azure China vagy Azure verem ügyfelek) hitelesítési mód nem működnek. Az ügyfelek e érintett területek a közzétételi profil letöltése az Azure portálról lesz lehetőség van a közzététel. Egy későbbi kiadásban fog javítása kézmozdulatok például "Csatolása hibakereső" és "Folyamatos átviteli naplók megtekintése" Azure Kína és verem ügyfelek. 
* Az ügyfelek App Service létrehozása, amikor az App Insights példány azok telepítésével USA keleti régiója nem abban a régióban van során hibák jelenhet meg. Ezekben az esetekben egy App Service létrehozása a portálon, és a közzétételi profil letöltése engedélyezi közzétételi forgatókönyvek. 

### <a name="azure-hdinsight-tools"></a>Az Azure HDInsight eszközök
#### <a name="new-updates"></a>Új frissítések
* A Hive-lekérdezést végrehajtani a fürt hiveserver2-n keresztül, szinte nincs terhelés mellett, és tekintse meg a feladat jelentkezik be a valós idejű.
* Az új Hive feladatvégrehajtási nézet akkor is elmerülne a rendszer a feladat mélyebb be további részletekért, és lehetséges problémák azonosítása.

További információ: [Azure SDK 2.8 a Visual Studio 2013 és a Visual Studio 2015-öt](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/). 

## <a name="azure-sdk-for-net-281"></a>Azure SDK a .NET 2.8.1-es verziójához
### <a name="known-issues-for-visual-studio-2013-and-visual-studio-2015"></a>A Visual Studio 2013 és a Visual Studio 2015-öt kapcsolatos ismert problémák
1. Indított webjobs-feladat közzéteszi az üzembe helyezési ponti lesz megjelenítése és a hiba, és nem beállított ütemezés szerint, de fogja leküldeni a webjobs-feladat az Azure-bA. Az ügyfelek, akik le kell egy ütemezett feladatot használhatja az Azure-portálon a webjobs-feladat ütemezésének beállítása. 
2. Python ügyfelek hibakereső problémákba ütközhetnek. Csoport folyamatban van egy javítsa ki a, de ha ügyfelet érintenének, kérjük, tájékoztasson Microsoft tudja a fórumokon vagy a hirdetmény blogon vagy a kibocsátási megjegyzések Megjegyzések szakasz. 
3. Felhasználók (például a Dél-India) egyes régiókban fog tapasztalni App Service-kiépítési hibák. Ez megfelel a portálon, és az ügyfelek, akik a probléma az Azure-portál használatával kérjen hozzáférést a földrajzi régiók közzétételére. Amennyiben ezek régiókat hozzáférést kérnek az Azure portál kiépítés kell működnie. 

## <a name="azure-sdk-for-net-282"></a>Azure SDK for .NET 2.8.2
Telepítését követően a 2.8.2 eszközök, az ügyfelek a következő problémákat tapasztalhat.         

* Ha a Windows 10 használata nem telepítette az Internet Explorer, előfordulhat, hogy "Az Internet Explorer nem található" hibaüzenetet kap.
  A probléma megoldása érdekében telepítse az Internet Explorer, a Windows-összetevők hozzáadása vagy eltávolítása párbeszédpanel használatával.

Ha a probléma, a Küldés a mosolynál szolgáltatás használatával jelentse be.

További információkért lásd: [ez](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-2-for-net/) utáni.

## <a name="other-updates"></a>Egyéb frissítések
Más frissítéseket, lásd: [Azure SDK 2.8 közlemény post](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="also-see"></a>Lásd még:
[Az Azure SDK 2.8 közlemény post](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[Támogatás és használatból való kivonást információk az Azure SDK-t a .NET és API-khoz](https://msdn.microsoft.com/library/azure/dn479282.aspx)

