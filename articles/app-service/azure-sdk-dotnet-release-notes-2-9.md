---
title: Az Azure SDK for .NET 2.9 kibocsátási megjegyzései
description: Az Azure SDK for .NET 2.9 kibocsátási megjegyzései
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: c83d815b-fc19-4260-821e-7d2a7206dffc
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 04ee2daaf7b06f8e7bdd8de144a039474551ea11
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51227042"
---
# <a name="azure-sdk-for-net-29-release-notes"></a>Az Azure SDK for .NET 2.9 kibocsátási megjegyzései

Ez a témakör a .NET-hez 2.9 és 2.9.6 Azure SDK kibocsátási megjegyzései tartalmaz.

## <a name="azure-sdk-for-net-296-release-summary"></a>Az Azure SDK for .NET 2.9.6 kiadási összegzése

Kiadás dátuma: 2016-11/16
 
Ebben a kiadásban nem kompatibilitástörő változások, az Azure SDK 2.9 vezettek be. Nincs szükség kihasználhatja a meglévő Felhőszolgáltatás-projektek az SDK frissítési folyamat is van.

### <a name="visual-studio-2017-release-candidate"></a>A Visual Studio 2017 Release Candidate

- A Visual Studio 2017 RC ebben a kiadásban az Azure SDK for .NET a beépített, az Azure számítási feladatokhoz. Az összes, a kell tennie az Azure-fejlesztési eszközök a Visual Studio 2017 RC, a jövőben része lesz. A Visual Studio 2015-öt és a Visual Studio 2013, az SDK továbbra is elérhető lesz webpi-vel. Azt fog kell megszűnő Azure SDK a .NET kiadásokhoz Pro Visual Studio 2013, amikor Visual Studio 2017-kiadások végső üzemel. Töltse le a Visual Studio 2017 RC erre a hivatkozásra: https://www.visualstudio.com/vs/visual-studio-2017-rc/

### <a name="azure-diagnostics"></a>Azure Diagnostics

- Megváltoztatta a módot csak tárolásához egy részleges kapcsolati karakterlánc helyébe a Felhőszolgáltatások diagnosztikai tárfiók kapcsolati karakterláncának a jogkivonatot a kulccsal. Így szabályozható a hozzáférés a tényleges tárterület kulcsot most tárolja a felhasználói profil mappájába. A Visual Studio a tárfiók kulcsát olvasni a helyi hibakereséshez és a közzétételi folyamat felhasználói profil mappájába. 
- A fenti módosítás válaszul, a Visual Studio Online team az Azure Cloud Services üzembe helyezési sablon fokozott, így a felhasználó megadhatja a tárfiók hívóbetűjét, a diagnosztikai bővítmény állítja a folyamatos integráció az Azure-ba való közzétételekor és A központi telepítés.
- Végeztünk, lehetséges, biztonságos kapcsolati karakterlánc és az Azure Diagnostics (WAD), az között environements konfigurálásával kapcsolatos problémák megoldásához a jogkivonatok tárolására.
 
### <a name="windows-server-2016-virtual-machines"></a>A Windows Server 2016 virtuális gépek

- A Visual Studio mostantól támogatja az operációs rendszer termékcsalád 5 (Windows Server 2016) virtuális gépek, Cloud Services rendszerbe állítása. A meglévő cloud services módosíthatja a beállításokat, amelyekre az új operációsrendszer-család. Új felhőszolgáltatások létrehozásakor, ha úgy dönt, hogy hozza létre a szolgáltatást használó .net 4.6-os vagy újabb, az operációsrendszer-család 5 használni a szolgáltatást alapértelmezés lesz.  További információkért megtekintheti a [támogatja a Vendég operációsrendszer-család tábla](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/).

#### <a name="known-issues"></a>Ismert problémák

- Az Azure .NET SDK 2.9.6 vezetett be, amely blokkolja a telepítési projektek nem támogatott .NET-keretrendszerek (például a .NET 4.6) használatával az operációs rendszer családja korlátozás < 5. Áthidaló megadott [Itt](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9).

 
### <a name="azure-in-role-cache"></a>Az Azure szerepköralapú gyorsítótárához 

- Támogatás a 2016. November 30-Azure szerepköralapú gyorsítótárához ér véget. További részletekért kattintson [Itt](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).

### <a name="azure-resource-manager-templates-for-azure-stack"></a>Az Azure Stack az Azure Resource Manager-sablonokkal

- Jelentettük Azure Stack üzembe helyezés céljaként az Azure Resource Manager-sablonokkal.


## <a name="azure-sdk-for-net-29-summary"></a>Az Azure SDK for .NET 2.9 összegzése

## <a name="overview"></a>Áttekintés
Ez a dokumentum a kibocsátási megjegyzések az Azure SDK for .NET 2.9 kiadás tartalmazza. 

Ebben a kiadásban frissítésekkel kapcsolatos részletes információkért lásd: a [Azure SDK 2.9-bejelentés post](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>Az Azure SDK 2.9-cel for Visual Studio 2015 Update 2 és a Visual Studio "15" előzetes verzió
Ez a frissítés a következő hibajavításokat tartalmaz:

* REST API Client. generációs, amelyben a karakterlánc "Ismeretlen típus" néven jelenik meg a kódot – általános mappa nevét és/vagy a generált kódot érkező névtér nevére kapcsolatos probléma.
* Ütemezett webjobs-feladatok, amelyben a hitelesítési adatokat létesítésének folyamatát kell használnia az ütemező átadandó volt sikertelen kapcsolatos probléma.

A frissítés a következő új szolgáltatást tartalmazza:

* A "Szolgáltatások" fülre az App Service üzembe helyezési párbeszédpanelének másodlagos az App Services támogatása. 

## <a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Az Azure Data Lake Tools for Visual Studio 2015 Update 2
A frissítés a következőket tartalmazza:

* **Az Azure Data Lake Tools** for Visual Studio mostantól egyesíti az Azure SDK for .NET-kiadásban. Az eszköz automatikusan települ az Azure SDK telepítésekor. 
  
    Az eszköz milyen gyakran frissülnek, lépjen [Itt](https://aka.ms/datalaketool) le a frissítéseket.
* **Server Explorer** most már lehetővé teszi, hogy az összes megtekintése és a egy U-SQL metaadat-entitások létrehozása. További információkért lásd: [ez](https://azure.microsoft.com/documentation/services/data-lake-analytics/) blog.

## <a name="hdinsight-tools"></a>HDInsight-eszközök
**HDInsight Tools** mostantól támogatja a HDInsight 3.3-as, beleértve a Tez ábrákat és más nyelvet használva verziója kijavítja a Visual Studióhoz készült.

## <a name="azure-resource-manager"></a>Azure Resource Manager
Ez a kiadás bővült [KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) Resource Manager-sablonok támogatása.

## <a name="see-also"></a>Lásd még
[Az Azure SDK 2.9-bejelentés post](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)

