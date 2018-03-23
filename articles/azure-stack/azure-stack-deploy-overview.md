---
title: Értékelje ki a Azure verem szoftverfejlesztői készlet |} Microsoft Docs
description: Megtudhatja, hogyan telepítheti az Azure verem szoftverfejlesztői készlet tesztelési célokra.
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
ms.date: 03/22/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: 1deabcf64b3fbf3cbc89232c340a8882cd2591e8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Gyors üzembe helyezés: Az Azure verem szoftverfejlesztői készlet kiértékelése
A [Azure verem Development Kit (ASDK)](.\asdk\asdk-what-is.md) egy tesztelési és fejlesztési környezet, amely értékeli, és bemutatja a verem Azure-szolgáltatások és szolgáltatások telepítése. A ASDK megkezdéséhez, meg kell készítse elő a fogadó számítógép hardvere, majd futtassa az egyes parancsfájlok (a telepítés több óráig tart). Ezt követően is bejelentkezik a rendszergazdai és felhasználói portálok Azure verem elindítására.

## <a name="prerequisites"></a>Előfeltételek 
A ASDK a telepítés előtt elő kell készíteni a szoftverfejlesztői készlet (development kit fogadó) futtató számítógépre. A development kit gazdaszámítógép minimális hardver-, szoftver, és hálózati követelményeknek kell megfelelniük. 

Szükség Azure Active Directory (Azure AD) vagy Active Directory összevonási szolgáltatások (AD FS) használatával választhat a identitáskezelési megoldásként az üzembe helyezéshez. 

Győződjön meg arról, hogy a development kit gazdagép teljesíti-e a minimális hardverkövetelményeknek, és el, hogy a telepítési folyamat zökkenőmentesen a központi telepítés előtt végzett identitáskezelési megoldás döntés. 

**[Tekintse át a ASDK telepítés tervezési szempontok](.\asdk\asdk-deploy-considerations.md)**

> [!TIP]
> Használhatja a [Azure verem telepítési követelményeinek ellenőrzése eszköz](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) az állomáson development kit annak ellenőrzéséhez, hogy a hardver megfelel-e az összes követelménynek az operációs rendszer telepítése után.

## <a name="download-and-extract-the-deployment-package"></a>Töltse le, és bontsa ki a központi telepítési csomag
Győződjön meg arról, hogy a development kit fogadó számítógép megfelel-e a ASDK telepítésének alapvető követelményeit, a következő lépésre letöltéséhez és kibontásához a ASDK központi telepítési csomagot. A központi telepítési csomag tartalmazza a Cloudbuilder.vhdx fájlt, amely a virtuális merevlemez, amely egy rendszerindításra alkalmas operációs rendszer és az Azure-verem fájlokat tartalmazza.

A központi telepítési csomag letöltheti a development kit gazdagépen vagy egy másik számítógépre. A kibontott tároló 60 GB szabad lemezterület, tarthat, ezért egy másik számítógép segítségével csökkentheti a development kit gazdagép hardverkövetelményeinek.

**[Töltse le, és bontsa ki az Azure verem Development Kit (ASDK)](.\asdk\asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>Készítse elő a development kit gazdaszámítógépen
A ASDK az állomáson telepítése előtt elő kell készíteni a környezetet, és a rendszer rendszerindító virtuális merevlemezről konfigurálva. A development kit gazdaszámítógép előkészítése után elinduló a CloudBuilder.vhdx virtuális gép merevlemez-meghajtóról, hogy ASDK telepítés megkezdése.

**[A ASDK számítógép előkészítése](.\asdk\asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>Telepítse a ASDK a gazdaszámítógépen
Előkészítése az development kit gazdaszámítógépet, miután a ASDK is telepíthető a CloudBuilder.vhdx lemezképpel. A ASDK megadott, ha letölti és a asdk-installer.ps1 PowerShell parancsprogram futtatása vagy teljesen a grafikus felhasználói felületen (GUI) telepíthető [parancssori](.\asdk\asdk-deploy-powershell.md). 

> [!NOTE]
> Ha szükséges, miután a számítógép be a CloudBuilder.vhdx betöltődött, konfigurálhatja [Azure verem telemetriai beállítások](.\asdk\asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *előtt* a ASDK telepítése.


**[Telepítse az Azure verem szoftverfejlesztői készlet (ASDK)](.\asdk\asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>Telepítés utáni konfigurációk végrehajtása
Miután telepítette a ASDK, van néhány ajánlott telepítés utáni ellenőrzések és konfigurációs változásokat, mértékét. Ellenőrizheti a telepítés sikeresen megtörtént a test-AzureStack parancsmaggal lett telepítve, és telepítse az Azure PowerShell-verem és a GitHub eszközöket. 

Után használó központi telepítések az Azure AD aktiválnia kell mindkét az Azure verem rendszergazda és bérlői portálon. Az aktiválás hozzájárul a verem Azure portál és az Azure Resource Manager a megfelelő engedélyeket ad (a hozzájárulási oldalon felsorolt) az összes felhasználó számára a könyvtár.

Győződjön meg arról, hogy a nem az értékelés időszak lejárta előtt jár le a development kit gazdagép jelszavát a jelszó-elévülési szabályzatának is visszaállítsa.

> [!NOTE]
> Másik lehetőségként beállíthatja úgy is [Azure verem telemetriai beállítások](.\asdk\asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *után* a ASDK telepítése.

**[POST-ASDK telepítési feladatok](.\asdk\asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Regisztrálja az Azure-ral
Azure verem regisztrálnia kell az Azure-ral, hogy [töltse le az Azure piactéren elemek](.\asdk\asdk-marketplace-item.md) Azure verem.

**[Azure verem regisztrálni Azure-ral](.\asdk\asdk-register.md)**

## <a name="next-steps"></a>További lépések
Gratulálunk! A lépések elvégzése után kell a csomag környezet mindkét [rendszergazda](https://adminportal.local.azurestack.external) és [felhasználói](https://portal.local.azurestack.external) portálok. 
