---
title: "Az Azure SDK for .NET 3.0-s kibocsátási megjegyzései |} Microsoft Docs"
description: "Az Azure SDK for .NET 3.0 kibocsátási megjegyzések"
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
ms.date: 03/07/2017
ms.author: juliako
ms.openlocfilehash: eea4e569ac2d0192ed7872d2fcb9bed03614832b
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="azure-sdk-for-net-30-release-notes"></a>Az Azure SDK for .NET 3.0 kibocsátási megjegyzései

Ez a témakör fontos tudnivalók az Azure SDK 3.0-s verziójának a .NET-keretrendszerhez készült.

##<a name="azure-sdk-for-net-30-release-summary"></a>Az Azure SDK .NET 3.0 kiadás összefoglaló

Kiadás dátuma: 03/07/2017
 
Ebben a kiadásban nincs jelentős változásokat az Azure SDK 3.0 vezettek be. Nem szükséges az SDK-val meglévő Felhőszolgáltatás-projektek kihasználhatják frissítési folyamat is van. Ahhoz, hogy az Azure SDK 3.0 használatát anélkül, hogy a frissítési folyamat, Azure SDK 3.0-s telepítése az Azure SDK 2.9 mint ugyanazon könyvtárak. Leggyakrabban az összetevők 2.9 a fő verziószáma nem változott, de ehelyett frissítése a buildszáma.

## <a name="visual-studio-2017-rtw"></a>A Visual Studio 2017 RTW

- A Visual Studio 2017 ebben a kiadásban az Azure SDK for .NET-t az Azure-alkalmazások. Minden eszköz a végre kell hajtani az Azure fejlesztési továbbítja a Visual Studio 2017 része lesz. A Visual Studio 2015 az SDK-t továbbra is elérhetők WebPI keresztül. A Microsoft megszüntetése Azure SDK .NET kiadásokban a Visual Studio 2013 most, hogy a Visual Studio 2017 kiadása.

### <a name="azure-diagnostics"></a>Azure Diagnostics

- A viselkedés csak tárolására Cloud Services diagnosztika tárolási kapcsolati karakterlánc egy token helyett a kulccsal részleges kapcsolati karakterlánc megváltozott. A tényleges kulcsot most tárolja a felhasználói profil mappában, így a hozzáférése vezérelhető. A Visual Studio a biztonságitár-kulcs olvasását helyi Hibakeresés és a közzétételi folyamat felhasználói profil mappájába. 
- A fent leírt módosítás válaszul a Visual Studio Online csapata az Azure Cloud Services központi telepítési sablon fokozott, így a felhasználók a diagnosztika bővítmény beállításához, a folyamatos integrációt és telepítést Azure való közzétételkor kulcsot kell megadni.
- Hajtottunk akkor lehet tárolni a biztonságos kapcsolati karakterláncot és létrehozása az Azure Diagnostics (ÜVEGVATTA), konfigurációjával kapcsolatos problémák megoldásához environements között.
 
### <a name="windows-server-2016-virtual-machines"></a>Windows Server 2016-os virtuális gépek

- A Visual Studio mostantól támogatja a felhő szolgáltatások telepítése az operációs rendszer családja 5 (Windows Server 2016) virtuális gépekhez. A meglévő felhőszolgáltatások módosíthatja a beállításokat, amelyekre az új operációsrendszer-család. Létrehozásakor új felhőalapú szolgáltatások, ha úgy dönt, hogy létrehozni a szolgáltatást használó .net 4.6-os vagy újabb, az alapértelmezés szerint a szolgáltatás az operációs rendszer családja 5.  További információkért tekintse át a [Vendég operációsrendszer-család támogatja a tábla](../cloud-services/cloud-services-guestos-update-matrix.md).

### <a name="known-issues"></a>Ismert problémák

- Az Azure .NET SDK 3.0-s bevezetett problémát, a Visual Studio 2015 párhuzamos konfigurációban a Visual Studio 2017 eltávolításakor.  Ha az Azure SDK telepítése a Visual Studio 2015, a Microsoft Azure Storage Emulator és a Microsoft Azure Compute Emulator törlődni fog a Visual Studio 2017 eltávolításakor.  Ez a művelet létrehoz az létrehozásakor, és új felhőalapú szolgáltatások projekteket a Visual Studio 2015-hibakeresés hiba. Ahhoz, hogy a probléma megoldásához telepítse újra az Azure SDK a Webplatform-telepítővel.  A probléma fel lesz oldva az egy későbbi kiadásban Visual Studio 2017.  .

 
### <a name="azure-in-role-cache"></a>Azure szerepköralapú gyorsítótár 

- Azure szerepköralapú gyorsítótár terméktámogatás November 30 2016. További információért kattintson [Itt](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).




