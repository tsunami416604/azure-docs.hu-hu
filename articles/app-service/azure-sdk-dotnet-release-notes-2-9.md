---
title: "Az Azure SDK for .NET 2.9 kibocsátási megjegyzések"
description: "Az Azure SDK for .NET 2.9 kibocsátási megjegyzések"
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: 
ms.assetid: c83d815b-fc19-4260-821e-7d2a7206dffc
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 199f0906f73d693d7cd4b73c928f23ae83b99596
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sdk-for-net-29-release-notes"></a>Az Azure SDK for .NET 2.9 kibocsátási megjegyzései

Ez a témakör a .NET 2.9 és az Azure SDK 2.9.6 kibocsátási megjegyzései tartalmaz.

##<a name="azure-sdk-for-net-296-release-summary"></a>Az Azure SDK for .NET 2.9.6 kiadási összegzése

Kiadás dátuma: 2016. 11/16
 
Ebben a kiadásban az Azure SDK 2.9 legfrissebb módosításokat nem vezettek be. Nem szükséges az SDK-val meglévő Felhőszolgáltatás-projektek kihasználhatják frissítési folyamat is van.

### <a name="visual-studio-2017-release-candidate"></a>A Visual Studio 2017 kiadásra jelölt verziójára

- A Visual Studio 2017 RC ebben a kiadásban az Azure SDK for .NET-t az Azure terheléseknél engedélyezett. Minden eszköz a végre kell hajtani az Azure fejlesztési továbbítja a Visual Studio 2017 RC része lesz. A Visual Studio 2015-öt és a Visual Studio 2013, az SDK-t továbbra is elérhetők WebPI keresztül. Azt fogja kell megszűnő Azure SDK .NET kiadásokban a Visual Studio 2013, ha a Visual Studio 2017 feloldja a végső termék. Hajtsa végre az erre a hivatkozásra kattintva töltse le a Visual Studio 2017 RC: https://www.visualstudio.com/vs/visual-studio-2017-rc/

### <a name="azure-diagnostics"></a>Azure Diagnostics

- A viselkedés csak tárolására Cloud Services diagnosztika tárolási kapcsolati karakterlánc egy token helyett a kulccsal részleges kapcsolati karakterlánc megváltozott. A tényleges kulcsot most tárolja a felhasználói profil mappában, így a hozzáférése vezérelhető. A Visual Studio a biztonságitár-kulcs olvasását helyi Hibakeresés és a közzétételi folyamat felhasználói profil mappájába. 
- A fent leírt módosítás válaszul a Visual Studio Online csapata az Azure Cloud Services központi telepítési sablon fokozott, így a felhasználók a diagnosztika bővítmény beállításához, a folyamatos integrációt és telepítést Azure való közzétételkor kulcsot kell megadni.
- Hajtottunk akkor lehet tárolni a biztonságos kapcsolati karakterláncot és létrehozása az Azure Diagnostics (ÜVEGVATTA), konfigurációjával kapcsolatos problémák megoldásához environements között.
 
### <a name="windows-server-2016-virtual-machines"></a>Windows Server 2016-os virtuális gépek

- A Visual Studio mostantól támogatja a felhő szolgáltatások telepítése az operációs rendszer családja 5 (Windows Server 2016) virtuális gépekhez. A meglévő felhőszolgáltatások módosíthatja a beállításokat, amelyekre az új operációsrendszer-család. Létrehozásakor új felhőalapú szolgáltatások, ha úgy dönt, hogy létrehozni a szolgáltatást használó .net 4.6-os vagy újabb, az alapértelmezés szerint a szolgáltatás az operációs rendszer családja 5.  További információkért tekintse át a [Vendég operációsrendszer-család támogatja a tábla](https://azure.microsoft.com/en-us/documentation/articles/cloud-services-guestos-update-matrix/).

#### <a name="known-issues"></a>Ismert problémák

- Az Azure .NET SDK 2.9.6 rendszerben jelent meg, amely blokkolja az telepítési projektek nem támogatott .NET-keretrendszert (például .NET 4.6) használatával egyetlen operációsrendszer-családról korlátozás < 5. A megoldás biztosítja [Itt](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9).

 
### <a name="azure-in-role-cache"></a>Azure szerepköralapú gyorsítótár 

- Támogatás a 2016. November 30 Azure szerepköralapú gyorsítótár végződik. További információért kattintson [Itt](https://azure.microsoft.com/en-us/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).

### <a name="azure-resource-manager-templates-for-azure-stack"></a>Az Azure-bA az Azure Resource Manager-sablonok a verem

- A Microsoft már megjelent Azure verem telepítési cél az Azure Resource Manager-sablonok.


## <a name="azure-sdk-for-net-29-summary"></a>Az Azure SDK for .NET 2.9 összegzése

## <a name="overview"></a>Áttekintés
Ez a dokumentum az Azure SDK for .NET 2.9 kiadás tartalmazza a kibocsátási megjegyzéseket. 

Ebben a kiadásban frissítésekkel kapcsolatos részletes információkért lásd: a [Azure SDK 2.9 közlemény post](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>A Visual Studio 2015 Update 2 és a Visual Studio "15" az Azure SDK 2.9 megtekintése
A frissítés tartalmazza a következő hibajavításokat tartalmaz:

* REST-API-ügyfél létrehozása a kapcsolatos problémát, amelyben a kód-generációs mappa nevét és/vagy a névtér nevét, a karakterlánc "Ismeretlen típusú" jelent a generált kód eldobva.
* Ahol a hitelesítő adatok nem működött átadását az ütemező létesítésének folyamatát kell használnia az ütemezett WebJobs kapcsolatos problémát.

A frissítés a következő új szolgáltatást tartalmaz:

* A "Szolgáltatások" fülre az App Service üzembe helyezési párbeszédpanel a másodlagos alkalmazásszolgáltatások támogatása. 

## <a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Az Azure Data Lake Tools for Visual Studio 2015 Update 2
A frissítés az alábbiakat tartalmazza:

* **Az Azure Data Lake Tools** a Visual Studio most egyesíti az Azure SDK for .NET kiadás. Az eszköz automatikusan települ Azure SDK telepítésekor. 
  
    Az eszköz gyakran frissül, nyissa meg [Itt](http://aka.ms/datalaketool) töltsék le a frissítéseket.
* **Server Explorer** most már lehetővé teszi az összes megtekintése és bizonyos U-SQL metaadat-entitások létrehozása. További információkért lásd: [ez](https://azure.microsoft.com/documentation/services/data-lake-analytics/) blog.

## <a name="hdinsight-tools"></a>A HDInsight Tools
**A HDInsight Tools** mostantól támogatja a HDInsight 3.3-as, beleértve a Tez ábrákat és egyéb nyelvi verzió kijavítja a Visual Studio.

## <a name="azure-resource-manager"></a>Azure Resource Manager
Ez a kiadás [KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) Resource Manager-sablonok támogatása.

## <a name="see-also"></a>Lásd még:
[Az Azure SDK 2.9 közlemény post](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)

