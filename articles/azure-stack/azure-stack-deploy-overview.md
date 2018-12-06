---
title: Az Azure Stack Development Kit értékelése |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe az Azure Stack Development Kit értékelési célokra.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/05/2018
ms.author: jeffgilb
ms.custom: mvc
ms.reviewer: misainat
ms.openlocfilehash: 44fed3311234e1a64cb46c3403f39a9e269d189b
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956925"
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Gyors útmutató: az Azure Stack Development Kit kiértékelése

A [Azure Stack Development Kit (ASDK)](./asdk/asdk-what-is.md) egy tesztelési és fejlesztési környezet, amely központilag telepíthető kiértékeléséhez, és az Azure Stack-szolgáltatások és szolgáltatások bemutatásához. Első lépések a ASDK, meg kell a gazdagép előkészítése a számítógép hardveréről és néhány parancsprogramot (telepítés néhány órát vesz igénybe) futtassa. Ezt követően bejelentkezhet a rendszergazda vagy a felhasználói portált az Azure Stack használatának megkezdéséhez.

## <a name="prerequisites"></a>Előfeltételek

### <a name="asdk-host-computer-requirements"></a>ASDK gazdagép számítógépekre vonatkozó követelmények

Mielőtt telepítené a ASDK, készítse elő a számítógépet, amely a csomag fogja futtatni kell. A fejlesztői csomag gazdagép számítógépnek meg kell felelnie, hogy a hardverek, szoftverek, és a hálózati követelmények ismertetett  **[tekintse át a tervezési szempontok a ASDK telepítés](./asdk/asdk-deploy-considerations.md)**.

> [!TIP]
> Használhatja a [Azure Stack üzembe helyezési követelményeket ellenőrizze az eszköz](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) a gazdagépen development kit, győződjön meg arról, hogy a hardver összes követelményének megfelel-e az operációs rendszer telepítése után.

### <a name="account-requirements"></a>Fiókra vonatkozó követelmények

Is szüksége lesz az Azure Active Directory (Azure AD) vagy az Active Directory összevonási szolgáltatások (AD FS) használatával választhat az identitáskezelési megoldásként az üzembe helyezés. Tekintse át a fiókra vonatkozó követelmények a  **[üzembe helyezési, tervezési szempontok](./asdk/asdk-deploy-considerations.md#account-requirements)**

## <a name="download-and-extract-the-deployment-package"></a>Töltse le és csomagolja ki a központi telepítési csomag

A fejlesztői csomag gazdaszámítógépet előkészítését, követően töltse le, és a ASDK központi telepítési csomag kicsomagolásához. A központi telepítési csomag tartalmazza a Cloudbuilder.vhdx fájlt, amely egy virtuális merevlemez (VHD) egy rendszerindításra alkalmas operációs rendszerrel, és az Azure Stack telepítési fájljait.

Letöltheti a központi telepítési csomag development kit gazdagépre vagy egy másik számítógépre. A kibontott üzembe helyezési fájlokat is igénybe vehet 60 GB szabad lemezterület szükséges, ezért egy másik számítógép segítségével csökkenthető a tárolási követelményeit a development kit gazdagépen.

**[Töltse le és csomagolja ki az Azure Stack Development Kit (ASDK)](./asdk/asdk-download.md)**

## <a name="prepare-the-host-computer"></a>A számítógép előkészítése

A ASDK telepítése előtt elő kell készíteni a gazdakörnyezetben, és a rendszer a development kit VHD rendszerindító konfigurálva. A gazdagép újraindításakor CloudBuilder.vhdx az indításkor, és elkezdheti a ASDK üzembe helyezéséhez.

**[A ASDK számítógép előkészítése](./asdk/asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>A gazdagépen a ASDK telepítése

Miután a számítógép elindul a VHD-ből, telepíthet a csomag a Cloudbuilder virtuális környezethez. Telepítheti a használatával a grafikus felhasználói felülettel, asdk-installer.ps1 PowerShell-parancsprogram futtatásával, vagy a ASDK [a PowerShell-parancssorból](./asdk/asdk-deploy-powershell.md)

> [!NOTE]
> Miután a gazdagép a Cloudbuilder.vhdx rendszerképből indul el, lehetősége van konfigurálásának [Azure Stack telemetriai beállítások](./asdk/asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *előtt* a ASDK telepítése.

**[Az Azure Stack Development Kit (ASDK) telepítése](./asdk/asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>Üzembe helyezés utáni konfigurációk végrehajtása

Miután telepítette a ASDK, nincsenek néhány ajánlott telepítés utáni ellenőrzést és konfigurációs változásokat, mértékét.

**Eszközök**

Az Azure Stack PowerShell és a GitHub-eszközök telepítése, majd ellenőrizze a telepítést, a teszt-AzureStack parancsmaggal sikeres.

**Identitáskezelési megoldás**

Az Azure ad-ben használó központi telepítések mind az Azure Stack rendszergazdai és bérlői portálok aktiválnia kell. Az aktiválás járul hozzá engedélyeket ad az Azure Stack portálon és az Azure Resource Manager a megfelelő (a jóváhagyás lapon felsorolt) az összes felhasználó számára a címtár.

**Jelszó lejárati ideje**

A jelszó-elévülési szabályzatának, győződjön meg arról, hogy a jelszó a development kit gazdagép le nem jár, a kiértékelési időszak vége előtt vissza kell állítania.

> [!NOTE]
> Akkor is beállíthatja [Azure Stack telemetriai beállítások](./asdk/asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *után* a ASDK telepítése.

**[POST-ASDK központi telepítésének feladatai](./asdk/asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Regisztráljon az Azure-ral

Regisztrálnia kell az Azure Stack az Azure-ral, hogy [töltse le az Azure marketplace-elemek](./asdk/asdk-marketplace-item.md) az Azure Stackhez.

**[Regisztráljon az Azure Stack az Azure-ral](./asdk/asdk-register.md)**

## <a name="next-steps"></a>További lépések

Gratulálunk! Ebben a rövid lépéseket követve tartalmazó ASDK környezet rendelkezik egy [rendszergazda](https://adminportal.local.azurestack.external) portál és a egy [felhasználói](https://portal.local.azurestack.external) portálon.
