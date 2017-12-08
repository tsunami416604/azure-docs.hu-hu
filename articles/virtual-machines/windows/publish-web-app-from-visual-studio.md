---
title: "A webes alkalmazás közzététele egy Azure virtuális gépre a Visual Studio eszközből |} Microsoft Docs"
description: "A Visual Studióból egy Azure virtuális géphez ASP.NET webes alkalmazás közzététele"
services: virtual-machines-windows
documentationcenter: 
author:
- kraigb
- justcla
manager: ghogen
editor: 
tags: azure-service-management
ms.assetid: 70267837-3629-41e0-bb58-2167ac4932b3
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2017
ms.author:
- kraigb
- justcla
ms.openlocfilehash: 5a0dd3d123cb0d580ea753cebc36ebcdb7084db9
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>ASP.NET webes alkalmazások közzététele egy Azure virtuális gépre a Visual Studio eszközből

Ez a dokumentum ismerteti az Azure virtuális gép (VM) használatával ASP.NET webes alkalmazás közzététele a **Microsoft Azure virtuális gépek** közzétételi szolgáltatást a Visual Studio 2017.  

## <a name="prerequisites"></a>Előfeltételek
Ahhoz, hogy a Visual Studio egy ASP.NET-projekt közzététele egy Azure virtuális gépre, a virtuális gép lehet megfelelően be kell állítania.

- Gép kell állítani egy ASP.NET-webalkalmazás fut, és a WebDeploy telepítve rendelkezik.

- A virtuális gép rendelkeznie kell egy DNS-név. További információkért lásd: [hozzon létre egy teljesen minősített tartománynevet az Azure-portálon a Windows virtuális gépek](portal-create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Az ASP.NET webalkalmazás közzététele a Visual Studio használatával Azure VM
A következő szakasz ismerteti, hogyan közzététele egy Azure virtuális gép egy meglévő ASP.NET webes alkalmazást.

1. Nyissa meg a webes alkalmazás megoldást a Visual Studio 2017.
2. Kattintson a jobb gombbal a projektben a Megoldáskezelőre, és válassza a **közzététel...**
3. Az oldalon jobbra látható nyílra segítségével Görgessen végig a közzétételi beállítások, amíg meg nem látja **Microsoft Azure virtuális gépek**.  

   ![Lap - jobbra mutató nyílra közzététele]

4. Válassza ki a **Microsoft Azure virtuális gépek** ikonra, és válassza ki **közzététel**.

   ![Közzététel lap – a Microsoft Azure virtuális gép ikonja]

5. Válassza ki a megfelelő fiókot (az Azure-előfizetés csatlakoztatva a virtuális gép).  
   - Ha be van jelentkezve Visual Studio, a fiókok listáján megjelenik a hitelesített fiókok.  
   - Ha nincs bejelentkezve, vagy ha van szüksége a fiók nem szerepel, válassza a "Fiók hozzáadása...", és kövesse az utasításokat a bejelentkezéshez.  
   ![Azure-fiók választó]  

6. Válassza ki a megfelelő virtuális Gépet a listából a meglévő virtuális gépek.

   > [!Note]
   > Ez a lista feltöltése némi időbe telhet.

   ![Azure virtuális gép választó]

7. Kattintson az OK gombra kattintva megkezdheti a közzététel.

8. Ha a hitelesítő adatok megadását kéri, adja meg a felhasználónevet és jelszót egy felhasználói fiók a cél virtuális Gépet, amely a jogok (általában a rendszergazda felhasználónevét és jelszavát, a virtuális gép létrehozásakor használt) közzétételi van konfigurálva.  

   ![WebDeploy-bejelentkezés]

9. A biztonsági tanúsítvány elfogadásához.

   ![Tanúsítványhiba]

10. A kimeneti ablakban a közzétételi művelet állapotának ellenőrzéséhez figyelje.

    ![Kimeneti ablak]

11. Sikeres közzététel esetén a böngésző megnyitásához az újonnan közzétett webhely URL-CÍMÉT indít.

**Sikerült!**

Most már sikeresen közzétette a webalkalmazás az Azure virtuális géphez.

## <a name="publish-page-options"></a>Közzététel lap beállításai

A Közzététel varázsló befejezése után a közzététel lap megnyitása a dokumentumban is a megadott új közzétételi profilhoz.

### <a name="re-publish"></a>Tegye közzé újra

A frissítések közzétételéhez a webalkalmazást, válassza ki a **közzététel** gombra a Publish oldalán.  
- Ha a rendszer kéri, adja meg a felhasználónevet és jelszót.  
- A közzététel azonnal megkezdődik.

![Lap közzététele – a Közzététel gombra]

### <a name="modify-publish-profile-settings"></a>Módosítsa közzétételi profil beállítások

Beállítások megtekintése és módosítása a közzétételi profil, jelölje be **beállítások...** .  

![Lap - Beállítások gombra közzététele]

A beállítások kell kinéznie:  

![Közzétételi beállítások – csatlakozási lapja]

#### <a name="save-user-name-and-password"></a>Felhasználónév és jelszó mentése
- Hitelesítési adatokat biztosít minden alkalommal, amikor a közzététel elkerüléséhez feltöltheti azt a **felhasználónév** és **jelszó** mezőket és kattintson a **jelszó mentése** mezőbe.
- Használja a **kapcsolat ellenőrzése** gombra kattintva győződjön meg arról, hogy megadta-e a megfelelő információk.

#### <a name="deploy-to-clean-web-server"></a>Webkiszolgáló tiszta telepítése

- Ha meg szeretné-e győződjön meg arról, hogy a webalkalmazás-kiszolgáló rendelkezik-e a webes alkalmazás egyes feltöltése után tiszta (és, hogy más fájlok nem meghagyott egy korábbi telepítésből függő), ellenőrizheti a **célhelyen további fájlok eltávolítása** a jelölőnégyzet jelölését a **beállítások** fülre.

- Figyelmeztetés: Ezt a beállítást a közzététel törli az összes olyan fájlt, amely a webkiszolgáló (wwwroot könyvtárat) létezik. Győződjön meg arról, hogy ez a beállítás engedélyezve van a közzététel előtt a gép állapotát tudja. 

![Közzétételi beállítások - beállítások lap]

## <a name="next-steps"></a>Következő lépések

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Automatikus telepítés Azure VM CI/CD beállítása

A Visual Studio Team szolgáltatással folyamatos kézbesítési adatcsatorna beállításához tekintse meg a [központi telepítése a Windows virtuális gépek](https://docs.microsoft.com/en-us/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Lap - jobbra mutató nyílra közzététele]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Közzététel lap – a Microsoft Azure virtuális gép ikonja]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Azure-fiók választó]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Azure virtuális gép választó]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[WebDeploy-bejelentkezés]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Tanúsítványhiba]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Kimeneti ablak]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Lap közzététele – a Közzététel gombra]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Lap - Beállítások gombra közzététele]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Közzétételi beállítások – csatlakozási lapja]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Közzétételi beállítások - beállítások lap]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
