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
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 5224fe4682509f92e1f18d865e5cc5afb1218ad1
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-configure-hybrid-cloud-identity-with-azure-and-azure-stack-applications"></a>Oktatóanyag: Azure és az Azure-verem alkalmazások hibrid felhőalapú identitás konfigurálása

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Hozzáférést biztosít az alkalmazások Azure-ban is globális és Azure verem két választási lehetősége van. Ha Azure verem kapcsolata folyamatosan az internethez, használhatja az Azure Active Directory (Azure AD). Ha Azure verem nem kapcsolódik az internethez, használhatja az Azure Directory összevont szolgáltatások (AD FS). Hozzáférés biztosítása az alkalmazások telepítését és a konfigurációs Azure verem az Azure erőforrás-kezelője segítségével az Azure-készletben szolgáltatásnevekről használja. 

Ebben az oktatóanyagban egy minta környezet fog létrehozni:

> [!div class="checklist"]
> * A hibrid azonosságának Azure-ban globális és Azure verem
> * Az Azure-verem API eléréséhez jogkivonat beolvasása.

Ezeket a lépéseket kell, hogy Ön jogosult Azure verem operátor.

## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Egy egyszerű szolgáltatás létrehozása az Azure AD a portálon

Ha az Azure AD használva identity Azure verem telepítése után, szolgáltatásnevekről ugyanúgy, mint az Azure hozhat létre. [Ez a dokumentum](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad) bemutatja, hogyan hajtsa végre a lépéseket a portálon keresztül. Ellenőrizze, hogy rendelkezik a [szükséges engedélyek az Azure AD](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) megkezdése előtt.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>A PowerShell használatával AD FS egyszerű szolgáltatás létrehozása

Ha telepítette az AD FS Azure verem, PowerShell segítségével hozzon létre egy egyszerű szolgáltatást, rendeljen hozzá egy szerepkört a hozzáféréshez és jelentkezzen be a Powershellből ezzel az identitással. [Ez a dokumentum](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs) bemutatja, hogyan hajtsa végre a lépéseket, PowerShell használatával.

## <a name="using-the-azure-stack-api"></a>Az Azure verem API használatával

[Ez az oktatóanyag](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-rest-api-use) részletesen ismerteti a folyamatot, amely az Azure-verem API eléréséhez jogkivonat beolvasása.

## <a name="connect-to-azure-stack-using-powershell"></a>Csatlakozás Azure verem Powershell használatával

Íme egy minta parancsfájlt használja az ebben a dokumentumban az Azure-verem történő kapcsolódáshoz tanított fogalmakat.

### <a name="prerequisites"></a>Előfeltételek

Egy Azure verem telepítése kapcsolódik Azure Active Directory előfizetés érheti el. Ha még nem rendelkezik Azure-verem telepítés, ezeket az utasításokat beállításához használhatja egy [Azure verem szoftverfejlesztői készlet](https://docs.microsoft.com/en-us/azure/azure-stack/asdk/asdk-deploy).

[Ez az oktatóanyag](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-powershell-configure-quickstart) végigvezeti a lépéseken, amelyekkel az Azure PowerShell telepítése és csatlakozás az Azure-verem telepítése.

#### <a name="connect-to-azure-stack-using-code"></a>Csatlakozás Azure verem kód használatával

Azure verem kód használatával csatlakozhat, használja a hitelesítés és a graph végpontok Azure verem telepített alkalmazáshoz, és hitelesítse a többi kérelmek használatával az Azure Resource Manager API-végpontok száma. A mintaalkalmazás található [Itt](https://github.com/shriramnat/HybridARMApplication).

> [!Note]  
Kivéve, ha az Azure SDK for a választott nyelv támogatja az Azure API-profilok, az SDK-t Azure verem nem működik. Azure API-profilokkal kapcsolatos további tudnivalókért lépjen [Itt](https://docs.microsoft.com/da-dk/azure/azure-stack/user/azure-stack-version-profiles).

## <a name="next-steps"></a>További lépések

 - Identitás rendszer hogyan kezelje a Azure verem kapcsolatos további információkért lásd: [identitás architektúra Azure verem](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture).  
 - Azure Cloud mintájával kapcsolatos további tudnivalókért lásd: [felhőalapú kialakítási mintájával](https://docs.microsoft.com/azure/architecture/patterns).
