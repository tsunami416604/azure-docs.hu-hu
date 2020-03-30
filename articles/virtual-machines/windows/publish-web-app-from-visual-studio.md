---
title: Webalkalmazás közzététele Azure-gépen a Visual Studio-ból
description: ASP.NET webalkalmazás közzététele egy Azure-beli virtuális gépen a Visual Studio-ból
services: virtual-machines-windows
author: ghogen
manager: jillfra
tags: azure-service-management
ms.assetid: 70267837-3629-41e0-bb58-2167ac4932b3
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: 6e5db9b4e46019aa386057d51d956ff11d90f498
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71970863"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>ASP.NET WebApp közzététele egy Azure-gépen a Visual Studio-ból

Ez a dokumentum ismerteti, hogyan tehet közzé egy ASP.NET webalkalmazást egy Azure virtuális gépen (VM) a Visual Studio 2019 **Microsoft Azure virtuális gépek** közzétételi szolgáltatásával.  

## <a name="prerequisites"></a>Előfeltételek
Ahhoz, hogy a Visual Studio használatával közzétegyen egy ASP.NET projektet egy Azure-beli virtuális gépen, a virtuális gép megfelelően be kell állítani.

- A számítógépet úgy kell konfigurálni, hogy ASP.NET webalkalmazást futtasson, és telepítse a WebDeploy programot.

- A virtuális gépnek dns-névvel kell rendelkeznie. További információ: [Teljes mértékben minősített tartománynév létrehozása az Azure Portalon Windows virtuális géphez](portal-create-fqdn.md)című témakörben talál.

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>A ASP.NET webalkalmazás közzététele az Azure virtuális gépén a Visual Studio használatával
A következő szakasz ismerteti, hogyan tehet közzé egy meglévő ASP.NET webalkalmazást egy Azure virtuális gépen.

1. Nyissa meg webalkalmazás-megoldását a Visual Studio 2019-ben.
2. Kattintson a jobb gombbal a projektre a Megoldáskezelőben, és válassza **a Közzététel...**
3. A lap jobb oldalán található nyíllal végiggörgetheti a közzétételi beállításokat, amíg meg nem találja a **Microsoft Azure virtuális gépeket**.  

   ![Lap közzététele – Jobbra nyíl]

4. Válassza a **Microsoft Azure virtuális gépek** ikont, majd a **Közzététel**lehetőséget.

   ![Lap közzététele – A Microsoft Azure virtuális gép ikonja]

5. Válassza ki a megfelelő fiókot (a virtuális géphez csatlakoztatott Azure-előfizetéssel).  
   - Ha be van jelentkezve a Visual Studióba, a fióklista az összes hitelesített fiókkal együtt lesz feltöltve.  
   - Ha nincs bejelentkezve, vagy ha a szükséges fiók nem szerepel a listában, válassza a "Fiók hozzáadása..." lehetőséget. és kövesse az utasításokat a bejelentkezéshez.  
   ![Azure-fiókválasztó]  

6. Válassza ki a megfelelő virtuális gépet a meglévő virtuális gépek listájából.

   > [!Note]
   > A lista feltöltése eltarthat egy ideig.

   ![Az Azure virtuálisgép-választó]

7. A közzététel megkezdéséhez kattintson az OK gombra.

8. Amikor hitelesítő adatokat kér, adja meg a felhasználói fiók felhasználónevét és jelszavát a megcélzott virtuális gépen, amely közzétételi jogokkal van konfigurálva. Ezek a hitelesítő adatok általában a rendszergazdai felhasználónév és jelszó a virtuális gép létrehozásakor.  

   ![Webdeploy bejelentkezés]

9. Fogadja el a biztonsági tanúsítványt.

   ![Tanúsítványhiba]

10. Tekintse meg a Kimenet ablakot a közzétételi művelet előrehaladásának ellenőrzéséhez.

    ![Kimeneti ablak]

11. Ha a közzététel sikeres, a böngésző elindítja az újonnan közzétett webhely URL-címének megnyitását.

**Siker!**

Most már sikeresen közzétette a webalkalmazást egy Azure virtuális gépen.

## <a name="publish-page-options"></a>Lapbeállítások közzététele

A közzétételi varázsló befejezése után a Közzététel lap jól megnyílik a dokumentumban, ha az új közzétételi profil ki van jelölve.

### <a name="re-publish"></a>Újbóli közzététel

A webalkalmazás frissítéseinek közzétételéhez kattintson a **Közzététel** gombra a Közzététel lapon.  
- Ha a rendszer kéri, adja meg a felhasználónevet és a jelszót.  
- A közzététel azonnal megkezdődik.

![Lap közzététele – Közzététel gomb]

### <a name="modify-publish-profile-settings"></a>Közzétételi profil beállításainak módosítása

A közzétételi profil beállításainak megtekintéséhez és módosításához válassza a **Beállítások...** lehetőséget.  

![Lap közzététele – Beállítások gomb]

A beállításoknak valahogy így kell kinézniük:  

![Közzétételi beállítások – Kapcsolat lap]

#### <a name="save-user-name-and-password"></a>Felhasználónév és jelszó mentése
- Ne adjon meg hitelesítési adatokat minden közzétételkor. Ehhez felkell nagyítania a **Felhasználónév** és **a Jelszó** mezőt, és jelölje be a **Jelszó mentése** jelölőnégyzetet.
- A **Kapcsolat ellenőrzése** gombbal ellenőrizheti, hogy a megfelelő adatokat adta-e meg.

#### <a name="deploy-to-clean-web-server"></a>Telepítés tiszta webkiszolgálóra

- Ha meg szeretne győződni arról, hogy a webkiszolgáló minden feltöltés után rendelkezik a webalkalmazás tiszta másolatával, és hogy egy korábbi telepítésből nem marad más fájl, a Beállítások lapon jelölje be a További fájlok eltávolítása a **célhelyen** **jelölőnégyzetet.**

- Figyelmeztetés: Ezzel a beállítással a közzététel törli a webkiszolgálón (wwwroot könyvtár) található összes fájlt. Győződjön meg arról, hogy ismeri a számítógép állapotát, mielőtt ezzel a beállítással szeretné tudni a számítógépet. 

![Beállítások közzététele – Beállítások lap]

## <a name="next-steps"></a>További lépések

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Ci/CD beállítása az Azure VM automatikus üzembe helyezéséhez

Ha folyamatos kézbesítési folyamatot szeretne beállítani az Azure Pipelines használatával, [olvassa el a Telepítés Windows virtuális gépre.](https://docs.microsoft.com/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups)

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Lap közzététele – Jobbra nyíl]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Lap közzététele – A Microsoft Azure virtuális gép ikonja]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Azure-fiókválasztó]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Az Azure virtuálisgép-választó]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[Webdeploy bejelentkezés]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Tanúsítványhiba]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Kimeneti ablak]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Lap közzététele – Közzététel gomb]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Lap közzététele – Beállítások gomb]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Közzétételi beállítások – Kapcsolat lap]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Beállítások közzététele – Beállítások lap]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
