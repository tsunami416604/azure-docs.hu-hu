---
title: "A Storage Emulator használatával a Visual Studio és konfigurálása |} Microsoft Docs"
description: "A Storage Emulator használatával a Visual Studio és konfigurálása"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: c8e7996f-6027-4762-806e-614b93131867
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/17/2017
ms.author: kraigb
ms.openlocfilehash: f4cd8ccc3b186cf2b4178b7d8a98d8928c705cbc
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>A Storage Emulator használatával a Visual Studio és konfigurálása
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Áttekintés
Az Azure SDK fejlesztési környezet magában foglalja a storage emulator egy segédprogram, amely a Blob, Queue és Table storage az elérhető szolgáltatások az Azure-ban a helyi fejlesztési számítógépén szimulálja. Ha egy felhőalapú szolgáltatás létrehozása, amely alkalmazza az Azure storage szolgáltatásainak, vagy minden külső alkalmazás, amely behívja a tárolási szolgáltatások írása, tesztelheti a storage emulatorban helyileg a kódot. A Microsoft Visual Studio Azure eszközei integrálja a storage emulator ebbe a Visual Studio. Az Azure-eszközök a storage emulator adatbázis első használatkor inicializálása, a storage emulator szolgáltatás akkor kezdődik, amikor elindítja vagy hibakeresése a Visual Studio code, és a storage emulator adatot Azure Tártallózó olvasási hozzáférést biztosít.

A storage emulator, beleértve a telepítési rendszerkövetelményeket és egyéni konfigurációs utasításokat, részletes információkat lásd: [fejlesztés és tesztelés az Azure Storage Emulator használata](storage/common/storage-use-emulator.md).

> [!NOTE]
> Néhány funkció a storage emulator szimuláció és az Azure storage szolgáltatások közötti különbségek vannak. Lásd: [különbségek között a Storage Emulator és az Azure Storage szolgáltatásainak](storage/common/storage-use-emulator.md) az Azure SDK dokumentációjában találhat információt a speciális eltéréseket.
> 
> 

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>A storage Emulator kapcsolati karakterlánc konfigurálása
A storage emulator elérje a szerepkörön belüli kódot, érdemes a storage emulator mutat, és, hogy később módosítható mutasson az Azure-tárfiók kapcsolati karakterláncának konfigurálása. A kapcsolati karakterlánc: a konfigurációs beállítások megadásához a szerepkör futásidőben a tárfiók való kapcsolódáshoz. Kapcsolati karakterláncok létrehozásával kapcsolatos további információkért lásd: [konfigurálása az Azure alkalmazás](https://msdn.microsoft.com/library/azure/2da5d6ce-f74d-45a9-bf6b-b3a60c5ef74e#BK_SettingsPage).

> [!NOTE]
> A felhasználói kódból emulátor tárfiók mutató hivatkozás segítségével visszatérhet a **DevelopmentStorageAccount** tulajdonság. Ez a megközelítés megfelelően működik, ha el szeretne érni a storage emulator a felhasználói kódból, de ha azt tervezi, tegye közzé az alkalmazást az Azure-ba, szüksége lesz egy kapcsolati karakterláncot az Azure storage-fiókját, és módosítsa a kapcsolati karakterlánc használatához a közzététel előtt kódot létrehozni. Vált a storage emulator fiókot és egy Azure storage-fiók között gyakran, ha egy kapcsolati karakterláncot a folyamat leegyszerűsítése érdekében.
> 
> 

## <a name="initializing-and-running-the-storage-emulator"></a>Inicializálása és futtatása a storage emulator
Megadhatja, hogy futtatásakor vagy a szolgáltatás a Visual Studio hibakeresési, Visual Studio automatikusan elindítja a storage emulator. A Solution Explorerben nyissa meg a helyi menüje a **Azure** projektre, és válassza a **tulajdonságok**. Az a **fejlesztési** lap a **indítsa el az Azure Storage Emulator** menüben válassza ki **igaz** (ha az értékhez már nincs beállítva).

Az első alkalommal futtat vagy hibakeresése a Visual Studio, a szolgáltatás a storage emulator elindítja az inicializálási folyamatot. Ez a folyamat a helyi portok fenntartja a storage Emulator, és a storage emulator adatbázist hoz létre. Művelet befejeződése után ez a folyamat nem kell újra fut, kivéve a storage emulator adatbázisa törlődik.

> [!NOTE]
> Az Azure-eszközök a 2012. júniusi kiadástól kezdve, a storage emulator fut, alapértelmezés szerint az SQL Express localdb programba. A korábbi kiadásokban az Azure-eszközök, a storage emulator fut az SQL Express 2005 vagy 2008 alapértelmezett példányán előtt telepítenie kell, amely telepíthető az Azure SDK-t. A storage emulator egy SQL Express vagy egy nevesített megnevezett példánya vagy a Microsoft SQL Server alapértelmezett példányát is futtathatja. Ha szeretne futtatni egy példány nem az alapértelmezett példány, olvassa el a storage emulator konfigurálnia kell [fejlesztés és tesztelés az Azure Storage Emulator használata](storage/common/storage-use-emulator.md).
> 
> 

A storage emulator a helyi tárterület-szolgáltatások állapotának megtekintése és indítása, leállítása, és visszaállíthatja őket felhasználói felülete lehetőséget nyújt. A storage emulator szolgáltatás elindítása után a felhasználói felület megjelenítése vagy elindításához, vagy állítsa le a szolgáltatást a Microsoft Azure Emulator a Windows tálcán található az értesítési területen megjelenő ikonra kattint.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>A Server Explorer storage emulator adatok megtekintése
Az Azure Storage-csomópont a Server Explorer lehetővé teszi a storage-fiókok, beleértve a storage emulator blob és a tábla adatainak beállítások adatok megtekintése és módosítása. Lásd: [a Tártallózó (előzetes verzió) kezelése az Azure Blob Storage-erőforrások](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs) további információt.

