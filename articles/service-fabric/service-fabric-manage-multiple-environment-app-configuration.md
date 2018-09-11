---
title: Alkalmazások kezelése több környezethez az Azure Service Fabricben |} A Microsoft Docs
description: Az Azure Service Fabric-alkalmazások futtatható fürtökön tartományban lévő méretű egyik gépről ezer gépre. Bizonyos esetekben célszerű az alkalmazás másképp változatos szkriptjét konfigurálása. Ez a cikk bemutatja, hogyan környezetenként különböző alkalmazás-paraméterek megadásához.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: dac96ef6fce38a0557444e181fa6eccb649cfb9a
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44298335"
---
# <a name="manage-applications-for-multiple-environments"></a>Alkalmazások kezelése több környezethez

Az Azure Service Fabric-fürtöket hozhat létre fürtöket egy és több ezer bárhol használatával engedélyezése gépek. A legtöbb esetben észre magát az alkalmazás telepítése több fürtkonfigurációk között kellene: a helyi fejlesztési fürt, egy közös fejlesztési fürtöt és az éles fürtöt. Ezek a fürtök összes minősülnek különböző környezetekben a kód futtatása rendelkezik. Alkalmazás bináris fájljainak módosítás nélkül is futtathatja a széles skáláját, de gyakran konfigurálni szeretné az alkalmazás eltérő.

Két egyszerű példa, vegye figyelembe:
  - a szolgáltatás egy meghatározott portot figyeli, de van szüksége erre a portra kell lennie a környezetek között
  - meg kell adnia a másik kötés hitelesítő adatokat egy adatbázis a környezetek között

## <a name="specifying-configuration"></a>Konfiguráció megadása

Azt adja meg a konfiguráció két kategóriába oszthatók:

- Konfigurációt, amely érvényes a szolgáltatások futtatásának módját
  - Ha például a portnak a számát egy végpontot, illetve egy szolgáltatás példányainak számát
  - Ez a konfiguráció van megadva, az alkalmazás vagy szolgáltatás Alkalmazásjegyzék-fájl
- Konfigurációt, amely az alkalmazás kódja érvényes
  - Egy adatbázis például kötési információ
  - Ezt a konfigurációt vagy konfigurációs fájlok vagy a környezeti változók adható meg

> [!NOTE]
> Nem minden attribútumokat az alkalmazás és a service manifest fájlparaméterek támogatása.
> Ezekben az esetekben kell karakterláncok helyettesítse az üzembe helyezést megvalósító munkafolyamat részeként támaszkodnak. Az Azure DevOps használhatja például a jogkivonatok cserélje le egy bővítmény: https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens vagy a jenkins kifejezést cserélje le az értékeket egy parancsfájl feladat futtatásával.
>

## <a name="specifying-parameters-during-application-creation"></a>Alkalmazás létrehozása során paramétereinek megadása

Amikor a Service Fabric egy elnevezett alkalmazáspéldányok hoz létre, lehetősége van átadni a paraméterek. Ezt megteheti módja attól függ, hogyan hoz létre az alkalmazáspéldány.

  - A PowerShell a [ `New-ServiceFabricApplication` ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) parancsmagnál az alkalmazás paramétereit egy kivonattáblát.
  - Sfctl, használja a [ `sfctl application create` ](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) parancs paraméter szükséges, egy JSON-karakterláncot. A szkript install.sh sfctl.
  - A Visual Studio tartalmaz, amelyek paraméter-fájlokat a projekt paraméterek mappájában. Közzététel a Visual Studióból az Azure DevOps-szolgáltatásokkal vagy Team Foundation Server használata esetén ezek paraméter fájljait használja. A Visual Studióban a paraméterfájlok alatt pedig továbbítva lesznek az üzembe helyezés – FabricApplication.ps1 parancsfájl.

## <a name="next-steps"></a>További lépések
A következő cikkek bemutatják, hogyan használhatja néhány ismertetett alapfogalmak itt:

- [A Service Fabric Services környezeti változók megadása](service-fabric-how-to-specify-environment-variables.md)
- [A port számát, a paraméterek használatával a Service Fabric szolgáltatás megadása](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Hogyan lehet paraméterezni a konfigurációs fájlok](service-fabric-how-to-parameterize-configuration-files.md)

- [Környezeti változó hivatkozás](service-fabric-environment-variables-reference.md)
