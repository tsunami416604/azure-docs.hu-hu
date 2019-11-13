---
title: A Storage Emulator konfigurálása és használata a Visual Studióval | Microsoft Docs
description: A Storage Emulator konfigurálása és használata, amely szimulálja az Azure-ban elérhető blob-, üzenetsor-és Table Storage-szolgáltatásokat a helyi fejlesztési gépen.
services: visual-studio-online
author: ghogen
manager: jillfra
assetId: c8e7996f-6027-4762-806e-614b93131867
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/17/2017
ms.author: ghogen
ms.openlocfilehash: aca4bf5017a4ee23d69016b937673443c1a0e200
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73935836"
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>A Storage Emulator konfigurálása és használata a Visual Studióval
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Áttekintés
Az Azure SDK fejlesztői környezet magában foglalja a Storage emulatort, amely az Azure-ban elérhető blob-, üzenetsor-és Table Storage-szolgáltatásokat szimulálja a helyi fejlesztői gépen. Ha olyan felhőalapú szolgáltatást fejleszt, amely az Azure Storage szolgáltatásokat alkalmazza, vagy bármilyen külső alkalmazást ír, amely meghívja a tárolási szolgáltatásokat, akkor a kódot helyileg tesztelheti a Storage emulatorban. A Microsoft Visual studióhoz készült Azure-eszközök integrálják a Storage emulatort a Visual studióba. Az Azure-eszközök az első használat során inicializálják a Storage Emulator-adatbázist, elindítja a Storage Emulator szolgáltatást a kód a Visual studióból való futtatásakor vagy hibakeresése során, valamint csak olvasási hozzáférést biztosít a Storage Emulator-adatokhoz a Azure Storage Explorer használatával.

A Storage Emulator részletes ismertetését, beleértve a rendszerkövetelményeket és az egyéni konfigurációs utasításokat lásd: [Az Azure Storage Emulator használata fejlesztéshez és teszteléshez](storage/common/storage-use-emulator.md).

> [!NOTE]
> A Storage Emulator-szimuláció és az Azure Storage-szolgáltatások között számos különböző funkció létezik. A konkrét különbségekről a [Storage Emulator és az Azure Storage szolgáltatások közötti különbségek](storage/common/storage-use-emulator.md) az Azure SDK dokumentációjában olvashatók.
> 
> 

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>A Storage emulatorhoz tartozó kapcsolatok karakterláncának konfigurálása
Ha egy szerepkörön belül szeretné elérni a Storage-emulátort, konfigurálnia kell egy olyan kapcsolati karakterláncot, amely a Storage emulatorra mutat, és később egy Azure Storage-fiókra mutat. A kapcsolati karakterlánc olyan konfigurációs beállítás, amelyet a szerepkör a futtatókörnyezetben tud olvasni a Storage-fiókhoz való kapcsolódáshoz. A kapcsolódási karakterláncok létrehozásával kapcsolatos további információkért lásd: az [Azure Storage kapcsolódási karakterláncok konfigurálása](/azure/storage/common/storage-configure-connection-string).

> [!NOTE]
> A **DevelopmentStorageAccount** tulajdonság használatával a kód alapján a Storage Emulator-fiókra mutató hivatkozást adhat vissza. Ez a megközelítés megfelelően működik, ha a kód alapján szeretné elérni a Storage emulatort, de ha azt tervezi, hogy közzéteszi az alkalmazást az Azure-ban, létre kell hoznia egy kapcsolati karakterláncot az Azure Storage-fiók eléréséhez, és módosítania kell a kódot a kapcsolat használatára a karakterláncot a közzététel előtt. Ha a Storage Emulator-fiók és az Azure Storage-fiók közötti váltás gyakran történik, a kapcsolati karakterlánc leegyszerűsíti ezt a folyamatot.
> 
> 

## <a name="initializing-and-running-the-storage-emulator"></a>A Storage Emulator inicializálása és futtatása
Megadhatja, hogy a Visual Studióban a szolgáltatás futtatásakor vagy hibakereséskor a Visual Studio automatikusan elindítja a Storage emulatort. A Megoldáskezelőban nyissa meg az **Azure** -projekt helyi menüjét, és válassza a **Tulajdonságok**lehetőséget. A **fejlesztés** lap **Azure Storage-emulátor elindítása** listájában válassza az **igaz** lehetőséget (ha még nincs beállítva erre az értékre).

Amikor először futtatja vagy végzi a szolgáltatást a Visual studióból, a Storage-emulátor inicializálási folyamatot indít el. Ez a folyamat fenntartja a Storage Emulator helyi portjait, és létrehozza a Storage Emulator-adatbázist. Ha elkészült, a folyamatnak nem kell újra futnia, hacsak nem törli a Storage Emulator-adatbázist.

> [!NOTE]
> Az Azure-eszközök június 2012-os kiadásával kezdődően a Storage Emulator alapértelmezés szerint az SQL Express LocalDB fut. Az Azure-eszközök korábbi kiadásaiban a Storage Emulator az SQL Express 2005 vagy a 2008 alapértelmezett példányán fut, amelyet az Azure SDK telepítése előtt telepítenie kell. A Storage emulatort futtathatja az SQL Express megnevezett példányán vagy a Microsoft SQL Server elnevezett vagy alapértelmezett példányán is. Ha a Storage emulatort úgy kell konfigurálni, hogy az az alapértelmezett példánytól eltérő példányon fusson, tekintse meg [a fejlesztés és tesztelés az Azure Storage Emulator használatával](storage/common/storage-use-emulator.md)című témakört.
> 
> 

A Storage Emulator felhasználói felületet biztosít a helyi tárolási szolgáltatások állapotának megtekintéséhez, valamint az elindításához, leállításához és alaphelyzetbe állításához. A Storage Emulator szolgáltatás elindítása után megtekintheti a felhasználói felületet, vagy elindíthatja vagy leállíthatja a szolgáltatást. ehhez kattintson a jobb gombbal az értesítési területen látható ikonra a Windows tálcán a Microsoft Azure emulátorhoz.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>A Storage Emulator-információinak megtekintése a Server Explorerben
A Server Explorer Azure Storage csomópontja lehetővé teszi, hogy megtekintse a Storage-fiókokban lévő blob-és Table-adat adatait, és módosítsa a beállításokat, beleértve a Storage emulatort is. További információ: az [Azure Blob Storage-erőforrások kezelése Storage Explorerokkal](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs) .

