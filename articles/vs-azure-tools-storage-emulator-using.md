---
title: A Tároló emulátor konfigurálása és használata a Visual Studio segítségével | Microsoft dokumentumok
description: Konfigurálása és használata a tárolási emulátor, egy segédprogram, amely szimulálja a Blob, várólista és table tárolási szolgáltatások elérhető az Azure-ban a helyi fejlesztői gépen.
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
ms.openlocfilehash: a6f853924416cce2440ca15767044029b20e651f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75450725"
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>A tárolóemulátor konfigurálása és használata a Visual Studio segítségével

[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Áttekintés

Az Azure SDK-fejlesztési környezet tartalmazza a tárolási emulátort, egy segédprogramot, amely szimulálja a blob, várólista és table tárolási szolgáltatások elérhető az Azure-ban a helyi fejlesztői gépen. Ha olyan felhőszolgáltatást hoz fel, amely az Azure storage-szolgáltatásokat használja, vagy bármilyen külső alkalmazást ír, amely meghívja a tárolási szolgáltatásokat, helyileg tesztelheti a kódot a tároló emulátorával szemben. Az Azure Tools for Microsoft Visual Studio integrálja a storage-emulátor kezelését a Visual Studióba. Az Azure Tools első használatkor inicializálja a tároló-emulátor-adatbázist, elindítja a storage-emulátor-szolgáltatást, amikor futtatja vagy hibakeresésa a kódot a Visual Studióból, és csak olvasható hozzáférést biztosít a tárolóemulátor adataihoz az Azure Storage Exploreren keresztül.

A tárolóemulátorról, beleértve a rendszerkövetelményeket és az egyéni konfigurációs utasításokat, [az Azure Storage-emulátor használata fejlesztési és tesztelési célokra](storage/common/storage-use-emulator.md)című témakörben talál részletes információt.

> [!NOTE]
> A storage-emulátor-szimuláció és az Azure storage-szolgáltatások között bizonyos funkciók vannak. Az Azure SDK-dokumentációban [a storage-emulátor és az Azure Storage Services közötti különbségek](storage/common/storage-use-emulator.md) című témakörben tájékozódhat a konkrét különbségekről.

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Kapcsolati karakterlánc konfigurálása a tárolóemulátorhoz

A storage emulátor elérése egy szerepkörön belüli kód, érdemes konfigurálni a kapcsolati karakterlánc, amely rámutat a storage-emulátor, és amely később módosítható, hogy pont egy Azure storage-fiók. A kapcsolati karakterlánc olyan konfigurációs beállítás, amelyet a szerepkör futásidőben olvashat egy tárfiókhoz való csatlakozáshoz. A kapcsolati karakterláncok létrehozásáról az [Azure Storage-kapcsolati karakterláncok konfigurálása](/azure/storage/common/storage-configure-connection-string)című témakörben talál további információt.

> [!NOTE]
> A **DevelopmentStorageAccount** tulajdonság használatával visszaadhat egy hivatkozást a tárterület-emulátorfiókra a kódból. Ez a megközelítés megfelelően működik, ha a kódból szeretné elérni a táremulátort, de ha azt tervezi, hogy közzéteszi az alkalmazást az Azure-ban, létre kell hoznia egy kapcsolati karakterláncot az Azure storage-fiók eléréséhez, és módosítania kell a kódot a kapcsolat használatához karakterláncot, mielőtt közzétenél. Ha gyakran vált a storage-emulátorfiók és az Azure storage-fiók között, a kapcsolati karakterlánc leegyszerűsíti ezt a folyamatot.

## <a name="initializing-and-running-the-storage-emulator"></a>A tárolóemulátor inicializálása és futtatása

Megadhatja, hogy amikor futtatja vagy hibakeresés a szolgáltatás a Visual Studio, A Visual Studio automatikusan elindítja a tároló emulátor. A Megoldáskezelőben nyissa meg az **Azure-projekt** helyi menüjét, és válassza a **Tulajdonságok lehetőséget.** A **Fejlesztés** lapon az **Azure Storage-emulátor indítása** listában válassza az **Igaz** lehetőséget (ha még nincs beállítva erre az értékre).  Egyes projekttípusoknem rendelkeznek a **Fejlesztés** lapval. Ebben az esetben engedélyezheti vagy letilthatja a tárolóemulátor `StartDevelopmentStorage` indítását a projektfájl elemének beállításával. Állítsa **igaz** értékre az engedélyezéséhez, vagy **hamis** értékre a letiltásához.  Egy Azure Functions projektben például nyissa meg a projektfájlt szerkesztésre, és módosítsa az XML-kódot az alábbiak szerint:

```xml
  <PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <AzureFunctionsVersion>v2</AzureFunctionsVersion>
    <StartDevelopmentStorage>True</StartDevelopmentStorage>
  </PropertyGroup>
```

Amikor először futtatja vagy hibakeresés közben futtatja a szolgáltatást a Visual Studióból, a tárolóemulátor inicializálási folyamatot indít el. Ez a folyamat helyi portokat foglal le a tárolóemulátor számára, és létrehozza a tárolóemulátor-adatbázist. A folyamat befejezését követően a folyamatnak csak akkor kell újra futnia, ha a tárolóemulátor-adatbázis törlődik.

> [!NOTE]
> Az Azure Tools 2012. Az Azure Tools korábbi kiadásaiban a storage-emulátor az SQL Express 2005 vagy 2008 alapértelmezett példányán fut, amelyet telepítenie kell az Azure SDK telepítése előtt. A tárolóemulátort az SQL Express elnevezett példányán vagy a Microsoft SQL Server megnevezett vagy alapértelmezett példányán is futtathatja. Ha konfigurálnia kell a tárolóemulátort, hogy az alapértelmezett példánytól eltérő példányellen fusson, olvassa [el az Azure Storage-emulátor használata fejlesztési és tesztelési célokra](storage/common/storage-use-emulator.md)című témakört.

A tárolóemulátor felhasználói felületet biztosít a helyi tárolási szolgáltatások állapotának megtekintéséhez, valamint indításához, leállításához és alaphelyzetbe állításához. A tárolóemulátor szolgáltatás elindítását követően megjelenítheti a felhasználói felületet, vagy elindíthatja vagy leállíthatja a szolgáltatást, ha a jobb gombbal a Windows tálcán a Microsoft Azure emulátor értesítési terület ikonjára kattint.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Tárolóemulátor adatok megtekintése a Kiszolgálókezelőben

Az Azure Storage-csomópont a Kiszolgáló-kezelőben lehetővé teszi a blob- és táblaadatok beállításainak megtekintését és módosítását a tárfiókokban, beleértve a storage-emulátort is. További [információ: Az Azure Blob Storage-erőforrások kezelése a Storage Explorer rel](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs) című témakörben található.

