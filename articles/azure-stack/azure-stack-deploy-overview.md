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
ms.date: 06/04/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: a0e742ab3ac43cc7977761dd94c9689e3a7c2e0b
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235185"
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Gyors üzembe helyezés: az Azure verem szoftverfejlesztői készlet kiértékelése

A [Azure verem Development Kit (ASDK)](.\asdk\asdk-what-is.md) egy tesztelési és fejlesztési környezet, amely értékeli, és bemutatja a verem Azure-szolgáltatások és szolgáltatások telepítése. A ASDK megkezdéséhez, meg kell készítse elő a fogadó számítógép hardvere, majd futtassa az egyes parancsfájlok (a telepítés több óráig tart). Ezt követően is bejelentkezik a rendszergazda vagy felhasználó portálok Azure verem elindítására.

## <a name="prerequisites"></a>Előfeltételek

### <a name="asdk-host-computer-requirements"></a>ASDK gazdagépre vonatkozó követelmények

A ASDK a telepítés előtt elő kell készíteni a szoftverfejlesztői készlet futtató számítógépre. A development kit gazdaszámítógép meg kell felelnie, hogy a hardverek, szoftverek és hálózati követelményeinek ismertetett  **[tekintse át a tervezési szempontok ASDK telepítési](.\asdk\asdk-deploy-considerations.md)**.

> [!TIP]
> Használhatja a [Azure verem telepítési követelményeinek ellenőrzése eszköz](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) az állomáson development kit annak ellenőrzéséhez, hogy a hardver megfelel-e az összes követelménynek az operációs rendszer telepítése után.

### <a name="account-requirements"></a>Fiókra vonatkozó követelmények

Szükség Azure Active Directory (Azure AD) vagy Active Directory összevonási szolgáltatások (AD FS) használatával választhat a identitáskezelési megoldásként az üzembe helyezéshez. Tekintse át a fiók a  **[központi telepítés tervezési szempontok](.\asdk\asdk-deploy-considerations.md#account-requirements)**

## <a name="download-and-extract-the-deployment-package"></a>Töltse le, és bontsa ki a központi telepítési csomag

Felkészülés a development kit gazdaszámítógépen, követően töltse le, és bontsa ki a ASDK központi telepítési csomagot. A központi telepítési csomag tartalmazza a Cloudbuilder.vhdx fájlt, amely egy virtuális merevlemez (VHD) egy rendszerindításra alkalmas operációs rendszerrel, és az Azure-verem fájlokat.

A központi telepítési csomag letöltheti a development kit gazdagépen vagy egy másik számítógépre. A kibontott tároló tarthat 60 GB szabad lemezterület, ezért egy másik számítógép segítségével csökkentheti a development kit gazdagépen tárolási követelmények érvényesek.

**[Töltse le, és bontsa ki az Azure verem Development Kit (ASDK)](.\asdk\asdk-download.md)**

## <a name="prepare-the-host-computer"></a>A számítógép előkészítése

A ASDK telepítése előtt elő kell készíteni a gazdagép-környezetben, és a rendszer a virtuális merevlemez csomag a rendszerindító konfigurálva. A gazdagép újraindítása esetén az CloudBuilder.vhdx elinduló, és megkezdheti a ASDK telepítését.

**[A ASDK számítógép előkészítése](.\asdk\asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>Telepítse a ASDK a gazdaszámítógépen

Miután a számítógép elindul a virtuális merevlemezről, a csomag telepítene a Cloudbuilder virtuális környezet. A grafikus felhasználói felülettel, a asdk-installer.ps1 PowerShell parancsfájl futtatásával, vagy a megadott használatával ASDK telepítése [a PowerShell-parancssorból](.\asdk\asdk-deploy-powershell.md)

> [!NOTE]
> A gazdagép indítása a Cloudbuilder.vhdx kép, után beállíthatja [Azure verem telemetriai beállítások](.\asdk\asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *előtt* a ASDK telepítése.

**[Telepítse az Azure verem szoftverfejlesztői készlet (ASDK)](.\asdk\asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>Telepítés utáni konfigurációk végrehajtása

Miután telepítette a ASDK, van néhány ajánlott telepítés utáni ellenőrzések és konfigurációs változásokat, mértékét.

**Eszközök**

Azure verem PowerShell és a GitHub telepíti, és a teszt-AzureStack parancsmag használatával a telepítés sikerességének ellenőrzése.

**Identitáskezelési megoldás**

Az Azure AD használó telepítéséhez aktiválnia kell mindkét az Azure verem rendszergazda és bérlői portálon. Az aktiválás hozzájárul a verem Azure portál és az Azure Resource Manager a megfelelő engedélyeket ad (a hozzájárulási oldalon felsorolt) az összes felhasználó számára a könyvtár.

**Jelszó lejárata**

Állítsa vissza a jelszó-elévülési szabályzatának, győződjön meg arról, hogy a jelszót ahhoz, hogy a development kit gazdagép az értékelés időszak lejárta előtt nem lejár.

> [!NOTE]
> Akkor is beállíthatja [Azure verem telemetriai beállítások](.\asdk\asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *után* a ASDK telepítése.

**[POST-ASDK telepítési feladatok](.\asdk\asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Regisztrálja az Azure-ral

Azure verem regisztrálnia kell az Azure-ral, hogy [töltse le az Azure piactéren elemek](.\asdk\asdk-marketplace-item.md) Azure verem.

**[Azure verem regisztrálni Azure-ral](.\asdk\asdk-register.md)**

## <a name="next-steps"></a>További lépések

Gratulálunk! A gyors üzembe helyezés a lépések végrehajtásával tartalmazó ASDK környezetben van egy [rendszergazda](https://adminportal.local.azurestack.external) portál és a [felhasználói](https://portal.local.azurestack.external) portál.
