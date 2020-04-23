---
title: Webalkalmazás közzététele Azure-beli virtuális gépen a Visual studióból
description: ASP.NET-webalkalmazás közzététele Azure-beli virtuális gépen a Visual studióból
author: ghogen
manager: jillfra
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: abf0570ee9f01a2378e8c0370cfc3ba5fecc7406
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82098232"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>ASP.NET-webalkalmazás közzététele Azure-beli virtuális gépen a Visual studióból

Ez a dokumentum azt ismerteti, hogyan tehet közzé egy ASP.NET-webalkalmazást egy Azure-beli virtuális gépen (VM) a Visual Studio 2019 **Microsoft Azure Virtual Machines** Publishing szolgáltatásának használatával.  

## <a name="prerequisites"></a>Előfeltételek
Ahhoz, hogy a Visual Studio használatával közzé lehessen tenni egy ASP.NET-projektet egy Azure-beli virtuális gépen, helyesen kell beállítani a virtuális gépet.

- A gépet úgy kell konfigurálni, hogy ASP.NET-webalkalmazást futtasson, és telepítve legyen a web Deploy.

- A virtuális gépnek konfigurálnia kell egy DNS-nevet. További információt a [Windows rendszerű virtuális gépekhez tartozó Azure Portal teljes tartománynév létrehozása](portal-create-fqdn.md)című témakörben talál.

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>ASP.NET-webalkalmazás közzététele az Azure-beli virtuális gépen a Visual Studio használatával
A következő szakasz azt ismerteti, hogyan tehet közzé egy meglévő ASP.NET-webalkalmazást egy Azure-beli virtuális gépen.

1. Nyissa meg a webalkalmazási megoldást a Visual Studio 2019-ben.
2. Kattintson a jobb gombbal a projektre Megoldáskezelő, majd válassza a **Közzététel...** lehetőséget.
3. A lap jobb oldalán található nyílra kattintva görgessen végig a közzétételi beállításokon, amíg meg nem találja a **Microsoft Azure Virtual Machines**.  

   ![Közzétételi oldal jobbra nyíl]

4. Válassza a **Microsoft Azure Virtual Machines** ikont, és válassza a **Közzététel**lehetőséget.

   ![Oldal közzététele – Microsoft Azure virtuális gép ikonja]

5. Válassza ki a megfelelő fiókot (a virtuális géphez csatlakoztatott Azure-előfizetéssel).  
   - Ha bejelentkezett a Visual studióba, a fiókok listája az összes hitelesített fiókkal együtt fel lesz töltve.  
   - Ha nincs bejelentkezve, vagy ha a szükséges fiók nem szerepel a listában, válassza a "fiók hozzáadása..." lehetőséget. és kövesse az utasításokat a bejelentkezéshez.  
   ![Azure-fiók választója]  

6. Válassza ki a megfelelő virtuális gépet a meglévő Virtual Machines listájából.

   > [!Note]
   > A lista feltöltése hosszabb időt is igénybe vehet.

   ![Azure VM-választó]

7. A közzététel megkezdéséhez kattintson az OK gombra.

8. Amikor a rendszer kéri a hitelesítő adatokat, adja meg egy olyan felhasználói fiók felhasználónevét és jelszavát, amely a közzétételi jogosultságokkal van konfigurálva. Ezek a hitelesítő adatok általában a virtuális gép létrehozásakor használt rendszergazdai Felhasználónév és jelszó.  

   ![Webtelepítési bejelentkezés]

9. Fogadja el a biztonsági tanúsítványt.

   ![Tanúsítvány hibája]

10. A közzétételi művelet előrehaladásának ellenőrzéséhez tekintse meg a kimenet ablakot.

    ![kimeneti ablak]

11. Ha a közzététel sikeres, egy böngésző elindul az újonnan közzétett webhely URL-címének megnyitásához.

**Sikeres!**

Most sikeresen közzétette a webalkalmazást egy Azure-beli virtuális gépen.

## <a name="publish-page-options"></a>Közzétételi oldal beállításai

A közzétételi varázsló befejezése után a közzétételi oldal a dokumentumban jól megnyílik, és az új közzétételi profilt választja.

### <a name="re-publish"></a>Közzététel újból

A webalkalmazás frissítéseinek közzétételéhez **kattintson a Közzététel gombra a** közzétételi lapon.  
- Ha a rendszer kéri, adja meg a felhasználónevet és a jelszót.  
- A közzététel azonnal megkezdődik.

![Közzétételi lap – Közzététel gomb]

### <a name="modify-publish-profile-settings"></a>Közzétételi profil beállításainak módosítása

A közzétételi profil beállításainak megtekintéséhez és módosításához válassza a **beállítások...** lehetőséget.  

![Közzétételi lap – beállítások gomb]

A beállításoknak a következőhöz hasonlóan kell kinéznie:  

![Közzétételi beállítások – a kapcsolatok lapja]

#### <a name="save-user-name-and-password"></a>Felhasználónév és jelszó mentése
- Kerülje a hitelesítési adatok minden közzétételkor történő biztosítását. Ehhez töltse ki a **Felhasználónév** és a **jelszó** mezőt, majd válassza a **Jelszó mentése** mezőt.
- A **Kapcsolódás ellenőrzése** gomb használatával ellenőrizze, hogy a megfelelő adatokat adta-e meg.

#### <a name="deploy-to-clean-web-server"></a>Üzembe helyezés a webkiszolgáló tisztítása szolgáltatásban

- Ha biztosítani szeretné, hogy a webkiszolgáló minden feltöltés után tiszta másolatot készítsen a webalkalmazásról, és hogy más fájlok ne maradnak egy korábbi telepítésből, a **Beállítások** lapon jelölje be a **További fájlok eltávolítása a célhelyen** jelölőnégyzetet.

- Figyelmeztetés: a közzététel ezzel a beállítással a webkiszolgálón található összes fájl (wwwroot könyvtár) törlődik. Győződjön meg arról, hogy ismeri a gép állapotát, mielőtt engedélyezi ezt a beállítást. 

![Közzétételi beállítások – beállítások lap]

## <a name="next-steps"></a>További lépések

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>CI/CD beállítása az Azure-beli virtuális gép automatikus üzembe helyezéséhez

Az Azure-folyamatokkal rendelkező folyamatos kézbesítési folyamat létrehozásához tekintse meg a [Windows rendszerű virtuális gépek üzembe helyezése](https://docs.microsoft.com/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups)című témakört.

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Közzétételi oldal jobbra nyíl]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Oldal közzététele – Microsoft Azure virtuális gép ikonja]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Azure-fiók választója]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Azure VM-választó]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[Webtelepítési bejelentkezés]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Tanúsítvány hibája]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[kimeneti ablak]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Közzétételi lap – Közzététel gomb]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Közzétételi lap – beállítások gomb]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Közzétételi beállítások – a kapcsolatok lapja]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Közzétételi beállítások – beállítások lap]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
