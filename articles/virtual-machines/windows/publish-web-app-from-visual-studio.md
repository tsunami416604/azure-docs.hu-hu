---
title: A webalkalmazás közzététele az Azure virtuális géphez a Visual Studióból
description: A Visual Studióból az Azure virtuális gépeken az ASP.NET webes alkalmazás közzététele
services: virtual-machines-windows
author: ghogen
manager: douge
tags: azure-service-management
ms.assetid: 70267837-3629-41e0-bb58-2167ac4932b3
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: 561de3ac9073fe5cfdfadf2dc61d3f7807bf9e46
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42058421"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>ASP.NET-webalkalmazás közzététele az Azure virtuális géphez a Visual Studióból

Ez a dokumentum ismerteti, hogyan tehet közzé egy ASP.NET-alkalmazás egy Azure virtuális gép (VM) használatával a **Microsoft Azure Virtual Machines** közzétételi szolgáltatás Visual Studio 2017-ben.  

## <a name="prerequisites"></a>Előfeltételek
ASP.NET-projekt közzététele egy Azure virtuális Gépen a Visual Studio használatához a virtuális Gépet kell megfelelően be kell állítania.

- Egy ASP.NET-alkalmazás futtatásához, és a WebDeploy telepítve van a gépen kell konfigurálni.

- A virtuális gép rendelkeznie kell egy DNS-név konfigurálva. További információkért lásd: [teljesen minősített tartománynév létrehozása az Azure Portalon Windows virtuális gép](portal-create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Az ASP.NET-webalkalmazás közzététele az Azure virtuális géphez, a Visual Studio használatával
A következő szakasz egy meglévő ASP.NET-alkalmazás egy Azure virtuális gépen való közzétételét ismerteti.

1. Nyissa meg a webes alkalmazás megoldást a Visual Studio 2017-ben.
2. Kattintson a jobb gombbal a projektre a Megoldáskezelőben, és válassza a **Publish...**
3. A lap jobb oldalán a nyílra használatával Görgessen végig a közzétételi beállítások, amíg meg nem találja **Microsoft Azure Virtual Machines**.  

   ![Közzététel a lap – jobbra mutató nyíl]

4. Válassza ki a **Microsoft Azure Virtual Machines** ikonra, majd **közzététel**.

   ![Közzététel a lap – Microsoft Azure virtuális gép ikonja]

5. Válassza ki a megfelelő fiókot (az Azure-előfizetést a virtuális gép csatlakozik).  
   - Visual Studio-felhasználó jelentkezett be, ha a fiók listában megjelenik a hitelesített fiókok.  
   - Ha nincs bejelentkezve, vagy ha a van szüksége a fiók nem szerepel a listán, válassza a "Fiók hozzáadása...", és kövesse az utasításokat követve jelentkezzen be.  
   ![Azure-fiók-választó]  

6. Válassza ki a megfelelő virtuális Gépet a listából a meglévő virtuális gépek.

   > [!Note]
   > Ez a lista feltöltéséhez hosszabb időt is igénybe vehet.

   ![Az Azure VM-választó]

7. Kattintson az OK gombra a közzététel megkezdéséhez.

8. Amikor a rendszer kéri a hitelesítő adatokat, adja meg a felhasználónevet és jelszót egy felhasználói fiók a cél virtuális Gépre, amelyet a közzétételi rights (általában a rendszergazdai felhasználónevét és jelszavát, a virtuális gép létrehozásakor használt) van konfigurálva.  

   ![WebDeploy-bejelentkezés]

9. Fogadja el a biztonsági tanúsítvány.

   ![Tanúsítványhiba]

10. Tekintse meg a kimeneti ablakban, a közzétételi művelet állapotának ellenőrzéséhez.

    ![Kimeneti ablak]

11. Sikeres közzététel esetén a böngésző elindítja az újonnan közzétett hely URL-címre.

**Success!**

Most már sikeresen közzétette a webalkalmazást az Azure virtuális gépeken.

## <a name="publish-page-options"></a>Közzétételi beállítások lap

A publish varázsló befejezése után a közzétételi oldalon nyílik meg a dokumentumot a kiválasztott új közzétételi profilt.

### <a name="re-publish"></a>Tegye közzé újra

Válassza ki a frissítések közzétételéhez a webes alkalmazások, a **közzététel** a közzétételi oldalon gombjára.  
- Ha a rendszer kéri, adja meg a felhasználónevet és jelszót.  
- Közzététel azonnal megkezdődik.

![Oldal közzététele – a közzététel gomb]

### <a name="modify-publish-profile-settings"></a>Módosítása közzététel profil beállításai

Beállítások megtekintése és módosítása a közzétételi profilt, válassza **beállítások...** .  

![Közzététel a lap - beállítások gomb]

A beállítások a következőhöz hasonlóan kell kinéznie:  

![Közzétételi beállítások – kapcsolat lap]

#### <a name="save-user-name-and-password"></a>Felhasználónév és jelszó mentése
- Elkerülheti, hogy megadta a hitelesítési adatokat, minden alkalommal, amikor közzéteszi, feltölti a **felhasználónév** és **jelszó** mezőket, és válassza a **jelszó mentése** mezőbe.
- Használja a **kapcsolat ellenőrzése** gombra annak megerősítéséhez, hogy megadta a megfelelő adatokat.

#### <a name="deploy-to-clean-web-server"></a>Web server tiszta telepítése

- Szeretné-e, ügyeljen arra, hogy a webkiszolgáló egyes feltöltése után a webes alkalmazás tiszta (és más fájlokat egy korábbi telepítésből függő van hátra), ellenőrizheti a **célhelyen további fájlok eltávolítása** a jelölőnégyzet a **beállítások** fülre.

- Figyelmeztetés: Ezzel a beállítással közzététel törli az összes fájlt, amely létezik a webkiszolgálón (wwwroot könyvtára). Győződjön meg arról, hogy ez a beállítás engedélyezve van a közzététel előtt a gép állapotát tudja. 

![Közzétételi beállítások – beállítások lap]

## <a name="next-steps"></a>További lépések

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Azure virtuális géphez az automatikus központi telepítési CI/CD beállítása

A Visual Studio Team Services egy folyamatos teljesítési folyamat beállításával, tekintse meg [központi telepítése a Windows virtuális gép](https://docs.microsoft.com/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Közzététel a lap – jobbra mutató nyíl]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Közzététel a lap – Microsoft Azure virtuális gép ikonja]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Azure Account Selector]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Az Azure VM-választó]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[WebDeploy-bejelentkezés]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Tanúsítványhiba]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Kimeneti ablak]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Oldal közzététele – a közzététel gomb]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Közzététel a lap - beállítások gomb]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Közzétételi beállítások – kapcsolat lap]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Közzétételi beállítások – beállítások lap]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
