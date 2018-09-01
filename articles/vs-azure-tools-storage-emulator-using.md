---
title: Konfigurálása és a Storage Emulator használata a Visual Studióval |} A Microsoft Docs
description: A Storage Emulator használatával a Visual Studióval és konfigurálása
services: visual-studio-online
author: ghogen
manager: douge
assetId: c8e7996f-6027-4762-806e-614b93131867
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/17/2017
ms.author: ghogen
ms.openlocfilehash: 4b14fa44f484735f2a5efee4d631a6f48e6a8bf6
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382636"
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>A Storage Emulator használatával a Visual Studióval és konfigurálása
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Áttekintés
Az Azure SDK-t fejlesztési környezet magában foglalja a storage emulatort, egy segédprogram, amely szimulálja a Blob, Queue és Table storage szolgáltatások a helyi fejlesztői gépen elérhető az Azure-ban. Ha Ön felhőszolgáltatást fejleszt az Azure storage szolgáltatások alkalmaz, vagy bármilyen külső alkalmazás, amely meghívja a tárolási szolgáltatások írása, tesztelheti a kódját a storage emulator helyileg. A Microsoft Visual Studióhoz készült Azure-eszközök kezelése a storage emulator integrálja a Visual Studio. A storage emulator adatbázis az első használatkor inicializálása, elindul a storage emulator szolgáltatás futtatásakor vagy a Visual Studio-kód hibaelhárítása és a storage emulator adatokat az Azure Storage Explorer használatával csak olvasható hozzáférést biztosít az Azure-eszközöket.

A storage emulatort, többek között a rendszerkövetelményeket és egyéni konfigurációs utasításokat, kapcsolatos részletes információkért lásd: [fejlesztés és tesztelés az Azure Storage Emulator használata](storage/common/storage-use-emulator.md).

> [!NOTE]
> Nincsenek a funkciók a storage emulator szimuláció és az Azure storage szolgáltatások közötti különbségeket. Lásd: [különbségek között a Storage Emulator és az Azure Storage Services](storage/common/storage-use-emulator.md) információ a konkrét különbségeket az Azure SDK dokumentációját.
> 
> 

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>A storage Emulator kapcsolati karakterlánc konfigurálása
A storage emulator eléréséhez kódból szerepkörökben, érdemes konfigurálhat egy kapcsolati karakterláncot, amely a storage emulator mutat, és, amelyek később módosítható átirányítása egy Azure storage-fiókot. Kapcsolati karakterlánc egy konfigurációs beállítás, amely egy storage-fiókhoz való csatlakozáshoz futásidőben a szerepkör is. Kapcsolati karakterláncok létrehozásával kapcsolatos további információkért lásd: [konfigurálása az Azure Storage kapcsolati karakterláncok](/azure/storage/common/storage-configure-connection-string).

> [!NOTE]
> Lépjen vissza egy hivatkozást a tárfiókra emulátor a kódból használatával a **DevelopmentStorageAccount** tulajdonság. Ez a megközelítés megfelelően működik, ha el szeretne érni a storage emulator a kódból, de ha azt tervezi, közzéteheti az alkalmazását az Azure-ba, szüksége lesz az Azure storage-fiókját, és módosítsa a kódot, hogy a kapcsolat használata a kapcsolati karakterlánc létrehozása karakterlánc közzététel előtt. Ha vált a storage emulator fiók és a egy Azure storage-fiók között gyakran, egy kapcsolati karakterláncot a folyamat leegyszerűsítése érdekében.
> 
> 

## <a name="initializing-and-running-the-storage-emulator"></a>Inicializálása és futtatása a storage emulator
Megadhatja, hogy futtatásakor vagy a szolgáltatás hibakeresése a Visual Studio, Visual Studio automatikusan elindul a storage emulatort. A Megoldáskezelőben nyissa meg a helyi menüjének a **Azure** projektre, és válassza a **tulajdonságok**. Az a **fejlesztési** lap a **indítsa el az Azure Storage Emulator** menüben válassza ki **igaz** (Ha már ezt az értéket nincs beállítva).

Az első alkalommal futtat vagy a Visual Studióban válassza a szolgáltatás hibakeresése a storage emulator elindítja az inicializálási folyamatot. Ez a folyamat helyi portok fenntartja a storage Emulator és a storage emulator adatbázist hoz létre. Ha elkészült, ezt a folyamatot nem kell újra futtatni, kivéve, ha a storage emulator adatbázisa törlődik.

> [!NOTE]
> Az Azure-eszközök a 2012. júniusi kiadásától kezdődően, a storage emulatort, alapértelmezés szerint fut, az SQL Express LocalDB. A korábbi kiadásokban az Azure-eszközök, a storage emulator SQL Express 2005 vagy 2008 alapértelmezett példánya fut is, amelyet telepíteni kell, mielőtt az Azure SDK telepítése. A storage emulator egy nevesített példányára, az SQL Express és a egy elnevezett vagy a Microsoft SQL Server alapértelmezett példányát is futtathatja. Ha be kell állítania egy példányt az alapértelmezett példány nem futtatható, olvassa el a storage emulator [fejlesztés és tesztelés az Azure Storage Emulator használata](storage/common/storage-use-emulator.md).
> 
> 

A storage emulator a helyi szolgáltatások állapotának megtekintése és indítása, leállítása és alaphelyzetbe állítsa őket egy felhasználói felületet biztosít. A storage emulator szolgáltatás elindítása után a felhasználói felület megjelenítése vagy elindításához, vagy állítsa le a szolgáltatást, kattintson a jobb gombbal az ikonra a Microsoft Azure Emulator a Windows tálcán.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Tár emuláló adatok megtekintése a Server Explorerben
Az Azure Storage-csomópont a Server Explorerben lehetővé teszi adatok megtekintése és módosítása a blob és a táblák adatait a tárfiókokban, beleértve a storage emulator beállításait. Lásd: [a Storage Explorer használatával kezelheti az Azure Blob Storage-erőforrások](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs) további információt.

