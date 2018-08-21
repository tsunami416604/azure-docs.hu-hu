---
title: Azure Stack érvényesítési szolgáltatás általános munkafolyamat-paraméterek |} A Microsoft Docs
description: Az Azure Stack érvényesítési szolgáltatás általános munkafolyamat-paraméterek
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 81a7be973739cfd6eb3f8fb8dc7a0723623c2b8e
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234951"
---
# <a name="workflow-common-parameters-for-azure-stack-validation-as-a-service"></a>Az Azure Stack érvényesítési szolgáltatás általános munkafolyamat-paraméterek

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Az általános paraméterek értékeit, például a környezeti változókat és a felhasználói is az összes teszt érvényesítési (VaaS) szolgáltatás szükséges hitelesítő adatokat. Meghatározhatja a munkafolyamat szintjén ezeket az értékeket. Mentse az értékeket, ha létrehozott vagy módosított egy munkafolyamatot. Ütemezés időpontban a munkafolyamat betölti az értékeket a teszt. 

## <a name="environment-parameters"></a>Környezet paraméterek

Környezet paramétereket ismertetik a teszt alatt álló Azure Stack-környezet. Ezeket az értékeket meg kell adni kódjának létrehozásával és a blokk-konfigurációs fájl feltöltése `&lt;link&gt;. [How to get the stamp info link].`

| Paraméter neve | Szükséges | Típus | Leírás |
|----------------------------------|----------|------|---------------------------------------------------------------------------------------------------------------------------------|
| Az Azure Stack-build | Szükséges |  | Az Azure Stack üzemelő példány (például 1.0.170330.9) buildszám |
| OEM-verzióra | Igen |  | Az Azure Stack üzembe helyezése során használt OEM csomag verziószáma. |
| OEM-aláírás | Igen |  | Az Azure Stack üzembe helyezése során használt OEM csomag aláírása. |
| AAD-bérlő azonosítója | Szükséges |  | Az Azure Active Directory-bérlő az Azure Stack üzembe helyezése során megadott GUID-azonosítója.|
| Régió | Szükséges |  | Azure Stack-telepítési régió. |
| Bérlő Resource Manager-végpont | Szükséges |  | A bérlő Azure Resource Manager-műveletet végpont (például https://management.<ExternalFqdn>) |
| Rendszergazdai Resource Manager-végpont | Igen |  | Végpont a bérlő Azure Resource Manager-műveletet (például https://adminmanagement.<ExternalFqdn>) |
| Külső teljes Tartományneve | Igen |  | Külső teljesen minősített tartománynév végpontok utótagként használni. (például local.azurestack.external vagy redmond.contoso.com). |
| Csomópontok száma | Igen |  | A központi telepítésben lévő csomópontok számát. |

## <a name="test-parameters"></a>Vizsgálati paraméter

Általános tesztelési paraméterek közé tartozik a bizalmas adatokat, amelyeket nem lehet konfigurációs fájlokban tárolja, és manuálisan meg kell adni.

| Paraméter neve | Szükséges | Típus | Leírás |
|--------------------------------|------------------------------------------------------------------------------|------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Bérlő felhasználóneve | Szükséges |  | Az Azure Active Directory-bérlői rendszergazdai vagy már üzembe helyezett vagy ki kell építeni az AAD-címtárában található a szolgáltatás-rendszergazda által igényeinek megfelelően. További információ a bérlői fiók kiépítése: [első lépései az Azure ad-vel](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). Ezt az értéket a teszt által használt erőforrások kiépítése sablonok üzembe helyezése például a bérlői szintű műveletek végrehajtásához (virtuális gépek, tárfiókok stb) és a számítási feladatokat. Ezt az értéket a teszt által használt erőforrások kiépítése sablonok üzembe helyezése például a bérlői szintű műveletek végrehajtásához (virtuális gépek, tárfiókok stb) és a számítási feladatokat. |
| Bérlő jelszó | Szükséges |  | A bérlői felhasználó jelszava. |
| Szolgáltatás-rendszergazda felhasználóneve | Kötelező: Megoldás érvényesítési, csomag érvényességének ellenőrzése<br>Nem kötelező: Tesztmenetek |  | Az Azure Active Directory-rendszergazda az Azure Stack üzembe helyezése során megadott AAD Directory-bérlő. |
| Szolgáltatás-rendszergazda jelszava | Kötelező: Megoldás érvényesítési, csomag érvényességének ellenőrzése<br>Nem kötelező: Tesztmenetek |  | A szolgáltatás-rendszergazda felhasználó jelszavát. |
| Felhőalapú rendszergazdai felhasználónév | Szükséges |  | Az Azure Stack tartományi rendszergazdai fiókot (például contoso\cloudadmin). Keresse meg a felhasználói szerepkör = "CloudAdmin" a konfigurációs fájlban, és válassza ki az értéket a felhasználónév-címke a konfigurációs fájlban. |
| Felhőalapú rendszergazdai jelszó | Szükséges |  | A felhő rendszergazdájához felhasználó jelszava. |
| Diagnosztikai kapcsolati karakterlánc | Szükséges |  | Egy SAS URI-t egy Azure Storage-fiókot, mely a diagnosztikai naplók lesznek másolva a teszt végrehajtása során. Útmutató: az SAS URI létrehozása találhatók [blob storage-fiók beállítása](azure-stack-vaas-set-up-account.md). |


## <a name="next-steps"></a>További lépések

- Tudjon meg többet a [szolgáltatásként az Azure Stack érvényesítési](https://docs.microsoft.com/azure/azure-stack/partner).
