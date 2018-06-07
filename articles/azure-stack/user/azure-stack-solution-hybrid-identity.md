---
title: Hibrid felhőalapú identitás konfigurálása az Azure és az Azure-verem alkalmazásokkal |} Microsoft Docs
description: Tudnivalók a hibrid felhőalapú identitás konfigurálása az Azure és az Azure-verem alkalmazásokkal.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/22/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: a57afb4a90da5877879afddc35545e0bfef622a7
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808162"
---
# <a name="tutorial-configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>Oktatóanyag: hibrid felhő identitás Azure és az Azure-verem alkalmazások konfigurálása

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Útmutató az Azure és az Azure-verem alkalmazások hibrid felhő identitásának beállítása.

Hozzáférés az Azure-ban is globális és Azure verem alkalmazások két választási lehetősége van.

 * Ha az Azure-verem folyamatos csatlakozik az internethez, Azure Active Directory (Azure AD) is használhatja.
 * Ha Azure verem nem kapcsolódik az internethez, használhatja az Azure Directory összevont szolgáltatások (AD FS).

Szolgáltatásnevekről segítségével hozzáférést az Azure-verem alkalmazások történő üzembe helyezése vagy CA konfigurációját az Azure Resource Manager segítségével Azure-készletben.

Ebben az oktatóanyagban egy minta környezet fog létrehozni:

> [!div class="checklist"]
> * A hibrid azonosságának Azure-ban globális és Azure verem
> * Az Azure-verem API eléréséhez jogkivonat beolvasása.

Ebben az oktatóanyagban Azure verem operátor engedélyekkel a lépéseket kell rendelkeznie.

## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Egy egyszerű szolgáltatás létrehozása az Azure AD a portálon

Ha az Azure AD használva identity Azure verem telepítése után, szolgáltatásnevekről ugyanúgy, mint az Azure hozhat létre. A [szolgáltatásnevekről létrehozása](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad) a cikk bemutatja, hogyan hajtsa végre a lépéseket a portálon keresztül. Ellenőrizze, hogy a [szükséges engedélyek az Azure AD](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) megkezdése előtt.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>A PowerShell használatával AD FS egyszerű szolgáltatás létrehozása

Ha telepítette az AD FS Azure verem, PowerShell segítségével hozzon létre egy egyszerű szolgáltatást, rendeljen hozzá egy szerepkört a hozzáféréshez és jelentkezzen be a Powershellből ezzel az identitással. [Egyszerű szolgáltatásnév létrehozása az AD FS](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs) bemutatja, hogyan hajtsa végre a szükséges lépéseket a PowerShell használatával.

## <a name="using-the-azure-stack-api"></a>Az Azure verem API használatával

A [Azure verem API](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-rest-api-use) oktatóanyag végigvezeti az Azure-verem API eléréséhez jogkivonat lekérésének folyamatát.

## <a name="connect-to-azure-stack-using-powershell"></a>Csatlakozás Azure verem Powershell használatával

A gyors üzembe helyezés [, amelyekből megismerheti a verem Azure PowerShell használatával történő](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart) végigvezeti az Azure PowerShell telepítése és csatlakozás az Azure-verem telepítése szükséges.

### <a name="prerequisites"></a>Előfeltételek

Egy Azure verem telepítése kapcsolódik Azure Active Directory előfizetés érheti el. Ha még nem rendelkezik Azure-verem telepítés, ezeket az utasításokat beállításához használhatja egy [Azure verem szoftverfejlesztői készlet](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy).

#### <a name="connect-to-azure-stack-using-code"></a>Csatlakozás Azure verem kód használatával

Azure verem kód használatával csatlakozhat, használja a hitelesítés és a graph végpontok Azure verem telepített alkalmazáshoz, és hitelesítse a többi kérelmek használatával az Azure Resource Manager API-végpontok száma. A minta ügyfélalkalmazás található [GitHub](https://github.com/shriramnat/HybridARMApplication).

>[!Note]
>Kivéve, ha az Azure SDK for a választott nyelv támogatja az Azure API-profilok, az SDK-t Azure verem nem működik. Azure API-profilokkal kapcsolatos további tudnivalókért tekintse meg a [API-verzió profilok kezeléséhez](https://docs.microsoft.com/da-dk/azure/azure-stack/user/azure-stack-version-profiles) cikk.

## <a name="next-steps"></a>További lépések

 - Identitás rendszer hogyan kezelje a Azure verem kapcsolatos további információkért lásd: [identitás architektúra Azure verem](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture).
 - Azure Cloud mintájával kapcsolatos további tudnivalókért lásd: [felhőalapú kialakítási mintájával](https://docs.microsoft.com/azure/architecture/patterns).
