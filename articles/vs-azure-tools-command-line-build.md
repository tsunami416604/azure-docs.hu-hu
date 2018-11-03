---
title: Az Azure parancssori build |} A Microsoft Docs
description: Az Azure parancssori build
services: visual-studio-online
author: ghogen
manager: douge
assetId: 94b35d0d-0d35-48b6-b48b-3641377867fd
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/05/2017
ms.author: ghogen
ms.openlocfilehash: f471e912aeaf392f1c7d29f9606c174bf90c18c6
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969299"
---
# <a name="building-azure-projects-from-the-command-line"></a>A parancssorból Azure-projektek készítése
A Microsoft Build Engine (MSBuild) használ, a build-tesztlabor-környezetekben, ahol nincs telepítve a Visual Studio termékek hozhat létre. MSBuild bővíthető és teljes mértékben a Microsoft által támogatott soubory projektu egy XML formátumú használ. Használja az MSBuild fájlformátum, leírhatja elemeket kell egy vagy több platformon, és konfigurációkat.

MSBuild is futtathatja a parancssorból, és ez a témakör ismerteti azt a módszert. A parancssori tulajdonságok beállításával hozhat létre egy projekt konfigurációkkal. Ehhez hasonlóan megadhat, amelyeknek MSBuild épít. Parancssori paraméterek és MSBuild kapcsolatos további információkért lásd: [MSBuild parancssori útmutatóban](https://msdn.microsoft.com/library/ms164311.aspx).

## <a name="msbuild-parameters"></a>MSBuild-paraméterek
Hozzon létre egy csomagot a legegyszerűbb módja az, hogy az MSBuild futtassa a `/t:Publish` lehetőséget. Alapértelmezés szerint ez a parancs létrehoz egy könyvtárat a projekthez, amely a gyökérmappában található viszonyítva például `<ProjectDirectory>\bin\Configuration\app.publish\`. Ha hoz létre az Azure-projekttel, jönnek létre két fájlt: a csomag fájlt saját maga és a hozzájuk tartozó konfigurációs fájlt:

* Csomagfájl (`project.cspkg`)
* Konfigurációs fájl (`ServiceConfiguration.TargetProfile.cscfg`)

Alapértelmezés szerint minden egyes Azure-projekt tartalmazza, egy szolgáltatás-konfigurációs fájlt a helyi (Hibakeresés) buildek és a egy másik a felhő (előkészítési vagy termelési) buildek. Azonban adja hozzá, vagy távolítsa el a szolgáltatás-konfigurációs fájlokat, igény szerint. Ha egy csomagot a Visual Studión belül hoz létre, mely szolgáltatás-konfigurációs fájl mellett a csomag tartalmazza a rendszer felkéri. Ha egy csomag MSBuild használatával hoz létre, alapértelmezés szerint a helyi szolgáltatás-konfigurációs fájl tartalmazza. Adja meg egy másik szolgáltatás-konfigurációs fájlt, állítsa be a `TargetProfile` az MSBuild parancs tulajdonságát (`MSBuild /t:Publish /p:TargetProfile=ProfileName`).

Ha szeretne egy másik címtár használata a tárolt csomag és a konfigurációs fájlok, állítsa be az elérési út használatával a `/p:PublishDir=Directory\` beállítást, többek között a záró fordított perjel elválasztó.

## <a name="next-steps"></a>További lépések
A csomagot a létrehozása után telepítheti az Azure-bA.